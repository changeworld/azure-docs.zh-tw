---
title: 權利管理中的委派和角色-Azure AD
description: 瞭解如何將存取治理從 IT 系統管理員委派給部門經理和專案經理，讓他們可以自行管理存取權。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab68ab4166ddf9e938648e6618ef37df6d998f0
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460893"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Azure AD 權利管理中的委派和角色

根據預設，全域系統管理員和使用者系統管理員可以建立和管理 Azure AD 權利管理的所有層面。 不過，這些角色中的使用者可能不知道需要存取套件的所有情況。 一般來說，這是在個別部門、小組或專案中，使用者可以使用哪些資源和多長的資源來共同作業。 您可以授與使用者執行其工作所需的最低許可權，並避免建立衝突或不當的存取權限，而不是授與不受限制的許可權給非系統管理員。

這段影片概述如何將「存取治理」從 IT 系統管理員委派給非系統管理員的使用者。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>委派範例

若要瞭解您可能在權利管理中委派存取治理的方式，可協助您考慮範例。 假設您的組織具有下列系統管理員和管理員。

![從 IT 系統管理員委派給管理員](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

身為 IT 系統管理員，Hana 在每個部門都有連絡人，也就是行銷中的 Mamta、標示財務，以及負責其部門資源和業務關鍵內容的 Joe。

使用權利管理時，您可以將存取管理委派給這些非系統管理員，因為他們是知道哪些使用者需要存取的使用者、時間長度，以及哪些資源。 這可確保適當的人員管理其部門的存取權。

以下是 Hana 可將存取治理委派給行銷、財務和法律部門的其中一種方式。

1. Hana 會建立新的 Azure AD 安全性群組，並新增 Mamta、Mark 和 Joe 作為群組的成員。

1. Hana 會將該群組新增至目錄建立者角色。

    Mamta、Mark 和 Joe 現在可以為其部門建立目錄、新增其部門所需的資源，以及在目錄內進行進一步的委派。

    請注意，Mamta、Mark 和 Joe 無法看到彼此的目錄。

1. Mamta 會建立 **行銷** 目錄，也就是資源的容器。

1. Mamta 會將行銷部門擁有的資源新增到此目錄。

1. Mamta 可將其部門的其他人員新增為此目錄的目錄擁有者。 這有助於共用目錄管理責任。

1. Mamta 可進一步將行銷目錄中存取套件的建立和管理，委派給行銷部門的專案經理。 她可以將它們指派給存取套件管理員角色來完成這項作業。 存取套件管理員可以建立和管理存取套件。 

下圖顯示具有行銷、財務和法律部門資源的目錄。 專案管理人員可以使用這些目錄來建立其小組或專案的存取套件。

![權利管理委派範例](./media/entitlement-management-delegate/elm-delegate.png)

委派之後，行銷部門可能會有類似下表的角色。

| User | 作業角色 | Azure AD 角色 | 權利管理角色 |
| --- | --- | --- | --- |
| 漢娜 | IT 管理員 | 全域管理員或使用者管理員 |  |
| Mamta | 行銷經理 | User | 目錄建立者和目錄擁有者 |
| Bob | 行銷潛在客戶 | User | 目錄擁有者 |
| 潔西嘉 | 行銷專案經理 | User | 存取套件管理員 |

## <a name="entitlement-management-roles"></a>權利管理角色

權利管理具有下列專屬於權利管理的角色。

| 權利管理角色 | 描述 |
| --- | --- |
| 目錄建立者 | 建立及管理目錄。 通常是不是全域系統管理員的 IT 系統管理員，或是資源集合的資源擁有者。 建立類別目錄的人員會自動成為目錄的第一個目錄擁有者，並可新增其他目錄擁有者。 目錄建立者無法管理或查看他們未擁有的目錄，也無法將其不擁有的資源新增至目錄。 如果目錄建立者需要管理其他類別目錄，或新增其不擁有的資源，則可以要求成為該目錄或資源的共同擁有者。 |
| 目錄擁有者 | 編輯和管理現有的目錄。 通常是 IT 系統管理員或資源擁有者，或是目錄擁有者指定的使用者。 |
| 存取套件管理員 | 編輯及管理目錄中的所有現有存取套件。 |

此外，指定的核准者和存取套件的要求者也有許可權，雖然它們不是角色。

| Right | 描述 |
| --- | --- |
| 核准者 | 由原則授權以核准或拒絕存取套件的要求，但無法變更存取套件定義。 |
| 要求者 | 由存取套件的原則授權，以要求該存取套件。 |

下表列出權利管理角色可執行檔工作。

| Task | 系統管理員 | 目錄建立者 | 目錄擁有者 | 存取套件管理員 |
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
| [查看具有存取套件指派的人員](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看存取套件的要求](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看要求的傳遞錯誤](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [重新處理要求](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [取消擱置要求](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [隱藏存取套件](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [刪除存取套件](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>將資源新增至目錄的必要角色

全域管理員可以在目錄中，新增或移除 (雲端建立的安全性群組或雲端建立的 Microsoft 365 群組) 、應用程式或 SharePoint Online 網站的任何群組。 使用者系統管理員可以在目錄中新增或移除任何群組或應用程式，但設定為可指派給目錄角色的群組除外。

對於非全域管理員或使用者系統管理員的使用者，若要將群組、應用程式或 SharePoint Online 網站新增至目錄 *，該使用者必須具有必要* 的 Azure AD 目錄角色和目錄擁有者權利管理角色。 下表列出將資源新增至目錄所需的角色組合。 若要移除目錄中的資源，您必須擁有相同的角色。

| Azure AD 目錄角色 | 權利管理角色 | 可以新增安全性群組 | 可以新增 Microsoft 365 群組 | 可以新增應用程式 | 可以新增 SharePoint Online 網站 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [全域管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [使用者管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [Microsoft Teams 服務管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| [雲端應用程式管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| User | 目錄擁有者 | 只有當群組擁有者 | 只有當群組擁有者 | 只有在應用程式擁有者 |  |

> [!NOTE]
> 如果使用者新增安全性群組或 Microsoft 365 群組，則群組將無法以角色指派。 如果使用者在建立存取套件時新增角色可指派的群組，則他們也必須是該角色可指派群組的擁有者。 如需詳細資訊，請參閱 [在 Azure Active Directory 中建立可指派角色的群組](../users-groups-roles/roles-groups-create-eligible.md)。

若要判斷工作的最低特殊許可權角色，您也可以 [在 Azure Active Directory 中依管理工作參考系統管理員角色](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)。

## <a name="next-steps"></a>接下來的步驟

- [將存取治理委派給目錄建立者](entitlement-management-delegate-catalog.md)
- [建立及管理資源的類別目錄](entitlement-management-catalog-create.md)
