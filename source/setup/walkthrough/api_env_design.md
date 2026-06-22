<a id="walkthrough-api-env-design"></a>

# 클래스 및 구성

시작하려면 작업으로 이동합니다: `source/isaac_lab_tutorial/isaac_lab_tutorial/tasks/direct/isaac_lab_tutorial`, 그리고 `isaac_lab_tutorial_env_cfg.py`의 내용을 확인해 보세요. 다음과 유사한 내용이 보일 것입니다.

```python
from isaaclab_assets.robots.cartpole import CARTPOLE_CFG

from isaaclab.assets import ArticulationCfg
from isaaclab.envs import DirectRLEnvCfg
from isaaclab.scene import InteractiveSceneCfg
from isaaclab.sim import SimulationCfg
from isaaclab.utils import configclass


@configclass
class IsaacLabTutorialEnvCfg(DirectRLEnvCfg):

    # Some useful fields
    .
    .
    .

    # simulation
    sim: SimulationCfg = SimulationCfg(dt=1 / 120, render_interval=2)

    # robot(s)
    robot_cfg: ArticulationCfg = CARTPOLE_CFG.replace(prim_path="/World/envs/env_.*/Robot")

    # scene
    scene: InteractiveSceneCfg = InteractiveSceneCfg(num_envs=4096, env_spacing=4.0, replicate_physics=True)

    # Some more useful fields
    .
    .
    .
```

이는 템플릿과 함께 제공되는 간단한 카트폴 환경의 기본 구성으로, 해당 환경 내에서 수행하는 모든 것에 대한 `self` 범위를 정의합니다.

첫 번째로 주의할 점은 `@configclass` 데코레이터의 존재입니다. 이는 Isaac Lab에서 특별한 위치를 차지하는 구성 클래스로 클래스를 정의합니다. 구성 클래스는 Isaac Lab이 환경을 복제하여 훈련을 확장할 때 무엇에 "관심"을 기울일지 결정하는 방법의 일부이며, 목표에 따라 Isaac Lab은 다양한 기본 구성 클래스를 제공합니다. 이 경우 우리는 직접 워크플로에서 강화 학습을 수행하려는 목표 때문에 [`DirectRLEnvCfg`](../../api/lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnvCfg) 클래스를 사용하고 있습니다.

두 번째로 주의할 점은 구성 클래스의 내용입니다. 작성자는 원하는 어떤 필드든 지정할 수 있지만, 일반적으로 다음과 같은 세 가지를 항상 정의하게 됩니다: **sim**, **scene**, 그리고 **robot**입니다. 이러한 필드들도 구성 클래스라는 점을 알 수 있습니다. 구성 클래스는 복제된 복잡한 환경에 대한 해법으로 이러한 방식으로 구성됩니다.

**sim**은 [`SimulationCfg`](../../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)의 인스턴스이며, 이는 구축하고 있는 시뮬레이션된 현실의 특성을 제어하는 구성입니다. 이 필드는 기본 클래스인 `DirectRLEnvCfg`의 멤버이지만 기본 시뮬레이션 구성이 제공되므로 기술적으로는 선택 사항입니다. `SimulationCfg`는 시간 단계를 얼마나 세밀하게 진행할지(dt), 중력의 방향, 심지어 물리 시뮬레이션 방식까지 결정합니다. 여기서는 시간 단계와 렌더 간격만 지정했는데, 전자는 시간이 한 단계 진행될 때마다 $1/120$ 초를 시뮬레이션함을 나타내고, 후자는 프레임을 렌더링하기 전에 몇 단계를 진행할지를 나타냅니다(값이 2이면 매 다른 프레임을 렌더링함을 의미합니다).

**scene**은 [`InteractiveSceneCfg`](../../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg)의 인스턴스입니다. scene은 무대에서 일어나는 일을 설명하고 환경 간에 복제될 시뮬레이션 엔티티를 관리합니다. scene도 기본 클래스 `DirectRLEnvCfg`의 멤버이지만, sim과는 달리 기본값이 없으므로 모든 `DirectRLEnvCfg`에서 반드시 정의해야 합니다. `InteractiveSceneCfg`는 훈련 목적으로 сцен의 복사본을 몇 개 만들고 싶은지, 그리고 무대상에서 얼마나 떨어져 배치해야 하는지를 설명합니다.

마지막으로, [`ArticulationCfg`](../../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg)의 인스턴스인 **robot** 정의를 살펴보겠습니다. 환경은 여러 개의 관절 로봇을 가질 수 있으므로 `ArticulationCfg`의 존재는 `DirectRLEnv`를 정의하는 데 필수적이지는 않습니다. 대신 일반적인 워크플로에서는 로봇에 대한 정규 표현식 경로를 정의하고 기본 구성의 `prim_path` 속성을 교체합니다. 이 경우 `CARTPOLE_CFG`는 `isaaclab_assets.robots.cartpole`에 정의된 구성이며, `prim_path`를 `/World/envs/env_.*/Robot`으로 교체함으로써 모든 сцен 복사본에 이름이 `Robot`인 로봇이 존재함을 암시적으로 표현합니다.

