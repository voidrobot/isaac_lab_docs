# isaaclab.markers

GUI에서 UI 요소 생성을 간소화하는 마커 유틸리티용 서브 패키지입니다.

현재, 서브 패키지는 다음과 같은 클래스를 제공합니다:

* [`VisualizationMarkers`](#isaaclab.markers.VisualizationMarkers)은 [UsdGeom.PointInstancer](https://graphics.pixar.com/usd/dev/api/class_usd_geom_point_instancer.html)를 사용하여 마커 그룹을 생성합니다.

#### 참고
간단한 사용 사례의 경우, Isaac Sim의 디버그 그리기 유틸리티를 사용하는 것으로 충분할 수 있습니다.
디버그 그리기 API는 [isaacsim.util.debug_drawing](https://docs.omniverse.nvidia.com/app_isaacsim/app_isaacsim/ext_omni_isaac_debug_drawing.html) 모듈에서 사용할 수 있습니다.
이를 통해 UI에서 점과 스플라인을 효율적으로 그릴 수 있습니다.

### 클래스

| [`VisualizationMarkers`](#isaaclab.markers.VisualizationMarkers)       | USD에서 로드된 시각적 마커 그룹을 조정하는 클래스입니다.                        |
|------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| [`VisualizationMarkersCfg`](#isaaclab.markers.VisualizationMarkersCfg) | [`VisualizationMarkers`](#isaaclab.markers.VisualizationMarkers)을 구성하는 클래스. |

## 시각화 마커

### *class* isaaclab.markers.VisualizationMarkers

베이스 클래스: [`object`](https://docs.python.org/3/library/functions.html#object)

USD에서 로드된 시각적 마커 그룹을 조정하는 클래스입니다.

이 클래스를 사용하면 장면에서 점과 프레임과 같은 다양한 UI 마커를 시각화할 수 있습니다.
이 클래스는 [UsdGeom.PointInstancer](https://graphics.pixar.com/usd/dev/api/class_usd_geom_point_instancer.html)를 둘러싸서 스테이지에서
생성된 마커 프로토타입 프리즘을 인스턴싱하여 객체를 효율적으로 처리합니다.

마커 프로토타입 프리즘은 장면에서 객체의 변형을 정의하기 위해 재사용 가능한 템플릿 프리즘입니다.
예를 들어, 구 프리즘을 마커 프로토타입 프리즘으로 사용하면 장면의 다른 위치에 여러 구 프리즘을 생성할 수 있습니다.
따라서 프로토타입 프리즘은 장면에서 동일한 프리즘의 여러 인스턴스를 생성하는 데 유용합니다.

이 클래스는 구성 파일을 파싱하여 단계에 다양한 마커 프로토타입을 생성합니다. 각 마커 프로토타입 프리즘은
`UsdGeom.PointInstancer` 프리즘의 자식으로 생성됩니다. 마커 프리밍의 프리밍 경로는
[`VisualizationMarkersCfg.markers`](#isaaclab.markers.VisualizationMarkersCfg.markers) 사전의 마커 키를 사용하여 해결됩니다.
마커 프로토타입은 [`isaaclab.sim.utils.prims.create_prim()`](isaaclab.sim.utils.md#isaaclab.sim.utils.prims.create_prim)
함수를 사용하여 생성되고, 그 다음 `UsdGeom.PointInstancer` 프리즘을 사용하여 인스턴싱되어
마커 프리밍의 여러 인스턴스를 생성할 수 있도록 합니다.

[`visualize()`](#isaaclab.markers.VisualizationMarkers.visualize) 메서드를 호출하여
마커 프로토타입에 해당하는 프로토타입 인덱스를 전달함으로써
다른 마커 프로토타입 사이를 전환할 수 있습니다.
프로토타입 인덱스는 [`VisualizationMarkersCfg.markers`](#isaaclab.markers.VisualizationMarkersCfg.markers) 사전의
순서를 기반으로 합니다. 예를 들어, 사전에 "marker1"과 "marker2"라는 두 개의 마커가 있는 경우,
그들의 프로토타입 인덱스는 각각 0과 1입니다. 프로토타입 인덱스는 정수 리스트 또는 배열로 전달할 수 있습니다.

사용법:
다음 스니펫은 [-1.0, 1.0] 범위 내의 랜덤한 변환 위치에 반지름이 1.0인 구 마커 24개를 생성하는 방법을 보여줍니다.
첫 12개 마커는 빨간색으로 color 지정하고 나머지 마커는 녹색으로 지정합니다.
<br/>
```python
import isaaclab.sim as sim_utils
from isaaclab.markers import VisualizationMarkersCfg, VisualizationMarkers
<br/>
# 마커 구성 생성
# 이는 반지름이 1.0인 구인 두 개의 마커 프로토타입 "marker1"과 "marker2"를 생성합니다.
# "marker1"의 색상은 빨간색이고 "marker2"의 색상은 녹색입니다.
cfg = VisualizationMarkersCfg(
    prim_path="/World/Visuals/testMarkers",
    markers={
        "marker1": sim_utils.SphereCfg(
            radius=1.0,
            visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(1.0, 0.0, 0.0)),
        ),
        "marker2": VisualizationMarkersCfg.SphereCfg(
            radius=1.0,
            visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 1.0, 0.0)),
        ),
    },
)
# 마커 인스턴스 생성
# 이는 주어진 경로에 UsdGeom.PointInstancer 프리밍과 함께 마커 프로토타입을 생성합니다.
marker = VisualizationMarkers(cfg)
<br/>
# 마커 위치 설정
# -- -1.0과 1.0 사이에서 랜덤하게 변환 샘플링
marker_translations = np.random.uniform(-1.0, 1.0, (24, 3))
# -- 주어진 변환 위치에 24개의 마커 생성
# 참고: 마커 인덱스가 주어지지 않았기 때문에 모든 마커가 `marker1`입니다.
marker.visualize(translations=marker_translations)
<br/>
# 프로토타입 인덱스를 기반으로 마커 변경
# 처음 12개 마커는 marker1이고 나머지 마커는 marker2입니다.
# 0 -> marker1, 1 -> marker2
marker_indices = [0] * 12 + [1] * 12
# 주어진 인덱스에서 마커 프로토타입 변경
# 참고: 변환이 주어지지 않았기 때문에 마커의 변환은 이전 호출과 동일합니다.
marker.visualize(marker_indices=marker_indices)
<br/>
# 프로토타입 인덱스와 변환을 기반으로 마커 변경
marker.visualize(marker_indices=marker_indices, translations=marker_translations)
```

**메서드:**

| [`__init__`](#isaaclab.markers.VisualizationMarkers.__init__)(cfg)                                 | 클래스를 초기화합니다.                 |
|----------------------------------------------------------------------------------------------------|---------------------------------------|
| [`set_visibility`](#isaaclab.markers.VisualizationMarkers.set_visibility)(visible)                 | 마커의 가시성을 설정합니다.   |
| [`is_visible`](#isaaclab.markers.VisualizationMarkers.is_visible)()                                | 마커의 가시성을 확인합니다. |
| [`visualize`](#isaaclab.markers.VisualizationMarkers.visualize)([translations, orientations, ...]) | 뷰포트에서 마커를 업데이트합니다.       |

**속성:**

| [`num_prototypes`](#isaaclab.markers.VisualizationMarkers.num_prototypes)   | 사용 가능한 마커 프로토타입의 수입니다.   |
|-----------------------------------------------------------------------------|----------------------------------------------|
| [`count`](#isaaclab.markers.VisualizationMarkers.count)                     | 마커 인스턴스의 총 개수입니다.        |

#### \_\_init_\_(cfg: [VisualizationMarkersCfg](#isaaclab.markers.VisualizationMarkersCfg))

클래스를 초기화합니다.

클래스가 초기화될 때, `UsdGeom.PointInstancer`가 스테이지에 생성되고
마커 프리밍이それに登録됩니다.

#### 참고
주어진 경로에 이미 프리밍이 존재하는 경우, 함수는 다음으로 사용 가능한 경로를 찾아
그 곳에 `UsdGeom.PointInstancer` 프리밍을 생성합니다.

* **매개변수:**
  **cfg** – 마커에 대한 구성.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `cfg`에 마커가 제공되지 않은 경우.

#### *property* num_prototypes *: [int](https://docs.python.org/3/library/functions.html#int)*

사용 가능한 마커 프로토타입의 수입니다.

#### *property* count *: [int](https://docs.python.org/3/library/functions.html#int)*

마커 인스턴스의 총 개수입니다.

#### set_visibility(visible: [bool](https://docs.python.org/3/library/functions.html#bool))

마커의 가시성을 설정합니다.

이 메서드는 USD API를 통해 이를 수행합니다.

* **매개변수:**
  **visible** – 가시성을 설정하는 플래그.

#### is_visible() → [bool](https://docs.python.org/3/library/functions.html#bool)

마커의 가시성을 확인합니다.

* **반환값:**
  마커가 보이면 True, 그렇지 않으면 False.

#### visualize(translations: np.ndarray | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, orientations: np.ndarray | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, scales: np.ndarray | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, marker_indices: [list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)] | np.ndarray | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None)

뷰포트에서 마커를 업데이트합니다.

#### 참고
스테이지에서 PointInstancer 프리밍이 숨겨져 있는 경우, 함수는 단순히 반환하여
마커 업데이트를 건너뜁니다. 이는 마커가 보이지 않을 때 불필요한 계산을 방지하는 데 도움이 됩니다.

마커를 업데이트할 때마다 입력 배열의 첫 번째 차원의 요소 수가 같아야 합니다.
요소 수가 다르면, UsdGeom.PointInstancer가 오류를 발생시켜 불일치를 알려줍니다.

또한, 이 함수는 마커의 동적 업데이트를 지원합니다.
즉, 호출 간에 마커의 개수가 변경될 수 있습니다.
예를 들어, 24개의 점을 시각화하고 싶다면, 24개의 변환, 방향, 및 스케일을 전달할 수 있습니다.
12개 점만 시각화하고 싶다면, 12개의 변환, 방향, 및 스케일을 전달할 수 있습니다.
함수는 scene 내의 마커 개수를 자동으로 업데이트합니다.

또한, 함수는 프로토타입 인덱스를 기반으로 마커 프로토타입을 업데이트합니다.
예를 들어, 두 개의 마커 프로토타입이 있고 다음 마커 인덱스를 전달하는 경우: [0, 1, 0, 1],
함수는 첫 번째 및 세 번째 마커에 첫 번째 프로토타입을, 두 번째 및 네 번째 마커에 두 번째 프로토타입을 적용합니다.
두 번째 프로토타입과 함께 사용할 때 유용합니다. 같은 장면에서 다양한 마커를 시각화하고자 할 때 유용합니다. 마커 인덱스 리스트의 요소 수는 변환, 방향 또는 스케일의 요소 수와 같아야 합니다. 요소 수가 다를 경우, 함수가 오류를 발생시킵니다.

* **Parameters:**
  * **translations** – 부모 prim 프레임에 대한 변환. Shape은 (M, 3)입니다.
    기본값은 None이며, 이는 그대로 유지됨을 의미합니다.
  * **orientations** – 부모 prim 프레임에 대한 쿼터니언 방향 (w, x, y, z). Shape은 (M, 4)입니다.
    기본값은 None이며, 이는 그대로 유지됨을 의미합니다.
  * **scales** – 회전이 적용되기 전에 적용되는 스케일. Shape은 (M, 3)입니다.
    기본값은 None이며, 이는 그대로 유지됨을 의미합니다.
  * **marker_indices** – 어떤 마커 프로토타입을 시각화할지 결정합니다. Shape은 (M)입니다.
    기본값은 None이며, 이전 호출에서의 마커 총 개수가 동일한 경우 그대로 유지됨을 의미합니다.
    마커 수가 다른 경우, 함수는 장면의 마커 수를 업데이트합니다.
* **Raises:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 배열이 예상된 형태를 따르지 않을 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 함수가 모든 None 인수로 호출될 때.

### *class* isaaclab.markers.VisualizationMarkersCfg

[`VisualizationMarkers`](#isaaclab.markers.VisualizationMarkers)를 구성하기 위한 클래스입니다.

**Attributes:**

| [`prim_path`](#isaaclab.markers.VisualizationMarkersCfg.prim_path)   | `UsdGeom.PointInstancer`가 생성될 prim 경로입니다.   |
|----------------------------------------------------------------------|------------------------------------------------------|
| [`markers`](#isaaclab.markers.VisualizationMarkersCfg.markers)       | 마커 구성의 사전입니다.                            |

#### prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

`UsdGeom.PointInstancer`가 생성될 prim 경로입니다.

#### markers *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [isaaclab.sim.spawners.spawner_cfg.SpawnerCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.SpawnerCfg)]*

마커 구성의 사전입니다.

키는 마커의 이름이며, 값은 해당 마커의 구성입니다. 키는 클래스 내에서 마커를 식별하는 데 사용됩니다.
