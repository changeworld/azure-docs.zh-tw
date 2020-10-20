---
title: PowerShell 腳本：監視 Azure Data Share 的使用量
description: 此 PowerShell 腳本會抓取已傳送資料共用的使用計量。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: e9ff7a29cba9b8e9ca058bfe742f484c5b495cd7
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221310"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>使用 PowerShell 來監視已傳送資料共用的使用量

此 PowerShell 腳本會列出已傳送資料共用的同步處理，並取得特定同步處理的詳細資料，藉此監視資料使用量。

## <a name="sample-script"></a>範例指令碼


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [AzDataShareSynchronization](/powershell/module/az.datashare/get-azdatasharesynchronization) | 列出共用上的同步處理。 |
| [AzDataShareSynchronizationDetails](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail) | 取得共用同步處理的同步處理詳細資料。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。

您可以在 [azure Data Share powershell 範例](../../samples-powershell.md)中找到其他 Azure Data Share powershell 腳本範例。
