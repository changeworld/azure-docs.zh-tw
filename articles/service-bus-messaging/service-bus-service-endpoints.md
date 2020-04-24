---
title: 設定 Azure 服務匯流排的虛擬網路服務端點
description: 本文提供如何將 Microsoft 服務端點新增至虛擬網路的相關資訊。
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: d1766ffb579bb1a86da91ac73a396ce0d008f89e
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117619"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>設定 Azure 服務匯流排的虛擬網路服務端點

將服務匯流排與[虛擬網路（VNet）服務端點][vnet-sep]整合，可讓您從系結至虛擬網路的工作負載（例如虛擬機器）安全地存取訊息功能，而兩端的網路流量路徑都受到保護。

一旦設定為繫結到至少一個虛擬網路子網路服務端點，個別的服務匯流排命名空間除了授權的虛擬網路以外，無法再接受任何位置的流量。 從虛擬網路的觀點而言，將服務匯流排命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離網路通道。

結果是繫結至子網路的工作負載與個別服務匯流排命名空間之間的私人和隔離關係，儘管傳訊服務端點的可觀察網路位址是在公用 IP 範圍中。

>[!WARNING]
> 實作虛擬網路整合可以防止其他 Azure 服務與服務匯流排互動。
>
> 實作「虛擬網路」時，不支援受信任的 Microsoft 服務。
>
> 無法與「虛擬網路」搭配運作的常見 Azure 案例 (請注意，這**不是**完整的清單) -
> - 與 Azure 事件方格的整合
> - Azure IoT 中樞路由
> - Azure IoT Device Explorer
>
> 虛擬網路上必須有下列 Microsoft 服務
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> 唯有[進階層](service-bus-premium-messaging.md)服務匯流排命名空間支援虛擬網路。
> 
> 搭配服務匯流排使用 VNet 服務端點時，您不應該在混合標準和進階層服務匯流排命名空間的應用程式中啟用這些端點。 因為標準層不支援 Vnet。 端點僅限進階層命名空間。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要緊密和劃分安全性，而且虛擬網路子網路分割各劃分服務的解決方案，通常仍然需要位於這些區間之各項服務之間的通訊路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 傳訊服務提供完全隔離的通訊路徑，在其中，當訊息在對象之間轉換時，甚至會寫入到磁碟。 兩個都繫結到相同服務匯流排執行個體的不同虛擬網路，其中的工作負載可以透過訊息有效且可靠地通訊，同時保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得了 Azure 領先業界可靠和可擴充非同步傳訊功能支援，且現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，本質上會比任何對等通訊模式 (包括 HTTPS 和其他受 TLS 保護的通訊端通訊協定) 所能達成的更為安全。

## <a name="binding-service-bus-to-virtual-networks"></a>將服務匯流排繫結至虛擬網路

「虛擬網路規則」** 是防火牆安全性功能，可控制 Azure 服務匯流排伺服器是否接受來自特定虛擬網路子網路的連線。

將服務匯流排命名空間繫結至虛擬網路是一個雙步驟的程序。 您必須先在虛擬網路子網上建立**虛擬網路服務端點**，並如[服務端點總覽][vnet-sep]中所述，針對 Microsoft 進行啟用 **。** 一旦您新增服務端點，您就可使用「虛擬網路規則」**** 將服務匯流排命名空間與它繫結。

虛擬網路規則是服務匯流排命名空間與虛擬網路子網路的關聯。 當此規則存在時，繫結至子網路的所有工作負載都會獲得授與服務匯流排命名空間的存取權。 服務匯流排本身永遠不會建立輸出連線，不需要獲得存取權，因此永遠不會因為啟用這項規則而獲得授與子網路的存取權。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節說明如何使用 Azure 入口網站來新增虛擬網路服務端點。 若要限制存取，您需要整合此事件中樞命名空間的虛擬網路服務端點。

1. 流覽至您在[Azure 入口網站](https://portal.azure.com)中的**服務匯流排命名空間**。
2. 在左側功能表上，選取 [**網路**] 選項。 預設會選取 [**所有網路**] 選項。 您的命名空間會接受來自任何 IP 位址的連接。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![[防火牆-所有網路] 選項已選取](./media/service-endpoints/firewall-all-networks-selected.png)
1. 選取頁面頂端的 [**選取的網路**] 選項。
2. 在頁面的 [**虛擬網路**] 區段中，選取 [ **+ 新增現有的虛擬網路**]。 

    ![新增現有的虛擬網路](./media/service-endpoints/add-vnet-menu.png)
3. 從虛擬網路清單中選取虛擬網路，然後挑選**子網**。 您必須先啟用服務端點，才能將虛擬網路新增至清單。 如果服務端點未啟用，入口網站會提示您啟用它。
   
   ![選取子網](./media/service-endpoints/select-subnet.png)

4. 在為**Microsoft**啟用子網的服務端點之後，您應該會看到下列成功訊息。 選取頁面底部的 [**新增**] 以新增網路。 

    ![選取子網路並啟用端點](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 如果您無法啟用服務端點，您可以使用 Resource Manager 範本來忽略遺失的虛擬網路服務端點。 在入口網站上無法使用這項功能。
6. 選取工具列上的 [**儲存**] 來儲存設定。 等候幾分鐘的時間，確認才會顯示在入口網站通知中。 [**儲存**] 按鈕應該是停用的。 

    ![儲存網路](./media/service-endpoints/save-vnet.png)

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
