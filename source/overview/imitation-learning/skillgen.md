<a id="skillgen"></a>

# SkillGen를 통한 자동 데모 생성

SkillGen은 모션 플래닝을 통합하여 Isaac Lab Mimic을 향상시키는 고급 데모 생성 시스템입니다. 인간의 제공된 서브태스크 세그먼트와 자동 모션 플래닝을 결합하여 높은 품질의 적응형, 충돌이 없는 로봇 데모를 생성합니다.

## SkillGen이란?

SkillGen은 전통적인 데모 생성의 주요 한계를 해결합니다:

* **모션 품질**: cuRobo의 GPU 가속 모션 플래너를 사용하여 부드럽고 충돌이 없는 궤적 생성
* **유효성**: 스킬 세그먼트 사이의 운동학적으로 실현 가능한 플랜 생성
* **다양성**: 구성 가능한 샘플링 및 플래닝 매개변수를 통해 다양한 데모 생성
* **적응성**: 데이터 생성 중 새로운 객체 배치 및 씬 구성에 적응할 수 있는 데모 생성

이 시스템은 수동으로 주석이 달린 인간 데모를 취하고, 로컬라이즈된 서브태스크 스킬을 추출한 후( [SkillGen의 서브태스크]() 참조), cuRobo를 사용하여 이러한 스킬 세그먼트 사이에 실행 가능한 모션을 플래닝하면서 로봇 운동학 및 충돌 제약을 존중합니다.

## 전제 조건

SkillGen을 사용하기 전에 다음을 이해해야 합니다:

1. **텔레오퍼레이션**: 키보드, SpaceMouse 또는 손 추적을 사용하여 로봇을 제어하고 데모를 기록하는 방법
2. **Isaac Lab Mimic**: 데이터 수집, 주석 달기, 생성 및 정책 훈련을 포함하는 전체 워크플로우

