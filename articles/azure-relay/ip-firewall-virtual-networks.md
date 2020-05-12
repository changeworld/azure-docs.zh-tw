---
title: 設定 Azure 轉送命名空間的 IP 防火牆
description: 本文說明如何使用防火牆規則，以允許來自特定 IP 位址的連線至 Azure 轉送命名空間。
services: service-bus-relay
documentationcenter: ''
author: spelluru
ms.service: service-bus-relay
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: e2a9e89ca8c3b77b09a58a45959f1acda1457b56
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211050"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>設定 Azure 轉送命名空間的 IP 防火牆
根據預設，只要要求隨附有效的驗證和授權，就可以從網際網路存取轉送命名空間。 使用 IP 防火牆，您可以將它進一步限制為[CIDR （無類別網域間路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)標記法中的一組 ipv4 位址或 ipv4 位址範圍。

這項功能在應該只能從特定知名網站存取 Azure 轉送的情況下很有説明。 防火牆規則可讓您設定規則，以接受來自特定 IPv4 位址的流量。 例如，如果您使用轉送搭配[Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)，您可以建立**防火牆規則**，只允許來自您內部部署基礎結構 IP 位址的流量。 


> [!IMPORTANT]
> 此功能目前為預覽狀態。 


## <a name="enable-ip-firewall-rules"></a>啟用 IP 防火牆規則
IP 防火牆規則會在命名空間層級套用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 從不符合命名空間上允許之 IP 規則的 IP 位址進行的任何連線嘗試都會被視為未經授權而遭到拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

### <a name="use-azure-portal"></a>使用 Azure 入口網站
本節說明如何使用 Azure 入口網站來建立命名空間的 IP 防火牆規則。 

1. 在[Azure 入口網站](https://portal.azure.com)中，流覽至您的**轉送命名空間**。
2. 在左側功能表上，選取 [**網路**] 選項。 如果您在 [**允許從下列位置存取**] 區段中選取 [**所有網路**] 選項，則轉送命名空間會接受來自任何 IP 位址的連線。 此設定相當於接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![[防火牆-所有網路] 選項已選取](./media/ip-firewall/all-networks-selected.png)
1. 若要限制對特定網路和 IP 位址的存取，請選取 [**選取的網路**] 選項。 在 [**防火牆**] 區段中，依照下列步驟執行：
    1. 選取 [**新增您的用戶端 ip 位址**] 選項，將命名空間的存取權授與您目前的用戶端 ip。 
    2. 在 [**位址範圍**] 中，以 CIDR 標記法輸入特定的 ipv4 位址或 ipv4 位址範圍。 
    3. 指定您是否要**允許受信任的 Microsoft 服務略過此防火牆**。 

        ![[防火牆-所有網路] 選項已選取](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. 選取工具列上的 [**儲存**] 來儲存設定。 等候幾分鐘的時間，確認才會顯示在入口網站通知上。


### <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
下列 Resource Manager 範本可讓您將 IP 篩選規則新增至現有的轉送命名空間。

範本會採用一個參數： **ipMask**，這是單一 IPv4 位址或 CIDR 標記法中的 IP 位址區塊。 例如，在 CIDR 標記法中，70.37.104.0/24 表示從 70.37.104.0 開始，到 70.37.104.255 為止，總共 256 個 IPv4 位址，而 24 則表示該範圍內的顯著前置詞位元。

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
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.Relay/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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

若要部署範本，請依照 [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md) 適用的指示執行。



## <a name="next-steps"></a>後續步驟
若要瞭解其他與網路安全性相關的功能，請參閱[網路安全性](network-security.md)。


<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[.lnk-deploy]：: 
