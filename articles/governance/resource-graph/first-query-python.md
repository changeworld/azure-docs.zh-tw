---
title: 快速入門：您的第一個 Python 查詢
description: 在本快速入門中，您將遵循步驟以啟用 Python 的 Resource Graph 程式庫，並執行第一個查詢。
ms.date: 05/27/2020
ms.topic: quickstart
ms.custom: tracking-python
ms.openlocfilehash: 58ba931f5d222df8d863a11a25af6563192ef453
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609942"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>快速入門：使用 Python 執行您的第一個 Resource Graph 查詢

使用 Azure Resource Graph 的第一個步驟，是確認已安裝適用於 Python 的必要程式庫。 此快速入門逐步引導您完成將程式庫新增至您 Python 安裝的程序。

在此程序結束時，程式庫將新增到您的 Python 安裝中，並執行您的第一個 Resource Graph 查詢。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>新增 Resource Graph 程式庫

若要讓 Python 可查詢 Azure Resource Graph，您必須新增程式庫。 此程式庫適用於可使用 Python 的任何位置，包括 [bash on Windows 10](/windows/wsl/install-win10) 或安裝在本機上。

1. 確認已安裝最新的 Python (至少 **3.8**)。 如果尚未安裝，請在 [Python.org](https://www.python.org/downloads/) 下載。

1. 確認已安裝最新的 Azure CLI (至少 **2.5.1**)。 如果尚未安裝，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

   > [!NOTE]
   > Azure CLI 是啟用 Python 以在下列範例中使用 **CLI 型驗證**的必要項目。 如需其他選項的相關資訊，請參閱[使用適用於 Python 的 Azure 管理程式庫進行驗證](/azure/developer/python/azure-sdk-authenticate)。

1. 透過 Azure CLI 進行驗證。

   ```azurecli
   az login
   ```

1. 在您選擇的 Python 環境中，安裝 Azure Resource Graph 所需的程式庫：

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > 如果為所有使用者安裝 Python，則必須從提高許可權的主控台執行這些命令。

1. 驗證是否已安裝程式庫。 `azure-mgmt-resourcegraph` 應為 **2.0.0** 或更高版本，`azure-mgmt-resource` 應為 **9.0.0** 或更高版本，`azure-cli-core` 應為 **2.5.0** 或更高版本。

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>執行第一個 Resource Graph 查詢

在 Python 程式庫新增至您選擇的環境後，現在可以試試看簡單的 Resource Graph 查詢。 查詢會傳回前五個 Azure 資源，以及每個資源的**名稱**與**資源類型**。

1. 使用已安裝的程式庫和 `resources` 方法，執行您的第一個 Azure Resource Graph 查詢：

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show JSON results
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > 當此查詢範例未提供排序修飾詞，例如 `order by`，多次執行此查詢可能會為每個要求產生不同的資源集。

1. 更新 `getresources` 的呼叫，並將查詢變更為 `order by` **名稱**屬性：

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > 如同第一個查詢一樣，多次執行此查詢可能會為每個要求產生不同的資源集。 查詢命令的順序很重要。 在此範例中，`order by` 會出現在 `limit` 之後。 此命令順序會先限制查詢結果，然後再加以排序。

1. 更新 `getresources` 的呼叫，並變更查詢，以先 `order by` **名稱**屬性，然後 `limit` 為只顯示前五個結果：

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

執行最終查詢數次後，假設您的環境中未變更任何內容，傳回的結果將會一致，且依**名稱**屬性排序，但仍限制為只顯示前五個結果。

## <a name="clean-up-resources"></a>清除資源

如果您想要從 Python 環境中移除已安裝的程式庫，則可以使用下列命令：

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已將 Resource Graph 程式庫新增至 Python 環境，並執行您的第一個查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)
