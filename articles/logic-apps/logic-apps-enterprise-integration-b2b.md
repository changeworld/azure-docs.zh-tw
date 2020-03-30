---
title: 為 B2B 企業集成方案交換消息
description: 使用企業集成包在 Azure 邏輯應用中的交易夥伴之間接收和發送 B2B 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151086"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>使用 Azure 邏輯應用和企業集成包接收和發送 B2B 消息

當您擁有定義交易夥伴和協定的集成帳戶時，您可以創建一個自動業務到企業 （B2B） 工作流，該工作流通過使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)與企業[集成包](../logic-apps/logic-apps-enterprise-integration-overview.md)一起在貿易合作夥伴之間交換消息。 Azure 邏輯應用適用于支援 AS2、X12、EDIFACT 和羅塞塔網行業標準協定的連接器。 您還可以將這些連接器與[邏輯應用中提供的其他連接器](../connectors/apis-list.md)（例如 Salesforce 和 Office 365 Outlook）進行組合。

本文演示如何使用請求觸發器創建接收 HTTP 要求的邏輯應用，使用 AS2 和 X12 操作解碼消息內容，然後使用回應操作返回回應。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有訂閱，[請註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 一個空白邏輯應用，以便您可以通過使用["請求](../connectors/connectors-native-reqres.md)觸發器"創建 B2B 工作流，隨後執行以下操作：

  * [AS2 解碼](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [條件](../logic-apps/logic-apps-control-flow-conditional-statement.md)， 根據 AS2 解碼操作成功還是失敗發送[回應](../connectors/connectors-native-reqres.md)

  * [解碼 X12 消息](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  如果您對邏輯應用是新加入的，請查看[什麼是 Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)和["快速入門：創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)"。

* 與 Azure 訂閱關聯並連結到邏輯應用的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)。 邏輯應用和集成帳戶必須存在於同一位置或 Azure 區域中。

* 您已在集成帳戶中定義的至少兩個[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)以及針對這些合作夥伴的[AS2 和 X12 協定](logic-apps-enterprise-integration-agreements.md)。

## <a name="add-request-trigger"></a>添加請求觸發器

此示例在 Azure 門戶中使用邏輯應用設計器，但可以在視覺化工作室中按照邏輯應用設計器的類似步驟操作。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開空白邏輯應用。

1. 在搜索框中，輸入`when a http request`，然後選擇 **"何時收到 HTTP 要求**以用作觸發器"。

   ![選擇"請求觸發器"以啟動邏輯應用工作流](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. 將**請求正文 JSON 架構**框留空，因為 X12 消息是一般檔案。

   ![將"請求正文 JSON 架構"留空](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. 完成後，在設計器工具列上，選擇 **"保存**"。

   此步驟將生成**HTTP POST URL，** 用於發送觸發邏輯應用的請求。 要複製此 URL，請選擇 URL 旁邊的複製圖示。

   ![為請求觸發器為接收呼叫而生成的 URL](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>添加 AS2 解碼操作

現在添加要使用的 B2B 操作。 此示例使用 AS2 和 X12 操作。

1. 在觸發程序下方，選取 [新增步驟]****。 要隱藏觸發器詳細資訊，請按一下觸發器的標題列。

   ![向邏輯應用工作流添加另一個步驟](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. 在搜索框中**選擇操作**，輸入`as2 decode`，然後選擇**AS2 解碼 （v2）**。

   ![查找並選擇"AS2 解碼 （v2）"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. 對於 **"消息"解碼**屬性，請輸入您希望 AS2 操作解碼的輸入，即 HTTP 要求觸發器`body`接收的內容。 您可以通過多種方式將此內容指定為輸入，從動態內容清單中指定或作為運算式：

   * 要從顯示可用觸發器輸出的清單中選擇，請按一下"**消息"以解碼**框。 顯示動態內容清單後，在 **"何時收到 HTTP 要求"** 下，選擇 **"正文**屬性值"，例如：

     ![從觸發器中選擇"正文"值](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * 要輸入引用觸發器輸出的`body`運算式，請按一下"**消息"以解碼**框。 顯示動態內容清單後，選擇 **"運算式**"。 在運算式編輯器中，在此處輸入運算式，然後選擇 **"確定**" ：

     `triggerOutputs()['body']`

     或者，在 **"消息解碼"** 框中，直接輸入此運算式：

     `@triggerBody()`

     運算式解析為**正文**權杖。

     ![從觸發器解析的正文輸出](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. 對於**消息標頭**屬性，輸入 AS2 操作所需的任何標頭，由 HTTP 要求觸發器接收`headers`的內容描述。

   要輸入引用觸發器輸出的`headers`運算式，請按一下 **"消息標題"** 框中。 顯示動態內容清單後，選擇 **"運算式**"。 在運算式編輯器中，在此處輸入運算式，然後選擇 **"確定**" ：

   `triggerOutputs()['Headers']`

   要使此運算式作為此權杖解析，請切換設計器和代碼視圖，例如：

   ![已解決的標頭從觸發器輸出](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>添加郵件接收通知的回應操作

要通知交易夥伴收到該消息，可以使用回應操作返回包含 AS2 消息處置通知 （MDN） 的**回應**。 通過在**AS2 解碼**操作後立即添加此操作，如果該操作失敗，邏輯應用不會繼續處理。

1. 在**AS2 解碼**操作下，選擇 **"新建步驟**"。

1. 在 **"在**搜索框下選擇操作"下，選擇 **"內置**"。 在搜尋方塊中，輸入 `condition`。 從 **"操作"** 清單中，選擇 **"條件**"。

   ![添加"條件"操作](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   現在將顯示條件形狀，包括條件是否滿足的路徑。

   ![具有決策路徑的條件形狀](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. 現在指定要評估的條件。 在"**選擇值**"框中，輸入此運算式：

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   在中間框中，確保比較操作設置為`is equal to`。 在右側框中，輸入值`Expected`。 要使運算式作為此權杖解析，請切換設計器和代碼視圖。

   ![具有決策路徑的條件形狀](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. 現在指定要返回的回應，**以便 AS2 解碼**操作是否成功。

   1. 對於**AS2 解碼**操作成功時的情況，在 **"如果為真"** 形狀中，選擇 **"添加操作**"。 在"在搜索框中**選擇操作**"，輸入`response`，然後選擇 **"回應**"。

      ![查找並選擇"回應"操作](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. 要從**AS2 解碼**操作的輸出訪問 AS2 MDN，請指定以下運算式：

      * 在**回應**操作的 **"標頭"** 屬性中，輸入此運算式：

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * 在 **"回應**"操作的 **"正文"** 屬性中，輸入以下運算式：

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 要將運算式作為權杖解析，請切換設計器和代碼視圖：

      ![用於訪問 AS2 MDN 的已解析運算式](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. 對於**AS2 解碼**操作失敗的情況，在 **"如果假"** 形狀中，選擇 **"添加操作**"。 在"在搜索框中**選擇操作**"，輸入`response`，然後選擇 **"回應**"。 設置**回應**操作以返回所需的狀態和錯誤。

1. 儲存您的邏輯應用程式。

## <a name="add-decode-x12-message-action"></a>添加解碼 X12 消息操作

1. 現在添加**解碼 X12 消息**操作。 在 **"回應**"操作下，選擇 **"添加操作**"。

1. 在搜索框中**選擇操作**，輸入`x12 decode`，然後選擇 **"解碼 X12"消息**。

   ![查找並選擇"解碼 X12 消息"操作](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. 如果 X12 操作提示您輸入連接資訊，請提供連接的名稱，選擇要使用的集成帳戶，然後選擇 **"創建**"。

   ![創建與集成帳戶的 X12 連接](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. 現在指定 X12 操作的輸入。 此示例使用 AS2 操作的輸出，該操作是消息內容，但請注意，此內容採用 JSON 物件格式，並且已對 base64 進行編碼。 因此，您必須將此內容轉換為字串。

   在**X12 一般檔案消息中解碼**框中，輸入此運算式以轉換 AS2 輸出：

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    要使運算式作為此權杖解析，請切換設計器和代碼視圖。

    ![將基本64編碼的內容轉換為字串](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. 儲存您的邏輯應用程式。

   如果需要此邏輯應用的其他步驟，例如，以 JSON 物件格式解碼消息內容和輸出該內容，請繼續構建邏輯應用。

現在，您已經設置完 B2B 邏輯應用。 在真實應用中，您可能希望將解碼的 X12 資料存儲在業務線 （LOB） 應用或資料存儲中。 例如，請參閱以下文章：

* [從 Azure Logic Apps 連線至 SAP 系統](../logic-apps/logic-apps-using-sap-connector.md)
* [藉由使用 SSH 和 Azure Logic Apps 來監視、建立及管理 SFTP 檔案](../connectors/connectors-sftp-ssh.md)

要連接自己的 LOB 應用並在邏輯應用中使用這些 API，可以添加更多操作或[編寫自訂 API。](../logic-apps/logic-apps-create-api-app.md)

## <a name="next-steps"></a>後續步驟

* [接收和回應傳入的 HTTPS 呼叫](../connectors/connectors-native-reqres.md)
* [為 B2B 企業集成交換 AS2 消息](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [為 B2B 企業集成交換 X12 消息](../logic-apps/logic-apps-enterprise-integration-x12.md)
* 瞭解有關[企業集成包的更多](../logic-apps/logic-apps-enterprise-integration-overview.md)