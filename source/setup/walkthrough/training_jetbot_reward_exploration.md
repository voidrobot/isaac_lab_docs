<a id="walkthrough-training-jetbot-reward-exploration"></a>

# RL 문제 탐색

Jetbot에 대한 명령은 원하는 주행 방향을 지정하는 단위 벡터이며, 이 somehow 에이전트에게 인식시켜야 한다. 그래야 그에 따라 행동을 조정할 수 있다. 이를 구현하는 방법은 여러 가지가 있으며, "제로 차수" 접근법은 단순히 관찰 공간에 이 명령을 포함시키는 것이다. 시작으로, **`IsaacLabTutorialEnvCfg` 를 편집하여 관찰 공간을 9로 설정**한다: 세계 속도 벡터는 로봇의 선속도와 각속도를 포함하며, 이는 6차원이므로 여기서 명령 벡터를 추가하면 관측 공간의 총 차수가 9가 된다.

다음으로, 관측값을 얻을 때 이 추가 연산을 수행해야 한다. 또한 앞으로 사용할 전방 벡터를 계산해야 한다. Jetbot의 전방 벡터는 x 축이므로, `root_link_quat_w` 를 `[1,0,0]` 에 적용하여 월드 프레임에서의 전방 벡터를 구한다. `_get_observations` 메서드를 다음 내용으로 교체한다:

```python
def _get_observations(self) -> dict:
    self.velocity = self.robot.data.root_com_vel_w
    self.forwards = math_utils.quat_apply(self.robot.data.root_link_quat_w, self.robot.data.FORWARD_VEC_B)
    obs = torch.hstack((self.velocity, self.commands))
    observations = {"policy": obs}
    return observations
```

그렇다면 보상은 어떻게 설계해야 할까?

로봇이 원하는 대로 행동할 때, 명령 방향으로 최대 속도로 주행한다. 만약 "전진 주행"과 "명령과의 정렬"에 모두 보상을 준다면, 이 결합된 신호를 최대화함으로써 명령을 향해 주행하게 될 것이다… 맞는가?

한 번 시도해 보자! `_get_rewards` 메서드를 다음 내용으로 교체한다:

```python
def _get_rewards(self) -> torch.Tensor:
    forward_reward = self.robot.data.root_com_lin_vel_b[:,0].reshape(-1,1)
    alignment_reward = torch.sum(self.forwards * self.commands, dim=-1, keepdim=True)
    total_reward = forward_reward + alignment_reward
    return total_reward
```

`forward_reward` 은 몸체 프레임에서의 로봇 질량 중심 선속도의 x 성분이다. 로봇 에셋의 전방 방향이 x 방향이므로, 이는 월드 프레임에서의 전방 벡터와 선속도 사이의 내적과 동일하다고 볼 수 있다. 정렬 항은 전방 벡터와 명령 벡터 사이의 내적이다: 두 벡터가 같은 방향을 가리킬 경우 이 값은 1이 되고, 반대 방향일 경우 -1이 된다. 이 둘을 더하여 결합된 보상을 얻고, 마지막으로 학습을 시작할 수 있다! 결과를 보자!

```bash
python scripts/skrl/train.py --task=Template-Isaac-Lab-Tutorial-Direct-v0
```

![Naive results](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/walkthrough_naive_webp.webp)

분명히 더 나은 결과를 얻을 수 있을 것이다!

## 보상 및 관측 튜닝

학습을 위한 환경을 튜닝할 때 경험칙으로, 관측 공간을 가능한 작게 유지하는 것이 좋다. 이렇게 함으로써 모델의 매개변수 수(오컴의 면도날의 직설적 해석)를 줄이고 thereby 학습 시간을 단축할 수 있다. 여기서 우리는 명령을 향한 정렬과 전진 속도를 somehow 인코딩해야 한다. 이를 수행하는 한 가지 방법은 선형 대수학의 내적과 외적을 활용하는 것이다! `_get_observations` 의 내용을 다음 내용으로 교체한다:

```python
def _get_observations(self) -> dict:
    self.velocity = self.robot.data.root_com_vel_w
    self.forwards = math_utils.quat_apply(self.robot.data.root_link_quat_w, self.robot.data.FORWARD_VEC_B)

    dot = torch.sum(self.forwards * self.commands, dim=-1, keepdim=True)
    cross = torch.cross(self.forwards, self.commands, dim=-1)[:,-1].reshape(-1,1)
    forward_speed = self.robot.data.root_com_lin_vel_b[:,0].reshape(-1,1)
    obs = torch.hstack((dot, cross, forward_speed))

    observations = {"policy": obs}
    return observations
```

또한 **`IsaacLabTutorialEnvCfg` 를 편집하여 관측 공간을 3으로 다시 설정**해야 한다: 내적(점곱), 외적의 z 성분, 그리고 전진 속도를 포함한다.

