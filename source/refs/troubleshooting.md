# 트러블슈팅 팁

#### 참고
다음은 일반적인 워크플로우에서 우리가 자주 사용하는 트러블슈팅 팁과 방법을 정리한 목록입니다.
Omniverse의 [troubleshooting 페이지](https://docs.omniverse.nvidia.com/kit/docs/kit-manual/latest/guide/linux_troubleshooting.html)를 추가로 확인하여 더 많은 도움을 받을 수 있습니다.

## 물리 시뮬레이션 안정성 문제 디버깅

Isaac Lab에 새로운 로봇을 가져오거나 새로운 환경을 설정할 때, 자산이 합리적인 시뮬레이션 파라미터로 튜닝되지 않은 경우 시뮬레이션 불안정이 자주 발생할 수 있습니다.
강화 학습 시나리오에서는 시뮬레이션의 잘못된 상태로 인해 학습 파이프라인에 NaNs가 전파되는 경우가 많습니다.

이런 경우 다음 가이드를 참고하는 것을 권장합니다.
[Articulation and Robot Simulation Stability Guide](https://docs.omniverse.nvidia.com/kit/docs/omni_physics/latest/dev_guide/guides/articulation_stability_guide.html)
이 가이드에서는 로봇 시뮬레이션의 안정성을 높이기 위한 다양한 시뮬레이션 파라미터와 모범 사례를 권장합니다.

또한, [Omniverse PhysX Visual Debugger](https://docs.omniverse.nvidia.com/kit/docs/omni_physics/latest/extensions/ux/source/omni.physx.pvd/docs/dev_guide/physx_visual_debugger.html)는 PhysX 시뮬레이션 데이터 기록을 허용하며, 이는 시뮬레이션 문제를 진단하고 디버깅 과정을 돕는 데 자주 사용됩니다.

Isaac Lab에서 OmniPVD 캡처를 활성화하려면, Isaac Lab 프로세스를 시작할 때 명령줄 프롬프트에 관련 키트 인수를 추가하세요

```bash
./isaaclab.sh -p scripts/demos/bipeds.py --kit_args "--/persistent/physics/omniPvdOvdRecordingDirectory=/tmp/ --/physics/omniPvdOutputEnabled=true" --headless
```

## 시뮬레이터의 내부 로그 확인

독립형 스크립트에서 시뮬레이터를 실행할 때, 터미널에 경고와 오류가 로그됩니다. 동시에 내부 메시지도 파일에 로그됩니다. 이는 디버깅과 시뮬레이터의 내부 상태를 이해하는 데 유용합니다. 시스템에 따라 로그 파일은 다음 [페이지](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_faq.html#common-path-locations)에 지정된 위치에서 찾을 수 있습니다.

로그 파일의 정확한 위치를 얻으려면 독립형 스크립트를 실행할 때 터미널 출력의 처음 몇 줄을 확인해야 합니다. 로그 파일 위치는 터미널 출력의 시작 부분에 출력됩니다. 예를 들어:

```bash
[INFO] Using python from: /home/${USER}/git/IsaacLab/_isaac_sim/python.sh
...
Passing the following args to the base kit application:  []
Loading user config located at: '.../data/Kit/Isaac-Sim/2023.1/user.config.json'
[Info] [carb] Logging to file: '.../logs/Kit/Isaac-Sim/2023.1/kit_20240328_183346.log'
```

위 예시에서 로그 파일은 `.../logs/Kit/Isaac-Sim/2023.1/kit_20240328_183346.log`에 위치하며, 여기서 `...`은 사용자 로그 디렉터리의 경로를 나타냅니다. 로그 파일 이름은 `kit_20240328_183346.log`입니다.

이 파일을 열어 시뮬레이터의 내부 로그를 확인할 수 있습니다. 또한 문제를 보고할 때는 이 로그 파일을 포함하여 문제 해결에 도움을 줄 수 있습니다.

## 시뮬레이터의 로그 채널 레벨 변경

기본적으로 시뮬레이터는 터미널에서 `WARN` 레벨 이상의 메시지를 로그합니다. 더 상세한 로그를 얻으려면 로그 채널 레벨을 변경할 수 있습니다. 로그 채널 레벨은 Omniverse의 로깅 시스템을 통해 설정할 수 있습니다.

더 상세한 로그를 얻으려면 다음 플래그와 함께 애플리케이션을 실행할 수 있습니다:

* `--info`: 이 플래그는 `INFO` 레벨 이상의 메시지를 로그합니다.
* `--verbose`: 이 플래그는 `VERBOSE` 레벨 이상의 메시지를 로그합니다.

예를 들어, 상세한 로그를 출력하며 독립형 스크립트를 실행하려면 다음 명령을 사용할 수 있습니다:

```bash
# Run the standalone script with info logging
./isaaclab.sh -p scripts/tutorials/00_sim/create_empty.py --headless --info
```

더 세밀한 제어가 필요하면 `logger` 모듈을 통해 로그 채널을 수정할 수 있습니다. 자세한 내용은 해당 [문서](https://docs.python.org/3/library/logging.html)를 참조하세요.

## 시뮬레이션 시작 시 긴 로드 시간 관찰

시뮬레이터를 처음 실행할 때는 셰이더 컴파일과 자산 로드로 인해 오래 걸릴 수 있습니다. 이후 실행은 더 빠르게 시작되지만, 여전히 некоторое время이 소요될 수 있습니다.

Isaac Sim 앱이 로드된 후 환경 생성 시간이 환경의 수에 따라 선형적으로 증가할 수 있다는 점을 유의하세요. 수천 개의 환경을 실행하거나 각 환경에 많은 자산이 포함된 경우 로드 시간이 더 길어질 수 있습니다. 우리는 이 시간을 지속적으로 개선하기 위해 노력하고 있습니다.

Isaac Sim 인스턴스가 이미 실행 중인 상태에서 다른 프로세스에서 또 다른 Isaac Sim 인스턴스를 시작하면 처음에 시작 시점에서 멈추는 것처럼 보일 수 있습니다. 두 번째 프로세스는 셰이더 컴파일이 더 느리기 때문에 시작하는 데 더 오래 걸리므로 잠시 기다려 주세요.

## GPU에서 시뮬레이션 실행 시 “PhysX 오류” 수신

GPU 파이프라인을 사용할 때, 물리 시뮬레이션에 사용되는 버퍼는 시뮬레이션 시작 시 GPU에 한 번만 할당됩니다. 이는 장면의 충돌 또는 객체 수가 변경되더라도 버퍼가 동적으로 증가하지 않음을 의미합니다. 장면의 충돌 또는 객체 수가 버퍼 크기를 초과하면 다음과 같은 오류와 함께 시뮬레이션이 실패할 수 있습니다:

```bash
PhysX error: the application need to increase the PxgDynamicsMemoryConfig::foundLostPairsCapacity
parameter to 3072, otherwise the simulation will miss interactions
```

이 경우, [`SimulationContext`](../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationContext) 클래스에 전달되는 버퍼의 크기를 증가시켜야 합니다. 버퍼 크기는 [`PhysxCfg`](../api/lab/isaaclab.sim.md#isaaclab.sim.PhysxCfg) 클래스의 [`gpu_found_lost_pairs_capacity`](../api/lab/isaaclab.sim.md#isaaclab.sim.PhysxCfg.gpu_found_lost_pairs_capacity) 파라미터를 설정하여 증가시킬 수 있습니다. 예를 들어 버퍼 크기를 4096으로 늘리려면 다음 코드를 사용할 수 있습니다:

```python
import isaaclab.sim as sim_utils

sim_cfg = sim_utils.SimulationConfig()
sim_cfg.physx.gpu_found_lost_pairs_capacity = 4096
sim = SimulationContext(sim_params=sim_cfg)
```

[`SimulationCfg`](../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg)의 문서를 참조하여 시뮬레이션을 구성하는 데 사용할 수 있는 파라미터에 대한 자세한 내용을 확인하세요.

## 시뮬레이터의 메모리 누수 방지

Isaac Sim 시뮬레이터에서 메모리 누수는 C++ 콜백이 Python 객체에 등록될 때 발생할 수 있습니다. 이는 클래스 내 콜백 함수가 연결된 Python 객체에 대한 참조를 유지하기 때문입니다.その結果, Python의 가비지 컬렉션은 이러한 객체와 관련된 메모리를 회수할 수 없어 해당 C++ 객체가 소멸되는 것을 방지합니다. 시간이 지남에 따라これがメモリリークとリソース使用量の増加を引き起こす可能性があります。

Isaac Sim 시뮬레이터에서 메모리 누수를 방지하려면 시뮬레이터에 콜백을 등록할 때 약한 참조를 사용하는 것이 필수적입니다.これにより、Pythonオブジェクトが不要になったときにガベージコレクションされることを保証し、メモリリークを回避することができます。この目的のために、Python標準ライブラリの[weakref](https://docs.python.org/3/library/weakref.html)モジュールを使用することができます。

例えば、シミュレータに登録する必要があるコールバック関数`on_event_callback`を持つクラスを考えます。もし`MyClass`オブジェクトへの強参照を使ってコールバックを渡すと、`MyClass`オブジェクトの参照カウントが増加します。これにより、`MyClass`オブジェクトが不要になったときにガベージコレクションされなくなります（つまり、`__del__`デストラクタが呼ばれなくなります）。

```python
import omni.kit

class MyClass:
    def __init__(self):
        app_interface = omni.kit.app.get_app_interface()
        self._handle = app_interface.get_post_update_event_stream().create_subscription_to_pop(
            self.on_event_callback
        )

    def __del__(self):
        self._handle.unsubscribe()
        self._handle = None

    def on_event_callback(self, event):
        # do something with the message
```

この問題を修正するには、コールバックを登録するときに弱い参照を使用することが重要です。このアプローチはコードに若干の冗長性を加えますが、`MyClass`オブジェクトが不要になったときにガベージコレクションされることを保証します。以下が修正されたコードです：

```python
import omni.kit
import weakref

class MyClass:
    def __init__(self):
        app_interface = omni.kit.app.get_app_interface()
        self._handle = app_interface.get_post_update_event_stream().create_subscription_to_pop(
            lambda event, obj=weakref.proxy(self): obj.on_event_callback(event)
        )

    def __del__(self):
        self._handle.unsubscribe()
        self._handle = None

    def on_event_callback(self, event):
        # do something with the message
```

この改訂されたコードでは、コールバックを登録するときに弱い参照`weakref.proxy(self)`を使用しており、`MyClass`オブジェクトが適切にガベージコレクションされるようになります。

このパターンに従うことで、メモリリークを防ぎ、より効率的で安定したシミュレーションを維持できます。

##クラッシュからのエラーログ理解

多くの場合、シミュレータのクラッシュは実装의 バグによるものです。これにより、ターミナルに例외があふれ出し、その中にはシミュレーションアプリケーションの`__del__()`デストラクタを呼び出すPythonインタプリタから来るものもあります。これらは通常次のように見えます：

```bash
...

[INFO]: Completed setting up the environment...

Traceback (most recent call last):
File "scripts/imitation_learning/robomimic/collect_demonstrations.py", line 166, in <module>
    main()
File "scripts/imitation_learning/robomimic/collect_demonstrations.py", line 126, in main
    actions = pre_process_actions(delta_pose, gripper_command)
File "scripts/imitation_learning/robomimic/collect_demonstrations.py", line 57, in pre_process_actions
    return torch.concat([delta_pose, gripper_vel], dim=1)
TypeError: expected Tensor as element 1 in argument 0, but got int
Exception ignored in: <function _make_registry.<locals>._Registry.__del__ at 0x7f94ac097f80>
Traceback (most recent call last):
File "../IsaacLab/_isaac_sim/kit/extscore/omni.kit.viewport.registry/omni/kit/viewport/registry/registry.py", line 103, in __del__
File "../IsaacLab/_isaac_sim/kit/extscore/omni.kit.viewport.registry/omni/kit/viewport/registry/registry.py", line 98, in destroy
TypeError: 'NoneType' object is not callable
Exception ignored in: <function _make_registry.<locals>._Registry.__del__ at 0x7f94ac097f80>
Traceback (most recent call last):
File "../IsaacLab/_isaac_sim/kit/extscore/omni.kit.viewport.registry/omni/kit/viewport/registry/registry.py", line 103, in __del__
File "../IsaacLab/_isaac_sim/kit/extscore/omni.kit.viewport.registry/omni/kit/viewport/registry/registry.py", line 98, in destroy
TypeError: 'NoneType' object is not callable
Exception ignored in: <function SettingChangeSubscription.__del__ at 0x7fa2ea173e60>
Traceback (most recent call last):
File "../IsaacLab/_isaac_sim/kit/kernel/py/omni/kit/app/_impl/__init__.py", line 114, in __del__
AttributeError: 'NoneType' object has no attribute 'get_settings'
Exception ignored in: <function RegisteredActions.__del__ at 0x7f935f5cae60>
Traceback (most recent call last):
File "../IsaacLab/_isaac_sim/extscache/omni.kit.viewport.menubar.lighting-104.0.7/omni/kit/viewport/menubar/lighting/actions.py", line 345, in __del__
File "../IsaacLab/_isaac_sim/extscache/omni.kit.viewport.menubar.lighting-104.0.7/omni/kit/viewport/menubar/lighting/actions.py", line 350, in destroy
TypeError: 'NoneType' object is not callable
2022-12-02 15:41:54 [18,514ms] [Warning] [carb.audio.context] 1 contexts were leaked
../IsaacLab/_isaac_sim/python.sh: line 41: 414372 Segmentation fault      (core dumped) $python_exe "$@" $args
There was an error running python
```

これは Isaac Sim シミュレータでスタンドアロンスクリプトを実行するときに発生する既知のエラーです。実際のエラーログを見るには、`registry`によってスローされた例外をスクロールアップしてください。

上記の場合、実際のエラーは以下の通りです：

```bash
Traceback (most recent call last):
File "scripts/imitation_learning/robomimic/tools/collect_demonstrations.py", line 166, in <module>
    main()
File "scripts/imitation_learning/robomimic/tools/collect_demonstrations.py", line 126, in main
    actions = pre_process_actions(delta_pose, gripper_command)
File "scripts/imitation_learning/robomimic/tools/collect_demonstrations.py", line 57, in pre_process_actions
    return torch.concat([delta_pose, gripper_vel], dim=1)
TypeError: expected Tensor as element 1 in argument 0, but got int
```
