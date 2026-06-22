# isaaclab.scene

대화형 씬 정의를 포함한 하위 패키지입니다.

씬은 시뮬레이션에 추가할 수 있는 지형, 관절형 구조, 센서, 조명 등 다양한 엔티티의 집합입니다. 그러나 이 엔티티 중 사용자가 직접 상호작용하는 데 관련된 엔티티는 일부에 불과합니다. 예를 들어, 사용자는 씬의 로봇과 상호작용을 원할 수 있지만 지형이나 조명과는 상호작용하지 않을 수 있습니다. 따라서 다양한 엔티티를 단일 클래스인 [`InteractiveScene`](#isaaclab.scene.InteractiveScene)에 통합했습니다.

대화형 씬은 다음 작업을 수행합니다.

1. 구성 클래스 [`InteractiveSceneCfg`](#isaaclab.scene.InteractiveSceneCfg)를 파싱하여 씬을 생성합니다. 이 구성 클래스는 사용자가 상속하여 씬에 엔티티를 추가합니다.
2. 사용자가 지정한 환경 수에 따라 엔티티를 복제합니다.
3. 엔티티를 유형별(예: 관절형 구조, 센서 등)으로 그룹화합니다.
4. 씬의 엔티티에 대한 공통 작업(예: 내부 버퍼 재설정, 시뮬레이션으로 버퍼 쓰기, 시뮬레이션에서 버퍼 업데이트 등)을 통합하는 메서드 세트를 제공합니다.

대화형 씬은 프레임워크의 다양한 모듈에 전달되어 다양한 작업을 수행할 수 있습니다. 예를 들어, 씬의 상태를 기반으로 관측값을 계산하거나, 씬을 무작위화하거나, 씬에 동작을 적용할 수 있습니다. 이러한 작업은 프레임워크의 다양한 "관리자"에서 처리됩니다. 자세한 내용은 [`isaaclab.managers`](isaaclab.managers.md#module-isaaclab.managers) 하위 패키지를 참조하십시오.

### 클래스

| [`InteractiveScene`](#isaaclab.scene.InteractiveScene)       | 시뮬레이션에 추가된 엔티티를 포함하는 씬입니다.   |
|--------------------------------------------------------------|-----------------------------------------------------------|
| [`InteractiveSceneCfg`](#isaaclab.scene.InteractiveSceneCfg) | 대화형 씬에 대한 구성입니다.                  |

## 대화형 씬

### *class* isaaclab.scene.InteractiveScene

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

시뮬레이션에 추가된 엔티티를 포함하는 씬입니다.

대화형 씬은 [`InteractiveSceneCfg`](#isaaclab.scene.InteractiveSceneCfg) 클래스를 파싱하여 씬을 생성합니다. 지정된 환경 수에 따라 엔티티를 복제하고 다양한 범주(예: 관절형 구조, 센서 등)로 그룹화합니다.

복제는 두 가지 방식으로 수행할 수 있습니다.

* 모든 환경이 동일한 에셋을 포함하는 작업의 경우, 더 효율적인 복제 패러다임을 사용하여 환경 생성 속도를 높일 수 있습니다. 이는 `replicate_physics` 플래그로 지정됩니다.
  ```python
  scene = InteractiveScene(cfg=InteractiveSceneCfg(replicate_physics=True))
  ```
* 환경마다 별도의 에셋이 필요한 작업의 경우, `replicate_physics`를 False로 설정해야 하며, 이는 전체 시작 시간에 일부 비용을 추가합니다.
  ```python
  scene = InteractiveScene(cfg=InteractiveSceneCfg(replicate_physics=False))
  ```

각 엔티티는 구성 클래스에서의 이름을 기반으로 씬에 등록됩니다. 예를 들어, 사용자가 구성 클래스에서 다음과 같이 로봇을 지정하는 경우:

```python
from isaaclab.scene import InteractiveSceneCfg
from isaaclab.utils import configclass

from isaaclab_assets.robots.anymal import ANYMAL_C_CFG


@configclass
class MySceneCfg(InteractiveSceneCfg):
    # 각 환경에 생성된 ANYmal-C 로봇
    robot = ANYMAL_C_CFG.replace(prim_path="{ENV_REGEX_NS}/Robot")
```

그런 다음 씬에서 로봇에 다음과 같이 접근할 수 있습니다:

```python
from isaaclab.scene import InteractiveScene

# 128개의 환경 생성
scene = InteractiveScene(cfg=MySceneCfg(num_envs=128))

# 씬에서 로봇 접근
robot = scene["robot"]
# 유형 기준으로 로봇 접근
robot = scene.articulations["robot"]
```

[`InteractiveSceneCfg`](#isaaclab.scene.InteractiveSceneCfg) 클래스에 에셋 엔티티가 포함되지 않은 경우에도, [`InteractiveScene`](#isaaclab.scene.InteractiveScene) 클래스 외부에서 단계에 에셋을 추가하면 클로닝 프로세스를 여전히 트리거할 수 있습니다:

```python
scene = InteractiveScene(cfg=InteractiveSceneCfg(num_envs=128, replicate_physics=True))
scene.clone_environments()
```

#### 참고
씬은 엔티티에 대한 공통 작업만 수행한다는 점에 유의해야 합니다. 예를 들어, 내부 버퍼 재설정, 시뮬레이션으로 버퍼 쓰기, 시뮬레이션에서 버퍼 업데이트 등이 해당됩니다. 씬은 엔티티별 특정 작업을 수행하지 않습니다. 예를 들어, 로봇에 동작을 적용하거나 로봇으로부터 관측값을 계산하는 작업은 수행하지 않습니다. 이러한 작업은 프레임워크의 다양한 "관리자" 모듈에서 처리됩니다. 자세한 내용은 [`isaaclab.managers`](isaaclab.managers.md#module-isaaclab.managers) 하위 패키지를 참조하십시오.

**메서드:**

| [`__init__`](#isaaclab.scene.InteractiveScene.__init__)(cfg)                                    | 씬을 초기화합니다.                                   |
|-------------------------------------------------------------------------------------------------|----------------------------------------------------------|
| [`clone_environments`](#isaaclab.scene.InteractiveScene.clone_environments)([copy_from_source]) | 환경 `/World/envs/env_0`의 클론을 생성합니다.   |
| [`filter_collisions`](#isaaclab.scene.InteractiveScene.filter_collisions)([global_prim_paths])  | 환경 충돌을 필터링합니다.                          |
| [`reset`](#isaaclab.scene.InteractiveScene.reset)([env_ids])                                    | 씬 엔티티를 재설정합니다.                               |
| [`write_data_to_sim`](#isaaclab.scene.InteractiveScene.write_data_to_sim)()                     | 씬 엔티티의 데이터를 시뮬레이션에 씁니다. |
| [`update`](#isaaclab.scene.InteractiveScene.update)(dt)                                         | 씬 엔티티를 업데이트합니다.                               |
| [`reset_to`](#isaaclab.scene.InteractiveScene.reset_to)(state[, env_ids, is_relative])          | 제공된 상태로 씬의 엔티티를 재설정합니다.  |
| [`get_state`](#isaaclab.scene.InteractiveScene.get_state)([is_relative])                        | 씬 엔티티의 상태를 반환합니다.                 |
| [`keys`](#isaaclab.scene.InteractiveScene.keys)()                                               | 씬 엔티티의 키를 반환합니다.                  |

**특성:**

| [`physics_scene_path`](#isaaclab.scene.InteractiveScene.physics_scene_path)             | USD 물리 씬의 경로입니다.                                                             |
|-----------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| [`physics_dt`](#isaaclab.scene.InteractiveScene.physics_dt)                             | 씬의 물리 타임스텝입니다.                                                         |
| [`device`](#isaaclab.scene.InteractiveScene.device)                                     | 씬이 생성된 장치입니다.                                                       |
| [`env_ns`](#isaaclab.scene.InteractiveScene.env_ns)                                     | 모든 환경이 생성된 네임스페이스 `/World/envs`입니다.                                 |
| [`env_regex_ns`](#isaaclab.scene.InteractiveScene.env_regex_ns)                         | 모든 환경이 생성된 네임스페이스 `/World/envs/env_.*`입니다.                          |
| [`num_envs`](#isaaclab.scene.InteractiveScene.num_envs)                                 | 씬이 처리하는 환경의 수입니다.                                               |
| [`env_origins`](#isaaclab.scene.InteractiveScene.env_origins)                           | 씬의 환경 원점입니다.                                                  |
| [`terrain`](#isaaclab.scene.InteractiveScene.terrain)                                   | 씬의 지형입니다.                                                                      |
| [`articulations`](#isaaclab.scene.InteractiveScene.articulations)                       | 씬의 관절형 구조 사전입니다.                                                    |
| [`deformable_objects`](#isaaclab.scene.InteractiveScene.deformable_objects)             | 씬의 변형 가능한 객체 사전입니다.                                               |
| [`rigid_objects`](#isaaclab.scene.InteractiveScene.rigid_objects)                       | 씬의 강체 객체 사전입니다.                                                    |
| [`rigid_object_collections`](#isaaclab.scene.InteractiveScene.rigid_object_collections) | 씬의 강체 객체 컬렉션 사전입니다.                                         |
| [`sensors`](#isaaclab.scene.InteractiveScene.sensors)                                   | 씬의 센서 사전(카메라 및 접촉 리포터 등).               |
| [`surface_grippers`](#isaaclab.scene.InteractiveScene.surface_grippers)                 | 씬의 표면 그리퍼 사전입니다.                                             |
| [`extras`](#isaaclab.scene.InteractiveScene.extras)                                     | 에셋이나 센서에서 상속하지 않는 다양한 시뮬레이션 객체의 사전입니다. |
| [`state`](#isaaclab.scene.InteractiveScene.state)                                       | 시뮬레이션 월드 프레임에서의 씬 엔티티 상태 사전입니다.                 |

#### \_\_init_\_(cfg: [InteractiveSceneCfg](#isaaclab.scene.InteractiveSceneCfg))

씬을 초기화합니다.

* **매개변수:**
  **cfg** – 씬의 구성 클래스입니다.

#### clone_environments(copy_from_source: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

`/World/envs/env_0` 환경의 클론을 생성합니다.

* **매개변수:**
  * **copy_from_source** – (bool): False로 설정된 경우, 클론은 `/World/envs/env_0`에서 상속받으며 그 변경 사항을 반영합니다.
  * **True** (*clones are independent copies* *of* *the source prim and won't reflect its changes* *(**start-up*) – 
  * **False.** (*may increase* *)* *. Defaults to*) – 

#### filter_collisions(global_prim_paths: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

환경 간 충돌을 필터링합니다.

`/World/envs/env_.*` 내 환경 간의 충돌을 비활성화하고, 전역 프림 경로(예: 지면 평면)에 있는 프림과의 충돌을 활성화합니다.

* **매개변수:**
  **global_prim_paths** – 충돌을 활성화할 전역 프림 경로 목록입니다.
  기본값은 None이며, 이 경우 전역 프림 경로는 고려되지 않습니다.

#### *property* physics_scene_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

USD 물리 씬의 경로입니다.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

씬의 물리 타임스텝입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬이 생성된 디바이스입니다.

#### *property* env_ns *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

모든 환경이 생성되는 네임스페이스 `/World/envs`입니다.

환경은 이 네임스페이스 기준으로 “env_{N}” 프림 아래에 존재하며, 여기서 N은 자연수입니다.

#### *property* env_regex_ns *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

모든 환경이 생성되는 네임스페이스 `/World/envs/env_.*`입니다.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

씬에서 처리하는 환경의 수입니다.

#### *property* env_origins *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

씬 내 환경의 원점입니다. 형태는 (num_envs, 3)입니다.

#### *property* terrain *: [TerrainImporter](isaaclab.terrains.md#isaaclab.terrains.TerrainImporter) | [None](https://docs.python.org/3/library/constants.html#None)*

씬의 지형입니다. None인 경우, 씬에는 지형이 없습니다.

#### NOTE
지형은 환경 원점을 정의하고 다른 잡다한 엔티티와 다르게 처리되므로 [`extras`](#isaaclab.scene.InteractiveScene.extras)와 별도로 취급합니다.

#### *property* articulations *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab.assets.articulation.articulation.Articulation](isaaclab.assets.md#isaaclab.assets.Articulation)]*

씬 내 아티큘레이션의 사전입니다.

#### *property* deformable_objects *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab.assets.deformable_object.deformable_object.DeformableObject](isaaclab.assets.md#isaaclab.assets.DeformableObject)]*

씬 내 변형 가능한 오브젝트의 사전입니다.

#### *property* rigid_objects *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab.assets.rigid_object.rigid_object.RigidObject](isaaclab.assets.md#isaaclab.assets.RigidObject)]*

씬 내 강체 오브젝트의 사전입니다.

#### *property* rigid_object_collections *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab.assets.rigid_object_collection.rigid_object_collection.RigidObjectCollection](isaaclab.assets.md#isaaclab.assets.RigidObjectCollection)]*

씬 내 강체 오브젝트 컬렉션의 사전입니다.

#### *property* sensors *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab.sensors.sensor_base.SensorBase](isaaclab.sensors.md#isaaclab.sensors.SensorBase)]*

씬 내 센서(예: 카메라 및 접촉 보고자)의 사전입니다.

#### *property* surface_grippers *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), isaaclab.assets.surface_gripper.surface_gripper.SurfaceGripper]*

씬 내 표면 그리퍼의 사전입니다.

#### *property* extras *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab.sim.views.xform_prim_view.XformPrimView](isaaclab.sim.views.md#isaaclab.sim.views.XformPrimView)]*

에셋이나 센서에서 상속받지 않는 다양한 시뮬레이션 오브젝트의 사전입니다.

키들은 다양한 오브젝트의 이름이며, 값들은 해당 프림의
[`XformPrimView`](isaaclab.sim.views.md#isaaclab.sim.views.XformPrimView) 인스턴스입니다.

예를 들어, 런타임 시 변경하고 싶지 않은 속성이나 속성이 없는 조명 또는 기타 프롭과 같은 씬 내 객체를 이 사전에 추가할 수 있습니다.

#### NOTE
이들은 씬에 의해 리셋 또는 업데이트되지 않습니다. 인터랙티브 씬에 의해 반드시 처리되지는 않는 다른 프림이지만, 사용자가 액세스하기에 유용합니다.

#### *property* state *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]]*

시뮬레이션 월드 프레임에서 씬 엔티티의 상태를 담은 사전입니다.

형식에 대해서는 [`get_state()`](#isaaclab.scene.InteractiveScene.get_state)를 참조하십시오.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

씬 엔티티를 리셋합니다.

* **매개변수:**
  **env_ids** – 리셋할 환경의 인덱스입니다.
  기본값은 None이며, 이 경우 모든 인스턴스가 리셋됩니다.

#### write_data_to_sim()

씬 엔티티의 데이터를 시뮬레이션에 씁니다.

#### update(dt: [float](https://docs.python.org/3/library/functions.html#float)) → [None](https://docs.python.org/3/library/constants.html#None)

씬 엔티티를 업데이트합니다.

* **매개변수:**
  **dt** – 마지막 [`update()`](#isaaclab.scene.InteractiveScene.update) 호출로부터 경과한 시간입니다.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

제공된 상태로 씬의 엔티티를 리셋합니다.

* **매개변수:**
  * **state** – 씬 엔티티를 리셋할 상태입니다. 형식에 대해서는 [`get_state()`](#isaaclab.scene.InteractiveScene.get_state)를 참조하십시오.
  * **env_ids** – 리셋할 환경의 인덱스입니다. 기본값은 None이며, 이 경우 모든 환경 인스턴스가 리셋됩니다.
  * **is_relative** – True로 설정된 경우, 상태는 환경 원점을 기준으로 상대적인 것으로 간주됩니다. 기본값은 False입니다.

#### get_state(is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]]

씬 엔티티의 상태를 반환합니다.

엔티티의 유형에 따라 상태는 다양한 구성 요소로 이루어집니다.

* 아티큘레이션의 경우, 상태는 루트 포즈, 루트 속도, 관절 위치 및 속도로 구성됩니다.
* 변형 가능한 오브젝트의 경우, 상태는 노드 위치 및 속도로 구성됩니다.
* 강체 오브젝트의 경우, 상태는 루트 포즈 및 루트 속도로 구성됩니다.

반환된 상태는 다음 형식을 갖는 사전입니다:

```python
{
    "articulation": {
        "entity_1_name": {
            "root_pose": torch.Tensor,
            "root_velocity": torch.Tensor,
            "joint_position": torch.Tensor,
            "joint_velocity": torch.Tensor,
        },
        "entity_2_name": {
            "root_pose": torch.Tensor,
            "root_velocity": torch.Tensor,
            "joint_position": torch.Tensor,
            "joint_velocity": torch.Tensor,
        },
    },
    "deformable_object": {
        "entity_3_name": {
            "nodal_position": torch.Tensor,
            "nodal_velocity": torch.Tensor,
        }
    },
    "rigid_object": {
        "entity_4_name": {
            "root_pose": torch.Tensor,
            "root_velocity": torch.Tensor,
        }
    },
}
```

여기서 `entity_N_name`은 씬에 등록된 엔티티의 이름입니다.

* **매개변수:**
  **is_relative** – True로 설정된 경우, 상태는 환경 기원을 기준으로 상대적으로 간주됩니다.
  기본값은 False입니다.
* **반환:**
  장면 엔티티의 상태 딕셔너리입니다.

#### keys() → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

장면 엔티티의 키를 반환합니다.

* **반환:**
  장면 엔티티의 키입니다.

### *class* isaaclab.scene.InteractiveSceneCfg

대화형 장면의 구성입니다.

사용자는 이 클래스를 상속하여 장면에 엔티티를 추가할 수 있습니다. 이렇게 구성된 정보는
[`InteractiveScene`](#isaaclab.scene.InteractiveScene) 클래스에서 파싱되어 장면을 생성합니다.

#### 참고
장면에 엔티티를 추가하는 작업은 구성의 속성 순서에 민감합니다.
속성을 추가하고 싶은 순서대로 추가했는지 확인하세요.
지정 순서의 권장 사항은 지형, 물리 관련 자산(아티큘레이션 및 강체), 센서, 비물리 관련 자산(조명) 순입니다.

예를 들어, 장면에 로봇을 추가하려면 사용자는 다음과 같이 구성 클래스를 생성할 수 있습니다:

```python
import isaaclab.sim as sim_utils
from isaaclab.assets import AssetBaseCfg
from isaaclab.scene import InteractiveSceneCfg
from isaaclab.sensors.ray_caster import GridPatternCfg, RayCasterCfg
from isaaclab.utils import configclass

from isaaclab_assets.robots.anymal import ANYMAL_C_CFG


@configclass
class MySceneCfg(InteractiveSceneCfg):
    # 지형 - 평평한 지형 평면
    terrain = TerrainImporterCfg(
        prim_path="/World/ground",
        terrain_type="plane",
    )

    # 아티큘레이션 - 로봇 1
    robot_1 = ANYMAL_C_CFG.replace(prim_path="{ENV_REGEX_NS}/Robot_1")
    # 아티큘레이션 - 로봇 2
    robot_2 = ANYMAL_C_CFG.replace(prim_path="{ENV_REGEX_NS}/Robot_2")
    robot_2.init_state.pos = (0.0, 1.0, 0.6)

    # 센서 - 로봇 1의 베이스에 부착된 지면을 스캔하는 레이스 캐스터
    height_scanner = RayCasterCfg(
        prim_path="{ENV_REGEX_NS}/Robot_1/base",
        offset=RayCasterCfg.OffsetCfg(pos=(0.0, 0.0, 20.0)),
        ray_alignment="yaw",
        pattern_cfg=GridPatternCfg(resolution=0.1, size=[1.6, 1.0]),
        debug_vis=True,
        mesh_prim_paths=["/World/ground"],
    )

    # 추가 - 조명
    light = AssetBaseCfg(
        prim_path="/World/light",
        spawn=sim_utils.DistantLightCfg(intensity=3000.0, color=(0.75, 0.75, 0.75)),
        init_state=AssetBaseCfg.InitialStateCfg(pos=(0.0, 0.0, 500.0)),
    )
```

**속성:**

| [`num_envs`](#isaaclab.scene.InteractiveSceneCfg.num_envs)                     | 장면이 처리하는 환경 인스턴스 수입니다.                     |
|--------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`env_spacing`](#isaaclab.scene.InteractiveSceneCfg.env_spacing)               | 환경 간 간격.                                             |
| [`lazy_sensor_update`](#isaaclab.scene.InteractiveSceneCfg.lazy_sensor_update) | 센서가 접근될 때만 업데이트할지 여부.                     |
| [`replicate_physics`](#isaaclab.scene.InteractiveSceneCfg.replicate_physics)   | Cloner API 사용 시 물리 스키마 복제 활성화/비활성화.   |
| [`filter_collisions`](#isaaclab.scene.InteractiveSceneCfg.filter_collisions)   | 클론된 환경 간 충돌 필터링 활성화/비활성화.             |
| [`clone_in_fabric`](#isaaclab.scene.InteractiveSceneCfg.clone_in_fabric)       | 패브릭에서 클론 활성화/비활성화.                         |

#### num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

장면이 처리하는 환경 인스턴스 수입니다.

#### env_spacing *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 간 간격.

이 값은 장면 내 환경 기원 간의 기본 거리를 나타냅니다. 환경 수가 1보다 클 때만 사용됩니다.

#### lazy_sensor_update *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 접근될 때만 업데이트할지 여부. 기본값은 True입니다.

참이면, 센서 데이터는 속성 `data`가 접근될 때만 업데이트됩니다. 그렇지 않으면, 센서가 업데이트될 때마다 센서 데이터가 업데이트됩니다.

#### replicate_physics *: [bool](https://docs.python.org/3/library/functions.html#bool)*

Cloner API 사용 시 물리 스키마 복제 활성화/비활성화. 기본값은 True입니다.

참이면, 시뮬레이션은 모든 클론된 환경에서 동일한 자산 인스턴스(USD 프림)를 갖습니다.
내부적으로는 scene 설정을 최적화하고 물리 단계 파서를 통해 파싱하는 과정을 최적화합니다.

거짓이면, 시뮬레이션은 각 환경마다 별도의 자산 인스턴스(USD 프림)를 가질 수 있습니다.
이 유연성은 scene 설정 및 파싱 속도의 저하라는 대가를 치릅니다.

#### 참고
변형 가능한 객체와 같은 특정 프림 타입의 파싱 최적화는 현재 물리 엔진에서 지원되지 않습니다.
이러한 경우, 이 플래그를 False로 설정해야 합니다.

#### filter_collisions *: [bool](https://docs.python.org/3/library/functions.html#bool)*

클론된 환경 간 충돌 필터링 활성화/비활성화. 기본값은 True입니다.

참이면, 클론된 환경 간에는 충돌이 발생하지 않습니다.

거짓이면, 시뮬레이션은 환경 간에 충돌을 생성합니다.

#### 참고
물리 복제가 활성화된 경우에만 직접 워크플로에서 충돌을 자동으로 필터링할 수 있습니다.
`replicated_physics`가 `False`이고 collision 필터링이 필요한 경우,
`scene.filter_collisions()`를 호출해야 합니다.

#### clone_in_fabric *: [bool](https://docs.python.org/3/library/functions.html#bool)*

패브릭에서 클론 활성화/비활성화. 기본값은 False입니다.

Omniverse Fabric은 장면 생성 시 클론을 수행하는 더욱 최적화된 방법입니다.
이를 통해 장면 생성 시간을 단축할 수 있습니다. 그러나 USD API를 통해 스테이지에 접근하는 유연성이 제한되고,
대신 스테이지는 USDRT를 통해 접근해야 합니다.

#### 참고
패브릭에서의 클론은 `replicated_physics`도 활성화된 경우에만 가능합니다.
`replicated_physics`가 `False`이면, 패브릭에서의 클론은 자동으로 `False`로 기본값이 설정됩니다.
