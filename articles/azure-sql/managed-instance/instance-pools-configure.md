---
title: 將 SQL 受控執行個體部署至實例集區
titleSuffix: Azure SQL Managed Instance
description: 本文說明如何建立及管理 Azure SQL 受控執行個體集區（預覽）。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 9bd98d69c9a941e8da08fc7ab798c37b1a22f0bc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86498390"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>將 Azure SQL 受控執行個體部署至實例集區
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文會詳細說明如何建立[實例集](instance-pools-overview.md)區，並將 Azure SQL 受控執行個體部署到其中。 

## <a name="instance-pool-operations"></a>實例集區作業

下表顯示與實例集區相關的可用作業，以及其在 Azure 入口網站和 PowerShell 中的可用性。

|命令|Azure 入口網站|PowerShell|
|:---|:---|:---|
|建立實例集區|否|是|
|更新實例集區（屬性數目有限）|否 |是 |
|檢查實例集區的使用方式和屬性|否|是 |
|刪除實例集區|否|是|
|在實例集區內建立受控實例|否|是|
|更新受控實例的資源使用狀況|是 |是|
|檢查受控實例的使用方式和屬性|是|是|
|從集區刪除受控實例|是|是|
|在集區內的實例中建立資料庫|是|是|
|從 SQL 受控執行個體中刪除資料庫|是|是|

