---
title: Azure App Service 存取限制
description: 瞭解如何藉由設定存取限制，在 Azure App Service 中保護您的應用程式。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1bcd0ea253db01d626ef1887ef0983d30ca794b9
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209939"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>設定 Azure App Service 存取限制

藉由設定存取限制，您可以定義優先順序排序的允許/拒絕清單，以控制對您應用程式的網路存取。 此清單可以包含 IP 位址或 Azure 虛擬網路子網。 當有一或多個專案時，清單結尾會有隱含的 *拒絕* 。

存取限制功能適用于所有 Azure App Service 主控的工作負載。 工作負載可以包括 web 應用程式、API 應用程式、Linux 應用程式、Linux 容器應用程式和功能。

對您的應用程式提出要求時，會根據存取限制清單中的規則來評估 [寄件者] 位址。 如果 [寄件者] 位址位於設定為 [服務端點] 的子網中，則會將來源子網與存取限制清單中的虛擬網路規則進行比較。 如果無法根據清單中的規則來存取位址，服務會回復 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 狀態碼。

存取限制功能是在 App Service 前端角色中執行，而這些角色是您的程式碼執行所在的背景工作主機上游。 因此，存取限制實際上是 (Acl) 的網路存取控制清單。

[服務端點][serviceendpoints]可讓您限制從 Azure 虛擬網路存取 web 應用程式的能力。 透過服務端點，您可以限制從選取的子網存取多租使用者服務。 將流量限制在 App Service 環境所裝載的應用程式時，並不會有任何作用。 如果您是在 App Service 環境中，您可以套用 IP 位址規則來控制應用程式的存取權。

