---
title: PowerShell：使用 Web 伺服器記錄進行監視
description: 了解如何使用 Azure PowerShell 將 App Service 的部署和管理自動化。 此範例說明如何使用 Web 伺服器記錄監視應用程式。
tags: azure-service-management
ms.assetid: 5805d7cd-9e56-4eba-bd85-75b013690ff5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 8a3af13060d932b1a39a85bbc631732f8f859dfb
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070270"
---
# <a name="monitor-a-web-appwith-web-server-logs"></a>使用 Web 伺服器記錄監視 Web 應用程式

在此案例中，您會建立資源群組、App Service 方案、Web 應用程式，並設定 Web 應用程式來啟用 Web 伺服器記錄。 然後，您會下載記錄檔來進行檢閱。

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1 "Monitor a web app with web server logs")]

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
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | 修改 Web 應用程式的組態。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。
