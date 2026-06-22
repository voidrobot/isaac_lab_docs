# isaaclab_contrib.actuators

스러스터 액추에이터 모델을 위한 서브패키지입니다.

이 패키지는 다중로터 스러스터용으로 특별히 설계된 액추에이터 모델을 제공합니다.
스러스터 액추에이터는 비대칭 상승/하강 시간 상수, 스러스트 한계, 동적 응답 특성을 포함한 현실적인 모터/프로펠러 동역학을 시뮬레이션합니다.

### 클래스

| [`Thruster`](#isaaclab_contrib.actuators.Thruster)       | 별도의 상승/하강 시간 상수를 가진 저수준 모터/스러스터 동역학.   |
|----------------------------------------------------------|----------------------------------------------------------------|
| [`ThrusterCfg`](#isaaclab_contrib.actuators.ThrusterCfg) | 스러스터 액추에이터 그룹을 위한 구성.                               |

## 스러스터 액추에이터

### *class* isaaclab_contrib.actuators.Thruster

기반: [`object`](https://docs.python.org/3/library/functions.html#object)

별도의 상승/하강 시간 상수를 가진 저수준 모터/스러스터 동역학.

통합 방식은 오일러 또는 RK4입니다. 모든 내부 버퍼는 (num_envs, num_motors) 모양입니다.
단위: 스러스트 [N], 비율 [N/s], 시간 [s].

**특성:**

| [`computed_thrust`](#isaaclab_contrib.actuators.Thruster.computed_thrust)   | 액추에이터 그룹의 계산된 스러스트.                 |
|-----------------------------------------------------------------------------|----------------------------------------------------|
| [`applied_thrust`](#isaaclab_contrib.actuators.Thruster.applied_thrust)     | 액추에이터 그룹의 적용된 스러스트.                  |
| [`num_thrusters`](#isaaclab_contrib.actuators.Thruster.num_thrusters)       | 그룹 내 액추에이터 수.                             |
| [`thruster_names`](#isaaclab_contrib.actuators.Thruster.thruster_names)     | 그룹에 포함된 어티큘레이션의 스러스터 이름.   |
| [`thruster_indices`](#isaaclab_contrib.actuators.Thruster.thruster_indices) | 그룹에 포함된 어티큘레이션의 스러스터 인덱스. |

**메서드:**

| [`__init__`](#isaaclab_contrib.actuators.Thruster.__init__)(cfg, thruster_names, thruster_ids, ...)   | 버퍼 구성 및 모터별 파라미터 샘플링.   |
|-------------------------------------------------------------------------------------------------------|------------------------------------------------|
| [`compute`](#isaaclab_contrib.actuators.Thruster.compute)(control_action)                             | 스러스터 상태 한 단계 진행.                 |
| [`reset_idx`](#isaaclab_contrib.actuators.Thruster.reset_idx)([env_ids])                              | 파라미터 재샘플링 및 상태 재초기화.         |
| [`reset`](#isaaclab_contrib.actuators.Thruster.reset)(env_ids)                                        | 모든 환경 재초기화.                          |

#### computed_thrust *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

액추에이터 그룹의 계산된 스러스트. 모양은 (num_envs, num_thrusters)입니다.

#### applied_thrust *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

액추에이터 그룹의 적용된 스러스트. 모양은 (num_envs, num_thrusters)입니다.

이는 스러스터 특성에 기반하여 [`computed_thrust`](#isaaclab_contrib.actuators.Thruster.computed_thrust)를 클리핑하여 얻은 스러스트입니다.

#### \_\_init_\_(cfg: [ThrusterCfg](#isaaclab_contrib.actuators.ThrusterCfg), thruster_names: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)], thruster_ids: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), num_envs: [int](https://docs.python.org/3/library/functions.html#int), device: [str](https://docs.python.org/3/library/stdtypes.html#str), init_thruster_rps: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor))

버퍼 구성 및 모터별 파라미터 샘플링.

* **파라미터:**
  * **cfg** – 스러스터 구성.
  * **thruster_names** – 이 그룹에 속한 스러스터 이름 목록.
  * **thruster_ids** – 어티큘레이션 스러스터 배열의 인덱스를 나타내는 슬라이스 또는 텐서.
  * **num_envs** – 병렬/벡터화된 환경 수.
  * **device** – PyTorch 장치 문자열 또는 장치 식별자.
  * **init_thruster_rps** – RPM 기반 스러스터 모델링을 사용하는 구성에서 사용되는 초당 회전 수 초기 텐서.

#### *property* num_thrusters *: [int](https://docs.python.org/3/library/functions.html#int)*

그룹 내 액추에이터 수.

#### *property* thruster_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

그룹에 포함된 어티큘레이션의 스러스터 이름.

#### *property* thruster_indices *: [slice](https://docs.python.org/3/library/functions.html#slice) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

그룹에 포함된 어티큘레이션의 스러스터 인덱스.

#### 참고
`slice(None)`이 반환되는 경우, 그룹이 어티큘레이션의 모든 스러스터를 포함한다는 의미입니다.
성능상의 이유로 스러스터 불필요한 인덱싱을 피하기 위해 이렇게 처리합니다.

#### compute(control_action: MultiRotorActions) → MultiRotorActions

스러스터 상태 한 단계 진행.

포화 적용, 모터별 상승/하강 타우 선택, 혼합 요소 계산, 선택된 커널을 사용한 적분 수행.

* **파라미터:**
  **control_action** – (num_envs, num_thrusters) 명령된 스러스터별 스러스트 [N].
* **반환값:**
  (num_envs, num_thrusters) 업데이트된 스러스터 상태 [N].

#### reset_idx(env_ids=None) → [None](https://docs.python.org/3/library/constants.html#None)

파라미터 재샘플링 및 상태 재초기화.

* **파라미터:**
  **env_ids** – 재초기화할 환경 인덱스. `None`인 경우 모든 환경 재초기화.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)]) → [None](https://docs.python.org/3/library/constants.html#None)

모든 환경 재초기화.

### *class* isaaclab_contrib.actuators.ThrusterCfg

기반: [`object`](https://docs.python.org/3/library/functions.html#object)

스러스터 액추에이터 그룹을 위한 구성.

이 구성은 저수준 스러스터/모터 모델(시간 상수, 스러스트 범위, 통합 방식, 초기 상태 사양)에서 사용되는 액추에이터 그룹별 파라미터를 정의합니다. `MISSING`로 남아 있는 필드는 필수이며 사용자 구성에서 제공해야 합니다.

**특성:**

| [`dt`](#isaaclab_contrib.actuators.ThrusterCfg.dt)                                                 | 스러스터 업데이트에 사용되는 시뮬레이션/통합 타임스텝 [s].                     |
|----------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| [`thrust_range`](#isaaclab_contrib.actuators.ThrusterCfg.thrust_range)                             | 이 구간으로 클리핑됩니다.                                                        |
| [`max_thrust_rate`](#isaaclab_contrib.actuators.ThrusterCfg.max_thrust_rate)                       | 퍼스트-오더 모델 내 적용되는 모터당 스러스트 속도 제한 [N/s].                   |
| [`thrust_const_range`](#isaaclab_contrib.actuators.ThrusterCfg.thrust_const_range)                 | 스러스트 계수 $k_f$ 범위 [N/(rps²)].                                            |
| [`tau_inc_range`](#isaaclab_contrib.actuators.ThrusterCfg.tau_inc_range)                           | 명령 출력이 **증가**하는 경우(상승 동역학)의 시간 상수 범위 [s].             |
| [`tau_dec_range`](#isaaclab_contrib.actuators.ThrusterCfg.tau_dec_range)                           | 명령 출력이 **감소**하는 경우(하강 동역학)의 시간 상수 범위 [s].             |
| [`torque_to_thrust_ratio`](#isaaclab_contrib.actuators.ThrusterCfg.torque_to_thrust_ratio)         | +Z 축을 중심으로 모터 토크로 변환하는 요우스-모멘트 계수 [N·m per N].        |
| [`use_discrete_approximation`](#isaaclab_contrib.actuators.ThrusterCfg.use_discrete_approximation) | 액추에이터/모터 혼합 요소 계산 방법을 결정.                                      |
| [`integration_scheme`](#isaaclab_contrib.actuators.ThrusterCfg.integration_scheme)                 | 퍼스트-오더 모델을 위한 수치 적분 방법.                                       |
| [`thruster_names_expr`](#isaaclab_contrib.actuators.ThrusterCfg.thruster_names_expr)               | 그룹에 포함된 어티큘레이션의 관절 이름.                                   |

#### dt *: [float](https://docs.python.org/3/library/functions.html#float)*

스러스터 업데이트에 사용되는 시뮬레이션/통합 타임스텝 [s].

#### thrust_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

이 구간으로 클리핑됩니다.

* **유형:**
  모터당 스러스트 클램프 범위 [N]

#### max_thrust_rate *: [float](https://docs.python.org/3/library/functions.html#float)*

퍼스트-오더 모델 내 적용되는 모터당 스러스트 속도 제한 [N/s].

#### thrust_const_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

스러스트 계수 $k_f$ 범위 [N/(rps²)].

#### tau_inc_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

명령 출력이 **증가**하는 경우(상승 동역학)의 시간 상수 범위 [s].

#### tau_dec_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

명령 출력이 **감소**하는 경우(하강 동역학)의 시간 상수 범위 [s].

#### torque_to_thrust_ratio *: [float](https://docs.python.org/3/library/functions.html#float)*

+Z 축을 중심으로 모터 토크로 변환하는 요우스-모멘트 계수 [N·m per N].
다음과 같이 사용됩니다: `tau_z = torque_to_thrust_ratio * thrust_z * direction`.

#### use_discrete_approximation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액추에이터/모터 혼합 요소 계산 방법을 결정. 기본값은 True입니다.

True인 경우, 제어 루프 타임스텝을 고려한離散 시간 요인 `1 / (dt + tau)`를 사용합니다.
False인 경우, 연속 시간 요인 `1 / tau`를 사용합니다.

#### integration_scheme *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['rk4', 'euler']*

퍼스트-오더 모델을 위한 수치 적분 방법. 기본값은 ‘rk4’입니다.

#### thruster_names_expr *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

그룹에 포함된 어티큘레이션의 관절 이름.
