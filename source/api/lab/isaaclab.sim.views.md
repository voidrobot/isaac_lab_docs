# isaaclab.sim.views

USD 프림을 조작하는 뷰.

### 클래스

| [`XformPrimView`](#isaaclab.sim.views.XformPrimView)   | 여러 USD 프림의 변형을 읽고 쓰기 위한 최적화된 배치 인터페이스.   |
|--------------------------------------------------------|-----------------------------------------------------------------------------------------|

## XForm 프림 뷰

### *class* isaaclab.sim.views.XformPrimView

Bases: [`object`](https://docs.python.org/3/library/functions.html#object)

여러 USD 프림의 변형을 읽고 쓰기 위한 최적화된 배치 인터페이스.

이 클래스는 torch 텐서를 사용하여 여러 프림의 포즈(위치 및 방향)를 한 번에 효율적으로 가져오고 설정하는 배치 작업을 제공합니다. 다중 에이전트 시뮬레이션 또는 대규모 프로시저 생성과 같이 많은 프림을 동시에 조작해야 하는 시나리오를 위해 설계되었습니다.

이 클래스는 세계 공간과 로컬 공간 포즈 연산을 모두 지원합니다:

- **월드 포즈**: 글로벌 월드 프레임의 위치와 방향
- **로컬 포즈**: 각 프림의 부모 기준 위치와 방향

Fabric이 활성화된 경우, 클래스는 NVIDIA의 Fabric API를 활용하여 GPU 가속 배치 작업을 수행합니다:

- 모든 Boundable 프림에 대해 `omni:fabric:worldMatrix` 및 `omni:fabric:localMatrix` 속성 사용
- GPU에서 Warp 커널을 사용하여 배치 행렬 분해/합성 수행
- Isaac Sim의 XFormPrim 구현과 비슷한 성능 달성
- 물리 기반 및 비물리 기반 프림 모두에 적용 가능(카메라, 메쉬 등).
  참고: 렌더러는 일반적으로 USD 작성 카메라 트랜스폼을 사용합니다.

#### 경고
**Fabric은 CUDA가 필요함**: Fabric은 CUDA 기기에서만 지원됨.
Warp의 CPU 백엔드에서 fabric-array 쓰기에 알려진 문제가 있으므로, CPU 기기(`device="cpu"`)에서 Fabric을 사용하려고 시도하면 초기화 시 ValueError가 발생합니다.

#### 주의
**Fabric 지원:**

Fabric이 활성화된 경우, 이 뷰는 필수 Fabric 계층 속성(`omni:fabric:localMatrix` 및 `omni:fabric:worldMatrix`)이 있는 프림을 갖도록 보장합니다. 최초 Fabric 읽기 시, USD 작성 트랜스폼이 Fabric 상태를 초기화합니다. Fabric 쓰기는 `sync_usd_on_fabric_write`을 통해 USD로 미러링될 수 있습니다.

자세한 내용은 [Fabric Hierarchy 문서](https://docs.omniverse.nvidia.com/kit/docs/usdrt/latest/docs/fabric_hierarchy.html)를 참조하세요.

#### 주의
**성능 고려 사항:**

* 텐서 연산은 지정된 기기(CPU/CUDA)에서 수행됨
* USD 쓰기 작업은 배치를 위해 `Sdf.ChangeBlock` 사용
* Fabric 작업은 최대 성능을 위해 GPU 가속 Warp 커널 사용
* 최대 성능을 위해 루프 내에서 get/set 작업 최소화

#### 주의
**변형 요구 사항:**

뷰의 모든 프림은 Xformable이어야 하며 표준화된 변형 작업 `[translate, orient, scale]`을 가져야 함.
`validate_xform_ops`이 True인 경우 표준이 아닌 프림은 초기화 중 ValueError를 발생시킵니다.
뷰 사용 전에 프림을 준비하려면 `isaaclab.sim.utils.standardize_xform_ops()` 함수를 사용하세요.

#### 경고
이 클래스는 USD 기본 시간 코드에서 작동합니다. 애니메이션 또는 시간 샘플링된 데이터는 쓰기 작업의 영향을 받지 않습니다.
애니메이션 트랜스폼의 경우, 시간 샘플링된 키프레임을 별도로 처리해야 합니다.

**메서드:**

| [`__init__`](#isaaclab.sim.views.XformPrimView.__init__)(prim_path[, device, ...])                     | 일치하는 프림으로 뷰 초기화.     |
|--------------------------------------------------------------------------------------------------------|----------------------------------------------|
| [`set_world_poses`](#isaaclab.sim.views.XformPrimView.set_world_poses)([positions, orientations, ...]) | 뷰의 프림에 대해 월드스페이스 포즈 설정. |
| [`set_local_poses`](#isaaclab.sim.views.XformPrimView.set_local_poses)([translations, ...])            | 뷰의 프림에 대해 로컬스페이스 포즈 설정. |
| [`set_scales`](#isaaclab.sim.views.XformPrimView.set_scales)(scales[, indices])                        | 뷰의 프림에 대한 스케일 설정.            |
| [`set_visibility`](#isaaclab.sim.views.XformPrimView.set_visibility)(visibility[, indices])            | 뷰의 프림에 대한 가시성 설정.            |
| [`get_world_poses`](#isaaclab.sim.views.XformPrimView.get_world_poses)([indices])                      | 뷰의 프림에 대한 월드스페이스 포즈 가져오기. |
| [`get_local_poses`](#isaaclab.sim.views.XformPrimView.get_local_poses)([indices])                      | 뷰의 프림에 대한 로컬스페이스 포즈 가져오기. |
| [`get_scales`](#isaaclab.sim.views.XformPrimView.get_scales)([indices])                                | 뷰의 프림에 대한 스케일 가져오기.        |
| [`get_visibility`](#isaaclab.sim.views.XformPrimView.get_visibility)([indices])                        | 뷰의 프림에 대한 가시성 가져오기.        |

**속성:**

| [`count`](#isaaclab.sim.views.XformPrimView.count)           | 이 뷰의 프림 수.                                             |
|--------------------------------------------------------------|---------------------------------------------------------------------------|
| [`device`](#isaaclab.sim.views.XformPrimView.device)         | 텐서가 할당된 기기(CPU 또는 CUDA).                                      |
| [`prims`](#isaaclab.sim.views.XformPrimView.prims)           | 이 뷰에서 관리하는 USD 프림 목록.                                      |
| [`prim_paths`](#isaaclab.sim.views.XformPrimView.prim_paths) | 이 뷰에서 관리하는 모든 프림의 경로 목록(문자열 형태).                   |

#### \_\_init_\_(prim_path: [str](https://docs.python.org/3/library/stdtypes.html#str), device: [str](https://docs.python.org/3/library/stdtypes.html#str) = 'cpu', validate_xform_ops: [bool](https://docs.python.org/3/library/functions.html#bool) = True, sync_usd_on_fabric_write: [bool](https://docs.python.org/3/library/functions.html#bool) = False, stage: Usd.Stage | [None](https://docs.python.org/3/library/constants.html#None) = None)

일치하는 프림으로 뷰 초기화.

이 메서드는 제공된 경로 패턴과 일치하는 모든 프림을 USD 스테이지에서 찾고, 표준 변형 작업을 갖는 Xformable 프림인지 검증한 후 효율적인 배치 작업을 위해 참조를 저장합니다.

일반적으로 xform 작업 검증을 권장합니다. 이는 프림이 일관된 상태를 유지하고 표준 변형 작업(translate, orient, scale 순서)을 갖도록 보장하기 때문입니다.
그러나 프림이 일관된 상태임을 확신하는 경우, 성능 향상을 위해これを False로 설정할 수 있습니다.これにより、ビューの初期化にかかる時間の約45-50%を節約できます。

* **매개변수:**
  * **prim_path** – 일치시킬 USD 프림 경로 패턴. 와일드카드(`*`) 및 정규 표현식 패턴(예: `"/World/Env_.*/Robot"`)을 지원합니다.
    패턴 구문은 `isaaclab.sim.utils.find_matching_prims()` 참조.
  * **device** – 텐서를 배치할 기기. `"cpu"` 또는 `"cuda:0"`과 같은 CUDA 기기를 지정할 수 있음. 기본값은 `"cpu"`.
  * **validate_xform_ops** – 프림이 표준 xform 작업을 갖는지 검증할지 여부. 기본값은 True.
  * **sync_usd_on_fabric_write** – Fabric 변형 쓰기를 USD로 미러링할지 여부.
    True인 경우, 변형 업데이트가 USD로 동기화되어 USD 데이터 리더(예: 렌더링 카메라)가 이러한 변경 사항을 관찰할 수 있습니다.
    성능을 위해 기본값은 False.
  * **stage** – 프림을 찾을 USD 스테이지. 기본값은 None이며, 이 경우 시뮬레이션 컨텍스트의 현재 활성 스테이지를 사용합니다.
* **예외:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 일치하는 프림 중 Xformable이 아니거나 표준화된 변형 작업(translate, orient, scale 순서)을 갖지 않은 프림이 있는 경우 발생.

#### *property* count *: [int](https://docs.python.org/3/library/functions.html#int)*

이 뷰의 프림 수.

#### *property* device *: [str](https://docs.python.org/3/library/stdtypes.html#str)*

텐서가 할당된 기기(CPU 또는 CUDA).

#### *property* prims *: [list](https://docs.python.org/3/library/stdtypes.html#list)[pxr.Usd.Prim]*

이 뷰에서 관리하는 USD 프림 목록.

#### *property* prim_paths *: [list](https://docs.python.org/3/library/stdtypes.html#list)[[str](https://docs.python.org/3/library/stdtypes.html#str)]*

이 뷰에서 관리하는 모든 프림의 경로 목록(문자열 형태).

이 속성은 각 프림을 경로 문자열로 변환합니다. 변환은 첫 번째 접근 시 지연 수행되고 이후 접근을 위해 캐시됩니다.

#### 주의
대부분의 사용 사례에서는 [`prims`](#isaaclab.sim.views.XformPrimView.prims)를 직접 사용하는 것이 좋습니다. 이는 USD 프림 객체에 직접 접근하여 변환 오버헤드를 줄이기 때문입니다.
이 속성은 주로 로깅, 디버깅 또는 문자열 경로가 명시적으로 필요한 경우에 유용합니다.

#### set_world_poses(positions: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, orientations: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, indices: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

뷰의 프림에 대한 월드스페이스 포즈 설정.

이 메서드는 각 프림의 월드 공간에서의 위치 및/또는 방향을 설정합니다.

- Fabric이 활성화된 경우, GPU 가속 배치 작업을 사용하여 Fabric의 `omni:fabric:worldMatrix` 속성에 직접 쓰기 수행.
- Fabric이 비활성화된 경우, 로컬 공간으로 변환하여 USD의 `xformOp:translate` 및 `xformOp:orient` 속성에 쓰기 수행.

* **매개변수:**
  * **positions** – 월드스페이스 위치. M은 설정할 프림의 수(indices가 None인 경우 모든 프림, indices가 제공된 경우 indices 개수)를 나타내는 형태 (M, 3)의 텐서.
    기본값은 None이며, 이 경우 위치가 수정되지 않습니다.
  * **orientations** – 월드 스페이스 방향을 쿼터니언(w, x, y, z) 형태로 표현한 것으로, 모양이 (M, 4)인 텐서.
    기본값은 None이며, 이 경우 방향이 수정되지 않습니다.
  * **indices** – 자세를 설정할 프림의 인덱스. 기본값은 None이며, 이 경우 뷰의 모든 프림에 자세가 설정됩니다.
* **오류 발생:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 위치의 모양이 (M, 3)이 아니거나 방향의 모양이 (M, 4)가 아닌 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 자세의 수가 제공된 인덱스 수와 일치하지 않는 경우.

#### set_local_poses(translations: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, orientations: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor) | [None](https://docs.python.org/3/library/constants.html#None) = None, indices: Sequence[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

뷰의 프림에 로컬 스페이스 자세를 설정합니다.

이 메서드는 각 프림의 위치와/또는 방향을 로컬 스페이스(상위 프림 상대)에서 설정합니다.

이 함수는 USD의 `xformOp:translate` 및 `xformOp:orient` 속성에 직접 쓰기를 수행합니다.

#### NOTE
Fabric 모드에서도 로컬 자세 작업은 USD를 사용합니다. 이는 Isaac Sim의 설계에 기반한 동작으로, Fabric은 월드 자세 작업에만 사용됩니다.

이유:
- 로컬 자세 쓰기는 올바른 부모-자식 계층 관계가 필요합니다.
- USD는 이러한 관계를 정확하고 효율적으로 유지합니다.
- Fabric은 월드 자세 작업에 최적화되어 있으며, 로컬 계층에는 적합하지 않습니다.

* **매개변수:**
  * **translations** – 로컬 스페이스 이동을 나타내는 텐서로, 모양이 (M, 3)이며, M은 설정할 프림의 수(indices가 None인 경우 모든 프림, 그렇지 않은 경우 제공된 인덱스의 수)를 나타냅니다.
    기본값은 None이며, 이 경우 이동이 수정되지 않습니다.
  * **orientations** – 로컬 스페이스 방향을 쿼터니언(w, x, y, z) 형태로 표현한 것으로, 모양이 (M, 4)인 텐서.
    기본값은 None이며, 이 경우 방향이 수정되지 않습니다.
  * **indices** – 자세를 설정할 프림의 인덱스. 기본값은 None이며, 이 경우 뷰의 모든 프림에 자세가 설정됩니다.
* **오류 발생:**
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 이동의 모양이 (M, 3)이 아니거나 방향의 모양이 (M, 4)가 아닌 경우.
  * [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 자세의 수가 제공된 인덱스 수와 일치하지 않는 경우.

#### set_scales(scales: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), indices: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

뷰의 프림에 스케일을 설정합니다.

이 메서드는 뷰의 각 프림에 대한 스케일을 설정합니다.

- Fabric이 활성화된 경우, 함수는 GPU 가속 배치 연산을 사용하여 Fabric 매트릭스의 스케일을 업데이트합니다.
- Fabric이 비활성화된 경우, 함수는 USD의 `xformOp:scale` 속성에 쓰기를 수행합니다.

* **매개변수:**
  * **scales** – 스케일을 나타내는 텐서로, 모양이 (M, 3)이며, M은 설정할 프림의 수(indices가 None인 경우 모든 프림, 그렇지 않은 경우 제공된 인덱스의 수)를 나타냅니다.
  * **indices** – 스케일을 설정할 프림의 인덱스. 기본값은 None이며, 이 경우 뷰의 모든 프림에 스케일이 설정됩니다.
* **오류 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 스케일의 모양이 (M, 3)이 아닌 경우.

#### set_visibility(visibility: [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), indices: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None)

뷰의 프림에 대한 가시성을 설정합니다.

이 메서드는 뷰의 각 프림에 대한 가시성을 설정합니다.

* **매개변수:**
  * **visibility** – 가시성을 나타내는 불리언 텐서로, 모양이 (M,)이며, M은 설정할 프림의 수(indices가 None인 경우 모든 프림, 그렇지 않은 경우 제공된 인덱스의 수)를 나타냅니다.
  * **indices** – 가시성을 설정할 프림의 인덱스. 기본값은 None이며, 이 경우 뷰의 모든 프림에 가시성이 설정됩니다.
* **오류 발생:**
  [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError) – 가시성의 모양이 (M,)이 아닌 경우.

#### get_world_poses(indices: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

뷰의 프림에 대한 월드 스페이스 자세를 가져옵니다.

이 메서드는 프림에서 월드 루트까지의 전체 변환 계층 구조를 계산하여 각 프림의 위치와 방향을 월드 스페이스에서 검색합니다.

- Fabric이 활성화된 경우, 함수는 Warp 커널을 사용하는 Fabric 배치 연산을 사용합니다.
- Fabric이 비활성화된 경우, 함수는 USD XformCache를 사용합니다.

#### NOTE
스케일과 스큐는 무시됩니다. 반환된 자세는 이동과 회전만 포함합니다.

* **매개변수:**
  **indices** – 자세를 가져올 프림의 인덱스. 기본값은 None이며, 이 경우 뷰의 모든 프림에 대한 자세가 검색됩니다.
* **반환값:**
  - positions: 월드 스페이스 위치(x, y, z)를 포함하는 shape이 (M, 3)인 토치 텐서. 여기서 M은 쿼리된 프림의 수입니다.
  - orientations: 월드 스페이스 쿼터니언(w, x, y, z)를 포함하는 shape이 (M, 4)인 토치 텐서
* **반환 타입:**
  (positions, orientations)의 튜플이며, 여기서

#### get_local_poses(indices: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)[[torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor), [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)]

뷰의 프림에 대한 로컬 스페이스 자세를 가져옵니다.

이 메서드는 USD의 `xformOp:translate` 및 `xformOp:orient` 속성을 직접 읽어 각 프림의 위치와 방향을 로컬 스페이스(상위 프림 상대)에서 검색합니다.

#### NOTE
Fabric 모드에서도 로컬 자세 작업은 USD를 사용합니다. 이는 Isaac Sim의 설계에 기반한 동작으로, Fabric은 월드 자세 작업에만 사용됩니다.

이유:
- 로컬 자세 읽기는 올바른 부모-자식 계층 관계가 필요합니다.
- USD는 이러한 관계를 정확하고 효율적으로 유지합니다.
- Fabric은 월드 자세 작업에 최적화되어 있으며, 로컬 계층에는 적합하지 않습니다.

#### NOTE
스케일은 무시됩니다. 반환된 자세는 이동과 회전만 포함합니다.

* **매개변수:**
  **indices** – 자세를 가져올 프림의 인덱스. 기본값은 None이며, 이 경우 뷰의 모든 프림에 대한 자세가 검색됩니다.
* **반환값:**
  - translations: 로컬 스페이스 이동(x, y, z)를 포함하는 shape이 (M, 3)인 토치 텐서. 여기서 M은 쿼리된 프림의 수입니다.
  - orientations: 로컬 스페이스 쿼터니언(w, x, y, z)를 포함하는 shape이 (M, 4)인 토치 텐서
* **반환 타입:**
  (translations, orientations)의 튜플이며, 여기서

#### get_scales(indices: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

뷰의 프림에 대한 스케일을 가져옵니다.

이 메서드는 뷰의 각 프림에 대한 스케일을 검색합니다.

- Fabric이 활성화된 경우, 함수는 Warp 커널을 사용하는 배치 연산을 통해 Fabric 매트릭스에서 스케일을 추출합니다.
- Fabric이 비활성화된 경우, 함수는 USD의 `xformOp:scale` 속성을 읽습니다.

* **매개변수:**
  **indices** – 스케일을 가져올 프림의 인덱스. 기본값은 None이며, 이 경우 뷰의 모든 프림에 대한 스케일이 검색됩니다.
* **반환값:**
  모양이 (M, 3)인 텐서로, 여기서 M은 쿼리된 프림의 수이며, 각 프림의 스케일을 포함합니다.

#### get_visibility(indices: [Sequence](https://trimesh.org/trimesh.typed.html#trimesh.typed.Sequence)[[int](https://docs.python.org/3/library/functions.html#int)] | [None](https://docs.python.org/3/library/constants.html#None) = None) → [torch.Tensor](https://docs.pytorch.org/docs/stable/tensors.html#torch.Tensor)

뷰의 프림에 대한 가시성을 가져옵니다.

이 메서드는 뷰의 각 프림에 대한 가시성을 검색합니다.

* **매개변수:**
  **indices** – 가시성을 가져올 프림의 인덱스. 기본값은 None이며, 이 경우 뷰의 모든 프림에 대한 가시성이 검색됩니다.
* **반환값:**
  모양이 (M,)인 텐서로, 여기서 M은 쿼리된 프림의 수이며, 각 프림의 가시성을 포함합니다. 텐서의 타입은 bool입니다.
