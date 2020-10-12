---
title: PowerShell 腳本：列出傳送給取用者的 Azure Data Share 邀請 |Microsoft Docs
description: 瞭解這個 PowerShell 腳本如何取得傳送給取用者的邀請，並查看您可以使用的腳本範例。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 3f0374ba5b98c145359d18143584e23fc543c552
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985615"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>使用 PowerShell 取得 data share 邀請

此 PowerShell 腳本會取得傳送給取用者的邀請。

## <a name="sample-script"></a>範例指令碼
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | 取得並列出已傳送的資料共用邀請。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [azure Data Share powershell 範例](../../samples-powershell.md)中找到其他 Azure Data Share powershell 腳本範例。
