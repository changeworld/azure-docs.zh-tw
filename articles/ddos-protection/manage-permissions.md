---
title: Azure DDoS 保護方案許可權
description: 瞭解如何管理保護計劃中的許可權。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 10b10309e438bab3c99cb2ed7202eaa272d24abe
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905229"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>管理 DDoS 保護計劃：許可權和限制

DDoS 保護方案可跨區域和訂用帳戶運作。 相同的方案可以從不同區域中的其他訂用帳戶連結至您的租使用者。 計劃所關聯的訂用帳戶會為該計劃帶來每月的週期性帳單，也會在受保護的公用 IP 位址數目超過 100 時衍生超額費用。 如需有關 DDoS 定價的詳細資訊，請參閱[價格詳細資料](https://azure.microsoft.com/pricing/details/ddos-protection/)。

## <a name="prerequisites"></a>Prerequisites

- 在您可以完成本教學課程中的步驟之前，您必須先建立 [Azure DDoS 標準保護計劃](manage-ddos-protection.md)。

## <a name="permissions"></a>權限

若要使用 DDoS 保護計劃，您的帳戶必須指派為[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色，或為已指派下表中所列適當動作的[自訂](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

| 動作                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | 讀取 DDoS 保護計劃              |
| Microsoft.Network/ddosProtectionPlans/write       | 建立或更新 DDoS 保護計劃  |
| Microsoft.Network/ddosProtectionPlans/delete      | 刪除 DDoS 保護計劃            |
| Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 保護計劃              |

若要啟用虛擬網路的 DDoS 保護，您的帳戶也必須指派[虛擬網路的適當動作](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network#permissions)。

## <a name="azure-policy"></a>Azure 原則

對大多數組織來說，並沒有必要建立多個計劃。 計劃無法在訂用帳戶之間移動。 如果您想要變更計劃所在的訂用帳戶，就必須刪除現有的計劃，再建立一個新計劃。

如果客戶有各種訂用帳戶，而且想要確保在其租使用者中部署單一方案以進行成本控制，您可以使用 Azure 原則來 [限制 Azure DDoS 保護標準方案的建立](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy)。 除非先前已將訂用帳戶標示為例外，否則此原則將會封鎖建立任何 DDoS 方案。 此原則也會顯示已部署 DDoS 方案但不應將其標示為不符合規範的所有訂用帳戶清單。


## <a name="next-steps"></a>下一步

若要瞭解如何為您的 DDoS 保護計劃查看及設定遙測，請繼續進行教學課程。

> [!div class="nextstepaction"]
> [查看及設定 DDoS 保護遙測](telemetry-monitoring-alerting.md)
