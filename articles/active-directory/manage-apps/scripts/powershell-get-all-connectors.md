---
title: PowerShell 範例 - 列出所有應用程式 Proxy 連接器群組
description: PowerShell 範例，其中列出您的目錄中的所有 Azure Active Directory (Azure AD) 應用程式 Proxy 連接器群組和連接器。
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
ms.openlocfilehash: 544bdf1c4f9e000c79241418f5e6adefbc1b6121
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858615"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>取得目錄中所有的連接器群組和連接器

此 PowerShell 指令碼範例列出您的目錄中的所有 Azure Active Directory (Azure AD) 應用程式 Proxy 連接器群組和連接器。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此範例需要 [AzureAD V2 PowerShell for Graph 模組](/powershell/azure/active-directory/install-adv2) (AzureAD) 或 [AzureAD V2 PowerShell for Graph 模組預覽版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>指令碼說明

| Command | 注意 |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | 取出所有連接器群組的清單，如有指定，則取出指定連接器群組的詳細資料。 |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | 取得與每個連接器群組相關聯的所有應用程式 Proxy 連接器。|

## <a name="next-steps"></a>後續步驟

如需有關 Azure AD PowerShell 模組的詳細資訊，請參閱 [Azure AD PowerShell 模組概觀](/powershell/azure/active-directory/overview)。

如需應用程式 Proxy 的其他 PowerShell 範例，請參閱 [Azure AD 應用程式 Proxy 的 Azure AD PowerShell 範例](../application-proxy-powershell-samples.md)。
