---
title: PowerShell：從 GitHub 持續部署
description: 了解如何使用 Azure PowerShell 將 App Service 的部署和管理自動化。 此範例說明如何從 GitHub 建立可進行 CI/CD 的應用程式。
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: e3193180165ec63f3716a9b875a8244ec7288eb8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083288"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>建立可從 GitHub 連續部署的 Web 應用程式

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關資源，然後設定從 GitHub 存放庫進行的[持續部署](../deploy-continuous-deployment.md)。 如需進行沒有連續部署的 GitHub 部署，請參閱[建立 Web 應用程式並從 GitHub 部署程式碼](powershell-deploy-github.md)。

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。 此外，請確定：

- 應用程式的程式碼位於您擁有的公用或私人 GitHub 存放庫。 若要取得自動組建，請根據[準備存放庫](../deploy-continuous-deployment.md#prepare-your-repository)資料表來結構化您的存放庫。
- 您已[在 GitHub 帳戶中建立個人存取權杖](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)。

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

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
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 建立 App Service 方案。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 建立 Web 應用程式。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | 修改資源群組中的資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。
