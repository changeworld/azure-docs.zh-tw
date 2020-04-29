---
title: 自動修復 Azure Kubernetes Service （AKS）節點
description: 瞭解節點自動修復功能，以及 AKS 如何修正中斷的背景工作節點。
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284835"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes Service （AKS）節點自動修復

AKS 會持續檢查背景工作節點的健全狀況狀態，並在節點變成狀況不良時執行自動修復。 本檔說明 Azure Kubernetes Service （AKS）如何監視背景工作節點，以及修復狀況不良的背景工作節點。  本檔是針對節點修復功能的行為來通知 AKS 操作員。 也請務必注意，Azure 平臺會在遇到問題的[虛擬機器上執行維護][vm-updates]。 AKS 和 Azure 會共同運作，以將叢集的服務中斷降至最低。

> [!Important]
> Windows Server 節點集區目前不支援節點自動修復功能。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS 如何檢查狀況不良的節點

> [!Note]
> AKS 會在具有使用者帳戶**AKS-remediator**的節點上採取修復動作。

AKS 會使用規則來判斷節點是否為狀況不良的狀態，以及是否需要修復。 AKS 會使用下列規則來判斷是否需要自動修復。

* 節點會報告在10分鐘時間範圍內連續檢查的**NotReady**狀態
* 節點未在10分鐘內報告狀態

您可以使用 kubectl，手動檢查節點的健全狀況狀態。 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自動修復的運作方式

> [!Note]
> AKS 會在具有使用者帳戶**AKS-remediator**的節點上採取修復動作。

這是**虛擬機器擴展集**的行為。  自動修復會採取數個步驟來修復中斷的節點。  如果節點判斷為狀況不良，則 AKS 會嘗試數個補救步驟。  這些步驟會依照下列循序執行：

1. 在容器執行時間沒有回應10分鐘之後，節點上的失敗執行時間服務就會重新開機。
2. 如果節點未在10分鐘內準備就緒，節點就會重新開機。
3. 如果節點未在30分鐘內就緒，則會重新製作節點的映射。

> [!Note]
> 如果有多個節點狀況不良，它們會逐一修復

## <a name="next-steps"></a>後續步驟

使用[可用性區域][availability-zones]來增加 AKS 叢集工作負載的高可用性。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
