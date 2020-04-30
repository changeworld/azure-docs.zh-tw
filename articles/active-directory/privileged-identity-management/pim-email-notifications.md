---
title: PIM 中的電子郵件通知-Azure Active Directory |Microsoft Docs
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
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02fbfc83c16cb13376cce820f19b247a7cd7db59
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232303"
---
# <a name="email-notifications-in-pim"></a>PIM 中的電子郵件通知

Privileged Identity Management （PIM）可讓您知道 Azure Active Directory （Azure AD）組織中發生重要事件的時間，例如指派或啟用角色的時間。 Privileged Identity Management 會傳送電子郵件通知給您和其他參與者，以保持您的通知。 這些電子郵件也可能包含相關工作 (例如啟用或更新角色) 的連結。 本文說明這些電子郵件的外觀、傳送時間及收件者。

## <a name="sender-email-address-and-subject-line"></a>寄件者電子郵件地址和主旨列

Azure AD 和 Azure 資源角色的 Privileged Identity Management 傳送的電子郵件都具有下列寄件者電子郵件地址：

- 電子郵件地址： **azure-\@noreply microsoft.com**
- 顯示名稱：Microsoft Azure

這些電子郵件的主旨行中包含 **PIM** 前置詞。 範例如下：

- PIM： Alain Charon 已永久指派「備份讀取者」角色

## <a name="notifications-for-azure-ad-roles"></a>Azure AD 角色的通知

Privileged Identity Management 會在 Azure AD 角色發生下列事件時傳送電子郵件：

- 當特殊權限角色啟用正在等待核准時
- 當特殊權限角色啟用要求已完成時
- 啟用 Azure AD Privileged Identity Management 時

收到 Azure AD 角色的這些電子郵件的對象取決於您的角色、事件和通知設定：

| User | 角色啟用正在等待核准 | 角色啟用要求已完成 | RDP 已啟用 |
| --- | --- | --- | --- |
| 特殊權限角色管理員</br>(已啟用/合格) | 是</br>(唯有未指定任何明確核准者時) | 是* | 是 |
| 安全性系統管理員</br>(已啟用/合格) | 否 | 是* | 是 |
| 全域管理員</br>(已啟用/合格) | 否 | 是* | 是 |

\* 如果 [**[通知]** 設定](pim-how-to-change-default-settings.md#notifications)設為 [啟用]**** 時。

以下所顯示的範例電子郵件，會在使用者啟用虛構 Contoso 組織的 Azure AD 角色時傳送。

![Azure AD 角色的新 Privileged Identity Management 電子郵件](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Azure AD 角色的每週 Privileged Identity Management 摘要電子郵件

Azure AD 角色的每週 Privileged Identity Management 摘要電子郵件會傳送給特殊許可權角色系統管理員、安全性系統管理員，以及已啟用 Privileged Identity Management 的全域管理員。 此每週電子郵件提供了一周 Privileged Identity Management 活動的快照集，以及特殊許可權角色指派。 僅適用于公用雲端上的 Azure AD 組織。 以下是範例電子郵件：

![Azure AD 角色的每週 Privileged Identity Management 摘要電子郵件](./media/pim-email-notifications/email-directory-weekly.png)

電子郵件包含四個圖格：

| 圖格 | 描述 |
| --- | --- |
| **已啟用的使用者** | 使用者在組織內啟用其合格角色的次數。 |
| **成為永久狀態的使用者** | 具有合格指派的使用者成為永久狀態的次數。 |
| **Privileged Identity Management 中的角色指派** | 在 Privileged Identity Management 內指派合格角色給使用者的次數。 |
| **PIM 外部的角色指派** | 使用者在 Privileged Identity Management 外部獲指派永久角色的次數（在 Azure AD 內）。 |

[**最上層角色**] 區段會根據每個角色的永久和合格系統管理員總數，列出組織中的前五個角色。 [採取動作]**** 連結會開啟 [PIM 精靈](pim-security-wizard.md)，您可以在其中將永久管理員分批轉換成合格的管理員。

## <a name="email-timing-for-activation-approvals"></a>啟用核准的電子郵件時間

當使用者啟用其角色，而角色設定需要核准時，核准者將會收到三封電子郵件，供每個核准之用：

- 要求核准或拒絕使用者的啟用要求（由要求核准引擎傳送）
- 已核准使用者的要求（由要求核准引擎傳送）
- 使用者的角色已啟用（由 Privileged Identity Management 傳送）

要求核准引擎所傳送的前兩個電子郵件可能會延遲。 目前，90% 的電子郵件需要三到十分鐘的時間，但對1% 的客戶而言，最長可達15分鐘。

若核准要求在第一封電子郵件傳送前 Azure 入口網站核准，則不會再觸發第一封電子郵件，而其他核准者也不會收到核准要求的電子郵件通知。 看起來似乎不會收到電子郵件，而是預期的行為。

## <a name="pim-emails-for-azure-resource-roles"></a>Azure 資源角色的 PIM 電子郵件

當 Azure 資源角色發生下列事件時，Privileged Identity Management 會將電子郵件傳送給擁有者和使用者存取系統管理員：

- 當角色指派等待核准時
- 當角色已指派時
- 當角色即將到期時
- 當角色有資格擴充時
- 當使用者正在更新角色時
- 當角色啟用要求已完成時

當 Azure 資源角色發生下列事件時，Privileged Identity Management 會將電子郵件傳送給終端使用者：

- 當角色已指派給使用者時
- 當使用者的角色已過期時
- 當使用者的角色已擴充時
- 當使用者的角色啟用要求已完成時

以下所顯示的範例電子郵件，會在指派虛構 Contoso 組織的 Azure 資源角色給使用者時傳送。

![適用于 Azure 資源角色的新 Privileged Identity Management 電子郵件](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)
- [核准或拒絕 Privileged Identity Management 中 Azure AD 角色的要求](azure-ad-pim-approval-workflow.md)
