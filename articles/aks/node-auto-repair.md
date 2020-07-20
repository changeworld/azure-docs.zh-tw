---
title: 自動修復 Azure Kubernetes Service （AKS）節點
description: 瞭解節點自動修復功能，以及 AKS 如何修正中斷的背景工作節點。
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 91384461567634faabaaa1dd588d6e7ec6ece60e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735620"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes Service （AKS）節點自動修復

AKS 會持續檢查背景工作節點的健全狀況狀態，並在節點變成狀況不良時執行自動修復。 本檔會通知操作員，自動節點修復功能的運作方式。 除了 AKS 修復以外，Azure VM 平臺也會在遇到問題的[虛擬機器上執行維護][vm-updates]。 AKS 和 Azure Vm 會共同運作，以將叢集的服務中斷降至最低。

> [!Important]
> Windows Server 節點集區目前不支援節點自動修復功能。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS 如何檢查狀況不良的節點

> [!Note]
> AKS 會在具有使用者帳戶**AKS-remediator**的節點上採取修復動作。

AKS 會使用規則來判斷節點是否狀況不良，以及是否需要修復。 AKS 會使用下列規則來判斷是否需要自動修復。

* 節點會報告在10分鐘時間範圍內連續檢查的**NotReady**狀態
* 節點未在10分鐘內報告狀態

您可以使用 kubectl，手動檢查節點的健全狀況狀態。 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自動修復的運作方式

> [!Note]
> AKS 會使用使用者帳戶**AKS-remediator**來起始修復作業。

VM 集合類型為 [**虛擬機器擴展集**] 的叢集預設支援自動修復。 如果根據上述規則判斷節點狀況不良，AKS 會在10次連續狀況不良分鐘後重新開機節點。 如果在初始修復作業之後節點保持狀況不良，AKS 工程師會調查其他補救。
  
如果健康狀態檢查期間有多個節點狀況不良，則會在另一個修復開始之前，個別修復每個節點。

## <a name="next-steps"></a>後續步驟

使用[可用性區域][availability-zones]提高 AKS 叢集工作負載的高可用性。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
