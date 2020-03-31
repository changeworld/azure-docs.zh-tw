---
title: Azure AD 授權管理中訪問包的自我審查
description: 瞭解如何在 Azure 活動目錄訪問審核（預覽）中查看使用者對授權管理訪問包的訪問。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967764"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 授權管理中訪問包的自我審查

Azure AD 授權管理簡化了企業管理對組、應用程式和 SharePoint 網站的訪問的方式。 本文介紹使用者如何自行查看其分配的訪問包。

## <a name="open-the-access-review"></a>打開訪問審核

要執行訪問審核，必須首先打開訪問審核。 使用以下過程查找和打開訪問審核：

1. 您可能會收到來自 Microsoft 的電子郵件，要求您查看存取權限。 找到電子郵件以打開訪問審核。 下面是請求查看存取權限的電子郵件示例： 
    
    ![訪問審閱自我審閱電子郵件](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. 按一下 **"查看訪問"** 連結。

1. 如果您沒有收到電子郵件，您也可以直接https://myaccess.microsoft.com查找掛起的訪問審核。  （對於美國政府，`https://myaccess.microsoft.us`請使用。

1. 按一下左側巡覽列上的 **"訪問審閱**"以查看分配給您掛起的訪問審核的清單。


1.  按一下要開始的評論。

## <a name="perform-the-access-review"></a>執行訪問審核

打開訪問審核後，即可看到存取權限。 使用以下過程執行訪問審核：

1.  確定是否仍需要訪問訪問包。 例如，您正在處理的專案尚未完成，因此您仍然需要存取權限才能繼續處理該專案。

1.  按一下"**是**"可保留您的存取權限，或按一下"**否**"刪除您的存取權限。
    >[!NOTE]
    >如果您說不再需要存取權限，則不會立即從訪問包中刪除。 審核結束時或管理員停止審核時，您將從訪問包中刪除。

1.  如果按一下"**是**"，則可能需要在 **"原因"** 框中包含一個對齊語句。

1.  按一下 [提交]****。

如果您改變主意並決定在審核結束前更改您的回復，您可以返回審核。

## <a name="next-steps"></a>後續步驟

- [查看訪問包的存取權限](entitlement-management-access-reviews-review-access.md) 
