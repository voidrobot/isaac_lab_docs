<a id="haply-teleoperation"></a>

# Haply 원격조작 설정하기

[Haply Devices](https://haply.co/)는 방향성 힘 피드백을 제공하는 직관적인 로봇 원격조작을 가능하게 하는 햅틱 기기들을 제공합니다. Haply Inverse3가 VerseGrip과 결합될 때 힘 피드백 기능을 갖춘 엔드이펙터 제어 시스템이 생성됩니다.

Isaac Lab은 Haply 기기들을 지원하여 공간 제어와 햅틱 피드백이 필요한 원격조작 워크플로우를 제공합니다. 이를 통해 운영자는 조작 작업 중 접촉 힘을 느낄 수 있어 제어 품질과 작업 성능을 향상시킬 수 있습니다.

이 가이드는 Isaac Lab을 활용한 Haply 기기 설정 및 사용 방법을 설명합니다.

## 개요

Isaac Lab에서 Haply를 사용하는 데는 다음과 같은 구성 요소들이 포함됩니다:

* **Isaac Lab**은 로봇 환경을 시뮬레이션하고 운영자에게 접촉 힘을 다시 스트리밍합니다
* **Haply Inverse3**은 운영자 작업 공간에서 3자유도 위치 추적과 힘 피드백을 제공합니다
* **Haply VerseGrip**은 그립 제어를 위한 방향 감지와 버튼 입력을 추가합니다
* **Haply SDK**는 Isaac Lab과 Haply 하드웨어 간 WebSocket 통신을 관리합니다

이 가이드는 다음 단계들을 안내합니다:

* [시스템 요구사항](#haply-system-requirements)
* [설치](#haply-installation)
* [기기 설정](#haply-device-setup)
* [데모 실행](#haply-running-demo)
* [문제 해결](#haply-troubleshooting)

<a id="haply-system-requirements"></a>

## 시스템 요구사항

### 하드웨어 요구사항

* **Isaac Lab 워크스테이션**
  * Ubuntu 22.04 또는 Ubuntu 24.04
  * 200Hz 물리 시뮬레이션을 위한 하드웨어 요구사항:
    * CPU: 8코어 Intel Core i7 또는 AMD Ryzen 7 (이상)
    * 메모리: 32GB RAM (64GB 권장)
    * GPU: RTX 3090 이상
  * 네트워크: Haply 기기와의 WebSocket 통신을 위한 동일한 로컬 네트워크
* **Haply 기기**
  * Haply Inverse3 - 위치 추적과 힘 피드백을 위한 햅틱 기기
  * Haply VerseGrip - 방향 및 버튼 입력을 위한 무선 컨트롤러
  * 두 기기 모두 켜져 있고 Haply SDK에 연결되어 있어야 합니다

### 소프트웨어 요구사항

* Isaac Lab ([설치 가이드](../setup/installation/index.md#isaaclab-installation-root) 참고)
* Haply SDK (Haply Robotics에서 제공)
* Python 3.10+
* `websockets` Python 패키지 (Isaac Lab과 함께 자동으로 설치됨)

<a id="haply-installation"></a>

## 설치

### 1. Isaac Lab 설치

Isaac Lab [설치 가이드](../setup/installation/index.md#isaaclab-installation-root)를 따라 환경을 설정하세요.

`websockets` 의존성은 Isaac Lab의 요구사항에 자동으로 포함됩니다.

### 2. Haply SDK 설치

[Haply Devices](https://haply.co/) 웹사이트에서 Haply SDK를 다운로드하세요.
SDK 소프트웨어를 설치하고 기기를 구성하세요.

### 3. 설치 확인

Haply 기기 관리자가 Haply 기기를 감지하는지 테스트하세요.
Inverse3와 VerseGrip이 모두 연결된 상태여야 합니다.

<a id="haply-device-setup"></a>

## 기기 설정

### 1. 물리적 설정

* Haply Inverse3를 안정적인 표면에 배치하세요
* VerseGrip이 충전되어 있고 페어링되었는지 확인하세요
* Inverse3 작업 공간에 도달할 수 있도록 편안한 자세를 취하세요
* 작업 공간에 장애물이 없도록 유지하세요

### 2. Haply SDK 시작

Haply 문서에 따라 Haply SDK를 실행하세요. SDK는 일반적으로 다음을 수행합니다:

* `localhost:10001`에서 WebSocket 서버 실행
* 기기 데이터를 200Hz로 스트리밍
* 두 기기의 연결 상태 표시

### 3. 통신 테스트

다음 Python 스크립트를 사용하여 WebSocket 연결을 테스트할 수 있습니다:

```python
import asyncio
import websockets
import json

async def test_haply():
    uri = "ws://localhost:10001"
    async with websockets.connect(uri) as ws:
        response = await ws.recv()
        data = json.loads(response)
        print("Inverse3:", data.get("inverse3", []))
        print("VerseGrip:", data.get("wireless_verse_grip", []))

asyncio.run(test_haply())
```

Inverse3와 VerseGrip 모두에서 기기 데이터가 스트리밍되는 것을 볼 수 있어야 합니다.

<a id="haply-running-demo"></a>

## 데모 실행

Haply 원격조작 데모는 힘 피드백을 사용한 로봇 조작을 Franka Panda 팔을 이용해 시연합니다.

### 기본 사용법

### Linux

```bash
# Haply SDK가 실행 중인지 확인
./isaaclab.sh -p scripts/demos/haply_teleoperation.py --websocket_uri ws://localhost:10001 --pos_sensitivity 1.65
```

### Windows

```batch
REM Haply SDK가 실행 중인지 확인
isaaclab.bat -p scripts\demos\haply_teleoperation.py --websocket_uri ws://localhost:10001 --pos_sensitivity 1.65
```

이 데모는 다음을 수행합니다:

1. WebSocket을 통해 Haply 기기에 연결
2. 시뮬레이션에 Franka Panda 로봇과 큐브 생성
3. Haply 위치를 로봇 엔드이펙터 위치에 매핑
4. 햅틱 피드백을 위해 접촉 힘을 Inverse3로 스트리밍

### 컨트롤

* **Inverse3 이동**: 로봇 엔드이펙터 위치 제어
* **VerseGrip 버튼 A**: 그리퍼 열기
* **VerseGrip 버튼 B**: 그리퍼 닫기
* **VerseGrip 버튼 C**: 엔드이펙터를 60° 회전

### 고급 옵션

명령줄 인수를 사용하여 데모를 맞춤 설정할 수 있습니다:

```bash
# 커스텀 WebSocket URI 사용
./isaaclab.sh -p scripts/demos/haply_teleoperation.py \
    --websocket_uri ws://192.168.1.100:10001

# 위치 감도 조정 (기본값: 1.0)
./isaaclab.sh -p scripts/demos/haply_teleoperation.py \
     --websocket_uri ws://localhost:10001 \
     --pos_sensitivity 2.0
```

### 데모 기능

* **작업 공간 매핑**: Haply 작업 공간은 안전 한계를 갖춘 로봇 도달 가능 공간에 매핑됩니다
* **역운동학**: 역운동학(IK)은 원하는 엔드이펙터 포즈를 위한 관절 위치를 계산합니다
* **힘 피드백**: 엔드이펙터 센서의 접촉 힘은 햅틱 피드백을 위해 Inverse3로 전송됩니다

<a id="haply-troubleshooting"></a>

## 문제 해결

### 햅틱 피드백 없음

**문제**: Inverse3에서 햅틱 피드백을 느낄 수 없음

해결책:

* Haply SDK에서 Inverse3가 활성 기기로 설정되어 있는지 확인
* 시뮬레이션에서 접촉 힘이 0이 아닌지 확인 (큐브를 잡으려고 시도해보기)
* `limit_force`가 너무 낮게 설정되어 있지 않은지 확인 (기본값: 2.0N)

## 다음 단계

* **데모 맞춤 설정**: 작업 공간 매핑 수정 또는 커스텀 버튼 동작 추가
* **자신만의 컨트롤러 구현**: 자신의 스크립트에서 `HaplyDevice` 사용

기기 API에 대한 자세한 정보는 API 문서의 `HaplyDevice`를 참조하세요.
