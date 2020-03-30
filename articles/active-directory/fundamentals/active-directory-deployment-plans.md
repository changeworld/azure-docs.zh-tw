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
ms.openlocfilehash: 17e6708225262349d56c6e261895882e9c31677f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262213"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署方案
是否尋找有關部署 Azure 活動目錄 （Azure AD） 功能的端到端指導？ Azure AD 部署計畫將引導您完成成功部署常見 Azure AD 功能所需的業務價值、規劃注意事項和操作過程。

從任何計畫頁面，使用瀏覽器的"列印到 PDF"功能創建文檔的最新離線版本。
## <a name="include-the-right-stakeholders"></a>包括合適的利益相關者

開始部署規劃新功能時，必須在整個組織中包括關鍵專案關係人。 我們建議您識別並記錄履行以下每個角色的人員，並與他們合作確定他們參與專案。  

角色可能包括以下內容 

|角色 |描述 |
|-|-|
|最終使用者|將為其實施該功能的代表性使用者組。 通常預覽試驗程式中的更改。
|IT 支援經理|IT 支援組織代表，他可以從説明台的角度為此更改的支援性提供意見。  
|身份架構師或 Azure 全域管理員|負責定義此更改如何與組織中的核心身份管理基礎結構保持一致的身份管理團隊代表。|
|應用程式業務擁有者 |受影響的應用程式的總體業務擁有者，其中可能包括管理訪問。還可以從最終使用者的角度提供有關此更改的使用者體驗和有用性的意見。
|安全擁有者|安全團隊的代表，可以簽署該計畫將滿足組織的安全要求。|
|合規性管理員|組織中負責確保符合公司、行業或政府要求的人員。|

**參與程度可能包括：**

- 執行專案計劃和成果的**R** 

- **A**專案計劃和成果的一個預測 

- **C**專案計劃和結果的三元分項 

- **我**形成專案計劃和結果


## <a name="best-practices-for-a-pilot"></a>飛行員的最佳做法
Pilot 允許您在為所有人打開功能之前，先使用一小群進行測試。 確保作為測試的一部分，對組織中的每個用例都進行了全面測試。 最好在將特定試點使用者組推出給整個組織之前，先將其定位為特定一組試點使用者。

在第一波中，針對 IT、可用性和其他可以測試和提供回饋的適當使用者。 此回饋應用於進一步開發您發送給使用者的通信和說明，並深入瞭解您的支援人員可能看到的問題類型。 

擴大向較大的使用者群體應通過增加目標群組的範圍來進行。 這可以通過[動態組成員身份](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)或手動將使用者添加到目標群組來實現。


## <a name="deploy-authentication"></a>部署身份驗證

| 功能 | 描述|
| -| -|
| [Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)| Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 使用管理員批准的身份驗證方法，Azure MFA 可説明保護對資料和應用程式的訪問，同時滿足對簡單登錄過程的需求。 |
| [條件式存取](https://aka.ms/deploymentplans/ca)| 借助條件訪問，您可以根據條件為誰可以訪問雲應用實施自動存取控制決策。 |
| [自助服務密碼重設](https://aka.ms/deploymentplans/sspr)| 自助服務密碼重設可説明使用者重置密碼，而無需管理員干預，何時何地需要。 |
| [無密碼](https://aka.ms/deploymentplans/passwordless) | 使用組織中的 Microsoft 身份驗證器應用或 FIDO2 安全金鑰實現無密碼身份驗證 |

## <a name="deploy-application-management"></a>部署應用程式管理

| 功能 | 描述|
| -| - |
| [單一登入](https://aka.ms/deploymentplans/sso)| 單一登入可説明使用者訪問開展業務所需的應用和資源，而只需登錄一次。 登錄後，他們可以從 Microsoft Office 轉到 SalesForce 到 Box 到內部應用程式，而無需再次輸入憑據。 |
| [訪問面板](https://aka.ms/deploymentplans/accesspanel)| 為使用者提供可探索及存取其所有應用程式的簡單中樞。 通過自助服務功能（如請求訪問應用和組或代表其他人管理對資源的存取權限）使他們能夠提高工作效率。 |


## <a name="deploy-hybrid-scenarios"></a>部署混合方案

| 功能 | 描述|
| -| -|
| [以 ADFS 進行密碼雜湊同步處理](https://aka.ms/deploymentplans/adfs2phs)| 使用密碼雜湊同步，使用者密碼雜湊從本地活動目錄同步到 Azure AD，允許 Azure AD 對使用者進行身份驗證，而無需與本地活動目錄交互 |
| [以 ADFS 通過驗證](https://aka.ms/deploymentplans/adfs2pta)| Azure AD 直通身份驗證可説明使用者使用相同的密碼登錄到本地和基於雲的應用程式。 此功能為使用者提供了更好的體驗 （少記住一個密碼）並降低了 IT 説明台成本，因為使用者不太可能忘記如何登錄。 當使用者使用 Azure AD 登入時，此功能會向您的內部部署 Active Directory 直接驗證使用者的密碼。 |
| [Azure AD 應用程式 Proxy](https://aka.ms/deploymentplans/appproxy)| 現今的員工想要隨時隨地都能在任何裝置發揮生產力。 他們需要訪問雲中的 SaaS 應用和本地的企業應用。 Azure AD 應用程式代理支援這種強大的訪問，而無需昂貴和複雜的虛擬私人網路絡 （VPN） 或非軍事區域 （DMZ）。 |
| [無縫 SSO](../hybrid/how-to-connect-sso-quick-start.md)| 使用者位於連線到公司網路的公司裝置時，Azure Active Directory 無縫單一登入 (Azure AD 無縫 SSO) 就會自動將他們登入。 使用此功能，使用者無需鍵入密碼才能登錄到 Azure AD，也通常需要輸入其使用者名。 此功能使授權使用者能夠輕鬆訪問基於雲的應用程式，而無需任何其他本機群組件。 |

## <a name="deploy-user-provisioning"></a>部署使用者預配

| 功能 | 描述|
| -| -|
| [使用者佈建](https://aka.ms/deploymentplans/userprovisioning)| Azure AD 可協助您自動化在雲端 (SaaS) 應用程式 (例如 Dropbox、Salesforce、ServiceNow 等等) 中建立、維護和移除使用者身分識別的作業。 |
| [雲 HR 使用者預配](https://aka.ms/deploymentplans/cloudhr)| 雲 HR 使用者預配到 Active Directory 為持續的身份治理奠定了基礎，並提高了依賴于權威標識資料的業務流程的品質。 Using this feature with your cloud HR product, such as Workday or Successfactors, you can seamlessly manage the identity lifecycle of employees and contingent workers by configuring rules that map Joiner-Mover-Leaver processes (such as New Hire, Terminate,傳輸到 IT 預配操作（如創建、啟用、禁用） |

## <a name="deploy-governance-and-reporting"></a>部署治理和報告

| 功能 | 描述|
| -| -|
| [特權身份管理](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) 可協助您管理遍及 Azure AD、Azure 資源及其他 Microsoft Online Services 的特殊權限系統管理角色。 PIM 提供 Just-In-Time 存取、要求核准工作流程，以及完全整合的存取權檢閱等解決方案，讓您能夠即時識別、發現和防止特殊權限角色的惡意活動。 |
| [報告和監測](https://aka.ms/deploymentplans/reporting)| Azure AD 報告和監視解決方案的設計取決於法律、安全性和操作要求以及現有環境和流程。 本文介紹了各種設計選項，並指導您制定正確的部署策略。 |
