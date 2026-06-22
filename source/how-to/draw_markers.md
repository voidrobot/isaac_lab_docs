# 시각화 마커 생성

시각화 마커는 환경의 상태를 디버깅하는 데 유용합니다. 시뮬레이션에서 프레임, 명령 및 기타 정보를 시각화하는 데 사용할 수 있습니다.

Isaac Sim은 자체 `isaacsim.util.debug_draw` 확장을 제공하지만, 이는 점, 선 및 스플라인만 렌더링하는 데 제한됩니다. 더 복잡한 모양을 렌더링해야 하는 경우, [`markers.VisualizationMarkers`](../api/lab/isaaclab.markers.md#isaaclab.markers.VisualizationMarkers) 클래스를 사용할 수 있습니다.

이 가이드는 `IsaacLab/scripts/demos` 디렉터리의 샘플 스크립트 `markers.py`와 함께 제공됩니다.

### markers.py 코드

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""이 스크립트는 다양한 유형의 마커를 시연합니다.

.. code-block:: bash

    # 사용법
    ./isaaclab.sh -p scripts/demos/markers.py

"""

"""Isaac Sim 시뮬레이터 먼저 실행합니다."""

import argparse

from isaaclab.app import AppLauncher

# add argparse arguments
parser = argparse.ArgumentParser(description="이 스크립트는 다양한 유형의 마커를 시연합니다.")
# append AppLauncher cli args
AppLauncher.add_app_launcher_args(parser)
# parse the arguments
args_cli = parser.parse_args()

# launch omniverse app
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app

"""나머지 모든 내용은 여기부터 시작됩니다."""

import torch

import isaaclab.sim as sim_utils
from isaaclab.markers import VisualizationMarkers, VisualizationMarkersCfg
from isaaclab.sim import SimulationContext
from isaaclab.utils.assets import ISAAC_NUCLEUS_DIR, ISAACLAB_NUCLEUS_DIR
from isaaclab.utils.math import quat_from_angle_axis


def define_markers() -> VisualizationMarkers:
    """다양한 모양의 마커를 정의합니다."""
    marker_cfg = VisualizationMarkersCfg(
        prim_path="/Visuals/myMarkers",
        markers={
            "frame": sim_utils.UsdFileCfg(
                usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/UIElements/frame_prim.usd",
                scale=(0.5, 0.5, 0.5),
            ),
            "arrow_x": sim_utils.UsdFileCfg(
                usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/UIElements/arrow_x.usd",
                scale=(1.0, 0.5, 0.5),
                visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 1.0, 1.0)),
            ),
            "cube": sim_utils.CuboidCfg(
                size=(1.0, 1.0, 1.0),
                visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(1.0, 0.0, 0.0)),
            ),
            "sphere": sim_utils.SphereCfg(
                radius=0.5,
                visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 1.0, 0.0)),
            ),
            "cylinder": sim_utils.CylinderCfg(
                radius=0.5,
                height=1.0,
                visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 0.0, 1.0)),
            ),
            "cone": sim_utils.ConeCfg(
                radius=0.5,
                height=1.0,
                visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(1.0, 1.0, 0.0)),
            ),
            "mesh": sim_utils.UsdFileCfg(
                usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/Blocks/DexCube/dex_cube_instanceable.usd",
                scale=(10.0, 10.0, 10.0),
            ),
            "mesh_recolored": sim_utils.UsdFileCfg(
                usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/Blocks/DexCube/dex_cube_instanceable.usd",
                scale=(10.0, 10.0, 10.0),
                visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(1.0, 0.25, 0.0)),
            ),
            "robot_mesh": sim_utils.UsdFileCfg(
                usd_path=f"{ISAACLAB_NUCLEUS_DIR}/Robots/ANYbotics/ANYmal-C/anymal_c.usd",
                scale=(2.0, 2.0, 2.0),
                visual_material=sim_utils.GlassMdlCfg(glass_color=(0.0, 0.1, 0.0)),
            ),
        },
    )
    return VisualizationMarkers(marker_cfg)


def main():
    """메인 함수."""
    # 키트 헬퍼 로드
    sim_cfg = sim_utils.SimulationCfg(dt=0.01, device=args_cli.device)
    sim = SimulationContext(sim_cfg)
    # 메인 카메라 설정
    sim.set_camera_view([0.0, 18.0, 12.0], [0.0, 3.0, 0.0])

    # 스테이지에 객체 생성
    # 조명
    cfg = sim_utils.DomeLightCfg(intensity=3000.0, color=(0.75, 0.75, 0.75))
    cfg.func("/World/Light", cfg)

    # 마커 생성
    my_visualizer = define_markers()

    # 마커가 배치될 그리드 위치 정의
    num_markers_per_type = 5
    grid_spacing = 2.0
    # 너비와 높이의 반 계산
    half_width = (num_markers_per_type - 1) / 2.0
    half_height = (my_visualizer.num_prototypes - 1) / 2.0
    # 원점을 중심으로 하는 x 및 y 범위 생성
    x_range = torch.arange(-half_width * grid_spacing, (half_width + 1) * grid_spacing, grid_spacing)
    y_range = torch.arange(-half_height * grid_spacing, (half_height + 1) * grid_spacing, grid_spacing)
    # 그리드 생성
    x_grid, y_grid = torch.meshgrid(x_range, y_range, indexing="ij")
    x_grid = x_grid.reshape(-1)
    y_grid = y_grid.reshape(-1)
    z_grid = torch.zeros_like(x_grid)
    # 마커 위치
    marker_locations = torch.stack([x_grid, y_grid, z_grid], dim=1)
    marker_indices = torch.arange(my_visualizer.num_prototypes).repeat(num_markers_per_type)

    # 시뮬레이터 실행
    sim.reset()
    # 이제 준비 완료!
    print("[INFO]: 설정 완료...")

    # 요 각도
    yaw = torch.zeros_like(marker_locations[:, 0])
    # 물리 시뮬레이션 실행
    while simulation_app.is_running():
        # z축을 중심으로 마커 회전 (시각화 목적)
        marker_orientations = quat_from_angle_axis(yaw, torch.tensor([0.0, 0.0, 1.0]))
        # 시각화
        my_visualizer.visualize(marker_locations, marker_orientations, marker_indices=marker_indices)
        # 마커 프로토타입을 변경하는 방법을 보여주기 위해 해당 인덱스를 롤링
        if yaw[0].item() % (0.5 * torch.pi) < 0.01:
            marker_indices = torch.roll(marker_indices, 1)
        # 한 단계 진행
        sim.step()
        # yaw 증가
        yaw += 0.01


if __name__ == "__main__":
    # 메인 함수 실행
    main()
    # 시뮬레이션 앱 종료
    simulation_app.close()
```

## 마커 구성

[`VisualizationMarkersCfg`](../api/lab/isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg) 클래스는 다양한 유형의 마커를 구성하기 위한 간단한 인터페이스를 제공합니다. 다음 매개변수를 받아들입니다.

- [`prim_path`](../api/lab/isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg.prim_path): 마커 클래스에 해당하는 프라임 경로입니다.
- [`markers`](../api/lab/isaaclab.markers.md#isaaclab.markers.VisualizationMarkersCfg.markers): 클래스가 처리하는 다양한 마커 프로토타입을 지정하는 사전입니다. 키는 마커 프로토타입의 이름이고, 값은 해당 프로토타입의 생성 구성입니다.

#### NOTE
마커 프로토타입이 물리 속성을 포함하는 구성으로 지정되는 경우, 이러한 속성은 제거됩니다.这是因为 마커는 시뮬레이션을 위해 설계되지 않았기 때문입니다.

다음은 구성할 수 있는 다양한 유형의 마커를 보여줍니다. 콘과 구와 같은 간단한 모양부터 프레임이나 화살표와 같은 더 복잡한 기하학까지 다양합니다. 마커 프로토타입은 USD 파일에서도 구성할 수 있습니다.

```python
marker_cfg = VisualizationMarkersCfg(
    prim_path="/Visuals/myMarkers",
    markers={
        "frame": sim_utils.UsdFileCfg(
            usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/UIElements/frame_prim.usd",
            scale=(0.5, 0.5, 0.5),
        ),
        "arrow_x": sim_utils.UsdFileCfg(
            usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/UIElements/arrow_x.usd",
            scale=(1.0, 0.5, 0.5),
            visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 1.0, 1.0)),
        ),
        "cube": sim_utils.CuboidCfg(
            size=(1.0, 1.0, 1.0),
            visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(1.0, 0.0, 0.0)),
        ),
        "sphere": sim_utils.SphereCfg(
            radius=0.5,
            visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 1.0, 0.0)),
        ),
        "cylinder": sim_utils.CylinderCfg(
            radius=0.5,
            height=1.0,
            visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 0.0, 1.0)),
        ),
        "cone": sim_utils.ConeCfg(
            radius=0.5,
            height=1.0,
            visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(1.0, 1.0, 0.0)),
        ),
        "mesh": sim_utils.UsdFileCfg(
            usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/Blocks/DexCube/dex_cube_instanceable.usd",
            scale=(10.0, 10.0, 10.0),
        ),
        "mesh_recolored": sim_utils.UsdFileCfg(
            usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/Blocks/DexCube/dex_cube_instanceable.usd",
            scale=(10.0, 10.0, 10.0),
            visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(1.0, 0.25, 0.0)),
        ),
        "robot_mesh": sim_utils.UsdFileCfg(
            usd_path=f"{ISAACLAB_NUCLEUS_DIR}/Robots/ANYbotics/ANYmal-C/anymal_c.usd",
            scale=(2.0, 2.0, 2.0),
            visual_material=sim_utils.GlassMdlCfg(glass_color=(0.0, 0.1, 0.0)),
        ),
    },
)
```

## 마커 그리기

마커를 그리려면 [`visualize`](../api/lab/isaaclab.markers.md#isaaclab.markers.VisualizationMarkers.visualize) 메서드를 호출합니다. 이 메서드는 마커의 포즈와 해당 마커 프로토타입을 인수로 받아서 그립니다.

```python
while simulation_app.is_running():
    # z축을 중심으로 마커 회전 (시각화 목적)
    marker_orientations = quat_from_angle_axis(yaw, torch.tensor([0.0, 0.0, 1.0]))
    # 시각화
    my_visualizer.visualize(marker_locations, marker_orientations, marker_indices=marker_indices)
    # 마커 프로토타입을 변경하는 방법을 보여주기 위해 해당 인덱스를 롤링
    if yaw[0].item() % (0.5 * torch.pi) < 0.01:
```

## 스크립트 실행

동반 스크립트를 실행하려면 다음 명령을 실행하세요:

```bash
./isaaclab.sh -p scripts/demos/markers.py
```

시뮬레이션이 시작되며, 격자 패턴으로 배열된 다양한 유형의 마커를 관찰할 수 있습니다. 마커는 각각의 축을 중심으로 회전합니다. 또한 몇 번의 회전마다 격자 위에서 앞으로 굴러갑니다.

시뮬레이션을 중지하려면 창을 닫거나 터미널에서 `Ctrl+C`를 사용하세요.
