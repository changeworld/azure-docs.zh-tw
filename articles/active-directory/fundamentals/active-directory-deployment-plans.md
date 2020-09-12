---
title: 部署方案 - Azure Active Directory | Microsoft Docs
description: 如何部署眾多 Azure Active Directory 功能的相關端對端指引。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75081c218536d17798d1ffa788f79130211f72d3
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318422"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署計劃
尋找部署 Azure Active Directory (Azure AD) 功能的端對端指引嗎？ Azure AD 部署計畫會逐步引導您完成商務價值、規劃考慮，以及成功部署常見 Azure AD 功能所需的操作程式。

從任何計畫頁面，使用瀏覽器的 [列印至 PDF] 功能建立檔的最新離線版本。
## <a name="include-the-right-stakeholders"></a>包含適當的專案關係人

當您開始規劃新功能的部署時，請務必在您的組織中包含重要的專案關係人。 我們建議您識別並記載滿足下列每個角色的人員或人員，並與他們合作來判斷他們在專案中的參與程度。  

角色可能包含下列各項： 

|角色 |描述 |
|-|-|
|終端使用者|將在其中執行功能的代表性使用者群組。 通常會預覽試驗計畫中的變更。
|IT 支援管理員|IT 支援組織代表誰可以提供這項變更的可支援性，從技術服務的觀點來看。  
|身分識別架構管理員或 Azure 全域管理員|身分識別管理小組代表定義此變更如何與組織中的核心身分識別管理基礎結構一致。|
|應用程式商務擁有者 |受影響應用程式的整體企業擁有者 (s) ，其中可能包括管理存取權。也可以提供使用者體驗的輸入，以及這項變更與使用者觀點的實用性。
|安全性擁有者|安全性小組的代表，可登出方案將符合您組織的安全性需求。|
|合規性管理員|您組織中負責確保符合公司、產業或政府需求的人員。|

**參與程度可能包括：**

- 用於執行專案計劃和結果的**R**esponsible 

- 專案計劃和結果**的 pproval** 

- **C**ontributor 至專案計劃和結果 

- **Nformed 專案**計畫和結果


## <a name="best-practices-for-a-pilot"></a>試驗的最佳作法
試驗可讓您使用小型群組進行測試，再為每個人開啟功能。 確定在測試過程中，您組織內的每個使用案例都經過徹底測試。 最好是將目標設為特定的試驗使用者群組，然後再將此專案推出給整個組織。

在您的第一波，將目標設為 IT、可用性，以及可以測試並提供意見反應的其他適當使用者。 此意見反應應用來進一步開發您傳送給使用者的通訊和指示，並提供您的支援人員可能會看到的問題類型見解。 

