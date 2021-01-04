---
title: 教學課程 - 建立存取套件 - Azure AD 權利管理
description: 在 Azure Active Directory 權利管理中建立第一個存取套件的逐步教學課程。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 09/30/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e8d68643b5cc0002467f738dd60343fb2c42dc6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631275"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>教學課程：在 Azure AD 權利管理中建立您的第一個存取套件

對員工所需的各種資源 (例如群組、應用程式和網站) 進行存取管理，對組織而言是很重要的功能。 您想要為員工授與維持其生產力所需的存取權，並且在不再需要時移除其存取權。

在本教學課程中，您將以 IT 系統管理員身分為 Woodgrove Bank 工作。 公司要求您建立行銷活動的資源套件，供內部使用者用來進行自助式要求。 要求不需經過核准，且使用者的存取權會在 30 天後到期。 在本教學課程中，行銷活動資源只是單一群組中的成員資格，但它可能是群組、應用程式或 SharePoint Online 網站的集合。

![顯示案例概觀的圖表。](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 透過群組建立存取套件作為資源
> * 允許您目錄中的使用者要求存取權
> * 示範內部使用者如何要求存取套件

如需部署 Azure Active Directory 權利管理的程序 (包括建立您的第一個存取套件) 的逐步示範，請觀看下列影片：

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

您也可以使用 Microsoft Graph 以程式設計方式建立存取套件。 如需說明如何以程式設計方式建立存取套件的教學課程，請參閱[權利管理 API](/graph/tutorial-access-package-api?view=graph-rest-beta)。

## <a name="prerequisites"></a>Prerequisites

若要使用 Azure AD 權利管理，您必須具有下列其中一個授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 授權

如需詳細資訊，請參閱[授權需求](entitlement-management-overview.md#license-requirements)。

## <a name="step-1-set-up-users-and-group"></a>步驟 1:設定使用者和群組

資源目錄會有一或多個共用的資源。 在此步驟中，您會在 Woodgrove Bank 目錄中建立名為 **行銷資源** 的群組，作為權利管理的目標資源。 您也會設定內部要求者。

**必要角色：** 全域管理員或使用者管理員

![建立 [使用者和群組]](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. 以全域管理員或使用者管理員身分登入 [Azure 入口網站](https://portal.azure.com)。  

1. 在左側導覽列中，按一下 [Azure Active Directory]  。

1. 建立或設定以下兩個使用者。 您可以使用這些名稱或不同的名稱。 **Admin1** 可以是您目前以其身分登入的使用者。

    | 名稱 | 目錄角色 |
    | --- | --- |
    | **Admin1** | 全域管理員<br/>-或-<br/>使用者管理員 |
    | **Requestor1** | User |

1. 建立名為 **行銷資源**、成員資格類型為 **已指派** 的 Azure AD 安全性群組。

    此群組將是權利管理的目標資源。 此群組一開始應不含任何成員。

## <a name="step-2-create-an-access-package"></a>步驟 2:建立存取套件

*存取套件* 是小組或專案所需且受原則管理的資源組合。 存取套件會定義在名為 *目錄* 的容器中。 在此步驟中，您會在 **一般** 目錄中建立 **行銷資源存取套件**。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

![建立存取套件](./media/entitlement-management-access-package-first/elm-access-package.png)

1. 在 Azure 入口網站的左側瀏覽窗格中，按一下 [Azure Active Directory]  。

2. 在左側功能表中，按一下 [身分識別治理] 

3. 在左側功能表中，按一下 [存取套件]  。  如果您看到「拒絕存取」  ，請確定該目錄中有 Azure AD Premium P2 授權。

4. 按一下 [新增存取套件]  。

    ![Azure 入口網站中的權利管理](./media/entitlement-management-shared/access-packages-list.png)

5. 在 [基本]  索引標籤上，輸入名稱為 **行銷活動** 的存取套件和 **活動資源的存取權** 這項描述。

6. 讓 [目錄]  下拉式清單設為 [一般]  。

    ![新增存取套件 - [基本] 索引標籤](./media/entitlement-management-access-package-first/basics.png)

7. 按 [下一步]  以開啟 [資源角色]  索引標籤。

    在此索引標籤上，您可以選取要包含在存取套件中的資源和資源角色。

8. 按一下 [群組與小組]  。

9. 在 [選取群組] 窗格中，尋找並選取您先前建立的 **行銷資源** 群組。

     根據預設，您會看到 [一般] 目錄內部的群組。 當您選取 [一般] 目錄 (您勾選 [查看全部] 和取方塊時會看到) 外部的群組時，該群組將會新增至 [一般] 目錄。

    ![顯示 [新增存取套件 - 資源角色] 索引標籤和 [選取群組] 視的螢幕擷取畫面。](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. 按一下 [選取]  將群組新增至清單。

11. 在 [角色]  下拉式清單中，選取 [成員]  。

    ![新增存取套件 - [資源角色] 索引標籤](./media/entitlement-management-access-package-first/resource-roles.png)

    >[!IMPORTANT]
    >新增至存取套件的可指派角色群組，將會使用 **可指派給角色** 的子類型來表示。 如需可指派給 Azure AD 角色的群組詳細資訊，請參閱 Azure Active Directory 中的[建立可指派角色的群組](../roles/groups-create-eligible.md)。 如果您沒有看到想要新增的可指派角色群組，或無法新增，請確定您擁有必要的 Azure AD 角色和權利管理角色，才能執行此作業。 您可能需要要求具有必要角色的人員將資源新增至您的目錄。 如需詳細資訊，請參閱[將資源新增至目錄的必要角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

    >[!NOTE]
    > 使用[動態群組](../enterprise-users/groups-create-rule.md)時，您不會看到擁有者以外的其他任何可用角色。 這是原廠設定。
    > ![案例概觀](./media/entitlement-management-access-package-first/dynamic-group-warning.png)
    


12. 按 [下一步]  以開啟 [要求]  索引標籤。

    在此索引標籤上，您會建立要求原則。 *原則* 會定義存取套件的存取規則或準則。 您會建立允許資源目錄中的特定使用者要求此存取套件的原則。

13. 在 [可要求存取的使用者]  區段中，按一下 [您目錄中的使用者]  ，然後按一下 [特定使用者和群組]  。

    ![新增存取套件 - [要求] 索引標籤](./media/entitlement-management-access-package-first/requests.png)

14. 按一下 [新增使用者和群組]  。

15. 在 [選取使用者和群組] 窗格中，選取您先前建立的使用者 **Requestor1**。

    ![新增存取套件 - [要求] 索引標籤 - 選取使用者和群組](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. 按一下 [選取]。 

17. 向下捲動至 [核准]  和 [啟用要求]  區段。

18. 保留 [需要核准]  設定為 [否]  。

19. 在 [啟用要求]  按一下 [是]  ，以便在建立此存取套件時立即予以要求。

    ![新增存取套件 - [要求] 索引標籤 - 核准和啟用要求](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. 按 [下一步]  以開啟 [生命週期]  索引標籤。

21. 在 [期限]  區段中，將 [存取套件指派到期]  設為 [天數]  。

22. 將 [指派到期時間]  設定為 **30** 天。

    ![新增存取套件 - [生命週期] 索引標籤](./media/entitlement-management-access-package-first/lifecycle.png)

23. 按 下一步  以開啟 檢閱 + 建立  索引標籤。

    ![新增存取套件 - [檢閱 + 建立] 索引標籤](./media/entitlement-management-access-package-first/review-create.png)

    幾分鐘後，您應該會看到已成功建立存取套件的通知。

24. 在行銷活動存取套件的左側功能表中，按一下 [概觀]  。

25. 複製 **我的存取權入口網站連結**。

    您將在下一個步驟使用此連結。

    ![存取套件概觀 - 我的存取權入口網站的連結](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>步驟 3：要求存取

在此步驟中，您會以 **內部要求者** 的身分執行步驟，並要求存取套件的存取權。 要求者會使用名為「我的存取權入口網站」的網站提交其要求。 「我的存取權入口網站」可讓要求者提交存取套件的要求、查看他們已有存取權的存取套件，以及檢視其要求歷程記錄。

**必要角色：** 內部要求者

1. 登出 Azure 入口網站。

1. 在新的瀏覽器視窗中，瀏覽至您在上一個步驟中複製的「我的存取權入口網站」連結。

1. 以 **Requestor1** 的身分登入「我的存取權入口網站」。

    您應該會看到 **行銷活動** 存取套件。

1. 如有必要，請按一下 [描述]  資料行中的箭頭，以檢視關於存取套件的詳細資料。

    ![我的存取權入口網站 - 存取套件](./media/entitlement-management-shared/my-access-access-packages.png)

1. 按一下核取記號以選取套件。

1. 按一下 [要求存取]  以開啟 [要求存取] 窗格。

    ![我的存取權入口網站 - [要求存取] 按鈕](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. 在 [業務理由]  方塊中，輸入 **我正在處理新行銷活動** 的理由。

    ![我的存取權入口網站 - 要求存取](./media/entitlement-management-shared/my-access-request-access.png)

1. 按一下 [提交]  。

1. 在左側功能表中按一下 [要求歷程記錄]  ，以確認您的要求已提交。

## <a name="step-4-validate-that-access-has-been-assigned"></a>步驟 4：驗證存取已指派

在此步驟中，您將確認已為 **內部要求者** 指派存取套件，且他們此時已是 **行銷活動** 群組的成員。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 登出「我的存取權入口網站」。

1. 以 **Admin1** 的身分登入 [Azure 入口網站](https://portal.azure.com)。

1. 按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [存取套件]  。

1. 找出並按一下 **行銷活動** 存取套件。

1. 在左側功能表中，按一下 [要求]  。

    您應該會看到 Requestor1，以及狀態為「已傳遞」  的初始原則。

1. 按一下要求，以查看要求詳細資料。

    ![存取套件 - 要求詳細資料](./media/entitlement-management-access-package-first/request-details.png)

1. 在左側導覽列中，按一下 [Azure Active Directory]  。

1. 按一下 [群組]  ，然後開啟 [行銷資源]  群組。

1. 按一下 [成員]  。

    您應該會看到 **Requestor1** 列為成員。

    ![行銷資源成員](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>步驟 5：清除資源

在此步驟中，您將移除先前所做的變更，並刪除 **行銷活動** 存取套件。

**必要角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 開啟 **行銷活動** 存取套件。

1. 按一下 [指派]  。

1. 針對 **Requestor1** 按一下省略符號 ( **...** )，然後按一下 [移除存取權]  。 在出現的訊息中，按一下 [確定]  。

    經過一些時間，狀態會從「已傳遞」變更為「已過期」。

1. 按一下 [資源角色]  。

1. 針對 **行銷活動** 按一下省略符號 ( **...** )，然後按一下 [移除資源角色]  。 在出現的訊息中，按一下 [確定]  。

1. 開啟存取套件的清單。

1. 針對 **行銷活動**，按一下省略符號 ( **...** )，然後按一下 [刪除]  。 在出現的訊息中，按一下 [確定]  。

1. 在 Azure Active Directory 中刪除您所建立的任何使用者，例如 **Requestor1** 和 **Admin1**。

1. 刪除 **行銷資源** 群組。

## <a name="next-steps"></a>後續步驟

前往下一篇文章，以了解權利管理的常見案例步驟。
> [!div class="nextstepaction"]
> [常見案例](entitlement-management-scenarios.md)