---
title: 如何使用 Redis 的 Azure 緩存使用 Redis-cli
description: 瞭解如何使用*Redis-cli.exe*作為命令列工具,以便作為用戶端與 Redis 的 Azure 緩存進行交互。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: bd2da798cae92a7e47bd879b69dd108618463402
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010761"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>如何搭配使用 Redis 命令列工具與 Azure Redis 快取

*redis-cli.exe* 是一個能以用戶端形式與「Azure Redis 快取」進行互動的常用命令列工具。 此工具也可供與「Azure Redis 快取」搭配使用。

如果使用 Windows 平台，可下載[適用於 Windows 的 Redis 命令列工具](https://github.com/MSOpenTech/redis/releases/)使用此工具。 

如果要在另一個平臺上運行命令列工具,請從[https://redis.io/download](https://redis.io/download)下載 Azure 緩存以進行 Redis。

## <a name="gather-cache-access-information"></a>收集快取存取資訊

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以使用三種方法收集存取快取所需的資訊：

1. 在 Azure CLI 使用[az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. 在 Azure PowerShell 使用 [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. 使用 Azure 入口網站。

在本節中，您將從 Azure 入口網站來擷取金鑰。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>啟用 redis-cli.exe 的存取

使用 Redis 的 Azure 緩存時,默認情況下僅啟用 TLS 埠 (6380)。 命令`redis-cli.exe`列工具不支援 TLS。 您有兩個設定選項可以使用它：

1. [啟用非 TLS 連接埠 (6379)](cache-configure.md#access-ports) - **不建議使用此設定**,因為在此設定中,存取金鑰透過 TCP 以明文形式發送。 此變更可能會危害您的快取存取。 只有當您只是要存取測試快取時，才會考慮此設定。

2. 下載並安裝 [stunnel](https://www.stunnel.org/downloads.html)。

    執行 **stunnel GUI Start** 啟動伺服器。

    以滑鼠右鍵按一下 stunnel 伺服器的工作列圖示，然後按一下 [Show Log Window \(顯示記錄視窗\)]****。

    在隧道紀錄視窗功能表上,按下 **「設定** > **編輯設定**」以打開當前設定檔。

    在 [Service definitions \(服務定義\)]**** 區段下加入 *redis-cli.exe* 的下列項目。 取代 `yourcachename`，插入您實際的快取名稱。 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    儲存並關閉設定檔。 
  
    在隧道日誌視窗功能表上,按一下「**設定** > **重新載入配置**」。


## <a name="connect-using-the-redis-command-line-tool"></a>使用 Redis 命令列工具連線。

使用 stunnel 時，執行 *redis-cli.exe*，只傳遞您的「連接埠」** 和「存取金鑰」**(主要或次要) 以連線到快取。

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![搭配使用 stunnel 與 redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

如果使用具有**不安全**非 TLS 連接埠的測試`redis-cli.exe`快取,請執行並傳遞*主機名*、*連接**金鑰*(主或輔助)以連接到測試緩存。

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![搭配使用 stunnel 與 redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>後續步驟

深入了解如何使用 [Redis 主控台](cache-configure.md#redis-console)發出命令。

