# 인구 기반 훈련

## PBT가 하는 일

* **동일한 작업**에서 *N*개의 정책을 병렬로 훈련(“인구”)합니다.
* 매 `interval_steps`마다:
  1. 각 정책의 체크포인트와 목표를 저장합니다.
  2. 인구의 점수를 매기고 **리더**와 **성과 부족자**를 식별합니다.
  3. 성과 부족자에 대해, 랜덤 리더의 가중치를 대체하고 **선택된 하이퍼파라미터를 돌연변이**합니다.
  4. 새로운 가중치/파라미터로 해당 프로세스를 자동으로 재시작합니다.

## 리더 / 성과 부족자 선택

`o_i`를 평균 `μ`와 표준편차 `σ`를 갖는 각 초기화된 정책의 목표라고 하자.

상한 및 하한 성능 컷은 다음과 같습니다:

```default
upper_cut = max(μ + threshold_std * σ, μ + threshold_abs)
lower_cut = min(μ - threshold_std * σ, μ - threshold_abs)
```

* **리더**: `o_i > upper_cut`
* **성과 부족자**: `o_i < lower_cut`

“자연 선택” 규칙:

1. **성과 부족자만** 행동 대상이 됩니다(돌연변이 또는 대체).
2. 리더가 존재하면, 성과 부족자를 랜덤 리더로 대체합니다; 그렇지 않으면 자체적으로 돌연변이를 일으킵니다.

## 돌연변이 (하이퍼파라미터)

* 각 파라미터는 돌연변이 함수(예: `mutate_float`, `mutate_discount` 등)를 가집니다.
* 파라미터는 확률 `mutation_rate`로 돌연변이가 발생합니다.
* 돌연변이 발생 시, 그 값은 `change_range = (min, max)` 범위 내에서 방해받습니다.
* PBT 설정에서 허용된(whitelisted) 키만 고려됩니다.

## 예시 설정

```yaml
pbt:
  enabled: True
  policy_idx: 0
  num_policies: 8
  directory: .
  workspace: "pbt_workspace"
  objective: episode.Curriculum/difficulty_level
  interval_steps: 50000000
  threshold_std: 0.1
  threshold_abs: 0.025
  mutation_rate: 0.25
  change_range: [1.1, 2.0]
  mutation:
    agent.params.config.learning_rate: "mutate_float"
    agent.params.config.grad_norm: "mutate_float"
    agent.params.config.entropy_coef: "mutate_float"
    agent.params.config.critic_coef: "mutate_float"
    agent.params.config.bounds_loss_coef: "mutate_float"
    agent.params.config.kl_threshold: "mutate_float"
    agent.params.config.gamma: "mutate_discount"
    agent.params.config.tau: "mutate_discount"
```

`objective: episode.Curriculum/difficulty_level`은
`infos["episode"]["Curriculum/difficulty_level"]`를 스칼라 값으로 사용하는 점선 표현으로, **정책을 순위 매기는 데 사용**됩니다(높을수록 더 좋음).
`num_policies: 8`인 경우, 동일한 `workspace`를 공유하고 고유한 `policy_idx`(0-7)를 갖는 여덟 개의 프로세스를 시작합니다.

## PBT 시작

각 정책에 대해 **한 프로세스씩** 시작하고, 모두 **같은 작업 공간**을 가리켜야 합니다. 각 프로세스에 고유한 `policy_idx`를 설정하고 공통의 `num_policies`도 설정해야 합니다.

필요한 최소 플래그:

* `agent.pbt.enabled=True`
* `agent.pbt.directory=<path/to/shared_folder>`
* `agent.pbt.policy_idx=<0..num_policies-1>`

#### 참고
모든 프로세스가 동일한 `agent.pbt.workspace`를 사용해야 서로의 체크포인트를 볼 수 있습니다.

## 팁

* 체크포인트를 적절히 유지하세요: 정말로 더 촘촘한 PBT 주기가 필요할 때만 `interval_steps`를 줄이세요.
* 더 큰 `threshold_std`와 `threshold_abs`를 사용하면 인구의 다양성을 높일 수 있습니다.
* PBT의 이점을 보기 위해 6개 이상의 워커로 실행하는 것이 권장됩니다.

## 훈련 예시

다음 작업에 대한 참조 PPO 구성을 제공합니다:
[Isaac-Dexsuite-Kuka-Allegro-Lift-v0](https://github.com/isaac-sim/IsaacLab/blob/main/source/isaaclab_tasks/isaaclab_tasks/manager_based/manipulation/dexsuite/config/kuka_allegro/agents/rl_games_ppo_cfg.yaml).
최적의 로깅 경험을 위해 스크립트에서 wandb를 사용한 로깅을 권장합니다.

*n*이 각 워커 인덱스를 나타내는 *N*개의 워커를 실행합니다:

```bash
# 각 워커마다 한 번씩 실행 (n = 0..N-1), 모두 동일한 디렉토리/작업 공간을 가리킴
./isaaclab.sh -p scripts/reinforcement_learning/rl_games/train.py \
  --seed=<n> \
  --task=Isaac-Dexsuite-Kuka-Allegro-Lift-v0 \
  --num_envs=8192 \
  --headless \
  --track \
  --wandb-name=idx<n> \
  --wandb-entity=<**entity**> \
  --wandb-project-name=<**project**>
  agent.pbt.enabled=True \
  agent.pbt.num_policies=<N> \
  agent.pbt.policy_idx=<n> \
  agent.pbt.workspace=<**pbt_workspace_name**> \
  agent.pbt.directory=<**/path/to/shared_folder**> \
```

## 참고문헌

이 PBT 구현은 *Dexpbt: Scaling up dexterous manipulation for hand-arm systems with population based training* (Petrenko et al., 2023)을 재구현하고 영감을 받았습니다.

```bibtex
@article{petrenko2023dexpbt,
  title={Dexpbt: Scaling up dexterous manipulation for hand-arm systems with population based training},
  author={Petrenko, Aleksei and Allshire, Arthur and State, Gavriel and Handa, Ankur and Makoviychuk, Viktor},
  journal={arXiv preprint arXiv:2305.12127},
  year={2023}
}
```
