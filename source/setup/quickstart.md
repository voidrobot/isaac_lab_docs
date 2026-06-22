<a id="isaac-lab-quickstart"></a>

# 퀵스타트 가이드

이 가이드는 바로 손발을 더럽히고 싶어하는 사람들을 위해 작성되었습니다. Isaac Lab로 나만의 프로젝트를 구축하면서 마주치게 될 가장 일반적인 개념들을 다룰 것입니다. 여기에는 설치, RL 실행, 환경 찾기, 새로운 프로젝트 생성 등이 포함됩니다!

Isaac Lab의 힘은 몇 가지 핵심 기능에서 비롯되며, 이 가이드에서는 이를 간단히 다룹니다.

1. **벡터화**: 강화 학습은 작업을 여러 번 시도해야 합니다. Isaac Lab은 환경을 벡터화하여 동일한 환경의 여러 복사본에서 병렬로 훈련을 실행함으로써 이 과정을 가속화합니다. 이렇게 하면 모델 가중치를 업데이트하기 전에 데이터 수집에 소요되는 시간을 줄일 수 있습니다. 대부분의 코드베이스는 이 벡터화 시스템이 작동해야 하는 환경의 부분을 정의하는 데 할애됩니다.
2. **모듈러 설계**: Isaac Lab은 모듈러하게 설계되었습니다. 즉, 다양한 필요에 따라 교체할 수 있는 여러 컴포넌트를 갖춘 프로젝트를 설계할 수 있다는 의미입니다. 예를 들어, 특정 서브셋의 로봇을 지원하는 정책을 훈련시키고 싶다고 가정해 봅시다. 여기서 `ActionManager`와 같은 관리자 클래스 중 하나의 형태인 컨트롤러 인터페이스 레이어를 작성하여 환경과 작업을 로봇에 무관심하게 만들 수 있습니다. 대부분의 나머지 코드베이스는 이 관리자 시스템이 작동해야 하는 프로젝트의 부분을 정의하는 데 할애됩니다.

시작하려면 먼저 Isaac Lab을 설치하고 훈련 스크립트를 실행합니다.

## 퀵 설치 가이드

