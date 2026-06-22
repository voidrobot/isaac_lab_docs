# IO Descriptors 101

이 튜토리얼에서는 IO 디스크립터가 무엇인지, 어떻게 내보내는지, 그리고 어떻게 환경에 추가할 수 있는지 알아볼 것입니다. Anymal-D 로봇을 예시로 들어 환경에서 IO 디스크립터를 내보내는 방법과, 맞춤형 행동 및 관측 용어에 IO 디스크립터를 연결하는 방법을 보여드리겠습니다.

## IO 디스크립터란?

IO 디스크립터에 대해 자세히 알아보기 전에 먼저 그것들이 무엇이고 어떻게 유용할 수 있는지를 이해해 봅시다.

IO 디스크립터는 Isaac Lab의 ManagerBasedRLEnv에서 학습된 정책의 입력과 출력을 설명하는 방법입니다. 즉, 정책의 행동과 관측 용어를 설명합니다. 이 설명은 외부 도구에서 행동과 관측 용어의 구성을 수동으로 입력하지 않고도 정책을 실행할 수 있도록 YAML 파일을 생성하는 데 사용됩니다.

또한 IO 디스크립터는 다음과 같은 정보를 제공합니다:
- 어큐에이터의 모든 관절의 매개변수.
- 시뮬레이션 타임스텝과 정책 타임스텝과 같은 일부 시뮬레이션 매개변수.
- 일부 행동 및 관측 용어에 대해, 행동/관측 용어에 나타나는 순서와 동일한 순서로 관절 이름 또는 몸체 이름을 제공합니다.
- 관측 및 행동 용어 모두에 대해, 관리자에서 나타나는 정확한 순서로 용어를 제공하여 YAML 파일에서 이를 쉽게 재구성할 수 있습니다.

다음은 Anymal-D 로봇에 대한 IO 디스크립터에서 생성된 YAML의 동작 부분의 예시입니다:

```yaml
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

actions:
- action_type: JointAction
  clip: null
  dtype: torch.float32
  extras:
    description: Joint action term that applies the processed actions to the articulation's
      joints as position commands.
  full_path: isaaclab.envs.mdp.actions.joint_actions.JointPositionAction
  joint_names:
  - LF_HAA
  - LH_HAA
  - RF_HAA
  - RH_HAA
  - LF_HFE
  - LH_HFE
  - RF_HFE
  - RH_HFE
  - LF_KFE
  - LH_KFE
  - RF_KFE
  - RH_KFE
  mdp_type: Action
  name: joint_position_action
  offset:
  - 0.0
  - 0.0
  - 0.0
  - 0.0
  - 0.4000000059604645
  - -0.4000000059604645
  - 0.4000000059604645
  - -0.4000000059604645
  - -0.800000011920929
  - 0.800000011920929
```

다음은 Anymal-D 로봇에 대한 IO 디스크립터에서 생성된 YAML의 관측 부분 일부의 예시입니다:

```yaml
    - RH_HFE
    - LF_KFE
    - LH_KFE
    - RF_KFE
    - RH_KFE
observations:
  policy:
  - dtype: torch.float32
    extras:
      axes:
      - X
      - Y
      - Z
      description: Root linear velocity in the asset's root frame.
      modifiers: null
      units: m/s
    full_path: isaaclab.envs.mdp.observations.base_lin_vel
    mdp_type: Observation
    name: base_lin_vel
    observation_type: RootState
    overloads:
      clip: null
      flatten_history_dim: true
      history_length: 0
      scale: null
    shape:
    - 3
  - dtype: torch.float32
    extras:
      axes:
      - X
      - Y
      - Z
      description: Root angular velocity in the asset's root frame.
      modifiers: null
      units: rad/s
    full_path: isaaclab.envs.mdp.observations.base_ang_vel
    mdp_type: Observation
    name: base_ang_vel
    observation_type: RootState
    overloads:
      clip: null
```

```yaml
      flatten_history_dim: true
      history_length: 0
      scale: null
    shape:
    - 3
  - dtype: torch.float32
    extras:
      description: 'The joint positions of the asset w.r.t. the default joint positions.
        Note: Only the joints configured in :attr:`asset_cfg.joint_ids` will have
        their positions returned.'
      modifiers: null
      units: rad
    full_path: isaaclab.envs.mdp.observations.joint_pos_rel
    joint_names:
    - LF_HAA
    - LH_HAA
    - RF_HAA
    - RH_HAA
    - LF_HFE
    - LH_HFE
    - RF_HFE
    - RH_HFE
    - LF_KFE
    - LH_KFE
    - RF_KFE
    - RH_KFE
    joint_pos_offsets:
    - 0.0
    - 0.0
    - 0.0
    - 0.0
    - 0.4000000059604645
    - -0.4000000059604645
    - 0.4000000059604645
    - -0.4000000059604645
    - -0.800000011920929
    - 0.800000011920929
    - -0.800000011920929
    - 0.800000011920929
    mdp_type: Observation
    name: joint_pos_rel
    observation_type: JointState
    overloads:
      clip: null
```

