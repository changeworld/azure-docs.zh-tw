---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: 85cfe3b062d7d9ef3a7bdcf29ef7d2125f8f3ae4
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812776"
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

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>使用快捷方式連接到模擬器帳戶

從應用程式連接到模擬器最簡單的方式，就是在應用程式佈建檔中設定參考快捷方式的連接字串 `UseDevelopmentStorage=true` 。 此快捷方式相當於模擬器的完整連接字串，它會指定每個 Azure 儲存體服務的帳戶名稱、帳戶金鑰和模擬器端點：

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

下列 .NET 程式碼片段會示範如何從採用連接字串的方法使用快捷方式。 例如， [BlobContainerClient (字串，string) ](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) 的函式會採用連接字串。

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

在呼叫程式碼片段中的程式碼之前，請先確定模擬器正在執行中。
