---
title: PowerShell：刪除應用程式備份
description: 了解如何使用 Azure PowerShell 將 App Service 的部署和管理自動化。 此範例說明如何刪除應用程式備份。
author: msangapu-msft
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: aa0274dcfeff9a36ab58c9f6e19631eb711742bd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086926"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>使用 Azure PowerShell 來刪除 Web 備份

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關資源，然後為其建立一次性的備份。 

若要執行此指令碼，您需要 Web 應用程式的現有備份。 若要建立一個，請參閱[ Web 應用程式](powershell-backup-onetime.md)或[建立 Web 應用程式的排程備份](powershell-backup-scheduled.md)。

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、Web 應用程式和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | 取得 Web 應用程式的備份清單。 |
| [Remove-AzWebAppBackup](/powershell/module/az.websites/remove-azwebappbackup) | 移除指定的 Web 應用程式備份。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。
