---
title: 在特權標識管理中無法管理的角色 - Azure 活動目錄 |微軟文件
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
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607530"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>在權限身分管理中無法管理的角色

Azure 的活動目錄 (Azure AD) 權識別管理 (PIM) 讓您能夠管理所有[Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md)與所有 Azure[角色](../../role-based-access-control/built-in-roles.md)。 Azure 角色還可以包括附加到管理組、訂閱、資源組和資源的自定義角色。 不過，您無法管理某些角色。 本文介紹了在特權身份管理中無法管理的角色。

## <a name="classic-subscription-administrator-roles"></a>傳統訂用帳戶管理員角色

您不能在權限管理中管理以下經典訂閱管理員角色:

- 帳戶管理員
- 服務管理員
- 共同管理員

如需傳統訂用帳戶管理員角色的詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-office-365-admin-roles"></a>那麼 Office 365 管理員角色呢？

我們支援 Azure AD 角色和管理員門戶體驗中的所有 Office365 角色,例如 Exchange 管理員和 SharePoint 管理員,但我們不支援 Exchange RBAC 或 SharePoint RBAC 中的特定角色。 如需有關這些 Office 365 服務的詳細資訊，請參閱 [Office 365 管理員角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> 符合 SharePoint 管理員角色的合格使用者以及嘗試訪問 Microsoft 安全和合規性中心的任何角色在啟動其角色後可能會遇到長達幾個小時的延遲。 我們正在與這些團隊合作來解決問題。

## <a name="next-steps"></a>後續步驟

- [在權限管理中配置 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在權限管理中配置 Azure 資源角色](pim-resource-roles-assign-roles.md)
