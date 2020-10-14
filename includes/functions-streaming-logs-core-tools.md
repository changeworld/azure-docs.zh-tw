---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "68881355"
---
#### <a name="built-in-log-streaming"></a>內建記錄資料流

使用 `logstream` 選項，開始接收在 Azure 中執行之特定函數應用程式的串流記錄，如下列範例所示：

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>即時計量串流

您也可藉由包含 `--browser` 選項，在新的瀏覽器視窗中檢視函式應用程式的[即時計量資料流](../articles/azure-monitor/app/live-stream.md)，如下列範例所示：

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
