---
title: 自動修復 Azure 庫伯奈斯服務 （AKS） 節點
description: 瞭解節點自動修復功能，以及 AKS 如何修復損壞的工作節點。
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284835"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure 庫伯奈斯服務 （AKS） 節點自動修復

AKS 持續檢查輔助節點的運行狀況，並在節點不正常時執行自動修復。 本文檔介紹 Azure Kubernetes 服務 （AKS） 如何監視輔助節點並修復不正常的工作節點。  本文檔旨在通知 AKS 操作員節點修復功能的行為。 還必須注意，Azure 平臺對遇到問題的[虛擬機器執行維護][vm-updates]。 AKS 和 Azure 協同工作，以最大程度地減少群集的服務中斷。

> [!Important]
> Windows Server 節點池當前不支援節點自動修復功能。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS 如何檢查不正常的節點

> [!Note]
> AKS 對具有使用者帳戶**aks-重新仲介的**節點執行修復操作。

AKS 使用規則來確定節點是否處於不正常狀態，是否需要修復。 AKS 使用以下規則來確定是否需要自動修復。

* 節點在 10 分鐘內連續檢查時報告 **"不就緒"** 狀態
* 節點在 10 分鐘內不報告狀態

您可以使用 kubectl 手動檢查節點的運行狀況狀態。 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自動修復的工作原理

> [!Note]
> AKS 對具有使用者帳戶**aks-重新仲介的**節點執行修復操作。

此行為適用于**虛擬機器縮放集**。  自動修復需要幾個步驟來修復損壞的節點。  如果確定節點不正常，AKS 將嘗試多個修正步驟。  這些步驟按以下循序執行：

1. 容器運行時在 10 分鐘內無回應後，故障運行時服務將重新開機節點上。
2. 如果節點未在 10 分鐘內準備就緒，則重新開機該節點。
3. 如果節點未在 30 分鐘內準備就緒，則重新映射節點。

> [!Note]
> 如果多個節點不正常，則逐個修復它們

## <a name="next-steps"></a>後續步驟

使用[可用性區域][availability-zones]提高 AKS 群集工作負載的高可用性。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
