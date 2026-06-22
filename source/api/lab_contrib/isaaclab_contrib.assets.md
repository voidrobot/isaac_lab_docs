# isaaclab_contrib.assets

외부에서 기여된 자산을 위한 서브 패키지입니다.

이 패키지는 다중 로터와 같은 외부 기여 로봇을 아이작 랩에서 시뮬레이션하기 위한 특수 자산 클래스를 제공합니다. 이러한 자산은 아직 아이작 랩 코어 프레임워크의 일부가 아니지만, 향후 추가될 계획입니다. 커뮤니티에서 아이작 랩의 기능을 확장하기 위해 기여했습니다.

### 클래스

| [`Multirotor`](#isaaclab_contrib.assets.Multirotor)         | 다중 로터 관절 자산 클래스입니다.                  |
|-------------------------------------------------------------|---------------------------------------------------------|
| [`MultirotorCfg`](#isaaclab_contrib.assets.MultirotorCfg)   | 다중 로터 관절에 대한 구성 파라미터입니다. |
| [`MultirotorData`](#isaaclab_contrib.assets.MultirotorData) | 다중 로터 관절에 대한 데이터 컨테이너입니다.           |

## 다중 로터 자산

### *class* isaaclab_contrib.assets.Multirotor

기반 클래스: [`Articulation`](../lab/isaaclab.assets.md#isaaclab.assets.Articulation)

다중 로터 관절 자산 클래스입니다.

이 클래스는 기본 [`Articulation`](../lab/isaaclab.assets.md#isaaclab.assets.Articulation) 클래스를 확장하여 특정 신체 위치에서 힘을 가하는 스러스터 액추에이터가 있는 다중 로터 차량(쿼드콥터, 헥사콥터, 옥토콥터 등)을 지원합니다. Kulkarni *et al.* [[KRA25](../../refs/bibliography.md#id22)]의 구현을 기반으로 합니다.

관절 기반 제어를 사용하는 표준 관절과 달리, 다중 로터는 개별 로터/프로펠러가 생성하는 추력을 통해 제어됩니다. 이 클래스는 여러 스러스터 액추에이터를 관리하고, 개별 추력에서 합성 렌치를 계산하여 다중 로터의 베이스 링크에 적용하는 특수 기능을 제공합니다.

주요 특징:
: - **스러스터 기반 제어**: [`Thruster`](isaaclab_contrib.actuators.md#isaaclab_contrib.actuators.Thruster) 액추에이터를 사용하여 관절 액추에이터 대신 현실적인 로터 동역학 시뮬레이션을 수행합니다.
  - **힘 할당**: 개별 스러스터 힘을 신체 렌치(힘과 토크)로 변환하기 위한 할당 행렬을 지원합니다.
  - **비대칭 동역학**: 스러스터 액추에이터는 실제 모터 동작을 반영하는 비대칭 상승/하강 동역학을 모델링할 수 있습니다.
  - **유연한 구성**: 정규식 기반 스러스터 이름 패턴을 통해 임의의 개수와 배열의 스러스터를 지원합니다.

사용 예시:
: ```python
  import isaaclab.sim as sim_utils
  from isaaclab_contrib.assets import MultirotorCfg
  from isaaclab_contrib.actuators import ThrusterCfg
  <br/>
  # 스러스터 액추에이터 구성 정의
  thruster_cfg = ThrusterCfg(
      thruster_names_expr=["rotor_[0-3]"],  # 로터 0-3 매치
      thrust_range=(0.0, 10.0),  # 최소 및 최대 추력 (뉴턴 단위)
      rise_time_constant=0.1,  # 추력 증가 시간 상수
      fall_time_constant=0.2,  # 추력 감소 시간 상수
  )
  <br/>
  # 다중 로터 구성 생성
  multirotor_cfg = MultirotorCfg(
      prim_path="/World/envs/env_.*/Robot",
      spawn=sim_utils.UsdFileCfg(usd_path="path/to/quadcopter.usd"),
      actuators={"thrusters": thruster_cfg},
      allocation_matrix=[  # 쿼드콥터용 6x4 행렬 (6 DOF, 4 스러스터)
          [1.0, 1.0, 1.0, 1.0],  # 총 수직 힘
          [0.0, 0.0, 0.0, 0.0],  # 측면 힘 (x)
          [0.0, 0.0, 0.0, 0.0],  # 측면 힘 (y)
          [0.0, 0.1, 0.0, -0.1],  # 롤 토크
          [-0.1, 0.0, 0.1, 0.0],  # 피치 토크
          [0.01, -0.01, 0.01, -0.01],  # 요 토크
      ],
  )
  <br/>
  # 다중 로터 인스턴스 생성
  multirotor = multirotor_cfg.class_type(multirotor_cfg)
  ```

#### 참고
할당 행렬은 개별 스러스터 힘을 베이스 링크에 적용되는 6D 렌치(3 힘 + 3 토크)에 매핑합니다. 행렬 차원은 (6, 스러스터 개수)여야 합니다.

#### 관련 참조
- [`Articulation`](../lab/isaaclab.assets.md#isaaclab.assets.Articulation): 기본 관절 클래스
- [`MultirotorCfg`](#isaaclab_contrib.assets.MultirotorCfg): 다중 로터를 위한 구성 클래스
- [`MultirotorData`](#isaaclab_contrib.assets.MultirotorData): 다중 로터 상태에 대한 데이터 컨테이너
- [`Thruster`](isaaclab_contrib.actuators.md#isaaclab_contrib.actuators.Thruster): 스러스터 액추에이터 모델

**속성:**

| [`cfg`](#isaaclab_contrib.assets.Multirotor.cfg)                                                     | 다중 로터의 구성 인스턴스입니다.                        |
|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| [`actuators`](#isaaclab_contrib.assets.Multirotor.actuators)                                         | 다중 로터의 스러스터 액추에이터 인스턴스 사전입니다.     |
| [`thruster_names`](#isaaclab_contrib.assets.Multirotor.thruster_names)                               | 다중 로터의 스러스터 순서화된 이름입니다.                     |
| [`num_thrusters`](#isaaclab_contrib.assets.Multirotor.num_thrusters)                                 | 다중 로터의 스러스터 개수입니다.                            |
| [`allocation_matrix`](#isaaclab_contrib.assets.Multirotor.allocation_matrix)                         | 제어 할당을 위한 할당 행렬입니다.                         |
| [`body_names`](#isaaclab_contrib.assets.Multirotor.body_names)                                       | 관절의 신체 순서화된 이름입니다.                          |
| [`data`](#isaaclab_contrib.assets.Multirotor.data)                                                   | 자산과 관련된 데이터입니다.                                        |
| [`device`](#isaaclab_contrib.assets.Multirotor.device)                                               | 연산에 사용되는 메모리 디바이스입니다.                                    |
| [`fixed_tendon_names`](#isaaclab_contrib.assets.Multirotor.fixed_tendon_names)                       | 관절의 고정 힘줄 순서화된 이름입니다.                   |
| [`has_debug_vis_implementation`](#isaaclab_contrib.assets.Multirotor.has_debug_vis_implementation)   | 자산에 디버그 시각화가 구현되어 있는지 여부입니다.          |
| [`instantaneous_wrench_composer`](#isaaclab_contrib.assets.Multirotor.instantaneous_wrench_composer) | 순간 렌치 컴포저.                                    |
| [`is_fixed_base`](#isaaclab_contrib.assets.Multirotor.is_fixed_base)                                 | 관절이 고정 베이스인지 또는 떠 있는 베이스 시스템인지 여부입니다. |
| [`is_initialized`](#isaaclab_contrib.assets.Multirotor.is_initialized)                               | 자산이 초기화되었는지 여부입니다.                                 |
| [`joint_names`](#isaaclab_contrib.assets.Multirotor.joint_names)                                     | 관절의 관절 순서화된 이름입니다.                          |
| [`num_bodies`](#isaaclab_contrib.assets.Multirotor.num_bodies)                                       | 관절의 신체 개수입니다.                                 |
| [`num_fixed_tendons`](#isaaclab_contrib.assets.Multirotor.num_fixed_tendons)                         | 관절의 고정 힘줄 개수입니다.                          |
| [`num_instances`](#isaaclab_contrib.assets.Multirotor.num_instances)                                 | 자산의 인스턴스 개수입니다.                                 |
| [`num_joints`](#isaaclab_contrib.assets.Multirotor.num_joints)                                       | 관절의 관절 개수입니다.                                 |
| [`num_spatial_tendons`](#isaaclab_contrib.assets.Multirotor.num_spatial_tendons)                     | 관절의 공간 힘줄 개수입니다.                        |
| [`permanent_wrench_composer`](#isaaclab_contrib.assets.Multirotor.permanent_wrench_composer)         | 영구 렌치 컴포저.                                        |
| [`root_physx_view`](#isaaclab_contrib.assets.Multirotor.root_physx_view)                             | 자산의 관절 뷰 (PhysX).                             |
| [`spatial_tendon_names`](#isaaclab_contrib.assets.Multirotor.spatial_tendon_names)                   | 관절의 공간 힘줄 순서화된 이름입니다.                   |

**메서드:**

| [`__init__`](#isaaclab_contrib.assets.Multirotor.__init__)(cfg)                                                               | 다중 로터 관절을 초기화합니다.                                                     |
|-------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`set_thrust_target`](#isaaclab_contrib.assets.Multirotor.set_thrust_target)(target[, thruster_ids, ...])                     | 스러스터의 목표 추력 값을 설정합니다.                                                     |
| [`reset`](#isaaclab_contrib.assets.Multirotor.reset)([env_ids])                                                               | 다중 로터를 기본 상태로 리셋합니다.                                                      |
| [`write_data_to_sim`](#isaaclab_contrib.assets.Multirotor.write_data_to_sim)()                                                | 추력 및 토크 명령을 시뮬레이션에 기록합니다.                                         |
| [`find_bodies`](#isaaclab_contrib.assets.Multirotor.find_bodies)(name_keys[, preserve_order])                                 | 이름 키를 기반으로 관절에서 신체를 찾습니다.                                     |
| [`find_fixed_tendons`](#isaaclab_contrib.assets.Multirotor.find_fixed_tendons)(name_keys[, ...])                              | 이름 키를 기반으로 관절에서 고정 힘줄을 찾습니다.                              |
| [`find_joints`](#isaaclab_contrib.assets.Multirotor.find_joints)(name_keys[, joint_subset, ...])                              | 이름 키를 기반으로 artikulation에서 관절을 찾습니다.                                     |
| [`find_spatial_tendons`](#isaaclab_contrib.assets.Multirotor.find_spatial_tendons)(name_keys[, ...])                          | 이름 키를 기반으로 artikulation에서 공간 테넌을 찾습니다.                            |
| [`set_debug_vis`](#isaaclab_contrib.assets.Multirotor.set_debug_vis)(debug_vis)                                               | 에셋 데이터를 시각화할지 여부를 설정합니다.                                                   |
| [`set_external_force_and_torque`](#isaaclab_contrib.assets.Multirotor.set_external_force_and_torque)(forces, torques)         | 에셋 바디의 로컬 프레임에 외부 힘과 토크를 적용하도록 설정합니다.          |
| [`set_fixed_tendon_damping`](#isaaclab_contrib.assets.Multirotor.set_fixed_tendon_damping)(damping[, ...])                    | 고정 테넌 감쇠를 내부 버퍼에 설정합니다.                                             |
| [`set_fixed_tendon_limit`](#isaaclab_contrib.assets.Multirotor.set_fixed_tendon_limit)(limit[, ...])                          | 고정 테넌 위치 제한을 내부 버퍼에 설정합니다.                                     |
| [`set_fixed_tendon_limit_stiffness`](#isaaclab_contrib.assets.Multirotor.set_fixed_tendon_limit_stiffness)(limit_stiffness)   | 고정 테넌 제한 강성을 내부 버퍼에 설정합니다.                             |
| [`set_fixed_tendon_offset`](#isaaclab_contrib.assets.Multirotor.set_fixed_tendon_offset)(offset[, ...])                       | 고정 테넌 오프셋 노력을 내부 버퍼에 설정합니다.                                      |
| [`set_fixed_tendon_position_limit`](#isaaclab_contrib.assets.Multirotor.set_fixed_tendon_position_limit)(limit[, ...])        | 고정 테넌 제한 노력을 내부 버퍼에 설정합니다.                                       |
| [`set_fixed_tendon_rest_length`](#isaaclab_contrib.assets.Multirotor.set_fixed_tendon_rest_length)(rest_length[, ...])        | 고정 테넌 잔여 길이 노력을 내부 버퍼에 설정합니다.                                 |
| [`set_fixed_tendon_stiffness`](#isaaclab_contrib.assets.Multirotor.set_fixed_tendon_stiffness)(stiffness[, ...])              | 고정 테넌 강성을 내부 버퍼에 설정합니다.                                           |
| [`set_joint_effort_target`](#isaaclab_contrib.assets.Multirotor.set_joint_effort_target)(target[, joint_ids, ...])            | 관절 노력을 내부 버퍼에 설정합니다.                                                    |
| [`set_joint_position_target`](#isaaclab_contrib.assets.Multirotor.set_joint_position_target)(target[, ...])                   | 관절 위치 목표를 내부 버퍼에 설정합니다.                                           |
| [`set_joint_velocity_target`](#isaaclab_contrib.assets.Multirotor.set_joint_velocity_target)(target[, ...])                   | 관절 속도 목표를 내부 버퍼에 설정합니다.                                           |
| [`set_spatial_tendon_damping`](#isaaclab_contrib.assets.Multirotor.set_spatial_tendon_damping)(damping[, ...])                | 공간 테넌 감쇠를 내부 버퍼에 설정합니다.                                           |
| [`set_spatial_tendon_limit_stiffness`](#isaaclab_contrib.assets.Multirotor.set_spatial_tendon_limit_stiffness)(...[, ...])    | 공간 테넌 제한 강성을 내부 버퍼에 설정합니다.                                   |
| [`set_spatial_tendon_offset`](#isaaclab_contrib.assets.Multirotor.set_spatial_tendon_offset)(offset[, ...])                   | 공간 테넌 오프셋 노력을 내부 버퍼에 설정합니다.                                    |
| [`set_spatial_tendon_stiffness`](#isaaclab_contrib.assets.Multirotor.set_spatial_tendon_stiffness)(stiffness[, ...])          | 공간 테넌 강성을 내부 버퍼에 설정합니다.                                         |
| [`set_visibility`](#isaaclab_contrib.assets.Multirotor.set_visibility)(visible[, env_ids])                                    | 에셋에 해당하는 프리므의 가시성을 설정합니다.                                 |
| [`update`](#isaaclab_contrib.assets.Multirotor.update)(dt)                                                                    | 내부 버퍼를 업데이트합니다.                                                                |
| [`write_fixed_tendon_properties_to_sim`](#isaaclab_contrib.assets.Multirotor.write_fixed_tendon_properties_to_sim)([...])     | 고정 테넌 속성을 시뮬레이션에 씁니다.                                          |
| [`write_joint_armature_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_armature_to_sim)(armature[, ...])             | 관절 아머처를 시뮬레이션에 씁니다.                                                   |
| [`write_joint_damping_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_damping_to_sim)(damping[, ...])                | 관절 감쇠를 시뮬레이션에 씁니다.                                                    |
| [`write_joint_effort_limit_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_effort_limit_to_sim)(limits[, ...])       | 관절 노력 제한을 시뮬레이션에 씁니다.                                              |
| [`write_joint_friction_coefficient_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_friction_coefficient_to_sim)(...) | 관절 마찰 계수를 시뮬레이션에 씁니다.                                      |
| [`write_joint_friction_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_friction_to_sim)(joint_friction)              | 관절 마찰 계수를 시뮬레이션에 씁니다.                                      |
| [`write_joint_limits_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_limits_to_sim)(limits[, ...])                   | 관절 제한을 시뮬레이션에 씁니다.                                                     |
| [`write_joint_position_limit_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_position_limit_to_sim)(limits[, ...])   | 관절 위치 제한을 시뮬레이션에 씁니다.                                            |
| [`write_joint_position_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_position_to_sim)(position[, ...])             | 관절 위치를 시뮬레이션에 씁니다.                                                    |
| [`write_joint_state_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_state_to_sim)(position, velocity)                | 관절 위치와 속도를 시뮬레이션에 씁니다.                                     |
| [`write_joint_stiffness_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_stiffness_to_sim)(stiffness[, ...])          | 관절 강성을 시뮬레이션에 씁니다.                                                  |
| [`write_joint_velocity_limit_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_velocity_limit_to_sim)(limits[, ...])   | 관절 최대 속도를 시뮬레이션에 씁니다.                                                 |
| [`write_joint_velocity_to_sim`](#isaaclab_contrib.assets.Multirotor.write_joint_velocity_to_sim)(velocity[, ...])             | 관절 속도를 시뮬레이션에 씁니다.                                                    |
| [`write_root_com_pose_to_sim`](#isaaclab_contrib.assets.Multirotor.write_root_com_pose_to_sim)(root_pose[, env_ids])          | 루트 질량 중심을 선택된 환경 인덱스에 설정하여 시뮬레이션에 씁니다.     |
| [`write_root_com_state_to_sim`](#isaaclab_contrib.assets.Multirotor.write_root_com_state_to_sim)(root_state[, ...])           | 루트 질량 중심을 선택된 환경 인덱스에 설정하여 시뮬레이션에 씁니다.    |
| [`write_root_com_velocity_to_sim`](#isaaclab_contrib.assets.Multirotor.write_root_com_velocity_to_sim)(root_velocity)         | 루트 질량 중심을 선택된 환경 인덱스에 설정하여 시뮬레이션에 씁니다. |
| [`write_root_link_pose_to_sim`](#isaaclab_contrib.assets.Multirotor.write_root_link_pose_to_sim)(root_pose[, env_ids])        | 루트 링크를 선택된 환경 인덱스에 설정하여 시뮬레이션에 씁니다.               |
| [`write_root_link_state_to_sim`](#isaaclab_contrib.assets.Multirotor.write_root_link_state_to_sim)(root_state[, ...])         | 루트 링크 상태를 선택된 환경 인덱스에 설정하여 시뮬레이션에 씁니다.              |
| [`write_root_link_velocity_to_sim`](#isaaclab_contrib.assets.Multirotor.write_root_link_velocity_to_sim)(root_velocity)       | 루트 링크 속도를 선택된 환경 인덱스에 설정하여 시뮬레이션에 씁니다.           |
| [`write_root_pose_to_sim`](#isaaclab_contrib.assets.Multirotor.write_root_pose_to_sim)(root_pose[, env_ids])                  | 루트 포즈를 선택된 환경 인덱스에 설정하여 시뮬레이션에 씁니다.                    |
| [`write_root_state_to_sim`](#isaaclab_contrib.assets.Multirotor.write_root_state_to_sim)(root_state[, env_ids])               | 루트 상태를 선택된 환경 인덱스에 설정하여 시뮬레이션에 씁니다.                   |
| [`write_root_velocity_to_sim`](#isaaclab_contrib.assets.Multirotor.write_root_velocity_to_sim)(root_velocity[, ...])          | 루트 질량 중심 속도를 선택된 환경 인덱스에 설정하여 시뮬레이션에 씁니다. |
| [`write_spatial_tendon_properties_to_sim`](#isaaclab_contrib.assets.Multirotor.write_spatial_tendon_properties_to_sim)([...]) | 공간 테넌 속성을 시뮬레이션에 씁니다.                                        |

#### cfg *: [MultirotorCfg](#isaaclab_contrib.assets.MultirotorCfg)*

멀티로터용 구성 인스턴스입니다.

#### actuators *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Thruster](isaaclab_contrib.actuators.md#isaaclab_contrib.actuators.Thruster)]*

다중로터 추진기 액추에이터 인스턴스 딕셔너리.

키는 액추에이터 이름이고 값은 액추에이터 인스턴스입니다. 액추에이터 인스턴스는 [`MultirotorCfg.actuators`](#isaaclab_contrib.assets.MultirotorCfg.actuators) 속성에 지정된 액추에이터 구성에 따라 초기화됩니다. 이들은 [`write_data_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_data_to_sim) 함수에서 추진기 명령을 계산하는 데 사용됩니다.

#### \_\_init_\_(cfg: [MultirotorCfg](#isaaclab_contrib.assets.MultirotorCfg))

다중로터 어티큘레이션을 초기화합니다.

* **매개변수:**
  **cfg** – 구성 인스턴스.

#### *property* thruster_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

다중로터의 추진기 이름의 순서 목록.

이 속성은 다중로터에 구성된 모든 추진기 액추에이터 그룹에서 추진기 이름을 집계합니다. 이름은 배열 인덱스에 따라 정렬되며, 이는 추진 대상 설정 및 추진기 데이터 해석에 중요합니다.

* **반환값:**
  순서대로 정렬된 추진기 이름 목록. 액추에이터가 아직 초기화되지 않은 경우 빈 목록을 반환합니다.
* **예외 상황:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 다중로터 액추에이터에 비추진기 액추에이터가 있는 경우.

#### *property* num_thrusters *: [int](https://docs.python.org/3/library/functions.html#int)*

다중로터의 추진기 수.

* **반환값:**
  모든 액추에이터 그룹에 걸친 총 추진기 수.

#### *property* allocation_matrix *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

제어 할당을 위한 할당 행렬.

할당 행렬은 개별 추진기 힘을 기준으로 프레임의 6D 렌치 벡터(3개의 힘 + 3개의 토크)에 매핑됩니다. 이를 통해 퍼추스처 명령이 바디 프레임에서의 힘과 모먼트로 변환됩니다.

행렬의 형태는 (6, num_thrusters)이며:
: - 행 0-2: 프레임에서의 힘 기여도 (Fx, Fy, Fz)
  - 행 3-5: 프레임에서의 토크 기여도 (Tx, Ty, Tz)

* **반환값:**
  디바이스의 토치 텐서인 할당 행렬.

#### set_thrust_target(target: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), thruster_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

추진기의 목표 추력 값을 설정합니다.

이 메서드는 특정 환경의 특정 추진기에 대한 원하는 추력 값을 설정합니다. 추력 목표는 저장되고 [`write_data_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_data_to_sim) 호출 중에 추진기 액추에이터 모델에 의해 처리됩니다. 액추에이터 모델은 이러한 목표에 동력 특성(상승/하강 시간)과 제약 조건(추력 제한)을 적용할 수 있습니다.

* **매개변수:**
  * **target** – 목표 추력 값. 형태는 (num_envs, num_thrusters) 또는 (num_envs,).
    값은 일반적으로 추진기 액추에이터에서 구성된 단위와 동일합니다(예: 힘의 경우 뉴턴, RPS의 경우 초당 회전 수).
  * **thruster_ids** – 설정할 추진기 인덱스. 기본값은 None(모든 추진기).
    정수 시퀀스, 슬라이스 또는 None일 수 있습니다.
  * **env_ids** – 설정할 환경 인덱스. 기본값은 None(모든 환경).
    정수 시퀀스 또는 None일 수 있습니다.

### 예시

```python
# 모든 환경의 모든 추진기에 추력 설정
multirotor.set_thrust_target(torch.ones(num_envs, 4) * 5.0)

# 특정 추진기에 추력 설정
multirotor.set_thrust_target(
    torch.tensor([[5.0, 6.0]]),  # 2개의 추진기에 서로 다른 추력
    thruster_ids=[0, 2],  # 추진기 0과 2에 적용
    env_ids=[0],  # 환경 0에서만 적용
)
```

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

다중로터를 기본 상태로 재설정합니다.

이 메서드는 기본 어티큘레이션 상태(포즈, 속도)와 다중로터 특화 상태(추력 목표)를 구성에서 지정된 기본값으로 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 환경 인덱스. 기본값은 None(모든 환경).
  정수 시퀀스 또는 None일 수 있습니다.

#### NOTE
기본 추진기 상태는 `MultirotorCfg.init_state.rps` 구성 매개변수를 통해 설정됩니다.

#### write_data_to_sim()

추진 및 토크 명령을 시뮬레이션에 씁니다.

이 메서드는 다음과 같은 순서로 작업을 수행합니다:

1. **액추에이터 모델 적용**: 추진기 액추에이터 모델을 통해 추진 목표를 처리하여 역동(상승/하강 시간) 및 제약(추력 제한)을 고려한 실제 추력 값을 계산합니다.
2. **추력 결합 및 렌치 변환**: 할당 행렬을 사용하여 개별 추진기 힘을 결합된 6D 렌치(힘 + 토크) 벡터로 변환합니다.
3. **시뮬레이션에 적용**: 결합된 렌치를 PhysX 시뮬레이션의 다중로터 기본 링크에 적용합니다.

이 메서드는 [`set_thrust_target()`](#isaaclab_contrib.assets.Multirotor.set_thrust_target)을 통해 추진 목표를 설정한 후 시뮬레이션을 진행하기 전에 호출해야 합니다.

#### NOTE
이 메서드는 다중로터가 관절 기반 제어가 아니라 추력 기반 제어를 사용하기 때문에 기본 클래스 구현을 오버라이드합니다.

#### *property* body_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

어티큘레이션의 바디 이름의 순서 목록.

#### *property* data *: [ArticulationData](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationData)*

자산과 관련된 데이터.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 디바이스.

#### find_bodies(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

이름 키를 기반으로 어티큘레이션에서 바디를 찾습니다.

이름 매칭에 대한 자세한 정보는 `isaaclab.utils.string_utils.resolve_matching_names()` 함수를 참조하십시오.

* **매개변수:**
  * **name_keys** – 바디 이름을 일치시키는 정규 표현식 또는 정규 표현식 목록.
  * **preserve_order** – 출력에서 이름 키의 순서를 보존할지 여부. 기본값은 False입니다.
* **반환값:**
  바디 인덱스와 이름을 포함하는 리스트들의 튜플.

#### find_fixed_tendons(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], tendon_subsets: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None, preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

이름 키를 기반으로 어티큘레이션에서 고정 텐던을 찾습니다.

텐던 이름 매칭에 대한 자세한 정보는 [`isaaclab.utils.string.resolve_matching_names()`](../lab/isaaclab.utils.md#isaaclab.utils.string.resolve_matching_names) 함수를 참조하십시오.

* **매개변수:**
  * **name_keys** – 고정 텐던이 있는 조인트 이름을 일치시키는 정규 표현식 또는 정규 표현식 목록.
  * **tendon_subsets** – 고정 텐던이 있는 조인트 서브셋을 검색할 대상. 기본값은 None이며, 이는 어티큘레이션의 모든 조인트를 검색함을 의미합니다.
  * **preserve_order** – 출력에서 이름 키의 순서를 보존할지 여부. 기본값은 False입니다.
* **반환값:**
  텐던 인덱스와 이름을 포함하는 리스트들의 튜플.

#### find_joints(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], joint_subset: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None, preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

이름 키를 기반으로 어티큘레이션에서 조인트를 찾습니다.

조인트 이름 매칭에 대한 자세한 정보는 [`isaaclab.utils.string.resolve_matching_names()`](../lab/isaaclab.utils.md#isaaclab.utils.string.resolve_matching_names) 함수를 참조하십시오.

* **매개변수:**
  * **name_keys** – 조인트 이름을 일치시키는 정규 표현식 또는 정규 표현식 목록입니다.
  * **joint_subset** – 검색할 조인트 부분 집합입니다. 기본값은 None이며, 이는 조인트 애티큘레이션의 모든 조인트를 검색함을 의미합니다.
  * **preserve_order** – 출력에서 name_keys의 순서를 유지할지 여부입니다. 기본값은 False입니다.
* **Returns:**
  조인트 인덱스와 이름을 포함하는 리스트들의 튜플입니다.

#### find_spatial_tendons(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], tendon_subsets: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None, preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

이름 키를 기반으로 애티큘레이션의 공간 힘줄을 찾습니다.

이름 매칭에 대한 자세한 정보는 [`isaaclab.utils.string.resolve_matching_names()`](../lab/isaaclab.utils.md#isaaclab.utils.string.resolve_matching_names) 함수를 참조하십시오.

* **Parameters:**
  * **name_keys** – 힘줄 이름을 일치시키는 정규 표현식 또는 정규 표현식 목록입니다.
  * **tendon_subsets** – 검색할 힘줄 부분 집합입니다. 기본값은 None이며, 이는 애티큘레이션의 모든 힘줄을 검색함을 의미합니다.
  * **preserve_order** – 출력에서 name_keys의 순서를 유지할지 여부입니다. 기본값은 False입니다.
* **Returns:**
  힘줄 인덱스와 이름을 포함하는 리스트들의 튜플입니다.

#### *property* fixed_tendon_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

애티큘레이션의 고정 힘줄의 순서가 지정된 이름입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 디버그 시각화를 구현하고 있는지 여부입니다.

#### *property* instantaneous_wrench_composer *: WrenchComposer*

순간 렌치 작곡자입니다.

`WrenchComposer` 인스턴스를 반환합니다. 이 렌치 작곡자에 추가되거나 설정된 렌치는 현재 시뮬레이션 단계에만 유효합니다. 시뮬레이션 단계가 끝나면 이 객체에 설정된 렌치는 폐기됩니다. 이는 항상 변하는 힘, 예를 들어 드래그 힘을 적용하는 데 유용합니다.

#### NOTE
영구 렌치는 순간 렌치에 합성된 후 시뮬레이션에 적용됩니다.

#### *property* is_fixed_base *: [bool](https://docs.python.org/3/library/functions.html#bool)*

애티큘레이션이 고정 기반인지 또는 부동 기반 시스템인지 여부입니다.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 초기화되었는지 여부입니다.

자산이 초기화된 경우 True를 반환하고, 그렇지 않은 경우 False를 반환합니다.

#### *property* joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

애티큘레이션의 조인트의 순서가 지정된 이름입니다.

#### *property* num_bodies *: [int](https://docs.python.org/3/library/functions.html#int)*

애티큘레이션의 바디 수입니다.

#### *property* num_fixed_tendons *: [int](https://docs.python.org/3/library/functions.html#int)*

애티큘레이션의 고정 힘줄 수입니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

자산의 인스턴스 수입니다.

이 값은 환경당 자산 인스턴스 수에 환경 수를 곱한 값과 같습니다.

#### *property* num_joints *: [int](https://docs.python.org/3/library/functions.html#int)*

애티큘레이션의 조인트 수입니다.

#### *property* num_spatial_tendons *: [int](https://docs.python.org/3/library/functions.html#int)*

애티큘레이션의 공간 힘줄 수입니다.

#### *property* permanent_wrench_composer *: WrenchComposer*

영구 렌치 작곡자입니다.

`WrenchComposer` 인스턴스를 반환합니다. 이 렌치 작곡자에 추가되거나 설정된 렌치는 지속적이며 시뮬레이션의 모든 단계에 적용됩니다. 이는 일정 기간 동안 일정한 힘, 예를 들어 모터의 추력을 적용하는 데 유용합니다.

#### NOTE
영구 렌치는 순간 렌치에 합성된 후 시뮬레이션에 적용됩니다.

#### *property* root_physx_view *: omni.physics.tensors.impl.api.ArticulationView*

자산의 애티큘레이션 뷰(PhysX)입니다.

#### NOTE
이 뷰는 주의해서 사용해야 합니다. 특정 방식으로 텐서를 처리해야 합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

자산 데이터의 시각화 여부를 설정합니다.

* **Parameters:**
  **debug_vis** – 자산 데이터를 시각화할지 여부입니다.
* **Returns:**
  디버그 시각화가 성공적으로 설정되었는지 여부를 나타냅니다. 자산이 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

#### set_external_force_and_torque(forces: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), torques: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, body_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, is_global: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

자산의 바디의 로컬 프레임에서 외부 힘과 토크를 설정합니다.

많은 애플리케이션에서, 우리는 강체에 적용된 외부 힘을 일정 기간 동안 일정하게 유지하고자 합니다(예: 정책 제어 중). 이 함수는 외부 힘과 토크를 버퍼에 저장하여 시뮬레이션의 모든 단계에 적용할 수 있도록 합니다. 선택적으로, 바디의 로컬 링크 프레임에서 외부 렌치를 적용할 위치를 설정할 수 있습니다(바디의 로컬 링크 프레임 기준).

#### NOTE
이 함수는 외부 렌치를 시뮬레이션에 적용하지 않습니다. 원하는 값으로 버퍼만 채웁니다. 외부 렌치를 적용하려면 시뮬레이션 단계 바로 전에 [`write_data_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_data_to_sim) 함수를 호출하십시오.

* **Parameters:**
  * **forces** – 바디의 로컬 프레임에서의 외부 힘입니다. 크기는 (len(env_ids), len(body_ids), 3)입니다.
  * **torques** – 바디의 로컬 프레임에서의 외부 토크입니다. 크기는 (len(env_ids), len(body_ids), 3)입니다.
  * **positions** – 외부 렌치를 적용할 위치입니다. 크기는 (len(env_ids), len(body_ids), 3)입니다. 기본값은 None입니다.
  * **body_ids** – 외부 렌치를 적용할 바디 인덱스입니다. 기본값은 None이며(모든 바디).
  * **env_ids** – 외부 렌치를 적용할 환경 인덱스입니다. 기본값은 None이며(모든 인스턴스).
  * **is_global** – 외부 렌치를 전역 프레임에서 적용할지 여부입니다. 기본값은 False입니다. False로 설정된 경우, 외부 렌치는 애티큘레이션 바디의 링크 프레임에서 적용됩니다.

#### set_fixed_tendon_damping(damping: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 힘줄 감쇠를 내부 버퍼에 설정합니다.

이 함수는 힘줄 감쇠를 시뮬레이션에 적용하지 않습니다. 원하는 값으로 버퍼만 채웁니다. 힘줄 감쇠를 적용하려면 [`write_fixed_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_fixed_tendon_properties_to_sim) 함수를 호출하십시오.

* **Parameters:**
  * **damping** – 고정 힘줄 감쇠입니다. 크기는 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 감쇠를 설정할 힘줄 인덱스입니다. 기본값은 None이며(모든 고정 힘줄).
  * **env_ids** – 감쇠를 설정할 환경 인덱스입니다. 기본값은 None이며(모든 환경).

#### set_fixed_tendon_limit(limit: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 힘줄 위치 제한을 내부 버퍼에 설정합니다.

#### Deprecated
버전 2.1.0부터 더 이상 사용되지 않음: [`set_fixed_tendon_position_limit()`](#isaaclab_contrib.assets.Multirotor.set_fixed_tendon_position_limit)를 대신 사용하십시오.

#### set_fixed_tendon_limit_stiffness(limit_stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 테니드 한계 강성을 내부 버퍼에 설정합니다.

이 함수는 테니드 한계 강성을 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 테니드 한계 강성을 적용하려면
[`write_fixed_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_fixed_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **limit_stiffness** – 고정 테니드 한계 강성. 형태는 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 한계 강성을 설정할 테니드 인덱스. 기본값은 None(모든 고정 테니드)입니다.
  * **env_ids** – 한계 강성을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_offset(offset: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 테니드 오프셋을 내부 버퍼에 설정합니다.

이 함수는 테니드 오프셋을 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 테니드 오프셋을 적용하려면
[`write_fixed_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_fixed_tendon_properties_to_sim) 함수를 호출하세요.

* **매개변수:**
  * **offset** – 고정 테니드 오프셋. 형태는 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 오프셋을 설정할 테니드 인덱스. 기본값은 None(모든 고정 테니드)입니다.
  * **env_ids** – 오프셋을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_position_limit(limit: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 테니드 위치 한계를 내부 버퍼에 설정합니다.

이 함수는 테니드 위치 한계를 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 테니드 위치 한계를 적용하려면
[`write_fixed_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_fixed_tendon_properties_to_sim) 함수를 호출하세요.

> Args:
> : limit: 고정 테니드 한계. 형태는 (len(env_ids), len(fixed_tendon_ids))입니다.
>   fixed_tendon_ids: 한계를 설정할 테니드 인덱스. 기본값은 None(모든 고정 테니드)입니다.
>   env_ids: 한계를 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_rest_length(rest_length: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 테니드 휴식 길이를 내부 버퍼에 설정합니다.

이 함수는 테니드 휴식 길이를 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 테니드 휴식 길이를 적용하려면
[`write_fixed_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_fixed_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **rest_length** – 고정 테니드 휴식 길이. 형태는 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 휴식 길이를 설정할 테니드 인덱스. 기본값은 None(모든 고정 테니드)입니다.
  * **env_ids** – 휴식 길이를 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_stiffness(stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 테니드 강성을 내부 버퍼에 설정합니다.

이 함수는 테니드 강성을 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 테니드 강성을 적용하려면
[`write_fixed_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_fixed_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **stiffness** – 고정 테니드 강성. 형태는 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 강성을 설정할 테니드 인덱스. 기본값은 None(모든 고정 테니드)입니다.
  * **env_ids** – 강성을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_joint_effort_target(target: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

관절 노력을 내부 버퍼에 설정합니다.

이 함수는 관절 목표를 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 관절 목표를 적용하려면
[`write_data_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_data_to_sim) 함수를 호출하세요.

* **매개변수:**
  * **target** – 관절 노력이 목표. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 목표를 설정할 관절 인덱스. 기본값은 None(모든 관절)입니다.
  * **env_ids** – 목표를 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_joint_position_target(target: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

관절 위치 목표를 내부 버퍼에 설정합니다.

이 함수는 관절 목표를 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 관절 목표를 적용하려면
[`write_data_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_data_to_sim) 함수를 호출하세요.

* **매개변수:**
  * **target** – 관절 위치 목표. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 목표를 설정할 관절 인덱스. 기본값은 None(모든 관절)입니다.
  * **env_ids** – 목표를 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_joint_velocity_target(target: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

관절 속도 목표를 내부 버퍼에 설정합니다.

이 함수는 관절 목표를 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 관절 목표를 적용하려면
[`write_data_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_data_to_sim) 함수를 호출하세요.

* **매개변수:**
  * **target** – 관절 속도 목표. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 대상을 설정할 조인트 인덱스. 기본값은 None(모든 조인트).
  * **env_ids** – 대상을 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### set_spatial_tendon_damping(damping: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

공간 힘줄 감쇠를 내부 버퍼에 설정합니다.

이 함수는 힘줄 감쇠를 시뮬레이션에 적용하지 않습니다. 버퍼에 원하는 값만 채웁니다. 힘줄 감쇠를 적용하려면
[`write_spatial_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_spatial_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **damping** – 공간 힘줄 감쇠. 차원은 (len(env_ids), len(spatial_tendon_ids)).
  * **spatial_tendon_ids** – 감쇠를 설정할 힘줄 인덱스. 기본값은 None이며, 이는 모든 공간 힘줄을 의미합니다.
  * **env_ids** – 감쇠를 설정할 환경 인덱스. 기본값은 None이며, 이는 모든 환경을 의미합니다.

#### set_spatial_tendon_limit_stiffness(limit_stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

공간 힘줄 제한 강성을 내부 버퍼에 설정합니다.

이 함수는 힘줄 제한 강성을 시뮬레이션에 적용하지 않습니다. 버퍼에 원하는 값만 채웁니다. 힘줄 제한 강성을 적용하려면
[`write_spatial_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_spatial_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **limit_stiffness** – 공간 힘줄 제한 강성. 차원은 (len(env_ids), len(spatial_tendon_ids)).
  * **spatial_tendon_ids** – 제한 강성을 설정할 힘줄 인덱스. 기본값은 None이며, 이는 모든 공간 힘줄을 의미합니다.
  * **env_ids** – 제한 강성을 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### set_spatial_tendon_offset(offset: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

공간 힘줄 오프셋 힘을 내부 버퍼에 설정합니다.

이 함수는 힘줄 오프셋을 시뮬레이션에 적용하지 않습니다. 버퍼에 원하는 값만 채웁니다. 힘줄 오프셋을 적용하려면
[`write_spatial_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_spatial_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **offset** – 공간 힘줄 오프셋. 차원은 (len(env_ids), len(spatial_tendon_ids)).
  * **spatial_tendon_ids** – 오프셋을 설정할 힘줄 인덱스. 기본값은 None(모든 공간 힘줄).
  * **env_ids** – 오프셋을 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### set_spatial_tendon_stiffness(stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

공간 힘줄 강성을 내부 버퍼에 설정합니다.

이 함수는 힘줄 강성을 시뮬레이션에 적용하지 않습니다. 버퍼에 원하는 값만 채웁니다. 힘줄 강성을 적용하려면
[`write_spatial_tendon_properties_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_spatial_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **stiffness** – 공간 힘줄 강성. 차원은 (len(env_ids), len(spatial_tendon_ids)).
  * **spatial_tendon_ids** – 강성을 설정할 힘줄 인덱스. 기본값은 None(모든 공간 힘줄).
  * **env_ids** – 강성을 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### set_visibility(visible: [bool](https://docs.python.org/3/library/functions.html#bool), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

자산에 해당하는 프리미브의 가시성을 설정합니다.

이 작업은 USD 스테이지에서 자산에 해당하는 프리미브의 가시성에 영향을 줍니다. 시뮬레이터에서 자산의 가시성을 토글하는 데 유용합니다. 예를 들어, 자산이 사용되지 않을 때 렌더링 오버헤드를 줄이기 위해 자산을 숨길 수 있습니다.

#### NOTE
이 작업은 PXR API를 사용하여 프리미브의 가시성을 설정합니다. 따라서 프리미브 수가 많을 경우 오버헤드가 발생할 수 있습니다.

* **매개변수:**
  * **visible** – 프리미브를 보이게 할지 여부.
  * **env_ids** – 가시성을 설정할 객체 인덱스. 기본값은 None(모든 인스턴스).

#### *property* spatial_tendon_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

관절 내 공간 힘줄의 순서가 지정된 이름 목록.

#### update(dt: [float](https://docs.python.org/3/library/functions.html#float))

내부 버퍼를 업데이트합니다.

시간 단계 `dt`는 시뮬레이터에서 제공하지 않는 관성 가속도와 같은 양의 수치 미분을 계산하는 데 사용됩니다.

* **매개변수:**
  **dt** – 마지막 `update` 호출로부터 지난 시간 간격.

#### write_fixed_tendon_properties_to_sim(fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 힘줄 속성을 시뮬레이션에 씁니다.

* **매개변수:**
  * **fixed_tendon_ids** – 제한을 설정할 고정 힘줄 인덱스. 기본값은 None(모든 고정 힘줄).
  * **env_ids** – 제한을 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_armature_to_sim(armature: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

조인트 관성 모멘트를 시뮬레이션에 씁니다.

관성은 해당 조인트 공간 관성에 직접 추가됩니다. 이는 조인트 속도를 줄여 시뮬레이션 안정성을 향상시키는 데 도움이 됩니다.

* **매개변수:**
  * **armature** – 조인트 관성 모멘트. 차원은 (len(env_ids), len(joint_ids)).
  * **joint_ids** – 조인트 토크 한계를 설정할 조인트 인덱스. 기본값은 None(모든 조인트).
  * **env_ids** – 조인트 토크 한계를 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_damping_to_sim(damping: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

조인트 감쇠를 시뮬레이션에 씁니다.

* **매개변수:**
  * **damping** – 조인트 감쇠. 차원은 (len(env_ids), len(joint_ids)).
  * **joint_ids** – 감쇠를 설정할 조인트 인덱스. 기본값은 None(모든 조인트).
  * **env_ids** – 감쇠를 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_effort_limit_to_sim(limits: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 힘 한계를 씁니다.

힘 한계는 물리 엔진에서 계산된 조인트 힘을 제한하는 데 사용됩니다. 계산된 힘이 이 한계를 초과하면, 물리 엔진은 힘을 이 값으로 자릅니다.

* **매개변수:**
  * **limits** – 조인트 토크 한계. 차원은 (len(env_ids), len(joint_ids)).
  * **joint_ids** – 조인트 토크 한계를 설정할 조인트 인덱스. 기본값은 None (모든 조인트).
  * **env_ids** – 조인트 토크 한계를 설정할 환경 인덱스. 기본값은 None (모든 환경).

#### write_joint_friction_coefficient_to_sim(joint_friction_coeff: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_dynamic_friction_coeff: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, joint_viscous_friction_coeff: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 마찰 계수를 씁니다.

Isaac Sim 5.0 미만 버전에서는 정적 마찰 계수만 설정됩니다. 이는 전송된 공간 힘에 비례하는 최대 저항력 또는 토크를 제한합니다: $\|F_{resist}\| \leq \mu_s \, \|F_{spatial}\|$.

Isaac Sim 5.0 이상 버전에서는 정적, 동적, 점성 마찰 계수가 모두 설정됩니다. 모델은 쿨롬(정적 및 동적) 마찰과 점성 항을 결합합니다:

- 정적 마찰 $\mu_s$는 정지 상태에서 움직임을 방지하는 최대 힘을 정의합니다.
- 동적 마찰 $\mu_d$는 움직임이 시작된 후 적용되며 움직인 동안 일정하게 유지됩니다.
- 점성 마찰 $c_v$는 속도에 비례하는 저항 항입니다.

* **매개변수:**
  * **joint_friction_coeff** – 정적 마찰 계수 $\mu_s$.
    차원은 (len(env_ids), len(joint_ids)). 스칼라는 모든 선택에 브로드캐스트됩니다.
  * **joint_dynamic_friction_coeff** – 동적 (쿨롬) 마찰 계수 $\mu_d$.
    위와 동일한 형태입니다. None이면 동적 계수는 업데이트되지 않습니다.
  * **joint_viscous_friction_coeff** – 점성 마찰 계수 $c_v$.
    위와 동일한 형태입니다. None이면 점성 계수는 업데이트되지 않습니다.
  * **joint_ids** – 마찰 계수를 설정할 조인트 인덱스. 기본값은 None (모든 조인트).
  * **env_ids** – 마찰 계수를 설정할 환경 인덱스. 기본값은 None (모든 환경).

#### write_joint_friction_to_sim(joint_friction: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 마찰 계수를 씁니다.

#### Deprecated
2.1.0 버전부터 더 이상 사용되지 않음: [`write_joint_friction_coefficient_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_joint_friction_coefficient_to_sim)를 대신 사용하십시오.

#### write_joint_limits_to_sim(limits: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, warn_limit_violation: [bool](https://docs.python.org/3/library/functions.html#bool) = True)

시뮬레이션에 조인트 제한을 씁니다.

#### Deprecated
2.1.0 버전부터 더 이상 사용되지 않음: [`write_joint_position_limit_to_sim()`](#isaaclab_contrib.assets.Multirotor.write_joint_position_limit_to_sim)를 대신 사용하십시오.

#### write_joint_position_limit_to_sim(limits: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, warn_limit_violation: [bool](https://docs.python.org/3/library/functions.html#bool) = True)

시뮬레이션에 조인트 위치 제한을 씁니다.

* **매개변수:**
  * **limits** – 조인트 제한. 차원은 (len(env_ids), len(joint_ids), 2).
  * **joint_ids** – 제한을 설정할 조인트 인덱스. 기본값은 None (모든 조인트).
  * **env_ids** – 제한을 설정할 환경 인덱스. 기본값은 None (모든 환경).
  * **warn_limit_violation** – 기본 조인트 위치가 새로운 제한을 초과할 때 경고 또는 정보 수준 로깅을 사용할지 여부. 기본값은 True.

#### write_joint_position_to_sim(position: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 위치를 씁니다.

* **매개변수:**
  * **position** – 조인트 위치. 차원은 (len(env_ids), len(joint_ids)).
  * **joint_ids** – 대상을 설정할 조인트 인덱스. 기본값은 None (모든 조인트).
  * **env_ids** – 대상을 설정할 환경 인덱스. 기본값은 None (모든 환경).

#### write_joint_state_to_sim(position: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 위치와 속도를 씁니다.

* **매개변수:**
  * **position** – 조인트 위치. 차원은 (len(env_ids), len(joint_ids)).
  * **velocity** – 조인트 속도. 차원은 (len(env_ids), len(joint_ids)).
  * **joint_ids** – 대상을 설정할 조인트 인덱스. 기본값은 None (모든 조인트).
  * **env_ids** – 대상을 설정할 환경 인덱스. 기본값은 None (모든 환경).

#### write_joint_stiffness_to_sim(stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 강성을 씁니다.

* **매개변수:**
  * **stiffness** – 조인트 강성. 차원은 (len(env_ids), len(joint_ids)).
  * **joint_ids** – 강성을 설정할 조인트 인덱스. 기본값은 None (모든 조인트).
  * **env_ids** – 강성을 설정할 환경 인덱스. 기본값은 None (모든 환경).

#### write_joint_velocity_limit_to_sim(limits: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 최대 속도를 작성합니다.

속도 제한은 물리 엔진에서 조인트 속도를 제한하는 데 사용됩니다. 조인트가 이 속도에 도달하려면 조인트의 힘 제한이 충분히 커야 합니다. 조인트가 이 속도보다 빠르게 움직이는 경우, 물리 엔진은 실제로 이 속도에 도달하도록 조인트를 브레이크하려고 시도합니다.

* **매개변수:**
  * **limits** – 조인트 최대 속도. 모양은 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 최대 속도를 설정할 조인트 인덱스입니다. 기본값은 None(모든 조인트)입니다.
  * **env_ids** – 최대 속도를 설정할 환경 인덱스입니다. 기본값은 None(모든 환경)입니다.

#### write_joint_velocity_to_sim(velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 속도를 작성합니다.

* **매개변수:**
  * **velocity** – 조인트 속도. 모양은 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 대상을 설정할 조인트 인덱스입니다. 기본값은 None(모든 조인트)입니다.
  * **env_ids** – 대상을 설정할 환경 인덱스입니다. 기본값은 None(모든 환경)입니다.

#### write_root_com_pose_to_sim(root_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심을 시뮬레이션에 설정합니다.

루트 포즈는 (w, x, y, z) 순서의 데카르트 위치와 쿼터니언 방향을 포함합니다. 방향은 관성 원리의 주요 축의 방향입니다.

* **매개변수:**
  * **root_pose** – 시뮬레이션 프레임에서의 루트 질량 중심 포즈. 모양은 (len(env_ids), 7)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_com_state_to_sim(root_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심 상태를 시뮬레이션에 설정합니다.

루트 상태는 데카르트 위치, (w, x, y, z) 순서의 쿼터니언 방향, 그리고 선형 및 각속도로 구성됩니다. 모든 양은 시뮬레이션 프레임에 있습니다.

* **매개변수:**
  * **root_state** – 시뮬레이션 프레임에서의 루트 상태. 모양은 (len(env_ids), 13)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_com_velocity_to_sim(root_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심 속도를 시뮬레이션에 설정합니다.

속도는 순서대로 선형 속도(x, y, z)와 각속도(x, y, z)로 구성됩니다.
참고: 이는 루트 프레임이 아닌 루트의 질량 중심의 속도를 설정합니다.

* **매개변수:**
  * **root_velocity** – 시뮬레이션 월드 프레임에서의 루트 질량 중심 속도. 모양은 (len(env_ids), 6)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_link_pose_to_sim(root_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 링크 포즈를 시뮬레이션에 설정합니다.

루트 포즈는 (w, x, y, z) 순서의 데카르트 위치와 쿼터니언 방향을 포함합니다.

* **매개변수:**
  * **root_pose** – 시뮬레이션 프레임에서의 루트 포즈. 모양은 (len(env_ids), 7)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_link_state_to_sim(root_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 링크 상태를 시뮬레이션에 설정합니다.

루트 상태는 데카르트 위치, (w, x, y, z) 순서의 쿼터니언 방향, 그리고 선형 및 각속도로 구성됩니다. 모든 양은 시뮬레이션 프레임에 있습니다.

* **매개변수:**
  * **root_state** – 시뮬레이션 프레임에서의 루트 상태. 모양은 (len(env_ids), 13)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_link_velocity_to_sim(root_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 링크 속도를 시뮬레이션에 설정합니다.

속도는 순서대로 선형 속도(x, y, z)와 각속도(x, y, z)로 구성됩니다.
참고: 이는 루트의 질량 중심이 아닌 루트 프레임의 속도를 설정합니다.

* **매개변수:**
  * **root_velocity** – 시뮬레이션 월드 프레임에서의 루트 프레임 속도. 모양은 (len(env_ids), 6)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_pose_to_sim(root_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 포즈를 시뮬레이션에 설정합니다.

루트 포즈는 (w, x, y, z) 순서의 데카르트 위치와 쿼터니언 방향을 포함합니다.

* **매개변수:**
  * **root_pose** – 시뮬레이션 프레임에서의 루트 포즈. 모양은 (len(env_ids), 7)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_state_to_sim(root_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 상태를 시뮬레이션에 설정합니다.

루트 상태는 데카르트 위치, (w, x, y, z) 순서의 쿼터니언 방향, 그리고 선형 및 각속도로 구성됩니다. 모든 양은 시뮬레이션 프레임에 있습니다.

* **매개변수:**
  * **root_state** – 시뮬레이션 프레임에서의 루트 상태. 모양은 (len(env_ids), 13)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_velocity_to_sim(root_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심 속도를 시뮬레이션에 설정합니다.

속도는 순서대로 선형 속도(x, y, z)와 각속도(x, y, z)로 구성됩니다.
참고: 이는 루트 프레임이 아닌 루트의 질량 중심의 속도를 설정합니다.

* **매개변수:**
  * **root_velocity** – 시뮬레이션 월드 프레임에서의 루트 질량 중심 속도. 모양은 (len(env_ids), 6)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우 모든 인덱스가 사용됩니다.

#### write_spatial_tendon_properties_to_sim(spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 공간 테endon 속성을 작성합니다.

* **매개변수:**
  * **spatial_tendon_ids** – 속성을 설정할 공간 테endon 인덱스입니다. 기본값은 None이며, 이는 모든 공간 테endon을 의미합니다.
  * **env_ids** – 속성을 설정할 환경 인덱스입니다. 기본값은 None이며, 이는 모든 환경을 의미합니다.

### *class* isaaclab_contrib.assets.MultirotorCfg

기반 클래스: [`ArticulationCfg`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg)

멀티로터 관절에 대한 구성 매개변수입니다.

이 구성 클래스는 [`ArticulationCfg`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg)를 확장하여 트러스터 액추에이터, 할당 매트릭스, 트러스터 특정 초기 상태와 같은 멀티로터 전용 매개변수를 추가합니다.

조인트 액추에이터를 사용하는 표준 관절과 달리, 멀티로터는 개별 로터/프로펠러 동역학을 모델링하는 [`ThrusterCfg`](isaaclab_contrib.actuators.md#isaaclab_contrib.actuators.ThrusterCfg) 액추에이터로 구성됩니다.

주요 구성 매개변수:
: - **actuators**: 액추에이터 이름을 [`ThrusterCfg`](isaaclab_contrib.actuators.md#isaaclab_contrib.actuators.ThrusterCfg) 구성에 매핑하는 딕셔너리입니다. 각 구성은 공유 속성을 가진 트러스터 그룹을 정의합니다.
  - **allocation_matrix**: 개별 트러스터 힘을 6D 바디 렌치로 매핑합니다. 이 행렬은 기하학적 구성을 인코딩하며, 형태는 (6, num_thrusters)여야 합니다.
  - **thruster_force_direction**: 바디 프레임에서 트러스터가 밀치는 방향 벡터입니다. 일반적으로 위로 향하는 트러스터의 경우 (0, 0, 1)입니다.
  - **rotor_directions**: 각 로터의 회전 방향(1은 반시계 방향, -1은 시계 방향). 반응 토크 계산에 사용됩니다.

### 예시

```python
from isaaclab_contrib.assets import MultirotorCfg
from isaaclab_contrib.actuators import ThrusterCfg
import isaaclab.sim as sim_utils

# 쿼드콥터 구성
quadcopter_cfg = MultirotorCfg(
    prim_path="/World/envs/env_.*/Quadcopter",
    spawn=sim_utils.UsdFileCfg(
        usd_path="path/to/quadcopter.usd",
    ),
    init_state=MultirotorCfg.InitialStateCfg(
        pos=(0.0, 0.0, 1.0),  # 지상으로부터 1m 위 시작
        rps={".*": 110.0},  # 모든 트러스터 110 RPS (호버링)
    ),
    actuators={
        "thrusters": ThrusterCfg(
            thruster_names_expr=["rotor_[0-3]"],
            thrust_range=(0.0, 12.0),  # 트러스터당 0-12N
            rise_time_constant=0.12,
            fall_time_constant=0.25,
        ),
    },
    allocation_matrix=[
        [1.0, 1.0, 1.0, 1.0],  # 수직 추력
        [0.0, 0.0, 0.0, 0.0],  # X축 측방 힘
        [0.0, 0.0, 0.0, 0.0],  # Y축 측방 힘
        [0.0, 0.13, 0.0, -0.13],  # 롤 토크
        [-0.13, 0.0, 0.13, 0.0],  # 피치 토크
        [0.01, -0.01, 0.01, -0.01],  # 요 토크
    ],
    rotor_directions=[1, -1, 1, -1],  # 교대로 CW/CCW
)
```

#### 관련 참조
- [`ArticulationCfg`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg): 기본 관절 구성
- [`ThrusterCfg`](isaaclab_contrib.actuators.md#isaaclab_contrib.actuators.ThrusterCfg): 트러스터 액추에이터 구성
- [`Multirotor`](#isaaclab_contrib.assets.Multirotor): 멀티로터 자산 클래스

**클래스:**

| [`InitialStateCfg`](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg)   | 멀티로터 관절의 초기 상태.   |
|-------------------------------------------------------------------------------|-------------------------------------------------|

**속성:**

| [`init_state`](#isaaclab_contrib.assets.MultirotorCfg.init_state)                                                       | 멀티로터 객체의 초기 상태.                                                                       |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| [`actuators`](#isaaclab_contrib.assets.MultirotorCfg.actuators)                                                         | 해당 트러스터 이름을 가진 멀티로터의 트러스터 액추에이터.                                      |
| [`prim_path`](#isaaclab_contrib.assets.MultirotorCfg.prim_path)                                                         | 자산의 프라이머 경로(또는 표현식).                                                                       |
| [`spawn`](#isaaclab_contrib.assets.MultirotorCfg.spawn)                                                                 | 자산의 스폰 구성.                                                                            |
| [`collision_group`](#isaaclab_contrib.assets.MultirotorCfg.collision_group)                                             | 자산의 충돌 그룹.                                                                                 |
| [`debug_vis`](#isaaclab_contrib.assets.MultirotorCfg.debug_vis)                                                         | 자산에 대한 디버그 시각화 활성화 여부.                                                          |
| [`articulation_root_prim_path`](#isaaclab_contrib.assets.MultirotorCfg.articulation_root_prim_path)                     | [`prim_path`](#isaaclab_contrib.assets.MultirotorCfg.prim_path) 아래의 관절 루트 프라이머 경로. |
| [`soft_joint_pos_limit_factor`](#isaaclab_contrib.assets.MultirotorCfg.soft_joint_pos_limit_factor)                     | 자산에서 파싱된 조인트 위치 제한 범위를 사용하는 비율 지정.                        |
| [`actuator_value_resolution_debug_print`](#isaaclab_contrib.assets.MultirotorCfg.actuator_value_resolution_debug_print) | 입력 cfg가 USD 값과 다를 때 액추에이터 최종 값의 해상도를 출력합니다. 기본값은 False입니다.    |
| [`thruster_force_direction`](#isaaclab_contrib.assets.MultirotorCfg.thruster_force_direction)                           | 트러스터의 바디 로컬 프레임에서의 기본 힘 방향.                                                    |
| [`allocation_matrix`](#isaaclab_contrib.assets.MultirotorCfg.allocation_matrix)                                         | 제어 할당을 위한 할당 매트릭스.                                                                     |
| [`rotor_directions`](#isaaclab_contrib.assets.MultirotorCfg.rotor_directions)                                           | 각 트러스터의 로터 방향 시퀀스.                                                               |

#### *class* InitialStateCfg

기반 클래스: [`InitialStateCfg`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg.InitialStateCfg)

멀티로터 관절의 초기 상태입니다.

이 클래스는 베이스 관절 초기 상태를 트러스터 특정 초기 조건을 포함하도록 확장합니다. 트러스터 초기 상태는 멀티로터 안정성에 특히 중요하며, 이는 시작 추력 수준을 결정합니다.

호버링 멀티로터의 경우, 중력을 상쇄할 수 있는 충분한 추력을 생성하도록 초기 RPS를 설정해야 합니다.

**속성:**

| [`rps`](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg.rps)             | 트러스터의 초당 회전수(RPS).                          |
|---------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| [`pos`](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg.pos)             | 시뮬레이션 월드 프레임에서 루트의 위치.                         |
| [`rot`](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg.rot)             | 시뮬레이션 월드 프레임에서 루트의 쿼터니언 회전(w, x, y, z). |
| [`lin_vel`](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg.lin_vel)     | 시뮬레이션 월드 프레임에서 루트의 선형 속도.                  |
| [`ang_vel`](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg.ang_vel)     | 시뮬레이션 월드 프레임에서 루트의 각속도.                 |
| [`joint_pos`](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg.joint_pos) | 조인트 위치.                                          |
| [`joint_vel`](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg.joint_vel) | 조인트 속도.                                         |

#### rps *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

트러스터의 초당 회전수(RPS). 기본값은 100 RPS입니다.

다음과 같이 지정할 수 있습니다:

- RPS 값을 매핑하는 정규 표현식 패턴의 딕셔너리
- `"{".*": 100.0}"`와 같은 단일 와일드카드 패턴으로 균일한 RPS 지정
- `{"rotor_0": 95.0, "rotor_1": 105.0}`와 같은 명시적 각 트러스터별 값

RPS 값은 트러스터 상태를 초기화하고 멀티로터가 리셋될 때 기본 추력 타깃을 결정하는 데 사용됩니다.

### 예시

```python
# 모든 트러스터에 균일한 RPS
rps = {".*": 110.0}

# 트러스터 그룹별로 다른 RPS
rps = {"rotor_[0-1]": 105.0, "rotor_[2-3]": 115.0}
```

#### 참고
실제로 생성되는 추력은 [`ThrusterCfg`](isaaclab_contrib.actuators.md#isaaclab_contrib.actuators.ThrusterCfg)의 추력 곡선 및 다른 매개변수에 따라 달라집니다.

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 위치. 기본값은 (0.0, 0.0, 0.0)입니다.

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 쿼터니언 회전(w, x, y, z). 기본값은 (1.0, 0.0, 0.0, 0.0)입니다.

#### lin_vel *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 선형 속도. 기본값은 (0.0, 0.0, 0.0)입니다.

#### ang_vel *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 각속도. 기본값은 (0.0, 0.0, 0.0)입니다.

#### joint_pos *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

관절의 위치. 모든 관절에 대해 기본값은 0.0입니다.

#### joint_vel *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

관절의 속도. 모든 관절에 대해 기본값은 0.0입니다.

#### init_state *: [InitialStateCfg](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg)*

멀티로터 객체의 초기 상태.

이에는 기본 관절 상태(위치, 방향, 속도)와 멀티로터 특정 상태(추진기 회전속도)가 포함됩니다. 자세한 내용은 [`InitialStateCfg`](#isaaclab_contrib.assets.MultirotorCfg.InitialStateCfg)를 참조하세요.

#### actuators *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab_contrib.actuators.thruster_cfg.ThrusterCfg](isaaclab_contrib.actuators.md#isaaclab_contrib.actuators.ThrusterCfg)]*

해당 추진기 이름과 함께 멀티로터의 추진기 액추에이터.

이 딕셔너리는 액추에이터 그룹 이름을 해당 구성에 매핑합니다. 각 [`ThrusterCfg`](isaaclab_contrib.actuators.md#isaaclab_contrib.actuators.ThrusterCfg)는 공유 동적 특성(상승/하강 시간, 추력 한계 등)을 가진 추진기 그룹을 정의합니다.

### 예시

```python
actuators = {
    "thrusters": ThrusterCfg(
        thruster_names_expr=["rotor_.*"],  # 스러스터 본체와 일치하는 정규식
        thrust_range=(0.0, 10.0),
        rise_time_constant=0.1,
        fall_time_constant=0.2,
    )
}
```

#### NOTE
표준 관절과 달리, 멀티로터는 추진기 액추에이터만 포함해야 합니다.
관절 기반과 추력 기반 액추에이터를 혼합하는 것은 현재 지원되지 않습니다.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

자산의 Prim 경로(또는 표현식).

#### NOTE
표현식에는 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`가 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot` 은 `/World/envs/env_.*/Robot` 로 대체됩니다.

#### spawn *: [SpawnerCfg](../lab/isaaclab.sim.spawners.md#isaaclab.sim.spawners.SpawnerCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

자산의 스폰 구성. 기본값은 None입니다.

None인 경우, 자산 클래스가 어떤 Prim도 스폰하지 않으며 대신 자산이 장면에 이미 존재한다고 가정됩니다.

#### collision_group *: Literal[0, -1]*

자산의 충돌 그룹. 기본값은 `0`입니다.

* `-1`: 전역 충돌 그룹 (장면의 모든 자산과 충돌)
* `0`: 로컬 충돌 그룹 (같은 환경의 다른 자산과 충돌)

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산에 대한 디버그 시각화를 활성화할지 여부. 기본값은 `False`입니다.

#### articulation_root_prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

[`prim_path`](#isaaclab_contrib.assets.MultirotorCfg.prim_path) 아래의 관절 루트 Prim 경로. 기본값은 None이며, 이 경우 클래스는 USD ArticulationRootAPI를 가진 Prim을 찾습니다.

이 경로는 자산의 [`prim_path`](#isaaclab_contrib.assets.MultirotorCfg.prim_path)에 상대적이어야 합니다. 자산이 USD 파일에서 로드되는 경우, 이 경로는 USD 단계의 루트에 상대적이어야 합니다. 예를 들어, [`prim_path`](#isaaclab_contrib.assets.MultirotorCfg.prim_path)에서 로드된 USD 파일에 /robot1과 /robot2에 각각 하나의 관절이 있고 robot2를 사용하려면 이를 `/robot2`로 설정해야 합니다.

경로는 슬래시(/)로 시작해야 합니다.

#### soft_joint_pos_limit_factor *: [float](https://docs.python.org/3/library/functions.html#float)*

자산에서 파싱된 관절 위치 제한 범위의 비율을 지정합니다. 기본값은 1.0입니다.

소프트 관절 위치 제한은 이 계수로 스케일링되어 시뮬레이션된 관절 위치 제한 내의 안전 영역을 지정합니다. 이는 시뮬레이션에서 사용되지 않지만, 학습 에이전트가 관절 위치가 제한을 위반하는 것을 방지하는 데 유용합니다(종료 조건 등으로).

소프트 관절 위치 제한은 `ArticulationData.soft_joint_pos_limits` 속성을 통해 접근할 수 있습니다.

#### actuator_value_resolution_debug_print *: [bool](https://docs.python.org/3/library/functions.html#bool)*

입력 cfg가 USD 값과 다를 때 액추에이터 최종 값의 해상도를 출력합니다. 기본값은 False입니다.

#### thruster_force_direction *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

추진기의 바디-로컬 프레임에서의 기본 힘 방향. 기본값은 `(0.0, 0.0, 1.0)`이며, 이는 Z축을 따라 위쪽 방향입니다.

이 3D 단위 벡터는 멀티로터의 바디 프레임에서 추진기가 힘을 생성하는 방향을 지정합니다. 표준 구성의 경우:

- `(0.0, 0.0, 1.0)`: 추진기가 위쪽으로 empuje (Z축, 쿼드콥터의 일반적인 경우)
- `(0.0, 0.0, -1.0)`: 추진기가 아래로 empuje
- `(1.0, 0.0, 0.0)`: 추진기가 앞으로 empuje (X축)

이는 각 추진기가 생성하는 렌치를 계산하기 위해 할당 매트릭스와 함께 사용됩니다.

기본값: `(0.0, 0.0, 1.0)` (Z축을 따라 위쪽)

#### allocation_matrix *: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]] | [None](https://docs.python.org/3/library/constants.html#None)*

제어 할당을 위한 할당 매트릭스. 기본값은 `None`이며, 이는 추진기가 제어 할당에 사용되지 않음을 의미합니다.

이 매트릭스는 개별 추진기 힘을 멀티로터 베이스 링크에 적용되는 6D 렌치(힘 + 토크)로 매핑합니다. 형태는 `(6, num_thrusters)`입니다:

- **행 0-2**: 바디 프레임에서의 힘 기여 (Fx, Fy, Fz)
- **행 3-5**: 바디 프레임에서의 토크 기여 (Tx, Ty, Tz)

할당 매트릭스는 추진기의 위치, 방향, 모멘트 암을 포함한 멀티로터의 기하학적 구성을 인코딩합니다.

4추진기가 + 구성인 쿼드콥터 예시:
: ```python
  allocation_matrix = [
      [1.0,  1.0,  1.0,  1.0],     # 총 수직 추력
      [0.0,  0.0,  0.0,  0.0],     # 측면 힘 없음
      [0.0,  0.0,  0.0,  0.0],     # 측면 힘 없음
      [0.0,  0.13, 0.0, -0.13],    # 롤 모멘트 (좌/우)
      [-0.13, 0.0, 0.13, 0.0],     # 피치 모멘트 (앞/뒤)
      [0.01,-0.01, 0.01,-0.01],    # 요 모멘트 (회전)
  ]
  ```

#### NOTE
`None`인 경우, 힘을 다른 수단을 통해 적용해야 합니다. 일반적인 멀티로터 제어를 위해서는 항상 지정해야 합니다.

#### rotor_directions *: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None)*

각 추진기의 로터 방향 시퀀스. 기본값은 `None`이며, 이는 로터 방향이 지정되지 않았음을 의미합니다.

이것은 각 로터의 회전 방향을 지정하며, 이는 생성되는 반발 토크에 영향을 미칩니다. 값은 다음과 같아야 합니다:

- `1`: 반시계 방향 (CCW) 회전
- `-1`: 시계 방향 (CW) 회전

쿼드콥터의 경우, 호버 시 반발 토크를 상쇄하기 위해 일반적인 구성은 `[1, -1, 1, -1]`과 같이 교대로 배치하는 것입니다.

### 예시

```python
# 교대 로터 방향을 가진 쿼드콥터
rotor_directions = [1, -1, 1, -1]

# 헥사콥터
rotor_directions = [1, -1, 1, -1, 1, -1]
```

#### NOTE
길이는 actuators 구성에서 정의된 총 추진기 수와 일치해야 하며, 그렇지 않으면 초기화 중에 `ValueError`가 발생합니다.

### *class* isaaclab_contrib.assets.MultirotorData

Bases: [`ArticulationData`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationData)

멀티로터 관절을 위한 데이터 컨테이너.

이 클래스는 기본 [`ArticulationData`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationData) 컨테이너를 확장하여 추진기 상태, 추력 목표, 계산된 힘과 같은 멀티로터-specific 데이터를 포함합니다. 이는 멀티로터 차량의 모니터링 및 제어에 필요한 모든 상태 정보에 접근하는 방법을 제공합니다.

이 데이터 컨테이너는 [`Multirotor`](#isaaclab_contrib.assets.Multirotor) 클래스에 의해 자동으로 생성 및 관리됩니다. 사용자는 일반적으로 [`Multirotor.data`](#isaaclab_contrib.assets.Multirotor) 속성을 통해 이 데이터에 접근합니다.

#### NOTE
모든 텐서 속성은 `(num_instances, num_thrusters)` 형태를 가집니다. 여기서 `num_instances`는 환경 인스턴스 수이고, `num_thrusters`는 멀티로터당 총 추진기 수입니다.

#### SEE ALSO
- [`ArticulationData`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationData): 기본 관절 데이터 컨테이너
- [`Multirotor`](#isaaclab_contrib.assets.Multirotor): 멀티로터 자산 클래스

**속성:**

| [`thruster_names`](#isaaclab_contrib.assets.MultirotorData.thruster_names)                                                 | 멀티로터의 스러스터 이름 목록입니다.                                                                |
|----------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [`default_thruster_rps`](#isaaclab_contrib.assets.MultirotorData.default_thruster_rps)                                     | 모든 스러스터의 기본 스러스터 RPM(초당 회전수) 상태입니다.                                                  |
| [`applied_torque`](#isaaclab_contrib.assets.MultirotorData.applied_torque)                                                 | 액추에이터 모델에서 적용된 조인트 토크(클리핑 후)입니다.                                                    |
| [`body_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_acc_w)                                                         | [`body_com_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_com_acc_w)와 동일합니다.                     |
| [`body_ang_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_ang_acc_w)                                                 | [`body_com_ang_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_com_ang_acc_w)와 동일합니다.               |
| [`body_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_ang_vel_w)                                                 | [`body_com_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_com_ang_vel_w)와 동일합니다.               |
| [`body_com_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_com_acc_w)                                                 | 모든 바디의 질량 중심 가속도 `[lin_acc, ang_acc]`입니다.                                                    |
| [`body_com_ang_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_com_ang_acc_w)                                         | 시뮬레이션 월드 프레임에서 모든 바디의 각 가속도입니다.                                                    |
| [`body_com_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_com_ang_vel_w)                                         | 시뮬레이션 월드 프레임에서 모든 바디의 각 속도입니다.                                                       |
| [`body_com_lin_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_com_lin_acc_w)                                         | 시뮬레이션 월드 프레임에서 모든 바디의 선형 가속도입니다.                                                  |
| [`body_com_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_com_lin_vel_w)                                         | 시뮬레이션 월드 프레임에서 모든 바디의 선형 속도입니다.                                                    |
| [`body_com_pos_b`](#isaaclab_contrib.assets.MultirotorData.body_com_pos_b)                                                 | 각각의 링크 프레임에 있는 모든 바디의 질량 중심 위치입니다.                                                |
| [`body_com_pos_w`](#isaaclab_contrib.assets.MultirotorData.body_com_pos_w)                                                 | 시뮬레이션 월드 프레임에서 모든 바디의 위치입니다.                                                          |
| [`body_com_pose_b`](#isaaclab_contrib.assets.MultirotorData.body_com_pose_b)                                               | 각각의 바디의 링크 프레임에서 질량 중심 포즈 `[pos, quat]`입니다.                                            |
| [`body_com_pose_w`](#isaaclab_contrib.assets.MultirotorData.body_com_pose_w)                                               | 시뮬레이션 월드 프레임에서 바디의 질량 중심 포즈 `[pos, quat]`입니다.                                        |
| [`body_com_quat_b`](#isaaclab_contrib.assets.MultirotorData.body_com_quat_b)                                               | 각각의 링크 프레임에서 모든 바디의 관성 주축의 방향 (w, x, y, z)입니다.                                      |
| [`body_com_quat_w`](#isaaclab_contrib.assets.MultirotorData.body_com_quat_w)                                               | 시뮬레이션 월드 프레임에서 모든 바디의 관성 주축의 방향 (w, x, y, z)입니다.                                  |
| [`body_com_state_w`](#isaaclab_contrib.assets.MultirotorData.body_com_state_w)                                             | 시뮬레이션 월드 프레임에서 모든 바디의 질량 중심 상태 [pos, quat, lin_vel, ang_vel]입니다.                   |
| [`body_com_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_com_vel_w)                                                 | 시뮬레이션 월드 프레임에서 바디의 질량 중심 속도 `[lin_vel, ang_vel]`입니다.                                |
| [`body_incoming_joint_wrench_b`](#isaaclab_contrib.assets.MultirotorData.body_incoming_joint_wrench_b)                     | 부모 바디 프레임에서 부모 바디에서 자식 바디로 적용된 조인트 반응 렌치입니다.                               |
| [`body_lin_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_lin_acc_w)                                                 | [`body_com_lin_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_com_lin_acc_w)와 동일합니다.               |
| [`body_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_lin_vel_w)                                                 | [`body_com_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_com_lin_vel_w)와 동일합니다.               |
| [`body_link_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_link_ang_vel_w)                                       | 시뮬레이션 월드 프레임에서 모든 바디의 각 속도입니다.                                                      |
| [`body_link_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_link_lin_vel_w)                                       | 시뮬레이션 월드 프레임에서 모든 바디의 선형 속도입니다.                                                    |
| [`body_link_pos_w`](#isaaclab_contrib.assets.MultirotorData.body_link_pos_w)                                               | 시뮬레이션 월드 프레임에서 모든 바디의 위치입니다.                                                          |
| [`body_link_pose_w`](#isaaclab_contrib.assets.MultirotorData.body_link_pose_w)                                             | 시뮬레이션 월드 프레임에서 바디 링크 포즈 `[pos, quat]`입니다.                                              |
| [`body_link_quat_w`](#isaaclab_contrib.assets.MultirotorData.body_link_quat_w)                                             | 시뮬레이션 월드 프레임에서 모든 바디의 방향 (w, x, y, z)입니다.                                              |
| [`body_link_state_w`](#isaaclab_contrib.assets.MultirotorData.body_link_state_w)                                           | 시뮬레이션 월드 프레임에서 모든 바디의 링크 프레임 상태 [pos, quat, lin_vel, ang_vel]입니다.                 |
| [`body_link_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_link_vel_w)                                               | 시뮬레이션 월드 프레임에서 바디 링크 속도 `[lin_vel, ang_vel]`입니다.                                       |
| [`body_names`](#isaaclab_contrib.assets.MultirotorData.body_names)                                                         | 시뮬레이션 뷰에서 파싱된 순서에 따른 바디 이름입니다.                                                       |
| [`body_pos_w`](#isaaclab_contrib.assets.MultirotorData.body_pos_w)                                                         | [`body_link_pos_w`](#isaaclab_contrib.assets.MultirotorData.body_link_pos_w)와 동일합니다.                   |
| [`body_pose_w`](#isaaclab_contrib.assets.MultirotorData.body_pose_w)                                                       | [`body_link_pose_w`](#isaaclab_contrib.assets.MultirotorData.body_link_pose_w)와 동일합니다.                 |
| [`body_quat_w`](#isaaclab_contrib.assets.MultirotorData.body_quat_w)                                                       | [`body_link_quat_w`](#isaaclab_contrib.assets.MultirotorData.body_link_quat_w)와 동일합니다.                 |
| [`body_state_w`](#isaaclab_contrib.assets.MultirotorData.body_state_w)                                                     | 시뮬레이션 월드 프레임에서 모든 바디의 상태 [pos, quat, lin_vel, ang_vel]입니다.                             |
| [`body_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_vel_w)                                                         | [`body_com_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_com_vel_w)와 동일합니다.                       |
| [`com_pos_b`](#isaaclab_contrib.assets.MultirotorData.com_pos_b)                                                           | [`body_com_pos_b`](#isaaclab_contrib.assets.MultirotorData.body_com_pos_b)와 동일합니다.                     |
| [`com_quat_b`](#isaaclab_contrib.assets.MultirotorData.com_quat_b)                                                         | [`body_com_quat_b`](#isaaclab_contrib.assets.MultirotorData.body_com_quat_b)와 동일합니다.                   |
| [`computed_torque`](#isaaclab_contrib.assets.MultirotorData.computed_torque)                                               | 액추에이터 모델에서 계산된 조인트 토크(클리핑 전)입니다.                                                    |
| [`default_fixed_tendon_damping`](#isaaclab_contrib.assets.MultirotorData.default_fixed_tendon_damping)                     | 모든 고정 테너던의 기본 테너던 감쇠입니다.                                                                  |
| [`default_fixed_tendon_limit`](#isaaclab_contrib.assets.MultirotorData.default_fixed_tendon_limit)                         | 사용되지 않는 속성입니다.                                                                                   |
| [`default_fixed_tendon_limit_stiffness`](#isaaclab_contrib.assets.MultirotorData.default_fixed_tendon_limit_stiffness)     | 모든 고정 건의 기본 건 제한 강성.                                                            |
| [`default_fixed_tendon_offset`](#isaaclab_contrib.assets.MultirotorData.default_fixed_tendon_offset)                       | 모든 고정 건의 기본 건 오프셋.                                                                     |
| [`default_fixed_tendon_pos_limits`](#isaaclab_contrib.assets.MultirotorData.default_fixed_tendon_pos_limits)               | 모든 고정 건의 기본 건 위치 제한.                                                            |
| [`default_fixed_tendon_rest_length`](#isaaclab_contrib.assets.MultirotorData.default_fixed_tendon_rest_length)             | 모든 고정 건의 기본 건 휴식 길이.                                                                |
| [`default_fixed_tendon_stiffness`](#isaaclab_contrib.assets.MultirotorData.default_fixed_tendon_stiffness)                 | 모든 고정 건의 기본 건 강성.                                                                  |
| [`default_inertia`](#isaaclab_contrib.assets.MultirotorData.default_inertia)                                               | 발동기 내 모든 바디의 기본 관성.                                                         |
| [`default_joint_armature`](#isaaclab_contrib.assets.MultirotorData.default_joint_armature)                                 | 모든 관절의 기본 관절 암머처.                                                                           |
| [`default_joint_damping`](#isaaclab_contrib.assets.MultirotorData.default_joint_damping)                                   | 모든 관절의 기본 관절 감쇠.                                                                            |
| [`default_joint_dynamic_friction_coeff`](#isaaclab_contrib.assets.MultirotorData.default_joint_dynamic_friction_coeff)     | 모든 관절의 기본 관절 동적 마찰 계수.                                                       |
| [`default_joint_friction`](#isaaclab_contrib.assets.MultirotorData.default_joint_friction)                                 | 사용되지 않는 속성.                                                                                            |
| [`default_joint_friction_coeff`](#isaaclab_contrib.assets.MultirotorData.default_joint_friction_coeff)                     | 모든 관절의 기본 관절 정적 마찰 계수.                                                        |
| [`default_joint_limits`](#isaaclab_contrib.assets.MultirotorData.default_joint_limits)                                     | 사용되지 않는 속성.                                                                                            |
| [`default_joint_pos`](#isaaclab_contrib.assets.MultirotorData.default_joint_pos)                                           | 모든 관절의 기본 관절 위치.                                                                          |
| [`default_joint_pos_limits`](#isaaclab_contrib.assets.MultirotorData.default_joint_pos_limits)                             | 모든 관절의 기본 관절 위치 제한.                                                                    |
| [`default_joint_stiffness`](#isaaclab_contrib.assets.MultirotorData.default_joint_stiffness)                               | 모든 관절의 기본 관절 강성.                                                                          |
| [`default_joint_vel`](#isaaclab_contrib.assets.MultirotorData.default_joint_vel)                                           | 모든 관절의 기본 관절 속도.                                                                         |
| [`default_joint_viscous_friction_coeff`](#isaaclab_contrib.assets.MultirotorData.default_joint_viscous_friction_coeff)     | 모든 관절의 기본 관절 점성 마찰 계수.                                                       |
| [`default_mass`](#isaaclab_contrib.assets.MultirotorData.default_mass)                                                     | 발동기 내 모든 바디의 기본 질량.                                                            |
| [`default_root_state`](#isaaclab_contrib.assets.MultirotorData.default_root_state)                                         | 로컬 환경 프레임에서의 기본 루트 상태 `[pos, quat, lin_vel, ang_vel]`.                              |
| [`default_spatial_tendon_damping`](#isaaclab_contrib.assets.MultirotorData.default_spatial_tendon_damping)                 | 모든 공간 건의 기본 건 감쇠.                                                                  |
| [`default_spatial_tendon_limit_stiffness`](#isaaclab_contrib.assets.MultirotorData.default_spatial_tendon_limit_stiffness) | 모든 공간 건의 기본 건 제한 강성.                                                          |
| [`default_spatial_tendon_offset`](#isaaclab_contrib.assets.MultirotorData.default_spatial_tendon_offset)                   | 모든 공간 건의 기본 건 오프셋.                                                                   |
| [`default_spatial_tendon_stiffness`](#isaaclab_contrib.assets.MultirotorData.default_spatial_tendon_stiffness)             | 모든 공간 건의 기본 건 강성.                                                                |
| [`fixed_tendon_damping`](#isaaclab_contrib.assets.MultirotorData.fixed_tendon_damping)                                     | 시뮬레이션에 제공된 고정 건 감쇠.                                                                |
| [`fixed_tendon_limit`](#isaaclab_contrib.assets.MultirotorData.fixed_tendon_limit)                                         | 사용되지 않는 속성.                                                                                            |
| [`fixed_tendon_limit_stiffness`](#isaaclab_contrib.assets.MultirotorData.fixed_tendon_limit_stiffness)                     | 시뮬레이션에 제공된 고정 건 제한 강성.                                                        |
| [`fixed_tendon_names`](#isaaclab_contrib.assets.MultirotorData.fixed_tendon_names)                                         | 시뮬레이션 뷰에서 파싱된 순서대로의 고정 건 이름.                                                  |
| [`fixed_tendon_offset`](#isaaclab_contrib.assets.MultirotorData.fixed_tendon_offset)                                       | 시뮬레이션에 제공된 고정 건 오프셋.                                                                 |
| [`fixed_tendon_pos_limits`](#isaaclab_contrib.assets.MultirotorData.fixed_tendon_pos_limits)                               | 시뮬레이션에 제공된 고정 건 위치 제한.                                                        |
| [`fixed_tendon_rest_length`](#isaaclab_contrib.assets.MultirotorData.fixed_tendon_rest_length)                             | 시뮬레이션에 제공된 고정 건 휴식 길이.                                                            |
| [`fixed_tendon_stiffness`](#isaaclab_contrib.assets.MultirotorData.fixed_tendon_stiffness)                                 | 시뮬레이션에 제공된 고정 건 강성.                                                              |
| [`gear_ratio`](#isaaclab_contrib.assets.MultirotorData.gear_ratio)                                                         | 모터 토크를 관절 토크에 적용하는 데 사용되는 기어 비율.                                                 |
| [`heading_w`](#isaaclab_contrib.assets.MultirotorData.heading_w)                                                           | 베이스 프레임의 요 heading (라디안 단위).                                                           |
| [`joint_acc`](#isaaclab_contrib.assets.MultirotorData.joint_acc)                                                           | 모든 관절의 관절 가속도.                                                                               |
| [`joint_armature`](#isaaclab_contrib.assets.MultirotorData.joint_armature)                                                 | 시뮬레이션에 제공된 관절 암머처.                                                                      |
| [`joint_damping`](#isaaclab_contrib.assets.MultirotorData.joint_damping)                                                   | 시뮬레이션에 제공된 관절 감쇠.                                                                       |
| [`joint_dynamic_friction_coeff`](#isaaclab_contrib.assets.MultirotorData.joint_dynamic_friction_coeff)                     | 시뮬레이션에 제공된 관절 동적 마찰 계수.                                                  |
| [`joint_effort_limits`](#isaaclab_contrib.assets.MultirotorData.joint_effort_limits)                                       | 시뮬레이션에 제공된 관절 최대 노력.                                                                |
| [`joint_effort_target`](#isaaclab_contrib.assets.MultirotorData.joint_effort_target)                                       | 사용자가 명령한 관절 노력 목표.                                                                     |
| [`joint_friction`](#isaaclab_contrib.assets.MultirotorData.joint_friction)                                                 | 사용되지 않는 속성.                                                                                            |
| [`joint_friction_coeff`](#isaaclab_contrib.assets.MultirotorData.joint_friction_coeff)                                     | 시뮬레이션에 제공된 관절 정적 마찰 계수.                                                   |
| [`joint_limits`](#isaaclab_contrib.assets.MultirotorData.joint_limits)                                                     | 지원 종료된 속성입니다.                                                                                            |
| [`joint_names`](#isaaclab_contrib.assets.MultirotorData.joint_names)                                                       | 시뮬레이션 뷰에서 파싱된 순서대로 배열된 관절 이름입니다.                                                         |
| [`joint_pos`](#isaaclab_contrib.assets.MultirotorData.joint_pos)                                                           | 모든 관절의 관절 위치입니다.                                                                                  |
| [`joint_pos_limits`](#isaaclab_contrib.assets.MultirotorData.joint_pos_limits)                                             | 시뮬레이션에 제공된 관절 위치 제한값입니다.                                                               |
| [`joint_pos_target`](#isaaclab_contrib.assets.MultirotorData.joint_pos_target)                                             | 사용자가 명령한 관절 위치 목표값입니다.                                                                   |
| [`joint_stiffness`](#isaaclab_contrib.assets.MultirotorData.joint_stiffness)                                               | 시뮬레이션에 제공된 관절 강성입니다.                                                                     |
| [`joint_vel`](#isaaclab_contrib.assets.MultirotorData.joint_vel)                                                           | 모든 관절의 관절 속도입니다.                                                                                 |
| [`joint_vel_limits`](#isaaclab_contrib.assets.MultirotorData.joint_vel_limits)                                             | 시뮬레이션에 제공된 관절의 최대 속도입니다.                                                              |
| [`joint_vel_target`](#isaaclab_contrib.assets.MultirotorData.joint_vel_target)                                             | 사용자가 명령한 관절 속도 목표값입니다.                                                                   |
| [`joint_velocity_limits`](#isaaclab_contrib.assets.MultirotorData.joint_velocity_limits)                                   | 지원 종료된 속성입니다.                                                                                            |
| [`joint_viscous_friction_coeff`](#isaaclab_contrib.assets.MultirotorData.joint_viscous_friction_coeff)                     | 시뮬레이션에 제공된 관절 점성 마찰 계수입니다.                                                  |
| [`projected_gravity_b`](#isaaclab_contrib.assets.MultirotorData.projected_gravity_b)                                       | 기본 프레임에 대한 중력 방향의 투영값입니다.                                                              |
| [`root_ang_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_ang_vel_b)                                                 | [`root_com_ang_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_com_ang_vel_b)와 동일합니다.                     |
| [`root_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_ang_vel_w)                                                 | [`root_com_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_com_ang_vel_w)와 동일합니다.                     |
| [`root_com_ang_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_com_ang_vel_b)                                         | 기본 월드 프레임 기준 루트 질량 중심의 각속도입니다.                                                       |
| [`root_com_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_com_ang_vel_w)                                         | 시뮬레이션 월드 프레임 기준 루트 질량 중심의 각속도입니다.                                                 |
| [`root_com_lin_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_com_lin_vel_b)                                         | 기본 프레임 기준 루트 질량 중심의 선속도입니다.                                                              |
| [`root_com_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_com_lin_vel_w)                                         | 시뮬레이션 월드 프레임 기준 루트 질량 중심의 선속도입니다.                                                  |
| [`root_com_pos_w`](#isaaclab_contrib.assets.MultirotorData.root_com_pos_w)                                                 | 시뮬레이션 월드 프레임 기준 루트 질량 중심의 위치입니다.                                                         |
| [`root_com_pose_w`](#isaaclab_contrib.assets.MultirotorData.root_com_pose_w)                                               | 시뮬레이션 월드 프레임 기준 루트 질량 중심의 자세 `[pos, quat]`입니다.                                               |
| [`root_com_quat_w`](#isaaclab_contrib.assets.MultirotorData.root_com_quat_w)                                               | 시뮬레이션 월드 프레임 기준 루트 질량 중심의 방향 (w, x, y, z)입니다.                                         |
| [`root_com_state_w`](#isaaclab_contrib.assets.MultirotorData.root_com_state_w)                                             | 시뮬레이션 월드 프레임 기준 루트 질량 중심의 상태 `[pos, quat, lin_vel, ang_vel]`입니다.                            |
| [`root_com_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_com_vel_w)                                                 | 시뮬레이션 월드 프레임 기준 루트 질량 중심의 속도 `[lin_vel, ang_vel]`입니다.                                    |
| [`root_lin_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_lin_vel_b)                                                 | [`root_com_lin_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_com_lin_vel_b)와 동일합니다.                     |
| [`root_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_lin_vel_w)                                                 | [`root_com_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_com_lin_vel_w)와 동일합니다.                     |
| [`root_link_ang_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_link_ang_vel_b)                                       | 기본 월드 프레임 기준 루트 링크의 각속도입니다.                                                                 |
| [`root_link_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_link_ang_vel_w)                                       | 시뮬레이션 월드 프레임 기준 루트 링크의 각속도입니다.                                                           |
| [`root_link_lin_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_link_lin_vel_b)                                       | 기본 프레임 기준 루트 링크의 선속도입니다.                                                                        |
| [`root_link_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_link_lin_vel_w)                                       | 시뮬레이션 월드 프레임 기준 루트 링크의 선속도입니다.                                                                 |
| [`root_link_pos_w`](#isaaclab_contrib.assets.MultirotorData.root_link_pos_w)                                               | 시뮬레이션 월드 프레임 기준 루트 링크의 위치입니다.                                                           |
| [`root_link_pose_w`](#isaaclab_contrib.assets.MultirotorData.root_link_pose_w)                                             | 시뮬레이션 월드 프레임 기준 루트 링크의 자세 `[pos, quat]`입니다.                                                         |
| [`root_link_quat_w`](#isaaclab_contrib.assets.MultirotorData.root_link_quat_w)                                             | 시뮬레이션 월드 프레임 기준 루트 링크의 방향 (w, x, y, z)입니다.                                                   |
| [`root_link_state_w`](#isaaclab_contrib.assets.MultirotorData.root_link_state_w)                                           | 시뮬레이션 월드 프레임 기준 루트 링크의 상태 `[pos, quat, lin_vel, ang_vel]`입니다.                                           |
| [`root_link_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_link_vel_w)                                               | 시뮬레이션 월드 프레임 기준 루트 링크의 속도 `[lin_vel, ang_vel]`입니다.                                              |
| [`root_pos_w`](#isaaclab_contrib.assets.MultirotorData.root_pos_w)                                                         | [`root_link_pos_w`](#isaaclab_contrib.assets.MultirotorData.root_link_pos_w)와 동일합니다.                           |
| [`root_pose_w`](#isaaclab_contrib.assets.MultirotorData.root_pose_w)                                                       | [`root_link_pose_w`](#isaaclab_contrib.assets.MultirotorData.root_link_pose_w)와 동일합니다.                         |
| [`root_quat_w`](#isaaclab_contrib.assets.MultirotorData.root_quat_w)                                                       | [`root_link_quat_w`](#isaaclab_contrib.assets.MultirotorData.root_link_quat_w)와 동일합니다.                         |
| [`root_state_w`](#isaaclab_contrib.assets.MultirotorData.root_state_w)                                                     | 시뮬레이션 월드 프레임 기준 루트 상태 `[pos, quat, lin_vel, ang_vel]`입니다.                                           |
| [`root_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_vel_w)                                                         | [`root_com_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_com_vel_w)와 동일합니다.                             |
| [`soft_joint_pos_limits`](#isaaclab_contrib.assets.MultirotorData.soft_joint_pos_limits)                                   | 모든 관절의 소프트 관절 위치 제한값입니다.                                                                     |
| [`soft_joint_vel_limits`](#isaaclab_contrib.assets.MultirotorData.soft_joint_vel_limits)                                   | 모든 관절의 소프트 관절 속도 제한값입니다.                                                                      |
| [`spatial_tendon_damping`](#isaaclab_contrib.assets.MultirotorData.spatial_tendon_damping)                                 | 시뮬레이션에 제공되는 공간 타건 감쇠입니다.                                                              |
| [`spatial_tendon_limit_stiffness`](#isaaclab_contrib.assets.MultirotorData.spatial_tendon_limit_stiffness)                 | 시뮬레이션에 제공되는 공간 타건 제한 강성입니다.                                                      |
| [`spatial_tendon_names`](#isaaclab_contrib.assets.MultirotorData.spatial_tendon_names)                                     | 시뮬레이션 뷰에서 파싱된 순서에 따른 공간 타건 이름입니다.                                                |
| [`spatial_tendon_offset`](#isaaclab_contrib.assets.MultirotorData.spatial_tendon_offset)                                   | 시뮬레이션에 제공되는 공간 타건 오프셋입니다.                                                               |
| [`spatial_tendon_stiffness`](#isaaclab_contrib.assets.MultirotorData.spatial_tendon_stiffness)                             | 시뮬레이션에 제공되는 공간 타건 강성입니다.                                                            |
| [`thrust_target`](#isaaclab_contrib.assets.MultirotorData.thrust_target)                                                   | 사용자 또는 컨트롤러에 의해 명령된 추력 목표값입니다.                                                             |
| [`computed_thrust`](#isaaclab_contrib.assets.MultirotorData.computed_thrust)                                               | 클리핑 전 액추에이터 모델에서 계산된 추력입니다.                                                        |
| [`applied_thrust`](#isaaclab_contrib.assets.MultirotorData.applied_thrust)                                                 | 클리핑 후 액추에이터 모델에서 적용된 추력입니다.                                                          |

**Methods:**

| [`__init__`](#isaaclab_contrib.assets.MultirotorData.__init__)(root_physx_view, device)   | 자세한 정보를 초기화합니다.   |
|-------------------------------------------------------------------------------------------|--------------------------------------|

#### thruster_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

멀티로터의 트러스터 이름 목록입니다.

이 목록에는 트러스터 텐서에서 인덱싱에 사용되는 순서와 일치하는 모든 트러스터의 정렬된 이름이 포함되어 있습니다. 이름은 액추에이터 구성의 트러스터 이름 표현식과 일치하는 USD 몸체 프라임 이름을 기준으로 매칭됩니다.

### 예시

쿼드코프터의 경우 `["rotor_0", "rotor_1", "rotor_2", "rotor_3"]`

#### default_thruster_rps *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 트러스터의 기본 트러스터 RPS(회전당 초) 상태입니다. 크기는 (num_instances, num_thrusters)입니다.

이 수량은 `MultirotorCfg.init_state.rps` 매개변수를 통해 구성되며, 각 트러스터의 기준/호버 RPS를 나타냅니다. 이 수량은 리셋 작업 중 트러스터 상태를 초기화하는 데 사용됩니다.

호버링 멀티로터의 경우, 중력을 상쇄할 수 있는 충분한 총 추력을 생성해야 합니다.

### 예시

1kg 쿼드코프터의 4개 트러스터가 각각 110 RPS에서 2.5N의 추력을 생성할 때, 호버를 위한 기본값은 `[[110.0, 110.0, 110.0, 110.0]]`이 될 수 있습니다.

#### \_\_init_\_(root_physx_view: omni.physics.tensors.impl.api.ArticulationView, device: [str](https://docs.python.org/3/library/stdtypes.html#str))

자세한 정보를 초기화합니다.

* **매개변수:**
  * **root_physx_view** – 루트 자세한 정보 뷰.
  * **device** – 처리에 사용되는 디바이스.

#### applied_torque *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

액추에이터 모델에서 적용된 관절 토크(클리핑 후). 크기는 (num_instances, num_joints)입니다.

이 토크는 시뮬레이션에 설정되며, [`computed_torque`](#isaaclab_contrib.assets.MultirotorData.computed_torque)를 액추에이터 모델에 따라 클리핑한 후 적용됩니다.

#### *property* body_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_com_acc_w)과 동일합니다.

#### *property* body_ang_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_ang_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_com_ang_acc_w)과 동일합니다.

#### *property* body_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_com_ang_vel_w)과 동일합니다.

#### *property* body_com_acc_w

모든 몸체의 질량 중심 가속도 `[lin_acc, ang_acc]`입니다.
크기는 (num_instances, num_bodies, 6)입니다.

모든 값은 월드 기준으로 상대적입니다.

#### *property* body_com_ang_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 질량 중심 각 가속도입니다. 크기는 (num_instances, num_bodies, 3)입니다.

이 수량은 질량 중심 프레임에서의 관절 몸체의 각 가속도입니다.

#### *property* body_com_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 질량 중심 각 속도입니다. 크기는 (num_instances, num_bodies, 3)입니다.

이 수량은 질량 중심 프레임에서의 관절 몸체의 각 속도입니다.

#### *property* body_com_lin_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 질량 중심 선형 가속도입니다. 크기는 (num_instances, num_bodies, 3)입니다.

이 수량은 질량 중심 프레임에서의 관절 몸체의 선형 가속도입니다.

#### *property* body_com_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 질량 중심 선형 속도입니다. 크기는 (num_instances, num_bodies, 3)입니다.

이 수량은 질량 중심 프레임에서의 관절 몸체의 선형 속도입니다.

#### *property* body_com_pos_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

각 몸체의 해당 링크 프레임에서의 질량 중심 위치입니다. 크기는 (num_instances, num_bodies, 3)입니다.

이 수량은 몸체의 링크 프레임에 상대적인 질량 중심 위치입니다.

#### *property* body_com_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 위치입니다. 크기는 (num_instances, num_bodies, 3)입니다.

이 수량은 관절 몸체의 액터 프레임 위치입니다.

#### *property* body_com_pose_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

각 몸체의 해당 링크 프레임에서의 질량 중심 포즈 `[pos, quat]`. 크기는 (num_instances, 1, 7)입니다.

이 수량은 몸체의 링크 프레임에 상대적인 질량 중심 프레임의 포즈입니다.
방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_com_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 질량 중심 포즈 `[pos, quat]`. 크기는 (num_instances, num_bodies, 7)입니다.

이 수량은 월드에 상대적인 관절 링크의 질량 중심 프레임의 포즈입니다.
방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_com_quat_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

각 몸체의 해당 링크 프레임에서의 관성 주축 방향 (w, x, y, z). 크기는 (num_instances, num_bodies, 4)입니다.

이 수량은 몸체의 링크 프레임에 상대적인 관성 주축의 방향입니다.

#### *property* body_com_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 관성 주축 방향 (w, x, y, z). 크기는 (num_instances, num_bodies, 4)입니다.

이 수량은 관절 몸체의 액터 프레임의 방향입니다.

#### *property* body_com_state_w

시뮬레이션 월드 프레임에서 모든 몸체의 질량 중심 상태 [pos, quat, lin_vel, ang_vel]. 크기는 (num_instances, num_bodies, 13)입니다.

위치, 사원수 및 선형/각속도는 월드에 상대적인 몸체의 질량 중심 프레임의 값입니다. 질량 중심 프레임은 관성의 주축 방향이 아닌 링크 방향과 동일하다고 가정됩니다.

#### *property* body_com_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 질량 중심 속도 `[lin_vel, ang_vel]`. 크기는 (num_instances, num_bodies, 6)입니다.

이 수량은 월드에 상대적인 관절 링크의 질량 중심 프레임의 선형 및 각속도를 포함합니다.

#### *property* body_incoming_joint_wrench_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

부모 몸체에서 자식 몸체로 적용된 관절 반력 렌치 (부모 몸체 프레임). 크기는 (num_instances, num_bodies, 6)입니다. 모든 몸체 반력 렌치는 관절 루트 몸체에서 월드까지 포함됩니다.

관절 렌치에 대한 자세한 내용은 [`PhysX 설명서`_] 및 기본
[PhysX Tensor API](https://docs.omniverse.nvidia.com/kit/docs/omni_physics/latest/extensions/runtime/source/omni.physics.tensors/docs/api/python.html#omni.physics.tensors.impl.api.ArticulationView.get_link_incoming_joint_force)를 참조하십시오.

#### *property* body_lin_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_lin_acc_w`](#isaaclab_contrib.assets.MultirotorData.body_com_lin_acc_w)과 동일합니다.

#### *property* body_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_com_lin_vel_w)와 동일합니다.

#### *property* body_link_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 각속도입니다. 형태는 (num_instances, num_bodies, 3)입니다.

이 양은 월드를 기준으로 한 관절 몸체의 질량중심 프레임의 각속도입니다.

#### *property* body_link_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 선속도입니다. 형태는 (num_instances, num_bodies, 3)입니다.

이 양은 월드를 기준으로 한 관절 몸체의 질량중심 프레임의 선속도입니다.

#### *property* body_link_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 위치입니다. 형태는 (num_instances, num_bodies, 3)입니다.

이 양은 월드를 기준으로 한 관절 몸체의 액터 프레임의 위치입니다.

#### *property* body_link_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 몸체 링크 자세 `[pos, quat]`입니다.
형태는 (num_instances, num_bodies, 7)입니다.

이 양은 월드를 기준으로 한 관절 링크의 액터 프레임의 자세입니다.
방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_link_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 몸체의 방향 (w, x, y, z)입니다. 형태는 (num_instances, num_bodies, 4)입니다.

이 양은 월드를 기준으로 한 관절 몸체의 액터 프레임의 방향입니다.

#### *property* body_link_state_w

시뮬레이션 월드 프레임에서 모든 몸체의 링크 프레임 상태 `[pos, quat, lin_vel, ang_vel]`입니다.
형태는 (num_instances, num_bodies, 13)입니다.

위치, 사원수, 선속도 및 각속도는 월드를 기준으로 한 몸체의 링크 프레임에 대한 값입니다.

#### *property* body_link_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 몸체 링크 속도 `[lin_vel, ang_vel]`입니다.
형태는 (num_instances, num_bodies, 6)입니다.

이 양은 월드를 기준으로 한 관절 링크의 액터 프레임의 선속도 및 각속도를 포함합니다.

#### body_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

시뮬레이션 뷰에서 파싱된 순서대로의 몸체 이름입니다.

#### *property* body_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_link_pos_w`](#isaaclab_contrib.assets.MultirotorData.body_link_pos_w)와 동일합니다.

#### *property* body_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_link_pose_w`](#isaaclab_contrib.assets.MultirotorData.body_link_pose_w)와 동일합니다.

#### *property* body_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_link_quat_w`](#isaaclab_contrib.assets.MultirotorData.body_link_quat_w)와 동일합니다.

#### *property* body_state_w

시뮬레이션 월드 프레임에서 모든 몸체의 상태 [pos, quat, lin_vel, ang_vel]입니다.
형태는 (num_instances, num_bodies, 13)입니다.

위치와 사원수는 모든 관절 링크의 액터 프레임을 기준으로 하며, 선속도 및 각속도는 관절 링크의 질량중심 프레임을 기준으로 합니다.

#### *property* body_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_vel_w`](#isaaclab_contrib.assets.MultirotorData.body_com_vel_w)와 동일합니다.

#### *property* com_pos_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_pos_b`](#isaaclab_contrib.assets.MultirotorData.body_com_pos_b)와 동일합니다.

#### *property* com_quat_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_quat_b`](#isaaclab_contrib.assets.MultirotorData.body_com_quat_b)와 동일합니다.

#### computed_torque *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

액추에이터 모델에서 계산된 조인트 토크 (클리핑 전). 형태는 (num_instances, num_joints)입니다.

이 양은 클리핑이 적용되기 전 액추에이터 모드에서 출력된 원시 토크입니다.
액추에이터 모델 내부의 계산을 검사하고자 하는 사용자를 위해 노출됩니다.
예를 들어, 계산된 토크와 적용된 토크 사이의 차이로 학습 에이전트에 패널티를 줄 때 사용할 수 있습니다.

#### default_fixed_tendon_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테нд론의 기본 테드론 댐핑입니다. 형태는 (num_instances, num_fixed_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### *property* default_fixed_tendon_limit *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`default_fixed_tendon_pos_limits`](#isaaclab_contrib.assets.MultirotorData.default_fixed_tendon_pos_limits)를 사용하세요.

#### default_fixed_tendon_limit_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테드론의 기본 테드론 제한 강성입니다. 형태는 (num_instances, num_fixed_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_fixed_tendon_offset *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테드론의 기본 테드론 오프셋입니다. 형태는 (num_instances, num_fixed_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_fixed_tendon_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테드론의 기본 테드론 위치 제한입니다. 형태는 (num_instances, num_fixed_tendons, 2)입니다.

위치 제한은 [하한, 상한] 순서로 제공되며, 초기화 시 USD 스키마에서 파싱됩니다.

#### default_fixed_tendon_rest_length *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테드론의 기본 테드론 휴지 길이입니다. 형태는 (num_instances, num_fixed_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_fixed_tendon_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테드론의 기본 테드론 강성입니다. 형태는 (num_instances, num_fixed_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_inertia *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

아티큘레이션의 모든 몸체에 대한 기본 관성입니다. 형태는 (num_instances, num_bodies, 9)입니다.

관성 텐서는 질량중심을 기준으로 하며, 아티큘레이션 링크의 액터 프레임에 표현되어야 합니다.
값은 다음 순서로 저장됩니다
$[I_{xx}, I_{yx}, I_{zx}, I_{xy}, I_{yy}, I_{zy}, I_{xz}, I_{yz}, I_{zz}]$. 그러나 관성 텐서의 대칭성 때문에 행-주열 순서와 열-주열 순서는 동일합니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_joint_armature *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 조인트의 기본 조인트 암마입니다. 형태는 (num_instances, num_joints)입니다.

이 양은 액추에이터 모델의 [`isaaclab.actuators.ActuatorBaseCfg.armature`](../lab/isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.armature) 매개변수를 통해 구성됩니다.
매개변수의 값이 None인 경우, 초기화 시 USD 스키마에서 파싱된 값이 사용됩니다.

#### default_joint_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 조인트의 기본 조인트 댐핑입니다. 형태는 (num_instances, num_joints)입니다.

이 양은 액추에이터 모델의 [`isaaclab.actuators.ActuatorBaseCfg.damping`](../lab/isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.damping) 매개변수를 통해 구성됩니다.
매개변수의 값이 None인 경우, 초기화 시 USD 스키마에서 파싱된 값이 사용됩니다.

#### ATTENTION
기본 강성은 사용자가 구성한 값 또는 초기화 시 USD 스키마에서 파싱된 값입니다.
[`joint_damping`](#isaaclab_contrib.assets.MultirotorData.joint_damping)와 혼동해서는 안 됩니다. 이는 시뮬레이션에 설정된 값을 의미합니다.

#### default_joint_dynamic_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 조인트의 기본 조인트 동적 마찰 계수입니다. 형태는 (num_instances, num_joints)입니다.

이 양은 액추에이터 모델의
[`isaaclab.actuators.ActuatorBaseCfg.dynamic_friction`](../lab/isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.dynamic_friction) 매개변수를 통해 구성됩니다.
매개변수의 값이 None인 경우, 초기화 시 USD 스키마에서 파싱된 값이 사용됩니다.

#### NOTE
Isaac Sim 4.5에서는 이 매개변수가 계수로 모델링됩니다. Isaac Sim 5.0부터는 노력(토크 또는 힘)으로 모델링됩니다.

#### *property* default_joint_friction *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`default_joint_friction_coeff`](#isaaclab_contrib.assets.MultirotorData.default_joint_friction_coeff)를 사용하세요.

#### default_joint_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 조인트의 기본 조인트 정적 마찰 계수입니다. 형태는 (num_instances, num_joints)입니다.

이 양은 액추에이터 모델의 [`isaaclab.actuators.ActuatorBaseCfg.friction`](../lab/isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.friction)
매개변수를 통해 구성됩니다. 매개변수의 값이 `None`이면, 초기화 시 USD 스키마에서 파싱된 값이 사용됩니다.

#### 참고 사항
Isaac Sim 4.5에서는 이 매개변수가 계수로 모델링됩니다. Isaac Sim 5.0 및 이후 버전에서는,
노력(토크 또는 힘)으로 모델링됩니다.

#### *property* default_joint_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`default_joint_pos_limits`](#isaaclab_contrib.assets.MultirotorData.default_joint_pos_limits)를 사용하세요.

#### default_joint_pos *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모든 관절의 기본 위치. 차원은 (num_instances, num_joints)입니다.

이 양은 [`isaaclab.assets.ArticulationCfg.init_state`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg.init_state) 매개변수를 통해 구성됩니다.

#### default_joint_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모든 관절의 기본 위치 제한. 차원은 (num_instances, num_joints, 2)입니다.

제한은 [lower, upper] 순서로 제공됩니다. 초기화 시 USD 스키마에서 파싱됩니다.

#### default_joint_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모든 관절의 기본 강성. 차원은 (num_instances, num_joints)입니다.

이 양은 액추에이터 모델의 [`isaaclab.actuators.ActuatorBaseCfg.stiffness`](../lab/isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.stiffness)
매개변수를 통해 구성됩니다. 매개변수의 값이 `None`이면, 초기화 시 USD 스키마에서 파싱된 값이 사용됩니다.

#### 경고
기본 강성은 사용자가 구성한 값 또는 USD 스키마에서 파싱된 값입니다. 시뮬레이션에 설정된 값인 [`joint_stiffness`](#isaaclab_contrib.assets.MultirotorData.joint_stiffness)와 혼동하지 마세요.

#### default_joint_vel *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모든 관절의 기본 속도. 차원은 (num_instances, num_joints)입니다.

이 양은 [`isaaclab.assets.ArticulationCfg.init_state`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg.init_state) 매개변수를 통해 구성됩니다.

#### default_joint_viscous_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모든 관절의 기본 점성 마찰 계수. 차원은 (num_instances, num_joints)입니다.

이 양은 액추에이터 모델의
[`isaaclab.actuators.ActuatorBaseCfg.viscous_friction`](../lab/isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.viscous_friction) 매개변수를 통해 구성됩니다. 매개변수의 값이 `None`이면,
초기화 시 USD 스키마에서 파싱된 값이 사용됩니다.

#### default_mass *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

아티큘레이션의 모든 바디의 기본 질량. 차원은 (num_instances, num_bodies)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_root_state *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

로컬 환경 프레임에서의 기본 루트 상태 `[pos, quat, lin_vel, ang_vel]`. 차원은 (num_instances, 13)입니다.

위치와 쿼터니언은 아티큘레이션 루트의 액터 프레임 기준입니다. 반면, 선형 및 각속도는 질량 중심 프레임 기준입니다.

이 양은 [`isaaclab.assets.ArticulationCfg.init_state`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg.init_state) 매개변수를 통해 구성됩니다.

#### default_spatial_tendon_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모든 공간 텐던의 기본 감쇠. 차원은 (num_instances, num_spatial_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_spatial_tendon_limit_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모든 공간 텐던의 기본 한계 강성. 차원은 (num_instances, num_spatial_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_spatial_tendon_offset *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모든 공간 텐던의 기본 오프셋. 차원은 (num_instances, num_spatial_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_spatial_tendon_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모든 공간 텐던의 기본 강성. 차원은 (num_instances, num_spatial_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### fixed_tendon_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 고정 텐던 감쇠. 차원은 (num_instances, num_fixed_tendons)입니다.

#### *property* fixed_tendon_limit *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`fixed_tendon_pos_limits`](#isaaclab_contrib.assets.MultirotorData.fixed_tendon_pos_limits)를 사용하세요.

#### fixed_tendon_limit_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 고정 텐던 한계 강성. 차원은 (num_instances, num_fixed_tendons)입니다.

#### fixed_tendon_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* = None

시뮬레이션 뷰에 의해 파싱된 순서의 고정 텐던 이름.

#### fixed_tendon_offset *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 고정 텐던 오프셋. 차원은 (num_instances, num_fixed_tendons)입니다.

#### fixed_tendon_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 고정 텐던 위치 제한. 차원은 (num_instances, num_fixed_tendons, 2)입니다.

#### fixed_tendon_rest_length *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 고정 텐던 휴식 길이. 차원은 (num_instances, num_fixed_tendons)입니다.

#### fixed_tendon_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 고정 텐던 강성. 차원은 (num_instances, num_fixed_tendons)입니다.

#### gear_ratio *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

모터 토크와 관절 토크 사이의 기어 비. 차원은 (num_instances, num_joints)입니다.

#### *property* heading_w

베이스 프레임의 요 heading (라디안 단위). 차원은 (num_instances,)입니다.

#### 참고 사항
이 양은 베이스 프레임의 전방 방향이 x-축 방향, 즉 $(1, 0, 0)$이라고 가정하여 계산됩니다.

#### *property* joint_acc

모든 관절의 가속도. 차원은 (num_instances, num_joints)입니다.

#### joint_armature *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 관절 관성 모멘트. 차원은 (num_instances, num_joints)입니다.

#### joint_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 관절 감쇠. 차원은 (num_instances, num_joints)입니다.

명시적 액추에이터의 경우, 해당 관절에 대한 값은 0입니다.

#### joint_dynamic_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 관절 동적 마찰 계수. 차원은 (num_instances, num_joints)입니다.

참고: Isaac Sim 4.5에서는 이 매개변수가 계수로 모델링됩니다. Isaac Sim 5.0 및 이후 버전에서는,
노력(토크 또는 힘)으로 모델링됩니다.

#### joint_effort_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 관절 최대 노력. 차원은 (num_instances, num_joints)입니다.

#### joint_effort_target *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

사용자가 명령한 관절 노력 목표. 차원은 (num_instances, num_joints)입니다.

암시적 액추에이터 모델의 경우, 목표는 시뮬레이션에 직접 설정됩니다.
명시적 액추에이터 모델의 경우, 목표는 관절 토크를 계산하는 데 사용됩니다([`applied_torque`](#isaaclab_contrib.assets.MultirotorData.applied_torque) 참고),
그리고 계산된 토크가 시뮬레이션에 설정됩니다.

#### *property* joint_friction *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`joint_friction_coeff`](#isaaclab_contrib.assets.MultirotorData.joint_friction_coeff)를 사용하세요.

#### joint_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* = None

시뮬레이션에 제공된 관절 정적 마찰 계수. 차원은 (num_instances, num_joints)입니다.

참고: Isaac Sim 4.5에서는 이 매개변수가 계수로 모델링됩니다. Isaac Sim 5.0 및 이후 버전에서는,
노력(토크 또는 힘)으로 모델링됩니다.

#### *property* joint_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`joint_pos_limits`](#isaaclab_contrib.assets.MultirotorData.joint_pos_limits)를 사용하세요.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

시뮬레이션 뷰에서 파싱된 순서대로의 조인트 이름.

#### *property* joint_pos

모든 조인트의 조인트 위치. 차원은 (num_instances, num_joints).

#### joint_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공된 조인트 위치 한계. 차원은 (num_instances, num_joints, 2).

한계는 $[lower, upper]$ 순서로 지정됨.

#### joint_pos_target *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

사용자가 명령한 조인트 위치 타깃. 차원은 (num_instances, num_joints).

암시적 액추에이터 모델에서는 타깃이 시뮬레이션에 직접 설정됨.
명시적 액추에이터 모델에서는 타깃이 조인트 토크를 계산하는 데 사용됨 ([`applied_torque`](#isaaclab_contrib.assets.MultirotorData.applied_torque) 참조),
그리고 그 토크가 시뮬레이션에 설정됨.

#### joint_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공된 조인트 강성. 차원은 (num_instances, num_joints).

명시적 액추에이터의 경우, 해당 조인트의 값은 0임.

#### *property* joint_vel

모든 조인트의 조인트 속도. 차원은 (num_instances, num_joints).

#### joint_vel_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공된 조인트 최대 속도. 차원은 (num_instances, num_joints).

#### joint_vel_target *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

사용자가 명령한 조인트 속도 타깃. 차원은 (num_instances, num_joints).

암시적 액추에이터 모델에서는 타깃이 시뮬레이션에 직접 설정됨.
명시적 액추에이터 모델에서는 타깃이 조인트 토크를 계산하는 데 사용됨 ([`applied_torque`](#isaaclab_contrib.assets.MultirotorData.applied_torque) 참조),
그리고 그 토크가 시뮬레이션에 설정됨.

#### *property* joint_velocity_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

지원 종료된 속성. 대신 [`joint_vel_limits`](#isaaclab_contrib.assets.MultirotorData.joint_vel_limits)를 사용하십시오.

#### joint_viscous_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공된 조인트 점성 마찰 계수. 차원은 (num_instances, num_joints).

#### *property* projected_gravity_b

기준 프레임에 대한 중력 방향의 투영. 차원은 (num_instances, 3).

#### *property* root_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

같음: [`root_com_ang_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_com_ang_vel_b).

#### *property* root_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

같음: [`root_com_ang_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_com_ang_vel_w).

#### *property* root_com_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 월드 프레임에서의 루트 질량 중심 각속도. 차원은 (num_instances, 3).

이 양은 articulation 루트의 질량 중심 프레임이 액터 프레임에 대한 각속도를 나타냄.

#### *property* root_com_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 각속도. 차원은 (num_instances, 3).

이 양은 루트 강체 질량 중심 프레임의 월드 기준 상대 각속도를 나타냄.

#### *property* root_com_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 프레임에서의 루트 질량 중심 선속도. 차원은 (num_instances, 3).

이 양은 articulation 루트의 질량 중심 프레임이 액터 프레임에 대한 선속도를 나타냄.

#### *property* root_com_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 선속도. 차원은 (num_instances, 3).

이 양은 루트 강체 질량 중심 프레임의 월드 기준 상대 선속도를 나타냄.

#### *property* root_com_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 위치. 차원은 (num_instances, 3).

이 양은 루트 강체의 액터 프레임이 월드 기준 상대 위치를 나타냄.

#### *property* root_com_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 포즈 `[pos, quat]`. 차원은 (num_instances, 7).

이 양은 articulation 루트의 질량 중심 프레임이 월드 기준 상대 포즈를 나타냄.
방향은 (w, x, y, z) 형식으로 제공됨.

#### *property* root_com_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 방향 (w, x, y, z). 차원은 (num_instances, 4).

이 양은 루트 강체의 액터 프레임이 월드 기준 상대 방향을 나타냄.

#### *property* root_com_state_w

시뮬레이션 월드 프레임에서의 루트 질량 중심 상태 `[pos, quat, lin_vel, ang_vel]`. 차원은 (num_instances, 13).

위치, 사원수, 선속도 및 각속도는 월드 기준 상대적인 articulation 루트 링크의 질량 중심 프레임을 나타냄.
질량 중심 프레임은 링크와 동일한 방향을 가지며, 주 관성과의 방향은 가정하지 않음.

#### *property* root_com_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 속도 `[lin_vel, ang_vel]`. 차원은 (num_instances, 6).

이 양은 월드 기준 상대적인 articulation 루트의 질량 중심 프레임의 선속도 및 각속도를 포함함.

#### *property* root_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

같음: [`root_com_lin_vel_b`](#isaaclab_contrib.assets.MultirotorData.root_com_lin_vel_b).

#### *property* root_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

같음: [`root_com_lin_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_com_lin_vel_w).

#### *property* root_link_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 월드 프레임에서의 루트 링크 각속도. 차원은 (num_instances, 3).

이 양은 articulation 루트의 액터 프레임이 액터 프레임에 대한 각속도를 나타냄 (참조 프레임 동일).

#### *property* root_link_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 각속도. 차원은 (num_instances, 3).

이 양은 루트 강체의 액터 프레임이 월드 기준 상대 각속도를 나타냄.

#### *property* root_link_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 프레임에서의 루트 링크 선속도. 차원은 (num_instances, 3).

이 양은 articulation 루트의 액터 프레임이 액터 프레임에 대한 선속도를 나타냄.

#### *property* root_link_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 선속도. 차원은 (num_instances, 3).

이 양은 루트 강체의 액터 프레임이 월드 기준 상대 선속도를 나타냄.

#### *property* root_link_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 위치. 차원은 (num_instances, 3).

이 양은 루트 강체의 액터 프레임이 월드 기준 상대 위치를 나타냄.

#### *property* root_link_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 포즈 `[pos, quat]`. 차원은 (num_instances, 7).

이 양은 articulation 루트의 액터 프레임이 월드 기준 상대 포즈를 나타냄.
방향은 (w, x, y, z) 형식으로 제공됨.

#### *property* root_link_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 방향 (w, x, y, z). 차원은 (num_instances, 4).

이 양은 루트 강체의 액터 프레임 방향을 나타냄.

#### *property* root_link_state_w

시뮬레이션 월드 프레임에서의 루트 상태 `[pos, quat, lin_vel, ang_vel]`. 차원은 (num_instances, 13).

위치, 사원수, 선속도 및 각속도는 월드 기준 상대적인 articulation 루트의 액터 프레임을 나타냄.

#### *property* root_link_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 속도 `[lin_vel, ang_vel]`. 차원은 (num_instances, 6).

이 양은 월드 기준 상대적인 articulation 루트의 액터 프레임의 선속도 및 각속도를 포함함.

#### *property* root_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

같음: [`root_link_pos_w`](#isaaclab_contrib.assets.MultirotorData.root_link_pos_w).

#### *property* root_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

같음: [`root_link_pose_w`](#isaaclab_contrib.assets.MultirotorData.root_link_pose_w).

#### *property* root_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

같음: [`root_link_quat_w`](#isaaclab_contrib.assets.MultirotorData.root_link_quat_w).

#### *property* root_state_w

시뮬레이션 월드 프레임에서 루트 상태 `[pos, quat, lin_vel, ang_vel]`. 모양은 (num_instances, 13)입니다.

위치와 쿼터니언은 월드 기준에서 액추에이터 루트의 액터 프레임에 대한 것입니다. 한편,
선형 및 각속도는 액추에이터 루트의 질량 중심을 기준으로 합니다.

#### *property* root_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_vel_w`](#isaaclab_contrib.assets.MultirotorData.root_com_vel_w)와 동일합니다.

#### soft_joint_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 소프트 관절 위치 제한. 모양은 (num_instances, num_joints, 2)입니다.

제한은 순서대로 $[lower, upper]$이며, 소프트 관절 위치 제한은
[`joint_pos_limits`](#isaaclab_contrib.assets.MultirotorData.joint_pos_limits)를 기준으로
[`soft_joint_pos_limit_factor`](../lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg.soft_joint_pos_limit_factor) 매개변수에 따라 계산된 서브 영역입니다.

관절 위치 제한이 $[lower, upper]$이고, 소프트 관절 위치 제한이 $[soft_lower, soft_upper]$라고 할 때, 소프트 관절 위치 제한은 다음과 같이 계산됩니다:

$$
soft\_lower = (lower + upper) / 2 - factor * (upper - lower) / 2
soft\_upper = (lower + upper) / 2 + factor * (upper - lower) / 2
$$

소프트 관절 위치 제한은 관절 제한 주변의 안전 영역을 지정하는 데 도움이 됩니다. 시뮬레이션에서는 사용되지 않지만, 학습 에이전트가 관절 위치가 제한을 위반하지 않도록 방지하는 데 유용합니다.

#### soft_joint_vel_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 소프트 관절 속도 제한. 모양은 (num_instances, num_joints)입니다.

액추에이터 모델에서 얻어집니다. 액추에이터 모델이 변수 속도 제한 모델을 가지고 있는 경우
[`joint_vel_limits`](#isaaclab_contrib.assets.MultirotorData.joint_vel_limits)와 다를 수 있습니다. 예를 들어, 변수 기어 비율 액추에이터 모델의 경우입니다.

#### spatial_tendon_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 공간 테endon 댐핑. 모양은 (num_instances, num_spatial_tendons)입니다.

#### spatial_tendon_limit_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 공간 테endon 제한 강성. 모양은 (num_instances, num_spatial_tendons)입니다.

#### spatial_tendon_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

시뮬레이션 뷰에서 파싱된 순서의 공간 테endon 이름.

#### spatial_tendon_offset *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 공간 테endon 오프셋. 모양은 (num_instances, num_spatial_tendons)입니다.

#### spatial_tendon_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 공간 테endon 강성. 모양은 (num_instances, num_spatial_tendons)입니다.

#### thrust_target *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

사용자 또는 컨트롤러가 명령한 스러스트 타겟. 모양은 `(num_instances, num_thrusters)`

이 양은 [`set_thrust_target()`](#isaaclab_contrib.assets.Multirotor.set_thrust_target) 메서드를 통해 설정된 타겟 스러스트 값 또는
RL 환경의 액션 항을 통해 설정된 타겟 스러스트 값을 포함합니다. 이러한 타겟은 스러스터 액추에이터 모델에서 처리되어 실제 적용 스러스트를 계산합니다.

단위는 액추에이터 모델 구성에 따라 달라집니다(일반적으로 힘의 경우 뉴턴, 회전 속도의 경우 RPM).

#### computed_thrust *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

클리핑 전에 액추에이터 모델에서 계산된 스러스트. 모양은 (num_instances, num_thrusters).

이 양은 어떤 클리핑 또는 포화도 적용되기 전에 스러스터 액추에이터 모델에서 계산된 스러스트 값을 포함합니다. 이는 액추에이터 다이내믹스(상승/하강 시간)에 기반한 "원하는" 스러스트를 나타내지만, 물리적 한계를 초과할 수 있습니다.

[`computed_thrust`](#isaaclab_contrib.assets.MultirotorData.computed_thrust)와 [`applied_thrust`](#isaaclab_contrib.assets.MultirotorData.applied_thrust)의 차이는 액추에이터가 한계에서 포화 상태에 있는지를 나타냅니다.

예시 사용법:
: 계산된 스러스트와 적용된 스러스트를 비교하여 액추에이터 포화 모니터링:
  <br/>
  ```python
  saturation = multirotor.data.computed_thrust - multirotor.data.applied_thrust
  is_saturated = saturation.abs() > 1e-6
  ```

#### applied_thrust *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

클리핑 후 액추에이터 모델에서 적용된 스러스트. 모양은 (num_instances, num_thrusters).

이 양은 액추에이터 모델 처리 후 시뮬레이션에 실제로 적용되는 최종 스러스트 값을 포함합니다. 여기에는 다음이 포함됩니다:

- 동적 응답(상승/하강 시간 상수)
- 스러스트 범위 제한에 대한 클리핑
- 기타 액추에이터 모델 제약 조건

이는 물리 시뮬레이션에서 멀티로터의 movimento에 영향을 미치는 "실제값" 스러스트입니다.
