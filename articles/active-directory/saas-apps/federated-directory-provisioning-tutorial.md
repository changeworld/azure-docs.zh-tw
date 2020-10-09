---
title: 教學課程：使用 Azure Active Directory 設定同盟目錄來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和解除布建到同盟目錄。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 5596dfab98f6826cd61241441a9ddade72e36674
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851266"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>教學課程：設定同盟目錄來自動布建使用者

本教學課程的目的是要示範在同盟目錄中執行的步驟，以及 Azure Active Directory (Azure AD) 將 Azure AD 設定為將使用者及/或群組自動布建和解除布建到同盟目錄。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* 同盟[目錄](https://www.federated.directory/pricing)。
* 同盟目錄中具有系統管理員許可權的使用者帳戶。

## <a name="assign-users-to-federated-directory"></a>將使用者指派給同盟目錄
Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者布建的內容中，只會同步處理在 Azure AD 中指派給應用程式的使用者和/或群組。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者及/或群組需要存取同盟目錄。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給同盟目錄：

 * [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>將使用者指派給同盟目錄的重要秘訣
 * 建議將單一 Azure AD 使用者指派給同盟目錄，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給同盟目錄時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色 (如果可用) 。 具有預設存取角色的使用者會從佈建中排除。
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>設定同盟目錄以進行布建

將同盟目錄設定為使用 Azure AD 進行自動使用者布建之前，您必須在同盟目錄上啟用 SCIM 布建。

1. 登入您的同盟 [目錄管理主控台](https://federated.directory/of)

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::

2. 流覽至 **目錄 > 的使用者目錄** ，然後選取您的租使用者。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::

3.  若要產生永久持有人權杖，請流覽至 **目錄金鑰 > 建立新的金鑰。** 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::

4. 建立目錄金鑰。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::
    

5. 複製 [存取權杖]  值。 在 Azure 入口網站中，您同盟目錄應用程式的 [布建] 索引標籤中，將會在 [ **秘密權杖** ] 欄位中輸入此值。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>從資源庫新增同盟目錄

若要使用 Azure AD 設定同盟目錄來自動布建使用者，您需要從 Azure AD 應用程式資源庫將同盟目錄新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增同盟目錄，請執行下列步驟：**

1. 在 [ **[Azure 入口網站](https://portal.azure.com)** 的左側導覽面板中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要加入新的應用程式，請選取窗格頂端的 [ **新增應用程式** ] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入同盟 **目錄**，然後在 [結果] 面板中選取 [同盟 **目錄** ]。

    ![結果清單中的同盟目錄](common/search-new-app.png)

5. 在不同的瀏覽器中，流覽至下方反白顯示的 **URL** 。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::

6. 按一下 [ **登入**]。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::

7.  由於同盟目錄是 OpenIDConnect 應用程式，請選擇使用您的 Microsoft 公司帳戶登入同盟目錄。
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::
 
8. 驗證成功之後，請接受同意頁面的同意提示。 然後，應用程式會自動新增至您的租使用者，而系統會將您重新導向至您的同盟目錄帳戶。

    ![同盟目錄新增 SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>設定自動使用者布建至同盟目錄 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在同盟目錄中建立、更新及停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>若要在 Azure AD 中設定同盟目錄的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [同盟 **目錄**]。

    ![應用程式清單中的同盟目錄連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![已呼叫 [布建] 選項的 [管理選項] 螢幕擷取畫面。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[布建模式] 下拉式清單的螢幕擷取畫面，其中已呼叫 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://api.federated.directory/v2/`。 輸入您先前從 **秘密權杖**中的同盟目錄中取出和儲存的值。 按一下 [ **測試連接** ] 以確保 Azure AD 可以連線到同盟目錄。 如果連接失敗，請確定您的同盟目錄帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

8. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [儲存]。

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至同盟目錄**]。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::
    
    
11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步至同盟目錄的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對同盟目錄中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="同盟目錄管理主控台的螢幕擷取畫面，其中顯示輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::
    

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用同盟目錄的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至同盟目錄的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [ **同步處理詳細資料** ] 區段來監視進度，並遵循連結來布建活動報告，其會說明同盟目錄上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 布建記錄的詳細資訊，請參閱自動布建 [使用者帳戶的報告](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
