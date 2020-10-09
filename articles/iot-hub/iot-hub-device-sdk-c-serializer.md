---
title: 適用於 C 的 Azure IoT 裝置 SDK - 序列化程式 | Microsoft Docs
description: 如何使用適用於 C 的 Azure IoT 裝置 SDK 中的 Serializer 程式庫，以建立與 IoT 中樞通訊的裝置應用程式。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: d4916d651638f0d1dbb4f10e0e0732f5c330d300
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81767021"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>適用於 C 的 Azure IoT 裝置 SDK - 深入了解序列化程式

本系列的第一篇文章介紹 [適用于 C 的 Azure IoT 裝置 SDK 簡介](iot-hub-device-sdk-c-intro.md)。下一篇文章提供 [適用于 C 的 Azure IoT 裝置 SDK-->iothubclient](iot-hub-device-sdk-c-iothubclient.md)的詳細說明。 本文將提供「序列化程式」 **** 程式庫這個最後元件的更詳細描述，來完成 SDK 的涵蓋範圍。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

本簡介文章描述如何使用「序列化程式」 **** 程式庫將事件傳送至 IoT 中樞，以及接收來自 IoT 中樞的訊息。 在本文中，我們將更完整說明如何利用「序列化程式」 **** 巨集語言來建立資料模型，以延伸該討論。 本文也包含更多有關程式庫如何將訊息序列化 (以及在某些情況下，如何控制序列化行為) 的詳細資料。 我們也將描述您可以修改以判斷您所建立之模型大小的某些參數。

最後，本文會回顧先前文章中涵蓋的一些主題，例如訊息和屬性處理。 因為我們將發現，使用「序列化程式」**** 程式庫時，這些功能的運作方式與使用 **IoTHubClient** 程式庫時相同。

本文中描述的所有內容都是根據「序列化程式」**** SDK 範例。 如果您想要依照這些內容，請參閱包含在「適用於 C 的 Azure IoT 裝置 SDK」中的 **simplesample\_amqp** 和 **simplesample\_http** 應用程式。

