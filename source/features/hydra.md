# Hydra 구성 시스템

Isaac Lab은 [Hydra](https://hydra.cc/docs/intro/) 구성 시스템을 지원하여 명령줄 인수를 사용해 작업의 구성을 수정할 수 있습니다. 이는 실험 자동화 및 하이퍼파라미터 튜닝에 유용할 수 있습니다.

환경의 모든 매개변수는 `env.a.b.param1=value` 형태의 하나 이상의 요소를 명령줄 입력에 추가함으로써 수정할 수 있으며, 여기서 `a.b.param1`은 매개변수의 계층 구조를 반영합니다. 예를 들어 `env.actions.joint_effort.scale=10.0`과 같습니다. 마찬가지로, 에이전트의 매개변수는 `agent` 접두사를 사용하여 수정할 수 있습니다. 예를 들어 `agent.seed=2024`와 같습니다.

이러한 명령줄 인수를 설정하는 방식은 구성 파일의 정확한 구조를 따릅니다. 다양한 RL 프레임워크가 서로 다른 관례를 사용하기 때문에 매개변수 설정 방식에 차이가 있을 수 있습니다. 예를 들어, *rl_games*에서는 `agent.params.seed`를 사용하여 시드를 설정하지만, *rsl_rl*, *skrl* 및 *sb3*에서는 `agent.seed`를 사용합니다.

따라서 Hydra 인수를 사용한 훈련은 다음 구문을 사용하여 실행할 수 있습니다:

### rsl_rl

```shell
python scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Cartpole-v0 --headless env.actions.joint_effort.scale=10.0 agent.seed=2024
```

### rl_games

```shell
python scripts/reinforcement_learning/rl_games/train.py --task=Isaac-Cartpole-v0 --headless env.actions.joint_effort.scale=10.0 agent.params.seed=2024
```

### skrl

```shell
python scripts/reinforcement_learning/skrl/train.py --task=Isaac-Cartpole-v0 --headless env.actions.joint_effort.scale=10.0 agent.seed=2024
```

### sb3

```shell
python scripts/reinforcement_learning/sb3/train.py --task=Isaac-Cartpole-v0 --headless env.actions.joint_effort.scale=10.0 agent.seed=2024
```

위 명령은 태스크 `Isaac-Cartpole-v0`에 대해 헤드리스 모드로 훈련 스크립트를 실행하고, `env.actions.joint_effort.scale` 매개변수를 10.0으로, `agent.seed` 매개변수를 2024로 설정합니다.

#### 참고
하위 호환성을 유지하고 사용자 친화적인 경험을 제공하기 위해, `--param` 형태의 기존 CLI 인수(예: `--num_envs`, `--seed`, `--max_iterations`)를 유지했습니다. 이러한 인수는 Hydra 인수보다 우선하며, Hydra 인수로 설정된 값을 덮어씁니다.

## 고급 매개변수 수정

### 콜러블

구성 파일의 함수와 클래스를 `module:attribute_name` 구문을 사용하여 수정할 수 있습니다. 예를 들어 Cartpole 환경에서:

```python
class ObservationsCfg:
    """Observation specifications for the MDP."""

    @configclass
    class PolicyCfg(ObsGroup):
        """Observations for policy group."""

        # observation terms (order preserved)
        joint_pos_rel = ObsTerm(func=mdp.joint_pos_rel)
        joint_vel_rel = ObsTerm(func=mdp.joint_vel_rel)

        def __post_init__(self) -> None:
            self.enable_corruption = False
            self.concatenate_terms = True

    # observation groups
    policy: PolicyCfg = PolicyCfg()
```

다음과 같이 `joint_pos_rel`를 상대 위치 대신 절대 위치를 계산하도록 수정할 수 있습니다:
`env.observations.policy.joint_pos_rel.func=isaaclab.envs.mdp:joint_pos`.

### 매개변수를 None으로 설정

매개변수를 None으로 설정하려면 Hydra에서 특별 키워드인 `null` 키워드를 사용하면 None으로 자동 변환됩니다. 위의 예시에서 `joint_pos_rel` 관측을 다음과 같이 None으로 설정하여 비활성화할 수도 있습니다:
`env.observations.policy.joint_pos_rel=null`.

### 사전

사전의 요소는 계층 구조의 매개변수로 처리됩니다. 예를 들어 Cartpole 환경에서:

```python
    reset_cart_position = EventTerm(
        func=mdp.reset_joints_by_offset,
        mode="reset",
        params={
            "asset_cfg": SceneEntityCfg("robot", joint_names=["slider_to_cart"]),
            "position_range": (-1.0, 1.0),
            "velocity_range": (-0.5, 0.5),
        },
    )

    reset_pole_position = EventTerm(
        func=mdp.reset_joints_by_offset,
        mode="reset",
        params={
            "asset_cfg": SceneEntityCfg("robot", joint_names=["cart_to_pole"]),
            "position_range": (-0.25 * math.pi, 0.25 * math.pi),
            "velocity_range": (-0.25 * math.pi, 0.25 * math.pi),
        },
    )


@configclass
class RewardsCfg:
    """Reward terms for the MDP."""

```

`position_range` 매개변수는 다음과 같이 수정할 수 있습니다:
`env.events.reset_cart_position.params.position_range="[-2.0, 2.0]"`.
이 예시는 두 가지 주목할 점을 보여줍니다:

- 설정하는 매개변수에 공백이 포함되어 있으므로 따옴표로 묶어야 합니다.
- 매개변수는 구성 파일에는 튜플이지만 목록으로 전달됩니다. 이는 Hydra가 튜플을 지원하지 않기 때문입니다.

## 상호 의존적인 매개변수 수정

명령줄 인수를 사용하여 매개변수를 수정할 때는 특별히 주의해야 합니다. 일부 구성은 다른 매개변수를 기반으로 중간 계산을 수행하며, 이러한 계산은 매개변수가 수정되었을 때 업데이트되지 않습니다.

예를 들어 Cartpole 카메라 깊이 환경의 구성에서:

```python
class CartpoleDepthCameraEnvCfg(CartpoleRGBCameraEnvCfg):
    # camera
    tiled_camera: TiledCameraCfg = TiledCameraCfg(
        prim_path="/World/envs/env_.*/Camera",
        offset=TiledCameraCfg.OffsetCfg(pos=(-5.0, 0.0, 2.0), rot=(1.0, 0.0, 0.0, 0.0), convention="world"),
        data_types=["depth"],
        spawn=sim_utils.PinholeCameraCfg(
            focal_length=24.0, focus_distance=400.0, horizontal_aperture=20.955, clipping_range=(0.1, 20.0)
        ),
        width=100,
        height=100,
    )

    # spaces
    observation_space = [tiled_camera.height, tiled_camera.width, 1]
```

사용자가 카메라의 너비를 수정한다고 가정해 보겠습니다. 즉 `env.tiled_camera.width=128`을 설정한 경우, 다음 매개변수도 업데이트하여 입력으로 제공해야 합니다:
`env.observation_space=[80,128,1]`.

마찬가지로, `__post_init__` 메서드는 명령줄 입력으로 업데이트되지 않습니다. 예를 들어 `LocomotionVelocityRoughEnvCfg`에서는 다음과 같이 후 초기화가 수행됩니다:

```python
class LocomotionVelocityRoughEnvCfg(ManagerBasedRLEnvCfg):
    """Configuration for the locomotion velocity-tracking environment."""

    # Scene settings
    scene: MySceneCfg = MySceneCfg(num_envs=4096, env_spacing=2.5)
    # Basic settings
    observations: ObservationsCfg = ObservationsCfg()
    actions: ActionsCfg = ActionsCfg()
    commands: CommandsCfg = CommandsCfg()
    # MDP settings
    rewards: RewardsCfg = RewardsCfg()
    terminations: TerminationsCfg = TerminationsCfg()
    events: EventCfg = EventCfg()
    curriculum: CurriculumCfg = CurriculumCfg()

    def __post_init__(self):
        """Post initialization."""
        # general settings
        self.decimation = 4
        self.episode_length_s = 20.0
        # simulation settings
        self.sim.dt = 0.005
        self.sim.render_interval = self.decimation
        self.sim.physx.gpu_max_rigid_patch_count = 10 * 2**15
        # update sensor update periods
        # we tick all the sensors based on the smallest update period (physics update period)
        if self.scene.height_scanner is not None:
            self.scene.height_scanner.update_period = self.decimation * self.sim.dt
        if self.scene.contact_forces is not None:
            self.scene.contact_forces.update_period = self.sim.dt

        # check if terrain levels curriculum is enabled - if so, enable curriculum for terrain generator
        # this generates terrains with increasing difficulty and is useful for training
        if getattr(self.curriculum, "terrain_levels", None) is not None:
            if self.scene.terrain.terrain_generator is not None:
                self.scene.terrain.terrain_generator.curriculum = True
        else:
            if self.scene.terrain.terrain_generator is not None:
                self.scene.terrain.terrain_generator.curriculum = False
```

여기서 `env.decimation` 또는 `env.sim.dt`를 수정하는 경우, 사용자는 다음 매개변수도 업데이트하여 입력으로 제공해야 합니다:
`env.sim.render_interval`, `env.scene.height_scanner.update_period`, 그리고 `env.scene.contact_forces.update_period`.
