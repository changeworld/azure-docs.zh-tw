---
title: Azure Cosmos DB SQL SDK 連接模式
description: 瞭解 Azure Cosmos DB SQL Sdk 上可用的不同連接模式。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: c30e97a4bff8fa845f6eb3c3092a00ee541e59f4
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032791"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB SQL SDK 連接模式
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

用戶端連線至 Azure Cosmos DB 的方式會影響重要的效能，特別是針對觀察到的用戶端延遲。 Azure Cosmos DB 透過 HTTPS （稱為「閘道模式」）提供簡單的開放 RESTful 程式設計模型。 此外，它還提供一個有效率的 TCP 通訊協定，它在通訊模型中也 RESTful，並使用 TLS 進行初始驗證和加密流量，稱為直接模式。

## <a name="available-connectivity-modes"></a>可用的連接模式

可用的兩種連接模式如下：

  * 閘道模式
      
    所有 SDK 平臺都支援閘道模式。 如果您的應用程式在有嚴格防火牆限制的公司網路中執行，則閘道模式是最好的選擇，因為它會使用標準 HTTPS 埠和單一 DNS 端點。 不過，效能的取捨在於，每次從 Azure Cosmos DB 讀取或寫入資料時，閘道模式都會涉及額外的網路躍點。 當您在擁有有限數目之通訊端連線的環境中執行應用程式時，也建議使用閘道連線模式。

    當您在 Azure Functions 中使用 SDK 時（特別是在取用 [方案](../azure-functions/functions-scale.md#consumption-plan)中），請注意目前的 [連接限制](../azure-functions/manage-connections.md)。

  * 直接模式

    直接模式支援透過 TCP 通訊協定連線，並提供更佳的效能，因為網路躍點較少。 應用程式會直接連接到後端複本。 目前只有 .NET 和 JAVA SDK 平臺支援直接模式。
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB 連接模式" border="false":::

這些連線模式基本上會將資料平面要求的路由（檔讀取和寫入）從您的用戶端電腦移至 Azure Cosmos DB 後端中的資料分割。 直接模式是最佳效能的最佳選項，可讓您的用戶端直接開啟 Azure Cosmos DB 後端中的資料分割的 TCP 連線，並傳送要求 *直接* lesson.ly 而不需要媒介。 相反地，在閘道模式中，用戶端所提出的要求會路由傳送至 Azure Cosmos DB 前端中的所謂「閘道」伺服器，然後將您對 Azure Cosmos DB 後端中的適當分割區)  (的要求進行風扇輸出。

## <a name="service-port-ranges"></a>服務埠範圍

當您使用直接模式時，除了閘道埠之外，您還必須確定10000和20000之間的埠範圍是開啟的，因為 Azure Cosmos DB 使用動態 TCP 埠。 在 [私人端點](./how-to-configure-private-endpoints.md)上使用直接模式時，必須開啟從0到65535的 TCP 埠的完整範圍。 如果未開啟這些埠，而您嘗試使用 TCP 通訊協定，您可能會收到503服務無法使用的錯誤。

下表顯示各種 Api 可用的連線模式摘要，以及用於每個 API 的服務埠：

|連線模式  |支援的通訊協定  |支援的 SDK  |API/服務連接埠  |
|---------|---------|---------|---------|
|閘道  |   HTTPS    |  所有 Sdk    |   SQL (443) 、MongoDB (10250、10255、10256) 、資料表 (443) 、Cassandra (10350) 、Graph (443)  <br> 埠10250會對應到預設的 Azure Cosmos DB API for MongoDB 實例，但不含異地複寫。 而埠10255和10256則對應到具有異地複寫的實例。   |
|直接    |     TCP    |  .NET SDK JAVA SDK    | 使用公用/服務端點時：10000到20000範圍中的埠<br>使用私人端點時：0到65535範圍中的埠 |

## <a name="next-steps"></a>後續步驟

針對特定的 SDK 平臺效能優化：

* [.NET V2 SDK 效能秘訣](performance-tips.md)

* [.NET V3 SDK 效能秘訣](performance-tips-dotnet-sdk-v3-sql.md)
 
* [JAVA V4 SDK 效能秘訣](performance-tips-java-sdk-v4-sql.md)