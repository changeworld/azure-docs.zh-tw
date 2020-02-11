---
title: 使用 Python 從 Azure 事件中樞傳送或接收事件 (舊版)
description: 此逐步解說將說明如何建立及執行 Python 指令碼，以使用舊有的 azure-eventhub 第 1 版套件將事件傳送至 Azure 事件中樞或從中接收事件。
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 654ccd6352dc0b671cc3becdafd2f1e1102dd39e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902948"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>快速入門：使用 Python 以事件中樞傳送和接收事件 (azure-eventhub 第 1 版)

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可處理及儲存來自分散式軟體和裝置的事件、資料或遙測資料。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細資訊，請參閱 [Azure 事件中樞](event-hubs-about.md)和[Azure 事件中樞的功能與術語](event-hubs-features.md)。

本快速入門會說明如何建立 Python 應用程式，以將事件傳送至事件中樞或從中接收事件。 

> [!WARNING]
> 本快速入門適用於第 1 版的 Azure 事件中樞 Python SDK。 建議您將程式碼[遷移](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)至 [Python SDK 第 5 版](get-started-python-send-v2.md)。

 
 

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您必須符合下列必要條件：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
- 作用中的事件中樞命名空間和事件中樞，可依照下列快速入門中的指示建立：[快速入門：使用 Azure 入口網站建立事件中樞](event-hubs-create.md)。 請記下命名空間和事件中樞名稱，以便稍後在此逐步解說中使用。 
- 您的事件中樞命名空間的共用存取金鑰名稱和主要金鑰值。 請依照[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的指示，取得存取金鑰名稱和值。 預設的存取金鑰名稱為 **RootManageSharedAccessKey**。 請複製金鑰名稱和主要金鑰值，以供稍後在此逐步解說中使用。 
- Python 3.4 或更新版本，且已安裝並更新 `pip`。
- 事件中樞的 Python 套件。 若要安裝此套件，請在路徑中有 Python 的命令提示字元中執行下列命令︰ 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```


## <a name="send-events"></a>傳送事件

若要建立可將事件傳送至事件中樞的 Python 應用程式：

> [!NOTE]
> 除了逐步執行快速入門以外，您也可以從 GitHub 下載並執行[範例應用程式](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)。 請將 `EventHubConnectionString` 和 `EventHubName` 字串取代為您的事件中樞值。

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 *send.py* 的新檔案。 此指令碼會將 100 個事件傳送到事件中樞。
3. 將下列程式碼貼到 *send.py* 中，並將事件中樞的 \<namespace>、\<eventhub>、\<AccessKeyName> 和 \<primary key value> 取代為您的值： 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. 儲存檔案。 

若要執行指令碼，請從您儲存 *send.py* 的目錄中執行下列命令：

```cmd
start python send.py
```

恭喜！ 您現在已將傳送訊息到事件中樞。

## <a name="receive-events"></a>接收事件

若要建立可從事件中樞接收事件的 Python 應用程式：

1. 在您的 Python 編輯器中，建立名為 *recv.py* 的檔案。
2. 將下列程式碼貼到 *recv.py* 中，並將事件中樞的 \<namespace>、\<eventhub>、\<AccessKeyName> 和 \<primary key value> 取代為您的值： 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. 儲存檔案。

若要執行指令碼，請從您儲存 *recv.py* 的目錄中執行下列命令：

```cmd
start python recv.py
```

## <a name="next-steps"></a>後續步驟
如需事件中樞的詳細資訊，請參閱下列文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中樞的功能與術語](event-hubs-features.md)
- [事件中樞常見問題集](event-hubs-faq.md)

