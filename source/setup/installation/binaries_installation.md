<a id="isaaclab-binaries-installation"></a>

# Isaac Sim 사전 빌드된 바이너리를 사용한 설치

다음 단계는 먼저 사전 빌드된 바이너리에서 Isaac Sim을 설치한 후, 소스 코드에서 Isaac Lab을 설치하는 순서로 진행됩니다.

## Isaac Sim 설치하기

### 사전 빌드된 바이너리 다운로드

Isaac Sim 바이너리는
[여기](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/download.html)에서 zip 파일로 직접 다운로드할 수 있습니다.
오래된 Isaac Sim 4.5 릴리스를 사용하려면 다음 페이지를 확인하세요
[여기](https://docs.isaacsim.omniverse.nvidia.com/4.5.0/installation/download.html).

zip 파일을 다운로드한 후 원하는 디렉터리에 압축을 풉니다.
예시로 Isaac Sim 바이너리 압축 해제 지침을 확인하려면
[Isaac Sim 문서](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_workstation.html#example-installation)를 참조하세요.

### Linux

Linux 시스템에서는 Isaac Sim 디렉터리를 `${HOME}/isaacsim`으로 가정합니다.

### Windows

Windows 시스템에서는 Isaac Sim 디렉터리를 `C:\isaacsim`으로 가정합니다.

### Isaac Sim 설치 검증

Isaac Sim 설치 디렉터리를 매번 찾고 위치를 확인하는 오버헤드를 피하려면, 남은 설치 과정 동안 다음 환경 변수를 터미널에 내보내는 것을 권장합니다:

### Linux

```bash
# Isaac Sim 루트 디렉터리
export ISAACSIM_PATH="${HOME}/isaacsim"
# Isaac Sim python 실행 파일
export ISAACSIM_PYTHON_EXE="${ISAACSIM_PATH}/python.sh"
```

### Windows

```batch
:: Isaac Sim 루트 디렉터리
set ISAACSIM_PATH="C:\isaacsim"
:: Isaac Sim python 실행 파일
set ISAACSIM_PYTHON_EXE="%ISAACSIM_PATH:"=%\python.bat"
```

시뮬레이터가 예상대로 실행되는지 확인합니다:

### Linux

```bash
# 참고: "--help" 인수를 전달하여 가능한 모든 인수를 확인할 수 있습니다.
${ISAACSIM_PATH}/isaac-sim.sh
```

### Windows

```batch
:: 참고: "--help" 인수를 전달하여 가능한 모든 인수를 확인할 수 있습니다.
%ISAACSIM_PATH%\isaac-sim.bat
```

독립형 파이썬 스크립트에서 시뮬레이터가 실행되는지 확인합니다:

### Linux

```bash
# 파이썬 경로가 올바르게 설정되었는지 확인
${ISAACSIM_PYTHON_EXE} -c "print('Isaac Sim configuration is now complete.')"
# Isaac Sim이 파이썬에서 실행될 수 있는지 확인
${ISAACSIM_PYTHON_EXE} ${ISAACSIM_PATH}/standalone_examples/api/isaacsim.core.api/add_cubes.py
```

### Windows

```batch
:: 파이썬 경로가 올바르게 설정되었는지 확인
%ISAACSIM_PYTHON_EXE% -c "print('Isaac Sim configuration is now complete.')"
:: Isaac Sim이 파이썬에서 실행될 수 있는지 확인
%ISAACSIM_PYTHON_EXE% %ISAACSIM_PATH%\standalone_examples\api\isaacsim.core.api\add_cubes.py
```

위의 지침을 따르면서 시뮬레이터가 실행되지 않거나 충돌이 발생하면 구성이 잘못된 것을 의미합니다. 디버깅 및 문제 해결을 위해서는 Isaac Sim
[문서](https://docs.omniverse.nvidia.com/dev-guide/latest/linux-troubleshooting.html)와
[Isaac Sim 포럼](https://docs.isaacsim.omniverse.nvidia.com/latest/common/feedback.html)을 확인하세요.

## Isaac Lab 설치하기

### Isaac Lab 복제하기

#### NOTE
프로젝트에 기여하기 위해 Isaac Lab 저장소를 [포크](https://github.com/isaac-sim/IsaacLab/fork)하는 것을 권장하지만, 프레임워크를 사용하기 위해서는 필수가 아닙니다. 포크를 만드는 경우, 다음 지침에서 `isaac-sim`을 사용자 이름으로 바꾸세요.

Isaac Lab 저장소를 프로젝트의 워크스페이스에 클론합니다:

### SSH

```bash
git clone git@github.com:isaac-sim/IsaacLab.git
```

### HTTPS

```bash
git clone https://github.com:isaac-sim/IsaacLab.git
```

Linux 및 Windows 각각에 대해 확장 기능을 관리하는 유틸리티를 제공하는 도우미 실행 파일 [isaaclab.sh](https://github.com/isaac-sim/IsaacLab/blob/main/isaaclab.sh)와 [isaaclab.bat](https://github.com/isaac-sim/IsaacLab/blob/main/isaaclab.bat)를 제공합니다.

### Linux

```text
./isaaclab.sh --help

usage: isaaclab.sh [-h] [-i] [-f] [-p] [-s] [-t] [-o] [-v] [-d] [-n] [-c] -- Utility to manage Isaac Lab.

optional arguments:
   -h, --help           Display the help content.
   -i, --install [LIB]  Install the extensions inside Isaac Lab and learning frameworks (rl_games, rsl_rl, sb3, skrl) as extra dependencies. Default is 'all'.
   -f, --format         Run pre-commit to format the code and check lints.
   -p, --python         Run the python executable provided by Isaac Sim or virtual environment (if active).
   -s, --sim            Run the simulator executable (isaac-sim.sh) provided by Isaac Sim.
   -t, --test           Run all python pytest tests.
   -o, --docker         Run the docker container helper script (docker/container.sh).
   -v, --vscode         Generate the VSCode settings file from template.
   -d, --docs           Build the documentation from source using sphinx.
   -n, --new            Create a new external project or internal task from template.
   -c, --conda [NAME]   Create the conda environment for Isaac Lab. Default name is 'env_isaaclab'.
   -u, --uv [NAME]      Create the uv environment for Isaac Lab. Default name is 'env_isaaclab'.
```

### Windows

```text
isaaclab.bat --help

usage: isaaclab.bat [-h] [-i] [-f] [-p] [-s] [-v] [-d] [-n] [-c] -- Utility to manage Isaac Lab.

optional arguments:
   -h, --help           Display the help content.
   -i, --install [LIB]  Install the extensions inside Isaac Lab and learning frameworks (rl_games, rsl_rl, sb3, skrl) as extra dependencies. Default is 'all'.
   -f, --format         Run pre-commit to format the code and check lints.
   -p, --python         Run the python executable provided by Isaac Sim or virtual environment (if active).
   -s, --sim            Run the simulator executable (isaac-sim.bat) provided by Isaac Sim.
   -t, --test           Run all python pytest tests.
   -v, --vscode         Generate the VSCode settings file from template.
   -d, --docs           Build the documentation from source using sphinx.
   -n, --new            Create a new external project or internal task from template.
   -c, --conda [NAME]   Create the conda environment for Isaac Lab. Default name is 'env_isaaclab'.
   -u, --uv [NAME]      Create the uv environment for Isaac Lab. Default name is 'env_isaaclab'.
```

### Isaac Sim 심볼릭 링크 생성

설치된 Isaac Sim 루트 폴더와 Isaac Lab 디렉터리 내의 `_isaac_sim` 간에 심볼릭 링크를 설정합니다. 이렇게 하면 Isaac Sim과 함께 제공되는 확장 기능을 색인하고 찾는 것이 편리해집니다.

### Linux

```bash
# 클론한 리포지토리로 이동
cd IsaacLab
# 심볼릭 링크 생성
ln -s ${ISAACSIM_PATH} _isaac_sim

# 예를 들어:
# 옵션 1: 사전 빌드된 바이너리를 설치한 경우:
# ln -s ${HOME}/isaacsim _isaac_sim
#
# 옵션 2: Isaac Sim을 소스 코드에서 빌드한 경우:
# ln -s ${HOME}/IsaacSim/_build/linux-x86_64/release _isaac_sim
```

### Windows

```batch
:: 클론한 리포지토리로 이동
cd IsaacLab
:: 관리자 권한으로 명령 프롬프트를 실행해야 하는 심볼릭 링크 생성
mklink /D _isaac_sim %ISAACSIM_PATH%

:: 예를 들어:
:: 옵션 1: 사전 빌드된 바이너리를 설치한 경우:
:: mklink /D _isaac_sim C:\isaacsim
::
:: 옵션 2: Isaac Sim을 소스 코드에서 빌드한 경우:
:: mklink /D _isaac_sim C:\IsaacSim\_build\windows-x86_64\release
```

### Python 환경 설정 (선택 사항)

#### ATTENTION
이 단계는 선택 사항입니다. Isaac Sim에 번들로 포함된 파이썬을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

Isaac Lab을 위한 전용 파이썬 환경을 생성하는 것은 선택 사항이지만 **강력히 권장**됩니다. 가상 환경을 사용하면 다음과 같은 이점이 있습니다:

- **시스템 파이썬 또는 다른 프로젝트와의 충돌 방지**
- **의존성 격리 유지**, 그래서 다른 프로젝트의 패키지 업그레이드나 실험이 Isaac Sim을 깨트리지 않음
- **의존성 버전이 다른 여러 환경을 쉽게 관리**
- **재현성 간소화** — 현재 프로젝트에 필요한 패키지만 포함되어 동료와 설정 공유나 다른 머신에서 실행이 쉬워짐

가상 환경을 생성하기 위해 다양한 패키지 관리자를 선택할 수 있습니다.

- **UV**: 현대적이고 빠르며 안전한 파이썬 패키지 관리자
- **Conda**: 크로스 플랫폼, 언어에 구애받지 않는 파이썬 패키지 관리자

생성된 후에는 가상 환경의 기본 파이썬 (*python* 또는 *python3*)을 대신 사용하여 `./isaaclab.sh -p` 또는 `isaaclab.bat -p`를 실행할 수 있습니다.

### Conda 환경

Conda를 설치하려면 다음 지침을 따르세요
[여기](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html).
다음 명령을 사용하여 Isaac Lab 환경을 생성할 것을 권장합니다.

경량이고 자원 효율적인 환경 관리 시스템인 [Miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main/) 사용을 권장합니다.

### Linux

```bash
# 옵션 1: 기본 환경 이름 'env_isaaclab'
./isaaclab.sh --conda  # 또는 "./isaaclab.sh -c"
# 옵션 2: 사용자 정의 이름
./isaaclab.sh --conda my_env  # 또는 "./isaaclab.sh -c my_env"
```

```bash
# 환경 활성화
conda activate env_isaaclab  # 또는 "conda activate my_env"
```

### Windows

```batch
:: 옵션 1: 기본 환경 이름 'env_isaaclab'
isaaclab.bat --conda  :: 또는 "isaaclab.bat -c"
:: 옵션 2: 사용자 정의 이름
isaaclab.bat --conda my_env  :: 또는 "isaaclab.bat -c my_env"
```

```batch
:: 환경 활성화
conda activate env_isaaclab  # 또는 "conda activate my_env"
```

### UV 환경 (실험적)

`uv`를 설치하려면 다음 지침을 따르세요
[여기](https://docs.astral.sh/uv/getting-started/installation/).
다음 명령을 사용하여 Isaac Lab 환경을 생성할 수 있습니다:

### Linux

```bash
# 옵션 1: 기본 환경 이름 'env_isaaclab'
./isaaclab.sh --uv  # 또는 "./isaaclab.sh -u"
# 옵션 2: 사용자 정의 이름
./isaaclab.sh --uv my_env  # 또는 "./isaaclab.sh -u my_env"
```

```bash
# 환경 활성화
source ./env_isaaclab/bin/activate  # 또는 "source ./my_env/bin/activate"
```

### Windows

#### WARNING
Windows용 UV 지원은 현재 사용할 수 없습니다. 진행 상황을 추적하려면 [issue #3483](https://github.com/isaac-sim/IsaacLab/issues/3438)를 확인하세요.

가상 환경에 진입한 후에는 `./isaaclab.sh -p` 또는 `isaaclab.bat -p`를 사용하여 Python 스크립트를 실행할 필요가 없습니다. 환경의 기본 Python 실행 파일을 `python` 또는 `python3` 명령어로 실행하여 사용할 수 있습니다. 그러나 나머지 문서에서는 `./isaaclab.sh -p` 또는 `isaaclab.bat -p`를 사용하여 Python 스크립트를 실행한다고 가정합니다.

### 설치

- `apt`를 사용하여 종속성을 설치합니다 (Linux 전용):
  ```bash
  # 이 종속성은 Windows에서는 사용 불가능한 robomimic에 필요합니다
  sudo apt install cmake build-essential
  ```
- `source` 디렉터리의 모든 확장을 반복하여 pip(플래그 `--editable` 포함)를 사용해 설치하는 설치 명령을 실행합니다:

  ### Linux

  ```bash
  ./isaaclab.sh --install # 또는 "./isaaclab.sh -i"
  ```

  ### Windows

  ```batch
  isaaclab.bat --install :: 또는 "isaaclab.bat -i"
  ```

  기본적으로 위의 명령은 **모든** 학습 프레임워크를 설치합니다. 여기에는 `rl_games`, `rsl_rl`, `sb3`, `skrl`, `robomimic`이 포함됩니다.

  특정 프레임워크만 설치하려면 프레임워크 이름을 인수로 전달할 수 있습니다. 예를 들어, `rl_games` 프레임워크만 설치하려면 다음과 같이 실행합니다:

  ### Linux

  ```bash
  ./isaaclab.sh --install rl_games  # 또는 "./isaaclab.sh -i rl_games"
  ```

  ### Windows

  ```batch
  isaaclab.bat --install rl_games :: 또는 "isaaclab.bat -i rl_games"
  ```

  유효한 옵션은 `all`, `rl_games`, `rsl_rl`, `sb3`, `skrl`, `robomimic`, 그리고 `none`입니다. `none`이 전달되면 학습 프레임워크가 전혀 설치되지 않습니다.

### Isaac Lab 설치 확인

설치가 성공적으로 이루어졌는지 확인하려면 저장소의 최상위 디렉터리에서 다음 명령을 실행합니다:

### Linux

```bash
# 옵션 1: isaaclab.sh 실행 파일 사용
# 참고: 이는 번들드 Python과 가상 환경 모두에서 작동합니다
./isaaclab.sh -p scripts/tutorials/00_sim/create_empty.py

# 옵션 2: 가상 환경의 Python 사용
python scripts/tutorials/00_sim/create_empty.py
```

### Windows

```batch
:: 옵션 1: isaaclab.bat 실행 파일 사용
:: 참고: 이는 번들드 Python과 가상 환경 모두에서 작동합니다
isaaclab.bat -p scripts\tutorials\00_sim\create_empty.py

:: 옵션 2: 가상 환경의 Python 사용
python scripts\tutorials\00_sim\create_empty.py
```

위 명령어를 실행하면 시뮬레이터가 시작되고 검은 뷰포트와 함께 창이 표시됩니다. 터미널에서 `Ctrl+C`를 눌러 스크립트를 종료할 수 있습니다. Windows 머신에서는 명령 프롬프트에서 `Ctrl+Break` 또는 `Ctrl+fn+B`를 눌러 프로세스를 종료하세요.

![검은 창이 있는 시뮬레이터](../../_static/setup/verify_install.jpg)

이 창이 보이면 설치가 성공적으로 완료된 것입니다! 🎉

#### 참고
`ModuleNotFoundError: No module named 'isaacsim'` 오류가 표시되는 경우, 가상 환경이 활성화되어 있고 `source _isaac_sim/setup_conda_env.sh`가 실행되었는지 확인하세요 (uv 역시 마찬가지입니다).

### 로봇 훈련하기!

이제 Isaac Lab을 사용하여 강화 학습을 통해 로봇을 훈련시킬 수 있습니다! Isaac Lab을 사용하는 가장 빠른 방법은 사전 정의된 워크플로우를 통해 우리 'Batteries-included' 로봇 작업 중 하나를 사용하는 것입니다. 다음 명령을 실행하여 개미가 걷는 것을 빠르게 훈련시켜 보세요! 더 빠른 훈련을 위해 `--headless` 옵션을 추가하는 것을 권장합니다.

### Linux

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Ant-v0 --headless
```

### Windows

```batch
isaaclab.bat -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Ant-v0 --headless
```

… 또는 로봇 개!

### Linux

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Velocity-Rough-Anymal-C-v0 --headless
```

### Windows

```batch
isaaclab.bat -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Velocity-Rough-Anymal-C-v0 --headless
```

Isaac Lab은 프로젝트의 필요에 따라 자신만의 **작업**과 **워크플로우**를 만들기 위한 도구를 제공합니다. [방법 가이드](../../how-to/index.md#how-to)를 살펴보세요. 예를 들어 [자신만의 학습 라이브러리 추가](../../how-to/add_own_library.md#how-to-add-library) 또는 [환경 래핑](../../how-to/wrap_rl_env.md#how-to-env-wrappers)에 대한 자세한 내용을 확인할 수 있습니다.

![휑한 손들...](../../_static/setup/isaac_ants_example.jpg)
