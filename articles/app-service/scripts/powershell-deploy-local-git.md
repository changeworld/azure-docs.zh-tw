---
title: PowerShell：從本機 Git 存放庫部署
description: 了解如何使用 Azure PowerShell 將 App Service 的部署和管理自動化。 此範例說明如何從本機 Git 存放庫部署程式碼。
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 03d2f2d3928393f8a251640a03d82b5963140141
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070287"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>建立 Web 應用程式並從本機 Git 存放庫部署程式碼

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關資源，然後從本機 Git 存放庫中部署 Web 應用程式程式碼。

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/) 中的指示以更新為最新的 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。 此外，應用程式程式碼必須認可到本機 Git 儲存機制。

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、Web 應用程式和所有相關資源。

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 建立具有必要資源群組和 App Service 群組的 Web 應用程式。 如果目前的目錄包含 Git 存放庫，也要新增 `azure` 遠端。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。
