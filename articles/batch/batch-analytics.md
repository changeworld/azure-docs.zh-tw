---
title: Azure Batch 分析
description: 批次分析中的主題包含批次服務資源所適用事件和警示的參考資訊。
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849506"
---
# <a name="batch-analytics"></a>批次分析

本章節中的主題包含適用于 Batch 服務資源的事件和警示的參考資訊。

請參閱[記錄事件以便對 Batch 解決方案進行診斷評估和監視](./batch-diagnostics.md)，以取得啟用與取用批次診斷記錄的詳細資訊。

## <a name="diagnostic-logs"></a>診斷記錄

Azure 批次服務會在某些批次資源的存留期間發出下列診斷記錄事件。

### <a name="service-log-events"></a>服務記錄檔事件

- [建立集區](batch-pool-create-event.md)
- [開始刪除集區](batch-pool-delete-start-event.md)
- [完成集區刪除](batch-pool-delete-complete-event.md)
- [開始調整集區大小](batch-pool-resize-start-event.md)
- [完成集區大小調整](batch-pool-resize-complete-event.md)
- [集區自動調整](batch-pool-autoscale-event.md)
- [開始工作](batch-task-start-event.md)
- [完成工作](batch-task-complete-event.md)
- [工作失敗](batch-task-fail-event.md)
- [工作排程失敗](batch-task-schedule-fail-event.md)