#### IMPORTANT
SkillGen으로 진행하기 전에 [Isaac Lab Mimic을 사용한 텔레오퍼레이션 및 모방 학습](teleop_imitation.md#teleoperation-imitation-learning) 문서를 철저히 검토하세요.

<a id="skillgen-installation"></a>

## 설치

SkillGen은 Isaac Lab, Isaac Sim 및 cuRobo가 필요합니다. Isaac Lab conda 환경에서 다음 단계를 따르세요.

### 단계 1: Isaac Sim과 Isaac Lab 설치 및 확인

공식 Isaac Sim 및 Isaac Lab 설치 가이드를 따르세요 [여기](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/pip_installation.html#installing-isaac-lab).

### 단계 2: cuRobo 설치

cuRobo는 SkillGen에 모션 플래닝 기능을 제공합니다. 이 설치는 Isaac Lab의 PyTorch 및 CUDA 요구사항과 작동하도록 테스트되었습니다:

```bash
# 한 줄로 cuRobo 설치 (읽기 쉬움을 위해 포맷됨)
conda install -c nvidia cuda-toolkit=12.8 -y && \
export CUDA_HOME="$CONDA_PREFIX" && \
export PATH="$CUDA_HOME/bin:$PATH" && \
export LD_LIBRARY_PATH="$CUDA_HOME/lib:$LD_LIBRARY_PATH" && \
export TORCH_CUDA_ARCH_LIST="8.0+PTX" && \
pip install -e "git+https://github.com/NVlabs/curobo.git@ebb71702f3f70e767f40fd8e050674af0288abe8#egg=nvidia-curobo" --no-build-isolation
```

#### NOTE
* 커밋 해시 `ebb71702f3f70e767f40fd8e050674af0288abe8`은 Isaac Lab과 테스트되었으며, 다른 버전을 사용할 경우 호환성 문제가 발생할 수 있습니다. 이 커밋은 cuRobo가 USDs를 충돌 객체로 파싱하는 데 필요한 쿼드 페이스 메시 삼각분할을 지원합니다.
* cuRobo는 소스에서 설치되며 에디터블로 설치되었습니다. 즉, cuRobo 소스 코드가 현재 디렉터리 아래 `src/nvidia-curobo`에 클론됩니다. 사용자는 cuRobo 설치를 위해 작업 디렉터리를 선택할 수 있습니다.
* 위의 명령어에서 `TORCH_CUDA_ARCH_LIST`는 GPU의 CUDA 컴퓨트 기능과 일치해야 합니다(예: A100의 경우 `8.0`, 많은 RTX 30‑시리즈의 경우 `8.6`, RTX 4090의 경우 `8.9`); `+PTX` 접미사는 최신 GPU에서 네이티브 SASS가 포함되지 않은 경우 JIT 컴파일할 수 있도록 PTX를 삽입하여 향후 호환성을 제공합니다.

#### WARNING
**cuRobo 설치는 Isaac Sim 환경 스크립트가 소싱될 경우 실패할 수 있음**

옴니버스 키트/Isaac Sim 환경 스크립트 소싱(예: `setup_conda_env.sh`)은 `PYTHONHOME`과 `PYTHONPATH`를 키트 런타임 및 사전 번들 Python 패키지로 내보냅니다. cuRobo 설치 중에 이로 인해 `conda`가 옴니버스의 번들 라이브러리(예: `requests`/`urllib3`)를 초기화 전에 가져올 수 있어 충돌이 발생할 수 있음(종종 `omni.kit.pip_archive`을 참조하는 `TypeError`로 나타남).

다음 중 하나를 수행하세요:

- 옴니버스/Isaac Sim 스크립트를 소싱하지 않은 깨끗한 셸에서 cuRobo를 설치합니다.
- Conda를 호출하기 전에 상속된 Python 환경 변수(특히 `PYTHONPATH`와 `PYTHONHOME`)를 일시적으로 재설정하거나 무시하여 키트의 Python이 Conda 환경을 가리지 않도록 합니다.
- 셸의 현재 Python 변수를 상속하지 않도록 작동하는 Conda 메커니즘을 사용합니다.

설치가 완료되면 정상 사용을 위해 Isaac Lab/Isaac Sim 스크립트를 다시 소싱할 수 있습니다.

### 단계 3: Rerun 설치

개발 중 trajectory 시각화를 위해:

```bash
pip install rerun-sdk==0.23
```

#### NOTE
**Rerun 시각화 설정:**

* Rerun은 선택 사항이지만 개발 중 계획된 궤적의 디버깅 및 검증에 매우 권장됩니다.
* 궤적 시각화를 활성화하려면 cuRobo 플래너 구성에서 `visualize_plan = True`를 설정합니다.
* 활성화되면, cuRobo 플래너 인터페이스는 계획된 엔드 이펙터 궤적, 웨이포인트 및 충돌 데이터를 Rerun으로 스트리밍하여 대화형 검사를 수행합니다.
* 시각화는 전체 데이터 세트 생성 전에 계획 문제, 충돌 문제 및 궤적 부드러움을 식별하는 데 도움이 됩니다.
* 또한 `--headless`로 실행하여 IsaacSim 시각화를 비활성화하면서 엔드 이펙터 궤적의 시각화 및 디버깅을 유지할 수 있습니다.

### 단계 4: 설치 확인

Isaac Lab과 함께 cuRobo가 작동하는지 테스트합니다:

```bash
# 이 명령어는 임포트 오류 없이 실행되어야 함
python -c "import curobo; print('cuRobo installed successfully')"
```

## SkillGen 데이터셋 다운로드

SkillGen을 빠르게 시작하는 데 도움이 되는 사전 주석이 달린 데이터셋을 제공합니다.

### 데이터셋 내용

데이터셋에는 다음이 포함됩니다:

* Franka 팔 큐브 스태킹을 위한 인간 데모
* 각 데모에 대한 수동으로 주석이 달린 서브태스크 경계
* 기본 큐브 스태킹 및 적응형 바인 큐브 스태킹 작업 모두와 호환 가능

### 다운로드 및 설정

1. [여기](https://omniverse-content-production.s3-us-west-2.amazonaws.com/Assets/Isaac/5.0/Isaac/IsaacLab/Mimic/franka_stack_datasets/annotated_dataset_skillgen.hdf5)를 클릭하여 사전 주석이 달린 데이터셋을 다운로드합니다.
2. 데이터셋 디렉터리를 준비하고 다운로드한 파일을 이동합니다:

```bash
# Isaac Lab 워크스페이스의 루트 디렉터리에 있는지 확인하세요
cd /path/to/your/IsaacLab

# datasets 디렉터리가 없으면 생성합니다
mkdir -p datasets

# 다운로드한 데이터셋을 datasets 디렉터리로 이동합니다
mv /path/to/annotated_dataset_skillgen.hdf5 datasets/annotated_dataset_skillgen.hdf5
```

## 데이터셋 주석 이해

SkillGen은 서브태스크 시작 및 종료 경계가 주석이 달린 데이터셋이 필요합니다. 자동 주석은 지원되지 않습니다.

### SkillGen의 서브태스크

**기술적 정의:** 서브태스크는 `SubTaskConfig`를 통해 정의된 조작 목표를 달성하는 연속 데모 세그먼트입니다:

* `object_ref`: 이 서브태스크의 공간 참조로 사용되는 객체(또는 `None`)
* `subtask_term_signal`: 서브태스크가 완료될 때(0에서 1로 전환) 이진 종료 신호 이름
* `subtask_start_signal`: 서브태스크가 시작될 때(0에서 1로 전환) 이진 시작 신호 이름(SkillGen에 필수)

서브태스크 лока라이제이션 프로세스는 다음을 수행합니다:

* 신호 전이점(0에서 1로 전환) 감지를 통해 서브태스크 경계 `[t_start, t_end]` 식별;
* 경계 사이의 서브태스크 세그먼트 추출;
* 객체-또는 작업-기준 프레임에서 엔드 이펙터 궤적 및 핵심 포즈 계산(`object_ref`가 제공된 경우 사용);

이를 통해 절대적이며 씬 특유인 모션을 객체 기준으로 변환하여, 데이터 생성 중에 새로운 객체 배치 및 씬 구성에 적응할 수 있는 스킬 세그먼트를 생성합니다.

### 수동 주석 워크플로우

Isaac Lab Mimic 워크플로우와 달리, SkillGen은 서브태스크 시작 및 종료 경계의 수동 주석이 필요합니다. 예를 들어, 큐브를grasping하는 경우, 시작 신호는 그리퍼가 닫히기 직전이고 종료 신호는 객체가 grasped된 직후입니다. 서브태스크 정의에 맞게 시작 및 종료 신호를 조정할 수 있습니다.

## SkillGen을 사용한 데이터 생성

SkillGen은 모션 플래닝을 사용하여 주석이 달린 데모를 다양한 고품질 데이터세트로 변환합니다.

### SkillGen 작동 방식

SkillGen 파이프라인은 주석이 달린 데이터셋과 환경의 Mimic API를 사용하여 새로운 데모를 합성합니다:

1. **서브태스크 경계 사용**: 주석이 달린 데이터셋에서 서브태스크당 시작 및 종료 인덱스를 읽음
2. **목표 샘플링**: 작업 제약 조건 및 데이터 생성 구성에 따라 서브태스크당 목표 포즈 샘플링
3. **궤적 플래닝**: cuRobo를 사용하여 서브태스크 세그먼트 사이에서 충돌이 없는 모션 플래닝 (`--use_skillgen` 시)
4. **궤적 스티칭**: 스킬 세그먼트와 계획된 궤적을 완전한 데모로 스티칭
5. **성공 평가**: 작업 성공 조건 검증; 성공한 시도만 출력 데이터세트에 기록

### 사용 매개변수

SkillGen 데이터 생성의 주요 매개변수:

* `--use_skillgen`: SkillGen 플래너 활성화 (필수)
* `--generation_num_trials`: 생성할 데모 수
* `--num_envs`: 병렬 환경 수 (GPU 메모리에 따라 튜닝)
* `--device`: 계산 장치 (cpu/cuda). 안정적인 물리 시뮬레이션을 위해 cpu 사용
* `--headless`: 시각화 비활성화로 더 빠른 생성

<a id="task-basic-cube-stacking"></a>

## 작업 1: 기본 큐브 스태킹

표준 Isaac Lab Mimic 큐브 스태킹 작업에 대한 데모를 생성합니다. 이 작업에서 Franka 로봇은 다음을 수행해야 합니다:

1. 빨간 큐브를 집어 파란 큐브 위에 놓기
2. 초록 큐브를 집어 빨간 큐브 위에 놓기
3. 최종 스태킹 순서: 파란색(아래), 빨간색(가운데), 초록색(위쪽).

![SkillGen으로 생성된 큐브 스태킹 작업](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/cube_stack_data_gen_skillgen.gif)

### 소규모 생성

모든 것이 제대로 작동하는지 확인하기 위해 작은 데이터셋부터 시작하세요:

```bash
./isaaclab.sh -p scripts/imitation_learning/isaaclab_mimic/generate_dataset.py \
--device cpu \
--num_envs 1 \
--generation_num_trials 10 \
--input_file ./datasets/annotated_dataset_skillgen.hdf5 \
--output_file ./datasets/generated_dataset_small_skillgen_cube_stack.hdf5 \
--task Isaac-Stack-Cube-Franka-IK-Rel-Skillgen-v0 \
--use_skillgen
```

### 전체 규모 생성

소규모 결과에 만족하면 전체 훈련 데이터셋을 생성하세요:

```bash
./isaaclab.sh -p scripts/imitation_learning/isaaclab_mimic/generate_dataset.py \
--device cpu \
--headless \
--num_envs 1 \
--generation_num_trials 1000 \
--input_file ./datasets/annotated_dataset_skillgen.hdf5 \
--output_file ./datasets/generated_dataset_skillgen_cube_stack.hdf5 \
--task Isaac-Stack-Cube-Franka-IK-Rel-Skillgen-v0 \
--use_skillgen
```

#### NOTE
* `--headless`를 사용하여 시각화를 비활성화하고 더 빠른 생성을 수행하세요. 시각화는 cuRobo 플래너 구성에서 `visualize_plan = True`로 설정하여 `--headless`가 활성화된 상태에서도 재활성화할 수 있습니다(디버깅용).
* GPU 메모리에 따라 `--num_envs`를 조정하세요(1로 시작하여 점차 증가). `num_envs`가 1보다 클 때 성능 향상은 크지 않습니다. 대부분의 GPU에서 cuRobo 인스턴스와 시뮬레이션 환경 간의 성능과 메모리 사용량 균형을 맞추기에 5가 적절한 값입니다.
* 생성 시간: RTX 6000 Ada GPU에서 `--headless`를 활성화하고 1000개의 시연을 생성할 때 한 환경당 약 90~120분 소요됩니다. 시간은 GPU, 환경 수, 시연의 성공률(주석이 달린 데이터셋의 품질에 따라 달라짐)에 따라 달라집니다.
* cuRobo 플래너 인터페이스 및 구성은 [cuRobo 인터페이스 기능](#curobo-interface-features)에서 설명합니다.

<a id="task-bin-cube-stacking"></a>

## Task 2: 바인에서의 적응형 큐브 스태킹

SkillGen은 적응형 작업에 대한 데이터셋 생성에도 사용될 수 있습니다. 이 예시에서는 좁은 바인에서 적응형 큐브 스태킹을 위한 데이터셋을 생성합니다. 바인은 작업 공간에서 고정된 위치와 방향으로 배치되며, 파란색 큐브는 바인 중앙에 위치합니다. 로봇은 바인과 충돌하지 않으면서 빨간색과 초록색 큐브를 파란색 큐브 위에 스태킹하는 성공적인 시연을 생성해야 합니다.

![SkillGen으로 생성된 적응형 바인 큐브 스태킹 작업](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/bin_cube_stack_data_gen_skillgen.gif)

### 소규모 생성

적응형 스태킹 설정을 테스트하세요:

```bash
./isaaclab.sh -p scripts/imitation_learning/isaaclab_mimic/generate_dataset.py \
--device cpu \
--num_envs 1 \
--generation_num_trials 10 \
--input_file ./datasets/annotated_dataset_skillgen.hdf5 \
--output_file ./datasets/generated_dataset_small_skillgen_bin_cube_stack.hdf5 \
--task Isaac-Stack-Cube-Bin-Franka-IK-Rel-Mimic-v0 \
--use_skillgen
```

### 전체 규모 생성

완전한 적응형 스태킹 데이터셋을 생성하세요:

```bash
./isaaclab.sh -p scripts/imitation_learning/isaaclab_mimic/generate_dataset.py \
--device cpu \
--headless \
--num_envs 1 \
--generation_num_trials 1000 \
--input_file ./datasets/annotated_dataset_skillgen.hdf5 \
--output_file ./datasets/generated_dataset_skillgen_bin_cube_stack.hdf5 \
--task Isaac-Stack-Cube-Bin-Franka-IK-Rel-Mimic-v0 \
--use_skillgen
```

#### WARNING
적응형 작업은 복잡성 증가로 인해 일반적으로 성공률이 낮고 데이터 생성 시간이 더 오래 걸립니다. 성공률이 낮은 기본 큐브 스태킹과 어려운 계획 문제로 인해 데이터셋 생성 시간도 더 길어집니다.

#### NOTE
사전 주석이 달린 데이터셋을 사용하고 `--headless`가 활성화된 상태로 데이터 생성 명령을 실행하면 RTX 6000 Ada GPU에서 단일 환경에 대해 1000개의 시연을 생성하는 데通常 ~220분이 소요됩니다.

#### NOTE
**VRAM 사용량 및 GPU 권장 사항**

RTX 6000 Ada에서 10개의 생성된 시연을 기준으로 측정되었습니다.
: * 기본 큐브 스태킹: 1개 환경 ~9.3~9.6 GB 안정적; 5개 환경 ~21.8~22.2 GB 안정적(초기화 중에 일시적으로 더 높음).
  * 적응형 바인 큐브 스태킹: 1개 환경 ~9.3~9.6 GB 안정적; 5개 환경 ~22.0~22.3 GB 안정적(초기화 중에 일시적으로 더 높음).
  * 최소 권장 GPU: `--num_envs` 1~2의 경우 ≥24 GB VRAM; `--num_envs`가 ~5까지의 경우 ≥48 GB VRAM.
  * VRAM 사용량을 줄이려면 `--headless`를 선호하고 `--num_envs`를 modest하게 유지하세요. 숫자는 scene 자산과 시연 수에 따라 달라질 수 있습니다.

## SkillGen 데이터로부터 정책 학습

Isaac Lab Mimic 워크플로와 유사하게, 생성된 SkillGen 데이터셋을 사용하여 Robomimic으로 모방 학습 정책을 훈련할 수 있습니다.

### 기본 큐브 스태킹 정책

기본 큐브 스태킹 작업에 대한 상태 기반 정책을 훈련하세요:

```bash
./isaaclab.sh -p scripts/imitation_learning/robomimic/train.py \
--task Isaac-Stack-Cube-Franka-IK-Rel-Skillgen-v0 \
--algo bc \
--dataset ./datasets/generated_dataset_skillgen_cube_stack.hdf5
```

### 적응형 바인 큐브 스태킹 정책

더 복잡한 적응형 바인 스태킹에 대한 정책을 훈련하세요:

```bash
./isaaclab.sh -p scripts/imitation_learning/robomimic/train.py \
--task Isaac-Stack-Cube-Bin-Franka-IK-Rel-Mimic-v0 \
--algo bc \
--dataset ./datasets/generated_dataset_skillgen_bin_cube_stack.hdf5
```

#### NOTE
훈련 스크립트는 `IssacLab/logs/robomimic` 아래의 모델 디렉터리에 모델 체크포인트를 저장합니다.

### 훈련된 정책 평가

훈련된 정책을 테스트하세요:

```bash
# 기본 큐브 스태킹 평가
./isaaclab.sh -p scripts/imitation_learning/robomimic/play.py \
--device cpu \
--task Isaac-Stack-Cube-Franka-IK-Rel-Skillgen-v0 \
--num_rollouts 50 \
--checkpoint /path/to/model_checkpoint.pth
```

```bash
# 적응형 바인 큐브 스태킹 평가
./isaaclab.sh -p scripts/imitation_learning/robomimic/play.py \
--device cpu \
--task Isaac-Stack-Cube-Bin-Franka-IK-Rel-Mimic-v0 \
--num_rollouts 50 \
--checkpoint /path/to/model_checkpoint.pth
```

#### NOTE
**큐브 스태킹 및 바인 큐브 스태킹 작업의 예상 성공률 및 권장 사항**

* SkillGen 데이터 생성 및 하류 정책 성공은 작업과 데이터셋 주석의 품질에 민감하며 높은 분산을 보일 수 있습니다.
* 큐브 스태킹과 바인 큐브 스태킹의 경우, 데이터셋이 지침에 따라 올바르게 주석이 달렸다면 데이터 생성 성공률은 일반적으로 40%에서 70%입니다.
* 1000개의 생성된 시연으로 훈련된 Behavior Cloning(BC) 정책의 성공률은 이 작업들에 대해 일반적으로 40%에서 85%이며, 데이터 품질에 따라 달라집니다.
* RTX 6000 Ada GPU에서 1000개의 시연으로 2000 에포크(기본값) 동안 정책을 훈련하는 데 약 30~35분이 소요됩니다. 훈련 시간은 시연 수와 에포크 수에 따라 증가합니다.
* 데이터 생성 시간에 대해서는 [Task 1: 기본 큐브 스태킹](#task-basic-cube-stacking)과 [Task 2: 바인에서의 적응형 큐브 스태킹](#task-bin-cube-stacking)을 참조하세요.
* 권장 사항: 약 1000개의 생성된 시연으로 기본값인 2000 에포크 동안 훈련하고, 1000번째 에포크 이후에 저장된 여러 체크포인트를 평가하여 가장 성능이 좋은 정책을 선택하세요.

<a id="curobo-interface-features"></a>

## cuRobo 인터페이스 기능

이 섹션에서는 cuRobo 플래너 인터페이스 및 기능을 요약합니다. SkillGen 파이프라인에서는 cuRobo 플래너를 사용하여 서브태스크 세그먼트 사이의 충돌 없는 움직임을 생성합니다. 그러나 사용자는 cuRobo를 독립형 운동 계획기로 자신의 작업에 사용할 수도 있습니다. 사용자는 기본 운동 계획기를 서브클래싱하고 동일한 API를 구현하여 자신의 운동 계획기를 구현할 수도 있습니다.

### 기본 운동 계획기 (확장 가능)

* 위치: `isaaclab_mimic/motion_planners/base_motion_planner.py`
* 목적: SkillGen에서 사용하는 모든 운동 계획기의 균일한 인터페이스
* 확장 가능성: 새로운 계획자는 동일한 API를 구현하여 서브클래스로 추가할 수 있으며, SkillGen은 코드 변경 없이 API를 소비합니다

### cuRobo 플래너 (GPU, 충돌 인식)

* 위치: `isaaclab_mimic/motion_planners/curobo`
* 다단계 계획:
  * 서브태스크당 후퇴 → 접촉 → 접근 단계
  * 접촉 단계에서의 충돌 필터링 구성 가능
  * SkillGen의 경우, 후퇴 및 접근 단계는 충돌이 없으며, 통과 단계는 충돌이 확인됩니다.
* 세계 동기화:
  * 각 시도마다 Isaac Lab 씬에서 로봇 상태, 부착된 객체 및 충돌 구체를 업데이트합니다.
  *grasping/placing 동안 객체의 동적 부착/분리
* 충돌 표현:
  * 단계별 활성화/필터링이 가능한 접촉 인식 구 세트
* 출력:
  * 스티칭을 위한 시간 매개변수화되고 충돌이 확인된 궤적
* 테스트:
  * `source/isaaclab_mimic/test/test_curobo_planner_cube_stack.py`
  * `source/isaaclab_mimic/test/test_curobo_planner_franka.py`
  * `source/isaaclab_mimic/test/test_generate_dataset_skillgen.py`

| ![Franka Panda 로봇에서의 cuRobo 플래너 큐브 스태킹 테스트](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/cube_stack_end_to_end_curobo.gif)   | ![Franka Panda 로봇에서의 cuRobo 플래너 장애물 회피 테스트](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/obstacle_avoidance_curobo.gif)   |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|

これらのテストは、cuRobo をスタンドアロンのモーションプランナーとして使用する方法の参照にもなります。

#### NOTE
cuRobo 구성 생성 및 매개변수에 대한 자세한 내용은 파일 `isaaclab_mimic/motion_planners/curobo/curobo_planner_config.py`를 참조하세요.

### 생성 파이프라인 통합

`generate_dataset.py`에서 `--use_skillgen`이 활성화되면 다음과 같은 파이프라인이 실행됩니다:

1. **서브태스크 경계 랜덤화**: 작업-구성된 오프셋 범위를 사용하여 각 서브태스크에 대한 시작 및 종료 인덱스를 데모당 랜덤화합니다.
2. **서브태스크별 트래젝터리 구축**:
   각 엔드 이펙터 및 서브태스크에 대해:
   - 소스 데모 세그먼트 선택(전략 기반; 협력/순차적 제약 조건 존중)
   - 현재 씬에 세그먼트 변환(객체 상대 또는 협력 델타; 선택적 첫 번째 포즈 보간)
   - 변환된 세그먼트를 웨이포인트 트래젝터리로 래핑
3. **서브태스크 간 전환**:
   - cuRobo를 사용하여 서브태스크의 첫 번째 웨이포인트로 충돌 인식 전환 계획(세계 동기화, 선택적 부착/분리), 계획된 웨이포인트 실행,その後 서브태스크 트래젝터리 재개
4. **제약 조건 하에서 실행**:
   - 엔드 이펙터 전반에 걸쳐 웨이포인트를 단계별로 실행하면서 서브태스크 제약 조건 적용(순차적, 협력과 동기화 단계); 선택적으로 플래너 시각화 활성화 시 업데이트
5. **기록 및 내보내기**:
   - 상태/관측/행동 축적, 에피소드 성공 플래그 설정, 에피소드 내보내기(외부 파이프라인에서는 성공을 필터링/소비)

### 시각화 및 디버깅

사용자는 Rerun 기반 플래너 시각화 도구를 사용하여 계획된 트래젝터리를 시각화하고 충돌을 디버그할 수 있습니다. 이는 cuRobo 플래너 구성에서 `visualize_plan = True`로 설정하여 활성화할 수 있습니다. 참고로, 계획된 트래젝터리를 시각화하려면 rerun이 설치되어 있어야 합니다. 설치 지침은 [설치](#skillgen-installation)의 단계 3을 참조하세요.

![Rerun 시각화로 본 계획된 트래젝터리와 충돌](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/rerun_cube_stack.gif)

#### NOTE
cuRobo 사용 라이선스는 `docs/licenses/dependencies/cuRobo-license.txt`를 확인하세요.
