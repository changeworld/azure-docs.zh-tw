---
title: 教學課程：使用 Azure Active Directory 設定 Elium 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至 Elium。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356735"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>教學課程：設定 Elium 來自動布建使用者

本教學課程說明如何設定 Elium 和 Azure Active Directory (Azure AD) ，以將使用者或群組自動布建和取消布建至 Elium。

> [!NOTE]
> 本教學課程說明建立在 Azure AD 使用者布建服務之上的連接器。 如需此服務的功能及其運作方式的重要詳細資料，以及常見問題的詳細資訊，請參閱使用 Azure Active Directory 自動布建和解除布建 [SaaS 應用程式](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 如需預覽中 Azure 功能的一般使用條款，請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程假設您已具備下列必要條件：

* Azure AD 租用戶
* [Elium 租使用者](https://www.elium.com/pricing/)
* Elium 中具有系統管理員許可權的使用者帳戶

## <a name="assigning-users-to-elium"></a>將使用者指派給 Elium

Azure AD 會使用稱為「 *指派* 」的概念，來判斷哪些使用者可以存取選取的應用程式。 在自動使用者布建的內容中，只會同步處理在 Azure AD 中指派給應用程式的使用者和群組。

設定並啟用自動使用者布建之前，請先決定 Azure AD 中的哪些使用者和群組需要存取 Elium。 然後，遵循將 [使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的步驟，將這些使用者和群組指派給 Elium。

## <a name="important-tips-for-assigning-users-to-elium"></a>將使用者指派給 Elium 的重要秘訣 

建議您將單一 Azure AD 使用者指派給 Elium，以測試自動使用者布建設定。 稍後可以指派更多使用者和群組。

將使用者指派給 Elium 時，您必須在 [指派] 對話方塊中選取有效的應用程式特定角色 (如果有任何可用的) 。 具有 **預設存取** 角色的使用者會從布建中排除。

## <a name="set-up-elium-for-provisioning"></a>設定布建的 Elium

使用 Azure AD 設定 Elium 來自動布建使用者之前，您必須在 Elium 上啟用系統以進行跨網域身分識別管理 (SCIM) 布建。 請遵循這些步驟：

1. 登入 Elium 並移至 [ **我的設定檔**  >  **設定** ]。

    ![Elium 中的 [設定] 功能表項目](media/Elium-provisioning-tutorial/setting.png)

1. 在左下角的 [ **ADVANCED** ] 底下，選取 [ **安全性** ]。

    ![Elium 中的安全性連結](media/Elium-provisioning-tutorial/security.png)

1. 複製 **租使用者 URL** 和 **秘密權杖** 值。 您稍後會在 Azure 入口網站的 Elium 應用程式 **的 [布** 建] 索引標籤中，于對應的欄位中使用這些值。

    ![Elium 中的租使用者 URL 和秘密權杖欄位](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>從資源庫新增 Elium

若要使用 Azure AD 設定 Elium 來自動布建使用者，您也必須從 Azure AD 應用程式資源庫將 Elium 新增至受控軟體即服務 (SaaS) 應用程式的清單。 請遵循這些步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)的左側導覽面板中，選取 [ **Azure Active Directory** ]。

    ![Azure Active Directory 功能表項目](common/select-azuread.png)

1. 移至 [企業應用程式]，然後選取 [所有應用程式]。

     ![Azure AD 企業應用程式分頁](common/enterprise-applications.png)

1. 若要加入新的應用程式，請選取窗格頂端的 [ **新增應用程式** ]。

    ![新增應用程式連結](common/add-new-app.png)

1. 在 [搜尋] 方塊中，輸入 **Elium** ，在結果清單中選取 [ **Elium** ]，然後選取 [ **新增** ] 以新增應用程式。

    ![資源庫搜尋方塊](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>設定自動使用者布建至 Elium

本節將引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和群組指派，在 Elium 中建立、更新及停用使用者和群組的步驟。

> [!TIP]
> 您也可以依照 [Elium 單一登入教學](Elium-tutorial.md)課程中的指示，選擇根據安全性聲明標記語言 (SAML) 來啟用 Elium 的單一登入。 雖然這兩個功能彼此互補，但您可以獨立設定自動使用者布建的單一登入。

若要在 Azure AD 中為 Elium 設定自動使用者布建，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，選取 [ **企業應用程式** ]，然後選取 [ **所有應用程式** ]。

    ![Azure AD 企業應用程式分頁](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [Elium]。

    ![企業應用程式分頁中的應用程式清單](common/all-applications.png)

1. 選取 [佈建]  索引標籤。

    ![企業應用程式分頁中的 [布建] 索引標籤](common/provisioning.png)

1. 將 [佈建模式]  設定為 [自動]  。

    ![布建模式的自動設定](common/provisioning-automatic.png)

1. 在 [ **管理員認證** ] 區段的 [租使用者 **URL** ] 欄位中，輸入 **\<tenantURL\> /scim/v2** 。  ( **租使用者 url>** 是先前從 Elium 管理主控台中取出的值。 ) 也在 [ **秘密權杖** ] 欄位中輸入 Elium **秘密權杖** 值。 最後，選取 [ **測試連接** ] 以確認 Azure AD 可以連線至 Elium。 如果連接失敗，請確定您的 Elium 帳戶具有系統管理員許可權，然後再試一次。

    ![系統管理員認證中的租使用者 URL 和秘密權杖欄位](common/provisioning-testconnection-tenanturltoken.png)

1. 在 [ **通知電子郵件** ] 欄位中，輸入將會收到布建錯誤通知的人員或群組的電子郵件地址。 然後，選取 [ **發生失敗時傳送電子郵件通知** ] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

1. 按一下 [檔案]  。

1. **在 [對應** ] 區段中，選取 [ **同步處理 Azure Active Directory 使用者至 Elium** ]。

    ![將 Azure AD 使用者對應的連結同步至 Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. 在 [ **屬性** 對應] 區段中，檢查從 Azure AD 同步處理到 Elium 的使用者屬性。 選取為 [比對 **] 屬性的屬性會** 用來比對 Elium 中的使用者帳戶以進行更新作業。 選取 [儲存] 認可任何變更。

    ![Azure AD 與 Elium 之間的屬性對應](media/Elium-provisioning-tutorial/userattribute.png)

1. 若要設定範圍篩選器，請遵循 [範圍篩選器教學](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)課程中的指示。

1. 若要啟用 Elium Azure AD 的布建服務，請在 [ **設定** ] 區段中，將 [布建 **狀態** ] 變更為 [ **開啟** ]。

    ![布建狀態設定為開啟](common/provisioning-toggle-on.png)

1. 在 [ **設定** ] 區段的 [ **領域** ] 下拉式清單方塊中選取所需的值，以定義您想要布建到 Elium 的使用者和群組。

    ![布建領域清單方塊](common/provisioning-scope.png)

1. 準備好要佈建時，請選取 [儲存]。

    ![布建設定的儲存按鈕](common/provisioning-configuration-save.png)

這項 **作業** 會針對 [ **設定** ] 區段中定義的所有使用者和群組，啟動首次同步處理。 此初始同步處理常式所花費的時間比之後的同步處理時間長。 如需布建所需時間的詳細資訊，請參閱布建 [使用者需要多久](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)的時間？。

使用 [ **目前狀態** ] 區段來監視進度，並遵循您布建活動報告的連結。 布建活動報告描述 Elium 上的 Azure AD 布建服務所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 布建記錄，請參閱 [有關自動布建使用者帳戶的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶](../app-provisioning/configure-automatic-user-provisioning-portal.md)布建。
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
