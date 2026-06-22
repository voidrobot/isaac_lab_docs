# isaaclab.managers

환경 관리자를 위한 서브 모듈입니다.

관리자들은 환경의 다양한 측면, 즉 무작위화 이벤트, 커리큘럼, 그리고 관측값을 처리하는 데 사용됩니다. 각 관리자는 환경에 대한 특정 기능을 구현합니다. 관리자들은 모듈식으로 설계되어 새로운 기능을 쉽게 확장할 수 있도록 설계되었습니다.

### 클래스

| [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg)           | 관리자 용어에서 사용되는 씬 엔티티에 대한 구성입니다.       |
|-----------------------------------------------------------------|----------------------------------------------------------------------------|
| [`ManagerBase`](#isaaclab.managers.ManagerBase)                 | 모든 관리자의 기본 클래스입니다.                                               |
| [`ManagerTermBase`](#isaaclab.managers.ManagerTermBase)         | 관리자 용어의 기본 클래스입니다.                                              |
| [`ManagerTermBaseCfg`](#isaaclab.managers.ManagerTermBaseCfg)   | 관리자 용어에 대한 구성입니다.                                          |
| [`ObservationManager`](#isaaclab.managers.ObservationManager)   | 주어진 세계에 대한 관측 신호를 계산하는 관리자입니다.               |
| [`ObservationGroupCfg`](#isaaclab.managers.ObservationGroupCfg) | 관측 그룹에 대한 구성입니다.                                    |
| [`ObservationTermCfg`](#isaaclab.managers.ObservationTermCfg)   | 관측 용어에 대한 구성입니다.                                     |
| [`ActionManager`](#isaaclab.managers.ActionManager)             | 주어진 세계에 대한 행동을 처리하고 적용하는 관리자입니다.             |
| [`ActionTerm`](#isaaclab.managers.ActionTerm)                   | 행동 용어의 기본 클래스입니다.                                               |
| [`ActionTermCfg`](#isaaclab.managers.ActionTermCfg)             | 행동 용어에 대한 구성입니다.                                          |
| [`EventManager`](#isaaclab.managers.EventManager)               | 다양한 시뮬레이션 이벤트에 기반한 작업을 조율하는 관리자입니다. |
| [`EventTermCfg`](#isaaclab.managers.EventTermCfg)               | 이벤트 용어에 대한 구성입니다.                                            |
| [`CommandManager`](#isaaclab.managers.CommandManager)           | 명령을 생성하는 관리자입니다.                                           |
| [`CommandTerm`](#isaaclab.managers.CommandTerm)                 | 명령 용어를 구현하기 위한 기본 클래스입니다.                            |
| [`CommandTermCfg`](#isaaclab.managers.CommandTermCfg)           | 명령 생성기 용어에 대한 구성입니다.                                |
| [`RewardManager`](#isaaclab.managers.RewardManager)             | 주어진 세계에 대한 보상 신호를 계산하는 관리자입니다.                    |
| [`RewardTermCfg`](#isaaclab.managers.RewardTermCfg)             | 보상 용어에 대한 구성입니다.                                           |
| [`TerminationManager`](#isaaclab.managers.TerminationManager)   | 주어진 세계에 대한 종료 신호를 계산하는 관리자입니다.                      |
| [`TerminationTermCfg`](#isaaclab.managers.TerminationTermCfg)   | 종료 용어에 대한 구성입니다.                                      |
| [`CurriculumManager`](#isaaclab.managers.CurriculumManager)     | 특정 커리큘럼을 구현하고 실행하는 관리자입니다.                       |
| [`CurriculumTermCfg`](#isaaclab.managers.CurriculumTermCfg)     | 커리큘럼 용어에 대한 구성입니다.                                       |
| [`RecorderManager`](#isaaclab.managers.RecorderManager)         | 기록기 용어로부터 데이터를 기록하는 관리자입니다.                            |
| [`RecorderTermCfg`](#isaaclab.managers.RecorderTermCfg)         | 기록기 용어에 대한 구성입니다.                                        |

## 씬 엔티티

### *class* isaaclab.managers.SceneEntityCfg

관리자 용어에서 사용되는 씬 엔티티에 대한 구성입니다.

이 클래스는 `InteractiveScene`에서 쿼리된 씬 엔티티의 이름을 지정하고 관리자 용어 함수에 전달하는 데 사용됩니다.

**속성:**

| [`name`](#isaaclab.managers.SceneEntityCfg.name)                                       | 씬 엔티티의 이름입니다.                                                                                     |
|----------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| [`joint_names`](#isaaclab.managers.SceneEntityCfg.joint_names)                         | 씬 엔티티의 관절 이름입니다.                                                                    |
| [`joint_ids`](#isaaclab.managers.SceneEntityCfg.joint_ids)                             | 용어가 요구하는 에셋의 관절 인덱스입니다.                                                    |
| [`fixed_tendon_names`](#isaaclab.managers.SceneEntityCfg.fixed_tendon_names)           | 씬 엔티티의 고정 건 명입니다.                                                             |
| [`fixed_tendon_ids`](#isaaclab.managers.SceneEntityCfg.fixed_tendon_ids)               | 용어가 요구하는 에셋의 고정 건 인덱스입니다.                                             |
| [`body_names`](#isaaclab.managers.SceneEntityCfg.body_names)                           | 용어가 요구하는 에셋의 몸체 이름입니다.                                                      |
| [`body_ids`](#isaaclab.managers.SceneEntityCfg.body_ids)                               | 용어가 요구하는 에셋의 몸체 인덱스입니다.                                                    |
| [`object_collection_names`](#isaaclab.managers.SceneEntityCfg.object_collection_names) | 용어가 요구하는 강체 객체 컬렉션의 객체 이름입니다.                                     |
| [`object_collection_ids`](#isaaclab.managers.SceneEntityCfg.object_collection_ids)     | 용어가 요구하는 강체 객체 컬렉션의 객체 인덱스입니다.                                 |
| [`preserve_order`](#isaaclab.managers.SceneEntityCfg.preserve_order)                   | 지정된 관절, 몸체, 또는 객체 컬렉션 이름과 일치하도록 인덱스 순서를 보존할지 여부입니다. |

**메서드:**

| [`resolve`](#isaaclab.managers.SceneEntityCfg.resolve)(scene)   | 씬 엔티티를 해결하고 관절 및 몸체 이름을 인덱스로 변환합니다.   |
|-----------------------------------------------------------------|-------------------------------------------------------------------------------|

#### name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이 이름은 씬 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하십시오.

#### joint_names *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None)*

씬 엔티티의 관절 이름입니다. 기본값은 None입니다.

이 이름은 관절 이름이나 관절 이름을 매치하는 정규 표현식일 수 있습니다.

이름은 관리자 초기화 시 관절 인덱스로 변환되어 [`joint_ids`](#isaaclab.managers.SceneEntityCfg.joint_ids)에서 관절 인덱스 목록으로 전달됩니다.

#### joint_ids *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice)*

용어가 요구하는 에셋의 관절 인덱스입니다. 기본값은 `slice(None)`이며, 이는 에셋에 존재할 경우 모든 관절을 의미합니다.

[`joint_names`](#isaaclab.managers.SceneEntityCfg.joint_names)이 지정되면, 관리자 초기화 시 자동으로 채워집니다.

#### fixed_tendon_names *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None)*

씬 엔티티의 고정 건 이름입니다. 기본값은 None입니다.

이 이름은 건 이름이나 건 이름을 매치하는 정규 표현식일 수 있습니다.

이름은 관리자 초기화 시 고정 건 인덱스로 변환되어 [`fixed_tendon_ids`](#isaaclab.managers.SceneEntityCfg.fixed_tendon_ids)에서 고정 건 인덱스 목록으로 전달됩니다.

#### fixed_tendon_ids *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice)*

용어가 요구하는 에셋의 고정 건 인덱스입니다. 기본값은 `slice(None)`이며, 이는 에셋에 존재할 경우 모든 고정 건을 의미합니다.

[`fixed_tendon_names`](#isaaclab.managers.SceneEntityCfg.fixed_tendon_names)이 지정되면, 관리자 초기화 시 자동으로 채워집니다.

#### body_names *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None)*

용어가 요구하는 에셋의 몸체 이름입니다. 기본값은 None입니다.

이 이름은 몸체 이름이나 몸체 이름을 매치하는 정규 표현식일 수 있습니다.

이름은 관리자 초기화 시 몸체 인덱스로 변환되어 [`body_ids`](#isaaclab.managers.SceneEntityCfg.body_ids)에서 몸체 인덱스 목록으로 전달됩니다.

#### body_ids *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice)*

에셋에서 필요한 용어의 본드 인덱스입니다. 기본값은 slice(None)이며, 이는 에셋의 모든 본드를 의미합니다.

[`body_names`](#isaaclab.managers.SceneEntityCfg.body_names)가 지정되면, 관리자 초기화 시 자동으로 채워집니다.

#### object_collection_names *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None)*

용어에서 필요한 강체 객체 컬렉션의 객체 이름입니다. 기본값은 None입니다.

이름은 객체 이름 자체이거나, 컬렉션 내 객체 이름과 일치하는 정규 표현식일 수 있습니다.

관리자 초기화 시, 이러한 이름은 객체 인덱스로 변환되어 [`object_collection_ids`](#isaaclab.managers.SceneEntityCfg.object_collection_ids)의 객체 인덱스 목록으로 전달됩니다.

#### object_collection_ids *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice)*

에셋에서 필요한 용어의 객체 컬렉션 인덱스입니다. 기본값은 slice(None)이며, 이는 컬렉션의 모든 객체를 의미합니다.

[`object_collection_names`](#isaaclab.managers.SceneEntityCfg.object_collection_names)가 지정되면, 관리자 초기화 시 자동으로 채워집니다.

#### preserve_order *: [bool](https://docs.python.org/3/library/functions.html#bool)*

지정된 조인트, 본드 또는 객체 컬렉션 이름과 인덱스 순서를 일치시킬지 여부를 나타냅니다. 기본값은 False입니다.

False이면, 인덱스의 순서는 엔티티의 조인트, 본드 또는 객체 컬렉션에서의 순서와 일치하도록 오름차순으로 정렬됩니다. 그렇지 않으면, 지정된 조인트, 본드 또는 객체 컬렉션 이름의 순서대로 인덱스가 유지됩니다.

자세한 내용은 [`isaaclab.utils.string.resolve_matching_names()`](isaaclab.utils.md#isaaclab.utils.string.resolve_matching_names) 함수를 참조하십시오.

#### NOTE
이 속성은 다음 중 하나가 지정될 때만 사용됩니다:
[`joint_names`](#isaaclab.managers.SceneEntityCfg.joint_names), [`body_names`](#isaaclab.managers.SceneEntityCfg.body_names), 또는 [`object_collection_names`](#isaaclab.managers.SceneEntityCfg.object_collection_names).

#### resolve(scene: [InteractiveScene](isaaclab.scene.md#isaaclab.scene.InteractiveScene))

장면 엔티티를 해결하고 조인트 및 본드 이름을 인덱스로 변환합니다.

이 함수는 `InteractiveScene`에서 장면 엔티티를 검사하여 조인트와 본드의 인덱스와 이름을 해결합니다. 정규 표현식을 해결해야 하므로 비용이 많이 드는 작업이며, 한 번만 호출해야 합니다.

* **매개변수:**
  **scene** – 인터랙티브 장면 인스턴스.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 장면 엔티티를 찾을 수 없는 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `joint_names`와 `joint_ids`가 모두 지정되었지만 일관되지 않은 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `fixed_tendon_names`와 `fixed_tendon_ids`가 모두 지정되었지만 일관되지 않은 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `body_names`와 `body_ids`가 모두 지정되었지만 일관되지 않은 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `object_collection_names`와 `object_collection_ids`가 모두 지정되었고 일관되지 않은 경우.

## 관리자 기본

### *class* isaaclab.managers.ManagerBase

모든 관리자의 기본 클래스입니다.

**메서드:**

| [`__init__`](#isaaclab.managers.ManagerBase.__init__)(cfg, env)                                  | 관리자를 초기화합니다.                                                       |
|--------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [`reset`](#isaaclab.managers.ManagerBase.reset)([env_ids])                                       | 관리자를 재설정하고 현재 타임스텝의 로깅 정보를 반환합니다. |
| [`find_terms`](#isaaclab.managers.ManagerBase.find_terms)(name_keys)                             | 이름을 기반으로 관리자에서 용어를 찾습니다.                                 |
| [`get_active_iterable_terms`](#isaaclab.managers.ManagerBase.get_active_iterable_terms)(env_idx) | 활성 용어를 튜플의 이터러블 시퀀스로 반환합니다.                      |

**속성:**

| [`num_envs`](#isaaclab.managers.ManagerBase.num_envs)         | 환경 수.                  |
|---------------------------------------------------------------|------------------------------------------|
| [`device`](#isaaclab.managers.ManagerBase.device)             | 계산을 수행할 장치. |
| [`active_terms`](#isaaclab.managers.ManagerBase.active_terms) | 활성 용어 이름.                    |

#### \_\_init_\_(cfg: [object](https://docs.python.org/3/library/functions.html#object), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

관리자를 초기화합니다.

이 함수는 구성 객체를 파싱하고 용어를 생성하는 역할을 담당합니다.

시뮬레이션이 실행 중이 아니면, 장면 엔티티는 즉시 해결되지 않습니다. 대신, 시뮬레이션 시작 시까지 해결이 미루어집니다. 이는 관리자가 시뮬레이션 시작 이후에 생성되더라도 장면 엔티티가 해결되도록 하기 위함입니다.

* **매개변수:**
  * **cfg** – 구성 객체. None이면, 관리자는 용어 없이 초기화됩니다.
  * **env** – 환경 인스턴스.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 수.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 수행할 장치.

#### *abstract property* active_terms *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]*

활성 용어 이름.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]

관리자를 재설정하고 현재 타임스텝의 로깅 정보를 반환합니다.

* **매개변수:**
  **env_ids** – 로그를 기록할 환경 ID.
  기본값은 None이며, 모든 환경의 로그를 기록합니다.
* **반환값:**
  로깅 정보를 포함하는 사전.

#### find_terms(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)]) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

이름을 기반으로 관리자에서 용어를 찾습니다.

이 함수는 관리자에서 이름을 기반으로 용어를 검색합니다. 이름은 정규 표현식 또는 정규 표현식 목록으로 지정할 수 있습니다. 검색은 관리자의 활성 용어에서 수행됩니다.

이름 매칭에 대한 자세한 내용은 `resolve_matching_names()` 함수를 참조하십시오.

* **매개변수:**
  **name_keys** – 용어 이름과 일치시키는 정규 표현식 또는 정규 표현식 목록.
* **반환값:**
  입력 키와 일치하는 용어 이름 목록.

#### get_active_iterable_terms(env_idx: [int](https://docs.python.org/3/library/functions.html#int)) → [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [collections.abc.Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]]]

활성 용어를 튜플의 이터러블 시퀀스로 반환합니다.

튜플의 첫 번째 요소는 용어 이름이고, 두 번째 요소는 용어의 원시 값(들)입니다.

* **반환값:**
  활성 용어.

### *class* isaaclab.managers.ManagerTermBase

관리자 용어의 기본 클래스입니다.

관리자 용어 구현은 함수 또는 클래스일 수 있습니다. 용어가 클래스인 경우, 이 기본 클래스를 상속하고 필요한 메서드를 구현해야 합니다.

각 관리자는 [`ManagerBase`](#isaaclab.managers.ManagerBase)를 상속하는 클래스로 구현됩니다. 각 관리자 클래스는 관리자의 구성 용어를 정의하는 해당 구성 클래스도 가져야 합니다. 각 용어는 [`ManagerTermBaseCfg`](#isaaclab.managers.ManagerTermBaseCfg) 클래스 또는 그 하위 클래스를 사용해야 합니다.

관리자를 생성하는 예시 의사코드:

```python
from isaaclab.utils import configclass
from isaaclab.utils.mdp import ManagerBase, ManagerTermBaseCfg


@configclass
class MyManagerCfg:
    my_term_1: ManagerTermBaseCfg = ManagerTermBaseCfg(...)
    my_term_2: ManagerTermBaseCfg = ManagerTermBaseCfg(...)
    my_term_3: ManagerTermBaseCfg = ManagerTermBaseCfg(...)


# 관리자 인스턴스 정의
my_manager = ManagerBase(cfg=ManagerCfg(), env=env)
```

**방법:**

| [`__init__`](#isaaclab.managers.ManagerTermBase.__init__)(cfg, env)   | 관리자 항목을 초기화합니다.   |
|-----------------------------------------------------------------------|--------------------------------|
| [`reset`](#isaaclab.managers.ManagerTermBase.reset)([env_ids])        | 관리자 항목을 재설정합니다.       |
| [`serialize`](#isaaclab.managers.ManagerTermBase.serialize)()         | 일반 시리얼라이즈 호출입니다.    |

**속성:**

| [`num_envs`](#isaaclab.managers.ManagerTermBase.num_envs)   | 환경 수입니다.                  |
|-------------------------------------------------------------|------------------------------------------|
| [`device`](#isaaclab.managers.ManagerTermBase.device)       | 계산을 수행할 장치입니다.       |

#### \_\_init_\_(cfg: [ManagerTermBaseCfg](#isaaclab.managers.ManagerTermBaseCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

관리자 항목을 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체입니다.
  * **env** – 환경 인스턴스입니다.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 수입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 수행할 장치입니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [None](https://docs.python.org/3/library/constants.html#None)

관리자 항목을 재설정합니다.

* **매개변수:**
  **env_ids** – 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 고려됩니다.

#### serialize() → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

일반 시리얼라이즈 호출입니다. 구성 사전을 포함합니다.

### *class* isaaclab.managers.ManagerTermBaseCfg

관리자 항목에 대한 구성입니다.

**속성:**

| [`func`](#isaaclab.managers.ManagerTermBaseCfg.func)     | 항목에 대해 호출될 함수 또는 클래스입니다.                  |
|----------------------------------------------------------|-------------------------------------------------------------------|
| [`params`](#isaaclab.managers.ManagerTermBaseCfg.params) | 함수에 키워드 인수로 전달될 매개변수입니다. |

#### func *: Callable | [ManagerTermBase](#isaaclab.managers.ManagerTermBase)*

항목에 대해 호출될 함수 또는 클래스입니다.

함수는 첫 번째 인수로 환경 객체를 받아야 합니다.
남은 인수는 [`params`](#isaaclab.managers.ManagerTermBaseCfg.params) 속성에 지정된 대로 처리됩니다.

또한 [`__call__()`](https://docs.python.org/3/reference/datamodel.html#object.__call__) 메서드를 구현하는 클래스(즉, 호출 가능한 클래스)를 지원합니다.
이 경우 클래스는 [`ManagerTermBase`](#isaaclab.managers.ManagerTermBase) 클래스를 상속받고 필요한 메서드를 구현해야 합니다.

#### params *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any | [SceneEntityCfg](#isaaclab.managers.SceneEntityCfg)]*

함수에 키워드 인수로 전달될 매개변수입니다. 기본값은 빈 사전입니다.

#### NOTE
값이 [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체인 경우, 관리자는 `InteractiveScene`에서 장면 엔티티를 쿼리하고
[`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체에 지정된 대로 엔티티의 관절 및 본체를 처리합니다.

## 관찰 관리자

### *class* isaaclab.managers.ObservationManager

기반 클래스: [`ManagerBase`](#isaaclab.managers.ManagerBase)

주어진 세계에 대한 관찰 신호를 계산하는 관리자입니다.

관찰은 의도된 사용에 따라 그룹으로 구성됩니다. 이를 통해 비대칭 액터-크리틱 및 학생-교사 학습과 같은 다양한 학습 유형에 대해 다른 관찰 그룹을 가질 수 있습니다.
각 그룹에는 관찰 함수 호출, 노이즈 손상 모델 사용, 센서에서 데이터 검색에 대한 정보를 포함하는 관찰 항목이 포함됩니다.

각 관찰 그룹은 [`ObservationGroupCfg`](#isaaclab.managers.ObservationGroupCfg) 클래스를 상속해야 합니다. 각 그룹 내에서 각 관찰 항목은
[`ObservationTermCfg`](#isaaclab.managers.ObservationTermCfg) 클래스를 인스턴스화해야 합니다. 구성에 따라 그룹의 관찰 항목은 단일 텐서로 연결되거나,
용어의 이름에 해당하는 키를 갖는 사전으로 반환될 수 있습니다.

관찰 항목이 그룹에서 연결되는 경우, 연결된 텐서의 형태는 개별 관찰 항목의 형태를 기반으로 계산됩니다. 이 정보는
[`group_obs_dim`](#isaaclab.managers.ObservationManager.group_obs_dim) 사전에 저장되며, 키는 그룹 이름이고 값은 관찰 텐서의 형태입니다.
그룹의 항목이 연결되지 않은 경우, 속성은 그룹 내 각 항목의 형태 목록을 저장합니다.

#### NOTE
그룹의 관찰 항목이 동일한 형태가 아닌 경우, 관찰 항목을 연결할 수 없습니다. 이 경우,
그룹 구성에서 [`ObservationGroupCfg.concatenate_terms`](#isaaclab.managers.ObservationGroupCfg.concatenate_terms) 속성을 False로 설정하십시오.

관찰 항목에는 또한 기록이 있을 수 있습니다. 이는 시뮬레이션 단계마다 실행 기록이 업데이트됨을 의미합니다.
기록은 [`ObservationTermCfg`](#isaaclab.managers.ObservationTermCfg)별로 제어할 수 있습니다
([`ObservationTermCfg.history_length`](#isaaclab.managers.ObservationTermCfg.history_length) 및
[`ObservationTermCfg.flatten_history_dim`](#isaaclab.managers.ObservationTermCfg.flatten_history_dim) 참조).
기록은 [`ObservationGroupCfg`](#isaaclab.managers.ObservationGroupCfg)를 통해 그룹 수준에서도 제어할 수 있으며,
그룹 구성은 설정된 경우 개별 항목 구성보다 우선합니다. 기록은 가장 오래된 것부터 가장 새로운 순서를 따릅니다.

관찰 관리자는 모든 그룹 또는 특정 그룹에 대한 관찰을 계산하는 데 사용될 수 있습니다.
관찰은 그룹 내 각 항목에 등록된 함수를 호출하여 계산됩니다.
함수는 그룹 내 항목 순서대로 호출됩니다. 함수는 (num_envs, …) 형태의 텐서를 반환해야 합니다.

항목에 노이즈 모델 또는 사용자 지정 수정자가 등록된 경우, 관찰을 손상시키기 위해 함수가 호출됩니다.
손상 함수는 관찰과 동일한 형태의 텐서를 반환해야 합니다.
관찰은 구성 설정에 따라 클리핑 및 스케일링됩니다.

**방법:**

| [`__init__`](#isaaclab.managers.ObservationManager.__init__)(cfg, env)                                  | 관찰 관리자를 초기화합니다.                                               |
|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [`get_active_iterable_terms`](#isaaclab.managers.ObservationManager.get_active_iterable_terms)(env_idx) | 활성 항목을 튜플의 반복 가능한 시퀀스로 반환합니다.                      |
| [`reset`](#isaaclab.managers.ObservationManager.reset)([env_ids])                                       | 관리자를 재설정하고 현재 시간 스텝의 로깅 정보를 반환합니다. |
| [`compute`](#isaaclab.managers.ObservationManager.compute)([update_history])                            | 모든 그룹에 대한 그룹별 관찰을 계산합니다.                            |
| [`compute_group`](#isaaclab.managers.ObservationManager.compute_group)(group_name[, update_history])    | 주어진 그룹에 대한 관찰을 계산합니다.                                  |
| [`serialize`](#isaaclab.managers.ObservationManager.serialize)()                                        | 모든 활성 그룹의 관찰 항목 구성을 시리얼라이즈합니다.          |
| [`find_terms`](#isaaclab.managers.ObservationManager.find_terms)(name_keys)                             | 이름을 기준으로 관리자에서 항목을 찾습니다.                                 |

**속성:**

| [`active_terms`](#isaaclab.managers.ObservationManager.active_terms)                   | 각 그룹의 활성 관찰 항목 이름입니다.                      |
|----------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [`group_obs_dim`](#isaaclab.managers.ObservationManager.group_obs_dim)                 | 각 그룹의 계산된 관찰 형태입니다.                        |
| [`group_obs_term_dim`](#isaaclab.managers.ObservationManager.group_obs_term_dim)       | 각 그룹의 개별 관찰 항목 형태입니다.                 |
| [`group_obs_concatenate`](#isaaclab.managers.ObservationManager.group_obs_concatenate) | 각 그룹에서 관찰 항목이 연결되는지 여부입니다. |
| [`get_IO_descriptors`](#isaaclab.managers.ObservationManager.get_IO_descriptors)       | 관찰 관리자의 IO 디스크립터를 가져옵니다.                  |
| [`device`](#isaaclab.managers.ObservationManager.device)                               | 계산을 수행할 장치입니다.                             |
| [`num_envs`](#isaaclab.managers.ObservationManager.num_envs)                           | 환경 수입니다.                                              |

#### \_\_init_\_(cfg: [object](https://docs.python.org/3/library/functions.html#object), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

관찰 관리자를 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체 또는 사전(`dict[str, ObservationGroupCfg]`).
  * **env** – 환경 인스턴스입니다.
* **오류:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 구성이 None인 경우.
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 그룹 내 관측 항목의 모양이 연결에 적합하지 않은 경우, [`concatenate_terms`](#isaaclab.managers.ObservationGroupCfg.concatenate_terms) 속성이 True로 설정되어 있으면 발생합니다.

#### get_active_iterable_terms(env_idx: [int](https://docs.python.org/3/library/functions.html#int)) → [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [collections.abc.Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]]]

활성 항목을 반복 가능한 튜플 시퀀스로 반환합니다.

튜플의 첫 번째 요소는 항목의 이름이고, 두 번째 요소는 항목의 원시 값(들)입니다.

* **매개변수:**
  **env_idx** – 활성 항목을 가져올 특정 환경입니다.
* **반환값:**
  활성 항목입니다.

#### *property* active_terms *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]*

각 그룹의 활성 관측 항목 이름입니다.

키 그룹 이름은 그룹 이름이고, 값은 해당 그룹 내 관측 항목 이름들의 목록입니다.

#### *property* group_obs_dim *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), ...] | [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), ...]]]*

각 그룹의 계산된 관측값의 형태입니다.

키는 그룹 이름이고, 값은 관측 텐서의 형태입니다.
그룹의 항목이 연결된 경우, 값은 연결된 관측 텐서의 형태를 나타내는 단일 튜플입니다. 그렇지 않은 경우, 값은 튜플들의 리스트이며, 각 튜플은 해당 그룹의 항목에 대한 관측 텐서의 형태를 나타냅니다.

#### *property* group_obs_term_dim *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), ...]]]*

각 그룹의 개별 관측 항목의 형태입니다.

키는 그룹 이름이고, 값은 그룹 내 관측 항목의 형태를 나타내는 튜플들의 리스트입니다.
튜플의 순서는 그룹 내 항목의 순서에 해당합니다.
이 순서는 [`active_terms`](#isaaclab.managers.ObservationManager.active_terms)의 항목 순서와 일치합니다.

#### *property* group_obs_concatenate *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [bool](https://docs.python.org/3/library/functions.html#bool)]*

각 그룹에서 관측 항목이 연결되어 있는지 여부를 나타냅니다.

키는 그룹 이름이고, 값은 해당 그룹의 관측 항목이 단일 텐서로 연결되어 있는지 지정하는 부울 값입니다. True인 경우, 관측값은 마지막 차원을 따라 연결됩니다.

값은 그룹 설정의 [`concatenate_terms`](#isaaclab.managers.ObservationGroupCfg.concatenate_terms) 속성을 기반으로 설정됩니다.

#### *property* get_IO_descriptors

관측 관리자의 IO 디스크립터를 가져옵니다.

* **반환값:**
  키가 그룹 이름이고 값이 IO 디스크립터인 딕셔너리입니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]

관측 관리자를 재설정하고 현재 타임스텝에 대한 로깅 정보를 반환합니다.

* **매개변수:**
  **env_ids** – 로그 데이터를 기록할 환경 ID입니다.
  기본값은 None이며, 모든 환경에 대한 데이터를 기록합니다.
* **반환값:**
  로깅 정보를 포함하는 딕셔너리입니다.

#### compute(update_history: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]

모든 그룹에 대한 그룹별 관측값을 계산합니다.

이 메서드는 관측 관리자가 처리하는 모든 그룹에 대한 관측값을 계산합니다.
그룹별 관측값의 처리 과정은 [`compute_group()`](#isaaclab.managers.ObservationManager.compute_group)를 참조하세요.

* **매개변수:**
  **update_history** – 관측 기록에 obs를 추가할지 여부를 나타내는 부울 값입니다.
  기본값은 False이며, 이 경우 `compute_group` 호출이 기록을 수정하지 않습니다. 그룹의 `history_length`가 0이면 이 입력은 동작하지 않습니다.
* **반환값:**
  키가 그룹 이름이고 값이 계산된 관측값인 딕셔너리입니다.
  관측값은 그룹 구성에 따라 단일 텐서로 연결되거나, 항목 이름을 키로 갖는 딕셔너리로 반환됩니다.

#### compute_group(group_name: [str](https://docs.python.org/3/library/stdtypes.html#str), update_history: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

주어진 그룹에 대한 관측값을 계산합니다.

주어진 그룹의 관측값은 그룹 내 각 항목에 등록된 함수를 호출하여 계산됩니다.
함수들은 그룹 내 항목의 순서대로 호출되며, 각 함수는 (num_envs, …) 형태의 텐서를 반환해야 합니다.

각 관측 항목에 대해 다음과 같은 단계가 수행됩니다:

1. 함수를 호출하여 관측 항목을 계산합니다.
2. [`ObservationTermCfg.modifiers`](#isaaclab.managers.ObservationTermCfg.modifiers)에 지정된 순서대로 커스텀 수식자를 적용합니다.
3. [`ObservationTermCfg.noise`](#isaaclab.managers.ObservationTermCfg.noise)에 기반한 잡음/노이즈 모델을 적용합니다.
4. [`ObservationTermCfg.clip`](#isaaclab.managers.ObservationTermCfg.clip)에 기반한 클리핑을 적용합니다.
5. [`ObservationTermCfg.scale`](#isaaclab.managers.ObservationTermCfg.scale)에 기반한 스케일링을 적용합니다.

노이즈를 클리핑 또는 스케일링 전에 적용하여 실제 세계에서 노이즈가 데이터에 미치는 영향의 무결성을 유지합니다.
노이즈를 클리핑 또는 스케일링 후에 적용하면 노이즈가 인위적으로 제한되거나 증폭될 수 있으며, 이는 데이터에서 자연스럽게 발생하는 노이즈를 잘못 표현할 수 있습니다.

* **매개변수:**
  * **group_name** – 관측값을 계산할 그룹의 이름입니다. 기본값은 None이며, 이 경우 모든 그룹의 관측값이 계산되고 반환됩니다.
  * **update_history** – 관측 그룹의 기록에 obs를 추가할지 여부를 나타내는 부울 값입니다.
    기본값은 False이며, 이 경우 `compute_group` 호출이 기록을 수정하지 않습니다. 그룹의 `history_length`가 0이면 이 입력은 동작하지 않습니다.
* **반환값:**
  그룹 구성에 따라, 개별 관측 항목의 텐서들은 마지막 차원을 따라 단일 텐서로 연결됩니다. 그렇지 않은 경우, 항목 이름을 키로 갖는 딕셔너리로 반환됩니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 `group_name`이 관리자가 처리하는 유효한 그룹이 아닌 경우 발생합니다.

#### serialize() → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

모든 활성 그룹의 관측 항목 구성 직렬화합니다.

* **반환값:**
  각 그룹 이름을 키로, 해당 그룹의 직렬화된 관측 항목 구성 값을 값으로 갖는 딕셔너리입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 수행할 디바이스입니다.

#### find_terms(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)]) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

이름 기반으로 관리자 내 항목을 찾습니다.

이 함수는 관리자 내에서 이름을 기반으로 항목을 검색합니다.
이름은 정규 표현식 또는 정규 표현식 리스트로 지정할 수 있습니다.
검색은 관리자의 활성 항목에서 수행됩니다.

이름 매칭에 대한 추가 정보는 `resolve_matching_names()` 함수를 참조하세요.

* **매개변수:**
  **name_keys** – 항목 이름과 매치시킬 정규 표현식 또는 정규 표현식 리스트입니다.
* **반환값:**
  입력 키와 일치하는 항목 이름들의 리스트입니다.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 수입니다.

### *class* isaaclab.managers.ObservationGroupCfg

관측 그룹의 구성입니다.

**속성:**

| [`concatenate_terms`](#isaaclab.managers.ObservationGroupCfg.concatenate_terms)     | 그룹에서 관측 항목을 연결할지 여부입니다.                                                       |
|-------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| [`concatenate_dim`](#isaaclab.managers.ObservationGroupCfg.concatenate_dim)         | 다양한 관측 항목을 연결할 차원입니다.                                                  |
| [`enable_corruption`](#isaaclab.managers.ObservationGroupCfg.enable_corruption)     | 관측 그룹에 대한 손상 활성화 여부입니다.                                                          |
| [`history_length`](#isaaclab.managers.ObservationGroupCfg.history_length)           | 그룹의 모든 관측 항목에 대한 관측 버퍼에 저장할 과거 관측 횟수입니다.               |
| [`flatten_history_dim`](#isaaclab.managers.ObservationGroupCfg.flatten_history_dim) | 그룹의 모든 관측 항목에 대한 기록 기반 관측 항목을 2-D (num_env, D) 텐서로 평탄화할지 여부를 나타내는 플래그입니다. |

#### concatenate_terms *: [bool](https://docs.python.org/3/library/functions.html#bool)*

그룹의 관측 항목을 연결할지 여부입니다. 기본값은 True입니다.

True인 경우, 그룹의 관측 항목은 [`concatenate_dim`](#isaaclab.managers.ObservationGroupCfg.concatenate_dim)에서 지정된 차원을 따라 연결됩니다. 그렇지 않으면 별도로 유지되고 딕셔너리로 반환됩니다.

관측 그룹에 서로 다른 차원의 항목이 포함된 경우, False로 설정해야 합니다.

#### concatenate_dim *: [int](https://docs.python.org/3/library/functions.html#int)*

다른 관측 항목을 연결할 차원입니다. 기본값은 -1이며, 이는 관측 항목의 마지막 차원을 의미합니다.

[`concatenate_terms`](#isaaclab.managers.ObservationGroupCfg.concatenate_terms)가 True인 경우, 이 매개변수는 관측 항목이 연결될 차원을 지정합니다. 표시된 차원은 관측 항목의 모양에 따라 다릅니다. 예를 들어, (H, W, C) 형태의 2-D RGB 이미지의 경우 차원 0은 높이를 따라 연결하고, 1은 너비를 따라 연결하며, 2는 채널을 따라 연결함을 의미합니다. 배atched 환경으로 인한 오프셋은 자동으로 처리됩니다.

#### enable_corruption *: [bool](https://docs.python.org/3/library/functions.html#bool)*

관측 그룹에 대한 손상을 활성화할지 여부입니다. 기본값은 False입니다.

True인 경우, 그룹의 관측 항목이 지정된 노이즈(있는 경우)로 손상됩니다. 그렇지 않으면 손상이 적용되지 않습니다.

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

그룹의 모든 관측 항목에 대해 관측 버퍼에 저장할 과거 관측 횟수입니다.

이 매개변수는 설정된 경우 [`ObservationTermCfg.history_length`](#isaaclab.managers.ObservationTermCfg.history_length)를 재정의합니다. 기본값은 None입니다. None인 경우, 각 항목의 기록은 개별 항목 기준으로 제어됩니다. [`ObservationTermCfg`](#isaaclab.managers.ObservationTermCfg)에서 [`ObservationTermCfg.history_length`](#isaaclab.managers.ObservationTermCfg.history_length) 구현에 대한 자세한 내용은 참고하세요.

#### flatten_history_dim *: [bool](https://docs.python.org/3/library/functions.html#bool)*

그룹의 모든 관측 항목에 대한 기록 기반 관측 항목을 2-D (num_env, D) 텐서로 평탄화할지 여부를 나타내는 플래그입니다. 기본값은 True입니다.

이 매개변수는 [`ObservationTermCfg.flatten_history_dim`](#isaaclab.managers.ObservationTermCfg.flatten_history_dim)을 그룹 전체에서 재정의하며, [`ObservationGroupCfg.history_length`](#isaaclab.managers.ObservationGroupCfg.history_length)가 설정된 경우에 적용됩니다.

### *class* isaaclab.managers.ObservationTermCfg

관측 항목에 대한 구성입니다.

**Attributes:**

| [`func`](#isaaclab.managers.ObservationTermCfg.func)                               | 호출할 함수의 이름입니다.                                                                        |
|------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| [`modifiers`](#isaaclab.managers.ObservationTermCfg.modifiers)                     | 순서대로 적용할 관측 데이터 수식자 목록입니다.                                              |
| [`noise`](#isaaclab.managers.ObservationTermCfg.noise)                             | 관측에 추가할 노이즈입니다.                                                                          |
| [`clip`](#isaaclab.managers.ObservationTermCfg.clip)                               | 노이즈 추가 후 관측에 적용할 클리핑 범위입니다.                                                    |
| [`scale`](#isaaclab.managers.ObservationTermCfg.scale)                             | 클리핑 후 관측에 적용할 스케일입니다.                                                         |
| [`history_length`](#isaaclab.managers.ObservationTermCfg.history_length)           | 관측 버퍼에 저장할 과거 관측 횟수입니다.                                              |
| [`flatten_history_dim`](#isaaclab.managers.ObservationTermCfg.flatten_history_dim) | 기록 기반 관측 항목을 2-D (N, D) 텐서로 평탄화해야 하는지 여부입니다. |
| [`params`](#isaaclab.managers.ObservationTermCfg.params)                           | 키워드 인수로 함수에 전달할 매개변수입니다.                                             |

#### func *: Callable[..., [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]*

호출할 함수의 이름입니다.

이 함수는 환경 객체와 다른 매개변수를 입력으로 받아 torch float 텐서 형태의 관측 신호 (num_envs, obs_term_dim)를 반환해야 합니다.

#### modifiers *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[ModifierCfg](isaaclab.utils.md#isaaclab.utils.modifiers.ModifierCfg)] | [None](https://docs.python.org/3/library/constants.html#None)*

순서대로 적용할 관측 데이터 수식자 목록입니다. 기본값은 None이며, 이 경우 수정이 적용되지 않습니다.

수식자는 목록에 지정된 순서대로 적용됩니다. 상태가 있을 수도 있고 없을 수도 있으며, 관측 데이터에 변형을 적용하는 데 사용될 수 있습니다. 예를 들어, 수식자를 사용하여 관측 데이터를 정규화하거나 이동 평균을 적용할 수 있습니다.

수식자에 대한 자세한 내용은 [`ModifierCfg`](isaaclab.utils.md#isaaclab.utils.modifiers.ModifierCfg) 클래스를 참조하세요.

#### noise *: [NoiseCfg](isaaclab.utils.md#isaaclab.utils.noise.NoiseCfg) | [NoiseModelCfg](isaaclab.utils.md#isaaclab.utils.noise.NoiseModelCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

관측에 추가할 노이즈입니다. 기본값은 None이며, 이 경우 노이즈가 추가되지 않습니다.

#### clip *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

노이즈 추가 후 관측에 적용할 클리핑 범위입니다. 기본값은 None이며, 이 경우 클리핑이 적용되지 않습니다.

#### scale *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), ...] | [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

클리핑 후 관측에 적용할 스케일입니다. 기본값은 None이며, 이 경우 스케일이 적용되지 않습니다(즉, scale을 `1`로 설정한 것과 동일함).

PyTorch 브로드캐스팅을 활용하여 제공된 값으로 관측 텐서를 스케일링합니다. 튜플이 제공되는 경우, 튜플의 길이가 관측 항목에서 출력되는 텐서의 차원과 일치하도록 해야 합니다.

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

관측 버퍼에 저장할 과거 관측 횟수입니다. 기본값은 0이며, 이는 기록이 없음을 의미합니다.

관측 기록은 처음에는 비어 있지만, 리셋 또는 초기화 후에 첫 번째 추가 시 채워집니다. 이후 기록은 기록 버퍼에 단일 항목만 추가됩니다. `flatten_history_dim`이 True로 설정된 경우, 차원이 (N, H, D, …)인 소스 데이터(여기서 N은 배치 차원이고 H는 기록 길이)는 2-D 텐서 형태인 (N, H\*D\*…)로 재형성됩니다. 그렇지 않은 경우, 데이터는 그대로 반환됩니다.

#### flatten_history_dim *: [bool](https://docs.python.org/3/library/functions.html#bool)*

관측 관리자가 기록 기반 관측 항목을 2-D (N, D) 텐서로 평탄화해야 하는지 여부입니다. 기본값은 True입니다.

#### params *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any | [SceneEntityCfg](#isaaclab.managers.SceneEntityCfg)]*

키워드 인수로 함수에 전달할 매개변수입니다. 기본값은 빈 딕셔너리입니다.

#### NOTE
값이 [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체인 경우, 관리자는 `InteractiveScene`에서 장면 엔티티를 쿼리하고 [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체에 지정된 대로 엔티티의 관절과 신체를 처리합니다.

## Action Manager

### *class* isaaclab.managers.ActionManager

Bases: [`ManagerBase`](#isaaclab.managers.ManagerBase)

주어진 월드에 대한 동작을 처리하고 적용하는 관리자입니다.

동작 관리자는 주어진 월드에서 사용자 정의 동작의 해석과 적용을 처리합니다. 예상되는 동작의 차원을 결정하는 다양한 동작 항목으로 구성됩니다.

동작 관리자는 두 단계에서 작업을 수행합니다:

* 동작 처리: 입력 동작을 각 항목으로 분할하고 필요한 경우 변형을 수행합니다.
  전처리 필요. 환경 단계마다 한 번씩 호출해야 합니다.
* 동작 적용: 이 작업은 일반적으로 처리된 동작을 씬(예: 로봇)의 자산에 설정합니다. 시뮬레이션 단계마다 호출해야 합니다.

**메서드:**

| [`__init__`](#isaaclab.managers.ActionManager.__init__)(cfg, env)                                  | 액션 매니저 초기화.                           |
|----------------------------------------------------------------------------------------------------|----------------------------------------------------------|
| [`get_active_iterable_terms`](#isaaclab.managers.ActionManager.get_active_iterable_terms)(env_idx) | 활성 항목을 튜플의 반복 가능한 시퀀스로 반환. |
| [`set_debug_vis`](#isaaclab.managers.ActionManager.set_debug_vis)(debug_vis)                       | 액션 데이터 시각화 여부 설정.               |
| [`reset`](#isaaclab.managers.ActionManager.reset)([env_ids])                                       | 액션 기록 초기화.                               |
| [`process_action`](#isaaclab.managers.ActionManager.process_action)(action)                        | 환경에 전달된 동작 처리.           |
| [`apply_action`](#isaaclab.managers.ActionManager.apply_action)()                                  | 환경/시뮬레이션에 동작 적용.       |
| [`get_term`](#isaaclab.managers.ActionManager.get_term)(name)                                      | 지정된 이름의 액션 항목 반환.         |
| [`serialize`](#isaaclab.managers.ActionManager.serialize)()                                        | 액션 매니저 구성 직렬화.              |
| [`find_terms`](#isaaclab.managers.ActionManager.find_terms)(name_keys)                             | 이름을 기준으로 관리자 내 항목 찾기.            |

**속성:**

| [`total_action_dim`](#isaaclab.managers.ActionManager.total_action_dim)                         | 동작 차원의 총합.                                     |
|-------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`active_terms`](#isaaclab.managers.ActionManager.active_terms)                                 | 활성 액션 항목 이름.                                    |
| [`action_term_dim`](#isaaclab.managers.ActionManager.action_term_dim)                           | 각 액션 항목의 형태.                                      |
| [`action`](#isaaclab.managers.ActionManager.action)                                             | 환경에 전달된 동작.                            |
| [`prev_action`](#isaaclab.managers.ActionManager.prev_action)                                   | 이전에 환경에 전달된 동작.                   |
| [`has_debug_vis_implementation`](#isaaclab.managers.ActionManager.has_debug_vis_implementation) | 명령 항목에 디버그 시각화 구현 여부. |
| [`get_IO_descriptors`](#isaaclab.managers.ActionManager.get_IO_descriptors)                     | 액션 매니저의 IO 디스크립터 가져오기.                  |
| [`device`](#isaaclab.managers.ActionManager.device)                                             | 계산을 수행할 장치.                        |
| [`num_envs`](#isaaclab.managers.ActionManager.num_envs)                                         | 환경 수.                                         |

#### \_\_init_\_(cfg: [object](https://docs.python.org/3/library/functions.html#object), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

액션 매니저 초기화.

* **매개변수:**
  * **cfg** – 구성 객체 또는 딕셔너리 (`dict[str, ActionTermCfg]`).
  * **env** – 환경 인스턴스.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 구성 객체가 None인 경우.

#### *property* total_action_dim *: [int](https://docs.python.org/3/library/functions.html#int)*

동작 차원의 총합.

#### *property* active_terms *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

활성 액션 항목 이름.

#### *property* action_term_dim *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

각 액션 항목의 형태.

#### *property* action *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

환경에 전달된 동작. 형태는 (num_envs, total_action_dim).

#### *property* prev_action *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

이전에 환경에 전달된 동작. 형태는 (num_envs, total_action_dim).

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

명령 항목에 디버그 시각화 구현 여부.

#### *property* get_IO_descriptors *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)]]*

액션 매니저의 IO 디스크립터 가져오기.

* **반환값:**
  키가 항목 이름이고 값이 IO 디스크립터인 딕셔너리.

#### get_active_iterable_terms(env_idx: [int](https://docs.python.org/3/library/functions.html#int)) → [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [collections.abc.Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]]]

활성 항목을 튜플의 반복 가능한 시퀀스로 반환.

튜플의 첫 번째 요소는 항목 이름이고, 두 번째 요소는 항목의 원시 값입니다.

* **매개변수:**
  **env_idx** – 활성 항목을 가져올 특정 환경.
* **반환값:**
  활성 항목.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool))

액션 데이터 시각화 여부 설정.
:param debug_vis: 액션 데이터 시각화 여부.

* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 액션이 디버그 시각화를 지원하지 않는 경우 False.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

액션 기록 초기화.

* **매개변수:**
  **env_ids** – 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 고려됩니다.
* **반환값:**
  빈 딕셔너리.

#### process_action(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor))

환경에 전달된 동작 처리.

#### 참고
이 함수는 환경 단계마다 한 번씩 호출해야 합니다.

* **매개변수:**
  **action** – 처리할 동작.

#### apply_action() → [None](https://docs.python.org/3/library/constants.html#None)

환경/시뮬레이션에 동작 적용.

#### 참고
시뮬레이션 단계마다 호출해야 합니다.

#### get_term(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [ActionTerm](#isaaclab.managers.ActionTerm)

지정된 이름의 액션 항목 반환.

* **매개변수:**
  **name** – 액션 항목 이름.
* **반환값:**
  지정된 이름의 액션 항목.

#### serialize() → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

액션 매니저 구성 직렬화.

* **반환값:**
  직렬화된 액션 항목 구성의 딕셔너리.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 수행할 장치.

#### find_terms(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)]) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

이름을 기준으로 관리자 내 항목 찾기.

이 함수는 이름을 기준으로 관리자 내 항목을 검색합니다. 이름은 정규 표현식 또는 정규 표현식 목록으로 지정할 수 있습니다. 검색은 관리자 내 활성 항목에 대해 수행됩니다.

이름 매칭에 대한 자세한 내용은 `resolve_matching_names()` 함수를 참조하십시오.

* **매개변수:**
  **name_keys** – 항목 이름과 일치시킬 정규 표현식 또는 정규 표현식 목록.
* **반환값:**
  입력 키와 일치하는 항목 이름 목록.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 수.

### *class* isaaclab.managers.ActionTerm

Bases: [`ManagerTermBase`](#isaaclab.managers.ManagerTermBase)

액션 항목 기본 클래스.

액션 항목은 환경에 전달된 원시 동작을 처리하고 해당 항목이 관리하는 자산에 적용하는 역할을 담당합니다. 액션 항목은 두 가지 작업으로 구성됩니다:

* 동작 처리: 이 작업은 **환경 단계**마다 한 번씩 수행되며, 환경에 전달된 원시 동작의 전처리를 담당합니다.
* 동작 적용: 이 작업은 **시뮬레이션 단계**마다 한 번씩 수행되며, 항목이 관리하는 자산에 처리된 동작을 적용하는 역할을 담당합니다.

**메서드:**

| [`__init__`](#isaaclab.managers.ActionTerm.__init__)(cfg, env)              | 액션 항을 초기화합니다.                           |
|-----------------------------------------------------------------------------|-------------------------------------------------------|
| [`set_debug_vis`](#isaaclab.managers.ActionTerm.set_debug_vis)(debug_vis)   | 액션 항 데이터 시각화 여부를 설정합니다.       |
| [`process_actions`](#isaaclab.managers.ActionTerm.process_actions)(actions) | 환경으로 전송된 액션을 처리합니다.        |
| [`apply_actions`](#isaaclab.managers.ActionTerm.apply_actions)()            | 해당 항이 관리하는 자산에 액션을 적용합니다. |
| [`reset`](#isaaclab.managers.ActionTerm.reset)([env_ids])                   | 관리자 항을 재설정합니다.                              |
| [`serialize`](#isaaclab.managers.ActionTerm.serialize)()                    | 일반 직렬화 호출.                           |

**속성:**

| [`action_dim`](#isaaclab.managers.ActionTerm.action_dim)                                     | 액션 항의 차원입니다.                                   |
|----------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`raw_actions`](#isaaclab.managers.ActionTerm.raw_actions)                                   | 항에 전송된 입력/원시 액션입니다.                         |
| [`processed_actions`](#isaaclab.managers.ActionTerm.processed_actions)                       | 항에서 처리 후 계산된 액션입니다. |
| [`has_debug_vis_implementation`](#isaaclab.managers.ActionTerm.has_debug_vis_implementation) | 액션 항에 디버그 시각화가 구현되었는지 여부입니다.  |
| [`IO_descriptor`](#isaaclab.managers.ActionTerm.IO_descriptor)                               | 액션 항의 IO 디스크립터입니다.                          |
| [`export_IO_descriptor`](#isaaclab.managers.ActionTerm.export_IO_descriptor)                 | 액션 항의 IO 디스크립터를 내보낼지 여부입니다.        |
| [`device`](#isaaclab.managers.ActionTerm.device)                                             | 계산을 수행할 디바이스입니다.                        |
| [`num_envs`](#isaaclab.managers.ActionTerm.num_envs)                                         | 환경 수.                                         |

#### \_\_init_\_(cfg: [ActionTermCfg](#isaaclab.managers.ActionTermCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

액션 항을 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체입니다.
  * **env** – 환경 인스턴스입니다.

#### *추상 속성* action_dim *: [int](https://docs.python.org/3/library/functions.html#int)*

액션 항의 차원입니다.

#### *추상 속성* raw_actions *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

항에 전송된 입력/원시 액션입니다.

#### *추상 속성* processed_actions *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

항에서 처리 후 계산된 액션입니다.

#### *속성* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액션 항에 디버그 시각화가 구현되었는지 여부입니다.

#### *속성* IO_descriptor *: GenericActionIODescriptor*

액션 항의 IO 디스크립터입니다.

#### *속성* export_IO_descriptor *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액션 항의 IO 디스크립터를 내보낼지 여부입니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

액션 항 데이터 시각화 여부를 설정합니다.
:param debug_vis: 액션 항 데이터 시각화 여부.

* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부입니다. 액션 항이 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

#### *추상* process_actions(actions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor))

환경으로 전송된 액션을 처리합니다.

#### 참고
이 함수는 관리자에 의해 환경 스텝당 한 번 호출됩니다.

* **매개변수:**
  **actions** – 처리할 액션입니다.

#### *추상* apply_actions()

해당 항이 관리하는 자산에 액션을 적용합니다.

#### 참고
이는 관리자에 의해 시뮬레이션 스텝마다 호출됩니다.

#### *속성* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 수행할 디바이스입니다.

#### *속성* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 수입니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [None](https://docs.python.org/3/library/constants.html#None)

관리자 항을 재설정합니다.

* **매개변수:**
  **env_ids** – 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 고려됩니다.

#### serialize() → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

일반 직렬화 호출. 구성 딕셔너리가 포함됩니다.

### *클래스* isaaclab.managers.ActionTermCfg

액션 항에 대한 구성입니다.

**속성:**

| [`class_type`](#isaaclab.managers.ActionTermCfg.class_type)   | 연결된 액션 항 클래스입니다.                      |
|---------------------------------------------------------------|--------------------------------------------------------|
| [`asset_name`](#isaaclab.managers.ActionTermCfg.asset_name)   | 장면 엔티티의 이름입니다.                          |
| [`debug_vis`](#isaaclab.managers.ActionTermCfg.debug_vis)     | 디버그 정보 시각화 여부입니다.                |
| [`clip`](#isaaclab.managers.ActionTermCfg.clip)               | 액션 클립 범위(정규식 표현 딕셔너리). |

#### class_type *: [type](https://docs.python.org/3/library/functions.html#type)[[ActionTerm](#isaaclab.managers.ActionTerm)]*

연결된 액션 항 클래스입니다.

이 클래스는 [`isaaclab.managers.action_manager.ActionTerm`](#isaaclab.managers.ActionTerm)을 상속해야 합니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

장면 엔티티의 이름입니다.

이 이름은 장면 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보 시각화 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

액션 클립 범위(정규식 표현 딕셔너리). 기본값은 None입니다.

## 이벤트 관리자

### *클래스* isaaclab.managers.EventManager

베이스: [`ManagerBase`](#isaaclab.managers.ManagerBase)

다양한 시뮬레이션 이벤트를 기반으로 작업을 오케스트레이션하는 관리자입니다.

이벤트 관리자는 시뮬레이션 이벤트를 기반으로 환경에 작업을 적용합니다. 예를 들어,
초기화/리셋 중에 객체의 질량이나 마찰 계수를 무작위로 변경하거나, 고정된 스텝 간격으로 로봇에 무작위 푸시를 적용할 수 있습니다. 사용자는 이벤트를 적용할 시점에 따라 동작을 세밀하게 조정하기 위해 여러 가지 이벤트 모드를 지정할 수 있습니다.

이벤트 항은 관리자의 설정과 각 항의 매개변수를 포함하는 구성 클래스에서 파싱됩니다. 각 이벤트 항은 [`EventTermCfg`](#isaaclab.managers.EventTermCfg) 클래스를 인스턴스화해야 합니다.

이벤트 항은 모드로 그룹화할 수 있습니다. 모드는 이벤트 항이 적용될 시점을 지정하는 사용자 정의 문자열이며, 이를 통해 사용자는 이벤트 항이 적용될 시점을 완전히 제어할 수 있습니다.

일반적인 훈련 프로세스에서는 다음 모드에서 이벤트를 적용하는 것이 좋습니다:

- “prestartup”: 시뮬레이션이 시작되기 전 훈련 시작 시 이벤트를 한 번 적용합니다. 이는 시뮬레이션 단계의 USD 수준 속성을 무작위화하는 데 사용됩니다.
- “startup”: 시뮬레이션이 시작된 후 훈련 시작 시 이벤트를 한 번 적용합니다.
- “reset”: 리셋 시마다 이벤트를 적용합니다.
- “interval”: 사전 지정된 시간 간격으로 이벤트를 적용합니다.

그러나 사용자 정의 모드를 정의하고 훈련 프로세스에서 필요에 따라 사용할 수도 있습니다. 이를 위해서는 환경 구현에서도 해당 모드의 트리거를 추가해야 합니다.

#### 참고
모드 `"interval"`에 해당하는 작업 트리거만 관리자 자체에서 직접 처리됩니다. 다른 모드는 환경 구현에서 처리됩니다.

**메서드:**

| [`__init__`](#isaaclab.managers.EventManager.__init__)(cfg, env)                                  | 이벤트 관리자를 초기화합니다.                                                 |
|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [`reset`](#isaaclab.managers.EventManager.reset)([env_ids])                                       | 관리자를 재설정하고 현재 타임스텝에 대한 로깅 정보를 반환합니다. |
| [`apply`](#isaaclab.managers.EventManager.apply)(mode[, env_ids, dt, global_env_step_count])      | 지정된 모드의 각 이벤트 항을 호출합니다.                                  |
| [`set_term_cfg`](#isaaclab.managers.EventManager.set_term_cfg)(term_name, cfg)                    | 지정된 항의 구성 값을 관리자에 설정합니다.                |
| [`get_term_cfg`](#isaaclab.managers.EventManager.get_term_cfg)(term_name)                         | 지정된 항의 구성 값을 가져옵니다.                                |
| [`find_terms`](#isaaclab.managers.EventManager.find_terms)(name_keys)                             | 이름 기반으로 관리자에서 항목을 찾습니다.                                 |
| [`get_active_iterable_terms`](#isaaclab.managers.EventManager.get_active_iterable_terms)(env_idx) | 활성 항목을 튜플의 반복 가능한 시퀀스로 반환합니다.                      |

**속성:**

| [`active_terms`](#isaaclab.managers.EventManager.active_terms)       | 활성 이벤트 항목 이름.              |
|----------------------------------------------------------------------|------------------------------------------|
| [`available_modes`](#isaaclab.managers.EventManager.available_modes) | 이벤트 모드.                         |
| [`device`](#isaaclab.managers.EventManager.device)                   | 계산을 수행할 장치.                   |
| [`num_envs`](#isaaclab.managers.EventManager.num_envs)               | 환경 수.                  |

#### \_\_init_\_(cfg: [object](https://docs.python.org/3/library/functions.html#object), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

이벤트 관리자를 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체 또는 딕셔너리 (`dict[str, EventTermCfg]`).
  * **env** – 환경 객체.

#### *property* active_terms *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]*

활성 이벤트 항목 이름.

키는 이벤트의 모드이며 값은 이벤트 항목 이름입니다.

#### *property* available_modes *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

이벤트 모드.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]

관리자를 재설정하고 현재 시간 단계에 대한 로깅 정보를 반환합니다.

* **매개변수:**
  **env_ids** – 데이터를 기록할 환경 ID.
  기본값은 `None`이며, 모든 환경의 데이터를 기록합니다.
* **반환값:**
  로깅 정보를 포함하는 딕셔너리.

#### apply(mode: [str](https://docs.python.org/3/library/stdtypes.html#str), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, dt: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, global_env_step_count: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None)

지정된 모드의 각 이벤트 항목을 호출합니다.

이 함수는 지정된 모드의 모든 이벤트 항목을 순회하며 해당 항목에 대응하는 함수를 호출합니다. 해당 함수는 환경 인스턴스와 환경 인덱스를 인수로 전달받아 이벤트를 적용합니다.

“interval” 모드의 경우, 시간 간격이 지났을 때 함수가 호출됩니다. 이는 환경의 시간 단계를 지정해야 함을 의미합니다.

“reset” 모드의 경우, 모드가 “reset”이고 마지막 함수 트리거 이후 발생한 환경 단계의 총 수가 구성된 파라미터(환경 단계 간 재설정 횟수)와 같을 때 함수가 호출됩니다.

* **매개변수:**
  * **mode** – 이벤트 모드.
  * **env_ids** – 이벤트를 적용할 환경 인덱스.
    기본값은 `None`이며, 해당하는 경우 모든 환경에 이벤트를 적용합니다.
  * **dt** – 환경의 시간 단계. 이는 “interval” 모드에서만 사용됩니다.
    다른 모드 호출을 간단히 하기 위해 기본값은 `None`입니다.
  * **global_env_step_count** – 발생한 환경 단계의 총 수. 이는 “reset” 모드에서만 사용됩니다.
    다른 모드 호출을 간단히 하기 위해 기본값은 `None`입니다.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 모드가 `"interval"`이고 시간 단계가 제공되지 않은 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 모드가 `"interval"`이고 환경 인덱스가 제공된 경우. 이는 환경 인덱스가 각 환경의 남은 시간에 기반하여 계산되므로 정의되지 않은 동작입니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 모드가 `"reset"`이고 발생한 환경 단계의 총 수가 제공되지 않은 경우.

#### set_term_cfg(term_name: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [EventTermCfg](#isaaclab.managers.EventTermCfg))

관리자에 지정된 항목의 구성을 설정합니다.

이 메서드는 모든 모드에서 이름을 검색하여 항목을 찾은 후, 첫 번째 일치하는 이름의 항목 구성을 업데이트합니다.

* **매개변수:**
  * **term_name** – 이벤트 항목 이름.
  * **cfg** – 이벤트 항목 구성.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 항목 이름을 찾을 수 없는 경우.

#### get_term_cfg(term_name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [EventTermCfg](#isaaclab.managers.EventTermCfg)

지정된 항목의 구성을 반환합니다.

이 메서드는 모든 모드에서 이름을 검색하여 항목을 찾은 후, 첫 번째 일치하는 이름의 항목 구성을 반환합니다.

* **매개변수:**
  **term_name** – 이벤트 항목 이름.
* **반환값:**
  이벤트 항목 구성.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 항목 이름을 찾을 수 없는 경우.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 수행할 장치.

#### find_terms(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)]) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

이름 기반으로 관리자에서 항목을 찾습니다.

이 함수는 이름을 기준으로 관리자에서 항목을 검색합니다. 이름은 정규 표현식 또는 정규 표현식 목록으로 지정할 수 있습니다. 검색은 관리자의 활성 항목에서 수행됩니다.

이름 매칭에 대한 자세한 내용은 `resolve_matching_names()` 함수를 참조하십시오.

* **매개변수:**
  **name_keys** – 항목 이름과 일치시킬 정규 표현식 또는 정규 표현식 목록.
* **반환값:**
  입력 키와 일치하는 항목 이름 목록.

#### get_active_iterable_terms(env_idx: [int](https://docs.python.org/3/library/functions.html#int)) → [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [collections.abc.Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]]]

활성 항목을 튜플의 반복 가능한 시퀀스로 반환합니다.

튜플의 첫 번째 요소는 항목 이름이고, 두 번째 요소는 항목의 원시 값(들)입니다.

* **반환값:**
  활성 항목.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 수.

### *class* isaaclab.managers.EventTermCfg

이벤트 항목 구성.

**속성:**

| [`params`](#isaaclab.managers.EventTermCfg.params)                                             | 함수에 키워드 인수로 전달할 매개변수.                           |
|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`func`](#isaaclab.managers.EventTermCfg.func)                                                 | 호출할 함수 이름.                                                      |
| [`mode`](#isaaclab.managers.EventTermCfg.mode)                                                 | 이벤트 항목이 적용되는 모드.                                                |
| [`interval_range_s`](#isaaclab.managers.EventTermCfg.interval_range_s)                         | 초 단위로 표현된 항목이 적용되는 시간 범위.                                  |
| [`is_global_time`](#isaaclab.managers.EventTermCfg.is_global_time)                             | 무작위화가 환경당 기준이 아닌 전역 기준으로 추적되어야 하는지 여부.                         |
| [`min_step_count_between_reset`](#isaaclab.managers.EventTermCfg.min_step_count_between_reset) | 마지막 적용 이후부터 항목이 다시 적용되기까지 필요한 환경 단계 수. |

#### params *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any | [SceneEntityCfg](#isaaclab.managers.SceneEntityCfg)]*

함수에 키워드 인수로 전달할 매개변수. 기본값은 빈 딕셔너리입니다.

#### NOTE
값이 [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체인 경우, 관리자는 `InteractiveScene`에서 장면 엔티티를 쿼리하고 [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체에 지정된 대로 엔티티의 관절과 본체를 처리합니다.

#### func *: Callable[..., [None](https://docs.python.org/3/library/constants.html#None)]*

호출할 함수의 이름입니다.

이 함수는 환경 객체, 환경 인덱스 및 기타 매개변수를 입력으로 받아야 합니다.

#### mode *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

이벤트 항목이 적용되는 모드입니다.

#### NOTE
모드 이름 `"interval"`은 관리자에 의해 처리되는 특별한 모드이므로, 그 이름은 예약되어 다른 모드에 사용할 수 없습니다.

#### interval_range_s *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

항목이 적용되는 시간 범위(초 단위)입니다. 기본값은 None입니다.

이 범위를 기반으로 각 환경 인스턴스에 대해 지정된 범위 내에서 균일하게 간격이 샘플링됩니다. 현재 시간이 간격 시간에 도달하는 환경 인스턴스에 항목이 적용됩니다.

#### NOTE
이 설정은 모드가 `"interval"`인 경우에만 사용됩니다.

#### is_global_time *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경당 기준으로 랜덤화를 추적할지 여부를 나타냅니다. 기본값은 False입니다.

True이면 모든 환경 인스턴스에 동일한 간격 시간이 사용됩니다. False이면 각 환경 인스턴스에 대해 독립적으로 간격 시간이 샘플링되며, 해당 인스턴스의 현재 시간이 간격 시간에 도달할 때 항목이 적용됩니다.

#### NOTE
이 설정은 모드가 `"interval"`인 경우에만 사용됩니다.

#### min_step_count_between_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

마지막 적용 이후 항목이 다시 적용되기까지의 환경 단계 수입니다. 기본값은 0입니다.

모드가 “reset”일 때, 마지막 적용 이후의 환경 단계 수가 이 값을 초과할 경우에만 항목이 적용됩니다. 이는 항목을 지나치게 자주 호출하는 것을 방지하여 성능을 향상시키는 데 도움이 됩니다.

값이 0이면 모드가 “reset”일 때 관리자 호출마다 항목이 적용됩니다.

#### NOTE
이 설정은 모드가 `"reset"`인 경우에만 사용됩니다.

## Command Manager

### *class* isaaclab.managers.CommandManager

명령을 생성하는 관리자입니다.

명령 관리자는 에이전트가 실행할 명령을 생성하는 데 사용됩니다. 동일한 환경 내에서 다양한 명령 생성 전략 간에 간편하게 전환할 수 있습니다. 예를 들어, 사족 보행 로봇으로 구성된 환경에서 해당 로봇에 대한 명령은 속도 명령 또는 위치 명령이 될 수 있습니다. 명령 생성 로직을 환경과 분리함으로써 다양한 명령 생성 전략 간 전환이 용이해집니다.

명령 항목은 [`CommandTerm`](#isaaclab.managers.CommandTerm) 클래스를 상속받아 구현됩니다. 각 명령 생성 항목은 또한 [`CommandTermCfg`](#isaaclab.managers.CommandTermCfg) 클래스를 상속받는 해당 구성 클래스를 가져야 합니다.

**메서드:**

| [`__init__`](#isaaclab.managers.CommandManager.__init__)(cfg, env)                                  | 명령 관리자를 초기화합니다.                          |
|-----------------------------------------------------------------------------------------------------|----------------------------------------------------------|
| [`get_active_iterable_terms`](#isaaclab.managers.CommandManager.get_active_iterable_terms)(env_idx) | 활성 항목을 튜플의 반복 가능한 시퀀스로 반환합니다. |
| [`set_debug_vis`](#isaaclab.managers.CommandManager.set_debug_vis)(debug_vis)                       | 명령 데이터 시각화 여부를 설정합니다.              |
| [`reset`](#isaaclab.managers.CommandManager.reset)([env_ids])                                       | 명령 항목을 재설정하고 그들의 메트릭을 로그로 남깁니다.           |
| [`compute`](#isaaclab.managers.CommandManager.compute)(dt)                                          | 명령을 업데이트합니다.                                    |
| [`get_command`](#isaaclab.managers.CommandManager.get_command)(name)                                | 지정된 명령 항목의 명령을 반환합니다.      |
| [`get_term`](#isaaclab.managers.CommandManager.get_term)(name)                                      | 지정된 이름의 명령 항목을 반환합니다.        |

**속성:**

| [`active_terms`](#isaaclab.managers.CommandManager.active_terms)                                 | 활성 명령 항목의 이름.                                   |
|--------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`has_debug_vis_implementation`](#isaaclab.managers.CommandManager.has_debug_vis_implementation) | 명령 항목에 디버그 시각화가 구현되었는지 여부. |

#### \_\_init_\_(cfg: [object](https://docs.python.org/3/library/functions.html#object), env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv))

명령 관리자를 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체 또는 딕셔너리 (`dict[str, CommandTermCfg]`).
  * **env** – 환경 인스턴스.

#### *property* active_terms *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

활성 명령 항목의 이름.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

명령 항목에 디버그 시각화가 구현되었는지 여부.

#### get_active_iterable_terms(env_idx: [int](https://docs.python.org/3/library/functions.html#int)) → [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [collections.abc.Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]]]

활성 항목을 튜플의 반복 가능한 시퀀스로 반환합니다.

튜플의 첫 번째 요소는 항목의 이름이고, 두 번째 요소는 항목의 원시 값입니다.

* **매개변수:**
  **env_idx** – 활성 항목을 가져올 특정 환경.
* **반환값:**
  활성 항목.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool))

명령 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 명령 데이터를 시각화할지 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 명령 생성기가 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

명령 항목을 재설정하고 그들의 메트릭을 로그로 남깁니다.

이 함수는 각 항목에 대한 명령 카운터를 재설정하고 명령을 재샘플링합니다. 각 에피소드의 시작 부분에서 호출해야 합니다.

* **매개변수:**
  **env_ids** – 재설정할 환경 ID 목록. 기본값은 None입니다.
* **반환값:**
  “Metrics/{term_name}/{metric_name}” 키 아래에 로그할 정보를 포함하는 딕셔너리.

#### compute(dt: [float](https://docs.python.org/3/library/functions.html#float))

명령을 업데이트합니다.

이 함수는 클래스가 관리하는 각 명령 항목을 호출합니다.

* **매개변수:**
  **dt** – 환경의 시간 간격.

#### get_command(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

지정된 명령 항목의 명령을 반환합니다.

* **매개변수:**
  **name** – 명령 항목의 이름.
* **반환값:**
  지정된 명령 항목의 명령 텐서.

#### get_term(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [CommandTerm](#isaaclab.managers.CommandTerm)

지정된 이름의 명령 항목을 반환합니다.

* **매개변수:**
  **name** – 명령 항목의 이름.
* **반환값:**
  지정된 이름의 명령 항목.

### *class* isaaclab.managers.CommandTerm

명령 항목을 구현하기 위한 기본 클래스입니다.

명령 항목은 목표 조건형 작업에 대한 에이전트 명령을 생성하는 데 사용됩니다. 예를 들어, 목표 조건형 내비게이션 작업의 경우, 명령 항목은 로봇이 이동해야 할 목표 위치를 생성하는 데 사용될 수 있습니다.

구성 객체에 지정된 고정 주파수로 명령을 재샘플링할 수 있는 메커니즘을 구현합니다. 또한, 명령 항목에 시각화 함수를 할당하여 시뮬레이터에서 명령을 시각화하는 데 사용할 수 있습니다.

**속성:**

| [`command`](#isaaclab.managers.CommandTerm.command)                                           | 명령 텐서.                                                  |
|-----------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [`has_debug_vis_implementation`](#isaaclab.managers.CommandTerm.has_debug_vis_implementation) | 명령 생성기에 디버그 시각화가 구현되었는지 여부. |

**메서드:**

| [`set_debug_vis`](#isaaclab.managers.CommandTerm.set_debug_vis)(debug_vis)   | 명령 데이터 시각화 여부를 설정합니다.   |
|------------------------------------------------------------------------------|-----------------------------------------------|
| [`reset`](#isaaclab.managers.CommandTerm.reset)([env_ids])                   | 명령 생성기를 재설정하고 메트릭을 로그로 남깁니다.  |
| [`compute`](#isaaclab.managers.CommandTerm.compute)(dt)                      | 명령을 계산합니다.                          |

#### *abstract property* command *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

명령 텐서. 형태는 (num_envs, command_dim)입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

명령 생성기에 디버그 시각화가 구현되었는지 여부입니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

명령 데이터를 시각화할지 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 명령 데이터를 시각화할지 여부입니다.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 명령 생성기가 디버그 시각화를 지원하지 않는 경우 False.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]

명령 생성기를 재설정하고 메트릭을 기록합니다.

이 함수는 명령 카운터를 재설정하고 명령을 재샘플링합니다. 에피소드 시작 시 호출해야 합니다.

* **매개변수:**
  **env_ids** – 재설정할 환경 ID 목록. 기본값은 None입니다.
* **반환값:**
  "{name}" 키 아래에 기록할 정보를 포함하는 사전입니다.

#### compute(dt: [float](https://docs.python.org/3/library/functions.html#float))

명령을 계산합니다.

* **매개변수:**
  **dt** – 이전 compute 호출 이후 경과된 시간 간격입니다.

### *class* isaaclab.managers.CommandTermCfg

명령 생성기 항목의 구성입니다.

**속성:**

| [`resampling_time_range`](#isaaclab.managers.CommandTermCfg.resampling_time_range)   | 명령이 변경되기 전 시간 [s].   |
|--------------------------------------------------------------------------------------|-----------------------------------------|
| [`debug_vis`](#isaaclab.managers.CommandTermCfg.debug_vis)                           | 디버그 정보를 시각화할지 여부입니다. |

#### resampling_time_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

명령이 변경되기 전 시간 [s].

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부. 기본값은 False입니다.

## 보상 관리자

### *class* isaaclab.managers.RewardManager

Bases: [`ManagerBase`](#isaaclab.managers.ManagerBase)

주어진 월드에 대한 보상 신호를 계산하는 관리자입니다.

보상 관리자는 가중된 보상 항목들의 합으로 총 보상을 계산합니다. 보상 항목은 보상 관리자 설정과 보상 항목 구성 중첩된 구성 클래스에서 파싱됩니다.

보상 항목은 관리자 설정과 각 항목의 매개변수를 포함하는 구성 클래스에서 파싱됩니다. 각 보상 항목은 [`RewardTermCfg`](#isaaclab.managers.RewardTermCfg) 클래스를 인스턴스화해야 합니다.

#### 참고
보상 관리자는 보상 항목의 `weight` 와 환경의 시간 간격 `dt` 을 곱합니다. 이를 통해 계산된 보상 항목이 환경에서 선택한 시간 간격과 균형을 이룰 수 있도록 합니다.

**메서드:**

| [`__init__`](#isaaclab.managers.RewardManager.__init__)(cfg, env)                                  | 보상 관리자를 초기화합니다.                                    |
|----------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| [`reset`](#isaaclab.managers.RewardManager.reset)([env_ids])                                       | 개별 보상 항목의 에피소드 합을 반환합니다.              |
| [`compute`](#isaaclab.managers.RewardManager.compute)(dt)                                          | 개별 항목들의 가중 합으로 보상 신호를 계산합니다. |
| [`set_term_cfg`](#isaaclab.managers.RewardManager.set_term_cfg)(term_name, cfg)                    | 관리자에 지정된 항목의 구성 설정을 합니다.    |
| [`get_term_cfg`](#isaaclab.managers.RewardManager.get_term_cfg)(term_name)                         | 지정된 항목의 구성을 가져옵니다.                    |
| [`get_active_iterable_terms`](#isaaclab.managers.RewardManager.get_active_iterable_terms)(env_idx) | 활성 항목을 튜플 형태의 반복 가능한 시퀀스로 반환합니다.          |
| [`find_terms`](#isaaclab.managers.RewardManager.find_terms)(name_keys)                             | 이름에 따라 관리자에서 항목을 찾습니다.                     |

**속성:**

| [`active_terms`](#isaaclab.managers.RewardManager.active_terms)   | 활성 보상 항목 이름.             |
|-------------------------------------------------------------------|------------------------------------------|
| [`device`](#isaaclab.managers.RewardManager.device)               | 계산을 수행할 디바이스. |
| [`num_envs`](#isaaclab.managers.RewardManager.num_envs)           | 환경 수.                  |

#### \_\_init_\_(cfg: [object](https://docs.python.org/3/library/functions.html#object), env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv))

보상 관리자를 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체 또는 사전(`dict[str, RewardTermCfg]`).
  * **env** – 환경 인스턴스.

#### *property* active_terms *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

활성 보상 항목 이름.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

개별 보상 항목의 에피소드 합을 반환합니다.

* **매개변수:**
  **env_ids** – 개별 보상 항목의 에피소드 합을 반환할 환경 ID. 기본값은 모든 환경 ID입니다.
* **반환값:**
  개별 보상 항목의 에피소드 합을 포함하는 사전.

#### compute(dt: [float](https://docs.python.org/3/library/functions.html#float)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

개별 항목들의 가중 합으로 보상 신호를 계산합니다.

이 함수는 클래스가 관리하는 각 보상 항목을 호출하여 순 보상 신호를 계산합니다. 또한 개별 보상 항목에 해당하는 에피소드 합도 업데이트합니다.

* **매개변수:**
  **dt** – 환경의 시간 간격.
* **반환값:**
  형태가 (num_envs,)인 순 보상 신호.

#### set_term_cfg(term_name: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [RewardTermCfg](#isaaclab.managers.RewardTermCfg))

지정된 항목의 구성 설정을 관리자에 적용합니다.

* **매개변수:**
  * **term_name** – 보상 항목의 이름.
  * **cfg** – 보상 항목의 구성.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 항목 이름이 존재하지 않을 경우.

#### get_term_cfg(term_name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [RewardTermCfg](#isaaclab.managers.RewardTermCfg)

지정된 항목의 구성 설정을 가져옵니다.

* **매개변수:**
  **term_name** – 보상 항목의 이름.
* **반환값:**
  보상 항목의 구성.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 항목 이름이 존재하지 않을 경우.

#### get_active_iterable_terms(env_idx: [int](https://docs.python.org/3/library/functions.html#int)) → [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [collections.abc.Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]]]

활성 항목을 튜플 형태의 반복 가능한 시퀀스로 반환합니다.

튜플의 첫 번째 요소는 항목 이름이고, 두 번째 요소는 항목의 원시 값입니다.

* **매개변수:**
  **env_idx** – 활성 항목을 가져올 특정 환경.
* **반환값:**
  활성 항목.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 수행할 디바이스.

#### find_terms(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)]) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

이름에 따라 관리자에서 항목을 찾습니다.

이 함수는 관리자에서 이름에 기반하여 항목을 검색합니다. 이름에는 정규 표현식 또는 정규 표현식 리스트를 지정할 수 있습니다. 검색은 관리자의 활성 항목에서 수행됩니다.

이름 매칭에 대한 자세한 내용은 `resolve_matching_names()` 함수를 참조하세요.

* **매개변수:**
  **name_keys** – 용어 이름과 일치하는 정규 표현식 또는 정규 표현식 리스트입니다.
* **반환값:**
  입력 키와 일치하는 용어 이름 목록.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 수.

### *class* isaaclab.managers.RewardTermCfg

보상 항목의 구성.

**메서드:**

| [`__new__`](#isaaclab.managers.RewardTermCfg.__new__)(\*\*kwargs)   |    |
|---------------------------------------------------------------------|----|

#### \_\_new_\_(\*\*kwargs)

## 종료 관리자

### *class* isaaclab.managers.TerminationManager

Bases: [`ManagerBase`](#isaaclab.managers.ManagerBase)

주어진 월드에 대한 완료 신호를 계산하는 관리자.

종료 관리자는 종료 항목의 조합으로 종료 신호(또는 dones)를 계산합니다. 각 종료 항목은 환경을 인수로 받아 boolean 텐서 형태로 (num_envs,) 모양을 반환하는 함수입니다. 종료 관리자는 모든 종료 항목에 대한 논리 OR(union)를 수행하여 종료 신호를 계산합니다.

[Gymnasium API](https://gymnasium.farama.org/tutorials/gymnasium_basics/handling_time_limits/)를 따를 때, 종료 신호는 다음 신호들의 논리 OR로 계산됩니다:

* **타임아웃(Time-out)**: 환경이 외부에서 정의된 조건(마르코프 결정 과정의 범위를 벗어난 조건)으로 인해 종료된 경우 true로 설정됩니다. 예를 들어, 에피소드가 타임아웃(즉, 최대 에피소드 길이에 도달)된 경우 환경이 종료될 수 있습니다.
* **종료(Terminated)**: 환경이 환경에서 정의된 터미널 상태에 도달한 경우 true로 설정됩니다. 이 상태는 작업 성공, 작업 실패, 로봇 낙하 등과 대응할 수 있습니다.

이러한 신호는 [`time_outs`](#isaaclab.managers.TerminationManager.time_outs)와 [`terminated`](#isaaclab.managers.TerminationManager.terminated) 속성을 통해 개별적으로 접근할 수 있습니다.

종료 항목은 관리자 설정과 각 항목의 매개변수를 포함하는 구성 클래스에서 파싱됩니다. 각 종료 항목은 [`TerminationTermCfg`](#isaaclab.managers.TerminationTermCfg) 클래스를 인스턴스화해야 합니다. 항목의 구성에서 [`TerminationTermCfg.time_out`](#isaaclab.managers.TerminationTermCfg.time_out)는 해당 항목이 타임아웃인지 종료 항목인지 결정합니다.

**메서드:**

| [`__init__`](#isaaclab.managers.TerminationManager.__init__)(cfg, env)                                  | 종료 관리자를 초기화합니다.                                        |
|---------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`reset`](#isaaclab.managers.TerminationManager.reset)([env_ids])                                       | 개별 종료 항목의 에피소드 횟수를 반환합니다.                |
| [`compute`](#isaaclab.managers.TerminationManager.compute)()                                            | 개별 항목의 논리 OR를 결합하여 종료 신호를 계산합니다.               |
| [`get_term`](#isaaclab.managers.TerminationManager.get_term)(name)                                      | 지정된 이름의 현재 단계에서의 종료 항목 값을 반환합니다. |
| [`get_active_iterable_terms`](#isaaclab.managers.TerminationManager.get_active_iterable_terms)(env_idx) | 활성 항목을 튜플의 이터러블 시퀀스로 반환합니다.                    |
| [`set_term_cfg`](#isaaclab.managers.TerminationManager.set_term_cfg)(term_name, cfg)                    | 지정된 항목의 구성을 관리자에 설정합니다.              |
| [`get_term_cfg`](#isaaclab.managers.TerminationManager.get_term_cfg)(term_name)                         | 지정된 항목의 구성을 가져옵니다.                              |
| [`find_terms`](#isaaclab.managers.TerminationManager.find_terms)(name_keys)                             | 이름 기준으로 관리자에서 항목을 찾습니다.                               |

**속성:**

| [`active_terms`](#isaaclab.managers.TerminationManager.active_terms)   | 활성 종료 항목 이름.                  |
|------------------------------------------------------------------------|----------------------------------------------------|
| [`dones`](#isaaclab.managers.TerminationManager.dones)                 | 전체 종료 신호.                                    |
| [`time_outs`](#isaaclab.managers.TerminationManager.time_outs)         | 타임아웃 신호(최대 에피소드 길이 도달).  |
| [`terminated`](#isaaclab.managers.TerminationManager.terminated)       | 종료 신호(터미널 상태 도달). |
| [`device`](#isaaclab.managers.TerminationManager.device)               | 계산을 수행할 장치.           |
| [`num_envs`](#isaaclab.managers.TerminationManager.num_envs)           | 환경의 수.                            |

#### \_\_init_\_(cfg: [object](https://docs.python.org/3/library/functions.html#object), env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv))

종료 관리자를 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체 또는 사전(`dict[str, TerminationTermCfg]`).
  * **env** – 환경 객체.

#### *property* active_terms *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

활성 종료 항목 이름.

#### *property* dones *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

전체 종료 신호. 크기는 (num_envs,)입니다.

#### *property* time_outs *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

타임아웃 신호(최대 에피소드 길이 도달). 크기는 (num_envs,)입니다.

이 신호는 환경이 외부에서 정의된 조건(마르코프 결정 과정의 범위를 벗어난 조건)으로 인해 종료된 경우 true로 설정됩니다. 예를 들어, 에피소드가 타임아웃(즉, 최대 에피소드 길이에 도달)된 경우 환경이 종료될 수 있습니다.

#### *property* terminated *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

종료 신호(터미널 상태 도달). 크기는 (num_envs,)입니다.

이 신호는 환경이 환경에서 정의된 터미널 상태에 도달한 경우 true로 설정됩니다. 이 상태는 작업 성공, 작업 실패, 로봇 낙하 등과 대응할 수 있습니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

개별 종료 항목의 에피소드 횟수를 반환합니다.

* **매개변수:**
  **env_ids** – 환경 ID. 기본값은 None이며, 이때 모든 환경이 고려됩니다.
* **반환값:**
  개별 보상 항목의 에피소드 합을 포함한 딕셔너리.

#### compute() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

개별 항목의 논리 OR를 결합하여 종료 신호를 계산합니다.

이 함수는 클래스가 관리하는 각 종료 항목을 호출하고 논리 OR 연산을 수행하여 전체 종료 신호를 계산합니다.

* **반환값:**
  크기가 (num_envs,)인 결합된 종료 신호.

#### get_term(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

지정된 이름의 현재 단계에서의 종료 항목 값을 반환합니다.

* **매개변수:**
  **name** – 종료 항목 이름.
* **반환값:**
  해당 종료 항목 값. 크기는 (num_envs,)입니다.

#### get_active_iterable_terms(env_idx: [int](https://docs.python.org/3/library/functions.html#int)) → [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [collections.abc.Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]]]

활성 항목을 튜플의 이터러블 시퀀스로 반환합니다.

튜플의 첫 번째 요소는 항목 이름이고, 두 번째 요소는 현재 단계에서 기록된 항목의 원시 값입니다.

* **매개변수:**
  **env_idx** – 활성 항목을 가져올 특정 환경.
* **반환값:**
  활성 항목.

#### set_term_cfg(term_name: [str](https://docs.python.org/3/library/stdtypes.html#str), cfg: [TerminationTermCfg](#isaaclab.managers.TerminationTermCfg))

지정된 항목의 구성을 관리자에 설정합니다.

* **매개변수:**
  * **term_name** – 종료 항목 이름.
  * **cfg** – 종료 항목 구성.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 항목 이름이 발견되지 않은 경우.

#### get_term_cfg(term_name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [TerminationTermCfg](#isaaclab.managers.TerminationTermCfg)

지정된 항목의 구성을 가져옵니다.

* **매개변수:**
  **term_name** – 종료 항목 이름.
* **반환값:**
  종료 항목 구성.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 항목 이름이 발견되지 않은 경우.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 수행할 장치.

#### find_terms(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)]) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

이름 기준으로 관리자에서 항목을 찾습니다.

이 함수는 관리자에서 이름을 기준으로 용어를 검색합니다. 이름은 정규 표현식 또는 정규 표현식 목록으로 지정할 수 있습니다. 검색은 관리자의 활성 용어에 대해 수행됩니다.

이름 매칭에 대한 자세한 내용은 `resolve_matching_names()` 함수를 확인하세요.

* **매개변수:**
  **name_keys** – 용어 이름을 일치시키기 위한 정규 표현식 또는 정규 표현식 목록.
* **반환값:**
  입력 키와 일치하는 용어 이름의 목록.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 수.

### *class* isaaclab.managers.TerminationTermCfg

종료 용어의 구성.

**속성:**

| [`params`](#isaaclab.managers.TerminationTermCfg.params)     | 함수의 키워드 인자로 전달될 매개변수.   |
|--------------------------------------------------------------|---------------------------------------------------------------------|
| [`func`](#isaaclab.managers.TerminationTermCfg.func)         | 호출할 함수의 이름.                              |
| [`time_out`](#isaaclab.managers.TerminationTermCfg.time_out) | 종료 용어가 에피소드 타임아웃에 기여하는지 여부. |

#### params *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any | [SceneEntityCfg](#isaaclab.managers.SceneEntityCfg)]*

함수의 키워드 인자로 전달될 매개변수. 기본값은 빈 딕셔너리입니다.

#### 참고
값이 [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체인 경우, 관리자는 `InteractiveScene`에서 장면을 쿼리하고, [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체에 지정된 대로 엔티티의 조인트와 바디를 처리합니다.

#### func *: Callable[..., [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]*

호출할 함수의 이름.

이 함수는 환경 객체와 기타 매개변수를 입력으로 받아서, 형태가 (num_envs,)인 토치 불리언 텐서로 종료 신호를 반환해야 합니다.

#### time_out *: [bool](https://docs.python.org/3/library/functions.html#bool)*

종료 용어가 에피소드 타임아웃에 기여하는지 여부. 기본값은 False입니다.

#### 참고
일반적으로 고정된 시간 제한이 있는 작업에 해당합니다.

## 교육 과정 관리자

### *class* isaaclab.managers.CurriculumManager

Bases: [`ManagerBase`](#isaaclab.managers.ManagerBase)

특정 교육 과정을 구현하고 실행하는 관리자.

교육 과정 관리자는 교육 과정에 따라 항목 목록을 호출하여 환경의 다양한 양을 업데이트함으로써, 에이전트가 개선됨에 따라 학습 작업을 점진적으로 어렵게 만들어 학습을 안정화하는 데 도움을 줍니다.

교육 과정 항목은 관리자의 설정과 각 항목의 매개변수를 포함하는 구성 클래스에서 파싱됩니다. 각 교육 과정 항목은 [`CurriculumTermCfg`](#isaaclab.managers.CurriculumTermCfg) 클래스를 인스턴스화해야 합니다.

**메서드:**

| [`__init__`](#isaaclab.managers.CurriculumManager.__init__)(cfg, env)                                  | 관리자를 초기화합니다.                                   |
|--------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`reset`](#isaaclab.managers.CurriculumManager.reset)([env_ids])                                       | 개별 교육 과정 항목의 현재 상태를 반환합니다. |
| [`compute`](#isaaclab.managers.CurriculumManager.compute)([env_ids])                                   | 교육 과정 항목을 업데이트합니다.                              |
| [`get_active_iterable_terms`](#isaaclab.managers.CurriculumManager.get_active_iterable_terms)(env_idx) | 활성 항목을 튜플의 반복 가능한 시퀀스로 반환합니다.  |
| [`find_terms`](#isaaclab.managers.CurriculumManager.find_terms)(name_keys)                             | 이름을 기준으로 관리자에서 항목을 찾습니다.             |

**속성:**

| [`active_terms`](#isaaclab.managers.CurriculumManager.active_terms)   | 활성 교육 과정 항목의 이름.         |
|-----------------------------------------------------------------------|------------------------------------------|
| [`device`](#isaaclab.managers.CurriculumManager.device)               | 연산을 수행할 장치. |
| [`num_envs`](#isaaclab.managers.CurriculumManager.num_envs)           | 환경의 수.                  |

#### \_\_init_\_(cfg: [object](https://docs.python.org/3/library/functions.html#object), env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv))

관리자를 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체 또는 딕셔너리 (`dict[str, CurriculumTermCfg]`)
  * **env** – 환경 객체.
* **예외:**
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – 교육 과정 항목이 [`CurriculumTermCfg`](#isaaclab.managers.CurriculumTermCfg) 타입이 아닌 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 교육 과정 항목 구성이 함수 시그니처를 충족하지 않는 경우.

#### *property* active_terms *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

활성 교육 과정 항목의 이름.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]

개별 교육 과정 항목의 현재 상태를 반환합니다.

#### 참고
이 함수는 환경 인덱스 `env_ids`를 사용하지 않고 모든 항목의 상태를 로그합니다. 인수는 다른 클래스와의 일관성을 유지하기 위해 존재합니다.

* **반환값:**
  교육 과정 항목과 그 상태의 딕셔너리.

#### compute(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

교육 과정 항목을 업데이트합니다.

이 함수는 클래스가 관리하는 각 교육 과정 항목을 호출합니다.

* **매개변수:**
  **env_ids** – 업데이트할 환경 ID 목록.
  None인 경우 모든 환경이 업데이트됩니다. 기본값은 None입니다.

#### get_active_iterable_terms(env_idx: [int](https://docs.python.org/3/library/functions.html#int)) → [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [collections.abc.Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]]]

활성 항목을 튜플의 반복 가능한 시퀀스로 반환합니다.

튜플의 첫 번째 요소는 항목의 이름이고, 두 번째 요소는 항목의 원시 값입니다.

* **매개변수:**
  **env_idx** – 활성 항목을 가져올 특정 환경.
* **반환값:**
  활성 항목.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

연산을 수행할 장치.

#### find_terms(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)]) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

이름을 기준으로 관리자에서 항목을 찾습니다.

이 함수는 관리자에서 이름을 기준으로 용어를 검색합니다. 이름은 정규 표현식 또는 정규 표현식 목록으로 지정할 수 있습니다. 검색은 관리자의 활성 용어에 대해 수행됩니다.

이름 매칭에 대한 자세한 내용은 `resolve_matching_names()` 함수를 확인하세요.

* **매개변수:**
  **name_keys** – 용어 이름을 일치시키기 위한 정규 표현식 또는 정규 표현식 목록.
* **반환값:**
  입력 키와 일치하는 용어 이름의 목록.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 수.

### *class* isaaclab.managers.CurriculumTermCfg

교육 과정 용어의 구성.

**속성:**

| [`func`](#isaaclab.managers.CurriculumTermCfg.func)     | 호출할 함수의 이름.                            |
|---------------------------------------------------------|-------------------------------------------------------------------|
| [`params`](#isaaclab.managers.CurriculumTermCfg.params) | 함수의 키워드 인자로 전달될 매개변수. |

#### func *: Callable[..., [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)]*

호출할 함수의 이름.

이 함수는 환경 객체, 환경 인덱스 및 기타 매개변수를 입력으로 받아서 로깅 목적으로 교육 과정 상태를 반환해야 합니다. 함수가 None을 반환하는 경우, 교육 과정 상태는 로그되지 않습니다.

#### params *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any | [SceneEntityCfg](#isaaclab.managers.SceneEntityCfg)]*

함수의 키워드 인자로 전달될 매개변수. 기본값은 빈 딕셔너리입니다.

#### 참고
값이 [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체인 경우, 관리자는 `InteractiveScene`에서 장면을 쿼리하고, [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체에 지정된 대로 엔티티의 조인트와 바디를 처리합니다.
from the `InteractiveScene`와 [`SceneEntityCfg`](#isaaclab.managers.SceneEntityCfg) 객체에 지정된 대로 엔티티의 관절 및 본문을 처리합니다.

## 레코더 관리자

### *class* isaaclab.managers.RecorderManager

기반: [`ManagerBase`](#isaaclab.managers.ManagerBase)

레코더 용어로부터 데이터 기록을 담당하는 관리자입니다.

**메서드:**

| [`__init__`](#isaaclab.managers.RecorderManager.__init__)(cfg, env)                                               | 레코더 관리자를 초기화합니다.                                                                                   |
|-------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| [`reset`](#isaaclab.managers.RecorderManager.reset)([env_ids])                                                    | 레코더 데이터를 재설정합니다.                                                                                          |
| [`get_episode`](#isaaclab.managers.RecorderManager.get_episode)(env_id)                                           | 지정된 환경 ID에 대한 에피소드 데이터를 반환합니다.                                                             |
| [`add_to_episodes`](#isaaclab.managers.RecorderManager.add_to_episodes)(key, value[, env_ids])                    | 지정된 환경 ID에 대한 에피소드에 주어진 키-값 쌍을 추가합니다.                                       |
| [`set_success_to_episodes`](#isaaclab.managers.RecorderManager.set_success_to_episodes)(env_ids, success_values)  | 지정된 환경 ID에 대한 에피소드에 작업 성공 값을 설정합니다.                                        |
| [`record_pre_step`](#isaaclab.managers.RecorderManager.record_pre_step)()                                         | 레코더 용어의 pre-step 함수를 트리거합니다.                                                                     |
| [`record_post_step`](#isaaclab.managers.RecorderManager.record_post_step)()                                       | 레코더 용어의 post-step 함수를 트리거합니다.                                                                    |
| [`find_terms`](#isaaclab.managers.RecorderManager.find_terms)(name_keys)                                          | 이름에 기반하여 관리자에서 레코더 용어를 찾습니다.                                                                      |
| [`get_active_iterable_terms`](#isaaclab.managers.RecorderManager.get_active_iterable_terms)(env_idx)              | 활성 레코더 용어를 튜플의 반복 가능한 시퀀스로 반환합니다.                                                           |
| [`record_post_physics_decimation_step`](#isaaclab.managers.RecorderManager.record_post_physics_decimation_step)() | 레코더 용어의 감소 루프에서 post-physics 단계 함수를 트리거합니다.                                     |
| [`record_pre_reset`](#isaaclab.managers.RecorderManager.record_pre_reset)(env_ids[, force_export_or_skip])        | 레코더 용어의 pre-reset 함수를 트리거합니다.                                                                    |
| [`record_post_reset`](#isaaclab.managers.RecorderManager.record_post_reset)(env_ids)                              | 레코더 용어의 post-reset 함수를 트리거합니다.                                                                   |
| [`get_ep_meta`](#isaaclab.managers.RecorderManager.get_ep_meta)()                                                 | 에피소드 메타데이터를 가져옵니다.                                                                                          |
| [`export_episodes`](#isaaclab.managers.RecorderManager.export_episodes)([env_ids, demo_ids])                      | 지정된 환경 ID에 대한 에피소드를 마무리하고 내보냅니다.                                                  |
| [`close`](#isaaclab.managers.RecorderManager.close)()                                                             | 레코더 관리자를 종료하기 위해 남은 데이터를 파일로 내보내고 레코더 용어를 적절히 종료합니다. |

**속성:**

| [`active_terms`](#isaaclab.managers.RecorderManager.active_terms)                                           | 활성 레코더 용어의 이름.           |
|-------------------------------------------------------------------------------------------------------------|------------------------------------------|
| [`exported_successful_episode_count`](#isaaclab.managers.RecorderManager.exported_successful_episode_count) | 성공한 에피소드의 수.           |
| [`exported_failed_episode_count`](#isaaclab.managers.RecorderManager.exported_failed_episode_count)         | 실패한 에피소드의 수.               |
| [`device`](#isaaclab.managers.RecorderManager.device)                                                       | 계산에 사용할 장치. |
| [`num_envs`](#isaaclab.managers.RecorderManager.num_envs)                                                   | 환경의 수.                  |

#### \_\_init_\_(cfg: [object](https://docs.python.org/3/library/functions.html#object), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

레코더 관리자를 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체 또는 사전 (`dict[str, RecorderTermCfg]`).
  * **env** – 환경 인스턴스.

#### *property* active_terms *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

활성 레코더 용어의 이름.

#### *property* exported_successful_episode_count *: [int](https://docs.python.org/3/library/functions.html#int)*

성공한 에피소드의 수.

* **매개변수:**
  **env_id** – 환경 ID. 기본값은 None이며,この場合 모든 환경이 고려됩니다.
* **반환값:**
  성공한 에피소드의 수.

#### *property* exported_failed_episode_count *: [int](https://docs.python.org/3/library/functions.html#int)*

실패한 에피소드의 수.

* **매개변수:**
  **env_id** – 환경 ID. 기본값은 None이며,この場合 모든 환경이 고려됩니다.
* **반환값:**
  실패한 에피소드의 수.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

레코더 데이터를 재설정합니다.

* **매개변수:**
  **env_ids** – 환경 ID. 기본값은 None이며,この場合 모든 환경이 고려됩니다.
* **반환값:**
  빈 사전.

#### get_episode(env_id: [int](https://docs.python.org/3/library/functions.html#int)) → EpisodeData

지정된 환경 ID에 대한 에피소드 데이터를 반환합니다.

* **매개변수:**
  **env_id** – 환경 ID.
* **반환값:**
  지정된 환경 ID에 대한 에피소드 데이터.

#### add_to_episodes(key: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [dict](https://docs.python.org/3/library/stdtypes.html#dict), env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

지정된 환경 ID에 대한 에피소드에 주어진 키-값 쌍을 추가합니다.

* **매개변수:**
  * **key** – 에피소드에 추가될 값의 키. 키는 '/'로 구분된 중첩 키를 포함할 수 있습니다. 예를 들어, “obs/joint_pos”는 기본 사전의 [‘obs’][‘policy’]에 해당 값을 추가합니다.
  * **value** – 에피소드에 추가될 값. 값은 텐서 또는 텐서의 중첩 사전일 수 있습니다. 값의 텐서 형태는 (env_ids, …)입니다.
  * **env_ids** – 환경 ID. 기본값은 None이며,この場合 모든 환경이 고려됩니다.

#### set_success_to_episodes(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), success_values: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor))

지정된 환경 ID에 대한 에피소드에 작업 성공 값을 설정합니다.

* **매개변수:**
  * **env_ids** – 환경 ID. 기본값은 None이며,この場合 모든 환경이 고려됩니다.
  * **success_values** – 에피소드에 설정할 작업 성공 값. 텐서의 형태는 (env_ids, 1)입니다.

#### record_pre_step() → [None](https://docs.python.org/3/library/constants.html#None)

레코더 용어의 pre-step 함수를 트리거합니다.

#### record_post_step() → [None](https://docs.python.org/3/library/constants.html#None)

레코더 용어의 post-step 함수를 트리거합니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용할 장치.

#### find_terms(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)]) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

이름에 기반하여 관리자에서 레코더 용어를 찾습니다.

이 함수는 이름을 기준으로 관리자에서 레코더 용어를 검색합니다. 이름은 정규 표현식 또는 정규 표현식 리스트로 지정할 수 있습니다. 검색은 관리자의 활성 용어에 대해 수행됩니다.

이름 매칭에 대한 자세한 내용은 `resolve_matching_names()` 함수를 참조하십시오.

* **매개변수:**
  **name_keys** – 용어 이름과 일치시킬 정규 표현식 또는 정규 표현식 리스트.
* **반환값:**
  입력 키와 일치하는 용어 이름 목록입니다.

#### get_active_iterable_terms(env_idx: [int](https://docs.python.org/3/library/functions.html#int)) → [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [collections.abc.Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]]]

활성 용어를 튜플의 iterable 시퀀스로 반환합니다.

튜플의 첫 번째 요소는 용어의 이름이고, 두 번째 요소는 용어의 원시 값입니다.

* **반환값:**
  활성 용어입니다.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 수입니다.

#### record_post_physics_decimation_step() → [None](https://docs.python.org/3/library/constants.html#None)

디메이션 루프에서 post-physics 단계 함수에 대한 레코더 용어를 트리거합니다.

#### record_pre_reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), force_export_or_skip=None) → [None](https://docs.python.org/3/library/constants.html#None)

pre-reset 함수에 대한 레코더 용어를 트리거합니다.

* **매개변수:**
  **env_ids** – 리셋이 트리거되는 환경 ID입니다.

#### record_post_reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None)) → [None](https://docs.python.org/3/library/constants.html#None)

post-reset 함수에 대한 레코더 용어를 트리거합니다.

* **매개변수:**
  **env_ids** – 리셋이 트리거되는 환경 ID입니다.

#### get_ep_meta() → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

에피소드 메타데이터를 가져옵니다.

#### export_episodes(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, demo_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [None](https://docs.python.org/3/library/constants.html#None)

주어진 환경 ID에 대한 에피소드를 결론짓고 내보냅니다.

* **매개변수:**
  * **env_ids** – 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 고려됩니다.
  * **demo_ids** – 내보낸 에피소드에 대한 사용자 지정 식별자입니다.
    제공된 경우, 에피소드는 데이터세트에서 “demo_{demo_id}”라는 이름을 갖습니다.
    두 개 모두 제공된 경우, env_ids와 동일한 길이를 가져야 합니다.
    None인 경우, 기본 순차적 이름 지정 방식을 사용합니다. 기본값은 None입니다.

#### close()

파일에 남아 있는 데이터를 내보내서 레코더 관리자를 닫고, 레코더 용어를 적절히 닫습니다.

### *class* isaaclab.managers.RecorderTermCfg

레코더 용어에 대한 구성입니다.

**속성:**

| [`class_type`](#isaaclab.managers.RecorderTermCfg.class_type)   | 연결된 레코더 용어 클래스입니다.   |
|-----------------------------------------------------------------|---------------------------------------|

#### class_type *: [type](https://docs.python.org/3/library/functions.html#type)[RecorderTerm]*

연결된 레코더 용어 클래스입니다.

클래스는 `isaaclab.managers.recorder_manager.RecorderTerm`을 상속해야 합니다.
