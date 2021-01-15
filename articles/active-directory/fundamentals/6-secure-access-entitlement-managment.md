---
title: 使用 Azure Active Directory 權利管理來管理外部存取
description: 如何使用 Azure Active Directory 權利管理作為整體外部存取安全性計畫的一部分。
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
ms.openlocfilehash: 9de0ce5e83b33cd793953e2b863f26dffafe58ee
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222389"
---
# <a name="manage-external-access-with-entitlement-management"></a>使用權利管理管理外部存取 


[權利管理](../governance/entitlement-management-overview.md) 是一項身分識別治理功能，可讓組織藉由自動化存取要求工作流程、存取指派、評論和到期日，大規模管理身分識別和存取生命週期。 權利管理可讓委派的非系統管理員建立 [存取套件](../governance/entitlement-management-overview.md) ，讓其他組織的外部使用者可以要求存取。 您可以設定一個和多個階段的核准工作流程來評估要求 [，並](../governance/what-is-provisioning.md) 使用週期性評論來布建使用者，以獲得限時的存取權。 權利管理可啟用以原則為基礎的外部帳戶布建和解除布建。

## <a name="key-concepts-for-enabling-entitlement-management"></a>啟用權利管理的重要概念

下列重要概念對權利管理相當重要。

### <a name="access-packages"></a>存取套件

[存取套件](../governance/entitlement-management-overview.md)是權利管理的基礎。 存取套件是使用者在專案上共同作業或進行其他工作所需的原則控管資源群組。 例如，存取套件可能包括：

* 存取特定的 SharePoint 網站。

* 企業應用程式，包括您的自訂內部和 SaaS 應用程式（例如 Salesforce）。

* Microsoft 團隊頻道。

* Microsoft 365 群組。 

### <a name="catalogs"></a>目錄

存取套件位於 [目錄](../governance/entitlement-management-catalog-create.md)。 當您想要將相關的資源和存取套件進行分組，並委派管理它們的能力時，請建立目錄。 首先，將資源新增至目錄，然後您可以新增這些資源以存取套件。 例如，您可能想要建立「財務」目錄，並將 [其管理委派](../governance/entitlement-management-delegate.md) 給財務小組的成員。 該人員接著可以 [新增資源](../governance/entitlement-management-catalog-create.md)、建立存取套件，以及管理這些套件的存取核准。

下圖顯示一般治理生命週期，可供外部使用者存取具有到期的存取套件。

