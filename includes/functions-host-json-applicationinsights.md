---
title: 包含檔案
description: 包含檔案
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a925076dfccd30c73febb2aadc8692667ea01525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279559"
---
控制 [Application Insights 中的取樣功能](../articles/azure-functions/functions-monitoring.md#configure-sampling)。

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|isEnabled|true|啟用或停用取樣。| 
|maxTelemetryItemsPerSecond|5|取樣的開始臨界值。| 
