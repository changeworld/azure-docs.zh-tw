---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881355"
---
#### <a name="built-in-log-streaming"></a>內建記錄資料流程

使用`logstream`選項來開始接收在 Azure 中執行之特定函式應用程式的串流記錄，如下列範例所示：

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>即時計量串流

您也可以藉由[Live Metrics Stream](../articles/azure-monitor/app/live-stream.md)包含`--browser`選項，在新的瀏覽器視窗中查看函數應用程式的即時計量資料流，如下列範例所示：

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
