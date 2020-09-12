---
title: 在 Azure AD 權利管理中管理外部使用者的存取權-Azure Active Directory
description: 瞭解您可以指定的設定，以管理 Azure Active Directory 權利管理中外部使用者的存取權。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72ed8fede56385248415caf0438b190d5c191bbc
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460859"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中管理外部使用者的存取權

Azure AD 權利管理會利用 [Azure AD 的企業對企業 (B2B) ](../external-identities/what-is-b2b.md) 與另一個目錄中的組織外部人員共同作業。 使用 Azure AD B2B，外部使用者會向其主目錄進行驗證，但在您的目錄中會有標記法。 您目錄中的標記法可讓使用者將您資源的存取權指派給使用者。

本文說明您可以指定的設定，以管理外部使用者的存取權。

## <a name="how-entitlement-management-can-help"></a>權利管理如何能提供協助

使用 [AZURE AD B2B](../external-identities/what-is-b2b.md) 邀請體驗時，您必須已經知道想要帶入資原始目錄並使用的外部來賓使用者的電子郵件地址。 當您使用較小或短期的專案，且您已經知道所有參與者，但如果您有許多想要使用的使用者，或是參與者隨著時間而改變，這項功能就很難管理。  例如，您可能會與另一個組織合作，並與該組織有一個聯繫點，但是經過一段時間後，該組織的其他使用者也將需要存取權。

使用權利管理時，您可以定義原則，讓您指定的組織的使用者能夠自行要求存取套件。 您可以指定是否需要核准，以及存取的到期日。 如果需要核准，您也可以將外部組織中的一或多個使用者邀請到您的目錄，並將其指定為核准者，因為他們很可能知道組織需要存取的外部使用者。 設定存取套件之後，您可以將存取套件的連結傳送給您的連絡人， (贊助者) 外部組織。 該連絡人可以與外部組織中的其他使用者共用，而且可以使用此連結來要求存取套件。 該組織中已經受邀加入您目錄的使用者也可以使用該連結。

核准要求時，權利管理會以必要的存取權來布建使用者，如果使用者還不在目錄中，則可能包括邀請使用者。 Azure AD 會自動為他們建立 B2B 來賓帳戶。 請注意，系統管理員先前可能會限制允許共同作業的組織，方法是設定 [B2B 允許或拒絕清單](../external-identities/allow-deny-list.md) ，以允許或封鎖其他組織的邀請。  如果允許或封鎖清單不允許使用者，則不會受邀。

由於您不想讓外部使用者的存取權一直持續，因此您必須在原則中指定到期日，例如180天。 180天之後，如果未延伸其存取權，權利管理將會移除與該存取套件相關聯的所有存取權。 依預設，如果透過權利管理邀請的使用者沒有其他存取套件指派，則當他們遺失最後一次的指派時，他們的來賓帳戶將會遭到封鎖而無法登入30天，然後再移除。 這可避免不必要的帳戶激增。 如以下各節所述，這些設定是可設定的。

## <a name="how-access-works-for-external-users"></a>外部使用者的存取權如何運作

下圖和步驟概述如何將存取套件的存取權授與外部使用者。

