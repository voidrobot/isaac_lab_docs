# Isaac Lab 설치 확인

설치가 성공적으로 이루어졌는지 확인하려면 저장소의 최상위 디렉터리에서 다음 명령을 실행하세요.

### Linux

```bash
# 옵션 1: isaaclab.sh 실행 파일 사용
# 참고: 이 방법은 번들 python과 가상 환경 모두에서 작동합니다.
./isaaclab.sh -p scripts/tutorials/00_sim/create_empty.py

# 옵션 2: 가상 환경의 python 사용
python scripts/tutorials/00_sim/create_empty.py
```

### Windows

```batch
:: 옵션 1: isaaclab.bat 실행 파일 사용
:: 참고: 이 방법은 번들 python과 가상 환경 모두에서 작동합니다.
isaaclab.bat -p scripts\tutorials\00_sim\create_empty.py

:: 옵션 2: 가상 환경의 python 사용
python scripts\tutorials\00_sim\create_empty.py
```

위 명령은 시뮬레이터를 실행하고 검은 뷰포트가 있는 창을 표시해야 합니다. 터미널에서 `Ctrl+C`를 눌러 스크립트를 종료할 수 있습니다.
Windows 머신에서는 Command Prompt에서 `Ctrl+Break` 또는 `Ctrl+fn+B`를 눌러 프로세스를 종료하세요.

![검은 창이 있는 시뮬레이터](../../../_static/setup/verify_install.jpg)

이 창을 보셨다면 설치가 성공적으로 완료된 것입니다! 🎉

#### 참고
`ModuleNotFoundError: No module named 'isaacsim'` 오류가 표시되면 가상 환경이 활성화되어 있고 `source _isaac_sim/setup_conda_env.sh`가 실행되었는지 확인하세요(uv를 사용하는 경우에도 적용됩니다).

# 로봇 훈련하기!

이제 Isaac Lab을 사용하여 강화 학습을 통해 로봇을 훈련시킬 수 있습니다! Isaac Lab을 사용하는 가장 빠른 방법은 **Batteries-included** 로봇 작업 중 하나를 사용한 사전 정의된 워크플로를 이용하는 것입니다. 다음 명령을 실행하여 빠르게 개미가 걷는 법을 훈련시켜 보세요!
더 빠른 훈련을 위해 `--headless` 옵션을 추가하는 것을 권장합니다.

### Linux

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Ant-v0 --headless
```

### Windows

```batch
isaaclab.bat -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Ant-v0 --headless
```

… 또는 로봇 개!

### Linux

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Velocity-Rough-Anymal-C-v0 --headless
```

### Windows

```batch
isaaclab.bat -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Velocity-Rough-Anymal-C-v0 --headless
```

Isaac Lab은 프로젝트 요구 사항에 맞는 자체 **Tasks**와 **Workflows**를 생성하는 데 필요한 도구를 제공합니다.
[Adding your own learning Library](../../../how-to/add_own_library.md#how-to-add-library) 또는 [Wrapping Environments](../../../how-to/wrap_rl_env.md#how-to-env-wrappers)와 같은 방법 가이드를 확인해 보세요.

![유휴 상태의 손...](../../../_static/setup/isaac_ants_example.jpg)
