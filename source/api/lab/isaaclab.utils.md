# isaaclab.utils

일반적인 작업과 도우미 함수를 위한 유틸리티가 포함된 하위 패키지입니다.

### 하위 모듈

| [`io`](#module-isaaclab.utils.io)                       | 파일 I/O 작업을 위한 하위 모듈입니다.                                               |
|---------------------------------------------------------|-----------------------------------------------------------------------------------|
| [`array`](#module-isaaclab.utils.array)                 | 다양한 배열 백엔드 작업을 위한 유틸리티가 포함된 하위 모듈입니다.        |
| [`assets`](#module-isaaclab.utils.assets)               | 에셋과 리소스가 저장된 호스트 서버를 정의하는 하위 모듈입니다.    |
| [`buffers`](#module-isaaclab.utils.buffers)             | 다양한 버퍼를 포함하는 하위 모듈입니다.                                          |
| [`datasets`](#module-isaaclab.utils.datasets)           | 데이터셋 클래스와 메서드를 위한 하위 모듈입니다.                                       |
| [`dict`](#module-isaaclab.utils.dict)                   | 딕셔너리 작업을 위한 유틸리티가 포함된 하위 모듈입니다.                           |
| [`interpolation`](#module-isaaclab.utils.interpolation) | 다양한 보간 방법을 다루는 하위 모듈입니다.                                    |
| [`logger`](#module-isaaclab.utils.logger)               | 로깅 유틸리티가 포함된 하위 모듈입니다.                                                |
| [`math`](#module-isaaclab.utils.math)                   | 다양한 수학 연산을 위한 유틸리티가 포함된 하위 모듈입니다.                      |
| [`mesh`](#module-isaaclab.utils.mesh)                   | 메시를 작업하기 위한 유틸리티 함수입니다.                                        |
| [`modifiers`](#module-isaaclab.utils.modifiers)         | 다양한 수정자 구현을 포함하는 하위 모듈입니다.                        |
| [`noise`](#module-isaaclab.utils.noise)                 | 다양한 노이즈 모델 구현을 포함하는 하위 모듈입니다.                     |
| [`seed`](#module-isaaclab.utils.seed)                   |                                                                                   |
| [`sensors`](#module-isaaclab.utils.sensors)             |                                                                                   |
| [`string`](#module-isaaclab.utils.string)               | 문자열 변환과 정규 표현식을 위한 유틸리티가 포함된 하위 모듈입니다. |
| [`timer`](#module-isaaclab.utils.timer)                 | 성능 측정에 사용할 수 있는 타이머 클래스를 포함하는 하위 모듈입니다.       |
| [`types`](#module-isaaclab.utils.types)                 | 다양한 데이터 타입을 다루는 하위 모듈입니다.                                              |
| [`version`](#module-isaaclab.utils.version)             | 버전 관리 유틸리티 함수입니다.                                                 |
| [`warp`](#module-isaaclab.utils.warp)                   | warp 기반 작업을 포함하는 하위 모듈입니다.                                   |

### 함수

| [`configclass`](#module-isaaclab.utils.configclass)(cls, \*\*kwargs)   | 추가 검사와 유틸리티를 제공하기 위해 dataclass 기능을 감싸는 래퍼입니다.   |
|------------------------------------------------------------------------|-----------------------------------------------------------------------------|

## 구성 클래스

Python 3.7 이상부터의 `dataclasses` 모듈을 감싸는 래퍼를 제공하는 하위 모듈입니다.

**함수:**

| [`configclass`](#isaaclab.utils.configclass.configclass)(cls, \*\*kwargs)   | 추가 검사와 유틸리티를 제공하기 위해 dataclass 기능을 감싸는 래퍼입니다.   |
|-----------------------------------------------------------------------------|-----------------------------------------------------------------------------|

### isaaclab.utils.configclass.configclass(cls, \*\*kwargs)

추가 검사와 유틸리티를 제공하기 위해 dataclass 기능을 감싸는 래퍼입니다.

Python 3.7부터 표준 dataclass는 구성 사용 사례에 범용적이지 못하게 만드는 두 가지 주요 문제가 있습니다. 이들에는 다음이 포함됩니다:

1. 모든 멤버에 타입 주석이 필요합니다.
2. 가변 변수를 다시 초기화하려면 `field(default_factory=...)()`를 명시적으로 사용해야 합니다.

이 함수는 위의 두 가지 문제를 해결하기 위해 Python의 [dataclass](https://docs.python.org/3/library/dataclasses.html) 유틸리티를 감싸는 데코레이터를 제공합니다. 또한 딕셔너리 <-> 클래스 변환과 클래스 인스턴스 복사을 쉽게 할 수 있는 추가 헬퍼 함수도 제공합니다.

사용법:

```python
from dataclasses import MISSING

from isaaclab.utils.configclass import configclass


@configclass
class ViewerCfg:
    eye: list = [7.5, 7.5, 7.5]  # 목적상 필드 누락
    lookat: list = field(default_factory=[0.0, 0.0, 0.0])


@configclass
class EnvCfg:
    num_envs: int = MISSING
    episode_length: int = 2000
    viewer: ViewerCfg = ViewerCfg()


# 구성 인스턴스 생성
env_cfg = EnvCfg(num_envs=24)

# 딕셔너리로 정보 출력
print(env_cfg.to_dict())

# 구성의 복사본 생성
env_cfg_copy = env_cfg.copy()

# 키워드 인수를 사용하여 임의 필드 대체
env_cfg_copy = env_cfg_copy.replace(num_envs=32)
```

* **매개변수:**
  * **cls** – 감쌀 클래스.
  * **\*\*kwargs** – `dataclass()`에 전달할 추가 인수.
* **반환값:**
  감싼 클래스입니다.

## I/O 작업

파일 I/O 작업을 위한 하위 모듈입니다.

**함수:**

| [`load_torchscript_model`](#isaaclab.utils.io.load_torchscript_model)(model_path[, device])   | 지정된 경로에서 TorchScript 모델을 로드합니다.   |
|-----------------------------------------------------------------------------------------------|-----------------------------------------------------|
| [`dump_yaml`](#isaaclab.utils.io.dump_yaml)(filename, data[, sort_keys])                      | 데이터를 YAML 파일에 안전하게 저장합니다.                 |
| [`load_yaml`](#isaaclab.utils.io.load_yaml)(filename)                                         | 입력 PKL 파일을 안전하게 로드합니다.                     |

### isaaclab.utils.io.load_torchscript_model(model_path: [str](https://docs.python.org/3/library/stdtypes.html#str), device: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'cpu') → [torch.nn.Module](https://docs.pytorch.org/docs/stable/generated/torch.nn.Module.html#torch.nn.Module)

지정된 경로에서 TorchScript 모델을 로드합니다.

이 함수는 TorchScript 모델(.pt 또는 .pth 파일로, torch.jit.save로 생성됨)만 로드합니다. 원시 PyTorch 체크포인트(.pth 파일로, torch.save로 생성됨)에는 작동하지 않습니다.

* **매개변수:**
  * **model_path** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – TorchScript 모델 파일 경로 (.pt 또는 .pth)
  * **device** ([*str*](https://docs.python.org/3/library/stdtypes.html#str) *,* *optional*) – 모델을 로드할 디바이스. 기본값은 ‘cpu’입니다.
* **반환값:**
  평가 모드의 로드된 TorchScript 모델
* **반환 유형:**
  [torch.nn.Module](https://docs.pytorch.org/docs/stable/generated/torch.nn.Module.html#torch.nn.Module)
* **예외:**
  [**FileNotFoundError**](https://docs.python.org/3/library/exceptions.html#FileNotFoundError) – 모델 파일이 존재하지 않을 경우

### isaaclab.utils.io.dump_yaml(filename: [str](https://docs.python.org/3/library/stdtypes.html#str), data: [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [object](https://docs.python.org/3/library/functions.html#object), sort_keys: [bool](https://docs.python.org/3/library/functions.html#bool) = False)

데이터를 YAML 파일에 안전하게 저장합니다.

#### 참고
이 함수는 파일 경로에 누락된 디렉터리를 생성합니다.

* **매개변수:**
  * **filename** – 파일을 저장할 경로입니다.
  * **data** – 저장할 데이터로, 딕셔너리 또는 클래스 객체일 수 있습니다.
  * **sort_keys** – 출력 파일에서 키를 정렬할지 여부. 기본값은 False입니다.

### isaaclab.utils.io.load_yaml(filename: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

입력 PKL 파일을 안전하게 로드합니다.

* **매개변수:**
  **filename** – 피클 파일 경로.
* **예외:**
  [**FileNotFoundError**](https://docs.python.org/3/library/exceptions.html#FileNotFoundError) – 지정된 파일이 존재하지 않을 경우.
* **반환값:**
  입력 파일에서 읽은 데이터입니다.

## 배열 작업

다양한 배열 백엔드 작업을 위한 유틸리티가 포함된 하위 모듈입니다.

**데이터:**

| [`TENSOR_TYPES`](#isaaclab.utils.array.TENSOR_TYPES)                       | 각 백엔드의 타입을 포함하는 딕셔너리입니다.                       |
|----------------------------------------------------------------------------|---------------------------------------------------------------------------|
| [`TENSOR_TYPE_CONVERSIONS`](#isaaclab.utils.array.TENSOR_TYPE_CONVERSIONS) | 각 백엔드의 변환 함수를 포함하는 중첩 딕셔너리입니다. |

**함수:**

| [`convert_to_torch`](#isaaclab.utils.array.convert_to_torch)(array[, dtype, device])   | 주어진 배열을 토치 텐서로 변환합니다.   |
|----------------------------------------------------------------------------------------|-----------------------------------------------|

### isaaclab.utils.array.TensorData(\*args, \*\*kwargs)

텐서 데이터 타입 정의입니다.

넘파이, 토치, 워프 배열의 연합입니다.

alias of [`Union`](https://docs.python.org/3/library/typing.html#typing.Union)[[`ndarray`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray), [`Tensor`](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), `array`]

### isaaclab.utils.array.TENSOR_TYPES *= {'numpy': numpy.ndarray, 'torch': torch.Tensor, 'warp': warp.array}*

각 백엔드의 타입을 포함하는 딕셔너리입니다.

키들은 백엔드의 이름(“numpy”, “torch”, “warp”)이고, 값들은 해당 타입(`np.ndarray`, `torch.Tensor`, `wp.array`)입니다.
</translate>

### isaaclab.utils.array.TENSOR_TYPE_CONVERSIONS *= {'numpy': {warp.array: <function <lambda>>, torch.Tensor: <function <lambda>>}, 'torch': {warp.array: <function <lambda>>, numpy.ndarray: <function <lambda>>}, 'warp': {numpy.array: <function <lambda>>, torch.Tensor: <function <lambda>>}}*

백엔드별 변환 함수를 포함하는 중첩된 사전입니다.

외부 사전의 키는 대상 백엔드의 이름(“numpy”, “torch”, “warp”)입니다. 내부 사전의 키는 소스 백엔드(`np.ndarray`, `torch.Tensor`, `wp.array`)입니다.

### isaaclab.utils.array.convert_to_torch(array: TensorData, dtype: [torch.dtype](https://docs.pytorch.org/docs/stable/tensor_attributes.html#torch.dtype) = None, device: [torch.device](https://docs.pytorch.org/docs/stable/tensor_attributes.html#torch.device) | [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

주어진 배열을 토치 텐서로 변환합니다.

이 함수는 배열을 토치 텐서로 변환하려고 시도합니다. 배열이 numpy/워프 배열이거나 파이썬 리스트/튜플인 경우, 토치 텐서로 변환합니다. 배열이 이미 토치 텐서인 경우, 그대로 반환합니다.

`device`가 None이면, 함수는 데이터의 현재 장치를 추론합니다. numpy 배열의 경우 기본값은 “cpu”이며, 토치 텐서의 경우 “cpu” 또는 “cuda”, 워프 배열의 경우 “cuda”입니다.

#### 주의
PyTorch는 부호 없는 정수 타입을 지원하지 않으므로, 부호 없는 정수 배열은 해당 부호 있는 정수 타입으로 캐스팅되어 부호 있는 정수 배열로 변환됩니다.

* **매개변수:**
  * **array** – 입력 배열. numpy 배열, 워프 배열, 파이썬 리스트/튜플, 또는 토치 텐서일 수 있습니다.
  * **dtype** – 텐서의 목표 데이터 타입.
  * **device** – 텐서의 목표 장치. 기본값은 None입니다.
* **반환값:**
  토치 텐서로 변환된 배열.

## 자산 작업

자산과 리소스가 저장되는 호스트 서버를 정의하는 서브 모듈입니다.

기본적으로 Isaac Sim Nucleus 서버를 사용하여 자산과 리소스를 호스팅합니다. 이를 통해 자산 배포가 쉬워지고 저장소의 코드 크기가 줄어듭니다.

자세한 내용은 [Omniverse Nucleus](https://docs.omniverse.nvidia.com/nucleus/latest/overview/overview.html)를 참조하십시오.

**데이터:**

| [`NUCLEUS_ASSET_ROOT_DIR`](#isaaclab.utils.assets.NUCLEUS_ASSET_ROOT_DIR)   | Nucleus 서버의 루트 디렉터리 경로.                    |
|-----------------------------------------------------------------------------|----------------------------------------------------------------------|
| [`NVIDIA_NUCLEUS_DIR`](#isaaclab.utils.assets.NVIDIA_NUCLEUS_DIR)           | NVIDIA Nucleus 서버의 루트 디렉터리 경로.             |
| [`ISAAC_NUCLEUS_DIR`](#isaaclab.utils.assets.ISAAC_NUCLEUS_DIR)             | NVIDIA Nucleus 서버의 `Isaac` 디렉터리 경로.          |
| [`ISAACLAB_NUCLEUS_DIR`](#isaaclab.utils.assets.ISAACLAB_NUCLEUS_DIR)       | NVIDIA Nucleus 서버의 `Isaac/IsaacLab` 디렉터리 경로. |

**함수:**

| [`check_file_path`](#isaaclab.utils.assets.check_file_path)(path)                                    | Nucleus 서버 또는 로컬에서 파일이 존재하는지 확인.                         |
|------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| [`retrieve_file_path`](#isaaclab.utils.assets.retrieve_file_path)(path[, download_dir, ...])         | Nucleus 서버 또는 로컬에서 파일의 경로를 가져옵니다.                    |
| [`read_file`](#isaaclab.utils.assets.read_file)(path)                                                | Nucleus 서버 또는 로컬에서 파일을 읽습니다.                                  |
| [`check_usd_path_with_timeout`](#isaaclab.utils.assets.check_usd_path_with_timeout)(usd_path[, ...]) | 주어진 USD 파일 경로가 NVIDIA Nucleus 서버에서 사용 가능한지 확인. |

### isaaclab.utils.assets.NUCLEUS_ASSET_ROOT_DIR *= '/persistent/isaac/asset_root/cloud'*

Nucleus 서버의 루트 디렉터리 경로.

### isaaclab.utils.assets.NVIDIA_NUCLEUS_DIR *= 'carb.settings.get_settings.get/NVIDIA'*

NVIDIA Nucleus 서버의 루트 디렉터리 경로.

### isaaclab.utils.assets.ISAAC_NUCLEUS_DIR *= 'carb.settings.get_settings.get/Isaac'*

NVIDIA Nucleus 서버의 `Isaac` 디렉터리 경로.

### isaaclab.utils.assets.ISAACLAB_NUCLEUS_DIR *= 'carb.settings.get_settings.get/Isaac/IsaacLab'*

NVIDIA Nucleus 서버의 `Isaac/IsaacLab` 디렉터리 경로.

### isaaclab.utils.assets.check_file_path(path: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)[0, 1, 2]

Nucleus 서버 또는 로컬에서 파일이 존재하는지 확인합니다.

* **매개변수:**
  **path** – 파일의 경로.
* **반환값:**
  파일의 상태. 가능한 값은 다음과 같습니다.
  * `0` - 파일이 존재하지 않음
  * `1` - 파일이 로컬에 존재함
  * `2` - 파일이 Nucleus 서버에 존재함

### isaaclab.utils.assets.retrieve_file_path(path: [str](https://docs.python.org/3/library/stdtypes.html#str), download_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, force_download: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [str](https://docs.python.org/3/library/stdtypes.html#str)

Nucleus 서버 또는 로컬에서 파일의 경로를 가져옵니다.

파일이 로컬에 존재하면, 파일의 절대 경로를 반환합니다.
파일이 Nucleus 서버에 존재하면, 파일을 로컬 머신으로 다운로드하고 해당 파일의 절대 경로를 반환합니다.

* **매개변수:**
  * **path** – 파일의 경로.
  * **download_dir** – 파일을 다운로드할 디렉터리. 기본값은 None이며, 이 경우 시스템의 임시 디렉터리로 다운로드됩니다.
  * **force_download** – Nucleus 서버에서 파일을 강제로 다운로드할지 여부. 이는 기존 로컬 파일을 덮어씁니다. 기본값은 True입니다.
* **반환값:**
  로컬 머신에서의 파일 경로.
* **예외:**
  * [**FileNotFoundError**](https://docs.python.org/3/library/exceptions.html#FileNotFoundError) – 파일이 로컬 또는 Nucleus 서버에서 발견되지 않을 때 발생.
  * [**RuntimeError**](https://docs.python.org/3/library/exceptions.html#RuntimeError) – Nucleus 서버에서 로컬 머신으로 파일을 복사할 수 없을 때 발생. 로컬에 파일이 già 존재하고 `force_download`가 False로 설정된 경우 발생할 수 있음.

### isaaclab.utils.assets.read_file(path: [str](https://docs.python.org/3/library/stdtypes.html#str)) → BytesIO

Nucleus 서버 또는 로컬에서 파일을 읽습니다.

* **매개변수:**
  **path** – 파일의 경로.
* **예외:**
  [**FileNotFoundError**](https://docs.python.org/3/library/exceptions.html#FileNotFoundError) – 파일이 로컬 또는 Nucleus 서버에서 발견되지 않을 때 발생.
* **반환값:**
  파일의 내용.

### isaaclab.utils.assets.check_usd_path_with_timeout(usd_path: [str](https://docs.python.org/3/library/stdtypes.html#str), timeout: [float](https://docs.python.org/3/library/functions.html#float) = 300, log_interval: [float](https://docs.python.org/3/library/functions.html#float) = 30) → [bool](https://docs.python.org/3/library/functions.html#bool)

NVIDIA Nucleus 서버에서 주어진 USD 파일 경로가 사용 가능한지 확인합니다.

이 함수는 비동기식 USD 경로 가용성 검사를 동기식으로 실행하며, 완료될 때까지 진행 상황을 주기적으로 로깅합니다. 파일의 HTTP 상태 코드가 200이면 서버에서 사용 가능한 것으로 간주됩니다. 그렇지 않으면 사용 불가능한 것으로 간주됩니다.

원격 자산을 로드하기 전에 서버 responsiveness를 확인하는 데 유용합니다. 검사가 완료되거나 타임아웃이 발생할 때까지 실행을 차단합니다.

* **매개변수:**
  * **usd_path** – 확인할 원격 USD 파일 경로.
  * **timeout** – 서버 확인을 기다릴 최대 시간(초).
  * **log_interval** – 진행 상황을 기록할 간격(초).
* **반환값:**
  주어진 USD 경로가 서버에서 사용 가능한지 여부.

## 버퍼 작업

다양한 버퍼를 포함하는 서브 모듈입니다.

**클래스:**

| [`CircularBuffer`](#isaaclab.utils.buffers.CircularBuffer)       | 배치된 텐서 데이터의 기록을 저장하는 원형 버퍼.   |
|------------------------------------------------------------------|-----------------------------------------------------------------|
| [`DelayBuffer`](#isaaclab.utils.buffers.DelayBuffer)             | 지연을 두어 저장된 데이터를 검색할 수 있는 지연 버퍼.    |
| [`TimestampedBuffer`](#isaaclab.utils.buffers.TimestampedBuffer) | 데이터와 타임스탬프를 포함하는 버퍼 클래스.               |

### *class* isaaclab.utils.buffers.CircularBuffer

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

배치된 텐서 데이터의 기록을 저장하는 원형 버퍼입니다.

이 클래스는 최대 길이와 배치 크기로 초기화되는 배치된 텐서 데이터의 기록을 저장하는 원형 버퍼를 구현합니다. 데이터는 원형 방식으로 저장되며, LIFO(Last-In-First-Out) 방식으로 검색할 수 있습니다. 이 버퍼는 각 환경마다 자체 데이터가 있는 멀티-환경 설정에서 사용하도록 설계되었습니다.

추가되는 데이터의 형태는 (batch_size, …)로 예상되며, 첫 번째 차원은 배치 차원입니다. 그에 따라 원형 버퍼의 형태는 (max_len, batch_size, …)입니다.

**메서드:**

| [`__init__`](#isaaclab.utils.buffers.CircularBuffer.__init__)(max_len, batch_size, device)   | 원형 버퍼를 초기화.                           |
|----------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| [`reset`](#isaaclab.utils.buffers.CircularBuffer.reset)([batch_ids])                         | 지정된 배치 인덱스에서 원형 버퍼를 초기화합니다.          |
| [`append`](#isaaclab.utils.buffers.CircularBuffer.append)(data)                              | 데이터를 원형 버퍼에 추가합니다.                          |

**속성:**

| [`batch_size`](#isaaclab.utils.buffers.CircularBuffer.batch_size)         | 원형 버퍼의 배치 크기입니다.                                                        |
|---------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
| [`device`](#isaaclab.utils.buffers.CircularBuffer.device)                 | 처리에 사용되는 장치입니다.                                                         |
| [`max_length`](#isaaclab.utils.buffers.CircularBuffer.max_length)         | 원형 버퍼의 최대 길이입니다.                                                        |
| [`current_length`](#isaaclab.utils.buffers.CircularBuffer.current_length) | 버퍼의 현재 길이입니다.                                                             |
| [`buffer`](#isaaclab.utils.buffers.CircularBuffer.buffer)                 | 가장 최근 항목이 끝에 있고 가장 오래된 항목이 앞에 있는 완전한 원형 버퍼입니다.    |

#### \_\_init_\_(max_len: [int](https://docs.python.org/3/library/functions.html#int), batch_size: [int](https://docs.python.org/3/library/functions.html#int), device: [str](https://docs.python.org/3/library/stdtypes.html#str))

원형 버퍼를 초기화합니다.

* **매개변수:**
  * **max_len** – 원형 버퍼의 최대 길이입니다. 허용되는 최소값은 1입니다.
  * **batch_size** – 데이터의 배치 차원입니다.
  * **device** – 처리에 사용되는 장치입니다.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 버퍼 크기가 1보다 작을 경우 발생합니다.

#### *property* batch_size *: [int](https://docs.python.org/3/library/functions.html#int)*

원형 버퍼의 배치 크기입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

처리에 사용되는 장치입니다.

#### *property* max_length *: [int](https://docs.python.org/3/library/functions.html#int)*

원형 버퍼의 최대 길이입니다.

#### *property* current_length *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

버퍼의 현재 길이입니다. 형태는 (batch_size,)입니다.

버퍼가 원형이므로 현재 길이는 푸시 횟수와 최대 길이 중 작은 값입니다.

#### *property* buffer *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

가장 최근 항목이 끝에 있고 가장 오래된 항목이 앞에 있는 완전한 원형 버퍼입니다.

버퍼의 형태는 (batch_size, max_length, …)입니다.

#### NOTE
가장 오래된 항목은 차원 1의 시작 부분에 있습니다.

#### reset(batch_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

지정된 배치 인덱스에서 원형 버퍼를 초기화합니다.

* **매개변수:**
  **batch_ids** – 배치 차원에서 재설정할 요소입니다. 기본값은 None이며, 이는 모든 배치 인덱스를 재설정한다는 의미입니다.

#### append(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor))

데이터를 원형 버퍼에 추가합니다.

* **매개변수:**
  **data** – 원형 버퍼에 추가할 데이터입니다. 첫 번째 차원은 배치 차원이어야 합니다. 형태는 (batch_size, …)입니다.
* **예외 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 데이터의 배치 크기가 버퍼와 다를 경우 발생합니다.

### *class* isaaclab.utils.buffers.DelayBuffer

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

지연을 두어 저장된 데이터를 검색할 수 있는 지연 버퍼입니다.

이 클래스는 입력 데이터를 저장하기 위해 배치된 원형 버퍼를 사용합니다. 표준 원형 버퍼가 데이터를 검색하기 위해 LIFO(후입선출) 원리를 사용하는 반면, 지연 버퍼 클래스는 사용자가 설정한 지연에 따라 데이터를 검색할 수 있습니다. 예를 들어, 버퍼 내 지연이 1로 설정되어 있다면 스트림에서 두 번째로 마지막 항목이 검색됩니다. 지연이 2라면 세 번째로 마지막 항목이 검색되고, 이와 같이 계속됩니다.

이 클래스는 배치된 텐서 데이터를 저장할 수 있습니다. 이는 추가되는 데이터의 형태가 (batch_size, …)가 되도록 예상한다는 의미이며, 여기서 첫 번째 차원은 배치 차원입니다. 이에 따라 지연은 각 배치 인덱스별로 별도로 설정할 수 있습니다. 요청된 지연이 기반 버퍼의 현재 길이보다 클 경우, 가장 최근 항목이 반환됩니다.

#### NOTE
기본적으로 지연 버퍼에는 지연이 없으며, 이는 데이터가 그대로 반환된다는 의미입니다.

**메서드:**

| [`__init__`](#isaaclab.utils.buffers.DelayBuffer.__init__)(history_length, batch_size, device)   | 지연 버퍼를 초기화합니다.                                                                         |
|--------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| [`set_time_lag`](#isaaclab.utils.buffers.DelayBuffer.set_time_lag)(time_lag[, batch_ids])        | 제공된 배치 인덱스에 대해 지연 버퍼의 시간 지연을 설정합니다.                            |
| [`reset`](#isaaclab.utils.buffers.DelayBuffer.reset)([batch_ids])                                | 지정된 배치 인덱스에서 지연 버퍼의 데이터를 초기화합니다.                                   |
| [`compute`](#isaaclab.utils.buffers.DelayBuffer.compute)(data)                                   | 입력 데이터를 버퍼에 추가하고 시간 지연 지연에 기반한 과거 버전의 데이터를 반환합니다. |

**속성:**

| [`batch_size`](#isaaclab.utils.buffers.DelayBuffer.batch_size)         | 원형 버퍼의 배치 크기입니다.                |
|------------------------------------------------------------------------|---------------------------------------------------|
| [`device`](#isaaclab.utils.buffers.DelayBuffer.device)                 | 처리에 사용되는 장치입니다.                   |
| [`history_length`](#isaaclab.utils.buffers.DelayBuffer.history_length) | 지연 버퍼의 기록 길이입니다.           |
| [`min_time_lag`](#isaaclab.utils.buffers.DelayBuffer.min_time_lag)     | 지연할 수 있는 최소 시간 단계 수입니다. |
| [`max_time_lag`](#isaaclab.utils.buffers.DelayBuffer.max_time_lag)     | 지연할 수 있는 최대 시간 단계 수입니다. |
| [`time_lags`](#isaaclab.utils.buffers.DelayBuffer.time_lags)           | 각 배치 인덱스에 대한 시간 지연입니다.             |

#### \_\_init_\_(history_length: [int](https://docs.python.org/3/library/functions.html#int), batch_size: [int](https://docs.python.org/3/library/functions.html#int), device: [str](https://docs.python.org/3/library/stdtypes.html#str))

지연 버퍼를 초기화합니다.

* **매개변수:**
  * **history_length** – 버퍼의 기록, 즉 과거의 몇 시간 단계 동안 데이터를 버퍼링할 것인지를 나타냅니다. 이 값은 예상되는 최대 시간 단계 지연과 동일하게 설정하는 것이 권장됩니다. 허용되는 최소값은 0이며, 이는 최신 데이터만 저장됨을 의미합니다.
  * **batch_size** – 데이터의 배치 차원입니다.
  * **device** – 처리에 사용되는 장치입니다.

#### *property* batch_size *: [int](https://docs.python.org/3/library/functions.html#int)*

원형 버퍼의 배치 크기입니다.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

처리에 사용되는 장치입니다.

#### *property* history_length *: [int](https://docs.python.org/3/library/functions.html#int)*

지연 버퍼의 기록 길이입니다.

0이면 최신 데이터만 저장됩니다. 1이면 최신 데이터와 이전 데이터가 저장되며, 이와 같이 증가합니다.

#### *property* min_time_lag *: [int](https://docs.python.org/3/library/functions.html#int)*

지연할 수 있는 최소 시간 단계 수입니다.

이 값은 음수이거나 [`max_time_lag`](#isaaclab.utils.buffers.DelayBuffer.max_time_lag)보다 클 수 없습니다.

#### *property* max_time_lag *: [int](https://docs.python.org/3/library/functions.html#int)*

지연할 수 있는 최대 시간 단계 수입니다.

이 값은 [`history_length`](#isaaclab.utils.buffers.DelayBuffer.history_length)을 초과할 수 없습니다.

#### *property* time_lags *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)*

각 배치 인덱스에 대한 시간 지연입니다.

텐서의 형태는 (batch_size, )이며, 각 인덱스의 값은 해당 인덱스에 대한 지연을 나타냅니다. 이 값은 버퍼에서 데이터를 검색하는 데 사용됩니다.

#### set_time_lag(time_lag: [int](https://docs.python.org/3/library/functions.html#int) | [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), batch_ids: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

제공된 배치 인덱스에 대해 지연 버퍼의 시간 지연을 설정합니다.

* **매개변수:**
  * **time_lag** – 

    버퍼에 대한 원하는 지연입니다.
    * 정수가 제공된 경우, 제공된 배치 인덱스에 동일한 지연이 설정됩니다.
    * 텐서가 제공된 경우, 각 배치 인덱스에 별도로 지연이 설정됩니다. 텐서의 형태는 (len(batch_ids),)이어야 합니다.
  * **batch_ids** – 시간 지연을 설정할 배치 인덱스입니다. 기본값은 None이며, 이는 모든 배치 인덱스에 시간 지연을 설정한다는 의미입니다.
* **예외 발생:**
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – `time_lag`의 유형이 int 또는 integer tensor가 아닌 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 최소 시간 지연이 음수이거나, 최대 시간 지연이 기록 길이보다 큰 경우.

#### reset(batch_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

지정된 배치 인덱스의 딜레이 버퍼 데이터 초기화.

* **Parameters:**
  **batch_ids** – 배치 차원에서 초기화할 요소. 기본값은 None이며, 모든 배치 인덱스를 초기화함.

#### compute(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

입력 데이터를 버퍼에 추가하고, 시간 지연 지연에 기반한 데이터의 과거 버전 반환.

요청된 지연이 마지막 초기화 이후 버퍼된 데이터 포인트 수보다 클 경우, 함수는 최신 데이터를 반환함. 예를 들어, 지연이 2로 설정되고 버퍼에 하나의 데이터 포인트만 저장된 경우, 함수는 최신 데이터를 반환함. 지연이 2로 설정되고 세 개의 데이터 포인트가 저장된 경우, 함수는 첫 번째 데이터 포인트를 반환함.

* **Parameters:**
  **data** – 입력 데이터. 형태는 (batch_size, …).
* **Returns:**
  저장된 버퍼에서 지연된 데이터 버전. 형태는 (batch_size, …).

### *class* isaaclab.utils.buffers.TimestampedBuffer

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

데이터와 타임스탬프를 포함하는 버퍼 클래스.

이 클래스는 텐서와 그 타임스탬프를 저장하는 간단한 데이터 컨테이너입니다. 타임스탬프는 버퍼의 마지막 업데이트를 추적하는 데 사용됩니다. 타임스탬프는 기본적으로 -1.0으로 설정되어 버퍼가 아직 업데이트되지 않았음을 나타냅니다. 버퍼의 데이터가 업데이트될 때마다 타임스탬프도 업데이트해야 합니다. 이렇게 하면 버퍼가 데이터가 오래되었는지 확인하고 새로 고침이 필요한지 판단하는 데 사용될 수 있습니다.

이 버퍼는 데이터가 오래된 경우에만 데이터를 업데이트하는 랜시 버퍼를 만드는 데 유용합니다. 데이터 계산을 비용이 많이 들거나 검색이 어려운 경우에 유용할 수 있습니다. 사용 예시는 [`isaaclab.assets`](isaaclab.assets.md#module-isaaclab.assets) 모듈의 데이터 클래스를 참조하십시오.

**Attributes:**

| [`data`](#isaaclab.utils.buffers.TimestampedBuffer.data)           | 버퍼에 저장된 데이터.              |
|--------------------------------------------------------------------|---------------------------------------------|
| [`timestamp`](#isaaclab.utils.buffers.TimestampedBuffer.timestamp) | 버퍼의 마지막 업데이트 타임스탬프. |

**Methods:**

| [`__init__`](#isaaclab.utils.buffers.TimestampedBuffer.__init__)([data, timestamp])   |    |
|---------------------------------------------------------------------------------------|----|

#### data *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)* *= None*

버퍼에 저장된 데이터. 기본값은 None이며, 버퍼가 비어있음을 나타냄.

#### \_\_init_\_(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) = None, timestamp: [float](https://docs.python.org/3/library/functions.html#float) = -1.0) → [None](https://docs.python.org/3/library/constants.html#None)

#### timestamp *: [float](https://docs.python.org/3/library/functions.html#float)* *= -1.0*

버퍼의 마지막 업데이트 타임스탬프. 기본값은 -1.0이며, 버퍼가 아직 업데이트되지 않았음을 나타냄.

## 데이터세트 작업

데이터세트 클래스 및 메서드를 위한 서브모듈.

## 딕셔너리 작업

딕셔너리와 작업하기 위한 유틸리티 서브모듈.

**Functions:**

| [`class_to_dict`](#isaaclab.utils.dict.class_to_dict)(obj)                                      | 객체를 재귀적으로 딕셔너리로 변환.                               |
|-------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| [`update_class_from_dict`](#isaaclab.utils.dict.update_class_from_dict)(obj, data[, \_ns])      | 딕셔너리를 읽고 객체 변수를 재귀적으로 설정.                    |
| [`dict_to_md5_hash`](#isaaclab.utils.dict.dict_to_md5_hash)(data)                               | MD5 해시를 사용하여 딕셔너리를 해시 가능한 키로 변환.                     |
| [`convert_dict_to_backend`](#isaaclab.utils.dict.convert_dict_to_backend)(data[, backend, ...]) | 딕셔너리의 모든 배열 또는 텐서를 지정된 백엔드로 변환.            |
| [`update_dict`](#isaaclab.utils.dict.update_dict)(orig_dict, new_dict)                          | 새 딕셔너리의 값으로 기존 딕셔너리 업데이트.               |
| [`replace_slices_with_strings`](#isaaclab.utils.dict.replace_slices_with_strings)(data)         | 딕셔너리에서 슬라이스 객체를 문자열 표현으로 대체.     |
| [`replace_strings_with_slices`](#isaaclab.utils.dict.replace_strings_with_slices)(data)         | 딕셔너리에서 슬라이스의 문자열 표현을 슬라이스 객체로 대체. |
| [`print_dict`](#isaaclab.utils.dict.print_dict)(val[, nesting, start])                          | 중첩된 딕셔너리 출력.                                                 |

### isaaclab.utils.dict.class_to_dict(obj: [object](https://docs.python.org/3/library/functions.html#object)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)]

객체를 재귀적으로 딕셔너리로 변환.

#### NOTE
"\_\_"로 시작하는 모든 이름은 무시됨(즉, 내장 메서드).

* **Parameters:**
  **obj** – 변환할 클래스 인스턴스.
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 인자가 객체가 아닌 경우.
* **Returns:**
  변환된 딕셔너리 매핑.

### isaaclab.utils.dict.update_class_from_dict(obj, data: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)], \_ns: [str](https://docs.python.org/3/library/stdtypes.html#str) = '') → [None](https://docs.python.org/3/library/constants.html#None)

딕셔너리를 읽고 객체 변수를 재귀적으로 설정.

이 함수는 클래스 멤버 속성을 제자리에서 업데이트함.

* **Parameters:**
  * **obj** – 업데이트할 클래스 인스턴스.
  * **data** – 업데이트할 입력 딕셔너리.
  * **\_ns** – 현재 객체의 네임스페이스. 중첩된 구성 클래스 또는 딕셔너리에 유용함. 기본값은 "".
* **Raises:**
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – 입력이 딕셔너리가 아닌 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 딕셔너리에 기본 설정 유형과 일치하지 않는 값이 있는 경우.
  * [**KeyError**](https://docs.python.org/3/library/exceptions.html#KeyError) – 딕셔너리에 기본 설정 유형에 존재하지 않는 키가 있는 경우.

### isaaclab.utils.dict.dict_to_md5_hash(data: [object](https://docs.python.org/3/library/functions.html#object)) → [str](https://docs.python.org/3/library/stdtypes.html#str)

딕셔너리를 MD5 해시를 사용하여 해시 가능한 키로 변환.

* **Parameters:**
  **data** – 변환할 입력 딕셔너리 또는 구성 객체.
* **Returns:**
  16진수만 포함하는 두 배 길이의 문자열 객체.

### isaaclab.utils.dict.convert_dict_to_backend(data: [dict](https://docs.python.org/3/library/stdtypes.html#dict), backend: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'numpy', array_types: [Iterable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Iterable)[[str](https://docs.python.org/3/library/stdtypes.html#str)] = ('numpy', 'torch', 'warp')) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

딕셔너리의 모든 배열 또는 텐서를 지정된 백엔드로 변환.

이 함수는 딕셔너리를 반복하여 지정된 유형의 모든 배열 또는 텐서를 원하는 백엔드로 변환하고 새로운 딕셔너리에 저장함. 중첩된 딕셔너리에서도 작동함.

현재 지원되는 백엔드는 "numpy", "torch", "warp"임.

#### NOTE
이 함수는 배열 또는 텐서만 변환함. 다른 유형의 데이터는 변경되지 않음. 가변 유형(예: 리스트)은 새 딕셔너리에 의해 참조되므로 복사되지 않음.

* **Parameters:**
  * **data** – 배열 또는 텐서 데이터를 값으로 포함하는 입력 딕셔너리.
  * **backend** – 딕셔너리의 배열을 변환할 백엔드("numpy", "torch", "warp"). 기본값은 "numpy".
  * **array_types** – 원하는 백엔드로 변환할 배열 유형을 포함하는 리스트. 기본값은 ("numpy", "torch", "warp").
* **Raises:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 지정된 `backend` 또는 `array_types`가 지원되는 백엔드("numpy", "torch", "warp")에 없는 경우.
* **Returns:**
  원하는 백엔드로 변환된 데이터를 포함하는 업데이트된 딕셔너리.

### isaaclab.utils.dict.update_dict(orig_dict: [dict](https://docs.python.org/3/library/stdtypes.html#dict), new_dict: [Mapping](https://trimesh.org/trimesh.typed.html#trimesh.typed.Mapping)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

새 딕셔너리의 값으로 기존 딕셔너리 업데이트.

이 함수는 dict.update() 함수의 동작을 모방함. 그러나 중첩된 딕셔너리에서도 작동함.

* **Parameters:**
  * **orig_dict** – 항목을 삽입할 원래 딕셔너리.
  * **new_dict** – 항목을 삽입할 새 딕셔너리.
* **Returns:**
  업데이트된 딕셔너리.

### isaaclab.utils.dict.replace_slices_with_strings(data: [dict](https://docs.python.org/3/library/stdtypes.html#dict)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

딕셔너리 내의 슬라이스 객체를 해당 문자열 표현으로 대체합니다.

* **매개변수:**
  **data** – 처리할 딕셔너리.
* **반환값:**
  슬라이스 객체가 문자열 표현으로 대체된 딕셔너리.

### isaaclab.utils.dict.replace_strings_with_slices(data: [dict](https://docs.python.org/3/library/stdtypes.html#dict)) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

딕셔너리 내의 슬라이스 문자열 표현을 슬라이스 객체로 대체합니다.

* **매개변수:**
  **data** – 처리할 딕셔너리.
* **반환값:**
  슬라이스 문자열 표현이 슬라이스 객체로 대체된 딕셔너리.

### isaaclab.utils.dict.print_dict(val, nesting: [int](https://docs.python.org/3/library/functions.html#int) = -4, start: [bool](https://docs.python.org/3/library/functions.html#bool) = True)

중첩된 딕셔너리를 출력합니다.

## 보간 연산

다양한 보간 방법을 위한 서브모듈입니다.

**클래스:**

| [`LinearInterpolation`](#isaaclab.utils.interpolation.LinearInterpolation)   | 임의의 쿼리 지점에서 샘플링된 스칼라 함수를 선형 보간합니다.   |
|------------------------------------------------------------------------------|---------------------------------------------------------------|

### *class* isaaclab.utils.interpolation.LinearInterpolation

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

임의의 쿼리 지점에서 샘플링된 스칼라 함수를 선형 보간합니다.

이 클래스는 스칼라 함수에 대한 선형 보간을 구현합니다. 이 함수는 실수 값 x를 실수 값 y로 매핑합니다. 함수 정의역의 샘플 x와 그에 해당하는 값 y를 기대합니다. 클래스는 임의의 지점에서 함수의 값을 쿼리할 수 있습니다.

보간은 쿼리 지점과 가장 가까운 두 x 점을 찾아 해당하는 y 값들 간에 선형 보간을 수행함으로써 이루어집니다. 입력 점 외부의 쿼리 지점에 대해서는 경계 값을 기준으로 0차 홀드 외삽을 수행합니다. 즉, 클래스는 x에서 가장 가까운 점의 값을 반환합니다.

**메서드:**

| [`__init__`](#isaaclab.utils.interpolation.LinearInterpolation.__init__)(x, y, device)   | 선형 보간을 초기화합니다.                           |
|------------------------------------------------------------------------------------------|-----------------------------------------------------|
| [`compute`](#isaaclab.utils.interpolation.LinearInterpolation.compute)(q)                | 쿼리 지점에 대한 선형 보간 값을 계산합니다.         |

#### \_\_init_\_(x: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), y: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), device: [str](https://docs.python.org/3/library/stdtypes.html#str))

선형 보간을 초기화합니다.

스칼라 함수는 실수 값 x를 실수 값 y로 매핑합니다. 클래스에 대한 입력은 함수 정의역의 샘플 x와 그에 해당하는 값 y입니다.

#### 참고
입력 텐서 x는 오름차순으로 정렬되어야 합니다.

* **매개변수:**
  * **x** – 함수 정의역의 샘플 벡터. 값은 오름차순으로 정렬되어야 합니다.
    형태는 (num_samples,)
  * **y** – 입력 x와 관련된 함수의 값. 형태는 (num_samples,)
  * **device** – 처리에 사용되는 장치.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 텐서가 비어 있거나 크기가 다른 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 텐서 x가 오름차순으로 정렬되지 않은 경우.

#### compute(q: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

쿼리 지점에 대한 선형 보간 값을 계산합니다.

* **매개변수:**
  **q** – 쿼리 지점. 임의의 형태를 가질 수 있습니다.
* **반환값:**
  쿼리 지점에서의 보간 값. 입력 텐서와 동일한 형태를 가집니다.

## 로거 연산

로깅 유틸리티를 위한 서브모듈입니다.

로거를 사용하려면 [`logging.getLogger()`](https://docs.python.org/3/library/logging.html#logging.getLogger) 함수를 사용할 수 있습니다.

### 예시

```pycon
>>> import logging
>>>
>>> # 현재 모듈에 대한 로거 정의(세부 제어 활성화)
>>> logger = logging.getLogger(__name__)
>>>
>>> # 로그 메시지 기록
>>> logger.info("이것은 정보 메시지입니다")
>>> logger.warning("이것은 경고 메시지입니다")
>>> logger.error("이것은 오류 메시지입니다")
>>> logger.critical("이것은 치명적 메시지입니다")
>>> logger.debug("이것은 디버그 메시지입니다")
```

**함수:**

| [`configure_logging`](#isaaclab.utils.logger.configure_logging)([logging_level, ...])   | 색상 포맷터와 속도 제한 필터로 로거를 설정합니다.   |
|-----------------------------------------------------------------------------------------|--------------------------------------------------|

**클래스:**

| [`ColoredFormatter`](#isaaclab.utils.logger.ColoredFormatter)   | 로깅용 색상 포맷터.      |
|-----------------------------------------------------------------|--------------------------|
| [`RateLimitFilter`](#isaaclab.utils.logger.RateLimitFilter)     | 사용자 정의 속도 제한 경고 필터. |

### isaaclab.utils.logger.configure_logging(logging_level: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL'] = 'WARNING', save_logs_to_file: [bool](https://docs.python.org/3/library/functions.html#bool) = True, log_dir: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [Logger](https://docs.python.org/3/library/logging.html#logging.Logger)

색상 포맷터와 속도 제한 필터로 로거를 설정합니다.

이 함수는 IsaacLab용 기본 로거를 정의합니다. 색상 포맷터가 있는 스트림 핸들러와 속도 제한 필터를 추가합니다. `save_logs_to_file`가 True이면, 로그를 파일에 저장하는 파일 핸들러도 추가합니다. 로그 디렉터리는 `log_dir`를 사용하여 지정할 수 있습니다. 지정하지 않으면 로그는 임시 디렉터리의 “isaaclab/logs” 하위 디렉터리에 저장됩니다.

로그 파일 이름은 “isaaclab_{datetime.now().strftime(‘%Y-%m-%d_%H-%M-%S’)}.log” 형식으로 지정됩니다.
로그 레코드 형식은 “%(asctime)s [%(filename)s:%(lineno)d] %(levelname)s: %(message)s”입니다.
날짜 형식은 “%Y-%m-%d %H:%M:%S”입니다.

* **매개변수:**
  * **logging_level** – 로깅 수준.
  * **save_logs_to_file** – 로그를 파일에 저장할지 여부.
  * **log_dir** – 로그를 저장할 디렉터리. 기본값은 None이며,この場合 로그는 임시 디렉터리의 “isaaclab/logs” 하위 디렉터리에 저장됩니다.
* **반환값:**
  루트 로거.

### *class* isaaclab.utils.logger.ColoredFormatter

Bases: [`Formatter`](https://docs.python.org/3/library/logging.html#logging.Formatter)

로깅용 색상 포맷터.

이 포맷터는 로그 수준에 따라 로그 메시지를 색칠합니다.

**속성:**

| [`COLORS`](#isaaclab.utils.logger.ColoredFormatter.COLORS)   | 로그 수준별 색상.   |
|--------------------------------------------------------------|---------------------|
| [`RESET`](#isaaclab.utils.logger.ColoredFormatter.RESET)     | 색상 리셋.          |

**메서드:**

| [`format`](#isaaclab.utils.logger.ColoredFormatter.format)(record)   | 로그 레코드를 포맷합니다.   |
|----------------------------------------------------------------------|----------------------------|

#### COLORS *= {'CRITICAL': '\\x1b[1;31m', 'DEBUG': '\\x1b[0m', 'ERROR': '\\x1b[31m', 'INFO': '\\x1b[0m', 'WARNING': '\\x1b[33m'}*

로그 수준별 색상.

#### RESET *= '\\x1b[0m'*

색상 리셋.

#### format(record: [LogRecord](https://docs.python.org/3/library/logging.html#logging.LogRecord)) → [str](https://docs.python.org/3/library/stdtypes.html#str)

로그 레코드를 포맷합니다.

* **매개변수:**
  **record** – 포맷할 로그 레코드.
* **반환값:**
  포맷된 로그 레코드.

### *class* isaaclab.utils.logger.RateLimitFilter

Bases: [`Filter`](https://docs.python.org/3/library/logging.html#logging.Filter)

사용자 정의 속도 제한 경고 필터.

이 필트는 경고 수준 메시지가 동일한 메시지에 대해 몇 초마다 한 번만 기록되도록 허용합니다. 이는 동일한 메시지로 로그가 넘치는 것을 방지하는 데 유용합니다.

**메서드:**

| [`__init__`](#isaaclab.utils.logger.RateLimitFilter.__init__)([interval_seconds])   | 속도 제한 필터를 초기화합니다.                                     |
|-------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| [`filter`](#isaaclab.utils.logger.RateLimitFilter.filter)(record)                   | 경고 수준 메시지가 동일한 메시지에 대해 몇 초마다 한 번만 기록되도록 허용합니다. |

#### \_\_init_\_(interval_seconds: [int](https://docs.python.org/3/library/functions.html#int) = 5)

속도 제한 필터를 초기화합니다.

* **매개변수:**
  **interval_seconds** – 경고를 제한할 초 단위 간격.
  기본값은 5초입니다.

#### filter(record: [LogRecord](https://docs.python.org/3/library/logging.html#logging.LogRecord)) → [bool](https://docs.python.org/3/library/functions.html#bool)

경고 수준 메시지가 동일한 메시지에 대해 몇 초마다 한 번만 기록되도록 허용합니다.

* **매개변수:**
  **record** – 필터링할 로그 레코드.
* **반환값:**
  메시지가 기록되어야 하면 True, 그렇지 않으면 False.

## 수학 연산

다양한 수학 연산을 위한 유틸리티를 포함하는 서브모듈입니다.

**함수:**

| [`scale_transform`](#isaaclab.utils.math.scale_transform)                                                              | 주어진 입력 텐서를 [-1, 1] 범위로 정규화합니다.                                                      |
|------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| [`unscale_transform`](#isaaclab.utils.math.unscale_transform)                                                          | [-1, 1] 범위의 주어진 입력 텐서를 (lower, upper) 범위로 역정규화합니다.                                 |
| [`saturate`](#isaaclab.utils.math.saturate)                                                                            | 주어진 입력 텐서를 (lower, upper) 범위로 클램프합니다.                                                |
| [`normalize`](#isaaclab.utils.math.normalize)                                                                          | 주어진 입력 텐서를 단위 길이로 정규화합니다.                                                          |
| [`wrap_to_pi`](#isaaclab.utils.math.wrap_to_pi)                                                                        | 입력 각도(라디안)를 [$-\pi, \pi$] 범위로 래핑합니다.                                                |
| [`copysign`](#isaaclab.utils.math.copysign)                                                                            | 입력의 크기와 다른 값의 부호를 요소별로 가지는 새로운 부동소수점 텐서를 생성합니다.                |
| [`quat_unique`](#isaaclab.utils.math.quat_unique)                                                                      | 단위 쿼터니언을 실수부가 음이 아닌 표준 형태로 변환합니다.                                          |
| [`matrix_from_quat`](#isaaclab.utils.math.matrix_from_quat)                                                            | 쿼터니언으로 주어진 회전을 회전 행렬로 변환합니다.                                                  |
| [`convert_quat`](#isaaclab.utils.math.convert_quat)(quat[, to])                                                        | 쿼터니언을 한 convention에서 다른 convention으로 변환합니다.                                        |
| [`quat_conjugate`](#isaaclab.utils.math.quat_conjugate)                                                                | 쿼터니언의 공역(공액)을 계산합니다.                                                               |
| [`quat_inv`](#isaaclab.utils.math.quat_inv)                                                                            | 쿼터니언의 역을 계산합니다.                                                                       |
| [`quat_from_euler_xyz`](#isaaclab.utils.math.quat_from_euler_xyz)                                                      | 라디안 단위의 오일러 각도로 주어진 회전을 쿼터니언으로 변환합니다.                                  |
| [`quat_from_matrix`](#isaaclab.utils.math.quat_from_matrix)                                                            | 회전 행렬로 주어진 회전을 쿼터니언으로 변환합니다.                                                  |
| [`matrix_from_euler`](#isaaclab.utils.math.matrix_from_euler)(euler_angles, convention)                                | 오일러 각도(내재적) 라디안 단위로 주어진 회전을 회전 행렬로 변환합니다.                             |
| [`euler_xyz_from_quat`](#isaaclab.utils.math.euler_xyz_from_quat)                                                      | 쿼터니언으로 주어진 회전을 라디안 단위의 오일러 각도로 변환합니다.                                 |
| [`axis_angle_from_quat`](#isaaclab.utils.math.axis_angle_from_quat)                                                    | 쿼터니언으로 주어진 회전을 축/각도로 변환합니다.                                                    |
| [`quat_from_angle_axis`](#isaaclab.utils.math.quat_from_angle_axis)                                                    | 각-축으로 주어진 회전을 쿼터니언으로 변환합니다.                                                    |
| [`quat_mul`](#isaaclab.utils.math.quat_mul)                                                                            | 두 쿼터니언을 함께 곱합니다.                                                                        |
| [`yaw_quat`](#isaaclab.utils.math.yaw_quat)                                                                            | 쿼터니언의 요성 성분을 추출합니다.                                                                  |
| [`quat_box_minus`](#isaaclab.utils.math.quat_box_minus)                                                                | 두 쿼터니언 사이의 박스-마이너스 연산자(쿼터니언 차이)를 계산합니다.                               |
| [`quat_box_plus`](#isaaclab.utils.math.quat_box_plus)                                                                  | 쿼터니언에 증분을 적용하는 박스-플러스 연산자(쿼터니언 업데이트)를 계산합니다.                       |
| [`quat_apply`](#isaaclab.utils.math.quat_apply)                                                                        | 벡터에 쿼터니언 회전을 적용합니다.                                                                |
| [`quat_apply_inverse`](#isaaclab.utils.math.quat_apply_inverse)                                                        | 벡터에 역 쿼터니언 회전을 적용합니다.                                                             |
| [`quat_apply_yaw`](#isaaclab.utils.math.quat_apply_yaw)                                                                | 벡터를 요(방향) 축 주변으로만 회전시킵니다.                                                          |
| [`quat_rotate`](#isaaclab.utils.math.quat_rotate)(q, v)                                                                | q와 v의 마지막 차원을 따라 벡터를 쿼터니언으로 회전시킵니다.                                      |
| [`quat_rotate_inverse`](#isaaclab.utils.math.quat_rotate_inverse)(q, v)                                                | q와 v의 마지막 차원을 따라 벡터를 쿼터니언의 역으로 회전시킵니다.                                  |
| [`quat_error_magnitude`](#isaaclab.utils.math.quat_error_magnitude)                                                    | 두 쿼터니언 사이의 회전 차이를 계산합니다.                                                          |
| [`skew_symmetric_matrix`](#isaaclab.utils.math.skew_symmetric_matrix)                                                  | 벡터의 대칭행렬을 계산합니다.                                                                      |
| [`is_identity_pose`](#isaaclab.utils.math.is_identity_pose)(pos, rot)                                                  | 입력 자세가 항등 변환인지 확인합니다.                                                               |
| [`combine_frame_transforms`](#isaaclab.utils.math.combine_frame_transforms)                                            | 두 참조 프레임 사이의 변환을 정지 프레임으로 결합합니다.                                            |
| [`rigid_body_twist_transform`](#isaaclab.utils.math.rigid_body_twist_transform)(v0, w0, t01, q01)                      | 참조 프레임 사이에서 강체의 선형 및 각속도를 변환합니다.                                          |
| [`subtract_frame_transforms`](#isaaclab.utils.math.subtract_frame_transforms)(t01, q01[, t02, q02])                    | 두 참조 프레임 사이의 변환을 정지 프레임으로 빼서 계산합니다.                                       |
| [`compute_pose_error`](#isaaclab.utils.math.compute_pose_error)(t01, q01, t02, q02[, ...])                             | 소스 프레임과 목표 프레임 사이의 위치 및 자세 오차를 계산합니다.                                   |
| [`apply_delta_pose`](#isaaclab.utils.math.apply_delta_pose)                                                            | 소스 자세에 델타 자세 변환을 적용합니다.                                                            |
| [`transform_points`](#isaaclab.utils.math.transform_points)(points[, pos, quat])                                       | 주어진 프레임의 입력 점을 목표 프레임으로 변환합니다.                                               |
| [`orthogonalize_perspective_depth`](#isaaclab.utils.math.orthogonalize_perspective_depth)                              | 원근 깊이 이미지를 직교 깊이 이미지로 변환합니다.                                                   |
| [`unproject_depth`](#isaaclab.utils.math.unproject_depth)                                                              | 깊이 이미지를 포인트클라우드로 역투영합니다.                                                        |
| [`project_points`](#isaaclab.utils.math.project_points)                                                                | 3D 점을 2D 이미지 평면에 투영합니다.                                                                |
| [`default_orientation`](#isaaclab.utils.math.default_orientation)                                                      | 항등 회전 변환을 반환합니다.                                                                        |
| [`random_orientation`](#isaaclab.utils.math.random_orientation)                                                        | 3D에서의 샘플된 회전을 쿼터니언으로 반환합니다.                                                     |
| [`random_yaw_orientation`](#isaaclab.utils.math.random_yaw_orientation)                                                | z축 주변의 샘플된 회전을 반환합니다.                                                                |
| [`sample_triangle`](#isaaclab.utils.math.sample_triangle)(lower, upper, size, device)                                  | 삼각형 분포에서 텐서를 무작위로 샘플링합니다.                                                       |
| [`sample_uniform`](#isaaclab.utils.math.sample_uniform)(lower, upper, size, device)                                    | 범위 내에서 균등 분포를 샘플링합니다.                                                               |
| [`sample_log_uniform`](#isaaclab.utils.math.sample_log_uniform)(lower, upper, size, device)                            | 범위 내에서 로그균등 분포를 사용하여 샘플링합니다.                                               |
| [`sample_gaussian`](#isaaclab.utils.math.sample_gaussian)(mean, std, size, device)                                     | 가우시안 분포를 사용하여 샘플링합니다.                                                                 |
| [`sample_cylinder`](#isaaclab.utils.math.sample_cylinder)(radius, h_range, size, device)                               | 실린더 표면상에 3D 점을 균등하게 샘플링합니다.                                                 |
| [`convert_camera_frame_orientation_convention`](#isaaclab.utils.math.convert_camera_frame_orientation_convention)(...) | 한 회전 관습에서 다른 관습으로 변환하는 쿼터니언을 변환합니다.                       |
| [`create_rotation_matrix_from_view`](#isaaclab.utils.math.create_rotation_matrix_from_view)(eyes, targets)             | 세계 좌표계에서 뷰 좌표계로의 회전 행렬을 계산합니다.                                         |
| [`make_pose`](#isaaclab.utils.math.make_pose)(pos, rot)                                                                | 위치와 회전 행렬로부터 변환 행렬을 만듭니다.                               |
| [`unmake_pose`](#isaaclab.utils.math.unmake_pose)(pose)                                                                | 변환 행렬을 위치와 회전 행렬로 분할합니다.                                |
| [`pose_inv`](#isaaclab.utils.math.pose_inv)(pose)                                                                      | 변환 행렬의 역을 계산합니다.                                                    |
| [`pose_in_A_to_pose_in_B`](#isaaclab.utils.math.pose_in_A_to_pose_in_B)(pose_in_A, pose_A_in_B)                        | 한 좌표계에서의 포즈를 다른 좌표계의 포즈로 변환합니다.                                                |
| [`quat_slerp`](#isaaclab.utils.math.quat_slerp)(q1, q2, tau)                                                           | 두 쿼터니언 사이에서 구형 선형 보간(SLERP)을 수행합니다.                            |
| [`interpolate_rotations`](#isaaclab.utils.math.interpolate_rotations)(R1, R2, num_steps[, ...])                        | 두 회전 행렬 사이에 보간합니다.                                                         |
| [`interpolate_poses`](#isaaclab.utils.math.interpolate_poses)(pose_1, pose_2[, ...])                                   | 두 포즈 사이에 선형 보간을 수행합니다.                                                    |
| [`transform_poses_from_frame_A_to_frame_B`](#isaaclab.utils.math.transform_poses_from_frame_A_to_frame_B)(...)         | 상대적인 포즈를 보존하면서 한 좌표계에서 다른 좌표계로 포즈를 변환합니다.                    |
| [`generate_random_rotation`](#isaaclab.utils.math.generate_random_rotation)([rot_boundary])                            | 오일러 각을 사용하여 무작위 회전 행렬을 생성합니다.                                              |
| [`generate_random_translation`](#isaaclab.utils.math.generate_random_translation)([pos_boundary])                      | 무작위 변환 벡터를 생성합니다.                                                              |
| [`generate_random_transformation_matrix`](#isaaclab.utils.math.generate_random_transformation_matrix)([...])           | 회전과 변환을 결합하여 무작위 변환 행렬을 생성합니다.                        |

### isaaclab.utils.math.logger *= <Logger isaaclab.utils.math (WARNING)>*

일반

### isaaclab.utils.math.scale_transform(x: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), lower: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), upper: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

주어진 입력 텐서를 [-1, 1] 범위로 정규화합니다.

#### NOTE
배치된 입력을 처리하기 위해 PyTorch 브로드캐스팅 기능을 사용합니다.

* **Parameters:**
  * **x** – 형태가 (N, dims)인 입력 텐서.
  * **lower** – 텐서의 최솟값. 형태는 (N, dims) 또는 (dims,)일 수 있습니다.
  * **upper** – 텐서의 최댓값. 형태는 (N, dims) 또는 (dims,)일 수 있습니다.
* **Returns:**
  정규화된 텐서 변환. 형태는 (N, dims).

### isaaclab.utils.math.unscale_transform(x: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), lower: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), upper: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

주어진 입력 텐서를 [-1, 1] 범위에서 (lower, upper)로 비정규화합니다.

#### NOTE
배치된 입력을 처리하기 위해 PyTorch 브로드캐스팅 기능을 사용합니다.

* **Parameters:**
  * **x** – 형태가 (N, dims)인 입력 텐서.
  * **lower** – 텐서의 최솟값. 형태는 (N, dims) 또는 (dims,)일 수 있습니다.
  * **upper** – 텐서의 최댓값. 형태는 (N, dims) 또는 (dims,)일 수 있습니다.
* **Returns:**
  비정규화된 텐서 변환. 형태는 (N, dims).

### isaaclab.utils.math.saturate(x: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), lower: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), upper: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

주어진 입력 텐서를 (lower, upper) 범위로 클램프합니다.

배치된 입력을 처리하기 위해 PyTorch 브로드캐스팅 기능을 사용합니다.

* **Parameters:**
  * **x** – 형태가 (N, dims)인 입력 텐서.
  * **lower** – 텐서의 최솟값. 형태는 (N, dims) 또는 (dims,)일 수 있습니다.
  * **upper** – 텐서의 최댓값. 형태는 (N, dims) 또는 (dims,)일 수 있습니다.
* **Returns:**
  클램프된 텐서 변환. 형태는 (N, dims).

### isaaclab.utils.math.normalize(x: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), eps: [float](https://docs.python.org/3/library/functions.html#float) = 1e-09) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

주어진 입력 텐서를 단위 길이로 정규화합니다.

* **Parameters:**
  * **x** – 형태가 (N, dims)인 입력 텐서.
  * **eps** – 0으로 나누는 것을 방지하기 위한 작은 값. 기본값은 1e-9입니다.
* **Returns:**
  형태가 (N, dims)인 정규화된 텐서.

### isaaclab.utils.math.wrap_to_pi(angles: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

라디안 단위의 입력 각도를 [-π, π] 범위로 래핑합니다.

이 함수는 라디안 단위의 각도를 [-π, π] 범위로 래핑하여, π는 π로, -π는 -π로 매핑됩니다. 일반적으로,
양의 홀수 배의 π는 π로, 음의 홀수 배의 π는 -π로 매핑됩니다.

이 함수는 MATLAB의 [wrapToPi](https://www.mathworks.com/help/map/ref/wraptopi.html) 함수와 유사하게 동작합니다.

* **Parameters:**
  **angles** – 어떤 형태든 가능한 입력 각도.
* **Returns:**
  [-π, π] 범위의 각도.

### isaaclab.utils.math.copysign(mag: [float](https://docs.python.org/3/library/functions.html#float), other: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

입력의 크기와 other의 부호를 요소별로 가지는 새로운 부동소수점 텐서를 만듭니다.

#### NOTE
구현은 torch.copysign을 따릅니다. 이 함수는 스칼라 크기를 허용합니다.

* **Parameters:**
  * **mag** – 크기 스칼라.
  * **other** – 부호 비트가 크기에 적용되는 값을 포함하는 텐서.
* **Returns:**
  출력 텐서.

### isaaclab.utils.math.quat_unique(q: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

단위 쿼터니언을 실수부가 비음이 되는 표준 형태로 변환합니다.

쿼터니언 표현은 `q`와 `-q`가 같은 회전을 나타내기 때문에 특이점을 가집니다. 이 함수는 쿼터니언의 실수부가 비음이 되도록 보장합니다.

* **Parameters:**
  **q** – (w, x, y, z) 형식의 쿼터니언 방향. 형태는 (…, 4).
* **Returns:**
  표준화된 쿼터니언. 형태는 (…, 4).

### isaaclab.utils.math.matrix_from_quat(quaternions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

쿼터니언으로 표현된 회전을 회전 행렬로 변환합니다.

* **Parameters:**
  **quaternions** – (w, x, y, z) 형식의 쿼터니언 방향. 형태는 (…, 4).
* **Returns:**
  회전 행렬. 형태는 (…, 3, 3).

참고:
: [https://github.com/facebookresearch/pytorch3d/blob/main/pytorch3d/transforms/rotation_conversions.py#L41-L70](https://github.com/facebookresearch/pytorch3d/blob/main/pytorch3d/transforms/rotation_conversions.py#L41-L70)

### isaaclab.utils.math.convert_quat(quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | np.ndarray, to: Literal['xyzw', 'wxyz'] = 'xyzw') → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | np.ndarray

쿼터니언의 한 관습에서 다른 관습으로 변환합니다.

변환할 관습(TO)은 선택적 인수로 지정됩니다. to == 'xyzw'인 경우 입력이 'wxyz' 형식이라면, 그 반대의 경우도 마찬가지입니다.

* **Parameters:**
  * **quat** – 형태가 (…, 4)인 쿼터니언.
  * **to** – 쿼터니언을 변환할 관습. 기본값은 "xyzw"입니다.
* **Returns:**
  지정된 관습의 쿼터니언.
* **Raises:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 잘못된 입력 인수, 즉 “xyzw” 또는 “wxyz”가 아님.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 잘못된 입력 쿼터니언의 형태, 즉 (…, 4)가 아님.

### isaaclab.utils.math.quat_conjugate(q: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

쿼터니언의 켤레를 계산합니다.

* **매개변수:**
  **q** – (w, x, y, z) 순서의 쿼터니언 방향. 형태는 (…, 4).
* **반환값:**
  (w, x, y, z) 순서의 켤레 쿼터니언. 형태는 (…, 4).

### isaaclab.utils.math.quat_inv(q: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), eps: [float](https://docs.python.org/3/library/functions.html#float) = 1e-09) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

쿼터니언의 역을 계산합니다.

* **매개변수:**
  * **q** – (w, x, y, z) 순서의 쿼터니언 방향. 형태는 (N, 4).
  * **eps** – 0으로 나누는 것을 방지하기 위한 작은 값. 기본값은 1e-9.
* **반환값:**
  (w, x, y, z) 순서의 역 쿼터니언. 형태는 (N, 4).

### isaaclab.utils.math.quat_from_euler_xyz(roll: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), pitch: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), yaw: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

라디안 단위의 오일러 각도로 표현된 회전을 쿼터니언으로 변환합니다.

#### 참고
오일러 각도는 XYZ convention을 따릅니다고 가정합니다.

* **매개변수:**
  * **roll** – x축 주위 회전 (라디안 단위). 형태는 (N,).
  * **pitch** – y축 주위 회전 (라디안 단위). 형태는 (N,).
  * **yaw** – z축 주위 회전 (라디안 단위). 형태는 (N,).
* **반환값:**
  (w, x, y, z) 순서의 쿼터니언. 형태는 (N, 4).

### isaaclab.utils.math.quat_from_matrix(matrix: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

회전 행렬로 표현된 회전을 쿼터니언으로 변환합니다.

* **매개변수:**
  **matrix** – 회전 행렬. 형태는 (…, 3, 3).
* **반환값:**
  (w, x, y, z) 순서의 쿼터니언. 형태는 (…, 4).

참조:
: [https://github.com/facebookresearch/pytorch3d/blob/main/pytorch3d/transforms/rotation_conversions.py#L102-L161](https://github.com/facebookresearch/pytorch3d/blob/main/pytorch3d/transforms/rotation_conversions.py#L102-L161)

### isaaclab.utils.math.matrix_from_euler(euler_angles: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), convention: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

라디안 단위의 오일러 각도(고유)를 회전 행렬로 변환합니다.

* **매개변수:**
  * **euler_angles** – 라디안 단위의 오일러 각도. 형태는 (…, 3).
  * **convention** – {“X”, “Y”, “Z”} 집합의 세 개의 대문자로 구성된 convention 문자열.
    예를 들어, “XYZ”는 먼저 x축을 기준으로, 그 다음 y축을 기준으로, 마지막으로 z축을 기준으로 회전을 적용한다는 의미입니다.
* **반환값:**
  회전 행렬. 형태는 (…, 3, 3).

참조:
: [https://github.com/facebookresearch/pytorch3d/blob/main/pytorch3d/transforms/rotation_conversions.py#L194-L220](https://github.com/facebookresearch/pytorch3d/blob/main/pytorch3d/transforms/rotation_conversions.py#L194-L220)

### isaaclab.utils.math.euler_xyz_from_quat(quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), wrap_to_2pi: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

쿼터니언으로 표현된 회전을 라디안 단위의 오일러 각도로 변환합니다.

#### 참고
오일러 각도는 XYZ extrinsic convention을 따릅니다고 가정합니다.

* **매개변수:**
  * **quat** – (w, x, y, z) 순서의 쿼터니언 방향. 형태는 (N, 4).
  * **wrap_to_2pi** ([*bool*](https://docs.python.org/3/library/functions.html#bool)) – 출력 오일러 각도를 [0, 2π) 범위로 래핑할지 여부를 나타냅니다.
    False인 경우, 각도는 기본 범위 (−π, π]로 반환됩니다. 기본값은
    False입니다.
* **반환값:**
  롤-피치-요우를 포함하는 튜플. 각 요소는 형태가 (N,)인 텐서입니다.

참조:
: [https://en.wikipedia.org/wiki/Conversion_between_quaternions_and_Euler_angles](https://en.wikipedia.org/wiki/Conversion_between_quaternions_and_Euler_angles)

### isaaclab.utils.math.axis_angle_from_quat(quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), eps: [float](https://docs.python.org/3/library/functions.html#float) = 1e-06) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

쿼터니언으로 표현된 회전을 축/각도 형태로 변환합니다.

* **매개변수:**
  * **quat** – (w, x, y, z) 순서의 쿼터니언 방향. 형태는 (…, 4).
  * **eps** – 테일러 근사에 사용할 허용 오차. 기본값은 1.0e-6.
* **반환값:**
  축-각도 형태로 표현된 회전. 형태는 (…, 3).
  벡터의 크기는 해당 벡터의 방향을 기준으로 반시계 방향으로 회전한 각도(라디안)입니다.

참조:
: [https://github.com/facebookresearch/pytorch3d/blob/main/pytorch3d/transforms/rotation_conversions.py#L526-L554](https://github.com/facebookresearch/pytorch3d/blob/main/pytorch3d/transforms/rotation_conversions.py#L526-L554)

### isaaclab.utils.math.quat_from_angle_axis(angle: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), axis: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

축-각도로 표현된 회전을 쿼터니언으로 변환합니다.

* **매개변수:**
  * **angle** – 벡터의 방향을 기준으로 반시계 방향으로 회전한 각도(라디안). 형태는 (N,).
  * **axis** – 회전 축. 형태는 (N, 3).
* **반환값:**
  (w, x, y, z) 순서의 쿼터니언. 형태는 (N, 4).

### isaaclab.utils.math.quat_mul(q1: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), q2: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

두 쿼터니언을 곱합니다.

* **매개변수:**
  * **q1** – 첫 번째 쿼터니언 (w, x, y, z) 순서. 형태는 (…, 4).
  * **q2** – 두 번째 쿼터니언 (w, x, y, z) 순서. 형태는 (…, 4).
* **반환값:**
  두 쿼터니언의 곱 (w, x, y, z) 순서. 형태는 (…, 4).
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `q1`과 `q2`의 입력 형태가 일치하지 않음.

### isaaclab.utils.math.yaw_quat(quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

쿼터니언에서 요우 성분을 추출합니다.

* **매개변수:**
  **quat** – (w, x, y, z) 순서의 방향 쿼터니언. 형태는 (…, 4)
* **반환값:**
  요우 성분만 있는 쿼터니언.

### isaaclab.utils.math.quat_box_minus(q1: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), q2: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

두 쿼터니언 사이의 박스 마이너스 연산자(쿼터니언 차이)를 계산합니다.

* **매개변수:**
  * **q1** – 첫 번째 쿼터니언 (w, x, y, z) 순서. 형태는 (N, 4).
  * **q2** – 두 번째 쿼터니언 (w, x, y, z) 순서. 형태는 (N, 4).
* **반환값:**
  두 쿼터니언의 차이. 형태는 (N, 3).

참조:
: [https://github.com/ANYbotics/kindr/blob/master/doc/cheatsheet/cheatsheet_latest.pdf](https://github.com/ANYbotics/kindr/blob/master/doc/cheatsheet/cheatsheet_latest.pdf)

### isaaclab.utils.math.quat_box_plus(q: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), delta: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), eps: [float](https://docs.python.org/3/library/functions.html#float) = 1e-06) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

델타를 쿼터니언에 적용하여 쿼터니언을 업데이트하는 박스 플러스 연산자(쿼터니언 업데이트)를 수행합니다.

* **매개변수:**
  * **q** – 초기 쿼터니언 (w, x, y, z) 순서. 형태는 (N, 4).
  * **delta** – 축-각도 disturbance. 형태는 (N, 3).
    eps: 0으로 나누는 것을 방지하기 위한 작은 값. 기본값은 1e-6.
* **반환값:**
  disturbance를 적용한 후 업데이트된 쿼터니언. 형태는 (N, 4).

참조:
: [https://github.com/ANYbotics/kindr/blob/master/doc/cheatsheet/cheatsheet_latest.pdf](https://github.com/ANYbotics/kindr/blob/master/doc/cheatsheet/cheatsheet_latest.pdf)

### isaaclab.utils.math.quat_apply(quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), vec: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

쿼터니언 회전을 벡터에 적용합니다.

* **매개변수:**
  * **quat** – (w, x, y, z) 순서의 쿼터니언. 형태는 (…, 4).
  * **vec** – (x, y, z) 순서의 벡터. 형태는 (…, 3).
* **반환값:**
  회전된 벡터 (x, y, z) 순서. 형태는 (…, 3).

### isaaclab.utils.math.quat_apply_inverse(quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), vec: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

벡터에 역 쿼터니언 회전을 적용합니다.

* **매개변수:**
  * **quat** – (w, x, y, z) 형태의 쿼터니언. 차원은 (…, 4)입니다.
  * **vec** – (x, y, z) 형태의 벡터. 차원은 (…, 3)입니다.
* **반환값:**
  회전된 벡터 (x, y, z) 형태. 차원은 (…, 3)입니다.

### isaaclab.utils.math.quat_apply_yaw(quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), vec: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

벡터를 요 방향(Yaw) 주변으로만 회전합니다.

* **매개변수:**
  * **quat** – (w, x, y, z) 형태의 방향. 차원은 (N, 4)입니다.
  * **vec** – (x, y, z) 형태의 벡터. 차원은 (N, 3)입니다.
* **반환값:**
  회전된 벡터 (x, y, z) 형태. 차원은 (N, 3)입니다.

### isaaclab.utils.math.quat_rotate(q: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), v: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

q와 v의 마지막 차원을 따라 쿼터니언으로 벡터를 회전합니다.

<!-- deprecated v2.1.0:
이 함수는 향후 릴리스에서 더 빠른 구현인 :meth:`quat_apply` 로 대체되어 제거됩니다. -->
* **매개변수:**
  * **q** – (w, x, y, z) 형태의 쿼터니언. 차원은 (…, 4)입니다.
  * **v** – (x, y, z) 형태의 벡터. 차원은 (…, 3)입니다.
* **반환값:**
  회전된 벡터 (x, y, z) 형태. 차원은 (…, 3)입니다.

### isaaclab.utils.math.quat_rotate_inverse(q: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), v: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

q와 v의 마지막 차원을 따라 쿼터니언의 역으로 벡터를 회전합니다.

<!-- deprecated v2.1.0:
이 함수는 향후 릴리스에서 더 빠른 구현인
:meth:`quat_apply_inverse` 로 대체되어 제거됩니다. -->
* **매개변수:**
  * **q** – (w, x, y, z) 형태의 쿼터니언. 차원은 (…, 4)입니다.
  * **v** – (x, y, z) 형태의 벡터. 차원은 (…, 3)입니다.
* **반환값:**
  회전된 벡터 (x, y, z) 형태. 차원은 (…, 3)입니다.

### isaaclab.utils.math.quat_error_magnitude(q1: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), q2: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

두 쿼터니언 사이의 회전 차이를 계산합니다.

* **매개변수:**
  * **q1** – (w, x, y, z) 형태의 첫 번째 쿼터니언. 차원은 (…, 4)입니다.
  * **q2** – (w, x, y, z) 형태의 두 번째 쿼터니언. 차원은 (…, 4)입니다.
* **반환값:**
  입력 쿼터니언 사이의 각도 오차 (라디안 단위).

### isaaclab.utils.math.skew_symmetric_matrix(vec: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

벡터의 대칭 반전 행렬을 계산합니다.

* **매개변수:**
  **vec** – 입력 벡터. 차원은 (3,) 또는 (N, 3)입니다.
* **반환값:**
  대칭 반전 행렬. 차원은 (1, 3, 3) 또는 (N, 3, 3)입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 텐서가 shape (…, 3)이 아닐 경우 발생합니다.

### isaaclab.utils.math.is_identity_pose(pos: torch.tensor, rot: torch.tensor) → [bool](https://docs.python.org/3/library/functions.html#bool)

입력 포즈가 항등 변환인지 확인합니다.

이 함수는 L2-노름을 사용하여 입력 위치가 영점에 가까운지, 방향이 항등인지 확인합니다. 방향의 오차는 확인하지 않습니다.

* **매개변수:**
  * **pos** – 데카르트 위치. 차원은 (N, 3)입니다.
  * **rot** – (w, x, y, z) 형태의 쿼터니언. 차원은 (N, 4)입니다.
* **반환값:**
  모든 입력 포즈가 항등 변환을 결과로 내면 True, 그렇지 않으면 False.

### isaaclab.utils.math.combine_frame_transforms(t01: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), q01: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), t12: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, q12: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

두 참조 프레임 간의 변환을 정지 프레임으로 결합합니다.

다음 변환 연산을 수행합니다: $T_{02} = T_{01} \times T_{12}$,
여기서 $T_{AB}$는 프레임 A에서 프레임 B로의 동차 변환 행렬입니다.

* **매개변수:**
  * **t01** – 프레임 0을 기준으로 한 프레임 1의 위치. 차원은 (N, 3)입니다.
  * **q01** – 프레임 0을 기준으로 한 프레임 1의 방향 (w, x, y, z). 차원은 (N, 4)입니다.
  * **t12** – 프레임 1을 기준으로 한 프레임 2의 위치. 차원은 (N, 3)입니다.
    기본값은 None이며, 이 경우 위치는 0으로 간주됩니다.
  * **q12** – 프레임 1을 기준으로 한 프레임 2의 방향 (w, x, y, z). 차원은 (N, 4)입니다.
    기본값은 None이며, 이 경우 방향은 항등으로 간주됩니다.
* **반환값:**
  프레임 0을 기준으로 한 프레임 2의 위치와 방향을 포함하는 튜플.
  텐서의 차원은 각각 (N, 3)과 (N, 4)입니다.

### isaaclab.utils.math.rigid_body_twist_transform(v0: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), w0: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), t01: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), q01: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

참조 프레임 사이에서 강체의 선형 및 각속도를 변환합니다.

프레임 0을 기준으로 한 프레임 0의 트위스트가 주어졌을 때, 이 함수는 프레임 0을 기준으로 한 프레임 1의 위치와 방향으로부터 프레임 1을 기준으로 한 프레임 1의 트위스트를 계산합니다. 변환은 다음 방정식을 따릅니다:

$$
w_{11} = R_{10} w_{00} = R_{01}^{-1} w_{00}
v_{11} = R_{10} v_{00} + R_{10} (w_{00} \times t_{01}) = R_{01}^{-1} (v_{00} + (w_{00} \times t_{01}))
$$

여기서

> - $R_{01}$은 쿼터니언 $q_{01}$에서 유도된 프레임 0에서 프레임 1로의 회전 행렬입니다.
> - $t_{01}$은 프레임 0에서 표현된 프레임 1의 위치(프레임 0 기준)입니다.
> - $w_0$은 프레임 0에서의 프레임 0의 각속도입니다.
> - $v_0$은 프레임 0에서의 프레임 0의 선속도입니다.
* **매개변수:**
  * **v0** – 프레임 0에서의 프레임 0의 선속도. 차원은 (N, 3)입니다.
  * **w0** – 프레임 0에서의 프레임 0의 각속도. 차원은 (N, 3)입니다.
  * **t01** – 프레임 0을 기준으로 한 프레임 1의 위치. 차원은 (N, 3)입니다.
  * **q01** – 프레임 0을 기준으로 한 프레임 1의 방향 (w, x, y, z). 차원은 (N, 4)입니다.
* **반환값:**
  - 프레임 1에서의 변환된 선속도. 차원은 (N, 3)입니다.
  - 프레임 1에서의 변환된 각속도. 차원은 (N, 3)입니다.
* **반환 유형:**
  프레임 1에서의 선속도와 각속도를 포함하는 튜플

### isaaclab.utils.math.subtract_frame_transforms(t01: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), q01: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), t02: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, q02: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

두 참조 프레임 간의 변환을 정지 프레임으로 뺍니다.

다음 변환 연산을 수행합니다: $T_{12} = T_{01}^{-1} \times T_{02}$,
여기서 $T_{AB}$는 프레임 A에서 프레임 B로의 동차 변환 행렬입니다.

* **매개변수:**
  * **t01** – 프레임 0을 기준으로 한 프레임 1의 위치. 차원은 (N, 3)입니다.
  * **q01** – 프레임 0을 기준으로 한 프레임 1의 방향 (w, x, y, z). 차원은 (N, 4)입니다.
  * **t02** – 프레임 0을 기준으로 한 프레임 2의 위치. 차원은 (N, 3)입니다.
    기본값은 None이며, 이 경우 위치는 0으로 간주됩니다.
  * **q02** – 프레임 0을 기준으로 한 프레임 2의 방향 (w, x, y, z). 차원은 (N, 4)입니다.
    기본값은 None이며, 이 경우 방향은 항등으로 간주됩니다.
* **반환값:**
  프레임 1을 기준으로 한 프레임 2의 위치와 방향을 포함하는 튜플.
  텐서의 차원은 각각 (N, 3)과 (N, 4)입니다.

### isaaclab.utils.math.compute_pose_error(t01: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), q01: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), t02: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), q02: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), rot_error_type: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['quat', 'axis_angle'] = 'axis_angle') → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

소스 프레임과 타겟 프레임 사이의 위치 및 방향 오차를 계산합니다.

* **매개변수:**
  * **t01** – 소스 프레임의 위치. 형태는 (N, 3)입니다.
  * **q01** – 소스 프레임의 쿼터니언 방향 (w, x, y, z). 형태는 (N, 4)입니다.
  * **t02** – 타겟 프레임의 위치. 형태는 (N, 3)입니다.
  * **q02** – 타겟 프레임의 쿼터니언 방향 (w, x, y, z). 형태는 (N, 4)입니다.
  * **rot_error_type** – 반환할 방향 오차 유형: “quat”, “axis_angle”. 기본값은 “axis_angle”입니다.
* **반환값:**
  위치 및 방향 오차를 포함하는 튜플입니다. 위치 오차의 형태는 (N, 3)입니다.
  방향 오차의 형태는 `rot_error_type`의 값에 따라 달라집니다:
  - `rot_error_type`이 “quat”이면, 방향 오차가 쿼터니언으로 반환됩니다. 형태는 (N, 4)입니다.
  - `rot_error_type`이 “axis_angle”이면, 방향 오차가 축-각도 벡터로 반환됩니다. 형태는 (N, 3)입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 유효하지 않은 방향 오차 유형입니다.

### isaaclab.utils.math.apply_delta_pose(source_pos: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), source_rot: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), delta_pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), eps: [float](https://docs.python.org/3/library/functions.html#float) = 1e-06) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

소스 포즈에 델타 포즈 변환을 적용합니다.

delta_pose의 첫 세 요소는 데카르트 위치 변위로 해석됩니다.
delta_pose의 나머지 세 요소는 각도-축 형식으로 된 방향 변위로 해석됩니다.

* **매개변수:**
  * **source_pos** – 소스 프레임의 위치. 형태는 (N, 3)입니다.
  * **source_rot** – 소스 프레임의 쿼터니언 방향 (w, x, y, z). 형태는 (N, 4)입니다.
  * **delta_pose** – 위치 및 방향 변위. 형태는 (N, 6)입니다.
  * **eps** – 방향 변위를 0으로 간주할 허용 오차. 기본값은 1.0e-6입니다.
* **반환값:**
  이동된 위치 및 방향 프레임을 포함하는 튜플입니다. 각각의 형태는 (N, 3) 및 (N, 4)입니다.

### isaaclab.utils.math.transform_points(points: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), pos: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, quat: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

주어진 프레임의 입력 점을 타겟 프레임으로 변환합니다.

이 함수는 소스 프레임에서 타겟 프레임으로 점을 변환합니다. 변환은 소스 프레임에서 타겟 프레임의 위치 $t$ 및 방향 $R$에 의해 정의됩니다.

$$
p_{target} = R_{target} \times p_{source} + t_{target}

$$

입력 점이 점의 배치인 경우, 입력 `pos`와 `quat`는 위치와 쿼터니언의 배치 또는 단일 위치와 쿼터니언이어야 합니다. 입력 `pos`와 `quat`가 단일 위치와 쿼터니언인 경우, 배치의 모든 점에 동일한 변환이 적용됩니다.

입력 `pos`와 `quat` 중 하나가 None인 경우, 해당 변환은 적용되지 않습니다.

* **매개변수:**
  * **points** – 변환할 점. 형태는 (N, P, 3) 또는 (P, 3)입니다.
  * **pos** – 타겟 프레임의 위치. 형태는 (N, 3) 또는 (3,)입니다. 기본값은 None이며, 이 경우 위치는 0으로 간주됩니다.
  * **quat** – 타겟 프레임의 쿼터니언 방향 (w, x, y, z). 형태는 (N, 4) 또는 (4,)입니다. 기본값은 None이며, 이 경우 방향은 항등 행렬로 간주됩니다.
* **반환값:**
  타겟 프레임의 변환된 점. 형태는 (N, P, 3) 또는 (P, 3)입니다.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 `points`의 형태가 (N, P, 3) 또는 (P, 3)이 아닙니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 `pos`의 형태가 (N, 3) 또는 (3,)가 아닙니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 `quat`의 형태가 (N, 4) 또는 (4,)가 아닙니다.

### isaaclab.utils.math.orthogonalize_perspective_depth(depth: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), intrinsics: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

원근 깊이 이미지를 직교 깊이 이미지로 변환합니다.

원근 깊이 이미지는 카메라의 광학 중심으로부터 측정된 거리를 포함합니다.
반면 직교 깊이 이미지는 카메라의 이미지 평면으로부터의 거리를 제공합니다.
이 메서드는 카메라 기하학을 사용하여 원근 깊이를 직교 깊이 이미지로 변환합니다.

이 함수는 너비와 높이가 모두 1보다 크다고 가정합니다.

* **매개변수:**
  * **depth** – 원근 깊이 이미지. 형태는 (H, W), (H, W, 1), (N, H, W), 또는 (N, H, W, 1)입니다.
  * **intrinsics** – 카메라의 보정 행렬. 단일 행렬이 제공되는 경우, 동일한 보정 행렬이 배치의 모든 깊이 이미지에 사용됩니다.
    형태는 (3, 3) 또는 (N, 3, 3)입니다.
* **반환값:**
  직교 깊이 이미지. 형태는 입력 깊이 이미지의 형태와 일치합니다.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 깊이가 (H, W), (H, W, 1), (N, H, W), 또는 (N, H, W, 1) 형태가 아닙니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 보정 행렬이 (3, 3) 또는 (N, 3, 3) 형태가 아닙니다.

### isaaclab.utils.math.unproject_depth(depth: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), intrinsics: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), is_ortho: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

깊이 이미지를 포인트 클라우드로 역투영합니다.

이 함수는 카메라의 보정 행렬을 주어 직교 또는 원근 깊이 이미지를 점으로 변환합니다. 다음 변환을 카메라 기하학을 기반으로 사용합니다:

$$
p_{3D} = K^{-1} \times [u, v, 1]^T \times d

$$

여기서 $p_{3D}$는 3D 점, $d$는 깊이 값(이미지 평면으로부터의 거리), $u$ 및 $v$는 픽셀 좌표, $K$는 내부 행렬입니다.

이 함수는 너비와 높이가 모두 1보다 크다고 가정합니다. 이는 다양한 형태의 깊이 이미지와 보정 행렬을 처리할 수 있게 합니다.

#### 참고
`is_ortho`가 False인 경우, 입력 깊이 이미지는 [`orthogonalize_perspective_depth()`](#isaaclab.utils.math.orthogonalize_perspective_depth) 메서드를 사용하여 직교 깊이 이미지로 변환됩니다.

* **매개변수:**
  * **depth** – 깊이 측정값. 형태는 (H, W), (H, W, 1), (N, H, W), 또는 (N, H, W, 1)입니다.
  * **intrinsics** – 카메라의 보정 행렬. 단일 행렬이 제공되는 경우, 동일한 보정 행렬이 배치의 모든 깊이 이미지에 사용됩니다.
    형태는 (3, 3) 또는 (N, 3, 3)입니다.
  * **is_ortho** – 입력 깊이 이미지가 직교형인지 원근형인지 여부. True이면, 입력 깊이 이미지는 카메라의 이미지 평면으로부터 측정이 이루어진 *직교* 유형으로 간주됩니다. False이면, 깊이 이미지는 카메라의 광학 중심으로부터 측정이 이루어진 *원근* 유형으로 간주됩니다. 기본값은 True입니다.
* **반환값:**
  점의 3D 좌표. 형태는 (P, 3) 또는 (N, P, 3)입니다.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 깊이가 (H, W), (H, W, 1), (N, H, W), 또는 (N, H, W, 1) 형태가 아닙니다.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 보정 행렬이 (3, 3) 또는 (N, 3, 3) 형태가 아닙니다.

### isaaclab.utils.math.project_points(points: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), intrinsics: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

3D 점을 2D 이미지 평면에 투영합니다.

이 함수는 3D 점을 카메라의 내부 행렬에 의해 정의된 변환을 통해 2D 이미지 평면에 투영합니다.

$$
\begin{align}
    p &= K \times p_{3D}  = \\
    p_{2D} &= \begin{pmatrix} u \\ v \\  d \end{pmatrix}
            = \begin{pmatrix} p[0] / p[2] \\  p[1] / p[2] \\ Z \end{pmatrix}
\end{align}
$$

여기서 $p_{2D} = (u, v, d)$는 투영된 3D 점, $p_{3D} = (X, Y, Z)$는 3D 점, $K \in \mathbb{R}^{3 \times 3}$는 내부 행렬입니다.

점들이 3D 점의 배치이고 내부 행렬이 단일인 경우, 동일한 보정 행렬이 배치의 모든 점에 적용됩니다.

* **매개변수:**
  * **points** – 3D 점의 좌표. 형태는 (P, 3) 또는 (N, P, 3)입니다.
  * **intrinsics** – 카메라의 보정 행렬. 형태는 (3, 3) 또는 (N, 3, 3)입니다.
* **Returns:**
  점의 투영된 3D 좌표. 형태는 (P, 3) 또는 (N, P, 3)입니다.

### isaaclab.utils.math.default_orientation(num: [int](https://docs.python.org/3/library/functions.html#int), device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

항등 회전 변환을 반환합니다.

* **Parameters:**
  * **num** – 샘플링할 회전의 개수.
  * **device** – 텐서를 생성할 장치.
* **Returns:**
  (w, x, y, z) 형태의 항등 사원수. 형태는 (num, 4)입니다.

### isaaclab.utils.math.random_orientation(num: [int](https://docs.python.org/3/library/functions.html#int), device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

3D 회전을 사원수 형태로 샘플링하여 반환합니다.

* **Parameters:**
  * **num** – 샘플링할 회전의 개수.
  * **device** – 텐서를 생성할 장치.
* **Returns:**
  (w, x, y, z) 형태의 샘플링된 사원수. 형태는 (num, 4)입니다.

Reference:
: [https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.transform.Rotation.random.html](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.transform.Rotation.random.html)

### isaaclab.utils.math.random_yaw_orientation(num: [int](https://docs.python.org/3/library/functions.html#int), device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

z축을 중심으로 한 회전을 사원수 형태로 샘플링하여 반환합니다.

* **Parameters:**
  * **num** – 샘플링할 회전의 개수.
  * **device** – 텐서를 생성할 장치.
* **Returns:**
  (w, x, y, z) 형태의 샘플링된 사원수. 형태는 (num, 4)입니다.

### isaaclab.utils.math.sample_triangle(lower: [float](https://docs.python.org/3/library/functions.html#float), upper: [float](https://docs.python.org/3/library/functions.html#float), size: [int](https://docs.python.org/3/library/functions.html#int) | [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), ...], device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

삼각형 분포에서 텐서를 랜덤하게 샘플링합니다.

* **Parameters:**
  * **lower** – 샘플링할 텐서의 하한 범위.
  * **upper** – 샘플링할 텐서의 상한 범위.
  * **size** – 텐서의 형태.
  * **device** – 텐서를 생성할 장치.
* **Returns:**
  샘플링된 텐서. 형태는 `size`에 따라 결정됩니다.

### isaaclab.utils.math.sample_uniform(lower: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), upper: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), size: [int](https://docs.python.org/3/library/functions.html#int) | [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), ...], device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

범위 내에서 균일하게 샘플링합니다.

* **Parameters:**
  * **lower** – 균일 범위의 하한.
  * **upper** – 균일 범위의 상한.
  * **size** – 텐서의 형태.
  * **device** – 텐서를 생성할 장치.
* **Returns:**
  샘플링된 텐서. 형태는 `size`에 따라 결정됩니다.

### isaaclab.utils.math.sample_log_uniform(lower: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), upper: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), size: [int](https://docs.python.org/3/library/functions.html#int) | [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), ...], device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

범위 내에서 로그-균일 분포를 사용하여 샘플링합니다.

로그-균일 분포는 로그 공간에서의 균일 분포로 정의됩니다. 여러 차원의 값을 샘플링할 때 유용합니다. 샘플링된 값은 로그 공간에서 균일하게 분포되고, 그 후에 지수 변환을 적용하여 최종 값을 얻습니다.

$$
x = \exp(\text{uniform}(\log(\text{lower}), \log(\text{upper})))
$$

* **Parameters:**
  * **lower** – 균일 범위의 하한.
  * **upper** – 균일 범위의 상한.
  * **size** – 텐서의 형태.
  * **device** – 텐서를 생성할 장치.
* **Returns:**
  샘플링된 텐서. 형태는 `size`에 따라 결정됩니다.

### isaaclab.utils.math.sample_gaussian(mean: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), std: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float), size: [int](https://docs.python.org/3/library/functions.html#int) | [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), ...], device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

가우시안 분포를 사용하여 샘플링합니다.

* **Parameters:**
  * **mean** – 가우시안의 평균.
  * **std** – 가우시안의 표준편차.
  * **size** – 텐서의 형태.
  * **device** – 텐서를 생성할 장치.
* **Returns:**
  샘플링된 텐서.

### isaaclab.utils.math.sample_cylinder(radius: [float](https://docs.python.org/3/library/functions.html#float), h_range: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float), [float](https://docs.python.org/3/library/functions.html#float)], size: [int](https://docs.python.org/3/library/functions.html#int) | [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[int](https://docs.python.org/3/library/functions.html#int), ...], device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

실린더 표면에 3D 점을 균일하게 샘플링합니다.

실린더는 원점에 중심을 두고 z축과 일치합니다. 실린더의 높이는 범위 `h_range`에서 균일하게 샘플링되며, 반지름은 `radius`로 고정됩니다.

샘플링된 점은 `(*size, 3)` 형태의 텐서로 반환되며, 마지막 차원은 샘플링된 점의 x, y, z 좌표를 포함합니다.

* **Parameters:**
  * **radius** – 실린더의 반지름.
  * **h_range** – 실린더의 최소 및 최대 높이.
  * **size** – 텐서의 형태.
  * **device** – 텐서를 생성할 장치.
* **Returns:**
  샘플링된 텐서. 형태는 `(*size, 3)`입니다.

### isaaclab.utils.math.convert_camera_frame_orientation_convention(orientation: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), origin: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['opengl', 'ros', 'world'] = 'opengl', target: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['opengl', 'ros', 'world'] = 'ros') → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

하나의 convention에서 다른 convention으로 회전을 나타내는 사원수를 변환합니다.

USD에서 카메라는 `"opengl"` convention을 따릅니다. 따라서 항상 **Y-up** convention을 따릅니다. 즉, 카메라는 -Z 축을 바라보고 있으며, +Y 축은 위쪽을 가리키고, +X 축은 오른쪽을 가리킵니다.
그러나 ROS에서는 카메라는 +Z 축을 바라보고 있으며, +Y 축은 아래쪽을 가리키고, +X 축은 오른쪽을 가리킵니다.
따라서 카메라는 ROS convention을 따르기 위해 X 축을 기준으로 $180^{\circ}$ 회전해야 합니다.

$$
T_{ROS} =
    \begin{bmatrix}
        1 & 0 & 0 & 0 \\ 0 & -1 & 0 & 0 \\ 0 & 0 & -1 & 0 \\ 0 & 0 & 0 & 1
    \end{bmatrix} T_{USD}
$$

반면에 일반적인 월드 좌표계는 +X가 앞쪽을, +Y가 왼쪽을, +Z가 위쪽을 가리킵니다. 카메라는 X 축을 기준으로 $90^{\circ}$ 회전하고 Y 축을 기준으로 $-90^{\circ}$ 회전함으로써 월드 프레임 convention을 따를 수 있습니다.

$$
T_{WORLD} =
    \begin{bmatrix}
        0 & 0 & -1 & 0 \\ -1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 0 & 1
    \end{bmatrix} T_{USD}
$$

따라서 애플리케이션에 따라 카메라는 서로 다른 convention을 따릅니다. 이 함수는 한 convention의 사원수를 다른 convention의 사원수로 변환합니다.

가능한 convention은 다음과 같습니다:

- `"opengl"` - 전방 축: -Z - 상방 축: +Y - 오프셋은 OpenGL (Usd.Camera) convention에 적용됨
- `"ros"`    - 전방 축: +Z - 상방 축: -Y - 오프셋은 ROS convention에 적용됨
- `"world"`  - 전방 축: +X - 상방 축: +Z - 오프셋은 World Frame convention에 적용됨

* **Parameters:**
  * **orientation** – 소스 convention에서의 (w, x, y, z) 형태의 사원수. 형태는 (…, 4)입니다.
  * **origin** – 변환할 원본 convention. 기본값은 “opengl”입니다.
  * **target** – 변환할 대상 convention. 기본값은 “ros”입니다.
* **Returns:**
  대상 convention에서의 (w, x, y, z) 형태의 사원수. 형태는 (…, 4)입니다.

### isaaclab.utils.math.create_rotation_matrix_from_view(eyes: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), targets: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), up_axis: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['Y', 'Z'] = 'Z', device: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'cpu') → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

월드 좌표에서 뷰 좌표로의 회전 행렬을 계산합니다.

이 함수는 월드 좌표에서 카메라의 위치를 지정하는 벡터 ‘eyes’와 개체의 위치를 나타내는 벡터 ‘targets’를 입력으로 받습니다.
출력은 월드 좌표 -> 뷰 좌표 변환을 나타내는 회전 행렬입니다.

> eyes와 targets 입력은 각각 다음 형태일 수 있습니다:
> - 3개의 요소를 가진 튜플/리스트
> - shape (1, 3)의 torch 텐서
> - shape (N, 3)의 torch 텐서
* **매개변수:**
  * **eyes** – 월드 좌표에서의 카메라 위치.
  * **targets** – 월드 좌표에서의 객체 위치.
  * **up_axis** – 카메라의 업 축. 기본값은 “Z”입니다.
  * **device** – torch 텐서를 생성할 장치. 기본값은 “cpu”입니다.

벡터들은 브로드캐스팅되어 모두 shape (N, 3)이 됩니다.

* **반환값:**
  (N, 3, 3) 형태의 배치 회전 행렬
* **반환 유형:**
  R

참고:
PyTorch3D 기반 ([https://github.com/facebookresearch/pytorch3d/blob/eaf0709d6af0025fe94d1ee7cec454bc3054826a/pytorch3d/renderer/cameras.py#L1635-L1685](https://github.com/facebookresearch/pytorch3d/blob/eaf0709d6af0025fe94d1ee7cec454bc3054826a/pytorch3d/renderer/cameras.py#L1635-L1685))

### isaaclab.utils.math.make_pose(pos: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), rot: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

위치와 회전 행렬로부터 변환 행렬을 생성합니다.

* **매개변수:**
  * **pos** – 마지막 차원이 3인 위치 벡터의 배치.
  * **rot** – 마지막 두 차원이 (3, 3)인 회전 행렬의 배치.
* **반환값:**
  마지막 두 차원이 (4, 4)인 포즈 행렬의 배치.

### isaaclab.utils.math.unmake_pose(pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

변환 행렬을 위치와 회전 행렬로 분할합니다.

* **매개변수:**
  **pose** – 마지막 두 차원이 (4, 4)인 포즈 행렬의 배치.
* **반환값:**
  - 마지막 차원이 3인 위치 벡터의 배치.
  - 마지막 두 차원이 (3, 3)인 회전 행렬의 배치.
* **반환 유형:**
  위치 텐서와 회전 텐서를 포함하는 튜플

### isaaclab.utils.math.pose_inv(pose: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

변환 행렬의 역을 계산합니다.

포즈 행렬 [R t; 0 1]의 역은 [R.T -R.T\*t; 0 1]입니다.

* **매개변수:**
  **pose** – 마지막 두 차원이 (4, 4)인 포즈 행렬의 배치.
* **반환값:**
  마지막 두 차원이 (4, 4)인 역 포즈 행렬의 배치.

### isaaclab.utils.math.pose_in_A_to_pose_in_B(pose_in_A: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), pose_A_in_B: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

한 좌표계에서 다른 좌표계로 포즈를 변환합니다.

프레임 A에 있는 점 C를 나타내는 변환 행렬을 프레임 B에 있는 동일한 점 C를 나타내는 행렬로 변환합니다.

예시 사용법:

frame_C_in_B = pose_in_A_to_pose_in_B(frame_C_in_A, frame_A_in_B)

* **매개변수:**
  * **pose_in_A** – 프레임 A에서의 점 C를 나타내는 변환 행렬의 배치.
  * **pose_A_in_B** – 프레임 A를 프레임 B에서 나타내는 변환 행렬의 배치.
* **반환값:**
  프레임 B에서의 점 C를 나타내는 변환 행렬의 배치.

### isaaclab.utils.math.quat_slerp(q1: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), q2: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), tau: [float](https://docs.python.org/3/library/functions.html#float)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

두 쿼터니언 사이의 구형 선형 보간(SLERP)을 수행합니다.

이 함수는 배치 처리를 지원하지 않습니다.

* **매개변수:**
  * **q1** – (w, x, y, z) 형식의 첫 번째 쿼터니언.
  * **q2** – (w, x, y, z) 형식의 두 번째 쿼터니언.
  * **tau** – q1(0)에서 q2(1) 사이의 보간 계수.
* **반환값:**
  (w, x, y, z) 형식의 보간된 쿼터니언.

### isaaclab.utils.math.interpolate_rotations(R1: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), R2: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), num_steps: [int](https://docs.python.org/3/library/functions.html#int), axis_angle: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

두 회전 행렬 사이의 보간을 수행합니다.

* **매개변수:**
  * **R1** – 첫 번째 회전 행렬. (4x4).
  * **R2** – 두 번째 회전 행렬. (4x4).
  * **num_steps** – 시작과 끝을 제외한 원하는 보간된 회전의 수.
  * **axis_angle** – True이면 축-각도 표현에서 보간하고, 그렇지 않으면 slerp를 사용합니다. 기본값은 True입니다.
* **반환값:**
  시작 및 끝 회전을 포함한 shape (num_steps + 1, 4, 4)의 보간된 회전 행렬 스택.

### isaaclab.utils.math.interpolate_poses(pose_1: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), pose_2: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), num_steps: [int](https://docs.python.org/3/library/functions.html#int) = None, step_size: [float](https://docs.python.org/3/library/functions.html#float) = None, perturb: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [int](https://docs.python.org/3/library/functions.html#int)]

두 포즈 사이의 선형 보간을 수행합니다.

* **매개변수:**
  * **pose_1** – 4x4 시작 포즈.
  * **pose_2** – 4x4 끝 포즈.
  * **num_steps** – 제공된 경우, 원하는 보간 점의 수를 지정합니다. 0을 전달하면 보간이 수행되지 않습니다. None인 경우 step_size를 제공해야 합니다.
  * **step_size** – 제공된 경우, 포즈 사이의 거리에 따라 단계 수를 결정합니다.
  * **perturb** – True이면 보간된 위치 점에 무작위 노이즈를 추가합니다.
* **반환값:**
  - shape (N + 2, 4, 4)의 보간된 포즈 경로에 해당하는 배열.
  - 경로 내 보간 점의 수(N).
* **반환 유형:**
  포즈 배열과 정수 값을 포함하는 튜플

### isaaclab.utils.math.transform_poses_from_frame_A_to_frame_B(src_poses: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), frame_A: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), frame_B: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

상대적인 포즈를 유지하면서 한 좌표계에서 다른 좌표계로 포즈를 변환합니다.

* **매개변수:**
  * **src_poses** – 소스 시연에서의 입력 포즈 시퀀스 (shape [T, 4, 4]).
  * **frame_A** – 4x4 프레임 A 포즈.
  * **frame_B** – 4x4 프레임 B 포즈.
* **반환값:**
  shape [T, 4, 4]의 변환된 포즈 시퀀스.

### isaaclab.utils.math.generate_random_rotation(rot_boundary: [float](https://docs.python.org/3/library/functions.html#float) = 6.283185307179586) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

오일러 각을 사용하여 무작위 회전 행렬을 생성합니다.

* **매개변수:**
  **rot_boundary** – 각 축(x, y, z) 주위의 무작위 회전 각도 범위.
* **반환값:**
  3x3 회전 행렬.

### isaaclab.utils.math.generate_random_translation(pos_boundary: [float](https://docs.python.org/3/library/functions.html#float) = 1) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

무작위 이동 벡터를 생성합니다.

* **매개변수:**
  **pos_boundary** – 3D 공간에서의 무작위 이동 값 범위.
* **반환값:**
  3요소 이동 벡터.

### isaaclab.utils.math.generate_random_transformation_matrix(pos_boundary: [float](https://docs.python.org/3/library/functions.html#float) = 1, rot_boundary: [float](https://docs.python.org/3/library/functions.html#float) = 6.283185307179586) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

회전과 이동을 결합한 무작위 변환 행렬을 생성합니다.

* **매개변수:**
  * **pos_boundary** – 무작위 이동 값의 범위.
  * **rot_boundary** – 무작위 회전 각도의 범위.
* **반환값:**
  4x4 변환 행렬.

## Mesh operations

메시 작업용 유틸리티 함수입니다.

**함수:**

| [`create_trimesh_from_geom_mesh`](#isaaclab.utils.mesh.create_trimesh_from_geom_mesh)(mesh_prim)     | 메시 프리미브의 정점과 면을 읽습니다.                |
|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------|
| [`create_trimesh_from_geom_shape`](#isaaclab.utils.mesh.create_trimesh_from_geom_shape)(prim)        | 프리미브 개체를 트라이메시로 변환합니다.                   |
| [`convert_faces_to_triangles`](#isaaclab.utils.mesh.convert_faces_to_triangles)(faces, point_counts) | 사각형 메시 면 인덱스를 삼각형 메시 면 인덱스로 변환합니다. |

**데이터:**

| [`PRIMITIVE_MESH_TYPES`](#isaaclab.utils.mesh.PRIMITIVE_MESH_TYPES)   | 트라이메시로 변환할 수 있는 지원되는 프리미티브 메시 타입 목록입니다.   |
|-----------------------------------------------------------------------|------------------------------------------------------------------------------|

### isaaclab.utils.mesh.create_trimesh_from_geom_mesh(mesh_prim: pxr.Usd.Prim) → [trimesh.Trimesh](https://trimesh.org/trimesh.html#trimesh.Trimesh)

메시 프림의 버텍스와 면을 읽습니다.

이 함수는 메시 프림의 버텍스와 면을 읽고 반환합니다. 기본 메시가 사각형 메시인 경우, 삼각형 메시로 변환합니다.

* **매개변수:**
  **mesh_prim** – 버텍스와 면을 읽을 메시 프림입니다.
* **반환값:**
  메시 기하학을 포함하는 trimesh.Trimesh 객체입니다.

### isaaclab.utils.mesh.create_trimesh_from_geom_shape(prim: pxr.Usd.Prim) → [trimesh.Trimesh](https://trimesh.org/trimesh.html#trimesh.Trimesh)

프리미티브 객체를 트라이메시로 변환합니다.

* **매개변수:**
  **prim** – 트라이메시로 변환할 프리미티브 프림입니다.
* **반환값:**
  프리미티브를 나타내는 트라이메시 객체입니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 프림이 지원되는 프리미티브가 아닌 경우입니다. 지원되는 프리미티브 목록은 PRIMITIVE_MESH_TYPES를 확인하세요.

### isaaclab.utils.mesh.convert_faces_to_triangles(faces: [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray), point_counts: [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray)) → [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray)

사각형 메시 면 인덱스를 삼각형 메시 면 인덱스로 변환합니다.

이 함수는 면(인덱스) 배열과 각 면의 점 개수 배열을 입력으로 기대합니다. 사각형을 삼각형으로 변환하고, 새로운 삼각형 면 인덱스를 (n_faces_new, 3) 형태의 numpy 배열로 반환합니다.

* **매개변수:**
  * **faces** – 사각형 메시의 면을 나타내는 1차원 배열입니다. 형태는 (N,)입니다.
  * **point_counts** – 각 면의 점 개수를 나타내는 배열입니다. 형태는 (N,)입니다.
* **반환값:**
  삼각형이 포함된 새로운 면 ID. 형태는 (n_faces_new, 3)입니다.

### isaaclab.utils.mesh.PRIMITIVE_MESH_TYPES *= ['Plane', 'Cube', 'Sphere', 'Cylinder', 'Capsule', 'Cone']*

트라이메시로 변환할 수 있는 지원되는 프리미티브 메시 타입 목록입니다.

## Modifier 작업

다양한 수정자 구현을 포함하는 서브모듈입니다.

수정자는 상태를 가지거나 가지지 않은 수정 작업으로 텐서 데이터에 적용하는 데 사용됩니다. 텐서와 구성을 입력으로 받아 수정이 적용된 텐서를 반환합니다. 이를 통해 사용자는 텐서에 적용할 맞춤 작업을 정의할 수 있습니다. 예를 들어, 수정자를 사용하여 입력 데이터를 정규화하거나 이동 평균을 적용할 수 있습니다.

주로 [`ObservationManager`](isaaclab.managers.md#isaaclab.managers.ObservationManager)에서 내장된 노이즈, 클리핑, 스케일링 후처리 작업 대신 맞춤 작업을 적용하는 데 사용됩니다. 자세한 내용은 [`ObservationTermCfg`](isaaclab.managers.md#isaaclab.managers.ObservationTermCfg) 클래스를 참조하세요.

함수 수정자 사용 예시:

```python
import torch
from isaaclab.utils import modifiers

# 랜덤 텐서 생성
my_tensor = torch.rand(256, 128, device="cuda")

# 수정자 구성 생성
cfg = modifiers.ModifierCfg(func=modifiers.clip, params={"bounds": (0.0, torch.inf)})

# 수정자 적용
my_modified_tensor = cfg.func(my_tensor, cfg)
```

클래스 수정자 사용 예시:

```python
import torch
from isaaclab.utils import modifiers

# 랜덤 텐서 생성
my_tensor = torch.rand(256, 128, device="cuda")

# 수정자 구성 생성
# 1 타임스텝 지연을 갖는 디지털 필터
cfg = modifiers.DigitalFilterCfg(A=[0.0], B=[0.0, 1.0])

# 수정자 인스턴스 생성
my_modifier = modifiers.DigitalFilter(cfg, my_tensor.shape, "cuda")

# 수정자를 호출 가능한 객체로 적용
my_modified_tensor = my_modifier(my_tensor)
```

**클래스:**

| [`ModifierCfg`](#isaaclab.utils.modifiers.ModifierCfg)           | 수정자 구성 파라미터                                                               |
|------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| [`ModifierBase`](#isaaclab.utils.modifiers.ModifierBase)         | 클래스로 구현된 수정자의 기본 클래스.                                                |
| [`DigitalFilter`](#isaaclab.utils.modifiers.DigitalFilter)       | 입력 데이터에 디지털 필터링을 적용하는 수정자.                                       |
| [`DigitalFilterCfg`](#isaaclab.utils.modifiers.DigitalFilterCfg) | 디지털 필터 수정자의 구성 파라미터.                                                |
| [`Integrator`](#isaaclab.utils.modifiers.Integrator)             | 중점 리만 합을 기반으로 한 수치적 전방 적분을 적용하는 수정자.                     |
| [`IntegratorCfg`](#isaaclab.utils.modifiers.IntegratorCfg)       | 적분자 수정자의 구성 파라미터.                                                    |

**함수:**

| [`bias`](#isaaclab.utils.modifiers.bias)(data, value)        | 데이터에 균일한 바이어스를 추가합니다.               |
|--------------------------------------------------------------|------------------------------------------------|
| [`clip`](#isaaclab.utils.modifiers.clip)(data, bounds)       | 데이터를 최소값과 최대값으로 클리핑합니다. |
| [`scale`](#isaaclab.utils.modifiers.scale)(data, multiplier) | 입력 데이터를 배수로 스케일링합니다.             |

### *class* isaaclab.utils.modifiers.ModifierCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

수정자 구성 파라미터

**속성:**

| [`params`](#isaaclab.utils.modifiers.ModifierCfg.params)   | 함수 또는 호출 가능한 클래스에 키워드 인자로 전달할 파라미터.   |
|------------------------------------------------------------|---------------------------------------------------------------------------------------|

#### params *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)]*

함수 또는 호출 가능한 클래스에 키워드 인자로 전달할 파라미터. 기본값은 빈 딕셔너리입니다.

### *class* isaaclab.utils.modifiers.ModifierBase

Bases: [`ABC`](https://docs.python.org/3/library/abc.html#abc.ABC)

클래스로 구현된 수정자의 기본 클래스.

수정자 구현은 함수 또는 클래스일 수 있습니다. 수정자가 클래스인 경우, 이 클래스를 상속하고 필수 메서드를 구현해야 합니다.

클래스 구현의 수정자는 호출 간 상태 정보를 저장하는 데 사용할 수 있습니다. 이는 이동 평균, 지연 또는 감쇠 필터와 같은 상태가 필요한 작업에 유용합니다.

클래스를 생성하고 사용하는 의사코드 예시:

```python
from isaaclab.utils import modifiers

# ModifierCfg에 전달할 사용자 정의 키워드 인자 정의
kwarg_dict = {"arg_1": VAL_1, "arg_2": VAL_2}

# 수정자 구성 객체 생성
# func는 수정자 클래스 이름이고, params는 인자 딕셔너리
modifier_config = modifiers.ModifierCfg(func=modifiers.ModifierBase, params=kwarg_dict)

# 수정자 인스턴스 생성
my_modifier = modifiers.ModifierBase(cfg=modifier_config)
```

**메서드:**

| [`reset`](#isaaclab.utils.modifiers.ModifierBase.reset)([env_ids])   | 수정자를 재설정합니다.                                    |
|----------------------------------------------------------------------|---------------------------------------------------------|
| [`__call__`](#isaaclab.utils.modifiers.ModifierBase.__call__)(data)  | 수정 함수 정의를 위한 추상 메서드. |

#### *abstract* reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

수정자를 재설정합니다.

* **매개변수:**
  **env_ids** – 환경 ID. 기본값은 None이며,この場合 모든 환경이 고려됩니다.

#### *abstract* \_\_call_\_(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

수정 함수 정의를 위한 추상 메서드.

* **매개변수:**
  **data** – 수정할 데이터. 초기화 시 전달된 data_dim과 형태가 일치해야 합니다.
* **반환값:**
  수정된 데이터. 형태는 입력 데이터와 동일합니다.

### *class* isaaclab.utils.modifiers.DigitalFilter

Bases: [`ModifierBase`](#isaaclab.utils.modifiers.ModifierBase)

입력 데이터에 디지털 필터링을 적용하는 수정자.

[디지털 필터](https://en.wikipedia.org/wiki/Digital_filter)는 이산 시간 신호를 처리하여 신호의 유용한 부분을 추출하는 데 사용됩니다. 예를 들어 평활화, 노이즈 제거, 주파수 분리 등이 있습니다.

필터는 시간 영역에서 선형 차분 방정식으로 구현할 수 있습니다. 이 방정식은 현재 및 이전 입력과 출력을 기반으로 각 시간 단계에서 출력을 계산하는 데 사용됩니다.

$$
y_{i} = X B - Y A = \sum_{j=0}^{N} b_j x_{i-j} - \sum_{j=1}^{M} a_j y_{i-j}

$$

여기서 $y_{i}$는 필터의 현재 출력입니다. 배열 $Y$는 필터의 이전 출력 $\{y_{i-j}\}_{j=1}^M$을 $M$개의 이전 시간 단계에 대해 포함합니다. 배열 $X$는 현재 입력 $x_{i}$와 필터에 대한 이전 입력 $\{x_{i-j}\}_{j=1}^N$을 $N$개의 이전 시간 단계에 대해 각각 포함합니다. 필터 계수 $A$와 $B$는 필터를 설계하는 데 사용되며, 각각 길이 $M$과 $N + 1$의 열 벡터입니다.

다양한 유형의 필터는 $A$와 $B$의 다른 값을 선택하여 구현할 수 있습니다.
아래에 몇 가지 예를 제공합니다.

### 예시

**단일 지연 필터**

입력 신호를 하나의 시간 단계만큼 지연시키는 필터는 단순히 이전 입력 값을 출력합니다.

$$
y_{i} = x_{i-1}

$$

이 디지털 필터는 계수 $A = [0.0]$ 및 $B = [0.0, 1.0]$을 사용하여 구현할 수 있습니다.

**이동 평균 필터**

이동 평균 필터는 신호의 잡음을 smoothing하기 위해 사용됩니다. 저역 통과 필터와 유사하지만, 유한 임펄스 응답(FIR)을 가지며 비재귀적입니다.

이 필터는 입력 신호의 시간 창을 따라 평균을 계산합니다. 이동 평균 필터의 선형 차이 방정식은 다음과 같습니다:

$$
y_{i} = \frac{1}{N} \sum_{j=0}^{N} x_{i-j}

$$

이 디지털 필터는 계수 $A = [0.0]$ 및 $B = [1/N, 1/N, \cdots, 1/N]$을 사용하여 구현할 수 있습니다.

**1차 재귀 저역 통과 필터**

재귀 저역 통과 필터는 신호의 고주파 잡음을 smoothing하기 위해 사용됩니다. 1차 무한 임펄스 응답(IIR) 필터로, 즉 재귀 구성 요소(이전 출력)가 선형 차이 방정식에 포함됨을 의미합니다.

1차 저역 통과 IIR 필터의 차이 방정식은 다음과 같습니다:

$$
y_{i} = \alpha y_{i-1} + (1-\alpha)x_{i}

$$

여기서 $\alpha$는 0과 1 사이의 smoothing 매개변수입니다. 일반적으로 $\alpha$의 값은 필터의 원하는 차단 주파수를 기반으로 선택됩니다.

이 필터는 계수 $A = [-\alpha]$ 및 $B = [1 - \alpha]$를 사용하여 디지털 필터로 구현할 수 있습니다.

**메서드:**

| [`reset`](#isaaclab.utils.modifiers.DigitalFilter.reset)([env_ids])   | 디지털 필터 기록을 재설정합니다.                                                        |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| [`__call__`](#isaaclab.utils.modifiers.DigitalFilter.__call__)(data)  | 롤링 히스토리 윈도우 입력 및 출력을 사용하여 디지털 필터 수정을 적용합니다. |

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

디지털 필터 기록을 재설정합니다.

* **매개변수:**
  **env_ids** – 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 고려됩니다.

#### \_\_call_\_(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

롤링 히스토리 윈도우 입력 및 출력을 사용하여 디지털 필터 수정을 적용합니다.

* **매개변수:**
  **data** – 필터를 적용할 데이터입니다.
* **반환값:**
  필터링된 데이터. 데이터와 동일한 형태입니다.

### *클래스* isaaclab.utils.modifiers.DigitalFilterCfg

Bases: [`ModifierCfg`](#isaaclab.utils.modifiers.ModifierCfg)

디지털 필터 수정자의 구성 파라미터입니다.

추가 정보는 [`DigitalFilter`](#isaaclab.utils.modifiers.DigitalFilter) 클래스를 참조하십시오.

**속성:**

| [`A`](#isaaclab.utils.modifiers.DigitalFilterCfg.A)           | 과거 출력에 대한 필터의 응답에 해당하는 계수입니다.            |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------|
| [`params`](#isaaclab.utils.modifiers.DigitalFilterCfg.params) | 함수 또는 호출 가능한 클래스에 키워드 인자로 전달할 파라미터입니다.  |
| [`B`](#isaaclab.utils.modifiers.DigitalFilterCfg.B)           | 현재 및 과거 입력에 대한 필터의 응답에 해당하는 계수입니다. |

#### A *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[float](https://docs.python.org/3/library/functions.html#float)]*

과거 출력에 대한 필터의 응답에 해당하는 계수입니다.

이는 필터의 과거 출력 가중치에 해당합니다. 첫 번째 요소는 이전 시간 단계의 출력의 계수, 두 번째 요소는 두 시간 단계 이전의 출력의 계수이며, 이와 같이 진행됩니다.

필터의 전달 함수의 분모 계수입니다.

#### params *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)]*

함수 또는 호출 가능한 클래스에 키워드 인자로 전달할 파라미터입니다. 기본값은 빈 사전입니다.

#### B *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[float](https://docs.python.org/3/library/functions.html#float)]*

현재 및 과거 입력에 대한 필터의 응답에 해당하는 계수입니다.

이는 필터의 현재 및 과거 입력 가중치에 해당합니다. 첫 번째 요소는 현재 입력의 계수, 두 번째 요소는 이전 시간 단계의 입력의 계수이며, 이와 같이 진행됩니다.

필터의 전달 함수의 분자 계수입니다.

### *클래스* isaaclab.utils.modifiers.Integrator

Bases: [`ModifierBase`](#isaaclab.utils.modifiers.ModifierBase)

중간 리emann 합을 기반으로 한 수치적 전방 적분을 적용하는 수정자입니다.

적분기는 시간에 따른 신호의 적분을 계산하는 데 사용됩니다. 신호의 적분은 신호 곡선 아래의 면적입니다. 적분은 [Riemann sum](https://en.wikipedia.org/wiki/Riemann_sum)과 같은 수치적 방법을 사용하여 근사할 수 있습니다.

중간 리emann 합은 곡선 아래의 영역을 사각형으로 나누어 함수의 적분을 근사하는 방법입니다. 각 사각형의 높이는 구간의 중점에서 함수의 값입니다. 각 사각형의 면적은 구간의 폭과 사각형의 높이의 곱입니다.

이 적분 방법은 규칙적인 간격으로 샘플링된 신호에 유용합니다. 적분은 다음과 같이 표현할 수 있습니다:

$$
\int_{t_0}^{t_n} f(t) dt & \approx \int_{t_0}^{t_{n-1}} f(t) dt + \frac{f(t_{n-1}) + f(t_n)}{2} \Delta t

$$

여기서 $f(t)$는 적분할 신호, $t_i$는 i번째 샘플의 시간, 그리고 $\Delta t$는 샘플 간의 시간 간격입니다.

**메서드:**

| [`reset`](#isaaclab.utils.modifiers.Integrator.reset)([env_ids])   | 적분기 상태를 0으로 재설정합니다.             |
|--------------------------------------------------------------------|----------------------------------------------|
| [`__call__`](#isaaclab.utils.modifiers.Integrator.__call__)(data)  | 입력 데이터에 적분 수정을 적용합니다. |

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

적분기 상태를 0으로 재설정합니다.

* **매개변수:**
  **env_ids** – 환경 ID입니다. 기본값은 None이며, 이 경우 모든 환경이 고려됩니다.

#### \_\_call_\_(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

입력 데이터에 적분 수정을 적용합니다.

* **매개변수:**
  **data** – 적분할 데이터입니다.
* **반환값:**
  입력 신호의 적분. 데이터와 동일한 형태입니다.

### *클래스* isaaclab.utils.modifiers.IntegratorCfg

Bases: [`ModifierCfg`](#isaaclab.utils.modifiers.ModifierCfg)

적분기 수정자의 구성 파라미터입니다.

추가 정보는 [`Integrator`](#isaaclab.utils.modifiers.Integrator) 클래스를 참조하십시오.

**속성:**

| [`params`](#isaaclab.utils.modifiers.IntegratorCfg.params)   | 함수 또는 호출 가능한 클래스에 키워드 인자로 전달할 파라미터입니다.   |
|--------------------------------------------------------------|---------------------------------------------------------------------------------------|
| [`dt`](#isaaclab.utils.modifiers.IntegratorCfg.dt)           | 적분기의 시간 단계.                                                      |

#### params *: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)]*

함수 또는 호출 가능한 클래스에 키워드 인자로 전달할 파라미터입니다. 기본값은 빈 사전입니다.

#### dt *: [float](https://docs.python.org/3/library/functions.html#float)*

적분기의 시간 단계입니다.

### isaaclab.utils.modifiers.bias(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), value: [float](https://docs.python.org/3/library/functions.html#float)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

데이터에 균일한 바이어스를 추가합니다.

* **매개변수:**
  * **data** – 바이어스를 추가할 데이터입니다.
  * **value** – 데이터에 추가할 바이어스의 값입니다.
* **반환값:**
  바이어스가 적용된 데이터. 데이터와 동일한 형태입니다.

### isaaclab.utils.modifiers.clip(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), bounds: [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None), [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None)]) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

데이터를 최소값과 최대값으로 클립합니다.

* **매개변수:**
  * **data** – 클립을 적용할 데이터입니다.
  * **bounds** – 데이터에 클립할 최소값과 최대값을 포함하는 튜플입니다.
    값이 None인 경우 해당 바운드는 적용되지 않습니다.
* **반환값:**
  클립된 데이터. 데이터와 동일한 형태입니다.

### isaaclab.utils.modifiers.scale(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), multiplier: [float](https://docs.python.org/3/library/functions.html#float)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

입력 데이터를 multiplier로 스케일링합니다.

* **매개변수:**
  * **data** – 스케일을 적용할 데이터입니다.
  * **multiplier** – 입력값을 스케일링하기 위한 값입니다.
* **반환값:**
  스케일링된 데이터. 형태는 data와 동일합니다.

## 노이즈 연산

다양한 노이즈 모델 구현을 포함하는 하위 모듈입니다.

노이즈 모델은 텐서와 구성을 입력으로 받아 노이즈가 적용된 텐서를 반환하는 함수로 구현됩니다. 이러한 함수는 [`NoiseCfg`](#isaaclab.utils.noise.NoiseCfg) 구성 클래스에서 사용됩니다.

사용법:

```python
import torch
from isaaclab.utils.noise import AdditiveGaussianNoiseCfg

# 임의의 텐서 생성
my_tensor = torch.rand(128, 128, device="cuda")

# 노이즈 구성 생성
cfg = AdditiveGaussianNoiseCfg(mean=0.0, std=1.0)

# 노이즈 적용
my_noisified_tensor = cfg.func(my_tensor, cfg)
```

**클래스:**

| [`NoiseCfg`](#isaaclab.utils.noise.NoiseCfg)                                           | 노이즈 항목에 대한 기본 구성.                                  |
|----------------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| [`ConstantNoiseCfg`](#isaaclab.utils.noise.ConstantNoiseCfg)                           | 상수 노이즈 항목에 대한 추가 구성.                    |
| [`GaussianNoiseCfg`](#isaaclab.utils.noise.GaussianNoiseCfg)                           | 가우시안 노이즈 항목에 대한 추가 구성.                    |
| [`NoiseModelCfg`](#isaaclab.utils.noise.NoiseModelCfg)                                 | 노이즈 모델에 대한 구성.                                      |
| [`NoiseModelWithAdditiveBiasCfg`](#isaaclab.utils.noise.NoiseModelWithAdditiveBiasCfg) | 편향이 있는 가우시안 노이즈 모델에 대한 구성.         |
| [`UniformNoiseCfg`](#isaaclab.utils.noise.UniformNoiseCfg)                             | 균등 분포 노이즈 항목에 대한 추가 구성.                      |
| [`NoiseModel`](#isaaclab.utils.noise.NoiseModel)                                       | 노이즈 모델의 기본 클래스.                                          |
| [`NoiseModelWithAdditiveBias`](#isaaclab.utils.noise.NoiseModelWithAdditiveBias)       | 편향이 있는 노이즈 모델.                                    |
| [`ConstantBiasNoiseCfg`](#isaaclab.utils.noise.ConstantBiasNoiseCfg)                   | [`ConstantNoiseCfg`](#isaaclab.utils.noise.ConstantNoiseCfg)의 별칭. |
| [`AdditiveUniformNoiseCfg`](#isaaclab.utils.noise.AdditiveUniformNoiseCfg)             | [`UniformNoiseCfg`](#isaaclab.utils.noise.UniformNoiseCfg)의 별칭.   |
| [`AdditiveGaussianNoiseCfg`](#isaaclab.utils.noise.AdditiveGaussianNoiseCfg)           | [`GaussianNoiseCfg`](#isaaclab.utils.noise.GaussianNoiseCfg)의 별칭. |

**함수:**

| [`constant_noise`](#isaaclab.utils.noise.constant_noise)(data, cfg)   | 주어진 데이터셋에 상수 노이즈 편향을 적용합니다.   |
|-----------------------------------------------------------------------|------------------------------------------------------|
| [`gaussian_noise`](#isaaclab.utils.noise.gaussian_noise)(data, cfg)   | 주어진 데이터셋에 가우시안 노이즈를 적용합니다.        |
| [`uniform_noise`](#isaaclab.utils.noise.uniform_noise)(data, cfg)     | 주어진 데이터셋에 균등 분포 노이즈를 적용합니다.         |

### *class* isaaclab.utils.noise.NoiseCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

노이즈 항목에 대한 기본 구성입니다.

**속성:**

| [`operation`](#isaaclab.utils.noise.NoiseCfg.operation)   | 데이터에 노이즈를 적용하는 연산.   |
|-----------------------------------------------------------|-------------------------------------------------|

#### operation *: [Literal](https://docs.python.org/3/library/typing.html#typing.Literal)['add', 'scale', 'abs']*

데이터에 노이즈를 적용하는 연산. 기본값은 “add”입니다.

### *class* isaaclab.utils.noise.ConstantNoiseCfg

Bases: [`NoiseCfg`](#isaaclab.utils.noise.NoiseCfg)

상수 노이즈 항목에 대한 추가 구성입니다.

**속성:**

| [`bias`](#isaaclab.utils.noise.ConstantNoiseCfg.bias)           | 추가할 편향.                              |
|-----------------------------------------------------------------|-----------------------------------------------|
| [`operation`](#isaaclab.utils.noise.ConstantNoiseCfg.operation) | 데이터에 노이즈를 적용하는 연산. |

#### bias *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float)*

추가할 편향. 기본값은 0.0입니다.

#### operation *: Literal['add', 'scale', 'abs']*

데이터에 노이즈를 적용하는 연산. 기본값은 “add”입니다.

### *class* isaaclab.utils.noise.GaussianNoiseCfg

Bases: [`NoiseCfg`](#isaaclab.utils.noise.NoiseCfg)

가우시안 노이즈 항목에 대한 추가 구성입니다.

**속성:**

| [`mean`](#isaaclab.utils.noise.GaussianNoiseCfg.mean)           | 노이즈의 평균.                        |
|-----------------------------------------------------------------|-----------------------------------------------|
| [`std`](#isaaclab.utils.noise.GaussianNoiseCfg.std)             | 노이즈의 표준 편차.          |
| [`operation`](#isaaclab.utils.noise.GaussianNoiseCfg.operation) | 데이터에 노이즈를 적용하는 연산. |

#### mean *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float)*

노이즈의 평균. 기본값은 0.0입니다.

#### std *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float)*

노이즈의 표준 편차. 기본값은 1.0입니다.

#### operation *: Literal['add', 'scale', 'abs']*

데이터에 노이즈를 적용하는 연산. 기본값은 “add”입니다.

### *class* isaaclab.utils.noise.NoiseModelCfg

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

노이즈 모델에 대한 구성입니다.

**클래스:**

| [`class_type`](#isaaclab.utils.noise.NoiseModelCfg.class_type)   | [`NoiseModel`](#isaaclab.utils.noise.NoiseModel)의 별칭.   |
|------------------------------------------------------------------|-------------------------------------------------------------|

**속성:**

| [`noise_cfg`](#isaaclab.utils.noise.NoiseModelCfg.noise_cfg)   | 사용할 노이즈 구성.   |
|----------------------------------------------------------------|-----------------------------------|

#### class_type

[`NoiseModel`](#isaaclab.utils.noise.NoiseModel)의 별칭.

#### noise_cfg *: [NoiseCfg](#isaaclab.utils.noise.NoiseCfg)*

사용할 노이즈 구성.

### *class* isaaclab.utils.noise.NoiseModelWithAdditiveBiasCfg

Bases: [`NoiseModelCfg`](#isaaclab.utils.noise.NoiseModelCfg)

편향이 있는 가우시안 노이즈 모델에 대한 구성입니다.

**속성:**

| [`noise_cfg`](#isaaclab.utils.noise.NoiseModelWithAdditiveBiasCfg.noise_cfg)                                 | 사용할 노이즈 구성.                            |
|--------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| [`bias_noise_cfg`](#isaaclab.utils.noise.NoiseModelWithAdditiveBiasCfg.bias_noise_cfg)                       | 편향에 대한 노이즈 구성.                      |
| [`sample_bias_per_component`](#isaaclab.utils.noise.NoiseModelWithAdditiveBiasCfg.sample_bias_per_component) | 데이터 컴포넌트별로 별도의 편향을 샘플링할지 여부. |

**클래스:**

| [`class_type`](#isaaclab.utils.noise.NoiseModelWithAdditiveBiasCfg.class_type)   | [`NoiseModelWithAdditiveBias`](#isaaclab.utils.noise.NoiseModelWithAdditiveBias)의 별칭.   |
|----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|

#### noise_cfg *: [NoiseCfg](#isaaclab.utils.noise.NoiseCfg)*

사용할 노이즈 구성.

#### class_type

[`NoiseModelWithAdditiveBias`](#isaaclab.utils.noise.NoiseModelWithAdditiveBias)의 별칭.

#### bias_noise_cfg *: [NoiseCfg](#isaaclab.utils.noise.NoiseCfg)*

편향에 대한 노이즈 구성.

이 구성에 따라, 노이즈 모델이 리셋될 때마다 편향이 샘플링됩니다.

#### sample_bias_per_component *: [bool](https://docs.python.org/3/library/functions.html#bool)*

데이터 컴포넌트별로 별도의 편향을 샘플링할지 여부.

기본값은 True입니다.

### *class* isaaclab.utils.noise.UniformNoiseCfg

Bases: [`NoiseCfg`](#isaaclab.utils.noise.NoiseCfg)

균등 분포 노이즈 항목에 대한 추가 구성입니다.

**속성:**

| [`n_min`](#isaaclab.utils.noise.UniformNoiseCfg.n_min)         | 노이즈의 최소값.               |
|----------------------------------------------------------------|-----------------------------------------------|
| [`n_max`](#isaaclab.utils.noise.UniformNoiseCfg.n_max)         | 노이즈의 최대값.               |
| [`operation`](#isaaclab.utils.noise.UniformNoiseCfg.operation) | 데이터에 노이즈를 적용하는 연산. |

#### n_min *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float)*

노이즈의 최소값. 기본값은 -1.0입니다.

#### n_max *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [float](https://docs.python.org/3/library/functions.html#float)*

노이즈의 최대값. 기본값은 1.0입니다.

#### operation *: Literal['add', 'scale', 'abs']*

데이터에 노이즈를 적용하는 연산입니다. 기본값은 “add”입니다.

### *class* isaaclab.utils.noise.NoiseModel

기반 클래스: [`object`](https://docs.python.org/3/library/functions.html#object)

노이즈 모델의 기본 클래스입니다.

**메서드:**

| [`reset`](#isaaclab.utils.noise.NoiseModel.reset)([env_ids])   | 노이즈 모델을 재설정합니다.   |
|----------------------------------------------------------------|------------------------------|

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

노이즈 모델을 재설정합니다.

이 메서드는 파생 클래스에서 노이즈 모델을 재설정하기 위해 구현할 수 있습니다.
랜덤 워크와 같은 시간적 노이즈 모델을 구현할 때 유용합니다.

* **매개변수:**
  **env_ids** – 노이즈 모델을 재설정할 환경 ID입니다. 기본값은 None이며,
  이 경우 모든 환경이 고려됩니다.

### *class* isaaclab.utils.noise.NoiseModelWithAdditiveBias

기반 클래스: [`NoiseModel`](#isaaclab.utils.noise.NoiseModel)

가산 편향을 갖는 노이즈 모델.

편향 항은 재설정 시 지정된 분포에서 샘플링됩니다.

**메서드:**

| [`reset`](#isaaclab.utils.noise.NoiseModelWithAdditiveBias.reset)([env_ids])   | 노이즈 모델을 재설정합니다.   |
|--------------------------------------------------------------------------------|------------------------------|

#### reset(env_ids: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

노이즈 모델을 재설정합니다.

이 메서드는 지정된 환경의 편향 항을 재설정합니다.

* **매개변수:**
  **env_ids** – 노이즈 모델을 재설정할 환경 ID입니다. 기본값은 None이며,
  이 경우 모든 환경이 고려됩니다.

### isaaclab.utils.noise.constant_noise(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), cfg: [noise_cfg.ConstantNoiseCfg](#isaaclab.utils.noise.ConstantNoiseCfg)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

주어진 데이터 세트에 상수 노이즈 편향을 적용합니다.

* **매개변수:**
  * **data** – 노이즈를 적용할 수정되지 않은 데이터 세트입니다.
  * **cfg** – 상수 노이즈의 구성 매개변수입니다.
* **반환값:**
  제공된 노이즈 매개변수로 수정된 데이터입니다.

### isaaclab.utils.noise.gaussian_noise(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), cfg: [noise_cfg.GaussianNoiseCfg](#isaaclab.utils.noise.GaussianNoiseCfg)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

주어진 데이터 세트에 가우시안 노이즈를 적용합니다.

* **매개변수:**
  * **data** – 노이즈를 적용할 수정되지 않은 데이터 세트입니다.
  * **cfg** – 가우시안 노이즈의 구성 매개변수입니다.
* **반환값:**
  제공된 노이즈 매개변수로 수정된 데이터입니다.

### isaaclab.utils.noise.uniform_noise(data: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), cfg: [noise_cfg.UniformNoiseCfg](#isaaclab.utils.noise.UniformNoiseCfg)) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

주어진 데이터 세트에 균등 분포 노이즈를 적용합니다.

* **매개변수:**
  * **data** – 노이즈를 적용할 수정되지 않은 데이터 세트입니다.
  * **cfg** – 균등 분포 노이즈의 구성 매개변수입니다.
* **반환값:**
  제공된 노이즈 매개변수로 수정된 데이터입니다.

### isaaclab.utils.noise.ConstantBiasNoiseCfg

 별칭: [`ConstantNoiseCfg`](#isaaclab.utils.noise.ConstantNoiseCfg)

### isaaclab.utils.noise.AdditiveUniformNoiseCfg

 별칭: [`UniformNoiseCfg`](#isaaclab.utils.noise.UniformNoiseCfg)

### isaaclab.utils.noise.AdditiveGaussianNoiseCfg

 별칭: [`GaussianNoiseCfg`](#isaaclab.utils.noise.GaussianNoiseCfg)

## 시드 연산

**함수:**

| [`configure_seed`](#isaaclab.utils.seed.configure_seed)(seed[, torch_deterministic])   | 모든 난수 생성기(torch, numpy, random, warp)에 시드를 설정합니다.   |
|----------------------------------------------------------------------------------------|-------------------------------------------------------------------|

### isaaclab.utils.seed.configure_seed(seed: [int](https://docs.python.org/3/library/functions.html#int) | [None](https://docs.python.org/3/library/constants.html#None), torch_deterministic: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [int](https://docs.python.org/3/library/functions.html#int)

모든 난수 생성기(torch, numpy, random, warp)에 시드를 설정합니다.

* **매개변수:**
  * **seed** – 난수 시드 값입니다. None인 경우 무작위 시드가 생성됩니다.
  * **torch_deterministic** – True인 경우 torch 연산에 대한 결정론적 모드를 활성화합니다.
* **반환값:**
  설정된 시드 값입니다.

## 센서 연산

**함수:**

| [`convert_camera_intrinsics_to_usd`](#isaaclab.utils.sensors.convert_camera_intrinsics_to_usd)(...[, ...])   | 카메라 내츄린직과 해상도로부터 USD 카메라 속성을 생성합니다.   |
|--------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------|

### isaaclab.utils.sensors.convert_camera_intrinsics_to_usd(intrinsic_matrix: [list](https://docs.python.org/3/library/stdtypes.html#list)[[float](https://docs.python.org/3/library/functions.html#float)], width: [int](https://docs.python.org/3/library/functions.html#int), height: [int](https://docs.python.org/3/library/functions.html#int), focal_length: [float](https://docs.python.org/3/library/functions.html#float) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [dict](https://docs.python.org/3/library/stdtypes.html#dict)

카메라 내츄린직과 해상도로부터 USD 카메라 속성을 생성합니다.

* **매개변수:**
  * **intrinsic_matrix** – 행-major 형식의 카메라 내츄린직 행렬입니다.
    행렬은 [f_x, 0, c_x, 0, f_y, c_y, 0, 0, 1]로 정의됩니다. 크기는 (9,)입니다.
  * **width** – 이미지 너비(픽셀 단위).
  * **height** – 이미지 높이(픽셀 단위).
  * **focal_length** – 픽셀 크기 계산을 위한 원근 초점 길이(cm). 기본값은 None이며,
    이 경우 초점 길이는 1 / width로 계산됩니다.
* **반환값:**
  focal_length, horizontal_aperture, vertical_aperture,
  horizontal_aperture_offset, vertical_aperture_offset에 대한 USD 카메라 매개변수 사전입니다.

## 문자열 연산

문자열과 정규 표현식 변환을 위한 유틸리티를 포함하는 서브 모듈입니다.

**함수:**

| [`to_camel_case`](#isaaclab.utils.string.to_camel_case)(snake_str[, to])                                   | snake case 문자열을 camel case로 변환합니다.                                                                                 |
|------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| [`to_snake_case`](#isaaclab.utils.string.to_snake_case)(camel_str)                                         | camel case 문자열을 snake case로 변환합니다.                                                                                 |
| [`string_to_slice`](#isaaclab.utils.string.string_to_slice)(s)                                             | 문자열 표현의 슬라이스를 슬라이스 객체로 변환합니다.                                                                    |
| [`is_lambda_expression`](#isaaclab.utils.string.is_lambda_expression)(name)                                | 입력 문자열이 람다 표현식인지 확인합니다.                                                                               |
| [`callable_to_string`](#isaaclab.utils.string.callable_to_string)(value)                                   | 호출 가능한 객체를 문자열로 변환합니다.                                                                                          |
| [`string_to_callable`](#isaaclab.utils.string.string_to_callable)(name)                                    | 모듈과 함수 이름을 해결하여 함수를 반환합니다.                                                                   |
| [`resolve_matching_names`](#isaaclab.utils.string.resolve_matching_names)(keys, list_of_strings)           | 문자열 목록에 대한 쿼리 정규 표현식 목록과 일치시켜 일치하는 인덱스와 이름을 반환합니다.                    |
| [`resolve_matching_names_values`](#isaaclab.utils.string.resolve_matching_names_values)(data, ...[, ...])  | 사전의 정규 표현식 목록과 문자열 목록을 일치시켜 일치하는 인덱스, 이름 및 값을 반환합니다. |
| [`find_unique_string_name`](#isaaclab.utils.string.find_unique_string_name)(initial_name, ...)             | 제공된 조건 함수를 기반으로 고유한 문자열 이름을 찾습니다.                                                              |
| [`find_root_prim_path_from_regex`](#isaaclab.utils.string.find_root_prim_path_from_regex)(prim_path_regex) | 정규 표현식 패턴 프림 위에 있는 첫 번째 프림과 그 위치를 찾습니다.                                                               |

### isaaclab.utils.string.to_camel_case(snake_str: [str](https://docs.python.org/3/library/stdtypes.html#str), to: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'cC') → [str](https://docs.python.org/3/library/stdtypes.html#str)

snake case 문자열을 camel case로 변환합니다.

* **매개변수:**
  * **snake_str** – snake case 형식의 문자열(즉, '_' 포함)
  * **to** – 문자열을 변환할 규칙. 기본값은 “cC”입니다.
* **오류:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – “cC” 또는 “CC”가 아닌 잘못된 입력 인수.
* **반환값:**
  camel-case 형식의 문자열.

### isaaclab.utils.string.to_snake_case(camel_str: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [str](https://docs.python.org/3/library/stdtypes.html#str)

캐멀 케이스 문자열을 스네이크 케이스로 변환합니다.

* **매개변수:**
  **camel_str** – 캐멀 케이스 문자열.
* **반환값:**
  스네이크 케이스 문자열(즉, '_'가 포함된 문자열)

### isaaclab.utils.string.string_to_slice(s: [str](https://docs.python.org/3/library/stdtypes.html#str))

슬라이스 객체의 문자열 표현을 슬라이스 객체로 변환합니다.

* **매개변수:**
  **s** – 슬라이스의 문자열 표현.
* **반환값:**
  슬라이스 객체.

### isaaclab.utils.string.is_lambda_expression(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [bool](https://docs.python.org/3/library/functions.html#bool)

입력 문자열이 람다 표현인지 확인합니다.

* **매개변수:**
  **name** – 입력 문자열.
* **반환값:**
  입력 문자열이 람다 표현인지 여부.

### isaaclab.utils.string.callable_to_string(value: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)) → [str](https://docs.python.org/3/library/stdtypes.html#str)

호출 가능한 객체를 문자열로 변환합니다.

* **매개변수:**
  **value** – 호출 가능한 객체.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 입력 인자가 호출 가능한 객체가 아닐 때 발생.
* **반환값:**
  호출 가능한 객체의 문자열 표현.

### isaaclab.utils.string.string_to_callable(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)

모듈과 함수 이름을 해석하여 함수를 반환합니다.

* **매개변수:**
  **name** – 함수 이름. 형식은 'module:attribute_name' 또는 람다 표현인 'lambda x: x' 이어야 합니다.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 해석된 속성이 함수가 아닐 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 모듈을 찾을 수 없을 때.
* **반환값:**
  모듈에서 로드된 함수.
* **반환 타입:**
  Callable

### isaaclab.utils.string.resolve_matching_names(keys: [str](https://docs.python.org/3/library/stdtypes.html#str) | [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], list_of_strings: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]]

쿼리 정규 표현식 목록을 문자열 목록에 매칭하여 매칭된 인덱스와 이름을 반환합니다.

쿼리 정규 표현식 목록이 제공되면, 함수는 각 대상 문자열을 각 쿼리 정규 표현식과 비교하여 매칭된 문자열의 인덱스와 매칭된 문자열을 반환합니다.

`preserve_order`가 True인 경우, 매칭된 인덱스와 이름의 순서는 제공된 문자열 목록의 순서와 동일합니다. 즉, 순서는 대상 문자열의 순서에 따라 결정되며 쿼리 정규 표현식의 순서에 따라 결정되지 않습니다.

`preserve_order`가 False인 경우, 매칭된 인덱스와 이름의 순서는 제공된 쿼리 정규 표현식 목록의 순서와 동일합니다.

예를 들어, 문자열 목록이 ['a', 'b', 'c', 'd', 'e']이고 정규 표현식이 ['a|c', 'b']일 때:
- `preserve_order`가 False이면, 함수는 매칭된 문자열의 인덱스와 문자열을 ([0, 1, 2], ['a', 'b', 'c'])로 반환합니다.
- `preserve_order`가 True이면, 함수는 이를 ([0, 2, 1], ['a', 'c', 'b'])로 반환합니다.

#### 주의
함수는 인덱스를 정렬하지 않습니다. 찾은 순서대로 인덱스를 반환합니다.

* **매개변수:**
  * **keys** – 문자열 목록에 매칭할 정규 표현식 또는 정규 표현식 목록.
  * **list_of_strings** – 매칭할 문자열 목록.
  * **preserve_order** – 반환된 값에서 쿼리 키의 순서를 유지할지 여부. 기본값은 False입니다.
* **반환값:**
  매칭된 인덱스와 이름을 포함하는 리스트들의 튜플.
* **예외:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 목록의 문자열에 대해 여러 번 매칭이 발견될 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 모든 정규 표현식이 매칭되지 않을 때.

### isaaclab.utils.string.resolve_matching_names_values(data: [dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [Any](https://docs.python.org/3/library/typing.html#typing.Any)], list_of_strings: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[str](https://docs.python.org/3/library/stdtypes.html#str)], preserve_order: [bool](https://docs.python.org/3/library/functions.html#bool) = False, strict: [bool](https://docs.python.org/3/library/functions.html#bool) = True) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[list](https://docs.python.org/3/library/stdtypes.html#list)[[int](https://docs.python.org/3/library/functions.html#int)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)], [list](https://docs.python.org/3/library/stdtypes.html#list)[[Any](https://docs.python.org/3/library/typing.html#typing.Any)]]

사전의 정규 표현식 목록을 문자열 목록에 매칭하여 매칭된 인덱스, 이름 및 값을 반환합니다.

`preserve_order`가 True인 경우, 매칭된 인덱스와 이름의 순서는 제공된 문자열 목록의 순서와 동일합니다. 즉, 순서는 대상 문자열의 순서에 따라 결정되며 쿼리 정규 표현식의 순서에 따라 결정되지 않습니다.

`preserve_order`가 False인 경우, 매칭된 인덱스와 이름의 순서는 제공된 쿼리 정규 표현식 목록의 순서와 동일합니다.

예를 들어, 사전이 {"a|d|e": 1, "b|c": 2}이고 문자열 목록이 ['a', 'b', 'c', 'd', 'e']일 때:
- `preserve_order`가 False이면, 함수는 매칭된 문자열의 인덱스, 문자열 및 값을 ([0, 1, 2, 3, 4], ['a', 'b', 'c', 'd', 'e'], [1, 2, 2, 1, 1])로 반환합니다.
- `preserve_order`가 True이면, 함수는 이를 ([0, 3, 4, 1, 2], ['a', 'd', 'e', 'b', 'c'], [1, 1, 1, 2, 2])로 반환합니다.

* **매개변수:**
  * **data** – 문자열 목록에 매칭할 정규 표현식과 값이 포함된 사전.
  * **list_of_strings** – 매칭할 문자열 목록.
  * **preserve_order** – 반환된 값에서 쿼리 키의 순서를 유지할지 여부. 기본값은 False입니다.
  * **strict** – 사전의 모든 키가 매칭되어야 하는지 여부. 기본값은 True입니다.
* **반환값:**
  매칭된 인덱스, 이름 및 값을 포함하는 리스트들의 튜플.
* **예외:**
  * [**TypeError**](https://docs.python.org/3/library/exceptions.html#TypeError) – 입력 인자 `data`가 사전이 아닐 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 사전의 문자열에 대해 여러 번 매칭이 발견될 때.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `strict`가 True인 경우 사전의 모든 정규 표현식 키가 매칭되지 않을 때.

### isaaclab.utils.string.find_unique_string_name(initial_name: [str](https://docs.python.org/3/library/stdtypes.html#str), is_unique_fn: [Callable](https://trimesh.org/trimesh.typed.html#trimesh.typed.Callable)[[[str](https://docs.python.org/3/library/stdtypes.html#str)], [bool](https://docs.python.org/3/library/functions.html#bool)]) → [str](https://docs.python.org/3/library/stdtypes.html#str)

제공된 술어 함수를 기반으로 고유한 문자열 이름을 찾습니다.
문자는 결과 문자열이 고유해질 때까지 "_N" 형식으로 추가됩니다. 여기서 N은 자연수입니다.
:param initial_name: 초기 문자열 이름.
:type initial_name: str
:param is_unique_fn: 검증할 술어 함수.
:type is_unique_fn: Callable[[str], bool]

* **반환값:**
  입력 함수에 기반한 고유한 문자열.
* **반환 타입:**
  [str](https://docs.python.org/3/library/stdtypes.html#str)

### isaaclab.utils.string.find_root_prim_path_from_regex(prim_path_regex: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[str](https://docs.python.org/3/library/stdtypes.html#str), [int](https://docs.python.org/3/library/functions.html#int)]

정규 표현식 프림 위에 있는 첫 번째 프림과 그 위치를 찾습니다.
:param prim_path_regex: 정규 표현식 프림을 포함한 전체 프림 경로.
:type prim_path_regex: str

* **반환값:**
  첫 번째 위치는 정규 표현식 프림의 부모 프림 경로입니다.
  두 번째 위치는 USD 스테이지 트리 표현에서 정규 표현식 프림의 레벨을 나타냅니다.
* **반환 타입:**
  Tuple[[str](https://docs.python.org/3/library/stdtypes.html#str), [int](https://docs.python.org/3/library/functions.html#int)]

## 타이머 작업

성능 측정에 사용할 수 있는 타이머 클래스를 위한 하위 모듈입니다.

**예외:**

| [`TimerError`](#isaaclab.utils.timer.TimerError)   | [`Timer`](#isaaclab.utils.timer.Timer) 클래스 사용 중 오류를 보고하는 데 사용되는 사용자 정의 예외입니다.   |
|----------------------------------------------------|----------------------------------------------------------------------------------------------------|

**클래스:**

| [`Timer`](#isaaclab.utils.timer.Timer)   | 성능 측정을 위한 타이머입니다.   |
|------------------------------------------|-----------------------------------------|

### *exception* isaaclab.utils.timer.TimerError

Bases: [`Exception`](https://docs.python.org/3/library/exceptions.html#Exception)

[`Timer`](#isaaclab.utils.timer.Timer) 클래스 사용 중 오류를 보고하기 위한 사용자 정의 예외입니다.

### *class* isaaclab.utils.timer.Timer

Bases: [`ContextDecorator`](https://docs.python.org/3/library/contextlib.html#contextlib.ContextDecorator)

성능 측정을 위한 타이머입니다.

성능 측정 시간을 추적하기 위한 클래스입니다.
컨텍스트 관리자와 데코레이터를 통한 타이밍을 지원합니다.

시스템에서 가장 높은 해상도를 제공하는 `time.perf_counter` 함수를 사용하여 시간을 측정합니다. 이 함수는 에포크부터 경과된 초 수를 부동소수점 값으로 반환합니다.

일반 객체로서:

```python
import time

from isaaclab.utils.timer import Timer

timer = Timer()
timer.start()
time.sleep(1)
print(1 <= timer.time_elapsed <= 2)  # Output: True

time.sleep(1)
timer.stop()
print(2 <= stopwatch.total_run_time)  # Output: True
```

컨텍스트 관리자로서:

```python
import time

from isaaclab.utils.timer import Timer

with Timer() as timer:
    time.sleep(1)
    print(1 <= timer.time_elapsed <= 2)  # Output: True
```

참조: [https://gist.github.com/sumeet/1123871](https://gist.github.com/sumeet/1123871)

**속성:**

| [`timing_info`](#isaaclab.utils.timer.Timer.timing_info)       | 타이머 인스턴스별 경과 시간을 전역적으로 저장하기 위한 딕셔너리입니다.            |
|----------------------------------------------------------------|----------------------------------------------------------------------------------|
| [`time_elapsed`](#isaaclab.utils.timer.Timer.time_elapsed)     | 이 타이머가 타이밍을 시작한 이후 경과된 초 수입니다.         |
| [`total_run_time`](#isaaclab.utils.timer.Timer.total_run_time) | 타이머가 시작되어 종료될 때까지 경과된 초 수입니다. |

**메서드:**

| [`__init__`](#isaaclab.utils.timer.Timer.__init__)([msg, name])      | 타이머를 초기화합니다.                             |
|----------------------------------------------------------------------|----------------------------------------------------|
| [`start`](#isaaclab.utils.timer.Timer.start)()                       | 타이밍 시작.                                      |
| [`stop`](#isaaclab.utils.timer.Timer.stop)()                         | 타이밍 중지.                                       |
| [`get_timer_info`](#isaaclab.utils.timer.Timer.get_timer_info)(name) | 전역 딕셔너리에서 기록된 시간을 가져옵니다 |

#### timing_info *: [ClassVar](https://docs.python.org/3/library/typing.html#typing.ClassVar)[[dict](https://docs.python.org/3/library/stdtypes.html#dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [float](https://docs.python.org/3/library/functions.html#float)]]* *= {}*

타이머 인스턴스별 경과 시간을 전역적으로 저장하기 위한 딕셔너리입니다.

이 딕셔너리는 타이머 정보를 기록합니다. 키는 타이머 클래스 초기화 시 제공된 이름입니다. 생성자에 `name`이 전달되지 않으면 딕셔너리에 시간이 기록되지 않습니다.

#### \_\_init_\_(msg: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None, name: [str](https://docs.python.org/3/library/stdtypes.html#str) | [None](https://docs.python.org/3/library/constants.html#None) = None)

타이머를 초기화합니다.

* **매개변수:**
  * **msg** – 컨텍스트 관리자에서 타이머 클래스 사용 시 표시할 메시지입니다. 기본값은 None입니다.
  * **name** – 전역 딕셔너리에서 시간을 기록하는 데 사용할 이름입니다. 기본값은 None입니다.

#### *property* time_elapsed *: [float](https://docs.python.org/3/library/functions.html#float)*

이 타이머가 타이밍을 시작한 이후 경과된 초 수입니다.

#### 참고
이 속성은 타이머가 여전히 실행 중일 때 경과 시간을 확인하는 데 사용됩니다.

#### *property* total_run_time *: [float](https://docs.python.org/3/library/functions.html#float)*

타이머가 시작되어 종료될 때까지 경과된 초 수입니다.

#### start()

타이밍을 시작합니다.

#### stop()

타이밍을 중지합니다.

#### *static* get_timer_info(name: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [float](https://docs.python.org/3/library/functions.html#float)

전역 딕셔너리에서 기록된 시간을 가져옵니다
: 이름을 기준으로.

* **매개변수:**
  **name** – 검색할 항목의 이름입니다.
* **예외:**
  [**TimerError**](#isaaclab.utils.timer.TimerError) – 이름에 해당하는 항목이 로그에 없을 경우 발생합니다.
* **반환값:**
  이름이 존재할 경우 해당 이름을 기준으로 기록된 시간을 포함한 부동소수점 값입니다.

## 타입 작업

다양한 데이터 타입을 위한 하위 모듈입니다.

**클래스:**

| [`ArticulationActions`](#isaaclab.utils.types.ArticulationActions)   | 관절 동작을 저장하기 위한 데이터 컨테이너입니다.   |
|----------------------------------------------------------------------|----------------------------------------------------------|

### *class* isaaclab.utils.types.ArticulationActions

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

관절의 동작을 저장하기 위한 데이터 컨테이너입니다.

이 클래스는 관절의 관절 동작을 저장하는 데 사용됩니다. 관절의 위치, 속도, 토크 및 인덱스를 저장하는 데 사용됩니다.

동작이 제공되지 않은 경우, 값은 None으로 설정됩니다.

**속성:**

| [`joint_positions`](#isaaclab.utils.types.ArticulationActions.joint_positions)   | 관절의 위치입니다.   |
|----------------------------------------------------------------------------------|--------------------------------------------|
| [`joint_velocities`](#isaaclab.utils.types.ArticulationActions.joint_velocities) | 관절의 속도입니다.  |
| [`joint_efforts`](#isaaclab.utils.types.ArticulationActions.joint_efforts)       | 관절의 토크입니다.     |
| [`joint_indices`](#isaaclab.utils.types.ArticulationActions.joint_indices)       | 관절의 인덱스입니다.     |

**메서드:**

| [`__init__`](#isaaclab.utils.types.ArticulationActions.__init__)([joint_positions, ...])   |    |
|--------------------------------------------------------------------------------------------|----|

#### joint_positions *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

관절의 위치입니다. 기본값은 None입니다.

#### joint_velocities *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

관절의 속도입니다. 기본값은 None입니다.

#### \_\_init_\_(joint_positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, joint_velocities: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, joint_efforts: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, joint_indices: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None) = None) → [None](https://docs.python.org/3/library/constants.html#None)

#### joint_efforts *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

관절의 토크입니다. 기본값은 None입니다.

#### joint_indices *: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [slice](https://docs.python.org/3/library/functions.html#slice) | [None](https://docs.python.org/3/library/constants.html#None)* *= None*

관절의 인덱스입니다. 기본값은 None입니다.

관절 인덱스가 슬라이스인 경우, 이는 인덱스가 연속되어 관절의 모든 관절에 해당함을 나타냅니다. 인덱싱 효율을 높이기 위해 슬라이스를 사용합니다.

## 버전 작업

버전 관리를 위한 유틸리티 함수입니다.

**함수:**

| [`get_isaac_sim_version`](#isaaclab.utils.version.get_isaac_sim_version)()   | 성능을 위해 캐시된 Isaac Sim 버전을 Version 객체로 반환합니다.   |
|------------------------------------------------------------------------------|--------------------------------------------------------------------------|
| [`compare_versions`](#isaaclab.utils.version.compare_versions)(v1, v2)       | 두 버전 문자열을 비교하고 비교 결과를 반환합니다.            |

### isaaclab.utils.version.get_isaac_sim_version() → Version

성능을 위해 캐시된 Isaac Sim 버전을 Version 객체로 반환합니다.

이 함수는 [`isaacsim.core.version.get_version()`](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/py/source/extensions/isaacsim.core.version/docs/index.html#isaacsim.core.version.get_version)을 래핑하고 결과를 캐시하여 반복적인 파일 I/O 작업을 방지합니다. underlying Isaac Sim 함수는 매번 파일을 읽기 때문에 자주 호출될 경우 속도가 느려질 수 있습니다.

* **반환값:**
  Isaac Sim 버전을 나타내는 `packaging.version.Version` 객체입니다.
  이 객체는 풍부한 비교 연산자(<, <=, >, >=, ==, !=)를 지원합니다.

### 예시

```pycon
>>> from isaaclab.utils import get_isaac_sim_version
>>> from packaging.version import Version
>>>
>>> isaac_version = get_isaac_sim_version()
>>> print(isaac_version)
5.0.0
>>>
>>> # 자연스러운 버전 비교
>>> if isaac_version >= Version("5.0.0"):
...     print("Isaac Sim 5.0 이상 버전 사용 중")
>>>
>>> # 구성 요소 접근
>>> print(isaac_version.major, isaac_version.minor, isaac_version.micro)
5 0 0
```

### isaaclab.utils.version.compare_versions(v1: [str](https://docs.python.org/3/library/stdtypes.html#str), v2: [str](https://docs.python.org/3/library/stdtypes.html#str)) → [int](https://docs.python.org/3/library/functions.html#int)

두 버전 문자열을 비교하고 비교 결과를 반환합니다.

버전 문자열은 "x.y.z" 형식(여기서 x, y, z는 정수)이 예상됩니다. 버전 문자열은 사전식 순서로 비교됩니다.

#### 참고
이 함수는 하위 호환성을 위해 제공됩니다. 새 코드에서는 `packaging.version.Version` 객체를 직접 사용하고 비교 연산자(`<`, `<=`, `>`, `>=`, `==`, `!=`)를 사용하는 것을 권장합니다.

* **매개변수:**
  * **v1** – 첫 번째 버전 문자열.
  * **v2** – 두 번째 버전 문자열.
* **반환값:**
  - v1이 더 크면 `1`
  - v2가 더 크면 `-1`
  - v1과 v2가 같으면 `0`
* **반환 타입:**
  비교 결과를 나타내는 정수

### 예제

```pycon
>>> from isaaclab.utils import compare_versions
>>> compare_versions("5.0.0", "4.5.0")
1
>>> compare_versions("4.5.0", "5.0.0")
-1
>>> compare_versions("5.0.0", "5.0.0")
0
>>>
>>> # 더 나은 방법: Version 객체 직접 사용
>>> from packaging.version import Version
>>> Version("5.0.0") > Version("4.5.0")
True
```

## Warp 연산

warp 기반 연산을 포함하는 서브 모듈입니다.

**함수:**

| [`convert_to_warp_mesh`](#isaaclab.utils.warp.convert_to_warp_mesh)(points, indices, device)    | 정점과 삼각형으로부터 정의된 메시로 워프 메시 객체 생성.   |
|-------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| [`raycast_dynamic_meshes`](#isaaclab.utils.warp.raycast_dynamic_meshes)(ray_starts, ...[, ...]) | 여러 동적 메시에 대한 레이캐스트 수행.                       |
| [`raycast_mesh`](#isaaclab.utils.warp.raycast_mesh)(ray_starts, ray_directions, mesh)           | 메시에 대한 레이캐스트 수행.                                         |
| [`raycast_single_mesh`](#isaaclab.utils.warp.raycast_single_mesh)(ray_starts, ...[, ...])       | 메시에 대한 레이캐스트 수행.                                         |

### isaaclab.utils.warp.convert_to_warp_mesh(points: [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray), indices: [numpy.ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray), device: [str](https://docs.python.org/3/library/stdtypes.html#str)) → warp.Mesh

정점과 삼각형으로부터 정의된 메시로 워프 메시 객체를 생성합니다.

* **매개변수:**
  * **points** – 메시의 정점. 형태는 (N, 3)이며, N은 정점의 개수입니다.
  * **indices** – 각 삼각형의 정점을 참조하는 메시의 삼각형. 형태는 (M, 3)이며, M은 삼각형/면의 개수입니다.
  * **device** – 메시에 사용할 디바이스.
* **반환값:**
  워프 메시 객체.

### isaaclab.utils.warp.raycast_dynamic_meshes(ray_starts: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), ray_directions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), mesh_ids_wp: wp.Array, mesh_positions_w: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, mesh_orientations_w: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, max_dist: [float](https://docs.python.org/3/library/functions.html#float) = 1000000.0, return_distance: [bool](https://docs.python.org/3/library/functions.html#bool) = False, return_normal: [bool](https://docs.python.org/3/library/functions.html#bool) = False, return_face_id: [bool](https://docs.python.org/3/library/functions.html#bool) = False, return_mesh_id: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)]

여러 동적 메시에 대한 레이캐스트를 수행합니다.

`ray_starts`, `ray_directions`, `ray_hits`가 호환되는 형태와 데이터 타입을 가져야 적절한 실행을 보장할 수 있습니다. 또한, 모두 동일한 프레임에 있어야 합니다.

메시 위치와 회전이 제공되는 경우,它们的形状必须与网格的数量相同。

* **매개변수:**
  * **ray_starts** – 레이의 시작 위치. 형태는 (B, N, 3).
  * **ray_directions** – 각 레이의 방향. 형태는 (B, N, 3).
  * **mesh_ids_wp** – 레이캐스트 대상이 되는 워프 메시 ID. 길이는 (B, M).
  * **mesh_positions_w** – 메시의 월드 위치. 형태는 (B, M, 3).
  * **mesh_orientations_w** – 월드 방향 (wxyz 쿼터니언 형식). 형태는 (B, M, 4).
  * **max_dist** – 레이캐스트 최대 거리. 기본값은 1e6.
  * **return_distance** – 메시와 충돌할 때까지 레이의 거리를 반환할지 여부. 기본값은 False.
  * **return_normal** – 레이와 충돌한 메시 면의 법선을 반환할지 여부. 기본값은 False.
  * **return_face_id** – 레이와 충돌한 메시 면의 면 ID를 반환할지 여부. 기본값은 False.
  * **return_mesh_id** – 레이와 충돌한 메시 면의 메시 ID를 반환할지 여부. 기본값은 False.
    참고: 반환된 텐서의 타입은 torch.int16이므로 32767개 이상의 메시 ID를 가질 수 없습니다.
* **반환값:**
  레이 충돌 위치. 형태는 (B, N, 3).
  : 충돌하지 않은 경우 반환된 텐서에 `float('inf')`가 포함됩니다.

  레이 충돌 거리. 형태는 (B, N,).
  : `return_distance`가 True일 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 경우 반환된 텐서에 `float('inf')`가 포함됩니다.

  레이 충돌 법선. 형태는 (B, N, 3).
  : `return_normal`이 True일 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 경우 반환된 텐서에 `float('inf')`가 포함됩니다.

  레이 충돌 면 ID. 형태는 (B, N,).
  : `return_face_id`가 True일 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 경우 반환된 텐서에 `int(-1)`이 포함됩니다.

  레이 충돌 메시 ID. 형태는 (B, N,).
  : `return_mesh_id`가 True일 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 경우 반환된 텐서에 `-1`이 포함됩니다.

### isaaclab.utils.warp.raycast_mesh(ray_starts: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), ray_directions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), mesh: wp.Mesh, max_dist: [float](https://docs.python.org/3/library/functions.html#float) = 1000000.0, return_distance: [bool](https://docs.python.org/3/library/functions.html#bool) = False, return_normal: [bool](https://docs.python.org/3/library/functions.html#bool) = False, return_face_id: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)]

메시에 대한 레이캐스트를 수행합니다.

`ray_starts`, `ray_directions`, `ray_hits`가 호환되는 형태와 데이터 타입을 가져야 적절한 실행을 보장할 수 있습니다. 또한, 모두 동일한 프레임에 있어야 합니다.

* **매개변수:**
  * **ray_starts** – 레이의 시작 위치. 형태는 (N, 3).
  * **ray_directions** – 각 레이의 방향. 형태는 (N, 3).
  * **mesh** – 레이캐스트 대상이 되는 워프 메시.
  * **max_dist** – 레이캐스트 최대 거리. 기본값은 1e6.
  * **return_distance** – 메시와 충돌할 때까지 레이의 거리를 반환할지 여부. 기본값은 False.
  * **return_normal** – 레이와 충돌한 메시 면의 법선을 반환할지 여부. 기본값은 False.
  * **return_face_id** – 레이와 충돌한 메시 면의 면 ID를 반환할지 여부. 기본값은 False.
* **반환값:**
  레이 충돌 위치. 형태는 (N, 3).
  : 충돌하지 않은 경우 반환된 텐서에 `float('inf')`가 포함됩니다.

  레이 충돌 거리. 형태는 (N,).
  : `return_distance`가 True일 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 경우 반환된 텐서에 `float('inf')`가 포함됩니다.

  레이 충돌 법선. 형태는 (N, 3).
  : `return_normal`이 True일 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 경우 반환된 텐서에 `float('inf')`가 포함됩니다.

  레이 충돌 면 ID. 형태는 (N,).
  : `return_face_id`가 True일 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 경우 반환된 텐서에 `int(-1)`이 포함됩니다.

### isaaclab.utils.warp.raycast_single_mesh(ray_starts: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), ray_directions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), mesh_id: [int](https://docs.python.org/3/library/functions.html#int), max_dist: [float](https://docs.python.org/3/library/functions.html#float) = 1000000.0, return_distance: [bool](https://docs.python.org/3/library/functions.html#bool) = False, return_normal: [bool](https://docs.python.org/3/library/functions.html#bool) = False, return_face_id: [bool](https://docs.python.org/3/library/functions.html#bool) = False) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None)]

메시에 대한 레이캐스트를 수행합니다.

`ray_starts`와 `ray_directions`, 그리고 `ray_hits`는 올바른 실행을 위해 호환 가능한 형태와 데이터 타입을 가져야 한다는 점에 유의하세요. 또한, 모두 동일한 좌표계에 있어야 합니다.

* **매개변수:**
  * **ray_starts** – 레이의 시작 위치. 형태 (B, N, 3).
  * **ray_directions** – 각 레이의 방향. 형태 (B, N, 3).
  * **mesh_id** – 레이캐스트를 수행할 대상인 warp 메시 id.
  * **max_dist** – 레이캐스트의 최대 거리. 기본값은 1e6.
  * **return_distance** – 레이가 메시에 충돌할 때까지의 거리를 반환할지 여부. 기본값은 False.
  * **return_normal** – 레이에 충돌한 메시에 대한 법선 벡터를 반환할지 여부. 기본값은 False.
  * **return_face_id** – 레이에 충돌한 메시의 면 ID를 반환할지 여부. 기본값은 False.
* **반환값:**
  레이 충돌 위치. 형태 (B, N, 3).
  : 충돌하지 않은 레이의 경우 반환된 텐서는 `float('inf')`를 포함합니다.

  레이 충돌 거리. 형태 (B, N,).
  : `return_distance`가 True인 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 레이의 경우 반환된 텐서는 `float('inf')`를 포함합니다.

  레이 충돌 법선. 형태 (B, N, 3).
  : `return_normal`이 True인 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 레이의 경우 반환된 텐서는 `float('inf')`를 포함합니다.

  레이 충돌 면 ID. 형태 (B, N,).
  : `return_face_id`가 True인 경우에만 반환되며, 그렇지 않으면 None을 반환합니다.
    충돌하지 않은 레이의 경우 반환된 텐서는 `int(-1)`을 포함합니다.
