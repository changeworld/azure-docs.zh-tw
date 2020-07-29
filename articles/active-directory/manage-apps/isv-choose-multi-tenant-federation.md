---
title: 為多租使用者應用程式選擇正確的同盟通訊協定
description: 獨立軟體廠商與 Azure Active Directory 整合的指引
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7dc40d0e4736b4fef8012b08737cbd07bc36bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763290"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>為您的多租使用者應用程式選擇正確的同盟通訊協定

當您開發軟體即服務（SaaS）應用程式時，您必須選取最符合您和客戶需求的同盟通訊協定。 這是以您的開發平臺為基礎，而您想要與客戶的 Office 365 和 Azure AD 生態系統內可用的資料整合在一起。

請參閱可與 Azure Active Directory[進行 SSO](what-is-single-sign-on.md)整合的完整通訊協定清單。
下表比較 
* 開啟驗證2.0 （OAuth 2.0）
* Open ID Connect （OIDC）
* 安全性判斷提示標記語言 (SAML)
* Web 服務同盟（WSFed）

| 功能| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| 網頁單一登入| √| √ |
| Web 單一登出| √| √ |
| 以行動為基礎的單一登入| √| √* |
| 以行動為基礎的單一登出| √| √* |
| 行動應用程式的條件式存取原則| √| X |
| 適用于行動應用程式的順暢 MFA 體驗| √| X |
| 存取 Microsoft Graph| √| X |

* 可能，但 Microsoft 不提供範例或指引。

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 和 Open ID Connect

OAuth 2.0 是用於授權的[業界標準](https://oauth.net/2/)通訊協定。 OIDC （OpenID Connect）是以 OAuth 2.0 通訊協定為基礎的[業界標準](https://openid.net/connect/)身分識別驗證層。

### <a name="benefits"></a>優點

Microsoft 建議使用 OIDC/OAuth 2.0，因為它們具有內建于通訊協定的驗證和授權。 使用 SAML 時，您必須另外執行授權。

這些通訊協定中固有的授權可讓您的應用程式透過 Microsoft Graph API 存取豐富的使用者和組織資料，並與之整合。

使用 OAuth 2.0 和 OIDC 可讓您在為應用程式採用 SSO 時，簡化客戶的使用者體驗。 您可以輕鬆地定義必要的許可權集合，然後自動向系統管理員或同意的使用者表示。

此外，使用這些通訊協定可讓您的客戶使用條件式存取和 MFA 原則來控制應用程式的存取權。 Microsoft 提供[跨多個技術平臺](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)的程式庫和程式碼範例，以協助您進行開發。  

### <a name="implementation"></a>實作

您會向 Microsoft 身分識別註冊您的應用程式，這是 OAuth 2.0 提供者。 您也可以使用您想要整合的任何其他身分識別提供者，註冊您的 OAuth 2.0 應用程式。 

如需如何註冊應用程式，並將這些通訊協定用於 SSO 到 web apps 的相關資訊，請參閱[使用 OpenID connect 和 Azure Active Directory 授權存取 web 應用程式](../develop/sample-v2-code.md)。  如需如何在行動裝置應用程式中針對 SSO 執行這些通訊協定的相關資訊，請參閱下列各項： 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [通用 Windows 平台](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 和 WSFed

安全性聲明標記語言（SAML）通常用於 web 應用程式。 瞭解[Azure 如何使用 SAML 通訊協定](../develop/active-directory-saml-protocol-reference.md)來進行總覽。 

Web 服務同盟（WSFed）是一種[業界標準](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)，通常用於使用 .net 平臺開發的 web 應用程式。

### <a name="benefits"></a>優點

SAML 2.0 是一個成熟的標準，而大部分的技術平臺都支援 SAML 2.0 的開放原始碼程式庫。 您可以為客戶提供管理介面來設定 SAML SSO。 他們可以設定 Microsoft Azure AD 的 SAML SSO，以及任何其他支援 SAML 2 的身分識別提供者

### <a name="trade-offs"></a>取捨

針對行動應用程式使用 SAML 2.0 或 WSFed 通訊協定時，某些條件式存取原則（包括多重要素驗證（MFA））會有降低的體驗。 此外，如果您想要存取 Microsoft Graph，就必須透過 OAuth 2.0 執行授權，以產生必要的權杖。 

### <a name="implementation"></a>實作

Microsoft 不提供 SAML 執行或建議特定程式庫的程式庫。 有許多開放原始碼程式庫可用。

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO 和使用 Microsoft Graph Rest API 

Microsoft Graph 是所有 Microsoft 365 的資料網狀架構，包括 Office 365、Windows 10 和企業行動性和安全性，以及 Dynamics 365 等其他產品。 這包括實體的核心架構，例如使用者、群組、行事曆、郵件、檔案等等，其可提高使用者生產力。 Microsoft Graph 為開發人員提供了以 REST 為基礎的 API 的三種介面，Microsoft Graph 資料連線，以及可讓開發人員將自己的資料新增至 Microsoft Graph 的連接器。  

針對 SSO 使用上述任何通訊協定，可讓您的應用程式存取透過 Microsoft Graph REST API 提供的豐富資料。 這可讓您的客戶從 Microsoft 365 的投資中獲得更多價值。 例如，您的應用程式可以呼叫 Microsoft Graph API 來與客戶的 Office 365 實例整合，並在應用程式內呈現使用者的 Microsoft Office 和 SharePoint 專案。 

如果您使用 Open ID Connect 進行驗證，則您的開發體驗會很順暢，因為您會使用 OAuth2 （Open ID Connect 的基礎）來取得權杖，以便用來叫用 Microsoft Graph Api。 如果您的應用程式使用 SAML 或 WSFed，您必須在應用程式內新增額外的程式碼，才能讓這些 OAuth2 取得叫用 Microsoft Graph Api 所需的權杖。 

## <a name="next-steps"></a>後續步驟

[為多租用戶應用程式啟用 SSO](isv-sso-content.md)

[建立多租使用者應用程式的檔](isv-create-sso-documentation.md)
