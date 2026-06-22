# 솔버 전환하기

Newton 물리 엔진으로 전환하면 다양한 수치적 접근 방식으로 시뮬레이션을 처리하는 새로운 물리 솔버가 도입됩니다.
Newton은 여러 가지 다른 솔버를 지원하지만, Isaac Lab에서의 초기 초점은 Google DeepMind의 MuJoCo-Warp 솔버 사용에 있습니다.

물리 씬 자체를 정의하는 방식은 변경되지 않습니다 - 객체와 로봇의 기본 파라미터를 씬에서 설정하는 데 USD를 계속 주요 방식으로 사용하고,
현재 환경에서는 PhysX 기반 Isaac Lab에 사용된 정확한 동일한 USD 파일이 사용됩니다.
미래에는 새로운 물리 파라미터를 캡처하는 USD 스키마가 개발 중이므로 이는 변경될 수 있습니다.

변경이 필요한 부분은 일부 솔버별 설정을 구성하는 방식입니다.
이러한 파라미터를 튜닝하면 시뮬레이션 성능과 동작에 상당한 영향을 미칠 수 있습니다.

이제 이러한 파라미터를 설정하는 예시를 보여 주어 이러한 변경 사항에 익숙해지도록 돕겠습니다.
다음과 같이 `NewtonCfg`가 [`PhysxCfg`](../../api/lab/isaaclab.sim.md#isaaclab.sim.PhysxCfg)를 대체하며, `dt`를 제외한 모든 물리 시뮬레이션 파라미터와 관련된 설정에 사용됩니다:

```python
from isaaclab.sim._impl.newton_manager_cfg import NewtonCfg
from isaaclab.sim._impl.solvers_cfg import MJWarpSolverCfg

solver_cfg = MJWarpSolverCfg(
    nefc_per_env=35,
    ls_iterations=10,
    cone="pyramidal",
    ls_parallel=True,
    impratio=1,
)
newton_cfg = NewtonCfg(
    solver_cfg=solver_cfg,
    num_substeps=1,
    debug_mode=False,
)
sim: SimulationCfg = SimulationCfg(dt=1 / 120, render_interval=decimation, newton_cfg=newton_cfg)
```

다음은 위에 있는 주요 매개변수 중 일부에 대한 매우 간단한 설명입니다:

* `nefc_per_env`: 주어진 환경에 대해 MuJoCo 워프가 사전 할당하고자 하는 버퍼 제약 조건의 크기입니다.
  큰 값은 시뮬레이션 속도를 느리게 할 수 있으며,
  너무 작은 값은 일부 접촉이 누락될 수 있습니다.
* `ls_iterations`: MuJoCo 워프 솔버가 수행하는 라인 서치의 수입니다.
  라인 서치는 최적의 스텝 크기를 찾기 위해 사용되며,
  각 솔버 스텝에 대해 최대 `ls_iterations` 번의 라인 서치가 수행됩니다.
  성능을 위해 이 숫자를 낮게 유지하는 것이 중요합니다.
  이 숫자는 `ls_parallel`이 설정되지 않은 경우 상한으로도 사용됩니다.
* `cone`: 접촉 처리에서 사용되는 마찰 원뿔에 대한 피라미달형 및 타원형 근사치 중 선택을 제공하는 매개변수입니다.
  접촉에 대한 추가 정보는 MuJoCo 문서를 참고하세요:
  [https://mujoco.readthedocs.io/en/stable/computation/index.html#contact](https://mujoco.readthedocs.io/en/stable/computation/index.html#contact)
* `ls_parallel`: 라인 서치를 반복 실행에서 병렬 실행으로 전환하는 스위치입니다.
  `ls_parallel`을 활성화하면 성능이 향상되지만,
  시뮬레이션 안정성의 일부를 희생할 수 있습니다.
  활성화 시 좋은 시뮬레이션 동작을 보장하려면 일반적으로 `ls_parallel`이 비활성화된 상태의 `ls_iterations` 설정보다 약 50% 높은 설정이 권장됩니다.
* `impratio`: 접촉에서의 마찰력에 대한 법선 방향 제약의 임피던스 비율로,
  접촉에서의 접선 힘의 중요도와 법선 힘의 중요도를 세밀하게 제어할 수 있습니다.
  더 큰 값은 더 강한 마찰력 제약에 더 중점을 두어 미끄러짐을 방지함을 나타냅니다.
  이 파라미터(및 `cone`)를 튜닝하는 방법에 대한詳細は MuJoCo 문서를 참고하세요:
  [https://mujoco.readthedocs.io/en/stable/XMLreference.html#option-impratio](https://mujoco.readthedocs.io/en/stable/XMLreference.html#option-impratio)
* `num_substeps`: 시뮬레이션을 실행할 때 수행할 서브스텝의 수입니다.
  이 값을 1보다 큰 숫자로 설정하면 Isaac Lab이 두 서브스텝 사이에서 데이터를 처리할 필요 없이 시뮬레이션을 디시메이트할 수 있습니다.
  암시적 액추에이터를 사용할 때 유용할 수 있습니다.

사용 가능한 모든 파라미터 세트와 튜닝 접근 방식을 다루는 보다 상세한 전환 가이드는 향후 릴리스에서 제공될 예정입니다.
