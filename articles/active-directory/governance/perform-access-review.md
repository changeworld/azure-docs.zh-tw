---
title: 在訪問審核中查看對組&應用程式的存取權限 - Azure AD
description: 瞭解如何在 Azure 活動目錄訪問審核中查看組成員或應用程式訪問的訪問。
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128460"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>查看 Azure AD 訪問審核中對組和應用程式的訪問

Azure 活動目錄 （Azure AD） 使用稱為 Azure AD 訪問審核的功能簡化了企業如何管理對 Azure AD 和其他 Microsoft 線上服務中的組和應用程式的訪問。

本文介紹指定檢閱者如何對組成員或有權訪問應用程式的使用者執行訪問審核。

## <a name="open-the-access-review"></a>打開訪問審核

執行訪問審核的第一步是查找並打開訪問審核。

1. 查找來自 Microsoft 的電子郵件，要求您查看存取權限。 下面是一個示例電子郵件，用於查看組的存取權限。

    ![來自 Microsoft 的電子郵件示例，用於查看對組的訪問](./media/perform-access-review/access-review-email.png)

1. 按一下 **"開始審核**"連結以打開訪問審核。

如果您沒有電子郵件，您可以按照以下步驟找到掛起的訪問審核。

1. 登錄到 中的[https://myapps.microsoft.com](https://myapps.microsoft.com)MyApps 門戶。

    ![MyApps 門戶列出您有權訪問的應用](./media/perform-access-review/myapps-access-panel.png)

1. 在頁面右上角按一下使用者符號，其中顯示您的名稱和預設組織。 如果列出多個組織，請選取已要求存取權檢閱的組織。

1. 按一下 **"訪問審核"** 磁貼以查看掛起的訪問審核的清單。

    如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

    ![應用和組的待定訪問審核清單](./media/perform-access-review/access-reviews-list.png)

1. 按一下 **"開始審閱"** 連結，查看要執行的訪問審核。

## <a name="perform-the-access-review"></a>執行訪問審核

打開訪問審核後，您將看到需要查看的使用者的姓名。

如果請求是查看您自己的存取權限，則頁面看起來會有所不同。 有關詳細資訊，請參閱[查看自己對組或應用程式的存取權限](review-your-access.md)。

![打開訪問審核，列出需要審核的使用者](./media/perform-access-review/perform-access-review.png)

有兩種方法可以批准或拒絕訪問：

- 您可以批准或拒絕一個或多個使用者的存取權限，或者
- 您可以接受系統建議，這是最簡單、最快捷的方法。

### <a name="approve-or-deny-access-for-one-or-more-users"></a>批准或拒絕一個或多個使用者的存取權限

1. 查看使用者清單以決定是否批准或拒絕其繼續訪問。

1. 要批准或拒絕單個使用者的存取權限，請按一下該行以打開一個視窗以指定要執行的操作。 要批准或拒絕多個使用者的訪問，請在使用者旁邊添加核取記號，然後按一下 **"審閱 X 使用者"** 按鈕以打開一個視窗以指定要執行的操作。

1. 按一下 **"批准**"或 **"拒絕**"。 如果您不確定，可以按一下 **"不知道**"。 這樣做將導致使用者維護其存取權限，但所選內容將反映在稽核記錄中。

    ![包含"批准"、"拒絕"和"不知道"選項的操作視窗](./media/perform-access-review/approve-deny.png)

1. 如有必要，請在 **"原因"** 框中輸入原因。

    訪問審核的管理員可能要求您提供批准繼續訪問或組成員身份的理由。

1. 指定要執行的操作後，按一下"**保存**"。

    如果要更改回應，請選擇該行並更新回應。 例如，您可以批准以前被拒絕的使用者或拒絕以前批准的使用者。 您可以隨時更改回應，直到訪問審核結束。

    如果有多個檢閱者，則記錄上次提交的回應。 考慮一個管理員指定兩個檢閱者的示例 - Alice 和 Bob。 Alice 首先打開訪問審核並批准訪問。 在審核結束之前，Bob 將打開訪問審核並拒絕訪問。 最後一個拒絕回應是錄製的。

    > [!NOTE]
    > 如果使用者被拒絕訪問，則不會立即刪除他們。 當審核結束時或管理員停止審核時，它們將被刪除。

### <a name="approve-or-deny-access-based-on-recommendations"></a>根據建議批准或拒絕訪問

為了讓您更輕鬆、更快地查看訪問審核，我們還提供您只需按一下一下即可接受的建議。 建議基於使用者的登錄活動生成。

1. 在頁面底部的藍色欄中，按一下"**接受建議**"。

    ![打開訪問審核清單，顯示"接受建議"按鈕](./media/perform-access-review/accept-recommendations.png)

    您將看到建議操作的摘要。

    ![顯示建議操作摘要的視窗](./media/perform-access-review/accept-recommendations-summary.png)

1. 按一下 **"確定"** 以接受建議。

## <a name="next-steps"></a>後續步驟

- [完成組或應用程式的訪問審核](complete-access-review.md)
