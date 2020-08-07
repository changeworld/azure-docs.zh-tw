---
author: baanders
description: 在 Azure 數位 Twins 設定中包含角色需求的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832313"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>必要條件：許可權需求

若要能夠完成本文中的所有步驟，您必須將其分類為 Azure 訂用帳戶中的擁有者。 

您可以在 Cloud Shell 中執行下列命令，以檢查您的許可權等級：

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> 如果此命令傳回錯誤，指出 CLI**在圖形資料庫中找不到使用者或服務主體**：
>
> 在本文的其餘部分，請使用您的使用者*物件識別碼*，而不是您的電子郵件。 個人[Microsoft 帳戶 (msa) ](https://account.microsoft.com/account)的使用者可能會發生這種情況。 
>
> 使用[Azure Active Directory 使用者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)的 [Azure 入口網站] 頁面選取您的使用者帳戶，並開啟其詳細資料。 複製使用者的*ObjectID*：
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="[物件識別碼] 欄位中顯示 GUID 的 Azure 入口網站中的使用者網頁檢視" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> 然後，使用您的使用者*物件識別碼*來取代您的電子郵件，以重複 [角色指派清單] 命令。

執行 [角色指派清單] 命令之後，如果您是擁有者，則 `roleDefinitionName` 輸出中的值為 [*擁有*者]：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="顯示 az role 指派 list 命令輸出的 Cloud Shell 視窗":::

如果您發現值是「*參與者*」或「*擁有*者」以外的專案，您可以透過下列其中一種方式繼續進行：
* 請洽詢您的訂用帳戶擁有者並要求擁有者，以代表您完成本文中的步驟
* 請洽詢訂用帳戶擁有者或訂用帳戶上具有使用者存取管理員角色的人員，並要求他們提高訂用帳戶的擁有者許可權，讓您能夠繼續進行。 這適用于您的組織和 it 內的角色。