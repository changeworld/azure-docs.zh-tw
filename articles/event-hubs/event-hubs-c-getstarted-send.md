---
title: 快速入門：使用 C 傳送事件 - Azure 事件中樞
description: 快速入門：本文會逐步解說如何建立 C 應用程式，以將事件傳送至 Azure 事件中樞。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 5bd4bb66b7e3c3ec37724f8684105befbc9132ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720671"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>快速入門：使用 C 將事件傳送至 Azure 事件中樞

## <a name="introduction"></a>簡介
Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何使用以 C 撰寫的主控台應用程式，將事件傳送至事件中樞。 

## <a name="prerequisites"></a>Prerequisites
若要完成此教學課程，您需要下列項目：

* C 開發環境。 本教學課程假設 Azure Linux VM上的 gcc 堆疊有 Ubuntu 14.04。
* [Microsoft Visual Studio](https://www.visualstudio.com/)。
* **建立事件中樞命名空間和事件中樞**。 請使用 [Azure 入口網站](https://portal.azure.com)建立「事件中樞」類型的命名空間，然後取得您的應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和事件中樞，請依照[這篇文章](event-hubs-create.md)中的程序操作。 請依照以下文章中的指示，取得事件中樞的存取金鑰值：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您可以在您於本教學課程稍後撰寫的程式碼中，使用此存取金鑰。 預設的金鑰名稱是：**RootManageSharedAccessKey**。

## <a name="write-code-to-send-messages-to-event-hubs"></a>撰寫程式碼以便將訊息傳送到事件中樞
本節示範如何撰寫 C 應用程式，以將事件傳送至事件中樞。 此程式碼會使用 [Apache Qpid 專案](https://qpid.apache.org/)中的 Proton AMQP 程式庫。 這與搭配使用服務匯流排佇列與主題與透過 C 的 AMQP 類似 (如[此範例中](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)所示)。 如需詳細資訊，請參閱 [Qpid Proton 文件](https://qpid.apache.org/proton/index.html) \(英文\)。

1. 從 [Qpid AMQP Messenger 頁面](https://qpid.apache.org/proton/messenger.html)，遵循指示以安裝 Qpid Proton (視您的環境而定)。
2. 若要編譯 Proton 程式庫，請安裝下列封裝：
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. 下載 [Qpid Proton 程式庫](https://qpid.apache.org/proton/index.html)，並將它解壓縮，例如：
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. 建立組建目錄、編譯並安裝：
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. 在工作目錄中，使用下列程式碼建立一個稱為 **sender.c** 的新檔案。 請記得取代 SAS 索引鍵/名稱、事件中樞名稱和命名空間的值。 您也必須替代先前針對 **SendRule** 建立之金鑰的 URL 編碼版本。 您可以在 [這裡](https://www.w3schools.com/tags/ref_urlencode.asp)對其進行 URL 編碼。
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. 編譯檔案，並假設 **gcc**：
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > 這個程式碼會使用輸出視窗 1 來盡快強制輸出訊息。 建議您的應用程式嘗試以批次方式處理訊息，以提高輸送量。 如需如何在此環境和其他環境中，以及從提供繫結的平台 (目前是 Perl、PHP、Python 和 Ruby) 中使用 Qpid Proton 程式庫的相關資訊，請參閱 [Qpid AMQP Messenger 頁面](https://qpid.apache.org/proton/messenger.html)。

執行應用程式以便將訊息傳送到事件中樞。 

恭喜！ 您現在已將傳送訊息到事件中樞。

## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中樞的功能與術語](event-hubs-features.md)。


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
