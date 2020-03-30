---
title: PIM 中的電子郵件通知 - Azure 活動目錄 |微軟文檔
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
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72756390"
---
# <a name="email-notifications-in-pim"></a>PIM 中的電子郵件通知

特權標識管理 （PIM） 可讓您知道 Azure 活動目錄 （Azure AD） 組織中何時發生重要事件，例如何時分配或啟動角色。 特權身份管理通過向您發送和其他參與者的電子郵件通知來通知您。 這些電子郵件也可能包含相關工作 (例如啟用或更新角色) 的連結。 本文說明這些電子郵件的外觀、傳送時間及收件者。

## <a name="sender-email-address-and-subject-line"></a>寄件者電子郵件地址和主旨列

從 Azure AD 和 Azure 資源角色的特權標識管理發送的電子郵件具有以下寄件者電子郵件地址：

- 電子郵件地址 **：azure 無回復\@microsoft.com**
- 顯示名稱：Microsoft Azure

這些電子郵件的主旨行中包含 **PIM** 前置詞。 以下是範例：

- PIM：阿蘭·查龍被永久分配為備份讀取器角色

## <a name="notifications-for-azure-ad-roles"></a>Azure AD 角色的通知

當 Azure AD 角色發生以下事件時，特權標識管理會發送電子郵件：

- 當特殊權限角色啟用正在等待核准時
- 當特殊權限角色啟用要求已完成時
- 啟用 Azure AD 特權標識管理時

收到 Azure AD 角色的這些電子郵件的對象取決於您的角色、事件和通知設定：

| User | 角色啟用正在等待核准 | 角色啟用要求已完成 | RDP 已啟用 |
| --- | --- | --- | --- |
| 特殊權限角色管理員</br>(已啟用/合格) | 是</br>(唯有未指定任何明確核准者時) | 是* | 是 |
| 安全性系統管理員</br>(已啟用/合格) | 否 | 是* | 是 |
| 全域管理員</br>(已啟用/合格) | 否 | 是* | 是 |

\* 如果 [**[通知]** 設定](pim-how-to-change-default-settings.md#notifications)設為 [啟用]**** 時。

以下所顯示的範例電子郵件，會在使用者啟用虛構 Contoso 組織的 Azure AD 角色時傳送。

![Azure AD 角色的新特權標識管理電子郵件](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>每週特權身份管理摘要電子郵件的 Azure AD 角色

Azure AD 角色的每週特權標識管理摘要電子郵件將發送給啟用了特權標識管理的特權角色管理員、安全管理員和全域管理員。 此每週電子郵件提供本周特權身份管理活動的快照以及特權角色指派。 它只適用於公用雲端上的租用戶。 以下是範例電子郵件：

![每週特權身份管理摘要電子郵件的 Azure AD 角色](./media/pim-email-notifications/email-directory-weekly.png)

電子郵件包含四個圖格：

| 圖格 | 描述 |
| --- | --- |
| **已啟用的使用者** | 使用者在租用戶內啟用其合格角色的次數。 |
| **成為永久狀態的使用者** | 具有合格指派的使用者成為永久狀態的次數。 |
| **特權身份管理中的角色指派** | 在特權標識管理中為使用者分配合格角色的次數。 |
| **PIM 外部的角色指派** | 在特權標識管理之外（Azure AD 內部）為使用者分配永久角色的次數。 |

[頂端的角色概觀]**** 區段會根據每個角色的永久與合格管理員總數，列出您租用戶中的前五個角色。 [採取動作]**** 連結會開啟 [PIM 精靈](pim-security-wizard.md)，您可以在其中將永久管理員分批轉換成合格的管理員。

## <a name="pim-emails-for-azure-resource-roles"></a>Azure 資源角色的 PIM 電子郵件

當 Azure 資源角色發生以下事件時，特權標識管理會向擁有者和使用者訪問管理員發送電子郵件：

- 當角色指派等待核准時
- 當角色已指派時
- 當角色即將到期時
- 當角色有資格擴充時
- 當使用者正在更新角色時
- 當角色啟用要求已完成時

特權標識管理在 Azure 資源角色發生以下事件時向最終使用者發送電子郵件：

- 當角色已指派給使用者時
- 當使用者的角色已過期時
- 當使用者的角色已擴充時
- 當使用者的角色啟用要求已完成時

以下所顯示的範例電子郵件，會在指派虛構 Contoso 組織的 Azure 資源角色給使用者時傳送。

![Azure 資源角色的新特權標識管理電子郵件](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中配置 Azure AD 角色設置](pim-how-to-change-default-settings.md)
- [在特權標識管理中批准或拒絕對 Azure AD 角色的請求](azure-ad-pim-approval-workflow.md)
