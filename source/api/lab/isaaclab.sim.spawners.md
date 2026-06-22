# isaaclab.sim.spawners

옴니버스에서 prim을 생성하는 유틸리티를 포함하는 하위 모듈입니다.

스포너는 옴니버스 시뮬레이터에 prim을 생성하는 데 사용됩니다. 핵심적으로는 USD 파이썬 API 또는 옴니버스 키트 커맨드를 호출하여 prim을 생성합니다. 하지만 편의성을 위해 해당 클래스들의 설정 클래스에서 prim을 생성할 수 있는 인터페이스도 제공합니다.

스포너를 사용하는 방법은 주로 두 가지입니다.

1. 모듈의 함수 사용
   ```python
   import isaaclab.sim as sim_utils
   from isaaclab.utils.assets import ISAACLAB_NUCLEUS_DIR

   # USD 파일에서 스폰
   cfg = sim_utils.UsdFileCfg(usd_path=f"{ISAACLAB_NUCLEUS_DIR}/Robots/FrankaEmika/panda_instanceable.usd")
   prim_path = "/World/myAsset"

   # 모듈의 함수로 스폰
   sim_utils.spawn_from_usd(prim_path, cfg)
   ```
2. 설정 클래스의 func 참조 사용
   ```python
   import isaaclab.sim as sim_utils
   from isaaclab.utils.assets import ISAACLAB_NUCLEUS_DIR

   # USD 파일에서 스폰
   cfg = sim_utils.UsdFileCfg(usd_path=f"{ISAACLAB_NUCLEUS_DIR}/Robots/FrankaEmika/panda_instanceable.usd")
   prim_path = "/World/myAsset"

   # 설정 클래스의 `func` 참조 사용
   cfg.func(prim_path, cfg)
   ```

편의를 위해 두 번째 접근 방식을 권장합니다. 이렇게 하면 설정 클래스와 함수 호출을 한 줄의 코드로 쉽게 변경할 수 있습니다.

prim의 유형에 따라 스포닝 함수는 여러 prim 경로에 걸쳐 prim 생성을 처리할 수도 있습니다. 이러한 경우는 부모 prim 경로를 기반으로 정규 표현식 형태의 prim 경로로 제공되어야 하며, `isaaclab.sim.utils.clone()` 함수 데코레이터를 사용하여 해결됩니다. 예를 들어:

* `/World/Table_[1,2]/Robot`은 부모 prim `/World/Table_1`과 `/World/Table_2`가 존재할 때만 `/World/Table_1/Robot`과 `/World/Table_2/Robot` prim을 생성합니다.
* `/World/Robot_[1,2]`는 prim 경로 표현식이 여러 prim으로 해석될 수 있으므로 `/World/Robot_1`과 `/World/Robot_2` prim을 **생성하지 않습니다**.

### 하위 모듈

