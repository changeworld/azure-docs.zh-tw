---
title: PowerShell 腳本：接受來自 Azure 資料共用的邀請 |微軟文檔
description: 此 PowerShell 腳本接受來自現有資料共用的邀請。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 246e3550650dfd458b4aeecda3b5b7733e49f017
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307338"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>使用 PowerShell 接受資料共用邀請

此 PowerShell 腳本接受發送給消費者的邀請。

## <a name="sample-script"></a>範例指令碼
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [獲取阿茲特資料共用邀請](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | 獲取並列出發送的資料共用邀請。 |
| [新-阿茲資料共用訂閱](/powershell/module/az.datashare/get-azdatasharesubscription?view=azps-2.6.0) | 創建資料共用訂閱。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

其他 Azure 資料共用 PowerShell 腳本示例可在[Azure 資料共用 PowerShell 示例中](../../samples-powershell.md)找到。

