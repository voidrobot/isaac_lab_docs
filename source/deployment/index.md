<a id="container-deployment"></a>

# 컨테이너 배포

Docker는 컨테이너를 생성할 수 있는 도구로, 이는 애플리케이션을 실행할 수 있는 고립된 환경을 제공합니다. Docker를 설치한 모든 머신에서 호스트 머신의 운영 체제나 설치된 라이브러리와 무관하게 애플리케이션을 실행할 수 있도록 보장하는 데 유용합니다.

Isaac Lab 및 모든 의존성을 포함하는 Docker 이미지를 빌드하는 데 사용할 수 있는 Dockerfile과 docker-compose.yaml 파일을 포함하고 있습니다. 이 이미지를 통해 컨테이너 내에서 Isaac Lab을 실행할 수 있습니다. Dockerfile은 NVIDIA에서 제공하는 Isaac Sim 이미지를 기반으로 하며, 이 이미지는 Omniverse 애플리케이션 런처와 Isaac Sim 애플리케이션을 포함합니다. Dockerfile은 이 이미지 위에 Isaac Lab과 그 의존성을 설치합니다.

## 리포지토리 클론하기

컨테이너를 빌드하기 전에 Isaac Lab 리포지토리를 클론하세요(이미 하지 않았다면):

### SSH

```bash
git clone git@github.com:isaac-sim/IsaacLab.git
```

### HTTPS

```bash
git clone https://github.com:isaac-sim/IsaacLab.git
```

## 다음 단계

클론한 후, 필요에 맞는 배포 워크플로를 선택할 수 있습니다:

- [Docker 가이드](docker.md)
  - Docker 컨테이너에서 Isaac Lab을 빌드, 구성 및 실행하는 방법을 학습합니다.
  - 저장소의 `docker/` 설정, `container.py` 헬퍼 스크립트, 마운트된 볼륨, 이미지 확장(ROS 2 등), 선택적 CloudXR 스트리밍 지원을 설명합니다.
  - NVIDIA NGC의 사전 빌드된 Isaac Lab 컨테이너를 헤드리스 트레이닝에 사용하는 방법을 다룹니다.
- [Docker로 예제 실행하기](run_docker_example.md)
  - Isaac Lab Docker 컨테이너 내에서의 개발 워크플로를 실행하는 방법을 학습합니다.
  - 컨테이너 빌드, 진입, 샘플 Python 스크립트(log_time.py) 실행 및 마운트된 볼륨을 사용한 로그 검색 과정을 보여줍니다.
  - 실시간 코드 수정을 위한 바인드 마운트 디렉터리를 강조하고, 이미지와 아티팩트를 유지한 채로 컨테이너를 중지하거나 제거하는 방법을 설명합니다.
- [클러스터 가이드](cluster.md)
  - 고성능 컴퓨팅(HPC) 클러스터에서 Isaac Lab을 실행하는 방법을 학습합니다.
  - Docker 이미지를 Singularity(Apptainer) 이미지로 내보내는 방법, 클러스터별 파라미터 구성, SLURM 또는 PBS와 같은 일반적인 워크로드 관리자를 사용한 작업 제출 방법을 설명합니다.
  - ETH 취리히의 Euler 클러스터 및 제노바의 IIT 프랭클린 클러스터에서 테스트된 워크플로를 포함하며, 다른 환경에 적응하는 방법에 대한 메모도 제공합니다.
- [Kubernetes에서의 CloudXR 텔레포케이션 배포](cloudxr_teleoperation_cluster.md)
  - Kubernetes 클러스터에서 Isaac Lab용 CloudXR 텔레포케이션을 배포합니다.
  - 시스템 요구사항, 소프트웨어 의존성, RBAC 권한을 포함한 준비 단계를 다룹니다.
  - Helm 차트 설치 및 검증, pod 실행 및 제거 방법을 보여줍니다.
