---
title: 設定 Azure 服務匯流排的虛擬網路服務端點
description: 本文提供如何將 Microsoft 服務端點新增至虛擬網路的相關資訊。
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 8005a2c43d42908a9ad6ebea10b6a13ef381084c
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427644"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>允許從特定虛擬網路存取 Azure 服務匯流排命名空間
服務匯流排與 [虛擬網路 (VNet) 服務端點][vnet-sep] 的整合可讓您安全地存取來自系結至虛擬網路之虛擬機器的工作負載（例如，系結至虛擬網路的虛擬機器），並將網路流量路徑安全地放在兩端。

一旦設定為系結到至少一個虛擬網路子網服務端點，個別的服務匯流排命名空間將不再接受來自任何位置的流量，但已授權的虛擬網路 (s) 和（選擇性）特定的網際網路 IP 位址。 從虛擬網路的觀點而言，將服務匯流排命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離網路通道。

結果是繫結至子網路的工作負載與個別服務匯流排命名空間之間的私人和隔離關係，儘管傳訊服務端點的可觀察網路位址是在公用 IP 範圍中。

>[!WARNING]
> 實作虛擬網路整合可以防止其他 Azure 服務與服務匯流排互動。 例外狀況是，即使在啟用網路服務端點時，您也可以允許從某些受信任的服務存取服務匯流排資源。 如需信任的服務清單，請參閱 [信任的服務](#trusted-microsoft-services)。
>
> 虛擬網路上必須有下列 Microsoft 服務
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> 唯有[進階層](service-bus-premium-messaging.md)服務匯流排命名空間支援虛擬網路。 使用 VNet 服務端點搭配服務匯流排時，您不應該在混合標準和進階層服務匯流排命名空間的應用程式中啟用這些端點。 因為標準層不支援 Vnet。 端點僅限進階層命名空間。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要緊密和劃分安全性，而且虛擬網路子網路分割各劃分服務的解決方案，通常仍然需要位於這些區間之各項服務之間的通訊路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 傳訊服務提供完全隔離的通訊路徑，在其中，當訊息在對象之間轉換時，甚至會寫入到磁碟。 兩個都繫結到相同服務匯流排執行個體的不同虛擬網路，其中的工作負載可以透過訊息有效且可靠地通訊，同時保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得了 Azure 領先業界可靠和可擴充非同步傳訊功能支援，且現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，本質上會比任何對等通訊模式 (包括 HTTPS 和其他受 TLS 保護的通訊端通訊協定) 所能達成的更為安全。

## <a name="binding-service-bus-to-virtual-networks"></a>將服務匯流排繫結至虛擬網路

「虛擬網路規則」是防火牆安全性功能，可控制 Azure 服務匯流排伺服器是否接受來自特定虛擬網路子網路的連線。

將服務匯流排命名空間繫結至虛擬網路是一個雙步驟的程序。 您必須先在虛擬網路子網上建立 **虛擬網路服務端點** ，並針對 **Microsoft** 進行啟用，如 [服務端點總覽][vnet-sep]中所述。 一旦您新增服務端點，您就可使用「虛擬網路規則」將服務匯流排命名空間與它繫結。

虛擬網路規則是服務匯流排命名空間與虛擬網路子網路的關聯。 當此規則存在時，繫結至子網路的所有工作負載都會獲得授與服務匯流排命名空間的存取權。 服務匯流排本身永遠不會建立輸出連線，不需要獲得存取權，因此永遠不會因為啟用這項規則而獲得授與子網路的存取權。

> [!NOTE]
> 請記住，網路服務端點會提供在虛擬網路中執行的應用程式存取服務匯流排命名空間。 虛擬網路可控制端點的連線能力，但無法)  (佇列、主題或訂用帳戶的服務匯流排實體上執行哪些作業。 使用 Azure Active Directory (Azure AD) 來授權應用程式可在命名空間及其實體上執行的作業。 如需詳細資訊，請參閱 [使用 Azure AD 來驗證和授權應用程式，以存取服務匯流排實體](authenticate-application.md)。


## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節說明如何使用 Azure 入口網站來新增虛擬網路服務端點。 若要限制存取，您需要整合此事件中樞命名空間的虛擬網路服務端點。

1. 導覽至 [Azure 入口網站](https://portal.azure.com)中的 **服務匯流排命名空間** 。
2. 在左側功能表中，選取 [ **設定** ] 下的 [ **網路** 功能] 選項。  

    > [!NOTE]
    > 您只會看到 **premium** 命名空間的 [ **網路** ] 索引標籤。  
    
    預設會選取 [ **選取的網路** ] 選項。 如果您未在此頁面上新增至少一個 IP 防火牆規則或虛擬網路，可以使用存取金鑰) ，透過公用網際網路 (存取命名空間。

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="網路頁面-預設值" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    如果您選取 [ **所有網路** ] 選項，則您的服務匯流排命名空間會接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。 

    ![防火牆 - 已選取 [所有網路] 選項](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. 若要限制特定虛擬網路的存取權，請選取 [ **選取的網路** ] 選項（如果尚未選取的話）。
1. 在頁面的 [ **虛擬網路** ] 區段中，選取 [ **+ 新增現有的虛擬網路** ]。 

    ![新增現有的虛擬網路](./media/service-endpoints/add-vnet-menu.png)
3. 從虛擬網路清單中選取虛擬網路，然後挑選 **子網** 。 您必須先啟用服務端點，才能將虛擬網路新增至清單。 如果未啟用服務端點，入口網站會提示您啟用它。
   
   ![選取子網](./media/service-endpoints/select-subnet.png)

4. 針對已啟用的子網的服務端點，您應該會看到下列成功訊息 **。** 選取頁面底部的 [ **新增** ] 以新增網路。 

    ![選取子網路並啟用端點](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 如果您無法啟用服務端點，您可以使用 Resource Manager 範本來忽略遺失的虛擬網路服務端點。 在入口網站上無法使用這項功能。
6. 選取工具列上的 [儲存] 來儲存設定。 等候幾分鐘讓確認顯示在入口網站通知中。 [ **儲存** ] 按鈕應停用。 

    ![儲存網路](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > 如需允許從特定 IP 位址或範圍存取的指示，請參閱 [允許從特定 ip 位址或範圍進行](service-bus-ip-filtering.md)存取。

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
下列 Resource Manager 範本可讓您將虛擬網路規則新增至現有的服務匯流排命名空間。

範本參數：

* **namespaceName** ：服務匯流排命名空間。
* **virtualNetworkingSubnetId** ：虛擬網路子網路的完整 Resource Manager 路徑，例如，`/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` 適用於虛擬網路的預設子網路。

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

若要部署範本，請依照適用於 [Azure Resource Manager][lnk-deploy] 的指示執行。

## <a name="next-steps"></a>後續步驟

如需虛擬網路的詳細資訊，請參閱下列連結：

- [Azure 虛擬網路服務端點][vnet-sep]
- [Azure 服務匯流排 IP 篩選][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
