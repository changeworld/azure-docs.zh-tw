---
title: PowerShell 範例 - 作用中異地複寫 - 單一 Azure SQL Database
description: 為 Azure SQL Database 中的單一資料庫設定作用中異地複寫並且進行容錯移轉的 Azure PowerShell 範例指令碼。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: ddaf9de7fdb09f8c93815c918edac010555bb4d8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691518"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-single-database-in-azure-sql-database"></a>使用 PowerShell 為 Azure SQL Database 中的單一資料庫設定作用中異地複寫

此 PowerShell 指令碼範例為單一資料庫設定作用中異地複寫，並將其容錯移轉到資料庫的次要複本。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果選擇在本機安裝和使用 PowerShell，此教學課程需要 AZ PowerShell 1.4.0 或更新版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="sample-scripts"></a>範例指令碼

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-single-database.ps1?highlight=18-21 "Set up active geo-replication for single database")]

## <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 建立裝載單一資料庫和彈性集區的 SQL Database 伺服器。 |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 建立彈性集區。 |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 更新資料庫屬性，或將資料庫移入、移出彈性集區或在彈性集區之間移動資料庫。 |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| 針對現有資料庫建立次要資料庫並開始資料複寫。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| 取得一或多個資料庫。 |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| 將次要資料庫切換為主要資料庫以開始容錯移轉。|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | 取得 Azure SQL Database 和資源群組或 SQL Server 之間的異地複寫連結。 |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) | 終止 SQL Database 和指定次要資料庫間的資料複寫。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。
