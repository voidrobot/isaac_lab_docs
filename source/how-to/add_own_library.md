<a id="how-to-add-library"></a>

# 학습 라이브러리 직접 추가하기

Isaac Lab에는 RSL-RL, RL-Games, SKRL, Stable Baselines 등과 같은 여러 라이브러리가 사전 통합되어 있습니다.
그러나 Isaac Lab과 함께 자체 라이브러리를 통합하거나, Isaac Lab에서 설치한 버전이 아닌 다른 버전의 라이브러리를 사용하고 싶을 수 있습니다.
이는 해당 라이브러리가 기반 시뮬레이터에서 사용되는 Python 버전을 지원하는 Python 패키지로 제공되는 경우 가능합니다.
예를 들어, Isaac Sim 4.0.0 이상을 사용하는 경우 라이브러리가 Python 3.11을 지원하는지 확인해야 합니다.

## 라이브러리의 다른 버전 사용하기

Isaac Lab에서 설치한 버전이 아닌 라이브러리의 다른 버전을 사용하려면 소스에서 빌드하여 설치하거나 PyPI에서 다른 버전의 라이브러리를 사용할 수 있습니다.

예를 들어, [rsl-rl](https://github.com/leggedrobotics/rsl_rl) 라이브러리의 수정된 버전을 사용하려면 다음 단계를 따를 수 있습니다:

1. Isaac Lab 설치 지침을 따르세요.これにより `rsl-rl` 라이브러리의 기본 버전이 설치됩니다.
2. GitHub 저장소에서 `rsl-rl` 라이브러리를 클론합니다:
   ```bash
   git clone git@github.com:leggedrobotics/rsl_rl.git
   ```
3. Python 환경에 라이브러리를 설치합니다:
   ```bash
   # Assuming you are in the root directory of the Isaac Lab repository
   cd IsaacLab

   # Note: If you are using a virtual environment, make sure to activate it before running the following command
   ./isaaclab.sh -p -m pip install -e /path/to/rsl_rl
   ```

이 경우 `rsl-rl` 라이브러리는 Isaac Lab에서 사용되는 Python 환경에 설치됩니다. 이제 실험에서 `rsl-rl` 라이브러리를 사용할 수 있습니다.
라이브러리 버전 및 기타 세부 정보를 확인하려면 다음 명령을 사용할 수 있습니다:

```bash
./isaaclab.sh -p -m pip show rsl-rl-lib
```

이제 이 명령은 라이브러리를 클론한 디렉토리를 `rsl-rl` 라이브러리의 위치로 표시해야 합니다.
예를 들어 라이브러리를 `/home/user/git/rsl_rl`에 클론한 경우, 위의 명령 출력은 다음과 같아야 합니다:

```bash
Name: rsl_rl
Version: 3.0.1
Summary: Fast and simple RL algorithms implemented in pytorch
Home-page: https://github.com/leggedrobotics/rsl_rl
Author: ETH Zurich, NVIDIA CORPORATION
Author-email:
License: BSD-3
Location: /home/user/git/rsl_rl
Requires: torch, torchvision, numpy, GitPython, onnx
Required-by:
```

## 새로운 라이브러리 통합하기

Isaac Lab에 새로운 라이브러리를 추가하는 것은 라이브러리의 다른 버전을 사용하는 것과 유사합니다.
Python 환경에 라이브러리를 설치하고 실험에서 사용할 수 있습니다.
ただし、ライブラリをIsaac Labと統合したい場合は、まず[環境のラッピング](wrap_rl_env.md#how-to-env-wrappers)で説明されているようにライブラリ用のラッパーを作成する必要があります。

新しいライブラリをIsaac Labと統合するには、次の手順に従ってください:

1. 拡張機能`isaaclab_rl`の`setup.py`にライブラリをextra-dependencyとして追加します。
   これにより、Isaac Labをインストールするときにライブラリがインストールされるか、ライブラリがインストールされていないまたは利用できない場合はエラーが発生します。
2. Isaac Labで使用されるPython環境にライブラリをインストールします。前のセクションで説明した手順に従って行うことができます。
3. ライブラリ用のラッパーを作成します。さまざまなライブラリ用のラッパーの例については、モジュール [`isaaclab_rl`](../api/lab_rl/isaaclab_rl.md#module-isaaclab_rl) を確認してください。ライブラリ用の新しいラッパーを作成し、モジュールに追加することができます。ラッパー用の新しいモジュールを作成することもできます。
4. ライブラリ用のワークフロースクリプトを作成して、エージェントのトレーニングと評価を行います。`scripts/reinforcement_learning`ディレクトリの既存のワークフロースクリプトを参考にしてください。ライブラリ用の新しいワークフロースクリプトを作成し、ディレクトリに追加することができます。

オプションとして、ラッパーのテストとドキュメントを追加することもできます。これによりラッパーが期待通りに動作することを確認し、ラッパーの使用方法についてユーザーをガイドすることができます。

* ラッパーが期待通りに動作し、ライブラリと互換性を保つことを確認するためのテストを追加します。これらのテストは `source/isaaclab_rl/test` ディレクトリに追加できます。
* ラッパーのドキュメントを追加します。APIドキュメントを[APIドキュメント](../api/lab_rl/isaaclab_rl.md#api-isaaclab-rl)の`isaaclab_rl`モジュールに追加できます。

## RL 에이전트 구성하기

Isaac Lab과 함께 새로운 라이브러리를 통합한 후에는 예제 환경을 구성하여 새로운 라이브러리를 사용할 수 있습니다.
다른 라이브러리를 사용하도록 훈련 프로세스를 구성하는 예시는 [RL 에이전트 구성하기](../tutorials/03_envs/configuring_rl_training.md#tutorial-configure-rl-training)를 참조하세요.
