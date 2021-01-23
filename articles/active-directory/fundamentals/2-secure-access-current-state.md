---
title: 使用 Azure Active Directory 探索外部共同作業的目前狀態
description: 學習探索您共同作業目前狀態的方法。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 302e4becd337c8e7b0b425a52ed46d562db5bae4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725322"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>探索您組織中外部共同作業的目前狀態 

在探索您的外部共同作業目前的狀態之前，您應該先 [決定您想要的安全性狀況](1-secure-access-posture.md)。 您將考慮組織集中式與委派控制的需求，以及任何相關的治理、法規和合規性目標。 

您組織中的個人可能已經與其他組織的使用者合作。 共同作業可以透過生產力應用程式（例如 Microsoft 365、電子郵件）的功能，或透過其他方式與外部使用者共用資源。 您的治理計畫的要素將會形成您的探索 
*   起始外部共同作業的使用者。
*   您要共同合作的外部使用者和組織。
*   授與外部使用者的存取權。


## <a name="users-initiating-external-collaboration"></a>起始外部共同作業的使用者

起始外部共同作業的使用者，最能瞭解與外部共同作業最相關的應用程式，以及何時應結束該存取。 瞭解這些使用者可以協助您判斷誰應委派邀請外部使用者的許可權、建立存取套件，以及完成存取權審核。

若要尋找目前共同作業的使用者，請參閱 [Microsoft 365 審核記錄以進行共用和存取要求活動](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance?view=o365-worldwide#sharing-and-access-request-activities)。 您也可以參閱 [Azure AD audit 記錄檔，以取得將 B2B 使用者邀請到您的目錄的詳細資料](../external-identities/auditing-and-reporting.md) 。

## <a name="find-current-collaboration-partners"></a>尋找目前的共同作業合作夥伴

外部使用者可能會 [AZURE AD B2B 使用者](../external-identities/what-is-b2b.md) (慣用的) 與合作夥伴管理的認證，或具有本機布建認證的外部使用者。 這些使用者通常會 (，但不一定會) 以來賓 UserType 標示。 您可以透過 [MICROSOFT GRAPH API](/graph/api/user-list?tabs=http&view=graph-rest-1.0)、 [PowerShell](/graph/api/user-list?tabs=http&view=graph-rest-1.0)或 [Azure 入口網站](../enterprise-users/users-bulk-download.md)來列舉來賓使用者。

### <a name="use-email-domains-and-companyname-property"></a>使用電子郵件網域和公司名稱屬性

外部組織可由外部使用者電子郵件地址的功能變數名稱來決定。 如果支援取用者身分識別提供者（例如 Google），可能就不可能。 在此情況下，我們建議您撰寫「公司名稱」屬性來清楚識別使用者的外部組織。

### <a name="use-allow-or-deny-lists"></a>使用允許或拒絕清單

請考慮您的組織是否想要只允許特定組織的共同作業。 或者，如果您的組織想要封鎖與特定組織的共同作業，請考慮此情況。  在租使用者層級，有 [允許或拒絕清單](../external-identities/allow-deny-list.md)，可用來控制所有的 B2B 邀請和兌換次數，不論來源 (例如，小組、SharePoint 和 Azure 入口網站) 。
如果您是使用權利管理，您也可以使用特定的 [已連線組織] 設定，將存取套件的範圍設定為您的夥伴子集，如下所示。


![建立新存取套件時允許拒絕清單的螢幕擷取畫面。](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>尋找授與外部使用者的存取權

當您有外部使用者和組織的清查之後，您可以使用 Microsoft Graph API 來決定授與這些使用者的存取權，以判斷 Azure AD [群組成員資格](/graph/api/resources/groups-overview?view=graph-rest-1.0) 或 Azure AD 中的 [直接應用程式指派](/graph/api/resources/approleassignment?view=graph-rest-1.0) 。


### <a name="enumerate-application-specific-permissions"></a>列舉應用程式特定的許可權

您也可以執行應用程式特定的許可權列舉。 例如，您可以使用 [這個腳本](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64)，以程式設計的方式產生 SharePoint Online 的許可權報表。

特別調查您對所有商務和商務關鍵應用程式的存取權，讓您完全瞭解任何外部存取。

### <a name="detect-ad-hoc-sharing"></a>偵測特定共用
如果您的電子郵件和網路方案已啟用，您可以調查透過電子郵件共用的內容，或透過未經授權的軟體即服務 (SaaS) 應用程式。 [Microsoft 365 資料遺失保護](/microsoft-365/compliance/data-loss-prevention-policies?view=o365-worldwide) 可協助您識別、預防及監視跨 Microsoft 365 基礎結構的機密資訊意外共用。 [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) 可協助您識別在您的環境中使用未經授權的 SaaS 應用程式。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。

1. [判斷外部存取的安全性狀態](1-secure-access-posture.md)

2. [探索](2-secure-access-current-state.md) 您目前的狀態 (。 ) 

3. [建立治理計畫](3-secure-access-plan.md)

4. [使用群組進行安全性](4-secure-access-groups.md)

5. [轉換至 Azure AD B2B](5-secure-access-b2b.md)

6. [使用權利管理來保護存取權](6-secure-access-entitlement-managment.md)

7. [使用條件式存取原則保護存取](7-secure-access-conditional-access.md)

8. [使用敏感度標籤保護存取](8-secure-access-sensitivity-labels.md)

9. [安全存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)