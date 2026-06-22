# 설치

Newton 물리 통합 브랜치를 설치하려면 다음 세 가지가 필요합니다:

1. Isaac Lab의 `feature/newton` 브랜치
2. Ubuntu 22.04 또는 24.04 (Windows는 곧 지원 예정)
3. [선택 사항] Isaac Sim 5.1 (옴니버스 시각화 도구를 사용하지 않는 경우 Isaac Sim이 필요하지 않음)

시작하려면 시뮬레이션 앱을 실행했을 때 생성된 창의 제목에서 Isaac Sim 버전을 확인하세요. 또는 앱 내 `Help → About` 메뉴에서 더 구체적인 버전 정보를 확인할 수 있습니다.
버전이 5.1 미만이라면 먼저 [Isaac Sim을 업데이트하거나 재설치](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/quick-install.html)해야 다음 단계로 진행할 수 있습니다.

다음으로, 로컬 Isaac Lab 저장소의 루트 디렉터리로 이동하여 터미널을 엽니다.

다음 명령을 실행하여 `feature/newton` 브랜치에 있는지 확인하세요:

```bash
git checkout feature/newton
```

다음은 pip을 통해 Isaac Sim을 설치하는 방법을 설명합니다.

## pip 설치

Python 환경을 관리하기 위해 conda 사용을 권장합니다. conda는 [여기서](https://docs.conda.io/en/latest/miniconda.html) 다운로드 및 설치할 수 있습니다.

이전에 Isaac Lab용 가상 환경을 이미 만들었다면, 의존성 충돌을 방지하기 위해 처음부터 새로운 환경으로 시작하는 것이 좋습니다.
이전에 pip으로 `mujoco`, `mujoco-warp`, 또는 `newton` 패키지를 설치한 경우, 먼저 `pip cache purge`를 실행하여 캐시를 정리하고 이전 버전으로 인한 충돌을 방지하는 것이 좋습니다.

새 conda 환경을 생성합니다:

```bash
conda create -n env_isaaclab python=3.11
```

환경을 활성화합니다:

```bash
conda activate env_isaaclab
```

torch와 torchvision의 올바른 버전을 설치합니다:

```bash
pip install -U torch==2.7.0 torchvision==0.22.0 --index-url https://download.pytorch.org/whl/cu128
```

[선택 사항] Isaac Sim 5.1을 설치합니다:

```bash
pip install "isaacsim[all,extscache]==5.1.0" --extra-index-url https://pypi.nvidia.com
```

Isaac Lab 확장 프로그램과 종속성을 설치합니다:

```bash
./isaaclab.sh -i
```

## 설치 테스트

설치가 성공했는지 확인하려면 Isaac Lab 저장소 루트 디렉터리에서 다음 명령을 실행하세요:

```bash
./isaaclab.sh -p scripts/environments/zero_agent.py --task Isaac-Cartpole-Direct-v0 --num_envs 128
```

Newton은 Isaac Sim 5.1보다 더 최신 버전의 Warp를 요구하기 때문에, `ModuleNotFoundError: No module named 'warp.sim'`과 같은 오류가 발생할 수 있습니다.
이런 오류는 무시해도 괜찮으며, 사용성에 영향을 주지 않습니다.
