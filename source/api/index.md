# API 참조

이 페이지에서는 Isaac Lab 확장의 모든 모듈과 클래스에 대한 개요를 제공합니다.

## isaaclab 확장

다음 모듈은 `isaaclab` 확장 내에 사용할 수 있습니다:

| [`app`](lab/isaaclab.app.md#module-isaaclab.app)                         | 앱별 기능을 포함하는 하위 패키지입니다.                             |
|--------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| [`actuators`](lab/isaaclab.actuators.md#module-isaaclab.actuators)       | 다양한 액추에이터 모델을 위한 하위 패키지입니다.                                       |
| [`assets`](lab/isaaclab.assets.md#module-isaaclab.assets)                | 강체 객체 및 관절 구조와 같은 다양한 자산을 포함하는 하위 패키지입니다.                |
| [`controllers`](lab/isaaclab.controllers.md#module-isaaclab.controllers) | 다양한 컨트롤러 및 모션 생성기를 위한 하위 패키지입니다.                     |
| [`devices`](lab/isaaclab.devices.md#module-isaaclab.devices)             | 다양한 원격 조작 장치에 대한 인터페이스를 제공하는 하위 패키지입니다.             |
| [`envs`](lab/isaaclab.envs.md#module-isaaclab.envs)                      | 환경 정의를 위한 하위 패키지입니다.                                         |
| [`managers`](lab/isaaclab.managers.md#module-isaaclab.managers)          | 환경 관리자를 위한 하위 모듈입니다.                                             |
| [`markers`](lab/isaaclab.markers.md#module-isaaclab.markers)             | GUI의 UI 요소 생성을 간소화하는 마커 유틸리티를 위한 하위 패키지입니다. |
| [`scene`](lab/isaaclab.scene.md#module-isaaclab.scene)                   | 인터랙티브 씬 정의를 포함하는 하위 패키지입니다.                          |
| [`sensors`](lab/isaaclab.sensors.md#module-isaaclab.sensors)             | 다양한 센서 클래스 구현을 포함하는 하위 패키지입니다.                   |
| [`sim`](lab/isaaclab.sim.md#module-isaaclab.sim)                         | 시뮬레이션 특정 기능을 포함하는 하위 패키지입니다.                      |
| [`terrains`](lab/isaaclab.terrains.md#module-isaaclab.terrains)          | 절차적으로 지형을 생성하기 위한 유틸리티를 포함하는 하위 패키지입니다.                   |
| [`utils`](lab/isaaclab.utils.md#module-isaaclab.utils)                   | 일반적인 작업 및 헬퍼 함수를 위한 유틸리티를 포함하는 하위 패키지입니다.     |

## isaaclab_rl 확장

다음 래퍼는 `isaaclab_rl` 확장 내에서 사용할 수 있습니다:

* [isaaclab_rl](lab_rl/isaaclab_rl.md)
  * [RL-Games Wrapper](lab_rl/isaaclab_rl.md#module-isaaclab_rl.rl_games)
  * [RSL-RL Wrapper](lab_rl/isaaclab_rl.md#module-isaaclab_rl.rsl_rl)
  * [SKRL Wrapper](lab_rl/isaaclab_rl.md#module-isaaclab_rl.skrl)
  * [Stable-Baselines3 Wrapper](lab_rl/isaaclab_rl.md#module-isaaclab_rl.sb3)

## isaaclab_mimic 확장

다음 모듈은 `isaaclab_mimic` 확장 내에서 사용할 수 있습니다:

| [`datagen`](lab_mimic/isaaclab_mimic.datagen.md#module-isaaclab_mimic.datagen)   | Isaac Lab Mimic을 위한 핵심 구현 로직을 포함하는 하위 패키지입니다.   |
|----------------------------------------------------------------------------------|-------------------------------------------------------------------|
| [`envs`](lab_mimic/isaaclab_mimic.envs.md#module-isaaclab_mimic.envs)            | Isaac Lab Mimic을 위한 환경 래퍼를 포함하는 하위 패키지입니다.        |

## isaaclab_contrib 확장

다음 모듈은 `isaaclab_contrib` 확장 내에서 사용할 수 있습니다:

| [`actuators`](lab_contrib/isaaclab_contrib.actuators.md#module-isaaclab_contrib.actuators)   | 스러스터 액추에이터 모델을 위한 하위 패키지입니다.                                              |
|----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| [`assets`](lab_contrib/isaaclab_contrib.assets.md#module-isaaclab_contrib.assets)            | 외부에서 기여된 자산을 위한 하위 패키지입니다.                                         |
| [`mdp`](lab_contrib/isaaclab_contrib.mdp.md#module-isaaclab_contrib.mdp)                     | 커뮤니티에서 기여한 MDP(마르코프 결정 프로세스) 구성 요소를 위한 하위 패키지입니다. |
| [`sensors`](lab_contrib/isaaclab_contrib.sensors.md#module-isaaclab_contrib.sensors)         | 외부에서 기여된 센서를 위한 하위 패키지입니다.                                        |

## isaaclab_tasks 확장

이 패키지 `isaaclab_tasks`에는 Isaac Lab에서 사용할 수 있는 작업이 포함되어 있습니다.
자세한 내용은 [사용 가능한 환경](../overview/environments.md#environments)을 참조하십시오.

다음 모듈을 포함합니다:

| [`utils`](lab_tasks/isaaclab_tasks.utils.md#module-isaaclab_tasks.utils)   | 유틸리티, 데이터 수집기 및 환경 래퍼를 포함하는 하위 패키지입니다.   |
|----------------------------------------------------------------------------|-------------------------------------------------------------------------|
