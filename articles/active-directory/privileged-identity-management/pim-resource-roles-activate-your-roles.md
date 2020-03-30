---
title: 在 PIM 中啟動 Azure 資源角色 - Azure AD |微軟文檔
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中啟用 Azure 資源角色。
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
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023140"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>在特權標識管理中啟動 Azure 資源角色

使用特權標識管理 （PIM） 允許 Azure 資源的合格角色成員為將來的日期和時間安排啟動。 他們也可以在最大範圍內選取特定的啟用期間 (由管理員設定)。

本文適用于需要在特權標識管理中啟動其 Azure 資源角色的成員。

## <a name="activate-a-role"></a>啟用角色

當您需要承擔 Azure 資源角色時，可以使用特權標識管理中的 **"我的角色**"導航選項請求啟動。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。 有關如何將特權標識管理磁貼添加到儀表板的資訊，請參閱[開始使用特權標識管理](pim-getting-started.md)。

1. 選擇**我的角色**。

    ![顯示可啟動的角色的"我的角色"頁面](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 選擇**Azure 資源角色**以查看符合條件的 Azure 資源角色的清單。

   ![我的角色 - Azure 資源角色頁面](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. 在 [Azure 資源角色]**** 清單中，尋找您要啟用的角色。

    ![Azure 資源角色 - 我的合格角色清單](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 選擇 **"啟動"** 以打開"啟動"窗格。

1. 如果您的角色需要多重要素驗證，請選取 [先驗證您的身分識別後再繼續]****。 您只需在每個工作階段驗證一次。

    ![在角色啟動之前，使用 MFA 驗證我的身份](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 選擇 **"驗證我的身份**"並按照說明提供其他安全驗證。

    ![提供安全驗證（如 PIN 代碼）的螢幕](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果要指定縮小的範圍，請選擇 **"範圍"** 以打開"資源"篩選器窗格。

    最佳做法是僅要求存取您需要的資源。 在 [資源] 篩選窗格中，您可以指定資源群組或您需要存取的群組。

    ![啟動 - 資源篩選器窗格以指定範圍](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 如有必要，請指定自訂啟用開始時間。 成員會在選取的時間後啟用。

1. 在 [原因]**** 方塊中輸入此啟用要求的原因。

    ![已完成具有範圍、開始時間、持續時間和原因的啟動窗格](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. 選取 [啟用]****。

    如果角色不需核准，就會啟動並新增至使用中角色的清單。 如果要使用該角色，請按照下一節中的步驟操作。

    如果[角色需要核准](pim-resource-roles-approval-workflow.md)才能啟用，通知會出現在瀏覽器右上角，通知您要求正在等待核准。

    ![啟動請求正在等待審批通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>啟用後可立即使用角色

如果啟動後有任何延遲，請按照以下步驟啟動後立即使用 Azure 資源角色。

1. 開啟 Azure AD Privileged Identity Management。

1. 選擇 **"我"角色**以查看符合條件的 Azure AD 角色和 Azure 資源角色的清單。

1. 選擇**Azure 資源角色**。

1. 選擇"**活動角色**"選項卡。

1. 角色處於活動狀態後，登出門戶並重新登錄。

    該角色現在應可供使用。

## <a name="view-the-status-of-your-requests"></a>檢視要求狀態

您可以檢視要啟用的擱置要求狀態。

1. 開啟 Azure AD Privileged Identity Management。

1. 選擇 **"我的請求**"以查看 Azure AD 角色和 Azure 資源角色請求的清單。

    ![我的請求 - 顯示掛起請求的 Azure 資源頁](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 捲動至右側可檢視 [要求狀態]**** 欄。

## <a name="cancel-a-pending-request"></a>取消擱置要求

如果您不需要啟用需要核准的角色，您可以隨時取消擱置要求。

1. 開啟 Azure AD Privileged Identity Management。

1. 選擇 **"我的請求**"。

1. 對於要取消的角色，請選擇 **"取消"** 連結。

    當您選擇"取消"時，請求將被取消。 若要再次啟用角色，您必須提交新的啟用要求。

   ![突出顯示了"取消"操作的請求清單](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>疑難排解

### <a name="permissions-are-not-granted-after-activating-a-role"></a>啟用角色之後未授與權限

啟動特權標識管理中的角色時，啟動可能不會立即傳播到需要特權角色的所有門戶。 有時候，即使該變更已傳遞，入口網站中的 Web 快取也可能導致變更無法立即生效。 如果您的啟動被延遲，下面是您應該做的。

1. 登出 Azure 入口網站，然後重新登入。

    啟動 Azure 資源角色時，您將看到啟動的各個階段。 當所有階段完成後，您會看到 [登出]**** 連結。 您可以使用此連結登出。這將解決大多數啟動延遲的情況。

1. 在特權標識管理中，驗證您是否被列為角色的成員。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中擴展或續訂 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [在特權標識管理中啟動 Azure AD 角色](pim-how-to-activate-role.md)
