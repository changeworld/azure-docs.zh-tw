---
title: 虛擬網路服務端點 - Azure 事件中樞 | Microsoft Docs
description: 本文提供有關如何向虛擬網路添加 Microsoft.EventHub 服務終結點的資訊。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 6de51c23bd6358a6f54fe3baf9e9b256047d4ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064902"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>將虛擬網路服務端點搭配 Azure 事件中樞使用

將事件中樞與[虛擬網路 (VNet) 服務端點][vnet-sep]整合，可以安全存取來自虛擬機器 (繫結至虛擬網路) 等工作負載的傳訊功能，而且兩端的網路流量路徑都能受到保護。

一旦配置為至少綁定到一個虛擬網路子網服務終結點，相應的事件中心命名空間將不再接受來自虛擬網路中的經過授權的子網的流量。 從虛擬網路的角度來看，將事件中樞命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離式網路通道。 

最終這會在繫結至子網路的工作負載與各自的事件中樞命名空間之間，建立私人且隔離的關係，儘管傳訊服務端點顯示的網路位址位於公用 IP 範圍中。 此行為有一個例外。 預設情況下，啟用服務終結點可在[IP](event-hubs-ip-filtering.md)防火牆`denyall`中啟用與虛擬網路關聯的規則。 您可以在 IP 防火牆中添加特定的 IP 位址，以啟用對事件中心公共終結點的訪問。 

> [!IMPORTANT]
> 事件中樞的**標準**和**專用**層級支援虛擬網路。 **基本**層不支援它。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要嚴格和分門別類安全性的解決方案，以及虛擬網路子網在隔離服務之間提供分段時，仍然需要位於這些隔離區中的服務之間的通訊路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 消息傳遞服務提供絕緣通訊路徑，其中消息甚至在各方之間轉換時寫入磁片。 兩個都繫結到相同事件中樞執行個體的不同虛擬網路，當中的工作負載可以透過訊息有效且可靠地通訊，並且保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得 Azure 可靠、可擴充的領先業界非同步傳訊功能的存取權，而且它們現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，這些區間本質上比任何對等通訊模式 (包括 HTTPS 和其他 TLS 保護的通訊端通訊協定) 更為安全。

## <a name="bind-event-hubs-to-virtual-networks"></a>將事件中心綁定到虛擬網路

「虛擬網路規則」**** 是防火牆安全性功能，可控制 Azure 事件中樞命名空間是否接受來自特定虛擬網路子網路的連線。

將事件中樞命名空間繫結至虛擬網路是一個雙步驟程序。 首先需要在虛擬網路的子網上創建**虛擬網路服務終結點**，並為**Microsoft.EventHub**啟用它，如[服務終結點概述][vnet-sep]文章中所述。 一旦您新增服務端點，便會使用**虛擬網路規則**將事件中樞命名空間與其繫結。

虛擬網路規則是「事件中樞」命名空間與虛擬網路子網路的關聯。 此規則存在時，繫結至該子網路的所有工作負載都會獲得事件中樞命名空間的存取權。 事件中心本身永遠不會建立出站連接，不需要訪問，因此永遠不會通過啟用此規則被授予對子網的存取權限。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節介紹如何使用 Azure 門戶添加虛擬網路服務終結點。 要限制訪問，您需要集成此事件中心命名空間的虛擬網路服務終結點。

1. 導航到[Azure 門戶](https://portal.azure.com)中的**事件中心命名空間**。
2. 在左側功能表上，選擇 **"網路**"選項。 如果選擇"**所有網路"** 選項，事件中心將接受來自任何 IP 位址的連接。 此設置等效于接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![防火牆 - 選擇的所有網路選項](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 要恢復對特定網路的訪問，請選擇頁面頂部的 **"選定網路"** 選項。
2. 在頁面的 **"虛擬網路**"部分中，選擇[添加現有虛擬網路]。 如果要創建新的 VNet，請選擇 **"創建新虛擬網路**"。 

    ![添加現有虛擬網路](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. 從虛擬網路清單中選擇虛擬網路，然後選擇**子網**。 在將虛擬網路添加到清單中之前，必須啟用服務終結點。 如果未啟用服務終結點，門戶將提示您啟用它。
   
   ![選擇子網](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. 在為**Microsoft**啟用子網的服務終結點後，您應該會看到以下成功消息。 選擇**頁面底部的"添加**"以添加網路。 

    ![選取子網路並啟用端點](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 如果無法啟用服務終結點，則可以使用資源管理器範本忽略缺少的虛擬網路服務終結點。 在入口網站上無法使用這項功能。
6. 選擇 **"在**工具列上保存"以保存設置。 等待幾分鐘，確認顯示在門戶通知上。

    ![保存網路](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本

下列 Resource Manager 範本可讓您將虛擬網路規則新增至現有的事件中樞命名空間。

範本參數：

* **namespaceName**：事件中樞命名空間。
* **vnetRuleName**：要建立之虛擬網路規則的名稱。
* **virtualNetworkingSubnetId**：虛擬網路子網路的完整 Resource Manager 路徑，例如，`/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` 適用於虛擬網路的預設子網路。

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
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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

如需虛擬網路的詳細資訊，請參閱下列連結：

- [Azure 虛擬網路服務端點][vnet-sep]
- [Azure 事件中樞 IP 篩選][ip-filtering] (英文)

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
