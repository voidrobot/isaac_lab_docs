<a id="tutorial-configure-rl-training"></a>

# RL 에이전트 구성

이전 튜토리얼에서 [Stable-Baselines3](https://stable-baselines3.readthedocs.io/en/master/) 라이브러리를 사용하여 카트폴 균형 잡기 작업을 해결하는 RL 에이전트를 훈련시키는 방법을 살펴보았습니다. 이번 튜토리얼에서는 다양한 RL 라이브러리와 다양한 훈련 알고리즘을 사용하도록 훈련 프로세스를 구성하는 방법을 살펴볼 것입니다.

`scripts/reinforcement_learning` 디렉토리에서 다양한 RL 라이브러리에 대한 스크립트를 찾을 수 있습니다. 이러한 스크립트는 라이브러리 이름을 따서 명명된 하위 디렉터리로 구성되어 있습니다. 각 하위 디렉터리에는 해당 라이브러리에 대한 훈련 및 실행 스크립트가 포함됩니다.

특정 작업에 학습 라이브러리를 구성하려면 학습 에이전트에 대한 구성 파일을 생성해야 합니다. 이 구성 파일은 학습 에이전트의 인스턴스를 생성하는 데 사용되며 훈련 프로세스를 구성하는 데 사용됩니다. [환경 등록](register_rl_env_gym.md#tutorial-register-rl-env-gym) 튜토리얼에서 보여준 환경 등록과 유사하게, `gymnasium.register` 메서드를 사용하여 학습 에이전트를 등록할 수 있습니다.

## 코드

예시로 `isaaclab_tasks` 패키지에 포함된 작업 `Isaac-Cartpole-v0`의 구성을 살펴보겠습니다. 이는 [RL 에이전트와 함께 훈련](run_rl_training.md#tutorial-run-rl-training) 튜토리얼에서 사용한 동일한 작업입니다.

```python
gym.register(
    id="Isaac-Cartpole-v0",
    entry_point="isaaclab.envs:ManagerBasedRLEnv",
    disable_env_checker=True,
    kwargs={
        "env_cfg_entry_point": f"{__name__}.cartpole_env_cfg:CartpoleEnvCfg",
        "rl_games_cfg_entry_point": f"{agents.__name__}:rl_games_ppo_cfg.yaml",
        "rsl_rl_cfg_entry_point": f"{agents.__name__}.rsl_rl_ppo_cfg:CartpolePPORunnerCfg",
        "rsl_rl_with_symmetry_cfg_entry_point": f"{agents.__name__}.rsl_rl_ppo_cfg:CartpolePPORunnerWithSymmetryCfg",
        "skrl_cfg_entry_point": f"{agents.__name__}:skrl_ppo_cfg.yaml",
        "sb3_cfg_entry_point": f"{agents.__name__}:sb3_ppo_cfg.yaml",
    },
```

## 코드 설명

속성 `kwargs`에서 다양한 학습 라이브러리의 구성을 확인할 수 있습니다. 키는 라이브러리의 이름이고 값은 구성 인스턴스의 경로입니다. 이 구성 인스턴스는 문자열, 클래스 또는 클래스의 인스턴스일 수 있습니다. 예를 들어, 키 `"rl_games_cfg_entry_point"`의 값은 RL-Games 라이브러리의 구성 YAML 파일을 가리키는 문자열입니다. 반면에 키 `"rsl_rl_cfg_entry_point"`의 값은 RSL-RL 라이브러리의 구성 클래스를 가리킵니다.

에이전트 구성 진입점을 지정하는 데 사용되는 패턴은 환경 구성 진입점을 지정하는 데 사용되는 패턴과 유사합니다. 즉, 다음은 동일합니다.

### 문자열을 구성 진입점으로 지정

```python
from . import agents

gym.register(
   id="Isaac-Cartpole-v0",
   entry_point="isaaclab.envs:ManagerBasedRLEnv",
   disable_env_checker=True,
   kwargs={
      "env_cfg_entry_point": f"{__name__}.cartpole_env_cfg:CartpoleEnvCfg",
      "rsl_rl_cfg_entry_point": f"{agents.__name__}.rsl_rl_ppo_cfg:CartpolePPORunnerCfg",
   },
)
```

### 클래스를 구성 진입점으로 지정

```python
from . import agents

gym.register(
   id="Isaac-Cartpole-v0",
   entry_point="isaaclab.envs:ManagerBasedRLEnv",
   disable_env_checker=True,
   kwargs={
      "env_cfg_entry_point": f"{__name__}.cartpole_env_cfg:CartpoleEnvCfg",
      "rsl_rl_cfg_entry_point": agents.rsl_rl_ppo_cfg.CartpolePPORunnerCfg,
   },
)
```

첫 번째 코드 블록이 구성 진입점을 지정하는 선호되는 방법입니다. 두 번째 코드 블록은 첫 번째 코드 블록과 동등하지만 구성 클래스의 임포트를 유발하여 임포트 속도를 늦춥니다.这就是为什么我们建议对配置入口点使用字符串的原因。

`scripts/reinforcement_learning` 디렉토리의 모든 스크립트는 기본적으로 `kwargs` 사전에서 `<library_name>_cfg_entry_point`를 읽어 구성 인스턴스를 가져오도록 구성됩니다.

예를 들어, 다음 코드 블록은 Stable-Baselines3 라이브러리의 구성 인스턴스를 읽는 `train.py` 스크립트의 방법을 보여줍니다.

### SB3의 train.py 코드

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause


"""Script to train RL agent with Stable Baselines3."""

"""Launch Isaac Sim Simulator first."""

import argparse
import contextlib
import signal
import sys
from pathlib import Path

from isaaclab.app import AppLauncher

# add argparse arguments
parser = argparse.ArgumentParser(description="Train an RL agent with Stable-Baselines3.")
parser.add_argument("--video", action="store_true", default=False, help="Record videos during training.")
parser.add_argument("--video_length", type=int, default=200, help="Length of the recorded video (in steps).")
parser.add_argument("--video_interval", type=int, default=2000, help="Interval between video recordings (in steps).")
parser.add_argument("--num_envs", type=int, default=None, help="Number of environments to simulate.")
parser.add_argument("--task", type=str, default=None, help="Name of the task.")
parser.add_argument(
    "--agent", type=str, default="sb3_cfg_entry_point", help="Name of the RL agent configuration entry point."
)
parser.add_argument("--seed", type=int, default=None, help="Seed used for the environment")
parser.add_argument("--log_interval", type=int, default=100_000, help="Log data every n timesteps.")
parser.add_argument("--checkpoint", type=str, default=None, help="Continue the training from checkpoint.")
parser.add_argument("--max_iterations", type=int, default=None, help="RL Policy training iterations.")
parser.add_argument("--export_io_descriptors", action="store_true", default=False, help="Export IO descriptors.")
parser.add_argument(
    "--keep_all_info",
    action="store_true",
    default=False,
    help="Use a slower SB3 wrapper but keep all the extra training info.",
)
parser.add_argument(
    "--ray-proc-id", "-rid", type=int, default=None, help="Automatically configured by Ray integration, otherwise None."
)
# append AppLauncher cli args
AppLauncher.add_app_launcher_args(parser)
# parse the arguments
args_cli, hydra_args = parser.parse_known_args()
# always enable cameras to record video
if args_cli.video:
    args_cli.enable_cameras = True

# clear out sys.argv for Hydra
sys.argv = [sys.argv[0]] + hydra_args

# launch omniverse app
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app


def cleanup_pbar(*args):
    """
    A small helper to stop training and
    cleanup progress bar properly on ctrl+c
    """
    import gc

    tqdm_objects = [obj for obj in gc.get_objects() if "tqdm" in type(obj).__name__]
    for tqdm_object in tqdm_objects:
        if "tqdm_rich" in type(tqdm_object).__name__:
            tqdm_object.close()
    raise KeyboardInterrupt


# disable KeyboardInterrupt override
signal.signal(signal.SIGINT, cleanup_pbar)

"""Rest everything follows."""

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

# import logger
logger = logging.getLogger(__name__)
# PLACEHOLDER: Extension template (do not remove this comment)


@hydra_task_config(args_cli.task, args_cli.agent)
def main(env_cfg: ManagerBasedRLEnvCfg | DirectRLEnvCfg | DirectMARLEnvCfg, agent_cfg: dict):
    """Train with stable-baselines agent."""
    # randomly sample a seed if seed = -1
    if args_cli.seed == -1:
        args_cli.seed = random.randint(0, 10000)

    # override configurations with non-hydra CLI arguments
    env_cfg.scene.num_envs = args_cli.num_envs if args_cli.num_envs is not None else env_cfg.scene.num_envs
    agent_cfg["seed"] = args_cli.seed if args_cli.seed is not None else agent_cfg["seed"]
    # max iterations for training
    if args_cli.max_iterations is not None:
        agent_cfg["n_timesteps"] = args_cli.max_iterations * agent_cfg["n_steps"] * env_cfg.scene.num_envs

    # set the environment seed
    # note: certain randomizations occur in the environment initialization so we set the seed here
    env_cfg.seed = agent_cfg["seed"]
    env_cfg.sim.device = args_cli.device if args_cli.device is not None else env_cfg.sim.device

    # directory for logging into
    run_info = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
    log_root_path = os.path.abspath(os.path.join("logs", "sb3", args_cli.task))
    print(f"[INFO] Logging experiment in directory: {log_root_path}")
    # The Ray Tune workflow extracts experiment name using the logging line below, hence,
    # do not change it (see PR #2346, comment-2819298849)
    print(f"Exact experiment name requested from command line: {run_info}")
    log_dir = os.path.join(log_root_path, run_info)
    # dump the configuration into log-directory
    dump_yaml(os.path.join(log_dir, "params", "env.yaml"), env_cfg)
    dump_yaml(os.path.join(log_dir, "params", "agent.yaml"), agent_cfg)

    # save command used to run the script
    command = " ".join(sys.orig_argv)
    (Path(log_dir) / "command.txt").write_text(command)

    # post-process agent configuration
    agent_cfg = process_sb3_cfg(agent_cfg, env_cfg.scene.num_envs)
    # read configurations about the agent-training
    policy_arch = agent_cfg.pop("policy")
    n_timesteps = agent_cfg.pop("n_timesteps")

    # set the IO descriptors export flag if requested
    if isinstance(env_cfg, ManagerBasedRLEnvCfg):
        env_cfg.export_io_descriptors = args_cli.export_io_descriptors
    else:
        logger.warning(
            "IO descriptors are only supported for manager based RL environments. No IO descriptors will be exported."
        )

    # set the log directory for the environment (works for all environment types)
    env_cfg.log_dir = log_dir

    # create isaac environment
    env = gym.make(args_cli.task, cfg=env_cfg, render_mode="rgb_array" if args_cli.video else None)

    # convert to single-agent instance if required by the RL algorithm
    if isinstance(env.unwrapped, DirectMARLEnv):
        env = multi_agent_to_single_agent(env)

    # wrap for video recording
    if args_cli.video:
        video_kwargs = {
            "video_folder": os.path.join(log_dir, "videos", "train"),
            "step_trigger": lambda step: step % args_cli.video_interval == 0,
            "video_length": args_cli.video_length,
            "disable_logger": True,
        }
        print("[INFO] Recording videos during training.")
        print_dict(video_kwargs, nesting=4)
        env = gym.wrappers.RecordVideo(env, **video_kwargs)

    start_time = time.time()

    # wrap around environment for stable baselines
    env = Sb3VecEnvWrapper(env, fast_variant=not args_cli.keep_all_info)

    norm_keys = {"normalize_input", "normalize_value", "clip_obs"}
    norm_args = {}
    for key in norm_keys:
        if key in agent_cfg:
            norm_args[key] = agent_cfg.pop(key)

    if norm_args and norm_args.get("normalize_input"):
        print(f"Normalizing input, {norm_args=}")
        env = VecNormalize(
            env,
            training=True,
            norm_obs=norm_args["normalize_input"],
            norm_reward=norm_args.get("normalize_value", False),
            clip_obs=norm_args.get("clip_obs", 100.0),
            gamma=agent_cfg["gamma"],
            clip_reward=np.inf,
        )

    # create agent from stable baselines
    agent = PPO(policy_arch, env, verbose=1, tensorboard_log=log_dir, **agent_cfg)
    if args_cli.checkpoint is not None:
        agent = agent.load(args_cli.checkpoint, env, print_system_info=True)

    # callbacks for agent
    checkpoint_callback = CheckpointCallback(save_freq=1000, save_path=log_dir, name_prefix="model", verbose=2)
    callbacks = [checkpoint_callback, LogEveryNTimesteps(n_steps=args_cli.log_interval)]

    # train the agent
    with contextlib.suppress(KeyboardInterrupt):
        agent.learn(
            total_timesteps=n_timesteps,
            callback=callbacks,
            progress_bar=True,
            log_interval=None,
        )
    # save the final model
    agent.save(os.path.join(log_dir, "model"))
    print("Saving to:")
    print(os.path.join(log_dir, "model.zip"))

    if isinstance(env, VecNormalize):
        print("Saving normalization")
        env.save(os.path.join(log_dir, "model_vecnormalize.pkl"))

    print(f"Training time: {round(time.time() - start_time, 2)} seconds")

    # close the simulator
    env.close()


if __name__ == "__main__":
    # run the main function
    main()
    # close sim app
    simulation_app.close()
```

인수 `--agent`는 사용할 학습 라이브러리를 지정하는 데 사용됩니다. 이는 `kwargs` 사전에서 구성 인스턴스를 검색하는 데 사용됩니다. `--agent` 인수를 전달하여 대체 구성 인스턴스를 수동으로 지정할 수 있습니다.

## 코드 실행

카트폴 균형 잡기 작업의 경우 RSL-RL 라이브러리는 두 가지 구성 인스턴스를 제공하므로 `--agent` 인수를 사용하여 사용할 구성 인스턴스를 지정할 수 있습니다.

* 표준 PPO 구성으로 훈련:
  ```bash
  # standard PPO training
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Cartpole-v0 --headless \
    --run_name ppo
  ```
* 대칭 증강을 사용한 PPO 구성으로 훈련:
  ```bash
  # PPO training with symmetry augmentation
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Cartpole-v0 --headless \
    --agent rsl_rl_with_symmetry_cfg_entry_point \
    --run_name ppo_with_symmetry_data_augmentation

  # you can use hydra to disable symmetry augmentation but enable mirror loss computation
  ./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Cartpole-v0 --headless \
    --agent rsl_rl_with_symmetry_cfg_entry_point \
    --run_name ppo_without_symmetry_data_augmentation \
    agent.algorithm.symmetry_cfg.use_data_augmentation=false
  ```

인수 `--run_name`은 실행 이름을 지정하는 데 사용됩니다. 이는 `logs/rsl_rl/cartpole` 디렉터리 내에 실행용 디렉터리를 생성하는 데 사용됩니다.
