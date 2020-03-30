---
title: 查看對 PIM 中 Azure 資源角色的訪問 - Azure AD |微軟文檔
description: 瞭解如何在 Azure AD 特權標識管理 （PIM） 中查看 Azure 資源角色的訪問。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847008"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>查看對特權標識管理中的 Azure 資源角色的訪問

特權標識管理 （PIM） 訪問審核可説明保護對 Azure 活動目錄 （Azure AD） 中特權角色的訪問。 本文介紹在 Azure AD 訪問審閱中完成審核特權角色指派的步驟。

如果您被分配到管理角色，則可能需要管理員完成訪問審核，以確認是否需要角色。 確認請求可以附帶包含連結的電子郵件，也可以在[Azure 門戶](https://portal.azure.com)中進行確認。

如果您是對存取權檢閱感興趣的特殊權限角色管理員，請在 [如何開始存取權檢閱](pim-resource-roles-start-access-review.md)中取得更多詳細資訊。

## <a name="approve-or-deny-access"></a>核准或拒絕存取

您可以根據是否仍使用此角色來批准或拒絕存取權限。 如果您想要繼續擔任此角色，請選擇 [核准]****，如果您不再需要此存取權，則請選擇 [拒絕]****。 只有在檢閱者應用結果後，您的狀態才會更改。

請依照下列步驟來尋找並完成存取權檢閱︰

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
1. 選擇**Azure 活動目錄**並打開**特權標識管理**。
1. 選擇 **"審閱存取權限**"。

   ![特權身份管理應用程式的螢幕截圖，並選擇了查看訪問邊欄選項卡](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. 選取您想要完成的檢閱。
1. 選擇 **"批准**"或 **"拒絕**"。 在 **"提供原因"框中**，根據需要輸入決策的業務理由。

   ![檢閱詳細資料頁面的螢幕擷取畫面](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中對 Azure AD 角色執行訪問審查](pim-how-to-perform-security-review.md)
