---
title: 在 PIM 中設定特殊許可權存取群組設定-Azure Active Directory |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management (PIM) 中設定角色可指派的群組設定。
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
ms.openlocfilehash: abb7f93437cd45914d3824e9f557241ba0d71162
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835385"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中設定 (preview) 的特殊許可權存取群組設定

角色設定是 Privileged Identity Management (PIM) 中套用至群組擁有者和群組成員特殊許可權存取指派的預設設定。 使用下列步驟來設定核准工作流程，以指定誰可以核准或拒絕提高許可權的要求。

## <a name="open-role-settings"></a>開啟角色設定

請遵循下列步驟來開啟 Azure 特殊許可權存取群組角色的設定。

1. 使用具有特殊[許可權角色管理員](../roles/permissions-reference.md#privileged-role-administrator)角色的使用者登入[Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [特殊 **許可權存取] (預覽)**。

1. 選取您要管理的群組。

    ![依組名篩選的特殊許可權存取群組](./media/groups-role-settings/group-select.png)

1. 選取 [設定]  。

    ![[設定] 頁面，其中列出所選群組的群組設定](./media/groups-role-settings/group-settings-select-role.png)

1. 選取您要查看或變更其設定的擁有者或成員角色。 您可以在 [ **角色設定詳細資料** ] 頁面中，查看角色的目前設定。

    ![列出數個指派和啟用設定的角色設定詳細資料頁面](./media/groups-role-settings/group-role-setting-details.png)

1. 選取 [ **編輯** ] 以開啟 [ **編輯角色設定** ] 頁面。 [ **啟動] 索引** 標籤可讓您變更角色啟用設定，包括是否允許永久合格和有效的指派。

    ![開啟啟用索引標籤的 [編輯角色設定] 頁面](./media/groups-role-settings/role-settings-activation-tab.png)

1. 選取 [ **指派** ] 索引標籤，以開啟 [指派設定] 索引標籤。這些設定會控制此角色的 Privileged Identity Management 指派設定。

    ![角色設定頁面中的 [角色指派] 索引標籤](./media/groups-role-settings/role-settings-assignment-tab.png)

1. 使用頁面底部的 [ **通知** ] 索引標籤或 [ **下一步：啟用** ] 按鈕，前往此角色的 [通知設定] 索引標籤。 這些設定會控制與此角色相關的所有電子郵件通知。

    ![角色設定頁面中的 [角色通知] 索引標籤](./media/groups-role-settings/role-settings-notification-tab.png)

1. 隨時選取 [ **更新** ] 按鈕以更新角色設定。

在 [角色設定] 頁面的 [ **通知** ] 索引標籤中，Privileged Identity Management 可讓您更精確地控制接收通知的人員，以及他們所收到的通知。

- **關閉電子郵件**<br>您可以藉由清除 [預設收件者] 核取方塊並刪除其他任何收件者，關閉特定的電子郵件。  
- **將電子郵件限制為指定的電子郵件地址**<br>您可以藉由清除 [預設收件者] 核取方塊，關閉傳送給預設收件者的電子郵件。 然後，您可以將其他電子郵件地址新增為其他收件者。 如果您想要新增一個以上的電子郵件地址，請使用分號 (; ) 。
- **傳送電子郵件給預設收件者和其他收件者**<br>您可以藉由選取 [預設收件者] 核取方塊，並新增其他收件者的電子郵件地址，將電子郵件傳送給預設收件者和
- **僅限重要電子郵件**<br>針對每一種類型的電子郵件，您可以選取核取方塊，只接收重要的電子郵件。 這表示 Privileged Identity Management 只有在電子郵件需要立即採取行動時，才會繼續將電子郵件傳送給已設定的收件者。 例如，將會觸發要求系統管理員核准擴充要求的電子郵件，而不會觸發要求使用者延伸其角色指派的電子郵件。

## <a name="assignment-duration"></a>指派持續時間

當您進行角色的設定時，每個指派類型 (合格和有效) 都有兩個指派持續時間選項可選擇。 當使用者指派給 Privileged Identity Management 中的角色時，這些選項會變成預設的最長持續時間。

您可以選擇下列其中一個 **合格** 的指派持續時間選項：

| | |
| --- | --- |
| **允許永久合格的指派** | 資源管理員可以指派永久合格的指派。 |
| **合格指派的到期時間** | 資源管理員可以要求所有合格指派有指定的開始和結束日期。 |

此外，您可以從下列 **有效** 指派持續時間選項中選擇一個：

| | |
| --- | --- |
| **允許永久有效的指派** | 資源管理員可以指派永久主動指派。 |
| **有效指派的到期時間** | 資源管理員可以要求所有有效指派有指定的開始和結束日期。 |

> [!NOTE]
> 資源管理員可以更新所有具有指定結束日期的指派。 此外，使用者也可以起始自助要求來 [延長或更新角色指派](pim-resource-roles-renew-extend.md)。

## <a name="require-multi-factor-authentication"></a>需要多重要素驗證

Privileged Identity Management 針對兩個不同的案例提供選擇性的 Azure AD Multi-Factor Authentication 強制執行。

### <a name="require-multi-factor-authentication-on-active-assignment"></a>要求為有效指派進行 Multi-Factor Authentication

在某些情況下，您可能會想要將使用者或群組指派給某個角色， (一天的時間，例如) 。 在此情況下，指派的使用者不需要要求啟用。 在此案例中，當使用者使用其角色指派時，Privileged Identity Management 無法強制執行多重要素驗證，因為在角色指派的時間內，角色已在使用中。

若要確保履行指派的資源系統管理員是他們所聲稱的身分，您可以勾選 [在使用中 **指派的要求 Multi-Factor Authentication** ] 核取方塊，對作用中的指派強制執行多重要素驗證。

### <a name="require-multi-factor-authentication-on-activation"></a>啟用時需要 Multi-Factor Authentication

您可以要求符合角色資格的使用者，證明他們使用 Azure AD Multi-Factor Authentication 的使用者可以啟動。 多重要素驗證可確保使用者是他們聲稱有合理確定性的人。 在使用者帳戶可能受到危害的情況下，強制執行這個選項可保護重要資源。

若要在啟用之前要求多重要素驗證，請核取 [ **啟用時需要 Multi-Factor Authentication** ] 方塊。

如需詳細資訊，請參閱[多重要素驗證和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="activation-maximum-duration"></a>啟用持續時間上限

使用 [啟用持續時間上限] 滑桿，可設定角色在到期前維持作用中狀態的最長時間 (以小時為單位)。 此值可介於1到24小時之間。

## <a name="require-justification"></a>需要理由

您可以要求使用者在啟用時輸入業務理由。 若要要求提供理由，請核取 [進行有效指派時需要提供理由] 方塊，或核取 [啟用時需要提供理由] 方塊。

## <a name="require-approval-to-activate"></a>需要核准才可啟用

如果您想要在啟用角色前先經過核准，請遵循下列步驟。

1. 請核取 [需要核准才可啟用] 核取方塊。

1. 選取 [ **選取核准者** ] 以開啟 [ **選取成員或群組** ] 頁面。

    ![選取使用者或群組窗格以選取核准者](./media/groups-role-settings/group-settings-select-approvers.png)

1. 至少選取一個使用者或群組，然後按一下 [ **選取**]。 您可以新增任何使用者和群組的組合。 您必須至少選取一個核准者。 沒有任何預設核准者。

    您的選項將出現在所選取的核准者清單中。

1. 在您指定所有角色設定後，請選取 [ **更新** ] 以儲存您的變更。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中指派特殊許可權存取群組成員資格或擁有權](groups-assign-member-owner.md)
