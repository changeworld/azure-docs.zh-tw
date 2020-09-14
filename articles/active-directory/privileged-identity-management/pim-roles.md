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
ms.openlocfilehash: 0403fe3cf0bf8cfaf9c722edadbecd2fee61cb46
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056280"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>您無法在 Privileged Identity Management 中管理的角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 可讓您管理所有 [Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md) 和所有 [Azure 角色](../../role-based-access-control/built-in-roles.md)。 Azure 角色也可以包含附加至管理群組、訂用帳戶、資源群組和資源的自訂角色。 不過，您無法管理某些角色。 本文說明您無法在 Privileged Identity Management 中管理的角色。

## <a name="classic-subscription-administrator-roles"></a>傳統訂用帳戶管理員角色

您無法在 Privileged Identity Management 中管理下列傳統訂用帳戶管理員角色：

- 帳戶管理員
- 服務管理員
- 共同管理員

如需傳統訂用帳戶管理員角色的詳細資訊，請參閱 [傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 系統管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-microsoft-365-admin-roles"></a>Microsoft 365 系統管理員角色呢？

我們支援 Azure AD 角色和系統管理員入口網站體驗中的所有 Microsoft 365 角色，例如 Exchange 系統管理員和 SharePoint 系統管理員，但我們不支援 Exchange RBAC 或 SharePoint RBAC 內的特定角色。 如需這些 Microsoft 365 服務的詳細資訊，請參閱 [Microsoft 365 管理員角色](/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> 適用于 SharePoint 系統管理員角色、裝置系統管理員角色，以及嘗試存取 Microsoft 安全性與合規性中心之任何角色的合格使用者，在啟用其角色之後，可能會遇到最多幾個小時的延遲。 我們與這些團隊合作來修正問題。

## <a name="next-steps"></a>接下來的步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)