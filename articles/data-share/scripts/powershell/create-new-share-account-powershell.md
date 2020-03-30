---
title: PowerShell 腳本：創建新的 Azure 資料共用帳戶 |微軟文檔
description: 此 PowerShell 腳本創建新的資料共用帳戶。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307277"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>使用 PowerShell 在 Azure 中創建資料共用帳戶

此 PowerShell 腳本創建新的資料共用帳戶。 

## <a name="sample-script"></a>範例指令碼

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [新-阿茲資料共用帳戶](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | 創建資料共用帳戶。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

其他 Azure 資料共用 PowerShell 腳本示例可在[Azure 資料共用 PowerShell 示例中](../../samples-powershell.md)找到。
