---
title: Azure 事件中樞-使用不同通訊協定的交換事件
description: 本文將說明使用不同通訊協定 (AMQP、Apache Kafka 及 HTTPS) 的取用者和產生者如何在使用 Azure 事件中樞時交換事件。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 6cdc4b9040f314b4ec41f84cc7436f0f2e3d6af6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002500"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>在使用不同通訊協定的取用者和產生者之間交換事件：AMQP、Kafka 及 HTTPS
Azure 事件中樞支援三種適用於取用者和產生者的通訊協定：AMQP、Kafka 及 HTTPS。 這些通訊協定中的每一個都有自己用來表示訊息的方式，因此自然會浮現下列問題：如果應用程式使用某種通訊協定來將事件傳送到事件中樞，並使用不同的通訊協定來取用它們，則事件的各種組件和值在送達取用者時看起來如何？ 本文將討論適用於產生者和取用者的最佳做法，以確保取用應用程式能夠正確解譯事件內的值。

本文中的建議特別涵蓋這些用戶端，並列出開發程式碼片段時所使用的版本：

* Kafka Java 用戶端 (來自 https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients) 的 1.1.1 版
* 適用於 Java 的 Microsoft Azure 事件中樞用戶端 (來自 https://github.com/Azure/azure-event-hubs-java) 的 1.1.0 版
* 適用於 .NET 的 Microsoft Azure 事件中樞用戶端 (來自 https://github.com/Azure/azure-event-hubs-dotnet) 的 2.1.0 版
* Microsoft Azure 服務匯流排 (來自 https://www.nuget.org/packages/WindowsAzure.ServiceBus) 的 5.0.0 版
* HTTPS (僅支援產生者)

其他的 AMQP 用戶端行為可能稍有不同。 AMQP 具有定義完善的類型系統，但將語言特定的類型序列化為該類型系統 (反之亦然) 的詳細規格取決於用戶端，就像用戶端提供存取 AMQP 訊息組件的權限時所做的。

## <a name="event-body"></a>事件內文
所有的 Microsoft AMQP 用戶端都會以未解譯的位元組封包來代表事件內文。 產生應用程式會將位元組序列傳送到用戶端，而取用應用程式會從用戶端接收同一個序列。 位元組序列的解譯會發生於應用程式的程式碼內。

透過 HTTPS 傳送事件時，事件內文為已張貼的內容，其也會被視為未解譯的位元組。 使用所提供的 ByteArraySerializer 和 ByteArrayDeserializer，就能輕易地在 Kafka 產生者或取用者中達成相同的狀態，如下列程式碼所示：

### <a name="kafka-byte-producer"></a>Kafka byte[] 產生者

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte[] 取用者
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

此程式碼會在這兩半的應用程式之間建立透明的位元組管線，並允許應用程式開發人員利用所需的任何方式，手動進行序列化和還原序列化，包括在執行階段進行還原序列化決策，例如，根據事件上使用者設定屬性中的類型或傳送者資訊。

具有單一且固定事件內文類型的應用程式，可能會使用其他 Kafka 序列化程式和還原序列化程式，以透明的方式轉換資料。 例如，假設應用程式會使用 JSON。 JSON 字串的建構與解譯會發生於應用程式層級。 在事件中樞層級，事件內文一律為字串，其為表示以 UTF-8 編碼之字元的位元組序列。 在此情況下，Kafka 產生者或取用者都可利用所提供的 StringSerializer 或 StringDeserializer，如下列程式碼所示：

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 字串產生者
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 字串取用者
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

針對 AMQP 端，Java 和 .NET 均提供內建方式來對字串與 UTF-8 位元組序列進行相互轉換。 Microsoft AMQP 用戶端會以名為 EventData 的類別來表示事件。 下列範例示範如何在 AMQP 產生者中將 UTF-8 字串序列化為 EventData 事件內文，以及如何 AMQP 取用者中將 EventData 事件內文還原序列化為 UTF-8 字串。

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 字串產生者
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 字串取用者
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 字串產生者
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 字串取用者
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

由於 Kafka 是開放原始碼，因此，應用程式開發人員可以檢查任何序列化程式或還原序列化程式的實作並實作程式碼，這會在 AMQP 端產生或取用相容的位元組序列。

## <a name="event-user-properties"></a>事件使用者屬性

使用者設定屬性可以從 AMQP 用戶端 (在 Microsoft AMQP 用戶端，它們稱為屬性) 和 Kafka (它們在其中稱為標頭) 進行設定和擷取。 HTTPS 傳送者可藉由在 POST 作業中提供使用者屬性作為 HTTP 標頭，在事件上設定它們。 不過，Kafka 會將事件內文和事件標頭值視為位元組序列。 然而在 AMQP 用戶端，屬性值所具備的類型會根據 AMQP 類型系統，透過為屬性值編碼來進行通訊。

HTTPS 是一個特殊案例。 在傳送時，所有屬性值都是 UTF-8 文字。 事件中樞服務會執行有限的解譯，以將適當的屬性值轉換為 AMQP 編碼的 32 位元和 64 位元帶正負號的整數、64 位元的浮點數及布林值。 任何不符合這其中一種類型的屬性值都會被視為字串。

混用這些方法來輸入屬性，表示 Kafka 取用者會看見 AMQP 編碼的原始位元組序列，包括 AMQP 類型資訊。 然而 AMQP 取用者會看到 Kafka 產生者所傳送之不具類型的位元組序列，而應用程式必須解譯此序列。

如果 Kafka 取用者要接收來自 AMQP 或 HTTPS 產生者的屬性，請使用 AmqpDeserializer 類別，這會在 Kafka 生態系統中的其他還原序列化程式之後進行模型化。 它會在 AMQP 編碼的位元組序列中解譯類型資訊，以將資料位元組還原序列化為 Java 類型。

最佳做法是，建議您在透過 AMQP 或 HTTPS 傳送的訊息中包含屬性。 Kafka 取用者可以使用它來判斷標頭值是否需要 AMQP 還原序列化。 屬性的值並不重要。 它只需要 Kafka 取用者可在標頭清單中找到的已知名稱，並據以調整其行為。

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP 到 Kafka 第 1 部分：在 C# (.NET) 中使用屬性來建立和傳送事件
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP 到 Kafka 第 2 部分：在 Kafka 取用者中使用 AmqpDeserializer 來將那些屬性還原序列化
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

如果應用程式知道屬性的預期類型，則會有之後不需轉換的還原序列化方法，但如果屬性不是預期的類型，它們就會擲回錯誤。

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP 到 Kafka 第 3 部分：在 Kafka 取用者中使用 AmqpDeserializer 的不同方式
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

反之則更為複雜，因為 AMQP 取用者一律會以原始位元組形式看見 Kafka 產生者所設定的標頭 (適用於 Java 的 Microsoft Azure 事件中樞用戶端的 org.apache.qpid.proton.amqp.Binary 類型，或適用於 Microsoft .NET AMQP 用戶端的 `System.Byte[]`)。 最簡單的路徑是使用 Kafka 提供的其中一個序列化程式，在 Kafka 產生者端產生標頭值的位元組，然後在 AMQP 取用者端編寫相容的還原序列化程式碼。

如同使用 AMQP-到-Kafka，我們建議的最佳做法是在透過 Kafka 傳送的訊息中包含屬性。 AMQP 取用者可以使用該屬性來判斷標頭值是否需要還原序列化。 屬性的值並不重要。 它只需要 AMQP 取用者可在標頭清單中找到的已知名稱，並據以調整其行為。 如果無法變更 Kafka 產生者，取用應用程式可能也會檢查屬性值是否為二進位或位元組類型，並嘗試根據該類型進行還原序列化。

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka 到 AMQP 第 1 部分：使用屬性從 Kafka 建立和傳送事件
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka 到 AMQP 第 2 部分：在 C#(.NET) 中以手動方式將那些屬性還原序列化
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka 到 AMQP 第 3 部分：在 Java 中以手動方式將那些屬性還原序列化
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何串流至事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列文章：  

* [了解事件中樞](./event-hubs-about.md)
* [了解適用於 Kafka 的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)
* [在適用於 Kafka 的事件中樞上探索更多範例 (GitHub)](https://github.com/Azure/azure-event-hubs-for-kafka)
* 使用[MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) ，將[事件從內部部署 Kafka 串流至雲端上的事件中樞。](event-hubs-kafka-mirror-maker-tutorial.md)
* 瞭解如何使用[原生 Kafka 應用程式](event-hubs-quickstart-kafka-enabled-event-hubs.md)、 [Apache Flink](event-hubs-kafka-flink-tutorial.md)或[akka streams 串流](event-hubs-kafka-akka-streams-tutorial.md)串流至事件中樞
