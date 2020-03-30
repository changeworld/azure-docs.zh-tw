---
title: 在 PIM 中配置 Azure 資源角色設置 - Azure AD |微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638675"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>在特權標識管理中配置 Azure 資源角色設置

配置 Azure 資源角色設置時，可以定義應用於 Azure 活動目錄 （Azure AD） 特權標識管理 （PIM） 中的 Azure 資源角色指派的預設設置。 請使用下列程序來設定核准工作流程，並指定可以核准或拒絕要求的人員。

## <a name="open-role-settings"></a>開啟角色設定

請遵循下列步驟來開啟 Azure 資源角色的設定。

1. 在[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色角色中，使用使用者登錄到[Azure 門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 選取 [Azure 資源]****。

1. 選擇要管理的資源，如訂閱或管理組。

    ![Azure 資源頁列出可管理的資源](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. 選擇**角色設置**。

    ![列出 Azure 資源角色的角色設置頁](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 選擇要配置其設置的角色。

    ![角色設置詳細資訊頁面，列出多個分配和啟動設置](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. 選擇 **"編輯"** 以打開**角色設置**窗格。 第一個選項卡允許您更新配置以在特權標識管理中啟動角色。

    ![打開"啟動"選項卡時編輯角色設置頁面](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. 選擇頁面底部的 **"分配"** 選項卡或"**下一步：分配"** 按鈕以打開分配設置選項卡。這些設置控制在特權標識管理介面中執行的角色指派。

    ![角色設置頁中的角色指派選項卡](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. 使用"**通知**"選項卡或頁面底部的 **"下一步：啟動"** 按鈕訪問此角色的通知設置選項卡。 這些設置控制與此角色相關的所有電子郵件通知。

    ![角色設置頁中的角色通知選項卡](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    在角色設置頁上的 **"通知"** 選項卡中，"特權標識管理"支援對接收通知的人員以及他們接收的通知進行精細控制。

    - **關閉電子郵件**<br>您可以通過清除預設收件者核取方塊並刪除任何其他收件者來關閉特定電子郵件。  

    - **將電子郵件限制為指定的電子郵件地址**<br>您可以通過清除預設收件者核取方塊來關閉發送給預設收件者的電子郵件。 然後，您可以將其他電子郵件地址添加為其他收件者。 如果要添加多個電子郵件地址，請使用分號分隔它們（;)。

    - **向預設收件者和其他收件者發送電子郵件**<br>您可以通過選擇預設收件者核取方塊和為其他收件者添加電子郵件地址，將電子郵件發送給預設收件者和其他收件者。

    - **僅關鍵電子郵件**<br>對於每種類型的電子郵件，您可以選擇僅接收關鍵電子郵件的核取方塊。 這意味著，僅當電子郵件需要立即採取行動時，特權標識管理才會繼續向配置的收件者發送電子郵件。 例如，請求使用者擴展其角色指派的電子郵件不會觸發，同時將觸發要求管理員批准擴展請求的電子郵件。

1. 隨時選擇 **"更新**"按鈕以更新角色設置。

## <a name="assignment-duration"></a>指派持續時間

當您進行角色的設定時，每個指派類型 (合格和有效) 都有兩個指派持續時間選項可選擇。 當使用者分配到特權標識管理中的角色時，這些選項將成為預設最長持續時間。

您可以選擇以下**符合條件的**分配持續時間選項之一：

| | |
| --- | --- |
| **允許永久合格分配** | 資源管理員可以分配永久符合條件的分配。 |
| **過期合格分配後** | 資源管理員可以要求所有合格指派有指定的開始和結束日期。 |

此外，您可以從下列**有效**指派持續時間選項中選擇一個：

| | |
| --- | --- |
| **允許永久活動分配** | 資源管理員可以分配永久活動分配。 |
| **過期活動分配後** | 資源管理員可以要求所有有效指派有指定的開始和結束日期。 |

> [!NOTE]
> 資源管理員可以更新所有具有指定結束日期的指派。 此外，使用者可以啟動自助服務請求以[擴展或續訂角色指派](pim-resource-roles-renew-extend.md)。

## <a name="require-multi-factor-authentication"></a>需要多重要素驗證

Privileged Identity Management 可針對兩個不同的案例選擇性地強制執行 Azure Multi-Factor Authentication。

### <a name="require-multi-factor-authentication-on-active-assignment"></a>要求為有效指派進行 Multi-Factor Authentication

在某些情況下，您可能希望將使用者或組分配給角色的持續時間很短（例如，一天）。 在這種情況下，分配的使用者不需要請求啟動。 在這種情況下，當使用者使用其角色指派時，特權標識管理無法強制實施多重要素驗證，因為他們從分配角色之時起就已在角色中處於活動狀態。

為了確保完成分配的資源管理員是他們說的，可以通過選中**活動賦值上的"多重要素驗證"** 框，對活動分配強制實施多重要素驗證。

### <a name="require-multi-factor-authentication-on-activation"></a>啟用時需要 Multi-Factor Authentication

您可以要求有資格擔任角色的使用者在啟動之前證明他們使用 Azure 多重要素驗證的人員。 多重要素驗證可確保使用者具有合理的確定性。 在使用者帳戶可能受到危害的情況下，強制執行這個選項可保護重要資源。

要在啟動之前需要多重要素驗證，請在**啟動時選中"需要多重要素驗證"** 框。

如需詳細資訊，請參閱[多重要素驗證和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="activation-maximum-duration"></a>啟用持續時間上限

使用 [啟用持續時間上限]**** 滑桿，可設定角色在到期前維持作用中狀態的最長時間 (以小時為單位)。 此值可以是 1 到 24 小時。

## <a name="require-justification"></a>需要理由

您可以要求使用者在啟動時輸入業務理由。 若要要求提供理由，請核取 [進行有效指派時需要提供理由]**** 方塊，或核取 [啟用時需要提供理由]**** 方塊。

## <a name="require-approval-to-activate"></a>需要核准才可啟用

如果您想要在啟用角色前先經過核准，請遵循下列步驟。

1. 請核取 [需要核准才可啟用]**** 核取方塊。

1. **選擇"選擇核准者**"以打開 **"選擇成員或組**"頁。

    ![選擇使用者或組窗格以選擇核准者](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 選擇至少一個使用者或組，然後按一下"**選擇**"。 您可以添加使用者和組的任何組合。 您必須至少選取一個核准者。 沒有任何預設核准者。

    您的選項將出現在所選取的核准者清單中。

1. 指定所有角色設置後，選擇 **"更新"** 以保存更改。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中分配 Azure 資源角色](pim-resource-roles-assign-roles.md)
- [在特權標識管理中為 Azure 資源角色配置安全警報](pim-resource-roles-configure-alerts.md)
