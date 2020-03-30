---
title: 完成 PIM 中 Azure AD 角色的訪問審閱 - Azure AD |微軟文檔
description: 瞭解如何在 Azure AD 特權標識管理 （PIM） 中完成 Azure AD 角色的訪問審核並查看結果
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022272"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>完成對特權標識管理中的 Azure AD 角色的訪問審查

在[開始存取權檢閱](pim-how-to-start-security-review.md)之後，特殊權限角色管理員就可以檢閱特殊權限存取權。  特權標識管理 （PIM） 將自動向 Azure 活動目錄 （Azure AD） 組織中的使用者發送電子郵件，提示他們查看其存取權限。 如果使用者未收到電子郵件，您可以將[如何執行存取權檢閱](pim-how-to-perform-security-review.md)中的指示傳送給他們。

存取權檢閱期間結束後，或所有使用者都已完成其自我檢閱後，請遵循本文的步驟來管理檢閱並查看結果。

## <a name="manage-access-reviews"></a>管理存取權檢閱

1. 轉到[Azure 門戶](https://portal.azure.com/)並在儀表板上選擇 Azure **AD 特權標識管理**服務。
1. 選取儀表板的 [存取權檢閱] **** 區段。
1. 選取您想要管理的存取權檢閱。

在訪問審閱的詳細資訊邊欄選項卡上，有許多選項可用於管理該審核。

![特權身份管理訪問審閱按鈕 - 螢幕截圖](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>提醒

如果將存取權檢閱設定成讓使用者自我檢閱，[提醒] **** 按鈕就會傳送通知。

### <a name="stop"></a>Stop

所有的存取權檢閱都有結束日期，但是您可以使用 [停止] **** 按鈕來提早結束檢閱。 如果此時有任何使用者尚未受到檢閱，在您停止檢閱之後，他們將無法受到檢閱。 在停止檢閱之後，即無法重新開始該檢閱。

### <a name="apply"></a>套用

在存取權檢閱因您已達到結束日期或手動停止它而完成之後，[套用] **** 按鈕就會實作該檢閱的結果。 如果使用者的存取權在檢閱中被拒絕，則在這個步驟就會將其角色指派移除。  

### <a name="export"></a>匯出

如果想要手動套用存取權檢閱的結果，您可以匯出檢閱。 [匯出] **** 按鈕會開始下載 CSV 檔案。 您可以在 Excel 或開啟 CSV 檔案的其他程式中管理結果。

### <a name="delete"></a>刪除

如果對檢閱不再有任何興趣，請刪除它。 "**刪除**"按鈕從特權標識管理服務中刪除審核。

> [!IMPORTANT]
> 您不需要確認此破壞性更改，因此請驗證是否要刪除該審核。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中啟動 Azure AD 角色的訪問審核](pim-how-to-start-security-review.md)
- [在特權標識管理中對 Azure AD 角色執行訪問審查](pim-how-to-perform-security-review.md)
