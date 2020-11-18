---
title: 教學課程：以 Azure Active Directory 設定 MerchLogix 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 MerchLogix。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 4d0a52f06a751fba57a00615e2d57485ff740d04
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359692"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>教學課程：設定 MerchLogix 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 MerchLogix 自動佈建及取消佈建使用者和/或群組時，MerchLogix 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* MerchLogix 租用戶
* MerchLogix 的技術連絡人，其可以提供要佈建使用者所需的 SCIM 端點 URL 和秘密權杖

## <a name="adding-merchlogix-from-the-gallery"></a>從資源庫新增 Merchlogix

設定 MerchLogix 來搭配 Azure AD 進行自動使用者佈建之前，您必須從 Azure AD 應用程式庫將 MerchLogix 新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 MerchLogix，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式] > [所有應用程式]。

    ![企業應用程式區段][2]

3. 若要新增 MerchLogix，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **MerchLogix**。

5. 在結果窗格中，選取 [MerchLogix]，然後按一下 [新增] 按鈕，將 MerchLogix 新增至您的 SaaS 應用程式清單。

    ![[從資源庫新增] 區段的螢幕擷取畫面，其中已指出 [輸入名稱] 文字方塊。][4]

## <a name="assigning-users-to-merchlogix"></a>將使用者指派給 MerchLogix

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。 

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 MerchLogix。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 MerchLogix：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>將使用者指派給 MerchLogix 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 MerchLogix，以測試您的初始自動使用者佈建的設定。 您可於稍後在測試成功之後指派其他使用者及/或群組。

* 將使用者指派給 MerchLogix 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>設定將使用者自動佈建至 MerchLogix 

本節將引導逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 MerchLogix 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 建議您選擇為 MerchLogix 啟用 SAML 型單一登入，方法是遵循 [MerchLogix 單一登入教學課程](merchlogix-tutorial.md)中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>若要在 Azure AD 中為 MerchLogix 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [Azure Active Directory] > [企業應用程式] > [所有應用程式]。

2. 從您的 SaaS 應用程式清單中選取 [MerchLogix]。

3. 選取 [佈建]  索引標籤。

4. 將 [佈建模式]  設定為 [自動]  。

    ![MerchLogix 的螢幕擷取畫面 - [佈建] 區段，其中已指出 [佈建] 選項、[佈建模式] 已設定為 [自動]，並已指出 [測試連線] 選項。](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. 在 [管理員認證] 區段底下：

    * 在 [租用戶 URL] 欄位中，輸入 MerchLogix 技術連絡人所提供的 SCIM 端點 URL。

    * 在 [秘密權杖] 欄位中，輸入 MerchLogix 技術連絡人所提供的秘密權杖。

6. 如步驟 5 所示填寫欄位後，按一下 [測試連線] 以確認 Azure AD 可以連線到 MerchLogix。 如果連線失敗，請確定您的 MerchLogix 帳戶具有管理員權限，然後再試一次。

7. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

8. 按一下 **[儲存]** 。

9. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 MerchLogix]。

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 MerchLogix 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 MerchLogix 中的使用者帳戶，以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

11. 在 [對應] 區段中，選取 [將 Azure Active Directory 群組同步處理至 MerchLogix]。

12. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 MerchLogix 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 MerchLogix 中的群組以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

13. 若要對 MerchLogix 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

14. 當您準備好要佈建時，按一下 [儲存]  。

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，當中會描述 MerchLogix 上的 Azure AD 佈建服務所執行之所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
