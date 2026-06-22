<a id="sim2real"></a>

# 시뮬레이션-실제 정책 전이

시뮬레이션에서 실제 로봇으로 정책을 배포하는 것은 중요한 세부 사항들을 해결해야 하는 작업입니다.
이 섹션에서는 실제 Unitree G1 로봇에 배포할 수 있는 정책을 훈련하기 위한 높은 수준의 가이드를 제공합니다.
주요 과제는 시뮬레이션에서 사용 가능한 모든 관측값이 실제 로봇 센서로 직접 측정될 수 없다는 점입니다.
즉, 센서에서 사용 가능한 관측값만 사용하는 정책이 아니라면 RL로 훈련된 정책은 직접 배포할 수 없습니다. 예를 들어, 실제 로봇 IMU 센서는 각 가속도(이를 적분하여 각속도를 구할 수 있음)를 제공하지만 선속도는 직접 측정할 수 없습니다. 따라서 훈련 중에 정책이 기반 선속도에 의존한다면, 실제 로봇 배포 전에 이 정보를 제거해야 합니다.

## 요구 사항

이 워크플로우의 정책은 먼저 시뮬레이션-시뮬레이션 전이를 통해 검증된 후 실제 로봇 배포를 진행한다고 가정합니다.
자세한 내용은 [여기](sim-to-sim.md#sim2sim)를 참조하세요.

### 개요

이 섹션에서는 Newton 백엔드를 사용한 Unitree G1 속도 추적 작업에 대한 교사-학생 증류 워크플로우를 시연합니다.

교사-학생 증류 워크플로우는 다음과 세 단계로 구성됩니다:

1. 실제 세계 센서에서 사용할 수 없는 특권 관측값을 사용한 교사 정책 훈련
2. 교사 정책의 행동 클로닝을 통해 특권 항목(예: 기반 선속도)을 제거한 학생 정책 증류
3. 실제 센서 관측값만을 사용한 RL을 통한 학생 정책 미세 조정

교사 및 학생 관측 그룹은 속도 작업 구성에 구현되어 있습니다. 자세한 내용은 다음 소스를 참고하세요:

- 교사 관측값: `PolicyCfg(ObsGroup)` in [velocity_env_cfg.py](https://github.com/isaac-sim/IsaacLab/blob/main/source/isaaclab_tasks/isaaclab_tasks/manager_based/locomotion/velocity/velocity_env_cfg.py)
- 학생 관측값: `StudentPolicyCfg(ObsGroup)` in [velocity_env_cfg.py](https://github.com/isaac-sim/IsaacLab/blob/main/source/isaaclab_tasks/isaaclab_tasks/manager_based/locomotion/velocity/velocity_env_cfg.py)

## 1. 교사 정책 훈련

Newton 백엔드를 사용하여 G1 속도 작업에 대한 교사 정책을 훈련합니다. 작업 ID는 `Isaac-Velocity-Flat-G1-v1`입니다.

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Velocity-Flat-G1-v1 --num_envs=4096
```

교사 정책은 [velocity_env_cfg.py](https://github.com/isaac-sim/IsaacLab/blob/main/source/isaaclab_tasks/isaaclab_tasks/manager_based/locomotion/velocity/velocity_env_cfg.py)에서 정의된 `PolicyCfg(ObsGroup)`에 특권 관측값(예: 기반 선속도)을 포함합니다.

## 2. 학생 정책 증류(특권 항목 제거)

증류 과정에서 학생 정책은 교사 정책의 행동을 모방하기 위해 행동 클로닝을 수행하며, 그들의 행동 간 평균 제곱 오차를 최소화합니다: $loss = MSE(\pi(O_{teacher}), \pi(O_{student}))$.

학생 정책은 실제 센서에서 사용 가능한 관측값만 사용합니다(참고: [velocity_env_cfg.py](https://github.com/isaac-sim/IsaacLab/blob/main/source/isaaclab_tasks/isaaclab_tasks/manager_based/locomotion/velocity/velocity_env_cfg.py)의 `StudentPolicyCfg(ObsGroup)`). 구체적으로: **기반 각속도**와 **투영된 중력**은 IMU 센서에서 제공하며, **관절 위치와 속도**는 관절 엔코더에서 제공하고, **액션**은 컨트롤러가 적용하는 관절 토크입니다.

학생 증류 작업 `Velocity-G1-Distillation-v1`을 실행하여 `--load_run` 및 `--checkpoint`로 증류하고자 하는 교사 정책을 지정합니다.

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task=Velocity-G1-Distillation-v1 --num_envs=4096 --load_run 2025-08-13_23-53-28 --checkpoint model_1499.pt
```

#### 참고
올바른 `--load_run` 및 `--checkpoint`를 사용하려는 교사 정책에서 증류가 올바르게 이루어지도록 합니다.

## 3. RL을 통한 학생 정책 미세 조정

`Velocity-G1-Student-Finetune-v1` 작업을 사용하여 RL로 증류된 학생 정책을 미세 조정합니다. `--load_run` 및 `--checkpoint`를 사용하여 증류된 정책에서 시작하도록 초기화합니다.

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task=Velocity-G1-Student-Finetune-v1 --num_envs=4096 --load_run 2025-08-20_16-06-52_distillation --checkpoint model_1499.pt
```

이 과정은 증류된 학생 정책에서 시작하여 RL 훈련을 통해 추가로 개선합니다.

#### 참고
`--load_run` 및 `--checkpoint`가 일반적으로 증류 단계의 가장 최신 체크포인트를 가리키는 올바른 초기 정책을 가리키는지 확인하세요.

다음 명령으로 학생 정책을 재생할 수 있습니다:

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task=Velocity-G1-Student-Finetune-v1 --num_envs=32 --visualizer newton
```

이 명령은 정책을 `.pt` 및 `.onnx` 파일로 실행 디렉터리의 export 폴더에 저장하여 실제 로봇 배포를 준비합니다.
