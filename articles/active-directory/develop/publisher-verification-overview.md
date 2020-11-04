---
title: 發行者驗證概觀 - Microsoft 身分識別平台 | Azure
description: 提供 Microsoft 身分識別平臺的發行者驗證程式總覽。 列出優點、計畫需求和常見問題。 當應用程式標示為「發行者已驗證」時，表示發行者已使用完成驗證程序的 Microsoft 合作夥伴網路 (MPN) 帳戶來通過身分驗證，並將此 MPN 帳戶與應用程式註冊相關聯。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 1e913e3a5356ad7f49d8b3066f5bd3da7eddd2c2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308782"
---
# <a name="publisher-verification"></a>發行者驗證

發行者驗證可協助系統管理員和終端使用者瞭解應用程式開發人員與 Microsoft 身分識別平臺整合的真實性。 

> [!VIDEO https://www.youtube.com/embed/IYRN2jDl5dc]

當應用程式標示為「發行者已驗證」時，表示發行者已使用完成[驗證](/partner-center/verification-responses)程序的 [Microsoft 合作夥伴網路 (MPN)](https://partner.microsoft.com/membership) 帳戶來通過身分驗證，並將此 MPN 帳戶與應用程式註冊相關聯。 

Azure AD 同意提示和其他畫面上會出現一個藍色的「已驗證」徽章：![同意提示](./media/publisher-verification-overview/consent-prompt.png)

這項功能主要是針對使用 [OAuth 2.0 和 OpenID Connect](active-directory-v2-protocols.md) 與 [Microsoft 身分識別平台](v2-overview.md)來建置多租用戶應用程式的開發人員而設計。 這些應用程式可以使用 OpenID Connect 將使用者登入，或使用 OAuth 2.0 來要求使用 API (例如 [Microsoft Graph](https://developer.microsoft.com/graph/)) 存取資料。

## <a name="benefits"></a>優點
發行者驗證提供下列優點：
- **為客戶提高透明度和降低風險** - 這項功能可協助客戶了解組織中使用的哪些應用程式是由他們信任的開發人員所發行。 

- **改善標章** - 「已驗證」徽章會出現在 Azure AD 的 [同意提示](application-consent-experience.md)、企業應用程式頁面，以及使用者和系統管理員所使用的其他 UX 表面。 

- **企業採用更流暢** -系統管理員可以設定 [使用者同意原則](../manage-apps/configure-user-consent.md)，並將發行者驗證狀態設定為其中一個主要原則準則。

> [!NOTE]
> 自2020年11月起，使用者將無法再將同意授與最新註冊的多租使用者應用程式，而不需要經過驗證的發行者。 這會套用至在11月 2020 8 日之後註冊的應用程式，使用 OAuth 2.0 來要求基本登入和讀取使用者設定檔以外的許可權，並要求來自不同租使用者的使用者同意，而不是應用程式註冊所在的租使用者。 同意畫面上會顯示警告，通知使用者這些應用程式具有風險，而且來自未驗證的發行者。    

## <a name="requirements"></a>需求
發行者驗證有幾個必要條件，而許多 Microsoft 合作夥伴已完成部分條件。 其中包括： 

-  有效 [Microsoft 合作夥伴網路](https://partner.microsoft.com/membership)帳戶的 MPN 識別碼，該帳戶必須已完成[驗證](/partner-center/verification-responses)程序。 此 MPN 帳戶必須是您組織的[合作夥伴通用帳戶 (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga)。 

-  在 Azure AD 租使用者中註冊的應用程式，並已設定 [發行者網域](howto-configure-publisher-domain.md) 。

-  在 MPN 帳戶驗證期間使用的電子郵件地址網域，必須符合在應用程式上設定的發行者網域，或新增至 Azure AD 租使用者的 DNS 驗證 [自訂網域](../fundamentals/add-custom-domain.md) 。 

-  執行驗證的使用者必須獲得授權，才能對 Azure AD 中的應用程式註冊和合作夥伴中心內的 MPN 帳戶進行變更。 

    -  在 Azure AD 此使用者必須是下列其中一個 [角色](../roles/permissions-reference.md)的成員：應用程式系統管理員、雲端應用程式系統管理員或全域管理員。 

    -  在合作夥伴中心內，此使用者必須擁有下列其中一種[角色](/partner-center/permissions-overview)：MPN 管理員、帳戶管理員或全域管理員 (這是在 Azure AD 中主控的共用角色)。
    
-  執行驗證的使用者必須使用 [多重要素驗證](../authentication/howto-mfa-getstarted.md)登入。

-  發行者同意[適用於開發人員的 Microsoft 身分識別平台使用規定](/legal/microsoft-identity-platform/terms-of-use)。

已符合這些必要條件的開發人員，可以在幾分鐘內完成驗證。 如果尚未符合需求，可免費進行設定。 

## <a name="frequently-asked-questions"></a>常見問題集 
以下是有關發行者驗證計畫的一些常見問題。 如需有關需求和流程的常見問題集，請參閱[將應用程式標示為發行者已驗證](mark-app-as-publisher-verified.md)。

- **發行者驗證 __未__ 提供哪些資訊？**  當應用程式標示為「發行者已驗證」時，其不會指出應用程式或其發行者是否已完成任何特定認證、遵守業界標準或遵循最佳做法等等。但其他 Microsoft 程式會提供這些資訊，包括 [Microsoft 365 應用程式認證](/microsoft-365-app-certification/overview)。

- **這會產生多少費用？需要任何授權嗎？** Microsoft 不會向開發人員收取發行者驗證的費用，也不需要任何特定的授權。 

- **這與 Microsoft 365 發行者證明有何關聯？與 Microsoft 365 應用程式認證有關聯嗎？** 這些都是互補的程式，可讓開發人員用來建立可放心供客戶使用的可靠應用程式。 發行者驗證是此程序的第一個步驟，而且只要開發人員建立符合上述準則的應用程式，他們就應該完成此步驟。 

  同時與 Microsoft 365 整合的開發人員可以從這些程式獲得額外好處。 如需詳細資訊，請參閱 [Microsoft 365 發行者證明](/microsoft-365-app-certification/docs/attestation)和 [Microsoft 365 應用程式認證](/microsoft-365-app-certification/docs/certification)。 

- **這與 Azure AD 應用程式庫相同嗎？** 不同，發行者驗證是 [Azure Active Directory 應用程式庫](v2-howto-app-gallery-listing.md)的互補程式，但是是個別的程式。 無論是否參與該程式，符合上述準則的開發人員都應該獨立完成發行者驗證流程。 

## <a name="next-steps"></a>後續步驟
* 了解如何[將應用程式標示為發行者已驗證](mark-app-as-publisher-verified.md)。
* 針對發行者驗證進行[疑難排解](troubleshoot-publisher-verification.md)。
