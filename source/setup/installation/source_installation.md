<a id="isaaclab-source-installation"></a>

# Isaac Sim 소스 코드를 사용한 설치

다음 단계에서는 먼저 소스 코드에서 Isaac Sim을 설치하고, 그 다음 소스 코드에서 Isaac Lab을 설치합니다.

#### 참고
이것은 보다 고급 설치 방법이며 대부분의 사용자에게 권장되지 않습니다. Isaac Sim의 소스 코드를 수정하고자 하는 경우에만 이 방법을 따르십시오.

## Isaac Sim 설치

### 소스 코드로 빌드

Isaac Sim 5.0 릴리스부터는 소스 코드에서 Isaac Sim을 빌드할 수 있습니다.
이 접근 방식은 Isaac Sim의 소스 코드를 수정하고 싶거나, Isaac Lab을 Isaac Sim의 나이트리 버전으로 테스트하고자 하는 사용자를 위해 설계되었습니다.

다음 지침은 사용자의 편의를 위해 [Isaac Sim 문서](https://github.com/isaac-sim/IsaacSim?tab=readme-ov-file#quick-start)에서 가져왔습니다.

#### 주의
Isaac Sim을 소스 코드로 빌드하려면 Ubuntu 22.04 LTS 이상이 필요합니다.

#### 주의
드라이버 요구 사항에 대한 자세한 내용은 [기술 요구 사항](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/common/technical-requirements.html) 가이드를 참조하십시오!

Windows에서는 OS 제한으로 인한 설치 오류를 방지하기 위해 [긴 경로 지원 활성화](https://learn.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation?tabs=registry#enable-long-paths-in-windows-10-version-1607-and-later)가 필요할 수 있습니다.

- 워크스페이스에 Isaac Sim 저장소를 클론합니다:
  ```bash
  git clone https://github.com/isaac-sim/IsaacSim.git
  ```
- Isaac Sim을 소스 코드에서 빌드합니다:

  ### Linux

  ```bash
  cd IsaacSim
  ./build.sh
  ```

  ### Windows

  ```bash
  cd IsaacSim
  build.bat
  ```

### Isaac Sim 설치 확인

Isaac Sim 설치 디렉토리를 매번 찾고 위치를 파악하는 오버헤드를 피하기 위해, 다음 환경 변수를 터미널에 내보내는 것을 권장합니다. 이렇게 하면 남은 설치 과정에서 이 변수들을 사용할 수 있습니다:

### Linux

```bash
# Isaac Sim 루트 디렉토리
export ISAACSIM_PATH="${pwd}/_build/linux-x86_64/release"
# Isaac Sim Python 실행 파일
export ISAACSIM_PYTHON_EXE="${ISAACSIM_PATH}/python.sh"
```

### Windows

```batch
:: Isaac Sim 루트 디렉토리
set ISAACSIM_PATH="%cd%\_build\windows-x86_64\release"
:: Isaac Sim Python 실행 파일
set ISAACSIM_PYTHON_EXE="%ISAACSIM_PATH:"=%\python.bat"
```

시뮬레이터가 예상대로 실행되는지 확인합니다:

### Linux

```bash
# 참고: "--help" 인수를 전달하여 가능한 모든 인수를 볼 수 있습니다.
${ISAACSIM_PATH}/isaac-sim.sh
```

### Windows

```batch
:: 참고: "--help" 인수를 전달하여 가능한 모든 인수를 볼 수 있습니다.
%ISAACSIM_PATH%\isaac-sim.bat
```

독립형 Python 스크립트에서 시뮬레이터가 실행되는지 확인합니다:

### Linux

```bash
# Python 경로가 올바르게 설정되었는지 확인
${ISAACSIM_PYTHON_EXE} -c "print('Isaac Sim configuration is now complete.')"
# Isaac Sim을 Python에서 실행할 수 있는지 확인
${ISAACSIM_PYTHON_EXE} ${ISAACSIM_PATH}/standalone_examples/api/isaacsim.core.api/add_cubes.py
```

### Windows

```batch
:: Python 경로가 올바르게 설정되었는지 확인
%ISAACSIM_PYTHON_EXE% -c "print('Isaac Sim configuration is now complete.')"
:: Isaac Sim을 Python에서 실행할 수 있는지 확인
%ISAACSIM_PYTHON_EXE% %ISAACSIM_PATH%\standalone_examples\api\isaacsim.core.api\add_cubes.py
```

위 지침을 따라 시뮬레이터가 실행되지 않거나崩溃하는 경우, 무언가가 잘못 구성되었다는 뜻입니다.
디버그 및 문제 해결을 위해 Isaac Sim
[문서](https://docs.omniverse.nvidia.com/dev-guide/latest/linux-troubleshooting.html)와
[Isaac Sim 포럼](https://docs.isaacsim.omniverse.nvidia.com/latest/common/feedback.html)을 확인하십시오.

## Isaac Lab 설치

### Isaac Lab 클론

#### 참고
프로젝트에 기여하기 위해 Isaac Lab 저장소를 [포크](https://github.com/isaac-sim/IsaacLab/fork)하는 것을 권장하지만, 프레임워크를 사용하기 위해서는 필수가 아닙니다.
포크를 만든 경우, 다음 지침에서 `isaac-sim`을 자신의 사용자 이름으로 바꾸십시오.

워크스페이스에 Isaac Lab 저장소를 클론합니다:

### SSH

```bash
git clone git@github.com:isaac-sim/IsaacLab.git
```

### HTTPS

```bash
git clone https://github.com/isaac-sim/IsaacLab.git
```

Linux 및 Windows용으로 확장 기능을 관리하는 유틸리티를 제공하는 헬퍼 실행 파일 [isaaclab.sh](https://github.com/isaac-sim/IsaacLab/blob/main/isaaclab.sh)와 [isaaclab.bat](https://github.com/isaac-sim/IsaacLab/blob/main/isaaclab.bat)를 제공합니다.

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

설치된 Isaac Sim 루트 폴더와 Isaac Lab 디렉토리의 `_isaac_sim` 사이에 심볼릭 링크를 설정합니다.这样可以方便地索引 Isaac Sim과 함께 제공되는 확장 기능의 Python 모듈。

### Linux

```bash
# 클론된 저장소로 이동
cd IsaacLab
# 심볼릭 링크 생성
ln -s ${ISAACSIM_PATH} _isaac_sim

# 예를 들어:
# 옵션 1: 사전 빌드된 바이너리가 설치된 경우:
# ln -s ${HOME}/isaacsim _isaac_sim
#
# 옵션 2: Isaac Sim이 소스 코드에서 빌드된 경우:
# ln -s ${HOME}/IsaacSim/_build/linux-x86_64/release _isaac_sim
```

### Windows

```batch
:: 클론된 저장소로 이동
cd IsaacLab
:: 관리자 권한으로 명령 프롬프트를 실행해야 하는 심볼릭 링크 생성
mklink /D _isaac_sim %ISAACSIM_PATH%

:: 예를 들어:
:: 옵션 1: 사전 빌드된 바이너리가 설치된 경우:
:: mklink /D _isaac_sim C:\isaacsim
::
:: 옵션 2: Isaac Sim이 소스 코드에서 빌드된 경우:
:: mklink /D _isaac_sim C:\IsaacSim\_build\windows-x86_64\release
```

### Python 환경 설정 (선택 사항)

#### 주의
이 단계는 선택 사항입니다. Isaac Sim과 함께 제공되는 Python을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

Isaac Lab용 전용 Python 환경을 만드는 것은 선택 사항이지만 **강력히 권장**됩니다. 가상 환경을 사용하면 다음과 같은 이점이 있습니다:

- **시스템 Python 또는 다른 프로젝트와의 충돌 방지**
- **의존성 izolated 관리** — 다른 프로젝트의 패키지 업그레이드나 실험으로 인해 Isaac Sim이 손상되지 않음
- **다양한 종속성 버전을 가진 여러 환경 쉽게 관리**
- **재현성 간소화** — 현재 프로젝트에 필요한 패키지만 포함되어 있어 동료와 설정을 공유하거나 다른 머신에서 실행하기 쉬움

가상 환경을 만들기 위해 다양한 패키지 관리자를 선택할 수 있습니다.

- **UV**: 현대적이고 빠르며 안전한 Python용 패키지 관리자
- **Conda**: 크로스플랫폼, 언어에 구애받지 않는 Python용 패키지 관리자

생성된 후에는 *./isaaclab.sh -p* 또는 *isaaclab.bat -p* 대신 가상 환경의 기본 Python (*python* 또는 *python3*)을 사용할 수 있습니다.

### Conda 환경

Conda를 설치하려면 [여기](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html)의 지침을 따르십시오.
다음 명령을 사용하여 Isaac Lab 환경을 만들 수 있습니다.

[Miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main/)를 사용하는 것을 권장합니다. 이는 가볍고 리소스 효율적인 환경 관리 시스템이기 때문입니다.

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

### UV 환경 (실험용)

`uv`를 설치하려면 다음 지침을 따라주세요: [여기](https://docs.astral.sh/uv/getting-started/installation/).  
다음 명령을 사용하여 Isaac Lab 환경을 생성할 수 있습니다:

### Linux

```bash
# 옵션 1: 기본 환경 이름 'env_isaaclab'
./isaaclab.sh --uv  # 또는 "./isaaclab.sh -u"
# 옵션 2: 사용자 지정 이름
./isaaclab.sh --uv my_env  # 또는 "./isaaclab.sh -u my_env"
```

```bash
# 환경 활성화
source ./env_isaaclab/bin/activate  # 또는 "source ./my_env/bin/activate"
```

### Windows

#### 경고
현재 Windows에서 UV 지원을 제공하지 않습니다. 진행 상황을 추적하려면  
[issue #3483](https://github.com/isaac-sim/IsaacLab/issues/3438)을 확인해주세요.

가상 환경에 진입한 후에는 `./isaaclab.sh -p` 또는  
`isaaclab.bat -p`를 사용해 Python 스크립트를 실행할 필요가 없습니다.  
환경의 기본 Python 실행 파일을 사용하려면 `python` 또는 `python3`를 실행하면 됩니다.  
그러나 나머지 문서에서는 `./isaaclab.sh -p` 또는 `isaaclab.bat -p`를  
사용해 Python 스크립트를 실행한다고 가정하겠습니다.

### 설치

- Linux에서만 `apt`를 사용하여 종속성을 설치하세요:
  ```bash
  # robomimic이 Windows에서 사용할 수 없으므로 필요한 종속성입니다
  sudo apt install cmake build-essential
  ```
- `source` 디렉터리의 모든 확장을 반복하여 `--editable` 플래그로  
  pip를 사용해 설치하는 명령어를 실행하세요:

  ### Linux

  ```bash
  ./isaaclab.sh --install  # 또는 "./isaaclab.sh -i"
  ```

  ### Windows

  ```batch
  isaaclab.bat --install :: 또는 "isaaclab.bat -i"
  ```

  기본적으로 위의 명령은 **모든** 학습 프레임워크를 설치합니다. 여기에는  
  `rl_games`, `rsl_rl`, `sb3`, `skrl`, `robomimic`이 포함됩니다.

  특정 프레임워크만 설치하려면 프레임워크 이름을 인수로 전달하면 됩니다.  
  예를 들어 `rl_games` 프레임워크만 설치하려면 다음을 실행하세요:

  ### Linux

  ```bash
  ./isaaclab.sh --install rl_games  # 또는 "./isaaclab.sh -i rl_games"
  ```

  ### Windows

  ```batch
  isaaclab.bat --install rl_games :: 또는 "isaaclab.bat -i rl_games"
  ```

  유효한 옵션은 `all`, `rl_games`, `rsl_rl`, `sb3`, `skrl`, `robomimic`,  
  그리고 `none`입니다. `none`을 전달하면 학습 프레임워크가 하나도 설치되지 않습니다.

### Isaac Lab 설치 확인

설치가 성공했는지 확인하려면 저장소 루트에서 다음 명령을 실행하세요:

### Linux

```bash
# 옵션 1: isaaclab.sh 실행 파일 사용
# 주의: 이는 번들된 Python과 가상 환경 모두에서 작동합니다
./isaaclab.sh -p scripts/tutorials/00_sim/create_empty.py

# 옵션 2: 가상 환경의 Python 사용
python scripts/tutorials/00_sim/create_empty.py
```

### Windows

```batch
:: 옵션 1: isaaclab.bat 실행 파일 사용
:: 주의: 이는 번들된 Python과 가상 환경 모두에서 작동합니다
isaaclab.bat -p scripts\tutorials\00_sim\create_empty.py

:: 옵션 2: 가상 환경의 Python 사용
python scripts\tutorials\00_sim\create_empty.py
```

위 명령은 시뮬레이터를 실행하고 검은 뷰포트 창을 표시해야 합니다.  
터미널에서 `Ctrl+C`를 눌러 스크립트를 종료할 수 있습니다.  
Windows 기계에서는 명령 프롬프트에서 `Ctrl+Break` 또는 `Ctrl+fn+B`를  
사용해 프로세스를 종료하세요.

![검은 창이 표시된 시뮬레이터](../../_static/setup/verify_install.jpg)

이를 보았다면 설치가 성공한 것입니다! 🎉

#### 주의
`ModuleNotFoundError: No module named 'isaacsim'` 오류가 표시되면,  
가상 환경이 활성화되어 있고 `source _isaac_sim/setup_conda_env.sh`가  
실행되었는지 확인하세요 (UV 사용 시에도 마찬가지입니다).

### 로봇 훈련하기!

이제 Isaac Lab을 사용하여 강화 학습으로 로봇을 훈련할 수 있습니다!  
Isaac Lab을 가장 빠르게 사용하는 방법은 **Batteries-included** 로봇 작업 중 하나를  
사용하는 사전 정의된 워크플로를 이용하는 것입니다. 다음 명령을 실행하여  
개미가 빠르게 걷도록 빠르게 훈련시켜 보세요!  
더 빠른 훈련을 위해 `--headless` 옵션을 추가하는 것을 권장합니다.

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

Isaac Lab은 프로젝트 요구 사항에 맞춘 나만의 **태스크**와 **워크플로**를  
생성하는 데 필요한 도구를 제공합니다. 다음 [방법 가이드](../../how-to/index.md#how-to)를  
참조해주세요: [자신만의 학습 라이브러리 추가](../../how-to/add_own_library.md#how-to-add-library) 또는  
[환경 래핑](../../how-to/wrap_rl_env.md#how-to-env-wrappers)에 대한 자세한 내용은 여기에서 확인할 수 있습니다.

![여유로운 손들...](../../_static/setup/isaac_ants_example.jpg)
