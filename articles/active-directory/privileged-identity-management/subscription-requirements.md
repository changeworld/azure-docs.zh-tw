---
title: 使用特權標識管理的許可證要求 - Azure 活動目錄 |微軟文檔
description: 說明要使用 Azure AD Privileged Identity Management (PIM) 的授權需求。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932327"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>使用 Privileged Identity Management 的授權要求

若要使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，目錄必須具有有效的授權。 此外，也必須指派授權給管理員和相關的使用者。 本文介紹了使用特權標識管理的許可證要求。

## <a name="valid-licenses"></a>有效許可證

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>您必須擁有多少許可證？

確保目錄至少具有與員工執行以下任務相同的 Azure AD 高級 P2 許可證：

- 使用 PIM 管理為符合資格的 Azure AD 角色的使用者
- 能夠批准或拒絕 PIM 中的啟動請求的使用者
- 指派給 Azure 資源角色 (透過及時或直接 (以時間為基礎) 的方式指派) 的使用者  
- 指派給存取權檢閱的使用者
- 執行存取權檢閱的使用者

以下任務**不需要**Azure AD 高級 P2 許可證：

- 具有設置 PIM、配置策略、接收警報和設置訪問審核的全域管理員或特權角色管理員角色的使用者不需要許可證。

有關許可證的詳細資訊，請參閱使用[Azure 活動目錄門戶分配或刪除許可證](../fundamentals/license-users-groups.md)。

## <a name="example-license-scenarios"></a>許可證方案示例

下面是一些示例許可證方案，可説明您確定必須擁有的許可數。

| 狀況 | 計算 | 許可證數 |
| --- | --- | --- |
| 伍德格羅夫銀行擁有 10 個不同部門的管理員和 2 名配置和管理 PIM 的全球管理員。 他們使五個管理員符合資格。 | 符合資格的管理員的五個許可證 | 5 |
| 圖形設計學院有 25 名管理員，其中 14 名通過 PIM 進行管理。 角色啟動需要批准，組織中有三個不同的使用者可以批准啟動。 | 合格角色的 14 個許可證 – 3 個審批人 | 17 |
| Contoso 有 50 名管理員，其中 42 名是通過 PIM 進行管理的。 角色啟動需要批准，組織中有五個不同的使用者可以批准啟動。 Contoso 還每月對分配給管理員角色的使用者進行審查，檢閱者是使用者經理，其中六個不是 PIM 管理的管理員角色。 | 合格角色的 42 個許可證 + 5 個審批人 + 6 個檢閱者 | 53 |

## <a name="what-happens-when-a-license-expires"></a>授權過期會發生什麼狀況？

如果 Azure AD 高級 P2、EMS E5 或試用許可證過期，則目錄中將不再提供特權標識管理功能：

- Azure AD 角色的永久角色指派不會受到影響。
- Azure 門戶中的特權標識管理服務以及特權標識管理的圖形 API Cmdlet 和 PowerShell 介面將不再可供使用者啟動特權角色、管理特權訪問或執行存取權限角色的審核。
- Azure AD 角色的合格角色指派將會遭到移除，因為使用者將無法再啟動特殊權限的角色。
- Azure AD 角色的任何正在進行的訪問審核都將結束，特權標識管理配置設置將被刪除。
- 特權身份管理將不再發送有關角色指派更改的電子郵件。

## <a name="next-steps"></a>後續步驟

- [部署 Privileged Identity Management](pim-deployment-plan.md)
- [開始使用 Privileged Identity Management](pim-getting-started.md)
- [在特權身份管理中無法管理的角色](pim-roles.md)
