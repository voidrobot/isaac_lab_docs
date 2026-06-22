# Python 환경 준비

전용 Python 환경을 만드는 것은 **강력히 권장**됩니다. 이렇게 하면:

- **시스템 Python이나 다른 프로젝트와의 충돌 방지**
- **의존성 격리 유지**, 그래서 다른 프로젝트의 패키지 업그레이드나 실험이 Isaac Sim을 손상시키지 않음
- **다양한 종속성 버전으로 설정을 위한 여러 환경 간편 관리**
- **재현성 간소화** — 현재 프로젝트에 필요한 패키지만 포함된 환경으로, 동료와 설정 공유나 다른 기기에서 실행이 쉬워짐

가상 환경을 만들기 위해 다양한 패키지 관리자를 선택할 수 있습니다.

- **UV**: 파이썬을 위한 현대적이고 빠르며 안전한 패키지 관리자
- **Conda**: 파이썬을 위한 크로스 플랫폼, 언어에 구애받지 않는 패키지 관리자
- **venv**: 파이썬에서 가상 환경을 만드는 표준 라이브러리

다음 지침은 Python 3.11이 필요한 Isaac Sim 5.X용입니다.
Isaac Sim 4.5를 설치하려면 Python 3.10을 사용하도록 지침을 수정하세요.

- 다음 중 하나의 패키지 관리자를 사용하여 가상 환경을 생성합니다:

  ### Conda 환경

  conda를 설치하려면 [여기](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html)의 지침을 따르세요.
  다음 명령으로 Isaac Lab 환경을 만들 수 있습니다.

  가벼우면서 리소스를 효율적으로 사용하는 환경 관리 시스템인 [Miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main/) 사용을 권장합니다.
  ```bash
  conda create -n env_isaaclab python=3.11
  conda activate env_isaaclab
  ```

  ### venv 환경

  표준 라이브러리를 사용하여 가상 환경을 만들려면 다음 명령어를 사용하세요:

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

  #### 주의 사항
  `uv venv`로 만든 가상 환경에는 `pip`가 **포함되지 않습니다**.
  Isaac Lab 설치가 `pip`를 필요로 하므로, 환경을 활성화한 후 수동으로 설치해야 합니다.

  다음 명령으로 Isaac Lab 환경을 만들 수 있습니다:

  ### Linux

  ```bash
  # python3.11과 pip을 사용하여 env_isaaclab이라는 가상 환경 생성
  uv venv --python 3.11 --seed env_isaaclab
  # 가상 환경 활성화
  source env_isaaclab/bin/activate
  ```

  ### Windows

  ```batch
  :: python3.11과 pip을 사용하여 env_isaaclab이라는 가상 환경 생성
  uv venv --python 3.11 --seed env_isaaclab
  :: 가상 환경 활성화
  env_isaaclab\Scripts\activate
  ```
- 최신 pip 버전이 설치되어 있는지 확인하세요. 가상 환경 내부에서 다음 명령을 실행하여 pip를 업데이트할 수 있습니다:

  ### Linux

  ```bash
  pip install --upgrade pip
  ```

  ### Windows

  ```batch
  python -m pip install --upgrade pip
  ```
