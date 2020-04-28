---
title: Azure Active Directory 的常見應用程式管理案例 |Microsoft Docs
description: 使用 Azure AD 集中化應用程式管理
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21caec50bf9c20ec60194976b1bfbf16e4815914
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261035"
---
# <a name="centralize-application-management-with-azure-ad"></a>使用 Azure AD 集中化應用程式管理

密碼，這兩者都是 IT 難題，而且對世界各地的員工而言都很困難。 這就是為什麼越來越多的公司想要 Azure Active Directory、Microsoft 的身分識別和存取管理解決方案，適用于雲端和所有其他資源。 從應用程式跳到應用程式，而不需要為每一個輸入密碼。 從 Outlook、Workday 到 ADP 的速度，就像您可以快速又安全地開啟它們一樣快。 然後與合作夥伴和組織外部的其他人共同作業，而不需要呼叫它。 此外，Azure AD 藉由保護您使用的應用程式與多重要素驗證等專案來驗證您的身分，以協助管理風險，無論您身在何處，都能使用持續的彈性機器學習和安全性情報來偵測可疑的登入，讓您安全地存取所需的應用程式。 這不僅適用于使用者，也適用于這種情況。 有了即時的存取權審查和完整的規模治理套件，Azure AD 可協助您保持合規性並強制執行原則。 如此一來，您甚至可以將布建使用者帳戶自動化，讓存取管理變得輕而易舉。 查看客戶使用 Azure Active Directory 的應用程式管理功能的一些常見案例。

**常見案例**


> [!div class="checklist"]
> * 適用于所有應用程式的 SSO
> * 自動布建和取消布建 
> * 保護您的應用程式
> * 管理應用程式的存取權
> * 混合式安全存取

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>案例1：為您的所有應用程式設定 SSO

不再管理密碼。 使用公司認證安全地存取您所需的所有資源。 

|功能  | 描述 | 建議 |
|---------|---------|---------|
|SSO|以標準為基礎的同盟 SSO，使用受信任的業界標準。|當您的應用程式支援 SSO 時，請一律使用[SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation)來啟用它。|
|存取面板|為使用者提供可探索及存取其所有應用程式的簡單中樞。 使用自助功能讓它們更具生產力，例如要求應用程式和群組的存取權，或代表其他人管理資源的存取權。| 當您將應用程式與 SSO 的 Azure AD 整合之後，請在您的組織中部署[存取面板](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan)。|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>案例2：自動布建和取消布建 


大部分的應用程式都需要將使用者布建到應用程式，才能存取所需的資源。 使用 CSV 檔案或複雜字集可能會很昂貴，而且難以管理。 此外，當有人不應該再有存取權時，客戶必須確保帳戶會遭到移除。 利用下列工具來自動化布建和取消布建。 


|功能  |描述|建議 |
|---------|---------|---------|
|SCIM 布建|[SCIM](https://aka.ms/SICMOverview)是自動化使用者布建的業界最佳作法。 任何符合 SCIM 規範的應用程式都可以與 Azure AD 整合。 自動建立、更新和刪除使用者帳戶，而不需要維護 CSV 檔案、自訂腳本或內部內部部署解決方案。|查看 Azure AD 應用程式庫中[預先整合](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)之應用程式的成長清單|
|Microsoft Graph|利用 Azure AD 的資料吸一口氣和深度，讓您的應用程式與所需的資料更豐富。|利用[microsoft graph](https://developer.microsoft.com/graph/)從整個 microsoft 生態系統取得資料。 |


## <a name="scenario-3-secure-your-applications"></a>案例3：保護您的應用程式
身分識別是安全性的 linchpin。 如果身分識別遭到入侵，則在過晚之前停止 domino 的效果很難。 在組織發現洩露時，平均超過100天。 使用 Azure AD 所提供的工具來改善應用程式的安全性狀態。 

|功能  |描述| 建議 |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 Azure MFA 使用系統管理員核准的驗證方法，協助保護對您的資料和應用程式的存取，同時滿足簡單登入流程的需求。| 為使用者[啟用 MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) 。  |
|條件式存取|透過條件式存取，您可以根據條件，針對可存取雲端應用程式的人員執行自動化存取控制決策。| 查看客戶使用的[安全性預設值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)和[一般原則](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。 | 
|身分識別保護|Identity Protection 使用 Microsoft 從 Azure AD 的組織中、Microsoft 帳戶的取用者空間，以及 Xbox 的遊戲中獲得的經驗，來保護您的使用者。 Microsoft 每天會分析 6.5 兆個信號，以識別威脅並保護客戶免於遭受威脅。|啟用服務所提供的[預設身分識別保護原則](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies)。 | 

## <a name="scenario-4-govern-access-to-your-applications"></a>案例4：管理應用程式的存取權
身分識別控管可協助組織在產能之間取得平衡-個人可存取所需應用程式的速度，例如當他們加入我的組織時？ 與安全性 - 其存取權應該如何隨著時間變更，例如由於該人員的雇用狀態變更？ 

|功能  |描述|建議 |
|---------|---------| ---------|
|榆樹|Azure AD 權利管理可協助組織內部和外部的使用者更有效率地管理其應用程式的存取權。| 允許非系統管理員使用[存取套件](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)來管理其應用程式的存取權。|
|存取權檢閱|使用者對應用程式的存取權可以定期審查，以確保只有適當的人員能夠繼續存取。| 檢查對您最敏感的應用程式的[存取權](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)。 |
|Log Analytics|產生有關誰正在存取哪些應用程式，並將其儲存在您選擇的 SIEM 工具中，以便在資料來源與一段時間之間相互關聯資料的報表。| 啟用[log analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ，並設定與您的應用程式相關之重大事件的警示。 |


## <a name="scenario-5-hybrid-secure-access"></a>案例5：混合式安全存取
如果身分識別可以在雲端和內部部署應用程式之間連接所有專案，就只能是您的控制平面。 利用 Azure AD 及其合作夥伴所提供的工具，來保護對舊版驗證型應用程式的存取。

|功能  |描述|建議 |
|---------|---------|---------|
|應用程式 Proxy|現今的員工想要隨時隨地都能在任何裝置發揮生產力。 他們需要存取雲端中的 SaaS 應用程式和內部部署的公司應用程式。 Azure AD 應用程式 proxy 可讓這種健全的存取不需要昂貴且複雜的虛擬私人網路（Vpn）或非軍事區域（Dmz）。|為您的內部內部部署應用程式設定[遠端存取](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。 |
|F5、Akamai、Zscaler|您可以使用現有的網路和傳遞控制器，輕鬆地保護對您的商務程序仍具重要性，但過去無法以 Azure AD 保護的舊版應用程式。 您可能已擁有開始保護這些應用程式所需的一切。| 使用 Akamai、Citrix、F5 或 Zscaler？ 查看我們[預先建立的解決方案](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)。 | 

## <a name="related-articles"></a>相關文章

- [應用程式管理](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [應用程式佈建](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [混合式安全存取](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [身分識別治理](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft 身分識別平台](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [身分識別安全性](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
