---
title: PowerShell 範例 - 列出應用程式 Proxy 應用程式的擴充資訊
description: PowerShell 範例，列出所有 Azure Active Directory (Azure AD) 應用程式 Proxy 應用程式以及應用程式識別碼 (AppId)、名稱 (DisplayName)、外部 URL (ExternalUrl)、內部 URL (InternalUrl) 及驗證類型 (ExternalAuthenticationType)。
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
ms.openlocfilehash: cd1120874ca9ee0d3ef65417d6a200ffd0a40fdb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659109"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>取得所有應用程式 Proxy 應用程式及列出擴充資訊

此 PowerShell 指令碼範例列出所有 Azure Active Directory (Azure AD) 應用程式 Proxy 應用程式的相關資訊，包括應用程式識別碼 (AppId)、名稱 (DisplayName)、外部 URL (ExternalUrl)、內部 URL (InternalUrl) 及驗證類型 (ExternalAuthenticationType)。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此範例需要 [AzureAD V2 PowerShell for Graph 模組](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) 或 [AzureAD V2 PowerShell for Graph 模組預覽版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>指令碼說明

| Command | 注意 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 取得服務主體。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | 取得 Azure AD 應用程式。 |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | 在 Azure AD 中，取出為應用程式 Proxy 設定的應用程式。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure AD PowerShell 模組的詳細資訊，請參閱 [Azure AD PowerShell 模組概觀](/powershell/azure/active-directory/overview?view=azureadps-2.0)。

如需應用程式 Proxy 的其他 PowerShell 範例，請參閱 [Azure AD 應用程式 Proxy 的 Azure AD PowerShell 範例](../application-proxy-powershell-samples.md)。