---
title: 教學課程：使用 Azure Active Directory 設定 Zscaler Private Access （ZPA）來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，將使用者帳戶自動布建和取消布建至 Zscaler 私用存取（ZPA）。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77064133"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>教學課程：設定 Zscaler Private Access （ZPA）來自動布建使用者

本教學課程的目的是要示範在 Zscaler Private Access （ZPA）和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組，以 Zscaler 私用存取（ZPA）。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先決條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Zscaler Private Access （ZPA）租使用者](https://www.zscaler.com/pricing-and-plans#contact-us)
* Zscaler Private Access （ZPA）中具有系統管理員許可權的使用者帳戶。

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>將使用者指派給 Zscaler 私用存取（ZPA）

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 Zscaler Private Access （ZPA）。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 Zscaler Private Access （ZPA）：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>將使用者指派給 Zscaler 私用存取（ZPA）的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Zscaler Private Access （ZPA），以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Zscaler Private Access （ZPA）時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>設定 Zscaler 私用存取（ZPA）以提供布建

1. 登入您的[Zscaler Private Access （ZPA）管理主控台](https://admin.private.zscaler.com/)。 流覽至 [系統**管理] > IdP**設定]。

    ![Zscaler Private Access （ZPA）管理主控台](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  確認是否已設定**單一登入**的 IdP。 如果未設定 IdP，請按一下螢幕右上角的加號圖示來新增一個。

    ![Zscaler Private Access （ZPA） Add SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. 請依照 [**新增 IdP**設定] 嚮導來新增 IdP。 將 [**單一登入**] 欄位保持設定為 [**使用者**]。 提供**名稱**，然後從下拉式清單中選取 [**網域**]。 按一下 **[下一步**] 以流覽至下一個視窗。

    ![Zscaler Private Access （ZPA） Add IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. 下載**服務提供者憑證**。 按一下 **[下一步**] 以流覽至下一個視窗。

    ![Zscaler 私用存取（ZPA） SP 憑證](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. 在下一個視窗中，上傳先前下載的**服務提供者憑證**。

    ![Zscaler Private Access （ZPA）上傳憑證](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  向下滾動以提供 [**單一登入 URL** ] 和 [ **IdP 實體識別碼**]。

    ![Zscaler Private Access （ZPA） IdP ID](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  向下滾動以**啟用 SCIM 同步**。按一下 [**產生新的權杖**] 按鈕。 複製**持有人權杖**。 此值會在 Azure 入口網站的 Zscaler Private Access （ZPA）應用程式的 [布建] 索引標籤的 [密碼權杖] 欄位中輸入。

    ![Zscaler Private Access （ZPA）建立權杖](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  若要找出**租使用者 URL** ，請流覽至 [系統管理]， **> IdP**設定]。 按一下頁面上所列新加入之 IdP 設定的名稱。

    ![Zscaler Private Access （ZPA） Idp 名稱](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  向下滾動以在頁面的結尾處， **SCIM 服務提供者端點**。 複製**SCIM 服務提供者端點**。 此值將會在 Azure 入口網站中 Zscaler Private Access （ZPA）應用程式的 [布建] 索引標籤的 [租使用者 URL] 欄位中輸入。

    ![Zscaler Private Access （ZPA） SCIM URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>從資源庫新增 Zscaler Private Access （ZPA）

設定 Zscaler 私用存取（ZPA）以 Azure AD 自動布建使用者之前，您必須從 Azure AD 應用程式庫將 Zscaler 私用存取（ZPA）新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫加入 Zscaler Private Access （ZPA），請執行下列步驟：**

1. 在**[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**Zscaler Private access （ZPA）**，在結果面板中選取 [ **Zscaler PRI加值稅E access （ZPA）** ]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 Zscaler Private Access (ZPA)](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>設定自動使用者布建以 Zscaler 私用存取（ZPA） 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Zscaler 私用存取（ZPA）中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以依照[Zscaler 私用存取（ZPA）單一登入教學](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)課程中提供的指示，選擇啟用 Zscaler Private ACCESS （ZPA）的 SAML 型單一登入。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此互補。

> [!NOTE]
> 若要深入瞭解 Zscaler 私用存取的 SCIM 端點，請參閱[此](https://www.zscaler.com/partners/microsoft)。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>若要在 Azure AD 中設定自動使用者布建以進行 Zscaler 私用存取（ZPA）：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler Private Access (ZPA)]****。

    ![應用程式清單中的 Zscaler Private Access (ZPA)](common/all-applications.png)

3. 選取 [**布**建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將布建模式設定為 [**自動** **]** 。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下，輸入稍早在 [**租使用者 URL**] 中取得的**SCIM 服務提供者端點**值。 輸入稍早在**秘密權杖**中所取得的**持有人權杖**值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 Zscaler 私用存取（ZPA）。 如果連線失敗，請確定您的 Zscaler Private Access （ZPA）帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 **[儲存]** 。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者 Zscaler 私用存取（ZPA）**]。

    ![Zscaler Private Access （ZPA）使用者對應](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步到 Zscaler 私用存取（ZPA）的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Zscaler Private ACCESS （ZPA）中的使用者帳戶以進行更新作業。 選取 [儲存]**** 按鈕以認可所有變更。

    ![Zscaler Private Access （ZPA）使用者屬性](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組到 Zscaler 私用存取（ZPA）**]。

    ![Zscaler Private Access （ZPA）群組對應](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步到 Zscaler 私用存取（ZPA）的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Zscaler Private ACCESS （ZPA）中的群組以進行更新作業。 選取 [儲存]**** 按鈕以認可所有變更。

    ![Zscaler Private Access （ZPA）群組屬性](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Zscaler Private Access （ZPA）的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Zscaler 私用存取（ZPA）的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述 Zscaler 私用存取（ZPA）上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶布建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

