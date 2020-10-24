---
title: 從儲存體分析計量移至 Azure 監視器計量 |Microsoft Docs
description: 瞭解如何從儲存體分析計量轉換 (傳統計量) 至 Azure 監視器中的計量。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: c1dc0c7f37dc848ecd361848934cbcc5640afc66
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490759"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>轉換至 Azure 監視器中的計量

**2023 年8月 31**日儲存體分析計量，也稱為*傳統計量*將會淘汰。 如需詳細資訊，請參閱[官方公告](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)。 如果您使用傳統計量，請務必在該日期之前轉換到 Azure 監視器中的計量。 本文可協助您進行轉換。 

## <a name="steps-to-complete-the-transition"></a>完成轉換的步驟

若要轉換為 Azure 監視器中的計量，我們建議採用下列方法。

1. 瞭解 Azure 監視器中傳統計量和計量之間的一些 [主要差異](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) 。 

2. 編譯您目前使用的傳統計量清單。

3. 識別 [Azure 監視器中的哪些計量](#metrics-mapping-between-old-metrics-and-new-metrics) 提供與您目前使用的計量相同的資料。 
   
4. 建立 [圖表](/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) 或 [儀表板](/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) 來查看度量資料。

   > [!NOTE]
   > 預設會啟用 Azure 監視器中的計量，因此您不需要執行任何動作來開始捕獲計量。 不過，您必須建立圖表或儀表板來查看這些計量。 
 
5. 如果您已建立以傳統儲存體計量為基礎的警示規則，則根據 Azure 監視器中的計量 [建立警示規則](/azure/azure-monitor/platform/alerts-overview) 。 

6. 當您可以在 Azure 監視器中看到所有計量之後，您可以關閉傳統記錄。 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>傳統計量與 Azure 監視器中的計量

本節說明這兩個計量平臺之間的一些主要差異。

主要差異在於計量的管理方式。 傳統計量是由 Azure 儲存體管理，而 Azure 監視器中的計量是由 Azure 監視器管理。 使用傳統計量時，Azure 儲存體會收集計量值、匯總它們，然後將它們儲存在位於儲存體帳戶中的資料表。 使用 Azure 監視器中的計量時，Azure 儲存體會將計量資料傳送到 Azure 監視器後端。 Azure 監視器提供統一的監視體驗，包括來自 Azure 入口網站的資料以及內嵌的資料。 

傳統計量會傳送並儲存在 Azure 儲存體帳戶中。 Azure 監視器計量可以傳送到多個位置。 儲存體帳戶可以是這些位置之一，但不是必要的。  

至於計量支援，傳統計量只會提供 Azure Blob 儲存體的 **容量** 計量。 Azure 監視器中的計量提供 Blob、資料表、檔案、佇列和 premium 儲存體的容量計量。 傳統計量提供 Blob、資料表、Azure 檔案和佇列儲存體的 **交易** 計量。 Azure 監視器中的計量會將 premium 儲存體新增至該清單。

如果您帳戶中的活動不會觸發計量，則傳統計量會顯示該計量的值為零 (0) 。 Azure 監視器中的計量會完全略過資料，而這會導致清除報表。 例如，使用傳統計量時，如果未報告任何伺服器逾時錯誤，則 `ServerTimeoutError` 度量資料表中的值會設定為0。 當您查詢維度等於的度量值時，Azure 監視器不會傳回任何資料 `Transactions` `ResponseType` `ServerTimeoutError` 。 

若要深入瞭解 Azure 監視器中的計量，請參閱 [Azure 監視器中的計量](/azure/azure-monitor/platform/data-platform-metrics)。

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>將傳統計量對應至 Azure 監視器中的計量

 使用這些資料表來識別 Azure 監視器中的哪些計量提供與您目前使用的計量相同的資料。 

**容量計量**

| 傳統計量 | Azure 監視器中的度量 |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity` 維度 `BlobType` 等於 `BlockBlob` 或 `PageBlob` |
| `ObjectCount`        | `BlobCount` 維度 `BlobType` 等於 `BlockBlob` 或 `PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> 另外還有幾個新的容量計量無法作為傳統計量使用。 若要查看完整清單，請參閱 [計量](../common/monitor-storage-reference.md#metrics)。

**交易計量**

| 傳統計量 | Azure 監視器中的度量 |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | 維度 `ResponseType` 等於 `AuthorizationError` 且維度 `Authentication` 等於的交易 `Anonymous` |
| `AnonymousClientOtherError` | 維度 `ResponseType` 等於 `ClientOtherError` 且維度 `Authentication` 等於的交易 `Anonymous` |
| `AnonymousClientTimeoutError` | 維度 `ResponseType` 等於 `ClientTimeoutError` 且維度 `Authentication` 等於的交易 `Anonymous` |
| `AnonymousNetworkError` | 維度 `ResponseType` 等於 `NetworkError` 且維度 `Authentication` 等於的交易 `Anonymous` |
| `AnonymousServerOtherError` | 維度 `ResponseType` 等於 `ServerOtherError` 且維度 `Authentication` 等於的交易 `Anonymous` |
| `AnonymousServerTimeoutError` | 維度 `ResponseType` 等於 `ServerTimeoutError` 且維度 `Authentication` 等於的交易 `Anonymous` |
| `AnonymousSuccess` | 維度 `ResponseType` 等於 `Success` 且維度 `Authentication` 等於的交易 `Anonymous` |
| `AnonymousThrottlingError` | 維度 `ResponseType` 等於 `ClientThrottlingError` 或 `ServerBusyError` 且維度 `Authentication` 等於的交易 `Anonymous` |
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
| `PercentTimeoutError` | 維度 `ResponseType` 等於 `ServerTimeoutError` 或 `ResponseType` 等於的交易 `ClientTimeoutError` |
| `SASAuthorizationError` | 維度 `ResponseType` 等於 `AuthorizationError` 且維度 `Authentication` 等於的交易 `SAS` |
| `SASClientOtherError` | 維度 `ResponseType` 等於 `ClientOtherError` 且維度 `Authentication` 等於的交易 `SAS` |
| `SASClientTimeoutError` | 維度 `ResponseType` 等於 `ClientTimeoutError` 且維度 `Authentication` 等於的交易 `SAS` |
| `SASNetworkError` | 維度 `ResponseType` 等於 `NetworkError` 且維度 `Authentication` 等於的交易 `SAS` |
| `SASServerOtherError` | 維度 `ResponseType` 等於 `ServerOtherError` 且維度 `Authentication` 等於的交易 `SAS` |
| `SASServerTimeoutError` | 維度 `ResponseType` 等於 `ServerTimeoutError` 且維度 `Authentication` 等於的交易 `SAS` |
| `SASSuccess` | 維度 `ResponseType` 等於 `Success` 且維度 `Authentication` 等於的交易 `SAS` |
| `SASThrottlingError` | 維度 `ResponseType` 等於 `ClientThrottlingError` 或 `ServerBusyError` 且維度 `Authentication` 等於的交易 `SAS` |
| `ServerOtherError` | 維度等於的交易 `ResponseType``ServerOtherError` |
| `ServerTimeoutError` | 維度等於的交易 `ResponseType``ServerTimeoutError` |
| `Success` | 維度等於的交易 `ResponseType``Success` |
| `ThrottlingError` | `Transactions` 維度 `ResponseType` 等於 `ClientThrottlingError` 或 `ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>後續步驟

* [Azure 監視器](../../monitoring-and-diagnostics/monitoring-overview.md)

