# isaaclab_mimic.envs

Isaac Lab Mimic용 환경 래퍼 서브 패키지입니다.

### 클래스

| [`isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv)                                                         | Franka 큐브 스택 IK 상대 환경용 Isaac Lab Mimic 환경 래퍼 클래스                     |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| [`isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg)                                           | Franka 큐브 스택 IK 상대 환경용 Isaac Lab Mimic 환경 설정 클래스                      |
| [`isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv)                                                         | Franka 큐브 스택 IK 절대 환경용 Isaac Lab Mimic 환경 래퍼 클래스                     |
| [`isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg)                                           | Franka 큐브 스택 IK 절대 환경용 Isaac Lab Mimic 환경 설정 클래스                      |
| [`isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv)                                             | Galbot 큐브 스택 RmpFlow 상대 환경용 Isaac Lab Mimic 환경 래퍼 클래스                |
| [`isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg)   | Galbot 그리퍼 큐브 스택 IK 상대 환경용 Isaac Lab Mimic 환경 설정 클래스              |
| [`isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg) | Galbot 흡입 그리퍼 큐브 스택 RmpFlow 상대 환경용 Isaac Lab Mimic 환경 설정 클래스    |
| [`isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv)                                             | Galbot 큐브 스택 RmpFlow 절대 환경용 Isaac Lab Mimic 환경 래퍼 클래스                |
| [`isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg)   | Galbot 그리퍼 큐브 스택 IK 상대 환경용 Isaac Lab Mimic 환경 설정 클래스              |
| [`isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg) | Galbot 흡입 그리퍼 큐브 스택 RmpFlow 절대 환경용 Isaac Lab Mimic 환경 설정 클래스    |
| [`isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv)                                                                                           | DiffIK / RmpFlow 상대 자세 제어 환경용 Isaac Lab Mimic 환경 래퍼 클래스               |
| [`isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv)                                                                                           | DiffIK / RmpFlow 절대 자세 제어 환경용 Isaac Lab Mimic 환경 래퍼 클래스               |
| [`isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg)                 | Agibot 직립 머그 환경용 Isaac Lab Mimic 환경 설정 클래스                              |
| [`isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg)                               | Agibot Toy2Box 배치 환경용 Isaac Lab Mimic 환경 설정 클래스                          |

## Franka 환경

### Franka 큐브 스택 IK 상대 Mimic 환경

### *class* isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv

Bases: [`ManagerBasedRLMimicEnv`](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLMimicEnv)

Franka 큐브 스택 IK 상대 환경용 Isaac Lab Mimic 환경 래퍼 클래스입니다.

**방법:**

| [`get_robot_eef_pose`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.get_robot_eef_pose)(eef_name[, env_ids])               | 현재 로봇 엔드 이펙터 포즈를 가져옵니다.                                                                                                                                       |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`target_eef_pose_to_action`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.target_eef_pose_to_action)(...[, ...])          | 엔드 이펙터 컨트롤러를 위한 목표 포즈와 그리퍼 액션을 입력으로 받아 해당 목표 포즈를 달성하려고 시도하는 액션(보통 정규화된 델타 포즈 액션)을 반환합니다. |
| [`action_to_target_eef_pose`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.action_to_target_eef_pose)(action)              | `env.step`과 호환되는 액션을 엔드 이펙터 컨트롤러용 목표 포즈로 변환합니다.                                                                               |
| [`actions_to_gripper_actions`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.actions_to_gripper_actions)(actions)           | `env.step`과 호환되는 환경 액션 시퀀스에서 그리퍼 actuation 부분을 추출합니다.                                                                             |
| [`get_subtask_term_signals`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.get_subtask_term_signals)([env_ids])             | 태스크의 각 서브태스크에 대한 종료 신호 플래그 딕셔너리를 가져옵니다.                                                                                                  |
| [`get_expected_attached_object`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.get_expected_attached_object)(eef_name, ...) | (SkillGen) 주어진 EEF/서브태스크에 대한 기대되는 연결된 객체를 반환합니다.                                                                                                  |
| [`__init__`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.__init__)(cfg[, render_mode])                                    | 환경을 초기화합니다.                                                                                                                                                |
| [`close`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.close)()                                                            | 환경을 정리합니다.                                                                                                                                               |
| [`export_IO_descriptors`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.export_IO_descriptors)([output_dir])                | 환경의 IO 설명자를 내보냅니다.                                                                                                                             |
| [`get_navigation_state`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.get_navigation_state)([env_ids])                     | 선택적 메서드입니다.                                                                                                                                                           |
| [`get_object_poses`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.get_object_poses)([env_ids])                             | Isaac Lab Mimic 데이터 생성에 관련된 현재 장면의 각 객체의 포즈를 가져옵니다.                                                                             |
| [`get_subtask_start_signals`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.get_subtask_start_signals)([env_ids])           | 태스크의 각 서브태스크에 대한 시작 신호 플래그 딕셔너리를 가져옵니다.                                                                                                        |
| [`get_wrapper_attr`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.get_wrapper_attr)(name)                                  | 환경에서 속성 이름을 가져옵니다.                                                                                                                              |
| [`has_wrapper_attr`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.has_wrapper_attr)(name)                                  | 환경에서 속성 이름이 존재하는지 확인합니다.                                                                                                                    |
| [`load_managers`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.load_managers)()                                            | 환경에 대한 관리자를 로드합니다.                                                                                                                                     |
| [`render`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.render)([recompute])                                               | 물리 시뮬레이션 단계를 거치지 않고 렌더링을 실행합니다.                                                                                                                        |
| [`reset`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.reset)([seed, env_ids, options])                                    | 지정된 환경을 재설정하고 관측값을 반환합니다.                                                                                                                |
| [`reset_to`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.reset_to)(state, env_ids[, seed, is_relative])                   | 제공된 상태로 지정된 환경을 재설정합니다.                                                                                                                          |
| [`seed`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.seed)([seed])                                                        | 환경의 시드를 설정합니다.                                                                                                                                          |
| [`serialize`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.serialize)()                                                    | 이 환경을 사전 형태로 재인스턴스화하는 데 필요한 모든 정보를 저장합니다.                                                                                            |
| [`set_wrapper_attr`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.set_wrapper_attr)(name, value, \*[, force])              | 환경에 `value` 값을 갖는 `name` 속성을 설정합니다. 자세한 내용은 `Wrapper.set_wrapper_attr`을 참조하세요.                                                                         |
| [`setup_manager_visualizers`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.setup_manager_visualizers)()                    | 관리자 항목에 대한 라이브 시각화 도구를 생성합니다.                                                                                                                                |
| [`step`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.step)(action)                                                        | 환경의 동역학에 대해 한 타임스텝을 실행하고 종료된 환경을 재설정합니다.                                                                                     |

**속성:**

| [`device`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.device)                             | 환경이 실행 중인 디바이스입니다.                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`get_IO_descriptors`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.get_IO_descriptors)     | 환경의 IO 서술자를 가져옵니다.                                                                           |
| [`is_vector_env`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.is_vector_env)               | 환경이 벡터화된 환경인지 여부를 나타냅니다.                                                                  |
| [`max_episode_length`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.max_episode_length)     | 환경 스텝 단위의 최대 에피소드 길이입니다.                                                                          |
| [`max_episode_length_s`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.max_episode_length_s) | 초 단위의 최대 에피소드 길이입니다.                                                                                    |
| [`metadata`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.metadata)                         | 환경의 메타데이터입니다.                                                                                         |
| [`np_random`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.np_random)                       | 설정되지 않은 경우 랜덤 시드로 초기화되는 환경의 내부 `_np_random`을 반환합니다.                   |
| [`np_random_seed`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.np_random_seed)             | 설정되지 않은 경우 먼저 랜덤 정수 시드로 초기화되는 환경의 내부 `_np_random_seed`을 반환합니다. |
| [`num_envs`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.num_envs)                         | 현재 실행 중인 환경 인스턴스의 수입니다.                                                          |
| [`physics_dt`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.physics_dt)                     | 물리 타임스텝(초 단위)입니다.                                                                                         |
| [`step_dt`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.step_dt)                           | 환경 스텝 타임스텝(초 단위)입니다.                                                                            |
| [`unwrapped`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.unwrapped)                       | 래핑되지 않은 기본 환경을 반환합니다.                                                                             |
| [`cfg`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.cfg)                                   | 환경에 대한 구성입니다.                                                                                    |

#### get_robot_eef_pose(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

로봇 엔드 이펙터의 현재 포즈를 가져옵니다. 로봇 엔드 이펙터 컨트롤러에서 사용하는 프레임과 동일해야 합니다.

* **매개변수:**
  * **eef_name** – 엔드 이펙터의 이름입니다.
  * **env_ids** – 포즈를 가져올 환경 인덱스입니다. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  길이가 `len(env_ids)`이고 모양이 `(len(env_ids), 4, 4)`인 엔드 이펙터 포즈 행렬을 나타내는 torch.Tensor입니다.

#### target_eef_pose_to_action(target_eef_pose_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), gripper_action_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), action_noise_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_id: [int](https://docs.python.org/3/library/functions.html#int) = 0) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

엔드 이펙터 컨트롤러에 대한 대상 포즈와 그리퍼 동작을 받아 해당 대상 포즈를 달성하기 위해 시도하는 동작(보통 정규화된 델타 포즈 동작)을 반환합니다.
지정된 경우 노이즈가 대상 포즈 동작에 추가됩니다.

* **매개변수:**
  * **target_eef_pose_dict** – 각 엔드 이펙터에 대한 4x4 대상 엔드 이펙터 포즈 사전입니다.
  * **gripper_action_dict** – 각 엔드 이펙터에 대한 그리퍼 동작 사전입니다.
  * **noise** – 동작에 추가할 노이즈입니다. None인 경우 노이즈가 추가되지 않습니다.
  * **env_id** – 동작을 가져올 환경 인덱스입니다.
* **반환값:**
  `env.step()`과 호환되는 동작을 나타내는 torch.Tensor입니다.

#### action_to_target_eef_pose(action: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

`env.step()`과 호환되는 동작을 엔드 이펙터 컨트롤러용 대상 포즈로 변환합니다.
`@target_eef_pose_to_action`의 역함수이며, 보통 기록된 동작을 사용하여 시연 경로에서 대상 컨트롤러 포즈 시퀀스를 추론하는 데 사용됩니다.

* **매개변수:**
  **action** – 환경 동작입니다. 모양은 `(num_envs, action_dim)`입니다.
* **반환값:**
  `@action`에 해당하는 엔드 이펙터 포즈를 나타내는 torch.Tensor 사전입니다.

#### actions_to_gripper_actions(actions: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

환경 동작(env.step과 호환)의 시퀀스에서 그리퍼 구동 부분을 추출합니다.

* **매개변수:**
  **actions** – 환경 동작. 형태는 (num_envs, 데모의 단계 수, action_dim)입니다.
* **반환값:**
  EEF 이름을 키로 하는 토치 텐서 그리퍼 동작의 사전입니다.

#### get_subtask_term_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

작업의 각 서브태스크에 대한 종료 신호 플래그의 사전을 가져옵니다. 서브태스크가 완료된 경우 플래그는 1이고, 그렇지 않은 경우 0입니다. 데이터셋 주석 도구를 실행할 때 자동 서브태스크 종료 신호 주석을 활성화하려는 경우 이 메서드의 구현이 필요합니다. 수동 서브태스크 종료 신호 주석을 사용하려는 경우 이 메서드를 구현하지 않은 상태로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 종료 신호를 가져올 환경 인덱스. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  각 서브태스크에 대한 종료 신호 플래그(참 또는 거짓)의 사전.

#### get_expected_attached_object(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), subtask_index: [int](https://docs.python.org/3/library/functions.html#int), env_cfg) → [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)

(SkillGen) 주어진 EEF/서브태스크에 대해 예상되는 연결된 객체를 반환합니다.

‘스택’ 서브태스크는 앞의 ‘그립’ 서브태스크에서 잡은 객체를 놓는다고 가정합니다.
‘그립’(또는 기타)의 시작 시에는 None을 반환합니다.

#### \_\_init_\_(cfg: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성입니다.
  * **render_mode** – 환경의 렌더링 모드. 기본값은 None이며, 이는 `"human"`과 유사합니다.

#### close()

환경을 정리합니다.

#### *property* device

환경이 실행 중인 장치입니다.

#### export_IO_descriptors(output_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

환경의 IO 설명자를 내보냅니다.

* **매개변수:**
  **output_dir** – IO 설명자를 내보낼 디렉터리입니다.

#### *property* get_IO_descriptors

환경의 IO 설명자를 가져옵니다.

* **반환값:**
  그룹 이름을 키로 하고 IO 설명자를 값으로 하는 사전입니다.

#### get_navigation_state(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

선택적 메서드입니다. 네비게이션 컨트롤러 로코마니퓰레이션 데이터 생성을 사용하는 경우에만 필요합니다.

로봇의 네비게이션 상태를 가져옵니다. 네비게이션 컨트롤러 사용이 활성화된 경우 필수입니다. 네비게이션 상태에는 네비게이션 컨트롤러의 제어 하에 있는지 여부를 나타내는 부울 플래그 “is_navigating”과 네비게이션 목표에 도달했는지 여부를 나타내는 부울 플래그 “navigation_goal_reached”가 포함됩니다.

* **매개변수:**
  **env_ids** – 네비게이션 상태를 가져올 환경 인덱스. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  네비게이션 상태 플래그(참 또는 거짓)의 사전입니다.

#### get_object_poses(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

현재 장면에서 Isaac Lab Mimic 데이터 생성을 위한 관련 객체의 포즈를 가져옵니다.

* **매개변수:**
  **env_ids** – 포즈를 가져올 환경 인덱스. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  객체 이름을 키로 하고 객체 포즈 행렬(4x4 토치 텐서)을 값으로 하는 사전입니다.

#### get_subtask_start_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

작업의 각 서브태스크에 대한 시작 신호 플래그의 사전을 가져옵니다. 서브태스크가 시작된 경우 플래그는 1이고, 그렇지 않은 경우 0입니다. 데이터셋 주석 도구를 실행할 때 자동 서브태스크 시작 신호 주석을 활성화하려는 경우 이 메서드의 구현이 필요합니다. 수동 서브태스크 시작 신호 주석을 사용하려는 경우 이 메서드를 구현하지 않은 상태로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 시작 신호를 가져올 환경 인덱스. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  각 서브태스크에 대한 시작 신호 플래그(참 또는 거짓)의 사전입니다.

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 속성 이름을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 속성 이름이 존재하는지 확인합니다.

#### is_vector_env *: ClassVar[[bool](https://docs.python.org/3/library/functions.html#bool)]* *= True*

환경이 벡터화된 환경인지 여부입니다.

#### load_managers()

환경의 관리자를 로드합니다.

이 함수는 액션, 관찰, 이벤트 등으로 구성된 다양한 관리자를 생성하는 역할을 담당합니다. 관리자는 물리 핸들에 접근해야 하므로 시뮬레이터가 리셋된 후(즉, 처음 실행된 후에만) 생성할 수 있습니다.

#### NOTE
독립형 애플리케이션(파이썬에서 시뮬레이터를 실행하는 경우)의 경우, 클래스가 초기화될 때 이 함수가 자동으로 호출됩니다.

그러나 확장 모드의 경우, 사용자는 시뮬레이터가 리셋된 후 이 함수를 수동으로 호출해야 합니다. 이는 시뮬레이터가 `SimulationContext.reset_async()`를 호출할 때만 리셋되기 때문이며, 생성자에서는 비동기 함수를 호출할 수 없습니다.

#### *property* max_episode_length *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 단계에서의 최대 에피소드 길이입니다.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

초 단위의 최대 에피소드 길이입니다.

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터입니다.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

환경의 내부 `_np_random`을 반환하며, 설정되지 않은 경우 랜덤 시드로 초기화됩니다.

* **반환값:**
  np.random.Generator 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 내부 `_np_random_seed`를 반환하며, 설정되지 않은 경우 먼저 랜덤 정수로 시드를 초기화합니다.

[`np_random_seed`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.np_random_seed)가 [`reset()`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.reset) 또는 `set_np_random_through_seed()` 대신 직접 설정된 경우, 시드는 -1이 됩니다.

* **반환값:**
  현재 np_random의 시드 또는 rng의 시드를 알 수 없는 경우 -1
* **반환 유형:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

실행 중인 환경의 인스턴스 수입니다.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 타임스텝(초 단위)입니다.

이 값은 시뮬레이션이 발생하는 가장 낮은 시간 소수점 자리입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 단계를 거치지 않고 렌더링을 실행합니다.

관례에 따라 모드가 다음과 같은 경우:
- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않음. 일반적으로 인간 소비를 위해 사용됨.
- **rgb_array**: (x, y, 3) 형태의 numpy.ndarray를 반환하며, 이는 x-by-y 픽셀 이미지의 RGB 값을 나타내고 비디오로 변환하기에 적합함.

* **매개변수:**
  **recompute** – 시뮬레이터가 이미 장면을 렌더링한 경우라도 강제로 렌더링할지 여부. 기본값은 False입니다.
* **반환값:**
  모드가 “rgb_array”인 경우 numpy 배열 형태의 렌더링된 이미지. 그렇지 않은 경우 None을 반환합니다.
* **오류:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 모드가 “rgb_data”로 설정되었고 시뮬레이션 렌더링 모드가 이를 지원하지 않는 경우.
        이 경우 시뮬레이션 렌더링 모드를 `RenderMode.PARTIAL_RENDERING` 또는 `RenderMode.FULL_RENDERING`으로 설정해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정된 경우.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

지정된 환경들을 재설정하고 관찰값을 반환합니다.

이 함수는 지정된 환경들을 재설정하기 위해 `_reset_idx()` 함수를 호출합니다.
그러나 초기화 중에 발생한 절차적 지형 생성과 같은 특정 작업은 반복되지 않습니다.

* **매개변수:**
  * **seed** – 무작위화에 사용할 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **env_ids** – 재설정할 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 재설정됩니다.
  * **options** – 

    환경을 재설정하는 방법을 지정하는 추가 정보. 기본값은 None입니다.

    #### NOTE
    이 인자는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **반환값:**
  관측값과 추가 정보를 포함하는 튜플.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

제공된 상태로 지정된 환경을 재설정합니다.

이 함수는 환경을 제공된 상태로 재설정합니다. 상태는 장면 엔티티의 상태를 포함하는 딕셔너리입니다.
형식에 대해서는 `InteractiveScene.get_state()` 를 참조하십시오.

이 함수는 [`reset()`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv.reset) 함수와 다릅니다. 랜덤화 이벤트를 사용하는 대신 환경들을 특정 상태로 재설정하기 때문입니다.

* **매개변수:**
  * **state** – 지정된 환경을 재설정할 상태. 형식에 대해서는 `InteractiveScene.get_state()` 를 참조하십시오.
  * **env_ids** – 재설정할 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 재설정됩니다.
  * **seed** – 무작위화에 사용할 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **is_relative** – True로 설정된 경우, 상태는 환경 원점에 상대적인 것으로 간주됩니다. 기본값은 False입니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **매개변수:**
  **seed** – 난수 생성기의 시드. 기본값은 -1입니다.
* **반환값:**
  난수 생성기에 사용된 시드.

#### serialize()

이 환경을 사전으로 재인스턴스화하는 데 필요한 모든 정보를 저장합니다.
이는 @env_meta - HDF5 데이터세트에 저장된 환경 메타데이터와 동일하며, utils/env_utils.py에서 사용됩니다.

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 이름과 값으로 속성을 설정합니다. 자세한 내용은 Wrapper.set_wrapper_attr를 참조하십시오.

#### setup_manager_visualizers()

관리자 항목에 대한 라이브 시각화 도구를 생성합니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동역학을 한 타임스텝 실행하고 종료된 환경을 재설정합니다.

`ManagerBasedEnv.step` 클래스와 달리, 이 함수는 다음과 같은 작업을 수행합니다:

1. 동작 처리.
2. 물리 스텝 수행.
3. GUI가 활성화된 경우 렌더링 수행.
4. 환경 카운터 업데이트 및 보상과 종료 계산.
5. 종료된 환경 재설정.
6. 관측값 계산.
7. 관측값, 보상, 재설정(종료 및 잘림) 및 추가 정보 반환.

* **매개변수:**
  **action** – 환경에 적용할 동작. 형태는 (num_envs, action_dim)입니다.
* **반환값:**
  관측값, 보상, 재설정(종료 및 잘림), 추가 정보를 포함하는 튜플.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스텝 타임스텝(초 단위).

이 값은 환경이 앞으로 진행되는 타임스텝입니다.

#### *property* unwrapped *: [Env](https://gymnasium.farama.org/api/env/#gymnasium.Env)[ObsType, ActType]*

기본 래핑되지 않은 환경을 반환합니다.

* **반환값:**
  기본 래핑되지 않은 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인스턴스
* **반환 타입:**
  Env

#### cfg *: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg)*

환경 구성.

### Franka Cube Stack IK Rel Mimic Env Cfg

### *class* isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg

Bases: `FrankaCubeStackEnvCfg`, [`MimicEnvCfg`](../lab/isaaclab.envs.md#isaaclab.envs.MimicEnvCfg)

Isaac Lab Mimic 환경 구성 클래스: Franka Cube Stack IK Rel env용.

**메서드:**

| [`__init__`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.__init__)([datagen_config, subtask_configs, ...])   |    |
|---------------------------------------------------------------------------------------------------------------------------------------------------------|----|

**클래스:**

| [`ui_window_class_type`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.ui_window_class_type)   | alias of [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)   |
|-----------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|

**속성:**

| [`viewer`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.viewer)                                 | 뷰어 구성.                                                                       |
|-------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.sim)                                       | 물리 시뮬레이션 구성.                                                           |
| [`seed`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.seed)                                     | 난수 생성기의 시드.                                                   |
| [`decimation`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.decimation)                         | 정책 dt당 sim dt에서의 제어 액션 업데이트 횟수.                                    |
| [`scene`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.scene)                                   | 씬 설정.                                                                             |
| [`recorders`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.recorders)                           | 레코더 설정.                                                                          |
| [`observations`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.observations)                     | 관측 공간 설정.                                                                 |
| [`actions`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.actions)                               | 행동 공간 설정.                                                                      |
| [`events`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.events)                                 | 이벤트 설정.                                                                             |
| [`rerender_on_reset`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.rerender_on_reset)           | 적어도 한 환경이 재설정된 후에 렌더 단계가 다시 수행되는지 여부.     |
| [`num_rerenders_on_reset`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.num_rerenders_on_reset) | 재설정 후에 수행할 렌더 단계 수.                                              |
| [`wait_for_textures`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.wait_for_textures)           | 자산이 완전히 로드될 때까지 대기하려면 True, 그렇지 않으면 False.                           |
| [`xr`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.xr)                                         | XR 장치를 통해 환경 보기 및 상호작용에 대한 구성.        |
| [`teleop_devices`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.teleop_devices)                 | 텔레오퍼레이션 장치에 대한 구성.                                                    |
| [`export_io_descriptors`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.export_io_descriptors)   | 환경의 IO 디스크립터를 내보낼지 여부.                                   |
| [`log_dir`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.log_dir)                               | 실험 아티팩트 로깅 디렉터리.                                                 |
| [`is_finite_horizon`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.is_finite_horizon)           | 학습 작업이 에이전트에 대해 유한 또는 무한 гори즌 문제로 취급되는지 여부. |
| [`episode_length_s`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.episode_length_s)             | 에피소드 기간 (초 단위).                                                        |
| [`rewards`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.rewards)                               | 보상 설정.                                                                            |
| [`terminations`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.terminations)                     | 종료 조건 설정.                                                                       |
| [`curriculum`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.curriculum)                         | 커리큘럼 설정.                                                                        |
| [`commands`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.commands)                             | 명령 설정.                                                                           |

#### \_\_init_\_(datagen_config: ~isaaclab.envs.mimic_env_cfg.DataGenConfig = <factory>, subtask_configs: dict[str, list[isaaclab.envs.mimic_env_cfg.SubTaskConfig]] = <factory>, task_constraint_configs: list[isaaclab.envs.mimic_env_cfg.SubTaskConstraintConfig] = <factory>, mimic_recorder_config: ~isaaclab.managers.recorder_manager.RecorderManagerBaseCfg | None = <factory>, viewer: ~isaaclab.envs.common.ViewerCfg = <factory>, sim: ~isaaclab.sim.simulation_cfg.SimulationCfg = <factory>, ui_window_class_type: type | None = <class 'isaaclab.envs.ui.manager_based_rl_env_window.ManagerBasedRLEnvWindow'>, seed: int | None = <factory>, decimation: int = <factory>, scene: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObjectTableSceneCfg = <factory>, recorders: object = <factory>, observations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObservationsCfg = <factory>, actions: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ActionsCfg = <factory>, events: object = <factory>, rerender_on_reset: bool = <factory>, num_rerenders_on_reset: int = <factory>, wait_for_textures: bool = <factory>, xr: ~isaaclab.devices.openxr.xr_cfg.XrCfg = <factory>, teleop_devices: ~isaaclab.devices.device_base.DevicesCfg = <factory>, export_io_descriptors: bool = <factory>, log_dir: str | None = <factory>, is_finite_horizon: bool = <factory>, episode_length_s: float = <factory>, rewards: object = <factory>, terminations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.TerminationsCfg = <factory>, curriculum: object | None = <factory>, commands: object | None = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### ui_window_class_type