여기서 주의할 점은 행동 및 관측 용어가 모두 딕셔너리의 딕셔너리가 아니라 딕셔너리의 리스트로 반환된다는 것입니다. 이렇게 함으로써 용어의 순서가 보존됩니다. 따라서 동작 또는 관측 용어를 검색하려면 사용자는 딕셔너리에서 `name` 키를 찾아야 합니다.

예를 들어, 다음 스니펫에서는 `projected_gravity` 관측 용어를 살펴보고 있습니다. 여기서 `name` 키는 용어를 식별하는 데 사용됩니다. `full_path` 키는 이 용어를 계산하는 Isaac Lab 소스 코드 내 함수의 명시적 경로를 제공합니다. `mdp_type` 및 `observation_type`과 같은 일부 플래그도 제공되며, 이는 기능적으로 영향을 미치지 않습니다. 이들은 사용자에게 이 용어가 어떤 범주에 속하는지를 알려주기 위해 존재합니다.

```yaml
      flatten_history_dim: true
      history_length: 0
      scale: null
    shape:
    - 3
  - dtype: torch.float32
    extras:
      axes:
      - X
      - Y
      - Z
      description: Gravity projection on the asset's root frame.
      modifiers: null
      units: m/s^2
    full_path: isaaclab.envs.mdp.observations.projected_gravity
    mdp_type: Observation
    name: projected_gravity
    observation_type: RootState
    overloads:
      clip: null
```

## 환경에서 IO 디스크립터 내보내기

이 섹션에서는 환경에서 IO 디스크립터를 내보내는 방법을 다룹니다. 이 기능은 관리자 기반 RL 환경에서만 사용 가능하다는 점을 기억하세요.

이미 주어진 구성으로 정책을 학습시켰다면, 다음과 같이 IO 디스크립터를 내보낼 수 있습니다:

```bash
./isaaclab.sh -p scripts/environments/export_io_descriptors.py --task <task_name> --output_dir <output_dir>
```

예를 들어, Anymal-D 로봇의 IO 디스크립터를 내보내려면 다음과 같이 실행할 수 있습니다:

```bash
./isaaclab.sh -p scripts/environments/export_io_descriptors.py --task Isaac-Velocity-Flat-Anymal-D-v0 --output_dir ./io_descriptors
```

정책을 학습시키는 동안 IO 디스크립터를 학습 시작 시 내보내도록 요청할 수도 있습니다. 이를 위해서는 명령줄에서 `export_io_descriptors` 플래그를 설정하면 됩니다:

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Velocity-Flat-Anymal-D-v0 --export_io_descriptors
./isaaclab.sh -p scripts/reinforcement_learning/sb3/train.py --task Isaac-Velocity-Flat-Anymal-D-v0 --export_io_descriptors
./isaaclab.sh -p scripts/reinforcement_learning/rl_games/train.py --task Isaac-Velocity-Flat-Anymal-D-v0 --export_io_descriptors
./isaaclab.sh -p scripts/reinforcement_learning/skrl/train.py --task Isaac-Velocity-Flat-Anymal-D-v0 --export_io_descriptors
```

## 맞춤형 관측 용어에 IO 디스크립터 연결하기

이 섹션에서는 맞춤형 관측 용어에 IO 디스크립터를 연결하는 방법을 다룹니다.

간단한 관측 용어에 IO 디스크립터를 연결하는 방법을 살펴봅시다:

```python
@generic_io_descriptor(
   units="m/s", axes=["X", "Y", "Z"], observation_type="RootState", on_inspect=[record_shape, record_dtype]
)
def base_lin_vel(env: ManagerBasedEnv, asset_cfg: SceneEntityCfg = SceneEntityCfg("robot")) -> torch.Tensor:
    """Root linear velocity in the asset's root frame."""
    # extract the used quantities (to enable type-hinting)
    asset: RigidObject = env.scene[asset_cfg.name]
    return asset.data.root_lin_vel_b
