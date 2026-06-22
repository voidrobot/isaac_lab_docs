# isaaclab.controllers

다양한 컨트롤러와 모션 생성기를 위한 서브 패키지입니다.

컨트롤러 또는 모션 생성기는 주어진 명령의 폐쇄 루프 추적을 담당합니다. 컨트롤러는 단순한 PID 컨트롤러일 수도 있고, 임피던스 제어나 역운동학 제어와 같은 더 복잡한 컨트롤러일 수도 있습니다. 컨트롤러는 로봇에 전송될 원하는 조인트 수준 명령을 생성하는 책임이 있습니다.

### 클래스

| [`DifferentialIKController`](#isaaclab.controllers.DifferentialIKController)           | 차분 역운동학(IK) 컨트롤러.                                                   |
|----------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| [`DifferentialIKControllerCfg`](#isaaclab.controllers.DifferentialIKControllerCfg)     | 차분 역운동학 컨트롤러용 구성.                                      |
| [`OperationalSpaceController`](#isaaclab.controllers.OperationalSpaceController)       | 작업 공간(Operational-space) 컨트롤러.                                                                      |
| [`OperationalSpaceControllerCfg`](#isaaclab.controllers.OperationalSpaceControllerCfg) | 작업 공간 컨트롤러용 구성.                                                    |
| [`pink_ik.PinkIKController`](#isaaclab.controllers.pink_ik.PinkIKController)           | Isaac Lab과 Pink IK 컨트롤러 통합.                                                  |
| [`pink_ik.PinkIKControllerCfg`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg)     | Pink IK 컨트롤러 설정.                                                 |
| [`pink_ik.NullSpacePostureTask`](#isaaclab.controllers.pink_ik.NullSpacePostureTask)   | 다른 작업의 영영공간(null space) 투영에 자세(objective)를 추가하는 Pink 기반 작업.   |

## 차분 역운동학

### *class* isaaclab.controllers.DifferentialIKController

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

차분 역운동학(IK) 컨트롤러.

이 컨트롤러는 차분 역운동학 [1, 2]의 개념에 기반합니다. 차분 역운동학은 원하는 포즈 변화에 해당하는 관절 위치 변화를 계산하는 방법입니다.

$$
\Delta \mathbf{q} &= \mathbf{J}^{\dagger} \Delta \mathbf{x} \\
\mathbf{q}_{\text{desired}} &= \mathbf{q}_{\text{current}} + \Delta \mathbf{q}
$$

여기서 $\mathbf{J}^{\dagger}$는 야코비안 행렬 $\mathbf{J}$의 Moore-Penrose 가역행렬(또는 의사역행렬)이고,
$\Delta \mathbf{x}$는 원하는 포즈 변화, $\mathbf{q}_{\text{current}}$
는 현재 관절 위치를 나타냅니다.

야코비안의 특이점을 처리하기 위해 다음 방법을 야코비안의 역 계산에 지원합니다:

- “pinv”: Moore-Penrose 가역행렬
- “svd”: 적응적 특이값 분해(SVD)
- “trans”: 행렬의 전치
- “dls”: 감쇠된 Moore-Penrose 가역행렬(레버버그-마쿼드트 방법이라고도 함)

참고:

1. [Robot Dynamics Lecture Notes](https://ethz.ch/content/dam/ethz/special-interest/mavt/robotics-n-intelligent-systems/rsl-dam/documents/RobotDynamics2017/RD_HS2017script.pdf)
   Marco Hutter (ETH Zurich) 저
2. [Introduction to Inverse Kinematics](https://www.cs.cmu.edu/~15464-s13/lectures/lecture6/iksurvey.pdf)
   Samuel R. Buss (University of California, San Diego) 저

**메서드:**

| [`__init__`](#isaaclab.controllers.DifferentialIKController.__init__)(cfg, num_envs, device)              | 컨트롤러를 초기화합니다.                                                         |
|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| [`reset`](#isaaclab.controllers.DifferentialIKController.reset)([env_ids])                                | 내부 상태를 재설정합니다.                                                               |
| [`set_command`](#isaaclab.controllers.DifferentialIKController.set_command)(command[, ee_pos, ee_quat])   | 목표 엔드이펙터 포즈 명령을 설정합니다.                                              |
| [`compute`](#isaaclab.controllers.DifferentialIKController.compute)(ee_pos, ee_quat, jacobian, joint_pos) | 원하는 엔드이펙터 포즈를 생성하는 목표 관절 위치를 계산합니다. |

**속성:**

| [`action_dim`](#isaaclab.controllers.DifferentialIKController.action_dim)   | 컨트롤러 입력 명령의 차원.   |
|-----------------------------------------------------------------------------|------------------------------------------------|

#### \_\_init_\_(cfg: [DifferentialIKControllerCfg](#isaaclab.controllers.DifferentialIKControllerCfg), num_envs: [int](https://docs.python.org/3/library/functions.html#int), device: [str](https://docs.python.org/3/library/stdtypes.html#str))

컨트롤러를 초기화합니다.

* **매개변수:**
  * **cfg** – 컨트롤러에 대한 구성입니다.
  * **num_envs** – 환경의 수입니다.
  * **device** – 계산을 수행할 장치입니다.

#### *property* action_dim *: [int](https://docs.python.org/3/library/functions.html#int)*

컨트롤러 입력 명령의 차원입니다.

#### reset(env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) = None)

내부 상태를 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 환경 인덱스. None인 경우 모든 환경이 재설정됩니다.

#### set_command(command: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), ee_pos: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, ee_quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

목표 엔드이펙터 포즈 명령을 설정합니다.

구성된 명령 유형과 상대 모드에 따라 이 메서드는 원하는 엔드이펙터 포즈를 계산합니다. 사용자는 명령이 올바른 프레임으로 주어졌는지 확인해야 합니다. 명령 유형이 `position_rel` 또는 `pose_rel`인 경우에만 상대 모드가 적용됩니다.

* **매개변수:**
  * **command** – (N, 3), (N, 6) 또는 (N, 7) 형태의 입력 명령입니다.
  * **ee_pos** – (N, 3) 형태의 현재 엔드이펙터 위치입니다.
    명령 유형이 `position_rel` 또는 `pose_rel`인 경우에만 필요합니다.
  * **ee_quat** – (N, 4) 형태의 현재 엔드이펙터 방향(위, x, y, z)입니다.
    명령 유형이 `position_*` 또는 `pose_rel`인 경우에만 필요합니다.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 명령 유형이 `position_*`이고 `ee_quat`가 None인 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 명령 유형이 `position_rel`이고 `ee_pos`가 None인 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 명령 유형이 `pose_rel`이고 `ee_pos` 또는 `ee_quat` 중 하나가 None인 경우.

#### compute(ee_pos: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), ee_quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), jacobian: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), joint_pos: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

원하는 엔드이펙터 포즈를 생성하는 목표 관절 위치를 계산합니다.

* **매개변수:**
  * **ee_pos** – (N, 3) 형태의 현재 엔드이펙터 위치입니다.
  * **ee_quat** – (N, 4) 형태의 현재 엔드이펙터 방향입니다.
  * **jacobian** – (N, 6, num_joints) 형태의 기하학적 야코비안 행렬입니다.
  * **joint_pos** – (N, num_joints) 형태의 현재 관절 위치입니다.
* **반환값:**
  (N, num_joints) 형태의 목표 관절 위치 명령입니다.

### *class* isaaclab.controllers.DifferentialIKControllerCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

차분 역운동학 컨트롤러용 구성.

**속성:**

| [`command_type`](#isaaclab.controllers.DifferentialIKControllerCfg.command_type)           | 관절체의 동작을 제어하는 작업 공간 명령 유형.   |
|--------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| [`use_relative_mode`](#isaaclab.controllers.DifferentialIKControllerCfg.use_relative_mode) | 컨트롤러에서 상대 모드를 사용할지 여부.                 |
| [`ik_method`](#isaaclab.controllers.DifferentialIKControllerCfg.ik_method)                 | 야코비안의 역을 계산하는 방법.                        |
| [`ik_params`](#isaaclab.controllers.DifferentialIKControllerCfg.ik_params)                 | 역운동학 방법에 사용되는 파라미터.                    |

#### command_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['position', 'pose']*

관절체의 동작을 제어하는 작업 공간 명령 유형입니다.

"position"인 경우, 컨트롤러는 관절체의 위치만 제어합니다. 그렇지 않은 경우, 컨트롤러는 관절체의 포즈(위치 + 방향)를 제어합니다.

#### use_relative_mode *: [bool](https://docs.python.org/3/library/functions.html#bool)*

컨트롤러에서 상대 모드를 사용할지 여부입니다. 기본값은 False입니다.

True인 경우, 컨트롤러는 입력 명령을 위치/포즈의 변화량(델타)으로 해석합니다. False인 경우, 입력 명령을 절대 위치/포즈로 해석합니다.

#### ik_method *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['pinv', 'svd', 'trans', 'dls']*

야코비안의 역을 계산하는 방법입니다.

#### ik_params *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

역학계산 방법의 매개변수입니다. 기본값은 None이며, 이 경우 해당 방법의 기본 매개변수가 사용됩니다.

- Moore-Penrose 의사역행렬 (“pinv”):
  : - “k_val”: 계산된 델타 조인트 위치의 스케일링 (기본값: 1.0).
- 적응적 특잇값 분해 (“svd”):
  : - “k_val”: 계산된 델타 조인트 위치의 스케일링 (기본값: 1.0).
    - “min_singular_value”: 이 값보다 작은 특잇값은 0으로 억제됩니다 (기본값: 1e-5).
- 자코비안 전치 (“trans”):
  : - “k_val”: 계산된 델타 조인트 위치의 스케일링 (기본값: 1.0).
- 감쇠된 Moore-Penrose 의사역행렬 (“dls”):
  : - “lambda_val”: 감쇠 계수 (기본값: 0.01).

## 운영 공간 컨트롤러

### *class* isaaclab.controllers.OperationalSpaceController

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

운영 공간 컨트롤러입니다.

참조:

1. [A unified approach for motion and force control of robot manipulators: The operational space formulation](http://dx.doi.org/10.1109/JRA.1987.1087068)
   Oussama Khatib (스탠퍼드 대학교)
2. [Robot Dynamics Lecture Notes](https://ethz.ch/content/dam/ethz/special-interest/mavt/robotics-n-intelligent-systems/rsl-dam/documents/RobotDynamics2017/RD_HS2017script.pdf)
   Marco Hutter (ETH 취리히)

**메서드:**

| [`__init__`](#isaaclab.controllers.OperationalSpaceController.__init__)(cfg, num_envs, device)                   | 운영 공간 컨트롤러 초기화.             |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------|
| [`reset`](#isaaclab.controllers.OperationalSpaceController.reset)()                                              | 내부 상태 초기화.                                 |
| [`set_command`](#isaaclab.controllers.OperationalSpaceController.set_command)(command[, current_ee_pose_b, ...]) | 작업 공간 목표치 및 임피던스 매개변수 설정. |
| [`compute`](#isaaclab.controllers.OperationalSpaceController.compute)(jacobian_b[, current_ee_pose_b, ...])      | 컨트롤러 추론 수행.              |

**속성:**

| [`action_dim`](#isaaclab.controllers.OperationalSpaceController.action_dim)   | 컨트롤러의 행동 공간 차원.   |
|-------------------------------------------------------------------------------|------------------------------------------------|

#### \_\_init_\_(cfg: [OperationalSpaceControllerCfg](#isaaclab.controllers.OperationalSpaceControllerCfg), num_envs: [int](https://docs.python.org/3/library/functions.html#int), device: [str](https://docs.python.org/3/library/stdtypes.html#str))

운영 공간 컨트롤러 초기화.

* **매개변수:**
  * **cfg** – 운영 공간 컨트롤러의 구성.
  * **num_envs** – 환경의 수.
  * **device** – 계산에 사용할 장치.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 유효하지 않은 제어 명령이 제공될 때 발생.

#### *property* action_dim *: [int](https://docs.python.org/3/library/functions.html#int)*

컨트롤러의 행동 공간 차원.

#### reset()

내부 상태 초기화.

#### set_command(command: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), current_ee_pose_b: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, current_task_frame_pose_b: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

작업 공간 목표치 및 임피던스 매개변수 설정.

* **매개변수:**
  * **command** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – (`num_envs`, `action_dim`) 크기의 텐서로, 작업 공간 목표치(즉, 자세/렌치)와 임피던스 매개변수가 연결된 형태입니다.
  * **current_ee_pose_b** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) *,* *optional*) – 루트 프레임 기준 현재 엔드 이펙터 자세로, (`num_envs`, 7) 크기의 텐서이며, 위치와 쿼터니언 `(w, x, y, z)`를 포함합니다. 상대 명령에 필요합니다. 기본값은 None입니다.
  * **current_task_frame_pose_b** – 루트 프레임 기준 현재 작업 프레임 자세로, 목표치 및 (모션/렌치) 제어 축이 정의되는 기준이 됩니다. (`num_envs`, 7) 크기의 텐서이며, 위치와 쿼터니언 `(w, x, y, z)`를 포함합니다. 기본값은 None입니다.

형식:
: 작업 공간 목표치는 ‘command_types’에 따라 다음 순서대로 배치됩니다:
  <br/>
  > 절대 자세: (`num_envs`, 7) 크기의 텐서로, 위치와 쿼터니언 `(w, x, y, z)` 포함.
  > 상대 자세: (`num_envs`, 6) 크기의 텐서로, 델타 위치 및 축-각도 형태의 회전 포함.
  > 절대 렌치: (`num_envs`, 6) 크기의 텐서로, 힘과 토크 포함.
  <br/>
  임피던스 매개변수: `variable_kp`에 대한 강성, 또는 `variable`에 대한 강성과 감쇠 비율이 순서대로 배치됩니다:
  <br/>
  > 강성: (`num_envs`, 6) 크기의 텐서
  > 감쇠 비율: (`num_envs`, 6) 크기의 텐서

* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 명령 차원이 유효하지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 유효하지 않은 임피던스 모드가 제공될 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `pose_rel` 명령에 현재 엔드 이펙터 자세가 제공되지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 유효하지 않은 제어 명령이 제공될 때.

#### compute(jacobian_b: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), current_ee_pose_b: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, current_ee_vel_b: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, current_ee_force_b: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, mass_matrix: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, gravity: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, current_joint_pos: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, current_joint_vel: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, nullspace_joint_pos_target: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

컨트롤러 추론 수행.

* **매개변수:**
  * **jacobian_b** – 루트 프레임 기준 엔드 이펙터의 자코비안 행렬. (`num_envs`, 6, `num_DoF`) 크기의 텐서입니다.
  * **current_ee_pose_b** – 루트 프레임 기준 현재 엔드 이펙터 자세. (`num_envs`, 7) 크기의 텐서이며, 위치와 쿼터니언 `(w, x, y, z)` 포함. 기본값은 `None`.
  * **current_ee_vel_b** – 루트 프레임 기준 현재 엔드 이펙터 속도. (`num_envs`, 6) 크기의 텐서이며, 선형 및 각속도 포함. 기본값은 None.
  * **current_ee_force_b** – 루트 프레임 기준 엔드 이펙터에 작용하는 외부 힘. (`num_envs`, 3) 크기의 텐서이며, 선형 힘 포함. 기본값은 `None`.
  * **mass_matrix** – 관절 공간 질량/관성 행렬. (`num_envs`, `num_DoF`, `num_DoF`) 크기의 텐서. 기본값은 `None`.
  * **gravity** – 관절 공간 중력 벡터. (`num_envs`, `num_DoF`) 크기의 텐서. 기본값은 `None`.
  * **current_joint_pos** – 현재 관절 위치. (`num_envs`, `num_DoF`) 크기의 텐서. 기본값은 `None`.
  * **current_joint_vel** – 현재 관절 속도. (`num_envs`, `num_DoF`) 크기의 텐서. 기본값은 `None`.
  * **nullspace_joint_pos_target** – 가능한 경우 null 공간 컨트롤러가 적용하려는 목표 관절 위치. (`num_envs`, `num_DoF`) 크기의 텐서.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 모션 제어가 활성화되어 있지만 현재 엔드 이펙터 자세 또는 속도가 제공되지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 관성 역동기 분리(inertial dynamics decoupling)가 활성화되어 있지만 질량 행렬이 제공되지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `pose_rel` 명령에 현재 엔드 이펙터 자세가 제공되지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 폐루프 힘 제어가 활성화되어 있지만 현재 엔드 이펙터 힘이 제공되지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 중력 보상이 활성화되어 있지만 중력 벡터가 제공되지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – null 공간 제어가 활성화되어 있지만 시스템이 중복되지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 동적으로 일관된 유사역행렬이 활성화되었으나 질량 행렬의 역행렬이 제공되지 않은 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 널스페이스 제어가 활성화되었으나 현재 조인트 위치와 속도가 제공되지 않은 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 널스페이스 제어를 위한 목표 조인트 위치가 제공되었으나 현재 조인트 위치의 차원과 일치하지 않는 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 잘못된 널스페이스 제어 방법이 제공된 경우.
* **Returns:**
  컨트롤러가 계산한 조인트 힘. (`num_envs`, `num_DoF`) 형태의 텐서입니다.
* **Return type:**
  Tensor

### *class* isaaclab.controllers.OperationalSpaceControllerCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

운영공간 컨트롤러의 구성입니다.

**Attributes:**

| [`target_types`](#isaaclab.controllers.OperationalSpaceControllerCfg.target_types)                                                 | 작업공간 대상의 유형입니다.                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| [`motion_control_axes_task`](#isaaclab.controllers.OperationalSpaceControllerCfg.motion_control_axes_task)                         | 작업 기준 프레임에서 제어할 움직임 방향입니다.                                                                                          |
| [`contact_wrench_control_axes_task`](#isaaclab.controllers.OperationalSpaceControllerCfg.contact_wrench_control_axes_task)         | 작업 기준 프레임에서 제어할 접촉 렌치 방향입니다.                                                                                  |
| [`inertial_dynamics_decoupling`](#isaaclab.controllers.OperationalSpaceControllerCfg.inertial_dynamics_decoupling)                 | 움직임 제어(역동학)에 대한 관성 역동학 분리 여부를 나타냅니다.                                                        |
| [`partial_inertial_dynamics_decoupling`](#isaaclab.controllers.OperationalSpaceControllerCfg.partial_inertial_dynamics_decoupling) | 변환 및 회전 운동 사이의 관성 결합을 무시할지 여부를 나타냅니다.                                                       |
| [`gravity_compensation`](#isaaclab.controllers.OperationalSpaceControllerCfg.gravity_compensation)                                 | 중력 보상을 수행할지 여부를 나타냅니다.                                                                                                      |
| [`impedance_mode`](#isaaclab.controllers.OperationalSpaceControllerCfg.impedance_mode)                                             | `"fixed"`, `"variable"`, `"variable_kp"` 중 하나입니다.                                                                                                     |
| [`motion_stiffness_task`](#isaaclab.controllers.OperationalSpaceControllerCfg.motion_stiffness_task)                               | 작업공간 포즈 오류를 기반으로 운영공간 명령 힘을 결정하는 위치 이득입니다.                                          |
| [`motion_damping_ratio_task`](#isaaclab.controllers.OperationalSpaceControllerCfg.motion_damping_ratio_task)                       | 작업공간 속도 오류를 기반으로 운영공간 명령 힘을 계산할 때 위치 이득과 결합하여 사용되는 감쇠 비율입니다. |
| [`motion_stiffness_limits_task`](#isaaclab.controllers.OperationalSpaceControllerCfg.motion_stiffness_limits_task)                 | 위치 이득의 최소값과 최대값입니다.                                                                                              |
| [`motion_damping_ratio_limits_task`](#isaaclab.controllers.OperationalSpaceControllerCfg.motion_damping_ratio_limits_task)         | 속도 이득을 계산하는 데 사용되는 감쇠 비율의 최소값과 최대값입니다.                                                                 |
| [`contact_wrench_stiffness_task`](#isaaclab.controllers.OperationalSpaceControllerCfg.contact_wrench_stiffness_task)               | 폐루프 접촉 힘 제어를 위한 운영공간 명령 힘을 결정하는 비례 이득입니다.                                 |
| [`nullspace_control`](#isaaclab.controllers.OperationalSpaceControllerCfg.nullspace_control)                                       | `"none"` 또는 `"position"` 중 하나입니다.                                                                                                                       |
| [`nullspace_stiffness`](#isaaclab.controllers.OperationalSpaceControllerCfg.nullspace_stiffness)                                   | 널스페이스 제어의 강성입니다.                                                                                                         |
| [`nullspace_damping_ratio`](#isaaclab.controllers.OperationalSpaceControllerCfg.nullspace_damping_ratio)                           | 널스페이스 제어의 감쇠 비율입니다.                                                                                                     |

#### target_types *: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

작업공간 대상의 유형입니다.

두 개의 하위 문자열이 언더스코어로 연결되어 있습니다:
: - 작업공간 대상의 유형: `"pose"` 또는 `"wrench"`
  - 작업공간 대상의 기준: `"abs"` (절대적), `"rel"` (상대적, pose의 경우에만 해당)

#### motion_control_axes_task *: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)]*

작업 기준 프레임에서 제어할 움직임 방향입니다. 각 축에 대해 `0/1`로 표시합니다.

#### contact_wrench_control_axes_task *: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)]*

작업 기준 프레임에서 제어할 접촉 렌치 방향입니다. 각 축에 대해 0/1로 표시합니다.

#### inertial_dynamics_decoupling *: [bool](https://docs.python.org/3/library/functions.html#bool)*

움직임 제어(역동학)에 대한 관성 역동학 분리를 수행할지 여부를 나타냅니다.

#### partial_inertial_dynamics_decoupling *: [bool](https://docs.python.org/3/library/functions.html#bool)*

변환 및 회전 운동 사이의 관성 결합을 무시할지 여부를 나타냅니다.

#### gravity_compensation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

중력 보상을 수행할지 여부를 나타냅니다.

#### impedance_mode *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

`"fixed"`, `"variable"`, `"variable_kp"` 중 하나입니다.

* **Type:**
  움직임 제어에 사용되는 게인의 유형입니다.

#### motion_stiffness_task *: [float](https://docs.python.org/3/library/functions.html#float) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]*

작업공간 포즈 오류를 기반으로 운영공간 명령 힘을 결정하는 위치 이득입니다.

#### motion_damping_ratio_task *: [float](https://docs.python.org/3/library/functions.html#float) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]*

작업공간 속도 오류를 기반으로 운영공간 명령 힘을 계산할 때 위치 이득과 결합하여 사용되는 감쇠 비율입니다.

다음과 같은 수학적 연산이 속도 이득 계산에 수행됩니다:
: $d_gains = 2 * \\sqrt{p_gains} * damping_ratio$.

#### motion_stiffness_limits_task *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

위치 이득의 최소값과 최대값입니다.

참고: 이 값은 [`impedance_mode`](#isaaclab.controllers.OperationalSpaceControllerCfg.impedance_mode)가 `"variable"` 또는 `"variable_kp"`인 경우에만 사용됩니다.

#### motion_damping_ratio_limits_task *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

속도 이득을 계산하는 데 사용되는 감쇠 비율의 최소값과 최대값입니다.

참고: 이 값은 [`impedance_mode`](#isaaclab.controllers.OperationalSpaceControllerCfg.impedance_mode)가 `"variable"`인 경우에만 사용됩니다.

#### contact_wrench_stiffness_task *: [float](https://docs.python.org/3/library/functions.html#float) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

폐루프 접촉 힘 제어를 위한 운영공간 명령 힘을 결정하는 비례 이득입니다.

`None`인 경우, 원하는 접촉 렌치에 대한 개방루프 제어가 수행됩니다.

참고: 현재 moment에서 선형 힘만 측정할 수 있으므로 피드백 루프에는 처음 세 개의 요소만 사용됩니다.

#### nullspace_control *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

`"none"` 또는 `"position"` 중 하나입니다.

참고: `"position"`은 기본적으로 중복 조작기를 영 구성으로 driving하기 위해 사용됩니다. `target_joint_pos`가 `compute()` 메서드에 제공된 경우, 이 구성으로 driving됩니다.

* **Type:**
  중복 조작기의 널스페이스 제어 방법입니다.

#### nullspace_stiffness *: [float](https://docs.python.org/3/library/functions.html#float)*

널 공간 제어의 강성입니다.

#### nullspace_damping_ratio *: [float](https://docs.python.org/3/library/functions.html#float)*

널 공간 제어의 감쇠 비율입니다.

## Pink IK 컨트롤러

Pink IK 컨트롤러 패키지(IsaacLab용).

이 패키지는 Pink 역운동학 솔버와 IsaacLab 간의 통합을 제공합니다.

### *class* isaaclab.controllers.pink_ik.PinkIKController

기반: [`object`](https://docs.python.org/3/library/functions.html#object)

Isaac Lab과 함께 작동하는 Pink IK 컨트롤러의 통합입니다.

Pink IK 컨트롤러는 가중 작업을 통해 차분 역운동학을 해결합니다. 각 작업은 0으로 수렴하도록 구동되는 잔차 함수 e(q)로 정의됩니다(예: 엔드 이펙터 위치 지정의 경우 e(q) = p_target - p_ee(q)). 컨트롤러는 J_e(q)v = -αe(q)를 만족하는 관절 속도 v를 계산합니다. 여기서 J_e(q)는 작업 자코비안입니다. 여러 작업은 가중 최적화를 통해 해결되며, 이는 관절 속도 한계를 준수하면서 가중 작업 오차를 최소화하는 이차 프로그램을 구성합니다.

사용자 정의 작업을 지원하며, 원하는 관절 구성을 유지하기 위한 NullSpacePostureTask를 제공했습니다.

참고:
: Pink IK 솔버: [https://github.com/stephane-caron/pink](https://github.com/stephane-caron/pink)

**메서드:**

| [`__init__`](#isaaclab.controllers.pink_ik.PinkIKController.__init__)(cfg, robot_cfg, device, ...)                                  | Pink IK 컨트롤러를 초기화합니다.                                   |
|-------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [`update_null_space_joint_targets`](#isaaclab.controllers.pink_ik.PinkIKController.update_null_space_joint_targets)(curr_joint_pos) | null 공간 관절 목표를 업데이트합니다.                                 |
| [`compute`](#isaaclab.controllers.pink_ik.PinkIKController.compute)(curr_joint_pos, dt)                                             | 현재 상태 및 작업을 기반으로 목표 관절 위치를 계산합니다. |

#### \_\_init_\_(cfg: [PinkIKControllerCfg](#isaaclab.controllers.pink_ik.PinkIKControllerCfg), robot_cfg: [ArticulationCfg](isaaclab.assets.md#isaaclab.assets.ArticulationCfg), device: [str](https://docs.python.org/3/library/stdtypes.html#str), controlled_joint_indices: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)])

Pink IK 컨트롤러를 초기화합니다.

* **매개변수:**
  * **cfg** – 작업 정의, 솔버 파라미터 및 관절 구성을 포함하는 Pink IK 컨트롤러의 구성입니다.
  * **robot_cfg** – 초기 관절 위치 및 로봇 사양을 포함하는 로봇 어티큘레이션 구성입니다.
  * **device** – 계산을 수행할 장치(예: ‘cuda:0’, ‘cpu’)입니다.
  * **controlled_joint_indices** – Pink IK 컨트롤러에 의해 제어되는 USD 자산의 관절 인덱스 목록입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 구성에 joint_names 또는 all_joint_names이 제공되지 않은 경우 발생합니다.

#### update_null_space_joint_targets(curr_joint_pos: [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray))

null 공간 관절 목표를 업데이트합니다.

이 메서드는 현재 관절 구성에 기반하여 null 공간 자세 작업의 목표 관절 위치를 업데이트합니다. 이는 주요 작업이 중복을 허용할 때 원하는 관절 구성을 유지하는 데 유용합니다.

* **매개변수:**
  **curr_joint_pos** – 형태가 (num_joints,)인 현재 관절 위치입니다.

#### compute(curr_joint_pos: [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray), dt: [float](https://docs.python.org/3/library/functions.html#float)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

현재 상태 및 작업을 기반으로 목표 관절 위치를 계산합니다.

Pink 솔버를 사용하여 역운동학을 수행하여 정의된 작업을 충족하는 목표 관절 위치를 계산합니다. 솔버는 제약 조건을 준수하면서 작업 오차를 최소화하는 최적의 관절 속도를 찾기 위해 이차 프로그래밍을 사용합니다.

* **매개변수:**
  * **curr_joint_pos** – 형태가 (num_joints,)인 현재 관절 위치입니다.
  * **dt** – 관절 위치 변화를 계산하는 시간 단위(초)입니다.
* **반환값:**
  지정된 장치의 (num_joints,) 형태인 목표 관절 위치를 나타내는 텐서입니다.
  IK 솔버가 실패하는 경우, 안정성을 유지하기 위해 현재 관절 위치를 그대로 반환합니다.

### *class* isaaclab.controllers.pink_ik.PinkIKControllerCfg

기반: [`object`](https://docs.python.org/3/library/functions.html#object)

Pink IK 컨트롤러의 구성 설정입니다.

Pink IK 컨트롤러는 다음에서 찾을 수 있습니다: [https://github.com/stephane-caron/pink](https://github.com/stephane-caron/pink)

**속성:**

| [`urdf_path`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.urdf_path)                                         | 로봇의 URDF 파일 경로입니다.                                                |
|--------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [`mesh_path`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.mesh_path)                                         | 로봇과 관련된 메시 파일 경로입니다.                                           |
| [`num_hand_joints`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.num_hand_joints)                             | 로봇의 손 관절 수입니다.                                                     |
| [`variable_input_tasks`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.variable_input_tasks)                   | Pink IK 컨트롤러의 작업 목록입니다.                                           |
| [`fixed_input_tasks`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.fixed_input_tasks)                         | Pink IK 컨트롤러의 작업 목록입니다.                                           |
| [`joint_names`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.joint_names)                                     | Pink IK 컨트롤러에 의해 제어되는 USD 자산의 관절 이름 목록입니다.            |
| [`all_joint_names`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.all_joint_names)                             | USD 자산의 모든 관절 이름 목록입니다.                                       |
| [`articulation_name`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.articulation_name)                         | 장면 내의 USD 자산인 어티큘레이션의 이름입니다.                               |
| [`base_link_name`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.base_link_name)                               | USD 자산의 기본 링크 이름입니다.                                             |
| [`show_ik_warnings`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.show_ik_warnings)                           | IK 솔버가 솔루션을 찾지 못하는 경우 경고를 표시합니다.                           |
| [`fail_on_joint_limit_violation`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.fail_on_joint_limit_violation) | 관절 한계 위반 시 실패할지 여부를 결정합니다.                                     |
| [`xr_enabled`](#isaaclab.controllers.pink_ik.PinkIKControllerCfg.xr_enabled)                                       | True인 경우, Pink IK 컨트롤러가 XRVisualization에 정보를 전송합니다.         |

#### urdf_path *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

로봇의 URDF 파일 경로입니다. 이 파일은 Pinocchio의 `robot_wrapper.BuildFromURDF`에서 로봇 모델을 로드하는 데 사용됩니다.

#### mesh_path *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

로봇과 관련된 메시 파일 경로입니다. 이 파일들은 Pinocchio의 `robot_wrapper.BuildFromURDF`에서도 로드됩니다.

#### num_hand_joints *: [int](https://docs.python.org/3/library/functions.html#int)*

로봇의 손 관절 수입니다.

컨트롤러의 동작 공간은 `pose_dim(7) * num_controlled_frames + num_hand_joints`를 포함합니다. 마지막 `num_hand_joints` 값은 손 관절 각도입니다.

#### variable_input_tasks *: [list](https://docs.python.org/3/library/stdtypes.html#list)[pink.tasks.FrameTask]*

Pink IK 컨트롤러의 작업 목록입니다.

이 작업들은 환경의 동작에 의해 제어할 수 있습니다.

이 작업들은 프레임의 자세 또는 관절 각도를 제어하는 데 사용될 수 있습니다. 자세한 내용은 다음을 방문하세요: [https://github.com/stephane-caron/pink](https://github.com/stephane-caron/pink)

#### fixed_input_tasks *: [list](https://docs.python.org/3/library/stdtypes.html#list)[pink.tasks.FrameTask]*

Pink IK 컨트롤러의 작업 목록입니다. 이 작업들은 고정되어 환경의 동작에 의해 제어할 수 없습니다.

이 작업들은 원하는 구성으로 프레임의 자세 또는 관절 각도를 고정하는 데 사용될 수 있습니다. 자세한 내용은 다음을 방문하세요: [https://github.com/stephane-caron/pink](https://github.com/stephane-caron/pink)

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None)*

Pink IK 컨트롤러에 의해 제어되는 USD 자산의 관절 이름 목록입니다.

이 값은 USD와 URDF 파일의 관절 명명 규칙이 다르기 때문에 필요합니다. 이 값은 관리자 기반 환경의 액션 용어에 의해 자동으로 채워지도록 현재 설계되었습니다.

#### all_joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None)*

USD 자산의 모든 관절 이름 목록입니다.

이것은 USD와 URDF 파일의 조인트 명명 방식이 다르기 때문에 필수적입니다. 이 값은 관리자 기반 환경의 action term에 의해 자동으로 채워지도록 현재 설계되었습니다.

#### articulation_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

장면에서 USD 아티큘레이션 자산의 이름입니다.

#### base_link_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

USD 자산의 베이스 링크 이름입니다.

#### show_ik_warnings *: [bool](https://docs.python.org/3/library/functions.html#bool)*

IK 솔버가 해를 찾지 못할 경우 경고를 표시합니다.

#### fail_on_joint_limit_violation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

조인트 한계 위반 시 실패 여부를 지정합니다.

True인 경우, 최적화 중에 조인트 한계가 위반되면 Pink IK 솔버가 실패하고 오류를 발생시킵니다. PinkIKController는 마지막 조인트 위치로 설정하여 오류를 처리합니다.

False인 경우, 솔버는 조인트 한계 위반을 무시하고 찾은 가장 가까운 해를 반환합니다.

#### xr_enabled *: [bool](https://docs.python.org/3/library/functions.html#bool)*

True인 경우, Pink IK 컨트롤러가 XRVisualization에 정보를 전송합니다.

### 사용 가능한 Pink IK 작업

### *class* isaaclab.controllers.pink_ik.NullSpacePostureTask

다른 작업의 영영 공간(Null Space)에서 자세 목표를 추가하는 Pink 기반 작업입니다.

이 작업은 Pink 역기구학 프레임워크 내에서 더 높은 우선순위의 작업(보통 엔드 이펙터 자세 작업)의 영영 공간에서 자세 제어를 구현합니다.

**수학적 공식화:**

Pink 역기구학 최적화 공식화에 대한 자세한 내용은 다음을 참조하세요: [https://github.com/stephane-caron/pink](https://github.com/stephane-caron/pink)

**영영 공간 자세 작업 구현:**

이 작업은 두 가지 구성 요소로 이루어집니다:

1. **오류 함수**: 자세 오류는 다음과 같이 계산됩니다:

$$
\mathbf{e}(\mathbf{q}) = \mathbf{M} \cdot (\mathbf{q}^* - \mathbf{q})
$$

여기서:
: - $\mathbf{q}^*$는 목표 조인트 구성입니다
  - $\mathbf{q}$는 현재 조인트 구성입니다
  - $\mathbf{M}$은 조인트 선택 마스크 행렬입니다

1. **야코비안 행렬**: 작업 야코비안은 영영 공간 프로젝터입니다:

$$
\mathbf{J}_{\text{posture}}(\mathbf{q}) = \mathbf{N}(\mathbf{q}) =
    \mathbf{I} -\mathbf{J}_{\text{primary}}^+ \mathbf{J}_{\text{primary}}
$$

여기서:
: - $\mathbf{J}_{\text{primary}}$는 모든 높은 우선순위 작업의 결합 야코비안입니다
  - $\mathbf{J}_{\text{primary}}^+$는 주요 작업 야코비안의 유사역행렬입니다
  - $\mathbf{N}(\mathbf{q})$는 영영 공간 프로젝터 행렬입니다

예를 들어, 두 개의 프레임 작업(예: 두 엔드 이펙터의 자세 제어)이 있는 경우, 결합 야코비안 $\mathbf{J}_{\text{primary}}$는 각 프레임의 개별 야코비안을 수직으로 쌓아 구성됩니다:

$$
\mathbf{J}_{\text{primary}} =
\begin{bmatrix}
    \mathbf{J}_1(\mathbf{q}) \\
    \mathbf{J}_2(\mathbf{q})
\end{bmatrix}
$$

여기서 $\mathbf{J}_1(\mathbf{q})$와 $\mathbf{J}_2(\mathbf{q})$는 각각 첫 번째 및 두 번째 프레임 작업의 야코비안입니다.

영영 공간 프로젝터는 영영 공간의 joint 속도가 주요 작업에 대해 0 속도를 생성하도록 보장합니다: $\mathbf{J}_{\text{primary}} \cdot \dot{\mathbf{q}}_{\text{null}} = \mathbf{0}$.

**작업 통합:**

Pink 프레임워크에 통합될 때, 이 작업은 최적화에 다음과 같이 기여합니다:

$$
\left\|
    \mathbf{N}(\mathbf{q}) \mathbf{v} + \mathbf{M} \cdot (\mathbf{q}^* - \mathbf{q})
\right\|_{W_{\text{posture}}}^2
$$

이 공식화는 더 높은 우선순위의 작업(예: 엔드 이펙터 위치 지정)이 부과하는 제약을 존중하면서 로봇이 원하는 자세를 유지할 수 있도록 합니다.

**메서드:**

| [`__init__`](#isaaclab.controllers.pink_ik.NullSpacePostureTask.__init__)(cost[, lm_damping, gain, ...])   | 영영 공간 자세 작업을 초기화합니다.   |
|------------------------------------------------------------------------------------------------------------|-------------------------------------------|
| [`__new__`](#isaaclab.controllers.pink_ik.NullSpacePostureTask.__new__)(cls, \*args, \*\*kwargs)           |                                           |

#### \_\_init_\_(cost: [float](https://docs.python.org/3/library/functions.html#float), lm_damping: [float](https://docs.python.org/3/library/functions.html#float) = 0.0, gain: [float](https://docs.python.org/3/library/functions.html#float) = 1.0, controlled_frames: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None, controlled_joints: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [None](https://docs.python.org/3/library/constants.html#None)

영영 공간 자세 작업을 초기화합니다.

이 작업은 더 높은 우선순위의 프레임 작업의 영영 공간에서 원하는 조인트 자세를 유지합니다. 조인트 선택을 통해 특정 조인트(예: umanoïde 조작에서 손목 조인트)를 제외하여 어깨와 허리와 같은 중요한 조인트의 오류를 과도한 회전 범위로 압도하는 것을 방지할 수 있습니다.

* **매개변수:**
  * **cost** – 최적화 목표에서의 작업 가중치 요소입니다.
    단위: $[\text{cost}] / [\text{rad}]$.
  * **lm_damping** – 레벤버그-마arquardt 정규화 규모 (무차원). 기본값은 0.0입니다.
  * **gain** – 저역 통과 필터링을 위한 작업 이득 $\alpha \in [0, 1]$입니다.
    기본값은 1.0 (필터링 없음)입니다.
  * **controlled_frames** – 영영 공간 프로젝션을 위한 주요 작업을 정의하는 프레임 이름 목록입니다. None 또는 빈 목록인 경우, 프로젝션이 적용되지 않습니다.
  * **controlled_joints** – 자세 작업을 제어할 조인트 이름 목록입니다. None 또는 빈 목록인 경우, 모든 구동 조인트가 제어됩니다.

#### *static* \_\_new_\_(cls, \*args: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*\*kwargs: [Any](https://docs.python.org/3/library/typing.html#typing.Any)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)
