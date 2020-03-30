---
title: 連接到 IBM MQ 伺服器
description: 使用 Azure 或本地 IBM MQ 伺服器和 Azure 邏輯應用發送和檢索郵件
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650942"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>從 Azure 邏輯應用連接到 IBM MQ 伺服器

IBM MQ 連接器發送和檢索存儲在本地或 Azure 中的 IBM MQ 伺服器中的郵件。 此連接器包含透過 TCP/IP 網路與遠端 IBM MQ Server 通訊的 Microsoft MQ 用戶端。 本文提供了使用 MQ 連接器的入門指南。 您可以首先流覽佇列上的單個消息，然後嘗試其他操作。

IBM MQ 連接器包括這些操作，但不提供觸發器：

- 流覽單個郵件而不從 IBM MQ 伺服器中刪除郵件
- 流覽一批郵件，而不從 IBM MQ 伺服器中刪除郵件
- 接收單個郵件並從 IBM MQ 伺服器中刪除該郵件
- 接收一批消息並從 IBM MQ 伺服器中刪除郵件
- 向 IBM MQ 伺服器發送一條消息

## <a name="prerequisites"></a>Prerequisites

* 如果您使用的是本地 MQ 伺服器，請在網路中的伺服器上[安裝本地資料閘道](../logic-apps/logic-apps-gateway-install.md)。 安裝本地資料閘道的伺服器還必須安裝 .NET Framework 4.6，以便 MQ 連接器正常工作。 還必須在 Azure 中為本地資料閘道創建資源。 有關詳細資訊，請參閱[設置資料閘道連接](../logic-apps/logic-apps-gateway-connection.md)。

  但是，如果您的 MQ 伺服器在 Azure 中是公開的或可用的，則不必使用資料閘道。

* 正式支援的 IBM WebSphere MQ 版本：

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* 要添加 MQ 操作的邏輯應用。 此邏輯應用必須使用與本地資料閘道連接相同的位置，並且必須已具有啟動工作流的觸發器。 

  MQ 連接器沒有任何觸發器，因此必須首先向邏輯應用添加觸發器。 例如，可以使用"定期"觸發器。 如果您是邏輯應用的新增功能，請嘗試此[快速入門創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

## <a name="browse-a-single-message"></a>瀏覽單一訊息

1. 在邏輯應用中，在觸發器或其他操作下，選擇 **"新建步驟**"。 

1. 在搜索框中，鍵入"mq"，然後選擇此操作：**流覽郵件**

   ![瀏覽訊息](media/connectors-create-api-mq/Browse_message.png)

1. 如果沒有現有的 MQ 連接，請創建連接：  

   1. 在操作中，選擇**通過本地資料閘道連接**。
   
   1. 輸入 MQ 伺服器的屬性。  

      針對 [伺服器]****，您可以輸入 MQ Server 的名稱，或輸入 IP 位址，後面接著冒號和連接埠號碼。
    
   1. 打開**閘道**清單，顯示以前配置的任何閘道連接。 選取您的閘道。
    
   1. 完成之後，請選擇 [建立]****。 
   
      您的連接類似于此示例：

      ![Connection Properties](media/connectors-create-api-mq/Connection_Properties.png)

1. 設置操作的屬性：

   * **佇列**：指定與連接不同的佇列。

   * **消息 Id**、**關聯 Id**、**組 Id**和其他屬性：根據不同的 MQ 消息屬性流覽消息

   * **包括資訊**：指定**True**以在輸出中包含其他消息資訊。 或者，指定**False**以不包括輸出中的其他消息資訊。

   * **超時**：輸入值以確定等待消息以空佇列到達的時間。 如果未輸入任何內容，就會取出佇列中的第一個訊息，且不會花費時間等候要顯示的訊息。

     ![瀏覽訊息屬性](media/connectors-create-api-mq/Browse_message_Props.png)

1. **保存**更改，然後**運行**邏輯應用。

   ![儲存並執行](media/connectors-create-api-mq/Save_Run.png)

   運行完成後，將顯示運行的步驟，您可以查看輸出。

1. 要查看每個步驟的詳細資訊，請選擇綠色核取記號。 要查看有關輸出資料的詳細資訊，請選擇"**顯示原始輸出**"。

   ![瀏覽訊息輸出](media/connectors-create-api-mq/Browse_message_output.png)  

   下面是一些示例原始輸出：

   ![瀏覽訊息原始輸出](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. 如果將 **"包含資訊"** 設置為 true，將顯示以下輸出：

   ![瀏覽訊息包含資訊](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>瀏覽多個訊息

[瀏覽訊息]**** 動作包含 **BatchSize** 選項，指出應該從佇列傳回的訊息數量。  如果 **BatchSize** 沒有項目，就會傳回所有的訊息。 傳回的輸出是陣列訊息。

1. 添加 **"流覽消息"** 操作時，預設情況下將選擇第一個以前配置的連接。 要創建新連接，請選擇 **"更改連接**"。 或者，選擇其他連接。

1. 邏輯應用運行完成後，下面是 **"流覽消息**"操作中的一些示例輸出：

   ![瀏覽訊息輸出](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>接收單條消息

[接收訊息]**** 動作具有與 [瀏覽訊息]**** 動作相同的輸入和輸出。 使用 [接收訊息]**** 時，會從佇列將訊息刪除。

## <a name="receive-multiple-messages"></a>接收多個訊息

[接收訊息]**** 動作具有與 [瀏覽訊息]**** 動作相同的輸入和輸出。 使用 [接收訊息]**** 時，會從佇列將訊息刪除。

如果在執行流覽或接收時佇列中沒有消息，則該步驟將使用此輸出失敗：  

![MQ 無訊息錯誤](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>傳送訊息

添加 **"發送消息"** 操作時，預設情況下將選擇第一個以前配置的連接。 要創建新連接，請選擇 **"更改連接**"。 或者，選擇其他連接。

1. 選擇有效的訊息類型：**資料圖**、**回復**或**請求**  

   ![傳送訊息屬性](media/connectors-create-api-mq/Send_Msg_Props.png)

1. 邏輯應用完成運行後，下面是 **"發送消息"** 操作中的一些示例輸出：

   ![傳送訊息輸出](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>連接器參考

有關此連接器的更多技術詳細資訊，例如連接器的 Swagger 檔所述的觸發器、操作和限制，請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/mq/)。

> [!NOTE]
> 對於[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用，此連接器的 ISE 標記版本使用[ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
