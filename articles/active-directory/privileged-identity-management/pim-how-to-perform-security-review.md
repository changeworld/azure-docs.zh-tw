---
title: 在 PIM 中審查 Azure AD 角色的存取權-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中檢查 Azure Active Directory 角色的存取權。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 04/24/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4096c65fa4375152f44891d4a0a5aac4b9077cf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743842"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中審查 Azure AD 角色的存取權

Privileged Identity Management （PIM）簡化了企業如何管理 Azure Active Directory （AD）和其他 Microsoft 線上服務（例如 Office 365 或 Microsoft Intune）中資源的特殊許可權存取。 請遵循這篇文章中的步驟，成功自我審查您指派的角色。

如果您已被指派系統管理角色，貴組織的特殊權限角色管理員可能會要求您定期確認您仍需要該角色來執行作業。 您可能會收到包含連結的電子郵件，或者您可以直接前往[Azure 入口網站](https://portal.azure.com)並開始。

如果您是對存取權檢閱感興趣的特殊權限角色管理員或全域管理員，請在[如何開始存取權檢閱](pim-how-to-start-security-review.md)中取得更多詳細資料。

## <a name="add-a-pim-dashboard-tile"></a>新增 PIM 儀表板磚

如果您未在 Azure 入口網站中將 Privileged Identity Management 服務釘選到儀表板，請遵循下列步驟來開始使用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站的右上角選取您的 [使用者名稱]，然後選取您將在其中操作的 Azure AD 組織。
3. 選取 [所有服務]**** 並使用 [篩選器] 文字方塊來搜尋 [Azure AD Privileged Identity Management]****。
4. 選取 [釘選到儀表板]****，然後按一下 [建立]****。 Privileged Identity Management 應用程式隨即開啟。

## <a name="approve-or-deny-access"></a>核准或拒絕存取

當您核准或拒絕存取權時，只是在告訴檢閱者您是否仍然使用此角色。 如果您想要繼續擔任此角色，請選擇 [核准]****，如果您不再需要此存取權，則請選擇 [拒絕]****。 您的狀態不會立即變更，必須等到檢閱者套用結果之後才會變更。
請依照下列步驟來尋找並完成存取權檢閱︰

1. 在 [Privileged Identity Management] 服務中，選取 [審查特殊許可權**存取**]。 如果您有任何暫止的存取權審查，它們會出現在 [Azure AD**存取評論**] 頁面中。
2. 選取您想要完成的檢閱。
3. 除非該檢閱是您所建立，否則您會顯示為該檢閱中的唯一使用者。 選取您名稱旁邊的核取記號。
4. 選擇 [核准]**** 或 [拒絕]****。 您可能需要在 [提供原因] **** 文字方塊中提供您的決定原因。  
5. 關閉 [檢閱 Azure AD 角色] **** 刀鋒視窗。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中執行 Azure 資源角色的存取權檢閱](pim-resource-roles-perform-access-review.md)
