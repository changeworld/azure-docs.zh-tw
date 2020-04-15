---
title: 使用 Python 從 Azure 事件中樞傳送或接收事件 (最新版)
description: 本文將逐步解說如何建立 Python 應用程式，以使用最新的 azure-eventhub 第 5 版套件對 Azure 事件中樞傳送事件或從中接收事件。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 352ff91bf26c7ff4f6945431fe6e1357f030e1db
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477527"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>使用 Python (azure-eventhub 第 5 版) 將事件傳送至事件中樞或從中接收事件
本快速入門說明如何使用 **azure-eventhub 第 5 版** Python 套件，來傳送事件至事件中樞和從事件中樞接收事件。

> [!IMPORTANT]
> 本快速入門會使用最新的 azure-eventhub 第 5 版套件。 如需如何使用舊有 azure-eventhub 第 1 版套件的快速入門，請參閱[使用 azure-eventhub 第 1 版傳送和接收事件](event-hubs-python-get-started-send.md)。 

## <a name="prerequisites"></a>Prerequisites
如果您對 Azure 事件中樞並不熟悉，在進行此快速入門之前，請先參閱[事件中樞概述](event-hubs-about.md)。 

若要完成本快速入門，您必須符合下列必要條件：

- **Microsoft Azure 訂用帳戶**。 若要使用 Azure 服務 (包括 Azure 事件中樞)，您需要訂用帳戶。  如果您沒有現有的 Azure 帳戶，您可以申請[免費試用](https://azure.microsoft.com/free/)，或是在[建立帳戶](https://azure.microsoft.com)時使用 MSDN 訂閱者權益。
- Python 2.7 或 3.5 或更新版本，且已安裝並更新 PIP。
- 事件中樞的 Python 套件。 

    若要安裝此套件，請在路徑中有 Python 的命令提示字元中執行下列命令︰

    ```cmd
    pip install azure-eventhub
    ```

    安裝下列套件，以使用 Azure Blob 儲存體作為檢查點存放區來接收事件：

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **建立事件中樞命名空間和事件中樞**。 第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和事件中樞，請依照[這篇文章](event-hubs-create.md)中的程序操作。 然後，依照下列文章中的指示，取得**事件中樞命名空間的連接字串**：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您稍後會在本快速入門中使用連接字串。

## <a name="send-events"></a>傳送事件
在本節中，您會建立 Python 指令碼來將事件傳送至您稍早建立的事件中樞。

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio 程式碼](https://code.visualstudio.com/)。
2. 建立名為 *send.py* 的指令碼。 此指令碼會將一批事件傳送至您稍早建立的事件中樞。
3. 將下列程式碼貼到 send.py  中：

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > 如需完整的原始程式碼 (包括資訊註解)，請移至 [GitHub send_async.py 頁面](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)。
    

## <a name="receive-events"></a>接收事件
本快速入門會使用 Azure Blob 儲存體作為檢查點存放區。 檢查點存放區的功用是保存檢查點 (也就是最後一個讀取位置)。  

> [!NOTE]
> 如果您在 Azure Stack Hub 上執行，該平台可能支援不同版本的儲存體 Blob SDK，而不是 Azure 上一般可用的版本。 例如，如果您在 [Azure Stack Hub 2002 版](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)上執行 ，儲存體服務的最高可用版本為 2017-11-09。 在此情況下，除了本節中的以下步驟外，您還需要新增程式碼，以將儲存體服務 API 版本設為 2017-11-09 為目標。 如需如何設定特定儲存體 API 版本目標的範例，請參閱 GitHub 上的 [同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py)和[非同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)範例。 如需 Azure Stack Hub 支援的 Azure 儲存體服務版本詳細資訊，請參閱 [Azure Stack Hub 儲存體：差異與注意事項](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)。


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>建立 Azure 儲存體帳戶和 Blob 容器
若要建立 Azure 儲存體帳戶及其中的 Blob 容器，請執行下列步驟：

1. [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [取得儲存體帳戶的連接字串](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

請務必記下連接字串和容器名稱，以便稍後在接收程式碼中使用。


### <a name="create-a-python-script-to-receive-events"></a>建立用來接收事件的 Python 指令碼

您會在本節中建立 Python 指令碼，以從事件中樞接收事件：

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio 程式碼](https://code.visualstudio.com/)。
2. 建立名為 *recv.py* 的指令碼。
3. 請將下列程式碼貼到 recv.py  中：

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > 如需完整的原始程式碼 (包括其他資訊註解)，請移至 [GitHub recv_with_checkpoint_store_async.py 頁面](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)。


### <a name="run-the-receiver-app"></a>執行接收者應用程式

若要執行指令碼，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>執行傳送者應用程式

若要執行指令碼，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰

```bash
python send.py
```

接收者視窗應該會顯示已傳送到事件中樞的訊息。


## <a name="next-steps"></a>後續步驟
在本快速入門中，您已透過非同步方式傳送和接收事件。 若要了解如何同步傳送和接收事件，請移至 [GitHub sync_samples 頁面](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)。

如需 GitHub 上的所有範例 (同步和非同步) 的資訊，請移至[適用於 Python 的 Azure 事件中樞用戶端程式庫範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)。
