---
title: 設定 Microsoft 客戶合約的計費 - Azure
description: 了解如何設定 Microsoft 客戶合約的計費帳戶。 請參閱設定的必要條件，並檢視其他可用的資源。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: afa95609e0bcb879b6ad4b3b50b5dff5feb9a9f7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682785"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>設定 Microsoft 客戶合約的計費帳戶

如果您的 Enterprise 合約註冊已過期或即將到期，您可以簽署 Microsoft 客戶合約以更新您的註冊。 本文說明您現有的帳單在設定之後將有何變更，並引導您完成新計費帳戶的設定。 續約包含下列步驟：

1. 接受新的 Microsoft 客戶合約。 請與您的 Microsoft 現場代表合作，以了解詳細資料並接受新的合約。
2. 設定為新的 Microsoft 客戶合約建立的新計費帳戶。

若要設定計費帳戶，您必須將 Azure 訂用帳戶的帳單從 Enterprise 合約註冊轉換至新帳戶。 此設定不會影響您訂用帳戶中執行的 Azure 服務。 不過，這會變更您管理訂用帳戶計費的方式。

- 您將在 [Azure 入口網站](https://portal.azure.com)中管理 Azure 服務和帳單，而不是 [EA 入口網站](https://ea.azure.com)。
- 您會按月收到費用的數位發票。 您可以在 Azure 的 [成本管理 + 帳單] 頁面中檢視和分析發票。
- 您將使用新帳戶的計費結構和範圍來管理及組織您的帳單，而不是您 Enterprise 合約註冊中的部門和帳戶。

在開始設定之前，建議您先執行下列動作：

- **了解您新的計費帳戶**
  - 您的新帳戶可簡化組織的帳單。 [快速了解新的計費帳戶](../understand/mca-overview.md)
- **確認您有完成設定的存取權**
  - 只有具備特定系統管理權限的使用者才能完成設定。 請確認您是否具備[完成設定所需的存取權](#access-required-to-complete-the-setup)。
- **了解您計費階層的變更**
  - 新計費帳戶的組織方式與 Enterprise 合約註冊不同。 [了解新帳戶中的計費階層有何變更](#understand-changes-to-your-billing-hierarchy)。
- **了解計費管理員的存取權有何變更**
  - Enterprise 合約註冊中的管理員有權存取新帳戶中的計費範圍。請[了解其存取權的變更](#changes-to-billing-administrator-access)。
- **檢視由新帳戶取代的 Enterprise 合約功能**
  - 檢視由新帳戶中的功能取代的 Enterprise 合約註冊功能。
- **檢視常見問題的解答**
  - 檢視[其他資訊](#additional-information)以深入了解設定。

## <a name="access-required-to-complete-the-setup"></a>完成設定所需的存取權

若要完成設定，您需要下列存取權：

- 簽署 Microsoft 客戶合約時所建立之帳單設定檔的擁有者。 若要深入了解帳單設定檔，請參閱[了解帳單設定檔](../understand/mca-overview.md#billing-profiles)。

- 已更新之註冊的企業系統管理員。

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>如果您不是註冊的企業系統管理員

您可以要求註冊的企業系統管理員為您完成計費帳戶的設定。

1. 使用您簽署 Microsoft 客戶合約時傳送給您的電子郵件隨附的連結，登入 Azure 入口網站。

2. 如果您沒有電子郵件，請使用下列連結進行登入。 將 `<enrollmentNumber>` 取代為您已更新 Enterprise 合約的註冊號碼。

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. 選取您要將要求傳送給哪個企業系統管理員。

   ![顯示邀請企業系統管理員的螢幕擷取畫面](./media/mca-setup-account/ea-mca-invite-admins.png)

4. 選取 [傳送要求]  。

   系統管理員會收到一則電子郵件，其中包含完成設定的指示。

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>如果您不是帳單設定檔的擁有者

您的組織中負責簽署 Microsoft 客戶合約的使用者會新增為帳單設定檔的擁有者。 請要求使用者將您新增為擁有者，讓您可以完成設定。

## <a name="understand-changes-to-your-billing-hierarchy"></a>了解您計費階層的變更

您新的計費帳戶可簡化組織的帳單，同時為您提供增強的計費和成本管理功能。 下圖說明如何在新的計費帳戶中組織帳單。

![ea-mca-post-transition-hierarchy 的影像](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. 您可以使用計費帳戶來管理 Microsoft 客戶合約的帳單。 企業系統管理員會成為計費帳戶的擁有者。 若要深入了解計費帳戶，請參閱[了解計費帳戶](../understand/mca-overview.md#your-billing-account)。
2. 您可以使用帳單設定檔來管理組織的帳單，如同使用您的 Enterprise 合約註冊。 企業系統管理員會成為帳單設定檔的擁有者。 若要深入了解帳單設定檔，請參閱[了解帳單設定檔](../understand/mca-overview.md#billing-profiles)。
3. 您可以使用發票區段根據您的需求彙整成本，如同使用 Enterprise 合約註冊中的部門。 部門會成為發票區段，而部門系統管理員會成為個別發票區段的擁有者。 若要深入了解發票區段，請參閱[了解發票區段](../understand/mca-overview.md#invoice-sections)。
4. 新的計費帳戶不支援在您 Enterprise 合約中建立的帳戶。 帳戶的訂用帳戶屬於其部門的個別發票區段。 帳戶擁有者可建立及管理其發票區段的訂用帳戶。

## <a name="changes-to-billing-administrator-access"></a>計費管理員存取權的變更

視存取權之不同，Enterprise 合約註冊中的計費管理員有權存取新帳戶的計費範圍。 下表說明設定期間的存取權變更：

| 現有的角色 | 轉換後的角色 |
| --- | --- |
| **企業系統管理員 (唯讀 = 否)** | **- 計費帳戶擁有者** </br> 管理計費帳戶的所有項目 </br> **- 帳單設定檔擁有者** </br> 管理帳單設定檔中的所有項目 </br> **- 所有發票區段上的發票區段擁有者** </br> 管理發票區段上的所有項目 |
| **企業系統管理員 (唯讀 = 是)** | **- 計費帳戶讀者** </br> 對計費帳戶中所有項目的唯讀檢視</br> **- 帳單設定檔讀者** </br> 對帳單設定檔中所有項目的唯讀檢視</br>**- 所有發票區段上的發票區段讀者**</br> 對發票區段上所有項目的唯讀檢視|
| **部門系統管理員 (唯讀 = 否)** |**- 為其各自部門建立之發票區段上的發票區段擁有者** </br>管理發票區段上的所有項目|
| **部門系統管理員 (唯讀 = 是)**|**- 為其各自部門建立之發票區段上的發票區段讀者**</br> 對發票區段上所有項目的唯讀檢視|
| **帳戶擁有者** | **- 為其各自部門建立之發票區段上的 Azure 訂用帳戶建立者** </br>  建立發票區段的 Azure 訂用帳戶|

在接受您的 Microsoft 客戶合約時，會為新的計費帳戶選取 Azure Active Directory (AD) 租用戶。 如果您的組織沒有租用戶，則會建立新的租用戶。 租用戶代表您在 Azure Active Directory 內的組織。 組織中的全域租用戶管理員會使用租用戶來管理您組織中的應用程式和資料存取。

您的新帳戶僅支援在簽署 Microsoft 客戶合約時所選取之租用戶中的使用者。 如果您的 Enterprise 合約上具有系統管理權限的使用者屬於該租用戶，其將在設定期間取得新計費帳戶的存取權。 如果他們不屬於該租用戶，則無法存取新的計費帳戶，除非獲得您的邀請。

使用者獲得您的邀請後，會以來賓使用者的身分新增至租用戶，並取得計費帳戶的存取權。 若要邀請使用者，必須開啟租用戶的來賓存取權。 如需詳細資訊，請參閱[控制 Azure Active Directory 中的來賓存取權](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory)。 如果來賓存取權關閉，請要求租用戶的全域管理員加以開啟。 <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>檢視已取代的功能

下列 Enterprise 合約的功能已取代為 Microsoft 客戶合約的計費帳戶中的新功能。

### <a name="enterprise-agreement-accounts"></a>Enterprise 合約帳戶

新的計費帳戶不支援在您的 Enterprise 合約註冊中建立的帳戶。 帳戶的訂用帳戶屬於為其各自部門建立的發票區段。 帳戶擁有者會成為 Azure 訂用帳戶建立者，且能夠建立及管理其發票區段的訂用帳戶。

### <a name="notification-contacts"></a>通知連絡人

通知連絡人會收到有關於 Azure Enterprise 合約的電子郵件通訊。 新的計費帳戶不支援這些功能。 關於 Azure 點數和發票的電子郵件會傳送給可對計費帳戶中的帳單設定檔進行存取的使用者。

### <a name="spending-quotas"></a>消費配額

為您 Enterprise 合約註冊中的部門設定的消費配額，已取代為新計費帳戶中的預算。 為註冊中的部門設定的每個消費配額，都會建立預算。 如需預算的詳細資訊，請參閱[教學課程：建立和管理 Azure 預算](../costs/tutorial-acm-create-budgets.md)。

### <a name="cost-centers"></a>成本中心

在您 Enterprise 合約註冊中的 Azure 訂用帳戶上設定的成本中心，會遞轉到新的計費帳戶中。 但不支援部門和 Enterprise 合約帳戶的成本中心。

## <a name="additional-information"></a>其他資訊

以下各節提供其他有關於設定計費帳戶的資訊。

### <a name="no-service-downtime"></a>沒有服務停機時間

您訂用帳戶中的 Azure 服務可以持續執行，不會發生任何中斷。 我們僅會轉換您 Azure 訂用帳戶的帳務關係。 現有的資源、資源群組或管理群組不受影響。

### <a name="user-access-to-azure-resources"></a>Azure 資源的使用者存取

在轉換期間，使用 Azure RBAC (角色型存取控制) 設定的 Azure 資源存取不受影響。

### <a name="azure-reservations"></a>Azure 保留

您 Enterprise 合約註冊中的所有 Azure 保留都會移轉至新的計費帳戶。 在轉換期間，套用至您訂用帳戶的保留折扣不會有任何變更。

### <a name="azure-marketplace-products"></a>Azure Marketplace 產品

您 Enterprise 合約註冊中的任何 Azure Marketplace 產品都會隨著訂用帳戶一起移轉。 在轉換期間，Marketplace 產品的服務存取不會有任何變更。

### <a name="support-plan"></a>支援計劃

在轉換過程中不會轉移支援權益。 請購買新的支援方案，以在新的計費帳戶中取得 Azure 訂用帳戶的權益。

### <a name="past-charges-and-balance"></a>過去的費用和餘額

轉換前的費用和點數餘額，可透過 Azure 入口網站在您的 Enterprise 合約註冊中檢視。 <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>設定應於何時完成？

請在 Enterprise 合約註冊到期之前完成計費帳戶的設定。 如果您的註冊到期，Azure 訂用帳戶中的服務仍會繼續執行，而不會中斷運作。 不過，您將需依隨用隨付費率支付服務的費用。

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Enterprise 合約註冊在設定後的變更

在轉換之後為 Enterprise 合約註冊建立的 Azure 訂用帳戶，可以手動移轉至新的計費帳戶。 如需詳細資訊，請參閱[向其他使用者接管 Azure 訂用帳戶的帳單擁有權](mca-request-billing-ownership.md)。 若要移轉在轉換後購買的 Azure 保留，請[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 您也可以在轉換後提供使用者對計費帳戶的存取權。 如需詳細資訊，請參閱[在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>還原轉換

轉換無法還原。 Azure 訂用帳戶的帳單轉換至新的計費帳戶後，您即無法將其還原至 Enterprise 合約註冊。

### <a name="closing-your-browser-during-setup"></a>在設定期間關閉您的瀏覽器

在按一下 [開始轉換]  之前，您可以先關閉瀏覽器。 您可以使用電子郵件中提供的連結返回設定程序，並開始轉換。 如果您關閉瀏覽器，在轉換開始後，您的轉換將會繼續執行。 請回到 [轉換狀態] 頁面，以監視轉換的最新狀態。 轉換完成時，您會收到一則電子郵件。

## <a name="complete-the-setup-in-the-azure-portal"></a>在 Azure 入口網站中完成設定

若要完成設定，您必須具有新的計費帳戶和 Enterprise 合約註冊的存取權。 如需詳細資訊，請參閱[完成計費帳戶設定所需的存取權](#access-required-to-complete-the-setup)。

1. 使用您簽署 Microsoft 客戶合約時傳送給您的電子郵件隨附的連結，登入 Azure 入口網站。

2. 如果您沒有電子郵件，請使用下列連結進行登入。 將 `<enrollmentNumber>` 取代為您已更新 Enterprise 合約的註冊號碼。

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. 在設定的最後一個步驟中，選取 [開始轉換]  。 在您選取 [開始轉換] 後：

    ![顯示設定精靈的螢幕擷取畫面](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - 新的計費帳戶中會建立與您的 Enterprise 合約階層相對應的計費階層。 如需詳細資訊，請參閱[了解您計費階層的變更](#understand-changes-to-your-billing-hierarchy)。
    - Enterprise 合約註冊中的系統管理員會獲得新計費帳戶的存取權，而能夠繼續管理組織的帳單。
    - Azure 訂用帳戶的帳單會轉換至新的帳戶。 **在此轉換期間，您的 Azure 服務將不受任何影響。服務會繼續執行，而不會發生任何中斷**。
    - 如果您有 Azure 保留，這些保留會移至新的計費帳戶，其權益或期限不變。

4. 您可以在 [轉換狀態]  頁面上監視轉換的狀態。

   ![顯示轉換狀態的螢幕擷取畫面](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>驗證計費帳戶設定

 請驗證下列各項，以確定您新的計費帳戶已正確設定：

### <a name="azure-subscriptions"></a>Azure 訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/mca-setup-account/search-cmb.png)

3. 選取計費帳戶。 計費帳戶的類型會是 **Microsoft 客戶合約**。

4. 從左側選取 [Azure 訂用帳戶]  。

   ![顯示訂用帳戶清單的螢幕擷取畫面](./media/mca-setup-account/mca-subscriptions-post-transition.png)

從您的 Enterprise 合約註冊轉換到新計費帳戶的 Azure 訂用帳戶，會顯示在 [Azure 訂用帳戶] 頁面上。 如果您認為有任何訂用帳戶遺失，請在 Azure 入口網站中手動轉換訂用帳戶的帳單。 如需詳細資訊，請參閱[向其他使用者接管 Azure 訂用帳戶的帳單擁有權](mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure 保留

您 Enterprise 合約註冊中的 Azure 保留都會移至新的計費帳戶，其權益或期限不變。 在轉換前完成的交易不會出現在新的計費帳戶中。 不過，您可以造訪 [Azure 保留頁面](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)，確認您的保留權益已套用至您的訂用帳戶。

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>企業系統管理員對於計費帳戶的存取權

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/mca-setup-account/search-cmb.png)

3. 針對您的 **Microsoft 客戶合約**設定計費帳戶。

4. 從左側選取 [存取控制 (IAM)]  。

   ![顯示轉換後的企業系統管理員存取權的螢幕擷取畫面](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

企業系統管理員會列為計費帳戶擁有者，而具有唯讀權限的企業系統管理員則會列為計費帳戶讀者。 如果您認為有任何企業系統管理員的存取權遺失，您可以在 Azure 入口網站中為他們授與存取權。 如需詳細資訊，請參閱[在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>企業系統管理員在帳單設定檔上的存取權

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/mca-setup-account/search-cmb.png)

3. 選取為您的註冊建立的帳單設定檔。 您可能需要根據存取權來選取帳單帳戶。 從計費帳戶中選取 [帳單設定檔]，然後選取帳單設定檔。

4. 從左側選取 [存取控制 (IAM)]  。

   ![顯示轉換後的企業系統管理員存取權的螢幕擷取畫面](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

企業系統管理員會列為帳單設定檔擁有者，而具有唯讀權限的企業系統管理員則會列為帳單設定檔讀者。 如果您認為有任何企業系統管理員的存取權遺失，您可以在 Azure 入口網站中為他們授與存取權。 如需詳細資訊，請參閱[在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>企業系統管理員、部門系統管理員和帳戶擁有者在發票區段上的存取權

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/mca-setup-account/search-cmb.png).

3. 選取發票區段。 發票區段會與其在 Enterprise 合約註冊中各自的部門同名。 您可能需要根據存取權來選取帳單帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取 [發票區段]  。 從發票區段清單中，選取一個發票區段。

   ![顯示轉換後的發票區段清單的螢幕擷取畫面](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. 從左側選取 [存取控制 (IAM)]  。

    ![顯示轉換後的部門和帳戶管理員存取權的螢幕擷取畫面](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

企業系統管理員和部門系統管理員會列為發票區段擁有者或發票區段讀者，而部門中的帳戶擁有者則會列為 Azure 訂用帳戶建立者。 請對所有發票區段重複此步驟，以檢查 Enterprise 合約註冊中所有部門的存取權。 不屬於任何部門的帳戶擁有者，將有權存取名為**預設發票區段**的發票區段。 如果您認為有任何系統管理員的存取權遺失，您可以在 Azure 入口網站中為他們授與存取權。 如需詳細資訊，請參閱[在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [開始使用您新的計費帳戶](../understand/mca-overview.md)

- [在您 Microsoft 客戶合約的計費帳戶中完成 Enterprise 合約工作](mca-enterprise-operations.md)

- [管理對計費帳戶的存取](understand-mca-roles.md)
