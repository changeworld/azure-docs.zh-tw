---
title: 連線到 IBM MQ 伺服器
description: 使用 Azure 或內部部署 IBM MQ 伺服器和 Azure Logic Apps 來傳送及擷取訊息
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 05/14/2020
tags: connectors
ms.openlocfilehash: e9e554fdc092e49f5a87049de0e3dc3163105f58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85609498"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>從 Azure Logic Apps 連線到 IBM MQ 伺服器

IBM MQ 連接器會傳送及擷取儲存在 IBM MQ Server 內部部署或在 Azure 中的訊息。 此連接器包含透過 TCP/IP 網路與遠端 IBM MQ Server 通訊的 Microsoft MQ 用戶端。 本文提供使用 MQ 連接器的入門指南。 您可以從瀏覽佇列上的單一訊息開始，然後再嘗試其他動作。

IBM MQ 連接器包含這些動作，但不提供任何觸發程序：

- 瀏覽單一訊息，而不從 IBM MQ 伺服器刪除訊息。
- 瀏覽批次訊息，而不從 IBM MQ 伺服器刪除訊息。
- 接收單一訊息，並從 IBM MQ 伺服器刪除訊息。
- 接收批次訊息，並從 IBM MQ 伺服器刪除訊息。
- 將單一訊息傳送至 IBM MQ 伺服器。

以下是正式支援的 IBM WebSphere MQ 版本：

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0
  * MQ 9.1

## <a name="prerequisites"></a>Prerequisites

