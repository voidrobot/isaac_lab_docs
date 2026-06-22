# isaaclab_tasks.utils

유틸리티, 데이터 수집기 및 환경 래퍼가 포함된 하위 패키지입니다.

**함수:**

| [`import_packages`](#isaaclab_tasks.utils.import_packages)(package_name[, blacklist_pkgs])   | 패키지의 모든 하위 패키지를 재귀적으로 가져옵니다.                       |
|----------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| [`get_checkpoint_path`](#isaaclab_tasks.utils.get_checkpoint_path)(log_path[, run_dir, ...]) | 입력 디렉터리에서 모델 체크포인트 경로를 가져옵니다.                    |
| [`load_cfg_from_registry`](#isaaclab_tasks.utils.load_cfg_from_registry)(task_name, ...)     | gym 레지스트리에서 엔트리 포인트를 기반으로 기본 구성을 로드합니다.     |
| [`parse_env_cfg`](#isaaclab_tasks.utils.parse_env_cfg)(task_name[, device, num_envs, ...])   | 환경 구성을 분석하고 입력값에 따라 재정의합니다.    |

### isaaclab_tasks.utils.import_packages(package_name: [str](https://docs.python.org/3/library/stdtypes.html#str), blacklist_pkgs: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

패키지의 모든 하위 패키지를 재귀적으로 가져옵니다.

이 함수를 사용하면 패키지의 모든 하위 패키지를 재귀적으로 수동으로 가져오는 것보다 편리합니다.

다음 코드 스니펫을 각 패키지의 `__init__.py` 파일 상단에 수동으로 추가할 필요가 없어집니다:

```python
import .locomotion.velocity
import .manipulation.reach
import .manipulation.lift
```

* **매개변수:**
  * **package_name** – 패키지 이름.
  * **blacklist_pkgs** – 건너뛸 블랙리스트에 추가된 패키지 목록. 기본값은 None이며, 이는 블랙리스트에 추가된 패키지가 없음을 의미합니다.

### isaaclab_tasks.utils.get_checkpoint_path(log_path: [str](https://docs.python.org/3/library/stdtypes.html#str), run_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) = '.\*', checkpoint: [str](https://docs.python.org/3/library/stdtypes.html#str) = '.\*', other_dirs: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] = None, sort_alpha: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [str](https://docs.python.org/3/library/stdtypes.html#str)

입력 디렉터리에서 모델 체크포인트 경로를 가져옵니다.

체크포인트 파일은 다음과 같이 해석됩니다: `<log_path>/<run_dir>/<*other_dirs>/<checkpoint>`, 여기서 `other_dirs`는 연결할 중간 폴더 이름입니다. 이들은 정규표현식이 될 수 없습니다.

`run_dir`와 `checkpoint`가 정규표현식인 경우, 가장 최근(알파벳 순서로 가장 높은 순서)의 실행 및 체크포인트가 선택됩니다. 이 동작을 비활성화하려면 플래그 `sort_alpha`를 False로 설정합니다.

* **매개변수:**
  * **log_path** – 모델을 찾을 로그 디렉터리 경로.
  * **run_dir** – 실행이 포함된 디렉터리 이름의 정규표현식. 기본값은 `log_path` 내에서 가장 최근에 생성된 디렉터리입니다.
  * **other_dirs** – 실행 디렉터리와 체크포인트 파일 사이의 중간 디렉터리. 기본값은 None이며, 이는 체크포인트 파일이 실행 디렉터리 바로 아래에 있음을 의미합니다.
  * **checkpoint** – 모델 체크포인트 파일의 정규표현식. 기본값은 `run_dir` 디렉터리에서 가장 최근에 저장된 torch 모델입니다.
  * **sort_alpha** – 실행 경로를 알파벳 순서로 정렬할지 여부. 기본값은 True입니다.
    False로 설정하면 `run_dir` 내의 폴더가 마지막으로 수정된 시간 기준으로 정렬됩니다.
* **반환값:**
  모델 체크포인트 경로.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 디렉터리에서 실행이 발견되지 않을 때 발생합니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 디렉터리에서 체크포인트가 발견되지 않을 때 발생합니다.

### isaaclab_tasks.utils.load_cfg_from_registry(task_name: [str](https://docs.python.org/3/library/stdtypes.html#str), entry_point_key: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [object](https://docs.python.org/3/library/functions.html#object)

gym 레지스트리의 엔트리 포인트를 기반으로 기본 구성을 로드합니다.

이 함수는 주어진 작업 이름에 대해 gym 레지스트리에서 구성 객체를 로드합니다. YAML 및 Python 구성 파일을 모두 지원합니다.

다음과 같이 gym 레지스트리에 구성이 등록되어 있다고 가정합니다:

```python
gym.register(
    id="My-Awesome-Task-v0",
    ...
    kwargs={"env_entry_point_cfg": "path.to.config:ConfigClass"},
)
```

위의 예시에 대한 파싱된 구성 객체는 다음과 같이 얻을 수 있습니다:

```python
from isaaclab_tasks.utils.parse_cfg import load_cfg_from_registry

cfg = load_cfg_from_registry("My-Awesome-Task-v0", "env_entry_point_cfg")
```

* **매개변수:**
  * **task_name** – 환경의 이름.
  * **entry_point_key** – 구성 파일을 해결할 엔트리 포인트 키.
* **반환값:**
  파싱된 구성 객체. 엔트리 포인트가 YAML 파일이면 딕셔너리로 파싱됩니다. 엔트리 포인트가 Python 클래스이면 인스턴스화되어 반환됩니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 해당 작업에 대해 gym 레지스트리에서 엔트리 포인트 키를 사용할 수 없을 때 발생합니다.

### isaaclab_tasks.utils.parse_env_cfg(task_name: [str](https://docs.python.org/3/library/stdtypes.html#str), device: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'cuda:0', num_envs: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, use_fabric: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg) | [DirectRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnvCfg)

환경 구성을 분석하고 입력값에 따라 재정의합니다.

* **매개변수:**
  * **task_name** – 환경의 이름.
  * **device** – 시뮬레이션을 실행할 디바이스. 기본값은 “cuda:0”입니다.
  * **num_envs** – 생성할 환경의 수. 기본값은 None이며, 이 경우 값이 변경되지 않습니다.
  * **use_fabric** – 패브릭 인터페이스를 활성화/비활성화할지 여부. False이면 모든 읽기/쓰기 작업이 USD를 통해 수행됩니다.これにより 시뮬레이션 속도가 느려지지만 USD 스테이지를 통해 변경 사항을 확인할 수 있습니다. 기본값은 None이며, 이 경우 값이 변경되지 않습니다.
* **반환값:**
  분석된 구성 객체.
* **예외:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 작업의 구성이 클래스가 아닌 경우 발생합니다. 사용자는 항상 환경 구성에 클래스를 사용한다고 가정합니다.
