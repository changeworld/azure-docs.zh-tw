---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173737"
---
自訂度量的集合。 使用此集合以報告與遙測項目相關聯的具名度量。 一般使用案例如下：
- 相依性遙測承載大小
- 要求遙測所處理的佇列項目數
- 客戶為完成「精靈步驟完成事件遙測」中的步驟所使用的時間。

您可以在應用程式分析中查詢[自訂度量](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H)︰

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > 自訂度量會與其所屬的遙測項目相關聯。 系統會使用包含這些自訂度量的遙測項目來進行取樣。 若要追蹤其值獨立於其他遙測類型的度量，請使用[計量遙測](../articles/azure-monitor/app/api-custom-events-metrics.md)。

最大金鑰長度︰150
