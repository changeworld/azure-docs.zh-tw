---
title: 自訂原則的開發人員注意事項
titleSuffix: Azure AD B2C
description: 開發人員在使用自訂原則來設定和維護 Azure AD B2C 時的注意事項。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6439393b72eb724ca017edc17ce7a7c36c275fca
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166970"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中自訂原則的開發人員注意事項

Azure Active Directory B2C 中的自訂原則設定現已正式推出。 這種設定方法的目標是要建立複雜身分識別解決方案的 advanced identity 開發人員。 自訂原則可讓 Identity Experience Framework 的功能在 Azure AD B2C 租使用者中使用。
使用自訂原則的 Advanced identity 開發人員應該規劃投入一些時間來完成逐步解說和閱讀參考檔。

雖然大部分可用的自訂原則選項現在已正式推出，但仍有基礎功能，例如在軟體生命週期中不同階段的技術配置檔案類型和內容定義 Api。 即將推出更多的資訊。 下表指定更細微層級的可用性層級。

## <a name="features-that-are-generally-available"></a>正式推出的功能

- 使用自訂原則來撰寫和上傳自訂驗證使用者旅程。
    - 逐步將使用者旅程描述為宣告提供者之間的交換。
    - 定義使用者旅程中的條件式分支。
- 與您的自訂驗證使用者旅程中已啟用 REST API 的服務相交互操作。
- 與相容于 OpenIDConnect 通訊協定的身分識別提供者同盟。
- 與遵守 SAML 2.0 通訊協定的識別提供者同盟。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自訂原則功能集開發人員的責任

手動原則設定會將較低層級的存取權授與 Azure AD B2C 的基礎平臺，並導致建立唯一的信任架構。 自訂身分識別提供者、信任關係、與外部服務的整合以及逐步執行工作流程的許多可能的排列，都需要有條理的方式來設計和設定。

使用自訂原則功能集的開發人員應遵循下列指導方針：

- 熟悉自訂原則的設定語言和金鑰/密碼管理。 如需詳細資訊，請參閱[TrustFrameworkPolicy](trustframeworkpolicy.md)。
- 完整掌控案例和自訂整合。 記錄您的工作並通知您的即時網站組織。
- 有系統地執行案例測試。
- 對至少一個開發和測試環境以及一個生產環境遵循軟體開發和分段進行的最佳做法。
- 掌握與您整合之識別提供者和服務的最新開發資訊。 例如，追蹤密碼的變更，以及服務的排程和未排程變更。
- 設定主動式監控，並監視生產環境的回應能力。 如需與 Application Insights 整合的詳細資訊，請參閱[Azure Active Directory B2C：收集記錄](analytics-with-application-insights.md)。
- 在 Azure 訂用帳戶中保有最新的連絡人電子郵件地址，並持續回應 Microsoft 即時網站小組的電子郵件。
- 在 Microsoft 即時網站小組的建議下及時採取動作。

## <a name="terms-for-features-in-public-preview"></a>公開預覽功能的條款

- 我們鼓勵您使用公開預覽功能，僅供評估之用。
- 服務等級協定（Sla）不適用於公開預覽功能。
- 公開預覽功能的支援要求可以透過一般支援通道來進行。

## <a name="features-by-stage-and-known-issues"></a>依階段和已知問題的功能

自訂原則/Identity Experience Framework 功能正處於持續且快速的開發。 下表是功能和元件可用性的索引。

### <a name="identity-providers-tokens-protocols"></a>識別提供者、權杖、通訊協定

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | 例如，Google +。  |
| IDP-OAUTH2 |  |  | X | 例如，Facebook。  |
| IDP-OAUTH1 （twitter） |  | X |  | 例如，Twitter。 |
| IDP-OAUTH1 （ex-twitter） |  |  |  | 不支援 |
| IDP-SAML |  |   | X | 例如，Salesforce、ADFS。 |
| IDP-WSFED | X |  |  |  |
| 信賴憑證者 OAUTH1 |  |  |  | 不支援。 |
| 信賴憑證者 OAUTH2 |  |  | X |  |
| 信賴憑證者 OIDC |  |  | X |  |
| 信賴憑證者 SAML |  |X  |  |  |
| 信賴憑證者 WSFED | X |  |  |  |
| 使用基本和憑證驗證的 REST API |  |  | X | 例如，Azure Logic Apps。 |

### <a name="component-support"></a>元件支援

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| 作為本機目錄的 Azure Active Directory |  |  | X |  |
| 用於電子郵件驗證的 Azure 電子郵件子系統 |  |  | X |  |
| 多語言支援|  |  | X |  |
| 述詞驗證 |  |  | X | 例如，密碼複雜性。 |
| 使用協力廠商電子郵件服務提供者 |  |X  |  |  |

### <a name="content-definition"></a>內容定義

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 錯誤頁面，api.error |  |  | X |  |
| IDP 選取項目頁面，api.idpselections |  |  | X |  |
| IDP 註冊選擇，api.idpselections.signup |  |  | X |  |
| 忘了密碼，api.localaccountpasswordreset |  |  | X |  |
| 本機帳戶登入，api.localaccountsignin |  |  | X |  |
| 本機帳戶登入，api.localaccountsignup |  |  | X |  |
| MFA 頁面，api.phonefactor |  |  | X |  |
| 自我判斷社交帳戶註冊，api. selfasserted |  |  | X |  |
| 自我判斷設定檔更新，api.selfasserted.profileupdate |  |  | X |  |
| 統一的註冊或登入頁面 signuporsignin.xml，其參數為 "disableSignup" |  |  | X |  |
| JavaScript/頁面配置 |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF 整合

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 查詢字串參數 domain_hint |  |  | X | 可以宣告的形式提供給 IDP。 |
| 查詢字串參數 login_hint |  |  | X | 可以宣告的形式提供給 IDP。 |
| 透過 client_assertion 將 JSON 插入至 UserJourney | X |  |  | 將會被取代。 |
| 將 JSON 插入至 UserJourney 作為 id_token_hint |  | X |  | 傳遞 JSON 的向前轉型方法。 |
| 將 IDP TOKEN 傳遞至應用程式 |  | X |  | 例如，從 Facebook 到應用程式。 |

### <a name="session-management"></a>工作階段管理

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| SSO 工作階段提供者 |  |  | X |  |
| 外部登入工作階段提供者 |  |  | X |  |
| SAML SSO 工作階段提供者 |  |  | X |  |
| 預設 SSO 會話提供者 |  |  | X |  |

### <a name="security"></a>安全性

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| 原則機碼產生，手動上傳 |  |  | X |  |
| 原則機碼 - RSA/憑證，密碼 |  |  | X |  |
| 原則上傳 |  |  | X |  |

### <a name="developer-interface"></a>開發人員介面

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure 入口網站 IEF UX |  |  | X |  |
| Application Insights UserJourney 記錄 |  | X |  | 用於在開發期間進行疑難排解。  |
| Application Insights 事件記錄檔（透過協調流程步驟） |  | X |  | 用來監視生產環境中的使用者流程。 |

## <a name="next-steps"></a>後續步驟

深入瞭解[自訂原則和使用者流程之間的差異](custom-policy-overview.md)。
