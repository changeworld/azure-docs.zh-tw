---
title: PowerShell 範例 - 使用相同憑證的應用程式 Proxy 應用程式
description: PowerShell 範例，其中列出所有使用相同憑證發佈的 Azure Active Directory (Azure AD) 應用程式 Proxy 應用程式。
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
ms.openlocfilehash: 39a116cdb8900c5adb1689c6b81649b1963e4fe6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511128"
---
# <a name="get-all-azure-ad-proxy-application-apps-that-are-published-with-the-identical-certificate"></a>取得以相同憑證發佈的所有 Azure AD Proxy 應用程式

此 PowerShell 指令碼範例列出所有使用相同憑證發佈的 Azure Active Directory (Azure AD) 應用程式 Proxy 應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此範例需要 [AzureAD V2 PowerShell for Graph 模組](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) 或 [AzureAD V2 PowerShell for Graph 模組預覽版](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>指令碼說明

| Command | 注意 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 取得服務主體。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | 取得 Azure AD 應用程式。 |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | 在 Azure AD 中，取出為應用程式 Proxy 設定的應用程式。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure AD PowerShell 模組的詳細資訊，請參閱 [Azure AD PowerShell 模組概觀](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

如需應用程式 Proxy 的其他 PowerShell 範例，請參閱 [Azure AD 應用程式 Proxy 的 Azure AD PowerShell 範例](../application-proxy-powershell-samples.md)。