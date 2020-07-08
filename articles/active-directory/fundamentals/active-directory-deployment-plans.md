---
title: 部署方案 - Azure Active Directory | Microsoft Docs
description: 如何部署眾多 Azure Active Directory 功能的相關端對端指引。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c439bbded7fe55f1edd5eb1597f98b339e340956
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386329"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署方案
正在尋找部署 Azure Active Directory （Azure AD）功能的端對端指引嗎？ Azure AD 部署計畫會引導您瞭解成功部署常見 Azure AD 功能所需的商業價值、規劃考慮和操作程式。

從任何計畫頁面，使用瀏覽器的 [列印至 PDF] 功能來建立檔的最新離線版本。
## <a name="include-the-right-stakeholders"></a>包含正確的專案關係人

當您開始規劃新功能的部署時，請務必在組織中包含重要的專案關係人。 我們建議您識別並記載符合下列每個角色的人員或人員，並與他們合作以判斷他們在專案中的參與程度。  

角色可能包括下列各項 

|角色 |Description |
|-|-|
|終端使用者|將會實作為功能的代表性使用者群組。 通常會預覽試驗計畫中的變更。
|IT 支援經理|IT 支援組織代表誰可以從技術支援人員的觀點來提供這項變更的可支援性。  
|身分識別架構設計人員或 Azure 全域管理員|身分識別管理小組代表負責定義這項變更如何與組織中的核心身分識別管理基礎結構一致。|
|應用程式商務擁有者 |受影響應用程式的整體商務擁有者，可能包括管理存取權。也可以提供使用者體驗的輸入，以及從使用者的觀點來看這項變更的實用性。
|安全性擁有者|安全性小組的代表，可以將方案登出，以符合組織的安全性需求。|
|合規性管理員|貴組織內負責確保符合公司、產業或政府需求的人員。|

**參與程度可能包括：**

- 用於執行專案計劃和結果的**R**esponsible 

- 專案計劃和結果**的 pproval** 

- **C**ontributor 至專案計劃和結果 

- 專案計劃和**結果 nformed**


## <a name="best-practices-for-a-pilot"></a>試驗的最佳做法
試驗可讓您使用小型群組進行測試，然後再為所有人開啟功能。 請確定在測試過程中，您組織內的每個使用案例都經過徹底測試。 最好先將特定的試驗使用者群組設為目標，然後再將其提供給整個組織。

在您的第一波，以 IT、可用性及其他可測試並提供意見反應的適當使用者為目標。 此意見反應應用來進一步開發您傳送給使用者的通訊和指示，並提供支援人員可能會看到的問題類型的深入解析。 

將首度發行擴大至較大群組的使用者，應該藉由增加目標群組的範圍來執行。 這可以透過[動態群組成員資格](../users-groups-roles/groups-dynamic-membership.md)來完成，或手動將使用者新增至目標群組。


## <a name="deploy-authentication"></a>部署驗證

| 功能 | 說明|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 Azure MFA 可使用管理員核准的驗證方法，以利保護對資料與應用程式的存取，同時達到對簡單登入程序的需求。 |
| [條件式存取](../conditional-access/plan-conditional-access.md)| 使用條件式存取，您可以實作自動化的存取控制決定，以根據條件指出誰可以存取雲端應用程式。 |
| [自助式密碼重設](../authentication/howto-sspr-deployment.md)| 自助式密碼重設可協助您的使用者在不需要系統管理員介入的情況下，重設其密碼。 |
| [無密碼](../authentication/howto-authentication-passwordless-deployment.md) | 在您的組織中使用 Microsoft Authenticator 應用程式或 FIDO2 安全性金鑰來執行無密碼驗證 |

## <a name="deploy-application-and-device-management"></a>部署應用程式和裝置管理

