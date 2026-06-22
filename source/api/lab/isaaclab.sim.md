# isaaclab.sim

시뮬레이션 특정 기능을 포함하는 하위 패키지입니다.

이러한 기능에는 다음이 포함됩니다:

* Omniverse에서 다양한 객체와 재료를 스폰할 수 있는 기능
* USD 프리미에서 다양한 스키마 정의 및 수정
* 다른 파일 형식(예: URDF, OBJ, STL, FBX)에서 USD 파일을 얻기 위한 변환기
* 시뮬레이터를 제어하기 위한 유틸리티 클래스

#### 참고
현재, Omniverse에서 가능한 모든 스키마와 프리미 중 일부만 지원됩니다.
필요에 따라 이러한 기능 세트를 확장하고 있습니다. 포함하고 싶은 특정 프리미나 스키마가 있다면,
GitHub에서 기능 요청으로 이슈를 생성하여 필요한 애플리케이션을 상세히 설명해 주세요.

모듈을 편리하게 사용하기 위해 다음과 같이 가져오는 것을 권장합니다:

```python
import isaaclab.sim as sim_utils
```

### 서브모듈

| [`converters`](isaaclab.sim.converters.md#module-isaaclab.sim.converters)   | 다양한 파일 형식을 USD로 변환하는 변환기를 포함하는 서브모듈입니다.   |
|-----------------------------------------------------------------------------|------------------------------------------------------------------------------|
| [`schemas`](isaaclab.sim.schemas.md#module-isaaclab.sim.schemas)            | Omniverse에서 사용되는 스키마를 위한 유틸리티를 포함하는 서브모듈입니다.               |
| [`spawners`](isaaclab.sim.spawners.md#module-isaaclab.sim.spawners)         | Omniverse에서 프리미를 생성하기 위한 유틸리티를 포함하는 서브모듈입니다.             |
| [`utils`](isaaclab.sim.utils.md#module-isaaclab.sim.utils)                  | USD 작업을 기반으로 하는 유틸리티입니다.                                       |

### 클래스

| [`SimulationContext`](#isaaclab.sim.SimulationContext)   | 물리 스텝 및 렌더링과 같은 시뮬레이션 관련 이벤트를 제어하는 클래스입니다.   |
|----------------------------------------------------------|----------------------------------------------------------------------------------------|
| [`SimulationCfg`](#isaaclab.sim.SimulationCfg)           | 시뮬레이션 물리 설정에 대한 구성입니다.                                                  |
| [`PhysxCfg`](#isaaclab.sim.PhysxCfg)                     | PhysX 솔버 관련 매개변수에 대한 구성입니다.                                     |
| [`RenderCfg`](#isaaclab.sim.RenderCfg)                   | Omniverse RTX 렌더러에 대한 구성입니다.                                              |

### 함수

| [`simulation_context.build_simulation_context`](#isaaclab.sim.simulation_context.build_simulation_context)([...])   | 제공된 설정으로 시뮬레이션 컨텍스트를 구축하는 컨텍스트 관리자입니다.   |
|---------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|

## 시뮬레이션 컨텍스트

### *class* isaaclab.sim.SimulationContext

Bases: [`SimulationContext`](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/py/source/extensions/isaacsim.core.api/docs/index.html#isaacsim.core.api.simulation_context.SimulationContext)

시뮬레이션 관련 이벤트(예: 물리 스텝 및 렌더링)를 제어하는 클래스입니다.

시뮬레이션 컨텍스트는 다양한 시뮬레이션 측면을 제어하는 데 도움이 됩니다. 여기에는 다음이 포함됩니다:

* 물리 시간 단계, 물리 서브스텝 수 및 물리 솔버 매개변수(자세한 내용은 [`isaaclab.sim.SimulationCfg`](#isaaclab.sim.SimulationCfg) 참조)와 같은 다양한 설정으로 시뮬레이터 구성
* 시뮬레이션 재생, 일시정지, 스텝 및 중지
* 물리 스텝, 렌더링 등과 같은 다양한 시뮬레이션 이벤트에 콜백 추가 및 제거

이 클래스는 [`isaacsim.core.api.simulation_context.SimulationContext`](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/py/source/extensions/isaacsim.core.api/docs/index.html#isaacsim.core.api.simulation_context.SimulationContext) 클래스를 상속받아
구성 객체를 사용한 시뮬레이션 컨텍스트 설정, 일반적으로 사용되는 시뮬레이터 관련 함수 노출, 릴리스 간 호환성을 보장하기 위한 Isaac Sim 버전 검사와 같은 추가 기능을 제공합니다.

시뮬레이션 컨텍스트는 싱글톤 객체입니다. 즉, 어느 시점에든 하나의 시뮬레이션 컨텍스트 인스턴스만 존재할 수 있습니다. 부모 클래스에 의해 이를 강제합니다. 따라서 시뮬레이션 컨텍스트의 여러 인스턴스를 생성할 수 없습니다. 대신 `instance()` 메서드를 사용하여 시뮬레이션 컨텍스트에 접근할 수 있습니다.

#### 주의
[PyTorch](https://pytorch.org/) 백엔드만을 시뮬레이션에 지원하므로, 시뮬레이션 컨텍스트는 기본적으로 `torch` 백엔드를 사용하도록 구성됩니다. 즉, 시뮬레이션에서 사용되는 모든 데이터 구조는 `torch.Tensor` 객체입니다.

시뮬레이션 컨텍스트는 두 가지 다른 운영 모드에서 사용될 수 있습니다:

1. **독립형 파이썬 스크립트**: 이 모드에서는 사용자가 시뮬레이션을 완전히 제어하고 동기적으로(즉, 블로킹 호출로서) 스텝 이벤트를 트리거할 수 있습니다. 이 경우 사용자는 [`step()`](#isaaclab.sim.SimulationContext.step)을 수동으로 호출하여 물리 시뮬레이션을 스텝하고 [`render()`](#isaaclab.sim.SimulationContext.render)을 호출하여 장면을 렌더링해야 합니다.
2. **Omniverse 확장**: 이 모드에서는 사용자의 시뮬레이션 스텝 제어가 제한적이며, 모든 시뮬레이션 이벤트는 비동기적으로(즉, 논블로킹 호출로서) 트리거됩니다. 이 경우 사용자는 시뮬레이션을 시작, 일시정지 및 중지할 수만 있습니다. 시뮬레이션이 물리 시뮬레이션 스텝 및 장면 렌더링을 담당합니다.

앞서 언급한 바와 같이, 이 클래스의 대부분의 함수에는 `_async` 접미사가 붙은 대응하는 함수가 있습니다. `_async` 함수는 Omniverse 확장 모드에서 사용되며, 비-`_async` 함수는 독립형 파이썬 스크립트 모드에서 사용됩니다.

**클래스:**

| [`RenderMode`](#isaaclab.sim.SimulationContext.RenderMode)   | 시뮬레이션의 다양한 렌더링 모드입니다.   |
|--------------------------------------------------------------|-------------------------------------------------|

**메서드:**

| [`__init__`](#isaaclab.sim.SimulationContext.__init__)([cfg])                                         | 시뮬레이터를 제어하기 위한 시뮬레이션 컨텍스트를 생성합니다.                                                |
|-------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| [`has_gui`](#isaaclab.sim.SimulationContext.has_gui)()                                                | 시뮬레이션에 GUI가 활성화되어 있는지 여부를 반환합니다.                                                     |
| [`has_rtx_sensors`](#isaaclab.sim.SimulationContext.has_rtx_sensors)()                                | 시뮬레이션에 RTX 렌더링 관련 센서가 있는지 여부를 반환합니다.                                 |
| [`is_fabric_enabled`](#isaaclab.sim.SimulationContext.is_fabric_enabled)()                            | 패브릭 인터페이스가 활성화되어 있는지 여부를 반환합니다.                                                      |
| [`get_version`](#isaaclab.sim.SimulationContext.get_version)()                                        | 시뮬레이터의 버전을 반환합니다.                                                                 |
| [`set_camera_view`](#isaaclab.sim.SimulationContext.set_camera_view)(eye, target[, camera_prim_path]) | 스테이지에서 뷰포트 카메라의 위치 및 대상을 설정합니다.                                      |
| [`set_render_mode`](#isaaclab.sim.SimulationContext.set_render_mode)(mode)                            | 시뮬레이션의 현재 렌더링 모드를 변경합니다.                                                     |
| [`set_setting`](#isaaclab.sim.SimulationContext.set_setting)(name, value)                             | Carbonite SDK를 사용하여 시뮬레이션 설정을 설정합니다.                                                      |
| [`get_setting`](#isaaclab.sim.SimulationContext.get_setting)(name)                                    | Carbonite SDK를 사용하여 시뮬레이션 설정을 읽습니다.                                                  |
| [`get_initial_stage`](#isaaclab.sim.SimulationContext.get_initial_stage)()                            | 장면 생성 중에 사용된 스테이지 핸들을 반환합니다.                                                      |
| [`forward`](#isaaclab.sim.SimulationContext.forward)()                                                | 렌더링을 위해 articulation 키네마틱스 및 패브릭을 업데이트합니다.                                             |
| [`step`](#isaaclab.sim.SimulationContext.step)([render])                                              | 시뮬레이션을 스텝합니다.                                                                                 |
| [`render`](#isaaclab.sim.SimulationContext.render)([mode])                                            | 렌더링 모드에 따라 UI 요소 및 뷰포트와 같은 렌더링 구성 요소를 새로 고칩니다.                                            |

**속성:**

| [`device`](#isaaclab.sim.SimulationContext.device)   | 시뮬레이션에서 사용되는 장치입니다.   |
|------------------------------------------------------|----------------------------------|

#### *class* RenderMode

Bases: [`IntEnum`](https://docs.python.org/3/library/enum.html#enum.IntEnum)

시뮬레이션의 다양한 렌더링 모드입니다.

렌더링 모드는 뷰포트 및 기타 UI 요소(예: 키보드 또는 마우스 이벤트의 리스너)가 어떻게 업데이트되는지에 해당합니다. 시뮬레이션이 렌더링될 때 업데이트할 수 있는 세 가지 주요 구성 요소가 있습니다:

1. **UI 요소 및 기타 확장**: 이들은 시뮬레이션이 실행될 때 업데이트가 필요한 버튼, 슬라이더 등과 같은 UI 요소 및 백그라운드에서 실행되는 기타 확장을 말합니다.
2. **카메라**: 이들은 일반적으로 Hydra 텍스처를 기반으로 하며, 다양한 관점에서 장면을 렌더링하는 데 사용됩니다. 뷰포트에 연결되거나 독립적으로 사용되어 장면을 렌더링할 수 있습니다.
3. **뷰포트**: 여기서는 렌더링된 장면을 볼 수 있는 창입니다.

위 컴포넌트 각각을 업데이트하는 오버헤드가 다릅니다. 예를 들어, 뷰포트를 업데이트하는 것은 UI 요소를 업데이트하는 것보다 계산 비용이 높습니다. 따라서 시뮬레이션이 렌더링될 때 무엇이 업데이트되는지를 제어하는 것이 유용합니다. 여기서 렌더 모드가 활용됩니다. 네 가지의 서로 다른 렌더 모드가 있습니다:

* [`NO_GUI_OR_RENDERING`](#isaaclab.sim.SimulationContext.RenderMode.NO_GUI_OR_RENDERING): 시뮬레이션이 GUI 없이 실행되고 화면 밖 렌더링 플래그가 비활성화되어 있으므로 위 세 가지 모두 업데이트되지 않습니다.
* [`NO_RENDERING`](#isaaclab.sim.SimulationContext.RenderMode.NO_RENDERING): 렌더링이 없으며, 여기서 UI 요소만 낮은 속도로 업데이트됩니다.
* [`PARTIAL_RENDERING`](#isaaclab.sim.SimulationContext.RenderMode.PARTIAL_RENDERING): 부분 렌더링, 여기서 1과 2만 업데이트됩니다.
* [`FULL_RENDERING`](#isaaclab.sim.SimulationContext.RenderMode.FULL_RENDERING): 전체 렌더링, 여기서 모든 것(1, 2, 3)이 업데이트됩니다.

**속성:**

| [`NO_GUI_OR_RENDERING`](#isaaclab.sim.SimulationContext.RenderMode.NO_GUI_OR_RENDERING)   | 시뮬레이션이 GUI 없이 실행되고 화면 밖 렌더링이 비활성화되어 있습니다.            |
|-------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| [`NO_RENDERING`](#isaaclab.sim.SimulationContext.RenderMode.NO_RENDERING)                 | 렌더링이 없으며, 여기서 UI 요소만 낮은 속도로 업데이트됩니다.                  |
| [`PARTIAL_RENDERING`](#isaaclab.sim.SimulationContext.RenderMode.PARTIAL_RENDERING)       | 부분 렌더링, 여기서 시뮬레이션 카메라와 UI 요소가 업데이트됩니다.             |
| [`FULL_RENDERING`](#isaaclab.sim.SimulationContext.RenderMode.FULL_RENDERING)             | 전체 렌더링, 여기서 시뮬레이션 뷰포트, 카메라 및 UI 요소가 모두 업데이트됩니다. |

**메서드:**

| [`__new__`](#isaaclab.sim.SimulationContext.RenderMode.__new__)(value)   |    |
|--------------------------------------------------------------------------|----|

#### NO_GUI_OR_RENDERING *= -1*

시뮬레이션이 GUI 없이 실행되고 화면 밖 렌더링이 비활성화되어 있습니다.

#### NO_RENDERING *= 0*

렌더링이 없으며, 여기서 UI 요소만 낮은 속도로 업데이트됩니다.

#### PARTIAL_RENDERING *= 1*

부분 렌더링, 여기서 시뮬레이션 카메라와 UI 요소가 업데이트됩니다.

#### FULL_RENDERING *= 2*

전체 렌더링, 여기서 시뮬레이션 뷰포트, 카메라 및 UI 요소가 모두 업데이트됩니다.

#### \_\_new_\_(value)

#### \_\_init_\_(cfg: [SimulationCfg](#isaaclab.sim.SimulationCfg) | [None](https://docs.python.org/3/library/constants.html#None) = None)

시뮬레이션 컨텍스트를 생성하여 시뮬레이터를 제어합니다.

* **파라미터:**
  **cfg** – 시뮬레이션의 구성입니다. 기본값은 None이며, 이 경우 기본 구성이 사용됩니다.

#### *프로퍼티* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

시뮬레이션에서 사용되는 디바이스입니다.

#### 참고
Omniverse에서는 렌더링을 위해 여러 GPU를 구성할 수 있으며, 물리 엔진은 단일 GPU에서 동작합니다. 이 함수는 물리 시뮬레이션에 사용되는 디바이스를 반환합니다.

#### has_gui() → [bool](https://docs.python.org/3/library/functions.html#bool)

시뮬레이션에 GUI가 활성화되어 있는지 여부를 반환합니다.

시뮬레이션이 로컬 또는 라이브 스트리밍을 통해 GUI가 활성화된 경우 True를 반환합니다.

#### has_rtx_sensors() → [bool](https://docs.python.org/3/library/functions.html#bool)

시뮬레이션에 RTX 렌더링 관련 센서가 있는지 여부를 반환합니다.

이 함수는 시뮬레이션 파라미터 `"/isaaclab/render/rtx_sensors"`의 값을 반환합니다. 이 파라미터는 Isaac Lab의 센서 클래스를 사용하여 RTX 관련 센서(카메라 또는 라이다)가 생성될 때 True로 설정됩니다.

시뮬레이션에 RTX 센서(USD 카메라 또는 라이다 등)가 있는 경우 True를 반환합니다.

자세한 내용은 [NVIDIA RTX 문서](https://developer.nvidia.com/rendering-technologies)를 참조하십시오.

#### is_fabric_enabled() → [bool](https://docs.python.org/3/library/functions.html#bool)

패브릭 인터페이스가 활성화되어 있는지 여부를 반환합니다.

패브릭 인터페이스가 활성화되면 USD 읽기/쓰기 작업이 비활성화됩니다. 대신 모든 애플리케이션이 패브릭 인터페이스를 직접 읽고 쓰면서 시뮬레이션 상태에 접근합니다.これにより、USD 읽기/쓰기 작업 동안 발생하는 오버헤드가 크게 감소합니다.

자세한 내용은 [패브릭 문서](https://docs.omniverse.nvidia.com/kit/docs/usdrt/latest/docs/usd_fabric_usdrt.html)를 참조하십시오.

#### get_version() → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int)]

시뮬레이터의 버전을 반환합니다.

반환된 튜플은 다음 정보를 포함합니다:

* 메이저 버전: 이는 릴리즈의 연도입니다(예: 2022).
* 마이너 버전: 이는 릴리즈의 반기입니다(예: 1 또는 2).
* 패치 버전: 이는 릴리즈의 패치 번호입니다(예: 0).

#### 주의
이 함수는 더 이상 사용되지 않으며 향후 제거될 예정입니다.
대신 [`isaaclab.utils.version.get_isaac_sim_version()`](isaaclab.utils.md#isaaclab.utils.version.get_isaac_sim_version) 사용을 권장합니다.

* **반환값:**
  메이저, 마이너 및 패치 버전을 포함하는 튜플.

### 예시

```pycon
>>> sim = SimulationContext()
>>> sim.get_version()
(2022, 1, 0)
```

#### set_camera_view(eye: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], target: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], camera_prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str) = '/OmniverseKit_Persp')

스테이지에서 뷰포트 카메라의 위치와 대상을 설정합니다.

#### 참고
이 함수는 `$isaacsim.core.utils.viewports.set_camera_view$` 함수의 래퍼입니다.
편의를 위해 여기에서 제공하여 필요한 임포트의 양을 줄입니다.

* **파라미터:**
  * **eye** – 카메라 눈의 위치입니다.
  * **target** – 카메라 대상의 위치입니다.
  * **camera_prim_path** – 스테이지에서 카메라 프리미티브의 경로입니다. 기본값은 “/OmniverseKit_Persp”입니다.

#### set_render_mode(mode: [RenderMode](#isaaclab.sim.SimulationContext.RenderMode))

시뮬레이션의 현재 렌더 모드를 변경합니다.

다른 렌더 모드에 대한 자세한 내용은 [`RenderMode`](#isaaclab.sim.SimulationContext.RenderMode)를 참조하십시오.

#### 참고
GUI를 사용할 수 없는 경우(로컬 또는 라이브 스트리밍), 뷰포트가 렌더링이 필요한지 여부를 선택할 필요가 없습니다(GUI가 없기 때문입니다). 따라서 이 경우 해당 함수를 호출해도 렌더 모드가 변경되지 않습니다.

* **파라미터:**
  * **mode** ([*RenderMode*](#isaaclab.sim.SimulationContext.RenderMode)) – 렌더링 모드입니다. SimulationContext의 현재 렌더 모드와 다를 경우,
  * **mode.** (*SimulationContext의 모드가 새로운 모드로 변경됨*) – 
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력된 모드가 지원되지 않는 경우 발생합니다.

#### set_setting(name: [str](https://docs.python.org/3/library/stdtypes.html#str), value: [Any](https://docs.python.org/3/library/typing.html#typing.Any))

Carbonite SDK를 사용하여 시뮬레이션 설정을 설정합니다.

#### 참고
입력된 설정 이름이 존재하지 않으면 새로 생성됩니다. 존재하면 값이 덮어씌워집니다. 올바른 설정 이름을 사용해야 합니다.

설정 인터페이스를 이해하려면
[Carbonite SDK](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref/settings.html)
문서를 참조하십시오.

* **파라미터:**
  * **name** – 설정의 이름입니다.
  * **value** – 설정의 값입니다.

#### get_setting(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

Carbonite SDK를 사용하여 시뮬레이션 설정을 읽습니다.

* **파라미터:**
  **name** – 설정의 이름입니다.
* **반환값:**
  설정의 값입니다.

#### get_initial_stage() → pxr.Usd.Stage

장면 생성 중에 사용되는 스테이지 핸들을 반환합니다.

* **반환값:**
  장면 생성 중에 사용된 스테이지입니다.

#### forward() → [None](https://docs.python.org/3/library/constants.html#None)

렌더링을 위해 아티큘레이션 운동학과 패브릭을 업데이트합니다.

#### step(render: [bool](https://docs.python.org/3/library/functions.html#bool) = True)

시뮬레이션을 한 단계 진행시킵니다.

#### 참고
이 함수는 타임라인이 일시 중지된 경우 차단됩니다. 타임라인이 재생될 때만 반환됩니다.

* **파라미터:**
  **render** – 물리 시뮬레이션을 진행한 후 장면을 렌더링할지 여부입니다. False로 설정하면 장면을 렌더링하지 않고 물리 시뮬레이션만 진행합니다.

#### render(mode: [RenderMode](#isaaclab.sim.SimulationContext.RenderMode) | [None](https://docs.python.org/3/library/constants.html#None) = None)

렌더 모드에 따라 UI 요소 및 뷰포트와 같은 렌더링 구성 요소를 새로 고칩니다.

이 함수는 시뮬레이션의 렌더링 구성 요소를 새로 고칩니다. 여기에는 뷰포트, UI 요소 및 백그라운드에서 실행되는 기타 확장(물리 시뮬레이션 제외) 요소가 포함됩니다. 렌더링 구성 요소는 렌더 모드에 따라 새로 고쳐집니다.

다른 렌더 모드에 대한 자세한 내용은 [`RenderMode`](#isaaclab.sim.SimulationContext.RenderMode)를 참조하십시오.

* **파라미터:**
  **mode** – 렌더링 모드입니다. 기본값은 None이며, 이 경우 현재 렌더링 모드가 사용됩니다.

## 시뮬레이션 구성

### *class* isaaclab.sim.SimulationCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

시뮬레이션 물리 구성입니다.

**Attributes:**

| [`physics_prim_path`](#isaaclab.sim.SimulationCfg.physics_prim_path)                   | USD PhysicsScene가 생성되는 프리임 경로입니다.       |
|----------------------------------------------------------------------------------------|------------------------------------------------------------|
| [`device`](#isaaclab.sim.SimulationCfg.device)                                         | 시뮬레이션을 실행할 장치입니다.                       |
| [`dt`](#isaaclab.sim.SimulationCfg.dt)                                                 | 물리 시뮬레이션 시간 간격(초 단위).             |
| [`render_interval`](#isaaclab.sim.SimulationCfg.render_interval)                       | 렌더링 단계당 물리 시뮬레이션 단계 수. |
| [`gravity`](#isaaclab.sim.SimulationCfg.gravity)                                       | 중력 벡터(m/s² 단위).                             |
| [`enable_scene_query_support`](#isaaclab.sim.SimulationCfg.enable_scene_query_support) | 충돌 형태에 대한 장면 쿼리 지원 활성화/비활성화.   |
| [`use_fabric`](#isaaclab.sim.SimulationCfg.use_fabric)                                 | 물리 버퍼를 직접 읽을지 여부를 활성화/비활성화합니다.        |
| [`physx`](#isaaclab.sim.SimulationCfg.physx)                                           | PhysX 솔버 설정.                                     |
| [`physics_material`](#isaaclab.sim.SimulationCfg.physics_material)                     | 강체에 대한 기본 물리 재질 설정.        |
| [`render`](#isaaclab.sim.SimulationCfg.render)                                         | 렌더링 설정.                                           |
| [`create_stage_in_memory`](#isaaclab.sim.SimulationCfg.create_stage_in_memory)         | 단계가 먼저 메모리에 생성되는지 여부.                       |
| [`logging_level`](#isaaclab.sim.SimulationCfg.logging_level)                           | 로그 수준.                                         |
| [`save_logs_to_file`](#isaaclab.sim.SimulationCfg.save_logs_to_file)                   | 로그를 파일에 저장합니다.                                       |
| [`log_dir`](#isaaclab.sim.SimulationCfg.log_dir)                                       | 로그를 저장할 디렉터리.                         |

#### physics_prim_path *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

USD PhysicsScene가 생성되는 프리임 경로입니다. 기본값은 “/physicsScene”입니다.

#### device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

시뮬레이션을 실행할 장치입니다. 기본값은 `"cuda:0"`입니다.

옵션은 다음과 같습니다:

- `"cpu"`: CPU를 사용합니다.
- `"cuda"`: GPU를 사용하며, 장치 ID는 [`AppLauncher`](isaaclab.app.md#isaaclab.app.AppLauncher)의 구성에서 추론됩니다.
- `"cuda:N"`: GPU를 사용하며, N은 장치 ID입니다. 예를 들어, “cuda:0”입니다.

#### dt *: [float](https://docs.python.org/3/library/functions.html#float)*

물리 시뮬레이션 시간 간격(초 단위). 기본값은 0.0167초입니다.

#### render_interval *: [int](https://docs.python.org/3/library/functions.html#int)*

렌더링 단계당 물리 시뮬레이션 단계 수. 기본값은 1입니다.

#### gravity *: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)]*

중력 벡터(m/s² 단위). 기본값은 (0.0, 0.0, -9.81)입니다.

(0.0, 0.0, 0.0)로 설정하면 중력이 비활성화됩니다.

#### enable_scene_query_support *: [bool](https://docs.python.org/3/library/functions.html#bool)*

충돌 형태에 대한 장면 쿼리 지원 활성화/비활성화. 기본값은 False입니다.

이 플래그는 씬의 액터와 연결된 형태에 대한 충돌 쿼리(레이캐스트, 스윕, 오버랩)를 수행할 수 있게 합니다. 이는 물리 엔진 외부에서 사용자 정의 충돌 감지 로직을 구현하는 데 유용합니다.

False로 설정하면, 물리 엔진이 장면 쿼리 관리자를 생성하지 않으며, 장면 쿼리 기능은 사용할 수 없습니다. 그러나 성능 향상의 이점이 있습니다.

#### NOTE
이 플래그는 GUI가 활성화된 상태에서 시뮬레이션을 실행할 때 [`SimulationContext`](#isaaclab.sim.SimulationContext) 클래스 내부에서 True로 재정의됩니다. 이는 특정 GUI 기능을 정상적으로 작동시키기 위함입니다.

#### use_fabric *: [bool](https://docs.python.org/3/library/functions.html#bool)*

물리 버퍼를 직접 읽을지 여부를 활성화/비활성화합니다. 기본값은 True입니다.

시뮬레이션 실행 중, 씬의 상태 업데이트는 일반적으로 USD와 동기화됩니다. 이로 인해 데이터 읽기 오버헤드가 발생하며, 대규모 병렬 처리에서는 확장성이 좋지 않습니다. 이 플래그는 동기화를 비활성화하고 물리 버퍼에서 데이터를 직접 읽을 수 있게 합니다.

장면에서 많은 수의 프리임을 사용하여 시뮬레이션을 실행하는 경우, 이 플래그를 [`True`](https://docs.python.org/3/library/constants.html#True)로 설정하는 것이 권장됩니다.

#### NOTE
이 플래그가 활성화되면 GUI에서는 물리 파라미터를 실시간으로 업데이트하지 않습니다. 실시간 업데이트를 활성화하려면 이 플래그를 [`False`](https://docs.python.org/3/library/constants.html#False)로 설정하세요.

GPU 시뮬레이션을 사용할 경우, 렌더러에서 업데이트를 시각화하기 위해 Fabric을 활성화해야 합니다. 트랜스폼 업데이트는 Fabric을 통해 렌더러로 전파됩니다. GPU 시뮬레이션에서 Fabric을 비활성화하면, 시뮬레이션은 백그라운드에서 여전히 실행되지만 렌더러는 시뮬레이션의 업데이트를 렌더링할 수 없습니다.

#### physx *: [PhysxCfg](#isaaclab.sim.PhysxCfg)*

PhysX 솔버 설정. 기본값은 PhysxCfg()입니다.

#### physics_material *: [RigidBodyMaterialCfg](isaaclab.sim.spawners.md#isaaclab.sim.spawners.materials.RigidBodyMaterialCfg)*

강체에 대한 기본 물리 재질 설정. 기본값은 RigidBodyMaterialCfg()입니다.

물리 엔진은 이 물리 재질을 물리 재질이 지정되지 않은 모든 강체 프리임에 기본값으로 사용합니다.

재질은 경로 `{physics_prim_path}/defaultMaterial`에 생성됩니다.

#### render *: [RenderCfg](#isaaclab.sim.RenderCfg)*

렌더링 설정. 기본값은 RenderCfg()입니다.

#### create_stage_in_memory *: [bool](https://docs.python.org/3/library/functions.html#bool)*

단계가 먼저 메모리에 생성되는지 여부. 기본값은 False입니다.

단계를 메모리에서 먼저 생성하면 시작 시간을 단축할 수 있습니다.

#### logging_level *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL']*

로그 수준. 기본값은 “WARNING”입니다.

#### save_logs_to_file *: [bool](https://docs.python.org/3/library/functions.html#bool)*

로그를 파일에 저장합니다. 기본값은 True입니다.

#### log_dir *: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None)*

로그를 저장할 디렉터리. 기본값은 None입니다.

[`save_logs_to_file`](#isaaclab.sim.SimulationCfg.save_logs_to_file)가 True이면, 로그는 [`log_dir`](#isaaclab.sim.SimulationCfg.log_dir)에서 지정된 디렉터리에 저장됩니다. None이면, 로그는 임시 디렉터리에 저장됩니다.

### *class* isaaclab.sim.PhysxCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

PhysX 솔버 관련 파라미터 구성입니다.

이 파라미터들은 PhysX 솔버를 구성하는 데 사용됩니다. 자세한 내용은 [PhysX 5 SDK 문서](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/_api_build/classPxSceneDesc.html)를 참조하세요.

PhysX 5는 GPU 가속 물리 시뮬레이션을 지원합니다. 이는 기본적으로 활성화되어 있지만, [`SimulationCfg`](#isaaclab.sim.SimulationCfg)의 [`device`](#isaaclab.sim.SimulationCfg.device)를 `cpu`로 설정하여 비활성화할 수 있습니다. CPU PhysX와 달리, GPU 시뮬레이션 기능은 모든 버퍼를 동적으로 확장할 수 없습니다. 따라서 GPU 기능에 대한 버퍼 크기를 적절히 추정하여 제공해야 합니다. 버퍼 크기가 부족하면 시뮬레이션이 오류로 실패하고 원치 않는 동작을 유발할 수 있습니다. 버퍼 크기는 `gpu_*` 파라미터를 통해 조정할 수 있습니다.

**Attributes:**

| [`solver_type`](#isaaclab.sim.PhysxCfg.solver_type)                                                         | 사용할 솔버 유형. 기본값은 1(TGS)입니다.                                                                 |
|-------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| [`solve_articulation_contact_last`](#isaaclab.sim.PhysxCfg.solve_articulation_contact_last)                 | 연결부 소프트웨어 내의 순서를 변경합니다.                                                          |
| [`min_position_iteration_count`](#isaaclab.sim.PhysxCfg.min_position_iteration_count)                       | 강체, 천, 입자 등에 대한 최소 솔버 위치 반복 횟수.                           |
| [`max_position_iteration_count`](#isaaclab.sim.PhysxCfg.max_position_iteration_count)                       | 강체, 천, 입자 등에 대한 최대 솔버 위치 반복 횟수.                           |
| [`min_velocity_iteration_count`](#isaaclab.sim.PhysxCfg.min_velocity_iteration_count)                       | 강체, 천, 입자 등에 대한 최소 솔버 속도 반복 횟수.                           |
| [`max_velocity_iteration_count`](#isaaclab.sim.PhysxCfg.max_velocity_iteration_count)                       | 강체, 천, 입자 등에 대한 최대 솔버 속도 반복 횟수.                           |
| [`enable_ccd`](#isaaclab.sim.PhysxCfg.enable_ccd)                                                           | 두 번째 광역 패스를 활성화하여 객체 간 터널링을 방지합니다.                                                      |
| [`enable_stabilization`](#isaaclab.sim.PhysxCfg.enable_stabilization)                                       | 솔버에서 추가 안정화 패스를 활성화/비활성화합니다.                                                        |
| [`enable_external_forces_every_iteration`](#isaaclab.sim.PhysxCfg.enable_external_forces_every_iteration)   | TGS 솔버에서 각 위치 반복마다 외부 힘을 활성화/비활성화합니다.                                                |
| [`enable_enhanced_determinism`](#isaaclab.sim.PhysxCfg.enable_enhanced_determinism)                         | 성능을 희생하여 개선된 결정론을 활성화/비활성화합니다.                                                     |
| [`bounce_threshold_velocity`](#isaaclab.sim.PhysxCfg.bounce_threshold_velocity)                             | 접촉이 반사되는 상대 속도 임계값(m/s 단위).                                                                  |
| [`friction_offset_threshold`](#isaaclab.sim.PhysxCfg.friction_offset_threshold)                             | 마찰력을 경험하는 접촉점의 임계값(m 단위).                                                                   |
| [`friction_correlation_distance`](#isaaclab.sim.PhysxCfg.friction_correlation_distance)                     | 단일 마찰 앵커 지점으로 접촉을 병합하는 거리 임계값(m 단위).                                                |
| [`gpu_max_rigid_contact_count`](#isaaclab.sim.PhysxCfg.gpu_max_rigid_contact_count)                         | 고정 호스트 메모리에 할당된 rigid contact 스트림 버퍼의 크기입니다.                                         |
| [`gpu_max_rigid_patch_count`](#isaaclab.sim.PhysxCfg.gpu_max_rigid_patch_count)                             | 고정 호스트 메모리에 할당된 rigid contact 패치 스트림 버퍼의 크기입니다.                                    |
| [`gpu_found_lost_pairs_capacity`](#isaaclab.sim.PhysxCfg.gpu_found_lost_pairs_capacity)                     | GPU 전역 메모리에 할당된 발견 및 분실 버퍼의 용량입니다.                                                  |
| [`gpu_found_lost_aggregate_pairs_capacity`](#isaaclab.sim.PhysxCfg.gpu_found_lost_aggregate_pairs_capacity) | 전체 시스템에서 GPU 전역 메모리에 할당된 발견 및 분실 버퍼의 용량입니다.                                    |
| [`gpu_total_aggregate_pairs_capacity`](#isaaclab.sim.PhysxCfg.gpu_total_aggregate_pairs_capacity)           | GPU 전역 메모리에 할당된 총Aggregate 쌍의 용량입니다.                                                     |
| [`gpu_collision_stack_size`](#isaaclab.sim.PhysxCfg.gpu_collision_stack_size)                               | 고정 호스트 메모리에 할당된 충돌 스택 버퍼의 크기입니다.                                                  |
| [`gpu_heap_capacity`](#isaaclab.sim.PhysxCfg.gpu_heap_capacity)                                             | GPU 및 고정 호스트 메모리 힙의 초기 용량입니다.                                                           |
| [`gpu_temp_buffer_capacity`](#isaaclab.sim.PhysxCfg.gpu_temp_buffer_capacity)                               | 고정 호스트 메모리에 할당된 임시 버퍼의 용량입니다.                                                       |
| [`gpu_max_num_partitions`](#isaaclab.sim.PhysxCfg.gpu_max_num_partitions)                                   | GPU 동역학 파이프라인의 파티션 수 제한입니다.                                                            |
| [`gpu_max_soft_body_contacts`](#isaaclab.sim.PhysxCfg.gpu_max_soft_body_contacts)                           | 고정 호스트 메모리에 할당된 소프트 바디 연락 스트림 버퍼의 크기입니다.                                    |
| [`gpu_max_particle_contacts`](#isaaclab.sim.PhysxCfg.gpu_max_particle_contacts)                             | 고정 호스트 메모리에 할당된 입자 연락 스트림 버퍼의 크기입니다.                                           |

#### solver_type *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)[0, 1]*

사용할 솔버의 유형입니다. 기본값은 1(TGS)입니다.

사용 가능한 솔버:

* `0`: PGS (Projective Gauss-Seidel)
* `1`: TGS (Temporal Gauss-Seidel)

#### solve_articulation_contact_last *: [bool](https://docs.python.org/3/library/functions.html#bool)*

관절 소프트웨어 내의 순서를 변경합니다. 기본값은 False입니다.

PhysX는 관절에서 제약 조건을 처리하는 데 엄격한 순서를 사용합니다. 각 제약 조건 해결의 결과는 조인트 및 연결된 링크 속도를 수정합니다. 그러나 기본 순서는 그립 시나리오에 이상적이지 않을 수 있습니다. 왜냐하면 솔버가 마지막에 해결되는 제약 조건 유형을 선호하기 때문입니다. 이는 특히 강성 제약 조건 시스템에서 vanishingly 작은 시뮬레이션 타임스텝에 의존하지 않고 해결하기 어려운 경우에 해당됩니다.

동적 접촉 해결이 그립에서 중요한 부분이므로, 솔버의 시작이 아닌 끝에 동적 접촉을 해결하는 것이 더 타당할 수 있습니다. 이 매개변수는 기본 순서를 수정하여 이러한 변경을 활성화합니다.

자세한 내용은 [여기](https://docs.omniverse.nvidia.com/kit/docs/omni_physics/107.3/dev_guide/guides/articulation_stability_guide.html#articulation-solver-order)를 참조하십시오.

#### Versionadded
버전 v2.3부터 새로 추가됨: 이 매개변수는 Isaac Sim 5.1에서만 사용할 수 있습니다.

#### min_position_iteration_count *: [int](https://docs.python.org/3/library/functions.html#int)*

솔버 위치 반복의 최소 횟수( rigid bodies, cloth, particles 등). 기본값은 1입니다.

#### NOTE
옴니버스의 각 물리 액터는 자체 솔버 반복 횟수를 지정합니다. 솔버는 가장 높은 반복 횟수를 가진 액터의 반복 횟수를 취하고 `[min_position_iteration_count, max_position_iteration_count]` 범위로 클램프합니다.

#### max_position_iteration_count *: [int](https://docs.python.org/3/library/functions.html#int)*

솔버 위치 반복의 최대 횟수( rigid bodies, cloth, particles 등). 기본값은 255입니다.

#### NOTE
옴니버스의 각 물리 액터는 자체 솔버 반복 횟수를 지정합니다. 솔버는 가장 높은 반복 횟수를 가진 액터의 반복 횟수를 취하고 `[min_position_iteration_count, max_position_iteration_count]` 범위로 클램프합니다.

#### min_velocity_iteration_count *: [int](https://docs.python.org/3/library/functions.html#int)*

솔버 속도 반복의 최소 횟수( rigid bodies, cloth, particles 등). 기본값은 0입니다.

#### NOTE
옴니버스의 각 물리 액터는 자체 솔버 반복 횟수를 지정합니다. 솔버는 가장 높은 반복 횟수를 가진 액터의 반복 횟수를 취하고 `[min_velocity_iteration_count, max_velocity_iteration_count]` 범위로 클램프합니다.

#### max_velocity_iteration_count *: [int](https://docs.python.org/3/library/functions.html#int)*

솔버 속도 반복의 최대 횟수( rigid bodies, cloth, particles 등). 기본값은 255입니다.

#### NOTE
옴니버스의 각 물리 액터는 자체 솔버 반복 횟수를 지정합니다. 솔버는 가장 높은 반복 횟수를 가진 액터의 반복 횟수를 취하고 `[min_velocity_iteration_count, max_velocity_iteration_count]` 범위로 클램프합니다.

#### enable_ccd *: [bool](https://docs.python.org/3/library/functions.html#bool)*

객체 간 터널링을 방지할 수 있는 두 번째 광역 패스를 활성화합니다. 기본값은 False입니다.

#### enable_stabilization *: [bool](https://docs.python.org/3/library/functions.html#bool)*

솔버에서 추가 안정화 패스를 활성화/비활성화합니다. 기본값은 False입니다.

#### NOTE
시뮬레이션 스텝 크기가 큰 경우(즉, 30 Hz 미만이거나 0.0333초 초과) 이 플래그를 true로 설정하는 것을 권장합니다.

#### WARNING
이 플래그를 활성화하면 접촉 센서에서 보고하는 접촉 힘이 부정확할 수 있습니다.

#### enable_external_forces_every_iteration *: [bool](https://docs.python.org/3/library/functions.html#bool)*

TGS 솔버에서 각 위치 반복마다 외부 힘을 활성화/비활성화합니다. 기본값은 False입니다.

TGS 솔버([`solver_type`](#isaaclab.sim.PhysxCfg.solver_type)가 1)를 사용할 때 이 플래그는 솔버의 각 위치 반복마다 외부 힘을 활성화할 수 있게 해줍니다. 이는 속도 업데이트의 정확성을 향상시키는 데 도움이 될 수 있습니다. 시뮬레이션에서 생성되는 속도가 노이즈가 많은 경우 이 플래그를 활성화하는 것을 고려하십시오. 속도 반복 횟수를 늘리고 이 플래그를 함께 사용하면 속도 업데이트의 정확성을 향상시킬 수 있습니다.

#### NOTE
PGS 솔버([`solver_type`](#isaaclab.sim.PhysxCfg.solver_type)가 0)를 사용할 때는 이 플래그가 무시됩니다.

#### enable_enhanced_determinism *: [bool](https://docs.python.org/3/library/functions.html#bool)*

성능을 희생하여 개선된 결정론을 활성화/비활성화합니다. 기본값은 False입니다.

PhysX 결정론에 대한 자세한 내용은 [여기](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/docs/RigidBodyDynamics.html#enhanced-determinism)를 참조하십시오.

#### bounce_threshold_velocity *: [float](https://docs.python.org/3/library/functions.html#float)*

접촉이 반사되는 상대 속도 임계값(m/s 단위). 기본값은 0.5 m/s입니다.

#### friction_offset_threshold *: [float](https://docs.python.org/3/library/functions.html#float)*

마찰력을 경험하는 접촉점의 임계값(m 단위). 기본값은 0.04 m입니다.

#### friction_correlation_distance *: [float](https://docs.python.org/3/library/functions.html#float)*

단일 마찰 앵커 지점으로 접촉을 병합하는 거리 임계값(m 단위). 기본값은 0.025 m입니다.

#### gpu_max_rigid_contact_count *: [int](https://docs.python.org/3/library/functions.html#int)*

고정 호스트 메모리에 할당된 rigid contact 스트림 버퍼의 크기입니다. 기본값은 2 \*\* 23입니다.

#### gpu_max_rigid_patch_count *: [int](https://docs.python.org/3/library/functions.html#int)*

고정 호스트 메모리에 할당된 rigid contact 패치 스트림 버퍼의 크기입니다. 기본값은 5 \* 2 \*\* 15입니다.

#### gpu_found_lost_pairs_capacity *: [int](https://docs.python.org/3/library/functions.html#int)*

GPU 전역 메모리에 할당된 발견 및 손실 버퍼의 용량입니다. 기본값은 2 \*\* 21입니다.

이는 BP에서 발견/손실 페어 보고서에 사용됩니다.

#### gpu_found_lost_aggregate_pairs_capacity *: [int](https://docs.python.org/3/library/functions.html#int)*

집계 시스템에서 GPU 전역 메모리에 할당된 발견 및 손실 버퍼의 용량입니다. 기본값은 2 \*\* 25입니다.

이는 AABB 관리자에서 발견/손실 페어 보고서에 사용됩니다.

#### gpu_total_aggregate_pairs_capacity *: [int](https://docs.python.org/3/library/functions.html#int)*

GPU 전역 메모리에 할당된 총 집계 페어 수의 용량입니다. 기본값은 2 \*\* 21입니다.

#### gpu_collision_stack_size *: [int](https://docs.python.org/3/library/functions.html#int)*

고정 호스트 메모리에 할당된 충돌 스택 버퍼의 크기입니다. 기본값은 2 \*\* 26입니다.

#### gpu_heap_capacity *: [int](https://docs.python.org/3/library/functions.html#int)*

GPU 및 고정 호스트 메모리 힙의 초기 용량입니다. 더 많은 메모리가 필요한 경우 추가 메모리가 할당됩니다. 기본값은 2 \*\* 26입니다.

#### gpu_temp_buffer_capacity *: [int](https://docs.python.org/3/library/functions.html#int)*

고정 호스트 메모리에 할당된 임시 버퍼의 용량입니다. 기본값은 2 \*\* 24입니다.

#### gpu_max_num_partitions *: [int](https://docs.python.org/3/library/functions.html#int)*

GPU 동력학 파이프라인에서의 파티션 수 제한입니다. 기본값은 8입니다.

이 변수는 2의 거듭제곱이어야 합니다. 32보다 큰 값은 현재 지원되지 않습니다. 범위: (1, 32)

#### gpu_max_soft_body_contacts *: [int](https://docs.python.org/3/library/functions.html#int)*

고정 호스트 메모리에 할당된 소프트 바디 컨택트 스트림 버퍼의 크기입니다. 기본값은 2 \*\* 20입니다.

#### gpu_max_particle_contacts *: [int](https://docs.python.org/3/library/functions.html#int)*

고정 호스트 메모리에 할당된 파티클 컨택트 스트림 버퍼의 크기입니다. 기본값은 2 \*\* 20입니다.

### *class* isaaclab.sim.RenderCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

Omniverse RTX 렌더러를 위한 구성입니다.

이러한 매개변수는 Omniverse RTX 렌더러를 구성하는 데 사용됩니다.

IsaacLab의 기본값은 경험 파일에서 설정됩니다:

* `apps/isaaclab.python.rendering.kit`: GUI가 활성화된 상태로 시뮬레이션을 실행할 때 사용되는 설정입니다.
* `apps/isaaclab.python.headless.rendering.kit`: 헤드리스 모드로 시뮬레이션을 실행할 때 사용되는 설정입니다.

여기서 어떤 값이라도 설정하면 경험 파일의 기본값이 재정의됩니다.

자세한 내용은 [Omniverse RTX Renderer 문서](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/rtx-renderer.html)를 참조하세요.

**속성:**

| [`enable_translucency`](#isaaclab.sim.RenderCfg.enable_translucency)                         | 성능의 대가로 유리와 같은 반사 투과 표면에 대한 반투명성을 활성화합니다.   |
|----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| [`enable_reflections`](#isaaclab.sim.RenderCfg.enable_reflections)                           | 성능의 대가로 반사를 활성화합니다.                                                     |
| [`enable_global_illumination`](#isaaclab.sim.RenderCfg.enable_global_illumination)           | 성능의 대가로 확산 전역 조명을 활성화합니다.                                    |
| [`antialiasing_mode`](#isaaclab.sim.RenderCfg.antialiasing_mode)                             | 사용할 안티-aliasing 모드를 선택합니다.                                                                   |
| [`enable_dlssg`](#isaaclab.sim.RenderCfg.enable_dlssg)                                       | "DLSS-G 사용을 활성화합니다.                                                                              |
| [`enable_dl_denoiser`](#isaaclab.sim.RenderCfg.enable_dl_denoiser)                           | DL 디노이저 사용을 활성화합니다.                                                                        |
| [`dlss_mode`](#isaaclab.sim.RenderCfg.dlss_mode)                                             | DLSS 안티-aliasing에 대한 성능/품질 트레이드오프 모드를 선택합니다.                                   |
| [`enable_direct_lighting`](#isaaclab.sim.RenderCfg.enable_direct_lighting)                   | 조명의 직접 광 기여를 활성화합니다.                                                           |
| [`samples_per_pixel`](#isaaclab.sim.RenderCfg.samples_per_pixel)                             | 픽셀당 직접 조명 샘플 수를 정의합니다.                                                           |
| [`enable_shadows`](#isaaclab.sim.RenderCfg.enable_shadows)                                   | 성능의 대가로 그림자를 활성화합니다.                                                              |
| [`enable_ambient_occlusion`](#isaaclab.sim.RenderCfg.enable_ambient_occlusion)               | 성능의 대가로 주변 음영을 활성화합니다.                                               |
| [`dome_light_upper_lower_strategy`](#isaaclab.sim.RenderCfg.dome_light_upper_lower_strategy) | 도미 라이트 샘플링 방법을 선택합니다.                                                                    |
| [`carb_settings`](#isaaclab.sim.RenderCfg.carb_settings)                                     | 네이티브 이름을 가진 모든 CARB 렌더링 설정을 제공하기 위한 사용자용 일반 사전입니다.                  |
| [`rendering_mode`](#isaaclab.sim.RenderCfg.rendering_mode)                                   | 렌더링 모드.                                                                                      |

#### enable_translucency *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

성능의 대가로 유리와 같은 반사 투과 표면에 대한 반투명성을 활성화합니다. 기본값은 False입니다.

이는 변수 `/rtx/translucency/enabled`로 설정됩니다.

#### enable_reflections *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

성능의 대가로 반사를 활성화합니다. 기본값은 False입니다.

이는 변수 `/rtx/reflections/enabled`로 설정됩니다.

#### enable_global_illumination *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

성능의 대가로 확산 전역 조명을 활성화합니다. 기본값은 False입니다.

이는 변수 `/rtx/indirectDiffuse/enabled`로 설정됩니다.

#### antialiasing_mode *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['Off', 'FXAA', 'DLSS', 'TAA', 'DLAA'] | [None](https://docs.python.org/3/library/constants.html#None)*

사용할 안티-aliasing 모드를 선택합니다. 기본값은 DLSS입니다.

- **DLSS**: AI를 사용하여 낮은 해상도 입력에서 더 높은 해상도 프레임을 출력함으로써 성능을 향상시킵니다. DLSS는 여러 낮은 해상도 이미지를 샘플링하고 이전 프레임의 동작 데이터 및 피드백을 사용하여 네이티브 품질 이미지를 재구성합니다.
- **DLAA**: DLSS에 사용된 슈퍼 해상도 기술을 활용하여 네이티브 해상도 이미지를 재구성함으로써 이미지 품질을 극대화하는 AI 기반 안티-aliasing 기법을 제공합니다.

이는 변수 `/rtx/post/dlss/execMode`로 설정됩니다.

#### enable_dlssg *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

“DLSS-G 사용을 활성화합니다. 기본값은 False입니다.

DLSS 프레임 생성은 AI를 사용하여 순차 프레임과 동작 데이터를 분석하여 추가적인 고품질 프레임을 생성함으로써 성능을 향상시킵니다.

#### 참고
이 기능은 Ada Lovelace 아키텍처 GPU가 필요합니다. 이 기능을 활성화하면 추가적인 스레드 관련 활동이 발생하여 성능이 저하될 수 있습니다.

이는 변수 `/rtx-transient/dlssg/enabled`로 설정됩니다.

#### enable_dl_denoiser *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

DL 디노이저 사용을 활성화합니다.

DL 디노이저는 렌더링 품질을 향상시키는 데 도움이 될 수 있지만, 성능 저하의 대가를 치릅니다.

이는 변수 `/rtx-transient/dldenoiser/enabled`로 설정됩니다.

#### dlss_mode *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)[0, 1, 2, 3] | [None](https://docs.python.org/3/library/constants.html#None)*

DLSS 안티-aliasing에 대한 성능/품질 트레이드오프 모드를 선택합니다. 기본값은 0입니다.

유효한 값은 다음과 같습니다:

* 0 (성능)
* 1 (균형)
* 2 (품질)
* 3 (자동)

이는 변수 `/rtx/post/dlss/execMode`로 설정됩니다.

#### enable_direct_lighting *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

조명의 직접 광 기여를 활성화합니다. 기본값은 False입니다.

이는 변수 `/rtx/directLighting/enabled`로 설정됩니다.

#### samples_per_pixel *: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None)*

픽셀당 직접 조명 샘플 수를 정의합니다. 기본값은 1입니다.

더 높은 값은 성능의 대가로 직접 조명 품질을 향상시킵니다.

이는 변수 `/rtx/directLighting/sampledLighting/samplesPerPixel`로 설정됩니다.

#### enable_shadows *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

성능의 대가로 그림자를 활성화합니다. 기본값은 True입니다.

비활성화하면 조명이 그림자를 드리우지 않습니다.

이는 변수 `/rtx/shadows/enabled`로 설정됩니다.

#### enable_ambient_occlusion *: [bool](https://docs.python.org/3/library/functions.html#bool) | [None](https://docs.python.org/3/library/constants.html#None)*

주변 광 occlusion을 활성화하지만 성능 저하의 대가를 치러야 합니다. 기본값은 False입니다.

이 설정은 다음 변수로 지정됩니다: `/rtx/ambientOcclusion/enabled`.

#### dome_light_upper_lower_strategy *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)[0, 3, 4] | [None](https://docs.python.org/3/library/constants.html#None)*

ドーム 라이트를 샘플링하는 방법을 선택합니다. 기본값은 0입니다.
자세한 내용은 [documentation](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/rtx-renderer_common.html#dome-light)을 참조하세요.

유효한 값은 다음과 같습니다:

* 0: **Image-Based Lighting (IBL)** - 고주파 Dome Light 텍스처에서도 가장 정확합니다.
  실시간 모드에서는 샘플링 아티팩트를 유발할 수 있습니다.
* 3: **Limited Image-Based Lighting** - 반사와 굴절에 대해서만 샘플링됩니다. 가장 빠르지만 가장 정확하지 않습니다. Dome Light가 다른 광원보다 기여도가 낮은 경우에 유용합니다.
* 4: **Approximated Image-Based Lighting** - 실시간 모드에서 빠르고 아티팩트가 없는 샘플링이지만, 저주파 텍스처(예: 태양 원반이 없는 하늘, 이때 태양은 별도의 Distant Light로 처리됨)에서만 잘 작동합니다. Direct Lighting 디노이저를 활성화해야 합니다.

이 설정은 다음 변수로 지정됩니다: `/rtx/domeLight/upperLowerStrategy`.

#### carb_settings *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)] | [None](https://docs.python.org/3/library/constants.html#None)*

사용자가 네이티브 이름의 모든 carb 렌더링 설정을 제공할 수 있는 일반적인 사전입니다.

사전 키는 carb 설정, .kit 파일 설정 또는 파이썬 변수처럼 형식화할 수 있습니다. 예를 들어, 키-값 쌍은 다음과 같이 지정할 수 있습니다:

- `/rtx/translucency/enabled: False` (carb)
- `rtx.translucency.enabled: False` (.kit)
- `rtx_translucency_enabled: False` (python)

#### rendering_mode *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['performance', 'balanced', 'quality'] | [None](https://docs.python.org/3/library/constants.html#None)*

렌더링 모드.

이 동작은 실행 가능한 스크립트에 CLI 인수 `--rendering_mode`를 전달하는 것과 동일합니다.

## 시뮬레이션 컨텍스트 빌더

#### simulation_context.build_simulation_context(gravity_enabled: [bool](https://docs.python.org/3/library/functions.html#bool) = True, device: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'cuda:0', dt: [float](https://docs.python.org/3/library/functions.html#float) = 0.01, sim_cfg: [SimulationCfg](#isaaclab.sim.SimulationCfg) | [None](https://docs.python.org/3/library/constants.html#None) = None, add_ground_plane: [bool](https://docs.python.org/3/library/functions.html#bool) = False, add_lighting: [bool](https://docs.python.org/3/library/functions.html#bool) = False, auto_add_lighting: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [Iterator](https://docs.python.org/3/library/collections.abc.html#collections.abc.Iterator)[[SimulationContext](#isaaclab.sim.SimulationContext)]

제공된 설정으로 시뮬레이션 컨텍스트를 빌드하는 컨텍스트 관리자입니다.

이 함수는 시뮬레이션 컨텍스트 생성을 용이하게 하고, 타임스텝, 중력, 디바이스, 지면 평면 및 조명과 같은 장면 요소 등 시뮬레이션의 다양한 측면을 구성하는 유연성을 제공합니다.

`sim_cfg`가 None이면, 함수 인수의 인수로 기반하여 매개변수가 덮어쓰여진 기본 설정을 가진 [`SimulationCfg`](#isaaclab.sim.SimulationCfg) 인스턴스가 생성됩니다.

컨텍스트 관리자 함수의 사용 예시는 다음과 같습니다:

```python
with build_simulation_context() as sim:
    # Design the scene

    # Play the simulation
    sim.reset()
    while sim.is_playing():
        sim.step()
```

* **매개변수:**
  * **create_new_stage** – 새 스테이지를 생성할지 여부. 기본값은 True입니다.
  * **gravity_enabled** – 시뮬레이션에서 중력을 활성화할지 여부. 기본값은 True입니다.
  * **device** – 시뮬레이션을 실행할 디바이스. 기본값은 “cuda:0”입니다.
  * **dt** – 시뮬레이션의 타임스텝. 기본값은 0.01입니다.
  * **sim_cfg** – [`isaaclab.sim.SimulationCfg`](#isaaclab.sim.SimulationCfg)를 시뮬레이션에 사용할 설정. 기본값은 None입니다.
  * **add_ground_plane** – 시뮬레이션에 지면 평면을 추가할지 여부. 기본값은 False입니다.
  * **add_lighting** – 시뮬레이션에ドーム 라이트를 추가할지 여부. 기본값은 False입니다.
  * **auto_add_lighting** – 시뮬레이션에 GUI가 있는 경우ドーム 라이트를 자동으로 추가할지 여부. 기본값은 False입니다. GUI에서 디버깅 테스트를 실행할 때 유용합니다.
* **반환값:**
  시뮬레이션에 사용할 시뮬레이션 컨텍스트.
