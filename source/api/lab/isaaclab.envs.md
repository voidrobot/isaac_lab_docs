# isaaclab.envs

환경 정의를 위한 하위 패키지입니다.

환경은 에이전트와 시뮬레이션 사이의 인터페이스를 정의합니다.
가장 간단한 경우, 환경은 에이전트에게 현재 관측을 제공하고 에이전트가 제공한 행동을 실행합니다. 그러나 환경은 현재 보상, 수행 플래그, 현재 에피소드에 대한 정보와 같은 추가 정보를 제공할 수도 있습니다.

환경 설계 워크플로에는 두 가지 유형이 있습니다:

* **관리자 기반**: 환경은 서로 다른 측면(관측 계산, 행동 적용, 무작위화 적용 등)을 위한 개별 구성 요소(또는 관리자)로 분해됩니다. 사용자는 주로 관리자를 구성하고 환경은 관리자를 조율하고 그들의 함수를 호출합니다.
* **직접**: 사용자는 추가 관리자가 필요 없이 단일 클래스에 모든 필요한 기능을 직접 구현합니다.

이러한 워크플로를 기반으로, 단일 및 다중 에이전트 RL을 위한 다음과 같은 환경 클래스가 제공됩니다:

**단일 에이전트 RL:**

* [`ManagerBasedEnv`](#isaaclab.envs.ManagerBasedEnv): 관리자 기반 워크플로의 기본 환경으로, 에이전트에게 현재 관측만 제공하고 에이전트가 제공한 행동을 실행합니다.
* [`ManagerBasedRLEnv`](#isaaclab.envs.ManagerBasedRLEnv): 관리자 기반 워크플로의 RL 태스크 환경으로, 기본 환경의 기능 외에도 현재 보상, 수행 플래그, 정보와 같은 마르코프 결정 과정(MDP)과 관련된 추가 정보를 제공합니다.
* [`DirectRLEnv`](#isaaclab.envs.DirectRLEnv): 직접 워크플로의 RL 태스크 환경으로, 장면 설정, 종료 조건 계산, 리셋 수행, 보상 및 관측 계산 구현을 제공합니다.

**다중 에이전트 RL (MARL):**

* [`DirectMARLEnv`](#isaaclab.envs.DirectMARLEnv): 직접 워크플로의 MARL 태스크 환경으로, 장면 설정, 종료 조건 계산, 리셋 수행, 보상 및 관측 계산 구현을 제공합니다.

워크플로 설계 패턴에 대한 자세한 내용은 [Task Design Workflows](https://docs.isaacsim.omniverse.nvidia.com/latest/introduction/workflows.html) 섹션을 참조하세요.

### 서브모듈

| [`mdp`](isaaclab.envs.mdp.md#module-isaaclab.envs.mdp)   | 관리자 용어 구현이 포함된 서브 모듈입니다.                |
|----------------------------------------------------------|-----------------------------------------------------------------|
| [`ui`](isaaclab.envs.ui.md#module-isaaclab.envs.ui)      | 환경용 UI 창 구현을 제공하는 서브 모듈입니다. |

### 클래스

| [`ManagerBasedEnv`](#isaaclab.envs.ManagerBasedEnv)                 | 관리자 기반 워크플로에서 시뮬레이션 장면과 환경 관리자를 캡슐화하는 기본 환경입니다.   |
|---------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`ManagerBasedEnvCfg`](#isaaclab.envs.ManagerBasedEnvCfg)           | 환경의 기본 구성입니다.                                                                                |
| [`ManagerBasedRLEnv`](#isaaclab.envs.ManagerBasedRLEnv)             | 관리자 기반 워크플로 강화 학습 기반 환경의 상위 클래스입니다.                              |
| [`ManagerBasedRLEnvCfg`](#isaaclab.envs.ManagerBasedRLEnvCfg)       | 관리자 기반 워크플로의 강화 학습 환경에 대한 구성입니다.                               |
| [`DirectRLEnv`](#isaaclab.envs.DirectRLEnv)                         | 직접 워크플로로 환경을 설계하기 위한 상위 클래스입니다.                                                        |
| [`DirectRLEnvCfg`](#isaaclab.envs.DirectRLEnvCfg)                   | 직접 워크플로로 정의된 RL 환경에 대한 구성입니다.                                                 |
| [`DirectMARLEnv`](#isaaclab.envs.DirectMARLEnv)                     | 직접 워크플로로 다중 에이전트 환경을 설계하기 위한 상위 클래스입니다.                                            |
| [`DirectMARLEnvCfg`](#isaaclab.envs.DirectMARLEnvCfg)               | 직접 워크플로로 정의된 MARL 환경에 대한 구성입니다.                                                |
| [`ManagerBasedRLMimicEnv`](#isaaclab.envs.ManagerBasedRLMimicEnv)   | Isaac Lab Mimic 환경의 상위 클래스입니다.                                                                  |
| [`MimicEnvCfg`](#isaaclab.envs.MimicEnvCfg)                         | Mimic 환경 통합을 위한 구성 클래스입니다.                                                            |
| [`SubTaskConfig`](#isaaclab.envs.SubTaskConfig)                     | Mimic 환경에서 사용되는 서브태스크를 지정하기 위한 구성 설정입니다.                                            |
| [`SubTaskConstraintConfig`](#isaaclab.envs.SubTaskConstraintConfig) | 다중 엔드 이펙터 Mimic 환경에서 사용되는 서브태스크 제약을 지정하기 위한 구성 설정입니다.                       |
| [`ViewerCfg`](#isaaclab.envs.ViewerCfg)                             | 장면 뷰포트 카메라의 구성입니다.                                                                           |

## 관리자 기반 환경

### *class* isaaclab.envs.ManagerBasedEnv

이 환경은 관리자 기반 워크플로를 위한 시뮬레이션 장면과 환경 관리자를 캡슐화하는 기본 환경입니다.

시뮬레이션 장면 또는 세계는 로봇, 객체, 센서(카메라, 라이다 등)와 같은 다양한 구성 요소로 이루어지지만, 환경은 시뮬레이션과 상호작용할 수 있는 인터페이스를 제공하는 더 높은 수준의 추상화입니다. 환경은 다음 구성 요소로 이루어집니다:

* **장면**: 로봇이 작동하는 가상 세계를 생성하고 관리하는 장면 관리자입니다. 여기에는 로봇, 정적 및 동적 객체, 센서 등을 정의하는 것이 포함됩니다.
* **관측 관리자**: 현재 시뮬레이션 상태와 센서에서 수집된 데이터로부터 관측을 생성하는 관측 관리자입니다. 이러한 관측에는 실제 세계의 로봇이 이용할 수 없는 특권 정보가 포함될 수 있습니다. 또한 사용자 정의 항목을 추가하여 관측을 처리하고 사용자 정의 관측을 생성할 수 있습니다. 예를 들어, 네트워크를 사용하여 고차원 관측을 저차원 공간으로 임베딩할 수 있습니다.
* **행동 관리자**: 환경에 전송된 원시 행동을 처리하여 시뮬레이션에 전송되는 저수준 명령으로 변환하는 행동 관리자입니다. 다양한 수준의 추상화에서 원시 행동을 받아들일 수 있도록 구성할 수 있습니다. 예를 들어, 로봇 팔의 경우 원시 행동이 관절 토크, 관절 위치 또는 엔드 이펙터 포즈일 수 있습니다. 마찬가지로 이동 기반의 경우 관절 토크 또는 부동 기저의 원하는 속도일 수 있습니다.
* **이벤트 관리자**: 시뮬레이션 이벤트에 기반하여 트리거되는 작업을 조율하는 이벤트 관리자입니다. 여기에는 시나리오의 기본 상태로 재설정, 서로 다른 시간 간격에서 로봇에 무작위 충격 적용, 질량 및 마찰 계수와 같은 속성의 무작위화가 포함됩니다. 이는 다양한 시나리오에서 로봇의 훈련 및 평가를 수행하는 데 유용합니다.
* **레코더 관리자**: 시뮬레이션의 다양한 단계에서 생성된 데이터를 처리하는 레코더 관리자입니다. 여기에는 리셋과 스텝의 시작 및 끝에서의 데이터 기록이 포함됩니다. 기록된 데이터는 에피소드별, 환경별로 구분되며 데이터셋 파일 처리기를 통해 파일로 내보낼 수 있습니다.

환경은 시뮬레이션과 상호작용하기 위한 통합 인터페이스를 제공하지만, 보상 함수 또는 종료 조건과 같은 작업 특수량은 포함하지 않습니다. 이러한 양상은 종종 마르코프 결정 과정(MDP)을 정의하는 데 특수하며 기본 환경은 MDP 정의에 대해 Agnostic합니다.

환경은 고정된 타임스텝으로 시간에 따라 앞으로 진행됩니다. 물리 시뮬레이션은 안정성을 보장하기 위해 낮은 타임스텝으로 디시메이트됩니다. 이 두 타임스텝은 [`ManagerBasedEnvCfg.decimation`](#isaaclab.envs.ManagerBasedEnvCfg.decimation) (환경 단계당 시뮬레이션 단계 수)와 `ManagerBasedEnvCfg.sim.dt` (물리 타임스텝) 파라미터를 사용하여 독립적으로 구성할 수 있습니다. 이러한 파라미터를 기반으로 환경 타임스텝은 두 값의 곱으로 계산됩니다. 두 타임스텝은 각각 [`physics_dt`](#isaaclab.envs.ManagerBasedEnv.physics_dt)와 [`step_dt`](#isaaclab.envs.ManagerBasedEnv.step_dt) 속성을 쿼리하여 얻을 수 있습니다.

**메서드:**

| [`__init__`](#isaaclab.envs.ManagerBasedEnv.__init__)(cfg)                                    | 환경을 초기화합니다.                                 |
|-----------------------------------------------------------------------------------------------|-------------------------------------------------------------|
| [`export_IO_descriptors`](#isaaclab.envs.ManagerBasedEnv.export_IO_descriptors)([output_dir]) | 환경의 IO 설명자를 내보냅니다.              |
| [`load_managers`](#isaaclab.envs.ManagerBasedEnv.load_managers)()                             | 환경의 관리자를 로드합니다.                      |
| [`setup_manager_visualizers`](#isaaclab.envs.ManagerBasedEnv.setup_manager_visualizers)()     | 관리자 항목에 대한 라이브 시각화 도구를 생성합니다.                 |
| [`reset`](#isaaclab.envs.ManagerBasedEnv.reset)([seed, env_ids, options])                     | 지정된 환경을 재설정하고 관측을 반환합니다. |
| [`reset_to`](#isaaclab.envs.ManagerBasedEnv.reset_to)(state, env_ids[, seed, is_relative])    | 제공된 상태로 지정된 환경을 재설정합니다.           |
| [`step`](#isaaclab.envs.ManagerBasedEnv.step)(action)                                         | 환경의 동역학에서 한 시간 단계를 실행합니다.        |
| [`seed`](#isaaclab.envs.ManagerBasedEnv.seed)([seed])                                         | 환경의 시드를 설정합니다.                           |
| [`close`](#isaaclab.envs.ManagerBasedEnv.close)()                                             | 환경을 정리합니다.                                |

**속성:**

| [`num_envs`](#isaaclab.envs.ManagerBasedEnv.num_envs)                     | 현재 실행 중인 환경 인스턴스의 수입니다.   |
|---------------------------------------------------------------------------|----------------------------------------------------------------|
| [`physics_dt`](#isaaclab.envs.ManagerBasedEnv.physics_dt)                 | 물리 시간 단계(초 단위).                                  |
| [`step_dt`](#isaaclab.envs.ManagerBasedEnv.step_dt)                       | 환경 스테핑 시간 단계(초 단위).                     |
| [`device`](#isaaclab.envs.ManagerBasedEnv.device)                         | 환경이 실행 중인 장치입니다.                |
| [`get_IO_descriptors`](#isaaclab.envs.ManagerBasedEnv.get_IO_descriptors) | 환경의 IO 디스크립터를 가져옵니다.                    |

#### \_\_init_\_(cfg: [ManagerBasedEnvCfg](#isaaclab.envs.ManagerBasedEnvCfg))

환경을 초기화합니다.

* **매개변수:**
  **cfg** – 환경의 구성 객체입니다.
* **예외 발생:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 시뮬레이션 컨텍스트가 이미 존재하는 경우입니다. 환경은 시뮬레이션 컨텍스트를 구성하고 시뮬레이션을 제어하므로 항상 새 컨텍스트를 생성해야 합니다.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

현재 실행 중인 환경 인스턴스의 수입니다.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 시간 단계(초 단위).

이 값은 시뮬레이션이 실행되는 가장 낮은 시간 디시메이션 수준입니다.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스테핑 시간 단계(초 단위).

이 값은 환경이 앞으로 이동하는 시간 단계입니다.

#### *property* device

환경이 실행 중인 장치입니다.

#### *property* get_IO_descriptors

환경의 IO 디스크립터를 가져옵니다.

* **반환값:**
  그룹 이름을 키로, IO 디스크립터를 값으로 갖는 딕셔너리입니다.

#### export_IO_descriptors(output_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

환경의 IO 디스크립터를 내보냅니다.

* **매개변수:**
  **output_dir** – IO 디스크립터를 내보낼 디렉터리입니다.

#### load_managers()

환경의 관리자를 로드합니다.

이 함수는 환경의 다양한 관리자(액션, 관찰, 이벤트 등)를 생성하는 역할을 합니다. 관리자는 물리 핸들에 접근해야 하므로, 시뮬레이터가 리셋된 후(즉, 최초 실행 후)에만 생성할 수 있습니다.

#### NOTE
독립형 애플리케이션(파이썬에서 시뮬레이터를 실행하는 경우)의 경우, 클래스가 초기화될 때 이 함수가 자동으로 호출됩니다.

그러나 확장 모드에서는 사용자가 시뮬레이터를 리셋한 후 이 함수를 수동으로 호출해야 합니다. 이는 시뮬레이터가 사용자가 `SimulationContext.reset_async()`를 호출할 때만 리셋되기 때문이며, 생성자에서는 비동기 함수를 호출할 수 없습니다.

#### setup_manager_visualizers()

관리자 항목의 실시간 시각화 도구를 생성합니다.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

지정된 환경을 리셋하고 관측값을 반환합니다.

이 함수는 지정된 환경을 리셋하기 위해 `_reset_idx()` 함수를 호출합니다. 그러나 초기화 중에 발생한 프로시저 지형 생성과 같은 일부 작업은 반복되지 않습니다.

* **매개변수:**
  * **seed** – 무작위화에 사용할 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **env_ids** – 리셋할 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 리셋됩니다.
  * **options** – 

    환경을 어떻게 리셋할지 지정하는 추가 정보입니다. 기본값은 None입니다.

    #### NOTE
    이 인수는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **반환값:**
  관측값과 추가 정보를 포함하는 튜플입니다.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

제공된 상태로 지정된 환경을 리셋합니다.

이 함수는 제공된 상태로 환경을 리셋합니다. 상태는 장면 엔티티의 상태를 포함하는 딕셔너리입니다. 형식은 `InteractiveScene.get_state()`를 참조하십시오.

이 함수는 [`reset()`](#isaaclab.envs.ManagerBasedEnv.reset) 함수와 달리, 무작위화 이벤트를 사용하는 대신 특정 상태로 환경을 리셋한다는 점이 다릅니다.

* **매개변수:**
  * **state** – 지정된 환경을 리셋할 상태입니다. 형식은 `InteractiveScene.get_state()`를 참조하십시오.
  * **env_ids** – 리셋할 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 리셋됩니다.
  * **seed** – 무작위화에 사용할 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **is_relative** – True로 설정된 경우, 상태는 환경 원점에 상대적인 것으로 간주됩니다. 기본값은 False입니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동역학에서 한 시간 단계를 실행합니다.

환경은 고정된 시간 단계로 앞으로 진행되며, 물리 시뮬레이션은 더 낮은 시간 단계로 디시메이트됩니다. 이는 시뮬레이션의 안정성을 보장하기 위함입니다. 이 두 시간 단계는 [`ManagerBasedEnvCfg.decimation`](#isaaclab.envs.ManagerBasedEnvCfg.decimation)(환경 단계당 시뮬레이션 단계 수)와 `ManagerBasedEnvCfg.sim.dt`(물리 시간 단계)를 사용하여 독립적으로 구성할 수 있습니다. 이러한 매개변수를 기반으로 환경 시간 단계는 두 값의 곱으로 계산됩니다.

* **매개변수:**
  **action** – 환경에 적용할 액션입니다. 형태는 (num_envs, action_dim)입니다.
* **반환값:**
  관측값과 추가 정보를 포함하는 튜플입니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **매개변수:**
  **seed** – 난수 생성기의 시드입니다. 기본값은 -1입니다.
* **반환값:**
  난수 생성기에 사용된 시드입니다.

#### close()

환경을 정리합니다.

### *class* isaaclab.envs.ManagerBasedEnvCfg

환경의 기본 구성입니다.

**속성:**

| [`viewer`](#isaaclab.envs.ManagerBasedEnvCfg.viewer)                                 | 뷰어 구성입니다.                                                                   |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [`sim`](#isaaclab.envs.ManagerBasedEnvCfg.sim)                                       | 물리 시뮬레이션 구성.                                                       |
| [`seed`](#isaaclab.envs.ManagerBasedEnvCfg.seed)                                     | 난수 생성기의 시드.                                               |
| [`decimation`](#isaaclab.envs.ManagerBasedEnvCfg.decimation)                         | 정책 dt당 시뮬레이션 dt당 제어 동작 업데이트 수.                                |
| [`scene`](#isaaclab.envs.ManagerBasedEnvCfg.scene)                                   | 씬 설정.                                                                         |
| [`recorders`](#isaaclab.envs.ManagerBasedEnvCfg.recorders)                           | 레코더 설정.                                                                      |
| [`observations`](#isaaclab.envs.ManagerBasedEnvCfg.observations)                     | 관찰 공간 설정.                                                             |
| [`actions`](#isaaclab.envs.ManagerBasedEnvCfg.actions)                               | 행동 공간 설정.                                                                  |
| [`events`](#isaaclab.envs.ManagerBasedEnvCfg.events)                                 | 이벤트 설정.                                                                     |
| [`rerender_on_reset`](#isaaclab.envs.ManagerBasedEnvCfg.rerender_on_reset)           | 최소 한 개의 환경이 재설정된 후 렌더 단계가 다시 수행되는지 여부. |
| [`num_rerenders_on_reset`](#isaaclab.envs.ManagerBasedEnvCfg.num_rerenders_on_reset) | 재설정 후 수행할 렌더 단계 수.                                          |
| [`wait_for_textures`](#isaaclab.envs.ManagerBasedEnvCfg.wait_for_textures)           | 자산이 완전히 로드될 때까지 기다리려면 True, 그렇지 않으면 False.                       |
| [`xr`](#isaaclab.envs.ManagerBasedEnvCfg.xr)                                         | XR 장치를 통해 환경을 보고 상호작용하는 구성.    |
| [`teleop_devices`](#isaaclab.envs.ManagerBasedEnvCfg.teleop_devices)                 | 텔레오퍼레이션 장치 구성.                                                |
| [`export_io_descriptors`](#isaaclab.envs.ManagerBasedEnvCfg.export_io_descriptors)   | 환경의 IO 디스크립터를 내보낼지 여부.                               |
| [`log_dir`](#isaaclab.envs.ManagerBasedEnvCfg.log_dir)                               | 실험 아티팩트 로깅 디렉터리.                                             |

**클래스:**

| [`ui_window_class_type`](#isaaclab.envs.ManagerBasedEnvCfg.ui_window_class_type)   | [`BaseEnvWindow`](isaaclab.envs.ui.md#isaaclab.envs.ui.BaseEnvWindow)의 별칭   |
|------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|

#### viewer *: [ViewerCfg](#isaaclab.envs.ViewerCfg)*

뷰어 구성. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성. 기본값은 SimulationCfg()입니다.

#### ui_window_class_type

[`BaseEnvWindow`](isaaclab.envs.ui.md#isaaclab.envs.ui.BaseEnvWindow)의 별칭

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 부분에서 설정됩니다. 이를 통해 환경 생성이 결정적이고 다양한 실행에서 동일하게 동작하도록 보장합니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

정책 dt당 시뮬레이션 dt당 제어 동작 업데이트 수.

예를 들어, 시뮬레이션 dt가 0.01초이고 정책 dt가 0.1초이면, 디메이션은 10입니다. 이는 제어 동작이 시뮬레이션 10스텝마다 업데이트됨을 의미합니다.

#### scene *: [InteractiveSceneCfg](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg)*

씬 설정.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스를 참조하세요.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정. 기본값은 아무것도 기록하지 않음입니다.

자세한 내용은 [`isaaclab.managers.RecorderManager`](isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참조하세요.

#### observations *: [object](https://docs.python.org/3/library/functions.html#object)*

관찰 공간 설정.

자세한 내용은 [`isaaclab.managers.ObservationManager`](isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참조하세요.

#### actions *: [object](https://docs.python.org/3/library/functions.html#object)*

행동 공간 설정.

자세한 내용은 [`isaaclab.managers.ActionManager`](isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참조하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정. 기본값은 씬을 기본 상태로 재설정하는 기본 구성입니다.

자세한 내용은 [`isaaclab.managers.EventManager`](isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참조하세요.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

최소 한 개의 환경이 재설정된 후 렌더 단계가 다시 수행되는지 여부. 기본값은 False로, 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* False일 경우, 재설정 후 센서에서 수집된 데이터는 오래되어 시뮬레이션에서 재설정으로 인한 최신 상태를 반영하지 않습니다.
* True일 경우, 추가 렌더 단계가 수행되어 센서 데이터를 재설정으로 인한 최신 상태로 업데이트합니다. 이는 성능 비용이 발생하며, 각 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### Deprecated
2.3.1 버전부터 비권장: 이 속성은 비권장이며 향후 삭제될 예정입니다. 대신 [`num_rerenders_on_reset`](#isaaclab.envs.ManagerBasedEnvCfg.num_rerenders_on_reset)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정했을 때와 동일한 동작을 얻으려면 [`num_rerenders_on_reset`](#isaaclab.envs.ManagerBasedEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하세요.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후 수행할 렌더 단계 수. 기본값은 0으로, 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 0일 경우, 재설정 후 렌더 단계가 수행되지 않으며, 센서에서 수집된 데이터는 오래되어 시뮬레이션에서 재설정으로 인한 최신 상태를 반영하지 않습니다.
* 0보다 클 경우, 지정된 수의 추가 렌더 단계가 수행되어 센서 데이터를 재설정으로 인한 최신 상태로 업데이트합니다. 이는 성능 비용이 발생하며, 각 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 완전히 로드될 때까지 기다리려면 True, 그렇지 않으면 False. 기본값은 True입니다.

#### xr *: XrCfg | [None](https://docs.python.org/3/library/constants.html#None)*

XR 장치를 통해 환경을 보고 상호작용하는 구성.

#### teleop_devices *: DevicesCfg*

텔레오퍼레이션 장치 구성.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 디스크립터를 내보낼지 여부. 기본값은 False입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트 로깅 디렉터리. 기본값은 None이며, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

## Manager Based RL 환경

### *class* isaaclab.envs.ManagerBasedRLEnv

베이스: [`ManagerBasedEnv`](#isaaclab.envs.ManagerBasedEnv), [`Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env)

관리자 기반 워크플로 강화 학습 기반 환경의 슈퍼클래스입니다.

이 클래스는 [`ManagerBasedEnv`](#isaaclab.envs.ManagerBasedEnv)를 상속하고 강화 학습 기반 환경의 핵심 기능을 구현합니다. 어떤 RL 라이브러리와도 함께 사용하도록 설계되었습니다. 이 클래스는 벡터화된 환경에서 사용하도록 설계되었습니다. 즉, 환경은 다수의 서브 환경과 병렬로 실행될 것으로 예상됩니다. 서브 환경의 수는 `num_envs`를 사용하여 지정됩니다.

환경의 각 관측은 각 서브 환경의 관측 배치입니다. 또한 [`step()`](#isaaclab.envs.ManagerBasedRLEnv.step) 메서드는 각 서브 환경의 행동 배치를 받을 것으로 예상됩니다.

환경 자체는 벡터화된 환경으로 구현되지만, `gym.vector.VectorEnv`에서 상속하지는 않습니다. 이는 주로 이 클래스가 대기 및 비동기 업데이트와 같은 다양한 메서드를 추가하기 때문이며, 이러한 메서드는 필수적이지 않습니다. 또한, 각 RL 라이브러리는 일반적으로 벡터화된 환경에 대한 자체 정의를 가지고 있습니다. 따라서 복잡성을 줄이기 위해 여기서 직접 `gym.Env`를 사용하고, 이 환경을 래핑하여 에이전트에 맞게 조정하는 것은 라이브러리 정의 래퍼에 맡깁니다.

#### NOTE
벡터화된 환경의 경우, 첫 번째 [`step()`](#isaaclab.envs.ManagerBasedRLEnv.step) 호출 전에 [`reset()`](#isaaclab.envs.ManagerBasedRLEnv.reset) 메서드를 **한 번만** 호출하는 것이 권장됩니다. 즉, 환경이 생성된 후에 한 번 호출합니다. 이후에는 [`step()`](#isaaclab.envs.ManagerBasedRLEnv.step) 함수가 종료된 서브 환경의 재설정을 처리합니다.
이유는 시뮬레이터가 벡터화된 환경에서 개별 서브 환경을 재설정하는 것을 지원하지 않기 때문입니다.

**속성:**

| [`is_vector_env`](#isaaclab.envs.ManagerBasedRLEnv.is_vector_env)               | 환경이 벡터화된 환경인지 여부입니다.                                                                  |
|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`metadata`](#isaaclab.envs.ManagerBasedRLEnv.metadata)                         | 환경의 메타데이터입니다.                                                                                         |
| [`cfg`](#isaaclab.envs.ManagerBasedRLEnv.cfg)                                   | 환경의 구성입니다.                                                                                    |
| [`max_episode_length_s`](#isaaclab.envs.ManagerBasedRLEnv.max_episode_length_s) | 초 단위의 최대 에피소드 길이입니다.                                                                                    |
| [`max_episode_length`](#isaaclab.envs.ManagerBasedRLEnv.max_episode_length)     | 환경 단계 단위의 최대 에피소드 길이입니다.                                                                          |
| [`device`](#isaaclab.envs.ManagerBasedRLEnv.device)                             | 환경이 실행되는 디바이스입니다.                                                                       |
| [`get_IO_descriptors`](#isaaclab.envs.ManagerBasedRLEnv.get_IO_descriptors)     | 환경의 IO 디스크립터를 가져옵니다.                                                                           |
| [`np_random`](#isaaclab.envs.ManagerBasedRLEnv.np_random)                       | 설정되지 않은 경우 랜덤 시드로 초기화되는 환경의 내부 `_np_random`을 반환합니다.                   |
| [`np_random_seed`](#isaaclab.envs.ManagerBasedRLEnv.np_random_seed)             | 설정되지 않은 경우 랜덤 정수로 먼저 초기화되는 환경의 내부 `_np_random_seed`을 반환합니다. |
| [`num_envs`](#isaaclab.envs.ManagerBasedRLEnv.num_envs)                         | 현재 실행 중인 환경 인스턴스의 수입니다.                                                          |
| [`physics_dt`](#isaaclab.envs.ManagerBasedRLEnv.physics_dt)                     | 물리 타임스텝(초).                                                                                         |
| [`step_dt`](#isaaclab.envs.ManagerBasedRLEnv.step_dt)                           | 환경 스텝 타임스텝(초).                                                                            |
| [`unwrapped`](#isaaclab.envs.ManagerBasedRLEnv.unwrapped)                       | 기본 래핑되지 않은 환경을 반환합니다.                                                                             |

**메서드:**

| [`__init__`](#isaaclab.envs.ManagerBasedRLEnv.__init__)(cfg[, render_mode])                       | 환경을 초기화합니다.                                                                        |
|---------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| [`load_managers`](#isaaclab.envs.ManagerBasedRLEnv.load_managers)()                               | 환경의 관리자를 로드합니다.                                                             |
| [`setup_manager_visualizers`](#isaaclab.envs.ManagerBasedRLEnv.setup_manager_visualizers)()       | 관리자 항목을 위한 라이브 시각화 도구를 생성합니다.                                                        |
| [`step`](#isaaclab.envs.ManagerBasedRLEnv.step)(action)                                           | 환경의 동역학에 대한 한 타임스텝을 실행하고 종료된 환경을 재설정합니다.             |
| [`render`](#isaaclab.envs.ManagerBasedRLEnv.render)([recompute])                                  | 물리 시뮬레이션을 진행하지 않고 렌더링을 실행합니다.                                                |
| [`close`](#isaaclab.envs.ManagerBasedRLEnv.close)()                                               | 환경 정리를 수행합니다.                                                                       |
| [`export_IO_descriptors`](#isaaclab.envs.ManagerBasedRLEnv.export_IO_descriptors)([output_dir])   | 환경의 IO 디스크립터를 내보냅니다.                                                     |
| [`get_wrapper_attr`](#isaaclab.envs.ManagerBasedRLEnv.get_wrapper_attr)(name)                     | 환경에서 속성 이름을 가져옵니다.                                                      |
| [`has_wrapper_attr`](#isaaclab.envs.ManagerBasedRLEnv.has_wrapper_attr)(name)                     | 환경에 속성 이름이 존재하는지 확인합니다.                                            |
| [`reset`](#isaaclab.envs.ManagerBasedRLEnv.reset)([seed, env_ids, options])                       | 지정된 환경을 재설정하고 관측값을 반환합니다.                                        |
| [`reset_to`](#isaaclab.envs.ManagerBasedRLEnv.reset_to)(state, env_ids[, seed, is_relative])      | 제공된 상태로 지정된 환경을 재설정합니다.                                                  |
| [`seed`](#isaaclab.envs.ManagerBasedRLEnv.seed)([seed])                                           | 환경의 시드를 설정합니다.                                                                  |
| [`set_wrapper_attr`](#isaaclab.envs.ManagerBasedRLEnv.set_wrapper_attr)(name, value, \*[, force]) | 값을 사용하여 환경에 속성 이름을 설정합니다. 자세한 내용은 `Wrapper.set_wrapper_attr`를 참조하십시오. |

#### is_vector_env *: ClassVar[[bool](https://docs.python.org/3/library/functions.html#bool)]* *= True*

환경이 벡터화된 환경인지 여부입니다.

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터입니다.

#### cfg *: [ManagerBasedRLEnvCfg](#isaaclab.envs.ManagerBasedRLEnvCfg)*

환경의 구성입니다.

#### \_\_init_\_(cfg: [ManagerBasedRLEnvCfg](#isaaclab.envs.ManagerBasedRLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성입니다.
  * **render_mode** – 환경의 렌더링 모드입니다. 기본값은 None이며, 이는 `"human"`과 유사합니다.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

초 단위의 최대 에피소드 길이입니다.

#### *property* max_episode_length *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 단계 단위의 최대 에피소드 길이입니다.

#### load_managers()

환경의 관리자를 로드합니다.

이 함수는 액션, 관찰, 이벤트 등 다양한 관리자(manager)를 생성하는 역할을 담당합니다. 관리자는 물리 핸들에 접근해야 하므로, 시뮬레이터가 리셋된 후(첫 번째 실행 이후)에만 생성할 수 있습니다.

#### 참고
독립형 애플리케이션(파이썬에서 시뮬레이터 실행 중)의 경우, 클래스가 초기화될 때 이 함수가 자동으로 호출됩니다.

그러나 확장 모드의 경우, 사용자는 시뮬레이터가 리셋된 후 이 함수를 수동으로 호출해야 합니다. 이는 시뮬레이터가 사용자가 `SimulationContext.reset_async()`를 호출할 때만 리셋되기 때문이며, 생성자에서는 비동기 함수를 호출할 수 없기 때문입니다.

#### setup_manager_visualizers()

관리자 항목을 위한 라이브 시각화 도구를 생성합니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동역학에 대한 한 타임스텝을 실행하고 종료된 환경을 재설정합니다.

[`ManagerBasedEnv.step`](#isaaclab.envs.ManagerBasedEnv.step) 클래스와 달리, 이 함수는 다음과 같은 작업을 수행합니다:

1. 액션을 처리합니다.
2. 물리 스텝을 수행합니다.
3. GUI가 활성화된 경우 렌더링을 수행합니다.
4. 환경 카운터를 업데이트하고 보상 및 종료 조건을 계산합니다.
5. 종료된 환경을 재설정합니다.
6. 관측값을 계산합니다.
7. 관측값, 보상, 재설정(종료 및 트렁케이션) 및 추가 정보를 반환합니다.

* **매개변수:**
  **action** – 환경에 적용할 액션입니다. 형태는 (num_envs, action_dim)입니다.
* **반환값:**
  관측값, 보상, 재설정(종료 및 트렁케이션) 및 추가 정보를 포함하는 튜플입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 시뮬레이션을 진행하지 않고 렌더링을 실행합니다.

관례에 따라 모드가 다음과 같을 때:

- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않습니다. 일반적으로 인간의 소비를 위해 사용됩니다.
- **rgb_array**: (x, y, 3) 형태의 numpy.ndarray를 반환하며, x-by-y 픽셀 이미지의 RGB 값을 나타냅니다. 동영상으로 변환하기에 적합합니다.

* **Parameters:**
  **recompute** – 시뮬레이터가 장면을 이미 렌더링했더라도 강제로 렌더링을 수행할지 여부를 나타냅니다.
  기본값은 False입니다.
* **Returns:**
  모드가 “rgb_array”인 경우 numpy 배열 형태의 렌더링된 이미지를 반환합니다. 그렇지 않은 경우 None을 반환합니다.
* **Raises:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 모드가 “rgb_data”로 설정되었으나 시뮬레이션 렌더 모드가 이를 지원하지 않는 경우 발생합니다.
        이 경우 시뮬레이션 렌더 모드는 `RenderMode.PARTIAL_RENDERING` 또는 `RenderMode.FULL_RENDERING`로 설정되어야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정된 경우 발생합니다.

#### close()

환경 정리를 수행합니다.

#### *property* device

환경이 실행 중인 디바이스를 반환합니다.

#### export_IO_descriptors(output_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

환경의 IO 디스크립터를 내보냅니다.

* **Parameters:**
  **output_dir** – IO 디스크립터를 내보낼 디렉터리입니다.

#### *property* get_IO_descriptors

환경의 IO 디스크립터를 가져옵니다.

* **Returns:**
  그룹 이름을 키로, IO 디스크립터를 값으로 갖는 딕셔너리를 반환합니다.

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 지정된 이름의 속성을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 지정된 이름의 속성이 존재하는지 확인합니다.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

환경의 내부 `_np_random`을 반환합니다. 설정되지 않은 경우 랜덤 시드로 초기화됩니다.

* **Returns:**
  np.random.Generator 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 내부 `_np_random_seed`를 반환합니다. 설정되지 않은 경우 먼저 랜덤 정수로 시드를 초기화합니다.

[`np_random_seed`](#isaaclab.envs.ManagerBasedRLEnv.np_random_seed)가 [`reset()`](#isaaclab.envs.ManagerBasedRLEnv.reset) 또는 `set_np_random_through_seed()` 대신 직접 설정된 경우,
시드는 -1이 됩니다.

* **Returns:**
  현재 np_random의 시드 또는 rng의 시드가 알려지지 않은 경우 -1
* **Return type:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

실행 중인 환경 인스턴스의 수를 반환합니다.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 타임스텝(초 단위)을 반환합니다.

이 값은 시뮬레이션이 발생하는 가장 낮은 시간 디시메이션입니다.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

지정된 환경을 리셋하고 관측값을 반환합니다.

이 함수는 지정된 환경을 리셋하기 위해 `_reset_idx()` 함수를 호출합니다.
그러나 초기화 중에 발생한 프로시저얼 지형 생성과 같은 특정 작업은 반복되지 않습니다.

* **Parameters:**
  * **seed** – 무작위화에 사용할 시드입니다. 기본값은 None이며,この場合 시드는 설정되지 않습니다.
  * **env_ids** – 리셋할 환경의 ID입니다. 기본값은 None이며,この場合 모든 환경이 리셋됩니다.
  * **options** – 

    환경 리셋 방법을 지정하기 위한 추가 정보입니다. 기본값은 None입니다.

    #### NOTE
    이 인수는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **Returns:**
  관측값과 추가 정보를 포함하는 튜플을 반환합니다.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

지정된 환경을 제공된 상태로 리셋합니다.

이 함수는 제공된 상태로 환경을 리셋합니다. 상태는 씬 엔티티의 상태를 포함하는 딕셔너리입니다.
포맷については `InteractiveScene.get_state()` を参照してください。

이 함수는 [`reset()`](#isaaclab.envs.ManagerBasedRLEnv.reset)와 다릅니다. 이 함수는 랜덤화 이벤트를 사용하는 대신 환경을 특정 상태로 리셋하기 때문입니다.

* **Parameters:**
  * **state** – 지정된 환경을 리셋할 상태입니다. 포맷については `InteractiveScene.get_state()` を参照してください。
  * **env_ids** – 리셋할 환경의 ID입니다. 기본값은 None이며,この場合 모든 환경이 리셋됩니다.
  * **seed** – 무작위화에 사용할 시드입니다. 기본값은 None이며,この場合 시드는 설정되지 않습니다.
  * **is_relative** – True로 설정된 경우, 상태는 환경 원점에 상대적인 것으로 간주됩니다.
    기본값은 False입니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **Parameters:**
  **seed** – 랜덤 생성기에 사용할 시드입니다. 기본값은 -1입니다.
* **Returns:**
  랜덤 생성기에 사용된 시드를 반환합니다.

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

지정된 값으로 환경의 속성을 설정합니다. 자세한 내용은 `Wrapper.set_wrapper_attr`를 참조하세요.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스텝 타임스텝(초 단위)을 반환합니다.

이 값은 환경이 앞으로 진행되는 시간 간격입니다.

#### *property* unwrapped *: [Env](https://gymnasium.farama.org/api/env/#gymnasium.Env)[ObsType, ActType]*

기본 래핑되지 않은 환경을 반환합니다.

* **Returns:**
  기본 래핑되지 않은 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인스턴스를 반환합니다.
* **Return type:**
  Env

### *class* isaaclab.envs.ManagerBasedRLEnvCfg

Bases: [`ManagerBasedEnvCfg`](#isaaclab.envs.ManagerBasedEnvCfg)

매니저 기반 워크플로우를 사용하는 강화 학습 환경의 구성입니다.

**Classes:**

| [`ui_window_class_type`](#isaaclab.envs.ManagerBasedRLEnvCfg.ui_window_class_type)   | [`ManagerBasedRLEnvWindow`](isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭입니다.   |
|--------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|

**Attributes:**

| [`is_finite_horizon`](#isaaclab.envs.ManagerBasedRLEnvCfg.is_finite_horizon)           | 에이전트에 대한 학습 작업이 유한 또는 무한 호라이즌 문제로 취급되는지 여부를 나타냅니다.   |
|----------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| [`episode_length_s`](#isaaclab.envs.ManagerBasedRLEnvCfg.episode_length_s)             | 에피소드의 기간(초 단위)을 나타냅니다.                                                          |
| [`rewards`](#isaaclab.envs.ManagerBasedRLEnvCfg.rewards)                               | 보상 설정을 나타냅니다.                                                                              |
| [`viewer`](#isaaclab.envs.ManagerBasedRLEnvCfg.viewer)                                 | 뷰어 구성을 나타냅니다.                                                                         |
| [`sim`](#isaaclab.envs.ManagerBasedRLEnvCfg.sim)                                       | 물리 시뮬레이션 구성을 나타냅니다.                                                             |
| [`seed`](#isaaclab.envs.ManagerBasedRLEnvCfg.seed)                                     | 난수 생성기의 시드입니다.                                                              |
| [`decimation`](#isaaclab.envs.ManagerBasedRLEnvCfg.decimation)                         | 정책 dt당 sim dt에 대한 제어 액션 업데이트 횟수입니다.                                      |
| [`scene`](#isaaclab.envs.ManagerBasedRLEnvCfg.scene)                                   | 장면 설정입니다.                                                                         |
| [`recorders`](#isaaclab.envs.ManagerBasedRLEnvCfg.recorders)                           | 레코더 설정입니다.                                                                       |
| [`observations`](#isaaclab.envs.ManagerBasedRLEnvCfg.observations)                     | 관찰 공간 설정입니다.                                                                    |
| [`actions`](#isaaclab.envs.ManagerBasedRLEnvCfg.actions)                               | 액션 공간 설정입니다.                                                                    |
| [`events`](#isaaclab.envs.ManagerBasedRLEnvCfg.events)                                 | 이벤트 설정입니다.                                                                       |
| [`rerender_on_reset`](#isaaclab.envs.ManagerBasedRLEnvCfg.rerender_on_reset)           | 하나 이상의 환경이 재설정된 후 렌더 단계가 다시 수행되는지 여부입니다.                   |
| [`num_rerenders_on_reset`](#isaaclab.envs.ManagerBasedRLEnvCfg.num_rerenders_on_reset) | 재설정 후 수행할 렌더 단계 수입니다.                                                     |
| [`wait_for_textures`](#isaaclab.envs.ManagerBasedRLEnvCfg.wait_for_textures)           | 에셋이 완전히 로드될 때까지 기다리려면 True, 그렇지 않으면 False입니다.                    |
| [`xr`](#isaaclab.envs.ManagerBasedRLEnvCfg.xr)                                         | XR 장치를 통해 환경을 보기 및 상호작용하기 위한 구성입니다.                                  |
| [`teleop_devices`](#isaaclab.envs.ManagerBasedRLEnvCfg.teleop_devices)                 | 원격조작 장치에 대한 구성입니다.                                                         |
| [`export_io_descriptors`](#isaaclab.envs.ManagerBasedRLEnvCfg.export_io_descriptors)   | 환경의 IO 설명자를 내보내는지 여부입니다.                                                |
| [`log_dir`](#isaaclab.envs.ManagerBasedRLEnvCfg.log_dir)                               | 실험 아티팩트를 기록할 디렉터리입니다.                                                   |
| [`terminations`](#isaaclab.envs.ManagerBasedRLEnvCfg.terminations)                     | 종료 설정입니다.                                                                         |
| [`curriculum`](#isaaclab.envs.ManagerBasedRLEnvCfg.curriculum)                         | 교육과정 설정입니다.                                                                     |
| [`commands`](#isaaclab.envs.ManagerBasedRLEnvCfg.commands)                             | 명령 설정입니다.                                                                         |

#### ui_window_class_type

[`ManagerBasedRLEnvWindow`](isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭입니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에이전트에 대한 학습 작업이 유한 호라이즌 문제인지 무한 호라이즌 문제인지 여부를 나타냅니다.
기본값은 False로, 이는 작업이 무한 호라이즌 문제로 처리됨을 의미합니다.

이 플래그는 유한 및 무한 호라이즌 작업의 미묘한 차이를 처리합니다:

* **유한 호라이즌**: 에이전트가 시간 초과 시 페널티 또는 부트스트래핑 값을 필요로 하지 않습니다. 그러나 환경은 여전히 시간 제한에 도달한 후 에피소드를 종료해야 합니다.
* **무한 호라이즌**: 에이전트는 에피소드 끝의 상태 값을 부트스트래핑해야 합니다. 이는 에이전트에게 시간 제한(또는 잘림) 완료 신호를 보내어 이 부트스트래핑 계산을 트리거함으로써 수행됩니다.

True인 경우 환경은 유한 호라이즌 문제로 처리되고 타임아웃(또는 잘림) 완료 신호가 에이전트로 전송되지 않습니다. False인 경우 환경은 무한 호라이즌 문제로 처리되고 타임아웃(또는 잘림) 완료 신호가 에이전트로 전송됩니다.

#### NOTE
기본 [`ManagerBasedRLEnv`](#isaaclab.envs.ManagerBasedRLEnv) 클래스는 이 플래그를 직접 사용하지 않습니다. 환경 래퍼에서 해당 학습 에이전트에 전송할 완료 신호의 유형을 결정하는 데 사용됩니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 지속 시간(초 단위).

디메이션 비율과 물리 시간 단계를 기반으로 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디메이션 비율이 10이고, 물리 시간 단계가 0.01초이며, 에피소드 길이가 10초인 경우,
에피소드 길이(단계)는 100입니다.

#### rewards *: [object](https://docs.python.org/3/library/functions.html#object)*

보상 설정입니다.

자세한 내용은 [`isaaclab.managers.RewardManager`](isaaclab.managers.md#isaaclab.managers.RewardManager) 클래스를 참조하십시오.

#### viewer *: [ViewerCfg](#isaaclab.envs.ViewerCfg)*

뷰어 구성입니다. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성입니다. 기본값은 SimulationCfg()입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 시 설정됩니다. 이렇게 하면 환경 생성이 결정적이며 다양한 실행에서 동일하게 동작합니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

정책 dt당 sim dt에 대한 제어 액션 업데이트 횟수입니다.

예를 들어, 시뮬레이션 dt가 0.01초이고 정책 dt가 0.1초인 경우, 디메이션은 10입니다.
이는 제어 액션이 시뮬레이션 10단계마다 업데이트됨을 의미합니다.

#### scene *: [InteractiveSceneCfg](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg)*

장면 설정입니다.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스를 참조하십시오.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정입니다. 기본값은 아무것도 기록하지 않습니다.

자세한 내용은 [`isaaclab.managers.RecorderManager`](isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참조하십시오.

#### observations *: [object](https://docs.python.org/3/library/functions.html#object)*

관찰 공간 설정입니다.

자세한 내용은 [`isaaclab.managers.ObservationManager`](isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참조하십시오.

#### actions *: [object](https://docs.python.org/3/library/functions.html#object)*

액션 공간 설정입니다.

자세한 내용은 [`isaaclab.managers.ActionManager`](isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참조하십시오.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정입니다. 기본값은 장면을 기본 상태로 재설정하는 기본 구성입니다.

자세한 내용은 [`isaaclab.managers.EventManager`](isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참조하십시오.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

하나 이상의 환경이 재설정된 후 렌더 단계가 다시 수행되는지 여부입니다. 기본값은 False로, 이는 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 False인 경우, 재설정 후 센서에서 수집된 데이터는 최신 상태가 아니며 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 이 값이 True인 경우, 추가 렌더 단계가 수행되어 센서 데이터가 재설정으로 인한 시뮬레이션의 최신 상태를 반영하도록 업데이트됩니다. 이는 성능에 비용이 발생하며, 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### Deprecated
버전 2.3.1부터 지원 중단됨: 이 특성은 더 이상 사용되지 않으며 향후 제거될 예정입니다. 대신
[`num_rerenders_on_reset`](#isaaclab.envs.ManagerBasedRLEnvCfg.num_rerenders_on_reset)을 사용하십시오.

이 매개변수를 `True` 또는 `False`로 설정한 것과 동일한 동작을 얻으려면
[`num_rerenders_on_reset`](#isaaclab.envs.ManagerBasedRLEnvCfg.num_rerenders_on_reset)을 각각 1 또는 0으로 설정하십시오.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후 수행할 렌더 단계 수입니다. 기본값은 0으로, 이는 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 0인 경우, 재설정 후 렌더 단계가 수행되지 않습니다. 재설정 후 센서에서 수집된 데이터는 최신 상태가 아니며 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 이 값이 0보다 큰 경우, 지정된 수의 추가 렌더 단계가 수행되어 센서 데이터가 재설정으로 인한 시뮬레이션의 최신 상태를 반영하도록 업데이트됩니다. 이는 성능에 비용이 발생하며, 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋이 완전히 로드될 때까지 기다리려면 True, 그렇지 않으면 False입니다. 기본값은 True입니다.

#### xr *: XrCfg | [None](https://docs.python.org/3/library/constants.html#None)*

XR 장치를 통해 환경을 보기 및 상호작용하기 위한 구성입니다.

#### teleop_devices *: DevicesCfg*

원격조작 장치에 대한 구성입니다.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 디스크립터를 내보낼지 여부. 기본값은 False입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 기록할 디렉터리. 기본값은 None이며, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

#### terminations *: [object](https://docs.python.org/3/library/functions.html#object)*

종료 설정.

자세한 내용은 [`isaaclab.managers.TerminationManager`](isaaclab.managers.md#isaaclab.managers.TerminationManager) 클래스를 참조하세요.

#### curriculum *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

커리큘럼 설정. 기본값은 None이며, 이 경우 커리큘럼이 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.CurriculumManager`](isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 참조하세요.

#### commands *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

명령 설정. 기본값은 None이며, 이 경우 명령이 생성되지 않습니다.

자세한 내용은 [`isaaclab.managers.CommandManager`](isaaclab.managers.md#isaaclab.managers.CommandManager) 클래스를 참조하세요.

## Direct RL 환경

### *class* isaaclab.envs.DirectRLEnv

기반: [`Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env)

환경을 설계하는 직접 워크플로우를 위한 슈퍼클래스입니다.

이 클래스는 강화학습(RL) 환경의 핵심 기능을 구현합니다. 어떤 RL 라이브러리와도 함께 사용되도록 설계되었으며, 벡터화된 환경에서 사용되도록 설계되었습니다. 즉, 환경은 여러 하위 환경을 병렬로 실행할 것으로 예상됩니다.

환경 자체는 벡터화된 환경으로 구현되었지만, `gym.vector.VectorEnv`를 상속하지 않습니다. 이는 주로 클래스가 비동기 업데이트(대기 및 비동기 업데이트)를 위한 다양한 메서드를 추가하기 때문이며, 이 메서드들은 필수적이지 않습니다. 또한, 각 RL 라이브러리는 일반적으로 벡터화된 환경에 대한 자체 정의를 가지고 있습니다. 따라서 복잡성을 줄이기 위해 여기서는 직접 `gym.Env`를 사용하고, 래퍼가 이 환경을 해당 에이전트에 맞게 래핑하도록 맡깁니다.

#### 참고
벡터화된 환경의 경우, [`reset()`](#isaaclab.envs.DirectRLEnv.reset) 메서드를 환경 생성 후 첫 [`step()`](#isaaclab.envs.DirectRLEnv.step) 호출 전에 **한 번만** 호출하는 것이 권장됩니다. 그 이후에는 [`step()`](#isaaclab.envs.DirectRLEnv.step) 함수가 종료된 하위 환경의 리셋을 처리합니다. 이는 시뮬레이터가 벡터화된 환경에서 개별 하위 환경을 리셋하는 것을 지원하지 않기 때문입니다.

**속성:**

| [`is_vector_env`](#isaaclab.envs.DirectRLEnv.is_vector_env)               | 환경이 벡터화된 환경인지 여부.                                                                  |
|---------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`metadata`](#isaaclab.envs.DirectRLEnv.metadata)                         | 환경의 메타데이터.                                                                                         |
| [`num_envs`](#isaaclab.envs.DirectRLEnv.num_envs)                         | 현재 실행 중인 환경 인스턴스의 수.                                                          |
| [`physics_dt`](#isaaclab.envs.DirectRLEnv.physics_dt)                     | 물리 타임스텝(초 단위).                                                                                         |
| [`step_dt`](#isaaclab.envs.DirectRLEnv.step_dt)                           | 환경 스텝 타임스텝(초 단위).                                                                            |
| [`device`](#isaaclab.envs.DirectRLEnv.device)                             | 환경이 실행 중인 디바이스.                                                                       |
| [`max_episode_length_s`](#isaaclab.envs.DirectRLEnv.max_episode_length_s) | 최대 에피소드 길이(초 단위).                                                                                    |
| [`max_episode_length`](#isaaclab.envs.DirectRLEnv.max_episode_length)     | 초 단위로 조정된 최대 에피소드 길이(스텝 단위).                                                                |
| [`np_random`](#isaaclab.envs.DirectRLEnv.np_random)                       | 환경의 내부 `_np_random`을 반환하며, 설정되지 않은 경우 랜덤 시드로 초기화됩니다.                   |
| [`np_random_seed`](#isaaclab.envs.DirectRLEnv.np_random_seed)             | 환경의 내부 `_np_random_seed`을 반환하며, 설정되지 않은 경우 먼저 랜덤 정수로 시드를 초기화합니다. |
| [`unwrapped`](#isaaclab.envs.DirectRLEnv.unwrapped)                       | 래핑되지 않은 기본 환경을 반환합니다.                                                                             |

**메서드:**

| [`__init__`](#isaaclab.envs.DirectRLEnv.__init__)(cfg[, render_mode])                       | 환경을 초기화합니다.                                                                        |
|---------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| [`reset`](#isaaclab.envs.DirectRLEnv.reset)([seed, options])                                | 모든 환경을 리셋하고 관측값을 반환합니다.                                              |
| [`step`](#isaaclab.envs.DirectRLEnv.step)(action)                                           | 환경의 역학에 따라 한 타임스텝을 실행합니다.                                               |
| [`seed`](#isaaclab.envs.DirectRLEnv.seed)([seed])                                           | 환경의 시드를 설정합니다.                                                                  |
| [`render`](#isaaclab.envs.DirectRLEnv.render)([recompute])                                  | 물리 시뮬레이션을 진행하지 않고 렌더링을 실행합니다.                                                |
| [`close`](#isaaclab.envs.DirectRLEnv.close)()                                               | 환경 정리를 수행합니다.                                                                       |
| [`set_debug_vis`](#isaaclab.envs.DirectRLEnv.set_debug_vis)(debug_vis)                      | 환경 디버그 시각화를 토글합니다.                                                       |
| [`get_wrapper_attr`](#isaaclab.envs.DirectRLEnv.get_wrapper_attr)(name)                     | 환경에서 속성 이름을 가져옵니다.                                                      |
| [`has_wrapper_attr`](#isaaclab.envs.DirectRLEnv.has_wrapper_attr)(name)                     | 환경에 속성 이름이 존재하는지 확인합니다.                                            |
| [`set_wrapper_attr`](#isaaclab.envs.DirectRLEnv.set_wrapper_attr)(name, value, \*[, force]) | 환경에 속성 이름을 값과 함께 설정합니다. 자세한 내용은 `Wrapper.set_wrapper_attr`를 참조하세요. |

#### is_vector_env *: ClassVar[[bool](https://docs.python.org/3/library/functions.html#bool)]* *= True*

환경이 벡터화된 환경인지 여부.

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터.

#### \_\_init_\_(cfg: [DirectRLEnvCfg](#isaaclab.envs.DirectRLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성 객체.
  * **render_mode** – 환경의 렌더링 모드. 기본값은 None이며, 이는 `"human"`과 유사합니다.
* **예외:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 시뮬레이션 컨텍스트가 이미 존재하는 경우. 환경은 항상 하나씩 생성해야 하며, 이는 시뮬레이션 컨텍스트를 구성하고 제어하기 때문입니다.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

현재 실행 중인 환경 인스턴스의 수.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 타임스텝(초 단위).

이 값은 시뮬레이션이 발생하는 가장 낮은 시간 디시메이션입니다.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스텝 타임스텝(초 단위).

이 값은 환경이 앞으로 진행되는 시간 간격입니다.

#### *property* device

환경이 실행 중인 디바이스.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

최대 에피소드 길이(초 단위).

#### *property* max_episode_length

초 단위로 조정된 최대 에피소드 길이(스텝 단위).

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

모든 환경을 재설정하고 관측값을 반환합니다.

이 함수는 모든 환경을 재설정하기 위해 `_reset_idx()` 함수를 호출합니다.
그러나 초기화 중에 발생한 절차적 지형 생성과 같은 특정 작업은 반복되지 않습니다.

* **매개변수:**
  * **seed** – 무작위화에 사용할 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **options** – 

    환경을 재설정하는 방법을 지정하는 추가 정보입니다. 기본값은 None입니다.

    #### 참고
    이 인수는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **반환값:**
  관측값과 추가 정보를 포함하는 튜플입니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동역학에 대한 하나의 시간 스텝을 실행합니다.

환경은 고정된 시간 스텝으로 앞으로 진행되며, 물리 시뮬레이션은 더 낮은 시간 스텝에서 디시메이트됩니다.
이는 시뮬레이션의 안정성을 보장하기 위함입니다. 이 두 시간 스텝은 [`DirectRLEnvCfg.decimation`](#isaaclab.envs.DirectRLEnvCfg.decimation) (환경 스텝당 시뮬레이션 스텝 수)와 `DirectRLEnvCfg.sim.physics_dt` (물리 시간 스텝)를 사용하여 독립적으로 구성할 수 있습니다.
이러한 매개변수를 기반으로 환경 시간 스텝은 두 값의 곱으로 계산됩니다.

이 함수는 다음 단계를 수행합니다:

1. 물리 시뮬레이션을 진행하기 전에 동작을 사전 처리합니다.
2. 시뮬레이터에 동작을 적용하고 디시메이트 방식으로 물리 시뮬레이션을 진행합니다.
3. 보상과 종료 신호를 계산합니다.
4. 종료되었거나 최대 에피소드 길이에 도달한 환경을 재설정합니다.
5. 활성화된 경우 구간 이벤트를 적용합니다.
6. 관측값을 계산합니다.

* **매개변수:**
  **action** – 환경에 적용할 동작입니다. 모양은 (num_envs, action_dim)입니다.
* **반환값:**
  관측값, 보상, 재설정(종료 및 잘림), 추가 정보를 포함하는 튜플입니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **매개변수:**
  **seed** – 랜덤 생성기의 시드입니다. 기본값은 -1입니다.
* **반환값:**
  랜덤 생성기에 사용된 시드입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 시뮬레이션을 진행하지 않고 렌더링을 실행합니다.

관례에 따라 mode가 다음과 같은 경우:

- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않습니다. 일반적으로 인간 소비를 위해 사용됩니다.
- **rgb_array**: (x, y, 3) 형태의 numpy.ndarray를 반환하며, 이는 x-by-y 픽셀 이미지의 RGB 값을 나타내며 비디오로 변환하기에 적합합니다.

* **매개변수:**
  **recompute** – 시뮬레이터가 이미 장면을 렌더링했더라도 강제로 렌더링할지 여부입니다. 기본값은 False입니다.
* **반환값:**
  mode가 “rgb_array”인 경우 numpy 배열 형태의 렌더링된 이미지를 반환합니다. 그렇지 않으면 None을 반환합니다.
* **예외:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – mode가 “rgb_data”로 설정되었고 시뮬레이션 렌더 모드가 이를 지원하지 않는 경우.
        이 경우 시뮬레이션 렌더 모드는 `RenderMode.PARTIAL_RENDERING` 또는 `RenderMode.FULL_RENDERING`으로 설정해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정된 경우.

#### close()

환경의 정리 작업을 수행합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경 디버그 시각화를 토글합니다.

* **매개변수:**
  **debug_vis** – 환경 디버그 시각화를 시각화할지 여부입니다.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부를 나타냅니다. 환경이 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 속성 이름을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 속성 이름이 존재하는지 확인합니다.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

환경의 내부 `_np_random`을 반환하며, 설정되지 않은 경우 랜덤 시드로 초기화됩니다.

* **반환값:**
  np.random.Generator의 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 내부 `_np_random_seed`를 반환하며, 설정되지 않은 경우 먼저 랜덤 정수로 시드를 초기화합니다.

[`np_random_seed`](#isaaclab.envs.DirectRLEnv.np_random_seed)가 [`reset()`](#isaaclab.envs.DirectRLEnv.reset) 또는 `set_np_random_through_seed()` 대신 직접 설정된 경우,
시드는 -1의 값을 가집니다.

* **반환값:**
  현재 np_random의 시드 또는 rng의 시드를 알 수 없는 경우 -1
* **반환 유형:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 값과 함께 속성 이름을 설정합니다. 자세한 내용은 Wrapper.set_wrapper_attr을 참조하세요.

#### *property* unwrapped *: [Env](https://gymnasium.farama.org/api/env/#gymnasium.Env)[ObsType, ActType]*

기본 래핑되지 않은 환경을 반환합니다.

* **반환값:**
  기본 래핑되지 않은 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인스턴스
* **반환 유형:**
  Env

### *class* isaaclab.envs.DirectRLEnvCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

직접 워크플로우로 정의된 RL 환경의 구성입니다.

자세한 내용은 [`isaaclab.envs.direct_rl_env.DirectRLEnv`](#isaaclab.envs.DirectRLEnv) 클래스를 참조하세요.

**특성:**

| [`viewer`](#isaaclab.envs.DirectRLEnvCfg.viewer)                                   | 뷰어 구성.                                                                       |
|------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab.envs.DirectRLEnvCfg.sim)                                         | 물리 시뮬레이션 구성.                                                           |
| [`seed`](#isaaclab.envs.DirectRLEnvCfg.seed)                                       | 난수 생성기의 시드.                                                   |
| [`decimation`](#isaaclab.envs.DirectRLEnvCfg.decimation)                           | 정책 dt당 sim dt에서의 제어 행동 업데이트 수.                                    |
| [`is_finite_horizon`](#isaaclab.envs.DirectRLEnvCfg.is_finite_horizon)             | 학습 작업이 에이전트에 대해 유한 또는 무한 호라이즌 문제로 간주되는지 여부. |
| [`episode_length_s`](#isaaclab.envs.DirectRLEnvCfg.episode_length_s)               | 에피소드 기간(초 단위).                                                        |
| [`scene`](#isaaclab.envs.DirectRLEnvCfg.scene)                                     | 씬 설정.                                                                             |
| [`events`](#isaaclab.envs.DirectRLEnvCfg.events)                                   | 이벤트 설정.                                                                             |
| [`observation_space`](#isaaclab.envs.DirectRLEnvCfg.observation_space)             | 관측 공간 정의.                                                               |
| [`num_observations`](#isaaclab.envs.DirectRLEnvCfg.num_observations)               | 각 환경 인스턴스에서의 관측 공간 차원.                      |
| [`state_space`](#isaaclab.envs.DirectRLEnvCfg.state_space)                         | 상태 공간 정의.                                                                     |
| [`num_states`](#isaaclab.envs.DirectRLEnvCfg.num_states)                           | 각 환경 인스턴스에서의 상태 공간 차원.                            |
| [`observation_noise_model`](#isaaclab.envs.DirectRLEnvCfg.observation_noise_model) | 환경에서 계산된 관측값에 적용할 노이즈 모델.                 |
| [`action_space`](#isaaclab.envs.DirectRLEnvCfg.action_space)                       | 행동 공간 정의.                                                                    |
| [`num_actions`](#isaaclab.envs.DirectRLEnvCfg.num_actions)                         | 각 환경에서의 행동 공간 차원.                                     |
| [`action_noise_model`](#isaaclab.envs.DirectRLEnvCfg.action_noise_model)           | 환경에 제공된 행동에 적용되는 노이즈 모델.                         |
| [`rerender_on_reset`](#isaaclab.envs.DirectRLEnvCfg.rerender_on_reset)             | 최소 하나의 환경이 리셋된 후에 렌더 단계를 다시 수행할지 여부.     |
| [`num_rerenders_on_reset`](#isaaclab.envs.DirectRLEnvCfg.num_rerenders_on_reset)   | 리셋 후 수행할 렌더 단계 횟수.                                              |
| [`wait_for_textures`](#isaaclab.envs.DirectRLEnvCfg.wait_for_textures)             | 애셋이 완전히 로드될 때까지 대기하려면 True, 아니면 False.                           |
| [`xr`](#isaaclab.envs.DirectRLEnvCfg.xr)                                           | XR 디바이스를 통해 환경을 보고 상호작용하기 위한 구성.        |
| [`log_dir`](#isaaclab.envs.DirectRLEnvCfg.log_dir)                                 | 실험 아티팩트를 기록할 디렉터리.                                                 |

**클래스:**

| [`ui_window_class_type`](#isaaclab.envs.DirectRLEnvCfg.ui_window_class_type)   | [`BaseEnvWindow`](isaaclab.envs.ui.md#isaaclab.envs.ui.BaseEnvWindow) 별칭   |
|--------------------------------------------------------------------------------|----------------------------------------------------------------------------------|

#### viewer *: [ViewerCfg](#isaaclab.envs.ViewerCfg)*

뷰어 구성. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성. 기본값은 SimulationCfg()입니다.

#### ui_window_class_type

[`BaseEnvWindow`](isaaclab.envs.ui.md#isaaclab.envs.ui.BaseEnvWindow)의 별칭

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 시 설정됩니다. 이렇게 하면 환경 생성이 결정적이고 다른 실행에서도 유사하게 동작합니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

정책 dt당 시뮬레이션 dt당 제어 액션 업데이트 수.

예를 들어, 시뮬레이션 dt가 0.01초이고 정책 dt가 0.1초이면, 디시메이션은 10이 됩니다.
즉, 제어 액션은 10번의 시뮬레이션 스텝마다 업데이트됩니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

학습 작업을 에이전트에게 유한 또는 무한 호라이즌 문제로 처리할지 여부. 기본값은 False이며, 이는 작업이 무한 호라이즌 문제로 처리됨을 의미합니다.

이 플래그는 유한 및 무한 호라이즌 작업의 세부 사항을 처리합니다:

* **유한 호라이즌**: 시간 초과 시 에이전트는 페널티 또는 부트스트래핑 값이 필요하지 않습니다. 그러나 환경은 시간 제한에 도달한 후 에피소드를 여전히 종료해야 합니다.
* **무한 호라이즌**: 에이전트는 에피소드 끝의 상태 값에 대해 부트스트래핑을 수행해야 합니다. 이는 시간 제한(또는 잘림) 완료 신호를 에이전트에 보내어 이를 트리거함으로써 수행됩니다.

True이면 환경은 유한 호라이즌 문제로 처리되고 타임아웃(또는 잘림) 완료 신호가 에이전트로 전송되지 않습니다.
False이면 환경은 무한 호라이즌 문제로 처리되고 타임아웃(또는 잘림) 완료 신호가 에이전트로 전송됩니다.

#### NOTE
기본 [`ManagerBasedRLEnv`](#isaaclab.envs.ManagerBasedRLEnv) 클래스는 이 플래그를 직접 사용하지 않습니다. 이 플래그는 환경 래퍼에서 사용되어 해당 학습 에이전트에 어떤 유형의 완료 신호를 보낼지 결정합니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 지속 시간(초 단위).

디시메이션 비율과 물리 시간 단계에 기반하여 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디시메이션 비율이 10, 물리 시간 단계가 0.01, 에피소드 길이가 10초이면,
에피소드 길이(스텝)는 100이 됩니다.

#### scene *: [InteractiveSceneCfg](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg)*

씬 설정.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스를 참조하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

이벤트 설정. 기본값은 None이며, 이 경우 이벤트 관리자를 통해 이벤트가 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.EventManager`](isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참조하세요.

#### observation_space *: SpaceType*

관측 공간 정의.

공간은 더 자세한 사양이 필요한 경우 Gymnasium [`spaces`](https://gymnasium.farama.org/api/spaces/#module-gymnasium.spaces)를 사용하여 정의하거나,
단순함을 위해 기본 Python 데이터 유형을 사용하여 정의할 수 있습니다.

| Gymnasium space                                                                                        | Python 데이터 유형                                                       |
|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| [`Box`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Box)                     | 정수 또는 정수 리스트 (예: `7`, `[64, 64, 3]`)                 |
| [`Discrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Discrete)           | 단일 요소 집합 (예: `{2}`)                                       |
| [`MultiDiscrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.MultiDiscrete) | 단일 요소 집합 리스트 (예: `[{2}, {5}]`)                       |
| [`Dict`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Dict)                     | 사전 (예: `{"joints": 7, "rgb": [64, 64, 3], "gripper": {2}}`) |
| [`Tuple`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Tuple)                   | 튜플 (예: `(7, [64, 64, 3], {2})`)                                  |

#### num_observations *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

각 환경 인스턴스에서의 관측 공간 차원.

#### WARNING
이 속성은 더 이상 사용되지 않음. [`observation_space`](#isaaclab.envs.DirectRLEnvCfg.observation_space)를 대신 사용하세요.

#### state_space *: SpaceType | [None](https://docs.python.org/3/library/constants.html#None)*

상태 공간 정의.

비대칭 액터-크리틱에 유용하며 크리틱의 관측 공간을 정의합니다.

공간은 더 자세한 사양이 필요한 경우 Gymnasium [`spaces`](https://gymnasium.farama.org/api/spaces/#module-gymnasium.spaces)를 사용하여 정의하거나,
단순함을 위해 기본 Python 데이터 유형을 사용하여 정의할 수 있습니다.

| Gymnasium space                                                                                        | Python 데이터 유형                                                       |
|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| [`Box`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Box)                     | 정수 또는 정수 리스트 (예: `7`, `[64, 64, 3]`)                 |
| [`Discrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Discrete)           | 단일 요소 집합 (예: `{2}`)                                       |
| [`MultiDiscrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.MultiDiscrete) | 단일 요소 집합 리스트 (예: `[{2}, {5}]`)                       |
| [`Dict`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Dict)                     | 사전 (예: `{"joints": 7, "rgb": [64, 64, 3], "gripper": {2}}`) |
| [`Tuple`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Tuple)                   | 튜플 (예: `(7, [64, 64, 3], {2})`)                                  |

#### num_states *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

각 환경 인스턴스에서의 상태 공간 차원.

#### WARNING
이 속성은 더 이상 사용되지 않음. [`state_space`](#isaaclab.envs.DirectRLEnvCfg.state_space)를 대신 사용하세요.

#### observation_noise_model *: [NoiseModelCfg](isaaclab.utils.md#isaaclab.utils.noise.NoiseModelCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

환경에서 계산된 관측에 적용할 노이즈 모델입니다. 기본값은 None이며, 이는 노이즈가 추가되지 않음을 의미합니다.

자세한 내용은 [`isaaclab.utils.noise.NoiseModel`](isaaclab.utils.md#isaaclab.utils.noise.NoiseModel) 클래스를 참조하세요.

#### action_space *: SpaceType*

행동 공간 정의.

공간은 더 자세한 사양이 필요한 경우 Gymnasium [`spaces`](https://gymnasium.farama.org/api/spaces/#module-gymnasium.spaces)를 사용하여 정의하거나, 간단함을 위해 기본 Python 데이터 유형을 사용할 수 있습니다.

| Gymnasium 공간                                                                                        | Python 데이터 유형                                                       |
|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| [`Box`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Box)                     | 정수 또는 정수 리스트 (예: `7`, `[64, 64, 3]`)                 |
| [`Discrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Discrete)           | 단일 요소 집합 (예: `{2}`)                                       |
| [`MultiDiscrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.MultiDiscrete) | 단일 요소 집합의 리스트 (예: `[{2}, {5}]`)                       |
| [`Dict`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Dict)                     | 딕셔너리 (예: `{"joints": 7, "rgb": [64, 64, 3], "gripper": {2}}`) |
| [`Tuple`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Tuple)                   | 튜플 (예: `(7, [64, 64, 3], {2})`)                                  |

#### num_actions *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

각 환경에 대한 행동 공간의 차원.

#### WARNING
이 속성은 더 이상 사용되지 않습니다. 대신 [`action_space`](#isaaclab.envs.DirectRLEnvCfg.action_space)를 사용하세요.

#### action_noise_model *: [NoiseModelCfg](isaaclab.utils.md#isaaclab.utils.noise.NoiseModelCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

환경에 제공된 행동에 적용되는 노이즈 모델입니다. 기본값은 None이며, 이는 노이즈가 추가되지 않음을 의미합니다.

자세한 내용은 [`isaaclab.utils.noise.NoiseModel`](isaaclab.utils.md#isaaclab.utils.noise.NoiseModel) 클래스를 참조하세요.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

최소한 하나의 환경이 재설정된 후 렌더 단계가 다시 수행되는지 여부입니다. 기본값은 False로, 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 False이면, 재설정 후 센서에서 수집된 데이터는 오래되어 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 이 값이 True이면, 센서 데이터를 업데이트하여 재설정으로부터의 최신 상태를 반영하기 위해 추가 렌더 단계가 수행됩니다. 이는 매번 환경이 재설정될 때 추가 렌더 단계가 수행되므로 성능에 비용이 발생합니다.

#### Deprecated
버전 2.3.1부터 더 이상 사용되지 않음: 이 속성은 더 이상 사용되지 않으며 향후 제거될 예정입니다. 대신 [`num_rerenders_on_reset`](#isaaclab.envs.DirectRLEnvCfg.num_rerenders_on_reset)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정했을 때의 동작과 동일하게 하려면, [`num_rerenders_on_reset`](#isaaclab.envs.DirectRLEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하세요.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후 수행할 렌더 단계 수입니다. 기본값은 0으로, 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 0이면, 재설정 후 렌더 단계가 수행되지 않습니다. 재설정 후 센서에서 수집된 데이터는 오래되어 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 이 값이 0보다 크면, 지정된 수의 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 재설정으로부터의 최신 상태를 반영합니다. 이는 매번 환경이 재설정될 때 추가 렌더 단계가 수행되므로 성능에 비용이 발생합니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 완전히 로드될 때까지 대기하려면 True, 그렇지 않으면 False입니다. 기본값은 True입니다.

#### xr *: XrCfg | [None](https://docs.python.org/3/library/constants.html#None)*

XR 장치를 통해 환경을 보고 상호작용하기 위한 구성입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 기록할 디렉터리입니다. 기본값은 None이며, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

## 직접 다중 에이전트 RL 환경

### *class* isaaclab.envs.DirectMARLEnv

Bases: [`Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env)

다중 에이전트 환경을 설계하기 위한 직접 워크플로의 슈퍼클래스입니다.

이 클래스는 다중 에이전트 강화 학습(MARL) 환경을 위한 핵심 기능을 구현합니다. 어떤 RL 라이브러리와도 함께 사용할 수 있도록 설계되었으며, 벡터화된 환경에서 사용되도록 설계되었습니다. 즉, 환경은 여러 서브 환경에서 병렬로 실행될 것으로 예상됩니다.

이 클래스의 설계는 PettingZoo Parallel API를 기반으로 합니다. 환경 자체는 벡터화된 환경으로 구현되지만, `pettingzoo.ParallelEnv` 또는 `gym.vector.VectorEnv`를 상속하지는 않습니다. 이는 주로 클래스가 그것들과 일관되지 않은 다양한 속성과 메서드를 추가하기 때문입니다.

#### NOTE
벡터화된 환경의 경우, 첫 번째 [`step()`](#isaaclab.envs.DirectMARLEnv.step) 호출 전에 [`reset()`](#isaaclab.envs.DirectMARLEnv.reset) 메서드를 **한 번만** 호출하는 것이 권장됩니다. 즉, 환경이 생성된 직후에 한 번 호출한 후에는, [`step()`](#isaaclab.envs.DirectMARLEnv.step) 함수가 종료된 서브 환경의 재설정을 처리합니다. 이는 벡터화된 환경에서 개별 서브 환경을 재설정하는 것을 시뮬레이터가 지원하지 않기 때문입니다.

**속성:**

| [`metadata`](#isaaclab.envs.DirectMARLEnv.metadata)                         | 환경에 대한 메타데이터입니다.                                                                                         |
|-----------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`num_envs`](#isaaclab.envs.DirectMARLEnv.num_envs)                         | 실행 중인 환경 인스턴스의 수입니다.                                                          |
| [`num_agents`](#isaaclab.envs.DirectMARLEnv.num_agents)                     | 현재 에이전트의 수입니다.                                                                                             |
| [`max_num_agents`](#isaaclab.envs.DirectMARLEnv.max_num_agents)             | 환경이 생성할 수 있는 모든 가능한 에이전트의 수입니다.                                                           |
| [`unwrapped`](#isaaclab.envs.DirectMARLEnv.unwrapped)                       | 모든 래퍼 계층 아래의 언래핑된 환경을 가져옵니다.                                                  |
| [`physics_dt`](#isaaclab.envs.DirectMARLEnv.physics_dt)                     | 물리 시간 간격(초 단위).                                                                                         |
| [`step_dt`](#isaaclab.envs.DirectMARLEnv.step_dt)                           | 환경 스텝 시간 간격(초 단위).                                                                            |
| [`device`](#isaaclab.envs.DirectMARLEnv.device)                             | 환경이 실행 중인 장치입니다.                                                                       |
| [`max_episode_length_s`](#isaaclab.envs.DirectMARLEnv.max_episode_length_s) | 최대 에피소드 길이(초 단위).                                                                                    |
| [`max_episode_length`](#isaaclab.envs.DirectMARLEnv.max_episode_length)     | 초 단위를 기준으로 조정된 최대 에피소드 길이(스텝 단위).                                                                  |
| [`np_random`](#isaaclab.envs.DirectMARLEnv.np_random)                       | 환경의 내부 `_np_random`을 반환합니다. 설정되지 않은 경우 랜덤 시드로 초기화됩니다.                   |
| [`np_random_seed`](#isaaclab.envs.DirectMARLEnv.np_random_seed)             | 환경의 내부 `_np_random_seed`을 반환합니다. 설정되지 않은 경우 먼저 랜덤 정수로 시드를 초기화합니다. |

**메서드:**

| [`__init__`](#isaaclab.envs.DirectMARLEnv.__init__)(cfg[, render_mode])                       | 환경을 초기화합니다.                                                                        |
|-----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| [`observation_space`](#isaaclab.envs.DirectMARLEnv.observation_space)(agent)                  | 지정된 에이전트의 관측 공간을 가져옵니다.                                                 |
| [`action_space`](#isaaclab.envs.DirectMARLEnv.action_space)(agent)                            | 지정된 에이전트의 행동 공간을 가져옵니다.                                                      |
| [`reset`](#isaaclab.envs.DirectMARLEnv.reset)([seed, options])                                | 모든 환경을 재설정하고 관측값을 반환합니다.                                              |
| [`step`](#isaaclab.envs.DirectMARLEnv.step)(actions)                                          | 환경 역학의 한 타임스텝을 실행합니다.                                               |
| [`state`](#isaaclab.envs.DirectMARLEnv.state)()                                               | 환경의 상태를 반환합니다.                                                             |
| [`seed`](#isaaclab.envs.DirectMARLEnv.seed)([seed])                                           | 환경의 시드를 설정합니다.                                                                  |
| [`render`](#isaaclab.envs.DirectMARLEnv.render)([recompute])                                  | 물리 시뮬레이션을 진행하지 않고 렌더링을 실행합니다.                                                |
| [`close`](#isaaclab.envs.DirectMARLEnv.close)()                                               | 환경 정리를 수행합니다.                                                                       |
| [`set_debug_vis`](#isaaclab.envs.DirectMARLEnv.set_debug_vis)(debug_vis)                      | 환경 디버그 시각화를 토글합니다.                                                       |
| [`get_wrapper_attr`](#isaaclab.envs.DirectMARLEnv.get_wrapper_attr)(name)                     | 환경에서 속성 이름을 가져옵니다.                                                      |
| [`has_wrapper_attr`](#isaaclab.envs.DirectMARLEnv.has_wrapper_attr)(name)                     | 환경에 속성 이름이 존재하는지 확인합니다.                                            |
| [`set_wrapper_attr`](#isaaclab.envs.DirectMARLEnv.set_wrapper_attr)(name, value, \*[, force]) | 환경에 지정된 이름의 속성을 값으로 설정합니다. 자세한 내용은 `Wrapper.set_wrapper_attr`를 참조하십시오. |

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터입니다.

#### \_\_init_\_(cfg: [DirectMARLEnvCfg](#isaaclab.envs.DirectMARLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성 객체입니다.
  * **render_mode** – 환경의 렌더 모드입니다. 기본값은 None이며, 이는 `"human"`과 유사합니다.
* **예외 발생:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 시뮬레이션 컨텍스트가 이미 존재하는 경우입니다. 환경은 시뮬레이션 컨텍스트를 구성하고 제어해야 하므로 항상 새로 생성해야 합니다.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

실행 중인 환경 인스턴스의 수입니다.

#### *property* num_agents *: [int](https://docs.python.org/3/library/functions.html#int)*

현재 에이전트의 수입니다.

에이전트 수는 환경이 진행됨에 따라 변경될 수 있습니다(예: 에이전트가 추가되거나 제거될 수 있음).

#### *property* max_num_agents *: [int](https://docs.python.org/3/library/functions.html#int)*

환경이 생성할 수 있는 모든 가능한 에이전트의 수입니다.

이 값은 환경이 진행됨에 따라 일정하게 유지됩니다.

#### *property* unwrapped *: [DirectMARLEnv](#isaaclab.envs.DirectMARLEnv)*

래퍼들의 모든 레이어 아래에 있는 래핑되지 않은 환경을 가져옵니다.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 타임스텝(초 단위)입니다.

이 값은 시뮬레이션이 발생하는 가장 낮은 시간 간격입니다.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스텝 타임스텝(초 단위)입니다.

이 값은 환경이 앞으로 진행하는 시간 간격입니다.

#### *property* device

환경이 실행 중인 장치입니다.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

초 단위로 표시된 최대 에피소드 길이입니다.

#### *property* max_episode_length

초 단위에서 조정된 최대 에피소드 길이(스텝 단위)입니다.

#### observation_space(agent: AgentID) → [Space](https://gymnasium.farama.org/api/spaces/#gymnasium.spaces.Space)

지정된 에이전트에 대한 관측 공간을 반환합니다.

* **반환값:**
  에이전트의 관측 공간입니다.

#### action_space(agent: AgentID) → [Space](https://gymnasium.farama.org/api/spaces/#gymnasium.spaces.Space)

지정된 에이전트에 대한 행동 공간을 반환합니다.

* **반환값:**
  에이전트의 행동 공간입니다.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[AgentID, ObsType], [dict](https://docs.python.org/3/library/stdtypes.html#dict)[AgentID, [dict](https://docs.python.org/3/library/stdtypes.html#dict)]]

모든 환경을 재설정하고 관측값을 반환합니다.

* **매개변수:**
  * **seed** – 랜덤화에 사용할 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **options** – 

    환경을 재설정하는 방법을 지정하는 추가 정보입니다. 기본값은 None입니다.

    #### 참고
    이 인수는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **반환값:**
  에이전트 ID별로 키가 지정된 관측값과 추가 정보(엑스트라)를 포함하는 튜플입니다.

#### step(actions: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[AgentID, ActionType]) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[AgentID, ObsType], [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[AgentID, [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)], [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[AgentID, [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)], [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[AgentID, [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)], [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[AgentID, [dict](https://docs.python.org/3/library/stdtypes.html#dict)]]

환경 역학의 한 타임스텝을 실행합니다.

환경은 고정된 타임스텝으로 앞으로 진행하지만, 물리 시뮬레이션은 더 낮은 타임스텝으로 디시메이션됩니다. 이는 시뮬레이션의 안정성을 보장하기 위함입니다. 이 두 타임스텝은 [`DirectMARLEnvCfg.decimation`](#isaaclab.envs.DirectMARLEnvCfg.decimation) (환경 스텝당 시뮬레이션 스텝 수)와 `DirectMARLEnvCfg.sim.physics_dt` (물리 타임스텝)를 사용하여 독립적으로 구성할 수 있습니다. 이러한 매개변수를 기반으로 환경 타임스텝은 두 값의 곱으로 계산됩니다.

이 함수는 다음 단계를 수행합니다:

1. 물리 시뮬레이션을 진행하기 전에 행동을 사전 처리합니다.
2. 행동을 시뮬레이터에 적용하고 디시메이션된 방식으로 물리 시뮬레이션을 진행합니다.
3. 보상 및 종료 신호를 계산합니다.
4. 종료되거나 최대 에피소드 길이에 도달한 환경을 재설정합니다.
5. 활성화된 경우 간격 이벤트를 적용합니다.
6. 관측값을 계산합니다.

* **매개변수:**
  **actions** – 환경에 적용할 행동(에이전트 ID별로 키가 지정된 것입니다). 개별 텐서의 형태는 (num_envs, action_dim)입니다.
* **반환값:**
  에이전트 ID별로 키가 지정된 관측값, 보상, 리셋(종료 및 트렁케이트), 추가 정보를 포함하는 튜플입니다. 개별 텐서의 형태는 (num_envs, …)입니다.

#### state() → StateType | [None](https://docs.python.org/3/library/constants.html#None)

환경의 상태를 반환합니다.

상태 공간은 중앙 집중식 훈련 또는 비대칭 액터-크리틱 아키텍처에 사용됩니다. 이는 [`DirectMARLEnvCfg.state_space`](#isaaclab.envs.DirectMARLEnvCfg.state_space) 매개변수를 사용하여 구성됩니다.

* **반환값:**
  환경의 상태이며, [`DirectMARLEnvCfg.state_space`](#isaaclab.envs.DirectMARLEnvCfg.state_space) 매개변수가 0인 경우 None을 반환합니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **매개변수:**
  **seed** – 랜덤 생성기의 시드입니다. 기본값은 -1입니다.
* **반환값:**
  랜덤 생성기에 사용된 시드입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 시뮬레이션을 진행하지 않고 렌더링을 실행합니다.

관례에 따라 모드가 다음과 같은 경우:

- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않습니다. 일반적으로 인간 소비를 위한 것입니다.
- **rgb_array**: (x, y, 3) 형태의 numpy.ndarray를 반환하며, 이는 x-by-y 픽셀 이미지의 RGB 값을 나타내며 비디오로 변환하기에 적합합니다.

* **매개변수:**
  **recompute** – 시뮬레이터가 이미 장면을 렌더링했을지라도 강제로 렌더링을 수행할지 여부입니다. 기본값은 False입니다.
* **반환값:**
  모드가 "rgb_array"인 경우 렌더링된 이미지를 numpy 배열로 반환합니다. 그렇지 않으면 None을 반환합니다.
* **예외 발생:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 모드가 "rgb_data"로 설정되었으나 시뮬레이션 렌더 모드가 이를 지원하지 않는 경우입니다. 이 경우 시뮬레이션 렌더 모드는 `RenderMode.PARTIAL_RENDERING` 또는 `RenderMode.FULL_RENDERING`으로 설정해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정되었을 때 발생합니다.

#### close()

환경 정리 작업을 수행합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경 디버그 시각화를 토글합니다.

* **파라미터:**
  **debug_vis** – 환경 디버그 시각화를 표시할지 여부입니다.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부를 나타냅니다. 환경이 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 지정된 이름의 속성을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 지정된 이름의 속성이 존재하는지 확인합니다.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

설정되지 않은 경우 랜덤 시드로 초기화되는 환경의 내부 `_np_random`을 반환합니다.

* **반환값:**
  np.random.Generator의 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

설정되지 않은 경우 먼저 랜덤 정수로 시드를 초기화하는 환경의 내부 `_np_random_seed`을 반환합니다.

[`np_random_seed`](#isaaclab.envs.DirectMARLEnv.np_random_seed)을 [`reset()`](#isaaclab.envs.DirectMARLEnv.reset) 또는 `set_np_random_through_seed()`를 통해가 아니라 직접 설정했을 경우,
시드는 -1이 됩니다.

* **반환값:**
  현재 np_random의 시드 또는 rng의 시드를 알 수 없는 경우 -1
* **반환 유형:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경의 지정된 이름의 속성에 값을 설정합니다. 자세한 내용은 Wrapper.set_wrapper_attr을 참조하세요.

### *class* isaaclab.envs.DirectMARLEnvCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

다이렉트 워크플로우로 정의된 MARL 환경의 구성입니다.

자세한 내용은 [`isaaclab.envs.direct_marl_env.DirectMARLEnv`](#isaaclab.envs.DirectMARLEnv) 클래스를 참조하세요.

**속성:**

| [`viewer`](#isaaclab.envs.DirectMARLEnvCfg.viewer)                                   | 뷰어 구성입니다.                                                                       |
|--------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab.envs.DirectMARLEnvCfg.sim)                                         | 물리 시뮬레이션 구성입니다.                                                           |
| [`seed`](#isaaclab.envs.DirectMARLEnvCfg.seed)                                       | 난수 생성기의 시드입니다.                                                   |
| [`decimation`](#isaaclab.envs.DirectMARLEnvCfg.decimation)                           | 정책 dt당 시뮬레이션 dt의 제어 액션 업데이트 횟수입니다.                                    |
| [`is_finite_horizon`](#isaaclab.envs.DirectMARLEnvCfg.is_finite_horizon)             | 에이전트에게 학습 작업이 유한 horizon인지 무한 horizon인지 여부를 나타냅니다. |
| [`episode_length_s`](#isaaclab.envs.DirectMARLEnvCfg.episode_length_s)               | 에피소드의 지속 시간(초 단위)입니다.                                                        |
| [`scene`](#isaaclab.envs.DirectMARLEnvCfg.scene)                                     | 씬 설정입니다.                                                                             |
| [`events`](#isaaclab.envs.DirectMARLEnvCfg.events)                                   | 이벤트 설정입니다.                                                                       |
| [`observation_spaces`](#isaaclab.envs.DirectMARLEnvCfg.observation_spaces)           | 각 에이전트에 대한 관측 공간 정의입니다.                                                |
| [`num_observations`](#isaaclab.envs.DirectMARLEnvCfg.num_observations)               | 각 에이전트에 대한 관측 공간의 차원입니다.                                      |
| [`state_space`](#isaaclab.envs.DirectMARLEnvCfg.state_space)                         | 상태 공간 정의입니다.                                                                     |
| [`num_states`](#isaaclab.envs.DirectMARLEnvCfg.num_states)                           | 각 환경 인스턴스에서의 상태 공간의 차원입니다.                            |
| [`observation_noise_model`](#isaaclab.envs.DirectMARLEnvCfg.observation_noise_model) | 환경에서 계산된 관측값에 적용할 노이즈 모델입니다.                 |
| [`action_spaces`](#isaaclab.envs.DirectMARLEnvCfg.action_spaces)                     | 각 에이전트에 대한 액션 공간 정의입니다.                                                     |
| [`num_actions`](#isaaclab.envs.DirectMARLEnvCfg.num_actions)                         | 각 에이전트에 대한 액션 공간의 차원입니다.                                           |
| [`action_noise_model`](#isaaclab.envs.DirectMARLEnvCfg.action_noise_model)           | 환경에 제공된 액션에 적용되는 노이즈 모델입니다.                         |
| [`possible_agents`](#isaaclab.envs.DirectMARLEnvCfg.possible_agents)                 | 환경이 생성할 수 있는 모든 가능한 에이전트의 목록입니다.                               |
| [`xr`](#isaaclab.envs.DirectMARLEnvCfg.xr)                                           | XR 디바이스를 통해 환경의 시각화 및 상호작용 구성입니다.        |
| [`log_dir`](#isaaclab.envs.DirectMARLEnvCfg.log_dir)                                 | 실험 아티팩트를 로그로 저장할 디렉터리입니다.                                                 |

**클래스:**

| [`ui_window_class_type`](#isaaclab.envs.DirectMARLEnvCfg.ui_window_class_type)   | [`BaseEnvWindow`](isaaclab.envs.ui.md#isaaclab.envs.ui.BaseEnvWindow)의 별칭입니다.   |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|

#### viewer *: [ViewerCfg](#isaaclab.envs.ViewerCfg)*

뷰어 구성입니다. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성입니다. 기본값은 SimulationCfg()입니다.

#### ui_window_class_type

[`BaseEnvWindow`](isaaclab.envs.ui.md#isaaclab.envs.ui.BaseEnvWindow)의 별칭입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작時に 설정됩니다.これにより、環境の作成が決定的になり、異なる実行間でも同様の動作をします。

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

정책 dt당 시뮬레이션 dt에서의 제어 액션 업데이트 횟수입니다.

예를 들어, 시뮬레이션 dt가 0.01s이고 정책 dt가 0.1s이면, 디시메이션은 10입니다.
이는 제어 액션이 매 10번의 시뮬레이션 스텝마다 업데이트됨을 의미합니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에이전트에게 학습 작업이 유한 horizon 문제인지 무한 horizon 문제인지 여부를 나타냅니다.
기본값은 False이며, 이는 작업이 무한 horizon 문제로 처리됨을 의미합니다.

이 플래그는 유한 horizon과 무한 horizon 작업의 미묘한 차이를 처리합니다:

* **유한 horizon**: 시간이 다 되었을 때 에이전트에게 페널티나 부트스트래핑 값이 필요하지 않습니다. 그러나 환경은 시간 제한이 도달한 후 에피소드를 여전히 종료해야 합니다.
* **무한 horizon**: 에이전트는 에피소드 끝의 상태 값에 부트스트래핑을 수행해야 합니다. 이는 시간 제한(또는 잘림) 완료 신호를 에이전트에게 보내서 이 부트스트래핑 계산을 트리거함으로써 수행됩니다.

True이면 환경은 유한 horizon 문제로 처리되며, 타임아웃(또는 잘림) 완료 신호가 에이전트에게 전송되지 않습니다.
False이면 환경은 무한 horizon 문제로 처리되며, 타임아웃(또는 잘림) 완료 신호가 에이전트에게 전송됩니다.

#### NOTE
기본 [`ManagerBasedRLEnv`](#isaaclab.envs.ManagerBasedRLEnv) 클래스는 이 플래그를 직접 사용하지 않습니다. 이 플래그는 환경 래퍼에서 사용되어 해당 학습 에이전트에게 전송할 완료 신호의 유형을 결정합니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드의 지속 시간(초 단위)입니다.

디시메이션 레이트와 물리 시간 스텝을 기반으로, 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디시메이션 레이트가 10이고, 물리 시간 스텝이 0.01이며, 에피소드 길이가 10초라면,
에피소드 길이(스텝 단위)는 100입니다.

#### scene *: [InteractiveSceneCfg](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg)*

씬 설정입니다.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스를 참조하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정입니다. 기본값은 None이며, 이 경우 이벤트 관리자를 통해 이벤트가 적용되지 않습니다.

#### observation_spaces *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[AgentID, SpaceType]*

각 에이전트에 대한 관측 공간 정의.

이 공간은 Gymnasium [`spaces`](https://gymnasium.farama.org/api/spaces/#module-gymnasium.spaces)를 사용하여 정의할 수 있습니다(공간에 대한 보다 상세한 사양이 필요한 경우). 또는 기본 Python 데이터 유형을 사용할 수 있습니다(단순성 목적으로).

| Gymnasium 공간                                                                                        | Python 데이터 유형                                                       |
|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| [`Box`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Box)                     | 정수 또는 정수 리스트 (예: `7`, `[64, 64, 3]`)                     |
| [`Discrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Discrete)           | 단일 요소 집합 (예: `{2}`)                                           |
| [`MultiDiscrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.MultiDiscrete) | 단일 요소 집합 리스트 (예: `[{2}, {5}]`)                             |
| [`Dict`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Dict)                     | 사전 (예: `{"joints": 7, "rgb": [64, 64, 3], "gripper": {2}}`)     |
| [`Tuple`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Tuple)                   | 튜플 (예: `(7, [64, 64, 3], {2})`)                                   |

#### num_observations *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[AgentID, [int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None)*

각 에이전트에 대한 관측 공간의 차원.

#### WARNING
이 속성은 더 이상 사용되지 않습니다. 대신 [`observation_spaces`](#isaaclab.envs.DirectMARLEnvCfg.observation_spaces)를 사용하십시오.

#### state_space *: SpaceType*

상태 공간 정의.

다음 값이 지원됩니다:

* -1: 다양한 에이전트의 모든 관측값이 자동으로 연결됩니다.
* 0: 상태공간이 구성되지 않습니다(state_space는 None). 알고리즘을 훈련할 때 상태공간이 필요하지 않은 경우 계산 리소스를 절약하는 데 유용합니다.
* 0보다 큼: 태스크 구현에서 제공해야 하는 사용자 정의 상태공간 차원.

이 공간은 Gymnasium [`spaces`](https://gymnasium.farama.org/api/spaces/#module-gymnasium.spaces)를 사용하여 정의할 수 있습니다(공간에 대한 보다 상세한 사양이 필요한 경우). 또는 기본 Python 데이터 유형을 사용할 수 있습니다(단순성 목적으로).

| Gymnasium 공간                                                                                        | Python 데이터 유형                                                       |
|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| [`Box`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Box)                     | 정수 또는 정수 리스트 (예: `7`, `[64, 64, 3]`)                     |
| [`Discrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Discrete)           | 단일 요소 집합 (예: `{2}`)                                           |
| [`MultiDiscrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.MultiDiscrete) | 단일 요소 집합 리스트 (예: `[{2}, {5}]`)                             |
| [`Dict`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Dict)                     | 사전 (예: `{"joints": 7, "rgb": [64, 64, 3], "gripper": {2}}`)     |
| [`Tuple`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Tuple)                   | 튜플 (예: `(7, [64, 64, 3], {2})`)                                   |

#### num_states *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

각 환경 인스턴스의 상태 공간 차원.

#### WARNING
이 속성은 더 이상 사용되지 않습니다. 대신 [`state_space`](#isaaclab.envs.DirectMARLEnvCfg.state_space)를 사용하십시오.

#### observation_noise_model *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[AgentID, [isaaclab.utils.noise.noise_cfg.NoiseModelCfg](isaaclab.utils.md#isaaclab.utils.noise.NoiseModelCfg) | [None](https://docs.python.org/3/library/constants.html#None)] | [None](https://docs.python.org/3/library/constants.html#None)*

환경에서 계산된 관측값에 적용할 노이즈 모델입니다. 기본값은 None이며, 이는 노이즈가 추가되지 않음을 의미합니다.

자세한 내용은 [`isaaclab.utils.noise.NoiseModel`](isaaclab.utils.md#isaaclab.utils.noise.NoiseModel) 클래스를 참조하십시오.

#### action_spaces *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[AgentID, SpaceType]*

각 에이전트에 대한 행동 공간 정의.

이 공간은 Gymnasium [`spaces`](https://gymnasium.farama.org/api/spaces/#module-gymnasium.spaces)를 사용하여 정의할 수 있습니다(공간에 대한 보다 상세한 사양이 필요한 경우). 또는 기본 Python 데이터 유형을 사용할 수 있습니다(단순성 목적으로).

| Gymnasium 공간                                                                                        | Python 데이터 유형                                                       |
|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| [`Box`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Box)                     | 정수 또는 정수 리스트 (예: `7`, `[64, 64, 3]`)                     |
| [`Discrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.Discrete)           | 단일 요소 집합 (예: `{2}`)                                           |
| [`MultiDiscrete`](https://gymnasium.farama.org/api/spaces/fundamental/#gymnasium.spaces.MultiDiscrete) | 단일 요소 집합 리스트 (예: `[{2}, {5}]`)                             |
| [`Dict`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Dict)                     | 사전 (예: `{"joints": 7, "rgb": [64, 64, 3], "gripper": {2}}`)     |
| [`Tuple`](https://gymnasium.farama.org/api/spaces/composite/#gymnasium.spaces.Tuple)                   | 튜플 (예: `(7, [64, 64, 3], {2})`)                                   |

#### num_actions *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[AgentID, [int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None)*

각 에이전트에 대한 행동 공간의 차원.

#### WARNING
이 속성은 더 이상 사용되지 않습니다. 대신 [`action_spaces`](#isaaclab.envs.DirectMARLEnvCfg.action_spaces)를 사용하십시오.

#### action_noise_model *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[AgentID, [isaaclab.utils.noise.noise_cfg.NoiseModelCfg](isaaclab.utils.md#isaaclab.utils.noise.NoiseModelCfg) | [None](https://docs.python.org/3/library/constants.html#None)] | [None](https://docs.python.org/3/library/constants.html#None)*

환경에 제공된 행동에 적용되는 노이즈 모델입니다. 기본값은 None이며, 이는 노이즈가 추가되지 않음을 의미합니다.

자세한 내용은 [`isaaclab.utils.noise.NoiseModel`](isaaclab.utils.md#isaaclab.utils.noise.NoiseModel) 클래스를 참조하십시오.

#### possible_agents *: [list](https://docs.python.org/3/library/stdtypes.html#list)[AgentID]*

환경이 생성할 수 있는 모든 가능한 에이전트의 목록.

전체 훈련 프로세스 동안 목록의 내용을 수정할 수 없습니다.

#### xr *: XrCfg | [None](https://docs.python.org/3/library/constants.html#None)*

XR 디바이스를 통해 환경을 보기 및 상호작용하는 구성.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 위한 로그 디렉터리. 기본값은 None이며, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

## Mimic 환경

### *class* isaaclab.envs.ManagerBasedRLMimicEnv

Bases: [`ManagerBasedRLEnv`](#isaaclab.envs.ManagerBasedRLEnv)

Isaac Lab Mimic 환경의 수퍼클래스입니다.

이 클래스는 [`ManagerBasedRLEnv`](#isaaclab.envs.ManagerBasedRLEnv)를 상속받으며, Isaac Lab Mimic 데이터 생성 워크플로를 실행하기 위해 정의해야 하는 함수에 대한 템플릿을 제공합니다. Isaac Lab 데이터 생성 파이프라인은 MimicGen 시스템에서 영감을 받아 몇 개의 인간 시연을 기반으로 새로운 데이터세트를 생성할 수 있습니다. MimicGen은 희소한 인간 시연 세트를 새로운 컨텍스트에 적응시켜 대규모 풍부한 데이터세트를 자동으로 합성하도록 설계된 혁신적인 접근 방식으로, 인간 시연을 수집하는 데 필요한 엄청난 시간과 노력을 줄이면서 대용량 데이터세트의 이점을 복제할 수 있습니다.

MimicGen 시스템은 시연을 객체 중심 세그먼트로 파싱한 후, 각 세그먼트를 새로운 장면의 컨텍스트에 따라 변환하여 새로운 장면에 적합하도록 조정하고, 이를 연결하여 로봇 엔드 이펙터가 실행할 수 있는 일관된 경로를 생성합니다. 이 접근 방식을 통해 학습자는 다양한 장면 구성, 객체 인스턴스 등에 대한 모방 학습을 통해 숙련된 에이전트를 훈련할 수 있습니다.

주요 기능:
: - 효율적인 데이터세트 생성: 작은 규모의 인간 시연을 활용하여 대규모 시연을 생성합니다.
  - 광범위한 적용 가능성: 물체 조작 및 연결된 객체와의 상호작용과 같이 조작 기술의 범위가 필요한 작업을 지원할 수 있습니다.
  - 데이터셋 다용성: 합성 데이터는 추가 인간 데모와 비교했을 때 유리한 품질을 유지합니다.

**Methods:**

| [`get_robot_eef_pose`](#isaaclab.envs.ManagerBasedRLMimicEnv.get_robot_eef_pose)(eef_name[, env_ids])      | 현재 로봇 엔드 이펙터 포즈를 가져옵니다.                                                                                                                                       |
|------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`target_eef_pose_to_action`](#isaaclab.envs.ManagerBasedRLMimicEnv.target_eef_pose_to_action)(...[, ...]) | 엔드 이펙터 컨트롤러에 대한 목표 포즈와 그리퍼 동작을 취해, 해당 목표 포즈를 달성하기 위해 시도하는 동작(보통 정규화된 델타 포즈 동작)을 반환합니다. |
| [`action_to_target_eef_pose`](#isaaclab.envs.ManagerBasedRLMimicEnv.action_to_target_eef_pose)(action)     | `env.step`과 호환되는 동작을 엔드 이펙터 컨트롤러의 목표 포즈로 변환합니다.                                                                               |
| [`actions_to_gripper_actions`](#isaaclab.envs.ManagerBasedRLMimicEnv.actions_to_gripper_actions)(actions)  | `env.step`과 호환되는 환경 동작 시퀀스에서 그리퍼 구동 부분을 추출합니다.                                                                             |
| [`get_object_poses`](#isaaclab.envs.ManagerBasedRLMimicEnv.get_object_poses)([env_ids])                    | 현재 씬에서 Isaac Lab Mimic 데이터 생성과 관련된 각 객체의 포즈를 가져옵니다.                                                                             |
| [`get_subtask_start_signals`](#isaaclab.envs.ManagerBasedRLMimicEnv.get_subtask_start_signals)([env_ids])  | 작업에서 각 서브태스크의 시작 신호 플래그를 포함하는 딕셔너리를 가져옵니다.                                                                                                        |
| [`get_subtask_term_signals`](#isaaclab.envs.ManagerBasedRLMimicEnv.get_subtask_term_signals)([env_ids])    | 작업에서 각 서브태스크의 종료 신호 플래그를 포함하는 딕셔너리를 가져옵니다.                                                                                                  |
| [`serialize`](#isaaclab.envs.ManagerBasedRLMimicEnv.serialize)()                                           | 이 환경을 재인스턴스화하기 위해 필요한 모든 정보를 딕셔너리 형태로 저장합니다.                                                                                            |
| [`get_navigation_state`](#isaaclab.envs.ManagerBasedRLMimicEnv.get_navigation_state)([env_ids])            | 선택적 메서드입니다.                                                                                                                                                           |
| [`__init__`](#isaaclab.envs.ManagerBasedRLMimicEnv.__init__)(cfg[, render_mode])                           | 환경을 초기화합니다.                                                                                                                                                |
| [`close`](#isaaclab.envs.ManagerBasedRLMimicEnv.close)()                                                   | 환경 정리를 수행합니다.                                                                                                                                               |
| [`export_IO_descriptors`](#isaaclab.envs.ManagerBasedRLMimicEnv.export_IO_descriptors)([output_dir])       | 환경의 IO 디스크립터를 내보냅니다.                                                                                                                             |
| [`get_wrapper_attr`](#isaaclab.envs.ManagerBasedRLMimicEnv.get_wrapper_attr)(name)                         | 환경에서 속성 이름을 가져옵니다.                                                                                                                              |
| [`has_wrapper_attr`](#isaaclab.envs.ManagerBasedRLMimicEnv.has_wrapper_attr)(name)                         | 환경에 특정 속성 이름이 존재하는지 확인합니다.                                                                                                                    |
| [`load_managers`](#isaaclab.envs.ManagerBasedRLMimicEnv.load_managers)()                                   | 환경의 관리자를 로드합니다.                                                                                                                                     |
| [`render`](#isaaclab.envs.ManagerBasedRLMimicEnv.render)([recompute])                                      | 물리 단계를 밟지 않고 렌더링을 실행합니다.                                                                                                                        |
| [`reset`](#isaaclab.envs.ManagerBasedRLMimicEnv.reset)([seed, env_ids, options])                           | 지정된 환경을 재설정하고 관측값을 반환합니다.                                                                                                                |
| [`reset_to`](#isaaclab.envs.ManagerBasedRLMimicEnv.reset_to)(state, env_ids[, seed, is_relative])          | 지정된 환경을 제공된 상태로 재설정합니다.                                                                                                                          |
| [`seed`](#isaaclab.envs.ManagerBasedRLMimicEnv.seed)([seed])                                               | 환경의 시드를 설정합니다.                                                                                                                                          |
| [`set_wrapper_attr`](#isaaclab.envs.ManagerBasedRLMimicEnv.set_wrapper_attr)(name, value, \*[, force])     | 환경에 값을 가진 속성 이름을 설정합니다. Wrapper.set_wrapper_attr를 참조하세요.                                                                         |
| [`setup_manager_visualizers`](#isaaclab.envs.ManagerBasedRLMimicEnv.setup_manager_visualizers)()           | 관리자 항목의 라이브 시각화 도구를 생성합니다.                                                                                                                                |
| [`step`](#isaaclab.envs.ManagerBasedRLMimicEnv.step)(action)                                               | 환경의 역학을 한 타임스텝 실행하고 종료된 환경을 재설정합니다.                                                                                     |

**Attributes:**

| [`device`](#isaaclab.envs.ManagerBasedRLMimicEnv.device)                             | 환경이 실행 중인 장치입니다.                                                                       |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`get_IO_descriptors`](#isaaclab.envs.ManagerBasedRLMimicEnv.get_IO_descriptors)     | 환경의 IO 디스크립터를 가져옵니다.                                                                           |
| [`is_vector_env`](#isaaclab.envs.ManagerBasedRLMimicEnv.is_vector_env)               | 환경이 벡터화된 환경인지 여부를 나타냅니다.                                                                  |
| [`max_episode_length`](#isaaclab.envs.ManagerBasedRLMimicEnv.max_episode_length)     | 환경 단계에서의 최대 에피소드 길이입니다.                                                                          |
| [`max_episode_length_s`](#isaaclab.envs.ManagerBasedRLMimicEnv.max_episode_length_s) | 초 단위의 최대 에피소드 길이입니다.                                                                                    |
| [`metadata`](#isaaclab.envs.ManagerBasedRLMimicEnv.metadata)                         | 환경의 메타데이터입니다.                                                                                         |
| [`np_random`](#isaaclab.envs.ManagerBasedRLMimicEnv.np_random)                       | 설정되지 않은 경우 랜덤 시드로 초기화되는 환경의 내부 `_np_random`을 반환합니다.                   |
| [`np_random_seed`](#isaaclab.envs.ManagerBasedRLMimicEnv.np_random_seed)             | 설정되지 않은 경우 먼저 랜덤 정수로 시드를 초기화한 후, 환경의 내부 `_np_random_seed`를 반환합니다. |
| [`num_envs`](#isaaclab.envs.ManagerBasedRLMimicEnv.num_envs)                         | 현재 실행 중인 환경 인스턴스의 수입니다.                                                          |
| [`physics_dt`](#isaaclab.envs.ManagerBasedRLMimicEnv.physics_dt)                     | 물리 타임스텝(초 단위)입니다.                                                                                         |
| [`step_dt`](#isaaclab.envs.ManagerBasedRLMimicEnv.step_dt)                           | 환경 스텝 타임스텝(초 단위)입니다.                                                                            |
| [`unwrapped`](#isaaclab.envs.ManagerBasedRLMimicEnv.unwrapped)                       | 래핑되지 않은 기본 환경을 반환합니다.                                                                             |
| [`cfg`](#isaaclab.envs.ManagerBasedRLMimicEnv.cfg)                                   | 환경의 구성입니다.                                                                                    |

#### get_robot_eef_pose(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

현재 로봇 엔드 이펙터의 자세를 가져옵니다. 로봇 엔드 이펙터 컨트롤러에서 사용되는 프레임과 동일해야 합니다.

* **매개변수:**
  * **eef_name** – 엔드 이펙터의 이름.
  * **env_ids** – 자세를 얻고자 하는 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  엔드 이펙터 자세 행렬을 나타내는 torch.Tensor. 형태는 (len(env_ids), 4, 4)입니다.

#### target_eef_pose_to_action(target_eef_pose_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), gripper_action_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), action_noise_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_id: [int](https://docs.python.org/3/library/functions.html#int) = 0) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

엔드 이펙터 컨트롤러에 대한 목표 자세와 그리퍼 동작을 받아, 해당 목표 자세를 달성하기 위한 동작(보통 정규화된 델타 자세 동작)을 반환합니다. 지정된 경우 목표 자세 동작에 노이즈가 추가됩니다.

* **매개변수:**
  * **target_eef_pose_dict** – 각 엔드 이펙터에 대한 4x4 목표 엔드 이펙터 자세의 딕셔너리.
  * **gripper_action_dict** – 각 엔드 이펙터에 대한 그리퍼 동작의 딕셔너리.
  * **action_noise_dict** – 동작에 추가할 노이즈. None이면 노이즈가 추가되지 않습니다.
  * **env_id** – 동작을 계산할 환경 인덱스.
* **반환값:**
  env.step()과 호환되는 동작 torch.Tensor.

#### action_to_target_eef_pose(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

env.step과 호환되는 동작을 엔드 이펙터 컨트롤러의 목표 자세로 변환합니다. @target_eef_pose_to_action의 역 함수입니다. 보통 기록된 동작을 사용하여 시연 경로에서 목표 컨트롤러 자세 시퀀스를 추론하는 데 사용됩니다.

* **매개변수:**
  **action** – 환경 동작. 형태는 (num_envs, action_dim)입니다.
* **반환값:**
  @action에 해당하는 엔드 이펙터 자세 torch.Tensor의 딕셔너리.

#### actions_to_gripper_actions(actions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

env.step과 호환되는 환경 동작 시퀀스에서 그리퍼 구동 부분을 추출합니다.

* **매개변수:**
  **actions** – 환경 동작. 형태는 (num_envs, 데모의 단계 수, action_dim)입니다.
* **반환값:**
  각 딕셔너리의 키가 엔드 이펙터 이름인 torch.Tensor 그리퍼 동작의 딕셔너리.

#### get_object_poses(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

현재.scene에서 Isaac Lab Mimic 데이터 생성과 관련된 각 객체의 자세를 가져옵니다.

* **매개변수:**
  **env_ids** – 자세를 얻고자 하는 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  객체 이름을 객체 자세 행렬(4x4 torch.Tensor)에 매핑하는 딕셔너리.

#### get_subtask_start_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

작업의 각 서브태스크에 대한 시작 신호 플래그의 딕셔너리를 가져옵니다. 서브태스크가 시작된 경우 플래그는 1이고, 그렇지 않은 경우 0입니다. 데이터셋 주석 도구 실행 시 자동 서브태스크 시작 신호 주석을 활성화하려는 경우 이 메서드의 구현이 필요합니다. 수동 서브태스크 시작 신호 주석을 사용하려는 경우 이 메서드를 구현하지 않은 상태로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 시작 신호를 얻고자 하는 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  각 서브태스크에 대한 시작 신호 플래그(False 또는 True)의 딕셔너리.

#### get_subtask_term_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

작업의 각 서브태스크에 대한 종료 신호 플래그의 딕셔너리를 가져옵니다. 서브태스크가 완료된 경우 플래그는 1이고, 그렇지 않은 경우 0입니다. 데이터셋 주석 도구 실행 시 자동 서브태스크 종료 신호 주석을 활성화하려는 경우 이 메서드의 구현이 필요합니다. 수동 서브태스크 종료 신호 주석을 사용하려는 경우 이 메서드를 구현하지 않은 상태로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 종료 신호를 얻고자 하는 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  각 서브태스크에 대한 종료 신호 플래그(False 또는 True)의 딕셔너리.

#### serialize()

이 환경을 다시 인스턴스화하는 데 필요한 모든 정보를 딕셔너리에 저장합니다. 이는 hdf5 데이터셋에 저장된 환경 메타데이터인 @env_meta와 동일하며, utils/env_utils.py에서 사용됩니다.

#### get_navigation_state(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

선택적 메서드입니다. 네비게이션 컨트롤러 로코맨풀레이션 데이터 생성 시에만 필요합니다.

로봇의 네비게이션 상태를 가져옵니다. 네비게이션 컨트롤러 사용이 활성화된 경우 필수입니다. 네비게이션 상태에는 네비게이션 컨트롤러에 의해 로봇이 제어되고 있는지 여부를 나타내는 불리언 플래그 "is_navigating"과 네비게이션 목표가 도달되었는지 여부를 나타내는 불리언 플래그 "navigation_goal_reached"가 포함됩니다.

* **매개변수:**
  **env_ids** – 네비게이션 상태를 얻고자 하는 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  네비게이션 상태 플래그(False 또는 True)의 딕셔너리.

#### \_\_init_\_(cfg: [ManagerBasedRLEnvCfg](#isaaclab.envs.ManagerBasedRLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성.
  * **render_mode** – 환경의 렌더 모드. 기본값은 None이며, 이는 `"human"`과 유사합니다.

#### close()

환경을 정리합니다.

#### *property* device

환경이 실행 중인 장치.

#### export_IO_descriptors(output_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

환경의 IO 디스크립터를 내보냅니다.

* **매개변수:**
  **output_dir** – IO 디스크립터를 내보낼 디렉터리.

#### *property* get_IO_descriptors

환경의 IO 디스크립터를 가져옵니다.

* **반환값:**
  그룹 이름을 키로 하고 IO 디스크립터를 값으로 하는 딕셔너리.

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 속성 이름을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 속성 이름이 존재하는지 확인합니다.

#### is_vector_env *: ClassVar[[bool](https://docs.python.org/3/library/functions.html#bool)]* *= True*

환경이 벡터화된 환경인지 여부.

#### load_managers()

환경의 관리자를 로드합니다.

이 함수는 환경의 다양한 관리자(동작, 관찰, 이벤트 등)를 생성하는 역할을 합니다. 관리자는 물리 핸들에 접근해야 하기 때문에 시뮬레이터가 리셋된 후에야(즉, 첫 번째로 실행된 후에야) 생성할 수 있습니다.

#### NOTE
독립형 애플리케이션의 경우(파이썬에서 시뮬레이터를 실행할 때), 클래스가 초기화될 때 이 함수가 자동으로 호출됩니다.

그러나 확장 모드의 경우, 사용자는 시뮬레이터가 리셋된 후 이 함수를 수동으로 호출해야 합니다.这是因为 시뮬레이터는 사용자가 `SimulationContext.reset_async()`를 호출할 때만 리셋되기 때문이며, 생성자에서는 비동기 함수를 호출할 수 없기 때문입니다.

#### *property* max_episode_length *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 스텝 단위의 최대 에피소드 길이.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

초 단위의 최대 에피소드 길이.

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

환경의 내부 `_np_random`을 반환합니다. 설정되지 않은 경우 랜덤 시드로 초기화됩니다.

* **반환값:**
  np.random.Generator의 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 내부 `_np_random_seed`를 반환합니다. 설정되지 않은 경우, 처음에 랜덤한 정수로 시드를 초기화합니다.

[`np_random_seed`](#isaaclab.envs.ManagerBasedRLMimicEnv.np_random_seed)를 [`reset()`](#isaaclab.envs.ManagerBasedRLMimicEnv.reset) 또는 `set_np_random_through_seed()`를 통해가 아니라 직접 설정한 경우,
시드 값은 -1이 됩니다.

* **Returns:**
  현재 np_random의 시드 또는 rng의 시드가 알려지지 않은 경우 -1
* **Return type:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

실행 중인 환경 인스턴스의 수입니다.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 시간 단계(초 단위)입니다.

시뮬레이션이 발생하는 가장 낮은 시간 분할입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 단계를 밟지 않고 렌더링을 실행합니다.

관례에 따라 mode가 다음과 같을 때:
- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않습니다. 보통 인간 소비용입니다.
- **rgb_array**: 형태가 (x, y, 3)인 numpy.ndarray를 반환하여 x-by-y 픽셀 이미지의 RGB 값을 나타냅니다. 이는 비디오로 변환하기에 적합합니다.

* **Parameters:**
  **recompute** – 시뮬레이터가 이미 장면을 렌더링했더라도 강제로 렌더링할지 여부입니다. 기본값은 False입니다.
* **Returns:**
  mode가 “rgb_array”인 경우 numpy 배열 형태의 렌더링된 이미지를 반환하고, 그렇지 않으면 None을 반환합니다.
* **Raises:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – mode가 “rgb_data”로 설정되었지만 시뮬레이션 렌더링 모드가 이를 지원하지 않는 경우입니다.
        이 경우, 시뮬레이션 렌더링 모드를 `RenderMode.PARTIAL_RENDERING` 또는 `RenderMode.FULL_RENDERING`으로 설정해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정된 경우입니다.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

지정된 환경을 재설정하고 관측값을 반환합니다.

이 함수는 지정된 환경을 재설정하기 위해 `_reset_idx()` 함수를 호출합니다.
그러나 초기화 중에 발생한 프로시저 지형 생성과 같은 특정 작업은 반복되지 않습니다.

* **Parameters:**
  * **seed** – 무작위화에 사용할 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **env_ids** – 재설정할 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 재설정됩니다.
  * **options** – 

    환경을 재설정하는 방법을 지정하는 추가 정보입니다. 기본값은 None입니다.

    #### NOTE
    이 인수는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **Returns:**
  관측값과 추가 정보를 포함하는 튜플입니다.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

제공된 상태로 지정된 환경을 재설정합니다.

이 함수는 환경을 제공된 상태로 재설정합니다. 상태는 scene 엔티티의 상태를 포함하는 사전입니다.
형식에 대해서는 `InteractiveScene.get_state()`를 참조하십시오.

이 함수는 [`reset()`](#isaaclab.envs.ManagerBasedRLMimicEnv.reset) 함수와 다릅니다. 이 함수는 랜덤화 이벤트를 사용하여 환경을 재설정하는 대신, 특정 상태로 환경을 재설정하기 때문입니다.

* **Parameters:**
  * **state** – 지정된 환경을 재설정할 상태입니다. 형식은 `InteractiveScene.get_state()`를 참조하십시오.
  * **env_ids** – 재설정할 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 재설정됩니다.
  * **seed** – 무작위화에 사용할 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **is_relative** – True로 설정된 경우, 상태는 환경 기원을 기준으로 상대적인 것으로 간주됩니다. 기본값은 False입니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **Parameters:**
  **seed** – 난수 생성기의 시드입니다. 기본값은 -1입니다.
* **Returns:**
  난수 생성기에 사용된 시드입니다.

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 value로 이름 속성을 설정합니다. 자세한 내용은 Wrapper.set_wrapper_attr을 참조하십시오.

#### setup_manager_visualizers()

관리자 항목에 대한 라이브 시각화 도구를 생성합니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동작을 한 타임스텝 실행하고 종료된 환경을 재설정합니다.

[`ManagerBasedEnv.step`](#isaaclab.envs.ManagerBasedEnv.step) 클래스와 달리, 이 함수는 다음 작업을 수행합니다:
1. 동작 처리.
2. 물리 스텝 실행.
3. GUI가 활성화된 경우 렌더링 수행.
4. 환경 카운터 업데이트 및 보상과 종료 조건 계산.
5. 종료된 환경 재설정.
6. 관측값 계산.
7. 관측값, 보상, 재설정(종료 및 잘림) 및 추가 정보를 반환합니다.

* **Parameters:**
  **action** – 환경에 적용할 동작입니다. 형태는 (num_envs, action_dim)입니다.
* **Returns:**
  관측값, 보상, 재설정(종료 및 잘림), 추가 정보를 포함하는 튜플입니다.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스텝 시간 단계(초 단위)입니다.

환경이 앞으로 나아가는 시간 단계입니다.

#### *property* unwrapped *: [Env](https://gymnasium.farama.org/api/env/#gymnasium.Env)[ObsType, ActType]*

래핑되지 않은 기본 환경을 반환합니다.

* **Returns:**
  기본 래핑되지 않은 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인스턴스
* **Return type:**
  Env

#### cfg *: [ManagerBasedRLEnvCfg](#isaaclab.envs.ManagerBasedRLEnvCfg)*

환경의 구성입니다.

### *class* isaaclab.envs.MimicEnvCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

Mimic 환경 통합을 위한 구성 클래스입니다.

이 클래스는 Isaac Lab Mimic 데이터 생성 파이프라인의 다양한 구성 측면을 통합합니다.

### *class* isaaclab.envs.SubTaskConfig

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

Mimic 환경에서 사용되는 서브태스크를 지정하기 위한 구성 설정입니다.

**속성:**

| [`object_ref`](#isaaclab.envs.SubTaskConfig.object_ref)                                             | 이 서브태스크와 관련된 객체에 대한 참조입니다.                         |
|-----------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| [`subtask_term_signal`](#isaaclab.envs.SubTaskConfig.subtask_term_signal)                           | 서브태스크 종료 신호 이름.                                          |
| [`selection_strategy`](#isaaclab.envs.SubTaskConfig.selection_strategy)                             | 서브태스크 세그먼트를 선택하는 전략.                                 |
| [`selection_strategy_kwargs`](#isaaclab.envs.SubTaskConfig.selection_strategy_kwargs)               | 선택된 전략에 대한 추가 인수.                            |
| [`first_subtask_start_offset_range`](#isaaclab.envs.SubTaskConfig.first_subtask_start_offset_range) | 첫 번째 서브태스크의 시작 오프셋 범위.                              |
| [`subtask_start_offset_range`](#isaaclab.envs.SubTaskConfig.subtask_start_offset_range)             | 서브태스크의 시작 오프셋 범위 (use_skillgen이 True인 경우에만 사용) |
| [`subtask_term_offset_range`](#isaaclab.envs.SubTaskConfig.subtask_term_offset_range)               | 서브태스크 종료 오프셋 범위.                                 |
| [`action_noise`](#isaaclab.envs.SubTaskConfig.action_noise)                                         | 적용되는 액션 노이즈의 진폭.                                        |
| [`num_interpolation_steps`](#isaaclab.envs.SubTaskConfig.num_interpolation_steps)                   | 웨이포인트 간 보간에 사용되는 단계 수.                      |
| [`num_fixed_steps`](#isaaclab.envs.SubTaskConfig.num_fixed_steps)                                   | 서브태스크에 사용되는 고정 단계 수.                                    |
| [`apply_noise_during_interpolation`](#isaaclab.envs.SubTaskConfig.apply_noise_during_interpolation) | 보간 중 노이즈 적용 여부.                              |
| [`description`](#isaaclab.envs.SubTaskConfig.description)                                           | 서브태스크 설명                                                |
| [`next_subtask_description`](#isaaclab.envs.SubTaskConfig.next_subtask_description)                 | 다음 서브태스크에 대한 지침                                         |

#### object_ref *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

이 서브태스크와 관련된 객체에 대한 참조.

객체가 관련되지 않은 경우 None으로 설정 (이는 드물게 발생함).

#### subtask_term_signal *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

서브태스크 종료 신호 이름.

#### selection_strategy *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

서브태스크 세그먼트를 선택하는 전략.

다음 중 하나일 수 있음:
: * ‘random’
  * ‘nearest_neighbor_object’
  * ‘nearest_neighbor_robot_distance’

#### NOTE
‘nearest_neighbor_object’ 및 ‘nearest_neighbor_robot_distance’의 경우, 서브태스크에 ‘object_ref’를 None이 아닌 값으로 설정해야 합니다. object_ref가 설정된 경우 이러한 전략은 기본 ‘random’ 전략보다 일반적으로 더 높은 성공률을 제공합니다.

#### selection_strategy_kwargs *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)*

선택된 전략에 대한 추가 인수. source/isaaclab_mimic/isaaclab_mimic/datagen/selection_strategy.py에서 각 전략의 세부 정보를 참조하세요.
인수가 select_source_demo 메서드에 전달됩니다.

#### first_subtask_start_offset_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)*

첫 번째 서브태스크의 시작 오프셋 범위.

#### subtask_start_offset_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)*

서브태스크의 시작 오프셋 범위 (use_skillgen이 True인 경우에만 사용)

참고: skillgen이 활성화된 경우 이 값은 first_subtask_start_offset_range을 재정의합니다.

#### subtask_term_offset_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)*

서브태스크 종료 오프셋 범위.

#### action_noise *: [float](https://docs.python.org/3/library/functions.html#float)*

적용되는 액션 노이즈의 진폭.

#### num_interpolation_steps *: [int](https://docs.python.org/3/library/functions.html#int)*

웨이포인트 간 보간에 사용되는 단계 수.

#### num_fixed_steps *: [int](https://docs.python.org/3/library/functions.html#int)*

서브태스크에 사용되는 고정 단계 수.

#### apply_noise_during_interpolation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

보간 중 노이즈 적용 여부.

#### description *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

서브태스크 설명

#### next_subtask_description *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

다음 서브태스크에 대한 지침

### *class* isaaclab.envs.SubTaskConstraintConfig

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

멀티-EEF Mimic 환경에서 서브태스크 제약을 지정하기 위한 구성 설정.

**속성:**

| [`eef_subtask_constraint_tuple`](#isaaclab.envs.SubTaskConstraintConfig.eef_subtask_constraint_tuple)               | 순서대로 관련된 서브태스크 튜플 목록.                       |
|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| [`constraint_type`](#isaaclab.envs.SubTaskConstraintConfig.constraint_type)                                         | 서브태스크 간 적용할 제약 유형.                      |
| [`sequential_min_time_diff`](#isaaclab.envs.SubTaskConstraintConfig.sequential_min_time_diff)                       | 두 개의 순차적 서브태스크 완료 사이의 최소 시간 차이. |
| [`coordination_scheme`](#isaaclab.envs.SubTaskConstraintConfig.coordination_scheme)                                 | 서브태스크 조정을 위한 스킴.                           |
| [`coordination_scheme_pos_noise_scale`](#isaaclab.envs.SubTaskConstraintConfig.coordination_scheme_pos_noise_scale) | 조정 중에 적용할 위치 노이즈 스케일.              |
| [`coordination_scheme_rot_noise_scale`](#isaaclab.envs.SubTaskConstraintConfig.coordination_scheme_rot_noise_scale) | 조정 중에 적용할 회전 노이즈 스케일.              |
| [`coordination_synchronize_start`](#isaaclab.envs.SubTaskConstraintConfig.coordination_synchronize_start)           | 서브태스크가 동시에 시작되어야 하는지 여부.                    |

**메서드:**

| [`generate_runtime_subtask_constraints`](#isaaclab.envs.SubTaskConstraintConfig.generate_runtime_subtask_constraints)()   | 작업 제약 구성에 기반하여 확장된 작업 제약 딕셔너리를 채웁니다.   |
|---------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|

#### eef_subtask_constraint_tuple *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [int](https://docs.python.org/3/library/functions.html#int)]]*

순서대로 관련된 서브태스크 튜플 목록.

튜플의 첫 번째 요소는 EEF 이름을 가리킵니다.
튜플의 두 번째 요소는 EEF의 서브태스크 인덱스를 가리킵니다.

#### constraint_type *: SubTaskConstraintType*

서브태스크 간 적용할 제약 유형.

#### sequential_min_time_diff *: [int](https://docs.python.org/3/library/functions.html#int)*

두 개의 순차적 서브태스크가 끝나는 사이의 최소 시간 차이.

두 번째 서브태스크는 첫 번째(전제 조건인) 서브태스크가 끝날 때까지 자신의 서브태스크 경로에서 sequential_min_time_diff 단계가 남을 때까지 실행되고, 그 후에 나머지를 계속 실행합니다.
-1로 설정된 경우, 두 번째 서브태스크는 첫 번째 서브태스크가 끝난 후에 시작됩니다.

#### coordination_scheme *: SubTaskConstraintCoordinationScheme*

서브태스크 조정을 위한 스킴.

#### coordination_scheme_pos_noise_scale *: [float](https://docs.python.org/3/library/functions.html#float)*

조정 중에 적용할 위치 노이즈 스케일.

#### coordination_scheme_rot_noise_scale *: [float](https://docs.python.org/3/library/functions.html#float)*

조정 중에 적용할 회전 노이즈 스케일.

#### coordination_synchronize_start *: [bool](https://docs.python.org/3/library/functions.html#bool)*

서브태스크가 동시에 시작되어야 하는지 여부.

#### generate_runtime_subtask_constraints()

작업 제약 구성에 기반하여 확장된 작업 제약 딕셔너리를 채웁니다.
작업 제약 구성은 사용자가 설정한 구성을 포함하며,
작업_constraints_dict에는 이 클래스에서 제약 로직을 구현하는 데 사용되는 플래그가 포함됩니다.

작업 제약 구성이 다음 유형을 포함할 수 있습니다:
- “sequential”
- “coordination”

“sequential” 제약의 경우:
: - task_constraint_configs의 데이터가 task_constraints_dict에 “sequential former” 작업 제약으로 추가됩니다.
  - 반대 유형인 “sequential latter” 제약도 task_constraints_dict에 추가됩니다.
  - 또한 task_constraints_dict에 (“fulfilled”, Bool) 키-값 쌍이 추가됩니다.
  - 이는 선행 작업(즉, “sequential former” 작업)이 완료되었는지 확인하는 데 사용됩니다.
  - “sequential latter”의 “fulfilled” 플래그가 “sequential former”에 의해 설정될 때까지,
    : “sequential latter” 서브태스크는 일시 중지 상태를 유지합니다.

“coordination” 제약의 경우:
: - task_constraint_configs의 데이터가 task_constraints_dict에 추가됩니다.
  - 반대 유형인 “coordination” 제약도 task_constraints_dict에 추가됩니다.
  - 동기화 단계 수는 subtask_len과 concurrent_subtask_len 중 최소값으로 설정됩니다.
  -これにより、両方の並行タスクが同じタイムステップで終了することを保証します。
  - “selected_src_demo_ind” 및 “transform” 필드는 두 서브태스크에서 사용되는 변환이 동일하도록 보장하는 데 사용됩니다.

## Common

### *class* isaaclab.envs.ViewerCfg

시나리오 뷰포트 카메라의 구성.

**속성:**

| [`eye`](#isaaclab.envs.ViewerCfg.eye)                     | 초기 카메라 위치 (미터 단위).                                                                                            |
|-----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| [`lookat`](#isaaclab.envs.ViewerCfg.lookat)               | 초기 카메라 타겟 위치 (미터 단위).                                                                                     |
| [`cam_prim_path`](#isaaclab.envs.ViewerCfg.cam_prim_path) | 이미지를 기록할 카메라 프림 경로.                                                                                |
| [`resolution`](#isaaclab.envs.ViewerCfg.resolution)       | [`cam_prim_path`](#isaaclab.envs.ViewerCfg.cam_prim_path)로 지정된 카메라의 해상도 (너비, 높이).    |
| [`origin_type`](#isaaclab.envs.ViewerCfg.origin_type)     | 카메라 위치 (eye)와 타겟 (lookat)이 정의되는 프레임.                                           |
| [`env_index`](#isaaclab.envs.ViewerCfg.env_index)         | 프레임 원점의 환경 인덱스.                                                                                    |
| [`asset_name`](#isaaclab.envs.ViewerCfg.asset_name)       | 프레임 원점을 위한 인터랙티브 씬의 에셋 이름.                                                              |
| [`body_name`](#isaaclab.envs.ViewerCfg.body_name)         | 프레임 원점을 위한 인터랙티브 씬의 [`asset_name`](#isaaclab.envs.ViewerCfg.asset_name) 내의 바디 이름. |

#### eye *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

초기 카메라 위치 (미터 단위). 기본값은 (7.5, 7.5, 7.5)입니다.

#### lookat *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

초기 카메라 타겟 위치 (미터 단위). 기본값은 (0.0, 0.0, 0.0)입니다.

#### cam_prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

이미지를 기록할 카메라 프림 경로. 기본값은 “/OmniverseKit_Persp”이며, 이는 뷰포트에서 기본 카메라입니다.

#### resolution *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int)]*

[`cam_prim_path`](#isaaclab.envs.ViewerCfg.cam_prim_path)로 지정된 카메라의 해상도 (너비, 높이). 기본값은 (1280, 720)입니다.

#### origin_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['world', 'env', 'asset_root', 'asset_body']*

카메라 위치 (eye)와 타겟 (lookat)이 정의되는 프레임. 기본값은 “world”입니다.

사용 가능한 옵션은 다음과 같습니다:

* `"world"`: 세계의 원점.
* `"env"`: [`env_index`](#isaaclab.envs.ViewerCfg.env_index)로 정의된 환경의 원점.
* `"asset_root"`: 환경 [`env_index`](#isaaclab.envs.ViewerCfg.env_index)에서 [`asset_name`](#isaaclab.envs.ViewerCfg.asset_name)로 정의된 에셋의 중심.
* `"asset_body"`: 환경 [`env_index`](#isaaclab.envs.ViewerCfg.env_index)에서 [`asset_name`](#isaaclab.envs.ViewerCfg.asset_name)로 정의된 에셋 내의 [`body_name`](#isaaclab.envs.ViewerCfg.body_name)로 정의된 바디의 중심.

#### env_index *: [int](https://docs.python.org/3/library/functions.html#int)*

프레임 원점을 위한 환경 인덱스. 기본값은 0입니다.

이 양은 `origin`이 “env” 또는 “asset_root”로 설정된 경우에만 유효합니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

프레임 원점을 위한 인터랙티브 씬의 에셋 이름. 기본값은 None입니다.

이 양은 `origin`이 “asset_root”로 설정된 경우에만 유효합니다.

#### body_name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

프레임 원점을 위한 인터랙티브 씬의 [`asset_name`](#isaaclab.envs.ViewerCfg.asset_name) 내의 바디 이름. 기본값은 None입니다.

이 양은 `origin`이 “asset_body”로 설정된 경우에만 유효합니다.
