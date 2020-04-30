---
title: 設定 Azure 服務匯流排的 IP 防火牆規則
description: 如何使用「防火牆規則」允許從特定 IP 位址連線至「Azure 服務匯流排」。
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9601689bbce9566b52664058911e9c45647152d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116813"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>設定 Azure 服務匯流排的 IP 防火牆規則
根據預設，只要要求隨附有效的驗證和授權，就可以從網際網路存取服務匯流排的命名空間。 使用 IP 防火牆，您可以將它進一步限制為[CIDR （無類別網域間路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)標記法中的一組 ipv4 位址或 ipv4 位址範圍。

這項功能在應該只能從特定知名網站存取 Azure 服務匯流排的情況下很有説明。 防火牆規則可讓您設定規則，以接受來自特定 IPv4 位址的流量。 例如，如果您使用服務匯流排搭配[Azure Express Route][express-route]，您可以建立**防火牆規則**，只允許來自您內部部署基礎結構 IP 位址或公司 NAT 閘道位址的流量。 

> [!IMPORTANT]
> 只有**服務匯流排的進**階層才支援防火牆和虛擬網路。 如果無法升級至**頂級**層，建議您將共用存取簽章（SAS）權杖保持在安全的狀態，並只與已授權的使用者共用。 如需 SAS 驗證的相關資訊，請參閱[驗證和授權](service-bus-authentication-and-authorization.md#shared-access-signature)。

## <a name="ip-firewall-rules"></a>IP 防火牆規則
IP 防火牆規則會在服務匯流排命名空間層級套用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 任何來自某個 IP 位址的連線嘗試，只要不符合「服務匯流排」命名空間上的允許 IP 規則，系統就會將它視為未經授權而予以拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

>[!WARNING]
> 實作「防火牆」規則可防止其他 Azure 服務與「服務匯流排」進行互動。
>
> 實作「IP 篩選」(防火牆規則) 時，不支援受信任的 Microsoft 服務，但很快就會提供這項支援。
>
> 無法與「IP 篩選」搭配運作的常見 Azure 案例 (請注意，這**不是**完整的清單) -
> - 與 Azure 事件方格的整合
> - Azure IoT 中樞路由
> - Azure IoT Device Explorer
>
> 下列 Microsoft 服務必須位於虛擬網路上
> - Azure App Service
> - Azure Functions

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節說明如何使用 Azure 入口網站來建立服務匯流排命名空間的 IP 防火牆規則。 

1. 流覽至您在[Azure 入口網站](https://portal.azure.com)中的**服務匯流排命名空間**。
2. 在左側功能表上，選取 [**網路**] 選項。 預設會選取 [**所有網路**] 選項。 您的服務匯流排命名空間會接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![[防火牆-所有網路] 選項已選取](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. 選取頁面頂端的 [**選取的網路**] 選項。 在 [**防火牆**] 區段中，依照下列步驟執行：
    1. 選取 [**新增您的用戶端 ip 位址**] 選項，將命名空間的存取權授與您目前的用戶端 ip。 
    2. 在 [**位址範圍**] 中，以 CIDR 標記法輸入特定的 ipv4 位址或 ipv4 位址範圍。 
    3. 指定您是否要**允許受信任的 Microsoft 服務略過此防火牆**。 

        ![[防火牆-所有網路] 選項已選取](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. 選取工具列上的 [**儲存**] 來儲存設定。 等候幾分鐘的時間，確認才會顯示在入口網站通知上。

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
本節提供的範例 Azure Resource Manager 範本會建立虛擬網路和防火牆規則。


下列 Resource Manager 範本可讓您將虛擬網路規則新增至現有的服務匯流排命名空間。

範本參數：

- **ipMask** 是單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 標記法中，70.37.104.0/24 表示從 70.37.104.0 開始，到 70.37.104.255 為止，總共 256 個 IPv4 位址，而 24 則表示該範圍內的顯著前置詞位元。

> [!NOTE]
> 雖然無法使用任何拒絕規則，但 Azure Resource Manager 範本是將預設動作設定為不會限制連線的 **"Allow"**。
> 在建立「虛擬網路」或「防火牆」規則時，我們必須將 ***"defaultAction"***
> 
> 從
> ```json
> "defaultAction": "Allow"
> ```
> to
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

若要部署範本，請依照 [Azure Resource Manager][lnk-deploy] 適用的指示執行。

## <a name="next-steps"></a>後續步驟

若要將對服務匯流排的存取限定為 Azure 虛擬網路，請參閱下列連結：

- [服務匯流排的虛擬網路服務端點][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
