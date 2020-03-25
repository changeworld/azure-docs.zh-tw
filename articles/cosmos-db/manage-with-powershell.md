---
title: 使用 PowerShell 建立和管理 Azure Cosmos DB
description: 使用 Azure Powershell 來管理 Azure Cosmos DB 帳戶、資料庫、容器和輸送量。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 978f37d08275de704dd01c0251dde42665fca552
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79222095"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>使用 PowerShell 來管理 Azure Cosmos DB SQL API 資源

下列指南說明如何使用 PowerShell 來為 Azure Cosmos DB 資源 (包括帳戶、資料庫、容器和輸送量) 編寫指令碼並將其管理工作自動化。 Azure Cosmos DB 的管理可由 Azure Cosmos DB 資源提供者直接透過 AzResource Cmdlet 來處理。 若要檢視 Azure Cosmos DB 資源提供者所有可使用 PowerShell 來管理的屬性，請參閱 [Azure Cosmos DB 資源提供者結構描述](/azure/templates/microsoft.documentdb/allversions)

如需跨平台管理 Azure Cosmos DB，您可以使用 [Azure CLI](manage-with-cli.md)、[REST API][rp-rest-api] 或 [Azure 入口網站](create-sql-api-dotnet.md#create-account)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>開始使用

請遵循[如何安裝和設定 Azure PowerShell][powershell-install-configure] 的指示進行安裝，並在 PowerShell 中登入 Azure 帳戶。

* 如果您想要執行下列命令但不需要使用者確認，請在命令附加 `-Force` 旗標。
* 下列所有命令都是同步的。

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

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> 建立 Azure Cosmos 帳戶

此命令會使用[多個區域][distribute-data-globally]、限定過期的[一致性原則](consistency-levels.md)來建立 Azure Cosmos 資料庫帳戶。

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Azure Cosmos 帳戶的名稱。 必須是小寫，可接受英數字元和 '-' 字元，且介於 3 到 31 個字元之間。
* `$location` Azure Cosmos 帳戶資源的位置。
* `$locations` 資料庫帳戶的複本區域。 每個資料庫帳戶都必須有一個容錯移轉優先順序值為 0 的寫入區域。
* `$consistencyPolicy` Azure Cosmos 帳戶的預設一致性層級。 如需詳細資訊，請參閱 [Azure Cosmos DB 的一致性層級](consistency-levels.md)。
* `$CosmosDBProperties` 傳遞至 Cosmos DB Azure Resource Manager 提供者以供佈建帳戶的屬性值。

您可以為 Azure Cosmos 帳戶設定 IP 防火牆以及虛擬網路服務端點。 如需如何為 Azure Cosmos DB 設定 IP 防火牆的相關資訊，請參閱[設定 IP 防火牆](how-to-configure-firewall.md)。  如需如何為 Azure Cosmos DB 啟用服務端點的詳細資訊，請參閱[設定從虛擬網路存取](how-to-configure-vnet-service-endpoint.md)。

### <a name="list-all-azure-cosmos-accounts-in-a-subscription"></a><a id="list-accounts"></a> 列出訂用帳戶中的所有 Azure Cosmos 帳戶

此命令可讓您列出訂用帳戶中的所有 Azure Cosmos 帳戶。

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> 取得 Azure Cosmos 帳戶的屬性

此命令可讓您取得現有 Azure Cosmos 帳戶的屬性。

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> 更新 Azure Cosmos 帳戶

此命令可讓您更新 Azure Cosmos 資料庫帳戶屬性。 可更新的屬性如下：

* 新增或移除區域
* 變更預設的一致性原則
* 變更 IP 範圍篩選條件
* 變更虛擬網路組態
* 啟用多重主機

> [!NOTE]
> 您不能同時新增或移除 `locations` 區域，以及變更 Azure Cosmos 帳戶的其他屬性。 修改區域必須與帳戶資源的任何其他變更分開作業。
> [!NOTE]
> 此命令可讓您新增及移除區域，但不允許您修改容錯移轉優先順序或觸發手動容錯移轉。 請參閱[修改容錯移轉優先順序](#modify-failover-priority)和[觸發手動容錯移轉](#trigger-manual-failover)。

```azurepowershell-interactive
# Create an account with 2 regions
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false },
    @{ "locationName"="South Central US"; "failoverPriority"=2, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a> 為 Azure Cosmos 帳戶啟用多個寫入區域

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> 刪除 Azure Cosmos 帳戶

此命令可讓您刪除現有的 Azure Cosmos 帳戶。

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> 更新 Azure Cosmos 帳戶的標籤

下列範例說明如何設定 Azure Cosmos 帳戶的 [Azure 資源標籤][azure-resource-tags]。

> [!NOTE]
> 此命令可以透過附加 `-Tags` 旗標與對應的參數，來結合建立或更新命令。

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> 列出帳戶金鑰

當您建立 Azure Cosmos DB 帳戶時，服務會產生兩個主要存取金鑰，用於存取 Azure Cosmos DB 帳戶時的驗證。 透過提供這兩個存取金鑰，Azure Cosmos DB 讓您可以重新產生金鑰，同時又不需中斷 Azure Cosmos DB 帳戶。 也提供驗證唯讀作業的唯讀金鑰。 有兩個讀寫金鑰 (主要和次要) 和兩個唯讀金鑰 (主要和次要)。

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Write-Host "PrimaryKey =" $keys.primaryMasterKey
Write-Host "SecondaryKey =" $keys.secondaryMasterKey
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> 列出連接字串

對於 MongoDB 帳戶，您可以使用下列命令來擷取將您的 MongoDB 應用程式連線到資料庫帳戶的連接字串。

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> 重新產生帳戶金鑰

請定期重新產生 Azure Cosmos 帳戶的存取金鑰，以便讓連線更加安全。 您可以對帳戶指派主要和次要存取金鑰。 這可讓用戶端在重新產生其他存取金鑰時保有存取能力。 Azure Cosmos 帳戶有四種金鑰 (主要、次要、主要唯讀和次要唯讀)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> 啟用自動容錯移轉

讓 Cosmos 帳戶在主要區域變得無法使用時可容錯移轉至其次要區域。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> 修改容錯移轉優先順序

針對已設定自動容錯移轉的帳戶，您可以變更順序，當主要複本無法使用時，Cosmos 就會將次要複本提升為主要複本。

在下列範例中，我們假設目前的容錯移轉優先順序為：`West US 2 = 0`、`East US 2 = 1``South Central US = 2`。

> [!CAUTION]
> 變更 `locationName` 的 `failoverPriority=0` 會讓 Azure Cosmos 帳戶觸發手動容錯移轉。 變更其他優先順序則不會觸發容錯移轉。

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="South Central US"; "failoverPriority"=1 },
    @{ "locationName"="East US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>觸發手動容錯移轉

對於已設定手動容錯移轉的帳戶，您可以藉由將任何次要複本修改為 `failoverPriority=0`，以容錯移轉及提升至主要複本。 此作業也可用來起始災害復原演練，以測試災害復原規劃。

在下列範例中，假設帳戶目前的容錯移轉優先順序為 `West US 2 = 0` 和 `East US 2 = 1`，並翻轉區域。

> [!CAUTION]
> 變更 `locationName` 的 `failoverPriority=0` 會讓 Azure Cosmos 帳戶觸發手動容錯移轉。 變更其他優先順序則不會觸發容錯移轉。

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="South Central US"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="West US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos 資料庫

下列各節會示範如何管理 Azure Cosmos 資料庫，包括：

* [建立 Azure Cosmos 資料庫](#create-db)
* [使用共用輸送量來建立 Azure Cosmos 資料庫](#create-db-ru)
* [取得 Azure Cosmos 資料庫的輸送量](#get-db-ru)
* [列出帳戶中的所有 Azure Cosmos 資料庫](#list-db)
* [取得單一的 Azure Cosmos 資料庫](#get-db)
* [刪除 Azure Cosmos 資料庫](#delete-db)

### <a name="create-an-azure-cosmos-database"></a><a id="create-db"></a>建立 Azure Cosmos 資料庫

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a name="create-an-azure-cosmos-database-with-shared-throughput"></a><a id="create-db-ru"></a>使用共用輸送量來建立 Azure Cosmos 資料庫

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a name="get-the-throughput-of-an-azure-cosmos-database"></a><a id="get-db-ru"></a>取得 Azure Cosmos 資料庫的輸送量

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a name="get-all-azure-cosmos-databases-in-an-account"></a><a id="list-db"></a>取得帳戶中的所有 Azure Cosmos 資料庫

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a name="get-a-single-azure-cosmos-database"></a><a id="get-db"></a>取得單一的 Azure Cosmos 資料庫

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a name="delete-an-azure-cosmos-database"></a><a id="delete-db"></a>刪除 Azure Cosmos 資料庫

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos 容器

下列各節會示範如何管理 Azure Cosmos 容器，包括：

* [建立 Azure Cosmos 容器](#create-container)
* [使用大型資料分割索引鍵來建立 Azure Cosmos 容器](#create-container-big-pk)
* [取得 Azure Cosmos 容器的輸送量](#get-container-ru)
* [使用共用輸送量來建立 Azure Cosmos 容器](#create-container-ru)
* [使用自訂索引編製來建立 Azure Cosmos 容器](#create-container-custom-index)
* [建立已關閉索引編製的 Azure Cosmos 容器](#create-container-no-index)
* [使用唯一索引鍵和 TTL 來建立 Azure Cosmos 容器](#create-container-unique-key-ttl)
* [使用衝突解決來建立 Azure Cosmos 容器](#create-container-lww)
* [列出資料庫中的所有 Azure Cosmos 容器](#list-containers)
* [取得資料庫中的單一 Azure Cosmos 容器](#get-container)
* [刪除 Azure Cosmos 容器](#delete-container)

### <a name="create-an-azure-cosmos-container"></a><a id="create-container"></a>建立 Azure Cosmos 容器

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="create-an-azure-cosmos-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>使用大型資料分割索引鍵大小來建立 Azure Cosmos 容器

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="get-the-throughput-of-an-azure-cosmos-container"></a><a id="get-container-ru"></a>取得 Azure Cosmos 容器的輸送量

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a name="create-an-azure-cosmos-container-with-shared-throughput"></a><a id="create-container-ru"></a>使用共用輸送量來建立 Azure Cosmos 容器

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a name="create-an-azure-cosmos-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>使用自訂索引原則來建立 Azure Cosmos 容器

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="create-an-azure-cosmos-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>建立已關閉索引編製的 Azure Cosmos 容器

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="create-an-azure-cosmos-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>使用唯一索引鍵原則和 TTL 來建立 Azure Cosmos 容器

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 86400;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="create-an-azure-cosmos-container-with-conflict-resolution"></a><a id="create-container-lww"></a>使用衝突解決來建立 Azure Cosmos 容器

若要建立衝突解決原則以使用預存程序，請設定 `"mode"="custom"`，並將解析路徑設定為 `"conflictResolutionPath"="myResolverStoredProcedure"` 預存程序的名稱。 若要將所有衝突寫入到 ConflictsFeed 並分開處理，請設定 `"mode"="custom"` 和 `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="list-all-azure-cosmos-containers-in-a-database"></a><a id="list-containers"></a>列出資料庫中的所有 Azure Cosmos 容器

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a name="get-a-single-azure-cosmos-container-in-a-database"></a><a id="get-container"></a>取得資料庫中的單一 Azure Cosmos 容器

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a name="delete-an-azure-cosmos-container"></a><a id="delete-container"></a>刪除 Azure Cosmos 容器

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>後續步驟

* [所有 PowerShell 範例](powershell-samples.md)
* [如何管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)
* [建立 Azure Cosmos 容器](how-to-create-container.md)
* [在 Azure Cosmos DB 中設定存留時間](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
