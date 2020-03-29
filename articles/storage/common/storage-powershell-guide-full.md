---
title: 將 Azure PowerShell 與 Azure 儲存體搭配使用 | Microsoft Docs
description: 了解如何使用 Azure 儲存體的 Azure PowerShell Cmdlet。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d2404ee58f5f44fbe5625f267e6d1c504d0bd237
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465103"
---
# <a name="using-azure-powershell-with-azure-storage"></a>搭配使用 Azure PowerShell 與 Azure 儲存體

Azure PowerShell 用來從 PowerShell 命令列或在指令碼中建立和管理 Azure 資源。 針對 Azure 儲存體，這些 Cmdlet 分為兩個類別：控制層和資料層。 控制項層 Cmdlet 是用來管理儲存體帳戶：建立儲存體帳戶、設定屬性、刪除儲存體帳戶、旋轉存取金鑰等等。 資料層 Cmdlet 是用來管理儲存體帳戶「中」** 所儲存的資料。 例如，上傳 Blob、建立檔案共用，以及將訊息新增至佇列。

本做法文章涵蓋使用管理層 Cmdlet 管理儲存體帳戶的一般作業。 您會了解如何：

> [!div class="checklist"]
> * 列出儲存體帳戶
> * 取得現有儲存體帳戶的參考
> * 建立儲存體帳戶
> * 設定儲存體帳戶屬性
> * 擷取和重新產生存取金鑰
> * 保護對儲存體帳戶的存取
> * 啟用儲存體分析

本文提供關於儲存體之數個其他 PowerShell 文章的連結，例如如何啟用及存取儲存體分析、如何使用資料層 Cmdlet，以及如何存取 Azure 獨立雲端 (例如中國雲端、德國雲端和政府雲端)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

這個練習需要 Azure PowerShell 模組 Az 0.7 版或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

在此練習中，您可以將命令鍵入一般 PowerShell 視窗中，或者使用 [Windows PowerShell 整合指令碼環境 (ISE)](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise)，並在編輯器中鍵入命令，然後在您瀏覽範例時一次測試一或多個命令。 您可以反白顯示您要執行的資料列，然後按一下 [執行選取項目] 只執行這些命令。

如需儲存體帳戶的詳細資訊，請參閱[儲存體簡介](storage-introduction.md)和[關於 Azure 儲存體帳戶](storage-create-storage-account.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>列出訂用帳戶中的儲存體帳戶

執行 [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) Cmdlet，來擷取目前訂用帳戶中的儲存體帳戶清單。

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>取得儲存體帳戶的參考

接下來，您需要儲存體帳戶的參考。 您可以建立新的儲存體帳戶，或取得現有儲存體帳戶的參考。 下列各節示範這兩種方法。

### <a name="use-an-existing-storage-account"></a>使用現有儲存體帳戶

若要擷取現有儲存體帳戶，您需要資源群組名稱和儲存體帳戶名稱。 設定那兩個欄位的變數，然後使用 [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) Cmdlet。

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

您現在具有指向現有儲存體帳戶的 $storageAccount。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

下列指令碼示範如何使用 [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) 建立一般用途的儲存體帳戶。 在您建立帳戶之後，請擷取其內容，而此內容可以用於後續命令，而不是指定具有每個呼叫的驗證。

```powershell
# Get list of locations and select one.
Get-AzLocation | select Location
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzResourceGroup -Name $resourceGroup -Location $location

# Set the name of the storage account and the SKU name.
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context.
$ctx = $storageAccount.Context
```

指令碼會使用下列 PowerShell Cmdlet：

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) -- 擷取有效位置的清單。 這個範例使用 `eastus` 作為位置。

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) -- 建立新的資源群組。 資源群組是在其中部署和管理 Azure 資源的邏輯容器。 稱為 `teststoragerg`。

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) -- 建立儲存體帳戶。 這個範例使用 `testpshstorage`。

SKU 名稱指出儲存體帳戶的複寫類型，例如 LRS (本地備援儲存體)。 有關複製的詳細資訊，請參閱[Azure 存儲複製](storage-redundancy.md)。

