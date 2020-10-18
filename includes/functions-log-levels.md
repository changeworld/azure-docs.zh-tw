---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164897"
---
*記錄層級*會指派給每個記錄檔。 值是表示相對重要性的整數：

|LogLevel    |程式碼| 描述 |
|------------|---|--------------|
|追蹤       | 0 |包含最詳細訊息的記錄。 這些訊息可能包含敏感性應用程式資料。 這些訊息預設會停用，且永遠不應在生產環境中啟用。|
|偵錯       | 1 | 開發期間用於互動式調查的記錄。 這些記錄主要應包含適用於偵錯的資訊，且不具備任何長期值。 |
|資訊 | 2 | 追蹤應用程式一般流程的記錄。 這些記錄應具備長期值。 |
|警告     | 3 | 在應用程式流程中醒目提示異常或未預期事件的記錄，但不會導致應用程式停止執行。 |
|錯誤       | 4 | 當目前執行中的流程因為失敗而停止時，會反白顯示的記錄。 這些錯誤應該指出目前活動中的失敗，而不是整個應用程式的失敗。 |
|重大    | 5 | 描述無法復原的應用程式或系統損毀，或需要立即注意重大失敗的記錄。 |
|None        | 6 | 停用指定分類的記錄。 |

檔案設定 [* 上的host.js* ](../articles/azure-functions/functions-host-json.md) 會決定函式應用程式傳送到 Application Insights 的記錄量。  
