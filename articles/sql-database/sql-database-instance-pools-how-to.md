---
title: 實例池指南（預覽）
description: 本文介紹如何創建和管理 Azure SQL 資料庫實例池（預覽）。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299357"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Azure SQL 資料庫實例池（預覽）指南

本文詳細介紹了如何創建和管理[實例池](sql-database-instance-pools.md)。

## <a name="instance-pool-operations"></a>實例池操作

下表顯示了與實例池及其可用性相關的可用操作，這些操作在 Azure 門戶和 PowerShell 中。

|Command|Azure 入口網站|PowerShell|
|:---|:---|:---|
|建立執行個體集區|否|是|
|更新實例池（屬性數量有限）|否 |是 |
|檢查實例池使用方式和屬性|否|是 |
|刪除實例池|否|是|
|在實例池內創建託管實例|否|是|
|更新託管實例資源使用方式|是 |是|
|檢查託管實例使用方式和屬性|是|是|
|從池中刪除託管實例|是|是|
|在放置在池中的託管實例中創建資料庫|是|是|
|從託管實例中刪除資料庫|是|是|

可用的[電源外殼命令](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |描述 |
|:---|:---|
|[新實例池](/powershell/module/az.sql/new-azsqlinstancepool/) | 創建 Azure SQL 資料庫實例池。 |
|[獲取-AzSql實例池](/powershell/module/az.sql/get-azsqlinstancepool/) | 返回有關 Azure SQL 實例池的資訊。 |
|[設置-AzSql實例池](/powershell/module/az.sql/set-azsqlinstancepool/) | 設置 Azure SQL 資料庫實例池的屬性。 |
|[刪除-AzSql實例池](/powershell/module/az.sql/remove-azsqlinstancepool/) | 刪除 Azure SQL 資料庫實例池。 |
|[獲取-AzSql實例池使用](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | 返回有關 Azure SQL 實例池使用方式的資訊。 |


要使用 PowerShell，[請安裝最新版本的 PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)，然後按照說明[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

對於與池內實例和單個實例相關的操作，請使用標準[託管實例命令](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)，但在在池中為實例使用這些命令時，必須填充*實例池名稱*屬性。

## <a name="how-to-deploy-managed-instances-into-pools"></a>如何將託管實例部署到池中

將實例部署到池的過程包括以下兩個步驟：

1. 一次性實例池部署。 這是一個長時間運行的操作，其中持續時間與部署[在空子網中創建的單個實例](sql-database-managed-instance.md#managed-instance-management-operations)相同。

2. 實例池中的重複實例部署。 實例池參數必須顯式指定為此操作的一部分。 這是一個相對快速的操作，通常需要長達 5 分鐘的時間。

在公共預覽版中，僅支援使用 PowerShell 和資源管理器範本。 Azure 門戶體驗當前不可用。

將託管實例部署到池後，*可以使用*Azure 門戶更改定價層頁上的屬性。


## <a name="create-an-instance-pool"></a>創建實例池

要創建實例池：

1. [使用子網創建虛擬網路](#create-a-virtual-network-with-a-subnet)。
2. [創建實例池](#create-an-instance-pool)。


### <a name="create-a-virtual-network-with-a-subnet"></a>建立具有子網路的虛擬網路 

要將多個實例池放置在同一虛擬網路中，請參閱以下文章：

- [確定 Azure SQL 資料庫託管實例的 VNet 子網大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。
- 使用[Azure 門戶範本](sql-database-managed-instance-create-vnet-subnet.md)創建新的虛擬網路和子網，或按照[準備現有虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)的說明進行操作。
 


### <a name="create-an-instance-pool"></a>創建實例池 

完成上述步驟後，即可創建實例池。

以下限制適用于實例池：

- 公共預覽版中僅提供通用和第 5 代版本。
- 池名稱只能包含小寫、數位和連字號，不能以連字號開頭。
- 如果要使用 AHB（Azure 混合權益），它將在實例池級別應用。 您可以在池創建期間設置許可證類型，或在創建後隨時更新它。

> [!IMPORTANT]
> 部署實例池是一項長時間運行的操作，大約需要 4.5 小時。

要獲取網路參數：

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

要創建實例池：

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
> 由於部署實例池是一項長時間運行的操作，因此您需要等待它完成，然後再運行本文中的任何以下步驟。

## <a name="create-a-managed-instance-inside-the-pool"></a>在池內創建託管實例 

成功部署實例池後，是時候在其中創建實例了。

要創建託管實例，請執行以下命令：

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

在池內部署實例需要幾分鐘時間。 創建第一個實例後，可以創建其他實例：

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>在實例內創建資料庫 

要在池內的託管實例中創建和管理資料庫，請使用單個實例命令。

要在託管實例內創建資料庫，請：

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>獲取實例池使用方式 
 
要獲取池內的實例清單，可以：

```powershell
$instancePool | Get-AzSqlInstance
```


要獲取池資源使用方式，

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


要獲取有關池及其內部實例的詳細使用方式概述，它：

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

要在實例中列出資料庫，可以：

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> 每個池有 100 個資料庫的限制（不是每個實例）。


## <a name="scale-a-managed-instance-inside-a-pool"></a>在池內縮放託管實例 


將託管實例填充到資料庫後，可能會觸及有關存儲或性能的實例限制。 在這種情況下，如果未超過池使用率，則可以縮放實例。
在池內縮放託管實例是一項需要幾分鐘的操作。 縮放的先決條件是在實例池級別提供 vCore 和存儲。

要更新 vCore 的數量和存儲大小，

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


要僅更新存儲大小：

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>連接到池內的託管實例

要連接到池中的託管實例，需要執行以下兩個步驟：

1. [啟用實例的公共終結點](#enable-the-public-endpoint-for-the-instance)。
2. [向網路安全性群組 （NSG） 添加入站規則](#add-an-inbound-rule-to-the-network-security-group)。

完成這兩個步驟後，可以使用實例創建期間提供的公共終結點位址、埠和憑據連接到實例。 

### <a name="enable-the-public-endpoint-for-the-instance"></a>啟用實例的公共終結點

可通過 Azure 門戶或使用以下 PowerShell 命令為實例啟用公共終結點：


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

此參數也可以在實例創建期間設置。

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>向網路安全性群組添加入站規則 

此步驟可以通過 Azure 門戶或使用 PowerShell 命令完成，並且可以隨時在為託管實例準備子網後完成。

有關詳細資訊，請參閱[在網路安全性群組上允許公共終結點流量](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)。


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>在實例池中移動現有單個實例 
 
將實例移出池是公共預覽限制之一。 可以使用的解決方法依賴于將資料庫從池外的實例還原到已在池中的實例的時間點還原。 

兩個實例必須位於同一訂閱和區域中。 當前不支援跨區域和跨訂閱還原。

此過程確實有一段時間的停機時間。

要移動現有資料庫：

1. 暫停要從中遷移的託管實例上的工作負載。
2. 生成腳本以創建系統資料庫，並在實例池內的實例上執行它們。
3. 對每個資料庫從單個實例到池中的實例進行時間點還原。

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

4. 將應用程式指向新實例並恢復其工作負載。

如果有多個資料庫，請為每個資料庫重複此過程。


## <a name="next-steps"></a>後續步驟

- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
- 如需 VNet 組態的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-connectivity-architecture.md)。
- 有關創建託管實例並從備份檔案還原資料庫的快速入門，請參閱[創建託管實例](sql-database-managed-instance-get-started.md)。
- 有關使用 Azure 資料庫移轉服務 （DMS） 進行遷移的教程，請參閱[使用 DMS 進行託管實例遷移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 有關使用內置故障排除智慧對託管實例資料庫性能的高級監視，請參閱[使用 Azure SQL 分析監視 Azure SQL 資料庫](../azure-monitor/insights/azure-sql.md)。
- 有關定價資訊，請參閱[SQL 資料庫託管實例定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
