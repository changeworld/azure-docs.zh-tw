---
title: 教學課程：使用 Azure Active Directory 來設定自動使用者布建的雪花 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至雪花。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063131"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的雪花

本教學課程的目的是要示範在雪花和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組至雪花。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [雪花租](https://www.Snowflake.com/pricing/)使用者。
* 雪花中具有系統管理員許可權的使用者帳戶。

## <a name="assigning-users-to-snowflake"></a>將使用者指派給雪花

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要雪花的存取權。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給雪花：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>將使用者指派給雪花的重要秘訣

* 建議將單一 Azure AD 使用者指派給雪花，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給雪花時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-snowflake-for-provisioning"></a>設定布建的雪花

設定雪花以 Azure AD 自動布建使用者之前，您必須在雪花上啟用 SCIM 布建。

1. 登入您的雪花式系統管理主控台。 在反白顯示的工作表中輸入如下所示的查詢，然後按一下 [**執行**]。

    ![雪花式系統管理主控台](media/Snowflake-provisioning-tutorial/image00.png)

2.  系統會為您的雪花租使用者產生 SCIM 存取權杖。 若要取得它，請按一下下方反白顯示的連結。

    ![雪花新增 SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. 複製產生的權杖值，然後按一下 [**完成**]。 此值會在 Azure 入口網站的雪花式應用程式之 [布建] 索引標籤的 [**密碼權杖**] 欄位中輸入。

    ![雪花新增 SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>從資源庫新增雪花

若要使用 Azure AD 設定雪花來自動布建使用者，您需要從 Azure AD 應用程式庫 addSnowflake 至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增雪花，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**雪花**，在結果面板中選取 [**雪花**]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 Snowflake](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>設定自動使用者布建至雪花 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在雪花中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇啟用雪花的 SAML 型單一登入，請遵循[雪花式單一登入教學](Snowflake-tutorial.md)課程中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>若要在 Azure AD 中設定雪花的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Snowflake]。

    ![應用程式清單中的 Snowflake 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下，于 [租使用者 URL] 中輸入 `https://<Snowflake Account URL>/scim/v2`。 租使用者 URL 的範例： `https://acme.snowflakecomputing.com/scim/v2`

6. 輸入稍早在**秘密權杖**中所取得的**SCIM Authentication Token**值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到雪花。 如果連線失敗，請確定您的雪花帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

7. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. Haga clic en **Guardar**.

9. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至雪花**]。

    ![雪花使用者對應](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至雪花的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對雪花中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![雪花式使用者屬性](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至雪花**]。

    ![雪花群組對應](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至雪花的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對雪花中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![雪花群組屬性](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

14. 若要啟用雪花的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

15. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至雪花的使用者和/或群組。 如果無法使用此選項，請在 [系統管理員認證] 底下設定必要欄位，然後按一下 [**儲存**] 並重新整理頁面。 

    ![佈建範圍](common/provisioning-scope.png)

16. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

    此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述雪花上的 Azure AD 布建服務所執行的所有動作。

    如需如何讀取 Azure AD 布建記錄的詳細資訊，請參閱[關於自動使用者帳戶](../app-provisioning/check-status-user-account-provisioning.md)布建的報告

## <a name="connector-limitations"></a>連接器限制

* 雪花產生的 SCIM 權杖會在6個月後到期。 請注意，這些需求必須在到期前重新整理，才能讓布建同步處理繼續運作。 

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶](../app-provisioning/configure-automatic-user-provisioning-portal.md)布建。
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟
* [了解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)。
