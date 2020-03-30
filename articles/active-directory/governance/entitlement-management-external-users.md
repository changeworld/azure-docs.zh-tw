---
title: 在 Azure AD 授權管理中管理外部使用者的訪問 - Azure 活動目錄
description: 瞭解為在 Azure 活動目錄授權管理中管理外部使用者的訪問而指定的設置。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128689"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中管理外部使用者的存取權限

Azure AD 授權管理利用[Azure AD 企業對企業 （B2B）](../b2b/what-is-b2b.md)與另一個目錄中的組織外部人員協作。 使用 Azure AD B2B，外部使用者對其主目錄進行身份驗證，但在目錄中具有表示形式。 目錄中的表示形式使使用者能夠分配對資源的訪問。

本文介紹了為控制外部使用者的訪問而可以指定的設置。

## <a name="how-entitlement-management-can-help"></a>授權管理如何提供説明

使用[Azure AD B2B](../b2b/what-is-b2b.md)邀請體驗時，必須已經知道要引入資原始目錄並處理的外部來賓使用者的電子郵件地址。 當您正在處理一個較小的或短期的專案，並且您已經知道所有參與者時，這非常有效，但是，如果您有大量要使用的使用者，或者參與者隨時間而變化，則很難管理。  例如，您可能正在與其他組織合作，並且與該組織有一個聯繫點，但隨著時間的推移，該組織的其他使用者也需要存取權限。

使用授權管理，您可以定義一個策略，允許指定組織的使用者能夠自行請求訪問包。 您可以指定是否需要批准以及訪問到期日期。 如果需要批准，您還可以邀請一個或多個使用者從外部組織到您的目錄，並將他們指定為核准者 -因為他們可能知道組織中哪些外部使用者需要訪問。 配置訪問包後，可以將訪問包的連結發送給外部組織的連絡人（贊助者）。 該連絡人可以與外部組織中的其他使用者共用，並且他們可以使用此連結請求訪問包。 已被邀請加入目錄的來自該組織的使用者也可以使用該連結。

請求獲得批准後，授權管理將為使用者提供必要的存取權限，其中可能包括邀請使用者（如果使用者尚未在目錄中）。 Azure AD 將自動為其創建 B2B 來賓帳戶。 請注意，管理員以前可能通過設置[B2B 允許或拒絕清單](../b2b/allow-deny-list.md)來允許或阻止對其他組織的邀請來限制哪些組織允許協作。  如果允許或阻止清單不允許使用者，則不會邀請他們。

由於不希望外部使用者的訪問永遠持續，因此在策略中指定到期日期，例如 180 天。 180 天后，如果訪問未擴展，授權管理將刪除與該訪問包關聯的所有存取權限。 預設情況下，如果通過授權管理邀請的使用者沒有其他訪問包分配，則當他們失去上次分配時，其來賓帳戶將被阻止登錄 30 天，然後被刪除。 這可以防止不必要的帳戶激增。 如以下各節所述，這些設置是可配置的。

## <a name="how-access-works-for-external-users"></a>訪問對外部使用者的工作原理

下圖和步驟概述了如何授予外部使用者訪問包存取權限。

