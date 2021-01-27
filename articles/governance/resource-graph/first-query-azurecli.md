---
title: 快速入門：您的第一個 Azure CLI 查詢
description: 在本快速入門中，您將依照步驟為 Azure CLI 啟用 Resource Graph 延伸模組，並執行第一個查詢。
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5111f59eb760afda4e206837ca5bdf8bcc201338
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917819"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>快速入門：使用 Azure CLI 執行您的第一個 Resource Graph 查詢

使用 Azure Resource Graph 的第一個步驟，是確認已安裝適用於 [Azure CLI](/cli/azure/) 的延伸模組。 此快速入門逐步引導您完成將延伸模組新增至您的 Azure CLI 安裝的程序。 您可以透過在本機安裝的 Azure CLI，或是透過 [Azure Cloud Shell](https://shell.azure.com) 使用延伸模組。

在此程序結束時，延伸模組將新增到您選擇的 Azure CLI 安裝中，並執行您的第一個 Resource Graph 查詢。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>新增 Resource Graph 延伸模組

若要讓 Azure CLI 可查詢 Azure Resource Graph，您必須新增延伸模組。 此延伸模組適用於可使用 Azure CLI 的任何地方，包括 [Windows 10 的 Bash](/windows/wsl/install-win10)、[Cloud Shell](https://shell.azure.com) (獨立與內部入口網站)、[Azure CLI Docker 映像](https://hub.docker.com/_/microsoft-azure-cli)，或在本機安裝。

1. 確認已安裝最新的 Azure CLI (至少 **2.0.76**)。 如果尚未安裝，請依照[這些指示](/cli/azure/install-azure-cli-windows)操作。

1. 在您選擇的 Azure CLI 環境中，使用下列命令匯入：

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. 驗證延伸模組已安裝，且為預期的版本 (至少為 **1.0.0**)：

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>執行第一個 Resource Graph 查詢

在 Azure CLI 延伸模組已新增至您選擇的環境後，現在可以試試看簡單的 Resource Graph 查詢。 查詢會傳回前五個 Azure 資源支每個資源的 **名稱** 與 **資源類型**。

1. 使用 `graph` 延伸模組與 `query` 命令，執行您的第一個 Azure Resource Graph 查詢：

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > 當此查詢範例未提供排序修飾詞，例如 `order by`，多次執行此查詢可能會為每個要求產生不同的資源集。

1. 將查詢更新為 `order by`**名稱** 屬性：

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > 如同第一個查詢一樣，多次執行此查詢可能會為每個要求產生不同的資源集。 查詢命令的順序很重要。 在此範例中，`order by` 會出現在 `limit` 之後。 此命令順序會先限制查詢結果，然後再加以排序。

1. 將查詢更新為第一個 `order by`，**名稱** 屬性，然後 `limit` 為前五個結果：

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

執行最終查詢數次後，假設您的環境中未變更任何內容，傳回的結果將會一致，且依 **名稱** 屬性排序，但仍限制為只顯示前五個結果。

## <a name="clean-up-resources"></a>清除資源

如果您想要從 Azure CLI 環境中移除 Resource Graph 延伸模組，則可以使用下列命令：

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已將 Resource Graph 延伸模組新增至 Azure CLI 環境，並執行您的第一個查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)