| 功能 | 說明|
| -| - |
| [單一登入](../manage-apps/plan-sso-deployment.md)| 單一登入可協助您的使用者在只登入一次時，存取所需的應用程式和資源來執行業務。 登入之後，他們可以從 Microsoft Office 到 SalesForce 到 Box，到內部應用程式，而不需要再次輸入認證。 |
| [存取面板](../manage-apps/access-panel-deployment-plan.md)| 為使用者提供可探索及存取其所有應用程式的簡單中樞。 讓他們能藉由自助式功能提升生產力，例如，要求存取應用程式和群組，或代表其他人管理資源的存取權。 |
| [裝置](../devices/plan-device-deployment.md) | 本文可協助您評估將裝置與 Azure AD 整合的方法、選擇執行計畫，並提供支援的裝置管理工具的重要連結。 |


## <a name="deploy-hybrid-scenarios"></a>部署混合式案例

| 功能 | 說明|
| -| -|
| [以 ADFS 進行密碼雜湊同步處理](../hybrid/plan-migrate-adfs-password-hash-sync.md)| 使用密碼雜湊同步處理時，使用者密碼的雜湊會從內部部署 Active Directory 同步到 Azure AD，讓 Azure AD 驗證使用者，而不需要與內部部署互動 Active Directory |
| [以 ADFS 通過驗證](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Azure AD 傳遞驗證可協助您的使用者使用相同的密碼登入內部部署和雲端式應用程式。 這項功能可為使用者提供更好的體驗，也就是要記住的密碼較少，並降低 IT 技術服務人員的成本，因為使用者較不可能忘記登入的方式。 當使用者使用 Azure AD 登入時，此功能會向您的內部部署 Active Directory 直接驗證使用者的密碼。 |
| [Azure AD 應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) |現今的員工想要隨時隨地都能在任何裝置發揮生產力。 他們需要存取雲端中的 SaaS 應用程式和內部部署的公司應用程式。 Azure AD 應用程式 Proxy 可提供這種健全的存取，且不需要昂貴和複雜的虛擬私人網路 (VPN) 或遮蔽式子網路 (DMZ)。 |
| [無縫 SSO](../hybrid/how-to-connect-sso-quick-start.md)| 使用者位於連線到公司網路的公司裝置時，Azure Active Directory 無縫單一登入 (Azure AD 無縫 SSO) 就會自動將他們登入。 有了這項功能，使用者就不需要輸入密碼就能登入 Azure AD，而且通常不需要輸入他們的使用者名稱。 這項功能可讓授權使用者輕鬆存取您的雲端應用程式，而不需要任何額外的內部部署元件。 |

## <a name="deploy-user-provisioning"></a>部署使用者布建

| 功能 | 說明|
| -| -|
| [使用者佈建](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD 可協助您自動化在雲端 (SaaS) 應用程式 (例如 Dropbox、Salesforce、ServiceNow 等等) 中建立、維護和移除使用者身分識別的作業。 |
| [雲端 HR 使用者布建](../app-provisioning/plan-cloud-hr-provision.md)| 雲端 HR 使用者布建到 Active Directory 會建立持續身分識別管理的基礎，並增強依賴授權身分識別資料的商務程式品質。 將這項功能與您的雲端 HR 產品（例如 Workday 或 Successfactors）搭配使用時，您可以設定規則，將按下的 Leaver 流程（例如新雇用、終止、轉移）對應到 IT 布建動作（例如建立、啟用、停用），以順暢地管理員工和臨時員工的身分識別生命週期。 |

## <a name="deploy-governance-and-reporting"></a>部署治理和報告

| 功能 | 說明|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) 可協助您管理遍及 Azure AD、Azure 資源及其他 Microsoft Online Services 的特殊權限系統管理角色。 PIM 提供 Just-In-Time 存取、要求核准工作流程，以及完全整合的存取權檢閱等解決方案，讓您能夠即時識別、發現和防止特殊權限角色的惡意活動。 |
| [報告和監視](../reports-monitoring/plan-monitoring-and-reporting.md)| Azure AD 報告和監視解決方案的設計取決於您的法律、安全性和營運需求，以及現有的環境和程式。 本文提供各種不同的設計選項，並引導您進行適當的部署策略。 |
