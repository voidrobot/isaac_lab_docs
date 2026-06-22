# Isaac Sim 심볼릭 링크 생성

설치된 Isaac Sim 루트 폴더와 Isaac Lab 디렉터리의 `_isaac_sim` 간에 심볼릭 링크를 설정합니다. 이렇게 하면 Isaac Sim과 함께 제공되는 Python 모듈을 인덱싱하고 확장을 찾는 것이 편리해집니다.

### Linux

```bash
# 클론된 저장소로 이동
cd IsaacLab
# 심볼릭 링크 생성
ln -s ${ISAACSIM_PATH} _isaac_sim

# 예를 들어:
# 옵션 1: 사전 빌드된 바이너리가 설치된 경우:
# ln -s ${HOME}/isaacsim _isaac_sim
#
# 옵션 2: Isaac Sim을 소스에서 빌드한 경우:
# ln -s ${HOME}/IsaacSim/_build/linux-x86_64/release _isaac_sim
```

### Windows

```batch
:: 클론된 저장소로 이동
cd IsaacLab
:: 심볼릭 링크 생성 - 관리자 권한으로 Command Prompt를 실행해야 함
mklink /D _isaac_sim %ISAACSIM_PATH%

:: 예를 들어:
:: 옵션 1: 사전 빌드된 바이너리가 설치된 경우:
:: mklink /D _isaac_sim C:\isaacsim
::
:: 옵션 2: Isaac Sim을 소스에서 빌드한 경우:
:: mklink /D _isaac_sim C:\IsaacSim\_build\windows-x86_64\release
```
