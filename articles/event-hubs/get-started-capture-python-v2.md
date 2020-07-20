---
title: 從 Python 應用程式讀取 Azure 事件中樞擷取的資料 (最新版)
description: 本文將說明如何撰寫 Python 程式碼來擷取傳送至事件中樞的資料，以及從 Azure 儲存體帳戶讀取已擷取的事件資料。
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 752a582e59b438f7449d6722b222969dc641880c
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85315299"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>使用 Python 擷取及讀取 Azure 儲存體中的事件中樞資料 (azure-eventhub 第 5 版)

您可以設定事件中樞，以便從 Azure 儲存體帳戶或 Azure Data Lake Storage Gen 1 或 Gen 2 擷取傳送到事件中樞的資料。 本文將說明如何撰寫 Python 程式碼來將事件傳送至事件中樞，以及從 **Azure Blob 儲存體**讀取已擷取的資料。 如需此功能的詳細資訊，請參閱[事件中樞擷取功能概觀](event-hubs-capture-overview.md)。

此快速入門使用 [Azure Python SDK](https://azure.microsoft.com/develop/python/) 來示範「擷取」功能。 sender.py 應用程式會以 JSON 格式將模擬的環境遙測傳送至事件中樞。 事件中樞已設定為使用擷取功能，將此資料批次寫入至 Blob 儲存體。 capturereader.py 應用程式會讀取這些 Blob，並為每個裝置建立附加檔案。 此應用程式接著會將資料寫入至 CSV 檔案。

> [!IMPORTANT]
> 本快速入門使用第 5 版的 Azure 事件中樞 Python SDK。 如需使用第 1 版 Python SDK 的快速入門，請參閱[這篇文章](event-hubs-capture-python.md)。 

在本快速入門中，您將： 

> [!div class="checklist"]
> * 在 Azure 入口網站中建立 Azure Blob 儲存體帳戶和容器。
> * 使用 Azure 入口網站建立事件中樞命名空間。
> * 建立已啟用擷取功能的事件中樞，並將其連線到您的儲存體帳戶。
> * 使用 Python 指令碼，將資料傳送至您的事件中樞。
> * 使用另一個 Python 指令碼，讀取和處理事件中樞「擷取」的檔案。

## <a name="prerequisites"></a>必要條件

- Python 2.7 和 3.5 或更新版本，且已安裝並更新 PIP。  
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。  
- 作用中的事件中樞命名空間和事件中樞。
[建立事件中樞命名空間和命名空間中的事件中樞](event-hubs-create.md)。 記下事件中樞命名空間的名稱、事件中樞的名稱，以及命名空間的主要存取金鑰。 若要取得存取金鑰，請參閱[取得事件中樞連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 預設的金鑰名稱為 *RootManageSharedAccessKey*。 在本快速入門中，您只需要主要金鑰。 您無須連接字串。  
- Azure 儲存體帳戶、儲存體帳戶中的 Blob 容器，以及儲存體帳戶的連接字串。 如果您沒有這些項目，請執行下列動作：  
    1. [建立 Azure 儲存體帳戶](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [在儲存體帳戶中建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [取得儲存體帳戶的連接字串](../storage/common/storage-configure-connection-string.md)

    請務必記下連接字串和容器名稱，以便稍後在本快速入門中使用。  
- 啟用事件中樞的擷取功能。 若要這麼做，請依照[使用 Azure 入口網站啟用事件中樞擷取功能](event-hubs-capture-enable-through-portal.md)中的指示。 選取您在先前步驟中建立的儲存體帳戶和 Blob 容器。 您也可以在建立事件中樞時啟用此功能。  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>建立 Python 指令碼以將事件傳送到事件中樞
在本節中，您會建立 Python 指令碼將 200 個事件 (10 個裝置 * 20 個事件) 傳送到事件中樞。 這些事件是以 JSON 格式傳送的環境數據範例。 

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio 程式碼][Visual Studio Code]。
2. 建立稱為 *sender.py*的指令碼。 
3. 將下列程式碼貼到 *sender.py* 中。 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. 取代指令碼中的下列值：  
    * 將 `EVENT HUBS NAMESPACE CONNECTION STRING` 取代為事件中樞命名空間的連接字串。  
    * 將 `EVENT HUB NAME` 取代為您的事件中樞名稱。  
5. 執行指令碼以將事件傳送到事件中樞。  
6. 在 Azure 入口網站中，您可以確認事件中樞是否已收到訊息。 切換至 [計量] 區段中的 [訊息] 檢視。 請重新整理頁面來更新圖表。 可能需要幾秒鐘的時間，頁面才會顯示已收到訊息。 

    [![確認事件中樞已收到訊息](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>建立 Python 指令碼來讀取擷取檔案
在此範例中，已擷取的資料會儲存在 Azure Blob 儲存體中。 本節中的指令碼會從您的 Azure 儲存體帳戶讀取已擷取的資料檔案，並產生方便您開啟和檢視的 CSV 檔案。 您會在應用程式目前的工作目錄中看到 10 個檔案。 這些檔案將包含 10 部裝置的環境數據。 

1. 在 Python 編輯器中，建立名為 capturereader.py 的指令碼。 此指令碼會讀取擷取檔案，並為每個裝置建立檔案以便只寫入該裝置的資料。
2. 將下列程式碼貼到 *capturereader.py* 中。 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. 將 `AZURE STORAGE CONNECTION STRING` 取代為 Azure 儲存體帳戶的連接字串。 您在本快速入門中建立的容器名稱是 capture。 如果您使用不同的容器名稱，請將 capture 取代為儲存體帳戶中的容器名稱。 

## <a name="run-the-scripts"></a>執行指令碼
1. 開啟在其路徑中具有 Python 的命令提示字元，並執行下列命令來安裝 Python 必要條件封裝︰
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. 將目錄變更為儲存已儲存 sender.py 和 capturereader.py 的目錄，並執行此命令︰
   
   ```
   python sender.py
   ```
   
   此命令會啟動新的 Python 程序來執行傳送器。
3. 讓擷取動作執行幾分鐘後，在原始命令視窗中輸入下列命令：
   
   ```
   python capturereader.py
   ```

   此擷取處理器會使用本機目錄從儲存體帳戶和容器下載所有 Blob。 也會處理任何非空白的 Blob，並將結果以 CSV 檔案的形式寫入到本機目錄。

## <a name="next-steps"></a>後續步驟
請參閱[探索 GitHub 上的 Python 範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples) \(英文\)。 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
