---
title: Azure 虛擬網路服務端點原則 | Microsoft Docs
description: 了解如何使用服務端點原則篩選流向 Azure 服務資源的虛擬網路流量
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 926da07ffaf0c61ca2a7fd02351ef3635ec4d73b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651284"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>用於 Azure 存儲的虛擬網路服務終結點策略

虛擬網路 （VNet） 服務終結點策略允許您通過服務終結點篩選到 Azure 存儲帳戶的虛擬網路流量，並允許僅將資料滲出到特定的 Azure 存儲帳戶。 通過服務終結點連接時，終結點策略為虛擬網路流量提供對 Azure 存儲的精細存取控制。

![保護到 Azure 存儲帳戶的虛擬網路出站流量](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

此功能通常可用於__所有全域 Azure 區域__中的__Azure 存儲__。

## <a name="key-benefits"></a>主要權益

虛擬網路服務端點原則具有下列優勢：

- __提高虛擬網路流量到 Azure 存儲的安全性__

  [用於網路安全性群組的 Azure 服務標記](https://aka.ms/servicetags)允許您將虛擬網路出站流量限制為特定的 Azure 存儲區域。 但是，這允許流量到所選 Azure 存儲區域中的任何帳戶。
  
  終結點策略允許您指定允許虛擬網路出站訪問的 Azure 存儲帳戶，並限制對所有其他存儲帳戶的訪問。 這為保護資料從虛擬網路中滲漏提供了更加精細的安全控制。

- __可用來篩選 Azure 服務流量的可調整與高可用性原則__

   端點原則提供可水平調整的高可用性解決方案，以篩選從虛擬網路透過服務端點通往 Azure 服務的流量。 在虛擬網路中，這個流量完全不會對中央網路設備的維護產生額外負荷。

## <a name="json-object-for-service-endpoint-policies"></a>用於服務終結點策略的 JSON 物件
讓我們快速查看服務終結點策略物件。

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>組態

-   可以配置終結點策略，將虛擬網路流量限制為特定的 Azure 存儲帳戶。
-   您可在虛擬網路的子網路上設定端點原則。 應在子網上啟用 Azure 存儲的服務終結點以應用策略。
-   終結點策略允許您使用 resourceID 格式添加特定的 Azure 存儲帳戶以允許清單。 您可以限制對
    - 訂閱中的所有存儲帳戶<br>
      `E.g. /subscriptions/subscriptionId`

    - 資源組中的所有存儲帳戶<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - 通過列出相應的 Azure 資源管理器資源 Id 來單個存儲帳戶。 這包含 Blob、資料表、佇列、檔案和 Azure Data Lake Storage Gen2 的流量。 <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   預設情況下，如果沒有策略附加到具有終結點的子網，則可以訪問服務中的所有存儲帳戶。 一旦在這個子網路上設定原則之後，就只能從該子網路的計算執行個體存取原則中所指定資源。 將被拒絕訪問所有其他存儲帳戶。
-   在子網上應用服務終結點策略時，Azure 存儲*服務終結點作用域*將從區域升級到**全域**。 這意味著 Azure 存儲的所有流量都通過服務終結點進行保護。 服務終結點策略也全域適用，因此任何不允許顯式訪問的存儲帳戶都將被拒絕訪問。 
-   您可以將多個原則套用至子網路。 當多個策略與子閘道聯時，將允許虛擬網路流量到任何這些策略中指定的資源。 若要存取任何原則中未指定的所有其他服務資源，則會遭到拒絕。

    > [!NOTE]  
    > 服務終結點策略是**允許策略**，因此除了指定的資源外，所有其他資源都受到限制。 請確保在策略中標識並列出應用程式的所有服務資源依賴項。

- 端點原則中只能指定使用 Azure 資源模型的儲存體帳戶。 您經典的 Azure 存儲帳戶將不支援 Azure 服務終結點策略。
- 如果已列出主要帳戶，系統就會自動允許 RA-GRS 次要存取。
- 儲存體帳戶不一定要位於與虛擬網路相同的訂用帳戶或 Azure Active Directory 租用戶中。

## <a name="scenarios"></a>案例

- **對等互連或多個虛擬網路**：若要篩選對等互連虛擬網路中的流量，您應該將端點原則個別套用至這些虛擬網路。
- **使用網路設備或 Azure 防火牆篩選 Internet 流量**：使用策略篩選 Azure 服務流量，通過服務終結點篩選 Azure 服務流量，並通過設備或 Azure 防火牆篩選 Internet 或 Azure 流量的其餘部分。
- **篩選部署到虛擬網路中的 Azure 服務的流量**：此時，部署到虛擬網路的任何託管 Azure 服務都不支援 Azure 服務終結點策略。 
- **篩選從內部部署到 Azure 服務的流量**：服務端點原則只適用於來自原則相關聯子網路的流量。 若要允許從內部部署存取特定的 Azure 服務資源，則應該使用網路虛擬設備或防火牆篩選流量。

## <a name="logging-and-troubleshooting"></a>記錄和疑難排解
服務端點原則並未提供任何集中式記錄。 如需服務診斷記錄，請參閱[服務端點記錄](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting)。

### <a name="troubleshooting-scenarios"></a>疑難排解案例
- 拒絕訪問在預覽版中工作的存儲帳戶（不在地理配對區域中）
  - 通過 Azure 存儲升級以使用全域服務標記，服務終結點和服務終結點策略的範圍現在為全域。 因此，對 Azure 存儲的任何流量都通過服務終結點加密，只允許訪問策略中顯式列出的存儲帳戶。
  - 顯式允許列出還原存取權限所需的所有存儲帳戶。  
  - 連絡 Azure 支援。
- 存取端點原則中所列的帳戶時遭到拒絕
  - 網路安全性群組或防火牆篩選條件可能會封鎖存取
  - 如果移除/重新套用原則會導致連線中斷：
    - 驗證 Azure 服務是否配置為允許從虛擬網路通過終結點進行訪問，或者資源的預設策略是否設置為 *"允許全部*"。
    - 驗證服務診斷是否顯示透過端點的流量。
    - 請檢查網路安全性群組流量記錄是否顯示存取狀態，而儲存體記錄是否如預期般顯示透過服務端點的存取狀態。
    - 連絡 Azure 支援。
- 存取服務端點原則中未列的帳戶時遭到拒絕
  - 驗證 Azure 存儲是否配置為允許從虛擬網路通過終結點進行訪問，或者資源的預設策略是否設置為 *"允許全部*"。
  - 確保這些帳戶不是具有子網上服務終結點策略**的經典存儲帳戶**。
- 在子網上應用服務終結點策略後，託管 Azure 服務停止工作
  - 此時服務終結點策略不支援託管服務。 *請觀看此空間以獲取更新*。

## <a name="provisioning"></a>佈建

擁有虛擬網路寫入權的使用者可以在子網路上設定服務端點原則。 深入了解 Azure [內建角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，以及如何將特定權限指派給[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

虛擬網路和 Azure 存儲帳戶可以位於相同或不同的訂閱中，也可以位於 Azure 活動目錄租戶中。

## <a name="limitations"></a>限制

- 您只能在透過 Azure Resource Manager 部署模型所部署的虛擬網路上部署服務端點原則。
- 虛擬網路必須位於與服務端點原則相同的區域中。
- 如果原則中列出的 Azure 服務已設定服務端點，您就只能將服務端點原則套用至子網路。
- 如果流量是從內部部署網路流向 Azure 服務，您就無法對其使用服務端點原則。
- Azure 託管服務當前不支援終結點策略。 這包括部署到共用子網的託管服務（例如*Azure HDInsight、Azure 批次處理、Azure ADDS、Azure APplication 閘道、Azure VPN 閘道、Azure 防火牆*）或專用子網（例如*Azure 應用服務環境、Azure Redis 緩存、Azure API 管理、Azure SQL MI、經典託管服務*）。

 > [!WARNING]
 > 針對基礎結構需求，部署到虛擬網路中的 Azure 服務 (例如 Azure HDInsight) 可存取其他 Azure 服務 (例如 Azure 儲存體)。 如果您將端點原則限制給特定資源，可能會中斷存取虛擬網路中已部署 Azure 服務的這些基礎結構資源。

- 端點原則不支援傳統儲存體帳戶。 原則預設會拒絕存取所有傳統儲存體帳戶。 如果您的應用程式需要存取 Azure Resource Manager 和傳統儲存體帳戶，就不應針對此流量使用端點原則。

## <a name="pricing-and-limits"></a>價格和限制

使用服務端點原則不會額外收費。 透過服務端點的 Azure 服務 (例如 Azure 儲存體) 其目前定價模式照常適用。

服務端點原則會強制執行下列限制： 

 |資源 | 預設限制 |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>後續步驟

- 了解[如何設定虛擬網路服務端點原則](virtual-network-service-endpoint-policies-portal.md)
- 瞭解有關[虛擬網路服務終結點](virtual-network-service-endpoints-overview.md)的更多資訊
