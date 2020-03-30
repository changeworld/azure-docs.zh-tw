---
title: PowerShell 腳本：設置和查看 Azure 資料共用同步設置*微軟文檔
description: 此 PowerShell 腳本設置並獲取共用同步設置。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: b8d01a383e816bfc73413d21e9cc8d51d326ab14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307191"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>使用 PowerShell 監視已發送資料共用的使用方式

此 PowerShell 腳本設置並獲取共用同步設置。

## <a name="sample-script"></a>範例指令碼


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [新資料共用同步設置](/powershell/module/az.datashare/new-azdatasharesynchronizationsetting?view=azps-2.6.0) | 創建共用同步。 |
| [獲取阿茲資料共用同步設置](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | 獲取共用同步的同步設置。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

其他 Azure 資料共用 PowerShell 腳本示例可在[Azure 資料共用 PowerShell 示例中](../../samples-powershell.md)找到。
