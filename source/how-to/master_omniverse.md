# 옴니버스에서 로봇공학 마스터하기

NVIDIA 옴니버스는 3D 콘텐츠 워크플로우를 위한 다양한 도구 세트를 제공합니다. 옴니버스에서 로봇공학과 관련된 세 가지 주요 구성 요소는 다음과 같습니다:

- **USD 컴포저**: 이는 애니메이션(원래 Pixar) 커뮤니티의 새로운 파일 형식(Universal Scene Description)을 기반으로 하며, 옴니버스에서 사용됩니다
- **PhysX SDK**: 이는 옴니버스의 주요 물리 엔진으로, GPU 기반 병렬 처리를 활용하여 대규모 씬을 처리합니다
- **RTX 지원 렌더러**: 이는 NVIDIA RTX GPU의 레이 트레이싱 커널을 사용하여 실시간 물리 기반 렌더링을 제공합니다

이 중에서 첫 번째와 두 번째는 옴니버스와 그 구성 애플리케이션(Isaac Sim 및 Isaac Lab)으로 작업을 시작하려면 더 깊은 이해가 필요합니다.

학습해야 할 주요 사항:

- 컴포저 GUI를 효율적으로 사용하는 방법은?
- USD 프림과 스키마는 무엇인가요?
- USD 씬을 어떻게 구성하나요?
- USD에서 참조와 페이로드의 차이는 무엇인가요?
- 씬-그래프 인스턴싱이란 무엇을 의미하나요?
- 프림에 PhysX 스키마를 어떻게 적용하나요? 가능한 모든 스키마는 무엇인가요?
- 프림을 생성하고 속성을 수정하는 데 USD에서 기본 연산을 어떻게 작성하나요?

## 파트 1: USD 컴포저 사용하기

