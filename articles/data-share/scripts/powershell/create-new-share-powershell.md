---
title: PowerShell 腳本：建立新的 Azure Data Share
description: 此 PowerShell 腳本會在現有的 Data Share 帳戶內建立新的資料共用。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 36c1bdfef2afe4c34796a804784317a5e7fe12ff
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221344"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>使用 PowerShell 在 Azure 中建立 Data Share

此 PowerShell 腳本會在現有的 Data Share 帳戶內建立新的 Data Share。

## <a name="sample-script"></a>範例指令碼

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [新 AzDataShare](/powershell/module/az.datashare/new-azdatashare) | 建立資料共用。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。

您可以在 [azure Data Share powershell 範例](../../samples-powershell.md)中找到其他 Azure Data Share powershell 腳本範例。
