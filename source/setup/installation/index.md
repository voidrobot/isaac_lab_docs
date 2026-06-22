<a id="isaaclab-installation-root"></a>

# 로컬 설치

[![IsaacSim 5.1.0](https://img.shields.io/badge/IsaacSim-5.1.0-silver.svg)](https://developer.nvidia.com/isaac-sim)[![Python 3.11](https://img.shields.io/badge/python-3.11-blue.svg)](https://www.python.org/downloads/release/python-31013/)[![Ubuntu 22.04](https://img.shields.io/badge/platform-linux--64-orange.svg)](https://releases.ubuntu.com/22.04/)[![Windows 11](https://img.shields.io/badge/platform-windows--64-orange.svg)](https://www.microsoft.com/en-ca/windows/windows-11)

Isaac Lab 설치는 Windows와 Linux에서 이용 가능합니다. Isaac Sim을 기반으로 구축되었기 때문에,
Isaac Lab을 설치하기 전에 Isaac Sim을 먼저 설치해야 합니다. 이 가이드는 Isaac Sim과 Isaac Lab에 대한
권장 설치 방법을 설명합니다.

## 시스템 요구 사항

### 일반 요구 사항

자세한 요구 사항은
[Isaac Sim 시스템 요구 사항](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/requirements.html)을 참조하세요.
기본 요구 사항은 다음과 같습니다:

- **OS:** Ubuntu 22.04 (Linux x64) 또는 Windows 11 (x64)
- **RAM:** 32GB 이상
- **GPU VRAM:** 16GB 이상 (렌더링 워크플로우에 추가 VRAM이 필요할 수 있음)

**Isaac Sim은 특정 Python 버전으로 빌드되었기 때문에**,
Isaac Lab을 설치할 때 동일한 Python 버전을 사용하는 것이 필수적입니다.
필요한 Python 버전은 다음과 같습니다:

- Isaac Sim 5.X의 경우, 필요한 Python 버전은 3.11입니다.
- Isaac Sim 4.X의 경우, 필요한 Python 버전은 3.10입니다.

### 드라이버 요구 사항

[Omniverse 기술 요구 사항](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/common/technical-requirements.html)에 권장되지 않은 다른 드라이버는 작동할 수 있지만,
모든 Omniverse 테스트에 대해 검증되지 않았습니다.

- **최신 NVIDIA 생산 브랜치 드라이버**를 사용하세요.
- Linux에서는 버전 `580.65.06` 이상이 권장되며, 특히
  **Ubuntu 22.04.5와 커널 6.8.0-48-generic 이상**으로 업그레이드할 때 유용합니다.
- Spark에서는 버전 `580.95.05`가 권장됩니다.
- Windows에서는 버전 `580.88`이 권장됩니다.
- 새 GPU를 사용하거나 드라이버 문제가 발생하는 경우,
  [Unix Driver Archive](https://www.nvidia.com/en-us/drivers/unix/)에서 최신 생산 브랜치 드라이버를 설치하세요.
  `.run` 설치 프로그램을 사용하여 설치할 수 있습니다.

### DGX Spark: 세부 사항 및 제한 사항

DGX Spark는 aarch64 아키텍처를 가진 독립형 기계 학습 장치입니다.
결과적으로, Isaac Lab의 일부 기능은 현재 DGX Spark에서 지원되지 않습니다.
가장 주목할 만한 제한 사항은 아키텍처가 CUDA ≥ 13을 *필요로* 하므로,
cu13 빌드의 PyTorch 또는 그 이상이 필요하다는 점입니다.
Isaac Lab과 관련한 기타 주목할 만한 제한 사항은 다음과 같습니다…

1. [SkillGen](https://isaac-sim.github.io/IsaacLab/main/source/overview/imitation-learning/skillgen.html)은 기본적으로 지원되지 않습니다.
   이는 cuRobo가 특정 툴링과 라이브러리 버전이 필요한 네이티브 CUDA/C++ 확장을 빌드하기 때문이며,
   이 버전들은 DGX Spark와의 사용에 대해 검증되지 않았습니다.
2. [OpenXR](https://isaac-sim.github.io/IsaacLab/v2.3.2/source/api/lab/isaaclab.devices.html#openxr)과 같은 확장 현실 텔레포테이션 도구는 지원되지 않습니다.
   이는 아직 완전히 조사되지 않은 인코딩 성능 제한 때문입니다.
3. DGX Spark에서 Isaac Lab에서 [JAX](https://docs.jax.dev/en/latest/notebooks/thinking_in_jax.html)를 사용한 SKRL 훈련은 명시적으로 검증 또는 테스트되지 않았습니다.
   JAX는 Linux x86_64용으로만 사전 빌드된 CUDA 휠을 제공하므로,
   aarch64 시스템(예: DGX Spark)에서 기본적으로 CPU에서만 실행됩니다.
   GPU 지원은 JAX를 소스에서 빌드해야 하며, 이는 Isaac Lab에서 검증되지 않았습니다.
4. Livestream 및 Hub Workstation 캐시는 DGX Spark에서 지원되지 않습니다.
5. [Cosmos Transfer1 실행](../../overview/imitation-learning/augmented_imitation.md#running-cosmos)은 현재 DGX Spark에서 지원되지 않습니다.

### 문제 해결

Linux에서 설치 문제를 해결하려면
[Linux 문제 해결 가이드](https://docs.omniverse.nvidia.com/dev-guide/latest/linux-troubleshooting.html)를 참조하세요.

[Isaac Sim 호환성 체커](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_workstation.html#isaac-sim-compatibility-checker)를 사용하여
시스템에서 Isaac Sim을 실행하기 위한 위의 요구 사항이 충족되는지 자동으로 확인할 수 있습니다.

## 빠른 시작 (권장 방법)

대부분의 사용자에게 Isaac Lab을 설치하는 가장 간단하고 빠른 방법은
[Isaac Sim Pip 패키지를 사용한 설치](pip_installation.md)를 따르는 것입니다.

이 방법은 pip를 통해 Isaac Sim을 설치하고,
소스 코드를 통해 Isaac Lab을 설치합니다.
Isaac Lab에 익숙하지 않다면 여기서 시작하세요.

## 설치 방법 선택

다양한 워크플로우에는 다양한 설치 방법이 필요합니다.
다음 표를 참조하여 결정하세요:

| 방법                | Isaac Sim              | Isaac Lab       | 가장 적합한 경우                                   | 난이도     |
|---------------------|------------------------|-----------------|----------------------------------------------------|------------|
| **권장 방법**       | 📦 pip install         | 💾 source (git) | 초보자, 표준 사용                                   | 쉬움       |
| 바이너리 + 소스     | 📥 binary<br/>다운로드  | 💾 source (git) | Isaac Sim 바이너리 설치를 선호하는 사용자           | 쉬움       |
| 전체 소스 빌드      | 💾 source (git)        | 💾 source (git) | 두 가지 모두 수정하는 개발자                         | 고급       |
| Pip 전용            | 📦 pip install         | 📦 pip install  | 외부 확장만<br/>(훈련/예제 없음)                     | 특별<br/>케이스 |
| Docker              | 🐳 Docker              | 💾 source (git) | Docker 사용자                                       | 고급       |

## 다음 단계

설치 방법을 검토한 후, 워크플로에 맞는 가이드를 계속 진행하세요:

- 😃 [Isaac Sim Pip 패키지를 사용한 설치](pip_installation.md)
  - pip를 통해 Isaac Sim을 설치하고 소스에서 Isaac Lab을 설치합니다.
  - 초보자와 대부분의 사용자에게 적합합니다.
- [Isaac Sim 사전 빌드 바이너리를 사용한 설치](binaries_installation.md)
  - 웹사이트 다운로드를 통해 Isaac Sim의 바이너리 패키지를 설치합니다.
  - 소스 코드에서 Isaac Lab을 설치합니다.
  - Isaac Sim을 위해 pip를 사용하지 않기를 원하는 경우(예: Ubuntu 20.04에서) 이 방법을 선택하세요.
- [Isaac Sim 소스 코드를 사용한 설치](source_installation.md)
  - Isaac Sim을 소스에서 빌드합니다.
  - 소스 코드에서 Isaac Lab을 설치합니다.
  - Isaac Sim 자체를 수정할 계획인 경우에만 권장됩니다.
- [Isaac Lab Pip 패키지를 사용한 설치](isaaclab_pip_installation.md)
  - Isaac Sim과 Isaac Lab을 pip 패키지로 설치합니다.
  - 맞춤형 러너 스크립트로 **외부 확장**을 구축하는 고급 사용자에게 적합합니다.
  - 참고: 이 방법은 훈련 또는 예제 스크립트를 **포함하지 않습니다**.
- [컨테이너 배포](../../deployment/index.md#container-deployment)
  - Docker 컨테이너에서 Isaac Sim과 Isaac Lab을 설치합니다.
  - 컨테이너화된 환경에서 Isaac Lab을 사용하고자 하는 사용자에게 적합합니다.

## 에셋 캐싱

Isaac Lab 에셋은 **AWS S3 클라우드 스토리지**에 호스팅됩니다.
로딩 시간은 **네트워크 연결**과 **지리적 위치**에 따라 달라질 수 있으며,
일부 경우 에셋이 각 실행마다 몇 분 동안 로드될 수 있습니다.
성능을 향상시키거나 **오프라인 워크플로**를 지원하려면 **에셋 캐싱**을 활성화하는 것이 좋습니다.

- 캐시된 에셋은 로컬에 저장되어 반복 다운로드를 줄입니다.
- 느리거나 간헐적인 인터넷 연결이 있거나 배포 환경이 오프라인인 경우 특히 유용합니다.

에셋 캐싱을 활성화하고 워크플로를 가속화하려면
[에셋 캐싱](asset_caching.md)의 단계를 따라주세요.
