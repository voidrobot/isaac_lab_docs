# isaaclab.devices

다양한 텔레오퍼레이션 장치에 대한 인터페이스를 제공하는 서브 패키지입니다.

현재 다음 카테고리의 장치들이 지원됩니다:

* **Keyboard**: WASD 및 화살표 키가 있는 표준 키보드.
* **Spacemouse**: 6자유도를 가진 3D 마우스.
* **Gamepad**: 2개의 2D 조이스틱과 버튼이 있는 게임패드. 예: Xbox 컨트롤러.
* **OpenXR**: 엄지/검지 끝 평균을 이용한 손 추적으로 대상 자세를 구동합니다. 집기는 핀칭으로 수행됩니다.
* **Haply**: 위치, 방향 추적 및 힘 피드백이 있는 햅틱 장치(Inverse3 + VerseGrip).

모든 장치 인터페이스는 [`DeviceBase`](#isaaclab.devices.DeviceBase) 클래스를 상속받으며, 모든 장치에 대한 공통 인터페이스를 제공합니다. 장치 인터페이스는 [`DeviceBase.advance()`](#isaaclab.devices.DeviceBase.advance) 메서드가 호출될 때 입력 데이터를 읽습니다. 또한, 주변 장치에서 특정 입력이 눌러졌을 때 호출될 사용자 정의 콜백 함수를 추가하는 [`DeviceBase.add_callback()`](#isaaclab.devices.DeviceBase.add_callback) 함수도 제공합니다.

### 클래스

| [`DeviceBase`](#isaaclab.devices.DeviceBase)            | 텔레오퍼레이션 장치용 인터페이스 클래스.                                                    |
|---------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| [`RetargeterBase`](#isaaclab.devices.RetargeterBase)    | 입력 데이터 재타겟팅을 위한 기본 인터페이스.                                                       |
| `Se2Gamepad`                                            | SE(2) 명령을 속도 명령으로 보내는 게임패드 컨트롤러.                            |
| `Se3Gamepad`                                            | SE(3) 명령을 델타 자세 및 바이너리 명령(열기/닫기)으로 보내는 게임패드 컨트롤러.  |
| `Se2Keyboard`                                           | SE(2) 명령을 속도 명령으로 보내는 키보드 컨트롤러.                           |
| `Se3Keyboard`                                           | SE(3) 명령을 델타 자세 및 바이너리 명령(열기/닫기)으로 보내는 키보드 컨트롤러. |
| `Se2SpaceMouse`                                         | SE(2) 명령을 델타 자세로 보내는 스페이스 마우스 컨트롤러.                              |
| `Se3SpaceMouse`                                         | SE(3) 명령을 델타 자세로 보내는 스페이스 마우스 컨트롤러.                              |
| `HaplyDevice`                                           | 힘 피드백과 함께 SE(3) 명령을 보내는 Haply 장치 컨트롤러.                        |
| `OpenXRDevice`                                          | 텔레오퍼레이션 및 상호작용을 위한 OpenXR 기반 장치.                                      |
| `ManusVive`                                             | 텔레오퍼레이션 및 상호작용을 위한 Manus 장갑과 Vive 트래커.                                |
| `isaaclab.devices.openxr.retargeters.GripperRetargeter` | 손 추적 데이터를 기반으로 한 그리퍼 제어를 위한 특수 재타겟터.                         |
| `isaaclab.devices.openxr.retargeters.Se3AbsRetargeter`  | 절대 위치 지정을 사용하여 OpenXR 손 추적 데이터를 엔드이펙터 명령으로 재타겟팅.         |
| `isaaclab.devices.openxr.retargeters.Se3RelRetargeter`  | 상대 위치 지정을 사용하여 OpenXR 손 추적 데이터를 엔드이펙터 명령으로 재타겟팅.         |
| `isaaclab.devices.openxr.retargeters.GR1T2Retargeter`   | OpenXR 손 추적 데이터를 GR1T2 손 엔드이펙터 명령으로 재타겟팅.                         |

### 모듈

| `isaaclab.devices.openxr.retargeters`   | 입력 장치 데이터를 로봇 명령으로 매핑하는 재타겟터.   |
|-----------------------------------------|----------------------------------------------------------------|

## Device Base

### *class* isaaclab.devices.DeviceBase

텔레오퍼레이션 장치용 인터페이스 클래스입니다.

파생 클래스는 두 가지 구현 옵션이 있습니다:

1. \_get_raw_data()를 오버라이드하고 기본 advance() 구현을 사용:
   이 접근 방식은 빌트인 재타겟팅 로직을 활용하면서 원시 데이터 acquisiton만 커스터마이즈해야 하는 장치에 적합합니다.
2. advance()를 완전히 오버라이드:
   이 접근 방식은 명령 생성 프로세스에 대한 완전한 제어를 제공하며, \_get_raw_data()를 완전히 무시할 수 있습니다.

**메서드:**

| [`__init__`](#isaaclab.devices.DeviceBase.__init__)([retargeters])     | 텔레오퍼레이션 인터페이스를 초기화합니다.                   |
|------------------------------------------------------------------------|-----------------------------------------------------------|
| [`reset`](#isaaclab.devices.DeviceBase.reset)()                        | 내부 상태를 초기화합니다.                                      |
| [`add_callback`](#isaaclab.devices.DeviceBase.add_callback)(key, func) | 키보드에 추가 함수를 바인딩합니다.                |
| [`advance`](#isaaclab.devices.DeviceBase.advance)()                    | 현재 장치 상태를 처리하고 제어 명령을 반환합니다. |

**클래스:**

| [`TrackingTarget`](#isaaclab.devices.DeviceBase.TrackingTarget)                             | 장치 간에 공유되는 표준 추적 대상입니다.   |
|---------------------------------------------------------------------------------------------|----------------------------------------------------|
| [`MotionControllerDataRowIndex`](#isaaclab.devices.DeviceBase.MotionControllerDataRowIndex) | 모션 컨트롤러 2x7 배열의 행입니다.           |
| [`MotionControllerInputIndex`](#isaaclab.devices.DeviceBase.MotionControllerInputIndex)     | 모션 컨트롤러 입력 행의 인덱스입니다.        |

#### \_\_init_\_(retargeters: [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.devices.retargeter_base.RetargeterBase](#isaaclab.devices.RetargeterBase)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

텔레오퍼레이션 인터페이스를 초기화합니다.

* **매개변수:**
  **retargeters** – 장치 데이터를 로봇 명령으로 변환하는 컴포넌트 목록.
  None 또는 빈 목록인 경우, 장치는 기본 데이터 형식으로 출력합니다.

#### *abstract* reset()

내부 상태를 초기화합니다.

#### *abstract* add_callback(key: [Any](https://docs.python.org/3/library/typing.html#typing.Any), func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

키보드에 추가 함수를 바인딩합니다.

* **매개변수:**
  * **key** – 확인할 버튼입니다.
  * **func** – 키가 눌렸을 때 호출할 함수입니다. 콜백 함수는 인수를 받지 않아야 합니다.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

현재 장치 상태를 처리하고 제어 명령을 반환합니다.

이 메서드는 장치로부터 원시 데이터를 가져오고 선택적으로 재타겟팅을 적용하여 로봇 명령으로 변환합니다.

파생 클래스는 다음 중 하나의 방식을 구현할 수 있습니다:
1. \_get_raw_data()를 오버라이드하고 이 기본 구현을 사용하거나,
2. 이 메서드를 완전히 오버라이드하여 사용자 정의 명령 처리를 수행

* **반환값:**
  재타겟터가 구성되지 않은 경우, 장치의 기본 형식으로 원시 장치 데이터를 반환합니다.
  재타겟터가 구성된 경우, 모든 재타겟터의 출력을 연결한 torch.Tensor를 반환합니다.

#### *class* TrackingTarget

장치 간에 공유되는 표준 추적 대상입니다.

#### *class* MotionControllerDataRowIndex

모션 컨트롤러 2x7 배열의 행입니다.

#### *class* MotionControllerInputIndex

모션 컨트롤러 입력 행의 인덱스입니다.

## Retargeter Base

### *class* isaaclab.devices.RetargeterBase

입력 데이터 재타겟팅을 위한 기본 인터페이스입니다.

이 추상 클래스는 원시 장치 데이터를 로봇 제어 명령으로 변환하는 컴포넌트에 대한 인터페이스를 정의합니다. 구현은 다음을 포함하여 다양한 유형의 변환을 처리할 수 있습니다:
- 손 관절 데이터를 엔드이펙터 자세로
- 입력 장치 명령을 로봇 이동으로
- 센서 데이터를 제어 신호로

**메서드:**

| [`__init__`](#isaaclab.devices.RetargeterBase.__init__)(cfg)              | 재타겟터를 초기화합니다.                                     |
|---------------------------------------------------------------------------|----------------------------------------------------------------|
| [`retarget`](#isaaclab.devices.RetargeterBase.retarget)(data)             | 입력 데이터를 원하는 출력 형식으로 재타겟팅합니다.                  |
| [`get_requirements`](#isaaclab.devices.RetargeterBase.get_requirements)() | 이 재타겟터에 필요한 데이터 기능 목록을 반환합니다. |

**클래스:**

| [`Requirement`](#isaaclab.devices.RetargeterBase.Requirement)   | 장치의 원시 데이터 피드에서 요구할 수 있는 기능입니다.   |
|-----------------------------------------------------------------|--------------------------------------------------------------------|

#### \_\_init_\_(cfg: RetargeterCfg)

재타겟터를 초기화합니다.

* **매개변수:**
  **cfg** – 재타겟터에 대한 구성

#### *class* Requirement

장치의 원시 데이터 피드에서 요구할 수 있는 기능입니다.

#### *abstract* retarget(data: [Any](https://docs.python.org/3/library/typing.html#typing.Any)) → [Any](https://docs.python.org/3/library/typing.html#typing.Any)

입력 데이터를 원하는 출력 형식으로 재타겟팅합니다.

* **매개변수:**
  **data** – 변환할 원시 입력 데이터
* **반환값:**
  구현별 형식으로 재타겟팅된 데이터

#### get_requirements() → [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.devices.retargeter_base.RetargeterBase.Requirement](#isaaclab.devices.RetargeterBase.Requirement)]

이 재타겟터에 필요한 데이터 기능 목록을 반환합니다.

하위 호환성을 위해 기본적으로 모든 사용 가능한 기능을 요청합니다.
구현에서는 필요한 것만 요청하도록 오버라이드해야 합니다.

## 게임 패드

### *class* isaaclab.devices.Se2Gamepad

Bases: [`DeviceBase`](#isaaclab.devices.DeviceBase)

SE(2) 명령을 속도 명령으로 보내는 게임패드 컨트롤러입니다.

이 클래스는 이동 기반(예: 사족 로봇)을 위한 게임패드 컨트롤러를 제공하도록 설계되었습니다.
이 클래스는 Omniverse 게임패드 인터페이스를 사용하여 게임패드 이벤트를 수신하고, 이를 로봇의 작업 공간 명령으로 매핑합니다.

명령은 기본 선형 및 각도 속도로 구성됩니다: $(v_x, v_y, \omega_z)$.

키 바인딩:
: | 명령                 | 키 (+축)       | 키 (-축)       |
  |----------------------|----------------|----------------|
  | x축 이동             | 왼쪽 스틱 위   | 왼쪽 스틱 아래 |
  | y축 이동             | 왼쪽 스틱 오른쪽 | 왼쪽 스틱 왼쪽 |
  | z축 회전             | 오른쪽 스틱 오른쪽 | 오른쪽 스틱 왼쪽 |

#### SEE ALSO
게임패드 인터페이스의 공식 문서: [Carb Gamepad Interface](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref/input-devices/gamepad.html).

**메서드:**

| `__init__`(cfg)           | 게임패드 레이어를 초기화합니다.                 |
|---------------------------|-----------------------------------------------|
| `reset`()                 | 내부 상태를 재설정합니다.                      |
| `add_callback`(key, func) | 게임패드에 추가 바인딩 함수를 등록합니다.       |
| `advance`()               | 게임패드 이벤트 상태의 결과를 제공합니다.      |

**클래스:**

| `MotionControllerDataRowIndex`   | 모션 컨트롤러 2x7 배열의 행입니다.         |
|----------------------------------|--------------------------------------------------|
| `MotionControllerInputIndex`     | 모션 컨트롤러 입력 행의 인덱스입니다.      |
| `TrackingTarget`                 | 장치 간 공유되는 표준 추적 대상입니다. |

#### \_\_init_\_(cfg: Se2GamepadCfg)

게임패드 레이어를 초기화합니다.

* **Parameters:**
  * **v_x_sensitivity** – x축 방향의 선형 속도 크기 스케일링 값. 기본값은 1.0입니다.
  * **v_y_sensitivity** – y축 방향의 선형 속도 크기 스케일링 값. 기본값은 1.0입니다.
  * **omega_z_sensitivity** – z축 방향의 각속도 크기 스케일링 값. 기본값은 1.0입니다.
  * **dead_zone** – 게임패드의 데드 존 크기. 이 값보다 작은 게임패드 이벤트 값은 무시됩니다. 기본값은 0.01입니다.

#### reset()

내부 상태를 재설정합니다.

#### add_callback(key: carb.input.GamepadInput, func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

게임패드에 추가 바인딩 함수를 등록합니다.

사용 가능한 게임패드 키 목록은 다음
[carb 문서](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref/input-devices/gamepad.html)에 있습니다.

* **Parameters:**
  * **key** – 확인할 게임패드 버튼.
  * **func** – 해당 키가 눌렸을 때 호출할 함수. 콜백 함수는 인자를 받아서는 안 됩니다.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

게임패드 이벤트 상태의 결과를 제공합니다.

* **Returns:**
  선형 속도(x, y)와 각속도(z)를 포함하는 텐서.

#### *class* MotionControllerDataRowIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 2x7 배열의 행입니다.

#### *class* MotionControllerInputIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 입력 행의 인덱스입니다.

#### *class* TrackingTarget

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치 간 공유되는 표준 추적 대상입니다.

### *class* isaaclab.devices.Se3Gamepad

Bases: [`DeviceBase`](#isaaclab.devices.DeviceBase)

SE(3) 명령(델타 포즈 및 바이너리 명령(열림/닫힘))을 전송하는 게임패드 컨트롤러입니다.

이 클래스는 로봇 팔과 그립퍼를 위한 게임패드 컨트롤러를 제공하도록 설계되었습니다.
게임패드 인터페이스를 사용하여 이벤트를 수신하고 로봇의 작업 공간 명령으로 매핑합니다.

명령은 두 부분으로 구성됩니다:

* 델타 포즈: 미터 및 라디안 단위의 (x, y, z, roll, pitch, yaw)로 구성된 6D 벡터.
* 그립퍼: 그립퍼를 열거나 닫는 바이너리 명령.

스틱 및 버튼 바인딩:
: | 설명                     | 스틱/버튼 (+축)    | 스틱/버튼 (-축)    |
  |--------------------------|--------------------|--------------------|
  | 그립퍼 토글(열림/닫힘)   | X 버튼             | X 버튼             |
  | x축 이동                 | 왼쪽 스틱 위       | 왼쪽 스틱 아래     |
  | y축 이동                 | 왼쪽 스틱 왼쪽     | 왼쪽 스틱 오른쪽   |
  | z축 이동                 | 오른쪽 스틱 위     | 오른쪽 스틱 아래   |
  | x축 회전                 | D-패드 왼쪽        | D-패드 오른쪽      |
  | y축 회전                 | D-패드 아래        | D-패드 위          |
  | z축 회전                 | 오른쪽 스틱 왼쪽   | 오른쪽 스틱 오른쪽 |

#### SEE ALSO
게임패드 인터페이스의 공식 문서: [Carb Gamepad Interface](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref/input-devices/gamepad.html).

**메서드:**

| `__init__`(cfg)           | 게임패드 레이어를 초기화합니다.                 |
|---------------------------|-----------------------------------------------|
| `reset`()                 | 내부 상태를 재설정합니다.                      |
| `add_callback`(key, func) | 게임패드에 추가 바인딩 함수를 등록합니다.       |
| `advance`()               | 게임패드 이벤트 상태의 결과를 제공합니다.      |

**클래스:**

| `MotionControllerDataRowIndex`   | 모션 컨트롤러 2x7 배열의 행입니다.         |
|----------------------------------|--------------------------------------------------|
| `MotionControllerInputIndex`     | 모션 컨트롤러 입력 행의 인덱스입니다.      |
| `TrackingTarget`                 | 장치 간 공유되는 표준 추적 대상입니다. |

#### \_\_init_\_(cfg: Se3GamepadCfg)

게임패드 레이어를 초기화합니다.

* **Parameters:**
  **cfg** – 게임패드 설정을 위한 구성 객체입니다.

#### reset()

내부 상태를 재설정합니다.

#### add_callback(key: carb.input.GamepadInput, func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

게임패드에 추가 바인딩 함수를 등록합니다.

사용 가능한 게임패드 키 목록은 다음
[carb 문서](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref/input-devices/gamepad.html)에 있습니다.

* **Parameters:**
  * **key** – 확인할 게임패드 버튼.
  * **func** – 해당 키가 눌렸을 때 호출할 함수. 콜백 함수는 인자를 받아서는 안 됩니다.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

게임패드 이벤트 상태의 결과를 제공합니다.

* **Returns:**
  7개 요소로 구성된 텐서:
  : - 델타 포즈: 앞의 6개 요소가 [x, y, z, rx, ry, rz] (미터 및 라디안 단위).
    - 그립퍼 명령: 마지막 요소는 바이너리 값 (+1.0: 열림, -1.0: 닫힘).
* **Return type:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### *class* MotionControllerDataRowIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 2x7 배열의 행입니다.

#### *class* MotionControllerInputIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 입력 행의 인덱스입니다.

#### *class* TrackingTarget

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치 간 공유되는 표준 추적 대상입니다.

## 키보드

### *class* isaaclab.devices.Se2Keyboard

Bases: [`DeviceBase`](#isaaclab.devices.DeviceBase)

속도 명령으로서의 SE(2) 명령을 전송하는 키보드 컨트롤러입니다.

이 클래스는 이동 기반(예: 사족 로봇)을 위한 키보드 컨트롤러를 제공하도록 설계되었습니다.
Omniverse 키보드 인터페이스를 사용하여 키보드 이벤트를 수신하고 로봇의 작업 공간 명령으로 매핑합니다.

명령은 기본 선형 및 각도 속도로 구성됩니다: $(v_x, v_y, \omega_z)$.

키 바인딩:
: | 명령                 | 키 (+축)                     | 키 (-축)                    |
  |----------------------|------------------------------|-----------------------------|
  | x축 이동             | NumPad 8 / 위 화살표         | NumPad 2 / 아래 화살표      |
  | y축 이동             | NumPad 4 / 오른쪽 화살표     | NumPad 6 / 왼쪽 화살표      |
  | z축 회전             | NumPad 7 / Z                 | NumPad 9 / X                |

#### SEE ALSO
키보드 인터페이스의 공식 문서: [Carb Keyboard Interface](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref/input-devices/keyboard.html).

**메서드:**

| `__init__`(cfg)           | 키보드 레이어를 초기화합니다.                 |
|---------------------------|------------------------------------------------|
| `reset`()                 | 내부 상태를 재설정합니다.                      |
| `add_callback`(key, func) | 키보드에 추가 바인딩 함수를 등록합니다.        |
| `advance`()               | 키보드 이벤트 상태의 결과를 제공합니다.        |

**클래스:**

| `MotionControllerDataRowIndex`   | 모션 컨트롤러 2x7 배열의 행입니다.         |
|----------------------------------|--------------------------------------------------|
| `MotionControllerInputIndex`     | 모션 컨트롤러 입력 행의 인덱스입니다.      |
| `TrackingTarget`                 | 장치 간 공유되는 표준 추적 대상입니다. |

#### \_\_init_\_(cfg: Se2KeyboardCfg)

키보드 레이어를 초기화합니다.

* **Parameters:**
  * **v_x_sensitivity** – x축 방향의 선형 속도 크기 스케일링 값. 기본값은 0.8입니다.
  * **v_y_sensitivity** – y축 방향의 선형 속도 크기 스케일링 값. 기본값은 0.4입니다.
  * **omega_z_sensitivity** – z축 방향의 각속도 크기 스케일링 값. 기본값은 1.0입니다.

#### reset()

내부 상태를 재설정합니다.

#### add_callback(key: [str](https://docs.python.org/3/library/stdtypes.html#str), func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

키보드에 추가 바인딩 함수를 등록합니다.

사용 가능한 키 목록은 다음
[carb 문서](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref/input-devices/keyboard.html).

* **Parameters:**
  * **key** – 확인할 키보드 버튼.
  * **func** – 키가 눌렸을 때 호출할 함수. 콜백 함수는 인수를 받지 않아야 함.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

키보드 이벤트 상태의 결과를 제공.

* **반환값:**
  선형(x,y) 및 각속도(z)를 포함하는 텐서.

#### *class* MotionControllerDataRowIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 2x7 배열의 행.

#### *class* MotionControllerInputIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 입력 행의 인덱스.

#### *class* TrackingTarget

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치 간 공유되는 표준 트래킹 대상.

### *class* isaaclab.devices.Se3Keyboard

Bases: [`DeviceBase`](#isaaclab.devices.DeviceBase)

키보드 컨트롤러는 SE(3) 명령을 delta 포즈와 바이너리 명령(열기/닫기)으로 전송.

이 클래스는 그립퍼가 있는 로봇 암을 위한 키보드 컨트롤러를 제공하도록 설계되었습니다.
Omniverse 키보드 인터페이스를 사용하여 키보드 이벤트를 수신하고 이를 로봇의 작업 공간 명령으로 매핑합니다.

명령은 두 부분으로 구성됩니다:

* delta 포즈: 미터 및 라디안 단위의 (x, y, z, roll, pitch, yaw) 6D 벡터.
* 그립퍼: 그립퍼를 열거나 닫기 위한 바이너리 명령.

키 바인딩:
: | 설명                | 키 (+축)   | 키 (-축)   |
  |---------------------|------------|------------|
  | 그립퍼 토글 (열기/닫기) | K          |            |
  | x축 이동            | W          | S          |
  | y축 이동            | A          | D          |
  | z축 이동            | Q          | E          |
  | x축 회전            | Z          | X          |
  | y축 회전            | T          | G          |
  | z축 회전            | C          | V          |

#### 참고
공식 키보드 인터페이스 문서: [Carb Keyboard Interface](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref/input-devices/keyboard.html).

**메서드:**

| `__init__`(cfg)           | 키보드 레이어 초기화.                 |
|---------------------------|------------------------------------------------|
| `reset`()                 | 내부 상태 초기화.                           |
| `add_callback`(key, func) | 키보드에 추가 함수 바인딩.     |
| `advance`()               | 키보드 이벤트 상태의 결과 제공. |

**클래스:**

| `MotionControllerDataRowIndex`   | 모션 컨트롤러 2x7 배열의 행.         |
|----------------------------------|--------------------------------------------------|
| `MotionControllerInputIndex`     | 모션 컨트롤러 입력 행의 인덱스.      |
| `TrackingTarget`                 | 장치 간 공유되는 표준 트래킹 대상. |

#### \_\_init_\_(cfg: Se3KeyboardCfg)

키보드 레이어 초기화.

* **Parameters:**
  **cfg** – 키보드 설정을 위한 구성 객체.

#### reset()

내부 상태 초기화.

#### add_callback(key: [str](https://docs.python.org/3/library/stdtypes.html#str), func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

키보드에 추가 함수 바인딩.

사용 가능한 키 목록은 다음에서 확인할 수 있습니다:
[carb 문서](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref/input-devices/keyboard.html).

* **Parameters:**
  * **key** – 확인할 키보드 버튼.
  * **func** – 키가 눌렸을 때 호출할 함수. 콜백 함수는 인수를 받지 않아야 함.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

키보드 이벤트 상태의 결과를 제공.

* **반환값:**
  7개 요소로 구성된 텐서:
  : - delta 포즈: 처음 6개 요소는 [x, y, z, rx, ry, rz] (미터 및 라디안 단위).
    - 그립퍼 명령: 마지막 요소는 바이너리 값 (+1.0은 열기, -1.0은 닫기).
* **반환 타입:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### *class* MotionControllerDataRowIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 2x7 배열의 행.

#### *class* MotionControllerInputIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 입력 행의 인덱스.

#### *class* TrackingTarget

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치 간 공유되는 표준 트래킹 대상.

## 스페이스 마우스

### *class* isaaclab.devices.Se2SpaceMouse

Bases: [`DeviceBase`](#isaaclab.devices.DeviceBase)

스페이스 마우스 컨트롤러는 SE(2) 명령을 delta 포즈로 전송.

이 클래스는 이동 기반을 위한 명령을 제공하기 위해 스페이스 마우스 컨트롤러를 구현합니다.
[HID-API](https://github.com/libusb/hidapi)를 사용하여 USD 및 블루투스 HID 클래스 장치와 다중 플랫폼에서 인터페이스합니다.

명령은 기본 선형 및 각속도로 구성됩니다: $(v_x, v_y, \omega_z)$.

#### 참고
인터페이스는 컴퓨터에 연결된 첫 번째 지원되는 장치를 찾고 사용합니다.

현재 다음과 같은 장치에서 테스트됨:

- SpaceMouse Compact: [https://3dconnexion.com/de/product/spacemouse-compact/](https://3dconnexion.com/de/product/spacemouse-compact/)

**메서드:**

| `__init__`(cfg)           | 스페이스 마우스 레이어 초기화.                 |
|---------------------------|--------------------------------------------------|
| `reset`()                 | 내부 상태 초기화.                             |
| `add_callback`(key, func) | 스페이스 마우스에 추가 함수 바인딩.     |
| `advance`()               | 스페이스 마우스 이벤트 상태의 결과 제공. |

**클래스:**

| `MotionControllerDataRowIndex`   | 모션 컨트롤러 2x7 배열의 행.         |
|----------------------------------|--------------------------------------------------|
| `MotionControllerInputIndex`     | 모션 컨트롤러 입력 행의 인덱스.      |
| `TrackingTarget`                 | 장치 간 공유되는 표준 트래킹 대상. |

#### \_\_init_\_(cfg: Se2SpaceMouseCfg)

스페이스 마우스 레이어 초기화.

* **Parameters:**
  **cfg** – 스페이스 마우스 장치를 위한 구성.

#### reset()

내부 상태 초기화.

#### add_callback(key: [str](https://docs.python.org/3/library/stdtypes.html#str), func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

스페이스 마우스에 추가 함수 바인딩.

* **Parameters:**
  * **key** – 확인할 키보드 버튼.
  * **func** – 키가 눌렸을 때 호출할 함수. 콜백 함수는 인수를 받지 않아야 함.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

스페이스 마우스 이벤트 상태의 결과를 제공.

* **반환값:**
  선형(x,y) 및 각속도(z)를 포함하는 3차원 텐서.

#### *class* MotionControllerDataRowIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 2x7 배열의 행.

#### *class* MotionControllerInputIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 입력 행의 인덱스.

#### *class* TrackingTarget

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치 간 공유되는 표준 트래킹 대상.

### *class* isaaclab.devices.Se3SpaceMouse

Bases: [`DeviceBase`](#isaaclab.devices.DeviceBase)

스페이스 마우스 컨트롤러는 SE(3) 명령을 delta 포즈로 전송.

이 클래스는 그립퍼가 있는 로봇 암을 위한 명령을 제공하기 위해 스페이스 마우스 컨트롤러를 구현합니다.
[HID-API](https://github.com/libusb/hidapi)를 사용하여 USD 및 블루투스 HID 클래스 장치와 다중 플랫폼에서 인터페이스합니다 [1].

명령은 두 부분으로 구성됩니다:

* delta 포즈: 미터 및 라디안 단위의 (x, y, z, roll, pitch, yaw) 6D 벡터.
* 그립퍼: 그립퍼를 열거나 닫기 위한 바이너리 명령.

#### 참고
인터페이스는 컴퓨터에 연결된 첫 번째 지원되는 장치를 찾고 사용합니다.

현재 다음과 같은 장치에서 테스트됨:

- SpaceMouse Compact: [https://3dconnexion.com/de/product/spacemouse-compact/](https://3dconnexion.com/de/product/spacemouse-compact/)

**메서드:**

| `__init__`(cfg)           | 스페이스 마우스 레이어 초기화.                |
|---------------------------|--------------------------------------------------|
| `reset`()                 | 내부 상태 초기화.                             |
| `add_callback`(key, func) | 스페이스 마우스에 추가 함수 바인딩.     |
| `advance`()               | 스페이스 마우스 이벤트 상태의 결과 제공. |

**클래스:**

| `MotionControllerDataRowIndex`   | 모션 컨트롤러 2x7 배열의 행.         |
|----------------------------------|--------------------------------------------------|
| `MotionControllerInputIndex`     | 모션 컨트롤러 입력 행의 인덱스.      |
| `TrackingTarget`                 | 장치 간 공유되는 표준 트래킹 대상. |

#### \_\_init_\_(cfg: Se3SpaceMouseCfg)

스페이스 마우스 레이어 초기화.

* **Parameters:**
  **cfg** – 스페이스 마우스 설정을 위한 구성 객체.

#### reset()

내부 상태 초기화.

#### add_callback(key: [str](https://docs.python.org/3/library/stdtypes.html#str), func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

스페이스 마우스에 추가 함수 바인딩.

* **Parameters:**
  * **key** – 확인할 키보드 버튼.
  * **func** – 키가 눌렸을 때 호출할 함수. 콜백 함수는 인수를 받지 않아야 함.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

스페이스마우스 이벤트 상태에서 결과를 제공합니다.

* **반환값:**
  다음을 포함하는 7개 요소 텐서:
  : - 델타 포즈: 미터와 라디안 단위로 [x, y, z, rx, ry, rz] 형태의 첫 6개 요소
    - 그리퍼 명령: 마지막 요소는 바이너리 값(+1.0은 열기, -1.0은 닫기)
* **반환 유형:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### *class* MotionControllerDataRowIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 2x7 배열의 행.

#### *class* MotionControllerInputIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 입력 행의 인덱스.

#### *class* TrackingTarget

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치 간에 공유되는 표준 추적 대상.

## Haply

### *class* isaaclab.devices.HaplyDevice

Bases: [`DeviceBase`](#isaaclab.devices.DeviceBase)

힘 피드백을 사용한 SE(3) 명령 전송을 위한 Haply 디바이스 컨트롤러입니다.

이 클래스는 Haply 로봇 디바이스(Inverse3 + VerseGrip)와의 인터페이스를 제공하여 텔레오퍼레이션을 지원합니다. WebSocket을 통해 통신하며 다음을 지원합니다:

- Inverse3 디바이스의 위치 추적
- VerseGrip 디바이스의 방향 및 버튼 입력
- Inverse3 디바이스의 방향성 힘 피드백
- 구성 가능한 속도로 실시간 데이터 스트리밍

이 디바이스는 다음과 같은 원시 데이터를 제공합니다:

* 위치: Inverse3에서 미터 단위로 제공되는 3D 위치 (x, y, z)
* 방향: VerseGrip에서 제공되는 쿼터니언 (x, y, z, w)
* 버튼: VerseGrip의 세 개의 버튼(a, b, c)과 상태(눌림/안 눌림)

참고: 버튼 로직(예: 그리퍼 제어, 리셋, 모드 전환)은 advance()에서 제공하는 원시 버튼 상태를 사용하여 애플리케이션 레이어에서 구현해야 합니다.

#### NOTE
Haply SDK가 실행 중이고 WebSocket을 통해 접근 가능한 것이 필요합니다.
의존성 설치: pip install websockets

**메서드:**

| `__init__`(cfg[, retargeters])   | Haply 디바이스 인터페이스를 초기화합니다.             |
|----------------------------------|----------------------------------------------------|
| `reset`()                        | 디바이스 내부 상태를 재설정합니다.                   |
| `add_callback`(key, func)        | 버튼 이벤트에 바인딩할 추가 함수를 추가합니다. |
| `advance`()                      | Haply 디바이스 상태에서 결과를 제공합니다.       |
| `push_force`(forces, position)   | Haply Inverse3 디바이스로 힘 벡터를 푸시합니다.        |

**클래스:**

| `MotionControllerDataRowIndex`   | 모션 컨트롤러 2x7 배열의 행.         |
|----------------------------------|--------------------------------------------------|
| `MotionControllerInputIndex`     | 모션 컨트롤러 입력 행의 인덱스.      |
| `TrackingTarget`                 | 장치 간에 공유되는 표준 추적 대상. |

#### \_\_init_\_(cfg: HaplyDeviceCfg, retargeters: [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.devices.retargeter_base.RetargeterBase](#isaaclab.devices.RetargeterBase)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

Haply 디바이스 인터페이스를 초기화합니다.

* **매개변수:**
  * **cfg** – Haply 디바이스 설정을 위한 구성 객체.
  * **retargeters** – 디바이스 데이터를 로봇 명령으로 변환하는 리타겟팅 구성 요소들의 선택적 목록. None 또는 비어 있는 경우, 디바이스는 네이티브 데이터 형식으로 출력됩니다.
* **예외:**
  * [**ImportError**](https://docs.python.org/3/library/exceptions.html#ImportError) – websockets 모듈이 설치되지 않은 경우.
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – Haply 디바이스에 대한 연결이 실패한 경우.

#### reset()

디바이스 내부 상태를 재설정합니다.

#### add_callback(key: [str](https://docs.python.org/3/library/stdtypes.html#str), func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

버튼 이벤트에 바인딩할 추가 함수를 추가합니다.

* **매개변수:**
  * **key** – 비교할 버튼. 유효한 값은 "a", "b", "c".
  * **func** – 버튼이 눌렸을 때 호출할 함수. 콜백 함수는 인수를 받지 않아야 합니다.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

Haply 디바이스 상태에서 결과를 제공합니다.

* **반환값:**
  원시 디바이스 데이터를 포함하는 텐서:
  : - 10개 요소: [x, y, z, qx, qy, qz, qw, button_a, button_b, button_c]
      : 여기서 (x, y, z)는 위치, (qx, qy, qz, qw)는 쿼터니언 방향,
        버튼은 눌림 상태일 때 1.0, 안 눌림 상태일 때 0.0
* **반환 유형:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### push_force(forces: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), position: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [None](https://docs.python.org/3/library/constants.html#None)

Haply Inverse3 디바이스로 힘 벡터를 푸시합니다.

DeviceBase.push_force()를 재정의하여 Haply Inverse3에 힘 피드백을 제공합니다.
힘은 안전성을 위해 [-limit_force, limit_force] 범위로 클리핑됩니다.

* **매개변수:**
  * **forces** – [fx, fy, fz] 형태의 힘 텐서, 모양은 (N, 3).
  * **position** – 사용할 힘을 지정하는 인덱스 텐서, 모양은 (N).

#### *class* MotionControllerDataRowIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 2x7 배열의 행.

#### *class* MotionControllerInputIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 입력 행의 인덱스.

#### *class* TrackingTarget

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치 간에 공유되는 표준 추적 대상.

## OpenXR

### *class* isaaclab.devices.OpenXRDevice

Bases: [`DeviceBase`](#isaaclab.devices.DeviceBase)

텔레오퍼레이션 및 상호작용을 위한 OpenXR 기반 디바이스입니다.

이 디바이스는 OpenXR을 사용하여 손 관절을 추적하고 다음과 같이 제공합니다:

1. 리타겟터 없이 사용 시 추적 데이터의 사전
2. 리타겟터가 제공될 경우 로봇 제어를 위한 리타겟된 명령

원시 데이터 형식(_get_raw_data 출력):

* HAND_LEFT, HAND_RIGHT, HEAD과 일치하는 키를 가진 사전
* 각 손 추적 항목은 관절 자세의 사전 포함
* 각 관절 자세는 미터와 쿼터니언 단위의 7D 벡터 (x, y, z, qw, qx, qy, qz)
* 관절 이름은 isaaclab.devices.openxr.common의 HAND_JOINT_NAMES에서 정의
* 지원되는 관절에는 palm, wrist 및 엄지, 검지, 중지, 약지, 소지의 관절 포함

텔레오퍼레이션 명령:
디바이스는 add_callback()을 통해 구독할 수 있는 여러 텔레오퍼레이션 명령에 응답합니다:

* “START”: 손 추적 데이터 흐름 재개
* “STOP”: 손 추적 데이터 흐름 일시 정지
* “RESET”: 추적 재설정 및 시뮬레이션 리셋 신호

디바이스는 TrackingTarget 열거형 값에 기반하여 왼쪽 손, 오른쪽 손, 머리 위치 또는 이들의 조합을 추적합니다.
리타겟터가 제공될 경우, 원시 추적 데이터는 텔레오퍼레이션에 적합한 로봇 제어 명령으로 변환됩니다.

**메서드:**

| `__init__`(cfg[, retargeters])   | OpenXR 디바이스를 초기화합니다.                             |
|----------------------------------|-----------------------------------------------------------|
| `reset`()                        | 내부 상태를 재설정합니다.                                      |
| `add_callback`(key, func)        | 클라이언트 메시지에 바인딩할 추가 함수를 추가합니다.      |
| `advance`()                      | 현재 디바이스 상태를 처리하고 제어 명령을 반환합니다. |

**클래스:**

| `MotionControllerDataRowIndex`   | 모션 컨트롤러 2x7 배열의 행.         |
|----------------------------------|--------------------------------------------------|
| `MotionControllerInputIndex`     | 모션 컨트롤러 입력 행의 인덱스.      |
| `TrackingTarget`                 | 장치 간에 공유되는 표준 추적 대상. |

#### \_\_init_\_(cfg: OpenXRDeviceCfg, retargeters: [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.devices.retargeter_base.RetargeterBase](#isaaclab.devices.RetargeterBase)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

OpenXR 디바이스를 초기화합니다.

* **매개변수:**
  * **cfg** – OpenXR 설정을 위한 구성 객체.
  * **retargeters** – 원시 추적 데이터를 변환하기 위한 리타겟터 인스턴스 목록.

#### reset()

내부 상태를 재설정합니다.

#### add_callback(key: [str](https://docs.python.org/3/library/stdtypes.html#str), func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

클라이언트 메시지에 바인딩할 추가 함수를 추가합니다.

* **매개변수:**
  * **key** – 바인딩할 메시지 유형. 유효한 값은 “START”, “STOP”, 그리고 “RESET”.
  * **func** – 메시지가 수신될 때 호출할 함수. 콜백 함수는 인수를 받지 않아야 합니다.

#### *class* MotionControllerDataRowIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 2x7 배열의 행.

#### *class* MotionControllerInputIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션 컨트롤러 입력 행의 인덱스.

#### *class* TrackingTarget

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치 간에 공유되는 표준 추적 대상.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

현재 디바이스 상태를 처리하고 제어 명령을 반환합니다.

이 메서드는 디바이스에서 원시 데이터를 검색하고 선택적으로 리타겟팅을 적용하여 로봇 명령으로 변환합니다.

파생 클래스는 다음 중 하나를 수행할 수 있습니다:
1. \_get_raw_data()를 오버라이드하고 이 기본 구현을 사용하거나,
2. 이 메서드를 완전히 오버라이드하여 맞춤형 명령 처리를 수행합니다.

* **반환값:**
  리타겟터가 구성되지 않은 경우, 디바이스의 네이티브 형식으로 원시 디바이스 데이터를 반환합니다.
  retargeter가 구성된 경우, 모든 retargeter의 출력값을 연결한 torch.Tensor을 반환합니다.

## Manus + Vive

### *class* isaaclab.devices.ManusVive

Bases: [`DeviceBase`](#isaaclab.devices.DeviceBase)

Manus 장갑과 Vive 트래커를 이용한 텔레오퍼레이션 및 상호작용을 위한 디바이스입니다.

이 디바이스는 Manus 장갑과 Vive 트래커를 사용해 손 관절을 추적하고 다음과 같은 형식으로 제공합니다:

1. 추적 데이터 딕셔너리 (retargeter를 사용하지 않을 때)
2. 로봇 제어를 위한 리타게팅된 명령 (retargeter가 제공될 때)

사용자는 Manus SDK를 설치하고 {path_to_manus_sdk}/manus_sdk/lib을 LD_LIBRARY_PATH에 추가해야 합니다.
데이터는 isaaclab.devices.openxr.manus_vive_utils의 ManusViveIntegration에서 획득되며, 다음을 포함합니다:

* AVP 손목 자세에서 보정된 scene 프레임 기준 Vive 트래커 포즈
* Vive 손목 관절과 Manus 손 관절에서 계산된 손 관절 (손목 기준 상대값)
* Vive 트래커는 자동으로 왼쪽 및 오른쪽 손목 관절에 매핑됩니다.

원시 데이터 형식 (`_get_raw_data` 출력): `OpenXRDevice`와 일관됩니다.
관절 이름은 isaaclab.devices.openxr.manus_vive_utils의 HAND_JOINT_MAP에서 정의됩니다.

Teleop 명령: `OpenXRDevice`와 일관됩니다.

이 디바이스는 TrackingTarget 열거형 값에 따라 왼쪽 손, 오른쪽 손, 머리 위치 또는 이들의 조합을 추적합니다.
retargeter가 제공될 때, 원시 추적 데이터는 텔레오퍼레이션에 적합한 로봇 제어 명령으로 변환됩니다.

**메서드:**

| `__init__`(cfg[, retargeters])   | Manus+Vive 디바이스를 초기화합니다.                         |
|----------------------------------|-----------------------------------------------------------|
| `reset`()                        | 캐시된 관절 및 머리 자세를 리셋합니다.                        |
| `add_callback`(key, func)        | 특정 키에 대한 콜백을 등록합니다.                      |
| `advance`()                      | 현재 디바이스 상태를 처리하고 제어 명령을 반환합니다. |

**클래스:**

| `MotionControllerDataRowIndex`   | 모션컨트롤러 2x7 배열의 행.         |
|----------------------------------|--------------------------------------------------|
| `MotionControllerInputIndex`     | 모션컨트롤러 입력 행의 인덱스.      |
| `TrackingTarget`                 | 디바이스 간 공유되는 표준 추적 대상. |

#### \_\_init_\_(cfg: ManusViveCfg, retargeters: [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.devices.retargeter_base.RetargeterBase](#isaaclab.devices.RetargeterBase)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

Manus+Vive 디바이스를 초기화합니다.

* **매개변수:**
  * **cfg** – Manus+Vive 설정을 위한 구성 객체.
  * **retargeters** – 원시 추적 데이터를 변환하기 위해 사용할 리타게터 인스턴스 목록.

#### reset()

캐시된 관절 및 머리 자세를 리셋합니다.

#### add_callback(key: [str](https://docs.python.org/3/library/stdtypes.html#str), func: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable))

특정 키에 대한 콜백을 등록합니다.

* **매개변수:**
  * **key** – 바인딩할 메시지 키 (‘START’, ‘STOP’, ‘RESET’).
  * **func** – 메시지 키를 수신했을 때 호출할 함수.

#### *class* MotionControllerDataRowIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션컨트롤러 2x7 배열의 행.

#### *class* MotionControllerInputIndex

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

모션컨트롤러 입력 행의 인덱스.

#### *class* TrackingTarget

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

디바이스 간 공유되는 표준 추적 대상.

#### advance() → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

현재 디바이스 상태를 처리하고 제어 명령을 반환합니다.

이 메서드는 디바이스에서 원시 데이터를 가져오고, 선택적으로 리타게팅을 적용하여 로봇 명령으로 변환합니다.

파생 클래스는 다음 중 하나를 수행할 수 있습니다:
1. `_get_raw_data()`를 오버라이드하고 이 기본 구현을 사용하거나,
2. 이 메서드를 완전히 오버라이드하여 맞춤 명령 처리를 수행합니다.

* **반환값:**
  리타게터가 구성되지 않은 경우, 원시 디바이스 데이터를 네이티브 형식으로 반환합니다.
  리타게터가 구성된 경우, 모든 리타게터의 출력값을 연결한 torch.Tensor을 반환합니다.

## 리타게터

### *class* isaaclab.devices.openxr.retargeters.GripperRetargeter

Bases: [`RetargeterBase`](#isaaclab.devices.RetargeterBase)

손 추적 데이터를 기반으로 그리퍼 제어를 위한 리타게터입니다.

이 리타게터는 엄지 손가락과 검지 손가락 끝 사이의 거리를 분석하여 그리퍼를 열거나 닫아야 하는지 판단합니다.
손가락 거리가 임계값 근처일 때 빠른 토글을 방지하기 위해 히스테리시스를 포함합니다.

특징:
- 엄지 손가락과 검지 손가락 거리 추적
- 안정적인 그리퍼 제어를 위한 히스테리시스 구현
- boolean 명령 출력 (True = 그리퍼 닫기, False = 그리퍼 열기)

**메서드:**

| `__init__`(cfg)      | 리타게터를 초기화합니다.                                     |
|----------------------|----------------------------------------------------------------|
| `retarget`(data)     | 손 관절 자세를 그리퍼 명령으로 변환합니다.                   |
| `get_requirements`() | 이 리타게터에 필요한 데이터 기능 목록을 반환합니다. |

**클래스:**

| `Requirement`   | 디바이스의 원시 데이터 피드에서 리타게터가 요구할 수 있는 기능.   |
|-----------------|--------------------------------------------------------------------|

#### \_\_init_\_(cfg: GripperRetargeterCfg)

리타게터를 초기화합니다.

* **매개변수:**
  **cfg** – 리타게터를 위한 구성

#### retarget(data: [dict](https://docs.python.org/3/library/stdtypes.html#dict)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

손 관절 자세를 그리퍼 명령으로 변환합니다.

* **매개변수:**
  **data** – 추적 대상을 매핑하는 관절 데이터 딕셔너리.
  관절 이름은 isaaclab.devices.openxr.common.HAND_JOINT_NAMES에 정의됩니다.
* **반환값:**
  True = 그리퍼 닫기, False = 그리퍼 열기를 나타내는 단일 불리언 값을 포함하는 텐서
* **반환 타입:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### get_requirements() → [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.devices.retargeter_base.RetargeterBase.Requirement](#isaaclab.devices.RetargeterBase.Requirement)]

이 리타게터에 필요한 데이터 기능 목록을 반환합니다.

후방 호환성을 위해 기본적으로 모든 사용 가능한 기능을 요청합니다.
구현에서는 필요한 기능만 요청하도록 오버라이드해야 합니다.

#### *class* Requirement

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

디바이스의 원시 데이터 피드에서 리타게터가 요구할 수 있는 기능.

### *class* isaaclab.devices.openxr.retargeters.Se3AbsRetargeter

Bases: [`RetargeterBase`](#isaaclab.devices.RetargeterBase)

절대 위치 기반으로 OpenXR 손 추적 데이터를 엔드이펙터 명령으로 리타게팅합니다.

이 리타게터는 손 관절 자세를 로봇 엔드이펙터의 위치와 방향에 직접 매핑하며, 상대 이동을 사용하지 않습니다. 다음 중 하나를 수행할 수 있습니다:
- 손목 위치와 방향 사용
- 엄지 손가락과 검지 손가락 사이의 중점 (핀치 위치) 사용

특징:
- X/Y 회전을 0으로 강제하여 Z축 회전만 유지하는 선택적 제약
- 대상 엔드이펙터 포즈의 선택적 시각화

**메서드:**

| `__init__`(cfg)      | 리타게터를 초기화합니다.                                     |
|----------------------|----------------------------------------------------------------|
| `retarget`(data)     | 손 관절 자세를 로봇 엔드이펙터 명령으로 변환합니다.        |
| `get_requirements`() | 이 리타게터에 필요한 데이터 기능 목록을 반환합니다. |

**클래스:**

| `Requirement`   | 디바이스의 원시 데이터 피드에서 리타게터가 요구할 수 있는 기능.   |
|-----------------|--------------------------------------------------------------------|

#### \_\_init_\_(cfg: Se3AbsRetargeterCfg)

리타게터를 초기화합니다.

* **매개변수:**
  * **bound_hand** – 추적할 손 (DeviceBase.TrackingTarget.HAND_LEFT 또는 DeviceBase.TrackingTarget.HAND_RIGHT)
  * **zero_out_xy_rotation** – True이면 x 및 y 축 주위 회전을 0으로 만듭니다
  * **use_wrist_rotation** – True이면 손목 회전 대신 손가락 평균을 사용하지 않습니다
  * **use_wrist_position** – True이면 손목 위치 대신 핀치 위치를 사용하지 않습니다
  * **enable_visualization** – True이면 장면에서 대상 포즈를 시각화합니다
  * **device** – 반환된 텐서를 배치할 장치 (‘cpu’ 또는 ‘cuda’)

#### retarget(data: [dict](https://docs.python.org/3/library/stdtypes.html#dict)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

손 관절 자세를 로봇 엔드이펙터 명령으로 변환합니다.

* **매개변수:**
  **data** – 추적 대상을 매핑하는 관절 데이터 딕셔너리.
  관절 이름은 isaaclab.devices.openxr.common.HAND_JOINT_NAMES에 정의됩니다.
* **반환값:**
  로봇 엔드이펙터를 위한 위치(xyz)와 방향(쿼터니언)을 포함한 7차원 텐서
* **반환 타입:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### get_requirements() → [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.devices.retargeter_base.RetargeterBase.Requirement](#isaaclab.devices.RetargeterBase.Requirement)]

이 리타게터에 필요한 데이터 기능 목록을 반환합니다.

후방 호환성을 위해 기본적으로 모든 사용 가능한 기능을 요청합니다.
구현에서는 필요한 기능만 요청하도록 오버라이드해야 합니다.

#### *class* Requirement

Bases: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

디바이스의 원시 데이터 피드에서 리타게터가 요구할 수 있는 기능.

### *class* isaaclab.devices.openxr.retargeters.Se3RelRetargeter

Bases: [`RetargeterBase`](#isaaclab.devices.RetargeterBase)

OpenXR 손 추적 데이터를 상대 위치 기반으로 엔드-이펙터 명령으로 재대상합니다.

이 재대상자는 연속된 손 조인트 포즈 사이의 delta 포즈를 계산하여 증가형 로봇 이동을 생성합니다.
다음 중 하나를 사용할 수 있습니다:
- 손목 위치 및 방향 사용
- 엄지와 검지 사이의 중점(집게 위치) 사용

**기능:**
- X/Y 회전을 0으로 만드는 선택적 제약 조건(Z축 회전만 유지)
- 조정 가능한 매개변수를 사용한 모션 스무딩
- 대상 엔드-이펙터 포즈의 선택적 시각화

**Methods:**

| `__init__`(cfg)      | 상대 운동 재대상자 초기화.                     |
|----------------------|----------------------------------------------------------------|
| `retarget`(data)     | 손 조인트 포즈를 로봇 엔드-이펙터 명령으로 변환.        |
| `get_requirements`() | 이 재대상자가 요구하는 데이터 기능 목록 반환. |

**Classes:**

| `Requirement`   | 장치의 원시 데이터 피드에서 요구할 수 있는 기능.   |
|-----------------|--------------------------------------------------------------------|

#### \_\_init_\_(cfg: Se3RelRetargeterCfg)

상대 운동 재대상자를 초기화합니다.

* **매개변수:**
  * **bound_hand** – 추적할 손 (DeviceBase.TrackingTarget.HAND_LEFT 또는 DeviceBase.TrackingTarget.HAND_RIGHT)
  * **zero_out_xy_rotation** – True이면 x 및 y 축 주변 회전을 무시하고 z축 회전만 허용
  * **use_wrist_rotation** – True이면 손목 회전을 제어에 사용하고 손가락 방향 평균 대신 사용
  * **use_wrist_position** – True이면 집게 위치(손가락 중간 지점) 대신 손목 위치 사용
  * **delta_pos_scale_factor** – 위치 변화 증폭 계수 (값이 클수록 로봇 이동량 증가)
  * **delta_rot_scale_factor** – 회전 변화 증폭 계수 (값이 클수록 로봇 회전량 증가)
  * **alpha_pos** – 위치 스무딩 매개변수 (0-1); 값이 클수록 입력에 더 가깝게 추적하고, 값이 작을수록 더 많이 스무딩
  * **alpha_rot** – 회전 스무딩 매개변수 (0-1); 값이 클수록 입력에 더 가깝게 추적하고, 값이 작을수록 더 많이 스무딩
  * **enable_visualization** – True이면 목표 엔드-이펙터 포즈를 나타내는 시각적 마커 표시
  * **device** – 반환된 텐서를 배치할 장치 (‘cpu’ 또는 ‘cuda’)

#### retarget(data: [dict](https://docs.python.org/3/library/stdtypes.html#dict)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

손 조인트 포즈를 로봇 엔드-이펙터 명령으로 변환합니다.

* **매개변수:**
  **data** – 추적 대상을 관절 데이터 사전으로 매핑하는 사전.
  관절 이름은 isaaclab.devices.openxr.common.HAND_JOINT_NAMES 에 정의됨
* **반환값:**
  위치(xyz)와 회전 벡터(rx, ry, rz)로 구성된 6D 텐서
  로봇 엔드-이펙터에 대한 명령
* **반환 타입:**
  [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

#### get_requirements() → [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.devices.retargeter_base.RetargeterBase.Requirement](#isaaclab.devices.RetargeterBase.Requirement)]

이 재대상자가 요구하는 데이터 기능 목록을 반환합니다.

기본값은 모든 사용 가능한 기능을 요청하여 하위 호환성을 유지합니다.
구현은 필요한 것만 요청하도록 오버라이드해야 합니다.

#### *class* Requirement

기반: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치의 원시 데이터 피드에서 요구할 수 있는 기능.

### *class* isaaclab.devices.openxr.retargeters.GR1T2Retargeter

기반: [`RetargeterBase`](#isaaclab.devices.RetargeterBase)

OpenXR 손 추적 데이터를 GR1T2 손 엔드-이펙터 명령으로 재대상합니다.

이 재대상자는 OpenXR에서 손 추적 데이터를 가져와 GR1T2 로봇의 손 관절 명령으로 매핑합니다.
왼손과 오른손 모두를 처리하며, OpenXR 형식의 손 관절 포즈를 GR1T2 로봇 손의 관절 각도로 변환합니다.

**Methods:**

| `__init__`(cfg)      | GR1T2 손 재대상자 초기화.                          |
|----------------------|----------------------------------------------------------------|
| `retarget`(data)     | 손 조인트 포즈를 로봇 엔드-이펙터 명령으로 변환.       |
| `get_requirements`() | 이 재대상자가 요구하는 데이터 기능 목록 반환. |

**Classes:**

| `Requirement`   | 장치의 원시 데이터 피드에서 요구할 수 있는 기능.   |
|-----------------|--------------------------------------------------------------------|

#### \_\_init_\_(cfg: GR1T2RetargeterCfg)

GR1T2 손 재대상자를 초기화합니다.

* **매개변수:**
  * **enable_visualization** – True이면 추적된 손 관절 시각화
  * **num_open_xr_hand_joints** – OpenXR이 추적하는 관절 수
  * **device** – 계산을 위한 PyTorch 장치
  * **hand_joint_names** – 로봇 손 관절 이름 목록

#### retarget(data: [dict](https://docs.python.org/3/library/stdtypes.html#dict)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

손 조인트 포즈를 로봇 엔드-이펙터 명령으로 변환합니다.

* **매개변수:**
  **data** – 추적 대상을 관절 데이터 사전으로 매핑하는 사전.
* **반환값:**
  왼쪽 손목 포즈
  USD 프레임에서의 오른쪽 손목 포즈
  재대상된 손 관절 각도
* **반환 타입:**
  포함하는 튜플

#### get_requirements() → [list](https://docs.python.org/3/library/stdtypes.html#list)[[isaaclab.devices.retargeter_base.RetargeterBase.Requirement](#isaaclab.devices.RetargeterBase.Requirement)]

이 재대상자가 요구하는 데이터 기능 목록을 반환합니다.

기본값은 모든 사용 가능한 기능을 요청하여 하위 호환성을 유지합니다.
구현은 필요한 것만 요청하도록 오버라이드해야 합니다.

#### *class* Requirement

기반: [`Enum`](https://docs.python.org/3/library/enum.html#enum.Enum)

장치의 원시 데이터 피드에서 요구할 수 있는 기능.
