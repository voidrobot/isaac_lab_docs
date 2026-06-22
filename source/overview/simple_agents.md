# 간단한 에이전트

## 워크플로우

Isaac Lab에서는 `isaaclab_tasks` 확장에 포함된 벤치마크 환경 모음도 제공합니다. OpenAI Gym 레지스트리를 사용하여 이러한 환경들을 등록합니다. 각 환경마다 장면, 관측값, 보상 및 액션 공간을 정의하는 기본 구성 파일을 제공합니다.

OpenAI Gym에 등록된 사용 가능한 환경 목록은 다음 명령을 실행하여 확인할 수 있습니다:

### Linux

```bash
./isaaclab.sh -p scripts/environments/list_envs.py
```

### Windows

```batch
isaaclab.bat -p scripts\environments\list_envs.py
```

### 더미 에이전트

이것들은 제로 또는 랜덤 액션을 출력하는 더미 에이전트들을 포함합니다. 환경이 올바르게 구성되었는지 확인하는 데 유용합니다.

- 카트폴 예제에서의 제로 액션 에이전트

### Linux

```bash
./isaaclab.sh -p scripts/environments/zero_agent.py --task Isaac-Cartpole-v0 --num_envs 32
```

### Windows

```batch
isaaclab.bat -p scripts\environments\zero_agent.py --task Isaac-Cartpole-v0 --num_envs 32
```

- 카트폴 예제에서의 랜덤 액션 에이전트:

### Linux

```bash
./isaaclab.sh -p scripts/environments/random_agent.py --task Isaac-Cartpole-v0 --num_envs 32
```

### Windows

```batch
isaaclab.bat -p scripts\environments\random_agent.py --task Isaac-Cartpole-v0 --num_envs 32
```

### 상태 머신

환경에 대한 수작업 상태 머신 예제를 포함합니다. 이러한 예제들은 환경과 제공된 인터페이스를 사용하는 방법을 이해하는 데 도움이 됩니다. 상태 머신은 [warp](https://github.com/NVIDIA/warp)로 작성되었으며, CUDA 커널을 사용하여 많은 환경 수에 대해 효율적인 실행을 허용합니다.

- 로봇 팔을 사용하여 큐브를 들어 올려 원하는 자세로 놓기:

### Linux

```bash
./isaaclab.sh -p scripts/environments/state_machine/lift_cube_sm.py --num_envs 32
```

### Windows

```batch
isaaclab.bat -p scripts\environments\state_machine\lift_cube_sm.py --num_envs 32
```

- 로봇 팔을 사용하여 변형 가능한 테디 베어를 들어 올려 원하는 자세로 놓기:

### Linux

```bash
./isaaclab.sh -p scripts/environments/state_machine/lift_teddy_bear.py
```

### Windows

```batch
isaaclab.bat -p scripts\environments\state_machine\lift_teddy_bear.py
```
