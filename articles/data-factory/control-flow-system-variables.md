---
title: Azure Data Factory 中的系統變數
description: 本文描述 Azure Data Factory 支援的系統變數。 定義 Data Factory 實體時，可以在運算式中使用這些變數。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 2690ded0ac45719cb1082c85ab535c91ad491172
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81417958"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory 支援的系統變數
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文描述 Azure Data Factory 支援的系統變數。 定義 Data Factory 實體時，可以在運算式中使用這些變數。

## <a name="pipeline-scope"></a>管線範圍
您可以在管線 JSON 中的任何位置參考這些系統變數。

| 變數名稱 | 描述 |
| --- | --- |
| @pipeline().DataFactory |管線執行在其中執行的資料處理站名稱 |
| @pipeline().Pipeline |管線的名稱 |
| @pipeline().RunId | 特定管線執行的識別碼 |
| @pipeline().TriggerType | 叫用管線的觸發程序類型 (手動、排程器) |
| @pipeline().TriggerId| 叫用管線之觸發程序的識別碼 |
| @pipeline().TriggerName| 叫用管線之觸發程序的名稱 |
| @pipeline().TriggerTime| 叫用管線之觸發程序的時間。 觸發程序時間是實際的觸發時間，而非排定的時間。 例如，會傳回 `13:20:08.0149599Z` 而非 `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>排程觸發程序範圍
如果觸發程序的類型為："ScheduleTrigger"，則可以在管線 JSON 中的任何位置參考這些系統變數。

| 變數名稱 | 描述 |
| --- | --- |
| @trigger().scheduledTime |排定觸發程序叫用管線執行的時間。 例如，對於每隔 5 分鐘就觸發的觸發程序，此變數分別會傳回 `2017-06-01T22:20:00Z`、`2017-06-01T22:25:00Z` 和 `2017-06-01T22:30:00Z`。|
| @trigger().startTime |觸發程序「實際」**** 觸發以叫用管線執行的時間。 例如，對於每隔 5 分鐘就觸發的觸發程序，此變數可能會傳回分別像是 `2017-06-01T22:20:00.4061448Z`、`2017-06-01T22:25:00.7958577Z` 和 `2017-06-01T22:30:00.9935483Z` 的項目。 （注意：時間戳記預設為 ISO 8601 格式）|

## <a name="tumbling-window-trigger-scope"></a>輪轉視窗觸發程序範圍
如果觸發程序的類型為："TumblingWindowTrigger"，則可以在管線 JSON 中的任何位置參考這些系統變數。
（注意：時間戳記預設為 ISO 8601 格式）

| 變數名稱 | 描述 |
| --- | --- |
| @trigger().outputs.windowStartTime |排定觸發程序叫用管線執行的開始時間範圍。 如果輪轉視窗觸發程序的頻率為「每小時」，則這可能是一小時開頭的時間。|
| @trigger().outputs.windowEndTime |排定觸發程序叫用管線執行的結束時間範圍。 如果輪轉視窗觸發程序的頻率為「每小時」，則這可能是一小時結束的時間。|
## <a name="next-steps"></a>後續步驟
如需如何在運算式中使用這些變數的詳細資訊，請參閱[運算式語言和函數](control-flow-expression-language-functions.md)。
