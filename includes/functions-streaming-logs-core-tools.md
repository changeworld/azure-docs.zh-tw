---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881355"
---
#### <a name="built-in-log-streaming"></a>內置日誌流

使用`logstream`選項開始接收在 Azure 中運行的特定函數應用的流式處理日誌，如以下示例所示：

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>即時計量串流

您還可以通過包含`--browser`選項在新的瀏覽器視窗中查看函數應用[的即時指標流](../articles/azure-monitor/app/live-stream.md)，如以下示例所示：

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
