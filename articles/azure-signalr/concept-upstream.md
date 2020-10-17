---
title: Azure SignalR Service 中的上游設定
description: 取得上游設定和上游訊息通訊協定的簡介。
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 1d51f5e8d2fac1e2b180a608c840d0a322e76271
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143248"
---
# <a name="upstream-settings"></a>上游設定

上游是一項預覽功能，可讓 Azure SignalR Service 將訊息和連接事件傳送到無伺服器模式中的一組端點。 您可以使用上游以無伺服器模式從用戶端叫用中樞方法，並讓端點在用戶端連接連線或中斷連線時收到通知。

> [!NOTE]
> 只有無伺服器模式可以設定上游設定。

## <a name="details-of-upstream-settings"></a>上游設定的詳細資料

上游設定包含一份順序相關專案清單。 每個專案都包含：

* URL 範本，指定訊息傳送至的位置。
* 一組規則。
* 驗證設定。 

當指定的事件發生時，會依序逐一檢查項目的規則。 訊息將會傳送至第一個相符專案的上游 URL。

### <a name="url-template-settings"></a>URL 範本設定

您可以參數化 URL 以支援各種模式。 有三個預先定義的參數：

|預先定義的參數|描述|
|---------|---------|
|資訊中心| 中樞是 Azure SignalR Service 的概念。 中樞是隔離的單位。 使用者和訊息傳遞的範圍受限於中樞。|
|組| 類別可以是下列其中一個值： <ul><li>**連接**：連線存留期事件。 當用戶端連接已連線或中斷連線時，就會引發此功能。 它包含已連線和已中斷連線的事件。</li><li>**訊息**：用戶端叫用中樞方法時引發。 除了 **連接** 類別中的其他所有事件之外，還包括其他所有事件。</li></ul>|
|事件類| 在 [ **訊息** ] 類別目錄中，事件是用戶端傳送的 [調用訊息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) 中的目標。 針對 [ **連接** ] 類別，只會使用 [ *已連接* ] 和 [已 *中斷* 連線]。|

這些預先定義的參數可用於 URL 模式。 當您評估上游 URL 時，參數將會取代為指定的值。 例如： 
```
http://host.com/{hub}/api/{category}/{event}
```
當連線「聊天」中樞內的用戶端連線時，會將訊息傳送到此 URL：
```
http://host.com/chat/api/connections/connected
```
當「聊天」中樞內的用戶端叫用中樞方法時 `broadcast` ，會將訊息傳送到此 URL：
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rule-settings"></a>規則設定

您可以分別設定 *中樞規則*、 *類別規則*和 *事件規則* 的規則。 比對規則支援三種格式。 以事件規則做為範例：
- 使用星號 ( * ) 來比對任何事件。
- 使用逗號 (，) 來聯結多個事件。 例如，會比 `connected, disconnected` 對已連線和已中斷連線的事件。
- 使用完整的事件名稱來與事件相符。 例如， `connected` 符合連接的事件。

> [!NOTE]
> 如果您正在使用 Azure Functions 和 [SignalR 觸發](../azure-functions/functions-bindings-signalr-service-trigger.md)程式，SignalR 觸發程式將會以下列格式公開單一端點： `https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>` 。
> 您可以直接將 url 範本設定為此 url。

### <a name="authentication-settings"></a>驗證設定

您可以分別為每個上游設定專案設定驗證。 當您設定驗證時，會在 `Authentication` 上游訊息的標頭中設定權杖。 目前，Azure SignalR Service 支援下列驗證類型：
- `None`
- `ManagedIdentity`

當您選取時 `ManagedIdentity` ，必須事先在 Azure SignalR Service 中啟用受控識別，並選擇性地指定資源。 如需詳細資訊，請參閱 [Azure SignalR Service 的受控](howto-use-managed-identity.md) 識別。

## <a name="create-upstream-settings-via-the-azure-portal"></a>透過 Azure 入口網站建立上游設定

1. 移至 Azure SignalR Service。
2. 選取 [ **設定** ] 並將 [ **服務模式]** 切換為 [ **無伺服器**]。 上游設定將會出現：

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="上游設定":::

3. 在 **上游 URL 模式**下新增 url。 然後， **中樞規則** 之類的設定會顯示預設值。
4. 若要設定 **中樞規則**、 **事件規則**、 **類別規則**和 **上游驗證**的設定，請選取 [ **中樞規則**] 的值。 可讓您編輯設定的頁面隨即出現：

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="上游設定":::

5. 若要設定 **上游驗證**，請先確定您已啟用受控識別。 然後選取 [ **使用受控識別**]。 根據您的需求，您可以選擇 [ **驗證資源識別碼**] 下的任何選項。 如需詳細資訊，請參閱 [Azure SignalR Service 的受控](howto-use-managed-identity.md) 識別。

## <a name="create-upstream-settings-via-resource-manager-template"></a>透過 Resource Manager 範本建立上游設定

若要使用 [Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)建立上游設定，請設定 `upstream` 屬性中的屬性 `properties` 。 下列程式碼片段示範如何設定用來 `upstream` 建立和更新上游設定的屬性。

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>無伺服器通訊協定

Azure SignalR Service 會將訊息傳送至遵循下列通訊協定的端點。

### <a name="method"></a>方法

POST

### <a name="request-header"></a>要求標頭

|Name |描述|
|---------|---------|
|X-ASRS-連接識別碼 |用戶端連接的連接識別碼。|
|X-ASRS-中樞 |用戶端連接所屬的中樞。|
|X-ASRS-類別 |訊息所屬的類別目錄。|
|X-ASRS-事件 |訊息所屬的事件。|
|X-ASRS-簽章 |以雜湊為基礎的訊息驗證碼 (HMAC) 用於驗證。 如需詳細資料，請參閱 [特徵](#signature) 標記。|
|X-ASRS-使用者宣告 |用戶端連接的宣告群組。|
|X-ASRS-使用者識別碼 |傳送訊息之用戶端的使用者身分識別。|
|X-ASRS-用戶端查詢 |用戶端連接到服務時的要求查詢。|
|驗證 |當您使用時的選擇性標記 `ManagedIdentity` 。 |

### <a name="request-body"></a>Request body

#### <a name="connected"></a>已連線

Content-Type: application/json

#### <a name="disconnected"></a>已中斷連接

Content-type： `application/json`

|名稱  |類型  |描述  |
|---------|---------|---------|
|錯誤 |字串 |關閉連接的錯誤訊息。 當連接關閉但沒有錯誤時為空白。|

#### <a name="invocation-message"></a>調用訊息

Content-type： `application/json` 或 `application/x-msgpack`

|名稱  |類型  |描述  |
|---------|---------|---------|
|InvocationId |字串 | 表示調用訊息的選擇性字串。 在 [調用](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)中尋找詳細資料。|
|目標 |字串 | 與事件相同，而且與 [調用訊息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目標相同。 |
|引數 |物件的陣列 |陣列，其中包含要套用至中所參考之方法的引數 `Target` 。 |

### <a name="signature"></a>簽章

服務將會 `X-ASRS-Connection-Id` 使用主要存取金鑰和次要存取金鑰作為金鑰來計算值的 SHA256 碼 `HMAC` 。 `X-ASRS-Signature`當對上游提出 HTTP 要求時，服務會在標頭中設定它：
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>後續步驟

- [Azure SignalR Service 的受控識別](howto-use-managed-identity.md)
- [使用 Azure SignalR Service 來開發與設定 Azure Functions](signalr-concept-serverless-development-config.md)