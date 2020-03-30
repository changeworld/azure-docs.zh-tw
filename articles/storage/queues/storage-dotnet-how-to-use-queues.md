---
title: 使用 .NET - Azure 存儲開始使用 Azure 佇列存儲
description: Azure 佇列可在應用程式元件之間提供可靠的非同步傳訊。 雲端傳訊可讓您的應用程式元件獨立擴充。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 0806c1101c0bc93a1b917cb2d18709721ff0c6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75968279"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>以 .NET 開始使用 Azure 佇列儲存體

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>總覽

Azure 佇列儲存體可提供應用程式元件之間的雲端傳訊。 設計擴充性的應用程式時，會經常分離應用程式元件，以便進行個別擴充。 佇列儲存體可針對應用程式元件間的通訊，提供非同步傳訊，無論應用程式元件是在雲端、桌面、內部部署伺服器或行動裝置上執行。 佇列儲存體也支援管理非同步工作並建置處理工作流程。

### <a name="about-this-tutorial"></a>關於本教學課程

本教學課程說明如何使用 Azure 佇列儲存體撰寫一些常見案例的 .NET 程式碼。 本文說明的案例包括建立和刪除佇列，以及新增、讀取和刪除佇列訊息。

**預估完成時間：** 45 分鐘

### <a name="prerequisites"></a>Prerequisites

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure 存儲 .NET 的常見用戶端庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [.NET 的 Azure 存儲佇列用戶端庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [適用於.NET 的 Azure 設定管理員](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* [Azure 存儲帳戶](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>設定開發環境

接下來，在 Visual Studio 中設定您的開發環境，以便您開始嘗試本指南中的程式碼範例。

### <a name="create-a-windows-console-application-project"></a>建立 Windows 主控台應用程式專案

在 Visual Studio 中，建立新的 Windows 主控台應用程式。 下列步驟說明如何在 Visual Studio 2019 中建立主控台應用程式。 這些步驟類似其他 Visual Studio 版本中的步驟。

1. 選擇**檔** > **新專案** >  ** **
2. 選擇**平臺** > **視窗**
3. 選取 **主控台應用程式 (.NET Framework)**
4. 選擇 **"下一步"**
5. 在 **"專案名稱**"欄位中，輸入應用程式的名稱
6. 選擇 **"創建"**

本教程中的所有代碼示例都可以添加到主控台應用程式**Program.cs**檔的**Main（）** 方法中。

可以在任何類型的 .NET 應用程式中使用 Azure 存儲用戶端庫，包括 Azure 雲服務或 Web 應用以及桌面和移動應用程式。 在本指南中，為求簡化，我們會使用主控台應用程式。

### <a name="use-nuget-to-install-the-required-packages"></a>使用 NuGet 來安裝必要的封裝

您需要參考專案中的以下三個包才能完成本教程：

* [Microsoft Azure 存儲通用用戶端庫為 .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)：此包提供對存儲帳戶中的資料資源的程式設計訪問。
* [Microsoft Azure 存儲佇列庫為 .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)：此用戶端庫啟用使用 Microsoft Azure 存儲佇列服務來存儲用戶端可能訪問的消息。
* [適用於 .NET 的 Microsoft Azure Configuration Manager 程式庫](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)︰此套件提供一個類別，無論您的應用程式於何處執行，均可用來剖析組態檔中的連接字串。

您可以使用 NuGet 獲取這些包。 請遵循下列步驟：

1. 在**解決方案資源管理器**中按右鍵您的專案，然後選擇 **"管理 NuGet 包**"。
2. 選取 [瀏覽]****
3. 連線搜索"Microsoft.Azure.存儲.佇列"，然後選擇 **"安裝**"以安裝存儲用戶端庫及其依賴項。 這還將安裝 Microsoft.Azure.Storage.公共庫，它是佇列庫的依賴項。
4. 連線搜索"Microsoft.Azure.組態管理員"，然後選擇 **"安裝**"以安裝 Azure 組態管理員。

> [!NOTE]
> 存儲用戶端庫包也包含在[.NET](https://azure.microsoft.com/downloads/)的 Azure SDK 中。 但是，我們建議您也安裝 NuGet 的存儲用戶端庫，以確保始終具有最新版本。
>
> .NET 的存儲用戶端庫中的 ODataLib 依賴項由 NuGet 上可用的 ODataLib 包解析，而不是從 WCF 資料服務解決。 您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。 存儲用戶端庫使用的特定 ODataLib 包是[OData、Edm](https://nuget.org/packages/Microsoft.Data.OData/)和[Spatial](https://nuget.org/packages/System.Spatial/)。 [Edm](https://nuget.org/packages/Microsoft.Data.Edm/) 雖然 Azure 表存儲類使用這些庫，但它們是使用存儲用戶端庫程式設計所必需的依賴項。

### <a name="determine-your-target-environment"></a>決定您的目標環境

有兩個環境選項可供您執行本指南中的範例︰

* 您可以對雲端中的 Azure 儲存體帳戶執行您的程式碼。
* 您可以對 Azure 儲存體模擬器執行您的程式碼。 儲存體模擬器是模擬雲端中 Azure 儲存體帳戶的本機環境。 模擬器是一個免費選項，在開發您的應用程式時可用於測試和偵錯您的程式碼。 模擬器會使用已知的帳戶和金鑰。 有關詳細資訊，請參閱使用[Azure 存儲模擬器進行開發和測試](../common/storage-use-emulator.md)。

如果您要選擇以雲端中的儲存體帳戶為目標，請從 Azure 入口網站複製您的儲存體帳戶的主要存取金鑰。 如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../common/storage-account-keys-manage.md)。

> [!NOTE]
> 您可以選擇以儲存體模擬器為目標，以避免產生與 Azure 儲存體相關聯的任何費用。 不過，如果您選擇以雲端中的 Azure 儲存體帳戶為目標，則執行本教學課程的費用可以忽略不計。

### <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

Azure 存儲用戶端庫用於 .NET 支援，使用存儲連接字串配置終結點和憑據以訪問存儲服務。 在組態檔中維護儲存體連接字串是最佳方式。

如需有關連接字串的詳細資訊，請參閱[設定 Azure 儲存體的連接字串](../common/storage-configure-connection-string.md)。

> [!NOTE]
> 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 請務必小心保護您的儲存體帳戶金鑰。 請避免轉發給其他使用者、進行硬式編碼，或將它儲存在其他人可以存取的純文字檔案。 如果您認為金鑰可能遭到破解，請使用 Azure 入口網站重新產生金鑰。

要配置連接字串，請從視覺化工作室中的解決方案資源管理器打開**app.config**檔。 添加如下所示**\<的應用設置\>** 元素的內容。 將*帳戶名稱*替換為存儲帳戶的名稱，並將*帳戶金鑰*替換為帳戶訪問金鑰：

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

例如，組態設定會如下顯示：

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

若要以儲存體模擬器為目標，您可以使用對應到已知帳戶名稱和金鑰的捷徑。 在此情況下，您的連接字串設定會是︰

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>新增 using 指示詞

將下列 `using` 指示詞新增至 `Program.cs` 檔案的開頭處：

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>從 Azure 入口網站複製您的認證

範例程式碼需要授與對您儲存體帳戶的存取權。 若要進行授權，您必須以連接字串的形式為應用程式提供您的儲存體帳戶認證。 若要檢視您的儲存體帳戶認證：

1. 導航到[Azure 門戶](https://portal.azure.com)。
2. 找出您的儲存體帳戶。
3. 在儲存體帳戶概觀的 [設定]**** 區段中，選取 [存取金鑰]****。 您的帳戶存取金鑰隨即出現，此外也會顯示每個金鑰的完整連接字串。
4. 尋找 [金鑰1]**** 下方的 [連接字串]**** 值，然後按一下 [複製]**** 按鈕來複製連接字串。 在下一個步驟中，您會將連接字串值新增至環境變數。

    ![顯示如何從 Azure 入口網站複製連接字串的螢幕擷取畫面](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>解析連接字串

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>建立佇列服務用戶端

[CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet) 類別可讓您擷取佇列儲存體中儲存的佇列。 以下是建立服務用戶端的其中一種方式：

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

您現在可以開始撰寫程式碼，以讀取佇列儲存體的資料並將資料寫入其中。

## <a name="create-a-queue"></a>建立佇列

此範例說明如何建立尚不存在的佇列：

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>將訊息插入佇列

若要將訊息插入現有佇列，請先建立新的 [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet)。 接下來，調用[AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet)方法。 **雲佇列消息**可以從字串（UTF-8 格式）或**位元組**陣列創建。 以下是建立佇列 (如果佇列不存在) 並插入訊息 'Hello, World' 的程式碼：

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>查看下一個訊息

您可以通過調用[PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet)方法查看佇列前面的消息，而無需將其從佇列中刪除。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

## <a name="change-the-contents-of-a-queued-message"></a>變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。 下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。 這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。 您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。 通常，您也會保留重試計數，如果重試該郵件超過*n*次，則將其刪除。 這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>將下一個訊息清除佇列

您的程式碼可以使用兩個步驟將訊息自佇列中清除佇列。 呼叫 [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet)時，您會取得佇列中的下一個訊息。 從**GetMessage**返回的消息將對此佇列中讀取消息的任何其他代碼不可見。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息，您還必須呼叫 [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet)。 這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 您的程式碼會在處理完訊息之後立即呼叫 **DeleteMessage** 。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>搭配通用佇列儲存體 API 使用 Async-Await 模式

這個範例示範如何搭配通用佇列儲存體 API 使用 Async-Await 模式。 此範例會呼叫每個指定方法的非同步版本，就像每個方法的非同步 *Async* 尾碼所指示的一樣。 使用非同步方法時，async-await 模式會暫停本機執行，直到呼叫完成為止。 這種行為可讓目前的執行緒執行其他工作，有助於避免發生效能瓶頸並提升應用程式的整體回應。 有關在 .NET 中使用 Async-Await 模式的更多詳細資訊，請參閱[非同步和等待（C# 和視覺化基本）](https://msdn.microsoft.com/library/hh191443.aspx)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="leverage-additional-options-for-de-queuing-messages"></a>運用清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。 首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。 下列程式碼範例將使用 [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) 方法，在一次呼叫中取得 20 個訊息。 接著它會使用 **foreach** 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘。 請注意，所有郵件的 5 分鐘開始，因此在調用**GetMessages**後 5 分鐘後，任何未刪除的消息都將再次可見。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>取得佇列長度

您可以取得佇列中的估計訊息數目。 [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) 方法會要求佇列服務擷取佇列屬性，其中包含訊息計數。 [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) 屬性會傳回 **FetchAttributes** 方法所擷取的最後一個值，而無需呼叫佇列服務。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>刪除佇列

若要刪除佇列及其內含的所有訊息，請在佇列物件上呼叫 [Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) 方法。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

## <a name="next-steps"></a>後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

* 如需可用 API 的完整詳細資訊，請檢視佇列服務參考文件：
  * [Storage Client Library for .NET 參考資料](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [REST API 參考資料](https://msdn.microsoft.com/library/azure/dd179355)
* 了解如何使用 [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)，來簡化您撰寫以使用 Azure 儲存體的程式碼。
* 如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  * [以 .NET 開始使用 Azure 表格儲存體](../../cosmos-db/table-storage-how-to-use-dotnet.md) 以儲存結構化資料。
  * [以 .NET 開始使用 Azure Blob 儲存體](../blobs/storage-dotnet-how-to-use-blobs.md) 以儲存非結構化資料。
  * [使用 .NET (C#) 連接到 SQL Database ](../../sql-database/sql-database-connect-query-dotnet-core.md) 以儲存關聯式資料。

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