내적 또는 점곱은 두 벡터가 얼마나 정렬되어 있는지를 단일 스칼라량으로 나타낸다. 두 벡터가 매우 정렬되어 있고 동일한 방향을 가리킨 경우, 내적은 큰 양수 값이 된다. 반대로 방향이 반대라면 큰 음수 값이 된다. 두 벡터가 수직이면 내적은 0이다. 즉, 전방 벡터와 명령 벡터 사이의 내적은 우리가 명령을 향해 있는지 또는 그 반대 방향을 향하고 있는지를 알려줄 수 있으나, 정렬을 개선하기 위해 어느 방향으로 전환해야 하는지는 알려주지 않는다.

외적 또한 두 벡터의 정렬 정도를 알려주지만, 이는 벡터 형태로 관계를 표현한다. 두 벡터 사이의 외적은 그 두 벡터를 포함하는 평면에 수직한 축을 정의하며, 이 축을 따라 결과 벡터의 방향은 좌표계의 치리성(차원 순서 또는 손잡이)에 의해 결정된다. 우리의 경우, 2D 평면 내에서 동작하므로 $\vec{forward} \times \vec{command}$ 의 결과에서 z 성분만을 살펴볼 수 있다. 이 성분은 두 벡터가 일직선상에 있으면 0이 되고, 명령 벡터가 전방 벡터의 왼쪽이면 양수, 오른쪽이면 음수가 된다.

마지막으로, 질량 중심의 선속도 x 성분은 전진 속도를 알려준다: 양수는 전진, 음수는 후진을 의미한다. 이러한 세 가지 요소를 "수평"(차원 1 방향으로) 결합하여 각 Jetbot의 관측값을 생성한다. 이렇게만 해도 성능이 향상된다!

![Improved results](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/walkthrough_improved_webp.webp)

질적으로 볼 때 학습이 더 잘 되는 듯하며, Jetbot들이 조금씩 앞으로 나아가고 있다… 더욱 더 나은 결과를 얻을 수 있을까?

학습을 위한 또 다른 경험칙은 보상 함수를 가능한 단순화하는 것이다. 보상의 항들은 논리적 "OR" 연산과 유사하게 동작한다. 우리의 경우, 전진 주행과 명령과의 정렬에 보상을 주기 위해 두 항을 더했기 때문에, 에이전트는 전진 주행 **또는** 명령과의 정렬 중 하나만으로도 보상을 받을 수 있다. 에이전트가 명령 방향으로 전진 주행하도록 강제하기 위해서는 전진 주행 **그리고** 명령과의 정렬에만 보상을 주어야 한다. 논리적 "AND"는 곱셈을 의미하므로 다음과 같은 보상 함수를 사용할 수 있다:

```python
def _get_rewards(self) -> torch.Tensor:
    forward_reward = self.robot.data.root_com_lin_vel_b[:,0].reshape(-1,1)
    alignment_reward = torch.sum(self.forwards * self.commands, dim=-1, keepdim=True)
    total_reward = forward_reward*alignment_reward
    return total_reward
```

이제 정렬 보상이 0이 아닌 경우에만 전진 주행에 대해 보상을 받게 된다. 이와 같은 보상 함수가 어떤 결과를 가져오는지 보자!

![Tuned results](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/walkthrough_tuned_webp.webp)

학습 속도는 확실히 빨라졌으나, Jetbot들이 명령이 뒤쪽을 향하고 있을 때 후진으로 주행하는 행동을 학습했다. 이는 우리의 경우에 바람직할 수 있으나, 보상 함수가 정책 행동에 얼마나 큰 영향을 미치는지를 보여준다.この場合, 보상 함수는 **퇴화된 해** 를 갖는다: 보상은 전진 주행かつ 명령과의 정렬일 때 최대화되지만, Jetbot이 후진으로 이동하면 전진 항은 음수가 되며, 후진하면서 명령 쪽으로 이동하면 정렬 항도 **음수** 가 되므로, 그 결과 보상은 양수가 된다! 자신만의 환경을 설계할 때 이와 같은 퇴화된 해를 마주치게 되며, 보상 함수를 수정하여 그러한 행동을 억제하거나 장치하는 데 상당한 보상 엔지니어링 노력이 투여된다.

우리 경우에서는 이와 같은 행동을 원하지 않는다고 가정하자. 우리 경우, 정렬 항의 정의域은 `[-1, 1]` 이지만, 이를 양수 값으로만 매핑하고 싶다. 정렬 항의 부호를 완전히 제거하고 싶지는 않으며, 오히려 큰 음수 값을 0에 가깝게 만들어, 오정렬 상태에서는 보상을 거의 받지 않도록 하고 싶다. 이를 달성하기 위해 지수 함수를 사용할 수 있다!

```python
def _get_rewards(self) -> torch.Tensor:
    forward_reward = self.robot.data.root_com_lin_vel_b[:,0].reshape(-1,1)
    alignment_reward = torch.sum(self.forwards * self.commands, dim=-1, keepdim=True)
    total_reward = forward_reward*torch.exp(alignment_reward)
    return total_reward
```

이제 학습 시, Jetbot들은 항상 명령 방향으로 전진해서 주행하도록 학습할 것이다!

![Directed results](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/walkthrough_directed_webp.webp)
