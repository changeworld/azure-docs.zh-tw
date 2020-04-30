---
title: 開始使用 PIM-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure 入口網站中啟用及開始使用 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f8d41fb3e6233d319725cac924c0c0ed41b7ce4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234293"
---
# <a name="start-using-privileged-identity-management"></a>開始使用 Privileged Identity Management

本文說明如何啟用 Privileged Identity Management （PIM）並開始使用。

使用 Privileged Identity Management （PIM）來管理、控制及監視您的 Azure Active Directory （Azure AD）組織內的存取權。 使用 PIM，您可以為 Azure 資源、Azure AD 資源及其他 Microsoft 線上服務（例如 Office 365 或 Microsoft Intune）提供必要和即時的存取權。

## <a name="prerequisites"></a>先決條件

若要使用 Privileged Identity Management，您必須具有下列其中一個授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

如需詳細資訊，請參閱[使用 Privileged Identity Management 的授權需求](subscription-requirements.md)。

## <a name="prepare-pim-for-azure-ad-roles"></a>為 Azure AD 角色準備 PIM

啟用目錄的 Privileged Identity Management 之後，您就可以準備 Privileged Identity Management 來管理 Azure AD 角色。

以下是我們建議您依序準備 Azure AD 角色的工作：

1. [設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)。
1. [提供合格的指派](pim-how-to-add-role-to-user.md)。
1. [讓符合資格的使用者能夠即時啟動其 Azure AD 角色](pim-how-to-activate-role.md)。

## <a name="prepare-pim-for-azure-roles"></a>準備適用于 Azure 角色的 PIM

啟用目錄的 Privileged Identity Management 之後，您就可以準備 Privileged Identity Management 來管理訂用帳戶上的 azure 資源存取權的 Azure 角色。

以下是我們建議您為 Azure 角色進行準備的工作，順序如下：

1. [探索 Azure 資源](pim-resource-roles-discover-resources.md)
1. [設定 Azure 角色設定](pim-resource-roles-configure-role-settings.md)。
1. [提供合格的指派](pim-resource-roles-assign-roles.md)。
1. [允許符合資格的使用者立即啟用其 Azure 角色](pim-resource-roles-activate-your-roles.md)。

## <a name="navigate-to-your-tasks"></a>瀏覽至您的工作

一旦設定 Privileged Identity Management 之後，您就可以瞭解自己的方式。

![Privileged Identity Management 中的導覽視窗，其中顯示工作和管理選項](./media/pim-getting-started/pim-quickstart-tasks.png)

| 工作 + 管理 | 描述 |
| --- | --- |
| **我的角色**  | 顯示一個清單，列出已指派給您的合格和使用中角色。 您可以在這裡啟動任何指派的合格角色。 |
| **我的要求** | 顯示您啟動合格角色指派的任何擱置要求。 |
| **核准要求** | 顯示一個清單，列出您目錄中的使用者為了啟動合格角色所提出的要求，而此目錄是指定給您以進行核准的目錄。 |
| **檢閱存取** | 列出指派給您完成的使用中存取權檢閱 (無論您是在檢閱自己還是他人的存取權)。 |
| **Azure AD 角色** | 顯示儀表板和設定，讓特殊許可權角色管理員管理 Azure AD 角色指派。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的視圖] 儀表板只會顯示存取儀表板的使用者相關資訊，而不是整個組織。 |
| **Azure 資源** | 顯示儀表板和設定，讓特殊許可權角色管理員可以管理 Azure 資源角色指派。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的視圖] 儀表板只會顯示存取儀表板的使用者相關資訊，而不是整個組織。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>將 PIM 圖格新增至儀表板

為了讓您更輕鬆地開啟 Privileged Identity Management，請將 PIM 磚新增至您的 Azure 入口網站儀表板。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 [**所有服務**]，然後尋找**Azure AD Privileged Identity Management**服務。

    ![所有服務中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 選取 Privileged Identity Management 快速入門。

1. 勾選 [**將分頁釘選到儀表板**]，將 Privileged Identity Management 快速入門分頁釘選到儀表板。

    ![釘選圖示以將 Privileged Identity Management 的分頁釘選到儀表板](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 儀表板上，您會看到像這樣的圖格：

    ![儀表板上的 Privileged Identity Management 快速入門磚](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [管理 Privileged Identity Management 中的 Azure 資源存取](pim-resource-roles-discover-resources.md)
