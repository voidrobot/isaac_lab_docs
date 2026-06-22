<a id="deployment-cluster"></a>

# 클러스터 가이드

클러스터는 학습 알고리즘의 훈련 및 평가를 빠르게 하는 데 좋은 방법입니다.
Isaac Lab Docker 이미지는 클러스터에서 작업을 실행하는 데 사용할 수 있지만, 많은 클러스터는 singularity 이미지만 지원합니다.
이는 [singularity](https://docs.sylabs.io/guides/2.6/user-guide/index.html)가 공유된 다중 사용자 시스템 및 고성능 컴퓨팅(HPC) 환경에서 사용하기 쉽도록 설계되었기 때문입니다.
컨테이너를 실행하기 위해 root 권한이 필요하지 않으며 사용자 정의 컨테이너를 실행하는 데 사용할 수 있습니다.

Singularity는 모든 Docker 이미지와 호환됩니다. 이 섹션에서는 Isaac Lab Docker 이미지를 singularity 이미지로 변환하고 클러스터에 작업을 제출하는 방법을 설명합니다.

#### 주의
클러스터 설정은 기관마다 다릅니다. 다음 지침은 [ETH Zurich Euler](https://www.gdc-docs.ethz.ch/EulerManual/site/overview/) 클러스터(SLURM 워크로드 관리자 사용)에서 테스트되었으며,
IIT 제노바 프랭클린 클러스터(PBS 워크로드 관리자 사용)에서 테스트되었습니다.

다른 클러스터의 경우 지침을 조정해야 할 수 있습니다. 다른 클러스터에 대한 지침을 성공적으로 조정했다면 문서 기여를 고려해 주세요.

## 설정 지침

Docker 이미지를 singularity 이미지로 내보내려면 [apptainer](https://apptainer.org/)가 필요합니다.
`apptainer`의 설치 절차에 대한 자세한 개요는 해당
[문서](https://www.apptainer.org/docs/admin/main/installation.html#install-ubuntu-packages)에서 확인할 수 있습니다.
편의를 위해 로컬 설치 단계는 다음과 같이 요약했습니다:

```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:apptainer/ppa
sudo apt update
sudo apt install -y apptainer
```

간단히, 로컬 개발 머신과 클러스터 사이에 SSH 연결을 설정하는 것을 권장합니다. 이러한 연결은 파일 전송을 간소화하고 클러스터 비밀번호가 여러 번 요청되는 것을 방지합니다.

#### 주의
이 워크플로우는 다음 버전과 함께 테스트되었습니다:

- `apptainer version 1.2.5-1.el7` 및 `docker version 24.0.7`
- `apptainer version 1.3.4` 및 `docker version 27.3.1`

문제가 발생하면 해당 버전으로 전환해 보세요.

### 클러스터 매개변수 구성

먼저 `docker/cluster/.env.cluster` 파일에서 클러스터별 매개변수를 구성해야 합니다.
구성해야 하는 매개변수는 다음과 같습니다:

| 파라미터 | 설명 |
|----------|------|
| CLUSTER_JOB_SCHEDULER | 클러스터에서 사용하는 작업 스케줄러/워크로드 관리자. 현재 ‘SLURM’ 및 ‘PBS’ 워크로드 관리자를 지원합니다. |
| CLUSTER_ISAAC_SIM_CACHE_DIR | 클러스터에서 Isaac Sim 캐시가 저장된 디렉토리입니다. 이 디렉토리는 `docker-isaac-sim`로 끝나야 합니다. 이는 컴퓨트 노드로 복사되어 싱귤래리티 컨테이너에 마운트됩니다.これにより 시뮬레이션 시작 속도가 향상됩니다. |
| CLUSTER_ISAACLAB_DIR | 클러스터에서 Isaac Lab 로그가 저장된 디렉토리입니다. 이 디렉토리는 `isaaclab`로 끝나야 합니다. 이는 컴퓨트 노드로 복사되어 싱귤래리티 컨테이너에 마운트됩니다. 작업이 제출될 때, 최신 로컬 변경 사항이 `${CLUSTER_ISAACLAB_DIR}_${datetime}` 형식의 새 디렉토리로 클러스터에 복사됩니다. 여기서 날짜와 시간은 작업 제출 시점입니다. 이를 통해 서로 다른 코드 버전으로 여러 작업을 동시에 실행할 수 있습니다. |
| CLUSTER_LOGIN | 클러스터에 로그인하는 계정입니다. 일반적으로 사용자와 클러스터 이름의 조합이며, 예를 들어 `your_user@euler.ethz.ch`와 같습니다. |
| CLUSTER_SIF_PATH | 클러스터에서 singularity 이미지가 저장될 경로입니다. 이미지는 컴퓨트 노드로 복사되지만 작업이 제출될 때 클러스터에 다시 업로드되지는 않습니다. |
| REMOVE_CODE_COPY_AFTER_JOB | 작업이 끝난 후 코드 복사본을 삭제할지 여부를 나타냅니다. 작업 로그는 영구적인 `CLUSTER_ISAACLAB_DIR`에 저장되므로 삭제되지 않습니다. 이 기능은 클러스터의 디스크 공간을 절약하는 데 유용합니다. `true`로 설정된 경우 코드 복사본이 삭제됩니다. |
| CLUSTER_PYTHON_EXECUTABLE | 제출된 작업에서 실행해야 하는 Isaac Lab 내의 Python 실행 파일 경로입니다. |

`job`이 제출될 때 `docker/.env.base`에 정의된 변수도 사용되며, 이 변수들은 기본적으로 올바른 값으로 설정되어 있습니다.

### singularity 이미지로 내보내기

다음으로, Docker 이미지를 singularity 이미지로 내보내고 클러스터에 업로드해야 합니다. 이 단계는 첫 번째 작업을 제출할 때 또는 Docker 이미지가 업데이트될 때만 필요합니다. 예를 들어, Isaac Sim 버전 업그레이드 또는 프로젝트에 대한 추가 요구 사항으로 인해 필요할 수 있습니다.

singularity 이미지로 내보내려면 다음 명령을 실행합니다:

```bash
./docker/cluster/cluster_interface.sh push [profile]
```

이 명령은 `docker/exports` 디렉터리 아래에 singularity 이미지를 생성하고 클러스터의 정의된 위치에 업로드합니다. 이전에 `container.py` 인터페이스를 사용하여 이미지를 빌드했어야 합니다. singularity 이미지 생성에는 시간이 걸릴 수 있습니다.
`[profile]`은 사용하려는 컨테이너 프로파일을 지정하는 선택적 인수입니다. 프로파일이 지정되지 않은 경우 기본 프로파일 `base`가 사용됩니다.

#### 참고
기본적으로 singularity 이미지는 `apptainer build` 명령에 `--fakeroot` 플래그를 제공하여 root 액세스 없이 생성됩니다. 이미지 생성이 실패하는 경우, `docker/cluster/cluster_interface.sh`에서 해당 플래그를 제거하여 root 액세스로 이미지 생성을 시도해 볼 수 있습니다.

## 작업 매개변수 정의

작업 매개변수는 클러스터에서 사용하는 작업 스케줄러에 따라 정의해야 합니다.
사용 가능한 스케줄러에 해당하는 스크립트만 업데이트하면 됩니다.

- SLURM의 경우, `docker/cluster/submit_job_slurm.sh`의 매개변수를 업데이트합니다.
- PBS의 경우, `docker/cluster/submit_job_pbs.sh`의 매개변수를 업데이트합니다.

### SLURM용

작업 매개변수는 `docker/cluster/submit_job_slurm.sh` 내에 정의됩니다.
일반적인 SLURM 작업에서는 CPU 및 GPU 수, 메모리, 시간 제한을 지정해야 합니다. 자세한 내용은 [SLURM 문서](https://www.slurm.schedmd.com/sbatch.html)를 참조하세요.

기본 구성은 다음과 같습니다:

```bash
#SBATCH --cpus-per-task=8
#SBATCH --gpus=rtx_3090:1
#SBATCH --time=23:00:00
#SBATCH --mem-per-cpu=4048
#SBATCH --mail-type=END
#SBATCH --mail-user=name@mail
#SBATCH --job-name="training-$(date +"%Y-%m-%dT%H:%M")"

```

클러스터의 필수 요구 사항 중 하나는 컴퓨트 노드가 항상 인터넷에 접근할 수 있어야 한다는 것입니다. 이는 Nucleus 서버에서 에셋을 로드하기 위해 필요합니다.
일부 클러스터 아키텍처에서는 인터넷 액세스를 허용하기 위해 추가 모듈을 로드해야 할 수 있습니다.

예를 들어, ETH Zurich Euler 클러스터에서는 `eth_proxy` 모듈을 로드해야 합니다. 이는 다음과 같이 `submit_job_slurm.sh` 스크립트에 추가하여 수행할 수 있습니다:

```bash
# in the case you need to load specific modules on the cluster, add them here
# e.g., `module load eth_proxy`

```

### PBS용

작업 매개변수는 `docker/cluster/submit_job_pbs.sh` 내에 정의됩니다.
일반적인 PBS 작업에서는 CPU 및 GPU 수와 시간 제한을 지정해야 합니다. 자세한 내용은 [PBS 공식 사이트](https://openpbs.org/)를 참조하세요.

기본 구성은 다음과 같습니다:

```bash
#PBS -l select=1:ncpus=8:mpiprocs=1:ngpus=1
#PBS -l walltime=01:00:00
#PBS -j oe
#PBS -q gpu
#PBS -N isaaclab
#PBS -m bea -M "user@mail"

```

## 작업 제출

클러스터에서 작업을 제출하려면 다음 명령을 사용할 수 있습니다:

```bash
./docker/cluster/cluster_interface.sh job [profile] "argument1" "argument2" ...
```

이 명령은 최신 코드 변경 사항을 클러스터에 복사하고 작업을 제출합니다. Python 실행 파일의 출력이 `isaaclab/logs` 디렉터리에 저장되어야 함을 확인하세요. 이 디렉터리는 컴퓨트 노드와 `CLUSTER_ISAACLAB_DIR` 간에 동기화됩니다.

`[profile]`은 사용할 컨테이너 프로파일에 해당하는 singularity 이미지를 지정하는 선택적 인수입니다. 프로파일이 지정되지 않은 경우 기본 프로파일 `base`가 사용됩니다. 프로파일은 `job` 명령 바로 뒤에 정의해야 합니다. 다른 모든 인수는 Python 실행 파일에 전달됩니다. 프로파일이 정의되지 않은 경우 모든 인수는 Python 실행 파일에 전달됩니다.

학습 인수는 Python 실행 파일에 전달됩니다. 예를 들어, standard ANYmal 거친 지형 이동 훈련은 다음 명령으로 실행할 수 있습니다:

```bash
./docker/cluster/cluster_interface.sh job --task Isaac-Velocity-Rough-Anymal-C-v0 --headless --video --enable_cameras
```

위의 명령은 훈련 진행 상황을 보여주는 비디오도 추가로 렌더링하여 `isaaclab/logs` 디렉터리에 저장합니다.
