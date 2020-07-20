---
title: 查看和指派系統管理員角色許可權-Azure AD |Microsoft Docs
description: 您現在可以查看和管理入口網站中的 Azure AD 系統管理員角色成員。 針對經常管理角色指派的人員。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e067e8d56f8a928f952648fc76cd5d6b7a1afe7
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86221243"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中檢視和指派系統管理員角色

您現在可以查看和管理 Azure Active Directory 入口網站中的所有系統管理員角色成員。 如果您經常管理角色指派，則可能會偏好這項體驗。 而且，如果您曾經想過「這些角色的實際作用到底為何？」，則可以查看每個 Azure AD 系統管理員角色的詳細權限清單。

## <a name="view-all-roles"></a>檢視所有角色

1. 登入[Azure 入口網站](https://portal.azure.com)並選取 [ **Azure Active Directory**]。

1. 選取 [**角色和系統管理員**] 以查看所有可用角色的清單。

1. 選取每個資料列右側的省略號，以查看角色的許可權。 選取角色以查看指派給角色的使用者。 如果您看到與下圖不同的內容，請閱讀[許可權角色的 View 指派](#view-assignments-for-privileged-roles)中的附注，以確認您是否位於 PRIVILEGED IDENTITY MANAGEMENT (PIM) 。

    ![Azure AD 入口網站中的角色清單](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>檢視我的角色

您也可以輕鬆檢視自己的權限。 在 [角色與系統管理員]**** 頁面中選取 [您的角色]****，即可查看目前已指派給您的角色。

## <a name="view-assignments-for-privileged-roles"></a>許可權角色的視圖指派

您可以選取 [Manage in PIM] \(在 PIM 中管理\)****，以取得額外的管理功能。 特殊權限角色管理員可以將「永久」(一律在角色中為作用中) 指派變更為「合格」(只有在提高權限時才在角色中)。 如果您沒有 Privileged Identity Management，您仍然可以選取 [**在 PIM 中管理**] 以註冊試用版。 Privileged Identity Management 需要 [Azure AD Premium P2 授權方案](../privileged-identity-management/subscription-requirements.md)。

![管理員角色成員清單](./media/directory-manage-roles-portal/member-list.png)

如果您是全域管理員或特殊權限角色管理員，則可以輕鬆地新增或移除成員、篩選清單，或選取成員以查看其作用中獲指派角色。

> [!Note]
> 如果您有 Azure AD premium P2 授權，而且您已經使用 Privileged Identity Management，則所有角色管理工作都是在許可權身分識別管理中執行，而不是在 Azure AD 中執行。
>
> ![在 PIM 中為已經使用 PIM 並具有 Premium P2 授權的使用者，Azure AD 管理的角色](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>檢視使用者的角色權限

當您檢視角色的成員時，請選取 [描述]**** 以查看角色指派所授與權限的完整清單。 此頁面包括相關文件的連結，協助引導您管理目錄角色。

![管理員角色權限清單](./media/directory-manage-roles-portal/role-description.png)

## <a name="assign-a-role"></a>指派角色

1. 以全域管理員或特殊許可權角色管理員許可權登入[Azure 入口網站](https://portal.azure.com)，然後選取 [ **Azure Active Directory**]。

1. 選取 [**角色和系統管理員**] 以查看所有可用角色的清單。

1. 選取要查看其指派的角色。

    ![管理員角色權限清單](./media/directory-manage-roles-portal/member-list.png)

1. 選取 [**新增指派**]，然後選取您想要指派的角色。 您可以選取 [Manage in PIM] \(在 PIM 中管理\)****，以取得額外的管理功能。 如果您看到與下圖不同的內容，請閱讀[許可權角色的 View 指派](#view-assignments-for-privileged-roles)中的附注，以確認您是否在 PIM 中。

    ![管理員角色權限清單](./media/directory-manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>後續步驟

* 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
* 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](directory-assign-admin-roles.md)。
* 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
