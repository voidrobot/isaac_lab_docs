<a id="how-to-estimate-how-cameras-can-run"></a>

# 훈련에 적합한 카메라 수를 결정하는 방법

현재 Isaac Lab에는 USD 카메라(표준), 타일 카메라, 레이스 캐스터 카메라 등 여러 종류의 카메라가 있습니다. 이들 카메라 종류는 기능과 성능 면에서 차이가 있습니다. `benchmark_cameras.py` 스크립트를 사용하면 카메라 종류 간의 차이를 이해하고, 카메라 수, 이미지 차원, 데이터 유형과 같은 매개변수에 따라 상대적인 성능을 특성화할 수 있습니다.

이 유틸리티를 제공함으로써 사용자의 시나리오 요구 사항을 충족하면서 가장 성능이 좋은 카메라 유형/매개변수를 쉽게 찾을 수 있습니다. 이 유틸리티는 단계 시간을 최소화하면서 환경을 최대한 많이 실행하는 것을 목표로 할 때 실행할 수 있는 카메라의 최대 수를 추정하는 데에도 도움이 됩니다.

이 유틸리티는 체육관 레지스트리의 기존 작업에 카메라를 주입할 수 있어 특정 시나리오에서 카메라를 벤치마크하는 데 유용할 수 있습니다. 또한 `pynvml`을 설치하면 이 유틸리티가 자동으로 특정 시스템 리소스 사용률 임계값 이하에서 작업 환경에서 실행할 수 있는 최대 카메라 수를 찾아냅니다(훈련 없이 매 타임스텝마다 0 행동을 취함).

이 가이드는 `scripts/benchmarks` 디렉터리의 `benchmark_cameras.py` 스크립트를’accomp니다.

### benchmark_cameras.py 코드

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 다양한 원하는 설정에 대해 시스템이 실질적으로 실행할 수 있는 카메라 수를 결정하는 데 도움이 될 수 있습니다.

주어진 작업 환경을 카메라 주입용으로 제공하거나, 샘플 장면만 테스트할 수 있습니다.
또한 자동 튠 기능을 통해 작업에서 실행할 수 있는 최대 카메라 수를 자동으로 찾을 수 있습니다.

.. code-block:: bash

    # GUI 사용법
    ./isaaclab.sh -p scripts/benchmarks/benchmark_cameras.py -h

    # 헤드리스 사용법
    ./isaaclab.sh -p scripts/benchmarks/benchmark_cameras.py -h --headless