```

여기서는 로봇의 루트 선형 속도를 계산하는 맞춤형 관측 용어인 `base_lin_vel`을 정의하고 있습니다. 또한 이 용어에 IO 디스크립터를 연결하고 있습니다. 이 IO 디스크립터는 `@generic_io_descriptor` 데코레이터를 사용하여 정의됩니다.

`@generic_io_descriptor` 데코레이터는 맞춤형 관측 용어에 IO 디스크립터를 연결하는 특수 데코레이터입니다. 이 데코레이터는 관측 용어를 설명하는 데 사용되는 임의의 인수를 받습니다. 여기서는 최종 사용자에게 유용할 수 있는 추가 정보를 제공합니다:

- `units`: 관측 용어의 단위입니다.
- `axes`: 관측 용어의 축입니다.
- `observation_type`: 관측 용어의 유형입니다.

또한 `on_inspect` 인수가 제공되는 것을 볼 수 있습니다. 이는 관측 용어를 검사하는 함수들의 리스트입니다. 여기서는 관측 용어의 출력의 형태와 데이터 타입을 기록하기 위해 `record_shape` 및 `record_dtype` 함수를 사용하고 있습니다.

이 함수들은 다음과 같이 정의됩니다:

```python
def record_shape(output: torch.Tensor, descriptor: GenericObservationIODescriptor, **kwargs) -> None:
    """Record the shape of the output tensor.

    Args:
       output: The output tensor.
       descriptor: The descriptor to record the shape to.
       **kwargs: Additional keyword arguments.
    """
    descriptor.shape = (output.shape[-1],)


def record_dtype(output: torch.Tensor, descriptor: GenericObservationIODescriptor, **kwargs) -> None:
    """Record the dtype of the output tensor.

    Args:
       output: The output tensor.
       descriptor: The descriptor to record the dtype to.
       **kwargs: Additional keyword arguments.
    """
    descriptor.dtype = str(output.dtype)
```

이 함수들은 항상 관측 용어의 출력 텐서를 첫 번째 인수로, 디스크립터를 두 번째 인수로 받습니다. `kwargs`에서는 관측 용어의 모든 입력이 제공됩니다. 또한 `on_inspect` 함수 외에도, 데코레이터는 백그라운드에서 일부 함수를 호출하여 `name`, `description`, 그리고 `full_path`를 수집합니다. (이 부분은 원문의 끝과 일치하도록 번역했습니다)
관측 용어. 이 데코레이터를 추가해도 관측 용어의 서명은 변경되지 않으므로 관측 관리자와 안전하게 함께 사용할 수 있다는 점에 유의하세요!

이제 더 복잡한 예시를 살펴보겠습니다: 로봇의 상대 관절 위치 가져오기.

```python
@generic_io_descriptor(
    observation_type="JointState",
    on_inspect=[record_joint_names, record_dtype, record_shape, record_joint_pos_offsets],
    units="rad",
)
def joint_pos_rel(env: ManagerBasedEnv, asset_cfg: SceneEntityCfg = SceneEntityCfg("robot")) -> torch.Tensor:
    """디폴트 관절 위치에 대한 자산의 관절 위치.

    Note: :attr:`asset_cfg.joint_ids`에 구성된 관절만 위치가 반환됩니다.
    """
    # 추출된 수량 (타입 힌팅을 활성화하기 위해)
    asset: Articulation = env.scene[asset_cfg.name]
    return asset.data.joint_pos[:, asset_cfg.joint_ids] - asset.data.default_joint_pos[:, asset_cfg.joint_ids]
```

이전 예시와 마찬가지로, 관측 용어를 조사하는 함수 집합과 함께 맞춤 관측 용어에 IO 설명자를 추가하고 있습니다.

관절 이름을 가져오려면 다음과 같은 함수를 작성할 수 있습니다:

```python
def record_joint_names(output: torch.Tensor, descriptor: GenericObservationIODescriptor, **kwargs) -> None:
    """출력 텐서의 관절 이름을 기록합니다.

    `asset_cfg` 키워드 인수가 설정되어 있어야 합니다.

    Args:
        output: 출력 텐서.
        descriptor: 관절 이름을 기록할 설명자.
        **kwargs: 추가 키워드 인수.
    """
    asset: Articulation = kwargs["env"].scene[kwargs["asset_cfg"].name]
    joint_ids = kwargs["asset_cfg"].joint_ids
    if joint_ids == slice(None, None, None):
        joint_ids = list(range(len(asset.joint_names)))
    descriptor.joint_names = [asset.joint_names[i] for i in joint_ids]