| [`shapes`](#module-isaaclab.sim.spawners.shapes)         | 시뮬레이션에서 기본 도형을 스폰하는 하위 모듈.             |
|----------------------------------------------------------|-------------------------------------------------------------------------|
| [`meshes`](#module-isaaclab.sim.spawners.meshes)         | 시뮬레이션에서 메시를 스폰하는 하위 모듈.                       |
| [`lights`](#module-isaaclab.sim.spawners.lights)         | 시뮬레이션에서 조명을 스폰하는 스포너를 포함하는 하위 모듈.            |
| [`sensors`](#module-isaaclab.sim.spawners.sensors)       | 시뮬레이션에서 센서를 스폰하는 스포너를 포함하는 하위 모듈.           |
| [`from_files`](#module-isaaclab.sim.spawners.from_files) | 파일에서 에셋을 스폰하는 스포너를 포함하는 하위 모듈.                   |
| [`materials`](#module-isaaclab.sim.spawners.materials)   | USD 기반 및 PhysX 기반 재질을 스폰하는 스포너를 포함하는 하위 모듈.   |
| [`wrappers`](#module-isaaclab.sim.spawners.wrappers)     | 스포너 구성을 래핑하는 하위 모듈.                         |

### 클래스

| [`SpawnerCfg`](#isaaclab.sim.spawners.SpawnerCfg)                                 | 에셋을 스폰하기 위한 구성 매개변수.           |
|-----------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`RigidObjectSpawnerCfg`](#isaaclab.sim.spawners.RigidObjectSpawnerCfg)           | 강체 에셋을 스폰하기 위한 구성 매개변수.      |
| [`DeformableObjectSpawnerCfg`](#isaaclab.sim.spawners.DeformableObjectSpawnerCfg) | 변형 가능한 에셋을 스폰하기 위한 구성 매개변수. |

## 스포너

### *class* isaaclab.sim.spawners.SpawnerCfg

에셋을 스폰하기 위한 구성 매개변수.

에셋 스폰은 [`func`](#isaaclab.sim.spawners.SpawnerCfg.func) 함수를 호출하여 수행됩니다. 이 함수는 에셋을 스폰할 prim 경로, 구성 인스턴스 및 변환을 입력으로 받아 스폰된 에셋의 prim 경로를 반환합니다.

이 함수는 보통 `isaaclab.sim.spawner.utils.clone()` 데코레이터로 장식되며, 입력 prim 경로가 정규 표현식인지 확인하고 일치하는 모든 prim에 에셋을 스폰합니다. 이 데코레이터는 Isaac Sim의 Cloner API를 사용하여 [`copy_from_source`](#isaaclab.sim.spawners.SpawnerCfg.copy_from_source) 매개변수를 처리합니다.

**속성:**

| [`func`](#isaaclab.sim.spawners.SpawnerCfg.func)                         | 에셋을 스폰하는 데 사용할 함수.                       |
|--------------------------------------------------------------------------|---------------------------------------------------------------|
| [`visible`](#isaaclab.sim.spawners.SpawnerCfg.visible)                   | 스폰된 에셋이 가시적인지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.SpawnerCfg.semantic_tags)       | 스폰된 에셋에 추가할 의미 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.SpawnerCfg.copy_from_source) | 소스 prim에서 에셋을 복사할지 여부 또는 상속할지 여부. |

#### func *: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)[[...], pxr.Usd.Prim]*

에셋을 스폰하는 데 사용할 함수.

이 함수는 에셋을 스폰할 prim 경로(또는 표현식), 구성 인스턴스 및 변환을 입력으로 받아 스폰된 소스 prim을 반환합니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

스폰된 에셋이 가시적인지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

스폰된 에셋에 추가할 의미 태그 목록. 기본값은 None으로, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형식의 튜플로, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 의미 레이블입니다. 예를 들어, 아보카도 클래스에 스폰된 에셋에 주석을 달려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 녹색 색상에 스폰된 에셋에 주석을 달려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### 참고
의미 필터에 대한 자세한 정보는 [의미 스키마 편집기 문서](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering)를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 prim에서 에셋을 복사할지 여부 또는 상속할지 여부. 기본값은 True입니다.

이 매개변수는 프라임 클로닝 시에만 사용됩니다. False이면 에셋이 소스 프라임에서 상속되며, 즉 소스 프라임에 대한 모든 USD 변경 사항이 복제된 프라임에 반영됩니다.

### *class* isaaclab.sim.spawners.RigidObjectSpawnerCfg

Bases: [`SpawnerCfg`](#isaaclab.sim.spawners.SpawnerCfg)

강체 에셋을 스폰하기 위한 구성 매개변수.

#### 참고
기본적으로 모든 속성이 None으로 설정됩니다. 이는 강체 에셋을 스폰할 때 기본적으로 제공되는 속성 외에는 prim에 속성을 추가하거나 수정하지 않음을 의미합니다.

**속성:**

| [`mass_props`](#isaaclab.sim.spawners.RigidObjectSpawnerCfg.mass_props)                             | 질량 속성.                                              |
|-----------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`func`](#isaaclab.sim.spawners.RigidObjectSpawnerCfg.func)                                         | 에셋을 스폰하는 데 사용할 함수.                       |
| [`visible`](#isaaclab.sim.spawners.RigidObjectSpawnerCfg.visible)                                   | 스폰된 에셋이 가시적인지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.RigidObjectSpawnerCfg.semantic_tags)                       | 스폰된 에셋에 추가할 의미 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.RigidObjectSpawnerCfg.copy_from_source)                 | 소스 프라임에서 에셋을 복사할지 여부 또는 상속할지 여부. |
| [`rigid_props`](#isaaclab.sim.spawners.RigidObjectSpawnerCfg.rigid_props)                           | 강체 속성.                                        |
| [`collision_props`](#isaaclab.sim.spawners.RigidObjectSpawnerCfg.collision_props)                   | 모든 충돌 메시에 적용할 속성.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.RigidObjectSpawnerCfg.activate_contact_sensors) | 모든 강체에 대한 접촉 보고서 활성화.               |

#### mass_props *: [MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### func *: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)[[...], pxr.Usd.Prim]*

에셋을 스폰하는 데 사용할 함수.

이 함수는 에셋을 스폰할 prim 경로(또는 표현식), 구성 인스턴스 및 변환을 입력으로 받아 스폰된 소스 prim을 반환합니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

스폰된 에셋이 가시적인지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 자산에 추가할 의미 태그 목록입니다. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 의미 레이블입니다. 예를 들어, 아보카도 클래스의 생성된 자산에 주석을 추가하려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그는 태그 목록을 전달하여 지정할 수 있습니다. 예를 들어, 아보카도 클래스와 초록색의 생성된 자산에 주석을 추가하려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 자산을 복사할지 아니면 상속할지 여부입니다. 기본값은 True입니다.

이 매개변수는 프림을 클론할 때만 사용됩니다. False인 경우, 자산은 소스 프림에서 상속됩니다. 즉, 소스 프림의 모든 USD 변경 사항이 클론된 프림에 반영됩니다.

#### rigid_props *: [RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강성 몸체 속성.

강성 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다. 이는 객체를 정적으로 만들고 중력이나 다른 힘의 영향을 받지 않게 합니다.

#### collision_props *: [CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강성 몸체에 접촉 보고를 활성화합니다. 기본값은 False입니다.

이 설정은 주어진 프림 경로와 그 하위 객체의 모든 강성 몸체에 PhysxContactReporter API를 추가합니다.

### *class* isaaclab.sim.spawners.DeformableObjectSpawnerCfg

Bases: [`SpawnerCfg`](#isaaclab.sim.spawners.SpawnerCfg)

변형 가능한 자산을 생성하기 위한 구성 매개변수입니다.

강성 객체와 달리, 변형 가능한 객체는 힘의 영향을 받고 외부 힘이 가해질 때 변형될 수 있습니다. 이 클래스는 변형 가능한 객체의 속성을 구성하는 데 사용됩니다.

변형 가능한 몸체는 별도의 충돌 메시가 없습니다. 충돌 메시는 시각 메시와 동일합니다. 휴식 및 충돌 오프셋과 같은 충돌 속성은 [`deformable_props`](#isaaclab.sim.spawners.DeformableObjectSpawnerCfg.deformable_props)에서 지정됩니다.

#### NOTE
기본적으로 모든 속성이 None으로 설정됩니다. 이는 프림을 생성할 때 기본적으로 제공되는 속성 외에는 프림 외부에 속성을 추가하거나 수정하지 않음을 의미합니다.

**속성:**

| [`func`](#isaaclab.sim.spawners.DeformableObjectSpawnerCfg.func)                         | 자산을 생성하는 데 사용할 함수.                       |
|------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`visible`](#isaaclab.sim.spawners.DeformableObjectSpawnerCfg.visible)                   | 생성된 자산이 보이는지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.DeformableObjectSpawnerCfg.semantic_tags)       | 생성된 자산에 추가할 의미 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.DeformableObjectSpawnerCfg.copy_from_source) | 소스 프림에서 자산을 복사할지 아니면 상속할지 여부. |
| [`mass_props`](#isaaclab.sim.spawners.DeformableObjectSpawnerCfg.mass_props)             | 질량 속성.                                              |
| [`deformable_props`](#isaaclab.sim.spawners.DeformableObjectSpawnerCfg.deformable_props) | 변형 가능한 몸체 속성.                                   |

#### func *: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)[[...], pxr.Usd.Prim]*

자산을 생성하는 데 사용할 함수입니다.

이 함수는 자산을 생성할 프림 경로(또는 표현), 구성 인스턴스 및 변환을 입력으로 받아 생성된 소스 프림을 반환합니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 자산이 보이는지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 자산에 추가할 의미 태그 목록입니다. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 의미 레이블입니다. 예를 들어, 아보카도 클래스의 생성된 자산에 주석을 추가하려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그는 태그 목록을 전달하여 지정할 수 있습니다. 예를 들어, 아보카도 클래스와 초록색의 생성된 자산에 주석을 추가하려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 자산을 복사할지 아니면 상속할지 여부입니다. 기본값은 True입니다.

이 매개변수는 프림을 클론할 때만 사용됩니다. False인 경우, 자산은 소스 프림에서 상속됩니다. 즉, 소스 프림의 모든 USD 변경 사항이 클론된 프림에 반영됩니다.

#### mass_props *: [MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### deformable_props *: [DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 몸체 속성.

## Shapes

시뮬레이션에서 프리미티브 형태를 생성하기 위한 서브 모듈입니다.

NVIDIA Omniverse는 USDGeom 프림을 생성하는 데 사용할 수 있는 다양한 프리미티브 형태를 제공합니다. 구성에 따라 생성된 프림은 다음과 같이 될 수 있습니다:

* 시각 메시(물리 없음)
* 정적 충돌체(강성 몸체 없음)
* 강성 몸체(충돌 및 강성 몸체 속성 포함)

### 클래스

| [`ShapeCfg`](#isaaclab.sim.spawners.shapes.ShapeCfg)       | USD 기하학 또는 기호 프림을 위한 구성 매개변수.   |
|------------------------------------------------------------|-------------------------------------------------------------|
| [`CapsuleCfg`](#isaaclab.sim.spawners.shapes.CapsuleCfg)   | 캡슐 프림을 위한 구성 매개변수.                |
| [`ConeCfg`](#isaaclab.sim.spawners.shapes.ConeCfg)         | 원뿔 프림을 위한 구성 매개변수.                   |
| [`CuboidCfg`](#isaaclab.sim.spawners.shapes.CuboidCfg)     | 직육면체 프림을 위한 구성 매개변수.                 |
| [`CylinderCfg`](#isaaclab.sim.spawners.shapes.CylinderCfg) | 원기둥 프림을 위한 구성 매개변수.               |
| [`SphereCfg`](#isaaclab.sim.spawners.shapes.SphereCfg)     | 구 프림을 위한 구성 매개변수.                 |

### *class* isaaclab.sim.spawners.shapes.ShapeCfg

USD 기하학 또는 기호 프림을 위한 구성 매개변수입니다.

**속성:**

| [`visual_material_path`](#isaaclab.sim.spawners.shapes.ShapeCfg.visual_material_path)         | 프림에 사용할 시각 재료의 경로.              |
|-----------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`visual_material`](#isaaclab.sim.spawners.shapes.ShapeCfg.visual_material)                   | 시각 재료 속성.                                   |
| [`physics_material_path`](#isaaclab.sim.spawners.shapes.ShapeCfg.physics_material_path)       | 프림에 사용할 물리 재료의 경로.       |
| [`physics_material`](#isaaclab.sim.spawners.shapes.ShapeCfg.physics_material)                 | 물리 재료 속성.                                  |
| [`visible`](#isaaclab.sim.spawners.shapes.ShapeCfg.visible)                                   | 생성된 자산이 보이는지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.shapes.ShapeCfg.semantic_tags)                       | 생성된 자산에 추가할 의미 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.shapes.ShapeCfg.copy_from_source)                 | 소스 프림에서 자산을 복사할지 아니면 상속할지 여부. |
| [`mass_props`](#isaaclab.sim.spawners.shapes.ShapeCfg.mass_props)                             | 질량 속성.                                              |
| [`rigid_props`](#isaaclab.sim.spawners.shapes.ShapeCfg.rigid_props)                           | 강성 몸체 속성.                                        |
| [`collision_props`](#isaaclab.sim.spawners.shapes.ShapeCfg.collision_props)                   | 모든 충돌 메시지에 적용할 속성입니다.                         |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.shapes.ShapeCfg.activate_contact_sensors) | 모든 강성 몸에 대해 접촉 보고를 활성화합니다.                 |

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프림에 사용할 시각 재질의 경로입니다. 기본값은 “material”입니다.

경로가 상대 경로인 경우, 프림의 경로를 기준으로 해석됩니다.
visual_material이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### visual_material *: [VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각 재질 속성.

#### NOTE
None인 경우 시각 재질이 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프림에 사용할 물리 재질의 경로입니다. 기본값은 “material”입니다.

경로가 상대 경로인 경우, 프림의 경로를 기준으로 해석됩니다.
physics_material이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### physics_material *: [PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리 재질 속성.

#### NOTE
None인 경우 물리 재질이 추가되지 않습니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 표시되는지 여부를 나타냅니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 의미 태그 목록입니다. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 종류이고 `data`는 태그와 연관된 의미 라벨입니다. 예를 들어, 생성된 에셋을 아보카도 클래스로 주석 처리하려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그는 태그 목록을 전달하여 지정할 수 있습니다. 예를 들어, 생성된 에셋을 아보카도 클래스와 초록색으로 주석 처리하려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 에셋을 복사할지 여부를 나타냅니다. 기본값은 True입니다.

이 매개변수는 프림을 복제할 때만 사용됩니다. False인 경우, 에셋은 소스 프림에서 상속되며, 즉 소스 프림의 모든 USD 변경 사항이 복제된 프림에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강성 몸체 속성.

강성 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다. 이렇게 하면 객체가 정적이 되고 중력이나 다른 힘의 영향을 받지 않습니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시지에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강성 몸에 대해 접촉 보고를 활성화합니다. 기본값은 False입니다.

이 옵션은 지정된 프림 경로와 그 하위 경로의 모든 강성 몸에 PhysxContactReporter API를 추가합니다.

### isaaclab.sim.spawners.shapes.spawn_capsule(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [shapes_cfg.CapsuleCfg](#isaaclab.sim.spawners.shapes.CapsuleCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 속성을 기반으로 USDGeom 기반 캡슐 프림을 생성합니다.

자세한 내용은 [USDGeomCapsule](https://openusd.org/dev/api/class_usd_geom_capsule.html)를 참조하십시오.

#### NOTE
이 함수는 `clone()` 데코레이터로 장식되어 있으며, 입력 프림 경로가 정규 표현식 패턴인 경우 프림 경로를 경로 목록으로 해석합니다. 이는 단일 입력에서 여러 에셋을 생성하고 주어진 경로 표현식의 USD 프림을 복제하는 것을 지원하기 위함입니다.

* **매개변수:**
  * **prim_path** – 에셋을 생성할 프림 경로 또는 패턴입니다. 프림 경로가 정규 표현식 패턴인 경우, 일치하는 모든 프림 경로에 에셋이 생성됩니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 프림을 기준으로 프림에 적용할 변환입니다. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 프림을 기준으로 프림에 적용할 (w, x, y, z) 형태의 방향입니다. 기본값은 None이며, 이 경우 단위 행렬로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수입니다.
* **반환값:**
  생성된 프림입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로에 프림이 이미 존재하는 경우 발생합니다.

### *class* isaaclab.sim.spawners.shapes.CapsuleCfg

Bases: [`ShapeCfg`](#isaaclab.sim.spawners.shapes.ShapeCfg)

캡슐 프림에 대한 구성 매개변수입니다.

자세한 내용은 [`spawn_capsule()`](#isaaclab.sim.spawners.shapes.spawn_capsule)를 참조하십시오.

**속성:**

| [`visible`](#isaaclab.sim.spawners.shapes.CapsuleCfg.visible)                                   | 생성된 에셋이 표시되는지 여부입니다.                          |
|-------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`semantic_tags`](#isaaclab.sim.spawners.shapes.CapsuleCfg.semantic_tags)                       | 생성된 에셋에 추가할 의미 태그 목록입니다.                    |
| [`copy_from_source`](#isaaclab.sim.spawners.shapes.CapsuleCfg.copy_from_source)                 | 소스 프림에서 에셋을 복사할지 여부입니다.                     |
| [`mass_props`](#isaaclab.sim.spawners.shapes.CapsuleCfg.mass_props)                             | 질량 속성입니다.                                              |
| [`rigid_props`](#isaaclab.sim.spawners.shapes.CapsuleCfg.rigid_props)                           | 강성 몸체 속성입니다.                                         |
| [`collision_props`](#isaaclab.sim.spawners.shapes.CapsuleCfg.collision_props)                   | 모든 충돌 메시지에 적용할 속성입니다.                         |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.shapes.CapsuleCfg.activate_contact_sensors) | 모든 강성 몸에 대해 접촉 보고를 활성화합니다.                 |
| [`visual_material_path`](#isaaclab.sim.spawners.shapes.CapsuleCfg.visual_material_path)         | 프림에 사용할 시각 재질의 경로입니다.                         |
| [`visual_material`](#isaaclab.sim.spawners.shapes.CapsuleCfg.visual_material)                   | 시각 재질 속성입니다.                                         |
| [`physics_material_path`](#isaaclab.sim.spawners.shapes.CapsuleCfg.physics_material_path)       | 프림에 사용할 물리 재질의 경로입니다.                         |
| [`physics_material`](#isaaclab.sim.spawners.shapes.CapsuleCfg.physics_material)                 | 물리 재질 속성입니다.                                         |
| [`radius`](#isaaclab.sim.spawners.shapes.CapsuleCfg.radius)                                     | 캡슐의 반지름 (미터 단위).                                    |
| [`height`](#isaaclab.sim.spawners.shapes.CapsuleCfg.height)                                     | 캡슐의 높이 (미터 단위).                                      |
| [`axis`](#isaaclab.sim.spawners.shapes.CapsuleCfg.axis)                                         | 캡슐의 축입니다.                                              |

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 표시되는지 여부를 나타냅니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 의미 태그 목록입니다. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 종류이고 `data`는 태그와 연관된 의미 라벨입니다. 예를 들어, 생성된 에셋을 아보카도 클래스로 주석 처리하려면 의미 태그는 `[("class", "avocado")]`가 됩니다.
와 관련된 태그입니다. 예를 들어, 클래스 avocado에서 생성된 에셋을 주석 처리하려면 의미 있는 태그는 `[("class", "avocado")]`가 됩니다.

여러 개의 의미 있는 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 클래스 avocado와 색상 green에서 생성된 에셋을 주석 처리하려면 의미 있는 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 에셋을 복사할지 아니면 상속할지 여부를 나타냅니다. 기본값은 True입니다.

이 파라미터는 프림을 클론할 때만 사용됩니다. False인 경우, 소스 프림에서 에셋을 상속받게 되며, 즉 소스 프림의 모든 USD 변경 사항이 클론된 프림에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리적 속성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

 rigid body 속성.

 rigid한 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정하십시오. 이렇게 하면 객체가 정적이 되고 중력이나 다른 힘의 영향을 받지 않게 됩니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시지에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 rigid body에 대한 접촉 보고를 활성화합니다. 기본값은 False입니다.

지정된 프림 경로 및 그 자식의 모든 rigid body에 PhysxContactReporter API를 추가합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 시각적 재질의 경로입니다. 기본값은 “material”입니다.

경로가 상대적인 경우, 해당 경도는 프림의 경로를 기준으로 합니다. visual_material이 None이 아닌 경우 이 파라미터는 무시됩니다.

#### visual_material *: [VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 재질 속성.

#### NOTE
None인 경우 시각적 재질이 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 물리 재질의 경로입니다. 기본값은 “material”입니다.

경로가 상대적인 경우, 해당 경도는 프림의 경로를 기준으로 합니다. physics_material이 None이 아닌 경우 이 파라미터는 무시됩니다.

#### physics_material *: [PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리 재질 속성.

#### NOTE
None인 경우 물리 재질이 추가되지 않습니다.

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

캡슐의 반지름 (미터 단위).

#### height *: [float](https://docs.python.org/3/library/functions.html#float)*

캡슐의 높이 (미터 단위).

#### axis *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['X', 'Y', 'Z']*

캡슐의 축. 기본값은 “Z”입니다.

### isaaclab.sim.spawners.shapes.spawn_cone(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [shapes_cfg.ConeCfg](#isaaclab.sim.spawners.shapes.ConeCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 속성을 기반으로 USDGeom 기반 콘 프림을 생성합니다.

자세한 내용은 [USDGeomCone](https://openusd.org/dev/api/class_usd_geom_cone.html)를 참조하십시오.

#### NOTE
이 함수는 `clone()` 데코레이터로 장식되어 입력 프림 경로가 정규 표현식 패턴인 경우 경로를 경로 목록으로 해석합니다. 이를 통해 단일 경로 표현식에서 여러 에셋을 생성하고 해당 경로 표현식의 USD 프림을 클론할 수 있습니다.

* **매개변수:**
  * **prim_path** – 에셋을 생성할 프림 경로 또는 패턴입니다. 프림 경로가 정규 표현식 패턴인 경우, 일치하는 모든 프림 경로에 에셋이 생성됩니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 프림을 기준으로 프림에 적용할 변환입니다. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 프림을 기준으로 프림에 적용할 방향 (w, x, y, z)입니다. 기본값은 None이며, 이 경우 단위 행렬로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수입니다.
* **반환값:**
  생성된 프림입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로에 프림이 이미 존재하는 경우.

### *class* isaaclab.sim.spawners.shapes.ConeCfg

Bases: [`ShapeCfg`](#isaaclab.sim.spawners.shapes.ShapeCfg)

콘 프림의 구성 매개변수입니다.

자세한 내용은 [`spawn_cone()`](#isaaclab.sim.spawners.shapes.spawn_cone)를 참조하십시오.

**속성:**

| [`visible`](#isaaclab.sim.spawners.shapes.ConeCfg.visible)                                   | 생성된 에셋이 표시되어야 하는지 여부입니다.                  |
|----------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`semantic_tags`](#isaaclab.sim.spawners.shapes.ConeCfg.semantic_tags)                       | 생성된 에셋에 추가할 의미 있는 태그 목록입니다.            |
| [`copy_from_source`](#isaaclab.sim.spawners.shapes.ConeCfg.copy_from_source)                 | 소스 프림에서 에셋을 복사할지 아니면 상속할지 여부를 나타냅니다. |
| [`mass_props`](#isaaclab.sim.spawners.shapes.ConeCfg.mass_props)                             | 물적 속성입니다.                                              |
| [`rigid_props`](#isaaclab.sim.spawners.shapes.ConeCfg.rigid_props)                           | rigid body 속성입니다.                                        |
| [`collision_props`](#isaaclab.sim.spawners.shapes.ConeCfg.collision_props)                   | 모든 충돌 메시지에 적용할 속성입니다.                         |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.shapes.ConeCfg.activate_contact_sensors) | 모든 rigid body에 대한 접촉 보고를 활성화합니다.               |
| [`visual_material_path`](#isaaclab.sim.spawners.shapes.ConeCfg.visual_material_path)         | 프림에 사용할 시각적 재질의 경로입니다.                       |
| [`visual_material`](#isaaclab.sim.spawners.shapes.ConeCfg.visual_material)                   | 시각적 재질 속성입니다.                                       |
| [`physics_material_path`](#isaaclab.sim.spawners.shapes.ConeCfg.physics_material_path)       | 프림에 사용할 물리 재질의 경로입니다.                         |
| [`physics_material`](#isaaclab.sim.spawners.shapes.ConeCfg.physics_material)                 | 물리 재질 속성입니다.                                         |
| [`radius`](#isaaclab.sim.spawners.shapes.ConeCfg.radius)                                     | 콘의 반지름 (미터 단위).                                    |
| [`height`](#isaaclab.sim.spawners.shapes.ConeCfg.height)                                     | 콘의 높이 (미터 단위).                                       |
| [`axis`](#isaaclab.sim.spawners.shapes.ConeCfg.axis)                                         | 콘의 축.                                                     |

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 표시되어야 하는지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 의미 있는 태그 목록입니다. 기본값은 None이며, 이는 의미 있는 태그가 추가되지 않음을 의미합니다.

의미 있는 태그는 Replicator 의미 있는 태그 지정 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형을 나타내고 `data`는 태그와 관련된 의미 있는 레이블을 나타냅니다. 예를 들어, 클래스 avocado에서 생성된 에셋을 주석 처리하려면 의미 있는 태그는 `[("class", "avocado")]`가 됩니다.

여러 개의 의미 있는 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 클래스 avocado와 색상 green에서 생성된 에셋을 주석 처리하려면 의미 있는 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 에셋을 복사할지 아니면 상속할지 여부를 나타냅니다. 기본값은 True입니다.

이 파라미터는 프림을 클론할 때만 사용됩니다. False인 경우, 소스 프림에서 에셋을 상속받게 되며, 즉 소스 프림의 모든 USD 변경 사항이 클론된 프림에 반영됩니다.
source prim, 즉 source prim의 모든 USD 변경 사항이 복제된 prim에 반영됨을 의미합니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

 rigid body 속성.

 rigid 물체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다. 이로 인해 물체가 정적 상태가 되고 중력 또는 기타 힘의 영향을 받지 않게 됩니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시에 적용할 속성.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 rigid body에서 접촉 보고를 활성화합니다. 기본값은 False입니다.

이 옵션은 주어진 prim 경로 및 모든 하위 prim의 모든 rigid body에 PhysxContactReporter API를 추가합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 시각적 재질의 경로. 기본값은 “material”입니다.

경로가 상대 경로인 경우, prim의 경로를 기준으로 해석됩니다.
visual_material이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### visual_material *: [VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 재질 속성.

#### 참고 사항
None인 경우 시각적 재질이 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 물리 재질의 경로. 기본값은 “material”입니다.

경로가 상대 경로인 경우, prim의 경로를 기준으로 해석됩니다.
physics_material이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### physics_material *: [PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리 재질 속성.

#### 참고 사항
None인 경우 물리 재질이 추가되지 않습니다.

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

원의 반지름(단위: m).

#### height *: [float](https://docs.python.org/3/library/functions.html#float)*

높이(단위: m).

#### axis *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['X', 'Y', 'Z']*

원의 축. 기본값은 “Z”입니다.

### isaaclab.sim.spawners.shapes.spawn_cuboid(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [shapes_cfg.CuboidCfg](#isaaclab.sim.spawners.shapes.CuboidCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 속성을 기반으로 USDGeom 기반의 큐보이드 prim을 생성합니다.

자세한 내용은 [USDGeomCube](https://openusd.org/dev/api/class_usd_geom_cube.html)를 참조하세요.

#### 참고 사항
USD는 큐브만 지원하므로, 주어진 크기의 최소값을 큐브의 크기로 설정한 후 해당 방향으로 큐브를 스케일링합니다.

#### 참고 사항
이 함수는 `clone()` 데코레이터로 수정되어, 입력 prim 경로가 정규 표현식 패턴인 경우 해당 경로를 경로 목록으로 해석합니다. 이는 단일 경로 표현식에서 여러 애셋을 생성하고, 지정된 경로 표현식의 USD prim을 복제하는 기능을 지원하기 위함입니다.

* **매개변수:**
  * **prim_path** – 애셋을 생성할 prim 경로 또는 패턴. prim 경로가 정규 표현식 패턴인 경우, 일치하는 모든 prim 경로에 애셋이 생성됩니다.
  * **cfg** – 구성 인스턴스.
  * **translation** – 부모 prim을 기준으로 적용할 이동 벡터. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 prim을 기준으로 적용할 회전 (w, x, y, z). 기본값은 None이며, 이 경우 항등 행렬로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수.
* **반환값:**
  생성된 prim.
* **예외:**
  **주어진 경로에 이미 prim이 존재하는 경우.** – 

### *class* isaaclab.sim.spawners.shapes.CuboidCfg

Bases: [`ShapeCfg`](#isaaclab.sim.spawners.shapes.ShapeCfg)

큐보이드 prim에 대한 구성 매개변수.

자세한 내용은 [`spawn_cuboid()`](#isaaclab.sim.spawners.shapes.spawn_cuboid)를 참조하세요.

**속성:**

| [`size`](#isaaclab.sim.spawners.shapes.CuboidCfg.size)                                         | 큐보이드의 크기.                                           |
|------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`visible`](#isaaclab.sim.spawners.shapes.CuboidCfg.visible)                                   | 생성된 애셋이 보이는지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.shapes.CuboidCfg.semantic_tags)                       | 생성된 애셋에 추가할 의미 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.shapes.CuboidCfg.copy_from_source)                 | source prim에서 애셋을 복사할지 여부 또는 상속할지 여부. |
| [`mass_props`](#isaaclab.sim.spawners.shapes.CuboidCfg.mass_props)                             | 질량 속성.                                              |
| [`rigid_props`](#isaaclab.sim.spawners.shapes.CuboidCfg.rigid_props)                           | rigid body 속성.                                        |
| [`collision_props`](#isaaclab.sim.spawners.shapes.CuboidCfg.collision_props)                   | 모든 충돌 메시에 적용할 속성.                   |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.shapes.CuboidCfg.activate_contact_sensors) | 모든 rigid body에서 접촉 보고를 활성화합니다.               |
| [`visual_material_path`](#isaaclab.sim.spawners.shapes.CuboidCfg.visual_material_path)         | prim에 사용할 시각적 재질의 경로.              |
| [`visual_material`](#isaaclab.sim.spawners.shapes.CuboidCfg.visual_material)                   | 시각적 재질 속성.                                   |
| [`physics_material_path`](#isaaclab.sim.spawners.shapes.CuboidCfg.physics_material_path)       | prim에 사용할 물리 재질의 경로.                 |
| [`physics_material`](#isaaclab.sim.spawners.shapes.CuboidCfg.physics_material)                 | 물리 재질 속성.                                  |

#### size *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

큐보이드의 크기.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 애셋이 보이는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 애셋에 추가할 의미 태그 목록. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태그 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 종류이고 `data`는 해당 태그와 연결된 의미 라벨입니다. 예를 들어, 생성된 애셋을 아보카도 클래스로 주석 처리하려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 생성된 애셋을 아보카도 클래스와 초록 색으로 주석 처리하려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### 참고 사항
의미 필터에 대한 자세한 내용은 [meaning 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

source prim에서 애셋을 복사할지 여부 또는 상속할지 여부. 기본값은 True입니다.

이 매개변수는 prim을 복제할 때만 사용됩니다. False인 경우, 애셋은 source prim에서 상속되며, 즉 source prim의 모든 USD 변경 사항이 복제된 prim에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

rigid body 속성.

 rigid 물체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다. 이로 인해 물체가 정적 상태가 되고 중력 또는 기타 힘의 영향을 받지 않게 됩니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시지에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 대한 접촉 보고를 활성화합니다. 기본값은 False입니다.

이 옵션은 주어진 prim 경로와 그 하위에 있는 모든 강체에 PhysxContactReporter API를 추가합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 시각적 재료의 경로입니다. 기본값은 “material”입니다.

경로가 상대 경로인 경우, prim의 경로를 기준으로 해석됩니다.
visual_material이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### visual_material *: [VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 재료 속성입니다.

#### NOTE
None인 경우 시각적 재료가 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 물리적 재료의 경로입니다. 기본값은 “material”입니다.

경로가 상대 경로인 경우, prim의 경로를 기준으로 해석됩니다.
physics_material이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### physics_material *: [PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리적 재료 속성입니다.

#### NOTE
None인 경우 물리적 재료가 추가되지 않습니다.

### isaaclab.sim.spawners.shapes.spawn_cylinder(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [shapes_cfg.CylinderCfg](#isaaclab.sim.spawners.shapes.CylinderCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 속성을 가진 USDGeom 기반의 실린더 prim을 생성합니다.

자세한 내용은 [USDGeomCylinder](https://openusd.org/dev/api/class_usd_geom_cylinder.html)를 참조하세요.

#### NOTE
이 함수는 `clone()` 데코레이터로 장식되어 있으며, 입력된 prim 경로가 정규식 패턴인 경우 경로 목록으로 해석됩니다. 이는 단일 표현식으로 여러 에셋을 생성하고 지정된 경로 표현식에 있는 USD prim을 복제하여 여러 에셋 생성을 지원하기 위함입니다.

* **매개변수:**
  * **prim_path** – 에셋을 생성할 prim 경로 또는 패턴입니다. prim 경로가 정규식 패턴이면, 일치하는 모든 prim 경로에 에셋이 생성됩니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 prim을 기준으로 적용할 평행 이동입니다. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 prim을 기준으로 적용할 방향 (w, x, y, z)입니다. 기본값은 None이며, 이 경우 단위 쿼터니언으로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수입니다.
* **반환값:**
  생성된 prim입니다.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 주어진 경로에 prim이 이미 존재하는 경우.

### *class* isaaclab.sim.spawners.shapes.CylinderCfg

Bases: [`ShapeCfg`](#isaaclab.sim.spawners.shapes.ShapeCfg)

실린더 prim의 구성 파라미터입니다.

자세한 내용은 [`spawn_cylinder()`](#isaaclab.sim.spawners.shapes.spawn_cylinder)를 참조하세요.

**속성:**

| [`radius`](#isaaclab.sim.spawners.shapes.CylinderCfg.radius)                                     | 실린더의 반지름 (m 단위).                                |
|--------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`height`](#isaaclab.sim.spawners.shapes.CylinderCfg.height)                                     | 실린더의 높이 (m 단위).                                |
| [`axis`](#isaaclab.sim.spawners.shapes.CylinderCfg.axis)                                         | 실린더의 축.                                         |
| [`visible`](#isaaclab.sim.spawners.shapes.CylinderCfg.visible)                                   | 생성된 에셋이 보이는지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.shapes.CylinderCfg.semantic_tags)                       | 생성된 에셋에 추가할 시맨틱 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.shapes.CylinderCfg.copy_from_source)                 | 소스 prim에서 에셋을 복사할지 여부 또는 상속할지 여부. |
| [`mass_props`](#isaaclab.sim.spawners.shapes.CylinderCfg.mass_props)                             | 질량 속성.                                              |
| [`rigid_props`](#isaaclab.sim.spawners.shapes.CylinderCfg.rigid_props)                           | 강체 속성.                                        |
| [`collision_props`](#isaaclab.sim.spawners.shapes.CylinderCfg.collision_props)                   | 모든 충돌 메시에 적용할 속성.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.shapes.CylinderCfg.activate_contact_sensors) | 모든 강체에 대한 접촉 보고 활성화.               |
| [`visual_material_path`](#isaaclab.sim.spawners.shapes.CylinderCfg.visual_material_path)         | prim에 사용할 시각적 재료의 경로.              |
| [`visual_material`](#isaaclab.sim.spawners.shapes.CylinderCfg.visual_material)                   | 시각적 재료 속성.                                   |
| [`physics_material_path`](#isaaclab.sim.spawners.shapes.CylinderCfg.physics_material_path)       | prim에 사용할 물리적 재료의 경로.             |
| [`physics_material`](#isaaclab.sim.spawners.shapes.CylinderCfg.physics_material)                 | 물리적 재료 속성.                                  |

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

실린더의 반지름 (m 단위).

#### height *: [float](https://docs.python.org/3/library/functions.html#float)*

실린더의 높이 (m 단위).

#### axis *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['X', 'Y', 'Z']*

실린더의 축. 기본값은 “Z”입니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 보이는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 시맨틱 태그 목록입니다. 기본값은 None이며,この場合 시맨틱 태그가 추가되지 않습니다.

시맨틱 태그는 Replicator 시맨틱 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 시맨틱 레이블입니다. 예를 들어, 생성된 에셋을 아보카도 클래스로 주석 처리하려면 시맨틱 태그는 `[("class", "avocado")]`가 됩니다.

여러 개의 시맨틱 태그를 지정하려면 태그 리스트를 전달하면 됩니다. 예를 들어, 아보카도 클래스와 녹색 색상을 가진 에셋에 주석을 달려면 시맨틱 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 prim에서 에셋을 복사할지 여부 또는 상속할지 여부. 기본값은 True입니다.

이 매개변수는 prim을 클론할 때만 사용됩니다. False인 경우, 에셋은 소스 prim에서 상속되므로 소스 prim의 모든 USD 변경 사항이 클론된 prim에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성입니다.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 속성입니다.

강체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정하세요.これにより 객체가 정적 상태가 되고 중력이나 다른 힘의 영향을 받지 않습니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 대한 접촉 보고를 활성화합니다. 기본값은 False입니다.

이 옵션은 주어진 prim 경로와 그 하위에 있는 모든 강체에 PhysxContactReporter API를 추가합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 시각적 재료의 경로입니다. 기본값은 “material”입니다.

경로가 상대 경로인 경우, prim의 경로를 기준으로 해석됩니다.
이 매개변수는 visual_material이 None이 아닌 경우 무시됩니다.

#### visual_material *: [VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 물질 속성입니다.

#### NOTE
None이면 시각적 물질을 추가하지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

기본 material 경로로 설정된, 프라이머에서 사용할 물리 물질을 지정하는 경로입니다.
경로가 상대 경로인 경우, 프라이머의 경로를 기준으로 해석됩니다.
이 매개변수는 physics_material이 None이 아닌 경우 무시됩니다.

#### physics_material *: [PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리 물질 속성입니다.

#### NOTE
None이면 물리 물질을 추가하지 않습니다.

### isaaclab.sim.spawners.shapes.spawn_sphere(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [shapes_cfg.SphereCfg](#isaaclab.sim.spawners.shapes.SphereCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

지정된 속성을 갖는 USDGeom 기반 구면 프라이머를 생성합니다.

자세한 내용은 [USDGeomSphere](https://openusd.org/dev/api/class_usd_geom_sphere.html)를 참조하십시오.

#### NOTE
이 함수는 `clone()` 데코레이터로 장식되어 있으며, 입력 프라이머 경로가 정규식 패턴인 경우 경로 목록으로 해석됩니다.
이는 단일 경로 표현식에서 여러 애셋을 생성하고 지정된 경로 표현식에서 USD 프라이머를 복제하기 위해 수행됩니다.

* **매개변수:**
  * **prim_path** – 애셋을 생성할 프라이머 경로 또는 패턴입니다. 프라이머 경로가 정규식 패턴인 경우,
    애셋은 모든 일치하는 프라이머 경로에 생성됩니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 프라이머에 대해 적용할 프라이머의 이동입니다. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 프라이머에 대해 적용할 프라이머의 방향(w, x, y, z)입니다. 기본값은 None이며, 이 경우 항등으로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수입니다.
* **반환값:**
  생성된 프라이머입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 주어진 경로에 프라이머가 이미 존재하는 경우.

### *class* isaaclab.sim.spawners.shapes.SphereCfg

Bases: [`ShapeCfg`](#isaaclab.sim.spawners.shapes.ShapeCfg)

구면 프라이머의 구성 매개변수입니다.

자세한 내용은 [`spawn_sphere()`](#isaaclab.sim.spawners.shapes.spawn_sphere)를 참조하십시오.

**속성:**

| [`radius`](#isaaclab.sim.spawners.shapes.SphereCfg.radius)                                     | 미터 단위로 표현된 구의 반지름.                                  |
|------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`visible`](#isaaclab.sim.spawners.shapes.SphereCfg.visible)                                   | 생성된 애셋이 보이는지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.shapes.SphereCfg.semantic_tags)                       | 생성된 애셋에 추가할 의미 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.shapes.SphereCfg.copy_from_source)                 | 소스 프라이머에서 애셋을 복사할지 또는 상속받을지 여부.                 |
| [`mass_props`](#isaaclab.sim.spawners.shapes.SphereCfg.mass_props)                             | 질량 속성.                                              |
| [`rigid_props`](#isaaclab.sim.spawners.shapes.SphereCfg.rigid_props)                           | 강체 속성.                                        |
| [`collision_props`](#isaaclab.sim.spawners.shapes.SphereCfg.collision_props)                   | 모든 충돌 메시지에 적용할 속성.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.shapes.SphereCfg.activate_contact_sensors) | 모든 강체에 접촉 보고 활성화.               |
| [`visual_material_path`](#isaaclab.sim.spawners.shapes.SphereCfg.visual_material_path)         | 프라이머에 사용할 시각적 물질을 지정하는 경로.              |
| [`visual_material`](#isaaclab.sim.spawners.shapes.SphereCfg.visual_material)                   | 시각적 물질 속성.                                   |
| [`physics_material_path`](#isaaclab.sim.spawners.shapes.SphereCfg.physics_material_path)       | 프라이머에 사용할 물리 물질을 지정하는 경로.             |
| [`physics_material`](#isaaclab.sim.spawners.shapes.SphereCfg.physics_material)                 | 물리 물질 속성.                                  |

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

미터 단위로 표현된 구의 반지름.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 애셋이 보이는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 애셋에 추가할 의미 태그 목록. 기본값은 None이며, 이 경우 의미 태그가 추가되지 않습니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 의미 라벨입니다. 예를 들어, 생성된 애셋을 아보카도 클래스로 주석 처리하려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 생성된 애셋을 아보카도 클래스와 녹색 색상으로 주석 처리하려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프라이머에서 애셋을 복사할지 또는 상속받을지 여부. 기본값은 True입니다.

이 매개변수는 프라이머 복제 시에만 사용됩니다. False인 경우, 소스 프라이머에서 애셋을 상속받게 되며, 즉 소스 프라이머에 대한 모든 USD 변경 사항이 복제된 프라이머에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성입니다.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 속성입니다.

강체 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다.これにより、オブジェクトが静止し、重力や他の力の影響を受けなくなります。

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시지에 적용할 속성.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 접촉 보고 활성화. 기본값은 False입니다.

주어진 프라이머 경로 및 그 자식의 모든 강체에 PhysxContactReporter API가 추가됩니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

기본 material 경로로 설정된, 프라이머에서 사용할 시각적 물질을 지정하는 경로입니다.
경로가 상대 경로인 경우, 프라이머의 경로를 기준으로 해석됩니다.
이 매개변수는 visual_material이 None이 아닌 경우 무시됩니다.

#### visual_material *: [VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 물질 속성입니다.

#### NOTE
None이면 시각적 물질을 추가하지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

기본 material 경로로 설정된, 프라이머에서 사용할 물리 물질을 지정하는 경로입니다.
경로가 상대 경로인 경우, 프라이머의 경로를 기준으로 해석됩니다.
이 매개변수는 physics_material이 None이 아닌 경우 무시됩니다.

#### physics_material *: [PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리 물질 속성입니다.

#### NOTE
None이면 물리 물질을 추가하지 않습니다.

## Meshes

시뮬레이션에서 메시 생성을 위한 서브 모듈입니다.

NVIDIA Omniverse에서는 메시가 [USDGeomMesh](https://openusd.org/release/api/class_usd_geom_mesh.html) 프라이머로 처리됩니다. 이 서브 모듈은 다양한 메시 유형을 생성하기 위한 다양한 구성 옵션을 제공합니다. 구성에 따라 생성된 프라이머는 다음과 같이 될 수 있습니다:

* 시각 메시 (물리 속성 없음)
* 정적 충돌체 (강체 또는 변형 가능한 물체 없음)
* 변형 가능한 물체 (변형 가능한 속성 포함)

#### NOTE
메시에 강체 속성을 설정할 수 있지만, 대신
[`isaaclab.sim.spawners.shapes`](#module-isaaclab.sim.spawners.shapes) 모듈을 사용하여 rigid bodies를 스폰하는 데 사용됩니다. 이는 USD 셰이프가 물리 시뮬레이션에 더 최적화되어 있기 때문입니다.

### 클래스

| [`MeshCfg`](#isaaclab.sim.spawners.meshes.MeshCfg)                 | USD 기하학 또는 Geom prim의 구성 매개변수입니다.                     |
|--------------------------------------------------------------------|---------------------------------------------------------------------|
| [`MeshCapsuleCfg`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg)   | 캡슐 메쉬 prim의 구성 매개변수입니다.                                  |
| [`MeshConeCfg`](#isaaclab.sim.spawners.meshes.MeshConeCfg)         | 원뿔 메쉬 prim의 구성 매개변수입니다.                                 |
| [`MeshCuboidCfg`](#isaaclab.sim.spawners.meshes.MeshCuboidCfg)     | 변형 가능한 속성을 가진 직육면체 메쉬 prim의 구성 매개변수입니다.      |
| [`MeshCylinderCfg`](#isaaclab.sim.spawners.meshes.MeshCylinderCfg) | 변형 가능한 속성을 가진 원통 메쉬 prim의 구성 매개변수입니다.         |
| [`MeshSphereCfg`](#isaaclab.sim.spawners.meshes.MeshSphereCfg)     | 변형 가능한 속성을 가진 구 메쉬 prim의 구성 매개변수입니다.           |

### *class* isaaclab.sim.spawners.meshes.MeshCfg

USD 기하학 또는 Geom prim의 구성 매개변수입니다.

이 클래스는 `ShapeCfg`와 유사하지만 메쉬에 특화되어 있습니다.

메쉬는 강체 및 변형 가능한 속성을 모두 지원합니다. 그러나 이들의 스키마는 객체 유형에 따라 USD 계층 구조의 다른 레벨에 적용됩니다. 아래에 설명되어 있습니다:

- 변형 가능한 몸체 속성: 메쉬 prim에 적용됨: `{prim_path}/geometry/mesh`.
- 충돌 속성: 메쉬 prim에 적용됨: `{prim_path}/geometry/mesh`.
- 강체 속성: 부모 prim에 적용됨: `{prim_path}`.

여기서 `{prim_path}`는 USD 스테이지에서 prim의 경로이고, `{prim_path}/geometry/mesh`는 메쉬 prim의 경로입니다.

#### 참고
강체와 변형 가능한 속성에 대한 매개변수는 서로 배타적입니다. 두 개 모두 설정되면 오류가 발생합니다. 이는 충돌과 변형 가능한 속성을 함께 설정할 때도 동일하게 적용됩니다.

**속성:**

| [`visual_material_path`](#isaaclab.sim.spawners.meshes.MeshCfg.visual_material_path)         | prim에 사용할 시각적 자료 경로입니다.              |
|----------------------------------------------------------------------------------------------|----------------------------------------------------|
| [`visual_material`](#isaaclab.sim.spawners.meshes.MeshCfg.visual_material)                   | 시각적 자료 속성입니다.                            |
| [`physics_material_path`](#isaaclab.sim.spawners.meshes.MeshCfg.physics_material_path)       | prim에 사용할 물리 재료 경로입니다.                 |
| [`physics_material`](#isaaclab.sim.spawners.meshes.MeshCfg.physics_material)                 | 물리 재료 속성입니다.                               |
| [`visible`](#isaaclab.sim.spawners.meshes.MeshCfg.visible)                                   | 스폰된 애셋이 보이는지 여부입니다.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.meshes.MeshCfg.semantic_tags)                       | 스폰된 애셋에 추가할 시맨틱 태그 목록입니다.          |
| [`copy_from_source`](#isaaclab.sim.spawners.meshes.MeshCfg.copy_from_source)                 | 소스 prim에서 애셋을 복사할지 또는 상속할지 여부입니다. |
| [`mass_props`](#isaaclab.sim.spawners.meshes.MeshCfg.mass_props)                             | 질량 속성입니다.                                   |
| [`deformable_props`](#isaaclab.sim.spawners.meshes.MeshCfg.deformable_props)                 | 변형 가능한 몸체 속성입니다.                        |
| [`rigid_props`](#isaaclab.sim.spawners.meshes.MeshCfg.rigid_props)                           | 강체 속성입니다.                                    |
| [`collision_props`](#isaaclab.sim.spawners.meshes.MeshCfg.collision_props)                   | 모든 충돌 메쉬에 적용할 속성입니다.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.meshes.MeshCfg.activate_contact_sensors) | 모든 강체에 접촉 보고를 활성화합니다.               |

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 시각적 자료 경로입니다. 기본값은 "material"입니다.

경로가 상대 경로인 경우, prim의 경로를 기준으로 해석됩니다. 이 매개변수는 `visual_material`이 None이 아닐 때 무시됩니다.

#### visual_material *: [materials.VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 자료 속성입니다.

#### 참고
None인 경우, 시각적 자료가 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 물리 재료 경로입니다. 기본값은 "material"입니다.

경로가 상대 경로인 경우, prim의 경로를 기준으로 해석됩니다. 이 매개변수는 `physics_material`이 None이 아닐 때 무시됩니다.

#### physics_material *: [materials.PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리 재료 속성입니다.

#### 참고
None인 경우, 물리 재료가 추가되지 않습니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

스폰된 애셋이 보이는지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

스폰된 애셋에 추가할 시맨틱 태그 목록입니다. 기본값은 None이며, 이는 시맨틱 태그가 추가되지 않음을 의미합니다.

시맨틱 태그는 Replicator 시맨틱 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그 유형이고 `data`는 태그와 관련된 시맨틱 라벨입니다. 예를 들어, 스폰된 애셋을 아보카도 클래스로 주석 달려면 시맨틱 태그는 `[("class", "avocado")]`가 됩니다.

여러 시맨틱 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 스폰된 애셋을 아보카도 클래스와 초록색으로 주석 달려면 시맨틱 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### 참고
시맨틱 필터에 대한 자세한 정보는 [시맨틱 스키마 에디터 문서](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering)를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 prim에서 애셋을 복사할지 또는 상속할지 여부입니다. 기본값은 True입니다.

이 매개변수는 프라임 복제 시에만 사용됩니다. False이면 애셋이 소스 prim에서 상속됩니다. 즉, 소스 prim에 대한 모든 USD 변경 사항이 복제된 프라임에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성입니다.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 몸체 속성입니다.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 속성입니다.

강체 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정하십시오.これによりオブジェクトは静止し、重力やその他の力の影響を受けなくなります。

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메쉬에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 접촉 보고를 활성화합니다. 기본값은 False입니다.

이로 인해 지정된 prim 경로와 그 자식의 모든 강체에 PhysxContactReporter API가 추가됩니다.

### isaaclab.sim.spawners.meshes.spawn_mesh_capsule(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [meshes_cfg.MeshCapsuleCfg](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 속성을 가진 USD-Mesh 캡슐 prim을 생성합니다.

#### 참고
이 함수는 `clone()` 데코레이터로 장식되어 있으며, 입력 prim 경로가 정규 표현식 패턴인 경우 경로를 경로 목록으로 해석합니다. 이는 단일 표현식으로 여러 애셋을 스폰하고 주어진 경로 표현식에서 USD prim을 복제하는 것을 지원하기 위해 수행됩니다.

* **매개변수:**
  * **prim_path** – 애셋을 스폰할 prim 경로 또는 패턴입니다. prim 경로가 정규 표현식 패턴인 경우, 일치하는 모든 prim 경로에 애셋이 스폰됩니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 prim에 대해 prim에 적용할 변환입니다. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 prim에 대해 prim에 적용할 방향(w, x, y, z)입니다. 기본값은 None이며, 이 경우 항등 행렬로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수입니다.
* **Returns:**
  생성된 prim입니다.
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로에 이미 prim이 존재하는 경우.

### *class* isaaclab.sim.spawners.meshes.MeshCapsuleCfg

Bases: [`MeshCfg`](#isaaclab.sim.spawners.meshes.MeshCfg)

캡슐 메시 prim을 위한 구성 매개변수입니다.

자세한 내용은 `spawn_capsule()`를 참조하세요.

**Attributes:**

| [`visible`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.visible)                                   | 스폰된 에셋이 보이는지 여부.                  |
|-----------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`semantic_tags`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.semantic_tags)                       | 스폰된 에셋에 추가할 시맨틱 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.copy_from_source)                 | 소스 prim에서 에셋을 복사할지, 상속받을지 여부. |
| [`mass_props`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.mass_props)                             | 질량 특성.                                              |
| [`deformable_props`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.deformable_props)                 | 변형 가능한 몸체 특성.                                   |
| [`rigid_props`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.rigid_props)                           | 강체 몸체 특성.                                        |
| [`collision_props`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.collision_props)                   | 모든 충돌 메시 적용을 위한 속성.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.activate_contact_sensors) | 모든 강체에 접촉 보고 활성화.               |
| [`visual_material_path`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.visual_material_path)         | prim에 사용할 시각적 자료 경로.              |
| [`visual_material`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.visual_material)                   | 시각적 자료 속성.                                   |
| [`physics_material_path`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.physics_material_path)       | prim에 사용할 물리적 자료 경로.             |
| [`physics_material`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.physics_material)                 | 물리적 자료 속성.                                  |
| [`radius`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.radius)                                     | 캡슐의 반지름(단위: m).                                 |
| [`height`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.height)                                     | 캡슐의 높이(단위: m).                                 |
| [`axis`](#isaaclab.sim.spawners.meshes.MeshCapsuleCfg.axis)                                         | 캡슐의 축.                                          |

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

스폰된 에셋이 보이는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

스폰된 에셋에 추가할 시맨틱 태그 목록. 기본값은 None이며, 이 경우 시맨틱 태그가 추가되지 않습니다.

시맨틱 태그는 Replicator 시맨틱 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형식의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 시맨틱 라벨입니다. 예를 들어, 아보카도 클래스의 스폰된 에셋에 주석을 달려면 시맨틱 태그는 `[("class", "avocado")]`가 됩니다.

시맨틱 태그 목록을 전달하여 여러 시맨틱 태그를 지정할 수 있습니다. 예를 들어, 아보카도 클래스와 녹색 색상의 스폰된 에셋에 주석을 달려면 시맨틱 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 prim에서 에셋을 복사할지, 상속받을지 여부. 기본값은 True입니다.

이 매개변수는 프림을 복제할 때만 사용됩니다. False인 경우, 에셋은 소스 prim에서 상속받으며, 즉 소스 prim에 대한 모든 USD 변경 사항이 복제된 프림에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 특성.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 몸체 특성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 몸체 특성.

강체 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정하세요. 이렇게 하면 객체가 정중력이거나 다른 힘의 영향을 받지 않는 정적 객체가 됩니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시 적용을 위한 속성.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 접촉 보고를 활성화합니다. 기본값은 False입니다.

이것은 주어진 prim 경로와 그 자식들의 모든 강체에 PhysxContactReporter API를 추가합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 시각적 자료 경로. 기본값은 “material”입니다.

경로가 상대 경로인 경우, prim의 경로를 기준으로 해석됩니다. visual_material이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### visual_material *: [materials.VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 자료 속성.

#### NOTE
None인 경우, 시각적 자료가 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

prim에 사용할 물리적 자료 경로. 기본값은 “material”입니다.

경로가 상대 경로인 경우, prim의 경로를 기준으로 해석됩니다. physics_material이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### physics_material *: [materials.PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리적 자료 속성.

#### NOTE
None인 경우, 물리적 자료가 추가되지 않습니다.

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

캡슐의 반지름(단위: m).

#### height *: [float](https://docs.python.org/3/library/functions.html#float)*

캡슐의 높이(단위: m).

#### axis *: Literal['X', 'Y', 'Z']*

캡슐의 축. 기본값은 “Z”입니다.

### isaaclab.sim.spawners.meshes.spawn_mesh_cone(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [meshes_cfg.MeshConeCfg](#isaaclab.sim.spawners.meshes.MeshConeCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 속성을 사용하여 USD-Mesh 콘 prim을 생성합니다.

#### NOTE
이 함수는 `clone()`으로 데코레이트되어 입력된 prim 경로가 정규 표현식 패턴인 경우 경로를 경로 목록으로 해석합니다.これにより、単一のパス表現から複数のアセットをスポーンさせ、指定されたパス表現におけるUSD primをクローンすることができます。

* **Parameters:**
  * **prim_path** – 에셋을 스폰할 prim 경로 또는 패턴입니다. prim 경로가 정규 표현식 패턴인 경우, 일치하는 모든 prim 경로에 에셋이 스폰됩니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 prim에 대해 prim에 적용할 변환입니다. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 prim에 대해 prim에 적용할 방향(w, x, y, z)입니다. 기본값은 None이며, 이 경우 항등 행렬로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수입니다.
* **Returns:**
  생성된 prim입니다.
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 주어진 경로에 프리미가 이미 존재할 경우 발생합니다.

### *class* isaaclab.sim.spawners.meshes.MeshConeCfg

Bases: [`MeshCfg`](#isaaclab.sim.spawners.meshes.MeshCfg)

콘 메시 프리미에 대한 구성 매개변수입니다.

자세한 내용은 `spawn_cone()`를 참조하십시오.

**속성:**

| [`visible`](#isaaclab.sim.spawners.meshes.MeshConeCfg.visible)                                   | 스폰된 자산이 보이도록 할지 여부입니다.                  |
|--------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`semantic_tags`](#isaaclab.sim.spawners.meshes.MeshConeCfg.semantic_tags)                       | 스폰된 자산에 추가할 의미 태그 목록입니다.            |
| [`copy_from_source`](#isaaclab.sim.spawners.meshes.MeshConeCfg.copy_from_source)                 | 소스 프리미에서 자산을 복사할지 또는 상속받을지 여부입니다. |
| [`mass_props`](#isaaclab.sim.spawners.meshes.MeshConeCfg.mass_props)                             | 질량 속성입니다.                                              |
| [`deformable_props`](#isaaclab.sim.spawners.meshes.MeshConeCfg.deformable_props)                 | 변형 가능한 몸체 속성입니다.                                   |
| [`rigid_props`](#isaaclab.sim.spawners.meshes.MeshConeCfg.rigid_props)                           | 강체 몸체 속성입니다.                                        |
| [`collision_props`](#isaaclab.sim.spawners.meshes.MeshConeCfg.collision_props)                   | 모든 충돌 메시지에 적용할 속성입니다.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.meshes.MeshConeCfg.activate_contact_sensors) | 모든 강체에 대해 접촉 보고를 활성화합니다.               |
| [`visual_material_path`](#isaaclab.sim.spawners.meshes.MeshConeCfg.visual_material_path)         | 프리미에 사용할 시각적 재료의 경로입니다.              |
| [`visual_material`](#isaaclab.sim.spawners.meshes.MeshConeCfg.visual_material)                   | 시각적 재료 속성입니다.                                   |
| [`physics_material_path`](#isaaclab.sim.spawners.meshes.MeshConeCfg.physics_material_path)       | 프리미에 사용할 물리 재료의 경로입니다.             |
| [`physics_material`](#isaaclab.sim.spawners.meshes.MeshConeCfg.physics_material)                 | 물리 재료 속성입니다.                                  |
| [`radius`](#isaaclab.sim.spawners.meshes.MeshConeCfg.radius)                                     | 콘의 반지름 (단위: m).                                    |
| [`height`](#isaaclab.sim.spawners.meshes.MeshConeCfg.height)                                     | 콘의 높이 (단위: m).                                       |
| [`axis`](#isaaclab.sim.spawners.meshes.MeshConeCfg.axis)                                         | 콘의 축입니다.                                             |

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

스폰된 자산이 보이도록 할지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

스폰된 자산에 추가할 의미 태그 목록입니다. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 관련된 의미 레이블입니다. 예를 들어, 아보카도 클래스의 스폰된 자산에 주석을 달려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 녹색 색상의 스폰된 자산에 주석을 달려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [의미 스키마 에디터](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프리미에서 자산을 복사할지 또는 상속받을지 여부입니다. 기본값은 True입니다.

이 매개변수는 프리미를 클론할 때만 사용됩니다. False로 설정하면 자산이 소스 프리미로부터 상속됩니다. 즉, 소스 프리미에 대한 모든 USD 변경 사항이 클론된 프리미에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성입니다.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 몸체 속성입니다.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 몸체 속성입니다.

강체 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다. 이렇게 하면 객체가 정중해지고 중력이나 기타 힘의 영향을 받지 않습니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시지에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 대해 접촉 보고를 활성화합니다. 기본값은 False입니다.

주어진 프리미 경로 및 그 자식의 모든 강체에 PhysxContactReporter API를 추가합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프리미에 사용할 시각적 재료의 경로입니다. 기본값은 “material”입니다.

경로가 상대 경로인 경우, 프리미의 경로를 기준으로 상대 경로로 해석됩니다. `visual_material`이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### visual_material *: [materials.VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 재료 속성입니다.

#### NOTE
None인 경우 시각적 재료가 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프리미에 사용할 물리 재료의 경로입니다. 기본값은 “material”입니다.

경로가 상대 경로인 경우, 프리미의 경로를 기준으로 상대 경로로 해석됩니다. `physics_material`이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### physics_material *: [materials.PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리 재료 속성입니다.

#### NOTE
None인 경우 물리 재료가 추가되지 않습니다.

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

콘의 반지름 (단위: m).

#### height *: [float](https://docs.python.org/3/library/functions.html#float)*

콘의 높이 (단위: m).

#### axis *: Literal['X', 'Y', 'Z']*

콘의 축입니다. 기본값은 “Z”입니다.

### isaaclab.sim.spawners.meshes.spawn_mesh_cuboid(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [meshes_cfg.MeshCuboidCfg](#isaaclab.sim.spawners.meshes.MeshCuboidCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 속성을 가진 USD-Mesh 큐보이드 프리미를 생성합니다.

#### NOTE
이 함수는 `clone()` 데코레이터로 수정되어 입력 프리미 경로가 정규 표현식 패턴인 경우 경로 목록으로 해석됩니다. 이를 통해 단일 경로 표현식에서 여러 자산을 생성하고 해당 경로 표현식의 USD 프리미를 클론할 수 있습니다.

* **매개변수:**
  * **prim_path** – 자산을 스폰할 프리미 경로 또는 패턴입니다. 프리미 경로가 정규 표현식 패턴인 경우, 일치하는 모든 프리미 경로에 자산을 스폰합니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 프리미에 상대적인 프리미에 적용할 변환입니다. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 프리미에 상대적인 프리미에 적용할 방향 (w, x, y, z)입니다. 기본값은 None이며, 이 경우 항등 행렬로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수입니다.
* **반환값:**
  생성된 프리미입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 주어진 경로에 프리미가 이미 존재할 경우 발생합니다.

### *class* isaaclab.sim.spawners.meshes.MeshCuboidCfg

Bases: [`MeshCfg`](#isaaclab.sim.spawners.meshes.MeshCfg)

변형 가능한 속성을 갖춘 큐보이드 메시 프리미에 대한 구성 매개변수입니다.

자세한 내용은 [`spawn_mesh_cuboid()`](#isaaclab.sim.spawners.meshes.spawn_mesh_cuboid)를 참조하십시오.

**속성:**

| [`size`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.size)                                         | 큐브오이드 크기(단위: m).                                    |
|----------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`visible`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.visible)                                   | 생성된 애셋이 표시되어야 하는지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.semantic_tags)                       | 생성된 애셋에 추가할 의미 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.copy_from_source)                 | 소스 프리임에서 애셋을 복사할지 또는 상속할지 여부. |
| [`mass_props`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.mass_props)                             | 질량 속성.                                              |
| [`deformable_props`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.deformable_props)                 | 변형 가능한 신체 속성.                                   |
| [`rigid_props`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.rigid_props)                           | 강체 신체 속성.                                        |
| [`collision_props`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.collision_props)                   | 모든 충돌 메시에 적용할 속성.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.activate_contact_sensors) | 모든 강체에서 접촉 보고 활성화.               |
| [`visual_material_path`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.visual_material_path)         | 프리임에 사용할 시각적 재질 경로.              |
| [`visual_material`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.visual_material)                   | 시각적 재질 속성.                                   |
| [`physics_material_path`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.physics_material_path)       | 프리임에 사용할 물리적 재질 경로.             |
| [`physics_material`](#isaaclab.sim.spawners.meshes.meshcuboidcfg.physics_material)                 | 물리적 재질 속성.                                  |

#### size *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

큐브오이드 크기(단위: m).

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 애셋이 표시되어야 하는지 여부. 기본값은 True.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 애셋에 추가할 의미 태그 목록. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미한다.

의미 태그는 Replicator 의미 태그 시스템을 따릅니다. 각 태그는 `(type, data)` 형식의 튜플이며, 여기서 `type`은 태그 유형을, `data`는 태그와 연결된 의미 레이블을 나타냅니다. 예를 들어, 생성된 애셋을 아보카도 클래스로 주석 처리하려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그를 지정하려면 태그 리스트를 전달하면 됩니다. 예를 들어, 생성된 애셋을 아보카도 클래스와 녹색 색상으로 주석 처리하려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [semantics schema editor](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프리임에서 애셋을 복사할지 또는 상속할지 여부. 기본값은 True.

이 파라미터는 프리임을 클론할 때만 사용됩니다. False이면, 애셋은 소스 프리임에서 상속되며, 즉 소스 프리임에 대한 모든 USD 변경 사항이 클론된 프리임에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.masspropertiescfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.deformablebodypropertiescfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 신체 속성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.rigidbodypropertiescfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 신체 속성.

강체 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다. 이는 객체를 정적으로 만들고 중력이나 다른 힘의 영향을 받지 않게 만듭니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.collisionpropertiescfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시에 적용할 속성.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에서 접촉 보고 활성화. 기본값은 False.

이 옵션은 주어진 프리임 경로와 그 하위의 모든 강체에 PhysxContactReporter API를 추가합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프리임에 사용할 시각적 재질 경로. 기본값은 “material”.

경로가 상대적인 경우, 프리임의 경로를 기준으로 해석됩니다. 이 파라미터는 visual_material이 None이 아닌 경우 무시됩니다.

#### visual_material *: [materials.VisualMaterialCfg](#isaaclab.sim.spawners.materials.visualmaterialcfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 재질 속성.

#### NOTE
None인 경우 시각적 재질이 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프리임에 사용할 물리적 재질 경로. 기본값은 “material”.

경로가 상대적인 경우, 프리임의 경로를 기준으로 해석됩니다. 이 파라미터는 physics_material이 None이 아닌 경우 무시됩니다.

#### physics_material *: [materials.PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.physicsmaterialcfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리적 재질 속성.

#### NOTE
None인 경우 물리적 재질이 추가되지 않습니다.

### isaaclab.sim.spawners.meshes.spawn_mesh_cylinder(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [meshes_cfg.MeshCylinderCfg](#isaaclab.sim.spawners.meshes.meshcylindercfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 속성으로 USD-Mesh 실린더 프리임을 생성합니다.

#### NOTE
이 함수는 `clone()`으로 장식되어 입력된 프리임 경로가 정규식 패턴인 경우 경로 목록으로 해석합니다. 이는 단일 경로 표현식에서 여러 애셋을 생성하고 USD 프리임을 해당 경로 표현식에 따라 복제하는 것을 지원하기 위해 수행됩니다.

* **매개변수:**
  * **prim_path** – 애셋을 생성할 프리임 경로 또는 패턴. 프리임 경로가 정규식 패턴인 경우, 애셋은 일치하는 모든 프리임 경로에 생성됩니다.
  * **cfg** – 구성 인스턴스.
  * **translation** – 부모 프리임에 상대하여 프리임에 적용할 변환. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 프리임에 상대하여 프리임에 적용할 방향 (w, x, y, z). 기본값은 None이며, 이 경우 단위 행렬로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수.
* **반환값:**
  생성된 프리임.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로에 프리임이 이미 존재하는 경우.

### *class* isaaclab.sim.spawners.meshes.MeshCylinderCfg

Bases: [`MeshCfg`](#isaaclab.sim.spawners.meshes.meshcfg)

변형 가능한 속성을 가진 실린더 메시 프리임에 대한 구성 매개변수.

자세한 내용은 `spawn_cylinder()`를 참조하세요.

**속성:**

| [`radius`](#isaaclab.sim.spawners.meshes.meshcylindercfg.radius)                                     | 실린더 반지름(단위: m).                                |
|------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`height`](#isaaclab.sim.spawners.meshes.meshcylindercfg.height)                                     | 실린더 높이(단위: m).                                |
| [`axis`](#isaaclab.sim.spawners.meshes.meshcylindercfg.axis)                                         | 실린더 축.                                         |
| [`visible`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.visible)                                   | 생성된 애셋이 보이는지 여부를 나타냅니다.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.semantic_tags)                       | 생성된 애셋에 추가할 시맨틱 태그 목록입니다.            |
| [`copy_from_source`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.copy_from_source)                 | 소스 프라이브에서 애셋을 복사할지 또는 상속할지 여부를 나타냅니다. |
| [`mass_props`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.mass_props)                             | 질량 속성입니다.                                              |
| [`deformable_props`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.deformable_props)                 | 변형 가능한 물체 속성입니다.                                   |
| [`rigid_props`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.rigid_props)                           | 강체 물체 속성입니다.                                        |
| [`collision_props`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.collision_props)                   | 모든 충돌 메시지에 적용할 속성입니다.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.activate_contact_sensors) | 모든 강체에 대한 접촉 보고를 활성화합니다.               |
| [`visual_material_path`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.visual_material_path)         | 프라이브에 사용할 시각적 재질의 경로입니다.              |
| [`visual_material`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.visual_material)                   | 시각적 재질 속성입니다.                                   |
| [`physics_material_path`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.physics_material_path)       | 프라이브에 사용할 물리 재질의 경로입니다.             |
| [`physics_material`](#isaaclab.sim.spawners.meshes.meshcylinder.MeshCylinderCfg.physics_material)                 | 물리 재질 속성입니다.                                  |

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

실린더의 반지름(단위: m)입니다.

#### height *: [float](https://docs.python.org/3/library/functions.html#float)*

실린더의 높이(단위: m)입니다.

#### axis *: Literal['X', 'Y', 'Z']*

실린더의 축입니다. 기본값은 “Z”입니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 애셋이 보이는지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 애셋에 추가할 시맨틱 태그 목록입니다. 기본값은 None이며,
이는 시맨틱 태그가 추가되지 않음을 의미합니다.

시맨틱 태그는 Replicator 시맨틱 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며,
여기서 `type`은 태그의 유형이고 `data`는 태그와 관련된 시맨틱 레이블입니다.
예를 들어, 생성된 애셋을 아보카도 클래스로 주석 처리하려면 시맨틱 태그는 `[("class", "avocado")]`가 됩니다.

여러 개의 시맨틱 태그는 태그 목록을 전달하여 지정할 수 있습니다.
예를 들어, 생성된 애셋을 아보카도 클래스와 초록색으로 주석 처리하려면 시맨틱 태그는
`[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프라이브에서 애셋을 복사할지 또는 상속할지 여부입니다. 기본값은 True입니다.

이 매개변수는 프라이브를 클론할 때만 사용됩니다. False인 경우, 애셋은 소스 프라이브로부터 상속됩니다.
즉, 소스 프라이브에 대한 모든 USD 변경 사항이 클론된 프라이브에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성입니다.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 물체 속성입니다.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 물체 속성입니다.

강체 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다.
이렇게 하면 객체가 정적이 되고 중력이나 기타 힘의 영향을 받지 않게 됩니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시지에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 대한 접촉 보고를 활성화합니다. 기본값은 False입니다.

이 옵션은 지정된 프라이브 경로와 그 자식의 모든 강체에 PhysxContactReporter API를 추가합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프라이브에 사용할 시각적 재질의 경로입니다. 기본값은 “material”입니다.

경로가 상대 경로인 경우, 프라이브의 경로를 기준으로 해석됩니다.
이 매개변수는 visual_material이 None이 아닌 경우 무시됩니다.

#### visual_material *: [materials.VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 재질 속성입니다.

#### NOTE
None인 경우, 시각적 재질이 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프라이브에 사용할 물리 재질의 경로입니다. 기본값은 “material”입니다.

경로가 상대 경로인 경우, 프라이브의 경로를 기준으로 해석됩니다.
이 매개변수는 physics_material이 None이 아닌 경우 무시됩니다.

#### physics_material *: [materials.PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리 재질 속성입니다.

#### NOTE
None인 경우, 물리 재질이 추가되지 않습니다.

### isaaclab.sim.spawners.meshes.spawn_mesh_sphere(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [meshes_cfg.MeshSphereCfg](#isaaclab.sim.spawners.meshes.MeshSphereCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 속성으로 USD-Mesh 구형 프라이브를 생성합니다.

#### NOTE
이 함수는 `clone()` 데코레이터로 장식되어 있으며,
입력 프라이브 경로가 정규 표현식 패턴인 경우 프라이브 경로를 경로 목록으로 해석합니다.
이는 단일 표현식에서 여러 애셋을 생성하고 지정된 경로 표현식의 USD 프라이브를 클론하기 위해 수행됩니다.

* **매개변수:**
  * **prim_path** – 애셋을 생성할 프라이브 경로 또는 패턴입니다. 프라이브 경로가 정규 표현식 패턴인 경우,
    애셋은 일치하는 모든 프라이브 경로에 생성됩니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 프라이브를 기준으로 프라이브에 적용할 변환입니다. 기본값은 None이며,
    이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 프라이브를 기준으로 프라이브에 적용할 (w, x, y, z) 형식의 방향입니다. 기본값은 None이며,
    이 경우 단위 행렬로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수입니다.
* **반환값:**
  생성된 프라이브입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로에 이미 프라이브가 존재하는 경우 발생합니다.

### *class* isaaclab.sim.spawners.meshes.MeshSphereCfg

Bases: [`MeshCfg`](#isaaclab.sim.spawners.meshes.meshcfg)

변형 가능한 속성을 가진 구형 메시 프라이브에 대한 구성 매개변수입니다.

더 많은 정보는 [`spawn_mesh_sphere()`](#isaaclab.sim.spawners.meshes.spawn_mesh_sphere)를 참조하세요.

**속성:**

| [`radius`](#isaaclab.sim.spawners.meshes.meshsphere.MeshSphereCfg.radius)                                     | 구의 반지름(단위: m)입니다.                                  |
|----------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`visible`](#isaaclab.sim.spawners.meshes.meshsphere.MeshSphereCfg.visible)                                   | 생성된 애셋이 보이는지 여부를 나타냅니다.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.meshes.meshsphere.MeshSphereCfg.semantic_tags)                       | 생성된 애셋에 추가할 시맨틱 태그 목록입니다.            |
| [`copy_from_source`](#isaaclab.sim.spawners.meshes.meshsphere.MeshSphereCfg.copy_from_source)                 | 소스 프라이브에서 애셋을 복사할지 또는 상속할지 여부를 나타냅니다. |
| [`mass_props`](#isaaclab.sim.spawners.meshes.meshsphere.MeshSphereCfg.mass_props)                             | 질량 속성입니다.                                              |
| [`deformable_props`](#isaaclab.sim.spawners.meshes.MeshSphereCfg.deformable_props)                 | 변형 가능한 신체 속성.                                   |
| [`rigid_props`](#isaaclab.sim.spawners.meshes.MeshSphereCfg.rigid_props)                           | 강체 속성.                                        |
| [`collision_props`](#isaaclab.sim.spawners.meshes.MeshSphereCfg.collision_props)                   | 모든 충돌 메시지에 적용할 속성.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.meshes.MeshSphereCfg.activate_contact_sensors) | 모든 강체에 대한 접촉 보고서 활성화.               |
| [`visual_material_path`](#isaaclab.sim.spawners.meshes.MeshSphereCfg.visual_material_path)         | 프리믹에 사용할 시각적 재료의 경로.              |
| [`visual_material`](#isaaclab.sim.spawners.meshes.MeshSphereCfg.visual_material)                   | 시각적 재료 속성.                                   |
| [`physics_material_path`](#isaaclab.sim.spawners.meshes.MeshSphereCfg.physics_material_path)       | 프리믹에 사용할 물리 재료의 경로.             |
| [`physics_material`](#isaaclab.sim.spawners.meshes.MeshSphereCfg.physics_material)                 | 물리 재료 속성.                                  |

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

구체의 반지름(미터).

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 보이는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 의미 태그 목록. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그 유형이고 `data`는 태그와 관련된 의미 라벨입니다. 예를 들어, 생성된 에셋을 아보카도 클래스로 주석 처리하려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

태그 목록을 전달하여 여러 의미 태그를 지정할 수 있습니다. 예를 들어, 생성된 에셋을 아보카도 클래스와 녹색 색상으로 주석 처리하려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 정보는 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프리믹에서 에셋을 복사할지, 상속할지 여부. 기본값은 True입니다.

이 매개변수는 프리믹을 복제할 때만 사용됩니다. False인 경우, 에셋은 소스 프리믹에서 상속됩니다. 즉, 소스 프리믹의 모든 USD 변경 사항이 복제된 프리믹에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 신체 속성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 속성.

강체 개체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다.これにより、객체는 정적으로 유지되고 중력 또는 기타 힘의 영향을 받지 않게 됩니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시지에 적용할 속성.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 대한 접촉 보고서 활성화. 기본값은 False입니다.

이는 주어진 프리믹 경로 및 그 하위의 모든 강체에 PhysxContactReporter API를 추가합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프리믹에 사용할 시각적 재료의 경로. 기본값은 “material”입니다.

경로가 상대 경로인 경우, 프리믹의 경로를 기준으로 해석됩니다. 이 매개변수는 visual_material이 None이 아닐 경우 무시됩니다.

#### visual_material *: [materials.VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

시각적 재료 속성.

#### NOTE
None인 경우, 시각적 재료가 추가되지 않습니다.

#### physics_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프리믹에 사용할 물리 재료의 경로. 기본값은 “material”입니다.

경로가 상대 경로인 경우, 프리믹의 경로를 기준으로 해석됩니다. 이 매개변수는 physics_material이 None이 아닐 경우 무시됩니다.

#### physics_material *: [materials.PhysicsMaterialCfg](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

물리 재료 속성.

#### NOTE
None인 경우, 물리 재료가 추가되지 않습니다.

## 조명

시뮬레이션에서 조명을 생성하는 스포너를 위한 서브모듈입니다.

USD 스테이지에 다양한 유형의 조명을 생성할 수 있습니다. 조명 개요에 대한 자세한 내용은 [옴니버스 문서](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/lighting.html)를 참조하세요.

### 클래스

| [`LightCfg`](#isaaclab.sim.spawners.lights.LightCfg)                 | 씬에서 조명을 생성하기 위한 구성 파라미터.          |
|----------------------------------------------------------------------|----------------------------------------------------------------------|
| [`CylinderLightCfg`](#isaaclab.sim.spawners.lights.CylinderLightCfg) | 씬에서 원통형 조명을 생성하기 위한 구성 파라미터. |
| [`DiskLightCfg`](#isaaclab.sim.spawners.lights.DiskLightCfg)         | 씬에서 디스크 조명을 생성하기 위한 구성 파라미터.     |
| [`DistantLightCfg`](#isaaclab.sim.spawners.lights.DistantLightCfg)   | 씬에서 원거리 조명을 생성하기 위한 구성 파라미터.   |
| [`DomeLightCfg`](#isaaclab.sim.spawners.lights.DomeLightCfg)         | 씬에서 돔 조명을 생성하기 위한 구성 파라미터.     |
| [`SphereLightCfg`](#isaaclab.sim.spawners.lights.SphereLightCfg)     | 씬에서 구형 조명을 생성하기 위한 구성 파라미터.   |

### isaaclab.sim.spawners.lights.spawn_light(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [lights_cfg.LightCfg](#isaaclab.sim.spawners.lights.LightCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

지정된 프리믹 경로와 구성으로 조명 프리믹을 생성합니다.

생성된 프리믹은 [USD.LuxLight](https://openusd.org/dev/api/class_usd_lux_light_a_p_i.html) API를 기반으로 합니다.

#### NOTE
이 함수는 `clone()` 데코레이터로 수정되어, 입력 프리믹 경로가 정규식 패턴인 경우 경로 목록으로 변환됩니다. 이를 통해 단일 경로 표현식에서 여러 에셋을 생성하고 USD 프리믹을 복제할 수 있습니다.

* **매개변수:**
  * **prim_path** – 에셋을 생성할 프리믹 경로 또는 패턴. 프리믹 경로가 정규식 패턴인 경우, 일치하는 모든 프리믹 경로에 에셋이 생성됩니다.
  * **cfg** – 조명 소스의 구성.
  * **translation** – 프리믹의 이동. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 프리믹의 방향 (w, x, y, z). 기본값은 None이며, 이 경우 항등 행렬로 설정됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 프리믹 경로에 이미 프리믹이 존재하는 경우.

### *class* isaaclab.sim.spawners.lights.LightCfg

씬에서 조명을 생성하기 위한 구성 파라미터.

자세한 내용은 [USD LuxLight](https://openusd.org/dev/api/class_usd_lux_light_a_p_i.html) 문서를 참조하세요.

#### NOTE
속성의 기본값은 해당 공식 문서에 명시된 값입니다.

**속성:**

| [`prim_type`](#isaaclab.sim.spawners.lights.LightCfg.prim_type)                               | 조명 프리믹의 프리믹 유형 이름.                        |
|-----------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`color`](#isaaclab.sim.spawners.lights.LightCfg.color)                                       | 에너지-선형 항의 방출된 빛의 색상.           |
| [`enable_color_temperature`](#isaaclab.sim.spawners.lights.LightCfg.enable_color_temperature) | 색 온도 활성화.                                    |
| [`color_temperature`](#isaaclab.sim.spawners.lights.LightCfg.color_temperature)               | 흰색 점을 나타내는 색 온도 (켈빈 단위).   |
| [`normalize`](#isaaclab.sim.spawners.lights.LightCfg.normalize)                               | 빛의 표면적에 따라 전력을 정규화합니다.            |
| [`exposure`](#isaaclab.sim.spawners.lights.LightCfg.exposure)                                 | 2의 지수로 빛의 전력을 지수 배율하여 스케일링합니다.  |
| [`intensity`](#isaaclab.sim.spawners.lights.LightCfg.intensity)                               | 빛의 전력을 선형으로 스케일링합니다.                       |
| [`visible`](#isaaclab.sim.spawners.lights.LightCfg.visible)                                   | 생성된 에셋이 표시되어야 하는지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.lights.LightCfg.semantic_tags)                       | 생성된 에셋에 추가할 의미 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.lights.LightCfg.copy_from_source)                 | 소스 프림에서 에셋을 복사할지 상속할지 여부. |

#### prim_type *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

빛 프림의 프림 타입 이름.

#### color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

에너지-선형 기준에서 방출되는 빛의 색상. 기본값은 흰색입니다.

#### enable_color_temperature *: [bool](https://docs.python.org/3/library/functions.html#bool)*

색 온도 활성화. 기본값은 false입니다.

#### color_temperature *: [float](https://docs.python.org/3/library/functions.html#float)*

흰색 점을 나타내는 색 온도 (켈빈 단위). 유효 범위는 [1000, 10000]이며, 기본값은 6500K입니다.

[색 온도](https://en.wikipedia.org/wiki/Color_temperature)는 빛의 따뜻함 또는 차가움에 대응합니다. 따뜻한 빛은 낮은 색 온도를 가지며, 차가운 빛은 높은 색 온도를 가집니다.

#### NOTE
[`enable_color_temperature`](#isaaclab.sim.spawners.lights.LightCfg.enable_color_temperature)가 true일 때만 효과가 적용됩니다.

#### normalize *: [bool](https://docs.python.org/3/library/functions.html#bool)*

빛의 표면적에 따라 전력을 정규화합니다. 기본값은 false입니다.

이를 통해 빛의 전력과 형태를 독립적으로 조정하기 쉽게 만들며, 전력이 빛의 면적 또는 각도 크기에 따라 변하지 않도록 합니다.

#### exposure *: [float](https://docs.python.org/3/library/functions.html#float)*

2의 지수로 빛의 전력을 지수 배율하여 스케일링합니다. 기본값은 0.0입니다.

결과는 강도와 곱해집니다.

#### intensity *: [float](https://docs.python.org/3/library/functions.html#float)*

빛의 전력을 선형으로 스케일링합니다. 기본값은 1.0입니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 표시되어야 하는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 의미 태그 목록. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그 유형이고 `data`는 태그와 연결된 의미 라벨입니다. 예를 들어, 아보카도 클래스의 생성된 에셋에 주석을 달려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 초록색의 생성된 에셋에 주석을 달려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 에셋을 복사할지 상속할지 여부. 기본값은 True입니다.

이 매개변수는 프림을 클론할 때만 사용됩니다. False이면 에셋이 소스 프림에서 상속되며, 즉 소스 프림에 대한 모든 USD 변경 사항이 클론된 프림에 반영됩니다.

### *class* isaaclab.sim.spawners.lights.CylinderLightCfg

장면에 실린더 라이트를 생성하기 위한 구성 매개변수.

실린더 라이트는 실린더에서 빛을 방출하는 광원으로, 형광등 시뮬레이션에 유용합니다. 자세한 내용은 [USDLux CylinderLight](https://openusd.org/dev/api/class_usd_lux_cylinder_light.html) 문서를 참조하십시오.

#### NOTE
속성의 기본값은 해당 공식 문서에 명시된 값입니다.

**속성:**

| [`visible`](#isaaclab.sim.spawners.lights.CylinderLightCfg.visible)                                   | 생성된 에셋이 표시되어야 하는지 여부.                       |
|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| [`semantic_tags`](#isaaclab.sim.spawners.lights.CylinderLightCfg.semantic_tags)                       | 생성된 에셋에 추가할 의미 태그 목록.                 |
| [`copy_from_source`](#isaaclab.sim.spawners.lights.CylinderLightCfg.copy_from_source)                 | 소스 프림에서 에셋을 복사할지 상속할지 여부.      |
| [`color`](#isaaclab.sim.spawners.lights.CylinderLightCfg.color)                                       | 에너지-선형 기준에서 방출되는 빛의 색상.                |
| [`enable_color_temperature`](#isaaclab.sim.spawners.lights.CylinderLightCfg.enable_color_temperature) | 색 온도 활성화.                                         |
| [`color_temperature`](#isaaclab.sim.spawners.lights.CylinderLightCfg.color_temperature)               | 흰색 점을 나타내는 색 온도 (켈빈 단위).        |
| [`normalize`](#isaaclab.sim.spawners.lights.CylinderLightCfg.normalize)                               | 빛의 표면적에 따라 전력을 정규화합니다.                 |
| [`exposure`](#isaaclab.sim.spawners.lights.CylinderLightCfg.exposure)                                 | 2의 지수로 빛의 전력을 지수 배율하여 스케일링합니다.       |
| [`intensity`](#isaaclab.sim.spawners.lights.CylinderLightCfg.intensity)                               | 빛의 전력을 선형으로 스케일링합니다.                            |
| [`prim_type`](#isaaclab.sim.spawners.lights.CylinderLightCfg.prim_type)                               | 빛 프림의 프림 타입 이름.                             |
| [`length`](#isaaclab.sim.spawners.lights.CylinderLightCfg.length)                                     | 실린더 길이 (미터 단위).                                     |
| [`radius`](#isaaclab.sim.spawners.lights.CylinderLightCfg.radius)                                     | 실린더 반지름 (미터 단위).                                     |
| [`treat_as_line`](#isaaclab.sim.spawners.lights.CylinderLightCfg.treat_as_line)                       | 실린더를 반지름이 0인 선 소스로 취급합니다. |

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 표시되어야 하는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 의미 태그 목록. 기본값은 None이며, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그 유형이고 `data`는 태그와 연결된 의미 라벨입니다. 예를 들어, 아보카도 클래스의 생성된 에셋에 주석을 달려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 초록색의 생성된 에셋에 주석을 달려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 에셋을 복사할지 상속할지 여부. 기본값은 True입니다.

이 매개변수는 프림을 클론할 때만 사용됩니다. False이면 에셋이 소스 프림에서 상속되며, 즉 소스 프림에 대한 모든 USD 변경 사항이 클론된 프림에 반영됩니다.

#### color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

에너지-선형 기준에서 방출되는 빛의 색상. 기본값은 흰색입니다.

#### enable_color_temperature *: [bool](https://docs.python.org/3/library/functions.html#bool)*

색온도 기능을 활성화합니다. 기본값은 false입니다.

#### color_temperature *: [float](https://docs.python.org/3/library/functions.html#float)*

백색 점을 나타내는 색온도(켈빈). 유효 범위는 [1000, 10000]입니다. 기본값은 6500K입니다.

[색온도](https://en.wikipedia.org/wiki/Color_temperature)는 빛의 따뜻함 또는 차가움을 나타냅니다. 따뜻한 빛은 낮은 색온도를 가지며, 차가운 빛은 높은 색온도를 가집니다.

#### NOTE
[`enable_color_temperature`](#isaaclab.sim.spawners.lights.CylinderLightCfg.enable_color_temperature)가 true일 때만 적용됩니다.

#### normalize *: [bool](https://docs.python.org/3/library/functions.html#bool)*

빛의 표면적에 따라 전력을 정규화합니다. 기본값은 false입니다.

이로 인해 빛의 면적 또는 각도 크기와 무관하게 전력이 변하지 않도록 하여, 빛의 전력과 모양을 독립적으로 조정하기 쉬워집니다.

#### exposure *: [float](https://docs.python.org/3/library/functions.html#float)*

빛의 전력을 2의 거듭제곱으로 기하급수적으로 배수합니다. 기본값은 0.0입니다.

결과값은 강도에 곱해집니다.

#### intensity *: [float](https://docs.python.org/3/library/functions.html#float)*

빛의 전력을 선형적으로 배수합니다. 기본값은 1.0입니다.

#### prim_type *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

빛 프리임의 프리임 유형 이름입니다.

#### length *: [float](https://docs.python.org/3/library/functions.html#float)*

실린더의 길이(미터). 기본값은 1.0m입니다.

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

실린더의 반지름(미터). 기본값은 0.5m입니다.

#### treat_as_line *: [bool](https://docs.python.org/3/library/functions.html#bool)*

실린더를 반지름이 0인 선형 광원으로 처리합니다. 기본값은 false입니다.

### *class* isaaclab.sim.spawners.lights.DiskLightCfg

장면에 디스크 라이트를 생성하기 위한 구성 매개변수입니다.

디스크 라이트는 디스크에서 빛을 발산하는 광원으로, 형광등 시뮬레이션에 유용합니다. 자세한 내용은
[USDLux DiskLight](https://openusd.org/dev/api/class_usd_lux_disk_light.html) 문서를 참조하십시오.

#### NOTE
기본값은 해당 공식 문서에서 지정된 값입니다.

**속성:**

| [`prim_type`](#isaaclab.sim.spawners.lights.DiskLightCfg.prim_type)                               | 빛 프리임의 프리임 유형 이름입니다.                        |
|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`radius`](#isaaclab.sim.spawners.lights.DiskLightCfg.radius)                                     | 디스크의 반지름(미터 단위).                                    |
| [`visible`](#isaaclab.sim.spawners.lights.DiskLightCfg.visible)                                   | 생성된 애셋이 보일지 여부입니다.                            |
| [`semantic_tags`](#isaaclab.sim.spawners.lights.DiskLightCfg.semantic_tags)                       | 생성된 애셋에 추가할 시맨틱 태그 목록입니다.                  |
| [`copy_from_source`](#isaaclab.sim.spawners.lights.DiskLightCfg.copy_from_source)                 | 소스 프리임에서 애셋을 복사할지 또는 상속할지 여부입니다.      |
| [`color`](#isaaclab.sim.spawners.lights.DiskLightCfg.color)                                       | 에너지-선형 기준으로 표현된 발산 빛의 색입니다.               |
| [`enable_color_temperature`](#isaaclab.sim.spawners.lights.DiskLightCfg.enable_color_temperature) | 색온도 기능을 활성화합니다.                                  |
| [`color_temperature`](#isaaclab.sim.spawners.lights.DiskLightCfg.color_temperature)               | 백색 점을 나타내는 색온도(켈빈).                              |
| [`normalize`](#isaaclab.sim.spawners.lights.DiskLightCfg.normalize)                               | 빛의 표면적에 따라 전력을 정규화합니다.                       |
| [`exposure`](#isaaclab.sim.spawners.lights.DiskLightCfg.exposure)                                 | 빛의 전력을 2의 거듭제곱으로 기하급수적으로 배수합니다.       |
| [`intensity`](#isaaclab.sim.spawners.lights.DiskLightCfg.intensity)                               | 빛의 전력을 선형적으로 배수합니다.                            |

#### prim_type *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

빛 프리임의 프리임 유형 이름입니다.

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

디스크의 반지름(미터 단위). 기본값은 0.5m입니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 애셋이 보일지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 애셋에 추가할 시맨틱 태그 목록입니다. 기본값은 None이며,この場合 시맨틱 태그가 추가되지 않습니다.

시맨틱 태그는 레플리케이터 시맨틱 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플로, 여기서 `type`은 태그의 종류이고 `data`는 해당 태그와 연결된 시맨틱 라벨입니다. 예를 들어, 아보카도 클래스의 생성된 애셋에 주석을 달려면 시맨틱 태그는 `[("class", "avocado")]`가 됩니다.

여러 시맨틱 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 녹색 색상의 생성된 애셋에 주석을 달려면 시맨틱 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프리임에서 애셋을 복사할지 또는 상속할지 여부입니다. 기본값은 True입니다.

이 매개변수는 프리임을 클론할 때만 사용됩니다. False인 경우, 애셋은 소스 프리임으로부터 상속되므로, 즉 소스 프리임에 대한 모든 USD 변경 사항이 클론된 프리임에 반영됩니다.

#### color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

에너지-선형 기준으로 표현된 발산 빛의 색입니다. 기본값은 흰색입니다.

#### enable_color_temperature *: [bool](https://docs.python.org/3/library/functions.html#bool)*

색온도 기능을 활성화합니다. 기본값은 false입니다.

#### color_temperature *: [float](https://docs.python.org/3/library/functions.html#float)*

백색 점을 나타내는 색온도(켈빈). 유효 범위는 [1000, 10000]입니다. 기본값은 6500K입니다.

[색온도](https://en.wikipedia.org/wiki/Color_temperature)는 빛의 따뜻함 또는 차가움을 나타냅니다. 따뜻한 빛은 낮은 색온도를 가지며, 차가운 빛은 높은 색온도를 가집니다.

#### NOTE
[`enable_color_temperature`](#isaaclab.sim.spawners.lights.DiskLightCfg.enable_color_temperature)가 true일 때만 적용됩니다.

#### normalize *: [bool](https://docs.python.org/3/library/functions.html#bool)*

빛의 표면적에 따라 전력을 정규화합니다. 기본값은 false입니다.

이로 인해 빛의 면적 또는 각도 크기와 무관하게 전력이 변하지 않도록 하여, 빛의 전력과 모양을 독립적으로 조정하기 쉬워집니다.

#### exposure *: [float](https://docs.python.org/3/library/functions.html#float)*

빛의 전력을 2의 거듭제곱으로 기하급수적으로 배수합니다. 기본값은 0.0입니다.

결과값은 강도에 곱해집니다.

#### intensity *: [float](https://docs.python.org/3/library/functions.html#float)*

빛의 전력을 선형적으로 배수합니다. 기본값은 1.0입니다.

### *class* isaaclab.sim.spawners.lights.DistantLightCfg

장면에 먼 빛(원거리 광원)을 생성하기 위한 구성 매개변수입니다.

먼 빛은 무한히 멀리 있는 광원으로, 평행한 빛 rays를 발산합니다. 이는 태양/달빛 시뮬레이션에 유용합니다. 자세한 내용은
[USDLux DistantLight](https://openusd.org/dev/api/class_usd_lux_distant_light.html) 문서를 참조하십시오.

#### NOTE
기본값은 해당 공식 문서에서 지정된 값입니다.

**속성:**

| [`prim_type`](#isaaclab.sim.spawners.lights.DistantLightCfg.prim_type)                               | 빛 프리임의 프리임 유형 이름입니다.                        |
|------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`angle`](#isaaclab.sim.spawners.lights.DistantLightCfg.angle)                                       | 빛의 각 크기(도 단위).                                      |
| [`visible`](#isaaclab.sim.spawners.lights.DistantLightCfg.visible)                                   | 생성된 애셋이 보일지 여부입니다.                            |
| [`semantic_tags`](#isaaclab.sim.spawners.lights.DistantLightCfg.semantic_tags)                       | 생성된 애셋에 추가할 시맨틱 태그 목록입니다.                  |
| [`copy_from_source`](#isaaclab.sim.spawners.lights.DistantLightCfg.copy_from_source)                 | 소스 프리임에서 애셋을 복사할지 또는 상속할지 여부입니다.      |
| [`color`](#isaaclab.sim.spawners.lights.DistantLightCfg.color)                                       | 에너지-선형 기준으로 표현된 발산 빛의 색입니다.               |
| [`enable_color_temperature`](#isaaclab.sim.spawners.lights.DistantLightCfg.enable_color_temperature) | 색 온도를 활성화합니다.                                        |
| [`color_temperature`](#isaaclab.sim.spawners.lights.DistantLightCfg.color_temperature)               | 흰 점을 나타내는 색 온도(켈빈 단위)입니다.                      |
| [`normalize`](#isaaclab.sim.spawners.lights.DistantLightCfg.normalize)                               | 빛의 표면적에 따라 전력을 정규화합니다.                        |
| [`exposure`](#isaaclab.sim.spawners.lights.DistantLightCfg.exposure)                                 | 빛의 전력을 2의 지수로 기하급수적으로 배율합니다.            |
| [`intensity`](#isaaclab.sim.spawners.lights.DistantLightCfg.intensity)                               | 빛의 전력을 선형으로 배율합니다.                                |

#### prim_type *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

빛 프림의 프림 타입 이름입니다.

#### angle *: [float](https://docs.python.org/3/library/functions.html#float)*

도 단위로 측정한 빛의 각도 크기입니다. 기본값은 0.53도입니다.

예를 들어, 지구에서 볼 때 태양의 각도 크기는 약 0.53도입니다. 값이 높아지면 빛이 넓어지고 결과적으로 그림자 경계가 부드러워집니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 자산이 보이는지 여부를 나타냅니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 자산에 추가할 의미론적 태그 목록입니다. 기본값은 None이며, 이는 의미론적 태그가 추가되지 않음을 의미합니다.

의미론적 태그는 Replicator 의미론적 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 관련된 의미론적 라벨입니다. 예를 들어, 아보카도 클래스의 생성된 자산에 주석을 달려면 의미론적 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미론적 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 초록색의 생성된 자산에 주석을 달려면 의미론적 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미론 필터에 대한 자세한 내용은 [의미론 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 자산을 복사할지 아니면 상속할지 여부를 나타냅니다. 기본값은 True입니다.

이 매개변수는 프림을 복제할 때만 사용됩니다. False이면 자산이 소스 프림에서 상속됩니다. 즉, 소스 프림에 대한 모든 USD 변경 사항이 복제된 프림에 반영됩니다.

#### color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

에너지-선형 기준으로 표현된 방출된 빛의 색입니다. 기본값은 흰색입니다.

#### enable_color_temperature *: [bool](https://docs.python.org/3/library/functions.html#bool)*

색 온도를 활성화합니다. 기본값은 false입니다.

#### color_temperature *: [float](https://docs.python.org/3/library/functions.html#float)*

흰 점을 나타내는 색 온도(켈빈 단위)입니다. 유효 범위는 [1000, 10000]이며 기본값은 6500K입니다.

[색 온도](https://en.wikipedia.org/wiki/Color_temperature)는 빛의 따뜻함 또는 차가움을 나타냅니다. 따뜻한 빛은 색 온도가 낮고, 차가운 빛은 색 온도가 높습니다.

#### NOTE
[`enable_color_temperature`](#isaaclab.sim.spawners.lights.DistantLightCfg.enable_color_temperature)가 true일 때만 효과가 있습니다.

#### normalize *: [bool](https://docs.python.org/3/library/functions.html#bool)*

빛의 표면적에 따라 전력을 정규화합니다. 기본값은 false입니다.

이 옵션을 사용하면 빛의 면적 또는 각도 크기와 무관하게 전력이 일정하게 유지되어 전력과 빛의 모양을 독립적으로 조정하기 쉬워집니다.

#### exposure *: [float](https://docs.python.org/3/library/functions.html#float)*

빛의 전력을 2의 지수로 기하급수적으로 배율합니다. 기본값은 0.0입니다.

결과는 intensity와 곱해집니다.

#### intensity *: [float](https://docs.python.org/3/library/functions.html#float)*

빛의 전력을 선형으로 배율합니다. 기본값은 1.0입니다.

### *class* isaaclab.sim.spawners.lights.DomeLightCfg

장면에 돔 라이트를 생성하기 위한 구성 매개변수입니다.

돔 라이트는 모든 방향에서 내부로 빛을 방출하는 빛 원천입니다. 또한ドーム 라이트에 텍스처를 연결할 수 있으며, 이 텍스처가 빛을 방출하는 데 사용됩니다. 자세한 내용은 [USDLux DomeLight](https://openusd.org/dev/api/class_usd_lux_dome_light.html) 문서를 참조하세요.

#### NOTE
속성의 기본값은 해당 공식 문서에 명시된 값입니다.

**속성:**

| [`prim_type`](#isaaclab.sim.spawners.lights.DomeLightCfg.prim_type)                               | 빛 프림의 프림 타입 이름입니다.                                                                                   |
|---------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| [`texture_file`](#isaaclab.sim.spawners.lights.DomeLightCfg.texture_file)                         | 돔에 사용할 색상 텍스처로, IBL(이미지 기반 조명)을 위한 HDR(고다이나믹 레인지) 텍스처일 수 있습니다. |
| [`visible`](#isaaclab.sim.spawners.lights.DomeLightCfg.visible)                                   | 생성된 자산이 보이는지 여부를 나타냅니다.                                                                             |
| [`semantic_tags`](#isaaclab.sim.spawners.lights.DomeLightCfg.semantic_tags)                       | 생성된 자산에 추가할 의미론적 태그 목록입니다.                                                                       |
| [`copy_from_source`](#isaaclab.sim.spawners.lights.DomeLightCfg.copy_from_source)                 | 소스 프림에서 자산을 복사할지 아니면 상속할지 여부를 나타냅니다.                                                            |
| [`color`](#isaaclab.sim.spawners.lights.DomeLightCfg.color)                                       | 에너지-선형 기준으로 표현된 방출된 빛의 색입니다.                                                                      |
| [`enable_color_temperature`](#isaaclab.sim.spawners.lights.DomeLightCfg.enable_color_temperature) | 색 온도를 활성화합니다.                                                                                               |
| [`color_temperature`](#isaaclab.sim.spawners.lights.DomeLightCfg.color_temperature)               | 흰 점을 나타내는 색 온도(켈빈 단위)입니다.                                                              |
| [`normalize`](#isaaclab.sim.spawners.lights.DomeLightCfg.normalize)                               | 빛의 표면적에 따라 전력을 정규화합니다.                                                                       |
| [`exposure`](#isaaclab.sim.spawners.lights.DomeLightCfg.exposure)                                 | 빛의 전력을 2의 지수로 기하급수적으로 배율합니다.                                                             |
| [`intensity`](#isaaclab.sim.spawners.lights.DomeLightCfg.intensity)                               | 빛의 전력을 선형으로 배율합니다.                                                                                  |
| [`texture_format`](#isaaclab.sim.spawners.lights.DomeLightCfg.texture_format)                     | 색상 맵 파일의 매개변수화 형식입니다.                                                                        |
| [`visible_in_primary_ray`](#isaaclab.sim.spawners.lights.DomeLightCfg.visible_in_primary_ray)     | 주 광선에서 돔 라이트가 보이는지 여부를 나타냅니다.                                                                    |

#### prim_type *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

빛 프림의 프림 타입 이름입니다.

#### texture_file *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

돔에 사용할 색상 텍스처로, IBL(이미지 기반 조명)을 위한 HDR(고다이나믹 레인지) 텍스처일 수 있습니다. 기본값은 None입니다.

None인 경우, 돔은 균일한 색을 발산합니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 자산이 보이는지 여부를 나타냅니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 자산에 추가할 의미론적 태그 목록입니다. 기본값은 None이며, 이는 의미론적 태그가 추가되지 않음을 의미합니다.

의미론적 태그는 Replicator 의미론적 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 관련된 의미론적 라벨입니다. 예를 들어, 아보카도 클래스의 생성된 자산에 주석을 달려면 의미론적 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미론적 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 초록색의 생성된 자산에 주석을 달려면 의미론적 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.
`[("class", "avocado"), ("color", "green")]`.

#### 참고
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 prim에서 자산을 복사하거나 상속받을지 여부를 지정합니다. 기본값은 True입니다.

이 파라미터는 prim을 클론할 때만 사용됩니다. False인 경우, 소스 prim에서 자산을 상속받게 되며, 즉 소스 prim에 대한 모든 USD 변경 사항이 클론된 prim에 반영됩니다.

#### color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

방출된 조명의 색상(에너지 선형 단위). 기본값은 흰색입니다.

#### enable_color_temperature *: [bool](https://docs.python.org/3/library/functions.html#bool)*

색 온도를 활성화합니다. 기본값은 false입니다.

#### color_temperature *: [float](https://docs.python.org/3/library/functions.html#float)*

백색점을 나타내는 색 온도(켈빈). 유효 범위는 [1000, 10000]이며, 기본값은 6500K입니다.

[색 온도](https://en.wikipedia.org/wiki/Color_temperature)는 빛의 따뜻함 또는 차가움을 나타냅니다. 따뜻한 빛은 낮은 색 온도를 가지며, 차가운 빛은 높은 색 온도를 가집니다.

#### 주의
[`enable_color_temperature`](#isaaclab.sim.spawners.lights.DomeLightCfg.enable_color_temperature)가 true일 때만 적용됩니다.

#### normalize *: [bool](https://docs.python.org/3/library/functions.html#bool)*

조명의 표면적에 따라 전력을 정규화합니다. 기본값은 false입니다.

이를 통해 조명의 면적 또는 각도 크기와 무관하게 전력을 일정하게 유지함으로써 전력과 형태를 독립적으로 조정하기 쉽게 만듭니다.

#### exposure *: [float](https://docs.python.org/3/library/functions.html#float)*

2의 거듭제곱으로 조명의 전력을 지수적으로 스케일링합니다. 기본값은 0.0입니다.

결과는 강도와 곱해집니다.

#### intensity *: [float](https://docs.python.org/3/library/functions.html#float)*

조명의 전력을 선형적으로 스케일링합니다. 기본값은 1.0입니다.

#### texture_format *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['automatic', 'latlong', 'mirroredBall', 'angular', 'cubeMapVerticalCross']*

컬러 맵 파일의 매개변수화 형식. 기본값은 “automatic”입니다.

유효한 값은 다음과 같습니다:

* `"automatic"`: 파일 자체에서 레이아웃을 결정하려고 시도합니다. 예를 들어, Renderman 텍스처 파일은 명시적 매개변수를 포함합니다.
* `"latlong"`: X축은 위도, Y축은 경도입니다.
* `"mirroredBall"`: 구에 반사된 환경의 이미지이며, 암시적으로 직교 투영을 사용합니다.
* `"angular"`: mirroredBall과 유사하지만, 반경 차원이 각도에 선형적으로 매핑되어 가장자리에서의 샘플링이 향상됩니다.
* `"cubeMapVerticalCross"`: 세로 십자 형태로 배치된 큐브 맵입니다.

#### visible_in_primary_ray *: [bool](https://docs.python.org/3/library/functions.html#bool)*

주요 광선에서 돔 조명이 보이는지 여부를 지정합니다. 기본값은 True입니다.

참인 경우, 하늘의 텍스처가 보이고, 거짓인 경우 하늘은 검게 보입니다.

### *class* isaaclab.sim.spawners.lights.SphereLightCfg

장면에 구형 조명을 생성하기 위한 구성 파라미터입니다.

구형 조명은 구 바깥쪽으로 빛을 발산하는 조명 소스입니다. 자세한 내용은 다음을 참조하세요:
[USDLux SphereLight](https://openusd.org/dev/api/class_usd_lux_sphere_light.html).

#### 주의
속성의 기본값은 공식 문서에 명시된 값입니다.

**속성:**

| [`visible`](#isaaclab.sim.spawners.lights.SphereLightCfg.visible)                                   | 생성된 자산이 보여야 하는지 여부.                    |
|-----------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`semantic_tags`](#isaaclab.sim.spawners.lights.SphereLightCfg.semantic_tags)                       | 생성된 자산에 추가할 시맨틱 태그 목록.              |
| [`copy_from_source`](#isaaclab.sim.spawners.lights.SphereLightCfg.copy_from_source)                 | 소스 prim에서 자산을 복사하거나 상속받을지 여부.   |
| [`color`](#isaaclab.sim.spawners.lights.SphereLightCfg.color)                                       | 방출된 조명의 색상(에너지 선형 단위).             |
| [`enable_color_temperature`](#isaaclab.sim.spawners.lights.SphereLightCfg.enable_color_temperature) | 색 온도를 활성화.                                      |
| [`color_temperature`](#isaaclab.sim.spawners.lights.SphereLightCfg.color_temperature)               | 백색점을 나타내는 색 온도(켈빈).     |
| [`normalize`](#isaaclab.sim.spawners.lights.SphereLightCfg.normalize)                               | 조명의 표면적에 따라 전력을 정규화.              |
| [`exposure`](#isaaclab.sim.spawners.lights.SphereLightCfg.exposure)                                 | 2의 거듭제곱으로 조명의 전력을 지수적으로 스케일링.    |
| [`intensity`](#isaaclab.sim.spawners.lights.SphereLightCfg.intensity)                               | 조명의 전력을 선형적으로 스케일링.                         |
| [`prim_type`](#isaaclab.sim.spawners.lights.SphereLightCfg.prim_type)                               | 조명 프리밥의 프리밥 타입 이름.                          |
| [`radius`](#isaaclab.sim.spawners.lights.SphereLightCfg.radius)                                     | 구의 반지름.                                           |
| [`treat_as_point`](#isaaclab.sim.spawners.lights.SphereLightCfg.treat_as_point)                     | 구를 점 광원으로 처리(반지름이 0인 구). |

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 자산이 보여야 하는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 자산에 추가할 시맨틱 태그 목록. 기본값은 None이며, 이는 시맨틱 태그가 추가되지 않음을 의미합니다.

시맨틱 태그는 Replicator 시맨틱 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 시맨틱 레이블입니다. 예를 들어, 생성된 자산을 아보카도 클래스로 주석 처리하려면 시맨틱 태그는 `[("class", "avocado")]`가 됩니다.

여러 개의 시맨틱 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 생성된 자산을 아보카도 클래스와 초록색으로 주석 처리하려면 시맨틱 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### 참고
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 prim에서 자산을 복사하거나 상속받을지 여부를 지정합니다. 기본값은 True입니다.

이 파라미터는 prim을 클론할 때만 사용됩니다. False인 경우, 소스 prim에서 자산을 상속받게 되며, 즉 소스 prim에 대한 모든 USD 변경 사항이 클론된 prim에 반영됩니다.

#### color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

방출된 조명의 색상(에너지 선형 단위). 기본값은 흰색입니다.

#### enable_color_temperature *: [bool](https://docs.python.org/3/library/functions.html#bool)*

색 온도를 활성화합니다. 기본값은 false입니다.

#### color_temperature *: [float](https://docs.python.org/3/library/functions.html#float)*

백색점을 나타내는 색 온도(켈빈). 유효 범위는 [1000, 10000]이며, 기본값은 6500K입니다.

[색 온도](https://en.wikipedia.org/wiki/Color_temperature)는 빛의 따뜻함 또는 차가움을 나타냅니다. 따뜻한 빛은 낮은 색 온도를 가지며, 차가운 빛은 높은 색 온도를 가집니다.

#### 주의
[`enable_color_temperature`](#isaaclab.sim.spawners.lights.SphereLightCfg.enable_color_temperature)가 true일 때만 적용됩니다.

#### normalize *: [bool](https://docs.python.org/3/library/functions.html#bool)*

조명의 표면적에 따라 전력을 정규화합니다. 기본값은 false입니다.

이를 통해 조명의 면적 또는 각도 크기와 무관하게 전력을 일정하게 유지함으로써 전력과 형태를 독립적으로 조정하기 쉽게 만듭니다.

#### exposure *: [float](https://docs.python.org/3/library/functions.html#float)*

2의 거듭제곱으로 조명의 전력을 지수적으로 스케일링합니다. 기본값은 0.0입니다.

결과는 강도와 곱해집니다.

#### intensity *: [float](https://docs.python.org/3/library/functions.html#float)*

조명의 전력을 선형적으로 스케일링합니다. 기본값은 1.0입니다.

#### prim_type *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

조명 프리밥의 프리밥 타입 이름.

#### radius *: [float](https://docs.python.org/3/library/functions.html#float)*

구의 반지름. 기본값은 0.5m입니다.

#### treat_as_point *: [bool](https://docs.python.org/3/library/functions.html#bool)*

구를 점 광원으로 처리(반지름이 0인 구). 기본값은 false입니다.

## 센서
</translate>

센서 스포너의 하위 모듈로, 시뮬레이션에서 센서를 생성하는 데 사용됩니다.

현재 다음 센서가 지원됩니다:

* 카메라: 핀홀 또는 어안 투영을 위한 설정이 있는 USD 카메라 프리임.

### 클래스

| [`PinholeCameraCfg`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg)   | 핀홀 카메라 설정이 있는 USD 카메라 프리임에 대한 구성 매개변수.                                                |
|-------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| [`FisheyeCameraCfg`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg)   | 어안 카메라 설정이 있는 USD 카메라 프리임에 대한 구성 매개변수. ([어안 렌즈](https://en.wikipedia.org/wiki/Fisheye_lens) 참조) |

### isaaclab.sim.spawners.sensors.spawn_camera(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [sensors_cfg.PinholeCameraCfg](#isaaclab.sim.spawners.sensors.PinholeCameraCfg) | [sensors_cfg.FisheyeCameraCfg](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

주어진 투영 유형으로 USD 카메라 프리임을 생성합니다.

이 함수는 카메라의 속성을 지정하는 다양한 속성을 카메라 프리임에 생성합니다.
이러한 속성은 나중에 `omni.replicator.core`에서 주어진 카메라로 장면을 렌더링하는 데 사용됩니다.

#### NOTE
이 함수는 `clone()` 데코레이터로 장식되어 있으며, 입력된 프리임 경로가 정규 표현식 패턴인 경우 프리임 경로를 경로 목록으로 해석합니다.
이를 통해 단일 경로 표현식으로 여러 자산을 생성하고 지정된 경로 표현식의 USD 프리임을 복제할 수 있습니다.

* **매개변수:**
  * **prim_path** – 자산을 생성할 프리임 경로 또는 패턴. 프리임 경로가 정규 표현식 패턴인 경우, 일치하는 모든 프리임 경로에 자산이 생성됩니다.
  * **cfg** – 구성 인스턴스.
  * **translation** – 부모 프리임 상대적으로 프리임에 적용할 변환. 기본값은 None이며, 이 경우 원점으로 설정됩니다.
  * **orientation** – 부모 프리임 상대적으로 프리임에 적용할 방향(w, x, y, z 순서). 기본값은 None이며, 이 경우 단위 방향(identity)으로 설정됩니다.
  * **\*\*kwargs** – 추가 키워드 인수, 예: `clone_in_fabric`.
* **반환값:**
  생성된 프리임.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 주어진 경로에 이미 프리임이 존재하는 경우.

### *class* isaaclab.sim.spawners.sensors.PinholeCameraCfg

핀홀 카메라 설정이 있는 USD 카메라 프리임에 대한 구성 매개변수.

매개변수에 대한 자세한 내용은 [카메라 문서](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/cameras.html)를 참조하세요.

..note ::
: 초점 거리뿐만 아니라 조리개 크기와 오프셋도 세계 단위의 십분의 일로 설정됩니다.この場合、世界単位はメートルであり、これらの値はすべてセンチメートルで設定されています。

**속성:**

| [`projection_type`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.projection_type)                       | 카메라에 사용할 투영 유형.                     |
|------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`clipping_range`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.clipping_range)                         | 근거리 및 원거리 클리핑 거리(단위: m).                       |
| [`focal_length`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.focal_length)                             | 원근 초점 거리(단위: cm).                             |
| [`focus_distance`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.focus_distance)                         | 카메라에서 초점 평면까지의 거리(단위: m).           |
| [`f_stop`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.f_stop)                                         | 렌즈 조리개.                                                |
| [`horizontal_aperture`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.horizontal_aperture)               | 수평 조리개(단위: cm).                                  |
| [`vertical_aperture`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.vertical_aperture)                   | 수직 조리개(단위: mm).                                    |
| [`horizontal_aperture_offset`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.horizontal_aperture_offset) | 해상도/필름 게이트의 수평 오프셋.                    |
| [`vertical_aperture_offset`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.vertical_aperture_offset)     | 해상도/필름 게이트의 수직 오프셋.                      |
| [`lock_camera`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.lock_camera)                               | 옴니버스 뷰포트에서 카메라 고정 여부.                   |
| [`visible`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.visible)                                       | 생성된 자산이 보이는지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.semantic_tags)                           | 생성된 자산에 추가할 의미 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.copy_from_source)                     | 소스 프리임에서 자산을 복사할지, 상속받을지 여부. |

**메서드:**

| [`from_intrinsic_matrix`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg.from_intrinsic_matrix)(intrinsic_matrix, ...)   | 내재 행렬로부터 [`PinholeCameraCfg`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg) 클래스 인스턴스를 생성합니다.   |
|---------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|

#### projection_type *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

카메라에 사용할 투영 유형. 기본값은 “pinhole”.

#### NOTE
현재 “pinhole”만 지원됩니다.

#### clipping_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

근거리 및 원거리 클리핑 거리(단위: m). 기본값은 (0.01, 1e6).

최소 클리핑 범위는 지정된 거리만큼 카메라를 앞으로 이동시킵니다. 거리 관련 데이터 유형(예: `distance_to_image_plane`)의 문제를 방지하기 위해 너무 높게 설정하지 마세요.

#### focal_length *: [float](https://docs.python.org/3/library/functions.html#float)*

원근 초점 거리(단위: cm). 기본값은 24.0cm.

렌즈 길이가 길면 시야각(FOV)이 좁아지고, 짧으면 넓어집니다.

#### focus_distance *: [float](https://docs.python.org/3/library/functions.html#float)*

카메라에서 초점 평면까지의 거리(단위: m). 기본값은 400.0.

완벽한 선명도가 달성되는 거리입니다.

#### f_stop *: [float](https://docs.python.org/3/library/functions.html#float)*

렌즈 조리개. 기본값은 0.0이며, 이 경우 초점 기능이 비활성화됩니다.

거리 흐림을 제어합니다. 숫자가 낮을수록 초점 범위가 줄어들고, 숫자가 높을수록 초점 범위가 늘어납니다.

#### horizontal_aperture *: [float](https://docs.python.org/3/library/functions.html#float)*

수평 조리개(단위: cm). 기본값은 20.955 cm.

카메라의 센서/필름 폭을 에뮬레이트합니다.

#### NOTE
기본값은 35mm 구형 프로젝터의 수평 조리개입니다.

#### vertical_aperture *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

수직 조리개(단위: mm). 기본값은 None.

카메라의 센서/필름 높이를 에뮬레이트합니다. None인 경우, 수평 조리개와 이미지의 가로세로 비율을 기반으로 수직 조리개가 계산되어 정사각형 픽셀을 유지합니다. 계산식은 다음과 같습니다:

$$
\text{수직 조리개} = \text{수평 조리개} \times \frac{\text{높이}}{\text{너비}}

$$

#### horizontal_aperture_offset *: [float](https://docs.python.org/3/library/functions.html#float)*

해상도/필름 게이트의 수평 오프셋. 기본값은 0.0.

#### vertical_aperture_offset *: [float](https://docs.python.org/3/library/functions.html#float)*

해상도/필름 게이트의 수직 오프셋. 기본값은 0.0.

#### lock_camera *: [bool](https://docs.python.org/3/library/functions.html#bool)*

옴니버스 뷰포트에서 카메라 고정 여부. 기본값은 True.

True인 경우, 카메라는 구성된 변환에 고정되어 GUI를 통해 카메라 출력을 볼 때 의도치 않게 카메라를 움직이는 것을 방지합니다.

#### *classmethod* from_intrinsic_matrix(intrinsic_matrix: [list](https://docs.python.org/3/library/stdtypes.html#list)[[float](https://docs.python.org/3/library/functions.html#float)], width: [int](https://docs.python.org/3/library/functions.html#int), height: [int](https://docs.python.org/3/library/functions.html#int), clipping_range: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] = (0.01, 1000000.0), focal_length: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, focus_distance: [float](https://docs.python.org/3/library/functions.html#float) = 400.0, f_stop: [float](https://docs.python.org/3/library/functions.html#float) = 0.0, projection_type: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'pinhole', lock_camera: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [PinholeCameraCfg](#isaaclab.sim.spawners.sensors.PinholeCameraCfg)

인트린직 행렬에서 [`PinholeCameraCfg`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg) 클래스 인스턴스를 생성합니다.

인트린직 행렬은 3D 월드 좌표와 2D 이미지 간의 매핑을 정의하는 3x3 행렬입니다. 행렬은 다음과 같이 정의됩니다:

$$
I_{cam} = \begin{bmatrix}
f_x & 0 & c_x \\
0 & f_y & c_y \\
0 & 0 & 1
\\end{bmatrix},

$$

여기서 $f_x$와 $f_y$는 각각 x 및 y 방향으로의 초점 거리를 나타내며, $c_x$와 $c_y$는 각각 x 및 y 방향으로의 원점 오프셋을 나타냅니다.

* **매개변수:**
  * **intrinsic_matrix** – 카메라의 인트린직 행렬(row-major 형식). 행렬은 [f_x, 0, c_x, 0, f_y, c_y, 0, 0, 1]로 정의됩니다. 형태는 (9,).
  * **width** – 이미지의 너비(픽셀 단위).
  * **height** – 이미지의 높이(픽셀 단위).
  * **clipping_range** – 근거리 및 원거리 클리핑 거리(m). 기본값은 (0.01, 1e6)입니다.
  * **focal_length** – 픽셀 크기를 계산하기 위해 사용되는 원근 초점 길이(cm). 기본값은 None입니다. None인 경우 focal_length은 1 / width로 계산됩니다.
  * **focus_distance** – 카메라에서 초점 평면까지의 거리(m). 기본값은 400.0 m입니다.
  * **f_stop** – 렌즈 조리개. 기본값은 0.0이며, 이는 초점 기능을 비활성화합니다.
  * **projection_type** – 카메라에 사용할 투영 유형. 기본값은 “pinhole”입니다.
  * **lock_camera** – 옴니버스 뷰포트에서 카메라를 잠금. 기본값은 True입니다.
* **반환값:**
  [`PinholeCameraCfg`](#isaaclab.sim.spawners.sensors.PinholeCameraCfg) 클래스의 인스턴스.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 보이는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 시맨틱 태그 목록. 기본값은 None으로, 시맨틱 태그가 추가되지 않음을 의미합니다.

시맨틱 태그는 Replicator 시맨틱 태그 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 시맨틱 라벨입니다. 예를 들어, 생성된 에셋을 아보카도 클래스로 주석 처리하려면 시맨틱 태그는 `[("class", "avocado")]`가 됩니다.

여러 시맨틱 태그를 지정하려면 태그 목록을 전달할 수 있습니다. 예를 들어, 생성된 에셋을 아보카도 클래스와 녹색 색상으로 주석 처리하려면 시맨틱 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 에셋을 복사할지 여부 또는 상속할지 여부. 기본값은 True입니다.

이 매개변수는 프림을 클론할 때만 사용됩니다. False인 경우, 에셋은 소스 프림에서 상속됩니다. 즉, 소스 프림에 대한 모든 USD 변경 사항이 클론된 프림에 반영됩니다.

### *class* isaaclab.sim.spawners.sensors.FisheyeCameraCfg

[물안경 렌즈](https://en.wikipedia.org/wiki/Fisheye_lens) 설정을 갖는 USD 카메라 프림에 대한 구성 파라미터.

파라미터에 대한 자세한 내용은
[camera 문서](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/cameras.html#fisheye-properties)를 참조하세요.

#### NOTE
기본값은 [Replicator 카메라](https://docs.omniverse.nvidia.com/py/replicator/1.12.16/source/extensions/omni.replicator.core/docs/API.html#cameras) 함수에서 가져온 것입니다.

**속성:**

| [`projection_type`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.projection_type)                       | 카메라에 사용할 투영 유형.                             |
|------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| [`visible`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.visible)                                       | 생성된 에셋이 보이는지 여부.                          |
| [`semantic_tags`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.semantic_tags)                           | 생성된 에셋에 추가할 시맨틱 태그 목록.                    |
| [`copy_from_source`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.copy_from_source)                     | 소스 프림에서 에셋을 복사할지 여부 또는 상속할지 여부.         |
| [`clipping_range`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.clipping_range)                         | 근거리 및 원거리 클리핑 거리(m).                               |
| [`focal_length`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.focal_length)                             | 원근 초점 길이(cm).                                     |
| [`focus_distance`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.focus_distance)                         | 카메라에서 초점 평면까지의 거리(m).                   |
| [`f_stop`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.f_stop)                                         | 렌즈 조리개.                                                        |
| [`horizontal_aperture`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.horizontal_aperture)               | 수평 조리개(cm).                                          |
| [`vertical_aperture`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.vertical_aperture)                   | 수직 조리개(mm).                                            |
| [`horizontal_aperture_offset`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.horizontal_aperture_offset) | 해상도/필름 게이트의 수평 오프셋.                            |
| [`vertical_aperture_offset`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.vertical_aperture_offset)     | 해상도/필름 게이트의 수직 오프셋.                              |
| [`lock_camera`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.lock_camera)                               | 옴니버스 뷰포트에서 카메라를 잠금.                           |
| [`fisheye_nominal_width`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_nominal_width)           | 물안경 렌즈 모델의 명목상 너비(픽셀).                      |
| [`fisheye_nominal_height`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_nominal_height)         | 물안경 렌즈 모델의 명목상 높이(픽셀).                     |
| [`fisheye_optical_centre_x`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_optical_centre_x)     | 물안경 렌즈 모델의 수평 광학적 중심 위치(픽셀). |
| [`fisheye_optical_centre_y`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_optical_centre_y)     | 물안경 렌즈 모델의 수직 광학적 중심 위치(픽셀).   |
| [`fisheye_max_fov`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_max_fov)                       | 물안경 렌즈 모델의 최대 시야 각도(도).             |
| [`fisheye_polynomial_a`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_polynomial_a)             | 물안경 다항식의 첫 번째 구성 요소.                                |
| [`fisheye_polynomial_b`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_polynomial_b)             | 물안경 다항식의 두 번째 구성 요소.                                |
| [`fisheye_polynomial_c`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_polynomial_c)             | 물안경 다항식의 세 번째 구성 요소.                                |
| [`fisheye_polynomial_d`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_polynomial_d)             | 물안경 다항식의 네 번째 구성 요소.                                |
| [`fisheye_polynomial_e`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_polynomial_e)             | 물안경 다항식의 다섯 번째 구성 요소.                                |
| [`fisheye_polynomial_f`](#isaaclab.sim.spawners.sensors.FisheyeCameraCfg.fisheye_polynomial_f)             | 물안경 다항식의 여섯 번째 구성 요소.                                |

#### projection_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['fisheyePolynomial', 'fisheyeSpherical', 'fisheyeKannalaBrandtK3', 'fisheyeRadTanThinPrism', 'omniDirectionalStereo']*

카메라에 사용할 투영 유형. 기본값은 “fisheyePolynomial”입니다.

사용 가능한 옵션:

- `"fisheyePolynomial"`: $360^{\circ}$ 구형 투영을 갖는 물안경 카메라 모델.
- `"fisheyeSpherical"`: $360^{\circ}$ 전체 프레임 투영을 갖는 물안경 카메라 모델.
- `"fisheyeKannalaBrandtK3"`: Kannala-Brandt K3 왜곡 모델을 사용하는 어안 카메라 모델.
- `"fisheyeRadTanThinPrism"`: 방사 및 접선 왜곡을 결합한 어안 카메라 모델.
- `"omniDirectionalStereo"`: 360° 스테레오스코픽 이미징을 지원하는 어안 카메라 모델.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 애셋이 표시되어야 하는지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 애셋에 추가할 의미 태그 목록입니다. 기본값은 None으로, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 의미 라벨입니다. 예를 들어, 아보카도 클래스의 생성된 애셋에 주석을 달려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 녹색 색상의 생성된 애셋에 주석을 달려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### 참고 사항
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 애셋을 복사할지 아니면 상속할지 여부입니다. 기본값은 True입니다.

이 매개변수는 프림을 클론할 때만 사용됩니다. False인 경우, 소스 프림에서 애셋을 상속받게 되며, 즉 소스 프림의 모든 USD 변경 사항이 클론된 프림에 반영됩니다.

#### clipping_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

근거리 및 원거리 클리핑 거리(단위: m). 기본값은 (0.01, 1e6)입니다.

최소 클리핑 범위는 지정된 거리만큼 카메라를 앞으로 이동시킵니다. 거리 관련 데이터 유형(예: `distance_to_image_plane`)의 문제를 방지하기 위해 너무 높은 값으로 설정하지 마세요.

#### focal_length *: [float](https://docs.python.org/3/library/functions.html#float)*

원근 초점 거리(단위: cm). 기본값은 24.0cm입니다.

렌즈 길이가 길수록 시야각이 좁아지고, 짧을수록 시야각이 넓어집니다.

#### focus_distance *: [float](https://docs.python.org/3/library/functions.html#float)*

카메라에서 초점 평면까지의 거리(단위: m). 기본값은 400.0입니다.

완전한 선명도가 달성되는 거리입니다.

#### f_stop *: [float](https://docs.python.org/3/library/functions.html#float)*

렌즈 조리개. 기본값은 0.0이며, 이로 인해 초점 기능이 꺼집니다.

거리 흐림을 제어합니다. 숫자가 작을수록 초점 범위가 좁아지고, 클수록 초점 범위가 넓어집니다.

#### horizontal_aperture *: [float](https://docs.python.org/3/library/functions.html#float)*

수평 조리개(단위: cm). 기본값은 20.955 cm입니다.

카메라의 센서/필름 폭을 에뮬레이트합니다.

#### 참고 사항
기본값은 35mm 구형 프로젝터의 수평 조리개입니다.

#### vertical_aperture *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

수직 조리개(단위: mm). 기본값은 None입니다.

카메라의 센서/필름 높이를 에뮬레이트합니다. None인 경우, 수평 조리개와 이미지의 가로세로 비율을 기반으로 수직 조리개가 계산되어 정사각형 픽셀이 유지됩니다. 이는 다음과 같이 계산됩니다:

$$
\text{수직 조리개} = \text{수평 조리개} \times \frac{\text{높이}}{\text{너비}}

$$

#### horizontal_aperture_offset *: [float](https://docs.python.org/3/library/functions.html#float)*

해상도/필름 게이트를 수평으로 오프셋합니다. 기본값은 0.0입니다.

#### vertical_aperture_offset *: [float](https://docs.python.org/3/library/functions.html#float)*

해상도/필름 게이트를 수직으로 오프셋합니다. 기본값은 0.0입니다.

#### lock_camera *: [bool](https://docs.python.org/3/library/functions.html#bool)*

옴니버스 뷰포트에서 카메라를 잠급니다. 기본값은 True입니다.

True인 경우, 카메라는 구성된 변환에서 고정된 상태를 유지합니다. 이는 GUI를 통해 카메라 출력을 보고 싶지만, GUI 상호작용으로 인해 카메라가 의도치 않게 움직이는 것을 방지하고자 할 때 유용합니다.

#### fisheye_nominal_width *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 렌즈 모델의 명목 가로 길이(단위: 픽셀). 기본값은 1936.0입니다.

#### fisheye_nominal_height *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 렌즈 모델의 명목 세로 길이(단위: 픽셀). 기본값은 1216.0입니다.

#### fisheye_optical_centre_x *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 렌즈 모델의 수평 광학 중심 위치(단위: 픽셀). 기본값은 970.94244입니다.

#### fisheye_optical_centre_y *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 렌즈 모델의 수직 광학 중심 위치(단위: 픽셀). 기본값은 600.37482입니다.

#### fisheye_max_fov *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 렌즈 모델의 최대 시야각(단위: 도). 기본값은 200.0도입니다.

#### fisheye_polynomial_a *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 다항식의 첫 번째 구성 요소. 기본값은 0.0입니다.

#### fisheye_polynomial_b *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 다항식의 두 번째 구성 요소. 기본값은 0.00245입니다.

#### fisheye_polynomial_c *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 다항식의 세 번째 구성 요소. 기본값은 0.0입니다.

#### fisheye_polynomial_d *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 다항식의 네 번째 구성 요소. 기본값은 0.0입니다.

#### fisheye_polynomial_e *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 다항식의 다섯 번째 구성 요소. 기본값은 0.0입니다.

#### fisheye_polynomial_f *: [float](https://docs.python.org/3/library/functions.html#float)*

어안 다항식의 여섯 번째 구성 요소. 기본값은 0.0입니다.

## 파일에서

파일에서 애셋을 생성하는 스포너를 위한 하위 모듈입니다.

현재 다음과 같은 스포너가 지원됩니다:

* [`UsdFileCfg`](#isaaclab.sim.spawners.from_files.UsdFileCfg): USD 파일에서 애셋을 생성합니다.
* [`UrdfFileCfg`](#isaaclab.sim.spawners.from_files.UrdfFileCfg): URDF 파일에서 애셋을 생성합니다.
* [`GroundPlaneCfg`](#isaaclab.sim.spawners.from_files.GroundPlaneCfg): 그리드월드 USD 파일을 사용하여 지면 평면을 생성합니다.

### 클래스

| [`UrdfFileCfg`](#isaaclab.sim.spawners.from_files.UrdfFileCfg)       | 애셋을 생성할 URDF 파일.   |
|----------------------------------------------------------------------|----------------------------------|
| [`UsdFileCfg`](#isaaclab.sim.spawners.from_files.UsdFileCfg)         | 애셋을 생성할 USD 파일.    |
| [`GroundPlaneCfg`](#isaaclab.sim.spawners.from_files.GroundPlaneCfg) | 지면 평면 프림을 생성합니다.      |

### isaaclab.sim.spawners.from_files.spawn_from_urdf(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [from_files_cfg.UrdfFileCfg](#isaaclab.sim.spawners.from_files.UrdfFileCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

URDF 파일에서 애셋을 생성하고 주어진 구성으로 설정을 재정의합니다.

이 함수는 `UrdfConverter` 클래스를 사용하여 URDF에서 USD 파일을 생성합니다. 이 파일은 지정된 프림 경로로 가져옵니다.

지정된 프림 경로에 이미 프림이 존재하는 경우, 새 프림을 생성하거나 프림이 이미 존재한다는 오류를 발생시키지 않습니다. 대신 기존 프림을 사용하고 주어진 구성으로 설정을 재정의합니다.

#### 참고 사항
이 함수는 `clone()`로 장식되어 있으며, 입력 프림 경로가 정규 표현식 패턴인 경우 프림 경로를 경로 목록으로 해석합니다. 이는 단일 경로 표현식에서 여러 애셋을 생성하고 지정된 경로 표현식에서 USD 프림을 클론하는 데 사용됩니다.

* **매개변수:**
  * **prim_path** – 애셋을 생성할 프림 경로 또는 패턴입니다. 프림 경로가 정규 표현식 패턴인 경우, 일치하는 모든 프림 경로에 애셋이 생성됩니다.
  * **cfg** – 구성 인스턴스.
  * **translation** – 부모 프림을 기준으로 프림에 적용할 변환입니다. 기본값은 None이며,この場合 생성된 USD 파일에 지정된 변환이 사용됩니다.
  * **orientation** – 부모 프림을 기준으로 프림에 적용할 (w, x, y, z) 방향입니다. 기본값은 None이며,この場合 생성된 USD 파일에 지정된 방향이 사용됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수.
* **반환 값:**
  생성된 애셋의 프림.
* **예외 발생:**
  [**FileNotFoundError**](https://docs.python.org/3/library/exceptions.html#FileNotFoundError) – 지정된 경로에 URDF 파일이 존재하지 않는 경우.

### *class* isaaclab.sim.spawners.from_files.UrdfFileCfg

URDF 파일에서 애셋을 생성합니다.

It uses the `UrdfConverter` class to create a USD file from URDF and spawns the imported
USD file. Similar to the [`UsdFileCfg`](#isaaclab.sim.spawners.from_files.UsdFileCfg), the generated USD file can be modified by specifying
the respective properties in the configuration class.

See [`spawn_from_urdf()`](#isaaclab.sim.spawners.from_files.spawn_from_urdf) for more information.

#### NOTE
The configuration parameters include various properties. If not None, these properties
are modified on the spawned prim in a nested manner.

If they are set to a value, then the properties are modified on the spawned prim in a nested manner.
This is done by calling the respective function with the specified properties.

**Attributes:**

| [`asset_path`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.asset_path)                                                       | 변환할 USD 파일의 절대 경로를 지정하는 자산 파일 경로입니다.                                     |
|--------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| [`usd_dir`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.usd_dir)                                                             | 생성된 USD 파일을 저장할 출력 디렉터리 경로입니다.                                                 |
| [`usd_file_name`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.usd_file_name)                                                 | 생성된 USD 파일의 이름입니다.                                                                  |
| [`force_usd_conversion`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.force_usd_conversion)                                   | 자산 파일을 USD로 강제로 변환할지 여부를 지정합니다.                                               |
| [`make_instanceable`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.make_instanceable)                                         | 생성된 USD 파일을 인스턴스 가능하도록 설정합니다.                                                    |
| [`fix_base`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.fix_base)                                                           | 루트/베이스 링크에 고정 조인트를 생성합니다.                                                     |
| [`root_link_name`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.root_link_name)                                               | 루트 링크의 이름입니다.                                                                        |
| [`link_density`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.link_density)                                                   | URDF에서 `"inertial"` 속성이 누락된 링크에 대한 기본 밀도(`kg/m^3`)입니다.                      |
| [`merge_fixed_joints`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.merge_fixed_joints)                                       | 고정 조인트로 연결된 링크를 병합합니다.                                                        |
| [`convert_mimic_joints_to_normal_joints`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.convert_mimic_joints_to_normal_joints) | 모방 조인트를 일반 조인트로 변환합니다.                                                          |
| [`joint_drive`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.joint_drive)                                                     | 조인트 드라이브 설정입니다.                                                                    |
| [`collider_type`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.collider_type)                                                 | 충돌 형태 간소화 유형입니다.                                                                     |
| [`self_collision`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.self_collision)                                               | 어트리뷰트의 링크 간 자체 충돌을 활성화합니다.                                                     |
| [`replace_cylinders_with_capsules`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.replace_cylinders_with_capsules)             | 실린더 형태를 캡슐 형태로 대체합니다.                                                            |
| [`collision_from_visuals`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.collision_from_visuals)                               | 시각 기하학에서 충돌 기하학을 생성할지 여부를 지정합니다.                                          |
| [`visible`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.visible)                                                             | 스폰된 자산이 보이는지 여부를 지정합니다.                                                         |
| [`semantic_tags`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.semantic_tags)                                                 | 스폰된 자산에 추가할 시맨틱 태그 목록입니다.                                                       |
| [`copy_from_source`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.copy_from_source)                                           | 소스 프리머에서 자산을 복사할지 아니면 상속받을지 여부를 지정합니다.                                |
| [`mass_props`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.mass_props)                                                       | 질량 속성입니다.                                                                                 |
| [`deformable_props`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.deformable_props)                                           | 변형 가능한 바디 속성입니다.                                                                     |
| [`rigid_props`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.rigid_props)                                                     | 강체 바디 속성입니다.                                                                            |
| [`collision_props`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.collision_props)                                             | 모든 충돌 메시지에 적용할 속성입니다.                                                            |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.activate_contact_sensors)                           | 모든 강체에 대한 접촉 보고를 활성화합니다.                                                         |
| [`scale`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.scale)                                                                 | 자산의 스케일입니다.                                                                             |
| [`articulation_props`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.articulation_props)                                       | 어트리뷰션 루트에 적용할 속성입니다.                                                             |
| [`fixed_tendons_props`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.fixed_tendons_props)                                     | 고정 테넌드(있는 경우)에 적용할 속성입니다.                                                        |
| [`spatial_tendons_props`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.spatial_tendons_props)                                 | 공간 테넌드(있는 경우)에 적용할 속성입니다.                                                        |
| [`joint_drive_props`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.joint_drive_props)                                         | 조인트에 적용할 속성입니다.                                                                      |
| [`visual_material_path`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.visual_material_path)                                   | 프리머에 사용할 시각적 재료의 경로입니다.                                                          |
| [`visual_material`](#isaaclab.sim.spawners.from_files.UrdfFileCfg.visual_material)                                             | URDF 파일의 시각적 재료 속성을 재정의하는 시각적 재료 속성입니다.                                  |

#### asset_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

변환할 USD 파일의 절대 경로를 지정하는 자산 파일 경로입니다.

#### usd_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 USD 파일을 저장할 출력 디렉터리 경로입니다. 기본값은 None입니다.

None인 경우, `/tmp/IsaacLab/usd_{date}_{time}_{random}`로 해석되며, 여기서 중괄호 안의 매개변수는 런타임에 생성됩니다.

#### usd_file_name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 USD 파일의 이름입니다. 기본값은 None입니다.

None인 경우, 자산 파일 이름에서 해결됩니다. 예를 들어, 자산 파일 이름이 `"my_asset.urdf"`이면, 생성된 USD 파일 이름은 `"my_asset.usd"`가 됩니다.

제공된 파일 이름이 “.usd” 또는 “.usda”로 끝나지 않는 경우, 파일 이름에 “.usd” 확장자가 추가됩니다.

#### force_usd_conversion *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산 파일을 USD로 강제로 변환할지 여부를 지정합니다. 기본값은 False입니다.

True인 경우, USD 파일이 항상 생성되며, 기존 USD 파일이 존재하면 덮어씁니다.

#### make_instanceable *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 USD 파일을 인스턴스 가능하도록 설정합니다. 기본값은 True입니다.

#### NOTE
인스턴싱은 장면에서 자산의 여러 복사본이 사용될 때 메모리 사용량을 줄이는 데 도움이 됩니다. 자세한 내용은 USD 문서에서
[scene-graph instancing](https://openusd.org/dev/api/_usd__page__scenegraph_instancing.html)를 참조하십시오.

#### fix_base *: [bool](https://docs.python.org/3/library/functions.html#bool)*

루트/베이스 링크에 고정 조인트를 생성합니다.

#### root_link_name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

루트 링크의 이름입니다. 기본값은 None입니다.

None인 경우, 루트 링크는 PhysX에 의해 설정됩니다.

#### link_density *: [float](https://docs.python.org/3/library/functions.html#float)*

URDF에서 `"inertial"` 속성이 누락된 링크에 대한 기본 밀도(`kg/m^3`)입니다. 기본값은 0.0입니다.

#### merge_fixed_joints *: [bool](https://docs.python.org/3/library/functions.html#bool)*

고정 조인트에 의해 연결된 링크를 통합합니다. 기본값은 True입니다.

#### convert_mimic_joints_to_normal_joints *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모방 조인트를 일반 조인트로 변환합니다. 기본값은 False입니다.

#### joint_drive *: [JointDriveCfg](isaaclab.sim.converters.md#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

조인트 드라이브 설정입니다. 기본값은 `JointDriveCfg`입니다.

이 매개변수는 조인트가 없는 URDF의 경우 `None`으로 설정할 수 있습니다.

#### collider_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['convex_hull', 'convex_decomposition']*

충돌 형태 단순화입니다. 기본값은 “convex_hull”입니다.

지원되는 값은 다음과 같습니다:

* `"convex_hull"`: 충돌 형태가 볼록 껍질로 단순화됩니다.
* `"convex_decomposition"`: 충돌 형태가 더 잘 맞는 작은 볼록 형태로 분해됩니다.

#### self_collision *: [bool](https://docs.python.org/3/library/functions.html#bool)*

아티큘레이션의 링크 간 자체 충돌을 활성화합니다. 기본값은 False입니다.

#### replace_cylinders_with_capsules *: [bool](https://docs.python.org/3/library/functions.html#bool)*

실린더 형태를 캡슐 형태로 대체합니다. 기본값은 False입니다.

#### collision_from_visuals *: [bool](https://docs.python.org/3/library/functions.html#bool)*

시각 기하학에서 충돌 기하학을 생성할지 여부를 나타냅니다. 기본값은 False입니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 자산이 보이는지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 자산에 추가할 의미적 태그 목록입니다. 기본값은 None으로, 의미적 태그가 추가되지 않음을 의미합니다.

의미적 태그는 Replicator 의미적 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 종류이고 `data`는 태그와 연결된 의미적 라벨입니다. 예를 들어, 생성된 자산을 아보카도 클래스에 주석으로 표시하려면 의미적 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미적 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 생성된 자산을 아보카도 클래스와 녹색 색상에 주석으로 표시하려면 의미적 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 자산을 복사할지, 아니면 상속받을지를 나타냅니다. 기본값은 True입니다.

이 매개변수는 프림을 복제할 때만 사용됩니다. False인 경우, 자산은 소스 프림에서 상속받으며, 즉 소스 프림에 대한 모든 USD 변경 사항이 복제된 프림에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 몸체 속성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 속성.

강체 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정하십시오. 이렇게 하면 객체가 정적이 되고 중력이나 다른 힘의 영향을 받지 않습니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 대한 접촉 보고를 활성화합니다. 기본값은 False입니다.

이 옵션은 주어진 프림 경로 및 해당 자식의 모든 강체에 PhysxContactReporter API를 추가합니다.

#### scale *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

자산의 스케일입니다. 기본값은 None이며, 이 경우 스케일이 수정되지 않습니다.

#### articulation_props *: [schemas.ArticulationRootPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.ArticulationRootPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

아티큘레이션 루트에 적용할 속성입니다.

#### fixed_tendons_props *: [schemas.FixedTendonPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.FixedTendonPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

고정 테인드(있는 경우)에 적용할 속성입니다.

#### spatial_tendons_props *: schemas.SpatialTendonPropertiesCfg | [None](https://docs.python.org/3/library/constants.html#None)*

공간 테인드(있는 경우)에 적용할 속성입니다.

#### joint_drive_props *: [schemas.JointDrivePropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.JointDrivePropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

조인트에 적용할 속성입니다.

#### NOTE
조인트 드라이브 속성은 자산의 모든 조인트 드라이브의 USD 속성을 설정합니다. 이 속성은 가능하면 적게 사용하고 꼭 필요한 경우에만 사용하는 것이 좋습니다. 대신, [`actuators`](isaaclab.assets.md#isaaclab.assets.ArticulationCfg.actuators) 매개변수를 사용하여 어티큘레이션의 특정 조인트에 대한 조인트 드라이브 속성을 설정하는 것이 좋습니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프림에 사용할 시각적 물질의 경로입니다. 기본값은 “material”입니다.

경로가 상대적인 경우, 프림의 경로를 기준으로 해석됩니다. 이 매개변수는 visual_material이 None이 아닌 경우 무시됩니다.

#### visual_material *: [materials.VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

URDF 파일의 시각적 물질 속성을 재정의하는 시각적 물질 속성입니다.

#### NOTE
None인 경우, 시각적 물질이 추가되지 않습니다.

### isaaclab.sim.spawners.from_files.spawn_from_usd(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [from_files_cfg.UsdFileCfg](#isaaclab.sim.spawners.from_files.UsdFileCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

USD 파일에서 자산을 생성하고 주어진 설정으로 설정을 재정의합니다.

USD 파일의 경우, 자산은 USD 파일에 지정된 기본 프림에서 생성됩니다. 기본 프림이 지정되지 않은 경우, 자산은 루트 프림에서 생성됩니다.

주어진 프림 경로에 이미 프림이 존재하는 경우, 함수는 새 프림을 생성하거나 프림이 이미 존재한다는 오류를 발생시키지 않습니다. 대신, 기존 프림을 가져와 주어진 설정으로 설정을 재정의합니다.

#### NOTE
이 함수는 `clone()` 데코레이터로 장식되어 있으며, 입력 프림 경로가 정규 표현식 패턴인 경우 프림 경로를 경로 목록으로 변환합니다. 이는 단일 프림 경로 표현식에서 여러 자산을 생성하고 해당 경로 표현식의 USD 프림을 복제하는 것을 지원하기 위해 수행됩니다.

* **매개변수:**
  * **prim_path** – 자산을 생성할 프림 경로 또는 패턴입니다. 프림 경로가 정규 표현식 패턴인 경우, 자산은 모든 일치하는 프림 경로에서 생성됩니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 프림 상대의 프림에 적용할 평행 이동입니다. 기본값은 None이며, 이 경우 USD 파일에 지정된 평행 이동이 사용됩니다.
  * **orientation** – 부모 프림 상대의 프림에 적용할 (w, x, y, z) 방향입니다. 기본값은 None이며, 이 경우 USD 파일에 지정된 방향이 사용됩니다.
  * **\*\*kwargs** – 추가 키워드 인수, 예를 들어 `clone_in_fabric`.
* **반환값:**
  생성된 자산의 프림입니다.
* **예외:**
  [**FileNotFoundError**](https://docs.python.org/3/library/exceptions.html#FileNotFoundError) – 지정된 경로에 USD 파일이 존재하지 않을 경우 발생합니다.

### *class* isaaclab.sim.spawners.from_files.UsdFileCfg

자산을 생성할 USD 파일입니다.

USD 파일은 씬에 직접 가져옵니다. 그러나 복잡성 때문에 다양한 작업을 수행할 수 있습니다. 예를 들어, 변형을 선택하거나, 물질을 적용하거나, 기존 속성을 수정할 수 있습니다.

구성 매개변수의 폭발을 방지하기 위해 사용 가능한 작업은 가장 일반적인 것으로 제한됩니다. 여기에는 다음이 포함됩니다:

- **변형 선택**: 이 작업은 [`variants`](#isaaclab.sim.spawners.from_files.UsdFileCfg.variants) 매개변수를 지정하여 수행됩니다.
- **재질 생성 및 적용**: 이 작업은 [`visual_material`](#isaaclab.sim.spawners.from_files.UsdFileCfg.visual_material) 매개변수를 지정하여 수행됩니다.
- **기존 속성 수정**: 이는 구성 클래스에 해당하는 속성을 지정하여 수행됩니다. 예를 들어, 가져온 프리머의 스케일을 수정하려면 [`scale`](#isaaclab.sim.spawners.from_files.UsdFileCfg.scale) 매개변수를 설정합니다.

자세한 내용은 [`spawn_from_usd()`](#isaaclab.sim.spawners.from_files.spawn_from_usd)를 참조하십시오.

#### NOTE
구성 매개변수에는 다양한 속성이 포함됩니다. None이 아닌 경우, 이러한 속성은 중첩 방식으로 프리머가 생성될 때 수정됩니다.

값이 설정된 경우, 프리머가 생성될 때 속성이 중첩 방식으로 수정됩니다. 이는 지정된 속성을 포함하여 해당 함수를 호출하여 수행됩니다.

**속성:**

| [`usd_path`](#isaaclab.sim.spawners.from_files.UsdFileCfg.usd_path)                                 | 자산을 생성할 USD 파일의 경로입니다.                                               |
|-----------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [`variants`](#isaaclab.sim.spawners.from_files.UsdFileCfg.variants)                                 | 입력 USD 파일에서 선택할 변형입니다.                                          |
| [`visible`](#isaaclab.sim.spawners.from_files.UsdFileCfg.visible)                                   | 생성된 자산이 표시되어야 하는지 여부입니다.                                            |
| [`semantic_tags`](#isaaclab.sim.spawners.from_files.UsdFileCfg.semantic_tags)                       | 생성된 자산에 추가할 의미 태그 목록입니다.                                      |
| [`copy_from_source`](#isaaclab.sim.spawners.from_files.UsdFileCfg.copy_from_source)                 | 소스 프리머에서 자산을 복사할 것인지 상속할 것인지 여부입니다.                           |
| [`mass_props`](#isaaclab.sim.spawners.from_files.UsdFileCfg.mass_props)                             | 질량 속성입니다.                                                                        |
| [`deformable_props`](#isaaclab.sim.spawners.from_files.UsdFileCfg.deformable_props)                 | 변형 가능한 물체 속성입니다.                                                             |
| [`rigid_props`](#isaaclab.sim.spawners.from_files.UsdFileCfg.rigid_props)                           | 강체 물체 속성입니다.                                                                  |
| [`collision_props`](#isaaclab.sim.spawners.from_files.UsdFileCfg.collision_props)                   | 모든 충돌 메시에 적용할 속성입니다.                                            |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.from_files.UsdFileCfg.activate_contact_sensors) | 모든 강체에 대해 접촉 보고를 활성화합니다.                                         |
| [`scale`](#isaaclab.sim.spawners.from_files.UsdFileCfg.scale)                                       | 자산의 스케일입니다.                                                                     |
| [`articulation_props`](#isaaclab.sim.spawners.from_files.UsdFileCfg.articulation_props)             | 어큘레이션 루트에 적용할 속성입니다.                                           |
| [`fixed_tendons_props`](#isaaclab.sim.spawners.from_files.UsdFileCfg.fixed_tendons_props)           | 고정 테넌(있는 경우)에 적용할 속성입니다.                                      |
| [`spatial_tendons_props`](#isaaclab.sim.spawners.from_files.UsdFileCfg.spatial_tendons_props)       | 공간 테넌(있는 경우)에 적용할 속성입니다.                                    |
| [`joint_drive_props`](#isaaclab.sim.spawners.from_files.UsdFileCfg.joint_drive_props)               | 조인트에 적용할 속성입니다.                                                         |
| [`visual_material_path`](#isaaclab.sim.spawners.from_files.UsdFileCfg.visual_material_path)         | 프리머에 사용할 시각적 재질의 경로입니다.                                        |
| [`visual_material`](#isaaclab.sim.spawners.from_files.UsdFileCfg.visual_material)                   | URDF 파일의 시각적 재질 속성을 오버라이드하는 시각적 재질 속성입니다. |

#### usd_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

자산을 생성할 USD 파일의 경로입니다.

#### variants *: [object](https://docs.python.org/3/library/functions.html#object) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None)*

입력 USD 파일에서 선택할 변형입니다. 기본값은 None이며, 이 경우 변형이 적용되지 않습니다.

이것은 각 속성이 변형 세트 이름을 나타내고 지정된 값이 되는 configclass 객체일 수 있거나, 두 항목 간의 매핑인 딕셔너리일 수 있습니다. 자세한 내용은 `select_usd_variants()` 함수를 참조하십시오.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 자산이 표시되어야 하는지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 자산에 추가할 의미 태그 목록입니다. 기본값은 None이며, 이 경우 의미 태그가 추가되지 않습니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형식의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 연결된 의미 레이블입니다. 예를 들어, 아보카도 클래스의 생성된 자산을 주석으로 달려면 의미 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 녹색 색상의 생성된 자산을 주석으로 달려면 의미 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프리머에서 자산을 복사할 것인지 상속할 것인지 여부입니다. 기본값은 True입니다.

이 매개변수는 프리머를 복제할 때만 사용됩니다. False인 경우, 자산은 소스 프리머에서 상속됩니다. 즉, 소스 프리머에 대한 모든 USD 변경 사항이 복제된 프리머에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성입니다.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 물체 속성입니다.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 물체 속성입니다.

강체 객체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다. 이는 객체가 정적이 되어 중력이나 다른 힘의 영향을 받지 않도록 합니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에 대해 접촉 보고를 활성화합니다. 기본값은 False입니다.

지정된 프리머 경로와 그 자식의 모든 강체에 PhysxContactReporter API를 추가합니다.

#### scale *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

자산의 스케일입니다. 기본값은 None이며, 이 경우 스케일이 수정되지 않습니다.

#### articulation_props *: [schemas.ArticulationRootPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.ArticulationRootPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

어큘레이션 루트에 적용할 속성입니다.

#### fixed_tendons_props *: [schemas.FixedTendonPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.FixedTendonPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

고정 테넌(있는 경우)에 적용할 속성입니다.

#### spatial_tendons_props *: schemas.SpatialTendonPropertiesCfg | [None](https://docs.python.org/3/library/constants.html#None)*

공간 테넌(있는 경우)에 적용할 속성입니다.

#### joint_drive_props *: [schemas.JointDrivePropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.JointDrivePropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

조인트에 적용할 속성입니다.

#### NOTE
조인트 드라이브 속성은 자산의 모든 조인트 드라이브의 USD 속성을 설정합니다. 이 속성은 필요할 때만 절제하여 사용하는 것이 좋습니다. 대신, 다음을 사용하는 것이 좋습니다.
[`actuators`](isaaclab.assets.md#isaaclab.assets.ArticulationCfg.actuators) 파라미터를 사용하여 관절 구동 속성을 특정 관절에 설정합니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

사용할 시각적 재료의 경로입니다. 기본값은 "material"입니다.

경로가 상대 경로인 경우, 프라임의 경로를 기준으로 해석됩니다.
visual_material이 None이 아닌 경우 이 파라미터는 무시됩니다.

#### visual_material *: [materials.VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

URDF 파일의 시각적 재료 속성을 오버라이드하는 시각적 재료 속성입니다.

#### 참고 사항
None인 경우 시각적 재료가 추가되지 않습니다.

### isaaclab.sim.spawners.from_files.spawn_ground_plane(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [from_files_cfg.GroundPlaneCfg](#isaaclab.sim.spawners.from_files.GroundPlaneCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs) → Usd.Prim

장면에 지상 평면을 생성합니다.

이 함수는 Isaac Sim의 그리드 평면 에셋이 포함된 USD 파일을 로드합니다. 다른 지상 평면 에셋과의 호환성은 보장되지 않을 수 있습니다.
다른 에셋을 사용해야 하는 경우, spawn_from_usd 함수를 사용하세요.

#### 참고 사항
이 함수는 다른 스포너와의 호환성을 위해 키워드 인수를 허용하지만, kwargs는 전혀 사용하지 않습니다.

* **파라미터:**
  * **prim_path** – 에셋을 스폰할 경로입니다.
  * **cfg** – 구성 인스턴스입니다.
  * **translation** – 부모 프라임에 대한 프라임의 변환 (translation). 기본값은 None으로, 이 경우 USD 파일에 지정된 변환이 사용됩니다.
  * **orientation** – 부모 프라임에 대한 프라임의 방향 (w, x, y, z 순서). 기본값은 None으로, 이 경우 USD 파일에 지정된 방향이 사용됩니다.
  * **\*\*kwargs** – `clone_in_fabric`과 같은 추가 키워드 인수입니다.
* **반환 값:**
  생성된 에셋의 프라임입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프라임 경로가 이미 존재하는 경우 발생합니다.

### *클래스* isaaclab.sim.spawners.from_files.GroundPlaneCfg

지상 평면 프라임을 생성합니다.

기본적으로 Isaac Sim의 표준 그리드-월드 지상 평면 USD 파일을 사용합니다.

**속성:**

| [`visible`](#isaaclab.sim.spawners.from_files.GroundPlaneCfg.visible)                   | 생성된 에셋이 보이는지 여부입니다.                  |
|-----------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`semantic_tags`](#isaaclab.sim.spawners.from_files.GroundPlaneCfg.semantic_tags)       | 생성된 에셋에 추가할 시맨틱 태그 목록입니다.            |
| [`copy_from_source`](#isaaclab.sim.spawners.from_files.GroundPlaneCfg.copy_from_source) | 소스 프라임에서 에셋을 복사할지, 아니면 상속할지 여부입니다. |
| [`usd_path`](#isaaclab.sim.spawners.from_files.GroundPlaneCfg.usd_path)                 | 에셋을 생성할 USD 파일 경로입니다.                     |
| [`color`](#isaaclab.sim.spawners.from_files.GroundPlaneCfg.color)                       | 지상 평면의 색상입니다.                                |
| [`size`](#isaaclab.sim.spawners.from_files.GroundPlaneCfg.size)                         | 지상 평면의 크기입니다.                                 |
| [`physics_material`](#isaaclab.sim.spawners.from_files.GroundPlaneCfg.physics_material) | 물리 재료 속성입니다.                                  |

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 보이는지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 시맨틱 태그 목록입니다. 기본값은 None이며, 이 경우 시맨틱 태그가 추가되지 않습니다.

시맨틱 태그는 Replicator 시맨틱 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 종류이고 `data`는 해당 태그와 연결된 시맨틱 라벨입니다. 예를 들어, 아보카도 클래스의 에셋에 태그를 추가하려면 시맨틱 태그는 `[("class", "avocado")]`가 됩니다.

여러 시맨틱 태그를 지정하려면 태그 목록을 전달하면 됩니다. 예를 들어, 아보카도 클래스와 초록색 색상의 에셋에 태그를 추가하려면 시맨틱 태그는 `[("class", "avocado"), ("color", "green")]`가 됩니다.

#### 참고 사항
시맨틱 필터에 대한 자세한 내용은 [시맨틱 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프라임에서 에셋을 복사할지, 아니면 상속할지 여부입니다. 기본값은 True입니다.

이 파라미터는 프라임을 클론할 때만 사용됩니다. False인 경우, 에셋은 소스 프라임에서 상속되며, 즉 소스 프라임의 모든 USD 변경 사항이 클론된 프라임에 반영됩니다.

#### usd_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

에셋을 생성할 USD 파일 경로입니다. 기본값은 그리드-월드 지상 평면입니다.

#### color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

지상 평면의 색상입니다. 기본값은 (0.0, 0.0, 0.0)입니다.

None인 경우 색상이 변경되지 않습니다.

#### size *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

지상 평면의 크기입니다. 기본값은 100m x 100m입니다.

#### physics_material *: [RigidBodyMaterialCfg](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg)*

물리 재료 속성입니다. 기본값은 기본 강체 재료입니다.

## 재질

USD 기반 및 PhysX 기반 재질을 스폰하는 스포너의 하위 모듈입니다.

[재질](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/materials.html)은 시뮬레이션에서 객체의 외관과 물리적 속성을 정의하는 데 사용됩니다.
옴니버스에서는 NVIDIA의 [재질 정의 언어(MDL)](https://raytracing-docs.nvidia.com/mdl/introduction/index.html#mdl_introduction#)을 사용하여 재질을 정의합니다. MDL은 물리 기반 렌더링(PBR) 모델을 기반으로 하며, 이는 표면과 빛의 상호작용을 설명하는 방정식의 집합입니다. PBR 모델은 현실적인 재질을 만드는 데 사용됩니다.

MDL은 주로 객체의 외관을 정의하는 데 사용되지만, 물리적 속성을 정의하도록 확장할 수도 있습니다. 예를 들어, 고무 재료의 마찰 및 반발 계수를 정의할 수 있습니다. [물리 재료](https://isaac-sim.github.io/IsaacLab/main/source/api/lab/isaaclab.sim.html#isaaclab.sim.SimulationCfg.physics_material)를 물리 객체에 할당하여 그 물리적 속성을 정의할 수 있습니다. 물리 재료에는 강체 재료, 변형 가능한 재료, 유체 재료 등이 있습니다.

객체에 재질을 적용하려면 객체의 기하학을 재질에 "바인딩"해야 합니다. 이를 위해 [USD 머티리얼 바인딩 API](https://openusd.org/dev/api/class_usd_shade_material_binding_a_p_i.html)를 사용합니다. 머티리얼 바인딩 API는 기하학 경로와 재질 경로를 입력으로 받아서 두 가지를 결합합니다.

물리 재료의 경우, 재질은 'physics' 목적을 가진 물리 객체에 바인딩됩니다. 객체에서 물리 재료 속성을 파싱할 때 다음 우선순위가 적용됩니다:

1. 'physics' 목적을 가진 머티리얼 바인딩 (물리 재료)
2. 목적 없이 머티리얼 바인딩 (시각적 재료)
3. [물리 씬](https://openusd.org/dev/api/usd_physics_page_front.html) 프라임의 'physics' 목적을 가진 머티리얼 바인딩
4. PhysX 내의 재질 속성 기본값

사용법:
: ```python
  import isaaclab.sim as sim_utils
  <br/>
  # 시각적 재료 생성
  visual_material_cfg = sim_utils.GlassMdlCfg(glass_ior=1.0, thin_walled=True)
  visual_material_cfg.func("/World/Looks/glassMaterial", visual_material_cfg)
  <br/>
  # 메시 프라임 생성
  cube_cfg = sim_utils.CubeCfg(size=[1.0, 1.0, 1.0])
  cube_cfg.func("/World/Primitives/Cube", cube_cfg)
  <br/>
  # 큐브를 시각적 재료에 바인딩
  sim_utils.bind_visual_material("/World/Primitives/Cube", "/World/Looks/glassMaterial")
  ```

### 클래스

| [`VisualMaterialCfg`](#isaaclab.sim.spawners.materials.VisualMaterialCfg)                 | 시각적 재료 생성을 위한 구성 파라미터입니다.          |
|-------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| [`PreviewSurfaceCfg`](#isaaclab.sim.spawners.materials.PreviewSurfaceCfg)                 | 미리보기 표면을 생성하기 위한 구성 매개변수입니다.          |
| [`MdlFileCfg`](#isaaclab.sim.spawners.materials.MdlFileCfg)                               | 파일에서 MDL 자료를 로드하기 위한 구성 매개변수입니다. |
| [`GlassMdlCfg`](#isaaclab.sim.spawners.materials.GlassMdlCfg)                             | 유리 MDL 자료를 로드하기 위한 구성 매개변수입니다.        |
| [`PhysicsMaterialCfg`](#isaaclab.sim.spawners.materials.PhysicsMaterialCfg)               | 물리 재료를 생성하기 위한 구성 매개변수입니다.         |
| [`RigidBodyMaterialCfg`](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg)           | 강체에 대한 물리 재료 매개변수입니다.                     |
| [`DeformableBodyMaterialCfg`](#isaaclab.sim.spawners.materials.DeformableBodyMaterialCfg) | 변형 가능한 신체에 대한 물리 재료 매개변수입니다.                |

### 시각적 재료

### *class* isaaclab.sim.spawners.materials.VisualMaterialCfg

시각적 재료를 생성하기 위한 구성 매개변수입니다.

### isaaclab.sim.spawners.materials.spawn_preview_surface(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [visual_materials_cfg.PreviewSurfaceCfg](#isaaclab.sim.spawners.materials.PreviewSurfaceCfg)) → Usd.Prim

주어진 구성으로 미리보기 표면 프라이머를 생성하고 설정을 오버라이드합니다.

미리보기 표면은 간단한 셰이더를 처리하면서 *specular* 및 *metallic* 워크플로우 모두를 지원하는 물리 기반 표면입니다. 모든 색상 입력은 선형 색 공간(RGB)에 있습니다. 자세한 내용은 [문서](https://openusd.org/release/spec_usdpreviewsurface.html)를 참조하세요.

이 함수는 프라이머를 생성하기 위해 USD 명령 [CreateShaderPrimFromSdrCommand](https://docs.omniverse.nvidia.com/kit/docs/omni.usd/latest/omni.usd.commands/omni.usd.commands.CreateShaderPrimFromSdrCommand.html)를 호출합니다.

#### NOTE
이 함수는 `clone()`로 데코레이트되어 입력 프라이머 경로가 정규 표현식 패턴인 경우 경로를 경로 목록으로 해석합니다. 이는 단일 경로 표현식에서 여러 에셋을 생성하고 지정된 경로 표현식에서 USD 프라이머를 클론하여 지원하기 위해 수행됩니다.

* **매개변수:**
  * **prim_path** – 에어셋을 생성할 프라이머 경로 또는 패턴입니다. 프라이머 경로가 정규 표현식 패턴인 경우, 일치하는 모든 프라이머 경로에 에어셋이 생성됩니다.
  * **cfg** – 구성 인스턴스.
* **반환값:**
  생성된 프라이머.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로에 프라이머가 이미 존재하는 경우.

### *class* isaaclab.sim.spawners.materials.PreviewSurfaceCfg

미리보기 표면을 생성하기 위한 구성 매개변수입니다.

추가 정보는 [`spawn_preview_surface()`](#isaaclab.sim.spawners.materials.spawn_preview_surface)를 참조하세요.

**속성:**

| [`diffuse_color`](#isaaclab.sim.spawners.materials.PreviewSurfaceCfg.diffuse_color)   | RGB 확산 색상입니다.                   |
|---------------------------------------------------------------------------------------|--------------------------------------------|
| [`emissive_color`](#isaaclab.sim.spawners.materials.PreviewSurfaceCfg.emissive_color) | 표면의 RGB 방출 구성 요소입니다. |
| [`roughness`](#isaaclab.sim.spawners.materials.PreviewSurfaceCfg.roughness)           | 스펙트럼 로브의 거칠기입니다.           |
| [`metallic`](#isaaclab.sim.spawners.materials.PreviewSurfaceCfg.metallic)             | 금속성 구성 요소입니다.                    |
| [`opacity`](#isaaclab.sim.spawners.materials.PreviewSurfaceCfg.opacity)               | 표면의 불투명도입니다.                |

#### diffuse_color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

RGB 확산 색상입니다. 이는 표면의 기본 색상입니다. 기본값은 회색입니다.

#### emissive_color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

표면의 RGB 방출 구성 요소입니다. 기본값은 검정색입니다.

#### roughness *: [float](https://docs.python.org/3/library/functions.html#float)*

스펙트럼 로브의 거칠기입니다. 0(매끄러움)에서 1(거칠음) 사이로 범위가 지정됩니다. 기본값은 0.5입니다.

#### metallic *: [float](https://docs.python.org/3/library/functions.html#float)*

금속성 구성 요소입니다. 0(유전체)에서 1(금속) 사이로 범위가 지정됩니다. 기본값은 0입니다.

#### opacity *: [float](https://docs.python.org/3/library/functions.html#float)*

표면의 불투명도입니다. 0(투명)에서 1(불투명) 사이로 범위가 지정됩니다. 기본값은 1입니다.

#### NOTE
불투명도는 인터랙티브 렌더링 중에 표면의 외관에만 영향을 미칩니다.

### isaaclab.sim.spawners.materials.spawn_from_mdl_file(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [visual_materials_cfg.MdlFileCfg](#isaaclab.sim.spawners.materials.MdlFileCfg) | [visual_materials_cfg.GlassMdlCfg](#isaaclab.sim.spawners.materials.GlassMdlCfg)) → Usd.Prim

MDL 파일에서 재료를 로드하고 주어진 구성으로 설정을 오버라이드합니다.

NVIDIA의 [Material Definition Language (MDL)](https://www.nvidia.com/en-us/design-visualization/technologies/material-definition-language/)은 물리 기반 재료를 정의하기 위한 언어입니다. MDL 파일 형식은 바이너리 형식으로, Omniverse 및 Adobe Substance Designer와 같은 기타 응용 프로그램에서 로드할 수 있습니다. MDL에 대해 자세히 알아보려면 [문서](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/materials.html)를 참조하세요.

이 함수는 프라이머를 생성하기 위해 USD 명령 [CreateMdlMaterialPrim](https://docs.omniverse.nvidia.com/kit/docs/omni.usd/latest/omni.usd.commands/omni.usd.commands.CreateMdlMaterialPrimCommand.html)를 호출합니다.

#### NOTE
이 함수는 `clone()`로 데코레이트되어 입력 프라이머 경로가 정규 표현식 패턴인 경우 경로를 경로 목록으로 해석합니다. 이는 단일 경로 표현식에서 여러 에어셋을 생성하고 지정된 경로 표현식에서 USD 프라이머를 클론하여 지원하기 위해 수행됩니다.

* **매개변수:**
  * **prim_path** – 에어셋을 생성할 프라이머 경로 또는 패턴입니다. 프라이머 경로가 정규 표현식 패턴인 경우, 일치하는 모든 프라이머 경로에 에어셋이 생성됩니다.
  * **cfg** – 구성 인스턴스.
* **반환값:**
  생성된 프라이머.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로에 프라이머가 이미 존재하는 경우.

### *class* isaaclab.sim.spawners.materials.MdlFileCfg

파일에서 MDL 자료를 로드하기 위한 구성 매개변수입니다.

추가 정보는 [`spawn_from_mdl_file()`](#isaaclab.sim.spawners.materials.spawn_from_mdl_file)를 참조하세요.

**속성:**

| [`mdl_path`](#isaaclab.sim.spawners.materials.MdlFileCfg.mdl_path)                   | MDL 재료의 경로입니다.                           |
|--------------------------------------------------------------------------------------|---------------------------------------------------------|
| [`project_uvw`](#isaaclab.sim.spawners.materials.MdlFileCfg.project_uvw)             | 재료의 UVW 좌표를 투영할지 여부입니다. |
| [`albedo_brightness`](#isaaclab.sim.spawners.materials.MdlFileCfg.albedo_brightness) | 재료의 확산 색상에 대한 배수입니다.       |
| [`texture_scale`](#isaaclab.sim.spawners.materials.MdlFileCfg.texture_scale)         | 텍스처의 스케일입니다.                               |

#### mdl_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

MDL 재료의 경로입니다.

NVIDIA Omniverse는 NVIDIA Nucleus에 다양한 MDL 재료를 제공합니다. 이러한 재료를 사용하려면 nuclei 디렉터리의 재료 경로를 `{NVIDIA_NUCLEUS_DIR}` 변수로 설정할 수 있습니다. 이는 호스트 머신의 NVIDIA Nucleus 디렉터리 경로로 내부적으로 해결되며, 이 과정은 [`isaaclab.utils.assets.NVIDIA_NUCLEUS_DIR`](isaaclab.utils.md#isaaclab.utils.assets.NVIDIA_NUCLEUS_DIR) 속성을 통해 수행됩니다.

예를 들어, “Aluminum_Anodized” 재료를 사용하려면 경로를 다음과 같이 설정할 수 있습니다:
`{NVIDIA_NUCLEUS_DIR}/Materials/Base/Metals/Aluminum_Anodized.mdl`.

#### project_uvw *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

재료의 UVW 좌표를 투영할지 여부입니다. 기본값은 None입니다.

None인 경우, MDL 재료의 기본 설정이 사용됩니다.

#### albedo_brightness *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

재료의 확산 색상에 대한 배수입니다. 기본값은 None입니다.

None인 경우, MDL 재료의 기본 설정이 사용됩니다.

#### texture_scale *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

텍스처의 스케일입니다. 기본값은 None입니다.

None인 경우, MDL 재료의 기본 설정이 사용됩니다.

### *class* isaaclab.sim.spawners.materials.GlassMdlCfg

유리 MDL 재료를 로드하기 위한 구성 매개변수입니다.

이 클래스는 유리 MDL 재료를 로드하기 위한 편의 클래스입니다. 유리 재료에 대한 자세한 내용은 [문서](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/materials.html#omniglass)를 참조하세요.

#### NOTE
기본값은 NVIDIA Nucleus의 유리 재료에서 가져온 것입니다.

**속성:**

| [`mdl_path`](#isaaclab.sim.spawners.materials.GlassMdlCfg.mdl_path)                     | MDL 재료의 경로입니다.                                                                 |
|-----------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| [`glass_color`](#isaaclab.sim.spawners.materials.GlassMdlCfg.glass_color)               | 유리의 RGB 색상 또는 틴트입니다.                                                           |
| [`frosting_roughness`](#isaaclab.sim.spawners.materials.GlassMdlCfg.frosting_roughness) | 표면의 반사도 양을 나타냅니다.                                                                |
| [`thin_walled`](#isaaclab.sim.spawners.materials.GlassMdlCfg.thin_walled)               | 얇은 벽 모델링에서의 굴절을 수행할지 여부를 지정합니다.                                   |
| [`glass_ior`](#isaaclab.sim.spawners.materials.GlassMdlCfg.glass_ior)                   | 유리를 통과할 때 빛이 얼마나 휘어지는지를 조절하는 굴절률의 입사율입니다.                    |

#### mdl_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

MDL 자료의 경로입니다. 기본값은 NVIDIA Nucleus 내의 유리 재질입니다.

#### glass_color *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

유리의 RGB 색상 또는 틴트입니다. 기본값은 흰색입니다.

#### frosting_roughness *: [float](https://docs.python.org/3/library/functions.html#float)*

표면의 반사도 양을 나타냅니다. 0 (완전히 투명)에서 1 (유리처럼 불투명) 범위를 가집니다. 기본값은 0입니다.

#### thin_walled *: [bool](https://docs.python.org/3/library/functions.html#bool)*

얇은 벽 모델링에서의 굴절을 수행할지 여부를 지정합니다. 기본값은 False입니다.

#### glass_ior *: [float](https://docs.python.org/3/library/functions.html#float)*

유리를 통과할 때 빛이 얼마나 휘어지는지를 조절하는 굴절률의 입사율입니다. 기본값은 1.491로, 이는 유리의 굴절률입니다.

### 물리적 재질

### *class* isaaclab.sim.spawners.materials.PhysicsMaterialCfg

물리적 재질 생성을 위한 구성 매개변수입니다.

물리적 재질은 PhysX 스키마로, USD 재질 프림에 적용하여 재질과 관련된 물리적 속성(예: 마찰 계수, 복원 계수 등)을 정의할 수 있습니다. 물리적 재질에 대한 자세한 내용은
[PhysX 문서](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/_api_build/classPxBaseMaterial.html)를 참조하십시오.

### isaaclab.sim.spawners.materials.spawn_rigid_body_material(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [physics_materials_cfg.RigidBodyMaterialCfg](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg)) → Usd.Prim

강체 물리적 속성을 갖는 재질을 생성합니다.

강체 재질은 강체의 메시에 물리적 속성을 정의하는 데 사용됩니다. 여기에는 마찰, 복원, 그리고 그에 해당하는 결합 모드가 포함됩니다. 강체 재질에 대한 자세한 내용은
[PxMaterial 문서](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/_api_build/classPxBaseMaterial.html)를 참조하십시오.

#### 참고
이 함수는 `clone()` 데코레이터로 장식되어 있으며, 입력된 prim 경로가 정규 표현식 패턴인 경우 해당 경로 목록으로 해석됩니다. 이는 단일 위치에서 여러 자산을 생성하고 주어진 경로 표현식의 USD 프림을 복제하여 지원하기 위해 수행됩니다.

* **매개변수:**
  * **prim_path** – 자산을 생성할 프림 경로 또는 패턴입니다. 프림 경로가 정규 표현식 패턴인 경우, 자산은 모든 일치하는 프림 경로에 생성됩니다.
  * **cfg** – 물리적 재질의 구성입니다.
* **반환값:**
  생성된 강체 재질 프림입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 프림 경로에 이미 프림이 존재하고 해당 프림이 재질이 아닌 경우 발생합니다.

### *class* isaaclab.sim.spawners.materials.RigidBodyMaterialCfg

강체에 대한 물리적 재질 매개변수입니다.

자세한 내용은 [`spawn_rigid_body_material()`](#isaaclab.sim.spawners.materials.spawn_rigid_body_material)를 참조하십시오.

**속성:**

| [`static_friction`](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg.static_friction)                         | 정적 마찰 계수입니다.                                               |
|--------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`dynamic_friction`](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg.dynamic_friction)                       | 동적 마찰 계수입니다.                                              |
| [`restitution`](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg.restitution)                                 | 복원 계수입니다.                                                   |
| [`friction_combine_mode`](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg.friction_combine_mode)             | 충돌 시 마찰이 결합되는 방식을 결정합니다.                |
| [`restitution_combine_mode`](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg.restitution_combine_mode)       | 충돌 시 복원 계수가 결합되는 방식을 결정합니다. |
| [`compliant_contact_stiffness`](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg.compliant_contact_stiffness) | 준수 접촉 모델에서 암시적 스프링을 사용한 스프링 강성입니다.         |
| [`compliant_contact_damping`](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg.compliant_contact_damping)     | 준수 접촉 모델에서 암시적 스프링을 사용한 감쇠 계수입니다.      |

#### static_friction *: [float](https://docs.python.org/3/library/functions.html#float)*

정적 마찰 계수입니다. 기본값은 0.5입니다.

#### dynamic_friction *: [float](https://docs.python.org/3/library/functions.html#float)*

동적 마찰 계수입니다. 기본값은 0.5입니다.

#### restitution *: [float](https://docs.python.org/3/library/functions.html#float)*

복원 계수입니다. 기본값은 0.0입니다.

#### friction_combine_mode *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['average', 'min', 'multiply', 'max']*

충돌 시 마찰이 결합되는 방식을 결정합니다. 기본값은 “average”입니다.

#### 주의
다른 결합 모드를 가진 두 물리적 재질이 충돌할 때, 우선순위가 높은 결합 모드가 사용됩니다. 우선순위 순서는
[여기](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/_api_build/structPxCombineMode.html)에서 제공됩니다.

#### restitution_combine_mode *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['average', 'min', 'multiply', 'max']*

충돌 시 복원 계수가 결합되는 방식을 결정합니다. 기본값은 “average”입니다.

#### 주의
다른 결합 모드를 가진 두 물리적 재질이 충돌할 때, 우선순위가 높은 결합 모드가 사용됩니다. 우선순위 순서는
[여기](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/_api_build/structPxCombineMode.html)에서 제공됩니다.

#### compliant_contact_stiffness *: [float](https://docs.python.org/3/library/functions.html#float)*

준수 접촉 모델에서 암시적 스프링을 사용한 스프링 강성입니다. 기본값은 0.0입니다.

강성이 높을수록 강성 접촉에 가까운 동작을 보입니다. 준수 접촉 모델은 강성이 0보다 클 때만 활성화됩니다.

#### compliant_contact_damping *: [float](https://docs.python.org/3/library/functions.html#float)*

준수 접촉 모델에서 암시적 스프링을 사용한 감쇠 계수입니다. 기본값은 0.0입니다.

[`compliant_contact_stiffness`](#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg.compliant_contact_stiffness)가 0으로 설정되어 준수 접촉이 비활성화되고 강성 접촉이 활성화된 경우 무의미합니다.

### isaaclab.sim.spawners.materials.spawn_deformable_body_material(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [physics_materials_cfg.DeformableBodyMaterialCfg](#isaaclab.sim.spawners.materials.DeformableBodyMaterialCfg)) → Usd.Prim

변형 가능한 물체의 물리적 속성을 갖는 재질을 생성합니다.

변형 가능한 재질은 변형 가능한 물체의 메시에 물리적 속성을 정의하는 데 사용됩니다. 여기에는 마찰 및 변형 가능한 물체 속성이 포함됩니다. 변형 가능한 재질에 대한 자세한 내용은
[PxFEMSoftBodyMaterial 문서](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/_api_build/structPxFEMSoftBodyMaterialModel.html)를 참조하십시오.

#### 참고
이 함수는 `clone()` 데코레이터로 장식되어 있으며, 입력된 prim 경로가 정규 표현식 패턴인 경우 해당 경로 목록으로 해석됩니다. 이는 단일 위치에서 여러 자산을 생성하고 주어진 경로 표현식의 USD 프림을 복제하여 지원하기 위해 수행됩니다.

* **매개변수:**
  * **prim_path** – 자산을 생성할 프림 경로 또는 패턴입니다. 프림 경로가 정규 표현식 패턴인 경우, 자산은 모든 일치하는 프림 경로에 생성됩니다.
  * **cfg** – 물리적 재질의 구성입니다.
* **반환값:**
  생성된 변형 가능한 재질 프림입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 프림 경로에 이미 프림이 존재하고 해당 프림이 재질이 아닌 경우 발생합니다.

### *class* isaaclab.sim.spawners.materials.DeformableBodyMaterialCfg

변형 가능한 물체에 대한 물리적 재질 매개변수입니다.

자세한 내용은 [`spawn_deformable_body_material()`](#isaaclab.sim.spawners.materials.spawn_deformable_body_material)를 참조하십시오.

**속성:**

| [`density`](#isaaclab.sim.spawners.materials.DeformableBodyMaterialCfg.density)                       | 재질의 밀도입니다.                                             |
|-------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| [`dynamic_friction`](#isaaclab.sim.spawners.materials.DeformableBodyMaterialCfg.dynamic_friction)     | 동적 마찰력.                                                      |
| [`youngs_modulus`](#isaaclab.sim.spawners.materials.DeformableBodyMaterialCfg.youngs_modulus)         | 신체의 강성을 정의하는 영률.                                      |
| [`poissons_ratio`](#isaaclab.sim.spawners.materials.DeformableBodyMaterialCfg.poissons_ratio)         | 신체의 부피 보존을 정의하는 푸아송 비율.                          |
| [`elasticity_damping`](#isaaclab.sim.spawners.materials.DeformableBodyMaterialCfg.elasticity_damping) | 변형 가능한 소재의 탄성 감쇠.                                     |
| [`damping_scale`](#isaaclab.sim.spawners.materials.DeformableBodyMaterialCfg.damping_scale)           | 변형 가능한 소재의 감쇠 스케일.                                   |

#### density *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

재료의 밀도. 기본값은 None이며, 이 경우 시뮬레이션이 기본 밀도를 결정합니다.

#### dynamic_friction *: [float](https://docs.python.org/3/library/functions.html#float)*

동적 마찰력. 기본값은 0.25입니다.

#### youngs_modulus *: [float](https://docs.python.org/3/library/functions.html#float)*

신체의 강성을 정의하는 영률. 기본값은 50000000.0입니다.

영률은 응력 하에서 재료가 변형될 수 있는 능력을 측정하는 지표입니다. 단위는 파스칼(Pa)입니다.

#### poissons_ratio *: [float](https://docs.python.org/3/library/functions.html#float)*

신체의 부피 보존을 정의하는 푸아송 비율. 기본값은 0.45입니다.

푸아송 비율은 축 방향 압축 시 재료가 횡방향으로 확장될 수 있는 능력을 측정하는 무차원 수입니다. 0과 0.5 사이의 값입니다. 0.5의 값을 사용하면 재료가 압축 불가(incompressible)가 됩니다.

#### elasticity_damping *: [float](https://docs.python.org/3/library/functions.html#float)*

변형 가능한 소재의 탄성 감쇠. 기본값은 0.005입니다.

#### damping_scale *: [float](https://docs.python.org/3/library/functions.html#float)*

변형 가능한 소재의 감쇠 스케일. 기본값은 1.0입니다.

스케일이 1이면 기본 감쇠를 적용합니다. 값이 0이면 특정 동작에만 감쇠를 적용하여 물로 채워진 부드러운 물체와 유사한 특수 효과를 만듭니다.

## 래퍼

스포너 구성을 래핑하는 서브 모듈입니다.

다른 스포너 모듈과 달리, 이 모듈은 여러 스포너 구성을 단일 구성으로 래핑하는 방법을 제공합니다. 이는 사용자가 서로 다른 구성에 기반하여 여러 자산을 생성하고자 할 때 유용합니다.

### 클래스

| [`MultiAssetSpawnerCfg`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg)   | 개별 구성에서 여러 자산을 로드하기 위한 구성 매개변수.   |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| [`MultiUsdFileCfg`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg)             | 여러 USD 파일을 로드하기 위한 구성 매개변수.                                     |

### isaaclab.sim.spawners.wrappers.spawn_multi_asset(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [wrappers_cfg.MultiAssetSpawnerCfg](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, clone_in_fabric: [bool](https://docs.python.org/3/library/functions.html#bool) = False, replicate_physics: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → Usd.Prim

제공된 구성에 기반하여 여러 자산을 생성합니다.

이 함수는 제공된 구성에 기반하여 여러 자산을 생성합니다. 자산은 목록에 제공된 순서대로 생성됩니다. [`random_choice`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.random_choice) 매개변수가 True로 설정된 경우, 각 생성에 대해 랜덤 자산 구성이 선택됩니다.

* **매개변수:**
  * **prim_path** – 자산을 생성할 프리머 경로.
  * **cfg** – 자산을 생성하기 위한 구성.
  * **translation** – 생성된 자산의 이동. 기본값은 None.
  * **orientation** – 생성된 자산의 방향 (w, x, y, z 순서). 기본값은 None.
  * **clone_in_fabric** – 패브릭에서 클론 생성 여부. 기본값은 False.
  * **replicate_physics** – 물리 복제 여부. 기본값은 False.
* **반환값:**
  첫 번째 프리머 경로에서 생성된 프리머.

### *클래스* isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg

개별 구성에서 여러 자산을 로드하기 위한 구성 매개변수.

구성 수준에서 속성에 값을 지정하면 개별 자산 구성의 설정을 재정의합니다. 예를 들어, 속성
[`MultiAssetSpawnerCfg.mass_props`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.mass_props)가 지정되면, 그 값이 [`assets_cfg`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.assets_cfg) 내 각 구성의 질량 속성 값(해당하는 경우)을 덮어씁니다. 이는 전역적으로 유사한 속성을 간편하게 구성하기 위해 수행됩니다. 기본적으로 모든 속성은 None으로 설정됩니다.

다음은 위의 규칙에 대한 예외입니다:

* [`visible`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.visible): 이 매개변수는 무시됩니다. 개별 자산의 값이 사용됩니다.
* [`semantic_tags`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.semantic_tags): 지정된 경우, 각 개별 자산의 시맨틱 태그에 추가됩니다.

**속성:**

| [`assets_cfg`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.assets_cfg)                             | 생성할 자산 구성 목록.                        |
|-------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`random_choice`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.random_choice)                       | 자산 구성을 랜덤으로 선택할지 여부.            |
| [`visible`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.visible)                                   | 생성된 자산이 보여져야 하는지 여부.                  |
| [`semantic_tags`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.semantic_tags)                       | 생성된 자산에 추가할 시맨틱 태그 목록.            |
| [`copy_from_source`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.copy_from_source)                 | 소스 프리머에서 자산을 복사할지 또는 상속받을지 여부. |
| [`mass_props`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.mass_props)                             | 질량 속성.                                              |
| [`deformable_props`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.deformable_props)                 | 변형 가능한 신체 속성.                                   |
| [`rigid_props`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.rigid_props)                           | 강체 속성.                                        |
| [`collision_props`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.collision_props)                   | 모든 충돌 메시에 적용할 속성.                  |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.wrappers.MultiAssetSpawnerCfg.activate_contact_sensors) | 모든 강체에 대한 접촉 보고서 활성화.               |

#### assets_cfg *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.sim.spawners.spawner_cfg.SpawnerCfg](#isaaclab.sim.spawners.SpawnerCfg)]*

생성할 자산 구성 목록.

#### random_choice *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산 구성을 랜덤으로 선택할지 여부. 기본값은 True입니다.

False인 경우, 목록에 제공된 순서대로 자산 구성이 생성됩니다.
True인 경우, 각 생성에 대해 랜덤 자산 구성이 선택됩니다.

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 자산이 보여져야 하는지 여부. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 자산에 추가할 시맨틱 태그 목록. 기본값은 None이며, 이는 시맨틱 태그가 추가되지 않음을 의미합니다.

시맨틱 태그는 리플리케이터 시맨틱 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형태의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 관련된 시맨틱 라벨입니다. 예를 들어, 아보카도 클래스의 생성된 자산에 주석을 달고자 할 때 시맨틱 태그는 `[("class", "avocado")]`가 됩니다.

여러 의미 태그는 태그 목록을 전달하여 지정할 수 있습니다. 예를 들어, `avocado` 클래스와 `green` 색을 가진 생성된 에셋을 주석 처리하려면 의미 태그는 다음과 같이 지정합니다.
`[("class", "avocado"), ("color", "green")]`.

#### 참조
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 에셋을 복사할지 아니면 상속받을지 여부입니다. 기본값은 True입니다.

이 매개변수는 프림 복제 시에만 사용됩니다. False이면 에셋이 소스 프림에서 상속받게 되며, 즉 소스 프림의 모든 USD 변경 사항이 복제된 프림에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 물체 속성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강성 물체 속성.

강성 물체를 정적으로 만들려면 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`를 True로 설정합니다. 이렇게 하면 물체가 정적으로 만들어지고 중력이나 다른 힘의 영향을 받지 않습니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

충돌 메시 모두에 적용할 속성입니다.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강성 물체에서 접촉 보고를 활성화합니다. 기본값은 False입니다.

이 옵션은 주어진 프림 경로와 그 자식의 모든 강성 물체에 PhysxContactReporter API를 추가합니다.

### isaaclab.sim.spawners.wrappers.spawn_multi_usd_file(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [wrappers_cfg.MultiUsdFileCfg](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg), translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, clone_in_fabric: [bool](https://docs.python.org/3/library/functions.html#bool) = False, replicate_physics: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → Usd.Prim

제공된 구성에 따라 여러 USD 파일을 생성합니다.

이 함수는 개별 USD 파일에 해당하는 구성 인스턴스를 생성하고 [`spawn_multi_asset()`](#isaaclab.sim.spawners.wrappers.spawn_multi_asset) 메서드를 호출하여 장면에 생성합니다.

* **매개변수:**
  * **prim_path** – 에셋을 생성할 프림 경로.
  * **cfg** – 에셋 생성을 위한 구성.
  * **translation** – 생성된 에셋의 변환. 기본값은 None입니다.
  * **orientation** – 생성된 에셋의 방향 (w, x, y, z 순서). 기본값은 None입니다.
  * **clone_in_fabric** – 패브릭에서 복제할지 여부. 기본값은 False입니다.
  * **replicate_physics** – 물리 현상을 복제할지 여부. 기본값은 False입니다.
* **반환값:**
  첫 번째 프림 경로에서 생성된 프림.

### *class* isaaclab.sim.spawners.wrappers.MultiUsdFileCfg

여러 USD 파일을 로드하기 위한 구성 매개변수입니다.

구성 수준에서 어떤 속성에도 값을 지정하면 해당 USD 파일에서 가져온 모든 에셋에 적용됩니다.

**속성:**

| [`visible`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.visible)                                   | 생성된 에셋이 표시되어야 하는지 여부입니다.                                            |
|--------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [`semantic_tags`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.semantic_tags)                       | 생성된 에셋에 추가할 의미 태그 목록입니다.                                      |
| [`copy_from_source`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.copy_from_source)                 | 소스 프림에서 에셋을 복사할지 아니면 상속받을지 여부입니다.                           |
| [`mass_props`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.mass_props)                             | 질량 속성.                                                                        |
| [`deformable_props`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.deformable_props)                 | 변형 가능한 물체 속성.                                                             |
| [`rigid_props`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.rigid_props)                           | 강성 물체 속성.                                                                  |
| [`collision_props`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.collision_props)                   | 충돌 메시 모두에 적용할 속성입니다.                                            |
| [`activate_contact_sensors`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.activate_contact_sensors) | 모든 강성 물체에서 접촉 보고를 활성화합니다.                                         |
| [`scale`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.scale)                                       | 에셋의 스케일.                                                                     |
| [`articulation_props`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.articulation_props)             | 어트큘레이션 루트에 적용할 속성입니다.                                           |
| [`fixed_tendons_props`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.fixed_tendons_props)           | 고정 테넌드(존재하는 경우)에 적용할 속성입니다.                                      |
| [`spatial_tendons_props`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.spatial_tendons_props)       | 공간 테넌드(존재하는 경우)에 적용할 속성입니다.                                    |
| [`joint_drive_props`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.joint_drive_props)               | 조인트에 적용할 속성입니다.                                                         |
| [`visual_material_path`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.visual_material_path)         | 프림에 사용할 시각적 재료의 경로입니다.                                        |
| [`visual_material`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.visual_material)                   | URDF 파일의 시각적 재료 속성을 재정의할 시각적 재료 속성입니다. |
| [`variants`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.variants)                                 | 입력 USD 파일에서 선택할 변형입니다.                                          |
| [`usd_path`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.usd_path)                                 | 에셋을 생성할 USD 파일의 경로 또는 경로 목록입니다.                           |
| [`random_choice`](#isaaclab.sim.spawners.wrappers.MultiUsdFileCfg.random_choice)                       | 에셋 구성에서 무작위로 선택할지 여부입니다.                                      |

#### visible *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 에셋이 표시되어야 하는지 여부입니다. 기본값은 True입니다.

#### semantic_tags *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]] | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 에셋에 추가할 의미 태그 목록입니다. 기본값은 None으로, 이는 의미 태그가 추가되지 않음을 의미합니다.

의미 태그는 Replicator 의미 태깅 시스템을 따릅니다. 각 태그는 `(type, data)` 형식의 튜플이며, 여기서 `type`은 태그의 유형이고 `data`는 태그와 관련된 의미 라벨입니다. 예를 들어, 생성된 에셋을 `avocado` 클래스로 주석 처리하려면 의미 태그는 다음과 같이 지정됩니다.
`[("class", "avocado")]`.

여러 의미 태그는 태그 목록을 전달하여 지정할 수 있습니다. 예를 들어, 생성된 에셋을 `avocado` 클래스와 `green` 색으로 주석 처리하려면 의미 태그는 다음과 같이 지정됩니다.
`[("class", "avocado"), ("color", "green")]`.

#### 참조
의미 필터에 대한 자세한 내용은 [의미 스키마 편집기](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### copy_from_source *: [bool](https://docs.python.org/3/library/functions.html#bool)*

소스 프림에서 에셋을 복사할지 아니면 상속받을지 여부입니다. 기본값은 True입니다.

이 매개변수는 프림 복제 시에만 사용됩니다. False이면 에셋이 소스 프림에서 상속받게 되며, 즉 소스 프림의 모든 USD 변경 사항이 복제된 프림에 반영됩니다.

#### mass_props *: [schemas.MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

질량 속성.

#### deformable_props *: [schemas.DeformableBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.DeformableBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

변형 가능한 물체 속성.

#### rigid_props *: [schemas.RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

강체 물체 속성.

강체 물체를 정적으로 만들기 위해 `schemas.RigidBodyPropertiesCfg.kinematic_enabled`을 True로 설정합니다. 이렇게 하면 물체가 정적으로 만들어지고 중력이나 기타 힘의 영향을 받지 않게 됩니다.

#### collision_props *: [schemas.CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

모든 충돌 메시시에 적용할 속성.

#### activate_contact_sensors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모든 강체에서 접촉 보고를 활성화합니다. 기본값은 False입니다.

주어진 prim 경로 및 그 하위 항목의 모든 강체에 PhysxContactReporter API를 추가합니다.

#### scale *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

자산의 규모. 기본값은 None이며, 이 경우 규모는 수정되지 않습니다.

#### articulation_props *: [schemas.ArticulationRootPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.ArticulationRootPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

관절 루트에 적용할 속성.

#### fixed_tendons_props *: [schemas.FixedTendonPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.FixedTendonPropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

고정 테넌(존재하는 경우)에 적용할 속성.

#### spatial_tendons_props *: schemas.SpatialTendonPropertiesCfg | [None](https://docs.python.org/3/library/constants.html#None)*

공간 테넌(존재하는 경우)에 적용할 속성.

#### joint_drive_props *: [schemas.JointDrivePropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.JointDrivePropertiesCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

관절에 적용할 속성.

#### NOTE
관절 드라이브 속성은 자산의 모든 관절 드라이브의 USD 속성을 설정합니다.
이 속성은 가급적 적게 사용하고, 꼭 필요한 경우에만 사용할 것을 권장합니다. 대신, 특정 관절의 관절 드라이브 속성을 설정하기 위해
[`actuators`](isaaclab.assets.md#isaaclab.assets.ArticulationCfg.actuators) 매개변수를 사용하는 것이 좋습니다.

#### visual_material_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

프라임에 사용할 시각적 재질의 경로. 기본값은 “material”입니다.

경로가 상대적인 경우, 프라임의 경로를 기준으로 해석됩니다.
visual_material이 None이 아닌 경우 이 매개변수는 무시됩니다.

#### visual_material *: [materials.VisualMaterialCfg](#isaaclab.sim.spawners.materials.VisualMaterialCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

URDF 파일의 시각적 재질 속성을 재정의하는 시각적 재질 속성.

#### NOTE
None인 경우 시각적 재질이 추가되지 않습니다.

#### variants *: [object](https://docs.python.org/3/library/functions.html#object) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None)*

입력 USD 파일에서 선택할 변형. 기본값은 None이며, 이 경우 변형이 적용되지 않습니다.

configclass 객체일 수 있으며, 이 경우 각 속성은 변형 세트 이름으로 사용되고 지정된 값이 사용되거나,
두 개를 매핑하는 사전일 수 있습니다. 자세한 내용은
`select_usd_variants()` 함수를 참조하십시오.

#### usd_path *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

USD 파일을 통해 자산을 생성할 경로 또는 경로 목록.

#### random_choice *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산 구성을 무작위로 선택할지 여부. 기본값은 True입니다.

False인 경우, 자산 구성은 제공된 리스트 순서대로 생성됩니다.
True인 경우, 각 생성 시 무작위로 자산 구성이 선택됩니다.
