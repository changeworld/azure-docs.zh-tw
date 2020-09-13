---
title: 新功能 版本資訊 - Azure Active Directory | Microsoft Docs
description: 瞭解 Azure Active Directory 的新功能;例如最新的版本資訊、已知問題、bug 修正、已淘汰的功能，以及即將進行的變更。
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff942cdad74c3b8b71a8f1658f13faae021b983
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567447"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？

>藉由將此 URL：`https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` 複製並貼到您的 ![RSS 摘要讀取程式圖示](./media/whats-new/feed-icon-16x16.png)摘要讀取程式，即可獲知何時要重新造訪此頁面來進行更新。

Azure AD 會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能
- 方案變更

此頁面會每月更新，因此請定期瀏覽。 如果您要尋找早于六個月的專案，您可以在封存中找到 [Azure Active Directory 的新功能](whats-new-archive.md)。

---

## <a name="august-2020"></a>2020 年 8 月 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication Server 防火牆需求的更新

**類型：** 方案變更  
**服務類別：** Mfa  
**產品功能：** 身分識別安全性 & 保護
 
從2020年10月1日開始，Azure MFA 伺服器防火牆需求需要額外的 IP 範圍。

如果您的組織有輸出防火牆規則，請更新規則，讓 MFA server 能夠與所有必要的 IP 範圍進行通訊。 IP 範圍記錄在 [Azure Multi-Factor Authentication Server 防火牆需求](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)中。

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>身分識別安全分數中的使用者體驗即將變更

**類型：** 方案變更  
**服務類別：** 身分識別保護 **產品功能：** 身分識別安全性 & 保護

我們正在更新身分識別安全分數入口網站，以配合 Microsoft 安全分數 [新版本](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)所引進的變更。 

包含變更的預覽版本將于9月開始提供。 預覽版本中的變更包括：
- 「身分識別安全分數」已重新命名為「身分識別的安全分數」，與 Microsoft 安全分數的品牌一致
- 點正規化為標準規模並以百分比報告，而非點

在此預覽中，客戶可以在現有體驗與新體驗之間切換。 此預覽將持續到2020年11月結束為止。 在預覽之後，客戶將會自動導向至新的 UX 體驗。

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Azure AD-公開預覽中的新限制來賓存取許可權

**類型：** 新功能  
**服務類別：** 存取控制   
**產品功能：** 使用者管理

我們已更新來賓使用者的目錄層級許可權。 這些許可權可讓系統管理員在外部來賓使用者存取上要求額外的限制和控制項。 系統管理員現在可以為外部來賓的使用者和群組設定檔和成員資格資訊的存取權新增額外的限制。 有了這項公開預覽功能，客戶可以藉由模糊化群組成員資格來大規模管理外部使用者的存取權，包括限制來賓使用者查看他們所在的群組 () 的成員資格。

若要深入瞭解，請參閱 [限制的來賓存取許可權](../users-groups-roles/users-restrict-guest-permissions.md) 和 [使用者的預設許可權](./users-default-permissions.md)。
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>服務主體的差異查詢正式推出

**類型：** 新功能  
**服務類別：** MS 圖形  
**產品功能：** 開發人員體驗
 
Microsoft Graph Delta Query 現在支援1.0 版中的資源類型：
- 服務主體

現在用戶端可以有效率地追蹤這些資源的變更，並提供最佳解決方案，以將這些資源的變更與本機資料存放區同步處理。 若要瞭解如何在查詢中設定這些資源，請參閱 [使用 delta 查詢來追蹤 Microsoft Graph 資料中的變更](/graph/delta-query-overview)。
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>適用于 oAuth2PermissionGrant 的 delta 查詢正式推出

**類型：** 新功能  
**服務類別：** MS 圖形  
**產品功能：** 開發人員體驗

Microsoft Graph Delta Query 現在支援1.0 版中的資源類型：
- OAuth2PermissionGrant

用戶端現在可以有效率地追蹤這些資源的變更，並提供最佳解決方案，以將這些資源的變更與本機資料存放區同步處理。 若要瞭解如何在查詢中設定這些資源，請參閱 [使用 delta 查詢來追蹤 Microsoft Graph 資料中的變更](/graph/delta-query-overview)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2020 年8月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在2020年8月，我們已在具有同盟支援的應用程式庫中新增了下列25個新應用程式：

