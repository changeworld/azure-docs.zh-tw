---
title: 教學課程：以 Azure Active Directory 設定 Oracle Fusion ERP 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建並取消佈建使用者帳戶至 Oracle Fusion ERP。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: da6e1a8ba31f8f4991bde4803191598a015a68b3
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358434"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>教學課程：設定 Oracle Fusion ERP 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Oracle Fusion ERP 自動佈建及取消佈建使用者和/或群組時，應在 Oracle Fusion ERP 與 Azure Active Directory (Azure AD) 中執行的步驟。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Oracle Fusion ERP 租用戶](https://www.oracle.com/applications/erp/)。
* Oracle Fusion ERP 中具有管理員權限的使用者帳戶。

## <a name="assign-users-to-oracle-fusion-erp"></a>將使用者指派給 Oracle Fusion ERP 
Azure Active Directory 使用所謂指派的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Oracle Fusion ERP。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Oracle Fusion ERP：
 
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>將使用者指派給 Oracle Fusion ERP 的重要秘訣 

 * 建議將單一 Azure AD 使用者指派給 Oracle Fusion ERP，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Oracle Fusion ERP 時，您必須在 [指派] 對話方塊中選取任何有效應用程式特有的角色 (如果有的話)。 具有預設存取角色的使用者會從佈建中排除。

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>設定 Oracle Fusion ERP 以進行佈建

以 Azure AD 設定 Oracle Fusion ERP 來自動佈建使用者之前，您必須在 Oracle Fusion ERP 上啟用 SCIM 佈建。

1. 登入您的 [Oracle Fusion ERP 管理主控台](https://cloud.oracle.com/sign-in)

2. 按一下左上角的 [導覽器]。 在 [工具] 下，選取 [安全性主控台]。

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="螢幕擷取畫面：Oracle Fusion ERP 管理主控台中的 [導覽器] 頁面。已醒目提示 [工具和安全性] 主控台。" border="false":::

3. 瀏覽至 [使用者]。
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="螢幕擷取畫面：Oracle Fusion ERP 管理主控台中的面板。已醒目提示 [使用者] 項目。" border="false":::

4. 儲存您將用來登入 Oracle Fusion ERP 管理主控台的管理員使用者帳戶所具有的使用者名稱和密碼。 您必須在 Azure 入口網站 Oracle Fusion ERP 應用程式的 [佈建] 索引標籤中，於 [管理員使用者名稱] 和 [密碼] 欄位中輸入這些值。

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>從資源庫新增 Oracle Fusion ERP

若要以 Azure AD 設定 Oracle Fusion ERP 來自動佈建使用者，您必須將 Oracle Fusion ERP 從 Azure AD 應用程式庫新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Oracle Fusion ERP，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Oracle Fusion ERP**，然後於結果面板中選取 [Oracle Fusion ERP]。

    ![結果清單中的 Oracle Fusion ERP](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>設定將使用者自動佈建至 Oracle Fusion ERP 

本節將引導逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Oracle Fusion ERP 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 建議您選擇為 Oracle Fusion ERP 啟用 SAML 型單一登入，方法是遵循 [Oracle Fusion ERP 單一登入教學課程](oracle-fusion-erp-tutorial.md)中提供的指示。 單一登入和自動使用者佈建可以分開設定，但這兩個功能是互補的。

> [!NOTE]
> 若要深入了解 Oracle Fusion ERP 的 SCIM 端點，請參閱 [Oracle 應用程式雲端中常見功能的 REST API](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)。

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>若要在 Azure AD 中為 Fuze 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Oracle Fusion ERP]。

    ![應用程式清單中的 Oracle Fusion ERP 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/`。 將稍早擷取到的管理員使用者名稱和密碼輸入到 [管理員使用者名稱] 和 [密碼] 欄位。 按一下 Azure AD 與 Oracle Fusion ERP 之間的 [測試連線]。 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="[管理員認證] 區段的螢幕擷取畫面。可以看見 [測試連線] 按鈕以及租用戶 URL、管理員使用者名稱和管理員密碼的欄位。" border="false":::

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]  。

8. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 Oracle Fusion ERP]。

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="[對應] 區段的螢幕擷取畫面。在 [名稱] 底下，可以看見 [將 Azure Active Directory 使用者同步至 Oracle Fusion ERP]。" border="false":::

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Oracle Fusion ERP 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Oracle Fusion ERP 中的使用者帳戶，以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="[屬性對應] 頁面的螢幕擷取畫面。資料表會列出 Azure Active Directory 和 Oracle Fusion ERP 的屬性，以及相符的優先順序。" border="false":::

10. 在 [對應] 區段中，選取 [將 Azure Active Directory 群組同步至 Oracle Fusion ERP]。

    ![Oracle Fusion ERP 群組對應](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Oracle Fusion ERP 的群組屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Oracle Fusion ERP 中的群組，以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Oracle Fusion ERP 群組屬性](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要對 Oracle Fusion ERP 啟用 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Oracle Fusion ERP 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

    此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告；當中會描述 Oracle Fusion ERP 上 Azure AD 佈建服務所執行的所有動作。

    如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Oracle Fusion ERP 只支援其 SCIM 端點的基本驗證。
* Oracle Fusion ERP 不支援群組佈建。
* Oracle Fusion ERP 中的角色會對應到 Azure AD 中的群組。 若要從 Azure AD 將角色指派給 Oracle Fusion ERP 中的使用者，您必須將使用者指派給以 Oracle Fusion ERP 中的角色所命名的所需 Azure AD 群組。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
