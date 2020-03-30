---
title: 使用 Az.搜索模組的 PowerShell 腳本
titleSuffix: Azure Cognitive Search
description: 使用 PowerShell 創建和配置 Azure 認知搜索服務。 您可以向上或向下擴展服務、管理管理員和查詢 api 鍵以及查詢系統資訊。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209291"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>使用 PowerShell 管理 Azure 認知搜索服務
> [!div class="op_single_selector"]
> * [入口網站](search-manage.md)
> * [電源外殼](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

您可以在 Windows、Linux 或[Azure 雲外殼](https://docs.microsoft.com/azure/cloud-shell/overview)中運行 PowerShell Cmdlet 和腳本，以創建和配置 Azure 認知搜索。 **Az.Search**模組將[Azure PowerShell](https://docs.microsoft.com/powershell/)擴展為[搜索管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement)的完整同位，並能夠執行以下任務：

> [!div class="checklist"]
> * [在訂閱中列出搜索服務](#list-search-services)
> * [退貨服務資訊](#get-search-service-information)
> * [創建或刪除服務](#create-or-delete-a-service)
> * [重新生成管理員 API 金鑰](#regenerate-admin-keys)
> * [創建或刪除查詢 api 鍵](#create-or-delete-query-keys)
> * [使用副本和分區向上或向下擴展](#scale-replicas-and-partitions)

偶爾會詢問有關*上述清單中未*出現的任務的問題。 目前，您不能使用**Az.Search**模組或管理 REST API 更改伺服器名稱、區域或層。 創建服務時分配專用資源。 因此，更改基礎硬體（位置或節點類型）需要新的服務。 同樣，沒有工具或 API 用於將內容（如索引）從一個服務傳輸到另一個服務。

在服務中，內容創建和管理是通過[搜索服務 REST API](https://docs.microsoft.com/rest/api/searchservice/)或[.NET SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search)。 雖然沒有專用的 PowerShell 命令用於內容，但您可以編寫調用 REST 或 .NET API 以創建和載入索引的 PowerShell 腳本。

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>檢查版本和載入模組

本文中的示例是互動式的，需要較高的權限。 必須安裝 Azure 電源外殼 **（Az**模組）。 有關詳細資訊，請參閱安裝[Azure 電源外殼](/powershell/azure/overview)。

### <a name="powershell-version-check-51-or-later"></a>電源外殼版本檢查（5.1 或更高版本）

本地 PowerShell 必須在任何受支援的作業系統上為 5.1 或更高版本。

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>載入 Azure 電源外殼

如果不確定是否安裝了**Az，** 請作為驗證步驟運行以下命令。 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

某些系統不自動載入模組。 如果上一個命令出錯，請嘗試載入模組，如果該模組失敗，請返回安裝說明以查看是否錯過了步驟。

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>使用瀏覽器登錄權杖連接到 Azure

您可以使用門戶登錄憑據連接到 PowerShell 中的訂閱。 或者，您可以使用[服務主體進行非交互身份驗證](../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

```azurepowershell-interactive
Connect-AzAccount
```

如果持有多個 Azure 訂閱，請設置 Azure 訂閱。 如果想查看目前的訂用帳戶清單，請執行這個命令。

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

若要指定訂用帳戶，請執行下列命令。 在下列範例中，訂用帳戶的名稱為 `ContosoSubscription`。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>列出訂閱中的服務

以下命令來自[**Az.Resources，**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)返回有關訂閱中已預配的現有資源和服務的資訊。 如果您不知道已創建多少個搜索服務，這些命令將返回該資訊，從而為您節省到門戶的行程。

第一個命令返回所有搜索服務。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

從服務清單中返回有關特定資源的資訊。

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

## <a name="import-azsearch"></a>導入 Az.搜索

在載入模組之前，[**來自 Az.Search 的命令**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search)不可用。

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>列出所有 Az.搜索命令

作為驗證步驟，返回模組中提供的命令清單。

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

## <a name="get-search-service-information"></a>獲取搜索服務資訊

導入**Az.Search**並知道包含搜索服務的資源組後，運行[Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0)以返回服務定義，包括名稱、區域、層以及副本和分區計數。

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

## <a name="create-or-delete-a-service"></a>創建或刪除服務

[**新搜索服務**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0)用於[創建新的搜索服務](search-create-service-portal.md)。

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

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0)用於滾動管理[API 金鑰](search-security-api-keys.md)。 為每個服務創建兩個管理金鑰，以便進行身份驗證訪問。 每個請求都需要金鑰。 兩個管理金鑰在功能上是等效的，授予搜索服務的完全寫入存取權限，並能夠檢索任何資訊，或創建和刪除任何物件。 存在兩個鍵，以便在替換另一個鍵時可以使用其中一個鍵。 

一次只能重新生成一個，指定為 或`primary``secondary`鍵。 對於不間斷的服務，請記住在滾動主鍵時更新所有用戶端代碼以使用輔助金鑰。 避免在操作操作時更改鑰匙。

正如您所料，如果不更新用戶端代碼而重新生成金鑰，使用舊金鑰的請求將失敗。 重新生成所有新金鑰不會永久將您鎖定在服務外，您仍可以通過門戶訪問該服務。 重新生成主鍵和輔助金鑰後，可以更新用戶端代碼以使用新金鑰，操作將相應地恢復。

API 金鑰的值由服務生成。 不能提供要使用 Azure 認知搜索的自訂金鑰。 同樣，管理 API 金鑰沒有使用者定義的名稱。 對鍵的引用是固定字串，或`primary` `secondary`。 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

結果看起來應該會類似以下的輸出。 即使一次只更改一個鍵，也會返回這兩個鍵。

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>創建或刪除查詢鍵

[**New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0)用於創建查詢[API 金鑰](search-security-api-keys.md)，用於從用戶端應用到 Azure 認知搜索索引的唯讀訪問。 查詢鍵用於對特定索引進行身份驗證，以便檢索搜尋結果。 查詢鍵不授予對服務上的其他項（如索引、資料來源或索引子）的唯讀存取權限。

不能提供要使用的 Azure 認知搜索的金鑰。 API 金鑰由服務生成。

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>縮放副本和分區

[**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0)用於[增加或減少副本和分區](search-capacity-planning.md)，以重新調整服務中的計費資源。 增加副本或分區會增加帳單，其中同時收取固定費用和可變費用。 如果暫時需要額外的處理能力，則可以增加副本和分區來處理工作負載。 "概述"門戶頁的監視區域具有查詢延遲、每秒查詢和限制的磁貼，指示當前容量是否足夠。

添加或刪除資源可能需要一段時間。 容量調整在後臺進行，允許繼續現有工作負載。 一旦準備好，就會為傳入請求使用額外的容量，無需額外的配置。 

刪除容量可能會造成破壞性。 建議在減少容量之前停止所有索引和索引子作業，以避免丟棄的請求。 如果這不可行，您可以考慮逐步減少容量，一次減少一個副本和分區，直到達到新的目標級別。

提交命令後，無法中途終止該命令。 在修改計數之前，必須等待命令完成。

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

生成[索引](search-what-is-an-index.md)，使用門戶、REST API 或 .NET SDK[查詢索引](search-query-overview.md)。

* [在 Azure 入口網站中建立 Azure 認知搜尋索引](search-create-index-portal.md)
* [設置索引子以從其他服務載入資料](search-indexer-overview.md)
* [使用 Azure 門戶中的搜索資源管理器查詢 Azure 認知搜索索引](search-explorer.md)
* [如何在 .NET 中使用 Azure 認知搜索](search-howto-dotnet-sdk.md)