---
title: Azure 事件中樞防火牆規則 | Microsoft Docs
description: 使用「防火牆規則」以允許從特定 IP 位址連線至「Azure 事件中樞」。
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 2d67ad70ccdf4ce1f88401806700f38bd1d3c11d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655878"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>設定 Azure 事件中樞命名空間的 IP 防火牆規則
根據預設，只要要求具備有效的驗證和授權，便可以從網際網路存取事件中樞命名空間。 透過 IP 防火牆，您可以將其進一步限制為僅允許一組 IPv4 位址，或是使用 [CIDR (無類別網域間路由)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 標記法來設定 IPv4 位址範圍。

此功能在只應該從特定知名網站存取 Azure 事件中樞的情況下會很有幫助。 防火牆規則可讓您設定規則以接受源自特定 IPv4 位址的流量。 例如，如果您搭配 [Azure Express Route][express-route] 使用事件中樞，您可以建立**防火牆規則**以僅允許來自您內部部署基礎結構 IP 位址的流量。 

>[!WARNING]
> 啟用 IP 篩選可防止其他 Azure 服務與事件中樞互動。
>
> 實作「虛擬網路」時，不支援受信任的 Microsoft 服務。
>
> 無法與「虛擬網路」搭配運作的常見 Azure 案例 (請注意，這**不是**完整的清單) -
> - Azure 監視器 (診斷設定)
> - Azure 串流分析
> - 與 Azure 事件方格的整合
> - Azure IoT 中樞路由
> - Azure IoT Device Explorer
>
> 虛擬網路上必須有下列 Microsoft 服務
> - Azure Web Apps
> - Azure Functions


## <a name="ip-firewall-rules"></a>IP 防火牆規則
IP 防火牆規則會在事件中樞命名空間層級套用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 任何來自某個 IP 位址的連線嘗試，只要不符合「事件中樞」命名空間上的允許 IP 規則，系統就會將其視為未經授權而予以拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
此節會示範如何使用 Azure 入口網站來為事件中樞命名空間建立 IP 防火牆規則。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的**事件中樞命名空間**。
2. 在左側功能表上，選取 [網路] 選項。 如果您選取 [所有網路] 選項，事件中樞便會接受來自任何 IP 位址的連線。 此設定等同於接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![防火牆 - 已選取 [所有網路] 選項](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 若要將存取限制到特定網路和 IP 位址，請選取 [選取的網路] 選項。 在 [防火牆] 區段中，依照下列步驟執行：
    1. 選取 [新增您的用戶端 IP 位址] 選項，來將命名空間的存取權授與您目前的用戶端 IP。 
    2. 針對 [位址範圍]，輸入特定的 IPv4 位址，或是以 CIDR 標記法輸入 IPv4 位址的範圍。 
    3. 指定您是否要 [允許受信任的 Microsoft 服務略過此防火牆]。 

        > [!WARNING]
        > 如果您選擇 [選取的網路] 選項，且不指定 IP 位址或位址範圍，服務將會允許來自所有網路的流量。 

        ![防火牆 - 已選取 [所有網路] 選項](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. 選取工具列上的 [儲存] 來儲存設定。 等候幾分鐘的時間，讓入口網站通知上顯示確認訊息。


## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本

> [!IMPORTANT]
> 「事件中樞」的**標準**和**專用**層級可支援防火牆規則。 基本層中不支援虛擬網路。

下列 Resource Manager 範本可讓您將 IP 篩選器規則新增至現有的事件中樞命名空間。

範本參數：

- **ipMask** 是單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 標記法中，70.37.104.0/24 表示從 70.37.104.0 開始，到 70.37.104.255 為止，總共 256 個 IPv4 位址，而 24 則表示該範圍內的顯著前置詞位元。

> [!NOTE]
> 雖然無法使用任何拒絕規則，但 Azure Resource Manager 範本是將預設動作設定為不會限制連線的 **"Allow"** 。
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
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
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
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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

若要部署範本，請依照適用於 [Azure Resource Manager][lnk-deploy] 的指示執行。

## <a name="next-steps"></a>後續步驟

若要將對事件中樞的存取權限制為 Azure 虛擬網路，請參閱下列連結：

- [事件中樞的虛擬網路服務端點][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
