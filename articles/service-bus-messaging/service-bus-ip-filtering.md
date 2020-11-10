---
title: 設定 Azure 服務匯流排的 IP 防火牆規則
description: 如何使用「防火牆規則」允許從特定 IP 位址連線至「Azure 服務匯流排」。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3aacf54dca07f0e1f2a66c8cdd85f892dda68cd4
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426561"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>允許從特定 IP 位址或範圍存取 Azure 服務匯流排命名空間
根據預設，只要要求具備有效的驗證和授權，便可以從網際網路存取服務匯流排命名空間。 透過 IP 防火牆，您可以將其進一步限制為僅允許一組 IPv4 位址，或是使用 [CIDR (無類別網域間路由)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 標記法來設定 IPv4 位址範圍。

此功能在只應該從特定知名網站存取 Azure 服務匯流排的情況下，會很有幫助。 防火牆規則可讓您設定規則以接受源自特定 IPv4 位址的流量。 例如，如果您搭配 [Azure Express Route][express-route] 使用服務匯流排，您可以建立 **防火牆規則** 以僅允許來自您內部部署基礎結構 IP 位址或公司 NAT 閘道位址的流量。 

> [!IMPORTANT]
> 只有在 **進階** 層服務匯流排中，才支援防火牆和虛擬網路。 如果升級至 **頂級** 層不可行，建議您保護共用存取簽章 (SAS) 權杖的安全性，並且只與已授權的使用者共用。 如需 SAS 驗證的相關資訊，請參閱[驗證和授權](service-bus-authentication-and-authorization.md#shared-access-signature)。

## <a name="ip-firewall-rules"></a>IP 防火牆規則
IP 防火牆規則會套用在服務匯流排命名空間層級上。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 任何來自某個 IP 位址的連線嘗試，只要不符合「服務匯流排」命名空間上的允許 IP 規則，系統就會將它視為未經授權而予以拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

>[!WARNING]
> 實作「防火牆」規則可防止其他 Azure 服務與「服務匯流排」進行互動。 例外狀況是，即使在啟用 IP 篩選時，您也可以允許從某些受信任的服務存取服務匯流排資源。 如需信任的服務清單，請參閱 [信任的服務](#trusted-microsoft-services)。 
>
> 虛擬網路上必須有下列 Microsoft 服務
> - Azure App Service
> - Azure Functions

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節說明如何使用 Azure 入口網站為服務匯流排命名空間建立 IP 防火牆規則。 

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)中的 [服務匯流排命名空間]。
2. 在左側功能表中，選取 [ **設定** ] 下的 [ **網路** 功能] 選項。  

    > [!NOTE]
    > 您只會看到 **premium** 命名空間的 [ **網路** ] 索引標籤。  
    
    預設會選取 [ **選取的網路** ] 選項。 如果您未在此頁面上新增至少一個 IP 防火牆規則或虛擬網路，可以使用存取金鑰) ，透過公用網際網路 (存取命名空間。

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="網路頁面-預設值" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    如果您選取 [ **所有網路** ] 選項，則您的服務匯流排命名空間會接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![[Azure 入口網站網路] 頁面的螢幕擷取畫面。 [防火牆和虛擬網路] 索引標籤上已選取允許從所有網路存取的選項。](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. 若只要允許從指定的 IP 位址進行存取，請選取 [ **選取的網路** ] 選項（如果尚未選取的話）。 在 [防火牆] 區段中，依照下列步驟：
    1. 選取 [新增您的用戶端 IP 位址] 選項，來將命名空間的存取權授與您目前的用戶端 IP。 
    2. 針對 [位址範圍]，輸入特定的 IPv4 位址，或是以 CIDR 標記法輸入 IPv4 位址的範圍。 
    3. 指定您是否要 [允許受信任的 Microsoft 服務略過此防火牆]。 

        > [!WARNING]
        > 如果您選擇 [選取的網路] 選項，且不指定 IP 位址或位址範圍，服務將會允許來自所有網路的流量。 

        ![[Azure 入口網站網路] 頁面的螢幕擷取畫面。 已選取 [允許從選取的網路存取] 選項，並反白顯示 [防火牆] 區段。](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. 選取工具列上的 [儲存] 來儲存設定。 等候幾分鐘的時間，讓入口網站通知上顯示確認訊息。

    > [!NOTE]
    > 若要限制對特定虛擬網路的存取，請參閱 [允許從特定網路存取](service-bus-service-endpoints.md)。

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
本節提供的範例 Azure Resource Manager 範本會建立虛擬網路和防火牆規則。


下列 Resource Manager 範本可讓您將虛擬網路規則新增至現有的服務匯流排命名空間。

範本參數：

- **ipMask** 是單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 標記法中，70.37.104.0/24 表示從 70.37.104.0 開始，到 70.37.104.255 為止，總共 256 個 IPv4 位址，而 24 則表示該範圍內的顯著前置詞位元。

> [!NOTE]
> 雖然無法使用任何拒絕規則，但 Azure Resource Manager 範本是將預設動作設定為不會限制連線的 **"Allow"** 。
> 建立虛擬網路或防火牆規則時，必須變更 **_"defaultAction"_**
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
        "type": "Microsoft.ServiceBus/namespaces/networkrulesets",
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

若要部署範本，請依照適用於 [Azure Resource Manager][lnk-deploy] 的指示執行。

## <a name="next-steps"></a>後續步驟

若要將對服務匯流排的存取限定為 Azure 虛擬網路，請參閱下列連結：

- [服務匯流排的虛擬網路服務端點][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services