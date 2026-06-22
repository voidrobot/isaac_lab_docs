# Docker를 사용한 예시 실행

Isaac Lab 리포지토리의 루트에서 `docker` 디렉터리에는 모든 Docker 관련 파일이 포함되어 있습니다. 여기에는 Docker에서 사용되는 세 개의 파일(**Dockerfile**, **docker-compose.yaml**, **.env**)과 이를 인터페이스하는 추가 스크립트 **container.py**가 포함됩니다.

이 튜토리얼에서는 Isaac Lab Docker 컨테이너를 개발에 사용하는 방법을 배웁니다. 설치 및 Isaac Sim 이미지 접근 방법을 포함한 Docker 설정의 상세한 설명은 [Docker 가이드](docker.md#deployment-docker)를 참조하세요. Docker에 대한 일반적인 설명은 [공식 문서](https://docs.docker.com/get-started/overview/)를 참조하세요.

## 컨테이너 빌드

Isaac Lab 리포지토리의 루트에서 Isaac Lab 컨테이너를 빌드하려면 다음 명령을 실행합니다.

```console
python docker/container.py start
```

터미널은 먼저 기본 IsaacSim 이미지를 가져온 다음, 그 위에 Isaac Lab 이미지의 추가 레이어를 빌드하고 Isaac Lab 컨테이너를 실행합니다. 첫 번째 빌드에는 몇 분이 소요되지만, 이후 실행에서는 Docker의 캐싱으로 인해 반복 작업이 방지되어 시간이 더 짧아집니다. 터미널에서 `docker container ls` 명령을 실행하면 시스템에서 실행 중인 컨테이너 목록이 표시됩니다. 모든 것이 올바르게 설정되었다면 `NAME`이 **isaac-lab-base**인 컨테이너가 아래와 유사하게 표시됩니다.

```console
CONTAINER ID   IMAGE               COMMAND   CREATED           STATUS         PORTS     NAMES
483d1d5e2def   isaac-lab-base      "bash"    30 seconds ago   Up 30 seconds             isaac-lab-base
```

컨테이너가 실행 중이면 터미널에서 컨테이너에 진입할 수 있습니다.

```console
python docker/container.py enter
```

Isaac Lab 컨테이너에 진입하면 `root` 슈퍼유저의 터미널에 있게 됩니다. 이 환경에는 Isaac Lab 리포지토리의 복사본이 포함되어 있을 뿐만 아니라 Isaac Sim의 디렉터리와 라이브러리에 대한 액세스도 제공됩니다. `root`의 **.bashrc**에 넣은 몇 가지 편리한 별칭을 사용하여 이 환경에서 실험을 실행할 수 있습니다. 예를 들어, 별칭 `isaaclab`을 입력하여 **isaaclab.sh** 스크립트를 어디서든 사용할 수 있습니다.

또한 컨테이너에서는 [바인드 마운트](https://docs.docker.com/storage/bind-mounts/)를 통해 호스트 머신의 `IsaacLab/source` 디렉터리를 마운트했습니다. 즉, 호스트 머신의 편집기를 사용하여 이 디렉터리 아래의 파일을 수정하면 Docker 이미지를 다시 빌드하지 않아도 변경 사항이 컨테이너 내에서 즉시 반영됩니다.

이제 컨테이너 내에서 샘플 스크립트를 실행하여 Isaac Lab Docker 컨테이너에서 아티팩트를 추출하는 방법을 시연해 보겠습니다.

## 코드

이 튜토리얼은 `IsaacLab/scripts/tutorials/00_sim` 디렉터리의 `log_time.py` 스크립트에 해당합니다.

### log_time.py 코드

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 시뮬레이션이 실행되는 동안 로그 출력을 생성하는 방법을 보여줍니다.
이 스크립트는 도커 사용법 튜토리얼과 함께 제공됩니다.

.. code-block:: bash

    # Usage
    ./isaaclab.sh -p scripts/tutorials/00_sim/log_time.py

"""

"""Isaac Sim 시뮬레이터 먼저 실행하기."""


import argparse
import os

from isaaclab.app import AppLauncher

# 인수 파서 생성
parser = argparse.ArgumentParser(description="도커 컨테이너 내에서 로그 생성 튜토리얼.")
# AppLauncher CLI 인수 추가
AppLauncher.add_app_launcher_args(parser)
# 인수 파싱
args_cli = parser.parse_args()
# 옴니버스 앱 실행
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app

"""나머지 부분 계속하기."""

from isaaclab.sim import SimulationCfg, SimulationContext


def main():
    """메인 함수."""
    # 로그가 logs/docker_tutorial에 저장되도록 지정
    log_dir_path = os.path.join("logs")
    if not os.path.isdir(log_dir_path):
        os.mkdir(log_dir_path)
    # 컨테이너에서 절대 경로는
    # /workspace/isaaclab/logs/docker_tutorial이 되며, 이는
    # 모든 파이썬 실행이 /workspace/isaaclab/isaaclab.sh를 통해 이루어지고
    # 호출 프로세스의 경로가 /workspace/isaaclab이기 때문입니다.
    log_dir_path = os.path.abspath(os.path.join(log_dir_path, "docker_tutorial"))
    if not os.path.isdir(log_dir_path):
        os.mkdir(log_dir_path)
    print(f"[INFO] Logging experiment to directory: {log_dir_path}")

    # 시뮬레이션 컨텍스트 초기화
    sim_cfg = SimulationCfg(dt=0.01)
    sim = SimulationContext(sim_cfg)
    # 메인 카메라 설정
    sim.set_camera_view([2.5, 2.5, 2.5], [0.0, 0.0, 0.0])

    # 시뮬레이터 실행
    sim.reset()
    # 이제 준비 완료!
    print("[INFO]: Setup complete...")

    # sim_time 계산을 위한 준비
    sim_dt = sim.get_physics_dt()
    sim_time = 0.0

    # 로그 파일 열기
    with open(os.path.join(log_dir_path, "log.txt"), "w") as log_file:
        # 물리 시뮬레이션 실행
        while simulation_app.is_running():
            log_file.write(f"{sim_time}" + "\n")
            # 단계 수행
            sim.step()
            sim_time += sim_dt


if __name__ == "__main__":
    # 메인 함수 실행
    main()
    # 시뮬레이터 앱 종료
    simulation_app.close()
```

## 코드 설명

Isaac Lab Docker 컨테이너에는 호스트 컴퓨터와 컨테이너 간에 영구 저장을 용이하게 하는 여러 [볼륨](https://docs.docker.com/storage/volumes/)이 있습니다. 그 중 하나가 `/workspace/isaaclab/logs` 디렉터리입니다. `log_time.py` 스크립트는 이 디렉터리를 `log.txt`가 작성되어야 하는 위치로 지정합니다:

```python
    # 로그가 logs/docker_tutorial에 저장되도록 지정
    log_dir_path = os.path.join("logs")
    if not os.path.isdir(log_dir_path):
        os.mkdir(log_dir_path)
    # 컨테이너에서 절대 경로는
    # /workspace/isaaclab/logs/docker_tutorial이 되며, 이는
    # 모든 파이썬 실행이 /workspace/isaaclab/isaaclab.sh를 통해 이루어지고
    # 호출 프로세스의 경로가 /workspace/isaaclab이기 때문입니다.
    log_dir_path = os.path.abspath(os.path.join(log_dir_path, "docker_tutorial"))
    if not os.path.isdir(log_dir_path):
        os.mkdir(log_dir_path)
    print(f"[INFO] Logging experiment to directory: {log_dir_path}")
```

주석에서 언급한 것처럼 [`os.path.abspath()`](https://docs.python.org/3/library/os.path.html#os.path.abspath)는 도커 컨테이너에서 모든 파이썬 실행이 `/workspace/isaaclab/isaaclab.sh`를 통해 이루어지기 때문에 `/workspace/isaaclab`을 앞에 붙입니다. 출력 결과는 매 시뮬레이션 단계마다 새 줄에 `sim_time`이 기록된 `log.txt` 파일이 됩니다:

```python
    # sim_time 계산을 위한 준비
    sim_dt = sim.get_physics_dt()
    sim_time = 0.0

    # 로그 파일 열기
    with open(os.path.join(log_dir_path, "log.txt"), "w") as log_file:
        # 물리 시뮬레이션 실행
        while simulation_app.is_running():
            log_file.write(f"{sim_time}" + "\n")
            # 단계 수행
            sim.step()
            sim_time += sim_dt
```

## 스크립트 실행

스크립트를 실행하여 로그를 생성하고, GUI를 방지하기 위해 실행에 `--headless` 플래그를 추가합니다:

```bash
isaaclab -p scripts/tutorials/00_sim/log_time.py --headless
```

이제 `log.txt`가 `/workspace/isaaclab/logs/docker_tutorial`에 생성되었습니다. 컨테이너에서 나가기 위해 `exit`를 입력하면 호스트 터미널 환경에서 `IsaacLab/docker`로 돌아갑니다. 그런 다음 다음 명령을 실행하여 도커 컨테이너에서 로그를 검색하고 호스트 머신에 배치할 수 있습니다:

```console
./container.py copy
```

컨테이너에서 검색한 아티팩트를 보고하는 터미널 출력이 표시됩니다. `/isaaclab/docker/artifacts/logs/docker_tutorial`로 이동하면 위의 스크립트에 의해 생성된 `log.txt` 파일의 사본을 볼 수 있습니다.

`artifacts` 아래의 각 디렉터리는 컨테이너 내의 디렉터리에 매핑된 Docker [볼륨](https://docs.docker.com/storage/volumes/)에 해당하며, `container.py copy` 명령은 이러한 [볼륨](https://docs.docker.com/storage/volumes/)에서 이러한 디렉터리로 데이터를 복사합니다.

`container.py enter`를 다시 실행하여 Isaac Lab Docker 터미널 환경으로 돌아갈 수 있지만, 로그를 검색하여 검사하고 싶습니다. 다음과 같은 명령을 실행하여 Isaac Lab Docker 컨테이너를 중지할 수 있습니다:

```console
./container.py stop
```

이로 인해 Docker Isaac Lab 컨테이너가 중지됩니다. 이미지는 지속되어 추가 사용이 가능하며, 볼륨의 내용도 그대로 유지됩니다. 이미지가 차지하는 디스크 공간(약 20.1GB)을 확보하고, 다음에 `./container.py start`를 실행할 때 빌드 프로세스를 반복하는 것이 괜찮다면 다음 명령을 입력하여 **isaac-lab-base** 이미지를 삭제할 수 있습니다:

```console
docker image rm isaac-lab-base
```

이후에 `docker image ls`를 실행하면 **isaac-lab-base** 태그가 붙은 이미지가 더 이상 존재하지 않는 것을 확인할 수 있습니다. 더 많은 공간을 확보하기 위해 기본 NVIDIA 컨테이너에 대해서도 동일한 프로세스를 반복할 수 있습니다. 도커에서 리소스를 더 강력하게 자유롭게 하고 싶다면 [docker prune](https://docs.docker.com/config/pruning/) 명령의 문서를 참조하세요.
