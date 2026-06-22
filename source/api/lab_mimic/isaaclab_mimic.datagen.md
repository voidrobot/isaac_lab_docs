# isaaclab_mimic.datagen

Isaac Lab Mimic의 핵심 구현 로직을 포함하는 서브 패키지입니다.

### 클래스

| [`DataGenerator`](#isaaclab_mimic.datagen.DataGenerator)                                               | 소스 데이터셋에서 새로운 궤적을 생성하는 메인 데이터 생성기 클래스입니다.                                                                                                |
|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`DatagenInfo`](#isaaclab_mimic.datagen.DatagenInfo)                                                   | 데이터 생성 프로세스에서 환경으로부터 필요한 정보의 구조를 정의합니다.                                                                                   |
| [`DataGenInfoPool`](#isaaclab_mimic.datagen.DataGenInfoPool)                                           | 데이터 생성을 위한 DatagenInfo 풀입니다.                                                                                                                                           |
| `SelectionStrategy`                                                                                    | 선택 전략을 구현하기 위한 메서드와 함수를 정의합니다.                                                                                                               |
| [`RandomStrategy`](#isaaclab_mimic.datagen.RandomStrategy)                                             | 소스 데모를 무작위로 선택합니다.                                                                                                                                                |
| [`NearestNeighborObjectStrategy`](#isaaclab_mimic.datagen.NearestNeighborObjectStrategy)               | 현재 장면의 객체 포즈와 가장 가까운 객체 포즈를 갖는 소스 데모를 선택합니다.                                                                           |
| [`NearestNeighborRobotDistanceStrategy`](#isaaclab_mimic.datagen.NearestNeighborRobotDistanceStrategy) | 현재 포즈에서 변환된 세그먼트의 첫 번째 포즈까지 로봇 엔드 이펙터가 이동해야 하는 거리를 최소화하는 소스 데모를 선택합니다. |
| [`Waypoint`](#isaaclab_mimic.datagen.Waypoint)                                                         | 해당 점에 대한 그립퍼 액추에이션과 함께 단일 6자유도 웨이포인트를 나타냅니다.                                                                             |
| [`WaypointSequence`](#isaaclab_mimic.datagen.WaypointSequence)                                         | Waypoint 객체의 시퀀스를 나타냅니다.                                                                                                                                         |
| [`WaypointTrajectory`](#isaaclab_mimic.datagen.WaypointTrajectory)                                     | 완전한 6자유도 궤적에 해당하는 WaypointSequence 객체의 시퀀스입니다.                                                                                                |

## 데이터 생성기

### *class* isaaclab_mimic.datagen.DataGenerator

소스 데이터셋에서 새로운 궤적을 생성하는 메인 데이터 생성기 클래스입니다.

데이터 생성기는 MimicGen에서 영감을 받아 몇 개의 인간 수집 소스 시연을 기반으로 새로운 데이터세트 생성을 가능하게 합니다.

데이터 생성기는 시연을 객체 중심의 하위 작업 세그먼트로 파싱하여 [`DataGenInfoPool`](#isaaclab_mimic.datagen.DataGenInfoPool)에 저장한 다음, 새로운 장면의 컨텍스트에 따라 각 세그먼트를 변환하여 하위 작업 세그먼트를 새로운 장면에 적용하고, 로봇 엔드 이펙터가 실행할 수 있는 일관된 궤적으로 결합합니다.

**메서드:**

| [`__init__`](#isaaclab_mimic.datagen.DataGenerator.__init__)(env[, src_demo_datagen_info_pool, ...])                    | * **param env:**<br/>  데이터 생성을 사용할 환경                        |
|-------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| [`randomize_subtask_boundaries`](#isaaclab_mimic.datagen.DataGenerator.randomize_subtask_boundaries)()                  | 작업 스펙에 따라 무작위 오프셋을 적용하여 하위 작업 경계를 샘플링합니다.        |
| [`select_source_demo`](#isaaclab_mimic.datagen.DataGenerator.select_source_demo)(eef_name, eef_pose, ...)               | 소스 하위 작업 세그먼트 선택을 실행하는 도우미 메서드입니다.                               |
| [`generate_eef_subtask_trajectory`](#isaaclab_mimic.datagen.DataGenerator.generate_eef_subtask_trajectory)(env_id, ...) | 엔드 이펙터의 단일 하위 작업에 대한 변환된 웨이포인트 궤적을 구축합니다.     |
| [`merge_eef_subtask_trajectory`](#isaaclab_mimic.datagen.DataGenerator.merge_eef_subtask_trajectory)(env_id, ...)       | 하위 작업 궤적을 로봇 엔드 이펙터를 위한 실행 가능한 궤적으로 병합합니다. |
| [`generate`](#isaaclab_mimic.datagen.DataGenerator.generate)(env_id, success_term[, ...])                               | 새로운 시연 생성을 시도합니다.                                             |

#### \_\_init_\_(env: [ManagerBasedRLMimicEnv](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLMimicEnv), src_demo_datagen_info_pool: [DataGenInfoPool](#isaaclab_mimic.datagen.DataGenInfoPool) | [None](https://docs.python.org/3/library/constants.html#None) = None, dataset_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, demo_keys: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

* **매개변수:**
  * **env** – 데이터 생성을 사용할 환경
  * **src_demo_datagen_info_pool** – 소스 데모 DatagenInfo 풀
  * **dataset_path** – 생성에 사용할 hdf5 데이터셋 경로
  * **demo_keys** – 파일에서 사용할 데모 키 목록. 제공되지 않은 경우,
    모든 데모 키가 사용됩니다.

#### randomize_subtask_boundaries() → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray)]

작업 스펙에 따라 무작위 오프셋을 적용하여 하위 작업 경계를 샘플링합니다.

각 시연이 하위 작업 세그먼트 세트로 분할된 것을 상기하고, 각 하위 작업의 끝 인덱스(그리고 skillgen이 활성화된 경우 시작 인덱스)에 무작위 오프셋을 적용할 수 있습니다.

#### select_source_demo(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), eef_pose: [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray), object_pose: [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray), src_demo_current_subtask_boundaries: [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray), subtask_object_name: [str](https://docs.python.org/3/library/stdtypes.html#str), selection_strategy_name: [str](https://docs.python.org/3/library/stdtypes.html#str), selection_strategy_kwargs: [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [int](https://docs.python.org/3/library/functions.html#int)

소스 하위 작업 세그먼트 선택을 실행하는 도우미 메서드입니다.

* **매개변수:**
  * **eef_name** – 엔드 이펙터의 이름
  * **eef_pose** – 현재 엔드 이펙터 포즈
  * **object_pose** – 이 하위 작업에 대한 현재 객체 포즈
  * **src_demo_current_subtask_boundaries** – 소스 시연에서의 하위 작업 세그먼트의 시작 및 끝 인덱스(shape (N, 2))
  * **subtask_object_name** – 이 하위 작업의 참조 객체 이름
  * **selection_strategy_name** – 선택 전략 이름
  * **selection_strategy_kwargs** – 선택 전략 실행에 사용할 추가 키워드 인수
* **반환값:**
  선택된 소스 데모 인덱스

#### generate_eef_subtask_trajectory(env_id: [int](https://docs.python.org/3/library/functions.html#int), eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), subtask_ind: [int](https://docs.python.org/3/library/functions.html#int), all_randomized_subtask_boundaries: [dict](https://docs.python.org/3/library/stdtypes.html#dict), runtime_subtask_constraints_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), selected_src_demo_inds: [dict](https://docs.python.org/3/library/stdtypes.html#dict)) → [WaypointTrajectory](#isaaclab_mimic.datagen.WaypointTrajectory)

엔드 이펙터의 단일 하위 작업에 대한 변환된 웨이포인트 궤적을 구축합니다.

이 메서드는 지정된 하위 작업에 대한 소스 데모 세그먼트를 선택하고, 무작위화된 하위 작업 경계를 사용하여 해당 EEF 포즈/타겟/그립퍼 액션을 슬라이스한 다음, 첫 번째 로봇 EEF 포즈를 선택적으로 미리 추가(로봇 포즈 대신 첫 번째 타겟에서 보간하기 위해)하고, 객체/조정 기반 변환을 포즈 시퀀스에 적용하여 결과를 WaypointTrajectory로 반환합니다.

선택 및 변환:

- 소스 데모 선택은 SubTaskConfig.selection_strategy(및 kwargs)와 datagen_config.generation_select_src_per_subtask / generation_select_src_per_arm에 의해 제어됩니다.
- 조정 제약 조건의 경우, 메서드는 동시 하위 작업에 걸쳐 선택된 소스 데모 ID를 재사용/설정하고, 동기화된 단계를 계산하며, 일관된 상대 이동을 보장하기 위해 사용된 포즈 변환을 저장합니다.
- 포즈 변환은 객체 포즈(object_ref)를 기준으로 계산되거나 델타 포즈를 기준으로 계산됩니다.
  동시 작업/조정 스킴을 통해 제공됩니다.

* **매개변수:**
  * **env_id** – 현재 로봇/객체 포즈를 쿼리하는 데 사용되는 환경 인덱스.
  * **eef_name** – 서브태스크 트랙터리가 생성되는 엔드-이펙터 키.
  * **subtask_ind** – `subtask_configs[eef_name]` 내의 서브태스크 인덱스.
  * **all_randomized_subtask_boundaries** – 각 엔드-이펙터에 대해 모든 서브태스크의 디모당 랜덤화된 (시작, 종료) 인덱스 배열.
  * **runtime_subtask_constraints_dict** – 제약 조건(예: 선택된 소스 ID, 델타 변환, 동기화 단계)의 런타임 필드를 전달하는 입력/출력 딕셔너리.
  * **selected_src_demo_inds** – 현재 선택된 소스 데모 인덱스에 대한 엔드-이펙터 매핑(구성된 경우 팔 간에 재사용될 수 있음).
* **반환값:**
  선택된 서브태스크 세그먼트에 대한 변환된 트랙터리.
* **반환 타입:**
  [WaypointTrajectory](#isaaclab_mimic.datagen.WaypointTrajectory)

#### merge_eef_subtask_trajectory(env_id: [int](https://docs.python.org/3/library/functions.html#int), eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), subtask_index: [int](https://docs.python.org/3/library/functions.html#int), prev_executed_traj: [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab_mimic.datagen.waypoint.Waypoint](#isaaclab_mimic.datagen.Waypoint)] | [None](https://docs.python.org/3/library/constants.html#None), subtask_trajectory: [WaypointTrajectory](#isaaclab_mimic.datagen.WaypointTrajectory)) → [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab_mimic.datagen.waypoint.Waypoint](#isaaclab_mimic.datagen.Waypoint)]

서브태스크 트랙터리를 로봇 엔드-이펙터에 대한 실행 가능한 트랙터리로 병합합니다.

먼저 초기 보간 세그먼트를 만든 다음, 제공된 subtask_trajectory를 이 위에 병합하여 새로운 WaypointTrajectory를 구성합니다.
초기 세그먼트는 구성된 경우 이전 서브태스크의 마지막 실행된 타겟 웨이포인트에서 시작하거나, 로봇의 현재 엔드-이펙터 포즈에서 시작합니다.

동작:

- datagen_config.generation_interpolate_from_last_target_pose이 True이고 첫 번째 서브태스크가 아닌 경우, 보간은 prev_executed_traj의 마지막 웨이포인트에서 시작됩니다.
- 그렇지 않은 경우, 보간은 현재 로봇 엔드-이펙터 포즈(환경에서 쿼리)에서 시작하며, 첫 번째 웨이포인트의 그립퍼 액션과 서브태스크의 액션 노이즈를 사용합니다.
- 병합은 해당 SubTaskConfig의 num_interpolation_steps, num_fixed_steps 및 선택적으로 apply_noise_during_interpolation을 사용합니다.
- 보간을 가능하게 하는 임시 초기 웨이포인트는 반환 전에 제거됩니다.

* **매개변수:**
  * **env_id** – 필요할 때 현재 로봇 엔드-이펙터 포즈를 쿼리하기 위한 환경 인덱스.
  * **eef_name** – 트랙터리가 병합되는 엔드-이펙터의 이름/키.
  * **subtask_index** – `subtask_configs[eef_name]` 내의 서브태스크 인덱스로, 보간 매개변수를 결정합니다.
  * **prev_executed_traj** – 마지막 타겟 웨이포인트에서 보간을 시드하는 데 사용되는 이전에 실행된 트랙터리. 보간-최종-타겟이 활성화되고 첫 번째 서브태스크가 아닌 경우 필요합니다.
  * **subtask_trajectory** – 초기 보간 세그먼트 뒤에 병합될 현재 서브태스크의 트랙터리 세그먼트.
* **반환값:**
  초기 보간 세그먼트 뒤에 따라오는 서브태스크 세그먼트를 포함한 전체 웨이포인트 시퀀스이며, 임시 초기 웨이포인트가 제거된 상태.

#### *async* generate(env_id: [int](https://docs.python.org/3/library/functions.html#int), success_term: [TerminationTermCfg](../lab/isaaclab.managers.md#isaaclab.managers.TerminationTermCfg), env_reset_queue: Queue | [None](https://docs.python.org/3/library/constants.html#None) = None, env_action_queue: Queue | [None](https://docs.python.org/3/library/constants.html#None) = None, pause_subtask: [bool](https://docs.python.org/3/library/functions.html#bool) = False, export_demo: [bool](https://docs.python.org/3/library/functions.html#bool) = True, motion_planner: [Any](https://docs.python.org/3/library/typing.html#typing.Any) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

새 시연을 생성하려고 시도합니다.

* **매개변수:**
  * **env_id** – 환경 ID
  * **success_term** – 작업이 성공했는지 확인하는 성공 함수
  * **env_reset_queue** – 재설정을 위한 환경 ID를 저장하는 큐
  * **env_action_queue** – 각 환경에 대한 작업을 저장하는 큐
  * **pause_subtask** – 서브태스크 생성을 일시 정지할지 여부
  * **export_demo** – 데모를 내보낼지 여부
  * **motion_planner** – 모션 계획에 사용할 모션 플래너
* **반환값:**
  - initial_state (dict): 실행된 트랙터리의 초기 시뮬레이터 상태
  - states (list): 각 타임스텝에서의 시뮬레이터 상태
  - observations (list): 각 타임스텝에서의 관찰 딕셔너리
  - datagen_infos (list): 각 타임스텝에서의 datagen_info
  - actions (np.array): 각 타임스텝에서 실행된 액션
  - success (bool): 트랙터리가 작업을 성공적으로 해결했는지 여부
  - src_demo_inds (list): 각 서브태스크에 대해 선택된 소스 데모 인덱스의 리스트
  - src_demo_labels (np.array): @src_demo_inds와 동일하지만, 트랙터리의 각 타임스텝에 레이블을 가질 수 있도록 반복된 배열.
* **반환 타입:**
  다음 항목을 포함하는 딕셔너리

## Datagen Info

### *class* isaaclab_mimic.datagen.DatagenInfo

데이터 생성 프로세스에서 환경으로부터 필요한 정보의 구조를 정의합니다.

[`DatagenInfo`](#isaaclab_mimic.datagen.DatagenInfo) 클래스는 데이터 생성에 필요한 모든 필수 데이터 요소를 한 곳에 집중시켜,
이 정보가 필요할 때마다 환경을 반복해서 쿼리하는 오버헤드와 복잡성을 줄입니다.

유연성을 위해 모든 정보가 존재할 필요는 없습니다.

핵심 요소:

- **eef_pose**: 로봇의 엔드-이펙터의 현재 6차원 포즈를 캡처합니다.
- **object_poses**: 장면에서 관련된 객체의 6차원 포즈를 캡처합니다.
- **subtask_start_signals**: 서브태스크 시작 신호를 캡처합니다. skillgen에서 시연에서 서브태스크의 정확한 시작을 식별하는 데 사용됩니다.
- **subtask_term_signals**: 서브태스크 완료 신호를 캡처합니다.
- **target_eef_pose**: 각 타임스텝에서 로봇 엔드-이펙터의 목표 6차원 포즈를 캡처합니다.
- **gripper_action**: 그립퍼의 상태를 캡처합니다.

**메서드:**

| [`__init__`](#isaaclab_mimic.datagen.DatagenInfo.__init__)([eef_pose, object_poses, ...])   | DatagenInfo 객체를 초기화합니다.   |
|---------------------------------------------------------------------------------------------|--------------------------------------|

#### \_\_init_\_(eef_pose=None, object_poses=None, subtask_term_signals=None, subtask_start_signals=None, target_eef_pose=None, gripper_action=None)

DatagenInfo 객체를 초기화합니다.

* **매개변수:**
  * **eef_pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) *or* *None*) – […, 4, 4] 형태의 로봇 엔드-이펙터 포즈
  * **object_poses** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict) *or* *None*) – 객체 이름을 객체 포즈에 매핑하는 딕셔너리이며, 형태는 […, 4, 4]
  * **subtask_start_signals** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict) *or* *None*) – 서브태스크 이름을 시작 여부를 나타내는 바이너리 지표(0 또는 1)에 매핑하는 딕셔너리. skillgen 사용 시 필수입니다. 딕셔너리의 각 값은 […, 1] 형태의 int, float, 또는 torch.Tensor일 수 있습니다.
  * **subtask_term_signals** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict) *or* *None*) – 서브태스크 이름을 완료 여부를 나타내는 바이너리 지표(0 또는 1)에 매핑하는 딕셔너리. 딕셔너리의 각 값은 […, 1] 형태의 int, float, 또는 torch.Tensor일 수 있습니다.
  * **target_eef_pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) *or* *None*) – […, 4, 4] 형태의 목표 엔드-이펙터 포즈
  * **gripper_action** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) *or* *None*) – […, D] 형태의 그립퍼 액션이며, D는 로봇 팔의 그립퍼 구동 액션의 차원

## Datagen Info Pool

### *class* isaaclab_mimic.datagen.DataGenInfoPool

데이터 생성을 위한 DatagenInfo 풀.

이 클래스는 에피소드에서 추출한 DatagenInfo 객체를 저장하는 컨테이너입니다.
풀은 asyncio 락을 사용하여 데이터를 소비하면서 새로운 에피소드를 안전하게 추가할 수 있도록 지원하므로,
여러 모방 데이터 생성기 간에 공유할 수 있습니다.

**메서드:**

| [`__init__`](#isaaclab_mimic.datagen.DataGenInfoPool.__init__)(env, env_cfg, device[, asyncio_lock])         | * **파라미터 env_cfg:**<br/>  환경 구성   |
|--------------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| [`add_episode`](#isaaclab_mimic.datagen.DataGenInfoPool.add_episode)(episode)                                | 주어진 에피소드에서 datagen 정보를 추가합니다.             |
| [`load_from_dataset_file`](#isaaclab_mimic.datagen.DataGenInfoPool.load_from_dataset_file)(file_path[, ...]) | 데이터셋 파일에서 로드합니다.                              |

**속성:**

| [`datagen_infos`](#isaaclab_mimic.datagen.DataGenInfoPool.datagen_infos)           | datagen 정보를 반환합니다.           |
|------------------------------------------------------------------------------------|--------------------------------------|
| [`subtask_boundaries`](#isaaclab_mimic.datagen.DataGenInfoPool.subtask_boundaries) | 서브태스크 경계를 반환합니다.      |
| [`asyncio_lock`](#isaaclab_mimic.datagen.DataGenInfoPool.asyncio_lock)             | asyncio 락을 반환합니다.            |
| [`num_datagen_infos`](#isaaclab_mimic.datagen.DataGenInfoPool.num_datagen_infos)   | datagen 정보의 수를 반환합니다.   |

#### \_\_init_\_(env, env_cfg, device, asyncio_lock: Lock | [None](https://docs.python.org/3/library/constants.html#None) = None)

* **Parameters:**
  * **env_cfg** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict)) – 환경 구성
  * **device** ([*torch.device*](https://docs.pytorch.org/docs/stable/tensor_attributes.html#torch.device)) – 데이터를 저장할 디바이스
  * **asyncio_lock** ([*asyncio.Lock*](https://docs.python.org/3/library/asyncio-sync.html#asyncio.Lock) *또는* *None*) – 스레드 안전성을 위해 사용할 asyncio 락

#### *property* datagen_infos

datagen 정보를 반환합니다.

#### *property* subtask_boundaries *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [list](https://docs.python.org/3/library/stdtypes.html#list)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int)]]]]*

서브태스크 경계를 반환합니다.

#### *property* asyncio_lock

asyncio 락을 반환합니다.

#### *property* num_datagen_infos

datagen 정보의 수를 반환합니다.

#### *async* add_episode(episode: EpisodeData)

주어진 에피소드에서 datagen 정보를 추가합니다.

* **Parameters:**
  **episode** (*EpisodeData*) – 추가할 에피소드

#### load_from_dataset_file(file_path, select_demo_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

데이터셋 파일에서 로드합니다.

* **Parameters:**
  * **file_path** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – 데이터셋 파일 경로
  * **select_demo_keys** ([*str*](https://docs.python.org/3/library/stdtypes.html#str) *또는* *None*) – 로드할 데모의 키

## Random Strategy

### *class* isaaclab_mimic.datagen.RandomStrategy

소스 데모를 무작위로 선택합니다.

**메서드:**

| [`select_source_demo`](#isaaclab_mimic.datagen.RandomStrategy.select_source_demo)(eef_pose, object_pose, ...)   | 현재 로봇 포즈, 현재 서브태스크에 대한 관련 객체 포즈, 현재 서브태스크의 소스 데모에서 관련 정보를 사용하여 소스 데모 인덱스를 선택합니다.   |
|-----------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`__init__`](#isaaclab_mimic.datagen.RandomStrategy.__init__)()                                                 |                                                                                                                                                                                                   |

#### select_source_demo(eef_pose, object_pose, src_subtask_datagen_infos)

현재 로봇 포즈, 현재 서브태스크에 대한 관련 객체 포즈, 소스 데모의 현재 서브태스크에서 관련 정보를 사용하여 소스 데모 인덱스를 선택합니다.

* **Parameters:**
  * **eef_pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 현재 4x4 엔드 이펙터 포즈
  * **object_pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 현재 4x4 객체 포즈, 이 서브태스크의 객체에 대한 것
  * **src_subtask_datagen_infos** ([*list*](https://docs.python.org/3/library/stdtypes.html#list)) – 소스 데모의 관련 서브태스크 세그먼트에 대한 DatagenInfo 인스턴스
* **Returns:**
  소스 데모 인덱스 - 사용할 소스 서브태스크 세그먼트를 나타냄
* **Return type:**
  source_demo_ind ([int](https://docs.python.org/3/library/functions.html#int))

#### \_\_init_\_()

## Nearest Neighbor Object Strategy

### *class* isaaclab_mimic.datagen.NearestNeighborObjectStrategy

현재 장면의 객체 포즈와 가장 가까운 객체 포즈를 가진 소스 데모를 선택합니다.

**메서드:**

| [`select_source_demo`](#isaaclab_mimic.datagen.NearestNeighborObjectStrategy.select_source_demo)(eef_pose, object_pose, ...)   | 현재 로봇 포즈, 현재 서브태스크에 대한 관련 객체 포즈, 현재 서브태스크의 소스 데모에서 관련 정보를 사용하여 소스 데모 인덱스를 선택합니다.   |
|--------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`__init__`](#isaaclab_mimic.datagen.NearestNeighborObjectStrategy.__init__)()                                                 |                                                                                                                                                                                                   |

#### select_source_demo(eef_pose, object_pose, src_subtask_datagen_infos, pos_weight=1.0, rot_weight=1.0, nn_k=3)

현재 로봇 포즈, 현재 서브태스크에 대한 관련 객체 포즈, 소스 데모의 현재 서브태스크에서 관련 정보를 사용하여 소스 데모 인덱스를 선택합니다.

* **Parameters:**
  * **eef_pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 현재 4x4 엔드 이펙터 포즈
  * **object_pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 현재 4x4 객체 포즈, 이 서브태스크의 객체에 대한 것
  * **src_subtask_datagen_infos** ([*list*](https://docs.python.org/3/library/stdtypes.html#list)) – 소스 데모의 관련 서브태스크 세그먼트에 대한 DatagenInfo 인스턴스
  * **pos_weight** ([*float*](https://docs.python.org/3/library/functions.html#float)) – 포즈 거리를 최소화하기 위한 위치에 대한 가중치
  * **rot_weight** ([*float*](https://docs.python.org/3/library/functions.html#float)) – 포즈 거리를 최소화하기 위한 회전에 대한 가중치
  * **nn_k** ([*int*](https://docs.python.org/3/library/functions.html#int)) – 상위 @nn_k개의 가장 가까운 이웃 중에서 소스 데모 인덱스를 균등하게 무작위로 선택
* **Returns:**
  소스 데모 인덱스 - 사용할 소스 서브태스크 세그먼트를 나타냄
* **Return type:**
  source_demo_ind ([int](https://docs.python.org/3/library/functions.html#int))

#### \_\_init_\_()

## Nearest Neighbor Robot Distance Strategy

### *class* isaaclab_mimic.datagen.NearestNeighborRobotDistanceStrategy

현재 포즈에서 첫 번째 포즈까지 로봇 엔드 이펙터가 이동해야 하는 거리를 최소화하는 소스 데모를 선택합니다.

**메서드:**

| [`select_source_demo`](#isaaclab_mimic.datagen.NearestNeighborRobotDistanceStrategy.select_source_demo)(eef_pose, object_pose, ...)   | 현재 로봇 포즈, 현재 서브태스크에 대한 관련 객체 포즈, 현재 서브태스크의 소스 데모에서 관련 정보를 사용하여 소스 데모 인덱스를 선택합니다.   |
|---------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`__init__`](#isaaclab_mimic.datagen.NearestNeighborRobotDistanceStrategy.__init__)()                                                 |                                                                                                                                                                                                   |

#### select_source_demo(eef_pose, object_pose, src_subtask_datagen_infos, pos_weight=1.0, rot_weight=1.0, nn_k=3)

현재 로봇 포즈, 현재 서브태스크에 대한 관련 객체 포즈, 소스 데모의 현재 서브태스크에서 관련 정보를 사용하여 소스 데모 인덱스를 선택합니다.

* **Parameters:**
  * **eef_pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 현재 4x4 엔드 이펙터 포즈
  * **object_pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 현재 4x4 객체 포즈, 이 서브태스크의 객체에 대한 것
  * **src_subtask_datagen_infos** ([*list*](https://docs.python.org/3/library/stdtypes.html#list)) – 소스 데모의 관련 서브태스크 세그먼트에 대한 DatagenInfo 인스턴스
  * **pos_weight** ([*float*](https://docs.python.org/3/library/functions.html#float)) – 포즈 거리를 최소화하기 위한 위치에 대한 가중치
  * **rot_weight** ([*float*](https://docs.python.org/3/library/functions.html#float)) – 포즈 거리를 최소화하기 위한 회전에 대한 가중치
  * **nn_k** ([*int*](https://docs.python.org/3/library/functions.html#int)) – 상위 @nn_k개의 가장 가까운 이웃 중에서 소스 데모 인덱스를 균등하게 무작위로 선택
* **Returns:**
  소스 데모 인덱스 - 사용할 소스 서브태스크 세그먼트를 나타냄
* **Return type:**
  source_demo_ind ([int](https://docs.python.org/3/library/functions.html#int))

#### \_\_init_\_()

## Waypoint

### *class* isaaclab_mimic.datagen.Waypoint

해당 포인트에 대한 그립퍼 액추에이션과 함께 단일 원하는 6-DoF 웨이포인트를 나타냅니다.

**메서드:**

| [`__init__`](#isaaclab_mimic.datagen.Waypoint.__init__)(pose, gripper_action[, noise])   | * **param pose:**<br/>  로봇 컨트롤러를 위한 4x4 포즈 대상   |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------|

#### \_\_init_\_(pose, gripper_action, noise=None)

* **Parameters:**
  * **pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 로봇 컨트롤러를 위한 4x4 자세 목표
  * **gripper_action** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 로봇 컨트롤러를 위한 그리퍼 동작
  * **noise** ([*float*](https://docs.python.org/3/library/functions.html#float) *or* *None*) – 현재 타임스텝에서 실행 중에 적용할 동작 노이즈 진폭
    (팔 동작에 적용되며, 그리퍼 동작에는 적용되지 않음)

## Waypoint 시퀀스

### *class* isaaclab_mimic.datagen.WaypointSequence

Waypoint 객체의 시퀀스를 나타냅니다.

**메서드:**

| [`__init__`](#isaaclab_mimic.datagen.WaypointSequence.__init__)([sequence])                               | * **파라미터 sequence:**<br/>  제공된 경우, Waypoint 객체의 리스트여야 함                       |
|-----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| [`from_poses`](#isaaclab_mimic.datagen.WaypointSequence.from_poses)(poses, gripper_actions, action_noise) | 포즈, 그리퍼 동작, 동작 노이즈 시퀀스를 주어 WaypointSequence 객체를 초기화합니다. |
| [`split`](#isaaclab_mimic.datagen.WaypointSequence.split)(ind)                                            | 이 시퀀스를 시간 인덱스 @ind까지의 부분과 restante로 두 개로 나눕니다.                   |

**속성:**

| [`last_waypoint`](#isaaclab_mimic.datagen.WaypointSequence.last_waypoint)   | 시퀀스의 마지막 Waypoint 반환.   |
|-----------------------------------------------------------------------------|-------------------------------------|

#### \_\_init_\_(sequence=None)

* **매개변수:**
  **sequence** ([*list*](https://docs.python.org/3/library/stdtypes.html#list) *or* *None*) – 제공된 경우, Waypoint 객체의 리스트여야 함

#### *classmethod* from_poses(poses, gripper_actions, action_noise)

포즈, 그리퍼 동작, 동작 노이즈 시퀀스를 주어 WaypointSequence 객체를 초기화합니다.

* **매개변수:**
  * **poses** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – (T, 4, 4) 형태의 포즈 행렬 시퀀스
  * **gripper_actions** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 각 타임스텝에 적용되어야 하는 그리퍼 동작 시퀀스, 형태 (T, D).
  * **action_noise** ([*float*](https://docs.python.org/3/library/functions.html#float) *or* [*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 각 타임스텝에 적용되어야 하는 동작 노이즈 진폭 시퀀스. 단일 float 값이 제공된 경우, 경로 전체에 걸쳐 노이즈 진폭이 일정하게 적용됨.

#### *property* last_waypoint

시퀀스의 마지막 Waypoint 반환.

* **반환값:**
  waypoint (Waypoint 인스턴스)

#### split(ind)

이 시퀀스를 시간 인덱스 @ind까지의 부분과 restante로 두 개로 나눕니다. 두 개의 WaypointSequence 객체를 반환합니다.

## Waypoint 궤적

### *class* isaaclab_mimic.datagen.WaypointTrajectory

전체 6자유도 궤적에 해당하는 WaypointSequence 객체의 시퀀스.

**메서드:**

| [`__init__`](#isaaclab_mimic.datagen.WaypointTrajectory.__init__)()                                                                    |                                                                      |
|----------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [`add_waypoint_sequence`](#isaaclab_mimic.datagen.WaypointTrajectory.add_waypoint_sequence)(sequence)                                  | 보간 없이 시퀀스를 목록에 직접 추가합니다.                           |
| [`add_waypoint_sequence_for_target_pose`](#isaaclab_mimic.datagen.WaypointTrajectory.add_waypoint_sequence_for_target_pose)(pose, ...) | 원하는 대상 포즈에 해당하는 새로운 waypoint 시퀀스를 추가합니다. |
| [`pop_first`](#isaaclab_mimic.datagen.WaypointTrajectory.pop_first)()                                                                  | 첫 번째 waypoint 시퀀스의 첫 번째 waypoint를 제거하고 반환합니다.    |
| [`merge`](#isaaclab_mimic.datagen.WaypointTrajectory.merge)(other[, num_steps_interp, ...])                                            | 이 궤적을 다른 궤적(@other)과 병합합니다.                         |
| [`get_full_sequence`](#isaaclab_mimic.datagen.WaypointTrajectory.get_full_sequence)()                                                  | 궤적의 모든 waypoint 시퀀스를 반환합니다.            |

**속성:**

| [`last_waypoint`](#isaaclab_mimic.datagen.WaypointTrajectory.last_waypoint)   | 시퀀스의 마지막 Waypoint 반환.   |
|-------------------------------------------------------------------------------|-------------------------------------|

#### \_\_init_\_()

#### *property* last_waypoint

시퀀스의 마지막 Waypoint 반환.

* **반환값:**
  waypoint (Waypoint 인스턴스)

#### add_waypoint_sequence(sequence)

보간 없이 시퀀스를 목록에 직접 추가합니다.

* **매개변수:**
  **sequence** (*WaypointSequence 인스턴스*) – 추가할 시퀀스

#### add_waypoint_sequence_for_target_pose(pose, gripper_action, num_steps, skip_interpolation=False, action_noise=0.0)

원하는 대상 포즈에 해당하는 새로운 waypoint 시퀀스를 추가합니다. 새로운 WaypointSequence가 구성되어 @num_steps 개의 중간 Waypoint 객체로 이루어집니다. 이러한 객체는 마지막 waypoint에서 대상 포즈까지 선형 보간을 통해 구성될 수 있음(기본값) 또는 @skip_interpolation을 True로 설정하여 대상 포즈를 고정하고 @num_steps번 반복할 수 있음.

* **매개변수:**
  * **pose** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 4x4 대상 포즈
  * **gripper_action** ([*torch.Tensor*](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) – 그리퍼 동작 값
  * **num_steps** ([*int*](https://docs.python.org/3/library/functions.html#int)) – 이 waypoint에 도달하기 위해 시도할 동작 단계 수. 마지막 포즈에서 대상 포즈까지 중간 선형 보간 점을 추가하여 총 단계 수가 @num_steps가 되도록 함.
  * **skip_interpolation** ([*bool*](https://docs.python.org/3/library/functions.html#bool)) – True인 경우, 대상 포즈를 고정하고 선형 보간 대신 @num_steps번 반복합니다.
  * **action_noise** ([*float*](https://docs.python.org/3/library/functions.html#float)) – 동작 실행 중에 추가할 가우시안 노이즈의 규모 (예: @execute가 호출될 때)

#### pop_first()

첫 번째 waypoint 시퀀스의 첫 번째 waypoint를 제거하고 반환합니다. 첫 번째 waypoint 시퀀스가 비어 있으면それも 함께 제거됩니다.

* **반환값:**
  waypoint (Waypoint 인스턴스)

#### merge(other, num_steps_interp=None, num_steps_fixed=None, action_noise=0.0)

이 궤적을 다른 궤적(@other)과 병합합니다.

* **매개변수:**
  * **other** (*WaypointTrajectory 객체*) – 이 궤적에 병합할 다른 궤적
  * **num_steps_interp** ([*int*](https://docs.python.org/3/library/functions.html#int) *or* *None*) – None이 아닌 경우, 현재 궤적의 끝과 @other의 시작 사이에서 보간하는 waypoint 시퀀스를 추가합니다.
  * **num_steps_fixed** ([*int*](https://docs.python.org/3/library/functions.html#int) *or* *None*) – None이 아닌 경우, @other의 첫 번째 대상 포즈에 해당하는 목표 포즈를 일정하게 갖는 waypoint 시퀀스를 추가합니다.
  * **action_noise** ([*float*](https://docs.python.org/3/library/functions.html#float)) – 보간 구간 동안 사용할 노이즈

#### get_full_sequence()

궤적의 모든 waypoint 시퀀스를 반환합니다.

* **반환값:**
  sequence (WaypointSequence 인스턴스)
