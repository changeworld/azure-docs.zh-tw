---
title: 使用者定義的還原點
description: 如何建立專用 SQL 集區的還原點。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9d7266e0b84ae57682ddcfe7195be9574a702c74
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313243"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中專用 SQL 集區的使用者定義還原點

在本文中，您將瞭解如何使用 PowerShell 和 Azure 入口網站，在 Azure Synapse Analytics 中為專用的 SQL 集區建立新的使用者定義還原點。

## <a name="create-user-defined-restore-points-through-powershell"></a>透過 PowerShell 建立使用者定義的還原點

若要建立使用者定義的還原點，請使用 [AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell Cmdlet。

1. 開始之前，請務必 [安裝 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 開啟 PowerShell。
3. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
4. 選取包含要還原之資料庫的訂用帳戶。
5. 建立資料倉儲立即複本的還原點。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. 查看所有現有還原點的清單。

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>透過 Azure 入口網站建立使用者定義的還原點

您也可以透過 Azure 入口網站建立使用者定義的還原點。

1. 登入您的 [Azure 入口網站](https://portal.azure.com/) 帳戶。

2. 流覽至您想要為其建立還原點的專用 SQL 集區。

3. 從左窗格選取 **[總覽** ]，然後選取 [ **+ 新增還原點** ]。 如果 [新的還原點] 按鈕未啟用，請確定未暫停專用的 SQL 集區。

    ![新增還原點](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 指定使用者定義的還原點名稱， **然後按一下 [** 套用]。 使用者定義還原點的預設保留期間為七天。

    ![還原點名稱](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>後續步驟

- [還原現有的專用 SQL 集區](sql-data-warehouse-restore-active-paused-dw.md)
- [還原已刪除的專用 SQL 集區](sql-data-warehouse-restore-deleted-dw.md)
- [從異地備份專用 SQL 集區還原](sql-data-warehouse-restore-from-geo-backup.md)

