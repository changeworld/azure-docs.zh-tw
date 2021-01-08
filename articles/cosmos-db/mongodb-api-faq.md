---
title: Azure Cosmos DB 的 MongoDB API 相關常見問題
description: 取得 Azure Cosmos DB 的 MongoDB API 相關常見問題的解答
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019001"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB 的 MongoDB API 相關常見問題
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

適用于 MongoDB 的 Azure Cosmos DB API 是一種網路通訊協定相容性層，可讓應用程式使用現有、支援 MongoDB 的 Sdk 和驅動程式，輕鬆且透明地與原生 Azure Cosmos 資料庫引擎通訊。 開發人員現在可以使用現有的 MongoDB 工具鏈和技能，建立利用 Azure Cosmos DB 的應用程式。 開發人員受益于 Azure Cosmos DB 的獨特功能，包括使用多重區域寫入複寫的全域散發、自動編制索引、備份維護、財務支援的服務等級協定 (Sla) 等等。

## <a name="how-do-i-connect-to-my-database"></a>我要如何連線到我的資料庫？

若要使用適用於 MongoDB 的 Azure Cosmos DB API 來連線至 Cosmos 資料庫，最快的方式是直接前往 [Azure 入口網站](https://portal.azure.com)。 移至您的帳戶，然後在左側瀏覽功能表中按一下 [快速入門]。 快速入門是取得程式碼片段以連接到資料庫的最佳方式。

Azure Cosmos DB 會強制執行嚴格的安全性需求和標準。 Azure Cosmos DB 帳戶需要透過 TLS 進行驗證和安全通訊，因此請務必使用 Tlsv1.1 1.2。

如需詳細資訊，請參閱[使用適用於 MongoDB 的 Azure Cosmos DB API 連線到 Cosmos 資料庫](connect-mongodb-account.md)。

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 適用于 MongoDB 的 API 時的錯誤碼？

除了常見的 MongoDB 錯誤碼，Azure Cosmos DB 的 MongoDB API 也有自己的特定錯誤碼。 這些可以在 [疑難排解指南](mongodb-troubleshoot.md)中找到。

## <a name="supported-drivers"></a>支援的驅動程式

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>適用於 MongoDB 的 Simba 驅動程式是否支援與適用於 MongoDB 的 Azure Cosmos DB API 搭配使用？

是，您可以使用 Simba 的 Mongo ODBC 驅動程式搭配 Azure Cosmos DB 的 MongoDB API

## <a name="next-steps"></a>後續步驟

* [使用 Azure Cosmos DB 適用于 MongoDB 的 API 建立 .NET web 應用程式](create-mongodb-dotnet.md)
* [在 Azure Cosmos DB 中使用 Java 和 MongoDB API 建立主控台應用程式](create-mongodb-java.md)
