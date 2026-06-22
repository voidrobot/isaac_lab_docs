# isaaclab.envs.mdp

관리자 구현에 사용되는 서브모듈입니다.

이 함수들은 MDP의 다양한 측면을 담당하는 다양한 관리자에 제공될 수 있습니다. 여기에는 관찰, 보상, 종료, 행동, 이벤트 및 커리큘럼 관리자가 포함됩니다.

이 용어들은 환경을 정의하는 데 사용되기 때문에 `envs` 모듈 아래에 정의되어 있습니다. 그러나 환경의 직접적인 부분은 아니지만 관리자를 통해 환경을 정의하는 데 사용됩니다.

## 관찰

관찰 항목을 생성하는 데 사용할 수 있는 일반적인 함수입니다.

이 함수들은 [`isaaclab.managers.ObservationTermCfg`](isaaclab.managers.md#isaaclab.managers.ObservationTermCfg) 객체에 전달되어 해당 함수로 도입된 관찰을 활성화할 수 있습니다.

**함수:**

| [`base_pos_z`](#isaaclab.envs.mdp.observations.base_pos_z)(env[, asset_cfg])                                 | 시뮬레이션 월드 프레임에서의 루트 높이.                                          |
|--------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
| [`base_lin_vel`](#isaaclab.envs.mdp.observations.base_lin_vel)(env[, asset_cfg])                             | 자산의 루트 프레임에서의 루트 선형 속도.                                     |
| [`base_ang_vel`](#isaaclab.envs.mdp.observations.base_ang_vel)(env[, asset_cfg])                             | 자산의 루트 프레임에서의 루트 각속도.                                    |
| [`projected_gravity`](#isaaclab.envs.mdp.observations.projected_gravity)(env[, asset_cfg])                   | 자산의 루트 프레임에 대한 중력 투영.                                       |
| [`root_pos_w`](#isaaclab.envs.mdp.observations.root_pos_w)(env[, asset_cfg])                                 | 환경 프레임에서의 자산 루트 위치.                                       |
| [`root_quat_w`](#isaaclab.envs.mdp.observations.root_quat_w)(env[, make_quat_unique, asset_cfg])             | 환경 프레임에서의 자산 루트 방향 (w, x, y, z).                       |
| [`root_lin_vel_w`](#isaaclab.envs.mdp.observations.root_lin_vel_w)(env[, asset_cfg])                         | 환경 프레임에서의 자산 루트 선형 속도.                                |
| [`root_ang_vel_w`](#isaaclab.envs.mdp.observations.root_ang_vel_w)(env[, asset_cfg])                         | 환경 프레임에서의 자산 루트 각속도.                               |
| [`body_pose_w`](#isaaclab.envs.mdp.observations.body_pose_w)(env[, asset_cfg])                               | env.scene.origin에 대한 자산의 플래트된 본 포즈.                   |
| [`body_projected_gravity_b`](#isaaclab.envs.mdp.observations.body_projected_gravity_b)(env[, asset_cfg])     | 아티큘레이션의 바디에 투영된 중력 방향.                 |
| [`joint_pos`](#isaaclab.envs.mdp.observations.joint_pos)(env[, asset_cfg])                                   | 자산의 조인트 위치.                                                   |
| [`joint_pos_rel`](#isaaclab.envs.mdp.observations.joint_pos_rel)(env[, asset_cfg])                           | 자산의 조인트 위치 (w.r.t.).                                              |
| [`joint_pos_limit_normalized`](#isaaclab.envs.mdp.observations.joint_pos_limit_normalized)(env[, asset_cfg]) | 자산의 조인트 한계로 정규화된 조인트 위치.          |
| [`joint_vel`](#isaaclab.envs.mdp.observations.joint_vel)(env[, asset_cfg])                                   | 자산의 조인트 속도.                                                  |
| [`joint_vel_rel`](#isaaclab.envs.mdp.observations.joint_vel_rel)(env[, asset_cfg])                           | 자산의 조인트 속도 (w.r.t.).                                            |
| [`joint_effort`](#isaaclab.envs.mdp.observations.joint_effort)(env[, asset_cfg])                             | 로봇에 적용된 조인트 토크.                                              |
| [`height_scan`](#isaaclab.envs.mdp.observations.height_scan)(env, sensor_cfg[, offset])                      | 주어진 센서에서의 높이 스캔 (w.r.t.).                            |
| [`body_incoming_wrench`](#isaaclab.envs.mdp.observations.body_incoming_wrench)(env, asset_cfg)               | 시뮬레이션 월드 프레임에서 아티큘레이션 바디에 작용하는 공간 렌치. |
| [`imu_orientation`](#isaaclab.envs.mdp.observations.imu_orientation)(env[, asset_cfg])                       | 시뮬레이션 월드 프레임에서의 IMU 센서 방향.                               |
| [`imu_projected_gravity`](#isaaclab.envs.mdp.observations.imu_projected_gravity)(env[, asset_cfg])           | env.scene.origin에 대한 IMU 센서 방향.                                  |
| [`imu_ang_vel`](#isaaclab.envs.mdp.observations.imu_ang_vel)(env[, asset_cfg])                               | IMU 센서 각속도 (w.r.t.).                                                  |
| [`imu_lin_acc`](#isaaclab.envs.mdp.observations.imu_lin_acc)(env[, asset_cfg])                               | IMU 센서 선형 가속도 (w.r.t.).                                               |
| [`image`](#isaaclab.envs.mdp.observations.image)(env[, sensor_cfg, data_type, ...])                          | 카메라 센서의 특정 데이터 타입에서의 이미지.                           |
| [`last_action`](#isaaclab.envs.mdp.observations.last_action)(env[, action_name])                             | 환경에 입력된 마지막 행동.                                           |
| [`generated_commands`](#isaaclab.envs.mdp.observations.generated_commands)(env[, command_name])              | 주어진 이름을 가진 커맨드 매니저의 커맨드 항에서 생성된 커맨드. |
| [`current_time_s`](#isaaclab.envs.mdp.observations.current_time_s)(env)                                      | 에피소드 내 현재 시간 (초 단위).                                       |
| [`remaining_time_s`](#isaaclab.envs.mdp.observations.remaining_time_s)(env)                                  | 에피소드 내 남은 최대 시간 (초 단위).                             |

**클래스:**

| [`image_features`](#isaaclab.envs.mdp.observations.image_features)   | 사전 훈련된 동결 인코더에서 추출된 이미지 특징.   |
|----------------------------------------------------------------------|---------------------------------------------------------------|

### isaaclab.envs.mdp.observations.base_pos_z(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

시뮬레이션 월드 프레임에서의 루트 높이.

### isaaclab.envs.mdp.observations.base_lin_vel(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 루트 프레임에서의 루트 선형 속도.

### isaaclab.envs.mdp.observations.base_ang_vel(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 루트 프레임에서의 루트 각속도.

### isaaclab.envs.mdp.observations.projected_gravity(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 루트 프레임에 대한 중력 투영.

### isaaclab.envs.mdp.observations.root_pos_w(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

환경 프레임에서의 자산 루트 위치.

### isaaclab.envs.mdp.observations.root_quat_w(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), make_quat_unique: [bool](https://docs.python.org/3/library/functions.html#bool) = False, asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

환경 프레임에서 자산 루트 방향(w, x, y, z).

만약 `make_quat_unique`가 True이면, 반환된 쿼터니언은 실수 성분이 음이 아닌 것을 보장함으로써 고유하게 만듭니다. 이는 `q`와 `-q`가 동일한 방향을 나타내기 때문입니다.

### isaaclab.envs.mdp.observations.root_lin_vel_w(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

환경 프레임에서 자산 루트 선형 속도.

### isaaclab.envs.mdp.observations.root_ang_vel_w(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

환경 프레임에서 자산 루트 각속도.

### isaaclab.envs.mdp.observations.body_pose_w(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 본체 자세(위치와 방향)가 env.scene.origin을 기준으로 평탄화된 형태.

참고: `asset_cfg.body_ids`에 구성된 본체만 자세가 반환됩니다.

* **매개변수:**
  * **env** – 환경.
  * **asset_cfg** – 이 관측과 연결된 SceneEntity.
* **반환값:**
  관절 관절의 본체 자세 [num_env, 7 \* num_bodies]. 자세 순서는 [x, y, z, qw, qx, qy, qz]입니다.
  출력은 본체당 수평 방향으로 쌓입니다.

### isaaclab.envs.mdp.observations.body_projected_gravity_b(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

아티큘레이션의 본체에 중력이 투영된 방향.

참고: `asset_cfg.body_ids`에 구성된 본체만 자세가 반환됩니다.

* **매개변수:**
  * **env** – 환경.
  * **asset_cfg** – 이 관측과 연결된 아티큘레이션.
* **반환값:**
  body_name의 프레임에 투영된 중력의 단위 벡터 방향. 중력 투영 벡터 순서는 [x, y, z]입니다.
  출력은 본체당 수평 방향으로 쌓입니다.

### isaaclab.envs.mdp.observations.joint_pos(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 관절 위치.

참고: `asset_cfg.joint_ids`에 구성된 관절만 위치가 반환됩니다.

### isaaclab.envs.mdp.observations.joint_pos_rel(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

기본 관절 위치에 대한 자산의 관절 위치.

참고: `asset_cfg.joint_ids`에 구성된 관절만 위치가 반환됩니다.

### isaaclab.envs.mdp.observations.joint_pos_limit_normalized(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 관절 한계로 정규화된 자산의 관절 위치.

참고: `asset_cfg.joint_ids`에 구성된 관절만 정규화된 위치가 반환됩니다.

### isaaclab.envs.mdp.observations.joint_vel(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

자산의 관절 속도.

참고: `asset_cfg.joint_ids`에 구성된 관절만 속도가 반환됩니다.

### isaaclab.envs.mdp.observations.joint_vel_rel(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

기본 관절 속도에 대한 자산의 관절 속도.

참고: `asset_cfg.joint_ids`에 구성된 관절만 속도가 반환됩니다.

### isaaclab.envs.mdp.observations.joint_effort(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

로봇의 관절에 적용된 힘(또는 토크).

참고: `asset_cfg.joint_ids`에 구성된 관절만 힘이 반환됩니다.

* **매개변수:**
  * **env** – 환경.
  * **asset_cfg** – 이 관측과 연결된 SceneEntity.
* **반환값:**
  asset_cfg의 joint_names에 대한 관절 힘(N 또는 N-m), 모양은 [num_env, num_joints]입니다.

### isaaclab.envs.mdp.observations.height_scan(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), sensor_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg), offset: [float](https://docs.python.org/3/library/functions.html#float) = 0.5) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

센서의 프레임을 기준으로 한 주어진 센서의 높이 스캔.

제공된 오프셋(기본값은 0.5)은 반환된 값에서 뺍니다.

### isaaclab.envs.mdp.observations.body_incoming_wrench(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

시뮬레이션 세계 프레임에서 아티큘레이션의 본체에 작용하는 들어오 공간 렌치.

이것은 들어오는 관절 힘에 의해 본체 링크에 작용하는 6차원 렌치(힘과 토크)입니다.

### isaaclab.envs.mdp.observations.imu_orientation(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='imu', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

시뮬레이션 세계 프레임에서 IMU 센서의 방향.

* **파라미터:**
  * **env** – 환경.
  * **asset_cfg** – IMU 센서와 연결된 SceneEntity. 기본값은 SceneEntityCfg("imu").
* **반환값:**
  세계 프레임에서의 방향, (w, x, y, z) 사원수 형태. 크기는 (num_envs, 4).

### isaaclab.envs.mdp.observations.imu_projected_gravity(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='imu', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

IMU 센서의 중력 벡터가 env.scene.origin 기준 시뮬레이션 프레임에 투영된 값.

* **파라미터:**
  * **env** – 환경.
  * **asset_cfg** – IMU 센서와 연결된 SceneEntity.
* **반환값:**
  IMU 프레임에 투영된 중력 벡터. 토치 텐서의 크기는 (num_env, 3).

### isaaclab.envs.mdp.observations.imu_ang_vel(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='imu', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

센서 프레임에서 환경 기준의 IMU 센서 각속도.

* **파라미터:**
  * **env** – 환경.
  * **asset_cfg** – IMU 센서와 연결된 SceneEntity. 기본값은 SceneEntityCfg("imu").
* **반환값:**
  센서 프레임에서 측정된 각속도 (rad/s). 크기는 (num_envs, 3).

### isaaclab.envs.mdp.observations.imu_lin_acc(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='imu', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

센서 프레임에서 환경 기준의 IMU 센서 선형 가속도.

* **파라미터:**
  * **env** – 환경.
  * **asset_cfg** – IMU 센서와 연결된 SceneEntity. 기본값은 SceneEntityCfg("imu").
* **반환값:**
  센서 프레임에서 측정된 선형 가속도 (m/s²). 크기는 (num_envs, 3).

### isaaclab.envs.mdp.observations.image(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), sensor_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='tiled_camera', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False), data_type: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'rgb', convert_perspective_to_orthogonal: [bool](https://docs.python.org/3/library/functions.html#bool) = False, normalize: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

카메라 센서에서 특정 데이터 유형의 이미지.

`normalize` 플래그가 True일 경우, 데이터 유형에 따라 이미지 후처리가 수행됩니다:

- "rgb": 이미지를 (0, 1) 범위로 스케일링한 후 현재 이미지 배치의 평균을 뺍니다.
- "depth", "distance_to_camera", 또는 "distance_to_plane": 무한대 값을 0으로 대체합니다.

* **파라미터:**
  * **env** – 카메라가 배치된 환경.
  * **sensor_cfg** – 읽을 원하는 센서. 기본값은 SceneEntityCfg("tiled_camera").
  * **data_type** – 카메라에서 가져올 데이터 유형. 기본값은 "rgb".
  * **convert_perspective_to_orthogonal** – 원근 깊이 이미지를 직교화할지 여부.
    데이터 유형이 "distance_to_camera"일 때만 사용됩니다. 기본값은 False.
  * **normalize** – 이미지를 정규화할지 여부. 이는 선택된 데이터 유형에 따라 달라집니다.
    기본값은 True.
* **반환값:**
  마지막 타임스텝에서 생성된 이미지.

### *class* isaaclab.envs.mdp.observations.image_features

사전 훈련된 고정된 인코더에서 추출된 이미지 특징.

이 항목은 PyTorch 모델 동물원에서 모델을 불러와 이미지에서 특징을 추출합니다.

[`image()`](#isaaclab.envs.mdp.observations.image) 함수를 호출하여 이미지를 얻은 후, 모델 동물원을 사용하여 처리합니다.

사용자는 모델 동물원 구성을 제공하여 특징 추출에 다른 모델을 사용할 수 있습니다.
모델 동물원 구성은 다양한 모델 이름을 해당 모델, 전처리 및 추론 함수를 정의하는 딕셔너리에 매핑하는 딕셔너리여야 합니다.
이 딕셔너리는 다음 항목을 포함해야 합니다:

- "model": 인수 없이 호출 시 모델을 반환하는 호출 가능한 객체.
- "reset": 모델을 재설정하는 호출 가능한 객체. 모델에 상태가 있어 재설정이 필요한 경우 유용합니다.
- "inference": 모델과 이미지를 입력으로 받아 추출된 특징을 반환하는 호출 가능한 객체.

모델 동물원 구성이 제공되지 않은 경우, 기본 모델 동물원 구성이 사용됩니다.
기본 모델 동물원 구성에는 Theia [[SSM+24](../../refs/bibliography.md#id18)] 및 ResNet [[HZRS16](../../refs/bibliography.md#id19)] 모델이 포함됩니다.
이 모델들은 각각 [Hugging-Face transformers](https://huggingface.co/docs/transformers/index)와 [PyTorch torchvision](https://pytorch.org/vision/stable/models.html)에서 로드됩니다.

* **파라미터:**
  * **sensor_cfg** – 폴링할 센서 구성. 기본값은 SceneEntityCfg("tiled_camera").
  * **data_type** – 센서 데이터 유형. 기본값은 "rgb".
  * **convert_perspective_to_orthogonal** – 원근 깊이 이미지를 직교화할지 여부.
    데이터 유형이 "distance_to_camera"일 때만 사용됩니다. 기본값은 False.
  * **model_zoo_cfg** – 다양한 모델 이름을 해당 구성에 매핑하는 사용자 정의 딕셔너리.
    기본값은 None이며, None인 경우 기본 모델 동물원 구성이 사용됩니다.
  * **model_name** – 추론에 사용할 모델의 이름. 기본값은 "resnet18".
  * **model_device** – 모델을 저장하고 추론에 사용할 장치.
    환경 시뮬레이션 장치에서 계산을 오프로드하는 데 유용합니다. 기본값은 환경 장치.
  * **inference_kwargs** – 추론 함수에 전달할 추가 키워드 인수.
    기본값은 None이며, 이는 추가 인수가 전달되지 않음을 의미합니다.
* **반환값:**
  추출된 특징 텐서. 크기는 (num_envs, feature_dim).
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 모델 동물원 구성에 모델 이름이 없을 때 발생.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 기본 모델 동물원 구성에 모델 이름이 없을 때 발생.

**메서드:**

| [`__init__`](#isaaclab.envs.mdp.observations.image_features.__init__)(cfg, env)   | 관리자 항목을 초기화합니다.   |
|-----------------------------------------------------------------------------------|--------------------------------|
| [`reset`](#isaaclab.envs.mdp.observations.image_features.reset)([env_ids])        | 관리자 항목을 재설정합니다.       |

#### \_\_init_\_(cfg: [ObservationTermCfg](isaaclab.managers.md#isaaclab.managers.ObservationTermCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

관리자 항목을 초기화합니다.

* **파라미터:**
  * **cfg** – 구성 객체.
  * **env** – 환경 인스턴스.

#### reset(env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

관리자 항목을 재설정합니다.

* **파라미터:**
  **env_ids** – 환경 ID. 기본값은 None이며, 이는 모든 환경을 대상으로 함을 의미합니다.

### isaaclab.envs.mdp.observations.last_action(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), action_name: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

환경에 마지막으로 입력된 동작.

동작이 필요한 동작 용어의 이름. None인 경우 전체 동작 텐서가 반환됩니다.

### isaaclab.envs.mdp.observations.generated_commands(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), command_name: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

커맨드 매니저의 지정된 이름의 커맨드 항목에서 생성된 커맨드입니다.

### isaaclab.envs.mdp.observations.current_time_s(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

에피소드의 현재 시간(초 단위).

### isaaclab.envs.mdp.observations.remaining_time_s(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

에피소드에서 남은 최대 시간(초 단위).

## 액션

환경에서 사용할 수 있는 다양한 액션 항목입니다.

**클래스:**

| [`JointActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg)                                                 | 기본 조인트 액션 항목에 대한 구성입니다.                                      |
|---------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| [`JointPositionActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg)                                 | 조인트 위치 액션 항목에 대한 구성입니다.                                  |
| [`RelativeJointPositionActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg)                 | 상대 조인트 위치 액션 항목에 대한 구성입니다.                         |
| [`JointVelocityActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg)                                 | 조인트 속도 액션 항목에 대한 구성입니다.                                  |
| [`JointEffortActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointEffortActionCfg)                                     | 조인트 힘 액션 항목에 대한 구성입니다.                                    |
| [`JointPositionToLimitsActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg)                 | 제한된 조인트 위치 액션 항목에 대한 구성입니다.                          |
| [`EMAJointPositionToLimitsActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg)           | 지수 이동 평균(EMA) 조인트 위치 액션 항목에 대한 구성입니다. |
| [`BinaryJointActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg)                                     | 기본 바이너리 조인트 액션 항목에 대한 구성입니다.                               |
| [`BinaryJointPositionActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointPositionActionCfg)                     | 바이너리 조인트 위치 액션 항목에 대한 구성입니다.                           |
| [`BinaryJointVelocityActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointVelocityActionCfg)                     | 바이너리 조인트 속도 액션 항목에 대한 구성입니다.                           |
| [`AbsBinaryJointPositionActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg)               | 절대 바이너리 조인트 위치 액션 항목에 대한 구성입니다.                  |
| [`NonHolonomicActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg)                                   | 기저에 더미 조인트가 있는 비홀노믹 액션 항목에 대한 구성입니다.     |
| [`DifferentialInverseKinematicsActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg) | 역 미분 운동학 액션 항목에 대한 구성입니다.                     |
| [`OperationalSpaceControllerActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg)       | 운영 공간 컨트롤러 액션 항목에 대한 구성입니다.                        |
| [`SurfaceGripperBinaryActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.SurfaceGripperBinaryActionCfg)                   | 바이너리 표면 그리퍼 액션 항목에 대한 구성입니다.                          |

### *class* isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg

Bases: [`ActionTermCfg`](isaaclab.managers.md#isaaclab.managers.ActionTermCfg)

기본 조인트 액션 항목에 대한 구성입니다.

자세한 내용은 `JointAction`을 참조하세요.

**속성:**

| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg.joint_names)       | 액션이 매핑될 조인트 이름 또는 정규 표현식 목록입니다.   |
|------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [`scale`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg.scale)                   | 액션의 스케일 팩터(플로트 또는 정규 표현식 딕셔너리).             |
| [`offset`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg.offset)                 | 액션의 오프셋 팩터(플로트 또는 정규 표현식 딕셔너리).            |
| [`preserve_order`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg.preserve_order) | 액션 출력에서 조인트 이름의 순서를 보존할지 여부입니다.        |
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg.asset_name)         | 씬 엔티티의 이름입니다.                                                 |
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg.debug_vis)           | 디버그 정보를 시각화할지 여부입니다.                                       |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg.clip)                     | 액션의 클립 범위(정규 표현식 딕셔너리).                        |

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

액션이 매핑될 조인트 이름 또는 정규 표현식 목록입니다.

#### scale *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션의 스케일 팩터(플로트 또는 정규 표현식 딕셔너리). 기본값은 1.0입니다.

#### offset *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션의 오프셋 팩터(플로트 또는 정규 표현식 딕셔너리). 기본값은 0.0입니다.

#### preserve_order *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액션 출력에서 조인트 이름의 순서를 보존할지 여부입니다. 기본값은 False입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이 이름은 씬 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

액션의 클립 범위(정규 표현식 딕셔너리). 기본값은 None입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg

Bases: [`JointActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg)

조인트 위치 액션 항목에 대한 구성입니다.

자세한 내용은 `JointPositionAction`을 참조하세요.

**속성:**

| [`use_default_offset`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg.use_default_offset)   | 아티큘레이션 자산에 구성된 기본 조인트 위치를 오프셋으로 사용할지 여부입니다.   |
|------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg.asset_name)                   | 씬 엔티티의 이름입니다.                                                            |
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg.debug_vis)                     | 디버그 정보를 시각화할지 여부입니다.                                                  |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg.clip)                               | 액션의 클립 범위(정규 표현식 딕셔너리).                                   |
| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg.joint_names)                 | 액션이 매핑될 조인트 이름 또는 정규 표현식 목록입니다.              |
| [`scale`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg.scale)                             | 액션의 스케일 팩터(플로트 또는 정규 표현식 딕셔너리).                        |
| [`offset`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg.offset)                           | 액션의 오프셋 팩터(플로트 또는 정규 표현식 딕셔너리).                       |
| [`preserve_order`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg.preserve_order)           | 액션 출력에서 조인트 이름의 순서를 보존할지 여부입니다.                   |

#### use_default_offset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

관절에 대한 기본 오프셋으로 어셈블리 자산에서 구성된 기본 관절 위치를 사용할지 여부입니다.
기본값은 True입니다.

이 플래그가 True이면, [`offset`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionActionCfg.offset)의 값을 어셈블리 자산의 기본 관절 위치로 덮어씁니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

시나리오 엔티티의 이름입니다.

이름은 시나리오 구성 파일에서 정의됩니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

액션에 대한 클리핑 범위(정규 표현식의 딕셔너리). 기본값은 None입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

액션이 매핑될 관절 이름 또는 정규 표현식 목록입니다.

#### scale *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션에 대한 스케일 팩터(부동 소수점 또는 정규 표현식의 딕셔너리). 기본값은 1.0입니다.

#### offset *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션에 대한 오프셋 팩터(부동 소수점 또는 정규 표현식의 딕셔너리). 기본값은 0.0입니다.

#### preserve_order *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액션 출력에서 관절 이름의 순서를 유지할지 여부입니다. 기본값은 False입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg

Bases: [`JointActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg)

상대 관절 위치 액션 항에 대한 구성입니다.

자세한 내용은 `RelativeJointPositionAction`을 참조하세요.

**Attributes:**

| [`use_zero_offset`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg.use_zero_offset)   | 어셈블리 자산에 정의된 오프셋을 무시할지 여부입니다.                 |
|--------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg.asset_name)             | 시나리오 엔티티의 이름입니다.                                               |
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg.debug_vis)               | 디버그 정보를 시각화할지 여부입니다.                                     |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg.clip)                         | 액션에 대한 클리핑 범위(정규 표현식의 딕셔너리).                      |
| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg.joint_names)           | 액션이 매핑될 관절 이름 또는 정규 표현식 목록입니다. |
| [`scale`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg.scale)                       | 액션에 대한 스케일 팩터(부동 소수점 또는 정규 표현식의 딕셔너리).           |
| [`offset`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg.offset)                     | 액션에 대한 오프셋 팩터(부동 소수점 또는 정규 표현식의 딕셔너리).          |
| [`preserve_order`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg.preserve_order)     | 액션 출력에서 관절 이름의 순서를 유지할지 여부입니다.      |

#### use_zero_offset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

어셈블리 자산에 정의된 오프셋을 무시할지 여부입니다. 기본값은 True입니다.

True이면, 이 플래그는 [`offset`](#isaaclab.envs.mdp.actions.actions_cfg.RelativeJointPositionActionCfg.offset)의 값을 0으로 덮어씁니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

시나리오 엔티티의 이름입니다.

이름은 시나리오 구성 파일에서 정의됩니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

액션에 대한 클리핑 범위(정규 표현식의 딕셔너리). 기본값은 None입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

액션이 매핑될 관절 이름 또는 정규 표현식 목록입니다.

#### scale *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션에 대한 스케일 팩터(부동 소수점 또는 정규 표현식의 딕셔너리). 기본값은 1.0입니다.

#### offset *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션에 대한 오프셋 팩터(부동 소수점 또는 정규 표현식의 딕셔너리). 기본값은 0.0입니다.

#### preserve_order *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액션 출력에서 관절 이름의 순서를 유지할지 여부입니다. 기본값은 False입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg

Bases: [`JointActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg)

관절 속도 액션 항에 대한 구성입니다.

자세한 내용은 `JointVelocityAction`을 참조하세요.

**Attributes:**

| [`use_default_offset`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg.use_default_offset)   | 어셈블리 자산에서 구성된 기본 관절 속도를 오프셋으로 사용할지 여부입니다.   |
|------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg.asset_name)                   | 시나리오 엔티티의 이름입니다.                                                             |
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg.debug_vis)                     | 디버그 정보를 시각화할지 여부입니다.                                                   |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg.clip)                               | 액션에 대한 클리핑 범위(정규 표현식의 딕셔너리).                                    |
| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg.joint_names)                 | 액션이 매핑될 관절 이름 또는 정규 표현식 목록입니다.               |
| [`scale`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg.scale)                             | 액션에 대한 스케일 팩터(부동 소수점 또는 정규 표현식의 딕셔너리).                         |
| [`offset`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg.offset)                           | 액션에 대한 오프셋 팩터(부동 소수점 또는 정규 표현식의 딕셔너리).                        |
| [`preserve_order`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg.preserve_order)           | 액션 출력에서 관절 이름의 순서를 유지할지 여부입니다.                    |

#### use_default_offset *: [bool](https://docs.python.org/3/library/functions.html#bool)*

어셈블리 자산에서 구성된 기본 관절 속도를 오프셋으로 사용할지 여부입니다.
기본값은 True입니다.

이 플래그가 True이면, [`offset`](#isaaclab.envs.mdp.actions.actions_cfg.JointVelocityActionCfg.offset)의 설정을 재정의합니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

시나리오 엔티티의 이름입니다.

이름은 시나리오 구성 파일에서 정의됩니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

액션에 대한 클리핑 범위(정규 표현식의 딕셔너리). 기본값은 None입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

액션이 매핑될 관절 이름 또는 정규 표현식 목록입니다.

#### scale *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션의 스케일 팩터(float 또는 정규 표현식 딕셔너리). 기본값은 1.0입니다.

#### offset *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션의 오프셋 팩터(float 또는 정규 표현식 딕셔너리). 기본값은 0.0입니다.

#### preserve_order *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액션 출력에서 조인트 이름의 순서를 보존할지 여부입니다. 기본값은 False입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.JointEffortActionCfg

Bases: [`JointActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointActionCfg)

조인트 노력 액션 항목에 대한 구성입니다.

자세한 내용은 `JointEffortAction`를 참조하세요.

**속성:**

| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.JointEffortActionCfg.asset_name)         | 장면 엔티티의 이름입니다.                                               |
|------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.JointEffortActionCfg.debug_vis)           | 디버그 정보를 시각화할지 여부입니다.                                     |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.JointEffortActionCfg.clip)                     | 액션의 클립 범위(정규 표현식 딕셔너리).                      |
| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.JointEffortActionCfg.joint_names)       | 해당 액션이 매핑될 조인트 이름 또는 정규 표현식 목록입니다. |
| [`scale`](#isaaclab.envs.mdp.actions.actions_cfg.JointEffortActionCfg.scale)                   | 액션의 스케일 팩터(float 또는 정규 표현식 딕셔너리).           |
| [`offset`](#isaaclab.envs.mdp.actions.actions_cfg.JointEffortActionCfg.offset)                 | 액션의 오프셋 팩터(float 또는 정규 표현식 딕셔너리).          |
| [`preserve_order`](#isaaclab.envs.mdp.actions.actions_cfg.JointEffortActionCfg.preserve_order) | 액션 출력에서 조인트 이름의 순서를 보존할지 여부입니다.      |

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

장면 엔티티의 이름입니다.

이는 장면 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

액션의 클립 범위(정규 표현식 딕셔너리). 기본값은 None입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

해당 액션이 매핑될 조인트 이름 또는 정규 표현식 목록입니다.

#### scale *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션의 스케일 팩터(float 또는 정규 표현식 딕셔너리). 기본값은 1.0입니다.

#### offset *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션의 오프셋 팩터(float 또는 정규 표현식 딕셔너리). 기본값은 0.0입니다.

#### preserve_order *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액션 출력에서 조인트 이름의 순서를 보존할지 여부입니다. 기본값은 False입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg

Bases: [`ActionTermCfg`](isaaclab.managers.md#isaaclab.managers.ActionTermCfg)

제한된 조인트 위치 액션 항목에 대한 구성입니다.

자세한 내용은 `JointPositionToLimitsAction`를 참조하세요.

**속성:**

| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg.joint_names)             | 해당 액션이 매핑될 조인트 이름 또는 정규 표현식 목록입니다.   |
|----------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [`scale`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg.scale)                         | 액션의 스케일 팩터(float 또는 정규 표현식 딕셔너리).             |
| [`rescale_to_limits`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg.rescale_to_limits) | 조인트 제한으로 액션을 재조정할지 여부입니다.                            |
| [`preserve_order`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg.preserve_order)       | 액션 출력에서 조인트 이름의 순서를 보존할지 여부입니다.        |
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg.asset_name)               | 장면 엔티티의 이름입니다.                                                 |
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg.debug_vis)                 | 디버그 정보를 시각화할지 여부입니다.                                       |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg.clip)                           | 액션의 클립 범위(정규 표현식 딕셔너리).                        |

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

해당 액션이 매핑될 조인트 이름 또는 정규 표현식 목록입니다.

#### scale *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

액션의 스케일 팩터(float 또는 정규 표현식 딕셔너리). 기본값은 1.0입니다.

#### rescale_to_limits *: [bool](https://docs.python.org/3/library/functions.html#bool)*

조인트 제한으로 액션을 재조정할지 여부입니다. 기본값은 True입니다.

True인 경우, 입력 액션이 조인트 제한으로 재조정되어, [-1, 1] 범위의 액션 값이 조인트의 하한 및 상한 limit에 각각 대응됩니다.

#### NOTE
이 작업은 스케일 팩터를 적용한 후에 수행됩니다.

#### preserve_order *: [bool](https://docs.python.org/3/library/functions.html#bool)*

액션 출력에서 조인트 이름의 순서를 보존할지 여부입니다. 기본값은 False입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

장면 엔티티의 이름입니다.

이는 장면 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

액션의 클립 범위(정규 표현식 딕셔너리). 기본값은 None입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg

Bases: [`JointPositionToLimitsActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.JointPositionToLimitsActionCfg)

지수 이동 평균(EMA) 조인트 위치 액션 항목에 대한 구성입니다.

자세한 내용은 `EMAJointPositionToLimitsAction`를 참조하세요.

**속성:**

| [`alpha`](#isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg.alpha)                         | 이동 평균의 가중치(float 또는 정규 표현식 딕셔너리).     |
|-------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg.asset_name)               | 장면 엔티티의 이름입니다.                                               |
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg.debug_vis)                 | 디버그 정보를 시각화할지 여부입니다.                                     |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg.clip)                           | 액션의 클립 범위(정규 표현식 딕셔너리).                      |
| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg.joint_names)             | 해당 액션이 매핑될 조인트 이름 또는 정규 표현식 목록입니다. |
| [`scale`](#isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg.scale)                         | 액션의 스케일 팩터(float 또는 정규 표현식 딕셔너리).           |
| [`rescale_to_limits`](#isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg.rescale_to_limits) | 조인트 제한으로 액션을 재조정할지 여부입니다.                          |
| [`preserve_order`](#isaaclab.envs.mdp.actions.actions_cfg.EMAJointPositionToLimitsActionCfg.preserve_order)       | 액션 출력에서 조인트 이름의 순서를 보존할지 여부입니다.      |

#### alpha *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

이동 평균 가중치(부동 소수점 또는 정규식 표현의 딕셔너리). 기본값은 1.0입니다.

1.0으로 설정된 경우, 이동 평균 창 없이 처리된 행동이 직접 적용됩니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이 이름은 씬 구성 파일에 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

행동에 대한 클립 범위(정규식 표현의 딕셔너리). 기본값은 None입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

행동이 매핑될 조인트 이름 또는 정규식 표현의 목록입니다.

#### scale *: [float](https://docs.python.org/3/library/functions.html#float) | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

행동의 스케일 팩터(부동 소수점 또는 정규식 표현의 딕셔너리). 기본값은 1.0입니다.

#### rescale_to_limits *: [bool](https://docs.python.org/3/library/functions.html#bool)*

조인트 제한으로 행동을 재스케일링할지 여부입니다. 기본값은 True입니다.

True인 경우, 입력 행동이 조인트 제한으로 재스케일링되며, 즉 [-1, 1] 범위의 행동 값은 조인트 하한 및 상한에 각각 대응됩니다.

#### NOTE
이 작업은 스케일 팩터를 적용한 후에 수행됩니다.

#### preserve_order *: [bool](https://docs.python.org/3/library/functions.html#bool)*

행동 출력에서 조인트 이름의 순서를 보존할지 여부입니다. 기본값은 False입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg

Bases: [`ActionTermCfg`](isaaclab.managers.md#isaaclab.managers.ActionTermCfg)

바이너리 조인트 액션 용어의 기본 구성입니다.

자세한 내용은 `BinaryJointAction`을 참조하세요.

**속성:**

| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg.joint_names)               | 행동이 매핑될 조인트 이름 또는 정규식 표현의 목록입니다.   |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [`open_command_expr`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg.open_command_expr)   | *open* 구성으로 이동할 조인트 명령입니다.                            |
| [`close_command_expr`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg.close_command_expr) | *close* 구성으로 이동할 조인트 명령입니다.                           |
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg.asset_name)                 | 씬 엔티티의 이름입니다.                                                 |
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg.debug_vis)                   | 디버그 정보를 시각화할지 여부입니다.                                       |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg.clip)                             | 행동에 대한 클립 범위(정규식 표현의 딕셔너리).                        |

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

행동이 매핑될 조인트 이름 또는 정규식 표현의 목록입니다.

#### open_command_expr *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

*open* 구성으로 이동할 조인트 명령입니다.

#### close_command_expr *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

*close* 구성으로 이동할 조인트 명령입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이 이름은 씬 구성 파일에 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

행동에 대한 클립 범위(정규식 표현의 딕셔너리). 기본값은 None입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.BinaryJointPositionActionCfg

Bases: [`BinaryJointActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg)

바이너리 조인트 위치 액션 용어의 구성입니다.

자세한 내용은 `BinaryJointPositionAction`을 참조하세요.

**속성:**

| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointPositionActionCfg.asset_name)                 | 씬 엔티티의 이름입니다.                                               |
|----------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointPositionActionCfg.debug_vis)                   | 디버그 정보를 시각화할지 여부입니다.                                     |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointPositionActionCfg.clip)                             | 행동에 대한 클립 범위(정규식 표현의 딕셔너리).                      |
| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointPositionActionCfg.joint_names)               | 행동이 매핑될 조인트 이름 또는 정규식 표현의 목록입니다. |
| [`open_command_expr`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointPositionActionCfg.open_command_expr)   | *open* 구성으로 이동할 조인트 명령입니다.                          |
| [`close_command_expr`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointPositionActionCfg.close_command_expr) | *close* 구성으로 이동할 조인트 명령입니다.                         |

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이 이름은 씬 구성 파일에 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

행동에 대한 클립 범위(정규식 표현의 딕셔너리). 기본값은 None입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

행동이 매핑될 조인트 이름 또는 정규식 표현의 목록입니다.

#### open_command_expr *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

*open* 구성으로 이동할 조인트 명령입니다.

#### close_command_expr *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

*close* 구성으로 이동할 조인트 명령입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.BinaryJointVelocityActionCfg

Bases: [`BinaryJointActionCfg`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointActionCfg)

바이너리 조인트 속도 액션 용어의 구성입니다.

자세한 내용은 `BinaryJointVelocityAction`을 참조하세요.

**속성:**

| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointVelocityActionCfg.asset_name)                 | 씬 엔티티의 이름입니다.                                               |
|----------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointVelocityActionCfg.debug_vis)                   | 디버그 정보를 시각화할지 여부입니다.                                     |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointVelocityActionCfg.clip)                             | 행동에 대한 클립 범위(정규식 표현의 딕셔너리).                      |
| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointVelocityActionCfg.joint_names)               | 행동이 매핑될 조인트 이름 또는 정규식 표현의 목록입니다. |
| [`open_command_expr`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointVelocityActionCfg.open_command_expr)   | *open* 구성으로 이동할 조인트 명령입니다.                          |
| [`close_command_expr`](#isaaclab.envs.mdp.actions.actions_cfg.BinaryJointVelocityActionCfg.close_command_expr) | 닫기 구성을 위해 이동하는 조인트 명령입니다.                         |

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이 이름은 씬 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하십시오.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

정규 표현식 딕셔너리인 액션의 클립 범위입니다. 기본값은 None입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

액션이 매핑될 조인트 이름 또는 정규 표현식 리스트입니다.

#### open_command_expr *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

열기 구성을 위해 이동하는 조인트 명령입니다.

#### close_command_expr *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

닫기 구성을 위해 이동하는 조인트 명령입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg

Bases: [`ActionTermCfg`](isaaclab.managers.md#isaaclab.managers.ActionTermCfg)

절대 이진 조인트 위치 액션 용어의 구성입니다.

이 액션 용어는 연속적인 그리퍼 조인트 위치 액션을 바이너리 열기/닫기 명령으로 변환하여 강력한 grasping을 위해 사용됩니다. 지속적인 그리퍼 조인트 위치 액션을 직접 적용하는 것과 달리, 이 클래스는 threshold 기반 의사결정 메커니즘을 적용하여 그립퍼를 열지 닫을지 결정합니다.

이 액션은 다음과 같이 작동합니다:
1. 연속적인 입력 액션 값 가져오기
2. 구성 가능한 threshold와 비교하기
3. threshold 비교 결과를 바탕으로 열기 또는 닫기 명령에 매핑하기
4. 해당하는 그립퍼 열기/닫기 명령 적용하기

이 접근 방식은 지속적인 그리퍼 조인트 위치 액션을 직접 적용하는 것보다 예측 가능하고 안정적인 grasping 동작을 제공합니다.

자세한 내용은 `AbsBinaryJointPositionAction`을 참조하십시오.

**Attributes:**

| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg.joint_names)               | 액션이 매핑될 조인트 이름 또는 정규 표현식 리스트입니다.   |
|-------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [`open_command_expr`](#isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg.open_command_expr)   | 열기 구성을 위해 이동하는 조인트 명령입니다.                            |
| [`close_command_expr`](#isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg.close_command_expr) | 닫기 구성을 위해 이동하는 조인트 명령입니다.                           |
| [`threshold`](#isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg.threshold)                   | 이진 액션의 threshold입니다.                                          |
| [`positive_threshold`](#isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg.positive_threshold) | 양의 threshold(열기 액션 > 닫기 액션)를 사용할지 여부입니다.             |
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg.asset_name)                 | 씬 엔티티의 이름입니다.                                                 |
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg.debug_vis)                   | 디버그 정보를 시각화할지 여부입니다.                                       |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.AbsBinaryJointPositionActionCfg.clip)                             | 정규 표현식 딕셔너리인 액션의 클립 범위입니다.                        |

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

액션이 매핑될 조인트 이름 또는 정규 표현식 리스트입니다.

#### open_command_expr *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

열기 구성을 위해 이동하는 조인트 명령입니다.

#### close_command_expr *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]*

닫기 구성을 위해 이동하는 조인트 명령입니다.

#### threshold *: [float](https://docs.python.org/3/library/functions.html#float)*

이진 액션의 threshold입니다. 기본값은 0.5입니다.

#### positive_threshold *: [bool](https://docs.python.org/3/library/functions.html#bool)*

양의 threshold(열기 액션 > 닫기 액션)를 사용할지 여부입니다. 기본값은 True입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이 이름은 씬 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하십시오.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

정규 표현식 딕셔너리인 액션의 클립 범위입니다. 기본값은 None입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg

Bases: [`ActionTermCfg`](isaaclab.managers.md#isaaclab.managers.ActionTermCfg)

기초에 더미 조인트가 있는 비홀노믹 액션 용어의 구성입니다.

자세한 내용은 `NonHolonomicAction`을 참조하십시오.

**Attributes:**

| [`body_name`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg.body_name)           | 더미 메커니즘이 연결된 바디의 이름입니다.   |
|-------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| [`x_joint_name`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg.x_joint_name)     | x 방향의 더미 조인트 이름입니다.                       |
| [`y_joint_name`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg.y_joint_name)     | y 방향의 더미 조인트 이름입니다.                       |
| [`yaw_joint_name`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg.yaw_joint_name) | yaw 방향의 더미 조인트 이름입니다.                     |
| [`scale`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg.scale)                   | 액션의 스케일 팩터입니다.                                   |
| [`offset`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg.offset)                 | 액션의 오프셋 팩터입니다.                                  |
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg.asset_name)         | 씬 엔티티의 이름입니다.                                  |
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg.debug_vis)           | 디버그 정보를 시각화할지 여부입니다.                        |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.NonHolonomicActionCfg.clip)                     | 정규 표현식 딕셔너리인 액션의 클립 범위입니다.         |

#### body_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

더미 메커니즘이 연결된 바디의 이름입니다.

#### x_joint_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

x 방향의 더미 조인트 이름입니다.

#### y_joint_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

y 방향의 더미 조인트 이름입니다.

#### yaw_joint_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

yaw 방향의 더미 조인트 이름입니다.

#### scale *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

액션의 스케일 팩터입니다. 기본값은 (1.0, 1.0)입니다.

#### offset *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

액션의 오프셋 팩터입니다. 기본값은 (0.0, 0.0)입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이 이름은 씬 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하십시오.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

정규 표현식 딕셔너리인 액션의 클립 범위입니다. 기본값은 None입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg

기반 클래스: [`ActionTermCfg`](isaaclab.managers.md#isaaclab.managers.ActionTermCfg)

역동적 역기구학 동작 항목에 대한 구성입니다.

자세한 내용은 `DifferentialInverseKinematicsAction`을 참조하세요.

**클래스:**

| [`OffsetCfg`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.OffsetCfg)   | 부모 프레임에서 자식 프레임으로의 오프셋 포즈입니다.   |
|----------------------------------------------------------------------------------------------------------|-----------------------------------------------------|

**속성:**

| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.asset_name)   | 씬 엔티티의 이름입니다.                                               |
|------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.debug_vis)     | 디버그 정보를 시각화할지 여부입니다.                                     |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.clip)               | 동작의 클립 범위(정규 표현식 딕셔너리)입니다.                      |
| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.joint_names) | 동작이 매핑될 조인트 이름 또는 정규 표현식 목록입니다. |
| [`body_name`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.body_name)     | IK이 수행될 바디 또는 프레임의 이름입니다.                        |
| [`body_offset`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.body_offset) | 대상 프레임의 오프셋(기준 프레임 기준)입니다.                     |
| [`scale`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.scale)             | 동작의 스케일 팩터입니다.                                                |
| [`controller`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.controller)   | 차동 IK 컨트롤러의 구성입니다.                       |

#### *class* OffsetCfg

기반 클래스: [`object`](https://docs.python.org/3/library/functions.html#object)

부모 프레임에서 자식 프레임으로의 오프셋 포즈입니다.

많은 로봇에서 엔드 이펙터 프레임은 가상의 프레임으로 해당하는 강성 바디가 없습니다. 이런 경우, 부모 강성 바디에 대해 이 변환을 정의하는 것이 더 쉽습니다. 예를 들어, Franka Emika 팔의 경우 엔드 이펙터는 "panda_hand" 프레임 기준으로 오프셋되어 정의됩니다.

**속성:**

| [`pos`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.OffsetCfg.pos)   | 부모 프레임 기준 이동입니다.                        |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------|
| [`rot`](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.OffsetCfg.rot)   | 부모 프레임 기준 쿼터니언 회전 `(w, x, y, z)`입니다.   |

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임 기준 이동입니다. 기본값은 (0.0, 0.0, 0.0)입니다.

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임 기준 쿼터니언 회전 `(w, x, y, z)`입니다. 기본값은 (1.0, 0.0, 0.0, 0.0)입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이것은 씬 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

동작의 클립 범위(정규 표현식 딕셔너리)입니다. 기본값은 None입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

동작이 매핑될 조인트 이름 또는 정규 표현식 목록입니다.

#### body_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

IK이 수행될 바디 또는 프레임의 이름입니다.

#### body_offset *: [OffsetCfg](#isaaclab.envs.mdp.actions.actions_cfg.DifferentialInverseKinematicsActionCfg.OffsetCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

대상 프레임의 바디 프레임 기준 오프셋입니다. 기본값은 None이며, 이 경우 오프셋이 적용되지 않습니다.

#### scale *: [float](https://docs.python.org/3/library/functions.html#float) | [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), ...]*

동작의 스케일 팩터입니다. 기본값은 1.0입니다.

#### controller *: [DifferentialIKControllerCfg](isaaclab.controllers.md#isaaclab.controllers.DifferentialIKControllerCfg)*

차동 IK 컨트롤러의 구성입니다.

### *class* isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg

기반 클래스: [`ActionTermCfg`](isaaclab.managers.md#isaaclab.managers.ActionTermCfg)

작업 공간 컨트롤러 동작 항목에 대한 구성입니다.

자세한 내용은 `OperationalSpaceControllerAction`을 참조하세요.

**속성:**

| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.asset_name)                                 | 씬 엔티티의 이름입니다.                                                          |
|---------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.debug_vis)                                   | 디버그 정보를 시각화할지 여부입니다.                                                |
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.clip)                                             | 동작의 클립 범위(정규 표현식 딕셔너리)입니다.                                 |
| [`joint_names`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.joint_names)                               | 동작이 매핑될 조인트 이름 또는 정규 표현식 목록입니다.            |
| [`body_name`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.body_name)                                   | 움직임/힘 제어가 수행될 바디 또는 프레임의 이름입니다.                 |
| [`body_offset`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.body_offset)                               | 대상 프레임의 바디 프레임 기준 오프셋입니다.                               |
| [`task_frame_rel_path`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.task_frame_rel_path)               | 하위 환경 기준으로 task frame을 나타내는 `RigidObject`의 경로입니다. |
| [`controller_cfg`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.controller_cfg)                         | 작업 공간 컨트롤러의 구성입니다.                                |
| [`position_scale`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.position_scale)                         | 위치 타겟의 스케일 팩터입니다.                                                 |
| [`orientation_scale`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.orientation_scale)                   | 방향(quad for `pose_abs` 또는 axis-angle for `pose_rel`)의 스케일 팩터입니다.   |
| [`wrench_scale`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.wrench_scale)                             | 힘/토크 타겟의 스케일 팩터입니다.                                                   |
| [`stiffness_scale`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.stiffness_scale)                       | 강성 명령의 스케일 팩터입니다.                                               |
| [`damping_ratio_scale`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.damping_ratio_scale)               | 감쇠 비율 명령의 스케일 팩터입니다.                                           |
| [`nullspace_joint_pos_target`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.nullspace_joint_pos_target) | `"none"`, `"zero"`, `"default"`, `"center"` 중 하나입니다.                                           |

**클래스:**

| [`OffsetCfg`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.OffsetCfg)   | 부모 프레임에서 자식 프레임으로의 오프셋 포즈입니다.   |
|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------|

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬 엔티티의 이름입니다.

이것은 씬 구성 파일에서 정의된 이름입니다. 자세한 내용은 `InteractiveSceneCfg` 클래스를 참조하세요.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부를 나타냅니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

작업의 클립 범위(정규식 표현의 딕셔너리)입니다. 기본값은 None입니다.

#### *class* OffsetCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

부모 프레임에서 자식 프레임까지의 오프셋 포즈입니다.

많은 로봇에서 엔드이펙터 프레임은 해당 강성 바디가 없는 가상의 프레임입니다. 이러한 경우, 해당 강성 바체에 대해 이 변환을 정의하는 것이 더 간단합니다. 예를 들어, Franka Emika 팔의 경우, 엔드이펙터는 "panda_hand" 프레임을 기준으로 오프셋된 위치에 정의됩니다.

**속성:**

| [`pos`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.OffsetCfg.pos)   | 부모 프레임 기준 이동       |
|-----------------------------------------------------------------------------------------------------|-----------------------------|
| [`rot`](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.OffsetCfg.rot)   | 부모 프레임 기준 쿼터니언 회전 `(w, x, y, z)` |

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임 기준 이동. 기본값은 (0.0, 0.0, 0.0)입니다.

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임 기준 쿼터니언 회전 `(w, x, y, z)`. 기본값은 (1.0, 0.0, 0.0, 0.0)입니다.

#### joint_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

작업이 매핑될 조인트 이름 또는 정규식 표현 목록입니다.

#### body_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

모션/포스 제어가 수행되는 바디 또는 프레임의 이름입니다.

#### body_offset *: [OffsetCfg](#isaaclab.envs.mdp.actions.actions_cfg.OperationalSpaceControllerActionCfg.OffsetCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

바디 프레임 기준 대상 프레임의 오프셋입니다. 기본값은 None이며, 이 경우 오프셋이 적용되지 않습니다.

#### task_frame_rel_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

서브환경을 기준으로 하는 `RigidObject`의 경로를 나타내며, 작업 프레임을 나타냅니다. 기본값은 None입니다.

#### controller_cfg *: [OperationalSpaceControllerCfg](isaaclab.controllers.md#isaaclab.controllers.OperationalSpaceControllerCfg)*

작업 공간 컨트롤러의 구성입니다.

#### position_scale *: [float](https://docs.python.org/3/library/functions.html#float)*

위치 목표의 배율 인자입니다. 기본값은 1.0입니다.

#### orientation_scale *: [float](https://docs.python.org/3/library/functions.html#float)*

자세( pose_abs 쿼터니언 또는 pose_rel 축-각도) 목표의 배율 인자입니다. 기본값은 1.0입니다.

#### wrench_scale *: [float](https://docs.python.org/3/library/functions.html#float)*

와렌치 목표의 배율 인자입니다. 기본값은 1.0입니다.

#### stiffness_scale *: [float](https://docs.python.org/3/library/functions.html#float)*

강성 명령의 배율 인자입니다. 기본값은 1.0입니다.

#### damping_ratio_scale *: [float](https://docs.python.org/3/library/functions.html#float)*

감쇠 비율 명령의 배율 인자입니다. 기본값은 1.0입니다.

#### nullspace_joint_pos_target *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

`"none"`, `"zero"`, `"default"`, `"center"`.

참고: `nullspace_control`이 `OperationalSpaceControllerCfg` 내에서 `"position"`으로 설정된 경우에만 기능합니다.

* **타입:**
  null-space 제어를 위한 조인트 목표값

### *class* isaaclab.envs.mdp.actions.actions_cfg.SurfaceGripperBinaryActionCfg

Bases: [`ActionTermCfg`](isaaclab.managers.md#isaaclab.managers.ActionTermCfg)

바이너리 서페이스 그리퍼 액션 항목의 구성입니다.

자세한 내용은 `SurfaceGripperBinaryAction`을 참조하세요.

**속성:**

| [`debug_vis`](#isaaclab.envs.mdp.actions.actions_cfg.SurfaceGripperBinaryActionCfg.debug_vis)         | 디버그 정보를 시각화할지 여부                |
|-------------------------------------------------------------------------------------------------------|----------------------------------------------|
| [`clip`](#isaaclab.envs.mdp.actions.actions_cfg.SurfaceGripperBinaryActionCfg.clip)                   | 작업의 클립 범위(정규식 표현의 딕셔너리)   |
| [`asset_name`](#isaaclab.envs.mdp.actions.actions_cfg.SurfaceGripperBinaryActionCfg.asset_name)       | 씬에서의 서페이스 그리퍼 에셋 이름         |
| [`open_command`](#isaaclab.envs.mdp.actions.actions_cfg.SurfaceGripperBinaryActionCfg.open_command)   | 그리퍼를 여는 명령 값                      |
| [`close_command`](#isaaclab.envs.mdp.actions.actions_cfg.SurfaceGripperBinaryActionCfg.close_command) | 그리퍼를 닫는 명령 값                      |

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부를 나타냅니다. 기본값은 False입니다.

#### clip *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)] | [None](https://docs.python.org/3/library/constants.html#None)*

작업의 클립 범위(정규식 표현의 딕셔너리)입니다. 기본값은 None입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

씬에서의 서페이스 그리퍼 에셋 이름입니다.

#### open_command *: [float](https://docs.python.org/3/library/functions.html#float)*

그리퍼를 여는 명령 값입니다. 기본값은 -1.0입니다.

#### close_command *: [float](https://docs.python.org/3/library/functions.html#float)*

그리퍼를 닫는 명령 값입니다. 기본값은 1.0입니다.

## 이벤트

시뮬레이션 상태를 변경할 수 있는 다양한 이벤트를 활성화하는 데 사용할 수 있는 공통 함수입니다.

이벤트는 물리 머터리얼 변경, 외부 힘 적용, 에셋 상태 재설정 등 시뮬레이션 상태를 변경하는 모든 것을 포함합니다.

이 함수들은 [`isaaclab.managers.EventTermCfg`](isaaclab.managers.md#isaaclab.managers.EventTermCfg) 객체에 전달되어 해당 함수가 도입한 이벤트를 활성화할 수 있습니다.

**함수:**

| [`randomize_rigid_body_scale`](#isaaclab.envs.mdp.events.randomize_rigid_body_scale)(env, env_ids, ...)               | USD 단계에서 강성 바디 에셋의 크기를 무작위로 조정합니다.                                                                                           |
|-----------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`randomize_rigid_body_com`](#isaaclab.envs.mdp.events.randomize_rigid_body_com)(env, env_ids, ...)                   | 주어진 범위에서 샘플링된 무작위 값을 더해 강성 바디의 질량 중심을 무작위로 조정합니다.                                            |
| [`randomize_rigid_body_collider_offsets`](#isaaclab.envs.mdp.events.randomize_rigid_body_collider_offsets)(env, ...)  | 강성 바디의 충돌 파라미터를 USD 에셋 내에서 더하기, 배율 조정 또는 무작위 값 설정으로 무작위로 조정합니다.                                           |
| [`randomize_physics_scene_gravity`](#isaaclab.envs.mdp.events.randomize_physics_scene_gravity)(env, ...[, ...])       | 무작위 값을 더하거나 배율 조정 또는 설정으로 중력을 무작위로 조정합니다.                                                                                       |
| [`apply_external_force_torque`](#isaaclab.envs.mdp.events.apply_external_force_torque)(env, env_ids, ...)             | 바디에 적용되는 외부 힘과 토크를 무작위로 조정합니다.                                                                                      |
| [`push_by_setting_velocity`](#isaaclab.envs.mdp.events.push_by_setting_velocity)(env, env_ids, ...)                   | 루트 속도를 주어진 범위 내의 무작위 값으로 설정하여 에셋을 밀냅니다.                                                                |
| [`reset_root_state_uniform`](#isaaclab.envs.mdp.events.reset_root_state_uniform)(env, env_ids, ...)                   | 에셋의 루트 상태를 주어진 범위 내에서 무작위 위치와 속도로 균일하게 재설정합니다.                                                       |
| [`reset_root_state_with_random_orientation`](#isaaclab.envs.mdp.events.reset_root_state_with_random_orientation)(...) | 에셋의 루트 위치와 속도를 주어진 범위 내에서 무작위로 샘플링하고, 루트 방향을 SO(3)에서 무작위로 샘플링하여 재설정합니다. |
| [`reset_root_state_from_terrain`](#isaaclab.envs.mdp.events.reset_root_state_from_terrain)(env, env_ids, ...)         | 지형에서 유효한 무작위 포즈를 샘플링하여 에셋의 루트 상태를 재설정합니다.                                                                          |
| [`reset_joints_by_scale`](#isaaclab.envs.mdp.events.reset_joints_by_scale)(env, env_ids, ...[, ...])                  | 지정된 범위로 기본 위치와 속도를 배 scale하여 로봇 관절을 초기화합니다.                                                              |
| [`reset_joints_by_offset`](#isaaclab.envs.mdp.events.reset_joints_by_offset)(env, env_ids, ...[, ...])                | 지정된 범위로 기본 위치와 속도에 오프셋을 더해 로봇 관절을 초기화합니다.                                                     |
| [`reset_nodal_state_uniform`](#isaaclab.envs.mdp.events.reset_nodal_state_uniform)(env, env_ids, ...)                 | 주어진 범위 내에서 자산의 노드 상태를 균등한 분포로 무작위 위치와 속도로 초기화합니다.                                                      |
| [`reset_scene_to_default`](#isaaclab.envs.mdp.events.reset_scene_to_default)(env, env_ids[, ...])                     | 씬 구성에서 지정된 기본 상태로 씬을 초기화합니다.                                                                            |

**Classes:**

| [`randomize_rigid_body_material`](#isaaclab.envs.mdp.events.randomize_rigid_body_material)         | 자산의 모든 기하학에 대한 물리 재질을 무작위로 설정합니다.                                                  |
|----------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| [`randomize_rigid_body_mass`](#isaaclab.envs.mdp.events.randomize_rigid_body_mass)                 | 추가, 스케일링 또는 무작위 값 설정을 통해 본체의 질량을 무작위로 설정합니다.                                   |
| [`randomize_actuator_gains`](#isaaclab.envs.mdp.events.randomize_actuator_gains)                   | 추가, 스케일링 또는 무작위 값 설정을 통해 기사큘레이션의 액추에이터 이득을 무작위로 설정합니다.                    |
| [`randomize_joint_parameters`](#isaaclab.envs.mdp.events.randomize_joint_parameters)               | 추가, 스케일링 또는 무작위 값 설정을 통해 기사큘레이션의 시뮬레이티드 조인트 파라미터를 무작위로 설정합니다.        |
| [`randomize_fixed_tendon_parameters`](#isaaclab.envs.mdp.events.randomize_fixed_tendon_parameters) | 추가, 스케일링 또는 무작위 값 설정을 통해 기사큘레이션의 시뮬레이티드 고정 테넌 파라미터를 무작위로 설정합니다. |
| [`randomize_visual_texture_material`](#isaaclab.envs.mdp.events.randomize_visual_texture_material) | Replicator API를 사용하여 자산의 바디에 대한 시각적 텍스처를 무작위로 설정합니다.                                         |
| [`randomize_visual_color`](#isaaclab.envs.mdp.events.randomize_visual_color)                       | Replicator API를 사용하여 자산의 바디에 대한 시각적 색상을 무작위로 설정합니다.                                           |

### isaaclab.envs.mdp.events.randomize_rigid_body_scale(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), scale_range: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg), relative_child_path: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

USD 단계에서 강체 자산의 스케일을 무작위로 설정합니다.

이 함수는 자산에 해당하는 모든 프리ム의 “xformOp:scale” 속성을 수정합니다.

스케일 범위로 튜플 또는 사전을 입력받습니다. 튜플인 경우, 개별 축에 대해 동일하게 스케일이 적용됩니다. 사전인 경우, 각 차원에 대해 독립적으로 스케일이 적용됩니다.
사전의 키는 `x`, `y`, `z`이며, 값은 `(min, max)` 형태의 튜플입니다.

사전에 특정 키가 없을 경우, 해당 축의 범위는 1로 설정됩니다.

자식 경로 상대 경로는 자산의 특정 자식 프리ム의 스케일을 무작위로 설정하는 데 사용할 수 있습니다.
예를 들어, asset의 prim 경로 표현이 `/World/envs/env_.*/Object`이고, 해당 자식 프리ム의 경로가 `/World/envs/env_.*/Object/mesh`라면, 상대 자식 경로는 `mesh` 또는 `/mesh`여야 합니다.

#### ATTENTION
이 함수는 시뮬레이션 시작 시 물리 엔진이 파싱하는 USD 속성을 수정하므로, 시뮬레이션이 재생되기 전에만 사용해야 합니다. 이는 이벤트 모드에서 이름 지정되는 “usd”에 해당합니다. 시뮬레이션 중에 사용할 경우 예측할 수 없는 동작이 발생할 수 있습니다.

#### NOTE
개별 자산의 스케일을 무작위로 설정할 때는 다음을 반드시 설정해야 합니다:
[`isaaclab.scene.InteractiveSceneCfg.replicate_physics`](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg.replicate_physics)를 False로 설정합니다.これにより物理パーサーが個々のアセットのプロパティを別々にパースすることを確保します。

### *class* isaaclab.envs.mdp.events.randomize_rigid_body_material

자산의 모든 기하학에 대한 물리 재질을 무작위로 설정합니다.

이 함수는 정적 마찰, 동적 마찰, restituion 값이 무작위인 물리 재질의 세트를 생성합니다. 재질의 개수는 `num_buckets`로 지정됩니다. 재질은 지정된 범위 내에서 균등한 분포를 따르는 무작위 값으로 샘플링됩니다.

생성된 재질은 자산의 기하학에 할당됩니다. 할당 방법은 `(num_instances, max_num_shapes)` 형태의 무작위 정수 텐서를 생성하는 것으로 이루어집니다. 여기서 `num_instances`는 생성된 자산의 개수이고, `max_num_shapes`는 자산 내 모든 바디에서의 최대 셰이프 수입니다. 이 정수 값들은 재질 버킷에서 재질 속성을 선택하는 인덱스로 사용됩니다.

`make_consistent` 플래그가 `True`로 설정된 경우, 동적 마찰이 정적 마찰보다 작거나 같도록 설정됩니다. 이는 물리적으로 마찰 값에 대한 제약을 준수합니다. 다만, 항상 애플리케이션에 필수적일 필요는 없으므로, 기본값은 `False`로 설정됩니다.

#### ATTENTION
이 함수는 CPU 텐서를 사용하여 물성 재질 속성을 할당합니다. 따라서 환경 초기화 시에만 사용하는 것이 권장됩니다. 그렇지 않으면 성능에 상당한 오버헤드가 발생할 수 있습니다.

#### NOTE
PhysX는 씬에서 최대 64000개의 고유한 물리 재질만 허용합니다. 재질의 수가 이 제한을 초과하면 시뮬레이션이崩괴될 수 있습니다.このため、初期化時にのみ材料をサンプリングします。その後、これらの材料はアセットのジオメトリにランダムに割り当てられます。

**Methods:**

| [`__init__`](#isaaclab.envs.mdp.events.randomize_rigid_body_material.__init__)(cfg, env)   | 용어를 초기화합니다.   |
|--------------------------------------------------------------------------------------------|------------------------|

#### \_\_init_\_(cfg: [EventTermCfg](isaaclab.managers.md#isaaclab.managers.EventTermCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

용어를 초기화합니다.

* **매개변수:**
  * **cfg** – 이벤트 항의 구성입니다.
  * **env** – 환경 인스턴스입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 자산이 RigidObject 또는 Articulation이 아닌 경우 발생합니다.

### *class* isaaclab.envs.mdp.events.randomize_rigid_body_mass

추가, 스케일링 또는 무작위 값 설정을 통해 본체의 질량을 무작위로 설정합니다.

이 함수는 자산의 본체 질량을 무작위로 설정할 수 있도록 합니다. 주어진 분포 매개변수에서 무작위 값을 샘플링하고, 연산에 따라 물리 시뮬레이션에 값을 더하거나, 스케일링하거나, 설정합니다.

`recompute_inertia` 플래그가 [`True`](https://docs.python.org/3/library/constants.html#True)로 설정된 경우, 질량을 설정한 후 본체의 관성 텐서를 재계산합니다. 이는 질량이 크게 변했을 때 유용하며, 관성 텐서는 질량에 의존하기 때문입니다. 균일한 밀도의 물체를 가정합니다. 물체가 균일한 밀도가 아닌 경우, 관성 텐서가 정확하지 않을 수 있습니다.

**Methods:**

| [`__init__`](#isaaclab.envs.mdp.events.randomize_rigid_body_mass.__init__)(cfg, env)   | 용어를 초기화합니다.   |
|----------------------------------------------------------------------------------------|------------------------|

#### \_\_init_\_(cfg: [EventTermCfg](isaaclab.managers.md#isaaclab.managers.EventTermCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

용어를 초기화합니다.

* **매개변수:**
  * **cfg** – 이벤트 항의 구성입니다.
  * **env** – 환경 인스턴스입니다.
* **예외:**
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – params가 두 숫자의 튜플이 아닌 경우 발생합니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지원되지 않는 연산입니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 허용되지 않는 경우 하한이 음수 또는 0입니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 상한이 하한보다 작습니다.

### isaaclab.envs.mdp.events.randomize_rigid_body_com(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), com_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg))

강체의 질량 중심(CoM)에 주어진 범위에서 샘플링된 랜덤 값을 더하여 무작위로 만듭니다.

#### 참고
이 함수는 CoM을 할당하기 위해 CPU 텐서를 사용합니다. 환경 초기화 중에만 이 함수를 사용하는 것이 좋습니다.

### isaaclab.envs.mdp.events.randomize_rigid_body_collider_offsets(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg), rest_offset_distribution_params: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, contact_offset_distribution_params: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None) = None, distribution: Literal['uniform', 'log_uniform', 'gaussian'] = 'uniform')

에셋의 강체에 대해 콜라이더 매개변수를 추가, 스케일링 또는 랜덤 값으로 설정하여 무작위로 만듭니다.

이 함수는 에셋의 콜라이더 매개변수(휴식 및 접촉 오프셋 등)를 무작위로 만듭니다. 이들은 충돌 검사에 영향을 주는 물리 엔진의 콜라이더 속성에 해당합니다.

이 함수는 주어진 분포 매개변수에서 랜덤 값을 샘플링하여 콜라이더 속성에 연산을 적용한 다음, 물리 시뮬레이션에 값을 설정합니다. 특정 속성에 대해 분포 매개변수가 제공되지 않은 경우, 해당 속성은 수정되지 않습니다.

현재, 분포 매개변수는 절대값으로 적용됩니다.

### isaaclab.envs.mdp.events.randomize_physics_scene_gravity(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), gravity_distribution_params: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[float](https://docs.python.org/3/library/functions.html#float)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[float](https://docs.python.org/3/library/functions.html#float)]], operation: Literal['add', 'scale', 'abs'], distribution: Literal['uniform', 'log_uniform', 'gaussian'] = 'uniform')

랜덤 값을 추가, 스케일링 또는 설정하여 중력을 무작위로 만듭니다.

이 함수는 물리 씬의 중력을 무작위로 만듭니다. 주어진 분포 매개변수에서 랜덤 값을 샘플링한 후, 연산에 따라 물리 시뮬레이션에 값을 추가, 스케일링 또는 설정합니다.

분포 매개변수는 각각 두 요소로 이루어진 리스트이며, 중력 벡터의 x, y, z 성분에 대한 분포의 하한과 상한을 나타냅니다. 이 함수는 각 성분에 대해 독립적으로 랜덤 값을 샘플링합니다.

#### 주의
이 함수는 모든 환경에 동일한 중력을 적용합니다.

### *class* isaaclab.envs.mdp.events.randomize_actuator_gains

연산에서 작동기 게인을 추가, 스케일링 또는 랜덤 값으로 설정하여 무작위로 만듭니다.

이 함수는 작동기의 강성 및 감쇠 게인을 무작위로 만듭니다.

이 함수는 주어진 분포 매개변수에서 랜덤 값을 샘플링하여 관절 속성에 연산을 적용한 다음, 값을 작동기 모델에 설정합니다. 특정 속성에 대해 분포 매개변수가 제공되지 않은 경우, 해당 속성은 수정되지 않습니다.

**메서드:**

| [`__init__`](#isaaclab.envs.mdp.events.randomize_actuator_gains.__init__)(cfg, env)   | 용어 초기화.   |
|---------------------------------------------------------------------------------------|------------------------|

#### \_\_init_\_(cfg: [EventTermCfg](isaaclab.managers.md#isaaclab.managers.EventTermCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

용어를 초기화합니다.

* **매개변수:**
  * **cfg** – 이벤트 용어의 구성.
  * **env** – 환경 인스턴스.
* **예외:**
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – params가 두 숫자의 튜플이 아닌 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 연산이 지원되지 않는 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 허용되지 않을 때 하한이 음수 또는 영인 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 상한이 하한보다 작은 경우.

### *class* isaaclab.envs.mdp.events.randomize_joint_parameters

연산에서 관절 매개변수를 추가, 스케일링 또는 랜덤 값으로 설정하여 무작위로 만듭니다.

이 함수는 에셋의 관절 매개변수를 무작위로 만듭니다. 이들은 관절 동작에 영향을 주는 물리 엔진의 관절 속성에 해당합니다. 해당 속성에는 관절 마찰 계수, 암추어, 관절 위치 한계가 포함됩니다.

이 함수는 주어진 분포 매개변수에서 랜덤 값을 샘플링하여 관절 속성에 연산을 적용한 다음, 값을 물리 시뮬레이션에 설정합니다. 특정 속성에 대해 분포 매개변수가 제공되지 않은 경우, 해당 속성은 수정되지 않습니다.

**메서드:**

| [`__init__`](#isaaclab.envs.mdp.events.randomize_joint_parameters.__init__)(cfg, env)   | 용어 초기화.   |
|-----------------------------------------------------------------------------------------|------------------------|

#### \_\_init_\_(cfg: [EventTermCfg](isaaclab.managers.md#isaaclab.managers.EventTermCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

용어를 초기화합니다.

* **매개변수:**
  * **cfg** – 이벤트 용어의 구성.
  * **env** – 환경 인스턴스.
* **예외:**
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – params가 두 숫자의 튜플이 아닌 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 연산이 지원되지 않는 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 허용되지 않을 때 하한이 음수 또는 영인 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 상한이 하한보다 작은 경우.

### *class* isaaclab.envs.mdp.events.randomize_fixed_tendon_parameters

연산에서 고정 테이던트 매개변수를 추가, 스케일링 또는 랜덤 값으로 설정하여 무작위로 만듭니다.

이 함수는 에셋의 고정 테이던트 매개변수를 무작위로 만듭니다. 이들은 관절 동작에 영향을 주는 물리 엔진의 테이던트 속성에 해당합니다.

이 함수는 주어진 분포 매개변수에서 랜덤 값을 샘플링하여 테이던트 속성에 연산을 적용한 다음, 값을 물리 시뮬레이션에 설정합니다. 특정 속성에 대해 분포 매개변수가 제공되지 않은 경우, 해당 속성은 수정되지 않습니다.

**메서드:**

| [`__init__`](#isaaclab.envs.mdp.events.randomize_fixed_tendon_parameters.__init__)(cfg, env)   | 용어 초기화.   |
|------------------------------------------------------------------------------------------------|------------------------|

#### \_\_init_\_(cfg: [EventTermCfg](isaaclab.managers.md#isaaclab.managers.EventTermCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

용어를 초기화합니다.

* **매개변수:**
  * **cfg** – 이벤트 용어의 구성.
  * **env** – 환경 인스턴스.
* **예외:**
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – params가 두 숫자의 튜플이 아닌 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 연산이 지원되지 않는 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 허용되지 않을 때 하한이 음수 또는 영인 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 상한이 하한보다 작은 경우.

### isaaclab.envs.mdp.events.apply_external_force_torque(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), force_range: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], torque_range: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

본문에서 기술된 외부 힘과 토크를 몸체에 적용합니다.

이 함수는 주어진 범위에서 무작위 힘과 토크를 샘플링하여 생성합니다. 힘과 토크의 수는 몸체 수와 환경 수의 곱과 같습니다. 힘과 토크는 `asset.set_external_force_and_torque`를 호출하여 몸체에 적용됩니다. 힘과 토크는 환경에서 `asset.write_data_to_sim()`가 호출될 때만 적용됩니다.

### isaaclab.envs.mdp.events.push_by_setting_velocity(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), velocity_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

루트 속도를 주어진 범위 내의 무작위 값으로 설정하여 자산을 밀칩니다.

이 함수는 루트 속도를 무작위로 변경하여 자산을 밀는 효과를 만듭니다. 주어진 범위에서 루트 속도를 샘플링하고 이를 물리 시뮬레이션에 설정합니다.

이 함수는 각 축과 회전에 대한 속도 범위를 담은 딕셔너리를 입력으로 받습니다. 딕셔너리의 키는 `x`, `y`, `z`, `roll`, `pitch`, `yaw`입니다. 값은 `(min, max)` 형태의 튜플입니다. 딕셔너리에 특정 키가 없는 경우, 해당 축의 속도는 0으로 설정됩니다.

### isaaclab.envs.mdp.events.reset_root_state_uniform(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), pose_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], velocity_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

자산의 루트 상태를 주어진 범위 내에서 균일 분포로 무작위 위치와 속도로 재설정합니다.

이 함수는 자산의 루트 위치와 속도를 무작위로 재설정합니다.

* 주어진 범위에서 루트 위치를 샘플링한 후, 기본 루트 위치에 더하여 물리 시뮬레이션에 설정합니다.
* 주어진 범위에서 루트 방향을 샘플링하여 물리 시뮬레이션에 설정합니다.
* 주어진 범위에서 루트 속도를 샘플링하여 물리 시뮬레이션에 설정합니다.

이 함수는 각 축과 회전에 대한 위치와 속도 범위를 담은 딕셔너리를 입력으로 받습니다. 딕셔너리의 키는 `x`, `y`, `z`, `roll`, `pitch`, `yaw`입니다. 값은 `(min, max)` 형태의 튜플입니다. 딕셔너리에 특정 키가 없는 경우, 해당 축의 위치 또는 속도는 0으로 설정됩니다.

### isaaclab.envs.mdp.events.reset_root_state_with_random_orientation(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), pose_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], velocity_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

주어진 범위 내에서 무작위로 샘플링된 자산의 루트 위치와 속도, 그리고 SO(3)에서 균일하게 샘플링된 자산의 루트 방향을 기준으로 루트 상태를 재설정합니다.

이 함수는 자산의 루트 위치와 속도를 무작위로 재설정합니다.

* 주어진 범위에서 루트 위치를 샘플링한 후, 기본 루트 위치에 더하여 물리 시뮬레이션에 설정합니다.
* SO(3)에서 균일하게 루트 방향을 샘플링하여 물리 시뮬레이션에 설정합니다.
* 주어진 범위에서 루트 속도를 샘플링하여 물리 시뮬레이션에 설정합니다.

이 함수는 각 축과 회전에 대한 위치와 속도 범위를 담은 딕셔너리를 입력으로 받습니다:

* `pose_range` - 각 축의 위치 범위를 담은 딕셔너리. 키는 `x`, `y`, `z`이며, 방향은 SO(3)에서 균일하게 샘플링됩니다.
* `velocity_range` - 각 축과 회전에 대한 속도 범위를 담은 딕셔너리. 키는 `x`, `y`, `z`, `roll`, `pitch`, `yaw`입니다.

값은 `(min, max)` 형태의 튜플입니다. 딕셔너리에 특정 키가 없는 경우, 해당 축의 위치는 0으로 설정됩니다.

### isaaclab.envs.mdp.events.reset_root_state_from_terrain(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), pose_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], velocity_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

지형에서 유효한 랜덤 포즈를 샘플링하여 자산의 루트 상태를 재설정합니다.

이 함수는 지형의 평평한 패치를 기반으로 유효한 랜덤 포즈를 샘플링하고, 자산의 루트 상태를 이 위치로 설정합니다. 또한 주어진 범위에서 무작위 속도를 샘플링하여 물리 시뮬레이션에 설정합니다.

이 함수는 각 축과 회전에 대한 위치와 속도 범위를 담은 딕셔너리를 입력으로 받습니다:

* `pose_range` - 각 축의 포즈 범위를 담은 딕셔너리. 키는 `roll`, `pitch`, `yaw`이며, 위치는 지형의 평평한 패치에서 샘플링됩니다.
* `velocity_range` - 각 축과 회전에 대한 속도 범위를 담은 딕셔너리. 키는 `x`, `y`, `z`, `roll`, `pitch`, `yaw`입니다.

값은 `(min, max)` 형태의 튜플입니다. 딕셔너리에 특정 키가 없는 경우, 해당 축의 위치는 0으로 설정됩니다.

#### 참고 사항
이 함수는 지형이 키 “init_pos” 아래에 유효한 평평한 패치를 가지고 있어야 한다고 기대합니다. 이 평평한 패치는 로봇의 랜덤 포즈를 샘플링하는 데 사용됩니다.

* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지형이 키 “init_pos” 아래에 유효한 평평한 패치를 가지고 있지 않은 경우.

### isaaclab.envs.mdp.events.reset_joints_by_scale(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), position_range: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], velocity_range: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

기본 위치와 속도를 주어진 범위로 스케일링하여 로봇 관절을 재설정합니다.

이 함수는 주어진 범위에서 무작위 값을 샘플링하여 기본 관절 위치와 속도를 이러한 값으로 스케일링합니다. 스케일링된 값은 물리 시뮬레이션에 설정됩니다.

### isaaclab.envs.mdp.events.reset_joints_by_offset(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), position_range: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], velocity_range: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

주어진 범위로 기본 위치와 속도 주변에 오프셋을 적용하여 로봇 관절을 재설정합니다.

이 함수는 주어진 범위에서 무작위 값을 샘플링하여 기본 관절 위치와 속도를 이러한 값으로 편향시킵니다. 편향된 값은 물리 시뮬레이션에 설정됩니다.

### isaaclab.envs.mdp.events.reset_nodal_state_uniform(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), position_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], velocity_range: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False))

자산의 노드 상태를 주어진 범위 내에서 균일하게 무작위 위치와 속도로 재설정합니다.

이 함수는 자산의 노드 위치와 속도를 무작위화합니다.

* 기본 노드 위치에 주어진 범위의 루트 위치를 샘플링하여 더한 후, 이를 물리 시뮬레이션에 설정합니다.
* 주어진 범위에서 루트 속도를 샘플링하여 물리 시뮬레이션에 설정합니다.

이 함수는 각 축에 대한 위치와 속도 범위의 딕셔너리를 받습니다. 딕셔너리의 키는 `x`, `y`, `z`입니다. 값은 `(min, max)` 형태의 튜플입니다.
딕셔너리에 키가 포함되지 않은 경우, 해당 축의 위치 또는 속도는 0으로 설정됩니다.

### isaaclab.envs.mdp.events.reset_scene_to_default(env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv), env_ids: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), reset_joint_targets: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

장면을 장면 구성에서 지정된 기본 상태로 재설정합니다.

`reset_joint_targets`가 True인 경우, 관절의 위치와 속도 목표도 기본값으로 재설정됩니다. 이전에 설정된 목표를 초기화해야 하는 경우에 유용할 수 있습니다.
그러나, 기본 동작이 아닌 이유는 경험상 목표를 기본값으로 재설정하는 것이 항상 바람직하지 않기 때문입니다. 특히 목표가 이벤트 항목이 아닌 동작 항목에 의해 처리되어야 하는 경우 더욱 그렇습니다.

### *class* isaaclab.envs.mdp.events.randomize_visual_texture_material

Replicator API를 사용하여 자산의 몸체에 대한 시각적 텍스처를 무작위화합니다.

이 함수는 Replicator API를 사용하여 자산의 몸체에 대한 시각적 텍스처를 무작위화합니다. 주어진 텍스처 경로에서 무작위 텍스처를 샘플링하여 자산의 몸체에 적용합니다. 텍스처는 몸체에 투영되고 주어진 각도로 회전됩니다.

#### 주의 사항
이 함수는 다음과 같은 프라이밍 명명 규칙을 따르는 자산을 가정합니다:
“{asset_prim_path}/{body_name}/visuals” 여기서 body 이름은 텍스처가 적용될 몸체의 이름입니다. 이는 Isaac Lab의 자산 변환기에서 자산을 가져올 때의 기본 프라이밍 순서입니다.

#### 주의 사항
개별 자산의 텍스처를 무작위화할 때는 다음을 반드시 설정해야 합니다:
[`isaaclab.scene.InteractiveSceneCfg.replicate_physics`](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg.replicate_physics)를 False로 설정합니다. 이렇게 하면 물리 파서가 개별 자산의 속성을 별도로 파싱하도록 보장합니다.

**메서드:**

| [`__init__`](#isaaclab.envs.mdp.events.randomize_visual_texture_material.__init__)(cfg, env)   | 항목을 초기화합니다.   |
|------------------------------------------------------------------------------------------------|------------------------|

#### \_\_init_\_(cfg: [EventTermCfg](isaaclab.managers.md#isaaclab.managers.EventTermCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

항목을 초기화합니다.

* **매개변수:**
  * **cfg** – 이벤트 항목의 구성입니다.
  * **env** – 환경 인스턴스입니다.

### *class* isaaclab.envs.mdp.events.randomize_visual_color

Replicator API를 사용하여 자산의 몸체에 대한 시각적 색상을 무작위화합니다.

이 함수는 Replicator API를 사용하여 자산의 몸체에 대한 시각적 색상을 무작위화합니다. 주어진 색상에서 무작위 색상을 샘플링하여 자산의 몸체에 적용합니다.

이 함수는 다음과 같은 프라이밍 명명 규칙을 따르는 자산을 가정합니다:
“{asset_prim_path}/{mesh_name}” 여기서 mesh 이름은 색상이 적용될 메시의 이름입니다. 예를 들어, 자산의 프라이밍 경로가 “/World/asset”이고 메시 이름이 “body_0/mesh”인 경우, 메시에 대한 프라이밍 경로는
“/World/asset/body_0/mesh”가 됩니다.

색상은 `(r, g, b)` 형태의 튜플 리스트로 지정하거나, 키가 `r`, `g`, `b`이고 값이 `(low, high)` 형태의 튜플인 딕셔너리로 지정할 수 있습니다.
딕셔너리가 사용되는 경우, 함수는 주어진 범위에서 무작위 색상을 샘플링합니다.

#### 주의 사항
개별 자산의 색상을 무작위화할 때는 다음을 반드시 설정해야 합니다:
[`isaaclab.scene.InteractiveSceneCfg.replicate_physics`](isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg.replicate_physics)를 False로 설정합니다. 이렇게 하면 물리 파서가 개별 자산의 속성을 별도로 파싱하도록 보장합니다.

**메서드:**

| [`__init__`](#isaaclab.envs.mdp.events.randomize_visual_color.__init__)(cfg, env)   | 무작위화 항목을 초기화합니다.   |
|-------------------------------------------------------------------------------------|--------------------------------------|

#### \_\_init_\_(cfg: [EventTermCfg](isaaclab.managers.md#isaaclab.managers.EventTermCfg), env: [ManagerBasedEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv))

무작위화 항목을 초기화합니다.

* **매개변수:**
  * **cfg** – 이벤트 항목의 구성입니다.
  * **env** – 환경 인스턴스입니다.

## 명령어

환경에서 사용할 수 있는 다양한 명령어 항목입니다.

**클래스:**

| [`NullCommandCfg`](#isaaclab.envs.mdp.commands.commands_cfg.NullCommandCfg)                             |_NULL 명령 생성기의 구성_                   |
|---------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`UniformVelocityCommandCfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg)       |_균일한 속도 명령 생성기의 구성_       |
| [`NormalVelocityCommandCfg`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg)         |_정규 분포 속도 명령 생성기의 구성_         |
| [`UniformPoseCommandCfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg)               |_균일 포즈 명령 생성기의 구성_               |
| [`UniformPose2dCommandCfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg)           |_균일한 2D 포즈 명령 생성기의 구성_           |
| [`TerrainBasedPose2dCommandCfg`](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg) | 지형 기반 위치 명령 생성기의 구성입니다. |

### *class* isaaclab.envs.mdp.commands.commands_cfg.NullCommandCfg

Bases: [`CommandTermCfg`](isaaclab.managers.md#isaaclab.managers.CommandTermCfg)

Null 명령 생성기의 구성입니다.

**Attributes:**

| [`resampling_time_range`](#isaaclab.envs.mdp.commands.commands_cfg.NullCommandCfg.resampling_time_range)   | 명령이 변경되기까지의 시간 [s].   |
|------------------------------------------------------------------------------------------------------------|-----------------------------------------|
| [`debug_vis`](#isaaclab.envs.mdp.commands.commands_cfg.NullCommandCfg.debug_vis)                           | 디버그 정보를 시각화할지 여부. |

#### resampling_time_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

명령이 변경되기까지의 시간 [s].

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부. 기본값은 False입니다.

### *class* isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg

Bases: [`CommandTermCfg`](isaaclab.managers.md#isaaclab.managers.CommandTermCfg)

균일 속도 명령 생성기의 구성입니다.

**Attributes:**

| [`asset_name`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.asset_name)                                 | 명령을 생성할 환경 내 자산의 이름.                                                                                           |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| [`heading_command`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.heading_command)                       | 헤딩 명령을 사용할지 각속도 명령을 사용할지 여부.                                                                                                          |
| [`heading_control_stiffness`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.heading_control_stiffness)   | 헤딩 오류를 각속도 명령으로 변환하는 스케일 팩터.                                                                                               |
| [`rel_standing_envs`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.rel_standing_envs)                   | 정지 상태를 유지해야 하는 환경의 샘플링된 확률.                                                                                               |
| [`rel_heading_envs`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.rel_heading_envs)                     | 헤딩 기반 각속도 명령을 따르는 환경의 샘플링된 확률 (다른 환경은 샘플링된 각속도 명령을 따름). |
| [`ranges`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.ranges)                                         | 속도 명령의 분포 범위.                                                                                                                       |
| [`goal_vel_visualizer_cfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.goal_vel_visualizer_cfg)       | 목표 속도 시각화 마커의 구성.                                                                                                        |
| [`current_vel_visualizer_cfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.current_vel_visualizer_cfg) | 현재 속도 시각화 마커의 구성.                                                                                                     |
| [`resampling_time_range`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.resampling_time_range)           | 명령이 변경되기까지의 시간 [s].                                                                                                                                |
| [`debug_vis`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.debug_vis)                                   | 디버그 정보를 시각화할지 여부.                                                                                                                              |

**Classes:**

| [`Ranges`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.Ranges)   | 속도 명령의 균일 분포 범위.   |
|-----------------------------------------------------------------------------------------|----------------------------------------------------------|

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

명령을 생성할 환경 내 자산의 이름.

#### heading_command *: [bool](https://docs.python.org/3/library/functions.html#bool)*

헤딩 명령을 사용할지 각속도 명령을 사용할지 여부. 기본값은 False입니다.

True인 경우, 제공된 범위에서 균일하게 샘플링된 목표 헤딩에서 헤딩 오류를 기반으로 각속도 명령이 계산됩니다. 그렇지 않은 경우, 각속도 명령은 제공된 범위에서 균일하게 샘플링됩니다.

#### heading_control_stiffness *: [float](https://docs.python.org/3/library/functions.html#float)*

헤딩 오류를 각속도 명령으로 변환하는 스케일 팩터. 기본값은 1.0입니다.

#### rel_standing_envs *: [float](https://docs.python.org/3/library/functions.html#float)*

정지 상태를 유지해야 하는 환경의 샘플링된 확률. 기본값은 0.0입니다.

#### rel_heading_envs *: [float](https://docs.python.org/3/library/functions.html#float)*

헤딩 기반 각속도 명령을 따르는 환경의 샘플링된 확률 (다른 환경은 샘플링된 각속도 명령을 따름). 기본값은 1.0입니다.

이 매개변수는 [`heading_command`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.heading_command)가 True인 경우에만 사용됩니다.

#### *class* Ranges

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

속도 명령의 균일 분포 범위.

**Attributes:**

| [`lin_vel_x`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.Ranges.lin_vel_x)   | 선형-x 속도 명령의 범위 (m/s 단위).    |
|------------------------------------------------------------------------------------------------------|------------------------------------------------------|
| [`lin_vel_y`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.Ranges.lin_vel_y)   | 선형-y 속도 명령의 범위 (m/s 단위).    |
| [`ang_vel_z`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.Ranges.ang_vel_z)   | 각도-z 속도 명령의 범위 (rad/s 단위). |
| [`heading`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.Ranges.heading)       | 헤딩 명령의 범위 (라디안 단위).              |

#### lin_vel_x *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

선형-x 속도 명령의 범위 (m/s 단위).

#### lin_vel_y *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

선형-y 속도 명령의 범위 (m/s 단위).

#### ang_vel_z *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

각도-z 속도 명령의 범위 (rad/s 단위).

#### heading *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)] | [None](https://docs.python.org/3/library/constants.html#None)*

헤딩 명령의 범위 (라디안 단위). 기본값은 None입니다.

이 매개변수는 [`heading_command`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.heading_command)가 True인 경우에만 사용됩니다.

#### ranges *: [Ranges](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg.Ranges)*

속도 명령의 분포 범위.

#### goal_vel_visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

목표 속도 시각화 마커의 구성. 기본값은 GREEN_ARROW_X_MARKER_CFG입니다.

#### current_vel_visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

현재 속도 시각화 마커의 구성. 기본값은 BLUE_ARROW_X_MARKER_CFG입니다.

#### resampling_time_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

명령이 변경되기까지의 시간 [s].

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부. 기본값은 False입니다.

### *class* isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg

Bases: [`UniformVelocityCommandCfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformVelocityCommandCfg)

정규 속도 명령 생성기의 구성입니다.

**Attributes:**

| [`heading_command`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.heading_command)                       | 헤딩 명령 사용 여부 또는 각속도 명령 사용 여부를 나타냅니다.                                                                                                          |
|------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`ranges`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.ranges)                                         | 속도 명령의 분포 범위입니다.                                                                                                                       |
| [`resampling_time_range`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.resampling_time_range)           | 명령이 변경되기 전 시간 [s].                                                                                                                                |
| [`debug_vis`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.debug_vis)                                   | 디버그 정보를 시각화할지 여부입니다.                                                                                                                              |
| [`asset_name`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.asset_name)                                 | 환경에서 명령이 생성되는 에셋의 이름입니다.                                                                                           |
| [`heading_control_stiffness`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.heading_control_stiffness)   | 헤딩 오류를 각속도 명령으로 변환하는 스케일 팩터입니다.                                                                                               |
| [`rel_standing_envs`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.rel_standing_envs)                   | 정지해야 하는 환경이 샘플링될 확률입니다.                                                                                               |
| [`rel_heading_envs`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.rel_heading_envs)                     | 헤딩 기반 각속도 명령을 따르는 환경이 샘플링될 확률 (나머지는 샘플링된 각속도 명령을 따름). |
| [`goal_vel_visualizer_cfg`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.goal_vel_visualizer_cfg)       | 목표 속도 시각화 마커의 구성입니다.                                                                                                        |
| [`current_vel_visualizer_cfg`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.current_vel_visualizer_cfg) | 현재 속도 시각화 마커의 구성입니다.                                                                                                     |

**Classes:**

| [`Ranges`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.Ranges)   | 속도 명령의 정규 분포 범위입니다.   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------|

#### heading_command *: [bool](https://docs.python.org/3/library/functions.html#bool)*

헤딩 명령을 사용할지 여부 또는 각속도 명령을 사용할지 여부입니다. 기본값은 False입니다.

True인 경우, 헤딩 오류에서 각속도 명령이 계산되며, 대상 헤딩은 제공된 범위에서 균일하게 샘플링됩니다. 그렇지 않은 경우, 각속도 명령은 제공된 범위에서 균일하게 샘플링됩니다.

#### *class* Ranges

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

속도 명령의 정규 분포 범위입니다.

**Attributes:**

| [`mean_vel`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.Ranges.mean_vel)   | 정규 분포에서의 평균 속도 (m/s 단위).       |
|---------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`std_vel`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.Ranges.std_vel)     | 정규 분포에서의 표준 편차 (m/s 단위).  |
| [`zero_prob`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.Ranges.zero_prob) | 정규 분포에서의 0 속도 확률. |

#### mean_vel *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

정규 분포에서의 평균 속도 (m/s 단위).

튜플에는 선형 x, 선형 y 및 각도 z 속도의 평균이 포함됩니다.

#### std_vel *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

정규 분포에서의 표준 편차 (m/s 단위).

튜플에는 선형 x, 선형 y 및 각도 z 속도의 표준 편차가 포함됩니다.

#### zero_prob *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

정규 분포에서의 0 속도 확률.

튜플에는 선형 x, 선형 y 및 각도 z 속도의 0 속도 확률이 포함됩니다.

#### ranges *: [Ranges](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.Ranges)*

속도 명령의 분포 범위입니다.

#### resampling_time_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

명령이 변경되기 전 시간 [s].

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부입니다. 기본값은 False입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

환경에서 명령이 생성되는 에셋의 이름입니다.

#### heading_control_stiffness *: [float](https://docs.python.org/3/library/functions.html#float)*

헤딩 오류를 각속도 명령으로 변환하는 스케일 팩터입니다. 기본값은 1.0입니다.

#### rel_standing_envs *: [float](https://docs.python.org/3/library/functions.html#float)*

정지해야 하는 환경이 샘플링될 확률입니다. 기본값은 0.0입니다.

#### rel_heading_envs *: [float](https://docs.python.org/3/library/functions.html#float)*

헤딩 기반 각속도 명령을 따르는 환경이 샘플링될 확률 (나머지는 샘플링된 각속도 명령을 따름). 기본값은 1.0입니다.

이 파라미터는 [`heading_command`](#isaaclab.envs.mdp.commands.commands_cfg.NormalVelocityCommandCfg.heading_command)가 True인 경우에만 사용됩니다.

#### goal_vel_visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

목표 속도 시각화 마커의 구성입니다. 기본값은 GREEN_ARROW_X_MARKER_CFG입니다.

#### current_vel_visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

현재 속도 시각화 마커의 구성입니다. 기본값은 BLUE_ARROW_X_MARKER_CFG입니다.

### *class* isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg

Bases: [`CommandTermCfg`](isaaclab.managers.md#isaaclab.managers.CommandTermCfg)

균일 포즈 명령 생성기의 구성입니다.

**Attributes:**

| [`asset_name`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.asset_name)                                   | 환경에서 명령이 생성되는 에셋의 이름입니다.   |
|-----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| [`body_name`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.body_name)                                     | 명령이 생성되는 에셋의 바디 이름입니다.          |
| [`make_quat_unique`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.make_quat_unique)                       | 쿼터니언을 고유하게 만들지 여부입니다.                                |
| [`resampling_time_range`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.resampling_time_range)             | 명령이 변경되기 전 시간 [s].                                        |
| [`debug_vis`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.debug_vis)                                     | 디버그 정보를 시각화할지 여부입니다.                                      |
| [`ranges`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.ranges)                                           | 명령의 범위입니다.                                                     |
| [`goal_pose_visualizer_cfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.goal_pose_visualizer_cfg)       | 목표 포즈 시각화 마커의 구성입니다.                    |
| [`current_pose_visualizer_cfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.current_pose_visualizer_cfg) | 현재 자세 시각화 마커에 대한 구성입니다.                |

**클래스:**

| [`Ranges`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.Ranges)   | 자세 명령에 대한 균일 분포 범위입니다.   |
|-------------------------------------------------------------------------------------|------------------------------------------|

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

명령이 생성되는 환경 내의 자산 이름입니다.

#### body_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

명령이 생성되는 자산 내의 바디 이름입니다.

#### make_quat_unique *: [bool](https://docs.python.org/3/library/functions.html#bool)*

쿼터니언을 고유하게 만들지 여부를 나타냅니다. 기본값은 False입니다.

True인 경우, 실수 부분이 양수가 되도록 쿼터니언을 조정하여 고유하게 만듭니다.

#### *class* Ranges

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

자세 명령에 대한 균일 분포 범위입니다.

**속성:**

| [`pos_x`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.Ranges.pos_x)   | x 위치 범위 (단위: m)입니다.    |
|------------------------------------------------------------------------------------------|---------------------------------|
| [`pos_y`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.Ranges.pos_y)   | y 위치 범위 (단위: m)입니다.    |
| [`pos_z`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.Ranges.pos_z)   | z 위치 범위 (단위: m)입니다.    |
| [`roll`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.Ranges.roll)     | 롤 각도 범위 (단위: rad)입니다.  |
| [`pitch`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.Ranges.pitch)   | 피치 각도 범위 (단위: rad)입니다. |
| [`yaw`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.Ranges.yaw)       | 요 각도 범위 (단위: rad)입니다.   |

#### pos_x *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

x 위치 범위 (단위: m)입니다.

#### pos_y *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

y 위치 범위 (단위: m)입니다.

#### pos_z *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

z 위치 범위 (단위: m)입니다.

#### roll *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

롤 각도 범위 (단위: rad)입니다.

#### pitch *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

피치 각도 범위 (단위: rad)입니다.

#### yaw *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

요 각도 범위 (단위: rad)입니다.

#### resampling_time_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

명령이 변경되기까지의 시간 [s].

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보 시각화 여부입니다. 기본값은 False입니다.

#### ranges *: [Ranges](#isaaclab.envs.mdp.commands.commands_cfg.UniformPoseCommandCfg.Ranges)*

명령의 범위입니다.

#### goal_pose_visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

목표 자세 시각화 마커에 대한 구성입니다. 기본값은 FRAME_MARKER_CFG입니다.

#### current_pose_visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

현재 자세 시각화 마커에 대한 구성입니다. 기본값은 FRAME_MARKER_CFG입니다.

### *class* isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg

Bases: [`CommandTermCfg`](isaaclab.managers.md#isaaclab.managers.CommandTermCfg)

통일된 2D 자세 명령 생성기의 구성입니다.

**속성:**

| [`resampling_time_range`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.resampling_time_range)       | 명령이 변경되기까지의 시간 [s].                                      |
|-------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [`debug_vis`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.debug_vis)                               | 디버그 정보 시각화 여부입니다.                                        |
| [`asset_name`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.asset_name)                             | 명령이 생성되는 환경 내의 자산 이름입니다.                            |
| [`simple_heading`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.simple_heading)                     | 간단한 헤딩을 사용할지 여부를 나타냅니다.                               |
| [`ranges`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.ranges)                                     | 위치 명령에 대한 분포 범위입니다.                                      |
| [`goal_pose_visualizer_cfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.goal_pose_visualizer_cfg) | 목표 자세 시각화 마커에 대한 구성입니다.                               |

**클래스:**

| [`Ranges`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.Ranges)   | 위치 명령에 대한 균일 분포 범위입니다.   |
|---------------------------------------------------------------------------------------|------------------------------------------|

#### resampling_time_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

명령이 변경되기까지의 시간 [s].

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보 시각화 여부입니다. 기본값은 False입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

명령이 생성되는 환경 내의 자산 이름입니다.

#### simple_heading *: [bool](https://docs.python.org/3/library/functions.html#bool)*

간단한 헤딩을 사용할지 여부를 나타냅니다.

True인 경우, 헤딩은 목표 위치의 방향으로 설정됩니다.

#### *class* Ranges

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

위치 명령에 대한 균일 분포 범위입니다.

**속성:**

| [`pos_x`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.Ranges.pos_x)     | x 위치 범위 (단위: m)입니다.                  |
|----------------------------------------------------------------------------------------------|------------------------------------------------|
| [`pos_y`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.Ranges.pos_y)     | y 위치 범위 (단위: m)입니다.                  |
| [`heading`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.Ranges.heading) | 위치 명령에 대한 헤딩 범위 (단위: rad)입니다. |

#### pos_x *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

x 위치 범위 (단위: m)입니다.

#### pos_y *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

y 위치 범위 (단위: m)입니다.

#### heading *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

위치 명령에 대한 헤딩 범위 (단위: rad)입니다.

`simple_heading`이 False인 경우에만 사용됩니다.

#### ranges *: [Ranges](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg.Ranges)*

위치 명령에 대한 분포 범위입니다.

#### goal_pose_visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

목표 자세 시각화 마커에 대한 구성입니다. 기본값은 GREEN_ARROW_X_MARKER_CFG입니다.

### *class* isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg

Bases: [`UniformPose2dCommandCfg`](#isaaclab.envs.mdp.commands.commands_cfg.UniformPose2dCommandCfg)

지형 기반 위치 명령 생성기의 구성입니다.

**속성:**

| [`resampling_time_range`](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg.resampling_time_range)       | 명령이 변경되기까지의 시간 [s].                                      |
|------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [`debug_vis`](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg.debug_vis)                               | 디버그 정보를 시각화할지 여부를 나타냅니다.                                    |
| [`asset_name`](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg.asset_name)                             | 명령이 생성되는 환경의 에셋 이름입니다. |
| [`simple_heading`](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg.simple_heading)                     | 간단한 헤딩을 사용할지 여부를 나타냅니다.                                      |
| [`goal_pose_visualizer_cfg`](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg.goal_pose_visualizer_cfg) | 목표 자세 시각화 마커의 구성입니다.                  |
| [`ranges`](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg.ranges)                                     | 샘플링된 명령의 분포 범위입니다.                              |

**Classes:**

| [`Ranges`](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg.Ranges)   | 위치 명령의 균일 분포 범위입니다.   |
|--------------------------------------------------------------------------------------------|----------------------------------------------------------|

#### resampling_time_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

명령이 변경되기 전의 시간 [s].

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 정보를 시각화할지 여부를 나타냅니다. 기본값은 False입니다.

#### asset_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

명령이 생성되는 환경의 에셋 이름입니다.

#### simple_heading *: [bool](https://docs.python.org/3/library/functions.html#bool)*

간단한 헤딩을 사용할지 여부를 나타냅니다.

True인 경우, 헤딩은 목표 위치의 방향으로 설정됩니다.

#### goal_pose_visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

목표 자세 시각화 마커의 구성입니다. 기본값은 GREEN_ARROW_X_MARKER_CFG입니다.

#### *class* Ranges

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

위치 명령의 균일 분포 범위입니다.

**Attributes:**

| [`heading`](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg.Ranges.heading)   | 위치 명령의 헤딩 범위 (라디안 단위).   |
|-----------------------------------------------------------------------------------------------------|-----------------------------------------------------|

#### heading *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

위치 명령의 헤딩 범위 (라디안 단위).

`simple_heading`이 False인 경우에만 사용됩니다.

#### ranges *: [Ranges](#isaaclab.envs.mdp.commands.commands_cfg.TerrainBasedPose2dCommandCfg.Ranges)*

샘플링된 명령의 분포 범위입니다.

## 보상

보상 함수를 활성화하는 데 사용할 수 있는 일반적인 함수입니다.

이러한 함수는 [`isaaclab.managers.RewardTermCfg`](isaaclab.managers.md#isaaclab.managers.RewardTermCfg) 객체에 전달하여 해당 함수에서 도입된 보상을 포함할 수 있습니다.

**Functions:**

| [`is_alive`](#isaaclab.envs.mdp.rewards.is_alive)(env)                                            | 생존에 대한 보상입니다.                                                             |
|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
| [`is_terminated`](#isaaclab.envs.mdp.rewards.is_terminated)(env)                                  | 에피소드 시간 초과와 대응되지 않는 종료된 에피소드에 대한 페널티입니다.            |
| [`lin_vel_z_l2`](#isaaclab.envs.mdp.rewards.lin_vel_z_l2)(env[, asset_cfg])                       | L2 제곱 커널을 사용하여 z축 기반 선형 속도에 페널티를 부과합니다.                       |
| [`ang_vel_xy_l2`](#isaaclab.envs.mdp.rewards.ang_vel_xy_l2)(env[, asset_cfg])                     | L2 제곱 커널을 사용하여 xy축 기반 각속도에 페널티를 부과합니다.                     |
| [`flat_orientation_l2`](#isaaclab.envs.mdp.rewards.flat_orientation_l2)(env[, asset_cfg])         | L2 제곱 커널을 사용하여 평평하지 않은 기반 방향에 페널티를 부과합니다.                         |
| [`base_height_l2`](#isaaclab.envs.mdp.rewards.base_height_l2)(env, target_height[, ...])          | L2 제곱 커널을 사용하여 대상에서의 에셋 높이에 페널티를 부과합니다.                      |
| [`body_lin_acc_l2`](#isaaclab.envs.mdp.rewards.body_lin_acc_l2)(env[, asset_cfg])                 | L2 커널을 사용하여 몸체의 선형 가속도에 페널티를 부과합니다.                         |
| [`joint_torques_l2`](#isaaclab.envs.mdp.rewards.joint_torques_l2)(env[, asset_cfg])               | L2 제곱 커널을 사용하여 관절에 적용된 토크에 페널티를 부과합니다.         |
| [`joint_vel_l1`](#isaaclab.envs.mdp.rewards.joint_vel_l1)(env, asset_cfg)                         | L1 커널을 사용하여 관절 속도에 페널티를 부과합니다.                   |
| [`joint_vel_l2`](#isaaclab.envs.mdp.rewards.joint_vel_l2)(env[, asset_cfg])                       | L2 제곱 커널을 사용하여 관절 속도에 페널티를 부과합니다.              |
| [`joint_acc_l2`](#isaaclab.envs.mdp.rewards.joint_acc_l2)(env[, asset_cfg])                       | L2 제곱 커널을 사용하여 관절 가속도에 페널티를 부과합니다.            |
| [`joint_deviation_l1`](#isaaclab.envs.mdp.rewards.joint_deviation_l1)(env[, asset_cfg])           | 기본값으로부터 벗어난 관절 위치에 페널티를 부과합니다.                         |
| [`joint_pos_limits`](#isaaclab.envs.mdp.rewards.joint_pos_limits)(env[, asset_cfg])               | 관절 위치가 소프트 한계를 초과하면 페널티를 부과합니다.                             |
| [`joint_vel_limits`](#isaaclab.envs.mdp.rewards.joint_vel_limits)(env, soft_ratio[, asset_cfg])   | 관절 속도가 소프트 한계를 초과하면 페널티를 부과합니다.                            |
| [`applied_torque_limits`](#isaaclab.envs.mdp.rewards.applied_torque_limits)(env[, asset_cfg])     | 적용된 토크가 한계를 초과하면 페널티를 부과합니다.                                  |
| [`action_rate_l2`](#isaaclab.envs.mdp.rewards.action_rate_l2)(env)                                | L2 제곱 커널을 사용하여 행동 변화율에 페널티를 부과합니다.                 |
| [`action_l2`](#isaaclab.envs.mdp.rewards.action_l2)(env)                                          | L2 제곱 커널을 사용하여 행동에 페널티를 부과합니다.                                       |
| [`undesired_contacts`](#isaaclab.envs.mdp.rewards.undesired_contacts)(env, threshold, sensor_cfg) | 임계값 이상의 위반 횟수로 원하지 않는 접촉에 페널티를 부과합니다. |
| [`desired_contacts`](#isaaclab.envs.mdp.rewards.desired_contacts)(env, sensor_cfg[, threshold])   | 원하는 접촉이 하나도 없을 경우 페널티를 부과합니다.                               |
| [`contact_forces`](#isaaclab.envs.mdp.rewards.contact_forces)(env, threshold, sensor_cfg)         | 순 접촉 힘의 위반량으로 접촉 힘에 페널티를 부과합니다.       |
| [`track_lin_vel_xy_exp`](#isaaclab.envs.mdp.rewards.track_lin_vel_xy_exp)(env, std, command_name) | 지수 커널을 사용하여 선형 속도 명령(xy 축)의 추적에 대한 보상입니다.     |
| [`track_ang_vel_z_exp`](#isaaclab.envs.mdp.rewards.track_ang_vel_z_exp)(env, std, command_name)   | 지수 커널을 사용하여 각속도 명령(요)의 추적에 대한 보상입니다.        |

**Classes:**

| [`is_terminated_term`](#isaaclab.envs.mdp.rewards.is_terminated_term)   | 에피소드 시간 초과와 대응되지 않는 특정 종료 항목에 대한 페널티입니다.   |
|-------------------------------------------------------------------------|---------------------------------------------------------------------------------------|

### isaaclab.envs.mdp.rewards.is_alive(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

생존에 대한 보상입니다.

### isaaclab.envs.mdp.rewards.is_terminated(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

에피소드 시간 초과와 대응되지 않는 종료된 에피소드에 대한 페널티입니다.

### *class* isaaclab.envs.mdp.rewards.is_terminated_term

에피소드 시간 초과와 대응되지 않는 특정 종료 항목에 대한 페널티입니다.

매개변수는 다음과 같습니다:

* attr:term_keys: 페널티를 부과할 종료 항목입니다. 문자열, 문자열 목록 또는 정규 표현식일 수 있습니다. 기본값은 “.\*”로, 모든 종료 항목에 페널티를 부과합니다.

보상은 에피소드 시간 초과와 대응되지 않는 종료 항목의 합으로 계산됩니다. 이는 에피소드 시간 초과로 인해 에피소드가 종료된 경우 보상이 0임을 의미합니다. 그렇지 않은 경우, 두 개의 종료 항목이 활성화되어 있다면 보상은 2가 됩니다.

**Methods:**

| [`__init__`](#isaaclab.envs.mdp.rewards.is_terminated_term.__init__)(cfg, env)   | 관리자 항목을 초기화합니다.   |
|----------------------------------------------------------------------------------|--------------------------------|

#### \_\_init_\_(cfg: [RewardTermCfg](isaaclab.managers.md#isaaclab.managers.RewardTermCfg), env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv))

관리자 항목을 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체입니다.
  * **env** – 환경 인스턴스입니다.

### isaaclab.envs.mdp.rewards.lin_vel_z_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 제곱 커널을 사용하여 z축 기준 기본 선속도를 페널티 처리합니다.

### isaaclab.envs.mdp.rewards.ang_vel_xy_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 제곱 커널을 사용하여 xy축 기준 기본 각속도를 페널티 처리합니다.

### isaaclab.envs.mdp.rewards.flat_orientation_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 제곱 커널을 사용하여 평면이 아닌 기본 방향을 페널티 처리합니다.

이 값은 투사된 중력 벡터의 xy성분을 페널티 처리하여 계산됩니다.

### isaaclab.envs.mdp.rewards.base_height_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), target_height: [float](https://docs.python.org/3/library/functions.html#float), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False), sensor_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 제곱 커널을 사용하여 자산의 높이가 목표 높이에서 벗어난 정도를 페널티 처리합니다.

#### 참고
평탄한 지형의 경우, 대상 높이는 월드 프레임 기준으로 측정됩니다. 거친 지형의 경우,
센서 읽기를 통해 지형 높이를 고려하여 대상 높이를 조정할 수 있습니다.

### isaaclab.envs.mdp.rewards.body_lin_acc_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 커널을 사용하여 신체의 선형 가속도를 페널티 처리합니다.

### isaaclab.envs.mdp.rewards.joint_torques_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 제곱 커널을 사용하여 관절에 적용된 토크를 페널티 처리합니다.

#### 참고
`asset_cfg.joint_ids`로 구성된 관절만 토크 값이 이 항에 기여합니다.

### isaaclab.envs.mdp.rewards.joint_vel_l1(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L1 커널을 사용하여 관절 속도를 페널티 처리합니다.

### isaaclab.envs.mdp.rewards.joint_vel_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 제곱 커널을 사용하여 관절 속도를 페널티 처리합니다.

#### 참고
`asset_cfg.joint_ids`로 구성된 관절만 속도 값이 이 항에 기여합니다.

### isaaclab.envs.mdp.rewards.joint_acc_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 제곱 커널을 사용하여 관절 가속도를 페널티 처리합니다.

#### 참고
`asset_cfg.joint_ids`로 구성된 관절만 가속도 값이 이 항에 기여합니다.

### isaaclab.envs.mdp.rewards.joint_deviation_l1(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

기본 관절 위치에서 벗어난 관절 위치를 페널티 처리합니다.

### isaaclab.envs.mdp.rewards.joint_pos_limits(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

관절 위치가 소프트 리미트를 벗어날 경우 페널티를 부과합니다.

이는 관절 위치와 소프트 리미트 간의 차이의 절댓값을 합산하여 계산됩니다.

### isaaclab.envs.mdp.rewards.joint_vel_limits(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), soft_ratio: [float](https://docs.python.org/3/library/functions.html#float), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

관절 속도가 소프트 리미트를 벗어날 경우 페널티를 부과합니다.

이는 관절 속도와 소프트 리미트 간의 차이의 절댓값을 합산하여 계산됩니다.

* **매개변수:**
  **soft_ratio** – 사용할 소프트 리미트의 비율입니다.

### isaaclab.envs.mdp.rewards.applied_torque_limits(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

적용된 토크가 제한을 벗어날 경우 페널티를 부과합니다.

이는 적용된 토크와 제한 간의 차이의 절댓값을 합산하여 계산됩니다.

### isaaclab.envs.mdp.rewards.action_rate_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 제곱 커널을 사용하여 행동 변화율을 페널티 처리합니다.

### isaaclab.envs.mdp.rewards.action_l2(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

L2 커널 제곱을 사용하여 행동을 페널티화합니다.

### isaaclab.envs.mdp.rewards.undesired_contacts(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), threshold: [float](https://docs.python.org/3/library/functions.html#float), sensor_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

임계값 이상의 위반 횟수만큼 원치 않는 접촉을 페널티화합니다.

### isaaclab.envs.mdp.rewards.desired_contacts(env, sensor_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg), threshold: [float](https://docs.python.org/3/library/functions.html#float) = 1.0) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

원하는 접촉이 하나도 없는 경우 페널티를 부과합니다.

### isaaclab.envs.mdp.rewards.contact_forces(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), threshold: [float](https://docs.python.org/3/library/functions.html#float), sensor_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

순접촉 힘 위반의 양만큼 접촉 힘을 페널티화합니다.

### isaaclab.envs.mdp.rewards.track_lin_vel_xy_exp(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), std: [float](https://docs.python.org/3/library/functions.html#float), command_name: [str](https://docs.python.org/3/library/stdtypes.html#str), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

지수 커널을 사용하여 선형 속도 명령(xy 축)의 추적을 보상합니다.

### isaaclab.envs.mdp.rewards.track_ang_vel_z_exp(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), std: [float](https://docs.python.org/3/library/functions.html#float), command_name: [str](https://docs.python.org/3/library/stdtypes.html#str), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

지수 커널을 사용하여 각도 속도 명령(요)의 추적을 보상합니다.

## 종료

특정 종료를 활성화하는 데 사용할 수 있는 일반적인 함수입니다.

이러한 함수는 [`isaaclab.managers.TerminationTermCfg`](isaaclab.managers.md#isaaclab.managers.TerminationTermCfg) 객체에 전달하여 함수에 의해 도입된 종료를 활성화할 수 있습니다.

**함수:**

| [`time_out`](#isaaclab.envs.mdp.terminations.time_out)(env)                                                          | 에피소드 길이가 최대 에피소드 길이를 초과할 때 에피소드를 종료합니다.         |
|----------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| [`command_resample`](#isaaclab.envs.mdp.terminations.command_resample)(env, command_name[, ...])                     | 명령이 재샘플링된 총 횟수를 기반으로 에피소드를 종료합니다.   |
| [`bad_orientation`](#isaaclab.envs.mdp.terminations.bad_orientation)(env, limit_angle[, asset_cfg])                  | 자산의 방향이 원하는 방향 제한에서 너무 멀어지면 종료합니다.    |
| [`root_height_below_minimum`](#isaaclab.envs.mdp.terminations.root_height_below_minimum)(env, minimum_height)        | 자산의 루트 높이가 최소 높이를 아래로 내려갈 때 종료합니다.                       |
| [`joint_pos_out_of_limit`](#isaaclab.envs.mdp.terminations.joint_pos_out_of_limit)(env[, asset_cfg])                 | 자산의 관절 위치가 소프트 관절 제한 외부에 있을 때 종료합니다.          |
| [`joint_pos_out_of_manual_limit`](#isaaclab.envs.mdp.terminations.joint_pos_out_of_manual_limit)(env, bounds[, ...]) | 자산의 관절 위치가 구성된 경계 외부에 있을 때 종료합니다.          |
| [`joint_vel_out_of_limit`](#isaaclab.envs.mdp.terminations.joint_vel_out_of_limit)(env[, asset_cfg])                 | 자산의 관절 속도가 소프트 관절 제한 외부에 있을 때 종료합니다.         |
| [`joint_vel_out_of_manual_limit`](#isaaclab.envs.mdp.terminations.joint_vel_out_of_manual_limit)(env, max_velocity)  | 자산의 관절 속도가 제공된 제한 외부에 있을 때 종료합니다.              |
| [`joint_effort_out_of_limit`](#isaaclab.envs.mdp.terminations.joint_effort_out_of_limit)(env[, asset_cfg])           | 자산의 관절에 가해진 힘이 소프트 관절 제한 외부에 있을 때 종료합니다. |
| [`illegal_contact`](#isaaclab.envs.mdp.terminations.illegal_contact)(env, threshold, sensor_cfg)                     | 센서의 접촉 힘이 힘 임계값을 초과할 때 종료합니다.               |

### isaaclab.envs.mdp.terminations.time_out(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

에피소드 길이가 최대 에피소드 길이를 초과할 때 에피소드를 종료합니다.

### isaaclab.envs.mdp.terminations.command_resample(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), command_name: [str](https://docs.python.org/3/library/stdtypes.html#str), num_resamples: [int](https://docs.python.org/3/library/functions.html#int) = 1) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

명령이 재샘플링된 총 횟수를 기반으로 에피소드를 종료합니다.

이것은 명령이 어떻게 샘플링되는지에 따라 최대 에피소드 길이가 유동적이게 만듭니다. 이는 지연된 보상이 사용되는 상황[[RHBH22](../../refs/bibliography.md#id10)]에서 유용합니다.

### isaaclab.envs.mdp.terminations.bad_orientation(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), limit_angle: [float](https://docs.python.org/3/library/functions.html#float), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 방향이 원하는 방향 제한에서 너무 멀어지면 종료합니다.

이는 중력 벡터의 투영과 z-축 사이의 각도를 확인하여 계산됩니다.

### isaaclab.envs.mdp.terminations.root_height_below_minimum(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), minimum_height: [float](https://docs.python.org/3/library/functions.html#float), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 루트 높이가 최소 높이를 아래로 내려갈 때 종료합니다.

#### 참고
현재 이 함수는 평탄한 지형에 대해서만 지원되며, 즉 최소 높이는 세계 프레임에 있습니다.

### isaaclab.envs.mdp.terminations.joint_pos_out_of_limit(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 관절 위치가 소프트 관절 제한 외부에 있을 때 종료합니다.

### isaaclab.envs.mdp.terminations.joint_pos_out_of_manual_limit(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), bounds: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

자산의 관절 위치가 구성된 경계 외부에 있을 때 종료합니다.

#### 참고
이 함수는 [`joint_pos_out_of_limit()`](#isaaclab.envs.mdp.terminations.joint_pos_out_of_limit)와 유사하지만 사용자가 경계를 수동으로 지정할 수 있도록 허용합니다.

### isaaclab.envs.mdp.terminations.joint_vel_out_of_limit(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

에셋의 관절 속도가 소프트 관절 한계를 벗어날 때 종료합니다.

### isaaclab.envs.mdp.terminations.joint_vel_out_of_manual_limit(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), max_velocity: [float](https://docs.python.org/3/library/functions.html#float), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

에셋의 관절 속도가 제공된 한계를 벗어날 때 종료합니다.

### isaaclab.envs.mdp.terminations.joint_effort_out_of_limit(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), asset_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg) = SceneEntityCfg(name='robot', joint_names=None, joint_ids=slice(None, None, None), fixed_tendon_names=None, fixed_tendon_ids=slice(None, None, None), body_names=None, body_ids=slice(None, None, None), object_collection_names=None, object_collection_ids=slice(None, None, None), preserve_order=False)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

에셋의 관절에 적용된 힘이 소프트 관절 한계를 벗어날 때 종료합니다.

액추에이터에서 적용된 토크는 관절에 가해지는 힘입니다. 이는 관절 한계에 따라 계산된 토크를 클리핑하여 구합니다. 따라서 계산된 토크가 적용된 토크와 동일한지 확인합니다. 동일하지 않으면 클리핑이 발생했음을 의미합니다.

### isaaclab.envs.mdp.terminations.illegal_contact(env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), threshold: [float](https://docs.python.org/3/library/functions.html#float), sensor_cfg: [SceneEntityCfg](isaaclab.managers.md#isaaclab.managers.SceneEntityCfg)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

센서에 가해지는 접촉 힘이 힘 임계값을 초과할 때 종료합니다.

## 교육 과정

학습 환경의 교육 과정을 만들기 위해 사용할 수 있는 일반적인 함수입니다.

이러한 함수는 [`isaaclab.managers.CurriculumTermCfg`](isaaclab.managers.md#isaaclab.managers.CurriculumTermCfg) 객체에 전달하여 해당 함수로 도입된 교육 과정을 활성화할 수 있습니다.

**클래스:**

| [`modify_reward_weight`](#isaaclab.envs.mdp.curriculums.modify_reward_weight)   | 단계별 일정에 따라 보상 가중치를 수정하는 교육 과정입니다.   |
|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`modify_env_param`](#isaaclab.envs.mdp.curriculums.modify_env_param)           | 런타임에 환경 매개변수를 수정하는 교육 과정 항목입니다.          |
| [`modify_term_cfg`](#isaaclab.envs.mdp.curriculums.modify_term_cfg)             | 런타임에 관리자 항목 구성을 수정하는 교육 과정입니다.           |

### *class* isaaclab.envs.mdp.curriculums.modify_reward_weight

단계별 일정에 따라 보상 가중치를 수정하는 교육 과정입니다.

**메서드:**

| [`__init__`](#isaaclab.envs.mdp.curriculums.modify_reward_weight.__init__)(cfg, env)   | 관리자 항목을 초기화합니다.   |
|----------------------------------------------------------------------------------------|--------------------------------|

#### \_\_init_\_(cfg: [CurriculumTermCfg](isaaclab.managers.md#isaaclab.managers.CurriculumTermCfg), env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv))

관리자 항목을 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체.
  * **env** – 환경 인스턴스.

### *class* isaaclab.envs.mdp.curriculums.modify_env_param

런타임에 환경 매개변수를 수정하는 교육 과정 항목입니다.

이 항목은 런타임에 환경 매개변수(또는 속성)를 수정하는 데 도움이 됩니다. 이 매개변수는 물리 재질 속성, 관측 범위, 또는 점 경로를 통해 접근할 수 있는 기타 구성 가능한 매개변수와 같은 환경의 어떤 속성일 수 있습니다.

이 항목은 `address` 매개변수를 사용하여 점 경로 문자열로 대상 속성을 지정합니다. 예를 들어, “event_manager.cfg.object_physics_material.func.material_buckets”는 이벤트 관리자의 이벤트 항목 “object_physics_material” 내의 `material_buckets` 속성을 참조하며, 이는 샘플링된 물리 재질 속성의 텐서입니다.

이 항목은 `modify_fn` 매개변수를 사용하여 대상 속성의 값을 수정하는 함수를 지정합니다. 이 함수는 다음과 같은 시그니처를 가져야 합니다:

```python
def modify_fn(env, env_ids, old_value, **modify_params) -> new_value | modify_env_param.NO_CHANGE:
    # modify the value based on the old value and the modify parameters
    new_value = old_value + modify_params["value"]
    return new_value
```

여기서 `env`는 학습 환경, `env_ids`는 하위 환경 인덱스, `old_value`는 대상 속성의 현재 값, 그리고 `modify_params`는 함수에 전달할 수 있는 추가 매개변수입니다. 함수는 대상 속성에 설정할 새 값을 반환하거나, 값을 변경하지 않아야 함을 나타내는 특수 토큰 `modify_env_param.NO_CHANGE`를 반환해야 합니다.

초기화 이후 첫 번째 항목 호출 시, `address` 매개변수로 지정된 대상 속성에 대한 게터와 세터 함수가 컴파일됩니다. 게터는 현재 값을 가져오고, 세터는 새 값을 해당 속성에 다시 씁니다.

이 항목은 (`address`인 term 구성 `cfg.params["address"]`으로 지정된) 대상 속성의 게터/세터 접근자를 첫 번째 호출 시 처리하고, 이후 각 호출 시 현재 값을 읽고, 사용자 제공 `modify_fn`를 적용한 뒤 결과를 다시 씁니다. [`None`](https://docs.python.org/3/library/constants.html#None)이 이 경우 때때로 원하는 값이 될 수 있으므로, 값을 변경하지 않음을 나타내는 토큰으로 [`NO_CHANGE`](#isaaclab.envs.mdp.curriculums.modify_env_param.NO_CHANGE)를 사용합니다. 아래 사용법을 참조하세요.

사용법:
: ```python
  def resample_bucket_range(
      env, env_id, data, static_friction_range, dynamic_friction_range, restitution_range, num_steps
  ):
      if env.common_step_counter > num_steps:
          range_list = [static_friction_range, dynamic_friction_range, restitution_range]
          ranges = torch.tensor(range_list, device="cpu")
          new_buckets = math_utils.sample_uniform(ranges[:, 0], ranges[:, 1], (len(data), 3), device="cpu")
          return new_buckets
  <br/>
      # if the step counter is not reached, return NO_CHANGE to indicate no modification.
      # we do this instead of returning None, since None is a valid value to set.
      # additionally, returning the input data would not change the value but still lead
      # to the setter being called, which may add overhead.
      return mdp.modify_env_param.NO_CHANGE
  <br/>
  <br/>
  object_physics_material_curriculum = CurrTerm(
      func=mdp.modify_env_param,
      params={
          "address": "event_manager.cfg.object_physics_material.func.material_buckets",
          "modify_fn": resample_bucket_range,
          "modify_params": {
              "static_friction_range": [0.5, 1.0],
              "dynamic_friction_range": [0.3, 1.0],
              "restitution_range": [0.0, 0.5],
              "num_step": 120000,
          },
      },
  )
  ```

**속성:**

| [`NO_CHANGE`](#isaaclab.envs.mdp.curriculums.modify_env_param.NO_CHANGE)   | 값 설정 변경이 없음을 나타내는 특수 토큰입니다.   |
|----------------------------------------------------------------------------|---------------------------------------------------------------|

**메서드:**

| [`__init__`](#isaaclab.envs.mdp.curriculums.modify_env_param.__init__)(cfg, env)   | 관리자 항목을 초기화합니다.   |
|------------------------------------------------------------------------------------|--------------------------------|

#### NO_CHANGE *: [ClassVar](https://docs.python.org/3/library/typing.html#typing.ClassVar)* *= <object object>*

값 설정 변경이 없음을 나타내는 특수 토큰입니다.

이 토큰은 `modify_fn`이 새 값을 생성하지 않았음을 신호로 사용합니다. `modify_fn`에서 이 토큰을 반환하여 현재 값이 변경되지 않아야 함을 나타낼 수 있습니다.

#### \_\_init_\_(cfg: [CurriculumTermCfg](isaaclab.managers.md#isaaclab.managers.CurriculumTermCfg), env: [ManagerBasedRLEnv](isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv))

관리자 항목을 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체.
  * **env** – 환경 인스턴스.

### *class* isaaclab.envs.mdp.curriculums.modify_term_cfg

런타임에 관리자 항목 구성을 수정하는 교육 과정입니다.

이 클래스는 [`modify_env_param`](#isaaclab.envs.mdp.curriculums.modify_env_param)를 상속하며, 환경의 관리자 항목 구성을 수정하도록 특별히 설계되었습니다. 주로 관리자 구성을 가리키는 간소화된 주소 스타일인 “s.” 접두사를 사용하는 편의성을 제공합니다.

예를 들어, “event_manager.cfg.object_physics_material.func.material_buckets” 대신 “events.object_physics_material.func.material_buckets”을 작성하여 동일한 항목 구성을 참조할 수 있습니다. 동일한 방식이 “observations”, “commands”, “rewards”, “terminations”과 같은 다른 관리자에도 적용됩니다.

내부적으로 주소에서 “s.”의 첫 번째 발생을 “_manager.cfg.”로 대체하여 간소화된 주소를 전체 관리자 경로로 변환합니다.

사용법:
: ```python
  def override_value(env, env_ids, data, value, num_steps):
      if env.common_step_counter > num_steps:
          return value
      return mdp.modify_term_cfg.NO_CHANGE
  <br/>
  <br/>
  command_object_pose_xrange_adr = CurrTerm(
      func=mdp.modify_term_cfg,
      params={
          "address": "commands.object_pose.ranges.pos_x",  # note: `_manager.cfg` is omitted
          "modify_fn": override_value,
          "modify_params": {"value": (-0.75, -0.25), "num_steps": 12000},
      },
  )
  ```

**메서드:**

| [`__init__`](#isaaclab.envs.mdp.curriculums.modify_term_cfg.__init__)(cfg, env)   | 관리자 항목을 초기화합니다.   |
|-----------------------------------------------------------------------------------|--------------------------------|

#### \_\_init_\_(cfg, env)

관리자 항목을 초기화합니다.

* **매개변수:**
  * **cfg** – 구성 객체.
  * **env** – 환경 인스턴스.
