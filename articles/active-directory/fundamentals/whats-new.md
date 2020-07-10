---
title: 新功能 版本資訊 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 的新功能，例如最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。
services: active-directory
author: msaburnley
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30c6be98eeca4433798ad8ed2bacb9338331b112
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170440"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？

>藉由複製並貼上此 URL，取得何時要重新流覽此頁面以進行更新的通知：在 `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` 您的 ![ RSS 摘要讀取器圖示摘要 ](./media/whats-new/feed-icon-16x16.png) 讀取器中。

Azure AD 會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能
- 方案變更

此頁面會每月更新，因此請定期瀏覽。 如果想要尋找超過 6 個月的項目，請至 [Azure Active Directory 的新增功能封存](whats-new-archive.md) (英文)。

---

## <a name="june-2020"></a>2020 年 6 月 

### <a name="user-risk-condition-in-conditional-access-policy"></a>條件式存取原則中的使用者風險條件

**類型：** 方案變更  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護
 

Azure AD 條件式存取原則中的使用者風險支援，可讓您建立多個以使用者風險為基礎的原則。 不同的使用者和應用程式可能需要不同的最低使用者風險層級。 根據使用者風險，您可以建立原則來封鎖存取、要求多重要素驗證、安全密碼變更，或重新導向至 Microsoft Cloud App Security 來強制執行會話原則，例如額外的審核。

