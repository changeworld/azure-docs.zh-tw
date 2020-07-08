---
title: Azure Active Directory B2C 的常見問題（FAQ）
description: 有關 Azure Active Directory B2C 常見問題的解答。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 08a1d655d19b5e7deb81e42ca5bebdfe4f18aeef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84297899"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C：常見問題集 (FAQ)

此頁面會回答有關 Azure Active Directory B2C （Azure AD B2C）的常見問題。 請隨時回來查看最新消息。

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>為什麼無法存取 Azure 入口網站中的 Azure AD B2C 擴充功能？

Azure AD 擴充功能無法運作有兩個常見原因。 Azure AD B2C 要求您在目錄中的使用者角色是全域管理員。 如果您認為自己具有存取權，請連絡您的管理員。 如果您有全域管理員權限，請確定您是在 Azure AD B2C 目錄中，而非 Azure Active Directory 目錄。 可至此查看[建立 Azure AD B2C 租用戶](tutorial-create-tenant.md)的指示。

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>我可以在以員工為主的現有 Azure AD 租用戶中使用 Azure AD B2C 功能嗎？

Azure AD 和 Azure AD B2C 為個別的產品供應項目，無法共存於同一個租用戶。 一個 Azure AD 租用戶代表一個組織。 一個 Azure AD B2C 租用戶代表一組要用於信賴憑證者應用程式的身分識別。 藉由在**Azure AD B2C > 身分識別提供者**或自訂原則中**新增 OpenID connect 提供者**，Azure AD B2C 可以與 Azure AD 允許組織中的員工驗證。

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>我可以使用 Azure AD B2C 來提供 Office 365 的社交登入 (Facebook 和 Google+) 嗎？

Azure AD B2C 無法用來驗證 Microsoft Office 365 的使用者。 Azure AD 是 Microsoft 用來管理 SaaS 應用程式存取權的解決方案，而且它具有專為此用途而設計的功能，例如授權和條件式存取。 Azure AD B2C 提供身分識別和存取管理平台來建置 web 和行動應用程式。 當 Azure AD B2C 設定為與 Azure AD 租用戶結成同盟時，Azure AD 租用戶會管理員工如何存取依賴 Azure AD B2C 的應用程式。

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C 中的本機帳戶是什麼？ 與 Azure AD 中的工作或學校帳戶有何不同？

在 Azure AD 租用戶中，屬於租用戶的使用者是以 `<xyz>@<tenant domain>` 格式的電子郵件地址登入。 `<tenant domain>` 是租用戶中已驗證的其中一個網域，或初始的 `<...>.onmicrosoft.com` 網域。 這種類型的帳戶就是工作或學校帳戶。

在 Azure AD B2C 租用戶中，大部分應用程式都希望使用者以任意的電子郵件地址登入 (例如 joe@comcast.net、bob@gmail.com、sarah@contoso.com 或 jim@live.com)。 這種類型的帳戶就是本機帳戶。 我們也支援使用任意的使用者名稱作為本機帳戶 (例如，joe、bob、sarah 或 jim)。 在 Azure 入口網站中設定 Azure AD B2C 的識別提供者時，您可以從這兩個本機帳戶類型中選擇一個。 在 Azure AD B2C 租使用者中，選取 [身分**識別提供者**]，選取 [**本機帳戶**]，然後選取 [使用者**名稱**]。

應用程式的使用者帳戶可以透過註冊使用者流程、註冊或登入使用者流程、Microsoft Graph API，或在 Azure 入口網站中建立。

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>你們現在支援哪些社交身分識別提供者？ 你們打算在未來支援哪些？

我們目前支援數個社交識別提供者，包括 Amazon、Facebook、GitHub （預覽）、Google、LinkedIn、Microsoft 帳戶（MSA）、QQ （預覽）、Twitter、WeChat （預覽）和 Weibo （預覽）。 我們會根據客戶需求，評估新增其他熱門社交識別提供者的支援。

