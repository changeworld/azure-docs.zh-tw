---
title: 連接到 IBM MQ server
description: 使用 Azure 或內部部署的 IBM MQ server 和 Azure Logic Apps 來傳送和取出訊息
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80410171"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>從 Azure Logic Apps 連接到 IBM MQ server

IBM MQ 連接器會傳送和抓取儲存在內部部署或 Azure 中的 IBM MQ server 的訊息。 此連接器包含透過 TCP/IP 網路與遠端 IBM MQ Server 通訊的 Microsoft MQ 用戶端。 本文提供使用 MQ 連接器的入門指南。 您可以從流覽佇列上的單一訊息開始，然後再嘗試其他動作。

IBM MQ connector 包含這些動作，但不提供任何觸發程式：

- 流覽單一訊息，而不從 IBM MQ server 刪除訊息。
- 流覽一批訊息，而不刪除 IBM MQ server 中的訊息。
- 接收單一訊息，並從 IBM MQ server 刪除訊息。
- 接收一批訊息，並從 IBM MQ server 刪除訊息。
- 將單一訊息傳送至 IBM MQ server。

以下是正式支援的 IBM WebSphere MQ 版本：

  * MQ 7.5
  * MQ 8.0
  * MQ 9。0

## <a name="prerequisites"></a>先決條件

* 如果您使用內部部署 MQ 伺服器，請將內部[部署資料閘道安裝](../logic-apps/logic-apps-gateway-install.md)在您網路內的伺服器上。 安裝內部部署資料閘道的伺服器也必須安裝 .NET Framework 4.6，MQ connector 才能正常操作。

  在您完成安裝閘道之後，您也必須在 Azure 中為內部部署資料閘道建立資源。 如需詳細資訊，請參閱[設定資料閘道聯](../logic-apps/logic-apps-gateway-connection.md)機。

  如果您的 MQ 伺服器已公開可用或在 Azure 中提供，您就不需要使用資料閘道。

