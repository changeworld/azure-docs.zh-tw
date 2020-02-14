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
ms.date: 01/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5bf9c5c80593066e31b1ff23b6def844f67e65
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185770"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？

>藉由複製並貼上此 URL，取得何時要重新流覽此頁面以取得更新的通知： `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` 至 ![RSS 摘要讀取器圖示](./media/whats-new/feed-icon-16x16.png) 摘要讀取器。

Azure AD 會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能
- 方案變更

此頁面會每月更新，因此請定期瀏覽。 如果想要尋找超過 6 個月的項目，請至 [Azure Active Directory 的新增功能封存](whats-new-archive.md) (英文)。

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

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial)、 [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial)、 [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial)、 [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial)、 [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/)、 [SkyKick Cloud Backup for Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)、 [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)、 [LEAVEBOT](https://leavebot.io/#home)、 [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial)、 [TRIPACTIONS](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial)、 [SMARTWORK](https://www.intumit.com/english/SmartWork.html)、 [DOTCOM-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial)、 [SSOGEN-Azure AD SSO Gateway for Oracle 電子商務套件-EBS、PeopleSoft 和 JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial)、 [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial)、 [Yuhu 屬性管理平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial)、 [LumApps](https://sites.lumapps.com/login)、[Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial)、 [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial)、 [SmartDB for Microsoft 小組](http://teams.smartdb.jp/login/)、 [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial)、 [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial)、 [Maxient 籌辦經理軟體](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial)、 [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial)、 [PortalTalk 365](https://www.portaltalk.com/)、 [CoreView](https://portal.coreview.com/)、[隱藏 Cloud Office365 Connector](https://laxmi.squelch.io/login)、 [PingFlow Authentication](https://app-staging.pingview.io/)、 [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial)、 [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial)、 [Sandwai](https://app.sandwai.com/)、 [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)、 [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial)、 [Akari虛擬助理](https://akari.io/akari-virtual-assistant/)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="two-new-identity-protection-detections"></a>兩個新的身分識別保護偵測

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護
 
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

作為 cookie 的安全預設模型的一部分，Chrome 80 瀏覽器會變更其處理 cookie 的方式，而不需要 `SameSite` 屬性。 未指定 `SameSite` 屬性的任何 cookie 都會被視為已設定為 `SameSite=Lax`，這會導致 Chrome 封鎖您的應用程式可能依存的某些跨網域 cookie 共用案例。 若要維護舊版 Chrome 行為，您可以使用 `SameSite=None` 屬性並新增額外的 `Secure` 屬性，因此跨網站 cookie 只能透過 HTTPS 連線來存取。 Chrome 已排程于2020年2月4日完成這項變更。

我們建議所有開發人員使用此指引來測試其應用程式：

- 將 [**使用安全 Cookie** ] 設定的預設值設定為 **[是]** 。

- 將 [ **SameSite** ] 屬性的預設值設定為 [**無**]。

- 新增其他 `SameSite` 屬性為 [**安全**]。

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

Azure 入口網站上新的**Azure AD App 註冊權杖**設定 分頁現在會向應用程式開發人員顯示其應用程式選擇性宣告的動態清單。 這種新體驗有助於簡化 Azure AD 的應用程式遷移，並將選擇性的宣告錯誤錯誤降至最低。

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
**產品功能：** 身分識別安全性與保護

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

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial)， [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial)，[成員（BAM）的藍色存取](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial)、 [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial)、 [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial)、 [ResLife 入口網站](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=)、 [NegometrixPortal 單一登入（SSO）](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial)、 [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279)、 [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial)、 [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial)、 [BlueMail](https://loginself1.bluemail.me/)、 [Beedle](https://teams-web.beedle.co/#/)、 [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial)、 [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial)、 [Foko 零售](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial)、Qmarkets[概念 & 創新管理](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial)、 [Netskope 使用者驗證](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial)、 [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial)、 [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial)， [Jisc Student 投票者註冊](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial)， [e4enable](https://portal.e4enable.com/)

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
**產品功能：** 身分識別安全性與保護

為回應開發人員的意見反應，Azure AD Premium P2 訂閱者現在可以使用適用于 Microsoft Graph 的新 riskDetection API，對 Azure AD Identity Protection 的風險偵測資料執行複雜的查詢。 現有的[identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API 搶鮮版（Beta）版本將會停止傳回**2020 年1月10日**附近的資料。 如果您的組織使用 identityRiskEvent API，您應該轉換至新的 riskDetection API。

如需有關新 riskDetection API 的詳細資訊，請參閱[風險偵測 API 參考檔](https://aka.ms/RiskDetectionsAPI)。

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>SameSite 屬性和 Chrome 80 的應用程式 Proxy 支援

**類型：** 方案變更  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

在 Chrome 80 瀏覽器版本之前的幾周，我們打算更新應用程式 Proxy cookie 如何處理**SameSite**屬性。 當 Chrome 80 發行時，任何未指定**SameSite**屬性的 cookie 都會視為已設定為 `SameSite=Lax`。

為了避免因這項變更而造成負面影響，我們將藉由下列方式來更新應用程式 Proxy 存取和會話 cookie：

- 將 [**使用安全 Cookie** ] 設定的預設值設定為 **[是]** 。

- 將**SameSite**屬性的預設值設定為 [**無**]。

    >[!NOTE]
    > 應用程式 Proxy 存取 cookie 一律以獨佔方式透過安全通道傳輸。 這些變更只適用于會話 cookie。

如需應用程式 Proxy cookie 設定的詳細資訊，請參閱[Azure Active Directory 中用來存取內部部署應用程式的 cookie 設定](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)。

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>應用程式註冊入口網站（apps.dev.microsoft.com）中的應用程式註冊（舊版）和聚合式應用程式管理將無法再使用

**類型：** 方案變更  
**服務類別：** N/A  
**產品功能：** 開發人員體驗

在不久的未來，具有 Azure AD 帳戶的使用者將無法再使用應用程式註冊入口網站（apps.dev.microsoft.com）來註冊和管理交集應用程式，或在應用程式註冊中註冊和管理應用程式（舊版）Azure 入口網站中的經驗。

若要深入瞭解新的應用程式註冊體驗，請參閱[Azure 入口網站訓練指南中的應用程式註冊](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)。

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>使用者不再需要在從每個使用者的 MFA 遷移到條件式存取型 MFA 期間重新註冊

**類型：** 固定  
**服務類別：** MFA  
**產品功能：** 身分識別安全性與保護

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

我們已新增 [我的登**入**] 頁面（ https://mysignins.microsoft.com)，讓您的組織使用者可以查看其最近的登入歷程記錄，以檢查是否有任何不尋常的活動。 這個新頁面可讓您的使用者看到：

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

[發生危機-](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial)行動裝置、 [Juno 旅程](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)圖、 [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial)、[原樣](https://tact.ai/assistant/)、 [OpusCapita 現金管理](http://cm1.opuscapita.com/tenantname)、 [Salestim](https://prd.salestim.io/forms)、 [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial)、 [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial)、 [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process)、 [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial)、 [eCornell、ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial)、 [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)、 [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)、 [Bindtuning](https://bindtuning.com/login) [、HireVue](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial)[座標-EU](https://www.hirevue.com/)、 [HireVue 座標-USOnly](https://www.hirevue.com/)、 [HireVue 座標-US](https://www.hirevue.com/)、 [WittyParrot知識箱](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)， [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial)， [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial)， [Cambium Xirrus EasyPass 入口網站](https://login.xirrus.com/azure-signup)， [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial)， [Mail 好運！](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial)， [Teamie](https://theteamie.com/)，[團隊的速度](https://velocity.peakup.org/teams/login)， [SIGNL4，](https://account.signl4.com/manage) [EAB 導覽 IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial)， [ScreenMeet](https://console.screenmeet.com/)， [Omega 點](https://pi.ompnt.com/)， [Intune 的電子郵件（Iphone）](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)，[說話的電子郵件，適用于 Office 365 Direct （iphone/Android）](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)， [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial)， [iHealthHome 護理流覽系統](https://ihealthnav.com/account/signin)， [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD 入口網站中的匯總安全性功能表項目

**類型：** 已變更的功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護

您現在可以從 [新增**安全性**] 功能表項目，以及從 [**搜尋**] 列的 [Azure 入口網站] 中，存取所有可用的 Azure AD 安全性功能。 此外，新的**安全性**登陸頁面（稱為「**安全性入門**」）將提供我們的公用檔、安全性指引和部署指南的連結。

[新增**安全性**] 功能表包含：

- 條件式存取
- 身分識別保護
- 資訊安全中心
- 身分識別安全分數
- 驗證方法
- MFA
- 風險報告-有風險的使用者、有風險的登入、風險偵測
- 還有更多 。

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

## <a name="september-2019"></a>2019 年 9 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>規劃變更：已淘汰 Power BI 內容套件

**類型：** 方案變更  
**服務類別：** 報告  
**產品功能：** 監視和報告

從2019年10月1日開始，Power BI 將開始取代所有內容套件，包括 Azure AD Power BI 內容套件。 除了此內容套件之外，您還可以使用 Azure AD 活頁簿，深入瞭解您的 Azure AD 相關服務。 其他活頁簿即將推出，包括有關僅限報表模式中的條件式存取原則、以應用程式同意為基礎的深入解析等等的活頁簿。

如需活頁簿的詳細資訊，請參閱[如何使用 Azure Active Directory 報表的 Azure 監視器活頁簿](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。 如需有關取代內容套件的詳細資訊，請參閱[宣佈 Power BI 範本應用程式正式](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)運作的 blog 文章。

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>我的設定檔正在重新命名，並與 Microsoft Office 帳戶頁面整合

**類型：** 方案變更  
**服務類別：** 我的設定檔/帳戶  
**產品功能：** 共同作業

從10月開始，我的設定檔體驗將會變成我的帳戶。 做為這項變更的一部分，目前顯示的所有**內容都會變更**為 [**我的帳戶**]。 在命名變更和一些設計改進方面，更新的體驗會與 Microsoft Office 帳戶 頁面提供額外的整合。 具體而言，您將能夠從 [**總覽帳戶**] 頁面存取 office 安裝和訂閱，以及 [**隱私權**] 頁面中的 office 相關連絡人喜好設定。

如需有關 [我的設定檔（預覽）] 體驗的詳細資訊，請參閱[我的設定檔（預覽）入口網站總覽](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)。

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>在 Azure AD 入口網站中使用 CSV 檔案大量管理群組和成員（公開預覽）

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

我們很高興能在 Azure AD 入口網站中宣佈大量群組管理體驗的公開預覽可用性。 您現在可以使用 CSV 檔案和 Azure AD 入口網站來管理群組和成員清單，包括：

- 新增或移除群組中的成員。

- 從目錄下載群組清單。

- 正在下載特定群組的群組成員清單。

如需詳細資訊，請參閱[大量新增成員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)、[大量移除成員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)、[大量下載成員清單](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)和[大量下載群組清單](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)。

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>透過新的系統管理員同意端點，現已支援動態同意

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

我們已建立新的系統管理員同意端點來支援動態同意，這對於想要在 Microsoft 身分識別平臺上使用動態同意模型的應用程式很有説明。

如需如何使用這個新端點的詳細資訊，請參閱[使用系統管理員同意端點](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Azure AD App 資源庫中提供的新同盟應用程式-2019 年9月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

我們已在2019年9月將下列29個新應用程式新增至應用程式庫，並提供同盟支援：

[ScheduleLook](https://schedulelook.bbsonlineservices.net/)， [Ethidex 合規性的 MS Azure SSO 存取權 Office™-單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial)、 [iServer 入口網站](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial)、 [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial)、 [Concur 旅遊和費用](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial)、 [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial)、 [YeeFlow](https://apps.yeeflow.com/)、 [ARC 設施](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial)、 [Luware Stratus 小組](https://stratus.emea.luware.cloud/login)、[廣泛構想](https://wideideas.online/wideideas/)、 [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial)、 [JDLT 用戶端中樞](https://clients.jdlt.co.uk/login)、 [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial)、 [SealPath 安全瀏覽器](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive)、 [Prisma cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial)、 [Penneo](https://app.penneo.com/) [、Hiretual、](https://app.testhtm.com/settings/email-integration) [Cintoo cloud](https://aec.cintoo.com/login)、[Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial)，[託管的資訊保護線上 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial)， [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial)， [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial)， [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial)， [Coo Kai TEAM Build](https://ms-contacts.coo-kai.jp/)， [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial)， [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial)，[探索優點 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial)， [Amelio](https://app.amelio.co/)， [iTask](https://itask.yipinapp.com/)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-azure-ad-global-reader-role"></a>新增 Azure AD 全域讀取者角色

**類型：** 新功能  
**服務類別：** RBAC  
**產品功能：** 存取控制

從2019年9月24日開始，我們將開始推出名為全域讀取者的新 Azure Active Directory （AD）角色。 此首度發行將從生產和全球雲端客戶（GCC）開始，在10月的全球完成。

全域讀取者角色是全域管理員的唯讀對應。 此角色中的使用者可以跨 Microsoft 365 服務讀取設定和系統管理資訊，但無法採取管理動作。 我們已建立全域讀取者角色，以協助減少組織中全域管理員的數目。 由於全域管理員帳戶的功能強大且容易受到攻擊，因此建議您擁有少於五個全域管理員。 我們建議使用全域讀取者角色進行規劃、審核或調查。 我們也建議將全域讀取者角色與其他有限的系統管理員角色（例如 Exchange 系統管理員）搭配使用，以協助完成工作而不需要全域管理員角色。

「全域讀取者」角色適用于新的 Microsoft 365 系統管理中心、Exchange 系統管理中心、小組系統管理中心、資訊安全中心、合規性中心、Azure AD 系統管理中心，以及裝置管理系統管理中心。

>[!NOTE]
> 在公開預覽開始時，全域讀者角色將無法使用： SharePoint、特殊許可權的存取管理、客戶加密箱、敏感度標籤、小組生命週期、報告 & 通話分析的小組、IP 電話裝置管理和小組應用程式目錄. 這些服務全都適用于未來的角色。

如需詳細資訊，請參閱[Azure Active Directory 中的系統管理員角色許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 應用程式 Proxy 從您的 Power BI 行動版應用程式存取內部部署報表伺服器

**類型：** 新功能  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

Power BI 行動應用程式與 Azure AD 應用程式 Proxy 之間的新整合，可讓您安全地登入 Power BI 行動應用程式，並查看裝載于內部部署 Power BI 報表伺服器上的任何組織報表。

如需 Power BI 行動版應用程式的相關資訊，包括下載應用程式的位置，請參閱[Power BI 網站](https://powerbi.microsoft.com/mobile/)。 如需如何使用 Azure AD 應用程式 Proxy 設定 Power BI 行動應用程式的詳細資訊，請參閱[使用 Azure AD 應用程式 Proxy 啟用 Power BI 行動版的遠端存取](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)。

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>AzureADPreview PowerShell 模組的新版本可供使用

**類型：** 已變更的功能  
**服務類別：** 其他  
**產品功能：** 目錄

新的 Cmdlet 已新增至 AzureADPreview 模組，以協助在 Azure AD 中定義和指派自訂角色，包括：

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect 的新版本

**類型：** 已變更的功能  
**服務類別：** 其他  
**產品功能：** 目錄

我們已發行自動升級客戶的更新版本 Azure AD Connect。 這個新版本包含數個新功能、改進和 bug 修正。 如需這個新版本的詳細資訊，請參閱[Azure AD Connect：版本發行歷程記錄](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)。

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure 多重要素驗證（MFA）伺服器（版本8.0.2）現已推出

**類型：** 固定  
**服務類別：** MFA  
**產品功能：** 身分識別安全性與保護

如果您是在2019年7月1日前啟用 MFA Server 的現有客戶，您現在可以下載最新版本的 MFA Server （版本8.0.2）。 在這個新版本中，我們：

- 已修正問題：當 Azure AD 同步處理將使用者從停用變更為啟用時，系統會傳送電子郵件給使用者。

- 已修正問題，讓客戶可以成功升級，同時繼續使用標記功能。

- 已新增科索沃（+ 383）國家/地區代碼。

- 已將一次性略過審核記錄新增至 MultiFactorAuthSvc。

- 改善 Web 服務 SDK 的效能。

- 已修正其他次要錯誤。

自2019年7月1日起，Microsoft 已停止為新的部署提供 MFA 伺服器。 需要多重要素驗證的新客戶應該使用以雲端為基礎的 Azure 多因素驗證。 如需詳細資訊，請參閱[規劃以雲端為基礎的 Azure 多因素驗證部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>群組的增強搜尋、篩選和排序功能可在 Azure AD 入口網站中取得（公開預覽）

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

我們很高興能在 Azure AD 入口網站中宣佈增強群組相關體驗的公開預覽可用性。 這些增強功能可協助您更有效地管理群組和成員清單，方法是提供：

- 先進的搜尋功能，例如 [群組] 清單上的 [子字串搜尋]。
- 成員和擁有者清單上的先進篩選和排序選項。
- 成員和擁有者清單的新搜尋功能。
- 較精確的群組計數適用于大型群組。

如需詳細資訊，請參閱[管理 Azure 入口網站中的群組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)。

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>新的自訂角色適用于應用程式註冊管理（公開預覽）

**類型：** 新功能  
**服務類別：** RBAC  
**產品功能：** 存取控制

自訂角色（可透過 Azure AD P1 或 P2 訂用帳戶取得）現在可以協助您提供更細緻的存取權，方法是讓您以特定許可權建立角色定義，然後將這些角色指派給特定的資源。 目前，您可以使用管理應用程式註冊的許可權，然後將角色指派給特定的應用程式，來建立自訂角色。 如需自訂角色的詳細資訊，請參閱[Azure Active Directory （預覽）中的自訂系統管理員角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)。

如果您需要支援的其他許可權或資源（目前並未看到），您可以將意見反應傳送給我們的[Azure 意見反應網站](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)，我們會將您的要求新增至我們的更新藍圖。

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>新的布建記錄可協助您監視和疑難排解您的應用程式布建部署（公開預覽）

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理

新的布建記錄檔可協助您監視和疑難排解使用者和群組布建部署。 這些新的記錄檔包含下列資訊：

- 已成功在[ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)中建立哪些群組
- 哪些角色是從 Amazon Web Services 匯入[（AWS）](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- 哪些員工未從[Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)匯入

如需詳細資訊，請參閱在[Azure Active Directory 入口網站中布建報表（預覽）](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)。

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>所有 Azure AD 系統管理員的新安全性報告（公開上市）

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護

根據預設，所有 Azure AD 系統管理員很快就能存取 Azure AD 內的新式安全性報告。 在9月底之前，您將能夠使用新式安全性報告頂端的橫幅來回到舊的報告。

新式安全性報告將提供較舊版本的其他功能，包括：

- 先進的篩選和排序
- 大量動作，例如關閉使用者風險
- 確認遭盜用或安全的實體
- 風險狀態，涵蓋：風險、已解除、已補救且已確認遭到入侵
- 新的風險相關偵測（可供 Azure AD Premium 訂閱者使用）

如需詳細資訊，請參閱有[風險的使用者](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users)、有風險的登[入](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)，以及[風險](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections)偵測。

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>使用者指派的受控識別適用于虛擬機器和虛擬機器擴展集（公開上市）

**類型：** 新功能  
**服務類別：** 適用于 Azure 資源的受控識別  
**產品功能：** 開發人員體驗

使用者指派的受控識別現在已正式提供虛擬機器和虛擬機器擴展集。 在此過程中，Azure 可以在使用中的訂用帳戶所信任的 Azure AD 租使用者中建立身分識別，並可將其指派給一或多個 Azure 服務實例。 如需使用者指派受控識別的詳細資訊，請參閱[什麼是適用于 Azure 資源的受控識別？](https://aka.ms/azuremanagedidentity)。

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>使用者可以使用行動應用程式或硬體權杖（一般可用性）重設其密碼

**類型：** 已變更的功能  
**服務類別：** 自助式密碼重設  
**產品功能：** 使用者驗證

已向您的組織註冊行動應用程式的使用者，現在可以藉由核准來自 Microsoft Authenticator 應用程式的通知，或從行動應用程式或硬體權杖輸入程式碼，來重設自己的密碼。

如需詳細資訊，請參閱[運作方式： Azure AD 自助式密碼重設](https://aka.ms/authappsspr)。 如需使用者體驗的詳細資訊，請參閱[重設您自己的工作或學校密碼總覽](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)。

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET 會針對代理者案例忽略 MSAL.NET 共用快取

**類型：** 固定  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

從 Azure AD authentication library （ADAL.NET）第5.0.0 版-preview 開始，應用程式開發人員必須[針對 web 應用程式和 Web api，將每個帳戶的一個](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)快取序列化。 否則，某些使用代理者[流程](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)的案例，以及某些特定的 `UserAssertion`使用案例，可能會導致權限提高。 為避免此弱點，ADAL.NET 現在會忽略適用于代理者案例的 Microsoft authentication library for dotnet （MSAL.NET）共用快取。

如需有關此問題的詳細資訊，請參閱[Azure Active Directory 驗證程式庫權限提高弱點](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD App 資源庫中提供的新同盟應用程式-2019 年8月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在2019年8月，我們已將下列26個具有同盟支援的新應用程式新增至應用程式庫：

[市政平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial)， [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial)， [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)， [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial)， [Viareport 的 Inativ Portal （歐洲）](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial)， [Azure Databricks](https://azure.microsoft.com/services/databricks)，[資源](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)管理員，學術[出席](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial)，[優先順序對照表](https://sync.appfluence.com/pmwebng/)， [Cousto MySpace](https://cousto.platformers.be/account/login)， [Uploadcare](https://uploadcare.com/accounts/signup/)， [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial)，CPQSync [by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial)， [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial)，[提供。 media™ Portal](https://portal.deliver.media)，[第一線教育](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial)， [F5](https://www.f5.com/products/security/access-policy-manager)， [stashcat ADconnect](https://www.stashcat.com)、[閃爍](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial)、 [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial)、 [ProNovos 分析](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial)、 [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial)、 [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial)、[依色彩監看](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial) [、控管、](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial) [EAB 流覽策略護理](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>有新版本的 AzureAD PowerShell 和 AzureADPreview PowerShell 模組可供使用

**類型：** 已變更的功能  
**服務類別：** 其他  
**產品功能：** 目錄

AzureAD 和 AzureAD Preview PowerShell 模組的新更新可供使用：

- 已將新的 `-Filter` 參數新增至 AzureAD 模組中的 `Get-AzureADDirectoryRole` 參數。 此參數可協助您篩選 Cmdlet 所傳回的目錄角色。
- 新的 Cmdlet 已新增至 AzureADPreview 模組，以協助在 Azure AD 中定義和指派自訂角色，包括：

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Azure 入口網站中動態群組規則產生器的 UI 改善

**類型：** 已變更的功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

我們對動態群組規則產生器提供了一些 UI 改良功能，可在 Azure 入口網站中使用，以協助您更輕鬆地設定新規則，或變更現有的規則。 這項設計改進可讓您建立最多五個運算式的規則，而不只是一個。 我們也已更新裝置屬性清單，以移除已淘汰的裝置屬性。

如需詳細資訊，請參閱[管理動態成員資格規則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)。

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>新的 Microsoft Graph 應用程式許可權可與存取審查搭配使用

**類型：** 已變更的功能  
**服務類別：** 存取權檢閱  
**產品功能：** 身分識別治理

我們引進了新的 Microsoft Graph 應用程式許可權 `AccessReview.ReadWrite.Membership`，可讓應用程式自動建立和抓取群組成員資格和應用程式指派的存取權審查。 此許可權可供您的排程工作或自動化的一部分使用，而不需要登入的使用者內容。

如需詳細資訊，請參閱[如何使用 PowerShell 的 Microsoft Graph 應用程式許可權建立 Azure AD 存取評論的範例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)。

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD 活動記錄現在可供中的政府雲端實例使用 Azure 監視器

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視和報告

我們很高興宣佈，Azure 監視器的政府雲端實例現已提供 Azure AD 活動記錄。 您現在可以將 Azure AD 記錄傳送到您的儲存體帳戶或事件中樞，以與 SIEM 工具（例如[Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)、 [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)和[ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight)）整合。 

如需設定 Azure 監視器的詳細資訊，請參閱[Azure 監視器中的 Azure AD 活動記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)。

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>將您的使用者更新為新的增強式安全性資訊體驗

**類型：** 已變更的功能  
**服務類別：** 驗證（登入）   
**產品功能：** 使用者驗證

在2019年9月25日，我們將關閉舊的非增強式安全性資訊體驗，以註冊和管理使用者安全性資訊，並只開啟新的[增強版](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。 這表示您的使用者將無法再使用舊體驗。

如需增強式安全性資訊體驗的詳細資訊，請參閱我們的系統[管理員檔](https://aka.ms/securityinfodocs)和我們的[使用者檔](https://aka.ms/securityinfoguide)。

#### <a name="to-turn-on-this-new-experience-you-must"></a>若要開啟此新體驗，您必須：

1. 以全域管理員或使用者系統管理員身分登入 Azure 入口網站。

2. 移至**Azure Active Directory > 使用者設定 > 管理存取面板預覽功能的設定**。

3. 在 [**使用者可以使用預覽功能來註冊及管理安全性資訊-增強**] 區域中，選取 [已**選取**]，然後選擇一組使用者，或選擇 [**全部**] 以針對租使用者中的所有使用者開啟此功能。

4. 在 [使用者可以使用預覽功能來註冊及管理安全性 * * info * * *] 區域中，選取 [**無**]。

5. 儲存您的設定。

    儲存設定之後，您將無法再存取舊的安全性資訊體驗。

>[!Important]
>如果您未在2019年9月25日之前完成這些步驟，將會自動啟用您的 Azure Active Directory 租使用者以獲得增強的體驗。 如有任何疑問，請在 registrationpreview@microsoft.com洽詢我們。

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>使用 POST 登入的驗證要求將會更嚴格地進行驗證

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 標準化

從2019年9月2日開始，使用 POST 方法的驗證要求將會更嚴格地針對 HTTP 標準進行驗證。 具體而言，不會再從要求表單值中移除空格和雙引號（"）。 這些變更不應該中斷任何現有的用戶端，而且將有助於確保傳送至 Azure AD 的要求會每次可靠地處理。

如需詳細資訊，請參閱[Azure AD 重大變更通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)。

---
