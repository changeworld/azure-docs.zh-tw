---
title: PowerShell 範例 - 移動 Azure SQL 資料庫 - 彈性集區
description: 使用 PowerShell 在彈性集區之間移動 SQL 資料庫的 Azure PowerShell 指令碼範例
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.reviewer: ''
ms.author: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 1f4465201d93f5bd48bda82b1d3b7e1ebcdc950e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691577"
---
# <a name="use-powershell-to-create-elastic-pools-and-move-databases-between-elastic-pools"></a>使用 PowerShell 建立彈性集區並在彈性集區之間移動資料庫

此 PowerShell 指令碼範例會建立兩個彈性集區，並將資料庫從其中一個彈性集區移到另一個彈性集區，然後將資料庫從彈性集區移出到單一資料庫計算大小。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果選擇在本機安裝和使用 PowerShell，此教學課程需要 AZ PowerShell 1.4.0 或更新版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/move-database-between-pools-and-standalone/move-database-between-pools-and-standalone.ps1?highlight=18-19 "Move database between pools")]

## <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 建立裝載單一資料庫或彈性集區的 SQL Database 伺服器。 |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 建立彈性集區。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 在 SQL Database 伺服器中將資料庫建立為獨立或集區資料庫。 |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 更新資料庫屬性，或將資料庫移入、移出彈性集區或在彈性集區之間移動資料庫。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。
