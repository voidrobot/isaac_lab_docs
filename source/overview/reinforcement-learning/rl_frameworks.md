<a id="rl-frameworks"></a>

# 강화 학습 라이브러리 비교

이 섹션에서는 Isaac Lab에서 지원하는 강화 학습 라이브러리 개요와 라이브러리 간 성능 벤치마크를 제공합니다.

지원되는 라이브러리는 다음과 같습니다:

- [SKRL](https://skrl.readthedocs.io)
- [RSL-RL](https://github.com/leggedrobotics/rsl_rl)
- [RL-Games](https://github.com/Denys88/rl_games)
- [Stable-Baselines3](https://stable-baselines3.readthedocs.io/en/master/index.html)

## 기능 비교

| 기능                         | RL-Games        | RSL RL            | SKRL                                                            | Stable Baselines3                                                                                       |
|------------------------------|-----------------|-------------------|-----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| 포함된 알고리즘              | PPO, SAC, A2C   | PPO, Distillation | [광범위한 목록](https://skrl.readthedocs.io/en/latest/#agents) | [광범위한 목록](https://github.com/DLR-RM/stable-baselines3?tab=readme-ov-file#implemented-algorithms) |
| 벡터화 훈련                  | Yes             | Yes               | Yes                                                             | No                                                                                                      |
| 분산 훈련                    | Yes             | Yes               | Yes                                                             | No                                                                                                      |
| 지원되는 ML 프레임워크       | PyTorch         | PyTorch           | PyTorch, JAX                                                    | PyTorch                                                                                                 |
| 멀티 에이전트 지원           | PPO             | PPO               | PPO + 멀티 에이전트 알고리즘                                    | 외부 프로젝트 지원                                                                               |
| 문서화                       | 낮음            | 낮음              | 종합적                                                            | 광범위함                                                                                               |
| 커뮤니티 지원                | 작은 커뮤니티   | 작은 커뮤니티     | 작은 커뮤니티                                                   | 큰 커뮤니티                                                                                         |
| Isaac Lab에서 사용 가능한 예시 | 많음            | 많음              | 많음                                                            | 적음                                                                                                   |

## 훈련 성능

우리는 단일 NVIDIA GeForce RTX 4090에서 `--headless` 옵션을 사용하여 `Isaac-Humanoid-v0` 환경에서 각 RL 라이브러리로 훈련을 수행하고, 65.5M 단계(4096개 환경 x 32개 롤아웃 단계 x 500회 반복)에 대한 총 훈련 시간을 기록했습니다.

| RL 라이브러리        |   초 단위 시간 |
|---------------------|----------------|
| RL-Games          |            201 |
| SKRL              |            201 |
| RSL RL            |            198 |
| Stable-Baselines3 |            287 |

훈련 명령어 (터미널 출력에서 *'Training time: XXX seconds'* 줄 확인):

```bash
python scripts/reinforcement_learning/rl_games/train.py --task Isaac-Humanoid-v0 --max_iterations 500 --headless
python scripts/reinforcement_learning/skrl/train.py --task Isaac-Humanoid-v0 --max_iterations 500 --headless
python scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Humanoid-v0 --max_iterations 500 --headless
python scripts/reinforcement_learning/sb3/train.py --task Isaac-Humanoid-v0 --max_iterations 500 --headless
```
