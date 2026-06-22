# 훈련 환경

훈련을 실행하려면 Isaac Lab 표준 워크플로우를 따릅니다. Isaac Lab에 처음이라면 [Quickstart Guide를 검토하는 것이 좋습니다](https://isaac-sim.github.io/IsaacLab/main/source/setup/quickstart.html#).

현재 지원되는 작업은 다음과 같습니다:

* Isaac-Cartpole-Direct-v0
* Isaac-Cartpole-v0
* Isaac-Cartpole-RGB-Camera-Direct-v0
* Isaac-Cartpole-Depth-Camera-Direct-v0
* Isaac-Ant-Direct-v0
* Isaac-Ant-v0
* Isaac-Humanoid-Direct-v0
* Isaac-Humanoid-v0
* Isaac-Velocity-Flat-Anymal-B-v0
* Isaac-Velocity-Flat-Anymal-C-v0
* Isaac-Velocity-Flat-Anymal-D-v0
* Isaac-Velocity-Flat-Cassie-v0
* Isaac-Velocity-Flat-G1-v0
* Isaac-Velocity-Flat-G1-v1 (Sim-to-Real 테스트됨)
* Isaac-Velocity-Flat-H1-v0
* Isaac-Velocity-Flat-Unitree-A1-v0
* Isaac-Velocity-Flat-Unitree-Go1-v0
* Isaac-Velocity-Flat-Unitree-Go2-v0
* Isaac-Reach-Franka-v0
* Isaac-Reach-UR10-v0
* Isaac-Repose-Cube-Allegro-Direct-v0

새로운 실험용 워프 기반 환경:

* Isaac-Cartpole-Direct-Warp-v0
* Isaac-Ant-Direct-Warp-v0
* Isaac-Humanoid-Direct-Warp-v0

기대대로 환경이 로드되는지 확인하려면 액추에이터에 제로 액션을 보내서 테스트해 볼 수 있습니다.
이를 수행하는 방법은 다음과 같으며, 여기서 `TASK_NAME`은 실행하려는 작업의 이름이고, `NUM_ENVS`는 생성하려는 작업 인스턴스의 수입니다.

```shell
./isaaclab.sh -p scripts/environments/zero_agent.py --task TASK_NAME --num_envs NUM_ENVS
```

카트폴에 128개 인스턴스의 경우 다음과 같이 실행할 수 있습니다:

```shell
./isaaclab.sh -p scripts/environments/zero_agent.py --task Isaac-Cartpole-Direct-v0 --num_envs 128
```

랜덤 액션으로 동일한 환경을 실행하려면 다른 스크립트를 사용할 수 있습니다:

```shell
./isaaclab.sh -p scripts/environments/random_agent.py --task Isaac-Cartpole-Direct-v0 --num_envs 128
```

환경을 훈련하기 위해 다양한 RL 프레임워크에 대한 후크(hooks)를 제공합니다. 자세한 내용은 [Reinforcement Learning Scripts 문서](https://isaac-sim.github.io/IsaacLab/main/source/overview/reinforcement-learning/rl_existing_scripts.html)를 참조하세요.

다음은 여러 가지 다른 RL 프레임워크에서 훈련을 실행하는 예시입니다. GPU 병렬 처리의 이점을 더 많이 얻기 위해 환경 수를 명시적으로 4096으로 설정하고 있습니다.

기본적으로 환경은 헤드리스 모드에서 훈련됩니다. 시각화가 필요한 경우 `--visualizer` 옵션을 사용하고 원하는 시각화 도구를 지정하세요.
사용 가능한 옵션은 `newton`, `rerun`, `omniverse`(Isaac Sim 설치 필요)입니다. 여러 시각화 도구를 선택하여 동시에 실행할 수 있다는 점에 유의하세요.

```shell
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Cartpole-Direct-v0 --num_envs 4096
```

```shell
./isaaclab.sh -p scripts/reinforcement_learning/skrl/train.py --task Isaac-Cartpole-Direct-v0 --num_envs 4096
```

```shell
./isaaclab.sh -p scripts/reinforcement_learning/rl_games/train.py --task Isaac-Cartpole-Direct-v0 --num_envs 4096
```

정책이 훈련되면 재생 스크립트를 사용하여 시각화할 수 있습니다. 먼저 훈련된 정책의 체크포인트를 찾아야 합니다. 일반적으로 다음 위치에 저장됩니다:
`logs/NAME_OF_RL_FRAMEWORK/TASK_NAME/DATE`.

예를 들어, rsl_rl 예제의 경우 다음과 같을 수 있습니다:
`logs/rsl_rl/cartpole_direct/2025-08-21_15-45-30/model_299.pt`

이 정책을 실행하려면 다음 명령을 사용할 수 있습니다. 여기서는 시각화 도구를 사용하여 정책의 동작을 확인할 수 있도록 환경 수를 128로 줄이고 `--visualizer newton` 옵션을 추가했습니다:

```shell
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Cartpole-Direct-v0 --num_envs 128 --visualizer newton --checkpoint logs/rsl_rl/cartpole_direct/2025-08-21_15-45-30/model_299.pt
```

다른 프레임워크에도 동일한 접근 방식이 적용됩니다.

모든 프레임워크에서 모든 환경을 지원하는 것은 아니라는 점에 유의하세요. 예를 들어, 여러 locomotion 환경은 rsl_rl 프레임워크에서만 지원됩니다.
