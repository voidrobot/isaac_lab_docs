# 추가 자료

여기서는 여러분이 유용하게 사용할 수 있는 도구 및 다양한 자료에 대한 외부 링크를 제공합니다.

## 시뮬레이션에서 실제 적용(SIM-to-REAL) 자료

Isaac 프로젝트의 핵심 목표 중 하나는 NVIDIA 기술의 힘을 통해 실제 로봇을 활성화하는 것입니다. 이를 달성하는 방법은 다양하며, 따라서 사용할 수 있는 도구도 많습니다. 이러한 자료는 예시와 토론을 통해 시뮬레이션에서 실제 적용 간극을 좁히고 실제 로봇에 정책을 배포하는 데 도움을 주기 위해 마련되었습니다.

* [Closing the Sim-to-Real Gap: Training Spot Quadruped Locomotion with NVIDIA Isaac Lab](https://developer.nvidia.com/blog/closing-the-sim-to-real-gap-training-spot-quadruped-locomotion-with-nvidia-isaac-lab/)은 보스턴 다이나믹스의 Spot 사족보행 로봇에 대한 보행 정책을 훈련하고 실제 로봇에 배포하는 상세한 가이드입니다.

## LLM 생성 보상 함수

우리의 연구 프로젝트인 `Eureka!`는 LLM을 사용하여 강화 학습(RL) 보상 함수를 생성하고 튜닝하는 파이프라인을 개발했습니다. 이러한 자료는 이 파이프라인을 활용하여 기존에 불가능하다고 여겨졌던 작업을 RL 기반 솔루션으로 구현하는 데 도움을 주기 위해 마련되었습니다.

* [Isaac Lab Eureka](https://github.com/isaac-sim/IsaacLabEureka)는 Isaac Lab에서 구축한 직접적인 RL 환경에 대해 LLM 기반 보상 생성 파이프라인을 설정할 수 있는 GitHub 저장소입니다.
* [Eureka! NVIDIA Research Breakthrough Puts New Spin on Robot Learning](https://blogs.nvidia.com/blog/eureka-robotics-research/)은 이 보상 생성 과정의 전반적인 아이디어를 다루는 블로그 게시물입니다.

## 시뮬레이션 기능

Isaac Lab의 핵심은 Isaac Sim으로, 이는 로봇공학 전반에 유용한 기능이 풍부한 도구이며 RL에만 국한되지 않습니다. 시뮬레이션에 대한 이해가 깊어질수록 해당 기능을 프로젝트 및 애플리케이션에 효과적으로 활용할 수 있습니다. 이러한 자료는 Isaac Lab에 특화된 관심사를 바탕으로 시뮬레이션의 다른 유용한 기능에 대해 알리기 위해 마련되었습니다.

* [Simulation Performance Guide](https://docs.omniverse.nvidia.com/kit/docs/omni_physics/latest/dev_guide/guides/physics-performance.html)는 OmniPhysics에서 최상의 시뮬레이션 성능을 달성하기 위한 모범 사례 가이드입니다.
* [Deploying Policies in Isaac Sim](https://docs.isaacsim.omniverse.nvidia.com/latest/isaac_lab_tutorials/tutorial_policy_deployment.html)은 Isaac Sim 내에서 훈련된 정책을 사용하는 방법을 다루는 Isaac Sim 튜토리얼입니다.
* [Supercharge Robotics Workflows with AI and Simulation Using NVIDIA Isaac Sim 4.0 and NVIDIA Isaac Lab](https://developer.nvidia.com/blog/supercharge-robotics-workflows-with-ai-and-simulation-using-nvidia-isaac-sim-4-0-and-nvidia-isaac-lab/)은 Isaac Sim 4.0의 최신 기능을 다루는 블로그 게시물로, `pip install`, 보다 고급 물리 엔진, 업데이트된 센서 시뮬레이션 등이 포함됩니다.
* [Fast-Track Robot Learning in Simulation Using NVIDIA Isaac Lab](https://developer.nvidia.com/blog/fast-track-robot-learning-in-simulation-using-nvidia-isaac-lab/)은 Isaac Lab을 통한 가속화된 로봇 학습의 다양한 기능을 다루는 블로그 게시물입니다.
