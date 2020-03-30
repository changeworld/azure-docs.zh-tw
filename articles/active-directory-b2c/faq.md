---
title: Azure 活動目錄 B2C 的常見問題 （FAQ）
description: 回答有關 Azure 活動目錄 B2C 的常見問題的解答。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40285c811cd6f407c20c40bf3a90ec5b779a9c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264397"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C：常見問題集 (FAQ)

此頁回答了有關 Azure 活動目錄 B2C（Azure AD B2C）的常見問題。 請隨時回來查看最新消息。

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>為什麼無法存取 Azure 入口網站中的 Azure AD B2C 擴充功能？

Azure AD 擴充功能無法運作有兩個常見原因。 Azure AD B2C 要求您在目錄中的使用者角色是全域管理員。 如果您認為自己具有存取權，請連絡您的管理員。 如果您有全域管理員權限，請確定您是在 Azure AD B2C 目錄中，而非 Azure Active Directory 目錄。 可至此查看[建立 Azure AD B2C 租用戶](tutorial-create-tenant.md)的指示。

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>我可以在以員工為主的現有 Azure AD 租用戶中使用 Azure AD B2C 功能嗎？

Azure AD 和 Azure AD B2C 為個別的產品供應項目，無法共存於同一個租用戶。 一個 Azure AD 租用戶代表一個組織。 一個 Azure AD B2C 租用戶代表一組要用於信賴憑證者應用程式的身分識別。 通過在**Azure AD B2C 下**添加新的**OpenID Connect 提供程式**>標識提供程式或自訂策略，Azure AD B2C 可以聯合到 Azure AD，允許對組織中的員工進行身份驗證。

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>我可以使用 Azure AD B2C 來提供 Office 365 的社交登入 (Facebook 和 Google+) 嗎？

Azure AD B2C 無法用來驗證 Microsoft Office 365 的使用者。 Azure AD 是 Microsoft 用於管理員工訪問 SaaS 應用的解決方案，它具有專為此目的設計的功能，如許可和條件訪問。 Azure AD B2C 提供身分識別和存取管理平台來建置 web 和行動應用程式。 當 Azure AD B2C 設定為與 Azure AD 租用戶結成同盟時，Azure AD 租用戶會管理員工如何存取依賴 Azure AD B2C 的應用程式。

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C 中的本機帳戶是什麼？ 與 Azure AD 中的工作或學校帳戶有何不同？

在 Azure AD 租用戶中，屬於租用戶的使用者是以 `<xyz>@<tenant domain>` 格式的電子郵件地址登入。 `<tenant domain>` 是租用戶中已驗證的其中一個網域，或初始的 `<...>.onmicrosoft.com` 網域。 這種類型的帳戶就是工作或學校帳戶。

在 Azure AD B2C 租用戶中，大部分應用程式都希望使用者以任意的電子郵件地址登入 (例如 joe@comcast.net、bob@gmail.com、sarah@contoso.com 或 jim@live.com)。 這種類型的帳戶就是本機帳戶。 我們也支援使用任意的使用者名稱作為本機帳戶 (例如，joe、bob、sarah 或 jim)。 在 Azure 入口網站中設定 Azure AD B2C 的識別提供者時，您可以從這兩個本機帳戶類型中選擇一個。 在 Azure AD B2C 租戶中，選擇**標識提供程式**，選擇**本地帳戶**，然後選擇**使用者名**。

應用程式使用者帳戶可以通過註冊使用者流、註冊或登錄使用者流、Microsoft 圖形 API 或在 Azure 門戶中創建。

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>你們現在支援哪些社交身分識別提供者？ 你們打算在未來支援哪些？

我們目前支援多個社交身份供應商，包括亞馬遜、Facebook、GitHub（預覽）、谷歌、LinkedIn、微軟帳戶（MSA）、QQ（預覽）、推特、微信（預覽）和微博（預覽）。 我們根據客戶需求評估對其他熱門社交身份供應商的添加支援。

