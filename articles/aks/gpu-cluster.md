---
title: 在 Azure Kubernetes Service (AKS) 上使用 GPU
description: 了解如何在 Azure Kubernetes Service (AKS) 上使用 GPU 處理高效能計算或大量圖形的工作負載
services: container-service
ms.topic: article
ms.date: 03/27/2020
ms.openlocfilehash: 242fefb3b153d11e23d66f26049d0b68c0a4bf4a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383985"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 上使用 GPU 處理計算密集型工作負載

圖形處理單元 (GPU) 通常用來處理計算密集型工作負載 (例如圖形和視覺效果工作負載)。 AKS 可讓您建立已啟用 GPU 的節點集區，以便在 Kubernetes 中執行這些計算密集型工作負載。 若要深入了解已啟用 GPU 的可用 VM，請參閱 [Azure 中的 GPU 最佳化 VM 大小][gpu-skus]。 針對 AKS 節點，我們建議使用最小的大小：Standard_NC6**。

> [!NOTE]
> 已啟用 GPU 的 VM 包含特定硬體，該特定硬體受限於較高的定價和區域可用性。 如需詳細資訊，請參閱[定價][azure-pricing]工具和[區域可用性][azure-availability]。

目前，使用啟用 GPU 的節點池僅適用于 Linux 節點池。

## <a name="before-you-begin"></a>開始之前

本文假設您的現有 AKS 叢集具有支援 GPU 的節點。 您的 AKS 叢集必須執行 Kubernetes 1.10 或更新版本。 如果您需要符合這些需求的 AKS 叢集，請參閱本文的第一節：[建立 AKS 叢集](#create-an-aks-cluster)。

您還需要 Azure CLI 版本 2.0.64 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

如果您需要符合最低需求的 AKS 叢集 (已啟用 GPU 的節點和使用 Kubernetes 1.10 或更新版本)，請完成下列步驟。 如果您已經有滿足這些要求的 AKS 群集，[請跳到下一節](#confirm-that-gpus-are-schedulable)。

首先，使用 [az group create][az-group-create] 命令來建立 叢集的資源群組。 下列範例會在 eastus** 地區建立名為 myResourceGroup** 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

現在，使用 [az aks create][az-aks-create] 命令來建立 AKS 叢集。 下面的示例創建具有單個大小`Standard_NC6`節點的群集：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

使用 [az aks get-credentials][az-aks-get-credentials] 命令取得 AKS 叢集的認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-drivers"></a>安裝 NVIDIA 驅動程式

在可以使用節點中的 GPU 之前，必須為 NVIDIA 設備外掛程式部署守護進程集。 此 DaemonSet 會在每個節點上執行 Pod，為 GPU 提供必要的驅動程式。

首先，使用 [kubectl create namespace][kubectl-create] 命令建立命名空間，例如 gpu-resources**：

```console
kubectl create namespace gpu-resources
```

建立名為 nvidia-device-plugin-ds.yaml** 的檔案，並貼上下列 YAML 資訊清單。 此清單是作為[Kubernetes 專案的 NVIDIA 設備外掛程式][nvidia-github]的一部分提供的。

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

現在使用[kubectl 應用][kubectl-apply]命令創建守護進程並確認成功創建 NVIDIA 設備外掛程式，如以下示例輸出所示：

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>確認 GPU 可進行排程

建好 AKS 叢集後，請確認 GPU 可在 Kubernetes 中進行排程。 首先，使用 [kubectl get nodes][kubectl-get] 命令列出您叢集中的節點：

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

現在，使用 [kubectl describe node][kubectl-describe] 命令確認 GPU 可進行排程。 在 [容量]** 區段下，GPU 應顯示為 `nvidia.com/gpu:  1`。

下列精簡範例顯示名為 aks-nodepool1-18821093-0** 的節點上有 GPU：

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>執行已啟用 GPU 的工作負載

若要查看運作中的 GPU，請使用適當的資源要求對已啟用 GPU 的工作負載進行排程。 在此範例中，我們要針對 [MNIST 資料集](http://yann.lecun.com/exdb/mnist/)執行 [Tensorflow](https://www.tensorflow.org/) 作業。

建立名為 samples-tf-mnist-demo.yaml** 的檔案，並貼上下列 YAML 資訊清單。 下列作業資訊清單包含 `nvidia.com/gpu: 1` 的資源限制：

> [!NOTE]
> 如果在調用驅動程式時收到版本不匹配錯誤，例如，CUDA 驅動程式版本不足以用於 CUDA 執行階段版本，請查看 NVIDIA 驅動程式矩陣相容性圖表 -[https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

使用 [kubectl apply][kubectl-apply] 命令來執行作業。 此命令會剖析資訊清單檔，並建立已定義的 Kubernetes 物件：

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>檢視已啟用 GPU 的工作負載狀態和輸出

使用 [kubectl get jobs][kubectl-get] 命令和 `--watch` 引數監視作業進度。 這可能需要幾分鐘來執行第一次的影像提取和資料集處理。 當 *"完成"* 列顯示*1/1*時，作業已成功完成。 使用`kubetctl --watch` *Ctrl-C*退出命令 ：

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

要查看啟用 GPU 的工作負載的輸出，首先獲取帶有[kubectl 獲取 pod 命令][kubectl-get]的 pod 的名稱：

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

現在，使用 [kubectl logs][kubectl-logs] 命令來檢視 Pod 記錄。 下列 Pod 記錄範例會確認已探索到適當的 GPU 裝置 `Tesla K80`。 提供自有 Pod 的名稱：

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>清除資源

若要移除本文中建立的相關 Kubernetes 物件，請使用 [kubectl delete job][kubectl delete] 命令，如下所示：

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>後續步驟

若要執行 Apache Spark 作業，請參閱[在 AKS 上執行 Apache Spark 作業][aks-spark]。

如需有關在 Kubernetes 上執行機器學習 (ML) 工作負載的詳細資訊，請參閱 [Kubeflow 實驗室][kubeflow-labs]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
