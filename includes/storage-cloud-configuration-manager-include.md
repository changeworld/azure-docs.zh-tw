---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "72038159"
---
[適用於 .NET 的 Microsoft Azure Configuration Manager 程式庫](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 會提供一個類別，可用來剖析組態檔中的連接字串。 [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx)類別會剖析設定。 它會剖析在桌上型電腦、行動裝置、Azure 虛擬機器或 Azure 雲端服務中執行的用戶端應用程式的設定。

若要參考 `CloudConfigurationManager` 封裝，請新增下列指示詞 `using` ：

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

以下範例示範如何從組態檔擷取連接字串：

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

是否使用 Azure Configuration Manager 可由您選擇。 您也可以使用 API，例如 .NET Framework 的 [ConfigurationManager 類別](/dotnet/api/system.configuration.configurationmanager)。
