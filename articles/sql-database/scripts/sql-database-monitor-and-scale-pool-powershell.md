---
title: PowerShell 範例 - 監視/調整彈性集區 - Azure SQL Database
description: 可在 Azure SQL Database 中監視和調整彈性集區的 Azure PowerShell 範例指令碼
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 6ab361b67741f2b96f593d04dafbabe355fc9121
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691615"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>使用 PowerShell 在 Azure SQL Database 中監視和調整彈性集區

此 PowerShell 指令碼範例會監視彈性集區的效能計量、將其調整為較高計算大小，並對其中一個效能計量建立警示規則。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果選擇在本機安裝和使用 PowerShell，此教學課程需要 AZ PowerShell 1.4.0 或更新版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale a single SQL Database")]

## <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 建立裝載單一資料庫或彈性集區的 SQL Database 伺服器。 |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 建立彈性集區。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 建立單一資料庫或彈性集區中的資料庫。 |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | 顯示資料庫的大小使用量資訊。|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | 新增或更新以度量為基礎的警示規則。 |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | 更新彈性集區屬性 |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | 設定警示規則以自動在日後監視 DTU。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。
