# 제한 사항

Newton과 이 Isaac Lab 통합의 초기 개발 단계에서는 중단되는 변경 사항과 제한된 문서를 만나기 쉽습니다.

프레임워크가 공식 릴리스에 도달하기 전에는 지원이나 디버깅 지원을 제공할 수 없습니다.

다음은 Newton 실험 기능 브랜치에서 현재 지원되는 기능 목록이며, 확장별로 그룹화하여 나열한 비포괄적 목록입니다.

* isaaclab:
  : * Articulation API (강체로서의 관절 및 단일 몸체 관절 모두 지원)
    * Contact Sensor
    * 직접 및 관리자 단일 에이전트 워크플로
    * Omniverse Kit 시각화 도구
    * Newton 시각화 도구
* isaaclab_assets:
  : * 사족 보행 로봇
      : * Anymal-B, Anymal-C, Anymal-D
        * Unitree A1, Go1, Go2
        * Spot
    * 휴머노이드
      : * Unitree H1 & G1
        * Cassie
    * 팔과 손
      : * Franka
        * UR10
        * Allegro Hand
    * 장난감 예제
      : * Cartpole
        * Ant
        * Humanoid
* isaaclab_tasks:
  : * 직접 방식:
      : * Cartpole (상태, RGB, 깊이)
        * Ant
        * Humanoid
        * Allegro Hand Repose Cube
    * 관리자 기반:
      : * Cartpole (상태)
        * Ant
        * Humanoid
        * Locomotion (평평한 지형에서의 속도 기반)
          : * Anymal-B
            * Anymal-C
            * Anymal-D
            * Cassie
            * A1
            * Go1
            * Go2
            * Unitree G1
            * Unitree H1
        * Manipulation Reach
          : * Franka
            * UR10
