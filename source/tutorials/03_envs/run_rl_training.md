<a id="tutorial-run-rl-training"></a>

# RL 에이전트로 훈련하기

이전 튜토리얼에서는 RL 태스크 환경을 정의하는 방법, `gym` 레지스트리에 등록하는 방법, 그리고 랜덤 에이전트를 사용하여 상호작용하는 방법을 다뤘습니다. 이제 다음 단계로 넘어가서, 태스크를 해결할 RL 에이전트를 훈련하는 방법을 살펴보겠습니다.

[`envs.ManagerBasedRLEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)는 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인터페이스를 준수하지만, 정확히 동일한 `gym` 환경은 아닙니다. 환경의 입력과 출력은 넘파이 배열이 아닌, 첫 번째 차원이 환경 인스턴스 수인 토치 텐서로 구성됩니다.

또한 대부분의 RL 라이브러리는 자체적인 환경 인터페이스 변형을 기대합니다. 예를 들어, [Stable-Baselines3](https://stable-baselines3.readthedocs.io/en/master/)는 환경이 자신의 [VecEnv API](https://stable-baselines3.readthedocs.io/en/master/guide/vec_envs.html#vecenv-api-vs-gym-api)를 준수하기를 기대하며, 이는 단일 텐서 대신 넘파이 배열 리스트를 요구합니다. 비슷하게, [RSL-RL](https://github.com/leggedrobotics/rsl_rl), [RL-Games](https://github.com/Denys88/rl_games) 및 [SKRL](https://skrl.readthedocs.io)는 서로 다른 인터페이스를 기대합니다. 하나의 통합 솔루션이 없으므로, 우리는 [`envs.ManagerBasedRLEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)를 특정 학습 라이브러리에 기반시키지 않습니다. 대신, 환경을 기대되는 인터페이스로 변환하는 래퍼를 구현합니다. 이들 래퍼는 [`isaaclab_rl`](../../api/lab_rl/isaaclab_rl.md#module-isaaclab_rl) 모듈에 명시되어 있습니다.

이 튜토리얼에서는 [Stable-Baselines3](https://stable-baselines3.readthedocs.io/en/master/)를 사용하여 카트폴 균형 태스크를 해결하는 RL 에이전트를 훈련할 것입니다.

## 코드

이 튜토리얼에서는 `scripts/reinforcement_learning/sb3` 디렉터리의 [Stable-Baselines3](https://stable-baselines3.readthedocs.io/en/master/) 워크플로우에서 훈련 스크립트를 사용합니다.

### train.py 코드

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause


"""Stable Baselines3를 사용하여 RL 에이전트를 훈련하는 스크립트."""

"""Isaac Sim 시뮬레이터 먼저 실행하기."""

import argparse
import contextlib
import signal
import sys
from pathlib import Path

from isaaclab.app import AppLauncher

# argparse 인수 추가
parser = argparse.ArgumentParser(description="Stable-Baselines3로 RL 에이전트 훈련하기.")
parser.add_argument("--video", action="store_true", default=False, help="훈련 중 비디오 기록.")
parser.add_argument("--video_length", type=int, default=200, help="기록된 비디오 길이(단계).")
parser.add_argument("--video_interval", type=int, default=2000, help="비디오 기록 간격(단계).")
parser.add_argument("--num_envs", type=int, default=None, help="시뮬레이션할 환경 수.")
parser.add_argument("--task", type=str, default=None, help="태스크 이름.")
parser.add_argument(
    "--agent", type=str, default="sb3_cfg_entry_point", help="RL 에이전트 구성 엔트리 포인트 이름."
)
parser.add_argument("--seed", type=int, default=None, help="환경에 사용되는 시드")
parser.add_argument("--log_interval", type=int, default=100_000, help="n 타임스텝마다 데이터 기록.")
parser.add_argument("--checkpoint", type=str, default=None, help="체크포인트에서 훈련 계속하기.")
parser.add_argument("--max_iterations", type=int, default=None, help="RL 정책 훈련 반복 횟수.")
parser.add_argument("--export_io_descriptors", action="store_true", default=False, help="IO 설명자 내보내기.")
parser.add_argument(
    "--keep_all_info",
    action="store_true",
    default=False,
    help="느린 SB3 래퍼를 사용하지만 추가 훈련 정보를 모두 유지합니다."
)
parser.add_argument(
    "--ray-proc-id", "-rid", type=int, default=None, help="Ray 통합에 의해 자동 구성되거나 None이 됨."
)
# AppLauncher CLI 인수 추가
AppLauncher.add_app_launcher_args(parser)
# 인수 파싱
args_cli, hydra_args = parser.parse_known_args()
# 비디오 기록을 위해 카메라 항상 활성화
if args_cli.video:
    args_cli.enable_cameras = True

# Hydra를 위해 sys.argv 정리
sys.argv = [sys.argv[0]] + hydra_args

# 옴니버스 앱 실행
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app


def cleanup_pbar(*args):
    """
    Ctrl+C 시 훈련을 중지하고
    진행률 표시줄을 올바르게 정리하는 작은 헬퍼
    """
    import gc

    tqdm_objects = [obj for obj in gc.get_objects() if "tqdm" in type(obj).__name__]
    for tqdm_object in tqdm_objects:
        if "tqdm_rich" in type(tqdm_object).__name__:
            tqdm_object.close()
    raise KeyboardInterrupt


# KeyboardInterrupt 오버라이드 비활성화
signal.signal(signal.SIGINT, cleanup_pbar)

"""나머지는 모두 이어집니다."""

import logging
import os
import random
import time
from datetime import datetime

import gymnasium as gym
import numpy as np
from stable_baselines3 import PPO
from stable_baselines3.common.callbacks import CheckpointCallback, LogEveryNTimesteps
from stable_baselines3.common.vec_env import VecNormalize

from isaaclab.envs import (
    DirectMARLEnv,
    DirectMARLEnvCfg,
    DirectRLEnvCfg,
    ManagerBasedRLEnvCfg,
    multi_agent_to_single_agent,
)
from isaaclab.utils.dict import print_dict
from isaaclab.utils.io import dump_yaml

from isaaclab_rl.sb3 import Sb3VecEnvWrapper, process_sb3_cfg

import isaaclab_tasks  # noqa: F401
from isaaclab_tasks.utils.hydra import hydra_task_config

# 로거 가져오기
logger = logging.getLogger(__name__)
# PLACEHOLDER: 확장 템플릿 (이 주석 삭제하지 마세요)


@hydra_task_config(args_cli.task, args_cli.agent)
def main(env_cfg: ManagerBasedRLEnvCfg | DirectRLEnvCfg | DirectMARLEnvCfg, agent_cfg: dict):
    """stable-baselines 에이전트로 훈련하기."""
    # seed = -1이면 무작위로 시드 샘플링
    if args_cli.seed == -1:
        args_cli.seed = random.randint(0, 10000)

    # 비-Hydra CLI 인수로 구성 재정의
    env_cfg.scene.num_envs = args_cli.num_envs if args_cli.num_envs is not None else env_cfg.scene.num_envs
    agent_cfg["seed"] = args_cli.seed if args_cli.seed is not None else agent_cfg["seed"]
    # 훈련 최대 반복 횟수
    if args_cli.max_iterations is not None:
        agent_cfg["n_timesteps"] = args_cli.max_iterations * agent_cfg["n_steps"] * env_cfg.scene.num_envs

    # 환경 시드 설정
    # 참고: 환경 초기화 중에 특정 랜덤화가 발생하므로 여기에서 시드를 설정합니다
    env_cfg.seed = agent_cfg["seed"]
    env_cfg.sim.device = args_cli.device if args_cli.device is not None else env_cfg.sim.device

    # 로깅을 위한 디렉터리
    run_info = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
    log_root_path = os.path.abspath(os.path.join("logs", "sb3", args_cli.task))
    print(f"[INFO] 실험을 다음 디렉터리에 기록 중: {log_root_path}")
    # Ray Tune 워크플로우는 아래 로깅 라인을 사용하여 실험 이름을 추출하므로,
    #これを変更しないでください（PR #2346, comment-2819298849を参照）
    print(f"Exact experiment name requested from command line: {run_info}")
    log_dir = os.path.join(log_root_path, run_info)
    # 구성 로그 디렉터리에 덤프하기
    dump_yaml(os.path.join(log_dir, "params", "env.yaml"), env_cfg)
    dump_yaml(os.path.join(log_dir, "params", "agent.yaml"), agent_cfg)

    # 스크립트 실행 명령 저장
    command = " ".join(sys.orig_argv)
    (Path(log_dir) / "command.txt").write_text(command)

    # 에이전트 구성 후처리
    agent_cfg = process_sb3_cfg(agent_cfg, env_cfg.scene.num_envs)
    # 에이전트 훈련에 대한 구성 읽기
    policy_arch = agent_cfg.pop("policy")
    n_timesteps = agent_cfg.pop("n_timesteps")

    # 요청된 경우 IO 설명자 내보내기 플래그 설정
    if isinstance(env_cfg, ManagerBasedRLEnvCfg):
        env_cfg.export_io_descriptors = args_cli.export_io_descriptors
    else:
        logger.warning(
            "IO 설명자는 관리자 기반 RL 환경에서만 지원됩니다. IO 설명자를 내보내지 않습니다."
        )

    # 환경에 대한 로그 디렉터리 설정 (모든 환경 유형에 적용됨)
    env_cfg.log_dir = log_dir

    # 아이작 환경 생성
    env = gym.make(args_cli.task, cfg=env_cfg, render_mode="rgb_array" if args_cli.video else None)

    # RL 알고리즘이 요구하는 경우 단일 에이전트 인스턴스로 변환
    if isinstance(env.unwrapped, DirectMARLEnv):
        env = multi_agent_to_single_agent(env)

    # 비디오 기록을 위한 래핑
    if args_cli.video:
        video_kwargs = {
            "video_folder": os.path.join(log_dir, "videos", "train"),
            "step_trigger": lambda step: step % args_cli.video_interval == 0,
            "video_length": args_cli.video_length,
            "disable_logger": True,
        }
        print("[INFO] 훈련 중 비디오 기록 중입니다.")
        print_dict(video_kwargs, nesting=4)
        env = gym.wrappers.RecordVideo(env, **video_kwargs)

    start_time = time.time()

    # Stable Baselines를 위한 환경 래핑
    env = Sb3VecEnvWrapper(env, fast_variant=not args_cli.keep_all_info)

    norm_keys = {"normalize_input", "normalize_value", "clip_obs"}
    norm_args = {}
    for key in norm_keys:
        if key in agent_cfg:
            norm_args[key] = agent_cfg.pop(key)

    if norm_args and norm_args.get("normalize_input"):
        print(f"입력 정규화 중, {norm_args=}")
        env = VecNormalize(
            env,
            training=True,
            norm_obs=norm_args["normalize_input"],
            norm_reward=norm_args.get("normalize_value", False),
            clip_obs=norm_args.get("clip_obs", 100.0),
            gamma=agent_cfg["gamma"],
            clip_reward=np.inf,
        )

    # Stable Baselines에서 에이전트 생성
    agent = PPO(policy_arch, env, verbose=1, tensorboard_log=log_dir, **agent_cfg)
    if args_cli.checkpoint is not None:
        agent = agent.load(args_cli.checkpoint, env, print_system_info=True)

    # 에이전트를 위한 콜백
    checkpoint_callback = CheckpointCallback(save_freq=1000, save_path=log_dir, name_prefix="model", verbose=2)
    callbacks = [checkpoint_callback, LogEveryNTimesteps(n_steps=args_cli.log_interval)]

    # 에이전트 훈련
    with contextlib.suppress(KeyboardInterrupt):
        agent.learn(
            total_timesteps=n_timesteps,
            callback=callbacks,
            progress_bar=True,
            log_interval=None,
        )
    # 최종 모델 저장
    agent.save(os.path.join(log_dir, "model"))
    print("저장 중:")
    print(os.path.join(log_dir, "model.zip"))

    if isinstance(env, VecNormalize):
        print("정규화 저장 중")
        env.save(os.path.join(log_dir, "model_vecnormalize.pkl"))

    print(f"훈련 시간: {round(time.time() - start_time, 2)}초")

    # 시뮬레이터 종료
    env.close()


if __name__ == "__main__":
    # 메인 함수 실행
    main()
    # 시뮬레이터 앱 종료
    simulation_app.close()
```

## 코드 설명

위 코드의 대부분은 로깅 디렉토리를 생성하고, 파싱된 구성 설정을 저장하며, Stable-Baselines3의 다양한 구성 요소를 설정하는 보일러플레이트 코드입니다. 이 튜토리얼에서 중요한 부분은 환경을 생성하고 Stable-Baselines3 래퍼로 감싸는 것입니다.

위 코드에서는 세 가지 래퍼가 사용됩니다:

1. [`gymnasium.wrappers.RecordVideo`](https://gymnasium.farama.org/api/wrappers/misc_wrappers/#gymnasium.wrappers.RecordVideo): 이 래퍼는 환경의 비디오를 기록하고 지정된 디렉터리에 저장합니다. 훈련 중 에이전트의 행동을 시각화하는 데 유용합니다.
2. `wrappers.sb3.Sb3VecEnvWrapper`: 이 래퍼는 환경을 Stable-Baselines3와 호환되는 환경으로 변환합니다.
3. [stable_baselines3.common.vec_env.VecNormalize](https://stable-baselines3.readthedocs.io/en/master/guide/vec_envs.html#vecnormalize): 이 래퍼는 환경의 관측값과 보상을 정규화합니다.

이러한 래퍼 각각은 `env = wrapper(env, *args, **kwargs)`를 반복하여 이전 래퍼 주변에 래핑됩니다. 최종 환경은 에이전트를 훈련하는 데 사용됩니다. 이러한 래퍼의 작동 방식에 대한 자세한 내용은 [Wrapping environments](../../how-to/wrap_rl_env.md#how-to-env-wrappers) 문서를 참조하세요.

## 코드 실행

Stable-Baselines3의 PPO 에이전트를 훈련하여 카트폴 균형 작업을 해결합니다.

### 에이전트 훈련

에이전트를 훈련하는 세 가지 주요 방법이 있습니다. 각각 장단점이 있으며, 사용 사례에 따라 선호하는 방법을 선택할 수 있습니다.

#### 헤드리스 실행

`--headless` 플래그가 설정되면 훈련 중 시뮬레이션이 렌더링되지 않습니다. 이는 원격 서버에서 훈련하거나 시뮬레이션을 표시하지 않을 때 유용합니다. 일반적으로 물리 시뮬레이션 단계만 수행되므로 훈련 속도가 빨라집니다.

```bash
./isaaclab.sh -p scripts/reinforcement_learning/sb3/train.py --task Isaac-Cartpole-v0 --num_envs 64 --headless
```

#### 헤드리스 실행 및 오프스크린 렌더링

위의 명령어는 시뮬레이션을 렌더링하지 않으므로 훈련 중 에이전트의 행동을 시각화할 수 없습니다. 에이전트의 행동을 시각화하려면 `--enable_cameras` 플래그를 전달하여 오프스크린 렌더링을 활성화해야 합니다. 또한 `--video` 플래그를 전달하여 훈련 중 에이전트의 행동을 기록하는 비디오를 생성할 수 있습니다.

```bash
./isaaclab.sh -p scripts/reinforcement_learning/sb3/train.py --task Isaac-Cartpole-v0 --num_envs 64 --headless --video
```

비디오는 `logs/sb3/Isaac-Cartpole-v0/<run-dir>/videos/train` 디렉터리에 저장됩니다. 어떤 비디오 플레이어든 사용하여 이 비디오를 열 수 있습니다.

#### 인터랙티브 실행

위 두 가지 방법은 에이전트 훈련에 유용하지만, 시뮬레이션과 상호작용하여 진행 상황을 확인할 수는 없습니다. 이 경우 `--headless` 플래그를 생략하고 훈련 스크립트를 다음과 같이 실행할 수 있습니다:

```bash
./isaaclab.sh -p scripts/reinforcement_learning/sb3/train.py --task Isaac-Cartpole-v0 --num_envs 64
```

이 명령어는 Isaac Sim 창을 열고 환경에서 에이전트 훈련 과정을 볼 수 있게 해줍니다. 그러나 화면에 시뮬레이션이 렌더링되므로 훈련 속도가 느려질 수 있습니다. 해결 방법으로 화면 오른쪽 아래에 도킹된 `"Isaac Lab"` 창에서 다양한 렌더 모드를 전환할 수 있습니다. 이러한 렌더 모드에 대해 자세히 알아보려면 [`sim.SimulationContext.RenderMode`](../../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationContext.RenderMode) 클래스를 참조하세요.

### 로그 보기

별도의 터미널에서 다음 명령을 실행하여 훈련 진행 상황을 모니터링할 수 있습니다:

```bash
# 저장소 루트 디렉터리에서 실행
./isaaclab.sh -p -m tensorboard.main --logdir logs/sb3/Isaac-Cartpole-v0
```

### 훈련된 에이전트 실행

훈련이 완료되면 다음 명령을 실행하여 훈련된 에이전트를 시각화할 수 있습니다:

```bash
# 저장소 루트 디렉터리에서 실행
./isaaclab.sh -p scripts/reinforcement_learning/sb3/play.py --task Isaac-Cartpole-v0 --num_envs 32 --use_last_checkpoint
```

위 명령어는 `logs/sb3/Isaac-Cartpole-v0` 디렉터리에서 최신 체크포인트를 로드합니다. `--checkpoint` 플래그를 전달하여 특정 체크포인트를 지정할 수도 있습니다.
