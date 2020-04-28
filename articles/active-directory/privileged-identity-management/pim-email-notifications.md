---
title: PIM 中的電子郵件通知 - Azure 活動目錄 |微軟文件
description: 說明 Azure AD Privileged Identity Management (PIM) 中的電子郵件通知。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 201abd24bc4056337f1ffecd2dabd002ae352c74
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866428"
---
# <a name="email-notifications-in-pim"></a>PIM 中的電子郵件通知

特權識別管理 (PIM) 可讓您知道 Azure 活動目錄 (Azure AD) 組織中何時發生重要事件,例如何時分配或啟動角色。 特權身份管理通過向您發送和其他參與者的電子郵件通知來通知您。 這些電子郵件也可能包含相關工作 (例如啟用或更新角色) 的連結。 本文說明這些電子郵件的外觀、傳送時間及收件者。

## <a name="sender-email-address-and-subject-line"></a>寄件者電子郵件地址和主旨列

從 Azure AD 與 Azure 資源角色的權限管理傳送的電子郵件具有以下寄件者電子郵件地址:

- 電子郵件地址 **:azure\@無回覆 microsoft.com**
- 顯示名稱：Microsoft Azure

這些電子郵件的主旨行中包含 **PIM** 前置詞。 以下是範例：

- PIM:阿蘭·查龍被永久分配為備份讀取器角色

## <a name="notifications-for-azure-ad-roles"></a>Azure AD 角色的通知

當 Azure AD 角色發生以下事件時,特權識別管理會傳送電子郵件:

- 當特殊權限角色啟用正在等待核准時
- 當特殊權限角色啟用要求已完成時
- 開啟 Azure AD 特權識別管理時

收到 Azure AD 角色的這些電子郵件的對象取決於您的角色、事件和通知設定：

| User | 角色啟用正在等待核准 | 角色啟用要求已完成 | RDP 已啟用 |
| --- | --- | --- | --- |
| 特殊權限角色管理員</br>(已啟用/合格) | 是</br>(唯有未指定任何明確核准者時) | 是* | 是 |
| 安全性系統管理員</br>(已啟用/合格) | 否 | 是* | 是 |
| 全域管理員</br>(已啟用/合格) | 否 | 是* | 是 |

\* 如果 [**[通知]** 設定](pim-how-to-change-default-settings.md#notifications)設為 [啟用]**** 時。

以下所顯示的範例電子郵件，會在使用者啟用虛構 Contoso 組織的 Azure AD 角色時傳送。

![Azure AD 角色的新特權識別管理電子郵件](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>每週特權身份管理摘要電子郵件的 Azure AD 角色

Azure AD 角色的每周特權標識管理摘要電子郵件將發送給啟用了特權標識管理的特權角色管理員、安全管理員和全域管理員。 此每周電子郵件提供本周特權身份管理活動的快照以及特權角色分配。 它只適用於公用雲端上的租用戶。 以下是範例電子郵件：

![每週特權身份管理摘要電子郵件的 Azure AD 角色](./media/pim-email-notifications/email-directory-weekly.png)

電子郵件包含四個圖格：

| 圖格 | 描述 |
| --- | --- |
| **已啟用的使用者** | 使用者在租用戶內啟用其合格角色的次數。 |
| **成為永久狀態的使用者** | 具有合格指派的使用者成為永久狀態的次數。 |
| **特權身份管理中的角色分配** | 在特權標識管理中為使用者分配合格角色的次數。 |
| **PIM 外部的角色指派** | 在特權識別管理之外(Azure AD 內部)為使用者分配永久角色的次數。 |

[頂端的角色概觀]**** 區段會根據每個角色的永久與合格管理員總數，列出您租用戶中的前五個角色。 [採取動作]**** 連結會開啟 [PIM 精靈](pim-security-wizard.md)，您可以在其中將永久管理員分批轉換成合格的管理員。

## <a name="email-timing-for-activation-approvals"></a>啟動核准的電子郵件時間

當使用者啟動其角色且角色設置需要審批時,審批者將收到三封每次審批的電子郵件:

- 要求批准或拒絕使用者的啟動要求(由請求審批引擎傳送)
- 使用者的要求獲得批准 (由請求審批引擎傳送)
- 使用者的角色被啟動 (由特權識別管理傳送)

請求審批引擎發送的前兩封電子郵件可能會延遲。 目前,90% 的電子郵件需要 3 到 10 分鐘,但對於 1% 的客戶來說,時間可能更長,長達 15 分鐘。

如果在發送第一封電子郵件之前在 Azure 門戶中批准了審批請求,則不再觸發第一封電子郵件,也不會通過電子郵件通知其他審批人批准請求。 看起來好像他們沒有得到電子郵件,但這是預期的行為。

## <a name="pim-emails-for-azure-resource-roles"></a>Azure 資源角色的 PIM 電子郵件

當 Azure 資源角色發生以下事件時,特權識別管理會向擁有者和使用者存取管理員發送電子郵件:

- 當角色指派等待核准時
- 當角色已指派時
- 當角色即將到期時
- 當角色有資格擴充時
- 當使用者正在更新角色時
- 當角色啟用要求已完成時

特權識別管理在 Azure 資源角色發生以下事件時向最終使用者發送電子郵件:

- 當角色已指派給使用者時
- 當使用者的角色已過期時
- 當使用者的角色已擴充時
- 當使用者的角色啟用要求已完成時

以下所顯示的範例電子郵件，會在指派虛構 Contoso 組織的 Azure 資源角色給使用者時傳送。

![Azure 資源角色的新特權識別管理電子郵件](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>後續步驟

- [在權限管理中設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)
- [在權限管理中批准或拒絕對 Azure AD 角色的請求](azure-ad-pim-approval-workflow.md)
