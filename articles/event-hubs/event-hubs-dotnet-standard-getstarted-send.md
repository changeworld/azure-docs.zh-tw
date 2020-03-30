---
title: 使用 .NET（舊）從 Azure 事件中心發送和接收事件
description: 本文提供了用於創建 .NET Core 應用的演練，該應用使用舊的 Microsoft.Azure.EventHubs 包向 Azure 事件中心發送/接收事件。
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 1d399ffec2cd702dcfa39310803bc780b85bfde1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187467"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core-microsoftazureeventhubs"></a>使用 .NET 核心（Microsoft.Azure.事件中心）向 Azure 事件中心發送事件或從 Azure 事件中心接收事件
此快速入門演示如何使用**Microsoft.Azure.EventHubs** .NET 核心庫向事件中心發送事件並從事件中心接收事件。

> [!WARNING]
> 此快速入門使用舊的**Microsoft.Azure.事件中心**包。 有關使用最新**Azure.消息傳遞.事件中心**庫的快速入門，請參閱[使用 Azure.消息傳遞.事件中心庫發送和接收事件](get-started-dotnet-standard-send-v2.md)。 要將應用程式從使用舊庫移動到新庫，請參閱[從 Microsoft.Azure.事件中心遷移到 Azure.消息器的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)。

## <a name="prerequisites"></a>Prerequisites
如果您對 Azure 事件中樞並不熟悉，在進行此快速入門之前，請先參閱[事件中樞概述](event-hubs-about.md)。 

若要完成本快速入門，您必須符合下列必要條件：

