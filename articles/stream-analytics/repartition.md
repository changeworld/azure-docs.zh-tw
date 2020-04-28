---
title: 使用重新分割來優化 Azure 串流分析作業
description: 本文說明如何使用重新分割來將無法平行處理的 Azure 串流分析作業優化。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75732377"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>使用 Azure 串流分析進行重新分割以優化處理

本文說明如何使用重新分割來調整您的 Azure 串流分析查詢，以找出[無法完全平行](stream-analytics-scale-jobs.md)處理的案例。

[在下列情況中](stream-analytics-parallelization.md)，您可能無法使用平行化：

* 您不會控制輸入資料流程的分割區索引鍵。
* 您在稍後需要合併多個分割區的來源「噴濺」輸入。

當您處理不是根據自然輸入配置分區化的資料流程（例如事件中樞的**PartitionId** ）時，需要重新分割或重新輪換。 當您重新分割時，每個分區都可以獨立處理，這可讓您以線性方式相應放大您的串流管線。

## <a name="how-to-repartition"></a>如何重新分割

若要重新分割，請在查詢中的**PARTITION BY**語句後面**使用關鍵字。** 下列範例會以**DeviceID**將資料分割為10的分割區計數。 **DeviceID**的雜湊用來判斷哪個分割區應接受哪些子串流。 資料會針對每個分割的資料流程個別排清，假設輸出支援分割的寫入，而且有10個磁碟分割。

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

下列範例查詢會聯結兩個重新分割資料的資料流程。 聯結兩個重新分割資料的資料流程時，資料流程必須具有相同的分割區索引鍵和計數。 結果是具有相同資料分割配置的資料流程。

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

輸出配置應符合資料流程配置索引鍵和計數，以便每個子資料流程可以獨立清除。 在排清之前，資料流程也可以透過不同的配置再次合併和重新分割，但您應該避免該方法，因為它會新增至處理的一般延遲，並增加資源使用率。

## <a name="streaming-units-for-repartitions"></a>會重新分割的串流處理單位

實驗並觀察作業的資源使用量，以判斷您所需的分割區數目。 [串流單位（SU）](stream-analytics-streaming-unit-consumption.md)的數目必須根據每個分割區所需的實體資源來調整。 一般來說，每個分割區都需要6個 su。 如果沒有足夠的資源指派給作業，系統只會在對作業有好處時套用重新分割。

## <a name="repartitions-for-sql-output"></a>適用于 SQL 輸出的會重新分割

當您的作業使用 SQL database 做為輸出時，請使用明確重新分割來比對最佳的磁碟分割計數，以將輸送量最大化。 由於 SQL 最適用于八個寫入器，因此在清除之前將流程重新分割為八個，或進一步上游，可能會使作業效能受益。 

當輸入資料分割超過 8 個時，繼承輸入資料分割配置可能不會是適當的選擇。 請考慮在您的查詢中使用[INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) ，以明確指定輸出寫入器的數目。 

下列範例會從輸入讀取（不論其是否為自然分割），並根據 DeviceID 維度會重新分割資料流程十倍，並將資料排清到輸出。 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。


## <a name="next-steps"></a>後續步驟

* [開始使用 Azure 串流分析](stream-analytics-introduction.md)
* [利用 Azure 串流分析中的查詢平行化作業](stream-analytics-parallelization.md)
