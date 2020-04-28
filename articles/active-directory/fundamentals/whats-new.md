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
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802504"
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

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021年3月 B2B 更新中的非受控 Azure Active Directory 帳戶

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
自**2021 年3月31日起**，Microsoft 將不再支援兌換邀請，方法是建立適用于 B2B 共同作業案例的非受控 Azure Active Directory （Azure AD）帳戶和租使用者。 為此，我們鼓勵您選擇以[電子郵件單次密碼驗證](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)。

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
 
工作區，系統管理員可以設定來組織其使用者應用程式的篩選器，現在稱為集合。 在[我的應用程式入口網站上的 [建立集合](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)] 中，尋找如何設定它們的詳細資訊。

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

## <a name="october-2019"></a>2019 年 10 月

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Azure AD Identity Protection 風險偵測的 identityRiskEvent API 已淘汰  

**類型：** 方案變更  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護

為回應開發人員的意見反應，Azure AD Premium P2 訂閱者現在可以使用適用于 Microsoft Graph 的新 riskDetection API，對 Azure AD Identity Protection 的風險偵測資料執行複雜的查詢。 現有的[identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API 搶鮮版（Beta）版本將會停止傳回**2020 年1月10日**附近的資料。 如果您的組織使用 identityRiskEvent API，您應該轉換至新的 riskDetection API。

如需有關新 riskDetection API 的詳細資訊，請參閱[風險偵測 API 參考檔](https://aka.ms/RiskDetectionsAPI)。

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>SameSite 屬性和 Chrome 80 的應用程式 Proxy 支援

**類型：** 方案變更  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

在 Chrome 80 瀏覽器版本之前的幾周，我們打算更新應用程式 Proxy cookie 如何處理**SameSite**屬性。 當 Chrome 80 發行時，任何未指定**SameSite**屬性的 cookie 都會被視為設定為`SameSite=Lax`。

為了避免因這項變更而造成負面影響，我們將藉由下列方式來更新應用程式 Proxy 存取和會話 cookie：

- 將 [**使用安全 Cookie** ] 設定的預設值設定為 **[是]**。

- 將**SameSite**屬性的預設值設定為 [**無**]。

    >[!NOTE]
    > 應用程式 Proxy 存取 cookie 一律以獨佔方式透過安全通道傳輸。 這些變更只適用于會話 cookie。

如需應用程式 Proxy cookie 設定的詳細資訊，請參閱[Azure Active Directory 中用來存取內部部署應用程式的 cookie 設定](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)。

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>應用程式註冊入口網站（apps.dev.microsoft.com）中的應用程式註冊（舊版）和聚合式應用程式管理將無法再使用

**類型：** 方案變更  
**服務類別：** N/A  
**產品功能：** 開發人員體驗

在不久的未來，具有 Azure AD 帳戶的使用者將無法再使用應用程式註冊入口網站（apps.dev.microsoft.com）來註冊和管理交集應用程式，或在 Azure 入口網站的應用程式註冊（舊版）體驗中註冊及管理應用程式。

若要深入瞭解新的應用程式註冊體驗，請參閱[Azure 入口網站訓練指南中的應用程式註冊](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)。

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>使用者不再需要在從每個使用者的 MFA 遷移到條件式存取型 MFA 期間重新註冊

**類型：** 固定  
**服務類別：** MFA  
**產品功能：** 身分識別安全性 & 保護

我們已修正已知問題，當使用者因為每個使用者的多重要素驗證（MFA）停用，然後透過條件式存取原則啟用 MFA 時，需要重新註冊。

若要要求使用者重新註冊，您可以從 Azure AD 入口網站中的使用者驗證方法選取 [**必要的重新註冊 MFA** ] 選項。 如需將使用者從每位使用者 MFA 遷移至條件式存取型 MFA 的詳細資訊，請參閱[將使用者從每位使用者 Mfa 轉換成以條件式存取為基礎的 mfa](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)。

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>在 SAML 權杖中轉換和傳送宣告的新功能

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO

我們新增了額外的功能，可協助您在 SAML 權杖中自訂和傳送宣告。 這些新功能包括：

- 額外的宣告轉換函數，可協助您修改在宣告中傳送的值。

- 能夠將多個轉換套用至單一宣告。

- 能夠根據使用者類型和使用者所屬的群組來指定宣告來源。

如需這些新功能的詳細資訊，包括其使用方式，請參閱[針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD 中的終端使用者的新 [我的登入] 頁面

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 監視和報告

我們已新增 [我的登**入**] 頁面（https://mysignins.microsoft.com)可讓您的組織使用者查看其最近的登入歷程記錄，以檢查是否有任何不尋常的活動。 這個新頁面可讓您的使用者看到：

- 如果有人嘗試猜測其密碼。

- 如果攻擊者成功登入其帳戶和位置。

- 攻擊者嘗試存取哪些應用程式。

如需詳細資訊，請參閱[使用者現在可以檢查其登入歷程記錄中是否有異常活動的](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)blog。

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>將 Azure AD Domain Services （Azure AD DS）從傳統遷移至 Azure Resource Manager 虛擬網路

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

對於已經停滯在傳統虛擬網路上的客戶而言，我們有很棒的消息。 您現在可以執行從傳統虛擬網路到現有 Resource Manager 虛擬網路的一次性遷移。 移至 Resource Manager 虛擬網路之後，您將能夠利用額外和升級的功能，例如更細緻的密碼原則、電子郵件通知和審核記錄。

如需詳細資訊，請參閱[預覽-將 Azure AD Domain Services 從傳統虛擬網路模型遷移至 Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)。

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C 頁面合約版面配置的更新

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C

我們對 Azure AD B2C 的頁面合約的版本1.2.0 引進了一些新變更。 在這個更新的版本中，您現在可以控制元素的載入順序，這也有助於停止載入樣式表單（CSS）時所發生的閃爍。

如需對頁面合約所做之變更的完整清單，請參閱[版本變更記錄](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)檔。

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>更新我的應用程式頁面以及新的工作區（公開預覽）

**類型：** 新功能  
**服務類別：** 我的應用程式  
**產品功能：** 存取控制

您現在可以自訂群組織使用者的觀點，並存取全新的我的應用程式體驗，包括使用新的工作區功能，讓他們更輕鬆地尋找應用程式。 新的工作區功能可做為組織使用者已擁有存取權的應用程式篩選。

如需有關推出新的我的應用程式體驗和建立工作區的詳細資訊，請參閱在[我的應用程式（預覽）入口網站上建立工作區](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)。

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>支援每月作用中使用者為基礎的計費模型（正式運作）

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C

Azure AD B2C 現在支援每月作用中使用者（MAU）計費。 MAU 計費是以行事曆月份期間具有驗證活動的唯一使用者數目為基礎。 現有的客戶可以隨時切換到這個新的計費方式。

自2019年11月1日起，所有新客戶都會使用此方法自動計費。 這種計費方法可透過成本優勢和規劃的能力，為客戶帶來好處。

如需詳細資訊，請參閱[升級為每月作用中使用者計費模式](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD App 資源庫中提供的新同盟應用程式-2019 年10月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在2019年10月，我們已將下列35新應用程式新增至應用程式庫的同盟支援：

[發生危機–](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial)行動、 [Juno 旅程](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)、 [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial)、[原樣](https://tact.ai/assistant/)、 [OpusCapita 現金管理](http://cm1.opuscapita.com/tenantname)、 [Salestim](https://prd.salestim.io/forms)、 [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial)、 [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial)、 [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process)、 [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial)、ECornell、 [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial) [SHIPHAZMAT](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial)、 [Netskope 雲端安全性](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)、 [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)、 [Bindtuning](https://bindtuning.com/login)、 [HireVue 座標-歐洲](https://www.hirevue.com/)、 [HireVue 座標-USOnly](https://www.hirevue.com/)， [HIREVUE 座標-US](https://www.hirevue.com/)， [WittyParrot 知識庫](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)， [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial)， [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial)， [Cambium Xirrus EasyPass 入口網站](https://login.xirrus.com/azure-signup) [，Paylocity，](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial)[電子郵件好運！](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial)， [Teamie](https://theteamie.com/)，[小組的速度](https://velocity.peakup.org/teams/login) [，SIGNL4，](https://account.signl4.com/manage) [EAB 流覽 IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial)， [ScreenMeet](https://console.screenmeet.com/)， [Omega 點](https://pi.ompnt.com/)，[說話電子郵件（iphone）](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)，[適用于 Office 365 Direct （iphone/Android）](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)， [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial)， [iHealthHome 護理流覽系統](https://ihealthnav.com/account/signin) [，Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD 入口網站中的匯總安全性功能表項目

**類型：** 已變更的功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護

您現在可以從 [新增**安全性**] 功能表項目，以及從 [**搜尋**] 列的 [Azure 入口網站] 中，存取所有可用的 Azure AD 安全性功能。 此外，新的**安全性**登陸頁面（稱為「**安全性入門**」）將提供我們的公用檔、安全性指引和部署指南的連結。

[新增**安全性**] 功能表包含：

- 條件式存取
- 身分識別保護
- 資訊安全中心
- 身分識別安全分數
- 驗證方法
- MFA
- 風險報告-有風險的使用者、有風險的登入、風險偵測
- 等等

如需詳細資訊，請參閱[安全性-](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)開始使用。

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>使用自動更新增強的 Office 365 群組到期原則

**類型：** 已變更的功能  
**服務類別：** 群組管理  
**產品功能：** 身分識別生命週期管理

已增強 Office 365 群組到期原則，以自動更新其成員正在使用中的群組。 系統會根據所有 Office 365 應用程式的使用者活動（包括 Outlook、SharePoint 和小組）來 autorenewed 群組。

這項增強功能有助於減少群組到期通知，並協助確保作用中的群組可以繼續使用。 如果您的 Office 365 群組已有作用中到期原則，則不需要執行任何動作即可開啟這種新功能。

如需詳細資訊，請參閱[設定 Office 365 群組的到期原則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)。

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>已更新 Azure AD Domain Services （Azure AD DS）建立體驗

**類型：** 已變更的功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

我們已更新 Azure AD Domain Services （Azure AD DS）以納入全新且改良的建立體驗，只要按三下滑鼠，就能協助您建立受控網域！ 此外，您現在可以從範本上傳和部署 Azure AD DS。

如需詳細資訊，請參閱[教學課程：建立和設定 Azure Active Directory Domain Services 實例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)。

---
