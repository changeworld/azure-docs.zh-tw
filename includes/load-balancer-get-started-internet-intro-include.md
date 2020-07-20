---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174401"
---
Azure 負載平衡器是第 4 層 (TCP、UDP) 負載平衡器。 此負載平衡器可藉由在負載平衡器集合中，將連入流量分散於雲端服務或虛擬機器中狀況良好的服務執行個體之間，來提供高可用性。 Azure Load Balancer 也會在多個連接埠、多個 IP 位址或兩者上顯示這些服務。

您可以設定負載平衡器：

* 對虛擬機器 (VM) 的連入網際網路流量進行負載平衡。 我們將此案例中的負載平衡器稱為 [網際網路面向的負載平衡器](../articles/load-balancer/load-balancer-internet-overview.md)。
* 對虛擬網路 (VNet) 中的 VM 之間、雲端服務中的 VM 之間，或內部部署電腦與跨單位虛擬網路中的 VM 之間的流量進行負載平衡。 我們將此案例中的負載平衡器稱為 [內部負載平衡器 (ILB)](../articles/load-balancer/load-balancer-internal-overview.md)。
* 將外部流量轉送到特定的 VM 執行個體。
