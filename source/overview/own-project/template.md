<a id="template-generator"></a>

# 새 프로젝트 또는 작업 생성

기존에는 Isaac Lab의 기능을 활용하는 새 프로젝트를 빌드하기 위해 Isaac Lab 저장소 내에 자체 확장을 만드는 것이 필요했습니다. 그러나 이 방식은 프로젝트 가시성을 낮추고 Isaac Lab의 한 버전에서 다른 버전으로 업데이트하는 것을 복잡하게 만들 수 있습니다. 이러한 문제를 해결하기 위해 이제 Isaac Lab 기반 프로젝트와 작업을 생성하기 위한 명령줄 도구인 **템플릿 생성기**를 제공합니다.

템플릿 생성기를 사용하면 다음을 생성할 수 있습니다:

* **외부 프로젝트**(권장): Isaac Lab 저장소의 일부가 아닌 독립된 프로젝트입니다. 이 방식은 핵심 Isaac Lab 저장소 외부에서 작동하여 개발 노력이 자체적으로 유지되도록 보장합니다. 또한, 코드를 옴니버스에서 확장으로 실행할 수 있습니다.

  #### 힌트
  외부 프로젝트의 경우, 템릿 생성기는 지정된 디렉터리에서 새로운 Git 저장소를 초기화합니다. 생성된 내용을 자신의 원격 저장소(예: GitHub)에 푸시하여 다른 사람과 공유할 수 있습니다.
* **내부 작업**: Isaac Lab 저장소의 일부인 작업입니다. 이 방식은 Isaac Lab 저장소 내에 새로운 작업을 생성하여 기여하기 위해のみ 사용되어야 합니다.

  #### 경고
  Isaac Lab의 pip 설치는 *내부* 템플릿을 지원하지 않습니다.
  `isaaclab`이 `site-packages` 또는 `dist-packages`에서 로드되는 경우, *내부* 옵션이 비활성화되고 대신 *외부* 템플릿이 사용됩니다.

## 템릿 생성기 실행

[설치 가이드](../../setup/installation/index.html)에 따라 Isaac Lab을 설치합니다.
터미널에서 Python 스크립트를 호출하기 간단해지도록 conda 또는 uv 설치를 사용하는 것을 권장합니다.

그런 다음 다음 명령을 실행하여 새 외부 프로젝트 또는 내부 작업을 생성합니다:

### Linux

```bash
./isaaclab.sh --new  # 또는 "./isaaclab.sh -n"
```

### Windows

```batch
isaaclab.bat --new  :: 또는 "isaaclab.bat -n"
```

생성기는 다음과 같은 질문을 통해 프로젝트/작업을 사용자의 필요에 맞게 설정하도록 안내합니다:

* 프로젝트/작업 유형(외부 또는 내부)과 선택한 유형에 따른 프로젝트/작업 경로 또는 이름.
* Isaac Lab 워크플로우(참조: [작업 설계 워크플로우](../core-concepts/task_workflows.md#feature-workflows)).
* 강화 학습 라이브러리(참조: [강화 학습 라이브러리 비교](../reinforcement-learning/rl_frameworks.md#rl-frameworks)) 및 알고리즘(선택된 라이브러리가 여러 알고리즘을 지원하는 경우).

## 외부 프로젝트 사용법 (생성 후)

외부 프로젝트가 생성되면 지정된 디렉터리에 `README.md` 파일이 생성됩니다.
이 파일에는 프로젝트를 설치하고 작업을 실행하는 방법이 포함됩니다.

다음은 시작하는 데 도움이 되는 일반적인 명령어입니다:

#### 참고
Isaac Lab이 conda 환경이나 (가상) Python 환경에 설치되어 있지 않은 경우, 아래 명령어에서 `python` 대신 `FULL_PATH_TO_ISAACLAB/isaaclab.sh -p`(또는 Windows에서는 `FULL_PATH_TO_ISAACLAB\isaaclab.bat -p`)를 사용하세요.

* 프로젝트 설치(편집 가능한 모드).

  ### Linux

  ```bash
  python -m pip install -e source/<given-project-name>
  ```

  ### Windows

  ```batch
  python -m pip install -e source\<given-project-name>
  ```
* 프로젝트에서 사용 가능한 작업 목록 보기.

  #### 경고
  작업 이름이 변경된 경우, `scripts/list_envs.py` 파일에서 검색 패턴 `"Template-"`를 업데이트하여 작업이 목록에 나타나도록 해야 할 수 있습니다.

  ### Linux

  ```bash
  python scripts/list_envs.py
  ```

  ### Windows

  ```batch
  python scripts\list_envs.py
  ```
* 작업 실행.

  ### Linux

  ```bash
  python scripts/<specific-rl-library>/train.py --task=<Task-Name>
  ```

  ### Windows

  ```batch
  python scripts\<specific-rl-library>\train.py --task=<Task-Name>
  ```

자세한 내용은 생성된 프로젝트의 `README.md` 파일의 지침을 따르세요.

## 내부 작업 사용법 (생성 후)

내부 작업이 생성되면 Isaac Lab의 다른 작업과 함께 사용할 수 있게 됩니다.

다음은 시작하는 데 도움이 되는 일반적인 명령어입니다:

#### 참고
Isaac Lab이 conda 환경이나 (가상) Python 환경에 설치되어 있지 않은 경우, 아래 명령어에서 `python` 대신 `./isaaclab.sh -p`(또는 Windows에서는 `isaaclab.bat -p`)를 사용하세요.

* Isaac Lab에서 사용 가능한 작업 목록 보기.

  ### Linux

  ```bash
  python scripts/environments/list_envs.py
  ```

  ### Windows

  ```batch
  python scripts\environments\list_envs.py
  ```
* 작업 실행.

  ### Linux

  ```bash
  python scripts/reinforcement_learning/<specific-rl-library>/train.py --task=<Task-Name>
  ```

  ### Windows

  ```batch
  python scripts\reinforcement_learning\<specific-rl-library>\train.py --task=<Task-Name>
  ```
* 더미 에이전트로 작업 실행.

  여기에는 0을 출력하거나 무작위 동작을 출력하는 더미 에이전트가 포함됩니다. 이들은 환경이 올바르게 구성되었는지 확인하는 데 유용합니다.
  * 제로 액션 에이전트

    ### Linux

    ```bash
    python scripts/zero_agent.py --task=<Task-Name>
    ```

    ### Windows

    ```batch
    python scripts\zero_agent.py --task=<Task-Name>
    ```
  * 랜덤 액션 에이전트

    ### Linux

    ```bash
    python scripts/random_agent.py --task=<Task-Name>
    ```

    ### Windows

    ```batch
    python scripts\random_agent.py --task=<Task-Name>
    ```
