---
title: Azure 應用程式安全性群組總覽
titlesuffix: Azure Virtual Network
description: 瞭解如何使用應用程式安全性群組。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 775ef92a0ca486d1f8a6c44c78a4df04cd5ef467
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274706"
---
# <a name="application-security-groups"></a>應用程式安全性群組

應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然擴充功能，讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。 您可以大規模重複使用您的安全性原則，而不需進行明確 IP 位址的手動維護。 此平台可處理明確 IP 位址和多個規則集的複雜性，讓您專注於商務邏輯。 若要進一步了解應用程式安全性群組，請考慮下列範例：

![應用程式安全性群組](./media/security-groups/application-security-groups.png)

在上圖中，NIC1 和 NIC2 都是 AsgWeb 應用程式安全性群組的成員。 NIC3 是 AsgLogic 應用程式安全性群組的成員。 NIC4 是 AsgDb 應用程式安全性群組的成員。 雖然在此範例中的每個網路介面都只是一個應用程式安全性群組的成員，但網路介面可以是多個應用程式安全性群組的成員，數量上限可參照 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 這些網路介面都沒有相關聯的網路安全性群組。 NSG1 與這兩個子網路相關聯且包含下列規則：

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

需要此規則才能讓流量從網際網路流向 Web 伺服器。 因為來自網際網路的輸入流量會遭到 **DenyAllInbound** 預設安全性規則拒絕，而 AsgLogic 或 AsgDb 應用程式安全性群組則不需要其他規則。

|優先順序|來源|來源連接埠| Destination | 目的地連接埠 | 通訊協定 | 存取 |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

## <a name="deny-database-all"></a>Deny-Database-All

由於 **AllowVNetInBound** 預設安全性規則允許相同虛擬網路中各資源之間的所有通訊，因此需要此規則才能拒絕來自所有資源的流量。

|優先順序|來源|來源連接埠| Destination | 目的地連接埠 | 通訊協定 | 存取 |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | 任意 | 拒絕 |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

此規則會允許流量從 AsgLogic應用程式安全性群組流向 AsgDb 應用程式安全性群組。 此規則的優先順序高於 Deny-Database-All 規則的優先順序。 如此一來，此規則會在 Deny-Database-All 規則之前進行處理，因此系統會允許來自 AsgLogic 應用程式安全性群組的流量，但所有其他流量仍會遭到封鎖。

|優先順序|來源|來源連接埠| Destination | 目的地連接埠 | 通訊協定 | 存取 |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

用於將應用程式安全性群組指定為來源或目的地的規則，只會套用至屬於此應用程式安全性群組成員的網路介面。 如果網路介面不是應用程式安全性群組的成員，則此規則不適用於此網路介面，即使子網路與網路安全性群組相關聯也一樣。

應用程式安全性群組具有下列條件約束：

-    您可以在訂用帳戶中擁有的應用程式安全性群組數量會有所限制，而且還有其他與應用程式安全性群組相關的限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 您可以將一個應用程式安全性群組指定為安全性規則中的來源和目的地。 您無法在來源或目的地中指定多個應用程式安全性群組。
- 指派給應用程式安全性群組的所有網路介面，都必須與指派給應用程式安全性群組的第一個網路介面位於相同虛擬網路中。 例如，如果指派給應用程式安全性群組 AsgWeb 的第一個網路介面位於名為 VNet1 的虛擬網路中，則後續所有指派給 ASGWeb 的網路介面都必須存在於 VNet1 中。 您無法將不同虛擬網路的網路介面新增至相同的應用程式安全性群組。
- 如果您指定安全性群組作為安全性規則中的來源和目的地，兩個應用程式安全性群組中的網路介面都必須在相同的虛擬網路中。 例如，如果 AsgLogic 包含來自 VNet1 的網路介面，而 AsgDb 包含來自 VNet2 的網路介面，您無法在規則中將 AsgLogic 指派為來源，將 AsgDb 指派為目的地。 來源和目的地應用程式安全性群組的所有網路介面都必須位在相同的虛擬網路中。

> [!TIP]
> 若要將所需的安全性規則數量及規則變更需求降到最低，請規劃好您需要的應用程式安全性群組，並盡可能使用服務標籤或應用程式安全性群組來建立規則，而不是使用個別的 IP 位址或 IP 位址範圍。

## <a name="next-steps"></a>後續步驟

* 了解如何[建立網路安全性群組](tutorial-filter-network-traffic.md)。
