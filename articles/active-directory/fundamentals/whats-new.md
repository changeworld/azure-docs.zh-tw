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
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802504"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？

>以複製和貼上此網址`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`:![到 RSS 源](./media/whats-new/feed-icon-16x16.png)閱讀器圖示 來源閱讀器讀取器,獲得有關何時重新存取此頁面以進行更新的通知。

Azure AD 會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能
- 方案變更

此頁面會每月更新，因此請定期瀏覽。 如果想要尋找超過 6 個月的項目，請至 [Azure Active Directory 的新增功能封存](whats-new-archive.md) (英文)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021 年 3 月 B2B 更新中的未託管 Azure 活動目錄帳戶

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
**從 2021 年 3 月 31 日開始**,Microsoft 將不再支援為 B2B 協作方案創建非託管 Azure 活動目錄 (Azure AD) 帳戶和租戶來兌換邀請。 此選項,我們鼓勵您選擇傳送電子郵件[一次性密碼認證](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)。

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>具有預設存取角色的使用者將處於預配範圍

**類型：** 方案變更  
**服務類別：** 應用程式佈建  
**產品功能:** 身份生命週期管理
 
從歷史上看,具有默認訪問角色的使用者已沒有預配範圍。 我們聽到反饋,客戶希望具有此角色的使用者在預配範圍內。 我們正在努力部署更改,以便所有新的預配配置都允許預配具有默認訪問角色的使用者。 逐漸地,我們將更改現有預配配置的行為,以支援使用此角色的預配使用者。 無需客戶操作。 此更改完成後,我們會向[文件](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)發佈更新。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B 協作將在 Microsoft Azure 中提供,該協作由 21Vianet(Azure China 21Vianet) 租戶運營

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure AD B2B 協作功能將在 Microsoft Azure 中提供,該功能將由 21Vianet(Azure China 21Vianet) 租戶運營,使 Azure 中國 21Vianet 租戶中的使用者可以與其他 Azure 中國 21Vianet 租戶中的使用者無縫協作。 [瞭解有關 Azure AD B2B 協作的更多](https://docs.microsoft.com/azure/active-directory/b2b/)。

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B 協作邀請電子郵件重新設計

**類型：** 方案變更  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure [emails](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) AD B2B 協作邀請服務發送的電子郵件將重新設計,以使邀請資訊和使用者的後續步驟更加清晰。

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealm 發現策略變更將顯示在稽核紀錄中

**類型：** 固定  
**服務類別:** 審計  
**產品功能：** 監視和報告
 
我們修復了一個錯誤,其中對[HomeRealm 發現策略](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)的更改未包含在審核日誌中。 現在,您將能夠查看策略更改的時和方式以及由誰更改。 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD 應用庫中提供的新聯合應用 - 2020 年 3 月

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** 協力廠商整合
 
2020 年 3 月,我們在應用庫中添加了這 51 個具有聯合支援的新應用: 

[思科 AnyConnect,](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect) [Zoho 一中國](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [Plus Plus,](https://test.plusplus.app/auth/login/azuread-outlook/) Profit.co [SAML 應用程式](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [iPoint 服務提供者](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), contexxt.ai [SPHERE,](https://contexxt-sphere.com/login)[智慧通過 Invictus,](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial)[火焰數位標牌](https://spark-dev.pixelnebula.com/login), Logz.io -[工程師雲可觀測性](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial),[頻譜U,](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial) [BizzContact,](https://bizzcontact.app/)[埃爾卡諾 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial),[市場標誌共用](http://www.signshare.com/),[交叉知識學習套件](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM HUB,](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial) [RIB A/S Byggeweb移動](https://apps.apple.com/us/app/docia/id529058757), [GoLinks,](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial)[數據狗](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial), [Zscaler B2B使用者門戶](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [LIFT,](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial)[平面視企業一](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial),[觀看團隊](https://www.devfinition.com/), A[斯特,](https://demo.asterapp.io/login)[技能工作流程](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial),[節點洞察](https://admin.nodeinsight.com/AADLogin.aspx), IP[平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial),[管道驅動](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial),[展示工作坊](https://app.showcaseworkshop.com/),[綠燈集成平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial),[綠燈合規存取管理](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial),[格羅克學習](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore在線](https://login.online.miradore.com/),[霍羅斯護理](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial),[詢問你的Team,](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial)[特魯吉特](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial),[智慧豁免](https://www.smartwaiver.com/m/user/sw_login.php?wms_login),[比扎吉 數位過程自動化工作室](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX,](https://www.insuite.jp/) [sybo,](https://www.systexsoftware.com.tw/) [Britive,](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial) [WhosOffice,](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial)[電子天](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial),[科洛蒂維奧 SDN,](https://portal.kollective.app/login) [Witivio,](https://app.witivio.com/) [Playvox,](https://my.playvox.com/login) [Korn Ferry 360,](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial)[校園咖啡廳](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial),[漁獲點](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial),[代碼42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B 協作在 Azure 政府租戶中可用

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C
 
Azure AD B2B 協作功能現在在某些 Azure 政府租戶之間可用。  要瞭解租戶是否能夠使用這些功能,請按照以下說明操作:[如何判斷 Azure 美國政府租戶中 B2B 協作是否可用?。](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure 紀錄的 Azure 監視器整合現在在 Azure 政府中可用

**類型：** 新功能  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
Azure 監視器與 Azure AD 日誌的集成現在在 Azure 政府中可用。 您可以將 Azure AD 紀錄(審核和登錄日誌)路由到存儲帳戶、事件中心和日誌分析。 請查看用於[報告和監視](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting)Azure AD 方案[的詳細文檔](https://aka.ms/aadlogsinamd)以及部署計畫。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure 政府中的識別保護刷新

**類型：** 新功能  
**服務類別:** 身份保護  
**產品功能:** 身份安全&保護

我們很高興地分享,我們現在在[Microsoft Azure 政府門戶](https://portal.azure.us/)中推出了刷新的[Azure AD 標識保護](https://aka.ms/IdentityProtectionDocs) 體驗。 有關詳細資訊,請參閱我們的[公告博客文章](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>災害復原:下載並儲存預先配置

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能:** 身份生命週期管理
 
Azure AD 預配服務提供了一組豐富的配置功能。 客戶需要能夠保存其配置,以便以後可以參考它或回滾到已知良好的版本。 我們添加了將預配配置下載為 JSON 檔並在您需要時上載的能力。 [深入了解](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR(自助服務密碼重置)現在需要兩個門,用於 Microsoft Azure 中的管理員,由 21Vianet (Azure China 21Vianet) 操作 

**類型：** 已變更的功能  
**服務類別:** 自助服務密碼重置  
**產品功能:** 身份安全&保護
 
以前在 Microsoft Azure 中由 21Vianet (Azure China 21Vianet) 操作,使用自助服務密碼重置 (SSPR) 重置自己的密碼的管理員只需要一個「門」(質詢)即可證明其身份。 在公共雲和其他國家雲中,管理員在使用 SSPR 時通常必須使用兩個門來證明其身份。 但由於我們不支援 Azure 中國 21Vianet 中的簡訊或電話,因此允許管理員重置單門密碼。

我們正在創建 Azure 中國 21Vianet 和公共雲之間的 SSPR 功能奇偶校驗。 今後,管理員在使用 SSPR 時必須使用兩個門。 將支援簡訊、電話呼叫和身份驗證器應用通知和代碼。 [深入了解](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>密碼長度限制為 256 個字元

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
為了確保 Azure AD 服務的可靠性,使用者密碼的長度現在限制為 256 個字元。 密碼長度超過此期限的使用者將在後續登錄時通過聯繫其管理員或使用自助服務密碼重置功能更改其密碼。

此更改於 2020 年 3 月 13 日上午 10 點(18:00 UTC)啟用,錯誤為 AADSTS 50052,無效密碼超過最大值長度。 有關詳細資訊,請參閱[中斷更改通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD 登入日誌現在透過 Azure 門戶可供所有免費租戶使用

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視和報告
 
從現在開始,具有免費租戶的客戶可以從 Azure 門戶訪問[Azure AD 登錄日誌](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)長達 7 天。 以前,登錄日誌僅適用於具有 Azure 活動目錄高級許可證的客戶。 通過此更改,所有租戶都可以通過門戶訪問這些日誌。

> [!NOTE]
> 客戶仍然需要進階許可證(Azure 活動目錄高級 P1 或 P2)才能透過 Microsoft 圖形 API 和 Azure 監視器存取登入日誌。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>從 Azure 門戶上的組一般設定棄目錄範圍群組選項

**類型：** 已被取代  
**服務類別：** 群組管理  
**產品功能：** 共同作業

為了為客戶提供更靈活的方法來創建最能滿足其需求的目錄範圍組,我們已將 Azure 門戶中的 **「組** > **一般**」設置中的 **「目錄範圍組」** 選項替換為指向[動態組文件](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)的連結。 我們改進了文檔以包含更多說明,以便管理員可以創建包含或排除來賓使用者的所有使用者組。

---

## <a name="february-2020"></a>2020 年 2 月

### <a name="upcoming-changes-to-custom-controls"></a>將對自訂控制項的變更

**類型：** 方案變更  
**服務類別:** Mfa  
**產品功能:** 身份安全&保護
 
我們計劃用允許合作夥伴提供的身份驗證功能與 Azure Active Directory 管理員和最終使用者體驗無縫協作的方法替換當前自定義控制件預覽。 如今,合作夥伴 MFA 解決方案面臨以下限制:它們僅在輸入密碼後才工作;它們不作為 MFA 在其他關鍵方案中進行後續身份驗證;並且它們不與最終使用者或管理憑據管理功能集成。 新的實現將允許合作夥伴提供的身份驗證因數與關鍵方案(包括註冊、使用、MFA 聲明、加強身份驗證、報告和日誌記錄)的內置因素一起工作。 

自定義控制者將繼續在預覽版中與新設計一起支援,直到其達到通用性。 屆時,我們將讓客戶有時間遷移到新設計。 由於當前方法的局限性,在新設計可用之前,我們不會加入新的提供程式。 我們正在與客戶和供應商密切合作,並將隨著我們越來越近而傳達時程表。 [深入了解](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)。

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>身份安全分數 ─MFA 改進操作更新

**類型：** 方案變更  
**服務類別:** Mfa  
**產品功能:** 身份安全&保護
 
為了反映企業在應用適用於其業務的策略時確保最高安全性的需求,Microsoft Secure Score 刪除了三個以多重身份驗證 (MFA) 為中心的改進操作,並添加了兩個改進操作。

將刪除以下改進操作:

- 註冊 MFA 的所有使用者
- 要求所有使用者使用 MFA
- 需要 MFA 才能取得 Azure AD 特權角色

新增以下改進操作:

- 確保所有使用者都能完成 MFA 進行安全存取
- 需要 MFA 才能擔任管理角色

這些新的改進操作將需要在目錄中註冊您的使用者或管理員 MFA,並建立適合組織需求的正確策略集。 主要目標是具有靈活性,同時確保所有使用者和管理員都能使用多種因素或基於風險的身份驗證提示進行身份驗證。 這可以採取設置安全預設值的形式,讓 Microsoft 決定何時向使用者挑戰 MFA,或者有多個策略應用範圍決策。 作為這些改進操作更新的一部分,基線保護策略將不再包含在評分計算中。 [閱讀更多關於微軟安全得分即將推出的內容](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide)。

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD 網域服務 SKU 選擇

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services
 
我們聽到反饋說,Azure AD 域服務客戶希望在為其實例選擇性能級別時更具靈活性。 從 2020 年 2 月 1 日開始,我們從動態模型(Azure AD 根據物件計數確定性能和定價層)切換到自選模型。 現在,客戶可以選擇與其環境相匹配的性能層。 此更改還允許我們啟用資源林等新方案以及高級功能(如每日備份)。 現在,所有 SKU 的物件計數都是無限的,但我們將繼續為每個層提供物件計數建議。

**無需立即採取客戶操作。** 對於現有客戶,2020 年 2 月 1 日使用的動態層決定了新的預設層。 此更改不會對定價或性能產生影響。 今後,Azure AD DS 客戶將需要評估性能要求,因為他們的目錄大小和工作負載特徵發生變化。 服務層之間的切換將繼續是一個無停機時間操作,並且我們將不再根據客戶目錄的增長自動將客戶移動到新的層。 此外,不會漲價,新的定價將與我們目前的計費模式一致。 有關詳細資訊,請參閱 Azure [AD DS SKU 文件](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus)和[Azure AD 網域服務定價頁](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD 應用庫中提供的新聯合應用 - 2020 年 2 月

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** 協力廠商整合
 
2020 年 2 月,我們在應用庫中添加了這 31 個具有聯合支援的新應用: 

[IamIP專利平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial),[體驗雲](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO為 Azure,](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)[梭子魚電子郵件安全服務](https://ess.barracudanetworks.com/sso/azure), [ABa 報告](https://myaba.co.uk/client-access/signin/auth/msad),[在危機的情況下 - 在線門戶](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC雲設計](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial),[養蜂人 Azure AD 數據連接器](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial) [Korn, 渡輪評估](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada 命令](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial),[飛濺頂](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense,](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial) [EAB 導航](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial),[新遺物 ( 限量發行)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) , [Thulium](https://admin.thulium.com/login/instance),[票務經理](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial),[範本選擇團隊](https://links.officeatwork.com/templatechooser-download-teams),[蜜蜂](https://www.beesy.me/index.php/site/login),[健康支援系統](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL,](https://app.mural.co/signup) [Hive,](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial) [LavaDo,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)[威克特,](https://wakelet.com/login) [Firmex VDR,](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial)[教師和學校,](https://www.thinglink.com/)[科達](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial),[近波達,](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial),[邀請人](https://invitepeople.com/login),[重印台 - 文章](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), 銀河[TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD 應用程式庫中的新預配連接器 - 2020 年 2 月

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** 協力廠商整合
 
現在,您可以自動建立、更新和刪除這些新整合應用的使用者帳戶:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

有關如何使用自動使用者帳戶預先更好地保護組織的詳細資訊,請參閱[使用 Azure AD 自動向 SaaS 應用程式預配使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>在混合環境中支援 FIDO2 安全金鑰

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
我們宣佈在混合環境中對 FIDO2 安全金鑰的 Azure AD 支援的公共預覽。 用戶現在可以使用 FIDO2 安全金鑰登錄到其混合 Azure AD 加入的 Windows 10 裝置,並無縫登錄到其本地和雲端資源。 自從我們最初在 Azure AD 加入的設備上啟動 FIDO2 支援的公共預覽以來,對混合環境的支援一直是我們無密碼客戶請求最多的功能。 使用生物識別和公開金鑰/私鑰加密等先進技術的無密碼身份驗證在安全保護的同時提供了方便和易用性。 使用此公共預覽版,您現在可以使用 FIDO2 安全金鑰等現代身份驗證來存取傳統的 Active Directory 資源。 關於詳細資訊,請存[取 SSO 到本地資源](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)。 

要開始使用,請造[訪為租戶啟用FIDO2安全金鑰](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key),以獲得分步說明。 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>新的「我的帳戶」體驗現已普遍提供

**類型：** 已變更的功能  
**服務類別:** 我的個人資料/帳戶  
**產品功能:** 最終使用者體驗
 
我的帳戶,所有終端使用者帳戶管理需求的一站式商店,現在普遍可用! 最終使用者可以通過 URL 或新的「我的應用」體驗的標頭訪問此新網站。 詳細瞭解新體驗[在"我的帳戶門戶概述"](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)中提供的所有自助服務功能。

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>我的帳號網站網址 更新到myaccount.microsoft.com

**類型：** 已變更的功能  
**服務類別:** 我的個人資料/帳戶  
**產品功能:** 最終使用者體驗
 
新的"我的帳戶"最終用戶體驗將在下個月將其 URL`https://myaccount.microsoft.com`更新到。 在["我的帳戶"門戶説明](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)中,查找有關體驗及其向最終使用者提供的所有帳戶自助服務功能的詳細資訊。

---
 
## <a name="january-2020"></a>2020 年 1 月
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>新的「我的應用」門戶現在通常可用

**類型：** 方案變更  
**服務類別：** 我的應用程式  
**產品功能:** 最終使用者體驗
 
將您的組織升級到新的「我的應用」門戶,該門戶現在通常可用! 在["我的應用"門戶上創建集合](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)上查找有關新門戶和集合的詳細資訊。

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD 的工作區已重新命名為集合

**類型：** 已變更的功能  
**服務類別：** 我的應用程式   
**產品功能:** 最終使用者體驗
 
工作區(篩選器管理員可以配置為組織其使用者應用)現在稱為集合。 在["我的應用"門戶上創建集合](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections),有關如何配置它們的詳細資訊。

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>使用自訂政策 (公共預覽)Azure AD B2C 電話註冊和登入

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C
 
通過電話號碼註冊和登錄,開發人員和企業可以允許其客戶使用通過 SMS 發送到使用者電話號碼的一次性密碼進行註冊和登錄。 此功能還允許客戶在無法訪問其手機時更改其電話號碼。 借助自定義策略的強大功能,電話註冊和登錄允許開發人員和企業通過頁面自定義來傳達其品牌。 瞭解如何[在 Azure AD B2C 中使用自訂策略設定電話註冊和登入](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)。
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD 應用程式庫中的新預配連接器 - 2020 年 1 月

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** 協力廠商整合
 
現在,您可以自動建立、更新和刪除這些新整合應用的使用者帳戶:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

有關如何使用自動使用者帳戶預先更好地保護組織的詳細資訊,請參閱[使用 Azure AD 自動向 SaaS 應用程式預配使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD 應用庫中提供的新聯合應用 - 2020 年 1 月

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** 協力廠商整合
 
2020 年 1 月,我們在應用庫中添加了這 33 個具有聯合支援的新應用: 

[JOSA,](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial)[快速邊緣雲](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform 企業](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial)[阿比博特網路科菲克](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick,](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)[上攻](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial),[離開機器人](https://leavebot.io/#home),[資料營](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial),[旅行行動](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial),[AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial)[智慧工作](https://www.intumit.com/english/SmartWork.html),[EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)[網路監控](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/) [SSOGEN - Azure AD SSO 閘道甲骨文電子商務套件 - EBS, PeopleSoft, 和 JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), 託管[MyCirqa SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial)[玉湖物業管理平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps,](https://sites.lumapps.com/login)[上工企業](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial),[人才軟](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB為微軟團隊](http://teams.smartdb.jp/login/),[新聞頁面](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial),[合同安全 Saml2 SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial) [Maxient 行為管理器軟體](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial),[幫助轉移](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365,](https://www.portaltalk.com/) [CoreView,](https://portal.coreview.com/) [Squelch 雲 Office365 連接器](https://laxmi.squelch.io/login), [PingFlow 認證](https://app-staging.pingview.io/),[印表機邏輯 SaaS,](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial) [Sandas](https://app.sandwai.com/) [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="two-new-identity-protection-detections"></a>兩個新的身份保護偵測

**類型：** 新功能  
**服務類別:** 身份保護  
**產品功能:** 身份安全&保護
 
我們添加了兩種新的登錄鏈接檢測類型到身份保護:可疑的收件箱操作規則和不可能的旅行。 這些離線檢測由Microsoft雲應用安全 (MCAS) 發現,並影響身份保護中的使用者和登錄風險。 有關這些檢測的詳細資訊,請參閱我們的[登入風險類型](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)。
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>中斷變更:URI 片段不會透過登入重定

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證
 
從 2020 年 2 月 8 日開始,當請求發送到 login.microsoftonline.com 以登錄使用者時,服務將附加一個空片段到請求中。  這通過確保瀏覽器擦除請求中的任何現有片段來防止一類重定向攻擊。 任何應用程式都不應依賴於此行為。 有關詳細資訊,請參閱在 Microsoft 識別平台文件中[分解變更](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020)。

---

## <a name="december-2019"></a>2019 年 12 月

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>將 SAP 成功因子預先載到 Azure AD 和本地 AD(公共預覽)

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能:** 身份生命週期管理

現在,您可以在 Azure AD 中整合 SAP 成功因數作為權威標識來源。 此整合可説明您自動執行端到端標識生命週期,包括使用基於 HR 的事件(如新員工或終止)來控制 Azure AD 帳戶的預配。

有關如何設置 SAP 成功因數到 Azure AD 的入站預配的詳細資訊,請參閱[配置 SAP 成功因數自動預配](https://aka.ms/SAPSuccessFactorsInboundTutorial)教程。

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>支援 Azure AD B2C 中的自訂電子郵件(公共預覽版)

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C

現在,當用戶註冊使用你的應用時,可以使用 Azure AD B2C 創建自訂電子郵件。 通過使用 DisplayControls(目前預覽版)和第三方電子郵件供應商(如[SendGrid、SparkPost](https://sendgrid.com/)[SparkPost](https://sparkpost.com/)或自訂 REST API),您可以使用自己的電子郵件範本 **「From**位址」和主題文本,以及支援當地語系化和自定義一次性密碼 (OTP) 設定。

關於詳細資訊,請參閱[Azure 的目錄 B2C 中的自訂電子郵件驗證](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)。

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>將基準策略更換為安全預設值

**類型：** 已變更的功能  
**服務類別：** 其他  
**產品功能：** 身分識別安全性與保護

作為身份驗證安全預設模型的一部分,我們將從所有租戶中刪除現有的基線保護策略。 此次刪除目標是在 2 月底完成。 這些基線保護策略的替代是安全預設值。 如果您一直在使用基線保護策略,則必須計劃遷移到新的安全預設策略或條件訪問。 如果您尚未使用這些策略,則無需執行任何操作。

有關新安全預設值的詳細資訊,請參閱[什麼是安全默認值?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 有關條件存取原則的詳細資訊,請參考[通用條件存取原則](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。

---

## <a name="november-2019"></a>2019 年 11 月

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>支援同一網站屬性和 Chrome 80

**類型：** 方案變更  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

作為 Cookie 安全預設模型的一部分,Chrome 80 瀏覽器`SameSite`正在改變在沒有 該屬性的情況下處理 Cookie 的方式。 任何未指定屬性的`SameSite`Cookie 將被視為`SameSite=Lax`設置為 ,這將導致 Chrome 阻止應用可能依賴於的某些跨域 Cookie 共用方案。 要維護較舊的 Chrome 行為,可以`SameSite=None`使用該 屬性`Secure`並添加其他 屬性,因此只能通過 HTTPS 連接訪問跨網站 Cookie。 Chrome 計劃於 2020 年 2 月 4 日前完成此更改。

我們建議所有開發人員使用本指南測試其應用:

- 將 **「使用安全 Cookie」** 設定的預設值設定為 **「是**」 。

- 將**SameSite**屬性的預設值設定為 **"無**" 。

- 新增安全性的額外`SameSite`屬性**Secure**。

有關詳細資訊,請參閱[即將推出的 ASP.NET 中的同一網站 Cookie 更改,並在](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)Chrome 版本[79 及更高版本中 ASP.NET 核心和可能中斷客戶網站和 Microsoft 產品和服務](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)。

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>微軟身份管理員 (MIM) 2016 服務套件 2 (SP2) 的新修補程式

**類型：** 固定  
**服務類別:** 微軟身份管理員  
**產品功能:** 身份生命週期管理

微軟標識管理員 (MIM) 2016 服務包 2 (SP2) 提供修補程式匯總包(內部版本 4.6.34.0)。 此匯總包可解決問題並添加"此更新中添加的問題和改進"部分中描述的改進。

有關詳細資訊並下載修補程式包,請參閱[Microsoft 識別管理員 2016 服務包 2 (內部 4.6.34.0) 更新匯總可用](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)。

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>新的 AD FS 應用程式活動報告可協助將應用移至 Azure AD(公共預覽版)

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** SSO

在 Azure 門戶中使用新的活動目錄聯合服務 (AD FS) 應用活動報告,以確定哪些應用能夠遷移到 Azure AD。 該報告評估所有 AD FS 應用與 Azure AD 的相容性,檢查任何問題,並提供有關準備各個應用進行遷移的指導。

有關詳細資訊,請參閱使用[AD FS 應用程式活動報告將應用程式移到 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)。

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>使用者要求管理員同意的新工作流(公共預覽版)

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** 存取控制

新的管理員同意工作流為管理員提供了一種授予對需要管理員批准的應用的訪問許可權的方法。 如果用戶嘗試訪問應用,但無法提供同意,他們現在可以發送管理員批准請求。 請求通過電子郵件發送,並放置在可從 Azure 門戶訪問的佇列中發送給被指定為審閱者的所有管理員。 審閱者對掛起的請求執行操作后,將通知請求使用者該操作。

有關詳細資訊,請參閱[配置管理員同意工作流(預覽)。](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>管理可選聲明的新 Azure AD 應用程式註冊權杖設定體驗(公共預覽)

**類型：** 新功能  
**服務類別：** 其他  
**產品功能：** 開發人員體驗

Azure 門戶上的新**Azure AD 應用註冊權杖配置**邊欄選項卡現在向應用開發人員顯示其應用的可選聲明的動態清單。 這種新體驗有助於簡化 Azure AD 應用遷移,並最大限度地減少可選聲明錯誤配置。

有關詳細資訊,請參閱向[Azure AD 應用程式提供可選聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)。

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD 授權管理中新的兩階段審批工作流(公共預覽版)

**類型：** 新功能  
**服務類別：** 其他  
**產品功能:** 權利管理

我們引入了一個新的兩階段審批工作流,允許您要求兩個審批人批准使用者對訪問包的請求。 例如,您可以設置它,以便請求使用者的經理必須首先批准,然後也可以要求資源擁有者批准。 如果其中一個審批人未批准,則不會授予訪問許可權。

有關詳細資訊,請參閱[Azure AD 授權管理中存取套件的變更要求與審批設定](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)。

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>"我的應用"頁面以及新工作區的更新(公共預覽)

**類型：** 新功能  
**服務類別：** 我的應用程式  
**產品功能：** 協力廠商整合

您現在可以自定義組織使用者查看和存取刷新的「我的應用」體驗的方式。 這種新體驗還包括新的工作區功能,使用戶能夠更輕鬆地查找和組織應用。

有關新的「我的應用」體驗和創建工作區的詳細資訊,請參閱[在「我的應用」門戶上創建工作區](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)。

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Google 對 Azure AD B2B 協作的社交 ID 支援 (通用發表)

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** 使用者驗證

在 Azure AD 中使用 Google 社交 ID(Gmail 帳戶)的新支援有助於簡化使用者和合作夥伴的協作。 您的合作夥伴不再需要創建和管理新的特定於Microsoft的帳戶。 Microsoft Teams 現在完全支援所有用戶端上的 Google 使用者以及公共和租戶相關的身份驗證終結點。

有關詳細資訊,請參閱添加[Google 作為 B2B 來賓使用者的身份供應商](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>微軟邊緣移動支援條件存取和單一登入(通用版)

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能:** 身份安全&保護

iOS 和 Android 上的 Microsoft Edge 的 Azure AD 現在支援 Azure AD 單一登入和條件存取:

- **微軟邊緣單一登入 (SSO):** 對於所有 Azure AD 連接的應用,現在本機用戶端(如 Microsoft Outlook 和 Microsoft Edge)都可以使用單一登錄。

- **微軟邊緣條件存取:** 通過基於應用程式的條件存取策略,您的使用者必須使用受Microsoft Intune保護的瀏覽器,如Microsoft Edge。

有關條件訪問和Microsoft邊緣SSO的詳細資訊,請參閱[Microsoft邊緣行動支援條件存取和單一登錄現在一般可用的](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179)部落格文章。 有關如何使用[使用使用的條件存取](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)或[以裝置的條件存取](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)設定客戶端應用的詳細資訊,請參閱[使用 Microsoft Intune 策略保護瀏覽器管理 Web 存取](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)。

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD 授權管理 (一般可用性)

**類型：** 新功能  
**服務類別：** 其他  
**產品功能:** 權利管理

Azure AD 授權管理是一項新的標識治理功能,可幫助組織大規模管理標識和訪問生命週期。 此新功能通過自動跨組、應用和 SharePoint Online 網站實現訪問請求工作流、訪問分配、審核和過期而有所説明。

借助 Azure AD 授權管理,您可以更有效地管理員工以及組織外部需要存取這些資源的用戶的存取。

有關詳細資訊,請參閱什麼是[Azure AD 授權管理?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>為這些新支援的 SaaS 應用自動設定使用者帳戶

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** 協力廠商整合  

現在,您可以自動建立、更新和刪除這些新整合應用的使用者帳戶:

[SAP雲平台身份認證服務](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial),[環中心](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial),[空間IQ,](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial)[米羅](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial),[雲門](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor雲端套件](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial),[辦公空間軟體](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial),[優先權矩陣](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

有關如何使用自動使用者帳戶預先更好地保護組織的詳細資訊,請參閱[使用 Azure AD 自動向 SaaS 應用程式預配使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD 應用庫中提供的新聯合應用 - 2019 年 11 月

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** 協力廠商整合

2019 年 11 月,我們在應用庫中添加了這 21 個具有聯合支援的新應用:

[空桌](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial),[胡特套房](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial),[藍色訪問會員 (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial),[位利](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial),[裡瓦](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife門戶](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal 單點 (SSO),](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial) [TeamsChamp,](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279) [Motus,](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial) [MyAryaka,](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial) [BlueMail,](https://loginself1.bluemail.me/) [Beedle,](https://teams-web.beedle.co/#/)[維斯瑪](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk,](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial) [Foko 零售](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets idea&創新管理](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial),[網科使用者身份驗證](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [UniFLOW在線](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial)[,](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial) [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [e4enable](https://portal.e4enable.com/)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>新增與改進的 Azure AD 應用程式庫

**類型：** 已變更的功能  
**服務類別:** 企業應用  
**產品功能：** SSO

我們更新了 Azure AD 應用程式庫,以便更輕鬆地在 Azure 活動目錄租戶上查找支援預配、OpenID 連接和 SAML 的預整合應用。

有關詳細資訊,請參閱[將應用程式新增到 Azure 活動目錄租戶](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)。

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>將應用角色定義長度限制從 120 個字元增加到 240 個字元

**類型：** 已變更的功能  
**服務類別:** 企業應用  
**產品功能：** SSO

我們從客戶那裡聽說,某些應用和服務中應用角色定義值的長度限制太短,為 120 個字元。 作為回應,我們將角色值定義的最大長度增加到 240 個字元。

有關使用特定於應用程式的角色定義的詳細資訊,請參閱[在應用程式中添加應用角色,並在權杖中接收它們](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。

---

## <a name="october-2019"></a>2019 年 10 月

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>棄用用於 Azure AD 識別保護風險偵測的識別RiskEvent API  

**類型：** 方案變更  
**服務類別:** 身份保護  
**產品功能:** 身份安全&保護

為了回應開發人員的反饋,Azure AD 高級 P2 訂閱者現在可以使用 Microsoft 圖形的新風險檢測 API 對 Azure AD 標識保護的風險檢測數據執行複雜的查詢。 現有的[標識RiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API測試版將在**2020年1月10**日左右停止返回數據。 如果您的組織正在使用標識RiskEvent API,則應過渡到新的風險檢測 API。

有關新風險檢測 API 的詳細資訊,請參閱[風險偵測 API 參考文件](https://aka.ms/RiskDetectionsAPI)。

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>對同一網站屬性和 Chrome 80 的應用程式代理支援

**類型：** 方案變更  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

在 Chrome 80 瀏覽器發佈前幾周,我們計劃更新應用程式代理 Cookie 如何處理**SameSite**屬性。 隨著 Chrome 80 的發表,任何未指定**SameSite**屬性的 Cookie 將被視為已設置為`SameSite=Lax`。

為了説明避免此更改的潛在負面影響,我們正在通過以下功能更新應用程式代理訪問和會話 Cookie:

- 將 **「使用安全 Cookie」** 設定的預設值設定為 **「是**」 。

- 將**SameSite**屬性的預設值設定為 **"無**" 。

    >[!NOTE]
    > 應用程式代理訪問 Cookie 始終通過安全通道獨家傳輸。 這些更改僅適用於會話 Cookie。

有關應用程式代理 Cookie 設定的詳細資訊,請參閱[用於造訪 Azure 的應用程式中的應用程式的 Cookie 設定](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)。

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>應用程式註冊(舊版)和應用程式從應用程式註冊門戶(apps.dev.microsoft.com)的融合應用管理將不再可用

**類型：** 方案變更  
**服務類別：** N/A  
**產品功能：** 開發人員體驗

在不久的將來,具有 Azure AD 帳戶的使用者將不再能夠使用應用程式註冊門戶 (apps.dev.microsoft.com)註冊和管理融合的應用程式,或在 Azure 門戶中的應用註冊(舊版)體驗中註冊和管理應用程式。

要瞭解有關新的應用註冊體驗,請參閱[Azure 門戶培訓指南中的應用註冊](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)。

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>從每個使用者 MFA 移轉到基於條件存取的 MFA 期間,使用者不再需要重新註冊

**類型：** 固定  
**服務類別:** Mfa  
**產品功能:** 身份安全&保護

我們已經修復了一個已知問題,即當使用者需要重新註冊是否為每使用者多因素身份驗證 (MFA) 禁用,然後通過條件訪問策略啟用 MFA 時。

要要求使用者重新註冊,可以從 Azure AD 門戶中的使用者的身份驗證方法中選擇 **「必需重新註冊 MFA」** 選項。 有關將使用者從每個使用者 MFA 移到基於條件存取的 MFA 的詳細資訊,請參閱[將使用者從每個使用者 MFA 轉換為基於條件存取的 MFA。](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>在 SAML 權杖中轉換並傳送聲明的新功能

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** SSO

我們添加了其他功能,可説明您在 SAML 令牌中自定義和發送聲明。 這些新功能包括:

- 其他聲明轉換函數,可説明您修改在索賠中發送的值。

- 能夠將多個轉換應用於單個索賠。

- 能夠根據使用者類型和用戶所屬的組指定聲明源。

有關這些新功能的詳細資訊(包括如何使用這些功能),請參閱[為企業應用程式的 SAML 權杖中發出的自訂聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD 中最終使用者的新「我的登錄」頁面

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 監視和報告

我們添加了一個新的 **「我的登錄」**https://mysignins.microsoft.com)頁面( 讓組織的使用者查看其最近的登入歷史記錄,以檢查是否有任何異常活動。 此新頁面允許使用者檢視:

- 如果有人試圖猜測他們的密碼。

- 如果攻擊者成功登錄到他們的帳戶和從什麼位置。

- 攻擊者嘗試訪問哪些應用。

有關詳細資訊,請參閱[「使用者」現在可以查看其登錄歷史記錄,瞭解異常活動](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)博客。

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Azure AD 網域服務 (Azure AD DS) 從傳統移植到 Azure 資源管理員虛擬網路

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

對於被困在經典虛擬網路上的客戶,我們為您提供好消息! 現在,您可以執行從經典虛擬網路到現有資源管理器虛擬網路的一次性遷移。 移至資源管理員虛擬網路後,您將能夠利用其他和升級的功能,如細粒度密碼策略、電子郵件通知和審核日誌。

有關詳細資訊,請參閱預覽[- 將 Azure AD 網域服務從傳統虛擬網路模型移轉到資源管理員](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)。

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C 網頁協定佈局的更新

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C

我們對 Azure AD B2C 的頁面協定版本 1.2.0 引入了一些新的更改。 在此更新版本中,您現在可以控制元素的載入順序,這也有助於阻止載入樣式表 (CSS) 時發生的閃爍。

有關對頁面協定所做的更改的完整清單,請參閱[版本更改紀錄](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)。

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>更新到「我的應用程式」頁面以及新的工作區(公共預覽)

**類型：** 新功能  
**服務類別：** 我的應用程式  
**產品功能：** 存取控制

現在,您可以自定義組織使用者查看和訪問全新"我的應用"體驗的方式,包括使用新的工作區功能,以便他們更輕鬆地查找應用。 新的工作區功能充當組織使用者已有權訪問的應用的篩選器。

有關推出新的「我的應用」體驗和創建工作區的詳細資訊,請參閱[在「我的應用程式(預覽)」門戶上創建工作區](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)。

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>支援每月活動基於使用者的計費模型(一般可用性)

**類型：** 新功能  
**服務類別：** B2C - 取用者身分識別管理  
**產品功能：** B2B/B2C

Azure AD B2C 現在支援每月活動使用者 (MAU) 計費。 MAU 計費基於日曆月中具有身份驗證活動的唯一用戶數。 現有客戶可以隨時切換到此新的計費方法。

從 2019 年 11 月 1 日起,所有新客戶將自動使用此方法計費。 這種計費方法通過成本效益和提前計劃的能力使客戶受益。

有關詳細資訊,請參閱[升級到每月活動使用者的計費模型](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD 應用庫中提供的新聯合應用 - 2019 年 10 月

**類型：** 新功能  
**服務類別:** 企業應用  
**產品功能：** 協力廠商整合

2019 年 10 月,我們在應用庫中添加了這 35 個具有聯合支援的新應用:

[在危機的情況下 - 移動](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial),[朱諾之旅](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial),[指數,](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial) [Tact,](https://tact.ai/assistant/) [OpusCapita現金管理](http://cm1.opuscapita.com/tenantname),[銷售,](https://prd.salestim.io/forms)[學習,](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial)[迪納茨](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz,](https://login.hunchbuzz.com/integrations/azure/process)[新鮮作品](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial),[Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial) [eCornell,](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial)[船哈茲馬特](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope 雲安全](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial),[內容,](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)[綁定調諧](https://bindtuning.com/login),[雇用Vue協調 - 歐洲](https://www.hirevue.com/), [HireVue座標 - 美國只](https://www.hirevue.com/),[Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup)[雇用Vue - 美國](https://www.hirevue.com/),[威蒂帕羅知識盒](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial)[雲](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial)[,](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial) [Teamie](https://theteamie.com/),[速度為 團隊](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB 導航 IMPL,](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial)螢幕[Meet,](https://console.screenmeet.com/)[歐米茄點](https://pi.ompnt.com/), 為[Intune (iPhone) 說話電子郵件](https://speaking.email/FAQ/98/email-access-via-microsoft-intune),[為辦公室 365 直接 (iPhone/安卓)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)說話電子郵件 , [ExactCare SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial) [iHealthHome護理導航系統](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD 門戶中的整合安全選單項

**類型：** 已變更的功能  
**服務類別:** 身份保護  
**產品功能:** 身份安全&保護

現在,您可以在 Azure 門戶中的新**安全**功能表項和 **「搜尋**」欄訪問所有可用的 Azure AD 安全功能。 此外,新的**安全**登錄頁稱為 **「安全 - 入門**」,將提供指向我們的公共文檔、安全指南和部署指南的連結。

新的**安全性**選單包括:

- 條件式存取
- 身分識別保護
- 資訊安全中心
- 身分識別安全分數
- 驗證方法
- Mfa
- 風險報告 - 風險使用者、風險登錄、風險檢測
- 等等

有關詳細資訊,請參閱安全[- 入門](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)。

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365 組過期策略增強自動續訂

**類型：** 已變更的功能  
**服務類別：** 群組管理  
**產品功能:** 身份生命週期管理

Office 365 組過期策略已增強,可自動續訂其成員正在使用中的組。 組將根據所有 Office 365 應用(包括 Outlook、SharePoint 和團隊)的用戶活動自動續訂。

此增強功能有助於減少組過期通知,並有助於確保活動組繼續可用。 如果 Office 365 組已具有活動過期策略,則無需執行任何操作來打開此新功能。

有關詳細資訊,請參閱為[Office 365 組設定過期策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)。

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>更新 Azure AD 網域服務 (Azure AD DS) 建立體驗

**類型：** 已變更的功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

我們更新了 Azure AD 功能服務 (Azure AD DS),以包括新的和改進的創建體驗,説明您只需點擊三下即可創建託管域! 此外,您現在可以從範本上載和部署 Azure AD DS。

有關詳細資訊,請參閱[教學:建立和設定 Azure 活動目錄網域服務實例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)。

---