![外部使用者治理週期的圖表。](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>自助外部存取

您可以透過 [Azure AD 我的存取權入口網站](../governance/entitlement-management-request-access.md) 來呈現存取套件，讓外部使用者要求存取權。 原則會決定誰可以要求存取套件。 您可以指定允許誰要求存取套件：

* 特定的 [已連線組織](../governance/entitlement-management-organization.md)

* 所有設定的已連線組織

* 所有組織中的所有使用者

* 已在您租使用者中的成員或來賓使用者

### <a name="approvals"></a>核准   
存取套件可包含強制核准以進行存取。 **一律為外部使用者執行核准程式**。 核准可以是單一或多階段的核准。 核准取決於原則。 如果內部和外部使用者都需要存取相同的封裝，您可能會針對不同類別的已連線組織和內部使用者，設定不同的存取原則。

### <a name="expiration"></a>到期  
存取套件可包含到期日。 到期日可以設定為特定一天，或讓使用者有特定天數可供存取。 當存取套件到期，而且使用者沒有其他存取權時，就可以刪除或封鎖代表使用者的 B2B 來賓使用者物件進行登入。 建議您針對外部使用者強制執行存取套件的到期日。 並非所有存取套件都有到期時間。 如果沒有，請確定您執行的是存取權審核。

### <a name="access-reviews"></a>存取權檢閱

存取套件可能需要定期 [存取評論](../governance/manage-guest-access-with-access-reviews.md)，這需要封裝擁有者或受指派者證明使用者存取的持續需要。 

在您設定評論之前，請先判斷下列各項。

* 人員

   * 繼續存取的準則為何？

   * 誰是指定的審核者？

* 排程審核的執行頻率為何？

   * 內建選項包括每月、每季、每半年或每年。 

   * 針對支援外部存取的封裝，我們建議每季或更頻繁。 

 

> [!IMPORTANT]
> 存取套件的存取權評論只會審核透過權利管理授與的存取權。 因此，您必須設定其他進程，以檢查在權利管理之外提供給外部使用者的任何存取權。

如需存取權評論的詳細資訊，請參閱 [規劃 Azure AD 存取評論部署](../governance/deploy-access-reviews.md)。

## <a name="using-automation-in-entitlement-management"></a>在權利管理中使用自動化

您可以 [使用 Microsoft Graph 來執行權利管理功能](https://docs.microsoft.com/graph/tutorial-access-package-api)，包括

* [管理存取套件](https://docs.microsoft.com/graph/api/resources/accesspackage?view=graph-rest-beta)

* [管理存取權檢閱](https://docs.microsoft.com/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [管理已連線的組織](https://docs.microsoft.com/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [管理權利管理設定](https://docs.microsoft.com/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>建議 

我們建議使用權利管理來管理外部存取的做法。

**對於具有一或多個商業夥伴的專案，請 [建立並使用存取套件](../governance/entitlement-management-access-package-create.md) 來上架並布建這些合作夥伴的使用者資源存取權**。 

* 如果您的目錄中已有 B2B 使用者，您也可以直接將它們指派給適當的存取套件。

* 您可以在 [Azure 入口網站](../governance/entitlement-management-access-package-assignments.md)中指派存取權，或透過 [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta)指派。

**當使用者的存取套件到期時，請使用您的身分識別治理設定來移除目錄中的使用者**。

![設定管理外部使用者生命週期的螢幕擷取畫面。](media/secure-external-access/6-manage-external-lifecycle.png)

這些設定僅適用于透過權利管理上線的使用者。

**將 [目錄和存取套件的管理委派](../governance/entitlement-management-delegate.md)給商務擁有者，以取得應存取人員的詳細資訊**。

![設定目錄的螢幕擷取畫面。](media/secure-external-access/6-catalog-management.png)

**針對外部使用者可存取的 [存取套件強制到期](../governance/entitlement-management-access-package-lifecycle-policy.md) 。**


![設定存取套件到期的螢幕擷取畫面。](media/secure-external-access/6-access-package-expiration.png)

* 如果您知道以專案為基礎之存取套件的結束日期，請使用 [開始日期] 來設定特定日期。 

* 否則，建議您不再將到期日設為365天，除非已知為多年的參與。

* 允許使用者擴充存取權。

* 需要核准才能授與延伸模組。

**[強制執行套件的存取權審核](../governance/manage-guest-access-with-access-reviews.md) ，以避免對來賓的不當存取。**

![建立新存取套件的螢幕擷取畫面。](media/secure-external-access/6-new-access-package.png)

* 每季強制執行評論。

* 針對符合規範的專案，請將審核者設定為特定審核者，而不是針對外部使用者進行自我檢查。 存取套件管理員的使用者是開始審核審核者的絕佳位置。 

* 針對較不敏感的專案，讓使用者自行審視可減少組織的負擔，以移除不再具有主要組織之使用者的存取權。

如需詳細資訊，請參閱 [在 Azure AD 權利管理中管理外部使用者的存取權](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>後續步驟

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。

1. [判斷外部存取的安全性狀態](1-secure-access-posture.md)

2. [探索您目前的狀態](2-secure-access-current-state.md)

3. [建立治理計畫](3-secure-access-plan.md)

4. [使用群組進行安全性](4-secure-access-groups.md)

5. [轉換至 Azure AD B2B](5-secure-access-b2b.md)

6. [使用權利管理](6-secure-access-entitlement-managment.md) (您的安全存取。 ) 

7. [使用條件式存取原則保護存取](7-secure-access-conditional-access.md)

8. [使用敏感度標籤保護存取](8-secure-access-sensitivity-labels.md)

9. [安全存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)

 

 
