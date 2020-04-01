---
title: 自訂策略的開發人員註解
titleSuffix: Azure AD B2C
description: 開發人員在使用自訂原則來設定和維護 Azure AD B2C 時的注意事項。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408706"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure 活動目錄 B2C 中自訂策略的開發人員註解

Azure 活動目錄 B2C 中的自訂策略配置現在通常可用。 這種配置方法針對先進識別開發人員構建複雜的標識解決方案。 自定義策略使標識體驗框架的強大功能在 Azure AD B2C 租戶中可用。
使用自定義策略的先進識別開發人員應計劃投入一些時間完成演練和閱讀參考文檔。

雖然大多數可用的自定義策略選項現在通常可用,但存在一些基礎功能,例如技術配置檔類型和內容定義 API,這些功能處於軟體生命週期的不同階段。 還有更多人來了。 下表指定更精細級別的可用性級別。

## <a name="features-that-are-generally-available"></a>通常可用的功能

- 使用自訂原則來撰寫和上傳自訂驗證使用者旅程。
    - 逐步將使用者旅程描述為宣告提供者之間的交換。
    - 定義使用者旅程中的條件式分支。
- 在自定義身份驗證使用者旅程中,與啟用 REST API 的服務互通。
- 與符合 OpenIDConnect 協定的標識提供程式聯合。
- 與遵守 SAML 2.0 協定的標識提供程式聯合。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自訂原則功能集開發人員的責任

手動策略配置允許較低級別的訪問 Azure AD B2C 的基礎平臺,並生成唯一的信任框架。 自訂標識提供者、信任關係、與外部服務的整合以及分步工作流的許多可能排列都需要一種有條理的設計和配置方法。

使用自訂策略功能集的開發人員應遵守以下準則:

- 熟悉自定義策略和密鑰/機密管理的配置語言。 有關詳細資訊,請參閱[信任框架策略](trustframeworkpolicy.md)。
- 完整掌控案例和自訂整合。 記錄您的工作並通知即時網站組織。
- 有系統地執行案例測試。
- 對至少一個開發和測試環境以及一個生產環境遵循軟體開發和分段進行的最佳做法。
- 掌握與您整合之識別提供者和服務的最新開發資訊。 例如，追蹤密碼的變更，以及服務的排程和未排程變更。
- 設定主動式監控，並監視生產環境的回應能力。 有關與應用程式見解整合的詳細資訊,請參閱[Azure 活動目錄 B2C:收集紀錄](analytics-with-application-insights.md)。
- 在 Azure 訂用帳戶中保有最新的連絡人電子郵件地址，並持續回應 Microsoft 即時網站小組的電子郵件。
- 在 Microsoft 即時網站小組的建議下及時採取動作。

## <a name="terms-for-features-in-public-preview"></a>公共預覽版中功能的術語

- 我們鼓勵您僅出於評估目的使用公共預覽功能。
- 服務級別協定 (SL) 不適用於公共預覽功能。
- 公共預覽功能的支援請求可通過常規支援管道提交。

## <a name="features-by-stage-and-known-issues"></a>依階段和已知問題的功能

自定義策略/身份體驗框架功能正在持續快速發展。 下表是功能和元件可用性的索引。


### <a name="protocols-and-authorization-flows"></a>協定與授權串流

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2 授權碼](authorization-code-flow.md) |  |  | X |  |
| 帶有 PKCE 的 OAuth2 授權代碼 |  |  | X | 只限移動應用程式  |
| [OAuth2 隱式流](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 資源擁有者密碼認證](ropc-custom.md) |  | X |  |  |
| [OIDC 連線](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |X  |  | POST 和重定向綁定。 |
| OAuth1 |  |  |  | 不支援。 |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>確定提供者 

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | 例如,Google+。  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | 例如,Facebook。  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | 例如,推特。 |
| [SAML2](saml-technical-profile.md) |  |   | X | 例如,銷售隊伍,ADFS。 |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST API 整合

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API 與基本 au](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [用於客戶端憑證 au 的 REST API](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST API 與 OAuth2 承載 auth](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>元件支援

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [電話因素身份驗證](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure MFA 認證](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [一次密碼](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure 的項目目錄](active-directory-technical-profile.md)為本地目錄 |  |  | X |  |
| 電子郵件認證的 Azure 電子郵件子系統 |  |  | X |  |
| [第三方電子郵件服務提供者](custom-email.md) |  |X  |  |  |
| [多語言支援](localization.md)|  |  | X |  |
| [謂詞驗證](predicates.md) |  |  | X | 例如,密碼複雜性。 |
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
| 查詢字串參數`domain_hint` |  |  | X | 可作為索賠提供,可傳遞給 IDP。 |
| 查詢字串參數`login_hint` |  |  | X | 可作為索賠提供,可傳遞給 IDP。 |
| 透過 JSON 插入到使用者旅程中`client_assertion` | X |  |  | 將被棄用。 |
| 將 JSON 插入到使用者旅程中,如`id_token_hint` |  | X |  | 前進方法通過 JSON。 |
| [將識別提供者傳遞給應用程式](idp-pass-through-custom.md) |  | X |  | 例如,從 Facebook 到應用。 |

### <a name="session-management"></a>工作階段管理

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [預設 SSO 工作階段提供者](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [外部登入工作階段提供者](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO 工作階段提供者](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


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
| [應用程式見解使用者旅程紀錄](troubleshoot-with-application-insights.md) |  | X |  | 用於開發過程中的故障排除。  |
| [應用程式見解事件紀錄](application-insights-technical-profile.md) |  | X |  | 用於監視生產中的使用者流。 |


## <a name="next-steps"></a>後續步驟

- 檢查[適用於 Azure AD B2C 的 Microsoft 圖形操作](microsoft-graph-operations.md)
- 詳細瞭解[自訂策略和使用者串流的差異](custom-policy-overview.md)。
