---
title: Azure 事件中樞 - 使用 .NET Framework 傳送/接收事件
description: 快速入門：本文會逐步解說如何建立 .NET Framework 應用程式，以將事件傳送至 Azure 事件中樞。
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ea3ad7e90a947269833a03a4cacf3d4fe76a6620
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334271"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>快速入門：使用 .NET Framework 將事件傳送至 Azure 事件中樞或從中接收事件
Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何以 C# 建立 .NET Framework 主控台應用程式，以將事件傳送至事件中樞或從中接收事件。 

## <a name="prerequisites"></a>Prerequisites
若要完成本教學課程，您需要下列必要條件：

- [Microsoft Visual Studio 2019](https://visualstudio.com)。
- **建立事件中樞命名空間和事件中樞**。 第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和事件中樞，請依照[這篇文章](event-hubs-create.md)中的程序操作。 然後，依照下列文章中的指示，取得**事件中樞命名空間的連接字串**：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您稍後會在本教學課程中使用連接字串。

## <a name="send-events"></a>傳送事件 
本節說明如何建立可將事件傳送至事件中樞的 .NET Framework 主控台應用程式。 

### <a name="create-a-console-application"></a>建立主控台應用程式

在 Visual Studio 中，使用 **主控台應用程式** 專案範本建立新的「Visual C# 桌面應用程式」專案。 將專案命名為 **Sender**。
   
![顯示 [新增專案] 視窗的螢幕擷取畫面，其中已選取 [主控台應用程式 (.NET Framework)]，並反白顯示專案名稱 [寄件者] 和 [確定] 按鈕。](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>新增事件中樞 NuGet 封裝

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [寄件者]  專案，然後按一下 [管理方案的 NuGet 封裝]  。 
2. 按一下 [瀏覽]  索引標籤，然後搜尋 `WindowsAzure.ServiceBus`。 按一下 [安裝]  並接受使用條款。 
   
    ![安裝服務匯流排 NuGet 套件](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio 會下載、安裝並新增 [Azure 服務匯流排程式庫 NuGet 封裝](https://www.nuget.org/packages/WindowsAzure.ServiceBus)的參考。

### <a name="write-code-to-send-messages-to-the-event-hub"></a>撰寫程式碼以將訊息傳送到事件中樞

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. 將下列欄位新增至 **Program** 類別，並以您在上一節中建立的事件中樞名稱替代預留位置值，以及您先前儲存的命名空間層級連接字串。 您可以在 Azure 入口網站的 [事件中樞] 頁面上，從 **RootManageSharedAccessKey** 下方的 **Connection string-primary** 索引鍵中，複製事件中樞的連接字串。 如需詳細步驟，請參閱[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. 將下列方法加入至 **Program** 類別：
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      這個方法會以 200 毫秒的延遲時間持續將事件傳送至事件中心。
4. 最後，將下列幾行新增至 **Main** 方法：
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. 執行程式，並確定沒有任何錯誤。
  
## <a name="receive-events"></a>接收事件
在本節中，您將撰寫使用[事件處理器主機](event-hubs-event-processor-host.md)從事件中樞接收訊息的 .NET Framework 主控台應用程式。 [事件處理器主機](event-hubs-event-processor-host.md)是一個 .NET 類別，透過管理持續檢查點以及來自事件中樞的平行接收，簡化來自事件中樞的事件接收作業。 使用事件處理器主機，可讓您將事件分割到多個接收者，即使裝載於不同的節點時也是一樣。 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>建立主控台應用程式

在 Visual Studio 中，使用**主控台應用程式**專案範本建立新的「Visual C# 桌面應用程式」專案。 將專案命名為 **Receiver**。
   
![建立主控台應用程式](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>新增事件中樞 NuGet 封裝

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [收件者]  專案，然後按一下 [管理方案的 NuGet 封裝]  。
2. 按一下 [瀏覽]  索引標籤，然後搜尋 `Microsoft Azure Service Bus Event Hub - EventProcessorHost`。 按一下 [安裝]  並接受使用條款。
   
    ![搜尋事件處理器主機 NuGet 套件](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio 會下載、安裝並加入 [Azure 服務匯流排事件中樞 - EventProcessorHost NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)的參考與其所有相依性。

### <a name="implement-the-ieventprocessor-interface"></a>實作 IEventProcessor 介面

1. 在 [接收者]  專案上按一下滑鼠右鍵，按一下 [新增]  ，然後按一下 [類別]  。 將新類別命名為 **SimpleEventProcessor**，然後按一下 [新增]  以建立該類別。
   
    ![新增 SimpleEventProcessor 類別](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. 在 SimpleEventProcessor.cs 檔案開頭處新增下列陳述式：
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. 將該類別的主體取代為下列程式碼：
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      **EventProcessorHost** 會呼叫這個類別，來處理接收自事件中樞的事件。 `SimpleEventProcessor` 類別會使用碼錶定期在 **EventProcessorHost** 內容上呼叫檢查點方法。 這個流程可確保重新啟動接收者時，遺失的處理工作不超過五分鐘。

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>使用 SimpleEventProcessor 更新 Main 方法

1. 在 **Program** 類別檔案的頂端，新增下列 `using` 陳述式：
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. 以下列程式碼取代 `Program` 類別中的 `Main` 方法，並替代您先前儲存的事件中樞名稱和命名空間層級連接字串，以及您在先前各節中複製的儲存體帳戶和金鑰。 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. 執行程式，並確定沒有任何錯誤。
  
## <a name="next-steps"></a>後續步驟
請閱讀下列文章： 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中樞的功能與術語](event-hubs-features.md)。
- [事件中樞常見問題集](event-hubs-faq.md)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
