---
title: Azure App Service 存取限制
description: 瞭解如何藉由設定存取限制，在 Azure App Service 中保護您的應用程式。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576439"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>設定 Azure App Service 存取限制

藉由設定存取限制，您可以定義優先順序排序的允許/拒絕清單，以控制對您應用程式的網路存取。 此清單可以包含 IP 位址或 Azure 虛擬網路子網。 當有一或多個專案時，清單結尾會有隱含的 *拒絕* 。

存取限制功能適用于所有 Azure App Service 主控的工作負載。 工作負載可以包括 web 應用程式、API 應用程式、Linux 應用程式、Linux 容器應用程式和功能。

對您的應用程式提出要求時，會根據您的存取限制清單中的 IP 位址規則來評估 [寄件者] 位址。 如果 [寄件者] 位址位於設定為 [服務端點] 的子網中，則會將來源子網與存取限制清單中的虛擬網路規則進行比較。 如果無法根據清單中的規則來存取位址，服務會回復 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 狀態碼。

存取限制功能是在 App Service 前端角色中執行，而這些角色是您的程式碼執行所在的背景工作主機上游。 因此，存取限制實際上是 (Acl) 的網路存取控制清單。

[服務端點][serviceendpoints]可讓您限制從 Azure 虛擬網路存取 web 應用程式的能力。 透過服務端點，您可以限制從選取的子網存取多租使用者服務。 將流量限制在 App Service 環境所裝載的應用程式時，並不會有任何作用。 如果您是在 App Service 環境中，您可以套用 IP 位址規則來控制應用程式的存取權。