使用者風險條件需要 Azure AD Premium P2，因為它使用 Azure Identity Protection，這是 P2 供應專案。 如需條件式存取的詳細資訊，請參閱[Azure AD 條件式存取檔](https://docs.microsoft.com/azure/active-directory/conditional-access/)。

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO 現在支援需要在要求時設定 SPNameQualifier 的應用程式

**類型：** 固定  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
某些 SAML 應用程式需要在要求時，在判斷提示主體中傳回 SPNameQualifier。 現在 Azure AD 在要求 NameID 原則中要求 SPNameQualifier 時，會正確回應。 這也適用于 SP 起始的登入，而 IdP 起始的登入將會遵循。  若要深入瞭解 Azure Active Directory 中的 SAML 通訊協定，請參閱[單一登入 saml 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)。

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B 共同作業支援在 Azure Government 租使用者中邀請 MSA 和 Google 使用者

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 

使用 B2B 共同作業功能 Azure Government 租使用者現在可以邀請具有 Microsoft 或 Google 帳戶的使用者。 若要瞭解您的租使用者是否可以使用這些功能，請依照[如何判斷我的 AZURE 美國政府租使用者中是否有 B2B 共同作業？中](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)的指示進行。

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>MS Graph v1 中的使用者物件現在包含 externalUserState 和 externalUserStateChangedDateTime 屬性

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 

ExternalUserState 和 externalUserStateChangedDateTime 屬性可用來尋找尚未接受其邀請的受邀 B2B 來賓，以及組建自動化，例如刪除在數天后尚未接受邀請的使用者。 這些屬性現在可在 MS Graph v1 中取得。 如需使用這些屬性的指引，請參閱[使用者資源類型](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)。
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Azure AD 條件式存取中管理驗證會話現已正式運作

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護
 
驗證會話管理功能可讓您設定使用者必須提供登入認證的頻率，以及是否需要在關閉和重新開啟瀏覽器之後提供認證，以在您的環境中提供更高的安全性和彈性。
 
此外，驗證會話管理僅適用于 Azure AD 聯結、混合式 Azure AD 聯結，以及 Azure AD 註冊的裝置上的第一次要素驗證。 現在，驗證會話管理也適用于 MFA。 如需詳細資訊，請參閱[使用條件式存取來設定驗證會話管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2020 年6月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2020年6月，我們已在應用程式庫中新增下列29個新的應用程式，並提供同盟支援：

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial)， [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial)， [MailGates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial)， [BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial)， [Raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial)，[區段](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial)， [Ai 審計員](https://www.mindbridge.ai/products/ai-auditor/)， [Pobuca Connect](https://app.pobu.ca/)， [Proto.io](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial)，[閘道管理員](https://www.gatekeeperhq.com/)，[中樞 Planner](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial)， [Ansira-合作夥伴進入市場工具箱](https://ansira.com/technology/channel-engagement)，[雲端上的 IBM 數位商務自動化](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial)， [Kisi 實體安全性](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial)， [ViewpointOne](https://team.viewpoint.com/)， [IntelligenceBank](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial)， [pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial)，[零](https://www.teamzero.com/)， [InStation](https://instation.invillia.com/)， [edX for Business SAML 2.0 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial)， [MOOC Office 365](https://mooc.office365-training.com/en/)， [SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial)， [PKIsigning platform](https://platform.pkisigning.nl/)， [SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial)，[欄位 iD](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial)，[課程 SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial)， [Perforce Helix 核心-Helix Authentication 服務](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial)， [MyCompliance Cloud](https://cloud.metacompliance.com/)， [Smallstep SSH](https://smallstep.com/sso-ssh/)  

您也可以從這裡找到所有應用程式的檔 https://aka.ms/AppsTutorial 。 如需在 Azure AD 應用程式庫中列出您的應用程式，請參閱這裡的詳細資料： https://aka.ms/AzureADAppRequest 。

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>適用于外部身分識別的 API 連接器自助式註冊現已開放公開預覽

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
外部身分識別 API 連接器可讓您運用 web Api 來整合與外部雲端系統的自助式註冊。 這表示您現在可以將 web Api 叫用為註冊流程中的特定步驟，以觸發雲端式自訂工作流程。 例如，您可以使用 API 連接器來執行下列動作：

- 與自訂核准工作流程整合。
- 執行身分識別校對
- 驗證使用者輸入資料
- 覆寫使用者屬性
- 執行自訂商務邏輯

如需有關 API 連接器可能發生之所有體驗的詳細資訊，請參閱[使用 api 連接器來自訂和擴充自助式註冊](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview)，或[自訂使用 Web API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)整合的外部身分識別自助式註冊。
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>在幾秒鐘內布建隨選並讓使用者進入您的應用程式

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
Azure AD 布建服務目前以迴圈方式運作。 服務每隔40分鐘會執行一次。 [隨選布建功能](https://aka.ms/provisionondemand)可讓您挑選使用者，並在幾秒鐘內布建它們。 這項功能可讓您快速疑難排解布建問題，而不需要重新開機以強制重新開機布建週期。 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>在 Graph 中使用 Azure AD 權利管理的新許可權

**類型：** 新功能  
**服務類別：** 其他  
**產品功能：** 權利管理
 
新的委派許可權 EntitlementManagement。現在已可在 Microsoft Graph Beta 版中與權利管理 API 搭配使用。 若要深入瞭解可用的 Api，請參閱[使用 Azure AD 權利管理 API](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)。

---

### <a name="identity-protection-apis-available-in-v10"></a>1.0 版中可用的 Identity Protection Api

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護
 
RiskyUsers 和 riskDetections Microsoft Graph Api 現已正式推出。 現在，我們已在 v1.0 端點上提供這些功能，我們邀請您在生產環境中使用它們。 如需詳細資訊，請參閱[Microsoft Graph](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)檔。
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>將原則套用至 Microsoft 365 群組的敏感度標籤現已正式運作

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業
 

您現在可以建立敏感度標籤，並使用標籤設定將原則套用至 Microsoft 365 群組，包括隱私權 (公用或私用) 以及外部使用者存取原則。 您可以建立具有隱私權原則的標籤，使其成為私人，而外部使用者存取原則則不允許新增來賓使用者。 當使用者將此標籤套用至群組時，該群組將會是私用的，而且不允許來賓使用者新增至群組。 

敏感度標籤非常重要，可保護您的業務關鍵資料，並可讓您以符合規範且安全的方式來大規模管理群組。 如需使用敏感度標籤的指引，請參閱[Azure Active Directory (preview) 中的將敏感度標籤指派給 Office 365 群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels)。
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>支援 Azure AD Premium 客戶之 Microsoft Identity Manager 的更新

**類型：** 已變更的功能  
**服務類別：** Microsoft Identity Manager  
**產品功能：** 身分識別生命週期管理
 
Azure 支援現在可供 Microsoft Identity Manager 2016 的 Azure AD 整合元件透過 Microsoft Identity Manager 2016 的延伸支援結束。 如需詳細資訊，請參閱[使用 Microsoft Identity Manager 之 Azure AD Premium 客戶的支援更新](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)。

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>在 SSO 宣告設定中使用群組成員資格條件已增加

**類型：** 已變更的功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
先前，當您根據任何單一應用程式設定中的群組成員資格，有條件地變更宣告時，可以使用的群組數目限制為10。 在 SSO 宣告設定中使用群組成員資格條件，現在已增加至最多50個群組。 如需有關如何設定宣告的詳細資訊，請參閱[企業應用程式 SSO 宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions)設定。 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>在公司商標的登入頁面文字元件上啟用基本格式設定。

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
Azure AD/Microsoft 365 登入體驗上的公司商標功能已更新，可讓客戶新增超連結和簡單的格式設定，包括粗體字型、底線和斜體。 如需使用這項功能的指引，請參閱[將商標新增至您組織的 Azure Active Directory 登入頁面](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)。

---

### <a name="provisioning-performance-improvements"></a>提供效能改進

**類型：** 已變更的功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
布建服務已更新，可減少[增量迴圈](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles)完成的時間。 這表示將使用者和群組布建到其應用程式的速度會比之前更快。 6/10/2020 之後建立的所有新布建作業，都將自動受益于效能改進。 任何設定在6/10/2020 之前布建的應用程式都必須在6/10/2020 之後重新開機一次，才能利用效能改進。 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>宣佈已淘汰 ADAL 和 MS Graph 同位檢查

**類型：** 已被取代  
**服務類別：** N/A  
**產品功能：** 裝置生命週期管理

既然 Microsoft 驗證程式庫 (MSAL) 可供使用，我們將不再將新功能新增至 Azure Active Directory 的驗證程式庫 (ADAL) 並會在2022年6月30日結束安全性修補程式。 如需如何遷移至 MSAL 的詳細資訊，請參閱將[應用程式遷移至 Microsoft 驗證程式庫 (MSAL) ](https://docs.microsoft.com/azure/active-directory/develop/msal-migration)。

此外，我們已完成工作，讓所有 Azure AD Graph 功能都可透過 MS Graph 使用。 因此，Azure AD Graph Api 只會在2022年6月30日收到錯誤修正和安全性修正。 如需詳細資訊，請參閱將[您的應用程式更新為使用 Microsoft 驗證程式庫和 MICROSOFT GRAPH API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
## <a name="may-2020"></a>2020 年 5 月

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>SignIns、riskyUsers 和 riskDetections Api 中的屬性淘汰

**類型：** 方案變更  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護

目前，列舉型別用來代表預覽) 中的 riskDetections API 和 riskyUserHistoryItem (中的 riskType 屬性。 列舉類型也會用於 signIns API 中的 riskEventTypes 屬性。 接下來，我們會將這些屬性工作表示為字串。 

客戶應該轉換至 Beta riskDetections 和 riskyUserHistoryItem API 中的 riskEventType 屬性，並于2020年9月9日在 Beta signIns API 中 riskEventTypes_v2 屬性。 在該日期，我們將淘汰目前的 riskType 和 riskEventTypes 屬性。 如需詳細資訊，請參閱[Microsoft Graph 上的風險事件屬性和身分識別保護 api 的變更](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)。

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Microsoft Graph 上的 signIns v1.0 API 中的 riskEventTypes 屬性已淘汰

**類型：** 方案變更  
**服務類別：** 報告  
**產品功能：** 身分識別安全性 & 保護

列舉類型會在2020年9月 Microsoft Graph 表示風險事件屬性時，會切換為字串類型。 除了影響預覽 Api 之外，這項變更也會影響生產環境內的 signIns API。

我們為 signIns v1.0 API 引進了新的 riskEventsTypes_v2 (字串) 屬性。 我們會根據我們的 Microsoft Graph 取代原則，在2022年6月11日淘汰目前的 riskEventTypes (列舉) 屬性。 客戶應于2022年6月11日轉換至 v1.0 signIns API 中的 riskEventTypes_v2 屬性。 如需詳細資訊，請參閱[Microsoft Graph 上的 signIns V1.0 API 中的 riskEventTypes 屬性](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)淘汰。

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>MFA 電子郵件通知即將進行的變更

**類型：** 方案變更  
**服務類別：** MFA  
**產品功能：** 身分識別安全性 & 保護
 

我們正在對 MFA 的 MFA 電子郵件通知進行下列變更：

電子郵件通知將會從下列位址傳送： azure-noreply@microsoft.com 和 msonlineservicesteam@microsoftonline.com 。 我們正在更新詐騙警示電子郵件的內容，以進一步指出解除封鎖使用的必要步驟。

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>新的自助式註冊可讓同盟網域中的使用者無法存取 Microsoft 團隊，因為他們未同步處理到 Azure Active Directory。

**類型：** 方案變更  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 

目前，位在 Azure AD，但未同步至租使用者的網域中的使用者，無法存取小組。 從6月底開始，這項新功能會藉由擴充現有的電子郵件驗證註冊功能，讓他們這麼做。 這可讓能夠登入同盟 IdP 的使用者，但還沒有 Azure 識別碼中的使用者物件，以自動建立使用者物件並向小組驗證。 其使用者物件將會標示為「自助式註冊」。 這是現有功能的延伸，可執行以電子郵件驗證的自我註冊，讓受控網域中的使用者可以執行，而且可以使用相同的旗標來控制。 這項變更會在下列兩個月內完成推出。 請[在這裡](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)觀看檔更新。
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>即將進行的修正：正在更新 Azure Government 雲端的 OIDC 探索檔，以參考正確的圖形端點。

**類型：** 方案變更  
**服務類別：** 主權雲端  
**產品功能：** 使用者驗證
 
從6月開始，OIDC 探索檔[Azure Government 雲端](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)端點上的[Microsoft 身分識別平臺和 OpenID connect 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) (login.microsoftonline.us) ，將會開始根據提供的租使用者，傳回正確的[國家/地區雲端圖形](https://docs.microsoft.com/graph/deployments)端點 (https://graph.microsoft.us 或 https://dod-graph.microsoft.us) 。  它目前提供了不正確的圖形端點 (graph.microsoft.com) "msgraph_host" 欄位。  

這個 bug 修正將會在大約2個月後逐漸推出。  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government 的使用者將無法再登入 login.microsoftonline.com

**類型：** 規劃變更  
**服務類別：** 主權雲端  
**產品功能：** 使用者驗證
 
自2018年6月1日起，Azure Government 的官方 Azure Active Directory (AAD) 授權單位已從變更 https://login-us.microsoftonline.com 為 https://login.microsoftonline.us 。 如果您擁有 Azure Government 租使用者內的應用程式，您必須更新應用程式，以便在美國的端點登入。

自5月5日起，Azure AD 將會開始強制執行端點變更，並封鎖 Azure Government 使用者使用公用端點 (microsoftonline.com) 登入裝載于 Azure Government 租使用者中的應用程式。 受影響的應用程式會開始看到錯誤 AADSTS900439-USGClientNotSupportedOnPublicEndpoint。 

這項變更將會以漸進方式導入，在2020年6月的所有應用程式中必須完成強制執行。 如需詳細資訊，請參閱[Azure Government 的 blog 文章](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)。

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML 單一登出要求現在會以正確的格式傳送 NameID

**類型：** 固定  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
當使用者按一下登出 (例如，在 MyApps 入口網站) 中，Azure AD 會將 SAML 單一登出訊息傳送至使用者會話中作用中的每個應用程式，並已設定登出 URL。 這些訊息包含持續性格式的 NameID。

如果原始 SAML 登入權杖使用不同的格式來進行 NameID (例如電子郵件/UPN) ，則 SAML 應用程式無法將登出訊息中的 NameID 與現有的會話相互關聯 (因為這兩個訊息中使用的 NameIDs 是不同的) ，這會導致 SAML 應用程式捨棄登出訊息，而讓使用者保持登入。 此修正會讓登出訊息與針對應用程式所設定的 NameID 一致。

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>混合式身分識別管理員角色現已提供雲端布建

**類型：** 新功能  
**服務類別：** Azure AD 雲端布建  
**產品功能：** 身分識別生命週期管理
 
IT 系統管理員可以開始使用新的「混合式系統管理員」角色，做為設定 Azure ADConnect 雲端布建的最低特殊許可權角色。 有了這個新角色，您就不再需要使用全域管理員角色來設定和設定雲端布建。 [深入了解](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD 應用程式庫中可用的新同盟應用程式-5 月2020

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在5月2020日，我們已在具有同盟支援的應用程式庫中新增下列36個新的應用程式：

[Moula](https://moula.com.au/pay/merchants)、 [Surveypal](https://www.surveypal.com/app)、 [Kbot365](https://www.konverso.ai/virtual-assistant-for-digital-workplace/)、 [TackleBox](http://www.tacklebox.app/)、 [Powell 小組](https://powell-software.com/en/powell-teams-en/)、 [Talentsoft Assistant](https://msteams.talent-soft.com/)、 [ASC 記錄深入](https://teams.asc-recording.app/product)解析、 [GO1](https://www.go1.com/)、 [B-參與](https://b-engaged.se/)、 [Competella 連絡人中心工作組](http://www.competella.com/)、 [Asite](http://www.asite.com/)， [ImageSoft 身分識別](https://identity.imagesoftinc.com/)，我的[IBISWorld](https://identity.imagesoftinc.com/)， [insuite](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial)，[變更流程管理](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial)， [Cyara CX 保證平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial)，[智慧型全域](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial)控管， [Prezi](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial)， [Mapbox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial)， [Datava Enterprise 服務平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial)，[比較古怪](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial)， [Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial)，EasySSO [for Confluence](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial)，EasySSO [for BitBucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial)，EasySSO [for Bamboo](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial) [，Torii，Axiad](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial) [Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial)， [Humanage](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial) [，ColorTokens](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial)， [ZTNA CCH](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial) [，Tagetik](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial)， [ShareVault](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial) [，Vyond，](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial)[任何人在家 CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial)， [TextExpander](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial)， [ice 連絡人中心](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial)

您也可以從這裡找到所有應用程式的檔 https://aka.ms/AppsTutorial 。

如需在 Azure AD 應用程式庫中列出您的應用程式，請參閱這裡的詳細資料 https://aka.ms/AzureADAppRequest 。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>條件式存取的僅限報告模式現已正式運作

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護

[Azure AD 條件式存取的報表專用模式](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only)可讓您評估原則的結果，而不需強制執行存取控制。 您可以在組織中測試僅限報告的原則，並瞭解其影響，再加以啟用，讓部署更安全且更容易。 過去幾個月，我們已經看過強式採用僅限報告模式，而透過26M 的使用者已經在僅限報告原則的範圍內。 在今天的公告中，預設會以僅限報表模式建立新的 Azure AD 條件式存取原則。 這表示您可以從建立原則的當時開始監視其影響。 對於使用 MS Graph Api 的使用者，您也可以用程式設計的[方式來管理僅限報告的原則](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)。 

---

### <a name="self-service-sign-up-for-guest-users"></a>來賓使用者的自助式註冊

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
透過 Azure AD 中的外部身分識別，您可以讓組織外部的人員存取您的應用程式和資源，同時讓他們使用自己偏好的任何身分識別進行登入。 與外部使用者共用應用程式時，您不一定都能事先知道哪些人需要存取應用程式。 透過[自助式註冊](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview)，您可以讓來賓使用者註冊並取得企業營運 (LOB) 應用程式的來賓帳戶。 您可以建立及自訂註冊流程，以支援 Azure AD 和社交身分識別。 您也可以在註冊期間收集使用者的其他相關資訊。

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>條件式存取深入解析和報表活頁簿已正式推出

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護

[[深入](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting)解析] 和 [報表] 活頁簿提供系統管理員在其租使用者中 Azure AD 條件式存取的摘要觀點。 有了選取個別原則的功能，系統管理員就能進一步瞭解每個原則的作用，並即時監視任何變更。 活頁簿會串流儲存在 Azure 監視器中的資料，您可以在幾分鐘後依照[這些指示](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)進行設定。 為了讓儀表板更容易搜尋，我們已將它移到 [Azure AD 條件式存取] 功能表中的 [新的深入解析和報告] 索引標籤。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>條件式存取的原則詳細資料分頁處於公開預覽狀態

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性 & 保護

新的 [[原則詳細資料](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access)] 分頁會顯示條件式存取原則評估期間所滿足的指派、條件和控制項。 您可以藉由在登入詳細資料的 [條件式存取] 或 [僅限報表] 索引標籤中選取資料列來存取此分頁。

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Microsoft Graph 中目錄物件的新查詢功能處於公開預覽狀態

**類型：** 新功能  
**服務類別：** MS Graph**產品功能：** 開發人員經驗

引進了新功能，可 Microsoft Graph Directory 物件 Api、啟用計數、搜尋、篩選和排序作業。 這可讓開發人員快速查詢目錄物件，而不需要因應措施，例如記憶體內部篩選和排序。 深入瞭解此[blog 文章](https://aka.ms/CountFilterMSGraphAAD)。

我們目前處於公開預覽狀態，尋找意見反應。 請使用這[份簡短問卷](https://aka.ms/MsGraphAADSurveyDocs)來傳送您的意見。

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>使用 Microsoft Graph API (搶鮮版（Beta）來設定 SAML 型單一登入) 

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
現在已提供在 Beta 版中使用 MS Graph Api 從 Azure AD 資源庫建立和設定應用程式的支援。 如果您需要為應用程式的多個實例設定 SAML 型單一登入，請使用 Microsoft Graph Api 來將[saml 型單一登入的設定自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api)，以節省時間。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD 應用程式資源庫中的新布建連接器-5 月2020

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 協力廠商整合
 
您現在可以為這些新整合的應用程式，自動建立、更新和刪除使用者帳戶：

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [依組織列出 New Relic](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

如需如何使用自動化使用者帳戶佈建來加強組織安全性的詳細資訊，請參閱[使用 Azure AD 自動將使用者佈建至 SaaS 應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML 權杖加密已正式推出

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
[SAML 權杖加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)可讓應用程式設定為接收加密的 SAML 判斷提示。 這項功能現在已在所有雲端中正式推出。
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>應用程式權杖中的組名宣告已正式推出

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO
 
在權杖中發出的群組宣告現在可以僅限於指派給應用程式的群組。  當使用者是大量群組的成員，而且有超過權杖大小限制的風險時，這一點特別重要。 有了這項新功能之後，將[組名新增至權杖](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)的功能就已正式推出。
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday 回寫現在支援設定公司電話號碼屬性

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
我們已增強 Workday 回寫布建應用程式，現在支援公司電話號碼和行動編號屬性的回寫。 除了電子郵件和使用者名稱之外，您現在還可以設定 Workday 回寫布建應用程式，將電話號碼值從 Azure AD 到 Workday。 如需有關如何設定電話號碼回寫的詳細資訊，請參閱[Workday 回寫](https://aka.ms/WorkdayWriteback)應用程式教學課程。 

---

### <a name="publisher-verification-preview"></a>發行者驗證 (預覽) 

**類型：** 新功能  
**服務類別：** 其他  
**產品功能：** 開發人員體驗
 
發行者驗證 (預覽) 可協助系統管理員和使用者瞭解與 Microsoft 身分識別平臺整合之應用程式開發人員的真實性。 如需詳細資訊，請參閱[發行者驗證 (預覽) ](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview)。
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>單一頁面應用程式的授權碼流程

**類型：** 已變更的功能**服務類別：** 驗證**產品功能：** 開發人員體驗

因為現代化的瀏覽器[協力廠商 cookie 限制（例如 SAFARI ITP](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas)），spa 將必須使用授權碼流程，而不是隱含流程來維護 SSO;MSAL.js 2. x 現在會支援授權碼流程。 Azure 入口網站的對應更新，讓您可以將 SPA 更新為「spa」類型，並使用驗證碼流程。 如需指導方針，請參閱[快速入門：使用驗證碼流程登入使用者並取得 JAVASCRIPT SPA 中的存取權杖](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code)。

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>改善裝置的篩選功能現供公開預覽

**類型：** 已變更功能   
**服務類別：** 裝置管理**產品功能：** 裝置生命週期管理
 
之前，您可以使用的篩選準則只有「已啟用」和「活動日期」。 現在，您可以[在更多屬性上篩選您的裝置清單](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview)，包括 OS 類型、聯結類型、合規性等。 這些新增功能應該簡化尋找特定裝置的工作。

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>適用于 Azure AD B2C 的新應用程式註冊體驗現已正式運作

**類型：** 已變更功能   
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** 身分識別生命週期管理
 
Azure AD B2C 的新應用程式註冊體驗現已正式推出。 

以往，您必須使用舊版的「應用程式」體驗，與您的應用程式的其餘部分分開管理 B2C 取用者面向應用程式。 這表示不同的應用程式建立體驗會跨 Azure 中的不同位置。

新體驗會顯示所有 B2C 應用程式註冊，並在一個位置 Azure AD 應用程式註冊，並提供一致的方式來進行管理。 無論您是否需要管理客戶面向的應用程式，或能夠存取 Microsoft Graph 以程式設計方式管理 Azure AD B2C 資源的應用程式，您只需要學習一種方法來執行工作。

您可以流覽 Azure AD B2C 服務並選取 [應用程式註冊] 分頁，以達到新的體驗。 也可以從 Azure Active Directory 服務存取體驗。

Azure AD B2C 應用程式註冊體驗是以 Azure AD 租使用者的一般[應用程式註冊體驗](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)為基礎，但已針對 Azure AD B2C 量身打造。 舊版「應用程式」體驗將于未來淘汰。

如需詳細資訊，請造訪[Azure AD B2C 的新應用程式註冊體驗](https://aka.ms/b2cappregtraining)。

---

## <a name="april-2020"></a>2020 年 4 月

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>結合的安全性資訊註冊體驗現已正式推出

**類型：** 新功能

**服務類別：** 驗證 (登入)

**產品功能：** 身分識別安全性 & 保護

多重要素驗證的結合註冊體驗 (MFA) 和 (SSPR) 的自助式密碼重設，現已正式推出。 這個新的註冊體驗可讓使用者在單一逐步程式中註冊 MFA 和 SSPR。 當您為組織部署新的體驗時，使用者可以更少的時間註冊，並減少麻煩。 請查看[這裡](https://bit.ly/3etiRyQ)的 blog 文章。

---

### <a name="continuous-access-evaluation"></a>連續存取評估

**類型：** 新功能

**服務類別：** 驗證 (登入)

**產品功能：** 身分識別安全性 & 保護

持續存取評估是一項新的安全性功能，可在 Azure AD (（例如使用者帳戶刪除) ）中發生事件時，針對取用 Azure AD 存取權杖的信賴憑證者，以近乎即時的方式強制執行原則。 我們會先為小組和 Outlook 用戶端推出這項功能。 如需詳細資訊，請參閱我們的[blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)和[檔](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)。

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

[SincroPool Apps](https://www.sincropool.com/)、 [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/)、 [Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial)、 [LMS365](https://lms.365.systems/)、 [IWT 採購套件](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial)、 [Lunni](https://lunni.fi/)， [EasySSO For Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial)，[虛擬訓練](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft)學術， [Meraki 儀表板](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial)， [Office 365 移動器](https://app.mover.io/login)，[說話者](https://speakerengage.com/login.php)，[誠實](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial)， [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial)， [DutyFlow](https://app.dutyflow.nl/)， [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial)，gr8[人員](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial)， [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial)， [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial)，[顏色](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial)對應， [Timetabling 解決方案](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial)， [SynchroNet 按一下](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial)，加強， [Fortes 變更雲端](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial)，Litmus， [GroupTalk](https://recorder.grouptalk.com/)， [Frontify，](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial) [MongoDB 雲端](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial) [，TickitLMS](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial)[學習](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial) [，COCO](https://www.made-in-office.com/en/) [，Nitro](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/)[生產力套件](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial)， [Trend 微 Web 安全性 (TMWS) ](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

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

這些 Api 是用來管理使用者驗證方法的重要工具。 現在您可以透過程式設計方式，預先註冊和管理用於 MFA 和自助式密碼重設的驗證器， (SSPR) 。 這是 Azure MFA、SSPR 和 Microsoft Graph 空間中最常要求的功能之一。 我們在這一波推出的新 Api，讓您能夠：

- 讀取、新增、更新及移除使用者的驗證電話
- 重設使用者的密碼
- 開啟和關閉 SMS 登入

如需詳細資訊，請參閱[Azure AD 驗證方法 API 總覽](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)。

---

### <a name="administrative-units-public-preview"></a>管理單位公開預覽

**類型：** 新功能

**服務類別：** RBAC

**產品功能：** 存取控制

管理單位可讓您授與系統管理權限，這些權限受限於您所定義組織的部門、區域或其他區段。 您可以使用管理單位將權限委派給區域管理員或以細微的層級設定原則。 例如，使用者帳戶管理員可以更新設定檔資訊、重設密碼，且只針對其管理單位中的使用者指派授權。

使用管理單位，中央系統管理員可以：

- 建立系統管理單位以進行分散的資源管理
- 僅針對管理單位中的 Azure AD 使用者，指派具有系統管理許可權的角色
- 視需要將使用者和群組填入管理單位

如需詳細資訊，請參閱[Azure Active Directory (preview) 中的系統管理單位管理](https://aka.ms/AdminUnitsDocs)。

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>印表機管理員和印表機技術人員內建角色

**類型：** 新功能

**服務類別：** RBAC

**產品功能：** 存取控制

**印表機管理員**：具有此角色的使用者可以註冊印表機，以及管理 Microsoft 通用列印解決方案中所有印表機設定的所有層面，包括通用列印連接器設定。 他們可以同意所有委派的列印權限要求。 印表機管理員也具有列印報告的權限。 

**印表機技術人員**：具有此角色的使用者可以在 Microsoft 通用列印解決方案中註冊印表機和管理印表機狀態。 他們也可以讀取所有連接器資訊。 印表機技術人員無法執行的主要工作，是在印表機和共用印表機上設定使用者權限。 [深入了解。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>混合式身分識別管理內建角色

**類型：** 新功能

**服務類別：** RBAC

**產品功能：** 存取控制

具備此角色的使用者可以啟用、設定及管理與在 Azure AD 中啟用混合式身分識別相關的服務和設定。 此角色可讓您將 Azure AD 設定為三種支援的驗證方法之一&#8212;密碼雜湊同步處理 (PHS) 、傳遞驗證 (PTA) 或同盟 (AD FS 或協力廠商同盟提供者) # B1，以及部署相關的內部部署基礎結構來啟用它們。 內部部署基礎結構包含布建和 PTA 代理程式。 此角色能夠啟用無縫單一登入 (SSO)，以在非 Windows 10 裝置或非 Windows Server 2016 電腦上啟用無縫驗證。 此外，此角色會授與查看登入記錄的功能，並存取健康情況和分析以進行監視和疑難排解。 [深入了解。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>網路系統管理員內建角色

**類型：** 新功能

**服務類別：** RBAC

**產品功能：** 存取控制

具有此角色的使用者可以根據其使用者位置的網路遙測，審查 Microsoft 的網路周邊架構建議。 Office 365 的網路效能依賴謹慎的企業客戶網路周邊架構，這通常是使用者位置特定的架構。 此角色能夠編輯探索到的使用者位置及設定這些位置的網路參數，促使改善遙測測量和設計建議。 [深入了解。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

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
- [新增 (匯入) 成員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)或[移除成員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)，或[下載群組成員清單](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)

---

### <a name="my-staff-delegated-user-management"></a>我的員工委派的使用者管理

**類型：** 新功能

**服務類別：** 使用者管理

**產品功能：**

我的員工可以讓第一線管理員（例如商店經理）確保他們的員工成員能夠存取他們的 Azure AD 帳戶。 組織可以將一般工作（例如重設密碼或變更電話號碼）委派給第一線管理員，而不是依賴中央技術服務人員。 在我的員工中，無法存取其帳戶的使用者只要按幾下，就可以重新取得存取權，而不需要技術服務人員或 IT 人員。 如需詳細資訊，請參閱使用[我的員工管理您的使用者 (預覽) ](https://aka.ms/MyStaffAdminDocs)和[使用我的員工 (預覽) 來委派使用者管理](https://aka.ms/MyStaffUserDocs)。

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

根據客戶的意見反應，我們現在已更新企業應用程式庫中的 Workday 輸入使用者布建和回寫應用程式，以支援最新版本的 Workday Web 服務 (WWS) API。 透過這項變更，客戶可以指定要在連接字串中使用的 WWS API 版本。 這讓客戶能夠抓取 Workday 版本中可用的更多 HR 屬性。 Workday 回寫應用程式現在會使用建議的 Change_Work_Contact_Info Workday web 服務來克服 Maintain_Contact_Info 的限制。

如果在連接字串中未指定任何版本，則根據預設，Workday 輸入布建應用程式會繼續使用 WWS v 21.1 切換至最新的 Workday Api 以進行輸入使用者布建，客戶必須更新[教學課程中](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)所述的連接字串，並更新 workday 屬性[參考指南](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)中所述的 workday 屬性所使用的 xpath。 

若要使用新的 API 來進行回寫，Workday 回寫布建應用程式中不需要進行任何變更。 在 Workday 端，請確定 Workday 整合系統使用者 (ISU) 帳戶有權叫用 Change_Work_Contact 商務程式，如教學課程區段[設定商務程式安全性原則許可權](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)中所述。 

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

Azure Active Directory (Azure AD) 現在提供驗證動態群組規則的方法。 在 [**驗證規則**] 索引標籤上，您可以針對範例群組成員驗證您的動態規則，以確認規則如預期般運作。 當建立或更新動態群組規則時，系統管理員想要知道使用者或裝置是否將成為該群組的成員。 這有助於評估使用者或裝置是否符合規則條件，並在不預期成員資格時協助進行疑難排解。

如需詳細資訊，請參閱[驗證動態群組成員資格規則 (預覽) ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation)。

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

這些新的改進動作需要註冊您的使用者或系統管理員進行多重要素驗證 (MFA) 在您的目錄中，並建立符合您組織需求的正確原則集。 主要的目標是要有彈性，同時確保所有使用者和系統管理員都可以使用多個因素或以風險為基礎的身分識別驗證提示進行驗證。 這可以採用套用範圍決定的多種原則，或將安全性預設值設定 (從3月16日起) ，讓 Microsoft 決定何時要挑戰使用者進行 MFA。 深入瞭解[Microsoft 安全分數的新功能](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021年3月 B2B 更新中的非受控 Azure Active Directory 帳戶

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
自**2021 年3月31日起**，Microsoft 將不再支援兌換邀請，其方式是建立適用于 B2B 共同作業案例的非受控 Azure Active Directory (Azure AD) 帳戶和租使用者。 為此，我們鼓勵您選擇以[電子郵件單次密碼驗證](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)。

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>具有預設存取角色的使用者將會在布建範圍內

**類型：** 方案變更  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
在過去，具有預設存取角色的使用者已超出布建的範圍。 我們聽說了客戶希望此角色的使用者在布建範圍內的意見反應。 我們正努力部署變更，讓所有新的布建設定都能提供預設存取角色的使用者。 我們會逐漸變更現有布建設定的行為，以支援以這個角色布建使用者。 不需要任何客戶動作。 一旦這項變更已就緒，我們就會在[檔](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)中張貼更新。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD 的 B2B 共同作業將在由世紀 Microsoft Azure 營運 (Azure 中國世紀) 租使用者中提供

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure AD 的 B2B 共同作業功能將會在由世紀 (Azure 中國世紀) 租使用者的 Microsoft Azure 營運，讓 Azure 中國世紀內的使用者能夠與其他 Azure 中國世紀的使用者緊密地共同作業。 [深入瞭解 AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)共同作業。

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
 
Azure 監視器與 Azure AD 記錄檔整合現已在 Azure Government 中提供。 您可以 (Audit 和登入記錄) 路由傳送 Azure AD 記錄到儲存體帳戶、事件中樞和 Log Analytics。 請查看[詳細檔](https://aka.ms/aadlogsinamd)，以及[報告和監視](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting)Azure AD 案例的部署計畫。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure Government 中的身分識別保護重新整理

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性 & 保護

我們很興奮地分享，我們現在已推出[Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)   [Microsoft Azure Government 入口網站](https://portal.azure.us/)中重新整理的 Azure AD Identity Protection 體驗。 如需詳細資訊，請參閱我們的[公告 blog 文章](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>嚴重損壞修復：下載並儲存您的布建設定

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理
 
Azure AD 布建服務提供一組豐富的設定功能。 客戶必須能夠儲存其設定，以供日後參考，或回復為已知的正確版本。 我們新增了將您的布建設定下載為 JSON 檔案的功能，並在您需要時將其上傳。 [深入了解](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (的自助式密碼重設) 現在需要兩個在世紀 (Azure 中國世紀地區營運的 Microsoft Azure 管理閘道)  

**類型：** 已變更的功能  
**服務類別：** 自助式密碼重設  
**產品功能：** 身分識別安全性 & 保護
 
先前在 Microsoft Azure 由世紀 (Azure 中國世紀) ，使用自助式密碼重設的系統管理員 (SSPR) 若要重設自己的密碼，只需要一個「閘道」 (挑戰) 來證明其身分識別。 在公用和其他國家雲端中，系統管理員通常必須在使用 SSPR 時，使用兩個閘道來證明其身分識別。 但因為我們不支援在 Azure 中國的世紀內進行 SMS 或電話通話，所以我們允許系統管理員透過單一閘道密碼重設。

我們將在 Azure 中國的世紀和公用雲端之間建立 SSPR 功能同位檢查。 從現在開始，系統管理員必須在使用 SSPR 時使用兩個閘道。 系統會支援 SMS、通話和驗證器代理程式更新和代碼。 [深入了解](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>密碼長度限制為256個字元

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
為確保 Azure AD 服務的可靠性，使用者密碼現在長度限制為256個字元。 若使用者的密碼長度超過此值，系統會要求他們在後續登入時變更其密碼，方法是聯絡其系統管理員或使用自助式密碼重設功能。

此變更已于2020年3月13日（10AM PST (18:00 UTC) ）啟用，錯誤為 AADSTS 50052，InvalidPasswordExceedsMaxLength。 如需詳細資訊，請參閱[重大變更通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>所有免費租使用者現在都可以透過 Azure 入口網站使用 Azure AD 登入記錄

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
從現在開始，具有免費租使用者的客戶可以[從 Azure 入口網站存取 Azure AD 登入記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)長達7天。 先前，登入記錄僅適用于具有 Azure Active Directory Premium 授權的客戶。 有了這項變更，所有租使用者都可以透過入口網站存取這些記錄。

> [!NOTE]
> 客戶仍需要 (Azure Active Directory Premium P1 或 P2) 的 premium 授權，才能透過 Microsoft Graph API 和 Azure 監視器存取登入記錄。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>從 Azure 入口網站上的群組一般設定淘汰全目錄群組選項

**類型：** 已被取代  
**服務類別：** 群組管理  
**產品功能：** 共同作業

為了提供更有彈性的方式，讓客戶建立最符合其需求的全目錄群組，我們已從 Azure 入口網站中的 [**群組**一般] 設定取代 [**全目錄群組**] 選項，  >  **General**並包含[動態群組檔](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)的連結。 我們已改進檔以包含更多指示，讓系統管理員可以建立包含或排除來賓使用者的所有使用者群組。

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
 
為了讓企業在套用適用于其業務的原則時，必須確保最安全性，Microsoft 安全分數會移除以多重要素驗證為中心的三個改進動作， (MFA) 和新增兩個。

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
 
我們聽到了意見反應，Azure AD Domain Services 客戶希望在為其實例選取效能層級時有更大的彈性。 從2020年2月1日開始，我們從動態模型切換 (，其中 Azure AD 會根據物件計數) 至自我選取模型來判斷效能和定價層。 客戶現在可以選擇符合其環境的效能層級。 這種變更也可讓我們啟用新的案例，例如資源樹系，以及每日備份等高階功能。 所有 Sku 的物件計數現在都不受限，但我們會繼續為每一層提供物件計數建議。

**不需要立即的客戶動作。** 針對現有的客戶，在2020年2月1日使用的動態層會決定新的預設層。 這項變更的結果並不會影響定價或效能。 接下來，Azure AD DS 客戶必須評估其目錄大小和工作負載特性變更時的效能需求。 在服務層之間切換會繼續成為不停機的作業，而且我們不會再根據其目錄的成長，自動將客戶移至新的層次。 此外，也不會增加價格，而新的定價將會與目前的計費模型一致。 如需詳細資訊，請參閱[AZURE AD DS sku 檔](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus)和[Azure AD Domain Services 定價頁面](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD App 資源庫中提供的新同盟應用程式-2020 年2月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2020年2月，我們已將下列31個具有同盟支援的新應用程式新增至應用程式庫： 

[IamIP 專利平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial)，[體驗雲端](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)， [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)， [Barracuda 電子郵件安全性服務](https://ess.barracudanetworks.com/sso/azure)， [ABa 報告](https://myaba.co.uk/client-access/signin/auth/msad)，適用[于危機線上入口網站](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial)， [BIC 雲端設計](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial)， [Beekeeper Azure AD 資料連線器](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial)、 [Korn Ferry 評](https://www.kornferry.com/solutions/kf-digital/kf-assess)量、 [Verkada 命令](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial)、 [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial)、 [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial)、 [EAB 導覽](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial)、[新 new relic (有限的發行) ](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial)、 [Thulium](https://admin.thulium.com/login/instance)、[票證管理員](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial)、[小組的範本選擇](https://links.officeatwork.com/templatechooser-download-teams)器、 [Beesy](https://www.beesy.me/index.php/site/login)、[健全狀況支援系統](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial)、 [MURAL](https://app.mural.co/signup)、 [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial)、 [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)、 [Wakelet](https://wakelet.com/login)、 [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial)、[適用于教師和學校](https://www.thinglink.com/)、 [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial)、 [ThingLink](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product)、 [NearpodApp](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial)、 [WEDO](https://invitepeople.com/login)、 [InvitePeople 服務台-文章 Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial)、 [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD 應用程式庫中的新布建連接器-2020 年2月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
您現在可以為這些新整合的應用程式，自動建立、更新和刪除使用者帳戶：

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

如需如何使用自動化使用者帳戶佈建來加強組織安全性的詳細資訊，請參閱[使用 Azure AD 自動將使用者佈建至 SaaS 應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

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
 
新的我的帳戶終端使用者體驗將會 `https://myaccount.microsoft.com` 在下個月更新其 URL。 在[我的帳戶入口網站](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)說明中，尋找體驗的詳細資訊，以及它提供給終端使用者的所有帳戶自助功能。

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
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>使用自訂原則 Azure AD B2C 手機註冊和登入 (公開預覽) 

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C
 
有了電話號碼註冊和登入，開發人員和企業可以讓他們的客戶使用透過 SMS 傳送到使用者電話號碼的一次性密碼來註冊和登入。 這項功能也可讓客戶在無法存取其電話時變更其電話號碼。 藉由自訂原則的威力，電話註冊和登入可讓開發人員和企業透過頁面自訂來傳達其品牌。 瞭解如何[使用 Azure AD B2C 中的自訂原則來設定電話註冊和登入](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)。
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD 應用程式資源庫中的新布建連接器-2020 年1月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
您現在可以為這些新整合的應用程式，自動建立、更新和刪除使用者帳戶：

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

如需如何使用自動化使用者帳戶佈建來加強組織安全性的詳細資訊，請參閱[使用 Azure AD 自動將使用者佈建至 SaaS 應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

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
 
我們已將兩個新的登入連結偵測類型新增至 Identity Protection：可疑的收件匣操作規則和不可能的移動。 這些離線偵測是由 Microsoft Cloud App Security (MCAS) 探索，並會影響使用者和登入身分識別保護中的風險。 如需這些偵測的詳細資訊，請參閱我們的登[入風險類型](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)。
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>中斷性變更：不會透過登入重新導向來執行 URI 片段

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
從2020年2月8日開始，當要求傳送至 login.microsoftonline.com 以登入使用者時，服務會將空的片段附加至要求。  這可確保瀏覽器抹除要求中任何現有的片段，藉此防止重新導向攻擊的類別。 應用程式不應該依賴此行為。 如需詳細資訊，請參閱 Microsoft 身分識別平臺檔中的[重大變更](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020)。

