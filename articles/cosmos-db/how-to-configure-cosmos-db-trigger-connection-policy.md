---
title: Azure Functions 的 Cosmos DB 觸發程序連線原則
description: 了解如何設定 Azure Functions 的 Cosmos DB 觸發程序所使用的連線原則
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: df30a0ddca58e6d7bd74184fa7287df6818cfc37
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117151"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>如何設定 Azure Functions 的 Cosmos DB 觸發程序所使用的連線原則

本文說明如何設定使用 Azure Functions 的 Cosmos DB 觸發程序連線到 Azure Cosmos 帳戶時的連線原則。

## <a name="why-is-the-connection-policy-important"></a>連線原則為何很重要？

有兩種連線模式 - 直接模式和閘道模式。 若要深入了解這些連線模式，請參閱[效能祕訣](./performance-tips.md#networking)一文。 依預設會使用**閘道**來建立 Azure Functions 的 Cosmos DB 觸發程序的所有連線。 不過，它可能不是效能導向案例的最佳選項。

## <a name="changing-the-connection-mode-and-protocol"></a>變更連接模式和通訊協定

有兩個重要組態設定可用來設定用戶端連線原則 - **連線模式**和**連線通訊協定**。 您可以變更 Azure Functions 的 Cosmos DB 觸發程序所使用的預設連線模式和通訊協定，以及所有的 [Azure Cosmos DB 繫結](../azure-functions/functions-bindings-cosmosdb-v2-output.md)。 若要變更預設設定，您需要在您的 Azure Functions 專案或 Azure Functions 應用程式中找出 `host.json` 檔案，並新增下列[額外設定](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)：

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

其中 `connectionMode` 必須具有所需的連連線模式 (直接或閘道) 和 `protocol` 所需的連線通訊協定 (Tcp 或 Https)。 

如果您的 Azure Functions 專案使用 Azure Functions V1 執行階段，組態會有些許名稱差異，您應該使用 `documentDB` 而不是 `cosmosDB`：

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> 使用 Azure Functions 取用方案主控方案時，每個執行個體都有可維護的通訊端連線數量限制。 使用 Direct / TCP 模式時，會依設計建立多個連線並可叫用[取用方案限制](../azure-functions/manage-connections.md#connection-limit)，在此情況下您可以使用閘道模式或在 [App Service 模式](../azure-functions/functions-scale.md#app-service-plan)中執行 Azure Functions。

## <a name="next-steps"></a>後續步驟

* [Azure Functions 中的連線限制](../azure-functions/manage-connections.md#connection-limit)
* [Azure Cosmos DB 效能祕訣](./performance-tips.md)
* [程式碼範例](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
