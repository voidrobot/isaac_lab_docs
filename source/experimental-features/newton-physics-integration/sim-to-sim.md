<a id="sim2sim"></a>

# Sim-to-Sim 정책 전송

이 섹션에서는 PhysX와 Newton 백엔드 간 sim-to-sim 정책 전송의 예를 제공합니다. sim-to-sim 전송은 실제 로봇 배포 전에 필수적인 단계로, 정책이 서로 다른 시뮬레이터 간에 작동하는지 확인합니다. sim-to-sim 검증을 통과한 정책은 실제 로봇에서 성공할 가능성이 훨씬 높습니다.

## 개요

이 가이드는 PhysX와 Newton 백엔드 간 양방향 정책 전송 방법을 보여줍니다. 주요한 도전 과제는 서로 다른 물리 엔진이 동일한 로봇 모델을 다른 관절 및 링크 순서로 해석할 수 있다는 점입니다.

하나의 백엔드에서 훈련된 정책은 해당 백엔드가 로봇 모델을 해석하는 방식에 따라 특정 순서로 관절과 링크를 기대합니다. 다른 백엔드로 전송할 때 관절 순서가 달라질 수 있으며, 이로 인해 관측값과 행동의 재매핑이 필요해집니다.

앞으로는 **로봇 스키마**를 사용하여 다양한 백엔드 간에 관절 및 링크 순서를 표준화할 계획입니다.

현재는 `scripts/sim2sim_transfer/config/` 디렉터리 아래에 있는 YAML 파일에 정의된 관절 매핑을 사용해 관측값과 행동을 재매핑함으로써 이 문제를 해결합니다. 이 파일들은 소스 및 타겟 백엔드 순서로 관절 이름을 지정합니다. 정책 실행 중에 이 매핑을 사용하여 관측값과 행동을 재정렬하고 타겟 백엔드에서 올바르게 작동하도록 합니다.

이 방법은 Unitree G1, Unitree Go2, Unitree H1 및 ANYmal-D 로봇에 대해 양방향 모두에서 테스트되었습니다.

### 필요 사항

- PhysX 또는 Newton(RSL-RL)로 훈련된 정책 체크포인트.
- `scripts/sim2sim_transfer/config/` 아래의 로봇용 관절 매핑 YAML 파일.
- 제공된 플레이어 스크립트: `scripts/sim2sim_transfer/rsl_rl_transfer.py`.

새로운 로봇을 추가하려면 소스 및 타겟 백엔드 순서에서 각 관절 이름이 정확히 한 번씩 나타나는 두 개의 리스트를 갖는 YAML 파일을 생성합니다.

```yaml
# 예시 구조
source_joint_names:  # 소스 백엔드 관절 순서
  - joint_1
  - joint_2
  # ...
target_joint_names:  # 타겟 백엔드 관절 순서
  - joint_1
  - joint_2
  # ...
```

스크립트는 자동으로 로봇 이동 작업에 필요한 매핑을 계산합니다.

### PhysX에서 Newton으로 전송

PhysX로 훈련된 정책을 Newton 백엔드에서 실행하려면 다음 명령어 템플릿을 사용하세요:

```bash
./isaaclab.sh -p scripts/sim2sim_transfer/rsl_rl_transfer.py \
    --task=<TASK_ID> \
    --num_envs=32 \
    --checkpoint <PATH_TO_PHYSX_CHECKPOINT> \
    --policy_transfer_file <PATH_TO_MAPPING_YAML> \
    --visualizer newton
```

다양한 로봇에 대한 예시는 다음과 같습니다:

1. Unitree G1

```bash
./isaaclab.sh -p scripts/sim2sim_transfer/rsl_rl_transfer.py \
    --task=Isaac-Velocity-Flat-G1-v0 \
    --num_envs=32 \
    --checkpoint <PATH_TO_PHYSX_CHECKPOINT> \
    --policy_transfer_file scripts/sim2sim_transfer/config/physx_to_newton_g1.yaml \
    --visualizer newton
```

1. Unitree H1

```bash
./isaaclab.sh -p scripts/sim2sim_transfer/rsl_rl_transfer.py \
    --task=Isaac-Velocity-Flat-H1-v0 \
    --num_envs=32 \
    --checkpoint <PATH_TO_PHYSX_CHECKPOINT> \
    --policy_transfer_file scripts/sim2sim_transfer/config/physx_to_newton_h1.yaml \
    --visualizer newton
```

