---
title: 教學課程：設定 Templafy OpenID Connect 以 Azure Active Directory 的自動使用者布建 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和解除布建到 Templafy OpenID Connect。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: f9e462d66e438e42c40b16ba9b818ddd330e9e5a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98612062"
---
# <a name="tutorial-configure-templafy-openid-connect-for-automatic-user-provisioning"></a>教學課程：設定 Templafy OpenID Connect 來自動布建使用者

本教學課程的目的是要示範在 Templafy OpenID Connect 中執行的步驟，並 Azure Active Directory (Azure AD) ，將 Azure AD 設定為自動布建和解除布建使用者和/或群組至 Templafy OpenID Connect。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [Templafy 租用戶](https://www.templafy.com/pricing/)。
* Templafy 中具有管理員權限的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃您的佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 與 Templafy OpenID Connect 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="assigning-users-to-templafy-openid-connect"></a>將使用者指派給 Templafy OpenID Connect

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 Templafy OpenID Connect。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 Templafy OpenID Connect：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-openid-connect"></a>將使用者指派給 Templafy OpenID Connect 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Templafy OpenID Connect，以測試自動使用者布建設定。 稍後可能會指派更多使用者及/或群組。

* 將使用者指派給 Templafy OpenID Connect 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色 (可用的) 。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="step-2-configure-templafy-openid-connect-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Templafy OpenID Connect 以支援以 Azure AD 布建

將 Templafy OpenID Connect 設定為使用 Azure AD 自動布建使用者之前，您必須在 Templafy OpenID Connect 上啟用 SCIM 布建。

1. 登入您的 Templafy 管理主控台。 按一下 [管理]。

    ![Templafy 管理主控台](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. 按一下 [驗證方法]。

    ![Templafy [管理] 區段的螢幕擷取畫面，其中已指出 [驗證方法] 選項。](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. 複製 **SCIM Api 金鑰** 值。 在 Azure 入口網站的 Templafy OpenID Connect 應用程式的 [布建] 索引標籤中，將會在 [ **秘密權杖** ] 欄位中輸入此值。

    ![SCIM API 金鑰的螢幕擷取畫面。](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-openid-connect-from-the-gallery"></a>步驟 3： 從資源庫新增 Templafy OpenID Connect

若要設定 Templafy OpenID Connect 以 Azure AD 自動布建使用者，您需要從 Azure AD 應用程式資源庫將 OpenID Connect Templafy 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Templafy OpenID Connect，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Templafy OpenID Connect**，在 [結果] 面板中選取 [ **Templafy] OpenID Connect** ，然後按一下 [ **新增** ] 按鈕以新增應用程式。

    ![結果清單中的 Templafy OpenID Connect](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-openid-connect"></a>步驟 4： 設定自動使用者布建至 Templafy OpenID Connect 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Templafy OpenID Connect 中建立、更新及停用使用者和/或群組。

> [!TIP]
> 您也可以選擇啟用 Templafy 的 OpenID connect 型單一登入，請遵循 [Templafy 單一登入教學](templafy-tutorial.md)課程中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-templafy-openid-connect-in-azure-ad"></a>若要在 Azure AD 中設定 Templafy OpenID Connect 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **Templafy OpenID Connect**]。

    ![應用程式清單中的 Templafy OpenID Connect 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://scim.templafy.com/scim`。 輸入稍早在 **秘密權杖** 中取出的 **SCIM API 金鑰** 值。 按一下 [測試連線]，以確保 Azure AD 可以連線至 Templafy。 如果連線失敗，請確定您的 Templafy 帳戶具有管理員權限並再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]  。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者] 以 Templafy OpenID Connect**。

    ![Templafy OpenID Connect 使用者對應](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 Templafy OpenID Connect 的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Templafy OpenID Connect 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Templafy OpenID Connect 使用者屬性](media/templafy-openid-connect-provisioning-tutorial/user-attribute.png)

10. 在 [對應] 區段中，選取 [將 Azure Active Directory 群組同步至 Templafy]。

    ![Templafy OpenID Connect 群組對應](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 Templafy OpenID Connect 的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Templafy OpenID Connect 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Templafy OpenID Connect 群組屬性](media/templafy-openid-connect-provisioning-tutorial/group-attribute.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Templafy OpenID Connect 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建 **狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Templafy OpenID Connect 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

    此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [ **同步處理詳細資料** ] 區段來監視進度，並遵循連結來布建活動報告，該報告描述 Templafy OpenID Connect 上 Azure AD 布建服務所執行的所有動作。

## <a name="step-5-monitor-your-deployment"></a>步驟 5。 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

* 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)來判斷哪些使用者已佈建成功或失敗
* 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)來查看佈建週期的狀態，以及其接近完成的程度
* 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)深入了解隔離狀態。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
