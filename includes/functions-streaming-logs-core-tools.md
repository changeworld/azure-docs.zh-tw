---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164854"
---
#### <a name="built-in-log-streaming"></a>內建記錄資料流

使用 `logstream` 選項，開始接收在 Azure 中執行之特定函數應用程式的串流記錄，如下列範例所示：

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>在 Core Tools 中，尚未針對取用方案中在 Linux 上執行的函式應用程式啟用內建記錄串流。 針對這些主控方案，您必須改為使用即時計量資料流，以近乎即時的方式檢視記錄。

#### <a name="live-metrics-stream"></a>即時計量串流

您可藉由包含 `--browser` 選項，在新的瀏覽器視窗中檢視函式應用程式的[即時計量資料流](../articles/azure-monitor/app/live-stream.md)，如下列範例所示：

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
