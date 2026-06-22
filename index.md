# Isaac Lab에 오신 것을 환영합니다!

![Isaac Lab을 사용한 H1 휴머노이드 예시](source/_static/isaaclab.jpg)

**Isaac Lab**은 로봇 학습에서 일반적인 워크플로우(강화 학습, 시연을 통한 학습, 움직임 계획 등)를 단순화하기 위해 설계된 통합적이고 모듈식 프레임워크입니다. 최신 시뮬레이션 기능을 활용하기 위해 [NVIDIA Isaac Sim](https://docs.isaacsim.omniverse.nvidia.com/latest/index.html) 위에 구축되어 사진처럼 현실적인 장면을 위한 고급 시뮬레이션과 빠른, 효율적인 시뮬레이션을 제공합니다.

프레임워크의 핵심 목표는 다음과 같습니다:

- **모듈성**: 새로운 환경, 로봇, 센서를 쉽게 커스터마이징하고 추가할 수 있습니다.
- **민첩성**: 커뮤니티의 변화하는 요구에 적응할 수 있습니다.
- **개방성**: 오픈 소스로 유지하여 커뮤니티가 프레임워크에 기여하고 확장할 수 있도록 합니다.
- **포함된 배터리**: 바로 사용할 수 있는 다양한 환경, 센서, 작업을 포함합니다.

Isaac Lab에서 제공하는 주요 기능에는 PhysX가 제공하는 빠른 정확한 물리 시뮬레이션, 벡터화된 렌더링을 위한 타일 렌더링 API, robustness와 적응성을 향상시키는 도메인 랜덤화, 클라우드에서 실행할 수 있는 지원이 포함됩니다.

또한, Isaac Lab은 다양한 환경을 제공하며, 목록에 더 많은 환경을 추가하기 위해 actively 작업 중입니다. 여기에는 classic control 작업, 고정 팔 및 유연한 조작 작업, 다리 달린 로보트의 이동 작업, 탐색 작업이 포함됩니다. 전체 목록은 [environments](source/overview/environments) 섹션에서 확인할 수 있습니다.

Isaac Lab은 이제 플랫폼의 일부로 **Batteries-included** 로 준비된 특정 로봇 자산으로 개발되었습니다. 학습 준비가 완료된 로봇은 다음과 같습니다…

- **클래식**: Cartpole, Humanoid, Ant
- **고정 팔과 손**: UR10, Franka, Allegro, Shadow Hand
- **사족 보행 로봇**: Anybotics Anymal-B, Anymal-C, Anymal-D, Unitree A1, Unitree Go1, Unitree Go2, Boston Dynamics Spot
- **휴머노이드**: Unitree H1, Unitree G1
- **쿼드콥터**: Crazyflie

플랫폼은 또한 사용자가 자체 로봇을 추가할 수 있도록 설계되었습니다. 자세한 내용은 [How-to Guides](source/how-to/index.md#how-to) 섹션을 참조하세요.

프레임워크에 대한 자세한 내용은 [기술 보고서](https://arxiv.org/abs/2511.04831) [[MRT+25](source/refs/bibliography.md#id16)]를 참조하세요. NVIDIA Isaac 생태계에 대한 설명은 [Isaac Lab Ecosystem](source/setup/ecosystem.md#isaac-lab-ecosystem) 섹션을 확인하세요.

![Isaac Lab으로 생성된 예시 작업](source/_static/tasks.jpg)

# 라이선스

Isaac Lab 프레임워크는 BSD-3-Clause 라이선스로 오픈 소스로 제공되며, 특정 부분은 Apache-2.0 라이선스로 제공됩니다. 자세한 내용은 [License](source/refs/license.md#license)를 참조하세요.

# 인용

연구에서 Isaac Lab을 사용하는 경우, 다음 기술 보고서를 인용해 주세요:

```bibtex
@article{mittal2025isaaclab,
   title={Isaac Lab: A GPU-Accelerated Simulation Framework for Multi-Modal Robot Learning},
   author={Mayank Mittal and Pascal Roth and James Tigue and Antoine Richard and Octi Zhang and Peter Du and Antonio Serrano-Muñoz and Xinjie Yao and René Zurbrügg and Nikita Rudin and Lukasz Wawrzyniak and Milad Rakhsha and Alain Denzler and Eric Heiden and Ales Borovicka and Ossama Ahmed and Iretiayo Akinola and Abrar Anwar and Mark T. Carlson and Ji Yuan Feng and Animesh Garg and Renato Gasoto and Lionel Gulich and Yijie Guo and M. Gussert and Alex Hansen and Mihir Kulkarni and Chenran Li and Wei Liu and Viktor Makoviychuk and Grzegorz Malczyk and Hammad Mazhar and Masoud Moghani and Adithyavairavan Murali and Michael Noseworthy and Alexander Poddubny and Nathan Ratliff and Welf Rehberg and Clemens Schwarke and Ritvik Singh and James Latham Smith and Bingjie Tang and Ruchik Thaker and Matthew Trepte and Karl Van Wyk and Fangzhou Yu and Alex Millane and Vikram Ramasamy and Remo Steiner and Sangeeta Subramanian and Clemens Volk and CY Chen and Neel Jawale and Ashwin Varghese Kuruttukulam and Michael A. Lin and Ajay Mandlekar and Karsten Patzwaldt and John Welsh and Huihua Zhao and Fatima Anes and Jean-Francois Lafleche and Nicolas Moënne-Loccoz and Soowan Park and Rob Stepinski and Dirk Van Gelder and Chris Amevor and Jan Carius and Jumyung Chang and Anka He Chen and Pablo de Heras Ciechomski and Gilles Daviet and Mohammad Mohajerani and Julia von Muralt and Viktor Reutskyy and Michael Sauter and Simon Schirm and Eric L. Shi and Pierre Terdiman and Kenny Vilella and Gordon Yeoman and Tiffany Chen and Sergey Grizan and Cathy Li and Lotus Li and Connor Smith and Rafael Wiltz and Kostas Alexis and Yan Chang and David Chu and Linxi "Jim" Fan and Farbod Farshidian and Ankur Handa and Spencer Huang and Marco Hutter and Yashraj Narang and Soha Pouya and Shiwei Sheng and Yuke Zhu and Miles Macklin and Adam Moravanszky and Philipp Reist and Yunrong Guo and David Hoeller and Gavriel State},
   journal={arXiv preprint arXiv:2511.04831},
   year={2025},
   url={https://arxiv.org/abs/2511.04831}
}
```

# 감사의 말

Isaac Lab 개발은 [Orbit](https://isaac-orbit.github.io/) 프레임워크에서 시작되었습니다. Orbit의 authors에게 기초적인 기여에 감사드립니다.

# 목차

# Isaac Lab

* [Isaac Lab 생태계](source/setup/ecosystem.md)
* [로컬 설치](source/setup/installation/index.md)
* [컨테이너 배포](source/deployment/index.md)
* [클라우드 배포](source/setup/installation/cloud_installation.md)
* [참조 아키텍처](source/refs/reference_architecture/index.md)

# 시작하기

* [빠른 시작 가이드](source/setup/quickstart.md)
* [나만의 프로젝트 또는 작업 빌드하기](source/overview/own-project/index.md)
  * [새 프로젝트 또는 작업 생성](source/overview/own-project/template.md)
  * [프로젝트 구조](source/overview/own-project/project_structure.md)
* [워크스루(안내서)](source/setup/walkthrough/index.md)
  * [환경 디자인 배경](source/setup/walkthrough/concepts_env_design.md)
  * [클래스 및 구성(Configs)](source/setup/walkthrough/api_env_design.md)
  * [환경 디자인](source/setup/walkthrough/technical_env_design.md)
  * [Jetbot 학습: Ground Truth](source/setup/walkthrough/training_jetbot_gt.md)
  * [RL(강화학습) 문제 탐색](source/setup/walkthrough/training_jetbot_reward_exploration.md)
* [튜토리얼](source/tutorials/index.md)
  * [빈 씬(Scene) 생성하기](source/tutorials/00_sim/create_empty.md)
  * [씬에 Prim 생성(Spawn)하기](source/tutorials/00_sim/spawn_prims.md)
  * [AppLauncher 심층 분석](source/tutorials/00_sim/launch_app.md)
  * [Isaac Lab에 새로운 로봇 추가하기](source/tutorials/01_assets/add_new_robot.md)
  * [강체(Rigid Object)와 상호작용하기](source/tutorials/01_assets/run_rigid_object.md)
  * [관절체(Articulation)와 상호작용하기](source/tutorials/01_assets/run_articulation.md)
  * [변형 가능한 물체(Deformable Object)와 상호작용하기](source/tutorials/01_assets/run_deformable_object.md)
  * [서페이스 그리퍼(Surface Gripper)와 상호작용하기](source/tutorials/01_assets/run_surface_gripper.md)
  * [대화형 씬(Interactive Scene) 사용하기](source/tutorials/02_scene/create_scene.md)
  * [매니저 기반 기본 환경 생성하기](source/tutorials/03_envs/create_manager_base_env.md)
  * [매니저 기반 RL 환경 생성하기](source/tutorials/03_envs/create_manager_rl_env.md)
  * [직접 워크플로우(Direct Workflow) RL 환경 생성하기](source/tutorials/03_envs/create_direct_rl_env.md)
  * [환경 등록하기](source/tutorials/03_envs/register_rl_env_gym.md)
  * [RL 에이전트로 학습하기](source/tutorials/03_envs/run_rl_training.md)
  * [RL 에이전트 설정하기](source/tutorials/03_envs/configuring_rl_training.md)
  * [기존 직접 워크플로우 RL 환경 수정하기](source/tutorials/03_envs/modify_direct_rl_env.md)
  * [USD 환경에서의 정책 추론(Policy Inference)](source/tutorials/03_envs/policy_inference_in_usd.md)
  * [로봇에 센서 추가하기](source/tutorials/04_sensors/add_sensors_on_robot.md)
  * [작업 공간 컨트롤러(Task-space Controller) 사용하기](source/tutorials/05_controllers/run_diff_ik.md)
  * [운용 공간 컨트롤러(Operational Space Controller) 사용하기](source/tutorials/05_controllers/run_osc.md)
* [하우투(How-to) 가이드](source/how-to/index.md)
  * [새로운 에셋 임포트하기](source/how-to/import_new_asset.md)
  * [에셋 구성(Configuration) 작성하기](source/how-to/write_articulation_cfg.md)
  * [시뮬레이션에서 물리 Prim을 고정(Fixed)하기](source/how-to/make_fixed_prim.md)
  * [여러 에셋 생성(Spawn)하기](source/how-to/multi_asset_spawning.md)
  * [렌더링된 이미지 저장 및 3D 재투영](source/how-to/save_camera_output.md)
  * [학습에 필요한 카메라 개수 및 종류 파악하기](source/how-to/estimate_how_many_cameras_can_run.md)
  * [렌더링 설정 구성하기](source/how-to/configure_rendering.md)
  * [시각화 마커 생성하기](source/how-to/draw_markers.md)
  * [환경 래핑(Wrapping)하기](source/how-to/wrap_rl_env.md)
  * [나만의 학습 라이브러리 추가하기](source/how-to/add_own_library.md)
  * [시뮬레이션 애니메이션 녹화하기](source/how-to/record_animation.md)
  * [학습 중 비디오 클립 녹화하기](source/how-to/record_video.md)
  * [커리큘럼 유틸리티](source/how-to/curriculums.md)
  * [로보틱스를 위한 옴니버스(Omniverse) 마스터하기](source/how-to/master_omniverse.md)
  * [CloudXR 원격 조작 설정하기](source/how-to/cloudxr_teleoperation.md)
  * [Haply 원격 조작 설정하기](source/how-to/haply_teleoperation.md)
  * [시뮬레이션 성능 및 튜닝](source/how-to/simulation_performance.md)
  * [스테이지 생성 최적화하기](source/how-to/optimize_stage_creation.md)
* [개발자 가이드](source/overview/developer-guide/index.md)
  * [Visual Studio Code 설정하기](source/overview/developer-guide/vs_code.md)
  * [Python 인터프리터 설정하기](source/overview/developer-guide/vs_code.md#configuring-the-python-interpreter)
  * [포맷팅 및 린팅(Linting) 설정하기](source/overview/developer-guide/vs_code.md#setting-up-formatting-and-linting)
  * [저장소 구조](source/overview/developer-guide/repo_structure.md)
  * [익스텐션(Extension) 개발](source/overview/developer-guide/development.md)

# 개요

* [핵심 개념](source/overview/core-concepts/index.md)
  * [작업 디자인 워크플로](source/overview/core-concepts/task_workflows.md)
  * [액추에이터](source/overview/core-concepts/actuators.md)
  * [센서](source/overview/core-concepts/sensors/index.md)
    * [카메라](source/overview/core-concepts/sensors/camera.md)
    * [접촉 센서](source/overview/core-concepts/sensors/contact_sensor.md)
    * [프레임 트랜스포머](source/overview/core-concepts/sensors/frame_transformer.md)
    * [관성 측정 장치 (IMU)](source/overview/core-concepts/sensors/imu.md)
    * [레이 캐스터](source/overview/core-concepts/sensors/ray_caster.md)
    * [비전-촉각 센서](source/overview/core-concepts/sensors/visuo_tactile_sensor.md)
  * [모션 제네레이터](source/overview/core-concepts/motion_generators.md)
* [사용 가능한 환경](source/overview/environments.md)
* [환경 종합 목록](source/overview/environments.md#comprehensive-list-of-environments)
* [강화 학습](source/overview/reinforcement-learning/index.md)
  * [강화 학습 스크립트](source/overview/reinforcement-learning/rl_existing_scripts.md)
  * [강화 학습 라이브러리 비교](source/overview/reinforcement-learning/rl_frameworks.md)
  * [성능 벤치마크](source/overview/reinforcement-learning/performance_benchmarks.md)
  * [디버깅 및 훈련 가이드](source/overview/reinforcement-learning/training_guide.md)
* [모방 학습](source/overview/imitation-learning/index.md)
  * [Isaac Lab Mimic을 사용한 텔레오퍼레이션 및 모방 학습](source/overview/imitation-learning/teleop_imitation.md)
  * [증강 모방 학습](source/overview/imitation-learning/augmented_imitation.md)
  * [SkillGen 자동 데모 생성](source/overview/imitation-learning/skillgen.md)
* [쇼룸 데모](source/overview/showroom.md)
* [간단한 에이전트](source/overview/simple_agents.md)

# 기능

* [Hydra 구성 시스템](source/features/hydra.md)
  * [고급 파라미터 수정](source/features/hydra.md#modifying-advanced-parameters)
  * [상호 의존적 파라미터 수정](source/features/hydra.md#modifying-inter-dependent-parameters)
* [멀티 GPU 및 멀티 노드 훈련](source/features/multi_gpu.md)
  * [멀티 GPU 훈련](source/features/multi_gpu.md#multi-gpu-training)
  * [멀티 노드 훈련](source/features/multi_gpu.md#multi-node-training)
* [인구 기반 훈련](source/features/population_based_training.md)
  * [PBT의 기능](source/features/population_based_training.md#what-pbt-does)
  * [리더 / 언더퍼포머 선택](source/features/population_based_training.md#leader-underperformer-selection)
  * [변이 (하이퍼파라미터)](source/features/population_based_training.md#mutation-hyperparameters)
  * [예제 구성](source/features/population_based_training.md#example-config)
  * [PBT 실행](source/features/population_based_training.md#launching-pbt)
  * [팁](source/features/population_based_training.md#tips)
  * [훈련 예제](source/features/population_based_training.md#training-example)
  * [참조](source/features/population_based_training.md#references)
* [타일 렌더링](source/overview/core-concepts/sensors/camera.md)
  * [타일 렌더링](source/overview/core-concepts/sensors/camera.md#tiled-rendering)
  * [어노테이터](source/overview/core-concepts/sensors/camera.md#annotators)
  * [RGB 및 RGBA](source/overview/core-concepts/sensors/camera.md#rgb-and-rgba)
  * [깊이 및 거리](source/overview/core-concepts/sensors/camera.md#depth-and-distances)
  * [노말](source/overview/core-concepts/sensors/camera.md#normals)
  * [모션 벡터](source/overview/core-concepts/sensors/camera.md#motion-vectors)
  * [의미론적 세그멘테이션](source/overview/core-concepts/sensors/camera.md#semantic-segmentation)
  * [인스턴스 ID 세그멘테이션](source/overview/core-concepts/sensors/camera.md#instance-id-segmentation)
* [레이 잡 디스패치 및 튜닝](source/features/ray.md)
  * [개요](source/features/ray.md#overview)
  * [Docker 기반 로컬 퀵스타트](source/features/ray.md#docker-based-local-quickstart)
  * [원격 클러스터](source/features/ray.md#remote-clusters)
* [재현 가능성 및 결정론](source/features/reproducibility.md)

# 실험적 기능

* [최신 기능 소개](source/experimental-features/bleeding-edge.md)
* [뉴턴 물리 통합](source/experimental-features/newton-physics-integration/index.md)
  * [설치](source/experimental-features/newton-physics-integration/installation.md)
    * [Pip 설치](source/experimental-features/newton-physics-integration/installation.md#pip-installation)
    * [설치 테스트](source/experimental-features/newton-physics-integration/installation.md#testing-the-installation)
  * [Isaac Lab - 뉴턴 베타 2](source/experimental-features/newton-physics-integration/isaaclab_newton-beta-2.md)
  * [다음 단계는?](source/experimental-features/newton-physics-integration/isaaclab_newton-beta-2.md#what-s-next)
  * [훈련 환경](source/experimental-features/newton-physics-integration/training-environments.md)
  * [시각화](source/experimental-features/newton-physics-integration/visualization.md)
    * [개요](source/experimental-features/newton-physics-integration/visualization.md#overview)
    * [퀵 스타트](source/experimental-features/newton-physics-integration/visualization.md#quick-start)
    * [시각화 백엔드](source/experimental-features/newton-physics-integration/visualization.md#visualizer-backends)
    * [성능 주의사항](source/experimental-features/newton-physics-integration/visualization.md#performance-note)
    * [제한 사항](source/experimental-features/newton-physics-integration/visualization.md#limitations)
  * [제한 사항](source/experimental-features/newton-physics-integration/limitations-and-known-bugs.md)
  * [솔버 전환](source/experimental-features/newton-physics-integration/solver-transitioning.md)
  * [시뮬레이션-시뮬레이션 정책 전송](source/experimental-features/newton-physics-integration/sim-to-sim.md)
    * [개요](source/experimental-features/newton-physics-integration/sim-to-sim.md#overview)
  * [시뮬레이션-실제 정책 전송](source/experimental-features/newton-physics-integration/sim-to-real.md)
    * [요구사항](source/experimental-features/newton-physics-integration/sim-to-real.md#requirements)
    * [1. 교사 정책 훈련](source/experimental-features/newton-physics-integration/sim-to-real.md#train-the-teacher-policy)
    * [2. 학생 정책 증류 (특권 항목 제거)](source/experimental-features/newton-physics-integration/sim-to-real.md#distill-the-student-policy-remove-privileged-terms)
    * [3. RL로 학생 정책 미세 조정](source/experimental-features/newton-physics-integration/sim-to-real.md#fine-tune-the-student-policy-with-rl)

# 자료

* [클라우드 배포](source/setup/installation/cloud_installation.md)
* [Isaac Lab에서 훈련된 정책의 Sim2Real 배포](source/policy_deployment/index.md)

# 마이그레이션 가이드

* [IsaacGymEnvs에서 마이그레이션](source/migration/migrating_from_isaacgymenvs.md)
* [OmniIsaacGymEnvs에서 마이그레이션](source/migration/migrating_from_omniisaacgymenvs.md)
* [Orbit에서 마이그레이션](source/migration/migrating_from_orbit.md)

# 소스 API

* [API 참조](source/api/index.md)

# 참조

* [추가 자료](source/refs/additional_resources.md)
* [기여 가이드라인](source/refs/contributing.md)
* [팁과 문제 해결](source/refs/troubleshooting.md)
* [마이그레이션 가이드 (Isaac Sim)](source/refs/migration.md)
* [알려진 문제](source/refs/issues.md)
* [릴리스 노트](source/refs/release_notes.md)
* [확장 changelog](source/refs/changelog.md)
* [라이선스](source/refs/license.md)
* [참고문헌](source/refs/bibliography.md)

# 색인 및 테이블

* [색인](genindex.md)
* [모듈 인덱스](py-modindex.md)
* [검색 페이지](search.md)
