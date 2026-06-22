<a id="cloudxr-teleoperation-cluster"></a>

# Kubernetes에서 CloudXR Teleoperation 배포

이 섹션에서는 Kubernetes (K8s) 클러스터에서 Isaac Lab용 CloudXR Teleoperation을 배포하는 방법을 설명합니다.

<a id="k8s-system-requirements"></a>

## 시스템 요구 사항

* **최소 요구 사항**: NVIDIA RTX PRO 6000 / L40 GPU 또는 동등한 GPU가 1개 이상 있는 노드가 있는 Kubernetes 클러스터
* **권장 요구 사항**: NVIDIA RTX PRO 6000 / L40 GPU 또는 동등한 GPU가 2개 이상 있는 노드가 있는 Kubernetes 클러스터

#### 주의 사항
DGX Spark를 사용하는 경우, [DGX Spark 제한 사항](https://isaac-sim.github.io/IsaacLab/v2.3.2/source/setup/installation/index.html#dgx-spark-details-and-limitations)에서 호환성을 확인하세요.

## 소프트웨어 종속성

* 호스트 컴퓨터의 `kubectl`
  * MicroK8s를 사용하는 경우 `microk8s kubectl`이 이미 있습니다
  * 그 외의 경우 [공식 kubectl 설치 가이드](https://kubernetes.io/docs/tasks/tools/#kubectl)를 따르세요
* 호스트 컴퓨터의 `helm`
  * MicroK8s를 사용하는 경우 `microk8s helm`이 이미 있습니다
  * 그 외의 경우 [공식 Helm 설치 가이드](https://helm.sh/docs/intro/install/)를 따르세요
* Kubernetes 클러스터에서 NGC 공용 레지스트리에 대한 접근, 특히 다음 컨테이너 이미지:
  * `https://catalog.ngc.nvidia.com/orgs/nvidia/containers/isaac-lab`
  * `https://catalog.ngc.nvidia.com/orgs/nvidia/containers/cloudxr-runtime`
* Kubernetes 클러스터에 NVIDIA GPU Operator 또는 이에 상응하는 것이 설치되어 NVIDIA GPU를 노출해야 함
* Kubernetes 클러스터의 노드에 NVIDIA Container Toolkit이 설치되어 있어야 함

## 준비

호스트 컴퓨터에서 Kubernetes 클러스터에 접근하도록 `kubectl`을 이미 구성해야 합니다. 다음 명령을 실행하여 노드가 올바르게 반환되는지 확인하여 유효성을 검사할 수 있습니다:

```bash
kubectl get node
```

[부록: MicroK8s로 로컬 K8s 클러스터 설정](#k8s-appendix)에서 설명한 설정을 사용하지 않고 자체 Kubernetes 클러스터에 이 설치를 수행하는 경우, K8s 클러스터에서 역할은 다음 RBAC 권한 이상을 가져야 합니다:

```yaml
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
- apiGroups: ["apps"]
  resources: ["deployments", "replicasets"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
- apiGroups: [""]
  resources: ["services"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

<a id="k8s-installation"></a>

## 설치

#### 주의 사항
다음 단계는 GPU Operator가 설치된 MicroK8s 클러스터에서 검증되었습니다([부록: MicroK8s로 로컬 K8s 클러스터 설정](#k8s-appendix)의 구성 참조). 자체 K8s 클러스터에 문제가 발생하는 경우 해당 설정에 따라 구성할 수 있습니다.

1. NGC에서 Helm 차트 다운로드([공개 가이드](https://docs.nvidia.com/ngc/ngc-overview/index.html#generating-api-key)를 기반으로 NGC API 키 가져오기):
   ```bash
   helm fetch https://helm.ngc.nvidia.com/nvidia/charts/isaac-lab-teleop-2.3.0.tgz \
     --username='$oauthtoken' \
     --password=<your-ngc-api-key>
   ```
2. 기본 네임스페이스에서 모든 호스트 GPU를 소비하는 CloudXR Teleoperation for Isaac Lab pod 설치 및 실행:
   ```bash
   helm upgrade --install hello-isaac-teleop isaac-lab-teleop-2.3.0.tgz \
     --set fullnameOverride=hello-isaac-teleop \
     --set hostNetwork="true"
   ```

   #### 주의 사항
   MetalLB와 같은 외부 LoadBalancer VIP(예)를 생성하고 Helm 차트를 배포할 때 환경 변수 `NV_CXR_ENDPOINT_IP`를 설정하여 호스트 네트워크가 필요하지 않도록 할 수 있습니다:
   ```yaml
   # local_values.yml 파일 예시:
   fullnameOverride: hello-isaac-teleop
   streamer:
     extraEnvs:
       - name: NV_CXR_ENDPOINT_IP
         value: "<your external LoadBalancer VIP>"
       - name: ACCEPT_EULA
         value: "Y"
   ```

   ```bash
   # 명령
   helm upgrade --install --values local_values.yml \
     hello-isaac-teleop isaac-lab-teleop-2.3.0.tgz
   ```
3. 배포 완료 확인:
   ```bash
   kubectl wait --for=condition=available --timeout=300s \
     deployment/hello-isaac-teleop
   ```

   Pod이 실행된 후, 에셋 로딩을 완료하고 스트리밍을 시작하는 데 약 5-8분이 소요될 수 있습니다.

## 제거

다음 명령을 실행하여 간단히 제거할 수 있습니다:

```bash
helm uninstall hello-isaac-teleop
```

<a id="k8s-appendix"></a>

## 부록: MicroK8s로 로컬 K8s 클러스터 설정

로컬 워크스테이션에 NVIDIA Container Toolkit 및 해당 종속성이 설치되어 있어야 합니다. 그렇지 않으면 다음 설정이 작동하지 않습니다.

### 기존 설치 정리(선택 사항)

```bash
# 시스템을 정리하여 처음부터 시작하도록 함
sudo snap remove microk8s
sudo snap remove helm
sudo apt-get remove docker-ce docker-ce-cli containerd.io
# snap docker이 설치된 경우, 해당 항목도 제거
sudo snap remove docker
```

### MicroK8s 설치

```bash
sudo snap install microk8s --classic
```

### NVIDIA GPU Operator 설치

```bash
microk8s helm repo add nvidia https://helm.ngc.nvidia.com/nvidia
microk8s helm repo update
microk8s helm install gpu-operator \
  -n gpu-operator \
  --create-namespace nvidia/gpu-operator \
  --set toolkit.env[0].name=CONTAINERD_CONFIG \
  --set toolkit.env[0].value=/var/snap/microk8s/current/args/containerd-template.toml \
  --set toolkit.env[1].name=CONTAINERD_SOCKET \
  --set toolkit.env[1].value=/var/snap/microk8s/common/run/containerd.sock \
  --set toolkit.env[2].name=CONTAINERD_RUNTIME_CLASS \
  --set toolkit.env[2].value=nvidia \
  --set toolkit.env[3].name=CONTAINERD_SET_AS_DEFAULT \
  --set-string toolkit.env[3].value=true
```

#### 주의 사항
GPU 연산자에서 `DEVICE_LIST_STRATEGY`에 볼륨 마운트를 사용하도록 구성하고 툴킷에서 `ACCEPT_NVIDIA_VISIBLE_DEVICES_ENVVAR_WHEN_UNPRIVILEGED`를 비활성화한 경우, 현재 이 구성을 지원하지 않습니다. 이는 동일한 포드의 컨테이너 간에 할당된 GPU 리소스가 일관되게 공유되는 것을 보장하는 방법이 없기 때문입니다.

### 설치 검증

다음 명령을 실행하여 모든 Pod이 올바르게 실행 중인지 확인합니다:

```bash
microk8s kubectl get pods -n gpu-operator
```

다음과 유사한 출력이 표시되어야 합니다:

```text
NAMESPACE          NAME                                                        READY   STATUS      RESTARTS   AGE
gpu-operator       gpu-operator-node-feature-discovery-gc-76dc6664b8-npkdg       1/1     Running     0          77m
gpu-operator       gpu-operator-node-feature-discovery-master-7d6b448f6d-76fqj   1/1     Running     0          77m
gpu-operator       gpu-operator-node-feature-discovery-worker-8wr4n              1/1     Running     0          77m
gpu-operator       gpu-operator-86656466d6-wjqf4                                 1/1     Running     0          77m
gpu-operator       nvidia-container-toolkit-daemonset-qffh6                      1/1     Running     0          77m
gpu-operator       nvidia-dcgm-exporter-vcxsf                                    1/1     Running     0          77m
gpu-operator       nvidia-cuda-validator-x9qn4                                   0/1     Completed   0          76m
gpu-operator       nvidia-device-plugin-daemonset-t4j4k                          1/1     Running     0          77m
gpu-operator       gpu-feature-discovery-8dms9                                   1/1     Running     0          77m
gpu-operator       nvidia-operator-validator-gjs9m                               1/1     Running     0          77m
```

모든 Pod이 실행 중이면 [설치](#k8s-installation) 섹션으로 진행할 수 있습니다.