可用的[PowerShell 命令](https://docs.microsoft.com/powershell/module/az.sql/)：

|Cmdlet |描述 |
|:---|:---|
|[新增-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | 建立 SQL 受控執行個體集區。 |
|[AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | 傳回實例集區的相關資訊。 |
|[設定-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | 在 SQL 受控執行個體中設定實例集區的屬性。 |
|[移除-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | 移除 SQL 受控執行個體中的實例集區。 |
|[AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | 傳回 SQL 受控執行個體集區使用方式的相關資訊。 |


若要使用 PowerShell，請[安裝最新版的 Powershell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)，然後依照指示[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

如需在集區和單一實例中與實例相關的作業，請使用標準的[受控實例命令](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)，但當您針對集區中的實例使用這些命令時，必須填入 [*實例集區名稱*] 屬性。

## <a name="deployment-process"></a>部署程序

若要將受控實例部署到實例集區，您必須先部署實例集區，這是一次性長時間執行的作業，其持續時間與部署[在空白子網中建立的單一實例](sql-managed-instance-paas-overview.md#management-operations)相同。 之後，您就可以將受控實例部署到集區中，這是相當快速的作業，通常需要長達五分鐘的時間。 實例集區參數必須明確指定為這項作業的一部分。

在公開預覽中，這兩個動作僅支援使用 PowerShell 和 Azure Resource Manager 範本。 目前無法使用 Azure 入口網站體驗。

將受控實例部署至集區之後，您*可以*使用 Azure 入口網站在 [定價層] 頁面上變更其屬性。

## <a name="create-a-virtual-network-with-a-subnet"></a>建立具有子網路的虛擬網路 

若要將多個實例集區放在同一個虛擬網路中，請參閱下列文章：

- [判斷 AZURE SQL 受控執行個體的 VNet 子網大小](vnet-subnet-determine-size.md)。
- 使用[Azure 入口網站範本](virtual-network-subnet-create-arm-template.md)建立新的虛擬網路和子網，或遵循[準備現有虛擬網路](vnet-existing-add-subnet.md)的指示。
 

## <a name="create-an-instance-pool"></a>建立實例集區 

完成上述步驟之後，您就可以開始建立實例集區。

下列限制適用于實例集區：

- 只有一般用途和第5代可供公開預覽。
- 集區名稱只能包含小寫字母、數位和連字號，而且不能以連字號開頭。
- 如果您想要使用 Azure Hybrid Benefit，它會套用至實例集區層級。 您可以在建立集區時設定授權類型，或在建立後隨時進行更新。

> [!IMPORTANT]
> 部署實例集區是長時間執行的作業，需要大約4.5 小時。

若要取得網路參數：

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

若要建立實例集區：

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> 由於部署實例集區是長時間執行的作業，因此您必須等到它完成後，再執行本文中的下列任何步驟。

## <a name="create-a-managed-instance"></a>建立受控執行個體

成功部署實例集區之後，就可以在其中建立受控實例。

若要建立受控實例，請執行下列命令：

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

在集區中部署實例需要幾分鐘的時間。 建立第一個實例之後，即可建立其他實例：

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>建立資料庫 

若要在集區內的受控實例中建立和管理資料庫，請使用單一實例命令。

若要在受控實例內建立資料庫：

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>取得集區使用量 
 
取得集區內的實例清單：

```powershell
$instancePool | Get-AzSqlInstance
```


若要取得集區資源使用方式：

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


若要取得其內部集區和實例的詳細使用方式總覽：

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

若要列出實例中的資料庫：

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> 每個集區有100個資料庫的限制（不是每個實例）。


## <a name="scale"></a>調整 


使用資料庫填入受控實例之後，您可能會遇到關於儲存體或效能的實例限制。 在此情況下，如果尚未超過集區使用量，您可以調整您的實例。
調整集區內的受控實例是需要幾分鐘的作業。 調整的必要條件可在實例集區層級上虛擬核心和儲存。

若要更新虛擬核心和儲存體大小的數目：

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


僅更新儲存體大小：

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>連線 

若要連接到集區中的受控實例，必須執行下列兩個步驟：

1. [啟用實例的公用端點](#enable-the-public-endpoint)。
2. [將輸入規則新增至網路安全性群組（NSG）](#add-an-inbound-rule-to-the-network-security-group)。

這兩個步驟都完成之後，您可以使用建立實例時提供的公用端點位址、埠和認證來連接到實例。 

### <a name="enable-the-public-endpoint"></a>啟用公用端點

若要啟用實例的公用端點，可以透過 Azure 入口網站或使用下列 PowerShell 命令來完成：


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

在建立實例時，也可以設定這個參數。

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>將輸入規則新增至網路安全性群組 

您可以透過 Azure 入口網站或使用 PowerShell 命令來完成此步驟，並可在針對受控實例準備子網之後隨時執行。

如需詳細資訊，請參閱[允許網路安全性群組上的公用端點流量](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)。


## <a name="move-an-existing-single-instance-to-a-pool"></a>將現有的單一實例移至集區
 
將實例移入和移出集區是其中一個公用預覽限制。 因應措施取決於從集區外部實例到已在集區中的實例的資料庫時間點還原。 

這兩個實例必須位於相同的訂用帳戶和區域中。 目前不支援跨區域和跨訂用帳戶還原。

此程式會有一段停機時間。

若要移動現有的資料庫：

1. 暫停您要遷移之受控實例上的工作負載。
2. 產生腳本來建立系統資料庫，並在實例集區內的實例上執行它們。
3. 從單一實例到集區中的實例，執行每個資料庫的時間點還原。

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. 將您的應用程式指向新的實例，並繼續其工作負載。

如果有多個資料庫，請針對每個資料庫重複此程式。


## <a name="next-steps"></a>後續步驟

- 如需功能與比較清單，請參閱 [SQL 的一般功能](../database/features-comparison.md)。
- 如需 VNet 設定的詳細資訊，請參閱[SQL 受控執行個體 vnet](connectivity-architecture-overview.md)設定。
- 如需建立受控實例並從備份檔案還原資料庫的快速入門，請參閱[建立受控實例](instance-create-quickstart.md)。
- 如需使用 Azure 資料庫移轉服務進行遷移的教學課程，請參閱[使用資料庫移轉服務的 SQL 受控執行個體遷移](../../dms/tutorial-sql-server-to-managed-instance.md)。
- 若要使用內建的疑難排解情報來進行 SQL 受控執行個體資料庫效能的先進監視，請參閱[使用 Azure SQL 分析監視 AZURE SQL 受控執行個體](../../azure-monitor/insights/azure-sql.md)。
- 如需定價資訊，請參閱[SQL 受控執行個體定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