Azure AD B2C 也支援[自訂策略](custom-policy-overview.md)。 自訂策略允許您為支援[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)或 SAML 的任何標識提供程式創建自己的策略。 查看我們的[自訂原則入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)，開始使用自訂原則。

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

* **橫幅徽標**：顯示在右下角。
* **背景色彩**：顯示在頂端。

    ![自訂驗證電子郵件的螢幕截圖](./media/faq/company-branded-verification-email.png)

電子郵件簽章包含您第一次建立 Azure AD B2C 租用戶時提供的 Azure AD B2C 租用戶名稱。 您可以使用這些指示變更名稱：

1. 以全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 打開**Azure 活動目錄**邊欄選項卡。
1. 按一下 [內容]**** 索引標籤。
1. 變更 [名稱]**** 欄位。
1. 按一下頁面頂端的 [儲存]****。

目前無法變更電子郵件中的 [從:] 欄位。

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>我如何將現有的使用者名稱、密碼和設定檔從資料庫移轉至 Azure AD B2C？

您可以使用 Microsoft 圖形 API 編寫遷移工具。 如需詳細資訊，請參閱[使用者移轉指南](user-migration.md)。

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C 中用於本機帳戶的密碼使用者流程為何？

Azure AD B2C 的本機帳戶密碼使用者流程是以 Azure AD 的原則為基礎。 Azure AD B2C 的註冊、註冊或登入和密碼重設使用者流程會使用「強式」密碼強度，而且不會讓任何密碼到期。 有關詳細資訊，請參閱[Azure 活動目錄中的密碼原則和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)。

如需帳戶鎖定和密碼相關資訊，請參閱[管理對 Azure Active Directory B2C 中的資源與資料的威脅](threat-management.md)。

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>我可以使用 Azure AD Connect，將儲存於內部部署 Active Directory 的取用者身分識別移轉至 Azure AD B2C 嗎？

否，Azure AD Connect 不是設計來搭配 Azure AD B2C 一起使用。 請考慮使用[Microsoft 圖形 API](manage-user-accounts-graph-api.md)進行使用者遷移。 如需詳細資訊，請參閱[使用者移轉指南](user-migration.md)。

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>我的應用程式是否能在 iFrame 內開啟 Azure AD B2C 頁面？

否，基於安全性考量，無法在 iFrame 內開啟 Azure AD B2C 頁面。 我們的服務會與瀏覽器通訊以禁止 iFrame。 安全性社群整體和 OAUTH2 規格的建議是不要使用 iFrame 來提供身分識別體驗，因為會有點擊劫持風險。

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C 可以搭配 Microsoft Dynamics 之類的 CRM 系統一起使用嗎？

與 Microsoft Dynamics 365 入口網站的整合已可供使用。 請參閱[設定 Dynamics 365 入口網站，以使用 Azure AD B2C 進行驗證](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c)。

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C 可以搭配 SharePoint 內部部署的 2016 或更舊版本一起使用嗎？

Azure AD B2C 不適用於 SharePoint 外部夥伴共用的情節。請改以參閱 [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/)。

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>我應該使用 Azure AD B2C 或 B2B 來管理外部身分識別？

閱讀[Azure AD 中的比較 B2B 協作和 B2C，](../active-directory/b2b/compare-with-b2c.md)瞭解有關將適當的功能應用於外部標識方案的更多內容。

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C 提供哪些報告和稽核功能？ 它們與 Azure AD Premium 的功能相同嗎？

否，Azure AD B2C 不支援與 Azure AD Premium 相同的一組報告。 不過有許多共同點：

* **登入報告**會提供每次登入的記錄，並縮減詳細資料。
* **稽核報告**包含管理活動以及應用程式活動。
* **使用報告**包含使用者數目、登入數目，以及 MFA 的磁碟區。

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>我可以將 Azure AD B2C 所提供的頁面 UI 當地語系化嗎？ 支援哪些語言？

是的，請參閱[語言自訂](user-flow-language-customization.md)。 我們提供 36 種語言的翻譯，您可以覆寫任何字串以符合您的需求。

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>我可以在 Azure AD B2C 提供的註冊與登入頁面上使用自己的 URL 嗎？ 例如，是否可以將 URL 從 contoso.b2clogin.com 更改為 login.contoso.com？

