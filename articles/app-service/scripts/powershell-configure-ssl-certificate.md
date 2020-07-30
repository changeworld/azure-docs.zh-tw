---
title: PowerShell：上傳和繫結 TLS/SSL
description: 了解如何使用 Azure PowerShell 將 App Service 的部署和管理自動化。 此範例說明如何將自訂 TLS/SSL 憑證繫結至應用程式。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: c0e68627691732e6cbcd08b423199164a69193d4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083339"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>使用 PowerShell 將自訂 TLS/SSL 憑證繫結至 Web 應用程式

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關的資源，然後將自訂網域名稱的 TLS/SSL 憑證加以繫結。 

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。 此外，請確定：

- 已使用 `az login` 命令建立與 Azure 的連線。
- 您可以存取網域註冊機構的 DNS 設定頁面。
- 對於想要上傳並繫結的 TLS/SSL 憑證，您具備有效的 .PFX 檔案和其密碼。

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

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
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | 修改 Web 應用程式的組態。 |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | 建立 Web 應用程式的 TLS/SSL 憑證繫結。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。
