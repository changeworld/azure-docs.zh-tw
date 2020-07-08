---
title: 使用 Python 從 Azure 事件中樞傳送或接收事件 (舊版)
description: 此逐步解說將說明如何建立及執行 Python 指令碼，以使用舊有的 azure-eventhub 第 1 版套件將事件傳送至 Azure 事件中樞或從中接收事件。
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 4ce53d2d82a00f98dbbd538bd7a61da9ba44e832
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314468"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>快速入門：使用 Python 以事件中樞傳送和接收事件 (azure-eventhub 第 1 版)
本快速入門說明如何使用 **azure-eventhub 第 1 版** Python 套件，來傳送事件至事件中樞和從事件中樞接收事件。 

> [!WARNING]
> 本快速入門會使用舊有的 azure-eventhub 第 1 版套件。 如需如何使用套件最新**第 5 版**的快速入門，請參閱[使用 azure-eventhub 第 5 版傳送和接收事件](get-started-python-send-v2.md)。 若要將應用程式從使用舊套件改為使用新套件，請參閱[從 azure-eventhub 第 1 版遷移至第 5 版的指南](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)。
 

## <a name="prerequisites"></a>必要條件
如果您對 Azure 事件中樞並不熟悉，在進行此快速入門之前，請先參閱[事件中樞概述](event-hubs-about.md)。 

若要完成本快速入門，您必須符合下列必要條件：

- **Microsoft Azure 訂用帳戶**。 若要使用 Azure 服務 (包括 Azure 事件中樞)，您需要訂用帳戶。  如果您沒有現有的 Azure 帳戶，您可以申請[免費試用](https://azure.microsoft.com/free/)，或是在[建立帳戶](https://azure.microsoft.com)時使用 MSDN 訂閱者權益。
- Python 3.4 或更新版本，且已安裝並更新 `pip`。
- 事件中樞的 Python 套件。 若要安裝此套件，請在路徑中有 Python 的命令提示字元中執行下列命令︰ 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **建立事件中樞命名空間和事件中樞**。 第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和事件中樞，請依照[這篇文章](event-hubs-create.md)中的程序操作。 然後，依照以下文章中的指示，取得事件中樞的存取金鑰值：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您可以在於本快速入門稍後撰寫的程式碼中，使用此存取金鑰。 預設的金鑰名稱是：**RootManageSharedAccessKey**。 


## <a name="send-events"></a>傳送事件

若要建立可將事件傳送至事件中樞的 Python 應用程式：

> [!NOTE]
> 除了逐步執行快速入門以外，您也可以從 GitHub 下載並執行[範例應用程式](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)。 請將 `EventHubConnectionString` 和 `EventHubName` 字串取代為您的事件中樞值。

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 *send.py* 的新檔案。 此指令碼會將 100 個事件傳送到事件中樞。
3. 將下列程式碼貼入 *send.py*，以您的值取代事件中樞 \<namespace>、\<eventhub>、\<AccessKeyName>和 \<primary key value>： 
   
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
2. 將下列程式碼貼入 *recv.py*，以您的值取代事件中樞 \<namespace>、\<eventhub>、\<AccessKeyName>和 \<primary key value>： 
   
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

