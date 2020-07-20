---
title: PowerShell 腳本：列出 Azure 資料共用中的現有共用 |Microsoft Docs
description: 此 PowerShell 腳本會列出並顯示共用的詳細資料。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "70307128"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>使用 PowerShell 來查看已傳送資料共用的詳細資料

此 PowerShell 腳本會列出現有帳戶的資料共用，並取得特定共用的詳細資料。


## <a name="sample-script"></a>範例指令碼

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | 取得和帳戶中的共用清單。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在[Azure 資料共用 powershell 範例](../../samples-powershell.md)中找到其他 Azure 資料共用 powershell 腳本範例。