- **Microsoft Azure 訂用帳戶**。 若要使用 Azure 服務 (包括 Azure 事件中樞)，您需要訂用帳戶。  如果您沒有現有的 Azure 帳戶，您可以申請[免費試用](https://azure.microsoft.com/free/)，或是在[建立帳戶](https://azure.microsoft.com)時使用 MSDN 訂閱者權益。
- [Microsoft Visual Studio 2019](https://www.visualstudio.com)。
- [.NET Core Visual Studio 2015 或 2017 工具](https://www.microsoft.com/net/core)。 
- **建立事件中樞命名空間和事件中樞**。 第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和事件中樞，請依照[這篇文章](event-hubs-create.md)中的程序操作。 然後，按照文章中的說明獲取**事件中心命名空間的連接字串**：[獲取連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您稍後會在本快速入門中使用連接字串。

## <a name="send-events"></a>傳送事件 
本節說明如何建立可將事件傳送至事件中樞的 .NET Core 主控台應用程式。 

> [!NOTE]
> 您可以從 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) 下載此快速入門來作為範例，並以您事件中樞的值取代 `EventHubConnectionString` 和 `EventHubName` 字串，然後執行。 或者，您可以遵循本快速入門中的步驟，來建立自己的解決方案。


### <a name="create-a-console-application"></a>建立主控台應用程式

啟動 Visual Studio。 在 **"檔"** 功能表中，按一下 **"新建**"，然後按一下"**專案**"。 建立 .NET Core 主控台應用程式。

![新增專案](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>新增事件中樞 NuGet 封裝

按照以下步驟[`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)將 .NET Core 庫 NuGet 包添加到您的專案中： 

1. 以滑鼠右鍵按一下新建立的專案，然後選取 [管理 NuGet 套件]****。
2. 按一下 [瀏覽]**** 索引標籤，然後搜尋「Microsoft.Azure.EventHubs」並選取 [Microsoft.Azure.EventHubs]**** 套件。 按一下 [安裝] **** 完成安裝作業，然後關閉此對話方塊。

### <a name="write-code-to-send-messages-to-the-event-hub"></a>撰寫程式碼以將訊息傳送到事件中樞

1. 在 Program.cs 檔案開頭處加入 `using` 陳述式：

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. 針對「事件中樞」連接字串和實體路徑 (個別事件中樞名稱)，將常數新增到 `Program` 類別。 以建立事件中樞時所取得的適當值取代方括號中的預留位置。 請確定 `{Event Hubs connection string}` 是命名空間層級的連接字串，而不是事件中樞字串。 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. 將名為 `MainAsync` 的新方法新增到 `Program` 類別，如下所示：

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. 將名為 `SendMessagesToEventHub` 的新方法新增到 `Program` 類別，如下所示：

    ```csharp
    // Uses the event hub client to send 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. 將下列程式碼行新增至 `Program` 類別中的 `Main` 方法：

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Program.cs 看起來應該會像下面這樣。

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Uses the event hub client to send 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. 執行程式，並確定沒有任何錯誤。

## <a name="receive-events"></a>接收事件
本節演示如何編寫使用[事件處理器主機](event-hubs-event-processor-host.md)從事件中心接收消息的 .NET Core 主控台應用程式。 [事件處理器主機](event-hubs-event-processor-host.md)是一個 .NET 類別，透過管理持續檢查點以及來自事件中樞的平行接收，簡化來自事件中樞之事件的接收作業。 使用事件處理器主機，可讓您將事件分割到多個接收者，即使裝載於不同的節點時也是一樣。 此範例說明單一接收者如何使用事件處理器主機。
> [!NOTE]
> 可以從[GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)下載此快速入門，使用事件中心值替換`EventHubConnectionString``EventHubName`和`StorageAccountName` `StorageAccountKey`，以及`StorageContainerName`字串，然後運行它。 或者，您可以遵循本教學課程中的步驟，來建立自己的解決方案。

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>建立主控台應用程式

啟動 Visual Studio。 在 **"檔"** 功能表中，按一下 **"新建**"，然後按一下"**專案**"。 建立 .NET Core 主控台應用程式。

![新增專案](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>新增事件中樞 NuGet 封裝

遵循下列步驟，在您的專案中新增 [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 和 [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET Standard 程式庫 NuGet 套件： 

1. 以滑鼠右鍵按一下新建立的專案，然後選取 [管理 NuGet 套件]****。
2. 按一下 [瀏覽]**** 索引標籤，然後搜尋 **Microsoft.Azure.EventHubs** 並選取 [Microsoft.Azure.EventHubs]**** 套件。 按一下 [安裝] **** 完成安裝作業，然後關閉此對話方塊。
3. 重複步驟 1 和 2，並安裝 **Microsoft.Azure.EventHubs.Processor** 套件。

### <a name="implement-the-ieventprocessor-interface"></a>實作 IEventProcessor 介面

1. 在 [方案總管] 中，於專案上按一下滑鼠右鍵，按一下 [新增]****，然後按一下 [類別]****。 將新類別命名為 **SimpleEventProcessor**。

2. 開啟 SimpleEventProcessor.cs 檔案，然後在檔案開頭新增下列 `using` 陳述式。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. 實作 `IEventProcessor` 介面。 以下列程式碼取代 `SimpleEventProcessor` 類別的整個內容：

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>使用 SimpleEventProcessor 更新 Main 方法

1. 在 Program.cs 檔案開頭處加入 `using` 陳述式。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. 針對事件中樞連接字串、事件中樞名稱、儲存體帳戶容器名稱、儲存體帳戶名稱及儲存體帳戶金鑰，將常數新增到 `Program` 類別。 新增下列程式碼，其中將預留位置取代成其對應的值：

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 將名為 `MainAsync` 的新方法新增到 `Program` 類別，如下所示：

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. 將下列程式碼行新增至 `Main` 方法：

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 檔案看起來應該會像下面這樣：

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. 執行程式，並確定沒有任何錯誤。


## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [基於角色的存取控制 （RBAC） 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    這些示例使用舊的**Microsoft.Azure.EventHubs**庫，但可以輕鬆地將其更新為使用最新的**Azure.消息.事件中心**庫。 要將示例從使用舊庫移動到新庫，請參閱[從 Microsoft.Azure.事件中心遷移到 Azure.消息傳遞器的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)。
- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中樞的功能與術語](event-hubs-features.md)
- [事件中樞常見問題集](event-hubs-faq.md)


