# 리포지토리 구성

```bash
IsaacLab
├── .vscode
├── CONTRIBUTING.md
├── CONTRIBUTORS.md
├── LICENSE
├── isaaclab.bat
├── isaaclab.sh
├── pyproject.toml
├── README.md
├── docs
├── docker
├── source
│   ├── isaaclab
│   ├── isaaclab_assets
│   ├── isaaclab_mimic
│   ├── isaaclab_rl
│   └── isaaclab_tasks
├── scripts
│   ├── benchmarks
│   ├── demos
│   ├── environments
│   ├── imitation_learning
│   ├── reinforcement_learning
│   ├── tools
│   ├── tutorials
├── tools
└── VERSION
```

Isaac Lab은 Isaac Sim과 동일한 백엔드를 기반으로 구축되었습니다. 따라서 **확장 프로그램**(extensions)으로 존재하며, 이를 조립하여 **애플리케이션**(applications)을 만들 수 있습니다.
`source` 디렉토리는 저장소의 대부분의 코드와 Isaac Lab을 구성하는 특정 확장 프로그램을 포함하며, `scripts` 디렉토리는 맞춤형 독립 실행형 앱을 실행하는 파이썬 스크립트를 포함합니다(예: 워크플로우).
이 두 가지 방법이 시뮬레이션과 Isaac Lab을 사용하는 주요 방법이며, 두 가지 모두 지원됩니다.
자세한 내용은 [Isaac Sim 워크플로우 소개](https://docs.isaacsim.omniverse.nvidia.com/latest/introduction/workflows.html)를 참조하세요.

## 확장 프로그램

Isaac Lab을 구성하는 확장 프로그램은 `source` 디렉토리에 저장됩니다. 빌드 프로세스를 간소화하기 위해 Isaac Lab은 직접 [setuptools](https://setuptools.readthedocs.io/en/latest/)를 사용합니다. Isaac Lab을 사용하여 자체 확장 프로그램을 생성하는 경우 이 프로세스를 따르는 것이 강력히 권장됩니다.

확장 프로그램은 다음과 같이 구성됩니다:

* **isaaclab**: Isaac Lab의 핵심 인터페이스 확장 프로그램을 포함합니다. 이는 액추에이터, 객체, 로봇 및 센서의 주요 모듈을 제공합니다.
* **isaaclab_assets**: Isaac Lab용 사전 구성된 자산을 포함하는 확장 프로그램을 포함합니다.
* **isaaclab_tasks**: Isaac Lab용 사전 구성된 환경을 포함하는 확장 프로그램을 포함합니다.
* **isaaclab_mimic**: 모방 학습을 위한 데이터 생성에 사용되는 API 및 사전 구성된 환경을 포함합니다.
* **isaaclab_rl**: 위의 환경을 다양한 강화 학습 에이전트와 함께 사용하기 위한 래퍼를 포함합니다.

## 독립 실행형

`scripts` 디렉토리에는 파이썬으로 작성된 다양한 독립 실행형 애플리케이션이 포함되어 있습니다.
이들은 다음과 같이 구성됩니다:

* **benchmarks**: 다양한 프레임워크 구성 요소를 벤치마크하는 스크립트를 포함합니다.
* **demos**: 핵심 프레임워크 `isaaclab`를 보여주는 다양한 데모 애플리케이션을 포함합니다.
* **environments**: `isaaclab_tasks`에 정의된 환경을 실행하는 애플리케이션을 포함합니다. 여기에는 무작위 정책, 제로액션 정책, 원격 조작 또는 스크립트 기반 상태 기계가 포함됩니다.
* **tools**: 프레임워크에서 제공하는 도구를 사용하는 애플리케이션을 포함합니다. 여기에는 자산 변환, 데이터 세트 생성 등이 포함됩니다.
* **tutorials**: 프레임워크에서 제공하는 API를 사용하는 단계별 튜토리얼을 포함합니다.
* **workflows**: 다양한 학습 기반 프레임워크와 환경을 사용하는 애플리케이션을 포함합니다. 여기에는 다양한 강화 학습 또는 모방 학습 라이브러리가 포함됩니다.
