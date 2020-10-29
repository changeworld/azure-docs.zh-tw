---
title: 在 Azure Kubernetes Service (AKS) 上使用 GPU
description: 了解如何在 Azure Kubernetes Service (AKS) 上使用 GPU 處理高效能計算或大量圖形的工作負載
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 52fd4867532832e0304a27317b21950bf131de79
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900787"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 上使用 GPU 處理計算密集型工作負載

圖形處理單元 (GPU) 通常用來處理計算密集型工作負載 (例如圖形和視覺效果工作負載)。 AKS 可讓您建立已啟用 GPU 的節點集區，以便在 Kubernetes 中執行這些計算密集型工作負載。 若要深入了解已啟用 GPU 的可用 VM，請參閱 [Azure 中的 GPU 最佳化 VM 大小][gpu-skus]。 針對 AKS 節點，我們建議使用最小的大小：Standard_NC6  。

> [!NOTE]
> 已啟用 GPU 的 VM 包含特定硬體，該特定硬體受限於較高的定價和區域可用性。 如需詳細資訊，請參閱[定價][azure-pricing]工具和[區域可用性][azure-availability]。

目前，使用已啟用 GPU 的節點集區僅適用于 Linux 節點集區。

## <a name="before-you-begin"></a>開始之前

本文假設您的現有 AKS 叢集具有支援 GPU 的節點。 您的 AKS 叢集必須執行 Kubernetes 1.10 或更新版本。 如果您需要符合這些需求的 AKS 叢集，請參閱本文的第一節：[建立 AKS 叢集](#create-an-aks-cluster)。

您也需要安裝並設定 Azure CLI 2.0.64 版版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

如果您需要符合最低需求的 AKS 叢集 (已啟用 GPU 的節點和使用 Kubernetes 1.10 或更新版本)，請完成下列步驟。 如果您已經有符合這些需求的 AKS 叢集，請 [跳到下一節](#confirm-that-gpus-are-schedulable)。

首先，使用 [az group create][az-group-create] 命令來建立叢集的資源群組。 下列範例會在 eastus  地區建立名為 myResourceGroup  的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

現在使用 [az AKS create][az-aks-create] 命令建立 AKS 叢集。 下列範例會建立具有單一節點大小的叢集 `Standard_NC6` ：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

使用 [az AKS 取得認證][az-aks-get-credentials] 命令取得 AKS 叢集的認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>安裝 NVIDIA 裝置外掛程式

在可以使用節點中的 Gpu 之前，您必須先為 NVIDIA 裝置外掛程式部署 DaemonSet。 此 DaemonSet 會在每個節點上執行 Pod，為 GPU 提供必要的驅動程式。

首先，使用  ：

```console
kubectl create namespace gpu-resources
```

建立名為 nvidia-device-plugin-ds.yaml  的檔案，並貼上下列 YAML 資訊清單。 此資訊清單會作為 [Kubernetes 專案的 NVIDIA 裝置外掛程式][nvidia-github]的一部分來提供。

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
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
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

現在，使用 [kubectl apply][kubectl-apply] 命令來建立 DaemonSet，並確認 NVIDIA 裝置外掛程式已成功建立，如下列範例輸出所示：

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>使用 AKS 專用 GPU 映射 (預覽) 

除了這些步驟之外，AKS 還提供完整設定的 AKS 映射，其中已包含 [適用于 Kubernetes 的 NVIDIA 裝置外掛程式][nvidia-github]。

> [!WARNING]
> 您不應該使用新的 AKS 專用 GPU 映射，手動安裝叢集的 NVIDIA 裝置外掛程式 daemon 集。


註冊 `GPUDedicatedVHDPreview` 功能：

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

若要安裝 aks-preview CLI 擴充功能，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
```

若要更新 aks-preview CLI 擴充功能，請使用下列 Azure CLI 命令：

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>在新的叢集上使用 AKS 專用 GPU 映射 (預覽版)     

設定叢集，以在建立叢集時使用 AKS 專用 GPU 映射。 在 `--aks-custom-headers` 新叢集上使用 GPU 代理程式節點的旗標，以使用 AKS 專用 GPU 映射。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

如果您想要使用一般 AKS 映射來建立叢集，您可以省略自訂標記來建立叢集 `--aks-custom-headers` 。 您也可以選擇新增更多特製化的 GPU 節點集區，如下所示。


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>在現有的叢集上使用 AKS 專用 GPU 映射 (預覽版) 

設定新的節點集區，以使用 AKS 專用 GPU 映射。 `--aks-custom-headers`針對新節點集區上的 GPU 代理程式節點使用旗標旗標，以使用 AKS 專用的 gpu 映射。

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

如果您想要使用一般 AKS 映射來建立節點集區，您可以省略自訂標記來進行 `--aks-custom-headers` 。 

> [!NOTE]
> 如果您的 GPU sku 需要第2代虛擬機器，您可以建立：
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>確認 GPU 可進行排程

建好 AKS 叢集後，請確認 GPU 可在 Kubernetes 中進行排程。 首先，使用 [kubectl get nodes][kubectl-get] 命令列出您叢集中的節點：

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

現在，使用 [kubectl describe node][kubectl-describe] 命令確認 GPU 可進行排程。 在 [容量]  區段下，GPU 應顯示為 `nvidia.com/gpu:  1`。

下列精簡範例顯示名為 aks-nodepool1-18821093-0  的節點上有 GPU：

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

建立名為 samples-tf-mnist-demo.yaml  的檔案，並貼上下列 YAML 資訊清單。 下列作業資訊清單包含 `nvidia.com/gpu: 1` 的資源限制：

> [!NOTE]
> 如果您在呼叫驅動程式時發生版本不符的錯誤，例如，CUDA 驅動程式版本不足以 CUDA 執行階段版本，請參閱 NVIDIA 驅動程式矩陣相容性圖表- [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

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

使用 [kubectl get jobs][kubectl-get] 命令和 `--watch` 引數監視作業進度。 這可能需要幾分鐘來執行第一次的影像提取和資料集處理。 當 [ *完成* ] 資料行顯示 *1/1* 時，表示作業已順利完成。 `kubetctl --watch`使用 *Ctrl + C* 來結束命令：

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

若要查看已啟用 GPU 的工作負載輸出，請先使用 [kubectl get][kubectl-get] pod 命令取得 pod 的名稱：

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
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
