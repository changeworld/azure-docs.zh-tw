---
title: Azure 串流分析中的使用者定義函數
description: 本文概述 Azure 串流分析中的使用者定義函數。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: d167c603ada885a1a4917c66bab110e4ce38cab4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82598363"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure 串流分析中的使用者定義函數

Azure 串流分析中類似 SQL 的查詢語言，可讓您輕鬆地對串流資料執行即時分析邏輯。 串流分析透過在查詢中叫用的自訂函式，提供額外的彈性。 下列程式碼範例是一個名為 `sampleFunction` 的 UDF，它會接受一個參數、該工作接收的每個輸入記錄，並將結果當做寫入輸出 `sampleResult` 。

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>函式類型

Azure 串流分析支援下列四種函數類型： 

* JavaScript 使用者定義函式 
* JavaScript 使用者定義彙總 
* C # 使用者定義函數（使用 Visual Studio） 
* Azure Machine Learning 

您可以在使用機器學習模型、字串操作、複雜的數學計算、編碼和解碼資料等案例中使用這些功能。 

## <a name="limitations"></a>限制

使用者定義函數是無狀態的，而且傳回值只能是純量值。 您無法從這些使用者定義的函式呼叫外部 REST 端點，因為這可能會影響作業的效能。 

Azure 串流分析不會保留所有函式呼叫和傳回結果的記錄。 為保證可重複性-例如，從較舊的時間戳記重新執行您的作業會再次產生相同的結果-請勿使用如 `Date.GetData()` 或之類 `Math.random()` 的函數，因為這些函式不會針對每個調用傳回相同的結果。  

## <a name="resource-logs"></a>資源記錄

任何執行階段錯誤都會被視為嚴重的，而且會透過活動和資源記錄來呈現。 建議您的函式處理所有例外狀況和錯誤，並將有效的結果傳回給您的查詢。 這會導致您的作業[無法進入失敗狀態](job-states.md)。  

## <a name="exception-handling"></a>例外狀況處理

使用 Azure 串流分析中的資料時，資料處理期間的任何例外狀況都會被視為災難性的失敗。 使用者定義函式有更高的可能性，可以擲回例外狀況並導致處理停止。 若要避免這個問題，請在 JavaScript 或 c # 中使用*try-catch*區塊，以在程式碼執行期間攔截例外狀況。 可以記錄和處理攔截到的例外狀況，而不會造成系統失敗。 建議您一律將自訂程式碼包裝在*try-catch*區塊中，以避免在處理引擎中擲回非預期的例外狀況。

## <a name="next-steps"></a>後續步驟

* [Azure 串流分析中的 JavaScript 使用者定義函式](stream-analytics-javascript-user-defined-functions.md)
* [Azure 串流分析 JavaScript 使用者定義彙總](stream-analytics-javascript-user-defined-aggregates.md)
* [為 Azure 串流分析作業開發 .NET Standard 的使用者定義函數](stream-analytics-edge-csharp-udf-methods.md)
* [整合 Azure 串流分析與 Azure Machine Learning](machine-learning-udf.md)
