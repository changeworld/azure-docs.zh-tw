---
title: Azure AD 應用程式 Proxy 的 PowerShell 範例
description: 針對 Azure AD 應用程式 Proxy 使用這些 PowerShell 範例，以取得您目錄中的應用程式 Proxy 應用程式和連接器的相關資訊，將使用者和群組指派給應用程式，並取得憑證資訊。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481257"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD Azure AD 應用程式 Proxy 的 PowerShell 範例

下表包含 Azure AD 應用程式 Proxy 的 PowerShell 腳本範例連結。 除非另有注明，否則這些範例需要[AzureAD V2 powershell For graph 模組](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或[AzureAD V2 powershell for graph 模組預覽版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)。


如需這些範例中所使用之 Cmdlet 的詳細資訊，請參閱[應用程式 Proxy 應用程式管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)和[應用程式 proxy 連接器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)。

| | |
|---|---|
|**應用程式 Proxy 應用程式**||
| [列出所有應用程式 Proxy 應用程式的基本資訊](scripts/powershell-get-all-app-proxy-apps-basic.md) | 列出目錄中所有應用程式 Proxy 應用程式的基本資訊（AppId、DisplayName、ObjId）。 |
| [列出所有應用程式 Proxy 應用程式的擴充資訊](scripts/powershell-get-all-app-proxy-apps-extended.md) | 列出目錄中所有應用程式 Proxy 應用程式的擴充資訊（AppId、DisplayName、ExternalUrl、InternalUrl、ExternalAuthenticationType）。  |
| [依連接器群組列出所有應用程式 Proxy 應用程式](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | 列出目錄中所有應用程式 Proxy 應用程式的相關資訊，以及指派給應用程式的連接器群組。 |
| [使用權杖存留期原則取得所有應用程式 Proxy 應用程式](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | 列出您目錄中具有權杖存留期原則及其詳細資料的所有應用程式 Proxy 應用程式。 此範例需要[AzureAD V2 PowerShell For Graph 模組預覽版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)。 |
|**連接器群組**||
| [取得目錄中的所有連接器群組和連接器](scripts/powershell-get-all-connectors.md) | 列出目錄中的所有連接器群組和連接器。 |
| [將所有指派給連接器群組的應用程式移至另一個連接器群組](scripts/powershell-move-all-apps-to-connector-group.md) | 將目前指派給連接器群組的所有應用程式移至不同的連接器群組。 |
|**已指派使用者和群組**||
| [顯示指派給應用程式 Proxy 應用程式的使用者和群組](scripts/powershell-display-users-group-of-app.md) | 列出指派給特定應用程式 Proxy 應用程式的使用者和群組。 |
| [將使用者指派給應用程式](scripts/powershell-assign-user-to-app.md) | 將特定使用者指派給應用程式。 |
| [將群組指派給應用程式](scripts/powershell-assign-group-to-app.md) | 將特定群組指派給應用程式。 |
|**外部 URL 設定**||
| [使用預設網域取得所有應用程式 Proxy 應用程式（. msappproxy.net）](scripts/powershell-get-all-default-domain-apps.md)  | 列出所有使用預設網域（. msappproxy.net）的應用程式 Proxy 應用程式。 |
| [使用萬用字元發行取得所有應用程式 Proxy 應用程式](scripts/powershell-get-all-wildcard-apps.md) | 列出所有使用萬用字元發行的應用程式 Proxy 應用程式。 |
|**自訂網域設定**||
| [使用自訂網域及憑證資訊取得所有應用程式 Proxy 應用程式](scripts/powershell-get-all-custom-domains-and-certs.md) | 列出所有使用自訂網域的應用程式 Proxy 應用程式，以及與自訂網域相關聯的憑證資訊。 |
| [取得發佈的所有 Azure AD Proxy 應用程式，但未上傳任何憑證](scripts/powershell-get-all-custom-domain-no-cert.md) | 列出所有使用自訂網域但未上傳有效的 TLS/SSL 憑證的應用程式 Proxy 應用程式。 |
| [取得使用相同憑證發佈的所有 Azure AD Proxy 應用程式](scripts/powershell-get-custom-domain-identical-cert.md) | 列出所有以相同憑證發佈的 Azure AD Proxy 應用程式。 |
| [取得使用相同憑證發佈的所有 Azure AD Proxy 應用程式，並加以取代](scripts/powershell-get-custom-domain-replace-cert.md) | 對於使用相同憑證發佈的 Azure AD Proxy 應用程式，可讓您取代 bulk 中的憑證。 |
