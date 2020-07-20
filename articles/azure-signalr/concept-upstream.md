---
title: Azure SignalR Service 中的上游設定
description: 取得上游設定和上游訊息通訊協定的簡介。
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: be7736d0c90d1c384e15e8c7dee29d016b052dbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559441"
---
# <a name="upstream-settings"></a>上游設定

上游這項功能可讓 Azure SignalR Service 將訊息和線上活動傳送至無伺服器模式中的一組端點。 您可以使用上游在無伺服器模式下從用戶端叫用中樞方法，並讓端點在用戶端連線連接或中斷連線時收到通知。

> [!NOTE]
> 只有無伺服器模式可以設定上游設定。

## <a name="details-of-upstream-settings"></a>上游設定的詳細資料

上游設定是由排序相關的專案清單所組成。 每個專案都包含：

* URL 範本，指定訊息傳送至的位置。
* 一組規則。
* 驗證設定。 

當指定的事件發生時，會依序逐一檢查項目的規則。 訊息將會傳送至第一個相符專案的上游 URL。

### <a name="url-template-settings"></a>URL 範本設定

您可以將 URL 參數化以支援各種模式。 有三個預先定義的參數：

|預先定義的參數|Description|
|---------|---------|
|中樞| 中樞是 Azure SignalR Service 的概念。 中樞是隔離單位。 使用者和訊息傳遞的範圍會限制為中樞。|
|category| 類別可以是下列其中一個值： <ul><li>**連接**：連接存留期事件。 當用戶端連線連接或中斷連接時，就會引發此程式。 其中包含已連接和已中斷連線的事件。</li><li>**messages**：當用戶端叫用中樞方法時引發。 除了 **[連線**] 類別中的事件以外，它還包含所有其他事件。</li></ul>|
|發生| 針對 [**訊息**] 分類，事件是用戶端所傳送的[調用訊息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目標。 針對 [**連接**] 類別，只會使用 [*已連接*] 和 [已*中斷*連線]。|

這些預先定義的參數可以在 URL 模式中使用。 當您正在評估上游 URL 時，參數將會取代為指定的值。 例如： 
```
http://host.com/{hub}/api/{category}/{event}
```
當「聊天」中樞內的用戶端連線已連接時，系統會將訊息傳送至此 URL：
```
http://host.com/chat/api/connections/connected
```
當「聊天」中樞內的用戶端叫用中樞方法時 `broadcast` ，會將訊息傳送至此 URL：
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rule-settings"></a>規則設定

您可以分別設定*中樞規則*、*類別規則*和*事件規則*的規則。 比對規則支援三種格式。 採取事件規則做為範例：
- 使用星號（*）來比對任何事件。
- 使用逗號（，）來聯結多個事件。 例如，會比 `connected, disconnected` 對已連接和已中斷連線的事件。
- 使用完整的事件名稱來符合事件。 例如， `connected` 符合連接事件。

### <a name="authentication-settings"></a>驗證設定

您可以分別為每個上游設定專案設定驗證。 當您設定驗證時，會在 `Authentication` 上游訊息的標頭中設定權杖。 目前，Azure SignalR Service 支援下列驗證類型：
- `None`
- `ManagedIdentity`

當您選取時 `ManagedIdentity` ，您必須事先在 Azure SignalR Service 中啟用受控識別，並選擇性地指定資源。 如需詳細資訊，請參閱[Azure SignalR Service 的受控](howto-use-managed-identity.md)識別。

## <a name="create-upstream-settings-via-the-azure-portal"></a>透過 Azure 入口網站建立上游設定

1. 移至 Azure SignalR Service。
2. 選取 [**設定**] 並將 [**服務模式]** 切換為 [**無伺服器**]。 將會顯示上游設定：

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="上游設定":::

3. 在**上游 URL 模式**下新增 url。 然後，**中樞規則**這類設定會顯示預設值。
4. 若要設定**中樞規則**、**事件規則**、**類別規則**和**上游驗證**的設定，請選取 [**中樞規則**] 的值。 可讓您編輯設定的頁面隨即出現：

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="上游設定":::

5. 若要設定**上游驗證**，請先確定您已啟用受控識別。 然後選取 [**使用受控識別**]。 根據您的需求，您可以選擇 [**驗證資源識別碼**] 底下的任何選項。 如需詳細資訊，請參閱[Azure SignalR Service 的受控](howto-use-managed-identity.md)識別。

## <a name="create-upstream-settings-via-resource-manager-template"></a>透過 Resource Manager 範本建立上游設定

若要使用[Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)建立上游設定，請 `upstream` 在屬性中設定屬性 `properties` 。 下列程式碼片段示範如何設定 `upstream` 用於建立和更新上游設定的屬性。

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

|Name |說明|
|---------|---------|
|X-ASRS-連接識別碼 |用戶端連接的連接識別碼。|
|X-ASRS-中樞 |用戶端連接所屬的中樞。|
|X-ASRS-Category |訊息所屬的類別目錄。|
|X-ASRS-事件 |訊息所屬的事件。|
|X-ASRS-簽章 |用於驗證的雜湊式訊息驗證碼（HMAC）。 如需詳細資訊，請參閱[Signature](#signature) 。|
|X-ASRS-使用者-宣告 |用戶端連接的一組宣告。|
|X-ASRS-使用者識別碼 |傳送訊息之用戶端的使用者身分識別。|
|X-ASRS-用戶端-查詢 |用戶端連接到服務時的要求查詢。|
|驗證 |當您使用時的選擇性 token `ManagedIdentity` 。 |

### <a name="request-body"></a>Request body

#### <a name="connected"></a>連線

Content-Type: application/json

#### <a name="disconnected"></a>已中斷連接

內容類型：`application/json`

|名稱  |類型  |Description  |
|---------|---------|---------|
|錯誤 |字串 |已關閉連接的錯誤訊息。 當連接關閉但沒有錯誤時，則為空白。|

#### <a name="invocation-message"></a>調用訊息

內容類型： `application/json` 或`application/x-msgpack`

|名稱  |類型  |Description  |
|---------|---------|---------|
|InvocationId |字串 | 表示調用訊息的選擇性字串。 尋找[調用](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)中的詳細資料。|
|目標 |字串 | 與事件相同，而且與[調用訊息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目標相同。 |
|引數 |物件的陣列 |陣列，其中包含要套用至中所參考之方法的引數 `Target` 。 |

### <a name="signature"></a>簽章

服務會 `X-ASRS-Connection-Id` 使用主要存取金鑰和次要存取金鑰做為金鑰，來計算值的 SHA256 代碼 `HMAC` 。 服務會在 `X-ASRS-Signature` 對上游提出 HTTP 要求時，將它設定在標頭中：
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>後續步驟

- [Azure SignalR Service 的受控識別](howto-use-managed-identity.md)
- [使用 Azure SignalR Service 來開發與設定 Azure Functions](signalr-concept-serverless-development-config.md)
