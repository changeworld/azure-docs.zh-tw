---
title: 為 Azure 服務匯流排配置虛擬網路服務終結點
description: 本文提供有關如何向虛擬網路添加 Microsoft.ServiceBus 服務終結點的資訊。
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9dbf65522d5c85e1054ed3f1f6ca9f86180e7f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454976"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>為 Azure 服務匯流排配置虛擬網路服務終結點

服務匯流排與[虛擬網路 （VNet） 服務終結點][vnet-sep]的集成使得對綁定到虛擬網路的工作負載（如虛擬機器）的消息傳遞功能進行安全訪問，並且網路流量路徑在兩端都受到保護。

一旦設定為繫結到至少一個虛擬網路子網路服務端點，個別的服務匯流排命名空間除了授權的虛擬網路以外，無法再接受任何位置的流量。 從虛擬網路的觀點而言，將服務匯流排命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離網路通道。

結果是繫結至子網路的工作負載與個別服務匯流排命名空間之間的私人和隔離關係，儘管傳訊服務端點的可觀察網路位址是在公用 IP 範圍中。

> [!IMPORTANT]
> 唯有[進階層](service-bus-premium-messaging.md)服務匯流排命名空間支援虛擬網路。
> 
> 將 VNet 服務終結點與服務匯流排一起使用時，不應在混合標準層和高級服務匯流排命名空間的應用程式中啟用這些終結點。 因為標準層不支援 VNet。 終結點僅限於高級層命名空間。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要緊密和劃分安全性，而且虛擬網路子網路分割各劃分服務的解決方案，通常仍然需要位於這些區間之各項服務之間的通訊路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 傳訊服務提供完全隔離的通訊路徑，在其中，當訊息在對象之間轉換時，甚至會寫入到磁碟。 兩個都繫結到相同服務匯流排執行個體的不同虛擬網路，其中的工作負載可以透過訊息有效且可靠地通訊，同時保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得了 Azure 領先業界可靠和可擴充非同步傳訊功能支援，且現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，本質上會比任何對等通訊模式 (包括 HTTPS 和其他受 TLS 保護的通訊端通訊協定) 所能達成的更為安全。

## <a name="binding-service-bus-to-virtual-networks"></a>將服務匯流排繫結至虛擬網路

「虛擬網路規則」** 是防火牆安全性功能，可控制 Azure 服務匯流排伺服器是否接受來自特定虛擬網路子網路的連線。

將服務匯流排命名空間繫結至虛擬網路是一個雙步驟的程序。 您首先需要在虛擬網路子網上創建**虛擬網路服務終結點**，並為**Microsoft**啟用[它。][vnet-sep] 一旦您新增服務端點，您就可使用「虛擬網路規則」**** 將服務匯流排命名空間與它繫結。

虛擬網路規則是服務匯流排命名空間與虛擬網路子網路的關聯。 當此規則存在時，繫結至子網路的所有工作負載都會獲得授與服務匯流排命名空間的存取權。 服務匯流排本身永遠不會建立輸出連線，不需要獲得存取權，因此永遠不會因為啟用這項規則而獲得授與子網路的存取權。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節介紹如何使用 Azure 門戶添加虛擬網路服務終結點。 要限制訪問，您需要集成此事件中心命名空間的虛擬網路服務終結點。

1. 導航到[Azure 門戶](https://portal.azure.com)中的**服務匯流排命名空間**。
2. 在左側功能表上，選擇 **"網路**"選項。 預設情況下，選擇"**所有網路**"選項。 您的命名空間接受來自任何 IP 位址的連接。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![防火牆 - 選擇的所有網路選項](./media/service-endpoints/firewall-all-networks-selected.png)
1. 選擇頁面頂部的 **"選定網路"** 選項。
2. 在頁面的 **"虛擬網路**"部分中，選擇 **"添加現有虛擬網路**"。 

    ![添加現有虛擬網路](./media/service-endpoints/add-vnet-menu.png)
3. 從虛擬網路清單中選擇虛擬網路，然後選擇**子網**。 在將虛擬網路添加到清單中之前，必須啟用服務終結點。 如果未啟用服務終結點，門戶將提示您啟用它。
   
   ![選擇子網](./media/service-endpoints/select-subnet.png)

4. 在為**Microsoft**啟用子網的服務終結點後，您應該會看到以下成功消息。 選擇**頁面底部的"添加**"以添加網路。 

    ![選取子網路並啟用端點](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 如果無法啟用服務終結點，則可以使用資源管理器範本忽略缺少的虛擬網路服務終結點。 在入口網站上無法使用這項功能。
6. 選擇 **"在**工具列上保存"以保存設置。 等待幾分鐘，確認顯示在門戶通知中。 應禁用 **"保存**"按鈕。 

    ![保存網路](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
下列 Resource Manager 範本可讓您將虛擬網路規則新增至現有的服務匯流排命名空間。

範本參數：

* **namespaceName**：服務匯流排命名空間。
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

範本：

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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
- [Azure 服務匯流排 IP 篩選][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
