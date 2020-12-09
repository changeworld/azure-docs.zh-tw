---
title: PowerShell 範例 - 列出應用程式 Proxy 應用程式的使用者和群組
description: PowerShell 範例，其中列出指派給特定 Azure Active Directory (Azure AD) 應用程式 Proxy 應用程式的所有使用者和群組。
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
ms.openlocfilehash: c9885e42dc81d81dddea6205ed85be8276ffbf94
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861705"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>顯示指派給應用程式 Proxy 應用程式的使用者和群組

此 PowerShell 指令碼範例列出指派給特定 Azure Active Directory (Azure AD) 應用程式 Proxy 應用程式的使用者和群組。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此範例需要 [AzureAD V2 PowerShell for Graph 模組](/powershell/azure/active-directory/install-adv2) (AzureAD) 或 [AzureAD V2 PowerShell for Graph 模組預覽版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>指令碼說明

| Command | 注意 |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| 取得使用者。 |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| 取得群組。 |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 取得服務主體。 |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | 取得使用者應用程式角色指派。 |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | 取得群組應用程式角色指派。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure AD PowerShell 模組的詳細資訊，請參閱 [Azure AD PowerShell 模組概觀](/powershell/azure/active-directory/overview)。

如需應用程式 Proxy 的其他 PowerShell 範例，請參閱 [Azure AD 應用程式 Proxy 的 Azure AD PowerShell 範例](../application-proxy-powershell-samples.md)。
