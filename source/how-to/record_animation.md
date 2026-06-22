# 시뮬레이션 애니메이션 녹화

Isaac Lab은 물리 시뮬레이션의 애니메이션을 녹화하기 위한 두 가지 접근 방식을 지원합니다: **Stage Recorder**와 **OVD Recorder**입니다.
두 가지 모두 Omniverse에서 재생할 수 있는 USD 출력을 생성하지만, 작동 방식과 사용 시점이 다릅니다.

[Stage Recorder](https://docs.omniverse.nvidia.com/extensions/latest/ext_animation_stage-recorder.html) 확장은 시뮬레이션 중에 스테이지의 모든 움직임과 USD 속성 변경을 감지하여 **시간 샘플링된 데이터**로 기록합니다.
결과는 애니메이션이 적용된 변경 사항만 캡처하는 USD 파일이 되며(전체 씬은 아님) 기록 당시의 원래 스테이지 계층 구조와 일치합니다.
이로 인해 재생 또는 렌더링을 위해 서브레이어로 쉽게 추가할 수 있습니다.

이 방법은 Isaac Lab의 UI를 통해 [`BaseEnvWindow`](../api/lab/isaaclab.envs.ui.md#isaaclab.envs.ui.BaseEnvWindow)에 내장되어 있습니다.
그러나 시뮬레이션의 애니메이션을 녹화하려면 [Fabric](https://docs.omniverse.nvidia.com/kit/docs/usdrt/latest/docs/usd_fabric_usdrt.html)을 비활성화하여 움직임과 USD 속성 변경과 같은 모든 변경 사항을 USD 스테이지에 읽고 쓸 수 있어야 합니다.

**OVD Recorder**는 더 확장 가능하거나 자동화된 워크플로우를 위해 설계되었습니다.
OmniPVD를 사용하여 재생된 스테이지에서 시뮬레이션된 물리 현상을 캡처한 다음 이를 직접 animated USD 파일에 **베이킹**합니다.
Fabric이 활성화된 상태에서도 작동하며 CLI 인수로 실행됩니다.
애니메이션된 USD는 비용이 많이 드는 물리 연산을 시뮬레이션하지 않고 타임라인 창을 스크러브하여 빠르게 재생하고 검토할 수 있습니다.

#### 참고
Omniverse는 USD 프리미티브에 대해 **물리 시뮬레이션** 또는 **애니메이션 재생** 중 하나만 지원하며, 동시에 둘 다 지원하지 않습니다.
애니메이션을 적용하려는 프리미티브의 물리 시뮬레이션을 비활성화하십시오.

## Stage Recorder

Isaac Lab에서 Stage Recorder는 [`BaseEnvWindow`](../api/lab/isaaclab.envs.ui.md#isaaclab.envs.ui.BaseEnvWindow) 클래스에 통합되어 있습니다.
이것은 UI를 통해 직접 작동하여 물리 시뮬레이션을 시각적으로 캡처하는 가장 쉬운 방법입니다.

녹화를 하려면 Fabric을 비활성화해야 합니다—이렇게 하면 기록기가 USD 변경 사항을 추적하고 기록할 수 있습니다.

### Stage Recorder 설정

Isaac Lab은 `base_env_window.py`에서 Stage Recorder에 합리적인 기본값을 설정합니다.
필요한 경우, Omniverse Create에서 Stage Recorder 확장을 직접 사용하여 이러한 설정을 재정의하거나 검사할 수 있습니다.

### base_env_window.py에서 사용되는 설정

```python
    def _toggle_recording_animation_fn(self, value: bool):
        """Toggles the animation recording."""
        if value:
            # log directory to save the recording
            if not hasattr(self, "animation_log_dir"):
                # create a new log directory
                log_dir = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
                self.animation_log_dir = os.path.join(os.getcwd(), "recordings", log_dir)
            # start the recording
            _ = omni.kit.commands.execute(
                "StartRecording",
                target_paths=[("/World", True)],
                live_mode=True,
                use_frame_range=False,
                start_frame=0,
                end_frame=0,
                use_preroll=False,
                preroll_frame=0,
                record_to="FILE",
                fps=0,
                apply_root_anim=False,
                increment_name=True,
                record_folder=self.animation_log_dir,
                take_name="TimeSample",
            )
        else:
            # stop the recording
            _ = omni.kit.commands.execute("StopRecording")
            # save the current stage
            source_layer = self.stage.GetRootLayer()
            # output the stage to a file
            stage_usd_path = os.path.join(self.animation_log_dir, "Stage.usd")
            source_prim_path = "/"
            # creates empty anon layer
            temp_layer = Sdf.Find(stage_usd_path)
            if temp_layer is None:
                temp_layer = Sdf.Layer.CreateNew(stage_usd_path)
            temp_stage = Usd.Stage.Open(temp_layer)
            # update stage data
            UsdGeom.SetStageUpAxis(temp_stage, UsdGeom.GetStageUpAxis(self.stage))
            UsdGeom.SetStageMetersPerUnit(temp_stage, UsdGeom.GetStageMetersPerUnit(self.stage))
            # copy the prim
            Sdf.CreatePrimInLayer(temp_layer, source_prim_path)
            Sdf.CopySpec(source_layer, source_prim_path, temp_layer, source_prim_path)
            # set the default prim
            temp_layer.defaultPrim = Sdf.Path(source_prim_path).name
            # remove all physics from the stage
            for prim in temp_stage.TraverseAll():
                # skip if the prim is an instance
                if prim.IsInstanceable():
                    continue
                # if prim has articulation then disable it
                if prim.HasAPI(UsdPhysics.ArticulationRootAPI):
                    prim.RemoveAPI(UsdPhysics.ArticulationRootAPI)
                    prim.RemoveAPI(PhysxSchema.PhysxArticulationAPI)
                # if prim has rigid body then disable it
                if prim.HasAPI(UsdPhysics.RigidBodyAPI):
                    prim.RemoveAPI(UsdPhysics.RigidBodyAPI)
                    prim.RemoveAPI(PhysxSchema.PhysxRigidBodyAPI)
                # if prim is a joint type then disable it
                if prim.IsA(UsdPhysics.Joint):
                    prim.GetAttribute("physics:jointEnabled").Set(False)
            # resolve all paths relative to layer path
            omni.usd.resolve_paths(source_layer.identifier, temp_layer.identifier)
            # save the stage
            temp_layer.Save()
            # print the path to the saved stage
            print("Recording completed.")
            print(f"\tSaved recorded stage to    : {stage_usd_path}")
            print(f"\tSaved recorded animation to: {os.path.join(self.animation_log_dir, 'TimeSample_tk001.usd')}")
            print("\nTo play the animation, check the instructions in the following link:")
            print(
                "\thttps://docs.omniverse.nvidia.com/extensions/latest/ext_animation_stage-recorder.html#using-the-captured-timesamples"
            )
            print("\n")
            # reset the log directory
            self.animation_log_dir = None
```

### 사용 예시

독립형 Isaac Lab 환경에서 `--disable_fabric` 플래그를 전달합니다:

```bash
./isaaclab.sh -p scripts/environments/state_machine/lift_cube_sm.py --num_envs 8 --device cpu --disable_fabric
```

실행한 후, Isaac Lab UI 창에 "Animation 기록" 버튼이 표시됩니다.
클릭하여 녹화를 시작하고, 다시 클릭하여 중지합니다.

다음 파일들이 `recordings/` 폴더에 저장됩니다:

- `Stage.usd` — 물리 비활성화가 적용된 원래 스테이지
- `TimeSample_tk001.usd` — 애니메이션(시간 샘플링) 레이어

재생하려면:

```bash
./isaaclab.sh -s  # Opens Isaac Sim
```

Isaac Sim 내부에서 레이어 패널에서 `Stage.usd`와 `TimeSample_tk001.usd`를 모두 서브레이어로 삽입합니다.
이제 재생 버튼을 누르면 애니메이션이 재생됩니다.

[Omniverse에서 레이어 작업에 대한 튜토리얼](https://www.youtube.com/watch?v=LTwmNkSDh-c&ab_channel=NVIDIAOmniverse)을 참조하여 레이어 사용 방법에 대한 자세한 정보를 확인하세요.

## OVD Recorder

OVD Recorder는 OmniPVD를 사용하여 시뮬레이션 데이터를 기록하고 이를 새로운 USD 스테이지에 직접 베이킹합니다.
이 방법은 확장성이 더 높고 대규모 학습 시나리오(예: 다중 환경 RL)에 더 적합합니다.

UI를 통한 제어가 아닌 CLI 플래그를 통해 전체 프로세스가 활성화되며 자동으로 실행됩니다.

### 워크플로우 요약

1. 사용자가 애니메이션 녹화를 활성화한 CLI로 Isaac Lab을 실행합니다
2. Isaac Lab이 시뮬레이션을 시작합니다
3. 시뮬레이션 실행 중에 OVD 데이터가 기록됩니다
4. 지정된 중지 시간 시에 시뮬레이션이 출력 USD 파일로 베이킹되고 IsaacLab이 종료됩니다
5. 최종 결과는 자체 포함된 USD 애니메이션 파일입니다

### 사용 예시

애니메이션을 기록하려면:

```bash
./isaaclab.sh -p scripts/tutorials/03_envs/run_cartpole_rl_env.py \
  --anim_recording_enabled \
  --anim_recording_start_time 1 \
  --anim_recording_stop_time 3
```

#### 참고
제공된 `--anim_recording_stop_time`은 시뮬레이션 시간보다 커야 합니다.

#### 경고
현재 최종 녹화 단계에서 [omni.usd]로부터 많은 경고 로그가 출력될 수 있습니다. 이는 알려진 문제이며, 이러한 경고 메시지는 무시해도 됩니다.

중지 시간에 도달하면 다음 위치에 파일이 저장됩니다:

```none
anim_recordings/<timestamp>/baked_animation_recording.usda
```
