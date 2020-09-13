---
title: 自動修復 Azure Kubernetes Service (AKS) 節點
description: 深入瞭解節點自動修復功能，以及 AKS 如何修正中斷的背景工作節點。
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89490100"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes Service (AKS) 節點自動修復

AKS 會持續檢查背景工作節點的健全狀況狀態，並在節點變成狀況不良時執行自動修復。 本檔會通知操作員有關自動節點修復功能如何針對 Windows 和 Linux 節點運作。 除了 AKS 維修之外，Azure VM 平臺也會在遇到問題的 [虛擬機器上執行維護][vm-updates] 。 AKS 和 Azure Vm 會一起運作，以將叢集的服務中斷降至最低。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS 如何檢查狀況不良的節點

AKS 會使用規則來判斷節點是否狀況不良，並需要修復。 AKS 會使用下列規則來判斷是否需要自動修復。

* 節點會在10分鐘的時間範圍內，報告連續檢查的 **NotReady** 狀態
* 節點不會在10分鐘內報告狀態

您可以使用 kubectl 手動檢查節點的健全狀況狀態。

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自動修復的運作方式

> [!Note]
> AKS 會使用使用者帳戶 **AKS （remediator）** 來起始修復作業。

如果節點根據上述規則而狀況不良，且維持不健康狀態10連續10分鐘，則會採取下列動作。

1. 重新開機節點
1. 如果重新開機失敗，請重新安裝節點的映射
1. 如果重新安裝映射失敗，請建立新節點並為其重新安裝映射

如果沒有任何動作成功，AKS 工程師會調查其他補救。 如果健康狀態檢查期間有多個節點狀況不良，則會在另一個修復開始之前個別修復每個節點。

## <a name="next-steps"></a>後續步驟

使用[可用性區域][availability-zones]提高 AKS 叢集工作負載的高可用性。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
