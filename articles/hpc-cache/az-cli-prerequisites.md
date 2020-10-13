---
title: Azure HPC Cache 的 Azure CLI 必要條件
description: 您可以使用 Azure CLI 來建立或修改 Azure HPC Cache 之前的設定步驟。
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097388"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>設定適用於 Azure HPC Cache 的 Azure CLI

使用 Azure CLI 來建立或管理 Azure HPC Cache 之前，請遵循下列步驟來準備您的環境。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>安裝 Azure CLI

Azure HPC Cache 需要版本2.7 或更新版本的 Azure CLI。 執行 `az --version` 以尋找已安裝的版本和相依程式庫。 若要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>安裝 Azure HPC Cache 擴充功能

Azure HPC Cache 函式不是主要程式碼基底的一部分，因此您也必須安裝延伸模組參考。 請遵循下列指示：

1. 登入

   如果您是使用本機安裝的 CLI 版本，請使用 [az login 命令登](/cli/azure/reference-index#az-login) 入。

    ```azurecli
    az login
    ```

    請遵循您終端機上顯示的步驟，來完成驗證程序。

   > [!TIP]
   > 如果您有多個訂用帳戶，您必須選擇一個訂用帳戶。 當您在 Azure 入口網站中啟動 Cloud Shell 會話時，請選取它，或遵循開始 [使用 Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in) 中的指示，從命令列設定您的訂用帳戶。

2. 安裝 Azure CLI 擴充功能

   Azure HPC Cache 函式會以 Azure CLI 擴充功能的形式提供，但它還不是核心 CLI 套件的一部分。 您必須先安裝延伸模組參考，才能使用它。

   Azure CLI 擴充功能可讓您存取實驗性和預先發行的命令。 若要深入瞭解擴充功能（包括更新和卸載），請參閱搭配 [Azure CLI 使用擴充](/cli/azure/azure-cli-extensions-overview)功能。

   執行下列命令，以安裝 Azure HPC Cache 的擴充功能：

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>將預設資源群組設定 (選擇性) 

大部分的 hpc cache 命令都需要您傳遞快取的資源群組。 您可以使用 [az configure](/cli/azure/reference-index#az-configure)來設定預設的資源群組。

## <a name="next-steps"></a>接下來的步驟

安裝 Azure CLI 擴充功能並登入之後，您可以使用 Azure CLI 來建立和管理 Azure HPC Cache 系統。

* [建立 Azure HPC Cache](hpc-cache-create.md)
* [Azure CLI hpc-快取檔](/cli/azure/ext/hpc-cache/hpc-cache)