* 您想要在其中新增 MQ 動作的邏輯應用程式。 此邏輯應用程式必須使用與您的內部部署資料閘道連線相同的位置，而且必須已有啟動工作流程的觸發程式。

  MQ 連接器沒有任何觸發程式，因此您必須先將觸發程式新增至邏輯應用程式。 例如，您可以使用「週期」觸發程式。 如果您不熟悉邏輯應用程式，請嘗試此[快速入門來建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>建立 MQ 連接

當您新增 MQ 動作時，如果您還沒有 MQ 連接，系統會提示您建立連接，例如：

![提供連接資訊](media/connectors-create-api-mq/connection-properties.png)

1. 如果您要連線到內部部署 MQ 伺服器，請選取 [透過內部**部署資料網**關聯機]。

1. 提供 MQ 伺服器的連接資訊。

   * 針對 [伺服器]****，您可以輸入 MQ Server 的名稱，或輸入 IP 位址，後面接著冒號和連接埠號碼。

   * 若要使用安全通訊端層（SSL），請選取 [**啟用 SSL？**]。

     MQ 連接器目前僅支援「伺服器驗證」，而非「用戶端驗證」。 如需詳細資訊，請參閱連線[和驗證問題](#connection-problems)。

1. 在 [**閘道**] 區段中，依照下列步驟執行：

   1. 從 [**訂**用帳戶] 清單中，選取與您的 azure 閘道資源相關聯的 azure 訂用帳戶。

   1. 從 [連線**閘道**] 清單中，選取您想要使用的 Azure 閘道資源。

1. 當您完成時，選取 [建立]  。

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>連接和驗證問題

當您的邏輯應用程式嘗試連線到您的內部部署 MQ 伺服器時，您可能會收到此錯誤：

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* 如果您直接在 Azure 中使用 MQ 連接器，MQ 伺服器就必須使用由受信任的[憑證授權單位](https://www.ssl.com/faqs/what-is-a-certificate-authority/)單位所發行的憑證。

* 如果您使用內部部署資料閘道，請嘗試使用受信任的[憑證授權單位](https://www.ssl.com/faqs/what-is-a-certificate-authority/)單位所發行的憑證（可能的話）。 不過，如果此選項不可行，您可以使用自我簽署的憑證，這不是由受信任的[憑證授權單位](https://www.ssl.com/faqs/what-is-a-certificate-authority/)單位所發出，而且被視為較不安全。

  若要安裝伺服器的自我簽署憑證，您可以使用**Windows 認證管理員**（certmgr.msc）工具。 在此案例中，內部部署資料閘道服務執行所在的本機電腦上，您必須在 [**信任的根憑證授權**單位] 層級的 [**本機電腦**] 憑證存放區中安裝憑證。

  1. 在內部部署資料閘道服務執行所在的電腦上，開啟 [開始] 功能表，尋找並選取 [**管理使用者憑證**]。

  1. Windows 認證管理員工具開啟之後，請移至 [**憑證-本機電腦** >  **信任的根憑證授權**單位] 資料夾，並安裝憑證。

     > [!IMPORTANT]
     > 請確定您在 [**憑證-本機電腦** > **信任的根憑證授權**單位] 存放區中安裝憑證。

* MQ server 會要求您定義要用於 SSL 連線的加密規格。 不過，.NET 中的 SsLStream 不允許您指定加密規格的順序。 若要解決這項限制，您可以變更 MQ 伺服器設定，使其符合連接器在 SSL 協商中傳送的第一個加密規格。

  當您嘗試連接時，MQ server 會記錄指出連接失敗的事件訊息，因為另一端使用了不正確的加密規格。 事件訊息包含第一個出現在清單中的加密規格。 更新通道設定中的加密規格，以符合事件訊息中的加密規格。

## <a name="browse-single-message"></a>流覽單一訊息

1. 在邏輯應用程式的觸發程式或其他動作底下，選取 [**新增步驟**]。

1. 在 [搜尋] 方塊中`mq`，輸入，然後選取 [**流覽訊息]** 動作。

   ![選取 [流覽訊息] 動作](media/connectors-create-api-mq/browse-message.png)

1. 如果您尚未建立 MQ 連線，系統會提示您[建立該連接](#create-connection)。

1. 建立連線之後，請設定 [**流覽訊息動作]** 的屬性：

   | 屬性 | 描述 |
   |----------|-------------|
   | **佇列** | 如果與連接中指定的佇列不同，請指定該佇列。 |
   | **MessageId**、 **CorrelationId**、 **GroupId**和其他屬性 | 流覽以不同 MQ message 屬性為基礎的訊息 |
   | **IncludeInfo** | 若要在輸出中包含其他訊息資訊，請選取 [ **true**]。 若要省略輸出中的其他訊息資訊，請選取 [ **false**]。 |
   | **逾時** | 輸入值，以決定等候訊息到達空白佇列的時間長度。 如果未輸入任何內容，就會取出佇列中的第一個訊息，且不會花費時間等候要顯示的訊息。 |
   |||

   例如：

   ![[流覽訊息] 動作的屬性](media/connectors-create-api-mq/browse-message-properties.png)

1. 當您完成時，請在設計工具工具列上選取 [**儲存**]。 若要測試您的應用程式，請選取 [**執行**]。

   執行完成之後，設計工具會顯示工作流程步驟及其狀態，讓您可以查看輸出。

1. 若要查看每個步驟的詳細資料，請按一下步驟的標題列。 若要查看有關步驟輸出的詳細資訊，請選取 [**顯示原始輸出**]。

   ![瀏覽訊息輸出](media/connectors-create-api-mq/browse-message-output.png)

   以下是一些範例原始輸出：

   ![瀏覽訊息原始輸出](media/connectors-create-api-mq/browse-message-raw-output.png)

1. 如果您將**IncludeInfo**設定為**true**，則會顯示其他輸出：

   ![瀏覽訊息包含資訊](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>瀏覽多個訊息

[**流覽訊息]** 動作包含一個**BatchSize**選項，指出要從佇列傳回多少訊息。 如果**BatchSize**沒有值，則會傳回所有訊息。 傳回的輸出是陣列訊息。

1. 遵循先前的步驟，但改為新增 [**流覽訊息]** 動作。

1. 如果您尚未建立 MQ 連線，系統會提示您[建立該連接](#create-connection)。 否則，預設會使用第一個先前設定的連接。 若要建立新的連接，請選取 [**變更連接**]。 或者，選取不同的連接。

1. 提供動作的資訊。

1. 儲存並執行邏輯應用程式。

   邏輯應用程式完成執行之後，以下是 [**流覽訊息]** 動作的一些範例輸出：

   ![範例「流覽訊息」輸出](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>接收單一訊息

[接收訊息]**** 動作具有與 [瀏覽訊息]**** 動作相同的輸入和輸出。 當您使用 [**接收訊息**] 時，訊息會從佇列中刪除。

## <a name="receive-multiple-messages"></a>接收多個訊息

[接收訊息]**** 動作具有與 [瀏覽訊息]**** 動作相同的輸入和輸出。 當您使用**接收訊息**時，訊息會從佇列中刪除。

> [!NOTE]
> 在沒有任何訊息的佇列上執行 [流覽] 或 [接收] 動作時，此動作會失敗並顯示下列輸出：
>
> ![MQ 「沒有訊息」錯誤](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>傳送訊息

1. 遵循先前的步驟，但改為新增 [**傳送訊息**] 動作。

1. 如果您尚未建立 MQ 連線，系統會提示您[建立該連接](#create-connection)。 否則，預設會使用第一個先前設定的連接。 若要建立新的連接，請選取 [**變更連接**]。 或者，選取不同的連接。

1. 提供動作的資訊。 針對**MessageType**，請選取有效的訊息類型：**資料包**、**回復**或**要求**

   ![[傳送訊息動作] 的屬性](media/connectors-create-api-mq/send-message-properties.png)

1. 儲存並執行邏輯應用程式。

   邏輯應用程式完成執行之後，以下是「**傳送訊息**」動作的一些範例輸出：

   ![範例「傳送訊息」輸出](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>連接器參考

如需有關動作和限制的技術詳細資料（如連接器的 Swagger 描述所述），請參閱連接器的[參考頁面](/connectors/mq/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
