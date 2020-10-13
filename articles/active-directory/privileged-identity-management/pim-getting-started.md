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
ms.topic: how-to
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bcfb21ab15355653780355f1b5e459bc806ec8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600716"
---
# <a name="start-using-privileged-identity-management"></a>開始使用 Privileged Identity Management

本文說明如何啟用 Privileged Identity Management (PIM) 並開始使用。

使用 Privileged Identity Management (PIM) 來管理、控制和監視 Azure Active Directory (Azure AD) 組織內的存取權。 使用 PIM，您可以提供 Azure 資源的隨需和即時存取，Azure AD 資源和其他 Microsoft 線上服務，例如 Microsoft 365 或 Microsoft Intune。

## <a name="prerequisites"></a>必要條件

若要使用 Privileged Identity Management，您必須擁有下列其中一個授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

如需詳細資訊，請參閱 [使用 Privileged Identity Management 的授權需求](subscription-requirements.md)。

> [!Note]
> 當在具有高階 P2 授權的 Azure AD 組織中具有特殊許可權角色的使用者進入 Azure AD 的 **角色和系統管理員** ，並選取角色 (或甚至只是造訪 Privileged Identity Management) 時：
>
> - 我們會自動為組織啟用 PIM
> - 他們的經驗現在可以指派「一般」角色指派或符合資格的角色指派
>
> 當 PIM 啟用時，不會對您的組織有任何其他需要擔心的影響。 它會提供您其他指派選項，例如主動式 vs 符合開始和結束時間。 PIM 也可讓您使用管理單位和自訂角色來定義角色指派的範圍。 如果您是全域管理員或特殊許可權角色管理員，您可能會開始取得一些額外的電子郵件，例如 PIM 的每週摘要。 您也可能會在與角色指派相關的 audit 記錄檔中看到 MS PIM 服務主體。 這是預期的變更，不會影響您的工作流程。

## <a name="prepare-pim-for-azure-ad-roles"></a>準備 Azure AD 角色的 PIM

以下是我們建議您準備 Privileged Identity Management 以管理 Azure AD 角色的工作：

1. [設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)。
1. [提供合格的指派](pim-how-to-add-role-to-user.md)。
1. [允許合格的使用者立即啟動其 Azure AD 角色](pim-how-to-activate-role.md)。

## <a name="prepare-pim-for-azure-roles"></a>準備適用于 Azure 角色的 PIM

以下是我們建議您準備 Privileged Identity Management 來管理訂用帳戶之 Azure 角色的工作：

1. [探索 Azure 資源](pim-resource-roles-discover-resources.md)
1. [設定 Azure 角色設定](pim-resource-roles-configure-role-settings.md)。
1. [提供合格的指派](pim-resource-roles-assign-roles.md)。
1. [允許合格的使用者立即啟動其 Azure 角色](pim-resource-roles-activate-your-roles.md)。

## <a name="navigate-to-your-tasks"></a>瀏覽至您的工作

Privileged Identity Management 設定好之後，您就可以瞭解您的做法。

![Privileged Identity Management 中顯示工作和管理選項的導覽視窗](./media/pim-getting-started/pim-quickstart-tasks.png)

| 工作 + 管理 | 描述 |
| --- | --- |
| **我的角色**  | 顯示一個清單，列出已指派給您的合格和使用中角色。 您可以在這裡啟動任何指派的合格角色。 |
| **我的要求** | 顯示您啟動合格角色指派的任何擱置要求。 |
| **核准要求** | 顯示一個清單，列出您目錄中的使用者為了啟動合格角色所提出的要求，而此目錄是指定給您以進行核准的目錄。 |
| **檢閱存取** | 列出指派給您完成的使用中存取權檢閱 (無論您是在檢閱自己還是他人的存取權)。 |
| **Azure AD 角色** | 顯示特殊許可權角色管理員的儀表板和設定，以管理 Azure AD 角色指派。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的視圖] 儀表板只會顯示存取儀表板之使用者的相關資訊，而不是整個組織的相關資訊。 |
| **Azure 資源** | 顯示特殊許可權角色系統管理員的儀表板和設定，以管理 Azure 資源角色指派。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的視圖] 儀表板只會顯示存取儀表板之使用者的相關資訊，而不是整個組織的相關資訊。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>將 PIM 圖格新增至儀表板

為了讓您更輕鬆地開啟 Privileged Identity Management，請將 PIM 磚新增至您的 Azure 入口網站儀表板。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 [ **所有服務** ]，並尋找 **Azure AD Privileged Identity Management** 服務。

    ![所有服務中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 選取 Privileged Identity Management **快速入門**]。

1. 選取 [ **將分頁釘選到儀表板** ]，將 Privileged Identity Management **快速入門** 頁面釘選到儀表板。

    ![將 Privileged Identity Management 頁面釘選到儀表板的圖釘圖示](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 儀表板上，您會看到像這樣的圖格：

    ![儀表板上的 Privileged Identity Management 快速入門磚](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中管理 Azure 資源存取](pim-resource-roles-discover-resources.md)
