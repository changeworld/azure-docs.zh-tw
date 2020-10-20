---
title: PowerShell 腳本：列出傳送給取用者的 Azure Data Share 邀請
description: 瞭解這個 PowerShell 腳本如何取得傳送給取用者的邀請，並查看您可以使用的腳本範例。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 5f75894094fa2a15dbc9e2809ed6c3631df96c3f
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221208"
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
| [AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | 取得並列出已傳送的資料共用邀請。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。

您可以在 [azure Data Share powershell 範例](../../samples-powershell.md)中找到其他 Azure Data Share powershell 腳本範例。
