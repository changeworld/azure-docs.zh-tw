---
title: 包含檔案
description: 包含檔案
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 864b37c9e59786546ad2c29faf8457cfc3a21f6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82161160"
---
**資料收集磁片區與保留期** 

| 層 | 每天限制 | 資料保留 | 註解 |
|:---|:---|:---|:---|
| 目前的每 GB 定價層<br>（2018年4月引進） | 沒有限制 | 30-730 天 | 超過31天的資料保留期可供額外收費。 深入了解 Azure 監視器價格。 |
| 舊版免費層<br>（2016年4月引進） | 500 MB | 7 天 | 當您的工作區達到每日 500 MB 的限制時，資料內嵌會在下一天開始時停止並繼續。 一天是以 UTC 為基礎。 請注意，Azure 資訊安全中心所收集的資料不會包含在此 500 MB 的每日限制中，而且將會繼續以高於此限制的方式收集。  |
| 舊版獨立的每 GB 層<br>（2016年4月引進） | 沒有限制 | 30至730天 | 超過31天的資料保留期可供額外收費。 深入了解 Azure 監視器價格。 |
| 每個節點的舊版（OMS）<br>（2016年4月引進） | 沒有限制 | 30至730天 | 超過31天的資料保留期可供額外收費。 深入了解 Azure 監視器價格。 |
| 舊版標準層 | 沒有限制 | 30 天  | 無法調整保留期 |
| 舊版 Premium 層 | 沒有限制 | 365 天  | 無法調整保留期 |

**每個訂用帳戶的工作區數目。**

| 定價層    | 工作區限制 | 註解
|:---|:---|:---|
| 免費層  | 10 | 無法增加此限制。 |
| 所有其他層級 | 沒有限制 | 您受限於資源群組內的資源數目，以及每個訂用帳戶的資源群組數目。 |

**Azure 入口網站**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 記錄查詢所傳回的記錄數目上限 | 10,000 | 在查詢中使用查詢範圍、時間範圍和篩選來減少結果。 |


**資料收集器 API**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 單一貼文的大小上限 | 30 MB | 將較大的磁片區分割成多篇文章。 |
| 域值的大小上限  | 32 KB | 超過 32 KB 的欄位會被截斷。 |

**搜尋 API**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 在單一查詢中傳回的記錄數目上限 | 500,000 | |
| 傳回的資料大小上限 | 64000000個位元組（~ 61 MiB）| |
| 查詢執行時間上限 | 10 分鐘 | 如需詳細資料，請參閱[超時](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)。  |
| 最大要求速率 | 每個 AAD 使用者或用戶端 IP 位址每30秒200個要求 | 如需詳細資訊，請參閱[速率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)。 |

**一般工作區限制**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 資料表中的最大資料行         | 500 | |
| 資料行名稱的字元數上限 | 500 | |
| 資料匯出 | 目前無法使用 | 使用 Azure 函式或邏輯應用程式來匯總和匯出資料。 | 

**資料內嵌磁片區速率**


Azure 監視器是一種大規模的資料服務，服務對象為每月需傳送數 TB 資料 (且不斷成長) 的上千名客戶。 使用[診斷設定](../articles/azure-monitor/platform/diagnostic-settings.md)從 Azure 資源傳送之資料的預設內嵌磁片區速率限制約為每個工作區**6 GB/分鐘**。 這是估計值，因為根據記錄長度和其壓縮比率，實際大小可能會在資料類型之間有所不同。 此限制不適用於從代理程式或[資料收集器 API](../articles/azure-monitor/platform/data-collector-api.md)傳送的資料。

如果您以較高的速率將資料傳送至單一工作區，則會卸載某些資料，並每隔6小時將事件傳送至工作區中的*作業資料表，* 而臨界值會繼續超過閾值。 如果您的內嵌磁片區持續超過速率限制，或您希望很快就會到達，您可以傳送電子郵件至 LAIngestionRate@microsoft.com 或開啟支援要求，以要求增加您的工作區。
 
若要在您的工作區中收到這類事件的通知，請使用下列查詢建立[記錄警示規則](../articles/azure-monitor/platform/alerts-log.md)，並以大於的結果數目為零的警示邏輯基底。

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>視您使用 Log Analytics 的時間長度而定，您可能會有舊版定價層的存取權。 深入瞭解[Log Analytics 舊版定價層](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)。 
