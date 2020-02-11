---
title: 呼叫 HTTP 和 HTTPS 端點
description: 使用 Azure Logic Apps 將連出要求傳送至 HTTP 和 HTTPS 端點
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 9c1b2af8d06c9466ed6c82308de941b43510238a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118001"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 將撥出電話傳送至 HTTP 或 HTTPS 端點

透過[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和內建的 HTTP 觸發程式或動作，您可以建立自動化的工作和工作流程，定期將要求傳送至任何 HTTP 或 HTTPS 端點。 若要改為接收和回應傳入的 HTTP 或 HTTPS 呼叫，請使用內建的[要求觸發程式或回應動作](../connectors/connectors-native-reqres.md)。

例如，您可以在指定的排程上檢查該端點，以監視網站的服務端點。 當特定事件發生在該端點時（例如您的網站停止運作），事件會觸發邏輯應用程式的工作流程，並執行指定的動作。

若要定期檢查或*輪詢*端點，您可以使用 HTTP 觸發程式做為工作流程中的第一個步驟。 在每次檢查時，觸發程序會將呼叫或「要求」傳送至端點。 端點的回應會決定是否執行邏輯應用程式的工作流程。 觸發程序會將回應的任何內容，傳遞至邏輯應用程式中的動作。

您可以使用 HTTP 動作作為工作流程中的任何其他步驟，在您想要的時候檢查端點。 端點的回應會決定工作流程的剩餘動作如何執行。

根據目標端點的功能，HTTP 連接器支援傳輸層安全性（TLS）版本1.0、1.1 和1.2。 Logic Apps 使用支援的最高版本來與端點協調。 因此，例如，如果端點支援1.2，連接器會先使用1.2。 否則，連接器會使用下一個支援的最高版本。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要呼叫的目標端點 URL

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請參閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md)？

* 您想要從中呼叫目標端點的邏輯應用程式。 若要開始使用 HTTP 觸發程式，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP 動作，請使用您想要的任何觸發程式來啟動邏輯應用程式。 這個範例會使用 HTTP 觸發程式做為第一個步驟。

## <a name="add-an-http-trigger"></a>新增 HTTP 觸發程式

此內建觸發程式會對指定的端點 URL 進行 HTTP 呼叫，並傳迴響應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟空白邏輯應用程式。

1. 在 [**選擇動作**] 下的 [搜尋] 方塊中，輸入 "HTTP" 作為篩選準則。 從**觸發**程式清單中，選取**HTTP**觸發程式。

   ![選取 HTTP 觸發程序](./media/connectors-native-http/select-http-trigger.png)

   這個範例會將觸發程式重新命名為「HTTP 觸發程式」，讓步驟有更具描述性的名稱。 此外，此範例稍後會新增 HTTP 動作，而且這兩個名稱都必須是唯一的。

1. 提供您想要包含在目標端點呼叫中的[HTTP 觸發程式參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)值。 針對您想要讓觸發程式檢查目標端點的頻率，設定週期。

   ![輸入 HTTP 觸發程序參數](./media/connectors-native-http/http-trigger-parameters.png)

   如果您選取 [**無**] 以外的驗證類型，驗證設定會根據您的選擇而有所不同。 如需適用于 HTTP 的驗證類型的詳細資訊，請參閱下列主題：

   * [將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用受控識別來驗證資源的存取權](../logic-apps/create-managed-service-identity.md)

1. 若要加入其他可用的參數，請開啟 [**加入新的參數**] 清單，然後選取您想要的參數。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 當您完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

## <a name="add-an-http-action"></a>新增 HTTP 動作

此內建動作會對指定的端點 URL 進行 HTTP 呼叫，並傳迴響應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

   這個範例會使用 HTTP 觸發程式做為第一個步驟。

1. 在您要新增 HTTP 動作的步驟底下，選取 [**新增步驟**]。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選取顯示的加號（ **+** ），然後選取 [**新增動作**]。

1. 在 [**選擇動作**] 下的 [搜尋] 方塊中，輸入 "HTTP" 作為篩選準則。 從 [**動作**] 清單中，選取 [ **HTTP** ] 動作。

   ![選取 HTTP 動作](./media/connectors-native-http/select-http-action.png)

   這個範例會將動作重新命名為「HTTP 動作」，讓步驟有更具描述性的名稱。

1. 提供您想要包含在目標端點呼叫中的[HTTP 動作參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)值。

   ![輸入 HTTP 動作參數](./media/connectors-native-http/http-action-parameters.png)

   如果您選取 [**無**] 以外的驗證類型，驗證設定會根據您的選擇而有所不同。 如需適用于 HTTP 的驗證類型的詳細資訊，請參閱下列主題：

   * [將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用受控識別來驗證資源的存取權](../logic-apps/create-managed-service-identity.md)

1. 若要加入其他可用的參數，請開啟 [**加入新的參數**] 清單，然後選取您想要的參數。

1. 當您完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

## <a name="content-with-multipartform-data-type"></a>具有多部分/表單資料類型的內容

若要處理在 HTTP 要求中具有 `multipart/form-data` 類型的內容，您可以使用此格式，將包含 `$content-type` 和 `$multipart` 屬性的 JSON 物件新增至 HTTP 要求的主體。

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

例如，假設您有一個邏輯應用程式，它會使用支援 `multipart/form-data` 類型的網站 API，將 Excel 檔案的 HTTP POST 要求傳送至網站。 以下是此動作可能的外觀：

![多部分表單資料](./media/connectors-native-http/http-action-multipart.png)

以下是在基礎工作流程定義中顯示 HTTP 動作之 JSON 定義的相同範例：

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>連接器參考

如需觸發程式和動作參數的詳細資訊，請參閱下列各節：

* [HTTP 觸發程式參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP 動作參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>輸出詳細資料

以下是 HTTP 觸發程式或動作輸出的詳細資訊，會傳回下列資訊：

| 屬性名稱 | 類型 | 描述 |
|---------------|------|-------------|
| 頁首 | object | 要求的標頭 |
| body | object | JSON 物件 | 具有來自要求之本文內容的物件 |
| 狀態碼 | int | 來自要求的狀態碼 |
|||

| 狀態碼 | 描述 |
|-------------|-------------|
| 200 | 確定 |
| 202 | 已接受 |
| 400 | 不正確的要求 |
| 401 | Unauthorized |
| 403 | 已禁止 |
| 404 | 找不到 |
| 500 | 內部伺服器錯誤。 發生未知錯誤。 |
|||

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
