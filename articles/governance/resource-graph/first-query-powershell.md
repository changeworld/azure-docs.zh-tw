---
title: 快速入門：您的第一個 PowerShell 查詢
description: 在本快速入門中，您將依照步驟為 Azure PowerShell 啟用 Resource Graph 模組，並執行第一個查詢。
ms.date: 08/10/2020
ms.topic: quickstart
ms.openlocfilehash: 4cc7eb524060f2a797077f38321ae9ce6c9cf267
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88056392"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 執行您的第一個 Resource Graph 查詢

使用 Azure Resource Graph 的第一個步驟是確認已安裝 Azure PowerShell 的模組。 本快速入門逐步引導您完成將模組新增至您的 Azure PowerShell 安裝的程序。

在此程序結束後，模組即已新增至所選的 Azure PowerShell 安裝中，並執行您的第一個 Resource Graph 查詢。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>新增 Resource Graph 模組

若要啟用 Azure PowerShell 來查詢 Azure Resource Graph，必須新增模組。 此模組適用於在本機安裝的 PowerShell、[Azure Cloud Shell](https://shell.azure.com) 或 [PowerShell Docker 映像](https://hub.docker.com/_/microsoft-powershell)。

### <a name="base-requirements"></a>基底需求

Azure Resource Graph 模組需要下列軟體：

- Azure PowerShell 1.0.0 或更新版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。

- PowerShellGet 2.0.1 或更新版本。 如果未安裝或更新，請依照[這些指示](/powershell/scripting/gallery/installing-psget)操作。

### <a name="install-the-module"></a>安裝模組

適用於 PowerShell 的 Resource Graph 模組為 **Az.ResourceGraph**。

1. 從**系統管理** PowerShell 提示字元中，執行下列命令：

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. 驗證已匯入模組，而且是最新的版本 (0.7.5)：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>執行第一個 Resource Graph 查詢

在 Azure PowerShell 模組已新增至您選擇的環境後，現在可以試試看簡單的 Resource Graph 查詢。 查詢會傳回前五個 Azure 資源，以及每個資源的**名稱**與**資源類型**。

1. 使用 `Search-AzGraph` Cmdlet 執行第一個 Azure Resource Graph 查詢：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > 當此查詢範例未提供排序修飾詞，例如 `order by`，多次執行此查詢可能會為每個要求產生不同的資源集。

1. 將查詢更新為 `order by`**名稱**屬性：

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > 如同第一個查詢一樣，多次執行此查詢可能會為每個要求產生不同的資源集。 查詢命令的順序很重要。 在此範例中，`order by` 會出現在 `limit` 之後。 此命令順序會先限制查詢結果，然後再加以排序。

1. 將查詢更新為第一個 `order by`，**名稱**屬性，然後 `limit` 為前五個結果：

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

執行最終查詢數次後，假設您的環境中未變更任何內容，傳回的結果將會一致，且依**名稱**屬性排序，但仍限制為只顯示前五個結果。

> [!NOTE]
> 如果查詢未從您可存取的訂用帳戶傳回結果，請注意，`Search-AzGraph` Cmdlet 是為預設內容中的訂用帳戶所預設。 若要查看屬於預設內容的訂用帳戶識別碼清單，請執行 `(Get-AzContext).Account.ExtendedProperties.Subscriptions`。如果您想要搜尋您可存取的所有訂用帳戶，您可以藉由執行 `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`，為 `Search-AzGraph` Cmdlet 設定 PSDefaultParameterValues
   
## <a name="clean-up-resources"></a>清除資源

如果您想要從 Azure PowerShell 環境中移除 Resource Graph 模組，則可以使用下列命令：

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> 這不會刪除先前下載的模組檔案。 它只會從正在執行的 PowerShell 工作階段中移除。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已將 Resource Graph 模組新增至 Azure PowerShell 環境，並執行第一個查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)