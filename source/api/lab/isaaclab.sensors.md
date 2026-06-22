# isaaclab.sensors

다양한 센서 클래스 구현을 포함하는 하위 패키지입니다.

이 하위 패키지는 Isaac Sim과 호환되는 센서 클래스를 포함합니다. USD 기반 센서와 사용자 정의 센서가 모두 포함됩니다.

* **USD-prim 센서**: Omniverse에서 사용 가능하며, 해당 센서를 사용하기 위해 USD prim을 생성해야 합니다. 예를 들어, RTX 레이 트레이싱 카메라 및 라이더 센서가 있습니다.
* **USD-스키마 센서**: Omniverse에서 사용 가능하며, 기존 prim에 USD 스키마를 생성해야 합니다. 예를 들어, 접촉 센서 및 프레임 트랜스포머가 있습니다.
* **사용자 정의 센서**: Python으로 구현되었으며, USD prim 또는 스키마를 생성할 필요가 없습니다. 예를 들어, 워프 기반 레이 캐스터가 있습니다.

위의 분류로 인해 센서의 구성 클래스에 전달되는 prim 경로는 센서 유형에 따라 다르게 해석됩니다. 다음 표는 다양한 센서 유형에 대한 prim 경로 해석을 요약한 것입니다.

| 센서 유형       | 예시 prim 경로          | 사전 검사                                                     |
|-----------------|-------------------------|---------------------------------------------------------------|
| 카메라          | /World/robot/base/camera | 리프가 존재하며, USD 카메라를 생성합니다.                       |
| 접촉 센서       | /World/robot/feet_\*    | 리프가 존재하며, 스키마가 존재하는지 확인합니다.               |
| 레이 캐스터     | /World/robot/base       | 리프가 존재하며, 물리 물체(구성 요소 / 강체)입니다.           |
| 프레임 트랜스포머| /World/robot/base       | 리프가 존재하며, 물리 물체(구성 요소 / 강체)입니다.           |
| Imu             | /World/robot/base       | 리프가 존재하며, 강체입니다.                                  |

### 서브모듈

