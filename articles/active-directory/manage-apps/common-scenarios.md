---
title: Azure 活動目錄的常見應用程式管理方案 |微軟文件
description: 使用 Azure AD 集中應用程式管理
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
ms.openlocfilehash: d21ff820470765b82e397e56a2458603b8e5a7c7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657945"
---
# <a name="centralize-application-management-with-azure-ad"></a>使用 Azure AD 集中應用程式管理

密碼,既是 IT 噩夢,也是全球員工的痛苦。 這就是為什麼越來越多的公司轉向 Azure 活動目錄,這是 Microsoft 雲和所有其他資源的標識和存取管理解決方案。 從應用程式跳轉到應用程式,而無需為每個應用程式輸入密碼。 快速、快速、安全地從 Outlook 跳到工作日到 ADP。 然後,無需致電 IT,即可與合作夥伴甚至組織外部的其他人協作。 此外,Azure AD 還通過保護您使用的應用(如多重身份驗證)來驗證您是誰,使用連續自適應機器學習和安全智慧來檢測可疑登錄,從而説明您安全地訪問所需的應用,從而管理風險,無論您身在何處。 它不僅對使用者都很好,對 IT 也大有用。 借助及時的訪問審核和全面的治理套件,Azure AD 還可以説明您保持合規性並強制實施策略。 並且,您甚至可以自動預配使用者帳戶,使存取管理變得輕鬆。 查看客戶使用 Azure 活動目錄的應用程式管理功能的一些常見方案。

**常見方案**


> [!div class="checklist"]
> * 適用於所有應用程式的 SSO
> * 自動預先定義並取消預先配 
> * 保護您的應用程式
> * 管理對應用程式的存取
> * 混合安全存取

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>配置 1: 為所有應用程式設定 SSO

無需再管理密碼。 使用公司憑據安全地訪問您需要的所有資源。 

|功能  | 描述 | 建議 |
|---------|---------|---------|
|SSO|使用受信任的行業標準,基於標準的聯合 SSO。|始終使用[SAML / OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation)在應用程式支援 SSO 時啟用 SSO。|
|存取面板|為使用者提供可探索及存取其所有應用程式的簡單中樞。 通過自助服務功能(如請求存取應用和組或代表其他人管理對資源的訪問許可權)使他們能夠提高工作效率。| 將應用與 SSO 的 Azure AD 整合後,在組織中部署[存取面板](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan)。|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>方案 2:自動預先配和取消預先 


大多數應用程式要求使用者在訪問所需的資源之前預配到應用程式中。 使用 CSV 檔或複雜的腳本可能成本高昂且難以管理。 此外,客戶需要確保在某人不應再具有訪問許可權時刪除帳戶。 利用以下工具自動調配和取消預配。 


|功能  |描述|建議 |
|---------|---------|---------|
|SCIM 預先接|[SCIM](https://aka.ms/SICMOverview)是自動化用戶預配的行業最佳實踐。 任何符合 SCIM 的應用程式都可以與 Azure AD 整合。 自動創建、更新和刪除使用者帳戶,而無需維護 CSV 檔、自訂文本或預處理解決方案。|檢視 Azure AD 應用函式庫中不斷成長的[預整合](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)的應用程式清單|
|Microsoft Graph|利用 Azure AD 所需的數據呼吸和深度,使用所需的數據豐富應用程式。|利用[Microsoft 圖形](https://developer.microsoft.com/graph/)從整個 Microsoft 生態系統獲取數據。 |


## <a name="scenario-3-secure-your-applications"></a>配置 3:保護您的應用程式
身份是安全的關鍵。 如果身份遭到破壞,在為時已晚之前阻止多米諾骨牌效應是非常困難的。 平均而言超過100天,組織才發現存在妥協。 使用 Azure AD 提供的工具改善應用程式的安全狀態。 

|功能  |描述| 建議 |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 使用管理員批准的身份驗證方法,Azure MFA 可幫助保護對數據和應用程式的訪問,同時滿足對簡單登錄過程的需求。| 為使用者[啟用 MFA。](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)  |
|條件式存取|借助條件訪問,您可以根據條件為誰可以訪問雲應用實施自動存取控制決策。| 檢視客戶正在使用[的安全預設值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)[與常見政策](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。 | 
|身分識別保護|Identity Protection 使用 Microsoft 從 Azure AD 的組織中、Microsoft 帳戶的取用者空間，以及 Xbox 的遊戲中獲得的經驗，來保護您的使用者。 Microsoft 每天會分析 6.5 兆個信號，以識別威脅並保護客戶免於遭受威脅。|啟用我們的服務提供的[預設身份保護原則](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies)。 | 

## <a name="scenario-4-govern-access-to-your-applications"></a>專案 4:控制對應用程式的存取
身份治理可幫助組織在工作效率之間取得平衡 - 一個人訪問他們需要的應用程式的速度有多快,例如當他們加入我的組織時? 與安全性 - 其存取權應該如何隨著時間變更，例如由於該人員的雇用狀態變更？ 

|功能  |描述|建議 |
|---------|---------| ---------|
|榆樹|Azure AD 授權管理可幫助組織內外的使用者更有效地管理對其應用程式的訪問。| 允許非管理員使用[訪問包](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)管理訪問其應用程式。|
|存取權檢閱|可以定期審查使用者對應用的訪問許可權,以確保只有合適的人員才能繼續訪問。| [查看](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)對最敏感應用程式的訪問許可權。 |
|Log Analytics|生成有關誰正在存取哪些應用程式的報告,並將其存儲在您選擇的 SIEM 工具中,以關聯數據源之間的資料並隨時間而關聯。| 啟用[日誌分析](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics),並為與應用程式相關的關鍵事件設置警報。 |


## <a name="scenario-5-hybrid-secure-access"></a>方案 5:混合安全存取
僅當標識能夠跨雲和本地應用程式連接所有內容時,身份才能成為您的控制平面。 利用 Azure AD 及其合作夥伴提供的工具,確保對基於舊版的應用程式的訪問。

|功能  |描述|建議 |
|---------|---------|---------|
|應用程式 Proxy|現今的員工想要隨時隨地都能在任何裝置發揮生產力。 他們需要存取雲中的 SaaS 應用和本地的企業應用。 Azure AD 應用程式代理支援這種強大的訪問,而無需昂貴和複雜的虛擬專用網路 (VPN) 或非軍事區域 (DMZ)。|為預先安裝應用程式設定[遠端存取](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。 |
|F5, Akamai, 茲卡爾|您可以使用現有的網路和傳遞控制器，輕鬆地保護對您的商務程序仍具重要性，但過去無法以 Azure AD 保護的舊版應用程式。 您可能已擁有開始保護這些應用程式所需的一切。| 使用 Akamai、Citrix、F5 還是 Zscaler? 檢視我們的[預建解決方案](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)。 | 

## <a name="related-articles"></a>相關文章

- [應用程式管理](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [應用程式佈建](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [混合安全存取]()
- [身分識別治理](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft 身分識別平台](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [身份安全](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
