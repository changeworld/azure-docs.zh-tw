---
title: 教學課程：以 Azure Active Directory 設定 Cornerstone OnDemand 來自動佈建使用者 | Microsoft Docs
description: 了解如何將 Azure Active Directory 設定為對 Cornerstone OnDemand 自動佈建和取消佈建使用者帳戶。
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
ms.openlocfilehash: b6aaed8d56bb254e5a6385c770f97b5238a5747b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647431"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>教學課程：設定 Cornerstone OnDemand 來自動佈建使用者

本教學課程中的步驟示範如何在 Cornerstone OnDemand 與 Azure Active Directory (Azure AD) 中，將 Azure AD 設定為對 Cornerstone OnDemand 自動佈建和取消佈建使用者或群組。

> [!NOTE]
> 本教學課程描述以 Azure AD 使用者佈建服務為基礎的連接器。 如需此服務的用途、運作方式和常見問題集的詳細資訊，請參閱[使用 Azure Active Directory 對 軟體即服務 (SaaS) 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您有下列項目：

* Azure AD 租用戶。
* Cornerstone OnDemand 租用戶。
* Cornerstone OnDemand 中具有管理員權限的使用者帳戶。

> [!NOTE]
> Azure AD 佈建整合依賴 [Cornerstone OnDemand Web 服務](https://www.cornerstoneondemand.com/)。 此服務可供 Cornerstone OnDemand 團隊使用。

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Cornerstone OnDemand

將 Cornerstone OnDemand 設定為由 Azure AD 自動佈建使用者之前，請從 Marketplace 將 Cornerstone OnDemand 新增至您的受控 SaaS 應用程式清單。

若要從 Marketplace 新增 Cornerstone OnDemand，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com)左側的導覽窗格上，選取 [Azure Active Directory]。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入 **Cornerstone OnDemand**，然後從結果面板中選取 [Cornerstone OnDemand]。 若要新增應用程式，請選取 [新增]。

    ![結果清單中的 Cornerstone OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>將使用者指派給 Cornerstone OnDemand

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動佈建使用者的情況下，只會同步處理 Azure AD 中已指派給應用程式的使用者或群組。

在設定並啟用自動佈建使用者之前，請決定 Azure AD 中有哪些使用者或群組需要存取 Cornerstone OnDemand。 若要將這些使用者或群組指派給 Cornerstone OnDemand，請遵循[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的指示。

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>將使用者指派給 Cornerstone OnDemand 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Cornerstone OnDemand，以測試自動佈建使用者的設定。 您可以稍後再指派其他使用者或群組。

* 將使用者指派給 Cornerstone OnDemand 時，請在指派對話方塊中選取任何有效的應用程式特定角色 (如果有的話)。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>設定自動將使用者佈建至 Cornerstone OnDemand

本節將逐步引導您設定 Azure AD 佈建服務。 請利用此服務，以根據 Azure AD 中的使用者或群組指派，在 Cornerstone OnDemand 中建立、更新和停用使用者或群組。

若要在 Azure AD 中為 Cornerstone OnDemand 設定自動佈建使用者，請遵循下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式] > [所有應用程式] > [Cornerstone OnDemand]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **Cornerstone OnDemand**。

    ![應用程式清單中的 Cornerstone OnDemand 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![Cornerstone OnDemand 佈建](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Cornerstone OnDemand 佈建模式](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 [管理員認證] 區段下，輸入 Cornerstone OnDemand 帳戶的管理員使用者名稱、管理員密碼和網域：

    * 在 [管理員使用者名稱] 欄位中，填入 Cornerstone OnDemand 租用戶上管理帳戶的網域或使用者名稱。 例如，contoso\admin。

    * 在 [管理員密碼] 欄位中，填入對應於管理使用者名稱的密碼。

    * 在 [網域] 欄位中，填入 Cornerstone OnDemand 租用戶的 Web 服務 URL。 例如，此服務位於 `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`，對於 Contoso 來說，網域為 `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`。 如需如何擷取 Web 服務 URL 的詳細資訊，請參閱[此 pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)。

6. 填入步驟 5 所示的方塊之後，請選取 [測試連線]，以確定 Azure AD 可以連線至 Cornerstone OnDemand。 如果連線失敗，請確定您的 Cornerstone OnDemand 帳戶具有管理員權限，然後再試一次。

    ![Cornerstone OnDemand 測試連線](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 在 [通知電子郵件] 方塊中，輸入應收到佈建錯誤通知之個人或群組的電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Cornerstone OnDemand 通知電子郵件](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. 選取 [儲存]。

9. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Cornerstone OnDemand]。

    ![Cornerstone OnDemand 同步處理](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Cornerstone OnDemand 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Cornerstone OnDemand 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取 [儲存]。

    ![Cornerstone OnDemand 屬性對應](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 若要設定範圍篩選條件，請遵循[範圍篩選條件教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的指示。

12. 若要為 Cornerstone OnDemand 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![Cornerstone OnDemand 佈建狀態](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 定義您想要佈建到 Cornerstone OnDemand 的使用者或群組。 在 [設定] 區段的 [範圍] 中，選取您想要的值。

    ![Cornerstone OnDemand 範圍](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 準備好要佈建時，請選取 [儲存]。

    ![Cornerstone OnDemand 儲存](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

針對 [設定] 區段的 [範圍] 中定義的所有使用者或群組，此作業會啟動首次同步處理。 首次同步處理所花的時間比後續同步處理更久。 只要 Azure AD 佈建服務在執行中，則後續同步處理大約每 40 分鐘進行一次。 

您可以使用 [同步處理詳細資料] 區段來監視進度，並循著連結取得佈建活動報告。 此報告描述 Azure AD 佈建服務在 Cornerstone OnDemand 上執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

Cornerstone OnDemand **Position** 屬性預期的值是對應至 Cornerstone OnDemand 入口網站上的角色。 若要取得有效 [位置] 值的清單，請移至 Cornerstone OnDemand 入口網站中的 [編輯使用者記錄 > 組織結構 > 位置]。

![Cornerstone OnDemand 佈建編輯使用者記錄](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Cornerstone OnDemand 佈建位置](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand 佈建位置清單](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
