---
title: PowerShell：建立排程備份
description: 了解如何使用 Azure PowerShell 將 App Service 的部署和管理自動化。 此範例說明如何建立應用程式的排程備份。
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 24723d442cdc684e109dee3270cdfbc217fd4f4c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80044615"
---
# <a name="create-a-scheduled-backup-for-a-web-app-using-powershell"></a>使用 PowerShell 建立 Web 應用程式的排程備份

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關資源，然後為其建立排程備份。 

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/overview) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。 

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、Web 應用程式和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | 建立儲存體帳戶。 |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | 建立 Azure 儲存體容器。 |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | 產生 Azure 儲存體容器的 SAS 權杖。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 建立 App Service 方案。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 建立 Web 應用程式。 |
| [Edit-AzWebAppBackupConfiguration](/powershell/module/az.websites/edit-azwebappbackupconfiguration) | 編輯 Web 應用程式的備份設定。 |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | 取得 Web 應用程式的備份清單。 |
| [Get-AzWebAppBackupConfiguration](/powershell/module/az.websites/get-azwebappbackupconfiguration) | 取得 Web 應用程式的備份設定。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。