![顯示外部使用者生命週期的圖表](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. 為要協作的 Azure AD 目錄或域[添加已連接的組織](entitlement-management-organization.md)。

1. 在目錄中創建一個訪問包，其中包含[針對不在目錄中的使用者](entitlement-management-access-package-create.md#for-users-not-in-your-directory)的策略。

1. 您將["我的訪問"門戶連結](entitlement-management-access-package-settings.md)發送給外部組織的連絡人，使用者可以與其使用者共用該連結以請求訪問包。

1. 外部使用者（本示例中**的請求器 A）** 使用"我的訪問"門戶連結[請求訪問](entitlement-management-request-access.md)包。 使用者登錄方式取決於連接組織中定義的目錄或域的身份驗證類型。

1. 審批人[批准請求](entitlement-management-request-approve.md)（或請求是自動批准的）。

1. 請求進入[傳遞狀態](entitlement-management-process.md)。

1. 使用 B2B 邀請過程，在目錄中創建來賓使用者帳戶（本示例中的請求**器 A（來賓）。** 如果定義了[允許清單或拒絕清單](../b2b/allow-deny-list.md)，將應用清單設置。

1. 來賓使用者被分配對訪問包中的所有資源的存取權限。 在 Azure AD 和其他 Microsoft 線上服務或連接的 SaaS 應用程式中進行更改可能需要一些時間。 有關詳細資訊，請參閱[何時應用更改](entitlement-management-access-package-resources.md#when-changes-are-applied)。

1. 外部使用者收到一封電子郵件，指示其存取權限已[送達](entitlement-management-process.md)。

1. 要訪問資源，外部使用者可以按一下電子郵件中的連結，也可以嘗試直接存取任何目錄資源以完成邀請過程。

1. 根據策略設置，隨著時間的推移，外部使用者的訪問包分配將過期，並刪除外部使用者的存取權限。

1. 根據外部使用者設置的生命週期，當外部使用者不再具有任何訪問包分配時，外部使用者將被阻止登錄，來賓使用者帳戶將從目錄中刪除。

## <a name="settings-for-external-users"></a>外部使用者的設置

為了確保組織外部的人員可以請求訪問包並訪問這些訪問包中的資源，應驗證某些設置是否正確配置。

### <a name="enable-catalog-for-external-users"></a>為外部使用者啟用目錄

- 預設情況下，當您[創建新目錄](entitlement-management-catalog-create.md)時，它啟用允許外部使用者請求目錄中的訪問包。 確保**為外部使用者啟用**設置為 **"是**"。

    ![編輯目錄設置](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>配置 Azure AD B2B 外部協作設置

- 允許客人邀請其他客人加入您的目錄意味著客人邀請可能發生在權利管理之外。 我們建議設置 **"來賓可以邀請**到 **""** 僅允許正確管理的邀請。
- 如果使用 B2B 允許清單，必須確保要與使用授權管理合作的任何域都添加到清單中。 或者，如果您使用的是 B2B 拒絕清單，則必須確保要與任何要合作的域不會添加到清單中。
- 如果為**所有使用者**（所有連接的組織 + 任何新的外部使用者）創建授權管理原則，則任何 B2B 允許或拒絕清單設置將優先。 因此，請確保將要在此策略中包括的域包含在允許清單中（如果您正在使用）中，並且如果您正在使用拒絕清單，則將其從拒絕清單中排除。
- 如果要創建包含**所有使用者**（所有連接的組織 + 任何新外部使用者）的權利管理原則，必須首先為目錄啟用電子郵件一次性密碼身份驗證。 有關詳細資訊，請參閱[電子郵件一次性密碼身份驗證（預覽）。](../b2b/one-time-passcode.md#opting-in-to-the-preview)
- 有關 Azure AD B2B 外部協作設置的詳細資訊，請參閱[啟用 B2B 外部協作並管理誰可以邀請來賓](../b2b/delegate-invitations.md)。

    ![Azure AD 外部協作設置](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>查看條件訪問策略

- 請確保將來賓排除在新來賓使用者無法滿足的任何條件訪問策略中，因為這將阻止他們登錄到您的目錄。 例如，來賓可能沒有註冊設備，不在已知位置，並且不想重新註冊多重要素驗證 （MFA），因此在條件訪問策略中添加這些要求將阻止來賓使用授權管理。 有關詳細資訊，請參閱[Azure 活動目錄條件訪問中的條件是什麼？](../conditional-access/concept-conditional-access-conditions.md)

    ![Azure AD 條件訪問策略排除設置](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>查看 SharePoint 線上外部共用設置

- 如果要在外部使用者的訪問包中包含 SharePoint Online 網站，請確保組織級外部共用設置設置為 **"任何人**"（使用者不需要登錄）或 **"新建"和"現有來賓**"（來賓必須登錄或提供驗證碼）。 有關詳細資訊，請參閱[打開或關閉外部共用](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 如果要限制授權管理之外的任何外部共用，可以將外部共用設置設置為**現有來賓**。 然後，只有通過授權管理邀請的新使用者才能訪問這些網站。 有關詳細資訊，請參閱[打開或關閉外部共用](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 確保網站級設置啟用來賓存取（與前面列出的選項選擇相同）。 有關詳細資訊，請參閱[打開或關閉網站的外部共用](https://docs.microsoft.com/sharepoint/change-external-sharing-site)。

### <a name="review-your-office-365-group-sharing-settings"></a>查看 Office 365 組共用設置

- 如果要在外部使用者的訪問包中包括 Office 365 組，請確保 **"讓使用者向組織添加新來賓**"設置為 **"打開**"以允許來賓存取。 有關詳細資訊，請參閱[管理來賓對 Office 365 組的訪問](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access)。

- 如果希望外部使用者能夠訪問 SharePoint Online 網站以及與 Office 365 組關聯的資源，請確保打開 SharePoint 連線外部共用。 有關詳細資訊，請參閱[打開或關閉外部共用](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 有關如何在 PowerShell 中的目錄級別為 Office 365 組設置來賓策略的資訊，請參閱[示例：在目錄級別為組配置來賓策略](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)。

### <a name="review-your-teams-sharing-settings"></a>查看您的團隊共用設置

- 如果要在外部使用者的訪問包中包含 Teams，請確保 Microsoft Teams**中的"允許來賓存取**"設置為 **"打開**"以允許訪客訪問。 有關詳細資訊，請參閱在[Microsoft 團隊管理中心配置來賓存取](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)。

## <a name="manage-the-lifecycle-of-external-users"></a>管理外部使用者的生命週期

您可以選擇外部使用者（通過受批准的訪問包請求受邀進入目錄）時發生的情況，該請求不再具有任何訪問包分配。 如果使用者放棄其所有訪問包分配，或者其上次訪問包分配過期，則可能發生這種情況。 預設情況下，當外部使用者不再具有任何訪問包分配時，他們將被阻止登錄到您的目錄。 30 天后，他們的來賓使用者帳戶將從您的目錄中刪除。

**先決條件角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，在 **"授權管理**"部分中，按一下 **"設置**"。

1. 按一下 **[編輯]**。

    ![管理外部使用者生命週期的設置](./media/entitlement-management-external-users/settings-external-users.png)

1. 在 **"管理外部使用者生命週期**"部分中，為外部使用者選擇不同的設置。

1. 一旦外部使用者失去了對任何訪問包的最後分配，如果要阻止他們登錄到此目錄，請將**阻止外部使用者登錄到此目錄**設置為 **"是**"。

    > [!NOTE]
    > 如果使用者被阻止登錄到此目錄，則使用者將無法重新請求訪問包或請求此目錄中的其他存取權限。 如果隨後需要請求訪問其他訪問包，則不要配置阻止他們登錄。

1. 一旦外部使用者失去對任何訪問包的最後分配，如果要刪除其在此目錄中的來賓使用者帳戶，請將 **"刪除外部使用者"** 設置為 **"是**"。

    > [!NOTE]
    > 授權管理僅刪除通過授權管理邀請的帳戶。 此外，請注意，即使該使用者已添加到此目錄中未訪問包分配的資源，使用者也會被阻止登錄到並將其從此目錄中刪除。 如果在接收訪問包分配之前來賓在此目錄中存在，則來賓將保留。 但是，如果通過訪問包分配邀請該來賓，並且被邀請後也分配到 OneDrive 商務或 SharePoint Online 網站，他們仍將被刪除。

1. 如果要刪除此目錄中的來賓使用者帳戶，可以設置刪除該帳戶的天數。 如果要在來賓使用者帳戶丟失對任何訪問包的最後分配後立即刪除該帳戶，請將**從此目錄中刪除外部使用者之前的天數**設置為**0**。

1. 按一下 [儲存]****。

## <a name="next-steps"></a>後續步驟

- [新增已連線的組織](entitlement-management-organization.md)
- [對於不在目錄中的使用者](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [疑難排解](entitlement-management-troubleshoot.md)
