---
title: 批准或拒絕訪問請求 - Azure AD 授權管理
description: 瞭解如何使用"我的訪問"門戶在 Azure 活動目錄授權管理中批准或拒絕對訪問包的請求。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261719"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中批准或拒絕訪問請求

使用 Azure AD 授權管理，可以將策略配置為需要訪問包的批准，並選擇一個或多個核准者。 本文介紹指定核准者如何批准或拒絕訪問包請求。

## <a name="open-request"></a>打開請求

批准或拒絕訪問請求的第一步是查找並打開待審批的訪問請求。 有兩種方法可以打開訪問請求。

**先決條件角色：** 批准人

1. 查找來自 Microsoft Azure 的電子郵件，要求您批准或拒絕請求。 下面是一個示例電子郵件：

    ![批准訪問包電子郵件的請求](./media/entitlement-management-shared/approver-request-email.png)

1. 按一下 **"批准"或"拒絕請求**"連結以打開訪問請求。

1. 登錄到"我的訪問"門戶。

如果您沒有電子郵件，您可以按照以下步驟找到等待批准的訪問請求。

1. 登錄到 中的[https://myaccess.microsoft.com](https://myaccess.microsoft.com)"我的訪問"門戶。  （對於美國政府，"我的訪問"門戶連結中的域將為`myaccess.microsoft.us`.）

1. 在左側功能表中，按一下 **"審批"** 以查看待審批的訪問請求清單。

1. 在 **"掛起"** 選項卡上，查找請求。

## <a name="approve-or-deny-request"></a>批准或拒絕請求

在打開待審批的存取權限請求後，您可以看到可説明您做出批准或拒絕決策的詳細資訊。

**先決條件角色：** 批准人

1. 按一下 [檢視]**** 連結以開啟 [存取要求] 窗格。

1. 按一下 **"詳細資訊**"以查看有關訪問請求的詳細資訊。

    詳細資訊包括使用者名、組織、訪問開始日期和結束日期（如果提供）、業務理由、提交請求的時間以及請求何時過期。

1. 按一下 **"批准**"或 **"拒絕**"。

1. 如有必要，輸入原因。

    ![我的存取權入口網站 - 存取要求](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. 按一下 [提交]**** 以提交您的決定。

    如果策略配置了多個核准者，則只有一個核准者需要對待決審批作出決定。 審批人向訪問請求提交其決策後，請求已完成，其他審批人無法再批准或拒絕該請求。 其他核准者可以在其"我的訪問"門戶中查看請求決策和決策者。 此時，僅支援單階段審批。

    如果任何已配置的核准者都不能批准或拒絕訪問請求，則請求將在配置的請求持續時間後過期。 使用者將收到其訪問請求已過期的通知，並且需要重新提交訪問請求。

## <a name="next-steps"></a>後續步驟

- [請求訪問包](entitlement-management-request-access.md)
- [請求流程和電子郵件通知](entitlement-management-process.md)
