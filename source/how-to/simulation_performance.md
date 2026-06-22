# 시뮬레이션 성능 및 튜닝

시뮬레이션의 성능은 장면 내 객체 수, 물리 시뮬레이션 복잡도, 사용 중인 하드웨어 등 다양한 요인에 영향을 받을 수 있습니다. 성능을 개선하기 위한 몇 가지 팁은 다음과 같습니다:

1. **헤드리스 모드 사용**: 헤드리스 모드에서 시뮬레이션을 실행하면 렌더링이 필요하지 않을 경우 성능을 크게 향상시킬 수 있습니다. 헤드리스 모드를 활성화하려면 시뮬레이터 실행 시 `--headless` 플래그를 사용하세요.
2. **불필요한 충돌 방지**: 가능하면 객체 겹침 수를 줄여 시뮬레이션 오버헤드를 줄이세요. 시뮬레이션에서 과도한 접촉과 충돌은 충돌 단계에서 비용이 많이 들 수 있습니다.
3. **간소화된 물리 사용**: 성능 향상을 위해 간소화된 물리 충돌 기하학을 사용하거나 시뮬레이션 정확도를 낮추는 것을 고려하세요. 이는 자산을 수정하고 시뮬레이션 구성에서 물리 매개변수를 조정하여 수행할 수 있습니다.
4. **CPU/GPU 시뮬레이션 사용**: 장면이 몇 개의 관절 또는 강체만으로 구성된 경우, 더 나은 성능을 위해 CPU 시뮬레이션을 고려하세요. 더 큰 장면의 경우, GPU 시뮬레이션을 사용하면 성능을 크게 개선할 수 있습니다.

## 충돌 기하학

충돌 기하학은 시뮬레이션에서 객체의 모양을 정의하여 충돌 감지에 사용됩니다. 간소화된 충돌 기하학을 사용하면 성능을 향상시키고 시뮬레이션 복잡도를 줄일 수 있습니다.

예를 들어, 복잡한 메시가 있는 경우 메시 모양을 근사하는 간소화된 충돌 기하학을 만들 수 있습니다. 이는 Isaac Sim의 UI를 통해 충돌 메시를 수정하고 근사화 방법을 조정하여 수행할 수 있습니다.

또한, 훈련에 중요하지 않은 로봇의 특정 영역의 충돌 기하학을 제거할 수 있습니다. 예를 들어, Anymal-C 로봇에서는 무릎과 발의 충돌 기하학은 유지하지만, 성능 최적화를 위해 다리의 다른 부분의 충돌 기하학은 제거합니다.

구형과 같은 단순한 충돌 기하학은 복잡한 메시보다 더 나은 성능을 제공합니다. 예를 들어, SDF 메시 콜라이더는 단순한 구보다 더 비용이 많이 듭니다.

참고로, 원통형 및 원추형 충돌 기하학은 삼각형 메시와의 부드러운 충돌을 지원하여 휠 시뮬레이션 동작을 향상시키는 특수 지원이 있습니다. 그러나 이는 성능 비용을 수반하며 항상 바람직하지 않을 수 있습니다. 이 기능을 비활성화하려면 스테이지 설정에서 `--/physics/collisionApproximateCylinders=true` 및 `--/physics/collisionApproximateCones=true`를 설정할 수 있습니다.

GPU RL 워크로드에서 주의해야 할 또 다른 항목은 `Convex Hull` 근사 메시 충돌 기하학의 GPU 호환성과 관련된 경고입니다. 입력 메시가 높은 종횡비(예: 길고 얇은 형태)를 가지는 경우, 볼록 껍질 근사가 GPU 시뮬레이션과 호환되지 않을 수 있으며, 이로 인해 CPU 폴백이 트리거되어 성능에 큰 영향을 줄 수 있습니다.

CPU 폴백 경고는 다음과 같이 표시됩니다: `[Warning] [omni.physx.cooking.plugin] ConvexMeshCookingTask: failed to cook GPU-compatible mesh, collision detection will fall back to CPU. Collisions with particles and deformables will not work with this mesh.` 적절한 해결책으로는 경계 큐 근사로 전환하거나, 기하학이 동적 강체의 일부가 아닌 경우 정적 삼각형 메시 콜라이더를 사용하는 것이 있습니다.

## Linux에서 CPU governor 설정

CPU governor는 CPU의 작동 클럭 주파수 범위와 스케일링을 결정합니다. 이는 Isaac Sim 성능의 제한 요인이 될 수 있습니다. 최대 성능을 위해 CPU governor를 `performance`로 설정해야 합니다. CPU governor를 수정하려면 다음 명령을 실행하세요:

```bash
sudo apt-get install linux-tools-common
cpupower frequency-info # 사용 가능한 governor 확인
sudo cpupower frequency-set -g performance # root 권한으로 governor 설정
```

#### NOTE
모든 시스템에서 모든 governor를 사용할 수 있는 것은 아닙니다. 더 높은 클럭 속도를 지원하는 governor는 일반적으로 성능 중심이며 Isaac Sim에 더 나은 성능을 제공합니다.

## 추가 성능 가이드

시뮬레이션 성능을 “튜닝”하는 방법은 다양하지만, 선택하는 방법은 주로 시뮬레이션하려는 내용에 따라 달라집니다. 일반적으로 성능 향상을 찾기 위해 먼저 살펴봐야 할 곳은 [물리 엔진](https://docs.omniverse.nvidia.com/kit/docs/omni_physics/107.3/dev_guide/guides.html)입니다. 렌더링 및 딥 러닝 모델 실행 다음으로, 물리 엔진은 가장 계산 비용이 높은 요소입니다. 물리 시뮬레이션을 조정하여 관심 있는 작업에만 범위를 제한하는 것은 성능 향상을 찾기 위한 좋은 시작점이 될 수 있습니다.

우리는 최근 접촉과 그립 튜닝에 특화된 새로운 [그리퍼 튜닝 가이드](https://docs.omniverse.nvidia.com/kit/docs/omni_physics/107.3/dev_guide/guides/gripper_tuning_example.html)를 발표했습니다. 로봇 그리퍼를 사용할 계획이라면 먼저 이것을 확인하는 것이 좋습니다. 추가 세부 사항을 위해서는 다음 가이드들도 확인해 보세요!

* [Isaac Sim 성능 최적화 핸드북](https://docs.isaacsim.omniverse.nvidia.com/latest/reference_material/sim_performance_optimization_handbook.html)
* [Omni Physics 시뮬레이션 성능 가이드](https://docs.omniverse.nvidia.com/kit/docs/omni_physics/latest/dev_guide/guides/physics-performance.html)
