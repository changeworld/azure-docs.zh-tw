---
title: 快速入門：您的第一個 Go 查詢
description: 在本快速入門中，您將遵循步驟以啟用 Go 的 Resource Graph 套件，並執行第一個查詢。
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 4949801b3dc97904680d09e685fd225812a0e14d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920057"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>快速入門：使用 Go 執行您的第一個 Resource Graph 查詢

使用 Azure Resource Graph 的第一個步驟，是確認已安裝適用於 Go 的必要套件。 此快速入門逐步引導您完成將套件新增至 Go 安裝的程序。

在此程序結束時，套件將新增到您的 Go 安裝中，並執行您的第一個 Resource Graph 查詢。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="add-the-resource-graph-package"></a>新增 Resource Graph 套件

若要讓 Go 可查詢 Azure Resource Graph，必須新增套件。 此套件適用於可使用 Go 的任何位置，包括 [bash on Windows 10](/windows/wsl/install-win10) 或安裝在本機上。

1. 確認已安裝最新的 Go (至少 **1.14**)。 如果尚未安裝，請在 [Golang.org](https://golang.org/dl/) 下載。

1. 確認已安裝最新的 Azure CLI (至少 **2.5.1**)。 如果尚未安裝，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

   > [!NOTE]
   > 需要 Azure CLI，才能讓 Go 在下列範例中使用 `auth.NewAuthorizerFromCLI()` 方法。 如需其他選項的相關資訊，請參閱 [Azure SDK for Go - 其他驗證詳細資料](https://github.com/Azure/azure-sdk-for-go#more-authentication-details)。

1. 透過 Azure CLI 進行驗證。

   ```azurecli
   az login
   ```

1. 在您選擇的 Go 環境中，安裝 Azure Resource Graph 所需的套件：

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>執行第一個 Resource Graph 查詢

在 Go 套件新增至您選擇的環境後，現在可以試試看簡單的 Resource Graph 查詢。 查詢會傳回前五個 Azure 資源，以及每個資源的 **名稱** 與 **資源類型**。

1. 建立 Go 應用程式，並將下列來源儲存為 `argQuery.go`：

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. 建置 Go 應用程式：

   ```bash
   go build argQuery.go
   ```

1. 使用編譯的 Go 應用程式執行第一個 Azure Resource Graph 查詢。 將 `<SubID>` 取代為您的訂用帳戶識別碼：

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > 當此查詢範例未提供排序修飾詞，例如 `order by`，多次執行此查詢可能會為每個要求產生不同的資源集。

1. 將第一個參數變更為 `argQuery`，並變更查詢，以 `order by` **名稱** 屬性。 將 `<SubID>` 取代為您的訂用帳戶識別碼：

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > 如同第一個查詢一樣，多次執行此查詢可能會為每個要求產生不同的資源集。 查詢命令的順序很重要。 在此範例中，`order by` 會出現在 `limit` 之後。 此命令順序會先限制查詢結果，然後再加以排序。

1. 將第一個參數變更為 `argQuery`，並變更查詢，以先 `order by` **名稱** 屬性，然後 `limit` 為只顯示前五個結果。 將 `<SubID>` 取代為您的訂用帳戶識別碼：

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

執行最終查詢數次後，假設您的環境中未變更任何內容，傳回的結果將會一致，且依 **名稱** 屬性排序，但仍限制為只顯示前五個結果。

## <a name="clean-up-resources"></a>清除資源

如果您想要從 Go 環境中移除已安裝的套件，則可以使用下列命令：

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已將 Resource Graph 套件新增至 Go 環境，並執行您的第一個查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)