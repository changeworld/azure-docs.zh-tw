---
title: 在 PIM - Azure AD 中為來賓分配 Azure 資源角色 |微軟文檔
description: 瞭解如何邀請外部來賓使用者並在 Azure AD 特權標識管理 （PIM） 中分配 Azure 資源角色。
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
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021945"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>邀請來賓使用者並在特權標識管理中分配 Azure 資源角色

Azure 活動目錄 （Azure AD） 來賓使用者是 Azure AD 中的企業對企業 （B2B） 協作功能的一部分，因此您可以在 Azure AD 中管理外部來賓使用者和供應商作為來賓。 將 B2B 協作與 Azure AD 特權標識管理 （PIM） 相結合時，可以將合規性和治理要求擴展到來賓。 例如，您可以將這些特權標識管理功能用於 Azure 標識任務，以便與來賓執行以下功能：

- 將存取權指派給特定 Azure 資源
- 啟用 Just-In-Time 存取
- 指定指派持續時間和結束日期
- 在活動分配或啟動時需要多重要素驗證
- 執行存取權檢閱
- 利用警示和稽核記錄

本文介紹如何邀請來賓加入您的組織，並使用特權標識管理管理他們對 Azure 資源的訪問。

## <a name="when-would-you-invite-guests"></a>你什麼時候邀請客人？

以下是您何時邀請來賓加入您的組織的幾個例子：

- 允許只有電子郵件帳戶的外部自營廠商存取專案的 Azure 資源。
- 允許大型組織中使用內部部署 Active Directory Federation Services 的外部夥伴存取您的費用應用程式。
- 允許不在貴組織 (例如 Microsoft 支援服務) 的支援工程師暫時存取您的 Azure 資源，針對問題進行疑難排解。

## <a name="how-does-collaboration-using-b2b-guests-work"></a>使用 B2B 來賓的協作如何工作？

使用 B2B 協作時，可以邀請外部使用者作為來賓加入您的組織。 來賓可以作為組織中的使用者進行管理，但來賓必須在其主組織中進行身份驗證，而不是在 Azure AD 組織中進行身份驗證。 這意味著，如果來賓不再有權訪問其家庭組織，他們也會無法訪問您的組織。 例如，如果來賓離開其組織，他們將自動無法訪問您在 Azure AD 中與他們共用的任何資源，而無需執行任何操作。 有關 B2B 協作的詳細資訊，請參閱[Azure 活動目錄 B2B 中的來賓使用者訪問是什麼？](../b2b/what-is-b2b.md)

![顯示來賓使用者在其主目錄中如何進行身份驗證的圖表](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>檢查來賓協作設置

為了確保可以邀請來賓加入您的組織，應檢查來賓協作設置。

1. 登錄到[Azure 門戶](https://portal.azure.com/)。

1. 選擇**Azure 活動目錄** > **使用者設置**。

1. 選擇 **"管理外部協作設置**"。

    ![顯示許可權、邀請和協作限制設置的外部協作設置頁面](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 確保**來賓邀請者角色的管理員和使用者可以邀請**切換設置為 **"是**"。

## <a name="invite-a-guest-and-assign-a-role"></a>邀請來賓並分配角色

使用特權標識管理，可以邀請來賓，並使他們有資格擔任 Azure 資源角色。

1. 使用[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)或[使用者管理員](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)角色的成員登錄到[Azure 門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 選取 [Azure 資源]****。

1. 使用 [資源篩選]**** 來篩選管理的資源清單。

1. 選擇要管理的資源，如資源、資源組、訂閱或管理組。

    您應該將範圍設置為僅滿足客人的需求。

1. 在"管理"下，選擇**角色**以查看 Azure 資源的角色清單。

    ![Azure 資源角色清單，顯示處於活動狀態且符合條件的使用者數](./media/pim-resource-roles-external-users/resources-roles.png)

1. 選擇使用者所需的最小角色。

    ![列出該角色的當前成員的選定角色頁面](./media/pim-resource-roles-external-users/selected-role.png)

1. 在角色頁上，選擇 **"添加成員**"以打開"新建分配"窗格。

1. 按一下 [選取成員或群組]****。

    ![新分配 - 選擇列出使用者和組的成員或組窗格以及"邀請"選項](./media/pim-resource-roles-external-users/select-member-group.png)

1. 要邀請來賓，請按一下"**邀請**"。

    ![邀請帶框的來賓頁面輸入電子郵件地址並指定個人郵件](./media/pim-resource-roles-external-users/invite-guest.png)

1. 選擇來賓後，按一下"**邀請**"。

    來賓應添加為選定成員。

1. 在"**選擇成員或組**"窗格中，按一下"**選擇**"。

1. 在 **"成員資格設置"** 窗格中，選擇分配類型和持續時間。

    ![新分配 - 成員設置頁面，具有指定分配類型、開始日期和結束日期的選項](./media/pim-resource-roles-external-users/membership-settings.png)

1. 要完成分配，請選擇 **"完成"，** 然後**添加**。

    來賓角色指派將顯示在您的角色清單中。

    ![將來賓列為合格角色頁](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>啟動來賓角色

如果您是外部使用者，則必須接受邀請才能成為 Azure AD 組織中的客人，並可能啟動角色指派。

1. 打開電子郵件與您的邀請。 電子郵件會如下所示。

    ![使用目錄名稱、個人消息和入門連結發送電子郵件邀請](./media/pim-resource-roles-external-users/email-invite.png)

1. 在電子郵件中選擇 **"入門"** 連結。

1. 檢閱權限之後，按一下 [接受]****。

    ![查看瀏覽器中的許可權頁，其中包含組織希望您查看的許可權清單](./media/pim-resource-roles-external-users/invite-accept.png)

1. 系統可能會要求您接受使用條款，並指定是否要保持登錄狀態。 在 Azure 門戶中，如果您*有資格*擔任角色，則您尚未有權訪問資源。

1. 要啟動角色指派，請使用啟動角色連結打開電子郵件。 電子郵件會如下所示。

    ![顯示您有資格使用"啟動"角色連結的角色的電子郵件](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 選擇 **"啟動角色"** 以在特權身份管理中打開符合條件的角色。

    ![我在特權身份管理中的角色頁面列出您的合格角色](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 在"操作"下，選擇 **"啟動"** 連結。

    根據角色設定，您必須指定一些資訊才能啟用該角色。

1. 您指定了角色的設定之後，按一下 [啟用]**** 以啟用角色。

    ![啟動頁面清單範圍和選項以指定開始時間、持續時間和原因](./media/pim-resource-roles-external-users/activate-role.png)

    除非需要系統管理員核准您的要求，否則您應該具有所指定資源的存取權。

## <a name="view-activity-for-a-guest"></a>查看來賓的活動

您可以查看稽核記錄，以跟蹤來賓正在執行的操作。

1. 作為管理員，打開特權標識管理並選擇已共用的資源。

1. 選擇 **"資源審核**"以查看該資源的活動。 下列範例顯示資源群組的活動範例。

    ![Azure 資源 - 資源審核頁面列出時間、要求者和操作](./media/pim-resource-roles-external-users/audit-resource.png)

1. 要查看來賓的活動，請選擇**Azure 活動目錄** > **使用者** > *來賓名稱*。

1. 選擇**稽核記錄**以查看組織的稽核記錄。 如有需要，您可以搜尋篩選條件。

    ![目錄稽核記錄列出日期、目標、啟動日期和活動](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中分配 Azure AD 管理員角色](pim-how-to-add-role-to-user.md)
- [Azure AD B2B 協作中的訪客使用者訪問是什麼？](../b2b/what-is-b2b.md)
