---
title: 教學課程︰以 Azure Active Directory 設定 Bonusly 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 Bonusly。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058819"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>教學課程︰設定 Bonusly 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Bonusly 自動佈建及取消佈建使用者和/或群組時，Bonusly 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

* Azure AD 租用戶
* [Bonusly 租用戶](https://bonus.ly/pricing)
* Bonusly 中具有系統管理員權限的使用者帳戶

> [!NOTE]
> Azure AD 佈建整合需仰賴可供 Bonusly 開發人員使用的 [Bonusly Rest API](https://konghq.com/solutions/gateway/)。

## <a name="adding-bonusly-from-the-gallery"></a>從資源庫新增 Bonusly

將 Bonusly 設定為可使用 Azure AD 自動佈建使用者之前，您必須將 Bonusly 從 Azure AD 應用程式庫新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Bonusly，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]**** 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]****，然後選取 [所有應用程式]**** 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]**** 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Bonusly**，從結果面板中選取 [Bonusly]****，然後按一下 [新增]**** 按鈕以新增應用程式。

    ![結果清單中的 Bonusly](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>將使用者指派給 Bonusly

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。 

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Bonusly。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Bonusly：

* [將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>將使用者指派給 Bonusly 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Bonusly，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Bonusly 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>對 Bonusly 設定自動使用者佈建

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Bonusly 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇為 Bonusly 啟用 SAML 型單一登入，請遵循 [ 單一登入教學課程](bonus-tutorial.md)中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>在 Azure AD 中為 Bonusly 設定自動使用者佈建：

1. 登錄到 Azure[門戶](https://portal.azure.com)並選擇**企業應用程式**，選擇**所有應用程式**，然後**選擇"獎金**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Bonusly]****。

    ![應用程式清單中的 Bonusly 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. 將**預配模式**設置為 **"自動**"。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 [管理員認證]**** 區段下，輸入 Bonusly 帳戶的 [祕密權杖]**** (如步驟 6 所述)。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. Bonusly 帳戶的 [秘密權杖]**** 位於 [管理員] > [公司] > [整合]****。 在 [如果您想要編碼]**** 區段中，按一下 [API] > [建立新的 API 存取權杖]**** 以建立新的祕密權杖。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. 在以下畫面上，在提供的文字方塊中輸入存取權杖的名稱，然後按 [建立 Api 金鑰]****。 新的存取權杖會出現在快顯視窗中幾秒鐘。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/Token02.png)

8. 如步驟 5 所示填寫欄位後，按一下 [測試連線]**** 以確認 Azure AD 可以連線到 Bonusly。 如果連線失敗，請確定您的 Bonusly 帳戶具有系統管理員權限並再試一次。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. 在 [通知電子郵件]**** 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. 按一下 [儲存]****。

11. 在 [對應]**** 區段中，選取 [同步處理 Azure Active Directory 使用者至 Bonusly]****。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. 在 [屬性對應]**** 區段中，檢閱從 Azure AD 同步至 Bonusly 的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Bonusly 中的使用者帳戶以進行更新作業。 選取 [儲存]**** 按鈕以認可所有變更。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

14. 若要啟用 Bonusly 的 Azure AD 佈建服務，在 [設定]**** 區段中，將 [佈建狀態]**** 變更為 [開啟]****。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. 透過在 [設定]**** 區段的 [範圍]**** 中選擇需要的值，可定義要佈建到 Bonusly 的使用者和/或群組。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. 當您準備好要佈建時，按一下 [儲存]****。

    ![Bonusly 佈建](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料]**** 區段來監視進度，並遵循連結來佈建活動報告，當中會描述 Bonusly 上的 Azure AD 佈建服務所執行之所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
