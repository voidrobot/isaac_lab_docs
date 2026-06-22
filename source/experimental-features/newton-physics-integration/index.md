# 뉴턴 물리 통합

[Newton](https://newton-physics.github.io/newton/guide/overview.html)은 로봇공학, 연구, 고급 시뮬레이션 워크플로우를 위한 GPU 가속화, 확장 가능하고 미분 가능한 물리 시뮬레이션 엔진입니다. [NVIDIA Warp](https://nvidia.github.io/warp/)를 기반으로 구축되고 MuJoCo Warp와 통합된 Newton은 높은 성능의 시뮬레이션, 최신 Python API, 사용자와 개발자를 위한 유연한 아키텍처를 제공합니다.

Newton은 NVIDIA, Google Deep Mind, Disney Research의 기여를 통해 커뮤니티 주도로 진행되는 오픈 소스 프로젝트이며 Linux Foundation을 통해 관리됩니다.

이 [실험적 기능 브랜치](https://github.com/isaac-sim/IsaacLab/tree/feature/newton)의 Isaac Lab은 Newton 물리 엔진과의 초기 통합을 제공하며 현재 활발히 개발 중입니다. 많은 기능이 아직 지원되지 않으며, 현재는 제한된 클래식 RL 및 평평한 지형에서의 이동 보강 학습 예시만 포함되어 있습니다.

이 Isaac Lab 통합 브랜치와 Newton 자체 모두 현재 활발히 개발 중입니다. 향후 다른 강화 학습 및 모방 학습 워크플로우에 대한 추가 기능을 지원할 계획이지만, 위에 언급된 작업들을 통해 Isaac Lab에서 Newton 통합이 어떻게 작동하는지를 이해하는 데 좋은 참고 자료가 될 것입니다.

우리는 Newton에서 PhysX로, 그리고 PhysX에서 Newton으로 학습된 정책을 전이시켜 Newton 시뮬레이션을 PhysX와 비교하여 검증했습니다. 또한, Newton으로 훈련된 이동 정책을 G1 로봇에 성공적으로 배포했습니다. 더 자세한 내용은 [여기](sim-to-real.md#sim2real)를 참조하세요.

Newton은 [다양한 솔버](https://newton-physics.github.io/newton/api/newton_solvers.html)를 지원하여 다양한 유형의 물리 시뮬레이션을 처리할 수 있지만, 현재 Isaac Lab 통합에서는 주로 MuJoCo-Warp 솔버에 초점을 맞추고 있습니다.

이 브랜치와 Newton의 미래 업데이트에는 성능에 대한 지속적인 개선뿐만 아니라 추가 솔버와의 통합도 포함될 예정입니다.

이 브랜치는 PhysX 물리 엔진을 지원하지 않음을 주의하세요 - 현재는 Newton만 지원됩니다. Lab 내에서 PhysX 지원을 유지하기 위한 여러 가능한 경로를 검토하고 있으며, 사용자들의 피드백을 환영합니다.

Newton 및 이 Isaac Lab 통합의 초기 개발 단계에서는 breaking changes와 제한된 문서를 만나실 수 있습니다. 프레임워크가 공식 릴리스에 도달할 때까지 공식 지원이나 디버깅 지원을 제공할 수 있을 것으로 기대하지 않습니다. 우리가 강력하고 완성도 높은 프레임워크를 제공하기 위해 노력하는 동안 여러분의 이해와 인내심을 부탁드립니다.

* [설치](installation.md)
* [Isaac Lab - Newton Beta 2](isaaclab_newton-beta-2.md)
* [다음은?](isaaclab_newton-beta-2.md#what-s-next)
* [훈련 환경](training-environments.md)
* [시각화](visualization.md)
* [제한 사항](limitations-and-known-bugs.md)
* [솔버 전환](solver-transitioning.md)
* [Sim-to-Sim 정책 전이](sim-to-sim.md)
* [Sim-to-Real 정책 전이](sim-to-real.md)
