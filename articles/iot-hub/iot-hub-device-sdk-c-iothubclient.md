---
title: 適用於 C 的 Azure IoT 裝置 SDK - IoTHubClient | Microsoft Docs
description: 如何使用適用於 C 的 Azure IoT 裝置 SDK 中的 IoTHubClient 程式庫，以建立與 IoT 中樞通訊的裝置應用程式。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: 91527b5f2159a336e8339c6a128e8d61965292a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81732610"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>適用於 C 的 Azure IoT 裝置 SDK - 深入了解 IoTHubClient

[適用于 c 的 Azure iot 裝置 sdk](iot-hub-device-sdk-c-intro.md) 是本系列簡介 **AZURE iot 裝置 sdk for c**的第一篇文章。本文說明 SDK 中有兩個架構層。 其基礎為 **IoTHubClient** 程式庫，可直接管理與 IoT 中樞之間的通訊。 還有 **序列化程式** 庫，此程式庫建置於其頂部以提供序列化服務。 在本文中，我們將提供 **>iothubclient** 程式庫的其他詳細資料。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

前一篇文章描述如何使用 **IoTHubClient** 程式庫傳送事件到 IoT 中樞及接收訊息。 本文將向您介紹「較低層級 API」****，以說明如何更精確地管理傳送和接收資料的「時機」** 來延伸該討論。 我們也將說明如何使用 **IoTHubClient** 程式庫中的屬性處理功能，將屬性附加到事件 (並從訊息將其擷取)。 最後，我們將提供其他說明，以不同的方式來處理從 IoT 中樞接收的訊息。

本文以涵蓋幾個其他主題 (包括更多有關裝置認證及如何透過組態選項變更 **IoTHubClient** 行為的資訊) 做總結。

我們將使用 **IoTHubClient** SDK 範例來說明這些主題。 如果您想要依照這些內容，請參閱「適用於 C 的 Azure IoT 裝置 SDK」中隨附的 **iothub\_client\_sample\_http** 和 **iothub\_client\_sample\_amqp** 應用程式。這些範例會示範下列各節中所述的所有內容。

