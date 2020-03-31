---
title: 調整 Synapse SQL 集區的計算 (Azure PowerShell)
description: 您可以使用 Azure PowerShell 來調整 Synapse SQL 集區 (資料倉儲) 的計算。
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c7ec8db212a24f1f23f393e4cb0e7f4150605a56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350784"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 調整 Synapse SQL 集區的計算

您可以使用 Azure PowerShell 來調整 Synapse SQL 集區 (資料倉儲) 的計算。 [擴增計算](sql-data-warehouse-manage-compute-overview.md)以提升效能，或將計算調整回來以節省成本。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本快速入門假設您已有可調整的 SQL 集區。 若您需要建立 SQL 集區，請使用[建立與連線 - 入口網站](create-data-warehouse-portal.md)建立名為 **mySampleDataWarehouse** 的 SQL 集區。

## <a name="log-in-to-azure"></a>登入 Azure

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Connect-AzAccount
```

若要查看您正在使用的訂用帳戶，請執行 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)。

```powershell
Get-AzSubscription
```

如果需要使用不同於預設值的訂用帳戶，請執行 [Set-AzContext](/powershell/module/az.accounts/set-azcontext)。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>查詢資料倉儲資訊

找出您計畫暫停與繼續之資料倉儲的資料庫名稱、伺服器名稱和資源群組。

遵循下列步驟來尋找您資料倉儲的位置資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下 Azure 入口網站左側導覽頁面中的 [Azure Synapse Analytics (先前為 SQL DW)]  。
3. 從 [Azure Synapse Analytics (先前為 SQL DW)]  頁面中選取 [mySampleDataWarehouse]  ，以開啟資料倉儲。

    ![伺服器名稱和資源群組](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. 記下資料倉儲名稱，這將作為資料庫名稱使用。 請記住，資料倉儲是一種資料庫。 也請記下伺服器名稱與資源群組。 您會在暫停和繼續命令中使用伺服器名稱與資源群組名稱。
5. 在 PowerShell Cmdlet 中，請使用伺服器名稱的第一個部分即可。 在上圖中，完整伺服器名稱是 sqlpoolservername.database.windows.net。 在 PowerShell Cmdlet 中，我們會使用 **sqlpoolservername** 作為伺服器名稱。

## <a name="scale-compute"></a>調整計算

在 SQL 集區中，您可以藉由調整資料倉儲單位來增加或減少計算資源。 [建立與連線 - 入口網站](create-data-warehouse-portal.md)已建立 **mySampleDataWarehouse**，並以 400 DWU 加以初始化。 下列步驟會調整 **mySampleDataWarehouse** 的 DWU。

若要變更資料倉儲單位，請使用 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell Cmdlet。 下列範例將資料庫 **mySampleDataWarehouse** 的資料倉儲單位設定為 DW300c，此資料庫裝載於伺服器 **sqlpoolservername** 上的資源群組 **resourcegroupname** 中。

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>檢查資料倉儲狀態

若要查看資料倉儲的目前狀態，請使用 [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) PowerShell Cmdlet。 此 Cmdlet 會取得資源群組 **resourcegroupname** 和伺服器 **sqlpoolservername.database.windows.net** 中的 **mySampleDataWarehouse** 資料庫的狀態。

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

這會導致如以下的結果：

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

您可以在輸出中查看資料庫的 **Status**。 在此情況下，您會看到此資料庫已上線。  當您執行此命令時，應該會收到下列其中一個 Status (狀態) 值：Online (上線)、Pausing (暫停中)、Resuming (正在恢復)、Scaling (正在調整) 或 Paused (已暫停)。

若要單獨查看狀態，請使用下列命令：

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>後續步驟
您現在已了解如何調整 SQL 集區的計算。 若要深入了解 SQL 集區，請繼續進行載入資料的教學課程。

> [!div class="nextstepaction"]
>[將資料載入至 SQL 集區](load-data-from-azure-blob-storage-using-polybase.md)
