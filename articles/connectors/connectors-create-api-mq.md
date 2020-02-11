---
title: 連接到 IBM MQ server
description: 使用 Azure 或內部部署的 IBM MQ server 和 Azure Logic Apps 來傳送和取出訊息
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 489f53a4f4c1c0d5bd782f42a9daf73217234793
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118044"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>從 Azure Logic Apps 連接到 IBM MQ server

IBM MQ 連接器會傳送和抓取儲存在內部部署或 Azure 中的 IBM MQ server 的訊息。 此連接器包含透過 TCP/IP 網路與遠端 IBM MQ Server 通訊的 Microsoft MQ 用戶端。 本文提供使用 MQ 連接器的入門指南。 您可以從流覽佇列上的單一訊息開始，然後再嘗試其他動作。

IBM MQ connector 包含這些動作，但不提供任何觸發程式：

- 流覽單一訊息，而不從 IBM MQ server 刪除訊息
- 流覽一批訊息，而不從 IBM MQ server 刪除訊息
- 接收單一訊息，並從 IBM MQ server 刪除訊息
- 接收一批訊息，並從 IBM MQ server 刪除訊息
- 將單一訊息傳送至 IBM MQ server

## <a name="prerequisites"></a>必要條件

* 如果您使用內部部署 MQ 伺服器，請將內部[部署資料閘道安裝](../logic-apps/logic-apps-gateway-install.md)在您網路內的伺服器上。 安裝內部部署資料閘道的伺服器也必須安裝 .NET Framework 4.6，MQ connector 才能正常操作。 您也必須在 Azure 中為內部部署資料閘道建立資源。 如需詳細資訊，請參閱[設定資料閘道聯](../logic-apps/logic-apps-gateway-connection.md)機。

  不過，如果您的 MQ 伺服器已公開可用或在 Azure 中提供，您就不需要使用資料閘道。

* 正式支援的 IBM WebSphere MQ 版本：

  * MQ 7.5
  * MQ 8.0
  * MQ 9。0

* 您想要在其中新增 MQ 動作的邏輯應用程式。 此邏輯應用程式必須使用與您的內部部署資料閘道連線相同的位置，而且必須已有啟動工作流程的觸發程式。 

  MQ 連接器沒有任何觸發程式，因此您必須先將觸發程式新增至邏輯應用程式。 例如，您可以使用「週期」觸發程式。 如果您不熟悉邏輯應用程式，請嘗試此[快速入門來建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

## <a name="browse-a-single-message"></a>瀏覽單一訊息

1. 在您的邏輯應用程式中，于觸發程式或其他動作底下，選擇 [**新增步驟**]。 

1. 在搜尋方塊中，輸入 "mq"，然後選取此動作： **[流覽訊息]**

   ![流覽訊息](media/connectors-create-api-mq/Browse_message.png)

1. 如果您沒有現有的 MQ 連接，請建立連接：  

   1. 在 [動作] 中，選取 [透過內部**部署資料網**關聯機]。
   
   1. 輸入 MQ 伺服器的屬性。  

      針對 [伺服器]，您可以輸入 MQ Server 的名稱，或輸入 IP 位址，後面接著冒號和連接埠號碼。
    
   1. 開啟 [**閘道**] 清單，其中會顯示任何先前設定的閘道連線。 選取您的閘道。
    
   1. 完成之後，請選擇 [建立]。 
   
      您的連線如下列範例所示：

      ![連接屬性](media/connectors-create-api-mq/Connection_Properties.png)

1. 設定動作的屬性：

   * **佇列**：指定與連接不同的佇列。

   * **MessageId**、 **CorrelationId**、 **GroupId**和其他屬性：根據不同的 MQ message 屬性流覽訊息

   * **IncludeInfo**：指定**True**以在輸出中包含其他訊息資訊。 或者，指定**False**以不在輸出中包含其他訊息資訊。

   * **Timeout**：輸入值，以決定等候訊息到達空白佇列的時間長度。 如果未輸入任何內容，就會取出佇列中的第一個訊息，且不會花費時間等候要顯示的訊息。

     ![流覽訊息屬性](media/connectors-create-api-mq/Browse_message_Props.png)

1. **儲存**您的變更，然後**執行**邏輯應用程式。

   ![儲存並執行](media/connectors-create-api-mq/Save_Run.png)

   執行完成之後，會顯示執行中的步驟，而且您可以檢查輸出。

1. 若要查看每個步驟的詳細資料，請選擇綠色核取記號。 若要查看輸出資料的詳細資訊，請選擇 [**顯示原始輸出**]。

   ![流覽訊息輸出](media/connectors-create-api-mq/Browse_message_output.png)  

   以下是一些範例原始輸出：

   ![瀏覽訊息原始輸出](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. 如果您將**IncludeInfo**設定為 true，則會顯示下列輸出：

   ![流覽訊息包含資訊](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>瀏覽多個訊息

[瀏覽訊息] 動作包含 **BatchSize** 選項，指出應該從佇列傳回的訊息數量。  如果 **BatchSize** 沒有項目，就會傳回所有的訊息。 傳回的輸出是陣列訊息。

1. 當您新增 [**流覽訊息]** 動作時，預設會選取先前設定的第一個連接。 若要建立新的連接，請選擇 [**變更連接**]。 或者，選取不同的連接。

1. 邏輯應用程式執行完成之後，以下是 [**流覽訊息]** 動作的一些範例輸出：

   ![瀏覽訊息輸出](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>接收單一訊息

[接收訊息] 動作具有與 [瀏覽訊息] 動作相同的輸入和輸出。 使用 [接收訊息] 時，會從佇列將訊息刪除。

## <a name="receive-multiple-messages"></a>接收多個訊息

[接收訊息] 動作具有與 [瀏覽訊息] 動作相同的輸入和輸出。 使用 [接收訊息] 時，會從佇列將訊息刪除。

當執行流覽或接收時，如果佇列中沒有任何訊息，此步驟會失敗，並顯示下列輸出：  

![MQ 無訊息錯誤](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>傳送訊息

當您新增 [**傳送訊息**] 動作時，預設會選取先前設定的第一個連接。 若要建立新的連接，請選擇 [**變更連接**]。 或者，選取不同的連接。

1. 請選取有效的訊息類型：**資料包**、**回復**或**要求**  

   ![傳送訊息 .Props](media/connectors-create-api-mq/Send_Msg_Props.png)

1. 邏輯應用程式完成執行之後，以下是「**傳送訊息**」動作的一些範例輸出：

   ![傳送訊息輸出](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>連接器參考

如需有關動作和限制的技術詳細資訊（由連接器的 OpenAPI （先前稱為 Swagger）描述所描述），請參閱連接器的[參考頁面](/connectors/mq/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
