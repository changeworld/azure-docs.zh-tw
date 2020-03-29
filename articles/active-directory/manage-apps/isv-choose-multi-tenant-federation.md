---
title: 為多租戶應用程式選擇正確的識別身分同盟協定
description: 獨立軟體廠商與 Azure 活動目錄集成指南
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443384"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>為多租戶應用程式選擇正確的識別身分同盟協定

當您將軟體發展為服務 （SaaS） 應用程式時，必須選擇最能滿足您和您的客戶需求的聯合協定。 此決策基於您的開發平臺，以及您希望與客戶 Office 365 和 Azure AD 生態系統中的資料整合。

請參閱可用於與 Azure 活動目錄[進行 SSO 集成](what-is-single-sign-on.md)的協定的完整清單。
下表比較 
* 打開身份驗證 2.0 （OAuth 2.0）
* 打開 ID 連接 （OIDC）
* 安全性判斷提示標記語言 (SAML)
* 網路服務聯盟

| 功能| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| 基於 Web 的單登錄| √| √ |
| 基於 Web 的單簽| √| √ |
| 基於移動的單一登入| √| √* |
| 基於移動的單次登出| √| √* |
| 移動應用程式的條件訪問策略| √| X |
| 適用于移動應用程式的無縫 MFA 體驗| √| X |
| 訪問微軟圖| √| X |

*可能，但微軟不提供示例或指南。

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 和打開 ID 連接

OAuth 2.0 是一個[行業標準](https://oauth.net/2/)的授權協定。 OIDC（OpenID 連接）是構建在 OAuth 2.0 協定之上的[行業標準](https://openid.net/connect/)標識身份驗證層。

### <a name="benefits"></a>優點

Microsoft 建議使用 OIDC/OAuth 2.0，因為它們具有內置到協定中的身份驗證和授權。 使用 SAML 時，還必須額外實現授權。

這些協定固有的授權使應用程式能夠通過 Microsoft 圖形 API 訪問和集成豐富的使用者和組織資料。

使用 OAuth 2.0 和 OIDC 可簡化客戶在為您的應用使用 SSO 時的最終使用者體驗。 您可以輕鬆地定義所需的許可權集，然後自動表示給管理員或最終使用者同意。

此外，使用這些協定使您的客戶能夠使用條件訪問和 MFA 策略來控制對應用程式的訪問。 Microsoft[跨多個技術平臺](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)提供庫和代碼示例，以説明您開發。  

### <a name="implementation"></a>實作

您將應用程式註冊到 Microsoft 標識，這是 OAuth 2.0 提供程式。 然後，您還可以將基於 OAuth 2.0 的應用程式註冊到您希望與任何其他標識供應商集成。 

有關如何註冊應用程式並將 SSO 協定實現到 Web 應用程式的資訊，請參閱[使用 OpenID 連接和 Azure 活動目錄授權訪問 Web 應用程式](../develop/sample-v2-code.md)。  有關如何在移動應用中為 SSO 實現這些協定的資訊，請參閱以下內容： 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [通用 Windows 平台](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 和 WSFed

安全斷言標記語言 （SAML） 通常用於 Web 應用程式。 請參閱[Azure 如何使用 SAML 協定](../develop/active-directory-saml-protocol-reference.md)進行概述。 

Web 服務聯盟 （WSFed） 是一種[行業標準](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)，通常用於使用 .Net 平臺開發的 Web 應用程式。

### <a name="benefits"></a>優點

SAML 2.0 是一個成熟的標準，大多數技術平臺都支援 SAML 2.0 的開源庫。 您可以為客戶提供一個管理介面來配置 SAML SSO。 他們可以為 Microsoft Azure AD 配置 SAML SSO，以及支援 SAML 2 的任何其他標識提供程式

### <a name="trade-offs"></a>取捨

在移動應用程式中使用 SAML 2.0 或 WSFed 協定時，某些條件訪問策略（包括多重要素驗證 （MFA） 將具有降級體驗。 此外，如果要訪問 Microsoft 圖形，則需要通過 OAuth 2.0 實現授權以生成必要的權杖。 

### <a name="implementation"></a>實作

Microsoft 不為 SAML 實現提供庫，也不推薦特定庫。 有許多開源庫可用。

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO 和使用微軟圖形靜止 API 

Microsoft Graph 是跨所有 Microsoft 365 的資料交換矩陣，包括 Office 365、Windows 10 和企業移動性和安全性，以及其他產品（如 Dynamics 365）。 這包括實體的核心架構，如使用者、組、日曆、郵件、檔等，這些實體可提高使用者工作效率。 Microsoft Graph 為開發人員提供了三個介面，即基於 REST 的 API、Microsoft 圖形資料連線和連接器，允許開發人員將自己的資料添加到 Microsoft 圖形中。  

使用上述任何協定進行 SSO 使應用程式能夠訪問通過 Microsoft 圖形 REST API 提供的豐富資料。 這使您的客戶能夠從對 Microsoft 365 的投資中獲得更多價值。 例如，您的應用程式可以調用 Microsoft 圖形 API 來與客戶的 Office 365 實例以及應用程式中的 Microsoft Office 和 SharePoint 專案集成。 

如果使用 Open ID 連接進行身份驗證，則開發體驗是無縫的，因為您將使用 Open ID Connect 的基礎 OAuth2 獲取權杖，可用於調用 Microsoft 圖形 API。 如果應用程式使用 SAML 或 WSFed，則必須在應用程式中添加其他代碼，以獲取這些 OAuth2 以獲取調用 Microsoft 圖形 API 所需的權杖。 

## <a name="next-steps"></a>後續步驟

[為多租用戶應用程式啟用 SSO](isv-sso-content.md)

[為多租戶應用程式創建文檔](isv-create-sso-documentation.md)
