---
title: Azure 應用服務訪問限制
description: 瞭解如何通過指定訪問限制來保護應用在 Azure 應用服務中的安全。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a02c099871ce9748f4c5f604900a7c4d57bb96b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473518"
---
# <a name="azure-app-service-access-restrictions"></a>Azure 應用服務訪問限制 #

訪問限制使您能夠定義控制網路對應用的存取權限的優先順序排序的允許/拒絕清單。 該清單可以包括 IP 位址或 Azure 虛擬網路子網。 當存在一個或多個條目時，清單末尾存在一個隱式"拒絕所有"。

訪問限制功能適用于所有應用服務託管工作負載，包括：Web 應用、API 應用、Linux 應用程式、Linux 容器應用和功能。

向應用發出請求時，會根據訪問限制清單中的 IP 位址規則評估 FROM 位址。 如果 FROM 位址位於配置了 Microsoft.Web 的服務終結點的子網中，則將源子網與訪問限制清單中的虛擬網路規則進行比較。 如果根據清單中的規則，不允許該位址進行存取，則服務會回覆 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 狀態碼。

訪問限制功能在應用服務前端角色中實現，這些角色位於代碼運行所在的工作主機的上游。 因此，訪問限制實際上是網路 ACL。

限制從 Azure 虛擬網路 （VNet） 訪問 Web 應用的能力稱為[服務終結點][serviceendpoints]。 服務終結點使您能夠限制從選定的子網訪問多租戶服務。 必須在網路端和正在啟用該服務上啟用它。 將流量限制為在應用服務環境中託管的應用不起作用。 如果您處於應用服務環境，則可以使用 IP 位址規則控制對應用的訪問。

![訪問限制流](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>在門戶中添加和編輯訪問限制規則 ##

要向應用添加訪問限制規則，請使用功能表打開**網路**>**訪問限制**，然後按一下"**配置訪問限制"**

![App Service 網路功能選項](media/app-service-ip-restrictions/access-restrictions.png)  

在"訪問限制"UI 中，您可以查看為應用定義的訪問限制規則清單。

![清單訪問限制](media/app-service-ip-restrictions/access-restrictions-browse.png)

該清單將顯示應用上的所有當前限制。 如果應用上有 VNet 限制，該表將顯示是否為 Microsoft.Web 啟用了服務終結點。 當應用上沒有定義的限制時，你的應用將從任何位置訪問。  

## <a name="adding-ip-address-rules"></a>添加 IP 位址規則

您可以按一下 **[ 添加規則]** 添加新的訪問限制規則。 當您新增規則之後，該規則會立即生效。 規則會依據優先順序強制執行，從最小數字開始往上增加。 當您新增規則時 (即使只新增一個規則)，隱含的「拒絕所有」語句就會發生作用。

創建規則時，必須選擇允許/拒絕以及規則類型。 您還需要提供優先順序值以及限制訪問的內容。  您可以選擇向規則添加名稱和說明。  

![添加 IP 訪問限制規則](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

要設置基於 IP 位址的規則，請選擇 IPv4 或 IPv6 的類型。 針對 IPv4 和 IPv6 位址，CIDR 標記法中必須指定 IP 位址標記法。 若要指定正確的位址，您可以使用類似 1.2.3.4/32 的位址，其中前四個八位元代表您的 IP 位址，而 /32 是遮罩。 適用於所有位址的 IPv4 CIDR 標記法是 0.0.0.0/0。 若要深入了解 CIDR 標記法，您可以參閱[無類別網域間路由](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 

## <a name="service-endpoints"></a>服務端點

服務終結點使您能夠限制對所選 Azure 虛擬網路子網的訪問。 要限制對特定子網的訪問，請使用虛擬網路類型創建限制規則。 您可以選擇要允許或拒絕訪問的訂閱、VNet 和子網。 如果 Microsoft.Web 尚未為您選擇的子網啟用服務終結點，則將自動為您啟用該服務終結點，除非您選中要求不這樣做的核取方塊。 想要在應用上啟用它而不是子網的情況與您是否具有在子網上啟用服務終結點的許可權有很大關系。 如果需要讓其他人在子網上啟用服務終結點，可以選中該框，並為您的應用佈建服務終結點，以便稍後在子網上啟用該終結點。 

![添加 VNet 訪問限制規則](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

服務終結點不能用於限制對在應用服務環境中運行的應用的訪問。 當應用處于應用服務環境時，您可以使用 IP 訪問規則控制對應用的訪問。 

使用服務終結點，您可以使用應用程式閘道或其他 WAF 設備配置應用。 您還可以使用安全後端配置多層應用程式。 有關一些可能性的更多詳細資訊，請閱讀[網路功能以及應用服務和](networking-features.md)[應用程式閘道與服務終結點的集成](networking/app-gateway-with-service-endpoints.md)。

## <a name="managing-access-restriction-rules"></a>管理訪問限制規則

您可以按一下任何行來編輯現有訪問限制規則。 編輯內容與優先順序的變更都會立即生效。

![編輯訪問限制規則](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

編輯規則時，不能更改 IP 位址規則和虛擬網路規則之間的類型。 

![編輯訪問限制規則](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

要刪除規則，請按一下規則上的 **...** **Remove**

![刪除訪問限制規則](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>阻止單個 IP 位址 ##

添加第一個 IP 限制規則時，該服務將添加優先順序為 2147483647 的顯式 **"拒絕所有**規則"。 實際上，顯式**拒絕所有**規則將是最後一個執行的規則，並將阻止訪問使用**Allow**規則顯式允許的任何 IP 位址。

對於使用者希望顯式阻止單個 IP 位址或 IP 位址塊，但允許其他所有訪問的情況，有必要添加顯式 **"全部允許"** 規則。

![塊單個 ip 位址](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM 網站 

除了能夠控制對應用的訪問外，您還可以限制對應用使用的 scm 網站的訪問。 scm 網站是 Web 部署終結點，也是庫杜主控台。 可以從應用單獨分配 scm 網站的訪問限制，或者對應用和 scm 網站使用相同的集。 當您選中該框以具有與應用相同的限制時，一切都將空白。如果取消選中該框，則應用之前在 scm 網站上的任何設置。 

![清單訪問限制](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>訪問限制規則的程式設計操作 ##

[Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest)和[Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0)支援編輯訪問限制。 使用 Azure CLI 添加訪問限制的示例：

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
使用 Azure PowerShell 添加訪問限制的示例：

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

還可以在資源管理器中的應用配置或使用 Azure 資源管理器範本上使用[Azure REST API](https://docs.microsoft.com/rest/api/azure/) PUT 操作手動設置值。 例如，您可以使用 resources.azure.com，並編輯 ipSecurityRestrictions 區塊來新增必要的 JSON。

您可以在資源管理員中的以下位置找到此資訊：

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

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

## <a name="azure-function-app-access-restrictions"></a>Azure 功能應用訪問限制

具有與應用服務方案相同的功能的功能的函數應用也提供訪問限制。 啟用訪問限制將禁用任何不允許的 IP 的門戶代碼編輯器。

## <a name="next-steps"></a>後續步驟
[Azure 功能應用的訪問限制](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[應用程式閘道與服務終結點集成](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