![顯示外部使用者生命週期的圖表](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. 針對您想要與之共同作業的 Azure AD 目錄或網域， [新增已連線的組織](entitlement-management-organization.md) 。

1. 您可以在您的目錄中建立存取套件，其中包含 [不在您目錄中的使用者](entitlement-management-access-package-create.md#for-users-not-in-your-directory)原則。

1. 您可以將 [我的存取權入口網站連結](entitlement-management-access-package-settings.md) 傳送給外部組織的連絡人，讓他們可以與使用者共用，以要求存取套件。

1. 在此範例中，外部使用者 (要求 **者，) ** 使用我的存取權入口網站連結來要求存取套件的 [存取](entitlement-management-request-access.md) 權。 使用者登入的方式取決於已連線組織中定義之目錄或網域的驗證類型。

1. 核准者 [核准要求](entitlement-management-request-approve.md) (或要求已自動核准) 。

1. 要求進入 [傳遞狀態](entitlement-management-process.md)。

1. 使用 B2B 邀請程式時，會在您的目錄中建立來賓使用者帳戶， (要求者在此範例) 中 ** (來賓) ** 。 如果已定義 [允許清單或拒絕清單](../external-identities/allow-deny-list.md) ，則會套用清單設定。

1. 來賓使用者會獲指派存取套件中所有資源的存取權。 在 Azure AD 和其他 Microsoft Online Services 或已連線的 SaaS 應用程式中進行變更可能需要一些時間。 如需詳細資訊，請參閱套用 [變更的時間](entitlement-management-access-package-resources.md#when-changes-are-applied)。

1. 外部使用者會收到一封電子郵件，指出已 [傳遞](entitlement-management-process.md)其存取權。

1. 若要存取資源，外部使用者可以按一下電子郵件中的連結，或嘗試直接存取任何目錄資源以完成邀請程式。

1. 根據原則設定，當時間通過時，外部使用者的存取套件指派會過期，而且會移除外部使用者的存取權。

1. 根據外部使用者設定的生命週期，當外部使用者不再具有任何存取套件指派時，外部使用者會被封鎖而無法登入，且會從您的目錄中移除來賓使用者帳戶。

## <a name="settings-for-external-users"></a>外部使用者的設定

為了確保組織外部的人員可以要求存取套件，並存取這些存取套件中的資源，您應該確認已正確設定某些設定。

### <a name="enable-catalog-for-external-users"></a>啟用外部使用者的目錄

- 依預設，當您建立 [新的類別目錄](entitlement-management-catalog-create.md)時，它會啟用以允許外部使用者要求目錄中的存取套件。 請確定 [ **為外部使用者啟用** ] 設定為 **[是]**。

    ![編輯類別目錄設定](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>設定您的 Azure AD B2B 外部共同作業設定

- 允許來賓邀請其他來賓前往您的目錄，表示來賓邀請可能會在權利管理之外進行。 建議您將 **來賓** 設定為 [ **否** ]，只允許適當控管的邀請。
- 如果您使用 B2B 允許清單，您必須確定您想要與使用權利管理合作的任何網域都已新增至清單中。 或者，如果您使用 B2B 拒絕清單，則必須確定您要加入的任何網域都未新增至清單中。
- 如果您為所有已連線組織 (的 **所有使用者** 建立權利管理原則) ，您所擁有的任何 B2B 允許或拒絕清單設定將會優先執行。 因此，如果您使用，請務必將您想要包含在此原則中的網域包含在允許清單中，如果您使用拒絕清單，請將其從拒絕清單中排除。
- 如果您想要建立包含所有 **使用者** (所有已連線組織的權利管理原則 +) 的任何新外部使用者，您必須先針對您的目錄啟用電子郵件單次密碼驗證。 如需詳細資訊，請參閱 [電子郵件單次密碼驗證 (預覽) ](../external-identities/one-time-passcode.md#opting-in-to-the-preview)。
- 如需有關 Azure AD B2B 外部共同作業設定的詳細資訊，請參閱 [啟用 b2b 外部共同作業及管理可邀請來賓的人員](../external-identities/delegate-invitations.md)。

    ![Azure AD 外部共同作業設定](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>檢查您的條件式存取原則

- 請務必將來賓從任何條件式存取原則中排除，因為新的來賓使用者將無法滿足這些原則，因為這樣會封鎖他們無法登入您的目錄。 例如，來賓可能沒有已註冊的裝置、不在已知的位置，也不想要重新註冊 (MFA) 的多重要素驗證，因此在條件式存取原則中新增這些需求將會封鎖來賓使用權利管理。 如需詳細資訊，請參閱 [Azure Active Directory 條件式存取中的條件為何？](../conditional-access/concept-conditional-access-conditions.md)。

    ![Azure AD 條件式存取原則排除設定](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>檢查您的 SharePoint Online 外部共用設定

- 如果您想要在外部使用者的存取套件中包含 SharePoint Online 網站，請確定您的組織層級外部共用設定設為 **任何** 使用者 (使用者不需要登入) 或 **新的和現有的來賓** (來賓必須登入或提供驗證碼) 。 如需詳細資訊，請參閱 [開啟或關閉外部共用](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 如果您想要限制在權利管理之外的任何外部共用，您可以將外部共用設定設為 **現有的來賓**。 然後，只有透過權利管理邀請的新使用者才能取得這些網站的存取權。 如需詳細資訊，請參閱 [開啟或關閉外部共用](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 請確定網站層級設定啟用來賓存取 (與先前所列) 相同的選項選取專案。 如需詳細資訊，請參閱 [開啟或關閉網站的外部共用](/sharepoint/change-external-sharing-site)。

### <a name="review-your-microsoft-365-group-sharing-settings"></a>檢查您的 Microsoft 365 群組共用設定

- 如果您想要在存取套件中包含外部使用者的 Microsoft 365 群組，請確認 [ **允許使用者將新的來賓新增至組織** ] 已設為 [ **開啟** ]，以允許來賓存取。 如需詳細資訊，請參閱 [管理對 Microsoft 365 群組的來賓存取](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access)。

- 如果您想要讓外部使用者能夠存取 SharePoint Online 網站和與 Microsoft 365 群組相關聯的資源，請確定您開啟的是 SharePoint Online 外部共用。 如需詳細資訊，請參閱 [開啟或關閉外部共用](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 如需如何在 PowerShell 中為目錄層級的 Microsoft 365 群組設定來賓原則的詳細資訊，請參閱 [範例：在目錄層級設定群組的來賓原則](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)。

### <a name="review-your-teams-sharing-settings"></a>檢查您的小組共用設定

- 如果您想要在您的外部使用者存取套件中包含小組，請確定已將 [ **允許 Microsoft 團隊的來賓存取** ] 設定為 [ **開啟** ]，以允許來賓存取。 如需詳細資訊，請參閱 [Microsoft 小組系統管理中心的設定來賓存取權](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)。

## <a name="manage-the-lifecycle-of-external-users"></a>管理外部使用者的生命週期

您可以選取當外部使用者透過核准的存取套件要求存取您的目錄時會發生什麼事，再也不會有任何存取套件的指派。 如果使用者會讓出其所有存取套件指派，或其上次存取套件指派過期，就會發生這種情況。 根據預設，當外部使用者不再具有任何存取套件指派時，系統會將其封鎖而無法登入您的目錄。 30天后，就會從您的目錄中移除其來賓使用者帳戶。

**必要角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表的 [ **權利管理** ] 區段中，按一下 [ **設定**]。

1. 按一下 **[編輯]** 。

    ![管理外部使用者生命週期的設定](./media/entitlement-management-external-users/settings-external-users.png)

1. 在 [ **管理外部使用者的生命週期** ] 區段中，選取 [外部使用者的不同設定]。

1. 當外部使用者失去其最後一次對任何存取套件的指派時，如果您想要封鎖使用者登入此目錄，請將 [ **禁止外部使用者登入這個目錄** ] 設定為 **[是]**。

    > [!NOTE]
    > 如果使用者遭到封鎖而無法登入此目錄，則使用者將無法在此目錄中重新要求存取套件或要求其他存取權。 如果後續需要要求存取其他存取套件，請不要設定封鎖它們進行登入。

1. 當外部使用者失去其最後一次對任何存取套件的指派時，如果您想要移除其在此目錄中的來賓使用者帳戶，請將 [ **移除外部使用者** ] 設定為 **[是]**。

    > [!NOTE]
    > 權利管理只會移除透過權利管理邀請的帳戶。 此外，請注意，即使該使用者已新增到此目錄中未存取套件指派的資源，也會封鎖使用者登入並從這個目錄移除該使用者。 如果在接收存取套件指派之前，來賓存在於此目錄中，則會保留它們。 但是，如果已透過存取套件指派邀請來賓，而且受邀之後也指派給商務用 OneDrive 或 SharePoint Online 網站，他們仍會被移除。

1. 如果您想要移除此目錄中的來賓使用者帳戶，您可以設定移除之前的天數。 如果您想要在使用者遺失其最後一次指派給任何存取套件的情況下移除該來賓使用者帳戶，請將 **此目錄中的外部使用者移除前的天數** 設定為 **0**。

1. 按一下 [檔案] 。

## <a name="next-steps"></a>後續步驟

- [新增已連線的組織](entitlement-management-organization.md)
- [針對不在您目錄中的使用者](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [疑難排解](entitlement-management-troubleshoot.md)