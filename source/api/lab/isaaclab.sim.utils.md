# isaaclab.sim.utils

USD 작업 주변의 유틸리티입니다.

### 서브모듈

| [`stage`](#module-isaaclab.sim.utils.stage)           | USD 스테이지 작업을 위한 유틸리티.                         |
|-------------------------------------------------------|-------------------------------------------------------------------|
| [`queries`](#module-isaaclab.sim.utils.queries)       | USD 스테이지 쿼리 유틸리티.                             |
| [`prims`](#module-isaaclab.sim.utils.prims)           | USD 프림 생성 및 조작 유틸리티.                |
| [`transforms`](#module-isaaclab.sim.utils.transforms) | USD 트랜스폼(xform) 작업 유틸리티.      |
| [`semantics`](#module-isaaclab.sim.utils.semantics)   | USD 프림에 시맨틱 라벨 적용 및 제거 유틸리티. |
| [`legacy`](#module-isaaclab.sim.utils.legacy)         | 레거시 기능 유틸리티.                               |

## 스테이지

USD 스테이지 작업을 위한 유틸리티입니다.

**함수:**

| [`create_new_stage`](#isaaclab.sim.utils.stage.create_new_stage)()                                        | USD 컨텍스트에 연결된 새 스테이지 생성.                                  |
|-----------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| [`create_new_stage_in_memory`](#isaaclab.sim.utils.stage.create_new_stage_in_memory)()                    | 지원되는 경우 메모리에 새 스테이지 생성.                                     |
| [`is_current_stage_in_memory`](#isaaclab.sim.utils.stage.is_current_stage_in_memory)()                    | 현재 스테이지가 메모리에 있는지 확인.                                        |
| [`open_stage`](#isaaclab.sim.utils.stage.open_stage)(usd_path)                                            | 지정된 USD 파일을 열고 현재 열려 있는 스테이지 대체.                      |
| [`use_stage`](#isaaclab.sim.utils.stage.use_stage)(stage)                                                 | 지원되는 경우 스레드 로컬 스테이지를 설정하는 컨텍스트 관리자.                                                 |
| [`update_stage`](#isaaclab.sim.utils.stage.update_stage)()                                                | 애플리케이션 업데이트 사이클을 트리거하여 현재 스테이지 업데이트.             |
| [`save_stage`](#isaaclab.sim.utils.stage.save_stage)(usd_path[, save_and_reload_in_place])                | 현재 스테이지의 루트 레이어 내용을 지정된 USD 파일에 저장. |
| [`close_stage`](#isaaclab.sim.utils.stage.close_stage)([callback_fn])                                     | 현재 USD 스테이지 닫기.                                                    |
| [`clear_stage`](#isaaclab.sim.utils.stage.clear_stage)([predicate])                                       | 언두 명령 버퍼를 채우지 않고 스테이지의 모든 프림 삭제.       |
| [`is_stage_loading`](#isaaclab.sim.utils.stage.is_stage_loading)()                                        | 파일이 로드 중인지 확인하는 편의 함수.                                       |
| [`get_current_stage`](#isaaclab.sim.utils.stage.get_current_stage)([fabric])                              | 현재 열려 있는 USD 또는 패브릭 스테이지 가져오기                               |
| [`get_current_stage_id`](#isaaclab.sim.utils.stage.get_current_stage_id)()                                | 현재 열려 있는 스테이지 ID 가져오기.                                           |
| [`attach_stage_to_usd_context`](#isaaclab.sim.utils.stage.attach_stage_to_usd_context)([attaching_early]) | 현재 USD 스테이지를 메모리에서 USD 컨텍스트에 연결.                     |

### isaaclab.sim.utils.stage.create_new_stage() → pxr.Usd.Stage

USD 컨텍스트에 연결된 새 스테이지 생성.

* **반환값:**
  생성된 USD 스테이지.
* **반환 타입:**
  Usd.Stage
* **예외:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 새 스테이지 생성 실패 시.

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> sim_utils.create_new_stage()
Usd.Stage.Open(rootLayer=Sdf.Find('anon:0x7fba6c04f840:World7.usd'),
               sessionLayer=Sdf.Find('anon:0x7fba6c01c5c0:World7-session.usda'),
               pathResolverContext=<invalid repr>)
```

### isaaclab.sim.utils.stage.create_new_stage_in_memory() → pxr.Usd.Stage

지원되는 경우 메모리에 새 스테이지 생성.

#### 버전 추가됨
버전 2.3.0에서 새로 추가: 이 함수는 Isaac Sim 5.0 이상에서 사용 가능합니다. 하위 호환성을 위해 USD 컨텍스트에 연결된 새 스테이지 생성으로 폴백됩니다.

* **반환값:**
  메모리에 있는 새 스테이지.

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> sim_utils.create_new_stage_in_memory()
Usd.Stage.Open(rootLayer=Sdf.Find('anon:0xf7b00e0:tmp.usda'),
               sessionLayer=Sdf.Find('anon:0xf7cd2e0:tmp-session.usda'),
               pathResolverContext=<invalid repr>)
```

### isaaclab.sim.utils.stage.is_current_stage_in_memory() → [bool](https://docs.python.org/3/library/functions.html#bool)

현재 스테이지가 메모리에 있는지 확인.

이 함수는 현재 USD 스테이지의 스테이지 ID를 USD 컨텍스트 스테이지의 스테이지 ID와 비교합니다.

* **반환값:**
  현재 스테이지가 메모리에 있는지 여부.

### isaaclab.sim.utils.stage.open_stage(usd_path: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

주어진 USD 파일을 열고 현재 열려 있는 스테이지를 대체합니다.

* **매개변수:**
  **usd_path** – 열 USD 파일의 경로.
* **반환값:**
  작업이 성공하면 True, 그렇지 않으면 False.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 경로가 USD에서 지원되지 않는 파일 유형일 때.

### isaaclab.sim.utils.stage.use_stage(stage: pxr.Usd.Stage) → [Generator](https://docs.python.org/3/library/collections.abc.html#collections.abc.Generator)[[None](https://docs.python.org/3/library/constants.html#None), [None](https://docs.python.org/3/library/constants.html#None), [None](https://docs.python.org/3/library/constants.html#None)]

지원되는 경우 스레드 로컬 스테이지를 설정하는 컨텍스트 관리자.

이 함수는 컨텍스트 관리자의 지속 시간 동안 스테이지를 스레드 로컬 컨텍스트에 바인딩합니다.
컨텍스트 관리자 내에서는 [`get_current_stage()`](#isaaclab.sim.utils.stage.get_current_stage) 호출 시 컨텍스트 관리자에 지정된 스테이지를 반환합니다.
컨텍스트 관리자가 종료된 후에는 USD 컨텍스트에 연결된 기본 스테이지로 복원됩니다.

#### 버전 추가됨
버전 2.3.0에서 새로 추가: 이 함수는 Isaac Sim 5.0 이상에서 사용 가능합니다. 하위 호환성을 위해 Isaac Sim < 5.0에서는 no-op 컨텍스트 관리자로 폴백됩니다.

* **매개변수:**
  **stage** – 컨텍스트에 설정할 스테이지.
* **반환값:**
  컨텍스트에 스테이지를 설정하는 컨텍스트 관리자.
* **예외:**
  [**AssertionError**](https://docs.python.org/3/library/exceptions.html#AssertionError) – 스테이지가 USD 스테이지 인스턴스가 아닐 때.

### 예시

```pycon
>>> from pxr import Usd
>>> import isaaclab.sim as sim_utils
>>>
>>> stage_in_memory = Usd.Stage.CreateInMemory()
>>> with sim_utils.use_stage(stage_in_memory):
...     # 지정된 스테이지에서 작업 수행
...     pass
>>> # USD 컨텍스트에 연결된 기본 스테이지에서 작업 수행
```

### isaaclab.sim.utils.stage.update_stage() → [None](https://docs.python.org/3/library/constants.html#None)

애플리케이션 업데이트 사이클을 트리거하여 현재 스테이지 업데이트.

이 함수는 애플리케이션 인터페이스의 단일 업데이트 사이클을 트리거하며, 이로 인해 단계와 관련된 모든 시스템(렌더링, 물리 등)이 업데이트됩니다.
단계에 적용된 변경 사항이 올바르게 처리되고 시뮬레이션에 반영되도록 보장하는 데 필요합니다.

#### 참고
이 함수는 스테이지 업데이트가 렌더링, 물리 및 기타 시스템을 포함하는 더 넓은 애플리케이션 업데이트 사이클의 일부이기 때문에
애플리케이션 업데이트 인터페이스를 호출하여 직접 스테이지를 업데이트하지 않습니다.

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> sim_utils.update_stage()
```

### isaaclab.sim.utils.stage.save_stage(usd_path: [str](https://docs.python.org/3/library/stdtypes.html#str), save_and_reload_in_place: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

현재 스테이지의 루트 레이어 내용을 지정된 USD 파일에 저장합니다.

파일이 이미 존재하면 덮어씁니다.

* **매개변수:**
  * **usd_path** – 현재 스테이지를 저장할 파일 경로
  * **save_and_reload_in_place** – 저장된 USD 파일을 제자리에서 열지 여부. 기본값은 True.
* **반환값:**
  작업이 성공하면 True, 그렇지 않으면 False.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 경로가 USD에서 지원되지 않는 파일 유형일 때.
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 레이어 생성 또는 저장 작업 실패 시.

### isaaclab.sim.utils.stage.close_stage(callback_fn: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)[[[bool](https://docs.python.org/3/library/functions.html#bool), [str](https://docs.python.org/3/library/stdtypes.html#str)], [None](https://docs.python.org/3/library/constants.html#None)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

현재 USD 스테이지 닫기.

#### 참고
한 번 스테이지를 닫은 후에는 새 스테이지를 열거나 새로 생성해야 작업할 수 있습니다.

* **매개변수:**
  **callback_fn** – 스테이지 닫는 동안 호출할 콜백 함수.
  이 함수는 두 개의 인수를 받아야 합니다: 스테이지가 닫히고 있는지 여부를 나타내는 불리언 값
  다음은 한국어로 번역된 마크다운 문서이며, 코드 블록 내 주석만 번역하고 나머지는 구조를 유지했습니다.

와 에러 메시지를 나타내는 문자열을 반환합니다. 스테이지 닫기가 실패할 경우 사용됩니다. 기본값은 None이며, 이 경우 콜백 없이 스테이지가 닫힙니다.
* **반환값:**
  작업이 성공하면 True, 그렇지 않으면 False.

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> sim_utils.close_stage()
True
>>>
```

콜백 함수를 사용한 예시:
: ```pycon
  >>> import isaaclab.sim as sim_utils
  >>>
  >>> def callback(*args, **kwargs):
  ...     print("callback:", args, kwargs)
  >>> sim_utils.close_stage(callback)
  True
  >>> sim_utils.close_stage(callback)
  callback: (False, 'Stage opening or closing already in progress!!') {}
  False
  ```

### isaaclab.sim.utils.stage.clear_stage(predicate: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)[[pxr.Usd.Prim], [bool](https://docs.python.org/3/library/functions.html#bool)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [None](https://docs.python.org/3/library/constants.html#None)

스테이지의 모든 프리임을 삭제하면서 취소 명령 버퍼를 채우지 않습니다.

이 함수는 주어진 프리디케이트를 만족하는 스테이지의 모든 프리임을 삭제합니다. 프리디케이트가 None이면, 루트 프리임이 아니고, /Render 네임스페이스가 아니며, `no_delete` 메타데이터를 가지지 않으며, 다른 프리임의 조상이 아니며, 스테이지 창에서 숨겨지지 않은 모든 프리임을 삭제하는 기본 프리디케이트가 사용됩니다.

* **매개변수:**
  **predicate** – USD 프리임을 인자로 받아 해당 프리임을 삭제해야 하는지 여부를 나타내는 불리언 값을 반환하는 사용자 정의 함수입니다. 프리디케이트가 None이면, 모든 프리임을 삭제하는 기본 프리디케이트가 사용됩니다.

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> # 스테이지 전체 삭제
>>> sim_utils.clear_stage()
>>>
>>> # 예시: 스테이지에 /World/Cube, /World/Cube_01, /World/Cube_02가 존재할 때
>>> # Cube 타입인 프리임만 삭제
>>> predicate = lambda _prim: _prim.GetTypeName() == "Cube"
>>> sim_utils.clear_stage(predicate)  # 실행 후 스테이지는 /World만 남음
```

### isaaclab.sim.utils.stage.is_stage_loading() → [bool](https://docs.python.org/3/library/functions.html#bool)

파일이 로드 중인지 확인하는 편의 함수입니다.

* **반환값:**
  로드 중이면 True, 그렇지 않으면 False

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> sim_utils.is_stage_loading()
False
```

### isaaclab.sim.utils.stage.get_current_stage(fabric: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → pxr.Usd.Stage

현재 열려 있는 USD 또는 Fabric 스테이지를 반환합니다.

* **매개변수:**
  **fabric** – True이면 Fabric 스테이지를 반환하고, False이면 USD 스테이지를 반환합니다. 기본값은 False입니다.
* **반환값:**
  입력된 fabric 인자에 따라 지정된 USD 또는 Fabric 스테이지

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> sim_utils.get_current_stage()
Usd.Stage.Open(rootLayer=Sdf.Find('anon:0x7fba6c04f840:World7.usd'),
               sessionLayer=Sdf.Find('anon:0x7fba6c01c5c0:World7-session.usda'),
               pathResolverContext=<invalid repr>)
```

### isaaclab.sim.utils.stage.get_current_stage_id() → [int](https://docs.python.org/3/library/functions.html#int)

현재 열린 스테이지 ID를 반환합니다.

* **반환값:**
  현재 열린 스테이지 ID

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> sim_utils.get_current_stage_id()
1234567890
```

### isaaclab.sim.utils.stage.attach_stage_to_usd_context(attaching_early: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

현재 메모리上的 USD 스테이지를 USD 컨텍스트에 연결합니다.

이 함수는 씬이 생성된 동안 또는 이후에 호출되어야 하며, 스테이지가 시뮬레이션 또는 렌더링되기 전에 호출해야 합니다. 스테이지가 메모리에 없거나 렌더링이 비활성화되어 있으면, 이 함수는 연결 없이 반환됩니다.

#### 버전 추가됨
버전 2.3.0에서 새로 추가됨: 이 함수는 Isaac Sim 5.0 이상에서 사용 가능합니다. 하위 호환성을 위해, USD 컨텍스트에 연결하지 않고 반환됩니다.

* **매개변수:**
  **attaching_early** – 스테이지가 생성되기 전에 USD 컨텍스트에 연결할지 여부를 나타냅니다. 기본값은 False입니다.

## 쿼리

USD 스테이지를 쿼리하는 유틸리티입니다.

**함수:**

| [`get_next_free_prim_path`](#isaaclab.sim.utils.queries.get_next_free_prim_path)(path[, stage])                    | 주어진 기본 경스테이지에 존재하지 않는 새로운 프리임 경로를 반환합니다.                                   |
|--------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| [`get_first_matching_ancestor_prim`](#isaaclab.sim.utils.queries.get_first_matching_ancestor_prim)(prim_path, ...) | 주어진 조건 함수를 만족하는 첫 번째 조상 프리임을 반환합니다.                                          |
| [`get_first_matching_child_prim`](#isaaclab.sim.utils.queries.get_first_matching_child_prim)(prim_path, ...)       | 주어진 경로 문자열에서 조건 함수를 만족하는 첫 번째 USD 프리임을 재귀적으로 검색하여 반환합니다.                 |
| [`get_all_matching_child_prims`](#isaaclab.sim.utils.queries.get_all_matching_child_prims)(prim_path[, ...])       | 루트부터 탐색하여 조건 함수를 만족하는 모든 프리임을 반환합니다.                |
| [`find_first_matching_prim`](#isaaclab.sim.utils.queries.find_first_matching_prim)(prim_path_regex[, ...])         | 입력된 정규 표현식에 일치하는 첫 번째 프리임을 스테이지에서 찾습니다.                                |
| [`find_matching_prims`](#isaaclab.sim.utils.queries.find_matching_prims)(prim_path_regex[, stage])                 | 입력된 정규 표현식에 일치하는 모든 프리임을 스테이지에서 찾습니다.                                 |
| [`find_matching_prim_paths`](#isaaclab.sim.utils.queries.find_matching_prim_paths)(prim_path_regex[, ...])         | 입력된 정규 표현식에 일치하는 모든 프리임 경로를 스테이지에서 찾습니다.                            |
| [`find_global_fixed_joint_prim`](#isaaclab.sim.utils.queries.find_global_fixed_joint_prim)(prim_path[, ...])       | 지정된 프리임 경로 아래에서 시뮬레이션 세계와 연결된 고정 조인트 프리임을 찾습니다. |

### isaaclab.sim.utils.queries.get_next_free_prim_path(path: [str](https://docs.python.org/3/library/stdtypes.html#str), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [str](https://docs.python.org/3/library/stdtypes.html#str)

주어진 기본 경로에 대해 스테이지에 존재하지 않는 새로운 프리임 경로를 반환합니다.

주어진 경로가 이미 스테이지에 존재하지 않으면, 그 경로를 그대로 반환합니다. 그렇지 않으면, 주어진 경로에 증가하는 숫자를 접미사로 붙여 새로운 경로를 생성합니다.

* **매개변수:**
  * **path** – 확인할 기본 프리임 경로.
  * **stage** – 확인할 스테이지. 기본값은 현재 스테이지입니다.
* **반환값:**
  현재 스테이지에 존재하지 않는 새로운 경로

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> # 스테이지에 /World/Cube, /World/Cube_01이 존재할 때
>>> # /World/Cube에 대한 다음 사용 가능한 경로를 얻음
>>> sim_utils.get_next_free_prim_path("/World/Cube")
/World/Cube_02
```

### isaaclab.sim.utils.queries.get_first_matching_ancestor_prim(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, predicate: Callable[[Usd.Prim], [bool](https://docs.python.org/3/library/functions.html#bool)], stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → Usd.Prim | [None](https://docs.python.org/3/library/constants.html#None)

주어진 조건 함수를 만족하는 첫 번째 조상 프리임을 반환합니다.

이 함수는 대상 프리임부터 계층 구조를 따라 올라가면서 조건 함수를 만족하는 첫 번째 조상 프리임을 반환합니다. 이 과정에는 대상 프리임 자체가 조건을 만족하는 경우도 포함됩니다.

* **매개변수:**
  * **prim_path** – 스테이지 내 프리임의 경로.
  * **predicate** – 프리임을 입력으로 받아 해당 프리임이 조건을 만족하는지 여부를 나타내는 불리언 값을 반환하는 함수.
  * **stage** – 프리임이 존재하는 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **반환값:**
  조건 함수를 만족하는 첫 번째 조상 프리임. 그러한 조상 프리임이 없으면 None을 반환합니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프리임 경로가 전역이 아닌 경우(즉, ‘/’로 시작하지 않는 경우).

### isaaclab.sim.utils.queries.get_first_matching_child_prim(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, predicate: Callable[[Usd.Prim], [bool](https://docs.python.org/3/library/functions.html#bool)], stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None, traverse_instance_prims: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → Usd.Prim | [None](https://docs.python.org/3/library/constants.html#None)

주어진 경로 문자열에서 조건 함수를 만족하는 첫 번째 USD 프리임을 재귀적으로 검색하여 반환합니다.

이 함수는 `prim_path`부터 프리임 계층 구조를 깊이 우선 탐색하며, 주어진 `predicate` 조건을 만족하는 첫 번째 프리임을 반환합니다. 인스턴스 프리임을 탐색하는 옵션을 지원하며, 이는 표준 USD 탐색에서는 일반적으로 건너뛰어집니다.

USD 인스턴스 프리임은 프로토타입 씬 구조의 가벼운 복사본이며, 기본 탐색에서는 포함되지 않습니다. 이 함수는 `traverse_instance_prims`가 `True`로 설정될 때 인스턴스 프리임으로의 탐색을 허용합니다.

#### 버전 변경됨
버전 2.3.0에서 변경됨: `traverse_instance_prims` 매개변수가 추가되어 인스턴스 프리임 탐색 여부를 제어할 수 있습니다. 기본값은 이제 `True`로, 인스턴스 프리임을 탐색합니다.

* **매개변수:**
  * **prim_path** – 스테이지 내 프리임의 경로.
  * **predicate** – 프리임을 입력으로 받아 해당 프리임이 조건을 만족하는지 여부를 나타내는 불리언 값을 반환하는 함수.
  * **stage** – 프라임이 존재하는 단계. 기본값은 None이며, 이 경우 현재 단계가 사용됩니다.
  * **traverse_instance_prims** – 인스턴스 프라임을 탐색할지 여부. 기본값은 True입니다.
* **반환값:**
  조건을 만족하는 첫 번째 프라임. 조건을 만족하는 프라임이 없으면 None을 반환합니다.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프라임 경로가 글로벌이 아닌 경우 (즉: ‘/’로 시작하지 않을 때).

### isaaclab.sim.utils.queries.get_all_matching_child_prims(prim_path: str | Sdf.Path, predicate: Callable[[Usd.Prim], bool] = <function <lambda>>, depth: int | None = None, stage: Usd.Stage | None = None, traverse_instance_prims: bool = True) → [list](https://docs.python.org/3/library/stdtypes.html#list)[Usd.Prim]

루트부터 시작하여 조건에 맞는 모든 프라임을 검색하여 반환합니다.

이 함수는 `prim_path`에서 시작하여 프라임 계층 구조를 깊이 우선 탐색하고, 제공된 `predicate`를 만족하는 모든 프라임을 반환합니다. 인스턴스 프라임의 탐색도 선택적으로 지원하며, 표준 USD 탐색에서는 일반적으로 건너뛰는 인스턴스 프라임을 처리할 수 있습니다.

USD 인스턴스 프라임은 프로토타입 씬 구조의 경량 복사본이며, 명시적으로 처리되지 않는 한 기본 탐색에서 제외됩니다. `traverse_instance_prims`를 `True`로 설정하면 인스턴스 프라임도 탐색할 수 있습니다.

#### Versionchanged
버전 2.3.0에서 변경됨: 인스턴스 프라임 탐색 여부를 제어하는 `traverse_instance_prims`가 추가되었습니다. 기본값으로 인스턴스 프라임이 이제 탐색됩니다.

* **매개변수:**
  * **prim_path** – 검색을 시작할 루트 프라임 경로.
  * **predicate** – 프라임이 원하는 조건에 해당하는지 확인하는 조건자. 프라임을 입력으로 받아 불리언 값을 반환합니다. 기본값은 항상 True를 반환하는 함수입니다.
  * **depth** – 탐색의 최대 깊이. 지정된 경우 0보다 커야 합니다. 기본값은 None (즉: 트리의 끝까지 탐색).
  * **stage** – 프라임이 존재하는 단계. 기본값은 None이며, 이 경우 현재 단계가 사용됩니다.
  * **traverse_instance_prims** – 인스턴스 프라임을 탐색할지 여부. 기본값은 True입니다.
* **반환값:**
  조건을 만족하는 모든 프라임을 포함하는 리스트.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프라임 경로가 글로벌이 아닌 경우 (즉: ‘/’로 시작하지 않을 때).

### isaaclab.sim.utils.queries.find_first_matching_prim(prim_path_regex: [str](https://docs.python.org/3/library/stdtypes.html#str), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → Usd.Prim | [None](https://docs.python.org/3/library/constants.html#None)

입력된 정규 표현식을 기반으로 단계에서 첫 번째 일치하는 프라임을 찾습니다.

* **매개변수:**
  * **prim_path_regex** – 프라임 경로에 대한 정규 표현식.
  * **stage** – 프라임이 존재하는 단계. 기본값은 None이며, 이 경우 현재 단계가 사용됩니다.
* **반환값:**
  입력된 표현과 일치하는 첫 번째 프라임. 일치하는 프라임이 없으면 None을 반환합니다.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프라임 경로가 글로벌이 아닌 경우 (즉: ‘/’로 시작하지 않을 때).

### isaaclab.sim.utils.queries.find_matching_prims(prim_path_regex: [str](https://docs.python.org/3/library/stdtypes.html#str), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [list](https://docs.python.org/3/library/stdtypes.html#list)[Usd.Prim]

입력된 정규 표현식을 기반으로 단계에서 모든 일치하는 프라임을 찾습니다.

* **매개변수:**
  * **prim_path_regex** – 프라임 경로에 대한 정규 표현식.
  * **stage** – 프라임이 존재하는 단계. 기본값은 None이며, 이 경우 현재 단계가 사용됩니다.
* **반환값:**
  입력된 표현과 일치하는 프라임들의 리스트.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프라임 경로가 글로벌이 아닌 경우 (즉: ‘/’로 시작하지 않을 때).

### isaaclab.sim.utils.queries.find_matching_prim_paths(prim_path_regex: [str](https://docs.python.org/3/library/stdtypes.html#str), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

입력된 정규 표현식을 기반으로 단계에서 모든 일치하는 프라임 경로를 찾습니다.

* **매개변수:**
  * **prim_path_regex** – 프라임 경로에 대한 정규 표현식.
  * **stage** – 프라임이 존재하는 단계. 기본값은 None이며, 이 경우 현재 단계가 사용됩니다.
* **반환값:**
  입력된 표현과 일치하는 프라임 경로들의 리스트.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프라임 경로가 글로벌이 아닌 경우 (즉: ‘/’로 시작하지 않을 때).

### isaaclab.sim.utils.queries.find_global_fixed_joint_prim(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, check_enabled_only: [bool](https://docs.python.org/3/library/functions.html#bool) = False, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → UsdPhysics.Joint | [None](https://docs.python.org/3/library/constants.html#None)

지정된 프라임 경로 아래에서 대상과 시뮬레이션 세계를 연결하는 고정 조인트 프라임을 찾습니다.

조인트는 두 물체를 연결하는 요소입니다. 고정 조인트는 두 물체 간의 상대 운동을 허용하지 않는 조인트입니다. 고정 조인트가 하나의 대상 물체만 가지고 있다면, 해당 물체가 시뮬레이션 세계에 연결된 것으로 간주됩니다.

이 함수는 지정된 프라임 경로 아래에서 대상 물체가 하나만 있는 고정 조인트 프라임을 찾습니다. 그러한 고정 조인트 프라임이 존재하지 않으면 None을 반환합니다.

* **매개변수:**
  * **prim_path** – 고정 조인트 프라임을 찾을 프라임 경로.
  * **check_enabled_only** – 활성화된 고정 조인트만 고려할지 여부. 기본값은 False입니다. False인 경우, 활성화 여부와 관계없이 모든 조인트를 고려합니다.
  * **stage** – 프라임이 존재하는 단계. 기본값은 None이며, 이 경우 현재 단계가 사용됩니다.
* **반환값:**
  대상 물체가 하나인 고정 조인트 프라임. 그러한 프라임이 없으면 None을 반환합니다.
* **예외 발생:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프라임 경로가 글로벌이 아닌 경우 (즉: ‘/’로 시작하지 않을 때).
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프라임이 단계에 존재하지 않는 경우.

## 프라임

USD 프라임 생성 및 조작 유틸리티.

**함수:**

| [`create_prim`](#isaaclab.sim.utils.prims.create_prim)(prim_path[, prim_type, ...])                               | 제공된 USD 단계에 프라임을 생성합니다.                                       |
|-------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| [`delete_prim`](#isaaclab.sim.utils.prims.delete_prim)(prim_path[, stage])                                        | 가능한 경우 USD 프라임과 그 자손들을 씬에서 제거합니다.                |
| [`move_prim`](#isaaclab.sim.utils.prims.move_prim)(path_from, path_to[, ...])                                     | USD 단계 내에서 프라임을 한 경로에서 다른 경로로 이동시킵니다.                       |
| [`make_uninstanceable`](#isaaclab.sim.utils.prims.make_uninstanceable)(prim_path[, stage])                        | 프라임과 그 자손들이 인스턴스화되어 있는지 확인하고, 인스턴스화를 해제합니다. |
| [`set_prim_visibility`](#isaaclab.sim.utils.prims.set_prim_visibility)(prim, visible)                             | 열린 단계에서 프라임의 가시성을 설정합니다.                            |
| [`safe_set_attribute_on_usd_schema`](#isaaclab.sim.utils.prims.safe_set_attribute_on_usd_schema)(schema_api, ...) | 해당 USD 스키마에 속성이 존재하면 그 값을 설정합니다.                   |
| [`safe_set_attribute_on_usd_prim`](#isaaclab.sim.utils.prims.safe_set_attribute_on_usd_prim)(prim, ...)           | USD 프라임에 속성의 값을 설정합니다.                                   |
| [`change_prim_property`](#isaaclab.sim.utils.prims.change_prim_property)(prop_path, value[, ...])                 | USD 프라임에서 속성 값을 변경하거나 생성합니다.                                |
| [`export_prim_to_file`](#isaaclab.sim.utils.prims.export_prim_to_file)(path, source_prim_path)                    | 주어진 단계에서 프라임을 USD 파일로 내보냅니다.                                |
| [`apply_nested`](#isaaclab.sim.utils.prims.apply_nested)(func)                                                    | 지정된 프라임 경로 아래의 모든 프라임에 함수를 적용하는 데코레이터.         |
| [`clone`](#isaaclab.sim.utils.prims.clone)(func)                                                                  | 프라임의 부모와 일치하는 프라임 경로를 기반으로 프라임을 복제하는 데코레이터. |
| [`bind_visual_material`](#isaaclab.sim.utils.prims.bind_visual_material)(prim_path, material_path)                | 프라임에 시각적 재질을 바인딩합니다.                                               |
| [`bind_physics_material`](#isaaclab.sim.utils.prims.bind_physics_material)(prim_path, material_path)              | 프라임에 물리 재질을 바인딩합니다.                                              |
| [`add_usd_reference`](#isaaclab.sim.utils.prims.add_usd_reference)(prim_path, usd_path[, ...])                    | 제공된 단계에서 지정된 프라임 경로에 USD 참조를 추가합니다.          |
| [`get_usd_references`](#isaaclab.sim.utils.prims.get_usd_references)(prim_path[, stage])                          | 제공된 단계에서 지정된 프라임 경로의 USD 참조를 가져옵니다.       |
| [`select_usd_variants`](#isaaclab.sim.utils.prims.select_usd_variants)(prim_path, variants[, stage])              | 지정된 variant 세트에서 USD prim의 variant 선택을 설정합니다.      |

### isaaclab.sim.utils.prims.logger *= <Logger isaaclab.sim.utils.prims (WARNING)>*

일반 유틸

### isaaclab.sim.utils.prims.create_prim(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), prim_type: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'Xform', position: Any | [None](https://docs.python.org/3/library/constants.html#None) = None, translation: Any | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: Any | [None](https://docs.python.org/3/library/constants.html#None) = None, scale: Any | [None](https://docs.python.org/3/library/constants.html#None) = None, usd_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, semantic_label: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, semantic_type: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'class', attributes: [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [None](https://docs.python.org/3/library/constants.html#None) = None, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → Usd.Prim

제공된 USD 스테이지에 프림을 생성합니다.

이 메서드는 지정된 변환, 시맨틱 라벨을 적용하고 지정된 속성을 설정합니다.
변환은 세계 공간(`position` 사용) 또는 로컬 공간(`translation` 사용)에서 지정할 수 있습니다.

함수는 제공된 인수를 기반으로 변환의 좌표 시스템을 결정합니다.

* `position`이 제공된 경우, 방향도 세계 프레임에 있다고 가정합니다.
* `translation`이 제공된 경우, 방향도 로컬 프레임에 있다고 가정합니다.

스케일은 항상 로컬 프레임에서 적용됩니다.

함수는 다양한 시퀀스 유형(리스트, 튜플, numpy 배열, 토치 텐서)을 처리하여 프림 작업에 적합한 타입의 튜플로 변환합니다.

#### 주의
변환 연산은 USD 규칙에 따라 표준화됩니다: 번역, 회전(쿼터니언), 스케일의 순서입니다. `standardize_xform_ops()`에 대한 자세한 내용은 참고하십시오.

* **매개변수:**
  * **prim_path** – 새로운 프림의 경로.
  * **prim_type** – 프림 유형 이름. 기본값은 “Xform”이며, 이 경우 간단한 Xform 프림이 생성됩니다.
  * **position** – 월드 공간에서의 프림 위치 (x, y, z)입니다. 프림에 부모가 있는 경우, 이는 부모를 기준으로 로컬 공간으로 자동 변환됩니다. `translation`과 함께 사용할 수 없습니다. 기본값은 None이며, 이 경우 위치가 적용되지 않습니다.
  * **translation** – 로컬 공간에서의 프림 변환 (x, y, z)입니다. 좌표 변환 없이 직접 적용됩니다. `position`과 함께 사용할 수 없습니다. 기본값은 None이며, 이 경우 변환이 적용되지 않습니다.
  * **orientation** – 프림의 회전으로, 쿼터니언 (w, x, y, z)로 표현됩니다. `position`과 함께 사용될 때, 방향은 세계 공간에서 로컬 공간으로도 변환됩니다. `translation`과 함께 사용될 때, 로컬 방향으로 직접 적용됩니다. 기본값은 None입니다.
  * **scale** – x, y, z 방향의 스케일 인수. 로컬 공간에서 적용됩니다. 기본값은 None이며, 이 경우 균일 스케일 1.0이 적용됩니다.
  * **usd_path** – 이 프림이 참조할 USD 파일의 경로. 기본값은 None입니다.
  * **semantic_label** – 프림에 적용할 시맨틱 라벨. 기본값은 None이며, 이 경우 라벨이 추가되지 않습니다.
  * **semantic_type** – 라벨의 시맨틱 유형. 기본값은 “class”입니다.
  * **attributes** – 설정할 프림 속성의 키-값 쌍. 기본값은 None이며, 이 경우 속성이 설정되지 않습니다.
  * **stage** – 프림을 생성할 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **반환값:**
  생성된 USD 프림.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 프림 경로에 이미 프림이 있는 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `position`과 `translation`이 모두 제공된 경우.

### 예제

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> # 월드 위치 (1.0, 0.5, 0.0)에 큐브 생성
>>> sim_utils.create_prim(
...     prim_path="/World/Parent/Cube",
...     prim_type="Cube",
...     position=(1.0, 0.5, 0.0),
...     attributes={"size": 2.0},
... )
Usd.Prim(</World/Parent/Cube>)
>>>
>>> # 부모 기준 로컬 변환으로 구 생성
>>> sim_utils.create_prim(
...     prim_path="/World/Parent/Sphere",
...     prim_type="Sphere",
...     translation=(0.5, 0.0, 0.0),
...     scale=(2.0, 2.0, 2.0),
... )
Usd.Prim(</World/Parent/Sphere>)
```

### isaaclab.sim.utils.prims.delete_prim(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sequence[[str](https://docs.python.org/3/library/stdtypes.html#str)], stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

가능한 경우 USD 프림과 그 자손을 씬에서 제거합니다.

* **매개변수:**
  * **prim_path** – 삭제할 프림의 경로. 경로 목록이 제공된 경우, 함수는 목록의 모든 프림을 삭제합니다.
  * **stage** – 프림을 삭제할 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **반환값:**
  프림 또는 프림이 성공적으로 삭제된 경우 True, 그렇지 않으면 False.

### 예제

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> sim_utils.delete_prim("/World/Cube")
```

### isaaclab.sim.utils.prims.move_prim(path_from: [str](https://docs.python.org/3/library/stdtypes.html#str), path_to: [str](https://docs.python.org/3/library/stdtypes.html#str), keep_world_transform: [bool](https://docs.python.org/3/library/functions.html#bool) = True, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

USD 스테이지 내에서 한 경로에서 다른 경로로 프림을 이동시킵니다.

이 함수는 프림을 소스 경로에서 대상 경로로 이동시킵니다. `keep_world_transform`이 True로 설정된 경우, 프림의 월드 트랜스폼이 유지됩니다. 이는 프림의 로컬 트랜스폼이 재설정되어 프림의 월드 트랜스폼이 소스 경로의 월드 트랜스폼과 동일함을 의미합니다. False로 설정된 경우, 프림의 로컬 트랜스폼이 보존됩니다.

#### 경고
USD에서 프림의 재부모 지정 또는 이동은 비용이 많이 드는 작업으로, 특히 대규모이거나 깊이 쌓인 스테이지에서는 상당한 재구성 비용을 유발할 수 있습니다.

* **매개변수:**
  * **path_from** – 이동시킬 USD 프림의 경로
  * **path_to** – 프림의 최종 목적지
  * **keep_world_transform** – 프림의 월드 트랜스폼을 유지할지 여부. 기본값은 True입니다.
  * **stage** – 프림을 이동시킬 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **반환값:**
  프림이 성공적으로 이동된 경우 True, 그렇지 않으면 False.

### 예제

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> # 단계: /World/Cube 가 주어졌을 때, 프림 Cube를 프림 World 외부 이동
>>> sim_utils.move_prim("/World/Cube", "/Cube")
```

### isaaclab.sim.utils.prims.make_uninstanceable(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

지정된 프림 경로의 프림과 그 자손이 인스턴싱되었는지 확인하고 인스턴싱을 해제합니다.

이 함수는 지정된 프림 경로의 프림과 그 자손이 인스턴싱되었는지 확인합니다. 인스턴싱된 경우, 프림에서 인스턴싱을 비활성화하여 해당 프림을 인스턴싱되지 않게 만듭니다.

이 기능은 인스턴싱된 프림의 속성을 수정하고자 할 때 유용합니다. 예를 들어, 인스턴싱된 프림에 다른 재질을 적용하려면 먼저 프림을 인스턴싱되지 않게 만들어야 합니다.

* **매개변수:**
  * **prim_path** – 확인할 프림 경로.
  * **stage** – 프림이 존재하는 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림 경로가 전역이 아닌 경우 (즉, ‘/’로 시작하지 않는 경우).

### isaaclab.sim.utils.prims.set_prim_visibility(prim: pxr.Usd.Prim, visible: [bool](https://docs.python.org/3/library/functions.html#bool)) → [None](https://docs.python.org/3/library/constants.html#None)

열려 있는 스테이지에서 프림의 가시성을 설정합니다.

#### 주의
이 메서드는 USD API를 통해 이를 수행합니다.

* **매개변수:**
  * **prim** – USD 프림
  * **visible** – 스테이지에서 USD 프림의 가시성을 설정하는 플래그.

### 예제

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> # 단계: /World/Cube 가 주어졌을 때, 큐브를 보이지 않게 만듦
>>> prim = sim_utils.get_prim_at_path("/World/Cube")
>>> sim_utils.set_prim_visibility(prim, False)
```

### isaaclab.sim.utils.prims.safe_set_attribute_on_usd_schema(schema_api: pxr.Usd.APISchemaBase, name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), camel_case: [bool](https://docs.python.org/3/library/functions.html#bool))

USD 스키마에 존재하면 해당 속성의 값을 설정합니다.

USD API 스키마는 속성을 작성하고 추출하기 위한 인터페이스 또는 API 역할을 합니다. 일반적으로 `pxr.Usd.SchemaBase` 클래스에서 파생됩니다. 이 함수는 속성이 스키마에 존재하는지 확인하고, 존재한다면 해당 속성의 값을 설정합니다.

* **매개변수:**
  * **schema_api** – 속성을 설정할 USD 스키마.
  * **name** – 속성의 이름.
  * **value** – 속성에 설정할 값.
  * **camel_case** – 속성 이름을 카멜 케이스로 변환할지 여부.
* **예외:**
  [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – 입력된 속성 이름이 제공된 스키마 API에 존재하지 않을 때 발생합니다.

### isaaclab.sim.utils.prims.safe_set_attribute_on_usd_prim(prim: pxr.Usd.Prim, attr_name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), camel_case: [bool](https://docs.python.org/3/library/functions.html#bool))

USD 프림에 속성의 값을 설정합니다.

프림에 속성이 존재하지 않을 경우 새 속성을 생성합니다. 이는 쉐이더와 같은 경우, 속성이 USD 프림 속성으로 노출되지 않아 수정할 수 없기 때문입니다. 이 함수를 사용하면 이러한 경우에도 속성 값을 설정할 수 있습니다.

* **Parameters:**
  * **prim** – 속성을 설정할 USD 프림.
  * **attr_name** – 속성의 이름.
  * **value** – 속성에 설정할 값.
  * **camel_case** – 속성 이름을 카멜 케이스로 변환할지 여부.

### isaaclab.sim.utils.prims.change_prim_property(prop_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, value: Any, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None, type_to_create_if_not_exist: Sdf.ValueTypeNames | [None](https://docs.python.org/3/library/constants.html#None) = None, is_custom: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [bool](https://docs.python.org/3/library/functions.html#bool)

USD 프림의 속성 값을 변경하거나 생성합니다.

현재 편집 타겟과 함께 작동하는 간단한 속성 설정자입니다. 복잡한 레이어 관리가 필요한 경우, `omni.kit.commands.ChangePropertyCommand`를 사용하세요.

기본적으로, 속성이 존재할 경우 그 값을 변경합니다. 속성이 존재하지 않는 경우, `type_to_create_if_not_exist`를 제공하여 생성해야 합니다.

#### NOTE
속성 `value`는 해당 속성에 맞는 올바른 타입이어야 합니다.
예를 들어, 속성이 float이면 값도 float이어야 합니다.
RGB 색상이어야 하는 경우, 값은 `Gf.Vec3f` 타입이어야 합니다.

* **Parameters:**
  * **prop_path** – `/World/Prim.propertyName` 형식의 속성 경로.
  * **value** – 설정할 값. None이면 속성 값은 기본값으로 이동합니다.
    속성에 기본값이 없으면 silenz하게 no-op이 됩니다.
  * **stage** – USD 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
  * **type_to_create_if_not_exist** – None이 아니고 속성이 존재하지 않을 경우, 지정된 타입과 값으로 새 속성을 생성합니다. 기본값은 None입니다.
  * **is_custom** – 속성이 생성된 경우, 스키마에 속하지 않은 커스텀 속성인지 여부를 지정합니다. 기본값은 False입니다.
* **Returns:**
  속성이 성공적으로 변경되었으면 True, 그렇지 않으면 False.
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로에 프림이 존재하지 않을 때 발생합니다.

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>> from pxr import Sdf
>>>
>>> # 기존 속성 변경
>>> sim_utils.change_prim_property(prop_path="/World/Cube.size", value=2.0)
True
>>>
>>> # 새 커스텀 속성 생성
>>> sim_utils.change_prim_property(
...     prop_path="/World/Cube.customValue",
...     value=42,
...     type_to_create_if_not_exist=Sdf.ValueTypeNames.Int,
...     is_custom=True,
... )
True
```

### isaaclab.sim.utils.prims.export_prim_to_file(path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, source_prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, target_prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path | [None](https://docs.python.org/3/library/constants.html#None) = None, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

주어진 스테이지에서 프림을 USD 파일로 내보냅니다.

함수는 제공된 경로에 새로운 레이어를 생성하고 프림을 해당 레이어로 복사합니다. 복사된 프림을 대상 레이어의 기본 프림으로 설정합니다. 또한, 현재 스테이지의 업-축과 미터당 단위를 일치시킵니다.

* **Parameters:**
  * **path** – 프림을 내보낼 파일 경로.
  * **source_prim_path** – 내보낼 프림 경로.
  * **target_prim_path** – 대상 레이어에서 기본 프림으로 설정할 프림 경로. 기본값은 None이며, 이 경우 소스 프림 경로가 사용됩니다.
  * **stage** – 프림이 존재하는 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림 경로가 전역이 아닌 경우(즉, '/'로 시작하지 않을 때) 발생합니다.

### isaaclab.sim.utils.prims.apply_nested(func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)) → [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)

지정된 프림 경로 하위의 모든 프림에 함수를 적용하는 데코레이터.

함수는 제공된 프림 경로와 모든 하위 프림을 반복하여 지정된 경로 하위의 모든 프림에 입력 함수를 적용합니다.

함수가 특정 프림에 성공적으로 적용되면, 그 프림의 하위 프림은 확인하지 않습니다. 이는 중첩된 스키마가 물리적으로 허용되지 않기 때문입니다. 예를 들어, 부모 프림과 자식 프림이 모두 rigid-body 스키마를 가질 수 없으며, 중첩된 아티큘레이션을 가질 수 없습니다.

지정된 프림 경로 하위의 프림을 순회할 때, 함수 적용에 실패한 프림이 있을 경우 경고를 발생시킵니다. 이는 사용자가 유효한 속성을 가지지 않은 프림에 함수를 적용하려고 했거나, 프림이 인스턴스화된 프림일 수 있기 때문입니다.

* **Parameters:**
  **func** – 지정된 프림 경로 하위의 모든 프림에 적용할 함수. 이 함수는 프림 경로와 기타 인수를 받아야 하며, 성공 여부를 나타내는 불리언 값을 반환해야 합니다.
* **Returns:**
  지정된 프림 경로 하위의 모든 프림에 함수를 적용하는 래핑된 함수.
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림 경로가 스테이지에 존재하지 않을 때 발생합니다.

### isaaclab.sim.utils.prims.clone(func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)) → [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)

프림의 부모 프림 경로와 일치하는 경로를 기반으로 프림을 클론하는 데코레이터.

데코레이터는 부모 프림 경로가 스테이지 내의 어떤 프림 경로와 일치하는지 확인합니다. 일치하는 경우, 각 일치하는 경로에 스폰된 프림을 클론합니다. 예를 들어, 입력 프림 경로가 `/World/Table_[0-9]/Bottle`인 경우, 데코레이터는 부모 프림 경로(`/World/Table_0/Bottle`, `/World/Table_1/Bottle` 등)와 일치하는 각 경소에 프림을 클론합니다.

#### NOTE
일치하는 프림 경로에 대해, 데코레이터는 모든 일치하는 경로에 유효한 프림이 존재한다고 가정합니다. 일치하는 경로가 없을 경우, 데코레이터는 `RuntimeError`를 발생시킵니다.

* **Parameters:**
  **func** – 데코레이트할 함수.
* **Returns:**
  프림을 스폰하고 일치하는 각 프림 경로에 클론하는 데코레이트된 함수. 일치하는 프림 경로 목록 중 첫 번째 프림인 스폰된 소스 프림을 반환합니다.

### isaaclab.sim.utils.prims.bind_visual_material(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, material_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None, stronger_than_descendants: [bool](https://docs.python.org/3/library/functions.html#bool) = True)

프림에 시각적 소재를 바인딩합니다.

이 함수는 USD 명령 [`BindMaterialCommand`](https://docs.omniverse.nvidia.com/kit/docs/omni.usd/latest/omni.usd.commands/omni.usd.commands.BindMaterialCommand.html)를 래핑한 것입니다.

#### NOTE
이 함수는 [`apply_nested()`](#isaaclab.sim.utils.prims.apply_nested)로 데코레이트되어 프림 경로와 그 모든 후손에 함수를 적용할 수 있습니다.

* **Parameters:**
  * **prim_path** – 소재를 적용할 프림 경로.
  * **material_path** – 적용할 소재의 프림 경로.
  * **stage** – 프림과 소재가 존재하는 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
  * **stronger_than_descendants** – 소재가 자손의 소재를 재정의할지 여부. 기본값은 True입니다.
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 프림 경로가 스테이지에 존재하지 않을 때 발생합니다.

### isaaclab.sim.utils.prims.bind_physics_material(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, material_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Sdf.Path, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None, stronger_than_descendants: [bool](https://docs.python.org/3/library/functions.html#bool) = True)

프림에 물리 소재를 바인딩합니다.

[물리 소재](https://isaac-sim.github.io/IsaacLab/main/source/api/lab/isaaclab.sim.html#isaaclab.sim.SimulationCfg.physics_material)는 물리 활성화가 된 프림에만 적용할 수 있습니다. 여기에는 충돌 API, 변형 가능한 본체 API, 또는 입자 시스템이 포함됩니다. 프림이 이러한 API 중 하나라도 가지고 있지 않은 경우, 함수는 소재를 적용하지 않고 False를 반환합니다.

#### NOTE
이 함수는 [`apply_nested()`](#isaaclab.sim.utils.prims.apply_nested)로 데코레이트되어 프림 경로와 그 모든 후손에 함수를 적용할 수 있습니다.

* **Parameters:**
  * **prim_path** – 소재를 적용할 프림 경로.
  * **material_path** – 적용할 소재의 프림 경로.
  * **stage** – 프림과 소재가 존재하는 스테이지. 기본값은 None이며, 이 경우 현재 스테이지가 사용됩니다.
  * **stronger_than_descendants** – 자손의 재질을 재정의해야 하는지 여부입니다.  
  기본값은 True입니다.  
* **Raises:**  
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 prim 경로가 스테이지에 존재하지 않는 경우.

### isaaclab.sim.utils.prims.add_usd_reference(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), usd_path: [str](https://docs.python.org/3/library/stdtypes.html#str), prim_type: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'Xform', stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → Usd.Prim

지정된 스테이지에서 특정 prim 경로에 USD 참조를 추가합니다.

이 함수는 제공된 스테이지에서 특정 prim 경로에 외부 USD 파일의 참조를 추가합니다.  
prim이 존재하지 않으면 지정된 타입으로 생성됩니다.

또한, 단계 단위 검증을 처리하여 호환성을 보장합니다. 예를 들어, 현재 단계가 미터 단위이고 참조된 USD 파일이 센티미터 단위인 경우, 단위를 일치시키기 위해 변환합니다.  
이는 `omni.metrics.assembler` 기능을 사용하여 수행됩니다.

* **Parameters:**  
  * **prim_path** – 참조를 연결할 prim 경로입니다.  
  * **usd_path** – 참조할 USD 파일의 경로입니다.  
  * **prim_type** – 존재하지 않을 경우 생성할 prim의 타입입니다. 기본값은 “Xform”입니다.  
  * **stage** – 참조를 추가할 스테이지입니다. 기본값은 None이며, 이 경우 현재 스테이지를 사용합니다.  
* **Returns:**  
  지정된 prim 경로의 USD prim입니다.  
* **Raises:**  
  [**FileNotFoundError**](https://docs.python.org/3/library/exceptions.html#FileNotFoundError) – 지정된 경로에서 입력 USD 파일을 찾을 수 없는 경우.

### isaaclab.sim.utils.prims.get_usd_references(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

제공된 스테이지에서 특정 prim 경로의 USD 참조를 가져옵니다.

* **Parameters:**  
  * **prim_path** – USD 참조를 가져올 prim 경로입니다.  
  * **stage** – USD 참조를 가져올 스테이지입니다. 기본값은 None이며, 이 경우 현재 스테이지를 사용합니다.  
* **Returns:**  
  USD 참조 경로 목록입니다.  
* **Raises:**  
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로의 prim이 유효하지 않은 경우.

### isaaclab.sim.utils.prims.select_usd_variants(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), variants: [object](https://docs.python.org/3/library/functions.html#object) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)], stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

USD prim에서 지정된 variant 세트의 variant 선택을 설정합니다.

[USD Variants](https://graphics.pixar.com/usd/docs/USD-Glossary.html#USDGlossary-Variant)는 USD 구성에서 매우 강력한 도구로, 단일 자산에 대해 다른 옵션을 가질 수 있도록 허용합니다.  
이는 세트의 variant 옵션에 따라 동일한 prim의 파라미터 변형을 수정하여 수행할 수 있습니다.  
이 함수는 USD prim에서 지정된 variant 세트의 variant 선택을 설정하기 위한 스크립트 기반 유틸리티 역할을 합니다.

이 함수는 variant 세트 이름을 variant 선택에 매핑하는 딕셔너리 또는 config 클래스를 취합니다. 예를 들어,  
`"/World/Table"`에 두 개의 variant 세트 “color”와 “size”가 있는 prim이 있는 경우, 다음처럼 variant 선택을 설정할 수 있습니다:

```python
select_usd_variants(
    prim_path="/World/Table",
    variants={
        "color": "red",
        "size": "large",
    },
)
```

또는 config 클래스를 사용하여 variant 선택을 정의할 수도 있습니다:

```python
@configclass
class TableVariants:
    color: Literal["blue", "red"] = "red"
    size: Literal["small", "large"] = "large"


select_usd_variants(
    prim_path="/World/Table",
    variants=TableVariants(),
)
```

* **Parameters:**  
  * **prim_path** – USD prim의 경로입니다.  
  * **variants** – variant 세트 이름을 variant 선택에 매핑하는 딕셔너리 또는 config 클래스입니다.  
  * **stage** – USD 스테이지입니다. 기본값은 None이며, 이 경우 현재 스테이지를 사용합니다.  
* **Raises:**  
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 경로의 prim이 유효하지 않은 경우.

## 변형

USD 변형 (xform) 작업을 위한 유틸리티입니다.

이 모듈은 prim에 대한 USD 변형 작업 (xform ops)을 조작하는 유틸리티를 제공합니다.  
USD의 변형 작업은 기하학이 3D 공간에서 어떻게 위치, 방향, 크기를 갖는지 정의합니다.

이 모듈의 유틸리티는 다양한 USD 자산 간에 변형 스택을 표준화하고, 작업을 지우며, 일관된 방식으로 변형을 조작하는 데 도움을 줍니다.

**Functions:**

| [`standardize_xform_ops`](#isaaclab.sim.utils.transforms.standardize_xform_ops)(prim[, translation, ...])   | USD prim의 변형 작업 스택을 정준형으로 표준화합니다.   |
|-------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`validate_standard_xform_ops`](#isaaclab.sim.utils.transforms.validate_standard_xform_ops)(prim)           | prim의 변형 작업이 표준화되었는지 검증합니다.               |
| [`resolve_prim_pose`](#isaaclab.sim.utils.transforms.resolve_prim_pose)(prim[, ref_prim])                   | 다른 prim을 기준으로 한 prim의 포즈를 해결합니다.                       |
| [`resolve_prim_scale`](#isaaclab.sim.utils.transforms.resolve_prim_scale)(prim)                             | 월드 프레임에서 prim의 크기를 해결합니다.                                |
| [`convert_world_pose_to_local`](#isaaclab.sim.utils.transforms.convert_world_pose_to_local)(position, ...)  | 기준 prim을 기준으로 월드 스페이스 포즈를 로컬 스페이스 포즈로 변환합니다.   |

### isaaclab.sim.utils.transforms.standardize_xform_ops(prim: pxr.Usd.Prim, translation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), ...] | [None](https://docs.python.org/3/library/constants.html#None) = None, orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), ...] | [None](https://docs.python.org/3/library/constants.html#None) = None, scale: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), ...] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [bool](https://docs.python.org/3/library/functions.html#bool)

USD prim의 변형 작업 스택을 표준 USD 변형 작업 순서인 [translate, orient, scale]의 정준형으로 표준화합니다.  
이 함수는 다음 작업을 수행합니다:

1. prim이 Xformable인지 검증합니다.
2. 현재 로컬 변형(변환, 회전, 스케일)을 캡처합니다.
3. 단위 규모 변환(xformOp:scale:unitsResolve)을 해결하고 베이킹합니다.
4. 표준 변형 작업(translate, orient, scale)을 생성하거나 재사용합니다.
5. 변형 작업 순서를 [translate, orient, scale]로 설정합니다.
6. 보존되거나 사용자가 지정한 변형 값을 적용합니다.

전체 수정 과정은 여러 prim을 처리할 때 성능을 최적화하기 위해 `Sdf.ChangeBlock` 내에서 수행됩니다.

#### NOTE
**표준 변형 순서:** 함수는 USD 모범 사례 순서를 강제합니다:  
`xformOp:translate`, `xformOp:orient`, `xformOp:scale`. 이 순서는 대부분의 USD 도구 및 워크플로와 호환되며,  
회전에 쿼터니언을 사용하여 짐벌 락(gimbal lock) 문제를 방지합니다.

#### NOTE
**포즈 보존:** 기본적으로 함수는 prim의 로컬 변형(부모 상대)을 보존합니다.  
명시적인 `translation`, `orientation`, 또는 `scale` 값이 제공되지 않는 한, prim의 월드 스페이스 위치는 변경되지 않습니다.

#### WARNING
**애니메이션 데이터 손실:** 이 함수는 기본 시간 코드(`Usd.TimeCode.Default()`)에서의 변형 값만 보존합니다.  
애니메이션 또는 시간 샘플링된 변형 데이터는 손실됩니다. 이 함수는 애니메이션된 prim이 아닌, 자산 가져오기 또는 준비 과정에서 사용해야 합니다.

#### WARNING
**단위 규모 해결:** prim에 `xformOp:scale:unitsResolve` 속성이 있는 경우 (단위 불일치가 있는 가져온 자산에서 일반적),  
이 속성은 스케일에 베이킹되고 제거됩니다. 예를 들어, 스케일이 (1, 1, 1)이고 unitsResolve가 (100, 100, 100)이면,  
최종 스케일은 (100, 100, 100)이 됩니다.

* **Parameters:**  
  * **prim** – 표준화할 USD prim입니다. UsdGeom.Xformable 스키마를 지원하는 유효한 prim이어야 합니다(예: Xform, Mesh, Cube 등).  
    Material 및 Shader prim은 Xformable이 아니며, 이 경우 False를 반환합니다.  
  * **translation** – 로컬 공간에서의 선택적 변환 벡터 (x, y, z). 제공되면 prim의 현재 변환을 대체합니다.  
    제공되지 않으면 현재 로컬 변환을 보존합니다. 기본값은 None입니다.  
  * **orientation** – 로컬 공간에서의 선택적 방향 쿼터니언 (w, x, y, z). 제공되면 prim의 현재 방향을 대체합니다.  
    제공되지 않으면 현재 로컬 방향을 보존합니다. 기본값은 None입니다.  
  * **scale** – 선택적 스케일 벡터 (x, y, z). 제공되면 prim의 현재 스케일을 대체합니다.  
    제공되지 않으면 단위 해상도 후 현재 스케일을 보존하거나, 스케일이 존재하지 않는 경우 (1, 1, 1)을 사용합니다.  
    기본값은 None입니다.  
* **Returns:**  
  변형 작업이 성공적으로 표준화된 경우 True, 그렇지 않은 경우 False를 반환합니다.  
  prim이 Xformable이 아닌 경우(Material, Shader prim 등) False를 반환하며, 이때 오류 메시지가 기록됩니다.  
* **Return type:**
  [bool](https://docs.python.org/3/library/functions.html#bool)
* **발생 오류:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림이 유효하지 않은 경우(존재하지 않거나 유효하지 않은 프림).

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> # 비표준 변환 작업을 표준화된 프림으로 변환
>>> prim = stage.GetPrimAtPath("/World/ImportedAsset")
>>> result = sim_utils.standardize_xform_ops(prim)
>>> if result:
...     print("Transform 스택이 성공적으로 표준화되었습니다")
>>> # 프림이 이제 [translate, orient, scale] 순서로 사용됨
>>>
>>> # 표준화 및 새 변환 값 설정
>>> sim_utils.standardize_xform_ops(
...     prim,
...     translation=(1.0, 2.0, 3.0),
...     orientation=(1.0, 0.0, 0.0, 0.0),  # 동일 회전 (w, x, y, z)
...     scale=(2.0, 2.0, 2.0),
... )
>>>
>>> # 성능을 위한 일괄 처리
>>> prims_to_standardize = [stage.GetPrimAtPath(p) for p in prim_paths]
>>> for prim in prims_to_standardize:
...     sim_utils.standardize_xform_ops(prim)  # 각 호출에서 Sdf.ChangeBlock 사용
```

### isaaclab.sim.utils.transforms.validate_standard_xform_ops(prim: pxr.Usd.Prim) → [bool](https://docs.python.org/3/library/functions.html#bool)

프림의 변환 작업이 표준화된 형태인지 검증합니다.

이 함수는 프림의 변환 작업이 정준형으로 표준화되었는지 확인합니다:
[translate, orient, scale].

* **매개변수:**
  **prim** – 검증할 USD 프림.

### isaaclab.sim.utils.transforms.resolve_prim_pose(prim: Usd.Prim, ref_prim: Usd.Prim | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]]

다른 프림을 기준으로 프림의 자세를 해결합니다.

#### 참고
이 함수는 최종 단계에서 변환 행렬을 정규직교화하여 스케일과 스큐를 무시합니다. 그러나 계층 구조의 특정 조상 프림에 비균일 스케일이 있는 경우, 해당 스케일은 여전히 프림의 결과 위치와 방향에 영향을 미칩니다(스케일 제거 전에 변환에 베이크되기 때문입니다).

즉, 스케일은 계층적으로 **제거되지 않습니다**. 완전히 스케일이 없는 자세가 필요하면, 변환 체인을 따라 이동하면서 각 수준에서 스케일을 제거해야 합니다. 이 기능이 필요한 경우 이슈를 생성해 주세요.

* **매개변수:**
  * **prim** – 자세를 해결할 USD 프림.
  * **ref_prim** – 자세를 계산할 기준이 되는 USD 프림.
    기본값은 None이며, 이 경우 월드 프레임이 사용됩니다.
* **반환값:**
  위치(3D 벡터)와 (w, x, y, z) 형식의 쿼터니언 방향을 포함하는 튜플.
* **발생 오류:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림 또는 참조 프림이 유효하지 않은 경우.

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>> from pxr import Usd, UsdGeom
>>>
>>> # 프림 가져오기
>>> stage = sim_utils.get_current_stage()
>>> prim = stage.GetPrimAtPath("/World/ImportedAsset")
>>>
>>> # 자세 해결
>>> pos, quat = sim_utils.resolve_prim_pose(prim)
>>> print(f"Position: {pos}")
>>> print(f"Orientation: {quat}")
>>>
>>> # 다른 프림을 기준으로 자세 해결
>>> ref_prim = stage.GetPrimAtPath("/World/Reference")
>>> pos, quat = sim_utils.resolve_prim_pose(prim, ref_prim)
>>> print(f"Position: {pos}")
>>> print(f"Orientation: {quat}")
```

### isaaclab.sim.utils.transforms.resolve_prim_scale(prim: pxr.Usd.Prim) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]

월드 프레임에서 프림의 스케일을 해결합니다.

속성 수준에서 USD 프림의 스케일은 부모 프림에 대한 스케일 변환을 적용한 것입니다. 이 함수는 프림의 로컬-월드 변환을 계산하여 월드 프레임에서의 프림 스케일을 해결합니다. 이는 프림 계층을 따라 올라가면서 프림들의 회전과 스케일을 고려하는 것과 동일합니다.

예를 들어, 프림의 스케일이 (1, 2, 3)이고 부모 프림의 스케일이 (4, 5, 6)이라면, 월드 프레임에서의 프림 스케일은 (4, 10, 18)입니다.

* **매개변수:**
  **prim** – 스케일을 해결할 USD 프림.
* **반환값:**
  월드 프레임에서 x, y, z 방향의 프림 스케일.
* **발생 오류:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림이 유효하지 않은 경우.

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>> from pxr import Usd, UsdGeom
>>>
>>> # 프림 가져오기
>>> stage = sim_utils.get_current_stage()
>>> prim = stage.GetPrimAtPath("/World/ImportedAsset")
>>>
>>> # 스케일 해결
>>> scale = sim_utils.resolve_prim_scale(prim)
>>> print(f"Scale: {scale}")
```

### isaaclab.sim.utils.transforms.convert_world_pose_to_local(position: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), ...], orientation: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), ...] | [None](https://docs.python.org/3/library/constants.html#None), ref_prim: pxr.Usd.Prim) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)]

월드 스페이스 포즈를 기준 프림을 기준으로 로컬 스페이스 포즈로 변환합니다.

이 함수는 월드 공간에서의 위치와 방향을 입력받아 주어진 기준 프림에 대한 로컬 공간으로 변환합니다. 이는 다른 프림에 대한 로컬 트랜스폼 속성을 설정해야 하는 경우, 원하는 월드 위치만을 아는 상황에서 유용합니다.

변환은 표준 USD 변환 수학을 사용합니다:
`local_transform = world_transform * inverse(ref_world_transform)`

#### 참고
참조 프림이 루트 프림("/")인 경우, 위치와 방향은 변경되지 않고 반환됩니다. 이는 이미 로컬/월드 공간에 효과적으로 존재하기 때문입니다.

* **매개변수:**
  * **position** – 월드 스페이스 위치로서 (x, y, z).
  * **orientation** – 월드 스페이스 방향으로서 쿼터니언 (w, x, y, z). None인 경우, 위치만 변환하고 방향에 대해 None을 반환합니다.
  * **ref_prim** – 로컬 트랜스폼을 기준으로 계산할 기준 USD 프림. 이 프림이 루트 프림("/")인 경우, 월드 포즈는 변경되지 않고 반환됩니다.
* **반환값:**
  - `local_translation`은 ref_prim을 기준으로 로컬 공간에서의 (x, y, z) 튜플
  - `local_orientation`은 ref_prim을 기준으로 로컬 공간에서의 (w, x, y, z) 튜플이며, 방향이 제공되지 않은 경우 None
* **반환 유형:**
  (local_translation, local_orientation) 형태의 튜플이며,
* **발생 오류:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 기준 프림이 유효한 USD 프림이 아닌 경우.

### 예시

```pycon
>>> import isaaclab.sim as sim_utils
>>> from pxr import Usd, UsdGeom
>>>
>>> # 기준 프림 가져오기
>>> stage = sim_utils.get_current_stage()
>>> ref_prim = stage.GetPrimAtPath("/World/Reference")
>>>
>>> # 월드 포즈를 로컬로 변환 (ref_prim 기준)
>>> world_pos = (10.0, 5.0, 0.0)
>>> world_quat = (1.0, 0.0, 0.0, 0.0)  # 동일 회전
>>> local_pos, local_quat = sim_utils.convert_world_pose_to_local(world_pos, world_quat, ref_prim)
>>> print(f"Local position: {local_pos}")
>>> print(f"Local orientation: {local_quat}")
```

## 의미론

USD 프림에 의미론적 라벨을 적용하고 제거하는 유틸리티.

**함수:**

| [`add_labels`](#isaaclab.sim.utils.semantics.add_labels)(prim, labels[, instance_name, ...])     | `UsdSemantics.LabelsAPI`를 사용하여 프림에 의미론적 라벨을 적용합니다.                                                       |
|--------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| [`get_labels`](#isaaclab.sim.utils.semantics.get_labels)(prim)                                   | 프림에 적용된 모든 의미론적 라벨(`UsdSemantics.LabelsAPI`)을 가져옵니다.                                                     |
| [`remove_labels`](#isaaclab.sim.utils.semantics.remove_labels)(prim[, instance_name, ...])       | 프림에서 의미론적 라벨(`UsdSemantics.LabelsAPI`)을 제거하고, 선택적으로 자손 프림에서도 제거합니다.                            |
| [`check_missing_labels`](#isaaclab.sim.utils.semantics.check_missing_labels)([prim_path, stage]) | 제공된 경로의 프림과 그 자손 프림에서 의미론적 라벨(`UsdSemantics.LabelsAPI`)이 누락되었는지 확인합니다. |
| [`count_total_labels`](#isaaclab.sim.utils.semantics.count_total_labels)([prim_path, stage])     | 제공된 경로에 있는 프리믹에 적용된 의미 라벨(`UsdSemantics.LabelsAPI`)의 개수를 세는 함수입니다.                |

### isaaclab.sim.utils.semantics.add_labels(prim: pxr.Usd.Prim, labels: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)], instance_name: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'class', overwrite: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [None](https://docs.python.org/3/library/constants.html#None)

`UsdSemantics.LabelsAPI`를 사용하여 프리믹에 의미 라벨을 적용합니다.

이 함수는 `omni.replicator.core.functional.modify.semantics()` 함수의 래퍼입니다.
`UsdSemantics.LabelsAPI`를 사용하여 프리믹에 라벨을 적용합니다.

#### Versionadded
버전 2.3.0부터 신규 추가: 이 함수는 Isaac Sim 5.0 이상에서 사용 가능하며, `UsdSemantics.LabelsAPI`를 도입했습니다.
이전 버전에서는 `UsdSemantics.SemanticsAPI`(지원 중단됨)를 대체로 사용합니다.

### Example

```pycon
>>> prim = sim_utils.create_prim("/World/Test/Sphere", "Sphere", stage=stage, attributes={"radius": 10.0})
>>> sim_utils.add_labels(prim, labels=["sphere"], instance_name="class")
```

* **Parameters:**
  * **prim** – 라벨을 추가하거나 업데이트할 USD 프리믹입니다.
  * **labels** – 적용할 라벨 리스트입니다.
  * **instance_name** – 의미 인스턴스의 이름입니다. 기본값은 “class”입니다.
  * **overwrite** – 이 인스턴스에 대한 기존 라벨을 덮어쓸지 여부입니다. False인 경우, 기존 라벨이 있다면 새로운 라벨을 기존 라벨에 추가합니다. 기본값은 True입니다.

### isaaclab.sim.utils.semantics.get_labels(prim: pxr.Usd.Prim) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

프리믹에 적용된 모든 의미 라벨(`UsdSemantics.LabelsAPI`)을 가져옵니다.

#### Versionadded
버전 2.3.0부터 신규 추가: 이 함수는 Isaac Sim 5.0 이상에서 사용 가능합니다. 이전 버전에서는 [`isaacsim.core.utils.semantics`](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/py/source/extensions/isaacsim.core.utils/docs/index.html#module-isaacsim.core.utils.semantics) 모듈을 대신 사용하십시오.

* **Parameters:**
  **prim** – 라벨을 반환할 USD 프리믹입니다.
* **Returns:**
  인스턴스 이름을 라벨 리스트에 매핑하는 딕셔너리입니다.
  라벨이 없는 경우 빈 딕셔너리를 반환합니다.

### isaaclab.sim.utils.semantics.remove_labels(prim: pxr.Usd.Prim, instance_name: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, include_descendants: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

프리믹과 선택적으로 그 후손에서 의미 라벨(`UsdSemantics.LabelsAPI`)을 제거합니다.

#### Versionadded
버전 2.3.0부터 신규 추가: 이 함수는 Isaac Sim 5.0 이상에서 사용 가능합니다. 이전 버전에서는 [`isaacsim.core.utils.semantics`](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/py/source/extensions/isaacsim.core.utils/docs/index.html#module-isaacsim.core.utils.semantics) 모듈을 대신 사용하십시오.

* **Parameters:**
  * **prim** – 라벨을 제거할 USD 프리믹입니다.
  * **instance_name** – 제거할 특정 인스턴스 이름입니다. 기본값은 None이며,この場合 모든 라벨을 제거합니다.
  * **include_descendants** – 자식 프리믹도 순회하여 재귀적으로 라벨을 제거할지 여부입니다. 기본값은 False입니다.

### isaaclab.sim.utils.semantics.check_missing_labels(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]

제공된 경로의 프리믹과 그 후손에 의미 라벨(`UsdSemantics.LabelsAPI`)이 누락되었는지 확인합니다.

#### NOTE
이 함수는 `UsdGeom.Gprim` 유형의 프리믹만 확인합니다.

#### Versionadded
버전 2.3.0부터 신규 추가: 이 함수는 Isaac Sim 5.0 이상에서 사용 가능합니다. 이전 버전에서는 [`isaacsim.core.utils.semantics`](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/py/source/extensions/isaacsim.core.utils/docs/index.html#module-isaacsim.core.utils.semantics) 모듈을 대신 사용하십시오.

* **Parameters:**
  * **prim_path** – 검색을 시작할 프리믹 경로입니다. None인 경우 전체 스테이지를 검사합니다.
  * **stage** – 검색을 시작할 스테이지입니다. None인 경우 현재 스테이지를 사용합니다.
* **Returns:**
  라벨이 적용되지 않은 프리믹 경로를 포함하는 리스트입니다.

### isaaclab.sim.utils.semantics.count_total_labels(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [int](https://docs.python.org/3/library/functions.html#int)]

제공된 경로의 프리믹에 적용된 의미 라벨(`UsdSemantics.LabelsAPI`)의 개수를 세는 함수입니다.

이 함수는 제공된 경로부터 모든 프리믹을 순회하며 각 라벨이 프리믹에 적용된 횟수를 세어 라벨과 해당 개수의 딕셔너리를 반환합니다.

#### Versionadded
버전 2.3.0부터 신규 추가: 이 함수는 Isaac Sim 5.0 이상에서 사용 가능합니다. 이전 버전에서는 [`isaacsim.core.utils.semantics`](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/py/source/extensions/isaacsim.core.utils/docs/index.html#module-isaacsim.core.utils.semantics) 모듈을 대신 사용하십시오.

* **Parameters:**
  * **prim_path** – 검색을 시작할 프리믹 경로입니다. None인 경우 전체 스테이지를 검사합니다.
  * **stage** – 검색을 시작할 스테이지입니다. None인 경우 현재 스테이지를 사용합니다.
* **Returns:**
  개별 라벨을 모든 인스턴스에서의 총 개수에 매핑하는 딕셔너리입니다.
  라벨이 없는 프리믹의 개수를 나타내는 ‘missing_labels’ 항목도 포함됩니다.

## Legacy

레거시 기능을 위한 유틸리티입니다.

이 서브모듈은 Isaac Lab에서 더 이상 필요하지 않은 Isaac Sim의 레거시 함수를 포함합니다. 대부분의 함수는 USD API의 간단한 래퍼이며, 주로 편의를 위해 제공됩니다.

가능한 경우 USD API를 직접 사용하는 것이 좋습니다.

**Functions:**

| [`add_reference_to_stage`](#isaaclab.sim.utils.legacy.add_reference_to_stage)(usd_path, path[, ...])   | 지정된 프리믹 경로의 스테이지에 USD 참조를 추가합니다.                         |
|--------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| [`get_stage_up_axis`](#isaaclab.sim.utils.legacy.get_stage_up_axis)()                                  | 스테이지의 업 축을 가져옵니다.                                                        |
| [`traverse_stage`](#isaaclab.sim.utils.legacy.traverse_stage)([fabric])                                | 스테이지를 순회하고 모든 프리믹을 반환합니다.                                        |
| [`get_prim_at_path`](#isaaclab.sim.utils.legacy.get_prim_at_path)(prim_path[, fabric])                 | 지정된 경로의 USD 프리믹을 가져옵니다.                                              |
| [`get_prim_path`](#isaaclab.sim.utils.legacy.get_prim_path)(prim)                                      | 지정된 USD 프리믹의 경로를 가져옵니다.                                              |
| [`is_prim_path_valid`](#isaaclab.sim.utils.legacy.is_prim_path_valid)(prim_path[, fabric])             | 지정된 스테이지에서 유효한 USD 프리믹이 존재하는 경로인지 확인합니다.                          |
| [`define_prim`](#isaaclab.sim.utils.legacy.define_prim)(prim_path[, prim_type, fabric])                | 이미 존재하지 않는 경우 지정된 프리믹 경로에 주어진 타입의 USD 프리믹을 생성합니다. |
| [`get_prim_type_name`](#isaaclab.sim.utils.legacy.get_prim_type_name)(prim_path[, fabric])             | 제공된 경로의 USD 프리믹의 타입 이름을 가져옵니다.                               |
| [`get_next_free_path`](#isaaclab.sim.utils.legacy.get_next_free_path)(path)                            | 기본 경로를 기준으로 스테이지에 존재하지 않는 새로운 프리믹 경로를 가져옵니다.               |

### isaaclab.sim.utils.legacy.logger *= <Logger isaaclab.sim.utils.legacy (WARNING)>*

스테이지 유틸리티입니다.

### isaaclab.sim.utils.legacy.add_reference_to_stage(usd_path: [str](https://docs.python.org/3/library/stdtypes.html#str), path: [str](https://docs.python.org/3/library/stdtypes.html#str), prim_type: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'Xform') → pxr.Usd.Prim

지정된 프리믹 경로의 스테이지에 USD 참조를 추가합니다.

#### Deprecated
버전 2.3.0부터 지원 중단: 이 함수는 지원 중단되었습니다. 대신 [`isaaclab.sim.utils.prims.add_usd_reference()`](#isaaclab.sim.utils.prims.add_usd_reference) 함수를 사용하십시오.

* **Parameters:**
  * **usd_path** – 참조할 USD 파일의 경로입니다.
  * **path** – 참조를 추가할 프리믹 경로입니다.
  * **prim_type** – 존재하지 않을 경우 생성할 프리믹의 타입입니다. 기본값은 “Xform”입니다.
* **Returns:**
  지정된 프리믹 경로의 USD 프리믹입니다.

### isaaclab.sim.utils.legacy.get_stage_up_axis() → [str](https://docs.python.org/3/library/stdtypes.html#str)

스테이지의 업 축을 가져옵니다.

#### Deprecated
버전 2.3.0부터 지원 중단: 이 함수는 지원 중단되었습니다. 대신 USD API를 직접 사용하십시오.

```pycon
>>> import isaaclab.sim as sim_utils
>>> from pxr import UsdGeom
>>>
>>> UsdGeom.GetStageUpAxis(sim_utils.get_current_stage())
'Z'
```

### isaaclab.sim.utils.legacy.traverse_stage(fabric: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [Iterable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Iterable)[pxr.Usd.Prim]

단계를 순회하고 모든 prims를 반환합니다.

#### Deprecated
버전 2.3.0부터 더 이상 사용되지 않음: 이 함수는 사용되지 않음. 대신 USD API를 직접 사용하세요.

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> stage = sim_utils.get_current_stage()
>>> for prim in stage.Traverse():
>>>     print(prim)
Usd.Prim(</World>)
Usd.Prim(</World/Cube>)
Usd.Prim(</World/Cube_01>)
Usd.Prim(</World/Cube_02>)
```

* **매개변수:**
  **fabric** – fabric 단계인 경우 True, USD 단계인 경우 False. 기본값은 False입니다.
* **반환값:**
  단계의 모든 prims로 구성된 반복 가능한 객체입니다.

### isaaclab.sim.utils.legacy.get_prim_at_path(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), fabric: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → Usd.Prim | [None](https://docs.python.org/3/library/constants.html#None)

지정된 경로에 있는 USD prim을 가져옵니다.

#### Deprecated
버전 2.3.0부터 더 이상 사용되지 않음: 이 함수는 사용되지 않음. 대신 USD API를 직접 사용하세요.

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> stage = sim_utils.get_current_stage()
>>> stage.GetPrimAtPath("/World/Cube")
Usd.Prim(</World/Cube>)
```

* **매개변수:**
  * **prim_path** – 가져올 prim의 경로입니다.
  * **fabric** – fabric 단계에서 prim을 가져올지 여부. 기본값은 False입니다.
* **반환값:**
  지정된 경로에 있는 USD prim. 단계가 없으면 None을 반환합니다.

### isaaclab.sim.utils.legacy.get_prim_path(prim: pxr.Usd.Prim) → [str](https://docs.python.org/3/library/stdtypes.html#str)

지정된 USD prim의 경로를 가져옵니다.

#### Deprecated
버전 2.3.0부터 더 이상 사용되지 않음: 이 함수는 사용되지 않음. 대신 USD API를 직접 사용하세요.

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> stage = sim_utils.get_current_stage()
>>> prim = stage.GetPrimAtPath("/World/Cube")
>>> prim.GetPath().pathString
"/World/Cube"
```

* **매개변수:**
  **prim** – 경로를 가져올 USD prim입니다.
* **반환값:**
  지정된 USD prim의 경로입니다.

### isaaclab.sim.utils.legacy.is_prim_path_valid(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), fabric: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [bool](https://docs.python.org/3/library/functions.html#bool)

지정된 단계에서 경로에 유효한 USD Prim이 있는지 확인합니다.

#### Deprecated
버전 2.3.0부터 더 이상 사용되지 않음: 이 함수는 사용되지 않음. 대신 USD API를 직접 사용하세요.

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> stage = sim_utils.get_current_stage()
>>> prim = stage.GetPrimAtPath("/World/Cube")
>>> prim.IsValid()
True
```

* **매개변수:**
  * **prim_path** – 단계에서의 prim 경로
  * **fabric** – fabric 단계인 경우 True, USD 단계인 경우 False. 기본값은 False입니다.
* **반환값:**
  경로가 유효한 prim을 가리키는 경우 True, 그렇지 않은 경우 False.

### isaaclab.sim.utils.legacy.define_prim(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), prim_type: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'Xform', fabric: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → pxr.Usd.Prim

주어진 prim_path에 지정된 prim_type의 USD Prim이 아직 존재하지 않는 경우 생성합니다.

이 함수는 지정된 경로에 지정된 유형의 prim을 생성합니다. 변환(위치, 방향, 스케일)을 적용하거나, 속성을 설정하거나, prim 생성 중에 USD 파일을 로드하려면 [`isaaclab.sim.utils.prims.create_prim()`](#isaaclab.sim.utils.prims.create_prim) 함수를 사용하세요.

#### Deprecated
버전 2.3.0부터 더 이상 사용되지 않음: 이 함수는 사용되지 않음. 대신 USD API를 직접 사용하세요.
새로운 prim이 필요한 경우, [`isaaclab.sim.utils.prims.create_prim()`](#isaaclab.sim.utils.prims.create_prim) 함수를 대신 사용하세요.

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> stage = sim_utils.get_current_stage()
>>> stage.DefinePrim("/World/Shapes", "Xform")
Usd.Prim(</World/Shapes>)
```

* **매개변수:**
  * **prim_path** – 단계 내의 prim 경로
  * **prim_type** – 생성할 prim의 유형. 기본값은 “Xform”입니다.
  * **fabric** – fabric 단계인 경우 True, USD 단계인 경우 False. 기본값은 False입니다.
* **반환값:**
  생성된 USD prim입니다.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – prim_path에 이미 prim이 존재하는 경우

### isaaclab.sim.utils.legacy.get_prim_type_name(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | Usd.Prim, fabric: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [str](https://docs.python.org/3/library/stdtypes.html#str)

제공된 경로에 있는 USD Prim의 타입 이름을 가져옵니다.

#### Deprecated
버전 2.3.0부터 더 이상 사용되지 않음: 이 함수는 사용되지 않음. 대신 USD API를 직접 사용하세요.

```pycon
>>> import isaaclab.sim as sim_utils
>>>
>>> stage = sim_utils.get_current_stage()
>>> prim = stage.GetPrimAtPath("/World/Cube")
>>> prim.GetTypeName()
"Cube"
```

* **매개변수:**
  * **prim_path** – 단계 내의 prim 경로 또는 prim 자체
  * **fabric** – fabric 단계인 경우 True, USD 단계인 경우 False. 기본값은 False입니다.
* **반환값:**
  제공된 경로에 있는 USD Prim의 타입 이름입니다.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 경로에 유효한 prim이 없는 경우

### isaaclab.sim.utils.legacy.get_next_free_path(path: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [str](https://docs.python.org/3/library/stdtypes.html#str)

주어진 기본 경에 대해 단계에 존재하지 않는 새로운 prim 경로를 반환합니다.

#### Deprecated
버전 2.3.0부터 더 이상 사용되지 않음: 이 함수는 사용되지 않음. 대신
[`isaaclab.sim.utils.queries.get_next_free_prim_path()`](#isaaclab.sim.utils.queries.get_next_free_prim_path) 함수를 사용하세요.

* **매개변수:**
  * **path** – 확인할 기본 prim 경로입니다.
  * **stage** – 확인할 단계. 기본값은 현재 단계입니다.
* **반환값:**
  현재 단계에 존재하지 않는 보장된 새로운 경로입니다.
