---
title: 開始使用 PIM - Azure 活動目錄 |微軟文檔
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
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472855"
---
# <a name="start-using-privileged-identity-management"></a>開始使用 Privileged Identity Management

使用特權標識管理 （PIM），您可以在 Azure 活動目錄 （Azure AD） 組織中管理、控制和監視訪問。 此範圍包括訪問 Azure 資源、Azure AD 和其他 Microsoft 線上服務（如 Office 365 或 Microsoft Intune）。

本文介紹如何使用特權標識管理啟用並開始使用特權標識管理。

## <a name="prerequisites"></a>Prerequisites

要使用特權標識管理，您必須具有以下許可證之一：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

有關詳細資訊，請參閱[使用特權標識管理的許可證要求](subscription-requirements.md)。

## <a name="sign-up-pim-for-azure-ad-roles"></a>針對 Azure AD 角色註冊 PIM

為目錄啟用特權標識管理後，需要註冊特權標識管理來管理 Azure AD 角色。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

    ![註冊 Azure AD 角色的特權標識管理](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. 選擇 **"註冊**"。

1. 在顯示的郵件中，按一下"**是**"以註冊特權標識管理以管理 Azure AD 角色。

    ![註冊 Azure AD 角色消息的特權標識管理](./media/pim-getting-started/sign-up-pim-message.png)

    完成註冊時，將會啟用 Azure AD 選項。 您可能需要重新整理入口網站。

    有關如何發現和選擇 Azure 資源以使用特權標識管理進行保護的資訊，請參閱[在特權標識管理中發現要管理的 Azure 資源](pim-resource-roles-discover-resources.md)。

## <a name="navigate-to-your-tasks"></a>瀏覽至您的工作

設置特權標識管理後，即可啟動身份管理工作。

![特權標識管理中的導航視窗，顯示任務和管理選項](./media/pim-getting-started/pim-quickstart-tasks.png)

| 工作 + 管理 | 描述 |
| --- | --- |
| **我的角色**  | 顯示一個清單，列出已指派給您的合格和使用中角色。 您可以在這裡啟動任何指派的合格角色。 |
| **我的要求** | 顯示您啟動合格角色指派的任何擱置要求。 |
| **核准要求** | 顯示一個清單，列出您目錄中的使用者為了啟動合格角色所提出的要求，而此目錄是指定給您以進行核准的目錄。 |
| **檢閱存取** | 列出指派給您完成的使用中存取權檢閱 (無論您是在檢閱自己還是他人的存取權)。 |
| **Azure AD 角色** | 顯示用於管理 Azure AD 角色指派的特權角色管理員的儀表板和設置。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的檢視] 儀表板只會顯示存取儀表板的使用者，而非整個租用戶的相關資訊。 |
| **Azure 資源** | 顯示儀表板和設定，讓特殊權限角色管理員可管理 Azure 資源角色指派。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的檢視] 儀表板只會顯示存取儀表板的使用者，而非整個租用戶的相關資訊。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>將 PIM 圖格新增至儀表板

為了更輕鬆地打開特權標識管理，請向 Azure 門戶儀表板添加特權標識管理磁貼。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 選擇**所有服務**並查找**Azure AD 特權標識管理**服務。

    ![所有服務中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 選擇特權身份管理快速入門。

1. 選中**儀表板的"固定"邊欄選項卡**，將特權標識管理快速入門邊欄選項卡固定到儀表板。

    ![將特權標識管理邊欄選項卡固定到儀表板的圖釘圖示](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 儀表板上，您會看到像這樣的圖格：

    ![儀表板上的特權身份管理快速入門磁貼](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [發現要在特權標識管理中管理的 Azure 資源](pim-resource-roles-discover-resources.md)