目前不支援。 但這項功能已在我們的規劃中。 在 Azure 入口網站的 [網域]**** 索引標籤中驗證您的網域，並無法達成此目標。 然而，b2clogin.com，我們提供一個[中立的頂層網域](b2clogin.md)，因此外部外觀可以實現，而無需提及微軟。

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>如何刪除 Azure AD B2C 租用戶？

按照以下步驟刪除 Azure AD B2C 租戶。

您可以使用當前的**應用程式**體驗或我們新的統一**應用程式註冊（預覽）** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 以*訂閱管理員*身份登錄到[Azure 門戶](https://portal.azure.com/)。 使用與註冊 Azure 相同的工作或學校帳戶或同一 Microsoft 帳戶。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 刪除 Azure AD B2C 租戶中的所有**使用者流（策略**）。
1. 刪除在 Azure AD B2C 租戶中註冊的所有**應用程式**。
1. 在左側功能表上選擇**Azure 活動目錄**。
1. 在 [管理]**** 底下選取 [使用者]****。
1. 依次選擇每個使用者（不包括您當前登錄的*訂閱管理員*使用者）。 選擇頁面底部的 **"刪除**"，並在提示時選擇 **"是**"。
1. 在 **"管理**"下，選擇**應用註冊**（或**應用註冊（舊版））。**
1. 選擇 **"查看所有應用程式**"
1. 選擇名為**b2c 擴展應用程式**的應用程式，選擇 **"刪除**"，然後在提示時選擇 **"是**"。
1. 在 **"管理**"下，選擇 **"使用者設置**"。
1. 如果存在，請在**LinkedIn帳戶連接**下，選擇 **"否**"，然後選擇"**保存**"。
1. 在 **"管理**"下，選擇 **"屬性"**
1. 在[Azure 資源的存取管理]**** 底下，選取 [是]****，然後選取 [儲存]****。
1. 登出 Azure 門戶，然後重新登錄以刷新存取權限。
1. 在左側功能表上選擇**Azure 活動目錄**。
1. 在 **"概述"** 頁上，選擇 **"刪除目錄**"。 按照螢幕上的說明完成此過程。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 以*訂閱管理員*身份登錄到[Azure 門戶](https://portal.azure.com/)。 使用與註冊 Azure 相同的工作或學校帳戶或同一 Microsoft 帳戶。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 刪除 Azure AD B2C 租戶中的所有**使用者流（策略**）。
1. 選擇**應用註冊（預覽），** 然後選擇"**所有應用程式**"選項卡。
1. 刪除您註冊的所有應用程式。
1. 刪除**b2c 擴展應用程式**。
1. 在 [管理]**** 底下選取 [使用者]****。
1. 依次選擇每個使用者（不包括您當前登錄的*訂閱管理員*使用者）。 選擇頁面底部的 **"刪除**"，並在提示時選擇 **"是**"。
1. 在左側功能表上選擇**Azure 活動目錄**。
1. 在 **"管理**"下，選擇 **"使用者設置**"。
1. 如果存在，請在**LinkedIn帳戶連接**下，選擇 **"否**"，然後選擇"**保存**"。
1. 在 **"管理**"下，選擇 **"屬性"**
1. 在[Azure 資源的存取管理]**** 底下，選取 [是]****，然後選取 [儲存]****。
1. 登出 Azure 門戶，然後重新登錄以刷新存取權限。
1. 在左側功能表上選擇**Azure 活動目錄**。
1. 在 **"概述"** 頁上，選擇 **"刪除目錄**"。 按照螢幕上的說明完成此過程。

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>我可以從 Enterprise Mobility Suite 中取得 Azure AD B2C 嗎？

否，Azure AD B2C 是隨用隨付的 Azure 服務，並不是 Enterprise Mobility Suite 的一部分。

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>如何報告有關 Azure AD B2C 的問題？

請參閱 [提出 Azure Active Directory B2C 的支援要求](support-options.md)。
