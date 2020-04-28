---
title: 將 Azure 資源角色指派給 PIM 中的來賓-Azure AD |Microsoft Docs
description: 瞭解如何邀請外部來賓使用者，並在 Azure AD Privileged Identity Management （PIM）中指派 Azure 資源角色。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74021945"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>邀請來賓使用者並在 Privileged Identity Management 中指派 Azure 資源角色

Azure Active Directory （Azure AD）來賓使用者是 Azure AD 內的企業對企業（B2B）共同作業功能的一部分，因此您可以在 Azure AD 中將外部來賓使用者和廠商管理為來賓。 當您結合 B2B 共同作業與 Azure AD Privileged Identity Management （PIM）時，您可以將合規性和治理需求延伸到來賓。 例如，您可以將這些 Privileged Identity Management 功能用於具有來賓的 Azure 身分識別工作：

- 將存取權指派給特定 Azure 資源
- 啟用 Just-In-Time 存取
- 指定指派持續時間和結束日期
- 在作用中指派或啟用時需要多重要素驗證
- 執行存取權檢閱
- 利用警示和稽核記錄

本文說明如何使用 Privileged Identity Management 來邀請來賓至您的組織，並管理其對 Azure 資源的存取權。

## <a name="when-would-you-invite-guests"></a>您何時會邀請來賓？

以下是您可能會邀請來賓加入貴組織的幾個範例：

- 允許只有電子郵件帳戶的外部自營廠商存取專案的 Azure 資源。
- 允許大型組織中使用內部部署 Active Directory Federation Services 的外部夥伴存取您的費用應用程式。
- 允許不在貴組織 (例如 Microsoft 支援服務) 的支援工程師暫時存取您的 Azure 資源，針對問題進行疑難排解。

## <a name="how-does-collaboration-using-b2b-guests-work"></a>使用 B2B 來賓的共同作業如何運作？

當您使用 B2B 共同作業時，您可以邀請外部使用者以來賓身分提供給您的組織。 來賓可以做為組織中的使用者來管理，但來賓必須在其主要組織中進行驗證，而不是在您的 Azure AD 組織中。 這表示如果來賓不再擁有其主要組織的存取權，他們也會失去貴組織的存取權。 例如，如果來賓離開其組織，他們會自動失去您在 Azure AD 中與他們共用的任何資源的存取權，而不需要執行任何動作。 如需 B2B 共同作業的詳細資訊，請參閱[什麼是 AZURE ACTIVE DIRECTORY B2B 中的來賓使用者存取？](../b2b/what-is-b2b.md)。

