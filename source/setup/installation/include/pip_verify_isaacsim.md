# Isaac Sim 설치 검증

- 가상 환경이 활성화되어 있는지 확인하세요(해당되는 경우).
- 시뮬레이터가 예상대로 실행되는지 확인하세요:
  ```bash
  # note: you can pass the argument "--help" to see all arguments possible.
  isaacsim
  ```
- 특정 경험 파일로 실행하는 것도 가능합니다. 다음 명령을 실행하세요:
  ```bash
  # experience files can be absolute path, or relative path searched in isaacsim/apps or omni/apps
  isaacsim isaacsim.exp.full.kit
  ```

#### NOTE
Isaac Sim을 처음 실행할 때, 모든 종속 확장 기능이 레지스트리에서 다운로드됩니다.
이 과정은 최대 10분까지 걸릴 수 있으며, 각 경험 파일의 첫 실행 시 필수입니다.
확장 기능이 다운로드된 후에는 동일한 경험 파일을 사용하여 연속 실행 시 캐시된 확장 기능을 사용합니다.

#### ATTENTION
첫 실행 시 사용자에게 Nvidia Omniverse 라이선스 계약서 수락 프롬프트가 표시됩니다.
EULA를 수락하려면 아래 메시지가 표시될 때 `Yes`라고 응답하세요:

```bash
By installing or using Isaac Sim, I agree to the terms of NVIDIA OMNIVERSE LICENSE AGREEMENT (EULA)
in https://docs.isaacsim.omniverse.nvidia.com/latest/common/NVIDIA_Omniverse_License_Agreement.html

Do you accept the EULA? (Yes/No): Yes
```

위 지침을 따라 시뮬레이터가 실행되지 않거나 크래시가 발생하면, 구성에 문제가 있음을 의미합니다.
디버그 및 문제 해결을 위해 Isaac Sim
[문서](https://docs.omniverse.nvidia.com/dev-guide/latest/linux-troubleshooting.html)
및
[Isaac Sim 포럼](https://docs.isaacsim.omniverse.nvidia.com/latest/common/feedback.html)을 참조하세요.
