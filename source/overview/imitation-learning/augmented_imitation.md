<a id="augmented-imitation-learning"></a>

# 증강 모방 학습

이 섹션에서는 Isaac Lab의 모방 학습 기능과 [Cosmos](https://www.nvidia.com/en-us/ai/cosmos/) 모델의 시각적 증강 기능을 결합하여 시각적 변형에 강인한 visuomotor 정책을 훈련하기 위한 대규모 시연을 생성하는 방법을 설명합니다.

## 시연 생성

Isaac Lab Mimic 기능을 사용하여 몇 개의 주석이 달린 시연으로부터 추가 시연을 자동으로 생성합니다.

#### 참고
이 섹션은 이미 주석이 달린 시연 데이터셋을 보유하고 있다고 가정합니다. 그렇지 않은 경우, [Isaac Lab Mimic을 사용한 텔레오퍼레이션 및 모방 학습](teleop_imitation.md#teleoperation-imitation-learning)의 지침을 따라 직접 시연을 수집하고 주석을 달 수 있습니다.

다음 예시에서는 Isaac Lab Mimic을 사용하여 visuomotor 정책을 직접 훈련하거나 Cosmos(using `Isaac-Stack-Cube-Franka-IK-Rel-Visuomotor-Cosmos-Mimic-v0` 환경)를 통한 시각적 변형으로 증강하여 사용할 수 있는 추가 시연을 생성하는 방법을 보여줍니다.

#### 참고
`Isaac-Stack-Cube-Franka-IK-Rel-Visuomotor-Cosmos-Mimic-v0` 환경은 표준 visuomotor 환경(`Isaac-Stack-Cube-Franka-IK-Rel-Visuomotor-Mimic-v0`)과 유사하지만, 생성된 데이터셋에 세그멘테이션 마스크, 깊이 맵, 법선 맵이 추가되었습니다. 이 추가 모달리티는 Cosmos를 사용한 시각적 증강에서 최상의 결과를 얻기 위해 필요합니다.

```bash
./isaaclab.sh -p scripts/imitation_learning/isaaclab_mimic/generate_dataset.py \
--device cpu --enable_cameras --headless --num_envs 10 --generation_num_trials 1000 \
--input_file ./datasets/annotated_dataset.hdf5 --output_file ./datasets/mimic_dataset_1k.hdf5 \
--task Isaac-Stack-Cube-Franka-IK-Rel-Visuomotor-Cosmos-Mimic-v0 \
--rendering_mode performance
```

시연의 수는 증가하거나 감소시킬 수 있으며, 이 작업에 대해 1000개의 시연이 좋은 훈련 결과를 제공하는 것으로 입증되었습니다.

또한, `--num_envs` 매개변수의 환경 수를 조정하여 데이터 생성을 가속화할 수 있습니다. 제안된 10개의 환경 수는 중간 성능의 노트북 CPU에서 실행할 수 있습니다. 더 강력한 데스크톱 머신에서는 이 단계의 속도를 크게 높이기 위해 더 많은 환경 수를 사용할 수 있습니다.

## Cosmos 증강

### HDF5에서 MP4로 변환

`hdf5_to_mp4.py` 스크립트는 HDF5 시연 파일에 저장된 카메라 프레임을 MP4 동영상으로 변환합니다. RGB, 세그멘테이션, 깊이 및 법선 맵을 포함한 여러 카메라 모달리티를 지원합니다. 이 변환은 Cosmos를 사용한 시각적 증강에 필요합니다. Cosmos는 HDF5 데이터가 아닌 동영상 파일만 처리하기 때문입니다.

### 필수 인수

| `--input_file`   | 입력 HDF5 파일 경로.            |
|------------------|---------------------------------|
| `--output_dir`   | 출력 MP4 파일을 저장할 디렉터리. |

### 선택적 인수

| `--input_keys`   | HDF5 파일에서 처리할 입력 키 목록. (기본값: [“table_cam”, “wrist_cam”, “table_cam_segmentation”, “table_cam_normals”, “table_cam_shaded_segmentation”, “table_cam_depth”])   |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--video_height` | 출력 동영상의 높이 (픽셀 단위). (기본값: 704)                                                                                                                                         |
| `--video_width`  | 출력 동영상의 너비 (픽셀 단위). (기본값: 1280)                                                                                                                                         |
| `--framerate`    | 출력 동영상의 프레임당 초당 프레임 수. (기본값: 30)                                                                                                                                        |

#### 참고
기본 입력 키는 `Isaac-Stack-Cube-Franka-IK-Rel-Visuomotor-Cosmos-Mimic-v0` 환경에서 사용되는 명명 규칙을 따르는 모든 카메라 모달리티를 포함합니다. 또한 시뮬레이션에서 생성된 모달리티의 일부가 아닌 “table_cam_shaded_segmentation”이라는 추가 모달리티도 포함됩니다. 이 모달리티는 세그멘테이션 및 법선 맵의 조합을 사용하여 자동으로 생성되며, Cosmos 증강을 위한 더 나은 제어를 위해 pseudo-textured 세그멘테이션 동영상을 얻기 위해 사용됩니다.

#### 참고
Cosmos 증강에 최상의 결과를 얻기 위해 출력 동영상의 높이, 너비 및 프레임률에 대한 기본값을 사용하는 것을 권장합니다.

큐브 쌓기 작업의 사용 예시:

```bash
python scripts/tools/hdf5_to_mp4.py \
--input_file datasets/mimic_dataset_1k.hdf5 \
--output_dir datasets/mimic_dataset_1k_mp4
```

<a id="running-cosmos"></a>

### Cosmos를 사용한 시각적 증강 실행

시연을 MP4 형식으로 변환한 후, [Cosmos](https://www.nvidia.com/en-us/ai/cosmos/) 모델을 사용하여 동영상을 시각적으로 증강할 수 있습니다. 증강 과정의詳細は Cosmos 문서를 참조하십시오. 시각적 증광은 조명, 텍스처, 배경 및 기타 시각적 요소의 변경을 포함하면서도 필수적인 작업 관련 특징은 보존합니다.

우리는 이전 단계에서 생성된 RGB, 깊이 및 shaded 세그멘테이션 동영상을 Cosmos 모델의 입력으로 사용합니다. 다음과 같이 보입니다:

![RGB, depth and segmentation control inputs to Cosmos](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/cosmos_inputs.gif)

다음은 [Cosmos Transfer1](https://github.com/nvidia-cosmos/cosmos-transfer1/tree/e4055e39ee9c53165e85275bdab84ed20909714a)을 사용한 증강 출력 예시입니다:

![Cosmos Transfer1 augmentation output](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/cosmos_output.gif)

우리는 Cosmos Transfer1 모델을 시각적 증강에 사용하는 것을 권장합니다. 왜냐하면 이 모델이 다양한 시각적 변형을 가진 매우 다양한 데이터셋을 생성하는 데 가장 좋은 결과를 제공했기 때문입니다. [설치 지침](https://github.com/nvidia-cosmos/cosmos-transfer1/blob/e4055e39ee9c53165e85275bdab84ed20909714a/INSTALL.md#environment-setup), [체크포인트 다운로드 지침](https://github.com/nvidia-cosmos/cosmos-transfer1/blob/e4055e39ee9c53165e85275bdab84ed20909714a/examples/inference_cosmos_transfer1_7b.md#download-checkpoints) 및 [이 예시](https://github.com/nvidia-cosmos/cosmos-transfer1/blob/e4055e39ee9c53165e85275bdab84ed20909714a/examples/inference_cosmos_transfer1_7b.md#example-2-multimodal-control)를 참조하여 Transfer1을 이 사용 사례에 사용하는 방법을 확인할 수 있습니다. 또한 이 작업에 Transfer1 모델을 사용할 때 다음과 같은 설정을 사용하는 것을 추가로 권장합니다.

#### 참고
이 워크플로는 Cosmos Transfer1 저장소의 커밋 `e4055e39ee9c53165e85275bdab84ed20909714a`에서 테스트되었으며, 이 버전을 사용하는 것이 권장됩니다. Cosmos Transfer1 저장소를 클론한 후, `git checkout e4055e39ee9c53165e85275bdab84ed20909714a`를 실행하여 이 특정 커밋으로 체크아웃하십시오.

### 하이퍼파라미터

| `negative_prompt`   | “The video captures a game playing, with bad crappy graphics and cartoonish frames. It represents a recording of old outdated games. The images are very pixelated and of poor CG quality. There are many subtitles in the footage. Overall, the video is unrealistic and appears cg. Plane background.”   |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `sigma_max`         | 50                                                                                                                                                                                                                                                                                                         |
| `control_weight`    | “0.3,0.3,0.6,0.7”                                                                                                                                                                                                                                                                                          |
| `hint_key`          | “blur,canny,depth,segmentation”                                                                                                                                                                                                                                                                            |

Cosmos 생성을 제어하는 데 사용되는 프롬프트 세트를 올바르게 설정하는 것도 좋은 증강을 얻는 데 중요합니다. 우리는 주의 깊게 선택된 템플릿에서 프롬프트를 구성하는 스크립트 `cosmos_prompt_gen.py`를 제공하여 증강 프로세스의 다양한 측면을 처리합니다.

### 필수 인수

| `--templates_path`   | 프롬프트 템플릿이 포함된 파일 경로.   |
|----------------------|--------------------------------------|

### 선택적 인수

| `--num_prompts`   | 생성할 프롬프트 수 (기본값: 1).                                |
|-------------------|----------------------------------------------------------------|
| `--output_path`   | 생성된 프롬프트를 쓸 출력 파일 경로. (기본값: prompts.txt) |
```bash
python scripts/tools/cosmos/cosmos_prompt_gen.py \
--templates_path scripts/tools/cosmos/transfer1_templates.json \
--num_prompts 10 --output_path prompts.txt
```

직접 프롬프트를 생성하고 싶은 경우, 다음 지침을 참조하시기 바랍니다:

1. 프롬프트는 가능한 한 상세하게 작성하는 것이 좋습니다. 보이는 객체/관심 영역 각각에 대해 생성 방법이 어떻게 처리해야 하는지에 대한 지침을 포함하는 것이 이상적입니다. 예를 들어, 우리가 제공하는 프롬프트는 테이블, 조명, 배경, 로봇 팔, 큐브, 일반적인 설정에 대한 명시적인 세부 정보를 다룹니다.
2. 증강 지침이 현실적이고 일관되도록 노력하세요. 프롬프트가 비현실적이거나 비상식적일수록 모델이 입력 제어 비디오의 주요 특징을 유지하는 능력이 떨어집니다.
3. 모든 측면에서 증강 지침을 동기화하세요. 여기서 말하는 것은 관심 있는 모든 객체/영역에 대한 증강이 서로 일관되고 상식적이어야 한다는 의미입니다. 예를 들어, “테이블은 faded polish와 음식 얼룩이 있는 오래된 어두운 목재이며, 배경은 교외 주택으로 구성되어 있다”와 같은 프롬프트가 “테이블은 faded polish와 음식 얼룩이 있는 오래된 어두운 목재이며, 배경은 우주 공간을 질주하는 우주선으로 구성되어 있다”보다 더 나은 선택입니다.
4. 입력 제어 비디오에서 유지하거나 변경하지 말아야 할 주요 측면에 대한 세부 정보를 포함하는 것이 중요합니다. 우리의 프롬프트에서는 큐브 색상이 변경되지 않아야 한다는 점을 명확히 언급하고 있습니다(하단 큐브는 파란색, 중간은 빨간색, 상단은 녹색). 우리가 단순히 유지해야 할 사항을 언급하는 것이 아니라, 그 측면이 현재 어떤 형태인지에 대한 세부 정보도 제공한다는 점에 유의하세요.

Cosmos Transfer1 모델을 이 사용 사례에 사용하는 예시 명령어:

```bash
export CUDA_VISIBLE_DEVICES="${CUDA_VISIBLE_DEVICES:=0}"
export CHECKPOINT_DIR="${CHECKPOINT_DIR:=./checkpoints}"
export NUM_GPU="${NUM_GPU:=1}"
PYTHONPATH=$(pwd) torchrun --nproc_per_node=$NUM_GPU --nnodes=1 --node_rank=0 cosmos_transfer1/diffusion/inference/transfer.py \
    --checkpoint_dir $CHECKPOINT_DIR \
    --video_save_folder outputs/cosmos_dataset_1k_mp4 \
    --controlnet_specs ./controlnet_specs/demo_0.json \
    --offload_text_encoder_model \
    --offload_guardrail_models \
    --num_gpus $NUM_GPU
```

위 명령어와 함께 사용할 `./controlnet_specs/demo_0.json` 예시 JSON 파일:

```json
{
    "prompt": "A robotic arm is picking up and stacking cubes inside a foggy industrial scrapyard at dawn, surrounded by piles of old robotic parts and twisted metal. The background includes large magnetic cranes, rusted conveyor belts, and flickering yellow floodlights struggling to penetrate the fog. The robot arm is bright teal with a glossy surface and silver stripes on the outer edges; the joints rotate smoothly and the pistons reflect a pale cyan hue. The robot arm is mounted on a table that is light oak wood with a natural grain pattern and a glossy varnish that reflects overhead lights softly; small burn marks dot one corner. The arm is connected to the base mounted on the table. The bottom cube is deep blue, the second cube is bright red, and the top cube is vivid green, maintaining their correct order after stacking. Sunlight pouring in from a large, open window bathes the table and robotic arm in a warm golden light. The shadows are soft, and the scene feels natural and inviting with a slight contrast between light and shadow.",
    "negative_prompt": "The video captures a game playing, with bad crappy graphics and cartoonish frames. It represents a recording of old outdated games. The images are very pixelated and of poor CG quality. There are many subtitles in the footage. Overall, the video is unrealistic and appears cg. Plane background.",
    "input_video_path" : "mimic_dataset_1k_mp4/demo_0_table_cam.mp4",
    "sigma_max": 50,
    "vis": {
        "input_control": "mimic_dataset_1k_mp4/demo_0_table_cam.mp4",
        "control_weight": 0.3
    },
    "edge": {
        "control_weight": 0.3
    },
    "depth": {
        "input_control": "mimic_dataset_1k_mp4/demo_0_table_cam_depth.mp4",
        "control_weight": 0.6
    },
    "seg": {
        "input_control": "mimic_dataset_1k_mp4/demo_0_table_cam_shaded_segmentation.mp4",
        "control_weight": 0.7
    }
}
```

### MP4에서 HDF5로 변환

`mp4_to_hdf5.py` 스크립트는 시각적으로 증강된 MP4 비디오를 훈련에 적합한 HDF5 형식으로 다시 변환합니다. 이 단계는 시각적으로 증강된 데이터가 Isaac Lab에서 visuomotor 정책을 훈련하기 위한 올바른 형식으로 유지되도록 하고, 원래 데이터세트의 해당 데모 데이터와 비디오를 쌍으로 연결하는 데 매우 중요합니다.

#### 필수 인수

| `--input_file`   | 원래 시연이 포함된 입력 HDF5 파일 경로.   |
|------------------|-----------------------------------------------------------------------|
| `--videos_dir`   | 시각적으로 증강된 MP4 비디오가 포함된 디렉터리.               |
| `--output_file`  | 증강된 비디오가 포함된 새 HDF5 파일을 저장할 경로.                 |

#### 참고
입력 HDF5 파일은 로봇 상태 및 액션과 같은 비시각적 데이터를 보존하면서 시각적 데이터를 증강된 버전으로 대체하는 데 사용됩니다.

#### 중요
시각적으로 증강된 MP4 파일은 다음 이름 규칙을 따라야 합니다: `demo_{demo_id}_*.mp4`, 여기서:

- `demo_id`는 원래 MP4 파일의 데모 ID와 일치해야 합니다.
- `*`은 사용자 선호도에 따라 이 지점부터 시작하는 파일 이름을 나타낼 수 있습니다.

이 이름 규칙은 스크립트가 증강된 비디오를 해당 데모와 올바르게 쌍으로 연결하는 데 필요합니다.

큐브 쌓기 작업에 대한 사용 예시:

```bash
python scripts/tools/mp4_to_hdf5.py \
--input_file datasets/mimic_dataset_1k.hdf5 \
--videos_dir datasets/cosmos_dataset_1k_mp4 \
--output_file datasets/cosmos_dataset_1k.hdf5
```

### 사전 생성된 데이터세트

우리는 큐브 쌓기 작업에 대한 시각적으로 증강된 시연을 포함하는 HDF5 형식의 사전 생성된 데이터세트를 제공합니다. 로컬에서 Cosmos를 실행하여 자체 증강 데이터를 생성하고 싶지 않은 경우 이 데이터세트를 사용할 수 있습니다. 데이터세트는 [Hugging Face](https://huggingface.co/datasets/nvidia/PhysicalAI-Robotics-Manipulation-Augmented)에서 이용 가능하며, 원래 시연과 증강된 시연을 별도의 데이터세트 파일로 제공하여 visuomotor 정책 훈련에 사용할 수 있습니다.

### 데이터세트 병합

`merge_hdf5_datasets.py` 스크립트는 여러 HDF5 데이터세트를 하나의 파일로 결합합니다. 이는 원래 시연과 증강된 시연을 결합하여 더 크고 다양한 훈련 데이터세트를 생성하려는 경우 유용합니다.

#### 필수 인수

| `--input_files`   | 병합할 HDF5 파일 경로 목록.   |
|-------------------|-------------------------------------------|

#### 선택적 인수

| `--output_file`   | 병합된 출력 파일 경로. (기본값: merged_dataset.hdf5)   |
|-------------------|--------------------------------------------------------------|

큐브 쌓기 작업에 대한 사용 예시:

```bash
python scripts/tools/merge_hdf5_datasets.py \
--input_files datasets/mimic_dataset_1k.hdf5 datasets/cosmos_dataset_1k.hdf5 \
--output_file datasets/mimic_cosmos_dataset.hdf5
```

## 모델 훈련 및 평가

### Robomimic 설정

예시로, [Robomimic](https://robomimic.github.io/)에서 구현한 BC 에이전트를 훈련하여 정책을 훈련시킬 것입니다. 다른 프레임워크나 훈련 방법도 사용될 수 있습니다.

Robomimic 프레임워크를 설치하려면 다음 명령을 사용하세요:

```bash
# 의존성 설치
sudo apt install cmake build-essential
# Python 모듈 설치 (robomimic용)
./isaaclab.sh -i robomimic
```

### 에이전트 훈련

생성된 데이터를 사용하여 이제 `Isaac-Stack-Cube-Franka-IK-Rel-Visuomotor-Cosmos-v0`용 visuomotor BC 에이전트를 훈련할 수 있습니다:

```bash
./isaaclab.sh -p scripts/imitation_learning/robomimic/train.py \
--task Isaac-Stack-Cube-Franka-IK-Rel-Visuomotor-Cosmos-v0 --algo bc \
--dataset ./datasets/mimic_cosmos_dataset.hdf5 \
--name bc_rnn_image_franka_stack_mimic_cosmos
```

#### 참고
기본적으로 훈련된 모델과 로그는 `IsaacLab/logs/robomimic`에 저장됩니다.

### 평가

`robust_eval.py` 스크립트는 시뮬레이션에서 훈련된 visuomotor 정책을 평가합니다. 이 평가는 정책이 다양한 시각적 변화에 얼마나 잘 일반화되는지 평가하고, 시각적으로 증강된 데이터가 정책의 견고성을 향상시켰는지 판단하는 데 도움이 됩니다.

다음은 평가에 사용된 다양한 설정에 대한 설명입니다:

### 평가 설정

| `Vanilla`                    | Mimic 데이터 생성 중에 사용된 설정과 동일함.            |
|------------------------------|--------------------------------------------------------------------------|
| `Light Intensity`            | 조명 강도/밝기가 변하고, 다른 모든 측면은 동일하게 유지됨. |
| `Light Color`                | 조명 색이 변하고, 다른 모든 측면은 동일하게 유지됨.                |
| `Light Texture (Background)` | 조명 텍스처/배경이 변하고, 다른 모든 측면은 동일하게 유지됨.   |
| `Table Texture`              | 테이블의 시각적 텍스처가 변하고, 다른 모든 측면은 동일하게 유지됨.     |
| `Robot Arm Texture`          | 로봇 팔의 시각적 텍스처가 변하고, 다른 모든 측면은 동일하게 유지됨. |

#### 필수 인수

| `--task`      | 환경 이름.                                |
|---------------|---------------------------------------------------------|
| `--input_dir` | 평가할 모델 체크포인트가 포함된 디렉터리. |

#### 선택적 인수

| `--start_epoch`      | 평가 시작 체크포인트의 에폭. (기본값: 100)     |
|----------------------|--------------------------------------------------------------------------|
| `--horizon`          | 각 롤아웃의 스텝 범위. (기본값: 400)                             |
| `--num_rollouts`     | 모델당 설정당 롤아웃 수. (기본값: 15)                  |
| `--num_seeds`        | 평가할 랜덤 시드 수. (기본값: 3)                         |
| `--seeds`            | 무작위 대신 사용할 특정 시드 목록.                    |
| `--log_dir`          | 결과를 저장할 디렉터리. (기본값: /tmp/policy_evaluation_results) |
| `--log_file`         | 출력 파일의 이름입니다. (기본값: results)                              |
| `--norm_factor_min`  | 동작 공간 정규화 팩터의 최소값입니다.                  |
| `--norm_factor_max`  | 동작 공간 정규화 팩터의 최대값입니다.                  |
| `--disable_fabric`   | Fabric를 비활성화하고 USD I/O 작업을 사용할지 여부입니다.                    |
| `--enable_pinocchio` | IK 컨트롤러에 Pinocchio를 활성화할지 여부입니다.                          |

#### 참고
평가 결과를 통해 시각적 증강이 정책의 성능과 강건성을 향상시켰는지 확인할 수 있습니다. 원래 데이터셋에서의 평가 결과와 비교하여 증강의 영향을 측정할 수 있습니다.

큐브 스태킹 작업의 사용 예시는 다음과 같습니다:

```bash
./isaaclab.sh -p scripts/imitation_learning/robomimic/robust_eval.py \
--task Isaac-Stack-Cube-Franka-IK-Rel-Visuomotor-Cosmos-v0 \
--input_dir logs/robomimic/Isaac-Stack-Cube-Franka-IK-Rel-Visuomotor-Cosmos-v0/bc_rnn_image_franka_stack_mimic_cosmos/*/models \
--log_dir robust_results/bc_rnn_image_franka_stack_mimic_cosmos \
--log_file result \
--enable_cameras \
--seeds 0 \
--num_rollouts 15 \
--rendering_mode performance
```

#### 참고
이 스크립트는 사용 중인 하드웨어에 따라 하루 이상 걸릴 수 있습니다. 이 동작은 예상되는 동작입니다.

위 스크립트를 사용하여 1000개의 Mimic 생성 시연으로 훈련된 모델, 2000개의 Mimic 생성 시연으로 훈련된 모델, 그리고 2000개의 Cosmos-Mimic 생성 시연으로 훈련된 모델(1000개 원본 Mimic + 1000개 Cosmos 증강)을 각각 비교합니다. 세 모델 모두 동일한 시드(0, 1000, 5000)를 사용하며, 각 시드별 최고 체크포인트의 평균값을 아래에 제시합니다:

### 모델 비교

| **평가 설정**       | **Mimic 1k 기준선**   | **Mimic 2k 기준선**   | **Cosmos-Mimic 2k**   |
|---------------------|-----------------------|-----------------------|-----------------------|
| `Vanilla`           | 62%                   | 96.6%                 | 86.6%                 |
| `Light Intensity`   | 11.1%                 | 20%                   | 62.2%                 |
| `Light Color`       | 24.6%                 | 30%                   | 77.7%                 |
| `Light Texture (Background)` | 16.6%        | 20%                   | 68.8%                 |
| `Table Texture`     | 0%                    | 0%                    | 20%                   |
| `Robot Arm Texture` | 0%                    | 0%                    | 4.4%                  |

위에서 훈련된 모델들의 체크포인트는 다음 링크에서 접근할 수 있습니다 [여기](https://huggingface.co/datasets/nvidia/PhysicalAI-Robotics-Manipulation-Augmented/tree/main/robomimic_bc_rnn_visuomotor_models). 직접 모델을 사용하고자 하는 경우 유용할 것입니다.
