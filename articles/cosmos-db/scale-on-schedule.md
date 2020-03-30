---
title: 使用 Azure 函數計時器按計劃縮放 Azure 宇宙資料庫
description: 瞭解如何使用 PowerShell 和 Azure 函數縮放 Azure Cosmos DB 中的輸送量更改。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935164"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>使用 Azure 函數計時器觸發器縮放 Azure 宇宙資料庫輸送量

Azure Cosmos 帳戶的性能基於以每秒請求單位 （RU/s） 表示的預配輸送量量。 預配是第二細微性，並且根據每小時的最高 RU/s 計費。 此佈建容量模型可讓服務提供可預測且一致的輸送量、保證低延遲以及高可用性。 大多數生產工作負載都具有這些功能。 但是，在 Azure Cosmos DB 僅在工作時間使用的開發和測試環境中，可以提高上午的輸送量，並在工作時間後在晚上縮減。

您可以通過[Azure 資源管理器範本](resource-manager-samples.md)[、Azure CLI](cli-samples.md)和[PowerShell](powershell-samples-sql.md)為核心 （SQL） API 帳戶，或使用特定于語言的 Azure Cosmos DB SDK 設置輸送量。 使用資源管理器範本、Azure CLI 或 PowerShell 的好處是它們支援所有 Azure Cosmos DB 模型 API。

## <a name="throughput-scheduler-sample-project"></a>輸送量計畫程式示例專案

為了簡化按計劃縮放 Azure Cosmos DB 的過程，我們創建了一個示例專案，稱為[Azure Cosmos 輸送量計畫程式](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)。 此專案是具有兩個計時器觸發器的 Azure 函數應用 - "縮放向上觸發器"和"縮放向下觸發器"。 觸發器運行 PowerShell 腳本，該腳本按每個觸發器中`resources.json`檔中定義的每個資源設置輸送量。 ScaleUpTrigger 配置為在 UTC 上午 8 點運行，縮放向下觸發器配置為在 UTC 下午 6 點運行，這些時間可以輕鬆地`function.json`在每個觸發器的檔內更新。

您可以在本地克隆此專案，對其進行修改以指定要向上和向下縮放的 Azure Cosmos DB 資源以及要運行的計畫。 稍後，您可以在 Azure 訂閱中部署它，並使用託管服務標識使用[基於角色的存取控制](role-based-access-control.md)（RBAC） 許可權與"Azure Cosmos DB 運算子"角色進行保護，以在 Azure Cosmos 帳戶上設置輸送量。

## <a name="next-steps"></a>後續步驟

- 瞭解更多資訊並從[Azure Cosmos DB 輸送量計畫程式](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)下載示例。
