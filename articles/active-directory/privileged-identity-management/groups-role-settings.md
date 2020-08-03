---
title: 在 PIM 中設定特殊許可權存取群組設定-Azure Active Directory |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中設定可指派角色的群組設定。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c31897e5dc50815d692be83af5d03ffe58c216c1
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505935"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中設定特殊許可權存取群組設定（預覽）

角色設定是在 Privileged Identity Management （PIM）中套用至群組擁有者和群組成員特殊許可權存取指派的預設設定。 使用下列步驟來設定核准工作流程，以指定誰可以核准或拒絕要求提升許可權。

## <a name="open-role-settings"></a>開啟角色設定

請遵循下列步驟來開啟 Azure 特殊許可權存取群組角色的設定。

1. 以特殊[許可權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色中的使用者身分登入[Azure 入口網站](https://portal.azure.com/)。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 **[特殊許可權存取（預覽）**]。

1. 選取您要管理的群組。

    ![依組名篩選的特殊許可權存取群組](./media/groups-role-settings/group-select.png)

1. 選取 [設定]。

    ![列出所選群組之群組設定的設定頁面](./media/groups-role-settings/group-settings-select-role.png)

1. 選取您想要查看或變更其設定的擁有者或成員角色。 您可以在 [**角色設定詳細資料**] 頁面中，查看角色的目前設定。

    ![列出數個指派和啟用設定的角色設定詳細資料頁面](./media/groups-role-settings/group-role-setting-details.png)

1. 選取 [**編輯**] 以開啟 [**編輯角色設定**] 頁面。 [**啟用**] 索引標籤可讓您變更角色啟用設定，包括是否允許永久合格和作用中的指派。

    ![開啟啟用索引標籤的 [編輯角色設定] 頁面](./media/groups-role-settings/role-settings-activation-tab.png)

1. 選取 [**指派**] 索引標籤，以開啟 [指派設定] 索引標籤。這些設定會控制此角色的 Privileged Identity Management 指派設定。

    ![[角色設定] 頁面中的 [角色指派] 索引標籤](./media/groups-role-settings/role-settings-assignment-tab.png)

1. 使用頁面底部的 [**通知**] 索引標籤或 [**下一步：啟用**] 按鈕，以前往此角色的 [通知設定] 索引標籤。 這些設定會控制與此角色相關的所有電子郵件通知。

    ![[角色設定] 頁面中的 [角色通知] 索引標籤](./media/groups-role-settings/role-settings-notification-tab.png)

1. 隨時選取 [**更新**] 按鈕以更新角色設定。

在 [角色設定] 頁面的 [**通知**] 索引標籤中，Privileged Identity Management 可讓您更精確地控制接收通知的物件，以及他們收到的通知。

- **關閉電子郵件**<br>您可以藉由清除 [預設收件者] 核取方塊，並刪除任何其他收件者，關閉特定的電子郵件。  
- **將電子郵件限制為指定的電子郵件地址**<br>您可以藉由清除 [預設收件者] 核取方塊，關閉傳送給預設收件者的電子郵件。 接著，您可以新增其他電子郵件地址作為其他收件者。 如果您想要新增一個以上的電子郵件地址，請使用分號分隔（;)。
- **傳送電子郵件給預設收件者和其他收件者**<br>您可以選取 [預設收件者] 核取方塊，並新增其他收件者的電子郵件地址，以將電子郵件傳送給預設收件者和其他
- **僅限重要電子郵件**<br>針對每種類型的電子郵件，您可以選取核取方塊，只接收重要的電子郵件。 這表示 Privileged Identity Management 只有在電子郵件需要立即採取行動時，才會繼續傳送電子郵件給設定的收件者。 例如，要求使用者擴充其角色指派的電子郵件將不會觸發，而需要系統管理員核准延伸模組要求的電子郵件將會觸發。

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

    ![選取使用者或群組窗格以選取核准者](./media/groups-role-settings/group-settings-select-approvers.png)

1. 至少選取一個使用者或群組，然後按一下 [**選取**]。 您可以新增任何使用者和群組的組合。 您必須至少選取一個核准者。 沒有任何預設核准者。

    您的選項將出現在所選取的核准者清單中。

1. 當您指定所有角色設定後，請選取 [**更新**] 以儲存變更。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中指派特殊許可權存取群組成員資格或擁有權](groups-assign-member-owner.md)
