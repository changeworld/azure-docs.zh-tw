---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556245"
---
<!-- List and set subscriptions -->

1. 使用 [az account list](/cli/azure/account#az-account-list) 命令取得訂用帳戶的清單：

    ```
    az account list --output table
    ```

2. 使用 `az account set` 搭配訂用帳戶 ID 或您想要切換的名稱。

    ```
    az account set --subscription "My Demos"
    ```
