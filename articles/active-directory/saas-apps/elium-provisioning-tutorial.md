---
title: 教學課程：使用 Azure Active Directory 設定 Elium 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至 Elium。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77058458"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>教學課程：設定 Elium 來自動布建使用者

本教學課程說明如何設定 Elium 和 Azure Active Directory （Azure AD），以將使用者或群組自動布建和取消布建至 Elium。

> [!NOTE]
> 本教學課程說明建立在 Azure AD 使用者布建服務之上的連接器。 如需有關此服務的用途和運作方式，以及常見問題的重要詳細資料，請參閱[使用 Azure Active Directory 自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md)布建和解除布建。
>
> 此連接器目前為預覽版。 如需預覽中 Azure 功能的一般使用規定，請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="prerequisites"></a>先決條件

本教學課程假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Elium 租使用者](https://www.elium.com/pricing/)
* Elium 中具有系統管理員許可權的使用者帳戶

## <a name="assigning-users-to-elium"></a>將使用者指派給 Elium

Azure AD 使用稱為「*指派*」的概念，來判斷哪些使用者會收到所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和群組會進行同步處理。

在您設定並啟用自動使用者布建之前，請先決定 Azure AD 中的哪些使用者和群組需要存取 Elium。 然後，遵循[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的步驟，將這些使用者和群組指派給 Elium。

## <a name="important-tips-for-assigning-users-to-elium"></a>將使用者指派給 Elium 的重要秘訣 

建議您將單一 Azure AD 使用者指派給 Elium，以測試自動使用者布建設定。 稍後可以指派更多的使用者和群組。

將使用者指派給 Elium 時，您必須在 [指派] 對話方塊中選取有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從布建中排除。

## <a name="set-up-elium-for-provisioning"></a>設定 Elium 以提供布建

將 Elium 設定為使用 Azure AD 自動布建使用者之前，您必須在 Elium 上啟用「跨網域身分識別管理」（SCIM）布建的「系統」。 請遵循下列步驟：

1. 登入 Elium 並移至 [**我的設定檔** > **設定**]。

    ![Elium 中的 [設定] 功能表項目](media/Elium-provisioning-tutorial/setting.png)

1. 在左下角的 [ **ADVANCED**] 底下，選取 [**安全性**]。

    ![Elium 中的安全性連結](media/Elium-provisioning-tutorial/security.png)

1. 複製 [**租使用者 URL** ] 和 [**秘密權杖**] 值。 您稍後會在 Azure 入口網站的 Elium 應用程式**的 [布**建] 索引標籤中，于對應的欄位中使用這些值。

    ![Elium 中的租使用者 URL 和秘密權杖欄位](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>從資源庫新增 Elium

若要使用 Azure AD 設定 Elium 來自動布建使用者，您也必須從 Azure AD 應用程式資源庫將 Elium 新增至受控軟體即服務（SaaS）應用程式清單。 請遵循下列步驟：

1. 在[Azure 入口網站](https://portal.azure.com)的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 功能表項目](common/select-azuread.png)

1. 移至 [**企業應用程式**]，然後選取 [**所有應用程式**]。

     ![Azure AD 企業應用程式] 分頁](common/enterprise-applications.png)

1. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**]。

    ![新增應用程式連結](common/add-new-app.png)

1. 在搜尋方塊中，輸入**Elium**，在結果清單中選取 [ **Elium** ]，然後選取 [**新增**] 以新增應用程式。

    ![圖庫搜尋方塊](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>設定自動使用者布建至 Elium

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和群組指派，在 Elium 中建立、更新和停用使用者和群組。

> [!TIP]
> 您也可以遵循[Elium 單一登入教學](Elium-tutorial.md)課程中的指示，選擇根據安全性聲明標記語言（SAML）啟用 Elium 的單一登入。 您可以設定獨立于自動使用者布建的單一登入，雖然這兩個功能彼此互補。

若要在 Azure AD 中設定自動使用者布建以進行 Elium，請遵循下列步驟：

1. 登入[Azure 入口網站](https://portal.azure.com)，選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![Azure AD 企業應用程式] 分頁](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [Elium]****。

    ![[企業應用程式] 分頁中的應用程式清單](common/all-applications.png)

1. 選取 [**布**建] 索引標籤。

    ![[企業應用程式] 分頁的 [布建] 索引標籤](common/provisioning.png)

1. 將布建模式設定為 [**自動** **]** 。

    ![布建模式的自動設定](common/provisioning-automatic.png)

1. 在 [**管理員認證**] 區段的 [**租使用者 URL** ] 欄位中，輸入** \< \>租使用者 url>/scim/v2** 。 （**租使用者 url>** 是先前從 Elium 管理主控台中抓取的值）。也請在 [**秘密權杖**] 欄位中輸入 Elium**秘密 token**值。 最後，選取 [**測試連接**] 以確認 Azure AD 可以連接到 Elium。 如果連線失敗，請確定您的 Elium 帳戶具有系統管理員許可權，然後再試一次。

    ![管理員認證中的租使用者 URL 和秘密權杖欄位](common/provisioning-testconnection-tenanturltoken.png)

1. 在 [**通知電子郵件**] 欄位中，輸入將接收布建錯誤通知之個人或群組的電子郵件地址。 然後選取 [**發生失敗時傳送電子郵件通知**] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

1. 按一下 **[儲存]** 。

1. **在 [對應**] 區段中，選取 [**同步處理 Azure Active Directory 使用者至 Elium**]。

    ![將 Azure AD 使用者對應的連結同步處理至 Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. 在 [**屬性**對應] 區段中，檢查從 Azure AD 同步處理到 Elium 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Elium 中的使用者帳戶，以進行更新作業。 選取 [儲存]**** 認可任何變更。

    ![Azure AD 和 Elium 之間的屬性對應](media/Elium-provisioning-tutorial/userattribute.png)

1. 若要設定範圍篩選準則，請遵循[範圍篩選教學](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)課程中的指示。

1. 若要啟用 Elium 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![將 [布建狀態] 設定為 [開啟]](common/provisioning-toggle-on.png)

1. 在 [**設定**] 區段的 [**範圍**] 下拉式清單方塊中選取所需的值，以定義您想要布建到 Elium 的使用者和群組。

    ![[布建領域] 清單方塊](common/provisioning-scope.png)

1. 當您準備好要布建時，請選取 [**儲存**]。

    ![[儲存] 按鈕以提供布建設定](common/provisioning-configuration-save.png)

這項作業會啟動 [**設定**] 區段的 [**範圍**] 中定義的所有使用者和群組的首次同步處理。 此初始同步處理常式所花費的時間比之後同步。 如需布建所需時間的詳細資訊，請參閱布建[使用者需要多久時間？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

使用 [**目前狀態**] 區段來監視進度，並遵循您的布建活動報告的連結。 布建活動報表會描述 Elium 上的 Azure AD 布建服務所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)布建的狀態。 若要讀取 Azure AD 布建記錄，請參閱[關於自動使用者帳戶](../app-provisioning/check-status-user-account-provisioning.md)布建的報告。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶](../app-provisioning/configure-automatic-user-provisioning-portal.md)布建。
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