1. Unitree Go2

```bash
./isaaclab.sh -p scripts/sim2sim_transfer/rsl_rl_transfer.py \
    --task=Isaac-Velocity-Flat-Go2-v0 \
    --num_envs=32 \
    --checkpoint <PATH_TO_PHYSX_CHECKPOINT> \
    --policy_transfer_file scripts/sim2sim_transfer/config/physx_to_newton_go2.yaml \
    --visualizer newton
```

1. ANYmal-D

```bash
./isaaclab.sh -p scripts/sim2sim_transfer/rsl_rl_transfer.py \
    --task=Isaac-Velocity-Flat-Anymal-D-v0 \
    --num_envs=32 \
    --checkpoint <PATH_TO_PHYSX_CHECKPOINT> \
    --policy_transfer_file scripts/sim2sim_transfer/config/physx_to_newton_anymal_d.yaml \
    --visualizer newton
```

이 명령을 실행하려면 Newton 기반의 IsaacLab 브랜치(예: `feature/newton`)를 체크아웃해야 한다는 점에 유의하세요.

### Newton에서 PhysX로 전송

PhysX 기반의 IsaacLab에서 Newton으로 훈련된 정책을 전송하려면 역매핑 파일을 사용하세요:

다양한 로봇에 대한 예시는 다음과 같습니다:

1. Unitree G1

```bash
./isaaclab.sh -p scripts/sim2sim_transfer/rsl_rl_transfer.py \
    --task=Isaac-Velocity-Flat-G1-v0 \
    --num_envs=32 \
    --checkpoint <PATH_TO_NEWTON_CHECKPOINT> \
    --policy_transfer_file scripts/sim2sim_transfer/config/newton_to_physx_g1.yaml
```

1. Unitree H1

```bash
./isaaclab.sh -p scripts/sim2sim_transfer/rsl_rl_transfer.py \
    --task=Isaac-Velocity-Flat-H1-v0 \
    --num_envs=32 \
    --checkpoint <PATH_TO_NEWTON_CHECKPOINT> \
    --policy_transfer_file scripts/sim2sim_transfer/config/newton_to_physx_h1.yaml
```

1. Unitree Go2

```bash
./isaaclab.sh -p scripts/sim2sim_transfer/rsl_rl_transfer.py \
    --task=Isaac-Velocity-Flat-Go2-v0 \
    --num_envs=32 \
    --checkpoint <PATH_TO_NEWTON_CHECKPOINT> \
    --policy_transfer_file scripts/sim2sim_transfer/config/newton_to_physx_go2.yaml
```

1. ANYmal-D

```bash
./isaaclab.sh -p scripts/sim2sim_transfer/rsl_rl_transfer.py \
    --task=Isaac-Velocity-Flat-Anymal-D-v0 \
    --num_envs=32 \
    --checkpoint <PATH_TO_NEWTON_CHECKPOINT> \
    --policy_transfer_file scripts/sim2sim_transfer/config/newton_to_physx_anymal_d.yaml
```

주요 차이점은 `physx_to_newton_*.yaml` 매핑 파일 대신 `newton_to_physx_*.yaml` 매핑 파일을 사용하는 것입니다. 또한 PhysX 기반의 IsaacLab 브랜치(예: `main`)를 체크아웃해야 한다는 점에 유의하세요.

### 참고 사항 및 제한 사항

- 양방향 전송 모두 Unitree G1, Unitree Go2, Unitree H1 및 ANYmal-D 로봇에서 테스트되었습니다.
- PhysX-to-Newton 전송은 `physx_to_newton_*.yaml` 매핑 파일을 사용합니다.
- Newton-to-PhysX 전송은 해당하는 `newton_to_physx_*.yaml` 매핑 파일과 PhysX 브랜치의 IsaacLab이 필요합니다.
- 관측값 재매핑은 기본 관측값 다음에 관절 관측값이 오는 locomotion 레이아웃을 가정합니다. 다른 관측값 레이아웃의 경우, `scripts/sim2sim_transfer/rsl_rl_transfer.py`의 `get_joint_mappings` 함수를 수정해야 합니다.
- 새로운 로봇이나 백엔드를 추가할 때, 소스 및 타겟이 동일한 관절 이름을 갖고 있고, YAML 리스트가 각 백엔드가 이러한 관절을 어떻게 순서대로 정렬하는지를 반영하는지 확인하세요.
