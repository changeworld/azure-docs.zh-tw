---
ms.openlocfilehash: dba7a3cc7a68d360fd6e56511b71ae364f624646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569268"
---
CRON 運算式使用的預設時區是國際標準時間 (UTC)。 若要讓 CRON 運算式以另一個時區為基礎，請為名為 `WEBSITE_TIME_ZONE` 的函式應用程式建立應用程式設定。 

這項設定的值取決於您的函數應用程式執行所在的作業系統和方案。

|作業系統 |計畫 |值 |
|-|-|-|
| **Windows** |全部 | 將值設定為所需的時區名稱，如 [Microsoft 時區索引](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10))中所示。 |
| **Linux** |Premium<br/>專用 |將值設定為所需的時區名稱，如 [tz 資料庫](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)所示。 |

> [!NOTE]
> `WEBSITE_TIME_ZONE` Linux 使用量方案目前不支援。

例如， *東部標準時間* (Windows) 或 *北美洲/New_York* (Linux) 為 UTC-05:00。 若要讓您的計時器觸發程式在每天上午10:00 執行，請使用下列 NCRONTAB 運算式，該運算式會使用 UTC 時區帳戶：

```
"0 0 15 * * *"
``` 

或者，為您的函數應用程式建立應用程式設定 `WEBSITE_TIME_ZONE` ，並將值設定為 `Eastern Standard Time` (Windows) 或 `America/New_York` (Linux) ，然後使用下列 NCRONTAB 運算式： 

```
"0 0 10 * * *"
``` 

當您使用 `WEBSITE_TIME_ZONE` 時，時間會隨特定時區的時間變更 (例如日光節約時間) 而調整。 
