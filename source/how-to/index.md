<a id="how-to"></a>

# 방법 안내서

이 섹션에는 Isaac Lab을 사용하도록 돕는 안내서가 포함되어 있습니다. 튜토리얼을 이미 완료한 사용자를 대상으로 하며, Isaac Lab 사용 방법에 대한 추가 정보를 찾고자 하는 사용자에게 유용합니다. Isaac Lab에 처음이라면 튜토리얼부터 시작하는 것을 권장합니다.

#### 참고
이 섹션은 작업 진행 중입니다. 여기서 답변되지 않은 질문이 있으면 [GitHub 페이지](https://github.com/isaac-sim/IsaacLab)에서 이슈를 열어 주세요.

## 새로운 애셋 가져오기

Isaac Lab에 애셋을 가져오는 것은 일반적인 작업입니다. USD 형식으로 애셋을 가져오고, 애셋용 구성 개체를 설정하는 두 단계로 구성됩니다. 다음 안내서는 Isaac Lab에 새로운 애셋을 가져오는 방법을 설명합니다.

* [새로운 애셋 가져오기](import_new_asset.md)
* [애셋 구성 작성하기](write_articulation_cfg.md)

## 고정 애셋 생성

종종 장면에 고정 애셋을 생성하고 싶을 수 있습니다. 예를 들어, 부동 베이스 로봇을 고정 베이스 로봇으로 만드는 것입니다. 이 안내서는 고정 애셋을 생성하기 위한 다양한 고려 사항과 단계를 설명합니다.

* [시뮬레이션에서 물리 프리미 고정하기](make_fixed_prim.md)

## 여러 애셋 스폰

이 안내서는 각 환경에 서로 다른 애셋을 가져오고 구성하는 방법을 설명합니다. 서로 다른 객체로 다양한 환경을 만들고자 할 때 유용합니다.

* [여러 애셋 스폰하기](multi_asset_spawning.md)

## 카메라 출력 저장

이 안내서는 Isaac Lab에서 카메라 출력을 저장하는 방법을 설명합니다.

* [렌더링된 이미지와 3D 재투영 저장하기](save_camera_output.md)

## 머신에서 실행할 수 있는 카메라 수 추정하기

이 안내서는 원하는 매개변수 하에서 머신에서 실행할 수 있는 카메라 수를 추정하는 방법을 보여줍니다.

* [어떤/얼마나 많은 카메라로 훈련해야 할지 찾기](estimate_how_many_cameras_can_run.md)

## 렌더링 구성

이 안내서는 렌더링 모드 프리셋을 선택하고 프리셋 렌더링 설정을 덮어쓰는 방법을 보여줍니다.

* [렌더링 설정 구성하기](configure_rendering.md)

## 마커 그리기

이 안내서는 [`VisualizationMarkers`](../api/lab/isaaclab.markers.md#isaaclab.markers.VisualizationMarkers) 클래스를 사용하여 Isaac Lab에서 마커를 그리는 방법을 설명합니다.

* [시각화 마커 만들기](draw_markers.md)

## 환경과 인터페이스 연결

이 안내서는 Isaac Lab에서 강화 학습 환경과 인터페이스 연결하는 방법을 설명합니다.

* [환경 래핑하기](wrap_rl_env.md)
* [자신만의 학습 라이브러리 추가하기](add_own_library.md)

## 애니메이션 및 비디오 녹화

이 안내서는 Isaac Lab에서 애니메이션과 비디오를 녹화하는 방법을 설명합니다.

* [시뮬레이션 애니메이션 녹화하기](record_animation.md)
* [훈련 중 비디오 클립 녹화하기](record_video.md)

## CurriculumTerm을 사용한 동적 환경 매개변수 수정

이 안내서는 Isaac Lab에서 훈련 중 환경 매개변수를 동적으로 수정하는 방법을 설명합니다. 런타임 중에 환경 매개변수를 변경하는 커리큘럼 유틸리티 사용법을 다룹니다.

* [커리큘럼 유틸리티](curriculums.md)

## 옴니버스 마스터하기

옴니버스는 다양한 기능을 제공하는 강력한 플랫폼입니다. 이 안내서는 Isaac Lab에서 옴니버스 기능을 사용하는 데 도움이 되는 추가 리소스를 연결합니다.

* [로봇 공학을 위한 옴니버스 마스터하기](master_omniverse.md)

## CloudXR 원격조작 설정하기

이 안내서는 Isaac Lab에서 CloudXR과 Apple Vision Pro을 사용하여 몰입형 스트리밍 및 원격조작을 수행하는 방법을 설명합니다.

* [CloudXR 원격조작 설정하기](cloudxr_teleoperation.md)

## Haply 원격조작 설정하기

이 안내서는 Isaac Lab에서 Haply Inverse3과 VerseGrip 장치를 사용하여 방향성 힘 피드백이 있는 로봇 원격조작을 수행하는 방법을 설명합니다.

* [Haply 원격조작 설정하기](haply_teleoperation.md)

## 시뮬레이션 성능 이해

이 안내서는 다양한 시뮬레이션 사용 사례에 대한 시뮬레이션 성능 최적화 팁을 제공합니다. Isaac Sim 및 옴니버스 물리와 관련된 추가 성능 가이드를 제공하는 링크도 포함되어 있습니다.

* [시뮬레이션 성능 및 튜닝](simulation_performance.md)

## 단계 생성 최적화

이 안내서는 단계 초기화를 속상할 수 있는 2가지 기능인 **패브릭 클로닝**과 **메모리 내 단계**를 설명합니다.

* [단계 생성 최적화](optimize_stage_creation.md)
