---
title: Azure Event Grid 安全性與驗證
description: 本文介紹對事件網格資源(WebHook、訂閱、自訂主題)的身份驗證存取的不同方法
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532388"
---
# <a name="authenticating-access-to-event-grid-resources"></a>驗證對事件網格資源的存取權限

Azure Event Grid 有三種驗證方法：

- WebHook 事件傳遞
- 事件訂閱
- 自訂主題發佈

## <a name="webhook-event-delivery"></a>WebHook 事件傳遞

Webhook 是從 Azure 事件方格接收事件的眾多方法之一。 當新事件準備就緒時，「事件方格」服務就會透過 POST 對所設定的端點發佈 HTTP 要求，並在要求本文內夾帶該事件。

和許多其他支援 Webhook 的服務一樣，「事件方格」也會先要求您證明具有 Webhook 端點的「擁有權」，然後才開始將事件傳遞給該端點。 此需求可避免惡意使用者利用事件癱瘓您的端點。 當您使用下列三種 Azure 服務的任一種時，Azure 基礎結構將會自動處理這項驗證：

- Azure Logic Apps，使用[事件格線連接器](https://docs.microsoft.com/connectors/azureeventgrid/)進行驗證
- Azure 自動化，透過 [Webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) 進行驗證
- Azure Functions，使用[事件格線觸發程序](../azure-functions/functions-bindings-event-grid.md)進行驗證

如果您使用任何其他類型的端點 (例如以 HTTP 觸發程序為基礎的 Azure 函式)，則您的端點程式碼必須參與和「事件方格」的驗證交握。 「事件方格」支援兩種驗證訂用帳戶的方式。

1. **同步握手**:在創建事件訂閱時,事件網格會向終結點發送訂閱驗證事件。 此事件的結構描述類似於任何其他「事件方格」事件。 此事件的資料部分包含 `validationCode` 屬性。 應用程序驗證驗證驗證請求是否用於預期事件訂閱,並同步返回回應中的驗證代碼。 所有「事件方格」版本都支援此交握機制。

2. **非同步握手**:在某些情況下,無法同步返回驗證代碼。 例如,如果您使用第三方服務(如[`Zapier`](https://zapier.com) [IFTTT),](https://ifttt.com/)則無法使用驗證代碼以程式設計方式回應。

   從 2018-05-01-preview 版開始，「事件方格」支援手動驗證交握。 如果您是以採用 API 2018-05-01-preview 版或更新版本的 SDK 或工具來建立事件訂閱，「事件方格」就會在訂閱驗證事件的資料部分中一併傳送 `validationUrl` 屬性。 要完成握手,請在事件數據中找到該 URL,然後對它執行 GET 請求。 您可以使用 REST 用戶端或您的網頁瀏覽器。

   提供的網址的合法為**5 分鐘**。 在那段時間，事件訂閱的佈建狀態為 `AwaitingManualAction`。 如果在 5 分鐘內未完成手動驗證,則預先設定`Failed`為 。 您必須再次建立事件訂閱，才能開始進行手動驗證。

   此身份驗證機制還要求 Webhook 終結點返回 HTTP 狀態代碼 200,以便知道驗證事件的 POST 已接受,然後才能將其置於手動驗證模式。 換句話說,如果終結點返回 200 但不同步返回驗證回應,則模式將轉換為手動驗證模式。 如果在 5 分鐘內驗證 URL 上有 GET,則驗證握手將被視為成功。

> [!NOTE]
> 不支援使用自簽名證書進行驗證。 改用證書頒發機構 (CA) 的已簽署證書。

### <a name="validation-details"></a>驗證詳細資料

- 建立/更新事件訂閱時，「事件方格」會將訂閱驗證事件發佈至目標端點。
- 事件包含標頭值 "aeg-event-type: SubscriptionValidation"。
- 此事件主體之結構描述與其他 Event Grid 事件相同。
- 事件的 eventType 屬性是 `Microsoft.EventGrid.SubscriptionValidationEvent`。
- 事件的資料屬性包含 `validationCode` 屬性，內含隨機產生的字串。 例如，"validationCode: acb13…"。
- 事件資料也包含具有 URL 的 `validationUrl` 屬性，可供手動驗證訂用帳戶。
- 此陣列只包含驗證事件。 在您回應驗證程式碼之後，其他事件會在不同的要求中傳送。
- EventGrid DataPlane SDK 具有對應於訂閱驗證事件資料和訂閱驗證回應的類別。

下列範例顯示 SubscriptionValidationEvent 的範例：

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

如需證明端點擁有權，請在 validationResponse 內容中回應驗證代碼，如下列範例所示：

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

您必須傳回「HTTP 200 正常」回應狀態碼。 「HTTP 202 已接受」無法辨識為有效的「事件方格」訂閱驗證回應。 HTTP 請求必須在 30 秒內完成。 如果操作未在 30 秒內完成,則該操作將被取消,並且可能在 5 秒後重新嘗試。 如果所有嘗試都失敗,則它將被視為驗證握手錯誤。

或者，您可以藉由將 GET 要求傳送至驗證 URL，以手動方式驗證訂用帳戶。 事件訂用帳戶會保持擱置狀態，直到通過驗證為止。 驗證網址使用埠 553。 如果您的防火牆規則阻止埠 553,則可能需要更新規則才能成功手動握手。

如需有關處理訂閱驗證交握的範例，請參閱 [C# 範例](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs) \(英文\)。

## <a name="troubleshooting-eventsubsciption-validation"></a>容錯排除事件子掃描驗證

在建立事件訂閱期間,如果您看到一條錯誤消息,如"嘗試驗證提供的終結點 H:/\/您的終結點-此處失敗。 有關詳細資訊,請造訪HTt:https:\//aka.ms/esvalidation",它指示驗證握手失敗。 若要解決此錯誤，請執行以下幾方面的驗證：

- 使用郵遞員或捲曲或類似工具,使用[範例訂閱驗證事件](#validation-details)請求正文對 Webhook URL 執行 HTTP POST。
- 如果您的 Webhook 正在實現同步驗證握手機制,請驗證驗證代碼是否作為回應的一部分返回。
- 如果您的 Webhook 正在實現非同步驗證握手機制,請驗證您是 HTTP POST 正在返回 200 OK。
- 如果 Webhook 在回應中傳回 403(禁止),請檢查 Webhook 是否位於 Azure 應用程式閘道或 Web 應用程式防火牆後面。 如果是,則需要禁用這些防火牆規則,並再次執行 HTTP POST:

  920300 (請求缺少接受標頭,我們可以修復此)

  942430 (受限 SQL 字元異常偵測 (args): * 超過特殊字元 (12))

  920230 (偵測到多個網址)

  942130 (SQL 注入攻擊:檢測到 SQL Taulogy。

  931130(可能的遠端檔包含 (RFI) 攻擊 = 網域外引用/連結)

### <a name="event-delivery-security"></a>事件傳遞安全性

#### <a name="azure-ad"></a>Azure AD

可以使用 Azure 活動目錄對 Webhook 終結點進行身份驗證並授權事件網格將事件發布到終結點,從而保護您的 Webhook 終結點。 您需要創建 Azure 活動目錄應用程式,在授權事件網格的應用程式中創建角色和服務原則,並將事件訂閱配置為使用 Azure AD 應用程式。 [瞭解如何使用事件網格配置 AAD。](secure-webhook-delivery.md)

#### <a name="query-parameters"></a>查詢參數

您可以在建立事件訂閱時將查詢參數新增至 Webhook URL，以保護您的 Webhook 端點。 將這些查詢參數中的其中一個設定為祕密，例如[存取權杖](https://en.wikipedia.org/wiki/Access_token)。 Webhook 可以使用秘密來辨識事件是否來自「事件方格」且具有有效的權限。 事件格線會在傳遞至 Webhook 的每個事件中包含這些查詢參數。

編輯事件訂閱時，除非在 Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 中使用 [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) 參數，否則不會顯示或傳回查詢參數。

最後請務必注意，Azure Event Grid 只支援 HTTPS Webhook 端點。

## <a name="event-subscription"></a>事件訂閱

若要訂閱事件，您必須證明您可存取事件來源和處理常式。 上一節已說明如何證明您擁有 WebHook。 如果您使用的事件處理常式不是 WebHook (例如事件中樞或佇列儲存體)，您將需要該資源的寫入存取權。 此權限檢查可防止未經授權的使用者將事件傳送至您的資源。

您在作為事件來源的資源上必須擁有 **Microsoft.EventGrid/EventSubscriptions/Write** 權限。 因為您要在該資源的範圍下寫入新的訂用帳戶，所以需要授權。 依您訂閱的是系統主題或自訂主題而定，所需資源會有所不同。 本節會說明這兩種類型。

### <a name="system-topics-azure-service-publishers"></a>系統主題 (Azure 服務發行者)

若您訂閱的是系統主題，您需要取得在資源發佈範圍下寫入新事件訂閱的權限。 資源的格式為：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

舉例來說，若要訂閱名為 **myacct** 之儲存體帳戶上的事件，您需要 Microsoft.EventGrid/EventSubscriptions/Write 授予您此權限：`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>自訂主題

若是自訂主題，您需要取得在 Event Grid 主題範圍下寫入新事件訂用帳戶的權限。 資源的格式為：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

舉例來說，若要訂閱名為**mytopic** 之自訂主題，您需要 Microsoft.EventGrid/EventSubscriptions/Write 授予您此權限：`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>自訂主題發佈

自訂主題使用共用存取簽章 (SAS) 或金鑰驗證。 我們建議使用 SAS，但金鑰驗證提供簡單的程式編寫，並且與許多現有的 Webhook 發佈者相容。

您要在 HTTP 標題包含驗證值。 若選擇 SAS，請使用 **aeg-sas-token** 作為標題的值。 若選擇金鑰驗證，請使用 **aeg-sas-key** 作為標題的值。

### <a name="key-authentication"></a>金鑰驗證

金鑰驗證是最簡單的驗證方法。 請使用此格式：`aeg-sas-key: <your key>`

舉例來說，您將金鑰與此一起傳遞：

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS 權杖

Event Grid 的 SAS 權杖包含資源、過期時間及簽章。 SAS 權杖的格式為：`r={resource}&e={expiration}&s={signature}`。

這裡的資源是您傳送事件之目標 Event Grid 主題的路徑。 例如,有效的資源路徑是: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`。 要檢視所有受支援的 API 版本,請參閱[Microsoft.EventGrid 資源類型](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)。 

簽章由您從金鑰產生。

以下為一個有效 **aeg-sas-token** 值的例子：

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

下列範例建立了供 Event Grid 使用的 SAS 權杖：

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>待用加密

事件網格服務寫入磁碟的所有事件或數據都由 Microsoft 管理的密鑰加密,確保它在靜態時加密。 此外,事件或資料保留的最長時間段為 24 小時,符合[事件網格重試策略](delivery-and-retry.md)。 事件格格將在 24 小時後自動刪除所有事件或數據,或者事件存留時間(以較少者為準)。

## <a name="endpoint-validation-with-cloudevents-v10"></a>使用雲事件 v1.0 的終結點驗證
如果您已經熟悉事件網格,您可能知道事件網格的終結點驗證握手,以防止濫用。 CloudEvents v1.0 使用 HTTP OPTIONS 方法實現自己的[濫用保護語義](security-authentication.md#webhook-event-delivery)。 你可以[在這裡](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)閱讀更多關於它。 使用 CloudEvents 架構進行輸出時,事件網格使用 CloudEvents v1.0 濫用保護代替事件網格驗證事件機制。

## <a name="next-steps"></a>後續步驟

- 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)
