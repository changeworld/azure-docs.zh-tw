---
title: 在訪問審核中查看對組&應用的訪問 - Azure AD
description: 瞭解如何在 Azure 活動目錄訪問審核中查看自己對組或應用程式的存取權限。
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422410"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>在 Azure AD 訪問審閱中查看自己對組或應用程式的存取權限

Azure 活動目錄 （Azure AD） 使用稱為 Azure AD 訪問審核的功能簡化了企業如何管理對 Azure AD 和其他 Microsoft 線上服務中的組或應用程式的訪問。

本文介紹如何查看自己對組或應用程式的存取權限。

## <a name="open-the-access-review"></a>打開訪問審核

執行訪問審核的第一步是查找並打開訪問審核。

1. 查找來自 Microsoft 的電子郵件，要求您查看存取權限。 下面是一個示例電子郵件，用於查看您對群組的存取權限。

    ![來自 Microsoft 的電子郵件示例，用於查看您對組的訪問](./media/review-your-access/access-review-email.png)

1. 按一下 **"查看訪問"** 連結以打開訪問審核。

如果您沒有電子郵件，您可以按照以下步驟找到掛起的訪問審核。

1. 登錄到 中的[https://myapps.microsoft.com](https://myapps.microsoft.com)MyApps 門戶。

    ![MyApps 門戶列出您有權訪問的應用](./media/review-your-access/myapps-access-panel.png)

1. 在頁面右上角按一下使用者符號，其中顯示您的名稱和預設組織。 如果列出多個組織，請選取已要求存取權檢閱的組織。

1. 在頁面右側，按一下 **"訪問審核"** 磁貼以查看掛起的訪問審核的清單。

    如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

    ![應用和組的待定訪問審核清單](./media/review-your-access/access-reviews-list.png)

1. 按一下 **"開始審閱"** 連結，查看要執行的訪問審核。

## <a name="perform-the-access-review"></a>執行訪問審核

打開訪問審核後，即可看到存取權限。

1. 查看您的存取權限，並決定您是否仍需要存取權限。

    如果請求是查看其他人的訪問，則頁面看起來會有所不同。 有關詳細資訊，請參閱[查看對組或應用程式的訪問](perform-access-review.md)。

    ![打開訪問審核，詢問您是否仍需要訪問組](./media/review-your-access/perform-access-review.png)

1. 按一下"**是**"可保留您的存取權限，或按一下"**否**"刪除您的存取權限。

1. 如果按一下"**是**"，則可能需要在 **"原因"** 框中指定對齊方式。

    ![已完成訪問審核，詢問您是否仍需要訪問組](./media/review-your-access/perform-access-review-submit.png)

1. 按一下 [提交]****。

    您的選擇已提交，您返回到 MyApps 門戶。

    如果要更改回應，請重新打開訪問審核頁面並更新您的回應。 您可以隨時更改回應，直到訪問審核結束。

    > [!NOTE]
    > 如果您指示不再需要存取權限，則不會立即刪除。 審核結束時或管理員停止審核時，您將被刪除。

## <a name="next-steps"></a>後續步驟

- [完成組或應用程式的訪問審核](complete-access-review.md)