별칭: [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)

#### viewer *: [ViewerCfg](../lab/isaaclab.envs.md#isaaclab.envs.ViewerCfg)*

뷰어 구성. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](../lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성. 기본값은 SimulationCfg()입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 부분에서 설정됩니다. 이로 인해 환경 생성이 결정적이 되고 다양한 실행에서 유사한 동작을 보입니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

시뮬레이션 dt당 정책 dt당 제어 액션 업데이트 수.

예를 들어, 시뮬레이션 dt가 0.01s이고 정책 dt가 0.1s이면, 디시메이션은 10입니다. 이는 제어 액션이 10개의 시뮬레이션 스텝마다 업데이트된다는 것을 의미합니다.

#### scene *: ObjectTableSceneCfg*

장면 설정.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](../lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCls) 클래스를 참조하세요.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정. 기본값은 아무것도 기록하지 않음입니다.

자세한 내용은 [`isaaclab.managers.RecorderManager`](../lab/isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참조하세요.

#### observations *: ObservationsCfg*

관측 공간 설정.

자세한 내용은 [`isaaclab.managers.ObservationManager`](../lab/isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참조하세요.

#### actions *: ActionsCfg*

행동 공간 설정.

자세한 내용은 [`isaaclab.managers.ActionManager`](../lab/isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참조하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정. 기본값은 장면을 기본 상태로 재설정하는 기본 구성이며, 자세한 내용은 [`isaaclab.managers.EventManager`](../lab/isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참조하세요.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

적어도 한 환경이 재설정된 후에 렌더 단계가 다시 수행되는지 여부. 기본값은 False이며, 이는 재설정 후에 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 False이면, 재설정 후에 센서에서 수집한 데이터는 오래되어 시뮬레이션에서 재설정으로 인한 최신 상태를 반영하지 않습니다.
* 이 값이 True이면, 센서 데이터를 최신 재설정 상태로 업데이트하기 위해 추가 렌더 단계가 수행됩니다. 이는 성능에 영향을 미치며, 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### Deprecated
버전 2.3.1부터 지원 중단됨: 이 특성은 지원 중단되었으며 앞으로 제거됩니다. 대신 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.num_rerenders_on_reset)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정했을 때와 동일한 동작을 얻으려면, [`num_rerenders_on_reset`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env_cfg.FrankaCubeStackIKRelMimicEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하세요.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후에 수행할 렌더 단계 수. 기본값은 0이며, 이는 재설정 후에 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 0이면, 재설정 후에 렌더 단계가 수행되지 않으며, 센서에서 수집한 데이터는 오래되어 시뮬레이션에서 재설정으로 인한 최신 상태를 반영하지 않습니다.
* 이 값이 0보다 크면, 지정된 수의 추가 렌더 단계가 수행되어 센서 데이터를 최신 재설정 상태로 업데이트합니다. 이는 성능에 영향을 미치며, 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋이 완전히 로드될 때까지 기다리려면 True, 그렇지 않으면 False. 기본값은 True입니다.

#### xr *: XrCfg*

XR 디바이스를 통해 환경을 보고 상호작용하기 위한 구성.

#### teleop_devices *: DevicesCfg*

원격 조작 장치의 구성.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 디스크립터를 내보낼지 여부. 기본값은 False입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트 로깅 디렉터리. 기본값은 None이며, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

학습 작업이 에이전트에 대해 유한 horizon 또는 무한 horizon 문제로 처리되는지 여부.
기본값은 False이며, 이는 작업이 무한 horizon 문제로 처리됨을 의미합니다.

이 플래그는 유한 horizon 및 무한 horizon 작업의 미묘한 차이를 처리합니다:

* **유한 horizon**: 시간이 다되어도 에이전트가 페널티 또는 부트스트래핑 값을 필요로 하지 않습니다. 그러나 환경은 시간 제한에 도달한 후 에피소드를 여전히 종료해야 합니다.
* **무한 horizon**: 에이전트는 에피소드 종료 시 상태의 값을 부트스트래핑해야 합니다. 이는 에이전트에 시간 제한(또는 잘림) 신호를 보내어 수행되며, 이 신호가 부트스트래핑 계산을 트리거합니다.

True이면 환경이 유한 horizon 문제로 처리되고 타임아웃(또는 잘림) 신호가 에이전트에 전송되지 않습니다. False이면 환경이 무한 horizon 문제로 처리되고 타임아웃(또는 잘림) 신호가 에이전트에 전송됩니다.

#### NOTE
기본 `ManagerBasedRLEnv` 클래스는 이 플래그를 직접 사용하지 않습니다. 환경 래퍼에서 이 플래그를 사용하여 해당 학습 에이전트에 보낼 신호의 유형을 결정합니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 길이(초 단위).

 decimation 비율과 물리 시간 단계를 기반으로 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, decimation 비율이 10, 물리 시간 단계가 0.01, 에피소드 길이가 10초이면,
에피소드 길이(스텝 단위)는 100입니다.

#### rewards *: [object](https://docs.python.org/3/library/functions.html#object)*

보상 설정.

자세한 내용은 [`isaaclab.managers.RewardManager`](../lab/isaaclab.managers.md#isaaclab.managers.RewardManager) 클래스를 참조하세요.

#### terminations *: TerminationsCfg*

종료 설정.

자세한 내용은 [`isaaclab.managers.TerminationManager`](../lab/isaaclab.managers.md#isaaclab.managers.TerminationManager) 클래스를 참조하세요.

#### curriculum *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

커리큘럼 설정. 기본값은 None이며, 이 경우 커리큘럼이 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.CurriculumManager`](../lab/isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 참조하세요.

#### commands *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

명령 설정. 기본값은 None이며, 이 경우 명령이 생성되지 않습니다.

자세한 내용은 [`isaaclab.managers.CommandManager`](../lab/isaaclab.managers.md#isaaclab.managers.CommandManager) 클래스를 참조하세요.

### Franka Cube Stack IK Abs Mimic Env

### *class* isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv

Bases: [`ManagerBasedRLMimicEnv`](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLMimicEnv)

Franka Cube Stack IK Abs 환경을 위한 Isaac Lab Mimic 환경 래퍼 클래스.

**메서드:**

| [`get_robot_eef_pose`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.get_robot_eef_pose)(eef_name[, env_ids])                | 현재 로봇 엔드 이펙터 포즈를 가져옵니다.                                                               |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| [`target_eef_pose_to_action`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.target_eef_pose_to_action)(...[, noise, env_id]) | 대상 포즈를 액션으로 변환합니다.                                                                     |
| [`action_to_target_eef_pose`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.action_to_target_eef_pose)(action)               | 액션을 대상 포즈로 변환합니다.                                                                     |
| [`actions_to_gripper_actions`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.actions_to_gripper_actions)(actions)            | 그리퍼 액션을 추출합니다.                                                                           |
| [`get_subtask_term_signals`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.get_subtask_term_signals)([env_ids])              | 서브태스크 종료 신호를 가져옵니다.                                                                   |
| [`__init__`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.__init__)(cfg[, render_mode])                                     | 환경을 초기화합니다.                                                                        |
| [`close`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.close)()                                                             | 환경 정리.                                                                       |
| [`export_IO_descriptors`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.export_IO_descriptors)([output_dir])                 | 환경의 IO 디스크립터를 내보냅니다.                                                     |
| [`get_navigation_state`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.get_navigation_state)([env_ids])                      | 선택적 메서드.                                                                                   |
| [`get_object_poses`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.get_object_poses)([env_ids])                              | 현재 장면에서 Isaac Lab Mimic 데이터 생성과 관련된 각 객체의 포즈를 가져옵니다.     |
| [`get_subtask_start_signals`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.get_subtask_start_signals)([env_ids])            | 각 서브태스크의 시작 신호 플래그 사전을 가져옵니다.                                |
| [`get_wrapper_attr`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.get_wrapper_attr)(name)                                   | 환경에서 속성 이름을 가져옵니다.                                                      |
| [`has_wrapper_attr`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.has_wrapper_attr)(name)                                   | 환경에 속성 이름이 존재하는지 확인합니다.                                            |
| [`load_managers`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.load_managers)()                                             | 환경의 관리자를 로드합니다.                                                             |
| [`render`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.render)([recompute])                                                | 물리 단계를 거치지 않고 렌더링을 실행합니다.                                                |
| [`reset`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.reset)([seed, env_ids, options])                                     | 지정된 환경을 재설정하고 관측값을 반환합니다.                                        |
| [`reset_to`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.reset_to)(state, env_ids[, seed, is_relative])                    | 제공된 상태로 지정된 환경을 재설정합니다.                                                  |
| [`seed`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.seed)([seed])                                                         | 환경의 시드를 설정합니다.                                                                  |
| [`serialize`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.serialize)()                                                     | 이 환경을 다시 인스턴스화하기 위해 필요한 모든 정보를 딕셔너리에 저장합니다.                    |
| [`set_wrapper_attr`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.set_wrapper_attr)(name, value, \*[, force])               | 환경에 값과 함께 속성 이름을 설정합니다. 자세한 내용은 Wrapper.set_wrapper_attr를 참조하세요. |
| [`setup_manager_visualizers`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.setup_manager_visualizers)()                     | 관리자 항목에 대한 라이브 시각화 도구를 생성합니다.                                                        |
| [`step`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.step)(action)                                                         | 환경의 동역학에 대한 하나의 타임스텝을 실행하고 종료된 환경을 재설정합니다.             |

**속성:**

| [`device`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.device)                             | 환경이 실행 중인 디바이스입니다.                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`get_IO_descriptors`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.get_IO_descriptors)     | 환경의 IO 디스크립터를 가져옵니다.                                                                           |
| [`is_vector_env`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.is_vector_env)               | 환경이 벡터화된 환경인지 여부를 나타냅니다.                                                                  |
| [`max_episode_length`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.max_episode_length)     | 환경 단계 기준 최대 에피소드 길이입니다.                                                                          |
| [`max_episode_length_s`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.max_episode_length_s) | 초 단위 최대 에피소드 길이입니다.                                                                                    |
| [`metadata`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.metadata)                         | 환경의 메타데이터입니다.                                                                                         |
| [`np_random`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.np_random)                       | 환경의 내부 `_np_random`을 반환합니다. 설정되지 않은 경우 랜덤 시드로 초기화됩니다.                   |
| [`np_random_seed`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.np_random_seed)             | 환경의 내부 `_np_random_seed`을 반환합니다. 설정되지 않은 경우 먼저 랜덤 정수로 시드를 초기화합니다. |
| [`num_envs`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.num_envs)                         | 현재 실행 중인 환경 인스턴스의 수입니다.                                                          |
| [`physics_dt`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.physics_dt)                     | 물리 타임스텝(초 단위)입니다.                                                                                         |
| [`step_dt`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.step_dt)                           | 환경 스텝 타임스텝(초 단위)입니다.                                                                            |
| [`unwrapped`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.unwrapped)                       | 기본 래핑되지 않은 환경을 반환합니다.                                                                             |
| [`cfg`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.cfg)                                   | 환경의 구성입니다.                                                                                    |

#### get_robot_eef_pose(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

현재 로봇 엔드 이펙터 자세를 가져옵니다.

#### target_eef_pose_to_action(target_eef_pose_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), gripper_action_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), noise: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_id: [int](https://docs.python.org/3/library/functions.html#int) = 0) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

목표 자세를 동작으로 변환합니다.

이 메서드는 목표 엔드 이펙터 자세와 그리퍼 동작을 담은 사전을 하나의 동작 텐서로 변환하여 환경에서 사용할 수 있도록 합니다.

함수 동작:
1. 자세 사전에서 목표 위치와 회전을 추출합니다.
2. 그리퍼 동작을 엔드 이펙터에 대해 추출합니다.
3. 위치와 쿼터니언 회전을 결합하여 자세 동작을 생성합니다.
4. 탐색을 위해 선택적으로 자세 동작에 노이즈를 추가합니다.
5. 자세 동작과 그리퍼 동작을 결합하여 최종 동작 텐서를 만듭니다.

* **매개변수:**
  * **target_eef_pose_dict** – 목표 엔드 이펙터 자세(들)를 포함하는 사전으로, 키는 엔드 이펙터 이름이고 값은 자세 텐서입니다.
  * **gripper_action_dict** – 그리퍼 동작(들)를 포함하는 사전으로, 키는 엔드 이펙터 이름이고 값은 동작 텐서입니다.
  * **noise** – 탐색을 위해 자세 동작에 적용할 선택적 노이즈 크기입니다. 제공된 경우, 랜덤 노이즈가 생성되어 자세 동작에 추가됩니다.
  * **env_id** – 다중 환경 설정에서의 환경 ID로, 기본값은 0입니다.
* **반환값:**
  자세와 그리퍼 명령을 결합한 단일 동작 텐서입니다.
* **반환 타입:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### action_to_target_eef_pose(action: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

동작을 목표 자세로 변환합니다.

#### actions_to_gripper_actions(actions: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

그리퍼 동작을 추출합니다.

#### get_subtask_term_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

서브태스크 종료 신호를 가져옵니다.

#### \_\_init_\_(cfg: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성입니다.
  * **render_mode** – 환경의 렌더 모드입니다. 기본값은 None이며, 이는 `"human"`과 유사합니다.

#### close()

환경 정리를 수행합니다.

#### *property* device

환경이 실행 중인 디바이스입니다.

#### export_IO_descriptors(output_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

환경의 IO 디스크립터를 내보냅니다.

* **매개변수:**
  **output_dir** – IO 디스크립터를 내보낼 디렉터리입니다.

#### *property* get_IO_descriptors

환경의 IO 디스크립터를 가져옵니다.

* **반환값:**
  그룹 이름을 키로 하고 IO 디스크립터를 값으로 하는 사전입니다.

#### get_navigation_state(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

선택적 메서드입니다. 네비게이션 컨트롤러 로코맨퓰레이션 데이터 생성을 사용할 때만 필요합니다.

로봇의 네비게이션 상태를 가져옵니다. 네비게이션 컨트롤러 사용이 활성화된 경우 필요합니다. 네비게이션 상태에는 네비게이션 컨트롤러에 의해 로봇이 제어 중인지 여부를 나타내는 불리언 플래그 `"is_navigating"`과 네비게이션 목표가 도달되었는지 여부를 나타내는 불리언 플래그 `"navigation_goal_reached"`가 포함됩니다.

* **매개변수:**
  **env_ids** – 네비게이션 상태를 가져올 환경 인덱스입니다. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  네비게이션 상태 플래그(False 또는 True)를 포함하는 사전입니다.

#### get_object_poses(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

Isaac Lab Mimic 데이터 생성을 위한 현재 장면의 관련 객체 자세를 가져옵니다.

* **매개변수:**
  **env_ids** – 자세를 가져올 환경 인덱스입니다. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  객체 이름을 키로 하고 객체 자세 행렬(4x4 torch.Tensor)을 값으로 하는 사전입니다.

#### get_subtask_start_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

작업의 각 서브태스크에 대한 시작 신호 플래그 딕셔너리를 가져옵니다. 플래그는 서브태스크가 시작된 경우 1이고, 그렇지 않은 경우 0입니다. 데이터셋 주석 도구를 실행할 때 자동 서브태스크 시작 신호 주석을 활성화하려는 경우 이 메서드를 구현해야 합니다. 수동 서브태스크 시작 신호 주석을 사용하려는 경우 이 메서드를 구현하지 않은 채로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 시작 신호를 가져올 환경 인덱스. None이면 모든 환경을 고려합니다.
* **반환값:**
  각 서브태스크에 대한 시작 신호 플래그(False 또는 True)를 포함하는 딕셔너리.

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 속성 이름을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에서 속성 이름이 존재하는지 확인합니다.

#### is_vector_env *: ClassVar[[bool](https://docs.python.org/3/library/functions.html#bool)]* *= True*

환경이 벡터화된 환경인지 여부.

#### load_managers()

환경의 관리자를 로드합니다.

이 함수는 환경의 다양한 관리자(액션, 관찰, 이벤트 등)를 생성하는 역할을 담당합니다. 관리자는 물리 핸들에 대한 액세스가 필요하므로, 시뮬레이터가 리셋된 이후(즉, 처음 플레이된 이후)에만 생성할 수 있습니다.

#### 참고
독립형 애플리케이션(파이썬에서 시뮬레이터 실행 시)의 경우, 클래스가 초기화될 때 이 함수가 자동으로 호출됩니다.

그러나 확장 모드의 경우, 사용자는 시뮬레이터가 리셋된 후 이 함수를 수동으로 호출해야 합니다. 이는 시뮬레이터가 사용자가 `SimulationContext.reset_async()`를 호출할 때만 리셋되기 때문이며, 생성자에서는 비동기 함수를 호출할 수 없기 때문입니다.

#### *property* max_episode_length *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 단계에서의 최대 에피소드 길이.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

초 단위의 최대 에피소드 길이.

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

환경의 내부 `_np_random`을 반환하며, 설정되지 않은 경우 랜덤 시드로 초기화됩니다.

* **반환값:**
  np.random.Generator의 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 내부 `_np_random_seed`를 반환하며, 설정되지 않은 경우 랜덤 정수로 시드를 먼저 초기화합니다.

[`np_random_seed`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.np_random_seed)가 [`reset()`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.reset) 또는 `set_np_random_through_seed()` 대신 직접 설정된 경우, 시드는 -1이 됩니다.

* **반환값:**
  현재 np_random의 시드 또는 rng의 시드가 inconn인 경우 -1
* **반환 유형:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

실행 중인 환경 인스턴스의 수.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 타임스텝(초 단위).

시뮬레이션이 일어나는 가장 낮은 시간_decimation입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 단계를 거치지 않고 렌더링을 실행합니다.

관례에 따라, 모드가 다음과 같은 경우:

- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않음. 일반적으로 인간 소비용.
- **rgb_array**: (x, y, 3) 형태의 numpy.ndarray를 반환하며, x-by-y 픽셀 이미지의 RGB 값을 나타내고 동영상으로 변환하기에 적합함.

* **매개변수:**
  **recompute** – 시뮬레이터가 이미 장면을 렌더링했더라도 강제로 렌더링할지 여부. 기본값은 False입니다.
* **반환값:**
  모드가 “rgb_array”인 경우 numpy 배열 형태의 렌더링된 이미지. 그 외의 경우 None을 반환합니다.
* **예외 발생:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 모드가 “rgb_data”로 설정되었으나 시뮬레이터 렌더 모드가 이를 지원하지 않는 경우.
        이 경우 시뮬레이터 렌더 모드를 `RenderMode.PARTIAL_RENDERING` 또는 `RenderMode.FULL_RENDERING`으로 설정해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정된 경우.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

지정된 환경을 리셋하고 관찰값을 반환합니다.

이 함수는 지정된 환경을 리셋하기 위해 `_reset_idx()` 함수를 호출합니다. 그러나 초기화 중에 발생한 절차적 지형 생성과 같은 특정 작업은 반복되지 않습니다.

* **매개변수:**
  * **seed** – 무작위화에 사용할 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **env_ids** – 리셋할 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 리셋됩니다.
  * **options** – 

    환경 리셋 방법을 지정하는 추가 정보. 기본값은 None입니다.

    #### 참고
    이 인수는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **반환값:**
  관찰값과 추가 정보를 포함하는 튜플.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

제공된 상태로 지정된 환경을 리셋합니다.

이 함수는 제공된 상태로 환경을 리셋합니다. 상태는 장면 엔티티의 상태를 포함하는 딕셔너리입니다. 형식은 `InteractiveScene.get_state()`를 참조하십시오.

이 함수는 [`reset()`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv.reset) 함수와 다르게 작동하며, 랜덤화 이벤트를 사용해 환경을 리셋하는 대신 특정 상태로 환경을 리셋합니다.

* **매개변수:**
  * **state** – 지정된 환경을 리셋할 상태. 형식은 `InteractiveScene.get_state()`를 참조하십시오.
  * **env_ids** – 리셋할 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 리셋됩니다.
  * **seed** – 무작위화에 사용할 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **is_relative** – True로 설정된 경우, 상태는 환경 기원을 기준으로 상대적으로 간주됩니다. 기본값은 False입니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **매개변수:**
  **seed** – 랜덤 생성기의 시드. 기본값은 -1입니다.
* **반환값:**
  랜덤 생성기에 사용된 시드.

#### serialize()

이 환경을 다시 인스턴스화하는 데 필요한 모든 정보를 딕셔너리에 저장합니다.
이는 @env_meta와 동일하며, HDF5 데이터셋에 저장된 환경 메타데이터이고, utils/env_utils.py에서 사용됩니다.

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 지정된 이름의 속성을 값으로 설정합니다. 자세한 내용은 `Wrapper.set_wrapper_attr`를 참조하세요.

#### setup_manager_visualizers()

관리자 항목에 대한 실시간 시각화 도구를 생성합니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동력을 하나의 시간 스텝으로 실행하고 종료된 환경을 재설정합니다.

`ManagerBasedEnv.step` 클래스와 달리, 이 함수는 다음 작업을 수행합니다:

1. 행동 처리
2. 물리 스텝 수행
3. GUI가 활성화된 경우 렌더링 수행
4. 환경 카운터 업데이트 및 보상과 종료 조건 계산
5. 종료된 환경 재설정
6. 관측값 계산
7. 관측값, 보상, 재설정 플래그(종료 및 트렁케이트), 추가 정보를 반환

* **매개변수:**
  **action** – 환경에 적용할 행동. 형태는 (num_envs, action_dim).
* **반환값:**
  관측값, 보상, 재설정(종료 및 트렁케이트), 추가 정보를 포함하는 튜플.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경의 시간 스텝(초 단위).

이 값은 환경이 앞으로 진행되는 시간 간격입니다.

#### *property* unwrapped *: [Env](https://gymnasium.farama.org/api/env/#gymnasium.Env)[ObsType, ActType]*

랩핑되지 않은 기본 환경을 반환합니다.

* **반환값:**
  랩핑되지 않은 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인스턴스
* **반환 유형:**
  Env

#### cfg *: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg)*

환경 구성.

### Franka Cube Stack IK Abs Mimic Env Cfg

### *class* isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg

기반 클래스: `FrankaCubeStackEnvCfg`, [`MimicEnvCfg`](../lab/isaaclab.envs.md#isaaclab.envs.MimicEnvCfg)

Franka Cube Stack IK Abs 환경을 위한 Isaac Lab Mimic 환경 구성 클래스.

**메서드:**

| [`__init__`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.__init__)([datagen_config, subtask_configs, ...])   |    |
|---------------------------------------------------------------------------------------------------------------------------------------------------------|----|

**클래스:**

| [`ui_window_class_type`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.ui_window_class_type)   | [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭   |
|-----------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|

**속성:**

| [`viewer`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.viewer)                                 | 뷰어 구성.                                                                       |
|-------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.sim)                                       | 물리 시뮬레이션 구성.                                                           |
| [`seed`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.seed)                                     | 난수 생성기의 시드.                                                   |
| [`decimation`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.decimation)                         | 정책 dt당 sim dt에서의 제어 행동 업데이트 횟수.                                    |
| [`scene`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.scene)                                   | 씬 설정.                                                                             |
| [`recorders`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.recorders)                           | 레코더 설정.                                                                          |
| [`observations`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.observations)                     | 관측 공간 설정.                                                                     |
| [`actions`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.actions)                               | 행동 공간 설정.                                                                    |
| [`events`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.events)                                 | 이벤트 설정.                                                                         |
| [`rerender_on_reset`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.rerender_on_reset)           | 최소 하나의 환경이 재설정된 후 렌더링 단계를 다시 수행할지 여부.     |
| [`num_rerenders_on_reset`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.num_rerenders_on_reset) | 재설정 후 수행할 렌더링 단계 수.                                              |
| [`wait_for_textures`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.wait_for_textures)           | 에셋이 완전히 로드될 때까지 기다릴지 여부 (True: 대기, False: 대기하지 않음).                           |
| [`xr`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.xr)                                         | XR 장치를 통한 환경 시각화 및 상호작용 구성.        |
| [`teleop_devices`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.teleop_devices)                 | 원격 조작 장치 구성.                                                    |
| [`export_io_descriptors`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.export_io_descriptors)   | 환경의 IO 서술자를 내보낼지 여부.                                   |
| [`log_dir`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.log_dir)                               | 실험 아티팩트 로깅 디렉터리.                                                 |
| [`is_finite_horizon`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.is_finite_horizon)           | 학습 과제가 유한 또는 무한 호라이즌 문제로 간주되는지 여부. |
| [`episode_length_s`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.episode_length_s)             | 에피소드 지속 시간(초 단위).                                                        |
| [`rewards`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.rewards)                               | 보상 설정.                                                                            |
| [`terminations`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.terminations)                     | 종료 조건 설정.                                                                       |
| [`curriculum`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.curriculum)                         | 교육 과정 설정.                                                                        |
| [`commands`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.commands)                             | 명령 설정.                                                                           |

#### \_\_init_\_(datagen_config: ~isaaclab.envs.mimic_env_cfg.DataGenConfig = <factory>, subtask_configs: dict[str, list[isaaclab.envs.mimic_env_cfg.SubTaskConfig]] = <factory>, task_constraint_configs: list[isaaclab.envs.mimic_env_cfg.SubTaskConstraintConfig] = <factory>, mimic_recorder_config: ~isaaclab.managers.recorder_manager.RecorderManagerBaseCfg | None = <factory>, viewer: ~isaaclab.envs.common.ViewerCfg = <factory>, sim: ~isaaclab.sim.simulation_cfg.SimulationCfg = <factory>, ui_window_class_type: type | None = <class 'isaaclab.envs.ui.manager_based_rl_env_window.ManagerBasedRLEnvWindow'>, seed: int | None = <factory>, decimation: int = <factory>, scene: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObjectTableSceneCfg = <factory>, recorders: object = <factory>, observations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObservationsCfg = <factory>, actions: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ActionsCfg = <factory>, events: object = <factory>, rerender_on_reset: bool = <factory>, num_rerenders_on_reset: int = <factory>, wait_for_textures: bool = <factory>, xr: ~isaaclab.devices.openxr.xr_cfg.XrCfg = <factory>, teleop_devices: ~isaaclab.devices.device_base.DevicesCfg = <factory>, export_io_descriptors: bool = <factory>, log_dir: str | None = <factory>, is_finite_horizon: bool = <factory>, episode_length_s: float = <factory>, rewards: object = <factory>, terminations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.TerminationsCfg = <factory>, curriculum: object | None = <factory>, commands: object | None = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### ui_window_class_type

alias of [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)

#### viewer *: [ViewerCfg](../lab/isaaclab.envs.md#isaaclab.envs.ViewerCfg)*

뷰어 구성. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](../lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성. 기본값은 SimulationCfg()입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 시 설정됩니다. 이를 통해 환경 생성은 결정적이게 되고, 다양한 실행 간에 유사하게 동작합니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

정책 시간 단위당 시뮬레이션 시간 단위(시뮬레이션 dt)별 제어 행동 업데이트 횟수.

예를 들어, 시뮬레이션 dt가 0.01s이고 정책 dt가 0.1s인 경우, 디시메이션은 10입니다. 즉, 제어 행동은 시뮬레이션 단계마다 10번마다 업데이트됩니다.

#### scene *: ObjectTableSceneCfg*

씬 설정.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](../lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스를 참조하세요.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정. 기본값은 아무것도 기록하지 않는 것입니다.

자세한 내용은 [`isaaclab.managers.RecorderManager`](../lab/isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참조하세요.

#### observations *: ObservationsCfg*

관찰 공간 설정.

자세한 내용은 [`isaaclab.managers.ObservationManager`](../lab/isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참조하세요.

#### actions *: ActionsCfg*

작업 공간 설정.

자세한 내용은 [`isaaclab.managers.ActionManager`](../lab/isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참조하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정. 기본값은 씬을 기본 상태로 재설정하는 기본 구성입니다.

자세한 내용은 [`isaaclab.managers.EventManager`](../lab/isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참조하세요.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

하나 이상의 환경이 재설정된 후 렌더 단계가 다시 수행되는지 여부입니다. 기본값은 False이며, 이는 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* False일 때, 재설정 후 센서에서 수집된 데이터는 오래되고, 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* True일 때, 추가 렌더 단계가 수행되어 센서 데이터가 재설정으로 인한 최신 상태를 반영하도록 업데이트됩니다. 이는 추가 렌더 단계가 각 환경 재설정 후에 수행되므로 성능에 비용이 발생합니다.

#### Deprecated
2.3.1 버전부터 사용되지 않음: 이 특성은 사용되지 않으며 향후 제거될 예정입니다. 대신 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.num_rerenders_on_reset)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정했을 때와 동일한 동작을 얻으려면 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env_cfg.FrankaCubeStackIKAbsMimicEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하세요.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후 수행할 렌더 단계 수. 기본값은 0이며, 이는 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 0일 때, 재설정 후 렌더 단계가 수행되지 않습니다. 재설정 후 센서에서 수집된 데이터는 오래되고, 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 0보다 클 때, 지정된 횟수의 추가 렌더 단계가 수행되어 센서 데이터가 재설정으로 인한 최신 상태를 반영하도록 업데이트됩니다. 이는 추가 렌더 단계가 각 환경 재설정 후에 수행되므로 성능에 비용이 발생합니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 완전히 로드될 때까지 대기하려면 True, 그렇지 않으면 False. 기본값은 True입니다.

#### xr *: XrCfg*

XR 기기를 통해 환경 보기 및 상호 작용을 위한 구성.

#### teleop_devices *: DevicesCfg*

텔레오퍼레이션 장치를 위한 구성.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 디스크립터를 내보내는지 여부. 기본값은 False입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 위한 로깅 디렉토리. 기본값은 None이며, 이 경우 특정 로그 디렉토리가 설정되지 않습니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

학습 작업이 에이전트에 대해 유한 또는 무한 시간 지평선 문제로 간주되는지 여부입니다. 기본값은 False이며, 이는 작업이 무한 시간 지평선 문제로 처리됨을 의미합니다.

이 플래그는 유한 및 무한 시간 지평선 작업의 미묘함을 처리합니다:

* **유한 시간 지평선**: 시간이 다했을 때 에이전트가 페널티 또는 부트스트래핑 값을 필요로 하지 않습니다. 그러나 환경은 시간 제한이 도달된 후 에피소드를 여전히 종료해야 합니다.
* **무한 시간 지평선**: 에이전트는 에피소드 끝의 상태 값에 대해 부트스트래핑을 수행해야 합니다. 이는 시간 제한(또는 잘림) 완료 신호를 에이전트에 전송하여 이 부트스트래핑 계산을 트리거함으로써 수행됩니다.

True이면, 환경은 유한 시간 지평선 문제로 처리되며 타임아웃(또는 잘림) 완료 신호가 에이전트에 전송되지 않습니다. False이면, 환경은 무한 시간 지평선 문제로 처리되며 타임아웃(또는 잘림) 완료 신호가 에이전트에 전송됩니다.

#### NOTE
기본 `ManagerBasedRLEnv` 클래스는 이 플래그를 직접 사용하지 않습니다. 이 플래그는 환경 래퍼에서 어떤 유형의 완료 신호를 해당 학습 에이전트에 보낼지 결정하기 위해 사용됩니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 지속 시간(초 단위).

디시메이션 비율과 물리 시간 단위를 기반으로, 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디시메이션 비율이 10, 물리 시간 단위가 0.01, 에피소드 길이가 10초인 경우, 에피소드 길이는 단계 단위로 100이 됩니다.

#### rewards *: [object](https://docs.python.org/3/library/functions.html#object)*

보상 설정.

자세한 내용은 [`isaaclab.managers.RewardManager`](../lab/isaaclab.managers.md#isaaclab.managers.RewardManager) 클래스를 참조하세요.

#### terminations *: TerminationsCfg*

종료 설정.

자세한 내용은 [`isaaclab.managers.TerminationManager`](../lab/isaaclab.managers.md#isaaclab.managers.TerminationManager) 클래스를 참조하세요.

#### curriculum *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

커리큘럼 설정. 기본값은 None이며, 이 경우 커리큘럼이 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.CurriculumManager`](../lab/isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 참조하세요.

#### commands *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

명령 설정. 기본값은 None이며, 이 경우 명령이 생성되지 않습니다.

자세한 내용은 [`isaaclab.managers.CommandManager`](../lab/isaaclab.managers.md#isaaclab.managers.CommandManager) 클래스를 참조하세요.

## 갈봇 환경

### 갈봇 큐브 스택 렐 미미크 환경 (RmpFlow)

### *클래스* isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv

기반: [`FrankaCubeStackIKRelMimicEnv`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv)

Isaac Lab Mimic 환경 워퍼 클래스: Galbot Cube Stack RmpFlow Rel env.

**메서드:**

| [`get_object_poses`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.get_object_poses)([env_ids])                             | 현재 씬에서 Isaac Lab Mimic 데이터 생성을 위해 로봇 베이스의 좌표계에서 각 객체의 포즈를 가져오도록 이 함수를 다시 작성합니다.                                |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`__init__`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.__init__)(cfg[, render_mode])                                    | 환경을 초기화합니다.                                                                                                                                                |
| [`action_to_target_eef_pose`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.action_to_target_eef_pose)(action)              | env.step과 호환되는 동작을 엔드 이펙터 컨트롤러의 목표 포즈로 변환합니다.                                                                               |
| [`actions_to_gripper_actions`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.actions_to_gripper_actions)(actions)           | env.step과 호환되는 환경 동작 시퀀스에서 그리퍼 액추에이션 부분을 추출합니다.                                                                             |
| [`close`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.close)()                                                            | 환경 정리.                                                                                                                                               |
| [`export_IO_descriptors`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.export_IO_descriptors)([output_dir])                | 환경의 IO 디스크립터를 내보냅니다.                                                                                                                             |
| [`get_expected_attached_object`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.get_expected_attached_object)(eef_name, ...) | (SkillGen) 주어진 EEF/subtask에 대한 예상 부착 객체를 반환합니다.                                                                                                  |
| [`get_navigation_state`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.get_navigation_state)([env_ids])                     | 선택적 메서드입니다.                                                                                                                                                           |
| [`get_robot_eef_pose`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.get_robot_eef_pose)(eef_name[, env_ids])               | 현재 로봇 엔드 이펙터 포즈를 가져옵니다.                                                                                                                                       |
| [`get_subtask_start_signals`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.get_subtask_start_signals)([env_ids])           | 작업의 각 서브태스크에 대한 시작 시그널 플래그를 딕셔너리로 가져옵니다.                                                                                                        |
| [`get_subtask_term_signals`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.get_subtask_term_signals)([env_ids])             | 작업의 각 서브태스크에 대한 종료 시그널 플래그를 딕셔너리로 가져옵니다.                                                                                                  |
| [`get_wrapper_attr`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.get_wrapper_attr)(name)                                  | 환경에서 속성 이름을 가져옵니다.                                                                                                                              |
| [`has_wrapper_attr`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.has_wrapper_attr)(name)                                  | 환경에 속성 이름이 존재하는지 확인합니다.                                                                                                                    |
| [`load_managers`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.load_managers)()                                            | 환경의 관리자를 로드합니다.                                                                                                                                     |
| [`render`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.render)([recompute])                                               | 물리 시뮬레이션을 수행하지 않고 렌더링을 실행합니다.                                                                                                                        |
| [`reset`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.reset)([seed, env_ids, options])                                    | 지정된 환경을 재설정하고 관측값을 반환합니다.                                                                                                                |
| [`reset_to`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.reset_to)(state, env_ids[, seed, is_relative])                   | 지정된 환경을 제공된 상태로 재설정합니다.                                                                                                                          |
| [`seed`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.seed)([seed])                                                        | 환경의 시드를 설정합니다.                                                                                                                                          |
| [`serialize`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.serialize)()                                                    | 이 환경을 다시 인스턴스화하기 위해 필요한 모든 정보를 딕셔너리에 저장합니다.                                                                                            |
| [`set_wrapper_attr`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.set_wrapper_attr)(name, value, \*[, force])              | 환경에 값을 갖는 속성 이름을 설정합니다. 자세한 내용은 Wrapper.set_wrapper_attr을 참조하세요.                                                                         |
| [`setup_manager_visualizers`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.setup_manager_visualizers)()                    | 관리자 용어를 위한 라이브 시각화 도구를 생성합니다.                                                                                                                                |
| [`step`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.step)(action)                                                        | 환경의 역학을 한 타임스텝 실행하고 종료된 환경을 재설정합니다.                                                                                     |
| [`target_eef_pose_to_action`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.target_eef_pose_to_action)(...[, ...])          | 엔드 이펙터 컨트롤러의 목표 포즈와 그리퍼 액션을 받아서 해당 목표 포즈를 달성하기 위한 행동(일반적으로 정규화된 델타 포즈 액션)을 반환합니다. |

**속성:**

| [`device`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.device)                             | 환경이 실행되는 디바이스입니다.                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`get_IO_descriptors`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.get_IO_descriptors)     | 환경의 IO 디스크립터를 가져옵니다.                                                                           |
| [`is_vector_env`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.is_vector_env)               | 환경이 벡터화된 환경인지 여부를 나타냅니다.                                                                  |
| [`max_episode_length`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.max_episode_length)     | 환경 스텝 단위의 최대 에피소드 길이.                                                                          |
| [`max_episode_length_s`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.max_episode_length_s) | 초 단위의 최대 에피소드 길이.                                                                                    |
| [`metadata`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.metadata)                         | 환경의 메타데이터입니다.                                                                                         |
| [`np_random`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.np_random)                       | 설정되지 않은 경우 랜덤 시드로 초기화되는 환경의 내부 `_np_random`을 반환합니다.                   |
| [`np_random_seed`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.np_random_seed)             | 설정되지 않은 경우 랜덤 정수 시드로 먼저 초기화되는 환경의 내부 `_np_random_seed`를 반환합니다. |
| [`num_envs`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.num_envs)                         | 현재 실행 중인 환경 인스턴스의 수입니다.                                                          |
| [`physics_dt`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.physics_dt)                     | 물리 시간 단계(초 단위).                                                                                         |
| [`step_dt`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.step_dt)                           | 환경 스텝 시간 단계(초 단위).                                                                            |
| [`unwrapped`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.unwrapped)                       | 기본 래핑되지 않은 환경을 반환합니다.                                                                             |
| [`cfg`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.cfg)                                   | 환경의 구성입니다.                                                                                    |

#### get_object_poses(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

이 함수를 각 객체의 로봇 베이스 프레임에서의 포즈를 가져오도록 다시 작성하세요. 현재 장면에서 Isaac Lab Mimic 데이터 생성과 관련이 있습니다.

* **매개변수:**
  **env_ids** – 포즈를 가져올 환경 인덱스. None인 경우 모든 환경을 고려합니다.
* **반환값:**
  로봇 베이스 프레임에서의 객체 포즈 행렬(4x4 torch.Tensor)에 객체 이름을 매핑하는 딕셔너리

#### \_\_init_\_(cfg: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성입니다.
  * **render_mode** – 환경의 렌더링 모드입니다. 기본값은 None으로, 이는 `"human"`과 유사합니다.

#### action_to_target_eef_pose(action: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

동작(action)을 엔드 이펙터 컨트롤러용 목표 포즈로 변환합니다. `@target_eef_pose_to_action`의 역함수입니다. 일반적으로 기록된 동작을 사용하여 데모 경로에서 목표 컨트롤러 포즈 시퀀스를 추론하는 데 사용됩니다.

* **매개변수:**
  **action** – 환경의 동작. 형태는 (num_envs, action_dim)입니다.
* **반환값:**
  @action에 해당하는 엔드 이펙터 포즈 torch.Tensor 딕셔너리

#### actions_to_gripper_actions(actions: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

env.step과 호환되는 환경 동작 시퀀스에서 그립퍼 actuation 부분을 추출합니다.

* **매개변수:**
  **actions** – 환경의 동작. 형태는 (num_envs, 데모의 단계 수, action_dim)입니다.
* **반환값:**
  키가 eef_name인 torch.Tensor 그립퍼 동작 딕셔너리

#### close()

환경을 정리합니다.

#### *property* device

환경이 실행 중인 장치입니다.

#### export_IO_descriptors(output_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

환경의 IO 설명자를 내보냅니다.

* **매개변수:**
  **output_dir** – IO 설명자를 내보낼 디렉터리입니다.

#### *property* get_IO_descriptors

환경의 IO 설명자를 가져옵니다.

* **반환값:**
  그룹 이름을 키로 하고 IO 설명자를 값으로 하는 딕셔너리

#### get_expected_attached_object(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), subtask_index: [int](https://docs.python.org/3/library/functions.html#int), env_cfg) → [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)

(SkillGen) 주어진 EEF/서브태스크에 대해 기대되는 연결된 객체를 반환합니다.

‘stack’ 서브태스크는 이전 ‘grasp’ 서브태스크에서 grasped된 객체를 배치한다고 가정합니다.
‘grasp’(또는 기타)의 시작 시점에서는 None을 반환합니다.

#### get_navigation_state(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

선택적 메서드입니다. 내비게이션 컨트롤러 로코맨िप우레이션 데이터 생성을 사용할 때만 필요합니다.

로봇의 내비게이션 상태를 가져옵니다. 내비게이션 컨트롤러 사용이 활성화된 경우 필수입니다. 내비게이션 상태에는 내비게이션 컨트롤러에 의해 로봇이 제어되고 있는지 여부를 나타내는 부울 플래그 “is_navigating”과 내비게이션 목표가 달성되었는지 여부를 나타내는 부울 플래그 “navigation_goal_reached”가 포함됩니다.

* **매개변수:**
  **env_ids** – 내비게이션 상태를 가져올 환경 인덱스. None인 경우 모든 환경을 고려합니다.
* **반환값:**
  내비게이션 상태 플래그(False 또는 True)를 포함하는 딕셔너리

#### get_robot_eef_pose(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

현재 로봇 엔드 이펙터 포즈를 가져옵니다. 로봇 엔드-이펙터 컨트롤러에서 사용되는 프레임과 동일해야 합니다.

* **매개변수:**
  * **eef_name** – 엔드 이펙터의 이름입니다.
  * **env_ids** – 포즈를 가져올 환경 인덱스. None인 경우 모든 환경을 고려합니다.
* **반환값:**
  엔드 이펙터 포즈 행렬인 torch.Tensor. 형태는 (len(env_ids), 4, 4)입니다.

#### get_subtask_start_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

태스크의 각 서브태스크에 대한 시작 신호 플래그 딕셔너리를 가져옵니다. 서브태스크가 시작된 경우 플래그는 1이고, 그렇지 않은 경우 0입니다. 이 메서드의 구현은 데이터셋 주석 도구를 실행할 때 자동 서브태스크 시작 신호 주석을 활성화하려는 경우 필수입니다. 수동 서브태스크 시작 신호 주석을 사용하려는 경우 이 메서드를 구현하지 않은 상태로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 시작 신호를 가져올 환경 인덱스. None인 경우 모든 환경을 고려합니다.
* **반환값:**
  각 서브태스크에 대한 시작 신호 플래그(False 또는 True)를 포함하는 딕셔너리

#### get_subtask_term_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

태스크의 각 서브태스크에 대한 종료 신호 플래그 딕셔너리를 가져옵니다. 서브태스크가 완료된 경우 플래그는 1이고, 그렇지 않은 경우 0입니다. 이 메서드의 구현은 데이터셋 주석 도구를 실행할 때 자동 서브태스크 종료 신호 주석을 활성화하려는 경우 필수입니다. 수동 서브태스크 종료 신호 주석을 사용하려는 경우 이 메서드를 구현하지 않은 상태로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 종료 신호를 가져올 환경 인덱스. None인 경우 모든 환경을 고려합니다.
* **반환값:**
  각 서브태스크에 대한 종료 신호 플래그(False 또는 True)를 포함하는 딕셔너리

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 속성 이름을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 속성 이름이 존재하는지 확인합니다.

#### is_vector_env *: ClassVar[[bool](https://docs.python.org/3/library/functions.html#bool)]* *= True*

환경이 벡터화된 환경인지 여부.

#### load_managers()

환경의 관리자를 로드합니다.

이 함수는 환경의 다양한 관리자(작업, 관찰, 이벤트 등)를 생성하는 역할을 담당합니다. 관리자는 물리 핸들러에 접근해야 하므로, 시뮬레이터가 재설정(즉, 처음 재생된 후)된 후에만 생성할 수 있습니다.

#### NOTE
독립형 애플리케이션의 경우(파이썬에서 시뮬레이터 실행 시), 클래스가 초기화될 때 자동으로 이 함수가 호출됩니다.

그러나 확장 모드에서는 사용자가 시뮬레이터를 재설정 한 후 수동으로 이 함수를 호출해야 합니다. 이는 시뮬레이터가 사용자가 `SimulationContext.reset_async()`를 호출할 때만 재설정되며, 생성자 내에서 비동기 함수를 호출할 수 없기 때문입니다.

#### *property* max_episode_length *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 단계에서의 최대 에피소드 길이.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

초 단위의 최대 에피소드 길이.

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

환경의 내부 `_np_random`을 반환합니다. 설정되지 않은 경우 랜덤 시드로 초기화됩니다.

* **반환값:**
  `np.random.Generator` 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 내부 `_np_random_seed`을 반환합니다. 설정되지 않은 경우 먼저 랜덤 정수로 시드가 초기화됩니다.

[`np_random_seed`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.np_random_seed)를 `reset()` 또는 `set_np_random_through_seed()` 대신 직접 설정한 경우, 시드는 -1이 됩니다.

* **반환값:**
  현재 `np_random`의 시드 또는 rng의 시드가 알려지지 않은 경우 -1
* **반환 타입:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

실행 중인 환경 인스턴스의 수.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 시간 간격(초 단위).

이 값은 시뮬레이션이 발생하는 최소 시간 간격입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 단계를 거치지 않고 렌더링을 실행합니다.

관례에 따라, 모드가 다음과 같은 경우:

- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않음. 일반적으로人間のために 사용됨.
- **rgb_array**: (x, y, 3) 형태의 `numpy.ndarray`를 반환하며, 이는 RGB 값을 나타내고 x-by-y 픽셀 이미지에 적합하여 비디오로 변환하기에 적합함.

* **매개변수:**
  **recompute** – 시뮬레이터가 이미 장면을 렌더링했더라도 강제로 렌더링할지 여부. 기본값은 False.
* **반환값:**
  모드가 “rgb_array”인 경우 렌더링된 이미지를 numpy 배열로 반환. 그렇지 않으면 None을 반환.
* **예외:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 모드가 “rgb_data”로 설정되었지만 시뮬레이션 렌더링 모드가 이를 지원하지 않는 경우.
        이 경우, 시뮬레이션 렌더링 모드는 `RenderMode.PARTIAL_RENDERING` 또는 `RenderMode.FULL_RENDERING`으로 설정해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정된 경우.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

지정된 환경을 재설정하고 관측값을 반환합니다.

이 함수는 지정된 환경을 재설정하기 위해 `_reset_idx()` 함수를 호출합니다. 그러나 초기화 중에 발생한 절차적 지형 생성과 같은 특정 작업은 반복되지 않습니다.

* **매개변수:**
  * **seed** – 무작위화에 사용할 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않음.
  * **env_ids** – 재설정할 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 재설정됨.
  * **options** – 

    환경을 재설정하는 방법을 지정하는 추가 정보. 기본값은 None.

    #### NOTE
    이 인자는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **반환값:**
  관측값과 추가 정보를 포함한 튜플.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

제공된 상태로 지정된 환경을 재설정합니다.

이 함수는 제공된 상태로 환경을 재설정합니다. 상태는 장면 엔티티의 상태를 포함하는 사전입니다. 형식은 `InteractiveScene.get_state()`를 참조하십시오.

이 함수는 [`reset()`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env.RmpFlowGalbotCubeStackRelMimicEnv.reset)와 달리, 무작위화 이벤트를 사용하는 대신 환경을 특정 상태로 재설정한다는 점에서 다릅니다.

* **매개변수:**
  * **state** – 지정된 환경을 재설정할 상태. 형식은 `InteractiveScene.get_state()`를 참조하십시오.
  * **env_ids** – 재설정할 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 재설정됨.
  * **seed** – 무작위화에 사용할 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않음.
  * **is_relative** – True로 설정된 경우, 상태는 환경 원점 기준으로 간주됩니다. 기본값은 False.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **매개변수:**
  **seed** – 랜덤 생성기의 시드. 기본값은 -1.
* **반환값:**
  랜덤 생성기에 사용된 시드.

#### serialize()

이 환경을 다시 인스턴스화하는 데 필요한 모든 정보를 사전에 저장합니다.
이는 @env_meta와 동일하며, hdf5 데이터셋에 저장된 환경 메타데이터이고, utils/env_utils.py에서 사용됩니다.

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 지정된 이름의 속성을 값으로 설정합니다. 자세한 내용은 `Wrapper.set_wrapper_attr`를 참조하십시오.

#### setup_manager_visualizers()

관리자 항목에 대한 라이브 시각화 도구를 생성합니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동역학에서 한 시간 단계를 실행하고 종료된 환경을 재설정합니다.

`ManagerBasedEnv.step` 클래스와 달리, 이 함수는 다음 작업을 수행합니다:

1. 행동을 처리합니다.
2. 물리 스텝을 수행합니다.
3. GUI가 활성화된 경우 렌더링을 수행합니다.
4. 환경 카운터를 업데이트하고 보상 및 종료 조건을 계산합니다.
5. 종료된 환경을 재설정합니다.
6. 관측값을 계산합니다.
7. 관측값, 보상, 재설정(종료 및 잘림) 및 추가 정보를 반환합니다.

* **매개변수:**
  **action** – 환경에 적용할 행동. 형태는 (num_envs, action_dim)입니다.
* **반환값:**
  관측값, 보상, 재설정(종료 및 잘림), 추가 정보를 포함한 튜플입니다.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스텝 시간 간격(초 단위).

이 값은 환경이 앞으로 진행되는 시간 간격입니다.

#### target_eef_pose_to_action(target_eef_pose_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), gripper_action_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), action_noise_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_id: [int](https://docs.python.org/3/library/functions.html#int) = 0) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

엔드 이펙터 컨트롤러의 목표 자세와 그리퍼 행동을 받아 해당 목표 자세를 달성하기 위한 행동(일반적으로 정규화된 델타 자세 행동)을 반환합니다.
지정된 경우 목표 자세 행동에 노이즈를 추가합니다.

* **매개변수:**
  * **target_eef_pose_dict** – 각 엔드 이펙터의 4x4 목표 엔드 이펙터 자세를 포함하는 딕셔너리.
  * **gripper_action_dict** – 각 엔드 이펙터의 그리퍼 행동을 포함하는 딕셔너리.
  * **action_noise_dict** – 행동에 추가할 노이즈. None인 경우 노이즈가 추가되지 않음.
  * **env_id** – 행동을 가져올 환경의 인덱스.
* **반환값:**
  env.step()과 호환되는 torch.Tensor 형태의 행동.

#### *property* unwrapped *: [Env](https://gymnasium.farama.org/api/env/#gymnasium.Env)[ObsType, ActType]*

래핑되지 않은 기본 환경을 반환합니다.

* **반환값:**
  래핑되지 않은 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인스턴스
* **반환 타입:**
  Env

#### cfg *: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg)*

환경 구성.

### Galbot Left Arm Gripper Cube Stack Rel Mimic Env Cfg

### *class* isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg

Bases: `RmpFlowGalbotLeftArmCubeStackEnvCfg`, [`MimicEnvCfg`](../lab/isaaclab.envs.md#isaaclab.envs.MimicEnvCfg)

Isaac Lab Mimic 환경 구성 클래스 - Galbot 그립퍼 큐브 스택 IK 상대 환경용.

**메서드:**

| [`__init__`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.__init__)([datagen_config, subtask_configs, ...])   |    |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----|

**클래스:**

| [`ui_window_class_type`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.ui_window_class_type)   | [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭   |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|

**속성:**

| [`viewer`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.viewer)                                 | 뷰어 구성.                                                                       |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.sim)                                       | 물리 시뮬레이션 구성.                                                           |
| [`seed`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.seed)                                     | 난수 생성기의 시드.                                                   |
| [`decimation`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.decimation)                         | 정책 시간 간격당 시뮬레이션 시간 간격에서의 제어 행동 업데이트 횟수.                                    |
| [`scene`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.scene)                                   | 씬 설정.                                                                             |
| [`recorders`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.recorders)                           | 레코더 설정.                                                                          |
| [`observations`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.observations)                     | 관측 공간 설정.                                                                     |
| [`actions`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.actions)                               | 행동 공간 설정.                                                                    |
| [`events`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.events)                                 | 이벤트 설정.                                                                       |
| [`rerender_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.rerender_on_reset)           | 적어도 하나의 환경이 재설정된 후 렌더링 단계를 다시 수행할지 여부.     |
| [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.num_rerenders_on_reset) | 재설정 후 수행할 렌더링 단계 수.                                              |
| [`wait_for_textures`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.wait_for_textures)           | 에셋이 완전히 로드될 때까지 대기할지 여부 (True: 대기, False: 비대기).                           |
| [`xr`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.xr)                                         | XR 장치를 통한 환경 시각화 및 상호작용 구성.                                |
| [`teleop_devices`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.teleop_devices)                 | 원격 조작 장치 구성.                                                            |
| [`export_io_descriptors`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.export_io_descriptors)   | 환경의 IO 설명자를 내보낼지 여부.                                             |
| [`log_dir`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.log_dir)                               | 실험 아티팩트를 기록할 디렉터리.                                                |
| [`is_finite_horizon`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.is_finite_horizon)           | 에이전트를 위한 학습 작업이 유한 horizon인지 무한 horizon인지 여부. |
| [`episode_length_s`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.episode_length_s)             | 에피소드 기간(초 단위).                                                            |
| [`rewards`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.rewards)                               | 보상 설정.                                                                        |
| [`terminations`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.terminations)                     | 종료 조건 설정.                                                                   |
| [`curriculum`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.curriculum)                         | 커리큘럼 설정.                                                                    |
| [`commands`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.commands)                             | 명령 설정.                                                                      |

#### \_\_init_\_(datagen_config: ~isaaclab.envs.mimic_env_cfg.DataGenConfig = <factory>, subtask_configs: dict[str, list[isaaclab.envs.mimic_env_cfg.SubTaskConfig]] = <factory>, task_constraint_configs: list[isaaclab.envs.mimic_env_cfg.SubTaskConstraintConfig] = <factory>, mimic_recorder_config: ~isaaclab.managers.recorder_manager.RecorderManagerBaseCfg | None = <factory>, viewer: ~isaaclab.envs.common.ViewerCfg = <factory>, sim: ~isaaclab.sim.simulation_cfg.SimulationCfg = <factory>, ui_window_class_type: type | None = <class 'isaaclab.envs.ui.manager_based_rl_env_window.ManagerBasedRLEnvWindow'>, seed: int | None = <factory>, decimation: int = <factory>, scene: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObjectTableSceneCfg = <factory>, recorders: object = <factory>, observations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObservationsCfg = <factory>, actions: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ActionsCfg = <factory>, events: object = <factory>, rerender_on_reset: bool = <factory>, num_rerenders_on_reset: int = <factory>, wait_for_textures: bool = <factory>, xr: ~isaaclab.devices.openxr.xr_cfg.XrCfg = <factory>, teleop_devices: ~isaaclab.devices.device_base.DevicesCfg = <factory>, export_io_descriptors: bool = <factory>, log_dir: str | None = <factory>, is_finite_horizon: bool = <factory>, episode_length_s: float = <factory>, rewards: object = <factory>, terminations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.TerminationsCfg = <factory>, curriculum: object | None = <factory>, commands: object | None = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### ui_window_class_type

[`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭입니다.

#### viewer *: [ViewerCfg](../lab/isaaclab.envs.md#isaaclab.envs.ViewerCfg)*

뷰어 구성입니다. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](../lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성입니다. 기본값은 SimulationCfg()입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 시 설정됩니다. 이를 통해 환경 생성이 결정적이며 다양한 실행 간에 동일하게 동작하도록 보장합니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

시뮬레이션 dt당 정책 dt에서의 제어 액션 업데이트 수입니다.

예를 들어, 시뮬레이션 dt가 0.01s이고 정책 dt가 0.1s인 경우, 디시메이션은 10입니다.
즉, 제어 액션은 시뮬레이션 10단계마다 업데이트됩니다.

#### scene *: ObjectTableSceneCfg*

씬 설정입니다.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](../lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCls) 클래스를 참고하세요.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정입니다. 기본값은 아무것도 기록하지 않는 것입니다.

자세한 내용은 [`isaaclab.managers.RecorderManager`](../lab/isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참고하세요.

#### observations *: ObservationsCfg*

관찰 공간 설정입니다.

자세한 내용은 [`isaaclab.managers.ObservationManager`](../lab/isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참고하세요.

#### actions *: ActionsCfg*

액션 공간 설정입니다.

자세한 내용은 [`isaaclab.managers.ActionManager`](../lab/isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참고하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정입니다. 기본값은 씬을 기본 상태로 재설정하는 기본 구성입니다.

자세한 내용은 [`isaaclab.managers.EventManager`](../lab/isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참고하세요.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경이 최소 한 번 이상 재설정된 후 렌더 단계가 다시 수행되는지 여부입니다. 기본값은 False이며, 이는 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 False인 경우, 재설정 후 센서에서 수집한 데이터는 오래되고 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 이 값이 True인 경우, 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 재설정으로 인한 최신 시뮬레이션 상태를 반영합니다. 이는 추가 렌더 단계가 수행됨에 따라 성능 저하를 초래합니다.

#### Deprecated
버전 2.3.1부터 더 이상 사용되지 않음: 이 속성은 더 이상 사용되지 않으며 향후 제거될 예정입니다. 대신 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.num_rerenders_on_reset)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정한 것과 동일한 동작을 얻으려면
[`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackRelMimicEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하세요.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후 수행할 렌더 단계 수입니다. 기본값은 0이며, 이는 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 0인 경우, 재설정 후 렌더 단계가 수행되지 않습니다. 재설정 후 센서에서 수집한 데이터는 오래되고 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 이 값이 0보다 큰 경우, 지정된 수의 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 재설정으로 인한 최신 시뮬레이션 상태를 반영합니다. 이는 추가 렌더 단계가 수행됨에 따라 성능 저하를 초래합니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋이 완전히 로드될 때까지 대기하려면 True, 그렇지 않으면 False입니다. 기본값은 True입니다.

#### xr *: XrCfg*

XR 기기를 통해 환경을 보고 상호작용하는 구성입니다.

#### teleop_devices *: DevicesCfg*

텔레오퍼레이션 장치 구성입니다.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 설명자를 내보낼지 여부입니다. 기본값은 False입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 로깅할 디렉터리입니다. 기본값은 None이며, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에이전트에 대한 학습 작업이 유한 또는 무한 시간 범위 문제로 취급되는지 여부입니다. 기본값은 False이며, 이는 작업이 무한 시간 범위 문제로 취급됨을 의미합니다.

이 플래그는 유한 및 무한 시간 범위 작업의 미묘한 차이를 처리합니다:

* **유한 시간 범위**: 시간이 초과되어도 에이전트에 페널티 또는 부트스트래핑 값이 필요하지 않습니다. 그러나 시간 제한에 도달했을 때 에피소드를 종료해야 하는 환경의 필요성은 여전히 존재합니다.
* **무한 시간 범위**: 에이전트는 에피소드 끝의 상태 값을 부트스트래핑해야 합니다. 이는 시간 제한(또는 잘림) 완료 신호를 에이전트에 전송하여 수행되며, 이 신호는 부트스트래핑 계산을 트리거합니다.

True인 경우, 환경은 유한 시간 범위 문제로 취급되며 에이전트에 타임아웃(또는 잘림) 완료 신호가 전송되지 않습니다. False인 경우, 환경은 무한 시간 범위 문제로 취급되며 에이전트에 타임아웃(또는 잘림) 완료 신호가 전송됩니다.

#### NOTE
기본 `ManagerBasedRLEnv` 클래스는 이 플래그를 직접 사용하지 않습니다. 이 플래그는 환경 래퍼에서 사용되어 해당 학습 에이전트에 전송할 완료 신호의 유형을 결정합니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 지속 시간(초 단위)입니다.

디시메이션률과 물리 타임스텝을 기반으로 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디시메이션률이 10이고 물리 타임스텝이 0.01이며 에피소드 길이가 10초인 경우, 에피소드 길이(단계)는 100입니다.

#### rewards *: [object](https://docs.python.org/3/library/functions.html#object)*

보상 설정입니다.

자세한 내용은 [`isaaclab.managers.RewardManager`](../lab/isaaclab.managers.md#isaaclab.managers.RewardManager) 클래스를 참고하세요.

#### terminations *: TerminationsCfg*

종료 설정입니다.

자세한 내용은 [`isaaclab.managers.TerminationManager`](../lab/isaaclab.managers.md#isaaclab.managers.TerminationManager) 클래스를 참고하세요.

#### curriculum *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

커리큘럼 설정입니다. 기본값은 None이며, 이 경우 커리큘럼이 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.CurriculumManager`](../lab/isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 참고하세요.

#### commands *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

명령 설정입니다. 기본값은 None이며, 이 경우 명령이 생성되지 않습니다.

자세한 내용은 [`isaaclab.managers.CommandManager`](../lab/isaaclab.managers.md#isaaclab.managers.CommandManager) 클래스를 참고하세요.

### Galbot Right Arm Suction Cube Stack Rel Mimic Env Cfg

### *class* isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg

Bases: `RmpFlowGalbotRightArmCubeStackEnvCfg`, [`MimicEnvCfg`](../lab/isaaclab.envs.md#isaaclab.envs.MimicEnvCfg)

Isaac Lab Mimic 환경 구성 클래스: Galbot 흡착 그리퍼 큐브 스택 RmpFlow Rel 환경.

**메서드:**

| [`__init__`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.__init__)([datagen_config, subtask_configs, ...])   |    |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----|

**클래스:**

| [`ui_window_class_type`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.ui_window_class_type)   | [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭   |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|

**속성:**

| [`viewer`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.viewer)                                 | 뷰어 구성.                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.sim)                                       | 물리 시뮬레이션 구성.                                                           |
| [`seed`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.seed)                                     | 난수 생성기의 시드.                                                   |
| [`decimation`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.decimation)                         | 정책 dt당 sim dt당 제어 행동 업데이트 수.                                    |
| [`scene`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.scene)                                   | 씬 설정.                                                                             |
| [`recorders`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.recorders)                           | 레코더 설정.                                                                          |
| [`observations`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.observations)                     | 관찰 공간 설정.                                                                     |
| [`actions`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.actions)                               | 행동 공간 설정.                                                                      |
| [`events`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.events)                                 | 이벤트 설정.                                                                             |
| [`rerender_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.rerender_on_reset)           | 최소한 하나의 환경이 리셋된 후 렌더링 단계가 다시 수행되는지 여부.     |
| [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.num_rerenders_on_reset) | 리셋 후 수행할 렌더링 단계 수.                                              |
| [`wait_for_textures`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.wait_for_textures)           | 에셋이 완전히 로드될 때까지 대기할지 여부. True이면 대기, False이면 대기 안 함.                           |
| [`xr`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.xr)                                         | XR 장치를 통해 환경을 보고 상호작용하기 위한 구성.        |
| [`teleop_devices`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.teleop_devices)                 | 원격 조작 장치 구성.                                                    |
| [`export_io_descriptors`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.export_io_descriptors)   | 환경의 IO 설명자 내보내기 여부.                                   |
| [`log_dir`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.log_dir)                               | 실험 아티팩트를 위한 로그 디렉터리.                                                 |
| [`is_finite_horizon`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.is_finite_horizon)           | 에이전트에 대한 학습 작업이 유한 또는 무한 호라이즌 문제로 간주되는지 여부. |
| [`episode_length_s`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.episode_length_s)             | 에피소드 지속 시간(초 단위).                                                        |
| [`rewards`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.rewards)                               | 보상 설정.                                                                            |
| [`terminations`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.terminations)                     | 종료 설정.                                                                       |
| [`curriculum`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.curriculum)                         | 커리큘럼 설정.                                                                        |
| [`commands`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.commands)                             | 명령 설정.                                                                           |

#### \_\_init_\_(datagen_config: ~isaaclab.envs.mimic_env_cfg.DataGenConfig = <factory>, subtask_configs: dict[str, list[isaaclab.envs.mimic_env_cfg.SubTaskConfig]] = <factory>, task_constraint_configs: list[isaaclab.envs.mimic_env_cfg.SubTaskConstraintConfig] = <factory>, mimic_recorder_config: ~isaaclab.managers.recorder_manager.RecorderManagerBaseCfg | None = <factory>, viewer: ~isaaclab.envs.common.ViewerCfg = <factory>, sim: ~isaaclab.sim.simulation_cfg.SimulationCfg = <factory>, ui_window_class_type: type | None = <class 'isaaclab.envs.ui.manager_based_rl_env_window.ManagerBasedRLEnvWindow'>, seed: int | None = <factory>, decimation: int = <factory>, scene: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObjectTableSceneCfg = <factory>, recorders: object = <factory>, observations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObservationsCfg = <factory>, actions: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ActionsCfg = <factory>, events: object = <factory>, rerender_on_reset: bool = <factory>, num_rerenders_on_reset: int = <factory>, wait_for_textures: bool = <factory>, xr: ~isaaclab.devices.openxr.xr_cfg.XrCfg = <factory>, teleop_devices: ~isaaclab.devices.device_base.DevicesCfg = <factory>, export_io_descriptors: bool = <factory>, log_dir: str | None = <factory>, is_finite_horizon: bool = <factory>, episode_length_s: float = <factory>, rewards: object = <factory>, terminations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.TerminationsCfg = <factory>, curriculum: object | None = <factory>, commands: object | None = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### ui_window_class_type

[`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭

#### viewer *: [ViewerCfg](../lab/isaaclab.envs.md#isaaclab.envs.ViewerCfg)*

뷰어 구성. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](../lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성. 기본값은 SimulationCfg()입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 시 설정됩니다.これにより 환경 생성이 결정적이며 다른 실행 간에 유사하게 동작하도록 보장합니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

정책 dt당 sim dt당 제어 행동 업데이트 수.

예를 들어, 시뮬레이션 dt가 0.01초이고 정책 dt가 0.1초이면, 디시메이션은 10입니다.
즉, 제어 행동은 시뮬레이션 단계 10마다 업데이트됩니다.

#### scene *: ObjectTableSceneCfg*

씬 설정.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](../lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스를 참조하십시오.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정. 기본값은 아무것도 기록하지 않습니다.

자세한 내용은 [`isaaclab.managers.RecorderManager`](../lab/isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참고하세요.

#### observations *: ObservationsCfg*

관찰 공간 설정.

자세한 내용은 [`isaaclab.managers.ObservationManager`](../lab/isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참고하세요.

#### actions *: ActionsCfg*

작업 공간 설정.

자세한 내용은 [`isaaclab.managers.ActionManager`](../lab/isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참고하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정. 기본값은 장면을 기본 상태로 재설정하는 기본 구성이며, 자세한 내용은 다음과 같습니다.

자세한 내용은 [`isaaclab.managers.EventManager`](../lab/isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참고하세요.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

하나 이상의 환경이 재설정될 때 렌더 단계를 다시 수행할지 여부입니다. 기본값은 `False`로, 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 `False`일 경우, 재설정 후 센서에서 수집된 데이터는 최신이 아니며, 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 이 값이 `True`일 경우, 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 재설정으로 인한 최신 상태를 반영합니다. 이때 추가 렌더 단계로 인해 성능 저하가 발생할 수 있습니다.

#### Deprecated
버전 2.3.1부터 지원 중단됨: 이 속성은 향후 제거될 예정입니다. 대신 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.num_rerenders_on_reset)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정했을 때의 동일 동작을 얻으려면 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_rel_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackRelMimicEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하세요.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후에 수행할 렌더 단계 수입니다. 기본값은 0으로, 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 0일 경우, 재설정 후 렌더 단계가 수행되지 않으며, 센서에서 수집된 데이터는 최신이 아니고 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 이 값이 0보다 클 경우, 지정된 횟수만큼 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 재설정으로 인한 최신 상태를 반영합니다. 이때 추가 렌더 단계로 인해 성능 저하가 발생할 수 있습니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에셋이 완전히 로드될 때까지 기다릴지 여부입니다. `True`이면 기다리며, `False`이면 기다리지 않습니다. 기본값은 `True`입니다.

#### xr *: XrCfg*

XR 기기를 통해 환경을 보고 상호작용하는 데 대한 설정입니다.

#### teleop_devices *: DevicesCfg*

텔레오퍼레이션 장치에 대한 설정입니다.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 설명자를 내보낼지 여부입니다. 기본값은 `False`입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 기록할 디렉터리입니다. 기본값은 `None`으로, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

학습 작업이 유한 또는 무한 시간 범위 문제로 간주되는지 여부를 나타냅니다. 기본값은 `False`로, 작업이 무한 시간 범위 문제로 간주됨을 의미합니다.

이 플래그는 유한 및 무한 시간 범위 작업의 세부 사항을 처리합니다:

* **유한 시간 범위**: 시간 초과 시 에이전트에 페널티 또는 부트스트랩 값이 필요하지 않습니다. 그러나 환경은 시간 제한이 도달된 후 에피소드를 여전히 종료해야 합니다.
* **무한 시간 범위**: 에이전트는 에피소드 종료 시 상태의 값을 부트스트랩해야 합니다. 이는 시간 제한(또는 잘림) 완료 신호를 에이전트에 전송하여 이루어지며, 이 신호가 부트스트랩 계산을 트리거합니다.

`True`이면 환경은 유한 시간 범위 문제로 간주되며 타임아웃(또는 잘림) 완료 신호가 에이전트에 전송되지 않습니다. `False`이면 환경은 무한 시간 범위 문제로 간주되며 타임아웃(또는 잘림) 완료 신호가 에이전트에 전송됩니다.

#### NOTE
기본 `ManagerBasedRLEnv` 클래스는 이 플래그를 직접 사용하지 않습니다. 이 플래그는 환경 래퍼에서 사용되어 해당 학습 에이전트에 어떤 유형의 완료 신호를 보낼지 결정합니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 지속 시간(초 단위).

디시메이션 비율과 물리 타임 스텝을 기반으로 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디시메이션 비율이 10이고 물리 타임 스텝이 0.01이며 에피소드 길이가 10초라면, 에피소드 길이는 100단계가 됩니다.

#### rewards *: [object](https://docs.python.org/3/library/functions.html#object)*

보상 설정.

자세한 내용은 [`isaaclab.managers.RewardManager`](../lab/isaaclab.managers.md#isaaclab.managers.RewardManager) 클래스를 참고하세요.

#### terminations *: TerminationsCfg*

종료 설정.

자세한 내용은 [`isaaclab.managers.TerminationManager`](../lab/isaaclab.managers.md#isaaclab.managers.TerminationManager) 클래스를 참고하세요.

#### curriculum *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

커리큘럼 설정. 기본값은 `None`으로, 이 경우 커리큘럼이 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.CurriculumManager`](../lab/isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 참고하세요.

#### commands *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

명령 설정. 기본값은 `None`으로, 이 경우 명령이 생성되지 않습니다.

자세한 내용은 [`isaaclab.managers.CommandManager`](../lab/isaaclab.managers.md#isaaclab.managers.CommandManager) 클래스를 참고하세요.

### Galbot Cube Stack Abs Mimic Env (RmpFlow)

### *class* isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv

Bases: [`FrankaCubeStackIKAbsMimicEnv`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv)

Isaac Lab Mimic 환경 래퍼 클래스로, Galbot Cube Stack RmpFlow 절대 환경용입니다.

**메서드:**

| [`get_object_poses`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.get_object_poses)([env_ids])                              | 현재 씬에서 Isaac Lab Mimic 데이터 생성과 관련하여 로봇 베이프 프레임에서 각 객체의 포즈를 얻으려면 이 함수를 다시 작성하세요.   |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| [`__init__`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.__init__)(cfg[, render_mode])                                     | 환경을 초기화합니다.                                                                                                                   |
| [`action_to_target_eef_pose`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.action_to_target_eef_pose)(action)               | 행동을 목표 엔드 이펙터 포즈로 변환합니다.                                                                                                                |
| [`actions_to_gripper_actions`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.actions_to_gripper_actions)(actions)            | 그리퍼 동작을 추출합니다.                                                                                                                      |
| [`close`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.close)()                                                             | 환경 정리를 수행합니다.                                                                                                                  |
| [`export_IO_descriptors`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.export_IO_descriptors)([output_dir])                 | 환경의 IO 설명자를 내보냅니다.                                                                                                |
| [`get_navigation_state`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.get_navigation_state)([env_ids])                      | 선택적 메서드입니다.                                                                                                                              |
| [`get_robot_eef_pose`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.get_robot_eef_pose)(eef_name[, env_ids])                | 현재 로봇 엔드 이펙터 포즈를 가져옵니다.                                                                                                          |
| [`get_subtask_start_signals`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.get_subtask_start_signals)([env_ids])            | 각 하위 작업의 시작 신호 플래그를 포함하는 딕셔너리를 가져옵니다.                                                                           |
| [`get_subtask_term_signals`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.get_subtask_term_signals)([env_ids])              | 하위 작업 종료 신호를 가져옵니다.                                                                                                              |
| [`get_wrapper_attr`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.get_wrapper_attr)(name)                                   | 환경에서 특정 속성 이름을 가져옵니다.                                                                                                 |
| [`has_wrapper_attr`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.has_wrapper_attr)(name)                                   | 특정 속성 이름이 환경에 존재하는지 확인합니다.                                                                                       |
| [`load_managers`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.load_managers)()                                             | 환경의 관리자(manager)를 로드합니다.                                                                                                        |
| [`render`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.render)([recompute])                                                | 물리 단계를 거치지 않고 렌더링을 실행합니다.                                                                                           |
| [`reset`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.reset)([seed, env_ids, options])                                     | 지정된 환경을 초기화하고 관측값을 반환합니다.                                                                                   |
| [`reset_to`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.reset_to)(state, env_ids[, seed, is_relative])                    | 지정된 환경을 제공된 상태로 초기화합니다.                                                                                             |
| [`seed`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.seed)([seed])                                                         | 환경의 시드를 설정합니다.                                                                                                             |
| [`serialize`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.serialize)()                                                     | 이 환경을 재구성하는 데 필요한 모든 정보를 딕셔너리에 저장합니다.                                                               |
| [`set_wrapper_attr`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.set_wrapper_attr)(name, value, \*[, force])               | 주어진 값으로 환경의 특정 속성을 설정합니다. Wrapper.set_wrapper_attr 를 참고하세요.                                            |
| [`setup_manager_visualizers`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.setup_manager_visualizers)()                     | 관리자 항목에 대한 라이브 시각화 도구를 생성합니다.                                                                                                   |
| [`step`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.step)(action)                                                         | 환경의 동역학에 따라 한 타임스텝을 실행하고 종료된 환경을 초기화합니다.                                                        |
| [`target_eef_pose_to_action`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.target_eef_pose_to_action)(...[, noise, env_id]) | 목표 자세를 동작(action)으로 변환합니다.                                                                                                                |

**Attributes:**

| [`device`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.device)                             | 환경이 실행 중인 디바이스입니다.                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`get_IO_descriptors`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.get_IO_descriptors)     | 환경의 입출력 디스크립터(IO descriptor)를 가져옵니다.                                                                           |
| [`is_vector_env`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.is_vector_env)               | 환경이 벡터화된 환경인지 여부를 나타냅니다.                                                                  |
| [`max_episode_length`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.max_episode_length)     | 환경 단계 기준으로 최대 에피소드 길이입니다.                                                                          |
| [`max_episode_length_s`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.max_episode_length_s) | 초 단위로 측정된 최대 에피소드 길이입니다.                                                                                    |
| [`metadata`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.metadata)                         | 환경의 메타데이터입니다.                                                                                         |
| [`np_random`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.np_random)                       | 설정되지 않은 경우 무작위 시드로 초기화되는 환경의 내부 `_np_random` 을 반환합니다.                   |
| [`np_random_seed`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.np_random_seed)             | 설정되지 않은 경우 먼저 무작위 정수로 초기화되는 환경의 내부 `_np_random_seed` 를 반환합니다. |
| [`num_envs`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.num_envs)                         | 현재 실행 중인 환경 인스턴스의 수입니다.                                                          |
| [`physics_dt`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.physics_dt)                     | 물리 타임스텝(초 단위)입니다.                                                                                         |
| [`step_dt`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.step_dt)                           | 환경 스텝 타임스텝(초 단위)입니다.                                                                            |
| [`unwrapped`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.unwrapped)                       | 래핑되지 않은 기본 환경을 반환합니다.                                                                             |
| [`cfg`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.cfg)                                   | 환경의 구성입니다.                                                                                    |

#### get_object_poses(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

로봇 기본 프레임에서 각 객체의 자세를 가져오는 이 함수를 Isaac Lab Mimic 데이터 생성에 맞게 현재 씬에서 다시 작성하세요.

* **매개변수:**
  **env_ids** – 자세를 가져올 환경 인덱스입니다. None이면 모든 환경이 고려됩니다.
* **반환값:**
  로봇 기본 프레임 기준 객체 자세 행렬(4x4 torch.Tensor)을 매핑하는 딕셔너리이며, 키는 객체 이름입니다.

#### \_\_init_\_(cfg: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성입니다.
  * **render_mode** – 환경의 렌더 모드입니다. 기본값은 None이며, 이는 `"human"`과 유사합니다.

#### action_to_target_eef_pose(action: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

동작을 목표 자세로 변환합니다.

#### actions_to_gripper_actions(actions: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

그리퍼 동작을 추출합니다.

#### close()

환경 정리를 수행합니다.

#### *property* device

환경이 실행 중인 디바이스입니다.

#### export_IO_descriptors(output_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

환경의 IO 설명자를 내보냅니다.

* **매개변수:**
  **output_dir** – IO 설명자를 내보낼 디렉터리.

#### *property* get_IO_descriptors

환경의 IO 설명자를 가져옵니다.

* **반환값:**
  키가 그룹 이름이고 값이 IO 설명자인 사전.

#### get_navigation_state(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

선택적 메서드입니다. 네비게이션 컨트롤러 로코매니퓰레이션 데이터 생성을 사용할 때만 필요합니다.

로봇의 네비게이션 상태를 가져옵니다. 네비게이션 컨트롤러 사용이 활성화된 경우 필수적입니다.
네비게이션 상태에는 네비게이션 컨트롤러에 의해 로봇이 제어 중인지 여부를 나타내는 부울 플래그
“is_navigating”과 네비게이션 목표가 달성되었는지 여부를 나타내는 부울 플래그
“navigation_goal_reached”가 포함됩니다.

* **매개변수:**
  **env_ids** – 네비게이션 상태를 가져올 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  네비게이션 상태 플래그(False 또는 True)를 담은 사전.

#### get_robot_eef_pose(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

로봇 엔드 이펙터의 현재 포즈를 가져옵니다.

#### get_subtask_start_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

작업 내 각 서브태스크의 시작 신호 플래그를 담은 사전을 가져옵니다.
플래그는 서브태스크가 시작된 경우 1, 그렇지 않은 경우 0입니다.
이 메서드의 구현은 데이터셋 주석 도구 실행 시 자동 서브태스크 시작 신호 주석
생성을 활성화하려는 경우 필수적입니다. 수동 서브태스크 시작 신호 주석을
사용하려는 경우 이 메서드를 구현하지 않은 채 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 시작 신호를 가져올 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  각 서브태스크에 대한 시작 신호 플래그(False 또는 True)를 담은 사전.

#### get_subtask_term_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

서브태스크 종료 신호를 가져옵니다.

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 특정 속성의 값을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 특정 속성이 존재하는지 확인합니다.

#### is_vector_env *: ClassVar[[bool](https://docs.python.org/3/library/functions.html#bool)]* *= True*

환경이 벡터화된 환경인지 여부.

#### load_managers()

환경의 관리자를 로드합니다.

이 함수는 환경의 다양한 관리자(작업, 관찰, 이벤트 등)를 생성하는 역할을 합니다.
관리자는 물리 핸들에 접근해야 하므로 시뮬레이터가 리셋된 후(즉, 첫 번째 실행 후)에만
생성할 수 있습니다.

#### NOTE
독립형 애플리케이션(파이썬에서 시뮬레이터를 실행하는 경우)의 경우, 클래스가 초기화될 때
이 함수가 자동으로 호출됩니다.

그러나 확장 모드에서는 사용자가 시뮬레이터를 리셋한 후에 이 함수를 수동으로 호출해야 합니다.
이는 시뮬레이터가 사용자가 `SimulationContext.reset_async()`를 호출할 때만 리셋되며,
생성자에서는 비동기 함수를 호출할 수 없기 때문입니다.

#### *property* max_episode_length *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 단계 기준 최대 에피소드 길이.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

초 기준 최대 에피소드 길이.

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

환경의 내부 `_np_random`을 반환하며, 설정되지 않은 경우 랜덤 시드로 초기화됩니다.

* **반환값:**
  np.random.Generator 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 내부 `_np_random_seed`를 반환하며, 설정되지 않은 경우 랜덤 정수로 시드를 초기화합니다.

[`np_random_seed`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.np_random_seed)를
[`reset()`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.reset) 또는 `set_np_random_through_seed()`를 통하지 않고
직접 설정했다면, 시드는 -1이 됩니다.

* **반환값:**
  현재 np_random의 시드 또는 rng의 시드를 알 수 없는 경우 -1
* **반환 타입:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

실행 중인 환경 인스턴스의 수.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 타임스텝(초 단위).

이는 시뮬레이션이 이루어지는 최소 시간 디시메이션 수준입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 단계를 진행하지 않고 렌더링을 실행합니다.

관례에 따라 모드가 다음과 같을 때:

- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않음. 보통 인간용으로 사용.
- **rgb_array**: (x, y, 3) 형태의 numpy.ndarray를 반환하며, x-by-y 픽셀 이미지의 RGB 값을
  나타내고 비디오로 변환하기에 적합함.

* **매개변수:**
  **recompute** – 시뮬레이터가 이미 장면을 렌더링했더라도 강제로 렌더링할지 여부.
  기본값은 False입니다.
* **반환값:**
  모드가 “rgb_array”인 경우 numpy 배열 형태의 렌더링된 이미지. 그렇지 않으면 None.
* **예외:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 모드가 “rgb_data”로 설정되었지만 시뮬레이터 렌더 모드가 이를 지원하지 않는 경우.
        이 경우 시뮬레이터 렌더 모드는 `RenderMode.PARTIAL_RENDERING` 또는
        `RenderMode.FULL_RENDERING`로 설정해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정되었을 때.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

지정된 환경을 리셋하고 관측값을 반환합니다.

이 함수는 지정된 환경을 리셋하기 위해 `_reset_idx()` 함수를 호출합니다.
그러나 초기화 중에 발생한 프로시저얼 지형 생성과 같은 작업은 반복되지 않습니다.

* **매개변수:**
  * **seed** – 무작위 생성에 사용할 시드. 기본값은 None이며, 이 경우 시드는 설정되지 않음.
  * **env_ids** – 리셋할 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 리셋됨.
  * **options** – 

    환경 리셋 방식을 지정하기 위한 추가 정보. 기본값은 None입니다.

    #### NOTE
    이 인수는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **반환값:**
  관측값과 추가 정보를 담은 튜플.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

지정된 환경을 제공된 상태로 초기화합니다.

이 함수는 환경을 제공된 상태로 초기화합니다. 상태는 장면 엔티티의 상태를 포함하는 사전입니다. 형식에 대해서는 `InteractiveScene.get_state()` 를 참조하십시오.

이 함수는 [`reset()`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env.RmpFlowGalbotCubeStackAbsMimicEnv.reset) 함수와 다릅니다. 여기서 환경을 특정 상태로 초기화하는 것이 아니라, 환경을 초기화하기 위해 랜덤화 이벤트를 사용하기 때문입니다.

* **매개변수:**
  * **state** – 지정된 환경을 초기화할 상태입니다. 형식에 대해서는 `InteractiveScene.get_state()` 를 참조하십시오.
  * **env_ids** – 초기화할 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 초기화됩니다.
  * **seed** – 랜덤화에 사용할 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **is_relative** – True로 설정된 경우, 상태는 환경 원점을 기준으로 상대적이라고 간주됩니다. 기본값은 False입니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **매개변수:**
  **seed** – 랜덤 생성기에 사용할 시드입니다. 기본값은 -1입니다.
* **반환값:**
  랜덤 생성기에 사용된 시드입니다.

#### serialize()

이 환경을 다시 인스턴스화하기 위해 필요한 모든 정보를 사전에 저장합니다.
이는 @env_meta - HDF5 데이터셋에 저장된 환경 메타데이터와 동일하며, utils/env_utils.py에서 사용됩니다.

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

Wrapper.set_wrapper_attr에서 더 자세한 정보를 참조하여, 값으로 환경의 속성 이름을 설정합니다.

#### setup_manager_visualizers()

관리자 항목에 대한 라이브 시각화 도구를 생성합니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동역학에 대한 한 타임스텝을 실행하고 종료된 환경을 초기화합니다.

`ManagerBasedEnv.step` 클래스와 달리, 이 함수는 다음 작업을 수행합니다:

1. 동작을 처리합니다.
2. 물리 스텝을 수행합니다.
3. GUI가 활성화된 경우 렌더링을 수행합니다.
4. 환경 카운터를 업데이트하고 보상 및 종료 조건을 계산합니다.
5. 종료된 환경을 초기화합니다.
6. 관측값을 계산합니다.
7. 관측값, 보상, 리셋(종료 및 절단), 추가 정보를 반환합니다.

* **매개변수:**
  **action** – 환경에 적용할 동작입니다. 모양은 (num_envs, action_dim)입니다.
* **반환값:**
  관측값, 보상, 리셋(종료 및 절단), 추가 정보를 포함하는 튜플입니다.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스테핑 타임스텝(초 단위).

이 값은 환경이 앞으로 진행되는 타임스텝입니다.

#### target_eef_pose_to_action(target_eef_pose_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), gripper_action_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), noise: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_id: [int](https://docs.python.org/3/library/functions.html#int) = 0) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

목표 자세를 동작으로 변환합니다.

이 메서드는 대상 엔드 이펙터 자세와 그리퍼 동작을 포함하는 사전을 단일 동작 텐서로 변환하여 환경에서 사용할 수 있도록 합니다.

이 함수는 다음 단계를 수행합니다:
1. 자세 사전에서 대상 위치와 회전을 추출합니다.
2. 엔드 이펙터에 대한 그리퍼 동작을 추출합니다.
3. 위치와 쿼터니언 회전을 연결하여 자세 동작을 만듭니다.
4. 탐색을 위해 선택적으로 자세 동작에 노이즈를 추가합니다.
5. 자세 동작과 그리퍼 동작을 결합하여 최종 동작 텐서를 만듭니다.

* **매개변수:**
  * **target_eef_pose_dict** – 대상 엔드 이펙터 자세를 포함하는 사전으로, 키는 eef 이름이고 값은 자세 텐서입니다.
  * **gripper_action_dict** – 그리퍼 동작을 포함하는 사전으로, 키는 eef 이름이고 값은 동작 텐서입니다.
  * **noise** – 탐색을 위해 자세 동작에 적용할 선택적 노이즈 크기입니다. 제공된 경우, 랜덤 노이즈가 생성되어 자세 동작에 추가됩니다.
  * **env_id** – 다중 환경 설정에서의 환경 ID로, 기본값은 0입니다.
* **반환값:**
  자세와 그리퍼 명령을 결합한 단일 동작 텐서입니다.
* **반환 타입:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### *property* unwrapped *: [Env](https://gymnasium.farama.org/api/env/#gymnasium.Env)[ObsType, ActType]*

기본 래핑되지 않은 환경을 반환합니다.

* **반환값:**
  기본 래핑되지 않은 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인스턴스
* **반환 타입:**
  Env

#### cfg *: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg)*

환경 구성입니다.

### Galbot 왼쪽 팔 그립퍼 큐브 스택 Abs Mimic Env 구성

### *class* isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg

Bases: `RmpFlowGalbotLeftArmCubeStackEnvCfg`, [`MimicEnvCfg`](../lab/isaaclab.envs.md#isaaclab.envs.MimicEnvCfg)

Isaac Lab Mimic 환경 구성 클래스: Galbot 그립퍼 큐브 스택 IK 상대 환경용.

**메서드:**

| [`__init__`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.__init__)([datagen_config, subtask_configs, ...])   |    |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----|

**클래스:**

| [`ui_window_class_type`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.ui_window_class_type)   | alias of [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)   |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|

**속성:**

| [`viewer`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.viewer)                                 | 뷰어 구성입니다.                                                                       |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.sim)                                       | 물리 시뮬레이션 구성입니다.                                                           |
| [`seed`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.seed)                                     | 랜덤 넘버 생성기의 시드입니다.                                                   |
| [`decimation`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.decimation)                         | 정책 dt당 sim dt당 제어 동작 업데이트 횟수입니다.                                    |
| [`scene`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.scene)                                   | 장면 설정입니다.                                                                             |
| [`recorders`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.recorders)                           | 레코더 설정.                                                                          |
| [`observations`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.observations)                     | 관측 공간 설정.                                                                 |
| [`actions`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.actions)                               | 액션 공간 설정.                                                                      |
| [`events`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.events)                                 | 이벤트 설정.                                                                             |
| [`rerender_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.rerender_on_reset)           | 적어도 하나의 환경이 리셋된 후에 렌더 단계가 다시 수행되는지 여부.     |
| [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.num_rerenders_on_reset) | 리셋 후 수행할 렌더 단계 수.                                              |
| [`wait_for_textures`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.wait_for_textures)           | 에셋이 완전히 로드될 때까지 대기하려면 True, 그렇지 않으면 False.                           |
| [`xr`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.xr)                                         | XR 장치를 통해 환경 보기 및 상호작용을 위한 구성.        |
| [`teleop_devices`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.teleop_devices)                 | 원격 조작 장치 구성.                                                    |
| [`export_io_descriptors`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.export_io_descriptors)   | 환경의 IO 설명자를 내보내는지 여부.                                   |
| [`log_dir`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.log_dir)                               | 실험 아티팩트를 로깅할 디렉터리.                                                 |
| [`is_finite_horizon`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.is_finite_horizon)           | 학습 작업이 에이전트에 대해 유한 또는 무한 호라이즌 문제로 취급되는지 여부. |
| [`episode_length_s`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.episode_length_s)             | 에피소드 길이(초 단위).                                                        |
| [`rewards`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.rewards)                               | 보상 설정.                                                                            |
| [`terminations`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.terminations)                     | 종료 설정.                                                                       |
| [`curriculum`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.curriculum)                         | 교육 과정 설정.                                                                        |
| [`commands`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.commands)                             | 명령 설정.                                                                           |

#### \_\_init_\_(datagen_config: ~isaaclab.envs.mimic_env_cfg.DataGenConfig = <factory>, subtask_configs: dict[str, list[isaaclab.envs.mimic_env_cfg.SubTaskConfig]] = <factory>, task_constraint_configs: list[isaaclab.envs.mimic_env_cfg.SubTaskConstraintConfig] = <factory>, mimic_recorder_config: ~isaaclab.managers.recorder_manager.RecorderManagerBaseCfg | None = <factory>, viewer: ~isaaclab.envs.common.ViewerCfg = <factory>, sim: ~isaaclab.sim.simulation_cfg.SimulationCfg = <factory>, ui_window_class_type: type | None = <class 'isaaclab.envs.ui.manager_based_rl_env_window.ManagerBasedRLEnvWindow'>, seed: int | None = <factory>, decimation: int = <factory>, scene: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObjectTableSceneCfg = <factory>, recorders: object = <factory>, observations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObservationsCfg = <factory>, actions: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ActionsCfg = <factory>, events: object = <factory>, rerender_on_reset: bool = <factory>, num_rerenders_on_reset: int = <factory>, wait_for_textures: bool = <factory>, xr: ~isaaclab.devices.openxr.xr_cfg.XrCfg = <factory>, teleop_devices: ~isaaclab.devices.device_base.DevicesCfg = <factory>, export_io_descriptors: bool = <factory>, log_dir: str | None = <factory>, is_finite_horizon: bool = <factory>, episode_length_s: float = <factory>, rewards: object = <factory>, terminations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.TerminationsCfg = <factory>, curriculum: object | None = <factory>, commands: object | None = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### ui_window_class_type

[`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭

#### viewer *: [ViewerCfg](../lab/isaaclab.envs.md#isaaclab.envs.ViewerCfg)*

뷰어 구성. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](../lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성. 기본값은 SimulationCfg()입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 시 설정됩니다. 이렇게 하면 환경 생성이 결정적이며 다양한 실행에서 동일하게 동작함을 보장합니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

시뮬레이션 dt당 정책 dt당 제어 액션 업데이트 횟수.

예를 들어, 시뮬레이션 dt가 0.01s이고 정책 dt가 0.1s이면 디메이션은 10입니다. 이는 제어 액션이 10번째 시뮬레이션 단계마다 업데이트됨을 의미합니다.

#### scene *: ObjectTableSceneCfg*

씬 설정.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](../lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스를 참조하세요.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정. 기본값은 아무것도 기록하지 않습니다.

자세한 내용은 [`isaaclab.managers.RecorderManager`](../lab/isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참조하세요.

#### observations *: ObservationsCfg*

관측 공간 설정.

자세한 내용은 [`isaaclab.managers.ObservationManager`](../lab/isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참조하세요.

#### actions *: ActionsCfg*

액션 공간 설정.

자세한 내용은 [`isaaclab.managers.ActionManager`](../lab/isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참조하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정. 기본값은 씬을 기본 상태로 재설정하는 기본 구성이며, 자세한 내용은 [`isaaclab.managers.EventManager`](../lab/isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참조하세요.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

하나 이상의 환경이 리셋된 후 렌더 단계가 다시 수행되는지 여부. 기본값은 False로, 리셋 후에 렌더 단계가 수행되지 않음을 의미합니다.

* False일 때, 리셋 후 센서에서 수집된 데이터는 오래되고 시뮬레이션에서 리셋으로 인한 최신 상태를 반영하지 않습니다.
* True일 때, 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 리셋으로 인한 최신 상태를 반영합니다. 이는 추가 렌더 단계가 각 환경 리셋 후에 수행되므로 성능에 비용이 발생합니다.

#### Deprecated
버전 2.3.1부터 지원 중단됨: 이 특성은 지원 중단되었으며 향후 제거됩니다. 대신 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.num_rerenders_on_reset)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정했을 때와 동일하게 동작하도록 하려면 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotLeftArmGripperCubeStackAbsMimicEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하세요.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

리셋 후 수행할 렌더 단계 수. 기본값은 0으로, 리셋 후 렌더 단계가 수행되지 않음을 의미합니다.

* 0일 때, 리셋 후 렌더 단계가 수행되지 않으며, 리셋 후 센서에서 수집된 데이터는 오래됩니다.
  reset이 오래된 상태가 되어 시뮬레이션에서 리셋으로 인한 최신 상태를 반영하지 않습니다.
* 이 값이 0보다 클 때, 지정된 수의 추가 렌더 단계가 수행되어 리셋으로부터 최신 상태를 반영하는 센서 데이터가 업데이트됩니다. 이는 추가 렌더 단계가 각 환경 리셋 후 수행되므로 성능 저하를 초래합니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

애셋이 완전히 로드될 때까지 기다리려면 True, 그렇지 않으면 False. 기본값은 True입니다.

#### xr *: XrCfg*

XR 장치를 통해 환경 보기 및 상호 작용을 위한 구성입니다.

#### teleop_devices *: DevicesCfg*

텔레오퍼레이션 장치를 위한 구성입니다.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 디스크립터를 내보낼지 여부입니다. 기본값은 False입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 기록할 디렉터리입니다. 기본값은 None으로, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

학습 작업이 에이전트에 대해 유한 horizon 또는 무한 horizon 문제로 취급되는지 여부입니다. 기본값은 False로, 이는 작업이 무한 horizon 문제로 취급됨을 의미합니다.

이 플래그는 유한 horizon 및 무한 horizon 작업의 미묘한 차이를 처리합니다:

* **유한 horizon**: 시간이 초과되었을 때 에이전트는 패널티 또는 부트스트래핑 값이 필요하지 않습니다. 그러나 환경은 시간 제한에 도달했을 때 에피소드를 여전히 종료해야 합니다.
* **무한 horizon**: 에이전트는 에피소드 종료 시 상태의 값을 부트스트래핑해야 합니다. 이는 시간 제한(또는 잘림) 완료 신호를 에이전트에 보내서 이 부트스트래핑 계산을 트리거함으로써 수행됩니다.

True이면, 환경은 유한 horizon 문제로 취급되고 타임아웃(또는 잘림) 완료 신호가 에이전트에 전송되지 않습니다. False이면, 환경은 무한 horizon 문제로 취급되고 타임아웃(또는 잘림) 완료 신호가 에이전트에 전송됩니다.

#### NOTE
기본 `ManagerBasedRLEnv` 클래스는 이 플래그를 직접 사용하지 않습니다. 이 플래그는 환경 래퍼에서 사용되어 해당 학습 에이전트에 어떤 유형의 완료 신호를 보낼지 결정합니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 지속 시간(초 단위).

디메이션 비율과 물리 시간 단계에 따라 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디메이션 비율이 10이고 물리 시간 단계가 0.01이며 에피소드 길이가 10초인 경우,
에피소드 길이(스텝 단위)는 100이 됩니다.

#### rewards *: [object](https://docs.python.org/3/library/functions.html#object)*

보상 설정.

자세한 내용은 [`isaaclab.managers.RewardManager`](../lab/isaaclab.managers.md#isaaclab.managers.RewardManager) 클래스를 참조하십시오.

#### terminations *: TerminationsCfg*

종료 설정.

자세한 내용은 [`isaaclab.managers.TerminationManager`](../lab/isaaclab.managers.md#isaaclab.managers.TerminationManager) 클래스를 참조하십시오.

#### curriculum *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

커리큘럼 설정. 기본값은 None으로, 이 경우 커리큘럼이 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.CurriculumManager`](../lab/isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 참조하십시오.

#### commands *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

명령 설정. 기본값은 None으로, 이 경우 명령이 생성되지 않습니다.

자세한 내용은 [`isaaclab.managers.CommandManager`](../lab/isaaclab.managers.md#isaaclab.managers.CommandManager) 클래스를 참조하십시오.

### Galbot Right Arm Suction Cube Stack Abs Mimic Env Cfg

### *class* isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg

Bases: `RmpFlowGalbotRightArmCubeStackEnvCfg`, [`MimicEnvCfg`](../lab/isaaclab.envs.md#isaaclab.envs.MimicEnvCfg)

Isaac Lab Mimic 환경 구성 클래스: Galbot 흡착 그리퍼 큐브 스택 RmpFlow Abs 환경용.

**메서드:**

| [`__init__`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.__init__)([datagen_config, subtask_configs, ...])   |    |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----|

**클래스:**

| [`ui_window_class_type`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.ui_window_class_type)   | alias of [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)   |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|

**속성:**

| [`viewer`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.viewer)                                 | 뷰어 구성입니다.                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.sim)                                       | 물리 시뮬레이션 구성입니다.                                                           |
| [`seed`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.seed)                                     | 난수 생성기의 시드입니다.                                                   |
| [`decimation`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.decimation)                         | 정책 dt당 sim dt에서의 제어 액션 업데이트 수.                                    |
| [`scene`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.scene)                                   | 장면 설정입니다.                                                                             |
| [`recorders`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.recorders)                           | 레코더 설정입니다.                                                                          |
| [`observations`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.observations)                     | 관측 공간 설정입니다.                                                                 |
| [`actions`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.actions)                               | 액션 공간 설정입니다.                                                                      |
| [`events`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.events)                                 | 이벤트 설정입니다.                                                                             |
| [`rerender_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.rerender_on_reset)           | 적어도 하나의 환경이 리셋된 후 렌더 단계가 다시 수행되는지 여부입니다.     |
| [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.num_rerenders_on_reset) | 리셋 후 수행할 렌더 단계 수.                                              |
| [`wait_for_textures`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.wait_for_textures)           | 애셋이 완전히 로드될 때까지 기다리려면 True, 그렇지 않으면 False.                           |
| [`xr`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.xr)                                         | XR 장치를 통해 환경 보기 및 상호 작용을 위한 구성입니다.        |
| [`teleop_devices`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.teleop_devices)                 | 텔레오퍼레이션 장치를 위한 구성입니다.                                                    |
| [`export_io_descriptors`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.export_io_descriptors)   | 환경의 IO 디스크립터를 내보낼지 여부입니다.                                   |
| [`log_dir`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.log_dir)                               | 실험 아티팩트를 기록할 디렉터리입니다.                                                 |
| [`is_finite_horizon`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.is_finite_horizon)           | 에이전트가 학습 작업을 유한 호라이즌 문제인지 무한 호라이즌 문제인지 여부를 나타냅니다. |
| [`episode_length_s`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.episode_length_s)             | 에피소드 지속 시간(초 단위).                                                                |
| [`rewards`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.rewards)                               | 보상 설정.                                                                                   |
| [`terminations`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.terminations)                     | 종료 설정.                                                                                   |
| [`curriculum`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.curriculum)                         | 커리큘럼 설정.                                                                               |
| [`commands`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.commands)                             | 명령 설정.                                                                                   |

#### \_\_init_\_(datagen_config: ~isaaclab.envs.mimic_env_cfg.DataGenConfig = <factory>, subtask_configs: dict[str, list[isaaclab.envs.mimic_env_cfg.SubTaskConfig]] = <factory>, task_constraint_configs: list[isaaclab.envs.mimic_env_cfg.SubTaskConstraintConfig] = <factory>, mimic_recorder_config: ~isaaclab.managers.recorder_manager.RecorderManagerBaseCfg | None = <factory>, viewer: ~isaaclab.envs.common.ViewerCfg = <factory>, sim: ~isaaclab.sim.simulation_cfg.SimulationCfg = <factory>, ui_window_class_type: type | None = <class 'isaaclab.envs.ui.manager_based_rl_env_window.ManagerBasedRLEnvWindow'>, seed: int | None = <factory>, decimation: int = <factory>, scene: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObjectTableSceneCfg = <factory>, recorders: object = <factory>, observations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObservationsCfg = <factory>, actions: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ActionsCfg = <factory>, events: object = <factory>, rerender_on_reset: bool = <factory>, num_rerenders_on_reset: int = <factory>, wait_for_textures: bool = <factory>, xr: ~isaaclab.devices.openxr.xr_cfg.XrCfg = <factory>, teleop_devices: ~isaaclab.devices.device_base.DevicesCfg = <factory>, export_io_descriptors: bool = <factory>, log_dir: str | None = <factory>, is_finite_horizon: bool = <factory>, episode_length_s: float = <factory>, rewards: object = <factory>, terminations: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.TerminationsCfg = <factory>, curriculum: object | None = <factory>, commands: object | None = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### ui_window_class_type

[`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭입니다.

#### viewer *: [ViewerCfg](../lab/isaaclab.envs.md#isaaclab.envs.ViewerCfg)*

뷰어 구성입니다. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](../lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성입니다. 기본값은 SimulationCfg()입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 시에 설정됩니다. 이를 통해 환경 생성이 결정적이며 다른 실행 간에도 동일하게 동작합니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

정책 dt당 시뮬레이션 dt에 대한 제어 행동 업데이트 횟수입니다.

예를 들어, 시뮬레이션 dt가 0.01초이고 정책 dt가 0.1초라면, 디시메이션은 10입니다.
즉, 제어 행동이 10개의 시뮬레이션 단계마다 한 번 업데이트된다는 의미입니다.

#### scene *: ObjectTableSceneCfg*

장면 설정입니다.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](../lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스를 참조하세요.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정입니다. 기본값은 아무것도 기록하지 않습니다.

자세한 내용은 [`isaaclab.managers.RecorderManager`](../lab/isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참조하세요.

#### observations *: ObservationsCfg*

관찰 공간 설정입니다.

자세한 내용은 [`isaaclab.managers.ObservationManager`](../lab/isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참조하세요.

#### actions *: ActionsCfg*

작업 공간 설정입니다.

자세한 내용은 [`isaaclab.managers.ActionManager`](../lab/isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참조하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정입니다. 기본값은 장면을 초기 상태로 재설정하는 기본 구성입니다.

자세한 내용은 [`isaaclab.managers.EventManager`](../lab/isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참조하세요.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

최소한 하나의 환경이 재설정된 후에 렌더 단계가 다시 수행되는지 여부를 나타냅니다. 기본값은 False로, 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 False인 경우, 재설정 후 센서에서 수집된 데이터는 오래되었으며 시뮬레이션에서 재설정으로 인한 최신 상태를 반영하지 않습니다.
* 이 값이 True인 경우, 추가 렌더 단계가 수행되어 센서 데이터를 재설정으로부터 최신 상태로 업데이트합니다. 이는 성능에 영향을 미치며, 추가 렌더 단계가 각 환경 재설정 후에 수행됩니다.

#### Deprecated
버전 2.3.1부터 사용이 중단되었습니다: 이 속성은 사용되지 않으며 향후 버전에서 제거됩니다. 대신 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.num_rerenders_on_reset)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정했을 때의 동작과 동일하게 만들려면
[`num_rerenders_on_reset`](#isaaclab_mimic.envs.galbot_stack_rmp_abs_mimic_env_cfg.RmpFlowGalbotRightArmSuctionCubeStackAbsMimicEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하세요.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후에 수행할 렌더 단계 수입니다. 기본값은 0으로, 재설정 후에 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 0인 경우, 재설정 후 렌더 단계가 수행되지 않으며, 센서에서 수집된 데이터는 오래되었으며 시뮬레이션에서 재설정으로 인한 최신 상태를 반영하지 않습니다.
* 이 값이 0보다 큰 경우, 지정된 수의 추가 렌더 단계가 수행되어 센서 데이터를 재설정으로부터 최신 상태로 업데이트합니다. 이는 성능에 영향을 미치며, 추가 렌더 단계가 각 환경 재설정 후에 수행됩니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 완전히 로드될 때까지 대기하려면 True, 그렇지 않으면 False입니다. 기본값은 True입니다.

#### xr *: XrCfg*

XR 장치를 통해 환경을 보고 상호작용하기 위한 구성입니다.

#### teleop_devices *: DevicesCfg*

원격 조작 장치를 위한 구성입니다.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 설명자를 내보낼지 여부를 나타냅니다. 기본값은 False입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 기록할 디렉터리입니다. 기본값은 None이며, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

에이전트가 학습 작업을 유한 호라이즌 문제인지 무한 호라이즌 문제인지 여부를 나타냅니다. 기본값은 False로, 작업이 무한 호라이즌 문제로 처리됨을 의미합니다.

이 플래그는 유한 호라이즌과 무한 호라이즌 작업의 미묘한 차이를 처리합니다:

* **유한 호라이즌**: 시간이 다 소진되어도 에이전트에게 패널티나 부트스트래핑 값이 필요하지 않습니다. 그러나 환경은 시간 제한에 도달한 후 에피소드를 여전히 종료해야 합니다.
* **무한 호라이즌**: 에피소드 종료 시 상태의 값을 부트스트래핑해야 하는 에이전트에게 필요합니다. 이는 시간 제한(또는 잘림) 완료 신호를 에이전트에 전송하여 이루어지며, 이 신호가 부트스트래핑 계산을 트리거합니다.

True이면 환경은 유한 호라이즌 문제로 처리되며 타임아웃(또는 잘림) 완료 신호가 에이전트에 전송되지 않습니다. False이면 환경은 무한 호라이즌 문제로 처리되며 타임아웃(또는 잘림) 완료 신호가 에이전트에 전송됩니다.

#### NOTE
기본 `ManagerBasedRLEnv` 클래스는 이 플래그를 직접 사용하지 않습니다. 이 플래그는 환경 래퍼에서 사용되어 해당 학습 에이전트에게 전송할 완료 신호의 유형을 결정합니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 지속 시간(초 단위).

디시메이션 비율과 물리 시간 단계에 따라 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디시메이션 비율이 10이고, 물리 시간 단계가 0.01이며, 에피소드 길이가 10초인 경우,
episode 길이(스텝 기준)는 100입니다.

#### rewards *: [object](https://docs.python.org/3/library/functions.html#object)*

보상 설정.

자세한 내용은 [`isaaclab.managers.RewardManager`](../lab/isaaclab.managers.md#isaaclab.managers.RewardManager) 클래스를 참조하세요.

#### terminations *: TerminationsCfg*

종료 설정.

자세한 내용은 [`isaaclab.managers.TerminationManager`](../lab/isaaclab.managers.md#isaaclab.managers.TerminationManager) 클래스를 참조하세요.

#### curriculum *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

커리큘럼 설정. 기본값은 None이며, 이 경우 커리큘럼이 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.CurriculumManager`](../lab/isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 참조하세요.

#### commands *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

명령 설정. 기본값은 None이며, 이 경우 명령이 생성되지 않습니다.

자세한 내용은 [`isaaclab.managers.CommandManager`](../lab/isaaclab.managers.md#isaaclab.managers.CommandManager) 클래스를 참조하세요.

## Agibot Environments

### Pick Place Rel Mimic Env

### *class* isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv

Bases: [`FrankaCubeStackIKRelMimicEnv`](#isaaclab_mimic.envs.franka_stack_ik_rel_mimic_env.FrankaCubeStackIKRelMimicEnv)

Isaac Lab Mimic 환경 래퍼 클래스 for DiffIK / RmpFlow 상대 포즈 제어 환경.

이 MimicEnv는 모든 관찰이 로봇 기준 프레임에 있을 때 사용됩니다.

**메서드:**

| [`get_object_poses`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.get_object_poses)([env_ids])                             | 로봇 기준 프레임에서 각 객체(강성 객체 및 연결된 객체 포함)의 포즈를 가져옵니다.                                                                    |
|----------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`get_subtask_term_signals`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.get_subtask_term_signals)([env_ids])             | 작업의 각 서브태스크에 대한 종료 신호 플래그를 담은 사전을 가져옵니다.                                                                                                  |
| [`__init__`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.__init__)(cfg[, render_mode])                                    | 환경을 초기화합니다.                                                                                                                                                |
| [`action_to_target_eef_pose`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.action_to_target_eef_pose)(action)              | env.step과 호환되는 행동을 엔드 이펙터 컨트롤러의 목표 포즈로 변환합니다.                                                                               |
| [`actions_to_gripper_actions`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.actions_to_gripper_actions)(actions)           | env.step과 호환되는 환경 행동 시퀀스에서 그리퍼 구동 부분을 추출합니다.                                                                             |
| [`close`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.close)()                                                            | 환경 정리를 수행합니다.                                                                                                                                               |
| [`export_IO_descriptors`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.export_IO_descriptors)([output_dir])                | 환경의 IO 디스크립터를 내보냅니다.                                                                                                                             |
| [`get_expected_attached_object`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.get_expected_attached_object)(eef_name, ...) | (SkillGen) 주어진 EEF/서브태스크에 대한 기대 연결 객체를 반환합니다.                                                                                                  |
| [`get_navigation_state`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.get_navigation_state)([env_ids])                     | 선택적 메서드입니다.                                                                                                                                                           |
| [`get_robot_eef_pose`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.get_robot_eef_pose)(eef_name[, env_ids])               | 현재 로봇 엔드 이펙터 포즈를 가져옵니다.                                                                                                                                       |
| [`get_subtask_start_signals`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.get_subtask_start_signals)([env_ids])           | 작업의 각 서브태스크에 대한 시작 신호 플래그를 담은 사전을 가져옵니다.                                                                                                        |
| [`get_wrapper_attr`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.get_wrapper_attr)(name)                                  | 환경에서 속성 이름을 가져옵니다.                                                                                                                              |
| [`has_wrapper_attr`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.has_wrapper_attr)(name)                                  | 환경에 속성 이름이 존재하는지 확인합니다.                                                                                                                    |
| [`load_managers`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.load_managers)()                                            | 환경의 관리자를 로드합니다.                                                                                                                                     |
| [`render`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.render)([recompute])                                               | 물리 단계를 거치지 않고 렌더링을 실행합니다.                                                                                                                        |
| [`reset`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.reset)([seed, env_ids, options])                                    | 지정된 환경을 리셋하고 관측값을 반환합니다.                                                                                                                |
| [`reset_to`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.reset_to)(state, env_ids[, seed, is_relative])                   | 지정된 환경을 제공된 상태로 리셋합니다.                                                                                                                          |
| [`seed`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.seed)([seed])                                                        | 환경의 시드를 설정합니다.                                                                                                                                          |
| [`serialize`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.serialize)()                                                    | 이 환경을 다시 인스턴스화하는 데 필요한 모든 정보를 사전에 저장합니다.                                                                                            |
| [`set_wrapper_attr`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.set_wrapper_attr)(name, value, \*[, force])              | 지정된 값으로 환경의 속성 이름을 설정합니다. 자세한 내용은 Wrapper.set_wrapper_attr을 참조하세요.                                                                         |
| [`setup_manager_visualizers`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.setup_manager_visualizers)()                    | 관리자 항목에 대한 라이브 시각화 도구를 생성합니다.                                                                                                                                |
| [`step`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.step)(action)                                                        | 환경의 동역학을 한 타임스텝 실행하고 종료된 환경을 리셋합니다.                                                                                     |
| [`target_eef_pose_to_action`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.target_eef_pose_to_action)(...[, ...])          | 엔드 이펙터 컨트롤러의 목표 포즈와 그리퍼 동작을 취하고, 일반적으로 정규화된 델타 포즈 동작인 해당 목표 포즈를 달성하기 위한 동작을 반환합니다. |

**속성:**

| [`device`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.device)                             | 환경이 실행 중인 디바이스입니다.                                                                       |
|---------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`get_IO_descriptors`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.get_IO_descriptors)     | 환경의 IO 디스크립터를 가져옵니다.                                                                           |
| [`is_vector_env`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.is_vector_env)               | 환경이 벡터화된 환경인지 여부를 나타냅니다.                                                                  |
| [`max_episode_length`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.max_episode_length)     | 환경 스텝 기준 최대 에피소드 길이.                                                                          |
| [`max_episode_length_s`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.max_episode_length_s) | 초 단위 최대 에피소드 길이.                                                                                    |
| [`metadata`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.metadata)                         | 환경의 메타데이터.                                                                                         |
| [`np_random`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.np_random)                       | 설정되지 않은 경우 랜덤 시드로 초기화되는 환경의 내부 `_np_random`을 반환합니다.                   |
| [`np_random_seed`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.np_random_seed)             | 설정되지 않은 경우 랜덤 정수로 먼저 초기화되는 환경의 내부 `_np_random_seed`를 반환합니다. |
| [`num_envs`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.num_envs)                         | 현재 실행 중인 환경 인스턴스의 수.                                                          |
| [`physics_dt`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.physics_dt)                     | 물리 시간 간격(초 단위).                                                                                         |
| [`step_dt`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.step_dt)                           | 환경 스텝 시간 간격(초 단위).                                                                            |
| [`unwrapped`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.unwrapped)                       | 기본 래핑되지 않은 환경을 반환합니다.                                                                             |
| [`cfg`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.cfg)                                   | 환경의 구성.                                                                                    |

#### get_object_poses(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

로봇 베이스 프레임에서의 각 객체( rigid 객체 및 articulated 객체 포함)의 포즈를 가져옵니다.

* **매개변수:**
  **env_ids** – 포즈를 가져올 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  객체 이름을 객체 포즈 매트릭스(robot base 프레임 기준 4x4 torch.Tensor)에 매핑하는 딕셔너리

#### get_subtask_term_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

태스크의 각 서브태스크에 대한 종료 신호 플래그의 딕셔너리를 가져옵니다. 서브태스크가 완료되면 플래그는 1, 그렇지 않으면 0입니다. 자동 서브태스크 종료 신호 어노테이션을 활성화하려면 이 메서드를 구현해야 합니다. 수동 어노테이션을 사용할 계획이라면 이 메서드를 구현하지 않은 상태로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 종료 신호를 가져올 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  각 서브태스크의 종료 신호 플래그(False 또는 True)를 포함하는 딕셔너리

#### \_\_init_\_(cfg: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성.
  * **render_mode** – 환경의 렌더 모드. 기본값은 None이며, 이는 `"human"`과 유사합니다.

#### action_to_target_eef_pose(action: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

env.step과 호환되는 액션을 엔드 이펙터 컨트롤러의 목표 포즈로 변환합니다. @target_eef_pose_to_action의 역변환입니다. 일반적으로 기록된 액션으로부터 시연 경로의 목표 컨트롤러 포즈 시퀀스를 추론하는 데 사용됩니다.

* **매개변수:**
  **action** – 환경 액션. 형태는 (num_envs, action_dim)
* **반환값:**
  @action에 해당하는 엔드 이펙터 포즈 torch.Tensor의 딕셔너리

#### actions_to_gripper_actions(actions: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

env.step과 호환되는 환경 액션 시퀀스에서 그리퍼 액추에이션 부분을 추출합니다.

* **매개변수:**
  **actions** – 환경 액션. 형태는 (num_envs, 데모의 단계 수, action_dim).
* **반환값:**
  그리퍼 액션 torch.Tensor의 딕셔너리. 각 딕셔너리의 키는 eef_name입니다.

#### close()

환경 정리를 수행합니다.

#### *property* device

환경이 실행 중인 디바이스.

#### export_IO_descriptors(output_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

환경의 IO 디스크립터를 내보냅니다.

* **매개변수:**
  **output_dir** – IO 디스크립터를 내보낼 디렉터리.

#### *property* get_IO_descriptors

환경의 IO 디스크립터를 가져옵니다.

* **반환값:**
  그룹 이름을 키로, IO 디스크립터를 값으로 갖는 딕셔너리.

#### get_expected_attached_object(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), subtask_index: [int](https://docs.python.org/3/library/functions.html#int), env_cfg) → [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)

(SkillGen) 주어진 EEF/서브태스크에 대해 기대되는 연결된 객체를 반환합니다.

‘stack’ 서브태스크는 앞선 ‘grasp’ 서브태스크에서grasped된 객체를 놓는다고 가정합니다.
‘grasp’(또는 기타) 서브태스크 시작 시에는 None을 반환합니다.

#### get_navigation_state(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

선택적 메서드. 내비게이션 컨트롤러 로코맨필레이션 데이터 생성을 사용할 때만 필요합니다.

로봇의 내비게이션 상태를 가져옵니다. 내비게이션 컨트롤러 사용이 활성화된 경우 필수입니다. 내비게이션 상태에는 로봇이 내비게이션 컨트롤러의 제어 하에 있는지 여부를 나타내는 불리언 플래그 “is_navigating”과 내비게이션 목표가 도달되었는지 여부를 나타내는 불리언 플래그 “navigation_goal_reached”가 포함됩니다.

* **매개변수:**
  **env_ids** – 내비게이션 상태를 가져올 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  내비게이션 상태 플래그(False 또는 True)를 포함하는 딕셔너리.

#### get_robot_eef_pose(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

현재 로봇 엔드 이펙터 포즈를 가져옵니다. 로봇 엔드 이펙터 컨트롤러가 사용하는 프레임과 동일해야 합니다.

* **매개변수:**
  * **eef_name** – 엔드 이펙터의 이름.
  * **env_ids** – 포즈를 가져올 환경 인덱스. None이면 모든 환경이 고려됩니다.
* **반환값:**
  엔드 이펙터 포즈 매트릭스를 나타내는 torch.Tensor. 형태는 (len(env_ids), 4, 4)

#### get_subtask_start_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

태스크의 각 서브태스크에 대한 시작 신호 플래그의 딕셔너리를 가져옵니다. 서브태스크가 시작되면 플래그는 1, 그렇지 않으면 0입니다. 자동 서브태스크 시작 신호 어노테이션을 활성화하려면 이 메서드를 구현해야 합니다. 자동 어노테이션을 사용할 계획이라면 이 메서드를 구현해야 하며, 수동 어노테이션을 사용할 계획이라면 구현하지 않은 상태로 둘 수 있습니다.
데이터셋 어노테이션 도구. 이 메서드는 수동 서브태스크 시작 신호 어노테이션을 사용하려는 경우 구현하지 않은 채 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 시작 신호를 가져올 환경 인덱스. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  각 서브태스크에 대한 시작 신호 플래그(False 또는 True)를 포함하는 딕셔너리.

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 지정된 이름의 속성을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 지정된 이름의 속성이 존재하는지 확인합니다.

#### is_vector_env *: ClassVar[[bool](https://docs.python.org/3/library/functions.html#bool)]* *= True*

환경이 벡터화된 환경인지 여부.

#### load_managers()

환경의 관리자(manager)를 로드합니다.

이 함수는 환경의 다양한 관리자(액션, 관찰, 이벤트 등)를 생성하는 역할을 합니다. 관리자는 물리 핸들러에 접근해야 하므로 시뮬레이터가 리셋된 후에야(즉, 처음으로 플레이된 후에) 생성할 수 있습니다.

#### NOTE
독립형 애플리케이션의 경우(Python에서 시뮬레이터 실행 시), 클래스가 초기화될 때 이 함수가 자동으로 호출됩니다.

그러나 확장 모드에서는 시뮬레이터가 리셋된 후에 사용자가 이 함수를 수동으로 호출해야 합니다. 이는 시뮬레이터가 사용자가 `SimulationContext.reset_async()`를 호출할 때만 리셋되기 때문이며, 생성자에서는 비동기 함수를 호출할 수 없기 때문입니다.

#### *property* max_episode_length *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 단계 단위의 최대 에피소드 길이.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

초 단위의 최대 에피소드 길이.

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

환경의 내부 `_np_random`을 반환하며, 설정되지 않은 경우 난수 시드로 초기화됩니다.

* **반환값:**
  np.random.Generator 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 내부 `_np_random_seed`를 반환하며, 설정되지 않은 경우 난수 정수로 먼저 초기화됩니다.

[`np_random_seed`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.np_random_seed)를 [`reset()`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.reset)이나 `set_np_random_through_seed()`를 통해가 아니라 직접 설정했을 경우, 시드는 -1이 됩니다.

* **반환값:**
  현재 np_random의 시드 또는 rng의 시드가 알려지지 않은 경우 -1
* **반환 타입:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

실행 중인 환경 인스턴스의 수.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 시간 단계(초 단위).

시뮬레이션이 이루어지는 가장 낮은 시간 해상도입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 단계를 거치지 않고 렌더링을 실행합니다.

관례에 따라 모드가 다음과 같은 경우:

- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않음. 일반적으로 인간 소비를 위한 용도.
- **rgb_array**: (x, y, 3) 형태의 numpy.ndarray를 반환하며, x-by-y 픽셀 이미지의 RGB 값을 나타내고 비디오로 변환하기에 적합함.

* **매개변수:**
  **recompute** – 시뮬레이터가 이미 장면을 렌더링했더라도 강제로 렌더링을 수행할지 여부. 기본값은 False입니다.
* **반환값:**
  모드가 “rgb_array”인 경우 numpy 배열 형태의 렌더링된 이미지. 그렇지 않으면 None.
* **예외:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 모드가 “rgb_data”로 설정되었으나 시뮬레이터 렌더링 모드가 이를 지원하지 않는 경우.
       この場合、シミュレータのレンダーモードを `RenderMode.PARTIAL_RENDERING` または `RenderMode.FULL_RENDERING` に設定する必要があります。
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정되었을 때.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

지정된 환경을 리셋하고 관측값을 반환합니다.

이 함수는 지정된 환경을 리셋하기 위해 `_reset_idx()` 함수를 호출합니다. 다만, 초기화 중에 발생한 프로시저널 지형 생성과 같은 특정 작업은 반복되지 않습니다.

* **매개변수:**
  * **seed** – 랜덤화에 사용할 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **env_ids** – 리셋할 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 리셋됩니다.
  * **options** – 

    환경을 리셋하는 방법을 지정하는 추가 정보. 기본값은 None입니다.

    #### NOTE
    이 인자는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **반환값:**
  관측값과 추가 정보를 포함하는 튜플.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

지정된 환경을 제공된 상태로 리셋합니다.

이 함수는 제공된 상태로 환경을 리셋합니다. 상태는 씬 엔티티의 상태를 포함하는 딕셔너리이며, 형식은 `InteractiveScene.get_state()`를 참조하십시오.

이 함수는 [`reset()`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceRelMimicEnv.reset)와 다르게, 무작위화 이벤트를 사용하는 대신 특정 상태로 환경을 리셋합니다.

* **매개변수:**
  * **state** – 지정된 환경을 리셋할 상태. 형식은 `InteractiveScene.get_state()`를 참조하십시오.
  * **env_ids** – 리셋할 환경 ID. 기본값은 None이며, 이 경우 모든 환경이 리셋됩니다.
  * **seed** – 랜덤화에 사용할 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **is_relative** – True로 설정된 경우, 상태는 환경 원점에 상대적인 것으로 간주됩니다. 기본값은 False입니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **매개변수:**
  **seed** – 난수 생성기의 시드. 기본값은 -1입니다.
* **반환값:**
  난수 생성기에 사용된 시드.

#### serialize()

이 환경을 다시 인스턴스화하기 위해 필요한 모든 정보를 딕셔너리에 저장합니다.
이것은 @env_meta와 동일하며, HDF5 데이터셋에 저장된 환경 메타데이터로, utils/env_utils.py에서 사용됩니다.

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

지정된 이름의 속성을 환경에 값으로 설정합니다. 자세한 내용은 `Wrapper.set_wrapper_attr`를 참조하십시오.

#### setup_manager_visualizers()

관리자 항목에 대한 라이브 시각화 도구를 생성합니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동력을 한 타임스텝 실행하고 종료된 환경을 재설정합니다.

`ManagerBasedEnv.step` 클래스와 달리, 이 함수는 다음과 같은 작업을 수행합니다:

1. 행동 처리.
2. 물리 스텝 수행.
3. GUI가 활성화된 경우 렌더링 수행.
4. 환경 카운터 업데이트 및 보상과 종료 계산.
5. 종료된 환경 재설정.
6. 관측값 계산.
7. 관측값, 보상, 재설정(종료 및 자름) 및 추가 정보 반환.

* **매개변수:**
  **action** – 환경에 적용할 행동. 형태는 (num_envs, action_dim).
* **반환값:**
  관측값, 보상, 재설정(종료 및 자름), 추가 정보를 포함한 튜플.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스텝 타임스텝(초 단위).

이 값은 환경이 앞으로 진행하는 타임스텝입니다.

#### target_eef_pose_to_action(target_eef_pose_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), gripper_action_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), action_noise_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_id: [int](https://docs.python.org/3/library/functions.html#int) = 0) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

엔드 이펙터 컨트롤러용 목표 자세와 그리퍼 행동을 받아, 해당 목표 자세를 달성하기 위해 시도하는 행동(보통 정규화된 델타 자세 행동)을 반환합니다.
지정된 경우 목표 자세 행동에 노이즈가 추가됩니다.

* **매개변수:**
  * **target_eef_pose_dict** – 각 엔드 이펙터에 대한 4x4 목표 엔드 이펙터 자세의 딕셔너리.
  * **gripper_action_dict** – 각 엔드 이펙터에 대한 그리퍼 행동의 딕셔너리.
  * **action_noise_dict** – 행동에 추가할 노이즈. None이면 노이즈를 추가하지 않음.
  * **env_id** – 행동을 가져올 환경 인덱스.
* **반환값:**
  env.step()과 호환되는 행동 torch.Tensor.

#### *property* unwrapped *: [Env](https://gymnasium.farama.org/api/env/#gymnasium.Env)[ObsType, ActType]*

기본 래핑되지 않은 환경을 반환합니다.

* **반환값:**
  기본 래핑되지 않은 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인스턴스
* **반환 타입:**
  Env

#### cfg *: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg)*

환경 구성.

### Pick Place Abs Mimic Env

### *class* isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv

Bases: [`FrankaCubeStackIKAbsMimicEnv`](#isaaclab_mimic.envs.franka_stack_ik_abs_mimic_env.FrankaCubeStackIKAbsMimicEnv)

DiffIK / RmpFlow 절대 자세 제어 환경을 위한 Isaac Lab Mimic 환경 래퍼 클래스입니다.

이 MimicEnv는 모든 관측이 로봇 베이스 프레임에 있는 경우 사용됩니다.

**메서드:**

| [`get_object_poses`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.get_object_poses)([env_ids])                              | 로봇 베이스 프레임에서 각 객체의 자세( rigid 객체 및 관절 객체 포함)를 가져옵니다.   |
|-----------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| [`get_subtask_term_signals`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.get_subtask_term_signals)([env_ids])              | 작업의 각 서브태스크에 대한 종료 신호 플래그의 딕셔너리를 가져옵니다.                                 |
| [`__init__`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.__init__)(cfg[, render_mode])                                     | 환경 초기화.                                                                               |
| [`action_to_target_eef_pose`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.action_to_target_eef_pose)(action)               | 행동을 목표 자세로 변환.                                                                            |
| [`actions_to_gripper_actions`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.actions_to_gripper_actions)(actions)            | 그리퍼 행동 추출.                                                                                  |
| [`close`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.close)()                                                             | 환경 정리.                                                                              |
| [`export_IO_descriptors`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.export_IO_descriptors)([output_dir])                 | 환경의 IO 서술자 내보내기.                                                            |
| [`get_navigation_state`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.get_navigation_state)([env_ids])                      | 선택적 메서드.                                                                                          |
| [`get_robot_eef_pose`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.get_robot_eef_pose)(eef_name[, env_ids])                | 현재 로봇 엔드 이펙터 자세 가져오기.                                                                      |
| [`get_subtask_start_signals`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.get_subtask_start_signals)([env_ids])            | 작업의 각 서브태스크에 대한 시작 신호 플래그의 딕셔너리를 가져옵니다.                                       |
| [`get_wrapper_attr`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.get_wrapper_attr)(name)                                   | 환경에서 특정 속성 이름 가져오기.                                                             |
| [`has_wrapper_attr`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.has_wrapper_attr)(name)                                   | 환경에 특정 속성 이름이 존재하는지 확인.                                                   |
| [`load_managers`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.load_managers)()                                             | 환경의 관리자 로드.                                                                    |
| [`render`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.render)([recompute])                                                | 물리 스텝 없이 렌더링 실행.                                                       |
| [`reset`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.reset)([seed, env_ids, options])                                     | 지정된 환경을 재설정하고 관측값 반환.                                               |
| [`reset_to`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.reset_to)(state, env_ids[, seed, is_relative])                    | 제공된 상태로 지정된 환경 재설정.                                                         |
| [`seed`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.seed)([seed])                                                         | 환경의 시드 설정.                                                                         |
| [`serialize`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.serialize)()                                                     | 이 환경을 재인스턴스화하기 위해 필요한 모든 정보를 딕셔너리에 저장.                           |
| [`set_wrapper_attr`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.set_wrapper_attr)(name, value, \*[, force])               | 환경에 특정 속성 이름과 값 설정, 자세한 내용은 Wrapper.set_wrapper_attr 참조.        |
| [`setup_manager_visualizers`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.setup_manager_visualizers)()                     | 관리자 항목용 라이브 시각화 도구 생성.                                                               |
| [`step`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.step)(action)                                                         | 환경의 동력을 한 타임스텝 실행하고 종료된 환경 재설정.                    |
| [`target_eef_pose_to_action`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.target_eef_pose_to_action)(...[, noise, env_id]) | 목표 자세를 행동으로 변환.                                                                            |

**속성:**

| [`device`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.device)                             | 환경이 실행 중인 디바이스.                                                                       |
|---------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [`get_IO_descriptors`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.get_IO_descriptors)     | 환경의 IO 디스크립터를 가져옵니다.                                                                           |
| [`is_vector_env`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.is_vector_env)               | 환경이 벡터화된 환경인지 여부를 반환합니다.                                                                  |
| [`max_episode_length`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.max_episode_length)     | 환경 단계 단위로 측정한 최대 에피소드 길이.                                                                          |
| [`max_episode_length_s`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.max_episode_length_s) | 초 단위로 측정한 최대 에피소드 길이.                                                                                    |
| [`metadata`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.metadata)                         | 환경의 메타데이터.                                                                                         |
| [`np_random`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.np_random)                       | 설정되지 않은 경우 랜덤 시드로 초기화되는 환경의 내부 `_np_random`을 반환합니다.                   |
| [`np_random_seed`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.np_random_seed)             | 설정되지 않은 경우 랜덤 정수로 시드를 먼저 초기화하는 환경의 내부 `_np_random_seed`을 반환합니다. |
| [`num_envs`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.num_envs)                         | 현재 실행 중인 환경 인스턴스의 수.                                                          |
| [`physics_dt`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.physics_dt)                     | 물리 타임스텝(초 단위).                                                                                         |
| [`step_dt`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.step_dt)                           | 환경 스텝 타임스텝(초 단위).                                                                            |
| [`unwrapped`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.unwrapped)                       | 래핑되지 않은 기본 환경을 반환합니다.                                                                             |
| [`cfg`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.cfg)                                   | 환경의 구성.                                                                                    |

#### get_object_poses(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

로봇 베이스 프레임에서 각 객체(강체 객체 및 관절 객체 포함)의 자세를 가져옵니다.

* **매개변수:**
  **env_ids** – 자세를 가져올 환경 인덱스. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  로봇 베이스 프레임에서의 객체 자세 행렬(4x4 torch.Tensor)에 객체 이름을 매핑하는 딕셔너리

#### get_subtask_term_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

작업의 각 서브태스크에 대한 종료 신호 플래그의 딕셔너리를 가져옵니다. 서브태스크가 완료된 경우 플래그는 1이 되고, 그렇지 않은 경우 0이 됩니다. 데이터셋 주석 도구를 실행할 때 자동 서브태스크 종료 신호 주석을 활성화하려는 경우 이 메서드의 구현이 필요합니다. 수동 서브태스크 종료 신호 주석을 사용하려는 경우 이 메서드를 구현하지 않은 상태로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 종료 신호를 가져올 환경 인덱스. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  각 서브태스크에 대한 종료 신호 플래그(False 또는 True)의 딕셔너리.

#### \_\_init_\_(cfg: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg), render_mode: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

환경을 초기화합니다.

* **매개변수:**
  * **cfg** – 환경의 구성입니다.
  * **render_mode** – 환경의 렌더 모드입니다. 기본값은 None이며, 이는 `"human"`과 유사합니다.

#### action_to_target_eef_pose(action: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

액션을 목표 엔드 이펙터 자세로 변환합니다.

#### actions_to_gripper_actions(actions: [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

그리퍼 액션을 추출합니다.

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
  그룹 이름을 키로, IO 디스크립터를 값으로 갖는 딕셔너리.

#### get_navigation_state(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

선택적 메서드입니다. 내비게이션 컨트롤러 로코맨풀레이션 데이터 생성을 사용할 때만 필요합니다.

로봇의 내비게이션 상태를 가져옵니다. 내비게이션 컨트롤러 사용이 활성화된 경우 필수입니다. 내비게이션 상태에는 내비게이션 컨트롤러의 제어를 받는 시점을 나타내는 불리언 플래그 `"is_navigating"`과 내비게이션 목표에 도달했는지를 나타내는 불리언 플래그 `"navigation_goal_reached"`가 포함됩니다.

* **매개변수:**
  **env_ids** – 내비게이션 상태를 가져올 환경 인덱스. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  내비게이션 상태 플래그(False 또는 True)의 딕셔너리.

#### get_robot_eef_pose(eef_name: [str](https://docs.python.org/3/library/stdtypes.html#str), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

현재 로봇 엔드 이펙터 자세를 가져옵니다.

#### get_subtask_start_signals(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

작업의 각 서브태스크에 대한 시작 신호 플래그의 딕셔너리를 가져옵니다. 서브태스크가 시작된 경우 플래그는 1이 되고, 그렇지 않은 경우 0이 됩니다. 데이터셋 주석 도구를 실행할 때 자동 서브태스크 시작 신호 주석을 활성화하려는 경우 이 메서드의 구현이 필요합니다. 수동 서브태스크 시작 신호 주석을 사용하려는 경우 이 메서드를 구현하지 않은 상태로 둘 수 있습니다.

* **매개변수:**
  **env_ids** – 시작 신호를 가져올 환경 인덱스. None인 경우 모든 환경이 고려됩니다.
* **반환값:**
  각 서브태스크에 대한 시작 신호 플래그(False 또는 True)의 딕셔너리.

#### get_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

환경에서 속성 이름을 가져옵니다.

#### has_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

환경에 속성 이름이 존재하는지 확인합니다.

#### is_vector_env *: ClassVar[[bool](https://docs.python.org/3/library/functions.html#bool)]* *= True*

환경이 벡터화된 환경인지 여부를 나타냅니다.

#### load_managers()

환경의 관리자를 로드합니다.

이 함수는 환경의 다양한 관리자(액션, 관찰, 이벤트 등)를 생성하는 역할을 담당합니다. 관리자는 물리 핸들에 접근해야 하므로 시뮬레이터가 리셋된 이후(즉, 처음 실행된 후에)만 생성할 수 있습니다.

#### NOTE
독립형 애플리케이션의 경우(파이썬에서 시뮬레이터를 실행할 때), 이 함수는 호출됩니다.
#### 초기화 시 자동으로 호출됩니다.

그러나 확장 모드에서는 시뮬레이터가 리셋된 후 사용자가 이 함수를 수동으로 호출해야 합니다. 이는 시뮬레이터가 사용자가 `SimulationContext.reset_async()`를 호출할 때만 리셋되고, 생성자에서는 비동기 함수를 호출할 수 없기 때문입니다.

#### *property* max_episode_length *: [int](https://docs.python.org/3/library/functions.html#int)*

환경 단계 단위의 최대 에피소드 길이.

#### *property* max_episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

초 단위의 최대 에피소드 길이.

#### metadata *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), Any]]* *= {'render_modes': [None, 'human', 'rgb_array']}*

환경의 메타데이터.

#### *property* np_random *: [numpy.random.Generator](https://numpy.org/doc/stable/reference/random/generator.html#numpy.random.Generator)*

환경의 내부 `_np_random`을 반환합니다. 설정되지 않은 경우 랜덤 시드로 초기화됩니다.

* **Returns:**
  np.random.Generator의 인스턴스

#### *property* np_random_seed *: [int](https://docs.python.org/3/library/functions.html#int)*

환경의 내부 `_np_random_seed`을 반환합니다. 설정되지 않은 경우 먼저 랜덤 정수로 시드를 초기화합니다.

[`np_random_seed`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.np_random_seed)를 [`reset()`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.reset) 또는 `set_np_random_through_seed()` 대신 직접 설정하는 경우,
시드는 -1이 됩니다.

* **Returns:**
  현재 np_random의 시드이거나, rng의 시드가 알 수 없는 경우 -1
* **Return type:**
  [int](https://docs.python.org/3/library/functions.html#int)

#### *property* num_envs *: [int](https://docs.python.org/3/library/functions.html#int)*

실행 중인 환경 인스턴스의 수.

#### *property* physics_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 타임스텝(초 단위).

이는 시뮬레이션이 발생하는 가장 낮은 시간 디시메이션입니다.

#### render(recompute: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → np.ndarray | [None](https://docs.python.org/3/library/constants.html#None)

물리 단계를 거치지 않고 렌더링을 실행합니다.

관례에 따라, 모드가 다음과 같은 경우:

- **human**: 현재 디스플레이에 렌더링하고 아무것도 반환하지 않습니다. 보통 인간을 위한 출력입니다.
- **rgb_array**: (x, y, 3) 형태의 numpy.ndarray를 반환하며, 이는 x-by-y 픽셀 이미지의 RGB 값을 나타내고, 비디오로 변환하기에 적합합니다.

* **Parameters:**
  **recompute** – 시뮬레이터가 이미 씬을 렌더링했더라도 강제로 렌더링을 수행할지 여부입니다. 기본값은 False입니다.
* **Returns:**
  모드가 “rgb_array”인 경우 렌더링된 이미지를 numpy 배열로 반환하고, 그렇지 않으면 None을 반환합니다.
* **Raises:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 모드가 “rgb_data”로 설정되었으나 시뮬레이션 렌더 모드가 이를 지원하지 않는 경우.
       この場合、シミュレーションのレンダーモードを `RenderMode.PARTIAL_RENDERING` または `RenderMode.FULL_RENDERING` に設定する必要があります。
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 지원되지 않는 렌더링 모드가 지정된 경우.

#### reset(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, options: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

지정된 환경을 리셋하고 관측값을 반환합니다.

이 함수는 지정된 환경을 리셋하기 위해 `_reset_idx()` 함수를 호출합니다.
그러나 초기화 중에 발생한 프로시저얼 지형 생성과 같은 특정 작업은 반복되지 않습니다.

* **Parameters:**
  * **seed** – 무작위화에 사용할 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **env_ids** – 리셋할 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 리셋됩니다.
  * **options** – 

    환경을 리셋하는 방법을 지정하는 추가 정보입니다. 기본값은 None입니다.

    #### NOTE
    이 인수는 Gymnasium 환경 정의와의 호환성을 위해 사용됩니다.
* **Returns:**
  관측값과 추가 정보를 포함한 튜플.

#### reset_to(state: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None), seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None) = None, is_relative: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

제공된 상태로 지정된 환경을 리셋합니다.

이 함수는 환경을 제공된 상태로 리셋합니다. 상태는 씬 엔티티의 상태를 포함하는 딕셔너리입니다.
형식에 대해서는 `InteractiveScene.get_state()`을 참조하십시오.

이 함수는 [`reset()`](#isaaclab_mimic.envs.pick_place_mimic_env.PickPlaceAbsMimicEnv.reset)와 달리, 랜덤화 이벤트를 사용하는 대신 특정 상태로 환경을 리셋한다는 점이 다릅니다.

* **Parameters:**
  * **state** – 지정된 환경을 리셋할 상태입니다. 형식에 대해서는 `InteractiveScene.get_state()`을 참조하십시오.
  * **env_ids** – 리셋할 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 리셋됩니다.
  * **seed** – 무작위화에 사용할 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.
  * **is_relative** – True로 설정된 경우, 상태는 환경 기원을 기준으로 상대적으로 간주됩니다. 기본값은 False입니다.

#### *static* seed(seed: [int](https://docs.python.org/3/library/functions.html#int) = -1) → [int](https://docs.python.org/3/library/functions.html#int)

환경의 시드를 설정합니다.

* **Parameters:**
  **seed** – 랜덤 생성기의 시드입니다. 기본값은 -1입니다.
* **Returns:**
  랜덤 생성기에 사용된 시드입니다.

#### serialize()

이 환경을 다시 인스턴스화하는 데 필요한 모든 정보를 딕셔너리에 저장합니다.
이는 @env_meta와 동일하며, hdf5 데이터셋에 저장된 환경 메타데이터이며, utils/env_utils.py에서 사용됩니다.

#### set_wrapper_attr(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any), \*, force: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [bool](https://docs.python.org/3/library/functions.html#bool)

Wrapper.set_wrapper_attr에서 더 자세한 정보를 참고하여, 환경에 지정된 이름의 속성을 값으로 설정합니다.

#### setup_manager_visualizers()

관리자 항목에 대한 라이브 시각화 도구를 생성합니다.

#### step(action: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Union](https://docs.python.org/3/library/typing.html#typing.Union)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]]], [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [dict](https://docs.python.org/3/library/stdtypes.html#dict)]

환경의 동역학에 따라 하나의 타임스텝을 실행하고 종료된 환경을 리셋합니다.

`ManagerBasedEnv.step` 클래스와 달리, 이 함수는 다음 작업을 수행합니다:

1. 동작을 처리합니다.
2. 물리 스텝을 수행합니다.
3. GUI가 활성화된 경우 렌더링을 수행합니다.
4. 환경 카운터를 업데이트하고 보상 및 종료 조건을 계산합니다.
5. 종료된 환경을 리셋합니다.
6. 관측값을 계산합니다.
7. 관측값, 보상, 리셋(종료 및 잘림) 및 추가 정보를 반환합니다.

* **Parameters:**
  **action** – 환경에 적용할 동작입니다. 형태는 (num_envs, action_dim)입니다.
* **Returns:**
  관측값, 보상, 리셋(종료 및 잘림), 추가 정보를 포함한 튜플.

#### *property* step_dt *: [float](https://docs.python.org/3/library/functions.html#float)*

환경 스테핑 시간 간격(초 단위).

이 값은 환경이 한 단계 진행되는 시간 간격을 나타냅니다.

#### target_eef_pose_to_action(target_eef_pose_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), gripper_action_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), noise: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_id: [int](https://docs.python.org/3/library/functions.html#int) = 0) → [Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

목표 자세를 행동으로 변환합니다.

이 메서드는 목표 엔드이펙터 자세와 그리퍼 동작을 포함하는 사전을 하나의 행동 텐서로 변환하여 환경에서 사용할 수 있도록 합니다.

함수는 다음 단계를 수행합니다:
1. 자세 사전에서 목표 위치와 회전을 추출합니다
2. 엔드이펙터에 대한 그리퍼 동작을 추출합니다
3. 위치와 쿼터니언 회전을 연결하여 자세 행동을 만듭니다
4. 탐색을 위해 선택적으로 자세 행동에 노이즈를 추가합니다
5. 자세 행동과 그리퍼 동작을 결합하여 최종 행동 텐서를 생성합니다

* **매개변수:**
  * **target_eef_pose_dict** – 목표 엔드이펙터 자세를 포함하는 사전이며, 키는 엔드이펙터 이름이고 값은 자세 텐서입니다.
  * **gripper_action_dict** – 그리퍼 동작을 포함하는 사전이며, 키는 엔드이펙터 이름이고 값은 동작 텐서입니다.
  * **noise** – 탐색을 위해 자세 행동에 적용할 선택적 노이즈 크기입니다. 제공된 경우, 랜덤 노이즈가 생성되어 자세 행동에 추가됩니다.
  * **env_id** – 다중 환경 설정을 위한 환경 ID로, 기본값은 0입니다.
* **반환값:**
  자세 및 그리퍼 명령을 결합한 단일 행동 텐서.
* **반환 유형:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### *property* unwrapped *: [Env](https://gymnasium.farama.org/api/env/#gymnasium.Env)[ObsType, ActType]*

기본 래핑되지 않은 환경을 반환합니다.

* **반환값:**
  기본 래핑되지 않은 [`gymnasium.Env`](https://gymnasium.farama.org/api/env/#gymnasium.Env) 인스턴스
* **반환 유형:**
  Env

#### cfg *: [ManagerBasedRLEnvCfg](../lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg)*

환경의 구성.

### Agibot Place Upright Mug Mimic Env Cfg

### *class* isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg

Bases: `RmpFlowAgibotPlaceUprightMugEnvCfg`, [`MimicEnvCfg`](../lab/isaaclab.envs.md#isaaclab.envs.MimicEnvCfg)

Agibot Place Upright Mug 환경용 Isaac Lab Mimic 환경 구성 클래스입니다.

**메서드:**

| [`__init__`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.__init__)([datagen_config, subtask_configs, ...])   |    |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|----|

**클래스:**

| [`ui_window_class_type`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.ui_window_class_type)   | [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭   |
|------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|

**속성:**

| [`viewer`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.viewer)                                 | 뷰어 구성.                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.sim)                                       | 물리 시뮬레이션 구성.                                                           |
| [`seed`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.seed)                                     | 난수 생성기의 시드.                                                   |
| [`decimation`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.decimation)                         | 정책 dt당 sim dt에서의 제어 동작 업데이트 횟수.                                    |
| [`scene`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.scene)                                   | 씬 설정.                                                                             |
| [`recorders`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.recorders)                           | 레코더 설정.                                                                          |
| [`observations`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.observations)                     | 관측 공간 설정.                                                                 |
| [`actions`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.actions)                               | 동작 공간 설정.                                                                      |
| [`events`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.events)                                 | 이벤트 설정.                                                                             |
| [`rerender_on_reset`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.rerender_on_reset)           | 하나 이상의 환경이 재설정된 후 렌더 단계가 다시 수행되는지 여부.     |
| [`num_rerenders_on_reset`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.num_rerenders_on_reset) | 재설정 후 수행할 렌더 단계 수.                                              |
| [`wait_for_textures`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.wait_for_textures)           | 에셋이 완전히 로드될 때까지 기다릴지 여부를 나타냄. True이면 대기, False이면 대기 안 함.                           |
| [`xr`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.xr)                                         | XR 기기를 통해 환경을 보고 상호작용하는 구성.        |
| [`teleop_devices`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.teleop_devices)                 | 텔레오퍼레이션 장치 구성.                                                    |
| [`export_io_descriptors`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.export_io_descriptors)   | 환경의 IO 디스크립터를 내보낼지 여부.                                   |
| [`log_dir`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.log_dir)                               | 실험 아티팩트를 기록할 디렉터리.                                                 |
| [`is_finite_horizon`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.is_finite_horizon)           | 학습 작업이 에이전트에 대해 유한 horizon인지 무한 horizon인지 여부. |
| [`episode_length_s`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.episode_length_s)             | 에피소드 기간(초 단위).                                                        |
| [`rewards`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.rewards)                               | 보상 설정.                                                                            |
| [`terminations`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.terminations)                     | 종료 조건 설정.                                                                       |
| [`curriculum`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.curriculum)                         | 커리큘럼 설정.                                                                        |
| [`commands`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.commands)                             | 명령 설정.                                                                           |

#### \_\_init_\_(datagen_config: ~isaaclab.envs.mimic_env_cfg.DataGenConfig = <factory>, subtask_configs: dict[str, list[isaaclab.envs.mimic_env_cfg.SubTaskConfig]] = <factory>, task_constraint_configs: list[isaaclab.envs.mimic_env_cfg.SubTaskConstraintConfig] = <factory>, mimic_recorder_config: ~isaaclab.managers.recorder_manager.RecorderManagerBaseCfg | None = <factory>, viewer: ~isaaclab.envs.common.ViewerCfg = <factory>, sim: ~isaaclab.sim.simulation_cfg.SimulationCfg = <factory>, ui_window_class_type: type | None = <class 'isaaclab.envs.ui.manager_based_rl_env_window.ManagerBasedRLEnvWindow'>, seed: int | None = <factory>, decimation: int = <factory>, scene: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObjectTableSceneCfg = <factory>, recorders: object = <factory>, observations: ~isaaclab_tasks.manager_based.manipulation.place.config.agibot.place_toy2box_rmp_rel_env_cfg.ObservationsCfg = <factory>, actions: ~isaaclab_tasks.manager_based.manipulation.place.config.agibot.place_toy2box_rmp_rel_env_cfg.ActionsCfg = <factory>, events: object = <factory>, rerender_on_reset: bool = <factory>, num_rerenders_on_reset: int = <factory>, wait_for_textures: bool = <factory>, xr: ~isaaclab.devices.openxr.xr_cfg.XrCfg | None = <factory>, teleop_devices: ~isaaclab.devices.device_base.DevicesCfg = <factory>, export_io_descriptors: bool = <factory>, log_dir: str | None = <factory>, is_finite_horizon: bool = <factory>, episode_length_s: float = <factory>, rewards: object = <factory>, terminations: ~isaaclab_tasks.manager_based.manipulation.place.config.agibot.place_toy2box_rmp_rel_env_cfg.TerminationsCfg = <factory>, curriculum: object | None = <factory>, commands: object | None = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### ui_window_class_type

[`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭입니다.

#### viewer *: [ViewerCfg](../lab/isaaclab.envs.md#isaaclab.envs.ViewerCfg)*

뷰어 구성입니다. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](../lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성입니다. 기본값은 SimulationCfg()입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드입니다. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 시 설정됩니다.これにより環境の作成が決定的になり、異なる実行間でも同様の動作をします。

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

시뮬레이션 dt당 정책 dt당 제어 액션 업데이트 수입니다.

예를 들어, 시뮬레이션 dt가 0.01s이고 정책 dt가 0.1s인 경우, 디시메이션은 10입니다.
즉, 제어 액션은 10개의 시뮬레이션 스텝마다 업데이트됩니다.

#### scene *: ObjectTableSceneCfg*

장면 설정입니다.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](../lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg) 클래스를 참조하십시오.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정입니다. 기본값은 녹화하지 않음입니다.

자세한 내용은 [`isaaclab.managers.RecorderManager`](../lab/isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참조하십시오.

#### observations *: ObservationsCfg*

관측 공간 설정입니다.

자세한 내용은 [`isaaclab.managers.ObservationManager`](../lab/isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참조하십시오.

#### actions *: ActionsCfg*

액션 공간 설정입니다.

자세한 내용은 [`isaaclab.managers.ActionManager`](../lab/isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참조하십시오.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정입니다. 기본값은 장면을 기본 상태로 재설정하는 기본 구성입니다.

자세한 내용은 [`isaaclab.managers.EventManager`](../lab/isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참조하십시오.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

최소 한 개의 환경이 재설정된 후 렌더 단계를 다시 수행할지 여부입니다. 기본값은 False이며, 이는 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* False인 경우, 재설정 후 센서에서 수집된 데이터는 오래되어 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* True인 경우, 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 재설정으로 인한 최신 상태를 반영합니다. 이는 성능 비용이 발생하며, 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### Deprecated
버전 2.3.1부터 deprecated되었습니다. 이 속성은 향후 제거될 예정입니다. 대신
[`num_rerenders_on_reset`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.num_rerenders_on_reset)를 사용하십시오.

이 매개변수를 `True` 또는 `False`로 설정했을 때와 동일한 동작을 얻으려면
[`num_rerenders_on_reset`](#isaaclab_mimic.envs.agibot_place_upright_mug_mimic_env_cfg.RmpFlowAgibotPlaceUprightMugMimicEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하십시오.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후 수행할 렌더 단계 수입니다. 기본값은 0이며, 이는 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 0인 경우, 재설정 후 렌더 단계가 수행되지 않습니다. 재설정 후 센서에서 수집된 데이터는 오래되어 재설정으로 인한 시뮬레이션의 최신 상태를 반영하지 않습니다.
* 0보다 큰 경우, 지정된 수의 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 재설정으로 인한 최신 상태를 반영합니다. 이는 성능 비용이 발생하며, 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 완전히 로드될 때까지 대기하려면 True, 그렇지 않으면 False입니다. 기본값은 True입니다.

#### xr *: XrCfg | [None](https://docs.python.org/3/library/constants.html#None)*

XR 장치를 통해 환경을 보고 상호작용하는 구성입니다.

#### teleop_devices *: DevicesCfg*

텔레오퍼레이션 장치 구성입니다.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 디스크립터를 내보낼지 여부입니다. 기본값은 False입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 기록할 디렉터리입니다. 기본값은 None이며, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

학습 작업이 에이전트에 대해 유한 또는 무한 호라이즌 문제로 처리되는지 여부입니다. 기본값은 False이며, 이는 작업이 무한 호라이즌 문제로 처리됨을 의미합니다.

이 플래그는 유한 및 무한 호라이즌 작업의 세부 사항을 처리합니다:

* **유한 호라이즌**: 에이전트는 시간 초과 시 페널티 또는 부트스트래핑 값이 필요하지 않습니다. 그러나 환경은 시간 제한에 도달했을 때 에피소드를 여전히 종료해야 합니다.
* **무한 호라이즌**: 에이전트는 에피소드 끝의 상태 값에 대해 부트스트래핑을 수행해야 합니다. 이는 시간 제한(또는 잘림) 완료 신호를 에이전트에 보내어 이 부트스트래핑 계산을 트리거함으로써 수행됩니다.

True인 경우, 환경은 유한 호라이즌 문제로 처리되며 시간 초과(또는 잘림) 완료 신호가 에이전트에 전송되지 않습니다. False인 경우, 환경은 무한 호라이즌 문제로 처리되며 시간 초과(또는 잘림) 완료 신호가 에이전트에 전송됩니다.

#### NOTE
기본 `ManagerBasedRLEnv` 클래스는 이 플래그를 직접 사용하지 않습니다. 이 플래그는 환경 래퍼에서 사용되어 해당 학습 에이전트에 어떤 유형의 완료 신호를 보낼지를 결정합니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 지속 시간(초 단위).

디시메이션 비율과 물리 시간 단계를 기반으로 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디시메이션 비율이 10이고 물리 시간 단계가 0.01이며 에피소드 길이가 10초인 경우, 에피소드 길이는 100스텝입니다.

#### rewards *: [object](https://docs.python.org/3/library/functions.html#object)*

보상 설정입니다.

자세한 내용은 [`isaaclab.managers.RewardManager`](../lab/isaaclab.managers.md#isaaclab.managers.RewardManager) 클래스를 참조하십시오.

#### terminations *: TerminationsCfg*

종료 설정입니다.

자세한 내용은 [`isaaclab.managers.TerminationManager`](../lab/isaaclab.managers.md#isaaclab.managers.TerminationManager) 클래스를 참조하십시오.

#### curriculum *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

커리큘럼 설정입니다. 기본값은 None이며, 이 경우 커리큘럼이 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.CurriculumManager`](../lab/isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 참조하십시오.

#### commands *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

명령 설정입니다. 기본값은 None이며, 이 경우 명령이 생성되지 않습니다.

자세한 내용은 [`isaaclab.managers.CommandManager`](../lab/isaaclab.managers.md#isaaclab.managers.CommandManager) 클래스를 참조하십시오.

### Agibot Place Toy2Box Mimic Env Cfg

### *class* isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg

기반: `RmpFlowAgibotPlaceToy2BoxEnvCfg`, [`MimicEnvCfg`](../lab/isaaclab.envs.md#isaaclab.envs.MimicEnvCfg)

Agibot Place Toy2Box 환경용 Isaac Lab Mimic 환경 구성 클래스.

**메서드:**

| [`__init__`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.__init__)([datagen_config, subtask_configs, ...])   |    |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------|----|

**클래스:**

| [`ui_window_class_type`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.ui_window_class_type)   | [`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭   |
|-----------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|

**특성:**

| [`viewer`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.viewer)                                 | 뷰어 구성.                                                                       |
|-------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`sim`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.sim)                                       | 물리 시뮬레이션 구성.                                                           |
| [`seed`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.seed)                                     | 난수 생성기의 시드.                                                   |
| [`decimation`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.decimation)                         | 정책 dt당 sim dt당 제어 동작 업데이트 수.                                    |
| [`scene`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.scene)                                   | 장면 설정.                                                                             |
| [`recorders`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.recorders)                           | 레코더 설정.                                                                          |
| [`observations`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.observations)                     | 관측 공간 설정.                                                                 |
| [`actions`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.actions)                               | 동작 공간 설정.                                                                      |
| [`events`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.events)                                 | 이벤트 설정.                                                                             |
| [`rerender_on_reset`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.rerender_on_reset)           | 하나 이상의 환경이 재설정될 경우 렌더링 단계가 다시 수행되는지 여부.     |
| [`num_rerenders_on_reset`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.num_rerenders_on_reset) | 재설정 후 수행할 렌더링 단계 수.                                              |
| [`wait_for_textures`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.wait_for_textures)           | 애셋이 완전히 로드될 때까지 대기하려면 True, 그렇지 않으면 False.                           |
| [`xr`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.xr)                                         | XR 장치를 통해 환경 보기 및 상호작용 구성.        |
| [`teleop_devices`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.teleop_devices)                 | 텔레오퍼레이션 장치 구성.                                                    |
| [`export_io_descriptors`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.export_io_descriptors)   | 환경의 IO 설명자 내보내기 여부.                                   |
| [`log_dir`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.log_dir)                               | 실험 아티팩트를 위한 로그 디렉터리.                                                 |
| [`is_finite_horizon`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.is_finite_horizon)           | 에이전트에 대해 학습 작업이 유한 또는 무한 호라이즌 문제로 취급되는지 여부. |
| [`episode_length_s`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.episode_length_s)             | 에피소드 지속 시간(초).                                                        |
| [`rewards`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.rewards)                               | 보상 설정.                                                                            |
| [`terminations`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.terminations)                     | 종료 조건 설정.                                                                       |
| [`curriculum`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.curriculum)                         | 교육 과정 설정.                                                                        |
| [`commands`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.commands)                             | 명령 설정.                                                                           |

#### \_\_init_\_(datagen_config: ~isaaclab.envs.mimic_env_cfg.DataGenConfig = <factory>, subtask_configs: dict[str, list[isaaclab.envs.mimic_env_cfg.SubTaskConfig]] = <factory>, task_constraint_configs: list[isaaclab.envs.mimic_env_cfg.SubTaskConstraintConfig] = <factory>, mimic_recorder_config: ~isaaclab.managers.recorder_manager.RecorderManagerBaseCfg | None = <factory>, viewer: ~isaaclab.envs.common.ViewerCfg = <factory>, sim: ~isaaclab.sim.simulation_cfg.SimulationCfg = <factory>, ui_window_class_type: type | None = <class 'isaaclab.envs.ui.manager_based_rl_env_window.ManagerBasedRLEnvWindow'>, seed: int | None = <factory>, decimation: int = <factory>, scene: ~isaaclab_tasks.manager_based.manipulation.stack.stack_env_cfg.ObjectTableSceneCfg = <factory>, recorders: object = <factory>, observations: ~isaaclab_tasks.manager_based.manipulation.place.config.agibot.place_toy2box_rmp_rel_env_cfg.ObservationsCfg = <factory>, actions: ~isaaclab_tasks.manager_based.manipulation.place.config.agibot.place_toy2box_rmp_rel_env_cfg.ActionsCfg = <factory>, events: object = <factory>, rerender_on_reset: bool = <factory>, num_rerenders_on_reset: int = <factory>, wait_for_textures: bool = <factory>, xr: ~isaaclab.devices.openxr.xr_cfg.XrCfg | None = <factory>, teleop_devices: ~isaaclab.devices.device_base.DevicesCfg = <factory>, export_io_descriptors: bool = <factory>, log_dir: str | None = <factory>, is_finite_horizon: bool = <factory>, episode_length_s: float = <factory>, rewards: object = <factory>, terminations: ~isaaclab_tasks.manager_based.manipulation.place.config.agibot.place_toy2box_rmp_rel_env_cfg.TerminationsCfg = <factory>, curriculum: object | None = <factory>, commands: object | None = <factory>) → [None](https://docs.python.org/3/library/constants.html#None)

#### ui_window_class_type

[`ManagerBasedRLEnvWindow`](../lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow)의 별칭

#### viewer *: [ViewerCfg](../lab/isaaclab.envs.md#isaaclab.envs.ViewerCfg)*

뷰어 구성. 기본값은 ViewerCfg()입니다.

#### sim *: [SimulationCfg](../lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)*

물리 시뮬레이션 구성. 기본값은 SimulationCfg()입니다.

#### seed *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

난수 생성기의 시드. 기본값은 None이며, 이 경우 시드가 설정되지 않습니다.

#### NOTE
시드는 환경 초기화 시작 시 설정됩니다. 이를 통해 환경 생성이 결정적이며 다양한 실행 간에 유사하게 동작하도록 보장됩니다.

#### decimation *: [int](https://docs.python.org/3/library/functions.html#int)*

시뮬레이션 dt당 정책 dt에서의 제어 동작 업데이트 수.

예를 들어, 시뮬레이션 dt가 0.01s이고 정책 dt가 0.1s인 경우, 디케이메이션은 10입니다. 즉, 제어 동작이 매 10개의 시뮬레이션 단계마다 업데이트됩니다.

#### scene *: ObjectTableSceneCfg*

장면 설정.

자세한 내용은 [`isaaclab.scene.InteractiveSceneCfg`](../lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCls) 클래스를 참조하십시오.

#### recorders *: [object](https://docs.python.org/3/library/functions.html#object)*

레코더 설정. 기본적으로 아무것도 기록하지 않습니다.

다음 [`isaaclab.managers.RecorderManager`](../lab/isaaclab.managers.md#isaaclab.managers.RecorderManager) 클래스를 참조하여 자세한 내용을 확인하세요.

#### observations *: ObservationsCfg*

관찰 공간 설정.

자세한 내용은 [`isaaclab.managers.ObservationManager`](../lab/isaaclab.managers.md#isaaclab.managers.ObservationManager) 클래스를 참조하세요.

#### actions *: ActionsCfg*

작업 공간 설정.

자세한 내용은 [`isaaclab.managers.ActionManager`](../lab/isaaclab.managers.md#isaaclab.managers.ActionManager) 클래스를 참조하세요.

#### events *: [object](https://docs.python.org/3/library/functions.html#object)*

이벤트 설정. 기본값은 환경을 기본 상태로 재설정하는 기본 구성입니다.

자세한 내용은 [`isaaclab.managers.EventManager`](../lab/isaaclab.managers.md#isaaclab.managers.EventManager) 클래스를 참조하세요.

#### rerender_on_reset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

하나 이상의 환경이 재설정된 후 렌더 단계가 다시 수행되는지 여부입니다. 기본값은 False로, 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 False인 경우, 재설정 후 센서에서 수집된 데이터는 오래된 상태이며, 재설정으로 인해 시뮬레이션에서 발생한 최신 상태를 반영하지 않습니다.
* 이 값이 True인 경우, 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 재설정으로 인한 최신 상태를 반영합니다. 추가 렌더 단계로 인해 성능 비용이 발생합니다. 즉, 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### Deprecated
2.3.1 버전부터 지원 중단: 이 속성은 지원 중단되었으며 향후 버전에서 삭제될 예정입니다. 대신 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.num_rerenders_on_reset)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정한 동작과 동일하게 얻으려면 [`num_rerenders_on_reset`](#isaaclab_mimic.envs.agibot_place_toy2box_mimic_env_cfg.RmpFlowAgibotPlaceToy2BoxMimicEnvCfg.num_rerenders_on_reset)를 각각 1 또는 0으로 설정하세요.

#### num_rerenders_on_reset *: [int](https://docs.python.org/3/library/functions.html#int)*

재설정 후 수행할 렌더 단계 수입니다. 기본값은 0으로, 재설정 후 렌더 단계가 수행되지 않음을 의미합니다.

* 이 값이 0인 경우, 재설정 후 렌더 단계가 수행되지 않습니다. 재설정 후 센서에서 수집된 데이터는 오래된 상태이며, 재설정으로 인해 시뮬레이션에서 발생한 최신 상태를 반영하지 않습니다.
* 이 값이 0보다 큰 경우, 지정된 수의 추가 렌더 단계가 수행되어 센서 데이터를 업데이트하고 재설정으로 인한 최신 상태를 반영합니다. 추가 렌더 단계로 인해 성능 비용이 발생합니다. 즉, 환경이 재설정될 때마다 추가 렌더 단계가 수행됩니다.

#### wait_for_textures *: [bool](https://docs.python.org/3/library/functions.html#bool)*

자산이 완전히 로드될 때까지 대기하려면 True, 그렇지 않으면 False입니다. 기본값은 True입니다.

#### xr *: XrCfg | [None](https://docs.python.org/3/library/constants.html#None)*

XR 장치를 통해 환경을 보기 및 상호작용하는 데 대한 구성입니다.

#### teleop_devices *: DevicesCfg*

텔레오퍼레이션 장치에 대한 구성입니다.

#### export_io_descriptors *: [bool](https://docs.python.org/3/library/functions.html#bool)*

환경의 IO 디스크립터를 내보낼지 여부입니다. 기본값은 False입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

실험 아티팩트를 기록할 디렉터리입니다. 기본값은 None이며, 이 경우 특정 로그 디렉터리가 설정되지 않습니다.

#### is_finite_horizon *: [bool](https://docs.python.org/3/library/functions.html#bool)*

학습 작업이 에이전트에 대해 유한 또는 무한 지평선 문제로 처리되는지 여부입니다. 기본값은 False로, 작업이 무한 지평선 문제로 처리됨을 의미합니다.

이 플래그는 유한 및 무한 지평선 작업의 세부 사항을 처리합니다:

* **유한 지평선**: 에이전트가 시간 초과 시 페널티 또는 부트스트랩 값을 요구하지 않습니다. 그러나 환경은 시간 제한이 도달된 후 에피소드를 여전히 종료해야 합니다.
* **무한 지평선**: 에이전트는 에피소드 끝 상태의 값을 부트스트랩해야 합니다. 이는 시간 제한(또는 잘림) 완료 신호를 에이전트에 보내어 이 부트스트랩 계산을 트리거함으로써 수행됩니다.

True이면 환경은 유한 지평선 문제로 처리되며, 에이전트에 시간 초과(또는 잘림) 완료 신호가 전송되지 않습니다. False이면 환경은 무한 지평선 문제로 처리되며, 에이전트에 시간 초과(또는 잘림) 완료 신호가 전송됩니다.

#### NOTE
기본 `ManagerBasedRLEnv` 클래스는 이 플래그를 직접 사용하지 않습니다. 이 플래그는 환경 래퍼에서 사용되어 해당 학습 에이전트에 어떤 유형의 완료 신호를 보낼지 결정합니다.

#### episode_length_s *: [float](https://docs.python.org/3/library/functions.html#float)*

에피소드 지속 시간(초 단위).

디메이션 레이트와 물리 시간 단계를 기반으로 에피소드 길이는 다음과 같이 계산됩니다:

```python
episode_length_steps = ceil(episode_length_s / (decimation_rate * physics_time_step))
```

예를 들어, 디메이션 레이트가 10, 물리 시간 단계가 0.01, 에피소드 길이가 10초인 경우, 에피소드 길이는 100단계가 됩니다.

#### rewards *: [object](https://docs.python.org/3/library/functions.html#object)*

보상 설정.

자세한 내용은 [`isaaclab.managers.RewardManager`](../lab/isaaclab.managers.md#isaaclab.managers.RewardManager) 클래스를 참조하세요.

#### terminations *: TerminationsCfg*

종료 설정.

자세한 내용은 [`isaaclab.managers.TerminationManager`](../lab/isaaclab.managers.md#isaaclab.managers.TerminationManager) 클래스를 참조하세요.

#### curriculum *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

커리큘럼 설정. 기본값은 None이며, 이 경우 커리큘럼이 적용되지 않습니다.

자세한 내용은 [`isaaclab.managers.CurriculumManager`](../lab/isaaclab.managers.md#isaaclab.managers.CurriculumManager) 클래스를 참조하세요.

#### commands *: [object](https://docs.python.org/3/library/functions.html#object) | [None](https://docs.python.org/3/library/constants.html#None)*

명령 설정. 기본값은 None이며, 이 경우 명령이 생성되지 않습니다.

자세한 내용은 [`isaaclab.managers.CommandManager`](../lab/isaaclab.managers.md#isaaclab.managers.CommandManager) 클래스를 참조하세요.
