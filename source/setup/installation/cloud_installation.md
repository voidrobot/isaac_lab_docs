# 클라우드 배포

Isaac Lab은 [Isaac Automator](https://github.com/isaac-sim/IsaacAutomator)를 사용하여 다양한 클라우드 인프라에서 실행할 수 있습니다.

Isaac Automator는 퍼블릭 클라우드(AWS, GCP, Azure, Alibaba Cloud가 현재 지원됨)에 Isaac Sim과 Isaac Lab을 빠르게 배포할 수 있도록 지원합니다. 그 결과, 몇 분 안에 개발 및 테스트에 사용할 수 있는 완전히 구성된 원격 데스크톱 클라우드 워크스테이션이 제공되며 비용 효율적입니다. Isaac Automator는 다양한 GPU 인스턴스와 스톱-스타트 기능을 지원하여 클라우드 비용을 절감하고 워크플로우를 지원하는 다양한 도구(데이터 업로드 및 다운로드, 자동 실행, 배포 관리 등)를 제공합니다.

## 시스템 요구 사항

Isaac Automator는 시스템에 `docker`가 사전 설치되어 있어야 합니다.

* Docker를 설치하려면 운영 체제별 지침을 따라 [Docker 웹사이트](https://docs.docker.com/desktop/install/linux-install/)의 안내를 따르세요. Isaac Automator와 함께 사용하려면 Docker Engine의 최소 버전 26.0.0과 Docker compose의 최소 버전 2.25.0이 필요합니다.
* [post-installation steps](https://docs.docker.com/engine/install/linux-postinstall/) 페이지의 Docker pós설치 단계를 따르세요. 이러한 단계를 통해 `sudo`를 사용하지 않고 Docker를 실행할 수 있습니다.

## Isaac Automator 설치

가장 최신かつ완전한 설치 지침については、[Isaac Automator](https://github.com/isaac-sim/IsaacAutomator?tab=readme-ov-file#installation) を参照してください。

Isaac Automator를 사용하려면 먼저 레포지토리를 클론하세요:

### HTTPS

```bash
git clone https://github.com/isaac-sim/IsaacAutomator.git
```

### SSH

```bash
git clone git@github.com:isaac-sim/IsaacAutomator.git
```

Isaac Automator는 NGC API 키를 얻어야 합니다.

* NVIDIA Developer Program 자격 증명에 가입하여 [Isaac Sim 컨테이너](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/isaac-sim)에 액세스 권한을 얻으세요.
* [NGC API 키](https://docs.nvidia.com/ngc/gpu-cloud/ngc-user-guide/index.html#generating-api-key)를 생성하여 NVIDIA GPU Cloud(NGC)의 잠긴 컨테이너 이미지에 액세스하세요.
  * 아직 NGC 계정이 없는 경우 이 단계에서 계정을 생성해야 합니다.
  * API 키를 생성한 후 터미널에서 NGC에 로그인해야 합니다.
    ```bash
    docker login nvcr.io
    ```
  * 사용자名으로 `$oauthtoken`을 정확히 입력하세요. 이는 NGC 인증에 사용되는 특별한 사용자名입니다.
    ```text
    Username: $oauthtoken
    Password: <Your NGC API Key>
    ```

## 컨테이너 빌드

Isaac Automator를 실행하려면 먼저 Isaac Automator 컨테이너를 빌드하세요:

### Linux

```bash
./build
```

### Windows

```batch
docker build --platform linux/x86_64 -t isa .
```

이는 Isaac Automator 컨테이너를 빌드하고 `isa`로 태그를 지정합니다.

## Automator 명령 실행

먼저 Automator 컨테이너에 진입하세요:

### Linux

```bash
./run
```

### Windows

```batch
docker run --platform linux/x86_64 -it --rm -v .:/app isa bash
```

다음으로 선호하는 클라우드에 대한 배포 스크립트를 실행하세요:

#### 참고
`--isaaclab` 플래그는 배포할 Isaac Lab의 버전을 지정하는 데 사용됩니다.
`v2.3.0` 태그는 Isaac Lab의 최신 릴리스입니다.

### AWS

```bash
./deploy-aws --isaaclab v2.3.2
```

### Azure

```bash
./deploy-azure --isaaclab v2.3.2
```

### GCP

```bash
./deploy-gcp --isaaclab v2.3.2
```

### Alibaba Cloud

```bash
./deploy-alicloud --isaaclab v2.3.2
```

환경 설정 및 자격 증명 관련 정보를 입력하라는 프롬프트를 따르세요. 성공하면 터미널에서 클라우드 인스턴스에 연결하는 방법에 대한 지침을 확인할 수 있습니다. 배포된 Isaac Sim 인스턴스는 다음 방법으로 액세스할 수 있습니다:

- SSH
- noVCN (브라우저 기반 VNC 클라이언트)
- NoMachine (원격 데스크톱 클라이언트)

배포 명령어 출력 끝에 연결 지침을 찾으세요. 또한 이 정보는 `state/<deployment-name>/info.txt` 파일에 저장됩니다.

각 클라우드에 필요한 자격 증명 및 설정에 대한 자세한 내용은 [Isaac Automator](https://github.com/isaac-sim/IsaacAutomator?tab=readme-ov-file#deploying-isaac-sim) 페이지를 방문하여 추가 지침을 확인하세요.

## 클라우드에서 Isaac Lab 실행

클라우드 인스턴스에 연결되면 데스크톱에 `isaaclab.sh` 아이콘이 표시됩니다. `isaaclab.sh` 실행 파일을 실행하면 새 터미널이 열립니다. 터미널 내에서 Isaac Lab 명령을 로컬에서 실행하는 것과 동일한 방식으로 실행할 수 있습니다.

예를 들어:

### Linux

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rl_games/train.py --task=Isaac-Cartpole-v0
```

### Windows

```batch
isaaclab.bat -p scripts/reinforcement_learning/rl_games/train.py --task=Isaac-Cartpole-v0
```

## 배포 삭제

비용을 절감하려면 사용하지 않는 배포를 삭제할 수 있습니다. 이는 Automator 컨테이너 내에서 수행할 수 있습니다.

이전 섹션에서 설명한 명령으로 Automator 컨테이너에 진입하세요:

### Linux

```bash
./run
```

### Windows

```batch
docker run --platform linux/x86_64 -it --rm -v .:/app isa bash
```

컨테이너 내에서 다음 명령으로 배포를 삭제할 수 있습니다:

```bash
./destroy <deployment-name>
```
