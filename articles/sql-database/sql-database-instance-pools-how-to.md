---
title: 實例集區操作指南（預覽）
description: 本文說明如何建立和管理 Azure SQL Database 實例集區（預覽）。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79299357"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Azure SQL Database 實例集區（預覽）操作指南

本文提供如何建立和管理[實例](sql-database-instance-pools.md)集區的詳細資料。

## <a name="instance-pool-operations"></a>實例集區作業

下表顯示與實例集區相關的可用作業，以及其在 Azure 入口網站和 PowerShell 中的可用性。

|Command|Azure 入口網站|PowerShell|
|:---|:---|:---|
|建立執行個體集區|否|是|
|更新實例集區（屬性數目有限）|否 |是 |
|檢查實例集區使用方式和屬性|否|是 |
|刪除實例集區|否|是|
|在實例集區內建立受控實例|否|是|
|更新受控實例資源使用量|是 |是|
|檢查受控實例的使用方式和屬性|是|是|
|從集區刪除受控實例|是|是|
|在位於集區的受控實例中建立資料庫|是|是|
|從受控實例中刪除資料庫|是|是|

可用的[PowerShell 命令](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |描述 |
|:---|:---|
|[新增-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | 建立 Azure SQL Database 實例集區。 |
|[AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | 傳回 Azure SQL 實例集區的相關資訊。 |
|[設定-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | 設定 Azure SQL Database 實例集區的屬性。 |
|[移除-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | 移除 Azure SQL Database 實例集區。 |
|[AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | 傳回 Azure SQL 實例集區使用量的相關資訊。 |


若要使用 PowerShell，請[安裝最新版的 Powershell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)，然後依照指示[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

如需在集區和單一實例中與實例相關的作業，請使用標準的[受控實例命令](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)，但當您針對集區中的實例使用這些命令時，必須填入 [*實例集區名稱*] 屬性。

## <a name="how-to-deploy-managed-instances-into-pools"></a>如何將受控實例部署至集區

將實例部署至集區的套裝程式含下列兩個步驟：

1. 一次性實例集區部署。 這是長期執行的作業，其持續時間與部署[在空白子網中建立的單一實例](sql-database-managed-instance.md#managed-instance-management-operations)相同。

2. 實例集區中的重複實例部署。 實例集區參數必須明確指定為這項作業的一部分。 這是相當快速的作業，通常需要最多5分鐘的時間。

在公開預覽中，這兩個步驟只能使用 PowerShell 和 Resource Manager 範本來支援。 目前無法使用 Azure 入口網站體驗。

將受控實例部署至集區之後，您*可以*使用 Azure 入口網站在 [定價層] 頁面上變更其屬性。


## <a name="create-an-instance-pool"></a>建立實例集區

若要建立實例集區：

1. [建立具有子網的虛擬網路](#create-a-virtual-network-with-a-subnet)。
2. [建立實例集](#create-an-instance-pool)區。


### <a name="create-a-virtual-network-with-a-subnet"></a>建立具有子網路的虛擬網路 

若要將多個實例集區放在同一個虛擬網路中，請參閱下列文章：

- [判斷 Azure SQL Database 受控實例的 VNet 子網大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。
- 使用[Azure 入口網站範本](sql-database-managed-instance-create-vnet-subnet.md)建立新的虛擬網路和子網，或遵循[準備現有虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)的指示。
 


### <a name="create-an-instance-pool"></a>建立實例集區 

完成上述步驟之後，您就可以開始建立實例集區。

下列限制適用于實例集區：

- 只有一般用途和第5代可供公開預覽。
- 集區名稱只能包含小寫字母、數位和連字號，且開頭不得為連字號。
- 如果您想要使用 AHB （Azure Hybrid Benefit），它會套用至實例集區層級。 您可以在建立集區時設定授權類型，或在建立後隨時進行更新。

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
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> 由於部署實例集區是長時間執行的作業，因此您必須等到它完成後，再執行本文中的下列任何步驟。

## <a name="create-a-managed-instance-inside-the-pool"></a>在集區內建立受控實例 

成功部署實例集區之後，就可以在其中建立實例。

若要建立受控實例，請執行下列命令：

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

在集區中部署實例需要幾分鐘的時間。 建立第一個實例之後，即可建立其他實例：

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>在實例內建立資料庫 

若要在集區內的受控實例中建立和管理資料庫，請使用單一實例命令。

若要在受控實例內建立資料庫：

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>取得實例集區使用方式 
 
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


## <a name="scale-a-managed-instance-inside-a-pool"></a>調整集區內的受控實例 


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



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>連接到集區內的受控實例

若要連接到集區中的受控實例，必須執行下列兩個步驟：

1. [啟用實例的公用端點](#enable-the-public-endpoint-for-the-instance)。
2. [將輸入規則新增至網路安全性群組（NSG）](#add-an-inbound-rule-to-the-network-security-group)。

這兩個步驟都完成之後，您可以使用建立實例時提供的公用端點位址、埠和認證來連接到實例。 

### <a name="enable-the-public-endpoint-for-the-instance"></a>啟用實例的公用端點

若要啟用實例的公用端點，可以透過 Azure 入口網站或使用下列 PowerShell 命令來完成：


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

在建立實例時，也可以設定這個參數。

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>將輸入規則新增至網路安全性群組 

您可以透過 Azure 入口網站或使用 PowerShell 命令來完成此步驟，並可在針對受控實例準備子網之後隨時執行。

如需詳細資訊，請參閱[允許網路安全性群組上的公用端點流量](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)。


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>移動實例集區內的現有單一實例 
 
將實例移入和移出集區是其中一個公用預覽限制。 可以使用的因應措施取決於從集區外部實例到已在集區中的實例的資料庫時間點還原。 

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

4. 將您的應用程式指向新的實例，並繼續執行工作負載。

如果有多個資料庫，請針對每個資料庫重複此程式。


## <a name="next-steps"></a>後續步驟

- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
- 如需 VNet 組態的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-connectivity-architecture.md)。
- 如需建立受控實例並從備份檔案還原資料庫的快速入門，請參閱[建立受控實例](sql-database-managed-instance-get-started.md)。
- 如需使用 Azure 資料庫移轉服務（DMS）進行遷移的教學課程，請參閱[使用 DMS 的受控實例遷移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 如需使用內建的疑難排解智慧進行受控實例資料庫效能的先進監視，請參閱[使用 Azure SQL 分析監視 Azure SQL Database](../azure-monitor/insights/azure-sql.md)。
- 如需定價資訊，請參閱[SQL Database 受控實例定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
