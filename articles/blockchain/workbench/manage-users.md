---
title: 管理 Azure Blockchain Workbench 中的使用者
description: 如何在 Azure Blockchain Workbench 中管理使用者。
ms.date: 07/15/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: be078e7149ba008ab8b7ad44a016a0d1cf82df0e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263006"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>在 Azure Blockchain Workbench 中管理使用者

Azure Blockchain Workbench 納入了對屬於協會一分子的人員與組織進行使用者管理的功能。

## <a name="prerequisites"></a>Prerequisites

需要進行 Blockchain Workbench 部署。 請參閱 [Azure Blockchain Workbench 部署](deploy.md)，以取得部署的詳細資訊。

## <a name="add-azure-ad-users"></a>新增 Azure AD 使用者

Azure Blockchain Workbench 會針對驗證、存取控制和角色使用 Azure Active Directory (Azure AD)。 Blockchain Workbench Azure AD 租用戶中的使用者可驗證及使用 Blockchain Workbench。 將使用者新增至系統管理員應用程式角色即可處理並執行動作。

Blockchain Workbench 使用者必須存在於 Azure AD 租用戶中，才能將其指派至應用程式和角色。 若要將使用者新增至 Azure AD，請使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在右上角選取帳戶，並切換至與 Blockchain Workbench 相關聯的 Azure AD 租用戶。
1. 選取 [Azure Active Directory] > [使用者]****。 您會在目錄中看到使用者清單。
1. 若要將使用者新增至目錄，請選取 [新增使用者]****。 如果是外部使用者，請選取 [新增來賓使用者]****。
1. 完成新使用者的必要欄位。 選取 [建立]。

如需如何在 Azure AD 中管理使用者的詳細資訊，請瀏覽 [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) 文件。

## <a name="manage-blockchain-workbench-administrators"></a>管理 Blockchain Workbench 系統管理員

將使用者新增至目錄後，下一個步驟是選擇要讓哪些使用者成為 Blockchain Workbench 系統管理員。 **系統管理員**群組中的使用者會與 Blockchain Workbench 中的**系統管理員應用程式角色**相關聯。 系統管理員可以新增或移除使用者、將使用者指派至特定案例，以及建立新的應用程式。

若要將使用者新增到 Azure AD 目錄中的**系統管理員**群組：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在右上角選取帳戶，以確認您位於與 Blockchain Workbench 相關聯的 Azure AD 租用戶內。
1. 選取 **Azure Active Directory > 企業應用程式**。
1. 將 [ **應用程式類型** ] 下拉式篩選器變更為 **所有應用程式** ， **然後選取 [** 套用]。
1. 選取 Azure Blockchain Workbench 的 Azure AD 用戶端應用程式

    ![所有企業應用程式註冊](./media/manage-users/select-blockchain-client-app.png)

1. 選取 [使用者和群組] > [新增使用者]****。
1. 在 [新增指派] 中，選取 [使用者]。 選擇或搜尋要新增為系統管理員的使用者。 完成選擇時按一下 [選取]****。

    ![新增指派](./media/manage-users/add-user-assignment.png)

1. 確認 [角色]**** 設定為 [系統管理員]****
1. 選取 [指派]。 新增的使用者會顯示在清單中，並已指派系統管理員角色。

    ![Blockchain 用戶端應用程式使用者](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>管理 Blockchain Workbench 成員

使用 Blockchain Workbench 應用程式來管理屬於協會一分子的使用者和組織。 您可以對應用程式和角色新增或移除使用者。

1. 在瀏覽器中[開啟 Blockchain Workbench](deploy.md#blockchain-workbench-web-url)，然後以系統管理員身分登入。

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    成員會新增至每個應用程式。 成員可以有一或多個用來起始合約或採取動作的應用程式角色。

1. 若要管理應用程式的成員，請在 [應用程式]**** 窗格中選取某個應用程式圖格。

    與選取的應用程式相關聯的成員數目會反映在成員圖格中。

    ![選取應用程式](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>將成員新增至應用程式

1. 選取成員圖格來顯示目前成員的清單。
1. 選取 [新增成員]。

    ![螢幕擷取畫面顯示 [應用程式成員資格] 視窗，其中醒目提示 [新增成員] 按鈕。](./media/manage-users/application-add-members.png)

1. 搜尋使用者的名稱。  只有存在於 Blockchain Workbench 租用戶的 Azure AD 使用者才會列出。 如果找不到使用者，您需要 [新增 Azure AD 的使用者](#add-azure-ad-users)。

    ![新增成員](./media/manage-users/find-user.png)

1. 從下拉式清單選取某個**角色**。

    ![選取角色成員](./media/manage-users/application-select-role.png)

1. 選取 [新增]  以在應用程式中新增與角色相關聯的成員。

#### <a name="remove-member-from-application"></a>從應用程式中移除成員

1. 選取成員圖格來顯示目前成員的清單。
1. 對於您想要移除的使用者，從角色下拉式清單中選擇 [移除]****。

    ![移除成員](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>變更或新增角色

1. 選取成員圖格來顯示目前成員的清單。
1. 對於您想要變更的使用者，在下拉式清單上按一下並選取新的角色。

    ![變更角色](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>後續步驟

在這篇操作說明文章中，您已了解如何管理 Azure Blockchain Workbench 的使用者。 若要了解如何建立區塊鏈應用程式，請繼續閱讀下一篇操作說明文章。

> [!div class="nextstepaction"]
> [在 Azure Blockchain Workbench 中建立區塊鏈應用程式](create-app.md)