## 환경

다음으로 작업 디렉터리의 다른 Python 파일인 `isaac_lab_tutorial_env.py`의 내용을 살펴봅시다.

```python
# imports
.
.
.
from .isaac_lab_tutorial_env_cfg import IsaacLabTutorialEnvCfg

class IsaacLabTutorialEnv(DirectRLEnv):
    cfg: IsaacLabTutorialEnvCfg

    def __init__(self, cfg: IsaacLabTutorialEnvCfg, render_mode: str | None = None, **kwargs):
        super().__init__(cfg, render_mode, **kwargs)
        . . .

    def _setup_scene(self):
        self.robot = Articulation(self.cfg.robot_cfg)
        # add ground plane
        spawn_ground_plane(prim_path="/World/ground", cfg=GroundPlaneCfg())
        # add articulation to scene
        self.scene.articulations["robot"] = self.robot
        # clone and replicate
        self.scene.clone_environments(copy_from_source=False)
        # add lights
        light_cfg = sim_utils.DomeLightCfg(intensity=2000.0, color=(0.75, 0.75, 0.75))
        light_cfg.func("/World/Light", light_cfg)

    def _pre_physics_step(self, actions: torch.Tensor) -> None:
        . . .

    def _apply_action(self) -> None:
        . . .

    def _get_observations(self) -> dict:
        . . .

    def _get_rewards(self) -> torch.Tensor:
        total_reward = compute_rewards(...)
        return total_reward

    def _get_dones(self) -> tuple[torch.Tensor, torch.Tensor]:
        . . .

    def _reset_idx(self, env_ids: Sequence[int] | None):
        . . .

@torch.jit.script
def compute_rewards(...):
    . . .
    return total_reward
```

의도치 않은 복잡성을 피하고 토론에 집중하기 위해 일부 코드가 생략되었습니다. 여기서 실제 "핵심"인 직접 워크플로가 존재하며, 템플릿을 우리의 필요에 맞게 수정하면서 대부분의 수정이 이루어질 위치입니다. 현재 `IsaacLabTutorialEnv`의 모든 멤버 함수는 [`DirectRLEnv`](../../api/lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv)로부터 직접 상속받고 있습니다. 이 알려진 인터페이스는 Isaac Lab과 지원되는 RL 프레임워크가 환경과 상호작용하는 방식입니다.

환경이 초기화될 때, 자신의 구성 인수를 받아 즉시 `super`에 전달하여 `DirectRLEnv`를 초기화합니다. 이 `super` 호출은 `_setup_scene`도 호출하는데, 여기서 실제로 시나리오를 구성하고 적절히 복제합니다. 특히 로봇이 `_setup_scene`에서 어떻게 생성되고 시나리오에 등록되는지 주목할 만합니다. 먼저, `IsaacLabTutorialEnvCfg`에서 정의한 `robot_config`를 사용하여 로봇 관절 로봇을 생성합니다; 이 시점 이전에는 존재하지 않았습니다! 관절 로봇이 생성되면, 로봇은 `/World/envs/env_0/Robot` 위치에 단계 상에 존재하게 됩니다. `scene.clone_environments` 호출이 `env_0`을 적절히 복사하면 이 시점에서 로봇이 단계상에 여러 복사본으로 존재하게 되므로, 남은 작업은 `scene` 객체에 이 관절 로봇의 존재를 알리는 것으로 추적 대상에 추가하는 것입니다. 시나리오의 관절 로봇은 사전으로 관리되므로 `scene.articulations["robot"] = self.robot`은 `articulations` 사전에 새로운 `robot` 요소를 생성하고 그 값을 `self.robot`으로 설정합니다.

나머지 함수들은 `_reset_idx`를 제외하고 추가 인수를 받지 않는다는 점도 주목할 만합니다.这是因为环境仅管理施加在被模拟智能体上的动作，然后更新模拟。这就是 `_pre_physics_step` 和 `_apply_action` 步骤的目的：我们设置机器人的驱动命令，以便当模拟向前推进时，动作被施加并且关节被驱动到新的目标。这一过程被分解为这样的步骤，目的是确保对环境执行方式进行系统控制，在管理器工作流中尤为重要。类似地，`_get_dones` 函数和 `_reset_idx` 之间也存在类似的关系。前者 `_get_dones` 确定每个环境是否处于终止状态，并填充布尔张量以指示哪些环境由于进入终止状态而终止，哪些环境由于超时而终止（该函数返回的两个布尔张量）。后者 `_reset_idx` 接受环境索引值的列表（整数），然后实际重置这些环境。更新驱动目标或重置环境等操作不应发生在物理或渲染步骤 **期间**，并且以这种方式划分接口有助于防止这种情况发生。
