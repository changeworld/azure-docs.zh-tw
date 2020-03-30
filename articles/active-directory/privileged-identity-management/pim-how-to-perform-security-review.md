---
title: 查看對 PIM 中 Azure AD 角色的訪問 - Azure AD |微軟文檔
description: 瞭解如何查看 Azure AD 特權標識管理 （PIM） 中 Azure 活動目錄角色的訪問。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847098"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>查看對特權標識管理中 Azure AD 角色的訪問

特權標識管理 （PIM） 簡化了企業如何管理對 Azure 活動目錄 （AD） 和其他 Microsoft 線上服務（如 Office 365 或 Microsoft Intune）中的資源的特權訪問。 按照本文中的步驟成功自查看分配的角色。

如果您已被指派系統管理角色，貴組織的特殊權限角色管理員可能會要求您定期確認您仍需要該角色來執行作業。 您可能會收到包含連結的電子郵件，也可以直接轉到[Azure 門戶](https://portal.azure.com)並開始。

如果您是對存取權檢閱感興趣的特殊權限角色管理員或全域管理員，請在[如何開始存取權檢閱](pim-how-to-start-security-review.md)中取得更多詳細資料。

## <a name="add-a-pim-dashboard-tile"></a>添加 PIM 儀表板磁貼

如果未將 Azure AD 特權標識管理服務固定到 Azure 門戶中的儀表板，請按照以下步驟開始。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 選取 Azure 入口網站右上角的使用者名稱，然後選取您要操作的目錄。
3. 選取 [所有服務]**** 並使用 [篩選器] 文字方塊來搜尋 [Azure AD Privileged Identity Management]****。
4. 選取 [釘選到儀表板]****，然後按一下 [建立]****。 Privileged Identity Management 應用程式隨即開啟。

## <a name="approve-or-deny-access"></a>核准或拒絕存取

當您核准或拒絕存取權時，只是在告訴檢閱者您是否仍然使用此角色。 如果您想要繼續擔任此角色，請選擇 [核准]****，如果您不再需要此存取權，則請選擇 [拒絕]****。 您的狀態不會立即變更，必須等到檢閱者套用結果之後才會變更。
請依照下列步驟來尋找並完成存取權檢閱︰

1. 在特權身份管理服務中，選擇 **"查看特權存取權限**"。 如果您有任何掛起的訪問審核，它們將顯示在 Azure AD**訪問審核**頁中。
2. 選取您想要完成的檢閱。
3. 除非該檢閱是您所建立，否則您會顯示為該檢閱中的唯一使用者。 選取您名稱旁邊的核取記號。
4. 選擇 [核准]**** 或 [拒絕]****。 您可能需要在 [提供原因] **** 文字方塊中提供您的決定原因。  
5. 關閉 [檢閱 Azure AD 角色] **** 刀鋒視窗。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

- [在 PIM 中執行 Azure 資源角色的存取權檢閱](pim-resource-roles-perform-access-review.md)
