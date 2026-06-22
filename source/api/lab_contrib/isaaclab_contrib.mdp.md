# isaaclab_contrib.mdp

커뮤니티가 기여한 MDP(Markov 결정 과정) 구성요소 하위 패키지입니다.

## Actions

멀티로터 제어를 위한 동작 용어입니다.

이 모듈은 추력 명령을 통해 멀티로터 차량을 제어하기 위해 특별히 설계된 동작 용어를 제공합니다. 이러한 동작 용어는 Isaac Lab의 MDP 프레임워크 및 [`Multirotor`](isaaclab_contrib.assets.md#isaaclab_contrib.assets.Multirotor) 자산과 통합됩니다.

### 클래스

| `ThrustActionCfg`   | 추력 동작 용어에 대한 구성입니다.                                 |
|---------------------|-------------------------------------------------------------------|
| `ThrustAction`      | 처리된 동작을 추력 명령으로 적용하는 추력 동작 용어입니다. |

## 추력 동작 구성

### *class* isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg

Bases: [`ActionTermCfg`](../lab/isaaclab.managers.md#isaaclab.managers.ActionTermCfg)

추력 동작 용어에 대한 구성입니다.

이 구성 클래스는 멀티로터 제어를 위한 스러스터 명령으로 정책 동작이 어떻게 변환되는지를 지정합니다. 스케일링, 오프셋팅, 클리핑을 포함하는 동작 처리 파이프라인을 광범위하게 커스터마이징할 수 있습니다.

이 동작 용어는 [`Multirotor`](isaaclab_contrib.assets.md#isaaclab_contrib.assets.Multirotor) 자산과 작동하도록 설계되었으며, 그들의 스러스터 구성을 사용하여 어떤 스러스터를 제어할지 결정합니다.

주요 구성 옵션:
: - **scale**: 명령의 크기를 조정하기 위해 원시 동작에 곱함  
  - **offset**: 동작에 기준값(예: 호버 추력)을 추가함  
  - **clip**: 안전한 동작 범위로 동작을 제한함  
  - **use_default_offset**: 호버 추력을 오프셋으로 자동 사용  

예시 구성:
: **호버 주변 정규화된 추력 제어**:
  <br/>
  ```python
  thrust_action = ThrustActionCfg(
      asset_name="robot",
      scale=2.0,  # [-1,1]의 동작이 [-2,2] N이 됨
      use_default_offset=True,  # 호버 추력 추가(예: 5N)
      clip={".*": (0.0, 10.0)},  # 최종 추력을 [0, 10] N으로 제한
  )
  ```
  <br/>
  **스러스터별 스케일링을 갖는 직접 추력 제어**:
  <br/>
  ```python
  thrust_action = ThrustActionCfg(
      asset_name="robot",
      scale={
          "rotor_[0-1]": 8.0,  # 앞쪽 스러스터: 더 강력함
          "rotor_[2-3]": 7.0,  # 뒤쪽 스러스터: 덜 강력함
      },
      offset=0.0,
      use_default_offset=False,
  )
  ```
  <br/>
  **차동 추력 제어**:
  <br/>
  ```python
  thrust_action = ThrustActionCfg(
      asset_name="robot",
      scale=3.0,
      use_default_offset=True,  # 호버 추력 주변으로 중앙 배치
      clip={".*": (-2.0, 8.0)},  # +/-2N 편차 허용
  )
  ```

#### SEE ALSO
- `ThrustAction`: 이 동작 용어의 구현
- [`ActionTermCfg`](../lab/isaaclab.managers.md#isaaclab.managers.ActionTermCfg): 기본 동작 용어 구성

**속성:**

| [`asset_name`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.asset_name)                 | 동작이 매핑될 자산의 이름 또는 정규식 표현입니다.    |
|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------|
| [`scale`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.scale)                           | 동작의 스케일 팩터입니다.                          |
| [`offset`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.offset)                         | 동작의 오프셋 팩터입니다.                          |
| [`debug_vis`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.debug_vis)                   | 디버그 정보를 시각화할지 여부입니다.               |
| [`clip`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.clip)                             | 처리된 동작에 대한 클리핑 범위입니다.              |
| [`preserve_order`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.preserve_order)         | 동작 출력에서 자산 이름의 순서를 보존할지 여부입니다. |
| [`use_default_offset`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.use_default_offset) | 멀티로터 자산에 구성된 기본 추력을 오프셋으로 사용할지 여부입니다. |

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

동작이 매핑될 자산의 이름 또는 정규식 표현입니다.

이것은 장면 구성에서 멀티로터 자산에 주어진 이름과 일치해야 합니다. 예를 들어 로봇이 `robot = MultirotorCfg(...)`로 정의되었다면, `asset_name`은 `"robot"`이어야 합니다.

#### scale *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

동작에 대한 스케일 팩터입니다. 기본값은 `1.0`이며, 이는 스케일링 없음을 의미합니다.

이는 원시 동작 값에 곱해져 명령의 크기를 조정합니다. 다음과 같이 지정할 수 있습니다:

- float: 모든 스러스터에 대한 균일한 스케일링(예: `2.0`)
- dict: 정규식 패턴을 사용한 스러스터별 스케일링(예: `{"rotor_.*": 2.5}`)

[-1, 1] 범위의 정규화된 동작에 대해, 스케일은 오프셋 값으로부터의 최대 편차를 결정합니다.

### 예시

```python
# 균일한 스케일링
scale = 5.0  # ±1의 동작이 ±5N이 됨

# 스러스터별 스케일링
scale = {
    "rotor_[0-1]": 8.0,   # 앞쪽 스러스터
    "rotor_[2-3]": 6.0,   # 뒤쪽 스러스터
}
```

#### offset *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

동작에 대한 오프셋 팩터입니다. 기본값은 `0.0`이며, 이는 오프셋 없음을 의미합니다.

이 값은 스케일링된 동작에 더해져 기준 추력을 설정합니다. 다음과 같이 지정할 수 있습니다:

- float: 모든 스러스터에 대한 균일한 오프셋(예: `5.0`으로 5N 호버 추력)
- dict: 정규식 패턴을 사용한 스러스터별 오프셋

[`use_default_offset`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.use_default_offset)이 `True`인 경우, 이 값은 멀티로터 구성의 기본 스러스터 RPS로 덮어쓰여집니다.

### 예시

```python
# 균일한 오프셋 (5N 기준 추력)
offset = 5.0

# 스러스터별 오프셋
offset = {
    "rotor_0": 5.2,
    "rotor_1": 4.8,
}
```

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]] | [None](https://docs.python.org/3/library/constants.html#None)*

처리된 동작에 대한 클리핑 범위입니다. 기본값은 `None`이며, 이는 클리핑 없음을 의미합니다.

이는 스케일링과 오프셋이 적용된 후 최종 추력 명령을 안전한 동작 범위로 제한합니다. 정규식 패턴을 (min, max) 튜플에 매핑하는 딕셔너리로 지정해야 합니다.

### 예시

```python
# 모든 스러스터를 [0, 10] N으로 클리핑
clip = {".*": (0.0, 10.0)}

# 스러스터별로 다른 제한 적용
clip = {
    "rotor_[0-1]": (0.0, 12.0),  # 앞쪽 스러스터
    "rotor_[2-3]": (0.0, 8.0),   # 뒤쪽 스러스터
}
```

#### preserve_order *: [bool](https://docs.python.org/3/library/functions.html#bool)*

동작 출력에서 자산 이름의 순서를 보존할지 여부입니다. 기본값은 `False`입니다.

`True`인 경우, 스러스터 순서는 정규식 패턴 순서와 정확히 일치합니다.
`False`인 경우, 순서는 USD 장면 탐색 순서에 의해 결정됩니다.

#### use_default_offset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

멀티로터 자산에 구성된 기본 추력을 오프셋으로 사용할지 여부입니다. 기본값은 `True`입니다.

`True`인 경우, [`offset`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.offset) 값은 `MultirotorCfg.init_state.rps`의 기본 스러스터 RPS 값으로 덮어씁니다. 이는 호버 상태에서의 추력 편차로 추력을 제어하는 데 유용합니다.

`False`인 경우, 수동으로 지정한 [`offset`](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg.offset) 값이 사용됩니다.

## 추력 동작 구현

### *class* isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction

Bases: [`ActionTerm`](../lab/isaaclab.managers.md#isaaclab.managers.ActionTerm)

처리된 동작을 추력 명령으로 적용하는 추력 동작 용어입니다.

이 동작 용어는 동작 입력을 스러스터 명령에 매핑하여 멀티로터 차량을 제어하도록 특별히 설계되었습니다. 다음을 통해 동작의 유연한 전처리를 제공합니다:

- **스케일링**: 동작에 스케일 팩터를 곱해 명령 크기 조정
- **오프셋**: 동작에 오프셋을 더해 기준값(예: 호버 추력) 주변으로 중앙 배치
- **클리핑**: 동작을 유효한 범위로 제한해 안전하지 않은 명령 방지

이 동작 용어는 Isaac Lab의 [`ActionManager`](../lab/isaaclab.managers.md#isaaclab.managers.ActionManager) 프레임워크와 통합되며, [`Multirotor`](isaaclab_contrib.assets.md#isaaclab_contrib.assets.Multirotor) 자산과 함께 작동하도록 특별히 설계되었습니다.

주요 기능:
: - 스러스터별 또는 균일한 스케일링 및 오프셋 지원
  - 호버 추력을 기반으로 한 자동 오프셋 계산 옵션
  - 안전 및 제약 조건 강제를 위한 동작 클리핑
  - 유연한 제어 방식을 위한 정규식 기반 스러스터 선택

### 예시

```python
from isaaclab.envs import ManagerBasedRLEnvCfg
from isaaclab_contrib.mdp.actions import ThrustActionCfg


@configclass
class MyEnvCfg(ManagerBasedRLEnvCfg):
    # ... 다른 구성 ...

    @configclass
    class ActionsCfg:
        # 정규화된 동작을 갖는 직접 추력 제어
        thrust = ThrustActionCfg(
            asset_name="robot",
            scale=5.0,  # [-1, 1]을 [-5, 5] N으로 변환
            use_default_offset=True,  # 호버 추력을 오프셋으로 추가
            clip={".*": (-2.0, 8.0)},  # 안전한 추력 범위로 클리핑
        )
```

**속성:**

| [`cfg`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.cfg)                                                   | 동작 항목의 구성입니다.                           |
|--------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`device`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.device)                                             | 계산을 수행할 장치입니다.                        |
| [`export_IO_descriptor`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.export_IO_descriptor)                 | 동작 항목에 대한 IO 디스크립터를 내보낼지 여부입니다.        |
| [`has_debug_vis_implementation`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.has_debug_vis_implementation) | 동작 항목에 디버그 시각화가 구현되어 있는지 여부입니다.  |
| [`num_envs`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.num_envs)                                         | 환경 수입니다.                                         |
| [`action_dim`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.action_dim)                                     | 동작 항목의 차원입니다.                                   |
| [`raw_actions`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.raw_actions)                                   | 항목에 전달되는 입력/원시 동작입니다.                         |
| [`processed_actions`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.processed_actions)                       | 처리 후 항목에서 계산된 동작입니다. |
| [`IO_descriptor`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.IO_descriptor)                               | 동작 항목의 IO 디스크립터입니다.                           |

**메서드:**

| [`__init__`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.__init__)(cfg, env)              | 동작 항목을 초기화합니다.                                |
|---------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| [`serialize`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.serialize)()                    | 일반 직렬화 호출입니다.                                |
| [`set_debug_vis`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.set_debug_vis)(debug_vis)   | 동작 항목 데이터 시각화 여부를 설정합니다.            |
| [`reset`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.reset)([env_ids])                   | 동작 항목을 재설정합니다.                                     |
| [`process_actions`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.process_actions)(actions) | 스케일링, 오프셋, 클리핑을 적용하여 동작을 처리합니다. |
| [`apply_actions`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.apply_actions)()            | 처리된 동작을 추력 명령으로 적용합니다.            |

#### cfg *: [thrust_actions_cfg.ThrustActionCfg](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg)*

동작 항목의 구성입니다.

#### \_\_init_\_(cfg: [thrust_actions_cfg.ThrustActionCfg](#isaaclab_contrib.mdp.actions.thrust_actions_cfg.ThrustActionCfg), env: [ManagerBasedEnv](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv)) → [None](https://docs.python.org/3/library/constants.html#None)

동작 항목을 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체입니다.
  * **env** – 환경 인스턴스입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 수행할 장치입니다.

#### *property* export_IO_descriptor *: [bool](https://docs.python.org/3/library/functions.html#bool)*

동작 항목에 대한 IO 디스크립터를 내보낼지 여부입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

동작 항목에 디버그 시각화가 구현되어 있는지 여부입니다.

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 수입니다.

#### serialize() → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

일반 직렬화 호출입니다. 구성 딕셔너리를 포함합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

동작 항목 데이터 시각화 여부를 설정합니다.
:param debug_vis: 동작 항목 데이터를 시각화할지 여부입니다.

* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부입니다. 동작 항목이 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

#### *property* action_dim *: [int](https://docs.python.org/3/library/functions.html#int)*

동작 항목의 차원입니다.

#### *property* raw_actions *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

항목에 전달되는 입력/원시 동작입니다.

#### *property* processed_actions *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

처리 후 항목에서 계산된 동작입니다.

#### *property* IO_descriptor *: GenericActionIODescriptor*

동작 항목의 IO 디스크립터입니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [None](https://docs.python.org/3/library/constants.html#None)

동작 항목을 재설정합니다.

이 메서드는 지정된 환경에 대해 원시 동작을 0으로 재설정합니다.
처리된 동작은 다음 [`process_actions()`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.process_actions) 호출 시 재계산됩니다.

* **매개변수:**
  **env_ids** – 재설정할 환경 인덱스입니다. 기본값은 None(모든 환경)입니다.

#### process_actions(actions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor))

스케일링, 오프셋, 클리핑을 적용하여 동작을 처리합니다.

이 메서드는 다음 변환을 통해 원시 정책 동작을 추력 명령으로 변환합니다:

$$
\text{processed} = \text{raw} \times \text{scale} + \text{offset}

$$

클리핑이 구성된 경우, 처리된 동작은 다음과 같이 클램프됩니다:

$$
\text{processed} = \text{clamp}(\text{processed}, \text{min}, \text{max})

$$

* **매개변수:**
  **actions** – 정책에서 전달된 원시 동작 텐서입니다. 형태는 `(num_envs, action_dim)`입니다.
  일반적으로 정규화된 정책의 경우 [-1, 1] 범위 내에 있습니다.

#### NOTE
처리된 동작은 내부적으로 저장되었으며, 다음 [`apply_actions()`](#isaaclab_contrib.mdp.actions.thrust_actions.ThrustAction.apply_actions) 호출 시 적용됩니다.

#### apply_actions()

처리된 동작을 추력 명령으로 적용합니다.

이 메서드는 멀티로터 자산에 처리된 동작을 추력 대상으로 설정합니다. 추력 대상은 그 다음 스러스터 액추에이터 모델에서 시뮬레이션 단계 동안 실제 추력 힘을 계산하는 데 사용됩니다.

이 메서드는 적절한 스러스터 ID와 함께 멀티로터 자산에 [`set_thrust_target()`](isaaclab_contrib.assets.md#isaaclab_contrib.assets.Multirotor.set_thrust_target)를 호출합니다.
