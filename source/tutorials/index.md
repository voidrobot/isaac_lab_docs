<a id="tutorials"></a>

# 튜토리얼

Isaac Lab 튜토리얼에 오신 것을 환영합니다! 이 튜토리얼은 프레임워크의 다양한 기능을 이해하고 사용하는 데 도움이 되는 단계별 가이드를 제공합니다. 모든 튜토리얼은 Python 스크립트로 작성되었습니다. 각 튜토리얼의 소스 코드는 Isaac Lab 리포지토리의 `scripts/tutorials` 디렉터리에서 찾을 수 있습니다.

#### 참고 사항
더 많은 주제와 사용 사례를 다루도록 튜토리얼을 확장하는 것을 환영합니다. 제안 사항이 있으면 알려 주세요.

튜토리얼은 여기에 나열된 순서대로 진행하는 것을 권장합니다.

## 간단한 시뮬레이션 설정

이 튜토리얼에서는 다양한 설정으로 시뮬레이션을 시작하고 시뮬레이션된 장면에서 객체를 생성하는 방법을 보여줍니다. 다음 API를 다룹니다: [`AppLauncher`](../api/lab/isaaclab.app.md#isaaclab.app.AppLauncher), [`SimulationContext`](../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationContext), 그리고 [`spawners`](../api/lab/isaaclab.sim.spawners.md#module-isaaclab.sim.spawners).

* [빈 장면 만들기](00_sim/create_empty.md)
* [장면에 프리미브 생성하기](00_sim/spawn_prims.md)
* [AppLauncher 심층 탐구](00_sim/launch_app.md)

## 자산과 상호작용

장면에서 객체를 생성한 후, 이 튜토리얼에서는 이러한 객체에 대한 물리 핸들을 생성하고 상호작용하는 방법을 보여줍니다. 이는 [`AssetBase`](../api/lab/isaaclab.assets.md#isaaclab.assets.AssetBase) 클래스와 그 파생 클래스인 [`RigidObject`](../api/lab/isaaclab.assets.md#isaaclab.assets.RigidObject), [`Articulation`](../api/lab/isaaclab.assets.md#isaaclab.assets.Articulation) 및 [`DeformableObject`](../api/lab/isaaclab.assets.md#isaaclab.assets.DeformableObject)를 중심으로 이루어집니다.

* [Isaac Lab에 새 로봇 추가하기](01_assets/add_new_robot.md)
* [강체 객체와 상호작용하기](01_assets/run_rigid_object.md)
* [관절체와 상호작용하기](01_assets/run_articulation.md)
* [변형 가능한 객체와 상호작용하기](01_assets/run_deformable_object.md)
* [서피스 그리퍼와 상호작용하기](01_assets/run_surface_gripper.md)

## 장면 생성

프레임워크의 기본 개념을 다룬 후, 튜토리얼은 [`InteractiveScene`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveScene) 클래스를 사용하는 더 직관적인 장면 인터페이스로 이동합니다. 이 클래스는 쉽게 장면을 생성하기 위한 상위 수준의 추상화를 제공합니다.

* [인터랙티브 씬 사용하기](02_scene/create_scene.md)

## 환경 설계

다음 튜토리얼은 관리자 기반 환경의 개념을 소개합니다: [`ManagerBasedEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedEnv) 및 그 파생 클래스인 [`ManagerBasedRLEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedRLEnv), 그리고 직접 워크플로 베이스 클래스인 [`DirectRLEnv`](../api/lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnv). 이러한 환경은 프레임워크의 다양한 측면을 결합하여 에이전트 상호작용을 위한 시뮬레이션 환경을 구축합니다.

* [관리자 기반 기본 환경 만들기](03_envs/create_manager_base_env.md)
* [관리자 기반 RL 환경 만들기](03_envs/create_manager_rl_env.md)
* [직접 워크플로 RL 환경 만들기](03_envs/create_direct_rl_env.md)
* [환경 등록하기](03_envs/register_rl_env_gym.md)
* [RL 에이전트와 함께 훈련하기](03_envs/run_rl_training.md)
* [RL 에이전트 구성하기](03_envs/configuring_rl_training.md)
* [기존의 직접 워크플로 RL 환경 수정하기](03_envs/modify_direct_rl_env.md)
* [USD 환경에서 정책 추론하기](03_envs/policy_inference_in_usd.md)

## 센서 통합

다음 튜토리얼에서는 시뮬레이션 환경에 센서를 통합하는 방법을 보여줍니다. 튜토리얼에서는 [`SensorBase`](../api/lab/isaaclab.sensors.md#isaaclab.sensors.SensorBase) 클래스와 그 파생 클래스인 [`Camera`](../api/lab/isaaclab.sensors.md#isaaclab.sensors.Camera) 및 [`RayCaster`](../api/lab/isaaclab.sensors.md#isaaclab.sensors.RayCaster)를 소개합니다.

* [로봇에 센서 추가하기](04_sensors/add_sensors_on_robot.md)

## 모션 생성기 사용하기

시뮬레이션 환경의 로봇이 관절 수준에서 제어될 수 있지만, 다음 튜토리얼에서는 모션 생성기를 사용하여 태스크 수준에서 로봇을 제어하는 방법을 보여줍니다.

* [태스페이스 컨트롤러 사용하기](05_controllers/run_diff_ik.md)
* [운영 공간 컨트롤러 사용하기](05_controllers/run_osc.md)
