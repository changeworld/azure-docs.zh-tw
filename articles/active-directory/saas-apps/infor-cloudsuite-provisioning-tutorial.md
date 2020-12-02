---
title: 教學課程：以 Azure Active Directory 設定 Infor CloudSuite 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來對 Infor CloudSuite 自動佈建及取消佈建使用者帳戶。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353896"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>教學課程：設定 Infor CloudSuite 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Infor CloudSuite 自動佈建及取消佈建使用者和/或群組時，Infor CloudSuite 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Infor CloudSuite 租用戶](https://www.infor.com/products/infor-os)
* Infor CloudSuite 中具有管理員權限的使用者帳戶。

## <a name="assigning-users-to-infor-cloudsuite"></a>將使用者指派給 Infor CloudSuite

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Infor CloudSuite。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Infor CloudSuite：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>將使用者指派給 Infor CloudSuite 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Infor CloudSuite，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Infor CloudSuite 時，您必須在 [指派] 對話方塊中選取任何有效應用程式特有的角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>設定 Infor CloudSuite 以進行佈建

1. 登入您的 [Infor CloudSuite 管理主控台](https://www.infor.com/customer-center)。 按一下 [使用者] 圖示，然後瀏覽至 [使用者管理]。

    ![Infor CloudSuite 管理主控台](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  按一下畫面左上角的功能表圖示。 按一下 [管理]。

    ![Infor CloudSuite 新增 SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  瀏覽至 [SCIM 帳戶]。

    ![Infor CloudSuite SCIM 帳戶](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  按一下加號圖示來新增管理使用者。 提供 [SCIM 密碼] ，然後在 [確認密碼] 下輸入相同的密碼。 按一下資料夾圖示以儲存密碼。 然後，您會看到為管理使用者產生的 [使用者識別碼]。

    ![Infor CloudSuite 管理使用者](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite 密碼](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Infor CloudSuite 管理主控台的螢幕擷取畫面，其中顯示已醒目提示的資料表資料列。該資料列包含使用者識別碼、密碼和時間戳記。" border="false":::

5. 若要產生持有人權杖，請複製 [使用者識別碼] 和 [SCIM 密碼]。 將這兩個值貼到 Notepad++ 中，並以冒號分隔。 瀏覽至 [外掛程式] > [MIME 工具] > [Basic64 編碼]，以將字串值編碼。 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Notepad++ 文件的螢幕擷取畫面。[外掛程式] 功能表中會醒目提示 [MIME 工具]。[MIME 工具] 功能表中會醒目提示 [Base64 編碼]。" border="false":::

3.  複製持有人權杖。 此值會輸入到 Azure 入口網站中 Infor CloudSuite 應用程式 [佈建] 索引標籤中的 [祕密權杖] 欄位。

## <a name="add-infor-cloudsuite-from-the-gallery"></a>從資源庫新增 Infor CloudSuite

將 Infor CloudSuite 設定為可使用 Azure AD 自動佈建使用者之前，您必須從 Azure AD 應用程式庫將 Infor CloudSuite 新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Infor CloudSuite，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Infor CloudSuite**，並在結果面板中選取 [Infor CloudSuite]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Infor CloudSuite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>設定將使用者自動佈建至 Infor CloudSuite 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Infor CloudSuite 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 建議您選擇為 Infor CloudSuite 啟用 SAML 型單一登入，方法是遵循 [Infor CloudSuite 單一登入教學課程](./infor-cloud-suite-tutorial.md)中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

> [!NOTE]
> 若要深入了解 Infor CloudSuite 的 SCIM 端點，請參閱[本文](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#)。

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>若要在 Azure AD 中為 Infor CloudSuite 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Infor CloudSuite]  。

    ![應用程式清單中的 [Infor CloudSuite] 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim`。 在 [祕密權杖] 中，輸入先前擷取的持有人權杖值。 按一下 [測試連線]，以確保 Azure AD 可以連線至 Infor CloudSuite。 如果連線失敗，請確定您的 Infor CloudSuite 帳戶具有管理員權限，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]  。

8. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 Infor CloudSuite]。

    ![Infor CloudSuite 使用者對應](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 Infor CloudSuite 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Infor CloudSuite 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Infor CloudSuite 使用者屬性](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. 在 [對應] 區段中，選取 [將 Azure Active Directory 群組同步至 Infor CloudSuite]。

    ![Infor CloudSuite 群組對應](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 Infor CloudSuite 的群組屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Infor CloudSuite 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Infor CloudSuite 群組屬性](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要對 Infor CloudSuite 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Infor CloudSuite 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，當中會描述 Infor CloudSuite 上的 Azure AD 佈建服務所執行之所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)