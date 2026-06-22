<a id="migrating-from-omniisaacgymenvs"></a>

# OmniIsaacGymEnvs에서 이전하기

[OmniIsaacGymEnvs](https://github.com/isaac-sim/OmniIsaacGymEnvs)는 Isaac Sim 플랫폼을 사용하는 강화 학습 프레임워크였습니다.
OmniIsaacGymEnvs의 기능은 Isaac Lab 프레임워크에 통합되었습니다.
Isaac Lab로의 마이그레이션 프로세스를 지원하기 위해 OmniIsaacGymEnvs를 Isaac Sim 버전 4.0.0으로 업데이트했습니다.
앞으로 OmniIsaacGymEnvs는 사용되지 않게 되며, 향후 개발은 Isaac Lab에서 계속됩니다.

#### 주의 사항
다음 변경 사항은 Isaac Lab 1.0 릴리즈를 기준으로 합니다. 향후 릴리즈의 변경 사항은 [릴리스 노트](https://github.com/isaac-sim/IsaacLab/releases)를 참조하세요.

## 태스크 구성 설정

OmniIsaacGymEnvs에서는 태스크 구성 파일이 `.yaml` 형식으로 정의되었습니다. Isaac Lab에서는 구성 사양이 이제 전문 파이썬 클래스 [`configclass`](../api/lab/isaaclab.utils.md#module-isaaclab.utils.configclass)를 사용합니다.
[`configclass`](../api/lab/isaaclab.utils.md#module-isaaclab.utils.configclass) 모듈은 Python의 `dataclasses` 모듈을 기반으로 한 래퍼를 제공합니다.
각 환경은 [`DirectRLEnvCfg`](../api/lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnvCfg) 클래스를 상속하고 `@configclass`로 주석이 달린 자체 구성 클래스를 지정해야 하며, 이 클래스에는 시뮬레이션 매개변수, 환경 장면 매개변수, 로봇 매개변수 및 태스크별 매개변수가 포함될 수 있습니다.

다음은 태스크 구성 클래스의 예시 골격입니다:

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
   # 태스크별 매개변수
   ...
```

### 시뮬레이션 구성

시뮬레이션 관련 매개변수는 [`SimulationCfg`](../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg) 클래스의 일부로 정의되며, 이 클래스는 `dt`, `device`, `gravity`와 같은 시뮬레이션 매개변수를 저장하는 [`configclass`](../api/lab/isaaclab.utils.md#module-isaaclab.utils.configclass) 모듈입니다. 각 태스크 구성에는 [`SimulationCfg`](../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg) 유형을 저장하는 `sim`이라는 변수가 정의되어야 합니다.

아티큘레이션 및 강체와 관련된 시뮬레이션 매개변수(예: `num_position_iterations`, `num_velocity_iterations`, `contact_offset`, `rest_offset`, `bounce_threshold_velocity`, `max_depenetration_velocity`)는 각 개별 아티큘레이션 및 강체의 구성 클래스에서 액터당 기준으로 지정할 수 있습니다.

GPU에서 시뮬레이션을 실행할 때, PhysX 버퍼는 접촉, 충돌 및 집계 쌍과 같은 정보를 계산하고 저장하기 위해 사전 할당이 필요합니다.
이러한 버퍼는 환경의 복잡성, 예상되는 접촉 및 충돌 수, 환경의 액터 수에 따라 조정해야 할 수 있습니다.
[`PhysxCfg`](../api/lab/isaaclab.sim.md#isaaclab.sim.PhysxCfg) 클래스는 GPU 버퍼 차원 설정에 대한 액세스를 제공합니다.

| <br/><br/><br/><br/># OmniIsaacGymEnvs<br/><br/><br/>sim:<br/><br/><br/><br/><br/>dt: 0.0083 # 1/120 s<br/><br/><br/>use_gpu_pipeline: ${eq:${…pipeline}，”gpu”}<br/><br/><br/>use_fabric: True<br/><br/><br/>enable_scene_query_support: False<br/><br/><br/>disable_contact_processing: False<br/><br/><br/>gravity: [0.0, 0.0, -9.81]<br/><br/><br/><br/><br/>default_physics_material:<br/><br/><br/>static_friction: 1.0<br/><br/><br/>dynamic_friction: 1.0<br/><br/><br/>restitution: 0.0<br/><br/><br/><br/><br/>physx:<br/><br/><br/>worker_thread_count: ${….num_threads}<br/><br/><br/>solver_type: ${….solver_type}<br/><br/><br/>use_gpu: ${contains:”cuda”,${….sim_device}}<br/><br/><br/>solver_position_iteration_count: 4<br/><br/><br/>solver_velocity_iteration_count: 0<br/><br/><br/>contact_offset: 0.02<br/><br/><br/>rest_offset: 0.001<br/><br/><br/>bounce_threshold_velocity: 0.2<br/><br/><br/>friction_offset_threshold: 0.04<br/><br/><br/>friction_correlation_distance: 0.025<br/><br/><br/>enable_sleeping: True<br/><br/><br/>enable_stabilization: True<br/><br/><br/>max_depenetration_velocity: 100.0<br/><br/><br/><br/><br/>gpu_max_rigid_contact_count: 524288<br/><br/><br/>gpu_max_rigid_patch_count: 81920<br/><br/><br/>gpu_found_lost_pairs_capacity: 1024<br/><br/><br/>gpu_found_lost_aggregate_pairs_capacity: 262144<br/><br/><br/>gpu_total_aggregate_pairs_capacity: 1024<br/><br/><br/>gpu_heap_capacity: 67108864<br/><br/><br/>gpu_temp_buffer_capacity: 16777216<br/><br/><br/>gpu_max_num_partitions: 8<br/><br/><br/>gpu_max_soft_body_contacts: 1048576<br/><br/><br/>gpu_max_particle_contacts: 1048576<br/><br/><br/><br/><br/><br/>   | <br/><br/><br/><br/># IsaacLab<br/><br/><br/>sim: SimulationCfg = SimulationCfg(<br/><br/><br/>device = “cuda:0” # can be “cpu”, “cuda”, “cuda:<device_id>”<br/><br/><br/>dt=1 / 120,<br/><br/><br/># use_gpu_pipeline is deduced from the device<br/><br/><br/>use_fabric=True,<br/><br/><br/>enable_scene_query_support=False,<br/><br/><br/><br/><br/>gravity=(0.0, 0.0, -9.81),<br/><br/><br/><br/><br/>physics_material=RigidBodyMaterialCfg(<br/><br/><br/>static_friction=1.0,<br/><br/><br/>dynamic_friction=1.0,<br/><br/><br/>restitution=0.0<br/><br/><br/>)<br/><br/><br/>physx: PhysxCfg = PhysxCfg(<br/><br/><br/># worker_thread_count is no longer needed<br/><br/><br/>solver_type=1,<br/><br/><br/># use_gpu is deduced from the device<br/><br/><br/>max_position_iteration_count=4,<br/><br/><br/>max_velocity_iteration_count=0,<br/><br/><br/># moved to actor config<br/><br/><br/># moved to actor config<br/><br/><br/>bounce_threshold_velocity=0.2,<br/><br/><br/>friction_offset_threshold=0.04,<br/><br/><br/>friction_correlation_distance=0.025,<br/><br/><br/># enable_sleeping is no longer needed<br/><br/><br/>enable_stabilization=True,<br/><br/><br/># moved to RigidBodyPropertiesCfg<br/><br/><br/><br/><br/>gpu_max_rigid_contact_count=2\*\*23,<br/><br/><br/>gpu_max_rigid_patch_count=5 \* 2\*\*15,<br/><br/><br/>gpu_found_lost_pairs_capacity=2\*\*21,<br/><br/><br/>gpu_found_lost_aggregate_pairs_capacity=2\*\*25,<br/><br/><br/>gpu_total_aggregate_pairs_capacity=2\*\*21,<br/><br/><br/>gpu_heap_capacity=2\*\*26,<br/><br/><br/>gpu_temp_buffer_capacity=2\*\*24,<br/><br/><br/>gpu_max_num_partitions=8,<br/><br/><br/>gpu_max_soft_body_contacts=2\*\*20,<br/><br/><br/>gpu_max_particle_contacts=2\*\*20,<br/><br/><br/>)<br/><br/><br/>)<br/><br/>   |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

`add_ground_plane` 및 `add_distant_light`와 같은 매개변수는 이제 장면 생성 시 태스크 로직의 일부가 되었습니다.
`enable_cameras`는 이제 훈련 스크립트에 직접 전달할 수 있는 명령줄 인수 `--enable_cameras`가 되었습니다.

### 장면 구성

[`InteractiveSceneCfg`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스는 장면과 관련된 매개변수(환경 수, 환경 간 간격 등)를 지정하는 데 사용할 수 있습니다.
각 태스크 구성에는 `scene`이라는 변수가 [`InteractiveSceneCfg`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 타입을 담도록 정의되어야 합니다.

| ```yaml<br/># OmniIsaacGymEnvs<br/>env:<br/>  numEnvs: ${resolve_default:512,${...num_envs}}<br/>  envSpacing: 4.0<br/>```   | ```python<br/># IsaacLab<br/>scene: InteractiveSceneCfg = InteractiveSceneCfg(<br/>   num_envs=512,<br/>   env_spacing=4.0)<br/>```   |
|------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|

### 태스크 구성

각 환경은 태스크별 매개변수(관찰 및 액션 버퍼 차원 등)를 저장하는 자체 구성 클래스를 지정해야 합니다. 보상 항 스케일링 매개변수도 구성 클래스에 지정할 수 있습니다.

Isaac Lab에서는 `controlFrequencyInv` 매개변수가 `decimation`으로 이름이 변경되었습니다. 이 매개변수는 구성 클래스의 매개변수로 지정해야 합니다.

또한, 최대 에피소드 길이 매개변수(현재 `episode_length_s`)는 단계 대신 초 단위이며, OmniIsaacGymEnvs와 다릅니다. 단계 수를 초로 변환하려면 다음 방정식을 사용하세요:
`episode_length_s = dt * decimation * num_steps`.

다음 매개변수는 각 환경 구성에 반드시 설정해야 합니다:

```python
decimation = 2
episode_length_s = 5.0
action_space = 1
observation_space = 4
state_space = 0
```

## RL 구성 설정

Isaac Lab에서 rl_games 라이브러리의 RL 구성 파일은 `.yaml` 파일로 계속 정의할 수 있습니다.
구성 파일의 대부분은 OmniIsaacGymEnvs에서 그대로 복사해 사용할 수 있습니다.
참고: Isaac Lab에서는 hydra를 사용하여 상대 경로를 해결하지 않습니다. `${....device}`와 같은 상대 경로는 해당 매개변수의 실제 값으로 대체해야 합니다.

또한, 관측 및 행동 클립 범위는 이제 RL 구성 파일로 이동되었습니다.
IsaacGymEnvs 태스크 구성 파일에 정의된 `clipObservations` 및 `clipActions` 매개변수는 Isaac Lab의 RL 구성 파일로 이동해야 합니다.

| IsaacGymEnvs 태스크 구성                                                                           | Isaac Lab RL 구성                                                                                            |
|----------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| ```yaml<br/># OmniIsaacGymEnvs<br/>env:<br/>  clipObservations: 5.0<br/>  clipActions: 1.0<br/>``` | ```yaml<br/># IsaacLab<br/>params:<br/>  env:<br/>    clip_observations: 5.0<br/>    clip_actions: 1.0<br/>``` |

## 환경 생성

OmniIsaacGymEnvs에서는 환경 생성이 일반적으로 `set_up_scene()` API에서 발생하며,
초기 환경 생성, 환경 복제, 충돌 필터링, 지면 평면 및 조명 추가, 액터의 `View` 클래스 생성이 포함되었습니다.

Isaac Lab에서는 `_setup_scene()` API에서 유사한 기능을 수행합니다.
주된 차이점은 기본 클래스의 `_setup_scene()`이 더 이상 환경 복제, 지면 평면 및 조명 추가 작업을 수행하지 않는다는 점입니다.
대신, 이러한 작업은 개별 태스크의 `_setup_scene` 구현에 있어야 하며, 이는 장면 설정 프로세스에 더 많은 유연성을 제공합니다.

또한, `Articulation` 또는 `RigidObject` 객체를 정의하면 해당 객체의 `spawn` 매개변수를 파싱하여 액터가 장면에 자동으로 추가되고, 액터에 대한 `View` 클래스도 자동으로 생성됩니다.
이로 인해 액터마다 별도의 `ArticulationView` 또는 `RigidPrimView` 객체를 정의할 필요가 없습니다.

| OmniIsaacGymEnvs                                                                                                                                                                                                                                                                                                                                     | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def set_up_scene(self, scene) -> None:<br/>  self.get_cartpole()<br/>  super().set_up_scene(scene)<br/><br/>  self._cartpoles = ArticulationView(<br/>               prim_paths_expr="/World/envs/.*/Cartpole",<br/>               name="cartpole_view", reset_xform_properties=False<br/>  )<br/>  scene.add(self._cartpoles)<br/>``` | ```python<br/>def _setup_scene(self):<br/>  self.cartpole = Articulation(self.cfg.robot_cfg)<br/>  # add ground plane<br/>  spawn_ground_plane(prim_path="/World/ground", cfg=GroundPlaneCfg()<br/>  # clone, filter, and replicate<br/>  self.scene.clone_environments(copy_from_source=False)<br/>  self.scene.filter_collisions(global_prim_paths=[])<br/>  # add articulation to scene<br/>  self.scene.articulations["cartpole"] = self.cartpole<br/>  # add lights<br/>  light_cfg = sim_utils.DomeLightCfg(intensity=2000.0)<br/>  light_cfg.func("/World/Light", light_cfg)<br/>``` |

### 지형 평면

위 예시 외에도 [`TerrainImporterCfg`](../api/lab/isaaclab.terrains.md#isaaclab.terrains.TerrainImporterCfg) 클래스를 사용하여 더 정교한 지형 평면을 정의할 수 있습니다.

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

지형은 `TerrainImporterCfg` 객체를 참조하여 `_setup_scene(self)` 함수에서 scena에 추가할 수 있습니다:

<!-- code-block::python

def _setup_scene(self):
   ...
   self.cfg.terrain.num_envs = self.scene.cfg.num_envs
   self.cfg.terrain.env_spacing = self.scene.cfg.env_spacing
   self._terrain = self.cfg.terrain.class_type(self.cfg.terrain) -->

### 액터

Isaac Lab에서 각 Articulation 및 Rigid Body 액터는 자체 설정 클래스를 가질 수 있습니다.  
[`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg) 클래스는 파일 경로, 시뮬레이션 파라미터, 액추에이터 속성 및 초기 상태와 같은 액추에이터 액터의 매개변수를 정의하는 데 사용됩니다.

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
            effort_limit_sim=400.0,
            velocity_limit_sim=100.0,
            stiffness=0.0,
            damping=10.0,
        ),
        "pole_actuator": ImplicitActuatorCfg(
            joint_names_expr=["cart_to_pole"], effort_limit=400.0, velocity_limit=100.0, stiffness=0.0, damping=0.0
        ),
    },
) -->

[`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg)의 `spawn` 속성은 로봇 파일의 경로를 지정하여 장면에 로봇을 추가하는 데 사용될 수 있습니다.  
또한 [`RigidBodyPropertiesCfg`](../api/lab/isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) 클래스는 어티큘레이션의 강체에 대한 시뮬레이션 속성을 지정하는 데 사용될 수 있습니다.  
비슷하게 [`ArticulationRootPropertiesCfg`](../api/lab/isaaclab.sim.schemas.md#isaaclab.sim.schemas.ArticulationRootPropertiesCfg) 클래스는 어티큘레이션 루트에 대한 시뮬레이션 속성을 지정하는 데 사용될 수 있습니다.  
관절 속성은 이제 [`ImplicitActuatorCfg`](../api/lab/isaaclab.actuators.md#isaaclab.actuators.ImplicitActuatorCfg)를 사용하여 `actuators` 사전의 일부로 지정됩니다.  
동일한 속성을 가진 관절은 정규 표현식을 사용하여 그룹화하거나 이름 또는 표현식의 리스트로 제공할 수 있습니다.

액터는 단순히 `self.cartpole = Articulation(self.cfg.robot_cfg)`를 호출하여 장면에 추가할 수 있습니다. 여기서 `self.cfg.robot_cfg`는 [`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg) 객체입니다.  
초기화된 후에는 [`InteractiveScene`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveScene)에 `self.scene.articulations["cartpole"] = self.cartpole`를 호출하여 추가해야 합니다.  
이를 통해 [`InteractiveScene`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveScene)은 시뮬레이션에 값을 쓰고 리셋하기 위해 장면의 액터를 순회할 수 있습니다.

## 시뮬레이션 상태 접근

Isaac Lab에서 물리 상태에 접근하는 API는 [`Articulation`](../api/lab/isaaclab.assets.md#isaaclab.assets.Articulation) 또는 [`RigidObject`](../api/lab/isaaclab.assets.md#isaaclab.assets.RigidObject) 객체의 생성을 필요로 합니다.  
여러 객체는 위에서 설명한 대로 해당 [`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg) 또는 [`RigidObjectCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.RigidObjectCfg) 구성을 정의하여  
장면의 서로 다른 어티큘레이션 또는 강체에 대해 초기화할 수 있습니다.  
이것은 이전에 사용된 `ArticulationView` 및 `omni.isaac.core.prims.RigidPrimView` 클래스를 대체합니다.

그러나 클래스 간의 기능은 유사합니다:

| OmniIsaacGymEnvs                                                                                                                                 | Isaac Lab                                                                                                         |
|--------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| ```python<br/>dof_pos = self._cartpoles.get_joint_positions(clone=False)<br/>dof_vel = self._cartpoles.get_joint_velocities(clone=False)<br/>``` | ```python<br/>self.joint_pos = self._robot.data.joint_pos<br/>self.joint_vel = self._robot.data.joint_vel<br/>``` |

Isaac Lab에서 [`Articulation`](../api/lab/isaaclab.assets.md#isaaclab.assets.Articulation) 및 [`RigidObject`](../api/lab/isaaclab.assets.md#isaaclab.assets.RigidObject) 클래스는 모두 `data` 클래스를 갖습니다.  
데이터 클래스([`ArticulationData`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationData) 및 [`RigidObjectData`](../api/lab/isaaclab.assets.md#isaaclab.assets.RigidObjectData))는  
어티큘레이션 및 강체의 상태를 저장하는 버퍼를 포함하고, 액터로부터 상태를 검색하는 더 효율적인 방법을 제공합니다.

일부 API 이름 변경 외에, 액터의 상태 설정도 OmniIsaacGymEnvs와 Isaac Lab 간에 유사하게 수행할 수 있습니다.

| OmniIsaacGymEnvs                                                                                                                                                                                   | Isaac Lab                                                                                                                                 |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>indices = env_ids.to(dtype=torch.int32)<br/>self._cartpoles.set_joint_positions(dof_pos, indices=indices)<br/>self._cartpoles.set_joint_velocities(dof_vel, indices=indices)<br/>``` | ```python<br/>self._robot.write_joint_state_to_sim(joint_pos, joint_vel,<br/>                                 joint_ids, env_ids)<br/>``` |

Isaac Lab에서는 `root_pose` 및 `root_velocity`가 단일 버퍼로 결합되어 더 이상 `root_position`, `root_orientation`, `root_linear_velocity` 및 `root_angular_velocity` 간에 분리되지 않습니다.

<!-- code-block::python

self.cartpole.write_root_pose_to_sim(default_root_state[:, :7], env_ids)
self.cartpole.write_root_velocity_to_sim(default_root_state[:, 7:], env_ids) -->

## 새로운 환경 생성

Isaac Lab의 각 환경은 다음과 같은 구조를 따르는 자체 디렉터리에 있어야 합니다:

```none
my_environment/
    - agents/
        - __init__.py
        - rl_games_ppo_cfg.py
    - __init__.py
    my_env.py
```

* `my_environment`는 작업의 루트 디렉터리입니다.
* `my_environment/agents`는 작업에 대한 모든 RL 구성 파일을 포함하는 디렉터리입니다. Isaac Lab은 여러 RL 라이브러리를 지원하며, 각각 고유한 개별 구성 파일을 가질 수 있습니다.
* `my_environment/__init__.py`는 Gymnasium 인터페이스에 환경을 등록하는 메인 파일입니다.  
  이를 통해 훈련 및 추론 스크립트가 이름별로 작업을 찾을 수 있습니다.  
  이 파일의 내용은 다음과 같아야 합니다:
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
* `my_environment/my_env.py`는 태스크 로직과 환경의 태스크 설정 클래스를 구현하는 주요 Python 스크립트입니다.

## 태스크 로직

OmniIsaacGymEnvs의 `post_reset` API는 더 이상 Isaac Lab에서 필요하지 않습니다. 이전에 `post_reset`에서 수행되었던 모든 작업은 베이스 클래스의 `__init__`을 실행한 후 `__init__` 메서드에서 수행할 수 있습니다. 이 시점에서 시뮬레이션은 이미 시작되었습니다.

OmniIsaacGymEnvs에서는 GPU API의 제한으로 인해 현재 스텝의 상태를 기반으로 리셋을 수행할 수 없었습니다. 대신, 다음 시간 스텝의 시작 부분에서 리셋을 수행해야 했습니다. 이 제한은 Isaac Lab에서 제거되었으며, 따라서 태스크는 동작 적용, 시뮬레이션 스텝, 상태 수집, 종료 조건 계산, 보상 계산, 리셋 수행, 마지막으로 관측 계산의 올바른 워크플로우를 따릅니다. 이 워크플로우는 프레임워크에서 자동으로 처리되므로 태스크에서 `post_physics_step` API가 필요하지 않습니다. 그러나 개별 태스크는 `step()` API를 재정의하여 워크플로우를 제어할 수 있습니다.

Isaac Lab에서는 정책에서 행동을 처리하는 `pre_physics_step` API와 시뮬레이션에 행동을 설정하는 `apply_action` API를 분리하여 제공합니다. 이를 통해 `decimation`이 사용될 때 행동을 시뮬레이션에 작성해야 하는 시점을 보다 유연하게 제어할 수 있습니다.
`pre_physics_step` 메서드는 시뮬레이션 스텝 전에 스텝당 한 번 호출됩니다.
`apply_actions` 메서드는 각 RL 스텝마다 `decimation` 횟수만큼 호출되며, 각 시뮬레이션 스텝 호출 전에 한 번씩 실행됩니다.

호출 순서는 다음과 같습니다:

| OmniIsaacGymEnvs                                                                                                                                                                            | Isaac Lab                                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```none<br/>pre_physics_step<br/>  |-- reset_idx()<br/>  |-- apply_action<br/><br/>post_physics_step<br/>  |-- get_observations()<br/>  |-- calculate_metrics()<br/>  |-- is_done()<br/>``` | ```none<br/>pre_physics_step<br/>  |-- _pre_physics_step(action)<br/>  |-- _apply_action()<br/><br/>post_physics_step<br/>  |-- _get_dones()<br/>  |-- _get_rewards()<br/>  |-- _reset_idx()<br/>  |-- _get_observations()<br/>``` |

이 접근 방식을 사용하면 현재 스텝의 행동을 기반으로 리셋이 수행되며, 리셋 후 올바른 상태에서 관측이 계산됩니다.

또한 다음 API 이름 변경도 수행했습니다:

* `set_up_scene(self, scene)` –> `_setup_scene(self)`
* `post_reset(self)` –> `__init__(...)`
* `pre_physics_step(self, actions)` –> `_pre_physics_step(self, actions)` 및 `_apply_action(self)`
* `reset_idx(self, env_ids)` –> `_reset_idx(self, env_ids)`
* `get_observations(self)` –> `_get_observations(self)` - `_get_observations()`는 이제 딕셔너리 `{"policy": obs}`를 반환해야 합니다
* `calculate_metrics(self)` –> `_get_rewards(self)` - `_get_rewards()`는 이제 보상 버퍼를 반환해야 합니다
* `is_done(self)` –> `_get_dones(self)` - `_get_dones()`는 이제 `reset` 및 `time_out` 버퍼 두 개를 반환해야 합니다

## 전체 워크플로우 이해하기

카트폴 환경은 여기에서 완성된 형태로 제시되어, OmniIsaacGymEnvs 구현과 Isaac Lab 구현 간의 차이점을 완전히 보여줍니다.

### 태스크 구성

Isaac Lab의 태스크 구성은 메인 태스크 구성 클래스와 개별 액터를 위한 구성 객체로 나뉠 수 있습니다.

| OmniIsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```yaml<br/># 객체를 생성하는 데 사용됩니다.<br/><br/>name: Cartpole<br/><br/>physics_engine: ${..physics_engine}<br/><br/># 제공된 경우, gym의 디바이스 설정을 덮어씁니다.<br/>env:<br/><br/>  numEnvs: ${resolve_default:512,${...num_envs}}<br/>  envSpacing: 4.0<br/>  resetDist: 3.0<br/>  maxEffort: 400.0<br/><br/>  clipObservations: 5.0<br/>  clipActions: 1.0<br/>  controlFrequencyInv: 2 # 60 Hz<br/><br/>sim:<br/><br/>  dt: 0.0083 # 1/120 s<br/>  use_gpu_pipeline: ${eq:${...pipeline},"gpu"}<br/>  gravity: [0.0, 0.0, -9.81]<br/>  add_ground_plane: True<br/>  add_distant_light: False<br/>  use_fabric: True<br/>  enable_scene_query_support: False<br/>  disable_contact_processing: False<br/><br/>  enable_cameras: False<br/><br/>  default_physics_material:<br/>    static_friction: 1.0<br/>    dynamic_friction: 1.0<br/>    restitution: 0.0<br/><br/>  physx:<br/>    worker_thread_count: ${....num_threads}<br/>    solver_type: ${....solver_type}<br/>    use_gpu: ${eq:${....sim_device},"gpu"} # False로 설정하면...<br/>    solver_position_iteration_count: 4<br/>    solver_velocity_iteration_count: 0<br/>    contact_offset: 0.02<br/>    rest_offset: 0.001<br/>    bounce_threshold_velocity: 0.2<br/>    friction_offset_threshold: 0.04<br/>    friction_correlation_distance: 0.025<br/>    enable_sleeping: True<br/>    enable_stabilization: True<br/>    max_depenetration_velocity: 100.0<br/><br/>    # GPU 버퍼<br/>    gpu_max_rigid_contact_count: 524288<br/>    gpu_max_rigid_patch_count: 81920<br/>    gpu_found_lost_pairs_capacity: 1024<br/>    gpu_found_lost_aggregate_pairs_capacity: 262144<br/>    gpu_total_aggregate_pairs_capacity: 1024<br/>    gpu_max_soft_body_contacts: 1048576<br/>    gpu_max_particle_contacts: 1048576<br/>    gpu_heap_capacity: 67108864<br/>    gpu_temp_buffer_capacity: 16777216<br/>    gpu_max_num_partitions: 8<br/><br/>    Cartpole:<br/>      override_usd_defaults: False<br/>      enable_self_collisions: False<br/>      enable_gyroscopic_forces: True<br/>      solver_position_iteration_count: 4<br/>      solver_velocity_iteration_count: 0<br/>      sleep_threshold: 0.005<br/>      stabilization_threshold: 0.001<br/>      density: -1<br/>      max_depenetration_velocity: 100.0<br/>      contact_offset: 0.02<br/>      rest_offset: 0.001<br/>``` | ```python<br/>@configclass<br/>class CartpoleEnvCfg(DirectRLEnvCfg):<br/><br/>    # 시뮬레이션<br/>    sim: SimulationCfg = SimulationCfg(dt=1 / 120)<br/>    # 로봇<br/>    robot_cfg: ArticulationCfg = CARTPOLE_CFG.replace(<br/>        prim_path="/World/envs/env_.*/Robot")<br/>    cart_dof_name = "slider_to_cart"<br/>    pole_dof_name = "cart_to_pole"<br/>    # 씬<br/>    scene: InteractiveSceneCfg = InteractiveSceneCfg(<br/>      num_envs=4096, env_spacing=4.0, replicate_physics=True)<br/>    # 환경<br/>    decimation = 2<br/>    episode_length_s = 5.0<br/>    action_scale = 100.0  # [N]<br/>    action_space = 1<br/>    observation_space = 4<br/>    state_space = 0<br/>    # 리셋<br/>    max_cart_pos = 3.0<br/>    initial_pole_angle_range = [-0.25, 0.25]<br/>    # 보상 스케일<br/>    rew_scale_alive = 1.0<br/>    rew_scale_terminated = -2.0<br/>    rew_scale_pole_pos = -1.0<br/>    rew_scale_cart_vel = -0.01<br/>    rew_scale_pole_vel = -0.005<br/><br/><br/>CARTPOLE_CFG = ArticulationCfg(<br/>  spawn=sim_utils.UsdFileCfg(<br/>    usd_path=f"{ISAACLAB_NUCLEUS_DIR}/.../cartpole.usd",<br/>    rigid_props=sim_utils.RigidBodyPropertiesCfg(<br/>      rigid_body_enabled=True,<br/>      max_linear_velocity=1000.0,<br/>      max_angular_velocity=1000.0,<br/>      max_depenetration_velocity=100.0,<br/>      enable_gyroscopic_forces=True,<br/>    ),<br/>    articulation_props=sim_utils.ArticulationRootPropertiesCfg(<br/>      enabled_self_collisions=False,<br/>      solver_position_iteration_count=4,<br/>      solver_velocity_iteration_count=0,<br/>      sleep_threshold=0.005,<br/>      stabilization_threshold=0.001,<br/>    ),<br/>  ),<br/>  init_state=ArticulationCfg.InitialStateCfg(<br/>    pos=(0.0, 0.0, 2.0),<br/>    joint_pos={"slider_to_cart": 0.0, "cart_to_pole": 0.0}<br/>  ),<br/>  actuators={<br/>    "cart_actuator": ImplicitActuatorCfg(<br/>       joint_names_expr=["slider_to_cart"],<br/>       effort_limit=400.0,<br/>       velocity_limit=100.0,<br/>       stiffness=0.0,<br/>       damping=10.0,<br/>    ),<br/>    "pole_actuator": ImplicitActuatorCfg(<br/>       joint_names_expr=["cart_to_pole"], effort_limit=400.0,<br/>       velocity_limit=100.0, stiffness=0.0, damping=0.0<br/>    ),<br/>  },<br/>``` |

### 작업 설정

OmniIsaacGymEnvs의 `post_reset` API는 이제 Isaac Lab에서는 더 이상 필요하지 않습니다.
이전에 `post_reset`에서 수행했던 모든 작업은 베이스 클래스의 `__init__`를 실행한 후 `__init__` 메서드 내에서 수행할 수 있습니다.
이 시점에서 시뮬레이션은 이미 시작되었습니다.

| OmniIsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>class CartpoleTask(RLTask):<br/><br/>    def __init__(self, name, sim_config, env, offset=None) -> None:<br/><br/>        self.update_config(sim_config)<br/>        self._max_episode_length = 500<br/><br/>        self._num_observations = 4<br/>        self._num_actions = 1<br/><br/>        RLTask.__init__(self, name, env)<br/><br/>        def update_config(self, sim_config):<br/>            self._sim_config = sim_config<br/>            self._cfg = sim_config.config<br/>            self._task_cfg = sim_config.<br/>            task_config<br/><br/>            self._num_envs = self._task_cfg["env"]["numEnvs"]<br/>            self._env_spacing = self._task_cfg["env"]["envSpacing"]<br/>            self._cartpole_positions = torch.tensor([0.0, 0.0, 2.0])<br/><br/>            self._reset_dist = self._task_cfg["env"]["resetDist"]<br/>            self._max_push_effort = self._task_cfg["env"]["maxEffort"]<br/><br/><br/>        def post_reset(self):<br/>            self._cart_dof_idx = self._cartpoles.get_dof_index(<br/>                "cartJoint")<br/>            self._pole_dof_idx = self._cartpoles.get_dof_index(<br/>                "poleJoint")<br/>            # randomize all envs<br/>            indices = torch.arange(<br/>                self._cartpoles.count, dtype=torch.int64,<br/>                device=self._device)<br/>            self.reset_idx(indices)<br/>``` | ```python<br/>class CartpoleEnv(DirectRLEnv):<br/>    cfg: CartpoleEnvCfg<br/>    def __init__(self, cfg: CartpoleEnvCfg,<br/>             render_mode: str | None = None, **kwargs):<br/>        super().__init__(cfg, render_mode, **kwargs)<br/><br/><br/>        self._cart_dof_idx, _ = self.cartpole.find_joints(<br/>              self.cfg.cart_dof_name)<br/>        self._pole_dof_idx, _ = self.cartpole.find_joints(<br/>               self.cfg.pole_dof_name)<br/>        self.action_scale=self.cfg.action_scale<br/><br/>        self.joint_pos = self.cartpole.data.joint_pos<br/>        self.joint_vel = self.cartpole.data.joint_vel<br/>``` |

### Scene Setup

`set_up_scene` 메서드인 OmniIsaacGymEnvs은 작업 클래스인 Isaac Lab의 `_setup_scene` API로 대체되었습니다. 또한, 장면 클로닝과 충돌 필터링이 필요 시 호출할 수 있는 작업 클래스의 API로 제공되었습니다. 비슷하게, 지면 평면과 조명 추가도 작업 클래스에서 처리해야 합니다. 장면의 액터 추가가 `self.scene.articulations["cartpole"] = self.cartpole` 로 대체되었습니다.

| OmniIsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def set_up_scene(self, scene) -> None:<br/><br/>    self.get_cartpole()<br/>    super().set_up_scene(scene)<br/>    self._cartpoles = ArticulationView(<br/>        prim_paths_expr="/World/envs/.*/Cartpole",<br/>        name="cartpole_view",<br/>        reset_xform_properties=False<br/>    )<br/>    scene.add(self._cartpoles)<br/>    return<br/><br/>def get_cartpole(self):<br/>    cartpole = Cartpole(<br/>        prim_path=self.default_zero_env_path+"/Cartpole",<br/>        name="Cartpole",<br/>        translation=self._cartpole_positions<br/>    )<br/>    # 적용하는 구성 YAML 파일에서 파생된 아티큘레이션 설정<br/>    self._sim_config.apply_articulation_settings(<br/>        "Cartpole", get_prim_at_path(cartpole.prim_path),<br/>        self._sim_config.parse_actor_config("Cartpole")<br/>    )<br/>``` | ```python<br/>def _setup_scene(self):<br/>    self.cartpole = Articulation(self.cfg.robot_cfg)<br/>    # 지면 평면 추가<br/>    spawn_ground_plane(prim_path="/World/ground",<br/>        cfg=GroundPlaneCfg())<br/>    # 복제, 필터링, 환경 복제<br/>    self.scene.clone_environments(<br/>        copy_from_source=False)<br/>    self.scene.filter_collisions(<br/>        global_prim_paths=[])<br/>    # 아티큘레이션을 씬에 추가<br/>    self.scene.articulations["cartpole"] = self.cartpole<br/><br/>    # 조명 추가<br/>    light_cfg = sim_utils.DomeLightCfg(<br/>        intensity=2000.0, color=(0.75, 0.75, 0.75))<br/>    light_cfg.func("/World/Light", light_cfg)<br/>``` |

### 물리 사전 단계

더 이상 `pre_physics_step` API에서 리셋이 수행되지 않는다는 점에 유의하세요. 또한 `_pre_physics_step`과 `_apply_action` 메서드의 분리를 통해 액션 버퍼 처리 및 시뮬레이션에 대한 액션 설정 시 더 큰 유연성을 제공합니다.

| OmniIsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | IsaacLab                                                                                                                                                                                                                                                                                          |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def pre_physics_step(self, actions) -> None:<br/>    if not self.world.is_playing():<br/>        return<br/><br/>    reset_env_ids = self.reset_buf.nonzero(<br/>        as_tuple=False).squeeze(-1)<br/>    if len(reset_env_ids) > 0:<br/>        self.reset_idx(reset_env_ids)<br/><br/>    actions = actions.to(self._device)<br/><br/>    forces = torch.zeros((self._cartpoles.count,<br/>        self._cartpoles.num_dof),<br/>        dtype=torch.float32, device=self._device)<br/>    forces[:, self._cart_dof_idx] =<br/>        self._max_push_effort * actions[:, 0]<br/><br/>    indices = torch.arange(self._cartpoles.count,<br/>        dtype=torch.int32, device=self._device)<br/>    self._cartpoles.set_joint_efforts(<br/>        forces, indices=indices)<br/>``` | ```python<br/>def _pre_physics_step(self,<br/>        actions: torch.Tensor) -> None:<br/>    self.actions = self.action_scale * actions<br/><br/>def _apply_action(self) -> None:<br/>    self.cartpole.set_joint_effort_target(<br/>        self.actions, joint_ids=self._cart_dof_idx)<br/>``` |

### 완료 및 리셋

Isaac Lab에서 `dones`는 `_get_dones()` 메서드에서 계산되며, `resets`와 `time_out`이라는 두 변수를 반환해야 합니다. `_reset_idx()` 메서드는 시뮬레이션 스텝 후 호출되며, 이는 OmniIsaacGymEnvs에서 수행된 방식(스텝 전 호출)과 다릅니다. `progress_buf` 텐서가 Isaac Lab에서 `episode_length_buf`로 이름이 변경되었으며, 이제 프레임워크에서 자동으로 책keeping을 처리합니다. 따라서 태스크 구현은 더 이상 `episode_length_buf` 버퍼를 증가시키거나 리셋할 필요가 없습니다.

| OmniIsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def is_done(self) -> None:<br/>  resets = torch.where(<br/>    torch.abs(self.cart_pos) > self._reset_dist, 1, 0)<br/>  resets = torch.where(<br/>    torch.abs(self.pole_pos) > math.pi / 2, 1, resets)<br/>  resets = torch.where(<br/>    self.progress_buf >= self._max_episode_length, 1, resets)<br/>  self.reset_buf[:] = resets<br/><br/><br/><br/><br/><br/>def reset_idx(self, env_ids):<br/>  num_resets = len(env_ids)<br/><br/>  # 무작위 DOF 위치<br/>  dof_pos = torch.zeros((num_resets, self._cartpoles.num_dof),<br/>      device=self._device)<br/>  dof_pos[:, self._cart_dof_idx] = 1.0 * (<br/>      1.0 - 2.0 * torch.rand(num_resets, device=self._device))<br/>  dof_pos[:, self._pole_dof_idx] = 0.125 * math.pi * (<br/>      1.0 - 2.0 * torch.rand(num_resets, device=self._device))<br/><br/>  # 무작위 DOF 속도<br/>  dof_vel = torch.zeros((num_resets, self._cartpoles.num_dof),<br/>      device=self._device)<br/>  dof_vel[:, self._cart_dof_idx] = 0.5 * (<br/>      1.0 - 2.0 * torch.rand(num_resets, device=self._device))<br/>  dof_vel[:, self._pole_dof_idx] = 0.25 * math.pi * (<br/>      1.0 - 2.0 * torch.rand(num_resets, device=self._device))<br/><br/>  # 리셋 적용<br/>  indices = env_ids.to(dtype=torch.int32)<br/>  self._cartpoles.set_joint_positions(dof_pos, indices=indices)<br/>  self._cartpoles.set_joint_velocities(dof_vel, indices=indices)<br/><br/>  # 기록 관리<br/>  self.reset_buf[env_ids] = 0<br/>  self.progress_buf[env_ids] = 0<br/>``` | ```python<br/>def _get_dones(self) -> tuple[torch.Tensor, torch.Tensor]:<br/>    self.joint_pos = self.cartpole.data.joint_pos<br/>    self.joint_vel = self.cartpole.data.joint_vel<br/><br/>    time_out = self.episode_length_buf >= self.max_episode_length - 1<br/>    out_of_bounds = torch.any(torch.abs(<br/>        self.joint_pos[:, self._cart_dof_idx]) > self.cfg.max_cart_pos,<br/>        dim=1)<br/>    out_of_bounds = out_of_bounds | torch.any(<br/>        torch.abs(self.joint_pos[:, self._pole_dof_idx]) > math.pi / 2,<br/>        dim=1)<br/>    return out_of_bounds, time_out<br/><br/>def _reset_idx(self, env_ids: Sequence[int] | None):<br/>    if env_ids is None:<br/>        env_ids = self.cartpole._ALL_INDICES<br/>    super()._reset_idx(env_ids)<br/><br/>    joint_pos = self.cartpole.data.default_joint_pos[env_ids]<br/>    joint_pos[:, self._pole_dof_idx] += sample_uniform(<br/>        self.cfg.initial_pole_angle_range[0] * math.pi,<br/>        self.cfg.initial_pole_angle_range[1] * math.pi,<br/>        joint_pos[:, self._pole_dof_idx].shape,<br/>        joint_pos.device,<br/>    )<br/>    joint_vel = self.cartpole.data.default_joint_vel[env_ids]<br/><br/>    default_root_state = self.cartpole.data.default_root_state[env_ids]<br/>    default_root_state[:, :3] += self.scene.env_origins[env_ids]<br/><br/>    self.joint_pos[env_ids] = joint_pos<br/>    self.joint_vel[env_ids] = joint_vel<br/><br/>    self.cartpole.write_root_pose_to_sim(<br/>        default_root_state[:, :7], env_ids)<br/>    self.cartpole.write_root_velocity_to_sim(<br/>        default_root_state[:, 7:], env_ids)<br/>    self.cartpole.write_joint_state_to_sim(<br/>        joint_pos, joint_vel, None, env_ids)<br/>``` |

### 보상

Isaac Lab에서 보상은 `_get_rewards` API에서 구현되며 `self.rew_buf`에 직접 할당하는 대신 보상 버퍼를 반환해야 합니다. 보상 함수에서의 계산도 `@torch.jit.script` 어노테이션으로 함수를 정의하여 PyTorch JIT을 통해 수행할 수 있습니다.

| OmniIsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def calculate_metrics(self) -> None:<br/>    reward = (1.0 - self.pole_pos * self.pole_pos<br/>        - 0.01 * torch.abs(self.cart_vel) - 0.005<br/>        * torch.abs(self.pole_vel))<br/>    reward = torch.where(<br/>        torch.abs(self.cart_pos) > self._reset_dist,<br/>        torch.ones_like(reward) * -2.0, reward)<br/>    reward = torch.where(<br/>        torch.abs(self.pole_pos) > np.pi / 2,<br/>        torch.ones_like(reward) * -2.0, reward)<br/><br/>    self.rew_buf[:] = reward<br/>``` | ```python<br/>def _get_rewards(self) -> torch.Tensor:<br/>    total_reward = compute_rewards(<br/>        self.cfg.rew_scale_alive,<br/>        self.cfg.rew_scale_terminated,<br/>        self.cfg.rew_scale_pole_pos,<br/>        self.cfg.rew_scale_cart_vel,<br/>        self.cfg.rew_scale_pole_vel,<br/>        self.joint_pos[:, self._pole_dof_idx[0]],<br/>        self.joint_vel[:, self._pole_dof_idx[0]],<br/>        self.joint_pos[:, self._cart_dof_idx[0]],<br/>        self.joint_vel[:, self._cart_dof_idx[0]],<br/>        self.reset_terminated,<br/>    )<br/>    return total_reward<br/><br/>@torch.jit.script<br/>def compute_rewards(<br/>    rew_scale_alive: float,<br/>    rew_scale_terminated: float,<br/>    rew_scale_pole_pos: float,<br/>    rew_scale_cart_vel: float,<br/>    rew_scale_pole_vel: float,<br/>    pole_pos: torch.Tensor,<br/>    pole_vel: torch.Tensor,<br/>    cart_pos: torch.Tensor,<br/>    cart_vel: torch.Tensor,<br/>    reset_terminated: torch.Tensor,<br/>):<br/>    rew_alive = rew_scale_alive * (1.0 - reset_terminated.float())<br/>    rew_termination = rew_scale_terminated * reset_terminated.float()<br/>    rew_pole_pos = rew_scale_pole_pos * torch.sum(<br/>        torch.square(pole_pos), dim=-1)<br/>    rew_cart_vel = rew_scale_cart_vel * torch.sum(<br/>        torch.abs(cart_vel), dim=-1)<br/>    rew_pole_vel = rew_scale_pole_vel * torch.sum(<br/>        torch.abs(pole_vel), dim=-1)<br/>    total_reward = (rew_alive + rew_termination<br/>        + rew_pole_pos + rew_cart_vel + rew_pole_vel)<br/>    return total_reward<br/>``` |

### Observations

Isaac Lab에서 `_get_observations()` API는 `policy` 키를 가지며 값으로 관측 버퍼를 갖는 딕셔너리를 반환해야 합니다.
비대칭 액터-크리틱 상태를 사용할 때는 크리틱용 상태에 `critic` 키를 할당하고, 동일한 딕셔너리에 관측 버퍼를 함께 반환해야 합니다.

| OmniIsaacGymEnvs                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Isaac Lab                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```python<br/>def get_observations(self) -> dict:<br/>    dof_pos = self._cartpoles.get_joint_positions(clone=False)<br/>    dof_vel = self._cartpoles.get_joint_velocities(clone=False)<br/><br/>    self.cart_pos = dof_pos[:, self._cart_dof_idx]<br/>    self.cart_vel = dof_vel[:, self._cart_dof_idx]<br/>    self.pole_pos = dof_pos[:, self._pole_dof_idx]<br/>    self.pole_vel = dof_vel[:, self._pole_dof_idx]<br/>    self.obs_buf[:, 0] = self.cart_pos<br/>    self.obs_buf[:, 1] = self.cart_vel<br/>    self.obs_buf[:, 2] = self.pole_pos<br/>    self.obs_buf[:, 3] = self.pole_vel<br/><br/>    observations = {self._cartpoles.name:<br/>        {"obs_buf": self.obs_buf}}<br/>    return observations<br/>``` | ```default<br/>def _get_observations(self) -> dict:<br/>    obs = torch.cat(<br/>                 (<br/>           self.joint_pos[:, self._pole_dof_idx[0]],<br/>           self.joint_vel[:, self._pole_dof_idx[0]],<br/>           self.joint_pos[:, self._cart_dof_idx[0]],<br/>           self.joint_vel[:, self._cart_dof_idx[0]],<br/>        ),<br/>        dim=-1,<br/>    )<br/>    observations = {"policy": obs}<br/>    return observations<br/>``` |

## 도메인 랜덤화

OmniIsaacGymEnvs에서는 도메인 랜덤화가 작업의 `.yaml` 구성 파일을 통해 지정되었습니다.
Isaac Lab에서는 도메인 랜덤화 구성이 [`configclass`](../api/lab/isaaclab.utils.md#module-isaaclab.utils.configclass) 모듈을 사용하여
[`EventTermCfg`](../api/lab/isaaclab.managers.md#isaaclab.managers.EventTermCfg) 변수로 구성된 구성 클래스를 지정하는 방식으로 이루어집니다.

다음은 도메인 랜덤화를 위한 구성 클래스 예시입니다:

```python
@configclass
class EventCfg:
  robot_physics_material = EventTerm(
      func=mdp.randomize_rigid_body_material,
      mode="reset",
      params={
          "asset_cfg": SceneEntityCfg("robot", body_names=".*"),
          "static_friction_range": (0.7, 1.3),
          "dynamic_friction_range": (1.0, 1.0),
          "restitution_range": (1.0, 1.0),
          "num_buckets": 250,
      },
  )
  robot_joint_stiffness_and_damping = EventTerm(
      func=mdp.randomize_actuator_gains,
      mode="reset",
      params={
          "asset_cfg": SceneEntityCfg("robot", joint_names=".*"),
          "stiffness_distribution_params": (0.75, 1.5),
          "damping_distribution_params": (0.3, 3.0),
          "operation": "scale",
          "distribution": "log_uniform",
      },
  )
  reset_gravity = EventTerm(
      func=mdp.randomize_physics_scene_gravity,
      mode="interval",
      is_global_time=True,
      interval_range_s=(36.0, 36.0),  # time_s = num_steps * (decimation * dt)
      params={
          "gravity_distribution_params": ([0.0, 0.0, 0.0], [0.0, 0.0, 0.4]),
          "operation": "add",
          "distribution": "gaussian",
      },
  )
```

각 `EventTerm` 객체는 [`EventTermCfg`](../api/lab/isaaclab.managers.md#isaaclab.managers.EventTermCfg) 클래스이며, 랜덤화 중에 호출할 함수를 지정하는 `func` 매개변수, `startup`,
`reset` 또는 `interval`. `params` 사전에는 `func` 매개변수에 지정된 함수에 필요한 인수가 제공되어야 합니다.
`EventTerm`에 지정된 `func` 함수는 [`events`](../api/lab/isaaclab.envs.mdp.md#module-isaaclab.envs.mdp.events) 모듈에서 찾을 수 있습니다.

`"asset_cfg": SceneEntityCfg("robot", body_names=".*")` 매개변수의 일부로, 액터 이름 `"robot"`이 제공되며, 정규식으로 지정된 몸체 또는 관절 이름이 함께 제공됩니다. 이는 랜덤화를 적용할 액터 및 몸체/관절을 지정합니다.

OmniIsaacGymEnvs와의 차이점 중 하나는 `interval` 랜덤화가 이제 스텝 대신 초(second)로 지정되었다는 점입니다. `mode="interval"`인 경우, `interval_range_s` 매개변수도 함께 제공되어야 하며, 이는 랜덤화를 적용할 초 단위 범위를 지정합니다. 이 범위는 다음 랜덤화가 발생할 특정 시간을 초 단위로 결정하기 위해 랜덤화됩니다. 스텝을 초로 변환하려면 다음 방정식을 사용하세요: `time_s = num_steps * (decimation * dt)`.

OmniIsaacGymEnvs와 마찬가지로, 관절 강성 및 감쇠, 관절 한계, 강체 재질, 고정 테넌드 속성 등과 같은 아티큘레이션 속성을 랜덤화하기 위한 API가 제공됩니다. 또한 질량 및 강체 재질과 같은 강체 속성도 랜덤화할 수 있습니다. 물리 장면 중력의 랜덤화도 지원됩니다. 참고로 Isaac Lab에서는 현재 스케일 랜덤화를 지원하지 않습니다. 스케일을 랜덤화하려면 각 환경이 서로 다른 스케일로 액터를 보유하도록 장면을 구성해야 합니다.

랜덤화 항목의 `configclass` 설정이 완료되면, 이 클래스는 작업의 기본 config 클래스에 추가되어 변수 `events`에 할당되어야 합니다.

```python
@configclass
class MyTaskConfig:
  events: EventCfg = EventCfg()
```

### 액션 및 관측 노이즈

액션 및 관측 노이즈도 [`configclass`](../api/lab/isaaclab.utils.md#module-isaaclab.utils.configclass) 모듈을 사용하여 추가할 수 있습니다.
액션 및 관측 노이즈 설정은 메인 작업 설정에 `action_noise_model` 및 `observation_noise_model` 변수를 통해 추가되어야 합니다.

```python
@configclass
class MyTaskConfig:
    # 매 타임스텝마다 가우시안 노이즈 + 바이어스 추가. 바이어스는 리셋 시 가우시안으로 샘플링됨
    action_noise_model: NoiseModelWithAdditiveBiasCfg = NoiseModelWithAdditiveBiasCfg(
      noise_cfg=GaussianNoiseCfg(mean=0.0, std=0.05, operation="add"),
      bias_noise_cfg=GaussianNoiseCfg(mean=0.0, std=0.015, operation="abs"),
    )
    # 매 타임스텝마다 가우시안 노이즈 + 바이어스 추가. 바이어스는 리셋 시 가우시안으로 샘플링됨
    observation_noise_model: NoiseModelWithAdditiveBiasCfg = NoiseModelWithAdditiveBiasCfg(
      noise_cfg=GaussianNoiseCfg(mean=0.0, std=0.002, operation="add"),
      bias_noise_cfg=GaussianNoiseCfg(mean=0.0, std=0.0001, operation="abs"),
    )
```

[`NoiseModelWithAdditiveBiasCfg`](../api/lab/isaaclab.utils.md#isaaclab.utils.noise.NoiseModelWithAdditiveBiasCfg)는 스텝마다 비상관 노이즈를 샘플링하고 리셋 시 재샘플링되는 상관 노이즈 모두에 사용할 수 있습니다.
`noise_cfg` 항목은 모든 환경에서 매 스텝마다 샘플링될 가우시안 분포를 지정합니다. 이 노이즈는 해당 액션 및 관측 버퍼에 매 스텝마다 추가됩니다.
`bias_noise_cfg` 항목은 리셋 시 환경에 대해 샘플링될 상관 노이즈용 가우시안 분포를 지정합니다. 동일한 노이즈는 해당 환경의 에피소드 restante 동안 매 스텝에 적용되며, 다음 리셋 시 재샘플링됩니다.

이 설정은 다음 OmniIsaacGymEnvs 구성을 대체합니다:

```yaml
domain_randomization:
randomize: True
randomization_params:
 observations:
   on_reset:
     operation: "additive"
     distribution: "gaussian"
     distribution_parameters: [0, .0001]
   on_interval:
     frequency_interval: 1
     operation: "additive"
     distribution: "gaussian"
     distribution_parameters: [0, .002]
 actions:
   on_reset:
     operation: "additive"
     distribution: "gaussian"
     distribution_parameters: [0, 0.015]
   on_interval:
     frequency_interval: 1
     operation: "additive"
     distribution: "gaussian"
     distribution_parameters: [0., 0.05]
```

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
