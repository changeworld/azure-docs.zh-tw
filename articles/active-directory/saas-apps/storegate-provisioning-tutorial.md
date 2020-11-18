---
title: 教學課程：以 Azure Active Directory 設定 Storegate 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來對 Storegate 自動佈建及取消佈建使用者帳戶。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357929"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>教學課程：設定 Storegate 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Samanage 自動佈建及取消佈建使用者和/或群組時，應在 Storegate 與 Azure Active Directory (Azure AD) 中執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Storegate 租用戶](https://www.storegate.com)
* Storegate 上具有系統管理員權限的使用者帳戶。

## <a name="assign-users-to-storegate"></a>將使用者指派給 Storegate

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Storegate。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Storegate：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>將使用者指派給 Storegate 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Storegate，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Storegate 時，您必須在 [指派] 對話方塊中選取任何有效應用程式特有的角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="set-up-storegate-for-provisioning"></a>設定 Storegate 以進行佈建

將 Storegate 設定為可使用 Azure AD 自動佈建使用者之前，您必須從 Storegate 擷取一些佈建資訊。

1. 登入您的 [Storegate 管理主控台](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) 並按一下右上角的使用者圖示，然後選取 [帳戶設定] 來瀏覽至設定。

    ![Storegate 新增 SCIM](media/storegate-provisioning-tutorial/admin.png)

2. 在 [設定] 中，瀏覽至 [小組] > [設定]，並確認已將 [單一登入] 區段切換為開啟。

    ![Storegate 設定](media/storegate-provisioning-tutorial/team.png)

    ![Storegate 切換按鈕](media/storegate-provisioning-tutorial/sso.png)

3. 複製 **租用戶 URL** 和 **權杖**。 這些值會分別輸入到 Azure 入口網站中 Storegate 應用程式 [佈建] 索引標籤中的 [租用戶 URL] 和 [祕密權杖] 欄位。 

    ![Storegate 建立權杖](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>從資源庫新增 Storegate

若要將 Storegate 設定為可使用 Azure AD 自動佈建使用者，您必須從 Azure AD 應用程式庫將 Storegate 新增至您的受控 SaaS 應用程式清單。

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Storegate**，然後在 [結果] 面板中選取 [Storegate]。 

    ![結果清單中的 Storegate](common/search-new-app.png)

5. 選取 [註冊 Storegate] 按鈕，這會將您重新導向至 Storegate 的登入頁面。 

    ![Storegate OIDC 新增](media/storegate-provisioning-tutorial/signup.png)

6.  登入您的 [Storegate 管理主控台](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) 並按一下右上角的使用者圖示，然後選取 [帳戶設定] 來瀏覽至設定。

    ![Storegate 登入](media/storegate-provisioning-tutorial/admin.png)

7. 在 [設定] 中瀏覽至 [小組] > [設定]，然後按一下 [單一登入] 區段中的切換按鈕，這將會啟動同意流程。 按一下 [啟動]。

    ![Storegate 小組](media/storegate-provisioning-tutorial/team.png)

    ![Storegate SSo](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate 啟動](media/storegate-provisioning-tutorial/activate.png)

8. 當 Storegate 是 OpenIDConnect 應用程式時，請選擇使用您的 Microsoft 公司帳戶登入 Storegate。

    ![Storegate OIDC 登入](media/storegate-provisioning-tutorial/login.png)

9. 驗證成功之後，請接受同意頁面的同意提示。 然後，應用程式會自動新增至您的租用戶，並將您重新導向至您的 Storegate 帳戶。

    ![Storegate OIDc 同意](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>對 Storegate 設定自動使用者佈建 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Storegate 中建立、更新和停用使用者和/或群組。

> [!NOTE]
> 若要深入了解 Storegate 的 SCIM 端點，請參閱[本文](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)。

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>若要在 Azure AD 中為 Storegate 設定自動使用者佈建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Storegate]。

    ![應用程式清單中的 Storegate 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://dialpad.com/scim`。 輸入您先前在 Storegate [祕密權杖] 中擷取並儲存的值。 按一下 [測試連線]，以確保 Azure AD 可以連線至 Storegate。 如果連線失敗，請確定您的 Storegate 帳戶具有管理員權限，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]  。

8. 在 [對應] 區段下，選取 [將 Azure Active Directory 使用者同步至 Storegate]。

    ![Storegate 使用者對應](media/storegate-provisioning-tutorial/usermappings.png)

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Storegate 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Storegate 中的使用者帳戶，以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Storegate 使用者屬性](media/storegate-provisioning-tutorial/userattributes.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要對 Storegate 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Storegate 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並進入佈建活動報告的連結，以透過報告了解 Storegate 上的 Azure AD 佈建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
