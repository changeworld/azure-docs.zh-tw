---
title: 快速入門：使用 Azure CLI 建立共用查詢
description: 在本快速入門中，您會依照步驟為 Azure CLI 啟用 Resource Graph 延伸模組，並建立共用查詢。
ms.date: 06/29/2020
ms.topic: quickstart
ms.openlocfilehash: c5f7e6fbe1b462c1f9b6e8ad46c598398e1aca02
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050942"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>快速入門：使用 Azure CLI 建立 Resource Graph 共用查詢

搭配 [Azure CLI](/cli/azure/) 使用 Azure Resource Graph 的第一個步驟是確認已安裝延伸模組。 此快速入門逐步引導您完成將延伸模組新增至您的 Azure CLI 安裝的程序。 您可以透過在本機安裝的 Azure CLI，或是透過 [Azure Cloud Shell](https://shell.azure.com) 使用延伸模組。

在此程序結束時，您會將延伸模組新增到您選擇的 Azure CLI 安裝中，並建立 Resource Graph 共用查詢。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>新增 Resource Graph 延伸模組

若要讓 Azure CLI 與 Azure Resource Graph 搭配使用，您必須新增延伸模組。 此延伸模組適用於可使用 Azure CLI 的任何地方，包括 [Windows 10 的 Bash](/windows/wsl/install-win10)、[Cloud Shell](https://shell.azure.com) (獨立與內部入口網站)、[Azure CLI Docker 映像](https://hub.docker.com/r/microsoft/azure-cli/)，或在本機安裝。

1. 確認已安裝最新的 Azure CLI (至少 **2.8.0**)。 如果尚未安裝，請依照[這些指示](/cli/azure/install-azure-cli-windows)操作。

1. 在您選擇的 Azure CLI 環境中，使用 [az extension add](/cli/azure/extension#az-extension-add)，透過下列命令匯入 Resource Graph 延伸模組：

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. 使用 [az extension list](/cli/azure/extension#az-extension-list)，驗證延伸模組已安裝，且為預期的版本 (至少為 **1.1.0**)：

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>建立 Resource Graph 共用查詢

在 Azure CLI 延伸模組已新增至您選擇的環境之後，現在可以試試看 Resource Graph 共用查詢。 共用查詢是 Azure Resource Manager 物件，您可以在 Azure Resource Graph Explorer 中授與權限或執行。 此查詢會摘要列出依_位置_分組的所有資源計數。

1. 使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組，以儲存 Azure Resource Graph 共用查詢。 此資源群組的名稱為 `resource-graph-queries`，位置為 `westus2`。

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. 使用 `graph` 延伸模組和 [az graph shared-query create](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-create) 命令，建立 Azure Resource Graph 共用查詢：

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. 列出新資源群組中的共用查詢。 [az graph shared-query list](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-list) 命令會傳回值的陣列。

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. 若只要取得單一的共用查詢結果，請使用 [az graph shared-query show](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-show) 命令。

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. 使用 [az graph query](/cli/azure/ext/resource-graph/graph#ext-resource-graph-az-graph-query) 命令中的 `{{shared-query-uri}}` 語法，在 Azure CLI 中執行共用查詢。
   首先，從上述 `show` 命令的結果複製 `id` 欄位。 以 `id` 欄位中的值取代範例中的 `shared-query-uri` 文字，但是保留周圍的 `{{` 和 `}}` 字元。

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > `{{shared-query-uri}}` 語法是**預覽**功能。

另一個尋找 Resource Graph 共用查詢的方法是透過 Azure 入口網站。 在入口網站中，使用搜尋列搜尋「Resource Graph 查詢」。 選取共用查詢。 在 [概觀] 頁面上，[查詢] 索引標籤會顯示已儲存的查詢。 [編輯] 按鈕會在 [Resource Graph Explorer](./first-query-portal.md) 中將其開啟。

## <a name="clean-up-resources"></a>清除資源

如果您想要從 Azure CLI 環境中移除 Resource Graph 共用查詢、資源群組和延伸模組，則可以使用下列命令：

- [az graph shared-query delete](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-delete)
- [az group delete](/cli/azure/group#az-group-delete)
- [az extension remove](/cli/azure/extension#az-extension-remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已將 Resource Graph 延伸模組新增至 Azure CLI 環境，並建立共用查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)