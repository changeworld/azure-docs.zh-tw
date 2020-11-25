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
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71726724e7c018f34b1175f323d0c8e55b604931
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973627"
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
## <a name="october-2020"></a>2020 年 10 月

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure AD 受 Azure TLS 憑證變更影響的內部部署混合式代理程式

**類型：** 方案變更  
**服務類別：** N/A  
**產品功能：** 平台

Microsoft 正在更新 Azure 服務，以使用來自一組不同根憑證授權單位 (CA) 的 TLS 憑證。 這項更新是因為目前的 CA 憑證不符合其中一個 CA/瀏覽器論壇基準需求。 這項變更將會影響在內部部署環境中安裝的 Azure AD 混合式代理程式，其具有具有固定的根憑證清單的強化環境，而且必須更新以信任新的憑證簽發者。

如果您未立即採取行動，這項變更會導致服務中斷。 這些代理套裝程式括可遠端存取內部部署的 [應用程式 Proxy 連接器](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 、 [通過驗證](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 代理程式，可讓您的使用者使用相同的密碼登入應用程式，以及執行 AD 來 Azure AD 同步處理的雲端布建 [預覽](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 代理程式。 

如果您的環境中的防火牆規則設定為只允許對特定憑證撤銷清單進行輸出呼叫 (CRL) 下載，您將需要允許下列 CRL 和 OCSP Url。 如需變更和 CRL 和 OCSP Url 的完整詳細資料以啟用存取，請參閱  [AZURE TLS 憑證變更](../../security/fundamentals/tls-certificate-changes.md)。

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>布建事件將會從審核記錄中移除，並僅發佈至布建記錄

**類型：** 方案變更  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
SCIM 布建 [服務](../app-provisioning/user-provisioning.md) 的活動會記錄在「審核記錄」和「布建」記錄中。 這包括像是在 ServiceNow 中建立使用者、在 GSuite 中分組，或從 AWS 匯入角色的活動。 未來，這些事件只會發佈到布建記錄檔中。 這項變更的執行是為了避免記錄中的重複事件，以及使用 log analytics 中記錄的客戶所產生的額外成本。 

我們將在日期完成時提供更新。 這不是日曆年度2020的計畫。 

> [!NOTE]
> 這不會影響布建服務所發出之同步處理事件以外的任何事件。 建立應用程式、條件式存取原則、目錄中的使用者等事件，將會繼續在 audit 記錄中發出。 [深入了解](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/app-provisioning/context/app-provisioning-context)。
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD 受 Azure 傳輸層安全性影響的內部部署混合式代理程式 (TLS) 憑證變更

**類型：** 方案變更  
**服務類別：** N/A  
**產品功能：** 平台
 
Microsoft 正在更新 Azure 服務，以使用來自一組不同根憑證授權單位 (CA) 的 TLS 憑證。 因為目前的 CA 憑證未遵循其中一個 CA/瀏覽器論壇基準需求，所以會有更新。 這項變更將會影響在內部部署環境中安裝的 Azure AD 混合式代理程式，其已強化的環境具有固定的根憑證清單。 這些代理程式將需要更新，以信任新的憑證簽發者。

如果您未立即採取行動，這項變更會導致服務中斷。 這些代理套裝程式括： 
- 用於遠端存取內部部署的[應用程式 Proxy 連接器](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 
- [傳遞驗證](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 代理程式，可讓您的使用者使用相同的密碼登入應用程式
- 可進行 AD 以 Azure AD 同步的雲端布建[預覽](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)代理程式。 

如果您的環境中的防火牆規則設定為只允許對特定憑證撤銷清單進行輸出呼叫 (CRL) 下載，您必須允許 CRL 和 OCSP Url。 如需變更和 CRL 和 OCSP Url 的完整詳細資料以啟用存取，請參閱  [AZURE TLS 憑證變更](../../security/fundamentals/tls-certificate-changes.md)。
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>US Gov 雲端 Azure Active Directory TLS 1.0、TLS 1.1 和3DES 淘汰

**類型：** 方案變更  
**服務類別：** N/A  
**產品功能：** 標準
 
Azure Active Directory 將在2021年3月31日取代下列通訊協定：
- TLS 1.0
- TLS 1.1
- 3DES 加密套件 (TLS_RSA_WITH_3DES_EDE_CBC_SHA) 

所有的用戶端-伺服器和瀏覽器伺服器組合都應該使用 TLS 1.2 和新式加密套件來維護對 Azure、Office 365 和 Microsoft 365 服務之 Azure Active Directory 的安全連線。

受影響的環境包括：
- Azure US Gov
- [Office 365 GCC High & DoD](https://docs.microsoft.com/microsoft-365/compliance/tls-1-2-in-office-365-gcc?view=o365-worldwide)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>將應用程式指派給 AU 和物件範圍中的角色

**類型：** 新功能  
**服務類別：** Rbac  
**產品功能：** 存取控制
 
這項功能可讓您將應用程式 (SPN) 指派給管理單位範圍上的系統管理員角色。 若要深入瞭解，請參閱 [將限域角色指派給管理單位](../roles/admin-units-assign-roles.md)。

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>現在，當來賓使用者被拒絕存取資源時，您可以停用及刪除來賓使用者

**類型：** 新功能  
**服務類別：** 存取評論  
**產品功能：** 身分識別治理
 
「停用」和「刪除」是 Azure AD 存取權評論的 advanced 控制項，可協助組織更妥善管理群組和應用程式中的外部來賓。 如果來賓在存取權審核中遭到拒絕，則 **停用和刪除** 會自動封鎖登入30天。 30天后，就會將它們全部從租使用者中移除。

如需這項功能的詳細資訊，請參閱 [使用 Azure AD 存取評論來停用及刪除外部身分識別 (Preview) ](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview)。
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>存取權審核建立者可以在電子郵件中將自訂訊息新增至審核者

**類型：** 新功能  
**服務類別：** 存取評論  
**產品功能：** 身分識別治理
 
在 Azure AD 存取權審核中，建立評論的系統管理員現在可以將自訂訊息寫入審核者。 審核者會在收到的電子郵件中看到訊息，提示他們完成評論。 若要深入瞭解如何使用這項功能，請參閱 [Advanced Settings](../governance/create-access-review.md#advanced-settings) 區段的步驟6。

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Azure AD 應用程式資源庫中的新布建連接器-2020 年10月

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 協力廠商整合
 
您現在可以為這些新整合的應用程式，自動建立、更新和刪除使用者帳戶：

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [全域轉送身分識別同步](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

如需如何使用自動化使用者帳戶佈建來加強組織安全性的詳細資訊，請參閱[使用 Azure AD 自動將使用者佈建至 SaaS 應用程式](../app-provisioning/user-provisioning.md)。
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Azure AD B2C 的整合助理

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C
 
整合助理 (預覽) 體驗現已提供 Azure AD B2C 應用程式註冊。 這項體驗有助於引導您針對一般案例設定應用程式。 深入瞭解 [Microsoft 身分識別平臺的最佳作法和建議](../develop/identity-platform-integration-checklist.md)。
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>在 Azure 入口網站 UI 中查看角色範本識別碼

**類型：** 新功能  
**服務類別：** Azure 角色  
**產品功能：** 存取控制
 

您現在可以在 Azure 入口網站中查看每個 Azure AD 角色的範本識別碼。 在 Azure AD 中，選取所選角色的  **描述** 。 

建議客戶在其 PowerShell 腳本和程式碼（而不是顯示名稱）中使用角色範本識別碼。 角色範本識別碼可支援用來 [directoryRoles](https://docs.microsoft.com/graph/api/resources/directoryrole?view=graph-rest-1.0) 和 [roleDefinition](https://docs.microsoft.com/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) 物件。 如需角色範本識別碼的詳細資訊，請參閱 [角色範本識別碼](../roles/permissions-reference.md#role-template-ids)。

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>適用于 Azure AD B2C 註冊使用者流程的 API 連接器現在處於公開預覽階段

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C
 

API 連接器現在可用於 Azure Active Directory B2C。 API 連接器可讓您使用 web Api 來自訂您的註冊使用者流程，並與外部雲端系統整合。 您可以使用 API 連接器來：

- 與自訂核准工作流程整合
- 驗證使用者輸入資料
- 覆寫使用者屬性 
- 執行自訂商務邏輯 

 若要深入瞭解，請造訪 [使用 API 連接器來自訂和擴充註冊](../../active-directory-b2c/api-connectors-overview.md) 檔。

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>權利管理中已連線組織的狀態屬性

**類型：** 新功能  
**服務類別：** 目錄管理 **產品功能：** 權利管理
 

 所有已連線的組織現在都會有一個稱為「狀態」的額外屬性。 狀態將會控制如何在參考「所有設定的已連線組織」的原則中使用連線的組織。 此值將會是「已設定」 (表示組織在使用 "all" 子句) 或「提議」 (的原則範圍中，表示組織不在) 範圍內。  

手動建立的已連線組織會有「已設定」的預設設定。 同時，自動建立的 (透過允許網際網路的任何使用者要求存取權的原則建立，) 會預設為「建議」。  在 9 2020 年9月之前建立的任何已連線組織都將設定為「已設定」。 系統管理員可以視需要更新此屬性。 [深入了解](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically)。
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory 外部身分識別現在具有 B2C 的 premium advanced security 設定

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C
 
以風險為基礎的條件式存取和 Identity Protection 的風險偵測功能現在可在 [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md)中取得。 有了這些先進的安全性功能，客戶現在可以：
- 利用智慧型深入解析，利用 B2C 應用程式和終端使用者帳戶來評估風險。 偵測包括非典型移動、匿名 IP 位址、惡意程式碼連結的 IP 位址，以及 Azure AD 威脅情報。 入口網站和 API 型報告也可供使用。
- 藉由設定 B2C 使用者的自動調整驗證原則，自動解決風險。 應用程式開發人員和系統管理員可以根據所偵測到的使用者風險層級 (MFA) 或封鎖存取，以根據位置、群組和應用程式提供額外的控制項，來減緩即時風險。
- 與 Azure AD B2C 使用者流程和自訂原則整合。 您可以從 Azure AD B2C 中的內建使用者流程觸發條件，也可以將條件併入 B2C 自訂原則中。 如同 B2C 使用者流程的其他方面，可以自訂終端使用者體驗訊息。 自訂是根據組織的語音、品牌和緩和替代方案。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2020 年10月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2020年10月，我們已在具有同盟支援的應用程式庫中新增了下列27個新應用程式：

[Sentry](../saas-apps/sentry-tutorial.md)、[黃蜂-生產力 Superapp](https://app.yellowmessenger.com/user/login)、 [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md)、 [EAComposer](../saas-apps/eacomposer-tutorial.md)、 [Genesys Cloud 整合 for Azure](https://apps.mypurecloud.com/msteams-integration/)、[區域技術入口網站](https://portail.zonetechnologie.com/signin)、 [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md)、 [Datawiza Access Broker](https://console.datawiza.com/)、 [ZOKRI](https://app.zokri.com/)、 [CheckProof](../saas-apps/checkproof-tutorial.md)、Ecochallenge.org [、atSpoke、](http://atspoke.com/login)[約會提醒](https://app.appointmentreminder.co.nz/account/login)、 [Cloud](https://cloud.market/) [、TravelPerk、](../saas-apps/travelperk-tutorial.md)Greetly [、[](https://app.greetly.com/)OrgVitality SSO} ( ... [Ecochallenge.org](https://events.ecochallenge.org/users/login)/saas-apps/orgvitality-sso-tutorial.md) 、 [Web 貨物 Air](../saas-apps/web-cargo-air-tutorial.md)、[迴圈流程 CRM](../saas-apps/loop-flow-crm-tutorial.md)、 [Starmind](../saas-apps/starmind-tutorial.md)、 [Workstem](https://hrm.workstem.com/login)、[零售 Zipline](../saas-apps/retail-zipline-tutorial.md)、 [Hoxhunt](../saas-apps/hoxhunt-tutorial.md)、 [MEVISIO](../saas-apps/mevisio-tutorial.md)、 [Samsara](../saas-apps/samsara-tutorial.md)、 [Nimbus](../saas-apps/nimbus-tutorial.md)、[脈衝安全虛擬流量管理員](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

您也可以在這裡找到所有應用程式的檔 https://aka.ms/AppsTutorial

若要在 Azure AD 應用程式資源庫中列出您的應用程式，請閱讀此處的詳細資料 https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>布建記錄現在可以串流至 log analytics

**類型：** 新功能  
**服務類別：** 報告  
**產品功能：** 監視和報告
 

將您的布建記錄發佈至 log analytics，以便：
- 儲存提供超過30天的布建記錄
- 定義自訂警示和通知
- 建立儀表板以視覺化記錄
- 執行複雜的查詢來分析記錄 

若要瞭解如何使用此功能，請參閱瞭解布建 [如何與 Azure 監視器記錄整合](../app-provisioning/application-provisioning-log-analytics.md)。
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>應用程式擁有者現在可以查看布建記錄

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
您現在可以讓應用程式擁有者透過布建服務監視活動並進行問題的疑難排解，而不需要為他們提供特殊許可權角色或使其成為瓶頸。 [深入了解](../reports-monitoring/concept-provisioning-logs.md)。
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>重新命名 10 Azure Active Directory 角色

**類型：** 已變更的功能  
**服務類別：** Azure 角色  
**產品功能：** 存取控制
 
某些 Azure Active Directory (AD) 內建角色的名稱與 Microsoft 365 系統管理中心、Azure AD 入口網站和 Microsoft Graph 中所顯示的名稱不同。 這種不一致可能會在自動化程式中造成問題。 在此更新中，我們會將10個角色名稱重新命名，使其一致。 下表包含新的角色名稱：

![新角色名稱的資料表](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>使用 MSAL JS 2.x Azure AD B2C Spa 的驗證程式代碼流程支援

**類型：** 已變更的功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C
 
MSAL.js 版本2.x 現在包含單一頁面 web 應用程式的授權碼流程支援 (Spa) 。 Azure AD B2C 現在可支援在 Azure 入口網站上使用 SPA 應用程式類型，以及使用 MSAL.js 授權碼流程搭配 PKCE 來進行單一頁面應用程式。 這可讓使用 Azure AD B2C 的 Spa 維持與較新瀏覽器的 SSO，並遵守較新的驗證通訊協定建議。 Azure Active Directory B2C 教學課程 [中， (SPA) 註冊單一頁面應用程式](../../active-directory-b2c/tutorial-register-spa.md) 入門。

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>在信任的裝置設定上記住 Multi-Factor Authentication (MFA) 的更新

**類型：** 已變更的功能  
**服務類別：** Mfa  
**產品功能：** 身分識別安全性 & 保護
 

我們最近已更新受信任裝置功能的「 [記住 Multi-Factor Authentication (MFA) ](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) ，以延長最多365天的驗證。 Azure Active Directory (Azure AD) Premium 授權，也可以使用 [條件式存取–登入頻率原則](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) ，以提供更大的重新驗證設定彈性。

為了獲得最佳的使用者體驗，建議您使用條件式存取登入頻率，將會話存留期擴充到受信任裝置、位置或低風險會話，以作為 [在信任的裝置上記住 MFA] 設定的替代方案。 若要開始使用，請參閱 [最新的指導方針，瞭解如何將重新驗證的體驗優化](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

---

## <a name="september-2020"></a>2020 年 9 月

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Azure AD 應用程式資源庫中的新布建連接器-2020 年9月

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 協力廠商整合
 
您現在可以為這些新整合的應用程式，自動建立、更新和刪除使用者帳戶：

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot 安全性意識](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

如需如何使用自動化使用者帳戶佈建來加強組織安全性的詳細資訊，請參閱[使用 Azure AD 自動將使用者佈建至 SaaS 應用程式](../app-provisioning/user-provisioning.md)。
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>雲端布建公開預覽更新

**類型：** 新功能  
**服務類別：** Azure AD 雲端布建 **產品功能：** 身分識別生命週期管理
 
Azure AD Connect 雲端布建公開預覽重新整理功能，提供了兩個從客戶意見反應開發的主要增強功能 

- 透過 Azure 入口網站的屬性對應體驗

    透過這項功能，IT 系統管理員可以使用目前存在的各種對應類型，將使用者、群組或連絡人屬性從 AD 對應至 Azure AD。 屬性對應是一項功能，可用來將從 Active Directory 流向 Azure Active Directory 的屬性值標準化。 您可以決定是否直接將屬性值從 AD 對應到 Azure AD，或在布建使用者時使用運算式來轉換屬性值。 [深入了解](../cloud-provisioning/how-to-attribute-mapping.md)

- 隨選布建或測試使用者體驗

    設定好設定之後，您可能會想要先測試使用者轉換是否如預期般運作，再將它套用至範圍內的所有使用者。 使用隨選布建時，IT 系統管理員可以輸入 AD 使用者 (DN) 的辨別名稱，並查看它們是否如預期般同步。 隨選布建提供一種絕佳的方式，可確保您先前執行的屬性對應會如預期般運作。 [深入了解](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Azure AD 中的已審核 BitLocker 修復-公開預覽

**類型：** 新功能  
**服務類別：** 裝置存取管理  
**產品功能：** 裝置生命週期管理
 
當 IT 系統管理員或使用者讀取 () s 的 BitLocker 修復金鑰時，Azure Active Directory 現在會產生審核記錄，以捕獲存取修復金鑰的人員。 相同的審核會提供與 BitLocker 金鑰相關聯之裝置的詳細資料。

終端使用者可以透過 [我的帳戶存取其修復金鑰](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key)。 IT 系統管理員可以透過 [Beta 版中的 BitLocker 修復金鑰 API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) 或透過 Azure AD 入口網站來存取修復金鑰。 若要深入瞭解，請參閱 [在 Azure AD 入口網站中查看或複製 BitLocker 金鑰](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)。

---

### <a name="teams-devices-administrator-built-in-role"></a>團隊裝置系統管理員內建角色

**類型：** 新功能  
**服務類別：** Rbac  
**產品功能：** 存取控制
 
具有 [小組裝置系統管理員](../roles/permissions-reference.md#teams-devices-administrator) 角色的使用者可以從小組系統管理中心管理 [經過團隊認證的裝置](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) 。 

此角色可讓使用者透過搜尋和篩選裝置的能力，一眼就能查看所有裝置。 使用者也可以檢查每個裝置的詳細資料，包括登入帳戶和裝置的製作和型號。 使用者可以變更裝置上的設定，並更新軟體版本。 此角色不會授與檢查小組活動和呼叫裝置品質的許可權。
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>適用于目錄物件的 Advanced query 功能

**類型：** 新功能  
**服務類別：** MS 圖形  
**產品功能：** 開發人員體驗
 
針對 Azure AD Api 中的目錄物件引進的所有新查詢功能，現在已可在 v1.0 端點和生產環境就緒中使用。 開發人員可以使用標準 OData 運算子來計算、搜尋、篩選和排序目錄物件以及相關的連結。

若要深入瞭解，請參閱 [此處](https://aka.ms/BlogPostMezzoGA)的檔，您也可以使用這 [份簡短問卷](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)傳送意見反應。
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>公開預覽：設定條件式存取原則的租使用者的連續存取評估

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 身分識別安全性 & 保護
 
使用條件式存取原則的 Azure AD 租使用者， (CAE) 的持續存取評估現在可供公開預覽。 使用 CAE 時，會即時評估關鍵安全性事件和原則。 這包括帳戶停用、密碼重設和位置變更。 若要深入瞭解，請參閱 [持續存取評估](../conditional-access/concept-continuous-access-evaluation.md)。

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>公開預覽：要求使用者要求存取套件其他問題，以改善核准決策

**類型：** 新功能  
**服務類別：** 使用者存取管理  
**產品功能：** 權利管理
 
系統管理員現在可以要求要求存取套件的使用者回答其他問題，而不只是 Azure AD 權利管理的我的存取權入口網站中的業務理由。 使用者的答案接著會向核准者顯示，以協助他們做出更精確的存取核准決策。 若要深入瞭解，請參閱 [ (預覽) 收集其他要求者資訊以進行核准 ](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)。
 
---

### <a name="public-preview-enhanced-user-management"></a>公開預覽：增強的使用者管理

**類型：** 新功能  
**服務類別：** 使用者管理  
**產品功能：** 使用者管理
 

Azure AD 的入口網站已更新，可讓您更輕鬆地在 [所有使用者] 和 [已刪除的使用者] 頁面中尋找使用者。 預覽版中的變更包括： 
- 更多可見的使用者屬性，包括物件識別碼、目錄同步處理狀態、建立類型和身分識別簽發者。
- 搜尋現在允許合併搜尋名稱、電子郵件和物件識別碼。
- 依使用者類型的增強篩選 (成員、來賓和無) 、目錄同步狀態、建立類型、公司名稱和功能變數名稱。
- 屬性的新排序功能，例如名稱、使用者主體名稱和刪除日期。
- 新的總使用者計數會以任何搜尋或篩選進行更新。

如需詳細資訊，請參閱 [Azure Active Directory 中的使用者管理增強功能 (預覽) ](../enterprise-users/users-search-enhanced.md)。

---

### <a name="new-notes-field-for-enterprise-applications"></a>企業應用程式的新 notes 欄位

**類型：** 新功能  
**服務類別：** 企業應用程式 **產品功能：** SSO

您可以在企業應用程式中新增免費的文字筆記。 您可以新增任何可協助您在企業應用程式下管理應用程式的相關資訊。 如需詳細資訊，請參閱 [快速入門：設定 Azure Active Directory (Azure AD) 租使用者中的應用程式屬性](../manage-apps/add-application-portal-configure.md)。 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2020 年9月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在2020年9月，我們已在具有同盟支援的應用程式庫中新增下列34個新應用程式：

[VMware 範圍-統一存取閘道]()、 [脈衝安全電腦](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md)、 [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md)、 [Frontitude](https://services.enteksystems.de/sso/microsoft/signup)、 [BookWidgets](https://www.bookwidgets.com/sso/office365)、 [ZVD_SERVER](https://zaas.zenmutech.com/user/signin)、 [HashData for Business](https://hashdata.app/login.xhtml)、 [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login)、 [CyberSolutions MAILBASEΣ/Cms](../saas-apps/cybersolutions-mailbase-tutorial.md)、 [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md)、 [LimbleCMMS](https://auth.limblecmms.com/)、 [Glint inc.](../saas-apps/glint-inc-tutorial.md)、 [zeroheight](../saas-apps/zeroheight-tutorial.md)、 [性別適用性](https://app.genderfitness.com/)、 [Coeo 入口網站](https://my.coeo.com/)、 [Grammarly](../saas-apps/grammarly-tutorial.md)、 [Fivetran](../saas-apps/fivetran-tutorial.md)、 [Kumolus](../saas-apps/kumolus-tutorial.md)、 [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md)、 [TeamzSkill](../saas-apps/teamzskill-tutorial.md)、 [raumfürraum](../saas-apps/raumfurraum-tutorial.md)、 [Saviynt](../saas-apps/saviynt-tutorial.md)、 [BIZMERLINHR](https://marketplace.bizmerlin.net/bmone/signup)、行動 [保險箱](../saas-apps/mobile-locker-tutorial.md)、 [Zengine](../saas-apps/zengine-tutorial.md)、 [CloudCADI](https://app.cloudcadi.com/login)、Simfoni [Analytics](https://simfonianalytics.com/accounts/microsoft/login/)、 [Priva Identity & Access Management](https://my.priva.com/)、 [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads)、 [Eventfinity](../saas-apps/eventfinity-tutorial.md)、 [Fexa](../saas-apps/fexa-tutorial.md)、 [安全簽署企業版入口網站](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)、 [安全簽署企業版入口網站 AAD 設定](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)、 [Wistec Online](https://wisteconline.com/auth/oidc)、 [Oracle PeopleSoft-由 F5 BIG IP APM 保護](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

您也可以從這裡找到所有應用程式的檔： https://aka.ms/AppsTutorial 。

若要在 Azure AD 應用程式資源庫中列出您的應用程式，請閱讀此處的詳細資訊： https://aka.ms/AzureADAppRequest 。

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Azure AD 權利管理中的新委派角色：存取套件指派管理員

**類型：** 新功能  
**服務類別：** 使用者存取管理  
**產品功能：** 權利管理
 
Azure AD 權利管理中新增了新的「存取套件指派管理員」角色，以提供更細微的許可權來管理指派。 您現在可以將工作委派給此角色中的使用者，該使用者可以將存取套件的指派管理委派給商務擁有者。 不過，存取套件指派管理員無法改變系統管理員所設定的存取套件原則或其他屬性。 

有了這個新的角色，您就可以從委派管理指派和維護所有其他存取套件設定之系統管理控制所需的最低許可權獲益。 若要深入瞭解，請參閱 [權利管理角色](../governance/entitlement-management-delegate.md#entitlement-management-roles)。
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Privileged Identity Management 上架流程的變更

**類型：** 已變更的功能  
**服務類別：** Privileged Identity Management  
**產品功能：** Privileged Identity Management
 
先前，上線至 Privileged Identity Management (PIM) 必要的使用者同意，以及 PIM 內含在 Azure AD MFA 中註冊的 blade 分頁中的上架流程。 在最近將 PIM 體驗整合到 Azure AD 角色和系統管理員] 分頁中，我們將會移除此體驗。 任何具有有效 P2 授權的租使用者都將自動上線至 PIM。

上架至 PIM 沒有任何對您租使用者的直接負面影響。 您可以預期下列變更：
- 當您在 PIM 或 Azure AD 角色和系統管理員] 分頁中進行指派時，其他指派選項，例如 [作用中] 和 [符合資格的開始和結束時間]。 
- 其他範圍機制（例如系統管理單位和自訂角色）直接引進指派體驗。 
- 如果您是全域管理員或特殊許可權角色管理員，您可以開始取得一些額外的電子郵件，例如 PIM 的每週摘要。 
- 您也可能會在與角色指派相關的 audit 記錄檔中看到 ms pim 服務主體。 預期的變更不會影響您的一般工作流程。

 如需詳細資訊，請參閱 [開始使用 Privileged Identity Management](../privileged-identity-management/pim-getting-started.md)。

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD 權利管理： [存取套件資源] 的 [選取] 窗格現在預設會顯示目前在所選目錄中的資源

**類型：** 已變更的功能  
**服務類別：** 使用者存取管理  
**產品功能：** 權利管理
 

在存取套件建立流程的 [資源角色] 索引標籤下，[選取] 窗格的行為會變更。 目前，預設行為是顯示使用者所擁有的所有資源，以及新增至所選目錄的資源。 

此體驗將會變更為只顯示目前在目錄中新增的資源，讓使用者可以輕鬆地從目錄中選取資源。 此更新將有助於探索要新增至存取套件的資源，並降低不慎新增不屬於目錄的使用者所擁有之資源的風險。 若要深入瞭解，請參閱 [在 Azure AD 權利管理中建立新的存取套件](../governance/entitlement-management-access-package-create.md#resource-roles)。
 
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

若要深入瞭解，請參閱 [限制的來賓存取許可權](../enterprise-users/users-restrict-guest-permissions.md) 和 [使用者的預設許可權](./users-default-permissions.md)。
 
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

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Azure AD My Sign-Ins 正式推出

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 終端使用者體驗
 
Azure AD 我的 Sign-Ins 是一項新功能，可讓企業使用者檢查其登入歷程記錄，以檢查是否有任何不尋常的活動。 此外，這項功能可讓終端使用者在可疑活動上報告「這不是我」或「這是我」。 若要深入瞭解如何使用這項功能，請參閱 [從我的 Sign-Ins 頁面查看和搜尋最近的登入活動](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)。
 
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

您現在可以使用這項新功能，將 Azure AD 內建角色指派給雲端群組。 例如，您可以將 SharePoint 系統管理員角色指派給 Contoso_SharePoint_Admins 群組。 您也可以使用 PIM 讓群組成為角色的合格成員，而不是授與長期存取權。 若要瞭解如何設定這項功能，請參閱 [Azure Active Directory (preview) 中使用雲端群組管理角色指派 ](../roles/groups-concept.md)。
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>現在提供見解商務領導者內建角色

**類型：** 新功能  
**服務類別：** Azure AD 角色  
**產品功能：** 存取控制
 
Insights 商務領導者角色中的使用者可透過 [M365 Insights 應用程式](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)存取一組儀表板和深入解析。 這包括所有儀表板的完整存取權，以及提供的深入解析和資料探索功能。 不過，此角色中的使用者無法存取「產品設定」設定，這是「深入解析管理員」角色的責任。 若要深入瞭解此角色，請參閱 [Azure Active Directory 中的系統管理員角色許可權](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>現在提供見解管理員內建角色

**類型：** 新功能  
**服務類別：** Azure AD 角色  
**產品功能：** 存取控制
 
深入解析系統管理員角色中的使用者可以存取 [M365 Insights 應用程式](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)中的完整管理功能集。 此角色的使用者可以讀取目錄資訊、監視服務健康狀態、檔案支援票證，以及存取深入解析系統管理員設定方面。 若要深入瞭解此角色，請參閱 [Azure Active Directory 中的系統管理員角色許可權](../roles/permissions-reference.md#insights-administrator)
 
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

建立新原則時，請務必排除仍在使用舊版驗證的使用者和服務帳戶。如果不這麼做，將會被封鎖。 [深入了解](../conditional-access/concept-conditional-access-conditions.md)。
 
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
 
Windows Hello 企業版可讓終端使用者使用手勢登入 Windows 電腦 (例如 PIN 或生物特徵辨識) 。 Azure AD 系統管理員可能會想要區分 Windows Hello 企業版從其他 Windows 登入的登入，作為組織無密碼驗證的旅程。 

系統管理員現在可以在 Azure 入口網站的 Azure AD Sign-Ins] 分頁中，檢查 Windows 登入事件的 [驗證詳細資料] 索引標籤，以查看 Windows 驗證是否使用 Windows Hello 企業版。 Windows Hello 企業版驗證會在 [驗證方法] 欄位中包含 "WindowsHelloForBusiness"。 如需有關解讀 Sign-In 記錄的詳細資訊，請參閱 [登入記錄檔](../reports-monitoring/concept-sign-ins.md)。
 
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

[Clap 您的手](http://www.rmit.com.ar/)、 [Appreiz](https://microsoftteams.appreiz.com/)、 [Inextor Vault](https://inexto.com/inexto-suite/inextor)、 [Beekast](https://my.beekast.com/)、 [Templafy OpenID Connect](https://app.templafy.com/)、 [PeterConnects 接待員](https://msteams.peterconnects.com/)、 [AlohaCloud](https://appfusions.alohacloud.com/auth)、 [Control 塔式](https://bpm.tnxcorp.com/sso/microsoft)、 [Cocoom](https://start.cocoom.com/)、[硬幣建築雲端](https://sso.coinsconstructioncloud.com/#login/)、 [Medxnote MT](https://task.teamsmain.medx.im/authorization)、 [Reflekt](https://reflekt.konsolute.com/login) [、Rever](https://app.reverscore.net/access)、 [MyCompanyArchive、GReminders](https://login.mycompanyarchive.com/)、Titanfile [、Wootric](../saas-apps/titanfile-tutorial.md) [、SolarWinds](../saas-apps/wootric-tutorial.md)、 [Orion、OpenText](https://app.greminders.com/o365-oauth)、 [Datasite、BlogIn](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US)、 [IntSights](../saas-apps/intsights-tutorial.md)、 [kpifire、](../saas-apps/textline-tutorial.md)[就、Chatwork](../saas-apps/blogin-tutorial.md) [、CloudSign、](../saas-apps/opentext-directory-services-tutorial.md) [Community Spark](../saas-apps/community-spark-tutorial.md) [、](../saas-apps/datasite-tutorial.md)、 [Chatwork](../saas-apps/chatwork-tutorial.md) [、、](../saas-apps/kpifire-tutorial.md) [、](../saas-apps/cloud-academy-sso-tutorial.md) [、](../saas-apps/cloudsign-tutorial.md)、 [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md)、 [SmartHR](https://smarthr.jp/)、 [NumlyEngage™](../saas-apps/numlyengage-tutorial.md)、[密歇根 Data Hub 單一登入](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md) [、輸出、](../saas-apps/egress-tutorial.md) [SendSafely](../saas-apps/sendsafely-tutorial.md)、 [Eletive](https://app.eletive.com/)、[右手邊的網路安全性 ADI](https://right-hand.ai/)、 [Fyde Enterprise Authentication](https://enterprise.fyde.com/)、 [Verme](../saas-apps/verme-tutorial.md)、 [Lenses.io](../saas-apps/lensesio-tutorial.md)、 [Momenta](../saas-apps/momenta-tutorial.md)、 [Uprise](https://app.uprise.co/sign-in)、 [Q](https://q.moduleq.com/login)、 [CloudCords](../saas-apps/cloudcords-tutorial.md)、 [TellMe Bot](https://tellme365liteweb.azurewebsites.net/)、[啟發](https://app.inspiresoftware.com/)、 [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/)、 [Smartschool (School 管理系統) ](https://smartschoolz.com/login)、Zepto [-智慧型 timekeeping](https://user.zepto-ai.com/signin)、 [Studi.ly](https://studi.ly/)、 [Trackplan](http://www.trackplanfm.com/)、 [Skedda](../saas-apps/skedda-tutorial.md)、 [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md)、 [Coggle](../saas-apps/coggle-tutorial.md)、 [Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/)、 [BrowserStack 單一登入](../saas-apps/browserstack-single-sign-on-tutorial.md)

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
 
您現在可以在 Azure AD 入口網站中的 [角色和系統管理員] 索引標籤上，針對角色的所有範圍，查看角色指派。 您也可以將每個角色的角色指派下載至 CSV 檔案。 如需有關如何查看和新增角色指派的指引，請參閱 [Azure Active Directory 中的 [查看和指派系統管理員角色](../roles/manage-roles-portal.md)]。
 
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
 
某些 SAML 應用程式需要在要求時，于判斷提示主體中傳回 SPNameQualifier。 現在 Azure AD 在要求 NameID 原則中要求 SPNameQualifier 時，會正確地回應。 這也適用于 SP 起始登入，而 IdP 起始的登入將會遵循。  若要深入瞭解 Azure Active Directory 中的 SAML 通訊協定，請參閱 [單一 Sign-On SAML 通訊協定](../develop/single-sign-on-saml-protocol.md)。

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

敏感度標籤很重要，可保護您的商務關鍵資料，並可讓您以符合規範且安全的方式，大規模管理群組。 如需使用敏感度標籤的指導方針，請參閱 [Azure Active Directory (preview) 將敏感度標籤指派給 Microsoft 365 群組 ](../enterprise-users/groups-assign-sensitivity-labels.md)。
 
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
 

目前，在 Azure AD 但未同步至租使用者的網域中的使用者，無法存取小組。 從6月底開始，這項新功能可讓使用者藉由擴充現有的電子郵件驗證註冊功能來達成此目的。 這可讓可登入同盟 IdP 的使用者，但在 Azure 識別碼中還沒有使用者物件的使用者，會自動建立使用者物件並對小組進行驗證。 其使用者物件將會標示為「自助式註冊」。 這是現有功能的延伸模組，可進行電子郵件驗證的自我註冊，也就是受控網域中的使用者可以進行，而且可以使用相同的旗標加以控制。 這項變更將在下列兩個月內完成推出。 請觀賞 [此處](../enterprise-users/directory-self-service-signup.md)的檔更新。
 
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
 
IT 系統管理員可以開始使用新的「混合式系統管理員」角色，作為設定 Azure ADConnect Cloud 布建的最低特殊許可權角色。 使用這個新的角色時，您不再需要使用全域管理員角色來設定和設定雲端布建。 [深入了解](../roles/delegate-by-task.md#connect)。
 
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
 
我們已增強 Workday 回寫布建應用程式，現在支援公司電話號碼和行動電話號碼屬性的回寫。 除了電子郵件和使用者名稱之外，您現在還可以設定 Workday 回寫布建應用程式，將電話號碼值從 Azure AD 傳送到 Workday。 如需有關如何設定電話號碼回寫的詳細資訊，請參閱 [Workday 回寫](../saas-apps/workday-writeback-tutorial.md) 應用程式教學課程。 

---

### <a name="publisher-verification-preview"></a>發行者驗證 (預覽) 

**類型：** 新功能  
**服務類別：** 其他  
**產品功能：** 開發人員體驗
 
發行者驗證 (預覽) 可協助系統管理員和終端使用者了解與 Microsoft 身分識別平台整合的應用程式開發人員是否真實。 如需詳細資訊，請參閱 [發行者驗證 (預覽) ](../develop/publisher-verification-overview.md)。
 
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

如需詳細資訊，請造訪 [Azure AD B2C 的新應用程式註冊體驗](../../active-directory-b2c/app-registrations-training-guide.md)。

---
