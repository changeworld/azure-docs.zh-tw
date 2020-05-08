---
title: 適用于 B2B 企業整合案例的 Exchange 訊息
description: 使用企業整合套件在 Azure Logic Apps 中的交易夥伴之間接收和傳送 B2B 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: b576fc99e2f203bb3d690a8135ee76cee26b3de8
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792355"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>使用 Azure Logic Apps 和企業整合套件來接收和確認 B2B AS2 訊息

當您擁有定義交易夥伴和協定的整合帳戶時，您可以使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)與[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)，建立自動化的企業對企業（B2B）工作流程，以交換交易夥伴之間的訊息。 Azure Logic Apps 適用于支援 AS2、X12、EDIFACT 和 RosettaNet 產業標準通訊協定的連接器。 您也可以將這些連接器與[Logic Apps 中可用](../connectors/apis-list.md)的其他連接器結合，例如 Salesforce 和 Office 365 Outlook。

本文說明如何建立邏輯應用程式，以使用要求觸發程式來接收 HTTP 要求、使用 AS2 和 X12 動作來解碼訊息內容，然後使用回應動作來傳迴響應。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有訂用帳戶，請[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 空白的邏輯應用程式，讓您可以使用後面接著這些動作的[要求](../connectors/connectors-native-reqres.md)觸發程式來建立 B2B 工作流程：

  * [AS2 解碼](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [條件](../logic-apps/logic-apps-control-flow-conditional-statement.md)，根據 AS2 解碼動作是成功還是失敗來傳送[回應](../connectors/connectors-native-reqres.md)。

  * [將 X12 訊息解碼](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  如果您不熟悉邏輯應用程式，請參閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 與您的 Azure 訂用帳戶相關聯，並連結至邏輯應用程式的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)。 您的邏輯應用程式和整合帳戶必須存在於相同的位置或 Azure 區域中。

* 至少有兩個您已在整合帳戶中定義的[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，以及這些合作夥伴的[AS2 和 X12 協定](logic-apps-enterprise-integration-agreements.md)。

## <a name="add-request-trigger"></a>新增要求觸發程式

這個範例會使用 Azure 入口網站中的邏輯應用程式設計工具，但您可以針對 Visual Studio 中的邏輯應用程式設計工具，遵循類似的步驟。

1. 在[Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟空白邏輯應用程式。

1. 在 [搜尋] 方塊中`when a http request`，輸入，然後選取 [**收到 HTTP 要求時**] 作為觸發程式。

   ![選取 [要求觸發程式] 以啟動邏輯應用程式工作流程](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. 將 [**要求本文 JSON 架構**] 方塊保留空白，因為 X12 訊息是一般檔案。

   ![將 [要求本文 JSON 架構] 保留空白](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. 當您完成時，請在設計工具工具列上選取 [**儲存**]。

   此步驟會產生**HTTP POST URL** ，以用於傳送觸發邏輯應用程式的要求。 若要複製此 URL，請選取 URL 旁邊的複製圖示。

   ![為要求觸發程式接收呼叫所產生的 URL](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>新增 AS2 解碼動作

現在，新增您想要使用的 B2B 動作。 這個範例會使用 AS2 和 X12 動作。

1. 在觸發程序下方，選取 [新增步驟]****。 若要隱藏觸發程式的詳細資料，請按一下觸發程式的標題列。

   ![將另一個步驟新增至邏輯應用程式工作流程](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. 在 [**選擇動作**] 下的 [搜尋] 方塊`as2 decode`中，輸入，然後選取 [ **AS2 解碼（v2）**]。

   ![尋找並選取 [AS2 解碼（v2）]](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. 針對 [**要**解碼的訊息] 屬性，輸入您要讓 AS2 動作解碼的輸入，這是 HTTP `body`要求觸發程式所收到的內容。 您可以透過多種方式，將此內容指定為輸入，不論是從動態內容清單或運算式：

   * 若要從顯示可用觸發程式輸出的清單中選取，請在 [要解碼的**訊息**] 方塊內按一下。 動態內容清單出現後，在 [**收到 HTTP 要求時**] 底下，選取 [**主體**屬性值]，例如：

     ![從觸發程式選取 [主體] 值](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * 若要輸入參考觸發程式`body`輸出的運算式，請在 [要解碼的**訊息**] 方塊內按一下。 動態內容清單出現之後，選取 [**運算式**]。 在 [運算式編輯器] 中，于此處輸入運算式，然後選取 **[確定]**：

     `triggerOutputs()['body']`

     或者，在 [**要**解碼的訊息] 方塊中，直接輸入下列運算式：

     `@triggerBody()`

     運算式會解析為**主體**token。

     ![從觸發程式解析的主體輸出](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. 針對 [**訊息標頭**] 屬性，輸入 AS2 動作所需的任何標頭，由 HTTP `headers`要求觸發程式收到的內容描述。

   若要輸入參考觸發程式`headers`輸出的運算式，請按一下 [**訊息標頭**] 方塊內部。 動態內容清單出現之後，選取 [**運算式**]。 在 [運算式編輯器] 中，于此處輸入運算式，然後選取 **[確定]**：

   `triggerOutputs()['Headers']`

   若要取得此運算式來解析為這個 token，請在設計工具和程式碼視圖之間切換，例如：

   ![已解析的標頭從觸發程式輸出](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>新增訊息回條通知的回應動作

若要通知交易夥伴已收到該訊息，您可以使用 [**回應**] 動作傳回包含 AS2 訊息處置通知（MDN）的回應。 在**AS2**解碼動作後面加入此動作之後，如果動作失敗，邏輯應用程式就不會繼續處理。

1. 在 [ **AS2**解碼] 動作底下，選取 [**新增步驟**]。

1. 在 [**選擇動作**] 底下的 [搜尋] 方塊中，選取 [**內建**]。 在搜尋方塊中，輸入 `condition`。 從 [**動作**] 清單中，選取 [**條件**]。

   ![新增「條件」動作](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   此時會出現 [條件] 圖形，包括是否符合條件的路徑。

   ![具有決策路徑的條件圖形](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. 現在指定要評估的條件。 在 [**選擇值**] 方塊中，輸入此運算式：

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   在中間方塊中，請確定比較作業設定為`is equal to`。 在右側方塊中，輸入值`Expected`。 若要取得要解析為此標記的運算式，請在設計工具和程式碼視圖之間切換。

   ![具有決策路徑的條件圖形](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. 現在指定回應，以傳回**AS2**解碼動作是否成功。

   1. 針對**AS2**解碼動作成功的情況，請在 [**若為 true** ] 圖形中，選取 [**新增動作**]。 在 [**選擇動作**] 下的 [搜尋] 方塊`response`中，輸入，然後選取 [**回應**]。

      ![尋找並選取 [回應] 動作](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. 若要從**as2**解碼動作的輸出存取 as2 MDN，請指定下列運算式：

      * 在 [**回應**] 動作的 [**標頭**] 屬性中，輸入此運算式：

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * 在 [**回應**] 動作的 [**主體**] 屬性中，輸入此運算式：

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 若要取得要解析為標記的運算式，請在設計工具和程式碼視圖之間切換：

      ![用來存取 AS2 MDN 的已解析運算式](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. 針對**AS2**解碼動作失敗的情況，請在 [**若為 false** ] 圖形中選取 [**新增動作**]。 在 [**選擇動作**] 下的 [搜尋] 方塊`response`中，輸入，然後選取 [**回應**]。 設定**回應**動作，以傳回您想要的狀態和錯誤。

1. 儲存您的邏輯應用程式。

## <a name="add-decode-x12-message-action"></a>新增解碼 X12 訊息動作

1. 現在新增 [將**X12 訊息**解碼] 動作。 在 [**回應**] 動作底下，選取 [**新增動作**]。

1. 在 [**選擇動作**] 下的 [搜尋] 方塊`x12 decode`中，輸入，然後選取 [將**X12 訊息**解碼]。

   ![尋找並選取 [將 X12 訊息解碼] 動作](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. 如果 X12 動作提示您輸入連線資訊，請提供連接的名稱、選取您要使用的整合帳戶，然後選取 [**建立**]。

   ![建立與整合帳戶的 X12 連線](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. 現在指定 X12 動作的輸入。 這個範例會使用 AS2 動作的輸出，也就是訊息內容，但請注意，此內容是 JSON 物件格式，而且是以 base64 編碼。 因此，您必須將此內容轉換成字串。

   在 [**要**解碼的 X12 一般檔案訊息] 方塊中，輸入此運算式來轉換 AS2 輸出：

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    若要取得要解析為此標記的運算式，請在設計工具和程式碼視圖之間切換。

    ![將 base64 編碼的內容轉換成字串](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. 儲存您的邏輯應用程式。

   如果您需要此邏輯應用程式的額外步驟，例如，若要將訊息內容解碼，並以 JSON 物件格式輸出該內容，請繼續建立邏輯應用程式。

您現在已完成設定 B2B 邏輯應用程式。 在真實世界的應用程式中，您可能會想要將解碼的 X12 資料儲存在企業營運（LOB）應用程式或資料存放區中。 例如，請參閱下列文章：

* [從 Azure Logic Apps 連線至 SAP 系統](../logic-apps/logic-apps-using-sap-connector.md)
* [藉由使用 SSH 和 Azure Logic Apps 來監視、建立及管理 SFTP 檔案](../connectors/connectors-sftp-ssh.md)

若要連接您自己的 LOB 應用程式，並在邏輯應用程式中使用這些 Api，您可以新增更多動作或[撰寫自訂 api](../logic-apps/logic-apps-create-api-app.md)。

## <a name="next-steps"></a>後續步驟

* [接收和回應連入的 HTTPS 呼叫](../connectors/connectors-native-reqres.md)
* [適用于 B2B 企業整合的交換 AS2 訊息](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Exchange X12 訊息以進行 B2B 企業整合](../logic-apps/logic-apps-enterprise-integration-x12.md)
* 深入瞭解[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)
