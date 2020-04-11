---
title: Azure 串流分析使用者定義的函數
description: 本文概述了 Azure 流分析中使用者定義的函數。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115582"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure 串流分析使用者定義的函數

Azure 串流分析中類似 SQL 的查詢語言便於在流資料上實現即時分析邏輯。 流分析通過查詢中調用的自定義函數提供了額外的靈活性。 以下代碼範例是一個 UDF,它`sampleFunction`接受一個參數,每個輸入紀錄作業接收,結果寫入輸出`sampleResult`為 。

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>函式類型

Azure 串流分析支援以下四種功能型態: 

* JavaScript 使用者定義函式 
* JavaScript 使用者定義彙總 
* C# 使用者定義的功能(使用視覺化工作室) 
* Azure Machine Learning 

您可以將這些函數用於使用機器學習模型、字串操作、複雜數學計算、編碼和解碼數據等方案。 

## <a name="limitations"></a>限制

使用者定義的函數是無狀態的,返回值只能是標量值。 您不能從這些使用者定義的函數向外部 REST 終結點調用,因為它可能會影響作業的性能。 

Azure 流分析不會記錄所有函數調用和返回的結果。 為了保證可重複性(例如,從較舊的時間戳重新運行作業)將再次生成相同的結果,請不要使用或`Date.GetData()``Math.random()`等函數,因為這些函數不會為每個調用返回相同的結果。  

## <a name="diagnostic-logs"></a>診斷記錄

任何運行時錯誤都被視為致命錯誤,並通過活動和診斷日誌顯示。 建議函數處理所有異常和錯誤,並將有效結果返回給查詢。 這會阻止您的工作進入[失敗狀態](job-states.md)。  


## <a name="next-steps"></a>後續步驟

* [Azure 串流分析中的 JavaScript 使用者定義函式](stream-analytics-javascript-user-defined-functions.md)
* [Azure 串流分析 JavaScript 使用者定義的聚合](stream-analytics-javascript-user-defined-aggregates.md)
* [為 Azure 串流分析作業開發 .NET 標準使用者定義的函數](stream-analytics-edge-csharp-udf-methods.md)
* [將 Azure 串流分析與 Azure 機器學習整合](machine-learning-udf.md)