"""

"""Isaac Sim 시뮬레이터 먼저 실행."""

import argparse
from collections.abc import Callable

from isaaclab.app import AppLauncher

# 인수 파싱
args_cli = argparse.Namespace()

parser = argparse.ArgumentParser(description="이 스크립트는 실행할 수 있는 카메라 수를 벤치마크하는 데 도움이 됩니다.")

"""
다음 인수는 환경을 주입하고 카메라 수를 자동으로 결정하려 할 때만 제공해야 합니다.
"""
parser.add_argument(
    "--task",
    type=str,
    default=None,
    required=False,
    help="이 인수를 제공하여 알려진 관리자 기반 작업 환경 내에서 카메라를 생성합니다.",
)

parser.add_argument(
    "--autotune",
    default=False,
    action="store_true",
    help=(
        "자동 튠은 제공된 작업 환경에서만 지원됩니다."
        " 이 인수를 제공하여 원하는 임계값에 도달할 때까지 환경 수를 증가시킵니다."
        "환경에 pynvml을 설치하세요; ./isaaclab.sh -m pip install pynvml"
    ),
)

parser.add_argument(
    "--task_num_cameras_per_env",
    type=int,
    default=1,
    help="알려진 작업을 사용할 때 환경당 카메라 수입니다.",
)

parser.add_argument(
    "--use_fabric", action="store_true", default=False, help="패브릭을 활성화하고 USD I/O 작업을 사용합니다."
)

parser.add_argument(
    "--autotune_max_percentage_util",
    nargs="+",
    type=float,
    default=[100.0, 80.0, 80.0, 80.0],
    required=False,
    help=(
        "자동 튠이 종료되기 전에 도달해야 하는 시스템 사용률 백분율 임계값입니다. "
        "이 중 하나라도 임계값에 도달하면 자동 튠이 중지됩니다."
        "임계값은 순서대로 최대 CPU 사용률 백분율,"
        "최대 RAM 사용률 백분율, 최대 GPU 계산 사용률 백분율, "
        "그리고 최대 GPU 메모리 사용률 백분율입니다."
    ),
)

parser.add_argument(
    "--autotune_max_camera_count", type=int, default=4096, help="자동 튠에서 허용되는 최대 카메라 수입니다."
)

parser.add_argument(
    "--autotune_camera_count_interval",
    type=int,
    default=25,
    help=(
        "현재 카메라 수가 허용된 시스템 리소스 사용률 한도 내에 있는 경우"
        "환경에 추가로 시도할 카메라 수입니다."
    ),
)

"""
다음 인수는 작업 환경에 카메라를 주입할 때와 작업 환경과 무관하게 카메라를 생성할 때 공유됩니다.
"""

parser.add_argument(
    "--num_tiled_cameras",
    type=int,
    default=0,
    required=False,
    help="생성할 타일 카메라 수. 자동 튠 시 이는 시작 카메라 수입니다.",
)

parser.add_argument(
    "--num_standard_cameras",
    type=int,
    default=0,
    required=False,
    help="생성할 표준 카메라 수. 자동 튠 시 이는 시작 카메라 수입니다.",
)

parser.add_argument(
    "--num_ray_caster_cameras",
    type=int,
    default=0,
    required=False,
    help="생성할 레이스 캐스터 카메라 수. 자동 튠 시 이는 시작 카메라 수입니다.",
)

parser.add_argument(
    "--tiled_camera_data_types",
    nargs="+",
    type=str,
    default=["rgb", "depth"],
    help="타일 카메라가 렌더링하는 데이터 유형입니다.",
)

parser.add_argument(
    "--standard_camera_data_types",
    nargs="+",
    type=str,
    default=["rgb", "distance_to_image_plane", "distance_to_camera"],
    help="표준 카메라가 렌더링하는 데이터 유형입니다.",
)

parser.add_argument(
    "--ray_caster_camera_data_types",
    nargs="+",
    type=str,
    default=["distance_to_image_plane"],
    help="레이스 캐스터 카메라가 렌더링하는 데이터 유형입니다.",
)

parser.add_argument(
    "--ray_caster_visible_mesh_prim_paths",
    nargs="+",
    type=str,
    default=["/World/ground"],
    help="WARNING: 레이스 캐스터는 현재 단일 정적 객체에 대해서만 캐스트를 수행할 수 있습니다",
)

parser.add_argument(
    "--convert_depth_to_camera_to_image_plane",
    action="store_true",
    default=True,
    help=(
        "원근 뷰(카메라까지의 거리 데이터 유형)에서 왜곡 보정을 해제하여"
        "직교 뷰(이미지 평면까지의 거리 데이터 유형)로 변환하여 깊이 데이터를 처리합니다."
        "이를 통해 왜곡 없는 깊이 이미지/포인트 클라우드를 생성해야 합니다."
    ),
)

parser.add_argument(
    "--keep_raw_depth",
    dest="convert_depth_to_camera_to_image_plane",
    action="store_false",
    help=(
        "원근 뷰(카메라까지의 거리)에서 왜곡 보정을 해제하여"
        "직교 뷰(이미지 평면까지의 거리 데이터 유형)로 변환하여 깊이 데이터를 처리하지 않습니다."
    ),
)

parser.add_argument(
    "--height",
    type=int,
    default=120,
    required=False,
    help="픽셀 단위의 카메라 높이",
)

parser.add_argument(
    "--width",
    type=int,
    default=140,
    required=False,
    help="픽셀 단위의 카메라 너비",
)

parser.add_argument(
    "--warm_start_length",
    type=int,
    default=3,
    required=False,
    help=(
        "벤치마크를 시작하기 전에 시뮬레이션을 실행할 단계 수입니다."
        "시뮬레이션 시작 시 빈 이미지가 나오는 것을 방지하기 위해 필요합니다."
    ),
)

parser.add_argument(
    "--experiment_length",
    type=int,
    default=15,
    required=False,
    help="평균을 낼 단계 수입니다.",
)

# 이 인수는 작업이 제공되지 않을 때만 사용됩니다.
parser.add_argument(
    "--num_objects",
    type=int,
    default=10,
    required=False,
    help="알려진 작업을 사용하지 않을 때 장면에서 생성할 객체 수입니다.",
)


AppLauncher.add_app_launcher_args(parser)
args_cli = parser.parse_args()
args_cli.enable_cameras = True

if args_cli.autotune:
    import pynvml

if len(args_cli.ray_caster_visible_mesh_prim_paths) > 1:
    print("[WARNING]: 레이스 캐스팅은 현재 단일 정적 객체에 대해서만 지원됩니다")
# 옴니버스 앱 실행
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app

"""이후의 모든 내용."""

import random
import time

import gymnasium as gym
import numpy as np
import psutil
import torch

import isaaclab.sim as sim_utils
from isaaclab.assets import RigidObject, RigidObjectCfg
from isaaclab.scene.interactive_scene import InteractiveScene
from isaaclab.sensors import (
    Camera,
    CameraCfg,
    RayCasterCamera,
    RayCasterCameraCfg,
    TiledCamera,
    TiledCameraCfg,
    patterns,
)
from isaaclab.utils.math import orthogonalize_perspective_depth, unproject_depth

from isaaclab_tasks.utils import load_cfg_from_registry

"""
카메라 생성
"""


def create_camera_base(
    camera_cfg: type[CameraCfg | TiledCameraCfg],
    num_cams: int,
    data_types: list[str],
    height: int,
    width: int,
    prim_path: str | None = None,
    instantiate: bool = True,
) -> Camera | TiledCamera | CameraCfg | TiledCameraCfg | None:
    """카메라 또는 타일 카메라 센서를 생성하는 일반화된 함수입니다."""
    # 카메라 클래스에 따라 prim 접두사 결정
    name = camera_cfg.class_type.__name__

    if instantiate:
        # 필요한 prim 생성
        for idx in range(num_cams):
            sim_utils.create_prim(f"/World/{name}_{idx:02d}", "Xform")
    if prim_path is None:
        prim_path = f"/World/{name}_.*/{name}"
    # 유효한 카메라 설정이 제공된 경우 카메라 생성
    if num_cams > 0 and len(data_types) > 0 and height > 0 and width > 0:
        cfg = camera_cfg(
            prim_path=prim_path,
            update_period=0,
            height=height,
            width=width,
            data_types=data_types,
            spawn=sim_utils.PinholeCameraCfg(
                focal_length=24, focus_distance=400.0, horizontal_aperture=20.955, clipping_range=(0.1, 1e4)
            ),
        )
        if instantiate:
            return camera_cfg.class_type(cfg=cfg)
        else:
            return cfg
    else:
        return None


def create_tiled_cameras(
    num_cams: int = 2, data_types: list[str] | None = None, height: int = 100, width: int = 120
) -> TiledCamera | None:
    if data_types is None:
        data_types = ["rgb", "depth"]
    """타일 카메라 센서를 정의합니다."""
    return create_camera_base(
        camera_cfg=TiledCameraCfg,
        num_cams=num_cams,
        data_types=data_types,
        height=height,
        width=width,
    )


def create_cameras(
    num_cams: int = 2, data_types: list[str] | None = None, height: int = 100, width: int = 120
) -> Camera | None:
    """표준 카메라를 정의합니다."""
    if data_types is None:
        data_types = ["rgb", "depth"]
    return create_camera_base(
        camera_cfg=CameraCfg, num_cams=num_cams, data_types=data_types, height=height, width=width
    )


def create_ray_caster_cameras(
    num_cams: int = 2,
    data_types: list[str] = ["distance_to_image_plane"],
    mesh_prim_paths: list[str] = ["/World/ground"],
    height: int = 100,
    width: int = 120,
    prim_path: str = "/World/RayCasterCamera_.*/RayCaster",
    instantiate: bool = True,
) -> RayCasterCamera | RayCasterCameraCfg | None:
    """레이스 캐스터 카메라를 생성합니다. 표준/타일 카메라와 다른 구성을 가집니다."""
    for idx in range(num_cams):
        sim_utils.create_prim(f"/World/RayCasterCamera_{idx:02d}/RayCaster", "Xform")

    if num_cams > 0 and len(data_types) > 0 and height > 0 and width > 0:
        cam_cfg = RayCasterCameraCfg(
            prim_path=prim_path,
            mesh_prim_paths=mesh_prim_paths,
            update_period=0,
            offset=RayCasterCameraCfg.OffsetCfg(pos=(0.0, 0.0, 0.0), rot=(1.0, 0.0, 0.0, 0.0)),
            data_types=data_types,
            debug_vis=False,
            pattern_cfg=patterns.PinholeCameraPatternCfg(
                focal_length=24.0,
                horizontal_aperture=20.955,
                height=480,
                width=640,
            ),
        )
        if instantiate:
            return RayCasterCamera(cfg=cam_cfg)
        else:
            return cam_cfg

    else:
        return None


def create_tiled_camera_cfg(prim_path: str) -> TiledCameraCfg:
    """작업 환경에 주입하기 위한 간단한 타일 카메라 구성을 가져옵니다."""
    return create_camera_base(
        TiledCameraCfg,
        num_cams=args_cli.num_tiled_cameras,
        data_types=args_cli.tiled_camera_data_types,
        width=args_cli.width,
        height=args_cli.height,
        prim_path="{ENV_REGEX_NS}/" + prim_path,
        instantiate=False,
    )


def create_standard_camera_cfg(prim_path: str) -> CameraCfg:
    """작업 환경에 주입하기 위한 간단한 표준 카메라 구성을 가져옵니다."""
    return create_camera_base(
        CameraCfg,
        num_caps=args_cli.num_standard_cameras,
        data_types=args_cli.standard_camera_data_types,
        width=args_cli.width,
        height=args_cli.height,
        prim_path="{ENV_REGEX_NS}/" + prim_path,
        instantiate=False,
    )


def create_ray_caster_camera_cfg(prim_path: str) -> RayCasterCameraCfg:
    """작업 환경에 주입하기 위한 간단한 레이스 캐스터 카메라 구성을 가져옵니다."""
    return create_ray_caster_cameras(
        num_cams=args_cli.num_ray_caster_cameras,
        data_types=args_cli.ray_caster_camera_data_types,
        width=args_cli.width,
        height=args_cli.height,
        prim_path="{ENV_REGEX_NS}/" + prim_path,
    )


"""
장면 생성
"""


def design_scene(
    num_tiled_cams: int = 2,
    num_standard_cams: int = 0,
    num_ray_caster_cams: int = 0,
    tiled_camera_data_types: list[str] | None = None,
    standard_camera_data_types: list[str] | None = None,
    ray_caster_camera_data_types: list[str] | None = None,
    height: int = 100,
    width: int = 200,
    num_objects: int = 20,
    mesh_prim_paths: list[str] = ["/World/ground"],
) -> dict:
    """장면을 설계합니다."""
    if tiled_camera_data_types is None:
        tiled_camera_data_types = ["rgb"]
    if standard_camera_data_types is None:
        standard_camera_data_types = ["rgb"]
    if ray_caster_camera_data_types is None:
        ray_caster_camera_data_types = ["distance_to_image_plane"]

    # 장면 채우기
    # -- 지면 평면
    cfg = sim_utils.GroundPlaneCfg()
    cfg.func("/World/ground", cfg)
    # -- 조명
    cfg = sim_utils.DistantLightCfg(intensity=3000.0, color=(0.75, 0.75, 0.75))
    cfg.func("/World/Light", cfg)

    # 장면 엔티티를 위한 사전 생성
    scene_entities = {}

    # 객체를 위한 Xform
    sim_utils.create_prim("/World/Objects", "Xform")
    # 랜덤 객체
    for i in range(num_objects):
        # 랜덤 위치 샘플링
        position = np.random.rand(3) - np.asarray([0.05, 0.05, -1.0])
        position *= np.asarray([1.5, 1.5, 0.5])
        # 랜덤 색상 샘플링
        color = (random.random(), random.random(), random.random())
        # 랜덤 프리미티브 타입 선택
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
        # 리지드 객체
        obj_cfg = RigidObjectCfg(
            prim_path=f"/World/Objects/Obj_{i:02d}",
            spawn=shape_cfg,
            init_state=RigidObjectCfg.InitialStateCfg(pos=position),
        )
        scene_entities[f"rigid_object{i}"] = RigidObject(cfg=obj_cfg)

    # 센서
    standard_camera = create_cameras(
        num_cams=num_standard_cams, data_types=standard_camera_data_types, height=height, width=width
    )
    tiled_camera = create_tiled_cameras(
        num_cams=num_tiled_cams, data_types=tiled_camera_data_types, height=height, width=width
    )
    ray_caster_camera = create_ray_caster_cameras(
        num_cams=num_ray_caster_cams,
        data_types=ray_caster_camera_data_types,
        mesh_prim_paths=mesh_prim_paths,
        height=height,
        width=width,
    )
    # 장면 정보 반환
    if tiled_camera is not None:
        scene_entities["tiled_camera"] = tiled_camera
    if standard_camera is not None:
        scene_entities["standard_camera"] = standard_camera
    if ray_caster_camera is not None:
        scene_entities["ray_caster_camera"] = ray_caster_camera
    return scene_entities


def inject_cameras_into_task(
    task: str,
    num_cams: int,
    camera_name_prefix: str,
    camera_creation_callable: Callable,
    num_cameras_per_env: int = 1,
) -> gym.Env:
    """작업을 로드하고 카메라를 구성에 추가한 다음 환경을 생성합니다."""
    cfg = load_cfg_from_registry(task, "env_cfg_entry_point")
    cfg.sim.device = args_cli.device
    cfg.sim.use_fabric = args_cli.use_fabric
    scene_cfg = cfg.scene

    num_envs = int(num_cams / num_cameras_per_env)
    scene_cfg.num_envs = num_envs

    for idx in range(num_cameras_per_env):
        suffix = "" if idx == 0 else str(idx)
        name = camera_name_prefix + suffix
        setattr(scene_cfg, name, camera_creation_callable(name))
    cfg.scene = scene_cfg
    env = gym.make(task, cfg=cfg)
    return env


"""
시스템 진단
"""


def get_utilization_percentages(reset: bool = False, max_values: list[float] = [0.0, 0.0, 0.0, 0.0]) -> list[float]:
    """마지막으로 reset이 true였던 이후의 최대 CPU, RAM, GPU 사용률(처리) 및
    GPU 메모리 사용률 백분율을 가져옵니다."""
    if reset:
        max_values[:] = [0, 0, 0, 0]  # 최대값 재설정

    # CPU 사용률
    cpu_usage = psutil.cpu_percent(interval=0.1)
    max_values[0] = max(max_values[0], cpu_usage)

    # RAM 사용률
    memory_info = psutil.virtual_memory()
    ram_usage = memory_info.percent
    max_values[1] = max(max_values[1], ram_usage)

    # pynvml을 사용한 GPU 사용률
    if torch.cuda.is_available():
        if args_cli.autotune:
            pynvml.nvmlInit()  # NVML 초기화
            for i in range(torch.cuda.device_count()):
                handle = pynvml.nvmlDeviceGetHandleByIndex(i)

                # GPU 사용률
                gpu_utilization = pynvml.nvmlDeviceGetUtilizationRates(handle)
                gpu_processing_utilization_percent = gpu_utilization.gpu  # GPU 코어 사용률
                max_values[2] = max(max_values[2], gpu_processing_utilization_percent)

                # GPU 메모리 사용률
                memory_info = pynvml.nvmlDeviceGetMemoryInfo(handle)
                gpu_memory_total = memory_info.total
                gpu_memory_used = memory_info.used
                gpu_memory_utilization_percent = (gpu_memory_used / gpu_memory_total) * 100
                max_values[3] = max(max_values[3], gpu_memory_utilization_percent)

            pynvml.nvmlShutdown()  # 사용 후 NVML 종료
    else:
        gpu_processing_utilization_percent = None
        gpu_memory_utilization_percent = None
    return max_values


"""
실험
"""


def run_simulator(
    sim: sim_utils.SimulationContext | None,
    scene_entities: dict | InteractiveScene,
    warm_start_length: int = 10,
    experiment_length: int = 100,
    tiled_camera_data_types: list[str] | None = None,
    standard_camera_data_types: list[str] | None = None,
    ray_caster_camera_data_types: list[str] | None = None,
    depth_predicate: Callable = lambda x: "to" in x or x == "depth",
    perspective_depth_predicate: Callable = lambda x: x == "distance_to_camera",
    convert_depth_to_camera_to_image_plane: bool = True,
    max_cameras_per_env: int = 1,
    env: gym.Env | None = None,
) -> dict:
    """모든 카메라로 시뮬레이터를 실행하고 타이밍 분석을 반환합니다. 원하는 경우 시각화합니다."""

    if tiled_camera_data_types is None:
        tiled_camera_data_types = ["rgb"]
    if standard_camera_data_types is None:
        standard_camera_data_types = ["rgb"]
    if ray_caster_camera_data_types is None:
        ray_caster_camera_data_types = ["distance_to_image_plane"]

    # 카메라 리스트 초기화
    tiled_cameras = []
    standard_cameras = []
    ray_caster_cameras = []

    # max_cameras_per_env까지 scene 엔티티에서 카메라를 동적으로 추출
    for i in range(max_cameras_per_env):
        # 타일 카메라 추출
        tiled_camera_key = f"tiled_camera{i}" if i > 0 else "tiled_camera"
        standard_camera_key = f"standard_camera{i}" if i > 0 else "standard_camera"
        ray_caster_camera_key = f"ray_caster_camera{i}" if i > 0 else "ray_caster_camera"

        try:  # 대신 key in scene_entities 확인 시 ... # 오류가 항상 발생함 even if key present
            tiled_cameras.append(scene_entities[tiled_camera_key])
            standard_cameras.append(scene_entities[standard_camera_key])
            ray_caster_cameras.append(scene_entities[ray_caster_camera_key])
        except KeyError:
            break

    # 카메라 수 초기화
    camera_lists = [tiled_cameras, standard_cameras, ray_caster_cameras]
    camera_data_types = [tiled_camera_data_types, standard_camera_data_types, ray_caster_camera_data_types]
    labels = ["tiled", "standard", "ray_caster"]

    if sim is not None:
        # 카메라 월드 포즈 설정
        for camera_list in camera_lists:
            for camera in camera_list:
                num_cameras = camera.data.intrinsic_matrices.size(0)
                positions = torch.tensor([[2.5, 2.5, 2.5]], device=sim.device).repeat(num_cameras, 1)
                targets = torch.tensor([[0.0, 0.0, 0.0]], device=sim.device).repeat(num_cameras, 1)
                camera.set_world_poses_from_view(positions, targets)

    # 타이밍 변수 초기화
    timestep = 0
    total_time = 0.0
    valid_timesteps = 0
    sim_step_time = 0.0

    while simulation_app.is_running() and timestep < experiment_length:
        print(f"On timestep {timestep} of {experiment_length}, with warm start of {warm_start_length}")
        get_utilization_percentages()

        # 총 시뮬레이션 스텝 시간 측정
        step_start_time = time.time()

        if sim is not None:
            sim.step()

        if env is not None:
            with torch.inference_mode():
                # 제로 액션 계산
                actions = torch.zeros(env.action_space.shape, device=env.unwrapped.device)
                # 액션 적용
                env.step(actions)

        # 시뮬레이션 단계 내에서 카메라 업데이트 및 비전 데이터 처리
        clouds = {}
        images = {}
        depth_images = {}

        # 모든 카메라 리스트와 해당 데이터 유형을 순회
        for camera_list, data_types, label in zip(camera_lists, camera_data_types, labels):
            for cam_idx, camera in enumerate(camera_list):
                if env is None:  # 환경이 없으면 수동으로 카메라 단계 진행
                    # scene_entities.update의 일부로 업데이트되지 않은 경우에만 카메라 업데이트
                    camera.update(dt=sim.get_physics_dt())

                for data_type in data_types:
                    data_label = f"{label}_{cam_idx}_{data_type}"

                    if depth_predicate(data_type):  # 깊 이미지이며 클라우드를 생성하려는 경우
                        depth = camera.data.output[data_type]
                        depth_images[data_label + "_raw"] = depth
                        if perspective_depth_predicate(data_type) and convert_depth_to_camera_to_image_plane:
                            depth = orthogonalize_perspective_depth(
                                camera.data.output[data_type], camera.data.intrinsic_matrices
                            )
                            depth_images[data_label + "_undistorted"] = depth

                        pointcloud = unproject_depth(depth=depth, intrinsics=camera.data.intrinsic_matrices)
                        clouds[data_label] = pointcloud
                    else:  # RGB 이미지인 경우 그대로 저장
                        image = camera.data.output[data_type]
                        images[data_label] = image

        # 단계 종료 타이밍
        step_end_time = time.time()
        sim_step_time += step_end_time - step_start_time

        if timestep > warm_start_length:
            get_utilization_percentages(reset=True)
            total_time += step_end_time - step_start_time
            valid_timesteps += 1

        timestep += 1

    # 평균 타이밍 계산
    if valid_timesteps > 0:
        avg_timestep_duration = total_time / valid_timesteps
        avg_sim_step_duration = sim_step_time / experiment_length
    else:
        avg_timestep_duration = 0.0
        avg_sim_step_duration = 0.0

    # 타이밍 분석을 사전에 패키징
    timing_analytics = {
        "average_timestep_duration": avg_timestep_duration,
        "average_sim_step_duration": avg_sim_step_duration,
        "total_simulation_time": sim_step_time,
        "total_experiment_duration": sim_step_time,
    }

    system_utilization_analytics = get_utilization_percentages()

    print("--- 벤치마크 결과 ---")
    print(f"평균 타임스텝 지속시간: {avg_timestep_duration:.6f} 초")
    print(f"평균 시뮬레이션 단계 지속시간: {avg_sim_step_duration:.6f} 초")
    print(f"총 시뮬레이션 시간: {sim_step_time:.6f} 초")
    print("\n시스템 사용률 통계:")
    print(
        f"| CPU:{system_utilization_analytics[0]}% | "
        f"RAM:{system_utilization_analytics[1]}% | "
        f"GPU 컴퓨팅:{system_utilization_analytics[2]}% | "
        f" GPU 메모리: {system_utilization_analytics[3]:.2f}% |"
    )

    return {"timing_analytics": timing_analytics, "system_utilization_analytics": system_utilization_analytics}


def main():
    """메인 함수."""
    # 시뮬레이션 컨텍스트 로드
    if args_cli.num_tiled_cameras + args_cli.num_standard_cameras + args_cli.num_ray_caster_cameras <= 0:
        raise ValueError("최소한 하나 이상의 카메라를 선택해야 합니다.")
    if (
        (args_cli.num_tiled_cameras > 0 and args_cli.num_standard_cameras > 0)
        or (args_cli.num_ray_caster_cameras > 0 and args_cli.num_standard_cameras > 0)
        or (args_cli.num_ray_caster_cameras > 0 and args_cli.num_tiled_cameras > 0)
    ):
        print("[WARNING]: 하나 이상의 카메라 유형을 선택했습니다.")
        print("[WARNING]: 의미 있는 벤치마크를 위해 ONE 가지 카메라 유형만 사용하는 것이 좋습니다.")
        print(
            "[WARNING]: 예를 들어, num_tiled_cameras=100인 경우 의미 있는 벤치마크를 위해서는"
            "num_standard_cameras와 num_ray_caster_cameras를 0으로 설정해야 합니다"
        )
        raise ValueError("한 번에 하나의 카메라 유형만 벤치마크하세요.")

    print("[INFO]: 장면 설계 중")
    if args_cli.task is None:
        print("[INFO]: 작업 환경이 제공되지 않아 랜덤 장면을 생성합니다.")
        sim_cfg = sim_utils.SimulationCfg(device=args_cli.device)
        sim = sim_utils.SimulationContext(sim_cfg)
        # 메인 카메라 설정
        sim.set_camera_view([2.5, 2.5, 2.5], [0.0, 0.0, 0.0])
        scene_entities = design_scene(
            num_tiled_cams=args_cli.num_tiled_cameras,
            num_standard_cams=args_cli.num_standard_cameras,
            num_ray_caster_cams=args_cli.num_ray_caster_cameras,
            tiled_camera_data_types=args_cli.tiled_camera_data_types,
            standard_camera_data_types=args_cli.standard_camera_data_types,
            ray_caster_camera_data_types=args_cli.ray_caster_camera_data_types,
            height=args_cli.height,
            width=args_cli.width,
            num_objects=args_cli.num_objects,
            mesh_prim_paths=args_cli.ray_caster_visible_mesh_prim_paths,
        )
        # 시뮬레이터 실행
        sim.reset()
        # 이제 준비 완료!
        print("[INFO]: 설정 완료...")
        # 시뮬레이터 실행
        run_simulator(
            sim=sim,
            scene_entities=scene_entities,
            warm_start_length=args_cli.warm_start_length,
            experiment_length=args_cli.experiment_length,
            tiled_camera_data_types=args_cli.tiled_camera_data_types,
            standard_camera_data_types=args_cli.standard_camera_data_types,
            ray_caster_camera_data_types=args_cli.ray_caster_camera_data_types,
            convert_depth_to_camera_to_image_plane=args_cli.convert_depth_to_camera_to_image_plane,
        )
    else:
        print("[INFO]: 알려진 작업 환경 사용 중, 카메라 주입 중.")
        autotune_iter = 0
        max_sys_util_thresh = [0.0, 0.0, 0.0]
        max_num_cams = max(args_cli.num_tiled_cameras, args_cli.num_standard_cameras, args_cli.num_ray_caster_cameras)
        cur_num_cams = max_num_cams
        cur_sys_util = max_sys_util_thresh
        interval = args_cli.autotune_camera_count_interval

        if args_cli.autotune:
            max_sys_util_thresh = args_cli.autotune_max_percentage_util
            max_num_cams = args_cli.autotune_max_camera_count
            print("[INFO]: 다음 임계값 중 하나가 충족될 때까지 자동 튠 중")
            print(f"|CPU: {max_sys_util_thresh[0]}% | RAM {max_sys_util_thresh[1]}% | GPU: {max_sys_util_thresh[2]}% |")
            print(f"[INFO]: 허용되는 최대 카메라 수: {max_num_cams}")
        # 테스트 중인 카메라 유형 결정...
        tiled_camera_cfg = create_tiled_camera_cfg("tiled_camera")
        standard_camera_cfg = create_standard_camera_cfg("standard_camera")
        ray_caster_camera_cfg = create_ray_caster_camera_cfg("ray_caster_camera")
        camera_name_prefix = ""
        camera_creation_callable = None
        num_cams = 0
        if tiled_camera_cfg is not None:
            camera_name_prefix = "tiled_camera"
            camera_creation_callable = create_tiled_camera_cfg
            num_cams = args_cli.num_tiled_cameras
        elif standard_camera_cfg is not None:
            camera_name_prefix = "standard_camera"
            camera_creation_callable = create_standard_camera_cfg
            num_cams = args_cli.num_standard_cameras
        elif ray_caster_camera_cfg is not None:
            camera_name_prefix = "ray_caster_camera"
            camera_creation_callable = create_ray_caster_camera_cfg
            num_cams = args_cli.num_ray_caster_cameras

        while (
            all(cur <= max_thresh for cur, max_thresh in zip(cur_sys_util, max_sys_util_thresh))
            and cur_num_cams <= max_num_cams
        ):
            cur_num_cams = num_cams + interval * autotune_iter
            autotune_iter += 1

            env = inject_cameras_into_task(
                task=args_cli.task,
                num_cams=cur_num_cams,
                camera_name_prefix=camera_name_prefix,
                camera_creation_callable=camera_creation_callable,
                num_cameras_per_env=args_cli.task_num_cameras_per_env,
            )
            env.reset()
            print(f"Testing with {cur_num_cams} {camera_name_prefix}")
            analysis = run_simulator(
                sim=None,
                scene_entities=env.unwrapped.scene,
                warm_start_length=args_cli.warm_start_length,
                experiment_length=args_cli.experiment_length,
                tiled_camera_data_types=args_cli.tiled_camera_data_types,
                standard_camera_data_types=args_cli.standard_camera_data_types,
                ray_caster_camera_data_types=args_cli.ray_caster_camera_data_types,
                convert_depth_to_camera_to_image_plane=args_cli.convert_depth_to_camera_to_image_plane,
                max_cameras_per_env=args_cli.task_num_cameras_per_env,
                env=env,
            )

            cur_sys_util = analysis["system_utilization_analytics"]
            print("리셋 트리거 중...")
            env.close()
            sim_utils.create_new_stage()
        print("[INFO]: 완료! CTRL + C 를 눌러 종료하세요 ")
        print(f"[INFO]: 이 지점까지 왔다면 {cur_num_cams} {camera_name_prefix} 개까지 시뮬레이션할 수 있을 것입니다")
        print("이 시점에서는 GPU에서의 훈련이 실행되지 않는다는 점을 유념하세요.")
        print("훈련을 고려하여 사용률 임계값을 낮게 설정하세요.")

        if not args_cli.autotune:
            print("[WARNING]: GPU 사용 통계는 자동 튠 시에만 정확하며, 위에 표시된 값은 무시하세요.")


if __name__ == "__main__":
    # 메인 함수 실행
    main()
    # 시뮬레이터 앱 종료
    simulation_app.close()
```

## 가능한 파라미터

먼저 다음을 실행해 보세요:

```bash
./isaaclab.sh -p scripts/benchmarks/benchmark_cameras.py -h
```

이 명령어로 이 유틸리티에서 조정할 수 있는 모든 가능한 파라미터를 확인할 수 있습니다.

자동으로 최대 카메라 수를 결정하는 `autotune` 관련 명령줄 파라미터에 대한 자세한 정보는 아래를 참조하세요.

## 태스크 환경에서 성능 비교 및 자동으로 태스크의 최대 카메라 수 결정

현재 타일드 카메라가 여러 개의 동적 객체를 처리할 수 있는 가장 성능이 좋은 카메라입니다.

예를 들어, 카트폴 환경에서 100개의 타일드 카메라가 어떻게 동작하는지 보려면, 환경당 2대의 카메라(총 50개의 환경)를 사용하고 RGB 모드만으로 다음 명령을 실행하세요:

```bash
./isaaclab.sh -p scripts/benchmarks/benchmark_cameras.py \
--task Isaac-Cartpole-v0 --num_tiled_cameras 100 \
--task_num_cameras_per_env 2 \
--tiled_camera_data_types rgb
```

pynvml이 설치된 경우 (`./isaaclab.sh -p -m pip install pynvml`), 지정된 환경에서 특정 성능 임계값(최대 CPU 사용률 %, 최대 RAM 사용률 %, 최대 GPU 연산 사용률 %, 최대 GPU 메모리 사용률 %) 이하로 최대 카메라 수를 찾을 수도 있습니다. 예를 들어, 카트폴에서 실행할 수 있는 최대 카메라 수를 찾으려면 다음 명령을 실행하세요:

```bash
./isaaclab.sh -p scripts/benchmarks/benchmark_cameras.py \
--task Isaac-Cartpole-v0 --num_tiled_cameras 100 \
--task_num_cameras_per_env 2 \
--tiled_camera_data_types rgb --autotune \
--autotune_max_percentage_util 100 80 50 50
```

자동 튜닝은 프로그램이崩괴(크래시)될 수 있는데, 이는 한 번에 너무 많은 카메라를 실행하려고 시도했기 때문입니다. 그러나 최대 사용률 백분율 파라미터는 이를 방지하기 위해 설계되었습니다.

벤치마크 출력은 네트워크 훈련 오버헤드를 포함하지 않으므로, 이 오버헤드를 고려하여 최대 사용률 백분율을 낮추는 것이 좋습니다. 최종 출력 카메라 수는 모든 카메라에 대한 총합이므로, 환경당 카메라 수로 나누어 전체 환경 수를 구해야 합니다.

## 태스크 없이 카메라 유형 및 성능 비교

이 도구는 태스크 환경 없이도 성능을 평가할 수 있습니다. 예를 들어, 2대의 표준 카메라로 100개의 랜덤 객체를 보려면 다음 명령을 실행하세요:

```bash
./isaaclab.sh -p scripts/benchmarks/benchmark_cameras.py \
--height 100 --width 100 --num_standard_cameras 2 \
--standard_camera_data_types instance_segmentation_fast normals --num_objects 100 \
--experiment_length 100
```

시스템 성능으로 인해 이 설정을 처리할 수 없는 경우, 프로세스가 종료될 수 있습니다. 스크립트를 실행하는 동안 CPU/RAM 및 GPU 사용량을 모니터링하여 원하는 카메라를 렌더링하는 데 필요한 리소스의 양을 파악하는 것이 좋습니다. Ubuntu에서는 `htop`과 `nvtop`과 같은 도구를 사용하여 실시간으로 리소스를 모니터링할 수 있으며, Windows에서는 작업 관리자를 사용할 수 있습니다.

시스템이 원하는 카메라 수를 처리하기 어렵다면 다음 방법을 시도해 보세요:

> - 헤드리스 모드로 전환 (`--headless` 플래그 사용)
> - CPU가 아닌 GPU 파이프라인을 사용하고 있는지 확인
> - 타일드 카메라를 사용하지 않는다면 타일드 카메라로 전환
> - 카메라 해상도 감소
> - 각 카메라의 데이터 유형 수 감소
> - 카메라 수 감소
> - 장면 내 객체 수 감소

시스템이 원하는 카메라 수를 처리할 수 있다면, 터미널에 시간 통계가 출력됩니다. 시뮬레이션이 중지된 후에는 `CTRL+C`를 눌러 스크립트를 종료할 수 있습니다.
