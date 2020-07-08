---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "68881355"
---
#### <a name="built-in-log-streaming"></a>內建記錄資料流

使用 `logstream` 選項來開始接收在 Azure 中執行之特定函式應用程式的串流記錄，如下列範例所示：

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>即時計量串流

您也可以藉由包含選項，在新的瀏覽器視窗中查看函數應用程式的[即時計量資料流](../articles/azure-monitor/app/live-stream.md) `--browser` ，如下列範例所示：

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
