---
title: 核准或拒絕存取要求-Azure AD 的權利管理
description: 瞭解如何使用我的存取權入口網站，在 Azure Active Directory 的權利管理中核准或拒絕存取套件的要求。
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261719"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>核准或拒絕 Azure AD 權利管理中的存取要求

透過 Azure AD 的權利管理，您可以設定原則以要求存取套件的核准，並選擇一或多個核准者。 本文說明指定的核准者可以如何核准或拒絕存取套件的要求。

## <a name="open-request"></a>開啟要求

核准或拒絕存取要求的第一個步驟，是尋找並開啟待核准的存取要求。 有兩種方式可以開啟存取要求。

**先決條件角色：** 人員

1. 尋找 Microsoft Azure 的電子郵件，要求您核准或拒絕要求。 以下是範例電子郵件：

    ![核准存取封裝電子郵件的要求](./media/entitlement-management-shared/approver-request-email.png)

1. 按一下 [**核准] 或 [拒絕要求**] 連結，以開啟存取要求。

1. 登入我的存取權入口網站。

如果您沒有電子郵件，您可以遵循下列步驟來尋找待核准的存取要求。

1. 在[https://myaccess.microsoft.com](https://myaccess.microsoft.com)登入我的存取權入口網站。  （針對美國政府，我的存取權入口網站連結中的網域將會 `myaccess.microsoft.us`）。

1. 在左側功能表中，按一下 [**核准**] 以查看待核准的存取要求清單。

1. 在 [**暫**止] 索引標籤上，尋找要求。

## <a name="approve-or-deny-request"></a>核准或拒絕要求

在您開啟待核准的存取要求之後，您可以看到可協助您做出核准或拒絕決策的詳細資料。

**先決條件角色：** 人員

1. 按一下 [檢視] 連結以開啟 [存取要求] 窗格。

1. 按一下 [**詳細資料**] 以查看存取要求的詳細資料。

    詳細資料包括使用者的名稱、組織、存取開始和結束日期（如有提供）、商業理由、提交要求的時間，以及要求何時會過期。

1. 按一下 [**核准**] 或 [**拒絕**]。

1. 如有需要，請輸入原因。

    ![我的存取權入口網站 - 存取要求](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. 按一下 [提交] 以提交您的決定。

    如果原則已設定多個核准者，則只有一位核准者需要做出有關等待核准的決策。 在核准者提交對存取要求的決策之後，要求會完成，而且其他核准者無法再核准或拒絕要求。 其他核准者可以在其我的存取權入口網站中查看要求決策和決策者。 目前僅支援單一階段核准。

    如果沒有任何設定的核准者能夠核准或拒絕存取要求，則要求會在設定的要求持續時間後過期。 使用者會收到通知，指出其存取要求已過期，而且需要重新提交存取要求。

## <a name="next-steps"></a>後續步驟

- [要求存取權套件](entitlement-management-request-access.md)
- [要求處理和電子郵件通知](entitlement-management-process.md)
