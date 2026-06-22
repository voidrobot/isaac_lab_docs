# 확장 개발

Omniverse의 모든 것은 확장 자체이거나 확장들의 집합(애플리케이션)입니다. 이들은 옴니버스 생태계의 원자들을 형성하는 모듈화된 패키지입니다. 각 확장은 다른 확장이나 독립형 애플리케이션에서 사용할 수 있는 기능 집합을 제공합니다. `config` 디렉터리에 `extension.toml` 파일이 포함된 경우 해당 폴더가 확장으로 인식됩니다. 확장에 대한 더 자세한 내용은 [Omniverse 문서](https://docs.omniverse.nvidia.com/kit/docs/kit-manual/latest/guide/extensions_basic.html)를 참고하세요.

Isaac Lab의 각 확장은 파이썬 패키지로 작성되며 다음 구조를 따릅니다:

```bash
<extension-name>
├── config
│   └── extension.toml
├── docs
│   ├── CHANGELOG.md
│   └── README.md
├── <extension-name>
│   ├── __init__.py
│   ├── ....
│   └── scripts
├── setup.py
└── tests
```

`config/extension.toml` 파일은 확장의 메타데이터를 포함합니다. 여기에는 이름, 버전, 설명, 종속성 등이 들어 있습니다. 이 정보는 옴니버스 API가 확장을 로드하는 데 사용됩니다. `docs` 디렉터리에는 확장에 대한 더 자세한 정보를 포함한 확장 문서와 각 버전에서 이루어진 변경 사항을 기록한 CHANGELOG 파일이 있습니다.

`<extension-name>` 디렉터리에는 확장의 주요 파이썬 패키지가 포함됩니다. 확장이 활성화될 때 옴니버스에 로드되는 파이썬 기반 애플리케이션을 보관하기 위한 `scripts` 디렉터리를 포함할 수도 있습니다. [Extension Manager](https://docs.omniverse.nvidia.com/kit/docs/kit-manual/latest/guide/extensions_basic.html)를 사용하면 됩니다.

더 구체적으로, 확장이 활성화될 때 `config/extension.toml` 파일에 지정된 파이썬 모듈이 로드되고, `omni.ext.IExt` 클래스의 자식을 포함하는 스크립트가 실행됩니다.

```python
import omni.ext

class MyExt(omni.ext.IExt):
   """My extension application."""

   def on_startup(self, ext_id):
      """Called when the extension is loaded."""
      pass

   def on_shutdown(self):
      """Called when the extension is unloaded.

      It releases all references to the extension and cleans up any resources.
      """
      pass
```

옴니버스에 확장을 로드하는 과정은 자동으로 이루어지지만, 독립형 애플리케이션에서 파이썬 패키지를 사용하려면 추가 단계가 필요합니다. 빌드 프로세스를 간소화하고 옴니버스에서 사용되는 [premake](https://premake.github.io/) 빌드 시스템을 이해하는 필요성을 없애기 위해, 확장에서 제공하는 파이썬 모듈을 직접 빌드하기 위해 [setuptools](https://setuptools.readthedocs.io/en/latest/) 파이썬 패키지를 사용합니다. 이는 확장 디렉터리의 `setup.py` 파일을 통해 수행됩니다.

#### NOTE
[Extension Manager](https://docs.omniverse.nvidia.com/prod_extensions/prod_extensions/ext_extension-manager.html)를 통해 옴니버스에만 로드되는 확장의 경우 `setup.py` 파일이 필수가 아닙니다.

마지막으로, `tests` 디렉터리에는 확장의 단위 테스트가 포함됩니다. 이 테스트는 [unittest](https://docs.python.org/3/library/unittest.html) 프레임워크를 사용해 작성됩니다. 옴니버스도 유사한 [테스트 프레임워크](https://docs.omniverse.nvidia.com/kit/docs/kit-manual/104.0/guide/testing_exts_python.html)를 제공한다는 점을 참고하는 것이 중요합니다. 그러나 이 프레임워크는 빌드 프로세스를 거쳐야 하며 독립형 애플리케이션에서 파이썬 모듈 테스트를 지원하지 않습니다.

## 맞춤형 확장 종속성 관리

특정 확장은 확장을 사용하기 전에 추가 패키지 설치를 필요로 하는 종속성을 가질 수 있습니다. 파이썬 종속성은 [setuptools](https://setuptools.readthedocs.io/en/latest/) 패키지를 통해 처리되며 `setup.py` 파일에 지정되지만, [ROS](https://www.ros.org/) 패키지나 [apt](https://en.wikipedia.org/wiki/APT_(software)) 패키지와 같은 비파이썬 종속성은 setuptools에서 처리되지 않습니다. 이런 종류의 종속성을 처리하려면 추가 절차가 필요합니다.

`extension.toml` 파일의 `isaac_lab_settings` 섹션 아래에서 두 가지 유형의 종속성을 지정할 수 있습니다.

1. **apt_deps**: 설치해야 할 apt 패키지 목록입니다. 이들은 [apt](https://ubuntu.com/server/docs/package-management) 패키지 관리자를 통해 설치됩니다.
2. **ros_ws**: ROS 패키지가 포함된 ROS 작업 공간의 경로입니다. 이들은 [rosdep](https://docs.ros.org/en/humble/Tutorials/Intermediate/Rosdep.html) 종속성 관리자를 통해 설치됩니다.

예를 들어, 다음 `extension.toml` 파일은 확장의 종속성을 지정합니다:

```toml
[isaac_lab_settings]
# apt dependencies
apt_deps = ["libboost-all-dev"]

# ROS workspace
# note: if this path is relative, it is relative to the extension directory's root
ros_ws = "/home/user/catkin_ws"
```

이 종속성들은 `tools` 디렉터리에 제공된 `install_deps.py` 스크립트를 사용하여 설치됩니다. 모든 확장의 모든 종속성을 설치하려면 다음 명령을 실행하세요:

```bash
# 레포지토리 루트에서 실행
# 스크립트는 설치할 종속성 유형과 확장 디렉터리 경로를 기대합니다
# 사용 가능한 유형: 'apt', 'rosdep' 및 'all'
python tools/install_deps.py all ${ISAACLAB_PATH}/source
```

#### NOTE
현재 이 스크립트는 `Dockerfile.base` 및 `Dockerfile.ros2` 빌드 프로세스 중에 자동으로 실행됩니다. 이를 통해 확장을 빌드하기 전에 각각의 ‘apt’ 및 ‘rosdep’ 종속성이 설치됩니다.

### 독립형 애플리케이션

일반적인 옴니버스 워크플로에서는 시뮬레이터를 먼저 시작한 다음 확장을 활성화합니다. 파이썬 모듈 및 기타 파이썬 애플리케이션의 로드는 백그라운드에서 자동으로 이루어지며, 이는 권장되는 워크플로이지만 항상 가능한 것은 아닙니다.

예를 들어, 로봇 강화 학습을 고려해 봅시다. 시묬레이션 단계를 완전히 통제하고 대신 비동기적으로 결과를 기다리는 것이 아니라 시점이 언제 업데이트되는지를 제어하는 것이 필수적입니다. 이런 경우에는 시뮬레이션을 직접 제어해야 하므로 독립형 애플리케이션을 작성해야 합니다. 이러한 애플리케이션은 [`AppLauncher`](../../api/lab/isaaclab.app.md#isaaclab.app.AppLauncher)를 사용하여 시뮬레이터를 시작한 다음 [`SimulationContext`](../../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationContext)를 통해 시뮬레이션을 직접 제어한다는 점에서 기능적으로 유사합니다. 이러한 경우, 확장의 파이썬 모듈은 앱이 시작된 후에 **반드시** 가져와야 합니다. 앱 시작 전에 가져오면 모듈 누락 오류가 발생합니다.

다음 스니펫은 독립형 애플리케이션을 작성하는 방법을 보여줍니다:

```python
"""Launch Isaac Sim Simulator first."""

from isaaclab.app import AppLauncher

# launch omniverse app
app_launcher = AppLauncher(headless=False)
simulation_app = app_launcher.app


"""Rest everything follows."""

from isaaclab.sim import SimulationContext

if __name__ == "__main__":
   # get simulation context
   simulation_context = SimulationContext()
   # reset and play simulation
   simulation_context.reset()
   # step simulation
   simulation_context.step()
   # stop simulation
   simulation_context.stop()

   # close the simulation
   simulation_app.close()
```

시뮬레이터를 시작하기 전에 다른 코드를 실행하는 것은 필수가 아닙니다. 확장은 시뮬레이터 시작時に 핫 로드되기 때문입니다. 많은 옴니버스 모듈은 시뮬레이터가 시작된 후에야 사용할 수 있습니다. 자세한 내용은 Isaac Lab [튜토리얼](../../tutorials/index.md#tutorials)을 참조하는 것을 권장합니다.
