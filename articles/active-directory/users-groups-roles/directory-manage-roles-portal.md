---
title: 查看和指派系統管理員角色許可權-Azure AD |Microsoft Docs
description: 您現在可以查看和管理入口網站中的 Azure AD 系統管理員角色成員。 針對經常管理角色指派的人員。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2815b496545ca5f920c00df7b2b5b7efe15c12a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900924"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中檢視和指派系統管理員角色

您現在可以查看和管理 Azure Active Directory 入口網站中的所有系統管理員角色成員。 如果您經常管理角色指派，則可能會偏好這項體驗。 而且，如果您曾經想過「這些角色的實際作用到底為何？」，則可以查看每個 Azure AD 系統管理員角色的詳細權限清單。

## <a name="view-all-roles"></a>檢視所有角色

搜尋並選取 [Azure Active Directory]  。 選取 [**角色和系統管理員**] 以查看所有可用角色的清單。

按一下每個資料列右側的省略符號，以開啟角色的詳細描述。

![Azure AD 入口網站中的角色清單](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>檢視我的角色

您也可以輕鬆檢視自己的權限。 在 [角色與系統管理員]**** 頁面中選取 [您的角色]****，即可查看目前已指派給您的角色。

## <a name="view-assignments-for-a-role"></a>檢視角色指派

按一下角色即可檢視指派給該角色的使用者。 您可以選取 [Manage in PIM] \(在 PIM 中管理\)****，以取得額外的管理功能。 特殊權限角色管理員可以將「永久」(一律在角色中為作用中) 指派變更為「合格」(只有在提高權限時才在角色中)。 如果您沒有 PIM，則仍然可以選取 [Manage in PIM] \(在 PIM 中管理\)**** 來註冊試用版。 Privileged Identity Management 需要 [Azure AD Premium P2 授權方案](../privileged-identity-management/subscription-requirements.md)。

![管理員角色成員清單](./media/directory-manage-roles-portal/member-list.png)

如果您是全域管理員或特殊權限角色管理員，則可以輕鬆地新增或移除成員、篩選清單，或選取成員以查看其作用中獲指派角色。

## <a name="view-a-users-role-permissions"></a>檢視使用者的角色權限

當您檢視角色的成員時，請選取 [描述]**** 以查看角色指派所授與權限的完整清單。 此頁面包括相關文件的連結，協助引導您管理目錄角色。

![管理員角色權限清單](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>後續步驟

* 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
* 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](directory-assign-admin-roles.md)。
* 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
