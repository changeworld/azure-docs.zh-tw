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
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733743f4680f3197a1754d87b4672641b2321907
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399336"
---
# <a name="start-using-privileged-identity-management"></a>開始使用 Privileged Identity Management

透過 Privileged Identity Management （PIM），您可以管理、控制及監視 Azure Active Directory （Azure AD）組織內的存取權。 此範圍包括存取 Azure 資源、Azure AD 及其他 Microsoft 線上服務，例如 Office 365 或 Microsoft Intune。

本文說明如何啟用和開始使用 Privileged Identity Management。

## <a name="prerequisites"></a>必要條件

若要使用 Privileged Identity Management，您必須具有下列其中一個授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

如需詳細資訊，請參閱[使用 Privileged Identity Management 的授權需求](subscription-requirements.md)。

## <a name="first-person-to-use-pim"></a>使用 PIM 的第一個人

如果您是在目錄中使用 Privileged Identity Management 的第一個人，系統會自動將目錄中的[安全性系統管理員](../users-groups-roles/directory-assign-admin-roles.md#security-administrator)和特殊[許可權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色指派給您。 只有特殊許可權角色管理員可以管理使用者的 Azure AD 角色指派。 此外，您也可以選擇執行[安全性精靈](pim-security-wizard.md)，讓其引導您完成初始的探索和指派體驗。

## <a name="enable-pim"></a>啟用 PIM

若要開始在您的目錄中使用 Privileged Identity Management，您必須先啟用 Privileged Identity Management。

1. 以目錄的全域系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。

    您必須是具有組織帳戶（例如 @yourdomain.com），而不是 Microsoft 帳戶（例如 @outlook.com）的全域管理員，才能啟用目錄的 Privileged Identity Management。

1. 按一下 [所有服務]，並尋找 [Azure AD Privileged Identity Management] 服務。

    ![所有服務中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 按一下以開啟 Privileged Identity Management 快速入門。

1. 在清單中，按一下 [同意 PIM]。

    ![同意啟用 Privileged Identity Management 的 Privileged Identity Management](./media/pim-getting-started/consent-pim.png)

1. 按一下 [驗證我的身分識別] 以使用 Azure MFA 驗證您的身分識別。 系統會要求您選取帳戶。

    ![選擇帳戶視窗來驗證您的身分識別](./media/pim-getting-started/pick-account.png)

1. 如果需要更多資訊才能完成驗證，系統將會引導您完成相關程序。 如需詳細資訊，請參閱[取得雙步驟驗證的說明](../user-help/multi-factor-authentication-end-user-troubleshoot.md)。

    ![如果您的組織需要詳細資訊，則需要更多資訊視窗](./media/pim-getting-started/more-information-required.png)

    例如，系統可能會要求您提供電話驗證。

    ![其他安全性驗證頁面，詢問如何聯絡您](./media/pim-getting-started/additional-security-verification.png)

1. 一旦您完成驗證程序後，請按一下 [同意] 按鈕。

1. 在出現的訊息中，按一下 **[是]** 以同意 Privileged Identity Management 服務。

    ![同意 Privileged Identity Management 訊息以完成同意程式](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>針對 Azure AD 角色註冊 PIM

啟用目錄的 Privileged Identity Management 之後，您必須註冊 Privileged Identity Management 以管理 Azure AD 角色。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]。

    ![註冊 Azure AD 角色的 Privileged Identity Management](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. 按一下 [註冊]。

1. 在出現的訊息中，按一下 [**是]** 以註冊 Privileged Identity Management 管理 Azure AD 角色。

    ![註冊 Azure AD 角色的 Privileged Identity Management 訊息](./media/pim-getting-started/sign-up-pim-message.png)

    完成註冊時，將會啟用 Azure AD 選項。 您可能需要重新整理入口網站。

    如需有關如何探索並選取要使用 Privileged Identity Management 保護的 Azure 資源的詳細資訊，請參閱[在 Privileged Identity Management 中探索要管理的 azure 資源](pim-resource-roles-discover-resources.md)。

## <a name="navigate-to-your-tasks"></a>瀏覽至您的工作

設定 Privileged Identity Management 之後，您就可以開始進行身分識別管理工作。

![Privileged Identity Management 中的導覽視窗，其中顯示工作和管理選項](./media/pim-getting-started/pim-quickstart-tasks.png)

| 工作 + 管理 | 描述 |
| --- | --- |
| **我的角色**  | 顯示一個清單，列出已指派給您的合格和使用中角色。 您可以在這裡啟動任何指派的合格角色。 |
| **我的要求** | 顯示您啟動合格角色指派的任何擱置要求。 |
| **核准要求** | 顯示一個清單，列出您目錄中的使用者為了啟動合格角色所提出的要求，而此目錄是指定給您以進行核准的目錄。 |
| **檢閱存取** | 列出指派給您完成的使用中存取權檢閱 (無論您是在檢閱自己還是他人的存取權)。 |
| **Azure AD 角色** | 顯示儀表板和設定，讓特殊許可權角色管理員管理 Azure AD 角色指派。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的檢視] 儀表板只會顯示存取儀表板的使用者，而非整個租用戶的相關資訊。 |
| **Azure 資源** | 顯示儀表板和設定，讓特殊權限角色管理員可管理 Azure 資源角色指派。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的檢視] 儀表板只會顯示存取儀表板的使用者，而非整個租用戶的相關資訊。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>將 PIM 圖格新增至儀表板

為了讓您更輕鬆地開啟 Privileged Identity Management，您應該將 Privileged Identity Management 圖格新增至您的 Azure 入口網站儀表板。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 按一下 [所有服務]，並尋找 [Azure AD Privileged Identity Management] 服務。

    ![所有服務中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 按一下以開啟 Privileged Identity Management 快速入門。

1. 勾選 [**將分頁釘選到儀表板**]，將 Privileged Identity Management 快速入門分頁釘選到儀表板。

    ![釘選圖示以將 Privileged Identity Management 的分頁釘選到儀表板](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 儀表板上，您會看到像這樣的圖格：

    ![儀表板上的 Privileged Identity Management 快速入門磚](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中探索要管理的 Azure 資源](pim-resource-roles-discover-resources.md)
