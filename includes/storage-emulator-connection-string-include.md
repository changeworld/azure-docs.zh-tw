---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582484"
---
模擬器支援單一固定帳戶及已知的驗證金鑰來進行共用金鑰驗證。 此帳戶和金鑰是唯一允許與模擬器搭配使用的共用金鑰認證。 這些包括：

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> 模擬器所支援的驗證金鑰僅適用于測試用戶端驗證碼的功能。 它不提供任何安全性用途。 您無法將生產儲存體帳戶和金鑰與模擬器搭配使用。 您不應該將開發帳戶與生產資料搭配使用。
> 
> 模擬器僅支援透過 HTTP 連接。 不過，HTTPS 是建議的通訊協定，用於存取生產 Azure 儲存體帳戶中的資源。
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>使用捷徑連接到模擬器帳戶
從應用程式連接到模擬器最簡單的方式，就是在應用程式佈建檔中設定參考快捷方式的連接字串 `UseDevelopmentStorage=true` 。 以下是 *app.config* 檔案中模擬器的連接字串範例： 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

相當於完整指定帳戶名稱、帳戶金鑰，以及您想要在連接字串中使用之每個模擬器服務的端點。 這是必要的，這樣一來連接字串將會參考模擬器端點 (不同於正式作業儲存體帳戶的模擬器端點)。 例如，連接字串的值看起來會像這樣：

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