> [!IMPORTANT]
> 儲存體帳戶的名稱在 Azure 中必須是獨一無二的且必須小寫。 如需命名慣例和限制，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)。
>

您現在具有新的儲存體帳戶和其參考。

## <a name="manage-the-storage-account"></a>管理儲存體帳戶

您現在具有新儲存體帳戶或現有儲存體帳戶的參考，下節示範一些您可用來管理儲存體帳戶的命令。

### <a name="storage-account-properties"></a>儲存體帳戶屬性

若要變更儲存體帳戶的設定，請使用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)。 雖然您無法變更儲存體帳戶或其所在資源群組的位置，但是可以變更許多其他屬性。 以下列出一些您可使用 PowerShell 變更的屬性。

* 指派給儲存體帳戶的**自訂網域**。

* 指派給儲存體帳戶的**標記**。 標記通常用來分類資源以進行計費。

* **SKU** 是儲存體帳戶的複寫設定，例如 LRS 代表「本地備援儲存體」。 例如，您可能會從標準\_LRS 變更為標準\_GRS 或標準\_RAGRS。 請注意，您\_無法將標準 ZRS、標準\_GZRS、標準\_RAGZRS 或\_高級 LRS 更改為其他 SKU，也不能將其他 SKU 更改為這些 SKU。

* Blob 儲存體帳戶的**存取層**。 存取層的值設為**經常性**或**非經常性**，並可讓您選取符合您如何使用儲存體帳戶的存取層來減少成本。 如需詳細資訊，請參閱[經常性、非經常性和封存儲存層](../blobs/storage-blob-storage-tiers.md)。

* 只允許 HTTPS 流量。

### <a name="manage-the-access-keys"></a>管理存取金鑰

Azure 儲存體帳戶會隨附兩個帳戶金鑰。 若要擷取金鑰，請使用 [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey)。 這個範例會擷取第一個金鑰。 若要擷取另一個金鑰，請使用 `Value[1]`，而非 `Value[0]`。

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

若要重新產生金鑰，請使用 [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey)。

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

若要重新產生另一個金鑰，請使用 `key2` 作為金鑰名稱，而非 `key1`。

重新產生其中一個金鑰，然後再擷取一次，以查看新的值。

> [!NOTE]
> 您應該先謹慎規劃，再重新產生實際執行儲存體帳戶的金鑰。 重新產生一或兩個金鑰會讓任何使用已重新產生金鑰之應用程式的存取權失效。 如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](storage-account-keys-manage.md)。


### <a name="delete-a-storage-account"></a>刪除儲存體帳戶

若要刪除儲存體帳戶，請使用 [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount)。

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> 當您刪除儲存體帳戶時，也會一併刪除帳戶中儲存的所有資產。 如果您不小心刪除帳戶，請立即連絡支援人員，並開啟要還原儲存體帳戶的票證。 不保證復原資料，但有時可以運作。 除非已解決支援票證，否則請不要建立與舊儲存體帳戶同名的新儲存體帳戶。
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>使用 VNet 和防火牆保護儲存體帳戶

任何可存取網際網路的網路預設都可以存取所有儲存體帳戶。 不過，您可以設定網路規則，只允許來自特定虛擬網路的應用程式存取儲存體帳戶。 有關詳細資訊，請參閱配置[Azure 存儲防火牆和虛擬網路](storage-network-security.md)。

本文示範如何使用下列 PowerShell Cmdlet 來管理這些設定：
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule) \(英文\)

## <a name="use-storage-analytics"></a>使用儲存體分析

[Azure 儲存體分析](storage-analytics.md)包含[儲存體分析計量](/rest/api/storageservices/about-storage-analytics-metrics)和[儲存體分析記錄](/rest/api/storageservices/about-storage-analytics-logging)。

**儲存體分析計量**用來收集 Azure 儲存體帳戶的計量，可用來監視儲存體帳戶健康狀態。 可以啟用 Blob、檔案、資料表和佇列的計量。

