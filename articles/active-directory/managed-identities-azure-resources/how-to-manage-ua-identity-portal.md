---
title: 在 Azure 入口網站 Azure AD 中管理使用者指派的受控識別
description: 如何建立、列出、刪除及指派角色給使用者指派受控識別的逐步指示。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27144dcd4c7d7490aba3626264e1cb4b64fbb162
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019194"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>使用 Azure 入口網站對使用者指派的受控識別建立、列出、刪除或指派角色

適用于 Azure 資源的受控識別會在 Azure Active Directory 中提供具有受控識別的 Azure 服務。 您可以使用此身分識別來向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您會了解如何使用 Azure 入口網站對使用者指派的受控識別建立、列出、刪除或指派角色。

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。 **請務必檢閱[系統指派和使用者指派受控識別之間的差異](overview.md#managed-identity-types)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。

## <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

若要建立使用者指派的受控識別，您的帳戶需要[受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色指派。

1. 使用與 Azure 訂用帳戶相關聯的帳戶登入 [Azure 入口網站](https://portal.azure.com)，以建立使用者指派的受控識別。
2. 在搜尋方塊中，輸入*受控*識別，然後在 [**服務**] 下，按一下 [**受控**識別]。
3. 按一下 [新增]****，並在 [建立使用者指派的受控識別]**** 窗格底下的下列欄位中輸入值：
   - **資源名稱**：這是使用者指派的受控識別名稱，例如 UAI1。
   - **訂用帳戶**：選擇訂用帳戶，以在其下方建立使用者指派的受控識別
   - **資源群組**：建立新資源群組來包含使用者指派的受控識別，或選擇 [使用現有項目]**** 在現有的資源群組中建立使用者指派的受控識別。
   - **位置**：選擇一個位置來部署使用者指派的受控識別，例如「**美國西部**」。
4. 按一下 [建立]。

![建立使用者指派的受控識別](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>列出使用者指派的受控識別

若要列出/讀取使用者指派的受控識別，您的帳戶需要[受控識別操作者](/azure/role-based-access-control/built-in-roles#managed-identity-operator)或[受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色指派。

1. 使用與 Azure 訂用帳戶相關聯的帳戶登入 [Azure 入口網站](https://portal.azure.com)，以列出使用者指派的受控識別。
2. 在 [搜尋] 方塊中，輸入「受控識別」**，然後在 [服務] 下方按一下 [受控識別]****。
3. 此時會針對訂用帳戶傳回使用者指派的受控識別清單。  若要查看使用者指派的受控識別有關的詳細資料，可以按一下其名稱。

![列出使用者指派的受控識別](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>刪除使用者指派的受控識別

若要刪除使用者指派的受控識別，您的帳戶需要[受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色指派。

將使用者指派的識別從受指派的 VM 或任何資源中刪除，並不會將它移除。  若要從虛擬機器移除使用者指派的識別，請參閱[從 VM 移除使用者指派的受控識別](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm)。

1. 使用與 Azure 訂用帳戶相關聯的帳戶登入 [Azure 入口網站](https://portal.azure.com)，以刪除使用者指派的受控識別。
2. 選取的使用者指派的受控識別，然後按一下 [刪除]****。
3. 在確認方塊中選擇 [是]****。

![刪除使用者指派的受控識別](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>對使用者指派的受控識別指派角色 

若要將角色指派給使用者指派的受控識別，您的帳戶需要[使用者存取管理員](/azure/role-based-access-control/built-in-roles#user-access-administrator)角色指派。

1. 使用與 Azure 訂用帳戶相關聯的帳戶登入 [Azure 入口網站](https://portal.azure.com)，以列出使用者指派的受控識別。
2. 在 [搜尋] 方塊中，輸入「受控識別」**，然後在 [服務] 下方按一下 [受控識別]****。
3. 此時會針對訂用帳戶傳回使用者指派的受控識別清單。  選取要指派角色的使用者指派受控識別。
4. 選取 **[存取控制（IAM）**]，然後選取 [**新增角色指派**]。

   ![使用者指派的受控識別啟動](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. 在 [新增角色指派] 分頁中，設定下列值，然後按一下 [**儲存**]：
   - **角色** - 要指派的角色
   - **存取權指派對象** - 要指派使用者指派受控識別的資源
   - **選取** - 要指派存取權的成員
   
   ![使用者指派的受控識別 IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
