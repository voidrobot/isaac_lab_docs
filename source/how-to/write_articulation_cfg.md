<a id="how-to-write-articulation-config"></a>

# 자산 구성 작성하기

이 가이드는 [`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg)를 생성하는 과정을 안내합니다.
[`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg)는 Isaac Lab에서 [`Articulation`](../api/lab/isaaclab.assets.md#isaaclab.assets.Articulation)의 속성을 정의하는 구성 객체입니다.

#### 참고
이 가이드에서는 [`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg) 생성만 다룹니다만,
다른 에셋 구성 객체를 생성하는 과정도 유사합니다.

Cartpole 예시를 사용하여 [`ArticulationCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg)를 생성하는 방법을 보여드리겠습니다.
Cartpole은 레일 위의 카트에 poles가 연결된 간단한 로봇입니다. 카트는 레일을 따라 자유롭게 이동할 수 있고, poles는 카트 주변에서 자유롭게 회전할 수 있습니다.
이 구성 예시 파일은 `source/isaaclab_assets/isaaclab_assets/robots/cartpole.py`입니다.

### Cartpole 구성 코드

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""간단한 Cartpole 로봇의 구성."""

import isaaclab.sim as sim_utils
from isaaclab.actuators import ImplicitActuatorCfg
from isaaclab.assets import ArticulationCfg
from isaaclab.utils.assets import ISAACLAB_NUCLEUS_DIR

##
# 구성
##

CARTPOLE_CFG = ArticulationCfg(
    spawn=sim_utils.UsdFileCfg(
        usd_path=f"{ISAACLAB_NUCLEUS_DIR}/Robots/Classic/Cartpole/cartpole.usd",
        rigid_props=sim_utils.RigidBodyPropertiesCfg(
            rigid_body_enabled=True,
            max_linear_velocity=1000.0,
            max_angular_velocity=1000.0,
            max_depenetration_velocity=100.0,
            enable_gyroscopic_forces=True,
        ),
        articulation_props=sim_utils.ArticulationRootPropertiesCfg(
            enabled_self_collisions=False,
            solver_position_iteration_count=4,
            solver_velocity_iteration_count=0,
            sleep_threshold=0.005,
            stabilization_threshold=0.001,
        ),
    ),
    init_state=ArticulationCfg.InitialStateCfg(
        pos=(0.0, 0.0, 2.0), joint_pos={"slider_to_cart": 0.0, "cart_to_pole": 0.0}
    ),
    actuators={
        "cart_actuator": ImplicitActuatorCfg(
            joint_names_expr=["slider_to_cart"],
            effort_limit_sim=400.0,
            stiffness=0.0,
            damping=10.0,
        ),
        "pole_actuator": ImplicitActuatorCfg(
            joint_names_expr=["cart_to_pole"], effort_limit_sim=400.0, stiffness=0.0, damping=0.0
        ),
    },
)
"""간단한 Cartpole 로봇의 구성."""
```

## 스폰 구성 정의하기

[씬에 프림 스폰하기](../tutorials/00_sim/spawn_prims.md#tutorial-spawn-prims) 튜토리얼에서 설명한 바와 같이, 스폰 구성은 스폰될 에셋의 속성을 정의합니다.
이 스폰은 절차적으로 발생할 수도 있고, 기존 에셋 파일(예: USD 또는 URDF)을 통해 이루어질 수도 있습니다.
이 예시에서는 USD 파일을 통해 Cartpole을 스폰합니다.

USD 파일에서 에셋을 스폰할 때, [`UsdFileCfg`](../api/lab/isaaclab.sim.spawners.md#isaaclab.sim.spawners.from_files.UsdFileCfg)를 정의합니다.
이 구성 객체는 다음 매개변수를 받습니다:

* [`usd_path`](../api/lab/isaaclab.sim.spawners.md#isaaclab.sim.spawners.from_files.UsdFileCfg.usd_path): 스폰할 USD 파일 경로
* [`rigid_props`](../api/lab/isaaclab.sim.spawners.md#isaaclab.sim.spawners.from_files.UsdFileCfg.rigid_props): 에셋 루트의 속성
* [`articulation_props`](../api/lab/isaaclab.sim.spawners.md#isaaclab.sim.spawners.from_files.UsdFileCfg.articulation_props): 모든 에셋 링크의 속성

마지막 두 매개변수는 선택 사항입니다. 지정하지 않으면 USD 파일의 기본값으로 유지됩니다.

```python
    usd_path=f"{ISAACLAB_NUCLEUS_DIR}/Robots/Classic/Cartpole/cartpole.usd",
    rigid_props=sim_utils.RigidBodyPropertiesCfg(
        rigid_body_enabled=True,
        max_linear_velocity=1000.0,
        max_angular_velocity=1000.0,
        max_depenetration_velocity=100.0,
        enable_gyroscopic_forces=True,
    ),
    articulation_props=sim_utils.ArticulationRootPropertiesCfg(
        enabled_self_collisions=False,
        solver_position_iteration_count=4,
        solver_velocity_iteration_count=0,
        sleep_threshold=0.005,
        stabilization_threshold=0.001,
    ),
),
init_state=ArticulationCfg.InitialStateCfg(
```

USD 파일 대신 URDF 파일에서 에셋을 스폰하려면 [`UsdFileCfg`](../api/lab/isaaclab.sim.spawners.md#isaaclab.sim.spawners.from_files.UsdFileCfg)를 [`UrdfFileCfg`](../api/lab/isaaclab.sim.spawners.md#isaaclab.sim.spawners.from_files.UrdfFileCfg)로 대체하면 됩니다.
자세한 내용은 API 문서를 확인하세요.

## 초기 상태 정의하기

모든 에셋은 구성에 초기(또는 *기본*) 상태를 정의하여 시뮬레이션에 포함해야 합니다.
이 구성은 에셋의 상태를 리셋해야 할 때 접근할 수 있는 에셋의 기본 상태 버퍼에 저장됩니다.

#### 참고
에셋의 초기 상태는 로컬 환경 프레임을 기준으로 정의됩니다. 따라서 에셋의 상태를 리셋할 때는 이를 글로벌 시뮬레이션 프레임으로 변환해야 합니다.
자세한 내용은 [아티큘레이션과 상호작용하기](../tutorials/01_assets/run_articulation.md#tutorial-interact-articulation) 튜토리얼을 참조하세요.

아티큘레이션의 경우, [`InitialStateCfg`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg.InitialStateCfg) 객체는 아티큘레이션 루트와 모든 조인트의 초기 상태를 정의합니다.
이 예시에서는 XY 평면의 원점에 Z 높이 2.0미터 위치에서 Cartpole을 스폰합니다.
한편, 조인트 위치와 속도는 0.0으로 설정됩니다.

```python
    pos=(0.0, 0.0, 2.0), joint_pos={"slider_to_cart": 0.0, "cart_to_pole": 0.0}
),
actuators={
```

## 액추에이터 구성 정의하기

액추에이터는 아티큘레이션의 중요한 구성 요소입니다. 이 구성을 통해 사용할 액추에이터 모델의 유형을 정의할 수 있습니다.
물리 엔진에서 제공하는 내부 액추에이터 모델(즉, 암시적 액추에이터 모델)을 사용하거나,
사용자 정의 방정식 시스템을 따르는 커스텀 액추에이터 모델(즉, 명시적 액추에이터 모델)을 사용할 수 있습니다.
액추에이터에 대한 자세한 내용은 [액추에이터](../overview/core-concepts/actuators.md#overview-actuators)를 참조하세요.

Cartpole의 아티큘레이션에는 각각의 조인트에 해당하는 두 개의 액추에이터가 있습니다: `cart_to_pole`와 `slider_to_cart`.
이 액추에이터에 서로 다른 액추에이터 모델을 사용하도록 예시를 구성했습니다. 그러나 두 액추에이터가 동일한 모델을 사용하므로
하나의 액추에이터 모델로 통합하는 것도 가능합니다.

### 별도 액추에이터 모델을 사용한 액추에이터 구성

```python
        "cart_actuator": ImplicitActuatorCfg(
            joint_names_expr=["slider_to_cart"],
            effort_limit_sim=400.0,
            stiffness=0.0,
            damping=10.0,
        ),
        "pole_actuator": ImplicitActuatorCfg(
            joint_names_expr=["cart_to_pole"], effort_limit_sim=400.0, stiffness=0.0, damping=0.0
        ),
    },
)
```

### 단일 액추에이터 모델을 사용한 액추에이터 구성

```python
actuators={
   "all_joints": ImplicitActuatorCfg(
      joint_names_expr=[".*"],
      effort_limit=400.0,
      velocity_limit=100.0,
      stiffness={"slider_to_cart": 0.0, "cart_to_pole": 0.0},
      damping={"slider_to_cart": 10.0, "cart_to_pole": 0.0},
   ),
},
```

## 액추에이터Cfg 속도/힘 제한 고려 사항

IsaacLab v1.4.0에서는 일반적인 `velocity_limit`과 `effort_limit` 속성이 물리 솔버에 **일관되게 반영되지 않았습니다**:

- **암시적 액추에이터**
  - velocity_limit이 무시됨(시뮬레이션에서 설정되지 않음)
  - effort_limit은 시뮬레이션에 설정됨
- **명시적 액추에이터**
  - velocity_limit과 effort_limit은 드라이브 모델에서만 사용되며 솔버에서는 사용되지 않음

v2.0.1에서는 실수로 이 동작이 변경되어, 암시적 및 명시적 액추에이터 모두의 모든 `velocity_limit` 및 `effort_limit`이 솔버에 적용되었습니다.
이로 인해 이전의 기본적인 제한되지 않은 솔버 제한을 사용하던 많은 학습이 중단되었습니다.

기존 동작을 유지하면서 사용자에게 솔버 제한에 대한 완전한 제어를 제공하기 위해 두 가지 새로운 플래그를 도입했습니다:

* **velocity_limit_sim**
  시뮬레이션에서 물리 솔버의 최대 조인트 속도 상한을 설정합니다.
* **effort_limit_sim**
  시뮬레이션에서 물리 솔버의 최대 조인트 힘 상한을 설정합니다.

이 플래그들은 시뮬레이션 수준에서 솔버의 조인트 속도 및 힘 상한을 명시적으로 설정합니다.

반면에, `velocity_limit`과 `effort_limit`은 모든 명시적 액추에이터에서 토크 연산 시 모터의 하드웨어 수준 제약을 모델링하는 데 사용되며,
시뮬레이션 수준의 제약에는 영향을 주지 않습니다. 암시적 액추에이터는 모터 하드웨어 제한을 모델링하지 않으므로
v2.1.1에서 `velocity_limit`이 제거되고 사용되지 않음으로 표시되었습니다.
이는 v1.4.0과의 동작 동일성을 유지하기 위한 것입니다.最終的に、`velocity_limit`과 `effort_limit`은 암시적 액추에이터에서 사용되지 않음으로 처리되어,
그 대신 `velocity_limit_sim`과 `effort_limit_sim`만 남게 됩니다.

#### 제한 옵션 비교

| **Attribute**        | **암시적 액추에이터**                       | **명시적 액추에이터**                                      |
|----------------------|---------------------------------------------|------------------------------------------------------------|
| `velocity_limit`     | 사용되지 않음(`velocity_limit_sim`의 별칭) | 모델에서 사용됨(예: DC 모터), 시뮬레이션에 설정되지 않음 |
| `effort_limit`       | 사용되지 않음(`effort_limit_sim`의 별칭)   | 모델에서 사용됨, 시뮬레이션에 설정되지 않음               |
| `velocity_limit_sim` | 시뮬레이션에 설정                           | 시뮬레이션에 설정                                       |
| `effort_limit_sim`   | 시뮬레이션에 설정                           | 시뮬레이션에 설정                                       |

물리 솔버의 기반 제한을 튜닝하고 싶은 사용자는 `_sim` 플래그를 설정해야 합니다.

## USD vs. ActuatorCfg 차이 해결

USD는 기본값을 가지고 있으며, ActuatorCfg는 `None`으로 지정하거나 재정의 값을 지정할 수 있으므로, 시뮬레이션에 정확히 어떤 값이 작성되는지 혼동될 수 있습니다. 해결 방법은 다음 간단한 규칙을 따릅니다. 즉, 각 조인트 및 각 속성에 대해:

#### USD vs. ActuatorCfg의 해결 규칙

| **조건**           | **ActuatorCfg 값** | **적용됨**       |
|--------------------|--------------------|------------------|
| 오버라이드 제공 없음 | 지정되지 않음      | USD 값           |
| 오버라이드 제공됨    | 사용자 ActuatorCfg | ActuatorCfg와 동일 |

USD를 탐색하는 것은 때때로 불편할 수 있으므로, 시뮬레이션에 정확히 어떤 값이 작성되는지 명확히 하기 위해 플래그 [`actuator_value_resolution_debug_print`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg.actuator_value_resolution_debug_print)를 설계하여 사용자가 확인할 수 있도록 도왔습니다.

액추에이터 매개변수가 사용자의 ActuatorCfg에서 오버라이드되든(지정되지 않은 채 남겨두든) 언제나, 우리는 그 값을 USD 정의에서 읽은 값과 비교하고 차이를 기록합니다. 각 조인트 및 각 속성에 대해 불일치 값이 발견되면, 다음과 같이 해결 정보를 기록합니다:

> 1. **USD 값**  
>    USD 자산에서 파싱된 기본 제한 또는 이득.
> 2. **ActuatorCfg 값**  
>    사용자 제공 오버라이드(제공되지 않은 경우 “지정되지 않음”).
> 3. **적용됨**  
>    시뮬레이션에 실제로 사용되는 값: 사용자가 오버라이드하지 않은 경우 이 값은 USD 값과 일치하며, 그렇지 않으면 사용자의 설정을 반영합니다.

이 해결 정보는 차이가 존재할 때만 경고 테이블로 출력됩니다. 다음과 같이 보입니다:

```default
+----------------+--------------------+---------------------+----+-------------+--------------------+----------+
|     Group      |      Property      |         Name        | ID |  USD Value  | ActuatorCfg Value  | Applied  |
+----------------+--------------------+---------------------+----+-------------+--------------------+----------+
| panda_shoulder | velocity_limit_sim |    panda_joint1     |  0 |    2.17e+00 |   Not Specified    | 2.17e+00 |
|                |                    |    panda_joint2     |  1 |    2.17e+00 |   Not Specified    | 2.17e+00 |
|                |                    |    panda_joint3     |  2 |    2.17e+00 |   Not Specified    | 2.17e+00 |
|                |                    |    panda_joint4     |  3 |    2.17e+00 |   Not Specified    | 2.17e+00 |
|                |     stiffness      |    panda_joint1     |  0 |    2.29e+04 |      8.00e+01      | 8.00e+01 |
|                |                    |    panda_joint2     |  1 |    2.29e+04 |      8.00e+01      | 8.00e+01 |
|                |                    |    panda_joint3     |  2 |    2.29e+04 |      8.00e+01      | 8.00e+01 |
|                |                    |    panda_joint4     |  3 |    2.29e+04 |      8.00e+01      | 8.00e+01 |
|                |      damping       |    panda_joint1     |  0 |    4.58e+03 |      4.00e+00      | 4.00e+00 |
|                |                    |    panda_joint2     |  1 |    4.58e+03 |      4.00e+00      | 4.00e+00 |
|                |                    |    panda_joint3     |  2 |    4.58e+03 |      4.00e+00      | 4.00e+00 |
|                |                    |    panda_joint4     |  3 |    4.58e+03 |      4.00e+00      | 4.00e+00 |
|                |      armature      |    panda_joint1     |  0 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |                    |    panda_joint2     |  1 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |                    |    panda_joint3     |  2 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |                    |    panda_joint4     |  3 |    0.00e+00 |   Not Specified    | 0.00e+00 |
| panda_forearm  | velocity_limit_sim |    panda_joint5     |  4 |    2.61e+00 |   Not Specified    | 2.61e+00 |
|                |                    |    panda_joint6     |  5 |    2.61e+00 |   Not Specified    | 2.61e+00 |
|                |                    |    panda_joint7     |  6 |    2.61e+00 |   Not Specified    | 2.61e+00 |
|                |     stiffness      |    panda_joint5     |  4 |    2.29e+04 |      8.00e+01      | 8.00e+01 |
|                |                    |    panda_joint6     |  5 |    2.29e+04 |      8.00e+01      | 8.00e+01 |
|                |                    |    panda_joint7     |  6 |    2.29e+04 |      8.00e+01      | 8.00e+01 |
|                |      damping       |    panda_joint5     |  4 |    4.58e+03 |      4.00e+00      | 4.00e+00 |
|                |                    |    panda_joint6     |  5 |    4.58e+03 |      4.00e+00      | 4.00e+00 |
|                |                    |    panda_joint7     |  6 |    4.58e+03 |      4.00e+00      | 4.00e+00 |
|                |      armature      |    panda_joint5     |  4 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |                    |    panda_joint6     |  5 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |                    |    panda_joint7     |  6 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |      friction      |    panda_joint5     |  4 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |                    |    panda_joint6     |  5 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |                    |    panda_joint7     |  6 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|  panda_hand    | velocity_limit_sim | panda_finger_joint1 |  7 |    2.00e-01 |   Not Specified    | 2.00e-01 |
|                |                    | panda_finger_joint2 |  8 |    2.00e-01 |   Not Specified    | 2.00e-01 |
|                |     stiffness      | panda_finger_joint1 |  7 |    1.00e+06 |      2.00e+03      | 2.00e+03 |
|                |                    | panda_finger_joint2 |  8 |    1.00e+06 |      2.00e+03      | 2.00e+03 |
|                |      armature      | panda_finger_joint1 |  7 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |                    | panda_finger_joint2 |  8 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |      friction      | panda_finger_joint1 |  7 |    0.00e+00 |   Not Specified    | 0.00e+00 |
|                |                    | panda_finger_joint2 |  8 |    0.00e+00 |   Not Specified    | 0.00e+00 |
+----------------+--------------------+---------------------+----+-------------+--------------------+----------+
```

로그의 깔끔함을 유지하기 위해 [`actuator_value_resolution_debug_print`](../api/lab/isaaclab.assets.md#isaaclab.assets.ArticulationCfg.actuator_value_resolution_debug_print)는 기본적으로 `False`로 설정되어 있으므로, 필요할 때 켜야 한다는 점을 기억하세요.
