# isaaclab.sim.converters

다양한 파일 유형을 USD로 변환하는 변환기를 포함하는 서브 모듈입니다.

Omniverse에서 다양한 파일 유형을 직접 로드하도록 지원하기 위해 파일을 USD 파일로 변환할 수 있는 변환기 세트를 제공합니다. 변환기는 [`AssetConverterBase`](#isaaclab.sim.converters.AssetConverterBase) 클래스의 서브클래스로 구현됩니다.

현재 지원되는 변환기는 다음과 같습니다:

* [`UrdfConverter`](#isaaclab.sim.converters.UrdfConverter): URDF 파일을 USD 파일로 변환합니다.
* [`MeshConverter`](#isaaclab.sim.converters.MeshConverter): 메시 파일을 USD 파일로 변환합니다. OBJ, STL 및 FBX 파일을 지원합니다.

### 클래스

| [`AssetConverterBase`](#isaaclab.sim.converters.AssetConverterBase)       | 다양한 형식의 애셋 파일을 USD 형식으로 변환하는 기본 클래스입니다.   |
|---------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| [`AssetConverterBaseCfg`](#isaaclab.sim.converters.AssetConverterBaseCfg) | 애셋 변환기의 기본 구성 클래스입니다.                                |
| [`MeshConverter`](#isaaclab.sim.converters.MeshConverter)                 | OBJ / STL / FBX 형식의 메시 파일을 USD 파일로 변환하는 변환기입니다.                |
| [`MeshConverterCfg`](#isaaclab.sim.converters.MeshConverterCfg)           | MeshConverter의 구성 클래스입니다.                                        |
| [`UrdfConverter`](#isaaclab.sim.converters.UrdfConverter)                 | URDF 설명 파일을 USD 파일로 변환하는 변환기입니다.                              |
| [`UrdfConverterCfg`](#isaaclab.sim.converters.UrdfConverterCfg)           | UrdfConverter의 구성 클래스입니다.                                        |
| [`MjcfConverter`](#isaaclab.sim.converters.MjcfConverter)                 | MJCF 설명 파일을 USD 파일로 변환하는 변환기입니다.                              |
| [`MjcfConverterCfg`](#isaaclab.sim.converters.MjcfConverterCfg)           | MjcfConverter의 구성 클래스입니다.                                        |

## 애셋 변환기 베이스

### *class* isaaclab.sim.converters.AssetConverterBase

다양한 형식의 애셋 파일을 USD 형식으로 변환하는 기본 클래스입니다.

이 클래스는 애셋 파일을 USD로 변환하기 위한 공통 인터페이스를 제공합니다. 변환 구현은 제공하지 않으며, 파생 클래스는 실제 변환을 제공하기 위해 `_convert_asset()` 메서드를 구현해야 합니다.

출력 디렉터리([`AssetConverterBaseCfg.usd_dir`](#isaaclab.sim.converters.AssetConverterBaseCfg.usd_dir))가 제공된 경우 파일 변환이 지연됩니다. 지연 변환에서는 다음 조건에 해당할 때만 USD 파일이 다시 생성됩니다:

* 애셋 파일이 수정된 경우.
* 구성 매개변수가 수정된 경우.
* USD 파일이 존재하지 않는 경우.

이 동작을 재정의하여 강제 변환을 수행하려면 [`AssetConverterBaseCfg.force_usd_conversion`](#isaaclab.sim.converters.AssetConverterBaseCfg.force_usd_conversion) 플래그를 True로 설정할 수 있습니다.

출력 디렉터리가 정의되지 않은 경우, 지연 변환이 비활성화되고 생성된 USD 파일은 `/tmp/IsaacLab/usd_{date}_{time}_{random}` 폴더에 저장됩니다. 여기서 중괄호 안의 매개변수는 런타임에 생성됩니다. 랜덤 식별자는 두 개의 동시 변환이 동일한 디렉터리를 읽기/쓰기 위해 경쟁하는 경합 조건을 방지하는 데 도움이 됩니다.

#### NOTE
[`AssetConverterBaseCfg.asset_path`](#isaaclab.sim.converters.AssetConverterBaseCfg.asset_path), [`AssetConverterBaseCfg.usd_dir`](#isaaclab.sim.converters.AssetConverterBaseCfg.usd_dir), 및
[`AssetConverterBaseCfg.usd_file_name`](#isaaclab.sim.converters.AssetConverterBaseCfg.usd_file_name) 매개변수의 변경은 USD 파일 재생성을 트리거하는 구성 인스턴스의 수정이 아닌 것으로 간주됩니다.

**메서드:**

| [`__init__`](#isaaclab.sim.converters.AssetConverterBase.__init__)(cfg)   | 클래스를 초기화합니다.   |
|---------------------------------------------------------------------------|--------------------------|

**속성:**

| [`usd_dir`](#isaaclab.sim.converters.AssetConverterBase.usd_dir)                                           | 생성된 USD 파일이 저장되는 디렉터리의 절대 경로입니다.   |
|------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`usd_file_name`](#isaaclab.sim.converters.AssetConverterBase.usd_file_name)                               | 생성된 USD 파일의 파일 이름입니다.                                       |
| [`usd_path`](#isaaclab.sim.converters.AssetConverterBase.usd_path)                                         | 생성된 USD 파일의 절대 경로입니다.                                   |
| [`usd_instanceable_meshes_path`](#isaaclab.sim.converters.AssetConverterBase.usd_instanceable_meshes_path) | 메시를 포함한 USD 파일의 상대 경로입니다.                                 |

#### \_\_init_\_(cfg: [AssetConverterBaseCfg](#isaaclab.sim.converters.AssetConverterBaseCfg))

클래스를 초기화합니다.

* **매개변수:**
  **cfg** – 애셋 파일을 USD 형식으로 변환하기 위한 구성 인스턴스입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 애셋 파일이 존재하지 않을 경우 발생합니다.

#### *property* usd_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일이 저장되는 디렉터리의 절대 경로입니다.

#### *property* usd_file_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일의 파일 이름입니다.

#### *property* usd_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일의 절대 경로입니다.

#### *property* usd_instanceable_meshes_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

메시를 포함한 USD 파일의 상대 경로입니다.

이 경로는 USD 디렉터리 [`usd_dir`](#isaaclab.sim.converters.AssetConverterBase.usd_dir)를 기준으로 합니다. 이렇게 함으로써 생성된 USD 파일의 메시 참조가 상대적으로 해결되도록 보장합니다. 그렇지 않으면 다른 위치로 USD 애셋을 이동하는 것이 어렵게 됩니다.

### *class* isaaclab.sim.converters.AssetConverterBaseCfg

애셋 변환기의 기본 구성 클래스입니다.

**속성:**

| [`asset_path`](#isaaclab.sim.converters.AssetConverterBaseCfg.asset_path)                     | USD로 변환할 애셋 파일의 절대 경로입니다.   |
|-----------------------------------------------------------------------------------------------|------------------------------------------------------------|
| [`usd_dir`](#isaaclab.sim.converters.AssetConverterBaseCfg.usd_dir)                           | 생성된 USD 파일을 저장할 출력 디렉터리 경로입니다. |
| [`usd_file_name`](#isaaclab.sim.converters.AssetConverterBaseCfg.usd_file_name)               | 생성된 usd 파일의 이름입니다.                        |
| [`force_usd_conversion`](#isaaclab.sim.converters.AssetConverterBaseCfg.force_usd_conversion) | 애셋 파일의 usd 변환을 강제합니다.             |
| [`make_instanceable`](#isaaclab.sim.converters.AssetConverterBaseCfg.make_instanceable)       | 생성된 USD 파일을 인스턴스 가능하도록 만듭니다.                  |

#### asset_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

USD로 변환할 애셋 파일의 절대 경로입니다.

#### usd_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 USD 파일을 저장할 출력 디렉터리 경로입니다. 기본값은 None입니다.

None인 경우, `/tmp/IsaacLab/usd_{date}_{time}_{random}`로 해결되며, 여기서 중괄호 안의 매개변수는 런타임에 생성됩니다.

#### usd_file_name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 usd 파일의 이름입니다. 기본값은 None입니다.

None인 경우, 애셋 파일 이름에서 해결됩니다. 예를 들어 애셋 파일 이름이 `"my_asset.urdf"`이면 생성된 USD 파일 이름은 `"my_asset.usd"`가 됩니다.

제공된 파일 이름이 “.usd” 또는 “.usda”로 끝나지 않는 경우, 파일 이름에 확장자 “.usd”가 추가됩니다.

#### force_usd_conversion *: [bool](https://docs.python.org/3/library/functions.html#bool)*

애셋 파일의 usd 변환을 강제합니다. 기본값은 False입니다.

True인 경우, USD 파일이 항상 생성되며 기존 USD 파일이 존재하면 덮어씁니다.

#### make_instanceable *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 USD 파일을 인스턴스 가능하도록 만듭니다. 기본값은 True입니다.

#### NOTE
인스턴싱은 씬에서 애셋의 여러 복사본이 사용될 때 메모리 사용량을 줄이는 데 도움이 됩니다. 자세한 내용은 USD 문서의 [scene-graph instancing](https://openusd.org/dev/api/_usd__page__scenegraph_instancing.html)을 참조하세요.

## 메시 변환기

### *class* isaaclab.sim.converters.MeshConverter

Bases: [`AssetConverterBase`](#isaaclab.sim.converters.AssetConverterBase)

OBJ / STL / FBX 형식의 메시 파일을 USD 파일로 변환하는 변환기입니다.

이 클래스는 [omni.kit.asset_converter](https://docs.omniverse.nvidia.com/extensions/latest/ext_asset-converter.html) 확장을 래핑하여 메시에서 USD로의 변환에 대한 지연 구현을 제공합니다. 일반적으로 학습 관련 애플리케이션에서 사용되는 인스턴스 가능한 형식으로 출력 USD 파일을 저장합니다.

애셋을 인스턴스 가능하게 만들려면 USD scene-graph 인스턴싱 및 물리 작동 방식에 따른 특정 구조를 따라야 합니다. 강체 구성 요소를 참조 애셋(즉, 프로토타입 프리임 자체)이 아닌 각 인스턴스에 추가해야 합니다. 이는 강체 구성 요소가
properties가 특정 인스턴스에 특화되어 참조된 자산에 공유될 수 없습니다. 자세한 내용은 [문서](https://docs.isaacsim.omniverse.nvidia.com/latest/physics/simulation_fundamentals.html#rigid-body)를 참조하세요.

따라서 다음 구조를 따릅니다:

* `{prim_path}` - Xform이며 강체 및 mass API(구성된 경우)를 갖는 루트 프라임입니다.
* `{prim_path}/geometry` - 메시를 포함하고 구성된 경우 선택적으로 재료도 포함하는 프라임입니다.
  인스턴싱이 활성화된 경우, 이 프라임은 프로토타입 프라임을 참조하는 인스턴스 가능한 참조가 됩니다.

**속성:**

| [`cfg`](#isaaclab.sim.converters.MeshConverter.cfg)                                                   | 메시를 USD로 변환하는 구성 인스턴스입니다.                       |
|-------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| [`usd_dir`](#isaaclab.sim.converters.MeshConverter.usd_dir)                                           | 생성된 USD 파일이 저장되는 디렉터리의 절대 경로입니다.           |
| [`usd_file_name`](#isaaclab.sim.converters.MeshConverter.usd_file_name)                               | 생성된 USD 파일의 파일 이름입니다.                               |
| [`usd_instanceable_meshes_path`](#isaaclab.sim.converters.MeshConverter.usd_instanceable_meshes_path) | 메시를 포함한 USD 파일의 상대 경로입니다.                        |
| [`usd_path`](#isaaclab.sim.converters.MeshConverter.usd_path)                                         | 생성된 USD 파일의 절대 경로입니다.                               |

**메서드:**

| [`__init__`](#isaaclab.sim.converters.MeshConverter.__init__)(cfg)   | 클래스를 초기화합니다.   |
|----------------------------------------------------------------------|--------------------------|

#### cfg *: [MeshConverterCfg](#isaaclab.sim.converters.MeshConverterCfg)*

메시를 USD로 변환하는 구성 인스턴스입니다.

#### \_\_init_\_(cfg: [MeshConverterCfg](#isaaclab.sim.converters.MeshConverterCfg))

클래스를 초기화합니다.

* **매개변수:**
  **cfg** – 메시를 USD로 변환하는 구성 인스턴스입니다.

#### *property* usd_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일이 저장되는 디렉터리의 절대 경로입니다.

#### *property* usd_file_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일의 파일 이름입니다.

#### *property* usd_instanceable_meshes_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

메시를 포함한 USD 파일의 상대 경로입니다.

이 경로는 USD 디렉터리 [`usd_dir`](#isaaclab.sim.converters.MeshConverter.usd_dir)를 기준으로 상대적입니다. 이렇게 함으로써 생성된 USD 파일의 메쉬 참조가 상대적으로 해결되도록 보장합니다. 그렇지 않으면 USD 자산을 다른 위치로 이동하는 것이 어려워집니다.

#### *property* usd_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일의 절대 경로입니다.

### *class* isaaclab.sim.converters.MeshConverterCfg

Bases: [`AssetConverterBaseCfg`](#isaaclab.sim.converters.AssetConverterBaseCfg)

MeshConverter의 구성 클래스입니다.

**속성:**

| [`mass_props`](#isaaclab.sim.converters.MeshConverterCfg.mass_props)                     | USD에 적용할 질량 속성입니다.                                       |
|------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [`rigid_props`](#isaaclab.sim.converters.MeshConverterCfg.rigid_props)                   | USD에 적용할 강체 속성입니다.                                        |
| [`collision_props`](#isaaclab.sim.converters.MeshConverterCfg.collision_props)           | USD에 적용할 충돌 속성입니다.                                        |
| [`asset_path`](#isaaclab.sim.converters.MeshConverterCfg.asset_path)                     | USD로 변환할 자산 파일의 절대 경로입니다.                           |
| [`usd_dir`](#isaaclab.sim.converters.MeshConverterCfg.usd_dir)                           | 생성된 USD 파일을 저장할 출력 디렉터리 경로입니다.                  |
| [`usd_file_name`](#isaaclab.sim.converters.MeshConverterCfg.usd_file_name)               | 생성된 USD 파일의 이름입니다.                                       |
| [`force_usd_conversion`](#isaaclab.sim.converters.MeshConverterCfg.force_usd_conversion) | 자산 파일을 USD로 변환하도록 강제합니다.                            |
| [`make_instanceable`](#isaaclab.sim.converters.MeshConverterCfg.make_instanceable)       | 생성된 USD 파일을 인스턴스 가능하게 만듭니다.                       |
| [`mesh_collision_props`](#isaaclab.sim.converters.MeshConverterCfg.mesh_collision_props) | USD의 모든 충돌 메시에 적용할 메쉬 근사 속성입니다.               |
| [`translation`](#isaaclab.sim.converters.MeshConverterCfg.translation)                   | 원점까지의 메쉬 변환입니다.                                         |
| [`rotation`](#isaaclab.sim.converters.MeshConverterCfg.rotation)                         | 쿼터니언 형식(w, x, y, z)의 메쉬 회전입니다.                        |
| [`scale`](#isaaclab.sim.converters.MeshConverterCfg.scale)                               | 메쉬의 배율입니다.                                                  |

#### mass_props *: [MassPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.MassPropertiesCfg)*

USD에 적용할 질량 속성입니다. 기본값은 None입니다.

#### NOTE
None인 경우, 질량 속성이 추가되지 않습니다.

#### rigid_props *: [RigidBodyPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg)*

USD에 적용할 강체 속성입니다. 기본값은 None입니다.

#### NOTE
None인 경우, 강체 속성이 추가되지 않습니다.

#### collision_props *: [CollisionPropertiesCfg](isaaclab.sim.schemas.md#isaaclab.sim.schemas.CollisionPropertiesCfg)*

USD에 적용할 충돌 속성입니다. 기본값은 None입니다.

#### NOTE
None인 경우, 충돌 속성이 추가되지 않습니다.

#### asset_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

USD로 변환할 자산 파일의 절대 경로입니다.

#### usd_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 USD 파일을 저장할 출력 디렉터리 경로입니다. 기본값은 None입니다.

None인 경우, `/tmp/IsaacLab/usd_{date}_{time}_{random}`로 해결되며, 여기서 중괄호 안의 매개변수는 런타임에 생성됩니다.

#### usd_file_name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 USD 파일의 이름입니다. 기본값은 None입니다.

None인 경우, 자산 파일 이름에서 해결됩니다. 예를 들어 자산 파일 이름이 `"my_asset.urdf"`이면, 생성된 USD 파일 이름은 `"my_asset.usd"`가 됩니다.

파일 이름이 “.usd” 또는 “.usda”로 끝나지 않는 경우, 확장자 “.usd”가 파일 이름에 추가됩니다.

#### force_usd_conversion *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산 파일을 USD로 변환하도록 강제합니다. 기본값은 False입니다.

True인 경우, USD 파일이 항상 생성되며, 존재하면 기존 USD 파일을 덮어씁니다.

#### make_instanceable *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 USD 파일을 인스턴스 가능하게 만듭니다. 기본값은 True입니다.

#### NOTE
인스턴싱은 장면에서 여러 복사본의 자산을 사용할 때 메모리 사용량을 줄이는 데 도움이 됩니다. 자세한 내용은 USD 문서의 [scene-graph instancing](https://openusd.org/dev/api/_usd__page__scenegraph_instancing.html)를 참조하세요.

#### mesh_collision_props *: MeshCollisionPropertiesCfg*

USD의 모든 충돌 메시에 적용할 메쉬 근사 속성입니다.
.. note:: None인 경우, 메쉬 근사 속성이 추가되지 않습니다.

#### translation *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

원점까지의 메쉬 변환입니다. 기본값은 (0.0, 0.0, 0.0)입니다.

#### rotation *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

쿼터니언 형식(w, x, y, z)의 메쉬 회전입니다. 기본값은 (1.0, 0.0, 0.0, 0.0)입니다.

#### scale *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

메쉬의 배율입니다. 기본값은 (1.0, 1.0, 1.0)입니다.

## URDF 변환기

### *class* isaaclab.sim.converters.UrdfConverter

Bases: [`AssetConverterBase`](#isaaclab.sim.converters.AssetConverterBase)

URDF 설명 파일을 USD 파일로 변환하는 변환기입니다.

이 클래스는 [isaacsim.asset.importer.urdf](https://docs.isaacsim.omniverse.nvidia.com/latest/importer_exporter/ext_isaacsim_asset_importer_urdf.html) 확장을 래핑하여 URDF에서 USD로의 변환을 위한 구현을 제공합니다. 일반적으로 학습 관련 애플리케이션에서 사용되는 형식인 인스턴스 가능한 형식으로 출력 USD 파일을 저장합니다.

#### NOTE
Isaac Sim 4.5부터 확장 이름이 `omni.importer.urdf`에서 `isaacsim.asset.importer.urdf`로 변경되었습니다.

#### NOTE
In Isaac Sim 5.1, URDF 임포터에서 고정 관절 병합 동작의 기본 동작이 변경되었습니다.
`fixed_joint` 요소로 연결된 링크는 URDF 링크 항목에 질량과 관성 모멘트가 지정된 경우, `merge-joint`가 True로 설정되어도 병합되지 않습니다.
새 동작은 질량/관성 모멘트가 있는 링크를 0질량 기준 프레임이 아닌 완전한 몸체로 취급합니다.

하위 호환성을 유지하기 위해, **이 변환기는 기본적으로 고정 관절을 병합하는 기존 버전의 URDF 임포터 확장 기능**(버전 2.4.31)으로 고정됩니다.
이를 통해 기존 URDF는 수정 없이도 예상대로 작동합니다.

**속성:**

| [`cfg`](#isaaclab.sim.converters.UrdfConverter.cfg)                                                   | URDF에서 USD로의 변환 구성 인스턴스입니다.                       |
|-------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| [`usd_dir`](#isaaclab.sim.converters.UrdfConverter.usd_dir)                                           | 생성된 USD 파일이 저장된 디렉터리의 절대 경로입니다.             |
| [`usd_file_name`](#isaaclab.sim.converters.UrdfConverter.usd_file_name)                               | 생성된 USD 파일의 파일 이름입니다.                               |
| [`usd_instanceable_meshes_path`](#isaaclab.sim.converters.UrdfConverter.usd_instanceable_meshes_path) | 메시가 포함된 USD 파일의 상대 경로입니다.                        |
| [`usd_path`](#isaaclab.sim.converters.UrdfConverter.usd_path)                                         | 생성된 USD 파일의 절대 경로입니다.                               |

**메서드:**

| [`__init__`](#isaaclab.sim.converters.UrdfConverter.__init__)(cfg)   | 클래스를 초기화합니다.   |
|----------------------------------------------------------------------|--------------------------|

#### cfg *: [UrdfConverterCfg](#isaaclab.sim.converters.UrdfConverterCfg)*

URDF에서 USD로의 변환 구성 인스턴스입니다.

#### \_\_init_\_(cfg: [UrdfConverterCfg](#isaaclab.sim.converters.UrdfConverterCfg))

클래스를 초기화합니다.

* **매개변수:**
  **cfg** – URDF에서 USD로의 변환 구성 인스턴스입니다.

#### *property* usd_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일이 저장된 디렉터리의 절대 경로입니다.

#### *property* usd_file_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일의 파일 이름입니다.

#### *property* usd_instanceable_meshes_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

메시가 포함된 USD 파일의 상대 경로입니다.

경로는 USD 디렉터리 [`usd_dir`](#isaaclab.sim.converters.UrdfConverter.usd_dir)를 기준으로 합니다. 이렇게 하면 생성된 USD 파일의 메시 참조가 상대적으로 해결됩니다. 그렇지 않으면 USD 자산을 다른 위치로 이동하기 어려워집니다.

#### *property* usd_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일의 절대 경로입니다.

### *class* isaaclab.sim.converters.UrdfConverterCfg

Bases: [`AssetConverterBaseCfg`](#isaaclab.sim.converters.AssetConverterBaseCfg)

UrdfConverter의 구성 클래스입니다.

**클래스:**

| [`JointDriveCfg`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg)   | 조인트 드라이브 구성입니다.   |
|------------------------------------------------------------------------------|------------------------------|

**속성:**

| [`fix_base`](#isaaclab.sim.converters.UrdfConverterCfg.fix_base)                                                           | 루트/베이스 링크에 고정 관절을 생성합니다.                                                    |
|----------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| [`asset_path`](#isaaclab.sim.converters.UrdfConverterCfg.asset_path)                                                       | USD로 변환할 자산 파일의 절대 경로입니다.                                     |
| [`usd_dir`](#isaaclab.sim.converters.UrdfConverterCfg.usd_dir)                                                             | 생성된 USD 파일을 저장할 출력 디렉터리 경로입니다.                                   |
| [`usd_file_name`](#isaaclab.sim.converters.UrdfConverterCfg.usd_file_name)                                                 | 생성된 USD 파일의 이름입니다.                                                          |
| [`force_usd_conversion`](#isaaclab.sim.converters.UrdfConverterCfg.force_usd_conversion)                                   | 자산 파일을 USD로 강제 변환합니다.                                               |
| [`make_instanceable`](#isaaclab.sim.converters.UrdfConverterCfg.make_instanceable)                                         | 생성된 USD 파일을 인스턴스 가능하도록 만듭니다.                                                    |
| [`root_link_name`](#isaaclab.sim.converters.UrdfConverterCfg.root_link_name)                                               | 루트 링크의 이름입니다.                                                                   |
| [`link_density`](#isaaclab.sim.converters.UrdfConverterCfg.link_density)                                                   | URDF에 `"inertial"` 속성이 누락된 링크에 대한 기본 밀도 (`kg/m^3`). |
| [`merge_fixed_joints`](#isaaclab.sim.converters.UrdfConverterCfg.merge_fixed_joints)                                       | 고정 관절로 연결된 링크를 통합합니다.                                        |
| [`convert_mimic_joints_to_normal_joints`](#isaaclab.sim.converters.UrdfConverterCfg.convert_mimic_joints_to_normal_joints) | 모방 관절을 일반 관절로 변환합니다.                                                       |
| [`joint_drive`](#isaaclab.sim.converters.UrdfConverterCfg.joint_drive)                                                     | 조인트 드라이브 설정입니다.                                                                    |
| [`collision_from_visuals`](#isaaclab.sim.converters.UrdfConverterCfg.collision_from_visuals)                               | 시각 기하학에서 충돌 기하학을 생성할지 여부를 결정합니다.                                   |
| [`collider_type`](#isaaclab.sim.converters.UrdfConverterCfg.collider_type)                                                 | 충돌 형태 간소화 방법입니다.                                                          |
| [`self_collision`](#isaaclab.sim.converters.UrdfConverterCfg.self_collision)                                               | 어티큘레이션의 링크 간 자체 충돌을 활성화합니다.                                  |
| [`replace_cylinders_with_capsules`](#isaaclab.sim.converters.UrdfConverterCfg.replace_cylinders_with_capsules)             | 실린더 형태를 캡슐 형태로 바꿉니다.                                                 |

#### *class* JointDriveCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

조인트 드라이브 구성입니다.

**클래스:**

| [`PDGainsCfg`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.PDGainsCfg)                             | 드라이브의 PD 게인 구성입니다.                |
|----------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------|
| [`NaturalFrequencyGainsCfg`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.NaturalFrequencyGainsCfg) | 드라이브의 자연 주파수 게인 구성입니다. |

**속성:**

| [`drive_type`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.drive_type)   | 조인트에 사용되는 드라이브 유형입니다.        |
|--------------------------------------------------------------------------------------|-------------------------------------------|
| [`target_type`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.target_type) | 조인트에 사용되는 드라이브 대상 유형입니다. |
| [`gains`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.gains)             | 드라이브 게인 구성입니다.            |

#### *class* PDGainsCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

드라이브의 PD 게인 구성입니다.

**속성:**

| [`stiffness`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.PDGainsCfg.stiffness)   | 조인트 드라이브의 강성(Nm/rad 또는 N/rad)입니다.       |
|-----------------------------------------------------------------------------------------------|------------------------------------------------------------|
| [`damping`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.PDGainsCfg.damping)       | 조인트 드라이브의 감쇠(Nm/(rad/s) 또는 N/(rad/s))입니다. |

#### stiffness *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)] | [float](https://docs.python.org/3/library/functions.html#float)*

조인트 드라이브의 강성(Nm/rad 또는 N/rad)입니다.

None이면 강성이 URDF 파일에서 파싱된 값으로 설정됩니다.
[`target_type`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.target_type)가 `"velocity"`로 설정된 경우, 이 값은 조인트 속도 공간에서의 드라이브 강도를 결정합니다.

#### damping *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)] | [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

관절 구동의 감쇠係수(Nm/(rad/s) 또는 N/(rad/s)). 기본값은 None입니다.

None이면, 감쇠係수는 URDF 파일에서 파싱된 값으로 설정되거나, URDF에 값이 없으면 0.0으로 설정됩니다.
[`target_type`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.target_type)이 `"velocity"`로 설정된 경우, 이 계수는 0.0으로 설정되고 [`stiffness`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.PDGainsCfg.stiffness)가 관절 속도 공간에서의 구동 강도로 사용됩니다.

#### *class* NaturalFrequencyGainsCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

구동의 자연 주파수 게인에 대한 구성.

다음 공식을 사용하여 원하는 자연 주파수에 기반한 구동 강성과 감쇠係수를 계산합니다:
$P = m \cdot f^2$, $D = 2 \cdot r \cdot f \cdot m$

여기서 $f$는 자연 주파수, $r$는 감쇠 비율, $m$는 관절에서의 총 등가 관성입니다. 감쇠 비율은 다음과 같이 정의됩니다:

* $r = 1.0$은 crítically damped(임계 감쇠) 시스템,
* $r < 1.0$은 underdamped(과소 감쇠),
* $r > 1.0$은 overdamped(과다 감쇠).

**속성:**

| [`natural_frequency`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.NaturalFrequencyGainsCfg.natural_frequency)   | 관절 구동의 자연 주파수.   |
|-----------------------------------------------------------------------------------------------------------------------------|---------------------------------------------|
| [`damping_ratio`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.NaturalFrequencyGainsCfg.damping_ratio)           | 관절 구동의 감쇠 비율.       |

#### natural_frequency *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)] | [float](https://docs.python.org/3/library/functions.html#float)*

관절 구동의 자연 주파수.

[`target_type`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.target_type)이 `"velocity"`로 설정된 경우, 이 값은 관절 속도 공간에서의 구동의 자연 주파수를 결정합니다.

#### damping_ratio *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)] | [float](https://docs.python.org/3/library/functions.html#float)*

관절 구동의 감쇠 비율. 기본값은 0.005입니다.

[`target_type`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.target_type)이 `"velocity"`로 설정된 경우, 이 값은 무시되고 오직 [`natural_frequency`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.NaturalFrequencyGainsCfg.natural_frequency)만 사용됩니다.

#### drive_type *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['acceleration', 'force']] | [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['acceleration', 'force']*

관절에 사용되는 구동 타입. 기본값은 `"force"`입니다.

* `"acceleration"`: 관절 구동은 관성과 질량 변화에 무관하도록 관성을 정규화한 후 구동 토크를 적용합니다(등가 감쇠 진동자와 동일).
* `"force"`: 힘을 통해 토크를 적용하므로 신체 관성의 변화에 영향을 받습니다.

#### target_type *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['none', 'position', 'velocity']] | [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['none', 'position', 'velocity']*

관절에 사용되는 구동 대상 타입. 기본값은 `"position"`입니다.

대상 타입이 `"none"`으로 설정된 경우, 관절 강성과 감쇠係수는 0.0으로 설정됩니다.

#### gains *: [PDGainsCfg](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.PDGainsCfg) | [NaturalFrequencyGainsCfg](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg.NaturalFrequencyGainsCfg)*

구동 게인 구성.

#### fix_base *: [bool](https://docs.python.org/3/library/functions.html#bool)*

루트/베이스 링크에 고정 관절을 생성합니다.

#### asset_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

USD로 변환할 애셋 파일의 절대 경로입니다.

#### usd_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 USD 파일을 저장할 출력 디렉터리 경로입니다. 기본값은 None입니다.

None이면, `/tmp/IsaacLab/usd_{date}_{time}_{random}`로 해결되며, 여기서 중괄호 안의 매개변수는 런타임에 생성됩니다.

#### usd_file_name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 USD 파일의 이름. 기본값은 None입니다.

None이면, 애셋 파일 이름에서 결정됩니다. 예를 들어, 애셋 파일 이름이 `"my_asset.urdf"`이면, 생성된 USD 파일 이름은 `"my_asset.usd"`입니다.

제공된 파일 이름이 “.usd” 또는 “.usda”로 끝나지 않는다면, 파일 이름에 확장자 “.usd”가 추가됩니다.

#### force_usd_conversion *: [bool](https://docs.python.org/3/library/functions.html#bool)*

애셋 파일을 USD로 강제 변환합니다. 기본값은 False입니다.

True이면, USD 파일이 항상 생성되며, 기존 USD 파일이 존재하면 덮어씁니다.

#### make_instanceable *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 USD 파일을 인스턴스 가능하도록 만듭니다. 기본값은 True입니다.

#### NOTE
인스턴싱은 씬에서 여러 복사본의 애셋을 사용할 때 메모리 사용량을 줄이는 데 도움이 됩니다. 자세한 내용은 USD 문서의
[scene-graph instancing](https://openusd.org/dev/api/_usd__page__scenegraph_instancing.html)를 참조하십시오.

#### root_link_name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

루트 링크의 이름. 기본값은 None입니다.

None이면, 루트 링크는 PhysX에 의해 설정됩니다.

#### link_density *: [float](https://docs.python.org/3/library/functions.html#float)*

URDF에 `"inertial"` 속성이 누락된 링크에 대한 기본 밀도(`kg/m^3`). 기본값은 0.0입니다.

#### merge_fixed_joints *: [bool](https://docs.python.org/3/library/functions.html#bool)*

고정 관절로 연결된 링크를 통합합니다. 기본값은 True입니다.

#### convert_mimic_joints_to_normal_joints *: [bool](https://docs.python.org/3/library/functions.html#bool)*

모방 관절을 일반 관절로 변환합니다. 기본값은 False입니다.

#### joint_drive *: [JointDriveCfg](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

관절 구동 설정. 기본값은 [`JointDriveCfg`](#isaaclab.sim.converters.UrdfConverterCfg.JointDriveCfg)입니다.

URDF에 관절이 없는 경우 이 매개변수를 `None`으로 설정할 수 있습니다.

#### collision_from_visuals *: [bool](https://docs.python.org/3/library/functions.html#bool)*

시각 기하학에서 충돌 기하학을 생성할지 여부. 기본값은 False입니다.

#### collider_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['convex_hull', 'convex_decomposition']*

충돌 모양 단순화. 기본값은 `"convex_hull"`입니다.

지원되는 값은 다음과 같습니다:

* `"convex_hull"`: 충돌 모양이 볼록 껍질로 단순화됩니다.
* `"convex_decomposition"`: 충돌 모양이 더 작은 볼록 모양으로 분해되어 더 정확한 맞춤을 제공합니다.

#### self_collision *: [bool](https://docs.python.org/3/library/functions.html#bool)*

아티큘레이션의 링크 간 자체 충돌을 활성화합니다. 기본값은 False입니다.

#### replace_cylinders_with_capsules *: [bool](https://docs.python.org/3/library/functions.html#bool)*

원통 형태를 캡슐 형태로 대체합니다. 기본값은 False입니다.

## MJCF 변환기

### *class* isaaclab.sim.converters.MjcfConverter

Bases: [`AssetConverterBase`](#isaaclab.sim.converters.AssetConverterBase)

MJCF 설명 파일을 USD 파일로 변환하는 변환기입니다.

이 클래스는 [isaacsim.asset.importer.mjcf](https://docs.isaacsim.omniverse.nvidia.com/latest/importer_exporter/ext_isaacsim_asset_importer_mjcf.html) 확장을 둘러싸서 MJCF에서 USD로의 변환을 지연 방식으로 구현합니다. 일반적으로 학습 관련 애플리케이션에서 사용되는 인스턴스 가능한 형식으로 출력 USD 파일을 저장합니다.

#### NOTE
Isaac Sim 4.5부터 확장 이름이 `omni.importer.mjcf`에서 `isaacsim.asset.importer.mjcf`로 변경되었습니다. 이 변환기 클래스는 이제 Isaac Sim의 최신 확장을 사용합니다.

**속성:**

| [`cfg`](#isaaclab.sim.converters.MjcfConverter.cfg)                                                   | MJCF에서 USD로의 변환을 위한 구성 인스턴스.                       |
|-------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| [`usd_dir`](#isaaclab.sim.converters.MjcfConverter.usd_dir)                                           | 생성된 USD 파일이 저장된 디렉터리의 절대 경로. |
| [`usd_file_name`](#isaaclab.sim.converters.MjcfConverter.usd_file_name)                               | 생성된 USD 파일의 파일 이름.                                     |
| [`usd_instanceable_meshes_path`](#isaaclab.sim.converters.MjcfConverter.usd_instanceable_meshes_path) | 메시를 포함한 USD 파일의 상대 경로.                               |
| [`usd_path`](#isaaclab.sim.converters.MjcfConverter.usd_path)                                         | 생성된 USD 파일의 절대 경로.                                 |

**메서드:**

| [`__init__`](#isaaclab.sim.converters.MjcfConverter.__init__)(cfg)   | 클래스를 초기화합니다.   |
|----------------------------------------------------------------------|--------------------------|

#### cfg *: [MjcfConverterCfg](#isaaclab.sim.converters.MjcfConverterCfg)*

MJCF에서 USD로 변환하는 구성 인스턴스입니다.

#### \_\_init_\_(cfg: [MjcfConverterCfg](#isaaclab.sim.converters.MjcfConverterCfg))

클래스를 초기화합니다.

* **매개변수:**
  **cfg** – URDF에서 USD로 변환하는 구성 인스턴스입니다.

#### *property* usd_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일이 저장되는 디렉터리의 절대 경로입니다.

#### *property* usd_file_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일의 파일 이름입니다.

#### *property* usd_instanceable_meshes_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

메시가 포함된 USD 파일의 상대 경로입니다.

경로는 USD 디렉터리 [`usd_dir`](#isaaclab.sim.converters.MjcfConverter.usd_dir)를 기준으로 합니다. 이렇게 하면 생성된 USD 파일의 메시 참조를 상대 경로로 해결할 수 있습니다. 그렇지 않으면 USD 자산을 다른 위치로 이동하기 어려워집니다.

#### *property* usd_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

생성된 USD 파일의 절대 경로입니다.

### *class* isaaclab.sim.converters.MjcfConverterCfg

Bases: [`AssetConverterBaseCfg`](#isaaclab.sim.converters.AssetConverterBaseCfg)

MjcfConverter의 구성 클래스입니다.

**특성:**

| [`link_density`](#isaaclab.sim.converters.MjcfConverterCfg.link_density)                   | 링크에 사용되는 기본 밀도입니다.                             |
|--------------------------------------------------------------------------------------------|-------------------------------------------------------------|
| [`import_inertia_tensor`](#isaaclab.sim.converters.MjcfConverterCfg.import_inertia_tensor) | MJCF에서 관성 텐서를 가져옵니다.                        |
| [`asset_path`](#isaaclab.sim.converters.MjcfConverterCfg.asset_path)                       | USD로 변환할 자산 파일의 절대 경로입니다.    |
| [`usd_dir`](#isaaclab.sim.converters.MjcfConverterCfg.usd_dir)                             | 생성된 USD 파일을 저장할 출력 디렉터리 경로입니다.  |
| [`usd_file_name`](#isaaclab.sim.converters.MjcfConverterCfg.usd_file_name)                 | 생성된 USD 파일의 이름입니다.                         |
| [`force_usd_conversion`](#isaaclab.sim.converters.MjcfConverterCfg.force_usd_conversion)   | 자산 파일을 USD로 강제 변환합니다.              |
| [`make_instanceable`](#isaaclab.sim.converters.MjcfConverterCfg.make_instanceable)         | 생성된 USD 파일을 인스턴스화합니다.                   |
| [`fix_base`](#isaaclab.sim.converters.MjcfConverterCfg.fix_base)                           | 루트/베이스 링크에 고정 조인트를 생성합니다.                   |
| [`import_sites`](#isaaclab.sim.converters.MjcfConverterCfg.import_sites)                   | MJCF에서 사이트를 가져옵니다.                             |
| [`self_collision`](#isaaclab.sim.converters.MjcfConverterCfg.self_collision)               | 어티큘레이션의 링크 간 자기 충돌을 활성화합니다. |

#### link_density *: [float](https://docs.python.org/3/library/functions.html#float)*

링크에 사용되는 기본 밀도입니다. 기본값은 0입니다.

이 설정은 MJCF에 `"inertial"` 속성이 누락된 경우에만 효과적입니다.

#### import_inertia_tensor *: [bool](https://docs.python.org/3/library/functions.html#bool)*

MJCF에서 관성 텐서를 가져옵니다. 기본값은 True입니다.

`"inertial"` 태그가 누락된 경우, 항등 행렬로 가져옵니다.

#### asset_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

USD로 변환할 자산 파일의 절대 경로입니다.

#### usd_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 USD 파일을 저장할 출력 디렉터리 경로입니다. 기본값은 None입니다.

None인 경우, `/tmp/IsaacLab/usd_{date}_{time}_{random}`로 해결되며, 여기서 중괄호 안의 매개변수는 런타임 중에 생성됩니다.

#### usd_file_name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

생성된 USD 파일의 이름입니다. 기본값은 None입니다.

None인 경우, 자산 파일 이름에서 해결됩니다. 예를 들어 자산 파일 이름이 `"my_asset.urdf"`이면, 생성된 USD 파일 이름은 `"my_asset.usd"`입니다.

제공된 파일 이름이 `.usd` 또는 `.usda`로 끝나지 않는 경우, 파일 이름에 확장자 `.usd`가 추가됩니다.

#### force_usd_conversion *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산 파일을 USD로 강제 변환합니다. 기본값은 False입니다.

True인 경우, USD 파일이 항상 생성되며, 기존에 존재하는 USD 파일은 덮어씁니다.

#### make_instanceable *: [bool](https://docs.python.org/3/library/functions.html#bool)*

생성된 USD 파일을 인스턴스화합니다. 기본값은 True입니다.

#### NOTE
인스턴스화를 통해 장면에서 자산의 여러 복사본을 사용할 때 메모리 사용량을 줄일 수 있습니다. 자세한 내용은 USD 문서의 [scene-graph instancing](https://openusd.org/dev/api/_usd__page__scenegraph_instancing.html)를 참조하십시오.

#### fix_base *: [bool](https://docs.python.org/3/library/functions.html#bool)*

루트/베이스 링크에 고정 조인트를 생성합니다. 기본값은 True입니다.

#### import_sites *: [bool](https://docs.python.org/3/library/functions.html#bool)*

MJCF에서 사이트를 가져옵니다. 기본값은 True입니다.

#### self_collision *: [bool](https://docs.python.org/3/library/functions.html#bool)*

어티큘레이션의 링크 간 자기 충돌을 활성화합니다. 기본값은 False입니다.
