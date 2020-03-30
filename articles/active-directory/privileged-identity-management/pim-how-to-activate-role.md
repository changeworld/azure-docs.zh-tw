---
title: 在 PIM 中啟動我的 Azure AD 角色 - Azure 活動目錄 |微軟文檔
description: 瞭解如何在 Azure AD 特權標識管理 （PIM） 中啟動 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499138"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>在 PIM 中啟用 Azure AD 角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 簡化了企業管理以特殊權限身分存取 Azure AD 中的資源和其他 Microsoft 線上服務 (如 Office 365 或 Microsoft Intune) 的方式。  

如果您已被設為符合系統管理角色資格，即表示您可以在需要執行特殊權限動作時，啟用該角色。 例如，如果您偶爾會管理 Office 365 功能，則貴組織的特殊權限角色管理員可能不會讓您成為永久全域管理員，因為該角色也會影響其他服務。 他們反而會讓您符合 Azure AD 角色 (例如「Exchange Online 管理員」) 的資格。 您可以在需要權限時，要求啟用該角色，然後您將會在預定的時段內擁有系統管理員控制權。

本文適用于需要在特權標識管理中啟動其 Azure AD 角色的管理員。

## <a name="determine-your-version-of-pim"></a>確定 PIM 的版本

從 2019 年 11 月開始，特權標識管理的 Azure AD 角色部分正在更新為與 Azure 資源角色體驗相匹配的新版本。 這將創建其他功能以及對現有[API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本時，本文中遵循的哪些過程取決於您當前擁有的特權標識管理版本。 按照本節中的步驟確定您擁有的特權標識管理版本。 瞭解特權標識管理版本後，可以選擇本文中與該版本匹配的過程。

1. 使用[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色登錄到[Azure 門戶](https://portal.azure.com/)。
1. 打開**Azure AD 特權標識管理**。 如果概述頁面頂部有橫幅，請按照本文**的"新版本**"選項卡中的說明進行操作。 否則，請按照 **"以前版本**"選項卡中的說明操作。

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="activate-a-role"></a>啟用角色

當您需要承擔 Azure AD 角色時，可以使用"特權標識管理 **"中的"我的角色**導航"選項請求啟動。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。 有關如何將特權標識管理磁貼添加到儀表板的資訊，請參閱[開始使用特權標識管理](pim-getting-started.md)。

1. 選擇 **"我的角色**"，然後選擇**Azure AD 角色**以查看符合條件的 Azure AD 角色的清單。

    ![顯示可啟動的角色的"我的角色"頁面](./media/pim-how-to-activate-role/my-roles.png)

1. 在**Azure AD 角色**清單中，找到要啟動的角色。

    ![Azure AD 角色 - 我的合格角色清單](./media/pim-how-to-activate-role/activate-link.png)

1. 選擇 **"啟動"** 以打開"啟動"窗格。

    ![Azure AD 角色 - 啟動頁包含持續時間和範圍](./media/pim-how-to-activate-role/activate-page.png)

1. 如果您的角色需要多重要素驗證，請選取 [先驗證您的身分識別後再繼續]****。 您只需在每個工作階段驗證一次。

    ![在角色啟動之前，使用 MFA 驗證我的身份](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 選擇 **"驗證我的身份**"並按照說明提供其他安全驗證。

    ![提供安全驗證（如 PIN 代碼）的螢幕](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果要指定縮小的範圍，請選擇 **"範圍"** 以打開篩選器窗格。 在篩選器窗格中，可以指定需要訪問的 Azure AD 資源。 最佳做法是僅請求訪問所需的資源。

1. 如有必要，請指定自訂啟用開始時間。 Azure AD 角色將在選定時間後啟動。

1. 在 [原因]**** 方塊中輸入此啟用要求的原因。

1. 選取 [啟用]****。

    如果角色不需核准，就會啟動並新增至使用中角色的清單。 如果要使用該角色，請按照下一節中的步驟操作。

    ![已完成具有範圍、開始時間、持續時間和原因的啟動窗格](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    如果[角色需要核准](pim-resource-roles-approval-workflow.md)才能啟用，通知會出現在瀏覽器右上角，通知您要求正在等待核准。

    ![啟動請求正在等待審批通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>啟用後可立即使用角色

如果啟動後有任何延遲，請按照以下步驟啟動後立即使用 Azure AD 角色。

1. 開啟 Azure AD Privileged Identity Management。

1. 選擇 **"我"角色**以查看符合條件的 Azure AD 角色和 Azure 資源角色的清單。

1. 選擇**Azure AD 角色**。

1. 選擇"**活動角色**"選項卡。

1. 角色處於活動狀態後，登出門戶並重新登錄。

    該角色現在應可供使用。

## <a name="view-the-status-of-your-requests"></a>檢視要求狀態

您可以檢視要啟用的擱置要求狀態。

1. 開啟 Azure AD Privileged Identity Management。

1. 選擇 **"我的請求**"以查看 Azure AD 角色和 Azure 資源角色請求的清單。

    ![我的請求 - 顯示掛起請求的 Azure AD 頁面](./media/pim-how-to-activate-role/my-requests-page.png)

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

    啟動 Azure AD 角色時，您將看到啟動的各個階段。 當所有階段完成後，您會看到 [登出]**** 連結。 您可以使用此連結登出。這將解決大多數啟動延遲的情況。

1. 在特權標識管理中，驗證您是否被列為角色的成員。

# <a name="previous-version"></a>[早期版本](#tab/previous)

## <a name="activate-a-role"></a>啟用角色

當您需要承擔 Azure AD 角色時，可以使用"特權標識管理"中的 **"我的角色**導航"選項請求啟動。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。 有關如何將特權標識管理磁貼添加到儀表板的資訊，請參閱[開始使用特權標識管理](pim-getting-started.md)。

1. 按一下 [Azure AD 角色]****。

1. 按一下 **"我的角色**"以查看符合條件的 Azure AD 角色的清單。

    ![Azure AD 角色 - 顯示合格或活動角色清單的我的角色](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. 尋找您想要啟用的角色。

    ![Azure AD 角色 - 顯示"啟動"連結的合格角色清單](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. 按一下 [啟用]**** 以開啟角色啟用詳細資料窗格。

1. 如果您的角色需要多重要素驗證 (MFA)，請按一下 [先驗證您的身分識別後再繼續]****。 您只需在每個工作階段驗證一次。

    ![在角色啟動之前，使用 MFA 驗證我的標識窗格](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. 按一下 [驗證我的身分識別]****，並遵循指示來提供其他安全性驗證。

    ![詢問如何與您聯繫的其他安全驗證頁面](./media/pim-how-to-activate-role/additional-security-verification.png)

1. 按一下 [啟用]**** 以開啟啟用窗格。

    ![啟動窗格，用於指定開始時間、持續時間、票證和原因](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 如有必要，請指定自訂啟用開始時間。

1. 指定啟用持續時間。

1. 在 [啟用原因]**** 方塊中輸入此啟用要求的原因。 有些角色會要求您提供問題票證號碼。

    ![已完成的啟動窗格，包含自訂啟動時間、持續時間、票證和原因](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. 按一下 [啟動]****。

    如果角色不需要審批，則會出現顯示啟動狀態的**啟動狀態**窗格。

    ![顯示啟動三個階段的啟動狀態頁](./media/pim-how-to-activate-role/activation-status.png)

    完成所有階段後，按一下 **"登出**"連結以登出 Azure 門戶。 當您重新登錄到門戶時，您現在可以使用該角色。

    如果[角色需要批准](./azure-ad-pim-approval-workflow.md)才能啟動，則瀏覽器右上角將顯示 Azure 通知，通知您請求正在等待審批。

## <a name="view-the-status-of-your-requests"></a>檢視要求狀態

您可以檢視要啟用的擱置要求狀態。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [Azure AD 角色]****。

1. 按一下 [我的要求]**** 以查看要求清單。

    ![Azure AD 角色 - 我的請求清單](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>停用角色

角色一經啟用，就會在達到時間限制 (合格的持續時間) 時自動停用。

如果您提早完成系統管理員工作，也可以在 Azure AD Privileged Identity Management 中手動停用角色。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [Azure AD 角色]****。

1. 按一下 [我的角色]****。

1. 按一下 [使用中的角色]****，以查看使用中的角色清單。

1. 尋找您使用完畢的角色，然後按一下 [停用]****。

## <a name="cancel-a-pending-request"></a>取消擱置要求

如果您不需要啟用需要核准的角色，您可以隨時取消擱置要求。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [Azure AD 角色]****。

1. 按一下 [我的要求]****。

1. 針對您想要取消的角色，按一下 [取消]**** 按鈕。

    按一下"取消"時，請求將被取消。 若要再次啟用角色，您必須提交新的啟用要求。

   ![突出顯示"取消"按鈕的請求清單](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>疑難排解

### <a name="permissions-are-not-granted-after-activating-a-role"></a>啟用角色之後未授與權限

啟動特權標識管理中的角色時，啟動可能不會立即傳播到需要特權角色的所有門戶。 有時候，即使該變更已傳遞，入口網站中的 Web 快取也可能導致變更無法立即生效。 如果您的啟動被延遲，下面是您應該做的。

1. 登出 Azure 入口網站，然後重新登入。

    啟動 Azure AD 角色時，您將看到啟動的各個階段。 當所有階段完成後，您會看到 [登出]**** 連結。 您可以使用此連結登出。這將解決大多數啟動延遲的情況。

1. 在特權標識管理中，驗證您是否被列為角色的成員。

 ---

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中啟動 Azure AD 角色](pim-how-to-activate-role.md)
