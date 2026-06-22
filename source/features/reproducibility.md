# 재현 가능성과 결정론

동일한 하드웨어 및 Isaac Sim(그리고 따라서 PhysX) 버전이 주어졌을 때, 강체와 관절체가 있는 장면에서는 시뮬레이션이 동일한 결과를 생성합니다. 그러나 하드웨어 구성의 차이로 인해 부동소수점 정밀도와 반올림 오류로 인해 시뮬레이션 결과가 달라질 수 있습니다. 현재 PhysX는 천이나 소프트 바디와 같은 비강체가 포함된 모든 장면에 대해 결정론을 보장하지 않습니다. 자세한 내용은 [PhysX Determinism 문서](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/docs/API.html#determinism)를 참조하십시오.

위와 같이, Isaac Lab은 결정론적 시뮬레이션을 제공하여 다양한 실행 간에 일관된 시뮬레이션 결과를 보장합니다. 이는 시뮬레이션 환경과 물리 엔진에 동일한 랜덤 시드를 사용함으로써 달성됩니다. 환경이 구성될 때, 랜덤 시드는 [`configure_seed()`](../api/lab/isaaclab.utils.md#isaaclab.utils.seed.configure_seed) 메서드를 사용하여 고정된 값으로 설정됩니다. 이 메서드는 PyTorch와 NumPy를 포함한 다양한 라이브러리에서 CPU와 GPU 전반에 걸쳐 랜덤 시드를 설정합니다.

포함된 워크플로 스크립트에서는 학습 에이전트의 구성 파일이나 명령줄 인수에 지정된 시드를 사용하여 환경의 랜덤 시드를 설정합니다. 이를 통해 시뮬레이션 결과가 다양한 실행 간에 재현 가능하도록 보장합니다. 시드는 관리자 기반 또는 직접 환경 구현에 따라 환경 매개변수 [`isaaclab.envs.ManagerBasedEnvCfg.seed`](../api/lab/isaaclab.envs.md#isaaclab.envs.ManagerBasedEnvCfg.seed) 또는 [`isaaclab.envs.DirectRLEnvCfg.seed`](../api/lab/isaaclab.envs.md#isaaclab.envs.DirectRLEnvCfg.seed)에 설정됩니다.

RL 훈련에서의 결정성 테스트 결과에 대해서는 GitHub Pull Request [#940](https://github.com/isaac-sim/IsaacLab/pull/940)를 확인하시기 바랍니다.
