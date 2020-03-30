---
title: 使用重新分區優化 Azure 流分析作業
description: 本文介紹如何使用重新分區來優化無法並行化的 Azure 流分析作業。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732377"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>使用重新分區優化 Azure 流分析的處理

本文介紹如何使用重新分區來縮放 Azure 流分析查詢，以便對無法完全[並行化](stream-analytics-scale-jobs.md)的方案進行縮放。

[如果：](stream-analytics-parallelization.md)

* 您不控制輸入流的分區鍵。
* 源"噴射"跨多個分區的輸入，以後需要合併。

當您處理未根據自然輸入方案（如事件中心的**分區 Id）** 對未分片的流上處理資料時，需要重新分區或重新洗牌。 重新分區時，可以獨立處理每個分片，從而允許您線性擴展流式處理管道。

## <a name="how-to-repartition"></a>如何重新分區

要重新分區，請使用查詢中**分區 BY**語句之後的關鍵字**INTO。** 下面的示例按**DeviceID**將資料分區為 10 的分區計數。 **DeviceID**的雜湊用於確定哪個分區應接受哪個子流。 假定輸出支援分區寫入，並且具有 10 個分區，則為每個分區流獨立刷新資料。

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

以下依例查詢聯接兩個重新分區資料流程。 聯接兩個重新分區資料流程時，流必須具有相同的分區鍵和計數。 結果是具有相同分區方案的流。

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

輸出方案應匹配流方案鍵並計數，以便可以獨立刷新每個子流。 在刷新之前，流也可以由不同的方案合併並再次重新分區，但應避免使用該方法，因為它會增加處理的一般延遲並增加資源利用率。

## <a name="streaming-units-for-repartitions"></a>用於重新分區的流式處理單元

試驗並觀察作業的資源使用方式，以確定所需的分區的確切數量。 必須根據每個分區所需的物理資源調整[流式處理單元 （SU）](stream-analytics-streaming-unit-consumption.md)的數量。 通常，每個分區需要六個 S統一。 如果分配給作業的資源不足，則系統將僅應用重新分區，如果它使作業受益。

## <a name="repartitions-for-sql-output"></a>SQL 輸出的重新分區

當作業使用 SQL 資料庫進行輸出時，請使用顯式重新分區以匹配最佳分區計數以最大化輸送量。 由於 SQL 最適合八個編寫器，因此在刷新之前將流重新分區為 8，或者將流重新劃分到更上游的某個地方，可能會有利於工作績效。 

當輸入資料分割超過 8 個時，繼承輸入資料分割配置可能不會是適當的選擇。 請考慮在查詢中使用[INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)顯式指定輸出編寫器的數量。 

下面的示例從輸入中讀取，無論它是否自然分區，並根據 DeviceID 維度對流進行了十倍的重新分區，並將資料刷新到輸出。 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。


## <a name="next-steps"></a>後續步驟

* [開始使用 Azure 串流分析](stream-analytics-introduction.md)
* [利用 Azure 串流分析中的查詢平行化作業](stream-analytics-parallelization.md)