![顯示來賓使用者在其主目錄中如何驗證的圖表](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>檢查來賓共同作業設定

若要確保您可以邀請來賓加入您的組織，您應該檢查您的來賓共同作業設定。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 [ **Azure Active Directory** > **使用者設定**]。

1. 選取 [**管理外部**共同作業設定]。

    ![顯示許可權、邀請和共同作業限制設定的 [外部共同作業設定] 頁面](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 確定 [**來賓邀請者] 角色中的 [系統管理員] 和 [使用者可以邀請**] 參數設定為 **[是]**。

## <a name="invite-a-guest-and-assign-a-role"></a>邀請來賓並指派角色

使用 Privileged Identity Management，您可以邀請來賓，使其符合 Azure 資源角色的資格。

1. 使用具有特殊[許可權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)或[使用者系統管理員](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)角色成員的使用者身分登入[Azure 入口網站](https://portal.azure.com/)。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 [Azure 資源]****。

1. 使用 [資源篩選]**** 來篩選管理的資源清單。

1. 選取您想要管理的資源，例如資源、資源群組、訂用帳戶或管理群組。

    您應該將範圍設定為僅限來賓所需的內容。

1. 在 [管理] 底下，選取 [**角色**] 以查看 Azure 資源的角色清單。

    ![Azure 資源角色清單顯示作用中且符合資格的使用者數目](./media/pim-resource-roles-external-users/resources-roles.png)

1. 選取使用者所需的最小角色。

    ![選取的角色頁面，列出該角色的目前成員](./media/pim-resource-roles-external-users/selected-role.png)

1. 在 [角色] 頁面上，選取 [**加入成員**] 以開啟 [新增指派] 窗格。

1. 按一下 [選取成員或群組]****。

    ![新增指派-選取 [成員] 或 [群組] 窗格，列出使用者和群組以及邀請選項](./media/pim-resource-roles-external-users/select-member-group.png)

1. 若要邀請來賓，請按一下 [**邀請**]。

    ![邀請具有方塊的來賓頁面，以輸入電子郵件地址並指定個人訊息](./media/pim-resource-roles-external-users/invite-guest.png)

1. 選取來賓之後，請按一下 [**邀請**]。

    應將來賓新增為選取的成員。

1. 在 [**選取成員或群組**] 窗格中，按一下 [**選取**]。

1. 在 [**成員資格設定**] 窗格中，選取 [指派類型] 和 [持續時間]。

    ![[新增指派-成員資格設定] 頁面，其中包含指定指派類型、開始日期和結束日期的選項](./media/pim-resource-roles-external-users/membership-settings.png)

1. 若要完成指派，請選取 [**完成**]，然後按一下 [**新增**]。

    [來賓] 角色指派會出現在您的角色清單中。

    ![向來賓列出符合資格的角色頁面](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>以來賓身分啟動角色

如果您是外部使用者，您必須接受邀請成為 Azure AD 組織中的來賓，而且可能會啟用您的角色指派。

1. 開啟包含您邀請的電子郵件。 電子郵件會如下所示。

    ![含有目錄名稱、個人訊息和開始使用連結的電子郵件邀請](./media/pim-resource-roles-external-users/email-invite.png)

1. 選取電子郵件中**的 [開始**使用] 連結。

1. 檢閱權限之後，按一下 [接受]****。

    ![瀏覽器中的 [檢查許可權] 頁面，其中包含組織希望您審查的許可權清單](./media/pim-resource-roles-external-users/invite-accept.png)

1. 系統可能會要求您接受使用規定，並指定是否要保持登入。 在 Azure 入口網站中，如果您符合某個角色的*資格*，則您還無法存取資源。

1. 若要啟用您的角色指派，請使用 [啟用角色] 連結開啟電子郵件。 電子郵件會如下所示。

    ![電子郵件，指出您有資格使用啟用角色連結的角色](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 選取 [**啟用角色**] 以在 Privileged Identity Management 中開啟您的合格角色。

    ![列出合格角色 Privileged Identity Management 中的 [我的角色] 頁面](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 在 [動作] 底下，選取 [**啟用**] 連結。

    根據角色設定，您必須指定一些資訊才能啟用該角色。

1. 您指定了角色的設定之後，按一下 [啟用]**** 以啟用角色。

    ![啟用頁面清單範圍和選項以指定開始時間、持續時間和原因](./media/pim-resource-roles-external-users/activate-role.png)

    除非需要系統管理員核准您的要求，否則您應該具有所指定資源的存取權。

## <a name="view-activity-for-a-guest"></a>查看來賓的活動

您可以查看審核記錄，以追蹤來賓正在進行的動作。

1. 以系統管理員身分開啟 Privileged Identity Management，然後選取已共用的資源。

1. 選取 [**資源審核**] 以查看該資源的活動。 下列範例顯示資源群組的活動範例。

    ![Azure 資源-資源 audit 頁面，列出時間、要求者和動作](./media/pim-resource-roles-external-users/audit-resource.png)

1. 若要查看來賓的活動，請選取 [ **Azure Active Directory** > **使用者** > *來賓名稱*]。

1. 選取 [ **audit logs** ] 以查看組織的審核記錄。 如有需要，您可以搜尋篩選條件。

    ![目錄 audit 記錄清單日期、目標、起始者和活動](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 系統管理員角色](pim-how-to-add-role-to-user.md)
- [什麼是 Azure AD B2B 共同作業中的來賓使用者存取權？](../b2b/what-is-b2b.md)
