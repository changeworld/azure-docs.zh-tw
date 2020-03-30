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
ms.openlocfilehash: 97f265d76ac70891e9cefc0ef6651e439706ed23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334701"
---
**資料收集量和保留率** 

| 層 | 每天的限制 | 資料保留 | 註解 |
|:---|:---|:---|:---|
| 當前每 GB 定價層<br>（2018年4月推出） | 沒有限制 | 30 - 730 天 | 超過 31 天的資料保留需要支付額外費用。 深入了解 Azure 監視器價格。 |
| 傳統免費層<br>（2016年4月推出） | 500 MB | 7 天 | 當您的工作區達到每天 500 MB 的限制時，資料引入將停止並在第二天開始時恢復。 一天是以 UTC 為基礎。 請注意，Azure 安全中心收集的資料不包括在此每天 500 MB 的限制中，並將繼續收集超過此限制。  |
| 舊式獨立每個 GB 層<br>（2016年4月推出） | 沒有限制 | 30 至 730 天 | 超過 31 天的資料保留需要支付額外費用。 深入了解 Azure 監視器價格。 |
| 每個節點的遺留 （OMS）<br>（2016年4月推出） | 沒有限制 | 30 至 730 天 | 超過 31 天的資料保留需要支付額外費用。 深入了解 Azure 監視器價格。 |
| 舊標準層 | 沒有限制 | 30 天  | 無法調整保留 |
| 傳統高級層 | 沒有限制 | 365 天  | 無法調整保留 |

**每個訂閱的工作區數。**

| 定價層    | 工作區限制 | 註解
|:---|:---|:---|
| 免費層  | 10 | 無法增加此限制。 |
| 所有其他層 | 沒有限制 | 您受到資源組中的資源數和每個訂閱的資源組數的限制。 |

**Azure 門戶**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 日誌查詢返回的最大記錄 | 10,000 | 使用查詢中的查詢範圍、時間範圍和篩選器減少結果。 |


**資料收集器 API**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 單個帖子的最大大小 | 30 MB | 將較大的卷拆分為多個帖子。 |
| 欄位值的最大大小  | 32 KB | 超過 32 KB 的欄位會被截斷。 |

**搜尋 API**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 在單個查詢中返回的最大記錄 | 500,000 | |
| 返回的最大資料大小 | 64，000，000 位元組 （+61 MiB）| |
| 最長查詢執行時間 | 10 分鐘 | 有關詳細資訊[，請參閱超時](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)。  |
| 最大請求率 | 每個 AAD 使用者或用戶端 IP 位址每 30 秒發出 200 個請求 | 有關詳細資訊，請參閱[費率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)。 |

**常規工作區限制**

| 類別 | 限制 | 註解 |
|:---|:---|:---|
| 表中的最大列數         | 500 | |
| 列名稱的最大字元數 | 500 | |
| 資料匯出 | 目前無法使用 | 使用 Azure 函數或邏輯應用聚合和匯出資料。 | 

**資料引入容積率**


Azure 監視器是一種大規模的資料服務，服務對象為每月需傳送數 TB 資料 (且不斷成長) 的上千名客戶。 使用[診斷設置](../articles/azure-monitor/platform/diagnostic-settings.md)從 Azure 資源發送的資料的預設引入容積速率限制約為每個工作區**6 GB/min。** 這是一個近似值，因為實際大小可能因資料類型而異，具體取決於日誌長度及其壓縮率。 此限制不適用於從代理或[資料收集器 API](../articles/azure-monitor/platform/data-collector-api.md)發送的資料。

如果以較高速率將資料發送到單個工作區，則某些資料將被刪除，並且每隔 6 小時將事件發送到工作區中的 *"操作"* 表，同時繼續超過閾值。 如果您的引入量繼續超過速率限制，或者您希望很快達到它，您可以通過打開支援請求請求請求增加工作區。
 
要在工作區中此類事件收到通知，請使用以下查詢創建[日誌警報規則](../articles/azure-monitor/platform/alerts-log.md)，該查詢的警報邏輯基於結果數大於零。

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>根據使用日誌分析的時間，您可能有權訪問舊版定價層。 詳細瞭解[日誌分析傳統定價層](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)。 