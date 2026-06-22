# 교육 과정 유틸리티

이 가이드에서는 Isaac Lab에서 강화 학습 환경에 유연한 교육 과정을 만들기 위해 사용할 수 있는 일반적인 교육 과정 헬퍼 함수 및 용어를 안내합니다. 이러한 유틸리티는 [`CurriculumTermCfg`](../api/lab/isaaclab.managers.md#isaaclab.managers.CurriculumTermCfg) 객체에 전달하여 학습 중에 보상 가중치 및 환경 매개변수를 동적으로 수정할 수 있도록 합니다.

#### 참고
이 가이드에서는 다음 세 가지 유틸리티를 다룹니다:
- 간단한 함수 `modify_reward_weight()`를 사용하여 보상 가중치를 수정합니다.
- `modify_env_param`을 사용하여 환경 매개변수를 수정합니다.
- `modify_term_cfg`을 사용하여 term_cfg를 수정합니다.

### 교육 과정 유틸리티 전체 소스

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""학습 환경에 교육 과정을 만들기 위해 사용할 수 있는 일반적인 함수입니다.

함수는 :class:`isaaclab.managers.CurriculumTermCfg` 객체에 전달하여 함수에서 도입한 교육 과정을 활성화할 수 있습니다.
"""

from __future__ import annotations

import re
from collections.abc import Sequence
from typing import TYPE_CHECKING, ClassVar

from isaaclab.managers import CurriculumTermCfg, ManagerTermBase

if TYPE_CHECKING:
    from isaaclab.envs import ManagerBasedRLEnv


class modify_reward_weight(ManagerTermBase):
    """단계별 일정에 따라 보상 가중치를 수정하는 교육 과정입니다."""

    def __init__(self, cfg: CurriculumTermCfg, env: ManagerBasedRLEnv):
        super().__init__(cfg, env)

        # 터미 구성 가져오기
        term_name = cfg.params["term_name"]
        self._term_cfg = env.reward_manager.get_term_cfg(term_name)

    def __call__(
        self,
        env: ManagerBasedRLEnv,
        env_ids: Sequence[int],
        term_name: str,
        weight: float,
        num_steps: int,
    ) -> float:
        # 터미 설정 업데이트
        if env.common_step_counter > num_steps:
            self._term_cfg.weight = weight
            env.reward_manager.set_term_cfg(term_name, self._term_cfg)

        return self._term_cfg.weight


class modify_env_param(ManagerTermBase):
    """런타임 시 환경 매개변수를 수정하는 교육 과정 용어입니다.

    이 용어는 런타임 시 환경 매개변수(또는 속성)를 수정하는 데 도움이 됩니다.
    이 매개변수는 물리 재료 속성, 관찰 범위, 또는 점 경로를 통해 액세스할 수 있는 기타 구성 가능한 매개변수와 같이 환경의 어떤 속성이라도 될 수 있습니다.

    이 용어는 ``address`` 매개변수를 사용하여 점 경로 문자열로 대상 속성을 지정합니다.
    예를 들어, "event_manager.cfg.object_physics_material.func.material_buckets"는 이벤트 관리자의 이벤트 용어 "object_physics_material"인 ``material_buckets`` 속성을 참조하며, 이는 샘플링된 물리 재료 속성의 텐서입니다.

    이 용어는 ``modify_fn`` 매개변수를 사용하여 대상 속성의 값을 수정하는 함수를 지정합니다.
    이 함수는 다음과 같은 시그니처를 가져야 합니다:

    .. code-block:: python

        def modify_fn(env, env_ids, old_value, **modify_params) -> new_value | modify_env_param.NO_CHANGE:
            # modify the value based on the old value and the modify parameters
            new_value = old_value + modify_params["value"]
            return new_value

    여기서 ``env``는 학습 환경이고, ``env_ids``는 하위 환경 인덱스이며, ``old_value``는 대상 속성의 현재 값이며, ``modify_params``는 함수에 전달할 추가 매개변수입니다.
    함수는 대상 속성에 설정할 새 값을 반환하거나, 값을 변경하지 않아야 함을 나타내는 특수 토큰 ``modify_env_param.NO_CHANGE``를 반환해야 합니다.

    초기화 후 첫 번째 호출 시, 이 용어는 ``address`` 매개변수로 지정된 대상 속성에 대한 getter 및 setter 함수를 컴파일합니다.
    getter는 현재 값을 retrieval하고, setter는 새 값을 해당 속성에 다시 작성합니다.

    이 용어는 대상 속성(주소인 `cfg.params["address"]`으로 지정)의 getter/ setter 액세서를 첫 호출 시 처리한 후, 각 호출마다 현재 값을 읽고, 사용자 제공 :attr:`modify_fn`을 적용한 후 결과를 다시 씁니다.
    이 경우 :obj:`None`이 때때로 바람직한 값일 수 있으므로, 이 클래스에 대한 비수정 신호로 토큰 :attr:`NO_CHANGE`를 사용합니다. 아래 사용법을 참조하세요.

    사용법:
        .. code-block:: python

            def resample_bucket_range(
                env, env_id, data, static_friction_range, dynamic_friction_range, restitution_range, num_steps
            ):
                if env.common_step_counter > num_steps:
                    range_list = [static_friction_range, dynamic_friction_range, restitution_range]
                    ranges = torch.tensor(range_list, device="cpu")
                    new_buckets = math_utils.sample_uniform(ranges[:, 0], ranges[:, 1], (len(data), 3), device="cpu")
                    return new_buckets

                # 단계 카운터에 도달하지 않은 경우, NO_CHANGE를 반환하여 수정이 없음을 나타냅니다.
                # None은 유효한 설정 값일 수 있으므로 이를 반환하지 않습니다.
                # 또한 입력 데이터를 반환하면 값은 변하지 않지만 여전히 setter가 호출되어 오버헤드가 발생할 수 있습니다.
                return mdp.modify_env_param.NO_CHANGE


            object_physics_material_curriculum = CurrTerm(
                func=mdp.modify_env_param,
                params={
                    "address": "event_manager.cfg.object_physics_material.func.material_buckets",
                    "modify_fn": resample_bucket_range,
                    "modify_params": {
                        "static_friction_range": [0.5, 1.0],
                        "dynamic_friction_range": [0.3, 1.0],
                        "restitution_range": [0.0, 0.5],
                        "num_step": 120000,
                    },
                },
            )
    """

    NO_CHANGE: ClassVar = object()
    """값을 설정하지 않음을 나타내는 특수 토큰입니다.

    이 토큰은 `modify_fn`이 새로운 값을 생성하지 않았음을 신호로 사용합니다.
    `modify_fn`에서 반환하여 현재 값이 그대로 유지되어야 함을 나타낼 수 있습니다.
    """

    def __init__(self, cfg: CurriculumTermCfg, env: ManagerBasedRLEnv):
        super().__init__(cfg, env)
        # 터미 구성 해결
        if "address" not in cfg.params:
            raise ValueError("교육 과정 용어 구성에 'address' 매개변수를 지정해야 합니다.")

        # 현재 주소 저장
        self._address: str = cfg.params["address"]
        # 액세서 함수 저장
        self._get_fn: callable = None
        self._set_fn: callable = None

    def __del__(self):
        """컴파일된 함수를 정리하는 소멸자입니다."""
        # getter 및 setter 함수 초기화
        self._get_fn = None
        self._set_fn = None
        self._container = None
        self._last_path = None

    """
    작업.
    """

    def __call__(
        self,
        env: ManagerBasedRLEnv,
        env_ids: Sequence[int],
        address: str,
        modify_fn: callable,
        modify_params: dict | None = None,
    ):
        # 이미 컴파일되지 않은 경우 getter 및 setter 함수 가져오기
        if not self._get_fn:
            self._get_fn, self._set_fn = self._process_accessors(self._env, self._address)

        # none 유형 해결
        modify_params = {} if modify_params is None else modify_params

        # 대상 속성의 현재 값 가져오기
        data = self._get_fn()
        # 제공된 함수를 사용하여 값 수정
        new_val = modify_fn(self._env, env_ids, data, **modify_params)
        # 수정된 값을 대상 속성에 다시 설정
        # 주의: modify_fn이 NO_CHANGE 신호를 반환하면 self.set_fn을 호출하지 않습니다.
        if new_val is not self.NO_CHANGE:
            self._set_fn(new_val)

    """
    헬퍼 함수.
    """

    def _process_accessors(self, root: ManagerBasedRLEnv, path: str) -> tuple[callable, callable]:
        """점 경로 속성에 대한 (getter, setter) 함수를 처리하고 반환합니다.

        이 함수는 주어진 루트 객체에서 점 경로 문자열을 속성으로 해석합니다.
        점 경로는 중첩된 속성, 딕셔너리 키, 시퀀스 인덱싱을 포함할 수 있습니다.

        예를 들어, 경로 "foo.bar[2].baz"는 `root.foo.bar[2].baz`로 해석됩니다.
       これにより、ディクショナリやリストなどのネストされた構造の属性にアクセスすることができます。

        Args:
            root: 속성을 해결할 메인 객체.
            path: 속성 변수에 대한 점 경로 문자열. 예를 들어 "foo.bar[2].baz".

        Returns:
            (getter, setter) 함수의 튜플로, 여기서:
            getter는 속성의 현재 값을 검색하고,
            setter는 새 값을 해당 속성에 다시 작성합니다.
        """
        # "a.b[2].c"를 ["a", ("b", 2), "c"]로 변환하고 parts에 저장
        path_parts: list[str | tuple[str, int]] = []
        for part in path.split("."):
            m = re.compile(r"^(\w+)\[(\d+)\]$").match(part)
            if m:
                path_parts.append((m.group(1), int(m.group(2))))
            else:
                path_parts.append(part)

        # 컨테이너를 찾기 위해 부분을 순회
        container = root
        for container_path in path_parts[:-1]:
            if isinstance(container_path, tuple):
                # 리스트 요소에 접근 중
                name, idx = container_path
                # 기반 속성 찾기
                if isinstance(container_path, dict):
                    seq = container[name]  # type: ignore[assignment]
                else:
                    seq = getattr(container, name)
                # 다음 반복을 위한 컨테이너 저장
                container = seq[idx]
            else:
                # 딕셔너리 키 또는 속성에 접근 중
                if isinstance(container, dict):
                    container = container[container_path]
                else:
                    container = getattr(container, container_path)

        # 컨테이너 및 경로의 마지막 부분 저장
        self._container = container
        self._last_path = path_parts[-1]  # "a.b[2].c"의 경우 "c", "a.b[2]"의 경우 2

        # getter 및 setter 생성
        if isinstance(self._container, tuple):
            get_value = lambda: self._container[self._last_path]  # noqa: E731

            def set_value(val):
                tuple_list = list(self._container)
                tuple_list[self._last_path] = val
                self._container = tuple(tuple_list)

        elif isinstance(self._container, (list, dict)):
            get_value = lambda: self._container[self._last_path]  # noqa: E731

            def set_value(val):
                self._container[self._last_path] = val

        elif isinstance(self._container, object):
            get_value = lambda: getattr(self._container, self._last_path)  # noqa: E731
            set_value = lambda val: setattr(self._container, self._last_path, val)  # noqa: E731
        else:
            raise TypeError(
                f"주소 '{path}'에 대한 접근자를 생성할 수 없습니다. 접근 변수에 알 수 없는 유형이 발견되었습니다:"
                f" '{type(self._container)}'. 리스트, 딕셔너리, 또는 속성을 가진 객체가 예상되었습니다."
            )

        return get_value, set_value


class modify_term_cfg(modify_env_param):
    """런타임 시 관리자 용어 구성을 수정하는 교육 과정입니다.

    이 클래스는 :class:`modify_env_param`을 상속하며, 관리자 용어의 구성를 수정하도록 특별히 설계되었습니다.
    주로 관리자의 구성을 참조하기 위해 "s."를 접두사로 사용하는 간소화된 주소 스타일을 사용할 수 있는 편의성을 추가합니다.

    예를 들어, "event_manager.cfg.object_physics_material.func.material_buckets" 대신
    "events.object_physics_material.func.material_buckets"를 작성하여 동일한 터미 구성을 참조할 수 있습니다.
    동일한 논리는 "observations", "commands", "rewards", "terminations"과 같은 다른 관리자에게도 적용됩니다.

    내부적으로, 주소에서 "s."의 첫 번째 발생을 "_manager.cfg."로 바꾸어 완전한 관리자 경로로 변환합니다.

    사용법:
        .. code-block:: python

            def override_value(env, env_ids, data, value, num_steps):
                if env.common_step_counter > num_steps:
                    return value
                return mdp.modify_term_cfg.NO_CHANGE


            command_object_pose_xrange_adr = CurrTerm(
                func=mdp.modify_term_cfg,
                params={
                    "address": "commands.object_pose.ranges.pos_x",  # 참고: `_manager.cfg`는 생략됨
                    "modify_fn": override_value,
                    "modify_params": {"value": (-0.75, -0.25), "num_steps": 12000},
                },
            )
    """

    def __init__(self, cfg, env):
        # 부모 초기화
        super().__init__(cfg, env)
        # 간소화된 주소를 전체 관리자 경로로 덮어쓰기
        self._address = self._address.replace("s.", "_manager.cfg.", 1)
```

## 보상 가중치 수정

함수 `modify_reward_weight()`는 지정된 수의 시뮬레이션 단계 후에 보상 항목의 가중치를 업데이트합니다. 이는 `CurriculumTermCfg`의 `func`로 직접 전달할 수 있습니다.

```python
class modify_reward_weight(ManagerTermBase):
    """단계별 일정에 따라 보상 가중치를 수정하는 커리큘럼입니다."""

    def __init__(self, cfg: CurriculumTermCfg, env: ManagerBasedRLEnv):
        super().__init__(cfg, env)

        # 항목 구성 가져오기
        term_name = cfg.params["term_name"]
        self._term_cfg = env.reward_manager.get_term_cfg(term_name)

    def __call__(
        self,
        env: ManagerBasedRLEnv,
        env_ids: Sequence[int],
        term_name: str,
        weight: float,
        num_steps: int,
    ) -> float:
        # 항목 설정 업데이트
        if env.common_step_counter > num_steps:
            self._term_cfg.weight = weight
            env.reward_manager.set_term_cfg(term_name, self._term_cfg)

        return self._term_cfg.weight
```

**사용 예시**:

```python
from isaaclab.managers import CurriculumTermCfg
import isaaclab.managers.mdp as mdp

# 100k 단계 이후에 "sparse_reward" 항목의 가중치를 0.5로 설정
sparse_reward_schedule = CurriculumTermCfg(
    func=mdp.modify_reward_weight,
    params={
        "term_name": "sparse_reward",
        "weight": 0.5,
        "num_steps": 100_000,
    }
)
```

## 환경 매개변수 동적 수정

클래스 `modify_env_param`은 [`ManagerTermBase`](../api/lab/isaaclab.managers.md#isaaclab.managers.ManagerTermBase)의 서브클래스로, 환경의 점선 속성 경로를 대상으로 하여 실행 시 사용자 제공 함수를 적용하여 새 값을 계산할 수 있습니다. 중첩된 속성, 딕셔너리 키, 리스트 또는 튜플 인덱싱을 처리하며, 업데이트가 필요하지 않은 경우 `NO_CHANGE` 센티넬을 존중합니다.

```python
class modify_env_param(ManagerTermBase):
    """실행 시간에 환경 매개변수를 수정하는 커리큘럼 용어입니다.

    이 용어는 실행 시간에 환경 매개변수(또는 속성)를 수정하는 데 도움이 됩니다.
    이 매개변수는 물리 재질 속성, 관찰 범위 또는 점선 경로를 통해 액세스할 수 있는 기타 구성 가능한 매개변수와 같이 환경의 어떤 속성일 수 있습니다.

    이 용어는 ``address`` 매개변수를 사용하여 점선 경로 문자열로 대상 속성을 지정합니다.
    예를 들어, "event_manager.cfg.object_physics_material.func.material_buckets"는
    이벤트 관리자의 이벤트 용어 "object_physics_material"의 속성 ``material_buckets``를 참조하며,
    이는 샘플된 물리 재질 속성의 텐서입니다.

    이 용어는 ``modify_fn`` 매개변수를 사용하여 대상 속성의 값을 수정하는 함수를 지정합니다.
    함수는 다음과 같은 시그니처를 가져야 합니다:

    .. code-block:: python

        def modify_fn(env, env_ids, old_value, **modify_params) -> new_value | modify_env_param.NO_CHANGE:
            # 오래된 값과 수정 매개변수를 기반으로 값을 수정합니다
            new_value = old_value + modify_params["value"]
            return new_value

    여기서 ``env``는 학습 환경, ``env_ids``는 서브 환경 인덱스,
    ``old_value``는 대상 속성의 현재 값이며,
    ``modify_params``는 함수에 전달할 수 있는 추가 매개변수입니다.
    함수는 대상 속성에 대한 새 값 또는 값을 변경하지 않아야 함을 나타내는 특수 토큰 ``modify_env_param.NO_CHANGE``를 반환해야 합니다.

    용어의 초기화 후 첫 번째 호출 시, ``address`` 매개변수로 지정된 대상 속성에 대한 getter 및 setter 함수를 컴파일합니다.
    getter는 현재 값을 검색하고, setter는 새 값을 속성에 다시 씁니다.

    이 용어는 (구성 :attr:`cfg.params["address"]`에 지정된 대로) 대상 속성의 getter/setter 액세서를 첫 번째 호출 시 처리하고,
    이후 각 호출에서는 현재 값을 읽고, 사용자 제공 :attr:`modify_fn`를 적용한 뒤 결과를 다시 씁니다.
    :obj:`None`인 경우도 종종 원하는 값이 될 수 있으므로, 수정 신호가 아님을 나타내기 위해 토큰 :attr:`NO_CHANGE`를 사용합니다. 아래 사용 예시를 참조하세요.

    사용법:
        .. code-block:: python

            def resample_bucket_range(
                env, env_id, data, static_friction_range, dynamic_friction_range, restitution_range, num_steps
            ):
                if env.common_step_counter > num_steps:
                    range_list = [static_friction_range, dynamic_friction_range, restitution_range]
                    ranges = torch.tensor(range_list, device="cpu")
                    new_buckets = math_utils.sample_uniform(ranges[:, 0], ranges[:, 1], (len(data), 3), device="cpu")
                    return new_buckets

                # 단계 카운터에 도달하지 않은 경우, 수정이 없음을 나타내기 위해 NO_CHANGE를 반환합니다.
                # None은 유효한 설정 값이 될 수 있으므로 이렇게 대신 처리합니다.
                # 또한, 입력 데이터를 반환해도 값은 바뀌지 않지만 여전히 setter가 호출되어 오버헤드가 발생할 수 있습니다.
                return mdp.modify_env_param.NO_CHANGE


            object_physics_material_curriculum = CurrTerm(
                func=mdp.modify_env_param,
                params={
                    "address": "event_manager.cfg.object_physics_material.func.material_buckets",
                    "modify_fn": resample_bucket_range,
                    "modify_params": {
                        "static_friction_range": [0.5, 1.0],
                        "dynamic_friction_range": [0.3, 1.0],
                        "restitution_range": [0.0, 0.5],
                        "num_step": 120000,
                    },
                },
            )
    """

    NO_CHANGE: ClassVar = object()
    """값 변경 없음을 나타내는 특수 토큰입니다.

    이 토큰은 `modify_fn`이 새로운 값을 생성하지 않았음을 신호로 전달하는 데 사용됩니다.
    현재 값이 변경되지 않아야 함을 나타내기 위해 `modify_fn`에서 반환할 수 있습니다.
    """

    def __init__(self, cfg: CurriculumTermCfg, env: ManagerBasedRLEnv):
        super().__init__(cfg, env)
        # 항목 구성 해결
        if "address" not in cfg.params:
            raise ValueError("커리큘럼 항목 구성에 'address' 매개변수를 지정해야 합니다.")

        # 현재 주소 저장
        self._address: str = cfg.params["address"]
        # 접근자 함수 저장
        self._get_fn: callable = None
        self._set_fn: callable = None

    def __del__(self):
        """컴파일된 함수를 정리하는 소멸자입니다."""
        # getter 및 setter 함수 정리
        self._get_fn = None
        self._set_fn = None
        self._container = None
        self._last_path = None

    """
    작업.
    """

    def __call__(
        self,
        env: ManagerBasedRLEnv,
        env_ids: Sequence[int],
        address: str,
        modify_fn: callable,
        modify_params: dict | None = None,
    ):
        # getter 및 setter 함수가 아직 컴파일되지 않은 경우 가져옵니다
        if not self._get_fn:
            self._get_fn, self._set_fn = self._process_accessors(self._env, self._address)

        # None 유형 처리
        modify_params = {} if modify_params is None else modify_params

        # 대상 속성의 현재 값을 가져옵니다
        data = self._get_fn()
        # 제공된 함수로 값을 수정합니다
        new_val = modify_fn(self._env, env_ids, data, **modify_params)
        # 수정된 값을 대상 속성에 다시 설정합니다
        # 참고: modify_fn이 NO_CHANGE 신호를 반환하는 경우 self.set_fn을 호출하지 않습니다
        if new_val is not self.NO_CHANGE:
            self._set_fn(new_val)

    """
    도움말 함수.
    """

    def _process_accessors(self, root: ManagerBasedRLEnv, path: str) -> tuple[callable, callable]:
        """점선 속성 경로에 대한 (getter, setter) 함수를 처리하고 반환합니다.

        이 함수는 주어진 루트 객체에서 점선 경로 문자열을 속성으로 해결합니다.
        점선 경로는 중첩된 속성, 딕셔너리 키 및 시퀀스 인덱싱을 포함할 수 있습니다.

        예를 들어, 경로 "foo.bar[2].baz"는 `root.foo.bar[2].baz`로 해결됩니다.
        이를 통해 딕셔너리 또는 리스트와 같은 중첩 구조의 속성에 접근할 수 있습니다.

        Args:
            root: 속성을 해결할 메인 객체입니다.
            path: 점선 경로 문자열로, 예를 들어 "foo.bar[2].baz".

        Returns:
            getter와 setter 함수로 구성된 튜플이며,
            getter는 속성의 현재 값을 검색하고,
            setter는 새 값을 속성에 다시 씁니다.
        """
        # "a.b[2].c"를 ["a", ("b", 2), "c"]로 변환하여 parts에 저장
        path_parts: list[str | tuple[str, int]] = []
        for part in path.split("."):
            m = re.compile(r"^(\w+)\[(\d+)\]$").match(part)
            if m:
                path_parts.append((m.group(1), int(m.group(2))))
            else:
                path_parts.append(part)

        # 컨테이너를 찾기 위해 부분 순회
        container = root
        for container_path in path_parts[:-1]:
            if isinstance(container_path, tuple):
                # 리스트 요소에 접근 중입니다
                name, idx = container_path
                # 기반 속성 찾기
                if isinstance(container_path, dict):
                    seq = container[name]  # type: ignore[assignment]
                else:
                    seq = getattr(container, name)
                # 다음 반복을 위한 컨테이너 저장
                container = seq[idx]
            else:
                # 딕셔너리 키 또는 속성에 접근 중입니다
                if isinstance(container, dict):
                    container = container[container_path]
                else:
                    container = getattr(container, container_path)

        # 컨테이너와 경로의 마지막 부분 저장
        self._container = container
        self._last_path = path_parts[-1]  # "a.b[2].c"의 경우 이는 "c", "a.b[2]"의 경우 이는 2

        # getter 및 setter 생성
        if isinstance(self._container, tuple):
            get_value = lambda: self._container[self._last_path]  # noqa: E731

            def set_value(val):
                tuple_list = list(self._container)
                tuple_list[self._last_path] = val
                self._container = tuple(tuple_list)

        elif isinstance(self._container, (list, dict)):
            get_value = lambda: self._container[self._last_path]  # noqa: E731

            def set_value(val):
                self._container[self._last_path] = val

        elif isinstance(self._container, object):
            get_value = lambda: getattr(self._container, self._last_path)  # noqa: E731
            set_value = lambda val: setattr(self._container, self._last_path, val)  # noqa: E731
        else:
            raise TypeError(
                f"Unable to build accessors for address '{path}'. Unknown type found for access variable:"
                f" '{type(self._container)}'. Expected a list, dict, or object with attributes."
            )

        return get_value, set_value
```

**사용 예시**:

```python
import torch
from isaaclab.managers import CurriculumTermCfg
import isaaclab.managers.mdp as mdp

def resample_friction(env, env_ids, old_value, low, high, num_steps):
    # num_steps 후에 균일한 분포에서 새로운 마찰 계수 샘플링
    if env.common_step_counter > num_steps:
        return torch.empty((len(env_ids),), device="cpu").uniform_(low, high)
    return mdp.modify_env_param.NO_CHANGE

friction_curriculum = CurriculumTermCfg(
    func=mdp.modify_env_param,
    params={
        "address": "event_manager.cfg.object_physics_material.func.material_buckets",
        "modify_fn": resample_friction,
        "modify_params": {
            "low": 0.3,
            "high": 1.0,
            "num_steps": 120_000,
        }
    }
)
```

## 수정 항목 구성

서브클래스 `modify_term_cfg`는 Hydra 구문과 일관된 보다 간결한 주소 구문을 제공하며, 기능적으로는 `modify_env_param`과 동일하게 동작합니다.

```python
class modify_term_cfg(modify_env_param):
    """실행 시간에 관리자 항목 구성을 수정하기 위한 커리큘럼.

    이 클래스는 :class:`modify_env_param`에서 상속받으며, 환경 내 관리자 항목의 구성을 수정하도록 특별히 설계되었습니다.
    관리자 구성에 접근하기 위해 간소화된 주소 스타일(`"s."` 접두어 사용)을 제공함으로써 편의성을 더합니다.

    예를 들어, `"event_manager.cfg.object_physics_material.func.material_buckets"` 대신,
    `"s.object_physics_material.func.material_buckets"`을(를) 작성하여 동일한 항목 구성을 참조할 수 있습니다.
    이는 `"observations"`, `"commands"`, `"rewards"`, `"terminations"`과(와) 같은 다른 관리자에게도 동일하게 적용됩니다.

    내부적으로는 주소에서 첫 번째 발생하는 `"s."`를 `"_manager.cfg."`로 대체하여,
    간소화된 주소를 전체 관리자 경로로 변환합니다.

    사용법:
        .. code-block:: python

            def override_value(env, env_ids, data, value, num_steps):
                if env.common_step_counter > num_steps:
                    return value
                return mdp.modify_term_cfg.NO_CHANGE


            command_object_pose_xrange_adr = CurrTerm(
                func=mdp.modify_term_cfg,
                params={
                    "address": "commands.object_pose.ranges.pos_x",  # 주의: `_manager.cfg` 생략됨
                    "modify_fn": override_value,
                    "modify_params": {"value": (-0.75, -0.25), "num_steps": 12000},
                },
            )
    """

    def __init__(self, cfg, env):
        # 부모 클래스 초기화
        super().__init__(cfg, env)
        # 간소화된 주소를 전체 관리자 경로로 대체
        self._address = self._address.replace("s.", "_manager.cfg.", 1)
```

**사용 예시**:

```python
def override_command_range(env, env_ids, old_value, value, num_steps):
    # num_steps 이후 값 오버라이드
    if env.common_step_counter > num_steps:
        return value
    return mdp.modify_term_cfg.NO_CHANGE

range_override = CurriculumTermCfg(
    func=mdp.modify_term_cfg,
    params={
        "address": "commands.object_pose.ranges.pos_x",
        "modify_fn": override_command_range,
        "modify_params": {
            "value": (-0.75, -0.25),
            "num_steps": 12_000,
        }
    }
)
```
