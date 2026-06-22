# 설치

- `apt`을 사용하여 의존성을 설치합니다 (Linux에서만 가능):
  ```bash
  # robomimic에서 필요한 종속성이며 Windows에서는 사용할 수 없습니다
  sudo apt install cmake build-essential
  ```
- `source` 디렉토리의 모든 확장 프로그램을 반복하여 pip로 설치합니다 (`--editable` 플래그 사용):

  ### Linux

  ```bash
  ./isaaclab.sh --install # 또는 "./isaaclab.sh -i"
  ```

  ### Windows

  ```batch
  isaaclab.bat --install :: 또는 "isaaclab.bat -i"
  ```

  기본적으로 위 명령은 **모든** 학습 프레임워크를 설치합니다. 이는 다음과 같습니다:
  `rl_games`, `rsl_rl`, `sb3`, `skrl`, `robomimic`.

  특정 프레임워크만 설치하려면 프레임워크 이름을 인수로 전달할 수 있습니다. 예를 들어, `rl_games` 프레임워크만 설치하려면 다음과 같이 실행합니다:

  ### Linux

  ```bash
  ./isaaclab.sh --install rl_games  # 또는 "./isaaclab.sh -i rl_games"
  ```

  ### Windows

  ```batch
  isaaclab.bat --install rl_games :: 또는 "isaaclab.bat -i rl_games"
  ```

  유효한 옵션은 `all`, `rl_games`, `rsl_rl`, `sb3`, `skrl`, `robomimic`, 그리고 `none`입니다. `none`이 전달되면 학습 프레임워크가 하나도 설치되지 않습니다.