여러 [video
tutorials](https://www.youtube.com/@NVIDIA-Studio)와
[documentation](https://docs.omniverse.nvidia.com/)이
NVIDIA 옴니버스에 대해 존재하지만, 모두 살펴보려면 상당한 시간과 노력이 필요합니다. 따라서 우리는 옴니버스를 사용하도록 안내하기 위해 이러한 자료를 엄선했습니다. 특히 로봇공학에 중점을 두었습니다.

옴니버스 및 USD 소개

- [NVIDIA 옴니버스가 무엇인가요?](https://youtu.be/dvdB-ndYJBM)
- [USD 파일 형식은 무엇인가요? | NVIDIA 옴니버스 시작하기](https://youtu.be/GOdyx-oSs2M)
- [NVIDIA 옴니버스에서 USD가 독특한 이유](https://youtu.be/o2x-30-PTkw)

옴니버스 USD 컴포저 사용하기

- [옴니버스 USD 컴포저 소개](https://youtu.be/_30Pf3nccuE)
- [옴니버스 USD 컴포저 내비게이션 기초](https://youtu.be/kb4ZA3TyMak)
- [NVIDIA 옴니버스 USD 컴포저의 조명 기초](https://youtu.be/c7qyI8pZvF4)
- [NVIDIA 옴니버스 USD 컴포저의 렌더링 개요](https://youtu.be/dCvq2ZyYmu4)

재질 및 MDL

- [NVIDIA 옴니버스에서 재질에 대해 알아야 할 다섯 가지](https://youtu.be/C0HmcQXaENc)
- [재질을 적용하는 방법?](https://docs.omniverse.nvidia.com/materials-and-rendering/latest/materials.html#applying-materials)

옴니버스 물리 및 PhysX SDK

- [기초 - 물리 설정 및 툴바 개요](https://youtu.be/nsJ0S9MycJI)
- [기초 - 데모 개요](https://youtu.be/-y0-EVTj10s)
- [강체 - 질량 편집](https://youtu.be/GHl2RwWeRuM)
- [재질 - 마찰 계수, 복원 계수 및 기본값](https://youtu.be/oTW81DltNiE)
- [옴니버스의 시뮬레이션 준비 애셋 물리 개요](https://youtu.be/lFtEMg86lJc)

에셋 임포트

- [옴니버스 크리에이트 - FBX 파일 임포트 | NVIDIA 옴니버스 튜토리얼](https://youtu.be/dQI0OpzfVHw)
- [옴니버스 에셋 임포터](https://docs.omniverse.nvidia.com/extensions/latest/ext_asset-importer.html)
- [Isaac Sim URDF 임포터](https://docs.isaacsim.omniverse.nvidia.com/latest/importer_exporter/ext_isaacsim_asset_importer_urdf.html)

## 파트 2: 옴니버스에서 스크립팅하기

위의 링크들은 주로 UI 작업을 통해 USD 컴포저와 그 기능을 사용하는 방법을 소개했습니다. 그러나 종종 개발자는 작업을 수행하기 위해 스크립트를 작성해야 합니다. 특히 특정 작업을 자동화하거나 옴니버스를 백엔드로 사용하는 커스텀 애플리케이션을 만들 때는 더욱 그렇습니다. 이 섹션에서는 옴니버스에서 스크립팅하는 방법을 소개합니다.

USD는 옴니버스가 작동하는 주요 파일 형식입니다. 따라서 자연스럽게 OpenUSD에서 제공하는 API(USD를 수정하는 데 사용)는 옴니버스의 핵심입니다. 대부분의 API는 C++로 작성되었으며, Python 바인딩도 제공됩니다. 따라서 옴니버스에서 스크립트를 작성하려면 USD API를 이해해야 합니다.

#### 참고
Isaac Sim과 Isaac Lab은 사용자가 핵심 USD 개념과 API를 이해하는 데서 "벗어나도록" 노력하지만, 이러한 기초를 이해하는 것이 코드베이스를 더 깊이 파고들고 자신의 애플리케이션을 위해 수정할 때 여전히 큰 도움이 됩니다.

USD 스크립팅에 뛰어들기 전에, USD에서 사용되는 용어에 익숙해지는 것이 좋습니다. 다음의 [USD 기초 소개](https://www.sidefx.com/docs/houdini/solaris/usd.html)를 Houdini(3D 애니메이션 소프트웨어)에서 추천합니다. 다음 섹션들을 꼭 살펴보세요:

- [빠른 예시](https://www.sidefx.com/docs/houdini/solaris/usd.html#quick-example)
- [어트리뷰트와 프림바](https://www.sidefx.com/docs/houdini/solaris/usd.html#attrs)
- [컴포지션](https://www.sidefx.com/docs/houdini/solaris/usd.html#compose)
- [스키마](https://www.sidefx.com/docs/houdini/solaris/usd.html#schemas)
- [인스턴스](https://www.sidefx.com/docs/houdini/solaris/usd.html#instancing)
  및 [씬-그래프 인스턴싱](https://openusd.org/dev/api/_usd__page__scenegraph_instancing.html)

이해도를 테스트하기 위해 다음 질문에 답할 수 있는지 확인하세요:

- 프림이란 무엇인가요? 스테이지에서 프림 경로란 무엇을 의미하나요?
- 어트리뷰트는 프림과 어떻게 관련되나요?
- 스키마는 프림과 어떻게 관련되나요?
- 어트리뷰트와 스키마의 차이는 무엇인가요?
- 에셋 인스턴싱이란 무엇인가요?

## 파트 3: 추가 자료

- [옴니버스 용어 사전](https://docs.isaacsim.omniverse.nvidia.com/latest/reference_material/reference_glossary.html)
- [옴니버스 코드 샘플](https://docs.omniverse.nvidia.com/dev-guide/latest/programmer_ref.html)
- [PhysX 제한 사항](https://docs.isaacsim.omniverse.nvidia.com/latest/physics/physics_resources.html)
- [PhysX 문서](https://nvidia-omniverse.github.io/PhysX/physx/).
