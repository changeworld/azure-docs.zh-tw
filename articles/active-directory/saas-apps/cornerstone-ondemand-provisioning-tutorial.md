---
title: 教學課程︰以 Azure Active Directory 設定 Cornerstone OnDemand 來自動佈建使用者 | Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至基石 OnDemand。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77058415"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>教學課程︰設定 Cornerstone OnDemand 來自動佈建使用者

本教學課程示範在基石 OnDemand 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 將使用者或群組自動布建和取消布建至基石 OnDemand。

> [!NOTE]
> 本教學課程說明建立在 Azure AD 使用者布建服務之上的連接器。 如需此服務的用途、運作方式和常見問題的資訊，請參閱[使用 Azure Active Directory 將使用者布建和取消布建至軟體即服務（SaaS）應用程式](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先決條件

本教學課程中所述的案例假設您有：

* Azure AD 租用戶。
* 基石 OnDemand 租使用者。
* 在具有系統管理員許可權的基石 OnDemand 中的使用者帳戶。

> [!NOTE]
> Azure AD 布建整合依賴于[基石 OnDemand web 服務](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)。 此服務可供基石 OnDemand 團隊使用。

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>從 Azure Marketplace 新增基石 OnDemand

在您設定 Azure AD 的自動使用者布建的基石 OnDemand 之前，請從 Marketplace 將基石 OnDemand 新增至受控 SaaS 應用程式清單。

若要從 Marketplace 新增基石 OnDemand，請遵循下列步驟。

1. 在[Azure 入口網站](https://portal.azure.com)的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]****。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 [**基石 ondemand** ]，然後從結果面板中選取 [**基石 ondemand** ]。 若要新增應用程式，請選取 [**新增**]。

    ![結果清單中的 Cornerstone OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>將使用者指派給基石 OnDemand

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有指派給 Azure AD 中應用程式的使用者或群組會進行同步處理。

在您設定並啟用自動使用者布建之前，請先決定 Azure AD 中的哪些使用者或群組需要存取基石 OnDemand。 若要將這些使用者或群組指派給基石 OnDemand，請依照[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的指示進行。

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>將使用者指派給 Cornerstone OnDemand 的重要秘訣

* 建議您將單一 Azure AD 使用者指派給基石 OnDemand，以測試自動使用者布建設定。 您稍後可以指派其他使用者或群組。

* 當您將使用者指派給基石 OnDemand 時，請在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>設定自動使用者布建至基石 OnDemand

本節會引導您完成設定 Azure AD 布建服務的步驟。 您可以使用它，根據 Azure AD 中的使用者或群組指派，在基石 OnDemand 中建立、更新和停用使用者或群組。

若要在 Azure AD 中為基石 OnDemand 設定自動使用者布建，請遵循下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式** > ] [**所有應用程式** > ] [**基石 OnDemand**]

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **Cornerstone OnDemand**。

    ![應用程式清單中的基石 OnDemand 連結](common/all-applications.png)

3. 選取 [**布**建] 索引標籤。

    ![Cornerstone OnDemand 佈建](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 將 [佈建模式]**** 設定為 [自動]****。

    ![基石 OnDemand 布建模式](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 [**管理員認證**] 區段下，輸入您的基石 OnDemand 帳戶的管理員使用者名稱、管理員密碼和網域：

    * 在 [**管理員使用者名稱**] 方塊中，填入您的基石 OnDemand 租使用者之系統管理員帳戶的網域或使用者名稱。 例如，contoso\admin。

    * 在 [**管理員密碼**] 方塊中，填入與管理員使用者名稱相對應的密碼。

    * 在 [**網域**] 方塊中，填入 [基石 OnDemand 租使用者] 的 [WEB 服務 URL]。 例如，服務位於`https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`，而 Contoso 的網域是。 `https://ws-contoso.csod.com/feed30/clientdataservice.asmx` 如需如何取得 web 服務 URL 的詳細資訊，請參閱[此 pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)。

6. 填入步驟5所示的方塊之後，請選取 [**測試**連線] 以確定 Azure AD 可以連接到 [基石 OnDemand]。 如果連線失敗，請確定您的基石 OnDemand 帳戶具有系統管理員許可權，然後再試一次。

    ![基石 OnDemand 測試連接](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 在 [**通知電子郵件**] 方塊中，輸入要接收布建錯誤通知的人員或群組的電子郵件地址。 選取 [**發生失敗時傳送電子郵件通知**] 核取方塊。

    ![基石 OnDemand 通知電子郵件](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. 選取 [儲存]  。

9. 在 [對應]**** 區段中，選取 [同步處理 Azure Active Directory 使用者至 Cornerstone OnDemand]****。

    ![基石 OnDemand 同步處理](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 在 [**屬性**對應] 區段中，檢查從 Azure AD 同步到基石 OnDemand 的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Cornerstone OnDemand 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取 [**儲存**]。

    ![基石 OnDemand 屬性對應](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 若要設定範圍篩選準則，請遵循[範圍篩選教學](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)課程中的指示。

12. 若要啟用基石 OnDemand 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![基石 OnDemand 布建狀態](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 定義您想要布建到「基石 OnDemand」的使用者或群組。 在 [**設定**] 區段的 [**範圍**] 中，選取您想要的值。

    ![基石 OnDemand 範圍](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 當您準備好要布建時，請選取 [**儲存**]。

    ![基石 OnDemand 儲存](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

這項作業會啟動 [**設定**] 區段的 [**範圍**] 中定義的所有使用者或群組的首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行。 只要執行 Azure AD 布建服務，它們大約每40分鐘就會發生一次。 

您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循 [布建活動報告] 的連結。 此報告會描述在基石 OnDemand 上，Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

Cornerstone OnDemand **Position** 屬性預期的值是對應至 Cornerstone OnDemand 入口網站上的角色。 若要取得有效**位置**值的清單，請移至在基石 OnDemand 入口網站中**編輯使用者記錄 > 組織結構 > 位置**。

![基石 OnDemand 布建編輯使用者記錄](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![基石 OnDemand 布建位置](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![基石 OnDemand 布建位置清單](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
