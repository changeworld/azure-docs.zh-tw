---
title: Azure Cosmos DB 的 MongoDB API 相關常見問題
description: 取得 Azure Cosmos DB 的 MongoDB API 相關常見問題的解答
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 05b0ff7d4f56a61c5c91848044a30cb9bf1d0f46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565304"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB 的 MongoDB API 相關常見問題

適用于 MongoDB 的 Azure Cosmos DB API 是一種網路通訊協定相容性層，可讓應用程式使用現有、支援 MongoDB 的 Sdk 和驅動程式，輕鬆且透明地與原生 Azure Cosmos 資料庫引擎通訊。 開發人員現在可以使用現有的 MongoDB 工具鏈和技能，建立利用 Azure Cosmos DB 的應用程式。 開發人員受益于 Azure Cosmos DB 的獨特功能，包括使用多重區域寫入複寫的全域散發、自動編制索引、備份維護、財務支援的服務等級協定 (Sla) 等等。

## <a name="how-do-i-connect-to-my-database"></a>我要如何連線到我的資料庫？

若要使用適用於 MongoDB 的 Azure Cosmos DB API 來連線至 Cosmos 資料庫，最快的方式是直接前往 [Azure 入口網站](https://portal.azure.com)。 移至您的帳戶，然後在左側瀏覽功能表中按一下 [快速入門]****。 快速入門是取得程式碼片段以連接到資料庫的最佳方式。

Azure Cosmos DB 會強制執行嚴格的安全性需求和標準。 Azure Cosmos DB 帳戶需要透過 TLS 進行驗證和安全通訊，因此請務必使用 Tlsv1.1 1.2。

如需詳細資訊，請參閱[使用適用於 MongoDB 的 Azure Cosmos DB API 連線到 Cosmos 資料庫](connect-mongodb-account.md)。

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 適用于 MongoDB 的 API 時的錯誤碼？

除了常見的 MongoDB 錯誤碼，適用於 MongoDB 的 Azure Cosmos DB API 有專用錯誤碼：

| 錯誤               | 程式碼  | 描述  | 解決方法  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 取用的要求單位總數超過容器已布建的要求單位速率，且已進行節流。 | 請考慮從 Azure 入口網站調整指派給容器或容器集的輸送量，或重試一次。 |
| ExceededMemoryLimit | 16501 | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 <br><br> 範例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |

## <a name="supported-drivers"></a>支援的驅動程式

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>適用於 MongoDB 的 Simba 驅動程式是否支援與適用於 MongoDB 的 Azure Cosmos DB API 搭配使用？

是，您可以使用 Simba 的 Mongo ODBC 驅動程式搭配 Azure Cosmos DB 的 MongoDB API

## <a name="next-steps"></a>後續步驟

* [使用 Azure Cosmos DB 適用于 MongoDB 的 API 建立 .NET web 應用程式](create-mongodb-dotnet.md)
* [在 Azure Cosmos DB 中使用 Java 和 MongoDB API 建立主控台應用程式](create-mongodb-java.md)
