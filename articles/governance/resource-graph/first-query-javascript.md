---
title: 快速入門：您的第一個 JavaScript 查詢
description: 在本快速入門中，您將遵循步驟以啟用 JavaScript 的 Resource Graph 程式庫，並執行第一個查詢。
ms.date: 06/23/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: cdc0589829f250851212ad990dde99eb6dcc958f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251961"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>快速入門：使用 JavaScript 執行您的第一個 Resource Graph 查詢

此快速入門逐步引導您完成將程式庫新增至您 JavaScript 安裝的程序。 使用 Azure Resource Graph 的第一個步驟，是使用必要的程式庫初始化 JavaScript 應用程式。

在此程序結束時，程式庫將新增到您的 JavaScript 安裝中，並執行您的第一個 Resource Graph 查詢。

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- **Node.js**：需要 [Node.js](https://nodejs.org/) 12 版或更新版本。

## <a name="application-setup"></a>應用程式設定

若要啟用 JavaScript 來查詢 Azure Resource Graph，必須設定環境。 此設定適用於可以使用 JavaScript 的任何位置，包括 [Windows 10 上的 Bash](/windows/wsl/install-win10)。

1. 執行下列命令以設定新的 Node.js 專案。

   ```bash
   npm init -y
   ```

1. 新增 yargs 模組的參考。

   ```bash
   npm install yargs
   ```

1. 新增 Azure Resource Graph 模組的參考。

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. 新增 Azure 驗證程式庫的參考。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > 確認 _package.json_ `@azure/arm-resourcegraph` 的版本是 **2.0.0** 版或更高版本，以及確認 `@azure/ms-rest-nodeauth` 是 **3.0.3** 版或更高版本。

## <a name="query-the-resource-graph"></a>查詢 Resource Graph

1. 建立名為 _index.js_ 的新檔案，並且輸入下列程式碼。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. 在終端機中輸入下列命令：

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   請務必以您的 Azure 訂用帳戶識別碼清單 (以逗號分隔) 取代 `<YOUR_SUBSCRIPTION_ID_LIST>` 預留位置。

   > [!NOTE]
   > 由於此查詢範例並未提供排序修飾詞 (例如 `order by`)，因此執行此查詢多次將可能會對每個要求產生一組不同的資源。

1. 將第一個參數變更為 `index.js`，並變更查詢，以 `order by` **名稱**屬性。 將 `<YOUR_SUBSCRIPTION_ID_LIST>` 取代為您的訂用帳戶識別碼：

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   當指令碼嘗試進行驗證時，終端機中會顯示類似下列訊息的訊息：

   > 若要登入，請使用網頁瀏覽器開啟頁面 https://microsoft.com/devicelogin ，並輸入代碼 FGB56WJUGK 以進行驗證。

   在瀏覽器中驗證之後，指令碼會繼續執行。

   > [!NOTE]
   > 如同第一個查詢一樣，多次執行此查詢可能會為每個要求產生不同的資源集。 查詢命令的順序很重要。 在此範例中，`order by` 會出現在 `limit` 之後。 此命令順序會先限制查詢結果，然後再加以排序。

1. 將第一個參數變更為 `index.js`，並變更查詢，以先 `order by` **名稱**屬性，然後 `limit` 為只顯示前五個結果。 將 `<YOUR_SUBSCRIPTION_ID_LIST>` 取代為您的訂用帳戶識別碼：

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

執行最終查詢數次後，假設您的環境中未變更任何內容，傳回的結果將會一致，且依**名稱**屬性排序，但仍限制為只顯示前五個結果。

## <a name="clean-up-resources"></a>清除資源

如果您想要從應用程式中移除已安裝的程式庫，請執行下列命令。

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已將 Resource Graph 程式庫新增至 JavaScript 環境，並執行您的第一個查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)