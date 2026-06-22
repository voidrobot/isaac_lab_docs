시뮬레이터가 예상대로 실행되는지 확인하십시오:

### Linux

```bash
# 참고: "--help" 인수를 전달하여 가능한 모든 인수를 볼 수 있습니다.
${ISAACSIM_PATH}/isaac-sim.sh
```

### Windows

```batch
:: 참고: "--help" 인수를 전달하여 가능한 모든 인수를 볼 수 있습니다.
%ISAACSIM_PATH%\isaac-sim.bat
```

독립형 Python 스크립트에서 시뮬레이터가 실행되는지 확인하십시오:

### Linux

```bash
# 파이썬 경로가 올바르게 설정되었는지 확인합니다
${ISAACSIM_PYTHON_EXE} -c "print('Isaac Sim configuration is now complete.')"
# Isaac Sim이 Python에서 실행될 수 있는지 확인합니다
${ISAACSIM_PYTHON_EXE} ${ISAACSIM_PATH}/standalone_examples/api/isaacsim.core.api/add_cubes.py
```

### Windows

```batch
:: 파이썬 경로가 올바르게 설정되었는지 확인합니다
%ISAACSIM_PYTHON_EXE% -c "print('Isaac Sim configuration is now complete.')"
:: Isaac Sim이 Python에서 실행될 수 있는지 확인합니다
%ISAACSIM_PYTHON_EXE% %ISAACSIM_PATH%\standalone_examples\api\isaacsim.core.api\add_cubes.py
```

위 지침을 따르면서 시뮬레이터가 실행되지 않거나 크래시가 발생하는 경우, 잘못된 구성이 있음을 의미합니다. 디버그 및 문제 해결을 위해 다음을 확인하십시오:
- Isaac Sim [문서](https://docs.omniverse.nvidia.com/dev-guide/latest/linux-troubleshooting.html)
- [Isaac Sim 포럼](https://docs.isaacsim.omniverse.nvidia.com/latest/common/feedback.html)
