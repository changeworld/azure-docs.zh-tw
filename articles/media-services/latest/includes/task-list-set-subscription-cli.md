---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 94df457cc93a059de3f9bb294bd1c568b095781c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94885212"
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
