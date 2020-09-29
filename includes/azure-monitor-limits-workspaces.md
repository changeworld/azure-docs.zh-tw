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
ms.openlocfilehash: 9de26246756f831ace57e7ed03a3a598ef020c91
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91451369"
---
**資料收集磁碟區和保留** 

| 服務層級 | 每天限制 | 資料保留 | 註解 |
|:---|:---|:---|:---|
| 目前的每 GB 定價層<br>(2018 年 4 月引進) | 沒有限制 | 30 - 730 天 | 保留超過 31 天的資料需要額外付費。 深入了解 Azure 監視器價格。 |
| 舊版免費層<br>(2016 年 4 月引進) | 500 MB | 7 天 | 當您的工作區達到每天 500 MB 的限制時，資料擷取會在第二天開始時停止並繼續。 一天是以 UTC 為基礎。 請注意，每天 500 MB 的限制不包含 Azure 資訊安全中心所收集的資料，而且將繼續收集高於此限制的資料。  |
| 舊版獨立的每 GB 層<br>(2016 年 4 月引進) | 沒有限制 | 30 至 730 天 | 保留超過 31 天的資料需要額外付費。 深入了解 Azure 監視器價格。 |
| 每個節點的舊版 (OMS)<br>(2016 年 4 月引進) | 沒有限制 | 30 至 730 天 | 保留超過 31 天的資料需要額外付費。 深入了解 Azure 監視器價格。 |
| 舊版標準層 | 沒有限制 | 30 天  | 無法調整保留期 |
| 舊版進階層 | 沒有限制 | 365 天  | 無法調整保留期 |

**每一訂用帳戶的工作區數目。**

| 定價層    | 工作區限制 | 註解
|:---|:---|:---|
| 免費層  | 10 | 此限制無法增加。 |
| 所有其他層級 | 沒有限制 | 您會受到資源群組內的資源數目和每一訂用帳戶的資源群組數目所限制 |

**Azure 入口網站**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 記錄查詢所傳回的記錄數目上限 | 10,000 | 在查詢中使用查詢範圍、時間範圍和篩選條件來減少查詢結果。 |


**資料收集器 API**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 單一篇文章的大小上限 | 30 MB | 將較大的磁碟區分割成多篇文章。 |
| 欄位值的大小上限  | 32 KB | 超過 32 KB 的欄位會被截斷。 |

**搜尋 API**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 在單一查詢中傳回的記錄數目上限 | 500,000 | |
| 所傳回的資料大小上限 | 64,000,000 個位元組 (~61 MiB)| |
| 查詢執行時間上限 | 10 分鐘 | 如需詳細資訊，請參閱[逾時](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)。  |
| 要求速率上限 | 每個 Azure AD 使用者或用戶端 IP 位址每 30 秒 200 個要求 | 如需詳細資訊，請參閱[速率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)。 |

**一般工作區限制**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 資料表中的資料行數上限         | 500 | |
| 資料行名稱的字元數上限 | 500 | |
| 資料匯出 | 目前無法使用 | 使用 Azure 函式或邏輯應用程式來彙總和匯出資料。 | 

**<a name="data-ingestion-volume-rate">資料擷取磁碟區速率</a>**

Azure 監視器是一種大規模的資料服務，服務對象為每月需傳送數 TB 資料 (且不斷成長) 的上千名客戶。 磁碟區速率限制旨在將 Azure 監視器客戶隔離，避免其受到多租用戶環境中突然激增的尖峰所影響。 定義於工作區的預設擷取磁碟區速率閾值為 500 MB (已壓縮)，解壓縮大約 **6 GB/分鐘** -- 根據記錄長度和其壓縮率的不同，實際大小在資料類型之間可能會有所不同。 不論是使用[診斷設定](../articles/azure-monitor/platform/diagnostic-settings.md)、[資料收集器 API](../articles/azure-monitor/platform/data-collector-api.md) 或代理程式，從 Azure 資源傳送的所有內嵌資料皆適用磁碟區速率限制。

如果您以高於工作區中所設定閾值的 80% 速率將資料傳送至工作區時，則每隔 6 小時會將事件傳送至工作區中的 [作業] 資料表，同時會持續超過閾值。 當內嵌的磁碟區速率高於閾值時，則系統會卸除某些資料，且每隔 6 小時會將事件傳送至工作區中的 [作業] 資料表，同時會持續超過閾值。 如果您的擷取磁碟區速率持續超過閾值，或您希望很快能達到某個閾值，則可以透過開啟支援要求，要求增加速率。 

若希望在接近或達到擷取磁碟區速率限制時，能夠在工作區中收到通知，請使用下列查詢建立[記錄警示規則](../articles/azure-monitor/platform/alerts-log.md)，該查詢具有警示邏輯基於結果數目大於零、評估期間為 5 分鐘且頻率為 5 分鐘的情況。

超過閾值的擷取磁碟區速率
```Kusto
Operation
| where OperationCategory == "Ingestion"
| where OperationKey == "Ingestion rate limit"
| where OperationStatus == "Error"
```

超過 80% 閾值的擷取磁碟區速率
```Kusto
Operation
| where OperationCategory == "Ingestion"
| where OperationKey == "Ingestion rate limit"
| where OperationStatus == "Warning"
```

超過 70% 閾值的擷取磁碟區速率
```Kusto
Operation
| where OperationCategory == "Ingestion"
| where OperationKey == "Ingestion rate limit"
| where OperationStatus == "Info"
```

>[!NOTE]
>視使用 Log Analytics 的時間長度而定，您可能會有舊版定價層的存取權。 深入了解 [Log Analytics 舊版定價層](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)。 
