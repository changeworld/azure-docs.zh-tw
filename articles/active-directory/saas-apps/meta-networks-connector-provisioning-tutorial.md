---
title: 教學課程：使用 Azure Active Directory 設定 Meta Networks Connector 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至 Meta Networks Connector。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 17ec8c04e3313155bdd00f119dc2f75882f51f33
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88534989"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的 Meta Networks Connector

本教學課程的目的是要示範在 Meta Networks Connector 中執行的步驟，以及 Azure Active Directory (Azure AD) 將 Azure AD 設定為自動布建和解除布建使用者和/或群組至 Meta Networks Connector。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Meta Networks Connector 租使用者](https://www.metanetworks.com/)
* Meta Networks Connector 中具有系統管理員許可權的使用者帳戶。

## <a name="assigning-users-to-meta-networks-connector"></a>將使用者指派給 Meta Networks Connector

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者布建的內容中，只會同步處理在 Azure AD 中指派給應用程式的使用者和/或群組。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者及/或群組需要存取 Meta Networks Connector。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 Meta Networks Connector：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>將使用者指派給 Meta Networks Connector 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Meta Networks Connector，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Meta Networks Connector 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色 (如果有的話) 。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-meta-networks-connector-for-provisioning"></a>設定用於布建的 Meta Networks Connector

1. 使用您的組織名稱登入 [Meta Networks Connector 管理主控台](https://login.metanetworks.com/login/) 。 流覽至 **管理 > API 金鑰**。

    ![Meta Networks Connector 管理主控台](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  按一下畫面右上方的加號，以建立新的 **API 金鑰**。

    ![Meta Networks Connector 加號圖示](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  設定 **Api 金鑰名稱** 和 **api 金鑰描述**。

    ![Meta Networks Connector Create Token](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  開啟**群組**和**使用者**的**寫入**許可權。

    ![Meta Networks Connector 許可權](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  按一下 [ **新增**]。 複製並儲存 **秘密** ，因為這會是您唯一可以看到的時間。 在 Azure 入口網站的 Meta Networks Connector 應用程式的 [布建] 索引標籤中，將會在 [秘密權杖] 欄位中輸入此值。

    ![Meta Networks Connector Create Token](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  藉由流覽至 [ **管理] > 設定 > IdP > 建立新**的來新增 IdP。

    ![Meta Networks Connector Add IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  在 [ **IdP** 設定] 頁面上，您可以為 IdP 設定 **命名** ，然後選擇 **圖示**。

    ![Meta Networks Connector IdP Name](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Meta Networks Connector IdP 圖示](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  在 [ **設定 SCIM** ] 底下，選取在先前步驟中建立的 API 金鑰名稱。 按一下 [ **儲存**]。

    ![Meta Networks Connector 設定 SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  流覽至 [ **管理] > 設定 > IdP]** 索引標籤。按一下先前步驟中所建立之 IdP 設定的名稱，以查看 **IDP 識別碼**。 當您在 Azure 入口網站的 Meta Networks Connector 應用程式的 [布建] 索引標籤中輸入 [**租使用者 url** ] 欄位中的值時，就會將此**識別碼**新增至**租使用者 url**的結尾。

    ![Meta Networks Connector IdP ID](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>從資源庫新增 Meta Networks Connector

使用 Azure AD 設定 Meta Networks Connector 來自動布建使用者之前，您必須從 Azure AD 應用程式資源庫將 Meta Networks Connector 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Meta Networks Connector，請執行下列步驟：**

1. 在 [ **[Azure 入口網站](https://portal.azure.com)** 的左側導覽面板中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要加入新的應用程式，請選取窗格頂端的 [ **新增應用程式** ] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Meta Networks connector**，在結果窗格中選取 [ **meta networks connector** ]，然後按一下 [ **新增** ] 按鈕以新增應用程式。

    ![結果清單中的 Meta Networks Connector](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>設定自動使用者布建至 Meta Networks Connector 

本節將引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Meta Networks Connector 中建立、更新及停用使用者和/或群組的步驟。

> [!TIP]
> 您也可以選擇啟用 Meta Networks Connector 的 SAML 型單一登入，請遵循「 [Meta Networks Connector 單一登入」教學](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)課程中提供的指示。 您可以獨立設定自動使用者布建的單一登入，雖然這兩個功能彼此補充

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>若要在 Azure AD 中設定 Meta Networks Connector 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Meta Networks Connector]****。

    ![應用程式清單中的 Meta Networks Connector 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![佈建索引標籤](common/provisioning-automatic.png)

5. 在 [ **管理員認證** ] 區段下的 [ `https://api.metanetworks.com/v1/scim/<IdP ID>` **租使用者 URL**] 中輸入。 輸入稍早在**秘密權杖**中取出的**SCIM Authentication 權杖**值。 按一下 [ **測試連接** ]，以確保 Azure AD 可以連線到 Meta Networks Connector。 如果連接失敗，請確定您的 Meta Networks Connector 帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案] 。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Meta Networks] 連接器**。

    ![Meta Networks Connector 使用者對應](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 Meta Networks Connector 的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Meta Networks Connector 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Meta Networks Connector 使用者屬性](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至 Meta Networks] 連接器**。

    ![Meta Networks Connector 群組對應](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 Meta Networks Connector 的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Meta Networks Connector 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Meta Networks Connector 群組屬性](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Meta Networks Connector 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 Meta Networks Connector 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [ **同步處理詳細資料** ] 區段來監視進度，並遵循連結來布建活動報告，該報告描述 Meta Networks Connector 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