您可以尋找[**適用於 C 的 Azure IoT 裝置 SDK**](https://github.com/Azure/azure-iot-sdk-c) GitHub 儲存機制，然後在 [C API 參考資料](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)中檢視 API 的詳細資料。

## <a name="the-lower-level-apis"></a>較低層級的 API

前一篇文章描述了 **iothub\_client\_sample\_amqp** 應用程式內容中 **IotHubClient** 的基本作業。 比方說，該文說明如何使用下列程式碼初始化程式庫。

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

此外，還會說明如何使用這個函式呼叫來傳送事件。

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

本文還會說明如何藉由註冊回呼函式來接收訊息。

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

本文同時示範了如何使用如下的程式碼釋放資源。

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

每個 API 都有隨附的函式：

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

這些函數全都包含 API 名稱中的 **LL** 。 除了名稱的 **LL** 部份之外，每個函式的參數都與非 LL 的函式相同。 不過，這些函式的行為有一個重要的差異。

當您呼叫 **IoTHubClient\_CreateFromConnectionString** 時，基礎程式庫會建立在背景中執行的新執行緒。 此執行緒會將事件傳送到 IoT 中樞以及接收其訊息。 使用 **LL** api 時不會建立這類執行緒。 背景執行緒的建立是為了方便開發人員。 您不必擔心明確地傳送事件和接收來自 IoT 中樞的訊息，它會自動在背景中發生。 相反地，「 **LL** 」 api 可讓您明確控制與 IoT 中樞的通訊（如有需要）。

若要深入了解這個概念，讓我們看看一個範例：

當您呼叫 **IoTHubClient\_SendEventAsync** 時，其實是將事件放入緩衝區中。 在您呼叫 **IoTHubClient\_CreateFromConnectionString** 時建立的背景執行緒會持續監視這個緩衝區，並將它所包含的任何資料傳送到「IoT 中樞」。 這些動作會在主執行緒執行其他工作的同時在背景中進行。

同樣地，當您使用 **IoTHubClient\_SetMessageCallback** 來註冊訊息的回呼函式時，即是在指示 SDK 讓背景執行緒在收到訊息時叫用回呼函式 (不受主執行緒影響)。

**LL** api 不會建立背景執行緒。 而是必須呼叫新的 API 明確地與 IoT 中樞之間傳送和接收資料。 下列範例就將此進行示範。

SDK 中包含的 **iothub \_ 用戶端 \_ 範例 \_ HTTP** 應用程式會示範較低層級的 api。 在該範例中，我們會使用類似以下的程式碼，將事件傳送給「IoT 中樞」︰

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

前三行會建立訊息，而最後一行會傳送事件。 不過，如先前所述，傳送事件表示只是將資料放置於緩衝區中。 當我們呼叫 **IoTHubClient\_LL\_SendEventAsync** 時，不會在網路上傳輸任何內容。 若要實際將資料輸入到「IoT 中樞」，您必須呼叫 **IoTHubClient\_LL\_DoWork**，如下列範例所示：

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

此程式碼 (來自 **iothub\_client\_sample\_http** 應用程式) 會重複地呼叫 **IoTHubClient\_LL\_DoWork**。 每次呼叫 **IoTHubClient\_LL\_DoWork** 時，它都會將某些事件從緩衝區傳送到「IoT 中樞」，而且會擷取傳送到裝置的佇列訊息。 在後者的情況下，這表示如果我們已註冊訊息的回呼函式，則會叫用回呼 (假設所有訊息皆已加入佇列)。 我們會使用如下的程式碼來註冊這類回呼函式：

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

通常會在迴圈中呼叫 **>iothubclient \_ LL \_ DoWork** 的原因是，每次呼叫它時，它都會將 *一些* 緩衝的事件傳送至 IoT 中樞，並抓取裝置的 *下一個* 已排入佇列的訊息。 每次呼叫都不保證傳送所有已緩衝處理的事件，或抓取所有已排入佇列的訊息。 如果您想要傳送緩衝區中的所有事件，並繼續進行其他處理程序，您可以使用如下程式碼來取代此迴圈：

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

此程式碼會呼叫 **IoTHubClient\_LL\_DoWork**，直到緩衝區中的所有事件都已傳送到「IoT 中樞」為止。 請注意，這並未一併暗示已收到所有佇列的訊息。 這部分的原因是檢查「所有」訊息的行為並不具決定性。 如果您擷取「所有」訊息，但另一個訊息又隨即傳送至裝置，會發生什麼事？ 更好的處理方法是利用程式化的逾時。 例如，訊息回呼函數可以在每次叫用時重設計時器。 如果最後 *X* 秒都沒有收到任何訊息，您可以接著撰寫邏輯來繼續處理。

當您完成輸入事件和接收訊息時，請務必呼叫對應的函式來清除資源。

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

基本上，只有一組 Api 可使用背景執行緒來傳送和接收資料，而另一組 Api 則是在沒有背景執行緒的情況下執行相同的工作。 許多開發人員可能會偏好非 LL API，但是如果開發人員想要明確控制網路傳輸，則較低層級的 API 會相當有用。 例如，有些裝置會隨時間收集資料，並且只在指定的時間間隔輸入事件 (例如，每小時一次或一天一次)。 較低層級 API 可在您與 IoT 中樞之間傳送和接收資料時，提供您明確控制的功能。 其他人則會單純地偏好較低層級 API 所提供的簡單性。 一切動作都發生在主執行緒上，而不是有些工作會在背景中發生。

無論您選擇哪種模型，請務必與您使用的 API 一致。 如果您是透過呼叫 **IoTHubClient\_LL\_CreateFromConnectionString** 來開始著手，請務必只使用對應的較低層級 API 來進行任何追蹤工作：

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

相反的情況也成立。 如果您從 **IoTHubClient\_CreateFromConnectionString** 開始著手，則請使用非 LL API 來進行任何額外的處理。

在「適用於 C 的 Azure IoT 裝置 SDK」中，如需較低層級 API 的完整範例，請參閱 **iothub\_client\_sample\_http** 應用程式。 如需非 LL API 的完整範例，請參考 **iothub\_client\_sample\_amqp** 應用程式。

## <a name="property-handling"></a>屬性處理

到我們描述了傳送資料的目前為止，我們也已經參考訊息的內文。 例如，請思考下列程式碼：

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

此範例會傳送訊息給 IoT 中樞，包含文字 "Hello World"。 不過，IoT 中樞也允許屬性附加至每個訊息。 這些屬性是可附加至訊息的名稱/值組。 例如，我們可以修改上述程式碼，將屬性附加至訊息：

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

我們從呼叫 **IoTHubMessage\_Properties** 開始著手，然後將訊息的控制代碼傳遞給它。 我們得到的回應是 **MAP\_HANDLE** 參考，這可讓我們開始新增屬性。 後者可透過呼叫 **Map\_AddOrUpdate** 來完成，這會參考 MAP\_HANDLE、屬性名稱和屬性值。 利用此 API，我們可以依意願新增應用程式。

從「事件中樞」**** 讀取事件時，接收者可以列舉屬性並擷取其對應的值。 例如，在 .NET 中，這可藉由存取 [EventData 物件上的屬性集合](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx)來完成。

在上述範例中，我們會將屬性附加至我們傳送至 IoT 中樞的事件。 屬性也可以附加至從 IoT 中樞接收的訊息。 如果我們想要從訊息擷取屬性，可以在訊息回呼函式中使用如下的程式碼：

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

對 **IoTHubMessage\_Properties** 的呼叫會傳回 **MAP\_HANDLE** 參考。 然後，我們會將該參考傳遞給 **Map\_GetInternals**，以取得對名稱/值組陣列的參考 (以及屬性的計數)。 此時就可以很容易地列舉屬性來取得我們想要的值。

您不必在應用程式中使用屬性。 不過，如果您需要在事件上設定它們，或從訊息擷取它們， **IoTHubClient** 程式庫很容易就能辦到。

## <a name="message-handling"></a>訊息處理

如先前所述，當訊息從 IoT 中樞送達時， **IoTHubClient** 程式庫會叫用註冊的回呼函式來回應。 此函式有一個傳回參數值得額外說明。 以下是 **iothub \_ 用戶端 \_ 範例 \_ HTTP** 範例應用程式中的回呼函式摘錄：

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

請注意，傳回類型為 **IOTHUBMESSAGE\_DISPOSITION\_RESULT**，而在此特殊案例中，我們會傳回 **IOTHUBMESSAGE\_ACCEPTED**。 我們還有其他的值可從此函式傳回，這些值會變更 **IoTHubClient** 程式庫回應訊息回呼的方式。 選項如下。

* **IOTHUBMESSAGE\_ACCEPTED** – 已成功處理訊息。 **IoTHubClient** 程式庫將不會以相同的訊息再次叫用回呼函式。

* **IOTHUBMESSAGE\_REJECTED** – 未處理訊息，且未來也不打算處理。 **IoTHubClient** 程式庫不得以相同的訊息再次叫用回呼函式。

* **IOTHUBMESSAGE\_ABANDONED** – 未成功處理訊息，但 **IoTHubClient** 程式庫應以相同的訊息再次叫用回呼函式。

針對前兩個傳回程式碼， **IoTHubClient** 程式庫會將訊息傳送至 IoT 中樞，代表應該從裝置佇列中刪除訊息且不再傳遞。 最終結果一樣 (從裝置佇列刪除訊息)，但仍會記錄是否已接受或拒絕訊息。  對於可聽取意見回應並了解裝置已接受或拒絕特定訊息的訊息傳送者而言，記錄這項區別的功能非常實用。

在最後一個案例中，訊息也會傳送至 IoT 中樞，但它表示應重新傳遞訊息。 如果您遇到一些錯誤，但想要再次嘗試處理訊息，通常會放棄訊息。 相反地，當您遇到無法復原的錯誤 (或您只是決定不想處理) 的訊息時，拒絕訊息是適當的。

在任何情況下，請留意不同的傳回程式碼，您就能從 **IoTHubClient** 程式庫引發所需的行為。

## <a name="alternate-device-credentials"></a>替代裝置認證

如先前所述，使用 **IoTHubClient** 程式庫時，必須先使用如以下的呼叫來取得 **IOTHUB\_CLIENT\_HANDLE**：

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

**IoTHubClient\_CreateFromConnectionString** 的引數是裝置連接字串和一個參數，此參數指出用來與 IoT 中樞通訊的通訊協定。 裝置連接字串的格式如下所示：

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

這個字串中包含四項資訊：IoT 中樞名稱、IoT 中樞尾碼、裝置識別碼和共用存取金鑰。 當您在 Azure 入口網站中建立 IoT 中樞執行個體時，可以取得 IoT 中樞的完整網域名稱 (FQDN) - 這可為您提供 IoT 中樞名稱 (FQDN 的第一個部分) 和 IoT 中樞尾碼 (FQDN 的其餘部分)。 當您向 IoT 中樞註冊裝置時，您會取得裝置識別碼和共用存取金鑰 (如 [先前文章](iot-hub-device-sdk-c-intro.md)) 所述。

**IoTHubClient\_CreateFromConnectionString** 提供您一個方法來初始化程式庫。 喜歡的話，您也可以使用這些個別的參數 (而不是裝置連接字串) 建立新的 **IOTHUB\_CLIENT\_HANDLE**。 使用下列程式碼即可達成：

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

其作用與 **IoTHubClient\_CreateFromConnectionString** 相同。

顯而易見，您會想要使用 **IoTHubClient\_CreateFromConnectionString**，而不是這個較冗長的初始化方法。 但請記住，當您在 IoT 中樞註冊裝置時，您得到的是裝置識別碼和裝置金鑰 (不是連接字串)。 [前一篇文章](iot-hub-device-sdk-c-intro.md)介紹的「裝置總管」** SDK 工具會使用 **Azure IoT 服務 SDK** 中的程式庫，從裝置識別碼、裝置金鑰及 IoT 中樞主機名稱建立裝置連接字串。 因此，呼叫 **IoTHubClient\_LL\_Create** 可能是較慣用的做法，因為它可以為您省下產生連接字串的步驟。 使用任何方法都很方便。

## <a name="configuration-options"></a>設定選項

到目前為止，有關 **IoTHubClient** 程式庫運作方式的所有描述內容都反映其預設行為。 不過，您可以設定幾個選項來變更程式庫的運作方式。 這可以藉由運用 **IoTHubClient\_LL\_SetOption** API 來完成。 請考慮此範例：

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

有一些常用的選項：

* **SetBatching** (bool) - 如果為 **true**，則傳送到「IoT 中樞」的資料會以批次傳送。 如果為 **false**，就表示訊息會個別傳送。 預設值為 **false**。 支援對 AMQP/AMQP-WS 進行批次處理，以及在 D2C 訊息上新增系統屬性。

* **Timeout** (unsigned int) - 這個值會以毫秒為單位表示。 如果傳送 HTTPS 要求或接收回應所花費的時間超過這個時間，連線就會逾時。

此批次處理選項極為重要。 根據預設，程式庫會個別輸入事件 (單一事件是您傳遞給 **IoTHubClient\_LL\_SendEventAsync** 的任何內容)。 如果批次處理選項為 **true**，程式庫會盡可能從緩衝區收集事件 (上限為 IoT 中樞將接受的最大訊息大小)。  事件批次會在單一 HTTPS 呼叫中傳送到「IoT 中樞」(個別事件會統合至 JSON 陣列中)。 啟用批次處理通常會大幅提升效能，因為您要減少網路往返。 它也會大幅減少頻寬，因為您是利用事件批次傳送一組 HTTPS 標頭，而不是針對每個個別事件傳送一組標頭。 除非您有特定原因要這麼做，否則您通常會想要啟用批次處理。

## <a name="next-steps"></a>接下來的步驟

本文詳細說明在**適用于 C 的 Azure IoT 裝置 SDK**中找到的 **>iothubclient**程式庫行為。有了這項資訊，您應該對 **>iothubclient**程式庫的功能有充分的瞭解。 這個系列的第二篇文章[適用於 C 的 Azure IoT 裝置 SDK - 序列化程式](iot-hub-device-sdk-c-serializer.md)提供了關於 **** 程式庫的相似詳細資料。

若要深入了解如何開發 IoT 中樞，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

若要進一步探索 IoT 中樞的功能，請參閱[使用 Azure IoT 中樞將 AI 部署到邊緣裝置](../iot-edge/tutorial-simulate-device-linux.md)。
