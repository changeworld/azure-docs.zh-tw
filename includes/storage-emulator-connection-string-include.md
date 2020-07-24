---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070561"
---
Azurite 支援單一固定帳戶及已知的驗證金鑰，以進行共用金鑰驗證。 此帳戶和金鑰是唯一允許與 Azurite 搭配使用的共用金鑰認證。 其中包括：

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Azurite 支援的驗證金鑰僅適用于測試用戶端驗證程式代碼的功能。 它不提供任何安全性用途。 您不能搭配 Azurite 使用您的生產儲存體帳戶和金鑰。 您不應該將開發帳戶與生產資料搭配使用。
> 
> Azurite 僅支援透過 HTTP 連接。 不過，HTTPS 是建議的通訊協定，用於存取生產 Azure 儲存體帳戶中的資源。
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>使用捷徑連接到模擬器帳戶
從您的應用程式連線到 Azurite 最簡單的方式，就是在應用程式的設定檔中設定參考快捷方式的連接字串 `UseDevelopmentStorage=true` 。 以下是在*app.config*檔案中 Azurite 的連接字串範例： 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>使用已知的帳戶名稱和金鑰連接到模擬器帳戶
若要建立參考模擬器帳戶名稱與金鑰的連接字串，您必須針對每一個您希望從連接字串中的模擬器使用的服務指定端點。 這是必要的，這樣一來連接字串將會參考模擬器端點 (不同於正式作業儲存體帳戶的模擬器端點)。 例如，連接字串的值看起來會像這樣：

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

此值等同於上面顯示的捷徑 `UseDevelopmentStorage=true`。
