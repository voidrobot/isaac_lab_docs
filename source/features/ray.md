# Ray 작업 디스패치 및 튜닝

Isaac Lab은 [Ray](https://docs.ray.io/en/latest/index.html)를 지원하여 로컬 및 원격 구성에서 여러 훈련 작업(병렬 및 직렬)과 하이퍼파라미터 튜닝을 간소화합니다.

이 [독립적인 커뮤니티 기여 워크스루 비디오](https://youtu.be/z7MDgSga2Ho?feature=shared)는 이 개요에서 다루는 Ray 통합의 핵심 기능을 보여줍니다. 비디오 제작 이후 코드베이스에 차이가 있을 수 있습니다(예: 파일 이름이 단축됨), 하지만 일반적인 워크플로는 동일합니다.

#### 주의
이 기능은 실험적이며, Linux에서만 테스트되었습니다.

#### 경고
**보안 통지**: Ray와 관련된 보안 위험으로 인해 이 워크플로는 엄격하게 통제된 네트워크 환경 외에서는 사용하지 않도록 의도되었습니다. Ray 클러스터는 적절한 접근 제어 및 보안 조치가 마련된 신뢰할 수 있고 격리된 네트워크에서만 배포해야 합니다.

## 개요

다음과 같은 경우 Ray 통합이 유용합니다.

- 최소한의 상호작용으로 여러 훈련 작업을 직렬 또는 병렬로 디스패치.
- 다중 GPU 및/또다중 GPU 노드 지원을 포함하여 직렬 또는 병렬로 하이퍼파라미터 튜닝.
- 최소한의 오버헤드로 모든 환경(클라우드 및 로컬)에서 동일한 훈련 설정 사용.
- 훈련 작업에 대한 리소스 격리(리소스 래핑된 작업).

Ray 워크플로의 핵심 기능은 resource-wrapped 및 튜닝 애그리게이트 작업의 오케스트레이션을 가능하게 하는 두 가지 주요 스크립트로 구성됩니다. resource-wrapped 애그리게이트 작업에서는 각 하위 작업과 해당 리소스 요구 사항을 수동으로 정의하여 리소스 격리를 활성화합니다. 튜닝 애그리게이트 작업에서는 하이퍼파라미터 스위프 구성에 기반하여 개별 작업이 자동으로 생성됩니다.

resource-wrapped 및 튜닝 애그리게이트 작업은 모두 지정된 Ray 클러스터에 개별 작업을 디스패치하고, 해당 클러스터의 리소스(예: 단일 워크스테이션 노드 또는 다중 노드)를 활용하여 병렬 및/또는 직렬로 워커를 통해 이러한 작업을 실행합니다.

기본적으로 작업은 각 사용 가능한 GPU 지원 노드의 모든 사용 가능한 리소스를 각 하위 작업 워커에 사용합니다. 이는 resource-wrapped 작업의 `--num_workers` 인수 또는 튜닝 작업의 `--num_workers_per_node` 인수를 지정하여 변경할 수 있으며, 이는 로컬/가상 멀티-GPU 머신에서 애그리게이트 작업 처리의 병렬 처리에 특히 중요합니다. 튜닝 작업은 GPU가 있는 노드에 대해 동일한 노드 리소스 구성을 가정합니다.

다음 세 파일에 Ray 통합의 핵심 기능이 포함되어 있습니다.

### scripts/reinforcement_learning/ray/wrap_resources.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 특정 클러스터의 GPU 지원 노드(s)에서 worker(s)에 sub-job(s)(개별 작업, 튜닝 작업의 경우 :file:`tuner.py` 사용)을 디스패치하여 resource-wrapped 애그리게이트 작업의 일부로 실행합니다. 하위 작업마다 원하는 컴퓨팅 리소스가 지정되지 않은 경우, 이 스크립트는 클러스터의 각 GPU가 있는 노드에 대해 각 노드당 하나의 워커를 생성합니다. 하위 작업마다 원하는 리소스가 지정되면,
원하는 리소스로 만들 수 있는 최대 워커 수를 클러스터의 각 GPU가 있는 노드에 대해 생성합니다. ``--num_workers`` 플래그를 사용하여 각 노드의 사용 가능한 리소스를 원하는 워커 수로 분할하여 멀티-GPU 노드에서 하위 작업을 쉽게 병렬화할 수도 있습니다. Isaac Lab이 GPU를 필요로 하므로, 이 스크립트는 CPU만 있는 노드(로거 등)를 무시합니다.

하위 작업은 클러스터의 노드와 다음 관계로 매칭됩니다:
sorted_nodes = GPU 수 내림차순, 그다음 CPU 수 내림차순, 그다음 RAM 용량 내림차순, 그다음 노드 ID 순으로 정렬된 노드
node_submitted_to = sorted_nodes[job_index % total_node_count]

정렬된 노드의 순서를 확인하려면 ``--test`` 인수를 제공하고 스크립트를 실행하세요.

하위 작업은 ``+`` 구분자로 구분됩니다. ``--sub_jobs`` 인수는 스크립트에 제공해야 하는 마지막 인수여야 합니다.

사용 가능한 워커가 2개 이상이고 하위 작업이 2개 이상이면, 하위 작업은 병렬로 실행됩니다. 하위 작업이 워커보다 많으면, 하위 작업은 워커가 사용 가능해질 때마다 디스패치됩니다. 근접 동시 제출할 수 있는 하위 작업의 수에는 제한이 없습니다.

이 스크립트는 Ray 클러스터 헤드 노드에서 애그리게이트 클러스터 작업으로 실행하도록 의도되었습니다. 이 스크립트와 같은 애그리게이트 클러스터 작업을 하나 이상의 원격 클러스터에 제출하려면 :file:`../submit_isaac_ray_job.py` 를 참조하세요.

Google GKE의 KubeRay 클러스터는 :file:`../launch.py` 로 생성할 수 있습니다.

사용법:

.. code-block:: bash
    # **하위 작업이 ``+`` 구분자로 구분되어 있는지 확인하세요.**
    # 일반 템플릿-----------------------------------
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py -h
    # 리소스 격리 없음; 병렬화 없음:
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py
    --sub_jobs <JOB0>+<JOB1>+<JOB2>
    # 자동 리소스 격리; 예시 A: 병렬화에 필요
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py \
    --num_workers <NUM_TO_DIVIDE_TOTAL_RESOURCES_BY> \
    --sub_jobs <JOB0>+<JOB1>
    # 수동 리소스 격리; 예시 B: 병렬화에 필요
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py --num_cpu_per_worker <CPU> \
    --gpu_per_worker <GPU> --ram_gb_per_worker <RAM> --sub_jobs <JOB0>+<JOB1>
    # 수동 리소스 격리; 예시 C: 병렬화에 필요, 이질적인 워크로드를 위해
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py --num_cpu_per_worker <CPU> \
    --gpu_per_worker <GPU1> <GPU2> --ram_gb_per_worker <RAM> --sub_jobs <JOB0>+<JOB1>
    # 모든 인수 보기
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py -h
"""

import argparse

import util


def wrap_resources_to_jobs(jobs: list[str], args: argparse.Namespace) -> None:
    """
    작업 목록이 제공되면, 리소스 제약에 의해 다르게 지정되지 않은 한 각 사용 가능한 노드당 하나의 워커에 작업을 디스패치합니다.

    Args:
        jobs: Ray 클러스터에서 실행할 bash 명령
        args: 리소스 할당에 대한 인수

    """
    job_objs = []
    util.ray_init(
        ray_address=args.ray_address,
        runtime_env={
            "py_modules": None if not args.py_modules else args.py_modules,
        },
        log_to_driver=False,
    )
    gpu_node_resources = util.get_gpu_node_resources(include_id=True, include_gb_ram=True)

    if any([args.gpu_per_worker, args.cpu_per_worker, args.ram_gb_per_worker]) and args.num_workers:
        raise ValueError("Either specify only num_workers or only granular resources(GPU,CPU,RAM_GB).")

    num_nodes = len(gpu_node_resources)
    # 인수 채우기
    formatted_node_resources = {
        "gpu_per_worker": [gpu_node_resources[i]["GPU"] for i in range(num_nodes)],
        "cpu_per_worker": [gpu_node_resources[i]["CPU"] for i in range(num_nodes)],
        "ram_gb_per_worker": [gpu_node_resources[i]["ram_gb"] for i in range(num_nodes)],
        "num_workers": args.num_workers,  # 기본값: 노드당 1 워커
    }
    args = util.fill_in_missing_resources(args, resources=formatted_node_resources, policy=min)
    print(f"[INFO]: Number of GPU nodes found: {num_nodes}")
    if args.test:
        jobs = ["nvidia-smi"] * num_nodes
    for i, job in enumerate(jobs):
        gpu_node = gpu_node_resources[i % num_nodes]
        print(f"[INFO]: Creating job {i + 1} of {len(jobs)} with job '{job}' to node {gpu_node}")
        print(
            f"[INFO]: Resource parameters: GPU: {args.gpu_per_worker[i]}"
            f" CPU: {args.cpu_per_worker[i]} RAM {args.ram_gb_per_worker[i]}"
        )
        print(f"[INFO] For the node parameters, creating {args.num_workers[i]} workers")
        num_gpus = args.gpu_per_worker[i] / args.num_workers[i]
        num_cpus = args.cpu_per_worker[i] / args.num_workers[i]
        memory = (args.ram_gb_per_worker[i] * 1024**3) / args.num_workers[i]
        job_objs.append(
            util.Job(
                cmd=job,
                name=f"Job-{i + 1}",
                resources=util.JobResource(num_gpus=num_gpus, num_cpus=num_cpus, memory=memory),
                node=util.JobNode(
                    specific="node_id",
                    node_id=gpu_node["id"],
                ),
            )
        )
    # 작업 제출
    util.submit_wrapped_jobs(jobs=job_objs, test_mode=args.test, concurrent=False)


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Submit multiple jobs with optional GPU testing.")
    parser = util.add_resource_arguments(arg_parser=parser)
    parser.add_argument("--ray_address", type=str, default="auto", help="the Ray address.")
    parser.add_argument(
        "--test",
        action="store_true",
        help=(
            "Run nvidia-smi test instead of the arbitrary job,"
            "can use as a sanity check prior to any jobs to check "
            "that GPU resources are correctly isolated."
        ),
    )
    parser.add_argument(
        "--py_modules",
        type=str,
        nargs="*",
        default=[],
        help=(
            "List of python modules or paths to add before running the job. Example: --py_modules my_package/my_package"
        ),
    )
    parser.add_argument(
        "--sub_jobs",
        type=str,
        nargs=argparse.REMAINDER,
        help="This should be last wrapper argument. Jobs separated by the + delimiter to run on a cluster.",
    )
    args = parser.parse_args()
    if args.sub_jobs is not None:
        jobs = " ".join(args.sub_jobs)
        formatted_jobs = jobs.split("+")
    else:
        formatted_jobs = []
    print(f"[INFO]: Isaac Ray Wrapper received jobs {formatted_jobs=}")
    wrap_resources_to_jobs(jobs=formatted_jobs, args=args)
```

### scripts/reinforcement_learning/ray/tuner.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause
import argparse
import importlib.util
import os
import random
import subprocess
import sys
from time import sleep, time

import ray
import util
from ray import air, tune
from ray.tune import Callback
from ray.tune.progress_reporter import ProgressReporter
from ray.tune.search.optuna import OptunaSearch
from ray.tune.search.repeater import Repeater
from ray.tune.stopper import CombinedStopper

"""
이 스크립트는 하이퍼파라미터 스윕 구성으로 정의된 집계 튜닝 작업을 개별 작업(쉘 명령)으로 분해하여
클러스터의 GPU가 활성화된 노드에서 실행합니다.
기본적으로 클러스터의 각 GPU가 활성화된 노드마다 개별 작업당 하나의 워커가 생성됩니다.
노드당 1개 이상의 워커를 사용하려면(다중 GPU 머신의 경우), num_workers_per_node 인수를 제공하세요.

각 하이퍼파라미터 스윕 구성에는 워크플로우, 실행자 인수, 그리고 hydra 인수를 포함해야 하며
이를 다양하게 변경해야 합니다.

클러스터의 모든 워커가 동일하다고 가정합니다. 동질적이지 않은 워크로드의 경우,
동질적인 노드가 있는 여러 이종 클러스터를 생성한 후
:file:`../submit_job.py` 를 사용하여 여러 전체 클러스터 작업 제출하세요.
Google GKE의 KubeRay 클러스터는 :file:`../launch.py` 로 생성할 수 있습니다.

클러스터에서 튜닝 메트릭을 보고하려면 클러스터에서 접근 가능한 알려진 URI를 가진 실행 중인 MLFlow 서버가 필요합니다.
:file:`../launch.py` 로 구성된 KubeRay 클러스터의 경우 이는 자동으로 포함되며,
:file:`grok_cluster_with_kubectl.py` 를 통해 쉽게 찾을 수 있습니다.

사용법:

.. code-block:: bash

    ./isaaclab.sh -p scripts/reinforcement_learning/ray/tuner.py -h

    # Examples
    # Local
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/tuner.py --run_mode local \
    --cfg_file scripts/reinforcement_learning/ray/hyperparameter_tuning/vision_cartpole_cfg.py \
    --cfg_class CartpoleTheiaJobCfg
    # Local with a custom progress reporter
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/tuner.py \
    --cfg_file scripts/reinforcement_learning/ray/hyperparameter_tuning/vision_cartpole_cfg.py \
    --cfg_class CartpoleTheiaJobCfg \
    --progress_reporter CustomCartpoleProgressReporter
    # Remote (run grok cluster or create config file mentioned in :file:`submit_job.py`)
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/submit_job.py \
    --aggregate_jobs tuner.py \
    --cfg_file hyperparameter_tuning/vision_cartpole_cfg.py \
    --cfg_class CartpoleTheiaJobCfg --mlflow_uri <MLFLOW_URI_FROM_GROK_OR_MANUAL>

"""

DOCKER_PREFIX = "/workspace/isaaclab/"
BASE_DIR = os.path.expanduser("~")
PYTHON_EXEC = "./isaaclab.sh -p"
WORKFLOW = "scripts/reinforcement_learning/rl_games/train.py"
NUM_WORKERS_PER_NODE = 1  # needed for local parallelism
PROCESS_RESPONSE_TIMEOUT = 200.0  # seconds to wait before killing the process when it stops responding
MAX_LINES_TO_SEARCH_EXPERIMENT_LOGS = 1000  # maximum number of lines to read from the training process logs
MAX_LOG_EXTRACTION_ERRORS = 10  # maximum allowed LogExtractionErrors before we abort the whole training


class IsaacLabTuneTrainable(tune.Trainable):
    """The Isaac Lab Ray Tune Trainable.
    This class uses the standalone workflows to start jobs, along with the hydra integration.
    This class achieves Ray-based logging through reading the tensorboard logs from
    the standalone workflows. This depends on a config generated in the format of
    :class:`JobCfg`
    """

    def setup(self, config: dict) -> None:
        """Get the invocation command, return quick for easy scheduling."""
        self.data = None
        self.time_since_last_proc_response = 0.0
        self.invoke_cmd = util.get_invocation_command_from_cfg(cfg=config, python_cmd=PYTHON_EXEC, workflow=WORKFLOW)
        print(f"[INFO]: Recovered invocation with {self.invoke_cmd}")
        self.experiment = None

    def reset_config(self, new_config: dict):
        """Allow environments to be reused by fetching a new invocation command"""
        self.setup(new_config)
        return True

    def step(self) -> dict:
        if self.experiment is None:  # start experiment
            # When including this as first step instead of setup, experiments get scheduled faster
            # Don't want to block the scheduler while the experiment spins up
            print(f"[INFO]: Invoking experiment as first step with {self.invoke_cmd}...")
            try:
                experiment = util.execute_job(
                    self.invoke_cmd,
                    identifier_string="",
                    extract_experiment=True,  # Keep this as True to return a valid dictionary
                    persistent_dir=BASE_DIR,
                    max_lines_to_search_logs=MAX_LINES_TO_SEARCH_EXPERIMENT_LOGS,
                    max_time_to_search_logs=PROCESS_RESPONSE_TIMEOUT,
                )
            except util.LogExtractionError:
                self.data = {
                    "LOG_EXTRACTION_ERROR_STOPPER_FLAG": True,
                    "done": True,
                }
                return self.data
            self.experiment = experiment
            print(f"[INFO]: Tuner recovered experiment info {experiment}")
            self.proc = experiment["proc"]
            self.experiment_name = experiment["experiment_name"]
            self.isaac_logdir = experiment["logdir"]
            self.tensorboard_logdir = self.isaac_logdir + "/" + self.experiment_name
            self.done = False

        if self.proc is None:
            raise ValueError("Could not start trial.")
        proc_status = self.proc.poll()
        if proc_status is not None:  # process finished, signal finish
            self.data["done"] = True
            print(f"[INFO]: Process finished with {proc_status}, returning...")
        else:  # wait until the logs are ready or fresh
            data = util.load_tensorboard_logs(self.tensorboard_logdir)

            while data is None:
                data = util.load_tensorboard_logs(self.tensorboard_logdir)
                proc_status = self.proc.poll()
                if proc_status is not None:
                    break
                sleep(2)  # Lazy report metrics to avoid performance overhead

            if self.data is not None:
                data_ = {k: v for k, v in data.items() if k != "done"}
                self_data_ = {k: v for k, v in self.data.items() if k != "done"}
                unresponsiveness_start_time = time()
                while util._dicts_equal(data_, self_data_):
                    self.time_since_last_proc_response = time() - unresponsiveness_start_time
                    data = util.load_tensorboard_logs(self.tensorboard_logdir)
                    data_ = {k: v for k, v in data.items() if k != "done"}
                    proc_status = self.proc.poll()
                    if proc_status is not None:
                        break
                    if self.time_since_last_proc_response > PROCESS_RESPONSE_TIMEOUT:
                        self.time_since_last_proc_response = 0.0
                        print("[WARNING]: Training workflow process is not responding, terminating...")
                        self.proc.terminate()
                        try:
                            self.proc.wait(timeout=20)
                        except subprocess.TimeoutExpired:
                            print("[ERROR]: The process did not terminate within timeout duration.")
                            self.proc.kill()
                            self.proc.wait()
                        self.data = data
                        self.data["done"] = True
                        return self.data
                    sleep(2)  # Lazy report metrics to avoid performance overhead

            self.data = data
            self.data["done"] = False
        return self.data

    def default_resource_request(self):
        """How many resources each trainable uses. Assumes homogeneous resources across gpu nodes,
        and that each trainable is meant for one node, where it uses all available resources."""
        resources = util.get_gpu_node_resources(one_node_only=True)
        if NUM_WORKERS_PER_NODE != 1:
            print("[WARNING]: Splitting node into more than one worker")
        return tune.PlacementGroupFactory(
            [{"CPU": resources["CPU"] / NUM_WORKERS_PER_NODE, "GPU": resources["GPU"] / NUM_WORKERS_PER_NODE}],
            strategy="STRICT_PACK",
        )


class LogExtractionErrorStopper(tune.Stopper):
    """Stopper that stops all trials if multiple LogExtractionErrors occur.

    Args:
        max_errors: The maximum number of LogExtractionErrors allowed before terminating the experiment.
    """

    def __init__(self, max_errors: int):
        self.max_errors = max_errors
        self.error_count = 0

    def __call__(self, trial_id, result):
        """Increments the error count if trial has encountered a LogExtractionError.

        It does not stop the trial based on the metrics, always returning False.
        """
        if result.get("LOG_EXTRACTION_ERROR_STOPPER_FLAG", False):
            self.error_count += 1
            print(
                f"[ERROR]: Encountered LogExtractionError {self.error_count} times. "
                f"Maximum allowed is {self.max_errors}."
            )
        return False

    def stop_all(self):
        """Returns true if number of LogExtractionErrors exceeds the maximum allowed, terminating the experiment."""
        if self.error_count > self.max_errors:
            print("[FATAL]: Encountered LogExtractionError more than allowed, aborting entire tuning run... ")
            return True
        else:
            return False


class ProcessCleanupCallback(Callback):
    """Callback to clean up processes when trials are stopped."""

    def on_trial_error(self, iteration, trials, trial, error, **info):
        """Called when a trial encounters an error."""
        self._cleanup_trial(trial)

    def on_trial_complete(self, iteration, trials, trial, **info):
        """Called when a trial completes."""
        self._cleanup_trial(trial)

    def _cleanup_trial(self, trial):
        """Clean up processes for a trial using SIGKILL."""
        try:
            subprocess.run(["pkill", "-9", "-f", f"rid {trial.config['runner_args']['-rid']}"], check=False)
            sleep(5)
        except Exception as e:
            print(f"[ERROR]: Failed to cleanup trial {trial.trial_id}: {e}")


def invoke_tuning_run(
    cfg: dict,
    args: argparse.Namespace,
    progress_reporter: ProgressReporter | None = None,
    stopper: tune.Stopper | None = None,
) -> None:
    """Invoke an Isaac-Ray tuning run.

    Log either to a local directory or to MLFlow.
    Args:
        cfg: Configuration dictionary extracted from job setup
        args: Command-line arguments related to tuning.
        progress_reporter: Custom progress reporter. Defaults to CLIReporter or JupyterNotebookReporter if not provided.
        stopper: Custom stopper, optional.
    """
    # Allow for early exit
    os.environ["TUNE_DISABLE_STRICT_METRIC_CHECKING"] = "1"

    print("[WARNING]: Not saving checkpoints, just running experiment...")
    print("[INFO]: Model parameters and metrics will be preserved.")
    print("[WARNING]: For homogeneous cluster resources only...")

    # Initialize Ray
    util.ray_init(
        ray_address=args.ray_address,
        log_to_driver=True,
    )

    # Get available resources
    resources = util.get_gpu_node_resources()
    print(f"[INFO]: Available resources {resources}")

    print(f"[INFO]: Using config {cfg}")

    # Configure the search algorithm and the repeater
    searcher = OptunaSearch(
        metric=args.metric,
        mode=args.mode,
    )
    repeat_search = Repeater(searcher, repeat=args.repeat_run_count)

    # Configure the stoppers
    stoppers: CombinedStopper = CombinedStopper(
        *[
            LogExtractionErrorStopper(max_errors=MAX_LOG_EXTRACTION_ERRORS),
            *([stopper] if stopper is not None else []),
        ]
    )

    if progress_reporter is not None:
        os.environ["RAY_AIR_NEW_OUTPUT"] = "0"
        if (
            getattr(progress_reporter, "_metric", None) is not None
            or getattr(progress_reporter, "_mode", None) is not None
        ):
            raise ValueError(
                "Do not set <metric> or <mode> directly in the custom progress reporter class, "
                "provide them as arguments to tuner.py instead."
            )

    if args.run_mode == "local":  # Standard config, to file
        run_config = air.RunConfig(
            storage_path="/tmp/ray",
            name=f"IsaacRay-{args.cfg_class}-tune",
            callbacks=[ProcessCleanupCallback()],
            verbose=1,
            checkpoint_config=air.CheckpointConfig(
                checkpoint_frequency=0,  # Disable periodic checkpointing
                checkpoint_at_end=False,  # Disable final checkpoint
            ),
            stop=stoppers,
            progress_reporter=progress_reporter,
        )

    elif args.run_mode == "remote":  # MLFlow, to MLFlow server
        mlflow_callback = MLflowLoggerCallback(
            tracking_uri=args.mlflow_uri,
            experiment_name=f"IsaacRay-{args.cfg_class}-tune",
            save_artifact=False,
            tags={"run_mode": "remote", "cfg_class": args.cfg_class},
        )

        run_config = ray.train.RunConfig(
            name="mlflow",
            storage_path="/tmp/ray",
            callbacks=[ProcessCleanupCallback(), mlflow_callback],
            checkpoint_config=ray.train.CheckpointConfig(checkpoint_frequency=0, checkpoint_at_end=False),
            stop=stoppers,
            progress_reporter=progress_reporter,
        )
    else:
        raise ValueError("Unrecognized run mode.")
    # RID isn't optimized as it is sampled from, but useful for cleanup later
    cfg["runner_args"]["-rid"] = tune.sample_from(lambda _: str(random.randint(int(1e9), int(1e10) - 1)))
    # Configure the tuning job
    tuner = tune.Tuner(
        IsaacLabTuneTrainable,
        param_space=cfg,
        tune_config=tune.TuneConfig(
            metric=args.metric,
            mode=args.mode,
            search_alg=repeat_search,
            num_samples=args.num_samples,
            reuse_actors=True,
        ),
        run_config=run_config,
    )

    # Execute the tuning
    tuner.fit()

    # Save results to mounted volume
    if args.run_mode == "local":
        print("[DONE!]: Check results with tensorboard dashboard")
    else:
        print("[DONE!]: Check results with MLFlow dashboard")


class JobCfg:
    """To be compatible with :meth: invoke_tuning_run and :class:IsaacLabTuneTrainable,
    at a minimum, the tune job should inherit from this class."""

    def __init__(self, cfg: dict):
        """
        Runner args include command line arguments passed to the task.
        For example:
        cfg["runner_args"]["headless_singleton"] = "--headless"
        cfg["runner_args"]["enable_cameras_singleton"] = "--enable_cameras"
        """
        assert "runner_args" in cfg, "No runner arguments specified."
        """
        Task is the desired task to train on. For example:
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-TheiaTiny-v0"])
        """
        assert "--task" in cfg["runner_args"], "No task specified."
        """
        Hydra args define the hyperparameters varied within the sweep. For example:
        cfg["hydra_args"]["agent.params.network.cnn.activation"] = tune.choice(["relu", "elu"])
        """
        assert "hydra_args" in cfg, "No hyperparameters specified."
        self.cfg = cfg


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Tune Isaac Lab hyperparameters.")
    parser.add_argument("--ray_address", type=str, default="auto", help="the Ray address.")
    parser.add_argument(
        "--cfg_file",
        type=str,
        default="hyperparameter_tuning/vision_cartpole_cfg.py",
        required=False,
        help="The relative filepath where a hyperparameter sweep is defined",
    )
    parser.add_argument(
        "--cfg_class",
        type=str,
        default="CartpoleRGBNoTuneJobCfg",
        required=False,
        help="Name of the hyperparameter sweep class to use",
    )
    parser.add_argument(
        "--run_mode",
        choices=["local", "remote"],
        default="remote",
        help=(
            "Set to local to use ./isaaclab.sh -p python, set to "
            "remote to use /workspace/isaaclab/isaaclab.sh -p python"
        ),
    )
    parser.add_argument(
        "--workflow",
        default=None,  # populated with RL Games
        help="The absolute path of the workflow to use for the experiment. By default, RL Games is used.",
    )
    parser.add_argument(
        "--mlflow_uri",
        type=str,
        default=None,
        required=False,
        help="The MLFlow Uri.",
    )
    parser.add_argument(
        "--num_workers_per_node",
        type=int,
        default=1,
        help="Number of workers to run on each GPU node. Only supply for parallelism on multi-gpu nodes",
    )

    parser.add_argument("--metric", type=str, default="rewards/time", help="What metric to tune for.")

    parser.add_argument(
        "--mode",
        choices=["max", "min"],
        default="max",
        help="What to optimize the metric to while tuning",
    )
    parser.add_argument(
        "--num_samples",
        type=int,
        default=100,
        help="How many hyperparameter runs to try total.",
    )
    parser.add_argument(
        "--repeat_run_count",
        type=int,
        default=3,
        help="How many times to repeat each hyperparameter config.",
    )
    parser.add_argument(
        "--process_response_timeout",
        type=float,
        default=PROCESS_RESPONSE_TIMEOUT,
        help="Training workflow process response timeout.",
    )
    parser.add_argument(
        "--max_lines_to_search_experiment_logs",
        type=float,
        default=MAX_LINES_TO_SEARCH_EXPERIMENT_LOGS,
        help="Max number of lines to search for experiment logs before terminating the training workflow process.",
    )
    parser.add_argument(
        "--max_log_extraction_errors",
        type=float,
        default=MAX_LOG_EXTRACTION_ERRORS,
        help="Max number number of LogExtractionError failures before we abort the whole tuning run.",
    )
    parser.add_argument(
        "--progress_reporter",
        type=str,
        default=None,
        help=(
            "Optional: name of a custom reporter class defined in the cfg_file. "
            "Must subclass ray.tune.ProgressReporter "
            "(e.g., CustomCartpoleProgressReporter)."
        ),
    )
    parser.add_argument(
        "--stopper",
        type=str,
        default=None,
        help="A stop criteria in the cfg_file, must be a tune.Stopper instance.",
    )

    args = parser.parse_args()
    PROCESS_RESPONSE_TIMEOUT = args.process_response_timeout
    MAX_LINES_TO_SEARCH_EXPERIMENT_LOGS = int(args.max_lines_to_search_experiment_logs)
    print(
        "[INFO]: The max number of lines to search for experiment logs before (early) terminating the training "
        f"workflow process is set to {MAX_LINES_TO_SEARCH_EXPERIMENT_LOGS}.\n"
        "[INFO]: The process response timeout, used while updating tensorboard scalars and searching for "
        f"experiment logs, is set to {PROCESS_RESPONSE_TIMEOUT} seconds."
    )
    MAX_LOG_EXTRACTION_ERRORS = int(args.max_log_extraction_errors)
    print(
        "[INFO]: Max number of LogExtractionError failures before we abort the whole tuning run is "
        f"set to {MAX_LOG_EXTRACTION_ERRORS}.\n"
    )
    NUM_WORKERS_PER_NODE = args.num_workers_per_node
    print(f"[INFO]: Using {NUM_WORKERS_PER_NODE} workers per node.")
    if args.run_mode == "remote":
        BASE_DIR = DOCKER_PREFIX  # ensure logs are dumped to persistent location
        PYTHON_EXEC = DOCKER_PREFIX + PYTHON_EXEC[2:]
        if args.workflow is None:
            WORKFLOW = DOCKER_PREFIX + WORKFLOW
        else:
            WORKFLOW = args.workflow
        print(f"[INFO]: Using remote mode {PYTHON_EXEC=} {WORKFLOW=}")

        if args.mlflow_uri is not None:
            import mlflow

            mlflow.set_tracking_uri(args.mlflow_uri)
            from ray.air.integrations.mlflow import MLflowLoggerCallback
        else:
            raise ValueError("Please provide a result MLFLow URI server.")
    else:  # local
        PYTHON_EXEC = os.getcwd() + "/" + PYTHON_EXEC[2:]
        if args.workflow is None:
            WORKFLOW = os.getcwd() + "/" + WORKFLOW
        else:
            WORKFLOW = args.workflow
        BASE_DIR = os.getcwd()
        print(f"[INFO]: Using local mode {PYTHON_EXEC=} {WORKFLOW=}")
    file_path = args.cfg_file
    class_name = args.cfg_class
    print(f"[INFO]: Attempting to use sweep config from {file_path=} {class_name=}")
    module_name = os.path.splitext(os.path.basename(file_path))[0]

    spec = importlib.util.spec_from_file_location(module_name, file_path)
    module = importlib.util.module_from_spec(spec)
    sys.modules[module_name] = module
    spec.loader.exec_module(module)
    print(f"[INFO]: Successfully imported {module_name} from {file_path}")
    if hasattr(module, class_name):
        ClassToInstantiate = getattr(module, class_name)
        print(f"[INFO]: Found correct class {ClassToInstantiate}")
        instance = ClassToInstantiate()
        print(f"[INFO]: Successfully instantiated class '{class_name}' from {file_path}")
        cfg = instance.cfg
        print(f"[INFO]: Grabbed the following hyperparameter sweep config: \n {cfg}")
        # Load optional stopper config
        stopper = None
        if args.stopper and hasattr(module, args.stopper):
            stopper = getattr(module, args.stopper)
            if isinstance(stopper, type) and issubclass(stopper, tune.Stopper):
                stopper = stopper()
            else:
                raise TypeError(f"[ERROR]: Unsupported stop criteria type: {type(stopper)}")
            print(f"[INFO]: Loaded custom stop criteria from '{args.stopper}'")
        # Load optional progress reporter config
        progress_reporter = None
        if args.progress_reporter and hasattr(module, args.progress_reporter):
            progress_reporter = getattr(module, args.progress_reporter)
            if isinstance(progress_reporter, type) and issubclass(progress_reporter, tune.ProgressReporter):
                progress_reporter = progress_reporter()
            else:
                raise TypeError(f"[ERROR]: {args.progress_reporter} is not a valid ProgressReporter.")
            print(f"[INFO]: Loaded custom progress reporter from '{args.progress_reporter}'")
        invoke_tuning_run(cfg, args, progress_reporter=progress_reporter, stopper=stopper)

    else:
        raise AttributeError(f"[ERROR]:Class '{class_name}' not found in {file_path}")
```

### scripts/reinforcement_learning/ray/task_runner.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 Ray 클러스터의 작업자들에게 하나 이상의 사용자 정의 Python 작업을 분배합니다.
각 작업, 그 작업의 리소스 요구사항 및 실행 매개변수는 YAML 구성 파일에서 지정됩니다.
사용자는 구성 파일을 통해 각 작업에 할당할 CPU, GPU 수 및 메모리 양을 정의할 수 있습니다.

주요 기능:
-------------
- config 필드(`num_gpus`, `num_cpus`, `memory`)를 통한 작업별 세밀한 리소스 관리.
- Ray 클러스터 전체에서 사용 가능한 리소스를 활용한 다중 작업의 병렬 실행.
- 호스트네임, 노드 ID 또는 특정 노드에 대한 작업 친화성 지정 옵션.
- 동시(동기화된) 또는 독립적인 작업 스케줄링 옵션.

작업 스케줄링 및 분배는 Ray의 내장 리소스 관리자를 통해 처리됩니다.

YAML 구성 필드:
--------------------------
- `pip`: 모든 작업 실행 전에 설치할 추가 pip 패키지 목록.
- `py_modules`: 런타임 환경에 포함할 추가 Python 모듈 경로(디렉터리 또는 파일) 목록.
- `concurrent`: (bool) 작업 디스패치 의미론을 결정:
    - `concurrent: true`이면, **모든 작업이 배치로 스케줄링됩니다**. 스크립트는 배치 내 모든 작업에 충분한 리소스가 제공될 때까지 대기한 후, 모든 작업을 함께 시작합니다. 리소스가 부족한 경우, 클러스터가 전체 배치를 지원할 수 있을 때까지 모든 작업이 차단됩니다.
    - `concurrent: false`이면, 각 작업에 필요한 리소스가 준비되는 즉시 작업을 시작하며, Ray가 독립적으로 스케줄링합니다. 이로 인해 작업 시작 시간이 비동기식일 수 있습니다.
- `tasks`: 각 작업 사양이 포함된 리스트이며, 다음과 같이 구성됩니다:
    - `name`: 작업의 문자열 식별자.
    - `py_args`: Python 인터프리터에 전달할 인수(예: 스크립트/모듈, 플래그, 사용자 인수).
    - `num_gpus`: 할당할 GPU 수(float 또는 문자열 산술식, 예: "2*2").
    - `num_cpus`: 할당할 CPU 수(float 또는 문자열).
    - `memory`: 바이트 단위의 RAM 양(int 또는 문자열).
    - `node` (선택 사항): 노드 배치 제약 조건.
        - `specific` (str): 노드 배치 유형으로, `hostname`, `node_id`, 또는 `any`를 지원합니다.
            - `any`: 사용 가능한 모든 노드에 작업을 배치합니다.
            - `hostname`: 특정 호스트네임에 작업을 배치합니다. `hostname`은 노드 필드에 반드시 지정해야 합니다.
            - `node_id`: 특정 노드 ID에 작업을 배치합니다. `node_id`는 노드 필드에 반드시 지정해야 합니다.
        - `hostname` (str): 작업을 배치할 특정 호스트네임.
        - `node_id` (str): 작업을 배치할 특정 노드 ID.


일반적인 사용법:
--------------

.. code-block:: bash

    # 도움말 및 인수 세부 정보 출력:
    python task_runner.py -h

    # YAML 파일에서 작업을 정의하여 Ray 클러스터에 제출 (Ray 헤드 주소 자동 감지):
    python task_runner.py --task_cfg /path/to/tasks.yaml

YAML 구성 예시-1:
-----------------------------

.. code-block:: yaml

    pip: ["xxx"]
    py_modules: ["my_package/my_package"]
    concurrent: false
    tasks:
      - name: "Isaac-Cartpole-v0"
        py_args: "-m torch.distributed.run --nnodes=1 --nproc_per_node=2  --rdzv_endpoint=localhost:29501 /workspace/isaaclab/scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Cartpole-v0 --max_iterations 200 --headless --distributed"
        num_gpus: 2
        num_cpus: 10
        memory: 10737418240
      - name: "스크립트에 의존성이 필요함"
        py_args: "script.py --option arg"
        num_gpus: 0
        num_cpus: 1
        memory: 10*1024*1024*1024

YAML 구성 예시-2:
-----------------------------

.. code-block:: yaml

    pip: ["xxx"]
    py_modules: ["my_package/my_package"]
    concurrent: true
    tasks:
    - name: "Isaac-Cartpole-v0-multi-node-train-1"
        py_args: "-m torch.distributed.run --nproc_per_node=1 --nnodes=2 --node_rank=0 --rdzv_id=123 --rdzv_backend=c10d --rdzv_endpoint=localhost:5555 /workspace/isaaclab/scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Cartpole-v0 --headless --distributed --max_iterations 1000"
        num_gpus: 1
        num_cpus: 10
        memory: 10*1024*1024*1024
        node:
          specific: "hostname"
          hostname: "xxx"
    - name: "Isaac-Cartpole-v0-multi-node-train-2"
        py_args: "-m torch.distributed.run --nproc_per_node=1 --nnodes=2 --node_rank=1 --rdzv_id=123 --rdzv_backend=c10d --rdzv_endpoint=x.x.x.x:5555 /workspace/isaaclab/scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Cartpole-v0 --headless --distributed --max_iterations 1000"
        num_gpus: 1
        num_cpus: 10
        memory: 10*1024*1024*1024
        node:
          specific: "hostname"
          hostname: "xxx"

모든 작업을 조기 중지하려면 Ctrl+C를 누르세요; 스크립트는 실행 중인 모든 Ray 작업을 취소합니다.
"""  # noqa: E501

import argparse
import ast
import operator
from datetime import datetime

import yaml

# Local imports
import util  # isort: skip

# Safe operators for arithmetic expression evaluation
_SAFE_OPERATORS = {
    ast.Add: operator.add,
    ast.Sub: operator.sub,
    ast.Mult: operator.mul,
    ast.Div: operator.truediv,
    ast.FloorDiv: operator.floordiv,
    ast.Pow: operator.pow,
    ast.Mod: operator.mod,
    ast.USub: operator.neg,
    ast.UAdd: operator.pos,
}


def safe_eval_arithmetic(expr: str) -> int | float:
    """
    산술 표현식만 포함된 문자열을 안전하게 평가합니다.

    지원: +, -, *, /, //, **, % 및 숫자 리터럴.
    산술이 아닌 표현식에 대해 ValueError를 발생시킵니다.

    Args:
        expr: 산술 표현식을 포함한 문자열(예: "10*1024*1024").

    Returns:
        표현식의 숫자 결과.

    Raises:
        ValueError: 표현식에 산술이 아닌 작업이 포함된 경우.
    """

    def _eval_node(node: ast.AST) -> int | float:
        if isinstance(node, ast.Expression):
            return _eval_node(node.body)
        elif isinstance(node, ast.Constant) and isinstance(node.value, (int, float)):
            return node.value
        elif isinstance(node, ast.BinOp) and type(node.op) in _SAFE_OPERATORS:
            left = _eval_node(node.left)
            right = _eval_node(node.right)
            return _SAFE_OPERATORS[type(node.op)](left, right)
        elif isinstance(node, ast.UnaryOp) and type(node.op) in _SAFE_OPERATORS:
            operand = _eval_node(node.operand)
            return _SAFE_OPERATORS[type(node.op)](operand)
        else:
            raise ValueError(f"Unsafe expression: {ast.dump(node)}")

    try:
        tree = ast.parse(expr.strip(), mode="eval")
        return _eval_node(tree)
    except (SyntaxError, TypeError) as e:
        raise ValueError(f"Invalid arithmetic expression: {expr}") from e


def parse_args() -> argparse.Namespace:
    """
    Ray 작업 실행기의 명령줄 인수를 구문 분석합니다.

    Returns:
        파싱된 CLI 인수를 포함하는 네임스페이스:
            - task_cfg (str): YAML 작업 파일 경로.
            - ray_address (str): Ray 클러스터 주소.
            - test (bool): GPU 리소스 격리 sanity 체크를 실행할지 여부.
    """
    parser = argparse.ArgumentParser(description="YAML 구성 파일에서 작업을 실행합니다.")
    parser.add_argument("--task_cfg", type=str, required=True, help="YAML 작업 파일 경로.")
    parser.add_argument("--ray_address", type=str, default="auto", help="Ray 주소.")
    parser.add_argument(
        "--test",
        action="store_true",
        help=(
            "임의 작업 대신 nvidia-smi 테스트를 실행합니다."
            "작업 실행 전 GPU 리소스가 올바르게 격리되었는지 확인하는 사전 검사로 사용할 수 있습니다."
        ),
    )
    return parser.parse_args()


def parse_task_resource(task: dict) -> util.JobResource:
    """
    YAML 구성에서 작업 리소스 요구사항을 구문 분석합니다.

    Args:
        task (dict): 단일 작업의 구성을 나타내는 딕셔너리.
            `num_gpus`, `num_cpus`, `memory` 키를 포함할 수 있으며, 각각 숫자 또는 평가 가능한 문자열 표현식 형태일 수 있습니다.

    Returns:
        util.JobResource: 파싱된 값을 가진 리소스 객체.
    """
    resource = util.JobResource()
    if "num_gpus" in task:
        value = task["num_gpus"]
        resource.num_gpus = safe_eval_arithmetic(value) if isinstance(value, str) else value
    if "num_cpus" in task:
        value = task["num_cpus"]
        resource.num_cpus = safe_eval_arithmetic(value) if isinstance(value, str) else value
    if "memory" in task:
        value = task["memory"]
        resource.memory = safe_eval_arithmetic(value) if isinstance(value, str) else value
    return resource


def run_tasks(
    tasks: list[dict], args: argparse.Namespace, runtime_env: dict | None = None, concurrent: bool = False
) -> None:
    """
    작업을 Ray 클러스터에 제출하여 실행합니다.

    Args:
        tasks (list[dict]): 작업 구성 딕셔너리 리스트.
        args (argparse.Namespace): 파싱된 명령줄 인수.
        runtime_env (dict | None): Ray 런타임 환경 구성으로 다음과 같은 항목을 포함할 수 있습니다:
            - pip (list[str] | None): 설치할 추가 pip 패키지 목록.
            - py_modules (list[str] | None): 환경에 포함할 Python 모듈 목록.
        concurrent (bool): 작업을 배치로 동시에 시작할지, 리소스가 준비되는 대로 독립적으로 시작할지 여부.

    Returns:
        None
    """
    job_objs = []
    util.ray_init(ray_address=args.ray_address, runtime_env=runtime_env, log_to_driver=False)
    for task in tasks:
        resource = parse_task_resource(task)
        print(f"[INFO] Creating job {task['name']} with resource={resource}")
        job = util.Job(
            name=task["name"],
            py_args=task["py_args"],
            resources=resource,
            node=util.JobNode(
                specific=task.get("node", {}).get("specific"),
                hostname=task.get("node", {}).get("hostname"),
                node_id=task.get("node", {}).get("node_id"),
            ),
        )
        job_objs.append(job)
    start = datetime.now()
    print(f"[INFO] Creating {len(job_objs)} jobs at {start.strftime('%H:%M:%S.%f')} with runtime env={runtime_env}")
    # submit jobs
    util.submit_wrapped_jobs(
        jobs=job_objs,
        test_mode=args.test,
        concurrent=concurrent,
    )
    end = datetime.now()
    print(
        f"[INFO] All jobs completed at {end.strftime('%H:%M:%S.%f')}, took {(end - start).total_seconds():.2f} seconds."
    )


def main() -> None:
    """
    Ray 작업 실행기 스크립트의 메인 진입점입니다.

    YAML 작업 구성 파일을 읽고, CLI 인수를 파싱하며, 작업을 Ray 클러스터에 분배합니다.

    Returns:
        None
    """
    args = parse_args()
    with open(args.task_cfg) as f:
        config = yaml.safe_load(f)
    tasks = config["tasks"]
    runtime_env = {
        "pip": None if not config.get("pip") else config["pip"],
        "py_modules": None if not config.get("py_modules") else config["py_modules"],
    }
    concurrent = config.get("concurrent", False)
    run_tasks(
        tasks=tasks,
        args=args,
        runtime_env=runtime_env,
        concurrent=concurrent,
    )


if __name__ == "__main__":
    main()
```

다음 스크립트는 한 개 이상의 Ray 클러스터에 집계 작업을 제출하는 데 사용될 수 있으며, 이는 원격 클러스터에서 작업 실행 또는 이기종 자원 요구 사항을 가진 동시 작업 실행에 사용될 수 있습니다.

### scripts/reinforcement_learning/ray/submit_job.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 ``name: <NAME> address: http://<IP>:<PORT>`` 형태의 한 줄씩 클러스터를 설명하는 설정 파일에서 설명된 클러스터(들)에 집계 작업(들)을 제출합니다. KubeRay 클러스터의 경우, 이 파일은 :file:`grok_cluster_with_kubectl.py`를 사용하여 자동으로 생성할 수 있습니다.

집계 작업(들)은 다음 관계를 통해 클러스터(들)과 매칭됩니다:
cluster_line_index_submitted_to = job_index % total_cluster_count

집계 작업은 * 구분자로 분리됩니다. ``--aggregate_jobs`` 인수는 스크립트에 제공되는 마지막 인수여야 합니다.

집계 작업은 클러스터에서 시작할 때 여러 개별 작업을 자동으로 생성하는 :file:`../tuner.py` 튜닝 작업일 수 있습니다. 또는, 여러 개별 하위 작업으로 구분된 + 구분자를 포함할 수 있는 :file:`../wrap_resources.py` 자원 래핑 작업일 수도 있습니다. 집계 작업은 각 하위 작업 및 해당 자원 요구 사항이 YAML 구성 파일에서 정의된 :file:`../task_runner.py` 다중 작업 제출 작업일 수도 있습니다. 이 모드에서, :file:`../task_runner.py`은 YAML 파일(--task_cfg)을 읽고, 정의된 모든 하위 작업을 Ray 클러스터에 제출하여, 작업별 자원 지정을 지원하고 하위 작업 출력의 실시간 스트리밍을 제공합니다.

집계 작업의 수가 클러스터 수보다 많으면, 정의된 관계에 따라 클러스터가 사용 가능해질 때마다 집계 작업이 제출됩니다. 집계 작업의 수가 클러스터 수보다 적으면 일부 클러스터는 집계 작업을 받지 않습니다. 동시에 실행할 수 있는 집계 작업의 최대 수는 작업을 제출하는 머신에서 ThreadPoolExecutor의 기본 작업자 수와 같으며, 작업이 끝난 후 로그 출력을 가져오는 것으로 인해 전체 작업 제출에 제약을 줄 가능성은 낮습니다.

사용법:

.. code-block:: bash

    # 예시; 튜닝 작업 제출
    python3 scripts/reinforcement_learning/ray/submit_job.py \
    --aggregate_jobs /workspace/isaaclab/scripts/reinforcement_learning/ray/tuner.py \
        --cfg_file hyperparameter_tuning/vision_cartpole_cfg.py \
        --cfg_class CartpoleTheiaJobCfg --mlflow_uri <ML_FLOW_URI>

    # 예시: 자원 래핑 작업 제출
    python3 scripts/reinforcement_learning/ray/submit_job.py --aggregate_jobs wrap_resources.py --test

    # 예시: 특정 자원을 가진 작업 제출 및 pip 패키지와 py_modules 지원
    # task_cfg 및 py_modules에 상대 경로를 사용할 수 있으며, 이 파일들은 "scripts/reinforcement_learning/ray" 디렉터리에 배치되어 클러스터에 업로드됩니다.
    python3 scripts/reinforcement_learning/ray/submit_job.py --aggregate_jobs task_runner.py --task_cfg tasks.yaml

    # 모든 명령줄 인수에 대해
    python3 scripts/reinforcement_learning/ray/submit_job.py -h
"""

import argparse
import os
import time
from concurrent.futures import ThreadPoolExecutor

from ray import job_submission

script_directory = os.path.dirname(os.path.abspath(__file__))
CONFIG = {"working_dir": script_directory, "executable": "/workspace/isaaclab/isaaclab.sh -p"}


def read_cluster_spec(fn: str | None = None) -> list[dict]:
    if fn is None:
        cluster_spec_path = os.path.expanduser("~/.cluster_config")
    else:
        cluster_spec_path = os.path.expanduser(fn)

    if not os.path.exists(cluster_spec_path):
        raise FileNotFoundError(f"Cluster spec file not found at {cluster_spec_path}")

    clusters = []
    with open(cluster_spec_path) as f:
        for line in f:
            parts = line.strip().split(" ")
            http_address = parts[3]
            cluster_info = {"name": parts[1], "address": http_address}
            print(f"[INFO] Setting {cluster_info['name']}")  # with {cluster_info['num_gpu']} GPUs.")
            clusters.append(cluster_info)

    return clusters


def submit_job(cluster: dict, job_command: str) -> None:
    """
    단일 클러스터에 작업을 제출하고, 마지막에 최종 결과 및 Ray 대시보드 URL을 출력합니다.
    """
    address = cluster["address"]
    cluster_name = cluster["name"]
    print(f"[INFO]: Submitting job to cluster '{cluster_name}' at {address}")  # with {num_gpus} GPUs.")
    client = job_submission.JobSubmissionClient(address)
    runtime_env = {"working_dir": CONFIG["working_dir"], "executable": CONFIG["executable"]}
    print(f"[INFO]: Checking contents of the directory: {CONFIG['working_dir']}")
    try:
        dir_contents = os.listdir(CONFIG["working_dir"])
        print(f"[INFO]: Directory contents: {dir_contents}")
    except Exception as e:
        print(f"[INFO]: Failed to list directory contents: {str(e)}")
    entrypoint = f"{CONFIG['executable']} {job_command}"
    print(f"[INFO]: Attempting entrypoint {entrypoint=} in cluster {cluster}")
    job_id = client.submit_job(entrypoint=entrypoint, runtime_env=runtime_env)
    status = client.get_job_status(job_id)
    while status in [job_submission.JobStatus.PENDING, job_submission.JobStatus.RUNNING]:
        time.sleep(5)
        status = client.get_job_status(job_id)

    final_logs = client.get_job_logs(job_id)
    print("----------------------------------------------------")
    print(f"[INFO]: Cluster {cluster_name} Logs: \n")
    print(final_logs)
    print("----------------------------------------------------")


def submit_jobs_to_clusters(jobs: list[str], clusters: list[dict]) -> None:
    """
    모든 작업을 해당 클러스터에 제출하고, 작업이 클러스터 수보다 많을 경우 클러스터를 순환하여 사용합니다.
    """
    if not clusters:
        raise ValueError("No clusters available for job submission.")

    if len(jobs) < len(clusters):
        print("[INFO]: Less jobs than clusters, some clusters will not receive jobs")
    elif len(jobs) == len(clusters):
        print("[INFO]: Exactly one job per cluster")
    else:
        print("[INFO]: More jobs than clusters, jobs submitted as clusters become available.")
    with ThreadPoolExecutor() as executor:
        for idx, job_command in enumerate(jobs):
            # 클러스터 수보다 작업이 많을 경우 modulus를 사용하여 클러스터를 순환
            cluster = clusters[idx % len(clusters)]
            executor.submit(submit_job, cluster, job_command)


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Submit multiple GPU jobs to multiple Ray clusters.")
    parser.add_argument("--config_file", default="~/.cluster_config", help="The cluster config path.")
    parser.add_argument(
        "--aggregate_jobs",
        type=str,
        nargs=argparse.REMAINDER,
        help="This should be last argument. The aggregate jobs to submit separated by the * delimiter.",
    )
    args = parser.parse_args()
    if args.aggregate_jobs is not None:
        jobs = " ".join(args.aggregate_jobs)
        formatted_jobs = jobs.split("*")
        if len(formatted_jobs) > 1:
            print("Warning; Split jobs by cluster with the * delimiter")
    else:
        formatted_jobs = []
    print(f"[INFO]: Isaac Ray Wrapper received jobs {formatted_jobs=}")
    clusters = read_cluster_spec(args.config_file)
    submit_jobs_to_clusters(formatted_jobs, clusters)
```

다음 스크립트는 집계 작업 제출을 위해 KubeRay 클러스터 정보를 추출하는 데 사용될 수 있습니다.

### scripts/reinforcement_learning/ray/grok_cluster_with_kubectl.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

import argparse
import os
import re
import subprocess
import threading
import time
from concurrent.futures import ThreadPoolExecutor, as_completed

"""
이 스크립트는 kubectl이 설치되어 있고 KubeRay를 사용하여 클러스터가 생성되었음을 전제로 합니다.

이 스크립트는 ``name: <NAME> address: http://<IP>:<PORT>`` 형태의 한 줄씩 클러스터를 설명하는 설정 파일을 생성하고, 또한 MLFlow URI를 가져옵니다.

사용법:

.. code-block:: bash

    python3 scripts/reinforcement_learning/ray/grok_cluster_with_kubectl.py
    # 옵션을 보려면 -h 인수를 제공
"""


def get_namespace() -> str:
    """현재 Kubernetes 네임스페이스를 컨텍스트에서 가져오고, 설정되지 않은 경우 기본값으로 fallback"""
    try:
        namespace = (
            subprocess.check_output(["kubectl", "config", "view", "--minify", "--output", "jsonpath={..namespace}"])
            .decode()
            .strip()
        )
        if not namespace:
            namespace = "default"
    except subprocess.CalledProcessError:
        namespace = "default"
    return namespace


def get_pods(namespace: str = "default") -> list[tuple]:
    """네임스페이스의 모든 pod 목록을 가져옵니다"""
    cmd = ["kubectl", "get", "pods", "-n", namespace, "--no-headers"]
    output = subprocess.check_output(cmd).decode()
    pods = []
    for line in output.strip().split("\n"):
        fields = line.split()
        pod_name = fields[0]
        status = fields[2]
        pods.append((pod_name, status))
    return pods


def get_clusters(pods: list, cluster_name_prefix: str) -> set:
    """
    고유한 클러스터 이름 집합을 가져옵니다. 하나의 클러스터 또는 여러 클러스터에 대해 작동하며, 헤드 노드 수를 기준으로 합니다.
    MLflow 배포는 제외합니다.
    """
    clusters = set()
    for pod_name, _ in pods:
        # MLflow pod 건너뛰기
        if "-mlflow" in pod_name:
            continue

        match = re.match(r"(" + re.escape(cluster_name_prefix) + r"[-\w]+)", pod_name)
        if match:
            # 헤드/워커 접미사 없이 기본 이름 가져오기 (워커 건너뛰기)
            if "head" in pod_name:
                base_name = match.group(1).split("-head")[0]
                clusters.add(base_name)
    return sorted(clusters)


def get_mlflow_info(namespace: str = None, cluster_prefix: str = "isaacray") -> str:
    """
    주어진 접두사를 가진 네임스페이스에 MLflow 서비스 정보가 존재하는 경우 가져옵니다.
    단일 클러스터 인스턴스에만 작동합니다.
    Args:
        namespace: Kubernetes 네임스페이스
        cluster_prefix: 기본 클러스터 이름 ( -head/-worker 접미사 없이)
    Returns:
        MLflow 서비스 URL
    """
    # 기본 이름을 얻기 위해 -head 또는 -worker 접미사 제거
    if namespace is None:
        namespace = get_namespace()
    pods = get_pods(namespace=namespace)
    clusters = get_clusters(pods=pods, cluster_name_prefix=cluster_prefix)
    if len(clusters) > 1:
        raise ValueError("More than one cluster matches prefix, could not automatically determine mlflow info.")
    mlflow_name = f"{cluster_prefix}-mlflow"

    cmd = ["kubectl", "get", "svc", mlflow_name, "-n", namespace, "--no-headers"]
    try:
        output = subprocess.check_output(cmd).decode()
        fields = output.strip().split()

        # 클러스터 IP 가져오기
        cluster_ip = fields[2]
        port = "5000"  # 기본 MLflow 포트
        # 이를 해결하기 위해 http여야 합니다. HTTPS는 해결할 수 없습니다
        # 클러스터 내의 서브넷에 있으므로 이는 문제가 없어야 합니다
        return f"http://{cluster_ip}:{port}"
    except subprocess.CalledProcessError as e:
        raise ValueError(f"Could not grok MLflow: {e}")  # Fixed f-string


def check_clusters_running(pods: list, clusters: set) -> bool:
    """
    모든 제공된 클러스터의 모든 pod가 실행 중인지 확인합니다.

    Args:
        pods (list): 각 튜플이 pod 이름과 해당 상태를 포함하는 튜플 목록.
        clusters (set): 확인할 클러스터 이름 집합.

    Returns:
        bool: 클러스터의 모든 pod가 실행 중이면 True, 그렇지 않으면 False.
    """
    clusters_running = False
    for cluster in clusters:
        cluster_pods = [p for p in pods if p[0].startswith(cluster)]
        total_pods = len(cluster_pods)
        running_pods = len([p for p in cluster_pods if p[1] == "Running"])
        if running_pods == total_pods and running_pods > 0:
            clusters_running = True
            break
    return clusters_running


def get_ray_address(head_pod: str, namespace: str = "default", ray_head_name: str = "head") -> str:
    """
    주어진 클러스터 헤드 pod에 대해, 해당 로그를 확인하여 job 요청을 수락할 수 있는 Ray 주소를 가져옵니다.

    Args:
        head_pod (str): 헤드 pod의 이름.
        namespace (str, optional): Kubernetes 네임스페이스. 기본값은 "default".
        ray_head_name (str, optional): Ray 헤드 컨테이너의 이름. 기본값은 "head".

    Returns:
        str: Ray 주소를 찾으면 해당 주소를 반환하고, 찾을 수 없으면 None을 반환합니다.

    Raises:
        ValueError: 로그를 가져올 수 없거나 Ray 주소를 찾을 수 없는 경우.
    """
    cmd = ["kubectl", "logs", head_pod, "-c", ray_head_name, "-n", namespace]
    try:
        output = subprocess.check_output(cmd).decode()
    except subprocess.CalledProcessError as e:
        raise ValueError(
            f"Could not enter head container with cmd {cmd}: {e}Perhaps try a different namespace or ray head name."
        )
    match = re.search(r"RAY_ADDRESS='([^']+)'", output)
    if match:
        return match.group(1)
    else:
        return None


def process_cluster(cluster_info: dict, ray_head_name: str = "head") -> str:
    """
    각 클러스터에 대해, 클러스터가 실행 중인지 확인하고 job을 수락할 수 있는 Ray 헤드 주소를 가져옵니다.

    Args:
        cluster_info: 'cluster', 'pods', 'namespace' 키를 포함하는 클러스터 정보를 담은 딕셔너리.
        ray_head_name: Ray 헤드 컨테이너의 이름. 기본값은 "head".

    Returns:
        클러스터 이름과 해당 Ray 헤드 주소를 포함한 문자열, 또는 헤드 pod 또는 Ray 주소를 찾을 수 없는 경우 오류 메시지.
    """
    cluster, pods, namespace = cluster_info
    head_pod = None
    for pod_name, status in pods:
        if pod_name.startswith(cluster + "-head"):
            head_pod = pod_name
            break
    if not head_pod:
        return f"Error: Could not find head pod for cluster {cluster}\n"

    # RAY_ADDRESS 및 상태 가져오기
    ray_address = get_ray_address(head_pod, namespace=namespace, ray_head_name=ray_head_name)
    if not ray_address:
        return f"Error: Could not find RAY_ADDRESS for cluster {cluster}\n"

    # 클러스터와 Ray 주소만 반환
    return f"name: {cluster} address: {ray_address}\n"


def main():
    # 명령줄 인수 파싱
    parser = argparse.ArgumentParser(description="Process Ray clusters and save their specifications.")
    parser.add_argument("--prefix", default="isaacray", help="The prefix for the cluster names.")
    parser.add_argument("--output", default="~/.cluster_config", help="The file to save cluster specifications.")
    parser.add_argument("--ray_head_name", default="head", help="The metadata name for the ray head container")
    parser.add_argument(
        "--namespace", help="Kubernetes namespace to use. If not provided, will detect from current context."
    )
    args = parser.parse_args()

    # args에서 네임스페이스를 가져오거나 감지
    current_namespace = args.namespace if args.namespace else get_namespace()
    print(f"Using namespace: {current_namespace}")

    cluster_name_prefix = args.prefix
    cluster_spec_file = os.path.expanduser(args.output)

    # 모든 pod 가져오기
    pods = get_pods(namespace=current_namespace)

    # 클러스터 가져오기
    clusters = get_clusters(pods, cluster_name_prefix)
    if not clusters:
        print(f"No clusters found with prefix {cluster_name_prefix}")
        return

    # 클러스터가 실행될 때까지 대기
    while True:
        pods = get_pods(namespace=current_namespace)
        if check_clusters_running(pods, clusters):
            break
        print("Waiting for all clusters to spin up...")
        time.sleep(5)

    print("Checking for MLflow:")
    # 각 클러스터의 MLflow 상태 확인
    for cluster in clusters:
        try:
            mlflow_address = get_mlflow_info(current_namespace, cluster)
            print(f"MLflow address for {cluster}: {mlflow_address}")
        except ValueError as e:
            print(f"ML Flow not located: {e}")
    print()

    # 병렬 처리를 위한 클러스터 정보 준비
    cluster_infos = []
    for cluster in clusters:
        cluster_pods = [p for p in pods if p[0].startswith(cluster)]
        cluster_infos.append((cluster, cluster_pods, current_namespace))

    # ThreadPoolExecutor를 사용하여 클러스터를 병렬로 처리
    results = []
    results_lock = threading.Lock()

    with ThreadPoolExecutor() as executor:
        future_to_cluster = {
            executor.submit(process_cluster, info, args.ray_head_name): info[0] for info in cluster_infos
        }
        for future in as_completed(future_to_cluster):
            cluster_name = future_to_cluster[future]
            try:
                result = future.result()
                with results_lock:
                    results.append(result)
            except Exception as exc:
                print(f"{cluster_name} generated an exception: {exc}")

    # 클러스터 이름을 기준으로 결과 정렬
    results.sort()

    # 정렬된 결과를 출력 파일에 기록 (Ray 정보만)
    with open(cluster_spec_file, "w") as f:
        for result in results:
            f.write(result)

    print(f"Cluster spec information saved to {cluster_spec_file}")
    # 설정 파일의 내용 표시
    with open(cluster_spec_file) as f:
        print(f.read())


if __name__ == "__main__":
    main()
```

다음 스크립트는 Google GKE에서 클러스터를 쉽게 생성하는 데 사용될 수 있습니다.

### scripts/reinforcement_learning/ray/launch.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""이 스크립트는 하나 이상의 KubeRay 클러스터를 생성하는 데 도움이 됩니다.

사용법:

.. code-block:: bash
    # 헤드 노드가 컨테이너 생성 중에 멈춘 경우, 비밀을 생성해야 함을 확인하세요
    python3 scripts/reinforcement_learning/ray/launch.py -h

    # 예시

    # 다음은 GPUx1 NVIDIA L4 워커 8개를 생성합니다
    python3 scripts/reinforcement_learning/ray/launch.py --cluster_host google_cloud \
    --namespace <NAMESPACE> --image <YOUR_ISAAC_RAY_IMAGE> \
    --num_workers 8 --num_clusters 1 --worker_accelerator nvidia-l4 --gpu_per_worker 1

    # 다음은 GPUx1 NVIDIA L4 워커 1개, GPUx2 NVIDIA-TESLA-T4 워커 2개,
    # 그리고 GPUx4 NVIDIA-TESLA-T4 GPU 워커 2개를 생성합니다
    python3 scripts/reinforcement_learning/ray/launch.py --cluster_host google_cloud \
    --namespace <NAMESPACE> --image <YOUR_ISAAC_RAY_IMAGE> \
    --num_workers 1 2 --num_clusters 1 \
    --worker_accelerator nvidia-l4 nvidia-tesla-t4 --gpu_per_worker 1 2 4
"""

import argparse
import pathlib
import subprocess

import yaml
from jinja2 import Environment, FileSystemLoader
from kubernetes import config

# Local imports
import util  # isort: skip

RAY_DIR = pathlib.Path(__file__).parent


def apply_manifest(args: argparse.Namespace) -> None:
    """제공된 Jinja 템플릿 ray.io/v1alpha1 파일을 처리하고
    인수를 채워 클러스터를 생성합니다. 또한, 파일 나머지와 '---'로 구분된
    쿠버네티스 컨테이너를 생성합니다.

    Args:
        args: 클러스터 매개변수와 관련된 가능한 인수입니다.
    """
    # Kubernetes 구성 로드
    config.load_kube_config()

    # 템플릿 로드를 위한 Jinja2 환경 설정
    templates_dir = RAY_DIR / "cluster_configs" / args.cluster_host
    file_loader = FileSystemLoader(str(templates_dir))
    jinja_env = Environment(loader=file_loader, keep_trailing_newline=True, autoescape=True)

    # 템플릿 파일 이름 정의
    template_file = "kuberay.yaml.jinja"

    # args 네임스페이스를 딕셔너리로 변환
    template_params = vars(args)

    # 템플릿 로드 및 렌더링
    template = jinja_env.get_template(template_file)
    file_contents = template.render(template_params)

    # 렌더링된 템플릿의 모든 YAML 문서 파싱
    all_yamls = []
    for doc in yaml.safe_load_all(file_contents):
        all_yamls.append(doc)

    # 여러 문서를 보존하여 YAML 문자열로 다시 변환
    cleaned_yaml_string = ""
    for i, doc in enumerate(all_yamls):
        if i > 0:
            cleaned_yaml_string += "\n---\n"
        cleaned_yaml_string += yaml.dump(doc)

    # kubectl을 사용하여 쿠버네티스 매니페스트 적용
    try:
        print(cleaned_yaml_string)
        subprocess.run(["kubectl", "apply", "-f", "-"], input=cleaned_yaml_string, text=True, check=True)
    except subprocess.CalledProcessError as e:
        exit(f"`kubectl` 실행 중 오류가 발생했습니다: {e}")


def parse_args() -> argparse.Namespace:
    """
    Kubernetes 배포 스크립트의 명령줄 인수를 파싱합니다.

    Returns:
        argparse.Namespace: 파싱된 명령줄 인수입니다.
    """
    arg_parser = argparse.ArgumentParser(
        description="Ray 클러스터를 위한 쿠버네티스 오브젝트를 생성하기 위해 매니페스트를 적용하는 스크립트입니다.",
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
    )

    arg_parser.add_argument(
        "--cluster_host",
        type=str,
        default="google_cloud",
        choices=["google_cloud"],
        help=(
            "cluster_configs 디렉터리에서 tune.yaml.jinja 파일이 존재하는 폴더의 이름입니다."
            "KubeRay 구성 정의. 현재 google_cloud만 지원됩니다."
        ),
    )

    arg_parser.add_argument(
        "--name",
        type=str,
        required=False,
        default="isaacray",
        help="쿠버네티스 배포의 이름입니다.",
    )

    arg_parser.add_argument(
        "--namespace",
        type=str,
        required=False,
        default="default",
        help="Ray 클러스터를 배포할 쿠버네티스 네임스페이스입니다.",
    )

    arg_parser.add_argument(
        "--service_acount_name", type=str, required=False, default="default", help="사용할 서비스 계정 이름입니다."
    )

    arg_parser.add_argument(
        "--image",
        type=str,
        required=True,
        help="Ray 클러스터 파드의 도커 이미지입니다.",
    )

    arg_parser.add_argument(
        "--worker_accelerator",
        nargs="+",
        type=str,
        default=["nvidia-l4"],
        help="GPU 가속기 이름입니다. hétérogènes 리소스를 위해 하나 이상 공급할 수 있습니다.",
    )

    arg_parser = util.add_resource_arguments(arg_parser, cluster_create_defaults=True)

    arg_parser.add_argument(
        "--num_clusters",
        type=int,
        default=1,
        help="생성할 Ray 클러스터의 수입니다.",
    )
    arg_parser.add_argument(
        "--num_head_cpu",
        type=float,  # 부분적인 CPU 헤드 스케줄링을 가능하게 함
        default=8,
        help="Ray 헤드에 할당할 CPU의 수입니다.",
    )

    arg_parser.add_argument("--head_ram_gb", type=int, default=8, help="Ray 헤드에 할당할 RAM의 기가바이트 수")
    args = arg_parser.parse_args()
    return util.fill_in_missing_resources(args, cluster_creation_flag=True)


def main():
    args = parse_args()

    if "head" in args.name:
        raise ValueError("다른 스크립트와의 호환성을 위해 이름에 head를 포함하지 마세요")
    if args.num_clusters == 1:
        apply_manifest(args)
    else:
        default_name = args.name
        for i in range(args.num_clusters):
            args.name = default_name + "-" + str(i)
            apply_manifest(args)


if __name__ == "__main__":
    main()
```

## Docker 기반 로컬 퀵스타트

먼저, [Docker 가이드](https://isaac-sim.github.io/IsaacLab/main/source/deployment/docker.html)를 따라
NVIDIA 컨테이너 툴킷과 Docker Compose를 설정합니다.

그런 다음, 튜닝 실행을 시작하기 위해 다음 단계를 따르세요.

```bash
# 베이스 이미지를 빌드하지만 실행할 필요는 없습니다
python3 docker/container.py start && python3 docker/container.py stop
# 추가 의존성이 포함된 튜닝 이미지 빌드
docker build -t isaacray -f scripts/reinforcement_learning/ray/cluster_configs/Dockerfile .
# 튜닝 이미지 시작 - 소스 폴더의 변경사항이 컨테이너에 반영되도록 symlink 사용
docker run -v $(pwd)/source:/workspace/isaaclab/source -it --gpus all --net=host --entrypoint /bin/bash isaacray
# 튜닝 이미지 내에서 Ray 서버 시작
echo "import ray; ray.init(); import time; [time.sleep(10) for _ in iter(int, 1)]" | ./isaaclab.sh -p
```

다른 터미널에서 다음을 실행합니다.

```bash
# 새로운 터미널에서 (위의 터미널을 닫지 않은 채) 새 셸과 함께 이미지에 진입합니다.
docker container ps
docker exec -it <ISAAC_RAY_IMAGE_ID_FROM_CONTAINER_PS> /bin/bash
# 각 GPU당 한 개의 병렬 워커로 튜닝 실행 시작
./isaaclab.sh -p scripts/reinforcement_learning/ray/tuner.py \
  --cfg_file scripts/reinforcement_learning/ray/hyperparameter_tuning/vision_cartpole_cfg.py \
  --cfg_class CartpoleTheiaJobCfg \
  --run_mode local \
  --workflow scripts/reinforcement_learning/rl_games/train.py \
  --num_workers_per_node <NUMBER_OF_GPUS_IN_COMPUTER>
```

훈련 로그를 보려면 다른 터미널에서 다음을 실행하고, 이후에 브라우저에서 `localhost:6006`을 방문하세요.

```bash
# 새로운 터미널에서 (위의 터미널을 닫지 않은 채) 새 셸과 함께 이미지에 진입합니다.
docker container ps
docker exec -it <ISAAC_RAY_IMAGE_ID_FROM_CONTAINER_PS> /bin/bash
# 각 GPU당 한 개의 병렬 워커로 튜닝 실행 시작
tensorboard --logdir=.
```

자동 튜닝 실행 대신 자원 래핑된 개별 작업을 제출하는 방법은 다음 파일에 설명되어 있습니다.

### scripts/reinforcement_learning/ray/wrap_resources.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 특정 클러스터의 GPU 활성화된 노드에서 작업자(s)에게
서브-잡(s) (개별 작업, 튜닝 작업의 경우 :file:`tuner.py` 사용)을
자원 래핑된 집계 작업의 일부로 전달합니다. 각 서브-잡에 원하는 컴퓨팅 리소스가 지정되지 않은 경우,
이 스크립트는 클러스터의 각 노드에서 GPU가 있는 노드당 한 명의 작업자를 생성합니다.
각 서브-잡에 원하는 리소스가 지정된 경우,
원하는 리소스로 가능한 최대 수의 작업자를 GPU가 있는 각 노드에 대해 생성합니다.
또한, ``--num_workers`` 플래그를 사용하여 각 노드의 사용 가능한 리소스를
원하는 작업자 수로 나누어, 다중 GPU 노드에서 서브-잡을 쉽게 병렬화할 수 있습니다.
Isaac Lab이 GPU를 필요로 하므로, 로거와 같은 CPU 전용 노드는 모두 무시됩니다.

서브-잡은 다음과 같은 관계를 통해 클러스터의 노드(s)와 매칭됩니다:
sorted_nodes = GPU 기준으로 내림차순 정렬된 노드, 다음으로 CPU 기준 내림차순, 다음으로 RAM 기준 내림차순, 마지막으로 노드 ID 기준
node_submitted_to = sorted_nodes[job_index % total_node_count]

정렬된 노드의 순서를 확인하려면 ``--test`` 인수를 제공하고 스크립트를 실행하세요.

서브-잡은 + 구분자로 구분됩니다. ``--sub_jobs`` 인수는 스크립트에 제공되는 마지막 인수여야 합니다.

사용 가능한 워커가 하나 이상이고 서브-잡이 하나 이상인 경우,
서브-잡은 사용 가능해진 순서대로 작업자에게 병렬로 실행됩니다. 서브-잡이 워커보다 많은 경우,
서브-잡은 워커가 사용 가능해질 때마다 작업자에게 전달됩니다. 제출할 수 있는
서브-잡의 수는 제한이 없습니다.

이 스크립트는 Ray 클러스터 헤드 노드에서 집계 클러스터 작업으로 실행되도록 의도되었습니다.
이 스크립트와 같은 집계 클러스터 작업을 하나 이상의 원격 클러스터에 제출하려면
:file:`../submit_isaac_ray_job.py`를 참조하세요.

Google GKE의 KubeRay 클러스터는 :file:`../launch.py`를 사용하여 생성할 수 있습니다.

사용법:

.. code-block:: bash
    # **서브-잡이 ``+`` 구분자로 분리되어 있는지 확인하세요.**
    # 일반 템플릿-----------------------------------
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py -h
    # 리소스 격리 없음; 병렬화 없음:
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py
    --sub_jobs <JOB0>+<JOB1>+<JOB2>
    # 자동 리소스 격리; 예시 A: 병렬화를 위해 필요
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py \
    --num_workers <NUM_TO_DIVIDE_TOTAL_RESOURCES_BY> \
    --sub_jobs <JOB0>+<JOB1>
    # 수동 리소스 격리; 예시 B: 병렬화를 위해 필요
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py --num_cpu_per_worker <CPU> \
    --gpu_per_worker <GPU> --ram_gb_per_worker <RAM> --sub_jobs <JOB0>+<JOB1>
    # 수동 리소스 격리; 예시 C: 이질적 워크로드를 위한 병렬화 필요
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py --num_cpu_per_worker <CPU> \
    --gpu_per_worker <GPU1> <GPU2> --ram_gb_per_worker <RAM> --sub_jobs <JOB0>+<JOB1>
    # 모든 인수 보기
    ./isaaclab.sh -p scripts/reinforcement_learning/ray/wrap_resources.py -h
"""

import argparse

import util


def wrap_resources_to_jobs(jobs: list[str], args: argparse.Namespace) -> None:
    """
    제공된 작업 목록에 대해, 리소스 제약 조건에 의해 다르게 지정되지 않는 한
    사용 가능한 노드당 하나의 작업자에 작업을 전달합니다.

    Args:
        jobs: Ray 클러스터에서 실행할 bash 명령어
        args: 리소스 할당을 위한 인수

    """
    job_objs = []
    util.ray_init(
        ray_address=args.ray_address,
        runtime_env={
            "py_modules": None if not args.py_modules else args.py_modules,
        },
        log_to_driver=False,
    )
    gpu_node_resources = util.get_gpu_node_resources(include_id=True, include_gb_ram=True)

    if any([args.gpu_per_worker, args.cpu_per_worker, args.ram_gb_per_worker]) and args.num_workers:
        raise ValueError("num_workers만 지정하거나granular resources(GPU, CPU, RAM_GB)만 지정하세요.")

    num_nodes = len(gpu_node_resources)
    # 인수 채우기
    formatted_node_resources = {
        "gpu_per_worker": [gpu_node_resources[i]["GPU"] for i in range(num_nodes)],
        "cpu_per_worker": [gpu_node_resources[i]["CPU"] for i in range(num_nodes)],
        "ram_gb_per_worker": [gpu_node_resources[i]["ram_gb"] for i in range(num_nodes)],
        "num_workers": args.num_workers,  # 기본적으로 노드당 1 워커
    }
    args = util.fill_in_missing_resources(args, resources=formatted_node_resources, policy=min)
    print(f"[INFO]: 발견된 GPU 노드 수: {num_nodes}")
    if args.test:
        jobs = ["nvidia-smi"] * num_nodes
    for i, job in enumerate(jobs):
        gpu_node = gpu_node_resources[i % num_nodes]
        print(f"[INFO]: 작업 {i + 1}개 중 {len(jobs)}개에 작업 '{job}'을 노드 {gpu_node}에 할당 중입니다")
        print(
            f"[INFO]: 리소스 매개변수: GPU: {args.gpu_per_worker[i]}"
            f" CPU: {args.cpu_per_worker[i]} RAM {args.ram_gb_per_worker[i]}"
        )
        print(f"[INFO] 노드 매개변수에 대해 {args.num_workers[i]}개의 워커 생성 중")
        num_gpus = args.gpu_per_worker[i] / args.num_workers[i]
        num_cpus = args.cpu_per_worker[i] / args.num_workers[i]
        memory = (args.ram_gb_per_worker[i] * 1024**3) / args.num_workers[i]
        job_objs.append(
            util.Job(
                cmd=job,
                name=f"Job-{i + 1}",
                resources=util.JobResource(num_gpus=num_gpus, num_cpus=num_cpus, memory=memory),
                node=util.JobNode(
                    specific="node_id",
                    node_id=gpu_node["id"],
                ),
            )
        )
    # 작업 제출
    util.submit_wrapped_jobs(jobs=job_objs, test_mode=args.test, concurrent=False)


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="선택적 GPU 테스트와 함께 여러 작업 제출.")
    parser = util.add_resource_arguments(arg_parser=parser)
    parser.add_argument("--ray_address", type=str, default="auto", help="Ray 주소입니다.")
    parser.add_argument(
        "--test",
        action="store_true",
        help=(
            "임의의 작업 대신 nvidia-smi 테스트를 실행합니다."
            "작업 실행 전 GPU 리소스가 올바르게 격리되었는지 확인하기 위한 사전 점검으로 사용할 수 있습니다."
        ),
    )
    parser.add_argument(
        "--py_modules",
        type=str,
        nargs="*",
        default=[],
        help=(
            "작업 실행 전에 추가할 파이썬 모듈 또는 경로 목록입니다. 예를 들어: --py_modules my_package/my_package"
        ),
    )
    parser.add_argument(
        "--sub_jobs",
        type=str,
        nargs=argparse.REMAINDER,
        help="이것은 마지막 래퍼 인수여야 합니다. 클러스터에서 실행하기 위해 + 구분자로 구분된 작업들입니다.",
    )
    args = parser.parse_args()
    if args.sub_jobs is not None:
        jobs = " ".join(args.sub_jobs)
        formatted_jobs = jobs.split("+")
    else:
        formatted_jobs = []
    print(f"[INFO]: Isaac Ray 래퍼가 받은 작업 {formatted_jobs=}")
    wrap_resources_to_jobs(jobs=formatted_jobs, args=args)
```

`task_runner.py`는 단일 선언적 YAML 파일을 통해 Ray 클러스터에 Python 작업을 디스패치합니다. 이 접근 방식은 사용자가 각 실행에 추가 pip 패키지와 Python 모듈을 지정할 수 있도록 합니다. 세밀한 리소스 할당이 지원되며, 각 작업에 할당된 CPU, GPU 및 메모리 수를 명시적으로 제어할 수 있습니다. 실행기는 또한 고급 스케줄링 기능을 제공합니다: 작업은 호스트명 또는 노드 ID별로 특정 노드로 제한될 수 있으며, 두 가지 실행 모드를 지원합니다: 작업은 리소스가 사용 가능해질 때 개별적으로 실행될 수 있고, 또는 동시에 배치로 그룹화될 수 있습니다—멀티노드 훈련 작업에 이상적이며—클러스터 전반에 충분한 리소스가 사용 가능할 때만 모든 작업이 함께 시작되도록 보장합니다.

### scripts/reinforcement_learning/ray/task_runner.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 Ray 클러스터의 워커에 하나 이상의 사용자 정의 Python 작업을 디스패치합니다.
각 작업, 리소스 요구 사항 및 실행 매개변수는 YAML 구성 파일에서 지정됩니다.
사용자는 구성 파일을 통해 각 작업에 할당할 CPU, GPU 및 메모리 수를 정의할 수 있습니다.

주요 기능:
-------------
- 구성 필드(`num_gpus`, `num_cpus`, `memory`)를 통한 세밀한 개별 작업 리소스 관리.
- Ray 클러스터의 사용 가능한 리소스를 활용한 여러 작업의 병렬 실행.
- 작업 친화성 지정 옵션, 예를 들어 호스트명, 노드 ID 또는 모든 노드별로 지정 가능.
- 선택적 배치(동시) 또는 개별 작업 스케줄링 옵션.

작업 스케줄링 및 분산은 Ray의 내장 리소스 관리자를 통해 처리됩니다.

YAML 구성 필드:
--------------------------
- `pip`: 작업 실행 전 추가로 설치할 pip 패키지 목록.
- `py_modules`: 런타임 환경에 포함할 추가 Python 모듈 경로 목록(디렉터리 또는 파일).
- `concurrent`: (bool) 작업 디스패치 semantics를 결정합니다:
    - `concurrent: true`인 경우, **모든 작업이 배치로 스케줄링됩니다**. 스크립트는 배치 내 모든 작업에 sufficient한 리소스가 클러스터에서 사용 가능해질 때까지 기다렸다가, 모든 작업을 함께 실행합니다. 리소스가 부족한 경우, 모든 작업이 클러스터가 전체 배치를 지원할 수 있을 때까지 차단됩니다.
    - `concurrent: false`인 경우, 각 작업에 대해 리소스가 사용 가능해질 때마다 작업이 즉시 실행되며, Ray가 개별적으로 스케줄링합니다. 이로 인해 작업 시작 시간이 동기화되지 않을 수 있습니다.
- `tasks`: 다음을 포함한 작업 사양 목록:
    - `name`: 작업의 문자열 식별자.
    - `py_args`: Python 인터프리터에 전달할 인수(예: 스크립트/모듈, 플래그, 사용자 인수).
    - `num_gpus`: 할당할 GPU 수(부동소수점 또는 문자열 산술 표현식, 예: "2*2").
    - `num_cpus`: 할당할 CPU 수(부동소수점 또는 문자열).
    - `memory`: 바이트 단위의 RAM 양(정수 또는 문자열).
    - `node` (선택 사항): 노드 배치 제약 조건.
        - `specific` (str): 노드 배치 유형, `hostname`, `node_id` 또는 `any` 중 하나 지원.
            - `any`: 사용 가능한 어떤 노드에도 작업을 배치합니다.
            - `hostname`: 특정 호스트명에 작업을 배치합니다. `hostname`은 노드 필드에서 지정해야 합니다.
            - `node_id`: 특정 노드 ID에 작업을 배치합니다. `node_id`는 노드 필드에서 지정해야 합니다.
        - `hostname` (str): 작업을 배치할 특정 호스트명.
        - `node_id` (str): 작업을 배치할 특정 노드 ID.


일반적인 사용법:
--------------

.. code-block:: bash

    # 도움말 및 인수 세부 정보 출력:
    python task_runner.py -h

    # YAML 파일에 정의된 작업을 Ray 클러스터에 제출( Ray 헤드 주소 자동 감지):
    python task_runner.py --task_cfg /path/to/tasks.yaml

YAML 구성 예시-1:
-----------------------------

.. code-block:: yaml

    pip: ["xxx"]
    py_modules: ["my_package/my_package"]
    concurrent: false
    tasks:
      - name: "Isaac-Cartpole-v0"
        py_args: "-m torch.distributed.run --nnodes=1 --nproc_per_node=2  --rdzv_endpoint=localhost:29501 /workspace/isaaclab/scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Cartpole-v0 --max_iterations 200 --headless --distributed"
        num_gpus: 2
        num_cpus: 10
        memory: 10737418240
      - name: "script need some dependencies"
        py_args: "script.py --option arg"
        num_gpus: 0
        num_cpus: 1
        memory: 10*1024*1024*1024

YAML 구성 예시-2:
-----------------------------

.. code-block:: yaml

    pip: ["xxx"]
    py_modules: ["my_package/my_package"]
    concurrent: true
    tasks:
    - name: "Isaac-Cartpole-v0-multi-node-train-1"
        py_args: "-m torch.distributed.run --nproc_per_node=1 --nnodes=2 --node_rank=0 --rdzv_id=123 --rdzv_backend=c10d --rdzv_endpoint=localhost:5555 /workspace/isaaclab/scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Cartpole-v0 --headless --distributed --max_iterations 1000"
        num_gpus: 1
        num_cpus: 10
        memory: 10*1024*1024*1024
        node:
          specific: "hostname"
          hostname: "xxx"
    - name: "Isaac-Cartpole-v0-multi-node-train-2"
        py_args: "-m torch.distributed.run --nproc_per_node=1 --nnodes=2 --node_rank=1 --rdzv_id=123 --rdzv_backend=c10d --rdzv_endpoint=x.x.x.x:5555 /workspace/isaaclab/scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Cartpole-v0 --headless --distributed --max_iterations 1000"
        num_gpus: 1
        num_cpus: 10
        memory: 10*1024*1024*1024
        node:
          specific: "hostname"
          hostname: "xxx"

작업을 조기에 중단하려면 Ctrl+C를 누르세요; 스크립트는 모든 실행 중인 Ray 작업을 취소합니다.
"""  # noqa: E501

import argparse
import ast
import operator
from datetime import datetime

import yaml

# Local imports
import util  # isort: skip

# Safe operators for arithmetic expression evaluation
_SAFE_OPERATORS = {
    ast.Add: operator.add,
    ast.Sub: operator.sub,
    ast.Mult: operator.mul,
    ast.Div: operator.truediv,
    ast.FloorDiv: operator.floordiv,
    ast.Pow: operator.pow,
    ast.Mod: operator.mod,
    ast.USub: operator.neg,
    ast.UAdd: operator.pos,
}


def safe_eval_arithmetic(expr: str) -> int | float:
    """
    산술 표현식만 포함된 문자열을 안전하게 평가합니다.

    +, -, *, /, //, **, % 및 숫자 리터럴을 지원합니다.
    산술이 아닌 표현식의 경우 ValueError를 발생시킵니다.

    Args:
        expr: 산술 표현식을 포함한 문자열(예: "10*1024*1024").

    Returns:
        표현식의 숫자 결과.

    Raises:
        ValueError: 표현식에 산술이 아닌 작업이 포함된 경우.
    """

    def _eval_node(node: ast.AST) -> int | float:
        if isinstance(node, ast.Expression):
            return _eval_node(node.body)
        elif isinstance(node, ast.Constant) and isinstance(node.value, (int, float)):
            return node.value
        elif isinstance(node, ast.BinOp) and type(node.op) in _SAFE_OPERATORS:
            left = _eval_node(node.left)
            right = _eval_node(node.right)
            return _SAFE_OPERATORS[type(node.op)](left, right)
        elif isinstance(node, ast.UnaryOp) and type(node.op) in _SAFE_OPERATORS:
            operand = _eval_node(node.operand)
            return _SAFE_OPERATORS[type(node.op)](operand)
        else:
            raise ValueError(f"Unsafe expression: {ast.dump(node)}")

    try:
        tree = ast.parse(expr.strip(), mode="eval")
        return _eval_node(tree)
    except (SyntaxError, TypeError) as e:
        raise ValueError(f"Invalid arithmetic expression: {expr}") from e


def parse_args() -> argparse.Namespace:
    """
    Ray 작업 실행기를 위한 명령줄 인수를 파싱합니다.

    Returns:
        파싱된 CLI 인수를 포함하는 네임스페이스:
            - task_cfg (str): YAML 작업 파일 경로.
            - ray_address (str): Ray 클러스터 주소.
            - test (bool): GPU 리소스 격리 sanity 검사를 실행할지 여부.
    """
    parser = argparse.ArgumentParser(description="YAML 구성 파일의 작업을 실행합니다.")
    parser.add_argument("--task_cfg", type=str, required=True, help="YAML 작업 파일 경로.")
    parser.add_argument("--ray_address", type=str, default="auto", help="Ray 주소.")
    parser.add_argument(
        "--test",
        action="store_true",
        help=(
            "임의의 작업 대신 nvidia-smi 테스트를 실행합니다."
            "작업을 실행하기 전에 GPU 리소스가 올바르게 격리되었는지 확인하는 데 사용할 수 있습니다."
        ),
    )
    return parser.parse_args()


def parse_task_resource(task: dict) -> util.JobResource:
    """
    YAML 구성에서 작업 리소스 요구 사항을 파싱합니다.

    Args:
        task (dict): 단일 작업의 구성을 나타내는 딕셔너리.
            키에는 `num_gpus`, `num_cpus`, `memory`가 포함될 수 있으며, 각각 숫자 또는 평가 가능한 문자열 표현식일 수 있습니다.

    Returns:
        util.JobResource: 파싱된 값을 갖는 리소스 객체.
    """
    resource = util.JobResource()
    if "num_gpus" in task:
        value = task["num_gpus"]
        resource.num_gpus = safe_eval_arithmetic(value) if isinstance(value, str) else value
    if "num_cpus" in task:
        value = task["num_cpus"]
        resource.num_cpus = safe_eval_arithmetic(value) if isinstance(value, str) else value
    if "memory" in task:
        value = task["memory"]
        resource.memory = safe_eval_arithmetic(value) if isinstance(value, str) else value
    return resource


def run_tasks(
    tasks: list[dict], args: argparse.Namespace, runtime_env: dict | None = None, concurrent: bool = False
) -> None:
    """
    Ray 클러스터에 작업을 제출하여 실행합니다.

    Args:
        tasks (list[dict]): 작업 구성 딕셔너리 목록.
        args (argparse.Namespace): 파싱된 명령줄 인수.
        runtime_env (dict | None): Ray 런타임 환경 구성으로 포함하는 내용:
            - pip (list[str] | None): 설치할 추가 pip 패키지.
            - py_modules (list[str] | None): 환경에 포함할 Python 모듈.
        concurrent (bool): 작업을 배치로 동시에 실행할지, 또는 리소스가 사용 가능해질 때마다 개별적으로 실행할지 여부.

    Returns:
        None
    """
    job_objs = []
    util.ray_init(ray_address=args.ray_address, runtime_env=runtime_env, log_to_driver=False)
    for task in tasks:
        resource = parse_task_resource(task)
        print(f"[INFO] Creating job {task['name']} with resource={resource}")
        job = util.Job(
            name=task["name"],
            py_args=task["py_args"],
            resources=resource,
            node=util.JobNode(
                specific=task.get("node", {}).get("specific"),
                hostname=task.get("node", {}).get("hostname"),
                node_id=task.get("node", {}).get("node_id"),
            ),
        )
        job_objs.append(job)
    start = datetime.now()
    print(f"[INFO] Creating {len(job_objs)} jobs at {start.strftime('%H:%M:%S.%f')} with runtime env={runtime_env}")
    # 작업 제출
    util.submit_wrapped_jobs(
        jobs=job_objs,
        test_mode=args.test,
        concurrent=concurrent,
    )
    end = datetime.now()
    print(
        f"[INFO] All jobs completed at {end.strftime('%H:%M:%S.%f')}, took {(end - start).total_seconds():.2f} seconds."
    )


def main() -> None:
    """
    Ray 작업 실행기 스크립트의 메인 진입점입니다.

    YAML 작업 구성 파일을 읽고, CLI 인수를 파싱하고, Ray 클러스터에 작업을 디스패치합니다.

    Returns:
        None
    """
    args = parse_args()
    with open(args.task_cfg) as f:
        config = yaml.safe_load(f)
    tasks = config["tasks"]
    runtime_env = {
        "pip": None if not config.get("pip") else config["pip"],
        "py_modules": None if not config.get("py_modules") else config["py_modules"],
    }
    concurrent = config.get("concurrent", False)
    run_tasks(
        tasks=tasks,
        args=args,
        runtime_env=runtime_env,
        concurrent=concurrent,
    )


if __name__ == "__main__":
    main()
```

이 스크립트를 사용하려면 다음 명령과 유사한 명령을 실행합니다(`tasks.yaml`을 실제 구성 파일로 바꿉니다):

```bash
python3 scripts/reinforcement_learning/ray/submit_job.py --aggregate_jobs task_runner.py --task_cfg tasks.yaml
```

`tasks.yaml` 파일을 작성하는 방법에 대한 자세한 지침은 `task_runner.py`의 주석을 참조하세요.

**팁:** `tasks.yaml` 파일을 `scripts/reinforcement_learning/ray` 디렉터리에 배치하여 `working_dir`이 업로드될 때 포함되도록 합니다. 그런 다음 명령에서 상대 경로를 사용하여 참조할 수 있습니다.

실행 중인 컨테이너에서 파일을 전송하는 방법은 다음과 같습니다.

```bash
docker container ps
docker cp <ISAAC_RAY_IMAGE_ID_FROM_CONTAINER_PS>:</path/in/container/file>  </path/on/host/>
```

튜닝 작업의 경우, 튜닝 작업/하이퍼파라미터 스위프를 `JobCfg`의 자식 클래스로 지정합니다.
제공된 `JobCfg`는 환경 진입점과 hydra 인수의 차이로 인해 `rl_games` 워크플로만 지원하지만, 호환 가능한 `JobCfg`가 제공되면 다른 워크플로도 작동합니다.

### scripts/reinforcement_learning/ray/tuner.py (JobCfg 정의)

```python
class JobCfg:
    """invoke_tuning_run 메서드와 IsaacLabTuneTrainable 클래스와 호환되기 위해,
    최소한 튜닝 작업은 이 클래스를 상속해야 합니다."""

    def __init__(self, cfg: dict):
        """
        runner 인수는 task에 전달된 명령줄 인수를 포함합니다.
        예를 들어:
        cfg["runner_args"]["headless_singleton"] = "--headless"
        cfg["runner_args"]["enable_cameras_singleton"] = "--enable_cameras"
        """
        assert "runner_args" in cfg, "런너 인수가 지정되지 않았습니다."
        """
        Task는 훈련할 원하는 작업을 나타냅니다. 예를 들어:
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-TheiaTiny-v0"])
        """
        assert "--task" in cfg["runner_args"], "작업이 지정되지 않았습니다."
        """
        Hydra 인수는 스위프 내에서 다양하게 변하는 하이퍼파라미터를 정의합니다. 예를 들어:
        cfg["hydra_args"]["agent.params.network.cnn.activation"] = tune.choice(["relu", "elu"])
        """
        assert "hydra_args" in cfg, "하이퍼파라미터가 지정되지 않았습니다."
        self.cfg = cfg
```

다음 Cartpole 예제 구성을 참조하세요.

### scripts/reinforcement_learning/ray/hyperparameter_tuning/vision_cartpole_cfg.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause
import pathlib
import sys
from typing import Any

# Allow for import of items from the ray workflow.
CUR_DIR = pathlib.Path(__file__).parent
UTIL_DIR = CUR_DIR.parent
sys.path.extend([str(UTIL_DIR), str(CUR_DIR)])
import util
import vision_cfg
from ray import tune
from ray.tune.progress_reporter import CLIReporter
from ray.tune.stopper import Stopper


class CartpoleRGBNoTuneJobCfg(vision_cfg.CameraJobCfg):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-v0"])
        super().__init__(cfg, vary_env_count=False, vary_cnn=False, vary_mlp=False)


class CartpoleRGBCNNOnlyJobCfg(vision_cfg.CameraJobCfg):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-v0"])
        super().__init__(cfg, vary_env_count=False, vary_cnn=True, vary_mlp=False)


class CartpoleRGBJobCfg(vision_cfg.CameraJobCfg):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-v0"])
        super().__init__(cfg, vary_env_count=True, vary_cnn=True, vary_mlp=True)


class CartpoleResNetJobCfg(vision_cfg.ResNetCameraJob):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-ResNet18-v0"])
        super().__init__(cfg)


class CartpoleTheiaJobCfg(vision_cfg.TheiaCameraJob):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-TheiaTiny-v0"])
        super().__init__(cfg)


class CustomCartpoleProgressReporter(CLIReporter):
    def __init__(self):
        super().__init__(
            metric_columns={
                "training_iteration": "iter",
                "time_total_s": "총 시간 (s)",
                "Episode/Episode_Reward/alive": "생존",
                "Episode/Episode_Reward/cart_vel": "카트 속도",
                "rewards/time": "보상/시간",
            },
            max_report_frequency=5,
            sort_by_metric=True,
        )


class CartpoleEarlyStopper(Stopper):
    def __init__(self):
        self._bad_trials = set()

    def __call__(self, trial_id: str, result: dict[str, Any]) -> bool:
        iter = result.get("training_iteration", 0)
        out_of_bounds = result.get("Episode/Episode_Termination/cart_out_of_bounds")

        # 조건이 충족되면 시도 중지 대상으로 표시
        if iter >= 20 and out_of_bounds is not None and out_of_bounds > 0.85:
            self._bad_trials.add(trial_id)

        return trial_id in self._bad_trials

    def stop_all(self) -> bool:
        return False  # 개별 시도만 중지
```

## 원격 클러스터

실행 및 전송된 작업을 수락하고 실행하는 Ray 클러스터를 생성하기 위한 다음 방법 중 하나를 선택합니다.

### KubeRay 설정

Google GKE에서 배터리가 포함된 클러스터 실행 파일을 사용하여 KubeRay 클러스터를 사용하는 경우, 다음 종속 항목도 필요합니다.

```bash
python3 -p -m pip install kubernetes Jinja2
```

Google Kubernetes Engine 또는 Amazon Elastic Kubernetes Service와 같은 Kubernetes 클러스터에서 KubeRay를 사용하려면 `kubectl`이 필요하며,
[Kubernetes 웹사이트](https://kubernetes.io/docs/tasks/tools/) 를 통해 설치할 수 있습니다.

Google Cloud는 현재 유일한 테스트된 플랫폼이지만, 다음을 구성하면 모든 클라우드 공급자가 작동해야 합니다.

#### 주의
`ray` 명령은 Isaac Python을 사용하도록 수정해야 하며, 이는 다음과 유사한 방식으로 달성할 수 있습니다.
`sed -i "1i $(echo "#!/workspace/isaaclab/_isaac_sim/python.sh")" \
/isaac-sim/kit/python/bin/ray && ln -s /isaac-sim/kit/python/bin/ray /usr/local/bin/ray`.

- NGC, GCS 아티팩트 레지스트리, AWS ECR 등의 컨테이너 레지스트리(Isaac Lab 이미지가 Ray를 지원하도록 구성되어 있어야 함). `cluster_configs/Dockerfile`을 참조하여 `isaac-lab-base` 컨테이너를 Ray 호환성을 위해 수정하는 방법을 확인하세요. Ray는 Isaac Sim Python 셰뱅을 사용해야 하며, 컨테이너 내에서 `nvidia-smi`가 작동해야 합니다. 경로를 올바르게 구성하는 데 주의하세요. 그렇지 않으면 모든 것이 정상적으로 작동하지 않을 수 있습니다. 베이스 이미지가 컨테이너 가이드에 따라 이미 빌드된 경우, 예시 Docker 파일은 레지스트리로 푸시하기 전에 문제 없이 작동할 가능성이 높습니다.
- 사용 가능한 NVIDIA RTX (가장 가능성이 높은 `l4` 또는 `l40` 또는 `tesla-t4` 또는 `a10`) GPU 패스스루 노드 풀 리소스가 있는 Kubernetes 설정으로, 컨테이너 레지스토리/스토리지 버킷에 접근할 수 있고, 올바른 IAM 권한으로 Ray 연산자가 활성화되어 있어야 합니다. 이는 Google GKE 또는 AWS EKS와 같은 서비스를 통해 쉽게 달성할 수 있으며, 계정 또는 조직에 GPU 예산이 할당된 경우에 특히 그렇습니다. 비용 효율적인 실험을 위해서는 "오토파일럿" 서비스보다는 수동 Kubernetes 서비스를 사용하는 것이 좋습니다. 이렇게 하면 사용하지 않을 때 클러스터를 완전히 종료할 수 있기 때문입니다. 다만 [NVIDIA GPU 연산자](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/google-gke.html)를 설치해야 할 수도 있습니다.
- 클러스터에 접근 가능한 [MLFlow 서버](https://mlflow.org/docs/latest/getting-started/logging-first-model/step1-tracking-server.html)(Google Cloud의 경우 이미 포함되어 있으며, 형식 및 MLFlow 통합을 참조할 수 있음).
- 리소스 할당 방법을 설명하는 `kuberay.yaml.ninja` 파일(Google Cloud의 경우 이미 포함되어 있으며, 형식 및 MLFlow 통합을 참조할 수 있음).

### Ray 클러스터 (Kubernetes 없이) 설정

#### 주의
KubeRay 클러스터와 마찬가지로 Ray 명령을 Isaac Python을 사용하도록 수정하고, 이미지/클러스터 권한 생성 단계를 따라야 합니다.

자세한 내용은 [Ray 클러스터 개요](https://docs.ray.io/en/latest/cluster/getting-started.html) 또는 [Anyscale](https://www.anyscale.com/product)을 참조하세요.

또한, 로컬 호스트와 클러스터가 모두 접근할 수 있는 [MLFlow 서버](https://mlflow.org/docs/latest/getting-started/logging-first-model/step1-tracking-server.html)를 생성해야 합니다.

### KubeRay와 순수 Ray 공통 단계 파트 I

1.) 로컬 머신에 Ray를 설치합니다.

```bash
python3 -p -m pip install ray[default]==2.31.0
```

2.) Isaac Ray 이미지를 빌드하고 선택한 컨테이너 레지스트리에 업로드합니다.

```bash
# 먼저 NGC (nvcr.io) 레지스트리에 로그인하고, 저장소의 docker 단계를 참조하세요.
python3 docker/container.py start
# 특수 Isaac Lab Ray 이미지 빌드
docker build -t <REGISTRY/IMAGE_NAME> -f scripts/reinforcement_learning/ray/cluster_configs/Dockerfile .
# 선택한 레지스트리에 이미지 푸시
docker push <REGISTRY/IMAGE_NAME>
```

### KubeRay 클러스터 전용

[k9s](https://github.com/derailed/k9s)는 클러스터를 모니터링하는 데 탁월한 도구로, `snap install k9s --devmode` 로 쉽게 설치할 수 있습니다.

1.) 클러스터 접근성을 확인하고 올바른 연산자가 설치되었는지 확인합니다.

```bash
# 클러스터 접근성 확인
kubectl cluster-info
# 수동으로 관리되는 클러스터(Autopilot 또는 이와 유사하지 않은 경우)의 경우
# 노드 풀이 존재하는지 확인
kubectl get nodes
# 클러스터에 Ray 연산자가 설치되었는지 확인
# rayclusters.ray.io , rayjobs.ray.io , rayservices.ray.io 가 나열되어야 함
kubectl get crds | grep ray
# 클러스터에 NVIDIA 드라이버 연산자가 설치되었는지 확인
# clusterpolicies.nvidia.com 이 나열되어야 함
kubectl get crds | grep nvidia
```

2.) KubeRay 클러스터와 로그를 수신하는 MLFlow 서버를 생성합니다.
클러스터가 이 MLFlow 서버에 접근할 수 있도록 해야 합니다.
Google GKE의 경우, 다음 생성 파일에 지침이 포함되어 있어 자동으로 수행할 수 있습니다.

### scripts/reinforcement_learning/ray/launch.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""이 스크립트는 하나 이상의 KubeRay 클러스터를 생성하는 데 도움을 줍니다.

사용법:

.. code-block:: bash
    # 헤드 노드가 컨테이너 생성 중에 멈춰 있다면, 시크릿을 생성했는지 확인하세요
    python3 scripts/reinforcement_learning/ray/launch.py -h

    # 예시

    # 다음 명령어는 GPUx1 NVIDIA L4 워커 8개를 생성합니다
    python3 scripts/reinforcement_learning/ray/launch.py --cluster_host google_cloud \
    --namespace <NAMESPACE> --image <YOUR_ISAAC_RAY_IMAGE> \
    --num_workers 8 --num_clusters 1 --worker_accelerator nvidia-l4 --gpu_per_worker 1

    # 다음 명령어는 GPUx1 NVIDIA L4 워커 1개, GPUx2 NVIDIA TESLA T4 워커 2개,
    # 그리고 GPUx4 NVIDIA TESLA T4 GPU 워커 2개를 생성합니다
    python3 scripts/reinforcement_learning/ray/launch.py --cluster_host google_cloud \
    --namespace <NAMESPACE> --image <YOUR_ISAAC_RAY_IMAGE> \
    --num_workers 1 2 --num_clusters 1 \
    --worker_accelerator nvidia-l4 nvidia-tesla-t4 --gpu_per_worker 1 2 4
"""

import argparse
import pathlib
import subprocess

import yaml
from jinja2 import Environment, FileSystemLoader
from kubernetes import config

# Local imports
import util  # isort: skip

RAY_DIR = pathlib.Path(__file__).parent


def apply_manifest(args: argparse.Namespace) -> None:
    """제공된 Jinja 템플릿 ray.io/v1alpha1 파일에 대해,
    인수를 채우고 클러스터를 생성합니다. 또한, '---'로 구분된 나머지 파일에서
    리소스용 Kubernetes 컨테이너를 생성합니다.

    Args:
        args: 클러스터 매개변수와 관련된 가능한 인수입니다.
    """
    # Kubernetes 구성 로드
    config.load_kube_config()

    # 템플릿 로드를 위한 Jinja2 환경 설정
    templates_dir = RAY_DIR / "cluster_configs" / args.cluster_host
    file_loader = FileSystemLoader(str(templates_dir))
    jinja_env = Environment(loader=file_loader, keep_trailing_newline=True, autoescape=True)

    # 템플릿 파일 이름 정의
    template_file = "kuberay.yaml.jinja"

    # args 네임스페이스를 딕셔너리로 변환
    template_params = vars(args)

    # 템플릿 로드 및 렌더링
    template = jinja_env.get_template(template_file)
    file_contents = template.render(template_params)

    # 렌더된 템플릿의 모든 YAML 문서 파싱
    all_yamls = []
    for doc in yaml.safe_load_all(file_contents):
        all_yamls.append(doc)

    # 여러 문서를 보존하면서 YAML 문자열로 변환
    cleaned_yaml_string = ""
    for i, doc in enumerate(all_yamls):
        if i > 0:
            cleaned_yaml_string += "\n---\n"
        cleaned_yaml_string += yaml.dump(doc)

    # kubectl을 사용하여 Kubernetes 매니페스트 적용
    try:
        print(cleaned_yaml_string)
        subprocess.run(["kubectl", "apply", "-f", "-"], input=cleaned_yaml_string, text=True, check=True)
    except subprocess.CalledProcessError as e:
        exit(f"`kubectl` 실행 중 오류가 발생했습니다: {e}")


def parse_args() -> argparse.Namespace:
    """
    Kubernetes 배포 스크립트에 대한 명령줄 인수를 구문 분석합니다.

    Returns:
        argparse.Namespace: 구문 분석된 명령줄 인수입니다.
    """
    arg_parser = argparse.ArgumentParser(
        description="Ray 클러스터용 Kubernetes 오브젝트를 생성하기 위한 매니페스트를 적용하는 스크립트입니다.",
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
    )

    arg_parser.add_argument(
        "--cluster_host",
        type=str,
        default="google_cloud",
        choices=["google_cloud"],
        help=(
            "cluster_configs 디렉터리에서 tune.yaml.jinja"
            "파일이 존재하는 폴더의 이름입니다. 현재는 google_cloud만 지원됩니다."
        ),
    )

    arg_parser.add_argument(
        "--name",
        type=str,
        required=False,
        default="isaacray",
        help="Kubernetes 배포의 이름입니다.",
    )

    arg_parser.add_argument(
        "--namespace",
        type=str,
        required=False,
        default="default",
        help="Ray 클러스터를 배포할 Kubernetes 네임스페이스입니다.",
    )

    arg_parser.add_argument(
        "--service_acount_name", type=str, required=False, default="default", help="사용할 서비스 계정 이름입니다."
    )

    arg_parser.add_argument(
        "--image",
        type=str,
        required=True,
        help="Ray 클러스터 파드용 Docker 이미지입니다.",
    )

    arg_parser.add_argument(
        "--worker_accelerator",
        nargs="+",
        type=str,
        default=["nvidia-l4"],
        help="GPU 가속기 이름입니다. 이질적 리소스를 위해 하나 이상 제공할 수 있습니다.",
    )

    arg_parser = util.add_resource_arguments(arg_parser, cluster_create_defaults=True)

    arg_parser.add_argument(
        "--num_clusters",
        type=int,
        default=1,
        help="생성할 Ray 클러스터 수입니다.",
    )
    arg_parser.add_argument(
        "--num_head_cpu",
        type=float,  # 부분적인 CPU 헤드를 스케줄링하기 위해
        default=8,
        help="Ray 헤드에 부여할 CPU 수입니다.",
    )

    arg_parser.add_argument("--head_ram_gb", type=int, default=8, help="Ray 헤드에 줄 RAM 용량(GB)")
    args = arg_parser.parse_args()
    return util.fill_in_missing_resources(args, cluster_creation_flag=True)


def main():
    args = parse_args()

    if "head" in args.name:
        raise ValueError("다른 스크립트와의 호환성을 위해 이름에 'head'를 포함하지 마세요")
    if args.num_clusters == 1:
        apply_manifest(args)
    else:
        default_name = args.name
        for i in range(args.num_clusters):
            args.name = default_name + "-" + str(i)
            apply_manifest(args)


if __name__ == "__main__":
    main()
```

다른 클라우드 서비스의 경우, `kuberay.yaml.ninja` 파일이 Google의 경우와 유사할 것입니다.

### scripts/reinforcement_learning/ray/cluster_configs/google_cloud/kuberay.yaml.ninja

```python
# Jinja는 전체 Helm 설정이 애플리케이션에 과도하기 때문에 여기에서 템플릿용으로 사용됩니다
apiVersion: ray.io/v1alpha1
kind: RayCluster
metadata:
  name: {{ name }}
  namespace: {{ namespace }}
spec:
  rayVersion: "2.8.0"
  enableInTreeAutoscaling: true
  autoscalerOptions:
    upscalingMode: Default
    idleTimeoutSeconds: 120
    imagePullPolicy: Always
    securityContext: {}
    envFrom: []

  headGroupSpec:
    rayStartParams:
      block: "true"
      dashboard-host: 0.0.0.0
      dashboard-port: "8265"
      port: "6379"
      include-dashboard: "true"
      ray-debugger-external: "true"
      object-manager-port: "8076"
      num-gpus: "0"
      num-cpus: "0" # 헤드 노드에 작업을 스케줄링하지 않도록 방지 - 워커만 사용
    headService:
      apiVersion: v1
      kind: Service
      metadata:
        name: {{ name }}-head
      spec:
        type: LoadBalancer
    template:
      metadata:
        labels:
          app.kubernetes.io/instance: tuner
          app.kubernetes.io/name: kuberay
          cloud.google.com/gke-ray-node-type: head
      spec:
        serviceAccountName: {{ service_account_name }}
        affinity: {}
        securityContext:
          fsGroup: 100
        containers:
          - env:
            image: {{ image }}
            imagePullPolicy: Always
            name: head
            resources:
              limits:
                cpu: "{{ num_head_cpu }}"
                memory: {{ head_ram_gb }}G
                nvidia.com/gpu: "0"
              requests:
                cpu: "{{ num_head_cpu }}"
                memory: {{ head_ram_gb }}G
                nvidia.com/gpu: "0"
            securityContext: {}
            volumeMounts:
              - mountPath: /tmp/ray
                name: ray-logs
            command: ["/bin/bash", "-c", "ray start --head --port=6379 --object-manager-port=8076 --dashboard-host=0.0.0.0 --dashboard-port=8265 --include-dashboard=true && tail -f /dev/null"]
          - image: fluent/fluent-bit:1.9.6
            name: fluentbit
            resources:
              limits:
                cpu: 100m
                memory: 128Mi
              requests:
                cpu: 100m
                memory: 128Mi
            volumeMounts:
              - mountPath: /tmp/ray
                name: ray-logs
        imagePullSecrets: []
        nodeSelector:
          iam.gke.io/gke-metadata-server-enabled: "true"
        volumes:
          - configMap:
              name: fluentbit-config
            name: fluentbit-config
          - name: ray-logs
            emptyDir: {}

  workerGroupSpecs:
    {% for it in range(gpu_per_worker|length) %}
    - groupName: "{{ worker_accelerator[it] }}x{{ gpu_per_worker[it] }}-cpu-{{ cpu_per_worker[it] }}-ram-gb-{{ ram_gb_per_worker[it] }}"
      replicas: {{ num_workers[it] }}
      maxReplicas: {{ num_workers[it] }}
      minReplicas: {{ num_workers[it] }}
      rayStartParams:
        block: "true"
        ray-debugger-external: "true"
        replicas: "{{num_workers[it]}}"
      template:
        metadata:
          annotations: {}
          labels:
            app.kubernetes.io/instance: tuner
            app.kubernetes.io/name: kuberay
            cloud.google.com/gke-ray-node-type: worker
        spec:
          serviceAccountName: {{ service_account_name }}
          affinity: {}
          securityContext:
            fsGroup: 100
          containers:
            - env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "all"
              - name: NVIDIA_DRIVER_CAPABILITIES
                value: "compute,utility"

              image: {{ image }}
              imagePullPolicy: Always
              name: ray-worker
              resources:
                limits:
                  cpu: "{{ cpu_per_worker[it] }}"
                  memory: {{ ram_gb_per_worker[it] }}G
                  nvidia.com/gpu: "{{ gpu_per_worker[it] }}"
                requests:
                  cpu: "{{ cpu_per_worker[it] }}"
                  memory: {{ ram_gb_per_worker[it] }}G
                  nvidia.com/gpu: "{{ gpu_per_worker[it] }}"
              securityContext: {}
              volumeMounts:
                - mountPath: /tmp/ray
                  name: ray-logs
              command: ["/bin/bash", "-c", "ray start --address={{name}}-head.{{ namespace }}.svc.cluster.local:6379 && tail -f /dev/null"]
            - image: fluent/fluent-bit:1.9.6
              name: fluentbit
              resources:
                limits:
                  cpu: 100m
                  memory: 128Mi
                requests:
                  cpu: 100m
                  memory: 128Mi
              volumeMounts:
                - mountPath: /tmp/ray
                  name: ray-logs

          imagePullSecrets: []
          nodeSelector:
            cloud.google.com/gke-accelerator: {{ worker_accelerator[it] }}
            iam.gke.io/gke-metadata-server-enabled: "true"
          tolerations:
            - key: "nvidia.com/gpu"
              operator: "Exists"
              effect: "NoSchedule"
          volumes:
            - configMap:
                name: fluentbit-config
              name: fluentbit-config
            - name: ray-logs
              emptyDir: {}
    {% endfor %}

---
# ML Flow 서버 - 로그 가져오기용
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{name}}-mlflow
  namespace: {{ namespace }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mlflow
  template:
    metadata:
      labels:
        app: mlflow
    spec:
      containers:
      - name: mlflow
        image: ghcr.io/mlflow/mlflow:v2.9.2
        ports:
        - containerPort: 5000
        command: ["mlflow"]
        args:
        - server
        - --host=0.0.0.0
        - --port=5000
        - --backend-store-uri=sqlite:///mlflow.db
---
# ML Flow 서비스 (포트 포워딩용, kubectl port-forward service/{name}-mlflow 5000:5000)
apiVersion: v1
kind: Service
metadata:
  name: {{name}}-mlflow
  namespace: {{ namespace }}
spec:
  selector:
    app: mlflow
  ports:
  - port: 5000
    targetPort: 5000
  type: ClusterIP
```

3.) KubeRay 클러스터 IP 주소와 MLFlow 서버 IP를 가져옵니다.
KubeRay 클러스터의 경우 다음 파일에서 제공되는 지침에 따라 자동으로 수행할 수 있습니다.
KubeRay 클러스터는 파일에 저장되지만, MLFlow 서버 IP는 출력됩니다.

### scripts/reinforcement_learning/ray/grok_cluster_with_kubectl.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

import argparse
import os
import re
import subprocess
import threading
import time
from concurrent.futures import ThreadPoolExecutor, as_completed

"""
이 스크립트는 kubectl이 설치되어 있고 KubeRay를 사용하여 클러스터를 생성했다고 가정합니다.

각 클러스터에 대해 ``name: <NAME> address: http://<IP>:<PORT>`` 형태의 문자열을
새 줄에 작성하는 구성 파일을 생성하고, MLFlow URI도 가져옵니다.

사용법:

.. code-block:: bash

    python3 scripts/reinforcement_learning/ray/grok_cluster_with_kubectl.py
    # 옵션을 보려면 -h 인수를 제공하세요.
"""


def get_namespace() -> str:
    """현재 Kubernetes 네임스페이스를 컨텍스트에서 가져오고, 설정되지 않은 경우 기본값으로 대체"""
    try:
        namespace = (
            subprocess.check_output(["kubectl", "config", "view", "--minify", "--output", "jsonpath={..namespace}"])
            .decode()
            .strip()
        )
        if not namespace:
            namespace = "default"
    except subprocess.CalledProcessError:
        namespace = "default"
    return namespace


def get_pods(namespace: str = "default") -> list[tuple]:
    """지정된 네임스페이스의 모든 포드 목록을 가져옵니다"""
    cmd = ["kubectl", "get", "pods", "-n", namespace, "--no-headers"]
    output = subprocess.check_output(cmd).decode()
    pods = []
    for line in output.strip().split("\n"):
        fields = line.split()
        pod_name = fields[0]
        status = fields[2]
        pods.append((pod_name, status))
    return pods


def get_clusters(pods: list, cluster_name_prefix: str) -> set:
    """
    클러스터 이름(들)을 고유하게 가져옵니다. 헤드 노드 수에 따라 하나 이상의 클러스터에 대해 작동합니다.
    MLflow 배포를 제외합니다.
    """
    clusters = set()
    for pod_name, _ in pods:
        # MLflow 포드 건너뛰기
        if "-mlflow" in pod_name:
            continue

        match = re.match(r"(" + re.escape(cluster_name_prefix) + r"[-\w]+)", pod_name)
        if match:
            # 헤드/워커 서픽스를 제외한 기본 이름 가져오기 (워커는 건너뜁니다)
            if "head" in pod_name:
                base_name = match.group(1).split("-head")[0]
                clusters.add(base_name)
    return sorted(clusters)


def get_mlflow_info(namespace: str = None, cluster_prefix: str = "isaacray") -> str:
    """
    지정된 접두사로 네임스페이스에 MLflow 서비스가 존재하면 해당 정보를 가져옵니다.
    단일 클러스터 인스턴스에만 작동합니다.
    Args:
        namespace: Kubernetes 네임스페이스
        cluster_prefix: 베이스 클러스터 이름 (-head/-worker 서픽스 제외)
    Returns:
        MLflow 서비스 URL
    """
    # 베이스 이름을 얻기 위해 -head 또는 -worker 서픽스 제거
    if namespace is None:
        namespace = get_namespace()
    pods = get_pods(namespace=namespace)
    clusters = get_clusters(pods=pods, cluster_name_prefix=cluster_prefix)
    if len(clusters) > 1:
        raise ValueError("하나 이상의 클러스터가 접두사와 일치하여 MLflow 정보를 자동으로 결정할 수 없습니다.")
    mlflow_name = f"{cluster_prefix}-mlflow"

    cmd = ["kubectl", "get", "svc", mlflow_name, "-n", namespace, "--no-headers"]
    try:
        output = subprocess.check_output(cmd).decode()
        fields = output.strip().split()

        # 클러스터 IP 가져오기
        cluster_ip = fields[2]
        port = "5000"  # 기본 MLflow 포트
        # 이 주소는 HTTP로 해결되어야 합니다. HTTPS는 해결할 수 없습니다
        # 클러스터 내 서브넷에 있으므로 이 방식이 적절합니다
        return f"http://{cluster_ip}:{port}"
    except subprocess.CalledProcessError as e:
        raise ValueError(f"MLflow 정보를 가져올 수 없습니다: {e}")  # 수정된 f-string


def check_clusters_running(pods: list, clusters: set) -> bool:
    """
    모든 제공된 클러스터의 포드가 실행 중인지 확인합니다.

    Args:
        pods (list): 각 튜플이 포드 이름과 해당 상태를 포함하는 포드 목록.
        clusters (set): 확인할 클러스터 이름 집합.

    Returns:
        bool: 클러스터 내의 모든 포드가 실행 중이면 True, 그렇지 않으면 False.
    """
    clusters_running = False
    for cluster in clusters:
        cluster_pods = [p for p in pods if p[0].startswith(cluster)]
        total_pods = len(cluster_pods)
        running_pods = len([p for p in cluster_pods if p[1] == "Running"])
        if running_pods == total_pods and running_pods > 0:
            clusters_running = True
            break
    return clusters_running


def get_ray_address(head_pod: str, namespace: str = "default", ray_head_name: str = "head") -> str:
    """
    지정된 클러스터 헤드 포드의 로그를 확인하여,
    작업 요청을 수락할 수 있는 Ray 주소를 가져옵니다.

    Args:
        head_pod (str): 헤드 포드의 이름.
        namespace (str, optional): Kubernetes 네임스페이스. 기본값은 "default"입니다.
        ray_head_name (str, optional): Ray 헤드 컨테이너의 이름. 기본값은 "head"입니다.

    Returns:
        str: 찾은 경우 Ray 주소, 그렇지 않으면 None.

    Raises:
        ValueError: 로그를 가져올 수 없거나 Ray 주소를 찾을 수 없는 경우.
    """
    cmd = ["kubectl", "logs", head_pod, "-c", ray_head_name, "-n", namespace]
    try:
        output = subprocess.check_output(cmd).decode()
    except subprocess.CalledProcessError as e:
        raise ValueError(
            f"다음 명령으로 헤드 컨테이너에 들어갈 수 없습니다 {cmd}: {e}다른 네임스페이스 또는 Ray 헤드 이름을 시도해 보세요."
        )
    match = re.search(r"RAY_ADDRESS='([^']+)'", output)
    if match:
        return match.group(1)
    else:
        return None


def process_cluster(cluster_info: dict, ray_head_name: str = "head") -> str:
    """
    각 클러스터에 대해 실행 중인지 확인하고, 작업 요청을 수락할 수 있는 Ray 헤드 주소를 가져옵니다.

    Args:
        cluster_info: 'cluster', 'pods', 'namespace' 키를 포함한 클러스터 정보를 담은 딕셔너리.
        ray_head_name: Ray 헤드 컨테이너의 이름. 기본값은 "head"입니다.

    Returns:
        클러스터 이름과 해당 Ray 헤드 주소를 포함한 문자열, 또는 헤드 포드 또는 Ray 주소를
        찾을 수 없는 경우 오류 메시지.
    """
    cluster, pods, namespace = cluster_info
    head_pod = None
    for pod_name, status in pods:
        if pod_name.startswith(cluster + "-head"):
            head_pod = pod_name
            break
    if not head_pod:
        return f"Error: Could not find head pod for cluster {cluster}\n"

    # RAY_ADDRESS 및 상태 가져오기
    ray_address = get_ray_address(head_pod, namespace=namespace, ray_head_name=ray_head_name)
    if not ray_address:
        return f"Error: Could not find RAY_ADDRESS for cluster {cluster}\n"

    # 클러스터 이름과 Ray 주소만 반환
    return f"name: {cluster} address: {ray_address}\n"


def main():
    # 명령줄 인수 파싱
    parser = argparse.ArgumentParser(description="Ray 클러스터를 처리하고 사양을 저장합니다.")
    parser.add_argument("--prefix", default="isaacray", help="클러스터 이름에 사용할 접두사.")
    parser.add_argument("--output", default="~/.cluster_config", help="클러스터 사양을 저장할 파일.")
    parser.add_argument("--ray_head_name", default="head", help="Ray 헤드 컨테이너의 메타데이터 이름")
    parser.add_argument(
        "--namespace", help="사용할 Kubernetes 네임스페이스. 제공되지 않으면 현재 컨텍스트에서 감지됩니다."
    )
    args = parser.parse_args()

    # 인수에서 네임스페이스를 가져오거나 감지
    current_namespace = args.namespace if args.namespace else get_namespace()
    print(f"Using namespace: {current_namespace}")

    cluster_name_prefix = args.prefix
    cluster_spec_file = os.path.expanduser(args.output)

    # 모든 포드 가져오기
    pods = get_pods(namespace=current_namespace)

    # 클러스터 가져오기
    clusters = get_clusters(pods, cluster_name_prefix)
    if not clusters:
        print(f"No clusters found with prefix {cluster_name_prefix}")
        return

    # 클러스터가 실행될 때까지 대기
    while True:
        pods = get_pods(namespace=current_namespace)
        if check_clusters_running(pods, clusters):
            break
        print("Waiting for all clusters to spin up...")
        time.sleep(5)

    print("Checking for MLflow:")
    # 각 클러스터에 대해 MLflow 상태 확인
    for cluster in clusters:
        try:
            mlflow_address = get_mlflow_info(current_namespace, cluster)
            print(f"MLflow address for {cluster}: {mlflow_address}")
        except ValueError as e:
            print(f"ML Flow not located: {e}")
    print()

    # 병렬 처리를 위한 클러스터 정보 준비
    cluster_infos = []
    for cluster in clusters:
        cluster_pods = [p for p in pods if p[0].startswith(cluster)]
        cluster_infos.append((cluster, cluster_pods, current_namespace))

    # ThreadPoolExecutor를 사용하여 클러스터를 병렬로 처리
    results = []
    results_lock = threading.Lock()

    with ThreadPoolExecutor() as executor:
        future_to_cluster = {
            executor.submit(process_cluster, info, args.ray_head_name): info[0] for info in cluster_infos
        }
        for future in as_completed(future_to_cluster):
            cluster_name = future_to_cluster[future]
            try:
                result = future.result()
                with results_lock:
                    results.append(result)
            except Exception as exc:
                print(f"{cluster_name} generated an exception: {exc}")

    # 클러스터 이름을 기준으로 결과 정렬
    results.sort()

    # 정렬된 결과를 출력 파일에 작성 (Ray 정보만)
    with open(cluster_spec_file, "w") as f:
        for result in results:
            f.write(result)

    print(f"Cluster spec information saved to {cluster_spec_file}")
    # 구성 파일의 내용 표시
    with open(cluster_spec_file) as f:
        print(f.read())


if __name__ == "__main__":
    main()
```

### Ray 클러스터만 (Kubernetes 없이)

1.) 클러스터 접근을 확인합니다.

2.) `~/.cluster_config` 파일을 생성하고, 각 클러스터마다 `name: <NAME> address: http://<IP>:<PORT>` 형식의 줄을 별도로 작성합니다. 하나의 클러스터에 대해 이 파일에는 한 줄만 있어야 합니다.

3.) 로그를 수신할 수 있는 MLFlow 서버를 시작하고, 해당 서버 URI를 결정합니다.

### KubeRay 및 순수 Ray 공통 디스패치 단계 Part II

1.) 다음 명령어를 사용하여 클러스터가 정상 작동하는지 테스트합니다.

```bash
# Test that NVIDIA GPUs are visible and that Ray is operation with the following command:
python3 scripts/reinforcement_learning/ray/submit_job.py --aggregate_jobs wrap_resources.py --test
```

2.) 튜닝 및/또는 리소스 래핑 작업 제출 방법은 `submit_job.py` 파일에서 설명됩니다.

### scripts/reinforcement_learning/ray/submit_job.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""
이 스크립트는 `name: <NAME> address: http://<IP>:<PORT>`가 각 줄에 새로 작성된 구성 파일에서 설명된 클러스터에 집계 작업(aggregate job)(s)을 제출합니다.
KubeRay 클러스터의 경우, 이 파일은 :file:`grok_cluster_with_kubectl.py`로 자동 생성할 수 있습니다.

집계 작업은 다음과 같은 관계를 통해 클러스터에 매칭됩니다:
cluster_line_index_submitted_to = job_index % total_cluster_count

집계 작업은 `*` 구분자로 분리됩니다. `--aggregate_jobs` 인자는 스크립트에 전달되는 마지막 인자여야 합니다.

집계 작업은 클러스터에서 시작할 때 여러 개의 개별 작업을 자동으로 생성하는 :file:`../tuner.py` 튜닝 작업일 수 있습니다.
또는, 여러 개의 개별 하위 작업으로 구분된 `+` 구분자를 포함할 수 있는 :file:`../wrap_resources.py` 리소스 래핑 작업일 수 있습니다.
집계 작업은 또한 YAML 구성 파일에 정의된 각 하위 작업과 그 리소스 요구사항을 갖는 :file:`../task_runner.py` 멀티태스크 제출 작업일 수도 있습니다.
이 모드에서 :file:`../task_runner.py`은 YAML 파일(인자 --task_cfg를 통해)을 읽고, Ray 클러스터에 모든 정의된 하위 작업을 제출하며, 작업별 리소스 지정 및 하위 작업 출력의 실시간 스트리밍을 지원합니다.

집계 작업 수가 클러스터 수보다 많을 경우, 위에 정의된 관계에 따라 클러스터가 사용 가능해질 때마다 집계 작업이 제출됩니다.
집계 작업 수가 클러스터 수보다 적을 경우, 일부 클러스터는 집계 작업을 받지 못합니다.
동시에 실행할 수 있는 최대 집계 작업 수는 작업 결과를 가져오기 위해 로그 출력을 가져오는 과정에서 사용되는 ThreadPoolExecutor에서 기본적으로 생성되는 워커 수와 같습니다. 이는 전체 작업 제출에 큰 제약이 되지 않을 가능성이 높습니다.

사용법:

.. code-block:: bash

    # 예시: 튜닝 작업 제출
    python3 scripts/reinforcement_learning/ray/submit_job.py \
    --aggregate_jobs /workspace/isaaclab/scripts/reinforcement_learning/ray/tuner.py \
        --cfg_file hyperparameter_tuning/vision_cartpole_cfg.py \
        --cfg_class CartpoleTheiaJobCfg --mlflow_uri <ML_FLOW_URI>

    # 예시: 리소스 래핑 작업 제출
    python3 scripts/reinforcement_learning/ray/submit_job.py --aggregate_jobs wrap_resources.py --test

    # 예시: 특정 리소스를 갖는 작업 제출 및 pip 패키지와 py_modules 지원
    # 작업 구성 및 py_modules에 상대 경로를 사용할 수 있으며, 이 파일들은
    # "scripts/reinforcement_learning/ray" 디렉터리에 배치되어야 하며, 클러스터에 업로드됩니다.
    python3 scripts/reinforcement_learning/ray/submit_job.py --aggregate_jobs task_runner.py --task_cfg tasks.yaml

    # 모든 명령줄 인수에 대해
    python3 scripts/reinforcement_learning/ray/submit_job.py -h
"""

import argparse
import os
import time
from concurrent.futures import ThreadPoolExecutor

from ray import job_submission

script_directory = os.path.dirname(os.path.abspath(__file__))
CONFIG = {"working_dir": script_directory, "executable": "/workspace/isaaclab/isaaclab.sh -p"}


def read_cluster_spec(fn: str | None = None) -> list[dict]:
    if fn is None:
        cluster_spec_path = os.path.expanduser("~/.cluster_config")
    else:
        cluster_spec_path = os.path.expanduser(fn)

    if not os.path.exists(cluster_spec_path):
        raise FileNotFoundError(f"Cluster spec file not found at {cluster_spec_path}")

    clusters = []
    with open(cluster_spec_path) as f:
        for line in f:
            parts = line.strip().split(" ")
            http_address = parts[3]
            cluster_info = {"name": parts[1], "address": http_address}
            print(f"[INFO] Setting {cluster_info['name']}")  # with {cluster_info['num_gpu']} GPUs.")
            clusters.append(cluster_info)

    return clusters


def submit_job(cluster: dict, job_command: str) -> None:
    """
    단일 클러스터에 작업을 제출하고, 최종 결과 및 Ray 대시보드 URL을 출력합니다.
    """
    address = cluster["address"]
    cluster_name = cluster["name"]
    print(f"[INFO]: Submitting job to cluster '{cluster_name}' at {address}")  # with {num_gpus} GPUs.")
    client = job_submission.JobSubmissionClient(address)
    runtime_env = {"working_dir": CONFIG["working_dir"], "executable": CONFIG["executable"]}
    print(f"[INFO]: Checking contents of the directory: {CONFIG['working_dir']}")
    try:
        dir_contents = os.listdir(CONFIG["working_dir"])
        print(f"[INFO]: Directory contents: {dir_contents}")
    except Exception as e:
        print(f"[INFO]: Failed to list directory contents: {str(e)}")
    entrypoint = f"{CONFIG['executable']} {job_command}"
    print(f"[INFO]: Attempting entrypoint {entrypoint=} in cluster {cluster}")
    job_id = client.submit_job(entrypoint=entrypoint, runtime_env=runtime_env)
    status = client.get_job_status(job_id)
    while status in [job_submission.JobStatus.PENDING, job_submission.JobStatus.RUNNING]:
        time.sleep(5)
        status = client.get_job_status(job_id)

    final_logs = client.get_job_logs(job_id)
    print("----------------------------------------------------")
    print(f"[INFO]: Cluster {cluster_name} Logs: \n")
    print(final_logs)
    print("----------------------------------------------------")


def submit_jobs_to_clusters(jobs: list[str], clusters: list[dict]) -> None:
    """
    모든 작업을 해당 클러스터에 제출하고, 작업 수가 클러스터 수보다 많을 경우 클러스터를 순환하여 제출합니다.
    """
    if not clusters:
        raise ValueError("No clusters available for job submission.")

    if len(jobs) < len(clusters):
        print("[INFO]: Less jobs than clusters, some clusters will not receive jobs")
    elif len(jobs) == len(clusters):
        print("[INFO]: Exactly one job per cluster")
    else:
        print("[INFO]: More jobs than clusters, jobs submitted as clusters become available.")
    with ThreadPoolExecutor() as executor:
        for idx, job_command in enumerate(jobs):
            # 작업 수가 클러스터 수보다 많을 경우 모듈러스를 사용해 클러스터를 순환
            cluster = clusters[idx % len(clusters)]
            executor.submit(submit_job, cluster, job_command)


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Submit multiple GPU jobs to multiple Ray clusters.")
    parser.add_argument("--config_file", default="~/.cluster_config", help="The cluster config path.")
    parser.add_argument(
        "--aggregate_jobs",
        type=str,
        nargs=argparse.REMAINDER,
        help="This should be last argument. The aggregate jobs to submit separated by the * delimiter.",
    )
    args = parser.parse_args()
    if args.aggregate_jobs is not None:
        jobs = " ".join(args.aggregate_jobs)
        formatted_jobs = jobs.split("*")
        if len(formatted_jobs) > 1:
            print("Warning; Split jobs by cluster with the * delimiter")
    else:
        formatted_jobs = []
    print(f"[INFO]: Isaac Ray Wrapper received jobs {formatted_jobs=}")
    clusters = read_cluster_spec(args.config_file)
    submit_jobs_to_clusters(formatted_jobs, clusters)
```

3.) 튜닝 작업의 경우, `JobCfg` 형태로 튜닝 작업 / 하이퍼파라미터 스위프를 지정해야 합니다.
제공된 `JobCfg`는 환경 진입점과 hydra 인수의 차이로 인해 현재 `rl_games` 워크플로만 지원하지만, 호환 가능한 `JobCfg`가 제공된다면 다른 워크플로도 작동합니다.

### scripts/reinforcement_learning/ray/tuner.py (JobCfg 정의)

```python
class JobCfg:
    """To be compatible with :meth: invoke_tuning_run and :class:IsaacLabTuneTrainable,
    at a minimum, the tune job should inherit from this class."""

    def __init__(self, cfg: dict):
        """
        Runner args include command line arguments passed to the task.
        For example:
        cfg["runner_args"]["headless_singleton"] = "--headless"
        cfg["runner_args"]["enable_cameras_singleton"] = "--enable_cameras"
        """
        assert "runner_args" in cfg, "No runner arguments specified."
        """
        Task is the desired task to train on. For example:
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-TheiaTiny-v0"])
        """
        assert "--task" in cfg["runner_args"], "No task specified."
        """
        Hydra args define the hyperparameters varied within the sweep. For example:
        cfg["hydra_args"]["agent.params.network.cnn.activation"] = tune.choice(["relu", "elu"])
        """
        assert "hydra_args" in cfg, "No hyperparameters specified."
        self.cfg = cfg
```

예를 들어, 다음 Cartpole 예제 구성を参照하세요.

### scripts/reinforcement_learning/ray/hyperparameter_tuning/vision_cartpole_cfg.py

```python
# Copyright (c) 2022-2026, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause
import pathlib
import sys
from typing import Any

# Allow for import of items from the ray workflow.
CUR_DIR = pathlib.Path(__file__).parent
UTIL_DIR = CUR_DIR.parent
sys.path.extend([str(UTIL_DIR), str(CUR_DIR)])
import util
import vision_cfg
from ray import tune
from ray.tune.progress_reporter import CLIReporter
from ray.tune.stopper import Stopper


class CartpoleRGBNoTuneJobCfg(vision_cfg.CameraJobCfg):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-v0"])
        super().__init__(cfg, vary_env_count=False, vary_cnn=False, vary_mlp=False)


class CartpoleRGBCNNOnlyJobCfg(vision_cfg.CameraJobCfg):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-v0"])
        super().__init__(cfg, vary_env_count=False, vary_cnn=True, vary_mlp=False)


class CartpoleRGBJobCfg(vision_cfg.CameraJobCfg):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-v0"])
        super().__init__(cfg, vary_env_count=True, vary_cnn=True, vary_mlp=True)


class CartpoleResNetJobCfg(vision_cfg.ResNetCameraJob):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-ResNet18-v0"])
        super().__init__(cfg)


class CartpoleTheiaJobCfg(vision_cfg.TheiaCameraJob):
    def __init__(self, cfg: dict = {}):
        cfg = util.populate_isaac_ray_cfg_args(cfg)
        cfg["runner_args"]["--task"] = tune.choice(["Isaac-Cartpole-RGB-TheiaTiny-v0"])
        super().__init__(cfg)


class CustomCartpoleProgressReporter(CLIReporter):
    def __init__(self):
        super().__init__(
            metric_columns={
                "training_iteration": "iter",
                "time_total_s": "total time (s)",
                "Episode/Episode_Reward/alive": "alive",
                "Episode/Episode_Reward/cart_vel": "cart velocity",
                "rewards/time": "rewards/time",
            },
            max_report_frequency=5,
            sort_by_metric=True,
        )


class CartpoleEarlyStopper(Stopper):
    def __init__(self):
        self._bad_trials = set()

    def __call__(self, trial_id: str, result: dict[str, Any]) -> bool:
        iter = result.get("training_iteration", 0)
        out_of_bounds = result.get("Episode/Episode_Termination/cart_out_of_bounds")

        # Mark the trial for stopping if conditions are met
        if iter >= 20 and out_of_bounds is not None and out_of_bounds > 0.85:
            self._bad_trials.add(trial_id)

        return trial_id in self._bad_trials

    def stop_all(self) -> bool:
        return False  # only stop individual trials
```

튜닝 결과를 보려면 이전에 생성한 MLFlow 서버의 대시보드를 확인하세요.
KubeRay의 경우, 다음 명령어로 포트 포워딩을 통해 MLFlow 대시보드에 접근할 수 있습니다.

`kubectl port-forward service/isaacray-mlflow 5000:5000`

그런 다음 웹 브라우저에서 다음 주소를 방문합니다.

`localhost:5000`

MLFlow 포트가 위에처럼 포트 포워딩된 경우, 다음 명령어로 로컬 TensorBoard 로그로 변환할 수 있습니다.

`./isaaclab.sh -p scripts/reinforcement_learning/ray/mlflow_to_local_tensorboard.py \
--uri http://localhost:5000 --experiment-name IsaacRay-<CLASS_JOB_CFG>-tune --download-dir test`

#### Kubernetes 클러스터 정리

공유 컴퓨팅 플랫폼에서 다른 사람들이 사용할 수 있는 précieux한 GPU 리소스를 확보하기 위해, 사용 후 Ray 클러스터를 삭제하여 리소스를 절약하는 것이 좋습니다. 클러스터는 쉽게 재생성할 수 있습니다!
KubeRay 클러스터의 경우, 다음 명령어로 정리할 수 있습니다.

```bash
kubectl get raycluster | egrep 'isaacray' | awk '{print $1}' | xargs kubectl delete raycluster &&
kubectl get deployments | egrep 'mlflow' | awk '{print $1}' | xargs kubectl delete deployment &&
kubectl get services | egrep 'mlflow' | awk '{print $1}' | xargs kubectl delete service &&
kubectl get services | egrep 'isaacray' | awk '{print $1}' | xargs kubectl delete service
```
