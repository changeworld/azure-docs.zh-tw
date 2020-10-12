---
title: Azure Cosmos DB 中的跨原始來源資源共用 (CORS)
description: 本文說明如何在 Azure Cosmos DB 中，使用 Azure 入口網站和 Azure Resource Manager 範本來設定跨原始來源資源共用 (CORS)。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: eb1cbed7b974b6f0015591df01674e40aac2d8c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85390868"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>設定跨原始來源資源共用 (CORS)

跨原始來源資源共用 (CORS) 是一項 HTTP 功能，可讓在某個網域下執行的 Web 應用程式存取其他網域中的資源。 網頁瀏覽器會實作稱為相同原始原則的安全性限制，它可防止網頁呼叫不同網域中的 API。 不過，CORS 提供了安全的方式來允許來源網域呼叫其他網域中的 API。 Azure Cosmos DB 中的 Core (SQL) API 現在支援使用 "allowedOrigins" 標頭 (CORS) 的跨原始來源資源分享。 在為 Azure Cosmos 帳戶啟用 CORS 支援後，就只會對已通過驗證的要求進行評估，以根據您指定的規則來判斷是否允許這些要求。

您可以從 Azure 入口網站或 Azure Resource Manager 範本設定跨原始來源資源共用 (CORS) 設定。 針對使用 Core (SQL) API 的 Cosmos 帳戶，Azure Cosmos DB 支援可在 Node.js 和瀏覽器型環境中運作的 JavaScript 程式庫。 使用閘道模式時，此程式庫現在可利用 CORS 支援。 不需要設定用戶端就能使用此功能。 使用 CORS 支援後，來自瀏覽器的資源就可以透過 [JavaScript 程式庫](https://www.npmjs.com/package/@azure/cosmos)或直接從 [REST API](/rest/api/cosmos-db/) 直接存取 Azure Cosmos DB 以進行簡單的作業。

> [!NOTE]
> CORS 支援僅適用于 Azure Cosmos DB Core (SQL) API，並受到支援。 它不適用於適用于 Cassandra、Gremlin 或 MongoDB 的 Azure Cosmos DB Api，因為這些通訊協定不會使用 HTTP 進行用戶端-伺服器通訊。

## <a name="enable-cors-support-from-azure-portal"></a>從 Azure 入口網站啟用 CORS 支援

使用下列步驟，透過 Azure 入口網站來啟用跨原始來源資源共用：

1. 瀏覽至 Azure cosmos DB 帳戶。 開啟 [CORS]**** 刀鋒視窗。

2. 指定可對您 Azure Cosmos DB 帳戶發出跨原始來源呼叫的原始來源逗號分隔清單。 例如，`https://www.mydomain.com`、`https://mydomain.com`、`https://api.mydomain.com`。 您也可以使用萬用字元 “\*” 來允許所有原始來源，然後選取 [提交]****。 

   > [!NOTE]
   > 目前，您不能在網域名稱中使用萬用字元。 例如，尚未支援 `https://*.mydomain.net` 格式。 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="使用 Azure 入口網站啟用跨原始來源資源共用":::

## <a name="enable-cors-support-from-resource-manager-template"></a>從 Resource Manager 範本啟用 CORS 支援

若要使用 Resource Manager 範本來啟用 CORS，請在任何現有範本中新增 “cors” 區段與 “allowedOrigins” 屬性。 下列 JSON 是某個範本的範例，其會建立新的 Azure Cosmos 帳戶並啟用 CORS。

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>從瀏覽器使用 Azure Cosmos DB JavaScript 程式庫

Azure Cosmos DB JavaScript 程式庫目前只在其套件內隨附 CommonJS 版本的程式庫。 若要從瀏覽器使用此程式庫，您必須使用 Rollup 或 Webpack 等工具來建立瀏覽器相容程式庫。 某些 Node.js 程式庫應該會有對其適用的瀏覽器模擬。 以下是具有所需模擬設定的 Webpack 組態檔範例。

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
以下[程式碼範例](https://github.com/christopheranderson/cosmos-browser-sample)搭配使用 TypeScript 和 Webpack 與 Azure Cosmos DB JavaScript SDK 程式庫，來建置會在有新項目建立時傳送即時更新的待辦事項應用程式。
最佳做法是不要使用主索引鍵從瀏覽器中與 Azure Cosmos DB 通訊。 相反地，請使用資源權杖來進行通訊。 如需資源權杖的詳細資訊，請參閱[保護對 Azure Cosmos DB 的存取](secure-access-to-data.md#resource-tokens)一文。

## <a name="next-steps"></a>後續步驟

若要了解其他可保護 Azure Cosmos 帳戶的方式，請參閱下列文章：

* [設定 Azure Cosmos DB 的防火牆](how-to-configure-firewall.md)一文。

* [針對 Azure Cosmos DB 帳戶設定虛擬網路和子網路型存取](how-to-configure-vnet-service-endpoint.md)