> [!NOTE]
> 您必須同時在網路端和其所啟用的 Azure 服務上啟用服務端點。 如需支援服務端點的 Azure 服務清單，請參閱 [虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。
>

![存取限制流程的圖表。](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>在入口網站中新增或編輯存取限制規則

若要將存取限制規則新增至您的應用程式，請執行下列動作：

1. 登入 Azure 入口網站。

1. 在左窗格中，選取 [ **網路** ]。

1. 在 [ **網路** ] 窗格的 [ **存取限制** ] 底下，選取 [ **設定存取限制** ]。

   ![Azure 入口網站中 [網路功能選項] 窗格 App Service 的螢幕擷取畫面。](media/app-service-ip-restrictions/access-restrictions.png)  

1. 在 [ **存取限制** ] 頁面上，檢查針對您的應用程式定義的存取限制規則清單。

   ![Azure 入口網站中 [存取限制] 頁面的螢幕擷取畫面，其中顯示針對所選應用程式定義的存取限制規則清單。](media/app-service-ip-restrictions/access-restrictions-browse.png)

   此清單會顯示套用至應用程式的所有目前限制。 如果您的應用程式上有虛擬網路限制，則表格會顯示是否已啟用 Microsoft 的服務端點。 如果您的應用程式未定義任何限制，則可從任何地方存取應用程式。  

### <a name="add-an-access-restriction-rule"></a>新增存取限制規則

若要將存取限制規則新增至應用程式，請在 [ **存取限制** ] 窗格中選取 [ **新增規則** ]。 當您新增規則之後，它會立即生效。 

規則會依照優先順序來強制執行，從 **優先順序** 資料行中最小的數位開始。 當您新增甚至是單一規則之後，隱含的 *deny 全都* 會生效。

在 [ **新增 IP 限制** ] 窗格中，當您建立規則時，請執行下列動作：

1. 在 [ **動作** ] 底下，選取 [ **允許** ] 或 [ **拒絕** ]。  

   ![[新增 IP 限制] 窗格的螢幕擷取畫面。](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. （選擇性）輸入規則的名稱和描述。  
1. 在 [ **類型** ] 下拉式清單中，選取規則的類型。  
1. 在 [ **優先順序** ] 方塊中，輸入優先權值。  
1. 在 [ **訂** 用帳戶]、[ **虛擬網路** ] 和 [ **子網** ] 下拉式清單中，選取您要限制存取的內容。  

### <a name="set-an-ip-address-based-rule"></a>設定以 IP 位址為基礎的規則

依照上一節所述的程式執行，但使用下列變化：
* 在步驟3的 [ **類型** ] 下拉式清單中，選取 [ **IPv4** ] 或 [ **IPv6** ]。 

針對 IPv4 和 IPv6 位址，指定無類別 Inter-Domain 路由 (CIDR) 標記法中的 IP 位址。 若要指定位址，您可以使用 *1.2.3.4/32* 之類的內容，其中前四個八位代表您的 IP 位址，而 */32* 是遮罩。 適用於所有位址的 IPv4 CIDR 標記法是 0.0.0.0/0。 若要深入瞭解 CIDR 標記法，請參閱無 [類別 Inter-Domain 路由](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 

## <a name="use-service-endpoints"></a>使用服務端點

藉由使用服務端點，您可以限制對選取的 Azure 虛擬網路子網的存取。 若要限制對特定子網的存取，請建立具有 **虛擬網路** 類型的限制規則。 然後，您可以選取您想要允許或拒絕存取的訂用帳戶、虛擬網路和子網。 

如果您選取的子網尚未啟用 Microsoft 的服務端點，除非您選取 [ **忽略遺失的 Microsoft web 服務端點** ] 核取方塊，否則系統將會自動啟用它們。 您可能想要在應用程式上啟用服務端點而非子網的案例，主要取決於您是否擁有在子網上啟用它們的許可權。 

如果您需要其他人在子網上啟用服務端點，請選取 [ **忽略遺失的 Microsoft Web 服務端點** ] 核取方塊。 您的應用程式將會針對服務端點進行設定，以便在稍後於子網上啟用。 

![[新增 IP 限制] 窗格的螢幕擷取畫面，其中已選取虛擬網路類型。](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

您無法使用服務端點來限制存取在 App Service 環境中執行的應用程式。 當您的應用程式在 App Service 環境時，您可以藉由套用 IP 存取規則來控制其存取權。 

透過服務端點，您可以使用應用程式閘道或其他 web 應用程式防火牆 (WAF) 裝置來設定您的應用程式。 您也可以使用安全的後端來設定多層式應用程式。 如需詳細資訊，請參閱 [網路功能和 App Service](networking-features.md) ，以及 [應用程式閘道與服務端點的整合](networking/app-gateway-with-service-endpoints.md)。

> [!NOTE]
> - 使用 IP 安全通訊端層 (SSL) 虛擬 IP (VIP) 的 web 應用程式目前不支援服務端點。
> - IP 或服務端點限制有512個數據列的限制。 如果您需要超過512個數據列的限制，建議您考慮安裝獨立的安全性產品，例如 Azure Front Door、Azure App 閘道或 WAF。
>

## <a name="manage-access-restriction-rules"></a>管理存取限制規則

您可以編輯或刪除現有的存取限制規則。

### <a name="edit-a-rule"></a>編輯規則

1. 若要開始編輯現有的存取限制規則，請在 [ **存取限制** ] 頁面上，按兩下您要編輯的規則。

1. 在 [ **編輯 IP 限制** ] 窗格上，進行您的變更，然後選取 [ **更新規則** ]。 編輯會立即生效，包括優先順序順序的變更。

   ![螢幕擷取畫面： Azure 入口網站中的 [編輯 IP 限制] 窗格，顯示現有存取限制規則的欄位。](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > 當您編輯規則時，無法在 IP 位址規則和虛擬網路規則之間切換。 

   ![螢幕擷取畫面： Azure 入口網站中的 [編輯 IP 限制] 窗格，顯示虛擬網路規則的設定。](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>刪除規則

若要刪除規則，請在 [ **存取限制** ] 頁面上，選取您要刪除的規則旁邊的省略號 ( **...** ) ，然後選取 [ **移除** ]。

![[存取限制] 頁面的螢幕擷取畫面，顯示要刪除的存取限制規則旁邊的 [移除] 省略號。](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>封鎖單一 IP 位址

當您新增第一個 IP 限制規則時，服務會新增具有優先順序2147483647的明確 *拒絕所有* 規則。 在實務上，明確 *拒絕全部* 規則是最後要執行的規則，而且會封鎖對任何未由 *允許* 規則明確允許的 IP 位址的存取。

如果您想要明確封鎖單一 IP 位址或 IP 位址區塊，但允許存取任何其他專案，請新增明確的 [ *允許所有* 規則]。

![Azure 入口網站中 [存取限制] 頁面的螢幕擷取畫面，其中顯示單一封鎖的 IP 位址。](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>限制對 SCM 網站的存取 

除了能夠控制對您應用程式的存取之外，您還可以限制對您應用程式所使用的 SCM 網站的存取。 SCM 網站是 web deploy 端點和 Kudu 主控台。 您可以從應用程式個別將存取限制指派給 SCM 網站，或對應用程式和 SCM 網站使用相同的限制集。 當您選取相同的 [ **限制 \<app name> 為** ] 核取方塊時，會遮蔽所有專案。如果您清除此核取方塊，則會重新套用您的 SCM 網站設定。 

![Azure 入口網站中 [存取限制] 頁面的螢幕擷取畫面，顯示未針對 SCM 網站或應用程式設定任何存取限制。](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>以程式設計方式管理存取限制規則

您可以藉由執行下列其中一項動作，以程式設計方式新增存取限制： 

* 使用 [Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true)。 例如：
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* 使用 [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true)。 例如：


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

您也可以執行下列其中一項動作，手動設定值：

* 在 Azure Resource Manager 的應用程式設定上使用 [Azure REST API](/rest/api/azure/) PUT 作業。 這項資訊在 Azure Resource Manager 的位置為：

  management.azure.com/subscriptions/ **subscription ID** /resourceGroups/ **resource groups** /providers/Microsoft.Web/sites/ **web app name** /config/web?api-version=2018-02-01

* 使用 ARM 範本。 例如，您可以使用 resources.azure.com，並編輯 ipSecurityRestrictions 區塊來新增必要的 JSON。

  先前的 JSON 語法範例為：

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```

## <a name="set-up-azure-functions-access-restrictions"></a>設定 Azure Functions 存取限制

存取限制也適用于具有 App Service 方案相同功能的函式應用程式。 當您啟用存取限制時，也會針對任何不允許的 Ip 停用 Azure 入口網站程式碼編輯器。

## <a name="next-steps"></a>後續步驟
[Azure Functions 的存取限制](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[應用程式閘道與服務端點的整合](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
