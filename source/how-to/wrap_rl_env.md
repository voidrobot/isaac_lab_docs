<a id="how-to-env-wrappers"></a>

# 환경 래퍼

환경 래퍼는 환경을 수정하지 않고 환경을 수정하는 방법입니다. 이는 관측값이나 보상값을 수정하는 함수 적용, 비디오 기록, 시간 제한 강제 등으로 사용할 수 있습니다. API의 상세한 설명은 [`gymnasium.Wrapper`](https://gymnasium.farama.org/api/wrappers/#gymnasium.Wrapper) 클래스에서 확인할 수 있습니다.

현재 [`ManagerBasedRLEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) 또는 [`DirectRLEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv) 클래스를 상속하는 모든 RL 환경은 기본 클래스가 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인터페이스를 구현하기 때문에 [`gymnasium.Wrapper`](https://gymnasium.farama.org/api/wrappers/#gymnasium.Wrapper)와 호환됩니다. 환경을 래핑하려면 먼저 기본 환경을 초기화해야 합니다. 그 후, `env = wrapper(env, *args, **kwargs)`를 반복해서 원하는 만큼의 래퍼로 환경을 래핑할 수 있습니다.

예를 들어, reset이 step 또는 render 전에 호출되도록 환경을 래핑하는 방법은 다음과 같습니다:

```python
"""Isaac Sim 시뮬레이터를 먼저 실행합니다."""


from isaaclab.app import AppLauncher

# 헤드리스 모드로 옴니버스 앱 실행
app_launcher = AppLauncher(headless=True)
simulation_app = app_launcher.app

"""그 이후의 모든 내용은 여기부터입니다."""

import gymnasium as gym

import isaaclab_tasks  # noqa: F401
from isaaclab_tasks.utils import load_cfg_from_registry

# 기본 환경 생성
cfg = load_cfg_from_registry("Isaac-Reach-Franka-v0", "env_cfg_entry_point")
env = gym.make("Isaac-Reach-Franka-v0", cfg=cfg)
# reset이 step 전에 호출되도록 환경 래핑
env = gym.wrappers.OrderEnforcing(env)
```

## 비디오 기록을 위한 래퍼

[`gymnasium.wrappers.RecordVideo`](https://gymnasium.farama.org/api/wrappers/misc_wrappers/#gymnasium.wrappers.RecordVideo) 래퍼는 환경의 비디오를 기록하는 데 사용할 수 있습니다. 이 래퍼는 `video_dir` 인수를 받아 비디오를 저장할 위치를 지정합니다. 비디오는 지정된 환경 스텝 또는 에피소드 수에 대해 지정된 간격으로 [mp4](https://en.wikipedia.org/wiki/MP4_file_format) 형식으로 저장됩니다.

래퍼를 사용하려면 먼저 `ffmpeg`를 설치해야 합니다. Ubuntu에서는 다음 명령을 실행하여 설치할 수 있습니다:

```bash
sudo apt-get install ffmpeg
```

#### 주의
기본적으로 헤드리스 모드에서 환경을 실행할 때, 옴니버스 뷰포트가 비활성화됩니다. 불필요한 렌더링을 방지하여 성능을 향상시키기 위해 이렇게 수행됩니다.

RTX 3090 GPU를 사용한 `Isaac-Reach-Franka-v0` 환경에서 다양한 렌더링 모드에서의 성능은 다음과 같습니다:

* 화면 없는 실행 및 오프스크린 렌더링 비활성화: ~65,000 FPS
* 화면 없는 실행 및 오프스크린 렌더링 활성화: ~57,000 FPS
* GUI 실행 및 전체 렌더링: ~13,000 FPS

렌더링에 사용되는 뷰포트 카메라는 장면의 기본 카메라인 `"/OmniverseKit_Persp"`입니다. 카메라의 포즈와 이미지 해상도는 [`ViewerCfg`](../api/lab/isaaclab.envs.md#isaaclab.envs.ViewerCfg) 클래스를 통해 구성할 수 있습니다.

### ViewerCfg 클래스의 기본 매개변수:

```python
@configclass
class ViewerCfg:
    """씬 뷰포트 카메라의 구성."""

    eye: tuple[float, float, float] = (7.5, 7.5, 7.5)
    """초기 카메라 위치(단위: m). 기본값은 (7.5, 7.5, 7.5)."""

    lookat: tuple[float, float, float] = (0.0, 0.0, 0.0)
    """초기 카메라 타겟 위치(단위: m). 기본값은 (0.0, 0.0, 0.0)."""

    cam_prim_path: str = "/OmniverseKit_Persp"
    """이미지를 기록할 카메라 프리뮬 경로. 기본값은 "/OmniverseKit_Persp"로, 뷰포트의 기본 카메라입니다.
    """

    resolution: tuple[int, int] = (1280, 720)
    """`:attr:`cam_prim_path`으로 지정된 카메라의 해상도(너비, 높이). 기본값은 (1280, 720).
    """

    origin_type: Literal["world", "env", "asset_root", "asset_body"] = "world"
    """카메라 위치(eye)와 타겟(lookat)이 정의되는 프레임. 기본값은 "world".

    사용 가능한 옵션은 다음과 같습니다:

    * ``"world"``: 세계의 원점.
    * ``"env"``: :attr:`env_index`에 의해 정의된 환경의 원점.
    * ``"asset_root"``: 환경 :attr:`env_index`의 :attr:`asset_name`에 의해 정의된 자산의 중심.
    * ``"asset_body"``: 환경 :attr:`env_index`의 :attr:`asset_name`에 의해 정의된 자산의 :attr:`body_name`에 의해 정의된 바디의 중심.
    """

    env_index: int = 0
    """프레임 원점의 환경 인덱스. 기본값은 0.

    이 값은 :attr:`origin`이 "env" 또는 "asset_root"로 설정된 경우에만 유효합니다.
    """

    asset_name: str | None = None
    """프레임 원점을 위한 인터랙티브 씬의 자산 이름. 기본값은 None.

    이 값은 :attr:`origin`이 "asset_root"로 설정된 경우에만 유효합니다.
    """

    body_name: str | None = None
    """프레임 원점을 위한 인터랙티브 씬의 :attr:`asset_name`에 있는 바디 이름. 기본값은 None.

    이 값은 :attr:`origin`이 "asset_body"로 설정된 경우에만 유효합니다.
    """
```

매개변수를 조정한 후, 오프스크린 렌더링 플래그를 활성화하고 [`gymnasium.wrappers.RecordVideo`](https://gymnasium.farama.org/api/wrappers/misc_wrappers/#gymnasium.wrappers.RecordVideo) 래퍼로 환경을 래핑하여 비디오를 기록할 수 있습니다. 또한 환경의 렌더 모드를 `"rgb_array"`로 지정해야 합니다.

예를 들어, 다음 코드는 `Isaac-Reach-Franka-v0` 환경의 비디오를 200 스텝 동안 기록하고, 1500 스텝 간격으로 `videos` 폴더에 저장합니다.

```python
"""Isaac Sim 시뮬레이터를 먼저 실행합니다."""


from isaaclab.app import AppLauncher

# 오프스크린 렌더링을 활성화하여 헤드리스 모드로 옴니버스 앱 실행
app_launcher = AppLauncher(headless=True, enable_cameras=True)
simulation_app = app_launcher.app

"""그 이후의 모든 내용은 여기부터입니다."""

import gymnasium as gym

# 카메라 해상도 및 포즈 조정
env_cfg.viewer.resolution = (640, 480)
env_cfg.viewer.eye = (1.0, 1.0, 1.0)
env_cfg.viewer.lookat = (0.0, 0.0, 0.0)
# isaac-env 인스턴스 생성
# render 호출 시 이미지를 얻기 위해 render 모드를 "rgb_array"로 설정
env = gym.make(task_name, cfg=env_cfg, render_mode="rgb_array")
# 비디오 기록을 위해 래핑
video_kwargs = {
    "video_folder": "videos/train",
    "step_trigger": lambda step: step % 1500 == 0,
    "video_length": 200,
}
env = gym.wrappers.RecordVideo(env, **video_kwargs)
```

## 학습 프레임워크를 위한 래퍼

각 학습 프레임워크는 환경을 상호작용하는 자체 API를 가지고 있습니다. 예를 들어, [Stable-Baselines3](https://stable-baselines3.readthedocs.io/en/master/) 라이브러리는 [gym.Env](https://gymnasium.farama.org/api/env/) 인터페이스를 사용하여 환경을 상호작용합니다. 그러나 [RL-Games](https://github.com/Denys88/rl_games), [RSL-RL](https://github.com/leggedrobotics/rsl_rl) 또는 [SKRL](https://skrl.readthedocs.io)와 같은 라이브러리는 학습 환경을 인터페이스하기 위해 자체 API를 사용합니다. 한 가지 솔루션으로 모든 것을 해결할 수 없기 때문에, [`ManagerBasedRLEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)와 [`DirectRLEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv) 클래스는 특정 학습 프레임워크의 환경 정의를 기반으로 하지 않습니다. 대신, 학습 프레임워크의 환경 정의와 호환되도록 래퍼를 구현합니다.

Stable-Baselines3와 함께 RL 태스크 환경을 사용하는 방법의 예시는 다음과 같습니다:

```python
from isaaclab_rl.sb3 import Sb3VecEnvWrapper

# isaac-env 인스턴스 생성
env = gym.make(task_name, cfg=env_cfg)
# 안정적인 기준을 위한 환경 래핑
env = Sb3VecEnvWrapper(env)
```

## 새 래퍼 추가

모든 새 래퍼는 [`isaaclab_rl`](../api/lab_rl/isaaclab_rl.md#module-isaaclab_rl) 모듈에 추가해야 합니다. 래퍼를 적용하기 전에 기본 환경이 [`isaaclab.envs.ManagerBasedRLEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) 또는 [`DirectRLEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv)의 인스턴스인지 확인해야 합니다. 이는 `unwrapped()` 속성을 사용하여 수행할 수 있습니다.

이 모듈에는 자신의 래퍼를 구현하는 데 참고할 수 있는 래퍼 세트가 포함되어 있습니다. 새로운 래퍼를 구현했다면, 풀 리퀘스트를 열어 프레임워크에 기여하는 것을 고려해 주세요.
