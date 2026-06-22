# isaaclab.sensors.patterns

레이캐스터에서 사용되는 레이캐스팅 패턴을 위한 서브 모듈입니다.

### 클래스

| [`PatternBaseCfg`](#isaaclab.sensors.patterns.PatternBaseCfg)                   | 패턴의 기본 구성입니다.                                       |
|---------------------------------------------------------------------------------|-------------------------------------------------------------|
| [`GridPatternCfg`](#isaaclab.sensors.patterns.GridPatternCfg)                   | 레이캐스팅을 위한 그리드 패턴의 구성입니다.                     |
| [`PinholeCameraPatternCfg`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg) | 레이캐스팅을 위한 핀홀 카메라 깊이 이미지 패턴의 구성입니다. |
| [`BpearlPatternCfg`](#isaaclab.sensors.patterns.BpearlPatternCfg)               | 레이캐스팅을 위한 Bpearl 패턴의 구성입니다.                   |

## 패턴 기본

### *class* isaaclab.sensors.patterns.PatternBaseCfg

패턴의 기본 구성입니다.

**속성:**

| [`func`](#isaaclab.sensors.patterns.PatternBaseCfg.func)   | 패턴을 생성하는 함수입니다.   |
|------------------------------------------------------------|-------------------------------|

#### func *: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)[[[PatternBaseCfg](#isaaclab.sensors.patterns.PatternBaseCfg), [str](https://docs.python.org/3/library/stdtypes.html#str)], [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]*

패턴을 생성하는 함수입니다.

이 함수는 구성과 디바이스 이름을 인수로 받아야 하며, 패턴의 시작 위치와 방향을 토치 텐서의 튜플로 반환해야 합니다.

## 그리드 패턴

### isaaclab.sensors.patterns.grid_pattern(cfg: [patterns_cfg.GridPatternCfg](#isaaclab.sensors.patterns.GridPatternCfg), device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

레이캐스팅을 위한 정규 그리드 패턴입니다.

그리드 패턴은 서로 평행한 광선으로 구성됩니다. 이 광선들은 센서의 로컬 좌표계에서 `(-length/2, -width/2)`부터 `(length/2, width/2)`까지 2D 그리드를 형성합니다. 여기서 `size = (length, width)` 및 `resolution` 매개변수는 구성에 정의됩니다.

* **매개변수:**
  * **cfg** – 패턴의 구성 인스턴스입니다.
  * **device** – 패턴을 생성할 디바이스입니다.
* **반환값:**
  광선의 시작 위치와 방향입니다.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 순서가 “xy” 또는 “yx”가 아닌 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 해상도가 0 이하인 경우.

### *class* isaaclab.sensors.patterns.GridPatternCfg

레이캐스팅을 위한 그리드 패턴의 구성입니다.

센서의 좌표계에서 2D 그리드의 광선을 정의합니다.

#### 주의
포인트는 [`ordering`](#isaaclab.sensors.patterns.GridPatternCfg.ordering) 속성에 따라 정렬됩니다.

**속성:**

| [`resolution`](#isaaclab.sensors.patterns.GridPatternCfg.resolution)   | 그리드 해상도(미터 단위).                            |
|------------------------------------------------------------------------|------------------------------------------------------|
| [`size`](#isaaclab.sensors.patterns.GridPatternCfg.size)               | 그리드 크기(길이, 너비)(미터 단위).                  |
| [`direction`](#isaaclab.sensors.patterns.GridPatternCfg.direction)     | 광선 방향.                                           |
| [`ordering`](#isaaclab.sensors.patterns.GridPatternCfg.ordering)       | 생성된 그리드에서 점의 순서를 지정합니다.            |

#### resolution *: [float](https://docs.python.org/3/library/functions.html#float)*

그리드 해상도(미터 단위).

#### size *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

그리드 크기(길이, 너비)(미터 단위).

#### direction *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

광선 방향. 기본값은 (0.0, 0.0, -1.0)입니다.

#### ordering *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['xy', 'yx']*

생성된 그리드에서 점의 순서를 지정합니다. 기본값은 `"xy"`입니다.

$(x, y)$ 좌표를 가진 격자 패턴의 점에 대해 $x$와 $y$가 격자 인덱스라고 가정해 보겠습니다. 점의 순서는 “xy” 또는 “yx”로 지정할 수 있으며, 이는 격자 점들을 반복할 때 내부 루프와 외부 루프의 순서를 결정합니다.

* “xy”가 선택되면, 점은 “x”에 대한 내부 루프와 “y”에 대한 외부 루프 순서로 정렬됩니다.
* “yx”가 선택되면, 점은 “y”에 대한 내부 루프와 “x”에 대한 외부 루프 순서로 정렬됩니다.

예를 들어, 격자 패턴의 점이 $X = (0, 1, 2)$와 $Y = (3, 4)$일 때:

* “xy” 순서: $[(0, 3), (1, 3), (2, 3), (1, 4), (2, 4), (2, 4)]$
* “yx” 순서: $[(0, 3), (0, 4), (1, 3), (1, 4), (2, 3), (2, 4)]$

## 핀홀 카메라 패턴

### isaaclab.sensors.patterns.pinhole_camera_pattern(cfg: [patterns_cfg.PinholeCameraPatternCfg](#isaaclab.sensors.patterns.PinholeCameraPatternCfg), intrinsic_matrices: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

레이캐스팅을 위한 이미지 패턴입니다.

* **매개변수:**
  * **cfg** – 패턴의 구성 인스턴스입니다.
  * **intrinsic_matrices** – 카메라의 내부 행렬입니다. 크기는 (N, 3, 3)입니다.
  * **device** – 패턴을 생성할 디바이스입니다.
* **반환값:**
  광선의 시작 위치와 방향입니다. 텐서의 형태는 각각 (N, H \* W, 3)와 (N, H \* W, 3)입니다.

### *class* isaaclab.sensors.patterns.PinholeCameraPatternCfg

레이캐스팅을 위한 핀홀 카메라 깊이 이미지 패턴의 구성입니다.

**속성:**

| [`focal_length`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg.focal_length)                             | 원근 초점 거리(센티미터 단위).          |
|---------------------------------------------------------------------------------------------------------------|----------------------------------------|
| [`horizontal_aperture`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg.horizontal_aperture)               | 수평 개구부(센티미터 단위).               |
| [`vertical_aperture`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg.vertical_aperture)                   | 수직 개구부(센티미터 단위).                 |
| [`horizontal_aperture_offset`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg.horizontal_aperture_offset) | 해상도/필름 게이트의 수평 오프셋. |
| [`vertical_aperture_offset`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg.vertical_aperture_offset)     | 해상도/필름 게이트의 수직 오프셋.   |
| [`width`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg.width)                                           | 이미지 너비(픽셀 단위).            |
| [`height`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg.height)                                         | 이미지 높이(픽셀 단위).           |

**메서드:**

| [`from_intrinsic_matrix`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg.from_intrinsic_matrix)(intrinsic_matrix, ...)   | 내부 행렬로부터 [`PinholeCameraPatternCfg`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg) 클래스 인스턴스를 생성합니다.   |
|------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|

#### focal_length *: [float](https://docs.python.org/3/library/functions.html#float)*

원근 초점 거리(센티미터 단위). 기본값은 24.0cm입니다.

더 긴 렌즈 길이는 시야각을 좁히고, 더 짧은 렌즈 길이는 시야각을 넓힙니다.

#### horizontal_aperture *: [float](https://docs.python.org/3/library/functions.html#float)*

수평 개구부(센티미터 단위). 기본값은 20.955 cm입니다.

카메라의 센서/필름 너비를 에뮬레이트합니다.

#### NOTE
기본값은 35mm 구형 프로젝터의 수평 개구부입니다.

#### vertical_aperture *: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)*

수직 개구부(센티미터 단위). 기본값은 None입니다.

카메라의 센서/필름 높이를 에뮬레이트합니다. None인 경우, 이미지의 가로세로 비율을 기반으로 수평 개구부로부터 수직 개구부를 계산하여 정사각형 픽셀을 유지합니다. 이 경우, 수직 개구부는 다음과 같이 계산됩니다:

$$
\text{수직 개구부} = \text{수평 개구부} \times \frac{\text{높이}}{\text{너비}}

$$

#### horizontal_aperture_offset *: [float](https://docs.python.org/3/library/functions.html#float)*

해상도/필름 게이트의 수평 오프셋. 기본값은 0.0입니다.

#### vertical_aperture_offset *: [float](https://docs.python.org/3/library/functions.html#float)*

해상도/필름 게이트의 수직 오프셋. 기본값은 0.0입니다.

#### width *: [int](https://docs.python.org/3/library/functions.html#int)*

이미지 너비(픽셀 단위).

#### height *: [int](https://docs.python.org/3/library/functions.html#int)*

이미지의 높이(픽셀 단위).

#### *classmethod* from_intrinsic_matrix(intrinsic_matrix: [list](https://docs.python.org/3/library/stdtypes.html#list)[[float](https://docs.python.org/3/library/functions.html#float)], width: [int](https://docs.python.org/3/library/functions.html#int), height: [int](https://docs.python.org/3/library/functions.html#int), focal_length: [float](https://docs.python.org/3/library/functions.html#float) = 24.0) → [PinholeCameraPatternCfg](#isaaclab.sensors.patterns.PinholeCameraPatternCfg)

내재 행렬을 기반으로 [`PinholeCameraPatternCfg`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg) 클래스 인스턴스를 생성합니다.

내재 행렬은 3D 세계 좌표와 2D 이미지 사이의 매핑을 정의하는 3x3 행렬입니다. 행렬은 다음과 같이 정의됩니다:

$$
I_{cam} = \begin{bmatrix}
f_x & 0 & c_x \\
0 & f_y & c_y \\
0 & 0 & 1
\end{bmatrix},

$$

여기서 $f_x$ 와 $f_y$ 는 각각 x와 y 방향의 초점 거리이며,
$c_x$ 와 $c_y$ 는 각각 x와 y 방향의 원점 오프셋을 나타냅니다.

* **Parameters:**
  * **intrinsic_matrix** – 행-주 형식을 기준으로 한 카메라의 내재 행렬입니다.
    행렬은 [f_x, 0, c_x, 0, f_y, c_y, 0, 0, 1] 형태로 정의됩니다. 모양은 (9,)입니다.
  * **width** – 이미지의 너비(픽셀 단위).
  * **height** – 이미지의 높이(픽셀 단위).
  * **focal_length** – 카메라의 초점 거리(센티미터 단위). 기본값은 24.0 cm입니다.
* **Returns:**
  [`PinholeCameraPatternCfg`](#isaaclab.sensors.patterns.PinholeCameraPatternCfg) 클래스의 인스턴스입니다.

## RS-Bpearl 패턴

### isaaclab.sensors.patterns.bpearl_pattern(cfg: [patterns_cfg.BpearlPatternCfg](#isaaclab.sensors.patterns.BpearlPatternCfg), device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

레이 캐스팅을 위한 RS-Bpearl 패턴입니다.

[Robosense RS-Bpearl](https://www.roscomponents.com/product/rs-bpearl/)는 360도 x 90도의 초광시야를 갖는 단거리 라이더로, 근거리 사각지대 감지를 위해 설계되었습니다.

* **Parameters:**
  * **cfg** – 패턴의 구성 인스턴스입니다.
  * **device** – 패턴을 생성할 디바이스입니다.
* **Returns:**
  레이의 시작 위치 및 방향.

### *class* isaaclab.sensors.patterns.BpearlPatternCfg

레이캐스팅을 위한 Bpearl 패턴의 구성입니다.

**Attributes:**

| [`horizontal_fov`](#isaaclab.sensors.patterns.BpearlPatternCfg.horizontal_fov)           | 수평 시야각(도 단위).   |
|------------------------------------------------------------------------------------------|--------------------------|
| [`horizontal_res`](#isaaclab.sensors.patterns.BpearlPatternCfg.horizontal_res)           | 수평 해상도(도 단위).      |
| [`vertical_ray_angles`](#isaaclab.sensors.patterns.BpearlPatternCfg.vertical_ray_angles) | 수직 레이 각도(도 단위).        |

#### horizontal_fov *: [float](https://docs.python.org/3/library/functions.html#float)*

수평 시야각(도 단위). 기본값은 360.0입니다.

#### horizontal_res *: [float](https://docs.python.org/3/library/functions.html#float)*

수평 해상도(도 단위). 기본값은 10.0입니다.

#### vertical_ray_angles *: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[float](https://docs.python.org/3/library/functions.html#float)]*

수직 레이 각도(도 단위). 기본값은 32개의 각도 목록입니다.

#### NOTE
Bpearl 센서에 맞게 수직 레이 각도를 수동으로 설정했습니다. 레이 각도는均등하게 분포되어 있지 않습니다.

## LiDAR 패턴

### isaaclab.sensors.patterns.lidar_pattern(cfg: [patterns_cfg.LidarPatternCfg](#isaaclab.sensors.patterns.LidarPatternCfg), device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

레이 캐스팅을 위한 라이더 센서 패턴입니다.

* **Parameters:**
  * **cfg** – 패턴의 구성 인스턴스입니다.
  * **device** – 패턴을 생성할 디바이스입니다.
* **Returns:**
  레이의 시작 위치 및 방향.

### *class* isaaclab.sensors.patterns.LidarPatternCfg

레이캐스팅을 위한 LiDAR 패턴의 구성입니다.

**Attributes:**

| [`channels`](#isaaclab.sensors.patterns.LidarPatternCfg.channels)                         | 채널 수(빔 수).                |
|-------------------------------------------------------------------------------------------|--------------------------------|
| [`vertical_fov_range`](#isaaclab.sensors.patterns.LidarPatternCfg.vertical_fov_range)     | 수직 시야각 범위(도 단위).   |
| [`horizontal_fov_range`](#isaaclab.sensors.patterns.LidarPatternCfg.horizontal_fov_range) | 수평 시야각 범위(도 단위). |
| [`horizontal_res`](#isaaclab.sensors.patterns.LidarPatternCfg.horizontal_res)             | 수평 해상도(도 단위).        |

#### channels *: [int](https://docs.python.org/3/library/functions.html#int)*

채널 수(빔 수). 라이더 센서의 수직 해상도를 결정합니다.

#### vertical_fov_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

수직 시야각 범위(도 단위).

#### horizontal_fov_range *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

수평 시야각 범위(도 단위).

#### horizontal_res *: [float](https://docs.python.org/3/library/functions.html#float)*

수평 해상도(도 단위).
