---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72038159"
---
[適用於 .NET 的 Microsoft Azure Configuration Manager 程式庫](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 會提供一個類別，可用來剖析組態檔中的連接字串。 [雲組態管理員](https://msdn.microsoft.com/library/azure/mt634650.aspx)類解析配置設置。 它分析在桌面、行動裝置、Azure 虛擬機器或 Azure 雲服務中運行的用戶端應用程式的設置。

要引用包`CloudConfigurationManager`，添加以下`using`指令：

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

是否使用 Azure Configuration Manager 可由您選擇。 您還可以使用 API，如 .NET 框架的[組態管理員類](/dotnet/api/system.configuration.configurationmanager)。
