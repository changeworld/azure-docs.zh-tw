---
title: 教學課程：使用 Azure Active Directory 設定 iProva 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至 iProva。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: bb730bad2837616aee0ebfa2da04015542782d9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77057495"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>教學課程：設定 iProva 來自動布建使用者

本教學課程的目的是要示範要在 iProva 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組至 iProva。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [IProva 租使用者](https://www.iProva.com/)
* IProva 中具有系統管理員許可權的使用者帳戶。

## <a name="assigning-users-to-iprova"></a>將使用者指派給 iProva

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 iProva。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 iProva：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>將使用者指派給 iProva 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 iProva，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 iProva 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-iprova-for-provisioning"></a>設定 iProva 以提供布建

1. 登入您的[IProva 管理主控台](https://www.iProva.com/)。 流覽至 [**移至 > 應用程式管理**]。

    ![iProva 管理主控台](media/iprova-provisioning-tutorial/admin.png)

2.  按一下 [**外部使用者管理**]。

    ![iProva 新增 SCIM](media/iprova-provisioning-tutorial/external.png)

3. 若要加入新的提供者，請按一下**加號**圖示。 在 [新增**加入提供者**] 對話方塊中，提供**標題**。 您可以選擇新增以**IP 為基礎的存取限制**。 按一下 **[確定]** 按鈕。

    ![iProva 加入新的](media/iprova-provisioning-tutorial/add.png)

    ![iProva 新增提供者](media/iprova-provisioning-tutorial/addprovider.png)

4.  按一下 [**永久權杖**] 按鈕。 複製**永久權杖**並加以儲存，因為這將是您唯一可以查看的時間。 此值將會在 Azure 入口網站中 iProva 應用程式的 [布建] 索引標籤的 [秘密權杖] 欄位中輸入。

    ![iProva 建立權杖](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>從資源庫新增 iProva

將 iProva 設定為使用 Azure AD 自動布建使用者之前，您需要從 Azure AD 應用程式庫將 iProva 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 iProva，請執行下列步驟：**

1. 在**[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**iProva**，在結果面板中選取 [ **iProva** ]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 iProva](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>設定自動使用者布建至 iProva 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 iProva 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以遵循[IProva 單一登入教學](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial)課程中提供的指示，選擇啟用 IPROVA 的 SAML 型單一登入。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此互補。

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>若要在 Azure AD 中設定 iProva 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取[iProva]****。

    ![應用程式清單中的 iProva 連結](common/all-applications.png)

3. 選取 [**布**建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將布建模式設定為 [**自動** **]** 。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下`https://identitymanagement.services.iProva.nl/scim` ，輸入 [**租使用者 URL**]。 輸入稍早在**秘密權杖**中所取得的**永久 token**值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 iProva。 如果連線失敗，請確定您的 iProva 帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]  。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 iProva**]。

    ![iProva 使用者對應](media/iprova-provisioning-tutorial/usermappings.png)

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到 iProva 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 iProva 中的使用者帳戶，以進行更新作業。 選取 [儲存]**** 按鈕以認可所有變更。

    ![iProva 使用者屬性](media/iprova-provisioning-tutorial/userattributes.png)

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至 iProva**]。

    ![iProva 群組對應](media/iprova-provisioning-tutorial/groupmappings.png)

11. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 iProva 的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對 iProva 中的群組以進行更新作業。 選取 [儲存]**** 按鈕以認可所有變更。

    ![iProva 群組屬性](media/iprova-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 iProva 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 iProva 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述 iProva 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶布建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

