---
title: 教學課程：以 Azure Active Directory 設定 Meta Networks Connector 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建並取消佈建使用者帳戶至 Meta Networks Connector。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: ac41fb5ed6fd1e46719fcc39ccaf5b29375e7410
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359896"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>教學課程：設定 Meta Networks Connector 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Meta Networks Connector 自動佈建及取消佈建使用者和/或群組時，應在 Meta Networks Connector 與 Azure Active Directory (Azure AD) 中執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Meta Networks Connector 租用戶](https://www.metanetworks.com/)
* Meta Networks Connector 中具有管理員權限的使用者帳戶。

## <a name="assigning-users-to-meta-networks-connector"></a>將使用者指派給 Meta Networks Connector

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Meta Networks Connector。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Meta Networks Connector：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>將使用者指派給 Meta Networks Connector 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Meta Networks Connector，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Meta Networks Connector 時，您必須在 [指派] 對話方塊中選取任何有效應用程式特有的角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="setup-meta-networks-connector-for-provisioning"></a>設定 Meta Networks Connector 以進行佈建

1. 使用您的組織名稱登入 [Meta Networks Connector 管理主控台](https://login.metanetworks.com/login/)。 瀏覽至 [管理] > [API 金鑰]。

    ![Meta Networks Connector 管理主控台](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  按一下畫面右上方的加號，以建立新的 [API 金鑰]。

    ![Meta Networks Connector 加號圖示](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  設定 [API 金鑰名稱] 和 [API 金鑰描述]。

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/keyname.png" alt-text="螢幕擷取畫面：Meta Networks Connector 管理主控台，已醒目提示 Azure AD 和 API 金鑰的 API 金鑰名稱和 API 金鑰描述值。" border="false":::

4.  針對 [群組] 和 [使用者] 開啟 [寫入] 權限。

    ![Meta Networks Connector 權限](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  按一下 [新增]。 複製 [秘密] 並加以儲存，因為這是您唯一可以看到此祕密的時候。 此值會輸入到 Azure 入口網站中 Meta Networks Connector 應用程式 [佈建] 索引標籤中的 [祕密權杖] 欄位。

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/token.png" alt-text="螢幕擷取畫面：告知使用者已新增 API 金鑰的視窗。[秘密] 方塊包含無法解讀的值，並已醒目提示。" border="false":::

6.  藉由瀏覽至 [管理] > [設定] > [IdP] > [新建] 來新增 IdP。

    ![Meta Networks Connector 新增 IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  在 [IdP 設定] 頁面上，您可以為 IdP 設定提供 [名稱]，然後選擇 [圖示]。

    ![Meta Networks Connector IdP 名稱](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Meta Networks Connector IdP 圖示](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  在 [設定 SCIM] 下，選取在先前步驟中建立的 API 金鑰名稱。 按一下 [ **儲存**]。

    ![Meta Networks Connector 設定 SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  瀏覽至 [管理] > [設定] > [IdP] 索引標籤。按一下先前步驟所建立 IdP 設定的名稱，以檢視 [IdP 識別碼]。 在 Azure 入口網站中於 Meta Networks Connector 應用程式 [佈建] 索引標籤內的 [租用戶 URL] 欄位中輸入值的同時，此 [識別碼] 會新增至 [租用戶 URL] 的結尾。

    ![Meta Networks Connector IdP 識別碼](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>從資源庫新增 Meta Networks Connector

將 Meta Networks Connector 設定為可使用 Azure AD 自動佈建使用者之前，您必須從 Azure AD 應用程式庫將 Meta Networks Connector 新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Meta Networks Connector，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Meta Networks Connector**，在結果面板中選取 [Meta Networks Connector]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Meta Networks Connector](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>設定將使用者自動佈建至 Meta Networks Connector 

本節將引導逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Meta Networks Connector 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 建議您選擇為 Meta Networks Connector 啟用 SAML 型單一登入，方法是遵循 [Meta Networks Connector 單一登入教學課程](./metanetworksconnector-tutorial.md)中提供的指示。 單一登入和自動使用者佈建可以分開設定，但這兩個功能是互補的

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>若要在 Azure AD 中為 Meta Networks Connector 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Meta Networks Connector]  。

    ![應用程式清單中的 Meta Networks Connector 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://api.metanetworks.com/v1/scim/<IdP ID>`。 輸入稍早在 [秘密權杖] 中擷取到的 [SCIM 權杖驗證] 值。 按一下 [測試連線]，以確保 Azure AD 可以連線至 Meta Networks Connector。 如果連線失敗，請確定您的 Meta Networks Connector 帳戶具有管理員權限，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]  。

8. 在 [對應] 區段下，選取 [將 Azure Active Directory 使用者同步至 Meta Networks Connector]。

    ![Meta Networks Connector 使用者對應](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Meta Networks Connector 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Meta Networks Connector 中的使用者帳戶，以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Meta Networks Connector 使用者屬性](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. 在 [對應] 區段下，選取 [將 Azure Active Directory 群組同步至 Meta Networks Connector]。

    ![Meta Networks Connector 群組對應](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Meta Networks Connector 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 Meta Networks Connector 中的群組，以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Meta Networks Connector 群組屬性](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要對 Meta Networks Connector 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Meta Networks Connector 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告；當中會描述 Meta Networks Connector 上 Azure AD 佈建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)