[Backup365](https://portal.backup365.io/login)、 [SOAPBOX](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2)、 [Alma SIS](https://almau.getalma.com/)、 [Enlyft Dynamics 365 連接器](http://enlyft.com/)、 [Serraview 空間使用量軟體解決方案](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md)、 [Uniq](https://web.uniq.app/)、[可見](../saas-apps/visibly-tutorial.md)性[、Zylo](../saas-apps/zylo-tutorial.md)、 [Edmentum-課程評定確切路徑](https://auth.edmentum.com/elf/login)、 [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome)、 [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md)、 [WireWheel](../saas-apps/wirewheel-tutorial.md)、 [Zix 合規性和捕捉](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common)、 [Greenlight 企業商務控制項平臺](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md)、 [genetec clearance 間隙](https://www.clearance.network/)、iSAMS [、VeraSMART、Amiko](../saas-apps/verasmart-tutorial.md) [、Twingate](https://amiko.web.rivero.app/)、[漏斗租用](https://nestiolistings.com/sso/oidc/azure/authorize/) [、](https://scalefusion.com/users/sign_in/) [Scalefusion、Bpanda](../saas-apps/isams-tutorial.md) [、Vivun](https://goto.bpanda.com/login)行事[曆](https://app.vivun.com/dashboard/calendar/connect) [、](https://auth.twingate.com/signup) [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)、 [Wandera 使用者](https://www.wandera.com/)

您也可以在這裡找到所有應用程式的檔 https://aka.ms/AppsTutorial

若要在 Azure AD 應用程式資源庫中列出您的應用程式，請閱讀此處的詳細資料 https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>資源樹系現在可用於 Azure AD DS 

**輸入：** 新功能 **服務類別：** Azure AD Domain Services   
**產品功能：** Azure AD Domain Services
 
Azure AD Domain Services 中資源樹系的功能現已正式推出。 您現在可以在不使用密碼雜湊同步處理的情況下啟用授權，以使用 Azure AD Domain Services，包括智慧卡授權。 若要深入瞭解，請參閱 [Azure Active Directory Domain Services (preview) 的複本集合概念和功能 ](../../active-directory-domain-services/concepts-replica-sets.md)。
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Azure AD DS 受控網域的區域複本支援現已可供使用

**類型：** 新功能   
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services
 
您可以擴充受控網域，讓每個 Azure AD 租用戶都有一個以上的複本集。 複本集可以新增至任何支援 Azure AD Domain Services 的 Azure 區域中的任何對等互連虛擬網路。 不同 Azure 區域中的其他複本集，可在 Azure 區域離線時，為舊版應用程式提供地區性的災害復原。 若要深入瞭解，請參閱 [Azure Active Directory Domain Services (preview) 的複本集合概念和功能 ](../../active-directory-domain-services/concepts-replica-sets.md)。

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Azure AD 我的登入正式推出

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 終端使用者體驗
 
Azure AD 我的登入是一項新功能，可讓企業使用者檢查其登入歷程記錄，以檢查是否有任何不尋常的活動。 此外，這項功能可讓使用者在可疑活動上報告「這不是我」或「這是我」。 若要深入瞭解如何使用這項功能，請參閱 [從 [我的登入] 頁面查看並搜尋最近的登入活動](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)。
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>SAP SuccessFactors HR 驅動的使用者布建至 Azure AD 現已正式推出

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
您現在可以使用 Azure AD 將 SAP SuccessFactors 整合為授權身分識別來源，並使用新進員工和終止等 HR 事件將端對端身分識別生命週期自動化，以在 Azure AD 中推動帳戶的布建和解除布建。 

若要深入瞭解如何設定 SAP SuccessFactors 輸入布建至 Azure AD，請參閱教學課程 [設定 Sap SuccessFactors 以 Active Directory 使用者](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)布建。
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>自訂的 Open ID Connect MS 圖形 API 支援 Azure AD B2C

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C
 
之前，只能透過 Azure 入口網站新增或管理自訂的 Open ID Connect 提供者。 現在 Azure AD B2C 客戶也可以透過 Microsoft Graph Api Beta 版來新增和管理它們。 若要瞭解如何使用 Api 來設定此資源，請參閱 [identityProvider 資源類型](/graph/api/resources/identityprovider?view=graph-rest-beta)。
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>將 Azure AD 內建角色指派給雲端群組

**類型：** 新功能  
**服務類別：** Azure AD 角色  
**產品功能：** 存取控制

您現在可以使用這項新功能，將 Azure AD 內建角色指派給雲端群組。 例如，您可以將 SharePoint 系統管理員角色指派給 Contoso_SharePoint_Admins 群組。 您也可以使用 PIM 讓群組成為角色的合格成員，而不是授與長期存取權。 若要瞭解如何設定這項功能，請參閱 [Azure Active Directory (preview) 中使用雲端群組管理角色指派 ](../users-groups-roles/roles-groups-concept.md)。
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>現在提供見解商務領導者內建角色

**類型：** 新功能  
**服務類別：** Azure AD 角色  
**產品功能：** 存取控制
 
Insights 商務領導者角色中的使用者可透過 [M365 Insights 應用程式](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)存取一組儀表板和深入解析。 這包括所有儀表板的完整存取權，以及提供的深入解析和資料探索功能。 不過，此角色中的使用者無法存取「產品設定」設定，這是「深入解析管理員」角色的責任。 若要深入瞭解此角色，請參閱 [Azure Active Directory 中的系統管理員角色許可權](../users-groups-roles/directory-assign-admin-roles.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>現在提供見解管理員內建角色

**類型：** 新功能  
**服務類別：** Azure AD 角色  
**產品功能：** 存取控制
 
深入解析系統管理員角色中的使用者可以存取 [M365 Insights 應用程式](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)中的完整管理功能集。 此角色的使用者可以讀取目錄資訊、監視服務健康狀態、檔案支援票證，以及存取深入解析系統管理員設定方面。 若要深入瞭解此角色，請參閱 [Azure Active Directory 中的系統管理員角色許可權](../users-groups-roles/directory-assign-admin-roles.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>應用程式管理員和雲端應用程式系統管理員可以管理應用程式的延伸模組屬性

**類型：** 已變更的功能  
**服務類別：** Azure AD 角色  
**產品功能：** 存取控制
 
先前，只有全域管理員可以管理延伸模組 [屬性](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)。 我們現在也為應用程式系統管理員和雲端應用程式系統管理員啟用這項功能。
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 修正程式4.6.263.0 和連接器1.1.1301。0

**類型：** 已變更的功能  
**服務類別：** Microsoft Identity Manager  
**產品功能：** 身分識別生命週期管理

[ (build 4.6.263.0) 的修正程式匯總套件](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident)適用于 MICROSOFT IDENTITY MANAGER (MIM) 2016 Service Pack 2 (SP2) 。 此匯總套件包含 MIM CM、MIM 同步處理管理員和 PAM 元件的更新。 此外，MIM 泛型連接器組建1.1.1301.0 包含 Graph 連接器的更新。

---
 
## <a name="july-2020"></a>2020 年 7 月

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>以 IT 系統管理員的身分，我想要使用條件式存取來鎖定用戶端應用程式

**類型：** 方案變更   
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護
 
由於用戶端應用程式在條件式存取中的正式發行版本，新原則現在會預設套用至所有用戶端應用程式。 這包括舊版驗證用戶端。 現有的原則會維持不變，但 [ *設定為]/[否]* 切換將會從現有的原則中移除，以輕鬆查看原則所套用的用戶端應用程式。 

建立新原則時，請務必排除仍在使用舊版驗證的使用者和服務帳戶。如果不這麼做，將會被封鎖。 [深入了解](https://aka.ms/caclientapps)。
 
---

### <a name="upcoming-scim-compliance-fixes"></a>即將推出的 SCIM 合規性修正

**類型：** 方案變更  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
Azure AD 布建服務會利用 SCIM 標準來與應用程式整合。 我們的 SCIM 標準實行不斷演進，我們預期會對我們執行修補作業的方式進行變更，以及在資源上設定「使用中」屬性。 [深入了解](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)。
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Azure 系統管理員入口網站上的群組擁有者設定將會變更

**類型：** 方案變更  
**服務類別：** 群組管理  
**產品功能：** 共同作業

群組一般設定頁面上的 [擁有者設定] 可設定為在 Azure 系統管理員入口網站中，將擁有者指派許可權限制在一組有限的使用者，並存取面板。 我們很快就能夠在這兩個 UX 入口網站上指派群組擁有者許可權，但也可以在後端強制執行原則，以在端點之間提供一致的行為，例如 PowerShell 和 Microsoft Graph。 

我們將開始針對未使用的客戶停用目前的設定，並在接下來幾個月提供選項來為群組擁有者許可權設定使用者範圍。 如需更新群組設定的指引，請參閱使用 [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)編輯您的群組資訊。

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory 的註冊服務即將結束對 TLS 1.0 和1.1 的支援

**類型：** 方案變更  
**服務類別：** 裝置註冊與管理  
**產品功能：** 平台

傳輸層安全性 (TLS) 1.2 和補救伺服器和用戶端即將與 Azure Active Directory 裝置註冊 Service 進行通訊。 與 Azure AD 裝置註冊 service 進行通訊的 TLS 1.0 和1.1 支援將會淘汰：
- 在2020年8月31日，所有主權雲端 (GCC High、DoD 等等。 ) 
- 于2020年10月30日，所有商業雲端

[深入瞭解](../devices/reference-device-registration-tls-1-2.md) Azure AD 註冊服務的 TLS 1.2。

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Azure AD 登入記錄中可以看見 Windows Hello 企業版登入

**類型：** 固定  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
Windows Hello 企業版可讓終端使用者使用手勢登入 Windows 電腦 (例如 PIN 或生物識別) 。 Azure AD 系統管理員可能會想要區分 Windows Hello 企業版從其他 Windows 登入的登入，作為組織無密碼驗證的旅程。 

系統管理員現在可以在 Azure 入口網站的 Azure AD 登入] 分頁中，檢查 Windows 登入事件的 [驗證詳細資料] 索引標籤，以查看 Windows 驗證是否使用 Windows Hello 企業版。 Windows Hello 企業版驗證會在 [驗證方法] 欄位中包含 "WindowsHelloForBusiness"。 如需有關解讀登入記錄的詳細資訊，請參閱 [登入記錄檔](../reports-monitoring/concept-sign-ins.md)。
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>修正群組刪除行為和效能改進

**類型：** 固定  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
先前，當群組從「範圍內」變更為「超出範圍」，且系統管理員在變更完成之前按下 [重新開機] 時，不會刪除群組物件。 現在，當群組物件超出範圍 (停用、刪除、未指派或未傳遞範圍篩選器) 時，會從目標應用程式中刪除該物件。 [深入了解](../app-provisioning/how-provisioning-works.md#incremental-cycles)。
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>公開預覽：系統管理員現在可以在建立存取權審查時，將電子郵件中的自訂內容新增至審核者

**類型：** 新功能  
**服務類別：** 存取評論  
**產品功能：** 身分識別治理
 
建立新的存取權審查時，審核者會收到一封電子郵件，要求他們完成存取權審核。 許多客戶都要求能夠將自訂內容新增至電子郵件，例如連絡人資訊，或其他其他支援內容以引導審核者。 

系統管理員可以在公開預覽版本中，藉由在 Azure AD 存取權評論的「advanced」區段中新增內容，在傳送給審核者的電子郵件中指定自訂內容。 如需建立存取權評論的指引，請參閱 [Azure AD 存取權評論中建立群組和應用程式的存取權審核](../governance/create-access-review.md)。
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>適用于單一頁面應用程式的授權碼流程

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 開發人員體驗
 
由於新式瀏覽器協力廠商 cookie 的限制（例如 Safari ITP），Spa 將必須使用授權碼流程，而不是隱含流程來維護 SSO，MSAL.js v 2. x 現在將會支援授權碼流程。 

Azure 入口網站有對應的更新，因此您可以將 SPA 更新為 "SPA" 類型，並使用驗證碼流程。 如需進一步指引，請參閱 [使用驗證碼流程登入使用者並取得 JAVASCRIPT SPA 中的存取權杖](../develop/quickstart-v2-javascript-auth-code.md) 。
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD 應用程式 Proxy 現在支援遠端桌面服務 Web 用戶端

**類型：** 新功能  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

Azure AD 應用程式 Proxy 現在支援遠端桌面服務 (RDS) Web 用戶端。 RDS web 用戶端可讓使用者透過任何支援 HTLM5 的瀏覽器（例如 Microsoft Edge、Internet Explorer 11、Google Chrome 等）來存取遠端桌面基礎結構。使用者可以與遠端應用程式或桌面互動，就像是從任何地方使用本機裝置一樣。 藉由使用 Azure AD 應用程式 Proxy，您可以針對所有類型的豐富型用戶端應用程式強制執行預先驗證和條件式存取原則，以提高 RDS 部署的安全性。 如需指引，請參閱 [使用 Azure AD 應用程式 Proxy 發佈遠端桌面](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)。
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>新一代 Azure AD B2C 使用者流程處於公開預覽狀態

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C
 
簡化的使用者流程體驗會提供與預覽功能的功能同位，而且是所有新功能的首頁。 使用者將能夠在相同的使用者流程中啟用新功能，以減少每個新功能版本建立多個版本的需求。 最後，新的易用 UX 可簡化使用者流程的選取和建立。 藉由 [建立使用者流程](../../active-directory-b2c/tutorial-create-user-flows.md)立即試用。 

如需使用者流程的詳細資訊，請參閱 [Azure Active Directory B2C 中的使用者流程版本](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows )。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2020 年7月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2020年7月，我們已在具有同盟支援的應用程式庫中新增下列55個新應用程式：

[Clap 您的手](http://www.rmit.com.ar/)、 [Appreiz](https://microsoftteams.appreiz.com/)、 [Inextor Vault](https://inexto.com/inexto-suite/inextor)、 [Beekast](https://my.beekast.com/)、 [Templafy OpenID Connect](https://app.templafy.com/)、 [PeterConnects 接待員](https://msteams.peterconnects.com/)、 [AlohaCloud](https://appfusions.alohacloud.com/auth)、 [Control 塔式](https://bpm.tnxcorp.com/sso/microsoft)、 [Cocoom](https://start.cocoom.com/)、[硬幣建築雲端](https://sso.coinsconstructioncloud.com/#login/)、 [Medxnote MT](https://task.teamsmain.medx.im/authorization)、 [Reflekt](https://reflekt.konsolute.com/login) [、Rever](https://app.reverscore.net/access)、 [MyCompanyArchive、GReminders](https://login.mycompanyarchive.com/)、Titanfile [、Wootric](../saas-apps/titanfile-tutorial.md) [、SolarWinds](../saas-apps/wootric-tutorial.md)、 [Orion、OpenText](https://app.greminders.com/o365-oauth)、 [Datasite、BlogIn](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US)、 [IntSights](../saas-apps/intsights-tutorial.md)、 [kpifire、](../saas-apps/textline-tutorial.md)[就、Chatwork](../saas-apps/blogin-tutorial.md) [、CloudSign、](../saas-apps/opentext-directory-services-tutorial.md) [Community Spark](../saas-apps/community-spark-tutorial.md) [、](../saas-apps/datasite-tutorial.md)、 [Chatwork](../saas-apps/chatwork-tutorial.md) [、、](../saas-apps/kpifire-tutorial.md) [、](../saas-apps/cloud-academy-sso-tutorial.md) [、](../saas-apps/cloudsign-tutorial.md)、 [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md)、 [SmartHR](https://smarthr.jp/)、 [NumlyEngage™](../saas-apps/numlyengage-tutorial.md)、[密歇根 Data Hub 單一登入](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md) [、輸出、](../saas-apps/egress-tutorial.md) [SendSafely](../saas-apps/sendsafely-tutorial.md)、 [Eletive](https://app.eletive.com/)、[右手邊的網路安全性 ADI](https://right-hand.ai/)、 [Fyde Enterprise Authentication](https://enterprise.fyde.com/)、 [Verme](../saas-apps/verme-tutorial.md)、 [Lenses.io](../saas-apps/lensesio-tutorial.md)、 [Momenta](../saas-apps/momenta-tutorial.md)、 [Uprise](https://app.uprise.co/sign-in)、 [Q](https://q.moduleq.com/login)、 [CloudCords](../saas-apps/cloudcords-tutorial.md)、 [TellMe Bot](https://tellme365liteweb.azurewebsites.net/)、[啟發](https://app.inspiresoftware.com/)、 [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/)、 [Smartschool (School 管理系統) ](https://smart-schoolapp.com/frmLoginForm)、Zepto [-智慧型 timekeeping](https://user.zepto-ai.com/signin)、 [Studi.ly](https://studi.ly/)、 [Trackplan](http://www.trackplanfm.com/)、 [Skedda](../saas-apps/skedda-tutorial.md)、 [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md)、 [Coggle](../saas-apps/coggle-tutorial.md)、 [Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/)、 [BrowserStack 單一登入](../saas-apps/browserstack-single-sign-on-tutorial.md)

您也可以在這裡找到所有應用程式的檔 https://aka.ms/AppsTutorial

若要在 Azure AD 應用程式資源庫中列出您的應用程式，請閱讀此處的詳細資料 https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Azure AD 應用程式資源庫中的新布建連接器-2020 年7月

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 協力廠商整合

您現在可以針對新的整合式應用程式 [LinkedIn Learning](../saas-apps/linkedin-learning-provisioning-tutorial.md)，自動建立、更新及刪除使用者帳戶。

如需如何使用自動化使用者帳戶佈建來加強組織安全性的詳細資訊，請參閱[使用 Azure AD 自動將使用者佈建至 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>查看所有範圍的角色指派，以及將其下載到 csv 檔案的能力

**類型：** 已變更的功能  
**服務類別：** Azure AD 角色  
**產品功能：** 存取控制
 
您現在可以在 Azure AD 入口網站中的 [角色和系統管理員] 索引標籤上，針對角色的所有範圍，查看角色指派。 您也可以將每個角色的角色指派下載至 CSV 檔案。 如需有關如何查看和新增角色指派的指引，請參閱 [Azure Active Directory 中的 [查看和指派系統管理員角色](../users-groups-roles/directory-manage-roles-portal.md)]。
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication 軟體發展 (Azure MFA SDK) 淘汰

**類型：** 已被取代  
**服務類別：** Mfa  
**產品功能：** 身分識別安全性 & 保護
 
Azure Multi-Factor Authentication 軟體發展 (Azure MFA SDK) 已于2018年11月14日結束，已于2017年11月14日宣佈到期。 Microsoft 將會在2020年9月30日起關閉 SDK 服務。 對 SDK 進行的任何呼叫都將會失敗。

如果您的組織使用 Azure MFA SDK，您必須在2020年9月30日之前遷移：
- 適用于 MIM 的 Azure MFA SDK：如果您搭配使用 SDK 與 MIM，則應該遷移至 Azure MFA 伺服器，並依照這些 [指示](/microsoft-identity-manager/working-with-mfaserver-for-mim)啟用 PRIVILEGED ACCESS MANAGEMENT (PAM) 。   
- 適用于自訂應用程式的 Azure MFA SDK：考慮將您的應用程式整合至 Azure AD，並使用條件式存取強制執行 MFA。 若要開始使用，請參閱此 [頁面](../manage-apps/plan-an-application-integration.md)。 

---

## <a name="june-2020"></a>2020 年 6 月 

### <a name="user-risk-condition-in-conditional-access-policy"></a>條件式存取原則中的使用者風險條件

**類型：** 方案變更  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護
 

Azure AD 條件式存取原則中的使用者風險支援，可讓您建立多個以使用者風險為基礎的原則。 不同的使用者和應用程式可能需要不同的最小使用者風險層級。 根據使用者的風險，您可以建立原則來封鎖存取、要求多重要素驗證、安全密碼變更，或重新導向至 Microsoft Cloud App Security 以強制執行會話原則，例如額外的審核。

使用者風險條件需要 Azure AD Premium P2，因為它使用的是 P2 供應專案的 Azure Identity Protection。 如需條件式存取的詳細資訊，請參閱 [Azure AD 條件式存取檔](../conditional-access/index.yml)。

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO 現在支援要求在要求時設定 SPNameQualifier 的應用程式

**類型：** 固定  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
某些 SAML 應用程式需要在要求時，于判斷提示主體中傳回 SPNameQualifier。 現在 Azure AD 在要求 NameID 原則中要求 SPNameQualifier 時，會正確地回應。 這也適用于 SP 起始登入，而 IdP 起始的登入將會遵循。  若要深入瞭解 Azure Active Directory 中的 SAML 通訊協定，請參閱 [單一登入 saml 通訊協定](../develop/single-sign-on-saml-protocol.md)。

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B 共同作業支援在 Azure Government 租使用者中邀請 MSA 和 Google 使用者

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 

使用 B2B 共同作業功能 Azure Government 租使用者現在可以邀請具有 Microsoft 或 Google 帳戶的使用者。 若要瞭解您的租使用者是否可以使用這些功能，請遵循指示 [如何判斷我的 AZURE 美國政府租使用者中是否有可用的 B2B 共同作業？](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>MS Graph v1 中的使用者物件現在包含 externalUserState 和 externalUserStateChangedDateTime 屬性

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 

ExternalUserState 和 externalUserStateChangedDateTime 屬性可用來尋找尚未接受其邀請的受邀 B2B 來賓，以及建立自動化，例如在幾天後刪除尚未接受邀請的使用者。 這些屬性現在可在 MS Graph v1 中使用。 如需使用這些屬性的指引，請參閱 [使用者資源類型](/graph/api/resources/user?view=graph-rest-1.0)。
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Azure AD 條件式存取中管理驗證會話現已正式推出

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護
 
驗證會話管理功能可讓您設定使用者需要提供登入認證的頻率，以及在關閉和重新開啟瀏覽器後是否需要提供認證，以在您的環境中提供更高的安全性和彈性。
 
此外，驗證會話管理僅適用于 Azure AD 聯結、混合式 Azure AD 聯結，以及 Azure AD 註冊的裝置上的第一個要素驗證。 現在驗證會話管理也適用于 MFA。 如需詳細資訊，請參閱 [使用條件式存取設定驗證會話管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2020 年6月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2020年6月，我們已在具有同盟支援的應用程式庫中新增了下列29個新的應用程式：

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md)、 [Ekarda](../saas-apps/ekarda-tutorial.md)、 [MailGates](../saas-apps/mailgates-tutorial.md)、 [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md)、 [Raketa](../saas-apps/raketa-tutorial.md)、 [區段](../saas-apps/segment-tutorial.md)、 [Ai 審計員](https://www.mindbridge.ai/products/ai-auditor/)、 [Pobuca Connect](https://app.pobu.ca/)、 [Proto.io](../saas-apps/proto.io-tutorial.md)， [閘道管理員](https://www.gatekeeperhq.com/)， [中樞規劃](../saas-apps/hub-planner-tutorial.md)， [Ansira-合作夥伴的進入市場工具箱](https://ansira.com/technology/channel-engagement)， [雲端上的 IBM 數位業務自動化](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md)， [Kisi 實體安全性](../saas-apps/kisi-physical-security-tutorial.md)， [ViewpointOne](https://team.viewpoint.com/)， [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md)， [pymetrics](../saas-apps/pymetrics-tutorial.md)， [零](https://www.teamzero.com/)， [InStation](https://instation.invillia.com/)， [edX for Business SAML 2.0 整合](../saas-apps/edx-for-business-saml-integration-tutorial.md)， [MOOC Office 365](https://mooc.office365-training.com/en/)， [SmartKargo](../saas-apps/smartkargo-tutorial.md)， [PKIsigning platform](https://platform.pkisigning.nl/)， [SiteIntel](../saas-apps/siteintel-tutorial.md)， [欄位識別碼](../saas-apps/field-id-tutorial.md)， [課程 SAML](../saas-apps/curricula-saml-tutorial.md)，Perforce [Helix Core-Helix Authentication 服務](../saas-apps/perforce-helix-core-tutorial.md)，MyCompliance [Cloud](https://cloud.metacompliance.com/)， [Smallstep SSH](https://smallstep.com/sso-ssh/)  

您也可以在這裡找到所有應用程式的檔 https://aka.ms/AppsTutorial 。 若要在 Azure AD 應用程式資源庫中列出您的應用程式，請閱讀此處的詳細資訊： https://aka.ms/AzureADAppRequest 。

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>適用于外部身分識別自助式註冊的 API 連接器現在已開放公開預覽

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
外部身分識別 API 連接器可讓您利用 web Api 來整合自助式註冊與外部雲端系統。 這表示您現在可以在註冊流程中將 web Api 叫用為特定步驟，以觸發雲端式自訂工作流程。 例如，您可以使用 API 連接器來：

- 與自訂核准工作流程整合。
- 執行身分識別校對
- 驗證使用者輸入資料
- 覆寫使用者屬性
- 執行自訂商務邏輯

如需 API 連接器的所有可能體驗的詳細資訊，請參閱 [使用 api 連接器自訂和擴充自助式註冊](../external-identities/api-connectors-overview.md)，或 [自訂外部](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)身分識別透過 web API 整合的自助註冊。
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>在幾秒內布建隨選並讓使用者進入您的應用程式

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
Azure AD 布建服務目前以迴圈的方式運作。 服務每隔40分鐘執行一次。 [隨選布建功能](https://aka.ms/provisionondemand)可讓您選擇使用者，並在幾秒鐘內布建。 這項功能可讓您快速針對布建問題進行疑難排解，而不需要重新開機以強制重新開機布建週期。 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>在 Graph 中使用 Azure AD 權利管理的新許可權

**類型：** 新功能  
**服務類別：** 其他  
**產品功能：** 權利管理
 
新的委派許可權 EntitlementManagement，現在已可搭配 Microsoft Graph Beta 版中的權利管理 API 使用。 若要瞭解可用 Api 的詳細資訊，請參閱 [使用 Azure AD 權利管理 api](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)。

---

### <a name="identity-protection-apis-available-in-v10"></a>1.0 版提供的 Identity Protection Api

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護
 
RiskyUsers 和 riskDetections Microsoft Graph Api 現已正式推出。 現在已可在 v1.0 端點上使用它們，我們邀請您在生產環境中使用它們。 如需詳細資訊，請參閱 [Microsoft Graph](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)檔。
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>將原則套用至 Microsoft 365 群組的敏感度標籤現已正式推出

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業
 

您現在可以建立敏感度標籤，並使用標籤設定將原則套用至 Microsoft 365 群組，包括隱私權 (公用或私用) 以及外部使用者存取原則。 您可以建立具有隱私權原則的標籤為私用，而外部使用者存取原則則不允許新增來賓使用者。 當使用者將此標籤套用至群組時，群組將會是私用的，而且不允許將任何來賓使用者新增至群組。 

敏感度標籤很重要，可保護您的商務關鍵資料，並可讓您以符合規範且安全的方式，大規模管理群組。 如需使用敏感度標籤的指導方針，請參閱 [Azure Active Directory (preview) 將敏感度標籤指派給 Microsoft 365 群組 ](../users-groups-roles/groups-assign-sensitivity-labels.md)。
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>支援 Azure AD Premium 客戶 Microsoft Identity Manager 的更新

**類型：** 已變更的功能  
**服務類別：** Microsoft Identity Manager  
**產品功能：** 身分識別生命週期管理
 
Azure 支援現在適用于 Microsoft Identity Manager 2016 的 Azure AD 整合元件，Microsoft Identity Manager 2016 的延伸支援結束。 請參閱 [使用 Microsoft Identity Manager Azure AD Premium 客戶的支援更新，以](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)深入瞭解。

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>已增加在 SSO 宣告設定中使用群組成員資格條件

**類型：** 已變更的功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
先前，您可以在任何單一應用程式設定中根據群組成員資格，以條件式變更宣告時使用的群組數目限制為10個。 在 SSO 宣告設定中使用群組成員資格條件現在已增加到最多50個群組。 如需有關如何設定宣告的詳細資訊，請參閱 [企業應用程式的 SSO 宣告](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions)設定。 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>在公司商標的登入頁面文字元件上啟用基本格式。

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
Azure AD/Microsoft 365 登入體驗上的公司商標功能已更新，可讓客戶加入超連結和簡單的格式設定，包括粗體字型、底線和斜體。 如需使用這項功能的指引，請參閱 [將商標新增至組織的 Azure Active Directory 登入頁面](./customize-branding.md)。

---

### <a name="provisioning-performance-improvements"></a>布建效能改進

**類型：** 已變更的功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
布建服務已更新，可減少 [增量週期](../app-provisioning/how-provisioning-works.md#incremental-cycles) 完成的時間。 這表示使用者和群組將會比以往更快布建到其應用程式。 在6/10/2020 之後建立的所有新布建作業都會自動受益于效能改進。 在6/10/2020 之前設定要布建的任何應用程式都必須在6/10/2020 之後重新開機一次，以充分利用效能改進。 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>宣佈已淘汰 ADAL 和 MS Graph 同位

**類型：** 已被取代  
**服務類別：** N/A  
**產品功能：** 裝置生命週期管理

現在 Microsoft 驗證程式庫 (MSAL) 可供使用，我們將不再將新功能新增至 (ADAL) 的 Azure Active Directory Authentication library，並將在2022年6月30日結束安全性修補程式。 如需如何遷移至 MSAL 的詳細資訊，請參閱將 [應用程式遷移至 Microsoft 驗證程式庫 (MSAL) ](../develop/msal-migration.md)。

此外，我們已完成工作，讓所有 Azure AD Graph 功能可透過 MS Graph 使用。 因此，Azure AD Graph Api 只會在2022年6月30日前收到錯誤修正和安全性修正。 如需詳細資訊，請參閱將 [您的應用程式更新為使用 Microsoft 驗證程式庫和 MICROSOFT GRAPH API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
## <a name="may-2020"></a>2020 年 5 月

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>停用 signIns、riskyUsers 和 riskDetections Api 中的屬性

**類型：** 方案變更  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護

目前，列舉型別用來代表 riskDetections API 和 riskyUserHistoryItem (in preview) 中的 riskType 屬性。 列舉類型也可用於 signIns API 中的 riskEventTypes 屬性。 接下來，我們會將這些屬性工作表示為字串。 

客戶應轉換至 Beta riskDetections 和 riskyUserHistoryItem API 中的 riskEventType 屬性，並在2020年9月9日之前，于 Beta signIns API 中 riskEventTypes_v2 屬性。 在該日期，我們將會淘汰目前的 riskType 和 riskEventTypes 屬性。 如需詳細資訊，請參閱 [Microsoft Graph 上的風險事件屬性和身分識別保護 api 的變更](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)。

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>在 Microsoft Graph 上取代 signIns v1.0 API 中的 riskEventTypes 屬性

**類型：** 方案變更  
**服務類別：** 報告  
**產品功能：** 身分識別安全性 & 保護

列舉型別會在2020年9月 Microsoft Graph 表示風險事件屬性時切換至字串類型。 除了影響預覽 Api，這項變更也會影響生產 signIns API。

我們引進了新的 riskEventsTypes_v2 (字串) 屬性 signIns v1.0 API。 我們會根據我們的 Microsoft Graph 淘汰原則，在2022年6月11日淘汰目前的 riskEventTypes (enum) 屬性。 客戶應該在2022年6月11日之前轉換至 v1.0 signIns API 中的 riskEventTypes_v2 屬性。 如需詳細資訊，請參閱 [Microsoft Graph 上的 signIns V1.0 API 中的 riskEventTypes 屬性](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)取代。

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>MFA 電子郵件通知即將進行的變更

**類型：** 方案變更  
**服務類別：** Mfa  
**產品功能：** 身分識別安全性 & 保護
 

我們正在對雲端 MFA 的電子郵件通知進行下列變更：

電子郵件通知將會從下列位址傳送： azure-noreply@microsoft.com 和 msonlineservicesteam@microsoftonline.com 。 我們正在更新詐騙警示電子郵件的內容，以進一步指出解除封鎖使用的必要步驟。

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>新的自助式註冊可供同盟網域中的使用者存取，因為他們未同步處理至 Azure Active Directory。

**類型：** 方案變更  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 

目前，在 Azure AD 但未同步至租使用者的網域中的使用者，無法存取小組。 從6月底開始，這項新功能可讓使用者藉由擴充現有的電子郵件驗證註冊功能來達成此目的。 這可讓可登入同盟 IdP 的使用者，但在 Azure 識別碼中還沒有使用者物件的使用者，會自動建立使用者物件並對小組進行驗證。 其使用者物件將會標示為「自助式註冊」。 這是現有功能的延伸模組，可進行電子郵件驗證的自我註冊，也就是受控網域中的使用者可以進行，而且可以使用相同的旗標加以控制。 這項變更將在下列兩個月內完成推出。 請觀賞 [此處](../users-groups-roles/directory-self-service-signup.md)的檔更新。
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>即將推出的修正：即將更新 Azure Government 雲端的 OIDC 探索檔，以參考正確的圖形端點。

**類型：** 方案變更  
**服務類別：** 主權雲端  
**產品功能：** 使用者驗證
 
從6月開始，OIDC 探索檔[Azure Government 雲端](../develop/authentication-national-cloud.md)端點上的[Microsoft 身分識別平臺和 OpenID Connect 通訊協定](../develop/v2-protocols-oidc.md) (login.microsoftonline.us) ，將會根據提供的租使用者，開始傳回正確的[國家/地區雲端圖形](/graph/deployments)端點 (https://graph.microsoft.us 或 https://dod-graph.microsoft.us) 。  它目前 (graph.microsoft.com) "msgraph_host" 欄位提供不正確的圖形端點。  

此錯誤修正將會在大約2個月內逐步推出。  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government 使用者將無法再登入 login.microsoftonline.com

**輸入：** 規劃變更  
**服務類別：** 主權雲端  
**產品功能：** 使用者驗證
 
自2018年6月1日起，Azure Government 的官方 Azure Active Directory (Azure AD) 授權單位變更 https://login-us.microsoftonline.com 為 https://login.microsoftonline.us 。 如果您擁有 Azure Government 租使用者內的應用程式，則必須更新您的應用程式，以將使用者登入至 us 端點。

從5月5日起，Azure AD 將開始強制端點變更，封鎖 Azure Government 使用者使用公用端點 (microsoftonline.com) 登入裝載于 Azure Government 租使用者中的應用程式。 受影響的應用程式將會開始看到錯誤 AADSTS900439-USGClientNotSupportedOnPublicEndpoint。 

這項變更將會以漸進方式推出，並預期在2020年6月的所有應用程式中都必須完成強制。 如需詳細資訊，請參閱 [Azure Government 的 blog 文章](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)。

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML 單一登出要求現在會以正確的格式傳送 NameID

**類型：** 固定  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
當使用者按一下登出 (例如，在 MyApps 入口網站) 中，Azure AD 會將 SAML 單一登出訊息傳送至使用者會話中作用中的每個應用程式，並設定登出 URL。 這些訊息包含持續性格式的 NameID。

如果原始 SAML 登入權杖使用不同的 NameID 格式 (例如，電子郵件/UPN) ，則 SAML 應用程式無法將登出訊息中的 NameID 關聯至現有的會話 (因為兩個訊息中使用的 NameIDs 是不同的) ，這會導致 SAML 應用程式捨棄登出訊息，讓使用者保持登入。 此修正可讓登出訊息與針對應用程式所設定的 NameID 保持一致。

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>雲端布建現在提供混合式身分識別管理員角色

**類型：** 新功能  
**服務類別：** Azure AD 雲端布建  
**產品功能：** 身分識別生命週期管理
 
IT 系統管理員可以開始使用新的「混合式系統管理員」角色，作為設定 Azure ADConnect Cloud 布建的最低特殊許可權角色。 使用這個新的角色時，您不再需要使用全域管理員角色來設定和設定雲端布建。 [深入了解](../users-groups-roles/roles-delegate-by-task.md#connect)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD 應用程式庫中可用的新同盟應用程式-5 月2020

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在5月2020中，我們已在具有同盟支援的應用程式庫中新增下列36個新應用程式：

[Moula](https://moula.com.au/pay/merchants)、 [Surveypal](https://www.surveypal.com/app)、 [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/)、 [TackleBox](http://www.tacklebox.app/)、 [Powell 小組](https://powell-software.com/en/powell-teams-en/)、 [Talentsoft 助理](https://msteams.talent-soft.com/)、 [ASC 記錄見解](https://teams.asc-recording.app/product)、 [GO1](https://www.go1.com/)、 [B 參與](https://b-engaged.se/)、 [Competella CONTACT Center Workgroup](http://www.competella.com/)、 [Asite](http://www.asite.com/)、 [ImageSoft Identity](https://identity.imagesoftinc.com/)、 [My IBISWorld](https://identity.imagesoftinc.com/)、 [insuite](../saas-apps/insuite-tutorial.md)、[變更流程管理](../saas-apps/change-process-management-tutorial.md)、 [Cyara CX 保證平臺](../saas-apps/cyara-cx-assurance-platform-tutorial.md)、 [Smart Global 治理](../saas-apps/smart-global-governance-tutorial.md)、 [Prezi](../saas-apps/prezi-tutorial.md)、 [Mapbox](../saas-apps/mapbox-tutorial.md)、 [Datava Enterprise Service PLATFORM](../saas-apps/datava-enterprise-service-platform-tutorial.md)、[比較古怪](../saas-apps/whimsical-tutorial.md)、 [Trelica](../saas-apps/trelica-tutorial.md)、 [EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md)、EasySSO [for BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md)、EasySSO [for Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md)、Torii [、](../saas-apps/torii-tutorial.md) [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md) [、Humanage、ColorTokens](../saas-apps/humanage-tutorial.md)、ZTNA、CCH、Tagetik、ShareVault [、](../saas-apps/vyond-tutorial.md)Vyond [、](../saas-apps/textexpander-tutorial.md)[任何家用 CRM](../saas-apps/anyone-home-crm-tutorial.md)、 [TextExpander](../saas-apps/askspoke-tutorial.md)、 [ ](../saas-apps/cch-tagetik-tutorial.md)[冰連絡人中心](../saas-apps/ice-contact-center-tutorial.md) [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md) [ShareVault](../saas-apps/sharevault-tutorial.md)

您也可以在這裡找到所有應用程式的檔 https://aka.ms/AppsTutorial 。

若要在 Azure AD 應用程式資源庫中列出您的應用程式，請閱讀此處的詳細資料 https://aka.ms/AzureADAppRequest 。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>條件式存取的僅限報表模式現已正式推出

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護

[Azure AD 條件式存取的僅限報表模式](../conditional-access/concept-conditional-access-report-only.md) ，可讓您評估原則的結果，而不需要強制執行存取控制。 您可以在您的組織中測試僅限報告的原則，並在啟用之前瞭解其影響，讓部署更安全且更容易。 在過去幾個月中，我們已發現強式採用僅限報表模式，而不是26M 使用者已在僅限報表原則的範圍內。 今天宣佈推出新的 Azure AD 條件式存取原則時，預設會在報告模式中建立。 這表示您可以在建立原則時監視其影響。 針對使用 MS Graph Api 的使用者，您也可以透過程式設計的 [方式來管理僅限報表的原則](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) 。 

---

### <a name="self-service-sign-up-for-guest-users"></a>來賓使用者的自助式註冊

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
使用 Azure AD 中的外部身分識別，您可以讓組織外部的人員存取您的應用程式和資源，同時讓他們使用偏好的任何身分識別登入。 與外部使用者共用應用程式時，您不一定都能事先知道哪些人需要存取應用程式。 使用 [自助式註冊](../external-identities/self-service-sign-up-overview.md)時，您可以讓來賓使用者註冊並取得您企業營運 (LOB) 應用程式的來賓帳戶。 您可以建立和自訂註冊流程，以支援 Azure AD 和社交身分識別。 您也可以在註冊期間收集使用者的其他相關資訊。

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>條件式存取深入解析和報告活頁簿已正式推出

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護

[深入解析和報告活頁簿](../conditional-access/howto-conditional-access-insights-reporting.md)可讓系統管理員在其租使用者中 Azure AD 條件式存取的摘要觀點。 系統管理員可以選取個別原則，更進一步瞭解每個原則的作用和監視任何變更。 活頁簿會將儲存在 Azure 監視器中的資料串流處理，您可以在幾分鐘內依照 [這些指示](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)進行設定。 為了讓儀表板更容易探索，我們已將它移至 Azure AD 條件式存取功能表中的 [新的深入解析和報告] 索引標籤。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>條件式存取的原則詳細資料分頁處於公開預覽狀態

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護

[新增 [原則詳細資料](../conditional-access/troubleshoot-conditional-access.md) ] 分頁會顯示條件式存取原則評估期間所滿足的指派、條件和控制項。 您可以在登入詳細資料的 [條件式存取] 或 [僅限報表] 索引標籤中選取資料列，以存取該分頁。

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Microsoft Graph 中的目錄物件的新查詢功能處於公開預覽狀態

**類型：** 新功能  
**服務類別：** MS Graph **產品功能：** 開發人員體驗

Microsoft Graph Directory 物件 Api、啟用計數、搜尋、篩選和排序作業的新功能即將推出。 這會讓開發人員能夠快速查詢目錄物件，而不需要因應措施，例如記憶體內部篩選和排序。 您將在這 [篇 blog 文章](https://aka.ms/CountFilterMSGraphAAD)中進一步瞭解。

我們目前處於公開預覽狀態，正在尋找意見反應。 請使用這 [份簡短問卷](https://aka.ms/MsGraphAADSurveyDocs)傳送您的意見。

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>使用 Microsoft Graph API (搶鮮版（Beta）設定 SAML 型單一登入) 

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
現在提供在 Beta 版中使用 MS Graph Api 從 Azure AD 資源庫建立和設定應用程式的支援。 如果您需要為應用程式的多個實例設定 SAML 型單一登入，請使用 Microsoft Graph Api 來將 [saml 型單一登入的設定自動化](/graph/application-saml-sso-configure-api)，以節省時間。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD 應用程式資源庫中的新布建連接器-5 月2020

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 協力廠商整合
 
您現在可以為這些新整合的應用程式，自動建立、更新和刪除使用者帳戶：

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [依組織列出 New Relic](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

如需如何使用自動化使用者帳戶佈建來加強組織安全性的詳細資訊，請參閱[使用 Azure AD 自動將使用者佈建至 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML 權杖加密已正式推出

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
[SAML 權杖加密](../manage-apps/howto-saml-token-encryption.md) 可讓應用程式設定為接收加密的 SAML 判斷提示。 這項功能現已在所有雲端中正式推出。
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>應用程式權杖中的組名宣告已正式推出

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
權杖中發出的群組宣告現在可以僅限於指派給應用程式的群組。  當使用者是大量群組的成員，而且有超過權杖大小限制的風險時，這一點特別重要。 有了這項新功能，就能 [將組名新增至權杖](../hybrid/how-to-connect-fed-group-claims.md) 的功能已正式運作。
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday 回寫現在支援設定公司電話號碼屬性

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
我們已增強 Workday 回寫布建應用程式，現在支援公司電話號碼和行動電話號碼屬性的回寫。 除了電子郵件和使用者名稱之外，您現在還可以設定 Workday 回寫布建應用程式，將電話號碼值從 Azure AD 傳送到 Workday。 如需有關如何設定電話號碼回寫的詳細資訊，請參閱 [Workday 回寫](https://aka.ms/WorkdayWriteback) 應用程式教學課程。 

---

### <a name="publisher-verification-preview"></a>發行者驗證 (預覽) 

**類型：** 新功能  
**服務類別：** 其他  
**產品功能：** 開發人員體驗
 
發行者驗證 (預覽版) 可協助系統管理員和終端使用者瞭解應用程式開發人員與 Microsoft 身分識別平臺整合的真實性。 如需詳細資訊，請參閱 [發行者驗證 (預覽) ](../develop/publisher-verification-overview.md)。
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>單一頁面應用程式的授權碼流程

**輸入：** 變更功能 **服務類別：** 驗證 **產品功能：** 開發人員體驗

由於新式瀏覽器 [協力廠商 cookie 的限制（例如 SAFARI ITP](../develop/reference-third-party-cookies-spas.md)），spa 將必須使用授權碼流程，而不是隱含流程來維護 SSO;MSAL.js v2.0 現在將支援授權碼流程。 這是 Azure 入口網站的對應更新，因此您可以將 SPA 更新為 "SPA" 類型，並使用驗證碼流程。 如需指引，請參閱 [快速入門：使用驗證碼流程來登入使用者並取得 JAVASCRIPT SPA 中的存取權杖](../develop/quickstart-v2-javascript-auth-code.md)。

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>改善的裝置篩選處於公開預覽狀態

**輸入：** 已變更功能   
**服務類別：** 裝置管理 **產品功能：** 裝置生命週期管理
 
先前，您可以使用的篩選準則為「已啟用」和「活動日期」。 現在，您可以 [在更多屬性上篩選您的裝置清單](../devices/device-management-azure-portal.md#device-list-filtering-preview)，包括 OS 類型、聯結類型、合規性等等。 這些新增專案應該可簡化尋找特定裝置的工作。

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Azure AD B2C 的新應用程式註冊體驗現已正式推出

**輸入：** 已變更功能   
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** 身分識別生命週期管理
 
Azure AD B2C 的新應用程式註冊體驗現已正式推出。 

先前，您必須使用舊版的「應用程式」體驗，與您的應用程式其餘部分分開管理 B2C 消費者面向應用程式。 這表示不同的應用程式在 Azure 中的不同位置之間的建立體驗。

新體驗會顯示所有 B2C 應用程式註冊，並在一個位置 Azure AD 應用程式註冊，並提供一致的方式來管理它們。 無論您是否需要管理客戶面向的應用程式或可存取 Microsoft Graph 以程式設計方式管理 Azure AD B2C 資源的應用程式，您只需要學習一種方法來進行作業。

您可以藉由流覽 Azure AD B2C 服務並選取應用程式註冊分頁，來達到新的體驗。 您也可以從 Azure Active Directory 服務存取此體驗。

Azure AD B2C 應用程式註冊體驗是根據 Azure AD 租使用者的一般 [應用程式註冊體驗](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) ，但專為 Azure AD B2C 量身打造。 舊版的「應用程式」體驗將在未來淘汰。

如需詳細資訊，請造訪 [Azure AD B2C 的新應用程式註冊體驗](https://aka.ms/b2cappregtraining)。

---

## <a name="april-2020"></a>2020 年 4 月

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>結合的安全性資訊註冊體驗現已正式推出

**類型：** 新功能

**服務類別：** 驗證 (登入)

**產品功能：** 身分識別安全性 & 保護

Multi-Factor Authentication (MFA) 和自助式密碼重設 (SSPR) 的結合註冊體驗現已正式推出。 這項新的註冊體驗可讓使用者在單一逐步程式中註冊 MFA 和 SSPR。 當您為您的組織部署新的體驗時，使用者可以在較短的時間內註冊，而且也比較少。 請參閱 [這裡](https://bit.ly/3etiRyQ)的 blog 文章。

---

### <a name="continuous-access-evaluation"></a>連續存取評估

**類型：** 新功能

**服務類別：** 驗證 (登入)

**產品功能：** 身分識別安全性 & 保護

持續存取評估是一項新的安全性功能，可讓信賴憑證者在 Azure AD (（例如使用者帳戶刪除) ）中發生事件時，近乎即時地強制使用 Azure AD 存取權杖的原則。 我們會先為小組和 Outlook 用戶端推出這項功能。 如需詳細資訊，請閱讀我們的 [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) 和  [檔](./concept-fundamentals-continuous-access-evaluation.md)。

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS 登入：第一線 Worker 可以使用電話號碼和密碼登入 Azure AD 支援的應用程式

**類型：** 新功能

**服務類別：** 驗證 (登入)

**產品功能：** 使用者驗證

Office 正在啟動一系列行動優先的商務應用程式，這些應用程式可滿足非傳統的組織，以及對不使用電子郵件作為主要通訊方法的大型組織中的員工。 這些應用程式的目標 frontline 員工、deskless 員工、現場代理商或零售員工可能無法從其雇主取得電子郵件地址、可存取電腦或存取電腦。 此專案可讓這些員工輸入電話號碼並 roundtripping 程式碼，以登入商務應用程式。 如需詳細資料，請參閱我們的系統 [管理員檔](../authentication/howto-authentication-sms-signin.md) 和 [使用者檔](../user-help/sms-sign-in-explainer.md)。

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>邀請內部使用者使用 B2B 協同作業

**類型：** 新功能

**服務類別：** B2B

**產品功能：**

我們正在擴大 B2B 邀請功能，以允許邀請現有的內部帳戶使用 B2B 共同作業認證。 這是藉由將使用者物件傳遞給邀請 API，以及一般參數（像是受邀的電子郵件地址）來完成。 使用者的物件識別碼、UPN、群組成員資格、應用程式指派等等都會保持不變，但未來會使用 B2B 來驗證其主租使用者認證，而不是在邀請之前使用的內部認證。 如需詳細資訊，請參閱 [檔](../external-identities/invite-internal-users.md)。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>條件式存取的僅限報表模式現已正式推出

**類型：** 新功能

**服務類別：** 條件式存取

**產品功能：** 身分識別安全性 & 保護

[Azure AD 條件式存取的僅限報表模式](../conditional-access/concept-conditional-access-report-only.md) ，可讓您評估原則的結果，而不需要強制執行存取控制。 您可以在您的組織中測試僅限報告的原則，並在啟用之前瞭解其影響，讓部署更安全且更容易。 在過去幾個月中，我們已瞭解採用僅限報表模式的強式採用，而26M 使用者已在僅限報表原則的範圍內。 在此公告中，預設會在僅限報表模式下建立新的 Azure AD 條件式存取原則。 這表示您可以在建立原則時監視其影響。 此外，對於使用 MS Graph Api 的使用者，您也可以透過程式設計的 [方式來管理僅限報表的原則](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)。 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>條件式存取深入解析和報告活頁簿已正式推出

**類型：** 新功能

**服務類別：** 條件式存取

**產品功能：** 身分識別安全性 & 保護

條件式存取 [深入解析和報告活頁簿](../conditional-access/howto-conditional-access-insights-reporting.md) 可讓系統管理員在其租使用者中 Azure AD 條件式存取的摘要查看。 系統管理員可以選取個別原則，更進一步瞭解每個原則的作用和監視任何變更。 活頁簿會將儲存在 Azure 監視器中的資料串流處理，您可以在幾分鐘內依照 [這些指示](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)進行設定。 為了讓儀表板更容易探索，我們已將它移至 Azure AD 條件式存取功能表中的 [新的深入解析和報告] 索引標籤。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>條件式存取的原則詳細資料分頁處於公開預覽狀態

**類型：** 新功能

**服務類別：** 條件式存取

**產品功能：** 身分識別安全性 & 保護

[新增 [原則詳細資料](../conditional-access/troubleshoot-conditional-access.md) ] 分頁會顯示條件式存取原則評估期間滿足的指派、條件和控制項。 您可以在登入詳細資料的 [條件式 **存取** ] 或 [ **僅限報表** ] 索引標籤中選取資料列，以存取該分頁。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD App 資源庫中可用的新同盟應用程式-2020 年4月

**類型：** 新功能

**服務類別：** 企業應用程式

**產品功能：** 協力廠商整合

在2020年4月，我們已將具有同盟支援的這31個新應用程式新增至應用程式庫： 

[SincroPool Apps](https://www.sincropool.com/)、 [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/)、 [Float](../saas-apps/float-tutorial.md)、 [LMS365](https://lms.365.systems/)、 [IWT 採購 Suite](../saas-apps/iwt-procurement-suite-tutorial.md)、 [Lunni](https://lunni.fi/)、 [EasySSO for Jira](../saas-apps/easysso-for-jira-tutorial.md)、[虛擬訓練](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft)學術、 [Meraki 儀表板](../saas-apps/meraki-dashboard-tutorial.md)、 [Microsoft 365 移動](https://app.mover.io/login)、[說話者](https://speakerengage.com/login.php) [、老實、](../saas-apps/honestly-tutorial.md)Ally [、DutyFlow、](https://app.dutyflow.nl/) [AlertMedia](../saas-apps/alertmedia-tutorial.md)、 [gr8 人員](../saas-apps/gr8-people-tutorial.md)、Pendo、 [HighGround](../saas-apps/highground-tutorial.md)、Timetabling [、SynchroNet、](../saas-apps/timetabling-solutions-tutorial.md) [Fortes、](../saas-apps/pendo-tutorial.md)Litmus [empower](https://www.made-in-office.com/en/) [、GroupTalk](../saas-apps/harmony-tutorial.md) [、Frontify](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) [、TickitLMS、](../saas-apps/synchronet-click-tutorial.md)COCO、Nitro [、)  (](../saas-apps/nitro-productivity-suite-tutorial.md) TMWS、、 [、](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [、、](../saas-apps/fortes-change-cloud-tutorial.md) [、](../saas-apps/tickitlms-learn-tutorial.md) [、](../saas-apps/litmus-tutorial.md) [、](../saas-apps/mongodb-cloud-tutorial.md) [、](../saas-apps/frontify-tutorial.md)、 [、](https://recorder.grouptalk.com/) [Ally](../saas-apps/ally-tutorial.md)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>適用于 oAuth2PermissionGrant 的 Microsoft Graph delta 查詢支援可供公開預覽

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 開發人員體驗

適用于 oAuth2PermissionGrant 的 Delta 查詢提供公開預覽！ 您現在可以追蹤變更，而不需要持續輪詢 Microsoft Graph。 [深入了解。](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>適用于組織連絡人的 Microsoft Graph delta 查詢支援已正式運作

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 開發人員體驗

組織連絡人的 Delta 查詢已正式推出！ 您現在可以在生產應用程式中追蹤變更，而不需要持續輪詢 Microsoft Graph。 取代任何現有的程式碼，以依差異查詢持續輪詢 orgContact 資料，以大幅提升效能。 [深入了解。](/graph/api/orgcontact-delta?tabs=http&view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>適用于應用程式的 Microsoft Graph delta 查詢支援已正式運作

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 開發人員體驗

應用程式的 Delta 查詢已正式推出！ 您現在可以在生產應用程式中追蹤變更，而不需要持續輪詢 Microsoft Graph。 取代任何現有的程式碼，以依差異查詢持續輪詢應用程式資料，以大幅提升效能。 [深入了解。](/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>適用于公開預覽的系統管理單位 Microsoft Graph 差異查詢支援

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 適用于管理單位的開發人員體驗差異查詢已提供公開預覽！ 您現在可以追蹤變更，而不需要持續輪詢 Microsoft Graph。 [深入了解。](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>在新的 Microsoft Graph 搶鮮版 Api 中管理驗證電話號碼及更多

**類型：** 新功能

**服務類別：** MS 圖形

**產品功能：** 開發人員體驗

這些 Api 是管理使用者驗證方法的主要工具。 現在您可以透過程式設計方式預先註冊及管理用於 MFA 和自助式密碼重設 (SSPR) 的驗證器。 這是 Azure MFA、SSPR 和 Microsoft Graph 空間中最常要求的功能之一。 我們在此 wave 中發行的新 Api，讓您能夠：

- 讀取、新增、更新和移除使用者的驗證電話
- 重設使用者的密碼
- 開啟和關閉 SMS 登入

如需詳細資訊，請參閱 [Azure AD 驗證方法 API 總覽](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)。

---

### <a name="administrative-units-public-preview"></a>管理單位公開預覽

**類型：** 新功能

**服務類別：** Azure AD 角色

**產品功能：** 存取控制

管理單位可讓您授與系統管理權限，這些權限受限於您所定義組織的部門、區域或其他區段。 您可以使用管理單位將權限委派給區域管理員或以細微的層級設定原則。 例如，使用者帳戶管理員可以更新設定檔資訊、重設密碼，且只針對其管理單位中的使用者指派授權。

使用管理單位，中央系統管理員可以：

- 建立管理單位以進行分散式資源管理
- 使用系統管理許可權來指派僅限管理單位中 Azure AD 使用者的角色
- 視需要使用使用者和群組填入系統管理單位

如需詳細資訊，請參閱 [Azure Active Directory (preview) 中的管理單位管理 ](https://aka.ms/AdminUnitsDocs)。

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>印表機系統管理員和印表機技術人員內建角色

**類型：** 新功能

**服務類別：** Azure AD 角色

**產品功能：** 存取控制

**印表機系統管理員**：具備此角色的使用者可以註冊印表機，並管理 Microsoft 通用列印解決方案中所有印表機設定的所有層面，包括通用列印連接器設定。 他們可以同意所有委派的列印權限要求。 印表機管理員也具有列印報告的權限。 

**印表機技術人員**：具備此角色的使用者可以在 Microsoft 通用列印解決方案中登錄印表機及管理印表機狀態。 他們也可以讀取所有連接器資訊。 印表機技術人員無法進行的主要工作，是設定印表機和共用印表機的使用者權限。 [深入了解。](../users-groups-roles/directory-assign-admin-roles.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>混合式身分識別管理員內建角色

**類型：** 新功能

**服務類別：** Azure AD 角色

**產品功能：** 存取控制

具備此角色的使用者可以啟用、設定及管理與在 Azure AD 中啟用混合式身分識別相關的服務和設定。 此角色可讓您將 Azure AD 設定為其中一種支援的驗證方法&#8212;密碼雜湊同步處理 (PHS) 、傳遞驗證 (PTA) 或同盟 (AD FS 或協力廠商同盟提供者) # B1，以及部署相關的內部部署基礎結構來加以啟用。 內部部署基礎結構包括布建和 PTA 代理程式。 此角色能夠啟用無縫單一登入 (SSO)，以在非 Windows 10 裝置或非 Windows Server 2016 電腦上啟用無縫驗證。 此外，此角色會授與查看登入記錄的能力，以及存取健康情況與分析以進行監視和疑難排解。 [深入了解。](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>網路系統管理員內建角色

**類型：** 新功能

**服務類別：** Azure AD 角色

**產品功能：** 存取控制

具有此角色的使用者可以根據來自其使用者位置的網路遙測，檢查來自 Microsoft 的網路周邊架構建議。 Microsoft 365 的網路效能依賴審慎的企業客戶網路周邊架構，通常是使用者位置特定的架構。 此角色能夠編輯探索到的使用者位置及設定這些位置的網路參數，促使改善遙測測量和設計建議。 [深入了解。](../users-groups-roles/directory-assign-admin-roles.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD 系統管理員入口網站體驗中的大量活動與下載

**類型：** 新功能

**服務類別：** 使用者管理

**產品功能：** 目錄

現在，您可以在 Azure AD 系統管理員入口網站體驗中上傳 CSV 檔案，以在 Azure AD 的使用者和群組上執行大量活動。 您可以建立使用者、刪除使用者，以及邀請來賓使用者。 您可以新增和移除群組中的成員。

您也可以從 Azure AD 系統管理員入口網站體驗下載 Azure AD 資源的清單。 您可以在目錄中下載使用者清單、目錄中的群組清單，以及特定群組的成員。

如需詳細資訊，請參閱下列內容：

- [建立使用者](../users-groups-roles/users-bulk-add.md) 或 [邀請來賓使用者](../external-identities/tutorial-bulk-invite.md)
- [刪除使用者](../users-groups-roles/users-bulk-delete.md) 或 [還原已刪除的使用者](../users-groups-roles/users-bulk-restore.md)
- [下載使用者清單](../users-groups-roles/users-bulk-download.md) 或 [下載群組清單](../users-groups-roles/groups-bulk-download.md)
- [新增 (匯入) 成員](../users-groups-roles/groups-bulk-import-members.md)或[移除成員](../users-groups-roles/groups-bulk-remove-members.md)或[下載群組成員清單](../users-groups-roles/groups-bulk-download-members.md)

---

### <a name="my-staff-delegated-user-management"></a>我的員工委派的使用者管理

**類型：** 新功能

**服務類別：** 使用者管理

**產品功能：**

「我的員工」可讓第一線管理員（例如商店經理）確保其員工成員能夠存取 Azure AD 帳戶。 組織可以將一般工作（例如重設密碼或變更電話號碼）委派給第一線 Manager，而不是依賴中央技術服務人員。 使用「我的員工」時，無法存取其帳戶的使用者只要按幾下，就可以重新取得存取權，而不需要技術服務人員或 IT 人員。 如需詳細資訊，請參閱使用 [我的員工來管理您的使用者 (預覽) ](https://aka.ms/MyStaffAdminDocs) 並 [使用我的員工來委派使用者管理 (preview) ](https://aka.ms/MyStaffUserDocs)。

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>存取權評論中已升級的終端使用者體驗

**類型：** 已變更的功能

**服務類別：** 存取評論

**產品功能：** 身分識別治理

我們已在我的應用程式入口網站中更新 Azure AD 存取權評論的審核者體驗。 在四月月底，已登入「Azure AD 存取權審查審核者」體驗的審核者將會看到橫幅，讓他們能夠在我的存取權中試用更新的體驗。 請注意，更新的存取權審核體驗提供與目前體驗相同的功能，但在新功能上有改善的使用者介面，可讓使用者提高生產力。 [您可以在這裡深入瞭解更新的體驗](../governance/perform-access-review.md)。 此公開預覽將于2020年7月底前持續。 7月底，未進入預覽體驗的審核者會自動導向我的存取權，以執行存取權審核。 如果您想要讓審核者永久切換至我的存取權的預覽體驗， [請在這裡提出要求](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)。

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday 輸入使用者布建和回寫應用程式現在支援最新版本的 Workday Web 服務 API

**類型：** 已變更的功能

**服務類別：** 應用程式佈建

**產品功能：** 

根據客戶的意見反應，我們現在已更新企業應用程式庫中的 Workday 輸入使用者布建和回寫應用程式，以支援最新版本的 Workday Web 服務 (WWS) API。 有了這項變更，客戶就可以指定要在連接字串中使用的 WWS API 版本。 這讓客戶能夠取得 Workday 發行版本中可用的 HR 屬性。 Workday 回寫應用程式現在會使用建議的 Change_Work_Contact_Info Workday web 服務來克服 Maintain_Contact_Info 的限制。

如果連接字串中未指定任何版本，則根據預設，Workday 輸入布建應用程式會繼續使用 WWS v 21.1 來切換至最新的 Workday Api 以進行輸入使用者布建，客戶需要更新連接字串（如本 [教學課程中](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) 所述），以及更新 workday 屬性的 xpath，如 [workday 屬性參考指南](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)中所述。 

若要使用新的 API 進行回寫，Workday 回寫布建應用程式中不需要進行任何變更。 在 Workday 端，確定 Workday 整合系統使用者 (ISU) 帳戶具有叫用 Change_Work_Contact 商務程式的許可權，如教學課程一節中所述， [設定商務程式安全性原則許可權](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions)。 

我們已更新 [教學課程指南](../saas-apps/workday-inbound-tutorial.md) ，以反映新的 API 版本支援。

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>具有預設存取角色的使用者現在已在布建範圍內

**類型：** 已變更的功能

**服務類別：** 應用程式佈建

**產品功能：** 身分識別生命週期管理

在過去，具有預設存取角色的使用者已超出布建的範圍。 我們聽說客戶希望具有此角色的使用者可以在布建範圍內的意見反應。 從2020年4月16日起，所有新的布建設定都允許布建預設存取角色的使用者。 我們會逐漸變更現有布建設定的行為，以支援使用此角色來布建使用者。 [深入了解。](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>更新布建 UI

**類型：** 已變更的功能

**服務類別：** 應用程式佈建

**產品功能：** 身分識別生命週期管理

我們已重新整理布建體驗，以建立更具焦點的管理檢視。 當您流覽至已設定之企業應用程式的布建分頁時，您將能夠輕鬆地監視布建和管理動作的進度，例如啟動、停止和重新開機布建。 [深入了解。](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>動態群組規則驗證現在可供公開預覽

**類型：** 已變更的功能

**服務類別：** 群組管理

**產品功能：** 共同作業

Azure Active Directory (Azure AD) 現在提供驗證動態群組規則的方法。 在 [ **驗證規則** ] 索引標籤上，您可以針對範例群組成員驗證動態規則，以確認規則如預期般運作。 當建立或更新動態群組規則時，系統管理員想要知道使用者或裝置是否為群組的成員。 這有助於評估使用者或裝置是否符合規則準則，並在不需要成員資格時協助進行疑難排解。

如需詳細資訊，請參閱 [驗證動態群組成員資格規則 (預覽) ](../users-groups-roles/groups-dynamic-rule-validation.md)。

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>身分識別安全分數-安全性預設值和 MFA 改進動作更新

**類型：** 已變更的功能

**服務類別：** N/A

**產品功能：** 身分識別安全性 & 保護

**支援 Azure AD 改進動作的安全性預設值：** Microsoft 安全分數將會更新改進動作，以支援 [Azure AD 中的安全性預設值](./concept-fundamentals-security-defaults.md)，讓您更輕鬆地使用預先設定的安全性設定來保護您的組織，以進行常見的攻擊。 這會影響下列改進動作：

- 確定所有使用者都可以完成安全存取的多重要素驗證
- 系統管理角色需要 MFA
- 啟用原則以封鎖舊版驗證
 
**MFA 改進動作更新：** 為了反映企業在套用與企業合作的原則時，必須確保最安全性的需求，Microsoft 安全分數已移除三個以多重要素驗證為中心的改進動作，並新增了兩個。

已移除改進動作：

- 註冊所有使用者進行多重要素驗證
- 要求所有使用者使用 MFA
- Azure AD 特殊許可權角色需要 MFA

新增的改進動作：

- 確定所有使用者都可以完成安全存取的多重要素驗證
- 系統管理角色需要 MFA

這些新的改進動作需要為您的使用者或系統管理員註冊多因素驗證 (MFA) 在您的目錄中，並建立符合您組織需求的正確原則組。 主要的目標是要有彈性，同時確保所有的使用者和系統管理員都可以使用多個因素或以風險為基礎的身分識別驗證提示進行驗證。 這樣做的形式可能會有多個原則套用範圍決策，或設定安全性預設值 (從3月) 16 日開始，讓 Microsoft 決定何時要挑戰使用者進行 MFA。 [深入瞭解 Microsoft 安全分數的新功能](/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021年3月 B2B 更新中的非受控 Azure Active Directory 帳戶

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
自**2021 年3月31日起**，Microsoft 將不再支援在 B2B 共同作業案例中建立非受控 Azure Active Directory (Azure AD) 帳戶和租使用者來兌換邀請。 為此，我們建議您選擇以電子郵件傳送單次 [密碼驗證](../external-identities/one-time-passcode.md)。

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>具有預設存取角色的使用者將在布建範圍內

**類型：** 方案變更  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
在過去，具有預設存取角色的使用者已超出布建的範圍。 我們聽說客戶希望具有此角色的使用者可以在布建範圍內的意見反應。 我們正致力於部署變更，讓所有新的布建設定都能允許布建預設存取角色的使用者。 我們會逐漸變更現有布建設定的行為，以支援使用此角色來布建使用者。 不需要客戶動作。 當這項變更準備就緒之後，我們就會將更新張貼到我們的 [檔](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) 。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD 的 B2B 共同作業將可在由世紀 (Azure 中國世紀) 租使用者的 Microsoft Azure 運作

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure AD 的 B2B 共同作業功能將會提供給由世紀 (Azure 中國世紀) 租使用者的 Microsoft Azure 營運，讓 Azure 中國世紀租使用者中的使用者能與其他 Azure 中國世紀租使用者中的使用者順暢地共同作業。 [深入瞭解 AZURE AD B2B 協同](/azure/active-directory/b2b/)作業。

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B 共同作業邀請電子郵件重新設計

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure AD B2B 共同作業邀請服務所傳送的 [電子郵件](../external-identities/invitation-email-elements.md) 邀請使用者加入目錄，將會重新設計，讓邀請資訊和使用者的後續步驟更清楚。

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery 原則變更將會出現在 audit 記錄檔中

**類型：** 固定  
**服務類別：** 審計  
**產品功能：** 監視和報告
 
修正了 [HomeRealmDiscovery 原則](../manage-apps/configure-authentication-for-federated-users-portal.md) 的變更未包含在 audit 記錄檔中的錯誤（bug）。 您現在將能夠看到原則的變更時間和方式，以及由誰變更。 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD App 資源庫中可用的新同盟應用程式-2020 年3月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2020年3月，我們已將下列具有同盟支援的51新應用程式新增至應用程式庫： 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)， [Zoho 一中國](../saas-apps/zoho-one-china-tutorial.md)， [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/)、 [Profit.co SAML APP](../saas-apps/profitco-saml-app-tutorial.md)、 [iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md)、 [contexxt.ai 球體](https://contexxt-sphere.com/login)、 [Invictus](../saas-apps/wisdom-by-invictus-tutorial.md)[的](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md)[智慧、Logz.io、可檢視性](https://spark-dev.pixelnebula.com/login) [、SpectrumU、](../saas-apps/spectrumu-tutorial.md) [BizzContact SSO](../saas-apps/elqano-sso-tutorial.md) [、Elqano](http://www.signshare.com/)、 [MarketSignShare Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md) [、CrossKnowledge](https://bizzcontact.app/) [Netvision](../saas-apps/netvision-compas-tutorial.md)、Compas [HUB](../saas-apps/fcm-hub-tutorial.md)、[筋 A/S FCM Mobile](https://apps.apple.com/us/app/docia/id529058757)、 [Byggeweb](../saas-apps/golinks-tutorial.md)、 [GoLinks](../saas-apps/datadog-tutorial.md)、Datadog [B2B 使用者入口網站](../saas-apps/zscaler-b2b-user-portal-tutorial.md) [、增益、](../saas-apps/lift-tutorial.md) [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md)、 [Zscaler](https://www.devfinition.com/) [、WatchTeams、](https://demo.asterapp.io/login)[技能工作流程](../saas-apps/skills-workflow-tutorial.md)、[節點深入](https://admin.nodeinsight.com/AADLogin.aspx)解析、 [IP Platform](../saas-apps/ip-platform-tutorial.md)、 [InVision](../saas-apps/invision-tutorial.md)、 [Pipedrive](../saas-apps/pipedrive-tutorial.md)、[展示研討會](https://app.showcaseworkshop.com/)、 [Greenlight 整合平臺](../saas-apps/greenlight-integration-platform-tutorial.md)、 [Greenlight 相容的存取管理](../saas-apps/greenlight-compliant-access-management-tutorial.md)、 [Grok 學習](../saas-apps/grok-learning-tutorial.md)、 [Miradore Online](https://login.online.miradore.com/)、 [khoros care 護理](../saas-apps/khoros-care-tutorial.md)、 [AskYourTeam](../saas-apps/askyourteam-tutorial.md)、 [TruNarrative](../saas-apps/trunarrative-tutorial.md)、 [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)、 [Bizagi Studio （數位程式自動化](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md)）、 [insuiteX](https://www.insuite.jp/)、 [sybo](https://www.systexsoftware.com.tw/)、 [Britive](../saas-apps/britive-tutorial.md) [、WhosOffice、](../saas-apps/whosoffice-tutorial.md) [E 天](../saas-apps/e-days-tutorial.md)、 [Kollective SDN](https://portal.kollective.app/login)、 [Witivio](https://app.witivio.com/) [、Playvox、Korn](https://my.playvox.com/login) [Ferry 360](../saas-apps/korn-ferry-360-tutorial.md)、[校園咖啡館](../saas-apps/campus-cafe-tutorial.md)、 [Catchpoint](../saas-apps/catchpoint-tutorial.md)、 [Code42](../saas-apps/code42-tutorial.md)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure Government 租使用者中可用的 Azure AD B2B 共同作業

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure AD 的 B2B 共同作業功能現在可在某些 Azure Government 租使用者之間取得。  若要瞭解您的租使用者是否能夠使用這些功能，請遵循指示 [如何判斷我的 AZURE 美國政府租使用者中是否有可用的 B2B 共同作業？](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)。

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure 記錄的 Azure 監視器整合現在可在 Azure Government 中取得

**類型：** 新功能  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
Azure 監視器與 Azure AD 記錄的整合現在已可在 Azure Government 中取得。 您可以將 Azure AD 記錄 () 至儲存體帳戶、事件中樞和 Log Analytics 的「審核」和「登入」記錄傳送。 請參閱詳細的 [檔](https://aka.ms/aadlogsinamd) ，以及 [用於報告和監視](../reports-monitoring/plan-monitoring-and-reporting.md) Azure AD 案例的部署計畫。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure Government 中的身分識別保護重新整理

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護

我們很高興能分享我們現在已[Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)   在[Microsoft Azure Government 入口網站](https://portal.azure.us/)中推出重新整理的 Azure AD Identity Protection 體驗。 如需詳細資訊，請參閱我們的 [公告 blog 文章](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>嚴重損壞修復：下載並儲存您的布建設定

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
Azure AD 布建服務提供一組豐富的設定功能。 客戶必須能夠儲存其設定，讓他們可以稍後參考，或回復為已知的良好版本。 我們已新增將布建設定下載為 JSON 檔案的功能，並在需要時將其上傳。 [深入了解](../app-provisioning/export-import-provisioning-configuration.md)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (自助式密碼重設) 現在需要兩個由世紀 Microsoft Azure 營運的系統管理員的閘道 (Azure 中國世紀)  

**類型：** 已變更的功能  
**服務類別：** 自助式密碼重設  
**產品功能：** 身分識別安全性 & 保護
 
先前在 Microsoft Azure 由世紀 (Azure 中國的世紀) ，使用自助式密碼重設的系統管理員 (SSPR) 重設自己的密碼，只需要一個「閘道」 (挑戰) 證明其身分識別。 在公用和其他國家雲端中，系統管理員通常必須在使用 SSPR 時使用兩個閘道來證明其身分識別。 但由於我們不支援 Azure 中國世紀的 SMS 或通話，因此我們允許系統管理員進行一次閘道密碼重設。

我們正在建立 Azure 中國的世紀和公用雲端之間的 SSPR 功能同位檢查。 接下來，系統管理員必須在使用 SSPR 時使用兩個閘道。 系統將支援 SMS、通話和驗證器代理程式更新和代碼。 [深入了解](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>密碼長度限制為256個字元

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
為確保 Azure AD 服務的可靠性，使用者密碼現在的長度限制為256個字元。 密碼超過此密碼的使用者將被要求在後續登入時變更其密碼，方法是聯絡其系統管理員或使用自助式密碼重設功能。

這項變更已在2020年3月13日、10AM PST (18:00 UTC) 上啟用，而錯誤為 AADSTS 50052、InvalidPasswordExceedsMaxLength。 如需詳細資訊，請參閱 [重大變更通知](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) 。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>所有免費租使用者現在都可以透過 Azure 入口網站 Azure AD 登入記錄

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
從現在開始，具有免費租使用者的客戶可以 [從 Azure 入口網站存取 Azure AD 登入記錄](../reports-monitoring/concept-sign-ins.md) ，最多7天。 先前，登入記錄僅適用于具有 Azure Active Directory Premium 授權的客戶。 透過這項變更，所有租使用者都可以透過入口網站存取這些記錄。

> [!NOTE]
> 客戶仍然需要 (Azure Active Directory Premium P1 或 P2) 的 premium 授權，才能透過 Microsoft Graph API 和 Azure 監視器存取登入記錄。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Azure 入口網站上群組一般設定中的整個目錄群組選項

**類型：** 已被取代  
**服務類別：** 群組管理  
**產品功能：** 共同作業

為了提供更有彈性的方式，讓客戶建立最符合其需求的全目錄群組，我們已從 Azure 入口網站中的 [**群組**一般設定] 取代 [**全目錄群組**] 選項，  >  **General**並提供[動態群組檔](../users-groups-roles/groups-dynamic-membership.md)的連結。 我們已改善檔以包含更多指示，讓系統管理員可以建立包含或排除來賓使用者的所有使用者群組。

---
