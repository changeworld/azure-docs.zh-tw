---
title: PowerShell 示例 - 具有相同證書的應用程式代理應用
description: PowerShell 示例列出了使用相同證書發佈的所有 Azure 活動目錄 （Azure AD） 應用程式代理應用程式。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef654226d041a7602c03e1ce4670d2f8eea0c2e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482006"
---
# <a name="get-all-azure-ad-proxy-application-apps-that-are-published-with-the-identical-certificate"></a>獲取使用相同證書發佈的所有 Azure AD 代理應用程式應用

此 PowerShell 腳本示例列出了使用相同證書發佈的所有 Azure 活動目錄 （Azure AD） 應用程式代理應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要用於圖形模組 （AzureAD）[的 AzureAD V2 電源外殼](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)，或[用於圖形模組預覽版本的 AzureAD V2 電源外殼](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)（AzureAD 預覽版）。

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>指令碼說明

| Command | 注意 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 獲取服務主體。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | 獲取 Azure AD 應用程式。 |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | 檢索為 Azure AD 中的應用程式代理配置的應用程式。 |

## <a name="next-steps"></a>後續步驟

有關 Azure AD 電源外殼模組的詳細資訊，請參閱[Azure AD PowerShell 模組概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有關應用程式代理的其他 PowerShell 示例，請參閱[Azure AD 應用程式代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。