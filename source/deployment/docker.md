<a id="deployment-docker"></a>

# Docker 가이드

## 설정 지침

#### NOTE
다음 단계는 Isaac Sim 문서의 [컨테이너 설치](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_container.html)에서 가져온 것입니다.
완전성을 위해 여기 추가되었습니다.

### Docker 및 Docker Compose

우리는 Docker 엔진 버전 26.0.0 및 Docker Compose 버전 2.25.0으로 컨테이너를 테스트했습니다.
이 버전 이상을 사용하는 것을 권장합니다.

* Docker를 설치하려면 운영 체제에 대한 지침을 [Docker 웹사이트](https://docs.docker.com/desktop/install/linux-install/)에 따라 주세요.
* Docker Compose를 설치하려면 운영 체제에 대한 지침을 [Docker Compose](https://docs.docker.com/compose/install/linux/#install-using-the-repository) 페이지에 따라 주세요.
* Docker의 [사후 설치 단계](https://docs.docker.com/engine/install/linux-postinstall/) 페이지의 단계를 따르세요. 이 단계는 `sudo` 없이 Docker를 실행할 수 있게 해줍니다.
* GPU 가속 컨테이너를 빌드하고 실행하려면 [NVIDIA 컨테이너 툴킷](https://github.com/NVIDIA/nvidia-container-toolkit)도 설치해야 합니다.
  설치 단계는 [컨테이너 툴킷 웹사이트](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)를 참조하세요.

#### NOTE
[snap](https://snapcraft.io/docs/home-outside-home)의 제한 사항으로 인해, Docker를 사용할 때 Isaac Lab 디렉터리가 `/home` 디렉터리 트리 아래에 위치하도록 하세요.

## 디렉터리 구성

Isaac Lab 저장소의 루트에는 Docker 컨테이너 내에서 Isaac Lab을 실행하는 데 필요한 다양한 파일과 스크립트를 포함하는 `docker` 디렉터리가 있습니다. 이들 중 일부는 다음과 같이 요약할 수 있습니다:

* **Dockerfile.base**: Isaac Sim Docker 이미지에 Isaac Lab의 종속성을 오버레이하여 기본 Isaac Lab 이미지를 정의합니다.
  다른 것으로 끝나는 Dockerfile(예: `Dockerfile.ros2`)은 [이미지 확장](#isaac-lab-image-extensions)을 빌드합니다.
* **docker-compose.yaml**: 호스트 머신에서 실행되는 컨테이너에서 Isaac Lab 코드를 직접 편집할 수 있도록 마운트를 생성합니다.
  또한 Isaac Sim에서 자주 재사용되는 리소스(예: 셰이더)를 컴파일하여 저장하고, 로그, 데이터 및 문서를 보존하기 위해 `isaac-cache-kit`와 같은 여러 명명된 볼륨을 생성합니다.
* **.env.base**: `base` 빌드 프로세스 및 컨테이너 자체에 필요한 환경 변수를 저장합니다.
  다른 것으로 끝나는 `.env` 파일(예: `.env.ros2`)은 [이미지 확장](#isaac-lab-image-extensions)에 대한 정의를 제공합니다.
* **docker-compose.cloudxr-runtime.patch.yaml**: 호환 가능한 XR 기기로 스트리밍하기 위해 CloudXR Runtime 지원을 활성화하도록 적용되는 패치 파일입니다.
  CloudXR Runtime 및 기본 이미지를 위한 서비스와 볼륨을 정의합니다.
* **.env.cloudxr-runtime**: CloudXR Runtime 지원을 위한 환경 변수입니다.
* **container.py**: `utils`의 도구와 인터페이스하여 이미지를 구성하고 빌드하며, 컨테이너를 실행하고 상호 작용하는 유틸리티 스크립트입니다.

## 컨테이너 실행

#### NOTE
Docker 컨테이너는 빌드 시간에 저장소의 모든 파일을 `/workspace/isaaclab` 위치로 복사합니다.
즉, 컨테이너에서 파일에 가한 변경 사항은 이미지가 빌드된 후(즉, `./container.py start` 실행 후) 일반적으로 저장소에 반영되지 않습니다.

더 빠른 개발 주기를 위해 Isaac Lab 저장소의 다음 디렉터리를 컨테이너에 마운트하여 호스트 머신에서 파일을 편집할 수 있도록 합니다:

* **IsaacLab/source**: Isaac Lab 소스 코드가 포함된 디렉터리입니다.
* **IsaacLab/docs**: Isaac Lab 문서의 소스 코드가 포함된 디렉터리입니다. 빌드 아티팩트가 저장되는 `_build` 서브디렉터리를 제외하고 오버레이됩니다.

스크립트 `container.py`는 기본 `docker compose` 명령과 병행합니다. 각각은 [이미지 확장 인수](#isaac-lab-image-extensions)를 받아들일 수 있으며, 그렇지 않으면 기본적으로 `base` 이미지 확장으로 동작합니다. 이러한 명령은 다음과 같습니다:

* **build**: 지정된 프로파일의 이미지를 빌드합니다. 컨테이너는 시작하지 않습니다.
* **start**: 이미지를 빌드하고 디태치 모드(백그라운드)에서 컨테이너를 시작합니다.
* **enter**: 기존 Isaac Lab 컨테이너에서 새로운 bash 프로세스를 시작하며, 컨테이너를 내리지 않고 종료할 수 있습니다.
* **config**: `container.py start`에 제공된 입력으로부터 결과가 될 compose.yaml을 출력합니다.
  이 명령은 compose 구성을 디버그하는 데 유용합니다.
* **copy**: `isaac-lab-logs`, `isaac-lab-data` 및 `isaac-lab-docs` 볼륨에서 각각 `logs`, `data_storage` 및 `docs/_build` 아티팩트를 복사하여 `docker/artifacts` 디렉터리에 저장합니다.
  이러한 아티팩트는 도커 컨테이너 인스턴스 간에 지속되며 이미지 확장 간에 공유됩니다.
* **stop**: 컨테이너를 내리고 제거합니다.

다음은 디태치 상태로 컨테이너를 시작하고 들어가는 방법을 보여줍니다:

```bash
# 디태치 모드로 컨테이너 시작
# 이미지 확장 인수를 전달하지 않으므로 'base'로 기본 설정됨
./docker/container.py start

# .env 또는 .yaml 파일을 추가하여 compose 구성을 커스터마이징하려면,
# compose CLI와 동일한 방식으로 semplicemente 지정할 수 있습니다
# ./docker/container.py start --file my-compose.yaml --env-file .env.my-vars

# 컨테이너 입력
# 'base'를 명시적으로 전달하지만, 전달하지 않으면 기본적으로 'base'로 설정됨
./docker/container.py enter base
```

베이스 컨테이너에서 호스트 머신으로 파일을 복사하려면 다음 명령을 사용할 수 있습니다:

```bash
# /workspace/isaaclab/logs 파일을 현재 디렉터리로 복사
docker cp isaac-lab-base:/workspace/isaaclab/logs .
```

스크립트 `container.py`는 이 명령 주변에 래퍼를 제공하여 `logs`, `data_storage` 및 `docs/_build` 디렉터리를 `docker/artifacts` 디렉터리로 복사합니다.
이는 로그, 데이터 및 문서를 복사하는 데 유용합니다:

```bash
# 컨테이너 중지
./docker/container.py stop
```

### CloudXR Runtime 지원

호환 가능한 XR 기기로 스트리밍하기 위해 CloudXR Runtime을 활성화하려면 `docker-compose.cloudxr-runtime.patch.yaml` 패치 파일을 적용하여 CloudXR Runtime 컨테이너를 실행해야 합니다.
패치 파일은 CloudXR Runtime 및 기본 이미지를 위한 서비스와 볼륨을 정의합니다.
CloudXR Runtime에 필요한 환경 변수는 `.env.cloudxr-runtime` 파일에 지정됩니다.
기본 이미지와 함께 CloudXR Runtime 컨테이너를 시작하거나 중지하려면 다음 명령을 사용하세요:

```bash
# 기본 이미지와 함께 CloudXR Runtime 컨테이너 시작
./docker/container.py start --files docker-compose.cloudxr-runtime.patch.yaml --env-file .env.cloudxr-runtime

# CloudXR Runtime 컨테이너 및 기본 이미지 중지
./docker/container.py stop --files docker-compose.cloudxr-runtime.patch.yaml --env-file .env.cloudxr-runtime
```

### X11 포워딩

컨테이너는 X11 포워딩을 지원하여 컨테이너에서 GUI 애플리케이션을 실행하고 호스트 머신에 표시할 수 있습니다.

컨테이너가 처음으로 `./docker/container.py start`로 시작될 때, 스크립트는 사용자에게 X11 포워딩을 활성화할지 여부를 묻습니다.
이 선택은 향후 실행을 위해 `docker/.container.cfg` 파일에 저장됩니다.

선택을 변경하려면 `docker/.container.cfg` 파일에서 `X11_FORWARDING_ENABLED` 매개변수를 '0' 또는 '1'로 설정하여 X11 포워딩을 비활성화하거나 활성화할 수 있습니다.
그 후 `./docker/container.py start`를 실행하여 컨테이너를 다시 빌드해야 합니다. 이렇게 rebuilt 프로세스를 통해 변경 사항이 컨테이너에 적용됩니다.
그렇지 않으면 변경 사항이 적용되지 않습니다.

컨테이너가 시작된 후, X11 포워딩이 활성화된 상태로 컨테이너에 들어가서 GUI 애플리케이션을 실행할 수 있습니다.
표시가 호스트 머신으로 포워딩됩니다.

### Python 인터프리터

컨테이너는 Isaac Sim에서 제공하는 Python 인터프리터를 사용합니다.
이 인터프리터는 `/isaac-sim/python.sh`에 위치합니다.
컨테이너 내부에서 별칭을 설정하여 Python 인터프리터를 더 쉽게 실행할 수 있습니다.
다음 명령을 사용하여 Python 인터프리터를 실행할 수 있습니다:

```bash
# Python 인터프리터 실행 -> /isaac-sim/python.sh를 가리킴
python
```

### 마운트된 볼륨 이해

`docker-compose.yaml` 파일은 컨테이너에 마운트되는 여러 명명된 볼륨을 생성합니다.
다음은 요약한 내용입니다:

| 볼륨 이름         | 설명                                                                | 컨테이너 경로                    |
|-------------------|--------------------------------------------------------------------|----------------------------------|
| isaac-cache-kit   | 캐시된 Kit 리소스 저장                                               | /isaac-sim/kit/cache             |
| isaac-cache-ov    | 캐시된 OV 리소스 저장                                                | /root/.cache/ov                  |
| isaac-cache-pip   | 캐시된 pip 리소스 저장                                               | /root/.cache/pip                 |
| isaac-cache-gl    | 캐시된 GLCache 리소스 저장                                           | /root/.cache/nvidia/GLCache      |
| isaac-cache-compute | 캐시된 컴퓨트 리소스 저장                                            | /root/.nv/ComputeCache           |
| isaac-logs        | Omniverse에서 생성된 로그 저장                                        | /root/.nvidia-omniverse/logs     |
| isaac-carb-logs   | carb에서 생성된 로그 저장                                             | /isaac-sim/kit/logs/Kit/Isaac-Sim |
| isaac-data        | Omniverse에서 생성된 데이터 저장                                      | /root/.local/share/ov/data       |
| isaac-docs        | Omniverse에서 생성된 문서 저장                                        | /root/Documents                  |
| isaac-lab-docs      | 컨테이너 내부에서 빌드된 Isaac Lab 문서를 저장합니다.                       | /workspace/isaaclab/docs/_build   |
| isaac-lab-logs      | 컨테이너 내부에서 실행된 Isaac Lab 워크플로우에서 생성된 로그를 저장합니다. | /workspace/isaaclab/logs          |
| isaac-lab-data      | 컨테이너 실행 간 사용자가 보존하고자 하는 모든 데이터를 저장합니다.         | /workspace/isaaclab/data_storage  |

이 볼륨의 내용을 보려면 다음 명령을 사용할 수 있습니다:

```bash
# 모든 볼륨 목록 표시
docker volume ls
# 특정 볼륨 검사, 예: isaac-cache-kit
docker volume inspect isaac-cache-kit
```

## Isaac Lab 이미지 확장

생성된 이미지는 `container.py start` 및 `container.py stop`에 전달된 인수에 따라 달라집니다.
이러한 명령은 추가 인수로 이미지 확장 매개변수를 받습니다. 인수를 전달하지 않으면 이 매개변수의 기본값은 `base`입니다. 현재 유효한 값은 (`base`, `ros2`)뿐입니다. 한 번에 하나의 이미지 확장만 전달할 수 있습니다.
생성된 이미지와 컨테이너는 `isaac-lab-${profile}`이라는 이름을 가집니다. 여기서 `${profile}`은 이미지 확장 이름입니다.

`suffix`는 `container.py`의 선택적 문자열 인수로, 개발 목적에 유용할 수 있는 도커 이미지와 컨테이너 이름 접미사를 지정합니다. 기본적으로 `${suffix}`는 빈 문자열입니다. `${suffix}`가 비어 있지 않은 문자열이면, 생성된 도커 이미지와 컨테이너는 `isaac-lab-${profile}-${suffix}`이라는 이름을 가집니다. 여기서 `${profile}`과 `${suffix}` 사이에 하이픈이 삽입됩니다. `suffix`는 클러스터 배포와 함께 사용해서는 안 됩니다.

```bash
# 기본적으로 base 시작, 이름은 isaac-lab-base
./docker/container.py start
# base 명시적으로 중지, 이름은 isaac-lab-base
./docker/container.py stop base
# ros2 컨테이너 시작, 이름은 isaac-lab-ros2
./docker/container.py start ros2
# ros2 컨테이너 중지, 이름은 isaac-lab-ros2
./docker/container.py stop ros2

# base 컨테이너 시작, 이름은 isaac-lab-base-custom
./docker/container.py start base --suffix custom
# base 컨테이너 중지, 이름은 isaac-lab-base-custom
./docker/container.py stop base --suffix custom
# ros2 컨테이너 시작, 이름은 isaac-lab-ros2-custom
./docker/container.py start ros2 --suffix custom
# ros2 컨테이너 중지, 이름은 isaac-lab-ros2-custom
./docker/container.py stop ros2 --suffix custom
```

전달된 이미지 확장 인수는 `Dockerfile.${image_extension}`에 정의된 이미지를 빌드하고, `docker-compose.yaml`의 해당 [프로파일](https://docs.docker.com/compose/compose-file/15-profiles/) 및 `.env.base`에 추가된 `.env.${image_extension}`의 환경 변수를 사용합니다.

### ROS2 이미지 확장

`Dockerfile.ros2`에서 컨테이너는 [apt 패키지](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html#install-ros-2-packages)를 통해 ROS2 Humble을 설치하고,これを `.bashrc`에서 소싱합니다.
정확한 버전은 `.env.ros2` 파일의 변수 `ROS_APT_PACKAGE`에 지정되어 있으며, 기본값은 `ros-base`입니다. 다른 관련 ROS2 변수도 `.env.ros2` 파일에 지정되어 있으며, 여기에는 [다양한 미들웨어](https://docs.ros.org/en/humble/How-To-Guides/Working-with-multiple-RMW-implementations.html) 옵션을 정의하는 변수도 포함됩니다.

컨테이너는 기본적으로 `FastRTPS`를 사용하지만, `CycloneDDS`도 지원됩니다. 이러한 미들웨어 각각은 `docker/.ros` 하위의 해당 `.xml` 파일을 사용하여 [튜닝](https://docs.ros.org/en/foxy/How-To-Guides/DDS-tuning.html)할 수 있습니다.

### ROS2 이미지 확장을 위한 매개변수

```bash
###
# ROS2 특정 설정
###
# 설치할 ROS2 apt 패키지 버전 설정 (ros-base, desktop, desktop-full)
ROS2_APT_PACKAGE=desktop
# 사용할 ROS2 미들웨어 구현 설정 (예: rmw_fastrtps_cpp, rmw_cyclonedds_cpp)
RMW_IMPLEMENTATION=rmw_fastrtps_cpp
# 사용할 fastdds.xml 파일 경로 (fastdds 사용 시에만 필요)
FASTRTPS_DEFAULT_PROFILES_FILE=${DOCKER_USER_HOME}/.ros/fastdds.xml
# 사용할 cyclonedds.xml 파일 경로 (cyclonedds 사용 시에만 필요)
CYCLONEDDS_URI=${DOCKER_USER_HOME}/.ros/cyclonedds.xml
# 도커 이미지와 컨테이너 이름 접미사 (기본값 "", container_interface.py 스크립트에 의해 설정됨)
# 예: "-custom"
DOCKER_NAME_SUFFIX=""
```

## 사전 빌드된 Isaac Lab 컨테이너 실행

Isaac Lab 2.0 릴리스에서 최소한의 사전 빌드 컨테이너를 도입했습니다. 이 컨테이너에는 Isaac Sim과 Omniverse의 매우 최소한의 의존성뿐 아니라 Isaac Lab 2.0이 사전 빌드되어 포함되어 있습니다.
이 컨테이너를 사용하면 로컬 도커 이미지 빌드 없이 NGC에서 컨테이너를 직접 풀할 수 있습니다. Isaac Lab 소스 코드는 이 컨테이너 내에서 `/workspace/IsaacLab` 경로에서 사용할 수 있습니다.

이 컨테이너는 **헤드리스** 전용으로 설계되었으며, X11 포워딩 또는 GUI 실행은 허용되지 않습니다. 헤드리스 트레이닝에만 이 컨테이너를 사용하세요. 다른 사용 사례의 경우, 위의 단계를 따라 직접 Isaac Lab 도커 이미지를 빌드하는 것을 권장합니다.

#### 참고
현재 우리는 Isaac Lab의 주요 릴리스마다 도커 이미지만 제공합니다.
예를 들어, 2.0.0 및 2.1.0 릴리스용 도커 이미지는 제공하지만 2.0.2용은 제공하지 않습니다.
앞으로는 Isaac Lab의 마이너 릴리스마다 도커 이미지를 제공할 예정입니다.

최소한의 Isaac Lab 컨테이너를 풀하려면 다음 명령을 실행하세요:

```bash
docker pull nvcr.io/nvidia/isaac-lab:2.3.2
```

대화형 bash 세션으로 Isaac Lab 컨테이너를 실행하려면 다음 명령을 실행하세요:

```bash
docker run --name isaac-lab --entrypoint bash -it --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
   -e "PRIVACY_CONSENT=Y" \
   -v ~/docker/isaac-sim/cache/kit:/isaac-sim/kit/cache:rw \
   -v ~/docker/isaac-sim/cache/ov:/root/.cache/ov:rw \
   -v ~/docker/isaac-sim/cache/pip:/root/.cache/pip:rw \
   -v ~/docker/isaac-sim/cache/glcache:/root/.cache/nvidia/GLCache:rw \
   -v ~/docker/isaac-sim/cache/computecache:/root/.nv/ComputeCache:rw \
   -v ~/docker/isaac-sim/logs:/root/.nvidia-omniverse/logs:rw \
   -v ~/docker/isaac-sim/data:/root/.local/share/ov/data:rw \
   -v ~/docker/isaac-sim/documents:/root/Documents:rw \
   nvcr.io/nvidia/isaac-lab:2.3.2
```

X11 포워딩을 통해 렌더링을 활성화하려면 다음 명령을 실행하세요:

```bash
xhost +
docker run --name isaac-lab --entrypoint bash -it --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
   -e "PRIVACY_CONSENT=Y" \
   -e DISPLAY \
   -v $HOME/.Xauthority:/root/.Xauthority \
   -v ~/docker/isaac-sim/cache/kit:/isaac-sim/kit/cache:rw \
   -v ~/docker/isaac-sim/cache/ov:/root/.cache/ov:rw \
   -v ~/docker/isaac-sim/cache/pip:/root/.cache/pip:rw \
   -v ~/docker/isaac-sim/cache/glcache:/root/.cache/nvidia/GLCache:rw \
   -v ~/docker/isaac-sim/cache/computecache:/root/.nv/ComputeCache:rw \
   -v ~/docker/isaac-sim/logs:/root/.nvidia-omniverse/logs:rw \
   -v ~/docker/isaac-sim/data:/root/.local/share/ov/data:rw \
   -v ~/docker/isaac-sim/documents:/root/Documents:rw \
   nvcr.io/nvidia/isaac-lab:2.3.2
```

컨테이너 내에서 예를 실행하려면 다음 명령을 실행하세요:

```bash
./isaaclab.sh -p scripts/tutorials/00_sim/log_time.py --headless
```
