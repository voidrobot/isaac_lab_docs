<a id="api-isaaclab-rl"></a>

# isaaclab_rl

다양한 학습 프레임워크를 위한 환경 래퍼 패키지입니다.

래퍼를 사용하면 환경을 수정하지 않고도 환자의 동작을 수정할 수 있습니다.
이는 관측 공간, 행동 공간 또는 보상 함수를 수정하는 데 유용합니다.
또한, 다양한 학습 프레임워크에서 사용하는 환경 클래스 정의로 주어진 환경을 캐스팅하는 데 사용할 수 있습니다.
이 작업에는 비대칭 액터-크리틱 관측 처리, NumPy와 PyTorch 같은 백엔드 간 데이터 캐스팅,
또는 학습 프레임워크에서 기대하는 데이터 구조로 반환된 데이터 구성 등이 포함될 수 있습니다.

모든 래퍼는 [`gymnasium.Wrapper`](https://gymnasium.farama.org/api/wrappers/#gymnasium.Wrapper) 클래스와 유사하게 동작합니다.
래퍼를 사용하는 것은 래퍼 생성자에 초기화된 환경 인스턴스를 전달하는 것만큼 간단합니다.
하지만 학습 프레임워크마다 입력 및 출력 데이터 구조가 다르므로
래퍼 클래스는 서로 호환되지 않습니다.
따라서 해당 학습 프레임워크와 함께 사용해야 합니다.

## RL-Games 래퍼

rl-games 라이브러리를 위한 환경 구성 래퍼 및 유틸리티입니다.

## RSL-RL 래퍼

RSL-RL 라이브러리를 위한 환경 구성 래퍼 및 유틸리티입니다.

다음 예시는 RSL-RL을 위한 환경 래핑 방법을 보여줍니다:

```python
from isaaclab_rl.rsl_rl import RslRlVecEnvWrapper

env = RslRlVecEnvWrapper(env)
```

**함수:**

| [`export_policy_as_jit`](#isaaclab_rl.rsl_rl.export_policy_as_jit)(policy, normalizer, path)   | 정책을 Torch JIT 파일로 내보냅니다.                                      |
|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| [`export_policy_as_onnx`](#isaaclab_rl.rsl_rl.export_policy_as_onnx)(policy, path[, ...])      | 정책을 Torch ONNX 파일로 내보냅니다.                                     |
| [`configclass`](#isaaclab_rl.rsl_rl.configclass)(cls, \*\*kwargs)                              | 추가 검사 및 유틸리티를 위한 dataclass 기능 래퍼입니다. |

**클래스:**

| [`RslRlRndCfg`](#isaaclab_rl.rsl_rl.RslRlRndCfg)                                                                 | 랜덤 네트워크 증류(RND) 모듈 구성입니다.        |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------|
| [`RslRlSymmetryCfg`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg)                                                       | 훈련에서의 대칭 증강을 위한 구성입니다.      |
| [`RslRlBaseRunnerCfg`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg)                                                   | 러너의 기본 구성입니다.                      |
| [`RslRlDistillationAlgorithmCfg`](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg)                             | 증류 알고리즘을 위한 구성입니다.             |
| [`RslRlDistillationRunnerCfg`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg)                                   | 증류 알고리즘을 위한 러너 구성입니다.        |
| [`RslRlDistillationStudentTeacherCfg`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg)                   | 증류 학생-교사 네트워크를 위한 구성입니다.   |
| [`RslRlDistillationStudentTeacherRecurrentCfg`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg) | 증류 학생-교사 재귀 네트워크를 위한 구성입니다. |
| [`RslRlOnPolicyRunnerCfg`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg)                                           | 온-폴리시 알고리즘을 위한 러너 구성입니다.   |
| [`RslRlPpoActorCriticCfg`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg)                                           | PPO 액터-크리틱 네트워크를 위한 구성입니다.  |
| [`RslRlPpoActorCriticRecurrentCfg`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg)                         | 재귀 레이어를 가진 PPO 액터-크리틱 네트워크를 위한 구성입니다. |
| [`RslRlPpoAlgorithmCfg`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg)                                               | PPO 알고리즘을 위한 구성입니다.              |
| [`RslRlVecEnvWrapper`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper)                                                   | RSL-RL 라이브러리를 위한 Isaac Lab 환경 래퍼  |

### isaaclab_rl.rsl_rl.export_policy_as_jit(policy: [object](https://docs.python.org/3/library/functions.html#object), normalizer: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None), path: [str](https://docs.python.org/3/library/stdtypes.html#str), filename='policy.pt')

정책을 Torch JIT 파일로 내보냅니다.

* **매개변수:**
  * **policy** – 정책 토치 모듈입니다.
  * **normalizer** – 경험적 정규화 모듈입니다. None인 경우 Identity가 사용됩니다.
  * **path** – 저장할 디렉터리 경로입니다.
  * **filename** – 내보낼 JIT 파일 이름입니다. 기본값은 “policy.pt”입니다.

### isaaclab_rl.rsl_rl.export_policy_as_onnx(policy: [object](https://docs.python.org/3/library/functions.html#object), path: [str](https://docs.python.org/3/library/stdtypes.html#str), normalizer: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None) = None, filename='policy.onnx', verbose=False)

정책을 Torch ONNX 파일로 내보냅니다.

* **매개변수:**
  * **policy** – 정책 토치 모듈입니다.
  * **normalizer** – 경험적 정규화 모듈입니다. None인 경우 Identity가 사용됩니다.
  * **path** – 저장할 디렉터리 경로입니다.
  * **filename** – 내보낼 ONNX 파일 이름입니다. 기본값은 “policy.onnx”입니다.
  * **verbose** – 모델 요약을 출력할지 여부입니다. 기본값은 False입니다.

### *class* isaaclab_rl.rsl_rl.RslRlRndCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

랜덤 네트워크 증류(RND) 모듈 구성입니다.

자세한 내용은 [[SKB+23](../../refs/bibliography.md#id20)] 작업을 참조하십시오.

**클래스:**

| [`WeightScheduleCfg`](#isaaclab_rl.rsl_rl.RslRlRndCfg.WeightScheduleCfg)             | 가중치 스케줄 구성입니다.        |
|--------------------------------------------------------------------------------------|----------------------------------|
| [`LinearWeightScheduleCfg`](#isaaclab_rl.rsl_rl.RslRlRndCfg.LinearWeightScheduleCfg) | 선형 가중치 스케줄 구성입니다.   |
| [`StepWeightScheduleCfg`](#isaaclab_rl.rsl_rl.RslRlRndCfg.StepWeightScheduleCfg)     | 단계별 가중치 스케줄 구성입니다. |

**속성:**

| [`weight`](#isaaclab_rl.rsl_rl.RslRlRndCfg.weight)                               | RND 보상의 가중치(내재적 보상으로도 알려져 있음).   |
|----------------------------------------------------------------------------------|-----------------------------------------------------|
| [`weight_schedule`](#isaaclab_rl.rsl_rl.RslRlRndCfg.weight_schedule)             | RND 보상의 가중치 스케줄.                           |
| [`reward_normalization`](#isaaclab_rl.rsl_rl.RslRlRndCfg.reward_normalization)   | RND 보상을 정규화할지 여부.                       |
| [`state_normalization`](#isaaclab_rl.rsl_rl.RslRlRndCfg.state_normalization)     | RND 상태를 정규화할지 여부.                       |
| [`learning_rate`](#isaaclab_rl.rsl_rl.RslRlRndCfg.learning_rate)                 | RND 모듈의 학습률.                                |
| [`num_outputs`](#isaaclab_rl.rsl_rl.RslRlRndCfg.num_outputs)                     | RND 모듈의 출력 수.                               |
| [`predictor_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlRndCfg.predictor_hidden_dims) | RND 예측기 네트워크의 은닉 차원.                  |
| [`target_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlRndCfg.target_hidden_dims)       | RND 타겟 네트워크의 은닉 차원.                    |

**메서드:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlRndCfg.__init__)([weight, weight_schedule, ...])   |    |
|------------------------------------------------------------------------------------------|----|

#### *class* WeightScheduleCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

가중치 스케줄 구성입니다.

**속성:**

| [`mode`](#isaaclab_rl.rsl_rl.RslRlRndCfg.WeightScheduleCfg.mode)   | 가중치 스케줄의 유형입니다.   |
|--------------------------------------------------------------------|-------------------------------|

**메서드:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlRndCfg.WeightScheduleCfg.__init__)([mode])   |    |
|------------------------------------------------------------------------------------|----|

#### mode *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

가중치 스케줄의 유형입니다. 기본값은 “constant”입니다.

#### \_\_init_\_(mode: str = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### *class* LinearWeightScheduleCfg

Bases: [`WeightScheduleCfg`](#isaaclab_rl.rsl_rl.RslRlRndCfg.WeightScheduleCfg)

선형 가중치 스케줄 구성입니다.

이 스케줄은 [`initial_step`](#isaaclab_rl.rsl_rl.RslRlRndCfg.LinearWeightScheduleCfg.initial_step)에서 [`final_step`](#isaaclab_rl.rsl_rl.RslRlRndCfg.LinearWeightScheduleCfg.final_step) 이전까지 초기 값에서 최종 값까지 선형으로 가중치를 감소시킵니다.

**속성:**

| [`mode`](#isaaclab_rl.rsl_rl.RslRlRndCfg.LinearWeightScheduleCfg.mode)                 | 가중치 스케줄의 유형입니다.             |
|----------------------------------------------------------------------------------------|------------------------------------------|
| [`final_value`](#isaaclab_rl.rsl_rl.RslRlRndCfg.LinearWeightScheduleCfg.final_value)   | 가중치 매개변수의 최종 값입니다. |
| [`initial_step`](#isaaclab_rl.rsl_rl.RslRlRndCfg.LinearWeightScheduleCfg.initial_step) | 가중치 스케줄의 초기 단계입니다. |
| [`final_step`](#isaaclab_rl.rsl_rl.RslRlRndCfg.LinearWeightScheduleCfg.final_step)     | 가중치 스케줄의 최종 단계입니다.   |

**Methods:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlRndCfg.LinearWeightScheduleCfg.__init__)([mode, final_value, initial_step, ...])   |    |
|--------------------------------------------------------------------------------------------------------------------------|----|

#### mode *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

가중치 스케줄의 유형입니다. 기본값은 “constant”입니다.

#### final_value *: [float](https://docs.python.org/3/library/functions.html#float)*

가중치 매개변수의 최종 값입니다.

#### initial_step *: [int](https://docs.python.org/3/library/functions.html#int)*

가중치 스케줄의 초기 단계입니다.

이 단계 이전의 단계에서는 가중치가 [`RslRlRndCfg.weight`](#isaaclab_rl.rsl_rl.RslRlRndCfg.weight)에서 지정된 초기 값으로 설정됩니다.

#### final_step *: [int](https://docs.python.org/3/library/functions.html#int)*

가중치 스케줄의 최종 단계입니다.

이 단계 이후의 단계에서는 가중치가 [`final_value`](#isaaclab_rl.rsl_rl.RslRlRndCfg.LinearWeightScheduleCfg.final_value)에서 지정된 최종 값으로 설정됩니다.

#### \_\_init_\_(mode: str = <factory>, final_value: float = <factory>, initial_step: int = <factory>, final_step: int = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### *class* StepWeightScheduleCfg

Bases: [`WeightScheduleCfg`](#isaaclab_rl.rsl_rl.RslRlRndCfg.WeightScheduleCfg)

단계 가중치 스케줄을 위한 구성입니다.

이 스케줄은 단계 [`final_step`](#isaaclab_rl.rsl_rl.RslRlRndCfg.StepWeightScheduleCfg.final_step)에서 가중치를 [`final_value`](#isaaclab_rl.rsl_rl.RslRlRndCfg.StepWeightScheduleCfg.final_value)에서 지정된 값으로 설정합니다.

**Attributes:**

| [`mode`](#isaaclab_rl.rsl_rl.RslRlRndCfg.StepWeightScheduleCfg.mode)               | 가중치 스케줄의 유형입니다.             |
|------------------------------------------------------------------------------------|------------------------------------------|
| [`final_step`](#isaaclab_rl.rsl_rl.RslRlRndCfg.StepWeightScheduleCfg.final_step)   | 가중치 스케줄의 최종 단계입니다.   |
| [`final_value`](#isaaclab_rl.rsl_rl.RslRlRndCfg.StepWeightScheduleCfg.final_value) | 가중치 매개변수의 최종 값입니다. |

**Methods:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlRndCfg.StepWeightScheduleCfg.__init__)([mode, final_step, final_value])   |    |
|-----------------------------------------------------------------------------------------------------------------|----|

#### mode *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

가중치 스케줄의 유형입니다. 기본값은 “constant”입니다.

#### final_step *: [int](https://docs.python.org/3/library/functions.html#int)*

가중치 스케줄의 최종 단계입니다.

이 단계 이후의 단계에서는 가중치가 [`final_value`](#isaaclab_rl.rsl_rl.RslRlRndCfg.StepWeightScheduleCfg.final_value)에서 지정된 값으로 설정됩니다.

#### final_value *: [float](https://docs.python.org/3/library/functions.html#float)*

가중치 매개변수의 최종 값입니다.

#### \_\_init_\_(mode: str = <factory>, final_step: int = <factory>, final_value: float = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### weight *: [float](https://docs.python.org/3/library/functions.html#float)*

RND 보상에 대한 가중치(내재적 보상으로도 알려짐)입니다. 기본값은 0.0입니다.

다른 보상 항과 유사하게, RND 보상은 이 가중치에 의해 스케일링됩니다.

#### \_\_init_\_(weight: float = <factory>, weight_schedule: ~isaaclab_rl.rsl_rl.rnd_cfg.RslRlRndCfg.WeightScheduleCfg | None = <factory>, reward_normalization: bool = <factory>, state_normalization: bool = <factory>, learning_rate: float = <factory>, num_outputs: int = <factory>, predictor_hidden_dims: list[int] = <factory>, target_hidden_dims: list[int] = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### weight_schedule *: [WeightScheduleCfg](#isaaclab_rl.rsl_rl.RslRlRndCfg.WeightScheduleCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

RND 보상의 가중치 스케줄입니다. 기본값은 None이며, 이는 가중치가 상수임을 의미합니다.

#### reward_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

RND 보상을 정규화할지 여부입니다. 기본값은 False입니다.

#### state_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

RND 상태를 정규화할지 여부입니다. 기본값은 False입니다.

#### learning_rate *: [float](https://docs.python.org/3/library/functions.html#float)*

RND 모듈의 학습률입니다. 기본값은 1e-3입니다.

#### num_outputs *: [int](https://docs.python.org/3/library/functions.html#int)*

RND 모듈의 출력 수입니다. 기본값은 1입니다.

#### predictor_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

RND 예측자 네트워크의 은닉 차원입니다. 기본값은 [-1]입니다.

리스트에 -1이 포함되어 있으면 은닉 차원이 입력 차원과 동일하게 설정됩니다.

#### target_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

RND 목표 네트워크의 은닉 차원입니다. 기본값은 [-1]입니다.

리스트에 -1이 포함되어 있으면 은닉 차원이 입력 차원과 동일하게 설정됩니다.

### *class* isaaclab_rl.rsl_rl.RslRlSymmetryCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

훈련에서의 대칭성 증강을 위한 구성입니다.

[`use_data_augmentation()`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.use_data_augmentation)이 True이면, [`data_augmentation_func()`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.data_augmentation_func)를 사용하여 증강된 관측값과 행동을 생성합니다. 이러한 값들은 모델 훈련에 사용됩니다.

[`use_mirror_loss()`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.use_mirror_loss)이 True이면, [`mirror_loss_coeff()`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.mirror_loss_coeff)를 사용하여 대칭-미러 손실에 가중치를 적용합니다. 이 손실은 에이전트의 손실 함수에 직접 더해집니다.

[`use_data_augmentation()`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.use_data_augmentation)과 [`use_mirror_loss()`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.use_mirror_loss)이 모두 False이면 대칭 기반 훈련이 활성화되지 않습니다. 그러나, [`data_augmentation_func()`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.data_augmentation_func)를 호출하여 대칭 지표를 계산하고 로그를 남깁니다. 이는 어레이션 실험을 수행하는 데 유용합니다.

추가 정보는 [[MRK+24](../../refs/bibliography.md#id21)] 논문을 참조하세요.

**Attributes:**

| [`use_data_augmentation`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.use_data_augmentation)   | 대칭 기반 데이터 증강 사용 여부입니다.   |
|-----------------------------------------------------------------------------------------|----------------------------------------------------|
| [`use_mirror_loss`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.use_mirror_loss)               | 대칭-증강 손실 사용 여부입니다.     |
| [`data_augmentation_func`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.data_augmentation_func) | 대칭 데이터 증강 함수입니다.           |
| [`mirror_loss_coeff`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.mirror_loss_coeff)           | 대칭-미러 손실에 대한 가중치입니다.           |

**Methods:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg.__init__)([use_data_augmentation, ...])   |    |
|---------------------------------------------------------------------------------------------|----|

#### use_data_augmentation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

대칭 기반 데이터 증강 사용 여부입니다. 기본값은 False입니다.

#### use_mirror_loss *: [bool](https://docs.python.org/3/library/functions.html#bool)*

대칭-증강 손실 사용 여부입니다. 기본값은 False입니다.

#### \_\_init_\_(use_data_augmentation: bool = <factory>, use_mirror_loss: bool = <factory>, data_augmentation_func: callable = <factory>, mirror_loss_coeff: float = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### data_augmentation_func *: callable*

대칭 데이터 증강 함수입니다.

함수 시그니처는 다음과 같아야 합니다:

* **매개변수:**
  * **env** (*VecEnv*) – 환경 객체입니다. 환경의 속성에 접근하는 데 사용됩니다.
  * **obs** (*tensordict.TensorDict* *|* *None*) – 관측 텐서 딕셔너리입니다. None이면 관측값이 사용되지 않습니다.
  * **action** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) *|* *None*) – 행동 텐서입니다. None이면 행동이 사용되지 않습니다.
* **반환값:**
  증강된 관측 딕셔너리와 행동 텐서를 포함하는 튜플입니다. 텐서는 해당 입력이 None인 경우 None이 될 수 있습니다.

#### mirror_loss_coeff *: [float](https://docs.python.org/3/library/functions.html#float)*

대칭-미러 손실에 대한 가중치입니다. 기본값은 0.0입니다.

### *class* isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

러너의 기본 구성입니다.

**Methods:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.__init__)([seed, device, num_steps_per_env, ...])   |    |
|---------------------------------------------------------------------------------------------------------|----|

**Attributes:**

| [`seed`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.seed)                                       | 실험의 시드입니다.                                    |
|---------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`device`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.device)                                   |                                                                 |
| [`num_steps_per_env`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.num_steps_per_env)             | 업데이트당 환경당 스텝 수입니다.                 |
| [`max_iterations`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.max_iterations)                   | 최대 반복 횟수입니다.                               |
| [`empirical_normalization`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.empirical_normalization) | 이 매개변수는 더 이상 사용되지 않으며 향후 제거될 예정입니다. |
| [`obs_groups`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.obs_groups)                           | 옵저브 그룹을 옵저브 세트에 매핑합니다.          |
| [`clip_actions`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.clip_actions)                       | 행동 클리핑 값입니다.                                 |
| [`save_interval`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.save_interval)                     | 저장 간격의 반복 횟수입니다.                         |
| [`experiment_name`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.experiment_name)                 | 실험 이름입니다.                                            |
| [`run_name`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.run_name)                               | 실행 이름입니다.                                                   |
| [`logger`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.logger)                                   | 사용할 로거입니다.                                              |
| [`neptune_project`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.neptune_project)                 | 네프투니 프로젝트 이름입니다.                                       |
| [`wandb_project`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.wandb_project)                     | wandb 프로젝트 이름입니다.                                         |
| [`resume`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.resume)                                   | 이전 훈련을 재개할지 여부입니다.                          |
| [`load_run`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.load_run)                               | 로드할 실행 디렉터리입니다.                                      |
| [`load_checkpoint`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg.load_checkpoint)                 | 로드할 체크포인트 파일입니다.                                    |

#### \_\_init_\_(seed: int = <factory>, device: str = <factory>, num_steps_per_env: int = <factory>, max_iterations: int = <factory>, empirical_normalization: bool | None = <factory>, obs_groups: dict[str, list[str]] = <factory>, clip_actions: float | None = <factory>, save_interval: int = <factory>, experiment_name: str = <factory>, run_name: str = <factory>, logger: ~typing.Literal['tensorboard', 'neptune', 'wandb'] = <factory>, neptune_project: str = <factory>, wandb_project: str = <factory>, resume: bool = <factory>, load_run: str = <factory>, load_checkpoint: str = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### seed *: [int](https://docs.python.org/3/library/functions.html#int)*

실험의 시드입니다. 기본값은 42입니다.

#### device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

* **유형:**
  rl-에이전트에 사용할 장치입니다. 기본값은 cuda입니다.

#### num_steps_per_env *: [int](https://docs.python.org/3/library/functions.html#int)*

업데이트당 환경당 스텝 수입니다.

#### max_iterations *: [int](https://docs.python.org/3/library/functions.html#int)*

최대 반복 횟수입니다.

#### empirical_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

이 매개변수는 더 이상 사용되지 않으며 향후 제거될 예정입니다.

actor_obs_normalization과 critic_obs_normalization을 대신 사용하세요.

#### obs_groups *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]*

옵저브 그룹을 옵저브 세트에 매핑합니다.

사전의 키는 기반 알고리즘에서 사용되는 미리 정의된 옵저브 세트이며, 값은 환경에서 제공되는 옵저브 그룹 목록입니다.

예를 들어 환경이 "policy", "images", "privileged" 그룹의 관측값을 포함한 사전을 제공하는 경우, 다음과 같이 알고리즘 관측 세트에 매핑할 수 있습니다:

```python
obs_groups = {
    "policy": ["policy", "images"],
    "critic": ["policy", "privileged"],
}
```

이렇게 하면 정책이 "policy"와 "images" 관측값을 받고, 비판가가 "policy"와 "privileged" 관측값을 받게 됩니다.

자세한 내용은 rsl_rl 라이브러리의 `vec_env.py`를 참조하세요.

#### clip_actions *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

행동 클리핑 값입니다. None이면 클리핑이 수행되지 않습니다. 기본값은 None입니다.

#### NOTE
이 클리핑은 [`RslRlVecEnvWrapper`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper) 래퍼 내에서 수행됩니다.

#### save_interval *: [int](https://docs.python.org/3/library/functions.html#int)*

저장 간격의 반복 횟수입니다.

#### experiment_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

실험 이름입니다.

#### run_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

실행 이름입니다. 기본값은 빈 문자열입니다.

실행 디렉터리의 이름은 일반적으로 실행 시 타임스탬프입니다. 실행 이름이 비어 있지 않으면, 실행 디렉터리 이름에 추가되므로 로깅 디렉터리의 이름은 `{time-stamp}_{run_name}`이 됩니다.

#### logger *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['tensorboard', 'neptune', 'wandb']*

사용할 로거입니다. 기본값은 tensorboard입니다.

#### neptune_project *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

네프투니 프로젝트 이름입니다. 기본값은 “isaaclab”입니다.

#### wandb_project *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

wandb 프로젝트 이름입니다. 기본값은 “isaaclab”입니다.

#### resume *: [bool](https://docs.python.org/3/library/functions.html#bool)*

이전 훈련을 재개할지 여부입니다. 기본값은 False입니다.

이 플래그는 증류에서는 무시됩니다.

#### load_run *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

로드할 실행 디렉터리입니다. 기본값은 “.\*” (모든 디렉터리)입니다.

정규 표현식인 경우, 알파벳 순으로 최신 매칭 실행이 로드됩니다.

#### load_checkpoint *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

로드할 체크포인트 파일입니다. 기본값은 `"model_.*.pt"` (모든 파일)입니다.

정규 표현식인 경우, 알파벳 순으로 최신 매칭 파일이 로드됩니다.

### *class* isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

증류 알고리즘에 대한 구성입니다.

**메서드:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg.__init__)([class_name, num_learning_epochs, ...])   |    |
|--------------------------------------------------------------------------------------------------------------------|----|

**속성:**

| [`class_name`](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg.class_name)                   | 알고리즘 클래스 이름입니다.                                |
|------------------------------------------------------------------------------------------------|----------------------------------------------------------|
| [`num_learning_epochs`](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg.num_learning_epochs) | 각 샘플에 대해 수행되는 업데이트 횟수입니다.        |
| [`learning_rate`](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg.learning_rate)             | 학생 정책에 대한 학습률입니다.                |
| [`gradient_length`](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg.gradient_length)         | 그래디언트가 뒤로 흐르는 환경 스텝 수입니다. |
| [`max_grad_norm`](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg.max_grad_norm)             | 그래디언트가 클리핑되는 최대 노름입니다.             |
| [`optimizer`](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg.optimizer)                     | 학생 정책에 사용할 옵티마이저입니다.             |
| [`loss_type`](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg.loss_type)                     | 학생 정책에 사용할 손실 유형입니다.             |

#### \_\_init_\_(class_name: str = <factory>, num_learning_epochs: int = <factory>, learning_rate: float = <factory>, gradient_length: int = <factory>, max_grad_norm: None | float = <factory>, optimizer: ~typing.Literal['adam', 'adamw', 'sgd', 'rmsprop'] = <factory>, loss_type: ~typing.Literal['mse', 'huber'] = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### class_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

알고리즘 클래스 이름입니다. 기본값은 Distillation입니다.

#### num_learning_epochs *: [int](https://docs.python.org/3/library/functions.html#int)*

각 샘플에 대해 수행되는 업데이트 횟수입니다.

#### learning_rate *: [float](https://docs.python.org/3/library/functions.html#float)*

학생 정책에 대한 학습률입니다.

#### gradient_length *: [int](https://docs.python.org/3/library/functions.html#int)*

그래디언트가 뒤로 흐르는 환경 스텝 수입니다.

#### max_grad_norm *: [None](https://docs.python.org/3/library/constants.html#None) | [float](https://docs.python.org/3/library/functions.html#float)*

그라디언트가 클리핑되는 최대 노름입니다.

#### optimizer *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['adam', 'adamw', 'sgd', 'rmsprop']*

학생 정책에 사용할 옵티마이저입니다.

#### loss_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['mse', 'huber']*

학생 정책에 사용할 손실 함수 유형입니다.

### *class* isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg

Bases: [`RslRlBaseRunnerCfg`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg)

증류 알고리즘을 위한 러너의 구성입니다.

**메서드:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.__init__)([seed, device, num_steps_per_env, ...])   |    |
|-----------------------------------------------------------------------------------------------------------------|----|

**속성:**

| [`seed`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.seed)                                       | 실험의 시드입니다.                                    |
|-----------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`device`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.device)                                   |                                                                 |
| [`num_steps_per_env`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.num_steps_per_env)             | 업데이트당 환경당 스텝 수입니다.                 |
| [`max_iterations`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.max_iterations)                   | 최대 반복 횟수입니다.                               |
| [`empirical_normalization`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.empirical_normalization) | 이 매개변수는 향후 제거될 예정인 deprecated 매개변수입니다. |
| [`obs_groups`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.obs_groups)                           | 관찰 그룹을 관찰 세트에 매핑합니다.          |
| [`clip_actions`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.clip_actions)                       | 동작의 클리핑 값입니다.                                 |
| [`save_interval`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.save_interval)                     | 저장 간격의 반복 횟수입니다.                         |
| [`experiment_name`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.experiment_name)                 | 실험 이름입니다.                                            |
| [`run_name`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.run_name)                               | 실행 이름입니다.                                                   |
| [`logger`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.logger)                                   | 사용할 로거입니다.                                              |
| [`neptune_project`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.neptune_project)                 | 넵투인 프로젝트 이름입니다.                                       |
| [`wandb_project`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.wandb_project)                     | wandb 프로젝트 이름입니다.                                         |
| [`resume`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.resume)                                   | 이전 훈련을 재개할지 여부를 나타냅니다.                          |
| [`load_run`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.load_run)                               | 로드할 실행 디렉터리입니다.                                      |
| [`load_checkpoint`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.load_checkpoint)                 | 로드할 체크포인트 파일입니다.                                    |
| [`class_name`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.class_name)                           | 러너 클래스 이름입니다.                                          |
| [`policy`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.policy)                                   | 정책 구성입니다.                                       |
| [`algorithm`](#isaaclab_rl.rsl_rl.RslRlDistillationRunnerCfg.algorithm)                             | 알고리즘 구성입니다.                                    |

#### \_\_init_\_(seed: int = <factory>, device: str = <factory>, num_steps_per_env: int = <factory>, max_iterations: int = <factory>, empirical_normalization: bool | None = <factory>, obs_groups: dict[str, list[str]] = <factory>, clip_actions: float | None = <factory>, save_interval: int = <factory>, experiment_name: str = <factory>, run_name: str = <factory>, logger: ~typing.Literal['tensorboard', 'neptune', 'wandb'] = <factory>, neptune_project: str = <factory>, wandb_project: str = <factory>, resume: bool = <factory>, load_run: str = <factory>, load_checkpoint: str = <factory>, class_name: str = <factory>, policy: ~isaaclab_rl.rsl_rl.distillation_cfg.RslRlDistillationStudentTeacherCfg = <factory>, algorithm: ~isaaclab_rl.rsl_rl.distillation_cfg.RslRlDistillationAlgorithmCfg = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### seed *: [int](https://docs.python.org/3/library/functions.html#int)*

실험의 시드입니다. 기본값은 42입니다.

#### device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

* **유형:**
  RL 에이전트에 사용할 장치입니다. 기본값은 cuda입니다.

#### num_steps_per_env *: [int](https://docs.python.org/3/library/functions.html#int)*

업데이트당 환경당 스텝 수입니다.

#### max_iterations *: [int](https://docs.python.org/3/library/functions.html#int)*

최대 반복 횟수입니다.

#### empirical_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

이 매개변수는 향후 제거될 예정인 deprecated 매개변수입니다.

대신 actor_obs_normalization과 critic_obs_normalization을 사용하세요.

#### obs_groups *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]*

관찰 그룹을 관찰 세트에 매핑합니다.

사전 정의된 관찰 세트는 딕셔너리의 키이며, 환경이 제공하는 관찰 그룹 리스트는 값입니다.

예를 들어, 환경이 “policy”, “images”, “privileged” 그룹을 가진 관찰 딕셔너리를 제공하는 경우, 다음과 같이 알고리즘 관찰 세트에 매핑할 수 있습니다:

```python
obs_groups = {
    "policy": ["policy", "images"],
    "critic": ["policy", "privileged"],
}
```

이렇게 하면 정책은 “policy”와 “images” 관측을 받고, 비판가는 “policy”와 “privileged” 관측을 받게 됩니다.

자세한 내용은 rsl_rl 라이브러리의 `vec_env.py`를 참조하세요.

#### clip_actions *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

동작의 클리핑 값입니다. None이면 클리핑이 수행되지 않습니다. 기본값은 None입니다.

#### NOTE
이 클리핑은 [`RslRlVecEnvWrapper`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper) 래퍼 내에서 수행됩니다.

#### save_interval *: [int](https://docs.python.org/3/library/functions.html#int)*

저장 간격의 반복 횟수입니다.

#### experiment_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

실험 이름입니다.

#### run_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

실행 이름입니다. 기본값은 빈 문자열입니다.

실행 디렉터리 이름은 일반적으로 실행 시의 타임스탬프입니다. 실행 이름이 비어 있지 않다면, 실행 디렉터리 이름에 추가됩니다. 즉, 로깅 디렉터리 이름은 `{time-stamp}_{run_name}`이 됩니다.

#### logger *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['tensorboard', 'neptune', 'wandb']*

사용할 로거입니다. 기본값은 tensorboard입니다.

#### neptune_project *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

넵투인 프로젝트 이름입니다. 기본값은 “isaaclab”입니다.

#### wandb_project *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

wandb 프로젝트 이름입니다. 기본값은 “isaaclab”입니다.

#### resume *: [bool](https://docs.python.org/3/library/functions.html#bool)*

이전 훈련을 재개할지 여부를 나타냅니다. 기본값은 False입니다.

이 플래그는 증류에서는 무시됩니다.

#### load_run *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

로드할 실행 디렉터리입니다. 기본값은 “.\*” (전체)입니다.

정규 표현식이면 알파벳 순서로 가장 일치하는 최신 실행이 로드됩니다.

#### load_checkpoint *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

로드할 체크포인트 파일입니다. 기본값은 `"model_.*.pt"` (전체)입니다.

정규 표현식이면 알파벳 순서로 가장 일치하는 최신 파일이 로드됩니다.

#### class_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

러너 클래스 이름입니다. 기본값은 DistillationRunner입니다.

#### policy *: [RslRlDistillationStudentTeacherCfg](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg)*

정책 구성입니다.

#### algorithm *: [RslRlDistillationAlgorithmCfg](#isaaclab_rl.rsl_rl.RslRlDistillationAlgorithmCfg)*

알고리즘 구성입니다.

### *class* isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

증류 학생-교사 네트워크를 위한 구성입니다.

**속성:**

| [`class_name`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg.class_name)                               | 정책 클래스 이름입니다.                                        |
|-----------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`init_noise_std`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg.init_noise_std)                       | 학생 정책의 초기 노이즈 표준 편차.                              |
| [`noise_std_type`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg.noise_std_type)                       | 정책의 노이즈 표준 편차 유형.                                   |
| [`student_obs_normalization`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg.student_obs_normalization) | 학생 네트워크의 관측값을 정규화할지 여부.                      |
| [`teacher_obs_normalization`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg.teacher_obs_normalization) | 교사 네트워크의 관측값을 정규화할지 여부.                      |
| [`student_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg.student_hidden_dims)             | 학생 네트워크의 히든 차원.                                      |
| [`teacher_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg.teacher_hidden_dims)             | 교사 네트워크의 히든 차원.                                      |
| [`activation`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg.activation)                               | 학생 및 교사 네트워크의 활성화 함수.                            |

**Methods:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg.__init__)([class_name, init_noise_std, ...])   |    |
|--------------------------------------------------------------------------------------------------------------------|----|

#### class_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

정책 클래스 이름. 기본값은 StudentTeacher.

#### init_noise_std *: [float](https://docs.python.org/3/library/functions.html#float)*

학생 정책의 초기 노이즈 표준 편차.

#### noise_std_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['scalar', 'log']*

정책의 노이즈 표준 편차 유형. 기본값은 scalar.

#### student_obs_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

학생 네트워크의 관측값을 정규화할지 여부.

#### teacher_obs_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

교사 네트워크의 관측값을 정규화할지 여부.

#### student_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

학생 네트워크의 히든 차원.

#### teacher_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

교사 네트워크의 히든 차원.

#### activation *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

학생 및 교사 네트워크의 활성화 함수.

#### \_\_init_\_(class_name: str = <factory>, init_noise_std: float = <factory>, noise_std_type: ~typing.Literal['scalar', 'log'] = <factory>, student_obs_normalization: bool = <factory>, teacher_obs_normalization: bool = <factory>, student_hidden_dims: list[int] = <factory>, teacher_hidden_dims: list[int] = <factory>, activation: str = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

### *class* isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg

Bases: [`RslRlDistillationStudentTeacherCfg`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherCfg)

증류 학생-교사 재귀 네트워크의 구성.

**Attributes:**

| [`class_name`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.class_name)                               | 정책 클래스 이름.                                                |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`rnn_type`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.rnn_type)                                   | RNN 네트워크의 유형.                                          |
| [`rnn_hidden_dim`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.rnn_hidden_dim)                       | RNN 네트워크의 히든 차원.                                    |
| [`rnn_num_layers`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.rnn_num_layers)                       | RNN 네트워크의 레이어 수.                                    |
| [`init_noise_std`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.init_noise_std)                       | 학생 정책의 초기 노이즈 표준 편차.                            |
| [`noise_std_type`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.noise_std_type)                       | 정책의 노이즈 표준 편차 유형.                                 |
| [`student_obs_normalization`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.student_obs_normalization) | 학생 네트워크의 관측값을 정규화할지 여부.                    |
| [`teacher_obs_normalization`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.teacher_obs_normalization) | 교사 네트워크의 관측값을 정규화할지 여부.                    |
| [`student_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.student_hidden_dims)             | 학생 네트워크의 히든 차원.                                   |
| [`teacher_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.teacher_hidden_dims)             | 교사 네트워크의 히든 차원.                                   |
| [`activation`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.activation)                               | 학생 및 교사 네트워크의 활성화 함수.                          |
| [`teacher_recurrent`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.teacher_recurrent)                 | 교사 네트워크도 재귀적일지 여부.                             |

**Methods:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlDistillationStudentTeacherRecurrentCfg.__init__)([class_name, init_noise_std, ...])   |    |
|-----------------------------------------------------------------------------------------------------------------------------|----|

#### class_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

정책 클래스 이름. 기본값은 StudentTeacherRecurrent.

#### rnn_type *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

RNN 네트워크의 유형. "lstm" 또는 "gru" 중 하나.

#### rnn_hidden_dim *: [int](https://docs.python.org/3/library/functions.html#int)*

RNN 네트워크의 히든 차원.

#### rnn_num_layers *: [int](https://docs.python.org/3/library/functions.html#int)*

RNN 네트워크의 레이어 수.

#### \_\_init_\_(class_name: str = <factory>, init_noise_std: float = <factory>, noise_std_type: ~typing.Literal['scalar', 'log'] = <factory>, student_obs_normalization: bool = <factory>, teacher_obs_normalization: bool = <factory>, student_hidden_dims: list[int] = <factory>, teacher_hidden_dims: list[int] = <factory>, activation: str = <factory>, rnn_type: str = <factory>, rnn_hidden_dim: int = <factory>, rnn_num_layers: int = <factory>, teacher_recurrent: bool = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### init_noise_std *: [float](https://docs.python.org/3/library/functions.html#float)*

학생 정책의 초기 노이즈 표준 편차.

#### noise_std_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['scalar', 'log']*

정책의 노이즈 표준 편차 유형. 기본값은 scalar.

#### student_obs_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

학생 네트워크의 관측값을 정규화할지 여부.

#### teacher_obs_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

교사 네트워크의 관측값을 정규화할지 여부.

#### student_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

학생 네트워크의 히든 차원.

#### teacher_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

교사 네트워크의 히든 차원.

#### activation *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

학생 및 교사 네트워크의 활성화 함수.

#### teacher_recurrent *: [bool](https://docs.python.org/3/library/functions.html#bool)*

교사 네트워크도 재귀적일지 여부.

### *class* isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg

Bases: [`RslRlBaseRunnerCfg`](#isaaclab_rl.rsl_rl.RslRlBaseRunnerCfg)

온-폴리시 알고리즘의 러너 구성.

**Methods:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.__init__)([seed, device, num_steps_per_env, ...])   |    |
|-------------------------------------------------------------------------------------------------------------|----|

**Attributes:**

| [`seed`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.seed)                                       | 실험의 시드.                                        |
|-------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`device`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.device)                                   |                                                                 |
| [`num_steps_per_env`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.num_steps_per_env)             | 환경당 업데이트당 단계 수.                   |
| [`max_iterations`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.max_iterations)                   | 반복 횟수의 최대값입니다.                               |
| [`empirical_normalization`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.empirical_normalization) | 이 매개변수는 더 이상 사용되지 않으며 향후 제거됩니다. |
| [`obs_groups`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.obs_groups)                           | 관찰 그룹을 관찰 세트에 매핑하는 사전입니다.          |
| [`clip_actions`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.clip_actions)                       | 행동의 클리핑 값입니다.                                 |
| [`save_interval`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.save_interval)                     | 저장 간격의 반복 횟수입니다.                         |
| [`experiment_name`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.experiment_name)                 | 실험 이름입니다.                                            |
| [`run_name`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.run_name)                               | 실행 이름입니다.                                                   |
| [`logger`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.logger)                                   | 사용할 로거입니다.                                              |
| [`neptune_project`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.neptune_project)                 | Neptune 프로젝트 이름입니다.                                       |
| [`wandb_project`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.wandb_project)                     | Wandb 프로젝트 이름입니다.                                         |
| [`resume`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.resume)                                   | 이전 학습을 다시 시작할지 여부입니다.                          |
| [`load_run`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.load_run)                               | 로드할 실행 디렉터리입니다.                                      |
| [`load_checkpoint`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.load_checkpoint)                 | 로드할 체크포인트 파일입니다.                                    |
| [`class_name`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.class_name)                           | 실행기 클래스 이름입니다.                                          |
| [`policy`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.policy)                                   | 정책 구성입니다.                                       |
| [`algorithm`](#isaaclab_rl.rsl_rl.RslRlOnPolicyRunnerCfg.algorithm)                             | 알고리즘 구성입니다.                                    |

#### \_\_init_\_(seed: int = <factory>, device: str = <factory>, num_steps_per_env: int = <factory>, max_iterations: int = <factory>, empirical_normalization: bool | None = <factory>, obs_groups: dict[str, list[str]] = <factory>, clip_actions: float | None = <factory>, save_interval: int = <factory>, experiment_name: str = <factory>, run_name: str = <factory>, logger: ~typing.Literal['tensorboard', 'neptune', 'wandb'] = <factory>, neptune_project: str = <factory>, wandb_project: str = <factory>, resume: bool = <factory>, load_run: str = <factory>, load_checkpoint: str = <factory>, class_name: str = <factory>, policy: ~isaaclab_rl.rsl_rl.rl_cfg.RslRlPpoActorCriticCfg = <factory>, algorithm: ~isaaclab_rl.rsl_rl.rl_cfg.RslRlPpoAlgorithmCfg = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### seed *: [int](https://docs.python.org/3/library/functions.html#int)*

실험의 시드 값입니다. 기본값은 42입니다.

#### device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

* **Type:**
  RL 에이전트를 위한 디바이스입니다. 기본값은 cuda입니다.

#### num_steps_per_env *: [int](https://docs.python.org/3/library/functions.html#int)*

업데이트당 환경당 단계 수입니다.

#### max_iterations *: [int](https://docs.python.org/3/library/functions.html#int)*

최대 반복 횟수입니다.

#### empirical_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

이 매개변수는 더 이상 사용되지 않으며 향후 제거됩니다.

대신 `actor_obs_normalization`과 `critic_obs_normalization`을 사용하세요.

#### obs_groups *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]*

관찰 그룹을 관찰 세트에 매핑하는 사전입니다.

사전의 키는 기반 알고리즘에서 사용하는 미리 정의된 관찰 세트이며, 값은 환경에서 제공하는 관찰 그룹의 목록입니다.

예를 들어, 환경이 "policy", "images", "privileged" 그룹의 관찰 딕셔너리를 제공하는 경우, 다음과 같이 알고리즘 관찰 세트에 매핑할 수 있습니다:

```python
obs_groups = {
    "policy": ["policy", "images"],
    "critic": ["policy", "privileged"],
}
```

이렇게 하면 정책이 "policy"와 "images" 관찰을 받고, 비평가가 "policy"와 "privileged" 관찰을 받게 됩니다.

자세한 내용은 rsl_rl 라이브러리의 `vec_env.py`를 확인하세요.

#### clip_actions *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

행동의 클리핑 값입니다. None인 경우 클리핑이 수행되지 않습니다. 기본값은 None입니다.

#### NOTE
이 클리핑은 [`RslRlVecEnvWrapper`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper) 래퍼 내부에서 수행됩니다.

#### save_interval *: [int](https://docs.python.org/3/library/functions.html#int)*

저장 간격의 반복 횟수입니다.

#### experiment_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

실험 이름입니다.

#### run_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

실행 이름입니다. 기본값은 빈 문자열입니다.

실행 디렉터리 이름은 일반적으로 실행 시 타임스탬프입니다. 실행 이름이 비어 있지 않으면, 실행 디렉터리 이름에 추가되므로 로깅 디렉터리 이름은 `{time-stamp}_{run_name}`이 됩니다.

#### logger *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['tensorboard', 'neptune', 'wandb']*

사용할 로거입니다. 기본값은 tensorboard입니다.

#### neptune_project *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

Neptune 프로젝트 이름입니다. 기본값은 “isaaclab”입니다.

#### wandb_project *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

Wandb 프로젝트 이름입니다. 기본값은 “isaaclab”입니다.

#### resume *: [bool](https://docs.python.org/3/library/functions.html#bool)*

이전 학습을 다시 시작할지 여부입니다. 기본값은 False입니다.

이 플래그는 증류(distillation)에서 무시됩니다.

#### load_run *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

로드할 실행 디렉터리입니다. 기본값은 “.\*” (전체)입니다.

정규 표현식이면 알파벳 순서로 가장 최근에 일치하는 실행이 로드됩니다.

#### load_checkpoint *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

로드할 체크포인트 파일입니다. 기본값은 `"model_.*.pt"` (전체)입니다.

정규 표현식이면 알파벳 순서로 가장 최근에 일치하는 파일이 로드됩니다.

#### class_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

실행기 클래스 이름입니다. 기본값은 OnPolicyRunner입니다.

#### policy *: [RslRlPpoActorCriticCfg](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg)*

정책 구성입니다.

#### algorithm *: [RslRlPpoAlgorithmCfg](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg)*

알고리즘 구성입니다.

### *class* isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

PPO 액터-크리틱 네트워크의 구성입니다.

**Attributes:**

| [`class_name`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.class_name)                             | 정책 클래스 이름입니다.                                            |
|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| [`init_noise_std`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.init_noise_std)                     | 정책의 초기 노이즈 표준 편차입니다.              |
| [`noise_std_type`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.noise_std_type)                     | 정책의 노이즈 표준 편차 유형입니다.              |
| [`state_dependent_std`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.state_dependent_std)           | 정책에 상태 의존적 표준 편차를 사용할지 여부입니다. |
| [`actor_obs_normalization`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.actor_obs_normalization)   | 액터 네트워크를 위한 관찰 정규화 여부입니다.       |
| [`critic_obs_normalization`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.critic_obs_normalization) | 크리틱 네트워크를 위한 관찰 정규화 여부입니다.      |
| [`actor_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.actor_hidden_dims)               | 액터 네트워크의 은닉 차원입니다.                       |
| [`critic_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.critic_hidden_dims)             | 크리틱 네트워크의 은닉 차원입니다.                      |
| [`activation`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.activation)                             | 액터 및 크리틱 네트워크의 활성화 함수입니다.        |

**Methods:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg.__init__)([class_name, init_noise_std, ...])   |    |
|--------------------------------------------------------------------------------------------------------|----|

#### class_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

정책 클래스 이름입니다. 기본값은 ActorCritic입니다.

#### init_noise_std *: [float](https://docs.python.org/3/library/functions.html#float)*

초기 정책의 노이즈 표준 편차입니다.

#### noise_std_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['scalar', 'log']*

정책의 노이즈 표준 편차 유형입니다. 기본값은 scalar입니다.

#### state_dependent_std *: [bool](https://docs.python.org/3/library/functions.html#bool)*

정책에 상태 의존적 표준 편차를 사용할지 여부입니다. 기본값은 False입니다.

#### actor_obs_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액터 네트워크의 관찰값을 정규화할지 여부입니다.

#### critic_obs_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

크리틱 네트워크의 관찰값을 정규화할지 여부입니다.

#### actor_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

액터 네트워크의 은닉 차원입니다.

#### critic_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

크리틱 네트워크의 은닉 차원입니다.

#### activation *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

액터와 크리틱 네트워크의 활성화 함수입니다.

#### \_\_init_\_(class_name: str = <factory>, init_noise_std: float = <factory>, noise_std_type: ~typing.Literal['scalar', 'log'] = <factory>, state_dependent_std: bool = <factory>, actor_obs_normalization: bool = <factory>, critic_obs_normalization: bool = <factory>, actor_hidden_dims: list[int] = <factory>, critic_hidden_dims: list[int] = <factory>, activation: str = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

### *class* isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg

Bases: [`RslRlPpoActorCriticCfg`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticCfg)

재귀 레이어를 갖는 PPO 액터-크리틱 네트워크에 대한 구성입니다.

**Attributes:**

| [`class_name`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.class_name)                             | 정책 클래스 이름입니다.                                            |
|------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| [`rnn_type`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.rnn_type)                                 | 사용할 RNN의 유형입니다.                                           |
| [`rnn_hidden_dim`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.rnn_hidden_dim)                     | RNN 레이어의 차원입니다.                                           |
| [`rnn_num_layers`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.rnn_num_layers)                     | RNN 레이어의 개수입니다.                                           |
| [`init_noise_std`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.init_noise_std)                     | 정책의 초기 노이즈 표준 편차입니다.                               |
| [`noise_std_type`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.noise_std_type)                     | 정책의 노이즈 표준 편차 유형입니다.                               |
| [`state_dependent_std`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.state_dependent_std)           | 정책에 상태 의존적 표준 편차를 사용할지 여부입니다.                |
| [`actor_obs_normalization`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.actor_obs_normalization)   | 액터 네트워크의 관찰값을 정규화할지 여부입니다.                    |
| [`critic_obs_normalization`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.critic_obs_normalization) | 크리틱 네트워크의 관찰값을 정규화할지 여부입니다.                  |
| [`actor_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.actor_hidden_dims)               | 액터 네트워크의 은닉 차원입니다.                                   |
| [`critic_hidden_dims`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.critic_hidden_dims)             | 크리틱 네트워크의 은닉 차원입니다.                                 |
| [`activation`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.activation)                             | 액터와 크리틱 네트워크의 활성화 함수입니다.                        |

**Methods:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlPpoActorCriticRecurrentCfg.__init__)([class_name, init_noise_std, ...])   |    |
|-----------------------------------------------------------------------------------------------------------------|----|

#### class_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

정책 클래스 이름입니다. 기본값은 ActorCriticRecurrent입니다.

#### rnn_type *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

사용할 RNN의 유형입니다. “lstm” 또는 “gru” 중 하나여야 합니다.

#### rnn_hidden_dim *: [int](https://docs.python.org/3/library/functions.html#int)*

RNN 레이어의 차원입니다.

#### rnn_num_layers *: [int](https://docs.python.org/3/library/functions.html#int)*

RNN 레이어의 개수입니다.

#### \_\_init_\_(class_name: str = <factory>, init_noise_std: float = <factory>, noise_std_type: ~typing.Literal['scalar', 'log'] = <factory>, state_dependent_std: bool = <factory>, actor_obs_normalization: bool = <factory>, critic_obs_normalization: bool = <factory>, actor_hidden_dims: list[int] = <factory>, critic_hidden_dims: list[int] = <factory>, activation: str = <factory>, rnn_type: str = <factory>, rnn_hidden_dim: int = <factory>, rnn_num_layers: int = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### init_noise_std *: [float](https://docs.python.org/3/library/functions.html#float)*

정책의 초기 노이즈 표준 편차입니다.

#### noise_std_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['scalar', 'log']*

정책의 노이즈 표준 편차 유형입니다. 기본값은 scalar입니다.

#### state_dependent_std *: [bool](https://docs.python.org/3/library/functions.html#bool)*

정책에 상태 의존적 표준 편차를 사용할지 여부입니다. 기본값은 False입니다.

#### actor_obs_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액터 네트워크의 관찰값을 정규화할지 여부입니다.

#### critic_obs_normalization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

크리틱 네트워크의 관찰값을 정규화할지 여부입니다.

#### actor_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

액터 네트워크의 은닉 차원입니다.

#### critic_hidden_dims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]*

크리틱 네트워크의 은닉 차원입니다.

#### activation *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

액터와 크리틱 네트워크의 활성화 함수입니다.

### *class* isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

PPO 알고리즘에 대한 구성입니다.

**Attributes:**

| [`class_name`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.class_name)                                                 | 알고리즘 클래스 이름입니다.                                    |
|---------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| [`num_learning_epochs`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.num_learning_epochs)                               | 업데이트당 학습 에포크 수입니다.                              |
| [`num_mini_batches`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.num_mini_batches)                                     | 업데이트당 미니배치 수입니다.                                 |
| [`learning_rate`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.learning_rate)                                           | 정책의 학습률입니다.                                          |
| [`schedule`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.schedule)                                                     | 학습률 스케줄입니다.                                          |
| [`gamma`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.gamma)                                                           | 할인 인자입니다.                                              |
| [`lam`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.lam)                                                               | 일반화된 이점 추정(GAE)의 람다 매개변수입니다.                |
| [`entropy_coef`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.entropy_coef)                                             | 엔트로피 손실의 계수입니다.                                   |
| [`desired_kl`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.desired_kl)                                                 | 원하는 KL 발산입니다.                                         |
| [`max_grad_norm`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.max_grad_norm)                                           | 최대 기울기 노름입니다.                                       |
| [`value_loss_coef`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.value_loss_coef)                                       | 값 손실의 계수입니다.                                         |
| [`use_clipped_value_loss`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.use_clipped_value_loss)                         | 클리핑된 값 손실을 사용할지 여부입니다.                        |
| [`clip_param`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.clip_param)                                                 | 정책의 클리핑 매개변수입니다.                                 |
| [`normalize_advantage_per_mini_batch`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.normalize_advantage_per_mini_batch) | 미니배치당 이점을 정규화할지 여부입니다.                      |
| [`rnd_cfg`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.rnd_cfg)                                                       | RND 구성.                                           |
| [`symmetry_cfg`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.symmetry_cfg)                                             | 대칭 구성.                                          |

**메서드:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlPpoAlgorithmCfg.__init__)([class_name, num_learning_epochs, ...])   |    |
|-----------------------------------------------------------------------------------------------------------|----|

#### class_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

알고리즘 클래스 이름. 기본값은 PPO입니다.

#### num_learning_epochs *: [int](https://docs.python.org/3/library/functions.html#int)*

업데이트당 학습 에포크 수.

#### num_mini_batches *: [int](https://docs.python.org/3/library/functions.html#int)*

업데이트당 미니 배치 수.

#### learning_rate *: [float](https://docs.python.org/3/library/functions.html#float)*

정책의 학습률.

#### schedule *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

학습률 스케줄.

#### gamma *: [float](https://docs.python.org/3/library/functions.html#float)*

할인 인자.

#### lam *: [float](https://docs.python.org/3/library/functions.html#float)*

일반화된 이점 추정(GAE)의 람다 매개변수.

#### entropy_coef *: [float](https://docs.python.org/3/library/functions.html#float)*

엔트로피 손실의 계수.

#### desired_kl *: [float](https://docs.python.org/3/library/functions.html#float)*

원하는 KL 발산.

#### max_grad_norm *: [float](https://docs.python.org/3/library/functions.html#float)*

최대 그래디언트 노름.

#### value_loss_coef *: [float](https://docs.python.org/3/library/functions.html#float)*

값 손실의 계수.

#### use_clipped_value_loss *: [bool](https://docs.python.org/3/library/functions.html#bool)*

클리핑된 값 손실을 사용할지 여부.

#### \_\_init_\_(class_name: str = <factory>, num_learning_epochs: int = <factory>, num_mini_batches: int = <factory>, learning_rate: float = <factory>, schedule: str = <factory>, gamma: float = <factory>, lam: float = <factory>, entropy_coef: float = <factory>, desired_kl: float = <factory>, max_grad_norm: float = <factory>, value_loss_coef: float = <factory>, use_clipped_value_loss: bool = <factory>, clip_param: float = <factory>, normalize_advantage_per_mini_batch: bool = <factory>, rnd_cfg: ~isaaclab_rl.rsl_rl.rnd_cfg.RslRlRndCfg | None = <factory>, symmetry_cfg: ~isaaclab_rl.rsl_rl.symmetry_cfg.RslRlSymmetryCfg | None = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### clip_param *: [float](https://docs.python.org/3/library/functions.html#float)*

정책의 클리핑 매개변수.

#### normalize_advantage_per_mini_batch *: [bool](https://docs.python.org/3/library/functions.html#bool)*

미니 배치당 이점을 정규화할지 여부. 기본값은 False.

True인 경우, 이점은 미니 배치에서만 정규화됩니다.
그렇지 않으면, 이점은 수집된 전체 궤적에 대해 정규화됩니다.

#### rnd_cfg *: [RslRlRndCfg](#isaaclab_rl.rsl_rl.RslRlRndCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

RND 구성. 기본값은 None이며, 이 경우 RND는 사용되지 않습니다.

#### symmetry_cfg *: [RslRlSymmetryCfg](#isaaclab_rl.rsl_rl.RslRlSymmetryCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

대칭 구성. 기본값은 None이며, 이 경우 대칭은 사용되지 않습니다.

### *class* isaaclab_rl.rsl_rl.RslRlVecEnvWrapper

Bases: `VecEnv`

Isaac Lab 환경을 RSL-RL 라이브러리용으로 감싸는 래퍼

참조:
: [https://github.com/leggedrobotics/rsl_rl/blob/master/rsl_rl/env/vec_env.py](https://github.com/leggedrobotics/rsl_rl/blob/master/rsl_rl/env/vec_env.py)

**메서드:**

| [`__init__`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.__init__)(env[, clip_actions])   | 래퍼를 초기화합니다.                             |
|--------------------------------------------------------------------------------------|------------------------------------------------------|
| [`class_name`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.class_name)()                  | 래퍼의 클래스 이름을 반환합니다.               |
| [`get_observations`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.get_observations)()      | 환경의 현재 관찰값을 반환합니다.      |

**속성:**

| [`cfg`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.cfg)                               | 환경의 구성 클래스 인스턴스를 반환합니다.                                       |
|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| [`render_mode`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.render_mode)               | `Env` [`render_mode`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.render_mode)를 반환합니다.             |
| [`observation_space`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.observation_space)   | `Env` [`observation_space`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.observation_space)를 반환합니다. |
| [`action_space`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.action_space)             | `Env` [`action_space`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.action_space)를 반환합니다.           |
| [`unwrapped`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.unwrapped)                   | 래퍼의 기본 환경을 반환합니다.                                                       |
| [`episode_length_buf`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.episode_length_buf) | 에피소드 길이 버퍼입니다.                                                                         |

#### \_\_init_\_(env: [ManagerBasedRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) | [DirectRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv), clip_actions: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None)

래퍼를 초기화합니다.

#### NOTE
RSL-RL 러너가 reset을 호출하지 않기 때문에 래퍼는 시작時に `reset()`을 호출합니다.

* **매개변수:**
  * **env** – 감쌀 환경.
  * **clip_actions** – 행동 클리핑 값. `None`인 경우 클리핑이 수행되지 않습니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 환경이 `ManagerBasedRLEnv` 또는 `DirectRLEnv`의 인스턴스가 아닌 경우.

#### *property* cfg *: [object](https://docs.python.org/3/library/functions.html#object)*

환경의 구성 클래스 인스턴스를 반환합니다.

#### *property* render_mode *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

`Env` [`render_mode`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.render_mode)를 반환합니다.

#### *property* observation_space *: [Space](https://gymnasium.farama.org/api/spaces/#gymnasium.spaces.Space)*

`Env` [`observation_space`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.observation_space)를 반환합니다.

#### *property* action_space *: [Space](https://gymnasium.farama.org/api/spaces/#gymnasium.spaces.Space)*

`Env` [`action_space`](#isaaclab_rl.rsl_rl.RslRlVecEnvWrapper.action_space)를 반환합니다.

#### *classmethod* class_name() → [str](https://docs.python.org/3/library/stdtypes.html#str)*

래퍼의 클래스 이름을 반환합니다.

#### *property* unwrapped *: [ManagerBasedRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) | [DirectRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv)*

래퍼의 기본 환경을 반환합니다.

이것은 모든 래퍼 레이어 아래에 있는 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 기본 환경입니다.

#### *property* episode_length_buf *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

에피소드 길이 버퍼.

#### get_observations() → tensordict.TensorDict

환경의 현재 관찰값을 반환합니다.

### isaaclab_rl.rsl_rl.configclass(cls, \*\*kwargs)

dataclass 기능을 감싸서 추가 검사 및 유틸리티를 제공하는 래퍼입니다.

Python 3.7부터 표준 dataclass에는 구성 사용 사례에 일반적이지 않은 두 가지 주요 문제점이 있습니다. 이러한 문제점은 다음과 같습니다.

1. 모든 멤버에 타입 어노테이션을 요구합니다.
2. 변경 가능한 변수를 재초기화하기 위해 `field(default_factory=...)()`를 명시적으로 사용해야 합니다.

이 함수는 Python의 [dataclass](https://docs.python.org/3/library/dataclasses.html) 유틸리티를 감싸는 데코레이터를 제공하여 위의 두 가지 문제를 처리합니다. 또한 딕셔너리 <-> 클래스 변환 및 클래스 인스턴스 복사를 위한 추가 헬퍼 함수를 제공합니다.

사용법:

```python
from dataclasses import MISSING

from isaaclab.utils.configclass import configclass


@configclass
class ViewerCfg:
    eye: list = [7.5, 7.5, 7.5]  # 필드가 의도적으로 누락됨
    lookat: list = field(default_factory=[0.0, 0.0, 0.0])


@configclass
class EnvCfg:
    num_envs: int = MISSING
    episode_length: int = 2000
    viewer: ViewerCfg = ViewerCfg()


# 구성 인스턴스 생성
env_cfg = EnvCfg(num_envs=24)

# 딕셔너리로 정보 출력
print(env_cfg.to_dict())

# 구성의 복사본 생성
env_cfg_copy = env_cfg.copy()

# 키워드 인수를 사용하여 임의 필드 교체
env_cfg_copy = env_cfg_copy.replace(num_envs=32)
```

* **매개변수:**
  * **cls** – 감쌀 클래스.
  * **\*\*kwargs** – `dataclass()`에 전달할 추가 인수.
* **반환값:**
  래핑된 클래스.

## SKRL 래퍼

skrl 환경에 환경 인스턴스를 구성하는 래퍼입니다.

다음 예시는 skrl용으로 환경을 래핑하는 방법을 보여줍니다.

```python
from isaaclab_rl.skrl import SkrlVecEnvWrapper

env = SkrlVecEnvWrapper(env, ml_framework="torch")  # 또는 ml_framework="jax"
```

동등하게, skrl 라이브러리 API를 다음과 같이 직접 호출할 수도 있습니다:

```python
from skrl.envs.torch.wrappers import wrap_env  # PyTorch용, 또는...
from skrl.envs.jax.wrappers import wrap_env  # JAX용

env = wrap_env(env, wrapper="isaaclab")
```

**함수:**

| [`SkrlVecEnvWrapper`](#isaaclab_rl.skrl.SkrlVecEnvWrapper)(env[, ml_framework, wrapper])   | Isaac Lab 환경을 skrl용으로 감싸는 래퍼입니다.   |
|--------------------------------------------------------------------------------------------|------------------------------------------------|

### isaaclab_rl.skrl.SkrlVecEnvWrapper(env: [ManagerBasedRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) | [DirectRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv) | [DirectMARLEnv](../lab/isaaclab.envs.md#isaaclab.envs.DirectMARLEnv), ml_framework: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['torch', 'jax', 'jax-numpy'] = 'torch', wrapper: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['auto', 'isaaclab', 'isaaclab-single-agent', 'isaaclab-multi-agent'] = 'isaaclab')

Isaac Lab 환경을 skrl용으로 감싸는 래퍼입니다.

이 함수는 Isaac Lab 환경을 감쌉니다. 래핑 기능이 skrl 라이브러리 자체 내에 정의되어 있기 때문에, 이 구현은 이를 포함하는 확장의 구조와의 호환성을 위해 유지됩니다. 내부적으로 skrl 라이브러리 API의 `wrap_env()`를 호출합니다.

* **매개변수:**
  * **env** – 감쌀 환경입니다.
  * **ml_framework** – 래퍼에 사용할 ML 프레임워크입니다. 기본값은 "torch"입니다.
  * **wrapper** – 사용할 래퍼입니다. 기본값은 "isaaclab"이며, skrl이 단일 에이전트 또는 다중 에이전트로 래핑할지를 결정하도록 둡니다.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 환경이 Isaac Lab 환경 인터페이스의 인스턴스가 아닐 때 발생합니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 ML 프레임워크가 유효하지 않을 때 발생합니다.

참조:
: [https://skrl.readthedocs.io/en/latest/api/envs/wrapping.html](https://skrl.readthedocs.io/en/latest/api/envs/wrapping.html)

## Stable-Baselines3 래퍼

Stable-Baselines3 벡터화된 환경 인스턴스를 구성하기 위한 래퍼입니다.

다음 예시는 Stable-Baselines3용으로 환경을 감싸는 방법을 보여줍니다:

```python
from isaaclab_rl.sb3 import Sb3VecEnvWrapper

env = Sb3VecEnvWrapper(env)
```

**함수:**

| [`process_sb3_cfg`](#isaaclab_rl.sb3.process_sb3_cfg)(cfg, num_envs)   | 간단한 YAML 타입을 Stable-Baselines 클래스/컴포넌트로 변환합니다.   |
|------------------------------------------------------------------------|---------------------------------------------------------------------|

**클래스:**

| [`Sb3VecEnvWrapper`](#isaaclab_rl.sb3.Sb3VecEnvWrapper)   | Stable Baselines3용으로 Isaac Lab 환경을 감싸는 래퍼입니다.   |
|-----------------------------------------------------------|-------------------------------------------------------------|

### isaaclab_rl.sb3.process_sb3_cfg(cfg: [dict](https://docs.python.org/3/library/stdtypes.html#dict), num_envs: [int](https://docs.python.org/3/library/functions.html#int)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

간단한 YAML 타입을 Stable-Baselines 클래스/컴포넌트로 변환합니다.

* **매개변수:**
  * **cfg** – 구성 딕셔너리입니다.
  * **num_envs** – 병렬 환경의 수(원하는 미니배치 수에 대한 배치 크기를 계산하는 데 사용됨)
* **반환값:**
  변환된 구성을 포함하는 딕셔너리입니다.

참조:
: [https://github.com/DLR-RM/rl-baselines3-zoo/blob/0e5eb145faefa33e7d79c7f8c179788574b20da5/utils/exp_manager.py#L358](https://github.com/DLR-RM/rl-baselines3-zoo/blob/0e5eb145faefa33e7d79c7f8c179788574b20da5/utils/exp_manager.py#L358)

### *class* isaaclab_rl.sb3.Sb3VecEnvWrapper

Bases: `VecEnv`

Stable Baselines3용으로 Isaac Lab 환경을 감싸는 래퍼입니다.

Isaac Sim은 내부적으로 벡터화된 환경을 구현하지만, 여전히 단일 환경 인스턴스로 간주되므로 Stable Baselines는 이를 `DummyVecEnv`로 감싸려고 시도합니다. 이는 환경이 그들의 `VecEnv`를 상속하지 않을 때만 수행됩니다. 따라서 이 클래스는 환경에서 `ManagerBasedRLEnv` 또는 `DirectRLEnv` 상위를 얇게 감쌉니다.

#### 참고 사항
Stable-Baselines3는 Gym 0.26+ API를 지원하지만, 그들의 벡터화된 환경은 자체 API를 사용합니다(즉, Gym 0.21에 더 가깝습니다). 따라서 우리는 벡터화된 환경용 API를 구현합니다.

또한 미할인 에피소드 반환과 길이를 계산하는 모니터링 기능을 추가합니다. 이 정보는 키 `episode` 아래의 info 딕셔너리에 추가됩니다.

Isaac Lab 환경과 달리, Stable-Baselines3는 다음을 기대합니다:

1. MDP 신호에 대한 numpy 데이터 타입
2. 각 서브 환경에 대한 info 딕셔너리 리스트(딕셔너리 대신)
3. 환경이 종료되었을 때, 환경에서의 관측치는 리셋 후의 관측치에 해당해야 합니다. 실제 최종 관측치는 키 `terminal_observation` 아래의 info 딕셔너리를 통해 전달됩니다.

#### 경고
Isaac Sim에서 물리 스텝의 특성상, 물리 스텝을 수행하지 않고 시뮬레이션 버퍼를 전달하는 것은 불가능합니다. 따라서 리셋은 실제 물리 스텝이行われた 후에 `step()` 함수 내부에서 수행됩니다. 따라서 종료된 환경에 대한 반환 관측치는 리셋 후의 관측치입니다.

참조:

1. [https://stable-baselines3.readthedocs.io/en/master/guide/vec_envs.html](https://stable-baselines3.readthedocs.io/en/master/guide/vec_envs.html)
2. [https://stable-baselines3.readthedocs.io/en/master/common/monitor.html](https://stable-baselines3.readthedocs.io/en/master/common/monitor.html)

**메서드:**

| [`__init__`](#isaaclab_rl.sb3.Sb3VecEnvWrapper.__init__)(env[, fast_variant])    | 래퍼를 초기화합니다.                               |
|----------------------------------------------------------------------------------|-------------------------------------------------------|
| [`class_name`](#isaaclab_rl.sb3.Sb3VecEnvWrapper.class_name)()                   | 래퍼의 클래스 이름을 반환합니다.                |
| [`get_episode_rewards`](#isaaclab_rl.sb3.Sb3VecEnvWrapper.get_episode_rewards)() | 모든 에피소드의 보상을 반환합니다.              |
| [`get_episode_lengths`](#isaaclab_rl.sb3.Sb3VecEnvWrapper.get_episode_lengths)() | 모든 에피소드의 시간-스텝 수를 반환합니다. |

**속성:**

| [`unwrapped`](#isaaclab_rl.sb3.Sb3VecEnvWrapper.unwrapped)   | 래퍼의 기본 환경을 반환합니다.   |
|--------------------------------------------------------------|------------------------------------------------|

#### \_\_init_\_(env: [ManagerBasedRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) | [DirectRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv), fast_variant: [bool](https://docs.python.org/3/library/functions.html#bool) = True)

래퍼를 초기화합니다.

* **매개변수:**
  * **env** – 감쌀 환경입니다.
  * **fast_variant** – 정보 처리에 빠른 변형을 사용합니다
    (에피소드 보상, 길이 및 트렁케이션 정보만 포함됨)
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 환경이 `ManagerBasedRLEnv` 또는 `DirectRLEnv` 인스턴스가 아닐 때 발생합니다.

#### *classmethod* class_name() → [str](https://docs.python.org/3/library/stdtypes.html#str)

래퍼의 클래스 이름을 반환합니다.

#### *property* unwrapped *: [ManagerBasedRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv) | [DirectRLEnv](../lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv)*

래퍼의 기본 환경을 반환합니다.

이것은 모든 래퍼 레이어 밑에 있는 순수한 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 환경입니다.

#### get_episode_rewards() → [list](https://docs.python.org/3/library/stdtypes.html#list)[[float](https://docs.python.org/3/library/functions.html#float)]

모든 에피소드의 보상을 반환합니다.

#### get_episode_lengths() → [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)]

모든 에피소드의 시간-스텝 수를 반환합니다.
</translate>
