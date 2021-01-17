---
title: 防止 Azure Cosmos DB API for MongoDB 作業的速率限制錯誤。
description: 瞭解如何使用 SSR (伺服器端重試) 功能，防止 MongoDB 作業的 Azure Cosmos DB API 發生速率限制錯誤。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540511"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>防止 Azure Cosmos DB API for MongoDB 作業的速率限制錯誤
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB API for MongoDB 作業可能會因為速率限制 (16500/429) 錯誤超過集合的輸送量限制 (ru) 而失敗。 

您可以啟用伺服器端重試 (SSR) 功能，並讓伺服器自動重試這些作業。 系統會在您帳戶中的所有集合短暫延遲之後，重試要求。 這項功能是在用戶端應用程式中處理速率限制錯誤的方便替代方法。


## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 流覽至您的 Azure Cosmos DB API for MongoDB 帳戶。

1. 移至 [**設定**] 區段底下的 [**功能**] 窗格。

1. 選取 **伺服器端重試**。

1. 按一下 [ **啟用** ]，為您帳戶中的所有集合啟用這項功能。

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="適用于 MongoDB 的 Azure Cosmos DB API 之伺服器端重試功能的螢幕擷取畫面":::


## <a name="next-steps"></a>後續步驟

若要深入瞭解如何針對常見的錯誤進行疑難排解，請參閱這篇文章：

* [針對 Azure Cosmos DB 的 MongoDB API 常見問題進行疑難排解](mongodb-troubleshoot.md)
