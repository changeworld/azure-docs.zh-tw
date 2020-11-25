---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028407"
---
CRON 運算式使用的預設時區是國際標準時間 (UTC)。 若要讓 CRON 運算式以另一個時區為基礎，請為名為 `WEBSITE_TIME_ZONE` 的函式應用程式建立應用程式設定。 

這項設定的值取決於您的函數應用程式執行所在的作業系統和方案。

|作業系統 |規劃 |值 |
|-|-|-|
| **Windows** |全部 | 將值設定為所需的時區名稱，如 Windows 命令所指定的每個配對的第二行所指定 `tzutil.exe /L` |
| **Linux** |Premium<br/>專用 |將值設定為所需的時區名稱，如 [tz 資料庫](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)所示。 |

> [!NOTE]
> `WEBSITE_TIME_ZONE` Linux 使用量方案目前不支援。

例如，美國東部時間 (`Eastern Standard Time` (Windows) 或 `America/New_York` (Linux) # A5 目前在標準時間和 utc-04:00 期間使用 utc-05:00 （日光節約時間）。 若要讓計時器觸發程式每天上午10:00 引發，請建立名為的函式應用程式的應用程式設定 `WEBSITE_TIME_ZONE` 、將值設定為 `Eastern Standard Time` (Windows) 或 `America/New_York` (Linux) ，然後使用下列 NCRONTAB 運算式： 

```
"0 0 10 * * *"
``` 

當您使用 `WEBSITE_TIME_ZONE` 時間時，會針對特定時區的時間變更進行調整，包括日光節約時間以及標準時間的變更。
