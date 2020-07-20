---
title: PowerShell 腳本：建立新的 Azure 資料共用帳戶 |Microsoft Docs
description: 此 PowerShell 腳本會建立新的資料共用帳戶。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "70307277"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>使用 PowerShell 在 Azure 中建立資料共用帳戶

此 PowerShell 腳本會建立新的資料共用帳戶。 

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
| [新增-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | 建立資料共用帳戶。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在[Azure 資料共用 powershell 範例](../../samples-powershell.md)中找到其他 Azure 資料共用 powershell 腳本範例。
