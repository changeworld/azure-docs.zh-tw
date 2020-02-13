---
title: 快速入門：從 Python 應用程式讀取擷取的資料 - Azure 事件中樞
description: 快速入門：使用 Azure Python SDK 來示範事件中樞擷取功能的指令碼。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: 6c830cf871c2ae650bb61e8b3712a664e9e405d4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187298"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>快速入門：事件中樞擷取逐步解說︰Python (azure-eventhub 第 1 版)

擷取是 Azure 事件中樞的功能。 您可以使用「擷取」，將事件中樞內的串流資料自動傳遞至您選擇的 Azure Blob 儲存體帳戶。 此功能可讓您輕鬆地對即時串流資料執行批次處理。 本文說明如何搭配使用事件中樞擷取與 Python。 如需事件中樞擷取的詳細資訊，請參閱[透過 Azure 事件中樞擷取事件][Overview of Event Hubs Capture]。

此逐步解說使用 [Azure Python SDK](https://azure.microsoft.com/develop/python/) 來示範「擷取」功能。 *sender.py* 程式會以 JSON 格式將模擬的環境遙測傳送至事件中樞。 事件中樞會使用擷取功能，將此資料分批寫入至 Blob 儲存體。 *capturereader.py* 應用程式會讀取這些 Blob、為每個裝置建立附加檔案，並將資料寫入至每個裝置上的 *.csv* 檔案。

> [!WARNING]
> 本快速入門適用於第 1 版的 Azure 事件中樞 Python SDK。 建議您將程式碼[遷移](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)至 [Python SDK 第 5 版](get-started-capture-python-v2.md)。

在本逐步解說中，您將： 

> [!div class="checklist"]
> * 在 Azure 入口網站中建立 Azure Blob 儲存體帳戶和容器。
> * 啟用事件中樞「擷取」功能，並將其導向至您的儲存體帳戶。
> * 使用 Python 指令碼，將資料傳送至您的事件中樞。
> * 使用另一個 Python 指令碼，讀取和處理事件中樞「擷取」的檔案。

## <a name="prerequisites"></a>Prerequisites

- Python 3.4 或更新版本，且已安裝並更新 `pip`。
  
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
  
- 作用中的事件中樞命名空間和事件中樞，可依照下列快速入門中的指示建立：[快速入門：使用 Azure 入口網站建立事件中樞](event-hubs-create.md)。 請記下您的命名空間和事件中樞名稱，以便稍後在此逐步解說中使用。 
  
  > [!NOTE]
  > 如果您已有可使用的儲存體容器，您可以在建立事件中樞時啟用「擷取」並選取儲存體容器。 
  > 
  
- 您的事件中樞共用存取金鑰名稱和主要金鑰值。 請在 [事件中樞] 頁面的 [共用存取原則]  下尋找或建立這些值。 預設的存取金鑰名稱為 **RootManageSharedAccessKey**。 複製存取金鑰名稱和主要金鑰值，以供稍後在此逐步解說中使用。 

## <a name="create-an-azure-blob-storage-account-and-container"></a>建立 Azure Blob 儲存體帳戶和容器

建立要用於擷取的儲存體帳戶和容器。 

1. 登入 [Azure 入口網站][Azure portal]。
2. 在左側導覽中選取 [儲存體帳戶]  ，然後在 [儲存體帳戶]  畫面上選取 [新增]  。
3. 在儲存體帳戶建立畫面上，選取訂用帳戶和資源群組，並指定儲存體帳戶的名稱。 您可以將其他選項保留為預設值。 選取 [檢閱 + 建立]  ，檢閱設定，然後選取 [建立]  。 
   
   ![建立儲存體帳戶][1]
   
4. 在部署完成後，選取 [前往資源]  ，然後在儲存體帳戶的 [概觀]  畫面上選取 [容器]  。
5. 在 [容器]  畫面上，選取 [+ 容器]  。 
6. 在 [新增容器]  畫面上指定容器的名稱，然後選取 [確定]  。 記下容器名稱，以便稍後在此逐步解說中使用。 
7. 在 [容器]  畫面的左側導覽中，選取 [存取金鑰]  。 複製 [儲存體帳戶名稱]  以及 **key1** 底下的 [金鑰]  值，以供稍後在此逐步解說中使用。
 
## <a name="enable-event-hubs-capture"></a>啟用事件中樞擷取

1. 在 Azure 入口網站中，從 [所有資源]  中選取 [事件中樞命名空間]，選取左側導覽中的 [事件中樞]  ，然後選取您的事件中樞，以瀏覽至您的事件中樞。 
2. 在事件中樞 [概觀]  畫面上，選取 [擷取事件]  。
3. 在 [擷取]  畫面上，選取 [開啟]  。 然後，在 [Azure 儲存體容器]  底下，選取 [選取容器]  。 
4. 在 [容器]  畫面上選取您要使用的儲存體容器，然後選取 [選取]  。 
5. 在 [擷取]  畫面上，選取 [儲存變更]  。 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>建立將事件傳送至事件中樞的 Python 指令碼
此指令碼會將 200 個事件傳送到事件中樞。 這些事件是以 JSON 格式傳送的簡單環境數據。

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio 程式碼][Visual Studio Code]。
2. 建立名為 *sender.py* 的新檔案。 
3. 將下列程式碼貼到 *sender.py* 中。 請將事件中樞的 \<namespace>、\<AccessKeyName>、\<primary key value> 和 \<eventhub> 取代為您自己的值。
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. 儲存檔案。

## <a name="create-a-python-script-to-read-capture-files"></a>建立讀取擷取檔案的 Python 指令碼

此指令碼會讀取擷取檔案，並為每個裝置建立檔案而僅寫入該裝置的資料。

1. 在 Python 編輯器中，建立名為 *capturereader.py* 的新檔案。 
2. 將下列程式碼貼到 *capturereader.py* 中。 請將 \<storageaccount>、\<storage account access key> 和 \<storagecontainer> 取代為您已儲存的值。
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
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
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>執行 Python 指令碼

1. 開啟在其路徑中具有 Python 的命令提示字元，並執行下列命令以安裝 Python 必要條件套件︰
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   如果您有舊版的 `azure-storage` 或 `azure`，則可能需要使用 `--upgrade` 選項。
   
   您可能也需要執行下列命令。 在大部分的系統上都不需要執行此命令。 
   
   ```cmd
   pip install cryptography
   ```
   
2. 從您儲存 *sender.py* 和 *capturereader.py* 的目錄中，執行下列命令：
   
   ```cmd
   start python sender.py
   ```
   
   此命令會啟動新的 Python 程序以執行傳送者。
   
3. 當擷取執行完成後，請執行下列命令：
   
   ```cmd
   python capturereader.py
   ```

   擷取處理器會從儲存體帳戶容器下載所有非空白的 Blob，並將結果以 *.csv* 檔案的形式寫入本機目錄中。 

## <a name="next-steps"></a>後續步驟

若要深入了解事件中樞，請參閱： 

* [事件中樞擷取概觀][Overview of Event Hubs Capture]
* [使用事件中樞的完整範例應用程式](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [事件中心概觀][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
