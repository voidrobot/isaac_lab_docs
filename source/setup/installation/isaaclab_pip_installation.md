# Isaac Lab Pip 패키지를 사용한 설치

Isaac Lab 2.0부터 pip 패키지를 제공하여 pip를 통해 Isaac Sim과 Isaac Lab 확장을 모두 설치할 수 있습니다.
이 설치 프로세스는 Isaac Lab을 기반으로 추가 확장 프로젝트를 작업하는 고급 사용자에게만 권장된다는 점을 유의하세요.
Isaac Lab pip 패키지에는 **독립형** Python 스크립트(학습, 추론, 데모 및 예와 같은 독립형 워크플로 실행 포함)가 포함되지 **않습니다**.
따라서 pip로 Isaac Lab을 설치할 때는 자체 실행 스크립트를 정의해야 합니다.

Isaac Lab 위에 자체 프로젝트를 설정하는 방법에 대해 알아보려면 [새 프로젝트 또는 작업 생성](../overview/own-project/template.md#template-generator)을 참조하세요.

#### 참고
현재 Isaac Lab의 각 메이저 릴리스에 대해서만 pip 패키지를 제공합니다.
예를 들어 릴리스 2.1.0과 2.2.0용 pip 패키지는 제공하지만 2.1.1용은 제공하지 않습니다.
앞으로는 Isaac Lab의 각 마이너 릴리스에 대해 pip 패키지를 제공할 예정입니다.

## Python 환경 준비

전용 Python 환경을 만드는 것이 **강력히 권장**됩니다. 이는 다음과 같은 이점을 제공합니다.

- **시스템 Python 또는 머신에 설치된 다른 프로젝트와의 충돌 방지**
- **의존성 격리 유지**하여 다른 프로젝트의 패키지 업그레이드 또는 실험으로 인해 Isaac Sim이 손상되지 않음
- **의존성 버전이 다른 여러 환경 쉽게 관리**
- **재현성 간소화** — 환경에는 현재 프로젝트에 필요한 패키지만 포함되어 동료와 설정을 공유하거나 다른 기계에서 실행하기 쉬워집니다.

다양한 패키지 관리자를 선택하여 가상 환경을 만들 수 있습니다.

- **UV**: 빠르고 안전하며 최신 Python용 패키지 관리자
- **Conda**: 크로스 플랫폼, 언어에 구애받지 않는 Python용 패키지 관리자
- **venv**: Python에서 가상 환경을 만드는 표준 라이브러리

다음 지침은 Python 3.11이 필요한 Isaac Sim 5.X용입니다.
Isaac Sim 4.5를 설치하려는 경우 Python 3.10을 사용하도록 지침을 entsprechend 수정하세요.

- 다음 중 하나의 패키지 관리자를 사용하여 가상 환경을 만듭니다:

  ### Conda 환경

  conda를 설치하려면 [여기](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html)의 지침을 따르세요.
  다음 명령을 사용하여 Isaac Lab 환경을 생성할 수 있습니다.

  경량이고 리소스 효율적인 환경 관리 시스템인 [Miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main/) 사용을 권장합니다.
  ```bash
  conda create -n env_isaaclab python=3.11
  conda activate env_isaaclab
  ```

  ### venv 환경

  표준 라이브러리를 사용하여 가상 환경을 만들려면 다음 명령을 사용할 수 있습니다.

  ### Linux

  ```bash
  # python3.11을 사용하여 env_isaaclab이라는 가상 환경 생성
  python3.11 -m venv env_isaaclab
  # 가상 환경 활성화
  source env_isaaclab/bin/activate
  ```

  ### Windows

  ```batch
  :: python3.11을 사용하여 env_isaaclab이라는 가상 환경 생성
  python3.11 -m venv env_isaaclab
  :: 가상 환경 활성화
  env_isaaclab\Scripts\activate
  ```

  ### UV 환경 (실험적)

  `uv`를 설치하려면 [여기](https://docs.astral.sh/uv/getting-started/installation/)의 지침을 따르세요.

  #### 참고
  `uv venv`로 생성된 가상 환경에는 **pip가 포함되지 않습니다**.
  Isaac Lab 설치를 위해서는 `pip`가 필요하므로 환경을 활성화한 후 수동으로 설치해야 합니다.

  다음 명령을 사용하여 Isaac Lab 환경을 생성할 수 있습니다.

  ### Linux

  ```bash
  # python3.11과 pip를 사용하여 env_isaaclab이라는 가상 환경 생성
  uv venv --python 3.11 --seed env_isaaclab
  # 가상 환경 활성화
  source env_isaaclab/bin/activate
  ```

  ### Windows

  ```batch
  :: python3.11과 pip를 사용하여 env_isaaclab이라는 가상 환경 생성
  uv venv --python 3.11 --seed env_isaaclab
  :: 가상 환경 활성화
  env_isaaclab\Scripts\activate
  ```
- 최신 pip 버전이 설치되어 있는지 확인합니다. 가상 환경 내부에서 다음 명령을 실행하여 pip를 업데이트합니다.

  ### Linux

  ```bash
  pip install --upgrade pip
  ```

  ### Windows

  ```batch
  python -m pip install --upgrade pip
  ```

## 종속성 설치

#### 참고
가상 환경을 생성하는 데 UV를 사용한 경우, 다음 명령에서 `pip`를 `uv pip`로 대체해 주세요.

- Isaac Lab 패키지와 Isaac Sim을 함께 설치합니다:
  ```none
  pip install isaaclab[isaacsim,all]==2.3.2 --extra-index-url https://pypi.nvidia.com
  ```
- 시스템 아키텍처에 맞는 CUDA 12.8용 CUDA 지원 PyTorch 2.7.0 빌드를 설치합니다:

  ### Linux (x86_64)

  ```bash
  pip install -U torch==2.7.0 torchvision==0.22.0 --index-url https://download.pytorch.org/whl/cu128
  ```

  ### Windows (x86_64)

  ```bash
  pip install -U torch==2.7.0 torchvision==0.22.0 --index-url https://download.pytorch.org/whl/cu128
  ```

  ### Linux (aarch64)

  ```bash
  pip install -U torch==2.9.0 torchvision==0.24.0 --index-url https://download.pytorch.org/whl/cu130
  ```

  #### 참고
  aarch64에서 Isaac Lab을 설치한 후 다음과 같은 경고가 나타날 수 있습니다:
  ```none
  ERROR: ld.so: object '...torch.libs/libgomp-XXXX.so.1.0.0' cannot be preloaded: ignored.
  ```

  이는 시스템과 PyTorch의 `libgomp`(GNU OpenMP) 라이브러리가 모두 사전 로드될 때 발생합니다.
  Isaac Sim은 **시스템** OpenMP 런타임을 기대하는 반면, PyTorch는 때때로 자체 라이브러리를 번들로 제공합니다.

  이 문제를 해결하려면 기존 `LD_PRELOAD`를 해제하고 시스템 라이브러리만 사용하도록 설정하세요:
  ```bash
  unset LD_PRELOAD
  export LD_PRELOAD="$LD_PRELOAD:/lib/aarch64-linux-gnu/libgomp.so.1"
  ```

  이렇게 하면 Isaac Sim과 Isaac Lab 모두에 올바른 `libgomp` 라이브러리가 사전 로드되어 런타임 중 사전 로드 경고가 제거됩니다.
- `rl_games`를 사용하여 학습 및 추론을 수행하려면 Python 3.11용 포크를 설치합니다:
  ```none
  pip install git+https://github.com/isaac-sim/rl_games.git@python3.11
  ```

## Isaac Sim 설치 확인

- 가상 환경이 활성화되어 있는지 확인합니다(해당되는 경우)
- 시뮬레이터가 예상대로 실행되는지 확인합니다:
  ```bash
  # 참고: "--help" 인수를 전달하여 가능한 모든 인수를 확인할 수 있습니다.
  isaacsim
  ```
- 특정 경험 파일로 실행하는 것도 가능합니다:
  ```bash
  # 경험 파일은 절대 경로일 수도 있고, isaacsim/apps 또는 omni/apps에서 검색하는 상대 경로일 수도 있습니다.
  isaacsim isaacsim.exp.full.kit
  ```

#### 참고
Isaac Sim을 처음 실행할 때 모든 의존 확장 프로그램이 레지스트리에서 다운로드됩니다.
이 프로세스는 각 경험 파일의 첫 실행 시 최대 10분 이상 소요될 수 있으며 필수입니다.
확장 프로그램이 다운로드되면 동일한 경험 파일을 사용하는 연속 실행에서는 캐시된 확장 프로그램을 사용합니다.

#### 주의
첫 실행 시 Nvidia Omniverse 라이센스 계약서 수락 프롬프트가 표시됩니다.
EULA를 수락하려면 아래 메시지가 표시될 때 `Yes`라고 답변하세요:

```bash
Isaac Sim을 설치하거나 사용하는 것은 NVIDIA OMNIVERSE 라이센스 계약서(EULA)의 조건에 동의함을 의미합니다.
https://docs.isaacsim.omniverse.nvidia.com/latest/common/NVIDIA_Omniverse_License_Agreement.html

EULA를 수락하십니까? (Yes/No): Yes
```

위의 지침을 따르는 동안 시뮬레이터가 실행되지 않거나 충돌이 발생한다면 잘못된 구성이 있다는 의미입니다.
디버그 및 문제 해결을 위해서는 Isaac Sim
[문서](https://docs.omniverse.nvidia.com/dev-guide/latest/linux-troubleshooting.html)와
[Isaac Sim 포럼](https://docs.isaacsim.omniverse.nvidia.com/latest/common/feedback.html)을 참조하세요.

## Isaac Lab 스크립트 실행

위의 스크립트를 따른 경우, 이제 Python 환경에 모든 Isaac Lab 확장이 포함되어야 합니다.
Isaac Lab용 사용자 정의 스크립트를 실행하려면 단순히 다음을 실행하면 됩니다:

```bash
python my_awesome_script.py
```

## VS Code 설정 생성

설치로 인한 구조 때문에 VS Code IntelliSense(코드 완성, 매개변수 정보 및 멤버 목록 등)가 기본적으로 작동하지 않습니다.
워크스페이스 폴더에 대해 설정 경로(import 해결을 위한), 기본 Python 해석기 경로 및 기타 설정을 정의하려면 다음 명령을 실행합니다:

```bash
python -m isaaclab --generate-vscode-settings
```

#### 경고
이 명령은 작업 공간 폴더에 `.vscode/settings.json` 파일을 생성합니다.
파일이 이미 존재한다면 덮어쓰게 되며(먼저 확인 프롬프트가 표시됨).
