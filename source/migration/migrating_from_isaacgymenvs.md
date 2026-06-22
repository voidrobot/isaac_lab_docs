<a id="migrating-from-isaacgymenvs"></a>

# IsaacGymEnvs에서

[IsaacGymEnvs](https://github.com/isaac-sim/IsaacGymEnvs)는 [Isaac Gym Preview Release](https://developer.nvidia.com/isaac-gym)를 위한 강화 학습 프레임워크였습니다.
IsaacGymEnvs와 Isaac Gym Preview Release 모두 더 이상 지원되지 않으므로, 다음 가이드에서는 IsaacGymEnvs와 Isaac Lab의 주요 차이점과 Isaac Gym Preview Release와 Isaac Sim 사이의 API 차이점을 안내합니다.

#### 참고
다음 변경 사항은 Isaac Lab 1.0 릴리스를 기준으로 합니다. 향후 릴리스의 변경 사항은 [릴리스 노트](https://github.com/isaac-sim/IsaacLab/releases)를 참조하십시오.

## 작업 구성 설정

IsaacGymEnvs에서는 작업 구성 파일이 `.yaml` 형식으로 정의되었습니다. Isaac Lab에서는 구성 설정이 특수한 Python 클래스 [`configclass`](../api/lab/isaaclab.utils.md#module-isaaclab.utils.configclass)를 사용하여 지정됩니다. [`configclass`](../api/lab/isaaclab.utils.md#module-isaaclab.utils.configclass) 모듈은 Python의 `dataclasses` 모듈을 래핑한 것입니다. 각 환경은 [`DirectRLEnvCfg`](../api/lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnvCfg)를 상속하고 `@configclass`로 주석이 달린 자체 구성 클래스를 지정해야 하며, 이 클래스는 시뮬레이션 매개변수, 환경 장면 매개변수, 로봇 매개변수 및 작업별 매개변수를 포함할 수 있습니다.

다음은 작업 구성 클래스의 예시 골격입니다:

```python
from isaaclab.envs import DirectRLEnvCfg
from isaaclab.scene import InteractiveSceneCfg
from isaaclab.sim import SimulationCfg

@configclass
class MyEnvCfg(DirectRLEnvCfg):
   # 시뮬레이션
   sim: SimulationCfg = SimulationCfg()
   # 로봇
   robot_cfg: ArticulationCfg = ArticulationCfg()
   # 장면
   scene: InteractiveSceneCfg = InteractiveSceneCfg()
   # 환경
   decimation = 2
   episode_length_s = 5.0
   action_space = 1
   observation_space = 4
   state_space = 0
   # 작업별 매개변수
   ...
```

### 시뮬레이션 구성

시뮬레이션 관련 매개변수는 [`SimulationCfg`](../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg) 클래스의 일부로 정의되며, 이 클래스는 `dt`, `device`, `gravity`와 같은 시뮬레이션 매개변수를 보유하는 [`configclass`](../api/lab/isaaclab.utils.md#module-isaaclab.utils.configclass) 모듈입니다. 각 작업 구성에서는 [`SimulationCfg`](../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg) 유형을 보유하는 변수 `sim`을 정의해야 합니다.

Isaac Lab에서는 `substeps` 사용이 시뮬레이션 `dt`와 `decimation` 매개변수의 조합으로 대체되었습니다. 예를 들어, IsaacGymEnvs에서 `dt=1/60`과 `substeps=2`는 `dt=1/120`으로 2단계 시뮬레이션 단계를 취하는 것과 동일하지만, 작업 단계는 `1/60`초 간격으로 실행됩니다. `decimation` 매개변수는 작업(또는 RL) 단계당 취할 시뮬레이션 단계 수를 제어하는 작업 매개변수이며, IsaacGymEnvs의 `controlFrequencyInv` 매개변수를 대체합니다. 따라서 Isaac Lab에서의 동일한 설정은 `dt=1/120`과 `decimation=2`가 됩니다.

Isaac Sim에서는 physx 시뮬레이션 매개변수인 `num_position_iterations`, `num_velocity_iterations`, `contact_offset`, `rest_offset`, `bounce_threshold_velocity`, `max_depenetration_velocity` 등을 액터 기준으로 지정할 수 있습니다. 이러한 매개변수는 physx 시뮬레이션 구성에서 개별 아티큘레이션 및 강체 구성으로 이동되었습니다.

GPU에서 시뮬레이션을 실행할 때, PhysX 버퍼는 접촉, 충돌 및 집약 쌍과 같은 정보를 계산하고 저장하기 위해 사전 할당이 필요합니다. 이러한 버퍼는 환경의 복잡성, 예상 접촉 및 충돌 수, 환경 내 액터 수에 따라 조정해야 할 수 있습니다. [`PhysxCfg`](../api/lab/isaaclab.sim.md#isaaclab.sim.PhysxCfg) 클래스는 GPU 버퍼 차원을 설정하는 데 사용됩니다.

| ```yaml<br/># IsaacGymEnvs<br/>sim:<br/><br/>  dt: 0.0166 # 1/60 s<br/>  substeps: 2<br/>  up_axis: "z"<br/>  use_gpu_pipeline: ${eq:${...pipeline},"gpu"}<br/>  gravity: [0.0, 0.0, -9.81]<br/>  physx:<br/>    num_threads: ${....num_threads}<br/>    solver_type: ${....solver_type}<br/>    use_gpu: ${contains:"cuda",${....sim_device}}<br/>    num_position_iterations: 4<br/>    num_velocity_iterations: 0<br/>    contact_offset: 0.02<br/>    rest_offset: 0.001<br/>    bounce_threshold_velocity: 0.2<br/>    max_depenetration_velocity: 100.0<br/>    default_buffer_size_multiplier: 2.0<br/>    max_gpu_contact_pairs: 1048576 # 1024*1024<br/>    num_subscenes: ${....num_subscenes}<br/>    contact_collection: 0<br/>```   | ```python<br/># IsaacLab<br/>sim: SimulationCfg = SimulationCfg(<br/>   device = "cuda:0" # can be "cpu", "cuda", "cuda:<device_id>"<br/>   dt=1 / 120,<br/>   # decimation will be set in the task config<br/>   # up axis will always be Z in isaac sim<br/>   # use_gpu_pipeline is deduced from the device<br/>   gravity=(0.0, 0.0, -9.81),<br/>   physx: PhysxCfg = PhysxCfg(<br/>       # num_threads is no longer needed<br/>       solver_type=1,<br/>       # use_gpu is deduced from the device<br/>       max_position_iteration_count=4,<br/>       max_velocity_iteration_count=0,<br/>       # moved to actor config<br/>       # moved to actor config<br/>       bounce_threshold_velocity=0.2,<br/>       # moved to actor config<br/>       # default_buffer_size_multiplier is no longer needed<br/>       gpu_max_rigid_contact_count=2**23<br/>       # num_subscenes is no longer needed<br/>       # contact_collection is no longer needed<br/>))<br/>```   |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

### 장면 구성

[`InteractiveSceneCfg`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스는 환경 수와 환경 간 간격과 같은 장면 관련 매개변수를 지정하는 데 사용할 수 있습니다. 각 작업 구성에서는 [`InteractiveSceneCfg`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 유형을 보유하는 변수 `scene`을 정의해야 합니다.

| ```yaml<br/># IsaacGymEnvs<br/>env:<br/>  numEnvs: ${resolve_default:512,${...num_envs}}<br/>  envSpacing: 4.0<br/>```   | ```python<br/># IsaacLab<br/>scene: InteractiveSceneCfg = InteractiveSceneCfg(<br/>   num_envs=512,<br/>   env_spacing=4.0)<br/>```   |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|

### 작업 구성

각 환경은 관측 및 액션 버퍼의 차원과 같은 작업별 매개변수를 보유하는 자체 구성 클래스를 지정해야 합니다. 보상 항 스케일링 매개변수도 구성 클래스에 지정할 수 있습니다.

각 환경 구성에 다음 매개변수를 설정해야 합니다:

```python
decimation = 2
episode_length_s = 5.0
action_space = 1
observation_space = 4
state_space = 0
```

참고: 최대 에피소드 길이 매개변수(현재 `episode_length_s`)는 IsaacGymEnvs에서의 단계 단위 대신 초 단위로 지정됩니다. 단계 수를 초 단위로 변환하려면 다음 방정식을 사용하세요:
`episode_length_s = dt * decimation * num_steps`

## RL 구성 설정

rl_games 라이브러리를 위한 RL 구성 파일은 Isaac Lab에서도 `.yaml` 파일로 계속 정의할 수 있습니다. 구성 파일의 대부분은 IsaacGymEnvs에서 직접 복사할 수 있습니다.
Isaac Lab에서는 상대 경로를 해결하기 위해 hydra를 사용하지 않으므로, `${....device}`와 같은 상대 경로는 실제 매개변수 값으로 교체해야 합니다.

또한, 관측 및 액션 클립 범위는 RL 구성 파일로 이동되었습니다. IsaacGymEnvs 작업 구성 파일에서 정의된 `clipObservations` 및 `clipActions` 매개변수는 Isaac Lab의 RL 구성 파일로 이동해야 합니다.

| IsaacGymEnvs 작업 구성                                                                       | Isaac Lab RL 구성                                                                                            |
|------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| ```yaml<br/># IsaacGymEnvs<br/>env:<br/>  clipObservations: 5.0<br/>  clipActions: 1.0<br/>``` | ```yaml<br/># IsaacLab<br/>params:<br/>  env:<br/>    clip_observations: 5.0<br/>    clip_actions: 1.0<br/>``` |

## 환경 생성

IsaacGymEnvs에서 환경 생성은 일반적으로 다음과 같은 네 가지 구성 요소를 포함했습니다: `create_sim()`로 sim 객체를 생성하고, 지상 평면을 생성하고, MJCF 또는 URDF 파일에서 에셋을 가져오고, 마지막으로 각 환경을 순환하며 에셋을 환경에 추가하여 환경을 만드는 것이었습니다.

Isaac Lab에서는 더 이상 `create_sim()` 메서드를 호출하여 sim 객체를 가져올 필요가 없습니다. 대신 프레임워크가 자동으로 시뮬레이션 컨텍스트를 가져옵니다. 또한 시뮬레이션 API에 `sim`을 인수로 전달할 필요가 더 이상 없습니다.

`create_sim()`을 대체하여 Isaac Lab의 `_setup_scene()` 메서드에서 작업을 구현할 수 있습니다. 이 메서드를 사용하면 장면을 위한 액터 추가, 지상 평면 추가, 액터 복제, 그리고 조명과 같은 기타 선택적 객체 추가를 수행할 수 있습니다.

| IsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def create_sim(self):<br/>  # set the up axis to be z-up<br/>  self.up_axis = self.cfg["sim"]["up_axis"]<br/><br/>  self.sim = super().create_sim(self.device_id, self.graphics_device_id,<br/>                                  self.physics_engine, self.sim_params)<br/>  self._create_ground_plane()<br/>  self._create_envs(self.num_envs, self.cfg["env"]['envSpacing'],<br/>                      int(np.sqrt(self.num_envs)))<br/>``` | ```python<br/>def _setup_scene(self):<br/>  self.cartpole = Articulation(self.cfg.robot_cfg)<br/>  # add ground plane<br/>  spawn_ground_plane(prim_path="/World/ground", cfg=GroundPlaneCfg()<br/>  # clone, filter, and replicate<br/>  self.scene.clone_environments(copy_from_source=False)<br/>  self.scene.filter_collisions(global_prim_paths=[])<br/>  # add articulation to scene<br/>  self.scene.articulations["cartpole"] = self.cartpole<br/>  # add lights<br/>  light_cfg = sim_utils.DomeLightCfg(intensity=2000.0)<br/>  light_cfg.func("/World/Light", light_cfg)<br/>``` |

### 지상 평면

Isaac Lab에서는 대부분의 환경 생성 프로세스가 [`configclass`](../api/lab/isaaclab.utils.md#module-isaaclab.utils.configclass) 모듈을 사용한 구성으로 간소화되었습니다.

지상 평면은 [`TerrainImporterCfg`](../api/lab/isaaclab.terrains.md#isaaclab.terrains.TerrainImporterCfg) 클래스를 사용하여 정의할 수 있습니다.

```python
from isaaclab.terrains import TerrainImporterCfg

terrain = TerrainImporterCfg(
     prim_path="/World/ground",
     terrain_type="plane",
     collision_group=-1,
     physics_material=sim_utils.RigidBodyMaterialCfg(
         friction_combine_mode="multiply",
         restitution_combine_mode="multiply",
         static_friction=1.0,
         dynamic_friction=1.0,
         restitution=0.0,
     ),
 )
```

지상 평면은 `_setup_scene(self)`에서 `TerrainImporterCfg` 객체를 참조하여 추가할 수 있습니다:

<!-- code-block::python

def _setup_scene(self):
   ...
   self.cfg.terrain.num_envs = self.scene.cfg.num_envs
   self.cfg.terrain.env_spacing = self.scene.cfg.env_spacing
   self._terrain = self.cfg.terrain.class_type(self.cfg.terrain) -->

### 액터

Isaac Lab과 Isaac Sim은 모두 [USD (Universal Scene Description)](https://github.com/PixarAnimationStudios/OpenUSD) 라이브러리를 사용하여 장면을 설명합니다. MJCF 및 URDF 형식으로 정의된 에셋은 [새 에셋 가져오기](../how-to/import_new_asset.html) 튜토리얼에 설명된 가져오기 도구를 사용하여 USD로 가져올 수 있습니다.

각 Articulation 및 Rigid Body 액터도 자체 구성 클래스를 가질 수 있습니다. [`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg) 클래스는 파일 경로, 시뮬레이션 매개변수, 액추에이터 속성 및 초기 상태와 같은 액추에이션 액터의 매개변수를 정의하는 데 사용할 수 있습니다.

<!-- code-block::python

from isaaclab.actuators import ImplicitActuatorCfg
from isaaclab.assets import ArticulationCfg

CARTPOLE_CFG = ArticulationCfg(
    spawn=sim_utils.UsdFileCfg(
        usd_path=f"{ISAACLAB_NUCLEUS_DIR}/Robots/Classic/Cartpole/cartpole.usd",
        rigid_props=sim_utils.RigidBodyPropertiesCfg(
            rigid_body_enabled=True,
            max_linear_velocity=1000.0,
            max_angular_velocity=1000.0,
            max_depenetration_velocity=100.0,
            enable_gyroscopic_forces=True,
        ),
        articulation_props=sim_utils.ArticulationRootPropertiesCfg(
            enabled_self_collisions=False,
            solver_position_iteration_count=4,
            solver_velocity_iteration_count=0,
            sleep_threshold=0.005,
            stabilization_threshold=0.001,
        ),
    ),
    init_state=ArticulationCfg.InitialStateCfg(
        pos=(0.0, 0.0, 2.0), joint_pos={"slider_to_cart": 0.0, "cart_to_pole": 0.0}
    ),
    actuators={
        "cart_actuator": ImplicitActuatorCfg(
            joint_names_expr=["slider_to_cart"],
            effort_limit=400.0,
            velocity_limit=100.0,
            stiffness=0.0,
            damping=10.0,
        ),
        "pole_actuator": ImplicitActuatorCfg(
            joint_names_expr=["cart_to_pole"], effort_limit=400.0, velocity_limit=100.0, stiffness=0.0, damping=0.0
        ),
    },
) -->

[`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg)에서 `spawn` 속성을 사용하여 로봇 파일의 경로를 지정하여 장면에 로봇을 추가할 수 있습니다. 또한 [`RigidBodyPropertiesCfg`](../api/lab/isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg)를 사용하여 액추에이션의 강성 본에 대한 시뮬레이션 속성을 지정할 수 있습니다. 마찬가지로 [`ArticulationRootPropertiesCfg`](../api/lab/isaaclab.sim.schemas.md#isaaclab.sim.schemas.ArticulationRootPropertiesCfg) 클래스를 사용하여 액추에이션 루트에 대한 시뮬레이션 속성을 지정할 수 있습니다. 조인트 속성은 이제 [`ImplicitActuatorCfg`](../api/lab/isaaclab.actuators.md#isaaclab.actuators.ImplicitActuatorCfg)를 사용하는 `actuators` 사전의 일부로 지정됩니다. 동일한 속성을 가진 조인트는 정규 표현식 또는 이름 또는 표현식의 리스트로 그룹화할 수 있습니다.

액터는 단순히 `self.cartpole = Articulation(self.cfg.robot_cfg)`를 호출하여 장면에 추가할 수 있으며, 여기서 `self.cfg.robot_cfg`는 [`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg) 객체입니다. 초기화된 후에는 `self.scene.articulations["cartpole"] = self.cartpole`를 호출하여 [`InteractiveScene`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveScene)에 추가해야 하며, 이를 통해 [`InteractiveScene`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveScene)가 장면을 순회하여 시뮬레이션에 값을 쓰고 재설정할 수 있습니다.

#### 액터의 시뮬레이션 매개변수

Rigid Body 및 Articulation과 관련된 일부 시뮬레이션 매개변수는 Isaac Gym 프리뷰 릴리즈와 Isaac Sim 간에 기본값이 다를 수 있습니다. 자산에 적용 가능한 기본값이 적용되는지 확인하기 위해 USD 자산을 두 번 확인하는 것이 도움이 될 수 있습니다.

예를 들어, `RigidBodyAPI`의 다음 매개변수는 Isaac Gym 프리뷰 릴리즈와 Isaac Sim 간에 기본값이 다를 수 있습니다:

| RigidBodyAPI 매개변수   | Isaac Sim에서의 기본값   | Isaac Gym 프리뷰 릴리즈에서의 기본값   |
|--------------------------|------------------------------|----------------------------------------------|
| Linear Damping           | 0.00                         | 0.00                                         |
| Angular Damping          | 0.05                         | 0.0                                          |
| Max Linear Velocity      | inf                          | 1000                                         |
| Max Angular Velocity     | 5729.58008 (degree/s)        | 64.0 (rad/s)                                 |
| 최대 접촉 충격      | inf                          | 1e32                                         |

`JointAPI` 및 `DriveAPI`의 관절 파라미터도 변경할 수 있습니다. Isaac Sim UI에서는 각도의 단위를 도(degree)로 가정한다는 점에 유의해야 합니다. 특히 `DriveAPI`의 `Damping` 및 `Stiffness` 파라미터는 Isaac Sim UI에서는 단위가 `1/deg`이지만, Isaac Gym 미리보기 릴리즈에서는 `1/rad`라는 점을 기억하는 것이 중요합니다.

| 관절 파라미터        | Isaac Sim의 기본값            | Isaac Gym 미리보기 릴리즈의 기본값    |
|------------------------|------------------------------|-----------------------------------------------|
| 최대 관절 속도 | 1000000.0 (deg)              | 100.0 (rad)                                   |

Isaac Gym과 Isaac Lab 간에 철저한 시뮬레이션 비교를 수행하는 방법에 대한 자세한 내용은 [Isaac Gym과 Isaac Lab 간 시뮬레이션 비교](comparing_simulation_isaacgym.md#migrating-from-isaacgymenvs-comparing-simulation) 섹션을 참조하십시오.

### Cloner

Isaac Sim은 장면을 생성하는 과정 중 복제를 위해 설계된 `Cloner`라는 개념을 도입했습니다. IsaacGymEnvs에서는 환경을 루프를 통해 생성해야 했으며, 각 반복마다 환경에 액터를 추가하고 핸들을 캐시해야 했습니다. Isaac Lab은 `Cloner` API를 사용하여 환경을 루프 처리할 필요가 없습니다. 장면을 생성하는 과정은 다음과 같습니다:

1. 단일 환경 구성 (환경 수가 1일 때 장면에 해당되는 모습)
2. `clone_environments()`를 호출하여 단일 환경 복제
3. `filter_collisions()`를 호출하여 환경 간 충돌 필터링 (필요한 경우)

```python
# Cartpole 로봇으로 단일 환경 구성
self.cartpole = Articulation(self.cfg.robot_cfg)
# 환경 복제
self.scene.clone_environments(copy_from_source=False)
# 충돌 필터링
self.scene.filter_collisions(global_prim_paths=[self.cfg.terrain.prim_path])
```

## 시뮬레이션에서 상태 접근하기

Isaac Lab에서 물리 상태에 접근하는 API는 [`Articulation`](../api/lab/isaaclab.assets.md#isaaclab.assets.Articulation) 또는 [`RigidObject`](../api/lab/isaaclab.assets.md#isaaclab.assets.RigidObject) 객체를 생성해야 합니다. 위에서 설명한 바와 같이 해당 [`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg) 또는 [`RigidObjectCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.RigidObjectCfg) 구성을 정의하여 장면의 다양한 관절 또는 강체에 대해 여러 객체를 초기화할 수 있습니다. 이 접근 방식은 장면의 특정 강체에 대한 상태를 슬라이스하기 위해 바디 핸들을 검색하는 필요가 없어집니다.

```python
self._robot = Articulation(self.cfg.robot)
self._cabinet = Articulation(self.cfg.cabinet)
self._object = RigidObject(self.cfg.object_cfg)
```

또한 Isaac Lab에서는 `acquire` 및 `refresh` API를 제거했습니다. 물리 상태는 관절 및 강체에 대해 정의된 API를 사용하여 직접 적용하거나 검색할 수 있습니다.

Isaac Lab에서 제공하는 API는 더 이상 기반 버퍼의 명시적 래핑 및 언래핑이 필요하지 않습니다. 이제 API는 텐서로 직접 데이터를 읽고 쓸 수 있습니다.

| IsaacGymEnvs                                                                                                                                                                                     | Isaac Lab                                                                                                         |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| ```python<br/>dof_state_tensor = self.gym.acquire_dof_state_tensor(self.sim)<br/>self.dof_state = gymtorch.wrap_tensor(dof_state_tensor)<br/>self.gym.refresh_dof_state_tensor(self.sim)<br/>``` | ```python<br/>self.joint_pos = self._robot.data.joint_pos<br/>self.joint_vel = self._robot.data.joint_vel<br/>``` |

Isaac Gym 미리보기 릴리즈와 Isaac Lab의 API 이름 차이에 유의하시기 바랍니다. 대부분의 `dof` 관련 API는 Isaac Lab에서 `joint`로 이름이 변경되었습니다. Isaac Lab의 API는 더 이상 명시적 `_tensors` 또는 `_tensor_indexed` 접미사를 따르지 않으며, 인덱싱된 버전의 API는 선택적 `indices` 매개변수를 통해 암시적으로 처리됩니다.

Isaac Lab의 대부분의 API는 또한 선택적 `indices` 매개변수를 제공하여 환경의 하위 집합에 대한 데이터 읽기 또는 쓰기를 가능하게 합니다. `indices` 매개변수를 사용하여 상태를 설정할 때 상태 버퍼의 형태가 `indices` 목록의 차원과 일치해야 함에 유의하시기 바랍니다.

| IsaacGymEnvs                                                                                                                                                                                                                               | Isaac Lab                                                                                                                                 |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>env_ids_int32 = env_ids.to(dtype=torch.int32)<br/>self.gym.set_dof_state_tensor_indexed(self.sim,<br/>    gymtorch.unwrap_tensor(self.dof_state),<br/>    gymtorch.unwrap_tensor(env_ids_int32), len(env_ids_int32))<br/>``` | ```python<br/>self._robot.write_joint_state_to_sim(joint_pos, joint_vel,<br/>                                 joint_ids, env_ids)<br/>``` |

### 쿼터니언 convention

Isaac Lab과 Isaac Sim은 모두 `wxyz`를 쿼터니언 convention으로 채택하고 있습니다. 그러나 Isaac Gym 미리보기 릴리즈에서 사용된 쿼터니언 convention은 `xyzw`였습니다. 회전 데이터 인덱싱 시 모든 쿼터니언을 `xyzw` convention으로 전환해야 함을 기억하시기 바랍니다. 마찬가지로, Isaac Lab API에 쿼터니언을 전달하기 전에 모든 쿼터니어가 `wxyz` convention인지 확인하시기 바랍니다.

### 관절 순서

Isaac Sim과 Isaac Lab에서의 물리 시뮬레이션은 주어진 운동학 트리의 관절에 대해 너비 우선(breadth-first) 순서를 가정합니다. 그러나 Isaac Gym 미리보기 릴리즈는 운동학 트리의 관절에 대해 깊이 우선(depth-first) 순서를 가정했습니다. 이는 관절의 순서를 기반으로 인덱싱할 때 IsaacGymEnvs와 Isaac Lab 간에 결과가 다를 수 있음을 의미합니다.

Isaac Lab에서는 `Articulation.data.joint_names`를 사용하여 관절 이름 목록을 검색할 수 있으며, 이는 또한 관절이 Articulation에서의 순서와 일치합니다.

## 새 환경 생성

Isaac Lab의 각 환경은 다음과 같은 구조를 가진 자체 디렉터리에 있어야 합니다:

```none
my_environment/
    - agents/
        - __init__.py
        - rl_games_ppo_cfg.py
    - __init__.py
    my_env.py
```

* `my_environment`는 작업의 루트 디렉터리입니다.
* `my_environment/agents`는 해당 작업의 모든 RL 구성 파일을 포함하는 디렉터리입니다. Isaac Lab은 여러 RL 라이브러리를 지원하며, 각 라이브러리마다 자체 개별 구성 파일을 가질 수 있습니다.
* `my_environment/__init__.py`는 Gymnasium 인터페이스와 환경을 등록하는 메인 파일입니다. 이를 통해 훈련 및 추론 스크립트가 이름에 따라 작업을 찾을 수 있습니다. 이 파일의 내용은 다음과 같아야 합니다:

```python
import gymnasium as gym

from . import agents
from .cartpole_env import CartpoleEnv, CartpoleEnvCfg

##
# Register Gym environments.
##

gym.register(
    id="Isaac-Cartpole-Direct-v0",
    entry_point="isaaclab_tasks.direct_workflow.cartpole:CartpoleEnv",
    disable_env_checker=True,
    kwargs={
        "env_cfg_entry_point": CartpoleEnvCfg,
        "rl_games_cfg_entry_point": f"{agents.__name__}:rl_games_ppo_cfg.yaml"
    },
)
```

* `my_environment/my_env.py`는 작업 로직 및 환경의 작업 구성 클래스를 구현하는 메인 파이썬 스크립트입니다.

## 작업 로직

Isaac Lab에서는 `post_physics_step` 함수가 기본 클래스의 프레임워크로 이동되었습니다. 작업은 이 메서드를 구현할 필요가 없지만, 다른 워크플로가 필요할 경우 오버라이드할 수 있습니다.

기본적으로 Isaac Lab은 다음과 같은 흐름을 따릅니다:

| IsaacGymEnvs                                                                                                                                                                 | Isaac Lab                                                                                                                                                                                                                          |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```none<br/>pre_physics_step<br/>  |-- apply_action<br/><br/><br/>post_physics_step<br/>  |-- reset_idx()<br/>  |-- compute_observation()<br/>  |-- compute_reward()<br/>``` | ```none<br/>pre_physics_step<br/>  |-- _pre_physics_step(action)<br/>  |-- _apply_action()<br/><br/>post_physics_step<br/>  |-- _get_dones()<br/>  |-- _get_rewards()<br/>  |-- _reset_idx()<br/>  |-- _get_observations()<br/>``` |

Isaac Lab에서는 정책에서 azioni를 처리하는 `pre_physics_step` API와 시뮬레이션에 azioni를 설정하는 `apply_action` API를 분리했습니다.これにより、`decimation`이 사용될 때 시뮬레이션에 azioni를 쓸 시기를 더 유연하게 제어할 수 있습니다.
`pre_physics_step`은 시뮬레이션 스텝을 실행하기 전에 한 번씩 호출됩니다.
`apply_actions`는 각 RL 스텝에 대해 `decimation`번 호출되며, 각 시뮬레이션 스텝 호출 전마다 한 번씩 실행됩니다.

이 접근 방식에서는 현재 스텝의 행동을 기반으로 리셋이 수행되며, 이전 스텝의 행동이 아닌 현재 스텝의 행동을 사용합니다.
관측값도 리셋 이후의 올바른 상태를 반영하여 계산됩니다.

또한 일부 API 이름이 변경되었습니다:

* `create_sim(self)` –> `_setup_scene(self)`
* `pre_physics_step(self, actions)` –> `_pre_physics_step(self, actions)` 및 `_apply_action(self)`
* `reset_idx(self, env_ids)` –> `_reset_idx(self, env_ids)`
* `compute_observations(self)` –> `_get_observations(self)` - `_get_observations()`은 이제 딕셔너리 `{"policy": obs}`를 반환해야 합니다
* `compute_reward(self)` –> `_get_rewards(self)` - `_get_rewards()`는 이제 리워드 버퍼를 반환해야 합니다
* `post_physics_step(self)` –> 베이스 클래스로 이동됨
* 또한, Isaac Lab에서는 `_is_done(self)`를 구현해야 하며, 이 함수는 `reset` 버퍼와 `time_out` 버퍼 두 가지를 반환해야 합니다.

## 전체 구조 정리

Cartpole 환경은 여기에서 완전한 형태로 보여지며, IsaacGymEnvs 구현과 Isaac Lab 구현 사이의 비교를 명확히 보여줍니다.

### 태스크 구성

| IsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```yaml<br/># used to create the object<br/>name: Cartpole<br/><br/>physics_engine: ${..physics_engine}<br/><br/># if given, will override the device setting in gym.<br/>env:<br/>  numEnvs: ${resolve_default:512,${...num_envs}}<br/>  envSpacing: 4.0<br/>  resetDist: 3.0<br/>  maxEffort: 400.0<br/><br/>  clipObservations: 5.0<br/>  clipActions: 1.0<br/><br/>  asset:<br/>    assetRoot: "../../assets"<br/>    assetFileName: "urdf/cartpole.urdf"<br/><br/>  enableCameraSensors: False<br/><br/>sim:<br/>  dt: 0.0166 # 1/60 s<br/>  substeps: 2<br/>  up_axis: "z"<br/>  use_gpu_pipeline: ${eq:${...pipeline},"gpu"}<br/>  gravity: [0.0, 0.0, -9.81]<br/>  physx:<br/>    num_threads: ${....num_threads}<br/>    solver_type: ${....solver_type}<br/>    use_gpu: ${contains:"cuda",${....sim_device}}<br/>    num_position_iterations: 4<br/>    num_velocity_iterations: 0<br/>    contact_offset: 0.02<br/>    rest_offset: 0.001<br/>    bounce_threshold_velocity: 0.2<br/>    max_depenetration_velocity: 100.0<br/>    default_buffer_size_multiplier: 2.0<br/>    max_gpu_contact_pairs: 1048576 # 1024*1024<br/>    num_subscenes: ${....num_subscenes}<br/>    contact_collection: 0<br/>``` | ```python<br/>@configclass<br/>class CartpoleEnvCfg(DirectRLEnvCfg):<br/><br/>    # simulation<br/>    sim: SimulationCfg = SimulationCfg(dt=1 / 120)<br/>    # robot<br/>    robot_cfg: ArticulationCfg = CARTPOLE_CFG.replace(<br/>        prim_path="/World/envs/env_.*/Robot")<br/>    cart_dof_name = "slider_to_cart"<br/>    pole_dof_name = "cart_to_pole"<br/>    # scene<br/>    scene: InteractiveSceneCfg = InteractiveSceneCfg(<br/>        num_envs=4096, env_spacing=4.0, replicate_physics=True)<br/>    # env<br/>    decimation = 2<br/>    episode_length_s = 5.0<br/>    action_scale = 100.0  # [N]<br/>    action_space = 1<br/>    observation_space = 4<br/>    state_space = 0<br/>    # reset<br/>    max_cart_pos = 3.0<br/>    initial_pole_angle_range = [-0.25, 0.25]<br/>    # reward scales<br/>    rew_scale_alive = 1.0<br/>    rew_scale_terminated = -2.0<br/>    rew_scale_pole_pos = -1.0<br/>    rew_scale_cart_vel = -0.01<br/>    rew_scale_pole_vel = -0.005<br/>``` |

### 태스크 설정

Isaac Lab에서는 더 이상 `acquire_*` API를 사용한 버퍼의 사전 초기화가 필요하지 않습니다.
또한 `wrap` 및 `unwrap` 텐서 작업도 더 이상 필요하지 않습니다.

| IsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
</translate>
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>class Cartpole(VecTask):<br/><br/>  def __init__(self, cfg, rl_device, sim_device, graphics_device_id,<br/>   headless, virtual_screen_capture, force_render):<br/>      self.cfg = cfg<br/><br/>      self.reset_dist = self.cfg["env"]["resetDist"]<br/><br/>      self.max_push_effort = self.cfg["env"]["maxEffort"]<br/>      self.max_episode_length = 500<br/><br/>      self.cfg["env"]["numObservations"] = 4<br/>      self.cfg["env"]["numActions"] = 1<br/><br/>      super().__init__(config=self.cfg,<br/>         rl_device=rl_device, sim_device=sim_device,<br/>         graphics_device_id=graphics_device_id, headless=headless,<br/>         virtual_screen_capture=virtual_screen_capture,<br/>         force_render=force_render)<br/><br/>      dof_state_tensor = self.gym.acquire_dof_state_tensor(self.sim)<br/>      self.dof_state = gymtorch.wrap_tensor(dof_state_tensor)<br/>      self.dof_pos = self.dof_state.view(<br/>          self.num_envs, self.num_dof, 2)[..., 0]<br/>      self.dof_vel = self.dof_state.view(<br/>          self.num_envs, self.num_dof, 2)[..., 1]<br/>``` | ```python<br/>class CartpoleEnv(DirectRLEnv):<br/>  cfg: CartpoleEnvCfg<br/>  def __init__(self, cfg: CartpoleEnvCfg,<br/>          render_mode: str | None = None, **kwargs):<br/><br/>      super().__init__(cfg, render_mode, **kwargs)<br/><br/>      self._cart_dof_idx, _ = self.cartpole.find_joints(<br/>          self.cfg.cart_dof_name)<br/>      self._pole_dof_idx, _ = self.cartpole.find_joints(<br/>          self.cfg.pole_dof_name)<br/>      self.action_scale = self.cfg.action_scale<br/><br/>      self.joint_pos = self.cartpole.data.joint_pos<br/>      self.joint_vel = self.cartpole.data.joint_vel<br/>``` |

### Scene Setup

Scene setup is now done through the `Cloner` API and by specifying actor attributes in config objects.
This eliminates the need to loop through the number of environments to set up the environments and avoids
the need to set simulation parameters for actors in the task implementation.

| IsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def create_sim(self):<br/>    # set the up axis to be z-up given that assets are y-up by default<br/>    self.up_axis = self.cfg["sim"]["up_axis"]<br/><br/>    self.sim = super().create_sim(self.device_id,<br/>        self.graphics_device_id, self.physics_engine,<br/>        self.sim_params)<br/>    self._create_ground_plane()<br/>    self._create_envs(self.num_envs,<br/>        self.cfg["env"]['envSpacing'],<br/>        int(np.sqrt(self.num_envs)))<br/><br/>def _create_ground_plane(self):<br/>    plane_params = gymapi.PlaneParams()<br/>    # set the normal force to be z dimension<br/>    plane_params.normal = (gymapi.Vec3(0.0, 0.0, 1.0)<br/>        if self.up_axis == 'z'<br/>        else gymapi.Vec3(0.0, 1.0, 0.0))<br/>    self.gym.add_ground(self.sim, plane_params)<br/><br/>def _create_envs(self, num_envs, spacing, num_per_row):<br/>    # define plane on which environments are initialized<br/>    lower = (gymapi.Vec3(0.5 * -spacing, -spacing, 0.0)<br/>        if self.up_axis == 'z'<br/>        else gymapi.Vec3(0.5 * -spacing, 0.0, -spacing))<br/>    upper = gymapi.Vec3(0.5 * spacing, spacing, spacing)<br/><br/>    asset_root = os.path.join(os.path.dirname(<br/>        os.path.abspath(__file__)), "../../assets")<br/>    asset_file = "urdf/cartpole.urdf"<br/><br/>    if "asset" in self.cfg["env"]:<br/>        asset_root = os.path.join(os.path.dirname(<br/>            os.path.abspath(__file__)),<br/>            self.cfg["env"]["asset"].get("assetRoot", asset_root))<br/>        asset_file = self.cfg["env"]["asset"].get(<br/>            "assetFileName", asset_file)<br/><br/>    asset_path = os.path.join(asset_root, asset_file)<br/>    asset_root = os.path.dirname(asset_path)<br/>    asset_file = os.path.basename(asset_path)<br/><br/>    asset_options = gymapi.AssetOptions()<br/>    asset_options.fix_base_link = True<br/>    cartpole_asset = self.gym.load_asset(self.sim,<br/>        asset_root, asset_file, asset_options)<br/>    self.num_dof = self.gym.get_asset_dof_count(<br/>        cartpole_asset)<br/><br/>    pose = gymapi.Transform()<br/>    if self.up_axis == 'z':<br/>        pose.p.z = 2.0<br/>        pose.r = gymapi.Quat(0.0, 0.0, 0.0, 1.0)<br/>    else:<br/>        pose.p.y = 2.0<br/>        pose.r = gymapi.Quat(<br/>            -np.sqrt(2)/2, 0.0, 0.0, np.sqrt(2)/2)<br/><br/>    self.cartpole_handles = []<br/>    self.envs = []<br/>    for i in range(self.num_envs):<br/>        # create env instance<br/>        env_ptr = self.gym.create_env(<br/>            self.sim, lower, upper, num_per_row<br/>        )<br/>        cartpole_handle = self.gym.create_actor(<br/>            env_ptr, cartpole_asset, pose,<br/>            "cartpole", i, 1, 0)<br/><br/>        dof_props = self.gym.get_actor_dof_properties(<br/>            env_ptr, cartpole_handle)<br/>        dof_props['driveMode'][0] = gymapi.DOF_MODE_EFFORT<br/>        dof_props['driveMode'][1] = gymapi.DOF_MODE_NONE<br/>        dof_props['stiffness'][:] = 0.0<br/>        dof_props['damping'][:] = 0.0<br/>        self.gym.set_actor_dof_properties(env_ptr, c<br/>            artpole_handle, dof_props)<br/><br/>        self.envs.append(env_ptr)<br/>        self.cartpole_handles.append(cartpole_handle)<br/>``` | ```python<br/>def _setup_scene(self):<br/>    self.cartpole = Articulation(self.cfg.robot_cfg)<br/>    # add ground plane<br/>    spawn_ground_plane(prim_path="/World/ground",<br/>        cfg=GroundPlaneCfg())<br/>    # clone, filter, and replicate<br/>    self.scene.clone_environments(<br/>        copy_from_source=False)<br/>    self.scene.filter_collisions(<br/>        global_prim_paths=[])<br/>    # add articulation to scene<br/>    self.scene.articulations["cartpole"] = self.cartpole<br/>    # add lights<br/>    light_cfg = sim_utils.DomeLightCfg(<br/>        intensity=2000.0, color=(0.75, 0.75, 0.75))<br/>    light_cfg.func("/World/Light", light_cfg)<br/><br/>CARTPOLE_CFG = ArticulationCfg(<br/>    spawn=sim_utils.UsdFileCfg(<br/>        usd_path=f"{ISAACLAB_NUCLEUS_DIR}/.../cartpole.usd",<br/>        rigid_props=sim_utils.RigidBodyPropertiesCfg(<br/>            rigid_body_enabled=True,<br/>            max_linear_velocity=1000.0,<br/>            max_angular_velocity=1000.0,<br/>            max_depenetration_velocity=100.0,<br/>            enable_gyroscopic_forces=True,<br/>        ),<br/>        articulation_props=sim_utils.ArticulationRootPropertiesCfg(<br/>            enabled_self_collisions=False,<br/>            solver_position_iteration_count=4,<br/>            solver_velocity_iteration_count=0,<br/>            sleep_threshold=0.005,<br/>            stabilization_threshold=0.001,<br/>        ),<br/>    ),<br/>    init_state=ArticulationCfg.InitialStateCfg(<br/>        pos=(0.0, 0.0, 2.0),<br/>        joint_pos={"slider_to_cart": 0.0, "cart_to_pole": 0.0}<br/>    ),<br/>    actuators={<br/>        "cart_actuator": ImplicitActuatorCfg(<br/>            joint_names_expr=["slider_to_cart"],<br/>            effort_limit_sim=400.0,<br/>            velocity_limit_sim=100.0,<br/>            stiffness=0.0,<br/>            damping=10.0,<br/>        ),<br/>        "pole_actuator": ImplicitActuatorCfg(<br/>            joint_names_expr=["cart_to_pole"],<br/>            effort_limit_sim=400.0, velocity_limit_sim=100.0,<br/>            stiffness=0.0, damping=0.0<br/>        ),<br/>    ),<br/>``` |

### Pre와 Post 물리 단계

IsaacGymEnvs에서는 GPU API의 제한으로 인해 환경을 리셋해야 할 때 관측 데이터가 오래된 경우가 있었습니다.
이 제한은 Isaac Lab에서 제거되었으며, 따라서 작업은 행동 적용, 시뮬레이션 진행, 상태 수집, 종료 계산, 보상 계산, 리셋 수행, 마지막으로 관측 계산 순으로 올바른 워크플로를 따릅니다.
이 워크플로는 프레임워크에서 자동으로 처리되므로 작업에서 `post_physics_step` API가 필요하지 않습니다.
다만 개별 작업은 `step()` API를 오버라이드하여 워크플로를 제어할 수 있습니다.

| IsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | IsaacLab                                                                                                                                                                                                                                                                               |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def pre_physics_step(self, actions):<br/>    actions_tensor = torch.zeros(<br/>        self.num_envs * self.num_dof,<br/>        device=self.device, dtype=torch.float)<br/>    actions_tensor[::self.num_dof] = actions.to(<br/>        self.device).squeeze() * self.max_push_effort<br/>    forces = gymtorch.unwrap_tensor(actions_tensor)<br/>    self.gym.set_dof_actuation_force_tensor(<br/>        self.sim, forces)<br/><br/>def post_physics_step(self):<br/>    self.progress_buf += 1<br/><br/>    env_ids = self.reset_buf.nonzero(<br/>        as_tuple=False).squeeze(-1)<br/>    if len(env_ids) > 0:<br/>        self.reset_idx(env_ids)<br/><br/>    self.compute_observations()<br/>    self.compute_reward()<br/>``` | ```python<br/>def _pre_physics_step(self, actions: torch.Tensor) -> None:<br/>    self.actions = self.action_scale * actions<br/><br/>def _apply_action(self) -> None:<br/>    self.cartpole.set_joint_effort_target(<br/>         self.actions, joint_ids=self._cart_dof_idx)<br/>``` |

### 종료 및 리셋

Isaac Lab에서 `dones`는 `_get_dones()` 메서드에서 계산되며, `resets`와 `time_out` 두 변수를 반환해야 합니다.
`progress_buf` 추적은 기본 클래스로 이동되었으며, 프레임워크에서 자동으로 증가하고 리셋됩니다.
`progress_buf` 변수도 `episode_length_buf`로 이름이 변경되었습니다.

| IsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def reset_idx(self, env_ids):<br/>    positions = 0.2 * (torch.rand((len(env_ids), self.num_dof),<br/>        device=self.device) - 0.5)<br/>    velocities = 0.5 * (torch.rand((len(env_ids), self.num_dof),<br/>        device=self.device) - 0.5)<br/><br/>    self.dof_pos[env_ids, :] = positions[:]<br/>    self.dof_vel[env_ids, :] = velocities[:]<br/><br/>    env_ids_int32 = env_ids.to(dtype=torch.int32)<br/>    self.gym.set_dof_state_tensor_indexed(self.sim,<br/>        gymtorch.unwrap_tensor(self.dof_state),<br/>        gymtorch.unwrap_tensor(env_ids_int32), len(env_ids_int32))<br/>    self.reset_buf[env_ids] = 0<br/>    self.progress_buf[env_ids] = 0<br/>``` | ```python<br/>def _get_dones(self) -> tuple[torch.Tensor, torch.Tensor]:<br/>    self.joint_pos = self.cartpole.data.joint_pos<br/>    self.joint_vel = self.cartpole.data.joint_vel<br/><br/>    time_out = self.episode_length_buf >= self.max_episode_length - 1<br/>    out_of_bounds = torch.any(torch.abs(<br/>        self.joint_pos[:, self._cart_dof_idx]) > self.cfg.max_cart_pos,<br/>        dim=1)<br/>    out_of_bounds = out_of_bounds | torch.any(<br/>        torch.abs(self.joint_pos[:, self._pole_dof_idx]) > math.pi / 2,<br/>        dim=1)<br/>    return out_of_bounds, time_out<br/><br/>def _reset_idx(self, env_ids: Sequence[int] | None):<br/>    if env_ids is None:<br/>        env_ids = self.cartpole._ALL_INDICES<br/>    super()._reset_idx(env_ids)<br/><br/>    joint_pos = self.cartpole.data.default_joint_pos[env_ids]<br/>    joint_pos[:, self._pole_dof_idx] += sample_uniform(<br/>        self.cfg.initial_pole_angle_range[0] * math.pi,<br/>        self.cfg.initial_pole_angle_range[1] * math.pi,<br/>        joint_pos[:, self._pole_dof_idx].shape,<br/>        joint_pos.device,<br/>    )<br/>    joint_vel = self.cartpole.data.default_joint_vel[env_ids]<br/><br/>    default_root_state = self.cartpole.data.default_root_state[env_ids]<br/>    default_root_state[:, :3] += self.scene.env_origins[env_ids]<br/><br/>    self.joint_pos[env_ids] = joint_pos<br/><br/>    self.cartpole.write_root_pose_to_sim(<br/>        default_root_state[:, :7], env_ids)<br/>    self.cartpole.write_root_velocity_to_sim(<br/>        default_root_state[:, 7:], env_ids)<br/>    self.cartpole.write_joint_state_to_sim(<br/>        joint_pos, joint_vel, None, env_ids)<br/>``` |

### 관찰

Isaac Lab에서 `_get_observations()` API는 이제 `policy` 키에 관측 버퍼를 값으로 갖는 딕셔너리를 반환해야 합니다.
비대칭 정책의 경우, 딕셔너리는 상태 버퍼를 보유하는 `critic` 키도 포함해야 합니다.

| IsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def compute_observations(self, env_ids=None):<br/>    if env_ids is None:<br/>        env_ids = np.arange(self.num_envs)<br/><br/>    self.gym.refresh_dof_state_tensor(self.sim)<br/><br/>    self.obs_buf[env_ids, 0] = self.dof_pos[env_ids, 0]<br/>    self.obs_buf[env_ids, 1] = self.dof_vel[env_ids, 0]<br/>    self.obs_buf[env_ids, 2] = self.dof_pos[env_ids, 1]<br/>    self.obs_buf[env_ids, 3] = self.dof_vel[env_ids, 1]<br/><br/>    return self.obs_buf<br/>``` | ```python<br/>def _get_observations(self) -> dict:<br/>    obs = torch.cat(<br/>        (<br/>            self.joint_pos[:, self._pole_dof_idx[0]],<br/>            self.joint_vel[:, self._pole_dof_idx[0]],<br/>            self.joint_pos[:, self._cart_dof_idx[0]],<br/>            self.joint_vel[:, self._cart_dof_idx[0]],<br/>        ),<br/>        dim=-1,<br/>    )<br/>    observations = {"policy": obs}<br/>    return observations<br/>``` |

### Rewards

In Isaac Lab, the reward method `_get_rewards` should return the reward buffer as a return value.
Similar to IsaacGymEnvs, computations in the reward function can also be performed using pytorch jit
by adding the `@torch.jit.script` annotation.

| IsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def compute_reward(self):<br/>    # 환경 버퍼에서 관측값을 가져옵니다<br/>    pole_angle = self.obs_buf[:, 2]<br/>    pole_vel = self.obs_buf[:, 3]<br/>    cart_vel = self.obs_buf[:, 1]<br/>    cart_pos = self.obs_buf[:, 0]<br/><br/>    self.rew_buf[:], self.reset_buf[:] = compute_cartpole_reward(<br/>        pole_angle, pole_vel, cart_vel, cart_pos,<br/>        self.reset_dist, self.reset_buf,<br/>        self.progress_buf, self.max_episode_length<br/>    )<br/><br/>@torch.jit.script<br/>def compute_cartpole_reward(pole_angle, pole_vel,<br/>                            cart_vel, cart_pos,<br/>                            reset_dist, reset_buf,<br/>                            progress_buf, max_episode_length):<br/><br/>    reward = (1.0 - pole_angle * pole_angle -<br/>        0.01 * torch.abs(cart_vel) -<br/>        0.005 * torch.abs(pole_vel))<br/><br/>    # 리셋된 에이전트에 대한 보상 조정<br/>    reward = torch.where(torch.abs(cart_pos) > reset_dist,<br/>        torch.ones_like(reward) * -2.0, reward)<br/>    reward = torch.where(torch.abs(pole_angle) > np.pi / 2,<br/>        torch.ones_like(reward) * -2.0, reward)<br/><br/>    reset = torch.where(torch.abs(cart_pos) > reset_dist,<br/>        torch.ones_like(reset_buf), reset_buf)<br/>    reset = torch.where(torch.abs(pole_angle) > np.pi / 2,<br/>        torch.ones_like(reset_buf), reset_buf)<br/>    reset = torch.where(progress_buf >= max_episode_length - 1,<br/>        torch.ones_like(reset_buf), reset)<br/>``` | ```python<br/>def _get_rewards(self) -> torch.Tensor:<br/>    total_reward = compute_rewards(<br/>        self.cfg.rew_scale_alive,<br/>        self.cfg.rew_scale_terminated,<br/>        self.cfg.rew_scale_pole_pos,<br/>        self.cfg.rew_scale_cart_vel,<br/>        self.cfg.rew_scale_pole_vel,<br/>        self.joint_pos[:, self._pole_dof_idx[0]],<br/>        self.joint_vel[:, self._pole_dof_idx[0]],<br/>        self.joint_pos[:, self._cart_dof_idx[0]],<br/>        self.joint_vel[:, self._cart_dof_idx[0]],<br/>        self.reset_terminated,<br/>    )<br/>    return total_reward<br/><br/>@torch.jit.script<br/>def compute_rewards (<br/>    rew_scale_alive: float,<br/>    rew_scale_terminated: float,<br/>    rew_scale_pole_pos: float,<br/>    rew_scale_cart_vel: float,<br/>    rew_scale_pole_vel: float,<br/>    pole_pos: torch.Tensor,<br/>    pole_vel: torch.Tensor,<br/>    cart_pos: torch.Tensor,<br/>    cart_vel: torch.Tensor,<br/>    reset_terminated: torch.Tensor,<br/>):<br/>    rew_alive = rew_scale_alive * (1.0 - reset_terminated.float())<br/>    rew_termination = rew_scale_terminated * reset_terminated.float()<br/>    rew_pole_pos = rew_scale_pole_pos * torch.sum(<br/>        torch.square(pole_pos), dim=-1)<br/>    rew_cart_vel = rew_scale_cart_vel * torch.sum(<br/>        torch.abs(cart_vel), dim=-1)<br/>    rew_pole_vel = rew_scale_pole_vel * torch.sum(<br/>        torch.abs(pole_vel), dim=-1)<br/>    total_reward = (rew_alive + rew_termination<br/>                     + rew_pole_pos + rew_cart_vel + rew_pole_vel)<br/>    return total_revenue<br/>``` |

## 훈련 실행

Isaac Lab에서 훈련을 실행하려면 다음 명령을 사용하세요:

```bash
python scripts/reinforcement_learning/rl_games/train.py --task=Isaac-Cartpole-Direct-v0 --headless
```

## 추론 실행

Isaac Lab에서 추론을 실행하려면 다음 명령을 사용하세요:

```bash
python scripts/reinforcement_learning/rl_games/play.py --task=Isaac-Cartpole-Direct-v0 --num_envs=25 --checkpoint=<path/to/checkpoint>
```

## 추가 자료

* [Isaac Gym과 Isaac Lab 간 시뮬레이션 비교](comparing_simulation_isaacgym.md)