* 如果您是使用內部部署 MQ 伺服器，請在您網路內的伺服器上[安裝內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。 安裝內部部署資料閘道的伺服器也必須安裝適用於 MQ 連接器的 .NET Framework 4.6 才能運作。

  在您完成閘道安裝之後，也必須在 Azure 中為內部部署資料閘道建立資源。 如需詳細資訊，請參閱[設定資料閘道連線](../logic-apps/logic-apps-gateway-connection.md)。

  如果您的 MQ 伺服器已在 Azure 中正式推出或提供，您就不需要使用資料閘道。

* 要在其中新增動作的邏輯應用程式。 此邏輯應用程式必須使用與您的內部部署資料閘道連線相同的位置，而且必須已有可啟動工作流程的觸發程序。

  MQ 連接器沒有任何觸發程序，因此您必須先將觸發程序新增至邏輯應用程式。 例如，您可以使用「週期」觸發程序。 如果您還不熟悉邏輯應用程式，請參閱[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>建立 MQ 連線

當您新增 MQ 動作時，如果您還沒有 MQ 連線，系統會提示您建立連線，例如：

![提供連線資訊](media/connectors-create-api-mq/connection-properties.png)

1. 如果您要連線到內部部署 MQ 伺服器，請選取 [透過內部部署資料閘道連線]。

1. 提供 MQ 伺服器的連線資訊。

   * 針對 [伺服器]，您可以輸入 MQ Server 的名稱，或輸入 IP 位址，後面接著冒號和連接埠號碼。

   * 若要使用安全通訊端層 (SSL)，請選取 [啟用 SSL?]。

     MQ 連接器目前僅支援「伺服器驗證」，不支援「用戶端驗證」。 如需詳細資訊，請參閱[連線和驗證問題](#connection-problems)。

1. 在 [閘道] 區段中，依照下列步驟執行：

   1. 從 [訂用帳戶] 清單中，選取與您的 Azure 閘道資源相關聯的 Azure 訂用帳戶。

   1. 從 [連線閘道] 清單中，選取您想要使用的 Azure 閘道資源。

1. 當您完成時，選取 [建立]。

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>連線和驗證問題

當您的邏輯應用程式嘗試連線到您的內部部署 MQ 伺服器時，您可能會收到此錯誤：

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* 如果您,是直接在 Azure 中使用 MQ 連接器，MQ 伺服器就必須使用由受信任的[憑證授權單位](https://www.ssl.com/faqs/what-is-a-certificate-authority/)發行的憑證。

* 如果您是使用內部部署資料閘道，可能的話，請嘗試使用由受信任的[憑證授權單位](https://www.ssl.com/faqs/what-is-a-certificate-authority/)發行的憑證。 不過，如果無法採取此做法，您可以使用自我簽署的憑證，這不是由受信任的[憑證授權單位](https://www.ssl.com/faqs/what-is-a-certificate-authority/)發行，而且被視為較不安全。

  若要安裝伺服器的自我簽署憑證，您可以使用 **Windows 認證管理員** (certmgr.msc) 工具。 此案例中，在內部部署資料閘道服務執行所在的本機電腦上，您必須在 **本機電腦**憑證存放區的**信任的根憑證授權單位**層級安裝憑證。

  1. 在內部部署資料閘道服務執行所在的電腦上，開啟 [開始] 功能表，尋找並選取 [管理使用者憑證]。

  1. 在 [Windows 認證管理員] 工具開啟之後，移至 [憑證-本機電腦]  >   [信任的根憑證授權單位] 資料夾，然後安裝憑證。

     > [!IMPORTANT]
     > 請確定您將憑證安裝在 [憑證-本機電腦]  >  [信任的根憑證授權單位] 存放區。

* MQ server 需要您定義要用於 TLS/SSL 連線的密碼規格。 不過，.NET 中的 SslStream 不允許您指定密碼規格的順序。 若要解決這項限制，您可以變更 MQ 伺服器設定，使其符合套件中的第一個加密規格（連接器會在 TLS/SSL 協商中傳送）。

  當您嘗試連線時，MQ 伺服器會記錄事件訊息，其中指出連線失敗是因為另一端使用了不正確的加密規格。 事件訊息包含第一個出現在清單中的加密規格。 更新通道設定中的加密規格，以符合事件訊息中的加密規格。

## <a name="browse-single-message"></a>瀏覽單一訊息

1. 在邏輯應用程式的觸發程序或其他動作底下，選取 [新增步驟]。

1. 在 [搜尋] 方塊中，輸入 `mq`，然後選取 [瀏覽訊息] 動作。

   ![選取 [瀏覽訊息] 動作](media/connectors-create-api-mq/browse-message.png)

1. 如果您尚未建立 MQ 連線，系統會提示您[建立連線](#create-connection)。

1. 建立連線之後，設定 [瀏覽訊息] 動作的屬性：

   | 屬性 | 描述 |
   |----------|-------------|
   | **佇列** | 如果與連線中指定的佇列不同，請指定該佇列。 |
   | **MessageId**、**CorrelationId**、**GroupId** 和其他屬性 | 依據不同的 MQ 訊息屬性瀏覽其他訊息 |
   | **IncludeInfo** | 若要在輸出中包含額外訊息資訊，請選取 **true**。 若要在輸出中省略額外訊息資訊，請選取 **false**。 |
   | **逾時** | 輸入一個值，來決定等候訊息到達空白佇列的等候時間。 如果未輸入任何內容，就會取出佇列中的第一個訊息，且不會花費時間等候要顯示的訊息。 |
   |||

   例如：

   ![[瀏覽訊息] 動作的屬性](media/connectors-create-api-mq/browse-message-properties.png)

1. 完成時，請在設計工具的工具列上，選取 [儲存]。 若要測試您的應用程式，請選取 [執行行]。

   執行完成之後，設計工具會顯示工作流程步驟及其狀態，讓您可以查看輸出。

1. 若要檢視每個步驟的詳細資料，按一下步驟的標題列。 若要查看步驟的輸出詳細資訊，選取 [顯示原始輸出]。

   ![瀏覽訊息輸出](media/connectors-create-api-mq/browse-message-output.png)

   以下是一些範例原始輸出：

   ![瀏覽訊息原始輸出](media/connectors-create-api-mq/browse-message-raw-output.png)

1. 如果您將 **IncludeInfo** 設定為 **true**，則會顯示額外輸出：

   ![瀏覽訊息包含資訊](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>瀏覽多個訊息

[瀏覽多個訊息] 動作有 **BatchSize** 選項，可指出應該從佇列傳回的訊息數量。 如果 **BatchSize** 沒有值，就會傳回所有的訊息。 傳回的輸出是陣列訊息。

1. 遵循先前的步驟，但改為新增**瀏覽多個訊息**動作。

1. 如果您尚未建立 MQ 連線，系統會提示您[建立連線](#create-connection)。 否則，預設會使用第一個先前設定的連線。 若要建立新的連線，選取 [變更連線]。 或者，選取其他的連線。

1. 提供動作的資訊。

1. 儲存並執行邏輯應用程式。

   邏輯應用程式完成執行之後，以下是 [瀏覽多個訊息] 動作的一些範例輸出：

   ![瀏覽多個訊息輸出範例](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>接收單一訊息

[接收訊息] 動作具有與 [瀏覽訊息] 動作相同的輸入和輸出。 當您使用 [接收訊息] 時，系統會從佇列中將訊息刪除。

## <a name="receive-multiple-messages"></a>接收多個訊息

[接收訊息] 動作具有與 [瀏覽訊息] 動作相同的輸入和輸出。 當您使用 [接收多個訊息] 時，系統會從佇列中將多個訊息刪除。

> [!NOTE]
> 在沒有任何訊息的佇列上執行瀏覽或接收動作時，動作會失敗，並顯示下列輸出：
>
> ![MQ 無訊息錯誤](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>傳送訊息

1. 遵循先前的步驟，但改為新增**傳送訊息**動作。

1. 如果您尚未建立 MQ 連線，系統會提示您[建立連線](#create-connection)。 否則，預設會使用第一個先前設定的連線。 若要建立新的連線，選取 [變更連線]。 或者，選取其他的連線。

1. 提供動作的資訊。 針對 [MessageType]，請選取有效的訊息類型：**Datagram**、**Reply** 或 **Request**。

   ![傳送訊息動作的屬性](media/connectors-create-api-mq/send-message-properties.png)

1. 儲存並執行邏輯應用程式。

   邏輯應用程式完成執行之後，以下是 [傳送訊息] 動作的一些範例輸出：

   ![傳送訊息輸出範例](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>連接器參考

如需有關動作和限制的技術詳細資訊 (它們是由連接器的 Swagger 描述)，請檢閱連接器的[參考頁面](/connectors/mq/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
