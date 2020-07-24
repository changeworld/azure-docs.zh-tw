---
title: 自訂原則的開發人員注意事項
titleSuffix: Azure AD B2C
description: 開發人員在使用自訂原則來設定和維護 Azure AD B2C 時的注意事項。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 255e0e38d8dc434843452308d2eebc2fd4774c50
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116526"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>開發人員在 Azure Active Directory B2C 中使用自訂原則的注意事項

Azure Active Directory B2C 中的自訂原則設定現已正式推出。 此設定方法的適用對象是正在建置複雜身分識別解決方案的進階身分識別開發人員。 自訂原則可讓使用者在 Azure AD B2C 租用戶中使用 Identity Experience Framework 的強大功能。
使用自訂原則的進階身分識別開發人員應規劃時間以完成逐步解說課程和閱讀參考文件。

雖然大部分的自訂原則選項現已正式推出，但仍有某些基礎功能處於軟體生命週期的不同階段，例如技術設定檔類型和內容定義 API。 後續將推出更多功能。 下表指定更精細的可用性層級。

## <a name="features-that-are-generally-available"></a>正式推出的功能

- 使用自訂原則來撰寫和上傳自訂驗證使用者旅程。
    - 逐步將使用者旅程描述為宣告提供者之間的交換。
    - 定義使用者旅程中的條件式分支。
- 在您的自訂驗證使用者旅程中交互操作已啟用 REST API 功能的服務。
- 與符合 OpenIDConnect 通訊協定的識別提供者同盟。
- 與遵守 SAML 2.0 通訊協定的識別提供者同盟。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自訂原則功能集開發人員的責任

手動設定原則會對 Azure AD B2C 基礎平台授與較低層級的存取權，並建立唯一的信任架構。 自訂識別提供者、信任關係、與外部服務的整合以及逐步工作流程會有各種可能的排列組合，都需要以有條理的方式進行設計和設定。

使用自訂原則功能集的開發人員應遵守下列指導方針：

- 熟悉自訂原則的設定語言和金鑰/秘密管理。 如需詳細資訊，請參閱 [TrustFrameworkPolicy](trustframeworkpolicy.md)。
- 完整掌控案例和自訂整合。 記錄您的工作並通知您的即時網站組織。
- 有系統地執行案例測試。
- 對至少一個開發和測試環境以及一個生產環境遵循軟體開發和分段進行的最佳做法。
- 掌握與您整合之識別提供者和服務的最新開發資訊。 例如，追蹤密碼的變更，以及服務的排程和未排程變更。
- 設定主動式監控，並監視生產環境的回應能力。 如需與 Application Insights 整合的詳細資訊，請參閱 [Azure Active Directory B2C：收集記錄](analytics-with-application-insights.md)。
- 在 Azure 訂用帳戶中保有最新的連絡人電子郵件地址，並持續回應 Microsoft 即時網站小組的電子郵件。
- 在 Microsoft 即時網站小組的建議下及時採取動作。

## <a name="terms-for-features-in-public-preview"></a>公開預覽功能的條款

- 建議將公開預覽功能僅供作評估之用。
- 服務等級協定 (SLA) 不適用於公開預覽功能。
- 您可以透過標準支援管道提出公開預覽功能的支援要求。

## <a name="features-by-stage-and-known-issues"></a>依階段和已知問題的功能

自訂原則/識別體驗架構功能正在進行持續且快速的開發。 下表是功能/元件可用性的索引。


### <a name="protocols-and-authorization-flows"></a>通訊協定和授權流程

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2 授權碼](authorization-code-flow.md) |  |  | X |  |
| 使用 PKCE 的 OAuth2 授權碼 |  |  | X | 僅限行動應用程式  |
| [OAuth2 隱含流程](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 資源擁有者密碼認證](ropc-custom.md) |  | X |  |  |
| [OIDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |  |X  | POST 和重新導向繫結。 |
| OAuth1 |  |  |  | 不支援。 |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>識別提供者同盟 

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | 例如，Google+。  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | 例如，Facebook。  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | 例如，Twitter。 |
| [SAML2](saml-identity-provider-technical-profile.md) |  |   | X | 例如 Salesforce、ADFS。 |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST API 整合

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [使用基本驗證的 REST API](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [使用用戶端憑證驗證的 REST API](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [具有 OAuth2 持有人驗證的 REST API](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>元件支援

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [電話要素驗證](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure MFA 驗證](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [單次密碼](one-time-password-technical-profile.md) |  | X |  |  |
| 作為本機目錄的 [Azure Active Directory](active-directory-technical-profile.md) |  |  | X |  |
| 電子郵件驗證的 Azure 電子郵件子系統 |  |  | X |  |
| [第三方電子郵件服務提供者](custom-email-mailjet.md) |  |X  |  |  |
| [多語言支援](localization.md)|  |  | X |  |
| [述詞驗證](predicates.md) |  |  | X | 例如，密碼複雜性。 |
| [顯示控制項](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>頁面配置版本

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [JavaScript 支援](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF 整合

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 查詢字串參數 `domain_hint` |  |  | X | 可作為宣告，可以傳遞至 IDP。 |
| 查詢字串參數 `login_hint` |  |  | X | 可作為宣告，可以傳遞至 IDP。 |
| 透過 `client_assertion` 將 JSON 插入使用者旅程中 | X |  |  | 即將淘汰。 |
| 將 JSON 插入使用者旅程中作為 `id_token_hint` |  | X |  | 傳遞 JSON 的前向方法。 |
| [將識別提供者權杖傳遞至應用程式](idp-pass-through-custom.md) |  | X |  | 例如，從 Facebook 到應用程式。 |

### <a name="session-management"></a>工作階段管理

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [預設 SSO 工作階段提供者](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [外部登入工作階段提供者](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO 工作階段提供者](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [單一登出](session-overview.md#sign-out)  |  | X |  |  |

### <a name="security"></a>安全性

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| 原則機碼產生，手動上傳 |  |  | X |  |
| 原則機碼 - RSA/憑證，密碼 |  |  | X |  |


### <a name="developer-interface"></a>開發人員介面

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure 入口網站 IEF UX |  |  | X |  |
| 原則上傳 |  |  | X |  |
| [Application Insights 使用者旅程記錄](troubleshoot-with-application-insights.md) |  | X |  | 用於開發期間的疑難排解。  |
| [Application Insights 事件記錄檔](application-insights-technical-profile.md) |  | X |  | 用來監視生產環境中的使用者流程。 |


## <a name="next-steps"></a>後續步驟

- 查看[適用於 Azure AD B2C 的 Microsoft Graph 作業](microsoft-graph-operations.md)
- 深入了解[自訂原則及其與使用者流程的差異](custom-policy-overview.md)。
