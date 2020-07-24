---
title: Azure HPC 快取的 Azure CLI 必要條件
description: 您可以使用 Azure CLI 建立或修改 Azure HPC 快取之前的設定步驟
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097388"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>設定 Azure HPC Cache 的 Azure CLI

請遵循下列步驟來準備您的環境，然後再使用 Azure CLI 來建立或管理 Azure HPC 快取。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>安裝 Azure CLI

Azure HPC 快取需要2.7 版或更新版本的 Azure CLI。 執行 `az --version` 以尋找已安裝的版本和相依程式庫。 若要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>安裝 Azure HPC Cache 擴充功能

Azure HPC 快取函數不是主要程式碼基底的一部分，因此您也必須安裝延伸模組參考。 請遵循下列指示：

1. 登入

   如果您使用的是本機安裝的 CLI 版本，請使用[az login 命令登](/cli/azure/reference-index#az-login)入。

    ```azurecli
    az login
    ```

    請遵循您終端機上顯示的步驟，來完成驗證程序。

   > [!TIP]
   > 如果您有多個訂用帳戶，則需要選擇一個訂用帳戶。 當您在 Azure 入口網站中啟動 Cloud Shell 會話，或遵循[開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in)中的指示，從命令列設定您的訂用帳戶時，請選取它。

2. 安裝 Azure CLI 擴充功能

   Azure HPC 快取函式會以 Azure CLI 擴充功能的形式提供-它還不是核心 CLI 套件的一部分。 您必須先安裝延伸模組參考，才能使用它。

   Azure CLI 擴充功能可讓您存取實驗性和發行前版本命令。 若要深入瞭解延伸模組，包括更新和卸載，請參閱搭配[使用延伸模組與 Azure CLI](/cli/azure/azure-cli-extensions-overview)。

   執行下列命令來安裝 Azure HPC Cache 的延伸模組：

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>設定預設的資源群組（選擇性）

大部分的 hpc 快取命令都需要您傳遞快取的資源群組。 您可以使用[az configure](/cli/azure/reference-index#az-configure)來設定預設的資源群組。

## <a name="next-steps"></a>後續步驟

安裝 Azure CLI 擴充功能並登入之後，您可以使用 Azure CLI 來建立和管理 Azure HPC 快取系統。

* [建立 Azure HPC Cache](hpc-cache-create.md)
* [Azure CLI hpc-快取檔](/cli/azure/ext/hpc-cache/hpc-cache)
