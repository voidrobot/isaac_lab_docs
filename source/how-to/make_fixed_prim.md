# 시뮬레이션에서 물리 프리마 고정하기

USD 프리마에 물리 스키마가 적용되면 물리 시뮬레이션의 영향을 받습니다.
이는 프리마가 시뮬레이션 세계에서 이동하고, 회전하며, 다른 프리마와 충돌할 수 있음을 의미합니다.
그러나 특정 프리마를 시뮬레이션 세계에서 정적으로 만드는 것이 바람직한 경우가 있습니다.
즉, 프리마는 여전히 충돌에 참여해야 하지만 위치와 방향은 변하지 않아야 합니다.

다음 섹션에서는 물리 스키마가 적용된 프리마를 생성하고 시뮬레이션 세계에서 정적으로 만드는 방법을 설명합니다.

## 정적 콜라이더

정적 콜라이더는 물리 시뮬레이션의 영향을 받지 않지만 시뮬레이션 세계의 다른 프리마와 충돌할 수 있는 프리마입니다.
이들은 강체 속성을 적용받지 않습니다. 그러나 이는 물리 텐서 API(즉, [`assets.RigidObject`](../api/lab/isaaclab.assets.md#isaaclab.assets.RigidObject) 클래스를 통해)로 접근할 수 없다는 의미이기도 합니다.

예를 들어, 시뮬레이션 세계에서 원뿔을 정적으로 생성하려면 다음 코드를 사용할 수 있습니다:

```python
import isaaclab.sim as sim_utils

cone_spawn_cfg = sim_utils.ConeCfg(
    radius=0.15,
    height=0.5,
    collision_props=sim_utils.CollisionPropertiesCfg(),
    visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 1.0, 0.0)),
)
cone_spawn_cfg.func(
    "/World/Cone", cone_spawn_cfg, translation=(0.0, 0.0, 2.0), orientation=(0.5, 0.0, 0.5, 0.0)
)
```

## 강체 객체

강체 객체(즉, 단일 본체만 있는 객체)는 파라미터 [`sim.schemas.RigidBodyPropertiesCfg.kinematic_enabled`](../api/lab/isaaclab.sim.schemas.md#isaaclab.sim.schemas.RigidBodyPropertiesCfg.kinematic_enabled)를 True로 설정하여 정적으로 만들 수 있습니다. 이는 객체를 키네마틱으로 만들고 물리 시뮬레이션의 영향을 받지 않게 합니다.

예를 들어, 시뮬레이션 세계에서 원뿔을 정적으로 생성하되 강체 스키마를 적용하려면 다음 코드를 사용할 수 있습니다:

```python
import isaaclab.sim as sim_utils

cone_spawn_cfg = sim_utils.ConeCfg(
    radius=0.15,
    height=0.5,
    rigid_props=sim_utils.RigidBodyPropertiesCfg(kinematic_enabled=True),
    mass_props=sim_utils.MassPropertiesCfg(mass=1.0),
    collision_props=sim_utils.CollisionPropertiesCfg(),
    visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 1.0, 0.0)),
)
cone_spawn_cfg.func(
    "/World/Cone", cone_spawn_cfg, translation=(0.0, 0.0, 2.0), orientation=(0.5, 0.0, 0.5, 0.0)
)
```

## 관절구조

관절구조의 루트를 고정하려면 관절구조의 루트 강체 링크와 고정 조인트를 가져야 합니다.
이는 파라미터 [`sim.schemas.ArticulationRootPropertiesCfg.fix_root_link`](../api/lab/isaaclab.sim.schemas.md#isaaclab.sim.schemas.ArticulationRootPropertiesCfg.fix_root_link)를 True로 설정하여 달성할 수 있습니다. 이 파라미터의 값에 따라 다음과 같은 경우가 가능합니다:

* [`None`](https://docs.python.org/3/library/constants.html#None)으로 설정된 경우, 루트 링크가 수정되지 않습니다.
* 관절구조에 이미 고정된 루트 링크가 있는 경우, 이 플래그는 고정 조인트를 활성화하거나 비활성화합니다.
* 관절구조에 고정된 루트 링크가 없는 경우, 이 플래그는 세계 프레임과 루트 링크 사이에 고정 조인트를 생성합니다. 조인트는 루트 링크 아래에 “FixedJoint”라는 이름으로 생성됩니다.

예를 들어, ANYmal 로봇을 생성하고 시뮬레이션 세계에서 정적으로 만들려면 다음 코드를 사용할 수 있습니다:

```python
import isaaclab.sim as sim_utils
from isaaclab.utils.assets import ISAACLAB_NUCLEUS_DIR

anymal_spawn_cfg = sim_utils.UsdFileCfg(
    usd_path=f"{ISAACLAB_NUCLEUS_DIR}/Robots/ANYbotics/ANYmal-C/anymal_c.usd",
    activate_contact_sensors=True,
    rigid_props=sim_utils.RigidBodyPropertiesCfg(
        disable_gravity=False,
        retain_accelerations=False,
        linear_damping=0.0,
        angular_damping=0.0,
        max_linear_velocity=1000.0,
        max_angular_velocity=1000.0,
        max_depenetration_velocity=1.0,
    ),
    articulation_props=sim_utils.ArticulationRootPropertiesCfg(
        enabled_self_collisions=True,
        solver_position_iteration_count=4,
        solver_velocity_iteration_count=0,
        fix_root_link=True,
    ),
)
anymal_spawn_cfg.func(
    "/World/ANYmal", anymal_spawn_cfg, translation=(0.0, 0.0, 0.8), orientation=(1.0, 0.0, 0.0, 0.0)
)
```

이는 세계 프레임과 ANYmal 로봇의 루트 링크 사이의 고정 조인트를 프리마 경로 `"/World/ANYmal/base/FixedJoint"`에서 생성합니다. 이는 루트 링크가 경로 `"/World/ANYmal/base"`에 위치하기 때문입니다.

## 추가 메모

USD 자산 설계의 유연성으로 인해 다음과 같은 시나리오가 일반적으로 발생합니다:

1. **고정 조인트가 없는 강체 프리마에 관절구조 루트 스키마 적용**:

   이는 부동 베이스 관절구조에 대한 가장 일반적이고 권장되는 시나리오입니다. 루트 프리마에는 강체 속성과 관절구조 루트 속성이 모두 적용됩니다. 이 경우, 관절구조 루트는 부동 베이스로 파싱되며 관절구조의 루트 프리마는 `Link0Xform`입니다.
   ```text
   ArticulationXform
       └── Link0Xform  (RigidBody and ArticulationRoot schema)
   ```
2. **고정 조인트가 있는 부모 프리마에 관절구조 루트 스키마 적용**:

   이는 고정 베이스 관절구조에 대한 예상되는 배열입니다. 루트 프리마에는 강체 속성만 적용되고, 관절구조 루트 속성은 부모 프리마에 적용됩니다. 이 경우, 관절구조 루트는 고정 베르스로 파싱되며 관절구조의 루트 프리마는 `Link0Xform`입니다.
   ```text
   ArticulationXform (ArticulationRoot schema)
       └── Link0Xform  (RigidBody schema)
       └── FixedJoint (connecting the world frame and Link0Xform)
   ```
3. **고정 조인트가 없는 부모 프리마에 관절구조 루트 스키마 적용**:

   이는 루트 프리마에는 강체 속성만 적용되고 관절구조 루트 속성은 부모 프리마에 적용되지만, 세계 프레임과 루트 링크 사이에 고정 조인트가 생성되지 않는 시나리오입니다. 이 경우, 관절구조는 부동 베이스 시스템으로 파싱됩니다. 그러나 PhysX 파서는 자체 휴리스틱(예: 알파벳 순서)을 사용하여 관절구조의 루트 프리마를 결정합니다. 이는 `Link0Xform`을 루트 프리마로 선택하거나 다른 프리마를 루트 프리마로 선택할 수 있음을 의미합니다.
   ```text
   ArticulationXform (ArticulationRoot schema)
       └── Link0Xform  (RigidBody schema)
   ```
4. **고정 조인트가 있는 강체 프리마에 관절구조 루트 스키마 적용**:

   이는 유효한 시나리오이지만 권장되지 않습니다. 이는 예기치 못한 동작을 유발할 수 있기 때문입니다. 이 경우, 관절구조는 여전히 부동 베이스 시스템으로 파싱됩니다. 그러나 세계 프레임과 루트 링크 사이에 생성된 고정 조인트는 최대 좌표 트리의 일부로 간주됩니다. 이는 PhysX가 관절구조를 고정 베이스 시스템으로 간주하는 방식과 다릅니다. 따라서 시뮬레이션이 예상대로 동작하지 않을 수 있습니다.
   ```text
   ArticulationXform
       └── Link0Xform  (RigidBody and ArticulationRoot schema)
       └── FixedJoint (connecting the world frame and Link0Xform)
   ```

부동 베이스 관절구조의 경우, 루트 프리마에는 보통 강체 속성과 관절구조 루트 속성이 모두 적용됩니다. 그러나 이 프리마를 세계 프레임에 직접 연결하면 시뮬레이션에서 고정 조인트를 최대 좌표 트리의 일부로 간주합니다. 이는 PhysX가 관절구조를 고정 베이스 시스템으로 간주하는 방식과 다릅니다.

내부적으로, 파라미터 [`sim.schemas.ArticulationRootPropertiesCfg.fix_root_link`](../api/lab/isaaclab.sim.schemas.md#isaaclab.sim.schemas.ArticulationRootPropertiesCfg.fix_root_link)가 True로 설정되고 관절구조가 부동 베이스 시스템으로 감지되면, 세계 프레임과 관절구조의 루트 강체 링크 사이에 고정 조인트가 생성됩니다. 그러나 PhysX 파서가 관절구조를 고정 베이스 시스템으로 간주하도록 만들려면, 관절구조 루트 속성을 루트 강체 프리마에서 제거하고 부모 프리마에 대신 적용해야 합니다.

#### NOTE
Isaac Sim의 향후 릴리스에서는 PhysX의 관절구조 루트 스키마에 명시적 플래그가 추가되어 고정 베이스 시스템과 부동 베이스 시스템 사이를 전환할 수 있게 됩니다. 이를 통해 위의 회피 방법이 필요 없어질 것입니다.
