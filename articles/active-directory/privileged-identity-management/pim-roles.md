---
title: 您無法在 Privileged Identity Management Azure Active Directory 中管理的角色 |Microsoft Docs
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
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80762511591d10bd2823101e2ff233fdd0f0eb00
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495619"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>您無法在 Privileged Identity Management 中管理的角色

Azure Active Directory （Azure AD） Privileged Identity Management （PIM）可讓您管理所有[Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md)和所有[Azure 角色](../../role-based-access-control/built-in-roles.md)。 Azure 角色也可以包含連結至您的管理群組、訂用帳戶、資源群組和資源的自訂角色。 不過，您無法管理某些角色。 本文說明您無法在 Privileged Identity Management 中管理的角色。

## <a name="classic-subscription-administrator-roles"></a>傳統訂用帳戶管理員角色

您無法在 Privileged Identity Management 中管理下列傳統訂用帳戶管理員角色：

- 帳戶管理員
- 服務管理員
- 共同管理員

如需傳統訂用帳戶管理員角色的詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 系統管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-office-365-admin-roles"></a>那麼 Office 365 管理員角色呢？

我們支援 Azure AD 角色和系統管理員入口網站體驗中的所有 Office365 角色，例如 Exchange 系統管理員和 SharePoint 管理員，但我們不支援 Exchange RBAC 或 SharePoint RBAC 內的特定角色。 如需有關這些 Office 365 服務的詳細資訊，請參閱 [Office 365 管理員角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> 符合資格的使用者 SharePoint 管理員角色、裝置系統管理員角色，以及嘗試存取 Microsoft 安全性與合規性中心的任何角色，可能會在啟用其角色後的幾小時內遇到延遲。 我們正與這些小組合作來修正問題。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
