---
title: Azure Active Directory 的常見應用程式管理案例 | Microsoft Docs
description: 使用 Azure AD 將應用程式管理集中化
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b81e792d3b3bf491b281e09c00f48a7ad56fa47
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763630"
---
# <a name="centralize-application-management-with-azure-ad"></a>使用 Azure AD 將應用程式管理集中化

密碼對於 IT 人員與世界各地的員工而言都是一大難題。 正因如此，有越來越多的公司想要藉由 Azure Active Directory、Microsoft 的身分識別和存取管理解決方案來處理雲端和所有其他資源的作業。 在不同應用程式之間切換時，並不需要輸入個別應用程式的密碼。 從 Outlook 切換到 Workday、再切換到 ADP 的速度就像開啟一樣快，且既迅速又安全。 然後，您可以直接與合作夥伴和組織外部人員共同作業，完全不需要 IT 人員的協助。 此外，Azure AD 可透過多重要素驗證之類的機制來驗證您的身分，以保護您所使用的應用程式，並使用持續的自適性機器學習和安全情報來偵測可疑的登入，讓您隨時隨地都能安全地存取所需的應用程式，進而為您管理風險。 不僅使用者可蒙受其利，IT 人員也是如此。 透過 Just-In-Time 存取權檢閱和完整的規模控管套件，Azure AD 可協助您保持合規性並強制執行原則。 藉此，您甚至可以將使用者帳戶的佈建自動化，讓存取管理變得輕而易舉。 請查看客戶使用 Azure Active Directory 應用程式管理功能的一些常見案例。

**常見案例**


> [!div class="checklist"]
> * 適用於所有應用程式的 SSO
> * 自動化佈建和取消佈建 
> * 保護您的應用程式
> * 控管對應用程式的存取
> * 混合式安全存取

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>案例 1：為您所有的應用程式設定 SSO

無須再管理密碼。 使用公司認證安全地存取您所需的所有資源。 

|功能  | 描述 | 建議 |
|---------|---------|---------|
|SSO|使用受信任業界標準的標準型同盟 SSO。|如果您的應用程式支援 SSO，請一律使用 [SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) 加以啟用。|
|存取面板|為使用者提供可探索及存取其所有應用程式的簡單中樞。 讓他們能藉由自助式功能提升生產力，例如，要求存取應用程式和群組，或代表其他人管理資源的存取權。| 當您將應用程式與 Azure AD 進行 SSO 的整合之後，請在您的組織中部署[存取面板](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan)。|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>案例 2：自動化佈建和取消佈建 


大部分的應用程式都必須在使用者佈建到應用程式之後，才能讓他們存取所需的資源。 使用 CSV 檔案或複雜的指令碼不僅潛在成本昂貴，也難以管理。 此外，當有人不應再享有存取權時，客戶必須確保帳戶會移除。 請利用下列工具將佈建和取消佈建自動化。 


|功能  |描述|建議 |
|---------|---------|---------|
|SCIM 佈建|[SCIM](https://aka.ms/SCIMOverview) 是將使用者佈建自動化的業界最佳做法。 任何符合 SCIM 規範的應用程式都可以與 Azure AD 整合。 使用者帳戶可以自動建立、更新和刪除，且不需要維護 CSV 檔案、自訂指令碼或內部部署解決方案。|查看 Azure AD 應用程式庫中的[預先整合](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)應用程式清單 (不斷增加中)|
|Microsoft Graph|利用 Azure AD 所含資料的廣度與深度，為您的應用程式擴充其所需的資料。|利用 [Microsoft Graph](https://developer.microsoft.com/graph/) 從整個 Microsoft 生態系統中取得資料。 |


## <a name="scenario-3-secure-your-applications"></a>案例 3：保護您的應用程式
身分識別是安全性的關鍵。 身分識別一旦遭到入侵，就很難及時阻止後續的連鎖效應。 平均而言，組織要超過 100 天才會發現遭到入侵。 請使用 Azure AD 所提供的工具來改善應用程式的安全性狀態。 

|功能  |描述| 建議 |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 Azure MFA 可使用管理員核准的驗證方法，以利保護對資料與應用程式的存取，同時達到對簡單登入程序的需求。| 為您的使用者[啟用 MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)。  |
|條件式存取|使用條件式存取，您可以實作自動化的存取控制決定，以根據條件指出誰可以存取雲端應用程式。| 檢閱客戶使用的[安全性預設值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)和[常用原則](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。 | 
|身分識別保護|Identity Protection 使用 Microsoft 從 Azure AD 的組織中、Microsoft 帳戶的取用者空間，以及 Xbox 的遊戲中獲得的經驗，來保護您的使用者。 Microsoft 每天會分析 6.5 兆個信號，以識別威脅並保護客戶免於遭受威脅。|啟用我們的服務所提供的[預設身分識別保護原則](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies)。 | 

## <a name="scenario-4-govern-access-to-your-applications"></a>案例 4：控管對應用程式的存取
身分識別控管可協助組織取得以下兩者之間的平衡：生產力 - 人員可在多短的時間內取得其所需應用程式的存取權？例如，當他們加入組織時嗎？ 與安全性 - 其存取權應如何隨著時間變更？例如，由於該人員的雇用狀態有所變更嗎？ 

|功能  |描述|建議 |
|---------|---------| ---------|
|ELM|Azure AD 權利管理可協助組織內部和外部的使用者更有效率地管理對其應用程式的存取。| 允許非管理員使用[存取套件](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)來管理對其應用程式的存取。|
|存取權檢閱|您可以定期檢閱使用者的應用程式存取權，以確定只有適當的人員具有持續存取權。| [檢閱對最敏感的應用程式的存取權](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)。 |
|Log Analytics|產生相關報表以指出誰正在存取哪些應用程式，並將其儲存在您選擇的 SIEM 工具中，以便為不同資料來源的資料建立一段時間內的相互關聯。| 啟用 [Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)，並針對應用程式的相關重大事件設定警示。 |


## <a name="scenario-5-hybrid-secure-access"></a>案例 5：混合式安全存取
身分識別必須可連接雲端和內部部署應用程式之間的一切，才能成為您的控制平面。 請利用 Azure AD 及其合作夥伴所提供的工具，來保護對舊版驗證型應用程式的存取。

|功能  |描述|建議 |
|---------|---------|---------|
|應用程式 Proxy|現今的員工想要隨時隨地都能在任何裝置發揮生產力。 他們需要存取雲端中的 SaaS 應用程式和內部部署的公司應用程式。 Azure AD 應用程式 Proxy 可提供這種健全的存取，且不需要昂貴和複雜的虛擬私人網路 (VPN) 或遮蔽式子網路 (DMZ)。|為您的內部部署應用程式設定[遠端存取](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。 |
|F5、Akamai、Zscaler|您可以使用現有的網路和傳遞控制器，輕鬆地保護對您的商務程序仍具重要性，但過去無法以 Azure AD 保護的舊版應用程式。 您可能已擁有開始保護這些應用程式所需的一切。| 應使用 Akamai、Citrix、F5 還是 Zscaler？ 請查看我們[預先建置的解決方案](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)。 | 

## <a name="related-articles"></a>相關文章

- [應用程式管理](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [應用程式佈建](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [混合式安全存取](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [身分識別治理](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft 身分識別平台](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [身分識別安全性](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
