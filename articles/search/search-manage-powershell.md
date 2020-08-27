---
title: 使用 Az. Search 模組的 PowerShell 腳本
titleSuffix: Azure Cognitive Search
description: 使用 PowerShell 建立和設定 Azure 認知搜尋服務。 您可以向上或向下調整服務、管理系統管理員和查詢 api 金鑰，以及查詢系統資訊。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 9f189d1889f3ca3a3aa3234432452b1b3d696c04
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935087"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>使用 PowerShell 管理您的 Azure 認知搜尋服務
> [!div class="op_single_selector"]
> * [入口網站](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

您可以在 Windows、Linux 或 [Azure Cloud Shell](../cloud-shell/overview.md) 中執行 PowerShell Cmdlet 和腳本，以建立和設定 Azure 認知搜尋。 **Az. search**模組會將[Azure PowerShell](/powershell/)與[搜尋管理 REST api](/rest/api/searchmanagement)的完全同位延伸，以及執行下列工作的能力：

> [!div class="checklist"]
> * [列出訂用帳戶中的搜尋服務](#list-search-services)
> * [傳回服務資訊](#get-search-service-information)
> * [建立或刪除服務](#create-or-delete-a-service)
> * [重新產生管理員 API 金鑰](#regenerate-admin-keys)
> * [建立或刪除查詢 api 金鑰](#create-or-delete-query-keys)
> * [使用複本和資料分割擴大或縮小](#scale-replicas-and-partitions)

有時候，系統會詢問問題， *而不* 在上述清單中。 目前，您不能使用 **Az. Search** 模組或管理 REST API 來變更伺服器名稱、區域或層級。 建立服務時，會配置專用資源。 因此，變更基礎硬體 (位置或節點類型) 需要新的服務。 同樣地，沒有任何工具或 Api 可將內容（例如索引）從某個服務傳送至另一個服務。

在服務內，內容的建立和管理是透過 [搜尋服務 REST API](/rest/api/searchservice/) 或 [.net SDK](/dotnet/api/?term=microsoft.azure.search)。 雖然沒有適用于內容的專用 PowerShell 命令，您可以撰寫 PowerShell 腳本來呼叫 REST 或 .NET Api 來建立和載入索引。

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>檢查版本和載入模組

本文中的範例是互動式的，且需要較高的許可權。 Azure PowerShell (必須安裝 **Az** 模組) 。 如需詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/) (英文)。

### <a name="powershell-version-check-51-or-later"></a>PowerShell 版本檢查 (5.1 或更新版本) 

在任何支援的作業系統上，本機 PowerShell 必須是5.1 或更新版本。

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>載入 Azure PowerShell

如果您不確定是否已安裝 **Az** ，請執行下列命令作為驗證步驟。 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

某些系統不會自動載入模組。 如果您在上一個命令收到錯誤，請嘗試載入模組，如果失敗，請返回安裝指示以查看您是否錯過了某個步驟。

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>使用瀏覽器登入權杖連線到 Azure

您可以使用入口網站的登入認證來連線至 PowerShell 中的訂用帳戶。 或者，您也可以 [使用服務主體以非互動方式進行驗證](../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

```azurepowershell-interactive
Connect-AzAccount
```

如果您保留多個 Azure 訂用帳戶，請設定您的 Azure 訂用帳戶。 如果想查看目前的訂用帳戶清單，請執行這個命令。

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

若要指定訂用帳戶，請執行下列命令。 在下列範例中，訂用帳戶的名稱為 `ContosoSubscription`。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>列出訂用帳戶中的服務

下列命令來自 [**Az. Resources**](/powershell/module/az.resources/?view=azps-1.4.0#resources)，傳回您訂用帳戶中已布建的現有資源和服務的相關資訊。 如果您不知道已建立多少搜尋服務，這些命令會傳回該資訊，以節省您入口網站的行程。

第一個命令會傳回所有搜尋服務。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

從服務清單中，傳回特定資源的相關資訊。

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

結果看起來應該會類似以下的輸出。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>匯入 Az. Search

來自 Az 的命令。在您載入模組之前，無法使用[**搜尋。**](/powershell/module/az.search/?view=azps-1.4.0#search)

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>列出所有 Az. Search 命令

在驗證步驟中，傳回模組中提供的命令清單。

```azurepowershell-interactive
Get-Command -Module Az.Search
```

結果看起來應該會類似以下的輸出。

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>取得搜尋服務資訊

Az 之後，即會匯入 **搜尋** ，且您知道包含您搜尋服務的資源群組，請執行 [AzSearchService](/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) 以傳回服務定義，包括名稱、區域、層級和複本和分割區計數。

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

結果看起來應該會類似以下的輸出。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>建立或刪除服務

[**AzSearchService**](/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) 可用來 [建立新的搜尋服務](search-create-service-portal.md)。

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
結果看起來應該會類似以下的輸出。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>重新產生系統管理金鑰

[**AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) 可用來變換管理 [API 金鑰](search-security-api-keys.md)。 每個服務都會建立兩個系統管理金鑰以進行驗證存取。 每個要求都需要金鑰。 這兩個系統管理金鑰在功能上都相同，授與對搜尋服務的完整寫入存取權，讓您能夠取得任何資訊，或是建立和刪除任何物件。 有兩個索引鍵，可讓您在取代另一個索引鍵時使用。 

您一次只能重新產生一個，並指定為或索引 `primary` `secondary` 鍵。 針對不中斷的服務，請記得更新所有用戶端程式代碼，以在主要金鑰變換時使用次要金鑰。 避免在作業進行時變更金鑰。

如您所預期，如果您在未更新用戶端程式代碼的情況下重新產生金鑰，使用舊金鑰的要求將會失敗。 重新產生所有新的金鑰並不會永久鎖定您的服務，而且您仍然可以透過入口網站存取服務。 重新產生主要和次要金鑰之後，您可以更新用戶端程式代碼以使用新的金鑰，而作業將會據以繼續。

API 金鑰的值是由服務所產生。 您無法提供 Azure 認知搜尋要使用的自訂金鑰。 同樣地，系統管理員 API 金鑰沒有使用者定義的名稱。 索引鍵的參考是固定字串，也就是 `primary` 或 `secondary` 。 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

結果看起來應該會類似以下的輸出。 這兩個金鑰會傳回，即使您一次只能變更一個金鑰也一樣。

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>建立或刪除查詢金鑰

[**AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) 可用來建立查詢 [API 金鑰](search-security-api-keys.md) ，以從用戶端應用程式到 Azure 認知搜尋索引的唯讀存取。 查詢金鑰是用來驗證特定索引，以取得搜尋結果。 查詢金鑰不會將唯讀存取權授與服務上的其他專案，例如索引、資料來源或索引子。

您無法提供 Azure 認知搜尋要使用的金鑰。 API 金鑰是由服務所產生。

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>調整複本和磁碟分割

[**AzSearchService**](/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) 可用來 [增加或減少複本和](search-capacity-planning.md) 資料分割，以重新調整服務內的可計費資源。 增加複本或資料分割會增加您的帳單，而您的帳單會有固定和不定的費用。 如果您需要額外的處理能力，您可以增加複本和分割區來處理工作負載。 總覽入口網站頁面中的 [監視] 區域具有查詢延遲、每秒查詢數和節流的圖格，指出目前的容量是否足夠。

新增或移除資源可能需要一些時間。 調整容量會在背景進行，讓現有的工作負載繼續進行。 當連入要求就緒時，會立即使用額外的容量，不需要額外的設定。 

移除容量可能會造成干擾。 建議您在減少容量之前，先停止所有的索引和索引子工作，以避免卸載的要求。 如果這不可行，您可以考慮在達到新的目標層級之前，以累加方式（一次一個複本和分割區）來減少容量。

提交命令之後，就無法在中途終止。 您必須等到命令完成後，才會修改計數。

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

結果看起來應該會類似以下的輸出。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>後續步驟

使用入口網站、REST Api 或 .NET SDK 來建立 [索引](search-what-is-an-index.md)、 [查詢索引](search-query-overview.md) 。

* [在 Azure 入口網站中建立 Azure 認知搜尋索引](search-get-started-portal.md)
* [設定索引子以從其他服務載入資料](search-indexer-overview.md)
* [在 Azure 入口網站中使用 [搜尋] explorer 查詢 Azure 認知搜尋索引](search-explorer.md)
* [如何在 .NET 中使用 Azure 認知搜尋](search-howto-dotnet-sdk.md)