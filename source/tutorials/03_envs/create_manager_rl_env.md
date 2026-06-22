<a id="tutorial-create-manager-rl-env"></a>

# 관리자 기반 강화 학습 환경 생성

[Manager-Based 기본 환경 생성](create_manager_base_env.md#tutorial-create-manager-base-env)에서 기본 환경을 생성하는 방법을 배운 후, 이제 강화 학습을 위한 관리자 기반 태스크 환경을 생성하는 방법을 살펴볼 것입니다.

기본 환경은 에이전트가 환경에 명령을 보내고 환경으로부터 관측값을 받을 수 있는 sense-act 환경으로 설계되었습니다. 이 최소한의 인터페이스는 전통적인 움직임 계획 및 제어와 같은 많은 애플리케이션에 충분합니다. 그러나 많은 애플리케이션에서는 에이전트의 학습 목표로 자주 사용되는 태스크 명세가 필요합니다. 예를 들어, 내비게이션 태스크에서 에이전트는 목표 위치에 도달해야 할 수 있습니다. 이를 위해 우리는 기본 환경을 확장하여 태스크 명세를 포함하는 [`envs.ManagerBasedRLEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) 클래스를 사용합니다.

Isaac Lab의 다른 구성 요소와 마찬가지로, 기본 클래스 [`envs.ManagerBasedRLEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)를 직접 수정하는 대신, 사용자는 태스크 환경에 대한 구성 [`envs.ManagerBasedRLEnvCfg`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg)를 단순히 구현하는 것을 권장합니다. 이 접근 방식은 태스크 명세와 환경 구현을 분리하여, 동일한 환경의 구성 요소를 다른 태스크에 재사용하기 쉽게 만듭니다.

이 튜토리얼에서는 [`envs.ManagerBasedRLEnvCfg`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg)를 사용하여 폴을 똑바로 세우는 관리자 기반 태스크로 카트폴 환경을 구성하는 방법을 배울 것입니다. 보상 항목, 종료 조건, 커리큘럼 및 명령을 사용하여 태스크를 지정하는 방법도 배우게 됩니다.

## 코드

이 튜토리얼에서는 `isaaclab_tasks.manager_based.classic.cartpole` 모듈에 정의된 카트폴 환경을 사용합니다.

### cartpole_env_cfg.py 코드

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

import math

import isaaclab.sim as sim_utils
from isaaclab.assets import ArticulationCfg, AssetBaseCfg
from isaaclab.envs import ManagerBasedRLEnvCfg
from isaaclab.managers import EventTermCfg as EventTerm
from isaaclab.managers import ObservationGroupCfg as ObsGroup
from isaaclab.managers import ObservationTermCfg as ObsTerm
from isaaclab.managers import RewardTermCfg as RewTerm
from isaaclab.managers import SceneEntityCfg
from isaaclab.managers import TerminationTermCfg as DoneTerm
from isaaclab.scene import InteractiveSceneCfg
from isaaclab.utils import configclass

import isaaclab_tasks.manager_based.classic.cartpole.mdp as mdp

##
# Pre-defined configs
##
from isaaclab_assets.robots.cartpole import CARTPOLE_CFG  # isort:skip


##
# Scene definition
##


@configclass
class CartpoleSceneCfg(InteractiveSceneCfg):
    """카트폴 씬 구성."""

    # ground plane
    ground = AssetBaseCfg(
        prim_path="/World/ground",
        spawn=sim_utils.GroundPlaneCfg(size=(100.0, 100.0)),
    )

    # cartpole
    robot: ArticulationCfg = CARTPOLE_CFG.replace(prim_path="{ENV_REGEX_NS}/Robot")

    # lights
    dome_light = AssetBaseCfg(
        prim_path="/World/DomeLight",
        spawn=sim_utils.DomeLightCfg(color=(0.9, 0.9, 0.9), intensity=500.0),
    )


##
# MDP 설정
##


@configclass
class ActionsCfg:
    """MDP의 액션 사양."""

    joint_effort = mdp.JointEffortActionCfg(asset_name="robot", joint_names=["slider_to_cart"], scale=100.0)


@configclass
class ObservationsCfg:
    """MDP의 관측 사양."""

    @configclass
    class PolicyCfg(ObsGroup):
        """정책 그룹을 위한 관측."""

        # 관측 항목(순서 유지)
        joint_pos_rel = ObsTerm(func=mdp.joint_pos_rel)
        joint_vel_rel = ObsTerm(func=mdp.joint_vel_rel)

        def __post_init__(self) -> None:
            self.enable_corruption = False
            self.concatenate_terms = True

    # 관측 그룹
    policy: PolicyCfg = PolicyCfg()


@configclass
class EventCfg:
    """이벤트 구성."""

    # 리셋
    reset_cart_position = EventTerm(
        func=mdp.reset_joints_by_offset,
        mode="reset",
        params={
            "asset_cfg": SceneEntityCfg("robot", joint_names=["slider_to_cart"]),
            "position_range": (-1.0, 1.0),
            "velocity_range": (-0.5, 0.5),
        },
    )

    reset_pole_position = EventTerm(
        func=mdp.reset_joints_by_offset,
        mode="reset",
        params={
            "asset_cfg": SceneEntityCfg("robot", joint_names=["cart_to_pole"]),
            "position_range": (-0.25 * math.pi, 0.25 * math.pi),
            "velocity_range": (-0.25 * math.pi, 0.25 * math.pi),
        },
    )


@configclass
class RewardsCfg:
    """MDP의 보상 항목."""

    # (1) 상수 실행 보상
    alive = RewTerm(func=mdp.is_alive, weight=1.0)
    # (2) 실패 페널티
    terminating = RewTerm(func=mdp.is_terminated, weight=-2.0)
    # (3) 주요 태스크: 폴을 upright하게 유지
    pole_pos = RewTerm(
        func=mdp.joint_pos_target_l2,
        weight=-1.0,
        params={"asset_cfg": SceneEntityCfg("robot", joint_names=["cart_to_pole"]), "target": 0.0},
    )
    # (4) 형성 태스크: 카트 속도 낮춤
    cart_vel = RewTerm(
        func=mdp.joint_vel_l1,
        weight=-0.01,
        params={"asset_cfg": SceneEntityCfg("robot", joint_names=["slider_to_cart"])},
    )
    # (5) 형성 태스크: 폴 각속도 낮춤
    pole_vel = RewTerm(
        func=mdp.joint_vel_l1,
        weight=-0.005,
        params={"asset_cfg": SceneEntityCfg("robot", joint_names=["cart_to_pole"])},
    )


@configclass
class TerminationsCfg:
    """MDP의 종료 조건."""

    # (1) 타임아웃
    time_out = DoneTerm(func=mdp.time_out, time_out=True)
    # (2) 카트가 경계 밖으로 벗어남
    cart_out_of_bounds = DoneTerm(
        func=mdp.joint_pos_out_of_manual_limit,
        params={"asset_cfg": SceneEntityCfg("robot", joint_names=["slider_to_cart"]), "bounds": (-3.0, 3.0)},
    )


##
# 환경 구성
##


@configclass
class CartpoleEnvCfg(ManagerBasedRLEnvCfg):
    """카트폴 환경 구성."""

    # 씬 설정
    scene: CartpoleSceneCfg = CartpoleSceneCfg(num_envs=4096, env_spacing=4.0, clone_in_fabric=True)
    # 기본 설정
    observations: ObservationsCfg = ObservationsCfg()
    actions: ActionsCfg = ActionsCfg()
    events: EventCfg = EventCfg()
    # MDP 설정
    rewards: RewardsCfg = RewardsCfg()
    terminations: TerminationsCfg = TerminationsCfg()

    # pós 초기화
    def __post_init__(self) -> None:
        """ pós 초기화."""
        # 일반적인 설정
        self.decimation = 2
        self.episode_length_s = 5
        # 뷰어 설정
        self.viewer.eye = (8.0, 0.0, 5.0)
        # 시뮬레이션 설정
        self.sim.dt = 1 / 120
        self.sim.render_interval = self.decimation
```

환경 실행 스크립트 `run_cartpole_rl_env.py`는 `isaaclab/scripts/tutorials/03_envs` 디렉터리에 있습니다. 이 스크립트는 이전 튜토리얼의 `cartpole_base_env.py` 스크립트와 유사하지만, [`envs.ManagerBasedRLEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)를 사용한다는 점이 다릅니다. 대신 [`envs.ManagerBasedEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv)를 사용합니다.

### run_cartpole_rl_env.py 코드

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 카트폴 균형 잡기 태스크에 대한 RL 환경을 실행하는 방법을 보여줍니다.

.. code-block:: bash

    ./isaaclab.sh -p scripts/tutorials/03_envs/run_cartpole_rl_env.py --num_envs 32

"""

"""먼저 Isaac Sim 시뮬레이터를 실행하세요."""

import argparse

from isaaclab.app import AppLauncher

# argparse 인수 추가
parser = argparse.ArgumentParser(description="카트폴 RL 환경 실행 튜토리얼.")
parser.add_argument("--num_envs", type=int, default=16, help="생성할 환경의 수.")

# AppLauncher cli 인수 추가
AppLauncher.add_app_launcher_args(parser)
# 인수 파싱
args_cli = parser.parse_args()

# 옴니버스 앱 실행
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app

"""그 이후의 모든 내용은 여기서부터."""

import torch

from isaaclab.envs import ManagerBasedRLEnv

from isaaclab_tasks.manager_based.classic.cartpole.cartpole_env_cfg import CartpoleEnvCfg


def main():
    """메인 함수."""
    # 환경 구성 생성
    env_cfg = CartpoleEnvCfg()
    env_cfg.scene.num_envs = args_cli.num_envs
    env_cfg.sim.device = args_cli.device
    # RL 환경 설정
    env = ManagerBasedRLEnv(cfg=env_cfg)

    # 물리 시뮬레이션
    count = 0
    while simulation_app.is_running():
        with torch.inference_mode():
            # 리셋
            if count % 300 == 0:
                count = 0
                env.reset()
                print("-" * 80)
                print("[INFO]: 환경 리셋 중...")
            # 랜덤 액션 샘플링
            joint_efforts = torch.randn_like(env.action_manager.action)
            # 환경 단계 실행
            obs, rew, terminated, truncated, info = env.step(joint_efforts)
            # 폴의 현재 방향 출력
            print("[Env 0]: Pole joint: ", obs["policy"][0][1].item())
            # 카운터 업데이트
            count += 1

    # 환경 종료
    env.close()


if __name__ == "__main__":
    # 메인 함수 실행
    main()
    # 시뮬레이터 앱 종료
    simulation_app.close()
```

## 코드 설명

이미지
참고: Isaac Lab에서 다양한 `envs.mdp` 모듈의 구현을 제공합니다. 이 튜토리얼에서는 일부를 사용하지만, 사용자는 자체 용어를 자유롭게 정의할 수도 있습니다. 이러한 용어는 보통 작업별 하위 패키지에 배치됩니다(예: `isaaclab_tasks.manager_based.classic.cartpole.mdp`).

### 보상 정의

[`managers.RewardManager`](../../api/lab/isaaclab.managers.md#isaaclab.managers.RewardManager)는 에이전트의 보상 항목을 계산하는 데 사용됩니다. 다른 관리자들과 마찬가지로, 항목은 [`managers.RewardTermCfg`](../../api/lab/isaaclab.managers.md#isaaclab.managers.RewardTermCfg) 클래스를 사용하여 구성됩니다. [`managers.RewardTermCfg`](../../api/lab/isaaclab.managers.md#isaaclab.managers.RewardTermCfg) 클래스는 보상을 계산하는 함수 또는 호출 가능한 클래스와 그에 따른 가중치를 지정합니다. 또한 호출될 때 보상 함수에 전달되는 인수 사전 `"params"`도 받아들입니다.

카트폴 작업에서는 다음 보상 항목을 사용합니다:

* **생존 보상**: 에이전트가 가능한 한 오래 생존하도록 장려합니다.
* **종료 보상**: 마찬가지로 에이전트가 종료될 경우 페널티를 부과합니다.
* **폴 각도 보상**: 에이전트가 폴을 원하는 직립 위치에 유지하도록 장려합니다.
* **카트 속도 보상**: 에이전트가 카트 속도를 가능한 작게 유지하도록 장려합니다.
* **폴 속도 보상**: 에이전트가 폴 각속도를 가능한 작게 유지하도록 장려합니다.

```python
@configclass
class RewardsCfg:
    """MDP의 보상 항목."""

    # (1) 상수 실행 보상
    alive = RewTerm(func=mdp.is_alive, weight=1.0)
    # (2) 실패 페널티
    terminating = RewTerm(func=mdp.is_terminated, weight=-2.0)
    # (3) 주요 작업: 폴을 직립 위치 유지
    pole_pos = RewTerm(
        func=mdp.joint_pos_target_l2,
        weight=-1.0,
        params={"asset_cfg": SceneEntityCfg("robot", joint_names=["cart_to_pole"]), "target": 0.0},
    )
    # (4) 형성 작업: 카트 속도 낮춤
    cart_vel = RewTerm(
        func=mdp.joint_vel_l1,
        weight=-0.01,
        params={"asset_cfg": SceneEntityCfg("robot", joint_names=["slider_to_cart"])},
    )
    # (5) 형성 작업: 폴 각속도 낮춤
    pole_vel = RewTerm(
        func=mdp.joint_vel_l1,
        weight=-0.005,
        params={"asset_cfg": SceneEntityCfg("robot", joint_names=["cart_to_pole"])},
    )
```

### 종료 조건 정의

대부분의 학습 작업은 에피소드라고 하는 유한한 단계 수에서 이루어집니다. 예를 들어 카트폴 작업에서는 에이전트가 폴을 가능한 한 오래 균형 잡히게 하려고 합니다. 그러나 에이전트가 불안정하거나 안전하지 않은 상태에 도달하면 에피소드를 종료하려고 합니다. 반면 에이전트가 장시간 폴을 균형 잡히게 하면 새로운 시작 구성에서 폴을 균형 잡히게 하는 법을 배우도록 에피소드를 종료하고 새로운 에피소드를 시작하려고 합니다.

`managers.TerminationsCfg`는 에피소드가 종료되는 조건을 구성합니다. 이 예에서는 다음 조건 중 하나가 충족될 때 작업이 종료되기를 원합니다:

* **에피소드 길이** 에피소드 길이가 정의된 `max_episode_length`보다 큼
* **카트 경계를 벗어남** 카트가 범위 [-3, 3]을 벗어남

플래그 `managers.TerminationsCfg.time_out`은 해당 항목이 타임아웃(절단) 항목인지 종료 항목인지를 지정합니다. 이는 [Gymnasium 문서](https://gymnasium.farama.org/tutorials/gymnasium_basics/handling_time_limits/)에서 설명하는 두 가지 종료 유형을 나타내는 데 사용됩니다.

```python
@configclass
class TerminationsCfg:
    """MDP의 종료 항목."""

    # (1) 타임아웃
    time_out = DoneTerm(func=mdp.time_out, time_out=True)
    # (2) 카트가 경계를 벗어남
    cart_out_of_bounds = DoneTerm(
        func=mdp.joint_pos_out_of_manual_limit,
        params={"asset_cfg": SceneEntityCfg("robot", joint_names=["slider_to_cart"]), "bounds": (-3.0, 3.0)},
    )
```

### 명령 정의

다양한 목표 조건부 작업에서 에이전트에 대한 목표 또는 명령을 지정하는 것이 유용합니다. 이는 [`managers.CommandManager`](../../api/lab/isaaclab.managers.md#isaaclab.managers.CommandManager)를 통해 처리됩니다. 명령 관리자는 각 단계에서 명령의 재샘플링 및 업데이트를 처리합니다. 또한 에이전트에 대한 관측값으로 명령을 제공하는 데도 사용할 수 있습니다.

이 간단한 작업에서는 명령을 사용하지 않으므로 이 속성을 기본값인 None으로 그대로 두었습니다. 다른 이동 또는 조작 작업에서 명령 관리자를 정의하는 예는 참고할 수 있습니다.

### 교육 과정 정의

종종 학습 에이전트를 훈련할 때는 간단한 작업부터 시작하여 에이전트 훈련이 진행됨에 따라 작업의 난이도를 점차 높이는 것이 도움이 됩니다. 이것이 교육 과정 학습의 아이디어입니다. Isaac Lab에서는 [`managers.CurriculumManager`](../../api/lab/isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 제공하여 환경에 대한 교육 과정을 정의할 수 있습니다.

이 튜토리얼에서는 간단히 하기 위해 교육 과정을 구현하지 않았으나, 다른 이동 또는 조작 작업에서 교육 과정 정의 예를 참고할 수 있습니다.

### 모두 연결하기

위에서 정의한 모든 구성 요소를 바탕으로 카트폴 환경에 대한 `ManagerBasedRLEnvCfg` 구성을 이제 만들 수 있습니다. 이는 [Manager-Based Base 환경 생성](create_manager_base_env.md#tutorial-create-manager-base-env)에서 정의한 `ManagerBasedEnvCfg`과 유사하지만, 위에 설명한 RL 구성 요소가 추가된 형태입니다.

```python
@configclass
class CartpoleEnvCfg(ManagerBasedRLEnvCfg):
    """카트폴 환경의 구성."""

    # 장면 설정
    scene: CartpoleSceneCfg = CartpoleSceneCfg(num_envs=4096, env_spacing=4.0, clone_in_fabric=True)
    # 기본 설정
    observations: ObservationsCfg = ObservationsCfg()
    actions: ActionsCfg = ActionsCfg()
    events: EventCfg = EventCfg()
    # MDP 설정
    rewards: RewardsCfg = RewardsCfg()
    terminations: TerminationsCfg = TerminationsCfg()

    # 후 초기화
    def __post_init__(self) -> None:
        """후 초기화."""
        # 일반 설정
        self.decimation = 2
        self.episode_length_s = 5
        # 뷰어 설정
        self.viewer.eye = (8.0, 0.0, 5.0)
        # 시뮬레이션 설정
        self.sim.dt = 1 / 120
        self.sim.render_interval = self.decimation
```

### 시뮬레이션 루프 실행

`run_cartpole_rl_env.py` 스크립트로 돌아가면, 시뮬레이션 루프는 이전 튜토리얼과 유사합니다. 유일한 차이점은 [`envs.ManagerBasedRLEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)의 인스턴스를 생성한다는 점이며,[`envs.ManagerBasedEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv)를 생성하지 않는다는 점입니다. 따라서 이제 [`envs.ManagerBasedRLEnv.step()`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv.step) 메서드는 보상 및 종료 상태와 같은 추가 신호를 반환합니다. 정보 사전에는 개별 항목에서의 보상 기여, 각 항목의 종료 상태, 에피소드 길이 등의 양에 대한 로깅도 유지됩니다.

```python
def main():
    """메인 함수."""
    # 환경 구성 생성
    env_cfg = CartpoleEnvCfg()
    env_cfg.scene.num_envs = args_cli.num_envs
    env_cfg.sim.device = args_cli.device
    # RL 환경 설정
    env = ManagerBasedRLEnv(cfg=env_cfg)

    # 물리 시뮬레이션
    count = 0
    while simulation_app.is_running():
        with torch.inference_mode():
            # 리셋
            if count % 300 == 0:
                count = 0
                env.reset()
                print("-" * 80)
                print("[INFO]: 환경 재설정 중...")
            # 랜덤 행동 샘플링
            joint_efforts = torch.randn_like(env.action_manager.action)
            # 환경 단계 진행
            obs, rew, terminated, truncated, info = env.step(joint_efforts)
            # 폴의 현재 방향 출력
            print("[Env 0]: Pole joint: ", obs["policy"][0][1].item())
            # 카운터 업데이트
            count += 1

    # 환경 종료
    env.close()
```

## 코드 실행

이전 튜토리얼과同様하게, `run_cartpole_rl_env.py` 스크립트를 실행하여 환경을 실행할 수 있습니다.

```bash
./isaaclab.sh -p scripts/tutorials/03_envs/run_cartpole_rl_env.py --num_envs 32
```

이전 튜토리얼과 유사한 시뮬레이션이 열려야 합니다. 그러나 이번엔 환경이 보상 및 종료 상태를 지정하는 추가 신호를 반환합니다. 또한 개별 환경은 구성에 명시된 종료 조건에 따라 종료될 때 자체적으로 재설정됩니다.

![run_cartpole_rl_env.py 실행 결과](../../_static/tutorials/tutorial_create_manager_rl_env.jpg)

시뮬레이션을 중지하려면 창을 닫거나 시뮬레이션을 시작한 터미널에서 `Ctrl+C`를 누르면 됩니다.

이 튜토리얼에서는 강화 학습을 위한 태스크 환경을 만드는 방법을 배웠습니다. 이는 기본 환경을 확장한 뒤 보상, 종료, 명령 및 교육 과정 항목을 포함하여 구현합니다. 또한 [`envs.ManagerBasedRLEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) 클래스를 사용하여 환경을 실행하고 다양한 신호를 받는 방법도 배웠습니다.

원하는 작업에 대해 [`envs.ManagerBasedRLEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) 클래스의 인스턴스를 수동으로 생성하는 것은 가능하지만, 각 작업별로 특수 스크립트가 필요하므로 확장성이 떨어집니다. 따라서 `gymnasium.make()` 함수를 활용하여 gym 인터페이스를 가진 환경을 만드는 방법을 익히면 됩니다. 이는 다음 튜토리얼에서 다룰 예정입니다.
