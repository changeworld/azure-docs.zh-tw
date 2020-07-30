---
title: PowerShell：將程式碼部署至預備位置
description: 了解如何使用 Azure PowerShell 將 App Service 的部署和管理自動化。 此範例說明如何將程式碼部署至預備環境。
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: ff686c4ffeaefe58a277e486f0960491f6401ef8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87068063"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>建立 Web 應用程式並將程式碼部署至預備環境

此範例指令碼會在 App Service 中建立 Web 應用程式以及稱為「預備」的其他部署位置，然後將範例應用程式部署至「預備」位置。

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

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
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | 修改 App Service 方案來變更其定價層。 |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | 建立 Web 應用程式的部署位置。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | 修改資源群組中的資源。 |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | 將 Web 應用程式的部署位置切換到生產環境。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。
