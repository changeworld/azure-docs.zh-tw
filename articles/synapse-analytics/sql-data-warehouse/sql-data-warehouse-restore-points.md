---
title: 使用者定義的還原點
description: 如何建立 SQL 集區的還原點。
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
ms.openlocfilehash: 66a2dad9396e8bf7c8ef49db529f7a5486cc8a8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87089202"
---
# <a name="user-defined-restore-points"></a>使用者定義的還原點

在本文中，您將瞭解如何使用 PowerShell 和 Azure 入口網站，在 Azure Synapse Analytics 中為 SQL 集區建立新的使用者定義還原點。

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

2. 流覽至您想要為其建立還原點的 SQL 集區。

3. 從左窗格選取 **[總覽** ]，然後選取 [ **+ 新增還原點**]。 如果 [新的還原點] 按鈕未啟用，請確定 SQL 集區未暫停。

    ![新增還原點](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 指定使用者定義的還原點名稱， **然後按一下 [** 套用]。 使用者定義還原點的預設保留期間為七天。

    ![還原點名稱](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>接下來的步驟

- [還原現有的 SQL 集區](sql-data-warehouse-restore-active-paused-dw.md)
- [還原已刪除的 SQL 集區](sql-data-warehouse-restore-deleted-dw.md)
- [從異地備份 SQL 集區還原](sql-data-warehouse-restore-from-geo-backup.md)