| [`patterns`](isaaclab.sensors.patterns.md#module-isaaclab.sensors.patterns)   | 레이 캐스터에서 사용되는 레이 캐스팅 패턴을 위한 하위 모듈입니다.   |
|-------------------------------------------------------------------------------|---------------------------------------------------------------|

### 클래스

| [`SensorBase`](#isaaclab.sensors.SensorBase)                                   | 센서 구현을 위한 기본 클래스입니다.                                                |
|--------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| [`SensorBaseCfg`](#isaaclab.sensors.SensorBaseCfg)                             | 센서의 구성 매개변수입니다.                                                   |
| [`Camera`](#isaaclab.sensors.Camera)                                           | 시각 데이터를 획득하기 위한 카메라 센서입니다.                                             |
| [`CameraData`](#isaaclab.sensors.CameraData)                                   | 카메라 센서를 위한 데이터 컨테이너입니다.                                                    |
| [`CameraCfg`](#isaaclab.sensors.CameraCfg)                                     | 카메라 센서의 구성입니다.                                                       |
| [`TiledCamera`](#isaaclab.sensors.TiledCamera)                                 | 타일 렌더링 기반 카메라 센서로, Camera 클래스와 동일한 데이터를 획득합니다. |
| [`TiledCameraCfg`](#isaaclab.sensors.TiledCameraCfg)                           | 타일 렌더링 기반 카메라 센서의 구성입니다.                                 |
| [`ContactSensor`](#isaaclab.sensors.ContactSensor)                             | 접촉 보고 센서입니다.                                                              |
| [`ContactSensorData`](#isaaclab.sensors.ContactSensorData)                     | 접촉 보고 센서를 위한 데이터 컨테이너입니다.                                         |
| [`ContactSensorCfg`](#isaaclab.sensors.ContactSensorCfg)                       | 접촉 센서의 구성입니다.                                                    |
| [`FrameTransformer`](#isaaclab.sensors.FrameTransformer)                       | 프레임 변환을 보고하는 센서입니다.                                                 |
| [`FrameTransformerData`](#isaaclab.sensors.FrameTransformerData)               | 프레임 트랜스포머 센서를 위한 데이터 컨테이너입니다.                                         |
| [`FrameTransformerCfg`](#isaaclab.sensors.FrameTransformerCfg)                 | 프레임 트랜스포머 센서의 구성입니다.                                             |
| [`RayCaster`](#isaaclab.sensors.RayCaster)                                     | 레이 캐스팅 센서입니다.                                                                    |
| [`RayCasterData`](#isaaclab.sensors.RayCasterData)                             | 레이 캐스트 센서를 위한 데이터 컨테이너입니다.                                                  |
| [`RayCasterCfg`](#isaaclab.sensors.RayCasterCfg)                               | 레이 캐스트 센서의 구성입니다.                                                   |
| [`RayCasterCamera`](#isaaclab.sensors.RayCasterCamera)                         | 레이 캐스팅 카메라 센서입니다.                                                             |
| [`RayCasterCameraCfg`](#isaaclab.sensors.RayCasterCameraCfg)                   | 레이 캐스트 센서의 구성입니다.                                                   |
| [`MultiMeshRayCaster`](#isaaclab.sensors.MultiMeshRayCaster)                   | 다중 메시 레이 캐스팅 센서입니다.                                                         |
| [`MultiMeshRayCasterData`](#isaaclab.sensors.MultiMeshRayCasterData)           | 다중 메시 레이 캐스트 센서를 위한 데이터 컨테이너입니다.                                       |
| [`MultiMeshRayCasterCfg`](#isaaclab.sensors.MultiMeshRayCasterCfg)             | 다중 메시 레이 캐스트 센서의 구성입니다.                                        |
| [`MultiMeshRayCasterCamera`](#isaaclab.sensors.MultiMeshRayCasterCamera)       | 다중 메시 레이 캐스팅 카메라 센서입니다.                                                  |
| [`MultiMeshRayCasterCameraCfg`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg) | 다중 메시 레이 캐스트 카메라 센서의 구성입니다.                                 |
| [`Imu`](#isaaclab.sensors.Imu)                                                 | 관성 측정 단위(IMU) 센서입니다.                                               |
| [`ImuCfg`](#isaaclab.sensors.ImuCfg)                                           | 관성 측정 단위(IMU) 센서의 구성입니다.                             |

## 센서 기본 클래스

### *class* isaaclab.sensors.SensorBase

센서 구현을 위한 기본 클래스입니다.

구현은 지연 평가를 기반으로 합니다. 센서 데이터는 사용자가 [`data`](#isaaclab.sensors.SensorBase.data) 속성을 통해 데이터에 접근하려고 시도하거나, `update()` 메서드에서 `force_compute=True`를 설정할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 피하기 위해 수행됩니다.

센서는 지정된 업데이트 주기에 따라 업데이트됩니다. 업데이트 주기가 zero이면, 시뮬레이션의 모든 단계에서 센서가 업데이트됩니다.

**메서드:**

| [`__init__`](#isaaclab.sensors.SensorBase.__init__)(cfg)                 | 센서 클래스를 초기화합니다.               |
|--------------------------------------------------------------------------|--------------------------------------------|
| [`set_debug_vis`](#isaaclab.sensors.SensorBase.set_debug_vis)(debug_vis) | 센서 데이터 시각화 여부를 설정합니다. |
| [`reset`](#isaaclab.sensors.SensorBase.reset)([env_ids])                 | 센서 내부를 재설정합니다.               |

**속성:**

| [`is_initialized`](#isaaclab.sensors.SensorBase.is_initialized)                             | 센서가 초기화되었는지 여부입니다.                        |
|---------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`num_instances`](#isaaclab.sensors.SensorBase.num_instances)                               | 센서의 인스턴스 수입니다.                        |
| [`device`](#isaaclab.sensors.SensorBase.device)                                             | 계산에 사용되는 메모리 장치입니다.                            |
| [`data`](#isaaclab.sensors.SensorBase.data)                                                 | 센서에서 얻은 데이터입니다.                                     |
| [`has_debug_vis_implementation`](#isaaclab.sensors.SensorBase.has_debug_vis_implementation) | 센서에 디버그 시각화가 구현되었는지 여부입니다. |

#### \_\_init_\_(cfg: [SensorBaseCfg](#isaaclab.sensors.SensorBaseCfg))

센서 클래스를 초기화합니다.

* **매개변수:**
  **cfg** – 센서의 구성 매개변수입니다.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부를 나타냅니다.

센서가 초기화된 경우 True를 반환하고, 그렇지 않은 경우 False를 반환합니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서의 인스턴스 수입니다.

이 값은 환경당 센서 수와 환경 수를 곱한 값과 같습니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 장치입니다.

#### *abstract property* data *: [Any](https://docs.python.org/3/library/typing.html#typing.Any)*

센서에서 얻은 데이터입니다.

이 속성은 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 피하기 위해 수행됩니다.

이 속성에 접근할 때 센서를 업데이트하려면 센서 구현에서 다음 코드 스니펫을 사용할 수 있습니다:

```python
# 업데이트가 필요한 센서 처리
self._update_outdated_buffers()
# 데이터 반환 (여기서 `_data`는 센서의 데이터입니다)
return self._data
```

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서에 디버그 시각화가 구현되었는지 여부입니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터 시각화 여부를 설정합니다.

* **Parameters:**
  **debug_vis** – 센서 데이터 시각화 여부.
* **Returns:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 센서가 디버그 시각화를 지원하지 않는 경우 False 반환.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서 내부를 재설정합니다.

* **Parameters:**
  **env_ids** – 재설정할 센서 ID. 기본값은 None.

### *class* isaaclab.sensors.SensorBaseCfg

센서의 구성 매개변수.

**Attributes:**

| [`prim_path`](#isaaclab.sensors.SensorBaseCfg.prim_path)           | 센서의 Prim 경로 (또는 표현식).              |
|--------------------------------------------------------------------|-------------------------------------------------------|
| [`update_period`](#isaaclab.sensors.SensorBaseCfg.update_period)   | 센서 버퍼의 업데이트 주기 (초 단위).     |
| [`history_length`](#isaaclab.sensors.SensorBaseCfg.history_length) | 센서 버퍼에 저장할 과거 프레임 수. |
| [`debug_vis`](#isaaclab.sensors.SensorBaseCfg.debug_vis)           | 센서 시각화 여부.                      |

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 Prim 경로 (또는 표현식).

#### NOTE
표현식에 환경 네임스페이스 정규식 `{ENV_REGEX_NS}` 을 포함할 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot/sensor` 가 `/World/envs/env_.*/Robot/sensor` 로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼의 업데이트 주기 (초 단위). 기본값은 0.0 (매 스텝마다 업데이트).

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수. 기본값은 0으로, 이는 현재 데이터만 저장함을 의미함 (기록 없음).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서 시각화 여부. 기본값은 False.

## USD 카메라

### *class* isaaclab.sensors.Camera

Bases: [`SensorBase`](#isaaclab.sensors.SensorBase)

시각적 데이터를 획득하는 카메라 센서.

이 클래스는 [UsdGeom Camera](https://graphics.pixar.com/usd/docs/api/class_usd_geom_camera.html)를 래핑하여 시각적 데이터 획득을 위한 일관된 API를 제공합니다.
ROS 좌표계 conventions을 따르도록 카메라를 보장합니다.

[replicator 확장 프로그램](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/annotators_details.html#annotator-output)에서 요약한 바와 같이, 다음과 같은 센서 유형이 지원됩니다:

- `"rgb"`: 3채널 렌더링된 컬러 이미지.
- `"rgba"`: 알파 채널이 있는 4채널 렌더링된 컬러 이미지.
- `"distance_to_camera"`: 카메라 광학 중심을 기준으로 거리 정보를 포함한 이미지.
- `"distance_to_image_plane"`: 카메라 평면으로부터의 Z축 방향 거리 정보를 포함한 이미지.
- `"depth"`: `"distance_to_image_plane"` 와 동일함.
- `"normals"`: 각 픽셀의 국소 표면 법선 벡터를 포함한 이미지.
- `"motion_vectors"`: 각 픽셀의 모션 벡터 데이터를 포함한 이미지.
- `"semantic_segmentation"`: 시맨틱 세그멘테이션 데이터.
- `"instance_segmentation_fast"`: 인스턴스 세그멘테이션 데이터 (빠른 버전).
- `"instance_id_segmentation_fast"`: 인스턴스 ID 세그멘테이션 데이터 (빠른 버전).

#### NOTE
현재 다음 센서 유형은 “뷰” 형식에서는 지원되지 않습니다:

- `"instance_segmentation"`: 인스턴스 세그멘테이션 데이터. 대신 빠른 버전을 사용하세요.
- `"instance_id_segmentation"`: 인스턴스 ID 세그멘테이션 데이터. 대신 빠른 버전을 사용하세요.
- `"bounding_box_2d_tight"`: 타이트한 2D 경계 상자 데이터 (비폐색 영역만 포함).
- `"bounding_box_2d_tight_fast"`: 타이트한 2D 경계 상자 데이터 (비폐색 영역만 포함).
- `"bounding_box_2d_loose"`: 루즈한 2D 경계 상자 데이터 (폐색 영역 포함).
- `"bounding_box_2d_loose_fast"`: 루즈한 2D 경계 상자 데이터 (폐색 영역 포함).
- `"bounding_box_3d"`: 뷰 공간 내 3D 경계 상자 데이터.
- `"bounding_box_3d_fast"`: 뷰 공간 내 3D 경계 상자 데이터.

**Attributes:**

| [`cfg`](#isaaclab.sensors.Camera.cfg)                                                   | 구성 매개변수.                                       |
|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [`UNSUPPORTED_TYPES`](#isaaclab.sensors.Camera.UNSUPPORTED_TYPES)                       | 카메라 클래스에서 지원되지 않는 센서 유형 집합. |
| [`num_instances`](#isaaclab.sensors.Camera.num_instances)                               | 센서 인스턴스 수.                                  |
| [`data`](#isaaclab.sensors.Camera.data)                                                 | 센서에서 얻은 데이터.                                               |
| [`frame`](#isaaclab.sensors.Camera.frame)                                               | 측정이 이루어진 프레임 번호.                       |
| [`render_product_paths`](#isaaclab.sensors.Camera.render_product_paths)                 | 카메라의 렌더 제품 경로.                    |
| [`image_shape`](#isaaclab.sensors.Camera.image_shape)                                   | 카메라 센서의 (높이, 너비) 튜플.            |
| [`device`](#isaaclab.sensors.Camera.device)                                             | 계산에 사용되는 메모리 디바이스.                                      |
| [`has_debug_vis_implementation`](#isaaclab.sensors.Camera.has_debug_vis_implementation) | 센서에 디버그 시각화 구현이 있는지 여부.           |
| [`is_initialized`](#isaaclab.sensors.Camera.is_initialized)                             | 센서 초기화 여부.                                  |

**Methods:**

| [`__init__`](#isaaclab.sensors.Camera.__init__)(cfg)                                                    | 카메라 센서 초기화.                                                 |
|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`set_intrinsic_matrices`](#isaaclab.sensors.Camera.set_intrinsic_matrices)(matrices[, ...])            | 내재 행렬로부터 USD 카메라 파라미터 설정.                    |
| [`set_world_poses`](#isaaclab.sensors.Camera.set_world_poses)([positions, orientations, ...])           | 월드 좌표계 기준 카메라 포즈 설정.                                              |
| [`set_world_poses_from_view`](#isaaclab.sensors.Camera.set_world_poses_from_view)(eyes, targets[, ...]) | 눈 위치 및 바라볼 위치로부터 카메라 포즈 설정. |
| [`reset`](#isaaclab.sensors.Camera.reset)([env_ids])                                                    | 센서 내부 재설정.                                                   |
| [`set_debug_vis`](#isaaclab.sensors.Camera.set_debug_vis)(debug_vis)                                    | 센서 데이터 시각화 여부 설정.                                     |

#### cfg *: [CameraCfg](#isaaclab.sensors.CameraCfg)*

구성 매개변수.

#### UNSUPPORTED_TYPES *: [set](https://docs.python.org/3/library/stdtypes.html#set)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= {'bounding_box_2d_loose', 'bounding_box_2d_loose_fast', 'bounding_box_2d_tight', 'bounding_box_2d_tight_fast', 'bounding_box_3d', 'bounding_box_3d_fast', 'instance_id_segmentation', 'instance_segmentation'}*

카메라 클래스에서 지원되지 않는 센서 유형 집합.

#### \_\_init_\_(cfg: [CameraCfg](#isaaclab.sensors.CameraCfg))

카메라 센서를 초기화합니다.

* **Parameters:**
  **cfg** – 구성 매개변수.
* **Raises:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 지정된 경로에 카메라 prim이 없을 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 데이터 유형이 카메라에서 지원되지 않을 경우.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 인스턴스 수.

이는 환경당 센서 수에 환경 수를 곱한 값과 동일합니다.

#### *property* data *: [CameraData](#isaaclab.sensors.CameraData)*

센서에서 얻은 데이터.

이 속성은 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 피하기 위함입니다.

이 속성에 접근할 때 센서를 업데이트하려면, 센서 구현에서 다음 코드 스니펫을 사용할 수 있습니다:

```python
# update sensors if needed
self._update_outdated_buffers()
# return the data (where `_data` is the data for the sensor)
return self._data
```

#### *property* frame *: torch.tensor*

측정이 이루어진 프레임 번호.

#### *property* render_product_paths *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

카메라의 렌더 제품 경로.

이 경로는 replicator 인터페이스를 통해 쓰기 작업에 연결하거나 외부 어노테이터 레지스트리와 연동하는 데 사용할 수 있습니다.

#### *property* image_shape *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int)]*

카메라 센서의 높이와 너비를 포함하는 튜플입니다.

#### set_intrinsic_matrices(matrices: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), focal_length: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

USD 카메라의 내부 행렬을 기반으로 카메라 매개변수를 설정합니다.

내부 행렬은 다음 USD 카메라 매개변수를 설정하는 데 사용됩니다:

- `focal_length`: 카메라의 초점 거리.
- `horizontal_aperture`: 카메라의 수평 개구부.
- `vertical_aperture`: 카메라의 수직 개구부.
- `horizontal_aperture_offset`: 카메라의 수평 오프셋.
- `vertical_aperture_offset`: 카메라의 수직 오프셋.

#### WARNING
옴니버스 카메라의 제한으로 인해 카메라가 구면 렌즈라고 가정해야 합니다. 즉, 픽셀이 정사각형이고 광학 중심이 카메라 눈 중앙에 있어야 합니다. 입력 내부 행렬에서 이 가정이 참이 아니라면 카메라가 올바르게 설정되지 않을 수 있습니다.

* **매개변수:**
  * **matrices** – 카메라의 내부 행렬. 크기는 (N, 3, 3)입니다.
  * **focal_length** – 픽셀 크기 계산을 위해 사용되는 원근 초점 거리(센티미터 단위). 기본값은 None입니다. None인 경우, focal_length은 1 / width로 계산됩니다.
  * **env_ids** – 조작할 센서 ID. 기본값은 None이며, 즉 모든 센서 인덱스를 의미합니다.

#### set_world_poses(positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, orientations: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, convention: Literal['opengl', 'ros', 'world'] = 'ros')

지정된 규칙에 따라 세계 좌표계 기준 카메라의 자세를 설정합니다.

여러 분야에서 카메라 방향에 서로 다른 규칙을 사용하기 때문에, 사용자는 지정된 규칙에 따라 카메라 자세를 설정할 수 있습니다. 가능한 규칙은 다음과 같습니다:

- `"opengl"` - 앞쪽 축: -Z - 위쪽 축: +Y - 오프셋이 OpenGL (Usd.Camera) 규칙에 적용됨
- `"ros"`    - 앞쪽 축: +Z - 위쪽 축: -Y - 오프셋이 ROS 규칙에 적용됨
- `"world"`  - 앞쪽 축: +X - 위쪽 축: +Z - 오프셋이 세계 좌표계 규칙에 적용됨

규칙에 대한 자세한 내용은 `isaaclab.sensors.camera.utils.convert_camera_frame_orientation_convention()`을 참조하세요.

* **매개변수:**
  * **positions** – 카르테시안 좌표(미터 단위). 크기는 (N, 3)입니다. 기본값은 None이며, 이 경우 카메라 위치가 변경되지 않습니다.
  * **orientations** – (w, x, y, z) 순서의 쿼터니언 방향. 크기는 (N, 4)입니다. 기본값은 None이며, 이 경우 카메라 방향이 변경되지 않습니다.
  * **env_ids** – 조작할 센서 ID. 기본값은 None이며, 즉 모든 센서 인덱스를 의미합니다.
  * **convention** – 자세가 제공되는 규칙. 기본값은 "ros"입니다.
* **예외:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 카메라 프라임이 설정되지 않은 경우. 먼저 `initialize()` 메서드를 호출해야 합니다.

#### set_world_poses_from_view(eyes: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), targets: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

카메라의 눈 위치와 바라볼 대상 위치로부터 카메라의 자세를 설정합니다.

* **매개변수:**
  * **eyes** – 카메라 눈의 위치. 크기는 (N, 3)입니다.
  * **targets** – 바라볼 대상 위치. 크기는 (N, 3)입니다.
  * **env_ids** – 조작할 센서 ID. 기본값은 None이며, 즉 모든 센서 인덱스를 의미합니다.
* **예외:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 카메라 프라임이 설정되지 않은 경우. 먼저 `initialize()` 메서드를 호출해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 스테이지 위쪽 축이 "Y" 또는 "Z"가 아닌 경우.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서 내부 상태를 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 센서 ID. 기본값은 None입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 디바이스.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서에 디버그 시각화가 구현되어 있는지 여부.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부.

센서가 초기화되었으면 True를 반환하고, 그렇지 않으면 False를 반환합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 센서 데이터를 시각화할지 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 센서가 디버그 시각화를 지원하지 않는 경우 False.

### *class* isaaclab.sensors.CameraData

카메라 센서를 위한 데이터 컨테이너.

**속성:**

| [`pos_w`](#isaaclab.sensors.CameraData.pos_w)                           | ROS 규칙에 따라 세계 좌표계에서의 센서 원점 위치.                                                                |
|-------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| [`quat_w_world`](#isaaclab.sensors.CameraData.quat_w_world)             | 세계 좌표계 규칙에 따라 세계 좌표계에서의 센서 원점 쿼터니언 방향 (w, x, y, z)                                          |
| [`image_shape`](#isaaclab.sensors.CameraData.image_shape)               | 카메라 센서의 (높이, 너비)를 포함하는 튜플.                                                                   |
| [`intrinsic_matrices`](#isaaclab.sensors.CameraData.intrinsic_matrices) | 카메라의 내부 행렬.                                                                                 |
| [`output`](#isaaclab.sensors.CameraData.output)                         | 센서 타입을 키로 갖는 검색된 센서 데이터.                                                                    |
| [`info`](#isaaclab.sensors.CameraData.info)                             | 센서 타입을 키로 갖는 검색된 센서 정보.                                                                    |
| [`quat_w_ros`](#isaaclab.sensors.CameraData.quat_w_ros)                 | ROS 규칙에 따라 세계 좌표계에서의 센서 원점 쿼터니언 방향 (w, x, y, z).                                          |
| [`quat_w_opengl`](#isaaclab.sensors.CameraData.quat_w_opengl)           | OpenGL / USD 카메라 규칙에 따라 세계 좌표계에서의 센서 원점 쿼터니언 방향 (w, x, y, z).                               |

#### pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

ROS 규칙에 따라 세계 좌표계에서의 센서 원점 위치.

크기는 (N, 3)이며, N은 센서의 수입니다.

#### quat_w_world *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

세계 좌표계 규칙에 따라 세계 좌표계에서의 센서 원점 쿼터니언 방향 (w, x, y, z).

#### NOTE
세계 좌표계 규칙은 앞쪽 축이 +X이고 위쪽 축이 +Z인 카메라 방향을 따릅니다.

크기는 (N, 4)이며, N은 센서의 수입니다.

#### image_shape *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int)]* *= None*

카메라 센서의 (높이, 너비)를 포함하는 튜플.

#### intrinsic_matrices *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

카메라의 내부 행렬.

크기는 (N, 3, 3)이며, N은 센서의 수입니다.

#### output *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]* *= None*

센서 타입을 키로 갖는 검색된 센서 데이터.

데이터 형식은 [Replicator 문서](https://docs.omniverse.nvidia.com/prod_extensions/prod_extensions/ext_replicator/annotators_details.html#annotator-output)에서 확인할 수 있습니다. 의미 기반 데이터의 경우, 이 값은 센서 출력에서 `"data"` 키에 해당합니다.

#### info *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)]]* *= None*

센서 타입을 키로 갖는 검색된 센서 정보.

의미 기반 분할 라벨 매핑, 프리밥 경로 등과 같은 센서에서 제공하는 추가 정보를 포함합니다. 의미 기반 데이터의 경우, 이 값은 센서 출력에서 `"info"` 키에 해당합니다. 다른 센서 유형의 경우, info는 비어 있을 수 있습니다.

#### *property* quat_w_ros *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

월드 프레임에서 ROS 규약을 따르는 센서 기원의 쿼터니언 방향 (w, x, y, z).

#### NOTE
ROS 규약은 카메라가 전방 축 +Z 및 위쪽 축 -Y에 맞춰져 있음을 따릅니다.

형상은 센서의 수 N에 따라 (N, 4)입니다.

#### *property* quat_w_opengl *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

월드 프레임에서 Opengl / USD 카메라 규약을 따르는 센서 기원의 쿼터니언 방향 (w, x, y, z).

#### NOTE
OpenGL 규약은 카메라가 전방 축 -Z 및 위쪽 축 +Y에 맞춰져 있음을 따릅니다.

형상은 센서의 수 N에 따라 (N, 4)입니다.

### *class* isaaclab.sensors.CameraCfg

기반: [`SensorBaseCfg`](#isaaclab.sensors.SensorBaseCfg)

카메라 센서 구성.

**속성:**

| [`offset`](#isaaclab.sensors.CameraCfg.offset)                                                       | 센서 부모 프레임에 대한 센서 프레임의 오프셋 포즈.     |
|------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| [`spawn`](#isaaclab.sensors.CameraCfg.spawn)                                                         | 자산의 생성 구성.                                        |
| [`depth_clipping_behavior`](#isaaclab.sensors.CameraCfg.depth_clipping_behavior)                     | 최대값을 초과하는 값에 대한 카메라의 클리핑 동작.     |
| [`data_types`](#isaaclab.sensors.CameraCfg.data_types)                                               | 카메라에 대해 활성화할 센서 이름/유형 목록.                      |
| [`prim_path`](#isaaclab.sensors.CameraCfg.prim_path)                                                 | 센서의 Prim 경로 (또는 표현식).                                  |
| [`update_period`](#isaaclab.sensors.CameraCfg.update_period)                                         | 센서 버퍼의 업데이트 주기 (초 단위).                         |
| [`history_length`](#isaaclab.sensors.CameraCfg.history_length)                                       | 센서 버퍼에 저장할 과거 프레임 수.                     |
| [`debug_vis`](#isaaclab.sensors.CameraCfg.debug_vis)                                                 | 센서 시각화 여부.                                          |
| [`width`](#isaaclab.sensors.CameraCfg.width)                                                         | 이미지의 너비 (픽셀).                                             |
| [`height`](#isaaclab.sensors.CameraCfg.height)                                                       | 이미지의 높이 (픽셀).                                            |
| [`update_latest_camera_pose`](#isaaclab.sensors.CameraCfg.update_latest_camera_pose)                 | 카메라 데이터를 가져올 때 최신 카메라 포즈 업데이트 여부. |
| [`semantic_filter`](#isaaclab.sensors.CameraCfg.semantic_filter)                                     | 의미 필터 지정자(문자열 또는 리스트).                |
| [`colorize_semantic_segmentation`](#isaaclab.sensors.CameraCfg.colorize_semantic_segmentation)       | 의미 분할 이미지 컬러화 여부.                     |
| [`colorize_instance_id_segmentation`](#isaaclab.sensors.CameraCfg.colorize_instance_id_segmentation) | 인스턴스 ID 분할 이미지 컬러화 여부.                  |
| [`colorize_instance_segmentation`](#isaaclab.sensors.CameraCfg.colorize_instance_segmentation)       | 인스턴스 분할 이미지 컬러화 여부.                     |
| [`semantic_segmentation_mapping`](#isaaclab.sensors.CameraCfg.semantic_segmentation_mapping)         | 의미에 특정 색상 매핑을 위한 사전                      |

#### offset *: [OffsetCfg](#isaaclab.sensors.TiledCameraCfg.OffsetCfg)*

센서 부모 프레임에 대한 센서 프레임의 오프셋 포즈. 기본값은 항등입니다.

#### NOTE
상위 프레임은 센서가 연결되는 프레임입니다. 예를 들어, 경로 `/World/envs/env_0/Robot/Camera`에 있는 카메라의 상위 프레임은 `/World/envs/env_0/Robot`입니다.

#### spawn *: [PinholeCameraCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.sensors.PinholeCameraCfg) | [FisheyeCameraCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.sensors.FisheyeCameraCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

자산의 생성 구성.

None인 경우, 자산에서 프레임이 생성되지 않으며, 대신 자산이 이미 씬에 존재한다고 가정됩니다.

#### depth_clipping_behavior *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['max', 'zero', 'none']*

최대값을 초과하는 값에 대한 카메라의 클리핑 동작. 기본값은 “none”입니다.

- `"max"`: 값이 최대값으로 클리핑됩니다.
- `"zero"`: 값이 0으로 클리핑됩니다.
- `"none`: 클리핑이 적용되지 않으며, 값은 `inf`로 반환됩니다.

#### data_types *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

카메라에 대해 활성화할 센서 이름/유형 목록. 기본값은 [“rgb”]입니다.

사용 가능한 데이터 유형 목록은 [`Camera`](#isaaclab.sensors.Camera) 클래스를 참조하세요.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 Prim 경로 (또는 표현식).

#### NOTE
이 표현식은 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`을 포함할 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot/sensor`는 `/World/envs/env_.*/Robot/sensor`로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼의 업데이트 주기 (초 단위). 기본값은 0.0 (매 단계마다 업데이트).

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수. 기본값은 0으로, 이는 현재 데이터만 저장됨을 의미합니다 (히스토리 없음).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서 시각화 여부. 기본값은 False입니다.

#### width *: [int](https://docs.python.org/3/library/functions.html#int)*

이미지의 너비 (픽셀).

#### height *: [int](https://docs.python.org/3/library/functions.html#int)*

이미지의 높이 (픽셀).

#### update_latest_camera_pose *: [bool](https://docs.python.org/3/library/functions.html#bool)*

카메라 데이터를 가져올 때 최신 카메라 포즈 업데이트 여부. 기본값은 False입니다.

True인 경우, 최신 카메라 포즈가 카메라 데이터에 업데이트되며, `XformPrimView` 사용으로 인해 성능이 저하될 수 있습니다.
False인 경우, 초기화 시 카메라 포즈가 반환됩니다.

#### semantic_filter *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

의미 필터 지정자(문자열 또는 리스트). 기본값은 `"*:*"`입니다.

문자열인 경우, (의미 유형, 레이블)의 합동 정규형이어야 합니다. 예를 들면:

* `"typeA : labelA & !labelB | labelC , typeB: labelA ; typeC: labelE"`:
  의미 유형 “typeA”이고 레이블이 “labelA”이지만 “labelB”가 아닌, 또는 레이블이 “labelC”인 모든 프림.
  또한, 의미 유형 “typeB”이고 레이블이 “labelA”이거나, 의미 유형 “typeC”이고 레이블이 “labelE”인 모든 프림.
* `"typeA : * ; * : labelA"`: 의미 유형 “typeA”이거나 레이블이 “labelA”인 모든 프림.

문자열 리스트인 경우, 각 문자열은 의미 유형이어야 합니다. 지정된 유형의 의미를 갖는 프림에 대한 분할이 검색됩니다. 예를 들어, 리스트가 [“class”]인 경우, 의미 유형이 “class”인 프림에 대한 분할만 검색됩니다.

#### SEE ALSO
의미 필터에 대한 자세한 내용은 [Replicator Semantics Schema Editor](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하세요.

#### colorize_semantic_segmentation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

의미 분할 이미지 컬러화 여부. 기본값은 True입니다.

True인 경우, 의미 분할은 의미 ID를 색상에 매핑하여 이미지로 변환되고 `uint8` 4채널 배열로 반환됩니다. False인 경우, 출력은 `int32` 배열로 반환됩니다.

#### colorize_instance_id_segmentation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

인스턴스 ID 분할 이미지 컬러화 여부. 기본값은 True입니다.

True인 경우, 인스턴스 ID 분할은 인스턴스 ID를 색상에 매핑하여 이미지로 변환되고 `uint8` 4채널 배열로 반환됩니다. False인 경우, 출력은 `int32` 배열로 반환됩니다.

#### colorize_instance_segmentation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

인스턴스 분할 이미지 컬러화 여부. 기본값은 True입니다.

True인 경우, 인스턴스 분할은 인스턴스 ID를 색상에 매핑하여 이미지로 변환되고 `uint8` 4채널 배열로 반환됩니다. False인 경우, 출력은 `int32` 배열로 반환됩니다.

#### semantic_segmentation_mapping *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)*

의미를 특정 색상에 매핑하는 사전

예시.

```python
{
    "class:cube_1": (255, 36, 66, 255),
    "class:cube_2": (255, 184, 48, 255),
    "class:cube_3": (55, 255, 139, 255),
    "class:table": (255, 237, 218, 255),
    "class:ground": (100, 100, 100, 255),
    "class:robot": (61, 178, 255, 255),
}
```

## 타일 렌더링된 USD 카메라

### *class* isaaclab.sensors.TiledCamera

기반: [`Camera`](#isaaclab.sensors.Camera)

카메라 클래스와 동일한 데이터를 획득하기 위한 타일 렌더링 기반 카메라 센서.

이 클래스는 [`Camera`](#isaaclab.sensors.Camera) 클래스를 상속하지만 타일드-렌더링 API를 사용하여 시각적 데이터를 획득합니다. 타일드-렌더링은 여러 카메라에서 렌더링된 이미지를 하나의 이미지로 연결합니다. 이를 통해 여러 카메라를 병렬로 렌더링할 수 있으며, 여러 카메라가 있는 대규모 장면을 효율적으로 렌더링하는 데 유용합니다.

다음 센서 유형이 지원됩니다:

- `"rgb"`: 3채널 렌더링된 컬러 이미지.
- `"rgba"`: 알파 채널이 포함된 4채널 렌더링된 컬러 이미지.
- `"distance_to_camera"`: 카메라 광학적 중심을 기준으로 한 거리를 포함하는 이미지.
- `"distance_to_image_plane"`: 카메라의 z축을 기준으로 카메라 평면으로부터의 3D 점의 거리를 포함하는 이미지.
- `"depth"`: `"distance_to_image_plane"`의 별칭.
- `"normals"`: 각 픽셀의 로컬 표면 법선 벡터를 포함하는 이미지.
- `"motion_vectors"`: 각 픽셀의 모션 벡터 데이터를 포함하는 이미지.
- `"semantic_segmentation"`: 의미적 분할 데이터.
- `"instance_segmentation_fast"`: 인스턴스 분할 데이터.
- `"instance_id_segmentation_fast"`: 인스턴스 ID 분할 데이터.

#### 참고
현재 다음과 같은 센서 유형은 “뷰” 형식에서 지원되지 않습니다:

- `"instance_segmentation"`: 인스턴스 분할 데이터. 대신 빠른 버전을 사용해 주세요.
- `"instance_id_segmentation"`: 인스턴스 ID 분할 데이터. 대신 빠른 버전을 사용해 주세요.
- `"bounding_box_2d_tight"`: 타이트한 2D 경계 상자 데이터(비폐색 영역만 포함).
- `"bounding_box_2d_tight_fast"`: 타이트한 2D 경계 상자 데이터(비폐색 영역만 포함).
- `"bounding_box_2d_loose"`: 루즈한 2D 경계 상자 데이터(폐색 영역 포함).
- `"bounding_box_2d_loose_fast"`: 루즈한 2D 경계 상자 데이터(폐색 영역 포함).
- `"bounding_box_3d"`: 3D 뷰 공간 경계 상자 데이터.
- `"bounding_box_3d_fast"`: 3D 뷰 공간 경계 상자 데이터.

#### 버전 추가
v1.0.0에서 새 기능: 이 기능은 Isaac Sim 4.2부터 사용 가능합니다. 이전 버전에서는 타일드 렌더링 API를 사용할 수 없었습니다.

**속성:**

| [`cfg`](#isaaclab.sensors.TiledCamera.cfg)                                                   | 구성 파라미터.                                       |
|----------------------------------------------------------------------------------------------|------------------------------------------------------|
| [`UNSUPPORTED_TYPES`](#isaaclab.sensors.TiledCamera.UNSUPPORTED_TYPES)                       | 카메라 클래스에서 지원되지 않는 센서 유형의 집합.    |
| [`data`](#isaaclab.sensors.TiledCamera.data)                                                 | 센서에서의 데이터.                                   |
| [`device`](#isaaclab.sensors.TiledCamera.device)                                             | 계산을 위한 메모리 디바이스.                         |
| [`frame`](#isaaclab.sensors.TiledCamera.frame)                                               | 측정 시점이 이루어진 프레임 번호.                      |
| [`has_debug_vis_implementation`](#isaaclab.sensors.TiledCamera.has_debug_vis_implementation) | 센서가 디버그 시각화를 구현했는지 여부.               |
| [`image_shape`](#isaaclab.sensors.TiledCamera.image_shape)                                   | 카메라 센서의 (높이, 너비)를 포함하는 튜플.          |
| [`is_initialized`](#isaaclab.sensors.TiledCamera.is_initialized)                             | 센서가 초기화되었는지 여부.                          |
| [`num_instances`](#isaaclab.sensors.TiledCamera.num_instances)                               | 센서의 인스턴스 수.                                  |
| [`render_product_paths`](#isaaclab.sensors.TiledCamera.render_product_paths)                 | 카메라의 렌더 제품 경로.                             |

**메서드:**

| [`__init__`](#isaaclab.sensors.TiledCamera.__init__)(cfg)                                                    | 타일드 카메라 센서를 초기화합니다.                                            |
|--------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`reset`](#isaaclab.sensors.TiledCamera.reset)([env_ids])                                                    | 센서 내부 상태를 재설정합니다.                                                 |
| [`set_debug_vis`](#isaaclab.sensors.TiledCamera.set_debug_vis)(debug_vis)                                    | 센서 데이터 시각화 여부를 설정합니다.                                          |
| [`set_intrinsic_matrices`](#isaaclab.sensors.TiledCamera.set_intrinsic_matrices)(matrices[, ...])            | USD 카메라의 내재 매트릭스를 통해 파라미터를 설정합니다.                       |
| [`set_world_poses`](#isaaclab.sensors.TiledCamera.set_world_poses)([positions, orientations, ...])           | 월드 좌표계 기준 카메라의 포즈를 설정합니다.                                   |
| [`set_world_poses_from_view`](#isaaclab.sensors.TiledCamera.set_world_poses_from_view)(eyes, targets[, ...]) | 눈 위치 및 보기 대상 위치로부터 카메라의 포즈를 설정합니다.                   |

#### cfg *: [TiledCameraCfg](#isaaclab.sensors.TiledCameraCfg)*

구성 파라미터.

#### \_\_init_\_(cfg: [TiledCameraCfg](#isaaclab.sensors.TiledCameraCfg))

타일드 카메라 센서를 초기화합니다.

* **매개변수:**
  **cfg** – 구성 파라미터.
* **예외:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 주어진 경로에 카메라 프리미가 없을 경우 발생.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 데이터 유형이 카메라에서 지원되지 않을 경우 발생.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서 내부 상태를 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 센서 ID. 기본값은 None입니다.

#### UNSUPPORTED_TYPES *: [set](https://docs.python.org/3/library/stdtypes.html#set)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= {'bounding_box_2d_loose', 'bounding_box_2d_loose_fast', 'bounding_box_2d_tight', 'bounding_box_2d_tight_fast', 'bounding_box_3d', 'bounding_box_3d_fast', 'instance_id_segmentation', 'instance_segmentation'}*

카메라 클래스에서 지원되지 않는 센서 유형의 집합.

#### *property* data *: [CameraData](#isaaclab.sensors.CameraData)*

센서에서의 데이터.

이 속성은 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 방지하기 위함입니다.

이 속성에 접근할 때 센서를 업데이트하려면 센서 구현에서 다음 코드 스니펫을 사용할 수 있습니다:

```python
# update sensors if needed
self._update_outdated_buffers()
# return the data (where `_data` is the data for the sensor)
return self._data
```

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 위한 메모리 디바이스.

#### *property* frame *: torch.tensor*

측정 시점이 이루어진 프레임 번호.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 디버그 시각화를 구현했는지 여부.

#### *property* image_shape *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int)]*

카메라 센서의 (높이, 너비)를 포함하는 튜플.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부.

센서가 초기화된 경우 True를 반환하고, 그렇지 않은 경우 False를 반환합니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서의 인스턴스 수.

이는 환경당 센서 수에 환경 수를 곱한 값과 같습니다.

#### *property* render_product_paths *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

카메라의 렌더 제품 경로.

이 경로는 레플리케이터 인터페이스를 통해 쓰기나 외부 어노테이터 레지스트리에 연결하는 데 사용할 수 있습니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)*

센서 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 센서 데이터를 시각화할지 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 센서가 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

#### set_intrinsic_matrices(matrices: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), focal_length: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

USD 카메라의 내재 매트릭스를 통해 파라미터를 설정합니다.

내재 매트릭스는 다음과 같은 USD 카메라 파라미터를 설정하는 데 사용됩니다:

- `focal_length`: 카메라의 초점 거리.
- `horizontal_aperture`: 카메라의 수평 개구부.
- `vertical_aperture`: 카메라의 수직 개구부.
- `horizontal_aperture_offset`: 카메라의 수평 오프셋.
- `vertical_aperture_offset`: 카메라의 수직 오프셋.

#### 경고
다음은 제공된 영어 마크다운 문서를 한국어로 번역한 내용입니다. 코드 블록 내 프로그래밍 주석만 번역했으며, 나머지는 기술 용어와 마크다운 구조를 그대로 유지했습니다.

Omniverse 카메라의 제한 사항으로 인해 카메라가 구형 렌즈라고 가정해야 합니다.
즉, 정사각형 픽셀을 가지며 광학적 중심이 카메라 눈 중앙에 위치해야 합니다.
입력 내재 행렬에서 이 가정이 성립되지 않으면 카메라가 올바르게 설정되지 않습니다.

* **매개변수:**
  * **matrices** – 카메라의 내재 행렬. 형태는 (N, 3, 3).
  * **focal_length** – 픽셀 크기를 계산하는 데 사용되는 원근 초점 거리(센티미터). 기본값은 None. None인 경우,
    focal_length은 1 / width로 계산됩니다.
  * **env_ids** – 조작할 센서 ID. 기본값은 None이며, 모든 센서 인덱스를 의미합니다.

#### set_world_poses(positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, orientations: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, convention: Literal['opengl', 'ros', 'world'] = 'ros')

지정된 규칙에 따라 세계 좌표계 기준 카메라의 자세를 설정합니다.

다양한 분야에서 카메라 방향에 서로 다른 규칙을 사용하므로, 이 메서드를 사용하면 지정된 규칙에 따라 카메라 자세를 설정할 수 있습니다.
가능한 규칙은 다음과 같습니다:

- `"opengl"` - 전방 축: -Z - 상방 축: +Y - OpenGL(Usd.Camera) 규칙에 따라 오프셋이 적용됨
- `"ros"`    - 전방 축: +Z - 상방 축: -Y - ROS 규칙에 따라 오프셋이 적용됨
- `"world"`  - 전방 축: +X - 상방 축: +Z - 월드 프레임 규칙에 따라 오프셋이 적용됨

자세한 내용은 `isaaclab.sensors.camera.utils.convert_camera_frame_orientation_convention()`을 참조하세요.

* **매개변수:**
  * **positions** – 데카르트 좌표(미터 단위). 형태는 (N, 3).
    기본값은 None이며, 이 경우 카메라 위치가 변경되지 않음.
  * **orientations** – (w, x, y, z) 형태의 쿼터니언 방향. 형태는 (N, 4).
    기본값은 None이며, 이 경우 카메라 방향이 변경되지 않음.
  * **env_ids** – 조작할 센서 ID. 기본값은 None이며, 모든 센서 인덱스를 의미함.
  * **convention** – 자세가 제공되는 규칙. 기본값은 "ros".
* **예외:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 카메라 프리미가 설정되지 않은 경우. 먼저 `initialize()` 메서드를 호출해야 함.

#### set_world_poses_from_view(eyes: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), targets: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

눈 위치와 응시 대상 위치로부터 카메라의 자세를 설정합니다.

* **매개변수:**
  * **eyes** – 카메라 눈의 위치. 형태는 (N, 3).
  * **targets** – 바라볼 대상 위치. 형태는 (N, 3).
  * **env_ids** – 조작할 센서 ID. 기본값은 None이며, 모든 센서 인덱스를 의미함.
* **예외:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 카메라 프리미가 설정되지 않은 경우. 먼저 `initialize()` 메서드를 호출해야 함.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 스테이지 상축이 "Y" 또는 "Z"가 아닌 경우.

### *class* isaaclab.sensors.TiledCameraCfg

Bases: [`CameraCfg`](#isaaclab.sensors.CameraCfg)

타일 기반 렌더링 카메라 센서의 구성입니다.

**클래스:**

| [`OffsetCfg`](#isaaclab.sensors.TiledCameraCfg.OffsetCfg)   | 센서 프레임과 센서 부모 프레임 사이의 오프셋 자세.   |
|-------------------------------------------------------------|-------------------------------------------------------------------------|

**속성:**

| [`prim_path`](#isaaclab.sensors.TiledCameraCfg.prim_path)                                                 | 센서의 프라임 경로(또는 표현식).                                  |
|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| [`update_period`](#isaaclab.sensors.TiledCameraCfg.update_period)                                         | 센서 버퍼의 업데이트 주기(초 단위).                         |
| [`history_length`](#isaaclab.sensors.TiledCameraCfg.history_length)                                       | 센서 버퍼에 저장할 과거 프레임 수.                     |
| [`debug_vis`](#isaaclab.sensors.TiledCameraCfg.debug_vis)                                                 | 센서 시각화 여부.                                          |
| [`offset`](#isaaclab.sensors.TiledCameraCfg.offset)                                                       | 센서 프레임과 센서 부모 프레임 사이의 오프셋 자세.     |
| [`spawn`](#isaaclab.sensors.TiledCameraCfg.spawn)                                                         | 에셋의 스폰 구성.                                        |
| [`depth_clipping_behavior`](#isaaclab.sensors.TiledCameraCfg.depth_clipping_behavior)                     | 최대값을 초과하는 값에 대한 카메라의 클리핑 동작.     |
| [`data_types`](#isaaclab.sensors.TiledCameraCfg.data_types)                                               | 카메라에 활성화할 센서 이름/유형 목록.                      |
| [`width`](#isaaclab.sensors.TiledCameraCfg.width)                                                         | 픽셀 단위의 이미지 너비.                                             |
| [`height`](#isaaclab.sensors.TiledCameraCfg.height)                                                       | 픽셀 단위의 이미지 높이.                                            |
| [`update_latest_camera_pose`](#isaaclab.sensors.TiledCameraCfg.update_latest_camera_pose)                 | 카메라 데이터를 가져올 때 최신 카메라 자세를 업데이트할지 여부. |
| [`semantic_filter`](#isaaclab.sensors.TiledCameraCfg.semantic_filter)                                     | 시맨틱 필터 프레디케이트를 지정하는 문자열 또는 리스트.                |
| [`colorize_semantic_segmentation`](#isaaclab.sensors.TiledCameraCfg.colorize_semantic_segmentation)       | 시맨틱 세그멘테이션 이미지를 색상화할지 여부.                     |
| [`colorize_instance_id_segmentation`](#isaaclab.sensors.TiledCameraCfg.colorize_instance_id_segmentation) | 인스턴스 ID 세그멘테이션 이미지를 색상화할지 여부.                  |
| [`colorize_instance_segmentation`](#isaaclab.sensors.TiledCameraCfg.colorize_instance_segmentation)       | 인스턴스 ID 세그멘테이션 이미지를 색상화할지 여부.                  |
| [`semantic_segmentation_mapping`](#isaaclab.sensors.TiledCameraCfg.semantic_segmentation_mapping)         | 시맨틱을 특정 색상에 매핑하는 딕셔너리                          |

#### *class* OffsetCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

센서의 프레임과 센서 부모 프레임 사이의 오프셋 자세.

**속성:**

| [`pos`](#isaaclab.sensors.TiledCameraCfg.OffsetCfg.pos)               | 부모 프레임에 대한 평행 이동.                                   |
|-----------------------------------------------------------------------|------------------------------------------------------|
| [`rot`](#isaaclab.sensors.TiledCameraCfg.OffsetCfg.rot)               | 부모 프레임에 대한 쿼터니언 회전(w, x, y, z).              |
| [`convention`](#isaaclab.sensors.TiledCameraCfg.OffsetCfg.convention) | 프레임 오프셋이 적용되는 규칙. |

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임에 대한 평행 이동. 기본값은 (0.0, 0.0, 0.0).

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임에 대한 쿼터니언 회전(w, x, y, z). 기본값은 (1.0, 0.0, 0.0, 0.0).

#### convention *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['opengl', 'ros', 'world']*

프레임 오프셋이 적용되는 규칙. 기본값은 "ros".

- `"opengl"` - 전방 축: `-Z` - 상방 축: `+Y` - OpenGL(Usd.Camera) 규칙에 따라 오프셋이 적용됨.
- `"ros"`    - 전방 축: `+Z` - 상방 축: `-Y` - ROS 규칙에 따라 오프셋이 적용됨.
- `"world"`  - 전방 축: `+X` - 상방 축: `+Z` - 월드 프레임 규칙에 따라 오프셋이 적용됨.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 프라임 경로(또는 표현식).

#### 참고
표현식에 환경 이름 공간 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 이름 공간으로 대체됩니다.

예: `{ENV_REGEX_NS}/Robot/sensor`는 `/World/envs/env_.*/Robot/sensor`로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼의 업데이트 주기(초 단위). 기본값은 0.0(매 단계 업데이트).

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수. 기본값은 0이며, 이는 현재 데이터만 저장(히스토리 없음)을 의미합니다.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서를 시각화할지 여부입니다. 기본값은 False입니다.

#### offset *: [OffsetCfg](#isaaclab.sensors.TiledCameraCfg.OffsetCfg)*

센서 프레임의 센서 부모 프레임에 대한 오프셋 포즈입니다. 기본값은 항등 변환입니다.

#### NOTE
부모 프레임은 센서가 연결된 프레임입니다. 예를 들어, 경로 `/World/envs/env_0/Robot/Camera`에 있는 카메라의 부모 프레임은 `/World/envs/env_0/Robot`입니다.

#### spawn *: [PinholeCameraCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.sensors.PinholeCameraCfg) | [FisheyeCameraCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.sensors.FisheyeCameraCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

에셋의 스폰 구성입니다.

None이면, 에셋이 프라임을 스폰하지 않습니다. 대신 에셋이 장면에 이미 존재한다고 가정합니다.

#### depth_clipping_behavior *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['max', 'zero', 'none']*

최대값을 초과하는 값에 대한 카메라의 클리핑 동작입니다. 기본값은 "none"입니다.

- `"max"`: 값이 최대값으로 클리핑됩니다.
- `"zero"`: 값이 0으로 클리핑됩니다.
- `"none`: 클리핑이 적용되지 않습니다. 값은 `inf`로 반환됩니다.

#### data_types *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

카메라에 활성화할 센서 이름/타입 목록입니다. 기본값은 [“rgb”]입니다.

사용 가능한 데이터 타입 목록은 [`Camera`](#isaaclab.sensors.Camera) 클래스를 참조하십시오.

#### width *: [int](https://docs.python.org/3/library/functions.html#int)*

픽셀 단위의 이미지 너비입니다.

#### height *: [int](https://docs.python.org/3/library/functions.html#int)*

픽셀 단위의 이미지 높이입니다.

#### update_latest_camera_pose *: [bool](https://docs.python.org/3/library/functions.html#bool)*

카메라 데이터를 가져올 때 최신 카메라 포즈를 업데이트할지 여부입니다. 기본값은 False입니다.

True이면, 최신 카메라 포즈가 카메라 데이터에 업데이트되며, `XformPrimView` 사용으로 인해 성능이 느려집니다.
False이면, 초기화 시 카메라 포즈가 반환됩니다.

#### semantic_filter *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

시맨틱 필터 프레디케이트를 지정하는 문자열 또는 문자열 목록입니다. 기본값은 `"*:*"`입니다.

문자열인 경우, (시맨틱 타입, 레이블)의 합동 정규형이어야 합니다. 예를 들어:

* `"typeA : labelA & !labelB | labelC , typeB: labelA ; typeC: labelE"`:
  시맨틱 타입이 "typeA"이고 레이블이 "labelA"이면서 "labelB"가 아닌 프라임 또는 레이블이 "labelC"인 모든 프라임.
  또한, 시맨틱 타입이 "typeB"이고 레이블이 "labelA"인 프라임 또는 시맨틱 타입이 "typeC"이고 레이블이 "labelE"인 모든 프라임.
* `"typeA : * ; * : labelA"`: 시맨틱 타입이 "typeA"이거나 레이블이 "labelA"인 모든 프라임

문자열 목록인 경우, 각 문자열은 시맨틱 타입이어야 합니다. 지정된 타입의 시맨틱스를 가진 프라임에 대한 세그멘테이션이 검색됩니다. 예를 들어, 목록이 ["class"]이면, 시맨틱 타입이 "class"인 프라임에 대한 세그멘테이션만 검색됩니다.

#### SEE ALSO
시맨틱 필터에 대한 자세한 내용은 [Replicator Semantics Schema Editor](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/semantics_schema_editor.html#semantics-filtering) 문서를 참조하십시오.

#### colorize_semantic_segmentation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

시맨틱 세그멘테이션 이미지를 색상화할지 여부입니다. 기본값은 True입니다.

True이면, 시맨틱 세그멘테이션이 시맨틱 ID를 색상에 매핑하여 이미지로 변환되고 `uint8` 4채널 배열로 반환됩니다. False이면, 출력은 `int32` 배열로 반환됩니다.

#### colorize_instance_id_segmentation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

인스턴스 ID 세그멘테이션 이미지를 색상화할지 여부입니다. 기본값은 True입니다.

True이면, 인스턴스 ID 세그멘테이션이 인스턴스 ID를 색상에 매핑하여 이미지로 변환되고 `uint8` 4채널 배열로 반환됩니다. False이면, 출력은 `int32` 배열로 반환됩니다.

#### colorize_instance_segmentation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

인스턴스 ID 세그멘테이션 이미지를 색상화할지 여부입니다. 기본값은 True입니다.

True이면, 인스턴스 세그멘테이션이 인스턴스 ID를 색상에 매핑하여 이미지로 변환되고 `uint8` 4채널 배열로 반환됩니다. False이면, 출력은 `int32` 배열로 반환됩니다.

#### semantic_segmentation_mapping *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)*

시맨틱을 특정 색상에 매핑하는 딕셔너리

예를 들어:

```python
{
    "class:cube_1": (255, 36, 66, 255),
    "class:cube_2": (255, 184, 48, 255),
    "class:cube_3": (55, 255, 139, 255),
    "class:table": (255, 237, 218, 255),
    "class:ground": (100, 100, 100, 255),
    "class:robot": (61, 178, 255, 255),
}
```

## Contact Sensor

### *class* isaaclab.sensors.ContactSensor

Bases: [`SensorBase`](#isaaclab.sensors.SensorBase)

접촉을 보고하는 센서입니다.

접촉 센서는 월드 프레임에서 강체의 법선 접촉 힘을 보고합니다. [PhysX ContactReporter](https://docs.omniverse.nvidia.com/kit/docs/omni_usd_schema_physics/104.2/class_physx_schema_physx_contact_report_a_p_i.html) API를 강체에 활성화하여 의존합니다.

강체에 접촉 리포터를 활성화하려면, 에셋 스폰너 구성에서 `isaaclab.sim.spawner.RigidObjectSpawnerCfg.activate_contact_sensors`를 활성화해야 합니다. 이렇게 하면 에셋의 모든 강체에 접촉 리포터가 활성화됩니다.

이 센서는 [`ContactSensorCfg.filter_prim_paths_expr`](#isaaclab.sensors.ContactSensorCfg.filter_prim_paths_expr)를 사용하여 지정된 필터 패턴을 가진 강체 집합에 대한 접촉 힘을 보고하도록 구성할 수 있습니다. 이는 센서 강체와 장면의 특정 강체 집합 사이의 접촉 힘을 보고하고자 할 때 유용합니다. 데이터는 [`ContactSensorData.force_matrix_w`](#isaaclab.sensors.ContactSensorData.force_matrix_w)를 사용하여 액세스할 수 있습니다. 자세한 내용은 [RigidContact](https://docs.isaacsim.omniverse.nvidia.com/latest/py/source/extensions/isaacsim.core.api/docs/index.html#isaacsim.core.api.sensors.RigidContactView) 문서를 참조하십시오.

필터링된 접촉 힘의 보고는 일대다 방식으로만 가능합니다. 즉, 한 환경에서 하나의 센서 강체만 여러 강체에 필터링될 수 있습니다. 여러 센서 강체를 여러 강체에 필터링해야 하는 경우, 각 센서 강체에 대해 별도의 센서를 생성해야 합니다.

예를 들어, 로봇의 모든 발이 객체에만 접촉 힘을 보고하도록 하려는 경우를 들어 봅시다. 이 경우, [`ContactSensorCfg.prim_path`](#isaaclab.sensors.ContactSensorCfg.prim_path)와 [`ContactSensorCfg.filter_prim_paths_expr`](#isaaclab.sensors.ContactSensorCfg.filter_prim_paths_expr)에 `{ENV_REGEX_NS}/Robot/.*_FOOT`와 `{ENV_REGEX_NS}/Object`를 각각 설정해도 작동하지 않습니다. 대신 각 발에 대해 별도의 센서를 생성하고 해당 센서를 객체에 필터링해야 합니다.

**속성:**

| [`cfg`](#isaaclab.sensors.ContactSensor.cfg)                                                   | 구성 매개변수입니다.                             |
|------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`num_instances`](#isaaclab.sensors.ContactSensor.num_instances)                               | 센서 인스턴스 수입니다.                        |
| [`data`](#isaaclab.sensors.ContactSensor.data)                                                 | 센서 데이터입니다.                                     |
| [`num_bodies`](#isaaclab.sensors.ContactSensor.num_bodies)                                     | 접촉 센서가 연결된 강체 수입니다.           |
| [`body_names`](#isaaclab.sensors.ContactSensor.body_names)                                     | 접촉 센서가 연결된 강체의 순서가 지정된 이름 목록입니다.    |
| [`body_physx_view`](#isaaclab.sensors.ContactSensor.body_physx_view)                           | 캡처된 강체에 대한 뷰(PhysX)입니다.               |
| [`contact_physx_view`](#isaaclab.sensors.ContactSensor.contact_physx_view)                     | 강체에 대한 접촉 리포터 뷰(PhysX)입니다.             |
| [`device`](#isaaclab.sensors.ContactSensor.device)                                             | 계산에 사용되는 메모리 디바이스입니다.                            |
| [`has_debug_vis_implementation`](#isaaclab.sensors.ContactSensor.has_debug_vis_implementation) | 디버그 시각화가 구현되어 있는지 여부입니다. |
| [`is_initialized`](#isaaclab.sensors.ContactSensor.is_initialized)                             | 센서가 초기화되었는지 여부입니다.                             |

**메서드:**

| [`__init__`](#isaaclab.sensors.ContactSensor.__init__)(cfg)                                     | 접촉 센서 객체를 초기화합니다.                                       |
|-------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| [`reset`](#isaaclab.sensors.ContactSensor.reset)([env_ids])                                     | 센서 내부를 재설정합니다.                                                 |
| [`find_bodies`](#isaaclab.sensors.ContactSensor.find_bodies)(name_keys[, preserve_order])       | 이름 키를 기반으로 어티큘레이션에서 강체를 찾습니다.                      |
| [`compute_first_contact`](#isaaclab.sensors.ContactSensor.compute_first_contact)(dt[, abs_tol]) | 지난 `dt` 초 내에 접촉을 수립한 강체가 있는지 확인합니다. |
| [`compute_first_air`](#isaaclab.sensors.ContactSensor.compute_first_air)(dt[, abs_tol])         | 지난 `dt` 초 이내에 접촉이 끊어진 신체를 확인합니다.      |
| [`set_debug_vis`](#isaaclab.sensors.ContactSensor.set_debug_vis)(debug_vis)                     | 센서 데이터를 시각화할지 여부를 설정합니다.                                   |

#### cfg *: [ContactSensorCfg](#isaaclab.sensors.ContactSensorCfg)*

구성 파라미터입니다.

#### \_\_init_\_(cfg: [ContactSensorCfg](#isaaclab.sensors.ContactSensorCfg))

접촉 센서 객체를 초기화합니다.

* **Parameters:**
  **cfg** – 구성 파라미터입니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 인스턴스 수입니다.

이 값은 환경당 센서 수에 환경 수를 곱한 값과 같습니다.

#### *property* data *: [ContactSensorData](#isaaclab.sensors.ContactSensorData)*

센서 데이터입니다.

이 속성은 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 방지하기 위함입니다.

이 속성에 접근할 때 센서를 업데이트하려면 센서 구현에서 다음 코드 스니펫을 사용할 수 있습니다:

```python
# update sensors if needed
self._update_outdated_buffers()
# return the data (where `_data` is the data for the sensor)
return self._data
```

#### *property* num_bodies *: [int](https://docs.python.org/3/library/functions.html#int)*

접촉 센서가 부착된 신체 수입니다.

#### *property* body_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

접촉 센서가 부착된 신체의 순서가 지정된 이름입니다.

#### *property* body_physx_view *: omni.physics.tensors.impl.api.RigidBodyView*

캡처된 강성 신체(PhysX)에 대한 뷰입니다.

#### NOTE
이 뷰를 사용할 때 주의하세요. 특정 방식으로 텐서를 처리해야 합니다.

#### *property* contact_physx_view *: omni.physics.tensors.impl.api.RigidContactView*

신체(PhysX)에 대한 접촉 리포터 뷰입니다.

#### NOTE
이 뷰를 사용할 때 주의하세요. 특정 방식으로 텐서를 처리해야 합니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서 내부 상태를 재설정합니다.

* **Parameters:**
  **env_ids** – 재설정할 센서 아이디입니다. 기본값은 None입니다.

#### find_bodies(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

이름 키를 기반으로 아티큘레이션에서 신체를 찾습니다.

* **Parameters:**
  * **name_keys** – 신체 이름과 일치하는 정규식 또는 정규식 목록입니다.
  * **preserve_order** – 출력에서 이름 키의 순서를 보존할지 여부입니다. 기본값은 False입니다.
* **Returns:**
  신체 인덱스와 이름을 포함하는 리스트들의 튜플입니다.

#### compute_first_contact(dt: [float](https://docs.python.org/3/library/functions.html#float), abs_tol: [float](https://docs.python.org/3/library/functions.html#float) = 1e-08) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

지난 `dt` 초 이내에 접촉이 수립된 신체를 확인합니다.

이 함수는 현재 접촉 시간을 지정된 시간 기간과 비교하여 신체가 지난 `dt` 초 이내에 접촉이 수립되었는지 확인합니다. 접촉 시간이 지정된 시간 기간보다 작다면, 해당 신체는 접촉 중인 것으로 간주됩니다.

#### NOTE
이 함수는 `dt`가 센서 업데이트 시간-스텝의 배수라고 가정합니다. 즉, $dt / dt_sensor = n$이며, 여기서 $n$은 자연수입니다. 이는 물리 또는 환경 스텝 시간-스텝으로 센서가 업데이트되고, 환경 스텝 시간-스텝으로 센서가 읽히는 경우 항상 성립합니다.

* **Parameters:**
  * **dt** – 접촉이 수립된 이후 경과한 시간 기간입니다.
  * **abs_tol** – 비교에 사용할 절대 허용 오차입니다.
* **Returns:**
  지난 `dt` 초 이내에 접촉이 수립된 신체를 나타내는 불리언 텐서입니다. 형태는 (N, B)이며, 여기서 N은 센서 수, B는 각 센서당 신체 수입니다.
* **Raises:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 센서가 접촉 시간을 추적하도록 구성되지 않은 경우 발생합니다.

#### compute_first_air(dt: [float](https://docs.python.org/3/library/functions.html#float), abs_tol: [float](https://docs.python.org/3/library/functions.html#float) = 1e-08) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

지난 `dt` 초 이내에 접촉이 끊어진 신체를 확인합니다.

이 함수는 현재 공중 시간을 지정된 시간 기간과 비교하여 신체가 지난 `dt` 초 이내에 접촉이 끊어졌는지 확인합니다. 공중 시간이 지정된 시간 기간보다 작다면, 해당 신체는 접촉 중이 아닌 것으로 간주됩니다.

#### NOTE
이 함수는 `dt`가 센서 업데이트 시간-스텝의 배수라고 가정합니다. 즉, $dt / dt_sensor = n$이며, 여기서 $n$은 자연수입니다. 이는 물리 또는 환경 스텝 시간-스텝으로 센서가 업데이트되고, 환경 스텝 시간-스텝으로 센서가 읽히는 경우 항상 성립합니다.

* **Parameters:**
  * **dt** – 접촉이 끊어진 이후 경과한 시간 기간입니다.
  * **abs_tol** – 비교에 사용할 절대 허용 오차입니다.
* **Returns:**
  지난 `dt` 초 이내에 접촉이 끊어진 신체를 나타내는 불리언 텐서입니다. 형태는 (N, B)이며, 여기서 N은 센서 수, B는 각 센서당 신체 수입니다.
* **Raises:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 센서가 접촉 시간을 추적하도록 구성되지 않은 경우 발생합니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 디바이스입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서에 디버그 시각화가 구현되어 있는지 여부입니다.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부입니다.

센서가 초기화되었다면 True를 반환하고, 그렇지 않다면 False를 반환합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터를 시각화할지 여부를 설정합니다.

* **Parameters:**
  **debug_vis** – 센서 데이터를 시각화할지 여부입니다.
* **Returns:**
  디버그 시각화가 성공적으로 설정되었는지 여부입니다. 센서가 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

### *class* isaaclab.sensors.ContactSensorData

접촉 보고 센서를 위한 데이터 컨테이너입니다.

**Attributes:**

| [`pos_w`](#isaaclab.sensors.ContactSensorData.pos_w)                                   | 월드 프레임에서의 센서 원점 위치입니다.                                                    |
|----------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| [`contact_pos_w`](#isaaclab.sensors.ContactSensorData.contact_pos_w)                   | 센서 신체와 필터 프림 사이의 접촉점들의 월드 프레임에서의 평균 위치입니다.   |
| [`friction_forces_w`](#isaaclab.sensors.ContactSensorData.friction_forces_w)           | 센서 신체와 필터 프림 사이의 마찰 힘의 월드 프레임에서의 합입니다.                   |
| [`quat_w`](#isaaclab.sensors.ContactSensorData.quat_w)                                 | 월드 프레임에서의 센서 원점 방향을 쿼터니언(w, x, y, z)으로 나타낸 값입니다.                      |
| [`net_forces_w`](#isaaclab.sensors.ContactSensorData.net_forces_w)                     | 월드 프레임에서의 순수 정규 접촉 힘입니다.                                                    |
| [`net_forces_w_history`](#isaaclab.sensors.ContactSensorData.net_forces_w_history)     | 월드 프레임에서의 순수 정규 접촉 힘 기록입니다.                                                    |
| [`force_matrix_w`](#isaaclab.sensors.ContactSensorData.force_matrix_w)                 | 센서 신체와 필터 신체 사이의 필터링된 정규 접촉 힘의 월드 프레임에서의 행렬입니다. |
| [`force_matrix_w_history`](#isaaclab.sensors.ContactSensorData.force_matrix_w_history) | 센서 신체와 필터 신체 사이의 필터링된 정규 접촉 힘의 월드 프레임에서의 기록입니다. |
| [`last_air_time`](#isaaclab.sensors.ContactSensorData.last_air_time)                   | 마지막 접촉 이전까지 공중에 있었던 시간(초).                                            |
| [`current_air_time`](#isaaclab.sensors.ContactSensorData.current_air_time)             | 마지막 분리 이후 공중에 있었던 시간(초).                                              |
| [`last_contact_time`](#isaaclab.sensors.ContactSensorData.last_contact_time)           | 마지막 분리 이전까지 접촉 상태였던 시간(초).                                             |
| [`current_contact_time`](#isaaclab.sensors.ContactSensorData.current_contact_time)     | 마지막 접촉 이후 접촉 상태였던 시간(초).                                             |

#### pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

센서 원점의 월드 프레임에서의 위치입니다.

모양은 (N, 3)이며, 여기서 N은 센서의 수입니다.

#### 주의
[`ContactSensorCfg.track_pose`](#isaaclab.sensors.ContactSensorCfg.track_pose)가 False인 경우, 이 양은 None입니다.

#### contact_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

센서 본체와 필터 프림 간 접촉점들의 월드 프레임에서의 위치 평균입니다.

모양은 (N, B, M, 3)이며, 여기서 N은 센서 수, B는 각 센서의 본체 수, M은 필터링된 본체 수입니다.

접촉되지 않은 충돌 쌍은 NaN으로 반환됩니다.

#### 주의
* [`ContactSensorCfg.track_contact_points`](#isaaclab.sensors.ContactSensorCfg.track_contact_points)가 False인 경우, 이 양은 None입니다.
* [`ContactSensorCfg.track_contact_points`](#isaaclab.sensors.ContactSensorCfg.track_contact_points)가 True인 경우, 다음 조건 중 하나라도 참이면 ValueError가 발생합니다:
  * [`ContactSensorCfg.filter_prim_paths_expr`](#isaaclab.sensors.ContactSensorCfg.filter_prim_paths_expr)가 비어 있습니다.
  * `ContactSensorCfg.max_contact_data_per_prim`이 지정되지 않았거나 1 미만입니다. 이 경우 계산되지 않습니다.

#### friction_forces_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

센서 본체와 필터 프림 간 마찰 힘의 합(월드 프레임 기준)입니다.

모양은 (N, B, M, 3)이며, 여기서 N은 센서 수, B는 각 센서의 본체 수, M은 필터링된 본체 수입니다.

접촉되지 않은 충돌 쌍은 NaN으로 반환됩니다.

#### 주의
* [`ContactSensorCfg.track_friction_forces`](#isaaclab.sensors.ContactSensorCfg.track_friction_forces)가 False인 경우, 이 양은 None입니다.
* [`ContactSensorCfg.track_friction_forces`](#isaaclab.sensors.ContactSensorCfg.track_friction_forces)가 True인 경우, 다음 조건 중 하나라도 참이면 ValueError가 발생합니다:
  * [`ContactSensorCfg.filter_prim_paths_expr`](#isaaclab.sensors.ContactSensorCfg.filter_prim_paths_expr)가 비어 있습니다.
  * `ContactSensorCfg.max_contact_data_per_prim`이 지정되지 않았거나 1 미만입니다.

#### quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

센서 원점의 월드 프레임에서의 쿼터니언(w, x, y, z) 방향입니다.

모양은 (N, 4)이며, 여기서 N은 센서 수입니다.

#### 주의
[`ContactSensorCfg.track_pose`](#isaaclab.sensors.ContactSensorCfg.track_pose)가 False인 경우, 이 양은 None입니다.

#### net_forces_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

월드 프레임에서의 순 수직 접촉 힘입니다.

모양은 (N, B, 3)이며, 여기서 N은 센서 수, B는 각 센서의 본체 수입니다.

#### 주의
이 양은 센서 본체에 작용하는 순 수직 접촉 힘의 합이며, 접촉 힘(법선 + 접선) 전체와 혼동해서는 안 됩니다.

#### net_forces_w_history *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

월드 프레임에서의 순 수직 접촉 힘입니다.

모양은 (N, T, B, 3)이며, 여기서 N은 센서 수, T는 구성된 히스토리 길이, B는 각 센서의 본체 수입니다.

히스토리 차원에서 첫 번째 인덱스는 가장 최근이고, 마지막 인덱스는 가장 오래된 데이터를 나타냅니다.

#### 주의
이 양은 센서 본체에 작용하는 순 수직 접촉 힘의 합이며, 접촉 힘(법선 + 접선) 전체와 혼동해서는 안 됩니다.

#### force_matrix_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

월드 프레임 기준에서 센서 본체와 필터링된 본체 사이의 법선 접촉 힘입니다.

모양은 (N, B, M, 3)이며, 여기서 N은 센서 수, B는 각 센서의 본체 수, M은 필터링된 본체 수입니다.

#### 주의
[`ContactSensorCfg.filter_prim_paths_expr`](#isaaclab.sensors.ContactSensorCfg.filter_prim_paths_expr)가 비어 있는 경우, 이 양은 None입니다.

#### force_matrix_w_history *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

월드 프레임 기준에서 센서 본체와 필터링된 본체 사이의 법선 접촉 힘입니다.

모양은 (N, T, B, M, 3)이며, 여기서 N은 센서 수, T는 구성된 히스토리 길이, B는 각 센서의 본체 수, M은 필터링된 본체 수입니다.

히스토리 차원에서 첫 번째 인덱스는 가장 최근이고, 마지막 인덱스는 가장 오래된 데이터를 나타냅니다.

#### 주의
[`ContactSensorCfg.filter_prim_paths_expr`](#isaaclab.sensors.ContactSensorCfg.filter_prim_paths_expr)가 비어 있는 경우, 이 양은 None입니다.

#### last_air_time *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

마지막 접촉 이전까지 공중에 머문 시간(초 단위)입니다.

모양은 (N, B)이며, 여기서 N은 센서 수, B는 각 센서의 본체 수입니다.

#### 주의
[`ContactSensorCfg.track_air_time`](#isaaclab.sensors.ContactSensorCfg.track_air_time)가 False인 경우, 이 양은 None입니다.

#### current_air_time *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

마지막 분리 이후 공중에 머문 시간(초 단위)입니다.

모양은 (N, B)이며, 여기서 N은 센서 수, B는 각 센서의 본체 수입니다.

#### 주의
[`ContactSensorCfg.track_air_time`](#isaaclab.sensors.ContactSensorCfg.track_air_time)가 False인 경우, 이 양은 None입니다.

#### last_contact_time *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

마지막 분리 이전까지 접촉 상태였던 시간(초 단위)입니다.

모양은 (N, B)이며, 여기서 N은 센서 수, B는 각 센서의 본체 수입니다.

#### 주의
[`ContactSensorCfg.track_air_time`](#isaaclab.sensors.ContactSensorCfg.track_air_time)가 False인 경우, 이 양은 None입니다.

#### current_contact_time *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

마지막 접촉 이후 접촉 상태였던 시간(초 단위)입니다.

모양은 (N, B)이며, 여기서 N은 센서 수, B는 각 센서의 본체 수입니다.

#### 주의
[`ContactSensorCfg.track_air_time`](#isaaclab.sensors.ContactSensorCfg.track_air_time)가 False인 경우, 이 양은 None입니다.

### *class* isaaclab.sensors.ContactSensorCfg

기반 클래스: [`SensorBaseCfg`](#isaaclab.sensors.SensorBaseCfg)

접촉 센서의 구성입니다.

**속성:**

| [`track_pose`](#isaaclab.sensors.ContactSensorCfg.track_pose)                                           | 센서 원점의 자세를 추적할지 여부입니다.                                                          |
|---------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| [`track_contact_points`](#isaaclab.sensors.ContactSensorCfg.track_contact_points)                       | 접촉점 위치를 추적할지 여부입니다.                                                              |
| [`track_friction_forces`](#isaaclab.sensors.ContactSensorCfg.track_friction_forces)                     | 접촉점에서 마찰 힘을 추적할지 여부입니다.                                                |
| [`max_contact_data_count_per_prim`](#isaaclab.sensors.ContactSensorCfg.max_contact_data_count_per_prim) | 센서의 모든 배치에 걸쳐 추적할 최대 접촉 데이터 수입니다.                          |
| [`prim_path`](#isaaclab.sensors.ContactSensorCfg.prim_path)                                             | 센서의 프림 경로(또는 표현식)입니다.                                                                   |
| [`update_period`](#isaaclab.sensors.ContactSensorCfg.update_period)                                     | 센서 버퍼의 업데이트 주기(초 단위)입니다.                                                          |
| [`history_length`](#isaaclab.sensors.ContactSensorCfg.history_length)                                   | 센서 버퍼에 저장할 과거 프레임 수입니다.                                                      |
| [`debug_vis`](#isaaclab.sensors.ContactSensorCfg.debug_vis)                                             | 센서 시각화 여부입니다.                                                                           |
| [`track_air_time`](#isaaclab.sensors.ContactSensorCfg.track_air_time)                                   | 본체의 공중/접촉 시간(접촉 사이 간 시간)을 추적할지 여부입니다.                               |
| [`force_threshold`](#isaaclab.sensors.ContactSensorCfg.force_threshold)                                 | 두 물체가 충돌 중인지 판단하기 위한 접촉 힘의 노름 기준값입니다. |
| [`filter_prim_paths_expr`](#isaaclab.sensors.ContactSensorCfg.filter_prim_paths_expr)                   | 접촉을 필터링할 프림 경로(또는 표현식) 목록입니다.                                      |
| [`visualizer_cfg`](#isaaclab.sensors.ContactSensorCfg.visualizer_cfg)                                   | 시각화 마커 구성 객체입니다.                                                    |

#### track_pose *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서 원점의 포즈를 추적할지 여부입니다. 기본값은 False입니다.

#### track_contact_points *: [bool](https://docs.python.org/3/library/functions.html#bool)*

접촉 점 위치를 추적할지 여부입니다. 기본값은 False입니다.

#### track_friction_forces *: [bool](https://docs.python.org/3/library/functions.html#bool)*

접촉 점에서의 마찰 힘을 추적할지 여부입니다. 기본값은 False입니다.

#### max_contact_data_count_per_prim *: [int](https://docs.python.org/3/library/functions.html#int)*

추적할 모든 센서 배치에서의 최대 접촉 수입니다. 기본값은 4입니다.

이 매개변수는 모든 신체 및 환경에서 시뮬레이션의 전체 최대 카운트를 설정합니다. 허용되는 총 접촉 수는 max_contact_data_count_per_prim\*num_envs\*num_sensor_bodies입니다.

#### NOTE
환경이 매우 접촉이 풍부한 경우, 메모리 오류 및 정확하지 않은 측정으로 이어질 수 있는 접촉 데이터 손실을 방지하기 위해 이 매개변수를 늘리는 것이 좋습니다.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 Prim 경로(또는 표현식)입니다.

#### NOTE
표현식에 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot/sensor`는 `/World/envs/env_.*/Robot/sensor`로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼의 업데이트 주기(초 단위)입니다. 기본값은 0.0(매 단계마다 업데이트)입니다.

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수입니다. 기본값은 0이며, 이는 현재 데이터만 저장됨(히스토리 없음)을 의미합니다.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서 시각화 여부입니다. 기본값은 False입니다.

#### track_air_time *: [bool](https://docs.python.org/3/library/functions.html#bool)*

신체의 공중/접촉 시간(접촉 사이의 시간)을 추적할지 여부입니다. 기본값은 False입니다.

#### force_threshold *: [float](https://docs.python.org/3/library/functions.html#float)*

두 신체가 충돌 중인지 여부를 판단하기 위한 접촉 힘의 노름 기준값입니다.

이 값은 [`track_air_time`](#isaaclab.sensors.ContactSensorCfg.track_air_time)가 True인 경우, 모드 지속 시간(접촉 또는 공중 시간) 추적에만 사용됩니다.

#### filter_prim_paths_expr *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

연락을 필터링할 프리미티브 경로(또는 표현식) 목록입니다. 기본값은 빈 리스트이며, 이 경우 필터링이 적용되지 않습니다.

접촉 센서는 [`prim_path`](#isaaclab.sensors.ContactSensorCfg.prim_path)로 지정된 프리미티브와 장면 내 다른 프리미티브 사이의 접촉을 보고할 수 있습니다. 예를 들어, 로봇, 땅 평면, 물체가 포함된 장면에서 로봇 기반과 땅 평면 및 물체 사이의 개별 접촉 보고를 얻을 수 있습니다.

#### NOTE
목록의 표현식에 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Object`는 `/World/envs/env_.*/Object`로 대체됩니다.

#### ATTENTION
필터링된 접촉 보고는 센서 프리미티브 [`prim_path`](#isaaclab.sensors.ContactSensorCfg.prim_path)가 해당 환경에서 단일 프리미티브에 해당할 때만 작동합니다. 센서 프리미티브가 여러 프리미티브에 해당하는 경우, 필터링이 예상대로 작동하지 않을 수 있습니다. 자세한 내용은 `ContactSensor`를 확인하십시오.
track_contact_points가 True인 경우, filter_prim_paths_expr는 빈 리스트가 될 수 없습니다!

#### visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

시각화 마커 구성 객체입니다. 기본값은 CONTACT_SENSOR_MARKER_CFG입니다.

#### NOTE
이 속성은 디버그 시각화가 활성화된 경우에만 사용됩니다.

## 프레임 변환기

### *class* isaaclab.sensors.FrameTransformer

Bases: [`SensorBase`](#isaaclab.sensors.SensorBase)

프레임 변환을 보고하는 센서입니다.

이 클래스는 한 개 이상의 프레임(대상 프레임)에 대한 변환을 다른 프레임(원본 프레임)에 대해 보고하기 위한 인터페이스를 제공합니다. 원본 프레임은 사용자 지정 프레임 경로([`FrameTransformerCfg.prim_path`](#isaaclab.sensors.FrameTransformerCfg.prim_path))로 지정되며, 대상 프레임은 사용자 지정 프레임 경로 목록([`FrameTransformerCfg.target_frames`](#isaaclab.sensors.FrameTransformerCfg.target_frames))로 지정됩니다.

원본 프레임과 대상 프레임은 강체라고 가정됩니다. 대상 프레임의 원본 프레임에 대한 변환은 먼저 물리 엔진에서 원본 프레임과 대상 프레임의 변환을 추출한 다음, 두 변환 사이의 상대 변환을 계산하여 계산됩니다.

또한, 사용자는 원본 프레임 및 각 대상 프레임에 대한 오프셋을 지정할 수 있습니다. 이는 예를 들어 신체의 질량 중심을 기준으로 원하는 프레임의 변환을 지정하는 데 유용합니다.

이 센서를 사용하는 일반적인 예는 로봇 매니퓰레이터의 엔드 이펙터의 위치 및 방향을 추적하는 것입니다. 이 경우, 원본 프레임은 매니퓰레이터의 기본 프레임에 해당하는 신체가 되며, 대상 프레임은 엔드 이펙터에 해당하는 신체가 됩니다. 엔드 이펙터는 일반적으로 가상의 신체이므로, 사용자는 엔드 이펙터에서 매니퓰레이터의 신체까지의 오프셋을 지정해야 할 수 있습니다.

**속성:**

| [`cfg`](#isaaclab.sensors.FrameTransformer.cfg)                                                   | 구성 매개변수입니다.                             |
|---------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`data`](#isaaclab.sensors.FrameTransformer.data)                                                 | 센서 데이터입니다.                                     |
| [`num_bodies`](#isaaclab.sensors.FrameTransformer.num_bodies)                                     | 추적 중인 대상 신체의 수를 반환합니다.        |
| [`body_names`](#isaaclab.sensors.FrameTransformer.body_names)                                     | 추적 중인 대상 신체의 이름을 반환합니다.     |
| [`device`](#isaaclab.sensors.FrameTransformer.device)                                             | 계산에 사용되는 메모리 장치입니다.                            |
| [`has_debug_vis_implementation`](#isaaclab.sensors.FrameTransformer.has_debug_vis_implementation) | 센서에 디버그 시각화 구현이 있는지 여부입니다. |
| [`is_initialized`](#isaaclab.sensors.FrameTransformer.is_initialized)                             | 센서가 초기화되었는지 여부입니다.                        |
| [`num_instances`](#isaaclab.sensors.FrameTransformer.num_instances)                               | 센서 인스턴스 수입니다.                               |

**메서드:**

| [`__init__`](#isaaclab.sensors.FrameTransformer.__init__)(cfg)                               | 프레임 변환기 객체를 초기화합니다.               |
|----------------------------------------------------------------------------------------------|---------------------------------------------------------|
| [`reset`](#isaaclab.sensors.FrameTransformer.reset)([env_ids])                               | 센서 내부 상태를 재설정합니다.                            |
| [`find_bodies`](#isaaclab.sensors.FrameTransformer.find_bodies)(name_keys[, preserve_order]) | 이름 키를 기반으로 어티큘레이션에서 신체를 찾습니다. |
| [`set_debug_vis`](#isaaclab.sensors.FrameTransformer.set_debug_vis)(debug_vis)               | 센서 데이터 시각화 여부를 설정합니다.              |

#### cfg *: [FrameTransformerCfg](#isaaclab.sensors.FrameTransformerCfg)*

구성 매개변수입니다.

#### \_\_init_\_(cfg: [FrameTransformerCfg](#isaaclab.sensors.FrameTransformerCfg))

프레임 변환기 객체를 초기화합니다.

* **매개변수:**
  **cfg** – 구성 매개변수입니다.

#### *property* data *: [FrameTransformerData](#isaaclab.sensors.FrameTransformerData)*

센서에서 오는 데이터입니다.

이 속성은 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 피하기 위해 수행됩니다.

이 속성에 접근할 때 센서를 업데이트하려면 센서 구현에서 다음 코드 스니펫을 사용할 수 있습니다:

```python
# update sensors if needed
self._update_outdated_buffers()
# return the data (where `_data` is the data for the sensor)
return self._data
```

#### *property* num_bodies *: [int](https://docs.python.org/3/library/functions.html#int)*

추적 중인 대상 신체의 수를 반환합니다.

#### NOTE
다른 센서와의 일관성을 위해 사용되는 별칭입니다. 그렇지 않으면, `len(data.target_frame_names)`을 사용하여 대상 프레임 수에 접근하는 것이 권장됩니다.

#### *property* body_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

추적 중인 대상 신체의 이름을 반환합니다.

#### NOTE
다른 센서와의 일관성을 위해 사용되는 별칭입니다. 그렇지 않으면, `data.target_frame_names`을 사용하여 대상 프레임 이름에 접근하는 것이 권장됩니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서 내부 상태를 재설정합니다.

* **매개변수:**
  **env_ids** – 초기화할 센서 ID. 기본값은 None.

#### find_bodies(name_keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

이름 키를 기반으로 아티큘레이션에서 바디를 찾습니다.

* **매개변수:**
  * **name_keys** – 바디 이름을 일치시키는 정규 표현식 또는 정규 표현식 목록.
  * **preserve_order** – 출력에서 name_keys의 순서를 보존할지 여부. 기본값은 False.
* **반환값:**
  바디 인덱스와 이름을 포함하는 리스트들의 튜플.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 위한 메모리 디바이스.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서에 디버그 시각화가 구현되어 있는지 여부.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부.

센서가 초기화된 경우 True, 그렇지 않은 경우 False를 반환합니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서의 인스턴스 수.

이는 환경당 센서 수와 환경 수를 곱한 값과 같습니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 센서 데이터를 시각화할지 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 센서가 디버그 시각화를 지원하지 않는 경우 False.

### *class* isaaclab.sensors.FrameTransformerData

프레임 트랜스포머 센서용 데이터 컨테이너.

**속성:**

| [`target_frame_names`](#isaaclab.sensors.FrameTransformerData.target_frame_names)   | 대상 프레임 이름 (이것은 프레임 데이터가 순서대로 정리되는 순서를 나타냄).          |
|-------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| [`target_pos_source`](#isaaclab.sensors.FrameTransformerData.target_pos_source)     | 소스 프레임에 상대적인 대상 프레임(들)의 위치.                                 |
| [`target_quat_source`](#isaaclab.sensors.FrameTransformerData.target_quat_source)   | 소스 프레임에 상대적인 대상 프레임(들)의 방향 쿼터니언 (w, x, y, z).      |
| [`target_pos_w`](#isaaclab.sensors.FrameTransformerData.target_pos_w)               | 오프셋 후 대상 프레임(들)의 위치 (월드 프레임).                            |
| [`target_quat_w`](#isaaclab.sensors.FrameTransformerData.target_quat_w)             | 오프셋 후 대상 프레임(들)의 방향 쿼터니언 (w, x, y, z) (월드 프레임). |
| [`source_pos_w`](#isaaclab.sensors.FrameTransformerData.source_pos_w)               | 오프셋 후 소스 프레임의 위치 (월드 프레임).                               |
| [`source_quat_w`](#isaaclab.sensors.FrameTransformerData.source_quat_w)             | 오프셋 후 소스 프레임의 방향 쿼터니언 (w, x, y, z) (월드 프레임).    |

#### target_frame_names *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]* *= None*

대상 프레임 이름 (이것은 프레임 데이터가 순서대로 정리되는 순서를 나타냄).

프레임 이름은 [`FrameTransformerCfg.FrameCfg.name`](#isaaclab.sensors.FrameTransformerCfg.FrameCfg.name) 필드에서 해결됩니다.
정규 표현식 매칭으로 인해 프레임이 구성에서 정의된 순서를 반드시 따르지 않을 수 있습니다.

#### target_pos_source *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

소스 프레임에 상대적인 대상 프레임(들)의 위치.

모양은 (N, M, 3)이며, 여기서 N은 환경 수이고 M은 대상 프레임 수입니다.

#### target_quat_source *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

소스 프레임에 상대적인 대상 프레임(들)의 방향 쿼터니언 (w, x, y, z).

모양은 (N, M, 4)이며, 여기서 N은 환경 수이고 M은 대상 프레임 수입니다.

#### target_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

오프셋 후 대상 프레임(들)의 위치 (월드 프레임).

모양은 (N, M, 3)이며, 여기서 N은 환경 수이고 M은 대상 프레임 수입니다.

#### target_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

오프셋 후 대상 프레임(들)의 방향 쿼터니언 (w, x, y, z) (월드 프레임).

모양은 (N, M, 4)이며, 여기서 N은 환경 수이고 M은 대상 프레임 수입니다.

#### source_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

오프셋 후 소스 프레임의 위치 (월드 프레임).

모양은 (N, 3)이며, 여기서 N은 환경 수입니다.

#### source_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

오프셋 후 소스 프레임의 방향 쿼터니언 (w, x, y, z) (월드 프레임).

모양은 (N, 4)이며, 여기서 N은 환경 수입니다.

### *class* isaaclab.sensors.FrameTransformerCfg

Bases: [`SensorBaseCfg`](#isaaclab.sensors.SensorBaseCfg)

프레임 트랜스포머 센서의 구성.

**클래스:**

| [`FrameCfg`](#isaaclab.sensors.FrameTransformerCfg.FrameCfg)   | 좌표 프레임에 특정한 정보.   |
|----------------------------------------------------------------|-----------------------------------------------|

**속성:**

| [`update_period`](#isaaclab.sensors.FrameTransformerCfg.update_period)             | 센서 버퍼 업데이트 기간 (초 단위).           |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| [`history_length`](#isaaclab.sensors.FrameTransformerCfg.history_length)           | 센서 버퍼에 저장할 과거 프레임 수.       |
| [`debug_vis`](#isaaclab.sensors.FrameTransformerCfg.debug_vis)                     | 센서 시각화 여부.                            |
| [`prim_path`](#isaaclab.sensors.FrameTransformerCfg.prim_path)                     | 변환할 몸체의 프라임 경로 (소스 프레임). |
| [`source_frame_offset`](#isaaclab.sensors.FrameTransformerCfg.source_frame_offset) | 소스 프라임 프레임에서의 포즈 오프셋.                 |
| [`target_frames`](#isaaclab.sensors.FrameTransformerCfg.target_frames)             | 대상 프레임 목록.                                |
| [`visualizer_cfg`](#isaaclab.sensors.FrameTransformerCfg.visualizer_cfg)           | 시각화 마커용 구성 객체.     |

#### *class* FrameCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

좌표 프레임에 특정한 정보.

**속성:**

| [`prim_path`](#isaaclab.sensors.FrameTransformerCfg.FrameCfg.prim_path)   | 강체의 프라임 경로.    |
|---------------------------------------------------------------------------|-------------------------------------------------|
| [`name`](#isaaclab.sensors.FrameTransformerCfg.FrameCfg.name)             | 새로운 좌표 프레임에 대한 사용자 정의 이름. |
| [`offset`](#isaaclab.sensors.FrameTransformerCfg.FrameCfg.offset)         | 부모 프라임 프레임에서의 포즈 오프셋.     |

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

강체에 해당하는 프라임 경로.

예를 들어, “/Robot/.\*”는 “/Robot” 아래의 모든 프라임을 일치시킬 수 있는 정규 표현식 패턴일 수 있습니다.

즉, 소스 [`FrameTransformerCfg.prim_path`](#isaaclab.sensors.FrameTransformerCfg.prim_path)가 “/Robot/base”이고 대상 [`FrameTransformerCfg.FrameCfg.prim_path`](#isaaclab.sensors.FrameTransformerCfg.FrameCfg.prim_path)가 “/Robot/.\*”이면, 프레임 트랜스포머는 “/Robot” 아래의 모든 프라임의 포즈를 추적하며, 여기에는 “/Robot/base”도 포함됩니다 (소스 프레임 상대적으로 동일 포즈가 발생하더라도).

#### name *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

새로운 좌표 프레임에 대한 사용자 정의 이름. 기본값은 None.

None인 경우, 프라임 경로의 leaf에서 이름을 추출합니다.

#### offset *: [OffsetCfg](#isaaclab.sensors.OffsetCfg)*

부모 프라임 프레임에서의 포즈 오프셋.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼 업데이트 기간 (초 단위). 기본값은 0.0 (매 스텝마다 업데이트).

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수. 기본값은 0으로, 이는 현재 데이터만 저장됨을 의미함 (히스토리 없음).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서 시각화 여부. 기본값은 False.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

변환할 몸체의 프라임 경로 (소스 프레임).

#### source_frame_offset *: [OffsetCfg](#isaaclab.sensors.OffsetCfg)*

소스 프라임 프레임에서의 포즈 오프셋.

#### target_frames *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.sensors.frame_transformer.frame_transformer_cfg.FrameTransformerCfg.FrameCfg](#isaaclab.sensors.FrameTransformerCfg.FrameCfg)]*

타겟 프레임 목록입니다.

이를 통해 하나의 FrameTransformer가 여러 타겟 프라임을 처리할 수 있습니다. 예를 들어, 사족 보행 로봇에서 하나의 FrameTransformer를 사용하여 몸체 프레임 기준 각 발의 위치와 방향을 네 개의 프레임 오프셋으로 추적할 수 있습니다.

#### visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

시각화 마커를 위한 구성 객체입니다. 기본값은 FRAME_MARKER_CFG입니다.

#### NOTE
이 속성은 디버그 시각화가 활성화된 경우에만 사용됩니다.

### *class* isaaclab.sensors.OffsetCfg

하나의 프레임이 다른 프레임에 상대적인 오프셋 포즈입니다.

**Attributes:**

| [`pos`](#isaaclab.sensors.OffsetCfg.pos)   | 기준 프레임에 대한 위치 이동                     |
|--------------------------------------------|--------------------------------------------------|
| [`rot`](#isaaclab.sensors.OffsetCfg.rot)   | 기준 프레임에 대한 쿼터니언 회전 (w, x, y, z)    |

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

기준 프레임에 대한 위치 이동입니다. 기본값은 (0.0, 0.0, 0.0)입니다.

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

기준 프레임에 대한 쿼터니언 회전 (w, x, y, z)입니다. 기본값은 (1.0, 0.0, 0.0, 0.0)입니다.

## Ray-Cast 센서

### *class* isaaclab.sensors.RayCaster

Bases: [`SensorBase`](#isaaclab.sensors.SensorBase)

레이캐스팅 센서입니다.

레이캐스터는 장면 내의 메쉬와의 충돌을 감지하기 위해 광선 세트를 사용합니다. 광선은 센서의 로컬 좌표계에서 정의됩니다. 이 센서는 특정 광선 패턴을 사용하여 메쉬 세트와의 레이캐스트를 구성할 수 있습니다.

메쉬는 구성에 제공된 프리미티브 경로 목록에서 파싱됩니다. 이러한 메쉬는 워프 메쉬로 변환되어 warp_meshes 리스트에 저장됩니다. 레이캐스터는 구성에 제공된 광선 패턴을 사용하여 이 워프 메쉬에 레이캐스트를 수행합니다.

#### NOTE
현재는 정적 메쉬만 지원됩니다. 동적 메쉬를 지원하도록 워프 메쉬를 확장하는 작업은 진행 중입니다.

**Attributes:**

| [`cfg`](#isaaclab.sensors.RayCaster.cfg)                                                   | 구성 파라미터입니다.                                                  |
|--------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`meshes`](#isaaclab.sensors.RayCaster.meshes)                                             | 레이캐스팅용 워프 메쉬를 저장하는 딕셔너리이며, 모든 인스턴스 간에 공유됩니다. |
| [`num_instances`](#isaaclab.sensors.RayCaster.num_instances)                               | 센서의 인스턴스 수입니다.                                                      |
| [`data`](#isaaclab.sensors.RayCaster.data)                                                 | 센서 데이터입니다.                                                             |
| [`device`](#isaaclab.sensors.RayCaster.device)                                             | 연산에 사용되는 메모리 디바이스입니다.                                         |
| [`has_debug_vis_implementation`](#isaaclab.sensors.RayCaster.has_debug_vis_implementation) | 디버그 시각화가 구현되었는지 여부를 나타냅니다.                                |
| [`is_initialized`](#isaaclab.sensors.RayCaster.is_initialized)                             | 센서가 초기화되었는지 여부를 나타냅니다.                                       |

**Methods:**

| [`__init__`](#isaaclab.sensors.RayCaster.__init__)(cfg)                 | 레이캐스터 객체를 초기화합니다.         |
|-------------------------------------------------------------------------|----------------------------------------|
| [`reset`](#isaaclab.sensors.RayCaster.reset)([env_ids])                 | 센서 내부 상태를 재설정합니다.          |
| [`set_debug_vis`](#isaaclab.sensors.RayCaster.set_debug_vis)(debug_vis) | 센서 데이터 시각화 여부를 설정합니다.   |

#### cfg *: [RayCasterCfg](#isaaclab.sensors.RayCasterCfg)*

구성 파라미터입니다.

#### meshes *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), wp.Mesh]]* *= {}*

레이캐스팅용 워프 메쉬를 저장하는 딕셔너리이며, 모든 인스턴스 간에 공유됩니다.

키들은 메쉬의 프리미티브 경로에 해당하며, 값은 해당 워프 Mesh 객체입니다.

#### \_\_init_\_(cfg: [RayCasterCfg](#isaaclab.sensors.RayCasterCfg))

레이캐스터 객체를 초기화합니다.

* **매개변수:**
  **cfg** – 구성 파라미터입니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서의 인스턴스 수입니다.

이는 환경당 센서 수에 환경 수를 곱한 값과 같습니다.

#### *property* data *: [RayCasterData](#isaaclab.sensors.RayCasterData)*

센서의 데이터입니다.

이 속성은 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이는 데이터가 사용되지 않을 때 불필요한 계산을 피하기 위함입니다.

이 속성에 접근할 때 센서를 업데이트하려면 센서 구현에서 다음 코드 스니펫을 사용할 수 있습니다:

```python
# 필요시 센서 업데이트
self._update_outdated_buffers()
# 데이터 반환 (여기서 `_data`는 센서의 데이터입니다)
return self._data
```

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서 내부 상태를 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 센서 ID입니다. 기본값은 None입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

연산에 사용되는 메모리 디바이스입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 시각화가 구현되었는지 여부를 나타냅니다.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부를 나타냅니다.

센서가 초기화된 경우 True를 반환하고, 그렇지 않은 경우 False를 반환합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 센서 데이터를 시각화할지 여부입니다.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부입니다. 센서가 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

### *class* isaaclab.sensors.RayCasterData

레이캐스트 센서를 위한 데이터 컨테이너입니다.

**Attributes:**

| [`pos_w`](#isaaclab.sensors.RayCasterData.pos_w)           | 월드 프레임에서의 센서 원점 위치                               |
|------------------------------------------------------------|--------------------------------------------------------------|
| [`quat_w`](#isaaclab.sensors.RayCasterData.quat_w)         | 월드 프레임에서의 센서 원점 방향 (쿼터니언 w, x, y, z)       |
| [`ray_hits_w`](#isaaclab.sensors.RayCasterData.ray_hits_w) | 월드 프레임에서의 광선 히트 위치                             |

#### pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

월드 프레임에서의 센서 원점 위치입니다.

차원은 (N, 3)이며, 여기서 N은 센서의 수입니다.

#### quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

월드 프레임에서의 센서 원점 방향 (쿼터니언 w, x, y, z)입니다.

차원은 (N, 4)이며, 여기서 N은 센서의 수입니다.

#### ray_hits_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

월드 프레임에서의 광선 히트 위치입니다.

차원은 (N, B, 3)이며, 여기서 N은 센서의 수, B는 센서당 스캔 패턴의 광선 수입니다.

### *class* isaaclab.sensors.RayCasterCfg

Bases: [`SensorBaseCfg`](#isaaclab.sensors.SensorBaseCfg)

레이캐스트 센서를 위한 구성입니다.

**Classes:**

| [`OffsetCfg`](#isaaclab.sensors.RayCasterCfg.OffsetCfg)   | 센서 프레임이 센서의 부모 프레임에 상대적인 오프셋 포즈입니다.   |
|-----------------------------------------------------------|-----------------------------------------------------------------|

**Attributes:**

| [`mesh_prim_paths`](#isaaclab.sensors.RayCasterCfg.mesh_prim_paths)           | 레이캐스트를 수행할 메쉬 프리미티브 경로 목록입니다.                                        |
|-------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| [`offset`](#isaaclab.sensors.RayCasterCfg.offset)                             | 센서 프레임이 센서의 부모 프레임에 상대적인 오프셋 포즈입니다.                            |
| [`attach_yaw_only`](#isaaclab.sensors.RayCasterCfg.attach_yaw_only)           | 광선의 시작 위치와 방향이 요( yaw ) 방향만 추적하는지 여부입니다.                         |
| [`prim_path`](#isaaclab.sensors.RayCasterCfg.prim_path)                       | 센서의 프리미티브 경로 (또는 표현식)입니다.                                                 |
| [`update_period`](#isaaclab.sensors.RayCasterCfg.update_period)               | 센서 버퍼의 업데이트 주기(초 단위).                                                        |
| [`history_length`](#isaaclab.sensors.RayCasterCfg.history_length)             | 센서 버퍼에 저장할 과거 프레임 수.                                                         |
| [`debug_vis`](#isaaclab.sensors.RayCasterCfg.debug_vis)                       | 센서를 시각화할지 여부.                                                                   |
| [`ray_alignment`](#isaaclab.sensors.RayCasterCfg.ray_alignment)               | 광선이 지면에 투사되는 프레임을 지정합니다.                                              |
| [`pattern_cfg`](#isaaclab.sensors.RayCasterCfg.pattern_cfg)                   | 광선의 로컬 시작 위치와 방향을 정의하는 패턴.                                            |
| [`max_distance`](#isaaclab.sensors.RayCasterCfg.max_distance)                 | 센서에서 광선 추적을 수행할 최대 거리(미터).                                             |
| [`drift_range`](#isaaclab.sensors.RayCasterCfg.drift_range)                   | 월드 프레임에서 광선 시작 위치(xyz)에 추가할 드리프트 범위(미터).                        |
| [`ray_cast_drift_range`](#isaaclab.sensors.RayCasterCfg.ray_cast_drift_range) | 로컬 투사 프레임에서 투사된 광선 포인트에 추가할 드리프트 범위(미터).                     |
| [`visualizer_cfg`](#isaaclab.sensors.RayCasterCfg.visualizer_cfg)             | 시각화 마커를 위한 구성 객체.                                                            |

#### *class* OffsetCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

센서의 프레임이 센서의 부모 프레임으로부터 떨어진 오프셋 포즈.

**Attributes:**

| [`pos`](#isaaclab.sensors.RayCasterCfg.OffsetCfg.pos)   | 부모 프레임에 대한 이동량.                        |
|---------------------------------------------------------|--------------------------------------------------|
| [`rot`](#isaaclab.sensors.RayCasterCfg.OffsetCfg.rot)   | 부모 프레임에 대한 쿼터니언 회전(w, x, y, z). |

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임에 대한 이동량. 기본값은 (0.0, 0.0, 0.0).

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임에 대한 쿼터니언 회전(w, x, y, z). 기본값은 (1.0, 0.0, 0.0, 0.0).

#### mesh_prim_paths *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

광선 추적을 수행할 메시 프리미티브 경로 리스트.

#### NOTE
현재 단일 정적 메시만 지원됩니다. 다중 정적 메시 및 동적 메시 지원은 현재 진행 중입니다.

#### offset *: [OffsetCfg](#isaaclab.sensors.RayCasterCfg.OffsetCfg)*

센서의 프레임이 센서의 부모 프레임으로부터 떨어진 오프셋 포즈. 기본값은 항등 변환.

#### attach_yaw_only *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

광선의 시작 위치와 방향이 요( yaw ) 방향만 추적하는지 여부. 기본값은 None으로, 이는 사용 중단 경고를 발생시키지 않습니다.

이는 요 회전만 필요한 높이 맵 레이 캐스팅에 유용합니다.

#### Deprecated
버전 2.1.1부터 사용 중단: 이 속성은 사용 중단되었으며 향후 삭제됩니다. 대신 [`ray_alignment`](#isaaclab.sensors.RayCasterCfg.ray_alignment)을 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정했을 때와 동일한 동작을 얻으려면 [`ray_alignment`](#isaaclab.sensors.RayCasterCfg.ray_alignment)을 각각 `"yaw"` 또는 `"base"`로 설정하세요.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 프리미티브 경로(또는 표현식).

#### NOTE
이 표현식에는 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`가 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot/sensor`는 `/World/envs/env_.*/Robot/sensor`로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼의 업데이트 주기(초 단위). 기본값은 0.0(매 스텝마다 업데이트).

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수. 기본값은 0으로, 이는 현재 데이터만 저장(히스토리 없음)을 의미합니다.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서를 시각화할지 여부. 기본값은 False.

#### ray_alignment *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['base', 'yaw', 'world']*

광선이 지면에 투사되는 프레임을 지정합니다. 기본값은 “base”.

옵션은 다음과 같습니다:

* `base`이면 광선의 시작 위치와 방향이 루트의 전체 위치와 방향을 추적합니다.
* `yaw`이면 광선의 시작 위치와 방향이 루트의 위치와 방향의 요( yaw ) 성분만 추적합니다. 이는 높이 맵 레이 캐스팅에 유용합니다.
* `world`이면 광선의 시작 위치와 방향이 항상 고정됩니다. 이는 로봇의 매핑 패키지와 결합하여 글로벌 프레임에서 레이 캐스트를 쿼리하는 데 유용합니다.

#### pattern_cfg *: [PatternBaseCfg](isaaclab.sensors.patterns.md#isaaclab.sensors.patterns.PatternBaseCfg)*

광선의 로컬 시작 위치와 방향을 정의하는 패턴.

#### max_distance *: [float](https://docs.python.org/3/library/functions.html#float)*

센서에서 광선 추적을 수행할 최대 거리(미터). 기본값은 1e6.

#### drift_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

월드 프레임에서 광선 시작 위치(xyz)에 추가할 드리프트 범위(미터). 기본값은 (0.0, 0.0).

부동 베이스 로봇의 경우, 포즈 추정의 드리프트를 시뮬레이션하는 데 유용합니다.

#### ray_cast_drift_range *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]]*

로컬 투사 프레임에서 투사된 광선 포인트에 추가할 드리프트 범위(미터). 기본값은 x, y, z 축 각각에 대해 zero drift인 딕셔너리입니다.

부동 베이스 로봇의 경우, 포즈 추정의 드리프트를 시뮬레이션하는 데 유용합니다.

#### visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

시각화 마커를 위한 구성 객체. 기본값은 RAY_CASTER_MARKER_CFG.

#### NOTE
이 속성은 디버그 시각화가 활성화된 경우에만 사용됩니다.

## 레이 캐스트 카메라

### *class* isaaclab.sensors.RayCasterCamera

Bases: [`RayCaster`](#isaaclab.sensors.RayCaster)

레이 캐스팅 카메라 센서.

레이 캐스터 카메라는 광선을 사용하여 씬의 메시까지의 거리를 얻습니다. 광선은 센서의 로컬 좌표 프레임에서 정의됩니다. 센서는 USD 카메라 프리미티브를 통해 카메라 클래스를 구현하는 [`isaaclab.sensors.Camera`](#isaaclab.sensors.Camera)와 동일한 인터페이스를 가집니다. 그러나 이 클래스는 더 빠른 이미지 생성을 제공합니다. 센서는 구성에서 제공된 프리미티브 경로 목록의 메시들을 Warp 메시로 변환하고, 이 Warp 메시들에 대해서만 레이 캐스팅을 수행합니다.

현재 다음 어노테이터만 지원됩니다:

- `"distance_to_camera"`: 카메라 광학 중심까지의 거리를 포함하는 이미지.
- `"distance_to_image_plane"`: 카메라의 z축을 따라 카메라 평면上的 3D 점들의 거리를 포함하는 이미지.
- `"normals"`: 각 픽셀의 로컬 표면 법선 벡터를 포함하는 이미지.

#### NOTE
현재 정적 메시만 지원됩니다. 워프 메시를 동적 메시까지 확장하는 작업은 현재 진행 중입니다.

**Attributes:**

| [`cfg`](#isaaclab.sensors.RayCasterCamera.cfg)                                                   | 구성 파라미터.                                                              |
|--------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`UNSUPPORTED_TYPES`](#isaaclab.sensors.RayCasterCamera.UNSUPPORTED_TYPES)                       | 레이 캐스터 카메라에서 지원하지 않는 센서 유형의 집합.                       |
| [`data`](#isaaclab.sensors.RayCasterCamera.data)                                                 | 센서 데이터.                                                                |
| [`image_shape`](#isaaclab.sensors.RayCasterCamera.image_shape)                                   | 카메라 센서의 (높이, 너비)를 포함하는 튜플.                                 |
| [`frame`](#isaaclab.sensors.RayCasterCamera.frame)                                               | 측정이 이루어진 프레임 번호.                                                |
| [`device`](#isaaclab.sensors.RayCasterCamera.device)                                             | 계산에 사용되는 메모리 디바이스.                                            |
| [`has_debug_vis_implementation`](#isaaclab.sensors.RayCasterCamera.has_debug_vis_implementation) | 센서에 디버그 시각화 구현이 있는지 여부.                                    |
| [`is_initialized`](#isaaclab.sensors.RayCasterCamera.is_initialized)                             | 센서가 초기화되었는지 여부를 나타냅니다.                                             |
| [`meshes`](#isaaclab.sensors.RayCasterCamera.meshes)                                             | 레이캐스팅을 위한 워프 메시를 저장하는 딕셔너리이며, 모든 인스턴스 간에 공유됩니다.       |
| [`num_instances`](#isaaclab.sensors.RayCasterCamera.num_instances)                               | 센서의 인스턴스 수.                                             |

**Methods:**

| [`__init__`](#isaaclab.sensors.RayCasterCamera.__init__)(cfg)                                                    | 카메라 객체를 초기화합니다.                                                 |
|------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`set_intrinsic_matrices`](#isaaclab.sensors.RayCasterCamera.set_intrinsic_matrices)(matrices[, ...])            | 카메라의 내부 행렬을 설정합니다.                                        |
| [`reset`](#isaaclab.sensors.RayCasterCamera.reset)([env_ids])                                                    | 센서의 내부 상태를 재설정합니다.                                                   |
| [`set_world_poses`](#isaaclab.sensors.RayCasterCamera.set_world_poses)([positions, orientations, ...])           | 카메라의 월드 프레임 대비 포즈를 설정합니다.                                       |
| [`set_world_poses_from_view`](#isaaclab.sensors.RayCasterCamera.set_world_poses_from_view)(eyes, targets[, ...]) | 눈 위치와 Blick-at 대상 위치로부터 카메라 포즈를 설정합니다. |
| [`set_debug_vis`](#isaaclab.sensors.RayCasterCamera.set_debug_vis)(debug_vis)                                    | 센서 데이터 시각화 여부를 설정합니다.                                     |

#### cfg *: [RayCasterCameraCfg](#isaaclab.sensors.RayCasterCameraCfg)*

구성 파라미터입니다.

#### UNSUPPORTED_TYPES *: ClassVar[[set](https://docs.python.org/3/library/stdtypes.html#set)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]* *= {'bounding_box_2d_loose', 'bounding_box_2d_loose_fast', 'bounding_box_2d_tight', 'bounding_box_2d_tight_fast', 'bounding_box_3d', 'bounding_box_3d_fast', 'instance_id_segmentation', 'instance_id_segmentation_fast', 'instance_segmentation', 'instance_segmentation_fast', 'motion_vectors', 'rgb', 'semantic_segmentation', 'skeleton_data'}*

레이캐스팅 카메라에서 지원되지 않는 센서 유형의 집합입니다.

#### \_\_init_\_(cfg: [RayCasterCameraCfg](#isaaclab.sensors.RayCasterCameraCfg))

카메라 객체를 초기화합니다.

* **Parameters:**
  **cfg** – 구성 파라미터.
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 데이터 유형이 레이캐스팅 카메라에서 지원되지 않는 경우.

#### *property* data *: [CameraData](#isaaclab.sensors.CameraData)*

센서에서 가져온 데이터입니다.

이 속성은 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이렇게 하면 센서 데이터가 사용되지 않을 때 불필요한 계산을 방지할 수 있습니다.

이 속성에 접근할 때 센서를 업데이트하려면 센서 구현에서 다음 코드 스니펫을 사용할 수 있습니다:

```python
# update sensors if needed
self._update_outdated_buffers()
# return the data (where `_data` is the data for the sensor)
return self._data
```

#### *property* image_shape *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int)]*

카메라 센서의 (높이, 너비)를 포함하는 튜플입니다.

#### *property* frame *: torch.tensor*

측정이 이루어진 프레임 번호입니다.

#### set_intrinsic_matrices(matrices: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), focal_length: [float](https://docs.python.org/3/library/functions.html#float) = 1.0, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

카메라의 내부 행렬을 설정합니다.

* **Parameters:**
  * **matrices** – 카메라의 내부 행렬. 크기는 (N, 3, 3)입니다.
  * **focal_length** – apertura 값을 계산할 때 사용할 초점 거리(센티미터 단위). 기본값은 1.0입니다.
  * **env_ids** – 조작할 센서 ID. 기본값은 None이며, 이는 모든 센서 인덱스를 의미합니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서의 내부 상태를 재설정합니다.

* **Parameters:**
  **env_ids** – 재설정할 센서 ID. 기본값은 None입니다.

#### set_world_poses(positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, orientations: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, convention: Literal['opengl', 'ros', 'world'] = 'ros')

지정된 convention을 사용하여 월드 프레임 대비 카메라의 포즈를 설정합니다.

다양한 분야에서 카메라 방향에 서로 다른 컨벤션을 사용하기 때문에, 이 방법을 사용하면 지정된 컨벤션에 따라 카메라 포즈를 설정할 수 있습니다. 가능한 컨벤션은 다음과 같습니다:

- `"opengl"` - 전방 축: -Z - 상축 +Y - OpenGL (Usd.Camera) 컨벤션에 따라 오프셋이 적용됩니다
- `"ros"`    - 전방 축: +Z - 상축 -Y - ROS 컨벤션에 따라 오프셋이 적용됩니다
- `"world"`  - 전방 축: +X - 상축 +Z - 월드 프레임 컨벤션에 따라 오프셋이 적용됩니다

컨벤션에 대한 자세한 내용은 `isaaclab.utils.maths.convert_camera_frame_orientation_convention()` 를 참조하십시오.

* **Parameters:**
  * **positions** – 데카르트 좌표(미터 단위). 크기는 (N, 3)입니다.
    기본값은 None이며, 이 경우 카메라 위치는 변경되지 않습니다.
  * **orientations** – (w, x, y, z) 형태의 쿼터니언 방향. 크기는 (N, 4)입니다.
    기본값은 None이며, 이 경우 카메라 방향은 변경되지 않습니다.
  * **env_ids** – 조작할 센서 ID. 기본값은 None이며, 이는 모든 센서 인덱스를 의미합니다.
  * **convention** – 포즈가 제공되는 컨벤션. 기본값은 "ros"입니다.
* **Raises:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 카메라 프리미가 설정되지 않았습니다. 먼저 `initialize()` 메서드를 호출해야 합니다.

#### set_world_poses_from_view(eyes: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), targets: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

눈 위치와 Blick-at 대상 위치로부터 카메라 포즈를 설정합니다.

* **Parameters:**
  * **eyes** – 카메라 눈의 위치. 크기는 (N, 3)입니다.
  * **targets** – Blick-at 대상 위치. 크기는 (N, 3)입니다.
  * **env_ids** – 조작할 센서 ID. 기본값은 None이며, 이는 모든 센서 인덱스를 의미합니다.
* **Raises:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 카메라 프리미가 설정되지 않았습니다. 먼저 `initialize()` 메서드를 호출해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 스테이지 업-축이 "Y" 또는 "Z"가 아닌 경우.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 장치입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서에 디버그 시각화가 구현되어 있는지 여부입니다.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부입니다.

센서가 초기화된 경우 True를 반환하고, 그렇지 않은 경우 False를 반환합니다.

#### meshes *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), wp.Mesh]]* *= {}*

레이캐스팅을 위한 워프 메시를 저장하는 딕셔너리이며, 모든 인스턴스 간에 공유됩니다.

키들은 메시의 prim 경로에 해당하며, 값은 해당 워프 Mesh 객체입니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서의 인스턴스 수.

이 값은 환경당 센서 수에 환경 수를 곱한 값과 같습니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터 시각화 여부를 설정합니다.

* **Parameters:**
  **debug_vis** – 센서 데이터를 시각화할지 여부.
* **Returns:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 센서가 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

### *class* isaaclab.sensors.RayCasterCameraCfg

Bases: [`RayCasterCfg`](#isaaclab.sensors.RayCasterCfg)

레이캐스트 센서용 구성입니다.

**Attributes:**

| [`offset`](#isaaclab.sensors.RayCasterCameraCfg.offset)                                   | 센서의 부모 프레임로부터 센서 프레임의 오프셋 포즈.                                 |
|-------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| [`prim_path`](#isaaclab.sensors.RayCasterCameraCfg.prim_path)                             | 센서의 프라임 경로(또는 표현식)입니다.                                                              |
| [`update_period`](#isaaclab.sensors.RayCasterCameraCfg.update_period)                     | 센서 버퍼의 업데이트 주기(초 단위)입니다.                                                     |
| [`history_length`](#isaaclab.sensors.RayCasterCameraCfg.history_length)                   | 센서 버퍼에 저장할 과거 프레임 수입니다.                                                 |
| [`debug_vis`](#isaaclab.sensors.RayCasterCameraCfg.debug_vis)                             | 센서를 시각화할지 여부입니다.                                                                      |
| [`mesh_prim_paths`](#isaaclab.sensors.RayCasterCameraCfg.mesh_prim_paths)                 | 레이를 캐스트할 메쉬 프리미티브 경로 목록입니다.                                                 |
| [`attach_yaw_only`](#isaaclab.sensors.RayCasterCameraCfg.attach_yaw_only)                 | 레이의 시작 위치와 방향이 요우 방향만 추적하는지 여부입니다.                   |
| [`ray_alignment`](#isaaclab.sensors.RayCasterCameraCfg.ray_alignment)                     | 레이가 지면에 투사되는 프레임을 지정합니다.                                         |
| [`max_distance`](#isaaclab.sensors.RayCasterCameraCfg.max_distance)                       | 센서에서 레이를 캐스트할 최대 거리(미터 단위)입니다.                                          |
| [`drift_range`](#isaaclab.sensors.RayCasterCameraCfg.drift_range)                         | 월드 프레임에서 레이 시작 위치(xyz)에 추가할 드리프트 범위(미터 단위)입니다.             |
| [`ray_cast_drift_range`](#isaaclab.sensors.RayCasterCameraCfg.ray_cast_drift_range)       | 로컬 투사 프레임에서 투사된 레이 포인트에 추가할 드리프트 범위(미터 단위)입니다.          |
| [`visualizer_cfg`](#isaaclab.sensors.RayCasterCameraCfg.visualizer_cfg)                   | 시각화 마커를 위한 구성 객체입니다.                                               |
| [`data_types`](#isaaclab.sensors.RayCasterCameraCfg.data_types)                           | 카메라용으로 활성화할 센서 이름/유형 목록입니다.                                                  |
| [`depth_clipping_behavior`](#isaaclab.sensors.RayCasterCameraCfg.depth_clipping_behavior) | 최대값을 초과하는 값에 대한 카메라의 클리핑 동작입니다.                                 |
| [`pattern_cfg`](#isaaclab.sensors.RayCasterCameraCfg.pattern_cfg)                         | 핀홀 카메라 패턴에서 로컬 레이 시작 위치와 방향을 정의하는 패턴입니다.           |

#### offset *: OffsetCfg*

센서의 부모 프레임에서 센서 프레임까지의 오프셋 포즈입니다. 기본값은 항등 변환입니다.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 프라임 경로(또는 표현식)입니다.

#### NOTE
표현식에는 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot/sensor`는 `/World/envs/env_.*/Robot/sensor`로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼의 업데이트 주기(초 단위)입니다. 기본값은 0.0(매 단계 업데이트)입니다.

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수입니다. 기본값은 0이며, 이는 현재 데이터만 저장(히스토리 없음)을 의미합니다.

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서를 시각화할지 여부입니다. 기본값은 False입니다.

#### mesh_prim_paths *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

레이를 캐스트할 메쉬 프리미티브 경로 목록입니다.

#### NOTE
현재는 단일 정적 메쉬만 지원됩니다. 여러 정적 메쉬와 동적 메쉬 지원을 위해 작업 중입니다.

#### attach_yaw_only *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

레이의 시작 위치와 방향이 요우 방향만 추적하는지 여부입니다. 기본값은 None이며, 이는 사용되지 않은 경고가 발생하지 않음을 의미합니다.

이 옵션은 요우 회전만 필요한 높이 맵 레이 캐스팅에 유용합니다.

#### Deprecated
버전 2.1.1부터 지원 중단됨: 이 속성은 지원 중단되었으며 향후 제거될 예정입니다. 대신
[`ray_alignment`](#isaaclab.sensors.RayCasterCameraCfg.ray_alignment)를 사용하세요.

이 매개변수를 `True` 또는 `False`로 설정했을 때와 동일한 동작을 얻으려면
[`ray_alignment`](#isaaclab.sensors.RayCasterCameraCfg.ray_alignment)를 각각 `"yaw"` 또는 `"base"`로 설정하세요.

#### ray_alignment *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['base', 'yaw', 'world']*

레이가 지면에 투사되는 프레임을 지정합니다. 기본값은 `"base"`입니다.

옵션은 다음과 같습니다:

* `base`이면 레이의 시작 위치와 방향이 루트의 전체 위치와 방향을 추적합니다.
* `yaw`이면 레이의 시작 위치와 방향이 루트 위치와 방향의 요우 성분만 추적합니다. 이는 높이 맵 레이 캐스팅에 유용합니다.
* `world`이면 레이의 시작 위치와 방향이 항상 고정됩니다. 이는 로봇의 매핑 패키지와 결합하여 전역 프레임에서 레이 캐스트를 쿼리하는 데 유용합니다.

#### max_distance *: [float](https://docs.python.org/3/library/functions.html#float)*

센서에서 레이를 캐스트할 최대 거리(미터 단위)입니다. 기본값은 1e6입니다.

#### drift_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

월드 프레임에서 레이 시작 위치(xyz)에 추가할 드리프트 범위(미터 단위)입니다. 기본값은 (0.0, 0.0)입니다.

플로팅 베이스 로봇의 경우, 이는 로봇의 포즈 추정에서 드리프트를 시뮬레이션하는 데 유용합니다.

#### ray_cast_drift_range *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]]*

로컬 투사 프레임에서 투사된 레이 포인트에 추가할 드리프트 범위(미터 단위)입니다. 기본값은 x, y, z 축 각각에 대해 제로 드리프트를 가진 딕셔너리입니다.

플로팅 베이스 로봇의 경우, 이는 로봇의 포즈 추정에서 드리프트를 시뮬레이션하는 데 유용합니다.

#### visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

시각화 마커를 위한 구성 객체입니다. 기본값은 RAY_CASTER_MARKER_CFG입니다.

#### NOTE
이 속성은 디버그 시각화가 활성화된 경우에만 사용됩니다.

#### data_types *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

카메라용으로 활성화할 센서 이름/유형 목록입니다. 기본값은 [`"distance_to_image_plane"']`입니다.

#### depth_clipping_behavior *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['max', 'zero', 'none']*

최대값을 초과하는 값에 대한 카메라의 클리핑 동작입니다. 기본값은 `"none"`입니다.

- `"max"`: 값은 최대값으로 클리핑됩니다.
- `"zero"`: 값은 0으로 클리핑됩니다.
- `"none"`: 클리핑이 적용되지 않으며, `distance_to_camera` 데이터 유형에 대해 `inf`, `distance_to_image_plane` 데이터 유형에 대해 `nan`이 반환됩니다.

#### pattern_cfg *: [PinholeCameraPatternCfg](isaaclab.sensors.patterns.md#isaaclab.sensors.patterns.PinholeCameraPatternCfg)*

핀홀 카메라 패턴에서 로컬 레이 시작 위치와 방향을 정의하는 패턴입니다.

## 다중 메쉬 레이-캐스트 센서

### *클래스* isaaclab.sensors.MultiMeshRayCaster

기반: [`RayCaster`](#isaaclab.sensors.RayCaster)

다중 메쉬 레이-캐스트 센서입니다.

이 레이-캐스터는 장면에 있는 메쉬와의 충돌을 감지하기 위해 레이 세트를 사용합니다. 레이는 센서의 로컬 좌표 프레임에서 정의됩니다. 센서는 특정 레이 패턴을 사용해 메쉬 세트와 레이-캐스트를 수행하도록 구성할 수 있습니다.

메쉬는 구성에 제공된 프리미티브 경로 목록에서 파싱됩니다. 이러한 메쉬는 워프 메쉬로 변환되어 [`meshes`](#isaaclab.sensors.MultiMeshRayCaster.meshes) 목록에 저장됩니다. 그런 다음 레이-캐스터는 구성에 제공된 레이 패턴을 사용하여 이러한 워프 메쉬와 레이-캐스트를 수행합니다.

기본 RayCaster에 비해 MultiMeshRayCaster는 기본 RayCaster의 확장으로 다음과 같은 향상된 기능을 제공합니다:

- 다중 대상 유형에 대한 레이캐스팅 : 구형, 큐브와 같은 프리미티브 형태뿐 아니라 임의의 메쉬도 지원합니다.
- 동적 메쉬 추적 : 지정된 메쉬를 추적하여 이동 부품(예: 로봇 링크, 관절 연결된 객체 또는 동적 장애물)에 대한 레이캐스팅을 가능하게 합니다.
- 메모리 효율적인 캐싱 : 환경 전반에 걸쳐 메쉬 데이터를 재사용하여 중복 메모리 사용을 방지합니다.

예시: 로봇(예: ANYmal)의 시각적 메쉬에 대해 레이캐스팅하기

```python
ray_caster_cfg = MultiMeshRayCasterCfg(
    prim_path="{ENV_REGEX_NS}/Robot",
    mesh_prim_paths=[
        "/World/Ground",
        MultiMeshRayCasterCfg.RaycastTargetCfg(prim_expr="{ENV_REGEX_NS}/Robot/LF_.*/visuals"),
        MultiMeshRayCasterCfg.RaycastTargetCfg(prim_expr="{ENV_REGEX_NS}/Robot/RF_.*/visuals"),
        MultiMeshRayCasterCfg.RaycastTargetCfg(prim_expr="{ENV_REGEX_NS}/Robot/LH_.*/visuals"),
        MultiMeshRayCasterCfg.RaycastTargetCfg(prim_expr="{ENV_REGEX_NS}/Robot/RH_.*/visuals"),
        MultiMeshRayCasterCfg.RaycastTargetCfg(prim_expr="{ENV_REGEX_NS}/Robot/base/visuals"),
    ],
    ray_alignment="world",
    pattern_cfg=patterns.GridPatternCfg(resolution=0.02, size=(2.5, 2.5), direction=(0, 0, -1)),
)
```

**특성:**

| [`cfg`](#isaaclab.sensors.MultiMeshRayCaster.cfg)                                                   | 구성 매개변수입니다.                                                  |
|-----------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`mesh_views`](#isaaclab.sensors.MultiMeshRayCaster.mesh_views)                                     | 레이캐스팅을 위한 메쉬 뷰를 저장하는 딕셔너리이며, 모든 인스턴스 간에 공유됩니다.  |
| [`data`](#isaaclab.sensors.MultiMeshRayCaster.data)                                                 | 센서의 데이터.                                                          |
| [`device`](#isaaclab.sensors.MultiMeshRayCaster.device)                                             | 계산에 사용되는 메모리 디바이스.                                                 |
| [`has_debug_vis_implementation`](#isaaclab.sensors.MultiMeshRayCaster.has_debug_vis_implementation) | 센서에 디버그 시각화가 구현되어 있는지 여부.                      |
| [`is_initialized`](#isaaclab.sensors.MultiMeshRayCaster.is_initialized)                             | 센서가 초기화되었는지 여부.                                             |
| [`meshes`](#isaaclab.sensors.MultiMeshRayCaster.meshes)                                             | 레이캐스팅을 위한 워프 메쉬를 저장하는 딕셔너리이며, 모든 인스턴스 간에 공유됩니다. |
| [`num_instances`](#isaaclab.sensors.MultiMeshRayCaster.num_instances)                               | 센서의 인스턴스 수.                                             |

**메서드:**

| [`__init__`](#isaaclab.sensors.MultiMeshRayCaster.__init__)(cfg)                 | 레이스-캐스터 객체를 초기화합니다.         |
|----------------------------------------------------------------------------------|--------------------------------------------|
| [`reset`](#isaaclab.sensors.MultiMeshRayCaster.reset)([env_ids])                 | 센서의 내부 상태를 재설정합니다.               |
| [`set_debug_vis`](#isaaclab.sensors.MultiMeshRayCaster.set_debug_vis)(debug_vis) | 센서 데이터 시각화 여부를 설정합니다. |

#### cfg *: [MultiMeshRayCasterCfg](#isaaclab.sensors.MultiMeshRayCasterCfg)*

구성 매개변수입니다.

#### mesh_views *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [XformPrimView](isaaclab.sim.views.md#isaaclab.sim.views.XformPrimView) | physx.ArticulationView | physx.RigidBodyView]]* *= {}*

레이캐스팅을 위한 메쉬 뷰를 저장하는 딕셔너리이며, 모든 인스턴스 간에 공유됩니다.

키들은 메쉬 뷰의 프리미티브 경로에 해당하며, 값들은 해당 뷰 객체입니다.

#### \_\_init_\_(cfg: [MultiMeshRayCasterCfg](#isaaclab.sensors.MultiMeshRayCasterCfg))

레이스-캐스터 객체를 초기화합니다.

* **매개변수:**
  **cfg** – 구성 매개변수입니다.

#### *property* data *: [MultiMeshRayCasterData](#isaaclab.sensors.MultiMeshRayCasterData)*

센서의 데이터입니다.

이 속성은 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 방지하기 위함입니다.

이 속성에 접근할 때 센서를 업데이트하려면, 센서 구현에 다음 코드 스니펫을 사용할 수 있습니다:

```python
# 필요하면 센서 업데이트
self._update_outdated_buffers()
# 데이터 반환 (여기서 `_data`는 센서의 데이터)
return self._data
```

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 디바이스입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서에 디버그 시각화가 구현되어 있는지 여부입니다.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부입니다.

센서가 초기화된 경우 True를 반환하고, 그렇지 않은 경우 False를 반환합니다.

#### meshes *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), wp.Mesh]]* *= {}*

레이캐스팅을 위한 워프 메쉬를 저장하는 딕셔너리이며, 모든 인스턴스 간에 공유됩니다.

키들은 메쉬의 프리미티브 경로에 해당하며, 값들은 해당 워프 Mesh 객체입니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서의 인스턴스 수입니다.

이는 환경당 센서 수에 환경 수를 곱한 값과 같습니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서의 내부 상태를 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 센서 ID. 기본값은 None입니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 센서 데이터 시각화 여부입니다.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부입니다. 센서가 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

### *class* isaaclab.sensors.MultiMeshRayCasterData

다중 메쉬 레이-캐스트 센서를 위한 데이터 컨테이너입니다.

**속성:**

| [`pos_w`](#isaaclab.sensors.MultiMeshRayCasterData.pos_w)               | 월드 프레임에서의 센서 원점 위치입니다.                               |
|-------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| [`quat_w`](#isaaclab.sensors.MultiMeshRayCasterData.quat_w)             | 월드 프레임에서의 센서 원점 방향(쿼터니언 w, x, y, z)입니다. |
| [`ray_hits_w`](#isaaclab.sensors.MultiMeshRayCasterData.ray_hits_w)     | 월드 프레임에서의 레이 히트 위치입니다.                                   |
| [`ray_mesh_ids`](#isaaclab.sensors.MultiMeshRayCasterData.ray_mesh_ids) | 레이 히트의 메쉬 ID입니다.                                               |

#### pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

월드 프레임에서의 센서 원점 위치입니다.

형상은 (N, 3)이며, 여기서 N은 센서의 수입니다.

#### quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

월드 프레임에서의 센서 원점 방향(쿼터니언 w, x, y, z)입니다.

형상은 (N, 4)이며, 여기서 N은 센서의 수입니다.

#### ray_hits_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

월드 프레임에서의 레이 히트 위치입니다.

형상은 (N, B, 3)이며, 여기서 N은 센서의 수, B는 센서당 스캔 패턴의 레이 수입니다.

#### ray_mesh_ids *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

레이 히트의 메쉬 ID입니다.

형상은 (N, B, 1)이며, 여기서 N은 센서의 수, B는 센서당 스캔 패턴의 레이 수입니다.

### *class* isaaclab.sensors.MultiMeshRayCasterCfg

베이스 클래스: [`RayCasterCfg`](#isaaclab.sensors.RayCasterCfg)

다중 메쉬 레이-캐스트 센서의 구성입니다.

**클래스:**

| [`RaycastTargetCfg`](#isaaclab.sensors.MultiMeshRayCasterCfg.RaycastTargetCfg)   | 다양한 레이-캐스트 대상에 대한 구성입니다.   |
|----------------------------------------------------------------------------------|-------------------------------------------------|

**속성:**

| [`prim_path`](#isaaclab.sensors.MultiMeshRayCasterCfg.prim_path)                       | 센서의 프리미티브 경로(또는 표현식)입니다.                                                     |
|----------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| [`update_period`](#isaaclab.sensors.MultiMeshRayCasterCfg.update_period)               | 센서 버퍼의 업데이트 주기(초 단위)입니다.                                            |
| [`history_length`](#isaaclab.sensors.MultiMeshRayCasterCfg.history_length)             | 센서 버퍼에 저장할 과거 프레임 수입니다.                                        |
| [`debug_vis`](#isaaclab.sensors.MultiMeshRayCasterCfg.debug_vis)                       | 센서 시각화 여부입니다.                                                             |
| [`offset`](#isaaclab.sensors.MultiMeshRayCasterCfg.offset)                             | 센서 프레임과 센서 부모 프레임 사이의 오프셋 포즈입니다.                        |
| [`attach_yaw_only`](#isaaclab.sensors.MultiMeshRayCasterCfg.attach_yaw_only)           | 레이의 시작 위치와 방향이 요우 방향만 추적하는지 여부입니다.          |
| [`ray_alignment`](#isaaclab.sensors.MultiMeshRayCasterCfg.ray_alignment)               | 레이스가 지면에 투영되는 기준 프레임을 지정합니다.                                |
| [`pattern_cfg`](#isaaclab.sensors.MultiMeshRayCasterCfg.pattern_cfg)                   | 로컬 레이 시작 위치와 방향을 정의하는 패턴입니다.                    |
| [`max_distance`](#isaaclab.sensors.MultiMeshRayCasterCfg.max_distance)                 | 레이캐스트를 수행할 최대 거리(미터 단위)입니다.                                 |
| [`drift_range`](#isaaclab.sensors.MultiMeshRayCasterCfg.drift_range)                   | 월드 프레임에서 레이 시작 위치(xyz)에 추가할 드리프트 범위(미터 단위)입니다.    |
| [`ray_cast_drift_range`](#isaaclab.sensors.MultiMeshRayCasterCfg.ray_cast_drift_range) | 지역 투영 프레임에서 투영된 레이 포인트에 추가할 드리프트 범위(미터 단위)입니다. |
| [`visualizer_cfg`](#isaaclab.sensors.MultiMeshRayCasterCfg.visualizer_cfg)             | 시각화 마커의 구성 객체입니다.                                      |
| [`mesh_prim_paths`](#isaaclab.sensors.MultiMeshRayCasterCfg.mesh_prim_paths)           | 레이캐스트에 사용할 메시 프리미티브 경로 목록입니다.                                        |
| [`update_mesh_ids`](#isaaclab.sensors.MultiMeshRayCasterCfg.update_mesh_ids)           | `data` 컨테이너의 레이 히트 메시 ID를 업데이트할지 여부입니다.                      |
| [`reference_meshes`](#isaaclab.sensors.MultiMeshRayCasterCfg.reference_meshes)         | 각 메시를 메모리에 별도로 로드하는 대신 중복된 메시에 대한 참조를 사용할지 여부입니다.   |

#### *class* RaycastTargetCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

다양한 레이캐스트 대상에 대한 구성입니다.

**Attributes:**

| [`prim_expr`](#isaaclab.sensors.MultiMeshRayCasterCfg.RaycastTargetCfg.prim_expr)                         | 레이캐스트 대상 프리미티브를 지정하는 정규식입니다.                       |
|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| [`is_shared`](#isaaclab.sensors.MultiMeshRayCasterCfg.RaycastTargetCfg.is_shared)                         | 대상 프리미티브가 모든 환경에서 동일한 메시를 가질 것으로 가정되는지 여부입니다. |
| [`merge_prim_meshes`](#isaaclab.sensors.MultiMeshRayCasterCfg.RaycastTargetCfg.merge_prim_meshes)         | 하나의 프리미티브에 여러 메시가 포함된 경우 파싱된 메시들을 병합할지 여부입니다.    |
| [`track_mesh_transforms`](#isaaclab.sensors.MultiMeshRayCasterCfg.RaycastTargetCfg.track_mesh_transforms) | 메시 변환을 추적할지 여부입니다.                             |

#### prim_expr *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

레이캐스트 대상 프리미티브를 지정하는 정규식입니다.

#### is_shared *: [bool](https://docs.python.org/3/library/functions.html#bool)*

대상 프리미티브가 모든 환경에서 동일한 메시로 가정되는지 여부입니다. 기본값은 False입니다.

True인 경우, 첫 번째 메시만 읽은 후 모든 환경에서 재사용되며 재파싱되지 않습니다. 이는 동일한 에셋을 사용하는 많은 환경에서 시작 성능을 향상시킵니다.

#### NOTE
[`MultiMeshRayCasterCfg.reference_meshes`](#isaaclab.sensors.MultiMeshRayCasterCfg.reference_meshes)가 False인 경우, 이 플래그는 효력이 없습니다.

#### merge_prim_meshes *: [bool](https://docs.python.org/3/library/functions.html#bool)*

하나의 프리미티브에 여러 메시가 포함된 경우 파싱된 메시들을 병합할지 여부입니다. 기본값은 True입니다.

이로써 프리미티브의 모든 메시들을 결합한 새 메시가 생성됩니다. 레이캐스트 히트의 메시 ID는 단일 병합된 메시를 참조하게 됩니다.

#### track_mesh_transforms *: [bool](https://docs.python.org/3/library/functions.html#bool)*

메시 변환을 추적할지 여부입니다. 기본값은 True입니다.

#### NOTE
메시가 정적인 경우 성능을 높이기 위해 메시 변환을 추적하지 않는 것이 권장됩니다.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 프리미티브 경로(또는 표현식)입니다.

#### NOTE
표현식은 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`를 포함할 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예: `{ENV_REGEX_NS}/Robot/sensor`은 `/World/envs/env_.*/Robot/sensor`로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼의 업데이트 주기(초 단위)입니다. 기본값은 0.0(매 스텝 업데이트)입니다.

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 이전 프레임 수입니다. 기본값은 0으로, 이는 현재 데이터만 저장됨을 의미합니다(히스토리 없음).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서를 시각화할지 여부입니다. 기본값은 False입니다.

#### offset *: [OffsetCfg](#isaaclab.sensors.RayCasterCfg.OffsetCfg)*

센서의 부모 프레임으로부터 센서 프레임까지의 오프셋 포즈입니다. 기본값은 항등 변환입니다.

#### attach_yaw_only *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

레이의 시작 위치와 방향이 오직 요 각도 방향만 추적하는지 여부입니다. 기본값은 None이며, 이는 더 이상 사용되지 않는 사용법에 대한 경고를 발생시키지 않습니다.

이것은 높이 맵 레이캐스팅에 유용하며, 여기서 오직 요 회전만 필요합니다.

#### Deprecated
2.1.1 버전부터 더 이상 사용되지 않음: 이 속성은 향후 제거될 예정입니다. 대신 [`ray_alignment`](#isaaclab.sensors.MultiMeshRayCasterCfg.ray_alignment)를 사용하십시오.

이 매개변수를 `True` 또는 `False`로 설정했을 때의 동작과 동일하게 얻으려면
[`ray_alignment`](#isaaclab.sensors.MultiMeshRayCasterCfg.ray_alignment)를 각각 `"yaw"` 또는 `"base"`로 설정하십시오.

#### ray_alignment *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['base', 'yaw', 'world']*

레이가 지면에 투영되는 프레임을 지정합니다. 기본값은 “base”입니다.

옵션은 다음과 같습니다:

* `base`이면 레이의 시작 위치와 방향이 루트의 전체 위치 및 방향을 추적합니다.
* `yaw`이면 레이의 시작 위치와 방향이 루트의 위치와 방향의 요 성분만 추적합니다. 이는 높이 맵 레이캐스팅에 유용합니다.
* `world`이면 레이의 시작 위치와 방향이 항상 고정됩니다. 이는 로봇의 매핑 패키지와 결합하여 전역 프레임에서 레이캐스트를 쿼리할 때 유용합니다.

#### pattern_cfg *: [PatternBaseCfg](isaaclab.sensors.patterns.md#isaaclab.sensors.patterns.PatternBaseCfg)*

로컬 레이의 시작 위치와 방향을 정의하는 패턴입니다.

#### max_distance *: [float](https://docs.python.org/3/library/functions.html#float)*

센서로부터 레이캐스트를 수행할 최대 거리(미터 단위)입니다. 기본값은 1e6입니다.

#### drift_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

월드 프레임에서 레이 시작 위치(xyz)에 추가할 드리프트 범위(미터 단위)입니다. 기본값은 (0.0, 0.0)입니다.

플로팅 베이스 로봇의 경우, 이는 포즈 추정치의 드리프트를 시뮬레이션하는 데 유용합니다.

#### ray_cast_drift_range *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]]*

로컬 투영 프레임에서 투사된 레이 포인트에 추가할 드리프트 범위(미터 단위)입니다. 기본값은 x, y, z 축 각각에 대해 0 드리프트를 갖는 딕셔너리입니다.

플로팅 베이스 로봇의 경우, 이는 포즈 추정치의 드리프트를 시뮬레이션하는 데 유용합니다.

#### visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

시각화 마커의 구성 객체입니다. 기본값은 RAY_CASTER_MARKER_CFG입니다.

#### NOTE
이 속성은 디버그 시각화가 활성화된 경우에만 사용됩니다.

#### mesh_prim_paths *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str) | [isaaclab.sensors.ray_caster.multi_mesh_ray_caster.cfg.MultiMeshRayCasterCfg.RaycastTargetCfg](#isaaclab.sensors.MultiMeshRayCasterCfg.RaycastTargetCfg)]*

레이캐스트에 사용할 메시 프리미티브 경로 목록입니다.

항목이 문자열인 경우, 내부적으로 [`RaycastTargetCfg`](#isaaclab.sensors.MultiMeshRayCasterCfg.RaycastTargetCfg)로 변환되며
[`track_mesh_transforms`](#isaaclab.sensors.MultiMeshRayCasterCfg.RaycastTargetCfg.track_mesh_transforms)는 비활성화됩니다. 이러한 설정은 기본 레이캐스터와의 하위 호환성을 보장하기 위함입니다.

#### update_mesh_ids *: [bool](https://docs.python.org/3/library/functions.html#bool)*

`data` 컨테이너의 레이 히트 메시 ID를 업데이트할지 여부입니다.

#### reference_meshes *: [bool](https://docs.python.org/3/library/functions.html#bool)*

각 메시를 메모리에 별도로 로드하는 대신 중복된 메시에 대한 참조를 사용할지 여부입니다. 기본값은 True입니다.

활성화되면, 레이캐스터는 모든 환경의 모든 메시들을 파싱하지만, 중복된 메시에 대해서는 복사본을 여러 개 저장하는 대신 참조를 재사용합니다.これにより 메모리 사용량이 감소됩니다.

## 멀티-메시 레이캐스트 카메라

### *class* isaaclab.sensors.MultiMeshRayCasterCamera

Bases: [`RayCasterCamera`](#isaaclab.sensors.RayCasterCamera), [`MultiMeshRayCaster`](#isaaclab.sensors.MultiMeshRayCaster)

멀티-메시 레이캐스팅 카메라 센서입니다.

레이캐스터 카메라는 씬의 메시까지의 거리를 얻기 위해 광선의 집합을 사용합니다. 광선은 센서의 로컬 좌표 프레임에서 정의됩니다. 이 센서는 USD 카메라 프리미티브를 통해 카메라 클래스를 구현하는 [`isaaclab.sensors.Camera`](#isaaclab.sensors.Camera)와 동일한 인터페이스를 가집니다. 그러나 이 클래스는 이미지 생성을 더 빠르게 제공합니다. 센서는 구성에 제공된 프리미티브 경로 목록의 메시들을 Warp 메시로 변환한 후, 이 Warp 메시들에 대해서만 레이캐스트를 수행합니다.

현재 다음과 같은 어노테이터만 지원됩니다:

- `"distance_to_camera"`: 카메라 광학 중심까지의 거리를 포함하는 이미지입니다.
- `"distance_to_image_plane"`: 카메라의 z-축을 따라 카메라 평면으로부터의 3D 점들의 거리를 포함하는 이미지입니다.
- `"normals"`: 각 픽셀에서의 로컬 표면 법선 벡터를 포함하는 이미지입니다.

**Attributes:**

| [`cfg`](#isaaclab.sensors.MultiMeshRayCasterCamera.cfg)                                                   | 구성 매개변수입니다.                                                  |
|-----------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`UNSUPPORTED_TYPES`](#isaaclab.sensors.MultiMeshRayCasterCamera.UNSUPPORTED_TYPES)                       | 레이캐스터 카메라에서 지원되지 않는 센서 타입의 집합.                       |
| [`data`](#isaaclab.sensors.MultiMeshRayCasterCamera.data)                                                 | 센서 데이터.                                                                 |
| [`device`](#isaaclab.sensors.MultiMeshRayCasterCamera.device)                                             | 계산을 위한 메모리 디바이스.                                                 |
| [`frame`](#isaaclab.sensors.MultiMeshRayCasterCamera.frame)                                               | 측정치가 이루어진 프레임 번호.                                               |
| [`has_debug_vis_implementation`](#isaaclab.sensors.MultiMeshRayCasterCamera.has_debug_vis_implementation) | 디버그 시각화가 구현되었는지 여부.                                           |
| [`image_shape`](#isaaclab.sensors.MultiMeshRayCasterCamera.image_shape)                                   | 카메라 센서의 (높이, 너비)를 포함하는 튜플.                                  |
| [`is_initialized`](#isaaclab.sensors.MultiMeshRayCasterCamera.is_initialized)                             | 센서가 초기화되었는지 여부.                                                  |
| [`mesh_views`](#isaaclab.sensors.MultiMeshRayCasterCamera.mesh_views)                                     | 레이캐스팅을 위한 메쉬 뷰를 저장하는 딕셔너리. 모든 인스턴스 간에 공유됨.   |
| [`meshes`](#isaaclab.sensors.MultiMeshRayCasterCamera.meshes)                                             | 레이캐스팅을 위한 워프 메쉬를 저장하는 딕셔너리. 모든 인스턴스 간에 공유됨.  |
| [`num_instances`](#isaaclab.sensors.MultiMeshRayCasterCamera.num_instances)                               | 센서 인스턴스 수.                                                            |

**Methods:**

| [`__init__`](#isaaclab.sensors.MultiMeshRayCasterCamera.__init__)(cfg)                                                    | 카메라 객체를 초기화합니다.                                                  |
|---------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| [`reset`](#isaaclab.sensors.MultiMeshRayCasterCamera.reset)([env_ids])                                                    | 센서 내부 상태를 재설정합니다.                                               |
| [`set_debug_vis`](#isaaclab.sensors.MultiMeshRayCasterCamera.set_debug_vis)(debug_vis)                                    | 센서 데이터 시각화 여부를 설정합니다.                                        |
| [`set_intrinsic_matrices`](#isaaclab.sensors.MultiMeshRayCasterCamera.set_intrinsic_matrices)(matrices[, ...])            | 카메라의 내부 행렬을 설정합니다.                                             |
| [`set_world_poses`](#isaaclab.sensors.MultiMeshRayCasterCamera.set_world_poses)([positions, orientations, ...])           | 카메라의 포즈(월드 프레임 기준)를 설정합니다.                                |
| [`set_world_poses_from_view`](#isaaclab.sensors.MultiMeshRayCasterCamera.set_world_poses_from_view)(eyes, targets[, ...]) | 눈 위치와 타겟 위치에서 카메라 포즈를 설정합니다.                             |

#### cfg *: [MultiMeshRayCasterCameraCfg](#isaaclab.sensors.MultiMeshRayCasterCameraCfg)*

구성 파라미터.

#### \_\_init_\_(cfg: [MultiMeshRayCasterCameraCfg](#isaaclab.sensors.MultiMeshRayCasterCameraCfg))

카메라 객체를 초기화합니다.

* **매개변수:**
  **cfg** – 구성 파라미터.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 제공된 데이터 타입이 레이캐스터 카메라에서 지원되지 않는 경우.

#### UNSUPPORTED_TYPES *: ClassVar[[set](https://docs.python.org/3/library/stdtypes.html#set)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]* *= {'bounding_box_2d_loose', 'bounding_box_2d_loose_fast', 'bounding_box_2d_tight', 'bounding_box_2d_tight_fast', 'bounding_box_3d', 'bounding_box_3d_fast', 'instance_id_segmentation', 'instance_id_segmentation_fast', 'instance_segmentation', 'instance_segmentation_fast', 'motion_vectors', 'rgb', 'semantic_segmentation', 'skeleton_data'}*

레이캐스터 카메라에서 지원되지 않는 센서 타입의 집합.

#### *property* data *: [CameraData](#isaaclab.sensors.CameraData)*

센서 데이터.

이 프로퍼티는 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 방지하기 위함입니다.

이 프로퍼티에 접근할 때 센서를 업데이트하려면 센서 구현에 다음 코드 스니펫을 사용할 수 있습니다:

```python
# 필요에 따라 센서 업데이트
self._update_outdated_buffers()
# 데이터 반환 (여기서 `_data`는 센서의 데이터)
return self._data
```

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산을 위한 메모리 디바이스.

#### *property* frame *: torch.tensor*

측정치가 이루어진 프레임 번호.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버그 시각화가 구현되었는지 여부.

#### *property* image_shape *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int)]*

카메라 센서의 (높이, 너비)를 포함하는 튜플.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부.

센서가 초기화된 경우 True, 그렇지 않은 경우 False를 반환합니다.

#### mesh_views *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [XformPrimView](isaaclab.sim.views.md#isaaclab.sim.views.XformPrimView) | physx.ArticulationView | physx.RigidBodyView]]* *= {}*

레이캐스팅을 위한 메쉬 뷰를 저장하는 딕셔너리. 모든 인스턴스 간에 공유됨.

키는 메쉬 뷰의 프리미버 경로에 해당하며, 값은 해당 뷰 객체입니다.

#### meshes *: ClassVar[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), wp.Mesh]]* *= {}*

레이캐스팅을 위한 워프 메쉬를 저장하는 딕셔너리. 모든 인스턴스 간에 공유됨.

키는 메쉬의 프리미버 경로에 해당하며, 값은 해당 워프 Mesh 객체입니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 인스턴스 수.

이는 환경당 센서 수에 환경 수를 곱한 값과 같습니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서 내부 상태를 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 센서 ID. 기본값은 None.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 센서 데이터 시각화 여부.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부. 센서가 디버그 시각화를 지원하지 않는 경우 False.

#### set_intrinsic_matrices(matrices: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), focal_length: [float](https://docs.python.org/3/library/functions.html#float) = 1.0, env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

카메라의 내부 행렬을 설정합니다.

* **매개변수:**
  * **matrices** – 카메라의 내부 행렬. 크기는 (N, 3, 3).
  * **focal_length** – 애퍼처 값을 계산할 때 사용할 초점 거리(센티미터 단위). 기본값은 1.0.
  * **env_ids** – 조작할 센서 ID. 기본값은 None으로, 이는 모든 센서 인덱스를 의미함.

#### set_world_poses(positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, orientations: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, env_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None, convention: Literal['opengl', 'ros', 'world'] = 'ros')

지정된 convention을 사용하여 월드 프레임 기준 카메라의 포즈를 설정합니다.

여러 분야에서 카메라 방향에 대한 서로 다른 convention을 사용하기 때문에, 이 메서드를 통해 사용자는 지정된 convention에 따라 카메라 포즈를 설정할 수 있습니다. 가능한 convention은 다음과 같습니다:

- `"opengl"` - 전방 축: -Z - 상 축: +Y - 오프셋이 OpenGL (Usd.Camera) convention에 적용됨
- `"ros"`    - 전방 축: +Z - 상 축: -Y - 오프셋이 ROS convention에 적용됨
- `"world"`  - 전방 축: +X - 상 축: +Z - 오프셋이 월드 프레임 convention에 적용됨

컨벤션에 대한 자세한 내용은 `isaaclab.utils.maths.convert_camera_frame_orientation_convention()`을 참조하십시오.

* **매개변수:**
  * **positions** – 데카르트 좌표(미터 단위). 크기는 (N, 3).
    기본값은 None이며, 이 경우 카메라 위치가 변경되지 않음.
  * **orientations** – (w, x, y, z) 순서의 쿼터니언 방향. 크기는 (N, 4).
    Defaults을 None으로 지정하면, 카메라의 방향은 변경되지 않습니다.
  * **env_ids** – 조작할 센서 ID입니다. 기본값은 None이며, 이는 모든 센서 인덱스를 의미합니다.
  * **convention** – 포즈가 전달되는 규칙입니다. 기본값은 "ros"입니다.
* **예외 발생:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 카메라 프라이머가 설정되지 않은 경우 발생합니다. 먼저 `initialize()` 메서드를 호출해야 합니다.

#### set_world_poses_from_view(eyes: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), targets: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

눈 위치와 룩-앳 타겟 위치로부터 카메라의 포즈를 설정합니다.

* **매개변수:**
  * **eyes** – 카메라 눈의 위치입니다. 형태는 N x 3입니다.
  * **targets** – 볼 대상 위치입니다. 형태는 (N, 3)입니다.
  * **env_ids** – 조작할 센서 ID입니다. 기본값은 None이며, 이는 모든 센서 인덱스를 의미합니다.
* **예외 발생:**
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 카메라 프라이머가 설정되지 않은 경우 발생합니다. 먼저 `initialize()` 메서드를 호출해야 합니다.
  * [**NotImplementedError**](https://docs.python.org/3/library/exceptions.html#NotImplementedError) – 스테이지의 업축이 "Y" 또는 "Z"가 아닌 경우 발생합니다.

### *class* isaaclab.sensors.MultiMeshRayCasterCameraCfg

Bases: [`RayCasterCameraCfg`](#isaaclab.sensors.RayCasterCameraCfg), [`MultiMeshRayCasterCfg`](#isaaclab.sensors.MultiMeshRayCasterCfg)

멀티 메쉬 레이캐스터 카메라 센서의 구성입니다.

**속성:**

| [`prim_path`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.prim_path)                             | 센서의 프라이머 경로(또는 표현식)입니다.                                                              |
|----------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| [`update_period`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.update_period)                     | 센서 버퍼의 업데이트 주기(초 단위)입니다.                                                           |
| [`history_length`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.history_length)                   | 센서 버퍼에 저장할 과거 프레임 수입니다.                                                            |
| [`debug_vis`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.debug_vis)                             | 센서를 시각화할지 여부를 나타냅니다.                                                                |
| [`mesh_prim_paths`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.mesh_prim_paths)                 | 레이캐스트를 수행할 메쉬 프라이머 경로 목록입니다.                                                    |
| [`offset`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.offset)                                   | 센서 프레임이 센서의 부모 프레임에 대해 갖는 오프셋 포즈입니다.                                        |
| [`attach_yaw_only`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.attach_yaw_only)                 | 레이의 시작 위치와 방향이 요 orientation만 추적하는지 여부를 나타냅니다.                             |
| [`ray_alignment`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.ray_alignment)                     | 레이스를 지면에 투영할 프레임을 지정합니다.                                                          |
| [`pattern_cfg`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.pattern_cfg)                         | 핀홀 카메라 패턴에서의 로컬 레이 시작 위치와 방향을 정의하는 패턴입니다.                              |
| [`max_distance`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.max_distance)                       | 센서로부터 레이캐스트를 수행할 최대 거리(미터 단위)입니다.                                            |
| [`drift_range`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.drift_range)                         | 월드 프레임에서 레이 시작 위치(xyz)에 추가할 드리프트 범위(미터 단위)입니다.                        |
| [`ray_cast_drift_range`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.ray_cast_drift_range)       | 로컬 투영 프레임에서 투영된 레이 포인트에 추가할 드리프트 범위(미터 단위)입니다.                    |
| [`visualizer_cfg`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.visualizer_cfg)                   | 시각화 마커에 대한 구성 객체입니다.                                                                 |
| [`update_mesh_ids`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.update_mesh_ids)                 | `data` 컨테이너의 레이 히트 메쉬 ID를 업데이트할지 여부를 나타냅니다.                               |
| [`reference_meshes`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.reference_meshes)               | 메모리에 각각 별도로 로드하는 대신 중복된 메쉬를 참조할지 여부를 나타냅니다.                        |
| [`data_types`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.data_types)                           | 카메라에 활성화할 센서 이름/유형 목록입니다.                                                        |
| [`depth_clipping_behavior`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.depth_clipping_behavior) | 최대값을 초과하는 값에 대한 카메라의 깊이 클리핑 동작입니다.                                        |

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 프라이머 경로(또는 표현식)입니다.

#### NOTE
이 표현식은 환경 이름SPACE 정규식 `{ENV_REGEX_NS}`를 포함할 수 있으며, 이는 환경 이름SPACE로 대체됩니다.

예: `{ENV_REGEX_NS}/Robot/sensor`은 `/World/envs/env_.*/Robot/sensor`로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼의 업데이트 주기(초 단위)입니다. 기본값은 0.0(매 스텝마다 업데이트)입니다.

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수입니다. 기본값은 0이며, 이는 현재 데이터만 저장됨을 의미합니다(히스토리 없음).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서를 시각화할지 여부를 나타냅니다. 기본값은 False입니다.

#### mesh_prim_paths *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

레이캐스트를 수행할 메쉬 프라이머 경로 목록입니다.

항목이 문자열인 경우, 내부적으로 `RaycastTargetCfg`로 변환되며, `track_mesh_transforms`은 비활성화됩니다. 이러한 설정은 기본 레이캐스터와의 하위 호환성을 보장합니다.

#### offset *: OffsetCfg*

센서 프레임이 센서의 부모 프레임에 대해 갖는 오프셋 포즈입니다. 기본값은 항등 변환입니다.

#### attach_yaw_only *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

레이의 시작 위치와 방향이 요 orientation만 추적하는지 여부를 나타냅니다. 기본값은 None이며, 이는 더 이상 사용되지 않는 기능에 대한 경고를 발생시키지 않습니다.

이 기능은 요 회전만 필요한 높이 맵 레이캐스팅에 유용합니다.

#### Deprecated
버전 2.1.1부터 지원 중단되었습니다. 이 속성은 향후 제거될 예정입니다. 대신
[`ray_alignment`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.ray_alignment)를 사용하세요.

이 파라미터를 `True` 또는 `False`로 설정한 것과 동일한 동작을 얻으려면
[`ray_alignment`](#isaaclab.sensors.MultiMeshRayCasterCameraCfg.ray_alignment)를 `"yaw"` 또는 `"base"`로 설정하세요.

#### ray_alignment *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['base', 'yaw', 'world']*

레이스를 지면에 투영할 프레임을 지정합니다. 기본값은 "base"입니다.

옵션은 다음과 같습니다:
* `base`이면, 레이의 시작 위치와 방향이 루트의 전체 위치와 방향을 추적합니다.
* `yaw`이면, 레이의 시작 위치와 방향이 루트의 위치와 방향의 요 성분만 추적합니다. 이는 높이 맵 레이캐스팅에 유용합니다.
* `world`이면, 레이의 시작 위치와 방향이 항상 고정됩니다. 이는 로봇의 매핑 패키지와 결합하여 전역 프레임에서 레이캐스트를 쿼리하는 데 유용합니다.

#### pattern_cfg *: [PinholeCameraPatternCfg](isaaclab.sensors.patterns.md#isaaclab.sensors.patterns.PinholeCameraPatternCfg)*

핀홀 카메라 패턴에서의 로컬 레이 시작 위치와 방향을 정의하는 패턴입니다.

#### max_distance *: [float](https://docs.python.org/3/library/functions.html#float)*

센서로부터 레이캐스트를 수행할 최대 거리(미터 단위)입니다. 기본값은 1e6입니다.

#### drift_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

월드 프레임에서 레이 시작 위치(xyz)에 추가할 드리프트 범위(미터 단위)입니다. 기본값은 (0.0, 0.0)입니다.

부동 기준 로봇의 경우, 이는 로봇의 포즈 추정에서의 드리프트를 시뮬레이션하는 데 유용합니다.

#### ray_cast_drift_range *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]]*

로컬 투영 프레임에서 투영된 레이 포인트에 추가할 드리프트 범위(미터 단위)입니다. 기본값은 x, y, z 축 각각에 대해 0 드리프트를 갖는 딕셔너리입니다.

부동 기준 로봇의 경우, 이는 로봇의 포즈 추정에서의 드리프트를 시뮬레이션하는 데 유용합니다.

#### visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

시각화 마커에 대한 구성 객체입니다. 기본값은 RAY_CASTER_MARKER_CFG입니다.

#### NOTE
이 속성은 디버그 시각화가 활성화된 경우에만 사용됩니다.

#### update_mesh_ids *: [bool](https://docs.python.org/3/library/functions.html#bool)*

`data` 컨테이너의 레이 히트 메시 ID를 업데이트할지 여부를 결정합니다.

#### reference_meshes *: [bool](https://docs.python.org/3/library/functions.html#bool)*

메시 중복을 메모리에 별도로 로드하는 대신 참조할지 여부를 결정합니다.
기본값은 True입니다.

활성화되면 레이캐스터가 모든 환경의 모든 메시을 파싱하지만 중복에 대해 복사본을 여러 개 저장하는 대신 참조를 재사용합니다. 이렇게 하면 메모리 사용량이 줄어듭니다.

#### data_types *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

카메라용으로 활성화할 센서 이름/타입 목록입니다. 기본값은 [`"distance_to_image_plane"`]입니다.

#### depth_clipping_behavior *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['max', 'zero', 'none']*

카메라의 최대값을 초과하는 값에 대한 클리핑 동작입니다. 기본값은 `"none"`입니다.

- `"max"`: 값을 최대값으로 클리핑합니다.
- `"zero"`: 값을 0으로 클리핑합니다.
- `"none"`: 클리핑을 적용하지 않습니다. `distance_to_camera` 데이터 유형의 값은 `inf`로, `distance_to_image_plane` 데이터 유형의 값은 `nan`으로 반환됩니다.

## 관성 측정 장치

### *class* isaaclab.sensors.Imu

Bases: [`SensorBase`](#isaaclab.sensors.SensorBase)

관성 측정 장치(IMU) 센서입니다.

이 센서는 rigid한 조상이 있는 모든 prim 경로에 부착할 수 있으며, 몸체 프레임에서의 선형 가속도와 각속도, 월드 프레임에서의 포즈, 그리고 몸체 프레임에서의 선형 및 각 가속도/속도를 생성합니다.

제공된 경로가 rigid한 본체가 아닌 경우, 가장 가까운 rigid한 본체 조상이 시뮬레이션 쿼리에 사용됩니다. 해당 조상에서 대상 prim까지의 고정 변환은 초기화 중에 한 번 계산되고 구성된 센서 오프셋과 결합됩니다.

#### NOTE
속도는 수치 미분을 통해 가속도를 계산하기 때문에, IMU 센서의 정확도는 선택된 physx 타임스텝에 따라 달라집니다. 충분한 정확도를 위해 타임스텝을 최소 200Hz로 유지하는 것을 권장합니다.

#### NOTE
사용자는 구성 파일에서 센서 오프셋을 설정할 수 있습니다. 오프셋은 rigid 소스 prim에 상대적으로 적용됩니다. 대상 prim이 rigid한 본체가 아닌 경우, 오프셋은 rigid한 조상에서 대상 prim까지의 고정 변환과 결합됩니다. 오프셋은 rigid 소스 prim의 몸체 프레임에서 적용됩니다. 오프셋은 위치 벡터와 쿼터니언 회전으로 정의되며, 순서대로 위치 적용 후 회전 적용됩니다. 위치는 rigid 소스 prim의 몸체 프레임에서의 trasl레이션으로 적용되고, 회전은 rigid 소스 prim의 몸체 프레임에서의 회전으로 적용됩니다.

**속성:**

| [`cfg`](#isaaclab.sensors.Imu.cfg)                                                   | 구성 매개변수입니다.                             |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`data`](#isaaclab.sensors.Imu.data)                                                 | 센서에서의 데이터입니다.                                     |
| [`num_instances`](#isaaclab.sensors.Imu.num_instances)                               | 센서 인스턴스 수입니다.                        |
| [`device`](#isaaclab.sensors.Imu.device)                                             | 계산에 사용되는 메모리 장치입니다.                            |
| [`has_debug_vis_implementation`](#isaaclab.sensors.Imu.has_debug_vis_implementation) | 센서에 디버그 시각화가 구현되었는지 여부입니다. |
| [`is_initialized`](#isaaclab.sensors.Imu.is_initialized)                             | 센서가 초기화되었는지 여부입니다.                             |

**메서드:**

| [`__init__`](#isaaclab.sensors.Imu.__init__)(cfg)                 | Imu 센서를 초기화합니다.                |
|-------------------------------------------------------------------|--------------------------------------------|
| [`reset`](#isaaclab.sensors.Imu.reset)([env_ids])                 | 센서 내부 상태를 재설정합니다.               |
| [`set_debug_vis`](#isaaclab.sensors.Imu.set_debug_vis)(debug_vis) | 센서 데이터 시각화 여부를 설정합니다. |

#### cfg *: [ImuCfg](#isaaclab.sensors.ImuCfg)*

구성 매개변수입니다.

#### \_\_init_\_(cfg: [ImuCfg](#isaaclab.sensors.ImuCfg))

Imu 센서를 초기화합니다.

* **매개변수:**
  **cfg** – 구성 매개변수입니다.

#### *property* data *: ImuData*

센서에서의 데이터입니다.

이 속성은 사용자가 데이터에 접근하려고 할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 피하기 위해 수행됩니다.

이 속성에 접근할 때 센서를 업데이트하려면 다음 코드 스니펫을 센서 구현에 사용할 수 있습니다:

```python
# update sensors if needed
self._update_outdated_buffers()
# return the data (where `_data` is the data for the sensor)
return self._data
```

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 인스턴스 수입니다.

이 값은 환경당 센서 수와 환경 수를 곱한 값과 같습니다.

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서 내부 상태를 재설정합니다.

* **매개변수:**
  **env_ids** – 재설정할 센서 ID입니다. 기본값은 None입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 장치입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서에 디버그 시각화가 구현되었는지 여부입니다.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부입니다.

센서가 초기화된 경우 True를 반환하고, 그렇지 않은 경우 False를 반환합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 센서 데이터를 시각화할지 여부입니다.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부입니다. 센서가 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

### *class* isaaclab.sensors.ImuCfg

Bases: [`SensorBaseCfg`](#isaaclab.sensors.SensorBaseCfg)

관성 측정 장치(IMU) 센서의 구성입니다.

**클래스:**

| [`OffsetCfg`](#isaaclab.sensors.ImuCfg.OffsetCfg)   | 센서 프레임과 센서 부모 프레임 사이의 오프셋 포즈입니다.   |
|-----------------------------------------------------|-------------------------------------------------------------------------|

**속성:**

| [`prim_path`](#isaaclab.sensors.ImuCfg.prim_path)           | 센서의 prim 경로(또는 표현식)입니다.                                                    |
|-------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| [`update_period`](#isaaclab.sensors.ImuCfg.update_period)   | 센서 버퍼의 업데이트 주기(초 단위)입니다.                                           |
| [`history_length`](#isaaclab.sensors.ImuCfg.history_length) | 센서 버퍼에 저장할 과거 프레임 수입니다.                                       |
| [`debug_vis`](#isaaclab.sensors.ImuCfg.debug_vis)           | 센서 시각화 여부입니다.                                                            |
| [`offset`](#isaaclab.sensors.ImuCfg.offset)                 | 센서 프레임과 센서 부모 프레임 사이의 오프셋 포즈입니다.                       |
| [`visualizer_cfg`](#isaaclab.sensors.ImuCfg.visualizer_cfg) | 시각화 마커의 구성 객체입니다.                                     |
| [`gravity_bias`](#isaaclab.sensors.ImuCfg.gravity_bias)     | 월드 프레임의 선형 가속도에 적용되는 선형 가속도 편향(x, y, z)입니다. |

#### *class* OffsetCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

센서 프레임과 센서 부모 프레임 사이의 오프셋 포즈입니다.

**속성:**

| [`pos`](#isaaclab.sensors.ImuCfg.OffsetCfg.pos)   | 부모 프레임에 대한 이동입니다.                      |
|---------------------------------------------------|-----------------------------------------|
| [`rot`](#isaaclab.sensors.ImuCfg.OffsetCfg.rot)   | 부모 프레임에 대한 쿼터니언 회전(w, x, y, z)입니다. |

#### pos *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임에 대한 이동입니다. 기본값은 (0.0, 0.0, 0.0)입니다.

#### rot *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

부모 프레임에 대한 쿼터니언 회전(w, x, y, z)입니다. 기본값은 (1.0, 0.0, 0.0, 0.0)입니다.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 prim 경로(또는 표현식)입니다.

#### NOTE
경로 표현식에는 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot/sensor`는 `/World/envs/env_.*/Robot/sensor`로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼의 업데이트 주기(초 단위)입니다. 기본값은 0.0(매 스텝마다 업데이트)입니다.

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수입니다. 기본값은 0으로, 이는 현재 데이터만 저장됨을 의미합니다(히스토리 없음).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서 시각화 여부입니다. 기본값은 False입니다.

#### offset *: [OffsetCfg](#isaaclab.sensors.ImuCfg.OffsetCfg)*

센서 프레임과 센서 부모 프레임 사이의 오프셋 포즈입니다. 기본값은 항등 변환입니다.

#### visualizer_cfg *: [VisualizationMarkersCfg](isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

시각화 마커의 구성 객체입니다. 기본값은 RED_ARROW_X_MARKER_CFG입니다.

이 속성은 디버그 시각화가 활성화된 경우에만 사용됩니다.

#### gravity_bias *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

월드 프레임의 선형 가속도에 적용되는 선형 가속도 편향(x, y, z)입니다.

IMU 센서는 일반적으로 중력 방향과 반대인 양의 중력 가속도를 출력합니다. 이 구성 매개변수를 사용하면 (0.,0.,0.)로 설정하여 해당 편차를 뺄 수 있습니다. 기본값은 (0.0,0.0,9.81)로 설정되어 월드 Z 방향에서 양의 가속도 읽기 값을 제공합니다.
