---
title: 在 PIM 中完成 Azure AD 角色的存取權檢查-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中完成 Azure AD 角色的存取權檢查並查看結果
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742211"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中完成 Azure AD 角色的存取權檢查

在[開始存取權檢閱](pim-how-to-start-security-review.md)之後，特殊權限角色管理員就可以檢閱特殊權限存取權。  Privileged Identity Management （PIM）會自動傳送電子郵件給您 Azure Active Directory （Azure AD）組織中的使用者，以提示他們檢查其存取權。 如果使用者未收到電子郵件，您可以將[如何執行存取權檢閱](pim-how-to-perform-security-review.md)中的指示傳送給他們。

存取權檢閱期間結束後，或所有使用者都已完成其自我檢閱後，請遵循本文的步驟來管理檢閱並查看結果。

## <a name="manage-access-reviews"></a>管理存取權檢閱

1. 移至 [ [Azure 入口網站](https://portal.azure.com/)，然後在儀表板上選取 [ **Azure AD Privileged Identity Management** ] 服務。
1. 選取儀表板的 [存取權檢閱] **** 區段。
1. 選取您想要管理的存取權檢閱。

在 [存取審查] 的 [詳細資料] 分頁上，有數個選項可用於管理該審查。

![Privileged Identity Management 存取權審查按鈕-螢幕擷取畫面](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>提醒

如果將存取權檢閱設定成讓使用者自我檢閱，[提醒] **** 按鈕就會傳送通知。

### <a name="stop"></a>Stop

所有的存取權檢閱都有結束日期，但是您可以使用 [停止] **** 按鈕來提早結束檢閱。 如果此時有任何使用者尚未受到檢閱，在您停止檢閱之後，他們將無法受到檢閱。 在停止檢閱之後，即無法重新開始該檢閱。

### <a name="apply"></a>套用

在存取權檢閱因您已達到結束日期或手動停止它而完成之後，[套用] **** 按鈕就會實作該檢閱的結果。 如果使用者的存取權在檢閱中被拒絕，則在這個步驟就會將其角色指派移除。  

### <a name="export"></a>匯出

如果想要手動套用存取權檢閱的結果，您可以匯出檢閱。 [匯出] **** 按鈕會開始下載 CSV 檔案。 您可以在 Excel 或開啟 CSV 檔案的其他程式中管理結果。

### <a name="delete"></a>刪除

如果對檢閱不再有任何興趣，請刪除它。 [**刪除**] 按鈕會移除 Privileged Identity Management 服務的審查。

> [!IMPORTANT]
> 您不需要確認此破壞性變更，因此請確認您想要刪除該評論。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中啟動 Azure AD 角色的存取權審查](pim-how-to-start-security-review.md)
- [在 Privileged Identity Management 中執行我的 Azure AD 角色的存取權檢查](pim-how-to-perform-security-review.md)
