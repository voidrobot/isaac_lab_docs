# isaaclab.envs.ui

환경용 UI 윈도우 구현을 제공하는 서브모듈입니다.

UI 요소는 환경을 제어하고 환자의 상태를 시각화하는 데 사용됩니다.
이에는 시뮬레이션에서 로봇 추적, 다양한 디버그 시각화 도구 토글링, 그리고 기타 사용자 정의 기능이 포함됩니다.

### 클래스

| [`BaseEnvWindow`](#isaaclab.envs.ui.BaseEnvWindow)                       | 기본 환경의 윈도우 관리자.                                              |
|--------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| [`ManagerBasedRLEnvWindow`](#isaaclab.envs.ui.ManagerBasedRLEnvWindow)   | RL 환경의 윈도우 관리자.                                                 |
| [`ViewportCameraController`](#isaaclab.envs.ui.ViewportCameraController) | 시뮬레이터의 뷰포트와 관련된 카메라를 제어하는 클래스입니다.                    |

## 기본 환경 UI

### *class* isaaclab.envs.ui.BaseEnvWindow

기본 환경의 윈도우 관리자입니다.

이 클래스는 환경을 제어하는 데 사용되는 윈도우를 생성합니다. 이 윈도우에는 렌더링, 디버그 시각화 및 기타 환경별 UI 요소에 대한 컨트롤이 포함됩니다.

사용자는 with 컨텍스트 관리자를 사용하여 윈도우에 자체 UI 요소를 추가할 수 있습니다.
이는 클래스를 상속받거나 독립 실행형 실행 스크립트에서 `env.window` 객체를 직접 사용함으로써 수행할 수 있습니다.

독립 실행형 실행 스크립트에서 UI 요소를 추가하는 예시:
: ```pycon
  >>> with env.window.ui_window_elements["main_vstack"]:
  >>>     ui.Label("My UI element")
  ```

**메서드:**

| [`__init__`](#isaaclab.envs.ui.BaseEnvWindow.__init__)(env[, window_name])   | 윈도우 초기화.   |
|------------------------------------------------------------------------------|--------------------------|

#### \_\_init_\_(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), window_name: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'IsaacLab')

윈도우 초기화.

* **매개변수:**
  * **env** – 환경 객체.
  * **window_name** – 윈도우 이름. 기본값은 “IsaacLab”.

## 구성 기반 RL 환경 UI

### *class* isaaclab.envs.ui.ManagerBasedRLEnvWindow

Bases: [`BaseEnvWindow`](#isaaclab.envs.ui.BaseEnvWindow)

RL 환경의 윈도우 관리자입니다.

기본 환경 윈도우 위에 이 클래스는 RL 환경을 위한 컨트롤을 추가합니다.
이에는 명령 관리자 시각화가 포함됩니다.

**메서드:**

| [`__init__`](#isaaclab.envs.ui.ManagerBasedRLEnvWindow.__init__)(env[, window_name])   | 윈도우 초기화.   |
|----------------------------------------------------------------------------------------|--------------------------|

#### \_\_init_\_(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), window_name: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'IsaacLab')

윈도우 초기화.

* **매개변수:**
  * **env** – 환경 객체.
  * **window_name** – 윈도우 이름. 기본값은 “IsaacLab”.

## 뷰포트 카메라 컨트롤러

### *class* isaaclab.envs.ui.ViewportCameraController

이 클래스는 시뮬레이터의 뷰포트와 관련된 카메라를 제어합니다.

구성에서 지정된 원점 유형을 추적하도록 뷰포트의 카메라를 설정할 수 있습니다:

- **world**: 세계의 중심(정적)
- **env**: 환경의 중심(정적)
- **asset_root**: 장면의 에셋 루트(예: 장면에서 움직이는 로봇 추적)

생성 시, 카메라는 구성에 지정된 원점 유형을 추적으로 설정됩니다.

`asset_root` 원점 유형의 경우, 카메라는 에셋의 루트 위치를 추적하기 위해 각 렌더링 단계에서 업데이트됩니다.
이를 위해 시뮬레이션 앱의 포스트 업데이트 이벤트 스트림에 콜백을 등록합니다.

**메서드:**

| [`__init__`](#isaaclab.envs.ui.ViewportCameraController.__init__)(env, cfg)                                                | ViewportCameraController 초기화.                                                          |
|----------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| [`set_view_env_index`](#isaaclab.envs.ui.ViewportCameraController.set_view_env_index)(env_index)                           | 카메라 뷰의 환경 인덱스 설정.                                                   |
| [`update_view_to_world`](#isaaclab.envs.ui.ViewportCameraController.update_view_to_world)()                                | 뷰어의 원점을 세계 원점(0, 0, 0)으로 업데이트.                        |
| [`update_view_to_env`](#isaaclab.envs.ui.ViewportCameraController.update_view_to_env)()                                    | 뷰어의 원점을 선택된 환경의 원점으로 업데이트.                            |
| [`update_view_to_asset_root`](#isaaclab.envs.ui.ViewportCameraController.update_view_to_asset_root)(asset_name)            | 장면에 있는 에셋의 루트를 기준으로 뷰어의 원점 업데이트.                         |
| [`update_view_to_asset_body`](#isaaclab.envs.ui.ViewportCameraController.update_view_to_asset_body)(asset_name, body_name) | 장면에 있는 에셋의 본체를 기준으로 뷰어의 원점 업데이트.                         |
| [`update_view_location`](#isaaclab.envs.ui.ViewportCameraController.update_view_location)([eye, lookat])                   | 현재 뷰어 원점 및 눈과 lookat 위치에 기반한 카메라 뷰 포즈 업데이트. |

**속성:**

| [`cfg`](#isaaclab.envs.ui.ViewportCameraController.cfg)   | 뷰어의 구성.   |
|-----------------------------------------------------------|-------------------------------------|

#### \_\_init_\_(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv) | [DirectRLEnv](isaaclab.envs.md#isaaclab.envs.DirectRLEnv), cfg: [ViewerCfg](isaaclab.envs.md#isaaclab.envs.ViewerCfg))

ViewportCameraController 초기화.

* **매개변수:**
  * **env** – 환경.
  * **cfg** – 뷰포트 카메라 컨트롤러의 구성.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 원점 유형이 “env”로 구성되었지만 `cfg.env_index`가 범위를 벗어난 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 원점 유형이 “asset_root”로 구성되었지만 `cfg.asset_name`이 설정되지 않은 경우.

#### *property* cfg *: [ViewerCfg](isaaclab.envs.md#isaaclab.envs.ViewerCfg)*

뷰어의 구성.

#### set_view_env_index(env_index: [int](https://docs.python.org/3/library/functions.html#int))

카메라 뷰의 환경 인덱스 설정.

* **매개변수:**
  **env_index** – 카메라 뷰를 설정할 환경의 인덱스.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 환경 인덱스가 범위를 벗어난 경우. 0과 num_envs - 1 사이여야 합니다.

#### update_view_to_world()

뷰어의 원점을 세계 원점(0, 0, 0)으로 업데이트합니다.

#### update_view_to_env()

뷰어의 원점을 선택된 환경의 원점으로 업데이트합니다.

#### update_view_to_asset_root(asset_name: [str](https://docs.python.org/3/library/stdtypes.html#str))

장면에 있는 에셋의 루트를 기준으로 뷰어의 원점을 업데이트합니다.

* **매개변수:**
  **asset_name** – 장면의 에셋 이름. 이름은 장면의 에셋 이름과 일치해야 합니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 에셋이 장면에 없는 경우.

#### update_view_to_asset_body(asset_name: [str](https://docs.python.org/3/library/stdtypes.html#str), body_name: [str](https://docs.python.org/3/library/stdtypes.html#str))

장면에 있는 에셋의 본체를 기준으로 뷰어의 원점을 업데이트합니다.

* **매개변수:**
  * **asset_name** – 장면의 에셋 이름. 이름은 장면의 에셋 이름과 일치해야 합니다.
  * **body_name** – 에셋의 본체 이름.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 에셋이 장면에 없거나 본체가 유효하지 않은 경우.

#### update_view_location(eye: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, lookat: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

현재 뷰어 원점 및 눈과 lookat 위치에 기반한 카메라 뷰 포즈를 업데이트합니다.

* **매개변수:**
  * **eye** – 카메라의 눈 위치. None인 경우 현재 눈 위치 사용.
  * **lookat** – 카메라의 lookat 위치. None인 경우 현재 lookat 위치 사용.
