---
title: 使用 HTTP 或 HTTPS 呼叫服務終結點
description: 從 Azure 邏輯應用向服務終結點發送出站 HTTP 或 HTTPS 請求
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 6c52f2df34faf441ab70b48b11bbc393ebcecb65
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617616"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>透過 HTTP 或 HTTPS 從 Azure 邏輯應用呼叫服務終結點

使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和內建的 HTTP 觸發器或操作,您可以建立自動任務和工作流,透過 HTTP 或 HTTPS 向服務終結點發送請求。 例如,您可以通過按特定計劃檢查終結點來監視網站的服務終結點。 當指定的事件發生在該終結點(如您的網站關閉)時,該事件將觸發邏輯應用的工作流並運行該工作流中的操作。 如果要接收與回應入站 HTTPS 呼叫,請使用內建[的請求觸發器或回應操作](../connectors/connectors-native-reqres.md)。

> [!NOTE]
> 基於目標終結點的功能,HTTP 連接器支援傳輸層安全 (TLS) 版本 1.0、1.1 和 1.2。 邏輯應用與終結點協商,協商使用可能支援的最高版本。 例如,如果終結點支援 1.2,則連接器首先使用 1.2。 否則,連接器將使用下一個受支援的最高版本。

要按定期計劃檢查或*輪詢*終結點,[請添加 HTTP 觸發器](#http-trigger)作為工作流中的第一步。 每次觸發器檢查終結點時,觸發器都會呼叫終結點或向終結點發送*請求*。 端點的回應會決定是否執行邏輯應用程式的工作流程。 觸發器將終結點回應中的任何內容傳遞到邏輯應用中的操作。

要從工作串流的任意位置呼叫終結點,[請新增 HTTP 作業](#http-action)。 端點的回應會決定工作流程的剩餘動作如何執行。

> [!IMPORTANT]
> 如果 HTTP 觸發器或操作包含這些標頭,邏輯應用將從生成的請求消息中刪除這些標頭,而不會顯示任何警告或錯誤:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`除這些例外情況外:`Content-Disposition``Content-Encoding`和`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> 儘管邏輯應用不會阻止您保存使用這些標頭的 HTTP 觸發器或操作的邏輯應用,但邏輯應用會忽略這些標頭。

本文演示如何向邏輯應用的工作流添加 HTTP 觸發器或操作。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 要呼叫的目標終結點的網址

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您是邏輯應用的新增功能,請查看什麼是[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)?

* 要從何處調用目標終結點的邏輯應用。 要從 HTTP 觸發器開始,[請建立空白邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 要使用 HTTP 操作,請使用所需的任何觸發器啟動邏輯應用。 本示例使用 HTTP 觸發器作為第一步。

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>新增 HTTP 觸發器

此內建觸發器對終結點的指定 URL 進行 HTTP 調用並返回回應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用設計器中打開空白邏輯應用。

1. 在設計器的搜尋框下,選擇**內建**。 在搜尋方塊中，輸入 `http` 作為篩選條件。 從**觸發器**列表中選擇**HTTP**觸發器。

   ![選取 HTTP 觸發程序](./media/connectors-native-http/select-http-trigger.png)

   本示例將觸發器重命名為"HTTP 觸發器",以便步驟具有更具描述性的名稱。 此外,該示例稍後添加一個 HTTP 操作,並且兩個名稱都必須是唯一的。

1. 提供要在調用目標終結點時包含的[HTTP 觸發器參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)的值。 設置重複,以表示您希望觸發器檢查目標終結點的頻率。

   ![輸入 HTTP 觸發程序參數](./media/connectors-native-http/http-trigger-parameters.png)

   如果選擇「**無」** 以外的身份驗證類型,則身份驗證設置會因您選擇的情況而異。 有關可用於 HTTP 的認證類型的詳細資訊,請參閱以下主題:

   * [傳出站撥號加入認證](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用託管識別對資源的身份驗證權限](../logic-apps/create-managed-service-identity.md)

1. 要添加其他可用參數,請打開 **「添加新參數**清單」,然後選擇所需的參數。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 完成後,請記住保存邏輯應用。 在設計工具的工具列上，選取 [儲存]****。

<a name="http-action"></a>

## <a name="add-an-http-action"></a>新增 HTTP 作業

此內建操作對終結點的指定 URL 發出 HTTP 呼叫並傳回回應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

   本示例使用 HTTP 觸發器作為第一步。

1. 在要添加 HTTP 操作的步驟下,選擇 **「新建步驟**」。。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇顯示的加號**+**(),然後選擇 **「添加操作**」。。

1. 在 [選擇動作]**** 底下，選取 [內建]****。 在搜尋方塊中，輸入 `http` 作為篩選條件。 從 **「操作」** 清單中,選擇**HTTP**操作。

   ![選取 HTTP 動作](./media/connectors-native-http/select-http-action.png)

   本示例將操作重命名為"HTTP 操作",以便步驟具有更具描述性的名稱。

1. 提供要在調用目標終結點時包含的[HTTP 操作參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)的值。

   ![輸入 HTTP 動作參數](./media/connectors-native-http/http-action-parameters.png)

   如果選擇「**無」** 以外的身份驗證類型,則身份驗證設置會因您選擇的情況而異。 有關可用於 HTTP 的認證類型的詳細資訊,請參閱以下主題:

   * [傳出站撥號加入認證](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用託管識別對資源的身份驗證權限](../logic-apps/create-managed-service-identity.md)

1. 要添加其他可用參數,請打開 **「添加新參數**清單」,然後選擇所需的參數。

1. 完成後,請記住保存邏輯應用。 在設計工具的工具列上，選取 [儲存]****。

## <a name="content-with-multipartform-data-type"></a>具有多部份/表單型資料類型的內容

要處理具有`multipart/form-data`HTTP 請求類型的內容,可以使用此格式添加`$content-type``$multipart`包含 HTTP 請求正文 和 屬性的 JSON 物件。

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

例如,假設您有一個邏輯應用,它使用該網站的 API 向網站發送 Excel 檔的 HTTP POST 請求,該 API`multipart/form-data`支援該類型。 此動作的外觀:

![多部份表單資料](./media/connectors-native-http/http-action-multipart.png)

下面是在基礎工作流定義中顯示 HTTP 操作的 JSON 定義的同一示例:

```json
"HTTP_action": {
   "inputs": {
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

有關觸發器和操作參數的詳細資訊,請參閱以下部分:

* [HTTP 觸發參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP 動作參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>輸出詳細資料

以下是有關 HTTP 觸發器或操作的輸出的詳細資訊,傳回此資訊:

| 屬性名稱 | 類型 | 描述 |
|---------------|------|-------------|
| headers | 物件 (object) | 要求中的標頭 |
| body | 物件 (object) | JSON 物件 | 具有要求中正文內容的物件 |
| 狀態碼 | int | 要求中的狀態代碼 |
|||

| 狀態碼 | 描述 |
|-------------|-------------|
| 200 | [確定] |
| 202 | 已接受 |
| 400 | 不正確的要求 |
| 401 | 未經授權 |
| 403 | 禁止 |
| 404 | 找不到 |
| 500 | 內部伺服器錯誤。 發生未知錯誤。 |
|||

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
