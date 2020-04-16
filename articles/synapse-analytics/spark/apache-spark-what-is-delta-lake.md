---
title: 什麼是三角洲湖
description: 三角洲湖概述及其作為 Azure 突觸分析的一部分的工作方式
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429196"
---
# <a name="what-is-delta-lake"></a>什麼是 Delta Lake？

Azure 突觸分析與 Linux 基礎三角洲湖相容。 Delta Lake 是一個開源存儲層,為 Apache Spark 和大數據工作負載帶來 ACID(原子性、一致性、隔離性和持久性)事務。

## <a name="key-features"></a>主要功能

| 功能 | 描述 |
| --- | --- |
| **ACID 交易** | 數據湖通常通過多個進程和管道填充,其中一些進程和管道與讀取同時寫入數據。 在達美湖和添加事務之前,數據工程師必須經過手動錯誤易發流程,以確保數據完整性。 三角洲湖為數據湖帶來了熟悉的 ACID 交易。 它提供序列化,最強的隔離級別。 瞭解更多關於[潛入三角洲湖:解包交易日志](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)。|
| **可擴充的中繼資料處理** | 在大數據中,即使是元數據本身也可以是"大數據"。 Delta Lake 將中繼資料與資料一樣處理,利用Spark的分散式處理能力來處理其所有元資料。 因此,Delta Lake 可以輕鬆處理具有數十億個分區和檔的 PB 級表。 |
| **時間差(資料版本控制)** | "撤消"更改或返回早期版本的能力是事務的關鍵功能之一。 Delta Lake 提供數據快照,使您能夠還原到早期版本的數據,以便進行審核、回滾或重現實驗。 瞭解更多關於[介紹大範圍資料湖的三角洲湖時間旅行](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)。 |
| **開啟格式** | Apache Parquet 是三角洲湖的基準格式,使您能夠利用該格式本機的有效壓縮和編碼方案。 |
| **整合處理及流式處理來源及接收器** | 增量湖中的表既是批處理表,也是流源和接收器。 流數據引入、批量歷史回填和互動式查詢都開箱即用。 |
| **架構強制** | 架構實施有助於確保數據類型正確且存在所需的列,防止不良數據導致數據不一致。 有關詳細資訊,請參閱[潛入三角洲湖:架構實施&演進](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **架構演化** | Delta Lake 使您能夠對可自動應用的表架構進行更改,而無需編寫遷移 DDL。 有關詳細資訊,請參閱[潛入三角洲湖:架構實施&演進](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **審計歷史記錄** | Delta Lake 事務日誌記錄對數據所做的每個更改的詳細資訊,從而提供更改的完整審核跟蹤。 |
| **更新與移除** | 三角洲湖支援Scala / Java / Python和SQL API的各種功能。 支援合併、更新和刪除操作可説明您滿足合規性要求。 有關詳細資訊,請參閱使用 Python API[在三角洲湖表上發佈增量湖 0.4.0 版本](https://delta.io/news/delta-lake-0-4-0-released/)[和簡單、可靠的 upsert 和刪除](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html),其中包括用於合併、更新和刪除 DML 命令的代碼段。 |
| **100% 相容阿帕奇火花 API** | 開發人員可以使用 Delta Lake 及其現有數據管道,只需極少的更改,因為它與現有的 Spark 實現完全相容。 |

有關完整文件,請參閱[三角洲湖文檔頁面](https://docs.delta.io/latest/delta-intro.html)

有關詳細資訊,請參閱[三角洲湖專案](https://lfprojects.org)。

## <a name="next-steps"></a>後續步驟

- [阿帕奇火花文檔的 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
