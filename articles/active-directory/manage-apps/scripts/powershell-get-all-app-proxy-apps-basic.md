---
title: PowerShell 範例-列出應用程式 Proxy 應用程式的基本資訊
description: PowerShell 範例，其中會列出 Azure Active Directory （Azure AD）應用程式 Proxy 應用程式以及應用程式識別碼（AppId）、名稱（DisplayName）和物件識別碼（ObjId）。
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
ms.openlocfilehash: 009aabb8b742a752a29d775efbd11b153588c09b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75482214"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>取得所有應用程式 Proxy 應用程式並列出基本資訊

此 PowerShell 腳本範例會列出所有 Azure Active Directory （Azure AD）應用程式 Proxy 應用程式的相關資訊，包括應用程式識別碼（AppId）、名稱（DisplayName）和物件識別碼（ObjId）。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此範例需要[AzureAD V2 powershell For graph 模組](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)（AzureAD）或[AzureAD V2 powershell for graph 模組預覽版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)（AzureADPreview）。
## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>指令碼說明

| Command | 注意 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 取得服務主體。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure AD PowerShell 模組的詳細資訊，請參閱[Azure AD powershell 模組總覽](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

如需應用程式 Proxy 的其他 PowerShell 範例，請參閱[Azure AD Azure AD 應用程式 proxy 的 powershell 範例](../application-proxy-powershell-samples.md)。