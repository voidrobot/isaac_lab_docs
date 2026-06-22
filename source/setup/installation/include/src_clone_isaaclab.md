# Isaac Lab 클론하기

#### NOTE
프로젝트에 기여하기 위해 [Isaac Lab 저장소](https://github.com/isaac-sim/IsaacLab/fork)를 포크하는 것을 권장하지만, 프레임워크를 사용하는 데 필수는 아닙니다. 포크하는 경우, 다음 지침에서 `isaac-sim`을 사용자 이름으로 교체하세요.

Isaac Lab 저장소를 프로젝트 워크스페이스로 클론합니다:

### SSH

```bash
git clone git@github.com:isaac-sim/IsaacLab.git
```

### HTTPS

```bash
git clone https://github.com:isaac-sim/IsaacLab.git
```

Linux와 Windows용 헬퍼 실행 파일 [isaaclab.sh](https://github.com/isaac-sim/IsaacLab/blob/main/isaaclab.sh)와 [isaaclab.bat](https://github.com/isaac-sim/IsaacLab/blob/main/isaaclab.bat)를 제공하여 확장을 관리하는 유틸리티를 포함합니다.

### Linux

```text
./isaaclab.sh --help

usage: isaaclab.sh [-h] [-i] [-f] [-p] [-s] [-t] [-o] [-v] [-d] [-n] [-c] -- Utility to manage Isaac Lab.

optional arguments:
   -h, --help           도움말 내용을 표시합니다.
   -i, --install [LIB]  Isaac Lab 내의 확장 및 학습 프레임워크(rl_games, rsl_rl, sb3, skrl)를 추가 종속성으로 설치합니다. 기본값은 'all'입니다.
   -f, --format         pre-commit을 실행하여 코드를 포맷하고 린트를 확인합니다.
   -p, --python         Isaac Sim 또는 가상 환경(활성화된 경우)에서 제공하는 파이썬 실행 파일을 실행합니다.
   -s, --sim            Isaac Sim에서 제공하는 시뮬레이터 실행 파일(isaac-sim.sh)을 실행합니다.
   -t, --test           모든 파이썬 pytest 테스트를 실행합니다.
   -o, --docker         도커 컨테이너 헬퍼 스크립트(docker/container.sh)를 실행합니다.
   -v, --vscode         템플릿에서 VSCode 설정 파일을 생성합니다.
   -d, --docs           Sphinx를 사용하여 소스에서 문서를 빌드합니다.
   -n, --new            템플릿에서 새 외부 프로젝트 또는 내부 작업을 생성합니다.
   -c, --conda [NAME]   Isaac Lab용 conda 환경을 생성합니다. 기본 이름은 'env_isaaclab'입니다.
   -u, --uv [NAME]      Isaac Lab용 uv 환경을 생성합니다. 기본 이름은 'env_isaaclab'입니다.
```

### Windows

```text
isaaclab.bat --help

usage: isaaclab.bat [-h] [-i] [-f] [-p] [-s] [-v] [-d] [-n] [-c] -- Utility to manage Isaac Lab.

optional arguments:
   -h, --help           도움말 내용을 표시합니다.
   -i, --install [LIB]  Isaac Lab 내의 확장 및 학습 프레임워크(rl_games, rsl_rl, sb3, skrl)를 추가 종속성으로 설치합니다. 기본값은 'all'입니다.
   -f, --format         pre-commit을 실행하여 코드를 포맷하고 린트를 확인합니다.
   -p, --python         Isaac Sim 또는 가상 환경(활성화된 경우)에서 제공하는 파이썬 실행 파일을 실행합니다.
   -s, --sim            Isaac Sim에서 제공하는 시뮬레이터 실행 파일(isaac-sim.bat)을 실행합니다.
   -t, --test           모든 파이썬 pytest 테스트를 실행합니다.
   -v, --vscode         템플릿에서 VSCode 설정 파일을 생성합니다.
   -d, --docs           Sphinx를 사용하여 소스에서 문서를 빌드합니다.
   -n, --new            템플릿에서 새 외부 프로젝트 또는 내부 작업을 생성합니다.
   -c, --conda [NAME]   Isaac Lab용 conda 환경을 생성합니다. 기본 이름은 'env_isaaclab'입니다.
   -u, --uv [NAME]      Isaac Lab용 uv 환경을 생성합니다. 기본 이름은 'env_isaaclab'입니다.
```
