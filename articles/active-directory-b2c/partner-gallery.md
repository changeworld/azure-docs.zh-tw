---
title: Azure AD B2C 的 ISV 合作夥伴資源庫
titleSuffix: Azure AD B2C
description: 瞭解如何與 ISV 合作夥伴整合，以根據您的需求量身打造您的終端使用者體驗。 我們的合作夥伴網路延伸了解決方案功能;啟用 MFA、保護客戶驗證、角色型存取控制;透過身份驗證驗證來對抗詐騙。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9aca01bae7911d2b6234d48bedc14b116e911a79
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675367"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV 合作夥伴

我們的 ISV 合作夥伴網路延伸了解決方案功能，以協助您建立順暢的終端使用者體驗。 有了 Azure AD B2C，您可以與 ISV 合作夥伴整合，以啟用多重要素驗證 (MFA) 方法、進行角色型存取控制、啟用身分識別驗證和證明、透過 bot 偵測和詐騙保護來改善安全性，以及符合付款服務指示詞 2 (PSD2) 安全的客戶驗證 (SCA) 需求。 使用我們詳細的範例逐步解說，以瞭解如何整合應用程式與 ISV 合作夥伴。

>[!NOTE]
>[GitHub 上的 Azure Active Directory B2C 社區網站](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)也提供來自社區的自訂原則範例。

## <a name="identity-verification-and-proofing"></a>身分識別驗證和校對

有了 Azure AD B2C 合作夥伴，客戶可以在允許帳戶註冊或存取之前，先啟用使用者的身分識別驗證和驗證。 身分識別驗證和校對可以檢查檔、知識資訊和活動。

高層級架構圖表會說明流程。

![顯示身分識別證明流程的圖表](./media/partner-gallery/third-party-identity-proofing.png)

Microsoft 與下列 Isv 進行身分識別驗證和驗證的合作夥伴。

| ISV 合作夥伴 | 描述和整合逐步解說 |
|:-------------------------|:--------------|
|![Experian 標誌的螢幕擷取畫面。](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) 是身分識別驗證和證明提供者，可根據使用者屬性來執行風險評定，以防止詐騙。 |
|![IDology 標誌的螢幕擷取畫面。](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) 是身分識別驗證和證明提供者，具有識別碼驗證解決方案、詐騙防護解決方案、合規性解決方案及其他資訊。|
|![Jumio 標誌的螢幕擷取畫面。](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) 是一種識別碼驗證服務，可啟用即時自動化識別碼驗證，保護客戶資料。 |
|![無索引鍵標誌的螢幕擷取畫面。](./media/partner-gallery/keyless-logo.png) | [無索引鍵](./partner-keyless.md) 是一項識別碼驗證服務，以臉部生物特徵辨識掃描的形式提供驗證，並免除詐騙、網路釣魚和認證的重複使用。
| ![LexisNexis 標誌的螢幕擷取畫面。](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) 是一種分析和身分識別驗證提供者，可驗證使用者的身分識別，並根據使用者的裝置提供完整的風險評估。 |
| ![Onfido 標誌的螢幕擷取畫面](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) 是一種檔識別碼和臉部生物特徵辨識驗證解決方案，可讓公司即時符合 *您的客戶* 和身分識別需求。  |

## <a name="mfa-and-passwordless-authentication"></a>MFA 和無密碼 authentication

Microsoft 與下列 Isv 進行 MFA 和無密碼 authentication 的合作夥伴。

| ISV 合作夥伴 | 描述和整合逐步解說 |
|:-------------------------|:--------------|
| ![Hypr 標誌的螢幕擷取畫面](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) 是一種無密碼 authentication 提供者，可將密碼取代為公開金鑰加密，以免除詐騙、網路釣魚和認證的重複使用。 |
| ![Itsme 標誌的螢幕擷取畫面](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) 是一種電子識別、驗證和信任服務， () eiDAS 符合規範的數位識別碼解決方案，可讓使用者安全地登入，而不需要卡片讀卡機、密碼、雙因素驗證，以及多個 PIN 碼。 |
| ![尼維斯標誌的螢幕擷取畫面](./media/partner-gallery/nevis-logo.png) | [尼維斯](./partner-nevis.md) 會啟用無密碼 authentication，並提供行動優先、完整品牌的終端使用者體驗，並提供尼維斯存取應用程式，以進行強式客戶驗證並符合 PSD2 交易需求。 |
| ![Trusona 標誌的螢幕擷取畫面](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) 整合可協助您安全地登入，並啟用無密碼 AUTHENTICATION、MFA 和數位授權掃描。 |
| ![Twilio 標誌的螢幕擷取畫面。](./media/partner-gallery/twilio-logo.png) | [Twilio Verify 應用程式](./partner-twilio.md) 提供多個解決方案，可透過 SMS 單次密碼 (OTP) 、時間型單次密碼 (TOTP) 和推播通知，以及符合 PSD2 的 SCA 需求來啟用 MFA。 |
| ![TypingDNA 標誌的螢幕擷取畫面](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) 藉由分析使用者的輸入模式來啟用強式客戶驗證。 它可協助公司啟用無訊息 MFA，並符合 PSD2 的 SCA 需求。 |
| ![Whoiam 標誌的螢幕擷取畫面](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) 是品牌身分識別管理系統， (BRIMS) 應用程式，可讓組織依語音、SMS 和電子郵件驗證其使用者群。 |

## <a name="role-based-access-control"></a>角色型存取控制 
 
Microsoft 與下列 Isv 進行角色型存取控制的合作夥伴。

| ISV 合作夥伴 | 描述和整合逐步解說 |
|:-------------------------|:--------------|
| ![N8identity 標誌的螢幕擷取畫面](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) 是身分識別即服務治理平臺，可提供解決客戶帳戶遷移和客戶服務要求的解決方案， (在 Microsoft Azure 上執行的 CSR) 管理。 |
| ![Saviynt 標誌的螢幕擷取畫面](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) 雲端原生平臺可透過智慧型分析和跨應用程式整合提高安全性、合規性和治理，以簡化 IT 現代化。 |

## <a name="security"></a>安全性

Microsoft 與下列 Isv 合作提供安全性。

| ISV 合作夥伴 | 描述和整合逐步解說 |
|:-------------------------|:--------------|
| ![Arkose lab 標誌的螢幕擷取畫面](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) 是一項詐騙防護解決方案提供者，可協助組織防止 bot 攻擊、帳戶接管攻擊，以及詐騙帳戶。 |
| ![分層標誌的螢幕擷取畫面](./media/partner-gallery/strata-logo.png) | [分層](./partner-strata.md) 藉由強制執行一致的存取原則、讓身分識別保持同步，讓您能夠輕鬆地將應用程式從舊版身分識別系統轉換成以標準為基礎的驗證以及 Azure AD B2C 所提供的存取控制，來提供內部部署應用程式的安全混合式存取。 |
| ![Zscaler 標誌的螢幕擷取畫面](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) 提供以原則為基礎、安全的私用應用程式和資產存取，而不會有 VPN 的成本、麻煩或安全性風險。 |

## <a name="additional-information"></a>其他資訊

- [Azure AD B2C 中的自訂原則](./custom-policy-overview.md)

- [Azure AD B2C 中的自訂原則入門](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>下一步

在所述的表格中選取合作夥伴，以瞭解如何將解決方案與 Azure AD B2C 整合。