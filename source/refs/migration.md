<a id="migration-guide"></a>

# 마이그레이션 가이드 (Isaac Sim)

Isaac Sim 4.2에서 4.5 이후로 이동하면 API 및 Isaac Sim 확장 및 클래스에 여러 가지 변경 사항이 발생합니다. 이 문서는 변경 사항을 개괄하고 새 API로 코드를 마이그레이션하는 방법을 설명합니다.

## Isaac Sim 확장 이름 변경

이전에는 Isaac Sim 확장이 `omni.isaac.*`이라는 규칙을 따르고 있었으며(예: `omni.isaac.core`), Isaac Sim 4.5에서는 Isaac Sim 확장이 `omni.isaac`을 `isaacsim` 접두사로 바꾸어 이름을 변경했습니다. 또한 많은 확장이 이름이 바뀌고 분할되어 사용자가 앱 템플릿을 통해 커스터마이징할 수 있는 보다 모듈식 프레임워크를 준비했습니다.

특히, Isaac Lab에서 자주 사용되는 다음 Isaac Sim 확장은 다음과 같이 이름이 변경되었습니다.

* `omni.isaac.cloner` → `isaacsim.core.cloner`
* `omni.isaac.core.prims` → `isaacsim.core.prims`
* `omni.isaac.core.simulation_context` → `isaacsim.core.api.simulation_context`
* `omni.isaac.core.utils` → `isaacsim.core.utils`
* `omni.isaac.core.world` → `isaacsim.core.api.world`
* `omni.isaac.kit.SimulationApp` → `isaacsim.SimulationApp`
* `omni.isaac.ui` → `isaacsim.gui.components`

## URDF 및 MJCF 가져오기 이름 변경

Isaac Sim 4.5부터 URDF 및 MJCF 가져오기가 다른 Isaac Sim 확장과 더 일관되게 이름을 변경했습니다. 가져오기는 Isaac-sim GitHub에서 오픈 소스 프로젝트로 제공됩니다.

확장 이름 변경으로 인해 Python 모듈 이름도 다음과 같이 변경되었습니다.

* URDF 가져오기: `isaacsim.asset.importer.urdf` (이전: `omni.importer.urdf`)
* MJCF 가져오기: `isaacsim.asset.importer.mjcf` (이전: `omni.importer.mjcf`)

Isaac Sim UI에서 두 URDF 및 MJCF 가져오기는 파일 브라우저에서 해당 .urdf 또는 .xml 파일을 선택할 때 File > Import 메뉴에서 직접 액세스할 수 있습니다.

## URDF 가져오기 변경 사항

Isaac Sim 4.5에서는 URDF 가져오기에 몇 가지 업데이트가 포함되었습니다. URDF에서 로봇을 가져올 때 더 나은 구성을 허용하는 새로운 UI가 도입되었습니다. 그 결과, Isaac Lab URDF 변환기도 이러한 변경 사항을 반영하도록 업데이트되었습니다. `UrdfConverterCfg`에는 `PDGainsCfg` 및 `NaturalFrequencyGainsCfg` 클래스와 같은 드라이브 게인을 구성하는 새로운 설정이 포함됩니다.

주의해야 할 한 가지 breaking change는 `UrdfConverterCfg.JointDriveCfg.gains` 속성이 `PDGainsCfg` 또는 `NaturalFrequencyGainsCfg` 클래스 타입이어야 한다는 것입니다.

`PDGainsCfg`의 강성은 다음과 같이 지정해야 합니다.

<!-- code::python

joint_drive=sim_utils.UrdfConverterCfg.JointDriveCfg(
    gains=sim_utils.UrdfConverterCfg.JointDriveCfg.PDGainsCfg(stiffness=None, damping=None)
) -->

`NaturalFrequencyGainsCfg`의 경우 `natural_frequency`를 지정해야 합니다.

## omni.isaac.core 클래스 이름 변경

Isaac Sim 4.5에서는 Isaac Lab에서 일반적으로 사용되는 핵심 프리미티브 클래스에 대한 이름 변경을 도입했습니다. 이는 Articulation, RigidPrim, XFormPrim 및 기타 클래스의 단일 및 `View` 변형에 영향을 미칩니다. 단일 객체 클래스는 이제 `Single`로 접두사가 붙어 있으며, 텐서화된 View 클래스는 이제 `View` 접미사가 제거되었습니다.

