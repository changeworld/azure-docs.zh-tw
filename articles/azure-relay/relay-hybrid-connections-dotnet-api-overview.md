---
title: Azure 轉送 .NET Standard API 概觀 | Microsoft Docs
description: 本文摘要說明 Azure 轉送混合式連線 .NET Standard API 的一些重點。
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 44d5800c08b49118e99a678e31d02e5b7a1f550c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935665"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure 轉送混合式連線 .NET Standard API 概觀

本文將摘要列出一些主要 Azure 轉送混合式連線 .NET Standard [用戶端 API](/dotnet/api/microsoft.azure.relay)。
  
## <a name="relay-connection-string-builder-class"></a>轉送連接字串產生器類別

[RelayConnectionStringBuilder][RelayConnectionStringBuilder] 類別會格式化專用於轉送混合式連線的連接字串。 您可以使用它來驗證連接字串的格式，或從頭開始建置連接字串。 如需範例，請參閱下列程式碼：

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

您也可以直接將連接字串傳遞到 `RelayConnectionStringBuilder` 方法。 這項作業可讓您確認連接字串格式有效。 如果有任何參數無效，建構函式會產生 `ArgumentException`。

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>混合式連線串流

無論您使用的是 [HybridConnectionClient][HCClient] 或 [HybridConnectionListener][HCListener]，[HybridConnectionStream][HCStream] 類別都是用來傳送和接收 Azure 轉送端點之資料的主要物件。

### <a name="getting-a-hybrid-connection-stream"></a>取得混合式連線串流

#### <a name="listener"></a>接聽程式

使用 [HybridConnectionListener][HCListener] 物件，您可以取得如下的 `HybridConnectionStream` 物件︰

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client

使用 [HybridConnectionClient][HCClient] 物件，您可以取得如下的 `HybridConnectionStream` 物件︰

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>接收資料

[HybridConnectionStream][HCStream] 類別允許雙向通訊。 在大部分的案例中，您會持續接收來自串流的資料。 如果您從串流讀取文字，建議您使用 [StreamReader](/dotnet/api/system.io.streamreader?view=netcore-3.1) 物件，這可讓您更容易剖析資料。 例如，您可以讀取文字 (而非`byte[]`) 格式的資料。

下列程式碼會從串流讀取個別的文字行，直到要求取消：

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>傳送資料

一旦建立連線，您可以傳送訊息到轉送端點。 因為連線物件會繼承[串流](/dotnet/api/system.io.stream?view=netcore-3.1)，請傳送 `byte[]` 格式的資料。 下列範例示範如何執行：

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

不過，如果您想要直接傳送文字，不需每次都將字串編碼，您可以使用 [StreamWriter](/dotnet/api/system.io.streamwriter?view=netcore-3.1) 物件來包裝 `hybridConnectionStream` 物件。

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 轉送，請造訪下列連結：

* [Microsoft.Azure.Relay reference](/dotnet/api/microsoft.azure.relay)
* [什麼是 Azure 轉送？](relay-what-is-it.md)(機器翻譯)
* [可用的轉送 API](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
