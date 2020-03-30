---
title: PowerShell 示例 - 列出所有應用程式代理連接器組
description: PowerShell 示例列出了目錄中的所有 Azure 活動目錄 （Azure AD） 應用程式代理連接器組和連接器。
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
ms.openlocfilehash: 53af7f3f9049edc23ed4226f43e7fe67398c29f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482110"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>獲取目錄中的所有連接器組和連接器

此 PowerShell 腳本示例列出了目錄中的所有 Azure 活動目錄 （Azure AD） 應用程式代理連接器組和連接器。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要用於圖形模組 （AzureAD）[的 AzureAD V2 電源外殼](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)，或[用於圖形模組預覽版本的 AzureAD V2 電源外殼](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)（AzureAD 預覽版）。

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>指令碼說明

| Command | 注意 |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | 檢索所有連接器組的清單，或者如果指定，則檢索指定連接器組的詳細資訊。 |
| [Get-AzureADApplicationProxyConnectorGroupMembers](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | 獲取與每個連接器組關聯的所有應用程式代理連接器。|

## <a name="next-steps"></a>後續步驟

有關 Azure AD 電源外殼模組的詳細資訊，請參閱[Azure AD PowerShell 模組概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有關應用程式代理的其他 PowerShell 示例，請參閱[Azure AD 應用程式代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。