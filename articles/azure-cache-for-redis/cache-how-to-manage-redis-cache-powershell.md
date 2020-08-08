---
title: 使用 Azure PowerShell 管理 Azure Cache for Redis
description: 了解如何使用 Azure PowerShell 執行 Azure Cache for Redis 的管理工作。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: c51e67dcc3536a3083179451743b1c97cf618dae
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88004863"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>使用 Azure PowerShell 管理 Azure Cache for Redis
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本主題說明如何執行一般工作，例如建立、更新及調整 Azure Cache for Redis 執行個體，如何重新產生存取金鑰，以及如何檢視您的快取的相關資訊。 如需 Azure Cache for Redis 的 PowerShell Cmdlet 完整清單，請參閱 [Azure Cache for Redis Cmdlet](https://docs.microsoft.com/powershell/module/az.rediscache)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

如需傳統部署模型的詳細資訊，請參閱 [Azure Resource Manager 與傳統部署：了解資源的部署模型和狀態](../azure-resource-manager/management/deployment-models.md)。

## <a name="prerequisites"></a>Prerequisites
如果您已安裝 Azure PowerShell，其必須是 Azure PowerShell 1.0.0 或更新的版本。 您可以在 Azure PowerShell 命令提示字元下使用這個命令來檢查已安裝的 Azure PowerShell 版本。

```azurepowershell
    Get-Module Az | format-table version
```

首先，您必須使用此命令登入 Azure。

```azurepowershell
    Connect-AzAccount
```

在 [Microsoft Azure 登入] 對話方塊中，指定 Azure 帳戶的電子郵件地址和密碼。

接下來，如果您有多個 Azure 訂用帳戶，請設定 Azure 訂用帳戶。 如果想查看目前的訂用帳戶清單，請執行這個命令。

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

若要指定訂用帳戶，請執行下列命令。 在下列範例中，訂用帳戶的名稱為 `ContosoSubscription`。

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

在將 Windows PowerShell 與 Azure 資源管理員搭配使用之前，您需要下列項目：

* Windows PowerShell 3.0 或 4.0 版本。 若要找出 Windows PowerShell 的版本，輸入：`$PSVersionTable`，並確認 `PSVersion` 的值是 3.0 或 4.0。 若要安裝相容版本，請參閱 [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。

若要取得您在本教學課程中任何所見 Cmdlet 的詳細說明，請使用 Get-Help Cmdlet。

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

例如，如需取得 `New-AzRedisCache` Cmdlet 的說明，請輸入：

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>如何連線到其他雲端
根據預設，Azure 環境是 `AzureCloud`，其代表全域 Azure 雲端執行個體。 若要連線至不同的執行個體，請使用 `Connect-AzAccount` 命令搭配 `-Environment` 或使用 -`EnvironmentName` 命令列參數搭配所需的環境或環境名稱。

若要查看可用環境的清單，請執行 `Get-AzEnvironment` Cmdlet。

### <a name="to-connect-to-the-azure-government-cloud"></a>連線到 Azure Government 雲端
如果要連線到 Azure Government 雲端，請使用下列其中一個命令。

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

或

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

如果要在 Azure Government 雲端建立快取，請使用下列其中一個位置。

* 美國政府維吉尼亞州
* 美國政府愛荷華州

如需 Azure Government 雲端的詳細資訊，請參閱 [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) 和 [Microsoft Azure Government 開發人員指南](../azure-government-developer-guide.md)。

### <a name="to-connect-to-the-azure-china-cloud"></a>連線到 Azure 中國雲端
如果要連線到 Azure 中國雲端，請使用下列其中一個命令。

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

或

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

如果要在 Azure 中國雲端建立快取，請使用下列其中一個位置。

* 中國東部
* 中國北部

如需 Azure 中國雲端的詳細資訊，請參閱 [由中國的世紀互聯營運的 Azure 中國雲端](https://www.windowsazure.cn/)。

### <a name="to-connect-to-microsoft-azure-germany"></a>連線到 Microsoft Azure (德國)
若要連線到 Microsoft Azure (德國)，請使用下列其中一個命令。

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

或

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

若要在 Microsoft Azure (德國) 中建立快取，請使用下列其中一個位置。

* 德國中部
* 德國東北部

如需有關 Microsoft Azure (德國) 的詳細資訊，請參閱 [Microsoft Azure (德國)](https://azure.microsoft.com/overview/clouds/germany/)。

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>用於 Azure Cache for Redis PowerShell 的屬性
下表為使用 Azure PowerShell 建立和管理 Azure Cache for Redis 執行個體時常用參數的屬性和說明。

| 參數 | 描述 | 預設 |
| --- | --- | --- |
| 名稱 |快取的名稱 | |
| Location |快取的位置 | |
| resourceGroupName |資源群組名稱，將在其中建立快取 | |
| 大小 |快取的大小。 有效值為：P1、P2、P3、P4、C0、C1、C2、C3、C4、C5、C6、250MB、1GB、2.5GB、6GB、13GB、26GB、53GB |1GB |
| ShardCount |在啟用叢集的情況下建立進階快取時要建立的分區數目。 有效值為：1、2、3、4、5、6、7、8、9、10 | |
| SKU |指定快取的 SKU。 有效值為：基本、標準、進階 |標準 |
| RedisConfiguration |指定 Redis 組態設定。 如需每個設定的詳細資訊，請參閱以下的 [RedisConfiguration 屬性](#redisconfiguration-properties) 表格。 | |
| EnableNonSslPort |指出是否已啟用非 SSL 連接埠。 |False |
| MaxMemoryPolicy |這個參數已被取代，請改用 RedisConfiguration。 | |
| StaticIP |當快取是裝載在 VNET 中，為快取在子網路中指定唯一 IP 位址。 如果未提供，則會從子網路中為您選擇一個。 | |
| 子網路 |當快取是裝載在 VNET 中，指定要在其中部署快取的子網路。 | |
| VirtualNetwork |當快取是裝載在 VNET 中，指定要在其中部署快取的 VNET 之資源識別碼。 | |
| KeyType |指定更新存取金鑰時要重新產生哪一個存取金鑰。 有效值為：主要、次要 | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration 屬性
| 屬性 | 描述 | 定價層 |
| --- | --- | --- |
| rdb-backup-enabled |是否已啟用 [Redis 資料持續性](cache-how-to-premium-persistence.md) |僅限進階版 |
| rdb-storage-connection-string |[Redis 資料持續性](cache-how-to-premium-persistence.md) |僅限進階版 |
| rdb-backup-frequency |[Redis 資料持續性](cache-how-to-premium-persistence.md) |僅限進階版 |
| maxmemory-reserved |設定非快取程序的 [保留記憶體](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) |標準和進階 |
| maxmemory-policy |設定快取的 [收回原則](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) |所有定價層 |
| notify-keyspace-events |設定 [Keyspace 通知](cache-configure.md#keyspace-notifications-advanced-settings) |標準和進階 |
| hash-max-ziplist-entries |設定小型彙總資料類型的 [記憶體最佳化](https://redis.io/topics/memory-optimization) |標準和進階 |
| hash-max-ziplist-value |設定小型彙總資料類型的 [記憶體最佳化](https://redis.io/topics/memory-optimization) |標準和進階 |
| set-max-intset-entries |設定小型彙總資料類型的 [記憶體最佳化](https://redis.io/topics/memory-optimization) |標準和進階 |
| zset-max-ziplist-entries |設定小型彙總資料類型的 [記憶體最佳化](https://redis.io/topics/memory-optimization) |標準和進階 |
| zset-max-ziplist-value |設定小型彙總資料類型的 [記憶體最佳化](https://redis.io/topics/memory-optimization) |標準和進階 |
| 資料庫 |設定資料庫數目。 這個屬性僅可以在建立快取時設定。 |標準和進階 |

## <a name="to-create-an-azure-cache-for-redis"></a>建立 Azure Cache for Redis
建立新的「Azure Redis 快取」執行個體時，會使用 [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) Cmdlet 來建立。

> [!IMPORTANT]
> 您第一次使用 Azure 入口網站在訂用帳戶中建立 Azure Cache for Redis 時，入口網站會為該訂用帳戶註冊 `Microsoft.Cache` 命名空間。 如果您嘗試使用 PowerShell 在訂用帳戶中建立第一個 Azure Cache for Redis，您必須先使用下列命令註冊該命名空間；否則 Cmdlet (例如 `New-AzRedisCache` 和 `Get-AzRedisCache`) 會失敗。
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

若要查看 `New-AzRedisCache`的可用參數清單及其說明，請執行下列命令。

```azurepowershell
    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

若要使用預設參數建立快取，請執行下列命令。

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`、`Name` 和 `Location` 是必要參數，其餘則為選擇性的而且有預設值。 執行先前的命令會建立標準 SKU Azure Cache for Redis 執行個體，具有指定的名稱、位置和資源群組，大小為 1 GB，且停用非 SSL 連接埠。

若要建立進階快取，請指定大小為 P1 (6 GB - 60 GB)、P2 (13 GB - 130 GB)、P3 (26 GB - 260 GB) 或 P4 (53 GB - 530 GB)。 若要啟用叢集，使用 `ShardCount` 參數指定分區計數。 下列範例會建立具有 3 個分區的 P1 進階快取。 P1 進階快取的大小為 6 GB，因為我們指定三個分區，大小總計為 18 GB (3 x 6 GB)。

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

若要指定 `RedisConfiguration` 參數的值，以索引鍵/值組的方式將值括在 `{}` 內，例如 `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`。 下列範例會建立標準 1 GB 快取，具有 `allkeys-random` maxmemory 原則，且 keyspace 通知設為 `KEA`。 如需詳細資訊，請參閱 [Keyspace 通知 (進階設定)](cache-configure.md#keyspace-notifications-advanced-settings) 和[記憶體原則](cache-configure.md#memory-policies)。

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>在快取建立期間設定資料庫設定
`databases` 設定僅可以在快取建立期間設定。 下列範例會使用 [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache) Cmdlet 來建立具有 48 個資料庫的進階 P3 (26 GB) 快取。

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

如需 `databases` 屬性的詳細資訊，請參閱 [預設的 Azure Cache for Redis 伺服器組態](cache-configure.md#default-redis-server-configuration)。 如需有關使用 [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) Cmdlet 來建立快取的詳細資訊，請參閱先前的＜建立 Azure Redis 快取＞一節。

## <a name="to-update-an-azure-cache-for-redis"></a>更新 Azure Cache for Redis
更新「Azure Redis 快取」執行個體時，會使用 [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache) Cmdlet 來更新。

若要查看 `Set-AzRedisCache`的可用參數清單及其說明，請執行下列命令。

```azurepowershell
    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

`Set-AzRedisCache` Cmdlet 可用來更新屬性，例如 `Size`、`Sku`、`EnableNonSslPort` 和 `RedisConfiguration` 的值。 

下列命令會更新名為 myCache 的 Azure Cache for Redis 的 maxmemory-policy。

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>調整 Azure Cache for Redis
修改 `Size`、`Sku` 或 `ShardCount` 屬性時，可使用 `Set-AzRedisCache` 來調整 Azure Cache for Redis 執行個體。 

> [!NOTE]
> 使用 PowerShell 調整快取，和從 Azure 入口網站調整快取有相同的限制和準則。 您可以調整具有下列限制的不同定價層。
> 
> * 您無法從較高的定價層調整至較低的定價層。
> * 您無法從**進階**快取向下調整至**標準**或**基本**快取。
> * 您無法從**標準**快取向下調整到**基本**快取。
> * 您可以從**基本**快取調整到**標準**快取，但您無法同時變更大小。 如果您需要不同的大小，您可以進行後續調整作業，調整到您需要的大小。
> * 您無法直接從**基本**快取調整至**進階**快取。 您必須在單一調整作業中從**基本**調整至**標準**，然後在後續的調整作業中從**標準**調整至**進階**。
> * 您無法從較大的大小向下調整至 **C0 (250 MB)** 的大小。
> 
> 如需詳細資訊，請參閱 [如何調整 Azure Cache for Redis](cache-how-to-scale.md)。
> 
> 

下列範例示範如何將名為 `myCache` 的快取調整為 2.5 GB 快取。 請注意，此命令可用於基本或標準快取。

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

發出此命令之後，會傳回快取的狀態 (類似於呼叫 `Get-AzRedisCache`)。 請注意，`ProvisioningState` 為 `Scaling`。

```azurepowershell
    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

當調整作業完成時，`ProvisioningState` 會變更為 `Succeeded`。 如果您需要進行後續的調整作業，例如先從基本變更為標準，然後再變更大小，您必須等到先前作業完成，否則會收到類似下列的錯誤。

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>取得 Azure Cache for Redis 的相關資訊
您可以使用 [Get-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache) Cmdlet 來擷取快取的相關資訊。

若要查看 `Get-AzRedisCache`的可用參數清單及其說明，請執行下列命令。

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

若要傳回目前訂用帳戶中所有快取的相關資訊，請不帶任何參數執行 `Get-AzRedisCache`。

```azurepowershell
    Get-AzRedisCache
```

若要傳回特定資源群組中所有快取的相關資訊，請執行 `Get-AzRedisCache` 並使用 `ResourceGroupName` 參數。

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

若要傳回特定快取的相關資訊，請執行 `Get-AzRedisCache`，並使用 `Name` 參數包含快取名稱，和 `ResourceGroupName` 參數包含快取的資源群組。

```azurepowershell
    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>擷取 Azure Cache for Redis 的存取金鑰
若要擷取您快取的存取金鑰，您可以使用 [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey) Cmdlet。

若要查看 `Get-AzRedisCacheKey`的可用參數清單及其說明，請執行下列命令。

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

若要擷取您快取的金鑰，請呼叫 `Get-AzRedisCacheKey` Cmdlet，並傳入快取的名稱以及包含快取的資源群組名稱。

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>重新產生 Azure Cache for Redis 的存取金鑰
若要重新產生您快取的存取金鑰，您可以使用 [New-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey) Cmdlet。

若要查看 `New-AzRedisCacheKey`的可用參數清單及其說明，請執行下列命令。

```azurepowershell
    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

若要重新產生快取的主要或次要金鑰，請呼叫 `New-AzRedisCacheKey` Cmdlet，並傳入名稱、資源群組，且針對 `KeyType` 參數指定 `Primary` 或 `Secondary`。 在下列範例中，會重新產生快取的次要存取金鑰。

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>刪除 Azure Cache for Redis
若要刪除「Azure Redis 快取」，請使用 [Remove-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache) Cmdlet。

若要查看 `Remove-AzRedisCache`的可用參數清單及其說明，請執行下列命令。

```azurepowershell
    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

在下列範例中，會移除名為 `myCache` 的快取。

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>匯入 Azure Cache for Redis
您可以使用 `Import-AzRedisCache` Cmdlet 將資料匯入 Azure Cache for Redis 執行個體中。

> [!IMPORTANT]
> 匯入/匯出僅[適用于進](cache-overview.md#service-tiers)階層快取。 如需匯入/匯出的詳細資訊，請參閱 [在 Azure Cache for Redis 中匯入與匯出資料](cache-how-to-import-export-data.md)。
> 
> 

若要查看 `Import-AzRedisCache`的可用參數清單及其說明，請執行下列命令。

```azurepowershell
    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


以下命令可將資料從 SAS URI 所指定的 Blob 匯入 Azure Cache for Redis 中。

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>匯出 Azure Cache for Redis
您可以使用 `Export-AzRedisCache` Cmdlet 從 Azure Cache for Redis 執行個體匯出資料。

> [!IMPORTANT]
> 匯入/匯出僅[適用于進](cache-overview.md#service-tiers)階層快取。 如需匯入/匯出的詳細資訊，請參閱 [在 Azure Cache for Redis 中匯入與匯出資料](cache-how-to-import-export-data.md)。
> 
> 

若要查看 `Export-AzRedisCache`的可用參數清單及其說明，請執行下列命令。

```azurepowershell
    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


以下命令可將資料從 Azure Cache for Redis 執行個體匯出到 SAS URI 所指定的容器中。

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>重新啟動 Azure Cache for Redis
您可以使用 `Reset-AzRedisCache` Cmdlet 將 Azure Cache for Redis 執行個體重新啟動。

> [!IMPORTANT]
> 重新開機僅[適用于進](cache-overview.md#service-tiers)階層快取。 如需重新啟動快取的詳細資訊，請參閱 [快取管理 - 重新啟動](cache-administration.md#reboot)。
> 
> 

若要查看 `Reset-AzRedisCache`的可用參數清單及其說明，請執行下列命令。

```azurepowershell
    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


以下命令會重新啟動指定快取的兩個節點。

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>後續步驟
若要深入了解如何將 Windows PowerShell 與 Azure 搭配使用，請參閱下列資源：

* [MSDN 上的 Azure Cache for Redis Cmdlet 文件](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Azure Resource Manager Cmdlet](https://go.microsoft.com/fwlink/?LinkID=394765)：了解如何使用 Azure Resource Manager 模組中的 Cmdlet。
* [使用資源群組來管理您的 Azure 資源](../azure-resource-manager/templates/deploy-portal.md)：了解如何在 Azure 入口網站中建立和管理資源群組。
* [Azure 部落格](https://azure.microsoft.com/blog/)：了解 Azure 的新功能。
* [Windows PowerShell 部落格](https://devblogs.microsoft.com/powershell/)：了解 Windows PowerShell 的新功能。
* ["Hey, Scripting Guy!"部落格](https://blogs.technet.microsoft.com/heyscriptingguy/author/the-scripting-guys/)：從 Windows PowerShell 社群中取得實際的秘訣及訣竅。