**儲存體分析記錄**是在伺服器端執行，並可讓您在儲存體帳戶中記錄成功和失敗要求的詳細資料。 這些記錄可讓您查看資料表、佇列和 Blob 的讀取、寫入和刪除作業詳細資料，以及失敗要求的原因。 記錄不適用於 Azure 檔案。

若要設定監視，可以使用 [Azure 入口網站](https://portal.azure.com)、PowerShell，或使用儲存體用戶端程式庫以程式設計方式進行。

> [!NOTE]
> 您可以使用 PowerShell 啟用分鐘分析。 這項功能無法使用於入口網站。
>

* 要瞭解如何使用 PowerShell 啟用和查看存儲指標資料，請參閱[存儲分析指標](storage-analytics-metrics.md)。

* 若要了解如何使用 PowerShell 啟用和擷取儲存體記錄的資料，請參閱[如何使用 PowerShell 啟用儲存體記錄](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)和[尋找儲存體記錄的記錄資料](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)。

* 如需使用儲存體計量和儲存體記錄疑難排解儲存體問題的詳細資訊，請參閱 [監控、診斷和疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)。

## <a name="manage-the-data-in-the-storage-account"></a>管理儲存體帳戶中的資料

您現在已了解如何使用 PowerShell 管理儲存體帳戶，您可以使用下列文章來了解如何存取儲存體帳戶中的資料物件。

* [如何使用 PowerShell 管理 Blob](../blobs/storage-how-to-use-blobs-powershell.md)
* [如何使用 PowerShell 管理檔案](../files/storage-how-to-use-files-powershell.md)
* [如何使用 PowerShell 管理佇列](../queues/storage-powershell-how-to-use-queues.md)
* [使用 PowerShell 執行 Azure 資料表儲存體作業](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB 資料表 API 提供資料表儲存體的高階功能，例如周全的全域發佈、低延遲讀取和寫入、自動次要索引，以及專用的輸送量。

* 如需詳細資訊，請參閱 [Azure Cosmos DB 資料表 API](../../cosmos-db/table-introduction.md)。

## <a name="independent-cloud-deployments-of-azure"></a>Azure 的獨立雲端部署

大多數人會針對其全域 Azure 部署使用 Azure 公用雲端。 也有因為主權等等原因的一些獨立部署 Microsoft Azure。 這些獨立部署稱為「環境」。 以下是可用的環境：

* [Azure Government 雲端](https://azure.microsoft.com/features/gov/)
* [Azure中國 21Vianet 雲，由 21Vianet 在中國運營](http://www.windowsazure.cn/)
* [Azure 德國雲端](../../germany/germany-welcome.md)

如需如何使用 PowerShell 來存取這些雲端及其儲存體的詳細資訊，請參閱[使用 PowerShell 在 Azure 獨立雲端中管理儲存體](storage-powershell-independent-clouds.md)。

## <a name="clean-up-resources"></a>清除資源

如果您針對這個練習建立新的資源群組和儲存體帳戶，您可以藉由移除資源群組來移除您建立的所有資產。 這會同時刪除群組內含的所有資源。 在本例中，它會移除建立的儲存體帳戶和資源群組本身。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>後續步驟

本做法文章涵蓋使用管理層 Cmdlet 管理儲存體帳戶的一般作業。 您已了解如何︰

> [!div class="checklist"]
> * 列出儲存體帳戶
> * 取得現有儲存體帳戶的參考
> * 建立儲存體帳戶
> * 設定儲存體帳戶屬性
> * 擷取和重新產生存取金鑰
> * 保護對儲存體帳戶的存取
> * 啟用儲存體分析

本文也提供數個其他文章的參考，例如如何管理資料物件、如何啟用儲存體分析，以及如何存取 Azure 獨立雲端 (例如中國雲端、德國雲端和政府雲端)。 以下是可供參考的一些其他相關文章和資源：

* [Azure 儲存體控制層 PowerShell Cmdlet](/powershell/module/az.storage/)
* [Azure 儲存體資料層 PowerShell Cmdlet](/powershell/module/azure.storage/)
* [Windows PowerShell 參考](/powershell/scripting/overview)
