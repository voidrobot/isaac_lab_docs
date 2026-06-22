# isaaclab.sim.schemas

옴니버스에서 사용되는 스키마를 위한 유틸리티를 포함하는 하위 모듈입니다.

PhysX 및 USD Physics용 USD 스키랩을 감싸서 Python에서 매개변수를 편리하게 설정할 수 있도록 하는 API를 제공합니다. 이렇게 하면 구성 객체가 스키마 속성을 정의하여 설정하고, 옴니버스 키트를 열고 USD 속성을 수동으로 설정하지 않고도 물리 매개변수를 쉽게 조정할 수 있습니다.

스키마는 다음 링크에서 정의됩니다:

* [UsdPhysics 스키마](https://openusd.org/dev/api/usd_physics_page_front.html)
* [PhysxSchema 스키마](https://docs.omniverse.nvidia.com/kit/docs/omni_usd_schema_physics/104.2/index.html)

로컬에서 스키마는 다음 파일에서 정의됩니다:

* `_isaac_sim/extsPhysics/omni.usd.schema.physics/plugins/UsdPhysics/resources/UsdPhysics/schema.usda`
* `_isaac_sim/extsPhysics/omni.usd.schema.physx/plugins/PhysxSchema/resources/generatedSchema.usda`

### 클래스

| [`ArticulationRootPropertiesCfg`](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg)   | 관절의 루트에 적용할 속성입니다.            |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| [`RigidBodyPropertiesCfg`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg)                 | 강체에 적용할 속성입니다.                           |
| [`CollisionPropertiesCfg`](#isaaclab.sim.schemas.CollisionPropertiesCfg)                 | 강체의 콜라이더에 적용할 속성입니다.              |
| [`MassPropertiesCfg`](#isaaclab.sim.schemas.MassPropertiesCfg)                           | 강체의 명시적 질량 속성을 정의하는 속성입니다. |
| [`JointDrivePropertiesCfg`](#isaaclab.sim.schemas.JointDrivePropertiesCfg)               | 조인트 구동 메커니즘을 정의하는 속성입니다.       |
| [`FixedTendonPropertiesCfg`](#isaaclab.sim.schemas.FixedTendonPropertiesCfg)             | 관절의 고정 텐던을 정의하는 속성입니다.         |
| [`DeformableBodyPropertiesCfg`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg)       | 변형 가능한 신체에 적용할 속성입니다.                      |

### 함수

| [`define_articulation_root_properties`](#isaaclab.sim.schemas.define_articulation_root_properties)(...[, stage])   | 입력 프림에 관절 루트 스키마를 적용하고 속성을 설정합니다.   |
|--------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`modify_articulation_root_properties`](#isaaclab.sim.schemas.modify_articulation_root_properties)(...[, stage])   | 관절 루트 프림의 PhysX 매개변수를 수정합니다.                         |
| [`define_rigid_body_properties`](#isaaclab.sim.schemas.define_rigid_body_properties)(prim_path, cfg)               | 입력 프림에 강체 스키마를 적용하고 속성을 설정합니다.          |
| [`modify_rigid_body_properties`](#isaaclab.sim.schemas.modify_rigid_body_properties)(prim_path, cfg)               | 강체 프림의 PhysX 매개변수를 수정합니다.                                 |
| [`activate_contact_sensors`](#isaaclab.sim.schemas.activate_contact_sensors)(prim_path[, ...])                     | 지정된 프림 경로 아래의 모든 강체에 접촉 센서를 활성화합니다.   |
| [`define_collision_properties`](#isaaclab.sim.schemas.define_collision_properties)(prim_path, cfg)                 | 입력 프림에 충돌 스키마를 적용하고 속성을 설정합니다.           |
| [`modify_collision_properties`](#isaaclab.sim.schemas.modify_collision_properties)(prim_path, cfg)                 | 콜라이더 프림의 PhysX 속성을 수정합니다.                                      |
| [`define_mass_properties`](#isaaclab.sim.schemas.define_mass_properties)(prim_path, cfg[, stage])                  | 입력 프림에 질량 스키마를 적용하고 속성을 설정합니다.                |
| [`modify_mass_properties`](#isaaclab.sim.schemas.modify_mass_properties)(prim_path, cfg[, stage])                  | 강체 프림의 질량 속성을 설정합니다.                              |
| [`modify_joint_drive_properties`](#isaaclab.sim.schemas.modify_joint_drive_properties)(prim_path, cfg)             | 조인트 프림의 PhysX 매개변수를 수정합니다.                                      |
| [`modify_fixed_tendon_properties`](#isaaclab.sim.schemas.modify_fixed_tendon_properties)(prim_path, cfg)           | 고정 텐던 연결 프림의 PhysX 매개변수를 수정합니다.                    |
| [`define_deformable_body_properties`](#isaaclab.sim.schemas.define_deformable_body_properties)(prim_path, cfg)     | 입력 프림에 변형 가능한 신체 스키마를 적용하고 속성을 설정합니다.     |
| [`modify_deformable_body_properties`](#isaaclab.sim.schemas.modify_deformable_body_properties)(prim_path, cfg)     | 변형 가능한 신체 프림의 PhysX 매개변수를 수정합니다.                            |

## 관절 루트

### *class* isaaclab.sim.schemas.ArticulationRootPropertiesCfg

관절의 루트에 적용할 속성입니다.

[`modify_articulation_root_properties()`](#isaaclab.sim.schemas.modify_articulation_root_properties)에 대한 자세한 내용은 해당 항목을 참조하십시오.

#### NOTE
값이 None인 경우 해당 속성은 수정되지 않습니다. 이는 속성의 일부만 설정하고 나머지는 그대로 유지하고자 할 때 유용합니다.

**속성:**

| [`articulation_enabled`](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg.articulation_enabled)                       | 관절을 활성화하거나 비활성화하는지 여부입니다.                                                                 |
|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| [`enabled_self_collisions`](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg.enabled_self_collisions)                 | 자체 충돌을 활성화하거나 비활성화하는지 여부입니다.                                                              |
| [`solver_position_iteration_count`](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg.solver_position_iteration_count) | 신체의 솔버 위치 반복 횟수입니다.                                                             |
| [`solver_velocity_iteration_count`](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg.solver_velocity_iteration_count) | 신체의 솔버 속도 반복 횟수입니다.                                                             |
| [`sleep_threshold`](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg.sleep_threshold)                                 | 액터가 슬립 상태로 전환될 수 있는 질량 정규화된 운동 에너지 임계값입니다.                             |
| [`stabilization_threshold`](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg.stabilization_threshold)                 | 관절이 안정화에 참여할 수 있는 질량 정규화된 운동 에너지 임계값입니다. |
| [`fix_root_link`](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg.fix_root_link)                                     | 관절의 루트 링크를 고정할지 여부입니다.                                                          |

#### articulation_enabled *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

관절을 활성화하거나 비활성화하는지 여부입니다.

#### enabled_self_collisions *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

자체 충돌을 활성화하거나 비활성화하는지 여부입니다.

#### solver_position_iteration_count *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

신체의 솔버 위치 반복 횟수입니다.

#### solver_velocity_iteration_count *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

신체의 솔버 속도 반복 횟수입니다.

#### sleep_threshold *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

액터가 슬립 상태로 전환될 수 있는 질량 정규화된 운동 에너지 임계값입니다.

#### stabilization_threshold *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

관절이 안정화에 참여할 수 있는 질량 정규화된 운동 에너지 임계값입니다.

#### fix_root_link *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

관절의 루트 링크를 고정할지 여부입니다.

* None으로 설정된 경우 루트 링크는 수정되지 않습니다.
* 관절에 이미 고정된 루트 링크가 있는 경우, 이 플래그는 고정 관절을 활성화하거나 비활성화합니다.
* 관절에 고정된 루트 링크가 없는 경우, 이 플래그는 월드 프레임과 루트 링크 사이에 이름이 “FixedJoint”인 고정 관절을 관절 프림 아래에 생성합니다.

#### NOTE
이 속성은 USD 스키마 속성이 아닙니다. [`modify_articulation_root_properties()`](#isaaclab.sim.schemas.modify_articulation_root_properties) 함수에서 처리됩니다.

### isaaclab.sim.schemas.define_articulation_root_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.ArticulationRootPropertiesCfg](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

입력 프림에 관절 루트 스키마를 적용하고 속성을 설정합니다.

속성이 어떻게 설정되는지에 대한 자세한 내용은 [`modify_articulation_root_properties()`](#isaaclab.sim.schemas.modify_articulation_root_properties)를 참조하십시오.

* **매개변수:**
  * **prim_path** – 어티큘레이션 루트 스키마를 적용할 프라임 경로.
  * **cfg** – 어티큘레이션 루트의 구성.
  * **stage** – 프라임을 찾을 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **예외 발생:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프라임 경로가 유효하지 않을 때.
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – 프라임이 이미 충돌하는 API 스키마를 가지고 있을 때.

### isaaclab.sim.schemas.modify_articulation_root_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.ArticulationRootPropertiesCfg](#isaaclab.sim.schemas.ArticulationRootPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

어티큘레이션 루트 프라임의 PhysX 매개변수를 수정합니다.

[어티큘레이션 루트](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/docs/Articulations.html)는 어티큘레이션 트리의 루트를 표시합니다. 플로팅 어티큘레이션의 경우, 이는 루트 바디에 있어야 합니다. 고정 어티큘레이션의 경우, 이 API는 세계에 고정된 루트 조인트의 직접 또는 간접 부모에 있을 수 있습니다.

스키마는 [ArticulationRootAPI](https://openusd.org/dev/api/class_usd_physics_articulation_root_a_p_i.html)와 [PhysxArticulationAPI](https://docs.omniverse.nvidia.com/kit/docs/omni_usd_schema_physics/104.2/class_physx_schema_physx_articulation_a_p_i.html)의 속성으로 구성됩니다. 후자는 어티큘레이션 루트의 PhysX 매개변수를 포함합니다.

속성은 어티큘레이션 루트 프라임에 적용됩니다. 일반적인 속성(예: 솔버 위치 및 속도 반복 횟수, 수면 임계값, 안정화 임계값)은 어티큘레이션의 모든 강성 바디의 강성 바디 스키마에 지정된 속성보다 우선합니다.

#### 참고
이 함수는 `apply_nested()`로 장식되어 입력 프라임 경로 아래의 모든 프라임(해당 프라임에 스키마가 적용된 프라임)에 속성을 설정합니다.

* **매개변수:**
  * **prim_path** – 어티큘레이션 루트의 프라임 경로.
  * **cfg** – 어티큘레이션 루트의 구성.
  * **stage** – 프라임을 찾을 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **반환값:**
  속성이 성공적으로 설정된 경우 True, 그렇지 않은 경우 False.
* **예외 발생:**
  [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 루트 프라임이 강성 바디가 아니며 고정 조인트를 생성해야 할 때.

## 강성 바디

### *class* isaaclab.sim.schemas.RigidBodyPropertiesCfg

강성 바디에 적용할 속성입니다.

[`modify_rigid_body_properties()`](#isaaclab.sim.schemas.modify_rigid_body_properties)에서 자세한 정보를 확인하세요.

#### 참고
값이 None인 경우 수정되지 않습니다. 이는 속성의 하위 집합만 설정하고 나머지는 그대로 유지하려는 경우에 유용합니다.

**속성:**

| [`rigid_body_enabled`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.rigid_body_enabled)                           | 강성 바디를 활성화하거나 비활성화할지 여부.                                                        |
|-------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| [`kinematic_enabled`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.kinematic_enabled)                             | 바디가 키네마틱인지 여부를 결정합니다.                                                    |
| [`disable_gravity`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.disable_gravity)                                 | 액터에 중력을 비활성화합니다.                                                                      |
| [`linear_damping`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.linear_damping)                                   | 바디의 선형 댐핑.                                                                        |
| [`angular_damping`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.angular_damping)                                 | 바디의 각도 댐핑.                                                                       |
| [`max_linear_velocity`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.max_linear_velocity)                         | 강성 바디의 최대 선형 속도 (m/s 단위).                                                  |
| [`max_angular_velocity`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.max_angular_velocity)                       | 강성 바디의 최대 각도 속도 (deg/s 단위).                                               |
| [`max_depenetration_velocity`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.max_depenetration_velocity)           | 솔버가 도입할 수 있는 최대 관통 해제 속도 (m/s 단위).                   |
| [`max_contact_impulse`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.max_contact_impulse)                         | 접촉에서 적용될 수 있는 임펄스의 제한.                                          |
| [`enable_gyroscopic_forces`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.enable_gyroscopic_forces)               | 강성 바디에 대한 자이로스코픽 힘 계산 활성화.                                         |
| [`retain_accelerations`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.retain_accelerations)                       | 서브스텝 간 힘/가속도 유지.                                                   |
| [`solver_position_iteration_count`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.solver_position_iteration_count) | 바디의 솔버 위치 반복 횟수.                                                      |
| [`solver_velocity_iteration_count`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.solver_velocity_iteration_count) | 바디의 솔버 속도 반복 횟수.                                                      |
| [`sleep_threshold`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.sleep_threshold)                                 | 액터가 수면 상태로 전환될 수 있는 질량 정규화된 운동 에너지 임계값.                      |
| [`stabilization_threshold`](#isaaclab.sim.schemas.RigidBodyPropertiesCfg.stabilization_threshold)                 | 액터가 안정화에 참여할 수 있는 질량 정규화된 운동 에너지 임계값. |

#### rigid_body_enabled *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

강성 바디를 활성화하거나 비활성화할지 여부.

#### kinematic_enabled *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

바디가 키네마틱인지 여부를 결정합니다.

키네마틱 바디는 애니메이션된 포즈 또는 사용자 정의 포즈를 통해 움직입니다. 시뮬레이션은 여전히 외부 운동에 기반하여 키네마틱 바디의 속도를 계산합니다.

키네마틱 바디에 대한 자세한 내용은 [문서](https://openusd.org/release/wp_rigid_body_physics.html#kinematic-bodies)를 참조하세요.

#### disable_gravity *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

액터에 중력을 비활성화합니다.

#### linear_damping *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

바디의 선형 댐핑.

#### angular_damping *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

바디의 각도 댐핑.

#### max_linear_velocity *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

강성 바디의 최대 선형 속도 (m/s 단위).

#### max_angular_velocity *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

강성 바디의 최대 각도 속도 (deg/s 단위).

#### max_depenetration_velocity *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

솔버가 도입할 수 있는 최대 관통 해제 속도 (m/s 단위).

#### max_contact_impulse *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

접촉에서 적용될 수 있는 임펄스의 제한.

#### enable_gyroscopic_forces *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

강성 바디에 대한 자이로스코픽 힘 계산 활성화.

#### retain_accelerations *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

서브스텝 간 힘/가속도 유지.

#### solver_position_iteration_count *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

바디의 솔버 위치 반복 횟수.

#### solver_velocity_iteration_count *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

바디의 솔버 속도 반복 횟수.

#### sleep_threshold *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

액터가 수면 상태로 전환될 수 있는 질량 정규화된 운동 에너지 임계값.

#### stabilization_threshold *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

안정화에 참여할 수 있는 액터의 질량 정규화된 운동 에너지 기준값 이하.

### isaaclab.sim.schemas.define_rigid_body_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.RigidBodyPropertiesCfg](#isaaclab.sim.schemas.RigidBodyPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

입력 프림에 강체 스키마를 적용하고 그 속성을 설정합니다.

속성 설정 방법에 대한 자세한 내용은 [`modify_rigid_body_properties()`](#isaaclab.sim.schemas.modify_rigid_body_properties)를 참조하십시오.

* **Parameters:**
  * **prim_path** – 강체 스키마를 적용할 프림 경로입니다.
  * **cfg** – 강체에 대한 구성입니다.
  * **stage** – 프림을 찾을 스테이지입니다. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **Raises:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림 경로가 유효하지 않을 때 발생합니다.
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – 프림이 이미 충돌하는 API 스키마를 가지고 있을 때 발생합니다.

### isaaclab.sim.schemas.modify_rigid_body_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.RigidBodyPropertiesCfg](#isaaclab.sim.schemas.RigidBodyPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

강체 프림의 PhysX 매개변수를 수정합니다.

[강체](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/docs/RigidBodyOverview.html)는 PhysX로 시뮬레이션할 수 있는 단일 신체로, 동적 또는 운동학적일 수 있습니다. 동적 신체는 힘과 충돌에 반응합니다. [운동학적 신체](https://openusd.org/release/wp_rigid_body_physics.html#kinematic-bodies)는 사용자에 의해 이동될 수 있지만 힘에는 반응하지 않습니다. 이동 가능한 정적 신체와 유사합니다.

이 스키마는 [RigidBodyAPI](https://openusd.org/dev/api/class_usd_physics_rigid_body_a_p_i.html)와 [PhysxRigidBodyAPI](https://docs.omniverse.nvidia.com/kit/docs/omni_usd_schema_physics/104.2/class_physx_schema_physx_rigid_body_a_p_i.html)에 속하는 속성으로 구성됩니다. 후자는 강체의 PhysX 매개변수를 포함합니다.

#### NOTE
이 함수는 `apply_nested()`로 데코레이트되어 입력 프림 경로 하위의 (해당 스키마가 적용된) 모든 프림에 속성을 설정합니다.

* **Parameters:**
  * **prim_path** – 강체의 프림 경로입니다.
  * **cfg** – 강체에 대한 구성입니다.
  * **stage** – 프림을 찾을 스테이지입니다. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **Returns:**
  속성이 성공적으로 설정되면 True, 그렇지 않으면 False입니다.

### isaaclab.sim.schemas.activate_contact_sensors(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), threshold: [float](https://docs.python.org/3/library/functions.html#float) = 0.0, stage: pxr.Usd.Stage = None)

지정된 프림 경로 하위의 모든 강체에 대해 접촉 센서를 활성화합니다.

이 함수는 지정된 프림 경로 하위의 모든 강체에 PhysX 접촉 보고 API를 추가합니다. 또한 접촉 센서가 접촉을 보고하는 힘의 임계값도 설정합니다. 접촉 보고 API는 강체에만 추가할 수 있습니다.

* **Parameters:**
  * **prim_path** – 접촉 센서를 검색하고 준비할 프림 경로입니다.
  * **threshold** – 접촉 센서의 임계값입니다. 기본값은 0.0입니다.
  * **stage** – 프림을 찾을 스테이지입니다. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **Raises:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 프림 경로가 유효하지 않을 때 발생합니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림 경로 하위에 강체가 없을 때 발생합니다.

## 충돌

### *class* isaaclab.sim.schemas.CollisionPropertiesCfg

강체에서 콜라이더에 적용할 속성입니다.

추가 정보는 [`modify_collision_properties()`](#isaaclab.sim.schemas.modify_collision_properties)를 참조하십시오.

#### NOTE
값이 None이면 수정되지 않습니다. 이는 속성의 하위 집합만 설정하고 나머지는 그대로 두고 싶을 때 유용합니다.

**Attributes:**

| [`collision_enabled`](#isaaclab.sim.schemas.CollisionPropertiesCfg.collision_enabled)                   | 충돌을 활성화하거나 비활성화 여부입니다.                                    |
|---------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`contact_offset`](#isaaclab.sim.schemas.CollisionPropertiesCfg.contact_offset)                         | 충돌 형태의 접촉 오프셋 (m 단위).                              |
| [`rest_offset`](#isaaclab.sim.schemas.CollisionPropertiesCfg.rest_offset)                               | 충돌 형태의 정지 오프셋 (m 단위).                                 |
| [`torsional_patch_radius`](#isaaclab.sim.schemas.CollisionPropertiesCfg.torsional_patch_radius)         | 비틀림 마찰을 적용하는 접촉 패치의 반지름 (m 단위).         |
| [`min_torsional_patch_radius`](#isaaclab.sim.schemas.CollisionPropertiesCfg.min_torsional_patch_radius) | 비틀림 마찰을 적용하는 접촉 패치의 최소 반지름 (m 단위). |

#### collision_enabled *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

충돌을 활성화하거나 비활성화 여부입니다.

#### contact_offset *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

충돌 형태의 접촉 오프셋 (m 단위).

충돌 감지기는 두 형태가 접촉 오프셋의 합보다 가까워질 때 즉시 접촉 점을 생성합니다. 이 값은 음수가 될 수 없으며, 이는 형태가 실제로 관통하기 전에 접촉 생성이 시작될 수 있음을 의미합니다.

#### rest_offset *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

충돌 형태의 정지 오프셋 (m 단위).

정지 오프셋은 형태가 정지 상태에서 다른 형태에 얼마나 가까운지를 나타냅니다. 정지 상태에서 두 개의 수직으로 쌓인 객체 사이의 거리는 그들의 정지 오프셋의 합입니다. 형태 쌍이 양의 정지 오프셋을 가질 경우, 정지 상태에서 공기 격자로 분리됩니다.

#### torsional_patch_radius *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

비틀림 마찰을 적용하는 접촉 패치의 반지름 (m 단위).

접촉 표면의 압축으로 인한 회전 마찰을 근사하는 데 사용됩니다. 반지름이 zero이면 비틀림 마찰이 적용되지 않습니다.

#### min_torsional_patch_radius *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

비틀림 마찰을 적용하는 접촉 패치의 최소 반지름 (m 단위).

### isaaclab.sim.schemas.define_collision_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.CollisionPropertiesCfg](#isaaclab.sim.schemas.CollisionPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

입력 프림에 충돌 스키마를 적용하고 그 속성을 설정합니다.

속성 설정 방법에 대한 자세한 내용은 [`modify_collision_properties()`](#isaaclab.sim.schemas.modify_collision_properties)를 참조하십시오.

* **Parameters:**
  * **prim_path** – 강체 스키마를 적용할 프림 경로입니다.
  * **cfg** – 콜라이더에 대한 구성입니다.
  * **stage** – 프림을 찾을 스테이지입니다. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림 경로가 유효하지 않을 때 발생합니다.

### isaaclab.sim.schemas.modify_collision_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.CollisionPropertiesCfg](#isaaclab.sim.schemas.CollisionPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

콜라이더 프림의 PhysX 속성을 수정합니다.

이 속성은 [UsdPhysics.CollisionAPI](https://openusd.org/dev/api/class_usd_physics_collision_a_p_i.html)와 [PhysxSchema.PhysxCollisionAPI](https://docs.omniverse.nvidia.com/kit/docs/omni_usd_schema_physics/104.2/class_physx_schema_physx_collision_a_p_i.html) 스키마를 기반으로 합니다. 속성에 대한 자세한 내용은 공식 문서를 참조하십시오.

이 매개변수를 조정하면 강체의 접촉 동작에 영향을 미칩니다. 조정 방법과 시뮬레이션에 미치는 영향에 대한 자세한 내용은 [PhysX 문서](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/docs/AdvancedCollisionDetection.html)를 참조하십시오.

#### NOTE
이 함수는 `apply_nested()`로 데코레이트되어 입력 프림 경로 하위의 (해당 스키마가 적용된) 모든 프림에 속성을 설정합니다.

* **Parameters:**
  * **prim_path** – 부모의 프림 경로입니다.
  * **cfg** – 콜라이더에 대한 구성입니다.
  * **stage** – 프림을 찾을 스테이지입니다. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **Returns:**
  속성이 성공적으로 설정되면 True, 그렇지 않으면 False입니다.

## 질량

### *class* isaaclab.sim.schemas.MassPropertiesCfg

강체의 명시적 질량 속성을 정의하는 속성입니다.

[`modify_mass_properties()`](#isaaclab.sim.schemas.modify_mass_properties)을 참조하여 자세히 알아보세요.

#### 참고 사항
값이 None이면 수정되지 않습니다. 이는 속성의 일부만 설정하고 나머지는 그대로 두려는 경우 유용합니다.

**속성:**

| [`mass`](#isaaclab.sim.schemas.MassPropertiesCfg.mass)       | 강체의 질량(kg 단위)입니다.        |
|--------------------------------------------------------------|--------------------------------------------|
| [`density`](#isaaclab.sim.schemas.MassPropertiesCfg.density) | 강체의 밀도(kg/m³ 단위)입니다. |

#### mass *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

강체의 질량(kg 단위)입니다.

#### 참고 사항
0이 아닌 경우 질량이 무시되고 밀도를 사용하여 질량이 계산됩니다.

#### density *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

강체의 밀도(kg/m³ 단위)입니다.

밀도는 강체의 질량을 간접적으로 정의합니다. 일반적으로 충돌 근사 방법을 통해 계산됩니다.

### isaaclab.sim.schemas.define_mass_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.MassPropertiesCfg](#isaaclab.sim.schemas.MassPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

입력된 prim에 질량 스키마를 적용하고 속성을 설정합니다.

속성이 어떻게 설정되는지 자세히 보려면 [`modify_mass_properties()`](#isaaclab.sim.schemas.modify_mass_properties)를 참조하세요.

* **매개변수:**
  * **prim_path** – 강체 스키마를 적용할 prim 경로.
  * **cfg** – 질량 속성에 대한 구성.
  * **stage** – prim을 찾을 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **오류:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – prim 경로가 유효하지 않을 때 발생합니다.

### isaaclab.sim.schemas.modify_mass_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.MassPropertiesCfg](#isaaclab.sim.schemas.MassPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

강체 prim의 질량 속성을 설정합니다.

이 속성은 UsdPhysics.MassAPI 스키마를 기반으로 합니다. 질량이 정의되지 않은 경우, 밀도를 사용하여 질량을 계산합니다. 하지만 이 경우, 강체의 충돌 근사치를 사용해 밀도를 계산합니다. 속성에 대한 자세한 내용은 다음을 참조하세요:
[문서](https://openusd.org/release/wp_rigid_body_physics.html#body-mass-properties).

#### 참고 사항
이 함수는 `apply_nested()`로 장식되어 입력된 prim 경로 아래에 스키마가 적용된 모든 prim(해당 스키마가 적용된 prim)에 속성을 설정합니다.

<!-- UsdPhysics.MassAPI: https://openusd.org/dev/api/class_usd_physics_mass_a_p_i.html -->
* **매개변수:**
  * **prim_path** – 강체 prim의 경로.
  * **cfg** – 질량 속성에 대한 구성.
  * **stage** – prim을 찾을 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **반환값:**
  속성이 성공적으로 설정된 경우 True, 그렇지 않은 경우 False.

## 조인트 드라이브

### *클래스* isaaclab.sim.schemas.JointDrivePropertiesCfg

조인의 드라이브 메커니즘을 정의하는 속성입니다.

자세한 내용은 [`modify_joint_drive_properties()`](#isaaclab.sim.schemas.modify_joint_drive_properties)를 참조하세요.

#### 참고 사항
값이 None이면 수정되지 않습니다. 이는 속성의 일부만 설정하고 나머지는 그대로 두려는 경우 유용합니다.

**속성:**

| [`drive_type`](#isaaclab.sim.schemas.JointDrivePropertiesCfg.drive_type)     | 적용할 조인트 드라이브 유형입니다.                                       |
|------------------------------------------------------------------------------|------------------------------------------------------------------|
| [`max_effort`](#isaaclab.sim.schemas.JointDrivePropertiesCfg.max_effort)     | 조인트에 적용할 수 있는 최대 노력(kg·m²/s² 단위)입니다. |
| [`max_velocity`](#isaaclab.sim.schemas.JointDrivePropertiesCfg.max_velocity) | 조인트의 최대 속도입니다.                                   |
| [`stiffness`](#isaaclab.sim.schemas.JointDrivePropertiesCfg.stiffness)       | 조인트 드라이브의 강성입니다.                                    |
| [`damping`](#isaaclab.sim.schemas.JointDrivePropertiesCfg.damping)           | 조인트 드라이브의 감쇠입니다.                                      |

#### drive_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['force', 'acceleration'] | [None](https://docs.python.org/3/library/constants.html#None)*

적용할 조인트 드라이브 유형입니다.

드라이브 유형이 “force”이면 조인트는 힘으로 구동됩니다. 드라이브 유형이 “acceleration”이면 조인트는 가속도로 구동됩니다(보통 키네마틱 조인트에 사용됨).

#### max_effort *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

조인트에 적용할 수 있는 최대 노력(kg·m²/s² 단위)입니다.

#### max_velocity *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

조인트의 최대 속도입니다.

단위는 조인트 모델에 따라 다릅니다:

* 선형 조인트의 경우, 단위는 m/s입니다.
* 각도 조인트의 경우, 단위는 rad/s입니다.

#### stiffness *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

조인트 드라이브의 강성입니다.

단위는 조인트 모델에 따라 다릅니다:

* 선형 조인트의 경우, 단위는 kg·m/s² (N/m)입니다.
* 각도 조인트의 경우, 단위는 kg·m²/s²/rad (N·m/rad)입니다.

#### damping *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

조인트 드라이브의 감쇠입니다.

단위는 조인트 모델에 따라 다릅니다:

* 선형 조인트의 경우, 단위는 kg·m/s (N·s/m)입니다.
* 각도 조인트의 경우, 단위는 kg·m²/s/rad (N·m·s/rad)입니다.

### isaaclab.sim.schemas.modify_joint_drive_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.JointDrivePropertiesCfg](#isaaclab.sim.schemas.JointDrivePropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

조인트 prim의 PhysX 매개변수를 수정합니다.

이 함수는 입력된 prim이 프리즘atic 또는 레볼루트 조인트인지 확인하고, 해당 조인트에 조인트 드라이브 스키마를 적용합니다. 조인트가 테이던트(즉, [PhysxTendonAxisAPI](https://docs.omniverse.nvidia.com/kit/docs/omni_usd_schema_physics/104.2/class_physx_schema_physx_tendon_axis_a_p_i.html) 스키마가 적용된 경우)인 경우, 조인트 드라이브 스키마는 적용되지 않습니다.

구성에 따라 이 메서드는 조인트 드라이브의 속성을 수정합니다. 이 속성은 [UsdPhysics.DriveAPI](https://openusd.org/dev/api/class_usd_physics_drive_a_p_i.html) 스키마를 기반으로 합니다. 속성에 대한 자세한 내용은 다음을 참조하세요:
공식 문서.

* **매개변수:**
  * **prim_path** – 조인트 드라이브 스키마를 적용할 prim 경로.
  * **cfg** – 조인트 드라이브에 대한 구성.
  * **stage** – prim을 찾을 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **반환값:**
  속성이 성공적으로 설정된 경우 True, 그렇지 않은 경우 False.
* **오류:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력된 prim 경로가 유효하지 않을 때 발생합니다.

## 고정 테이던트

### *클래스* isaaclab.sim.schemas.FixedTendonPropertiesCfg

관절의 고정 테이던트를 정의하는 속성입니다.

자세한 내용은 [`modify_fixed_tendon_properties()`](#isaaclab.sim.schemas.modify_fixed_tendon_properties)를 참조하세요.

#### 참고 사항
값이 None이면 수정되지 않습니다. 이는 속성의 일부만 설정하고 나머지는 그대로 두려는 경우 유용합니다.

**속성:**

| [`tendon_enabled`](#isaaclab.sim.schemas.FixedTendonPropertiesCfg.tendon_enabled)   | 테이던트를 활성화하거나 비활성화할지 여부입니다.                                        |
|-------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| [`stiffness`](#isaaclab.sim.schemas.FixedTendonPropertiesCfg.stiffness)             | 테이던트 길이에 작용하는 스프링 강성 항입니다.                            |
| [`damping`](#isaaclab.sim.schemas.FixedTendonPropertiesCfg.damping)                 | 테이던트 길이와 테이던트 길이 제한에 모두 작용하는 감쇠 항입니다. |
| [`limit_stiffness`](#isaaclab.sim.schemas.FixedTendonPropertiesCfg.limit_stiffness) | 테이던트 길이 제한에 작용하는 제한 강성 항입니다.                      |
| [`offset`](#isaaclab.sim.schemas.FixedTendonPropertiesCfg.offset)                   | 테이던트의 길이 오프셋 항입니다.                                              |
| [`rest_length`](#isaaclab.sim.schemas.FixedTendonPropertiesCfg.rest_length)         | 테이던트의 스프링 휴지 길이입니다.                                               |

#### tendon_enabled *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

테이던트를 활성화하거나 비활성화할지 여부입니다.

#### stiffness *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

테이던트 길이에 작용하는 스프링 강성 항입니다.

#### damping *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*
</translate>

힘이 작용하는 두 개의 텐던트 길이와 텐던트 길이 제한에 작용하는 감쇠 항.

#### limit_stiffness *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

텐던트의 길이 제한에 작용하는 강성 항.

#### offset *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

텐던트의 길이 오프셋 항.

텐던트에 대해 계산된 누적 길이에 추가할 양을 정의합니다. 이를 통해 애플리케이션은 텐던트를 짧게 하거나 길게 하여 작동시킬 수 있습니다.

#### rest_length *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

텐던트의 스프링 휴식 길이.

### isaaclab.sim.schemas.modify_fixed_tendon_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.FixedTendonPropertiesCfg](#isaaclab.sim.schemas.FixedTendonPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

고정 텐던트 어태치먼트 프림의 PhysX 파라미터를 수정합니다.

[고정 텐던트](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/_api_build/classPxArticulationFixedTendon.html)는 길이와 제한 제약을 통해 아티큘레이션 조인트의 여러 자유도를 연결하는 데 사용될 수 있습니다. 예를 들어, driven 및 수동 회전 조인트 사이의 등식 제약을 설정하는 데 사용할 수 있습니다.

스키마는 [PhysxTendonAxisRootAPI](https://docs.omniverse.nvidia.com/kit/docs/omni_usd_schema_physics/104.2/class_physx_schema_physx_tendon_axis_root_a_p_i.html) 스키마에 속하는 속성으로 구성됩니다.

#### 참고
이 함수는 `apply_nested()`로 장식되어 입력 프림 경로 아래에 적용된 스키마가 있는 모든 프림에 속성을 설정합니다.

* **매개변수:**
  * **prim_path** – 텐던트 어태치먼트의 프림 경로.
  * **cfg** – 텐던트 어태치먼트의 구성.
  * **stage** – 프림을 찾을 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **반환값:**
  속성이 성공적으로 설정된 경우 True, 그렇지 않으면 False.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 프림 경로가 유효하지 않은 경우.

## 변형 가능한 본체

### *class* isaaclab.sim.schemas.DeformableBodyPropertiesCfg

변형 가능한 본체에 적용할 속성.

변형 가능한 본체는 힘에 의해 변형될 수 있는 본체입니다. 이 구성을 사용하면 변형 가능한 본체의 속성, 즉 솔버 반복 횟수, 감쇠, 자체 충돌 등을 지정할 수 있습니다.

FEM 기반 변형 가능한 본체는 충돌 메시와 시뮬레이션 메시를 제공하여 생성됩니다. 충돌 메시는 충돌 감지에 사용되고, 시뮬레이션 메시는 시뮬레이션에 사용됩니다. 충돌 메시는 일반적으로 시뮬레이션 메시를 간소화한 버전입니다.

위와 관련하여, PhysX 팀은 시뮬레이션 메시와 충돌 메시를 직접 설정하거나(점 지정) 시뮬레이션 메시를 기반으로 충돌 메시를 간소화하는 API를 제공합니다. 간소화 과정은 충돌 메시를 리메시하고 대상 삼각형 수에 따라 간소화하는 과정을 포함합니다.

충돌 메시 포인트를 직접 지정하는 것은 일반적인 사용 사례가 아니므로, 시뮬레이션 메시를 기반으로 충돌 메시를 간소화하는 매개변수만 노출합니다. 충돌 메시 포인트를 제공하려는 경우, 저장소에 이슈를 열어 주시면 지원 추가를 검토하겠습니다.

[`modify_deformable_body_properties()`](#isaaclab.sim.schemas.modify_deformable_body_properties)를 참조하여 자세한 정보를 확인하세요.

#### 참고
값이 [`None`](https://docs.python.org/3/library/constants.html#None)인 경우 수정되지 않습니다. 이는 속성의 일부만 설정하고 나머지는 그대로 두는 경우에 유용합니다.

**속성:**

| [`deformable_enabled`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.deformable_enabled)                                                         | 변형 가능한 본체 활성화 여부.                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| [`kinematic_enabled`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.kinematic_enabled)                                                           | 운동학적 본체 활성화 여부.                                                                                    |
| [`self_collision`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.self_collision)                                                                 | 잔치 위치 거리를 기반으로 변형 가능한 본체의 자체 충돌을 활성화 또는 비활성화 여부.                             |
| [`self_collision_filter_distance`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.self_collision_filter_distance)                                 | 자체 충돌에 대한 접촉이 생성되기 전에 초과해야 하는 침투 값.                                                     |
| [`settling_threshold`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.settling_threshold)                                                         | 수면 감쇠가 추가로 적용되는 정점 속도(m/s) 임계값.                                                           |
| [`sleep_damping`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.sleep_damping)                                                                   | 정점 속도가 설정 임계값 이하로 떨어질 경우 적용되는 추가 감쇠 항의 계수.                                        |
| [`sleep_threshold`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.sleep_threshold)                                                               | 정점이 다음 단계에서 수면 후보가 되는 속도 임계값(m/s).                                                       |
| [`solver_position_iteration_count`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.solver_position_iteration_count)                               | 스텝당 솔버 위치 반복 횟수.                                                                               |
| [`vertex_velocity_damping`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.vertex_velocity_damping)                                               | 정점 속도에 대한 인공 감쇠 계수.                                                                           |
| [`simulation_hexahedral_resolution`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.simulation_hexahedral_resolution)                             | 시뮬레이션에 사용되는 육면체 메시의 목표 해상도.                                                            |
| [`collision_simplification`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.collision_simplification)                                             | 소프트 바디 생성 전 충돌 메시를 간소화할지 여부.                                                            |
| [`collision_simplification_remeshing`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.collision_simplification_remeshing)                         | 단순화 전 충돌 메시 리메시 여부.                                                                          |
| [`collision_simplification_remeshing_resolution`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.collision_simplification_remeshing_resolution)   | 리메시에 사용되는 해상도.                                                                                  |
| [`collision_simplification_target_triangle_count`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.collision_simplification_target_triangle_count) | 단순화에 사용되는 대상 삼각형 수.                                                                         |
| [`collision_simplification_force_conforming`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.collision_simplification_force_conforming)           | 단순화 시 출력 메시가 입력 메시에 강제로 적합하도록 할지 여부.                                            |
| [`contact_offset`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.contact_offset)                                                                 | 충돌 형태의 접촉 오프셋(단위: m).                                                                         |
| [`rest_offset`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.rest_offset)                                                                       | 충돌 형태의 휴식 오프셋(단위: m).                                                                       |
| [`max_depenetration_velocity`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.max_depenetration_velocity)                                         | 솔버에 의해 도입될 수 있는 최대 관통 해제 속도(단위: m/s).                                                   |

#### self_collision_filter_distance *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

자체 충돌에 대한 접촉이 생성되기 전에 초과되어야 하는 관통 값입니다.

이 매개변수는 [`rest_offset`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.rest_offset) 값의 두 배보다 크거나 같아야 합니다.

이 값은 [`self_collision`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.self_collision)이 활성화된 경우에만 효과가 있습니다.

#### settling_threshold *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

속도 감쇠 외에도 슬립 감쇠가 적용되는 정점 속도 임계값(단위: m/s)

#### sleep_damping *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

정점 속도가 설정 임계값 미만으로 떨어질 경우 적용되는 추가 감쇠 항의 계수입니다.

#### sleep_threshold *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

다음 단계에서 정점이 슬립 후보가 되기 위한 속도 임계값(단위: m/s)

#### solver_position_iteration_count *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

단계당 솔버 위치 반복 횟수. 범위는 [1,255]

#### vertex_velocity_damping *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

정점 속도에 대한 인공 감쇠 계수입니다.

이 매개변수는 deformable body에 대한 공기 저항의 효과를 근사하는 데 사용할 수 있습니다.

#### simulation_hexahedral_resolution *: [int](https://docs.python.org/3/library/functions.html#int)*

시뮬레이션에 사용되는 육각형 메시의 대상 해상도. 기본값은 10입니다.

#### NOTE
사용자가 시뮬레이션 메시 포인트를 직접 제공하는 경우 이 값은 무시됩니다. 그러나 대부분의 사용자가 시뮬레이션 메시 포인트를 직접 제공하지 않을 것이라고 가정합니다. 시뮬레이션 메시 포인트를 직접 제공하려면 이 값을 [`None`](https://docs.python.org/3/library/constants.html#None)으로 설정하세요.

#### collision_simplification *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소프트 바디를 생성하기 전에 충돌 메시를 간소화할지 여부. 기본값은 True입니다.

#### NOTE
사용자가 시뮬레이션 메시 포인트를 직접 제공하는 경우 이 플래그는 무시됩니다. 그러나 대부분의 사용자가 시뮬레이션 메시 포인트를 직접 제공하지 않을 것이라고 가정합니다. 따라서 이 플래그는 기본적으로 활성화되어 있습니다.

시뮬레이션 메시 포인트를 직접 제공하려면 이 플래그를 False로 설정하세요.

#### collision_simplification_remeshing *: [bool](https://docs.python.org/3/library/functions.html#bool)*

간소화 전에 충돌 메시를 재메싱해야 하는지 여부. 기본값은 True입니다.

이 매개변수는 [`collision_simplification`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.collision_simplification)이 False인 경우 무시됩니다.

#### collision_simplification_remeshing_resolution *: [int](https://docs.python.org/3/library/functions.html#int)*

재메싱에 사용되는 해상도. 기본값은 0으로, 이는 휴리스틱을 사용하여 해상도를 결정함을 의미합니다.

이 매개변수는 [`collision_simplification_remeshing`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.collision_simplification_remeshing)이 False인 경우 무시됩니다.

#### collision_simplification_target_triangle_count *: [int](https://docs.python.org/3/library/functions.html#int)*

간소화에 사용되는 대상 삼각형 수. 기본값은 0으로, 이는 [`simulation_hexahedral_resolution`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.simulation_hexahedral_resolution)을 기반으로 한 휴리스틱을 사용하여 대상 수를 결정함을 의미합니다.

이 매개변수는 [`collision_simplification`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.collision_simplification)이 False인 경우 무시됩니다.

#### collision_simplification_force_conforming *: [bool](https://docs.python.org/3/library/functions.html#bool)*

간소화가 출력 메시를 입력 메시에 맞게 강제해야 하는지 여부. 기본값은 True입니다.

이 플래그는 충돌 메시를 생성하기 위해 사용되는 테트라헤드라이저가 삼각형 메시에 맞게 테트라헤드를 생성해야 함을 나타냅니다. False인 경우, 간소화기는 테트라헤드라이저의 출력을 사용합니다.

이 매개변수는 [`collision_simplification`](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg.collision_simplification)이 False인 경우 무시됩니다.

#### contact_offset *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

충돌 형태의 접촉 오프셋(단위: m).

충돌 감지기는 두 형태의 접촉 오프셋의 합보다 가까워지는 즉시 접촉 점을 생성합니다. 이 값은 음이 아니어야 하며, 이는 접촉 생성이 형태가 실제로 관통하기 전에 시작될 수 있음을 의미합니다.

#### rest_offset *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

충돌 형태의 휴식 오프셋(단위: m).

휴식 오프셋은 형태가 휴식 상태에서 다른 형태에 얼마나 가까워지는지를 정량화합니다. 휴식 상태에서 두 개의 수직으로 쌓인 물체 사이의 거리는 그들의 휴식 오프셋의 합입니다. 형태 쌍이 양의 휴식 오프셋을 가지면, 형태는 공기 간격으로 인해 휴식 상태에서 분리됩니다.

#### max_depenetration_velocity *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

솔버가 도입할 수 있는 최대 관통 해제 속도(단위: m/s).

### isaaclab.sim.schemas.define_deformable_body_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.DeformableBodyPropertiesCfg](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

입력 프리미에 deformable body 스키마를 적용하고 해당 속성을 설정합니다.

속성이 어떻게 설정되는지에 대한 자세한 내용은 [`modify_deformable_body_properties()`](#isaaclab.sim.schemas.modify_deformable_body_properties)를 참조하세요.

#### NOTE
입력 프리미가 메시가 아닌 경우, 이 함수는 프리미를 traversing하여 그 아래에 있는 첫 번째 메시를 찾습니다. 메시가 없거나 여러 개의 메시가 발견되면 오류가 발생합니다. 이는 deformable body 스키마가 단일 메시에만 적용될 수 있기 때문입니다.

* **매개변수:**
  * **prim_path** – deformable body 스키마를 적용할 프리미 경로.
  * **cfg** – deformable body에 대한 구성.
  * **stage** – 프리미를 찾을 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프리미 경로가 유효하지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프리미에 메시가 없거나 여러 개의 메시가 있을 때.

### isaaclab.sim.schemas.modify_deformable_body_properties(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [schemas_cfg.DeformableBodyPropertiesCfg](#isaaclab.sim.schemas.DeformableBodyPropertiesCfg), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

deformable body 프리미의 PhysX 매개변수를 수정합니다.

[deformable body](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/docs/SoftBodies.html)는 PhysX로 시뮬레이션할 수 있는 단일 물체입니다. 강성 물체와 달리, deformable body는 메시의 노드에 대한 상대적 움직임을 지원합니다. 따라서 적용된 힘에 의한 변형을 시뮬레이션하는 데 사용될 수 있습니다.

PhysX 소프트 바디 시뮬레이션은 유한 요소 분석(FEA)을 사용하여 메시의 변형을 시뮬레이션합니다. 두 개의 테트라헤럴 메시를 사용하여 deformable body를 표현합니다:

1. **시뮬레이션 메시**: 이 메시가 시뮬레이션에 사용되며 솔버에 의해 변형되는 메시입니다.
2. **충돌 메시**: 이 메시가 시뮬레이션 메시의 표면과 일치하기만 하면 되며 충돌 감지에 사용됩니다.

대부분의 응용 프로그램에서는 위 두 메시가 deformable body의 "렌더 메시"에서 계산된다고 가정합니다. 렌더 머시는 씬에서 보이는 메시로 렌더링 목적으로 사용됩니다. 삼각형으로 구성되어 있으며, PhysX 쿡킹을 기반으로 위 두 메시를 계산하는 데 사용됩니다.

스키마는 [PhysxDeformableBodyAPI](https://docs.omniverse.nvidia.com/kit/docs/omni_usd_schema_physics/104.2/class_physx_schema_physx_deformable_a_p_i.html)에 속하는 속성으로 구성되며, deformable body의 PhysX 매개변수를 포함하는 schemas로 이루어져 있습니다.

#### NOTE
이 함수는 `apply_nested()`로 데코레이트되어 입력 프리미 경로 아래의 모든 프리미(해당 프리미에 스키마가 적용된 경우)에 속성을 설정합니다.

* **매개변수:**
  * **prim_path** – deformable body의 프리미 경로.
  * **cfg** – deformable body에 대한 구성.
  * **stage** – 프리미를 찾을 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **반환값:**
  속성이 성공적으로 설정된 경우 True, 그렇지 않으면 False.
