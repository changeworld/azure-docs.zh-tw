---
title: 在 PIM 中配置 Azure AD 角色設置 - Azure AD |微軟文檔
description: 瞭解如何在 Azure AD 特權標識管理 （PIM） 中配置 Azure AD 角色設置。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205019"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>在特權標識管理中配置 Azure AD 角色設置

特權角色管理員可以在其 Azure 活動目錄 （Azure AD） 組織中自訂特權標識管理 （PIM），包括更改啟動符合條件的角色指派的使用者的體驗。

## <a name="determine-your-version-of-pim"></a>確定 PIM 的版本

從 2019 年 11 月開始，特權標識管理的 Azure AD 角色部分正在更新為與 Azure 資源角色體驗相匹配的新版本。 這將創建其他功能以及對現有[API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本時，本文中遵循的哪些過程取決於您當前擁有的特權標識管理版本。 按照本節中的步驟確定您擁有的特權標識管理版本。 瞭解特權標識管理版本後，可以選擇本文中與該版本匹配的過程。

1. 使用處于[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的使用者登錄到[Azure 門戶](https://portal.azure.com/)。
1. 打開**Azure AD 特權標識管理**。 如果概述頁面頂部有橫幅，請按照本文**的"新版本**"選項卡中的說明進行操作。 否則，請按照 **"以前版本**"選項卡中的說明操作。

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

按照本文中的步驟批准或拒絕對 Azure AD 角色的請求。

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="open-role-settings"></a>開啟角色設定

請依照下列步驟開啟 Azure AD 角色的設定。

1. 在[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色角色中，使用使用者登錄到[Azure 門戶](https://portal.azure.com/)。
gt
1. 打開**Azure AD 特權標識管理**&gt; **Azure AD** &gt; **角色角色設置**。

    ![列出 Azure AD 角色的角色設置頁](./media/pim-how-to-change-default-settings/role-settings.png)

1. 選擇要配置其設置的角色。

    ![角色設置詳細資訊頁面，列出多個分配和啟動設置](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. 選取 [編輯]**** 以開啟 [角色設定] 頁面。

    ![使用用於更新分配和啟動設置的選項編輯角色設置頁面](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    在每個角色的 [角色設定] 窗格上，有許多您可以設定的設定。

## <a name="assignment-duration"></a>指派持續時間

當您進行角色的設定時，每個指派類型 (合格和有效) 都有兩個指派持續時間選項可選擇。 當使用者分配到特權標識管理中的角色時，這些選項將成為預設最長持續時間。

您可以選擇以下**符合條件的**分配持續時間選項之一：

| | |
| --- | --- |
| **允許永久合格分配** | 全域管理員和特權角色管理員可以分配永久符合條件的分配。 |
| **過期合格分配後** | 全域管理員和特權角色管理員可以要求所有符合條件的分配具有指定的開始日期和結束日期。 |

此外，您可以從下列**有效**指派持續時間選項中選擇一個：

| | |
| --- | --- |
| **允許永久活動分配** | 全域管理員和特權角色管理員可以分配永久活動分配。 |
| **過期活動分配後** | 全域管理員和特權角色管理員可以要求所有活動分配具有指定的開始日期和結束日期。 |

> [!NOTE]
> 全域管理員和特權角色管理員可以續訂具有指定結束日期的所有分配。 此外，使用者可以啟動自助服務請求以[擴展或續訂角色指派](pim-resource-roles-renew-extend.md)。

## <a name="require-multi-factor-authentication"></a>需要多重要素驗證

Privileged Identity Management 可針對兩個不同的案例選擇性地強制執行 Azure Multi-Factor Authentication。

### <a name="require-multi-factor-authentication-on-active-assignment"></a>要求為有效指派進行 Multi-Factor Authentication

在某些情況下，您可能希望將使用者分配給角色的持續時間很短（例如，一天）。 在這種情況下，分配的使用者不需要請求啟動。 在這種情況下，當使用者使用其角色指派時，特權標識管理無法強制實施多重要素驗證，因為他們從分配角色之時起就已在角色中處於活動狀態。

為了確保完成分配的管理員是他們說的，可以通過選中**活動賦值上的"多重要素驗證"** 框，對活動分配強制實施多重要素驗證。

### <a name="require-multi-factor-authentication-on-activation"></a>啟用時需要 Multi-Factor Authentication

您可以要求有資格擔任角色的使用者在啟動之前證明他們使用 Azure 多重要素驗證的人員。 多重要素驗證可確保使用者具有合理的確定性。 在使用者帳戶可能受到危害的情況下，強制執行這個選項可保護重要資源。

要在啟動之前需要多重要素驗證，請在 **"編輯角色設置**的分配"選項卡中選中**啟動時需要多重要素驗證**框。

如需詳細資訊，請參閱[多重要素驗證和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="activation-maximum-duration"></a>啟用持續時間上限

使用 [啟用持續時間上限]**** 滑桿，可設定角色在到期前維持作用中狀態的最長時間 (以小時為單位)。 此值可以是 1 到 24 小時。

## <a name="require-justification"></a>需要理由

您可以要求使用者在啟動時輸入業務理由。 若要要求提供理由，請核取 [進行有效指派時需要提供理由]**** 方塊，或核取 [啟用時需要提供理由]**** 方塊。

## <a name="require-approval-to-activate"></a>需要核准才可啟用

如果設置多個審批人，審批一旦其中一個審批或拒絕，審批即完成。 不能要求至少兩個使用者批准。 要需要批准才能啟動角色，請按照以下步驟操作。

1. 請核取 [需要核准才可啟用]**** 核取方塊。

1. **選擇選擇批准者**。

    ![選擇使用者或組窗格以選擇核准者](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 選擇至少一個使用者，然後按一下"**選擇**"。 您必須至少選取一個核准者。 沒有任何預設核准者。

    您的選項將出現在所選取的核准者清單中。

1. 指定所有角色設置後，選擇 **"更新"** 以保存更改。

# <a name="previous-version"></a>[早期版本](#tab/previous)

## <a name="open-role-settings"></a>開啟角色設定

請依照下列步驟開啟 Azure AD 角色的設定。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選取 [設定]****。

    ![Azure 廣告角色 - 設置](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. 選取 [角色]****。

1. 選擇要配置其設置的角色。

    ![Azure AD 角色 - 設置角色](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    在每個角色的設定頁面上，有許多您可以設定的設定。 這些設定只會影響身為**合格**指派 (而不是**永久**指派) 的使用者。

## <a name="activations"></a>啟用

使用 [啟用]**** 滑桿，設定角色在到期前維持作用中狀態的最長時間 (以小時為單位)。 此值可介於 1 到 72 小時。

## <a name="notifications"></a>通知

使用 [通知]**** 參數來指定啟動角色時，系統管理員是否會收到電子郵件通知。 此通知可用於檢測未經授權的或非法啟動。

設定為 [已啟用]**** 時，就會傳送通知給：

- 特殊權限角色管理員
- 安全性系統管理員
- 全域管理員

有關詳細資訊，請參閱[特權標識管理 中的電子郵件通知](pim-email-notifications.md)。

## <a name="incidentrequest-ticket"></a>事件/要求票證

使用 **"事件/請求票證**"開關要求符合條件的管理員在啟動其角色時包含票證編號。 這種做法可以使角色訪問審核更加有效。

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

使用 [Multi-Factor Authentication]**** 的切換，指定是否要要求使用者在啟用其角色之前，先以 MFA 驗證其身分識別。 他們只需要在每個會話驗證其身份一次，而不是每次啟動角色時驗證。 啟用 MFA 時，需要記住兩個秘訣：

- 擁有 Microsoft 電子郵件地址帳戶的使用者（通常@outlook.com為 ，但並不總是）無法註冊 Azure 多重要素驗證。 如果要將角色指派給具有 Microsoft 帳戶的使用者，則應將其指定為永久管理員，或禁用該角色的多重要素驗證。
- 不能為 Azure AD 和 Office 365 的高度特權角色禁用 Azure 多重要素驗證。 此安全功能有助於保護以下角色：  
  
  - Azure 資訊保護管理員
  - 計費管理員
  - 雲端應用程式系統管理員
  - 規範管理員
  - 條件式存取系統管理員
  - 動態 365 管理員
  - 客戶 LockBox 存取核准者
  - 目錄寫入器
  - Exchange 系統管理員
  - 全域管理員
  - Intune 系統管理員
  - 電源 BI 管理員
  - 特殊權限角色管理員
  - 安全性系統管理員
  - SharePoint 管理員
  - 商務用 Skype 的管理員
  - 使用者管理員

如需詳細資訊，請參閱[多重要素驗證和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="require-approval"></a>需要核准

如果要委派啟動角色所需的批准，請按照以下步驟操作。

1. 將 [需要核准]**** 切換為 [啟用]****。 展開的窗格會有選取核准者的選項。

    ![Azure AD 角色 - 設置 - 需要審批](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    如果不指定任何核准者，特權角色管理員將成為預設核准者，然後需要批准此角色的所有啟動請求。

1. 要添加核准者，請按一下 **"選擇核准者**"。

    ![Azure AD 角色 - 設置 - 需要審批](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 除了特權角色管理員之外，選擇一個或多個審批人，然後按一下"**選擇**"。 我們建議您至少添加兩個核准者。 即使您將自己添加為核准者，也不能自行批准角色啟動。 您的選項將出現在所選取的核准者清單中。

1. 指定所有角色設置後，選擇 **"保存"** 以保存更改。

---

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在特權標識管理中為 Azure AD 角色配置安全警報](pim-how-to-configure-security-alerts.md)
