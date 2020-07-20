---
title: 教學課程：使用 Azure Active Directory 設定自動使用者布建的 Netskope 系統管理員主控台 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至 Netskope 系統管理員主控台。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77061215"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>教學課程：設定 Netskope 系統管理員主控台以進行自動使用者布建

本教學課程的目的是要示範要在 Netskope 系統管理員主控台和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組至 Netskope 系統管理員主控台。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Netskope 系統管理員主控台租使用者](https://www.netskope.com/)
* Netskope 中具有系統管理員許可權的使用者帳戶系統管理員主控台。

## <a name="assigning-users-to-netskope-administrator-console"></a>將使用者指派給 Netskope 系統管理員主控台

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 Netskope 系統管理員主控台。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 Netskope 系統管理員主控台：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>將使用者指派給 Netskope 系統管理員主控台的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Netskope 系統管理員主控台，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Netskope 系統管理員主控台時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>設定 Netskope 系統管理員主控台以提供布建

1. 登入您的[Netskope 系統管理員主控台管理主控台](https://netskope.goskope.com/)。 流覽至 [**首頁] > [設定**]。

    ![Netskope 系統管理員主控台管理主控台](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  流覽至 [**工具**]。 在 [**工具**] 功能表下，流覽至 [**目錄工具] > SCIM 整合**]。

    ![Netskope 系統管理員主控台工具](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope 系統管理員主控台新增 SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. 向下滾動，然後按一下 [**新增權杖**] 按鈕。 在 [**新增 OAuth 用戶端名稱**] 對話方塊中，提供**用戶端名稱**，然後按一下 [**儲存**] 按鈕。

    ![Netskope 系統管理員主控台新增權杖](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope 系統管理員主控台用戶端名稱](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  複製**SCIM 伺服器 URL**和**權杖**。 這些值會分別在 [租使用者 URL] 和 [秘密權杖] 欄位中輸入，在 Azure 入口網站的 Netskope 系統管理員主控台應用程式的 [布建] 索引標籤中。

    ![Netskope 系統管理員主控台建立權杖](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>從資源庫新增 Netskope 系統管理員主控台

設定 Netskope 系統管理員主控台以 Azure AD 自動布建使用者之前，您需要從 Azure AD 應用程式庫將 Netskope 系統管理員主控台新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Netskope 系統管理員主控台，請執行下列步驟：**

1. 在**[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**Netskope 系統管理員主控台**，在結果面板中選取 [ **Netskope 系統管理員主控台**]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 Netskope 系統管理員主控台](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>設定自動使用者布建至 Netskope 系統管理員主控台 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Netskope 系統管理員主控台中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以遵循[Netskope 系統管理員主控台單一登入教學](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)課程中提供的指示，選擇啟用 Netskope 系統管理員主控台的 SAML 型單一登入。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此互補。

> [!NOTE]
> 若要深入瞭解 Netskope 系統管理員主控台的 SCIM 端點，請[參閱。](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>若要在 Azure AD 中設定 Netskope 系統管理員主控台的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Netskope 系統管理員主控台]****。

    ![應用程式清單中的 Netskope 系統管理員主控台連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建] 索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下，輸入稍早在 [**租使用者 url**] 中抓取的**SCIM 伺服器 URL**值。 輸入稍早在**秘密權杖**中所取得的**TOKEN**值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 Netskope 系統管理員主控台。 如果連線失敗，請確定您的 Netskope 系統管理員主控台帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案] 。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者] 以 Netskope 系統管理員主控台**。

    ![Netskope 系統管理員主控台使用者對應](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到 Netskope 系統管理員主控台的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Netskope 系統管理員主控台中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Netskope 系統管理員主控台使用者屬性](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組到 Netskope 系統管理員主控台**。

    ![Netskope 系統管理員主控台群組對應](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到 Netskope 系統管理員主控台的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Netskope 系統管理員主控台中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Netskope 系統管理員主控台群組屬性](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Netskope 系統管理員主控台的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Netskope 系統管理員主控台的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述 Netskope 系統管理員主控臺上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

