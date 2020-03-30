---
title: 在特權標識管理中無法管理的角色 - Azure 活動目錄 |微軟文檔
description: 說明您無法在 Azure AD Privileged Identity Management (PIM) 中管理的角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895194"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>在特權身份管理中無法管理的角色

Azure 活動目錄 （Azure AD） 特權標識管理 （PIM） 使您能夠管理所有[Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md)和所有 Azure[資源角色](../../role-based-access-control/built-in-roles.md)。 這些角色也包含連結至管理群組、訂用帳戶、資源群組和資源的自訂角色。 不過，您無法管理某些角色。 本文介紹了在特權標識管理中無法管理的角色。

## <a name="classic-subscription-administrator-roles"></a>傳統訂用帳戶管理員角色

您不能在特權標識管理中管理以下經典訂閱管理員角色：

- 帳戶管理員
- 服務管理員
- 共同管理員

如需傳統訂用帳戶管理員角色的詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-office-365-admin-roles"></a>那麼 Office 365 管理員角色呢？

Exchange Online 或 SharePoint Online 中的角色（Exchange 管理員和 SharePoint 管理員除外）在 Azure AD 中不表示，因此無法在特權標識管理中管理。 如需有關這些 Office 365 服務的詳細資訊，請參閱 [Office 365 管理員角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> SharePoint 管理員具有透過 SharePoint Online 系統管理中心存取 SharePoint Online 的系統管理權限，並可在 SharePoint Online 執行幾乎所有工作。 在特權標識管理中啟動後，符合條件的使用者可能會在 SharePoint 中使用此角色遇到延遲。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在特權標識管理中分配 Azure 資源角色](pim-resource-roles-assign-roles.md)
