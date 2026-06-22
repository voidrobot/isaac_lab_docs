# isaaclab.assets

강체 객체 및 관절식 로봇과 같은 다양한 자산을 위한 서브 패키지입니다.

자산은 시뮬레이션에서 생성할 수 있는 물리적 객체입니다. 이 클래스는 USD 스테이지에 자산을 생성하고 자산과의 상호작용을 위한 필수 물리 핸들을 초기화하는 작업을 모두 처리합니다.

자산 인스턴스가 생성될 때, 스폰 구성이 None이 아닌 경우 해당 자산에 대응하는 프리미가 USD 스테이지에 생성됩니다. 스폰 구성은 [`AssetBaseCfg.spawn`](#isaaclab.assets.AssetBaseCfg.spawn) 속성에 정의됩니다. 구성된 [`AssetBaseCfg.prim_path`](#isaaclab.assets.AssetBaseCfg.prim_path)가 표현식인 경우, 일치하는 모든 경로에 프리미가 생성됩니다. 그렇지 않은 경우, 구성된 경로에 단일 프리미만 생성됩니다. 스폰 구성에 대한 자세한 내용은 [`isaaclab.sim.spawners`](isaaclab.sim.spawners.md#module-isaaclab.sim.spawners) 모듈을 참조하세요.

자산 클래스는 스테이지 플레이/정지 이벤트에 대한 콜백도 등록합니다. 이는 물리 엔진이 스테이지가 재생 중일 때만 사용 가능하기 때문에 자산의 물리 핸들을 구성하는 데 사용됩니다. 또한, 클래스는 자산의 디버그 시각화를 위한 콜백을 등록하며, 이는 [`AssetBaseCfg.debug_vis`](#isaaclab.assets.AssetBaseCfg.debug_vis) 속성을 True로 설정하여 활성화할 수 있습니다.

자산 클래스는 다음과 같은 명명 규칙을 메서드에 따릅니다:

* **set_xxx()**: 버퍼를 `data` 인스턴스에 설정하는 데 사용됩니다. 그러나 시뮬레이터에 데이터를 쓰지 않습니다. 데이터 쓰기는 `write_data_to_sim()` 메서드가 호출될 때만 발생합니다.
* **write_xxx_to_sim()**: 버퍼를 `data` 인스턴스에 설정하고 해당 데이터를 시뮬레이터에도 쓰는 데 사용됩니다.
* **update(dt)**: `data` 인스턴스의 버퍼를 업데이트하는 데 사용됩니다. 시뮬레이션 단계 수행 후 호출해야 합니다.

`set` 및 `write` 작업을 분리하는 주된 이유는 사용자가 시뮬레이터에 적용하기 전에 버퍼에 대한 후처리 작업을 수행할 수 있도록 유연성을 제공하기 위해서입니다. 일반적인 예로는 명시적 액추에이터 모델을 다룰 때가 있습니다. 여기서 지정된 조인트 타겟은 시뮬레이터에 직접 적용되지 않고 대신 해당 액추에이터 토크를 계산하는 데 사용됩니다.

### 클래스

| [`AssetBase`](#isaaclab.assets.AssetBase)                                 | 자산의 기본 인터페이스 클래스입니다.                    |
|---------------------------------------------------------------------------|---------------------------------------------------------|
| [`AssetBaseCfg`](#isaaclab.assets.AssetBaseCfg)                           | 자산의 매개변수를 위한 기본 구성 클래스입니다.           |
| [`RigidObject`](#isaaclab.assets.RigidObject)                             | 강체 객체 자산 클래스입니다.                            |
| [`RigidObjectData`](#isaaclab.assets.RigidObjectData)                     | 강체 객체를 위한 데이터 컨테이너입니다.                 |
| [`RigidObjectCfg`](#isaaclab.assets.RigidObjectCfg)                       | 강체 객체를 위한 구성 매개변수입니다.                   |
| [`RigidObjectCollection`](#isaaclab.assets.RigidObjectCollection)         | 강체 객체 컬렉션 클래스입니다.                          |
| [`RigidObjectCollectionData`](#isaaclab.assets.RigidObjectCollectionData) | 강체 객체 컬렉션을 위한 데이터 컨테이너입니다.          |
| [`RigidObjectCollectionCfg`](#isaaclab.assets.RigidObjectCollectionCfg)   | 강체 객체 컬렉션을 위한 구성 매개변수입니다.            |
| [`Articulation`](#isaaclab.assets.Articulation)                           | 관절식 로봇 자산 클래스입니다.                          |
| [`ArticulationData`](#isaaclab.assets.ArticulationData)                   | 관절식 로봇을 위한 데이터 컨테이너입니다.               |
| [`ArticulationCfg`](#isaaclab.assets.ArticulationCfg)                     | 관절식 로봇을 위한 구성 매개변수입니다.                 |
| [`DeformableObject`](#isaaclab.assets.DeformableObject)                   | 변형 가능한 객체 자산 클래스입니다.                     |
| [`DeformableObjectData`](#isaaclab.assets.DeformableObjectData)           | 변형 가능한 객체를 위한 데이터 컨테이너입니다.          |
| [`DeformableObjectCfg`](#isaaclab.assets.DeformableObjectCfg)             | 변형 가능한 객체를 위한 구성 매개변수입니다.            |

## 자산 기본

### *class* isaaclab.assets.AssetBase

자산의 기본 인터페이스 클래스입니다.

자산은 시뮬레이션에서 생성할 수 있는 모든 물리 활성화된 객체에 해당합니다. 여기에는 강체 객체, 관절식 객체, 변형 가능한 객체 등이 포함됩니다. 자산의 핵심 기능은 시뮬레이터와의 상호작용에 사용할 수 있는 버퍼 세트를 제공하는 것입니다. 버퍼는 자산 클래스에 의해 업데이트되며, 해당 `write` 메서드를 사용하여 시뮬레이터에 기록할 수 있습니다. 이를 통해 시뮬레이터에 기록하기 전에 버퍼에 대한 후처리 작업을 수행하고 해당 시뮬레이션 결과를 얻을 수 있습니다.

클래스는 USD 스테이지에 자산을 생성하고 자산과의 상호작용을 위한 필수 물리 핸들을 초기화하는 작업을 모두 처리합니다. 자산 인스턴스가 생성될 때, 스폰 구성이 None이 아닌 경우 해당 자산에 대응하는 프리미가 USD 스테이지에 생성됩니다. 스폰 구성은 [`AssetBaseCfg.spawn`](#isaaclab.assets.AssetBaseCfg.spawn) 속성에 정의됩니다. 구성된 [`AssetBaseCfg.prim_path`](#isaaclab.assets.AssetBaseCfg.prim_path)가 표현식인 경우, 일치하는 모든 경로에 프리미가 생성됩니다. 그렇지 않은 경우, 구성된 경로에 단일 프리미만 생성됩니다. 스폰 구성에 대한 자세한 내용은 [`isaaclab.sim.spawners`](isaaclab.sim.spawners.md#module-isaaclab.sim.spawners) 모듈을 참조하세요.

Isaac Sim 인터페이스와 달리, 보통 [`isaacsim.core.prims.XFormPrim.initialize()`](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/py/source/extensions/isaacsim.core.prims/docs/index.html#isaacsim.core.prims.XFormPrim.initialize) 메서드를 호출하여 PhysX 핸들을 초기화해야 하지만, 자산 클래스는 스테이지가 재생/정지될 때 PhysX 핸들을 자동으로 초기화하고 무효화합니다. 이는 스테이지 플레이/정지 이벤트에 대한 콜백을 등록하여 수행됩니다.

또한, 클래스는 자산 클래스에 디버그 시각화가 구현된 경우 디버그 시각화를 위한 콜백을 등록합니다. 이는 [`AssetBaseCfg.debug_vis`](#isaaclab.assets.AssetBaseCfg.debug_vis) 속성을 True로 설정하여 활성화할 수 있습니다. 디버그 시각화는 `_set_debug_vis_impl()` 및 `_debug_vis_callback()` 메서드를 통해 구현됩니다.

**메서드:**

| [`__init__`](#isaaclab.assets.AssetBase.__init__)(cfg)                            | 자산 기본을 초기화합니다.                                  |
|-----------------------------------------------------------------------------------|-------------------------------------------------------------|
| [`set_visibility`](#isaaclab.assets.AssetBase.set_visibility)(visible[, env_ids]) | 자산에 해당하는 프리미의 가시성을 설정합니다.               |
| [`set_debug_vis`](#isaaclab.assets.AssetBase.set_debug_vis)(debug_vis)            | 자산 데이터 시각화 여부를 설정합니다.                       |
| [`reset`](#isaaclab.assets.AssetBase.reset)([env_ids])                            | 선택된 환경의 모든 내부 버퍼를 재설정합니다.               |
| [`write_data_to_sim`](#isaaclab.assets.AssetBase.write_data_to_sim)()             | 시뮬레이터에 데이터를 기록합니다.                           |
| [`update`](#isaaclab.assets.AssetBase.update)(dt)                                 | 내부 버퍼를 업데이트합니다.                                 |

**속성:**

| [`is_initialized`](#isaaclab.assets.AssetBase.is_initialized)                             | 자산이 초기화되었는지 여부입니다.                        |
|-------------------------------------------------------------------------------------------|----------------------------------------------------------|
| [`num_instances`](#isaaclab.assets.AssetBase.num_instances)                               | 자산의 인스턴스 수입니다.                                |
| [`device`](#isaaclab.assets.AssetBase.device)                                             | 계산을 위한 메모리 장치입니다.                           |
| [`data`](#isaaclab.assets.AssetBase.data)                                                 | 자산과 관련된 데이터입니다.                              |
| [`has_debug_vis_implementation`](#isaaclab.assets.AssetBase.has_debug_vis_implementation) | 자산에 디버그 시각화가 구현되어 있는지 여부입니다.       |

#### \_\_init_\_(cfg: [AssetBaseCfg](#isaaclab.assets.AssetBaseCfg))

자산 básicos를 초기화합니다.

* **매개변수:**
  **cfg** – 자산의 구성 클래스입니다.
* **예외 발생:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 입력 프리미 경로 또는 프리미 경로 표현식에서 프리미를 찾을 수 없는 경우.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 초기화되었는지 여부입니다.

자산이 초기화된 경우 True, 그렇지 않은 경우 False를 반환합니다.

#### *abstract property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

자산의 인스턴스 수입니다.

이는 환경당 자산 인스턴스 수에 환경 수를 곱한 값과 같습니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 위한 메모리 장치입니다.

#### *abstract property* data *: [Any](https://docs.python.org/3/library/typing.html#typing.Any)*

자산과 관련된 데이터입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산에 디버그 시각화가 구현되어 있는지 여부입니다.

#### set_visibility(visible: [bool](https://docs.python.org/3/library/functions.html#bool), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

자산에 해당하는 프리미스의 가시성을 설정합니다.

이 작업은 USD 스테이지에서 자산에 해당하는 프리미스의 가시성에 영향을 미칩니다.
시뮬레이터에서 자산의 가시성을 토글하는 데 유용합니다. 예를 들어, 사용되지 않는 자산을 숨겨서 렌더링 오버헤드를 줄일 수 있습니다.

#### NOTE
이 작업은 PXR API를 사용하여 프리미스의 가시성을 설정합니다. 따라서 프리미스 수가 많을 경우 오버헤드가 발생할 수 있습니다.

* **매개변수:**
  * **visible** – 프리미스를 보이게 할지 여부.
  * **env_ids** – 가시성을 설정할 객체의 인덱스. 기본값은 None(모든 인스턴스).

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

자산 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 자산 데이터를 시각화할지 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 자산이 디버그 시각화를 지원하지 않으면 False.

#### *abstract* reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경의 모든 내부 버퍼를 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 객체의 인덱스. 기본값은 None(모든 인스턴스).

#### *abstract* write_data_to_sim()

시뮬레이터에 데이터를 씁니다.

#### *abstract* update(dt: [float](https://docs.python.org/3/library/functions.html#float))

내부 버퍼를 업데이트합니다.

시간 단계 `dt`는 시뮬레이터에서 제공하지 않는 joint 가속도와 같은 양의 수치 미분을 계산하는 데 사용됩니다.

* **매개변수:**
  **dt** – 마지막 `update` 호출로부터 경과한 시간.

### *class* isaaclab.assets.AssetBaseCfg

자산의 파라미터를 위한 기본 구성 클래스입니다.

자산 클래스에 대한 자세한 내용은 [`AssetBase`](#isaaclab.assets.AssetBase)를 참조하세요.

**속성:**

| [`prim_path`](#isaaclab.assets.AssetBaseCfg.prim_path)             | 자산의 프리미스 경로(또는 표현식).              |
|--------------------------------------------------------------------|------------------------------------------------------|
| [`spawn`](#isaaclab.assets.AssetBaseCfg.spawn)                     | 자산의 생성 설정.                   |
| [`init_state`](#isaaclab.assets.AssetBaseCfg.init_state)           | 강성 객체의 초기 상태.                   |
| [`collision_group`](#isaaclab.assets.AssetBaseCfg.collision_group) | 자산의 충돌 그룹.                        |
| [`debug_vis`](#isaaclab.assets.AssetBaseCfg.debug_vis)             | 자산에 대한 디버그 시각화 활성화 여부. |

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

자산의 프리미스 경로(또는 표현식).

#### NOTE
표현식에는 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot`은 `/World/envs/env_.*/Robot`로 대체됩니다.

#### spawn *: [SpawnerCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.SpawnerCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

자산의 생성 설정. 기본값은 None입니다.

None인 경우, 자산 클래스가 프리미스를 생성하지 않으며, 대신 자산이 이미 сц에 존재한다고 가정합니다.

#### init_state *: [InitialStateCfg](#isaaclab.assets.DeformableObjectCfg.InitialStateCfg)*

강성 객체의 초기 상태. 기본값은 동일 포즈(identity pose)입니다.

#### collision_group *: Literal[0, -1]*

자산의 충돌 그룹. 기본값은 `0`입니다.

* `-1`: 전역 충돌 그룹(시뮬레이션 내의 모든 자산과 충돌).
* `0`: 로컬 충돌 그룹(같은 환경 내의 다른 자산과 충돌).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산에 대한 디버그 시각화를 활성화할지 여부. 기본값은 `False`.

## 강성 객체

### *class* isaaclab.assets.RigidObject

Bases: [`AssetBase`](#isaaclab.assets.AssetBase)

강성 객체 자산 클래스.

강성 객체는 강성 몸체로 구성된 자산으로, 상자, 구 등 동적 객체를 나타내는 데 사용할 수 있습니다. 강성 몸체는 자세, 속도 및 질량 분포로 설명됩니다.

자산이 강성 객체로 간주되기 위해서는 해당 자산의 루트 프리미스에 [USD RigidBodyAPI](https://openusd.org/dev/api/class_usd_physics_rigid_body_a_p_i.html)가 적용되어 있어야 합니다. 이 API는 강성 몸체의 시뮬레이션 속성을 정의하는 데 사용됩니다. 시뮬레이션을 실행하면 물리 엔진이 자동으로 강성 몸체를 등록하고 해당 강성 몸체 핸들을 생성합니다. 이 핸들은 [`root_physx_view`](#isaaclab.assets.RigidObject.root_physx_view) 속성을 통해 접근할 수 있습니다.

#### NOTE
Isaac Sim에 익숙한 사용자를 위해 말씀드리면, PhysX 뷰 클래스 API는 Isaac Sim의 뷰 클래스 API와 정확히 동일하지 않습니다. Isaac Lab과 마찬가지로 Isaac Sim도 PhysX 뷰 API를 래핑합니다. 그러나 현재(2023.1 릴리스 기준) Isaac Sim에서 뷰 클래스를 초기화하는 방식에 큰 차이가 있습니다. 이는 Isaac Sim의 뷰 클래스가 느린 USD 관련 추가 작업을 수행하기 때문이며, 이러한 작업은 필요하지 않습니다.

**속성:**

| [`cfg`](#isaaclab.assets.RigidObject.cfg)                                                     | 강성 객체의 구성 인스턴스.             |
|-----------------------------------------------------------------------------------------------|----------------------------------------------------------|
| [`data`](#isaaclab.assets.RigidObject.data)                                                   | 자산과 관련된 데이터.                               |
| [`num_instances`](#isaaclab.assets.RigidObject.num_instances)                                 | 자산의 인스턴스 수.                        |
| [`num_bodies`](#isaaclab.assets.RigidObject.num_bodies)                                       | 자산 내의 몸체 수.                           |
| [`body_names`](#isaaclab.assets.RigidObject.body_names)                                       | 강성 객체 내의 몸체의 순서된 이름.             |
| [`root_physx_view`](#isaaclab.assets.RigidObject.root_physx_view)                             | 강성 몸체 뷰(PhysX).                               |
| [`instantaneous_wrench_composer`](#isaaclab.assets.RigidObject.instantaneous_wrench_composer) |瞬间扭矩作曲家.                           |
| [`permanent_wrench_composer`](#isaaclab.assets.RigidObject.permanent_wrench_composer)         |永久扭矩作曲家.                               |
| [`device`](#isaaclab.assets.RigidObject.device)                                               | 계산에 사용되는 메모리 디바이스.                           |
| [`has_debug_vis_implementation`](#isaaclab.assets.RigidObject.has_debug_vis_implementation)   | 자산에 디버그 시각화 구현이 있는지 여부. |
| [`is_initialized`](#isaaclab.assets.RigidObject.is_initialized)                               | 자산이 초기화되었는지 여부.                        |

**메서드:**

| [`__init__`](#isaaclab.assets.RigidObject.__init__)(cfg)                                                         | 강성 객체를 초기화.                                                                |
|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`reset`](#isaaclab.assets.RigidObject.reset)([env_ids])                                                         | 선택된 환경의 모든 내부 버퍼를 재설정.                                       |
| [`write_data_to_sim`](#isaaclab.assets.RigidObject.write_data_to_sim)()                                          | 시뮬레이션에 외부 토크 작성.                                                    |
| [`update`](#isaaclab.assets.RigidObject.update)(dt)                                                              | 내부 버퍼 업데이트.                                                                |
| [`find_bodies`](#isaaclab.assets.RigidObject.find_bodies)(name_keys[, preserve_order])                           | 이름 키를 기반으로 강성 몸체 내에서 몸체 찾기.                                       |
| [`write_root_state_to_sim`](#isaaclab.assets.RigidObject.write_root_state_to_sim)(root_state[, env_ids])         | 선택된 환경 인덱스에 대한 루트 상태를 시뮬레이션에 설정.                   |
| [`write_root_com_state_to_sim`](#isaaclab.assets.RigidObject.write_root_com_state_to_sim)(root_state[, ...])     | 선택된 환경 인덱스에 대한 루트 질량 중심을 시뮬레이션에 설정.    |
| [`write_root_link_state_to_sim`](#isaaclab.assets.RigidObject.write_root_link_state_to_sim)(root_state[, ...])   | 선택된 환경 인덱스에 대한 루트 링크 상태를 시뮬레이션에 설정.              |
| [`write_root_pose_to_sim`](#isaaclab.assets.RigidObject.write_root_pose_to_sim)(root_pose[, env_ids])            | 선택된 환경 인덱스에 대한 루트 포즈를 시뮬레이션에 설정.                    |
| [`write_root_link_pose_to_sim`](#isaaclab.assets.RigidObject.write_root_link_pose_to_sim)(root_pose[, env_ids])  | 선택된 환경 인덱스에 대한 루트 링크 포즈를 시뮬레이션에 설정.               |
| [`write_root_com_pose_to_sim`](#isaaclab.assets.RigidObject.write_root_com_pose_to_sim)(root_pose[, env_ids])    | 선택된 환경 인덱스에 대한 질량 중심 포즈를 시뮬레이션에 설정합니다.     |
| [`write_root_velocity_to_sim`](#isaaclab.assets.RigidObject.write_root_velocity_to_sim)(root_velocity[, ...])    | 선택된 환경 인덱스에 대한 질량 중심 속도를 시뮬레이션에 설정합니다. |
| [`write_root_com_velocity_to_sim`](#isaaclab.assets.RigidObject.write_root_com_velocity_to_sim)(root_velocity)   | 선택된 환경 인덱스에 대한 질량 중심 속도를 시뮬레이션에 설정합니다. |
| [`write_root_link_velocity_to_sim`](#isaaclab.assets.RigidObject.write_root_link_velocity_to_sim)(root_velocity) | 선택된 환경 인덱스에 대한 루트 링크 속도를 시뮬레이션에 설정합니다.           |
| [`set_external_force_and_torque`](#isaaclab.assets.RigidObject.set_external_force_and_torque)(forces, torques)   | 로컬 프레임에서 애셋의 바디에 외부 힘과 토크를 적용합니다.          |
| [`set_debug_vis`](#isaaclab.assets.RigidObject.set_debug_vis)(debug_vis)                                         | 애셋 데이터의 시각화 여부를 설정합니다.                                                   |
| [`set_visibility`](#isaaclab.assets.RigidObject.set_visibility)(visible[, env_ids])                              | 애셋에 해당하는 프리미의 가시성을 설정합니다.                                 |

#### cfg *: [RigidObjectCfg](#isaaclab.assets.RigidObjectCfg)*

강체 객체에 대한 구성 인스턴스.

#### \_\_init_\_(cfg: [RigidObjectCfg](#isaaclab.assets.RigidObjectCfg))

강체 객체를 초기화합니다.

* **Parameters:**
  **cfg** – 구성 인스턴스.

#### *property* data *: [RigidObjectData](#isaaclab.assets.RigidObjectData)*

애셋과 관련된 데이터.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

애셋의 인스턴스 수.

이 값은 환경당 애셋 인스턴스 수에 환경 수를 곱한 값과 동일합니다.

#### *property* num_bodies *: [int](https://docs.python.org/3/library/functions.html#int)*

애셋의 바디 수.

각 객체가 단일 강체이므로 항상 1입니다.

#### *property* body_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

강체 객체의 바디 이름을 순서대로 나열한 목록.

#### *property* root_physx_view *: omni.physics.tensors.impl.api.RigidBodyView*

애셋에 대한 강체 뷰 (PhysX).

#### NOTE
이 뷰는 주의해서 사용해야 합니다. 특정 방식으로 텐서를 처리해야 합니다.

#### *property* instantaneous_wrench_composer *: WrenchComposer*

순간적 휀치 작곡가.

`WrenchComposer` 인스턴스를 반환합니다. 이 휀치 작곡가에 추가되거나 설정된 휀치는 현재 시뮬레이션 단계에서만 유효합니다. 시뮬레이션 단계가 끝날 때 이 객체에 설정된 휀치는 폐기됩니다. 이는 드래그 힘과 같이 지속적으로 변하는 힘을 적용하는 데 유용합니다.

#### *property* permanent_wrench_composer *: WrenchComposer*

영구적 휀치 작곡가.

`WrenchComposer` 인스턴스를 반환합니다. 이 휀치 작곡가에 추가되거나 설정된 휀치는 지속적이며 시뮬레이션의 모든 단계에서 적용됩니다. 이는 모터의 추력과 같이 일정 기간 동안 일정한 힘을 적용하는 데 유용합니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경의 모든 내부 버퍼를 재설정합니다.

* **Parameters:**
  **env_ids** – 재설정할 객체의 인덱스. 기본값은 None(모든 인스턴스).

#### write_data_to_sim()

시뮬레이션에 외부 휀치를 기록합니다.

#### NOTE
이 함수는 시뮬레이션 단계 이전에 호출되므로 여기에서 외부 휀치를 시뮬레이션에 기록합니다.これにより、外部ワレンチが毎シミュレーションステップに適用されることを保証します。

#### update(dt: [float](https://docs.python.org/3/library/functions.html#float))

내부 버퍼를 업데이트합니다.

시간 단계 `dt`는 시뮬레이터에서 제공하지 않는 관절 가속도와 같은 양의 수치 미분을 계산하는 데 사용됩니다.

* **Parameters:**
  **dt** – 마지막 `update` 호출로부터 경과된 시간.

#### find_bodies(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

이름 키를 기반으로 강체에서 바디를 찾습니다.

이름 매칭에 대한 자세한 내용은 `isaaclab.utils.string_utils.resolve_matching_names()` 함수를 참조하십시오.

* **Parameters:**
  * **name_keys** – 바디 이름과 일치하는 정규 표현식 또는 정규 표현식 목록.
  * **preserve_order** – 출력에서 이름 키의 순서를 유지할지 여부. 기본값은 False입니다.
* **Returns:**
  바디 인덱스와 이름을 포함하는 리스트들의 튜플.

#### write_root_state_to_sim(root_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 상태를 시뮬레이션에 설정합니다.

루트 상태는 (w, x, y, z) 순서의 사반수형 방향 및 사반수형 방향, 그리고 선형 및 각속도를 포함합니다. 모든 값은 시뮬레이션 프레임에 있습니다.

* **Parameters:**
  * **root_state** – 시뮬레이션 프레임의 루트 상태. 형태는 (len(env_ids), 13).
  * **env_ids** – 환경 인덱스. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_com_state_to_sim(root_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심 상태를 시뮬레이션에 설정합니다.

루트 상태는 (w, x, y, z) 순서의 사반수형 방향 및 사반수형 방향, 그리고 선형 및 각속도를 포함합니다. 모든 값은 시뮬레이션 프레임에 있습니다.

* **Parameters:**
  * **root_state** – 시뮬레이션 프레임의 루트 상태. 형태는 (len(env_ids), 13).
  * **env_ids** – 환경 인덱스. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_link_state_to_sim(root_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 링크 상태를 시뮬레이션에 설정합니다.

루트 상태는 (w, x, y, z) 순서의 사반수형 방향 및 사반수형 방향, 그리고 선형 및 각속도를 포함합니다. 모든 값은 시뮬레이션 프레임에 있습니다.

* **Parameters:**
  * **root_state** – 시뮬레이션 프레임의 루트 상태. 형태는 (len(env_ids), 13).
  * **env_ids** – 환경 인덱스. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_pose_to_sim(root_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 포즈를 시뮬레이션에 설정합니다.

루트 포즈는 데카르트 위치와 사반수형 방향 (w, x, y, z)으로 구성됩니다.

* **Parameters:**
  * **root_pose** – 시뮬레이션 프레임의 루트 링크 포즈. 형태는 (len(env_ids), 7).
  * **env_ids** – 환경 인덱스. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_link_pose_to_sim(root_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 링크 포즈를 시뮬레이션에 설정합니다.

루트 포즈는 데카르트 위치와 사반수형 방향 (w, x, y, z)으로 구성됩니다.

* **Parameters:**
  * **root_pose** – 시뮬레이션 프레임의 루트 링크 포즈. 형태는 (len(env_ids), 7).
  * **env_ids** – 환경 인덱스. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_com_pose_to_sim(root_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심 포즈를 시뮬레이션에 설정합니다.

루트 포즈는 데카르트 위치와 사반수형 방향 (w, x, y, z)으로 구성됩니다. 방향은 관성의 주축 방향입니다.

* **Parameters:**
  * **root_pose** – 시뮬레이션 프레임의 루트 질량 중심 포즈. 형태는 (len(env_ids), 7).
  * **env_ids** – 환경 인덱스. None인 경우, 모든 인덱스가 사용됩니다.

#### write_root_velocity_to_sim(root_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스의 루트 질량 중심을 시뮬레이션에 설정합니다.

속도는 순서대로 선형 속도(x, y, z)와 각속도(x, y, z)로 구성됩니다.
참고: 이 함수는 루트의 프레임이 아닌 루트 질량 중심의 속도를 설정합니다.

* **매개변수:**
  * **root_velocity** – 시뮬레이션 월드 프레임에서의 루트 질량 중심 속도. 형태는 (len(env_ids), 6)입니다.
  * **env_ids** – 환경 인덱스. None인 경우, 모든 인덱스가 사용됩니다.

#### write_root_com_velocity_to_sim(root_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스의 루트 질량 중심을 시뮬레이션에 설정합니다.

속도는 순서대로 선형 속도(x, y, z)와 각속도(x, y, z)로 구성됩니다.
참고: 이 함수는 루트의 프레임이 아닌 루트 질량 중심의 속도를 설정합니다.

* **매개변수:**
  * **root_velocity** – 시뮬레이션 월드 프레임에서의 루트 질량 중심 속도. 형태는 (len(env_ids), 6)입니다.
  * **env_ids** – 환경 인덱스. None인 경우, 모든 인덱스가 사용됩니다.

#### write_root_link_velocity_to_sim(root_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스의 루트 링크 속도를 시뮬레이션에 설정합니다.

속도는 순서대로 선형 속도(x, y, z)와 각속도(x, y, z)로 구성됩니다.
참고: 이 함수는 루트 질량 중심이 아닌 루트의 프레임 속도를 설정합니다.

* **매개변수:**
  * **root_velocity** – 시뮬레이션 월드 프레임에서의 루트 프레임 속도. 형태는 (len(env_ids), 6)입니다.
  * **env_ids** – 환경 인덱스. None인 경우, 모든 인덱스가 사용됩니다.

#### set_external_force_and_torque(forces: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), torques: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, body_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, is_global: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

자산의 본체에 로컬 프레임에서 외부 힘과 토크를 적용합니다.

많은 응용 프로그램에서 강체에 적용된 외부 힘이 일정 기간(예: 정책 제어 중) 동안 일정하게 유지되도록 하는 것이 바람직합니다. 이 함수는 외부 힘과 토크를 버퍼에 저장하여 시뮬레이션의 각 단계에서 적용할 수 있도록 합니다. 선택적으로, 본체의 로컬 링크 프레임에서 외부 렌치를 적용할 위치를 설정할 수 있습니다.

#### 참고
이 함수는 시뮬레이션에 외부 렌치를 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 외부 렌치를 적용하려면 시뮬레이션 단계 직전에 [`write_data_to_sim()`](#isaaclab.assets.RigidObject.write_data_to_sim) 함수를 호출하세요.

* **매개변수:**
  * **forces** – 본체의 로컬 프레임에서의 외부 힘. 형태는 (len(env_ids), len(body_ids), 3)입니다.
  * **torques** – 본체의 로컬 프레임에서의 외부 토크. 형태는 (len(env_ids), len(body_ids), 3)입니다.
  * **positions** – 본체의 로컬 프레임에서의 외부 렌치 위치. 형태는 (len(env_ids), len(body_ids), 3)입니다. 기본값은 None입니다.
  * **body_ids** – 외부 렌치를 적용할 본체 인덱스. 기본값은 None(모든 본체).
  * **env_ids** – 외부 렌치를 적용할 환경 인덱스. 기본값은 None(모든 인스턴스).
  * **is_global** – 전역 프레임에서 외부 렌치를 적용할지 여부. 기본값은 False이며, False로 설정된 경우 외부 렌치는 본체의 링크 프레임에서 적용됩니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 디바이스.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋에 디버그 시각화가 구현되어 있는지 여부.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋이 초기화되었는지 여부.

에셋이 초기화되었다면 True, 그렇지 않다면 False를 반환합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

에셋 데이터의 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 에셋 데이터를 시각화할지 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 에셋이 디버그 시각화를 지원하지 않는 경우 False.

#### set_visibility(visible: [bool](https://docs.python.org/3/library/functions.html#bool), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

에셋에 해당하는 프리머스의 가시성을 설정합니다.

이 작업은 USD 단계에서 에셋에 해당하는 프리머스의 가시성에 영향을 미칩니다. 시뮬레이터에서 에셋의 가시성을 토글하는 데 유용합니다. 예를 들어, 렌더링 오버헤드를 줄이기 위해 에셋이 사용되지 않을 때 에셋을 숨길 수 있습니다.

#### 참고
이 작업은 PXR API를 사용하여 프리머스의 가시성을 설정합니다. 따라서 프리머스 수가 많은 경우 오버헤드가 발생할 수 있습니다.

* **매개변수:**
  * **visible** – 프리머스를 보이게 할지 여부.
  * **env_ids** – 가시성을 설정할 객체의 인덱스. 기본값은 None(모든 인스턴스).

### *class* isaaclab.assets.RigidObjectData

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

강체 객체의 데이터 컨테이너.

이 클래스는 시뮬레이션에서의 강체 객체의 데이터를 포함합니다. 데이터에는 루트 강체 본체의 상태와 객체 내 모든 본체의 상태가 포함됩니다. 데이터는 별도로 지정되지 않는 한 시뮬레이션 월드 프레임에 저장됩니다.

강체 본체에 대해서는 두 가지 참조 프레임이 사용됩니다.

- 액터 프레임: 강체 본체 프리머스의 참조 프레임. 이는 일반적으로 강체 본체 스키마가 있는 Xform 프리머스에 해당합니다.
- 질량 중심 프레임: 강체 본체의 질량 중심의 참조 프레임.

시뮬레이션의 설정에 따라 액터 프레임과 질량 중심 프레임이 동일할 수 있습니다. 이 점을 고려하여 데이터를 해석해야 합니다.

데이터는 지연 업데이트 방식으로, 즉 데이터가 접근될 때만 업데이트됩니다. 이는 데이터의 계산이나 조회가 비용이 많이 드는 경우 유용합니다. 데이터는 버퍼의 타임스탬프가 현재 시뮬레이션 타임스탬프보다 오래되었을 때 업데이트되며, 데이터가 업데이트될 때마다 타임스탬프가 갱신됩니다.

**메서드:**

| [`update`](#isaaclab.assets.RigidObjectData.update)(dt)   | 강체 객체의 데이터를 업데이트합니다.   |
|-----------------------------------------------------------|------------------------------------------|

**속성:**

| [`body_names`](#isaaclab.assets.RigidObjectData.body_names)                   | 시뮬레이션 뷰에서 파싱된 순서대로의 본체 이름.                                                          |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [`default_root_state`](#isaaclab.assets.RigidObjectData.default_root_state)   | 로컬 환경 프레임에서의 기본 루트 상태 `[pos, quat, lin_vel, ang_vel]`.                                  |
| [`default_mass`](#isaaclab.assets.RigidObjectData.default_mass)               | 시뮬레이션에서 읽은 기본 질량.                                                                          |
| [`default_inertia`](#isaaclab.assets.RigidObjectData.default_inertia)         | 시뮬레이션에서 읽은 기본 관성 텐서.                                                                |
| [`root_link_pose_w`](#isaaclab.assets.RigidObjectData.root_link_pose_w)       | 시뮬레이션 월드 프레임에서의 루트 링크 포즈 `[pos, quat]`.                                                         |
| [`root_link_vel_w`](#isaaclab.assets.RigidObjectData.root_link_vel_w)         | 시뮬레이션 월드 프레임에서의 루트 링크 속도 `[lin_vel, ang_vel]`.                                              |
| [`root_com_pose_w`](#isaaclab.assets.RigidObjectData.root_com_pose_w)         | 시뮬레이션 월드 프레임에서의 루트 질량 중심 포즈 `[pos, quat]`.                                               |
| [`root_com_vel_w`](#isaaclab.assets.RigidObjectData.root_com_vel_w)           | 시뮬레이션 월드 프레임에서의 루트 질량 중심 속도 `[lin_vel, ang_vel]`.                                    |
| [`root_state_w`](#isaaclab.assets.RigidObjectData.root_state_w)               | 시뮬레이션 월드 프레임에서의 루트 상태 `[pos, quat, lin_vel, ang_vel]`입니다.                                           |
| [`root_link_state_w`](#isaaclab.assets.RigidObjectData.root_link_state_w)     | 시뮬레이션 월드 프레임에서의 루트 상태 `[pos, quat, lin_vel, ang_vel]`입니다.                                           |
| [`root_com_state_w`](#isaaclab.assets.RigidObjectData.root_com_state_w)       | 시뮬레이션 월드 프레임에서의 루트 질량중심 상태 `[pos, quat, lin_vel, ang_vel]`입니다.                            |
| [`body_link_pose_w`](#isaaclab.assets.RigidObjectData.body_link_pose_w)       | 시뮬레이션 월드 프레임에서의 바디 링크 포즈 `[pos, quat]`입니다.                                                         |
| [`body_link_vel_w`](#isaaclab.assets.RigidObjectData.body_link_vel_w)         | 시뮬레이션 월드 프레임에서의 바디 링크 속도 `[lin_vel, ang_vel]`입니다.                                              |
| [`body_com_pose_w`](#isaaclab.assets.RigidObjectData.body_com_pose_w)         | 시뮬레이션 월드 프레임에서의 바디 질량중심 포즈 `[pos, quat]`입니다.                                               |
| [`body_com_vel_w`](#isaaclab.assets.RigidObjectData.body_com_vel_w)           | 시뮬레이션 월드 프레임에서의 바디 질량중심 속도 `[lin_vel, ang_vel]`입니다.                                    |
| [`body_state_w`](#isaaclab.assets.RigidObjectData.body_state_w)               | 시뮬레이션 월드 프레임에서의 모든 바디의 상태 `[pos, quat, lin_vel, ang_vel]`입니다.                                    |
| [`body_link_state_w`](#isaaclab.assets.RigidObjectData.body_link_state_w)     | 시뮬레이션 월드 프레임에서의 모든 바디의 상태 `[pos, quat, lin_vel, ang_vel]`입니다.                                  |
| [`body_com_state_w`](#isaaclab.assets.RigidObjectData.body_com_state_w)       | 시뮬레이션 월드 프레임에서의 모든 바디의 상태 `[pos, quat, lin_vel, ang_vel]`입니다.                                  |
| [`body_com_acc_w`](#isaaclab.assets.RigidObjectData.body_com_acc_w)           | 시뮬레이션 월드 프레임에서의 모든 바디의 가속도 `[lin_acc, ang_acc]`입니다.                                  |
| [`body_com_pose_b`](#isaaclab.assets.RigidObjectData.body_com_pose_b)         | 모든 바디의 해당 바디 링크 프레임에서의 질량중심 포즈 `[pos, quat]`입니다.                         |
| [`projected_gravity_b`](#isaaclab.assets.RigidObjectData.projected_gravity_b) | 기본 프레임에 대한 중력 방향의 투영입니다.                                                              |
| [`heading_w`](#isaaclab.assets.RigidObjectData.heading_w)                     | 기본 프레임의 요 헤딩(라디안 단위)입니다.                                                                     |
| [`root_link_lin_vel_b`](#isaaclab.assets.RigidObjectData.root_link_lin_vel_b) | 기본 프레임에서의 루트 링크 선형 속도입니다.                                                                        |
| [`root_link_ang_vel_b`](#isaaclab.assets.RigidObjectData.root_link_ang_vel_b) | 기본 월드 프레임에서의 루트 링크 각속도입니다.                                                                 |
| [`root_com_lin_vel_b`](#isaaclab.assets.RigidObjectData.root_com_lin_vel_b)   | 기본 프레임에서의 루트 질량중심 선형 속도입니다.                                                              |
| [`root_com_ang_vel_b`](#isaaclab.assets.RigidObjectData.root_com_ang_vel_b)   | 기본 월드 프레임에서의 루트 질량중심 각속도입니다.                                                       |
| [`root_link_pos_w`](#isaaclab.assets.RigidObjectData.root_link_pos_w)         | 시뮬레이션 월드 프레임에서의 루트 링크 위치입니다.                                                                   |
| [`root_link_quat_w`](#isaaclab.assets.RigidObjectData.root_link_quat_w)       | 시뮬레이션 월드 프레임에서의 루트 링크 방향 (w, x, y, z)입니다.                                                   |
| [`root_link_lin_vel_w`](#isaaclab.assets.RigidObjectData.root_link_lin_vel_w) | 시뮬레이션 월드 프레임에서의 루트 선형 속도입니다.                                                                 |
| [`root_link_ang_vel_w`](#isaaclab.assets.RigidObjectData.root_link_ang_vel_w) | 시뮬레이션 월드 프레임에서의 루트 링크 각속도입니다.                                                           |
| [`root_com_pos_w`](#isaaclab.assets.RigidObjectData.root_com_pos_w)           | 시뮬레이션 월드 프레임에서의 루트 질량중심 위치입니다.                                                         |
| [`root_com_quat_w`](#isaaclab.assets.RigidObjectData.root_com_quat_w)         | 시뮬레이션 월드 프레임에서의 루트 질량중심 방향 (w, x, y, z)입니다.                                         |
| [`root_com_lin_vel_w`](#isaaclab.assets.RigidObjectData.root_com_lin_vel_w)   | 시뮬레이션 월드 프레임에서의 루트 질량중심 선형 속도입니다.                                                  |
| [`root_com_ang_vel_w`](#isaaclab.assets.RigidObjectData.root_com_ang_vel_w)   | 시뮬레이션 월드 프레임에서의 루트 질량중심 각속도입니다.                                                 |
| [`body_link_pos_w`](#isaaclab.assets.RigidObjectData.body_link_pos_w)         | 시뮬레이션 월드 프레임에서의 모든 바디 위치입니다.                                                              |
| [`body_link_quat_w`](#isaaclab.assets.RigidObjectData.body_link_quat_w)       | 시뮬레이션 월드 프레임에서의 모든 바디 방향 (w, x, y, z)입니다.                                               |
| [`body_link_lin_vel_w`](#isaaclab.assets.RigidObjectData.body_link_lin_vel_w) | 시뮬레이션 월드 프레임에서의 모든 바디 선형 속도입니다.                                                        |
| [`body_link_ang_vel_w`](#isaaclab.assets.RigidObjectData.body_link_ang_vel_w) | 시뮬레이션 월드 프레임에서의 모든 바디 각속도입니다.                                                       |
| [`body_com_pos_w`](#isaaclab.assets.RigidObjectData.body_com_pos_w)           | 시뮬레이션 월드 프레임에서의 모든 바디 위치입니다.                                                              |
| [`body_com_quat_w`](#isaaclab.assets.RigidObjectData.body_com_quat_w)         | 시뮬레이션 월드 프레임에서의 모든 바디 관성 주축 방향 (w, x, y, z)입니다.              |
| [`body_com_lin_vel_w`](#isaaclab.assets.RigidObjectData.body_com_lin_vel_w)   | 시뮬레이션 월드 프레임에서의 모든 바디 선형 속도입니다.                                                        |
| [`body_com_ang_vel_w`](#isaaclab.assets.RigidObjectData.body_com_ang_vel_w)   | 시뮬레이션 월드 프레임에서의 모든 바디 각속도입니다.                                                       |
| [`body_com_lin_acc_w`](#isaaclab.assets.RigidObjectData.body_com_lin_acc_w)   | 시뮬레이션 월드 프레임에서의 모든 바디 선형 가속도입니다.                                                    |
| [`body_com_ang_acc_w`](#isaaclab.assets.RigidObjectData.body_com_ang_acc_w)   | 시뮬레이션 월드 프레임에서의 모든 바디 각 가속도입니다.                                                    |
| [`body_com_pos_b`](#isaaclab.assets.RigidObjectData.body_com_pos_b)           | 해당 바디 링크 프레임에서의 모든 바디 질량중심 위치입니다.                                   |
| [`body_com_quat_b`](#isaaclab.assets.RigidObjectData.body_com_quat_b)         | 해당 바디 링크 프레임에서의 모든 바디 관성 주축 방향 (w, x, y, z)입니다. |
| [`root_pose_w`](#isaaclab.assets.RigidObjectData.root_pose_w)                 | [`root_link_pose_w`](#isaaclab.assets.RigidObjectData.root_link_pose_w)와 동일합니다.                                |
| [`root_pos_w`](#isaaclab.assets.RigidObjectData.root_pos_w)                   | [`root_link_pos_w`](#isaaclab.assets.RigidObjectData.root_link_pos_w)와 동일합니다.                                  |
| [`root_quat_w`](#isaaclab.assets.RigidObjectData.root_quat_w)                 | [`root_link_quat_w`](#isaaclab.assets.RigidObjectData.root_link_quat_w)와 동일합니다.                                |
| [`root_vel_w`](#isaaclab.assets.RigidObjectData.root_vel_w)                   | [`root_com_vel_w`](#isaaclab.assets.RigidObjectData.root_com_vel_w)와 동일합니다.                                    |
| [`root_lin_vel_w`](#isaaclab.assets.RigidObjectData.root_lin_vel_w)           | [`root_com_lin_vel_w`](#isaaclab.assets.RigidObjectData.root_com_lin_vel_w)와 동일합니다.                            |
| [`root_ang_vel_w`](#isaaclab.assets.RigidObjectData.root_ang_vel_w)           | [`root_com_ang_vel_w`](#isaaclab.assets.RigidObjectData.root_com_ang_vel_w)와 동일합니다.                            |
| [`root_lin_vel_b`](#isaaclab.assets.RigidObjectData.root_lin_vel_b)           | [`root_com_lin_vel_b`](#isaaclab.assets.RigidObjectData.root_com_lin_vel_b)와 동일합니다.                            |
| [`root_ang_vel_b`](#isaaclab.assets.RigidObjectData.root_ang_vel_b)           | [`root_com_ang_vel_b`](#isaaclab.assets.RigidObjectData.root_com_ang_vel_b)와 동일합니다.                            |
| [`body_pose_w`](#isaaclab.assets.RigidObjectData.body_pose_w)                 | [`body_link_pose_w`](#isaaclab.assets.RigidObjectData.body_link_pose_w)와 동일합니다.                                |
| [`body_pos_w`](#isaaclab.assets.RigidObjectData.body_pos_w)                   | [`body_link_pos_w`](#isaaclab.assets.RigidObjectData.body_link_pos_w)와 동일합니다.                                  |
| [`body_quat_w`](#isaaclab.assets.RigidObjectData.body_quat_w)                 | [`body_link_quat_w`](#isaaclab.assets.RigidObjectData.body_link_quat_w)와 동일합니다.                                |
| [`body_vel_w`](#isaaclab.assets.RigidObjectData.body_vel_w)                   | [`body_com_vel_w`](#isaaclab.assets.RigidObjectData.body_com_vel_w)와 동일합니다.                                    |
| [`body_lin_vel_w`](#isaaclab.assets.RigidObjectData.body_lin_vel_w)           | [`body_com_lin_vel_w`](#isaaclab.assets.RigidObjectData.body_com_lin_vel_w)와 동일합니다.                            |
| [`body_ang_vel_w`](#isaaclab.assets.RigidObjectData.body_ang_vel_w)           | [`body_com_ang_vel_w`](#isaaclab.assets.RigidObjectData.body_com_ang_vel_w)와 동일합니다.                            |
| [`body_acc_w`](#isaaclab.assets.RigidObjectData.body_acc_w)                   | [`body_com_acc_w`](#isaaclab.assets.RigidObjectData.body_com_acc_w)와 동일합니다.                                    |
| [`body_lin_acc_w`](#isaaclab.assets.RigidObjectData.body_lin_acc_w)           | [`body_com_lin_acc_w`](#isaaclab.assets.RigidObjectData.body_com_lin_acc_w)와 동일합니다.                            |
| [`body_ang_acc_w`](#isaaclab.assets.RigidObjectData.body_ang_acc_w)           | [`body_com_ang_acc_w`](#isaaclab.assets.RigidObjectData.body_com_ang_acc_w)와 동일합니다.                            |
| [`com_pos_b`](#isaaclab.assets.RigidObjectData.com_pos_b)                     | [`body_com_pos_b`](#isaaclab.assets.RigidObjectData.body_com_pos_b)와 동일합니다.                                    |
| [`com_quat_b`](#isaaclab.assets.RigidObjectData.com_quat_b)                   | [`body_com_quat_b`](#isaaclab.assets.RigidObjectData.body_com_quat_b)와 동일합니다.                                  |

#### update(dt: [float](https://docs.python.org/3/library/functions.html#float))

 rigid object의 데이터를 업데이트합니다.

* **Parameters:**
  **dt** – 업데이트에 사용할 시간 간격입니다. 양수여야 합니다.

#### body_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

시뮬레이션 뷰에 의해 파싱된 순서대로 정리된 본체 이름 목록.

#### default_root_state *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

로컬 환경 프레임에서의 기본 루트 상태 `[pos, quat, lin_vel, ang_vel]`. 크기는 (num_instances, 13)입니다.

위치와 쿼터니언은 rigid body의 액터 프레임 기준이며, 선형 및 각속도는 질량 중심 프레임 기준입니다.

#### default_mass *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에서 읽은 기본 질량. 크기는 (num_instances, 1)입니다.

#### default_inertia *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에서 읽은 기본 관성 텐서. 크기는 (num_instances, 9)입니다.

관성 텐서는 질량 중심을 기준으로 rigid body의 액터 프레임에서 표현되어야 하며, 값은 $[I_{xx}, I_{yx}, I_{zx}, I_{xy}, I_{yy}, I_{zy}, I_{xz}, I_{yz}, I_{zz}]$ 순서로 저장됩니다.
그러나 관성 텐서의 대칭성으로 인해 행-주열 및 열-주열 순서는 동등합니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### *property* root_link_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 자세 `[pos, quat]`. 크기는 (num_instances, 7)입니다.

이 양은 월드에 대한 루트 rigid body의 액터 프레임 자세를 나타냅니다. 방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* root_link_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 속도 `[lin_vel, ang_vel]`. 크기는 (num_instances, 6)입니다.

이 양은 월드에 대한 루트 rigid body의 액터 프레임 선형 및 각속도를 포함합니다.

#### *property* root_com_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 자세 `[pos, quat]`. 크기는 (num_instances, 7)입니다.

이 양은 월드에 대한 루트 rigid body의 질량 중심 프레임 자세를 나타냅니다. 방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* root_com_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 속도 `[lin_vel, ang_vel]`. 크기는 (num_instances, 6)입니다.

이 양은 월드에 대한 루트 rigid body의 질량 중심 프레임 선형 및 각속도를 포함합니다.

#### *property* root_state_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 상태 `[pos, quat, lin_vel, ang_vel]`. 크기는 (num_instances, 13)입니다.

위치와 방향은 rigid body의 액터 프레임 기준이며, 선형 및 각속도는 질량 중심 프레임 기준입니다.

#### *property* root_link_state_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 상태 `[pos, quat, lin_vel, ang_vel]`. 크기는 (num_instances, 13)입니다.

위치, 쿼터니언 및 선형/각속도는 월드 기준 루트 rigid body 프레임을 기준으로 제공됩니다. 방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* root_com_state_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 상태 `[pos, quat, lin_vel, ang_vel]`. 크기는 (num_instances, 13)입니다.

위치, 쿼터니언 및 선형/각속도는 월드 기준 루트 rigid body의 질량 중심 프레임을 기준으로 제공됩니다. 질량 중심 프레임은 관성의 주축 방향을 기준으로 합니다.

#### *property* body_link_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 바디 링크 자세 `[pos, quat]`. 크기는 (num_instances, 1, 7)입니다.

이 양은 월드에 대한 rigid body의 액터 프레임 자세를 나타냅니다. 방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_link_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 바디 링크 속도 `[lin_vel, ang_vel]`. 크기는 (num_instances, 1, 6)입니다.

이 양은 월드에 대한 rigid body의 액터 프레임 선형 및 각속도를 포함합니다.

#### *property* body_com_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 바디 질량 중심 자세 `[pos, quat]`. 크기는 (num_instances, 1, 7)입니다.

이 양은 월드에 대한 rigid body의 질량 중심 프레임 자세를 나타냅니다. 방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_com_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 바디 질량 중심 속도 `[lin_vel, ang_vel]`. 크기는 (num_instances, 1, 6)입니다.

이 양은 월드에 대한 rigid body의 질량 중심 프레임 선형 및 각속도를 포함합니다.

#### *property* body_state_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 모든 바디 상태 [pos, quat, lin_vel, ang_vel]. 크기는 (num_instances, 1, 13)입니다.

위치와 방향은 rigid bodies의 액터 프레임 기준이며, 선형 및 각속도는 질량 중심 프레임 기준입니다.

#### *property* body_link_state_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 모든 바디 상태 `[pos, quat, lin_vel, ang_vel]`. 크기는 (num_instances, 1, 13)입니다.

위치, 쿼터니언 및 선형/각속도는 월드 기준 바디 링크 프레임을 기준으로 제공됩니다. 방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_com_state_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 모든 바디 상태 `[pos, quat, lin_vel, ang_vel]`. 크기는 (num_instances, num_bodies, 13)입니다.

위치, 쿼터니언 및 선형/각속도는 월드 기준 바디의 질량 중심 프레임을 기준으로 제공됩니다. 질량 중심 프레임은 관성의 주축이 아닌 링크 방향과 동일한 방향으로 가정됩니다. 방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_com_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 모든 바디 가속도 `[lin_acc, ang_acc]`. 크기는 (num_instances, 1, 6)입니다.

이 양은 월드에 대한 rigid bodies의 질량 중심 프레임 가속도를 나타냅니다.

#### *property* body_com_pose_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

각 바디의 링크 프레임 기준 바디 질량 중심 자세 `[pos, quat]`. 크기는 (num_instances, 1, 7)입니다.

이 양은 rigid body의 링크 프레임에 대한 질량 중심 프레임 자세를 나타냅니다. 방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* projected_gravity_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 프레임에 대한 중력 방향의 투영. 크기는 (num_instances, 3)입니다.

#### *property* heading_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 프레임의 요 방위각 (라디안 단위). 크기는 (num_instances,)입니다.

#### NOTE
이 값은 기준 프레임의 전방 방향이 x축 방향, 즉 $(1, 0, 0)$임을 가정하여 계산됩니다.

#### *property* root_link_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 프레임에서의 루트 링크 선형 속도. 크기는 (num_instances, 3)입니다.

이 양은 rigid body의 액터 프레임을 기준으로 본 루트 rigid body의 액터 프레임 선형 속도를 나타냅니다.

#### *property* root_link_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 월드 프레임에서의 루트 링크 각속도. 크기는 (num_instances, 3)입니다.

이 양은 rigid body의 액터 프레임을 기준으로 본 루트 rigid body의 액터 프레임 각속도를 나타냅니다.

#### *property* root_com_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

베이스 프레임에서 질량 중심을 기준선형 속도. 모양은 (num_instances, 3)입니다.

이량은 강체(리저드 바디)의 중심 질량 프레임에 대해 강체(리저드 바디)의 액터 프레임에 대한 선형 속도입니다.

#### *property* root_com_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

베이스 월드 프레임에서 질량 중심을 기준각속도. 모양은 (num_instances, 3)입니다.

이량은 강체(리저드 바디)의 중심 질량 프레임에 대해 강체(리저드 바디)의 액터 프레임에 대한 각속도입니다.

#### *property* root_link_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 루트 링크 위치. 모양은 (num_instances, 3)입니다.

이량은 월드에 대한 강체(리저드 바디)의 액터 프레임의 위치입니다.

#### *property* root_link_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 루트 링크 방향 (w, x, y, z). 모양은 (num_instances, 4)입니다.

이량은 강체(리저드 바디)의 액터 프레임의 방향입니다.

#### *property* root_link_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 루트 선형 속도. 모양은 (num_instances, 3)입니다.

이량은 월드에 대한 강체(리저드 바디)의 액터 프레임의 선형 속도입니다.

#### *property* root_link_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 루트 링크 각속도. 모양은 (num_instances, 3)입니다.

이량은 월드에 대한 강체(리저드 바디)의 액터 프레임의 각속도입니다.

#### *property* root_com_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 루트 질량 중심 위치. 모양은 (num_instances, 3)입니다.

이량은 월드에 대한 강체(리저드 바디)의 액터 프레임의 위치입니다.

#### *property* root_com_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 루트 질량 중심 방향 (w, x, y, z). 모양은 (num_instances, 4)입니다.

이량은 월드에 대한 강체(리저드 바디)의 액터 프레임의 방향입니다.

#### *property* root_com_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 루트 질량 중심 선형 속도. 모양은 (num_instances, 3)입니다.

이량은 월드에 대한 강체(리저드 바디)의 중심 질량 프레임의 선형 속도입니다.

#### *property* root_com_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 루트 질량 중심 각속도. 모양은 (num_instances, 3)입니다.

이량은 월드에 대한 강체(리저드 바디)의 중심 질량 프레임의 각속도입니다.

#### *property* body_link_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 바디의 위치. 모양은 (num_instances, 1, 3)입니다.

이량은 월드에 대한 강체(리저드 바디)의 액터 프레임의 위치입니다.

#### *property* body_link_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 바디의 방향 (w, x, y, z). 모양은 (num_instances, 1, 4)입니다.

이량은 월드에 대한 강체(리저드 바디)의 액터 프레임의 방향입니다.

#### *property* body_link_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 바디의 선형 속도. 모양은 (num_instances, 1, 3)입니다.

이량은 월드에 대한 강체(리저드 바디)의 질량 중심 프레임의 선형 속도입니다.

#### *property* body_link_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 바디의 각속도. 모양은 (num_instances, 1, 3)입니다.

이량은 월드에 대한 강체(리저드 바디)의 질량 중심 프레임의 각속도입니다.

#### *property* body_com_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 바디의 위치. 모양은 (num_instances, 1, 3)입니다.

이량은 강체(리저드 바디)의 액터 프레임의 위치입니다.

#### *property* body_com_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 관성 원리 축의 방향 (w, x, y, z). 모양은 (num_instances, 1, 4)입니다.

이량은 강체(리저드 바디)의 액터 프레임의 방향입니다.

#### *property* body_com_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 바디의 선형 속도. 모양은 (num_instances, 1, 3)입니다.

이량은 강체(리저드 바디)의 질량 중심 프레임의 선형 속도입니다.

#### *property* body_com_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 바디의 각속도. 모양은 (num_instances, 1, 3)입니다.

이량은 강체(리저드 바디)의 질량 중심 프레임의 각속도입니다.

#### *property* body_com_lin_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 바디의 선형 가속도. 모양은 (num_instances, 1, 3)입니다.

이량은 강체(리저드 바디)의 질량 중심 프레임의 선형 가속도입니다.

#### *property* body_com_ang_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서 모든 바디의 각 가속도. 모양은 (num_instances, 1, 3)입니다.

이량은 강체(리저드 바디)의 질량 중심 프레임의 각 가속도입니다.

#### *property* body_com_pos_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

모든 바디의 질량 중심 위치 (해당 링크 프레임 기준). 모양은 (num_instances, 1, 3)입니다.

이량은 해당 바디의 링크 프레임에 대한 질량 중심의 위치입니다.

#### *property* body_com_quat_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

모든 바디의 관성 원리 축의 방향 (w, x, y, z) (해당 링크 프레임 기준). 모양은 (num_instances, 1, 4)입니다.

이량은 해당 바디의 링크 프레임에 대한 관성 원리 축의 방향입니다.

#### *property* root_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_link_pose_w`](#isaaclab.assets.RigidObjectData.root_link_pose_w)와 동일합니다.

#### *property* root_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_link_pos_w`](#isaaclab.assets.RigidObjectData.root_link_pos_w)와 동일합니다.

#### *property* root_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_link_quat_w`](#isaaclab.assets.RigidObjectData.root_link_quat_w)와 동일합니다.

#### *property* root_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_vel_w`](#isaaclab.assets.RigidObjectData.root_com_vel_w)와 동일합니다.

#### *property* root_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_lin_vel_w`](#isaaclab.assets.RigidObjectData.root_com_lin_vel_w)와 동일합니다.

#### *property* root_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_ang_vel_w`](#isaaclab.assets.RigidObjectData.root_com_ang_vel_w)와 동일합니다.

#### *property* root_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_lin_vel_b`](#isaaclab.assets.RigidObjectData.root_com_lin_vel_b)와 동일합니다.

#### *property* root_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_ang_vel_b`](#isaaclab.assets.RigidObjectData.root_com_ang_vel_b)와 동일합니다.

#### *property* body_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_link_pose_w`](#isaaclab.assets.RigidObjectData.body_link_pose_w)와 동일합니다.

#### *property* body_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_link_pos_w`](#isaaclab.assets.RigidObjectData.body_link_pos_w)와 동일합니다.

#### *property* body_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_link_quat_w`](#isaaclab.assets.RigidObjectData.body_link_quat_w)와 동일합니다.

#### *property* body_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_vel_w`](#isaaclab.assets.RigidObjectData.body_com_vel_w)와 동일합니다.

#### *property* body_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_lin_vel_w`](#isaaclab.assets.RigidObjectData.body_com_lin_vel_w)와 동일합니다.

#### *property* body_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_ang_vel_w`](#isaaclab.assets.RigidObjectData.body_com_ang_vel_w)와 동일합니다.

#### *property* body_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_acc_w`](#isaaclab.assets.RigidObjectData.body_com_acc_w)와 동일합니다.

#### *property* body_lin_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_lin_acc_w`](#isaaclab.assets.RigidObjectData.body_com_lin_acc_w)와 동일합니다.

#### *property* body_ang_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

Same as [`body_com_ang_acc_w`](#isaaclab.assets.RigidObjectData.body_com_ang_acc_w).

#### *property* com_pos_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

Same as [`body_com_pos_b`](#isaaclab.assets.RigidObjectData.body_com_pos_b).

#### *property* com_quat_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

Same as [`body_com_quat_b`](#isaaclab.assets.RigidObjectData.body_com_quat_b).

### *class* isaaclab.assets.RigidObjectCfg

Bases: [`AssetBaseCfg`](#isaaclab.assets.AssetBaseCfg)

 rigid object에 대한 구성 매개변수입니다.

**Classes:**

| [`InitialStateCfg`](#isaaclab.assets.RigidObjectCfg.InitialStateCfg)   | rigid body의 초기 상태.   |
|------------------------------------------------------------------------|---------------------------|

**Attributes:**

| [`prim_path`](#isaaclab.assets.RigidObjectCfg.prim_path)             | 에셋의 Prim 경로(또는 표현식).              |
|----------------------------------------------------------------------|--------------------------------------------------|
| [`spawn`](#isaaclab.assets.RigidObjectCfg.spawn)                     | 에셋의 Spawn 구성.                               |
| [`collision_group`](#isaaclab.assets.RigidObjectCfg.collision_group) | 에셋의 충돌 그룹.                                |
| [`debug_vis`](#isaaclab.assets.RigidObjectCfg.debug_vis)             | 에셋에 대한 디버그 시각화 활성화 여부.           |
| [`init_state`](#isaaclab.assets.RigidObjectCfg.init_state)           | rigid object의 초기 상태.                        |

#### *class* InitialStateCfg

Bases: [`InitialStateCfg`](#isaaclab.assets.DeformableObjectCfg.InitialStateCfg)

 rigid body의 초기 상태.

**Attributes:**

| [`lin_vel`](#isaaclab.assets.RigidObjectCfg.InitialStateCfg.lin_vel)   | 시뮬레이션 월드 프레임에서 루트의 선형 속도.                  |
|------------------------------------------------------------------------|------------------------------------------------------------|
| [`ang_vel`](#isaaclab.assets.RigidObjectCfg.InitialStateCfg.ang_vel)   | 시뮬레이션 월드 프레임에서 루트의 각속도.                    |
| [`pos`](#isaaclab.assets.RigidObjectCfg.InitialStateCfg.pos)           | 시뮬레이션 월드 프레임에서 루트의 위치.                     |
| [`rot`](#isaaclab.assets.RigidObjectCfg.InitialStateCfg.rot)           | 시뮬레이션 월드 프레임에서 루트의 쿼터니언 회전 (w, x, y, z). |

#### lin_vel *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 선형 속도. 기본값은 (0.0, 0.0, 0.0)입니다.

#### ang_vel *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 각속도. 기본값은 (0.0, 0.0, 0.0)입니다.

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 위치. 기본값은 (0.0, 0.0, 0.0)입니다.

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 쿼터니언 회전 (w, x, y, z).
기본값은 (1.0, 0.0, 0.0, 0.0)입니다.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

에셋의 Prim 경로(또는 표현식).

#### NOTE
표현식은 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`을 포함할 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot`은 `/World/envs/env_.*/Robot`으로 대체됩니다.

#### spawn *: [SpawnerCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.SpawnerCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

에셋의 Spawn 구성. 기본값은 None입니다.

None인 경우, 에셋 클래스는 Prim을 생성하지 않습니다. 대신, 에셋이 이미 씬에 존재한다고 가정합니다.

#### collision_group *: Literal[0, -1]*

에셋의 충돌 그룹. 기본값은 `0`입니다.

* `-1`: 전역 충돌 그룹(씬의 모든 에셋과 충돌).
* `0`: 로컬 충돌 그룹(같은 환경의 다른 에셋과 충돌).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋에 대한 디버그 시각화 활성화 여부. 기본값은 `False`입니다.

#### init_state *: [InitialStateCfg](#isaaclab.assets.RigidObjectCfg.InitialStateCfg)*

rigid object의 초기 상태. 기본값은 속도가 0인 초기자세입니다.

## Rigid Object Collection

### *class* isaaclab.assets.RigidObjectCollection

Bases: [`AssetBase`](#isaaclab.assets.AssetBase)

 rigid object 컬렉션 클래스입니다.

이 클래스는 시뮬레이션에서 rigid object 컬렉션을 표현하며, `(env_ids, object_ids)` 형태의 배치된 API를 사용하여 rigid object의 상태에 접근하고 수정할 수 있습니다.

컬렉션의 각 rigid body에 대해, asset의 루트 prim에는 [USD RigidBodyAPI](https://openusd.org/dev/api/class_usd_physics_rigid_body_a_p_i.html)를 적용해야 합니다. 이 API는 rigid body의 시뮬레이션 속성을 정의하는 데 사용됩니다. 시뮬레이션 재생 시, 물리 엔진은 rigid body를 자동으로 등록하고 해당하는 rigid body 핸들을 생성합니다. 이 핸들은 [`root_physx_view`](#isaaclab.assets.RigidObjectCollection.root_physx_view) 속성을 통해 접근할 수 있습니다.

컬렉션의 rigid object는 [`rigid_objects`](#isaaclab.assets.RigidObjectCollectionCfg.rigid_objects) 사전의 키를 통해 유일하게 식별됩니다. 이 식별 방식은 [`RigidObjectCollectionCfg`](#isaaclab.assets.RigidObjectCollectionCfg) 구성 클래스에 따릅니다. 이는 [`RigidObject`](#isaaclab.assets.RigidObject) 클래스와의 차이점입니다. 후자는 [USD RigidBodyAPI](https://openusd.org/dev/api/class_usd_physics_rigid_body_a_p_i.html)가 적용된 Xform의 이름을 통해 rigid object를 식별하기 때문입니다. 전자의 경우, [`rigid_objects`](#isaaclab.assets.RigidObjectCollectionCfg.rigid_objects) 사전에 동일한 rigid object가 여러 번 포함될 수 있어 모호성이 발생할 수 있으므로, 이름 기반 식별 방식은 사용할 수 없습니다.

**Methods:**

| [`__init__`](#isaaclab.assets.RigidObjectCollection.__init__)(cfg)                                                             | rigid object 컬렉션을 초기화합니다.                                                                                                                               |
|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`reset`](#isaaclab.assets.RigidObjectCollection.reset)([env_ids, object_ids])                                                 | 선택된 환경과 오브젝트의 내부 버퍼를 모두 재설정합니다.                                                                                                     |
| [`write_data_to_sim`](#isaaclab.assets.RigidObjectCollection.write_data_to_sim)()                                              | 시뮬레이션에 외부 렌치를 씁니다.                                                                                                                              |
| [`update`](#isaaclab.assets.RigidObjectCollection.update)(dt)                                                                  | 내부 버퍼를 업데이트합니다.                                                                                                                                          |
| [`find_objects`](#isaaclab.assets.RigidObjectCollection.find_objects)(name_keys[, preserve_order])                             | 이름 키를 기준으로 컬렉션에서 오브젝트를 찾습니다.                                                                                                                |
| [`write_object_state_to_sim`](#isaaclab.assets.RigidObjectCollection.write_object_state_to_sim)(object_state[, ...])           | 선택된 환경 및 오브젝트 인덱스에 대한 오브젝트 상태를 시뮬레이션에 설정합니다.                                                                                |
| [`write_object_com_state_to_sim`](#isaaclab.assets.RigidObjectCollection.write_object_com_state_to_sim)(object_state)          | 선택된 환경 인덱스에 대한 오브젝트 질량 중심을 기준으로 한 상태를 시뮬레이션에 설정합니다.                                                                            |
| [`write_object_link_state_to_sim`](#isaaclab.assets.RigidObjectCollection.write_object_link_state_to_sim)(object_state)        | 선택된 환경 인덱스에 대한 오브젝트 링크 상태를 시뮬레이션에 설정합니다.                                                                                      |
| [`write_object_pose_to_sim`](#isaaclab.assets.RigidObjectCollection.write_object_pose_to_sim)(object_pose[, ...])              | 선택된 환경 및 오브젝트 인덱스에 대한 오브젝트 포즈를 시뮬레이션에 설정합니다.                                                                                 |
| [`write_object_link_pose_to_sim`](#isaaclab.assets.RigidObjectCollection.write_object_link_pose_to_sim)(object_pose[, ...])    | 선택된 환경 및 객체 인덱스에 대한 객체 포즈를 시뮬레이션에 설정합니다.                                                                                 |
| [`write_object_com_pose_to_sim`](#isaaclab.assets.RigidObjectCollection.write_object_com_pose_to_sim)(object_pose[, ...])      | 선택된 환경 인덱스에 대한 객체 질량 중심 포즈를 시뮬레이션에 설정합니다.                                                                             |
| [`write_object_velocity_to_sim`](#isaaclab.assets.RigidObjectCollection.write_object_velocity_to_sim)(object_velocity)         | 선택된 환경 및 객체 인덱스에 대한 객체 속도를 시뮬레이션에 설정합니다.                                                                             |
| [`write_object_com_velocity_to_sim`](#isaaclab.assets.RigidObjectCollection.write_object_com_velocity_to_sim)(object_velocity) | 선택된 환경 및 객체 인덱스에 대한 객체 질량 중심 속도를 시뮬레이션에 설정합니다.                                                              |
| [`write_object_link_velocity_to_sim`](#isaaclab.assets.RigidObjectCollection.write_object_link_velocity_to_sim)(...[, ...])    | 선택된 환경 인덱스에 대한 객체 링크 속도를 시뮬레이션에 설정합니다.                                                                                   |
| [`set_external_force_and_torque`](#isaaclab.assets.RigidObjectCollection.set_external_force_and_torque)(forces, torques)       | 객체의 본체에 로컬 프레임에서 외부 힘과 토크를 적용하도록 설정합니다.                                                                                   |
| [`reshape_view_to_data`](#isaaclab.assets.RigidObjectCollection.reshape_view_to_data)(data)                                    | [`root_physx_view`](#isaaclab.assets.RigidObjectCollection.root_physx_view)에서 오는 데이터를 재형성하고 (num_instances, num_objects, data_dim)으로 정리합니다. |
| [`reshape_data_to_view`](#isaaclab.assets.RigidObjectCollection.reshape_data_to_view)(data)                                    | 데이터를 [`root_physx_view`](#isaaclab.assets.RigidObjectCollection.root_physx_view)의 데이터와 일관되게 재형성하고 정리합니다.                   |
| [`set_debug_vis`](#isaaclab.assets.RigidObjectCollection.set_debug_vis)(debug_vis)                                             | 자산 데이터 시각화 여부를 설정합니다.                                                                                                                             |
| [`set_visibility`](#isaaclab.assets.RigidObjectCollection.set_visibility)(visible[, env_ids])                                  | 자산에 해당하는 프리미스의 가시성을 설정합니다.                                                                                                           |

**속성:**

| [`cfg`](#isaaclab.assets.RigidObjectCollection.cfg)                                                     | 강체 객체 컬렉션의 구성 인스턴스입니다.   |
|---------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`data`](#isaaclab.assets.RigidObjectCollection.data)                                                   | 자산과 관련된 데이터.                                |
| [`num_instances`](#isaaclab.assets.RigidObjectCollection.num_instances)                                 | 컬렉션의 인스턴스 수.                    |
| [`num_objects`](#isaaclab.assets.RigidObjectCollection.num_objects)                                     | 컬렉션에 포함된 객체 수.                      |
| [`object_names`](#isaaclab.assets.RigidObjectCollection.object_names)                                   | 강체 객체 컬렉션의 객체 이름 순서.  |
| [`root_physx_view`](#isaaclab.assets.RigidObjectCollection.root_physx_view)                             | 강체 객체 컬렉션을 위한 강체 뷰 (PhysX).    |
| [`instantaneous_wrench_composer`](#isaaclab.assets.RigidObjectCollection.instantaneous_wrench_composer) | 순간적인 휸스 작곡가.                            |
| [`permanent_wrench_composer`](#isaaclab.assets.RigidObjectCollection.permanent_wrench_composer)         | 영구적인 휸스 작곡가.                                |
| [`device`](#isaaclab.assets.RigidObjectCollection.device)                                               | 계산을 위한 메모리 장치.                            |
| [`has_debug_vis_implementation`](#isaaclab.assets.RigidObjectCollection.has_debug_vis_implementation)   | 자산에 디버그 시각화 구현이 있는지 여부.   |
| [`is_initialized`](#isaaclab.assets.RigidObjectCollection.is_initialized)                               | 자산이 초기화되었는지 여부.                         |

#### \_\_init_\_(cfg: [RigidObjectCollectionCfg](#isaaclab.assets.RigidObjectCollectionCfg))

강체 객체 컬렉션을 초기화합니다.

* **Parameters:**
  **cfg** – 구성 인스턴스.

#### cfg *: [RigidObjectCollectionCfg](#isaaclab.assets.RigidObjectCollectionCfg)*

강체 객체 컬렉션의 구성 인스턴스입니다.

#### *property* data *: [RigidObjectCollectionData](#isaaclab.assets.RigidObjectCollectionData)*

자산과 관련된 데이터.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

컬렉션의 인스턴스 수.

#### *property* num_objects *: [int](https://docs.python.org/3/library/functions.html#int)*

컬렉션에 포함된 객체 수.

이 값은 컬렉션에 포함된 강체의 고유한 수에 해당합니다.

#### *property* object_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

강체 객체 컬렉션의 객체 이름 순서.

#### *property* root_physx_view *: omni.physics.tensors.impl.api.RigidBodyView*

강체 객체 컬렉션을 위한 강체 뷰 (PhysX).

#### NOTE
이 뷰를 사용할 때는 주의가 필요합니다. 특정 방식으로 텐서를 처리해야 합니다.

#### *property* instantaneous_wrench_composer *: WrenchComposer*

순간적인 휸스 작곡가.

`WrenchComposer` 인스턴스를 반환합니다. 이 작곡가에 추가되거나 설정된 휸스는 현재 시뮬레이션 단계에서만 유효합니다. 시뮬레이션 단계가 끝날 때 이 객체에 설정된 휸스는 폐기됩니다. 이는 드래그 힘과 같이 매번 변하는 힘을 적용하는 데 유용합니다.

#### *property* permanent_wrench_composer *: WrenchComposer*

영구적인 휸스 작곡가.

`WrenchComposer` 인스턴스를 반환합니다. 이 작곡가에 추가되거나 설정된 휸스는 지속적이며 시뮬레이션의 모든 단계에서 적용됩니다. 이는 모터의 추력과 같이 일정 기간 동안 일정한 힘을 적용하는 데 유용합니다.

#### reset(env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경과 객체의 모든 내부 버퍼를 재설정합니다.

* **Parameters:**
  * **env_ids** – 재설정할 객체의 인덱스. 기본값은 None(모든 인스턴스).
  * **object_ids** – 재설정할 객체의 인덱스. 기본값은 None(모든 객체).

#### write_data_to_sim()

시뮬레이션에 외부 휸스를 기록합니다.

#### NOTE
외부 휸스를 시뮬레이션에 기록하는 것은 이 함수가 시뮬레이션 단계 전에 호출되기 때문입니다.これにより外部의ワインが各シミュレーションステップで適用されることを確実にします。

#### update(dt: [float](https://docs.python.org/3/library/functions.html#float))

내부 버퍼를 업데이트합니다.

시간 단계 `dt`는 시뮬레이터에서 제공하지 않는 관절 가속도와 같은 양의 수치적 미분을 계산하는 데 사용됩니다.

* **Parameters:**
  **dt** – 마지막 `update` 호출로부터 경과한 시간.

#### find_objects(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

이름 키를 기반으로 컬렉션에서 객체를 찾습니다.

이름 매칭에 대한 자세한 내용은 `isaaclab.utils.string_utils.resolve_matching_names()` 함수를 확인하세요.

* **Parameters:**
  * **name_keys** – 객체 이름과 일치시키는 정규 표현식 또는 정규 표현식 목록.
  * **preserve_order** – 출력에서 이름 키의 순서를 유지할지 여부. 기본값은 False입니다.
* **Returns:**
  객체 인덱스와 이름을 포함하는 튜플.

#### write_object_state_to_sim(object_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 및 객체 인덱스에 대한 객체 상태를 시뮬레이션에 설정합니다.

오브젝트 상태는 카르테시안 위치, (w, x, y, z) 사원수 방향, 그리고 선형 및 각속도로 구성됩니다. 모든 양은 시뮬레이션 프레임 기준입니다.

* **Parameters:**
  * **object_state** – 시뮬레이션 프레임의 오브젝트 상태. Shape은 (len(env_ids), len(object_ids), 13)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.
  * **object_ids** – 오브젝트 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_object_com_state_to_sim(object_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 오브젝트 질량중심 상태를 시뮬레이션에 설정합니다.

오브젝트 상태는 카르테시안 위치, (w, x, y, z) 사원수 방향, 그리고 선형 및 각속도로 구성됩니다. 모든 양은 시뮬레이션 프레임 기준입니다.

* **Parameters:**
  * **object_state** – 시뮬레이션 프레임의 오브젝트 상태. Shape은 (len(env_ids), len(object_ids), 13)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.
  * **object_ids** – 오브젝트 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_object_link_state_to_sim(object_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 오브젝트 링크 상태를 시뮬레이션에 설정합니다.

오브젝트 상태는 카르테시안 위치, (w, x, y, z) 사원수 방향, 그리고 선형 및 각속도로 구성됩니다. 모든 양은 시뮬레이션 프레임 기준입니다.

* **Parameters:**
  * **object_state** – 시뮬레이션 프레임의 오브젝트 상태. Shape은 (len(env_ids), len(object_ids), 13)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.
  * **object_ids** – 오브젝트 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_object_pose_to_sim(object_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 및 오브젝트 인덱스에 대한 오브젝트 포즈를 시뮬레이션에 설정합니다.

오브젝트 포즈는 카르테시안 위치와 (w, x, y, z) 사원수 방향으로 구성됩니다.

* **Parameters:**
  * **object_pose** – 시뮬레이션 프레임의 오브젝트 포즈. Shape은 (len(env_ids), len(object_ids), 7)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.
  * **object_ids** – 오브젝트 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_object_link_pose_to_sim(object_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 및 오브젝트 인덱스에 대한 오브젝트 포즈를 시뮬레이션에 설정합니다.

오브젝트 포즈는 카르테시안 위치와 (w, x, y, z) 사원수 방향으로 구성됩니다.

* **Parameters:**
  * **object_pose** – 시뮬레이션 프레임의 오브젝트 포즈. Shape은 (len(env_ids), len(object_ids), 7)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.
  * **object_ids** – 오브젝트 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_object_com_pose_to_sim(object_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 오브젝트 질량중심 포즈를 시뮬레이션에 설정합니다.

오브젝트 포즈는 카르테시안 위치와 (w, x, y, z) 사원수 방향으로 구성됩니다. 방향은 관성 텐서의 주축 방향입니다.

* **Parameters:**
  * **object_pose** – 시뮬레이션 프레임의 오브젝트 포즈. Shape은 (len(env_ids), len(object_ids), 7)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.
  * **object_ids** – 오브젝트 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_object_velocity_to_sim(object_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 및 오브젝트 인덱스에 대한 오브젝트 속도를 시뮬레이션에 설정합니다.

* **Parameters:**
  * **object_velocity** – 시뮬레이션 프레임의 오브젝트 속도. Shape은 (len(env_ids), len(object_ids), 6)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.
  * **object_ids** – 오브젝트 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_object_com_velocity_to_sim(object_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 및 오브젝트 인덱스에 대한 오브젝트 질량중심 속도를 시뮬레이션에 설정합니다.

* **Parameters:**
  * **object_velocity** – 시뮬레이션 프레임의 오브젝트 속도. Shape은 (len(env_ids), len(object_ids), 6)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.
  * **object_ids** – 오브젝트 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_object_link_velocity_to_sim(object_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 오브젝트 링크 속도를 시뮬레이션에 설정합니다.

속도는 순서대로 선형 속도 (x, y, z)와 각속도 (x, y, z)로 구성됩니다.
NOTE: 이 함수는 오브젝트의 프레임 속도를 설정하며, 질량중심 속도가 아닌 점을 주의하세요.

* **Parameters:**
  * **object_velocity** – 시뮬레이션 프레임의 오브젝트 속도. Shape은 (len(env_ids), len(object_ids), 6)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.
  * **object_ids** – 오브젝트 인덱스. None이면 모든 인덱스가 사용됩니다.

#### set_external_force_and_torque(forces: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), torques: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, object_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_global: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

오브젝트의 바디에 로컬 프레임 기준으로 외부 힘과 토크를 적용합니다.

많은 애플리케이션에서 rigid body에 가해지는 외부 힘을 일정 기간 동안 일정하게 유지하고자 할 수 있습니다(예: 정책 제어 중). 이 함수는 외부 힘과 토크를 버퍼에 저장하여 매 시뮬레이션 스텝에서 적용할 수 있도록 합니다.

#### NOTE
이 함수는 외부 렌치를 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 외부 렌치를 적용하려면 시뮬레이션 스텝 직전에 [`write_data_to_sim()`](#isaaclab.assets.RigidObjectCollection.write_data_to_sim) 함수를 호출하세요.

* **Parameters:**
  * **forces** – 바디의 로컬 프레임에서의 외부 힘. 형태는 (len(env_ids), len(object_ids), 3)입니다.
  * **torques** – 바디의 로컬 프레임에서의 외부 토크. 형태는 (len(env_ids), len(object_ids), 3)입니다.
  * **positions** – 바디의 로컬 프레임에서의 외부 렌치 위치. 형태는 (len(env_ids), len(object_ids), 3)입니다.
  * **object_ids** – 외부 렌치를 적용할 객체 인덱스. 기본값은 None(모든 객체).
  * **env_ids** – 외부 렌치를 적용할 환경 인덱스. 기본값은 None(모든 인스턴스).
  * **is_global** – 외부 렌치를 글로벌 프레임에 적용할지 여부. 기본값은 False. False로 설정된 경우,
    외부 렌치는 바디의 링크 프레임에 적용됩니다.

#### reshape_view_to_data(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

[`root_physx_view`](#isaaclab.assets.RigidObjectCollection.root_physx_view)에서 오는 데이터를 재형상하고 배열하여
(num_instances, num_objects, data_dim) 형태를 만듭니다.

* **매개변수:**
  **data** – [`root_physx_view`](#isaaclab.assets.RigidObjectCollection.root_physx_view)에서 오는 데이터. 형태는 (num_instances \* num_objects, data_dim).
* **반환값:**
  재형상된 데이터. 형태는 (num_instances, num_objects, data_dim).

#### reshape_data_to_view(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

[`root_physx_view`](#isaaclab.assets.RigidObjectCollection.root_physx_view)의 데이터와 일관되도록 데이터를 재형상하고 배열합니다.

* **매개변수:**
  **data** – 재형상할 데이터. 형태는 (num_instances, num_objects, data_dim).
* **반환값:**
  재형상된 데이터. 형태는 (num_instances \* num_objects, data_dim).

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 위한 메모리 디바이스.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 디버그 시각화를 구현했는지 여부.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 초기화되었는지 여부.

자산이 초기화되었을 경우 True, 그렇지 않을 경우 False를 반환합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

자산 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 자산 데이터를 시각화할지 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 자산이 디버그 시각화를 지원하지 않는 경우 False.

#### set_visibility(visible: [bool](https://docs.python.org/3/library/functions.html#bool), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

자산에 해당하는 프림의 가시성을 설정합니다.

이 작업은 USD 스테이지에서 자산에 해당하는 프림의 가시성에 영향을 미칩니다. 시뮬레이터에서 자산의 가시성을 토글하는 데 유용합니다. 예를 들어, 사용하지 않을 때 자산을 숨겨 렌더링 오버헤드를 줄일 수 있습니다.

#### 참고
이 작업은 PXR API를 사용하여 프림의 가시성을 설정합니다. 따라서 프림 수가 많을 경우 오버헤드가 발생할 수 있습니다.

* **매개변수:**
  * **visible** – 프림을 보이게 할지 여부.
  * **env_ids** – 가시성을 설정할 객체 인덱스. 기본값은 None(모든 인스턴스).

### *class* isaaclab.assets.RigidObjectCollectionData

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

강성 객체 컬렉션용 데이터 컨테이너.

이 클래스는 시뮬레이션에서 강성 객체 컬렉션의 데이터를 포함합니다. 데이터에는 컬렉션 내의 모든 바디의 상태가 포함됩니다. 데이터는 별도로 지정되지 않는 한 시뮬레이션 월드 프레임에 저장됩니다. 데이터는 (num_instances, num_objects, data_size) 순서로 저장되며, 여기서 data_size는 데이터의 크기입니다.

강성 바디의 경우 두 가지参照 프레임이 사용됩니다:

- 액터 프레임: 강성 바디 프림의参照 프레임. 일반적으로 강성 바디 스키마가 있는 Xform 프림에 해당합니다.
- 질량 중심 프레임: 강성 바디의 질량 중심을 기준으로 하는参照 프레임.

시뮬레이션의 설정에 따라 액터 프레임과 질량 중심 프레임이 동일할 수 있습니다. 데이터를 해석할 때 이를 고려해야 합니다.

데이터는 지연 업데이트 방식으로, 데이터가 접근될 때만 업데이트됩니다. 이는 데이터 계산 또는 검색이 비용이 많이 드는 경우 유용합니다. 데이터는 버퍼의 타임스탬프가 현재 시뮬레이션 타임스탬프보다 오래되었을 때 업데이트됩니다. 타임스탬프는 데이터가 업데이트될 때마다 갱신됩니다.

**메서드:**

| [`update`](#isaaclab.assets.RigidObjectCollectionData.update)(dt)   | 강성 객체 컬렉션의 데이터를 업데이트합니다.   |
|---------------------------------------------------------------------|-----------------------------------------------------|

**속성:**

| [`object_names`](#isaaclab.assets.RigidObjectCollectionData.object_names)                   | 시뮬레이션 뷰에서 파싱된 순서대로의 객체 이름.                                               |
|---------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| [`default_object_state`](#isaaclab.assets.RigidObjectCollectionData.default_object_state)   | 로컬 환경 프레임에서의 기본 객체 상태 `[pos, quat, lin_vel, ang_vel]`.                       |
| [`default_mass`](#isaaclab.assets.RigidObjectCollectionData.default_mass)                   | 시뮬레이션에서 읽은 기본 객체 질량.                                                          |
| [`default_inertia`](#isaaclab.assets.RigidObjectCollectionData.default_inertia)             | 시뮬레이션에서 읽은 기본 객체 관성 텐서.                                                |
| [`object_link_pose_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_pose_w)       | 시뮬레이션 월드 프레임에서의 객체 링크 자세 `[pos, quat]`.                                              |
| [`object_link_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_vel_w)         | 시뮬레이션 월드 프레임에서의 객체 링크 속도 `[lin_vel, ang_vel]`.                                   |
| [`object_com_pose_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_pose_w)         | 시뮬레이션 월드 프레임에서의 객체 질량 중심 자세 `[pos, quat]`.                                    |
| [`object_com_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_vel_w)           | 시뮬레이션 월드 프레임에서의 객체 질량 중심 속도 `[lin_vel, ang_vel]`.                         |
| [`object_state_w`](#isaaclab.assets.RigidObjectCollectionData.object_state_w)               | 시뮬레이션 월드 프레임에서의 객체 상태 `[pos, quat, lin_vel, ang_vel]`.                                |
| [`object_link_state_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_state_w)     | 시뮬레이션 월드 프레임에서의 객체 질량 중심 상태 `[pos, quat, lin_vel, ang_vel]`.                 |
| [`object_com_state_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_state_w)       | 시뮬레이션 월드 프레임에서의 객체 상태 `[pos, quat, lin_vel, ang_vel]`.                                |
| [`object_com_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_acc_w)           | 모든 객체의 가속도.                                                                           |
| [`object_com_pose_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_pose_b)         | 객체 각각의 링크 프레임에서의 질량 중심 자세 `[pos, quat]`.                        |
| [`projected_gravity_b`](#isaaclab.assets.RigidObjectCollectionData.projected_gravity_b)     | 베이스 프레임에 대한 중력 방향의 투영.                                                     |
| [`heading_w`](#isaaclab.assets.RigidObjectCollectionData.heading_w)                         | 베이스 프레임의 yaw 방향 (라디안 단위).                                                            |
| [`object_link_lin_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_link_lin_vel_b) | 베이스 프레임에서의 객체 링크 선형 속도.                                                             |
| [`object_link_ang_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_link_ang_vel_b) | 베이스 월드 프레임에서의 객체 링크 각속도.                                                      |
| [`object_com_lin_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_lin_vel_b)   | 베이스 프레임에서의 객체 질량 중심 선형 속도.                                                   |
| [`object_com_ang_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_ang_vel_b)   | 베이스 월드 프레임에서의 객체 질량 중심 각속도.                                            |
| [`object_link_pos_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_pos_w)         | 시뮬레이션 월드 프레임에서의 객체 링크 위치.                                                        |
| [`object_link_quat_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_quat_w)       | 시뮬레이션 월드 프레임에서의 객체 링크 방향 (w, x, y, z).                                        |
| [`object_link_lin_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_lin_vel_w) | 시뮬레이션 월드 프레임에서의 객체 링크 선형 속도.                                                 |
| [`object_link_ang_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_ang_vel_w) | 시뮬레이션 월드 프레임에서의 객체 링크 각속도입니다.                                                |
| [`object_com_pos_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_pos_w)           | 시뮬레이션 월드 프레임에서의 객체 질량 중심 위치입니다.                                              |
| [`object_com_quat_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_quat_w)         | 시뮬레이션 월드 프레임에서의 객체 질량 중심 방향 (w, x, y, z)입니다.                              |
| [`object_com_lin_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_lin_vel_w)   | 시뮬레이션 월드 프레임에서의 객체 질량 중심 선형 속도입니다.                                       |
| [`object_com_ang_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_ang_vel_w)   | 시뮬레이션 월드 프레임에서의 객체 질량 중심 각속도입니다.                                      |
| [`object_com_lin_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_lin_acc_w)   | 시뮬레이션 월드 프레임에서의 객체 질량 중심 선형 가속도입니다.                                   |
| [`object_com_ang_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_ang_acc_w)   | 시뮬레이션 월드 프레임에서의 객체 질량 중심 각 가속도입니다.                                  |
| [`object_com_pos_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_pos_b)           | 각각의 몸체 프레임에서의 모든 몸체의 질량 중심입니다.                             |
| [`object_com_quat_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_quat_b)         | 시뮬레이션 월드 프레임에서의 모든 몸체의 관성 주축 방향 (w,x,y,z)입니다. |
| [`object_pose_w`](#isaaclab.assets.RigidObjectCollectionData.object_pose_w)                 | [`object_link_pose_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_pose_w)와 동일합니다.         |
| [`object_pos_w`](#isaaclab.assets.RigidObjectCollectionData.object_pos_w)                   | [`object_link_pos_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_pos_w)와 동일합니다.           |
| [`object_quat_w`](#isaaclab.assets.RigidObjectCollectionData.object_quat_w)                 | [`object_link_quat_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_quat_w)와 동일합니다.         |
| [`object_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_vel_w)                   | [`object_com_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_vel_w)와 동일합니다.             |
| [`object_lin_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_lin_vel_w)           | [`object_com_lin_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_lin_vel_w)와 동일합니다.     |
| [`object_ang_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_ang_vel_w)           | [`object_com_ang_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_ang_vel_w)와 동일합니다.     |
| [`object_lin_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_lin_vel_b)           | [`object_com_lin_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_lin_vel_b)와 동일합니다.     |
| [`object_ang_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_ang_vel_b)           | [`object_com_ang_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_ang_vel_b)와 동일합니다.     |
| [`object_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_acc_w)                   | [`object_com_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_acc_w)와 동일합니다.             |
| [`object_lin_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_lin_acc_w)           | [`object_com_lin_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_lin_acc_w)와 동일합니다.     |
| [`object_ang_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_ang_acc_w)           | [`object_com_ang_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_ang_acc_w)와 동일합니다.     |
| [`com_pos_b`](#isaaclab.assets.RigidObjectCollectionData.com_pos_b)                         | [`object_com_pos_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_pos_b)와 동일합니다.             |
| [`com_quat_b`](#isaaclab.assets.RigidObjectCollectionData.com_quat_b)                       | [`object_com_quat_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_quat_b)와 동일합니다.           |

#### update(dt: [float](https://docs.python.org/3/library/functions.html#float))

 rigid object 컬렉션의 데이터를 업데이트합니다.

* **매개변수:**
  **dt** – 업데이트 시간 단계입니다. 양수여야 합니다.

#### object_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

시뮬레이션 뷰에서 파싱된 순서의 객체 이름입니다.

#### default_object_state *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

로컬 환경 프레임에서의 기본 객체 상태 `[pos, quat, lin_vel, ang_vel]`입니다.
모양은 (num_instances, num_objects, 13)입니다.

위치와 쿼터니언은 각 객체의 rigid body 액터 프레임을 기준으로 합니다. 반면, 선형 및 각속도는 질량 중심 프레임을 기준으로 합니다.

#### default_mass *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에서 읽은 기본 객체 질량입니다. 모양은 (num_instances, num_objects, 1)입니다.

#### default_inertia *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에서 읽은 기본 객체 관성 텐서입니다. 모양은 (num_instances, num_objects, 9)입니다.

관성 텐서는 질량 중심을 기준으로 하고, rigid body의 액터 프레임에서 표현되어야 합니다.
값은 다음과 같은 순서로 저장됩니다: $[I_{xx}, I_{yx}, I_{zx}, I_{xy}, I_{yy}, I_{zy}, I_{xz}, I_{yz}, I_{zz}]$.
그러나 관성 텐서의 대칭성으로 인해 행 주열과 열 주열 순서는 동등합니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### *property* object_link_pose_w

시뮬레이션 월드 프레임에서의 객체 링크 포즈 `[pos, quat]`입니다. 모양은 (num_instances, num_objects, 7)입니다.

위치와 방향은 rigid body의 액터 프레임을 기준으로 합니다.

#### *property* object_link_vel_w

시뮬레이션 월드 프레임에서의 객체 링크 속도 `[lin_vel, ang_vel]`입니다.
모양은 (num_instances, num_objects, 6)입니다.

선형 및 각속도는 rigid body의 액터 프레임을 기준으로 합니다.

#### *property* object_com_pose_w

시뮬레이션 월드 프레임에서의 객체 질량 중심 포즈 `[pos, quat]`입니다.
모양은 (num_instances, num_objects, 7)입니다.

위치와 방향은 rigid body의 질량 중심 프레임을 기준으로 합니다.

#### *property* object_com_vel_w

시뮬레이션 월드 프레임에서의 객체 질량 중심 속도 `[lin_vel, ang_vel]`입니다.
모양은 (num_instances, num_objects, 6)입니다.

선형 및 각속도는 rigid body의 질량 중심 프레임을 기준으로 합니다.

#### *property* object_state_w

시뮬레이션 월드 프레임에서의 객체 상태 `[pos, quat, lin_vel, ang_vel]`입니다.
모양은 (num_instances, num_objects, 13)입니다.

위치와 방향은 rigid body의 액터 프레임을 기준으로 합니다. 반면, 선형 및 각속도는 rigid body의 질량 중심 프레임을 기준으로 합니다.

#### *property* object_link_state_w

시뮬레이션 월드 프레임에서의 객체 질량 중심 상태 `[pos, quat, lin_vel, ang_vel]`입니다.
모양은 (num_instances, num_objects, 13)입니다.

위치, 쿼터니언 및 선형/각속도는 월드를 기준으로 한 rigid body 루트 프레임에 상대적입니다.

#### *property* object_com_state_w

시뮬레이션 월드 프레임에서의 객체 상태 `[pos, quat, lin_vel, ang_vel]`입니다.
모양은 (num_instances, num_objects, 13)입니다.

위치, 쿼터니언 및 선형/각속도는 월드를 기준으로 한 rigid body의 질량 중심 프레임에 상대적입니다. 질량 중심 프레임은 관성 주축 방향을 기준으로 방향이 정해집니다.

#### *property* object_com_acc_w

모든 객체의 가속도입니다. 모양은 (num_instances, num_objects, 6)입니다.

이 양은 rigid body의 질량 중심 프레임의 가속도입니다.

#### *property* object_com_pose_b

각각의 몸체 프레임에서의 객체 질량 중심 포즈 `[pos, quat]`입니다.
모양은 (num_instances, num_objects, 7)입니다.

위치와 방향은 rigid body의 질량 중심 프레임을 기준으로 합니다.
방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* projected_gravity_b

기준 프레임에 대한 중력 방향의 투영입니다. 모양은 (num_instances, num_objects, 3)입니다.

#### *property* heading_w

기준 프레임의 요 방위 (라디안 단위)입니다. 모양은 (num_instances, num_objects,)입니다.

#### NOTE
이 양은 기준 프레임의 전방 방향이 x 방향, 즉 $(1, 0, 0)$에 있다고 가정하여 계산됩니다.

#### *property* object_link_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 프레임에서의 객체 링크 선형 속도입니다. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 rigid body 루트 프레임의 액터 프레임 선형 속도로, rigid body의 액터 프레임에 상대적입니다.

#### *property* object_link_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 월드 프레임에서의 객체 링크 각속도입니다. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 rigid body 루트 프레임의 액터 프레임 각속도로, rigid body의 액터 프레임에 상대적입니다.

#### *property* object_com_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 프레임에서의 객체 질량 중심 선형 속도입니다. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 rigid body 루트 프레임의 질량 중심 프레임 선형 속도로, rigid body의 액터 프레임에 상대적입니다.

#### *property* object_com_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

물체 질량 중심의 각속도 (베이스 월드 프레임). 모양은 (num_instances, num_objects, 3)입니다.

이 양은 단일 강체 프레임의 질량 중심 프레임의 각속도이며, 강체의 액터 프레임에 대해 계산됩니다.

#### *property* object_link_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 링크 위치. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 강체의 액터 프레임의 위치입니다.

#### *property* object_link_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 링크 방향 (w, x, y, z). 모양은 (num_instances, num_objects, 4)입니다.

이 양은 강체의 액터 프레임의 방향입니다.

#### *property* object_link_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 링크 선속도. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 강체의 액터 프레임의 선속도입니다.

#### *property* object_link_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 링크 각속도. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 강체의 액터 프레임의 각속도입니다.

#### *property* object_com_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 질량 중심 위치. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 강체의 질량 중심 프레임의 위치입니다.

#### *property* object_com_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 질량 중심 방향 (w, x, y, z). 모양은 (num_instances, num_objects, 4)입니다.

이 양은 강체의 질량 중심 프레임의 방향입니다.

#### *property* object_com_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 질량 중심 선속도. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 강체의 질량 중심 프레임의 선속도입니다.

#### *property* object_com_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 질량 중심 각속도. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 강체의 질량 중심 프레임의 각속도입니다.

#### *property* object_com_lin_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 질량 중심 선가속도. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 강체의 질량 중심 프레임의 선가속도입니다.

#### *property* object_com_ang_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 물체 질량 중심 각가속도. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 강체의 질량 중심 프레임의 각가속도입니다.

#### *property* object_com_pos_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

각 몸체에 해당하는 몸체 링크 프레임에서의 모든 몸체의 질량 중심. 모양은 (num_instances, num_objects, 3)입니다.

이 양은 몸체 링크 프레임을 기준으로 한 질량 중심의 위치입니다.

#### *property* object_com_quat_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 모든 몸체의 관성 주축의 방향 (w, x, y, z). 모양은 (num_instances, num_objects, 4)입니다.

이 양은 몸체 링크 프레임을 기준으로 한 관성 주축의 방향입니다. 방향은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* object_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_link_pose_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_pose_w)와 동일합니다.

#### *property* object_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_link_pos_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_pos_w)와 동일합니다.

#### *property* object_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_link_quat_w`](#isaaclab.assets.RigidObjectCollectionData.object_link_quat_w)와 동일합니다.

#### *property* object_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_vel_w)와 동일합니다.

#### *property* object_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_lin_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_lin_vel_w)와 동일합니다.

#### *property* object_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_ang_vel_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_ang_vel_w)와 동일합니다.

#### *property* object_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_lin_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_lin_vel_b)와 동일합니다.

#### *property* object_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_ang_vel_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_ang_vel_b)와 동일합니다.

#### *property* object_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_acc_w)와 동일합니다.

#### *property* object_lin_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_lin_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_lin_acc_w)와 동일합니다.

#### *property* object_ang_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_ang_acc_w`](#isaaclab.assets.RigidObjectCollectionData.object_com_ang_acc_w)와 동일합니다.

#### *property* com_pos_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_pos_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_pos_b)와 동일합니다.

#### *property* com_quat_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`object_com_quat_b`](#isaaclab.assets.RigidObjectCollectionData.object_com_quat_b)와 동일합니다.

### *class* isaaclab.assets.RigidObjectCollectionCfg

기본 클래스: [`object`](https://docs.python.org/3/library/functions.html#object)

강체 객체 컬렉션을 위한 구성 파라미터.

**속성:**

| [`rigid_objects`](#isaaclab.assets.RigidObjectCollectionCfg.rigid_objects)   | 생성할 강체 객체 구성의 사전.   |
|------------------------------------------------------------------------------|-------------------------------------------------------|

#### rigid_objects *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab.assets.rigid_object.rigid_object_cfg.RigidObjectCfg](#isaaclab.assets.RigidObjectCfg)]*

생성할 강체 객체 구성의 사전.

키들은 코드 전반에서 사용되는 객체의 이름이며, 고유 식별자로 사용됩니다.

## 관절 시스템

### *class* isaaclab.assets.Articulation

기본 클래스: [`AssetBase`](#isaaclab.assets.AssetBase)

관절 시스템 자산 클래스.

관절 시스템은 조인트로 연결된 강체의 집합입니다. 조인트는 고정 또는 구동식일 수 있습니다. 조인트의 유형은 회전, prismatic, D-6 등이 될 수 있습니다. 그러나 현재 관절 시스템 클래스는 회전 및 prismatic 조인트로 테스트되었습니다. 이 클래스는 떠받침 기반과 고정 기반 관절 시스템을 모두 지원합니다. 관절 시스템의 유형은 루트 조인트에 따라 결정됩니다. 루트 조인트가 고정되어 있다면, 관절 시스템은 고정 기반 시스템으로 간주됩니다. 그렇지 않다면, 떠받침 기반 시스템으로 간주됩니다. 이는 [`Articulation.is_fixed_base`](#isaaclab.assets.Articulation.is_fixed_base) 속성을 사용하여 확인할 수 있습니다.

자산이 관절 시스템으로 간주되려면, 자산의 루트 prim이 [USD ArticulationRootAPI](https://openusd.org/dev/api/class_usd_physics_articulation_root_a_p_i.html)를 가져야 합니다. 이 API는 감소된 좌표 공식을 사용하여 관절 시스템의 서브트리를 정의하는 데 사용됩니다. 시뮬레이션을 실행할 때, 물리 엔진은 관절 시스템 루트 prim을 파싱하고 해당하는 관절 시스템을 물리 엔진에 생성합니다. 관절 시스템 루트 prim은 [`AssetBaseCfg.prim_path`](#isaaclab.assets.AssetBaseCfg.prim_path) 속성을 사용하여 지정할 수 있습니다.

관절 시스템 클래스는 맞춤형 액추에이터 모델로 관절 시스템의 시뮬레이션을 향상시키는 기능도 제공합니다. 이러한 모델은 명시적 또는 암시적일 수 있으며, 자세한 내용은 [`isaaclab.actuators`](isaaclab.actuators.md#module-isaaclab.actuators) 모듈을 참조하십시오. 액추에이터 모델은 [`ArticulationCfg.actuators`](#isaaclab.assets.ArticulationCfg.actuators) 속성을 사용하여 지정됩니다. 시뮬레이션을 실행할 때, 이 모델들은 파싱되어 해당하는 액추에이터 모델을 초기화하는 데 사용됩니다.

시뮬레이션 단계 동안, 관절 시스템 클래스는 먼저 액추에이터 모델을 적용하여 사용자가 지정한 목표에 기반한 조인트 명령을 계산합니다. 이러한 조인트 명령은 luego 시뮬레이션에 적용됩니다. 조인트 명령은 위치, 속도 또는 힘 명령일 수 있습니다. 예를 들어, 다음 코드 조각은 위치 명령에 이 방법을 사용하는 방법을 보여줍니다:

```python
# articulation 클래스의 예시 인스턴스
my_articulation = Articulation(cfg)

# 조인트 위치 목표 설정
my_articulation.set_joint_position_target(position)
# 액추에이터 모델을 전파하고 계산된 명령을 시뮬레이션에 적용
my_articulation.write_data_to_sim()

# 시뮬레이션 컨텍스트를 사용하여 시뮬레이션 단계 실행
sim_context.step()

# dt가 시뮬레이션 시간 단계인 관절 시스템 상태 업데이트
my_articulation.update(dt)
```

**속성:**

| [`cfg`](#isaaclab.assets.Articulation.cfg)                                                     | арти큘레이션을 위한 구성 인스턴스입니다.                     |
|------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| [`actuators`](#isaaclab.assets.Articulation.actuators)                                         | арти큘레이션을 위한 액추에이터 인스턴스의 사전입니다.            |
| [`data`](#isaaclab.assets.Articulation.data)                                                   | 자산과 관련된 데이터입니다.                                        |
| [`num_instances`](#isaaclab.assets.Articulation.num_instances)                                 | 자산의 인스턴스 수입니다.                                 |
| [`is_fixed_base`](#isaaclab.assets.Articulation.is_fixed_base)                                 | арти큘레이션이 고정 기반 시스템인지 부동 기반 시스템인지 여부입니다. |
| [`num_joints`](#isaaclab.assets.Articulation.num_joints)                                       | арти큘레이션의 조인트 수입니다.                                 |
| [`num_fixed_tendons`](#isaaclab.assets.Articulation.num_fixed_tendons)                         | арти큘레이션의 고정 테너돈 수입니다.                          |
| [`num_spatial_tendons`](#isaaclab.assets.Articulation.num_spatial_tendons)                     | арти큘레이션의 공간 테너돈 수입니다.                        |
| [`num_bodies`](#isaaclab.assets.Articulation.num_bodies)                                       | арти큘레이션의 바디 수입니다.                                 |
| [`joint_names`](#isaaclab.assets.Articulation.joint_names)                                     | арти큘레이션의 조인트 순서가 지정된 이름입니다.                          |
| [`fixed_tendon_names`](#isaaclab.assets.Articulation.fixed_tendon_names)                       | арти큘레이션의 고정 테너돈 순서가 지정된 이름입니다.                   |
| [`spatial_tendon_names`](#isaaclab.assets.Articulation.spatial_tendon_names)                   | арти큘레이션의 공간 테너돈 순서가 지정된 이름입니다.                 |
| [`body_names`](#isaaclab.assets.Articulation.body_names)                                       | арти큘레이션의 바디 순서가 지정된 이름입니다.                          |
| [`root_physx_view`](#isaaclab.assets.Articulation.root_physx_view)                             | PhysX 기반 자산의 арти큘레이션 뷰입니다.                          |
| [`instantaneous_wrench_composer`](#isaaclab.assets.Articulation.instantaneous_wrench_composer) | 순간 와렌치 작곡자입니다.                                    |
| [`permanent_wrench_composer`](#isaaclab.assets.Articulation.permanent_wrench_composer)         | 영구 와렌치 작곡자입니다.                                        |
| [`device`](#isaaclab.assets.Articulation.device)                                               | 계산을 위한 메모리 장치입니다.                                    |
| [`has_debug_vis_implementation`](#isaaclab.assets.Articulation.has_debug_vis_implementation)   | 자산에 디버그 시각화가 구현되어 있는지 여부입니다.          |
| [`is_initialized`](#isaaclab.assets.Articulation.is_initialized)                               | 자산이 초기화되었는지 여부입니다.                                 |

**메서드:**

| [`__init__`](#isaaclab.assets.Articulation.__init__)(cfg)                                                               | арти큘레이션을 초기화합니다.                                                                |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`reset`](#isaaclab.assets.Articulation.reset)([env_ids])                                                               | 선택된 환경의 모든 내부 버퍼를 재설정합니다.                                       |
| [`write_data_to_sim`](#isaaclab.assets.Articulation.write_data_to_sim)()                                                | 외부 와렌치와 조인트 명령을 시뮬레이션에 씁니다.                               |
| [`update`](#isaaclab.assets.Articulation.update)(dt)                                                                    | 내부 버퍼를 업데이트합니다.                                                                |
| [`find_bodies`](#isaaclab.assets.Articulation.find_bodies)(name_keys[, preserve_order])                                 | 이름 키를 기반으로 арти큘레이션에서 바디를 찾습니다.                                     |
| [`find_joints`](#isaaclab.assets.Articulation.find_joints)(name_keys[, joint_subset, ...])                              | 이름 키를 기반으로 арти큘레이션에서 조인트를 찾습니다.                                     |
| [`find_fixed_tendons`](#isaaclab.assets.Articulation.find_fixed_tendons)(name_keys[, ...])                              | 이름 키를 기반으로 арти큘레이션에서 고정 테너돈을 찾습니다.                              |
| [`find_spatial_tendons`](#isaaclab.assets.Articulation.find_spatial_tendons)(name_keys[, ...])                          | 이름 키를 기반으로 арти큘레이션에서 공간 테너돈을 찾습니다.                            |
| [`write_root_state_to_sim`](#isaaclab.assets.Articulation.write_root_state_to_sim)(root_state[, env_ids])               | 선택된 환경 인덱스의 루트 상태를 시뮬레이션에 설정합니다.                   |
| [`write_root_com_state_to_sim`](#isaaclab.assets.Articulation.write_root_com_state_to_sim)(root_state[, ...])           | 선택된 환경 인덱스의 루트 질량 중심을 시뮬레이션에 설정합니다.    |
| [`write_root_link_state_to_sim`](#isaaclab.assets.Articulation.write_root_link_state_to_sim)(root_state[, ...])         | 선택된 환경 인덱스의 루트 링크 상태를 시뮬레이션에 설정합니다.              |
| [`write_root_pose_to_sim`](#isaaclab.assets.Articulation.write_root_pose_to_sim)(root_pose[, env_ids])                  | 선택된 환경 인덱스의 루트 포즈를 시뮬레이션에 설정합니다.                    |
| [`write_root_link_pose_to_sim`](#isaaclab.assets.Articulation.write_root_link_pose_to_sim)(root_pose[, env_ids])        | 선택된 환경 인덱스의 루트 링크 포즈를 시뮬레이션에 설정합니다.               |
| [`write_root_com_pose_to_sim`](#isaaclab.assets.Articulation.write_root_com_pose_to_sim)(root_pose[, env_ids])          | 선택된 환경 인덱스의 루트 질량 중심 포즈를 시뮬레이션에 설정합니다.     |
| [`write_root_velocity_to_sim`](#isaaclab.assets.Articulation.write_root_velocity_to_sim)(root_velocity[, ...])          | 선택된 환경 인덱스의 루트 질량 중심 속도를 시뮬레이션에 설정합니다. |
| [`write_root_com_velocity_to_sim`](#isaaclab.assets.Articulation.write_root_com_velocity_to_sim)(root_velocity)         | 선택된 환경 인덱스의 루트 질량 중심 속도를 시뮬레이션에 설정합니다. |
| [`write_root_link_velocity_to_sim`](#isaaclab.assets.Articulation.write_root_link_velocity_to_sim)(root_velocity)       | 선택된 환경 인덱스의 루트 링크 속도를 시뮬레이션에 설정합니다.           |
| [`write_joint_state_to_sim`](#isaaclab.assets.Articulation.write_joint_state_to_sim)(position, velocity)                | 조인트 위치와 속도를 시뮬레이션에 씁니다.                                     |
| [`write_joint_position_to_sim`](#isaaclab.assets.Articulation.write_joint_position_to_sim)(position[, ...])             | 조인트 위치를 시뮬레이션에 씁니다.                                                    |
| [`write_joint_velocity_to_sim`](#isaaclab.assets.Articulation.write_joint_velocity_to_sim)(velocity[, ...])             | 조인트 속도를 시뮬레이션에 씁니다.                                                   |
| [`write_joint_stiffness_to_sim`](#isaaclab.assets.Articulation.write_joint_stiffness_to_sim)(stiffness[, ...])          | 조인트 강성을 시뮬레이션에 씁니다.                                                  |
| [`write_joint_damping_to_sim`](#isaaclab.assets.Articulation.write_joint_damping_to_sim)(damping[, ...])                | 조인트 감쇠를 시뮬레이션에 씁니다.                                                  |
| [`write_joint_position_limit_to_sim`](#isaaclab.assets.Articulation.write_joint_position_limit_to_sim)(limits[, ...])   | 조인트 위치 한계를 시뮬레이션에 씁니다.                                            |
| [`write_joint_velocity_limit_to_sim`](#isaaclab.assets.Articulation.write_joint_velocity_limit_to_sim)(limits[, ...])   | 조인트 최대 속도를 시뮬레이션에 씁니다.                                                 |
| [`write_joint_effort_limit_to_sim`](#isaaclab.assets.Articulation.write_joint_effort_limit_to_sim)(limits[, ...])       | 조인트 노력 한계를 시뮬레이션에 씁니다.                                              |
| [`write_joint_armature_to_sim`](#isaaclab.assets.Articulation.write_joint_armature_to_sim)(armature[, ...])             | 조인트 암추어를 시뮬레이션에 씁니다.                                                   |
| [`write_joint_friction_coefficient_to_sim`](#isaaclab.assets.Articulation.write_joint_friction_coefficient_to_sim)(...) | 조인트 마찰 계수를 시뮬레이션에 씁니다.                                      |
| [`set_external_force_and_torque`](#isaaclab.assets.Articulation.set_external_force_and_torque)(forces, torques)         | 자산의 바디에 로컬 프레임에서 외부 힘과 토크를 적용합니다.          |
| [`set_joint_position_target`](#isaaclab.assets.Articulation.set_joint_position_target)(target[, ...])                   | 내부 버퍼에 조인트 위치 목표를 설정합니다.                                           |
| [`set_joint_velocity_target`](#isaaclab.assets.Articulation.set_joint_velocity_target)(target[, ...])                   | 내부 버퍼에 조인트 속도 목표를 설정합니다.                                           |
| [`set_joint_effort_target`](#isaaclab.assets.Articulation.set_joint_effort_target)(target[, joint_ids, ...])            | 내부 버퍼에 조인트 토크를 설정합니다.                                                    |
| [`set_fixed_tendon_stiffness`](#isaaclab.assets.Articulation.set_fixed_tendon_stiffness)(stiffness[, ...])              | 내부 버퍼에 고정 탄성 힘 스티프니스를 설정합니다.                                           |
| [`set_fixed_tendon_damping`](#isaaclab.assets.Articulation.set_fixed_tendon_damping)(damping[, ...])                    | 내부 버퍼에 고정 탄성 힘 감쇠를 설정합니다.                                             |
| [`set_fixed_tendon_limit_stiffness`](#isaaclab.assets.Articulation.set_fixed_tendon_limit_stiffness)(limit_stiffness)   | 내부 버퍼에 고정 탄성 힘 제한 스티프니스를 설정합니다.                             |
| [`set_fixed_tendon_position_limit`](#isaaclab.assets.Articulation.set_fixed_tendon_position_limit)(limit[, ...])        | 내부 버퍼에 고정 탄성 힘 위치 제한을 설정합니다.                                       |
| [`set_fixed_tendon_rest_length`](#isaaclab.assets.Articulation.set_fixed_tendon_rest_length)(rest_length[, ...])        | 내부 버퍼에 고정 탄성 힘 휴지 길이를 설정합니다.                                 |
| [`set_fixed_tendon_offset`](#isaaclab.assets.Articulation.set_fixed_tendon_offset)(offset[, ...])                       | 내부 버퍼에 고정 탄성 힘 오프셋을 설정합니다.                                      |
| [`write_fixed_tendon_properties_to_sim`](#isaaclab.assets.Articulation.write_fixed_tendon_properties_to_sim)([...])     | 시뮬레이션에 고정 탄성 힘 속성을 씁니다.                                          |
| [`set_spatial_tendon_stiffness`](#isaaclab.assets.Articulation.set_spatial_tendon_stiffness)(stiffness[, ...])          | 내부 버퍼에 공간 탄성 힘 스티프니스를 설정합니다.                                         |
| [`set_spatial_tendon_damping`](#isaaclab.assets.Articulation.set_spatial_tendon_damping)(damping[, ...])                | 내부 버퍼에 공간 탄성 힘 감쇠를 설정합니다.                                           |
| [`set_spatial_tendon_limit_stiffness`](#isaaclab.assets.Articulation.set_spatial_tendon_limit_stiffness)(...[, ...])    | 내부 버퍼에 공간 탄성 힘 제한 스티프니스를 설정합니다.                                   |
| [`set_spatial_tendon_offset`](#isaaclab.assets.Articulation.set_spatial_tendon_offset)(offset[, ...])                   | 내부 버퍼에 공간 탄성 힘 오프셋을 설정합니다.                                    |
| [`write_spatial_tendon_properties_to_sim`](#isaaclab.assets.Articulation.write_spatial_tendon_properties_to_sim)([...]) | 시뮬레이션에 공간 탄성 힘 속성을 씁니다.                                        |
| [`write_joint_friction_to_sim`](#isaaclab.assets.Articulation.write_joint_friction_to_sim)(joint_friction)              | 시뮬레이션에 조인트 마찰 계수를 씁니다.                                      |
| [`set_debug_vis`](#isaaclab.assets.Articulation.set_debug_vis)(debug_vis)                                               | 에셋 데이터 시각화 여부를 설정합니다.                                                   |
| [`set_visibility`](#isaaclab.assets.Articulation.set_visibility)(visible[, env_ids])                                    | 에셋에 해당하는 프리밥의 가시성을 설정합니다.                                 |
| [`write_joint_limits_to_sim`](#isaaclab.assets.Articulation.write_joint_limits_to_sim)(limits[, ...])                   | 시뮬레이션에 조인트 제한을 씁니다.                                                     |
| [`set_fixed_tendon_limit`](#isaaclab.assets.Articulation.set_fixed_tendon_limit)(limit[, ...])                          | 내부 버퍼에 고정 탄성 힘 위치 제한을 설정합니다.                                     |

#### cfg *: [ArticulationCfg](#isaaclab.assets.ArticulationCfg)*

아티큘레이션의 구성 인스턴스.

#### actuators *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [ActuatorBase](isaaclab.actuators.md#isaaclab.actuators.ActuatorBase)]*

아티큘레이션의 액추에이터 인스턴스 사전.

키들은 액추에이터 이름이고, 값들은 액추에이터 인스턴스입니다. 액추에이터 인스턴스는 [`ArticulationCfg.actuators`](#isaaclab.assets.ArticulationCfg.actuators) 속성에 지정된 액추에이터 구성에 기반하여 초기화됩니다. 이들은 [`write_data_to_sim()`](#isaaclab.assets.Articulation.write_data_to_sim) 함수 동안 조인트 명령을 계산하는 데 사용됩니다.

#### \_\_init_\_(cfg: [ArticulationCfg](#isaaclab.assets.ArticulationCfg))

아티큘레이션을 초기화합니다.

* **Parameters:**
  **cfg** – 구성 인스턴스.

#### *property* data *: [ArticulationData](#isaaclab.assets.ArticulationData)*

에셋과 관련된 데이터.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

에셋 인스턴스 수.

이것은 환경당 에셋 인스턴스 수에 환경 수를 곱한 값과 같습니다.

#### *property* is_fixed_base *: [bool](https://docs.python.org/3/library/functions.html#bool)*

아티큘레이션이 고정 기반 시스템인지 또는 부동 기반 시스템인지 여부.

#### *property* num_joints *: [int](https://docs.python.org/3/library/functions.html#int)*

아티큘레이션의 조인트 수.

#### *property* num_fixed_tendons *: [int](https://docs.python.org/3/library/functions.html#int)*

아티큘레이션의 고정 탄성 힘 수.

#### *property* num_spatial_tendons *: [int](https://docs.python.org/3/library/functions.html#int)*

아티큘레이션의 공간 탄성 힘 수.

#### *property* num_bodies *: [int](https://docs.python.org/3/library/functions.html#int)*

아티큘레이션의 바디 수.

#### *property* joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

아티큘레이션의 조인트 순서가 지정된 이름.

#### *property* fixed_tendon_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

아티큘레이션의 고정 탄성 힘 순서가 지정된 이름.

#### *property* spatial_tendon_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

아티큘레이션의 공간 탄성 힘 순서가 지정된 이름.

#### *property* body_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

아티큘레이션의 바디 순서가 지정된 이름.

#### *property* root_physx_view *: omni.physics.tensors.impl.api.ArticulationView*

에셋의 아티큘레이션 뷰(PhysX).

#### NOTE
이 뷰는 주의해서 사용해야 합니다. 특정 방식으로 텐서를 처리해야 합니다.

#### *property* instantaneous_wrench_composer *: WrenchComposer*

순간적 렌치 컴포저.

`WrenchComposer` 인스턴스를 반환합니다. 이 렌치 컴포저에 추가되거나 설정된 렌치는 현재 시뮬레이션 스텝에서만 유효합니다. 시뮬레이션 스텝이 끝날 때 이 객체에 설정된 렌치는 폐기됩니다. 드래그 힘과 같이 시간이 지남에 따라 변화하는 힘을 적용하는 데 유용합니다.

#### NOTE
영구 렌치는 순간 렌치가 시뮬레이션에 적용되기 전에 순간 렌치에 합성됩니다.

#### *property* permanent_wrench_composer *: WrenchComposer*

영구 렌치 컴포저.

`WrenchComposer` 인스턴스를 반환합니다. 이 렌치 컴포저에 추가되거나 설정된 렌치는 지속적이며, 시뮬레이션의 모든 스텝에 적용됩니다. 모터의 추력과 같이 일정 기간 동안 일정한 힘을 적용하는 데 유용합니다.

#### NOTE
영구 렌치는 순간 렌치가 시뮬레이션에 적용되기 전에 순간 렌치에 합성됩니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경의 모든 내부 버퍼를 재설정합니다.

* **Parameters:**
  **env_ids** – 재설정할 객체의 인덱스. 기본값은 None(모든 인스턴스).

#### write_data_to_sim()

시뮬레이션에 외부 렌치와 조인트 명령을 씁니다.

명시적 액추에이터가 존재하는 경우, 액추에이터 모델을 사용하여 조인트 명령을 계산합니다. 그렇지 않으면, 조인트 명령을 시뮬레이션에 직접 설정합니다.

#### NOTE
외부 렌치를 여기서 시뮬레이션에 쓰는 이유는 이 함수가 시뮬레이션 스텝 전에 호출되기 때문입니다.これにより, 외부 렌치가 매 시뮬레이션 스텝에 적용됨을 보장합니다.

#### update(dt: [float](https://docs.python.org/3/library/functions.html#float))

내부 버퍼를 업데이트합니다.

시간 단계 `dt`는 시뮬레이터가 제공하지 않는 조인트 가속도와 같은 양의 수치 미분을 계산하는 데 사용됩니다.

* **Parameters:**
  **dt** – 마지막 `update` 호출로부터 경과된 시간 양.

#### find_bodies(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

명칭 키를 기준으로 어티큘레이션에서 바디를 찾습니다.

이름 매칭에 대한 자세한 내용은 `isaaclab.utils.string_utils.resolve_matching_names()` 함수를 참고하세요.

* **매개변수:**
  * **name_keys** – 바디 이름을 매치하는 정규 표현식 또는 정규 표현식 목록입니다.
  * **preserve_order** – 출력에서 이름 키의 순서를 보존할지 여부입니다. 기본값은 False입니다.
* **반환값:**
  바디 인덱스와 이름을 포함하는 리스트들의 튜플입니다.

#### find_joints(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], joint_subset: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None, preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

명칭 키를 기준으로 어티큘레이션에서 관절을 찾습니다.

이름 매칭에 대한 자세한 내용은 [`isaaclab.utils.string.resolve_matching_names()`](isaaclab.utils.md#isaaclab.utils.string.resolve_matching_names) 함수를 참고하세요.

* **매개변수:**
  * **name_keys** – 관절 이름을 매치하는 정규 표현식 또는 정규 표현식 목록입니다.
  * **joint_subset** – 검색할 관절의 부분 집합입니다. 기본값은 None이며, 이는 어티큘레이션의 모든 관절을 검색함을 의미합니다.
  * **preserve_order** – 출력에서 이름 키의 순서를 보존할지 여부입니다. 기본값은 False입니다.
* **반환값:**
  관절 인덱스와 이름을 포함하는 리스트들의 튜플입니다.

#### find_fixed_tendons(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], tendon_subsets: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None, preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

명칭 키를 기준으로 어티큘레이션에서 고정 텐던을 찾습니다.

이름 매칭에 대한 자세한 내용은 [`isaaclab.utils.string.resolve_matching_names()`](isaaclab.utils.md#isaaclab.utils.string.resolve_matching_names) 함수를 참고하세요.

* **매개변수:**
  * **name_keys** – 고정 텐던이 있는 관절의 이름을 매치하는 정규 표현식 또는 정규 표현식 목록입니다.
  * **tendon_subsets** – 고정 텐던이 있는 관절의 부분 집합을 검색합니다. 기본값은 None이며, 이는 어티큘레이션의 모든 관절을 검색함을 의미합니다.
  * **preserve_order** – 출력에서 이름 키의 순서를 보존할지 여부입니다. 기본값은 False입니다.
* **반환값:**
  텐던 인덱스와 이름을 포함하는 리스트들의 튜플입니다.

#### find_spatial_tendons(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], tendon_subsets: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None, preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

명칭 키를 기준으로 어티큘레이션에서 공간 텐던을 찾습니다.

이름 매칭에 대한 자세한 내용은 [`isaaclab.utils.string.resolve_matching_names()`](isaaclab.utils.md#isaaclab.utils.string.resolve_matching_names) 함수를 참고하세요.

* **매개변수:**
  * **name_keys** – 텐던 이름을 매치하는 정규 표현식 또는 정규 표현식 목록입니다.
  * **tendon_subsets** – 검색할 텐던의 부분 집합입니다. 기본값은 None이며, 이는 어티큘레이션의 모든 텐던을 검색함을 의미합니다.
  * **preserve_order** – 출력에서 이름 키의 순서를 보존할지 여부입니다. 기본값은 False입니다.
* **반환값:**
  텐던 인덱스와 이름을 포함하는 리스트들의 튜플입니다.

#### write_root_state_to_sim(root_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 상태를 시뮬레이션에 설정합니다.

루트 상태는 데카르트 위치, 쿼터니언 방향 (w, x, y, z), 그리고 선형 및 각속도로 구성됩니다. 모든 양은 시뮬레이션 프레임에 있습니다.

* **매개변수:**
  * **root_state** – 시뮬레이션 프레임의 루트 상태입니다. 크기는 (len(env_ids), 13)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우, 모든 인덱스가 사용됩니다.

#### write_root_com_state_to_sim(root_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심 상태를 시뮬레이션에 설정합니다.

루트 상태는 데카르트 위치, 쿼터니언 방향 (w, x, y, z), 그리고 선형 및 각속도로 구성됩니다. 모든 양은 시뮬레이션 프레임에 있습니다.

* **매개변수:**
  * **root_state** – 시뮬레이션 프레임의 루트 상태입니다. 크기는 (len(env_ids), 13)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우, 모든 인덱스가 사용됩니다.

#### write_root_link_state_to_sim(root_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 링크 상태를 시뮬레이션에 설정합니다.

루트 상태는 데카르트 위치, 쿼터니언 방향 (w, x, y, z), 그리고 선형 및 각속도로 구성됩니다. 모든 양은 시뮬레이션 프레임에 있습니다.

* **매개변수:**
  * **root_state** – 시뮬레이션 프레임의 루트 상태입니다. 크기는 (len(env_ids), 13)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우, 모든 인덱스가 사용됩니다.

#### write_root_pose_to_sim(root_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 자세를 시뮬레이션에 설정합니다.

루트 자세는 데카르트 위치와 쿼터니언 방향 (w, x, y, z)로 구성됩니다.

* **매개변수:**
  * **root_pose** – 시뮬레이션 프레임의 루트 자세입니다. 크기는 (len(env_ids), 7)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우, 모든 인덱스가 사용됩니다.

#### write_root_link_pose_to_sim(root_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 링크 자세를 시뮬레이션에 설정합니다.

루트 자세는 데카르트 위치와 쿼터니언 방향 (w, x, y, z)로 구성됩니다.

* **매개변수:**
  * **root_pose** – 시뮬레이션 프레임의 루트 자세입니다. 크기는 (len(env_ids), 7)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우, 모든 인덱스가 사용됩니다.

#### write_root_com_pose_to_sim(root_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심 자세를 시뮬레이션에 설정합니다.

루트 자세는 데카르트 위치와 쿼터니언 방향 (w, x, y, z)로 구성됩니다. 자세는 관성의 주축의 방향입니다.

* **매개변수:**
  * **root_pose** – 시뮬레이션 프레임의 루트 질량 중심 자세입니다. 크기는 (len(env_ids), 7)입니다.
  * **env_ids** – 환경 인덱스입니다. None인 경우, 모든 인덱스가 사용됩니다.

#### write_root_velocity_to_sim(root_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심 속도를 시뮬레이션에 설정합니다.

속도는 선형 속도(x, y, z)와 각속도(x, y, z)의 순서대로 구성됩니다.
NOTE: 이는 루트 프레임이 아닌 루트의 질량 중심을 기준으로 속도를 설정합니다.

* **매개변수:**
  * **root_velocity** – 시뮬레이션 월드 프레임에서의 루트 질량 중심 속도. 형태는 (len(env_ids), 6)입니다.
  * **env_ids** – 환경 인덱스. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_com_velocity_to_sim(root_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 질량 중심을 시뮬레이션에 기록합니다.

속도는 선형 속도(x, y, z)와 각속도(x, y, z)의 순서대로 구성됩니다.
NOTE: 이는 루트 프레임이 아닌 루트의 질량 중심을 기준으로 속도를 설정합니다.

* **매개변수:**
  * **root_velocity** – 시뮬레이션 월드 프레임에서의 루트 질량 중심 속도. 형태는 (len(env_ids), 6)입니다.
  * **env_ids** – 환경 인덱스. None인 경우 모든 인덱스가 사용됩니다.

#### write_root_link_velocity_to_sim(root_velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경 인덱스에 대한 루트 링크 속도를 시뮬레이션에 기록합니다.

속도는 선형 속도(x, y, z)와 각속도(x, y, z)의 순서대로 구성됩니다.
NOTE: 이는 루트의 질량 중심이 아닌 루트 프레임을 기준으로 속도를 설정합니다.

* **매개변수:**
  * **root_velocity** – 시뮬레이션 월드 프레임에서의 루트 프레임 속도. 형태는 (len(env_ids), 6)입니다.
  * **env_ids** – 환경 인덱스. None인 경우 모든 인덱스가 사용됩니다.

#### write_joint_state_to_sim(position: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 관절 위치와 속도를 기록합니다.

* **매개변수:**
  * **position** – 관절 위치. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **velocity** – 관절 속도. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 대상을 설정할 관절 인덱스. 기본값은 None(모든 관절).
  * **env_ids** – 대상을 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_position_to_sim(position: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 관절 위치를 기록합니다.

* **매개변수:**
  * **position** – 관절 위치. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 대상을 설정할 관절 인덱스. 기본값은 None(모든 관절).
  * **env_ids** – 대상을 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_velocity_to_sim(velocity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 관절 속도를 기록합니다.

* **매개변수:**
  * **velocity** – 관절 속도. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 대상을 설정할 관절 인덱스. 기본값은 None(모든 관절).
  * **env_ids** – 대상을 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_stiffness_to_sim(stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 관절 강성을 기록합니다.

* **매개변수:**
  * **stiffness** – 관절 강성. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 강성을 설정할 관절 인덱스. 기본값은 None(모든 관절).
  * **env_ids** – 강성을 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_damping_to_sim(damping: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 관절 감쇠를 기록합니다.

* **매개변수:**
  * **damping** – 관절 감쇠. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 감쇠를 설정할 관절 인덱스. 기본값은 None(모든 관절).
  * **env_ids** – 감쇠를 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_position_limit_to_sim(limits: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, warn_limit_violation: [bool](https://docs.python.org/3/library/functions.html#bool) = True)

시뮬레이션에 관절 위치 한계를 기록합니다.

* **매개변수:**
  * **limits** – 관절 한계. 형태는 (len(env_ids), len(joint_ids), 2)입니다.
  * **joint_ids** – 한계를 설정할 관절 인덱스. 기본값은 None(모든 관절).
  * **env_ids** – 한계를 설정할 환경 인덱스. 기본값은 None(모든 환경).
  * **warn_limit_violation** – 기본 관절 위치가 새로운 한계를 초과할 때 경고 또는 정보 수준 로깅을 사용할지 여부. 기본값은 True입니다.

#### write_joint_velocity_limit_to_sim(limits: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 관절 최대 속도를 기록합니다.

속도 한계는 물리 엔진에서 관절 속도를 제한하는 데 사용됩니다. 관절이 이 속도에 도달하려면 관절의 effort 한계가 충분히 커야 합니다. 관절이 이 속도보다 빠르게 움직이면, 물리 엔진이 실제로 관절을 브레이크하여 이 속도에 도달하려고 시도합니다.

* **매개변수:**
  * **limits** – 관절 최대 속도. 형태는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 최대 속도를 설정할 관절 인덱스. 기본값은 None(모든 관절).
  * **env_ids** – 최대 속도를 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_effort_limit_to_sim(limits: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 노력 한도를 작성합니다.

노력 한도는 물리 엔진에서 계산된 조인트 노력량을 제한하는 데 사용됩니다. 계산된 노력이 이 한도를 초과하면, 물리 엔진은 노력을 이 값으로 자릅니다.

* **매개변수:**
  * **limits** – 조인트 토크 한도. 크기는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 조인트 토크 한도를 설정할 조인트 인덱스. 기본값은 None(모든 조인트).
  * **env_ids** – 조인트 토크 한도를 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### write_joint_armature_to_sim(armature: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 아머처를 작성합니다.

아머처는 해당 조인트 공간 관성에 직접 추가됩니다. 조인트 속도를 줄여 시뮬레이션 안정성을 향상시키는 데 도움이 됩니다.

* **매개변수:**
  * **armature** – 조인트 아머처. 크기는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 조인트 토크 한도를 설정할 조인트 인덱스. 기본값은 None(모든 조인트). (참고: 주석은 원래 의미와 일치하도록 조정되었습니다. 원문에서는 토크 한도라고 명시되었지만, 함수 이름과 설명을 고려하면 토크 한도가 아닌 아머처를 설정하는 것으로 보이나, 원문을 그대로 번역합니다.)
  * **env_ids** – 조인트 토크 한도를 설정할 환경 인덱스. 기본값은 None(모든 환경). (참고: 위와 동일하게 원문을 그대로 번역합니다.)

#### write_joint_friction_coefficient_to_sim(joint_friction_coeff: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_dynamic_friction_coeff: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, joint_viscous_friction_coeff: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션에 조인트 마찰 계수를 작성합니다.

Isaac Sim 버전이 5.0 미만인 경우, 오직 정적 마찰 계수만 설정됩니다.
이는 전달된 공간 힘에 비례하는 최대 값까지 저항력 또는 토크를 제한합니다: $\|F_{resist}\| \leq \mu_s \, \|F_{spatial}\|$.

Isaac Sim 버전이 5.0 이상인 경우, 정적, 동적 및 점성 마찰 계수가 모두 설정됩니다. 이 모델은 쿨롱(정적 & 동적) 마찰과 점성 항을 결합합니다:
- 정적 마찰 $\mu_s$는 정지 상태에서 움직임을 방지하는 최대 노력을 정의합니다.
- 동적 마찰 $\mu_d$는 movimento가 시작되면 적용되며, 운동 중 일정하게 유지됩니다.
- 점성 마찰 $c_v$는 속도에 비례하는 저항 항입니다.

* **매개변수:**
  * **joint_friction_coeff** – 정적 마찰 계수 $\mu_s$.
    크기는 (len(env_ids), len(joint_ids))입니다. 스칼라는 모든 선택에 브로드캐스트됩니다.
  * **joint_dynamic_friction_coeff** – 동적(쿨롱) 마찰 계수 $\mu_d$.
    위와 동일한 형태입니다. None이면 동적 계수가 업데이트되지 않습니다.
  * **joint_viscous_friction_coeff** – 점성 마찰 계수 $c_v$.
    위와 동일한 형태입니다. None이면 점성 계수가 업데이트되지 않습니다.
  * **joint_ids** – 마찰 계수를 설정할 조인트 인덱스. 기본값은 None(모든 조인트).
  * **env_ids** – 마찰 계수를 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### set_external_force_and_torque(forces: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), torques: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, body_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, is_global: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

자산의 본체에 국소 프레임에서 외부 힘과 토크를 설정합니다.

많은 응용 프로그램에서, 강체에 가해지는 외부 힘을 일정 기간 동안 일정하게 유지하고 싶을 수 있습니다(예: 정책 제어 중). 이 함수는 외부 힘과 토크를 버퍼에 저장하여, 시뮬레이션의 각 단계에서 적용되도록 합니다. 선택적으로, 외부 렌치를 적용할 위치(본체의 국소 링크 프레임 기준)를 설정할 수 있습니다.

#### 참고
이 함수는 외부 렌치를 시뮬레이션에 적용하지 않습니다. 단지 원하는 값으로 버퍼를 채울 뿐입니다.
외부 렌치를 적용하려면 시뮬레이션 단계 바로 전에 [`write_data_to_sim()`](#isaaclab.assets.Articulation.write_data_to_sim) 함수를 호출하십시오.

* **매개변수:**
  * **forces** – 본체의 국소 프레임에서의 외부 힘. 크기는 (len(env_ids), len(body_ids), 3)입니다.
  * **torques** – 본체의 국소 프레임에서의 외부 토크. 크기는 (len(env_ids), len(body_ids), 3)입니다.
  * **positions** – 외부 렌치를 적용할 위치. 크기는 (len(env_ids), len(body_ids), 3)입니다. 기본값은 None입니다.
  * **body_ids** – 외부 렌치를 적용할 본체 인덱스. 기본값은 None(모든 본체).
  * **env_ids** – 외부 렌치를 적용할 환경 인덱스. 기본값은 None(모든 인스턴스).
  * **is_global** – 외부 렌치를 전역 프레임에서 적용할지 여부. 기본값은 False입니다. False로 설정된 경우,
    외부 렌치는 조인트의 본체 링크 프레임에서 적용됩니다.

#### set_joint_position_target(target: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

조인트 위치 목표를 내부 버퍼에 설정합니다.

이 함수는 조인트 목표를 시뮬레이션에 적용하지 않습니다. 단지 원하는 값으로 버퍼를 채울 뿐입니다.
조인트 목표를 적용하려면 [`write_data_to_sim()`](#isaaclab.assets.Articulation.write_data_to_sim) 함수를 호출하십시오.

* **매개변수:**
  * **target** – 조인트 위치 목표. 크기는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 목표를 설정할 조인트 인덱스. 기본값은 None(모든 조인트).
  * **env_ids** – 목표를 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### set_joint_velocity_target(target: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

조인트 속도 목표를 내부 버퍼에 설정합니다.

이 함수는 조인트 목표를 시뮬레이션에 적용하지 않습니다. 단지 원하는 값으로 버퍼를 채울 뿐입니다.
조인트 목표를 적용하려면 [`write_data_to_sim()`](#isaaclab.assets.Articulation.write_data_to_sim) 함수를 호출하십시오.

* **매개변수:**
  * **target** – 조인트 속도 목표. 크기는 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 목표를 설정할 조인트 인덱스. 기본값은 None(모든 조인트).
  * **env_ids** – 목표를 설정할 환경 인덱스. 기본값은 None(모든 환경).

#### set_joint_effort_target(target: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

관절 토크 목표를 내부 버퍼에 설정합니다.

이 함수는 시뮬레이션에 관절 목표를 적용하지 않습니다. 단지 버퍼에 원하는 값을 채웁니다. 관절 목표를 적용하려면 [`write_data_to_sim()`](#isaaclab.assets.Articulation.write_data_to_sim) 함수를 호출하세요.

* **Parameters:**
  * **target** – 관절 토크 목표. Shape은 (len(env_ids), len(joint_ids))입니다.
  * **joint_ids** – 목표를 설정할 관절 인덱스. 기본값은 None(모든 관절)입니다.
  * **env_ids** – 목표를 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_stiffness(stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 tendon 강성을 내부 버퍼에 설정합니다.

이 함수는 시뮬레이션에 tendon 강성을 적용하지 않습니다. 단지 버퍼에 원하는 값을 채웁니다. tendon 강성을 적용하려면 [`write_fixed_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_fixed_tendon_properties_to_sim) 메서드를 호출하세요.

* **Parameters:**
  * **stiffness** – 고정 tendon 강성. Shape은 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 강성을 설정할 tendon 인덱스. 기본값은 None(모든 고정 tendon)입니다.
  * **env_ids** – 강성을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_damping(damping: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 tendon 감쇠를 내부 버퍼에 설정합니다.

이 함수는 시뮬레이션에 tendon 감쇠를 적용하지 않습니다. 단지 버퍼에 원하는 값을 채웁니다. tendon 감쇠를 적용하려면 [`write_fixed_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_fixed_tendon_properties_to_sim) 함수를 호출하세요.

* **Parameters:**
  * **damping** – 고정 tendon 감쇠. Shape은 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 감쇠를 설정할 tendon 인덱스. 기본값은 None(모든 고정 tendon)입니다.
  * **env_ids** – 감쇠를 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_limit_stiffness(limit_stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 tendon 한계 강성을 내부 버퍼에 설정합니다.

이 함수는 시뮬레이션에 tendon 한계 강성을 적용하지 않습니다. 단지 버퍼에 원하는 값을 채웁니다. tendon 한계 강성을 적용하려면 [`write_fixed_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_fixed_tendon_properties_to_sim) 메서드를 호출하세요.

* **Parameters:**
  * **limit_stiffness** – 고정 tendon 한계 강성. Shape은 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 한계 강성을 설정할 tendon 인덱스. 기본값은 None(모든 고정 tendon)입니다.
  * **env_ids** – 한계 강성을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_position_limit(limit: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 tendon 위치 한계를 내부 버퍼에 설정합니다.

이 함수는 시뮬레이션에 tendon 한계를 적용하지 않습니다. 단지 버퍼에 원하는 값을 채웁니다. tendon 한계를 적용하려면 [`write_fixed_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_fixed_tendon_properties_to_sim) 함수를 호출하세요.

> Args:
> : limit: 고정 tendon 위치 한계. Shape은 (len(env_ids), len(fixed_tendon_ids))입니다.
>   fixed_tendon_ids: 한계를 설정할 tendon 인덱스. 기본값은 None(모든 고정 tendon)입니다.
>   env_ids: 한계를 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_rest_length(rest_length: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 tendon 휴식 길이를 내부 버퍼에 설정합니다.

이 함수는 시뮬레이션에 tendon 휴식 길이를 적용하지 않습니다. 단지 버퍼에 원하는 값을 채웁니다. tendon 휴식 길이를 적용하려면 [`write_fixed_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_fixed_tendon_properties_to_sim) 메서드를 호출하세요.

* **Parameters:**
  * **rest_length** – 고정 tendon 휴식 길이. Shape은 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 휴식 길이를 설정할 tendon 인덱스. 기본값은 None(모든 고정 tendon)입니다.
  * **env_ids** – 휴식 길이를 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_fixed_tendon_offset(offset: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 tendon 오프셋을 내부 버퍼에 설정합니다.

이 함수는 시뮬레이션에 tendon 오프셋을 적용하지 않습니다. 단지 버퍼에 원하는 값을 채웁니다. tendon 오프셋을 적용하려면 [`write_fixed_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_fixed_tendon_properties_to_sim) 함수를 호출하세요.

* **Parameters:**
  * **offset** – 고정 tendon 오프셋. Shape은 (len(env_ids), len(fixed_tendon_ids))입니다.
  * **fixed_tendon_ids** – 오프셋을 설정할 tendon 인덱스. 기본값은 None(모든 고정 tendon)입니다.
  * **env_ids** – 오프셋을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### write_fixed_tendon_properties_to_sim(fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

고정 tendon 속성을 시뮬레이션에 씁니다.

* **Parameters:**
  * **fixed_tendon_ids** – 제한을 설정할 고정 tendon 인덱스. 기본값은 None(모든 고정 tendon)입니다.
  * **env_ids** – 제한을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_spatial_tendon_stiffness(stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

내부 버퍼에 공간 테든 강성을 설정합니다.

이 함수는 테든 강성을 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 테든 강성을 적용하려면
[`write_spatial_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_spatial_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **stiffness** – 공간 테든 강성. 형태는 (len(env_ids), len(spatial_tendon_ids))입니다.
  * **spatial_tendon_ids** – 강성을 설정할 테든 인덱스. 기본값은 None(모든 공간 테든)이며, 모든 테든을 대상으로 합니다.
  * **env_ids** – 강성을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_spatial_tendon_damping(damping: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

내부 버퍼에 공간 테든 감쇠를 설정합니다.

이 함수는 테든 감쇠를 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 테든 감쇠를 적용하려면
[`write_spatial_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_spatial_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **damping** – 공간 테든 감쇠. 형태는 (len(env_ids), len(spatial_tendon_ids))입니다.
  * **spatial_tendon_ids** – 감쇠를 설정할 테든 인덱스. 기본값은 None이며, 모든 공간 테든을 대상으로 합니다.
  * **env_ids** – 감쇠를 설정할 환경 인덱스. 기본값은 None이며, 모든 환경을 대상으로 합니다.

#### set_spatial_tendon_limit_stiffness(limit_stiffness: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

내부 버퍼에 공간 테든 한계 강성을 설정합니다.

이 함수는 테든 한계 강성을 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 테든 한계 강성을 적용하려면
[`write_spatial_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_spatial_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **limit_stiffness** – 공간 테든 한계 강성. 형태는 (len(env_ids), len(spatial_tendon_ids))입니다.
  * **spatial_tendon_ids** – 한계 강성을 설정할 테든 인덱스. 기본값은 None이며, 모든 공간 테든을 대상으로 합니다.
  * **env_ids** – 한계 강성을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### set_spatial_tendon_offset(offset: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

내부 버퍼에 공간 테든 오프셋 힘을 설정합니다.

이 함수는 테든 오프셋을 시뮬레이션에 적용하지 않습니다. 대신 원하는 값으로 버퍼만 채웁니다. 테든 오프셋을 적용하려면
[`write_spatial_tendon_properties_to_sim()`](#isaaclab.assets.Articulation.write_spatial_tendon_properties_to_sim) 메서드를 호출하세요.

* **매개변수:**
  * **offset** – 공간 테든 오프셋. 형태는 (len(env_ids), len(spatial_tendon_ids))입니다.
  * **spatial_tendon_ids** – 오프셋을 설정할 테든 인덱스. 기본값은 None(모든 공간 테든)이며, 모든 테든을 대상으로 합니다.
  * **env_ids** – 오프셋을 설정할 환경 인덱스. 기본값은 None(모든 환경)입니다.

#### write_spatial_tendon_properties_to_sim(spatial_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

공간 테든 속성을 시뮬레이션에 씁니다.

* **매개변수:**
  * **spatial_tendon_ids** – 속성을 설정할 공간 테든 인덱스. 기본값은 None이며, 모든 공간 테든을 대상으로 합니다.
  * **env_ids** – 속성을 설정할 환경 인덱스. 기본값은 None이며, 모든 환경을 대상으로 합니다.

#### write_joint_friction_to_sim(joint_friction: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

관절 마찰 계수를 시뮬레이션에 씁니다.

#### Deprecated
버전 2.1.0부터 더 이상 사용되지 않습니다: 대신 [`write_joint_friction_coefficient_to_sim()`](#isaaclab.assets.Articulation.write_joint_friction_coefficient_to_sim)을 사용하세요.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 장치.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋에 디버그 시각화가 구현되었는지 여부를 나타냅니다.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋이 초기화되었는지 여부를 나타냅니다.

에셋이 초기화되었다면 True를 반환하고, 그렇지 않다면 False를 반환합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

에셋 데이터를 시각화할지 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 에셋 데이터를 시각화할지 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부를 반환합니다. 에셋이 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

#### set_visibility(visible: [bool](https://docs.python.org/3/library/functions.html#bool), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

에셋에 해당하는 프리믹스의 가시성을 설정합니다.

이 작업은 USD 스테이지에서 에셋에 해당하는 프리믹스의 가시성에 영향을 줍니다. 시뮬레이터에서 에셋의 가시성을 전환하는 데 유용합니다. 예를 들어, 렌더링 오버헤드를 줄이기 위해 사용 중이지 않을 때 에셋을 숨길 수 있습니다.

#### 참고
이 작업은 PXR API를 사용하여 프리믹스의 가시성을 설정합니다. 따라서 프리믹스의 수가 많을 경우 오버헤드가 발생할 수 있습니다.

* **매개변수:**
  * **visible** – 프리믹스를 보이게 할지 여부.
  * **env_ids** – 가시성을 설정할 객체의 인덱스. 기본값은 None(모든 인스턴스)입니다.

#### write_joint_limits_to_sim(limits: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), joint_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, warn_limit_violation: [bool](https://docs.python.org/3/library/functions.html#bool) = True)

관절 제한을 시뮬레이션에 씁니다.

#### Deprecated
버전 2.1.0부터 더 이상 사용되지 않습니다: 대신 [`write_joint_position_limit_to_sim()`](#isaaclab.assets.Articulation.write_joint_position_limit_to_sim)을 사용하세요.

#### set_fixed_tendon_limit(limit: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), fixed_tendon_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

내부 버퍼에 고정 텐던 위치 제한을 설정합니다.

#### Deprecated
버전 2.1.0부터 사용되지 않음: 대신 [`set_fixed_tendon_position_limit()`](#isaaclab.assets.Articulation.set_fixed_tendon_position_limit)를 사용하십시오.

### *class* isaaclab.assets.ArticulationData

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

힘줄 포함 다관절 시스템의 데이터 컨테이너.

이 클래스는 시뮬레이션에서 힘줄 포함 다관절 시스템의 데이터를 포함합니다. 데이터에는 뿌리 강체의 상태, 다관절 시스템의 모든 강체의 상태, 그리고 관절 상태가 포함됩니다. 데이터는 달리 지정되지 않은 한 시뮬레이션 월드 프레임에 저장됩니다.

힘줄 포함 다관절 시스템은 여러 개의 강체 또는 링크로 구성됩니다. 강체의 경우, 다음과 같은 두 개의 참조 프레임이 사용됩니다:

- 액터 프레임: 강체 프리임의 참조 프레임. 이는 일반적으로 강체 스키마가 적용된 Xform 프리임에 해당합니다.
- 질량 중심 프레임: 강체의 질량 중심을 기준으로 하는 참조 프레임.

설정에 따라 이 두 프레임이 일치하지 않을 수 있습니다. 로봇공학 관점에서 액터 프레임은 링크 프레임으로 해석할 수 있습니다.

**속성:**

| [`body_names`](#isaaclab.assets.ArticulationData.body_names)                                                         | 시뮬레이션 뷰에서 파싱된 순서대로 된 강체 이름 목록                                                          |
|----------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [`joint_names`](#isaaclab.assets.ArticulationData.joint_names)                                                       | 시뮬레이션 뷰에서 파싱된 순서대로 된 관절 이름 목록                                                         |
| [`fixed_tendon_names`](#isaaclab.assets.ArticulationData.fixed_tendon_names)                                         | 시뮬레이션 뷰에서 파싱된 순서대로 된 고정 힘줄 이름 목록                                                  |
| [`spatial_tendon_names`](#isaaclab.assets.ArticulationData.spatial_tendon_names)                                     | 시뮬레이션 뷰에서 파싱된 순서대로 된 공간 힘줄 이름 목록                                                  |
| [`default_root_state`](#isaaclab.assets.ArticulationData.default_root_state)                                         | 로컬 환경 프레임에서의 기본 루트 상태 `[pos, quat, lin_vel, ang_vel]`                                      |
| [`default_joint_pos`](#isaaclab.assets.ArticulationData.default_joint_pos)                                           | 모든 관절의 기본 위치                                                                                      |
| [`default_joint_vel`](#isaaclab.assets.ArticulationData.default_joint_vel)                                           | 모든 관절의 기본 속도                                                                                      |
| [`default_mass`](#isaaclab.assets.ArticulationData.default_mass)                                                     | 다관절 시스템의 모든 강체에 대한 기본 질량                                                                  |
| [`default_inertia`](#isaaclab.assets.ArticulationData.default_inertia)                                               | 다관절 시스템의 모든 강체에 대한 기본 관성 모멘트                                                            |
| [`default_joint_stiffness`](#isaaclab.assets.ArticulationData.default_joint_stiffness)                               | 모든 관절의 기본 강성                                                                                      |
| [`default_joint_damping`](#isaaclab.assets.ArticulationData.default_joint_damping)                                   | 모든 관절의 기본 감쇠                                                                                      |
| [`default_joint_armature`](#isaaclab.assets.ArticulationData.default_joint_armature)                                 | 모든 관절의 기본 아머처                                                                                   |
| [`default_joint_friction_coeff`](#isaaclab.assets.ArticulationData.default_joint_friction_coeff)                     | 모든 관절의 기본 정적 마찰 계수                                                                            |
| [`default_joint_dynamic_friction_coeff`](#isaaclab.assets.ArticulationData.default_joint_dynamic_friction_coeff)     | 모든 관절의 기본 동적 마찰 계수                                                                            |
| [`default_joint_viscous_friction_coeff`](#isaaclab.assets.ArticulationData.default_joint_viscous_friction_coeff)     | 모든 관절의 기본 점성 마찰 계수                                                                            |
| [`default_joint_pos_limits`](#isaaclab.assets.ArticulationData.default_joint_pos_limits)                             | 모든 관절의 기본 위치 제한                                                                                |
| [`default_fixed_tendon_stiffness`](#isaaclab.assets.ArticulationData.default_fixed_tendon_stiffness)                 | 모든 고정 힘줄의 기본 강성                                                                                |
| [`default_fixed_tendon_damping`](#isaaclab.assets.ArticulationData.default_fixed_tendon_damping)                     | 모든 고정 힘줄의 기본 감쇠                                                                               |
| [`default_fixed_tendon_limit_stiffness`](#isaaclab.assets.ArticulationData.default_fixed_tendon_limit_stiffness)     | 모든 고정 힘줄의 기본 제한 강성                                                                          |
| [`default_fixed_tendon_rest_length`](#isaaclab.assets.ArticulationData.default_fixed_tendon_rest_length)             | 모든 고정 힘줄의 기본 휴지 길이                                                                         |
| [`default_fixed_tendon_offset`](#isaaclab.assets.ArticulationData.default_fixed_tendon_offset)                       | 모든 고정 힘줄의 기본 오프셋                                                                             |
| [`default_fixed_tendon_pos_limits`](#isaaclab.assets.ArticulationData.default_fixed_tendon_pos_limits)               | 모든 고정 힘줄의 기본 위치 제한                                                                         |
| [`default_spatial_tendon_stiffness`](#isaaclab.assets.ArticulationData.default_spatial_tendon_stiffness)             | 모든 공간 힘줄의 기본 강성                                                                               |
| [`default_spatial_tendon_damping`](#isaaclab.assets.ArticulationData.default_spatial_tendon_damping)                 | 모든 공간 힘줄의 기본 감쇠                                                                              |
| [`default_spatial_tendon_limit_stiffness`](#isaaclab.assets.ArticulationData.default_spatial_tendon_limit_stiffness) | 모든 공간 힘줄의 기본 제한 강성                                                                        |
| [`default_spatial_tendon_offset`](#isaaclab.assets.ArticulationData.default_spatial_tendon_offset)                   | 모든 공간 힘줄의 기본 오프셋                                                                           |
| [`joint_pos_target`](#isaaclab.assets.ArticulationData.joint_pos_target)                                             | 사용자가 명령한 관절 위치 목표값                                                                        |
| [`joint_vel_target`](#isaaclab.assets.ArticulationData.joint_vel_target)                                             | 사용자가 명령한 관절 속도 목표값                                                                        |
| [`joint_effort_target`](#isaaclab.assets.ArticulationData.joint_effort_target)                                       | 사용자가 명령한 관절 노력(토크) 목표값                                                                  |
| [`computed_torque`](#isaaclab.assets.ArticulationData.computed_torque)                                               | 액추에이터 모델에서 계산된 관절 토크 (클리핑 전)                                                         |
| [`applied_torque`](#isaaclab.assets.ArticulationData.applied_torque)                                                 | 액추에이터 모델에서 적용된 관절 토크 (클리핑 후)                                                         |
| [`joint_stiffness`](#isaaclab.assets.ArticulationData.joint_stiffness)                                               | 시뮬레이션에 제공된 관절 강성                                                                           |
| [`joint_damping`](#isaaclab.assets.ArticulationData.joint_damping)                                                   | 시뮬레이션에 제공된 관절 감쇠                                                                         |
| [`joint_armature`](#isaaclab.assets.ArticulationData.joint_armature)                                                 | 시뮬레이션에 제공되는 조인트 관성 모멘트(armature)                                                                     |
| [`joint_friction_coeff`](#isaaclab.assets.ArticulationData.joint_friction_coeff)                                     | 시뮬레이션에 제공되는 조인트 정적 마찰 계수                                                  |
| [`joint_dynamic_friction_coeff`](#isaaclab.assets.ArticulationData.joint_dynamic_friction_coeff)                     | 시뮬레이션에 제공되는 조인트 동적 마찰 계수                                                  |
| [`joint_viscous_friction_coeff`](#isaaclab.assets.ArticulationData.joint_viscous_friction_coeff)                     | 시뮬레이션에 제공되는 조인트 점성 마찰 계수                                                  |
| [`joint_pos_limits`](#isaaclab.assets.ArticulationData.joint_pos_limits)                                             | 시뮬레이션에 제공되는 조인트 위치 한계                                                       |
| [`joint_vel_limits`](#isaaclab.assets.ArticulationData.joint_vel_limits)                                             | 시뮬레이션에 제공되는 조인트 최대 속도                                                       |
| [`joint_effort_limits`](#isaaclab.assets.ArticulationData.joint_effort_limits)                                       | 시뮬레이션에 제공되는 조인트 최대 토크(힘)                                                   |
| [`soft_joint_pos_limits`](#isaaclab.assets.ArticulationData.soft_joint_pos_limits)                                   | 모든 조인트에 대한 소프트 조인트 위치 한계                                                   |
| [`soft_joint_vel_limits`](#isaaclab.assets.ArticulationData.soft_joint_vel_limits)                                   | 모든 조인트에 대한 소프트 조인트 속도 한계                                                   |
| [`gear_ratio`](#isaaclab.assets.ArticulationData.gear_ratio)                                                         | 모터 토크와 가해지는 조인트 토크 간 관계를 위한 기어 비율                                             |
| [`fixed_tendon_stiffness`](#isaaclab.assets.ArticulationData.fixed_tendon_stiffness)                                 | 시뮬레이션에 제공되는 고정 테넌드 강성                                                       |
| [`fixed_tendon_damping`](#isaaclab.assets.ArticulationData.fixed_tendon_damping)                                     | 시뮬레이션에 제공되는 고정 테넌드 감쇠                                                       |
| [`fixed_tendon_limit_stiffness`](#isaaclab.assets.ArticulationData.fixed_tendon_limit_stiffness)                     | 시뮬레이션에 제공되는 고정 테넌드 한계 강성                                                   |
| [`fixed_tendon_rest_length`](#isaaclab.assets.ArticulationData.fixed_tendon_rest_length)                             | 시뮬레이션에 제공되는 고정 테넌드 휴지 길이                                                   |
| [`fixed_tendon_offset`](#isaaclab.assets.ArticulationData.fixed_tendon_offset)                                       | 시뮬레이션에 제공되는 고정 테넌드 오프셋                                                    |
| [`fixed_tendon_pos_limits`](#isaaclab.assets.ArticulationData.fixed_tendon_pos_limits)                               | 시뮬레이션에 제공되는 고정 테넌드 위치 한계                                                  |
| [`spatial_tendon_stiffness`](#isaaclab.assets.ArticulationData.spatial_tendon_stiffness)                             | 시뮬레이션에 제공되는 공간 테넌드 강성                                                        |
| [`spatial_tendon_damping`](#isaaclab.assets.ArticulationData.spatial_tendon_damping)                                 | 시뮬레이션에 제공되는 공간 테넌드 감쇠                                                        |
| [`spatial_tendon_limit_stiffness`](#isaaclab.assets.ArticulationData.spatial_tendon_limit_stiffness)                 | 시뮬레이션에 제공되는 공간 테넌드 한계 강성                                                   |
| [`spatial_tendon_offset`](#isaaclab.assets.ArticulationData.spatial_tendon_offset)                                   | 시뮬레이션에 제공되는 공간 테넌드 오프셋                                                    |
| [`root_link_pose_w`](#isaaclab.assets.ArticulationData.root_link_pose_w)                                             | 시뮬레이션 월드 프레임에서 루트 링크 포즈 `[pos, quat]`                                   |
| [`root_link_vel_w`](#isaaclab.assets.ArticulationData.root_link_vel_w)                                               | 시뮬레이션 월드 프레임에서 루트 링크 속도 `[lin_vel, ang_vel]`                                 |
| [`root_com_pose_w`](#isaaclab.assets.ArticulationData.root_com_pose_w)                                               | 시뮬레이션 월드 프레임에서 루트 질량 중심 포즈 `[pos, quat]`                                   |
| [`root_com_vel_w`](#isaaclab.assets.ArticulationData.root_com_vel_w)                                                 | 시뮬레이션 월드 프레임에서 루트 질량 중심 속도 `[lin_vel, ang_vel]`                               |
| [`root_state_w`](#isaaclab.assets.ArticulationData.root_state_w)                                                     | 시뮬레이션 월드 프레임에서 루트 상태 `[pos, quat, lin_vel, ang_vel]`                               |
| [`root_link_state_w`](#isaaclab.assets.ArticulationData.root_link_state_w)                                           | 시뮬레이션 월드 프레임에서 루트 상태 `[pos, quat, lin_vel, ang_vel]`                               |
| [`root_com_state_w`](#isaaclab.assets.ArticulationData.root_com_state_w)                                             | 시뮬레이션 월드 프레임에서 루트 질량 중심 상태 `[pos, quat, lin_vel, ang_vel]`                           |
| [`body_link_pose_w`](#isaaclab.assets.ArticulationData.body_link_pose_w)                                             | 시뮬레이션 월드 프레임에서 바디 링크 포즈 `[pos, quat]`                                   |
| [`body_link_vel_w`](#isaaclab.assets.ArticulationData.body_link_vel_w)                                               | 시뮬레이션 월드 프레임에서 바디 링크 속도 `[lin_vel, ang_vel]`                                 |
| [`body_com_pose_w`](#isaaclab.assets.ArticulationData.body_com_pose_w)                                               | 시뮬레이션 월드 프레임에서 바디 질량 중심 포즈 `[pos, quat]`                                   |
| [`body_com_vel_w`](#isaaclab.assets.ArticulationData.body_com_vel_w)                                                 | 시뮬레이션 월드 프레임에서 바디 질량 중심 속도 `[lin_vel, ang_vel]`                               |
| [`body_state_w`](#isaaclab.assets.ArticulationData.body_state_w)                                                     | 시뮬레이션 월드 프레임에서 모든 바디의 상태 [pos, quat, lin_vel, ang_vel]                            |
| [`body_link_state_w`](#isaaclab.assets.ArticulationData.body_link_state_w)                                           | 시뮬레이션 월드 프레임에서 모든 바디의 링크 프레임 상태 `[pos, quat, lin_vel, ang_vel]`                     |
| [`body_com_state_w`](#isaaclab.assets.ArticulationData.body_com_state_w)                                             | 시뮬레이션 월드 프레임에서 모든 바디의 질량 중심 상태 [pos, quat, lin_vel, ang_vel]                           |
| [`body_com_acc_w`](#isaaclab.assets.ArticulationData.body_com_acc_w)                                                 | 모든 바디의 질량 중심 가속도 `[lin_acc, ang_acc]`                                             |
| [`body_com_pose_b`](#isaaclab.assets.ArticulationData.body_com_pose_b)                                               | 모든 바디의 자체 바디 링크 프레임에서 질량 중심 포즈 `[pos, quat]`                              |
| [`body_incoming_joint_wrench_b`](#isaaclab.assets.ArticulationData.body_incoming_joint_wrench_b)                     | 부모 바디 프레임에서 자식 바디로 적용되는 조인트 반응 렌치(힘과 토크)                               |
| [`joint_pos`](#isaaclab.assets.ArticulationData.joint_pos)                                                           | 모든 조인트의 위치                                                                               |
| [`joint_vel`](#isaaclab.assets.ArticulationData.joint_vel)                                                           | 모든 조인트의 속도                                                                                 |
| [`joint_acc`](#isaaclab.assets.ArticulationData.joint_acc)                                                           | 모든 조인트의 가속도                                                                               |
| [`projected_gravity_b`](#isaaclab.assets.ArticulationData.projected_gravity_b)                                       | 기본 프레임에 투영된 중력 방향                                                                     |
| [`heading_w`](#isaaclab.assets.ArticulationData.heading_w)                                                           | 기본 프레임의 요각 방향 (라디안 단위)                                                          |
| [`root_link_lin_vel_b`](#isaaclab.assets.ArticulationData.root_link_lin_vel_b)                                       | 베이스 프레임의 루트 링크 선속도입니다.                                                                        |
| [`root_link_ang_vel_b`](#isaaclab.assets.ArticulationData.root_link_ang_vel_b)                                       | 베이스 월드 프레임의 루트 링크 각속도입니다.                                                                 |
| [`root_com_lin_vel_b`](#isaaclab.assets.ArticulationData.root_com_lin_vel_b)                                         | 베이스 프레임의 루트 질량 중심을 통한 선속도입니다.                                                              |
| [`root_com_ang_vel_b`](#isaaclab.assets.ArticulationData.root_com_ang_vel_b)                                         | 베이스 월드 프레임의 루트 질량 중심을 통한 각속도입니다.                                                       |
| [`root_link_pos_w`](#isaaclab.assets.ArticulationData.root_link_pos_w)                                               | 시뮬레이션 월드 프레임의 루트 링크 위치입니다.                                                                   |
| [`root_link_quat_w`](#isaaclab.assets.ArticulationData.root_link_quat_w)                                             | 시뮬레이션 월드 프레임의 루트 링크 방향 (w, x, y, z)입니다.                                                   |
| [`root_link_lin_vel_w`](#isaaclab.assets.ArticulationData.root_link_lin_vel_w)                                       | 시뮬레이션 월드 프레임의 루트 선속도입니다.                                                                     |
| [`root_link_ang_vel_w`](#isaaclab.assets.ArticulationData.root_link_ang_vel_w)                                       | 시뮬레이션 월드 프레임의 루트 링크 각속도입니다.                                                               |
| [`root_com_pos_w`](#isaaclab.assets.ArticulationData.root_com_pos_w)                                                 | 시뮬레이션 월드 프레임의 루트 질량 중심 위치입니다.                                                           |
| [`root_com_quat_w`](#isaaclab.assets.ArticulationData.root_com_quat_w)                                               | 시뮬레이션 월드 프레임의 루트 질량 중심 방향 (w, x, y, z)입니다.                                               |
| [`root_com_lin_vel_w`](#isaaclab.assets.ArticulationData.root_com_lin_vel_w)                                         | 시뮬레이션 월드 프레임의 루트 질량 중심을 통한 선속도입니다.                                                  |
| [`root_com_ang_vel_w`](#isaaclab.assets.ArticulationData.root_com_ang_vel_w)                                         | 시뮬레이션 월드 프레임의 루트 질량 중심을 통한 각속도입니다.                                                 |
| [`body_link_pos_w`](#isaaclab.assets.ArticulationData.body_link_pos_w)                                               | 시뮬레이션 월드 프레임의 모든 바디 위치입니다.                                                               |
| [`body_link_quat_w`](#isaaclab.assets.ArticulationData.body_link_quat_w)                                             | 시뮬레이션 월드 프레임의 모든 바디 방향 (w, x, y, z)입니다.                                                  |
| [`body_link_lin_vel_w`](#isaaclab.assets.ArticulationData.body_link_lin_vel_w)                                       | 시뮬레이션 월드 프레임의 모든 바디 선속도입니다.                                                             |
| [`body_link_ang_vel_w`](#isaaclab.assets.ArticulationData.body_link_ang_vel_w)                                       | 시뮬레이션 월드 프레임의 모든 바디 각속도입니다.                                                             |
| [`body_com_pos_w`](#isaaclab.assets.ArticulationData.body_com_pos_w)                                                 | 시뮬레이션 월드 프레임의 모든 바디 질량 중심 위치입니다.                                                     |
| [`body_com_quat_w`](#isaaclab.assets.ArticulationData.body_com_quat_w)                                               | 시뮬레이션 월드 프레임의 모든 바디 관성 주요 축의 방향 (w, x, y, z)입니다.                                    |
| [`body_com_lin_vel_w`](#isaaclab.assets.ArticulationData.body_com_lin_vel_w)                                         | 시뮬레이션 월드 프레임의 모든 바디 질량 중심을 통한 선속도입니다.                                             |
| [`body_com_ang_vel_w`](#isaaclab.assets.ArticulationData.body_com_ang_vel_w)                                         | 시뮬레이션 월드 프레임의 모든 바디 질량 중심을 통한 각속도입니다.                                             |
| [`body_com_lin_acc_w`](#isaaclab.assets.ArticulationData.body_com_lin_acc_w)                                         | 시뮬레이션 월드 프레임의 모든 바디 질량 중심을 통한 선가속도입니다.                                           |
| [`body_com_ang_acc_w`](#isaaclab.assets.ArticulationData.body_com_ang_acc_w)                                         | 시뮬레이션 월드 프레임의 모든 바디 질량 중심을 통한 각가속도입니다.                                           |
| [`body_com_pos_b`](#isaaclab.assets.ArticulationData.body_com_pos_b)                                                 | 모든 바디의 각각의 링크 프레임 내 질량 중심 위치입니다.                                                      |
| [`body_com_quat_b`](#isaaclab.assets.ArticulationData.body_com_quat_b)                                               | 모든 바디의 각각의 링크 프레임 내 관성 주요 축의 방향 (w, x, y, z)입니다.                                     |
| [`root_pose_w`](#isaaclab.assets.ArticulationData.root_pose_w)                                                       | [`root_link_pose_w`](#isaaclab.assets.ArticulationData.root_link_pose_w)와 동일합니다.                         |
| [`root_pos_w`](#isaaclab.assets.ArticulationData.root_pos_w)                                                         | [`root_link_pos_w`](#isaaclab.assets.ArticulationData.root_link_pos_w)와 동일합니다.                          |
| [`root_quat_w`](#isaaclab.assets.ArticulationData.root_quat_w)                                                       | [`root_link_quat_w`](#isaaclab.assets.ArticulationData.root_link_quat_w)와 동일합니다.                        |
| [`root_vel_w`](#isaaclab.assets.ArticulationData.root_vel_w)                                                         | [`root_com_vel_w`](#isaaclab.assets.ArticulationData.root_com_vel_w)와 동일합니다.                           |
| [`root_lin_vel_w`](#isaaclab.assets.ArticulationData.root_lin_vel_w)                                                 | [`root_com_lin_vel_w`](#isaaclab.assets.ArticulationData.root_com_lin_vel_w)와 동일합니다.                   |
| [`root_ang_vel_w`](#isaaclab.assets.ArticulationData.root_ang_vel_w)                                                 | [`root_com_ang_vel_w`](#isaaclab.assets.ArticulationData.root_com_ang_vel_w)와 동일합니다.                   |
| [`root_lin_vel_b`](#isaaclab.assets.ArticulationData.root_lin_vel_b)                                                 | [`root_com_lin_vel_b`](#isaaclab.assets.ArticulationData.root_com_lin_vel_b)와 동일합니다.                   |
| [`root_ang_vel_b`](#isaaclab.assets.ArticulationData.root_ang_vel_b)                                                 | [`root_com_ang_vel_b`](#isaaclab.assets.ArticulationData.root_com_ang_vel_b)와 동일합니다.                   |
| [`body_pose_w`](#isaaclab.assets.ArticulationData.body_pose_w)                                                       | [`body_link_pose_w`](#isaaclab.assets.ArticulationData.body_link_pose_w)와 동일합니다.                        |
| [`body_pos_w`](#isaaclab.assets.ArticulationData.body_pos_w)                                                         | [`body_link_pos_w`](#isaaclab.assets.ArticulationData.body_link_pos_w)와 동일합니다.                          |
| [`body_quat_w`](#isaaclab.assets.ArticulationData.body_quat_w)                                                       | [`body_link_quat_w`](#isaaclab.assets.ArticulationData.body_link_quat_w)와 동일합니다.                        |
| [`body_vel_w`](#isaaclab.assets.ArticulationData.body_vel_w)                                                         | [`body_com_vel_w`](#isaaclab.assets.ArticulationData.body_com_vel_w)와 동일합니다.                           |
| [`body_lin_vel_w`](#isaaclab.assets.ArticulationData.body_lin_vel_w)                                                 | [`body_com_lin_vel_w`](#isaaclab.assets.ArticulationData.body_com_lin_vel_w)와 동일합니다.                   |
| [`body_ang_vel_w`](#isaaclab.assets.ArticulationData.body_ang_vel_w)                                                 | [`body_com_ang_vel_w`](#isaaclab.assets.ArticulationData.body_com_ang_vel_w)와 동일합니다.                   |
| [`body_acc_w`](#isaaclab.assets.ArticulationData.body_acc_w)                                                         | [`body_com_acc_w`](#isaaclab.assets.ArticulationData.body_com_acc_w)와 동일합니다.                           |
| [`body_lin_acc_w`](#isaaclab.assets.ArticulationData.body_lin_acc_w)                                                 | [`body_com_lin_acc_w`](#isaaclab.assets.ArticulationData.body_com_lin_acc_w)와 동일합니다.                   |
| [`body_ang_acc_w`](#isaaclab.assets.ArticulationData.body_ang_acc_w)                                                 | [`body_com_ang_acc_w`](#isaaclab.assets.ArticulationData.body_com_ang_acc_w)와 동일합니다.                   |
| [`com_pos_b`](#isaaclab.assets.ArticulationData.com_pos_b)                                                           | [`body_com_pos_b`](#isaaclab.assets.ArticulationData.body_com_pos_b)와 동일합니다.                           |
| [`com_quat_b`](#isaaclab.assets.ArticulationData.com_quat_b)                                                         | [`body_com_quat_b`](#isaaclab.assets.ArticulationData.body_com_quat_b)와 동일합니다.                                 |
| [`joint_limits`](#isaaclab.assets.ArticulationData.joint_limits)                                                     | 더 이상 사용되지 않는 속성입니다.                                                                                            |
| [`default_joint_limits`](#isaaclab.assets.ArticulationData.default_joint_limits)                                     | 더 이상 사용되지 않는 속성입니다.                                                                                            |
| [`joint_velocity_limits`](#isaaclab.assets.ArticulationData.joint_velocity_limits)                                   | 더 이상 사용되지 않는 속성입니다.                                                                                            |
| [`joint_friction`](#isaaclab.assets.ArticulationData.joint_friction)                                                 | 더 이상 사용되지 않는 속성입니다.                                                                                            |
| [`default_joint_friction`](#isaaclab.assets.ArticulationData.default_joint_friction)                                 | 더 이상 사용되지 않는 속성입니다.                                                                                            |
| [`fixed_tendon_limit`](#isaaclab.assets.ArticulationData.fixed_tendon_limit)                                         | 더 이상 사용되지 않는 속성입니다.                                                                                            |
| [`default_fixed_tendon_limit`](#isaaclab.assets.ArticulationData.default_fixed_tendon_limit)                         | 더 이상 사용되지 않는 속성입니다.                                                                                            |

#### body_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

시뮬레이션 뷰에 의해 구문 분석된 순서대로 된 본체 이름입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

시뮬레이션 뷰에 의해 구문 분석된 순서대로 된 관절 이름입니다.

#### fixed_tendon_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

시뮬레이션 뷰에 의해 구문 분석된 순서대로 된 고정 테본 이름입니다.

#### spatial_tendon_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

시뮬레이션 뷰에 의해 구문 분석된 순서대로 된 공간 테본 이름입니다.

#### default_root_state *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

로컬 환경 프레임에서의 기본 루트 상태 `[pos, quat, lin_vel, ang_vel]`.  
형태는 (num_instances, 13)입니다.

위치와 쿼터니언은 어티큘레이션 루트의 액터 프레임에 대한 것이며, 선형 및 각속도는 질량 중심을 기준으로 한 프레임에 대한 것입니다.

이 값은 [`isaaclab.assets.ArticulationCfg.init_state`](#isaaclab.assets.ArticulationCfg.init_state) 매개변수를 통해 구성됩니다.

#### default_joint_pos *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 기본 관절 위치. 형태는 (num_instances, num_joints)입니다.

이 값은 [`isaaclab.assets.ArticulationCfg.init_state`](#isaaclab.assets.ArticulationCfg.init_state) 매개변수를 통해 구성됩니다.

#### default_joint_vel *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 기본 관절 속도. 형태는 (num_instances, num_joints)입니다.

이 값은 [`isaaclab.assets.ArticulationCfg.init_state`](#isaaclab.assets.ArticulationCfg.init_state) 매개변수를 통해 구성됩니다.

#### default_mass *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

어티큘레이션의 모든 본체에 대한 기본 질량. 형태는 (num_instances, num_bodies)입니다.

이 값은 초기화 시 USD 스키마에서 구문 분석됩니다.

#### default_inertia *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

어티큘레이션의 모든 본체에 대한 기본 관성. 형태는 (num_instances, num_bodies, 9)입니다.

관성 텐서는 질량 중심을 기준으로 표현되어야 하며, 어티큘레이션 링크의 액터 프레임에 따라야 합니다. 값은 다음과 같은 순서로 저장됩니다  
$[I_{xx}, I_{yx}, I_{zx}, I_{xy}, I_{yy}, I_{zy}, I_{xz}, I_{yz}, I_{zz}]$. 그러나 관성 텐서의 대칭성으로 인해 행-주열 및 열-주열 순서는 동일합니다.

이 값은 초기화 시 USD 스키마에서 구문 분석됩니다.

#### default_joint_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 기본 관절 강성. 형태는 (num_instances, num_joints)입니다.

이 값은 액추에이터 모델의 [`isaaclab.actuators.ActuatorBaseCfg.stiffness`](isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.stiffness) 매개변수를 통해 구성됩니다. 매개변수의 값이 None이면, 초기화 시 USD 스키마에서 구문 분석된 값이 사용됩니다.

#### ATTENTION
기본 강성은 사용자가 구성한 값이거나 USD 스키마에서 구문 분석된 값입니다.  
이는 시뮬레이션에 설정된 값인 [`joint_stiffness`](#isaaclab.assets.ArticulationData.joint_stiffness)와 혼동해서는 안 됩니다.

#### default_joint_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 기본 관절 감쇠. 형태는 (num_instances, num_joints)입니다.

이 값은 액추에이터 모델의 [`isaaclab.actuators.ActuatorBaseCfg.damping`](isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.damping) 매개변수를 통해 구성됩니다. 매개변수의 값이 None이면, 초기화 시 USD 스키마에서 구문 분석된 값이 사용됩니다.

#### ATTENTION
기본 감쇠는 사용자가 구성한 값이거나 USD 스키마에서 구문 분석된 값입니다.  
이는 시뮬레이션에 설정된 값인 [`joint_damping`](#isaaclab.assets.ArticulationData.joint_damping)와 혼동해서는 안 됩니다.

#### default_joint_armature *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 기본 관절 암처. 형태는 (num_instances, num_joints)입니다.

이 값은 액추에이터 모델의 [`isaaclab.actuators.ActuatorBaseCfg.armature`](isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.armature) 매개변수를 통해 구성됩니다. 매개변수의 값이 None이면, 초기화 시 USD 스키마에서 구문 분석된 값이 사용됩니다.

#### default_joint_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 기본 관절 정적 마찰 계수. 형태는 (num_instances, num_joints)입니다.

이 값은 액추에이터 모델의 [`isaaclab.actuators.ActuatorBaseCfg.friction`](isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.friction) 매개변수를 통해 구성됩니다. 매개변수의 값이 None이면, 초기화 시 USD 스키마에서 구문 분석된 값이 사용됩니다.

#### NOTE
Isaac Sim 4.5에서는 이 매개변수가 계수로 모델링됩니다. Isaac Sim 5.0 이상에서는 힘(토크 또는 힘)으로 모델링됩니다.

#### default_joint_dynamic_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 기본 관절 동적 마찰 계수. 형태는 (num_instances, num_joints)입니다.

이 값은 액추에이터 모델의  
[`isaaclab.actuators.ActuatorBaseCfg.dynamic_friction`](isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.dynamic_friction) 매개변수를 통해 구성됩니다. 매개변수의 값이 None이면, 초기화 시 USD 스키마에서 구문 분석된 값이 사용됩니다.

#### NOTE
Isaac Sim 4.5에서는 이 매개변수가 계수로 모델링됩니다. Isaac Sim 5.0 이상에서는 힘(토크 또는 힘)으로 모델링됩니다.

#### default_joint_viscous_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 기본 관절 점성 마찰 계수. 형태는 (num_instances, num_joints)입니다.

이 값은 액추에이터 모델의  
[`isaaclab.actuators.ActuatorBaseCfg.viscous_friction`](isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg.viscous_friction) 매개변수를 통해 구성됩니다. 매개변수의 값이 None이면, 초기화 시 USD 스키마에서 구문 분석된 값이 사용됩니다.

#### default_joint_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 관절의 기본 관절 위치 제한. 형태는 (num_instances, num_joints, 2)입니다.

제한은 $[하한, 상한]$ 순서로 되어 있으며, 초기화 시 USD 스키마에서 구문 분석됩니다.

#### default_fixed_tendon_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테본의 기본 테본 강성. 형태는 (num_instances, num_fixed_tendons)입니다.

이 값은 초기화 시 USD 스키마에서 구문 분석됩니다.

#### default_fixed_tendon_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테본의 기본 테본 감쇠. 형태는 (num_instances, num_fixed_tendons)입니다.

이 값은 초기화 시 USD 스키마에서 구문 분석됩니다.

#### default_fixed_tendon_limit_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테본의 기본 테본 제한 강성. 형태는 (num_instances, num_fixed_tendons)입니다.

이 값은 초기화 시 USD 스키마에서 구문 분석됩니다.

#### default_fixed_tendon_rest_length *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테넌드의 기본 테넌드 rest 길이입니다. 크기는 (num_instances, num_fixed_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_fixed_tendon_offset *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테넌드의 기본 테넌드 오프셋입니다. 크기는 (num_instances, num_fixed_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_fixed_tendon_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 고정 테넌드의 기본 테넌드 위치 제한입니다. 크기는 (num_instances, num_fixed_tendons, 2)입니다.

위치 제한은 [lower, upper] 순서로 되어 있습니다. 이들은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_spatial_tendon_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 공간 테넌드의 기본 테넌드 강성입니다. 크기는 (num_instances, num_spatial_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_spatial_tendon_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 공간 테넌드의 기본 테넌드 감쇠입니다. 크기는 (num_instances, num_spatial_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_spatial_tendon_limit_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 공간 테넌드의 기본 테넌드 제한 강성입니다. 크기는 (num_instances, num_spatial_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### default_spatial_tendon_offset *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 공간 테넌드의 기본 테넌드 오프셋입니다. 크기는 (num_instances, num_spatial_tendons)입니다.

이 양은 초기화 시 USD 스키마에서 파싱됩니다.

#### joint_pos_target *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

사용자가 명령한 조인트 위치 목표입니다. 크기는 (num_instances, num_joints)입니다.

암시적 액추에이터 모델의 경우, 목표는 시뮬레이션에 직접 설정됩니다.
명시적 액추에이터 모델의 경우, 목표는 조인트 토크를 계산하는 데 사용됩니다([`applied_torque`](#isaaclab.assets.ArticulationData.applied_torque) 참조), 이 토크는 시뮬레이션에 설정됩니다.

#### joint_vel_target *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

사용자가 명령한 조인트 속도 목표입니다. 크기는 (num_instances, num_joints)입니다.

암시적 액추에이터 모델의 경우, 목표는 시뮬레이션에 직접 설정됩니다.
명시적 액추에이터 모델의 경우, 목표는 조인트 토크를 계산하는 데 사용됩니다([`applied_torque`](#isaaclab.assets.ArticulationData.applied_torque) 참조), 이 토크는 시뮬레이션에 설정됩니다.

#### joint_effort_target *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

사용자가 명령한 조인트 힘 목표입니다. 크기는 (num_instances, num_joints)입니다.

암시적 액추에이터 모델의 경우, 목표는 시뮬레이션에 직접 설정됩니다.
명시적 액추에이터 모델의 경우, 목표는 조인트 토크를 계산하는 데 사용됩니다([`applied_torque`](#isaaclab.assets.ArticulationData.applied_torque) 참조), 이 토크는 시뮬레이션에 설정됩니다.

#### computed_torque *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

액추에이터 모델에서 계산된 조인트 토크 (클리핑 전). 크기는 (num_instances, num_joints)입니다.

이 양은 클리핑이 적용되기 전 액추에이터 모드의 원시 토크 출력입니다. 액추에이터 모델 내부의 계산을 검사하려는 사용자에게 노출됩니다.
예를 들어, 계산된 토크와 적용된 토크의 차이로 학습 에이전트를 벌점주기 위해 사용됩니다.

#### applied_torque *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

액추에이터 모델에서 적용된 조인트 토크 (클리핑 후). 크기는 (num_instances, num_joints)입니다.

이 토크는 [`computed_torque`](#isaaclab.assets.ArticulationData.computed_torque)를 액추에이터 모델에 따라 클리핑한 후 시뮬레이션에 설정됩니다.

#### joint_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 조인트 강성입니다. 크기는 (num_instances, num_joints)입니다.

명시적 액추에이터의 경우, 해당 조인트의 값은 0입니다.

#### joint_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 조인트 감쇠입니다. 크기는 (num_instances, num_joints)입니다.

명시적 액추에이터의 경우, 해당 조인트의 값은 0입니다.

#### joint_armature *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 조인트 암어처입니다. 크기는 (num_instances, num_joints)입니다.

#### joint_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 조인트 정적 마찰 계수입니다. 크기는 (num_instances, num_joints)입니다.

참고: Isaac Sim 4.5에서는 이 매개변수가 계수로 모델링됩니다. Isaac Sim 5.0 및 이후 버전에서는 힘(토크 또는 힘)으로 모델링됩니다.

#### joint_dynamic_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 조인트 동적 마찰 계수입니다. 크기는 (num_instances, num_joints)입니다.

참고: Isaac Sim 4.5에서는 이 매개변수가 계수로 모델링됩니다. Isaac Sim 5.0 및 이후 버전에서는 힘(토크 또는 힘)으로 모델링됩니다.

#### joint_viscous_friction_coeff *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 조인트 점성 마찰 계수입니다. 크기는 (num_instances, num_joints)입니다.

#### joint_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 조인트 위치 제한입니다. 크기는 (num_instances, num_joints, 2)입니다.

제한은 [lower, upper] 순서로 되어 있습니다.

#### joint_vel_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 조인트 최대 속도입니다. 크기는 (num_instances, num_joints)입니다.

#### joint_effort_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 조인트 최대 힘입니다. 크기는 (num_instances, num_joints)입니다.

#### soft_joint_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 조인트의 소프트 조인트 위치 제한입니다. 크기는 (num_instances, num_joints, 2)입니다.

제한은 [lower, upper] 순서로 되어 있습니다. 소프트 조인트 위치 제한은
[`joint_pos_limits`](#isaaclab.assets.ArticulationData.joint_pos_limits)의 하위 영역으로,
[`soft_joint_pos_limit_factor`](#isaaclab.assets.ArticulationCfg.soft_joint_pos_limit_factor) 매개변수를 기반으로 계산됩니다.

조인트 위치 제한이 $[lower, upper]$이고 소프트 조인트 위치 제한이 $[soft_lower, soft_upper]$라고 할 때,
소프트 조인트 위치 제한은 다음과 같이 계산됩니다:

$$
soft\_lower = (lower + upper) / 2 - factor * (upper - lower) / 2
soft\_upper = (lower + upper) / 2 + factor * (upper - lower) / 2
$$

소프트 조인트 위치 제한은 조인트 제한 주변의 안전 영역을 지정하는 데 도움이 됩니다. 시뮬레이션에서는 사용되지 않지만,
학습 에이전트가 조인트 위치가 제한을 위반하지 않도록 하는 데 유용합니다.

#### soft_joint_vel_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모든 조인트의 소프트 조인트 속도 제한입니다. 크기는 (num_instances, num_joints)입니다.

이 값들은 액추에이터 모델에서 얻어집니다. 액추에이터 모델이 변수 속도 제한 모델을 가진 경우
[`joint_vel_limits`](#isaaclab.assets.ArticulationData.joint_vel_limits)와 다를 수 있습니다. 예를 들어, 변수 기어 비율 액추에이터 모델의 경우입니다.

#### gear_ratio *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

모터 토크를 적용된 조인트 토크와 관련짓는 기어 비율입니다. 크기는 (num_instances, num_joints)입니다.

#### fixed_tendon_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 고정 테넌드 강성입니다. 크기는 (num_instances, num_fixed_tendons)입니다.

#### fixed_tendon_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 고정 테넌드 감쇠입니다. 크기는 (num_instances, num_fixed_tendons)입니다.

#### fixed_tendon_limit_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 고정 테넌드 제한 강성입니다. 크기는 (num_instances, num_fixed_tendons)입니다.

#### fixed_tendon_rest_length *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 고정 테넌드 rest 길이입니다. 크기는 (num_instances, num_fixed_tendons)입니다.

#### fixed_tendon_offset *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 고정 테넌드 오프셋입니다. 크기는 (num_instances, num_fixed_tendons)입니다.

#### fixed_tendon_pos_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공되는 고정 테넌드 위치 제한입니다. 크기는 (num_instances, num_fixed_tendons, 2)입니다.

#### spatial_tendon_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공된 공간 텐던 강성. 모양은 (num_instances, num_spatial_tendons).

#### spatial_tendon_damping *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공된 공간 텐던 감쇠. 모양은 (num_instances, num_spatial_tendons).

#### spatial_tendon_limit_stiffness *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공된 공간 텐던 한계 강성. 모양은 (num_instances, num_spatial_tendons).

#### spatial_tendon_offset *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션에 제공된 공간 텐던 오프셋. 모양은 (num_instances, num_spatial_tendons).

#### *property* root_link_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 자세 [pos, quat]. 모양은 (num_instances, 7).

이 양은 월드 기준의 루트 링크의 액터 프레임 자세를 나타냅니다.
Orientation은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* root_link_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 속도 [lin_vel, ang_vel]. 모양은 (num_instances, 6).

이 양은 월드 기준의 루트 링크의 액터 프레임의 선형 및 각속도를 포함합니다.

#### *property* root_com_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 자세 [pos, quat]. 모양은 (num_instances, 7).

이 양은 월드 기준의 루트 링크의 질량 중심 프레임 자세를 나타냅니다.
Orientation은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* root_com_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 질량 중심 속도 [lin_vel, ang_vel]. 모양은 (num_instances, 6).

이 양은 월드 기준의 루트 링크의 질량 중심 프레임의 선형 및 각속도를 포함합니다.

#### *property* root_state_w

시뮬레이션 월드 프레임에서의 루트 상태 [pos, quat, lin_vel, ang_vel]. 모양은 (num_instances, 13).

위치와 쿼터니언은 월드 기준의 루트 링크의 액터 프레임을 나타냅니다. 한편, 선형 및 각속도는 루트 링크의 질량 중심 프레임을 나타냅니다.

#### *property* root_link_state_w

시뮬레이션 월드 프레임에서의 루트 상태 [pos, quat, lin_vel, ang_vel]. 모양은 (num_instances, 13).

위치, 쿼터니언 및 선형/각속도는 월드 기준의 루트 링크의 액터 프레임을 나타냅니다.

#### *property* root_com_state_w

시뮬레이션 월드 프레임에서의 루트 질량 중심 상태 [pos, quat, lin_vel, ang_vel].
모양은 (num_instances, 13).

위치, 쿼터니언 및 선형/각속도는 월드 기준의 루트 링크의 질량 중심 프레임을 나타냅니다. 질량 중심 프레임은 관성 주축의 방향이 아닌 링크의 방향과 동일하다고 가정됩니다.

#### *property* body_link_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 바디 링크 자세 [pos, quat].
모양은 (num_instances, num_bodies, 7).

이 양은 월드 기준의 관절 링크의 액터 프레임 자세를 나타냅니다.
Orientation은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_link_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 바디 링크 속도 [lin_vel, ang_vel].
모양은 (num_instances, num_bodies, 6).

이 양은 월드 기준의 관절 링크의 액터 프레임의 선형 및 각속도를 포함합니다.

#### *property* body_com_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 바디 질량 중심 자세 [pos, quat].
모양은 (num_instances, num_bodies, 7).

이 양은 월드 기준의 관절 링크의 질량 중심 프레임 자세를 나타냅니다.
Orientation은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_com_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 바디 질량 중심 속도 [lin_vel, ang_vel].
모양은 (num_instances, num_bodies, 6).

이 양은 월드 기준의 관절 링크의 질량 중심 프레임의 선형 및 각속도를 포함합니다.

#### *property* body_state_w

시뮬레이션 월드 프레임에서의 모든 바디 상태 [pos, quat, lin_vel, ang_vel].
모양은 (num_instances, num_bodies, 13).

위치와 쿼터니언은 모든 관절 링크의 액터 프레임을 나타냅니다. 한편, 선형 및 각속도는 모든 관절 링크의 질량 중심 프레임을 나타냅니다.

#### *property* body_link_state_w

시뮬레이션 월드 프레임에서의 모든 바디 링크 프레임 상태 [pos, quat, lin_vel, ang_vel].
모양은 (num_instances, num_bodies, 13).

위치, 쿼터니언 및 선형/각속도는 월드 기준의 바디 링크 프레임을 나타냅니다.

#### *property* body_com_state_w

시뮬레이션 월드 프레임에서의 모든 바디 질량 중심 상태 [pos, quat, lin_vel, ang_vel].
모양은 (num_instances, num_bodies, 13).

위치, 쿼터니언 및 선형/각속도는 월드 기준의 바디 질량 중심 프레임을 나타냅니다. 질량 중심 프레임은 관성 주축의 방향이 아닌 링크의 방향과 동일하다고 가정됩니다.

#### *property* body_com_acc_w

시뮬레이션 월드 프레임에서의 모든 바디 질량 중심 가속도 [lin_acc, ang_acc].
모양은 (num_instances, num_bodies, 6).

모든 값은 월드 기준입니다.

#### *property* body_com_pose_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

해당 바디 링크 프레임 기준의 모든 바디의 질량 중심 자세 [pos, quat].
모양은 (num_instances, 1, 7).

이 양은 바디의 질량 중심 프레임이 바디의 링크 프레임 기준에서의 자세를 나타냅니다.
Orientation은 (w, x, y, z) 형식으로 제공됩니다.

#### *property* body_incoming_joint_wrench_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

바디 부모에서 자식 바디로 바디 부모 프레임 기준에서 작용하는 관절 반력 렌치.

모양은 (num_instances, num_bodies, 6). 루트 바디에서 월드까지의 모든 바디 반력 렌치가 포함됩니다.

관절 렌치에 대한 자세한 내용은 [PhysX 문서] 및 기반 [PhysX Tensor API]을 참조하십시오.

#### *property* joint_pos

모든 관절의 관절 위치. 모양은 (num_instances, num_joints).

#### *property* joint_vel

모든 관절의 관절 속도. 모양은 (num_instances, num_joints).

#### *property* joint_acc

모든 관절의 관절 가속도. 모양은 (num_instances, num_joints).

#### *property* projected_gravity_b

기준 프레임에 투영된 중력 방향. 모양은 (num_instances, 3).

#### *property* heading_w

기준 프레임의 요 각도 (라디안). 모양은 (num_instances,).

#### NOTE
이 양은 기준 프레임의 전방 방향이 x 방향, 즉 (1, 0, 0)에 있다고 가정하여 계산됩니다.

#### *property* root_link_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 프레임에서의 루트 링크 선형 속도. 모양은 (num_instances, 3).

이 양은 루트 링크의 액터 프레임이 자체 액터 프레임 기준에서의 선형 속도를 나타냅니다.

#### *property* root_link_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 월드 프레임에서의 루트 링크 각속도. 모양은 (num_instances, 3).

이 양은 루트 링크의 액터 프레임이 자체 액터 프레임 기준에서의 각속도를 나타냅니다.

#### *property* root_com_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 프레임에서의 루트 질량 중심 선형 속도. 모양은 (num_instances, 3).

이 양은 루트 링크의 질량 중심 프레임이 자체 액터 프레임 기준에서의 선형 속도를 나타냅니다.

#### *property* root_com_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

기준 월드 프레임에서의 루트 질량 중심 각속도. 모양은 (num_instances, 3).

이 양은 루트 링크의 질량 중심 프레임이 자체 액터 프레임 기준에서의 각속도를 나타냅니다.

#### *property* root_link_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 위치. 모양은 (num_instances, 3).

이 양은 월드 기준의 루트 강체의 액터 프레임 위치를 나타냅니다.

#### *property* root_link_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 방향 (w, x, y, z). 모양은 (num_instances, 4).

이 양은 루트 강체의 액터 프레임 방향을 나타냅니다.

#### *property* root_link_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 선형 속도. 모양은 (num_instances, 3).

이 양은 월드 기준의 루트 강체의 액터 프레임 선형 속도를 나타냅니다.

#### *property* root_link_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 월드 프레임에서의 루트 링크 각속도. 모양은 (num_instances, 3).

이 양은 세계 좌표계에 대한 루트 강성체의 액터 프레임의 각속도입니다.

#### *property* root_com_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 루트 질량 중심 위치. 크기는 (num_instances, 3)입니다.

이 양은 세계 좌표계에 대한 루트 강성체의 액터 프레임 위치입니다.

#### *property* root_com_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 루트 질량 중심 방향 (w, x, y, z). 크기는 (num_instances, 4)입니다.

이 양은 세계 좌표계에 대한 루트 강성체의 액터 프레임 방향입니다.

#### *property* root_com_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 루트 질량 중심 선속도. 크기는 (num_instances, 3)입니다.

이 양은 세계 좌표계에 대한 루트 강성체의 질량 중심 프레임 선속도입니다.

#### *property* root_com_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 루트 질량 중심 각속도. 크기는 (num_instances, 3)입니다.

이 양은 세계 좌표계에 대한 루트 강성체의 질량 중심 프레임 각속도입니다.

#### *property* body_link_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 모든 바디 위치. 크기는 (num_instances, num_bodies, 3)입니다.

이 양은 세계 좌표계에 대한 관절 연결된 바디들의 액터 프레임 위치입니다.

#### *property* body_link_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 모든 바디 방향 (w, x, y, z). 크기는 (num_instances, num_bodies, 4)입니다.

이 양은 세계 좌표계에 대한 관절 연결된 바디들의 액터 프레임 방향입니다.

#### *property* body_link_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 모든 바디 선속도. 크기는 (num_instances, num_bodies, 3)입니다.

이 양은 세계 좌표계에 대한 관절 연결된 바디들의 질량 중심 프레임 선속도입니다.

#### *property* body_link_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 모든 바디 각속도. 크기는 (num_instances, num_bodies, 3)입니다.

이 양은 세계 좌표계에 대한 관절 연결된 바디들의 질량 중심 프레임 각속도입니다.

#### *property* body_com_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 모든 바디 위치. 크기는 (num_instances, num_bodies, 3)입니다.

이 양은 관절 연결된 바디들의 액터 프레임 위치입니다.

#### *property* body_com_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 관성 원리 축의 방향 (w, x, y, z) (모든 바디). 크기는 (num_instances, num_bodies, 4)입니다.

이 양은 관절 연결된 바디들의 액터 프레임 방향입니다.

#### *property* body_com_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 모든 바디 선속도. 크기는 (num_instances, num_bodies, 3)입니다.

이 양은 관절 연결된 바디들의 질량 중심 프레임 선속도입니다.

#### *property* body_com_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 모든 바디 각속도. 크기는 (num_instances, num_bodies, 3)입니다.

이 양은 관절 연결된 바디들의 질량 중심 프레임 각속도입니다.

#### *property* body_com_lin_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 모든 바디 선가속도. 크기는 (num_instances, num_bodies, 3)입니다.

이 양은 관절 연결된 바디들의 질량 중심 프레임 선가속도입니다.

#### *property* body_com_ang_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

시뮬레이션 세계 좌표계에서의 모든 바디 각가속도. 크기는 (num_instances, num_bodies, 3)입니다.

이 양은 관절 연결된 바디들의 질량 중심 프레임 각가속도입니다.

#### *property* body_com_pos_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

해당 링크 프레임에서의 모든 바디의 질량 중심 위치. 크기는 (num_instances, num_bodies, 3)입니다.

이 양은 바디의 링크 프레임에 대한 질량 중심 위치입니다.

#### *property* body_com_quat_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

해당 링크 프레임에서의 모든 바디의 관성 원리 축 방향 (w, x, y, z). 크기는 (num_instances, num_bodies, 4)입니다.

이 양은 바디의 링크 프레임에 대한 관성 원리 축 방향입니다.

#### *property* root_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_link_pose_w`](#isaaclab.assets.ArticulationData.root_link_pose_w)와 동일합니다.

#### *property* root_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_link_pos_w`](#isaaclab.assets.ArticulationData.root_link_pos_w)와 동일합니다.

#### *property* root_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_link_quat_w`](#isaaclab.assets.ArticulationData.root_link_quat_w)와 동일합니다.

#### *property* root_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_vel_w`](#isaaclab.assets.ArticulationData.root_com_vel_w)와 동일합니다.

#### *property* root_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_lin_vel_w`](#isaaclab.assets.ArticulationData.root_com_lin_vel_w)와 동일합니다.

#### *property* root_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_ang_vel_w`](#isaaclab.assets.ArticulationData.root_com_ang_vel_w)와 동일합니다.

#### *property* root_lin_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_lin_vel_b`](#isaaclab.assets.ArticulationData.root_com_lin_vel_b)와 동일합니다.

#### *property* root_ang_vel_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`root_com_ang_vel_b`](#isaaclab.assets.ArticulationData.root_com_ang_vel_b)와 동일합니다.

#### *property* body_pose_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_link_pose_w`](#isaaclab.assets.ArticulationData.body_link_pose_w)와 동일합니다.

#### *property* body_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_link_pos_w`](#isaaclab.assets.ArticulationData.body_link_pos_w)와 동일합니다.

#### *property* body_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_link_quat_w`](#isaaclab.assets.ArticulationData.body_link_quat_w)와 동일합니다.

#### *property* body_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_vel_w`](#isaaclab.assets.ArticulationData.body_com_vel_w)와 동일합니다.

#### *property* body_lin_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_lin_vel_w`](#isaaclab.assets.ArticulationData.body_com_lin_vel_w)와 동일합니다.

#### *property* body_ang_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_ang_vel_w`](#isaaclab.assets.ArticulationData.body_com_ang_vel_w)와 동일합니다.

#### *property* body_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_acc_w`](#isaaclab.assets.ArticulationData.body_com_acc_w)와 동일합니다.

#### *property* body_lin_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_lin_acc_w`](#isaaclab.assets.ArticulationData.body_com_lin_acc_w)와 동일합니다.

#### *property* body_ang_acc_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_ang_acc_w`](#isaaclab.assets.ArticulationData.body_com_ang_acc_w)와 동일합니다.

#### *property* com_pos_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_pos_b`](#isaaclab.assets.ArticulationData.body_com_pos_b)와 동일합니다.

#### *property* com_quat_b *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

[`body_com_quat_b`](#isaaclab.assets.ArticulationData.body_com_quat_b)와 동일합니다.

#### *property* joint_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`joint_pos_limits`](#isaaclab.assets.ArticulationData.joint_pos_limits)를 사용하십시오.

#### *property* default_joint_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`default_joint_pos_limits`](#isaaclab.assets.ArticulationData.default_joint_pos_limits)를 사용하십시오.

#### *property* joint_velocity_limits *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`joint_vel_limits`](#isaaclab.assets.ArticulationData.joint_vel_limits)를 사용하십시오.

#### *property* joint_friction *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. 대신 [`joint_friction_coeff`](#isaaclab.assets.ArticulationData.joint_friction_coeff)를 사용하십시오.

#### *property* default_joint_friction *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. [`default_joint_friction_coeff`](#isaaclab.assets.ArticulationData.default_joint_friction_coeff)를 대신 사용하세요.

#### *property* fixed_tendon_limit *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. [`fixed_tendon_pos_limits`](#isaaclab.assets.ArticulationData.fixed_tendon_pos_limits)를 대신 사용하세요.

#### *property* default_fixed_tendon_limit *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

더 이상 사용되지 않는 속성입니다. [`default_fixed_tendon_pos_limits`](#isaaclab.assets.ArticulationData.default_fixed_tendon_pos_limits)를 대신 사용하세요.

### *class* isaaclab.assets.ArticulationCfg

Bases: [`AssetBaseCfg`](#isaaclab.assets.AssetBaseCfg)

관절 연결물(아티큘레이션)에 대한 구성 매개변수.

**클래스:**

| [`InitialStateCfg`](#isaaclab.assets.ArticulationCfg.InitialStateCfg)   | 아티큘레이션의 초기 상태.   |
|-------------------------------------------------------------------------|----------------------------|

**속성:**

| [`prim_path`](#isaaclab.assets.ArticulationCfg.prim_path)                                                         | 자산의 프라임 경로(또는 표현식).                                                                    |
|-------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| [`spawn`](#isaaclab.assets.ArticulationCfg.spawn)                                                                 | 자산의 스폰 구성.                                                                                   |
| [`collision_group`](#isaaclab.assets.ArticulationCfg.collision_group)                                             | 자산의 충돌 그룹.                                                                                   |
| [`debug_vis`](#isaaclab.assets.ArticulationCfg.debug_vis)                                                         | 자산의 디버그 시각화 여부.                                                                          |
| [`articulation_root_prim_path`](#isaaclab.assets.ArticulationCfg.articulation_root_prim_path)                     | [`prim_path`](#isaaclab.assets.ArticulationCfg.prim_path) 아래에 있는 아티큘레이션 루트 프라임 경로.    |
| [`init_state`](#isaaclab.assets.ArticulationCfg.init_state)                                                       | 아티큘레이션 객체의 초기 상태.                                                                       |
| [`soft_joint_pos_limit_factor`](#isaaclab.assets.ArticulationCfg.soft_joint_pos_limit_factor)                     | 자산에서 파싱한 관절 위치 제한 범위를 사용할 비율 지정.                                               |
| [`actuators`](#isaaclab.assets.ArticulationCfg.actuators)                                                         | 해당 관절 이름이 있는 로봇의 액추에이터.                                                             |
| [`actuator_value_resolution_debug_print`](#isaaclab.assets.ArticulationCfg.actuator_value_resolution_debug_print) | 입력 cfg가 USD 값과 다른 경우 액추에이터 최종 값의 해상도를 출력합니다. 기본값은 False입니다.       |

#### *class* InitialStateCfg

Bases: [`InitialStateCfg`](#isaaclab.assets.DeformableObjectCfg.InitialStateCfg)

아티큘레이션의 초기 상태.

**속성:**

| [`lin_vel`](#isaaclab.assets.ArticulationCfg.InitialStateCfg.lin_vel)     | 시뮬레이션 월드 프레임에서 루트의 선형 속도.                  |
|---------------------------------------------------------------------------|--------------------------------------------------------------|
| [`ang_vel`](#isaaclab.assets.ArticulationCfg.InitialStateCfg.ang_vel)     | 시뮬레이션 월드 프레임에서 루트의 각속도.                     |
| [`joint_pos`](#isaaclab.assets.ArticulationCfg.InitialStateCfg.joint_pos) | 관절 위치.                                                   |
| [`joint_vel`](#isaaclab.assets.ArticulationCfg.InitialStateCfg.joint_vel) | 관절 속도.                                                   |
| [`pos`](#isaaclab.assets.ArticulationCfg.InitialStateCfg.pos)             | 시뮬레이션 월드 프레임에서 루트의 위치.                       |
| [`rot`](#isaaclab.assets.ArticulationCfg.InitialStateCfg.rot)             | 시뮬레이션 월드 프레임에서 루트의 쿼터니언 회전 (w, x, y, z). |

#### lin_vel *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 선형 속도. 기본값은 (0.0, 0.0, 0.0)입니다.

#### ang_vel *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 각속도. 기본값은 (0.0, 0.0, 0.0)입니다.

#### joint_pos *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

관절 위치. 모든 관절에 대해 기본값은 0.0입니다.

#### joint_vel *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

관절 속도. 모든 관절에 대해 기본값은 0.0입니다.

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 위치. 기본값은 (0.0, 0.0, 0.0)입니다.

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서 루트의 쿼터니언 회전 (w, x, y, z).
기본값은 (1.0, 0.0, 0.0, 0.0)입니다.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

자산의 프라임 경로(또는 표현식).

#### NOTE
이 표현식에는 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot`은 `/World/envs/env_.*/Robot`으로 대체됩니다.

#### spawn *: [SpawnerCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.SpawnerCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

자산의 스폰 구성. 기본값은 None입니다.

None인 경우, 자산 클래스가 프라임을 생성하지 않습니다. 대신 자산이 이미 장면에 존재한다고 가정합니다.

#### collision_group *: Literal[0, -1]*

자산의 충돌 그룹. 기본값은 `0`입니다.

* `-1`: 전역 충돌 그룹(장면의 모든 자산과 충돌).
* `0`: 로컬 충돌 그룹(동일한 환경 내 다른 자산과 충돌).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산의 디버그 시각화 여부. 기본값은 `False`입니다.

#### articulation_root_prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

[`prim_path`](#isaaclab.assets.ArticulationCfg.prim_path) 아래에 있는 아티큘레이션 루트 프라임 경로. 기본값은 None이며, 이 경우 클래스는 USD ArticulationRootAPI를 가진 프라임을 검색합니다.

이 경로는 자산의 [`prim_path`](#isaaclab.assets.ArticulationCfg.prim_path)에 상대적입니다. 자산이 USD 파일에서 로드되는 경우, 이 경로는 USD 스테이지의 루트에 상대적이어야 합니다. 예를 들어, [`prim_path`](#isaaclab.assets.ArticulationCfg.prim_path)에서 로드된 USD 파일에 /robot1과 /robot2에 각각 아티큘레이션이 두 개 있고, robot2를 사용하려면 이 값을 /robot2로 설정해야 합니다.

경로는 슬래시(/)로 시작해야 합니다.

#### init_state *: [InitialStateCfg](#isaaclab.assets.ArticulationCfg.InitialStateCfg)*

아티큘레이션 객체의 초기 상태. 기본값은 0 속도와 0 관절 상태를 가진 초기 자세입니다.

#### soft_joint_pos_limit_factor *: [float](https://docs.python.org/3/library/functions.html#float)*

자산에서 파싱한 관절 위치 제한 범위를 사용할 비율 지정. 기본값은 1.0입니다.

소프트 관절 위치 제한은 이 요소로 확장되어 시뮬레이션된 관절 위치 제한 내의 안전 영역을 지정합니다. 이는 시뮬레이션에서 사용되지 않지만, 학습 에이전트가 관절 위치가 제한을 위반하지 않도록 방지하는 데 유용합니다. 예를 들어, 종료 조건과 관련하여 유용합니다.

소프트 관절 위치 제한은 [`ArticulationData.soft_joint_pos_limits`](#isaaclab.assets.ArticulationData.soft_joint_pos_limits) 속성을 통해 접근할 수 있습니다.

#### actuators *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab.actuators.actuator_base_cfg.ActuatorBaseCfg](isaaclab.actuators.md#isaaclab.actuators.ActuatorBaseCfg)]*

해당 관절 이름이 있는 로봇의 액추에이터.

#### actuator_value_resolution_debug_print *: [bool](https://docs.python.org/3/library/functions.html#bool)*

입력 cfg가 USD 값과 다른 경우 액추에이터 최종 값의 해상도를 출력합니다. 기본값은 False입니다.

## 변형 가능한 물체

### *class* isaaclab.assets.DeformableObject

Bases: [`AssetBase`](#isaaclab.assets.AssetBase)

변형 가능한 객체 자산 클래스입니다.

변형 가능한 객체는 시뮬레이션에서 변형될 수 있는 자산입니다. 일반적으로 stuffed animal이나 음식과 같은 부드러운 물체에 사용됩니다.

 rigid 객체 자산과는 달리, 변형 가능한 객체는 더 복잡한 구조를 가지며 시뮬레이션을 위해 추가적인 처리가 필요합니다. 변형 가능한 객체의 시뮬레이션은 유한 요소 접근법을 따르며, 여기서 객체는 노드와 요소로 구성된 메시로 이산화됩니다. 노드는 요소로 연결되어 있으며, 이 요소들은 객체의 재질 특성을 정의합니다. 노드는 이동하고 변형될 수 있으며, 요소들은 이러한 변화에 반응합니다.

 변형 가능한 객체의 상태는 노드의 위치와 속도로 구성되며, 객체의 루트 위치와 방향이 아닙니다. 노드의 위치와 속도는 시뮬레이션 프레임 내에서 정의됩니다.

 부드러운 물체는 [부분적으로 키네마틱](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/docs/SoftBodies.html#kinematic-soft-bodies)이 될 수 있으며, 여기서 일부 노드는 키네마틱 타겟에 의해 구동되고 나머지는 시뮬레이션됩니다. 키네마틱 타겟은 노드의 원하는 위치를 나타내며, 시뮬레이션은 노드를 이러한 타겟쪽으로 구동합니다. 이는 부분적인 제어가 유용한 경우, 예를 들어 stuffed animal의 머리를 움직이면서 몸 나머지는 시뮬레이션되도록 하는 경우에 적용됩니다.

#### 주의
이 클래스는 실험적이며, 그에 기반한 PhysX API의 변경으로 인해 변경될 수 있습니다. 가능한 한 하위 호환성을 유지하려고 노력하겠지만, 일부 변경이 필요할 수 있습니다.

**속성:**

| [`cfg`](#isaaclab.assets.DeformableObject.cfg)                                                         | 변형 가능한 객체에 대한 구성 인스턴스입니다.                   |
|--------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [`data`](#isaaclab.assets.DeformableObject.data)                                                       | 자산과 관련된 데이터입니다.                                          |
| [`num_instances`](#isaaclab.assets.DeformableObject.num_instances)                                     | 자산의 인스턴스 수입니다.                                           |
| [`num_bodies`](#isaaclab.assets.DeformableObject.num_bodies)                                           | 자산 내의 바디 수입니다.                                            |
| [`root_physx_view`](#isaaclab.assets.DeformableObject.root_physx_view)                                 | 변형 가능한 바디 뷰 (PhysX)입니다.                                 |
| [`material_physx_view`](#isaaclab.assets.DeformableObject.material_physx_view)                         | 변형 가능한 재질 뷰 (PhysX)입니다.                                 |
| [`max_sim_elements_per_body`](#isaaclab.assets.DeformableObject.max_sim_elements_per_body)             | 변형 가능한 바디당 시뮬레이션 메시 요소의 최대 수입니다.         |
| [`max_collision_elements_per_body`](#isaaclab.assets.DeformableObject.max_collision_elements_per_body) | 변형 가능한 바디당 충돌 메시 요소의 최대 수입니다.               |
| [`max_sim_vertices_per_body`](#isaaclab.assets.DeformableObject.max_sim_vertices_per_body)             | 변형 가능한 바디당 시뮬레이션 메시 정점의 최대 수입니다.         |
| [`max_collision_vertices_per_body`](#isaaclab.assets.DeformableObject.max_collision_vertices_per_body) | 변형 가능한 바디당 충돌 메시 정점의 최대 수입니다.             |
| [`device`](#isaaclab.assets.DeformableObject.device)                                                   | 계산에 사용되는 메모리 장치입니다.                                   |
| [`has_debug_vis_implementation`](#isaaclab.assets.DeformableObject.has_debug_vis_implementation)       | 자산이 디버그 시각화를 구현했는지 여부입니다.                      |
| [`is_initialized`](#isaaclab.assets.DeformableObject.is_initialized)                                   | 자산이 초기화되었는지 여부입니다.                                   |

**메서드:**

| [`__init__`](#isaaclab.assets.DeformableObject.__init__)(cfg)                                                           | 변형 가능한 객체를 초기화합니다.                                                                    |
|-------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| [`reset`](#isaaclab.assets.DeformableObject.reset)([env_ids])                                                           | 선택된 환경의 모든 내부 버퍼를 재설정합니다.                                                |
| [`write_data_to_sim`](#isaaclab.assets.DeformableObject.write_data_to_sim)()                                            | 시뮬레이터에 데이터를 씁니다.                                                                        |
| [`update`](#isaaclab.assets.DeformableObject.update)(dt)                                                                | 내부 버퍼를 업데이트합니다.                                                                         |
| [`write_nodal_state_to_sim`](#isaaclab.assets.DeformableObject.write_nodal_state_to_sim)(nodal_state[, env_ids])        | 선택된 환경 인덱스에 대한 노드 상태를 시뮬레이션에 설정합니다.                           |
| [`write_nodal_pos_to_sim`](#isaaclab.assets.DeformableObject.write_nodal_pos_to_sim)(nodal_pos[, env_ids])              | 선택된 환경 인덱스에 대한 노드 위치를 시뮬레이션에 설정합니다.                       |
| [`write_nodal_velocity_to_sim`](#isaaclab.assets.DeformableObject.write_nodal_velocity_to_sim)(nodal_vel[, env_ids])    | 선택된 환경 인덱스에 대한 노드 속도를 시뮬레이션에 설정합니다.                        |
| [`write_nodal_kinematic_target_to_sim`](#isaaclab.assets.DeformableObject.write_nodal_kinematic_target_to_sim)(targets) | 표시된 인덱스에 의해 지정된 변형 가능한 바디의 시뮬레이션 메시에 대한 키네마틱 타겟을 설정합니다. |
| [`transform_nodal_pos`](#isaaclab.assets.DeformableObject.transform_nodal_pos)(nodal_pos[, pos, quat])                  | 포즈 변환을 기반으로 노드 위치를 변환합니다.                                                      |
| [`set_debug_vis`](#isaaclab.assets.DeformableObject.set_debug_vis)(debug_vis)                                           | 자산 데이터의 시각화 여부를 설정합니다.                                                            |
| [`set_visibility`](#isaaclab.assets.DeformableObject.set_visibility)(visible[, env_ids])                                | 자산에 해당하는 prim의 가시성을 설정합니다.                                          |

#### cfg *: [DeformableObjectCfg](#isaaclab.assets.DeformableObjectCfg)*

변형 가능한 객체에 대한 구성 인스턴스입니다.

#### \_\_init_\_(cfg: [DeformableObjectCfg](#isaaclab.assets.DeformableObjectCfg))

변형 가능한 객체를 초기화합니다.

* **매개변수:**
  **cfg** – 구성 인스턴스입니다.

#### *property* data *: [DeformableObjectData](#isaaclab.assets.DeformableObjectData)*

자산과 관련된 데이터입니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

자산의 인스턴스 수입니다.

이는 환경당 자산 인스턴스 수와 환경 수의 곱과 같습니다.

#### *property* num_bodies *: [int](https://docs.python.org/3/library/functions.html#int)*

자산 내의 바디 수입니다.

각 객체가 단일 변형 가능한 바디이므로 항상 1입니다.

#### *property* root_physx_view *: omni.physics.tensors.impl.api.SoftBodyView*

변형 가능한 바디 뷰 (PhysX)입니다.

#### 참고
이 뷰를 사용할 때는 주의가 필요합니다. 특정 방식으로 텐서를 다루어야 합니다.

#### *property* material_physx_view *: physx.SoftBodyMaterialView | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 재질 뷰 (PhysX)입니다.

이 뷰는 선택 사항이며, 재질이 변형 가능한 바디에 바인딩되지 않은 경우 사용 불가능할 수 있습니다. 재질이 사용 불가능한 경우, 재질 속성은 기본값으로 설정됩니다.

#### 참고
이 뷰를 사용할 때는 주의가 필요합니다. 특정 방식으로 텐서를 다루어야 합니다.

#### *property* max_sim_elements_per_body *: [int](https://docs.python.org/3/library/functions.html#int)*

변형 가능한 바디당 시뮬레이션 메시 요소의 최대 수입니다.

#### *property* max_collision_elements_per_body *: [int](https://docs.python.org/3/library/functions.html#int)*

변형 가능한 바디당 충돌 메시 요소의 최대 수입니다.

#### *property* max_sim_vertices_per_body *: [int](https://docs.python.org/3/library/functions.html#int)*

변형 가능한 바디당 시뮬레이션 메시 정점의 최대 수입니다.

#### *property* max_collision_vertices_per_body *: [int](https://docs.python.org/3/library/functions.html#int)*

변형 가능한 바디당 충돌 메시 정점의 최대 수입니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택된 환경의 모든 내부 버퍼를 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 객체의 인덱스입니다. 기본값은 None(모든 인스턴스)입니다.

#### write_data_to_sim()

시뮬레이터에 데이터를 씁니다.

#### update(dt: [float](https://docs.python.org/3/library/functions.html#float))

내부 버퍼를 업데이트합니다.

시간 단계 `dt`는 시뮬레이터에서 제공하지 않는 관절 가속도와 같은 양의 수치적 도함수를 계산하는 데 사용됩니다.

* **매개변수:**
  **dt** – 마지막 `update` 호출 이후 경과한 시간입니다.

#### write_nodal_state_to_sim(nodal_state: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택한 환경 인덱스에 대한 노드 상태를 시뮬레이션에 설정합니다.

노드 상태는 노드 위치와 속도로 구성됩니다. 노드이기 때문에 속도는 trasl레이셔널 구성 요소만 갖습니다. 모든 양은 시뮬레이션 프레임에 있습니다.

* **매개변수:**
  * **nodal_state** – 시뮬레이션 프레임에서의 노드 상태.
    크기는 (len(env_ids), max_sim_vertices_per_body, 6)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_nodal_pos_to_sim(nodal_pos: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택한 환경 인덱스에 대한 노드 위치를 시뮬레이션에 설정합니다.

노드 위치는 변형 가능한 물체의 시뮬레이션 메시의 개별 노드 위치로 구성됩니다. 위치는 시뮬레이션 프레임에 있습니다.

* **매개변수:**
  * **nodal_pos** – 시뮬레이션 프레임에서의 노드 위치.
    크기는 (len(env_ids), max_sim_vertices_per_body, 3)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_nodal_velocity_to_sim(nodal_vel: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

선택한 환경 인덱스에 대한 노드 속도를 시뮬레이션에 설정합니다.

노드 속도는 변형 가능한 물체의 시뮬레이션 메시의 개별 노드 속도로 구성됩니다. 노드이기 때문에 속도는 trasl레이셔널 구성 요소만 갖습니다. 속도는 시뮬레이션 프레임에 있습니다.

* **매개변수:**
  * **nodal_vel** – 시뮬레이션 프레임에서의 노드 속도.
    크기는 (len(env_ids), max_sim_vertices_per_body, 3)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.

#### write_nodal_kinematic_target_to_sim(targets: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션 메시의 변형 가능한 물체(지정된 인덱스에 의해 나타남)의 운동학적 목표를 설정합니다.

운동학적 목표는 변형 가능한 물체의 시뮬레이션 메시의 개별 노드 위치와 노드가 운동학적으로 구동되는지 여부를 나타내는 플래그로 구성됩니다. 위치는 시뮬레이션 프레임에 있습니다.

#### 참고 사항
플래그는 운동학적으로 구동되는 노드에는 0.0으로, 자유 노드에는 1.0로 설정됩니다.

* **매개변수:**
  * **targets** – 노드 위치와 플래그로 구성된 운동학적 목표.
    크기는 (len(env_ids), max_sim_vertices_per_body, 4)입니다.
  * **env_ids** – 환경 인덱스. None이면 모든 인덱스가 사용됩니다.

#### transform_nodal_pos(nodal_pos: torch.tensor, pos: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

포즈 변환을 기반으로 노드 위치를 변환합니다.

이 함수는 포즈 변환을 기반으로 노드 위치의 변환을 계산합니다. 노드 위치에 포즈의 회전 행렬을 곱하고 평행 이동을 더합니다. 내부적으로는 [`isaaclab.utils.math.transform_points()`](isaaclab.utils.md#isaaclab.utils.math.transform_points) 함수를 호출합니다.

* **매개변수:**
  * **nodal_pos** – 시뮬레이션 프레임에서의 노드 위치. 크기는 (N, max_sim_vertices_per_body, 3)입니다.
  * **pos** – 위치 변환. 크기는 (N, 3)입니다.
    기본값은 None이며, 이 경우 위치는 영으로 간주됩니다.
  * **quat** – 쿼터니언(w, x, y, z)로 표현된 방향 변환. 크기는 (N, 4)입니다.
    기본값은 None이며, 이 경우 방향은 단위 행렬로 간주됩니다.
* **반환값:**
  변환된 노드 위치. 크기는 (N, max_sim_vertices_per_body, 3)입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 디바이스.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋에 디버그 시각화가 구현되었는지 여부.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋이 초기화되었는지 여부.

에셋이 초기화되었으면 True를 반환하고, 그렇지 않으면 False를 반환합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

에셋 데이터를 시각화할지 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 에셋 데이터를 시각화할지 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 에셋이 디버그 시각화를 지원하지 않으면 False를 반환합니다.

#### set_visibility(visible: [bool](https://docs.python.org/3/library/functions.html#bool), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

에셋에 해당하는 프리미브의 가시성을 설정합니다.

이 작업은 USD 스테이지에서 에셋에 해당하는 프리미브의 가시성에 영향을 미칩니다. 시뮬레이터에서 에셋의 가시성을 토글하는 데 유용합니다. 예를 들어, 에셋이 사용되지 않을 때 렌더링 오버헤드를 줄이기 위해 에셋을 숨길 수 있습니다.

#### 참고 사항
이 작업은 프리미브의 가시성을 설정하기 위해 PXR API를 사용합니다. 따라서 프리미브 수가 많을 경우 오버헤드가 발생할 수 있습니다.

* **매개변수:**
  * **visible** – 프리미브를 보이게 할지 여부.
  * **env_ids** – 가시성을 설정할 객체의 인덱스. 기본값은 None(모든 인스턴스).

### *class* isaaclab.assets.DeformableObjectData

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

변형 가능한 물체를 위한 데이터 컨테이너.

이 클래스는 시뮬레이션에서의 변형 가능한 물체의 데이터를 포함합니다. 데이터에는 루트 변형 가능한 물체의 노드 상태가 포함됩니다. 데이터는 그렇지 않은 경우를 제외하고는 시뮬레이션 월드 프레임에 저장됩니다.

PhysX에서의 변형 가능한 물체는 물체를 표현하기 위해 두 개의 사면체 메시를 사용합니다:

1. **시뮬레이션 메시**: 이 메시는 시뮬레이션에 사용되며 솔버에 의해 변형되는 메시입니다.
2. **충돌 메시**: 이 메시는 시뮬레이션 메시의 표면과 일치하도록만 필요하며 충돌 감지에 사용됩니다.

노출된 API는 시뮬레이션 메시와 충돌 메시 양쪽의 데이터를 제공합니다. 이는 속성 이름의 sim 및 collision 접두사로 지정됩니다.

데이터는 지연 업데이트 방식으로, 데이터에 접근할 때만 업데이트됩니다. 이는 데이터 계산을 검색하는 데 비용이 많이 드는 경우 유용합니다. 데이터는 버퍼의 타임스탬프가 현재 시뮬레이션 타임스탬프보다 오래될 때 업데이트됩니다. 타임스탬프는 데이터가 업데이트될 때마다 갱신됩니다.

**메서드:**

| [`update`](#isaaclab.assets.DeformableObjectData.update)(dt)   | 변형 가능한 물체의 데이터를 업데이트합니다.   |
|----------------------------------------------------------------|-----------------------------------------------|

**속성:**

| [`default_nodal_state_w`](#isaaclab.assets.DeformableObjectData.default_nodal_state_w)                             | 시뮬레이션 월드 프레임에서의 기본 노드 상태 `[nodal_pos, nodal_vel]`                                                     |
|--------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| [`nodal_kinematic_target`](#isaaclab.assets.DeformableObjectData.nodal_kinematic_target)                           | 변형 가능한 물체의 시뮬레이션 메시 운동학적 목표.                                                                |
| [`nodal_pos_w`](#isaaclab.assets.DeformableObjectData.nodal_pos_w)                                                 | 시뮬레이션 월드 프레임에서의 노드 위치.                                                                                  |
| [`nodal_vel_w`](#isaaclab.assets.DeformableObjectData.nodal_vel_w)                                                 | 시뮬레이션 월드 프레임에서의 노드 속도.                                                                                 |
| [`nodal_state_w`](#isaaclab.assets.DeformableObjectData.nodal_state_w)                                             | 시뮬레이션 월드 프레임에서의 노드 상태 `[nodal_pos, nodal_vel]`                                                         |
| [`sim_element_quat_w`](#isaaclab.assets.DeformableObjectData.sim_element_quat_w)                                   | 시뮬레이션 월드 프레임에서의 변형 가능한 물체의 시뮬레이션 메시 요소별 회전(쿼터니언).                  |
| [`collision_element_quat_w`](#isaaclab.assets.DeformableObjectData.collision_element_quat_w)                       | 변형 가능한 물체의 시뮬레이션 월드 프레임에서 충돌 메시 요소별 사원수 회전               |
| [`sim_element_deform_gradient_w`](#isaaclab.assets.DeformableObjectData.sim_element_deform_gradient_w)             | 변형 가능한 물체의 시뮬레이션 월드 프레임에서 시뮬레이션 메시 요소별 2차 변형 그라디언트 텐서 |
| [`collision_element_deform_gradient_w`](#isaaclab.assets.DeformableObjectData.collision_element_deform_gradient_w) | 변형 가능한 물체의 시뮬레이션 월드 프레임에서 충돌 메시 요소별 2차 변형 그라디언트 텐서  |
| [`sim_element_stress_w`](#isaaclab.assets.DeformableObjectData.sim_element_stress_w)                               | 변형 가능한 물체의 시뮬레이션 월드 프레임에서 시뮬레이션 메시 요소별 2차 코시 응력 텐서     |
| [`collision_element_stress_w`](#isaaclab.assets.DeformableObjectData.collision_element_stress_w)                   | 변형 가능한 물체의 시뮬레이션 월드 프레임에서 충돌 메시 요소별 2차 코시 응력 텐서         |
| [`root_pos_w`](#isaaclab.assets.DeformableObjectData.root_pos_w)                                                   | 변형 가능한 물체의 시뮬레이션 월드 프레임에서 시뮬레이션 메시의 노드 위치에서 유래된 루트 위치 |
| [`root_vel_w`](#isaaclab.assets.DeformableObjectData.root_vel_w)                                                   | 변형 가능한 물체의 시뮬레이션 월드 프레임에서 정점 속도에서 유래된 루트 속도               |

#### update(dt: [float](https://docs.python.org/3/library/functions.html#float))

변형 가능한 물체의 데이터를 업데이트합니다.

* **매개변수:**
  **dt** – 업데이트할 시간 단계입니다. 양의 값이어야 합니다.

#### default_nodal_state_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

시뮬레이션 월드 프레임에서의 기본 노드 상태 `[nodal_pos, nodal_vel]`.
모양은 (num_instances, max_sim_vertices_per_body, 6)입니다.

#### nodal_kinematic_target *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

변형 가능한 물체의 시뮬레이션 메시 운동학적 대상.
모양은 (num_instances, max_sim_vertices_per_body, 4)입니다.

운동학적 대상은 시뮬레이션 메시 정점을 목표 위치로 이동시키는 데 사용됩니다.
대상은 (x, y, z, is_not_kinematic)로 저장되며, 여기서 “is_not_kinematic”은 정점이 운동학적인지 여부를 나타내는 바이너리 플래그입니다.
플래그는 운동학적 정점에 대해 0으로 설정되고, 비운동학적 정점에 대해 1로 설정됩니다.

#### *property* nodal_pos_w

시뮬레이션 월드 프레임에서의 노드 위치. 모양은 (num_instances, max_sim_vertices_per_body, 3)입니다.

#### *property* nodal_vel_w

시뮬레이션 월드 프레임에서의 노드 속도. 모양은 (num_instances, max_sim_vertices_per_body, 3)입니다.

#### *property* nodal_state_w

시뮬레이션 월드 프레임에서의 노드 상태 `[nodal_pos, nodal_vel]`.
모양은 (num_instances, max_sim_vertices_per_body, 6)입니다.

#### *property* sim_element_quat_w

시뮬레이션 월드 프레임에서의 변형 가능한 물체의 시뮬레이션 메시 요소별 사원수 회전.
모양은 (num_instances, max_sim_elements_per_body, 4)입니다.

회전은 (w, x, y, z) 순서의 사원수로 저장됩니다.

#### *property* collision_element_quat_w

시뮬레이션 월드 프레임에서의 변형 가능한 물체의 충돌 메시 요소별 사원수 회전.
모양은 (num_instances, max_collision_elements_per_body, 4)입니다.

회전은 (w, x, y, z) 순서의 사원수로 저장됩니다.

#### *property* sim_element_deform_gradient_w

시뮬레이션 월드 프레임에서의 변형 가능한 물체의 시뮬레이션 메시 요소별 2차 변형 그라디언트 텐서.
모양은 (num_instances, max_sim_elements_per_body, 3, 3)입니다.

#### *property* collision_element_deform_gradient_w

시뮬레이션 월드 프레임에서의 변형 가능한 물체의 충돌 메시 요소별 2차 변형 그라디언트 텐서.
모양은 (num_instances, max_collision_elements_per_body, 3, 3)입니다.

#### *property* sim_element_stress_w

시뮬레이션 월드 프레임에서의 변형 가능한 물체의 시뮬레이션 메시 요소별 2차 코시 응력 텐서.
모양은 (num_instances, max_sim_elements_per_body, 3, 3)입니다.

#### *property* collision_element_stress_w

시뮬레이션 월드 프레임에서의 변형 가능한 물체의 충돌 메시 요소별 2차 코시 응력 텐서.
모양은 (num_instances, max_collision_elements_per_body, 3, 3)입니다.

#### *property* root_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

변형 가능한 물체의 시뮬레이션 월드 프레임에서 시뮬레이션 메시의 노드 위치에서 유래된 루트 위치. 모양은 (num_instances, 3)입니다.

이 양은 노드 위치의 평균으로 계산됩니다.

#### *property* root_vel_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

변형 가능한 물체의 시뮬레이션 월드 프레임에서 정점 속도에서 유래된 루트 속도. 모양은 (num_instances, 3)입니다.

이 양은 노드 속도의 평균으로 계산됩니다.

### *class* isaaclab.assets.DeformableObjectCfg

Bases: [`AssetBaseCfg`](#isaaclab.assets.AssetBaseCfg)

변형 가능한 물체의 구성 매개변수.

**클래스:**

| [`InitialStateCfg`](#isaaclab.assets.DeformableObjectCfg.InitialStateCfg)   | 자산의 초기 상태.   |
|-----------------------------------------------------------------------------|-------------------------------|

**속성:**

| [`prim_path`](#isaaclab.assets.DeformableObjectCfg.prim_path)             | 자산의 프라임 경로(또는 표현식).                 |
|---------------------------------------------------------------------------|---------------------------------------------------------|
| [`spawn`](#isaaclab.assets.DeformableObjectCfg.spawn)                     | 자산의 스폰 구성.                      |
| [`init_state`](#isaaclab.assets.DeformableObjectCfg.init_state)           | 강체 물체의 초기 상태.                      |
| [`collision_group`](#isaaclab.assets.DeformableObjectCfg.collision_group) | 자산의 충돌 그룹.                           |
| [`debug_vis`](#isaaclab.assets.DeformableObjectCfg.debug_vis)             | 자산에 대한 디버그 시각화를 활성화할지 여부.    |
| [`visualizer_cfg`](#isaaclab.assets.DeformableObjectCfg.visualizer_cfg)   | 시각화 마커에 대한 구성 객체. |

#### *class* InitialStateCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

자산의 초기 상태.

이것은 시뮬레이션에 스폰될 때 자산의 기본 초기 상태를 정의하며, 시뮬레이션이 재설정될 때의 기본 상태도 정의합니다.

초기 상태를 파싱한 후, 자산 클래스는 이 정보를 자산 클래스의 `data` 속성에 저장합니다. 이를 통해 사용자는 시뮬레이션 중에 자산의 상태를 수정할 수 있습니다. 예를 들어, 재설정 시에 사용할 수 있습니다.

**속성:**

| [`pos`](#isaaclab.assets.DeformableObjectCfg.InitialStateCfg.pos)   | 시뮬레이션 월드 프레임에서의 루트 위치.                         |
|---------------------------------------------------------------------|-------------------------------------------------------------------------|
| [`rot`](#isaaclab.assets.DeformableObjectCfg.InitialStateCfg.rot)   | 시뮬레이션 월드 프레임에서의 루트 사원수 회전 (w, x, y, z). |

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서의 루트 위치. 기본값은 (0.0, 0.0, 0.0)입니다.

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

시뮬레이션 월드 프레임에서의 루트 사원수 회전 (w, x, y, z).
기본값은 (1.0, 0.0, 0.0, 0.0)입니다.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

자산의 프라임 경로(또는 표현식).

#### NOTE
표현식에는 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot`은 `/World/envs/env_.*/Robot`으로 대체됩니다.

#### spawn *: [SpawnerCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.SpawnerCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

자산의 스폰 구성. 기본값은 None입니다.

None인 경우, 자산 클래스는 어떤 프라임도 스폰하지 않습니다. 대신, 자산이 이미 сцีน에 존재한다고 가정합니다.

#### init_state *: [InitialStateCfg](#isaaclab.assets.DeformableObjectCfg.InitialStateCfg)*

강체 물체의 초기 상태. 기본값은 항등 자세입니다.

#### collision_group *: Literal[0, -1]*

자산의 충돌 그룹. 기본값은 `0`입니다.

* `-1`: 글로벌 충돌 그룹 ( scena 내의 모든 자산과 충돌).
* `0`: 로컬 충돌 그룹 (같은 환경 내의 다른 자산과 충돌).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산에 대한 디버그 시각화를 활성화할지 여부. 기본값은 `False`입니다.

#### visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

시각화 마커에 대한 구성 객체. 기본값은 DEFORMABLE_TARGET_MARKER_CFG입니다.

#### NOTE
이 속성은 디버그 시각화가 활성화된 경우에만 사용됩니다.
