---
title: 在存取權審查中審查您對群組 & 應用程式的存取權-Azure AD
description: 瞭解如何在 Azure Active Directory 存取評論中，檢查您自己對群組或應用程式的存取權。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422410"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>在 Azure AD 存取評論中，對群組或應用程式的存取權進行審核

Azure Active Directory （Azure AD）藉由稱為 Azure AD 存取評論的功能，簡化了企業管理 Azure AD 和其他 Microsoft 線上服務中群組或應用程式存取權的方式。

本文說明如何檢查您自己對群組或應用程式的存取權。

## <a name="open-the-access-review"></a>開啟存取權審查

執行存取權審查的第一個步驟是尋找並開啟存取權審查。

1. 尋找 Microsoft 的電子郵件，要求您審查存取權。 以下是用來審查群組存取權的電子郵件範例。

    ![Microsoft 提供的電子郵件範例，用以審查您對群組的存取權](./media/review-your-access/access-review-email.png)

1. 按一下 [**審查存取**] 連結以開啟存取權審查。

如果您沒有電子郵件，您可以遵循下列步驟來尋找您的暫止存取審查。

1. 登入 MyApps 入口網站，網址[https://myapps.microsoft.com](https://myapps.microsoft.com)為。

    ![MyApps 入口網站列出您有許可權的應用程式](./media/review-your-access/myapps-access-panel.png)

1. 在頁面右上角按一下使用者符號，其中顯示您的名稱和預設組織。 如果列出多個組織，請選取已要求存取權檢閱的組織。

1. 在頁面右側，按一下 [**存取評論**] 磚，以查看暫止的存取評論清單。

    如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

    ![應用程式和群組的暫止存取審查清單](./media/review-your-access/access-reviews-list.png)

1. 按一下 [**開始審查**] 連結，以取得您想要執行的存取權審查。

## <a name="perform-the-access-review"></a>執行存取權審查

開啟存取權審查之後，您就可以看到您的存取權。

1. 檢查您的存取權，並決定您是否仍需要存取權。

    如果要求要檢查其他人的存取權，頁面看起來會不同。 如需詳細資訊，請參閱[審查群組或應用程式的存取權](perform-access-review.md)。

    ![開啟存取權審查，詢問您是否仍需要存取群組](./media/review-your-access/perform-access-review.png)

1. 按一下 **[是]** 以保留您的存取權，或按一下 [**否**] 移除您的存取權。

1. 如果您按一下 [**是]**，您可能需要在 [**原因**] 方塊中指定理由。

    ![完整的存取權審查，詢問您是否仍需要群組的存取權](./media/review-your-access/perform-access-review-submit.png)

1. 按一下 [提交]  。

    您的選擇隨即提交，並返回 MyApps 入口網站。

    如果您想要變更您的回應，請重新開啟 [存取評論] 頁面，並更新您的回應。 您可以隨時變更您的回應，直到存取權審查結束為止。

    > [!NOTE]
    > 如果您指出不再需要存取權，就不會立即移除。 當審核結束或系統管理員停止審核時，會將您移除。

## <a name="next-steps"></a>後續步驟

- [完成群組或應用程式的存取權審查](complete-access-review.md)
