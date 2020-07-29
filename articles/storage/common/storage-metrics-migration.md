---
title: 從儲存體分析計量移至 Azure 監視器計量 |Microsoft Docs
description: 瞭解如何從儲存體分析計量（傳統計量）轉換為 Azure 監視器中的計量。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 219d2b972089f9d3b7f84caa8b527474ac241c4f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374162"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>轉換成 Azure 監視器中的計量

Azure 儲存體現在會將計量整合到 Azure 監視器平臺。 **2023 年8月 31**日儲存體分析計量，亦稱為*傳統計量*將會淘汰。 如果您使用傳統計量，請務必在該日期之前轉換到 Azure 監視器中的計量。 本文可協助您進行轉換。

## <a name="steps-to-complete-the-transition"></a>完成轉換的步驟

若要轉換為 Azure 監視器中的計量，建議您採用下列方法。

1. 瞭解 Azure 監視器中傳統計量和計量之間的一些[主要差異](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor)。 

2. 編譯您目前使用的傳統計量清單。

3. 識別[Azure 監視器中的哪些計量](#metrics-mapping-between-old-metrics-and-new-metrics)提供與您目前所使用的計量相同的資料。 
   
4. 建立[圖表](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal)或[儀表板](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal)來查看度量資料。

   > [!NOTE]
   > Azure 監視器中的計量預設為啟用，因此您不需要執行任何動作來開始捕獲計量。 不過，您必須建立圖表或儀表板來查看這些計量。 
 
5. 如果您已建立以傳統儲存體計量為基礎的警示規則，請根據 Azure 監視器中的計量[建立警示規則](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。 

6. 在 Azure 監視器中查看所有計量之後，您就可以關閉傳統記錄。 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>傳統計量與 Azure 監視器中的計量

本節說明這兩個計量平臺之間的幾個主要差異。

主要的差異在於計量的管理方式。 傳統計量是由 Azure 儲存體管理，而 Azure 監視器中的計量則是由 Azure 監視器來管理。 使用傳統計量時，Azure 儲存體會收集度量值、加以匯總，然後將它們儲存在位於儲存體帳戶中的資料表。 使用 Azure 監視器中的計量，Azure 儲存體會將度量資料傳送至 Azure 監視器後端。 Azure 監視器提供統一的監視體驗，其中包含來自 Azure 入口網站的資料，以及內嵌的資料。 

就計量支援而言，傳統計量只會提供 Azure Blob 儲存體的**容量**計量。 Azure 監視器中的計量可提供 Blob、資料表、檔案、佇列和 premium 儲存體的容量計量。 傳統計量會在 Blob、資料表、Azure 檔案和佇列儲存體上提供**交易**計量。 Azure 監視器中的計量會將 premium 儲存體新增至該清單。

如果您帳戶中的活動不會觸發計量，則傳統度量會顯示該度量的值為零（0）。 Azure 監視器中的計量會完全省略資料，這會導致更清楚的報告。 例如，使用傳統計量時，如果未報告任何伺服器逾時錯誤，則 `ServerTimeoutError` [計量] 資料表中的值會設定為0。 當您查詢「維度」等於的「度量」值時，Azure 監視器不會傳回任何資料 `Transactions` `ResponseType` `ServerTimeoutError` 。 

若要深入瞭解 Azure 監視器中的計量，請參閱[Azure 監視器中的計量](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>將傳統計量對應至 Azure 監視器中的計量

 使用這些資料表來識別 Azure 監視器中的哪些計量提供與您目前所使用的計量相同的資料。 

**容量計量**

| 傳統計量 | Azure 監視器中的度量 |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity`維度 `BlobType` 等於 `BlockBlob` 或`PageBlob` |
| `ObjectCount`        | `BlobCount`維度 `BlobType` 等於 `BlockBlob` 或`PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> 也有數個新的容量計量無法當做傳統計量提供。 若要查看完整清單，請參閱[計量](../common/monitor-storage-reference.md#metrics)。

**交易計量**

| 傳統計量 | Azure 監視器中的度量 |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | 維度 `ResponseType` 等於 `AuthorizationError` 且維度 `Authentication` 等於的交易`Anonymous` |
| `AnonymousClientOtherError` | 維度 `ResponseType` 等於 `ClientOtherError` 且維度 `Authentication` 等於的交易`Anonymous` |
| `AnonymousClientTimeoutError` | 維度 `ResponseType` 等於 `ClientTimeoutError` 且維度 `Authentication` 等於的交易`Anonymous` |
| `AnonymousNetworkError` | 維度 `ResponseType` 等於 `NetworkError` 且維度 `Authentication` 等於的交易`Anonymous` |
| `AnonymousServerOtherError` | 維度 `ResponseType` 等於 `ServerOtherError` 且維度 `Authentication` 等於的交易`Anonymous` |
| `AnonymousServerTimeoutError` | 維度 `ResponseType` 等於 `ServerTimeoutError` 且維度 `Authentication` 等於的交易`Anonymous` |
| `AnonymousSuccess` | 維度 `ResponseType` 等於 `Success` 且維度 `Authentication` 等於的交易`Anonymous` |
| `AnonymousThrottlingError` | 維度 `ResponseType` 等於 `ClientThrottlingError` 或 `ServerBusyError` 且維度 `Authentication` 等於的交易`Anonymous` |
| `AuthorizationError` | 維度等於的交易 `ResponseType``AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | 維度等於的交易 `ResponseType``ClientOtherError` |
| `ClientTimeoutError` | 維度等於的交易 `ResponseType``ClientTimeoutError` |
| `NetworkError` | 維度等於的交易 `ResponseType``NetworkError` |
| `PercentAuthorizationError` | 維度等於的交易 `ResponseType``AuthorizationError` |
| `PercentClientOtherError` | 維度等於的交易 `ResponseType``ClientOtherError` |
| `PercentNetworkError` | 維度等於的交易 `ResponseType``NetworkError` |
| `PercentServerOtherError` | 維度等於的交易 `ResponseType``ServerOtherError` |
| `PercentSuccess` | 維度等於的交易 `ResponseType``Success` |
| `PercentThrottlingError` | 維度 `ResponseType` 等於或的交易 `ClientThrottlingError``ServerBusyError` |
| `PercentTimeoutError` | 維度 `ResponseType` 等於 `ServerTimeoutError` 或 `ResponseType` 等於的交易`ClientTimeoutError` |
| `SASAuthorizationError` | 維度 `ResponseType` 等於 `AuthorizationError` 且維度 `Authentication` 等於的交易`SAS` |
| `SASClientOtherError` | 維度 `ResponseType` 等於 `ClientOtherError` 且維度 `Authentication` 等於的交易`SAS` |
| `SASClientTimeoutError` | 維度 `ResponseType` 等於 `ClientTimeoutError` 且維度 `Authentication` 等於的交易`SAS` |
| `SASNetworkError` | 維度 `ResponseType` 等於 `NetworkError` 且維度 `Authentication` 等於的交易`SAS` |
| `SASServerOtherError` | 維度 `ResponseType` 等於 `ServerOtherError` 且維度 `Authentication` 等於的交易`SAS` |
| `SASServerTimeoutError` | 維度 `ResponseType` 等於 `ServerTimeoutError` 且維度 `Authentication` 等於的交易`SAS` |
| `SASSuccess` | 維度 `ResponseType` 等於 `Success` 且維度 `Authentication` 等於的交易`SAS` |
| `SASThrottlingError` | 維度 `ResponseType` 等於 `ClientThrottlingError` 或 `ServerBusyError` 且維度 `Authentication` 等於的交易`SAS` |
| `ServerOtherError` | 維度等於的交易 `ResponseType``ServerOtherError` |
| `ServerTimeoutError` | 維度等於的交易 `ResponseType``ServerTimeoutError` |
| `Success` | 維度等於的交易 `ResponseType``Success` |
| `ThrottlingError` | `Transactions`維度 `ResponseType` 等於 `ClientThrottlingError` 或`ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>後續步驟

* [Azure 監視器](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Azure 監視器中的儲存體計量](./storage-metrics-in-azure-monitor.md)
