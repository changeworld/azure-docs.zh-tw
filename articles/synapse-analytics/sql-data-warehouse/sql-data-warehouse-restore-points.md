---
title: 使用者定義的還原點
description: 如何為 SQL 池創建還原點。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5c9b7eb1b03b6b6e3721c13f9ebf7da25dd2e376
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745069"
---
# <a name="user-defined-restore-points"></a>使用者定義的還原點

在本文中,您將學習如何使用 PowerShell 和 Azure 門戶在 Azure Synapse 分析中為 SQL 池創建新的使用者定義的還原點。

## <a name="create-user-defined-restore-points-through-powershell"></a>以 PowerShell 建立使用者定義的還原點

要建立使用者定義的還原點,請使用[New-AzSql 資料庫還原點](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)PowerShell cmdlet。

1. 開始之前,請確保安裝 Azure [PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 開啟 PowerShell。
3. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
4. 選取包含要還原之資料庫的訂用帳戶。
5. 為數據倉庫的即時副本創建還原點。

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

6. 請參閱所有現有還原點的清單。

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>透過 Azure 門戶建立使用者定義的還原點

也可以通過 Azure 門戶創建使用者定義的還原點。

1. 登錄到 Azure[門戶](https://portal.azure.com/)帳戶。

2. 導航到要為其創建還原點的 SQL 池。

3. 從左邊窗格中選擇 **「概覽**」,選擇 **「新建還原點**」。。 如果未啟用"新建還原點"按鈕,請確保 SQL 池未暫停。

    ![新增還原點](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 為使用者定義的還原點指定名稱,然後單擊"**應用**"。 用戶定義的還原點預設保留期為 7 天。

    ![還原點名稱](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>後續步驟

- [回復的 SQL 池](sql-data-warehouse-restore-active-paused-dw.md)
- [回復已移除的 SQL 池](sql-data-warehouse-restore-deleted-dw.md)
- [從異地備份 SQL 池還原](sql-data-warehouse-restore-from-geo-backup.md)

