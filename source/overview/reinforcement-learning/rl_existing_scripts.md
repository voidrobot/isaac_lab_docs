# 강화 학습 스크립트

다양한 강화 학습 라이브러리를 위한 래퍼를 제공합니다. 이러한 래퍼는 환경에서 데이터를 해당 라이브러리의 함수 인수와 반환 타입으로 변환합니다.

## RL-Games

#### 주의
Ray 워크플로를 사용한 분산 훈련 또는 하이퍼파라미터 튜닝에서 RL-Games을 사용할 때, Ray와 관련된 보안 위험으로 인해 이 워크플로는 엄격하게 제어된 네트워크 환경 외부에서 사용되지 않도록 설계되었습니다.

- [RL-Games](https://github.com/Denys88/rl_games)를 사용한 `Isaac-Ant-v0` 에이전트 훈련:

  ### Linux

  ```bash
  # 파이썬 모듈 설치 (rl-games용)
  ./isaaclab.sh -i rl_games
  # 훈련 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/rl_games/train.py --task Isaac-Ant-v0 --headless
  # 32개 환경으로 플레이 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/rl_games/play.py --task Isaac-Ant-v0 --num_envs 32 --checkpoint /PATH/TO/model.pth
  # 32개 환경으로 사전 훈련된 체크포인트로 플레이 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/rl_games/play.py --task Isaac-Ant-v0 --num_envs 32 --use_pretrained_checkpoint
  # 훈련된 에이전트의 비디오 녹화 스크립트 실행 (ffmpeg 설치 필요)
  ./isaaclab.sh -p scripts/reinforcement_learning/rl_games/play.py --task Isaac-Ant-v0 --headless --video --video_length 200
  ```

  ### Windows

  ```batch
  :: 파이썬 모듈 설치 (rl-games용)
  isaaclab.bat -i rl_games
  :: 훈련 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\rl_games\train.py --task Isaac-Ant-v0 --headless
  :: 32개 환경으로 플레이 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\rl_games\play.py --task Isaac-Ant-v0 --num_envs 32 --checkpoint /PATH/TO/model.pth
  :: 32개 환경으로 사전 훈련된 체크포인트로 플레이 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\rl_games\play.py --task Isaac-Ant-v0 --num_envs 32 --use_pretrained_checkpoint
  :: 훈련된 에이전트의 비디오 녹화 스크립트 실행 (ffmpeg 설치 필요)
  isaaclab.bat -p scripts\reinforcement_learning\rl_games\play.py --task Isaac-Ant-v0 --headless --video --video_length 200
  ```

## RSL-RL

- [RSL-RL](https://github.com/leggedrobotics/rsl_rl)을 사용한 `Isaac-Reach-Franka-v0` 에이전트 훈련:

  ### Linux

  ```bash
  # 파이썬 모듈 설치 (rsl-rl용)
  ./isaaclab.sh -i rsl_rl
  # 훈련 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Reach-Franka-v0 --headless
  # 32개 환경으로 플레이 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Reach-Franka-v0 --num_envs 32 --load_run run_folder_name --checkpoint /PATH/TO/model.pt
  # 32개 환경으로 사전 훈련된 체크포인트로 플레이 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Reach-Franka-v0 --num_envs 32 --use_pretrained_checkpoint
  # 훈련된 에이전트의 비디오 녹화 스크립트 실행 (ffmpeg 설치 필요)
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Reach-Franka-v0 --headless --video --video_length 200
  ```

  ### Windows

  ```batch
  :: 파이썬 모듈 설치 (rsl-rl용)
  isaaclab.bat -i rsl_rl
  :: 훈련 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\train.py --task Isaac-Reach-Franka-v0 --headless
  :: 32개 환경으로 플레이 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\play.py --task Isaac-Reach-Franka-v0 --num_envs 32 --load_run run_folder_name --checkpoint /PATH/TO/model.pt
  :: 32개 환경으로 사전 훈련된 체크포인트로 플레이 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\play.py --task Isaac-Reach-Franka-v0 --num_envs 32 --use_pretrained_checkpoint
  :: 훈련된 에이전트의 비디오 녹화 스크립트 실행 (ffmpeg 설치 필요)
  isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\play.py --task Isaac-Reach-Franka-v0 --headless --video --video_length 200
  ```
- [RSL-RL](https://github.com/leggedrobotics/rsl_rl)을 사용한 `Isaac-Velocity-Flat-Anymal-D-v0` 에이전트 훈련 및 디스틸링:

  ### Linux

  ```bash
  # 파이썬 모듈 설치 (rsl-rl용)
  ./isaaclab.sh -i rsl_rl
  # 교사 에이전트의 RL 훈련 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Flat-Anymal-D-v0 --headless
  # 교사 에이전트를 학생 에이전트로 디스틸링하는 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Flat-Anymal-D-v0 --headless --agent rsl_rl_distillation_cfg_entry_point --load_run teacher_run_folder_name
  # 64개 환경으로 학생 에이전트 플레이 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Velocity-Flat-Anymal-D-v0 --num_envs 64 --agent rsl_rl_distillation_cfg_entry_point
  ```

  ### Windows

  ```batch
  :: 파이썬 모듈 설치 (rsl-rl용)
  isaaclab.bat -i rsl_rl
  :: 교사 에이전트의 RL 훈련 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\train.py --task Isaac-Velocity-Flat-Anymal-D-v0 --headless
  :: 교사 에이전트를 학생 에이전트로 디스틸링하는 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\train.py --task Isaac-Velocity-Flat-Anymal-D-v0 --headless --agent rsl_rl_distillation_cfg_entry_point --load_run teacher_run_folder_name
  :: 64개 환경으로 학생 에이전트 플레이 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\play.py --task Isaac-Velocity-Flat-Anymal-D-v0 --num_envs 64 --agent rsl_rl_distillation_cfg_entry_point
  ```

## SKRL

- [SKRL](https://skrl.readthedocs.io)을 사용한 `Isaac-Reach-Franka-v0` 에이전트 훈련:

  ### PyTorch

  ### Linux

  ```bash
  # 파이썬 모듈 설치 (skrl용)
  ./isaaclab.sh -i skrl
  # 훈련 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/train.py --task Isaac-Reach-Franka-v0 --headless
  # 32개 환경으로 플레이 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/play.py --task Isaac-Reach-Franka-v0 --num_envs 32 --checkpoint /PATH/TO/model.pt
  # 32개 환경으로 사전 훈련된 체크포인트로 플레이 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/play.py --task Isaac-Reach-Franka-v0 --num_envs 32 --use_pretrained_checkpoint
  # 훈련된 에이전트의 비디오 녹화 스크립트 실행 (ffmpeg 설치 필요)
  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/play.py --task Isaac-Reach-Franka-v0 --headless --video --video_length 200
  ```

  ### Windows

  ```batch
  :: 파이썬 모듈 설치 (skrl용)
  isaaclab.bat -i skrl
  :: 훈련 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\skrl\train.py --task Isaac-Reach-Franka-v0 --headless
  :: 32개 환경으로 플레이 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\skrl\play.py --task Isaac-Reach-Franka-v0 --num_envs 32 --checkpoint /PATH/TO/model.pt
  :: 32개 환경으로 사전 훈련된 체크포인트로 플레이 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\skrl\play.py --task Isaac-Reach-Franka-v0 --num_envs 32 --use_pretrained_checkpoint
  :: 훈련된 에이전트의 비디오 녹화 스크립트 실행 (ffmpeg 설치 필요)
  isaaclab.bat -p scripts\reinforcement_learning\skrl\play.py --task Isaac-Reach-Franka-v0 --headless --video --video_length 200
  ```

  ### JAX

  #### 경고
  skrl 및 그 종속성을 설치하기 전에 [JAX를 수동으로 설치](https://jax.readthedocs.io/en/latest/installation.html)하는 것이 좋습니다. JAX는 기본적으로 CPU 버전을 설치하기 때문입니다. 자세한 내용은 **skrl** [설치](https://skrl.readthedocs.io/en/latest/intro/installation.html) 페이지를 참조하세요. JAX GPU 지원은 Linux에서만 가능하다는 점에 유의하세요.

  JAX 0.6.0 이상 (CuDNN v9.8 기반)은 Isaac Lab의 PyTorch 2.7 (CuDNN v9.7 기반)과 호환되지 않으므로 지원되지 않습니다.
  CUDA 12용 호환 가능한 JAX 버전을 설치하려면 `pip install "jax[cuda12]<0.6.0" "flax<0.10.7"`을 사용하세요(예시).

  ```bash
  # 파이썬 모듈 설치 (skrl용)
  ./isaaclab.sh -i skrl
  # 토치 2.7용 jax<0.6.0 설치
  ./isaaclab.sh -p -m pip install "jax[cuda12]<0.6.0" "flax<0.10.7"
  # JAX용 skrl 종속성 설치
  ./isaaclab.sh -p -m pip install skrl["jax"]
  # 훈련 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/train.py --task Isaac-Reach-Franka-v0 --headless --ml_framework jax
  # 32개 환경으로 플레이 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/play.py --task Isaac-Reach-Franka-v0 --num_envs 32  --ml_framework jax --checkpoint /PATH/TO/model.pt
  # 훈련된 에이전트의 비디오 녹화 스크립트 실행 (ffmpeg 설치 필요)
  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/play.py --task Isaac-Reach-Franka-v0 --headless --ml_framework jax --video --video_length 200
  ```

  - skrl을 사용한 멀티 에이전트 환경 `Isaac-Shadow-Hand-Over-Direct-v0` 훈련:

  ### Linux

  ```bash
  # 파이썬 모듈 설치 (skrl용)
  ./isaaclab.sh -i skrl
  # MAPPO 알고리즘을 사용한 훈련 스크립트 실행 (IPPO도 지원됨)
  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/train.py --task Isaac-Shadow-Hand-Over-Direct-v0 --headless --algorithm MAPPO
  # 32개 환경으로 MAPPO 알고리즘을 사용한 플레이 스크립트 실행 (IPPO도 지원됨)
  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/play.py --task Isaac-Shadow-Hand-Over-Direct-v0 --num_envs 32 --algorithm MAPPO --checkpoint /PATH/TO/model.pt
  ```

  ### Windows

  ```batch
  :: 파이썬 모듈 설치 (skrl용)
  isaaclab.bat -i skrl
  :: MAPPO 알고리즘을 사용한 훈련 스크립트 실행 (IPPO도 지원됨)
  isaaclab.bat -p scripts\reinforcement_learning\skrl\train.py --task Isaac-Shadow-Hand-Over-Direct-v0 --headless --algorithm MAPPO
  :: 32개 환경으로 MAPPO 알고리즘을 사용한 플레이 스크립트 실행 (IPPO도 지원됨)
  isaaclab.bat -p scripts\reinforcement_learning\skrl\play.py --task Isaac-Shadow-Hand-Over-Direct-v0 --num_envs 32 --algorithm MAPPO --checkpoint /PATH/TO/model.pt
  ```

## Stable-Baselines3

- Training an agent with
  [Stable-Baselines3](https://stable-baselines3.readthedocs.io/en/master/index.html)
  on `Isaac-Velocity-Flat-Unitree-A1-v0`:

  ### Linux

  ```bash
  # 파이썬 모듈 설치 (stable-baselines3 용)
  ./isaaclab.sh -i sb3
  # 훈련 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/sb3/train.py --task Isaac-Velocity-Flat-Unitree-A1-v0 --headless
  # 32개의 환경에서 플레이하기 위한 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/sb3/play.py --task Isaac-Velocity-Flat-Unitree-A1-v0 --num_envs 32 --checkpoint /PATH/TO/model.zip
  # 32개의 환경에서 사전 훈련된 체크포인트로 플레이하기 위한 스크립트 실행
  ./isaaclab.sh -p scripts/reinforcement_learning/sb3/play.py --task Isaac-Velocity-Flat-Unitree-A1-v0 --num_envs 32 --use_pretrained_checkpoint
  # 훈련된 에이전트의 비디오 녹화 스크립트 실행 (`ffmpeg` 설치 필요)
  ./isaaclab.sh -p scripts/reinforcement_learning/sb3/play.py --task Isaac-Velocity-Flat-Unitree-A1-v0 --headless --video --video_length 200
  ```

  ### Windows

  ```batch
  :: 파이썬 모듈 설치 (stable-baselines3 용)
  isaaclab.bat -i sb3
  :: 훈련 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\sb3\train.py --task Isaac-Velocity-Flat-Unitree-A1-v0 --headless
  :: 32개의 환경에서 플레이하기 위한 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\sb3\play.py --task Isaac-Velocity-Flat-Unitree-A1-v0 --num_envs 32 --checkpoint /PATH/TO/model.zip
  :: 32개의 환경에서 사전 훈련된 체크포인트로 플레이하기 위한 스크립트 실행
  isaaclab.bat -p scripts\reinforcement_learning\sb3\play.py --task Isaac-Velocity-Flat-Unitree-A1-v0 --num_envs 32 --use_pretrained_checkpoint
  :: 훈련된 에이전트의 비디오 녹화 스크립트 실행 (`ffmpeg` 설치 필요)
  isaaclab.bat -p scripts\reinforcement_learning\sb3\play.py --task Isaac-Velocity-Flat-Unitree-A1-v0 --headless --video --video_length 200
  ```

위 스크립트들은 모두 저장소 루트의 `logs` 디렉터리에서 [Tensorboard](https://www.tensorflow.org/tensorboard)로 훈련 진행 상황을 로깅합니다. 로그 디렉터리는 `logs/<library>/<task>/<date-time>` 형식을 따르며, 여기서 `<library>`는 학습 프레임워크의 이름, `<task>`는 태스크 이름, `<date-time>`은 훈련 스크립트가 실행된 타임스탬프입니다.

로그를 보려면 다음을 실행하세요:

### Linux

```bash
# 저장소 루트 디렉터리에서 실행
./isaaclab.sh -p -m tensorboard.main --logdir=logs
```

### Windows

```batch
:: 저장소 루트 디렉터리에서 실행
isaaclab.bat -p -m tensorboard.main --logdir=logs
```