Azure AD B2C 也支援[自訂原則](custom-policy-overview.md)。 自訂原則可讓您為任何支援[OpenID connect](https://openid.net/specs/openid-connect-core-1_0.html)或 SAML 的身分識別提供者建立自己的原則。 查看我們的[自訂原則入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)，開始使用自訂原則。

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>我可以設定範圍，以便從各種社交身分識別提供者收集取用者的詳細資訊嗎？

否。 我們支援的一組社交身分識別提供者所使用的預設範圍如下：

* Facebook: email
* Google+: email
* Microsoft 帳戶︰openid 電子郵件設定檔
* Amazon: profile
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>我的應用程式必須在 Azure 上執行，才能使用 Azure AD B2C 嗎？

否，您可以將應用程式裝載於任何地方 (雲端或內部部署)。 只要能夠在公開存取的端點上傳送和接收 HTTP 要求，就可以與 Azure AD B2C 互動。

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>我有多個 Azure AD B2C 租用戶。 如何在 Azure 入口網站上管理它們？

在 Azure 入口網站的左側功能表中開啟 'Azure AD B2C' 之前，您必須切換到需要管理的目錄。 在 Azure 入口網站右上角按一下您的身分識別來切換目錄，然後選擇出現在下拉式清單中的目錄。

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>我如何自訂 Azure AD B2C 傳送的驗證電子郵件 (內容和 [寄件者:] 欄位)？

您可以使用 [公司商標功能](../active-directory/fundamentals/customize-branding.md) 自訂驗證電子郵件的內容。 明確地說，您可以自訂電子郵件的下列兩個元素：

* **橫幅標誌**：顯示在右下方。
* **背景色彩**：顯示在頂端。

    ![自訂驗證電子郵件的螢幕擷取畫面](./media/faq/company-branded-verification-email.png)

電子郵件簽章包含您第一次建立 Azure AD B2C 租用戶時提供的 Azure AD B2C 租用戶名稱。 您可以使用這些指示變更名稱：

1. 以全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 開啟 [ **Azure Active Directory** ] 分頁。
1. 按一下 [內容]**** 索引標籤。
1. 變更 [名稱]**** 欄位。
1. 按一下頁面頂端的 [儲存]  。

目前無法變更電子郵件中的 [從:] 欄位。

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>我如何將現有的使用者名稱、密碼和設定檔從資料庫移轉至 Azure AD B2C？

您可以使用 Microsoft Graph API 來撰寫您的遷移工具。 如需詳細資訊，請參閱[使用者移轉指南](user-migration.md)。

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C 中用於本機帳戶的密碼使用者流程為何？

Azure AD B2C 的本機帳戶密碼使用者流程是以 Azure AD 的原則為基礎。 Azure AD B2C 的註冊、註冊或登入和密碼重設使用者流程會使用「強式」密碼強度，而且不會讓任何密碼到期。 如需詳細資訊，請參閱[Azure Active Directory 中的密碼原則和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)。

如需帳戶鎖定和密碼相關資訊，請參閱[管理對 Azure Active Directory B2C 中的資源與資料的威脅](threat-management.md)。

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>我可以使用 Azure AD Connect，將儲存於內部部署 Active Directory 的取用者身分識別移轉至 Azure AD B2C 嗎？

否，Azure AD Connect 不是設計來搭配 Azure AD B2C 一起使用。 請考慮使用[MICROSOFT GRAPH API](manage-user-accounts-graph-api.md)來進行使用者遷移。 如需詳細資訊，請參閱[使用者移轉指南](user-migration.md)。

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>我的應用程式是否能在 iFrame 內開啟 Azure AD B2C 頁面？

否，基於安全性考量，無法在 iFrame 內開啟 Azure AD B2C 頁面。 我們的服務會與瀏覽器通訊以禁止 iFrame。 安全性社群整體和 OAUTH2 規格的建議是不要使用 iFrame 來提供身分識別體驗，因為會有點擊劫持風險。

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C 可以搭配 Microsoft Dynamics 之類的 CRM 系統一起使用嗎？

與 Microsoft Dynamics 365 入口網站的整合已可供使用。 請參閱[設定 Dynamics 365 入口網站，以使用 Azure AD B2C 進行驗證](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c)。

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C 可以搭配 SharePoint 內部部署的 2016 或更舊版本一起使用嗎？

Azure AD B2C 不適用於 SharePoint 外部夥伴共用的情節。請改以參閱 [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/)。

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>我應該使用 Azure AD B2C 或 B2B 來管理外部身分識別？

如需深入瞭解將適當的功能套用至外部身分識別案例，請參閱[比較 Azure AD 中的 B2B 共同作業和 B2C](../active-directory/b2b/compare-with-b2c.md) 。

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C 提供哪些報告和稽核功能？ 它們與 Azure AD Premium 的功能相同嗎？

否，Azure AD B2C 不支援與 Azure AD Premium 相同的一組報告。 不過有許多共同點：

* **登入報告**會提供每次登入的記錄，並縮減詳細資料。
* **稽核報告**包含管理活動以及應用程式活動。
* **使用報告**包含使用者數目、登入數目，以及 MFA 的磁碟區。

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>我可以將 Azure AD B2C 所提供的頁面 UI 當地語系化嗎？ 支援哪些語言？

是，請參閱[語言自訂](user-flow-language-customization.md)。 我們提供 36 種語言的翻譯，您可以覆寫任何字串以符合您的需求。

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>我可以在 Azure AD B2C 提供的註冊與登入頁面上使用自己的 URL 嗎？ 例如，我可以將 URL 從 contoso.b2clogin.com 變更為 login.contoso.com 嗎？

目前不支援。 但這項功能已在我們的規劃中。 在 Azure 入口網站的 [網域]**** 索引標籤中驗證您的網域，並無法達成此目標。 不過，在 b2clogin.com 中，我們提供了[中性的頂層網域](b2clogin.md)，因此外部外觀可以在不提及 Microsoft 的情況下執行。

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>如何刪除 Azure AD B2C 租用戶？

請遵循下列步驟來刪除您的 Azure AD B2C 租使用者。

您可以使用新的統一**應用程式註冊**體驗或我們的舊版**應用程式（舊版）** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 以*訂用帳戶管理員*身分登入[Azure 入口網站](https://portal.azure.com/)。 使用相同的公司或學校帳戶，或您用來註冊 Azure 的相同 Microsoft 帳戶。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 刪除 Azure AD B2C 租使用者中的所有**使用者流程（原則）** 。
1. 選取 [**應用程式註冊**]，然後選取 [**所有應用程式**] 索引標籤。
1. 刪除您註冊的所有應用程式。
1. 刪除**b2c-延伸模組-應用程式**。
1. 在 [管理] 下，選取 [使用者]。
1. 輪流選取每個使用者（排除您目前登入的*訂用帳戶管理員*使用者身分）。 選取頁面底部的 [**刪除**]，然後在出現提示時選取 **[是]** 。
1. 選取左側功能表上的 [ **Azure Active Directory** ]。
1. 在 [**管理**] 底下，選取 [**使用者設定**]。
1. 如果存在，請在 [ **LinkedIn 帳戶**連線] 下選取 [**否**]，然後選取 [**儲存**]。
1. 在 [**管理**] 底下，選取 [**屬性**]
1. 在[Azure 資源的存取管理]**** 底下，選取 [是]****，然後選取 [儲存]****。
1. 登出 Azure 入口網站然後再次登入以重新整理您的存取權。
1. 選取左側功能表上的 [ **Azure Active Directory** ]。
1. 在 [**總覽**] 頁面上，選取 [**刪除目錄**]。 遵循畫面上的指示來完成程式。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 以*訂用帳戶管理員*身分登入[Azure 入口網站](https://portal.azure.com/)。 使用相同的公司或學校帳戶，或您用來註冊 Azure 的相同 Microsoft 帳戶。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 刪除 Azure AD B2C 租使用者中的所有**使用者流程（原則）** 。
1. 刪除您在 Azure AD B2C 租使用者中註冊的所有**應用程式（舊版）** 。
1. 選取左側功能表上的 [ **Azure Active Directory** ]。
1. 在 [管理] 下，選取 [使用者]。
1. 輪流選取每個使用者（排除您目前登入的*訂用帳戶管理員*使用者身分）。 選取頁面底部的 [**刪除**]，然後在出現提示時選取 **[是]** 。
1. 在 [管理] 底下選取 [應用程式註冊]。
1. 選取 [**查看所有應用程式**]
1. 選取名為**b2c**的應用程式，選取 [**刪除**]，然後在出現提示時選取 **[是]** 。
1. 在 [**管理**] 底下，選取 [**使用者設定**]。
1. 如果存在，請在 [ **LinkedIn 帳戶**連線] 下選取 [**否**]，然後選取 [**儲存**]。
1. 在 [**管理**] 底下，選取 [**屬性**]
1. 在[Azure 資源的存取管理]**** 底下，選取 [是]****，然後選取 [儲存]****。
1. 登出 Azure 入口網站然後再次登入以重新整理您的存取權。
1. 選取左側功能表上的 [ **Azure Active Directory** ]。
1. 在 [**總覽**] 頁面上，選取 [**刪除目錄**]。 遵循畫面上的指示來完成程式。

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>我可以從 Enterprise Mobility Suite 中取得 Azure AD B2C 嗎？

否，Azure AD B2C 是隨用隨付的 Azure 服務，並不是 Enterprise Mobility Suite 的一部分。

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>如何報告有關 Azure AD B2C 的問題？

請參閱 [提出 Azure Active Directory B2C 的支援要求](support-options.md)。
