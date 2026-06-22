<a id="how-to-save-images-and-3d-reprojection"></a>

# 저장된 이미지와 3D 재투영

이 가이드는 `IsaacLab/scripts/tutorials/04_sensors` 디렉터리의 `run_usd_camera.py` 스크립트와 함께 제공됩니다.

### run_usd_camera.py 코드

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 Isaac Lab 프레임워크에서 카메라 센서를 사용하는 방법을 보여줍니다.

카메라 센서는 Omniverse Replicator API를 통해 생성되고 인터페이스됩니다. 그러나 시뮬레이터나 OpenGL 컨벤션 대신 로봇공학 또는 ROS 컨벤션을 사용합니다.

.. code-block:: bash

    # GUI 사용 시
    ./isaaclab.sh -p scripts/tutorials/04_sensors/run_usd_camera.py --enable_cameras

    # 헤드리스 사용 시
    ./isaaclab.sh -p scripts/tutorials/04_sensors/run_usd_camera.py --headless --enable_cameras

"""

"""Isaac Sim 시뮬레이터 먼저 실행하기."""

import argparse

from isaaclab.app import AppLauncher

# argparse 인수 추가
parser = argparse.ArgumentParser(description="이 스크립트는 카메라 센서를 사용하는 방법을 보여줍니다.")
parser.add_argument(
    "--draw",
    action="store_true",
    default=False,
    help="``--camera_id`` 로 지정된 인덱스의 카메라에서 포인트클라우드를 그립니다.",
)
parser.add_argument(
    "--save",
    action="store_true",
    default=False,
    help="``--camera_id`` 로 지정된 인덱스의 카메라 데이터를 저장합니다."
)
parser.add_argument(
    "--camera_id",
    type=int,
    choices={0, 1},
    default=0,
    help=(
        "포인트를 표시하거나 카메라 데이터를 저장하는 데 사용할 카메라 ID입니다. 기본값은 0입니다."
        " 뷰포트는 항상 카메라 0의 관점으로 초기화됩니다."
    ),
)
# AppLauncher cli 인수 추가
AppLauncher.add_app_launcher_args(parser)
# 인수 파싱
args_cli = parser.parse_args()

# omniverse 앱 실행
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app

"""나머지 부분은 여기서부터 시작됩니다."""

import os
import random

import numpy as np
import torch

import omni.replicator.core as rep

import isaaclab.sim as sim_utils
from isaaclab.assets import RigidObject, RigidObjectCfg
from isaaclab.markers import VisualizationMarkers
from isaaclab.markers.config = RAY_CASTER_MARKER_CFG
from isaaclab.sensors.camera = Camera, CameraCfg
from isaaclab.sensors.camera.utils = create_pointcloud_from_depth
from isaaclab.utils = convert_dict_to_backend


def define_sensor() -> Camera:
    """장면에 추가할 카메라 센서를 정의합니다."""
    # 카메라 센서 설정
    # 레이-캐스트 카메라와 달리, 이 위치에 프림을 생성합니다.
    # 이는 카메라 센서가 이러한 프림에 연결됨을 의미합니다.
    sim_utils.create_prim("/World/Origin_00", "Xform")
    sim_utils.create_prim("/World/Origin_01", "Xform")
    camera_cfg = CameraCfg(
        prim_path="/World/Origin_.*/CameraSensor",
        update_period=0,
        height=480,
        width=640,
        data_types=[
            "rgb",
            "distance_to_image_plane",
            "normals",
            "semantic_segmentation",
            "instance_segmentation_fast",
            "instance_id_segmentation_fast",
        ],
        colorize_semantic_segmentation=True,
        colorize_instance_id_segmentation=True,
        colorize_instance_segmentation=True,
        spawn=sim_utils.PinholeCameraCfg(
            focal_length=24.0, focus_distance=400.0, horizontal_aperture=20.955, clipping_range=(0.1, 1.0e5)
        ),
    )
    # 카메라 생성
    camera = Camera(cfg=camera_cfg)

    return camera


def design_scene() -> dict:
    """장면을 설계합니다."""
    # 장면 채우기
    # -- 지면 평면
    cfg = sim_utils.GroundPlaneCfg()
    cfg.func("/World/defaultGroundPlane", cfg)
    # -- 조명
    cfg = sim_utils.DistantLightCfg(intensity=3000.0, color=(0.75, 0.75, 0.75))
    cfg.func("/World/Light", cfg)

    # 장면 엔티티를 담을 딕셔너리 생성
    scene_entities = {}

    # 객체를 담을 Xform 생성
    sim_utils.create_prim("/World/Objects", "Xform")
    # 무작위 객체 생성
    for i in range(8):
        # 무작위 위치 샘플링
        position = np.random.rand(3) - np.asarray([0.05, 0.05, -1.0])
        position *= np.asarray([1.5, 1.5, 0.5])
        # 무작위 색상 샘플링
        color = (random.random(), random.random(), random.random())
        # 무작위 프림 유형 선택
        prim_type = random.choice(["Cube", "Cone", "Cylinder"])
        common_properties = {
            "rigid_props": sim_utils.RigidBodyPropertiesCfg(),
            "mass_props": sim_utils.MassPropertiesCfg(mass=5.0),
            "collision_props": sim_utils.CollisionPropertiesCfg(),
            "visual_material": sim_utils.PreviewSurfaceCfg(diffuse_color=color, metallic=0.5),
            "semantic_tags": [("class", prim_type)],
        }
        if prim_type == "Cube":
            shape_cfg = sim_utils.CuboidCfg(size=(0.25, 0.25, 0.25), **common_properties)
        elif prim_type == "Cone":
            shape_cfg = sim_utils.ConeCfg(radius=0.1, height=0.25, **common_properties)
        elif prim_type == "Cylinder":
            shape_cfg = sim_utils.CylinderCfg(radius=0.25, height=0.25, **common_properties)
        # rigid 객체
        obj_cfg = RigidObjectCfg(
            prim_path=f"/World/Objects/Obj_{i:02d}",
            spawn=shape_cfg,
            init_state=RigidObjectCfg.InitialStateCfg(pos=position),
        )
        scene_entities[f"rigid_object{i}"] = RigidObject(cfg=obj_cfg)

    # 센서
    camera = define_sensor()

    # 장면 정보 반환
    scene_entities["camera"] = camera
    return scene_entities


def run_simulator(sim: sim_utils.SimulationContext, scene_entities: dict):
    """시뮬레이터를 실행합니다."""
    # 간단한 표기법을 위한 엔티티 추출
    camera: Camera = scene_entities["camera"]

    # replicator 작성자 생성
    output_dir = os.path.join(os.path.dirname(os.path.realpath(__file__)), "output", "camera")
    rep_writer = rep.BasicWriter(
        output_dir=output_dir,
        frame_padding=0,
        colorize_instance_id_segmentation=camera.cfg.colorize_instance_id_segmentation,
        colorize_instance_segmentation=camera.cfg.colorize_instance_segmentation,
        colorize_semantic_segmentation=camera.cfg.colorize_semantic_segmentation,
    )

    # 카메라 위치, 타겟, 방향
    camera_positions = torch.tensor([[2.5, 2.5, 2.5], [-2.5, -2.5, 2.5]], device=sim.device)
    camera_targets = torch.tensor([[0.0, 0.0, 0.0], [0.0, 0.0, 0.0]], device=sim.device)
    # 이 방향들은 ROS 컨벤션에 따라 있으며, 원점을 바라보도록 카메라를 배치합니다
    camera_orientations = torch.tensor(  # noqa: F841
        [[-0.1759, 0.3399, 0.8205, -0.4247], [-0.4247, 0.8205, -0.3399, 0.1759]], device=sim.device
    )

    # 자세 설정: 카메라의 자세를 설정하는 두 가지 방법
    # -- 옵션-1: 뷰를 사용하여 자세 설정
    camera.set_world_poses_from_view(camera_positions, camera_targets)
    # -- 옵션-2: ROS를 사용하여 자세 설정
    # camera.set_world_poses(camera_positions, camera_orientations, convention="ros")

    # 시각화 및 저장에 사용할 카메라 인덱스
    camera_index = args_cli.camera_id

    # --draw 옵션을 위한 마커를 is_running() 루프 외부에 생성
    if sim.has_gui() and args_cli.draw:
        cfg = RAY_CASTER_MARKER_CFG.replace(prim_path="/Visuals/CameraPointCloud")
        cfg.markers["hit"].radius = 0.002
        pc_markers = VisualizationMarkers(cfg)

    # 물리 시뮬레이션 실행
    while simulation_app.is_running():
        # 시뮬레이션 스텝
        sim.step()
        # 카메라 데이터 업데이트
        camera.update(dt=sim.get_physics_dt())

        # 카메라 정보 출력
        print(camera)
        if "rgb" in camera.data.output.keys():
            print("Received shape of rgb image        : ", camera.data.output["rgb"].shape)
        if "distance_to_image_plane" in camera.data.output.keys():
            print("Received shape of depth image      : ", camera.data.output["distance_to_image_plane"].shape)
        if "normals" in camera.data.output.keys():
            print("Received shape of normals          : ", camera.data.output["normals"].shape)
        if "semantic_segmentation" in camera.data.output.keys():
            print("Received shape of semantic segm.   : ", camera.data.output["semantic_segmentation"].shape)
        if "instance_segmentation_fast" in camera.data.output.keys():
            print("Received shape of instance segm.   : ", camera.data.output["instance_segmentation_fast"].shape)
        if "instance_id_segmentation_fast" in camera.data.output.keys():
            print("Received shape of instance id segm.: ", camera.data.output["instance_id_segmentation_fast"].shape)
        print("-------------------------------")

        # 카메라 데이터 추출
        if args_cli.save:
            # camera_index의 카메라에서 이미지 저장
            # 참고: BasicWriter는 numpy 형식의 데이터만 지원하므로 데이터를 numpy로 변환해야 합니다.
            single_cam_data = convert_dict_to_backend(
                {k: v[camera_index] for k, v in camera.data.output.items()}, backend="numpy"
            )

            # 기타 정보 추출
            single_cam_info = camera.data.info[camera_index]

            # 저장하기 위해 replicator 형식으로 데이터 다시 패킹
            rep_output = {"annotators": {}}
            for key, data, info in zip(single_cam_data.keys(), single_cam_data.values(), single_cam_info.values()):
                if info is not None:
                    rep_output["annotators"][key] = {"render_product": {"data": data, **info}}
                else:
                    rep_output["annotators"][key] = {"render_product": {"data": data}}
            # 이미지 저장
            # 참고: Replicator가 이미지를 저장할 수 있도록 On-time 데이터를 제공해야 합니다.
            rep_output["trigger_outputs"] = {"on_time": camera.frame[camera_index]}
            rep_writer.write(rep_output)

        # GUI가 있고 --draw가 전달된 경우 포인트클라우드 그리기
        if sim.has_gui() and args_cli.draw and "distance_to_image_plane" in camera.data.output.keys():
            # camera_index의 카메라에서 포인트클라우드 유도
            pointcloud = create_pointcloud_from_depth(
                intrinsic_matrix=camera.data.intrinsic_matrices[camera_index],
                depth=camera.data.output["distance_to_image_plane"][camera_index],
                position=camera.data.pos_w[camera_index],
                orientation=camera.data.quat_w_ros[camera_index],
                device=sim.device,
            )

            # 처음 몇 단계에서는 인스턴스가 아직 생성되지 않았고 Camera.data가 비어있을 수 있습니다.
            # 빈 포인트클라우드를 시각화하려 하면 시뮬레이션이 충돌할 수 있으므로 비어있지 않은지 확인합니다.
            if pointcloud.size()[0] > 0:
                pc_markers.visualize(translations=pointcloud)


def main():
    """메인 함수."""
    # 시뮬레이션 컨텍스트 로드
    sim_cfg = sim_utils.SimulationCfg(device=args_cli.device)
    sim = sim_utils.SimulationContext(sim_cfg)
    # 메인 카메라 설정
    sim.set_camera_view([2.5, 2.5, 2.5], [0.0, 0.0, 0.0])
    # 장면 설계
    scene_entities = design_scene()
    # 시뮬레이터 실행
    sim.reset()
    # 이제 준비 완료!
    print("[INFO]: Setup complete...")
    # 시뮬레이터 실행
    run_simulator(sim, scene_entities)


if __name__ == "__main__":
    # 메인 함수 실행
    main()
    # 시뮬레이터 앱 종료
    simulation_app.close()
```

## 기본 Writer를 사용한 저장

Omniverse Replicator의 기본 작성 클래스를 사용하여 카메라 출력을 저장할 수 있습니다. 이 클래스를 사용하면 numpy 형식으로 이미지를 저장할 수 있습니다. 기본 작성자에 대한 자세한 내용은 다음을 참조하세요.
[문서](https://docs.omniverse.nvidia.com/extensions/latest/ext_replicator/writer_examples.html).

```python
    rep_writer = rep.BasicWriter(
        output_dir=output_dir,
        frame_padding=0,
        colorize_instance_id_segmentation=camera.cfg.colorize_instance_id_segmentation,
        colorize_instance_segmentation=camera.cfg.colorize_instance_segmentation,
        colorize_semantic_segmentation=camera.cfg.colorize_semantic_segmentation,
    )

```

시뮬레이터를 단계별로 실행하는 동안 이미지를 정의된 폴더에 저장할 수 있습니다. BasicWriter는 NumPy 형식으로만 데이터를 저장할 수 있으므로,
먼저 PyTorch 센서를 NumPy 배열로 변환한 후 딕셔너리에 패킹해야 합니다.

```python
            # 카메라 인덱스에서 카메라 이미지 저장
            # 주의: BasicWriter는 numpy 형식으로만 데이터를 저장할 수 있으므로 데이터를 numpy로 변환해야 합니다.
            single_cam_data = convert_dict_to_backend(
                {k: v[camera_index] for k, v in camera.data.output.items()}, backend="numpy"
            )

            # 기타 정보 추출
            single_cam_info = camera.data.info[camera_index]
```

이 단계를 완료한 후 BasicWriter를 사용하여 이미지를 저장할 수 있습니다.

```python
            # 데이터를 replicator 형식으로 다시 패킹하여 해당 작성자로 저장
            rep_output = {"annotators": {}}
            for key, data, info in zip(single_cam_data.keys(), single_cam_data.values(), single_cam_info.values()):
                if info is not None:
                    rep_output["annotators"][key] = {"render_product": {"data": data, **info}}
                else:
                    rep_output["annotators"][key] = {"render_product": {"data": data}}
            # 이미지 저장
            # 참고: Replicator가 이미지를 저장하도록 On-time 데이터를 제공해야 합니다.
            rep_output["trigger_outputs"] = {"on_time": camera.frame[camera_index]}
            rep_writer.write(rep_output)
```

## 3D 공간으로 투영

깊이 이미지를 3D 공간으로 투영하는 유틸리티를 포함합니다. 재투영 작업은 PyTorch 연산을 사용하여 수행되므로 계산 속도가 더 빠릅니다.

```python
from isaaclab.utils.math import transform_points, unproject_depth

# 월드 프레임의 포인트 클라우드
points_3d_cam = unproject_depth(
   camera.data.output["distance_to_image_plane"], camera.data.intrinsic_matrices
)

points_3d_world = transform_points(points_3d_cam, camera.data.pos_w, camera.data.quat_w_ros)
```

대안으로 `isaaclab.sensors.camera.utils.create_pointcloud_from_depth()` 함수를 사용하여
깊이 이미지에서 포인트 클라우드를 생성하고 이를 월드 프레임으로 변환할 수도 있습니다.

```python
            # 카메라 인덱스에서 카메라 포인트 클라우드 생성
            pointcloud = create_pointcloud_from_depth(
                intrinsic_matrix=camera.data.intrinsic_matrices[camera_index],
                depth=camera.data.output["distance_to_image_plane"][camera_index],
                position=camera.data.pos_w[camera_index],
                orientation=camera.data.quat_w_ros[camera_index],
                device=sim.device,
            )

```

생성된 포인트 클라우드는 Isaac Sim의 `isaacsim.util.debug_draw` 확장을 사용하여 시각화할 수 있습니다.
이를 통해 3D 공간에서 포인트 클라우드를 쉽게 시각화할 수 있습니다.

```python
            # 처음 몇 단계에서는 인스턴싱이 여전히 진행 중이고 Camera.data
            # 가 비어 있을 수 있습니다. 빈 포인트 클라우드를 시각화하려고 시도하면
            # 시뮬레이션이 충돌할 수 있으므로 포인트 클라우드가 비어 있지 않은지 확인합니다.
            if pointcloud.size()[0] > 0:
                pc_markers.visualize(translations=pointcloud)
```

## 스크립트 실행

수반되는 스크립트를 실행하려면 다음 명령을 실행하세요:

```bash
# 저장 및 그림 그리기 사용
./isaaclab.sh -p scripts/tutorials/04_sensors/run_usd_camera.py --save --draw --enable_cameras

# 헤드리스 모드에서 저장만 사용
./isaaclab.sh -p scripts/tutorials/04_sensors/run_usd_camera.py --save --headless --enable_cameras
```

시뮬레이션이 시작되어 서로 다른 물체가 떨어지는 것을 관찰할 수 있습니다. `IsaacLab/scripts/tutorials/04_sensors` 디렉터리에 출력 폴더가 생성되며,
이 폴더에 이미지가 저장됩니다. 또한 뷰포트에서 3D 공간에 포인트 클라우드가 그려진 것을 볼 수 있습니다.

시뮬레이션을 중지하려면 창을 닫거나 터미널에서 `Ctrl+C`를 사용하세요.
