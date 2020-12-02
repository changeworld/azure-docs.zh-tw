---
title: 在適用于 Kubernetes 部署的 Azure Stack Edge 裝置上使用計算加速 GPU 或 VPU |Microsoft Docs
description: 說明如何在 Azure Stack Edge Pro GPU 上使用計算加速 GPU 或 VPU，Azure Stack Edge Pro R 或 Azure Stack Edge 迷你 Ri 進行 Kubernetes 部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: 0aaad18ba5bf98ca2ad53bd86605dfc6cce3e52c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466228"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>在 Azure Stack Edge Pro GPU 上使用計算加速以進行 Kubernetes 部署

本文說明如何在使用 Kubernetes 部署時，在 Azure Stack Edge 裝置上使用計算加速。 本文適用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 裝置。


## <a name="about-compute-acceleration"></a>關於計算加速 

中央處理器 (CPU) 是在電腦上執行之大部分進程的預設一般目的計算。 通常會使用特製化的電腦硬體，更有效率地執行某些功能，而不是在 CPU 的軟體中執行這些功能。 例如，圖形處理單位 (GPU) 可用來加速處理圖元資料。  

「計算加速」是專門用於 Azure Stack Edge 裝置的詞彙，其中圖形處理單位 (GPU) 、視覺處理單位 (VPU) ，或可現場程式化閘道陣列 (FPGA) 用於硬體加速。 Azure Stack Edge 裝置上部署的大部分工作負載都牽涉到重要的時間、多個相機串流及/或高畫面播放速率，這些都需要特定的硬體加速。

本文將僅針對下列裝置使用 GPU 或 VPU 討論計算加速：

- **Azure Stack Edge PRO GPU** -這些裝置可以有1或2個 Nvidia T4 TENSOR 核心 GPU。 如需詳細資訊，請參閱 [NVIDIA T4](https://www.nvidia.com/data-center/tesla-t4/)。
- **Azure Stack Edge Pro R** -這些裝置有1個 Nvidia T4 TENSOR 核心 GPU。 如需詳細資訊，請參閱 [NVIDIA T4](https://www.nvidia.com/data-center/tesla-t4/)。
- **Azure Stack Edge 迷你 R** -這些裝置有1個 Intel Movidius 無數 X VPU。 如需詳細資訊，請參閱 [Intel Movidius 無數 X VPU](https://www.movidius.com/MyriadX)。


## <a name="use-gpu-for-kubernetes-deployment"></a>使用 GPU 進行 Kubernetes 部署

下列範例 `yaml` 可用於 Azure Stack Edge PRO GPU 或具有 GPU 的 Azure Stack Edge Pro R 裝置。

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>使用 VPU 進行 Kubernetes 部署

下列範例 `yaml` 可用於具有 VPU 的 Azure Stack Edge 迷你 R 裝置。

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>後續步驟

瞭解如何 [使用 kubectl，以 PersistentVolume 在您 Azure Stack Edge PRO GPU 裝置上執行 Kubernetes 具狀態應用程式](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。
