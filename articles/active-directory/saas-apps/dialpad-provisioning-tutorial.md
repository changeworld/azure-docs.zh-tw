---
title: 教學課程：以 Azure Active Directory 設定 Dialpad 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 Dialpad。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: b88e618da3f8a23c0517aaeb251e54bf559fc468
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358468"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>教學課程：設定 Dialpad 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Dialpad 自動佈建及取消佈建使用者和/或群組時，Dialpad 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

> 此連接器目前為預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [Dialpad 租用戶](https://www.dialpad.com/pricing/)。
* Dialpad 中具有管理員權限的使用者帳戶。

## <a name="assign-users-to-dialpad"></a>將使用者指派給 Dialpad
Azure Active Directory 使用所謂指派的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Dialpad。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Dialpad：
 
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>將使用者指派給 Dialpad 的重要秘訣

 * 建議將單一 Azure AD 使用者指派給 Dialpad，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Dialpad 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有預設存取角色的使用者會從佈建中排除。

## <a name="setup-dialpad-for-provisioning"></a>設定 Dialpad 以進行佈建

將 Dialpad 設定為可使用 Azure AD 自動佈建使用者之前，您必須從 Dialpad 擷取一些佈建資訊。

1. 登入您的 [Dialpad 管理主控台](https://dialpadbeta.com/login)，然後選取 [管理員設定]。 確定已從下拉式清單中選取 [我的公司]。 瀏覽至 [驗證] > [API 金鑰]。

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad01.png" alt-text="螢幕擷取畫面：Dialpad 管理主控台，已醒目提示 [設定] 圖示、[我的公司]、[驗證] 和 [API 金鑰]，並已選取 [我的公司]。" border="false":::

2. 按一下 [新增金鑰] 並設定秘密權杖的屬性，以產生新的金鑰。

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad02.png" alt-text="螢幕擷取畫面：Dialpad 管理主控台中的 [API 金鑰] 頁面。已醒目提示 [新增金鑰]。" border="false":::

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad03.png" alt-text="螢幕擷取畫面：Dialpad 管理主控台中的 [編輯 API 金鑰] 頁面。已醒目提示 [儲存] 按鈕。" border="false":::

3. 針對最近建立的 API 金鑰，按一下 [按一下以顯示值] 按鈕，並複製所顯示的值。 此值會輸入到 Azure 入口網站中 Dialpad 應用程式 [佈建] 索引標籤中的 [祕密權杖] 欄位。 

    ![Dialpad 建立權杖](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>從資源庫新增 Dialpad

若要以 Azure AD 設定 Dialpad 來自動佈建使用者，您必須將 Dialpad 從 Azure AD 應用程式庫新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Dialpad，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Dialpad**，然後在結果面板中選取 [Dialpad]。
    ![結果清單中的 Dialpad](common/search-new-app.png)

5. 瀏覽至下面已在另一個瀏覽器中醒目提示的 [URL]。 

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad05.png" alt-text="螢幕擷取畫面：顯示 Dialpad 應用程式相關資訊的頁面。在 URL 底下，已列出並醒目提示位址。" border="false":::

6. 在右上角選取 [登入] > [線上使用 Dialpad]。

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad06.png" alt-text="Dialpad 網站的螢幕擷取畫面。已醒目提示 [登入]，並已開啟 [登入] 索引標籤。也已醒目提示 [線上使用 Dialpad]。" border="false":::

7. 當 Dialpad 是 OpenIDConnect 應用程式時，請選擇使用您的 Microsoft 公司帳戶登入 Dialpad。

    :::image type="content" source="media/dialpad-provisioning-tutorial/loginpage.png" alt-text="Dialpad 網站中 [開始撥打電話] 頁面的螢幕擷取畫面。已醒目提示 [使用 Office 365 登入] 按鈕。" border="false":::

8. 驗證成功之後，請接受同意頁面的同意提示。 然後，應用程式會自動新增至您的租用戶，並將您重新導向至您的 Dialpad 帳戶。

    :::image type="content" source="media/dialpad-provisioning-tutorial/redirect.png" alt-text="螢幕擷取畫面：顯示 Microsoft 驗證頁面，其內容指出 Dialpad 應用程式已要求存取某些資料。已醒目提示 [接受] 按鈕。" border="false":::

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>設定使用者自動佈建至 Dialpad

本節將引導逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Dialpad 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>在 Azure AD 中為 Dialpad 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Dialpad]。

    ![應用程式清單中的 Dialpad 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://dialpad.com/scim`。 在 [祕密權杖] 中輸入您先前從 Dialpad 擷取並儲存的值。 按一下 [測試連線]，以確保 Azure AD 可以連線至 Dialpad。 如果連線失敗，請確定您的 Dialpad 帳戶具有管理員權限並再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]  。

8. 在 [對應] 區段下，選取 [將 Azure Active Directory 使用者同步至 Dialpad]。

    ![Dialpad 使用者對應](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Dialpad 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Dialpad 中的使用者帳戶以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Dialpad 使用者屬性](media/dialpad-provisioning-tutorial/dialpad07.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Dialpad 的 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Dialpad 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，當中會描述 Dialpad 上的 Azure AD 佈建服務所執行之所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>連接器限制
* Dialpad 目前不支援群組重新命名。 這表示對 Azure AD 中群組的 **displayName** 所做的任何變更，都不會更新並反映在 Dialpad 中。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
