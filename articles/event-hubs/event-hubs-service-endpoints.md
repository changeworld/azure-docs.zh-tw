---
title: 虛擬網路服務端點 - Azure 事件中樞 | Microsoft Docs
description: 本文提供如何將 Microsoft EventHub 服務端點新增至虛擬網路的相關資訊。
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: cb0d9a9c4d5e2503e68620ec4e6386d8e05d471c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185057"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>允許從特定虛擬網路存取 Azure 事件中樞命名空間 

將事件中樞與[虛擬網路 (VNet) 服務端點][vnet-sep]整合，可以安全存取來自虛擬機器 (繫結至虛擬網路) 等工作負載的傳訊功能，而且兩端的網路流量路徑都能受到保護。

一旦設定為系結到至少一個虛擬網路子網服務端點，個別的事件中樞命名空間就不再接受來自虛擬網路中任何地點的流量，而不接受來自已授權子網的流量。 從虛擬網路的角度來看，將事件中樞命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離式網路通道。 

最終這會在繫結至子網路的工作負載與各自的事件中樞命名空間之間，建立私人且隔離的關係，儘管傳訊服務端點顯示的網路位址位於公用 IP 範圍中。 此行為有例外狀況。 啟用服務端點時，根據預設，會啟用 `denyall` 與虛擬網路相關聯之 [IP 防火牆](event-hubs-ip-filtering.md) 中的規則。 您可以在 IP 防火牆中新增特定 IP 位址，以啟用事件中樞公用端點的存取。 

>[!IMPORTANT]
> 事件中樞的**標準**和**專用**層級支援虛擬網路。 **基本**層中不支援此功能。
>
> 依預設，開啟事件中樞命名空間的防火牆規則會封鎖連入要求，除非要求是源自于允許的虛擬網路的服務。 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。 
>
> 以下是啟用虛擬網路時無法存取事件中樞資源的部分服務。 請注意，清單並 **不** 完整。
>
> - Azure 串流分析
> - Azure IoT 中樞路由
> - Azure IoT Device Explorer
> - Azure Event Grid
> - Azure 監視器 (診斷設定) 
>
> 例外狀況是，即使在啟用虛擬網路的情況下，您也可以允許從某些受信任的服務存取事件中樞資源。 如需信任的服務清單，請參閱 [信任的服務](#trusted-microsoft-services)。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要嚴格且劃分安全性的解決方案，以及虛擬網路子網提供劃分服務之間分割的位置，仍需要位於這些區間的服務之間的通訊路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 訊息服務提供了隔離的通訊路徑，其中訊息甚至是在合作物件之間轉換時寫入磁片。 兩個都繫結到相同事件中樞執行個體的不同虛擬網路，當中的工作負載可以透過訊息有效且可靠地通訊，並且保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得 Azure 可靠、可擴充的領先業界非同步傳訊功能的存取權，而且它們現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，這些區間本質上比任何對等通訊模式 (包括 HTTPS 和其他 TLS 保護的通訊端通訊協定) 更為安全。

## <a name="bind-event-hubs-to-virtual-networks"></a>將事件中樞系結至虛擬網路

「虛擬網路規則」**** 是防火牆安全性功能，可控制 Azure 事件中樞命名空間是否接受來自特定虛擬網路子網路的連線。

將事件中樞命名空間繫結至虛擬網路是一個雙步驟程序。 您必須先在虛擬網路的子網上建立虛擬網路服務端點，並將其啟用為適用于**Microsoft**的**虛擬網路服務端點**，如[服務端點總覽][vnet-sep]文章中所述。 新增服務端點之後，您可以使用 **虛擬網路規則**來系結事件中樞命名空間與它。

虛擬網路規則是「事件中樞」命名空間與虛擬網路子網路的關聯。 此規則存在時，繫結至該子網路的所有工作負載都會獲得事件中樞命名空間的存取權。 事件中樞本身永遠不會建立輸出連線，也不需要取得存取權，因此不會藉由啟用此規則來授與您子網的存取權。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節說明如何使用 Azure 入口網站來新增虛擬網路服務端點。 若要限制存取，您需要整合此事件中樞命名空間的虛擬網路服務端點。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的**事件中樞命名空間**。
4. 在左側功能表的 [**設定**] 底下選取 [**網路**]。 您只會看到標準或**專用**命名空間的 [**網路**] 索引**卷**標。 

    > [!NOTE]
    > 預設會選取 [ **選取的網路** ] 選項，如下圖所示。 如果您未在此頁面上指定 IP 防火牆規則或新增虛擬網路，可以使用存取金鑰) ，透過 **公用網際網路** (來存取命名空間。 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="[網路] 索引標籤-選取的網路選項" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    如果您選取 [ **所有網路** ] 選項，事件中樞會接受來自任何 IP 位址的連線， (使用存取金鑰) 。 此設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![防火牆 - 已選取 [所有網路] 選項](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 若要限制對特定網路的存取，請在頁面頂端選取 [ **選取的網路** ] 選項（如果尚未選取的話）。
2. 在頁面的 [ **虛擬網路** ] 區段中，選取 [+ 新增現有的虛擬網路]。 如果您想要建立新的 VNet，請選取 [ **+ 建立新的虛擬網路** ]。 

    ![新增現有的虛擬網路](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. 從虛擬網路清單中選取虛擬網路，然後挑選 **子網**。 您必須先啟用服務端點，才能將虛擬網路新增至清單。 如果未啟用服務端點，入口網站會提示您啟用它。
   
   ![選取子網](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. 針對 **Microsoft**的子網啟用服務端點之後，您應該會看到下列成功訊息。 選取頁面底部的 [ **新增** ] 以新增網路。 

    ![選取子網路並啟用端點](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 如果您無法啟用服務端點，您可以使用 Resource Manager 範本來忽略遺失的虛擬網路服務端點。 在入口網站上無法使用這項功能。
5. 指定您是否要 [允許受信任的 Microsoft 服務略過此防火牆]。 如需詳細資訊，請參閱 [信任的 Microsoft 服務](#trusted-microsoft-services) 。 
6. 選取工具列上的 [儲存] 來儲存設定。 等候幾分鐘的時間，讓入口網站通知上顯示確認訊息。

    ![儲存網路](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > 若要限制對特定 IP 位址或範圍的存取，請參閱 [允許從特定 ip 位址或範圍進行存取](event-hubs-ip-filtering.md)。

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本

下列 Resource Manager 範本可讓您將虛擬網路規則新增至現有的事件中樞命名空間。

範本參數：

* `namespaceName`：事件中樞命名空間。
* `vnetRuleName`：要建立的虛擬網路規則名稱。
* `virtualNetworkingSubnetId`：虛擬網路子網的完整 Resource Manager 路徑;例如， `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` 針對虛擬網路的預設子網。

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

若要部署範本，請依照適用於 [Azure Resource Manager][lnk-deploy] 的指示執行。

## <a name="next-steps"></a>後續步驟

如需虛擬網路的詳細資訊，請參閱下列連結：

- [Azure 虛擬網路服務端點][vnet-sep]
- [Azure 事件中樞 IP 篩選][ip-filtering] (英文)

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
