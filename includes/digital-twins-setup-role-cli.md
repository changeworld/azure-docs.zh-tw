---
author: baanders
description: 在 Azure 數位 Twins 設定中包含角色需求的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407422"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>必要條件：許可權需求

若要能夠完成本文中的所有步驟，您必須將其分類為 Azure 訂用帳戶中的擁有者。 

您可以在 Cloud Shell 中執行下列命令，以檢查您的許可權等級：

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

如果您是擁有者， `roleDefinitionName` 則輸出中的值為 [*擁有*者]：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="顯示 az role 指派 list 命令輸出的 Cloud Shell 視窗":::

如果您發現值是「*參與者*」或「*擁有*者」以外的專案，您可以透過下列其中一種方式繼續進行：
* 請洽詢您的訂用帳戶擁有者並要求擁有者，以代表您完成本文中的步驟
* 請洽詢訂用帳戶擁有者或訂用帳戶上具有使用者存取管理員角色的人員，並要求他們提高訂用帳戶的擁有者許可權，讓您能夠繼續進行。 這適用于您的組織和 it 內的角色。