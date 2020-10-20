---
title: PowerShell 腳本：建立 Azure Data Share 邀請
description: 此 PowerShell 腳本會傳送資料共用邀請。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 5f65d081e724206c1c64ad08189d1b620bbb4f2c
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221327"
---
# <a name="use-a-powershell-script-to-monitor-the-usage-of-a-sent-data-share"></a>使用 PowerShell 腳本來監視已傳送資料共用的使用量

此 PowerShell 腳本會建立資料共用邀請。

## <a name="sample-script"></a>範例指令碼


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [新 AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation) | 建立資料共用邀請。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。

您可以在 [azure Data Share powershell 範例](../../samples-powershell.md)中找到其他 Azure Data Share powershell 腳本範例。
