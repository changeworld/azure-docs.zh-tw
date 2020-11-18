---
title: PowerShell 範例 - 將應用程式 Proxy 應用程式移至另一個群組
description: Azure Active Directory (Azure AD) 應用程式 Proxy PowerShell 範例，用來將目前指派給連接器群組的所有應用程式移至不同的連接器群組。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 24bbb0d6133ecf95debb97b673b984f18481803e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653873"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>將所有指派給連接器群組的應用程式移至另一個連接器群組

此 PowerShell 指令碼範例會將目前指派給連接器群組的所有 Azure Active Directory (Azure AD) 應用程式 Proxy 應用程式移至不同的連接器群組。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此範例需要 [AzureAD V2 PowerShell for Graph 模組](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) 或 [AzureAD V2 PowerShell for Graph 模組預覽版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>指令碼說明

| Command | 注意 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 取得服務主體。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | 取得 Azure AD 應用程式。 |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | 取出所有連接器群組的清單，如有指定，則取出指定連接器群組的詳細資料。 |
| [Set-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup?view=azureadps-2.0) | 將指定的連接器群組指派給指定的應用程式。|

## <a name="next-steps"></a>後續步驟

如需有關 Azure AD PowerShell 模組的詳細資訊，請參閱 [Azure AD PowerShell 模組概觀](/powershell/azure/active-directory/overview?view=azureadps-2.0)。

如需應用程式 Proxy 的其他 PowerShell 範例，請參閱 [Azure AD 應用程式 Proxy 的 Azure AD PowerShell 範例](../application-proxy-powershell-samples.md)。