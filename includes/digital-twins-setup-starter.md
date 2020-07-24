---
author: baanders
description: 在 Azure 數位 Twins 安裝程式中包含 starter 資訊的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 3f19674c0e25ebf5cddc2dfb45580a15d04fad46
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096217"
---
>[!NOTE]
>這些作業應由 Azure 訂用帳戶上具有*擁有*者角色的使用者完成。 雖然有些部分可以在沒有此提高許可權的情況下完成，但還是需要擁有者的合作，才能完整設定可用的實例。 如需詳細資訊，請參閱下列[*必要條件：必要許可權*](#prerequisites-permission-requirements)一節。

新的 Azure 數位 Twins 實例的完整設定包含三個部分：
1. **建立實例**
2. **設定您的使用者存取權限**：您的 azure 使用者必須擁有實例的*Azure 數位 Twins 擁有者（預覽）* 角色，才能執行管理活動。 在此步驟中，您將為自己指派此角色（如果您是 Azure 訂用帳戶中的擁有者），或取得訂用帳戶的擁有者，將其指派給您。
3. **設定用戶端應用程式的存取權限**：通常會撰寫用來與您的實例互動的用戶端應用程式。 為了讓該用戶端應用程式存取您的 Azure 數位 Twins，您必須在用戶端應用程式將用來向實例進行驗證的[Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md)中設定*應用程式註冊*。

若要繼續，您將需要 Azure 訂用帳戶。 您可以在[這裡](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免費設定一個。

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>必要條件：許可權需求

若要能夠完成本文中的所有步驟，您必須將其分類為 Azure 訂用帳戶中的擁有者。 

您可以在 Cloud Shell 中執行下列命令，以檢查您的許可權等級：

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

如果您是擁有者， `roleDefinitionName` 則輸出中的值為 [*擁有*者]：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="顯示 az role 指派 list 命令輸出的 Cloud Shell 視窗":::

如果您發現值是「*參與者*」或「*擁有*者」以外的專案，您可以聯繫您的訂用帳戶擁有者，並以下列其中一種方式繼續進行：
* 要求擁有者代表您完成本文中的步驟
* 要求擁有者也將您提升為訂用帳戶的擁有者，以便您擁有繼續執行的許可權。 這適用于您的組織和 it 內的角色。