Isaac Lab을 설치하는 방법은 많지만 [설치](installation/index.md#isaaclab-installation-root) 페이지를 참조하면 됩니다. 그러나 이 퀵스타트 가이드에서는 가상 환경을 사용하는 pip 설치 경로를 따릅니다.

먼저 가상 환경을 정의해야 합니다.

### conda

```bash
# create a virtual environment named env_isaaclab with python3.11 and pip
conda create -n env_isaaclab python=3.11
# activate the virtual environment
conda activate env_isaaclab
```

### uv (실험적)

### Linux

```bash
# create a virtual environment named env_isaaclab with python3.11 and pip
uv venv --python 3.11 --seed env_isaaclab
# activate the virtual environment
source env_isaaclab/bin/activate
```

### Windows

```batch
# create a virtual environment named env_isaaclab with python3.11
uv venv --python 3.11 env_isaaclab
# activate the virtual environment
env_isaaclab\Scripts\activate
```

다음으로, CUDA 지원 PyTorch 2.7.0 버전을 설치합니다.

> ```bash
> pip install -U torch==2.7.0 torchvision==0.22.0 --index-url https://download.pytorch.org/whl/cu128
> ```

Isaac Sim을 설치하기 전에 pip가 최신 상태인지 확인해야 합니다. pip를 업데이트하려면 다음을 실행합니다.

### Linux

```bash
pip install --upgrade pip
```

### Windows

```batch
python -m pip install --upgrade pip
```

이제 Isaac Sim 패키지를 설치할 수 있습니다.

```none
pip install "isaacsim[all,extscache]==5.1.0" --extra-index-url https://pypi.nvidia.com
```

마지막으로 Isaac Lab을 설치합니다. 먼저 다음 명령을 사용하여 저장소를 클론합니다.

### SSH

```bash
git clone git@github.com:isaac-sim/IsaacLab.git
```

### HTTPS

```bash
git clone https://github.com/isaac-sim/IsaacLab.git
```

설치는 이제 저장소로 이동한 다음 루트 스크립트를 `--install` 플래그와 함께 호출하는 것만큼 간단합니다!

### Linux

```bash
./isaaclab.sh --install # 또는 "./isaaclab.sh -i"
```

### Windows

```bash
isaaclab.bat --install :: 또는 "isaaclab.bat -i"
```

## Isaac 실행 가능을 사용한 퀵 스타트

Isaac Lab을 처음 배우는 사용자이며 로컬 컴퓨팅 리소스가 충분하지 않은 경우, [Isaac 실행 가능](https://github.com/isaac-sim/isaac-launchable) 프로젝트는 수동 설치를 거치지 않고 시작하는 빠른 방법을 제공합니다.

이 프로젝트를 통해 사용자는 웹 브라우저에서 Isaac Sim과 Isaac Lab과 순수하게 상호작용할 수 있습니다. 한 탭에서는 개발 및 명령 실행을 위한 Visual Studio Code가 실행되고, 다른 탭에서는 Isaac Sim의 스트리밍된 사용자 인터페이스가 제공됩니다.

이 방법은 [NVIDIA Brev](https://brev.nvidia.com/)를 사용합니다. NVIDIA Brev은 시간당 결제형 클라우드 컴퓨팅을 쉽게 구성할 수 있는 플랫폼을 제공합니다. Brev 실행 가능은 사전 구성되어 최적화된 컴퓨팅 및 소프트웨어 환경을 제공합니다.

지금 바로 사용해 보려면 아래 버튼을 클릭하세요. 이 프로젝트를 사용하는 방법이나 자신만의 실행 가능을 만드는 방법에 대해 자세히 알아보려면 프로젝트 저장소를 참조하세요 [여기](https://github.com/isaac-sim/isaac-launchable).

[![배포하려면 클릭](https://brev-assets.s3.us-west-1.amazonaws.com/nv-lb-dark.svg)](https://brev.nvidia.com/launchable/deploy/now?launchableID=env-35JP2ywERLgqtD0b0MIeK1HnF46)

## 훈련 실행

Isaac Lab의 다양한 백엔드는 `isaaclab/scripts/reinforcement_learning` 디렉터리에 위치한 해당하는 `train.py` 및 `play.py` 스크립트를 통해 액세스됩니다. 이러한 스크립트를 호출하려면 **태스크 이름**과 gymnasium API에 대한 해당하는 **진입점**이 필요합니다. 예를 들어

```bash
python scripts/reinforcement_learning/skrl/train.py --task=Isaac-Ant-v0
```

이 명령은 무지개개미(Mujoco Ant)를 "달리도록" 훈련시킵니다. `--help` 플래그를 사용해서 이용 가능한 다양한 런치 옵션을 볼 수 있습니다. 특히 `--num_envs` 옵션과 `--headless` 플래그에 주목하세요. 이 두 가지는 새 환경을 개발하고 디버그할 때 유용할 수 있습니다. 이 수준에서 지정된 옵션은 코드에 정의된 해당하는 구성과 동등한 값을 자동으로 덮어씁니다(그렇다면 그 정의가 `@configclass`의 일부인 경우에 한함).

## 사용 가능한 환경 목록 보기

위에서 `Isaac-Ant-v0`는 태스크 이름이고 `skrl`은 사용 중인 RL 프레임워크입니다. `Isaac-Ant-v0` 환경은 [Gymnasium API](https://gymnasium.farama.org/)에 등록되었으며, `isaaclab/scripts/environments/list_envs.py`에서 찾을 수 있는 `list_envs.py` 스크립트를 호출하여 진입점이 어떻게 정의되는지 확인할 수 있습니다. 다음과 유사한 항목들을 보게 될 것입니다.

```bash
$> python scripts/environments/list_envs.py

+--------------------------------------------------------------------------------------------------------------------------------------------+
|  Available Environments in Isaac Lab
+--------+----------------------+--------------------------------------------+---------------------------------------------------------------+
| S. No. | Task Name            | Entry Point                                | Config
.
.
.
+--------+----------------------+--------------------------------------------+---------------------------------------------------------------+
|   2    | Isaac-Ant-Direct-v0  |  isaaclab_tasks.direct.ant.ant_env:AntEnv  |  isaaclab_tasks.direct.ant.ant_env:AntEnvCfg
+--------+----------------------+--------------------------------------------+---------------------------------------------------------------+
.
.
.
+--------+----------------------+--------------------------------------------+---------------------------------------------------------------+
|   48   | Isaac-Ant-v0         | isaaclab.envs:ManagerBasedRLEnv            |   isaaclab_tasks.manager_based.classic.ant.ant_env_cfg:AntEnvCfg
+--------+----------------------+--------------------------------------------+---------------------------------------------------------------+
```

두 가지 다른 `Ant` 작업이 있음을 알 수 있습니다. 하나는 `Direct` 환경용이고, 다른 하나는 `ManagerBased` 환경용입니다. 이는 Isaac Lab과 함께 제공되는 [두 가지 주요 워크플로](../overview/core-concepts/task_workflows.md#feature-workflows)입니다. Direct 워크플로는 강화 학습을 위한 맞춤 환경을 만드는 가장 빠른 경로를 제공하지만, Manager 기반 워크플로는 보다 범용적인 개발에 필요한 모듈러성을 프로젝트에 제공합니다. 이 퀵스타트 가이드에서는 Direct 워크플로에만 초점을 맞출 것입니다.

## 나만의 프로젝트 생성

Isaac Lab로 새로운 프로젝트를 시작하는 것이 처음에는 어렵게 느껴질 수 있지만, 이것이 바로 [템플릿 생성기](../overview/own-project/template.md#template-generator)를 제공하는 이유입니다. 이 생성기를 사용하면 명령줄을 통해 신속하게 새 프로젝트의 보일러플레이트를 생성할 수 있습니다.

```bash
./isaaclab.sh --new
```

이 명령은 사용자가 선택한 설정을 기반으로 새로운 프로젝트를 생성합니다.

* **외부 vs 내부**: 프로젝트가 Isaac Lab 저장소의 일부로 구축되는지, 아니면 외부 확장 기능으로 로드되는지를 결정합니다.
* **Direct vs Manager**: Direct 태스크는 주로 환경 정의 내에서 모든 구현 세부 사항을 포함하는 반면, Manager 기반 프로젝트는 환경의 다양한 "부분"에 대한 모듈러한 정의를 사용하도록 설계되었습니다.
* **프레임워크**: 여기서 하나 이상의 옵션을 선택할 수 있습니다. 이는 프로젝트에서 네이티브로 사용할 RL 프레임워크(사용하고 싶은 특정 알고리즘 구현)를 결정합니다.

생성된 후, 생성된 프로젝트로 이동하여 다음을 실행하여 설치 프로세스를 완료하고 환경을 등록합니다.

```bash
python -m pip install -e source/<given-project-name>
```

템플릿 생성기가 만든 디렉터리 안에는 다음과 같은 모양의 `__init__.py` 파일이 적어도 하나 있습니다.

```python
import gymnasium as gym

gym.register(
    id="Template-isaaclabtutorial_env-v0",
    entry_point=f"{__name__}.isaaclabtutorial_env:IsaaclabtutorialEnv",
    disable_env_checker=True,
    kwargs={
        "env_cfg_entry_point": f"{__name__}.isaaclabtutorial_env_cfg:IsaaclabtutorialEnvCfg",
        "skrl_cfg_entry_point": f"{agents.__name__}.skrl_ppo_cfg:PPORunnerCfg",
    },
)
```

이 함수가 실제로 향후 사용을 위해 환경을 등록하는 함수입니다. `entry_point`가 환경 정의로의 파이썬 모듈 경로 literal임을 알 수 있습니다.这就是为什么我们需要将项目安装为一个包：模块路径**就是** Gymnasium API 的入口点。

## 구성

Isaac Lab에서 작업을 수행하든간에 **Configurations** 을 다루는 것은 필수적입니다. Configurations 은 클래스 정의 위에 `@configclass` 데코레이터가 포함되어 있고 `__init__` 함수가 없는 것으로 식별할 수 있습니다. 예를 들어, [카트폴 환경](../tutorials/03_envs/create_direct_rl_env.md#tutorial-create-direct-rl-env)에 대한 다음과 같은 구성 클래스를 고려해 보겠습니다.

```python
@configclass
class CartpoleEnvCfg(DirectRLEnvCfg):
    # env
    decimation = 2
    episode_length_s = 5.0
    action_scale = 100.0  # [N]
    action_space = 1
    observation_space = 4
    state_space = 0

    # simulation
    sim: SimulationCfg = SimulationCfg(dt=1 / 120, render_interval=decimation)

    # robot
    robot_cfg: ArticulationCfg = CARTPOLE_CFG.replace(prim_path="/World/envs/env_.*/Robot")
    cart_dof_name = "slider_to_cart"
    pole_dof_name = "cart_to_pole"

    # scene
    scene: InteractiveSceneCfg = InteractiveSceneCfg(num_envs=4096, env_spacing=4.0, replicate_physics=True)

    # reset
    max_cart_pos = 3.0  # the cart is reset if it exceeds that position [m]
    initial_pole_angle_range = [-0.25, 0.25]  # the range in which the pole angle is sampled from on reset [rad]

    # reward scales
    rew_scale_alive = 1.0
    rew_scale_terminated = -2.0
    rew_scale_pole_pos = -1.0
    rew_scale_cart_vel = -0.01
    rew_scale_pole_vel = -0.005
```

전체 클래스 정의는 단지 값 필드와 다른 구성들의 목록임을 알 수 있습니다. 구성 클래스는 학습 중에 랩에 의해 벡터화되어야 하는 모든 것에 대해 필수적입니다. 환경 수천 개를 복사하고 각 환경의 데이터를 비동기적으로 관리하려면 복사 프로세스와 관련된 장면의 일부를 somehow “라벨링”해야 합니다(벡터화). 이것이 바로 구성 클래스가 수행하는 역할입니다!

이 경우, 클래스는 전체 훈련 환경의 구성을 정의합니다! 또한 `InteractiveSceneCfg` 내의 `num_envs` 변수에 주목하세요. 실제로 이 변수는 `train.py` 스크립트 내의 CLI 인수에 의해 덮어쓰여집니다. 구성은 구성 계층 구조 내의 모든 변수에 직접 접근할 수 있는 경로를 제공하여 런칭 시간에 환경에 의해 “구성된” 모든 것을 쉽게 수정할 수 있도록 합니다.

## 로봇

Isaac Lab에서 로봇은 구성의 인스턴스로 완전히 정의됩니다. `source/isaaclab_assets/isaaclab_assets/robots` 를 조사하면 로봇마다 구성들이 포함된 여러 파일을 볼 수 있습니다. 이러한 개별 파일의 목적은 다양한 로봇에 대한 범위를 더 명확히 정의하는 것이지만, [자신의 로봇을 추가](../tutorials/01_assets/add_new_robot.md#tutorial-add-new-robot)하여 프로젝트나 심지어 `isaaclab` 저장소 자체에 포함시키는 것을 막는 것은 아무것도 없습니다. 예를 들어, 다음은 Dofbot에 대한 구성입니다.

```python
import isaaclab.sim as sim_utils
from isaaclab.actuators import ImplicitActuatorCfg
from isaaclab.assets.articulation import ArticulationCfg
from isaaclab.utils.assets import ISAAC_NUCLEUS_DIR

DOFBOT_CONFIG = ArticulationCfg(
    spawn=sim_utils.UsdFileCfg(
        usd_path=f"{ISAAC_NUCLEUS_DIR}/Robots/Dofbot/dofbot.usd",
        rigid_props=sim_utils.RigidBodyPropertiesCfg(
            disable_gravity=False,
            max_depenetration_velocity=5.0,
        ),
        articulation_props=sim_utils.ArticulationRootPropertiesCfg(
            enabled_self_collisions=True, solver_position_iteration_count=8, solver_velocity_iteration_count=0
        ),
    ),
    init_state=ArticulationCfg.InitialStateCfg(
        joint_pos={
            "joint1": 0.0,
            "joint2": 0.0,
            "joint3": 0.0,
            "joint4": 0.0,
        },
        pos=(0.25, -0.25, 0.0),
    ),
    actuators={
        "front_joints": ImplicitActuatorCfg(
            joint_names_expr=["joint[1-2]"],
            effort_limit_sim=100.0,
            velocity_limit_sim=100.0,
            stiffness=10000.0,
            damping=100.0,
        ),
        "joint3_act": ImplicitActuatorCfg(
            joint_names_expr=["joint3"],
            effort_limit_sim=100.0,
            velocity_limit_sim=100.0,
            stiffness=10000.0,
            damping=100.0,
        ),
        "joint4_act": ImplicitActuatorCfg(
            joint_names_expr=["joint4"],
            effort_limit_sim=100.0,
            velocity_limit_sim=100.0,
            stiffness=10000.0,
            damping=100.0,
        ),
    },
)
```

이 구성은 도프봇을 완전히 정의합니다!これを`.py`ファイルにコピーしてモジュールとしてインポートすれば、自分のラボシミュレーションでドフボットを使用できるようになります。状態を持つものを定義する設定では、よく見られる機能の一つに`InitialStateCfg`の存在があります。設定はベクトル化に関する情報を提供し、その中でも`InitialStateCfg`は各環境でロボットが生成される際の関節の状態を記述します。`ImplicitActuatorCfg`は、関節時間によって決定されるデフォルトのアクチュエーションモデルを使用してロボットの関節を定義します。すべての関節がアクチュエートされる必要はありませんが、そうしないと警告が出ます。未定義の関節を使用する予定がない場合は、これらの警告を無視することが一般的です。

## 앱과 시뮬레이션

시뮬레이션을 사용한다는 것은 시뮬레이션 컨텍스트를 제공하기 위해 Isaac Sim 앱을 실행하는 것을 의미합니다. 표준 워크플로에 정의된 작업을 실행하고 있지 않다면, 앱을 생성하고 컨텍스트를 관리하며 시뮬레이션을 시간에 따라 앞으로 진행시키는 책임이 있습니다. 이것이 바로 **독립형** 워크플로이며, 프레임워크, 데모, 벤치마크 등에 사용되는 스크립트를 지칭합니다.

독립형 워크플로는 앱과 시뮬레이션 컨텍스트의 *모든 것*에 대한 완전한 제어를 제공합니다. 독립형 앱 개발은 [Isaac Sim 문서](https://docs.isaacsim.omniverse.nvidia.com/latest/index.html)에서 자세히 다루지만, incredibly useful 할 수 있는 몇 가지 사항을 언급해 보겠습니다.

```python
import argparse

from isaaclab.app import AppLauncher
# add argparse arguments
parser = argparse.ArgumentParser(
    description="This script demonstrates adding a custom robot to an Isaac Lab environment."
)
parser.add_argument("--num_envs", type=int, default=1, help="Number of environments to spawn.")
# append AppLauncher cli args
AppLauncher.add_app_launcher_args(parser)
# parse the arguments
args_cli = parser.parse_args()

# launch omniverse app
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app
```

`AppLauncher`는 Isaac Lab을 포함한 모든 Isaac Sim 애플리케이션의 진입점입니다! *많은 Isaac Lab 및 Isaac Sim 모듈은 앱이 실행될 때까지 가져올 수 없습니다!*. 이는 위의 코드에서 마지막에서 두 번째 줄에서 `AppLauncher`가 생성될 때 수행됩니다. `app_launcher.app`은 키트 앱 프레임워크와의 인터페이스이며, 시뮬레이션을 확장 관리 시스템 또는 GUI 등과 연결하는 더 넓은 중개 코드입니다. 독립형 워크플로에서는 이 인터페이스(종종 `simulation_app`이라고 함)를 주로 사용하여 시뮬레이션이 실행 중인지 확인하고 시뮬레이션 종료 후 정리 작업을 수행하는 데 사용됩니다.
