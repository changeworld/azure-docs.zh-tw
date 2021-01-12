---
title: Azure Data Factory 中的系統變數
description: 本文描述 Azure Data Factory 支援的系統變數。 定義 Data Factory 實體時，可以在運算式中使用這些變數。
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: fc6b2e4c944394d811abc19f70aeb34a0ae3c9a4
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127663"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory 支援的系統變數
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文描述 Azure Data Factory 支援的系統變數。 定義 Data Factory 實體時，可以在運算式中使用這些變數。

## <a name="pipeline-scope"></a>管線範圍
您可以在管線 JSON 中的任何位置參考這些系統變數。

| 變數名稱 | 描述 |
| --- | --- |
| @pipeline().DataFactory |執行管線的資料處理站名稱 |
| @pipeline().Pipeline |管線的名稱 |
| @pipeline().RunId |特定管線執行的識別碼 |
| @pipeline().TriggerType |叫用管線的觸發程式類型 (例如， `ScheduleTrigger` `BlobEventsTrigger`) 。 如需支援的觸發程式類型清單，請參閱 [Azure Data Factory 中的管線執行和觸發](concepts-pipeline-execution-triggers.md)程式。 的觸發程式類型 `Manual` 表示已手動觸發管線。 |
| @pipeline().TriggerId|叫用管線之觸發程式的識別碼 |
| @pipeline().TriggerName|叫用管線之觸發程式的名稱 |
| @pipeline().TriggerTime|叫用管線之觸發程式執行的時間。 這是觸發程式 **實際** 引發來叫用管線執行的時間，與觸發程式的排程時間可能稍有不同。  |

>[!NOTE]
>觸發程式相關的日期/時間系統變數 (在管線和觸發程式範圍中) 會傳回 ISO 8601 格式的 UTC 日期，例如， `2017-06-01T22:20:00.4061448Z` 。

## <a name="schedule-trigger-scope"></a>排程觸發程式範圍
您可以在 [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger)類型之觸發程式的觸發程式 JSON 中的任何位置參考這些系統變數。

| 變數名稱 | 描述 |
| --- | --- |
| @trigger().scheduledTime |排定觸發程式叫用管線執行的時間。 |
| @trigger().startTime |**實際** 引發觸發程式以叫用管線執行的時間。 這可能與觸發程式的排程時間稍有不同。 |

## <a name="tumbling-window-trigger-scope"></a>輪轉視窗觸發程式範圍
您可以在 [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger)類型之觸發程式的觸發程式 JSON 中的任何位置參考這些系統變數。

| 變數名稱 | 描述 |
| --- | --- |
| @trigger().outputs.windowStartTime |與觸發程式執行相關聯之視窗的起點。 |
| @trigger().outputs.windowEndTime |與觸發程式執行相關聯的視窗結尾。 |
| @trigger().scheduledTime |排定觸發程式叫用管線執行的時間。 |
| @trigger().startTime |**實際** 引發觸發程式以叫用管線執行的時間。 這可能與觸發程式的排程時間稍有不同。 |

## <a name="event-based-trigger-scope"></a>以事件為基礎的觸發程式範圍
您可以在 [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger)類型之觸發程式的觸發程式 JSON 中的任何位置參考這些系統變數。

| 變數名稱 | 描述 |
| --- | --- |
| @triggerBody ( # A1. 檔案名  |建立或刪除造成觸發程式引發的檔案名。   |
| @triggerBody ( # A1. 資料夾資料夾  |包含所指定檔案之資料夾的路徑 `@triggerBody().fileName` 。 資料夾路徑的第一個區段是 Azure Blob 儲存體容器的名稱。  |
| @trigger().startTime |引發觸發程式以叫用管線執行的時間。 |

## <a name="next-steps"></a>後續步驟
如需如何在運算式中使用這些變數的詳細資訊，請參閱[運算式語言和函數](control-flow-expression-language-functions.md)。
