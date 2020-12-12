---
title: B2B 共同作業常見問題-Azure Active Directory |Microsoft Docs
description: 取得 Azure Active Directory B2B 共同作業常見問題集的解答
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 09/23/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90cc15dc3944efd82d025df1bb5072c57346055e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355625"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory B2B 共同作業常見問題集

這些有關 Azure Active Directory (Azure AD) 企業對企業 (B2B) 共同作業的常見問題集 (FAQ) 會定期更新來包含新的主題。

> [!IMPORTANT]
> - 自 **2021 年1月4日起**，Google 將 [淘汰 web 服務登入支援](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)。 如果您要使用 Google 同盟或使用 Gmail 的自助式註冊，您應該 [測試企業營運原生應用程式的相容性](google-federation.md#deprecation-of-webview-sign-in-support)。
> - **自 2021 年 3 月 31 日起**，Microsoft 將不再支援兌換邀請，而是建立適用於 B2B 共同作業案例的非受控 Azure AD 帳戶和租用戶。 在準備過程中，我們鼓勵客戶選擇使用[電子郵件一次性密碼驗證](one-time-passcode.md)。 我們歡迎您提供此公開預覽功能的意見反應，而且期待能建立更多共同作業的方式。

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>是否可以自訂登入頁面，讓我們的 B2B 共同作業來賓使用者感到更直覺式？
當然！ 請參閱我們[有關這項功能的部落格文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)。 如需有關如何自訂組織登入頁面的詳細資訊，請參閱[將公司商標新增至登入和存取面板頁面](../fundamentals/customize-branding.md)。

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B 共同作業的使用者可以存取 SharePoint Online 和 OneDrive 嗎？
是。 不過，在 SharePoint Online 中使用人員選擇器搜尋現有來賓使用者的功能，預設為 **關閉**。 若要開啟搜尋現有來賓使用者的選項，請將 **ShowPeoplePickerSuggestionsForGuestUsers** 設定為 **On**。 您可以在租用戶等級或網站集合等級開啟這項設定。 您可以使用 Set-SPOTenant 和 Set-SPOSite Cmdlet 變更此設定。 透過這些 Cmdlet，成員可以搜尋目錄中所有現有的來賓使用者。 租用戶範圍中的變更不會影響已佈建的 SharePoint Online 網站。

### <a name="is-the-csv-upload-feature-still-supported"></a>是否仍支援 CSV 上傳功能？
是。 如需有關使用 .csv 檔案上傳功能的詳細資訊，請參閱[這個 PowerShell 範例](code-samples.md)。

### <a name="how-can-i-customize-my-invitation-emails"></a>如何自訂我的邀請電子郵件？
您可以使用 [B2B 邀請 API](customize-invitation-api.md) 自訂關於邀請者程序的幾乎一切事項。

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>來賓使用者是否可以重設其多重要素驗證方法？
是。 來賓使用者集可以重設其多重要素驗證方法，作法與一般使用者一樣。

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>哪個組織負責多重要素驗證授權？
邀請方組織執行多重要素驗證。 邀請方組織必須確定組織有足夠的授權給使用多重要素驗證的 B2B 使用者。

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>如果夥伴組織已設定多重要素驗證呢？ 我們可以信任其多重要素驗證，而不要使用我們自己的多重要素驗證嗎？
這項功能目前不受支援。 如果您組織資源的存取權需要多重要素驗證，夥伴組織將需要在您的 (邀請) 組織中註冊多重要素驗證。

### <a name="how-can-i-use-delayed-invitations"></a>我如何使用延遲的邀請？
組織可能想要新增 B2B 共同作業使用者，依需要將他們佈建至應用程式，然後送出邀請。 您可以使用 B2B 共同作業邀請 API 自訂登入工作流程。

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>能否在 Exchange 全域通訊清單中顯示來賓使用者？
是。 根據預設，來賓物件不會顯示在您組織的全域通訊清單中 (GAL) ，但您可以使用 Azure Active Directory PowerShell 來讓它們顯示。 [我可以在全域通訊清單中看到來賓物件嗎？](/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>我是否可以將來賓使用者設為受限的管理員？
當然。 如需詳細資訊，請參閱[將來賓使用者新增至角色](add-guest-to-role.md)。

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B 共同作業是否允許 B2B 使用者存取 Azure 入口網站？
除非使用者獲指派受限的系統管理員角色，否則 B2B 共同作業使用者將不需要存取 Azure 入口網站。 不過，獲派受限系統管理員角色的 B2B 共同作業使用者可以存取入口網站。 此外，如果未獲指派其中一個管理員角色的來賓使用者存取入口網站，使用者可能可以存取特定部份的體驗。 來賓使用者角色在目錄中具有某些權限。

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>我可以封鎖來賓使用者存取 Azure 入口網站嗎？

可以！ 您可以建立條件式存取原則，以封鎖所有來賓和外部使用者存取 Azure 入口網站。 當您設定此原則時，請小心避免不慎封鎖成員和管理員的存取權。

1. 以安全性系統管理員或條件式存取系統管理員的身分，登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站中，選取 [Azure Active Directory]  。 
3. 在 [ **管理**] 底下，選取 [ **安全性**]。
4. 在 [ **保護**] 下，選取 [ **條件式存取**]。 選取 [新增原則]。
5. 在 [ **新增** ] 頁面的 [ **名稱** ] 文字方塊中，輸入原則的名稱 (例如「封鎖來賓存取入口網站」 ) 。
6. 在 [指派]  底下，選取 [使用者和群組]  。
7. 在 [ **包含** ] 索引標籤上，選擇 [ **選取使用者和群組**]，然後選取 [ **所有來賓和外部使用者] (預覽)**。
9. 選取 [完成]  。
10. 在 [ **新增** ] 頁面的 [ **指派** ] 區段中，選取 [ **雲端應用程式] 或 [動作**]。
11. 在 [ **雲端應用程式] 或 [動作** ] 頁面上，選擇 [ **選取應用程式**]，然後選擇 [ **選取**]。
12. 在 [選取]  頁面上，選擇 [Microsoft Azure 管理]  ，然後選擇 [選取]  。
13. 在 [ **雲端應用程式] 或 [動作** ] 頁面上，選取 [ **完成**]。

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Azure AD B2B 共同作業是否支援多重要素驗證和取用者電子郵件帳戶？
是。 Azure AD B2B 共同作業支援多重要素驗證和取用者電子郵件帳戶。

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>您是否支援 Azure AD B2B 共同作業使用者的密碼重設？
如果您的 Azure AD 租用戶是使用者的主目錄，您可以從 Azure 入口網站[重設使用者密碼](../fundamentals/active-directory-users-reset-password-azure-portal.md)。 但您無法直接重設使用由其他 Azure AD 目錄或外部身分識別提供者所管理的帳戶進行登入的來賓使用者的密碼。 只有來賓使用者或使用者主目錄中的系統管理員可以重設密碼。 以下是一些如何重設來賓使用者密碼的範例：
 
* 使用 Microsoft 帳戶 (例如guestuser@live.com) 登入的來賓使用者，可以使用 Microsoft 帳戶自助式密碼重設 (SSPR) 功能重設自己的密碼。 請參閱[如何重設 Microsoft 帳戶密碼](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password)。
* 使用 Google 帳戶或其他外部身分識別提供者登入的來賓使用者，可以使用其身分識別提供者的 SSPR 方法重設自己的密碼。 例如，使用 Google 帳戶 guestuser@gmail.com 的來賓使用者可以依照[變更或重設密碼](https://support.google.com/accounts/answer/41078)中的指示重設自己的密碼。
* 如果身分識別租用戶是 Just-in-time (JIT) 或 「病毒式」租用戶 (表示它是獨立、非受控的 Azure 租用戶)，只有來賓使用者才能重設自己的密碼。 組織有時會[接管病毒式租用戶的管理](../enterprise-users/domains-admin-takeover.md)，這些是員工使用其工作電子郵件地址來註冊服務時所建立的租用戶。 在組織接管病毒式租用戶之後，只有該組織的系統管理員可以重設使用者的密碼或啟用 SSPR。 如有必要，作為發出邀請的組織，您可以從目錄中移除來賓使用者帳戶，並重新傳送邀請。

* 如果來賓使用者的主目錄是您的 Azure AD 租用戶，您可以重設使用者的密碼。 例如，您可能已建立使用者或從您的內部部署 Active Directory 同步使用者，並將他們的 UserType 設為 Guest。 因為這位使用者位於您的主目錄中，所以您可以從 Azure 入口網站重設其密碼。

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics 365 是否提供 Azure AD B2B 共同作業的線上支援？
是，Dynamics 365 提供 Azure AD B2B 共同作業的線上支援。 如需詳細資訊，請參閱 Dynamics 365 文章[透過 Azure Active Directory B2B 共同作業邀請使用者](/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration)。

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>什麼是新建立的 B2B 共同作業使用者之初始密碼存留期？
Azure AD 有一組固定的字元、密碼強度，以及帳戶鎖定需求，可同樣適用於所有 Azure AD 雲端使用者帳戶。 雲端使用者帳戶是不與其他身分識別提供者聯盟的帳戶，例如 
* Microsoft 帳戶
* Facebook
* Active Directory Federation Services
* 其他雲端租用戶 (適用於 B2B 共同作業)

針對同盟帳戶，密碼原則取決於內部部署租用中套用的原則，以及使用者的 Microsoft 帳戶設定。

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>組織可能想要在其應用程式中讓租用戶使用者和來賓使用者有不同的體驗。 有適用於此案例的標準指引嗎？ 識別提供者宣告的存在是否為應使用的正確模型？
來賓使用者可以使用任何識別提供者進行驗證。 如需詳細資訊，請參閱 [B2B 共同作業使用者的屬性](user-properties.md)。 使用 **UserType** 屬性來決定使用者體驗。 權杖中目前不包含 **UserType** 宣告。 應用程式應該使用 Microsoft Graph API 來查詢使用者的目錄，並取得 UserType。

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>哪裡可以找到 B2B 共同作業社群，以分享解決方案和提交構想？
我們會時常聆聽您對改進 B2B 共同作業的意見反應。 請分享您的使用者案例、最佳做法，以及您喜歡 Azure AD B2B 共同作業的什麼功能。 請前往 [Microsoft 技術社群](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)參與討論。
 
我們也邀請您前往 [B2B 共同作業構想](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas)提交您的構想和票選未來的功能。

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>我們是否可以傳送自動兌換的邀請，讓使用者「隨時出發」？ 還是使用者一定要按一下才能前往兌換 URL？
您可以使用 UI、PowerShell 指令碼或 API，邀請夥伴組織中的其他使用者。 然後，您可以將共用應用程式的直接連結傳送給來賓使用者。 在大部分情況下，不再需要開啟電子郵件邀請，然後按一下兌換 URL。 請參閱 [Azure Active Directory B2B 共同作業邀請兌換](redemption-experience.md)。

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>當受邀的合作夥伴使用同盟來新增自己的內部部署驗證時，B2B 共同作業如何運作？
如果合作夥伴具有與內部部署驗證基礎結構同盟的 Azure AD 租用戶，就會自動達成內部部署單一登入 (SSO)。 如果合作夥伴沒有 Azure AD 租用戶，則系統會為新的使用者建立 Azure AD 帳戶。 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>我以為 Azure AD B2B 不接受 gmail.com 和 outlook.com 電子郵件地址，而 B2C 用於這些類型的帳戶？
在支援哪些身分識別這方面，我們將會消除 B2B 與企業對消費者 (B2C) 共同作業之間的差異。 使用的身分識別並不是用來選擇要使用 B2B 或 B2C 的好理由。 如需有關選擇協同作業選項的資訊，請參閱[比較 Azure Active Directory 的 B2B 共同作業和 B2C](compare-with-b2c.md)。

### <a name="can-an-azure-ad-b2c-local-account-be-invited-to-an-azure-ad-tenant-for-b2b-collaboration"></a>是否可以將 Azure AD B2C 本機帳戶邀請到 Azure AD 租使用者以進行 B2B 共同作業？
否。 Azure AD B2C 本機帳戶只能用來登入 Azure AD B2C 租使用者。 帳戶無法用來登入 Azure AD 的租使用者。 不支援針對 B2B 共同作業邀請 Azure AD B2C 本機帳戶至 Azure AD 租使用者。

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>哪些應用程式和服務支援 Azure B2B 來賓使用者？
所有 Azure AD 整合的應用程式都可以支援 Azure B2B 來賓使用者，但他們必須使用設定為租使用者的端點來驗證來賓使用者。 您可能也需要在使用者向應用程式驗證時所發出的 SAML 權杖中[自訂宣告](claims-mapping.md)。 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>如果我們的合作夥伴沒有多重要素驗證，我們是否可以對 B2B 來賓使用者強制使用多重要素驗證？
是。 如需詳細資訊，請參閱 B2B 共同作業 [使用者的條件式存取](conditional-access.md)。

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>在 SharePoint 中，您可以針對外部使用者定義「允許」或「拒絕」清單。 這在 Azure 中辦得到嗎？
是。 Azure AD B2B 共同作業支援允許清單和拒絕清單。 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>我們需要哪些使用權才能使用 Azure AD B2B？
如需您的組織使用 Azure AD B2B 的授權的相關資訊，請參閱外部身分識別 [定價](external-identities-pricing.md)。

### <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)