---
title: 使用 PowerShell 管理 Azure Cosmos DB Core (SQL) API 資源
description: 使用 PowerShell 管理 Azure Cosmos DB Core (SQL) API 資源。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/07/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 652c546c5a38543e89f7a3b5ab8bc036c8d80911
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840875"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>使用 PowerShell 管理 Azure Cosmos DB Core (SQL) API 資源

下列指南說明如何使用 PowerShell 來編寫腳本並自動化管理 Azure Cosmos DB Core (SQL) API 資源，包括 Cosmos 帳戶、資料庫、容器和輸送量。

> [!NOTE]
> 本文中的範例使用 [Az.CosmosDB](/powershell/module/az.cosmosdb) 管理 Cmdlet。 如需有關最新變更的相關資訊，請參閱 [Az.CosmosDB](/powershell/module/az.cosmosdb) API 參考頁面。

針對 Azure Cosmos DB 的跨平台管理，您可以使用 `Az` 和 `Az.CosmosDB` Cmdlet 來搭配 [跨平台 PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)，也可使用 [Azure CLI](manage-with-cli.md)、[REST API][rp-rest-api] 或 [Azure 入口網站](create-sql-api-dotnet.md#create-account)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>開始使用

請遵循[如何安裝和設定 Azure PowerShell][powershell-install-configure] 的指示進行安裝，並在 PowerShell 中登入 Azure 帳戶。

> [!IMPORTANT]
> 無法重新命名 Azure Cosmos DB 資源，因為這違反了 Azure Resource Manager 搭配資源 Uri 運作的方式。

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 帳戶

下列各節會示範如何管理 Azure Cosmos 帳戶，包括：

* [建立 Azure Cosmos 帳戶](#create-account)
* [更新 Azure Cosmos 帳戶](#update-account)
* [列出訂用帳戶中的所有 Azure Cosmos 帳戶](#list-accounts)
* [取得 Azure Cosmos 帳戶](#get-account)
* [刪除 Azure Cosmos 帳戶](#delete-account)
* [更新 Azure Cosmos 帳戶的標籤](#update-tags)
* [列出 Azure Cosmos 帳戶的金鑰](#list-keys)
* [重新產生 Azure Cosmos 帳戶的金鑰](#regenerate-keys)
* [列出 Azure Cosmos 帳戶的連接字串](#list-connection-strings)
* [修改 Azure Cosmos 帳戶的容錯移轉優先順序](#modify-failover-priority)
* [觸發 Azure Cosmos 帳戶的手動容錯移轉](#trigger-manual-failover)
* [列出 Azure Cosmos DB 帳戶上的資源鎖定](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> 建立 Azure Cosmos 帳戶

此命令會使用[多個區域][distribute-data-globally]、[自動容錯移轉](how-to-manage-database-account.md#automatic-failover)和限定過期的[一致性原則](consistency-levels.md)來建立 Azure Cosmos DB 資料庫帳戶。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` 要在其中部署 Cosmos 帳戶的 Azure 資源群組。 此項目必須已經存在。
* `$locations` 資料庫帳戶的區域，包含的區域 `FailoverPriority 0` 是寫入區域。
* `$accountName` Azure Cosmos 帳戶的名稱。 必須是唯一的、小寫的，且僅包含英數字元和 '-' 字元，且長度介於 3 到 31 個字元之間。
* `$apiKind` 要建立的 Cosmos 帳戶類型。 如需詳細資訊，請參閱 [Cosmos DB 中的 API](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis)。
* `$consistencyPolicy`、`$maxStalenessInterval`和 `$maxStalenessPrefix` Azure Cosmos 帳戶的預設一致性層級與設定。 如需詳細資訊，請參閱 [Azure Cosmos DB 的一致性層級](consistency-levels.md)。

您可以為 Azure Cosmos 帳戶設定 IP 防火牆以、虛擬網路服務端點及私人端點。 如需如何為 Azure Cosmos DB 設定 IP 防火牆的相關資訊，請參閱[設定 IP 防火牆](how-to-configure-firewall.md)。 如需如何為 Azure Cosmos DB 啟用服務端點的詳細資訊，請參閱[設定從虛擬網路存取](how-to-configure-vnet-service-endpoint.md)。 如需如何為 Azure Cosmos DB 啟用私人端點的詳細資訊，請參閱[設定來自私人端點的存取](how-to-configure-private-endpoints.md)。

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> 列出資源群組中的所有 Azure Cosmos 帳戶

此命令會列出資源群組中的所有 Azure Cosmos 帳戶。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> 取得 Azure Cosmos 帳戶的屬性

此命令可讓您取得現有 Azure Cosmos 帳戶的屬性。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> 更新 Azure Cosmos 帳戶

此命令可讓您更新 Azure Cosmos DB 資料庫帳戶屬性。 可更新的屬性如下：

* 新增或移除區域
* 變更預設的一致性原則
* 變更 IP 範圍篩選條件
* 變更虛擬網路組態
* 啟用多重區域寫入

> [!NOTE]
> 您不能同時新增或移除區域 (`locations`)，以及變更 Azure Cosmos 帳戶的其他屬性。 修改區域必須與帳戶的任何其他變更分開作業。
> [!NOTE]
> 此命令可讓您新增及移除區域，但不允許您修改容錯移轉優先順序或觸發手動容錯移轉。 請參閱[修改容錯移轉優先順序](#modify-failover-priority)和[觸發手動容錯移轉](#trigger-manual-failover)。

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> 為 Azure Cosmos 帳戶啟用多個寫入區域

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> 刪除 Azure Cosmos 帳戶

此命令會刪除現有的 Azure Cosmos 帳戶。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> 更新 Azure Cosmos 帳戶的標籤

此命令會設定 Azure Cosmos 帳戶的 [Azure 資源標記][azure-resource-tags]。 標記可以在使用 `New-AzCosmosDBAccount` 建立帳戶時，以及使用 `Update-AzCosmosDBAccount`更新帳戶時設定。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> 列出帳戶金鑰

當您建立 Azure Cosmos 帳戶時，服務會產生兩個主要存取金鑰，可在存取 Azure Cosmos 帳戶時用於驗證。 也產生用於驗證唯讀作業的唯讀金鑰。
透過提供這兩個存取金鑰，Azure Cosmos DB 讓您可以重新產生及輪替金鑰 (一次一個)，同時又不需中斷 Azure Cosmos 帳戶。
Cosmos DB 帳戶有兩個讀寫金鑰 (主要和次要) 和兩個唯讀金鑰 (主要和次要)。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> 列出連接字串

下列命令會擷取連接字串，以將應用程式連線至 Cosmos DB 帳戶。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> 重新產生帳戶金鑰

請定期重新產生 Azure Cosmos 帳戶的存取金鑰，以便讓連線保持安全。 您可以對帳戶指派主要和次要存取金鑰。 這可讓用戶端在重新產生金鑰 (一次一個) 時保有存取能力。
Azure Cosmos 帳戶有四種金鑰 (主要、次要、主要唯讀和次要唯讀)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> 啟用自動容錯移轉

下列命令會將 Cosmos DB 帳戶設定為在主要區域變成無法使用時，自動容錯移轉到其次要區域。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> 修改容錯移轉優先順序

針對已設定自動容錯移轉的帳戶，您可以變更順序，當主要複本無法使用時，Cosmos 就會將次要複本提升為主要複本。

在下列範例中，我們假設目前的容錯移轉優先順序為 `West US 2 = 0`、`East US 2 = 1`、`South Central US = 2`。 此命令會將此變更為 `West US 2 = 0`、`South Central US = 1`、`East US 2 = 2`。

> [!CAUTION]
> 變更 `failoverPriority=0` 的位置會讓 Azure Cosmos 帳戶觸發手動容錯移轉。 變更其他優先順序則不會觸發容錯移轉。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>觸發手動容錯移轉

對於已設定手動容錯移轉的帳戶，您可以藉由將任何次要複本修改為 `failoverPriority=0`，以容錯移轉及提升至主要複本。 此作業也可用來起始災害復原演練，以測試災害復原規劃。

在下列範例中，假設帳戶目前的容錯移轉優先順序為 `West US 2 = 0` 和 `East US 2 = 1`，並翻轉區域。

> [!CAUTION]
> 變更 `failoverPriority=0` 的 `locationName` 會讓 Azure Cosmos 帳戶觸發手動容錯移轉。 變更其他優先順序則不會觸發容錯移轉。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> 列出 Azure Cosmos DB 帳戶上的資源鎖定

資源鎖定可以放在 Azure Cosmos DB 資源上，包括資料庫和集合。 下列範例顯示如何列出 Azure Cosmos DB 帳戶上的所有 Azure 資源鎖定。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB 資料庫

下列各節會示範如何管理 Azure Cosmos DB 資料庫，包括：

* [建立 Azure Cosmos DB 資料庫](#create-db)
* [使用共用輸送量來建立 Azure Cosmos DB 資料庫](#create-db-ru)
* [取得 Azure Cosmos DB 資料庫的輸送量](#get-db-ru)
* [將資料庫輸送量遷移至自動調整](#migrate-db-ru)
* [列出帳戶中的所有 Azure Cosmos DB 資料庫](#list-db)
* [取得單一的 Azure Cosmos DB 資料庫](#get-db)
* [刪除 Azure Cosmos DB 資料庫](#delete-db)
* [在 Azure Cosmos DB 資料庫上建立資源鎖定以防止刪除](#create-db-lock)
* [移除 Azure Cosmos DB 資料庫上的資源鎖定](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>建立 Azure Cosmos DB 資料庫

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>使用共用輸送量來建立 Azure Cosmos DB 資料庫

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>取得 Azure Cosmos 資料庫的輸送量

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>將資料庫輸送量遷移至自動調整

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>取得帳戶中的所有 Azure Cosmos 資料庫

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>取得單一的 Azure Cosmos 資料庫

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>刪除 Azure Cosmos DB 資料庫

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>在 Azure Cosmos DB 資料庫上建立資源鎖定以防止刪除

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>移除 Azure Cosmos DB 資料庫上的資源鎖定

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB 容器

下列各節會示範如何管理 Azure Cosmos DB 容器，包括：

* [建立 Azure Cosmos DB 容器](#create-container)
* [建立具有自動調整功能的 Azure Cosmos DB 容器](#create-container-autoscale)
* [使用大型資料分割索引鍵來建立 Azure Cosmos DB 容器](#create-container-big-pk)
* [取得 Azure Cosmos DB 容器的輸送量](#get-container-ru)
* [遷移容器輸送量以自動調整](#migrate-container-ru)
* [使用自訂索引編製來建立 Azure Cosmos DB 容器](#create-container-custom-index)
* [建立已關閉索引編製的 Azure Cosmos DB 容器](#create-container-no-index)
* [使用唯一索引鍵和 TTL 來建立 Azure Cosmos DB 容器](#create-container-unique-key-ttl)
* [使用衝突解決來建立 Azure Cosmos DB 容器](#create-container-lww)
* [列出資料庫中的所有 Azure Cosmos DB 容器](#list-containers)
* [取得資料庫中的單一 Azure Cosmos DB 容器](#get-container)
* [刪除 Azure Cosmos DB 容器](#delete-container)
* [在 Azure Cosmos DB 容器上建立資源鎖定以防止刪除](#create-container-lock)
* [移除 Azure Cosmos DB 容器上的資源鎖定](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>建立 Azure Cosmos DB 容器

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>建立具有自動調整功能的 Azure Cosmos DB 容器

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>使用大型資料分割索引鍵大小來建立 Azure Cosmos DB 容器

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>取得 Azure Cosmos 容器的輸送量

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>遷移容器輸送量以自動調整

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>使用自訂索引原則來建立 Azure Cosmos DB 容器

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>建立已關閉索引編製的 Azure Cosmos DB 容器

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>使用唯一索引鍵原則和 TTL 來建立 Azure Cosmos DB 容器

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>使用衝突解決來建立 Azure Cosmos DB 容器

若要將所有衝突寫入到 ConflictsFeed 並分開處理，請傳遞 `-Type "Custom" -Path ""`。

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

若要建立衝突解決原則以使用預存程序，請呼叫 `New-AzCosmosDBSqlConflictResolutionPolicy` 並傳遞 `-Type` 和 `-ConflictResolutionProcedure`參數。

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>列出資料庫中的所有 Azure Cosmos DB 容器

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>取得資料庫中的單一 Azure Cosmos DB 容器

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>刪除 Azure Cosmos DB 容器

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>在 Azure Cosmos DB 容器上建立資源鎖定以防止刪除

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>移除 Azure Cosmos DB 容器上的資源鎖定

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>後續步驟

* [所有 PowerShell 範例](powershell-samples.md)
* [如何管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)
* [建立 Azure Cosmos DB 容器](how-to-create-container.md)
* [在 Azure Cosmos DB 中設定存留時間](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/
