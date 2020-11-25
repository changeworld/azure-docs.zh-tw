---
title: 教學課程：以 Azure Active Directory 設定 Federated Directory 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建並取消佈建使用者帳戶至 Federated Directory。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 8ca7654d930247f70d85cbc20fbbeb961223f05f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998357"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>教學課程：設定 Federated Directory 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Federated Directory 自動佈建及取消佈建使用者和/或群組時，Federated Directory 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [Federated Directory](https://www.federated.directory/pricing)。
* Federated Directory 中具有管理員權限的使用者帳戶。

## <a name="assign-users-to-federated-directory"></a>將使用者指派給 Federated Directory
Azure Active Directory 使用所謂指派的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Federated Directory。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Federated Directory：

 * [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>將使用者指派給 Federated Directory 的重要秘訣
 * 建議將單一 Azure AD 使用者指派給 Federated Directory，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Federated Directory 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有預設存取角色的使用者會從佈建中排除。
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>設定 Federated Directory 以進行佈建

以 Azure AD 設定 Federated Directory 來自動佈建使用者之前，您必須在 Federated Directory 上啟用 SCIM 佈建。

1. 登入您的 [Federated Directory 管理主控台](https://federated.directory/of)

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="螢幕擷取畫面：Federated Directory 管理主控台，其中顯示用於輸入公司名稱的欄位。也可以看見 [登入] 按鈕。" border="false":::

2. 瀏覽至 [目錄] > [使用者目錄] 並選取您的租用戶。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="螢幕擷取畫面：Federated Directory 管理主控台，其中已醒目提示 [目錄] 和 [Federated Directory Azure AD 測試]。" border="false":::

3.  若要產生永久的持有人權杖，請瀏覽至 [目錄金鑰] > [建立新的金鑰]。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="螢幕擷取畫面：Federated Directory 管理主控台的 [目錄金鑰] 頁面。已醒目提示 [建立新的金鑰] 按鈕。" border="false":::

4. 建立目錄金鑰。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="螢幕擷取畫面：Federated Directory 管理主控台的 [建立目錄金鑰] 頁面，其中包含 [名稱] 和 [描述] 欄位，以及 [建立金鑰] 按鈕。" border="false":::
    

5. 複製 [存取權杖]  值。 此值會輸入到 Azure 入口網站中 Federated Directory 應用程式 [佈建] 索引標籤中的 [祕密權杖] 欄位。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="螢幕擷取畫面：Federated Directory 管理主控台中的頁面。可以看見存取權杖預留位置和金鑰名稱、描述和簽發者。" border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>從資源庫新增 Federated Directory

若要以 Azure AD 設定 Federated Directory 來自動佈建使用者，您必須將 Federated Directory 從 Azure AD 應用程式庫新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Federated Directory，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Federated Directory**，然後在結果面板中選取 [Federated Directory]。

    ![結果清單中的 Federated Directory](common/search-new-app.png)

5. 瀏覽至下面已在另一個瀏覽器中醒目提示的 [URL]。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="螢幕擷取畫面：Azure 入口網站中顯示 Federated Directory 上資訊的頁面。已醒目提示 URL 值。" border="false":::

6. 按一下 [登入]。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="螢幕擷取畫面：Federated Directory 網站上的主要功能表。已醒目提示 [登入] 按鈕。" border="false":::

7.  當 Federated Directory 是 OpenIDConnect 應用程式時，請選擇使用 Microsoft 公司帳戶登入 Federated Directory。
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="螢幕擷取畫面：Federated Directory 網站上的 SCIM AD 測試頁面。已醒目提示 [使用 Microsoft 帳戶登入]。" border="false":::
 
8. 驗證成功之後，請接受同意頁面的同意提示。 然後，應用程式會自動新增至您的租用戶，並將您重新導向至您的 Federated Directory 帳戶。

    ![Federated Directory 新增 SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>設定將使用者自動佈建至 Federated Directory 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Federated Directory 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>若要在 Azure AD 中為 Federated Directory 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Federated Directory]。

    ![應用程式清單中的 Federated Directory 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://api.federated.directory/v2/`。 在 [祕密權杖] 中輸入您先前從 Federated Directory 擷取並儲存的值。 按一下 [測試連線]，以確保 Azure AD 可連線至 Federated Directory。 如果連線失敗，請確定 Federated Directory 帳戶具有管理員權限，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

8. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [檔案]  。

10. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 Federated Directory]。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="[對應] 區段的螢幕擷取畫面。醒目提示 [名稱] 底下的同步 Azure Active Directory 使用者與 Federated Directory。" border="false":::
    
    
11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Federated Directory 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Federated Directory 中的使用者帳戶以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="[屬性對應] 頁面的螢幕擷取畫面。資料表會列出 Azure Active Directory 和 Federated Directory 屬性，以及相符的狀態。" border="false":::
    

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要對 Federated Directory 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Federated Directory 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告；當中會描述 Federated Directory 上 Azure AD 佈建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
