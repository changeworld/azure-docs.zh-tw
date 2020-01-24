---
title: 使用 Python 傳送或接收事件-Azure 事件中樞 |Microsoft Docs
description: 本文提供逐步解說，說明如何建立可將事件傳送至 Azure 事件中樞的 Python 應用程式。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: d7ab79d49aade7dd6e98cf33ce538174d176c784
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705338"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>使用 Python 將事件傳送至事件中樞或從中接收事件

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體及裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料，可以透過任何即時分析提供者或批次/儲存體配接器加以轉換及儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何建立 Python 應用程式，以將事件傳送至事件中樞或從中接收事件。

> [!IMPORTANT]
> 本快速入門使用第5版的 Azure 事件中樞 Python SDK。 如需使用舊版 Python SDK 的快速入門，請參閱[這篇文章](event-hubs-python-get-started-send.md)。 如果您使用第1版的 SDK，建議您將程式碼遷移至最新版本。 如需詳細資訊，請參閱[遷移指南](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)。


## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
- 作用中的事件中樞命名空間和事件中樞，遵循[快速入門：使用 Azure 入口網站建立事件中樞](event-hubs-create.md)中的指示建立。 請記下命名空間和事件中樞名稱，以便稍後在此逐步解說中使用。
- 您的事件中樞命名空間的共用存取金鑰名稱和主要金鑰值。 請依照[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的指示，取得存取金鑰名稱和值。 預設的存取金鑰名稱為 **RootManageSharedAccessKey**。 請複製金鑰名稱和主要金鑰值，以供稍後在此逐步解說中使用。
- Python 2.7 和3.5 或更新版本，並已安裝並更新 `pip`。
- 事件中樞的 Python 套件。 若要安裝此套件，請在路徑中有 Python 的命令提示字元中執行下列命令︰

    ```cmd
    pip install azure-eventhub
    ```

    安裝此套件，以使用 Azure Blob 儲存體作為檢查點存放區來接收事件。

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>傳送事件
在本節中，您會建立 Python 腳本，將事件傳送至您稍早建立的事件中樞。

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 **send.py** 的指令碼。 此腳本會將一批事件傳送至您稍早建立的事件中樞。
3. 將下列程式碼貼入 send.py。 如需詳細資訊，請參閱程式碼批註。

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()

            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > 如需具有非常有用批註的完整原始程式碼，請參閱[GitHub 上的這個](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)檔案

## <a name="receive-events"></a>接收事件
本快速入門會使用 Azure Blob 儲存體做為檢查點存放區。 檢查點存放區是用來保存檢查點（最後一個讀取位置）。  

### <a name="create-an-azure-storage-and-a-blob-container"></a>建立 Azure 儲存體和 blob 容器
請遵循下列步驟來建立 Azure 儲存體帳戶中的 blob 容器。

1. [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [取得儲存體帳戶的連接字串](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    記下連接字串和容器名稱。 您會在接收程式碼中使用它們。


### <a name="create-python-script-to-receive-events"></a>建立用來接收事件的 Python 腳本

在本節中，您會建立 Python 腳本，以從事件中樞接收事件：

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 **recv.py** 的指令碼。
3. 將下列程式碼貼入 recv.py。 如需詳細資訊，請參閱程式碼批註。

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # print the event data
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)

    async def main():
        # create an Azure blob checkpoint store to store the checkpoints
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > 如需具有非常有用批註的完整原始程式碼，請參閱[GitHub 上的這個](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)檔案


### <a name="run-the-receiver-app"></a>執行接收者應用程式

若要執行指令碼，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>執行寄件者應用程式

若要執行指令碼，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰

```bash
python send.py
```

您應該會在 [接收者] 視窗中看到傳送到事件中樞的訊息。


## <a name="next-steps"></a>後續步驟
在本快速入門中，您已以非同步方式傳送和接收事件。 若要瞭解如何同步傳送和接收事件，請參閱[此位置](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)中的範例。

您可以在[這裡](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)找到 GitHub 上的所有範例（同步和非同步）。
