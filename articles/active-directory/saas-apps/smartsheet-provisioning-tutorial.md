---
title: 教學課程：使用 Azure Active Directory 設定 Smartsheet 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至 Smartsheet。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063179"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>教學課程：設定 Smartsheet 來自動布建使用者

本教學課程的目的是要示範要在 Smartsheet 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組至 Smartsheet。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Smartsheet 租使用者](https://www.smartsheet.com/pricing)
* 具有系統管理員許可權之 Smartsheet Enterprise 或企業頂級方案上的使用者帳戶。

## <a name="assign-users-to-smartsheet"></a>將使用者指派給 Smartsheet

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 Smartsheet。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 Smartsheet：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>將使用者指派給 Smartsheet 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Smartsheet，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Smartsheet 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

* 若要確保 Smartsheet 與 Azure AD 之間使用者角色指派的同位，建議使用完整 Smartsheet 使用者清單中填入的相同角色指派。 若要從 Smartsheet 抓取此使用者清單，請流覽至 **帳戶管理員 > 使用者管理 > 其他動作 > 下載使用者清單（csv）** 。

* 若要存取應用程式中的某些功能，Smartsheet 需要使用者擁有多個角色。 若要深入瞭解 Smartsheet 中的使用者類型和許可權，請移至 [[使用者類型和許可權](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)]。

*  如果使用者在 Smartsheet 中已指派多個角色，您**必須**確保這些角色指派會在 Azure AD 中複寫，以避免使用者無法永久存取 Smartsheet 物件的情況。 Smartsheet 中的每個唯一角色都**必須**指派給 Azure AD 中的不同群組。 接著，**必須**將使用者新增至對應于所需角色的每個群組。 

## <a name="set-up-smartsheet-for-provisioning"></a>設定 Smartsheet 以提供布建

將 Smartsheet 設定為使用 Azure AD 自動布建使用者之前，您必須啟用 Smartsheet 上的 SCIM 布建。

1. 在 **[Smartsheet 入口網站](https://app.smartsheet.com/b/home)** 中以**系統管理員**身分登入，然後流覽至 [**帳戶管理員**]。

    ![Smartsheet 帳戶管理員](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. 移至 **安全性控制 > 使用者自動**布建 > 編輯。

    ![Smartsheet 安全性控制](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. 針對您打算從 Azure AD 到 Smartsheet 布建的使用者，新增及驗證電子郵件網域。 選擇 [**未啟用**] 以確保所有布建動作只源自于 Azure AD，同時確保您的 Smartsheet 使用者清單與 Azure AD 指派同步。

    ![Smartsheet 使用者布建](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 驗證完成後，您就必須啟用網域。 

    ![Smartsheet 啟用網域](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. 藉由流覽至 [**應用程式和**整合]，產生使用 Azure AD 設定自動使用者布建所需的**秘密權杖**。

    ![Smartsheet 安裝](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. 選擇 [ **API 存取**]。 按一下 [**產生新的存取權杖**]。

    ![Smartsheet 安裝](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. 定義 API 存取權杖的名稱。 按一下 [確定]。

    ![Smartsheet 安裝](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. 複製 API 存取權杖並加以儲存，因為這會是您唯一可以查看的時間。 這在 Azure AD 的 [**秘密權杖**] 欄位中是必要的。

    ![Smartsheet token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>從資源庫新增 Smartsheet

若要使用 Azure AD 設定 Smartsheet 來自動布建使用者，您需要從 Azure AD 應用程式資源庫將 Smartsheet 新增至受控 SaaS 應用程式清單。

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**Smartsheet**，並在 [結果] 面板中選取 [ **Smartsheet** ]。 

    ![結果清單中的 Smartsheet](common/search-new-app.png)

5. 選取 [**註冊 Smartsheet** ] 按鈕，以將您重新導向至 Smartsheet 的登入頁面。 

    ![Smartsheet OIDC 新增](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. 當 Smartsheet 是 OpenIDConnect 應用程式時，請選擇使用您的 Microsoft 工作帳戶登入 Smartsheet。

    ![Smartsheet OIDC 登入](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. 驗證成功之後，請接受同意頁面的同意提示。 然後，應用程式會自動新增至您的租使用者，並將您重新導向至您的 Smartsheet 帳戶。

    ![Smartsheet OIDc 同意](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>設定自動使用者布建至 Smartsheet 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Smartsheet 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>若要在 Azure AD 中設定 Smartsheet 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **Smartsheet**]。

    ![應用程式清單中的 Smartsheet 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下，于 [**租使用者 URL**] 中輸入 `https://scim.smartsheet.com/v2/`。 輸入您先前從 Smartsheet 的 [**秘密權杖**] 中抓取並儲存的值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 Smartsheet。 如果連線失敗，請確定您的 Smartsheet 帳戶具有系統管理員（SysAdmin）許可權，然後再試一次。

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Smartsheet**]。

    ![Smartsheet 使用者對應](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到 Smartsheet 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Smartsheet 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Smartsheet 使用者屬性](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Smartsheet 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Smartsheet 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述 Smartsheet 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Smartsheet 不支援虛刪除。 當使用者的**active**屬性設定為 False 時，Smartsheet 會永久刪除該使用者。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
