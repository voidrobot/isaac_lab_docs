# isaaclab.app

앱별 기능을 포함하는 서브 패키지입니다.

다음과 같은 기능이 포함됩니다:

* 다양한 구성으로 시뮬레이션 앱 실행
* 시뮬레이션 앱으로 테스트 실행

### 클래스

| [`AppLauncher`](#isaaclab.app.AppLauncher)   | 명령줄 인수 및 환경 변수에 따라 Isaac Sim 애플리케이션을 실행하는 유틸리티 클래스.   |
|----------------------------------------------|--------------------------------------------------------------------------------------------------------------|

## 환경 변수

다음은 환경 변수에 따라 클래스의 동작을 자세히 설명합니다:

* **헤드리스 모드**: 환경 변수 `HEADLESS=1`이면 SimulationApp이 헤드리스 모드로 시작됩니다.
  `LIVESTREAM={1,2}`이면 `HEADLESS` 환경 변수를 대체하고 헤드리스 모드를 강제합니다.
  * `HEADLESS=1`은 앱을 헤드리스 모드로 실행하게 됩니다.
* **라이브스트리밍**: 환경 변수 `LIVESTREAM={1,2}`이면 [라이브스트림](https://docs.omniverse.nvidia.com/app_isaacsim/app_isaacsim/manual_livestream_clients.html)이 활성화됩니다.
  라이브스트림 모드 중 하나가 활성화되면 앱이 헤드리스 모드로 실행됨을 강제합니다.
  * `LIVESTREAM=1`은 공용 네트워크를 통해 [WebRTC 라이브스트림](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/manual_livestream_clients.html#isaac-sim-short-webrtc-streaming-client) 확장을 활성화하여 스트리밍을 가능하게 합니다.
    이로 인해 사용자는 WebRTC 프로토콜을 통해 WebRTC 클라이언트에 연결할 수 있습니다.
  * `LIVESTREAM=2`은 프라이빗 및 로컬 네트워크를 통해 [WebRTC 라이브스트림](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/manual_livestream_clients.html#isaac-sim-short-webrtc-streaming-client) 확장을 활성화하여 스트리밍을 가능하게 합니다.
    이로 인해 사용자는 WebRTC 프로토콜을 통해 WebRTC 클라이언트에 연결할 수 있습니다.

  #### 주의
  각 Isaac Sim 인스턴스는 하나의 스트리밍 클라이언트만 연결할 수 있습니다.
  현재 스트리밍 클라이언트를 서비스 중인 Isaac Sim 인스턴스에 연결하면 두 번째 사용자에게 오류가 발생합니다.
* **공용 IP 주소**: 환경 변수 `LIVESTREAM={1,2}`를 사용할 때 `PUBLIC_IP` 환경 변수를 설정하여 원격 라이브스트리밍을 위한 공용 IP 주소 엔드포인트를 정의합니다.
* **카메라 활성화**: 환경 변수 `ENABLE_CAMERAS`가 1로 설정되면 카메라가 활성화됩니다.
  이는 GUI 없이 시뮬레이터를 실행하면서 뷰포트와 카메라 이미지를 렌더링하는 데 유용합니다.
  * `ENABLE_CAMERAS=1`: GUI 없이도 씬을 렌더링할 수 있는 오프스크린 렌더 파이프라인을 활성화합니다.

  #### 주의
  오프스크린 렌더 파이프라인은 [`isaaclab.sim.SimulationContext`](isaaclab.sim.md#isaaclab.sim.SimulationContext) 클래스와 함께 사용될 때만 작동합니다.
  이는 오프스크린 렌더 파이프라인이 SimulationContext 클래스에서 내부적으로 사용하는 플래그를 활성화하기 때문입니다.

터미널에서 다음 명령을 사용하여 환경 변수를 설정할 수 있습니다:

```bash
export LIVESTREAM=2
export ENABLE_CAMERAS=1
# 파이썬 스크립트 실행
./isaaclab.sh -p scripts/demos/quadrupeds.py
```

대안으로, 파이썬 스크립트에 직접 환경 변수를 설정할 수 있습니다:

```bash
LIVESTREAM=2 ENABLE_CAMERAS=1 ./isaaclab.sh -p scripts/demos/quadrupeds.py
```

## 환경 변수 재정의

환경 변수는 [`AppLauncher`](#isaaclab.app.AppLauncher)를 사용하여 파이썬 스크립트 내에서 재정의할 수 있습니다.
이러한 값은 딕셔너리, [`argparse.Namespace`](https://docs.python.org/3/library/argparse.html#argparse.Namespace) 객체 또는 키워드 인수로 전달할 수 있습니다.
전달된 인수가 기본값이 아닌 경우, 이들은 환경 변수를 재정의합니다.

다음 코드 스니펫은 [`AppLauncher`](#isaaclab.app.AppLauncher)를 다양한 방식으로 사용하는 방법을 보여줍니다:

```python
import argparser

from isaaclab.app import AppLauncher

# add argparse arguments
parser = argparse.ArgumentParser()
# add your own arguments
# ....
# add app launcher arguments for cli
AppLauncher.add_app_launcher_args(parser)
# parse arguments
args = parser.parse_args()

# launch omniverse isaac-sim app
# -- Option 1: Pass the settings as a Namespace object
app_launcher = AppLauncher(args).app
# -- Option 2: Pass the settings as keywords arguments
app_launcher = AppLauncher(headless=args.headless, livestream=args.livestream)
# -- Option 3: Pass the settings as a dictionary
app_launcher = AppLauncher(vars(args))
# -- Option 4: Pass no settings
app_launcher = AppLauncher()

# obtain the launched app
simulation_app = app_launcher.app
```

## 시뮬레이션 앱 실행기

### *class* isaaclab.app.AppLauncher

명령줄 인수 및 환경 변수에 따라 Isaac Sim 애플리케이션을 실행하는 유틸리티 클래스입니다.

이 클래스는 환경 변수, 명령줄 인수(CLI) 또는 입력 키워드 인수를 통해 나타나는 시뮬레이션 앱 설정을 해결합니다.
이러한 설정을 기반으로 시뮬레이션 앱을 실행하고 확장을 로드하도록 구성합니다(출시 후 설정의 일부로).

클래스에 제공된 입력 인수는 해당 환경 변수에서 설정된 값보다 우선순위가 높습니다.
これにより、異なるユーザーの好みに柔軟に対応することができます。

#### 注
明示的に定義された引数は、デフォルト設定以外の値に設定されている場合のみ優先されます。
たとえば、`livestream` 引数はデフォルトで -1 です。`livestream` 引数が -1 より大きい値に設定されている場合のみ、`LIVESTREAM` 環境変数をオーバーライドします。
つまり、`livestream=-1` の場合は、環境変数 `LIVESTREAM` の値が使用されます。

**メソッド:**

| [`__init__`](#isaaclab.app.AppLauncher.__init__)([launcher_args])                  | 入力設定に基づいて [SimulationApp](https://docs.isaacsim.omniverse.nvidia.com/latest/py/source/extensions/isaacsim.simulation_app/docs/index.html#isaacsim.simulation_app.SimulationApp) インスタンスを作成します。   |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`add_app_launcher_args`](#isaaclab.app.AppLauncher.add_app_launcher_args)(parser) | 既存の argument parser オブジェクトに AppLauncher 引数を設定するユーティリティ関数。                                                                                                                           |

**属性:**

| [`app`](#isaaclab.app.AppLauncher.app)   | 起動された SimulationApp。   |
|------------------------------------------|-------------------------------|

#### \_\_init_\_(launcher_args: [Namespace](https://docs.python.org/3/library/argparse.html#argparse.Namespace) | [dict](https://docs.python.org/3/library/stdtypes.html#dict) | [None](https://docs.python.org/3/library/constants.html#None) = None, \*\*kwargs)

入力設定に基づいて [SimulationApp](https://docs.isaacsim.omniverse.nvidia.com/latest/py/source/extensions/isaacsim.simulation_app/docs/index.html#isaacsim.simulation_app.SimulationApp) インスタンスを作成します。

* **Parameters:**
  * **launcher_args** – AppLauncher を使用してパースし、SimulationApp に設定する入力引数。
    デフォルトは None で、これは空の辞書を渡すのと等価です。可能な引数の詳細な説明については、[SimulationApp](https://docs.isaacsim.omniverse.nvidia.com/latest/py/source/extensions/isaacsim.simulation_app/docs/index.html#isaacsim.simulation_app.SimulationApp) のドキュメントを参照してください。
  * **\*\*kwargs** – `launcher_args` にマージされる追加のキーワード引数。
    argparse インターフェースで一部の引数を渡し、他の引数を直接 AppLauncher に渡したいユーザーのための便利な機能です。
    `launcher_args` と重複する引数がある場合は ValueError が発生します。
* **Raises:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `launcher_args` と `kwargs` の間に共通/重複する引数がある場合。
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – `launcher_args` と `kwargs` の組み合わせが、AppLauncher が希望するアプリ構成を解決するために必要な引数が不足している場合。
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 関連する環境値に互換性のないまたは未定義の値が割り当てられた場合、
        例えば `LIVESTREAM`。

#### *property* app *: isaacsim.SimulationApp*

起動された SimulationApp。

#### *static* add_app_launcher_args(parser: [ArgumentParser](https://docs.python.org/3/library/argparse.html#argparse.ArgumentParser)) → [None](https://docs.python.org/3/library/constants.html#None)

既存の argument parser オブジェクトに AppLauncher 引数を設定するユーティリティ関数。

この関数は `argparse.ArgumentParser` オブジェクトを取り、SimulationApp に取り込むための既存の引数に対してサニティチェックを行います。
その後、SimulationApp に関連するカスタムコマンドライン引数を入力の [`argparse.ArgumentParser`](https://docs.python.org/3/library/argparse.html#argparse.ArgumentParser) インスタンスに追加します。
これにより、コマンドライン引数を使用して環境変数をオーバーライドすることができます。

現在、argparser オブジェクトに次のパラメータを追加します:

* `headless` (bool): True の場合、アプリはヘッドレス（GUI なし）モードで起動されます。
  この値は `HEADLESS` 環境変数と同じマッピングになります。
  False の場合、ヘッドレスモードは `HEADLESS` 環境変数によって決定されます。
* `livestream` (int): {1, 2} のいずれかの場合、ライ브ストリーミングとヘッドレスモードが有効になります。
  この値は `LIVESTREAM` 環境変数と同じマッピングになります。
  `-1` の場合、ライブストリーミングは `LIVESTREAM` 環境変数によって決定されます。
  有効なオプションは:
  - `0`: 無効
  - `1`: 공용 네트워크를 통한 [WebRTC](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/manual_livestream_clients.html#isaac-sim-short-webrtc-streaming-client)
  - `2`: 로컬/개인 네트워크를 통한 [WebRTC](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/manual_livestream_clients.html#isaac-sim-short-webrtc-streaming-client)
* `enable_cameras` (bool): True이면 앱이 헤드리스 모드에서도 카메라 센서를 활성화하고 렌더링합니다. 환경에 카메라 센서가 포함된 경우 이 플래그는 반드시 True로 설정해야 합니다. 이 값은 `ENABLE_CAMERAS` 환경 변수와 동일한 방식으로 매핑됩니다. False인 경우, `enable_cameras` 모드는 `ENABLE_CAMERAS` 환경 변수에 따라 결정됩니다.
* `device` (str): 시뮬레이션을 실행할 장치.
  유효한 옵션은 다음과 같습니다:
  - `cpu`: CPU 사용.
  - `cuda`: 장치 ID `0`인 GPU 사용.
  - `cuda:N`: 장치 ID가 N인 GPU 사용. 예를 들어, “cuda:0”.
* `experience` (str): SimulationApp 시작 시 로드할 경험 파일. 상대 경로가 제공된 경우, Isaac Sim 및 Isaac Lab의 `apps` 폴더(순서대로) 기준으로 해석됩니다.

  빈 문자열로 제공된 경우, 경험 파일은 명령줄 플래그에 따라 결정됩니다:
  * headless와 enable_cameras가 모두 True이면, 경험 파일은 `isaaclab.python.headless.rendering.kit`로 설정됩니다.
  * headless가 False이고 enable_cameras가 True이면, 경험 파일은 `isaaclab.python.rendering.kit`로 설정됩니다.
  * headless와 enable_cameras가 모두 False이면, 경험 파일은 `isaaclab.python.kit`로 설정됩니다.
  * headless가 True이고 enable_cameras가 False이면, 경험 파일은 `isaaclab.python.headless.kit`로 설정됩니다.
* `kit_args` (str): Omniverse Kit에 직접 전달할 선택적 명령줄 인수. 인수는 공백으로 구분된 단일 문자열로 결합해야 합니다.
  사용 예시: –kit_args “–ext-folder=/path/to/ext1 –ext-folder=/path/to/ext2”

* **매개변수:**
  **parser** – AppLauncher 특정 옵션으로 확장할 인수 파서 인스턴스.