您可以尋找[**適用於 C 的 Azure IoT 裝置 SDK**](https://github.com/Azure/azure-iot-sdk-c) GitHub 儲存機制，然後在 [C API 參考資料](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)中檢視 API 的詳細資料。

## <a name="the-modeling-language"></a>模型化語言

本系列中的[適用於 C 的 Azure IoT 裝置 SDK](iot-hub-device-sdk-c-intro.md) 文章，透過 **simplesample\_amqp** 應用程式中提供的範例，介紹了**適用於 C 的 Azure IoT 裝置 SDK** 模型化語言：

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

如您所見，模型化語言是以 C 巨集為基礎。 您的定義開頭一律會是 **BEGIN\_NAMESPACE**，而結尾則一律是 **END\_NAMESPACE**。 通常會為您的公司命名此命名空間，或如同此範例，為您正在使用的專案命名此命名空間。

在命名空間內部進行的是模型定義。 在此案例中，有一個單一的風速計模型。 同樣地，可以任意命名此模型，但通常會針對您想要與 IoT 中樞交換的裝置或資料類型來為模型命名。  

模型除了包含您可以從「IoT 中樞」接收的訊息 (動作**) 之外，也包含您可以輸入到「IoT 中樞」(資料**) 之事件的定義。 如同您在範例中看到的，事件具有類型和名稱。動作會有一個名稱和選擇性的參數 (各有其類型)。

此範例中未示範的是 SDK 所支援的其他資料類型。 我們將在稍後討論。

> [!NOTE]
> 「IoT 中樞」會將裝置傳送給它的資料視為「事件」**，而模型化語言則是會將它視為「資料」**(使用 **WITH_DATA** 來定義)。 同樣地，「IoT 中樞」會將您傳送給裝置的資料視為「訊息」**，而模型化語言則是會將它視為「動作」**(使用 **WITH_ACTION** 來定義)。 請注意，在本文中可能會交替使用這些詞彙。
> 
> 

## <a name="supported-data-types"></a>支援的資料類型

利用 **序列化程式** 程式庫建立的模型支援下列資料類型：

| 類型 | 說明 |
| --- | --- |
| double |雙精確度浮點數 |
| int |32 位元整數 |
| FLOAT |單精確度浮點數 |
| long |長整數 |
| int8\_t |8位整數 |
| int16\_t |16位整數 |
| int32\_t |32 位元整數 |
| int64\_t |64 位元整數 |
| bool |boolean |
| ascii\_char\_ptr |ASCII 字串 |
| EDM\_DATE\_TIME\_OFFSET |日期時間位移 |
| EDM\_GUID |GUID |
| EDM\_BINARY |BINARY |
| DECLARE\_STRUCT |複雜資料類型 |

讓我們從最後一個資料類型開始。 **DECLARE\_STRUCT** 可讓您定義複雜的資料類型，也就是其他基本類型的群組。 這些群組可讓我們定義看起來像這樣的模型：

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

我們的模型包含 **TestType**類型的單一資料事件。 **TestType** 是包含數個成員的複雜類型，這些成員共同展示了「序列化程式」**** 模型化語言所支援的基本類型。

使用類似這樣的模型，我們可以撰寫程式碼以將資料傳送到看起來像這樣的 IoT 中樞：

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

基本上，我們會將值指派給 **測試** 結構的每個成員，然後呼叫 **SendAsync** 將 **測試** 資料事件傳送至雲端。 **SendAsync** 是將單一資料事件傳送到 IoT 中樞的協助程式函式：

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

此函式會將指定的資料事件序列化，然後使用 **IoTHubClient\_SendEventAsync** 將它傳送到「IoT 中樞」。 這是在先前的文章中討論過的相同程式碼 (**SendAsync** 會將邏輯封裝入方便的函式)。

在先前的程式碼中使用的一個其他協助程式函式是 **GetDateTimeOffset**。 此函式會將指定的時間轉換成 **EDM\_DATE\_TIME\_OFFSET** 類型的值：

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

如果您執行此程式碼，就會傳送下列訊息到 IoT 中樞：

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

請注意，序列化屬於 JSON 格式，這是由**序列化程式**庫所產生的格式。 另請注意，序列化 JSON 物件的每個成員皆與在我們模型中定義的 **TestType** 成員相符。 值也與程式碼中使用的值完全相符。 不過，請注意，二進位資料是採用 base64 編碼："AQID" 是 {0x01, 0x02, 0x03} 的 base64 編碼。

這個範例示範使用 **序列化程式** 程式庫的優點 -- 它可讓我們將 JSON 傳送至雲端，而不需要在我們的應用程式中明確處理序列化。 我們所需操心的只有要在模型中設定資料事件的值，然後呼叫簡單的 API 將這些事件傳送至雲端。

有了這項資訊，我們便可以定義包含支援之資料類型範圍的模型，這些資料類型包括複雜類型 (我們甚至可以包含其他複雜類型內的複雜類型)。 不過，上述範例所產生的序列化 JSON 突顯了一個重點。 *如何* 利用 **** 程式庫傳送資料，可完全決定 JSON 如何形成。 此特點就是我們接下來要討論的內容。

## <a name="more-about-serialization"></a>深入了解序列化

上一節強調 **序列化程式** 程式庫所產生的輸出範例。 在本節中，我們將說明程式庫如何將資料序列化，以及如何使用序列化 API 來控制該行為。

為了進一步討論序列化，我們將使用以控溫器為基礎的新模型。 首先，讓我們針對所要嘗試處理的案例提供一些背景。

我們想要建立可測量氣溫和溼度的控溫器模型。 每一項資料將會以不同的方式傳送至 IoT 中樞。 根據預設，控溫器會每 2 分鐘輸入溫度事件一次；每 15 分鐘輸入溼度事件一次。 輸入任一事件時，必須包含顯示相對應溫度或溼度之測量時間的時間戳記。

在這個案例中，我們將示範兩種不同的資料模型化方式，並將說明該模型化對序列化輸出的影響。

### <a name="model-1"></a>模型 1

以下是支援先前案例的第一版模型：

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

請注意，此模型包含兩個資料事件：**Temperature** 和 **Humidity**。 與先前的範例不同，這裡每個事件的類型都是使用 **DECLARE\_STRUCT** 來定義的結構。 **TemperatureEvent** 包含溫度測量和時間戳記；**HumidityEvent** 包含溼度測量和時間戳記。 此模型可讓我們以自然的方式模型化上述案例的資料。 當我們將事件傳送至雲端時，我們將會傳送溫度/時間戳記組或溼度/時間戳記組。

我們可以使用類似以下的程式碼將溫度事件傳送至雲端：

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

在範例程式碼中，我們將使用硬式編碼的值來表示溫度和濕度，但假設我們實際上是藉由取樣控溫器上的對應感應器來取得這些值。

上述程式碼使用先前介紹的協助程式 **GetDateTimeOffset** 。 此程式碼會將序列化與傳送事件的工作明確分隔，原因在稍後會漸趨明朗。 先前的程式碼會將溫度事件以序列化方式傳送至緩衝區。 然後，**sendMessage** 則是一個協助程式函式 (包含在 **simplesample\_amqp** 中)，會將事件傳送到「IoT 中樞」：

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

這段程式碼是上一節中所述之 **SendAsync** 協助程式的子集，所以我們不會在此找到它。

我們執行先前的程式碼以傳送溫度事件時，事件的序列化形式會傳送到 IoT 中樞：

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

我們正在傳送的溫度為 **TemperatureEvent**類型，而該結構包含 **溫度** 和 **時間** 成員。 這會直接反映在序列化資料中。

同樣地，我們可以利用此程式碼來傳送溼度事件：

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

傳送至 IoT 中樞的序列化表單會如下所示：

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

同樣地，全如預期一般。

您可以使用此模型來想像如何輕鬆地加入其他事件。 您會使用 **DECLARE\_STRUCT** 來定義更多結構，並使用 **WITH\_DATA** 將相對應的事件包含在模型中。

現在，讓我們修改模型，讓它包含相同的資料，但有不同的結構。

### <a name="model-2"></a>模型 2

請考慮上述模型的替代模型：

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

在此情況下，我們已經消除了 **DECLARE \_ 結構** 宏，而且只是使用模型語言的簡單型別，從我們的案例中定義資料項目。

現在請忽略**時間**事件。 除此之外，以下是輸入 **溫度**的程式碼：

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

此程式碼會將下列序列化事件傳送到 IoT 中樞：

```C
{"Temperature":75}
```

而傳送 Humidity 事件的程式碼則看起來像這樣：

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

此程式碼會將其傳送至 IoT 中樞：

```C
{"Humidity":45}
```

到目前為止，仍毫無意外。 現在，讓我們變更 SERIALIZE 巨集的使用方式。

**SERIALIZE** 巨集可以將多個資料事件視為引數。 這可讓我們將 **Temperature** 與 **Humidity** 事件一起序列化，然後以單一呼叫將它們傳送到「IoT 中樞」：

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

您或許已經猜到，此程式碼的結果是兩個資料事件都會傳送到 IoT 中樞：

[ {"Temperature":75}, {"Humidity":45} ]

換句話說，您可以預期此程式碼與分別傳送 **Temperature** 和 **Humidity** 一樣。 這只是將這兩個事件傳遞至相同呼叫中 **序列化** 的便利性。 不過，這並不是這樣。 上述程式碼會改為將這個單一資料事件傳送到 IoT 中樞：

{"Temperature":75, "Humidity":45}

這可能看似奇怪，因為我們的模型將 **Temperature** 和 **Humidity** 定義成兩個「個別」** 事件：

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

重點是，我們不會建立這些事件的模型，因為 **溫度** 和 **濕度** 都在相同的結構中：

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

如果我們使用了這個模型，就會更容易了解如何在相同的序列化訊息中傳送 **Temperature** 和 **Humidity**。 不過，如果您是使用模型 2 將兩個資料事件都傳遞至 **SERIALIZE** ，可能就無法突顯它以該方式運作的原因。

如果您知道 **序列化程式** 程式庫所做的假設，您可以更容易了解這種行為。 為了瞭解這一點，讓我們回到我們的模型：

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

請以物件導向的觀點思考此模型。 在此情況下，我們會將實體裝置模型化 (控溫器) ，而該裝置會包含 **溫度** 和 **濕度**等屬性。

我們可以利用類似以下的程式碼來傳送模型的整個狀態：

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

假設已設定溫度、溼度和時間的值，我們會看到這樣的事件傳送到 IoT 中樞：

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

有時，您可能只想將模型的「部分」 ** 屬性傳送到雲端 (尤其是當您的模型包含大量資料事件的時候)。 只傳送資料事件的子集（例如在先前的範例中）會很有用：

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

這會產生如同我們使用 **Temperature** 和 **Time** 成員來定義 **TemperatureEvent** 一樣的完全相同 (就像我們在模型 1 所做的一樣) 序列化事件。 在此情況下，我們可以使用不同的模型 (模型 2) 來產生完全相同的序列化事件，因為我們以不同的方式呼叫 **序列化** 。

重點是，如果您將多個資料事件傳送給 **SERIALIZE** ，則它會假設每個事件都是單一 JSON 物件中的一個屬性。

最佳方法取決於您及您對模型的思考方式。 如果您要將「事件」傳送至雲端，且每個事件都包含一組已定義的屬性，則第一種方法會很有意義。 在該情況下，您會使用 **DECLARE\_STRUCT** 來定義每個事件的結構，然後利用 **WITH\_DATA** 巨集將它們包含在模型中。 然後您會依照我們在上述第一個範例中使用的方法來傳送每個事件。 在此方法中，您只會將單一資料事件傳遞給 **序列化程式**。

如果您以物件導向的方式思考模型，則第二種方法可能比較適合您。 在此情況下，使用 **WITH\_DATA** 來定義的元素是您物件的「屬性」。 您可以根據您想要傳送至雲端的「物件」狀態多少，傳遞您喜歡的任何事件子集來進行 **序列化** 。

沒有絕對正確或錯誤的方法。 請務必了解 **序列化程式** 程式庫的運作方式，並挑選最適合您需求的模型化方法。

## <a name="message-handling"></a>訊息處理

到目前為止，本文只討論傳送事件到 IoT 中樞，尚未處理接收訊息。 這是因為在 [C 的 Azure IoT 裝置 SDK](iot-hub-device-sdk-c-intro.md)文章中，我們必須知道接收訊息的相關資訊。回想一下，您可以藉由註冊訊息回呼函數來處理訊息：

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

然後，您可以撰寫收到訊息時所叫用的回呼函數：

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

**IoTHubMessage** 的實作會針對您模型中的每個動作呼叫特定的函式。 例如，如果您的模型定義這項動作：

```C
WITH_ACTION(SetAirResistance, int, Position)
```

您必須使用此簽章定義函式：

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** 。

不過我們還沒說明序列化版本訊息的外觀。 換句話說，如果您想要傳送 **SetAirResistance** 訊息至您的裝置，該訊息的外觀如何？

如果您要傳送訊息給裝置，您會透過 Azure IoT 服務 SDK 進行。 您仍然需要知道要傳送哪個字串才能叫用特殊動作。 傳送訊息的一般格式看起來像這樣：

```C
{"Name" : "", "Parameters" : "" }
```

您將使用兩個屬性來傳送序列化的 JSON 物件：**Name** 是動作 (訊息) 的名稱，而 **Parameters** 則包含該動作的參數。

例如，若要叫用 **SetAirResistance** ，您可以將下列訊息傳送給裝置：

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

動作名稱必須完全符合您的模型中所定義的動作。 參數名稱也必須相符。 也請注意區分大小寫。 **Name** 和 **Parameters** 一律是大寫。 請務必符合您模型中動作名稱和參數的大小寫。 在此範例中，動作名稱是 "SetAirResistance"，而不是 "setairresistance"。

您可以透過將下列訊息傳送到裝置，以叫用兩個其他動作 **TurnFanOn** 與 **TurnFanOff**：

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

本節說明使用 **序列化程式** 程式庫來傳送事件及接收訊息時的一切資訊。 在繼續討論之前，讓我們先討論一些您可以設定以控制模型大小的參數。

## <a name="macro-configuration"></a>巨集組態

如果您使用 **序列化程式** 程式庫，您可以在 azure-c-共用公用程式程式庫中找到需要注意的 SDK 重要部分。

如果您已從 GitHub 複製 Azure-iot-sdk-c 存放庫併發出 `git submodule update --init` 命令，則您會在此找到此共用公用程式程式庫：

```C
.\\c-utility
```

如果您尚未複製程式庫，可以在 [這裡](https://github.com/Azure/azure-c-shared-utility)找到。

在共用公用程式庫中，可以找到下列資料夾：

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

此資料夾包含名為 **macro\_utils\_h\_generator.sln** 的 Visual Studio 方案：

  ![Visual Studio 解決方案 maco_utils_h_generator 的螢幕擷取畫面](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

此方案中的程式會產生 **macro\_utils.h** 檔案。 \_SDK 中包含預設的宏公用程式 .h 檔案。 這個解決方案可讓您修改部分參數，然後根據這些參數重新建立標頭檔。

要考慮的兩個重要參數為 **nArithmetic** 和 **>nmacroparameters**，這兩行定義在宏 \_ utils.tt 中：

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

這些值是 SDK 隨附的預設參數。 每個參數都具有下列意義：

* nMacroParameters – 控制您在一個 DECLARE\_MODEL 巨集定義中可以擁有多少參數。
* nArithmetic – 控制模型中允許的成員總數。

這些參數之所以重要，是因為它們控制您模型的大小。 例如，請考慮以此模型定義：

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

如先前所述，**DECLARE\_MODEL** 只是一個 C 巨集。 模型的名稱和 **WITH\_DATA** 陳述式 (另一個巨集) 是 **DECLARE\_MODEL** 的參數。 **nMacroParameters** 會定義 **DECLARE\_MODEL** 中可以包含多少參數。 實際上，這定義的是您所能擁有的資料事件和動作宣告數目。 因此，使用預設限制 124 時，表示您可以定義由大約 60 個動作和事件資料所組成的模型。 如果您嘗試超過這個限制，您會收到看起來像這樣的編譯器錯誤：

  ![巨集參數編譯器錯誤的螢幕擷取畫面](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

**nArithmetic** 參數與巨集語言的內部運作較有關，與您的應用程式較無關。  它會控制您可以在模型中擁有的成員總數，包括 **DECLARE_STRUCT** 巨集。 如果您開始看到這類編譯器錯誤，則應該嘗試提高 **nArithmetic**的值：

   ![算術編譯器錯誤的螢幕擷取畫面](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

如果您想要變更這些參數，請修改 macro\_utils.tt 檔案中的值、重新編譯 macro\_utils\_h\_generator.sln 方案，然後執行編譯過的程式。 當您這麼做時，會產生新的 macro\_utils.h 檔案並放在 .\\common\\inc 目錄中。

若要使用新版的 macro\_utils.h，請從您的方案中移除「序列化程式」**** NuGet 套件，並在其位置中納入「序列化程式」**** Visual Studio 專案。 這可讓您的程式碼針對序列化程式程式庫的原始程式碼進行編譯。 這包括已更新的 macro\_utils.h。 如果您想要針對 **simplesample\_amqp** 執行這項操作，請從自方案中移除序列化程式程式庫的 NuGet 套件開始：

   ![移除序列化程式庫的 NuGet 套件螢幕擷取畫面](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

然後將此專案新增到您的 Visual Studio 解決方案：

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

完成時，解決方案應該如下所示：

   ![Simplesample_amqp Visual Studio 解決方案的螢幕擷取畫面](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

現在，當您編譯您的方案時，已更新的 macro\_utils.h 就會包含在您的二進位檔中。

請注意，增加這些值到足夠高的數目可能會超出編譯器限制。 到這個階段， **nMacroParameters** 會是需要納入考量的主要參數。 C99 規格指定巨集定義中允許最少 127 個參數。 Microsoft 編譯器完全遵循此規格 (限制為 127)，因此您無法將 **nMacroParameters** 提高到超過預設值。 其他編譯器可能會允許您這麼做 (例如 GNU 編譯器支援更高的限制)。

到目前為止，我們幾乎已經討論了如何使用 **序列化程式** 程式庫撰寫程式碼所必須知道的一切內容。 在做出結論之前，讓我們先從先前的文章中回顧一些您可能想知道的主題。

## <a name="the-lower-level-apis"></a>較低層級的 API
本文著重的範例應用程式是 **simplesample\_amqp**。 這個範例會使用較高層級 (非 **LL**) API 來傳送事件和接收訊息。 如果您使用這些 API，將會執行背景執行緒來處理事件傳送和訊息接收。 不過，您可以使用較低層級 (LL) API 來消除這個背景執行緒，並明確掌控傳送事件或從雲端接收訊息的時機。

如 [前一篇文章](iot-hub-device-sdk-c-iothubclient.md)所述，有一組由較高層級 API 組成的函式：

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_Destroy

**simplesample\_amqp** 中會示範這些 API。

此外，也有一組類似的較低層級 API。

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

請注意，較低層級 API 的運作方式與先前文章中所述的完全相同。 如果您想要背景執行緒以處理事件傳送和訊息接收，您可以使用第一組 API。 如果您想要掌握與 IoT 中樞之間傳送和接收資料時的明確控制權，您可以使用第二組 API。 任何一組 API 使用 **序列化程式** 程式庫的效果都相同。

如需有關如何將較低層級 API 與「序列化程式」**** 程式庫搭配使用的範例，請參閱 **simplesample\_http** 應用程式。

## <a name="additional-topics"></a>其他主題
幾個其他值得再次一提的主題包括屬性處理、使用替代裝置認證及組態選項。 這些都是 [先前的文章](iot-hub-device-sdk-c-iothubclient.md)中所涵蓋的主題。 重點在於，所有這些功能不論是與「序列化程式」**** 程式庫搭配，還是與 **IoTHubClient** 程式庫搭配，其運作方式均相同。 例如，如果您想要將屬性附加到來自您模型的事件，您需透過前述的相同方式，使用 **IoTHubMessage\_Properties** 和 **Map**\_**AddorUpdate**：

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

無論事件是從「序列化程式」**** 程式庫產生，還是使用 **IoTHubClient** 程式庫手動建立，並不重要。

就替代裝置認證而言，使用 **IoTHubClient\_LL\_Create** 來配置 **IOTHUB\_CLIENT\_HANDLE** 的效果與使用 **IoTHubClient\_CreateFromConnectionString** 一樣好。

最後，如果您使用「序列化程式」**** 程式庫，就可以使用 **IoTHubClient\_LL\_SetOption** 來設定組態選項，就像您使用 **IoTHubClient** 程式庫時所做的一樣。

**序列化程式** 程式庫有一個獨一無二的功能，也就是初始化 API。 您必須先呼叫 **serializer\_init**，才能開始使用該程式庫：

```C
serializer_init(NULL);
```

此動作必須正好在呼叫 **IoTHubClient\_CreateFromConnectionString** 之前執行。

同樣地，當您使用完程式庫時，最後一次呼叫會是 **序列化程式 \_ >deinit**：

```C
serializer_deinit();
```

除此之外，上面列出的所有其他功能在「序列化程式」**** 程式庫中的運作方式，皆與在 **IoTHubClient** 程式庫中的運作方式相同。 如需有關任何這些主題的詳細資訊，請參閱本系列中的 [前一篇文章](iot-hub-device-sdk-c-iothubclient.md) 。

## <a name="next-steps"></a>接下來的步驟

本文詳細說明**適用于 C 的 Azure IoT 裝置 SDK**中包含的**序列化程式**程式庫的獨特層面。透過提供的資訊，您應該充分瞭解如何使用模型來傳送事件，以及從 IoT 中樞接收訊息。

這也結束了三部分的系列，說明如何使用 **適用于 C 的 Azure IoT 裝置 SDK**來開發應用程式。這應該是足夠的資訊，不僅能讓您開始使用，還能讓您徹底瞭解 Api 的運作方式。 如需其他資訊，還有一些 SDK 中的範例未涵蓋在本文中。 除此之外，[Azure IoT SDK 文件](https://github.com/Azure/azure-iot-sdk-c) \(英文\) 也是取得其他資訊的絕佳資源。

若要深入了解如何開發 IoT 中樞，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

若要進一步探索 IoT 中樞的功能，請參閱[使用 Azure IoT 中樞將 AI 部署到邊緣裝置](../iot-edge/tutorial-simulate-device-linux.md)。