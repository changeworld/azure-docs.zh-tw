---
title: 權利管理中的委派和角色-Azure AD
description: 瞭解如何將 IT 系統管理員的存取管理委派給部門經理和專案經理，讓他們可以自行管理存取權。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261836"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Azure AD 權利管理中的委派和角色

根據預設，全域系統管理員和使用者系統管理員可以建立及管理 Azure AD 權利管理的所有層面。 不過，這些角色中的使用者可能不知道需要存取套件的所有情況。 這通常是個別部門、小組或專案中的使用者，他們知道誰在共同作業、使用哪些資源，以及有多長的時間。 您可以授與使用者執行其工作所需的最低許可權，並避免建立衝突或不適當的存取權限，而不是將不受限制的許可權授予非系統管理員。

這段影片提供如何將存取管理委派給不是系統管理員的使用者的總覽。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>委派範例

若要瞭解如何在權利管理中委派存取治理，它有助於考慮一個範例。 假設您的組織具有下列系統管理員和經理。

![從 IT 系統管理員委派給經理](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

身為 IT 系統管理員，Hana 在每個部門都有連絡人--在行銷中 Mamta、在財務中標記，以及在法律中負責其部門資源和商務關鍵內容的 Joe。

使用「權利管理」，您可以將存取治理委派給這些非系統管理員，因為他們是知道哪些使用者需要存取、多久時間，以及對哪些資源有哪些許可權。 這可確保適當的人員管理其部門的存取權。

以下是 Hana 可以將存取管理委派給行銷、財務和法律部門的一種方式。

1. Hana 會建立新的 Azure AD 安全性群組，並將 Mamta、Mark 和 Joe 新增為群組的成員。

1. Hana 會將該群組新增至目錄建立者角色。

    Mamta、Mark 和 Joe 現在可以為其部門建立目錄、新增其部門所需的資源，以及在目錄內執行進一步的委派。

    請注意，Mamta、Mark 和 Joe 無法看到彼此的目錄。

1. Mamta 會建立**行銷**目錄，這是資源的容器。

1. Mamta 會將其行銷部門擁有的資源新增至此目錄。

1. Mamta 可以新增其部門的其他人員做為此目錄的目錄擁有者。 這有助於分享目錄管理責任。

1. Mamta 可以進一步將行銷目錄中存取套件的建立和管理委派給行銷部門的專案經理。 她可以藉由將它們指派給存取套件管理員角色來執行這項操作。 存取套件管理員可以建立和管理存取封裝。 

下圖顯示具有行銷、財務和法律部門資源的類別目錄。 專案經理可以使用這些目錄，為其小組或專案建立存取封裝。

![權利管理委派範例](./media/entitlement-management-delegate/elm-delegate.png)

委派之後，行銷部門可能會有類似下表的角色。

| User | 作業角色 | Azure AD 角色 | 權利管理角色 |
| --- | --- | --- | --- |
| Hana | IT 管理員 | 全域管理員或使用者管理員 |  |
| Mamta | 行銷經理 | User | 目錄建立者和目錄擁有者 |
| Bob | 行銷組長 | User | 目錄擁有者 |
| Jessica | 行銷專案經理 | User | 存取套件管理員 |

## <a name="entitlement-management-roles"></a>權利管理角色

權利管理具有下列專屬於權利管理的角色。

| 權利管理角色 | 描述 |
| --- | --- |
| 目錄建立者 | 建立和管理目錄。 通常是不是全域管理員的 IT 系統管理員，或是資源集合的資源擁有者。 建立類別目錄的人員會自動成為目錄的第一個目錄擁有者，而且可以新增其他目錄擁有者。 目錄建立者無法管理或查看其未擁有的類別目錄，也無法將其本身未擁有的資源新增至目錄。 如果目錄建立者需要管理另一個類別目錄，或新增其未擁有的資源，他們可以要求成為該目錄或資源的共同擁有者。 |
| 目錄擁有者 | 編輯和管理現有的目錄。 通常是 IT 系統管理員或資源擁有者，或是目錄擁有者已指定的使用者。 |
| 存取套件管理員 | 編輯和管理目錄中的所有現有存取封裝。 |

此外，指定的核准者和存取套件的要求者也具有許可權，但它們並不是角色。

| Right | 描述 |
| --- | --- |
| 核准者 | 由原則授權以核准或拒絕存取封裝的要求，但無法變更存取套件定義。 |
| 申請者 | 由存取封裝的原則授權，以要求該存取封裝。 |

下表列出權利管理角色可以執行的工作。

| Task | 管理 | 目錄建立者 | 目錄擁有者 | 存取套件管理員 |
| --- | :---: | :---: | :---: | :---: |
| [委派給目錄建立者](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [新增已連線的組織](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [[建立新的目錄]](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [將資源新增至目錄](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [新增目錄擁有者](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [編輯目錄](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [刪除目錄](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [委派給存取套件管理員](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [移除存取套件管理員](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [在目錄中建立新的存取套件](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [變更存取套件中的資源角色](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [建立和編輯原則](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [直接將使用者指派給存取套件](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看誰具有存取套件的指派](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看存取套件的要求](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看要求的傳遞錯誤](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [重新處理要求](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [解除擱置中的要求](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [隱藏存取套件](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [刪除存取套件](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>將資源新增至目錄的必要角色

全域管理員可以在目錄中新增或移除任何群組（已建立雲端的安全性群組或雲端建立的 Office 365 群組）、應用程式或 SharePoint Online 網站。 使用者系統管理員可以新增或移除目錄中的任何群組或應用程式。

對於不是全域管理員或使用者系統管理員的使用者，若要將群組、應用程式或 SharePoint Online 網站新增至目錄，該使用者必須*同時*擁有必要的 Azure AD 目錄角色和目錄擁有者權利管理角色。 下表列出將資源新增至目錄所需的角色組合。 若要移除目錄中的資源，您必須擁有相同的角色。

| Azure AD 目錄角色 | 權利管理角色 | 可以新增安全性群組 | 可以新增 Office 365 群組 | 可以新增應用程式 | 可以新增 SharePoint Online 網站 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [全域管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [使用者系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [Microsoft Teams 服務管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| [雲端應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| User | 目錄擁有者 | 只有在群組擁有者 | 只有在群組擁有者 | 只有應用程式擁有者 |  |

若要判斷工作的最低特殊許可權角色，您也可以[在 Azure Active Directory 中，依管理工作參考系統管理員角色](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)。

## <a name="next-steps"></a>後續步驟

- [將存取管理委派給目錄建立者](entitlement-management-delegate-catalog.md)
- [建立和管理資原始目錄](entitlement-management-catalog-create.md)
