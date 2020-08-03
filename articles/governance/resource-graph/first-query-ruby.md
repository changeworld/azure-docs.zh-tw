---
title: 快速入門：您的第一個 Ruby 查詢
description: 在此快速入門中，您將遵循步驟以啟用 Ruby 的 Resource Graph gem，並執行第一個查詢。
ms.date: 07/12/2020
ms.topic: quickstart
ms.openlocfilehash: 9763e2e9ec49d6fb4ea37fac12578ab23b7b5363
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100748"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>快速入門：使用 Ruby 執行您的第一個 Resource Graph 查詢

使用 Azure Resource Graph 的第一個步驟，是確認已安裝適用於 Ruby 的必要 gem。 此快速入門逐步引導您完成將 gem 新增至 Ruby 安裝的程序。

在此程序結束時，gem 將新增到您的 Ruby 安裝中，並執行您的第一個 Resource Graph 查詢。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- Azure 服務主體，包括 _clientId_ 和 _clientSecret_。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>建立 Resource Graph 專案

若要讓 Ruby 查詢 Azure Resource Graph，必須將 gem 新增至 `Gemfile`。 此 gem 適用於可以使用 Ruby 的任何地方，包括 [Azure Cloud Shell](https://shell.azure.com)、[Bash on Windows 10](/windows/wsl/install-win10)，或安裝在本機。

1. 確認已安裝最新的 Ruby (至少 **2.7.1**)。 如果尚未安裝，請在 [Ruby-Lang.org](https://www.ruby-lang.org/en/downloads/) 下載。

1. 在您選擇的 Ruby 環境中，初始化新專案資料夾中的套件組合：

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. 將您的 `Gemfile` 更新為 Azure Resource Graph 所需的 gem。 更新的檔案應該類似此範例：

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. 從專案資料夾，執行 `bundle install`。 使用 `bundle list` 確認已安裝 gem。

1. 在相同的專案資料夾中，使用下列程式碼建立 `argQuery.rb`，並儲存更新的檔案：

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>執行第一個 Resource Graph 查詢

儲存 Ruby 指令碼並準備好使用之後，就可以開始試用簡單的 Resource Graph 查詢。 查詢會傳回前五個 Azure 資源，以及每個資源的**名稱**與**資源類型**。

在 `argQuery` 的每個呼叫中都有一些變數，需要您以自己的值取代這些變數：

- `{tenantId}` - 以您的租用戶識別碼取代
- `{clientId}` - 以您服務主體的用戶端識別碼取代
- `{clientSecret}` - 以您服務主體的用戶端密碼取代
- `{subscriptionId}` - 以您的訂用帳戶識別碼取代

1. 將目錄變更為您建立 `Gemfile` 和 `argClient.rb` 檔案所在的專案資料夾。

1. 使用 gem 和 `resources` 方法，執行第一個 Azure Resource Graph 查詢：

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > 當此查詢範例未提供排序修飾詞，例如 `order by`，多次執行此查詢可能會為每個要求產生不同的資源集。

1. 將最後一個參數變更為 `argQuery.rb`，並變更查詢，以 `order by` **名稱**屬性：

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > 如同第一個查詢一樣，多次執行此查詢可能會為每個要求產生不同的資源集。 查詢命令的順序很重要。 在此範例中，`order by` 會出現在 `limit` 之後。 此命令順序會先限制查詢結果，然後再加以排序。

1. 將最後一個參數變更為 `argQuery.rb`，並變更查詢，以先 `order by` **名稱**屬性，然後 `limit` 為只顯示前五個結果：

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

執行最終查詢數次後，假設您的環境中未變更任何內容，傳回的結果將會一致，且依**名稱**屬性排序，但仍限制為只顯示前五個結果。

## <a name="clean-up-resources"></a>清除資源

如果您想要從 Ruby 環境中移除已安裝的 gem，則可以使用下列命令：

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> gem `azure_mgmt_resourcegraph` 具有相依性，例如可能也已根據您的環境安裝的 `ms_rest` 和 `ms_rest_azure`。 若不再需要，您也可以解除安裝這些 gem。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已經將 Resource Graph gem 新增至 Ruby 環境，並執行您的第一個查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)