```

관측 용어의 모든 입력을 `kwargs` 딕셔너리에서 접근할 수 있음을 주의하세요. 따라서 관측 용어가 계산되는 아티큘레이션의 구성을 포함하는 `asset_cfg`에 접근할 수 있습니다.

오프셋을 가져오려면 다음과 같은 함수를 작성할 수 있습니다:

```python
def record_joint_pos_offsets(output: torch.Tensor, descriptor: GenericObservationIODescriptor, **kwargs):
 """출력 텐서의 관절 위치 오프셋을 기록합니다.

 `asset_cfg` 키워드 인수가 설정되어 있어야 합니다.

 Args:
     output: 출력 텐서.
     descriptor: 관절 위치 오프셋을 기록할 설명자.
     **kwargs: 추가 키워드 인수.
 """
    asset: Articulation = kwargs["env"].scene[kwargs["asset_cfg"].name]
    ids = kwargs["asset_cfg"].joint_ids
    # scene의 첫 번째 로봇에 대한 관절 오프셋을 가져옵니다.
    # 모든 로봇이 동일한 관절 오프셋을 가진다고 가정합니다.
    descriptor.joint_pos_offsets = asset.data.default_joint_pos[:, ids][0]
```

이를 염두에 두고, 이제 맞춤 관측 용어에 IO 설명자를 연결할 수 있어야 합니다! 그러나 이 튜토리얼을 마치기 전에, 맞춤 액션 용어에 IO 설명자를 연결하는 방법을 살펴보겠습니다.

## 맞춤 액션 용어에 IO 설명자 연결하기

이 섹션에서는 맞춤 액션 용어에 IO 설명자를 연결하는 방법을 다룹니다. 액션 용어는 [`managers.ActionTerm`](../../api/lab/isaaclab.managers.md#isaaclab.managers.ActionTerm) 클래스를 상속받는 클래스입니다. 액션 용어에 IO 설명자를 추가하려면 그 [`IO_descriptor()`](../../api/lab/isaaclab.managers.md#isaaclab.managers.ActionTerm.IO_descriptor) 속성을 확장해야 합니다.

기본적으로, [`IO_descriptor()`](../../api/lab/isaaclab.managers.md#isaaclab.managers.ActionTerm.IO_descriptor) 속성은 기본 설명자를 반환하고 다음 필드를 채웁니다:
- `name`: 액션 용어의 이름.
- `full_path`: 액션 용어의 전체 경로.
- `description`: 액션 용어의 설명.
- `export`: 액션 용어를 내보낼지 여부.

```python
@property
def IO_descriptor(self) -> GenericActionIODescriptor:
    """액션 용어에 대한 IO 설명자."""
    self._IO_descriptor.name = re.sub(r"([a-z])([A-Z])", r"\1_\2", self.__class__.__name__).lower()
    self._IO_descriptor.full_path = f"{self.__class__.__module__}.{self.__class__.__name__}"
    self._IO_descriptor.description = " ".join(self.__class__.__doc__.split())
    self._IO_descriptor.export = self.export_IO_descriptor
    return self._IO_descriptor
```

설명자에 더 많은 정보를 추가하려면 [`IO_descriptor()`](../../api/lab/isaaclab.managers.md#isaaclab.managers.ActionTerm.IO_descriptor) 속성을 오버라이드해야 합니다.
관절 이름, 스케일, 오프셋, 클립을 설명자에 추가하는 예시를 살펴보겠습니다.

```python
@property
def IO_descriptor(self) -> GenericActionIODescriptor:
    """액션 용어의 IO 설명자.

    이 설명자는 관절 액션의 액션 용어를 설명하는 데 사용됩니다.
    기본 설명자에 다음 정보를 추가합니다:
    - joint_names: 관절 이름.
    - scale: 액션 용어의 스케일.
    - offset: 액션 용어의 오프셋.
    - clip: 액션 용어의 클립.

    Returns:
        액션 용어의 IO 설명자.
    """
    super().IO_descriptor
    self._IO_descriptor.shape = (self.action_dim,)
    self._IO_descriptor.dtype = str(self.raw_actions.dtype)
    self._IO_descriptor.action_type = "JointAction"
    self._IO_descriptor.joint_names = self._joint_names
    self._IO_descriptor.scale = self._scale
    # 이는 항상 [4xNum_joints]인 것 같아요. 이유를 확인해야 할 것 같습니다.
    if isinstance(self._offset, torch.Tensor):
        self._IO_descriptor.offset = self._offset[0].detach().cpu().numpy().tolist()
    else:
        self._IO_descriptor.offset = self._offset
    # FIXME: 이는 올바르지 않습니다. 리스트 지원을 추가해야 합니다.
    if self.cfg.clip is not None:
        if isinstance(self._clip, torch.Tensor):
            self._IO_descriptor.clip = self._clip[0].detach().cpu().numpy().tolist()
        else:
            self._IO_descriptor.clip = self._clip
    else:
        self._IO_descriptor.clip = None
    return self._IO_descriptor
```

이제 맞춤 액션 용어에 IO 설명자를 연결할 수 있어야 합니다! 이것이 이 튜토리얼의 결론입니다.
