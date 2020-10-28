---
title: 什麼是 Azure Active Directory 中的 B2B 共同作業？
description: Azure Active Directory B2B 共同作業支援來賓使用者存取權，以便您可以安全地共用資源，並與外部夥伴共同作業。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d10eb3334154f4915aa0bddd488ddc78bde06805
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897257"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>什麼是 Azure Active Directory B2B 中的來賓使用者存取權？

Azure Active Directory (Azure AD) 企業對企業 (B2B) 共同作業是外部身分識別中的一項功能，可讓您邀請來賓使用者與您的組織共同作業。 透過企業對企業 (B2B) 共同作業，您可以與來自任何其他組織的來賓使用者安全地共用公司的應用程式與服務，同時持續控制您公司的資料。 即使沒有 Azure AD 或 IT 部門，也可以安全地與外部合作夥伴 (無論大型或小型) 合作。 透過簡單的邀請和兌換程序，夥伴可以使用自己的認證來存取您的公司資源。 開發人員可以使用 Azure AD 企業對企業 API 來自訂邀請程序，或撰寫自助式註冊入口網站等應用程式。 如需來賓使用者的相關授權和定價資訊，請參閱 [Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)。  

   > [!IMPORTANT]
   > **自 2021 年 3 月 31 日起** ，Microsoft 將不再支援兌換邀請，而是建立適用於 B2B 共同作業案例的非受控 Azure AD 帳戶和租用戶。 在準備過程中，我們鼓勵客戶選擇使用[電子郵件一次性密碼驗證](one-time-passcode.md)。 我們歡迎您提供此公開預覽功能的意見反應，而且期待能建立更多共同作業的方式。

## <a name="collaborate-with-any-partner-using-their-identities"></a>使用他們的身分識別與任何夥伴共同作業

使用 Azure AD B2B，夥伴可使用自己的身分識別管理解決方案，因此您的組織不會產生外部系統管理額外負荷。 來賓使用者使用自己的公司、學校或社交身分識別登入您的應用程式與服務。

- 夥伴使用他們自己的身分識別與認證；Azure AD 不是必需的。
- 您不需要管理外部帳戶或密碼。
- 您不需要同步處理帳戶或管理帳戶的生命週期。  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>從 Azure AD 入口網站輕鬆地邀請來賓使用者

系統管理員可以在 Azure 入口網站中輕鬆地將來賓使用者新增到您的組織。

- 在 Azure AD 中[建立新的來賓使用者](b2b-quickstart-add-guest-users-portal.md)，類似於加入新使用者的方式。
- 將來賓使用者指派給應用程式或群組。
- 傳送包含兌換連結的邀請電子郵件，也可以傳送您要共用之應用程式的直接連結。

![顯示 [新增來賓使用者] 進入頁面的螢幕擷取畫面](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- 來賓使用者只需幾個簡單的[兌換步驟](redemption-experience.md)即可登入。

![顯示 [檢閱權限] 頁面的螢幕擷取畫面](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>使用原則來安全地共用您的應用程式與服務

您可以使用授權原則保護您的公司內容。 可以強制執行條件式存取原則，例如多重要素驗證：

- 在租用戶層級。
- 在應用程式層級。
- 針對特定來賓使用者來保護公司應用程式與資料。

![顯示 [條件式存取] 選項的螢幕擷取畫面](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>讓應用程式與群組擁有者管理自己的來賓使用者

您可以將來賓使用者管理委派給應用程式擁有者，讓他們可以將來賓使用者直接新增到他們想要共用的任何應用程式，無論它是否為 Microsoft 應用程式。

- 系統管理員可設定自助式應用程式與群組管理。
- 非系統管理員必須使用其[存取面板](https://myapps.microsoft.com)將來賓使用者新增到應用程式或群組。

![顯示來賓使用者存取面板的螢幕擷取畫面](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>自訂 B2B 來賓使用者的上線體驗

讓您的外部合作夥伴以針對您組織需求量身打造的方式上線使用。

- 使用 [Azure AD 權利管理](../governance/entitlement-management-overview.md)來設定原則，以[管理外部使用者的存取權](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)。
- 使用 [B2B 共同作業邀請 API](/graph/api/resources/invitation) 來自訂您的上線體驗。

## <a name="integrate-with-identity-providers"></a>與識別提供者整合

Azure AD 支援 Facebook、Microsoft 帳戶、Google 或企業識別提供者之類的外部識別提供者。 您可以設定與識別提供者的同盟，以便外部使用者使用其現有社交或企業帳戶進行登入，而不只是為您的應用程式建立新的帳戶。 深入了解外部身分識別的識別提供者。

![顯示 [識別提供者] 頁面的螢幕擷取畫面](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>建立自助式註冊使用者流程 (預覽)

透過自助式註冊使用者流程，您可以為想要存取您應用程式的外部使用者建立註冊體驗。 在註冊流程中，您可以為不同的社交或企業識別提供者提供選項，並收集使用者的相關資訊。 了解[自助式註冊及如何進行設定](self-service-sign-up-overview.md)。

您也可以使用 [API 連接器](api-connectors-overview.md)，將您的自助式註冊使用者流程與外部雲端系統整合。 您可以使用自訂核准工作流程進行連線、執行身分識別驗證、驗證使用者提供的資訊等等。

![顯示使用者流程頁面的螢幕擷取畫面](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>後續步驟

- [外部身分識別定價](external-identities-pricing.md)
- [在入口網站中新增 B2B 共同作業來賓使用者](add-users-administrator.md)
- [了解邀請兌換程序](redemption-experience.md)