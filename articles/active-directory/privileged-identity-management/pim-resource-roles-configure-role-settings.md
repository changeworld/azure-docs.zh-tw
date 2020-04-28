---
title: 在 PIM 中設定 Azure 資源角色設定-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中設定 Azure 資源角色設定。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75638675"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>在 Privileged Identity Management 中設定 Azure 資源角色設定

當您設定 Azure 資源角色設定時，您會在 Azure Active Directory （Azure AD） Privileged Identity Management （PIM）中定義套用至 Azure 資源角色指派的預設設定。 請使用下列程序來設定核准工作流程，並指定可以核准或拒絕要求的人員。

## <a name="open-role-settings"></a>開啟角色設定

請遵循下列步驟來開啟 Azure 資源角色的設定。

1. 以特殊[許可權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色中的使用者身分登入[Azure 入口網站](https://portal.azure.com/)。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 [Azure 資源]****。

1. 選取您想要管理的資源，例如訂用帳戶或管理群組。

    ![列出可管理資源的 Azure 資源頁面](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. 選取 [**角色設定**]。

    ![列出 Azure 資源角色的 [角色設定] 頁面](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 選取您要設定其設定的角色。

    ![列出數個指派和啟用設定的角色設定詳細資料頁面](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. 選取 [**編輯**] 以開啟 [**角色設定**] 窗格。 第一個索引標籤可讓您更新 Privileged Identity Management 中角色啟用的設定。

    ![開啟啟用索引標籤的 [編輯角色設定] 頁面](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. 選取頁面底部的 [**指派**] 索引標籤或 [**下一步：指派**] 按鈕，以開啟 [指派設定] 索引標籤。這些設定會控制在 Privileged Identity Management 介面內進行的角色指派。

    ![[角色設定] 頁面中的 [角色指派] 索引標籤](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. 使用頁面底部的 [**通知**] 索引標籤或 [**下一步：啟用**] 按鈕，以前往此角色的 [通知設定] 索引標籤。 這些設定會控制與此角色相關的所有電子郵件通知。

    ![[角色設定] 頁面中的 [角色通知] 索引標籤](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    在 [角色設定] 頁面的 [**通知**] 索引標籤中，Privileged Identity Management 可讓您更精確地控制接收通知的物件，以及他們收到的通知。

    - **關閉電子郵件**<br>您可以藉由清除 [預設收件者] 核取方塊，並刪除任何其他收件者，關閉特定的電子郵件。  

    - **將電子郵件限制為指定的電子郵件地址**<br>您可以藉由清除 [預設收件者] 核取方塊，關閉傳送給預設收件者的電子郵件。 接著，您可以新增其他電子郵件地址作為其他收件者。 如果您想要新增一個以上的電子郵件地址，請使用分號分隔（;)。

    - **傳送電子郵件給預設收件者和其他收件者**<br>您可以選取 [預設收件者] 核取方塊，並新增其他收件者的電子郵件地址，以將電子郵件傳送給預設收件者和其他

    - **僅限重要電子郵件**<br>針對每種類型的電子郵件，您可以選取核取方塊，只接收重要的電子郵件。 這表示 Privileged Identity Management 只有在電子郵件需要立即採取行動時，才會繼續傳送電子郵件給設定的收件者。 例如，要求使用者擴充其角色指派的電子郵件將不會觸發，而需要系統管理員核准延伸模組要求的電子郵件將會觸發。

1. 隨時選取 [**更新**] 按鈕以更新角色設定。

## <a name="assignment-duration"></a>指派持續時間

當您進行角色的設定時，每個指派類型 (合格和有效) 都有兩個指派持續時間選項可選擇。 在 Privileged Identity Management 中將使用者指派給角色時，這些選項會變成預設的最大持續時間。

您可以選擇下列其中一個**合格**的指派持續時間選項：

| | |
| --- | --- |
| **允許永久合格的指派** | 資源管理員可以指派永久的合格指派。 |
| **合格指派的到期時間** | 資源管理員可以要求所有合格指派有指定的開始和結束日期。 |

此外，您可以從下列**有效**指派持續時間選項中選擇一個：

| | |
| --- | --- |
| **允許永久有效的指派** | 資源管理員可以指派永久的主動指派。 |
| **有效指派的到期時間** | 資源管理員可以要求所有有效指派有指定的開始和結束日期。 |

> [!NOTE]
> 資源管理員可以更新所有具有指定結束日期的指派。 此外，使用者也可以起始自助要求，以[延長或更新角色指派](pim-resource-roles-renew-extend.md)。

## <a name="require-multi-factor-authentication"></a>需要多重要素驗證

Privileged Identity Management 可針對兩個不同的案例選擇性地強制執行 Azure Multi-Factor Authentication。

### <a name="require-multi-factor-authentication-on-active-assignment"></a>要求為有效指派進行 Multi-Factor Authentication

在某些情況下，您可能會想要在短時間內將使用者或群組指派給某個角色（例如一天）。 在此情況下，指派的使用者不需要要求啟用。 在此案例中，當使用者使用其角色指派時，Privileged Identity Management 無法強制執行多重要素驗證，因為它們已在角色中從指派的時間開始生效。

若要確保資源管理員履行指派，就是他們所說的是誰，您可以勾選 [在作用中**指派時需要多重要素驗證**] 方塊，對作用中的指派強制執行多重要素驗證。

### <a name="require-multi-factor-authentication-on-activation"></a>啟用時需要 Multi-Factor Authentication

您可以要求具有角色資格的使用者，證明他們使用 Azure 多重要素驗證的物件，然後才能啟用。 多重要素驗證可確保使用者的身分為合理的確定性。 在使用者帳戶可能受到危害的情況下，強制執行這個選項可保護重要資源。

若要在啟用前要求多重要素驗證，請核取 [**啟用時需要多重要素驗證**] 方塊。

如需詳細資訊，請參閱[多重要素驗證和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="activation-maximum-duration"></a>啟用持續時間上限

使用 [啟用持續時間上限]**** 滑桿，可設定角色在到期前維持作用中狀態的最長時間 (以小時為單位)。 此值可以是1到24小時。

## <a name="require-justification"></a>需要理由

您可以要求使用者在啟動時輸入業務理由。 若要要求提供理由，請核取 [進行有效指派時需要提供理由]**** 方塊，或核取 [啟用時需要提供理由]**** 方塊。

## <a name="require-approval-to-activate"></a>需要核准才可啟用

如果您想要在啟用角色前先經過核准，請遵循下列步驟。

1. 請核取 [需要核准才可啟用]**** 核取方塊。

1. 選取 [**選取核准者**] 以開啟 [**選取成員或群組**] 頁面。

    ![選取使用者或群組窗格以選取核准者](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 至少選取一個使用者或群組，然後按一下 [**選取**]。 您可以新增任何使用者和群組的組合。 您必須至少選取一個核准者。 沒有任何預設核准者。

    您的選項將出現在所選取的核准者清單中。

1. 當您指定所有角色設定後，請選取 [**更新**] 以儲存變更。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
- [在 Privileged Identity Management 中設定 Azure 資源角色的安全性警示](pim-resource-roles-configure-alerts.md)
