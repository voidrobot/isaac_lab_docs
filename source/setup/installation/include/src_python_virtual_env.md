# Python 환경 설정 (옵션)

#### 주의
이 단계는 선택 사항입니다. Isaac Sim에 번들로 제공된 Python을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

Isaac Lab용 전용 Python 환경을 만드는 것이 **강력히 권장**되지만, 이는 선택 사항입니다. 가상 환경을 사용하면 다음이 가능해집니다:

- **시스템 Python 또는 다른 프로젝트와의 충돌 방지**
- **의존성 격리 유지**, 이를 통해 다른 프로젝트의 패키지 업그레이드 또는 실험이 Isaac Sim을 손상시키지 않음
- **다양한 의존성 버전으로 환경 설정 시 여러 환경 쉽게 관리**
- **재현성 단순화** — 환경에는 현재 프로젝트에 필요한 패키지만 포함되어 동료와 설정을 공유하거나 다른 기계에서 실행하기 쉬워짐

가상 환경을 만들기 위해 다양한 패키지 관리자를 선택할 수 있습니다.

- **UV**: 빠르고 현대적이며 보안성이 높은 Python 패키지 관리자
- **Conda**: 플랫폼 간 및 언어에 구애받지 않는 Python 패키지 관리자

생성 후, *./isaaclab.sh -p* 또는 *isaaclab.bat -p* 대신 가상 환경의 기본 Python(*python* 또는 *python3*)을 사용할 수 있습니다.

### Conda 환경

Conda를 설치하려면 [여기](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html)의 지침을 따르세요.
다음 명령을 사용하여 Isaac Lab 환경을 생성할 수 있습니다.

경량이며 자원 효율적인 환경 관리 시스템인 [Miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main/) 사용을 권장합니다.

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

`uv`를 설치하려면 [여기](https://docs.astral.sh/uv/getting-started/installation/)의 지침을 따르세요.
다음 명령을 사용하여 Isaac Lab 환경을 생성할 수 있습니다.

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

#### 경고
Windows에서 UV 지원은 현재 사용할 수 없습니다. 진행 상황을 추적하려면 [이슈 #3483](https://github.com/isaac-sim/IsaacLab/issues/3438)을 확인하세요.

가상 환경에 진입한 후에는 `./isaaclab.sh -p` 또는 `isaaclab.bat -p`를 사용하여 Python 스크립트를 실행할 필요가 없습니다. 환경의 기본 Python 실행 파일(`python` 또는 `python3`)을 실행하여 사용할 수 있습니다. 그러나 나머지 문서에서는 `./isaaclab.sh -p` 또는 `isaaclab.bat -p`를 사용하여 Python 스크립트를 실행한다고 가정합니다.
