---
title: 用於 Azure AD 應用程式代理的 PowerShell 示例
description: 使用這些適用于 Azure AD 應用程式代理的 PowerShell 示例來獲取有關目錄中的應用程式代理應用和連接器的資訊，將使用者和組分配給應用，以及獲取證書資訊。
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
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481257"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD 電源外殼示例，用於 Azure AD 應用程式代理

下表包括指向 Azure AD 應用程式代理的 PowerShell 腳本示例的連結。 這些示例需要[用於圖形模組的 AzureAD V2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或[用於圖形模組預覽版本的 AzureAD V2 PowerShell，](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)除非另有說明。


有關這些示例中使用的 Cmdlet 的詳細資訊，請參閱[應用程式代理應用程式管理和](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)[應用程式代理連接器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)。

| | |
|---|---|
|**應用程式代理應用**||
| [列出所有應用程式代理應用的基本資訊](scripts/powershell-get-all-app-proxy-apps-basic.md) | 列出有關目錄中所有應用程式代理應用的基本資訊（AppId、顯示名稱、ObjId）。 |
| [列出所有應用程式代理應用的擴展資訊](scripts/powershell-get-all-app-proxy-apps-extended.md) | 列出有關目錄中所有應用程式代理應用的擴展資訊（AppId、顯示名稱、外部 Url、內部 Url、外部身份驗證類型）。  |
| [按連接器組列出所有應用程式代理應用](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | 列出有關目錄中所有應用程式代理應用以及應用分配給哪個連接器的資訊。 |
| [使用權杖存留期策略獲取所有應用程式代理應用](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | 列出目錄中包含權杖存留期策略的所有應用程式代理應用及其詳細資訊。 此示例需要[AzureAD V2 PowerShell 用於圖形模組預覽版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)。 |
|**連接器群組**||
| [獲取目錄中的所有連接器組和連接器](scripts/powershell-get-all-connectors.md) | 列出目錄中的所有連接器組和連接器。 |
| [將分配給連接器組的所有應用移動到其他連接器組](scripts/powershell-move-all-apps-to-connector-group.md) | 將當前分配給連接器組的所有應用程式移動到其他連接器組。 |
|**分配的使用者和組**||
| [顯示分配給應用程式代理應用程式的使用者和組](scripts/powershell-display-users-group-of-app.md) | 列出分配給特定應用程式代理應用程式的使用者和組。 |
| [將使用者分配給應用程式](scripts/powershell-assign-user-to-app.md) | 將特定使用者分配給應用程式。 |
| [將組分配給應用程式](scripts/powershell-assign-group-to-app.md) | 將特定組分配給應用程式。 |
|**外部 URL 配置**||
| [使用預設域獲取所有應用程式代理應用 （.msappproxy.net）](scripts/powershell-get-all-default-domain-apps.md)  | 使用預設域 （.msappproxy.net） 列出所有應用程式代理應用程式。 |
| [使用萬用字元發佈獲取所有應用程式代理應用](scripts/powershell-get-all-wildcard-apps.md) | 使用萬用字元發佈列出所有應用程式代理應用。 |
|**自訂域配置**||
| [使用自訂域和證書資訊獲取所有應用程式代理應用](scripts/powershell-get-all-custom-domains-and-certs.md) | 列出使用自訂域的所有應用程式代理應用以及與自訂域關聯的證書資訊。 |
| [獲取發佈的所有 Azure AD 代理應用程式應用程式，無需上載證書](scripts/powershell-get-all-custom-domain-no-cert.md) | 列出使用自訂域但未上載有效 TLS/SSL 憑證的所有應用程式代理應用。 |
| [使用相同證書發佈所有 Azure AD 代理應用程式應用程式](scripts/powershell-get-custom-domain-identical-cert.md) | 列出使用相同證書發佈的所有 Azure AD 代理應用程式。 |
| [獲取使用相同證書發佈的所有 Azure AD 代理應用程式應用程式並替換它](scripts/powershell-get-custom-domain-replace-cert.md) | 對於使用相同證書發佈的 Azure AD 代理應用程式應用，允許您批量替換證書。 |
