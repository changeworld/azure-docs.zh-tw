---
title: 為 Azure 服務組態 IP 防火牆規則
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
ms.openlocfilehash: 24591c20ed707d9541eece0698ecd6e6b5ddee35
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878182"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>為 Azure 服務組態 IP 防火牆規則
默認情況下,只要請求附帶有效的身份驗證和授權,服務總線命名空間即可從 Internet 訪問。 使用 IP 防火牆,您可以進一步將其限制為[CIDR(無類域間路由)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法中的一組 IPv4 位址或 IPv4 位址範圍。

此功能在 Azure 服務總線應僅從某些已知網站訪問的情況下非常有用。 防火牆規則使您能夠配置規則以接受來自特定 IPv4 位址的流量。 例如,如果將服務總線與 Azure[快速路由][express-route]一起使用 ,則可以創建**防火牆規則**,僅允許來自本地基礎結構 IP 位址或公司 NAT 閘道位址的流量。 

> [!IMPORTANT]
> 防火牆和虛擬網路僅在**服務**總線的高級層中支援。 如果升級到**首選層**不是一個選項,我們建議您保持共享訪問簽名 (SAS) 令牌的安全,並僅與授權使用者共用。 關於 SAS 認證資訊,請參考[身份認證與授權](service-bus-authentication-and-authorization.md#shared-access-signature)。

## <a name="ip-firewall-rules"></a>IP 防火牆規則
IP 防火牆規則在服務總線命名空間級別應用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 任何來自某個 IP 位址的連線嘗試，只要不符合「服務匯流排」命名空間上的允許 IP 規則，系統就會將它視為未經授權而予以拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節介紹如何使用 Azure 門戶為服務總線命名空間創建 IP 防火牆規則。 

1. 瀏覽到[Azure 門戶](https://portal.azure.com)中的**服務匯流命名空間**。
2. 在左側選單上,選擇 **「網路**」選項。 預設情況下,選擇所有**網路**「選項」。 服務總線命名空間接受來自任何 IP 位址的連接。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![防火牆 ─ 選擇的所有網路選項](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. 選擇頁面頂部的 **「選定網路**」選項。 在 **「防火牆」** 部分中,按照以下步驟操作:
    1. 選擇 **「新增用戶端 IP 位址**」選項,使目前用戶端 IP 有權存取命名空間。 
    2. 對於**位址範圍**,在 CIDR 表示法中輸入特定的 IPv4 位址或 IPv4 位址範圍。 
    3. 指定是否要**允許受信任的 Microsoft 服務繞過此防火牆**。 

        ![防火牆 ─ 選擇的所有網路選項](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. 選擇 **「在**工具列上保存」 以儲存設定。 等待幾分鐘,確認顯示在門戶通知上。

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
本節具有創建虛擬網路和防火牆規則的示例 Azure 資源管理器範本。


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
