---
title: 新功能 版本資訊 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 的新功能，例如最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c58c99faa83d6f04ac37d03789202e111f1b06d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611155"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？

>藉由複製並貼上此 URL，取得何時要重新流覽此頁面以`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`進行更新![的通知：在](./media/whats-new/feed-icon-16x16.png)您的 RSS 摘要讀取器圖示摘要讀取器中。

Azure AD 會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能
- 方案變更

此頁面會每月更新，因此請定期瀏覽。 如果想要尋找超過 6 個月的項目，請至 [Azure Active Directory 的新增功能封存](whats-new-archive.md) (英文)。

---

## <a name="april-2020"></a>2020 年 4 月

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>結合的安全性資訊註冊體驗現已正式推出

**類型：** 新功能

**服務類別：** 驗證 (登入)

**產品功能：** 身分識別安全性 & 保護

多重要素驗證（MFA）和自助式密碼重設（SSPR）的合併註冊體驗現已正式推出。 這個新的註冊體驗可讓使用者在單一逐步程式中註冊 MFA 和 SSPR。 當您為組織部署新的體驗時，使用者可以更少的時間註冊，並減少麻煩。 請查看[這裡](https://bit.ly/3etiRyQ)的 blog 文章。

---

### <a name="continuous-access-evaluation"></a>連續存取評估

**類型：** 新功能

**服務類別：** 驗證 (登入)

**產品功能：** 身分識別安全性 & 保護

持續存取評估是一項新的安全性功能，可在 Azure AD 中發生事件（例如刪除使用者帳戶）時，針對取用 Azure AD 存取權杖的信賴憑證者，進行近乎即時的原則強制執行。 我們會先為小組和 Outlook 用戶端推出這項功能。 如需詳細資訊，請參閱我們的[blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)和[檔](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)。

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS 登入：第一線 Worker 可以使用其電話號碼和密碼登入 Azure AD 支援的應用程式

**類型：** 新功能

**服務類別：** 驗證 (登入)

**產品功能：** 使用者驗證

Office 正在推出一系列的行動優先商務應用程式，以滿足非傳統的組織和大型組織中不使用電子郵件作為主要通訊方法的員工。 這些應用程式的目標第一線員工、deskless 工作者、現場代理人或零售員工可能無法取得雇主的電子郵件地址、存取電腦或 IT。 此專案可讓這些員工輸入電話號碼並 roundtripping 程式碼，以登入商務應用程式。 如需詳細資訊，請參閱系統[管理員檔](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin)和[使用者檔](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer)。

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>邀請內部使用者使用 B2B 共同作業

**類型：** 新功能

**服務類別：** B2B

**產品功能：**

我們即將擴充 B2B 邀請功能，讓現有內部帳戶受到邀請，可以使用 B2B 共同作業認證。 除了一般參數（如受邀的電子郵件地址）之外，也會將使用者物件傳遞至邀請 API 來完成這項作業。 使用者的物件識別碼、UPN、群組成員資格、應用程式指派等等會保持不變，但未來他們會使用 B2B 來驗證其主要租使用者認證，而不是在邀請之前使用的內部認證。 如需詳細資訊，請參閱[檔](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users)集。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>條件式存取的僅限報告模式現已正式運作

**類型：** 新功能

**服務類別：** 條件式存取

**產品功能：** 身分識別安全性 & 保護

[Azure AD 條件式存取的報表專用模式](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only)可讓您評估原則的結果，而不需強制執行存取控制。 您可以在組織中測試僅限報告的原則，並瞭解其影響，再加以啟用，讓部署更安全且更容易。 過去幾個月，我們已經看過強式採用僅限報表模式，並已有超過26M 的使用者已經在僅限報表的原則範圍內。 在此公告中，預設會以僅限報表模式建立新的 Azure AD 條件式存取原則。 這表示您可以從建立原則的當時開始監視其影響。 對於使用 MS Graph Api 的使用者，您也可以透過程式設計的[方式來管理僅限報告的原則](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)。 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>條件式存取深入解析和報表活頁簿已正式推出

**類型：** 新功能

**服務類別：** 條件式存取

**產品功能：** 身分識別安全性 & 保護

條件式存取[見解和報告活頁簿](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting)提供系統管理員在其租使用者中 Azure AD 條件式存取的摘要觀點。 有了選取個別原則的功能，系統管理員就能進一步瞭解每個原則的作用，並即時監視任何變更。 活頁簿會串流儲存在 Azure 監視器中的資料，您可以在幾分鐘後依照[這些指示](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)進行設定。 為了讓儀表板更容易搜尋，我們已將它移到 [Azure AD 條件式存取] 功能表中的 [新的深入解析和報告] 索引標籤。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>條件式存取的原則詳細資料分頁處於公開預覽狀態

**類型：** 新功能

**服務類別：** 條件式存取

**產品功能：** 身分識別安全性 & 保護

新的 [[原則詳細資料](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access)] 分頁會顯示條件式存取原則評估期間所滿足的指派、條件和控制項。 您可以藉由在登入詳細資料的 [**條件式存取**] 或 [**僅限報表**] 索引標籤中選取資料列來存取此分頁。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD App 資源庫中提供的新同盟應用程式-2020 年4月

**類型：** 新功能

**服務類別：** 企業應用程式

**產品功能：** 協力廠商整合

我們已在2020年4月將下列31個新應用程式新增至應用程式庫，並提供同盟支援： 

[SincroPool Apps](https://www.sincropool.com/)、 [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/)、 [Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial)、 [LMS365](https://lms.365.systems/)、 [IWT 採購套件](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial)、 [Lunni](https://lunni.fi/)， [EasySSO For Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial)，[虛擬訓練](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft)學術， [Meraki 儀表板](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial)， [Office 365 移動器](https://app.mover.io/login)，[講師參與](https://speakerengage.com/login.php)，[誠實](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial)， [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial)， [DutyFlow](https://app.dutyflow.nl/)， [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial)，gr8[人員](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial)， [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial)， [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial)，[協調](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial)器， [Timetabling 解決方案](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial)， [SynchroNet 按一下](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial)，加強， [Fortes 變更雲端](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial)，Litmus， [GroupTalk](https://recorder.grouptalk.com/) [，Frontify，](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial) [MongoDB 雲端](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial) [，TickitLMS](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial)[學習](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial) [，COCO](https://www.made-in-office.com/en/) [，Nitro](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/)[生產力套件](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial)， [Trend 微 Web 安全性（TMWS）](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>適用于 oAuth2PermissionGrant 的 Microsoft Graph 差異查詢支援可供公開預覽

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 開發人員體驗

OAuth2PermissionGrant 的差異查詢可供公開預覽！ 您現在可以追蹤變更，而不需要持續輪詢 Microsoft Graph。 [深入了解。](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>組織連絡人的 Microsoft Graph 差異查詢支援已正式運作

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 開發人員體驗

組織連絡人的差異查詢已公開上市！ 您現在可以追蹤生產應用程式中的變更，而不需要持續輪詢 Microsoft Graph。 取代任何現有的程式碼，以差異查詢持續輪詢 orgContact 資料，以大幅改善效能。 [深入了解。](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>適用于應用程式的 Microsoft Graph 差異查詢支援已正式運作

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 開發人員體驗

應用程式的差異查詢已公開上市！ 您現在可以追蹤生產應用程式中的變更，而不需要持續輪詢 Microsoft Graph。 取代任何現有的程式碼，以差異查詢持續輪詢應用程式資料，以大幅改善效能。 [深入了解。](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>適用于公開預覽的系統管理單位 Microsoft Graph 差異查詢支援

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 系統管理單位的開發人員體驗差異查詢可供公開預覽！ 您現在可以追蹤變更，而不需要持續輪詢 Microsoft Graph。 [深入了解。](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>在新的 Microsoft Graph 搶鮮版 Api 中管理驗證電話號碼和更多

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 開發人員體驗

這些 Api 是用來管理使用者驗證方法的重要工具。 現在您可以透過程式設計方式，預先註冊並管理用於 MFA 和自助式密碼重設（SSPR）的驗證器。 這是 Azure MFA、SSPR 和 Microsoft Graph 空間中最常要求的功能之一。 我們在這一波推出的新 Api，讓您能夠：

- 讀取、新增、更新及移除使用者的驗證電話
- 重設使用者的密碼
- 開啟和關閉 SMS 登入

如需詳細資訊，請參閱[Azure AD 驗證方法 API 總覽](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)。

---

### <a name="administrative-units-public-preview"></a>管理單位公開預覽

**類型：** 新功能

**服務類別：** RBAC

**產品功能：** 存取控制

管理單位可讓您授與系統管理員許可權，限制為您所定義之組織的部門、區域或其他區段。 您可以使用系統管理單位，將許可權委派給地區系統管理員，或在細微層級設定原則。 例如，使用者帳戶系統管理員可以更新設定檔資訊、重設密碼，並只在其管理單位指派授權給使用者。

使用管理單位，中央系統管理員可以：

- 建立系統管理單位以進行分散的資源管理
- 僅針對管理單位中的 Azure AD 使用者，指派具有系統管理許可權的角色
- 視需要將使用者和群組填入管理單位

如需詳細資訊，請參閱[Azure Active Directory 中的系統管理單位管理（預覽）](https://aka.ms/AdminUnitsDocs)。

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>印表機管理員和印表機技術人員內建角色

**類型：** 新功能

**服務類別：** RBAC

**產品功能：** 存取控制

**印表機管理員**：具有此角色的使用者可以註冊印表機，以及管理 Microsoft 通用列印解決方案中所有印表機設定的所有層面，包括通用列印連接器設定。 他們可以同意所有委派的列印許可權要求。 印表機管理員也具有列印報表的存取權。 

**印表機技術人員**：具有此角色的使用者可以在 Microsoft 通用列印解決方案中註冊印表機和管理印表機狀態。 他們也可以讀取所有連接器資訊。 印表機技術人員無法執行的主要工作，是在印表機和共用印表機上設定使用者權限。 [深入了解。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>混合式身分識別管理內建角色

**類型：** 新功能

**服務類別：** RBAC

**產品功能：** 存取控制

此角色中的使用者可以啟用、設定和管理與在 Azure AD 中啟用混合式身分識別相關的服務和設定。 此角色可讓您將 Azure AD 設定為三種支援的驗證方法之一&#8212;密碼雜湊同步處理（PHS）、傳遞驗證（PTA）或同盟（AD FS 或協力廠商同盟提供者） &#8212;以及部署相關的內部部署基礎結構來啟用它們。 內部部署基礎結構包含布建和 PTA 代理程式。 此角色會授與啟用無縫單一登入（SSO）的功能，以在非 Windows 10 裝置或非 Windows Server 2016 電腦上啟用順暢的驗證。 此外，此角色會授與查看登入記錄的功能，並存取健康情況和分析以進行監視和疑難排解。 [深入了解。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>網路系統管理員內建角色

**類型：** 新功能

**服務類別：** RBAC

**產品功能：** 存取控制

具有此角色的使用者可以根據其使用者位置的網路遙測，審查 Microsoft 的網路周邊架構建議。 Office 365 的網路效能依賴謹慎的企業客戶網路周邊架構，這通常是使用者位置特定的架構。 此角色可讓您編輯探索到的使用者位置及設定這些位置的網路參數，以加速遙測測量和設計建議。 [深入了解。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD 系統管理員入口網站體驗中的大量活動和下載

**類型：** 新功能

**服務類別：** 使用者管理

**產品功能：** Directory

現在您可以在 Azure AD 系統管理員入口網站體驗中上傳 CSV 檔案，以在 Azure AD 中的使用者和群組上執行大量活動。 您可以建立使用者、刪除使用者，以及邀請來賓使用者。 而且您可以新增和移除群組中的成員。

您也可以從 Azure AD 系統管理員入口網站體驗下載 Azure AD 資源的清單。 您可以下載目錄中的使用者清單、目錄中的群組清單，以及特定群組的成員。

如需詳細資訊，請參閱下列內容：

- [建立使用者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add)或[邀請來賓使用者](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [刪除使用者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete)或[還原已刪除的使用者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [下載使用者清單](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download)或[下載群組清單](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [新增（匯入）成員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)或[移除成員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)或[下載群組成員清單](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)

---

### <a name="my-staff-delegated-user-management"></a>我的員工委派的使用者管理

**類型：** 新功能

**服務類別：** 使用者管理

**產品功能：**

我的員工可以讓第一線管理員（例如商店經理）確保他們的員工成員能夠存取他們的 Azure AD 帳戶。 組織可以將一般工作（例如重設密碼或變更電話號碼）委派給第一線管理員，而不是依賴中央技術服務人員。 在我的員工中，無法存取其帳戶的使用者只要按幾下，就可以重新取得存取權，而不需要技術服務人員或 IT 人員。 如需詳細資訊，請參閱使用[我的員工管理您的使用者（預覽）](https://aka.ms/MyStaffAdminDocs)和[使用我的員工委派使用者管理（預覽）](https://aka.ms/MyStaffUserDocs)。

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>存取審查中的已升級使用者體驗

**類型：** 已變更的功能

**服務類別：** 存取評論

**產品功能：** 身分識別治理

我們已在我的應用程式入口網站中更新 Azure AD 存取審查的審查者體驗。 在4月底，已登入 Azure AD 存取權審查審查者體驗的審核者將會看到橫幅，讓他們嘗試我的存取權中的更新體驗。 請注意，已更新的存取權審查體驗與目前的體驗提供相同的功能，但在新功能之上有改良的使用者介面，可讓您的使用者提高生產力。 [您可以在這裡深入瞭解更新的體驗](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。 此公開預覽將持續到2020年7月底為止。 7月底，尚未進入預覽體驗的審核者會自動導向我的存取權以執行存取權審查。 如果您想要讓您的審核者永久切換到我的存取權的預覽體驗，[請在這裡提出要求](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)。

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday 輸入使用者布建和回寫應用程式現在支援最新版本的 Workday Web 服務 API

**類型：** 已變更的功能

**服務類別：** 應用程式佈建

**產品功能：** 

根據客戶的意見反應，我們現在已更新企業應用程式庫中的 Workday 輸入使用者布建和回寫應用程式，以支援最新版本的 Workday Web 服務（WWS） API。 透過這項變更，客戶可以指定要在連接字串中使用的 WWS API 版本。 這讓客戶能夠抓取 Workday 版本中可用的更多 HR 屬性。 Workday 回寫應用程式現在會使用建議的 Change_Work_Contact_Info Workday web 服務來克服 Maintain_Contact_Info 的限制。

如果在連接字串中未指定任何版本，則根據預設，Workday 輸入布建應用程式會繼續使用 WWS v 21.1 切換至最新的 Workday Api 以進行輸入使用者布建，客戶必須更新[教學課程中](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)所述的連接字串，並更新 workday 屬性[參考指南](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)中所述的 workday 屬性所使用的 xpath。 

若要使用新的 API 來進行回寫，Workday 回寫布建應用程式中不需要進行任何變更。 在 Workday 端，請確定 Workday 整合系統使用者（ISU）帳戶有權叫用 Change_Work_Contact 商務程式，如教學課程區段[設定商務程式安全性原則許可權](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)中所述。 

我們已更新[教學課程指南](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)，以反映新的 API 版本支援。

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>具有預設存取角色的使用者現在已在布建範圍內

**類型：** 已變更的功能

**服務類別：** 應用程式佈建

**產品功能：** 身分識別生命週期管理

在過去，具有預設存取角色的使用者已超出布建的範圍。 我們聽說了客戶希望此角色的使用者在布建範圍內的意見反應。 從2020年4月16日起，所有新的布建設定都可讓使用者擁有預設存取角色。 我們會逐漸變更現有布建設定的行為，以支援使用此角色來布建使用者。 [深入了解。](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>已更新布建 UI

**類型：** 已變更的功能

**服務類別：** 應用程式佈建

**產品功能：** 身分識別生命週期管理

我們已重新整理布建體驗，以建立更專注的管理檢視。 當您流覽至已設定之企業應用程式的布建分頁時，您將能夠輕鬆地監視布建和管理動作的進度，例如啟動、停止和重新開機布建。 [深入了解。](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>動態群組規則驗證現已提供公開預覽

**類型：** 已變更的功能

**服務類別：** 群組管理

**產品功能：** 共同作業

Azure Active Directory （Azure AD）現在提供驗證動態群組規則的方法。 在 [**驗證規則**] 索引標籤上，您可以針對範例群組成員驗證您的動態規則，以確認規則如預期般運作。 當建立或更新動態群組規則時，系統管理員想要知道使用者或裝置是否將成為該群組的成員。 這有助於評估使用者或裝置是否符合規則條件，並在不預期成員資格時協助進行疑難排解。

如需詳細資訊，請參閱[驗證動態群組成員資格規則（預覽）](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation)。

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>身分識別安全分數-安全性預設值和 MFA 改進動作更新

**類型：** 已變更的功能

**服務類別：** N/A

**產品功能：** 身分識別安全性 & 保護

**支援 Azure AD 改進動作的安全性預設值：** Microsoft 安全分數將會更新改進動作，以支援[Azure AD 中的安全性預設值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)，讓您更輕鬆地使用預先設定的安全性設定來協助保護組織，以進行常見的攻擊。 這會影響下列改進動作：

- 確保所有使用者都可以完成多重要素驗證以進行安全存取
- 系統管理角色需要 MFA
- 啟用原則以封鎖舊版驗證
 
**MFA 改進動作更新：** 為了讓企業在套用適用于其業務的原則時，必須確保最安全性，Microsoft 安全分數已移除三個以多重要素驗證為中心的改進動作，並新增了兩個。

已移除改進動作：

- 註冊所有使用者進行多重要素驗證
- 要求所有使用者使用 MFA
- Azure AD 特殊許可權角色需要 MFA

新增的改進動作：

- 確保所有使用者都可以完成多重要素驗證以進行安全存取
- 系統管理角色需要 MFA

這些新的改進動作需要在您的目錄中註冊您的使用者或系統管理員，以進行多重要素驗證（MFA），並建立符合您組織需求的正確原則集。 主要的目標是要有彈性，同時確保所有使用者和系統管理員都可以使用多個因素或以風險為基礎的身分識別驗證提示進行驗證。 這種形式的格式可以採用多個原則來套用限定範圍的決策，或設定安全性預設值（從3月16日起），讓 Microsoft 決定何時要挑戰使用者進行 MFA。 深入瞭解[Microsoft 安全分數的新功能](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021年3月 B2B 更新中的非受控 Azure Active Directory 帳戶

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
自**2021 年3月31日起**，Microsoft 將不再支援兌換邀請，方法是建立適用于 B2B 共同作業案例的非受控 Azure Active Directory （Azure AD）帳戶和租使用者。 為此，我們鼓勵您選擇以[電子郵件單次密碼驗證](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)。

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>具有預設存取角色的使用者將會在布建範圍內

**類型：** 方案變更  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
在過去，具有預設存取角色的使用者已超出布建的範圍。 我們聽說了客戶希望此角色的使用者在布建範圍內的意見反應。 我們正努力部署變更，讓所有新的布建設定都能提供預設存取角色的使用者。 我們會逐漸變更現有布建設定的行為，以支援以這個角色布建使用者。 不需要任何客戶動作。 一旦這項變更已就緒，我們就會在[檔](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)中張貼更新。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD 的 B2B 共同作業將在由世紀（Azure 中國）租使用者營運的 Microsoft Azure 中提供

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure AD 的 B2B 共同作業功能將會在由世紀（Azure 中國）租使用者營運的 Microsoft Azure 中提供，讓 Azure 中國世紀內的使用者能夠與其他 Azure 中國世紀租使用者中的使用者順暢地共同作業。 [深入瞭解 AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)共同作業。

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B 共同作業邀請電子郵件重新設計

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure AD 的 B2B 共同作業邀請服務傳送的[電子郵件](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)，邀請使用者加入目錄，將重新設計，讓邀請資訊和使用者的後續步驟更清楚。

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery 原則變更會出現在 audit 記錄中

**類型：** 固定  
**服務類別：** 稽核  
**產品功能：** 監視和報告
 
我們已修正[HomeRealmDiscovery 原則](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)的變更未包含在 audit 記錄中的錯誤。 您現在將能夠查看原則的變更時間和方式，以及由誰變更。 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD App 資源庫中提供的新同盟應用程式-2020 年3月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2020年3月，我們已將這些51新應用程式新增至應用程式庫的同盟支援： 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)， [Zoho One 中國](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial)， [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/)， [Profit.co SAML 應用](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial) [Playvox](https://my.playvox.com/login)程式， [E-days](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial) [IPoint 服務提供者](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial) [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial) ， [Contexxt.ai 球體](https://contexxt-sphere.com/login)，[依 Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial)、 [Flare 數位告示](https://spark-dev.pixelnebula.com/login)、Logz.io [-雲端可檢視性，適用于工程師](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial)、 [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial)、 [BizzContact](https://bizzcontact.app/)、 [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial)、 [MarketSignShare](http://www.signshare.com/)、 [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial)、 [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial)、 [FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial)、筋 A [/S Byggeweb Mobile、](https://apps.apple.com/us/app/docia/id529058757)GoLinks [、](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial)Datadog、Zscaler B2B[使用者入口網站、](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial)增益[Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [、Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial) [、](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial)WatchTeams [、](https://www.devfinition.com/)aster (aster [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx) [、技術](https://demo.asterapp.io/login)[工作流程 IP](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial)[平臺、](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial)InVision [、](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial)pipedrive 新增了[、](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial)展示[研討會、](https://app.showcaseworkshop.com/)Greenlight[整合平臺、](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial)Greenlight[合規性存取管理、](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial)Grok[學習、](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial)Miradore [Online、](https://login.online.miradore.com/)Khoros[護理、](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial) [ ](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial)AskYourTeam [、](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial)TruNarrative [、](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial)Smartwaiver [、](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)Bizagi [Studio （適用于數位程式自動化）](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial)、insuiteX [、](https://www.insuite.jp/)sybo [、](https://www.systexsoftware.com.tw/) [ ](https://app.witivio.com/)Britive、WhosOffice [、Kollective](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial) [、](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial) [Witivio、Playvox](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial) [360](https://portal.kollective.app/login)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure Government 租使用者中可用 Azure AD B2B 共同作業

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure AD 的 B2B 共同作業功能現已可在部分 Azure Government 租使用者之間取得。  若要瞭解您的租使用者是否能夠使用這些功能，請依照[如何判斷我的 AZURE 美國政府租使用者中是否有 B2B 共同作業？中](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)的指示進行。

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>適用于 Azure 記錄的 Azure 監視器整合現已于 Azure Government

**類型：** 新功能  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
Azure 監視器與 Azure AD 記錄檔整合現已在 Azure Government 中提供。 您可以將 Azure AD 記錄（Audit 和登入記錄）路由傳送至儲存體帳戶、事件中樞和 Log Analytics。 請查看[詳細檔](https://aka.ms/aadlogsinamd)，以及[報告和監視](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting)Azure AD 案例的部署計畫。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure Government 中的身分識別保護重新整理

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護

我們很興奮地分享，我們現在已推出[Microsoft Azure Government 入口網站](https://portal.azure.us/)中重新整理的[Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs) 體驗。 如需詳細資訊，請參閱我們的[公告 blog 文章](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>嚴重損壞修復：下載並儲存您的布建設定

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
Azure AD 布建服務提供一組豐富的設定功能。 客戶必須能夠儲存其設定，以供日後參考，或回復為已知的正確版本。 我們新增了將您的布建設定下載為 JSON 檔案的功能，並在您需要時將其上傳。 [深入了解](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR （自助式密碼重設）現在在由世紀營運的 Microsoft Azure 中，系統管理員需要兩個閘道（Azure 中國世紀） 

**類型：** 已變更的功能  
**服務類別：** 自助式密碼重設  
**產品功能：** 身分識別安全性 & 保護
 
先前在由世紀（Azure 中國世紀）營運的 Microsoft Azure 中，使用自助式密碼重設（SSPR）來重設自己的密碼的系統管理員，只需要一個「閘道」（挑戰）來證明其身分識別。 在公用和其他國家雲端中，系統管理員通常必須在使用 SSPR 時，使用兩個閘道來證明其身分識別。 但因為我們不支援在 Azure 中國的世紀內進行 SMS 或電話通話，所以我們允許系統管理員透過單一閘道密碼重設。

我們將在 Azure 中國的世紀和公用雲端之間建立 SSPR 功能同位檢查。 從現在開始，系統管理員必須在使用 SSPR 時使用兩個閘道。 系統會支援 SMS、通話和驗證器代理程式更新和代碼。 [深入了解](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>密碼長度限制為256個字元

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
為確保 Azure AD 服務的可靠性，使用者密碼現在長度限制為256個字元。 若使用者的密碼長度超過此值，系統會要求他們在後續登入時變更其密碼，方法是聯絡其系統管理員或使用自助式密碼重設功能。

此變更已于2020年3月13日（10AM PST （18:00 UTC））啟用，錯誤為 AADSTS 50052，InvalidPasswordExceedsMaxLength。 如需詳細資訊，請參閱[重大變更通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>所有免費租使用者現在都可以透過 Azure 入口網站使用 Azure AD 登入記錄

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
從現在開始，具有免費租使用者的客戶可以[從 Azure 入口網站存取 Azure AD 登入記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)長達7天。 先前，登入記錄僅適用于具有 Azure Active Directory Premium 授權的客戶。 有了這項變更，所有租使用者都可以透過入口網站存取這些記錄。

> [!NOTE]
> 客戶仍需要 premium 授權（Azure Active Directory Premium P1 或 P2），才能透過 Microsoft Graph API 和 Azure 監視器來存取登入記錄。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>從 Azure 入口網站上的群組一般設定淘汰全目錄群組選項

**類型：** 已被取代  
**服務類別：** 群組管理  
**產品功能：** 共同作業

為了提供更有彈性的方式，讓客戶建立最符合其需求的全目錄群組，我們已從 Azure 入口網站中的 [**群組** > **一般**] 設定取代 [**全目錄群組**] 選項，並包含[動態群組檔](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)的連結。 我們已改進檔以包含更多指示，讓系統管理員可以建立包含或排除來賓使用者的所有使用者群組。

---

## <a name="february-2020"></a>2020 年 2 月

### <a name="upcoming-changes-to-custom-controls"></a>自訂控制項即將進行的變更

**類型：** 方案變更  
**服務類別：** MFA  
**產品功能：** 身分識別安全性 & 保護
 
我們打算將目前的自訂控制項預覽取代為可讓合作夥伴提供的驗證功能順暢地與 Azure Active Directory 系統管理員和使用者體驗搭配使用的方法。 目前，合作夥伴 MFA 解決方案面臨下列限制：只有在輸入密碼之後，才會使用它們。在其他重要案例中，它們不會做為 MFA 以進行步驟驗證;而且它們不會與終端使用者或系統管理認證管理功能整合。 新的執行可讓合作夥伴提供的驗證因素配合主要案例的內建因素，包括註冊、使用方式、MFA 宣告、逐步執行驗證、報告和記錄。 

自訂控制項在預覽中將繼續受到新設計的支援，直到正式推出為止。 此時，我們會讓客戶有時間遷移至新的設計。 基於目前方法的限制，我們將不會將新的提供者上線，直到新的設計可供使用為止。 我們正與客戶和提供者密切合作，並會隨著我們的深入討論，來溝通時程表。 [深入了解](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)。

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>身分識別安全分數-MFA 改進動作更新

**類型：** 方案變更  
**服務類別：** MFA  
**產品功能：** 身分識別安全性 & 保護
 
為了讓企業在套用適用于其業務的原則時，必須確保最安全性，Microsoft 安全分數會移除以多重要素驗證（MFA）為中心的三個改進動作，並新增兩個。

將移除下列改進動作：

- 註冊所有使用者進行 MFA
- 要求所有使用者使用 MFA
- Azure AD 特殊許可權角色需要 MFA

將新增下列改進動作：

- 確保所有使用者都可以完成 MFA 以進行安全存取
- 系統管理角色需要 MFA

這些新的改進動作將需要註冊您的使用者或系統管理員，才能在您的目錄中進行 MFA，並建立符合您組織需求的正確原則組。 主要的目標是要有彈性，同時確保所有使用者和系統管理員都可以使用多個因素或以風險為基礎的身分識別驗證提示進行驗證。 這可以採用設定安全性預設值的形式，讓 Microsoft 決定何時要挑戰使用者進行 MFA，或擁有多個原則來套用限定範圍的決策。 在這些改進動作更新過程中，基準保護原則將不再包含在評分計算中。 [深入瞭解 Microsoft 安全分數即將推出的內容](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide)。

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU 選擇

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services
 
我們聽到了意見反應，Azure AD Domain Services 客戶希望在為其實例選取效能層級時有更大的彈性。 從2020年2月1日開始，我們從動態模型切換（其中 Azure AD 會根據物件計數來決定效能和定價層）到自我選取模型。 客戶現在可以選擇符合其環境的效能層級。 這種變更也可讓我們啟用新的案例，例如資源樹系，以及每日備份等高階功能。 所有 Sku 的物件計數現在都不受限，但我們會繼續為每一層提供物件計數建議。

**不需要立即的客戶動作。** 針對現有的客戶，在2020年2月1日使用的動態層會決定新的預設層。 這項變更的結果並不會影響定價或效能。 接下來，Azure AD DS 客戶必須評估其目錄大小和工作負載特性變更時的效能需求。 在服務層之間切換會繼續成為不停機的作業，而且我們不會再根據其目錄的成長，自動將客戶移至新的層次。 此外，也不會增加價格，而新的定價將會與目前的計費模型一致。 如需詳細資訊，請參閱[AZURE AD DS sku 檔](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus)和[Azure AD Domain Services 定價頁面](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD App 資源庫中提供的新同盟應用程式-2020 年2月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2020年2月，我們已將下列31個具有同盟支援的新應用程式新增至應用程式庫： 

[IamIP 專利平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial)，[體驗雲端](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)， [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)， [Barracuda 電子郵件安全性服務](https://ess.barracudanetworks.com/sso/azure)， [ABa 報告](https://myaba.co.uk/client-access/signin/auth/msad)，適用[于危機線上入口網站](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial)， [BIC 雲端設計](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial)， [Beekeeper Azure AD 資料連線器](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial)、 [Korn Ferry 評](https://www.kornferry.com/solutions/kf-digital/kf-assess)量、 [Verkada 命令](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial)、 [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial)、 [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial)、 [EAB 導覽](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial)、[新的 new relic （有限發行）](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial)、 [Thulium](https://admin.thulium.com/login/instance)、[票證管理員](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial)、[小組的範本選擇](https://links.officeatwork.com/templatechooser-download-teams)器、 [Beesy](https://www.beesy.me/index.php/site/login)、[健全狀況支援系統](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial)、 [MURAL](https://app.mural.co/signup)、 [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial)、 [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)、 [Wakelet](https://wakelet.com/login)、 [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial)、[適用于教師和學校](https://www.thinglink.com/)[的](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial)、 [ThingLink、](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [NearpodApp](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [、WEDO、](https://invitepeople.com/login) [InvitePeople 服務台-文章 Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial)、 [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD 應用程式庫中的新布建連接器-2020 年2月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
您現在可以自動為這些新整合的應用程式建立、更新和刪除使用者帳戶：

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

如需如何使用自動使用者帳戶布建更進一步保護組織安全的詳細資訊，請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建。

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD 支援混合式環境中的 FIDO2 安全性金鑰

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
我們即將宣佈在混合式環境中 FIDO2 安全性金鑰的 Azure AD 支援公開預覽。 使用者現在可以使用 FIDO2 安全性金鑰來登入其混合式 Azure AD 加入的 Windows 10 裝置，並讓他們能夠順暢地登入其內部部署和雲端資源。 混合式環境的支援已經是我們無密碼客戶最常要求的功能，因為我們一開始會在已加入 Azure AD 的裝置上啟動 FIDO2 支援的公開預覽。 使用生物識別和公開/私密金鑰加密等先進技術進行無密碼驗證，可提供便利且便於使用，同時又安全。 在此公開預覽中，您現在可以使用新式驗證（例如 FIDO2 安全性金鑰）來存取傳統的 Active Directory 資源。 如需詳細資訊，請移至內部[部署資源的 SSO](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)。 

若要開始使用，請造訪針對[您的租使用者啟用 FIDO2 安全性金鑰](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)，以取得逐步指示。 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>新的我的帳戶體驗現已正式推出

**類型：** 已變更的功能  
**服務類別：** 我的設定檔/帳戶  
**產品功能：** 終端使用者體驗
 
「我的帳戶」是一種停止購買所有使用者帳戶管理需求的帳戶，現已正式推出！ 終端使用者可以透過 URL 或新我的應用程式體驗的標頭來存取這個新的網站。 深入瞭解新體驗在[我的帳戶入口網站](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)上提供的所有自助功能總覽。

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>我的帳戶網站 URL 正在更新為 myaccount.microsoft.com

**類型：** 已變更的功能  
**服務類別：** 我的設定檔/帳戶  
**產品功能：** 終端使用者體驗
 
新的我的帳戶終端使用者體驗將會`https://myaccount.microsoft.com`在下個月更新其 URL。 在[我的帳戶入口網站](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)說明中，尋找體驗的詳細資訊，以及它提供給終端使用者的所有帳戶自助功能。

---
 
## <a name="january-2020"></a>2020 年 1 月
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>新的我的應用程式入口網站現已正式推出

**類型：** 方案變更  
**服務類別：** 我的應用程式  
**產品功能：** 終端使用者體驗
 
將您的組織升級至現已正式推出的新我的應用程式入口網站！ 在[我的應用程式入口網站上的建立集合中](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)，尋找新入口網站和集合的詳細資訊。

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD 中的工作區已重新命名為集合

**類型：** 已變更的功能  
**服務類別：** 我的應用程式   
**產品功能：** 終端使用者體驗
 
工作區是系統管理員可以設定來組織其使用者應用程式的篩選器，現在稱為「集合」。 在[我的應用程式入口網站上的 [建立集合](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)] 中，尋找如何設定它們的詳細資訊。

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>使用自訂原則 Azure AD B2C 手機註冊和登入（公開預覽）

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C
 
有了電話號碼註冊和登入，開發人員和企業可以讓他們的客戶使用透過 SMS 傳送到使用者電話號碼的一次性密碼來註冊和登入。 這項功能也可讓客戶在無法存取其電話時變更其電話號碼。 藉由自訂原則的威力，電話註冊和登入可讓開發人員和企業透過頁面自訂來傳達其品牌。 瞭解如何[使用 Azure AD B2C 中的自訂原則來設定電話註冊和登入](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)。
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD 應用程式資源庫中的新布建連接器-2020 年1月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
您現在可以自動為這些新整合的應用程式建立、更新和刪除使用者帳戶：

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

如需如何使用自動使用者帳戶布建更進一步保護組織安全的詳細資訊，請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD App 資源庫中可用的新同盟應用程式-2020 年1月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2020年1月，我們已將這些33新的應用程式與同盟支援新增至應用程式庫： 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial)、 [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial)、 [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial)、 [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial)、 [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik)、 [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)、 [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)、 [LeaveBot](https://leavebot.io/#home)、 [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial)、 [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial)、 [SmartWork、Dotcom](https://www.intumit.com/english/SmartWork.html) [-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial)、 [SSOGEN-Azure AD 適用于 Oracle 電子商務套件的 SSO 閘道-EBS、PeopleSoft 和 JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial)、 [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial)、 [Yuhu 內容管理平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial)、 [LumApps](https://sites.lumapps.com/login)、 [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial)、 [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial)、 [SmartDB for Microsoft 小組](http://teams.smartdb.jp/login/)、 [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial)、 [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial)、 [Maxient 籌辦經理軟體](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial)、 [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial)、PortalTalk [365](https://www.portaltalk.com/)、 [CoreView](https://portal.coreview.com/)、[隱藏 Cloud Office365 Connector](https://laxmi.squelch.io/login)、 [PingFlow Authentication](https://app-staging.pingview.io/)、PrinterLogic [SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial)、Taskize [Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial)、 [Sandwai](https://app.sandwai.com/)、EZRentOut [、AssetSonar、Akari](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [Virtual Assistant](https://akari.io/akari-virtual-assistant/) [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="two-new-identity-protection-detections"></a>兩個新的身分識別保護偵測

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護
 
我們已將兩個新的登入連結偵測類型新增至 Identity Protection：可疑的收件匣操作規則和不可能的移動。 Microsoft Cloud App Security （MCAS）會探索這些離線偵測，並會影響身分識別保護中的使用者和登入風險。 如需這些偵測的詳細資訊，請參閱我們的登[入風險類型](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)。
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>中斷性變更：不會透過登入重新導向來執行 URI 片段

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
從2020年2月8日開始，當要求傳送至 login.microsoftonline.com 以登入使用者時，服務會將空的片段附加至要求。  這可確保瀏覽器抹除要求中任何現有的片段，藉此防止重新導向攻擊的類別。 應用程式不應該依賴此行為。 如需詳細資訊，請參閱 Microsoft 身分識別平臺檔中的[重大變更](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020)。

---

## <a name="december-2019"></a>2019 年 12 月

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>將 SAP SuccessFactors 布建整合到 Azure AD 和內部部署 AD （公開預覽）

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理

您現在可以將 SAP SuccessFactors 整合為 Azure AD 中的授權身分識別來源。 這項整合可協助您自動化端對端身分識別生命週期，包括使用 HR 事件（例如新進員工或終止）來控制 Azure AD 帳戶的布建。

如需有關如何設定 SAP SuccessFactors 輸入布建至 Azure AD 的詳細資訊，請參閱設定[Sap SuccessFactors 自動](https://aka.ms/SAPSuccessFactorsInboundTutorial)布建教學課程。

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>支援 Azure AD B2C 中的自訂電子郵件（公開預覽）

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C

當您的使用者註冊使用您的應用程式時，您現在可以使用 Azure AD B2C 來建立自訂的電子郵件。 藉由使用 DisplayControls （目前為預覽狀態）和協力廠商電子郵件提供者（例如[SendGrid](https://sendgrid.com/)、 [SparkPost](https://sparkpost.com/)或自訂 REST API），您可以使用自己的電子郵件範本 **、位址**和主旨文字，以及支援當地語系化和自訂的單次密碼（OTP）設定。

如需詳細資訊，請參閱[Azure Active Directory B2C 中的自訂電子郵件驗證](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)。

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>以安全性預設值取代基準原則

**類型：** 已變更的功能  
**服務類別：** 其他  
**產品功能：** 身分識別安全性與保護

做為驗證的安全預設模型的一部分，我們會從所有租使用者中移除現有的基準保護原則。 這項移除作業的目標是在2月底結束時完成。 這些基準保護原則的取代是安全性預設值。 如果您已使用基準保護原則，您必須規劃移至新的安全性預設原則或條件式存取。 如果您尚未使用這些原則，則不需要採取任何動作。

如需新安全性預設值的詳細資訊，請參閱[什麼是安全性預設值？](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 如需條件式存取原則的詳細資訊，請參閱[常見的條件式存取原則](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。

---

## <a name="november-2019"></a>2019 年 11 月

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>支援 SameSite 屬性和 Chrome 80

**類型：** 方案變更  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

作為 cookie 的安全預設模型的一部分，Chrome 80 瀏覽器會變更其處理 cookie 的方式，而不需要`SameSite`屬性。 任何未指定屬性的`SameSite` cookie 都會被視為設定為`SameSite=Lax`，這會導致 Chrome 封鎖您的應用程式可能相依的某些跨網域 cookie 共用案例。 若要維護較舊的 Chrome 行為，您可以`SameSite=None`使用屬性並新增額外`Secure`的屬性，因此跨網站 cookie 只能透過 HTTPS 連線來存取。 Chrome 已排程于2020年2月4日完成這項變更。

我們建議所有開發人員使用此指引來測試其應用程式：

- 將 [**使用安全 Cookie** ] 設定的預設值設定為 **[是]**。

- 將 [ **SameSite** ] 屬性的預設值設定為 [**無**]。

- 新增另`SameSite`一個 [**安全**] 屬性。

如需詳細資訊，請參閱[ASP.NET 和 ASP.NET Core 即將推出的 SameSite Cookie 變更](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)，以及[Chrome 79 版和更新版本中客戶網站和 Microsoft 產品和服務的潛在中斷情形](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)。

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Microsoft Identity Manager （MIM） 2016 Service Pack 2 （SP2）的新修補程式

**類型：** 固定  
**服務類別：** Microsoft Identity Manager  
**產品功能：** 身分識別生命週期管理

可供 Microsoft Identity Manager （MIM） 2016 Service Pack 2 （SP2）使用的「修復匯總套件」（build 4.6.34.0）。 此匯總套件會解決問題，並新增「此更新中新增的問題已修正」一節中所述的改良功能。

如需詳細資訊及下載此修補套件，請參閱[Microsoft Identity Manager 2016 Service Pack 2 （組建4.6.34.0）可用的更新彙總套件](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)。

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>新的 AD FS 應用程式活動報表，可協助您將應用程式遷移至 Azure AD （公開預覽）

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO

使用 Azure 入口網站中的新 Active Directory 同盟服務（AD FS）應用程式活動報告，識別哪些應用程式能夠遷移至 Azure AD。 此報表會評估所有 AD FS 應用程式，以與 Azure AD 相容、檢查是否有任何問題，並提供有關準備個別應用程式以進行遷移的指引。

如需詳細資訊，請參閱[使用 AD FS 應用程式活動報告將應用程式遷移至 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)。

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>新的工作流程，讓使用者要求系統管理員同意（公開預覽）

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 存取控制

新的系統管理員同意工作流程可讓系統管理員將存取權授與需要系統管理員核准的應用程式。 如果使用者嘗試存取應用程式，但無法提供同意，他們現在可以傳送要求以進行系統管理員核准。 要求是透過電子郵件傳送，放在可從 Azure 入口網站存取的佇列中，到已指定為審核者的所有系統管理員。 當審查者對暫止的要求採取動作之後，要求的使用者會收到動作的通知。

如需詳細資訊，請參閱[設定管理員同意工作流程（預覽）](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)。

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>新的 Azure AD App 註冊權杖設定體驗，以管理選擇性宣告（公開預覽）

**類型：** 新功能  
**服務類別：** 其他  
**產品功能：** 開發人員體驗

Azure 入口網站上新的**Azure AD App 註冊權杖**設定] 分頁現在會向應用程式開發人員顯示其應用程式選擇性宣告的動態清單。 這種新體驗有助於簡化 Azure AD 的應用程式遷移，並將選擇性的宣告錯誤錯誤降至最低。

如需詳細資訊，請參閱為[您的 Azure AD 應用程式提供選擇性宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)。

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD 權利管理（公開預覽）中的新兩階段核准工作流程

**類型：** 新功能  
**服務類別：** 其他  
**產品功能：** 權利管理

我們引進了新的兩階段核准工作流程，可讓您要求兩個核准者核准使用者對存取套件的要求。 例如，您可以設定它，要求使用者的管理員必須先核准，然後您也可以要求資源擁有者核准。 如果其中一個核准者未核准，則不會授與存取權。

如需詳細資訊，請參閱[Azure AD 權利管理中的變更存取套件的要求和核准設定](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)。

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>更新我的應用程式頁面以及新的工作區（公開預覽）

**類型：** 新功能  
**服務類別：** 我的應用程式  
**產品功能：** 協力廠商整合

您現在可以自訂群組織使用者的觀點，並存取重新整理的我的應用程式體驗。 這項新體驗也包含新的工作區功能，可讓您的使用者更輕鬆地尋找及組織應用程式。

如需新我的應用程式體驗和建立工作區的詳細資訊，請參閱在[我的應用程式入口網站上建立工作區](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)。

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Azure AD B2B 共同作業的 Google 社交識別碼支援（正式運作）

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** 使用者驗證

在 Azure AD 中使用 Google 社交識別碼（Gmail 帳戶）的新支援有助於讓您的使用者和合作夥伴更輕鬆地進行共同作業。 您的合作夥伴不再需要建立及管理新的 Microsoft 特定帳戶。 Microsoft 小組現在完全支援所有用戶端上的 Google 使用者，以及跨一般和租使用者相關的驗證端點。

如需詳細資訊，請參閱[將 Google 新增為 B2B 來賓使用者的身分識別提供者](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>適用于條件式存取和單一登入的 Microsoft Edge 行動支援（正式運作）

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護

IOS 和 Android 上的 Microsoft Edge Azure AD 現在支援 Azure AD 單一登入和條件式存取：

- **Microsoft Edge 單一登入（SSO）：** 所有 Azure AD 連線應用程式的原生用戶端（例如 Microsoft Outlook 和 Microsoft Edge）現在都可以使用單一登入。

- **Microsoft Edge 條件式存取：** 透過以應用程式為基礎的條件式存取原則，您的使用者必須使用受 Microsoft Intune 保護的瀏覽器，例如 Microsoft Edge。

如需有關使用 Microsoft Edge 的條件式存取和 SSO 的詳細資訊，請參閱[適用于條件式存取的 Microsoft Edge 行動支援和單一登入現已正式](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179)運作的 blog 文章。 如需有關如何使用以[應用程式為基礎的條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)或[裝置型條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)來設定用戶端應用程式的詳細資訊，請參閱[使用 Microsoft Intune 受原則保護的瀏覽器來管理 web 存取](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)。

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD 權利管理（公開上市）

**類型：** 新功能  
**服務類別：** 其他  
**產品功能：** 權利管理

Azure AD 權利管理是新的身分識別治理功能，可協助組織大規模管理身分識別和存取生命週期。 這項新功能可協助您跨群組、應用程式和 SharePoint Online 網站，自動化存取要求工作流程、存取指派、評論和到期。

有了 Azure AD 的權利管理，您可以更有效率地管理員工的存取權，以及您組織外部需要存取這些資源的使用者。

如需詳細資訊，請參閱[什麼是 Azure AD 權利管理？](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>針對這些新支援的 SaaS 應用程式自動布建使用者帳戶

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合  

您現在可以自動為這些新整合的應用程式建立、更新和刪除使用者帳戶：

[SAP Cloud Platform Identity Authentication 服務](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)、 [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial)、 [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial)、 [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial)、 [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial)、 [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial)、 [officespace software Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial)、 [Priority 對照表](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

如需如何使用自動使用者帳戶布建更進一步保護組織安全的詳細資訊，請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD App 資源庫中提供的新同盟應用程式-2019 年11月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在2019年11月，我們已將這些21個具有同盟支援的新應用程式新增至應用程式庫：

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial)， [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial)，[成員（BAM）的藍色存取](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial)、 [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial)、 [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial)、 [ResLife 入口網站](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=)、 [NegometrixPortal 單一登入（SSO）](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial)、 [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279)、 [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial)、 [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial)、 [BlueMail](https://loginself1.bluemail.me/)、 [Beedle](https://teams-web.beedle.co/#/)、 [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial)、 [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial)、 [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial)、 [Qmarkets 觀念 & 創新管理](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial)、 [Netskope 使用者驗證](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial)、 [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial)、 [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial)、 [Jisc Student 投票者註冊](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial)、 [e4enable](https://portal.e4enable.com/)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>新的和改良的 Azure AD 應用程式庫

**類型：** 已變更的功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO

我們已更新 Azure AD 應用程式庫，讓您更輕鬆地在您的 Azure Active Directory 租使用者上尋找支援布建、OpenID Connect 和 SAML 的預先整合應用程式。

如需詳細資訊，請參閱[將應用程式新增至您的 Azure Active Directory 租使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)。

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>將應用程式角色定義長度限制從120增加至240個字元

**類型：** 已變更的功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO

我們聽說客戶，在某些應用程式和服務中，應用程式角色定義值的長度限制太短（120個字元）。 為了回應，我們已將角色值定義的最大長度增加為240個字元。

如需使用應用程式專屬角色定義的詳細資訊，請參閱在[您的應用程式中加入應用程式角色，並在權杖中接收它們](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。

---
