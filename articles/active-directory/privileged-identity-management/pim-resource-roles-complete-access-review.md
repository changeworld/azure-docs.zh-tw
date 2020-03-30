---
title: 完成 PIM 中 Azure 資源角色的訪問審查 - Azure AD |微軟文檔
description: 瞭解如何在 Azure 活動目錄中完成對 Azure 資源角色特權標識管理的訪問審查。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021989"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>完成特權標識管理中 Azure 資源角色的訪問審核

特權角色管理員可以在[開始訪問審查](pim-resource-roles-start-access-review.md)後查看特權存取權限。 Azure 活動目錄 （Azure AD） 中的特權標識管理 （PIM） 會自動發送電子郵件，提示使用者查看其存取權限。 如果使用者未收到電子郵件，您可以將[如何執行存取權檢閱](pim-resource-roles-perform-access-review.md)中的指示傳送給他們。

存取權檢閱期間結束後，或所有使用者都已完成其自我檢閱後，請遵循本文的步驟來管理檢閱並查看結果。

## <a name="manage-access-reviews"></a>管理存取權檢閱

1. 轉到[Azure 門戶](https://portal.azure.com/)。 在儀表板上，選擇**Azure 資源**服務。

2. 選取您的資源。

3. 選取儀表板的 [存取權檢閱] **** 區段。

    ![Azure 資源 - 訪問審核清單，顯示角色、擁有者、開始日期、結束日期和狀態](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. 選取您想要管理的存取權檢閱。

在訪問審核的詳細資訊頁上，有許多選項可用於管理該審核。 選項如下：

![管理審核的選項 - 停止、重置、應用、刪除](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stop

所有訪問審核都有結束日期。 選擇 **"停止"** 以提前完成它。 任何此時尚未完成審核的使用者在停止審核後將無法完成審核。 在停止檢閱之後，即無法重新開始檢閱。

### <a name="reset"></a>重設

您可以重設存取權檢閱，以移除在其上所做的所有決策。 重置訪問審核後，所有使用者將標記為不再審核。

### <a name="apply"></a>套用

訪問審核完成後，選擇 **"應用"** 以實施審核結果。 如果使用者的存取權在檢閱中遭拒，則此步驟就會將其角色指派移除。  

### <a name="delete"></a>刪除

如果對檢閱不再有任何興趣，請將其刪除。 選擇 **"刪除**"從特權身份管理服務中刪除審核。

## <a name="results"></a>結果

在 **"結果"** 頁上，查看並下載審核結果的清單。

![結果頁列出使用者、結果、原因、審閱、應用和應用結果](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>檢閱者

檢視檢閱者並將其新增至現有的存取權檢閱。 提醒檢閱者完成其檢閱。

![檢閱者頁面清單名稱和使用者主體名稱](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中啟動 Azure 資源角色的訪問審查](pim-resource-roles-start-access-review.md)
- [在特權標識管理中對 Azure 資源角色執行訪問審查](pim-resource-roles-perform-access-review.md)