클래스의 정확한 이름 변경은 다음과 같습니다.

* `Articulation` → `SingleArticulation`
* `ArticulationView` → `Articulation`
* `ClothPrim` → `SingleClothPrim`
* `ClothPrimView` → `ClothPrim`
* `DeformablePrim` → `SingleDeformablePrim`
* `DeformablePrimView` → `DeformablePrim`
* `GeometryPrim` → `SingleGeometryPrim`
* `GeometryPrimView` → `GeometryPrim`
* `ParticleSystem` → `SingleParticleSystem`
* `ParticleSystemView` → `ParticleSystem`
* `RigidPrim` → `SingleRigidPrim`
* `RigidPrimView` → `RigidPrim`
* `XFormPrim` → `SingleXFormPrim`
* `XFormPrimView` → `XFormPrim`

## Isaac Lab 확장 및 폴더 이름 변경

Isaac Sim 4.5 변경 사항에 대응하여 Isaac Lab 디렉터리 및 확장도 일부 업데이트되었습니다. 이전에 `source/extensions`에 있던 모든 확장 기능은 이제 `source/` 디렉터리 바로 아래에 위치합니다. `source/apps` 및 `source/standalone` 폴더는 루트 디렉터리로 이동하여 이제 `apps/` 및 `scripts/`라고 불립니다.

Isaac Lab 확장은 다음과 같이 이름이 변경되었습니다.

* `omni.isaac.lab` → `isaaclab`
* `omni.isaac.lab_assets` → `isaaclab_assets`
* `omni.isaac.lab_tasks` → `isaaclab_tasks`

또한 이전 `source/standalone/workflows` 디렉터리가 `scripts/imitation_learning` 및 `scripts/reinforcement_learning` 디렉터리로 분할되었습니다. RSL RL, Stable-Baselines, RL_Games, SKRL 및 Ray 디렉터리는 `scripts/reinforcement_learning` 아래에 있으며, Robomimic 및 새로운 Isaac Lab Mimic 디렉터리는 `scripts/imitation_learning` 아래에 있습니다.

Isaac Lab 확장의 이름을 프로젝트에서 변경하는 데 도움을 주기 위해 [간단한 스크립트](https://gist.github.com/kellyguo11/3e8f73f739b1c013b1069ad372277a85)를 제공했습니다. 이 스크립트는 로컬 Isaac Lab 프로젝트의 `source` 및 `docs` 디렉터리를 탐색하여 이름이 변경된 디렉터리와 imports의 모든 인스턴스를 바꿉니다. **이 스크립트는 소스 파일을 직접 덮어쓰므로 사용자의 책임 하에 사용하세요.**

## Isaac Lab 확장 재구성

`isaaclab_mimic` 확장이 도입되어 모방 학습을 위한 데이터 생성 워크플로를 지원하면서, 이전에 `isaaclab_tasks` 아래에 있던 `wrappers` 폴더가 자체 모듈인 `isaaclab_rl`로 분할되었습니다. 이 새 확장은 Isaac Lab에서 지원하는 다양한 RL 라이브러리에 대한 강화 학습 전용 래퍼를 포함합니다.

새로운 `isaaclab_mimic` 확장은 또한 이전의 `robomimic` 폴더 아래에 있던 모방 학습 스크립트를 대체합니다. 데이터 수집 및 데이터셋 준비용 이전 스크립트는 새로운 모방 워크플로를 선호하는 대신 이전 릴리스 브랜치에서 사용할 수 있습니다.

또한 `isaaclab_assets` 확장이 `robots` 및 `sensors` 하위 디렉터리로 분할되어 재구성되었습니다.これにより、拡張機能で提供される事前定義済み構成の明確な分離が可能になります。既存のインポート（例: `from omni.isaac.lab_assets.anymal import ANYMAL_C_CFG`）については、`from isaaclab.robots.anymal import ANYMAL_C_CFG` に置き換えてください。
