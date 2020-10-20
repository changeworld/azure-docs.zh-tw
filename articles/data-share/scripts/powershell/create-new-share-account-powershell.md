---
title: PowerShell 腳本：建立新的 Azure Data Share 帳戶
description: 此 PowerShell 腳本會建立新的 Data Share 帳戶。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 4608afc16398a4cd1fa47ee1ae2cd4a4489a3f96
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221361"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>使用 PowerShell 在 Azure 中建立資料共用帳戶

此 PowerShell 腳本會建立新的 Data Share 帳戶。 

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
| [新 AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount) | 建立資料共用帳戶。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。

您可以在 [azure Data Share powershell 範例](../../samples-powershell.md)中找到其他 Azure Data Share powershell 腳本範例。
