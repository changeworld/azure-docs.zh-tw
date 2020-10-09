---
title: 使用重新分割來優化 Azure 串流分析作業
description: 本文說明如何使用重新分割來優化無法平行處理的 Azure 串流分析作業。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75732377"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>使用 Azure 串流分析來使用重新分割來優化處理

本文說明如何使用重新分割來調整 Azure 串流分析查詢，以找出 [無法完全平行](stream-analytics-scale-jobs.md)處理的案例。

[如果有](stream-analytics-parallelization.md)下列情況，您可能無法使用平行化：

* 您不會控制輸入資料流程的分割區索引鍵。
* 您的來源 "噴濺" 輸入跨多個分割區，稍後需要合併。

當您處理的資料流程不是根據自然輸入配置（例如事件中樞的 **PartitionId** ）所分區化的資料流程時，就需要重新分割（或重新輪換）。 當您重新分割時，每個分區都可以獨立處理，讓您以線性方式相應放大串流管線。

## <a name="how-to-repartition"></a>如何重新分割

若要重新分割，請 **在查詢** 中的 **PARTITION BY** 語句後面使用關鍵字。 下列範例會依 **DeviceID** 將資料分割成10個分割區計數。 **DeviceID**的雜湊會用來判斷哪個資料分割應接受哪些子串流。 如果輸出支援資料分割寫入，而且有10個數據分割，則會針對每個分割的資料流程獨立清除資料。

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

下列範例查詢會聯結兩個重新分割資料的資料流程。 聯結兩個重新分割的資料流程時，資料流程必須具有相同的資料分割索引鍵和計數。 結果是具有相同資料分割配置的資料流程。

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

輸出配置應符合資料流程配置索引鍵和計數，讓每個子資料流程可以獨立地排清。 您也可以在排清之前，透過不同的配置重新合併和重新分配資料流程，但您應該避免該方法，因為它會增加處理的一般延遲並增加資源使用率。

## <a name="streaming-units-for-repartitions"></a>會重新分割的串流單位

實驗並觀察您作業的資源使用狀況，以判斷您需要的確切資料分割數目。 您必須根據每個資料分割所需的實體資源，調整 [ (SU) 的串流單位 ](stream-analytics-streaming-unit-consumption.md) 數目。 一般而言，每個分割區都需要六個 su。 如果沒有足夠的資源指派給作業，系統將只會在其受益時套用重新分割。

## <a name="repartitions-for-sql-output"></a>適用于 SQL 輸出的會重新分割

當您的作業使用 SQL database 進行輸出時，請使用明確的重新分割來比對最佳的分割區計數，以將輸送量最大化。 由於 SQL 與八個寫入器的效果最佳，所以在排清之前或在上游的某個位置，可能會讓作業效能受益。 

當輸入資料分割超過 8 個時，繼承輸入資料分割配置可能不會是適當的選擇。 請考慮在查詢中使用 [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) ，以明確指定輸出寫入器的數目。 

下列範例會從輸入中讀取（不論其是否以自然方式進行資料分割），並根據 DeviceID 維度會重新分割資料流程十倍，並清除要輸出的資料。 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。


## <a name="next-steps"></a>後續步驟

* [開始使用 Azure 串流分析](stream-analytics-introduction.md)
* [利用 Azure 串流分析中的查詢平行化作業](stream-analytics-parallelization.md)
