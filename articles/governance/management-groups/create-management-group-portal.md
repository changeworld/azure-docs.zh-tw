---
title: 透過入口網站建立管理群組。
description: 在本快速入門中，您會使用 Azure 入口網站來建立管理群組，以將您的資源組織成資源階層。
ms.date: 08/31/2020
ms.topic: quickstart
ms.openlocfilehash: c8cb7b8bd3ad33d1f315670bfbb1782972e6e97f
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661747"
---
# <a name="quickstart-create-a-management-group"></a>快速入門：建立管理群組

管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../policy/overview.md)和 [Azure 角色型存取控制](../../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。 如需詳細資訊，請參閱[管理群組的初始設定](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>先決條件：

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- 如果未啟用[階層保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，則租用戶中的任何 Azure AD 使用者都可以建立管理群組，而不需將管理群組寫入權限指派給該使用者。 這個新管理群組會變成根管理群組或[預設管理群組](./how-to/protect-resource-hierarchy.md#setting---default-management-group)的子系，而建立者會獲得「擁有者」角色指派。 管理群組服務允許此功能，因此在根層級不需要角色指派。 沒有任何使用者可以存取建立的根管理群組。 為了避免尋找 Azure AD 全域管理員的困難以開始使用管理群組，我們允許在根層級建立初始管理群組。

### <a name="create-in-portal"></a>在入口網站中建立

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務]**** > [管理 + 治理]****。

1. 選取 [管理群組]****。

1. 選取 [+ 新增管理群組]****。

   :::image type="content" source="./media/main.png" alt-text="[管理群組] 頁面的螢幕擷取畫面，其中顯示子管理群組和訂用帳戶。" border="false":::

1. 維持選取 [新建]****，並填寫 [管理群組識別碼] 欄位。

   - [管理群組識別碼]**** 是用來在此管理群組上提交命令的目錄唯一識別碼。 此識別碼在建立後即無法編輯，因為整個 Azure 系統會使用它來識別此群組。 系統會使用 Azure Active Directory 識別碼自動建立[根管理群組](./overview.md#root-management-group-for-each-directory)。 針對所有其他管理群組，指派唯一的識別碼。
   - [顯示名稱] 欄位是顯示在 Azure 入口網站內的名稱。 在建立管理群組時，不同的顯示名稱是選擇性欄位，並且可以隨時進行變更。

   :::image type="content" source="./media/create_context_menu.png" alt-text="[管理群組] 頁面的螢幕擷取畫面，其中顯示子管理群組和訂用帳戶。":::

1. 選取 [儲存]****。

## <a name="clean-up-resources"></a>清除資源

若要移除已建立的管理群組，請遵循下列步驟：

1. 選取 [所有服務]**** > [管理 + 治理]****。

1. 選取 [管理群組]****。

1. 尋找上方建立的管理群組並加以選取，然後選取名稱旁邊的 [詳細資料]****。
   然後選取 [刪除]**** 並確認提示。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立管理群組來組織您的資源階層。 管理群組可以保留訂用帳戶或其他管理群組。

若要深入了解管理群組，以及如何管理您的資源階層，請繼續：

> [!div class="nextstepaction"]
> 利用管理群組來管理您的資源