---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165648"
---
CRON 運算式使用的預設時區是國際標準時間 (UTC)。 若要讓 CRON 運算式以另一個時區為基礎，請為名為 `WEBSITE_TIME_ZONE` 的函式應用程式建立應用程式設定。 

此設定的值取決於您的函數應用程式執行所在的作業系統和計畫。

|作業系統 |計畫 |值 |
|-|-|-|
| **Windows** |全部 | 將值設定為所需的時區名稱，如 [Microsoft 時區索引] (所示 https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) 。 |
| **Linux** |進階<br/>專用 |將值設定為所需的時區名稱，如[tz 資料庫](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)中所示。 |

> [!NOTE]
> `WEBSITE_TIME_ZONE`Linux 使用量計畫目前不支援。

例如，*東部標準時間* (Windows) 或*北美洲/New_York* (Linux) 是 UTC-05:00。 若要讓計時器觸發程式在每天的上午10:00 觸發，請使用下列 NCRONTAB 運算式來表示 UTC 時區：

```
"0 0 15 * * *"
``` 

或者，為您的函數應用程式建立名為的應用程式設定 `WEBSITE_TIME_ZONE` ，將值設定為 `Eastern Standard Time` (Windows) 或 `America/New_York` (Linux) ，然後使用下列 NCRONTAB 運算式： 

```
"0 0 10 * * *"
``` 

當您使用 `WEBSITE_TIME_ZONE` 時，時間會隨特定時區的時間變更 (例如日光節約時間) 而調整。 
