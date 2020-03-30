---
title: 自訂策略的開發人員注釋
titleSuffix: Azure AD B2C
description: 開發人員在使用自訂原則來設定和維護 Azure AD B2C 時的注意事項。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189390"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure 活動目錄 B2C 中自訂策略的開發人員注釋

Azure 活動目錄 B2C 中的自訂策略配置現在通常可用。 這種配置方法針對高級標識開發人員構建複雜的標識解決方案。 自訂策略使標識體驗框架的強大功能在 Azure AD B2C 租戶中可用。
使用自訂策略的高級標識開發人員應計畫投入一些時間完成演練和閱讀參考文檔。

雖然大多數可用的自訂策略選項現在通常可用，但存在一些基礎功能，例如技術配置檔案類型和內容定義 API，這些功能處於軟體生命週期的不同階段。 還有更多人來了。 下表指定更精細級別的可用性級別。

## <a name="features-that-are-generally-available"></a>通常可用的功能

- 使用自訂原則來撰寫和上傳自訂驗證使用者旅程。
    - 逐步將使用者旅程描述為宣告提供者之間的交換。
    - 定義使用者旅程中的條件式分支。
- 在自訂身份驗證使用者旅程中，與啟用 REST API 的服務交互操作。
- 與符合 OpenIDConnect 協定的標識提供程式聯合。
- 與遵守 SAML 2.0 協定的標識提供程式聯合。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自訂原則功能集開發人員的責任

手動策略配置允許較低級別的訪問 Azure AD B2C 的基礎平臺，並生成唯一的信任框架。 自訂標識提供程式、信任關係、與外部服務的集成以及分步工作流的許多可能排列都需要一種有條理的設計和配置方法。

使用自訂策略功能集的開發人員應遵守以下準則：

- 熟悉自訂策略和金鑰/機密管理的配置語言。 有關詳細資訊，請參閱[信任框架策略](trustframeworkpolicy.md)。
- 完整掌控案例和自訂整合。 記錄您的工作並通知即時網站組織。
- 有系統地執行案例測試。
- 對至少一個開發和測試環境以及一個生產環境遵循軟體開發和分段進行的最佳做法。
- 掌握與您整合之識別提供者和服務的最新開發資訊。 例如，追蹤密碼的變更，以及服務的排程和未排程變更。
- 設定主動式監控，並監視生產環境的回應能力。 有關與應用程式見解集成的詳細資訊，請參閱[Azure 活動目錄 B2C：收集日誌](analytics-with-application-insights.md)。
- 在 Azure 訂用帳戶中保有最新的連絡人電子郵件地址，並持續回應 Microsoft 即時網站小組的電子郵件。
- 在 Microsoft 即時網站小組的建議下及時採取動作。

## <a name="terms-for-features-in-public-preview"></a>公共預覽版中功能的術語

- 我們鼓勵您僅出於評估目的使用公共預覽功能。
- 服務等級協定 （SL） 不適用於公共預覽功能。
- 公共預覽功能的支援請求可通過常規支援管道提交。

## <a name="features-by-stage-and-known-issues"></a>依階段和已知問題的功能

自訂策略/身份體驗框架功能正在持續快速發展。 下表是功能和元件可用性的索引。

### <a name="identity-providers-tokens-protocols"></a>識別提供者、權杖、通訊協定

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | 例如，Google+。  |
| IDP-OAUTH2 |  |  | X | 例如，Facebook。  |
| IDP-OAUTH1（推特） |  | X |  | 例如，推特。 |
| IDP-OAUTH1（前推特） |  |  |  | 不支援 |
| IDP-SAML |  |   | X | 例如，銷售隊伍，ADFS。 |
| IDP-WSFED | X |  |  |  |
| 依賴方 OAUTH1 |  |  |  | 不支援。 |
| 依賴方 OAUTH2 |  |  | X |  |
| 信賴憑證者 OIDC |  |  | X |  |
| 信賴憑證者 SAML |  |X  |  |  |
| 信賴憑證者 WSFED | X |  |  |  |
| REST API，帶基本證書 |  |  | X | 例如，Azure 邏輯應用。 |

### <a name="component-support"></a>元件支援

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| 作為本機目錄的 Azure Active Directory |  |  | X |  |
| 用於電子郵件驗證的 Azure 電子郵件子系統 |  |  | X |  |
| 多語言支援|  |  | X |  |
| 謂詞驗證 |  |  | X | 例如，密碼複雜性。 |
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
| 自我聲明的社會帳戶註冊，api.自我斷言 |  |  | X |  |
| 自我判斷設定檔更新，api.selfasserted.profileupdate |  |  | X |  |
| 統一註冊或登錄頁面 api.註冊登錄，參數"禁用登錄" |  |  | X |  |
| JavaScript / 頁面配置 |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF 整合

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 查詢字串參數 domain_hint |  |  | X | 可作為索賠提供，可傳遞給 IDP。 |
| 查詢字串參數 login_hint |  |  | X | 可作為索賠提供，可傳遞給 IDP。 |
| 透過 client_assertion 將 JSON 插入至 UserJourney | X |  |  | 將被棄用。 |
| 將 JSON 插入至 UserJourney 作為 id_token_hint |  | X |  | 前進方法通過 JSON。 |
| 將 IDP TOKEN 傳遞給應用程式 |  | X |  | 例如，從 Facebook 到應用。 |

### <a name="session-management"></a>工作階段管理

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| SSO 工作階段提供者 |  |  | X |  |
| 外部登入工作階段提供者 |  |  | X |  |
| SAML SSO 工作階段提供者 |  |  | X |  |
| 預設 SSO 會話提供程式 |  |  | X |  |

### <a name="security"></a>安全性

| 功能 | 部署 | 預覽 | GA | 注意 |
|-------- | :-----------: | :-------: | :--: | ----- |
| 原則機碼產生，手動上傳 |  |  | X |  |
| 原則機碼 - RSA/憑證，密碼 |  |  | X |  |
| 策略上傳 |  |  | X |  |

### <a name="developer-interface"></a>開發人員介面

| 功能 | 部署 | 預覽 | GA | 注意 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure 入口網站 IEF UX |  |  | X |  |
| Application Insights UserJourney 記錄 |  | X |  | 用於開發過程中的故障排除。  |
| 應用程式見解事件日誌（通過業務流程步驟） |  | X |  | 用於監視生產中的使用者流。 |

## <a name="next-steps"></a>後續步驟

詳細瞭解[自訂策略和使用者流的差異](custom-policy-overview.md)。
