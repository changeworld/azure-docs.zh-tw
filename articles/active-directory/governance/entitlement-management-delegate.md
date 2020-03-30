---
title: 授權和角色在授權管理 - Azure AD
description: 瞭解如何將訪問治理從 IT 管理員委派給部門經理和專案經理，以便他們可以自行管理存取權限。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261836"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Azure AD 授權管理中的委派和角色

預設情況下，全域管理員和使用者管理員可以創建和管理 Azure AD 授權管理的所有方面。 但是，這些角色中的使用者可能不知道需要訪問包的所有情況。 通常，是各個部門、團隊或專案中的使用者知道他們正在與誰協作、使用什麼資源以及多長時間。 您可以向非管理員授予不受限制的許可權，而不是向非管理員授予執行作業所需的最少許可權，並避免創建衝突或不適當的存取權限。

本視頻概述了如何將訪問治理從 IT 管理員委派給不是管理員的使用者。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>委託示例

要瞭解如何在授權管理中委派訪問治理，考慮一個示例很有説明。 假設您的組織具有以下管理員和經理。

![從 IT 管理員委派給經理](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

作為 IT 管理員，Hana 在每個部門都有連絡人 -- -- 市場行銷部的 Mamta、財務部的 Mark 和法律部的 Joe，負責其部門的資源和業務關鍵內容。

通過授權管理，您可以將訪問治理委派給這些非管理員，因為他們知道哪些使用者需要訪問、訪問多長時間以及哪些資源。 這可確保合適的人員管理其部門的訪問。

以下是 Hana 將訪問治理委派給行銷、財務和法律部門的一種方式。

1. Hana 創建了一個新的 Azure AD 安全性群組，並將媽媽、Mark 和 Joe 添加為該組的成員。

1. Hana 將該組添加到目錄建立者角色。

    Mamta、Mark 和 Joe 現在可以為其部門創建目錄，添加其部門所需的資源，並在目錄中執行進一步的委派。

    請注意，媽媽、馬克和 Joe 看不到彼此的目錄。

1. Mamta 創建**一個市場行銷**目錄，它是一個資源容器。

1. Mamta 將行銷部門擁有的資源添加到此目錄中。

1. Mamta 可以從她的部門添加其他人作為此目錄的目錄擁有者。 這有助於共用目錄管理職責。

1. Mamta 可以進一步將市場行銷目錄中的訪問包的創建和管理委託給行銷部門的專案經理。 她可以通過將它們分配給訪問包管理器角色來執行此操作。 訪問包管理器可以創建和管理訪問包。 

下圖顯示了包含市場行銷、財務和法律部門資源的目錄。 使用這些目錄，專案經理可以為其團隊或專案創建訪問包。

![授權管理委託示例](./media/entitlement-management-delegate/elm-delegate.png)

委派後，行銷部門可能具有類似于下表的角色。

| User | 工作角色 | Azure AD 角色 | 權利管理角色 |
| --- | --- | --- | --- |
| 漢娜 | IT 管理員 | 全域管理員或使用者管理員 |  |
| 馬姆塔 | 行銷經理 | User | 目錄建立者和目錄擁有者 |
| Bob | 行銷主管 | User | 目錄擁有者 |
| 潔西嘉 | 行銷專案經理 | User | 訪問包管理器 |

## <a name="entitlement-management-roles"></a>權利管理角色

授權管理具有特定于授權管理的角色。

| 權利管理角色 | 描述 |
| --- | --- |
| 目錄建立者 | 創建和管理目錄。 通常是不是全域管理員的 IT 管理員，或資源集合的資源擁有者。 創建目錄的人員將自動成為目錄的第一個目錄擁有者，並可以添加其他目錄擁有者。 目錄建立者無法管理或查看他們不擁有的目錄，也不能將他們不擁有的資源添加到目錄中。 如果目錄建立者需要管理其他目錄或添加他們不擁有的資源，他們可以請求成為該目錄或資源的共同擁有者。 |
| 目錄擁有者 | 編輯和管理現有目錄。 通常是 IT 管理員或資源擁有者，或目錄擁有者指定的使用者。 |
| 訪問包管理器 | 編輯和管理目錄中的所有現有訪問包。 |

此外，指定的審批人和訪問包的請求人也具有許可權，儘管它們不是角色。

| Right | 描述 |
| --- | --- |
| 核准者 | 由策略授權批准或拒絕訪問包的請求，儘管它們無法更改訪問包定義。 |
| 要求者 | 由訪問包的策略授權以請求該訪問包。 |

下表列出了授權管理角色可以執行的任務。

| Task | 管理 | 目錄建立者 | 目錄擁有者 | 訪問包管理器 |
| --- | :---: | :---: | :---: | :---: |
| [委託給目錄建立者](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [新增已連線的組織](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [[建立新的目錄]](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [將資源添加到目錄](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [添加目錄擁有者](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [編輯目錄](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [刪除目錄](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [委託給訪問包管理器](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [刪除訪問包管理器](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [在目錄中創建新的訪問包](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [更改訪問包中的資源角色](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [創建和編輯策略](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [直接將使用者分配給訪問包](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看誰分配了訪問包](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看訪問包的請求](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看請求的傳遞錯誤](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [重新處理請求](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [取消擱置要求](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [隱藏訪問包](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [刪除訪問包](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>向目錄添加資源所需的角色

全域管理員可以在目錄中添加或刪除任何組（雲創建的安全性群組或雲創建的 Office 365 組）、應用程式或 SharePoint Online 網站。 使用者管理員可以添加或刪除目錄中的任何組或應用程式。

對於不是全域管理員或使用者管理員的使用者，要將組、應用程式或 SharePoint Online 網站添加到目錄中，該使用者必須*同時*具有所需的 Azure AD 目錄角色和目錄擁有者版權管理角色。 下表列出了向目錄添加資源所需的角色組合。 要從目錄中刪除資源，必須具有相同的角色。

| Azure AD 目錄角色 | 權利管理角色 | 可以添加安全性群組 | 可以添加 Office 365 組 | 可以添加應用 | 可以添加 SharePoint 線上網站 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [全域管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [使用者管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [團隊服務管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [應用程式管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| [雲應用程式管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| User | 目錄擁有者 | 僅當組擁有者 | 僅當組擁有者 | 僅當應用擁有者 |  |

要確定任務的特權最低角色，還可以[按 Azure 活動目錄 中的管理員任務引用管理員角色](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)。

## <a name="next-steps"></a>後續步驟

- [將訪問治理委派給目錄建立者](entitlement-management-delegate-catalog.md)
- [創建和管理資原始目錄](entitlement-management-catalog-create.md)
