# isaaclab_contrib.sensors

외부에서 기여된 센서들을 위한 서브 패키지입니다.

이 패키지는 Isaac Lab에서 외부에서 기여된 센서들을 시뮬레이션하기 위한 전문적인 센서 클래스들을 제공합니다. 이러한 센서들은 아직 핵심 Isaac Lab 프레임워크의 일부가 아니지만, 미래에 추가될 예정입니다. Isaac Lab의 기능을 확장하기 위해 커뮤니티에 의해 기여되었습니다.

[`isaaclab.sensors`](../lab/isaaclab.sensors.md#module-isaaclab.sensors) 서브 패키지의 분류를 따르는 경우, 센서의 구성 클래스에 전달되는 프라임 경로는 센서 유형에 따라 다르게 해석됩니다. 다음 표는 다양한 센서 유형의 프라임 경로 해석을 요약합니다:

| 센서 유형          | 예시 프라임 경로   | 사전 검사                                      |
|--------------------|--------------------|------------------------------------------------|
| 비주얼-택타일 센서 | /World/robot/base  | 리프가 존재하고 물리 본체(강체)인지 확인       |

### 클래스

| [`VisuoTactileSensor`](#isaaclab_contrib.sensors.VisuoTactileSensor)         | 카메라 기반 및 힘 필드 택타일 센싱을 위한 택타일 센서입니다.   |
|------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [`VisuoTactileSensorCfg`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg)   | 비주얼-택타일 센서에 대한 구성입니다.                             |
| [`VisuoTactileSensorData`](#isaaclab_contrib.sensors.VisuoTactileSensorData) | 비주얼-택타일 센서를 위한 데이터 컨테이너입니다.                    |
| [`GelSightRenderCfg`](#isaaclab_contrib.sensors.GelSightRenderCfg)           | GelSight 센서 렌더링 매개변수에 대한 구성입니다.                   |

## 비주얼-택타일 센서

### *class* isaaclab_contrib.sensors.VisuoTactileSensor

기반 클래스: [`SensorBase`](../lab/isaaclab.sensors.md#isaaclab.sensors.SensorBase)

카메라 기반 및 힘 필드 택타일 센싱을 위한 택타일 센서입니다.

이 센서는 다음과 같은 기능을 제공합니다:
1. 카메라 기반 택타일 센싱: 택타일 표면에서의 깊이 이미지
2. 힘 필드 택타일 센싱: SDF 쿼리를 사용한 페널티 기반 법선 및 전단 힘

이 센서는 하나 또는 두 가지 감지 모드를 모두 사용하도록 구성할 수 있습니다.

**계산 파이프라인:**
: 카메라 기반 센싱은 노말(무접촉) 기준선과의 깊이 차이를 계산하고, 이를 통해 슬-겔사이트 렌더러를 통과시켜 현실적인 택타일 이미지를 생성합니다.
  <br/>
  힘 필드 센싱은 부호 거리 함수(SDF)를 쿼리하여 침투 깊이를 계산한 후, 페널티 기반 스프링-댐퍼 모델을 적용합니다.
  ($F_n = k_n \cdot \text{depth}$, $F_t = \min(k_t \cdot \|v_t\|, \mu \cdot F_n)$)
  이산적인 택타일 지점에서 법선 및 전단 힘을 계산하기 위해 적용됩니다.

**사용 예시:**
: 전체 작동 예시는 다음을 참조하세요: `scripts/demos/sensors/tacsl/tacsl_example.py`

**현재 제한 사항:**
: - SDF 충돌 메시는 시뮬레이션 시작 전에 사전 계산되어야 하고, 객체들이 명시되어야 합니다
  - 힘 필드 계산은 특정 강체 및 메시 구성을 필요로 합니다
  - 런타임 중 동적으로 상호작용하는 객체의 추가/제거를 지원하지 않습니다

구성 요구 사항:
: 올바른 센서 작동을 위해 다음 요구 사항을 충족해야 합니다:
  <br/>
  **카메라 택타일 이미징**
  : `enable_camera_tactile=True`인 경우, 적절한 카메라 매개변수를 갖는 유효한 `camera_cfg` (TiledCameraCfg)가 제공되어야 합니다.
  <br/>
  **힘 필드 계산**
  : `enable_force_field=True`인 경우, 다음 매개변수가 필요합니다:
    <br/>
    * `contact_object_prim_path_expr` - 접촉 객체 프라임을 찾기 위한 프라임 경로 표현식
  <br/>
  **SDF 계산**
  : 힘 필드 계산이 활성화된 경우, 부호 거리 필드(SDF) 쿼리를 사용하여 페널티 기반 법선 및 전단 힘이 계산됩니다. GPU 가속을 달성하려면:
    <br/>
    * 상호작용하는 객체들은 사전 계산된 SDF 충돌 메시를 가져야 합니다
    * 초기화 중에 SDFView가 정의되어야 하므로, 상호작용하는 객체들은 시뮬레이션 시작 전에 지정되어야 합니다.

**속성:**

| [`cfg`](#isaaclab_contrib.sensors.VisuoTactileSensor.cfg)                                                   | 구성 매개변수입니다.                             |
|-------------------------------------------------------------------------------------------------------------|--------------------------------------------------|
| [`num_instances`](#isaaclab_contrib.sensors.VisuoTactileSensor.num_instances)                               | 센서의 인스턴스 수입니다.                        |
| [`data`](#isaaclab_contrib.sensors.VisuoTactileSensor.data)                                                 | 센서에서 나오는 데이터입니다.                     |
| [`device`](#isaaclab_contrib.sensors.VisuoTactileSensor.device)                                             | 계산에 사용되는 메모리 장치입니다.               |
| [`has_debug_vis_implementation`](#isaaclab_contrib.sensors.VisuoTactileSensor.has_debug_vis_implementation) | 센서가 디버그 시각화를 구현했는지 여부입니다.   |
| [`is_initialized`](#isaaclab_contrib.sensors.VisuoTactileSensor.is_initialized)                             | 센서가 초기화되었는지 여부입니다.                |

**메서드:**

| [`__init__`](#isaaclab_contrib.sensors.VisuoTactileSensor.__init__)(cfg)                  | 택타일 센서 객체를 초기화합니다.                         |
|-------------------------------------------------------------------------------------------|----------------------------------------------------------|
| [`reset`](#isaaclab_contrib.sensors.VisuoTactileSensor.reset)([env_ids])                  | 센서 내부 상태를 재설정합니다.                           |
| [`get_initial_render`](#isaaclab_contrib.sensors.VisuoTactileSensor.get_initial_render)() | 기준 비교를 위한 초기 택타일 센서 렌더를 가져옵니다.     |
| [`set_debug_vis`](#isaaclab_contrib.sensors.VisuoTactileSensor.set_debug_vis)(debug_vis)  | 센서 데이터 시각화 여부를 설정합니다.                    |

#### cfg *: [VisuoTactileSensorCfg](#isaaclab_contrib.sensors.VisuoTactileSensorCfg)*

구성 매개변수입니다.

#### \_\_init_\_(cfg: [VisuoTactileSensorCfg](#isaaclab_contrib.sensors.VisuoTactileSensorCfg))

택타일 센서 객체를 초기화합니다.

* **매개변수:**
  **cfg** – 구성 매개변수입니다.

#### *property* num_instances *: [int](https://docs.python.org/3/library/functions.html#int)*

센서의 인스턴스 수입니다.

이 값은 환경당 센서 수와 환경 수를 곱한 값과 같습니다.

#### *property* data *: [VisuoTactileSensorData](#isaaclab_contrib.sensors.VisuoTactileSensorData)*

센서에서 나오는 데이터입니다.

이 속성은 사용자가 데이터를 접근하려고 할 때만 업데이트됩니다. 이는 센서 데이터가 사용되지 않을 때 불필요한 계산을 피하기 위해 수행됩니다.

이 속성에 접근할 때 센서를 업데이트하려면, 센서 구현에서 다음 코드 스니펫을 사용할 수 있습니다:

```python
# 필요에 따라 센서 업데이트
self._update_outdated_buffers()
# 데이터 반환 (여기서 `_data`는 센서 데이터를 나타냄)
return self._data
```

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

센서 내부 상태를 재설정합니다.

#### get_initial_render() → [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [None](https://docs.python.org/3/library/constants.html#None)

기준 비교를 위한 초기 택타일 센서 렌더를 가져옵니다.

이 메서드는 접촉이 발생하지 않는 상태에서의 택타일 센서의 초기 상태를 캡처합니다. 이 기준선은 택타일 상호작용 중 상대적인 변화를 계산하는 데 사용됩니다.

#### 경고
이 메서드를 호출할 때 센서가 "무접촉" 상태인지 확인하는 것은 사용자의 책임입니다. 센서가 객체와 접촉 중이면, 기준선이 잘못되어 오류가 있는 택타일 읽기를 초래할 수 있습니다.

* **반환값:**
  센서 출력 키와 해당 텐서 값으로 구성된 초기 렌더 데이터가 포함된 사전. 카메라 택타일 센싱이 비활성화된 경우 None을 반환합니다.
* **반환 유형:**
  [dict](https://docs.python.org/3/library/stdtypes.html#dict) | None
* **예외 발생:**
  [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – 카메라 센서가 초기화되지 않았거나 초기 렌더링에 실패한 경우.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

계산에 사용되는 메모리 장치입니다.

#### *property* has_debug_vis_implementation *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 디버그 시각화를 구현했는지 여부입니다.

#### *property* is_initialized *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서가 초기화되었는지 여부입니다.

센서가 초기화된 경우 True를 반환하고, 그렇지 않은 경우 False를 반환합니다.

#### set_debug_vis(debug_vis: [bool](https://docs.python.org/3/library/functions.html#bool)) → [bool](https://docs.python.org/3/library/functions.html#bool)

센서 데이터 시각화 여부를 설정합니다.

* **매개변수:**
  **debug_vis** – 센서 데이터를 시각화할지 여부입니다.
* **반환값:**
  디버그 시각화가 성공적으로 설정되었는지 여부를 반환합니다. 센서가 디버그 시각화를 지원하지 않는 경우 False를 반환합니다.

### *class* isaaclab_contrib.sensors.VisuoTactileSensorData

비주얼-택타일 센서를 위한 데이터 컨테이너입니다.

이 클래스는 다음과 같은 택타일 센서 데이터를 포함합니다:

- 카메라 기반 택타일 센싱 (RGB 및 깊이 이미지)
- 힘 필드 택타일 센싱 (법선 및 전단 힘)
- 택타일 점 위치 및 접촉 정보

**속성:**

| [`tactile_depth_image`](#isaaclab_contrib.sensors.VisuoTactileSensorData.tactile_depth_image)     | 택타일 깊이 이미지입니다.                                                                                            |
|---------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| [`tactile_rgb_image`](#isaaclab_contrib.sensors.VisuoTactileSensorData.tactile_rgb_image)         | Si와 Yuan [[SY22](../../refs/bibliography.md#id23)]의 Taxim 접근 방식을 사용한 촉각 RGB 이미지 렌더링 결과. |
| [`tactile_points_pos_w`](#isaaclab_contrib.sensors.VisuoTactileSensorData.tactile_points_pos_w)   | 월드 프레임에서의 촉각 점 위치.                                                                      |
| [`tactile_points_quat_w`](#isaaclab_contrib.sensors.VisuoTactileSensorData.tactile_points_quat_w) | 월드 프레임에서의 촉각 점 방향.                                                                   |
| [`penetration_depth`](#isaaclab_contrib.sensors.VisuoTactileSensorData.penetration_depth)         | 각 촉각 점에서의 관통 깊이.                                                                         |
| [`tactile_normal_force`](#isaaclab_contrib.sensors.VisuoTactileSensorData.tactile_normal_force)   | 센서 프레임에서의 각 촉각 점에 대한 수직 힘.                                                             |
| [`tactile_shear_force`](#isaaclab_contrib.sensors.VisuoTactileSensorData.tactile_shear_force)     | 센서 프레임에서의 각 촉각 점에 대한 전단 힘.                                                              |

#### tactile_depth_image *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

촉각 깊이 이미지. 형태는 (num_instances, height, width, 1).

#### tactile_rgb_image *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

Si와 Yuan [[SY22](../../refs/bibliography.md#id23)]의 Taxim 접근 방식을 사용한 촉각 RGB 이미지 렌더링 결과. 형태는 (num_instances, height, width, 3).

#### tactile_points_pos_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

월드 프레임에서의 촉각 점 위치. 형태는 (num_instances, num_tactile_points, 3).

#### tactile_points_quat_w *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

월드 프레임에서의 촉각 점 방향. 형태는 (num_instances, num_tactile_points, 4).

#### penetration_depth *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

각 촉각 점에서의 관통 깊이. 형태는 (num_instances, num_tactile_points).

#### tactile_normal_force *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

센서 프레임에서의 각 촉각 점에 대한 수직 힘. 형태는 (num_instances, num_tactile_points).

#### tactile_shear_force *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

센서 프레임에서의 각 촉각 점에 대한 전단 힘. 형태는 (num_instances, num_tactile_points, 2).

### *class* isaaclab_contrib.sensors.VisuoTactileSensorCfg

Bases: [`SensorBaseCfg`](../lab/isaaclab.sensors.md#isaaclab.sensors.SensorBaseCfg)

비주얼-촉각 센서 구성.

이 센서는 카메라 기반 촉각 감지와 힘장 기반 촉각 감지를 모두 제공합니다. 촉각 RGB/깊이 이미지를 캡처하고 페널티 기반 접촉 힘을 계산할 수 있습니다.

**속성:**

| [`render_cfg`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.render_cfg)                                       | GelSight 센서 렌더링 구성.                                 |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| [`enable_camera_tactile`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.enable_camera_tactile)                 | 카메라 기반 촉각 감지 활성화 여부.                              |
| [`prim_path`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.prim_path)                                         | 센서의 Prim 경로(또는 표현식).                                    |
| [`update_period`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.update_period)                                 | 센서 버퍼 업데이트 주기(초 단위).                                |
| [`history_length`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.history_length)                               | 센서 버퍼에 저장할 과거 프레임 수.                                |
| [`debug_vis`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.debug_vis)                                         | 센서 시각화 여부.                                             |
| [`enable_force_field`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.enable_force_field)                       | 힘장 기반 촉각 감지 활성화 여부.                               |
| [`tactile_array_size`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.tactile_array_size)                       | 힘장 감지를 위한 촉각 점 개수(행, 열) 형식.                     |
| [`tactile_margin`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.tactile_margin)                               | 촉각 점 생성 마진(미터 단위).                                   |
| [`contact_object_prim_path_expr`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.contact_object_prim_path_expr) | 힘장 계산을 위한 접촉 객체 찾기 위한 Prim 경로 표현식. |
| [`normal_contact_stiffness`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.normal_contact_stiffness)           | 페널티 기반 힘 계산에 대한 수직 접촉 강성.                |
| [`friction_coefficient`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.friction_coefficient)                   | 전단 힘에 대한 마찰 계수.                                       |
| [`tangential_stiffness`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.tangential_stiffness)                   | 전단 힘에 대한 접선 강성.                                       |
| [`camera_cfg`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.camera_cfg)                                       | 촉각 RGB/깊이 감지를 위한 카메라 구성.                          |
| [`visualizer_cfg`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.visualizer_cfg)                               | 시각화 마커를 위한 구성 객체.                                    |
| [`trimesh_vis_tactile_points`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.trimesh_vis_tactile_points)       | trimesh를 사용한 촉각 점 시각화(디버깅용) 여부.                   |
| [`visualize_sdf_closest_pts`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.visualize_sdf_closest_pts)         | SDF 최접근점 시각화(디버깅용) 여부.                              |

#### render_cfg *: [GelSightRenderCfg](#isaaclab_contrib.sensors.GelSightRenderCfg)*

GelSight 센서 렌더링 구성.

깊이 맵을 현실적인 촉각 이미지로 변환하기 위한 렌더링 매개변수를 정의합니다.

간단히 하기 위해 표준 센서 모델에 대한 미리 정의된 구성을 사용할 수 있습니다:

- `isaaclab_assets.sensors.GELSIGHT_R15_CFG`
- `isaaclab_assets.sensors.GELSIGHT_MINI_CFG`

#### enable_camera_tactile *: [bool](https://docs.python.org/3/library/functions.html#bool)*

카메라 기반 촉각 감지 활성화 여부.

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서의 Prim 경로(또는 표현식).

#### NOTE
이 표현식에는 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/Robot/sensor`은 `/World/envs/env_.*/Robot/sensor`로 대체됩니다.

#### update_period *: [float](https://docs.python.org/3/library/functions.html#float)*

센서 버퍼 업데이트 주기(초 단위). 기본값은 0.0(매 단계 업데이트).

#### history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

센서 버퍼에 저장할 과거 프레임 수. 기본값은 0으로, 이는 오직 현재 데이터만 저장됨을 의미합니다(기록 없음).

#### debug_vis *: [bool](https://docs.python.org/3/library/functions.html#bool)*

센서 시각화 여부. 기본값은 False.

#### enable_force_field *: [bool](https://docs.python.org/3/library/functions.html#bool)*

힘장 기반 촉각 감지 활성화 여부.

#### tactile_array_size *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int)]*

힘장 감지를 위한 촉각 점 개수(행, 열) 형식.

#### tactile_margin *: [float](https://docs.python.org/3/library/functions.html#float)*

촉각 점 생성 마진(미터 단위).

이 매개변수는 촉각 점 격자 생성을 위해 엘라스토머 메쉬 가장자리에서의 배제 마진을 정의합니다. 이는 힘장 점이 기하학적 불안정성 또는 관련성이 낮을 수 있는 센서 표면의 가장자리에서 생성되지 않도록 보장합니다.

#### contact_object_prim_path_expr *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

힘장 계산을 위한 접촉 객체 찾기 위한 Prim 경로 표현식.

이 문서는 촉각 센서와 접촉하는 객체를 지정합니다. 센서는 최적의 힘 필드 계산을 위해 이 객체 내에서 SDF 충돌 메시를 자동으로 찾습니다.

#### 참고
식에는 환경 네임스페이스 정규식 `{ENV_REGEX_NS}`이 포함될 수 있으며, 이는 환경 네임스페이스로 대체됩니다.

예시: `{ENV_REGEX_NS}/ContactObject`은 `/World/envs/env_.*/ContactObject`로 대체됩니다.

#### 주의
힘 필드 계산을 제대로 작동시키려면 접촉 객체에 SDF 충돌 메시가 있어야 합니다.
센서는 지정된 프라임 계층 구조 내에서 첫 번째 SDF 메시를 검색합니다.

#### normal_contact_stiffness *: [float](https://docs.python.org/3/library/functions.html#float)*

페널티 기반 힘 계산을 위한 일반 접촉 강성.

#### friction_coefficient *: [float](https://docs.python.org/3/library/functions.html#float)*

전단 힘을 위한 마찰 계수.

#### tangential_stiffness *: [float](https://docs.python.org/3/library/functions.html#float)*

전단 힘을 위한 접선 강성.

#### camera_cfg *: [TiledCameraCfg](../lab/isaaclab.sensors.md#isaaclab.sensors.TiledCameraCfg) | [None](https://docs.python.org/3/library/constants.html#None)*

촉각 RGB/깊이 감지를 위한 카메라 구성.

None인 경우, [`enable_camera_tactile`](#isaaclab_contrib.sensors.VisuoTactileSensorCfg.enable_camera_tactile)가 True라도 카메라 기반 감지는 비활성화됩니다.

#### visualizer_cfg *: [VisualizationMarkersCfg](../lab/isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg)*

시각화 마커에 대한 구성 객체.

#### 참고
이 속성은 디버그 시각화가 활성화된 경우에만 사용됩니다.

#### trimesh_vis_tactile_points *: [bool](https://docs.python.org/3/library/functions.html#bool)*

trimesh를 사용하여 촉각 점을 디버깅 목적으로 시각화할지 여부. 기본값은 False입니다.

#### visualize_sdf_closest_pts *: [bool](https://docs.python.org/3/library/functions.html#bool)*

디버깅 목적으로 SDF 가장 가까운 점을 시각화할지 여부. 기본값은 False입니다.

### *class* isaaclab_contrib.sensors.GelSightRenderCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

GelSight 센서 렌더링 매개변수에 대한 구성.

이 구성은 Taxim 접근법을 사용한 예시 기반 촉각 이미지 합성을 위한 렌더링 매개변수를 정의합니다.

참조:
: Si, Z., & Yuan, W. (2022). Taxim: An example-based simulation model for GelSight
  tactile sensors. IEEE Robotics and Automation Letters, 7(2), 2361-2368.
  [https://arxiv.org/abs/2109.04027](https://arxiv.org/abs/2109.04027)

데이터 디렉터리 구조:
: 센서 데이터는 다음 구조로 구성되어야 합니다:
  <br/>
  ```default
  base_data_path/
  └── sensor_data_dir_name/
      ├── bg.jpg              # 배경 이미지 (필수)
      ├── polycalib.npz       # 다항식 보정 데이터 (필수)
      └── real_bg.npy         # 실제 배경 데이터 (선택 사항)
  ```

### 예시

미리 정의된 센서 구성 사용:

```default
from isaaclab_contrib.sensors.tacsl_sensor import VisuoTactileSensorCfg

from isaaclab_assets.sensors import GELSIGHT_R15_CFG

sensor_cfg = VisuoTactileSensorCfg(render_cfg=GELSIGHT_R15_CFG)
```

맞춤 센서 데이터 사용:

```default
custom_cfg = GelSightRenderCfg(
    base_data_path="/path/to/my/sensors",
    sensor_data_dir_name="my_custom_sensor",
    image_height=480,
    image_width=640,
    mm_per_pixel=0.05,
)
```

**속성:**

| [`base_data_path`](#isaaclab_contrib.sensors.GelSightRenderCfg.base_data_path)             | 센서 보정 데이터를 포함하는 디렉터리의 기본 경로.                                   |
|--------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| [`sensor_data_dir_name`](#isaaclab_contrib.sensors.GelSightRenderCfg.sensor_data_dir_name) | 센서 보정 및 배경 데이터를 포함하는 디렉터리 이름.                            |
| [`background_path`](#isaaclab_contrib.sensors.GelSightRenderCfg.background_path)           | 데이터 디렉터리 내 배경 이미지 파일 이름.                                      |
| [`calib_path`](#isaaclab_contrib.sensors.GelSightRenderCfg.calib_path)                     | 데이터 디렉터리 내 다항식 보정 데이터 파일 이름.                           |
| [`real_background`](#isaaclab_contrib.sensors.GelSightRenderCfg.real_background)           | 데이터 디렉터리 내 실제 배경 데이터 파일 이름.                                  |
| [`image_height`](#isaaclab_contrib.sensors.GelSightRenderCfg.image_height)                 | 촉각 이미지의 픽셀 단위 높이.                                                           |
| [`image_width`](#isaaclab_contrib.sensors.GelSightRenderCfg.image_width)                   | 촉각 이미지의 픽셀 단위 너비.                                                            |
| [`num_bins`](#isaaclab_contrib.sensors.GelSightRenderCfg.num_bins)                         | 그라디언트 크기와 방향 양자화를 위한 바인 수.                                |
| [`mm_per_pixel`](#isaaclab_contrib.sensors.GelSightRenderCfg.mm_per_pixel)                 | 높이 맵에서 2D 촉각 이미지를 재구성하기 위한 픽셀당 밀리미터 변환 계수. |

#### base_data_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서 보정 데이터를 포함하는 디렉터리의 기본 경로. 기본값은 Isaac Lab Nucleus 디렉터리 `{ISAACLAB_NUCLEUS_DIR}/TacSL`입니다.

#### sensor_data_dir_name *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

센서 보정 및 배경 데이터를 포함하는 디렉터리 이름.

이것은 [`base_data_path`](#isaaclab_contrib.sensors.GelSightRenderCfg.base_data_path) 내의 상대 경로(디렉터리 이름이어야 합니다).

#### background_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

데이터 디렉터리 내 배경 이미지 파일 이름.

#### calib_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

데이터 디렉터리 내 다항식 보정 데이터 파일 이름.

#### real_background *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

데이터 디렉터리 내 실제 배경 데이터 파일 이름.

#### image_height *: [int](https://docs.python.org/3/library/functions.html#int)*

촉각 이미지의 픽셀 단위 높이.

#### image_width *: [int](https://docs.python.org/3/library/functions.html#int)*

촉각 이미지의 픽셀 단위 너비.

#### num_bins *: [int](https://docs.python.org/3/library/functions.html#int)*

그라디언트 크기와 방향 양자화를 위한 바인 수.

#### mm_per_pixel *: [float](https://docs.python.org/3/library/functions.html#float)*

높이 맵에서 2D 촉각 이미지를 재구성하기 위한 픽셀당 밀리미터 변환 계수.