將導入擴展至更大的使用者群組，應藉由增加) 目標的群組 (範圍來執行。 這可以透過 [動態群組成員資格](../users-groups-roles/groups-dynamic-membership.md)來完成，或手動將使用者新增至目標群組， (s) 。


## <a name="deploy-authentication"></a>部署驗證

| 功能 | 描述|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 Azure MFA 可使用管理員核准的驗證方法，以利保護對資料與應用程式的存取，同時達到對簡單登入程序的需求。 |
| [條件式存取](../conditional-access/plan-conditional-access.md)| 使用條件式存取，您可以實作自動化的存取控制決定，以根據條件指出誰可以存取雲端應用程式。 |
| [自助式密碼重設](../authentication/howto-sspr-deployment.md)| 自助式密碼重設可協助您的使用者在不需要系統管理員介入的情況下，重設其密碼。 |
| [無密碼](../authentication/howto-authentication-passwordless-deployment.md) | 在您的組織中使用 Microsoft Authenticator 應用程式或 FIDO2 安全性金鑰來執行無密碼 authentication |

## <a name="deploy-application-and-device-management"></a>部署應用程式和裝置管理

| 功能 | 描述|
| -| - |
| [單一登入](../manage-apps/plan-sso-deployment.md)| 單一登入可協助您的使用者存取在登入時只需要進行商務所需的應用程式和資源。 登入之後，他們就可以從 Microsoft Office 至 SalesForce 到 Box 到內部應用程式，而不需要再次輸入認證。 |
| [存取面板](../manage-apps/access-panel-deployment-plan.md)| 為使用者提供可探索及存取其所有應用程式的簡單中樞。 讓他們能藉由自助式功能提升生產力，例如，要求存取應用程式和群組，或代表其他人管理資源的存取權。 |
| [裝置](../devices/plan-device-deployment.md) | 本文可協助您評估將裝置與 Azure AD 整合的方法、選擇實行方案，並提供支援的裝置管理工具的主要連結。 |


## <a name="deploy-hybrid-scenarios"></a>部署混合式案例

| 功能 | 描述|
| -| -|
| [以 ADFS 進行密碼雜湊同步處理](../hybrid/plan-migrate-adfs-password-hash-sync.md)| 使用密碼雜湊同步處理時，系統會將使用者密碼的雜湊從內部部署 Active Directory 同步處理至 Azure AD，讓 Azure AD 驗證使用者，而不需要與內部部署進行互動 Active Directory |
| [以 ADFS 通過驗證](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Azure AD 傳遞驗證可協助您的使用者使用相同的密碼登入內部部署和雲端式應用程式。 這項功能可為使用者提供更好的體驗，也就是一個比較少的密碼，並降低 IT 技術服務人員的成本，因為使用者比較不可能忘記如何登入。 當使用者使用 Azure AD 登入時，此功能會向您的內部部署 Active Directory 直接驗證使用者的密碼。 |
| [Azure AD 應用程式 Proxy](../manage-apps/application-proxy-deployment-plan.md) |現今的員工想要隨時隨地都能在任何裝置發揮生產力。 他們需要存取雲端中的 SaaS 應用程式和內部部署的公司應用程式。 Azure AD 應用程式 Proxy 可提供這種健全的存取，且不需要昂貴和複雜的虛擬私人網路 (VPN) 或遮蔽式子網路 (DMZ)。 |
| [無縫 SSO](../hybrid/how-to-connect-sso-quick-start.md)| 使用者位於連線到公司網路的公司裝置時，Azure Active Directory 無縫單一登入 (Azure AD 無縫 SSO) 就會自動將他們登入。 使用這項功能時，使用者不需要輸入密碼就能登入 Azure AD，通常不需要輸入其使用者名稱。 這項功能可讓已獲授權的使用者輕鬆存取雲端式應用程式，而不需要任何額外的內部部署元件。 |

## <a name="deploy-user-provisioning"></a>部署使用者布建

| 功能 | 描述|
| -| -|
| [使用者佈建](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD 可協助您自動化在雲端 (SaaS) 應用程式 (例如 Dropbox、Salesforce、ServiceNow 等等) 中建立、維護和移除使用者身分識別的作業。 |
| [雲端 HR 使用者布建](../app-provisioning/plan-cloud-hr-provision.md)| 雲端 HR 使用者布建至 Active Directory 建立進行中身分識別治理的基礎，並增強依賴授權身分識別資料的商務程式品質。 使用這項功能搭配您的雲端 HR 產品（例如 Workday 或 Successfactors），您可以藉由設定規則來對應「員工」、「終止」、「) 」 (的 (流程（例如建立、啟用、停用) ），以順暢地管理員工和臨時員工的身分識別生命週期。 |

## <a name="deploy-governance-and-reporting"></a>部署治理和報告

| 功能 | 描述|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) 可協助您管理遍及 Azure AD、Azure 資源及其他 Microsoft Online Services 的特殊權限系統管理角色。 PIM 提供 Just-In-Time 存取、要求核准工作流程，以及完全整合的存取權檢閱等解決方案，讓您能夠即時識別、發現和防止特殊權限角色的惡意活動。 |
| [報告和監視](../reports-monitoring/plan-monitoring-and-reporting.md)| Azure AD 報告和監視解決方案的設計取決於您的法律、安全性和操作需求，以及現有的環境和流程。 本文提供各種設計選項，並引導您進行適當的部署策略。 |
| [存取評論](../governance/deploy-access-reviews.md) | 存取評論是治理策略中很重要的一部分，可讓您知道及管理誰有權存取，以及他們有權存取的內容。 本文可協助您規劃及部署存取權評論，以達成所需的安全性與共同作業做法。 |