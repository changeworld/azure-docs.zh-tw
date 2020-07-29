---
title: Azure AD B2C 的 ISV 合作夥伴資源庫
titleSuffix: Azure AD B2C
description: 瞭解如何與我們的 ISV 合作夥伴整合，依據您的需求量身打造您的使用者體驗。 我們的合作夥伴網路延伸了我們的解決方案功能;啟用 MFA、保護客戶驗證、以角色為基礎的存取控制;透過身分識別驗證來對抗詐騙。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 986abbe6c124ce1323add5b8e854973416dfcbf4
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371204"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV 合作夥伴

我們的 ISV 合作夥伴網路延伸了我們的解決方案功能，協助您建立順暢的使用者體驗。 透過 Azure AD B2C，您可以與 ISV 合作夥伴整合以啟用多重要素驗證方法、啟用安全客戶驗證（SCA）、執行以角色為基礎的存取控制，以及藉由身分識別驗證和校對來對抗詐騙。 使用我們的詳細範例逐步解說，瞭解如何整合應用程式與下列 ISV 合作夥伴。

>[!NOTE]
>[GitHub 上的 Azure Active Directory B2C 的社區網站](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)也提供來自社區的範例自訂原則。

## <a name="integration-isv-partners"></a>整合 ISV 合作夥伴

| ISV 合作夥伴 | 描述與整合逐步解說  |
| :--- | :--- |
| ![標誌](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md)是一種詐騙預防解決方案提供者，可協助組織抵禦 bot 攻擊、帳戶接管攻擊，以及詐騙帳戶的下機會。
| ![標誌](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md)是身分識別驗證和校對提供者，會根據使用者屬性來執行風險評估，以防止詐騙。|
| ![標誌](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md)是一種身分識別驗證和校對提供者，具有識別碼驗證解決方案、詐騙預防解決方案、合規性解決方案等等。|
| ![標誌](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md)是一種電子識別、驗證及信任服務（eiDAS）相容的數位識別碼解決方案，可讓使用者安全地登入，而不需要卡片讀取器、密碼、雙因素驗證和多個 PIN 碼。 |
| ![標誌](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md)是一種分析和身分識別驗證提供者，可驗證使用者識別，並根據使用者的裝置提供完整的風險評估。 |
| ![標誌](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md)整合可協助您安全地登入，並啟用無密碼 authentication、多重要素驗證和數位授權掃描。|
| ![標誌](./media/partner-gallery/twilio-logo.png) | [Twilio Verify 應用程式](./partner-twilio.md)提供多個解決方案，可透過 SMS 單次密碼（OTP）、以時間為基礎的單次密碼（TOTP）和推播通知來啟用多重要素驗證（MFA），並符合適用于付款服務指示詞2（PSD2）的 SCA 需求。|
| ![標誌](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md)是以使用者輸入模式為基礎的身分識別驗證和校對提供者，可提供強制執行多重要素驗證的識別碼驗證解決方案，並協助符合適用于付款服務指示詞2（PSD2）的 SCA 需求。 |

## <a name="next-steps"></a>後續步驟

選取上表中的合作夥伴，以瞭解如何將其解決方案與 Azure AD B2C 整合。

其他資訊：

- [GitHub 上的 B2C 社區網站](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)
- [Azure AD B2C 上的自訂原則](custom-policy-overview.md)
