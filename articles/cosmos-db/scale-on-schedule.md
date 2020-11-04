---
title: 使用 Azure Functions 計時器依排程調整 Azure Cosmos DB
description: 瞭解如何使用 PowerShell 和 Azure Functions 在 Azure Cosmos DB 中調整輸送量變更。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: c60f3fc6b4ce4a1aead273fedb81e39de697f576
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339252"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>使用 Azure Functions 計時器觸發程式來調整 Azure Cosmos DB 輸送量
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos 帳戶的效能取決於以每秒要求單位數 (RU/秒) 所提供的布建輸送量量。 布建為第二個數據細微性，而且會根據每小時的最高 RU/秒計費。 此佈建容量模型可讓服務提供可預測且一致的輸送量、保證低延遲以及高可用性。 大部分的生產工作負載都是這些功能。 不過，在開發和測試環境中，Azure Cosmos DB 只會在工作時間使用，您可以在早上擴大輸送量，並在工作時間後的晚上調整回來。

您可以透過 [Azure Resource Manager 範本](./templates-samples-sql.md)、 [Azure CLI](cli-samples.md)和 [PowerShell](powershell-samples.md)、適用于 Core (SQL) API 帳戶，或使用特定語言的 Azure Cosmos DB sdk 來設定輸送量。 使用 Resource Manager 範本、Azure CLI 或 PowerShell 的優點是它們支援所有 Azure Cosmos DB 模型 Api。

## <a name="throughput-scheduler-sample-project"></a>輸送量排程器範例專案

為了簡化依排程調整 Azure Cosmos DB 的程式，我們已建立名為 [Azure Cosmos 輸送量](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)排程器的範例專案。 此專案是具有兩個計時器觸發程式（"ScaleUpTrigger" 和 "ScaleDownTrigger"）的 Azure Functions 應用程式。 觸發程式會執行 PowerShell 腳本，以在每個觸發程式中的檔案中定義每個資源的輸送量 `resources.json` 。 ScaleUpTrigger 設定為在上午8點執行，而 ScaleDownTrigger 設定為在下午6點執行，並可在 `function.json` 每個觸發程式的檔案內輕鬆更新這些時間。

您可以在本機複製此專案、加以修改，以指定要擴大和縮小的 Azure Cosmos DB 資源，以及要執行的排程。 稍後您可以將它部署在 Azure 訂用帳戶中，並使用受控服務識別搭配 [azure 角色型存取控制來保護它 (AZURE RBAC) ](role-based-access-control.md) 具有「Azure Cosmos DB 操作員」角色的許可權，以在 azure Cosmos 帳戶上設定輸送量。

## <a name="next-steps"></a>後續步驟

- 深入瞭解並下載 [Azure Cosmos DB 輸送量](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)排程器的範例。