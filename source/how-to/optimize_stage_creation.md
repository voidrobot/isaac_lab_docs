# 단계 생성 최적화

Isaac Lab은 단계 생성을 가속화하는 두 가지 실험적 기능을 지원합니다: **패브릭 클로닝** 및 **메모리 내 단계**.
이러한 기능은 수천 개의 환경을 갖는 대형 RL 설정에 특히 효과적입니다.

## 이 기능이 하는 일

**패브릭 클로닝**

- 패브릭 라이브러리를 사용하여 환경을 클론합니다([USD 패브릭 USDRT 문서](https://docs.omniverse.nvidia.com/kit/docs/usdrt/latest/docs/usd_fabric_usdrt.html) 참고)
- 일부 환경에서는 기본적으로 부분적으로 지원 및 활성화됩니다(제한 사항 섹션의 목록 참고)

**메모리 내 단계**

- USD 파일이 아닌 메모리에 단계를 구성하여 디스크 I/O 오버헤드를 방지합니다
- 단계 생성 후 렌더링이 필요한 경우 단계가 USD 컨텍스트에 연결되어 기본 단계 구성으로 반환됩니다
- 기본적으로 활성화되지 않습니다

## 사용 예시

패브릭 클로닝은 [`isaaclab.scene.InteractiveSceneCfg.clone_in_fabric`](../api/lab/isaaclab.scene.md#isaaclab.scene.InteractiveSceneCfg.clone_in_fabric) 플래그를 설정하여 토글할 수 있습니다.

**RL 환경에서 패브릭 클로닝 사용**

```python
# 환경 구성 생성
env_cfg = CartpoleEnvCfg()
env_cfg.scene.clone_in_fabric = True
# RL 환경 설정
env = ManagerBasedRLEnv(cfg=env_cfg)
```

메모리 내 단계는 [`isaaclab.sim.SimulationCfg.create_stage_in_memory`](../api/lab/isaaclab.sim.md#isaaclab.sim.SimulationCfg.create_stage_in_memory) 플래그를 설정하여 토글할 수 있습니다.

**RL 환경에서 메모리 내 단계 사용**

```python
# 구성 생성 및 플래그 설정
cfg = CartpoleEnvCfg()
cfg.scene.num_envs = 1024
cfg.sim.create_stage_in_memory = True
# 메모리 내 단계를 사용하여 환경 생성
env = ManagerBasedRLEnv(cfg=cfg)
```

참고: 기존 RL 환경 클래스를 사용하지 않고 메모리 내 단계를 활성화하는 경우 추가 단계가 필요합니다.
단계 생성 단계는 [`with`](https://docs.python.org/3/reference/compound_stmts.html#with) 문으로 감싸서 단계 컨텍스트를 설정해야 합니다.
단계를 연결해야 하는 경우, 단계 생성 후 `attach_stage_to_usd_context()` 함수를 호출해야 합니다.

**수동 씬 설정에서 메모리 내 단계 사용**

```python
# 메모리 내 단계로 시뮬레이션 컨텍스트 초기화
sim = SimulationContext(cfg=SimulationCfg(create_stage_in_memory=True))

# 메모리 내 단계 가져오기 및 단계 컨텍스트 설정
stage_in_memory = sim.get_initial_stage()
with stage_utils.use_stage(stage_in_memory):
    # 카트폴 씬 생성
    scene_cfg = CartpoleSceneCfg(num_envs=1024)
    scene = InteractiveScene(scene_cfg)
    # 단계 생성 후 메모리에 단계 연결
    sim_utils.attach_stage_to_usd_context()

sim.play()
```

## 제한 사항

**패브릭 클로닝**

- 패브릭 클론된 환경은 USD 함수 대신 USDRT 함수를 사용하여 액세스해야 합니다.
- 패브릭 클로닝은 일부 환경에서 부분적으로 지원되며 기본적으로 활성화됩니다. 여기서는 그러한 환경 목록을 제공합니다.

```none
1.  Isaac-Ant-Direct-v0
2.  Isaac-Ant-v0
3.  Isaac-Cartpole-Direct-v0
4.  Isaac-Cartpole-Showcase-Box-Box-Direct-v0
5.  Isaac-Cartpole-Showcase-Box-Discrete-Direct-v0
6.  Isaac-Cartpole-Showcase-Box-MultiDiscrete-Direct-v0
7.  Isaac-Cartpole-Showcase-Dict-Box-Direct-v0
8.  Isaac-Cartpole-Showcase-Dict-Discrete-Direct-v0
9.  Isaac-Cartpole-Showcase-Dict-MultiDiscrete-Direct-v0
10. Isaac-Cartpole-Showcase-Discrete-Box-Direct-v0
11. Isaac-Cartpole-Showcase-Discrete-Discrete-Direct-v0
12. Isaac-Cartpole-Showcase-Discrete-MultiDiscrete-Direct-v0
13. Isaac-Cartpole-Showcase-MultiDiscrete-Box-Direct-v0
14. Isaac-Cartpole-Showcase-MultiDiscrete-Discrete-Direct-v0
15. Isaac-Cartpole-Showcase-MultiDiscrete-MultiDiscrete-Direct-v0
16. Isaac-Cartpole-Showcase-Tuple-Box-Direct-v0
17. Isaac-Cartpole-Showcase-Tuple-Discrete-Direct-v0
18. Isaac-Cartpole-Showcase-Tuple-MultiDiscrete-Direct-v0
19. Isaac-Cartpole-v0
20. Isaac-Factory-GearMesh-Direct-v0
21. Isaac-Factory-NutThread-Direct-v0
22. Isaac-Factory-PegInsert-Direct-v0
23. Isaac-Franka-Cabinet-Direct-v0
24. Isaac-Humanoid-Direct-v0
25. Isaac-Humanoid-v0
26. Isaac-Quadcopter-Direct-v0
27. Isaac-Repose-Cube-Allegro-Direct-v0
28. Isaac-Repose-Cube-Allegro-NoVelObs-v0
29. Isaac-Repose-Cube-Allegro-v0
30. Isaac-Repose-Cube-Shadow-Direct-v0
31. Isaac-Repose-Cube-Shadow-OpenAI-FF-Direct-v0
32. Isaac-Repose-Cube-Shadow-OpenAI-LSTM-Direct-v0
```

**메모리 내 단계**

- 현재 **패브릭 클로닝**과 동시에 활성화할 수 없습니다.
- 메모리 내 단계를 USD 컨텍스트에 연결하는 것은 느릴 수 있어 일부 또는 전체 성능 이점을 상쇄할 수 있습니다.
  - 참고: 연결은 렌더링이 활성화된 경우에만 필요합니다. 예를 들어, 헤드리스 모드에서는 연결이 필요하지 않습니다.
- 일부 저수준 Kit API는 아직 메모리 내 단계를 지원하지 않습니다.
  - 대부분의 경우 이러한 API가 호출되면 기존 스크립트가 자동으로 단계 초기 연결을 수행하고 경고 메시지를 출력합니다.
  - 특정 경우, 일부 환경에서 메모리 내 단계가 활성화된 경우 지면 평면을 색칠하는 API 호출이 생략됩니다.

## 벤치마크 결과

렌더링이 활성화된 상태에서 4000개의 ShadowHand 로봇 클로닝 성능 비교

|   테스트 # | 메모리 내 단계   | 패브릭 내 클로닝   | 단계 연결 시간 (초)   |   패브릭 연결 시간 (초) |   클론 경로 시간 (초) |   첫 단계 시간 (초) |
|----------|-------------------|-------------------|-------------------------|--------------------------|------------------------|-----------------------|
|        1 | 예               | 예               | 3.88                    |                     0.15 |                   4.84 |                  1.39 |
|        2 | 아니오           | 아니오           | —                       |                    60.17 |                   4.46 |                  3.52 |
|        3 | 아니오           | 예               | —                       |                     0.47 |                   4.72 |                  2.56 |
|        4 | 예               | 아니오           | 42.64                   |                    21.75 |                   1.87 |                  2.16 |
