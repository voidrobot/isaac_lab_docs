# Isaac Lab - Newton Beta 2

Isaac Lab - Newton Beta 2 (feature/newton branch)는 Newton 물리 엔진 통합을 Isaac Lab에 제공합니다. 우리는 코드를 리팩토링하여 PhysX, Newton뿐만 아니라 다른 물리 엔진도 지원할 수 있도록 했으며, 이를 통해 사용자는 원하는 경우 Isaac Lab에 자체 물리 엔진을 가져올 수 있습니다. 이를 활성화하기 위해 `시뮬레이션 인터페이스`, `Articulation` 또는 [`ContactSensor`](../../api/lab/isaaclab.sensors.md#isaaclab.sensors.ContactSensor)의 기본 구현을 도입했습니다. 이들은 모든 물리 엔진이 구현해야 하는 추상 메서드 세트를 제공합니다. 결과적으로 모든 기본 Isaac Lab 환경이 어떤 물리 엔진과도 작동할 수 있게 됩니다. 이는 또한 Isaac Lab - Newton Beta 2가 Isaac Lab 2.X와 하위 호환성을 유지하도록 보장합니다. 엔진 별 호출의 경우, 사용자는 물리 엔진의 기본 뷰를 가져와서 엔진 별 API를 직접 호출할 수 있습니다.

하지만 코드를 리팩토링하는 과정에서 우리는 Isaac Lab의 오버헤드를 줄이는 방법도 모색하고 있습니다. 오버헤드를 최소화하기 위해, 우리는 저수준 코드를 torch에서 벗어나 warp에 의존하도록 이동하고 있습니다. 이를 통해 더 효율적인 저수준 코드를 작성하고 cuda-그래핑의 이점을 활용할 수 있습니다. 그러나 이로 인해 `ArticulationData` 또는 [`ContactSensorData`](../../api/lab/isaaclab.sensors.md#isaaclab.sensors.ContactSensorData)와 같은 `데이터 클래스`는 warp 배열만 반환하게 됩니다. 사용자는 필요 시 `wp.to_torch`를 호출하여 이를 토치 텐서로 변환해야 합니다. 우리의 세터/라이터는 warp 배열과 토치 텐서를 모두 지원하며, 내부적으로 warp 배열을 업데이트하기 위한 가장 최적의 전략을 사용합니다. 이는 Isaac Lab - Newton Beta 2로 마이그레이션하는 사용자에게 필요한 변경 사항의 양을 최소화합니다.

라이터와 세터의 또 다른 새로운 기능은 마스크와 전체 데이터를 제공할 수 있는 기능입니다(Isaac Lab 2.X의 인덱스와 부분 데이터와 대비됩니다). 이 기능은 인덱스와 부분 데이터를 제공하는 기능과 함께 제공되며, 기본 동작은 여전히 인덱스와 부분 데이터를 제공하는 것입니다. 그러나 warp를 사용하는 경우 사용자는 마스크와 전체 데이터를 제공해야 합니다. 일반적으로 이 새로운 기능을 채택하도록 권장합니다. 잘 구현된다면 런타임 메모리 할당을 줄이고 성능을 향상시킬 수 있기 때문입니다.

최적화 측면에서 우리는 쿼터니언 표기 방식을 변경하기로 결정했습니다. 원래 Isaac Lab과 Isaac Sim 모두 `wxyz` 표기 방식을 채택했습니다. 그러나 PhysX가 `xyzw` 표기 방식을 사용하기 때문에 우리 설정기/작성기에서 `xyzw`로의 여러 변환을 수행하고 있었습니다. 뉴턴과 워프도 모두 `xyzw` 표기 방식을 사용하기 때문에, 우리는 기본 표기 방식을 `xyzw`로 변경하기로 결정했습니다. 이는 이제 모든 API가 `xyzw` 표기 방식의 쿼터니언을 반환함을 의미합니다. 이는 우리 [`math`](../../api/lab/isaaclab.utils.md#module-isaaclab.utils.math) 모듈을 사용하지 않는 사용자 정의 mdps에 대해 가능성이 큰 breaking change일 수 있습니다. 이 변경은 상당하지만, 사용자가 시뮬레이션 뷰를 직접 사용할 때 일관성을 높이고 불필요한 변환을 제거하는 데 도움이 될 것입니다.

마지막으로, 새로운 `isaaclab_newton` 확장과 함께, 우리는 또한 새로운 `isaaclab_experimental` 및 `isaaclab_task_experimental` 확장을 도입하고 있습니다. 이 확장들은 핵심 Isaac Lab 확장에 완전히 통합되기 전에 성숙할 시간을 주면서 Isaac Lab 메인에 새로운 기능을 빠르게 제공할 수 있게 해줍니다. 이번 릴리스에서는 직접 RL 작업에 대한 cuda-그래핑 지원을 도입하고 있습니다. 이는 Isaac Lab의 오버헤드를 크게 줄여 학습 속도를 높입니다. 사용해 보시고 피드백을 공유해 주세요!

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Cartpole-Direct-Warp-v0 --num_envs 4096 --headless
```

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Ant-Direct-Warp-v0 --num_envs 4096 --headless
```

```bash
./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Humanoid-Direct-Warp-v0 --num_envs 4096 --headless
```

# 다음 단계는?

Isaac Lab 3.0은 Isaac Sim 6.0과 호환되며 동시에 새로운 Newton 물리 엔진을 지원할 예정인 Isaac Lab의 다가오는 릴리스입니다. 이를 통해 사용자는 Newton 물리 엔진 또는 PhysX에서 정책을 훈련할 수 있습니다. 이와 같은 주요 코드 리팩토링을 수용하기 위해 필요합니다. 이 섹션에서는 몇 가지 변경 사항, Isaac Lab 2.X 사용자에게 미치는 영향, 그리고 Isaac Lab 3.0으로 마이그레이션하는 방법을 살펴볼 것입니다. `feature/newton` 브랜치는 앞으로 나올 내용의 일부를 엿볼 수 있게 해줍니다. 내부 코드 구조의 변경은 상당하지만, 사용자 API의 변경은 최소한입니다.
