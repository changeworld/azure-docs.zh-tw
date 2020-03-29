---
title: PowerShell 示例 - 將使用者分配給應用程式代理應用
description: PowerShell 示例，用於將使用者分配給 Azure 活動目錄 （Azure AD） 應用程式代理應用程式。
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
ms.openlocfilehash: 42667ebdfc37b679d56421e3a82d4a6aaaed8fdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483332"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>將使用者分配給特定的 Azure AD 應用程式代理應用程式

此 PowerShell 腳本示例允許您將使用者分配給特定的 Azure AD 應用程式代理應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要用於圖形模組 （AzureAD）[的 AzureAD V2 電源外殼](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)，或[用於圖形模組預覽版本的 AzureAD V2 電源外殼](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)（AzureAD 預覽版）。

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>指令碼說明

| Command | 注意 |
|---|---|
| [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | 將使用者分配到應用程式角色。 |

## <a name="next-steps"></a>後續步驟

有關 Azure AD 電源外殼模組的詳細資訊，請參閱[Azure AD PowerShell 模組概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有關應用程式代理的其他 PowerShell 示例，請參閱[Azure AD 應用程式代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。