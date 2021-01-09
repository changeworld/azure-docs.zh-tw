---
title: 教學課程：以 Azure Active Directory 設定 Zscaler One 來自動佈建使用者 | Microsoft Docs
description: 了解如何將 Azure Active Directory 設定為可對 Zscaler One 自動佈建及取消佈建使用者帳戶。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f6725045064b74079e00ca5bbe1d560f3b19f3ff
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937122"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>教學課程：設定 Zscaler One 來自動佈建使用者

本教學課程中的步驟示範如何在 Zscaler One 與 Azure Active Directory (Azure AD) 中，將 Azure AD 設定為對 Zscaler One 自動佈建和取消佈建使用者和群組。

> [!NOTE]
> 本教學課程描述以 Azure AD 使用者佈建服務為基礎的連接器。 如需此服務的用途、運作方式和常見問題集的詳細資訊，請參閱[使用 Azure Active Directory 對 軟體即服務 (SaaS) 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。


## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您有下列項目：

* Azure AD 租用戶。
* Zscaler One 租用戶。
* Zscaler One 中具有管理員權限的使用者帳戶。

> [!NOTE]
> Azure AD 佈建整合需依賴 Zscaler One SCIM API。 針對具有 Enterprise 套件的帳戶，Zscaler One 開發人員可使用此 API。

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Zscaler One

以 Azure AD 設定 Zscaler One 來自動佈建使用者之前，請先從 Azure Marketplace 將 Zscaler One 新增至您的受控 SaaS 應用程式清單。

若要從 Azure Marketplace 新增 Zscaler One，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com)左側的導覽窗格上，選取 [Azure Active Directory]。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Zscaler One**，然後從結果面板中選取 [Zscaler One]。 若要新增應用程式，請選取 [新增]。

    ![結果清單中的 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>將使用者指派給 Zscaler One

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動佈建使用者的情況下，只會同步處理 Azure AD 中已指派給應用程式的使用者或群組。

在設定並啟用自動佈建使用者之前，請決定 Azure AD 中有哪些使用者或群組需要存取 Zscaler One。 若要將這些使用者或群組指派給 Zscaler One，請遵循[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的指示。

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>將使用者指派給 Zscaler One 的重要秘訣

* 建議您將單一 Azure AD 使用者指派至 Zscaler One，以測試自動使用者佈建組態。 您可以稍後再指派其他使用者或群組。

* 將使用者指派至 Zscaler One 時，請在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>設定使用者自動佈建至 Zscaler One

本節將逐步引導您設定 Azure AD 佈建服務。 請利用此服務，以根據 Azure AD 中的使用者或群組指派，在 Zscaler One 中建立、更新和停用使用者或群組。

> [!TIP]
> 您也可以為 Zscaler One 啟用 SAML 型單一登入。 請遵循 [Zscaler One 單一登入教學課程](zscaler-One-tutorial.md)中的指示。 單一登入和自動使用者佈建可以分開設定，但這兩個功能是互補的。

> [!NOTE]
> 在佈建或取消佈建使用者和群組時，建議您定期重新啟動佈建，以確保群組成員資格會正確更新。 重新啟動會強制讓我們的服務重新評估所有群組，並更新成員資格。  

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>在 Azure AD 中為 Zscaler One 設定自動使用者佈建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式] > [所有應用程式] > [Zscaler One]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler One]  。

    ![應用程式清單中的 Zscaler One 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![Zscaler One 佈建](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![Zscaler One 佈建模式](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 在 [管理員認證] 區段底下，使用 Zscaler One 帳戶的設定來填入 [租用戶 URL] 和 [秘密權杖] 方塊，如步驟 6 所述。

6. 若要取得租用戶 URL 和秘密權杖，請在 Zscaler One 入口網站 UI 中，移至 [管理] > [驗證設定]。 在 [驗證類型] 下方，選取 [SAML]。

    ![Zscaler One 驗證設定](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. 選取 [設定 SAML] 來開啟 [設定 SAML] 選項。

    ![Zscaler One 設定 SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. 選取 [啟用 SCIM 型佈建]，以取得 [基底 URL] 和 [持有人權杖] 中的設定。 然後儲存設定。 將 [基底 URL] 設定複製到 Azure 入口網站中的 [租用戶 URL]。 將 [持有人權杖] 設定複製到 Azure 入口網站中的 [秘密權杖]。

7. 填入步驟 5 所示的方塊之後，請選取 [測試連線]，以確定 Azure AD 可以連線至 Zscaler One。 如果連線失敗，請確定您的 Zscaler One 帳戶具有管理員權限並再試一次。

    ![Zscaler One 測試連線](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 在 [通知電子郵件] 方塊中，輸入應收到佈建錯誤通知之個人或群組的電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Zscaler One 通知電子郵件](./media/zscaler-one-provisioning-tutorial/notification.png)

9. 選取 [儲存]。

10. 在 [對應] 區段底下，選取 [將 Azure Active Directory 使用者同步至 Zscaler One]。

    ![Zscaler One 使用者同步](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Zscaler One 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Zscaler One 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取 [儲存]。

    ![Zscaler One 比對使用者屬性](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. 在 [對應] 區段底下，選取 [將 Azure Active Directory 群組同步至 Zscaler One]。

    ![Zscaler One 群組同步](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Zscaler One 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 Zscaler One 中的群組以進行更新作業。 若要儲存任何變更，請選取 [儲存]。

    ![Zscaler One 比對群組屬性](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 若要設定範圍篩選條件，請遵循[範圍篩選條件教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的指示。

15. 若要啟用 Zscaler One 的 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![Zscaler One 佈建狀態](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. 定義您想要佈建到 Zscaler One 的使用者或群組。 在 [設定] 區段的 [範圍] 中，選取您想要的值。

    ![Zscaler One 範圍](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 準備好要佈建時，請選取 [儲存]。

    ![Zscaler One 儲存](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

針對 [設定] 區段的 [範圍] 中定義的所有使用者或群組，此作業會啟動首次同步處理。 首次同步處理所花的時間比後續同步處理更久。 只要 Azure AD 佈建服務在執行中，則後續同步處理大約每 40 分鐘進行一次。 

您可以使用 [同步處理詳細資料] 區段來監視進度，並循著連結取得佈建活動報告。 此報告描述 Azure AD 佈建服務在 Zscaler One 上執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png