<a id="migrating-from-orbit"></a>

# Orbit에서 마이그레이션하기

[Orbit](https://isaac-orbit.github.io/)이 Isaac Lab의 기반이 되었기 때문에, Orbit에서 Isaac Lab로의 마이그레이션은 직관적입니다.
다음 섹션에서는 코드를 Orbit에서 Isaac Lab로 마이그레이션하기 위해 필요한 변경 사항을 설명합니다.

#### 참고
다음 변경 사항은 Isaac Lab 1.0 릴리스 기준이며, 향후 릴리스의 변경 사항은 [릴리스 노트](https://github.com/isaac-sim/IsaacLab/releases)를 참조하십시오.

## 런칭 스크립트 이름 변경

스크립트 `orbit.sh`가 `isaaclab.sh`로 이름이 변경되었습니다.

## 확장 프로그램 업데이트

확장 프로그램 `omni.isaac.orbit`, `omni.isaac.orbit_tasks`, 그리고 `omni.isaac.orbit_assets`가 각각 `isaaclab`, `isaaclab_tasks`, 그리고 `isaaclab_assets`로 이름이 변경되었습니다. 따라서 새로운 폴더 구조는 다음과 같습니다:

- `source/isaaclab/isaaclab`
- `source/isaaclab_tasks/isaaclab_tasks`
- `source/isaaclab_assets/isaaclab_assets`

상위 수준의 임포트도 업데이트해야 합니다:

| Orbit                             | Isaac Lab                 |
|-----------------------------------|---------------------------|
| `from omni.isaac.orbit...`        | `from isaaclab...`        |
| `from omni.isaac.orbit_tasks...`  | `from isaaclab_tasks...`  |
| `from omni.isaac.orbit_assets...` | `from isaaclab_assets...` |

## 클래스 이름 업데이트

Isaac Lab에서는 태스크 설계 워크플로 개념을 도입했습니다([태스크 설계 워크플로](../overview/core-concepts/task_workflows.md#feature-workflows) 참조). Orbit 코드는 관리자 기반 워크플로를 사용하며, 환경 특정 클래스 이름이 이 변경을 반영하도록 업데이트되었습니다:

| Orbit             | Isaac Lab                                                                                                             |
|-------------------|-----------------------------------------------------------------------------------------------------------------------|
| `BaseEnv`         | [`isaaclab.envs.ManagerBasedEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv)                          |
| `BaseEnvCfg`      | [`isaaclab.envs.ManagerBasedEnvCfg`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedEnvCfg)                    |
| `RLTaskEnv`       | [`isaaclab.envs.ManagerBasedRLEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv)                      |
| `RLTaskEnvCfg`    | [`isaaclab.envs.ManagerBasedRLEnvCfg`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnvCfg)                |
| `RLTaskEnvWindow` | [`isaaclab.envs.ui.ManagerBasedRLEnvWindow`](../api/lab/isaaclab.envs.ui.md#isaaclab.envs.ui.ManagerBasedRLEnvWindow) |

## 태스크 폴더 구조 업데이트

관리자 기반 및 직접 워크플로를 지원하기 위해, 태스크 확장 프로그램에 두 개의 폴더를 추가했습니다:

- `source/isaaclab_tasks/isaaclab_tasks/manager_based`
- `source/isaaclab_tasks/isaaclab_tasks/direct`

Orbit의 태스크는 이제 `manager_based` 폴더에서 찾을 수 있습니다. 이 변경 사항은 태스크의 임포트에도 반영되어야 합니다. 예를 들어,

```python
from omni.isaac.orbit_tasks.locomotion.velocity.velocity_env_cfg ...
```

은 이제 다음과 같이 변경되어야 합니다:

```python
from isaaclab_tasks.manager_based.locomotion.velocity.velocity_env_cfg ...
```

## 기타 브레이킹 체인지

### 디바이스 설정

인수 `--cpu`가 `--device device_name`으로 대체되었으며, `device_name`에 대한 유효한 옵션은 다음과 같습니다:

- `cpu`: CPU 사용.
- `cuda`: 디바이스 ID `0`의 GPU 사용.
- `cuda:N`: 디바이스 ID가 N인 GPU 사용. 예를 들어, `cuda:0`.

기본값은 `cuda:0`입니다.

### 오프스크린 렌더링

[`isaaclab.app.AppLauncher`](../api/lab/isaaclab.app.md#isaaclab.app.AppLauncher)에 전달되는 입력 인수 `--offscreen_render` 및 환경 변수 `OFFSCREEN_RENDER`가 각각 `--enable_cameras` 및 `ENABLE_CAMERAS`로 이름이 변경되었습니다.

### 이벤트 항목 분포 구성

[events.py](https://github.com/isaac-sim/IsaacLab/blob/main/source/isaaclab/isaaclab/envs/mdp/events.py)의 일부 이벤트 함수는 `distribution` 매개변수와 샘플링 범위를 지정하는 `range` 매개변수를 받았습니다. 임의의 분포를 지원하기 위해, 입력 인수 `AAA_range`가 `AAA_distribution_params`로 이름이 변경되었습니다. 따라서, `distribution` 인수를 가진 이벤트 함수에 대한 이벤트 항목 구성은 다음과 같이 업데이트해야 합니다. 예를 들어,

```python
add_base_mass = EventTerm(
    func=mdp.randomize_rigid_body_mass,
    mode="startup",
    params={
        "asset_cfg": SceneEntityCfg("robot", body_names="base"),
        "mass_range": (-5.0, 5.0),
        "operation": "add",
    },
)
```

은 이제 다음과 같이 변경되어야 합니다:

```python
add_base_mass = EventTerm(
    func=mdp.randomize_rigid_body_mass,
    mode="startup",
    params={
        "asset_cfg": SceneEntityCfg("robot", body_names="base"),
        "mass_distribution_params": (-5.0, 5.0),
        "operation": "add",
    },
)
```
