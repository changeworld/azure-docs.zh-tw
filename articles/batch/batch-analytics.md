---
title: Azure Batch 分析
description: 批次分析中的主題包含批次服務資源所適用事件和警示的參考資訊。
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 9d0e5c90c7bade82d76c7dbdfd4276e9671c961c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958101"
---
# <a name="batch-analytics"></a>批次分析
批次分析中的主題包含批次服務資源所適用事件和警示的參考資訊。

請參閱[記錄事件以便對 Batch 解決方案進行診斷評估和監視](./batch-diagnostics.md)，以取得啟用與取用批次診斷記錄的詳細資訊。

## <a name="diagnostic-logs"></a>診斷記錄

Azure 批次服務會在某些批次資源的存留期間發出下列診斷記錄事件。

**服務記錄檔事件**
* [建立集區](batch-pool-create-event.md)
* [開始刪除集區](batch-pool-delete-start-event.md)
* [完成集區刪除](batch-pool-delete-complete-event.md)
* [開始調整集區大小](batch-pool-resize-start-event.md)
* [完成集區大小調整](batch-pool-resize-complete-event.md)
* [開始工作](batch-task-start-event.md)
* [完成工作](batch-task-complete-event.md)
* [工作失敗](batch-task-fail-event.md)
