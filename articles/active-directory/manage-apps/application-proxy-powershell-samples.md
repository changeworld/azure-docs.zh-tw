---
title: 適用於Azure AD 應用程式 Proxy 的 PowerShell 範例
description: 使用這些適用於Azure AD 應用程式 Proxy 的 PowerShell 範例，可取得您目錄中的應用程式 Proxy 應用程式和連接器的相關資訊、將使用者和群組指派給應用程式，以及取得憑證資訊。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f10e2b0aa5c7e178ae6b58805703fcb1b0ff3e2b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352485"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>適用於Azure AD 應用程式 Proxy 的 Azure AD PowerShell 範例

下表包含適用於 Azure AD 應用程式 Proxy 的 PowerShell 指令碼範例的連結。 這些範例需要 [AzureAD V2 PowerShell for Graph 模組](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或 [AzureAD V2 PowerShell for Graph 模組預覽版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)，除非另有註明。


若要進一步了解這些範例中使用 Cmdlet，請參閱[應用程式 Proxy 應用程式管理](/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)和[應用程式 Proxy 連接器管理](/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)。

| 連結 | 描述 |
|---|---|
|**應用程式 Proxy 應用程式**||
| [列出所有應用程式 Proxy 應用程式的基本資訊](scripts/powershell-get-all-app-proxy-apps-basic.md) | 列出目錄中所有應用程式 Proxy 應用程式的相關基本資訊 (AppId、DisplayName、ObjId)。 |
| [列出所有應用程式 Proxy 應用程式的擴充資訊](scripts/powershell-get-all-app-proxy-apps-extended.md) | 列出目錄中所有應用程式 Proxy 應用程式的相關擴充資訊 (AppId、DisplayName、ExternalUrl、InternalUrl、ExternalAuthenticationType)。  |
| [依連接器群組列出所有的應用程式 Proxy 應用程式](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | 列出目錄中所有應用程式 Proxy 應用程式的相關資訊，以及應用程式指派到的連接器群組。 |
| [取得具有權杖存留期原則的所有應用程式 Proxy 應用程式](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | 列出目錄中具有權杖存留期原則的所有應用程式 Proxy 應用程式，以及該原則的詳細資料。 此範例需要 [AzureAD V2 PowerShell for Graph 模組預覽版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)。 |
|**連接器群組**||
| [取得目錄中所有的連接器群組和連接器](scripts/powershell-get-all-connectors.md) | 列出目錄中所有的連接器群組和連接器。 |
| [將所有指派給連接器群組的應用程式移至另一個連接器群組](scripts/powershell-move-all-apps-to-connector-group.md) | 將目前指派給連接器群組的所有應用程式移至不同的連接器群組。 |
|**已指派使用者和群組**||
| [顯示指派給應用程式 Proxy 應用程式的使用者和群組](scripts/powershell-display-users-group-of-app.md) | 列出指派給特定應用程式 Proxy 應用程式的使用者和群組。 |
| [將使用者指派給應用程式](scripts/powershell-assign-user-to-app.md) | 將特定使用者指派給應用程式。 |
| [將群組指派給應用程式](scripts/powershell-assign-group-to-app.md) | 將特定群組指派給應用程式。 |
|**外部 URL 設定**||
| [取得使用預設網域的所有應用程式 Proxy 應用程式 (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | 列出使用預設網域 (.msappproxy.net) 的所有應用程式 Proxy 應用程式。 |
| [取得使用萬用字元發佈的所有應用程式 Proxy 應用程式](scripts/powershell-get-all-wildcard-apps.md) | 列出使用萬用字元發佈的所有應用程式 Proxy 應用程式。 |
|**自訂網域設定**||
| [取得使用自訂網域和憑證資訊的所有應用程式 Proxy 應用程式](scripts/powershell-get-all-custom-domains-and-certs.md) | 列出使用自訂網域以及與自訂網域相關聯之憑證資訊的所有應用程式 Proxy 應用程式。 |
| [取得在發佈時未上傳憑證的所有 Azure AD Proxy 應用程式](scripts/powershell-get-all-custom-domain-no-cert.md) | 列出使用自訂網域、但未上傳有效 TLS/SSL 憑證的所有應用程式 Proxy 應用程式。 |
| [取得以相同憑證發佈的所有 Azure AD Proxy 應用程式](scripts/powershell-get-custom-domain-identical-cert.md) | 列出以相同憑證發佈的所有 Azure AD Proxy 應用程式。 |
| [取得以相同憑證發佈的所有 Azure AD Proxy 應用程式，並取代憑證](scripts/powershell-get-custom-domain-replace-cert.md) | 對於以相同憑證發佈的 Azure AD Proxy 應用程式，您可以大量取代該憑證。 |