> [!NOTE]
> 您必須同時在網路端和其所啟用的 Azure 服務上啟用服務端點。 如需支援服務端點的 Azure 服務清單，請參閱 [虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="存取限制流程的圖表。":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>在入口網站中管理存取限制規則

若要將存取限制規則新增至應用程式，請執行下列動作：

1. 登入 Azure 入口網站。

1. 在左窗格中，選取 [ **網路**]。

1. 在 [ **網路** ] 窗格的 [ **存取限制**] 底下，選取 [ **設定存取限制**]。

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Azure 入口網站中 [網路功能選項] 窗格 App Service 的螢幕擷取畫面。":::

1. 在 [ **存取限制** ] 頁面上，檢查針對您的應用程式定義的存取限制規則清單。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Azure 入口網站中 [存取限制] 頁面的螢幕擷取畫面，其中顯示針對所選應用程式定義的存取限制規則清單。":::

   此清單會顯示套用至應用程式的所有目前限制。 如果您的應用程式上有虛擬網路限制，則表格會顯示是否已啟用 Microsoft 的服務端點。 如果您的應用程式未定義任何限制，則可從任何地方存取應用程式。

### <a name="add-an-access-restriction-rule"></a>新增存取限制規則

若要將存取限制規則新增至應用程式，請在 [ **存取限制** ] 窗格中選取 [ **新增規則**]。 當您新增規則之後，它會立即生效。 

規則會依照優先順序來強制執行，從 **優先順序** 資料行中最小的數位開始。 當您新增甚至是單一規則之後，隱含的 *deny 全都* 會生效。

在 [ **新增存取限制** ] 窗格中，當您建立規則時，請執行下列動作：

1. 在 [ **動作**] 底下，選取 [ **允許** ] 或 [ **拒絕**]。  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="[新增存取限制] 窗格的螢幕擷取畫面。":::

1. （選擇性）輸入規則的名稱和描述。
1. 在 [ **優先順序** ] 方塊中，輸入優先權值。
1. 在 [ **類型** ] 下拉式清單中，選取規則的類型。

下列各節將說明不同類型的規則。

> [!NOTE]
> - 有512存取限制規則的限制。 如果您需要512個以上的存取限制規則，建議您考慮安裝獨立的安全性產品，例如 Azure Front Door、Azure App 閘道或替代 WAF。
>
#### <a name="set-an-ip-address-based-rule"></a>設定以 IP 位址為基礎的規則

依照上一節所述的程式執行，但使用下列新增功能：
* 在步驟4的 [ **類型** ] 下拉式清單中，選取 [ **IPv4** ] 或 [ **IPv6**]。 

針對 IPv4 和 IPv6 位址，指定無類別 Inter-Domain 路由 (CIDR) 標記法中的 **IP 位址區塊** 。 若要指定位址，您可以使用 *1.2.3.4/32* 之類的內容，其中前四個八位代表您的 IP 位址，而 */32* 是遮罩。 適用於所有位址的 IPv4 CIDR 標記法是 0.0.0.0/0。 若要深入瞭解 CIDR 標記法，請參閱無 [類別 Inter-Domain 路由](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 

#### <a name="set-a-service-endpoint-based-rule"></a>設定以服務端點為基礎的規則

* 在步驟4的 [ **類型** ] 下拉式清單中，選取 [ **虛擬網路**]。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="[新增限制] 窗格的螢幕擷取畫面，其中已選取虛擬網路類型。":::

指定 **訂** 用帳戶、 **虛擬網路** 和 **子網** 下拉式清單，以符合您想要限制存取的內容。

藉由使用服務端點，您可以限制對選取的 Azure 虛擬網路子網的存取。 如果您選取的子網尚未啟用 Microsoft 的服務端點，除非您選取 [ **忽略遺失的 Microsoft web 服務端點** ] 核取方塊，否則系統將會自動啟用它們。 您可能想要在應用程式上啟用服務端點而非子網的案例，主要取決於您是否擁有在子網上啟用它們的許可權。 

如果您需要其他人在子網上啟用服務端點，請選取 [ **忽略遺失的 Microsoft Web 服務端點** ] 核取方塊。 您的應用程式將會針對服務端點進行設定，以便在稍後於子網上啟用。 

您無法使用服務端點來限制存取在 App Service 環境中執行的應用程式。 當您的應用程式在 App Service 環境時，您可以藉由套用 IP 存取規則來控制其存取權。 

透過服務端點，您可以使用應用程式閘道或其他 web 應用程式防火牆 (WAF) 裝置來設定您的應用程式。 您也可以使用安全的後端來設定多層式應用程式。 如需詳細資訊，請參閱 [網路功能和 App Service](networking-features.md) ，以及 [應用程式閘道與服務端點的整合](networking/app-gateway-with-service-endpoints.md)。

> [!NOTE]
> - 使用 IP 安全通訊端層 (SSL) 虛擬 IP (VIP) 的 web 應用程式目前不支援服務端點。
>
#### <a name="set-a-service-tag-based-rule-preview"></a> (預覽版設定以服務標記為基礎的規則) 

* 在步驟4的 [ **類型** ] 下拉式清單中，選取 [ **服務標籤 (預覽])**。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png" alt-text="[新增限制] 窗格的螢幕擷取畫面，其中已選取服務標記類型。":::

每個服務標籤代表來自 Azure 服務的 IP 範圍清單。 您可以在 [服務標記檔][servicetags]中找到這些服務的清單和特定範圍的連結。

預覽階段期間，存取限制規則支援下列服務標記清單：
* ActionGroup
* AzureCloud
* AzureCognitiveSearch
* AzureConnectors
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureSignalR
* AzureTrafficManager
* LogicApps
* ServiceFabric

### <a name="edit-a-rule"></a>編輯規則

1. 若要開始編輯現有的存取限制規則，請在 [ **存取限制** ] 頁面上，選取您要編輯的規則。

1. 在 [ **編輯存取限制** ] 窗格上，進行您的變更，然後選取 [ **更新規則**]。 編輯會立即生效，包括優先順序順序的變更。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Azure 入口網站中 [編輯存取限制] 窗格的螢幕擷取畫面，其中顯示現有存取限制規則的欄位。":::

   > [!NOTE]
   > 當您編輯規則時，無法在規則類型之間切換。 

### <a name="delete-a-rule"></a>刪除規則

若要刪除規則，請在 [ **存取限制** ] 頁面上，選取您要刪除的規則旁邊的省略號 (**...**) ，然後選取 [ **移除**]。

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="[存取限制] 頁面的螢幕擷取畫面，顯示要刪除之存取限制規則旁邊的 [移除] 省略號。":::

## <a name="access-restriction-advanced-scenarios"></a>存取限制的 advanced 案例
下列各節說明一些使用存取限制的先進案例。
### <a name="block-a-single-ip-address"></a>封鎖單一 IP 位址

當您新增第一個存取限制規則時，服務會新增具有優先順序2147483647的明確 *拒絕所有* 規則。 在實務上，明確 *拒絕全部* 規則是最後要執行的規則，而且會封鎖對任何未由 *允許* 規則明確允許的 IP 位址的存取。

如果您想要明確封鎖單一 IP 位址或 IP 位址區塊，但允許存取任何其他專案，請新增明確的 [ *允許所有* 規則]。

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Azure 入口網站中 [存取限制] 頁面的螢幕擷取畫面，其中顯示單一封鎖的 IP 位址。":::

### <a name="restrict-access-to-an-scm-site"></a>限制對 SCM 網站的存取 

除了能夠控制對您應用程式的存取之外，您還可以限制對您應用程式所使用的 SCM 網站的存取。 SCM 網站是 web deploy 端點和 Kudu 主控台。 您可以從應用程式個別將存取限制指派給 SCM 網站，或對應用程式和 SCM 網站使用相同的限制集。 當您選取相同的 [**限制 \<app name> 為**] 核取方塊時，會遮蔽所有專案。如果您清除此核取方塊，則會重新套用您的 SCM 網站設定。 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Azure 入口網站中 [存取限制] 頁面的螢幕擷取畫面，顯示未針對 SCM 網站或應用程式設定任何存取限制。":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance-preview"></a>限制特定 Azure Front Door 實例的存取權 (預覽) 
從 Azure Front Door 到您應用程式的流量源自于 AzureFrontDoor 後端服務標籤中定義的一組知名的 IP 範圍。 您可以使用服務標記限制規則，將流量限制為僅來自 Azure Front Door。 為了確保流量只來自于您的特定實例，您將需要根據 Azure Front Door 傳送的唯一 HTTP 標頭，進一步篩選傳入要求。 在預覽期間，您可以使用 PowerShell 或 REST/ARM 來達成此目標。 

* 您可以在 Azure 入口網站) 中找到 PowerShell 範例 (Front Door 識別碼：

   ```azurepowershell-interactive
    $frontdoorId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
      -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
      -HttpHeader @{'x-azure-fdid' = $frontdoorId}
    ```
## <a name="manage-access-restriction-rules-programmatically"></a>以程式設計方式管理存取限制規則

您可以藉由執行下列其中一項動作，以程式設計方式新增存取限制： 

* 使用 [Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true)。 例如：
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* 使用 [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-5.2.0&preserve-view=true)。 例如：


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > 使用服務標記、HTTP 標頭或多來源規則都需要至少5.1.0 版。 您可以使用下列內容來確認已安裝模組的版本： **get-installedmodule uninstall-module-Name Az**

您也可以執行下列其中一項動作，手動設定值：

* 在 Azure Resource Manager 的應用程式設定上使用 [Azure REST API](/rest/api/azure/) PUT 作業。 這項資訊在 Azure Resource Manager 的位置為：

  management.azure.com/subscriptions/**訂** 用帳戶識別碼/resourceGroups/**資源群組**/providers/Microsoft.Web/sites/**Web 應用程式名稱**/config/web？ api 版本 = 2020-06-01

* 使用 Resource Manager 範本。 例如，您可以使用 resources.azure.com，並編輯 ipSecurityRestrictions 區塊來新增必要的 JSON。

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
  使用服務標記和 HTTP 標頭限制之 advanced 範例的 JSON 語法為：
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
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
[servicetags]: ../virtual-network/service-tags-overview.md