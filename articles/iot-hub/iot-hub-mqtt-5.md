---
title: 'Azure IoT 中樞 MQTT 5 支援 (preview) '
description: 瞭解 IoT 中樞的 MQTT 5 支援
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603207"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT 中樞 MQTT 5 支援總覽 (預覽) 

**版本：** 2.0 **api 版本：** 2020-10-01-preview

本檔定義了 MQTT 5.0 版通訊協定的 IoT 中樞資料平面 API。 如需完整定義，請參閱此 API 中的 [Api 參考](iot-hub-mqtt-5-reference.md) 。

## <a name="prerequisites"></a>先決條件

- 在全新的 IoT 中樞上[啟用預覽模式](iot-hub-preview-mode.md)，以嘗試 MQTT 5。
- 需要事先瞭解 [MQTT 5 規格](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) 。

## <a name="level-of-support-and-limitations"></a>支援層級和限制

MQTT 5 的 IoT 中樞支援處於預覽階段，並受到下列限制： (透過屬性與用戶端通訊， `CONNACK` 除非明確注明，否則) ：

- 尚未正式 [Azure IoT 中樞裝置 SDK](iot-hub-devguide-sdks.md) 支援。
- 不支援訂用帳戶識別碼。
- 不支援共用訂閱。
- 不支援 `RETAIN`。
- `Maximum QoS` 為 `1`。
- `Maximum Packet Size``256 KiB` (受制于每個作業) 的進一步限制。
- 不支援指派的用戶端識別碼。
- `Keep Alive` 受限於 `19 min` 活動檢查的 (延遲上限- `28.5 min`) 。
- `Topic Alias Maximum` 為 `10`。
- `Response Information` 不受支援; `CONNACK` `Response Information` 即使 `CONNECT` contains 屬性，也不會傳回 property `Request Response Information` 。
- `Receive Maximum` (`PUBLISH`) 為) 的用戶端-伺服器方向中， (允許未完成的未認可封包數目上限 `QoS: 1` `16` 。
- 單一用戶端的訂用帳戶不能超過 `50` 訂閱。
  當達到限制時，將會傳回超過訂用帳戶 `SUBACK` `0x97` 的) 原因碼 (配額。

## <a name="connection-lifecycle"></a>連接生命週期

### <a name="connection"></a>連線

若要使用此 API 將用戶端連線到 IoT 中樞，請建立每個 MQTT 5 規格的連接。
用戶端必須 `CONNECT` 在成功的 TLS 交握之後，于30秒內傳送封包，否則伺服器會關閉連接。
以下是封包範例 `CONNECT` ：

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` 需要屬性，並識別使用的驗證方法。 如需驗證方法的詳細資訊，請參閱 [驗證](#authentication)。
- `Authentication Data` 屬性處理取決於 `Authentication Method` 。 如果 `Authentication Method` 設定為 `SAS` ，則 `Authentication Data` 為必要項，而且必須包含有效的簽章。 如需驗證資料的詳細資訊，請參閱 [驗證](#authentication)。
- `api-version` 屬性是必要的，而且必須設定為此規格標頭中提供的 API 版本值，此規格才能套用。
- `host` 屬性定義租使用者的主機名稱。 除非在 TLS 信號交換期間于用戶端 Hello 記錄中呈現 SNI 擴充功能，否則需要此項
- `sas-at` 定義連接的時間。
- `sas-expiry` 定義所提供 SAS 的到期時間。
- `client-agent` 選擇性地傳達有關建立連接之用戶端的資訊。

> [!NOTE]
> `Authentication Method` 而且，在具有大寫名稱的規格中的其他屬性是 MQTT 5 中的第一類屬性-這些屬性的詳細說明請見 MQTT 5 規格。 `api-version` 虛線案例中的其他屬性是 IoT 中樞 API 特定的使用者屬性。

IoT 中樞會在 `CONNACK` 完成時以驗證和提取資料來回應封包，以支援連接。 如果成功建立連線，如下 `CONNACK` 所示：

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

這些封 `CONNACK` 包屬性遵循 [MQTT 5 規格](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080)。 它們會反映 IoT 中樞的功能。

### <a name="authentication"></a>驗證

`Authentication Method`用戶端上的屬性 `CONNECT` 會定義用於此連接的驗證類型：

- `SAS`-在 `CONNECT` 的屬性中提供共用存取簽章。 `Authentication Data`
- `X509` -用戶端依賴用戶端憑證驗證。

 如果驗證方法不符合用戶端在 IoT 中樞中設定的方法，驗證就會失敗。

> [!NOTE]
> 此 API 需要 `Authentication Method` 在封包中設定屬性 `CONNECT` 。 如果 `Authentication Method` 未提供屬性，連接會失敗並出現 `Bad Request` 回應。

不支援先前 API 版本中使用的使用者名稱/密碼驗證。

#### <a name="sas"></a>SAS

使用 SAS 驗證時，用戶端必須提供連接內容的簽章。 這證明了 MQTT 連接的真實性。 簽章必須以 IoT 中樞內用戶端設定中的兩個驗證金鑰之一為基礎，或 [共用存取原則](iot-hub-devguide-security.md)的兩個共用存取金鑰之一。

要簽署的字串必須採用下列格式：

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` 衍生自 SNI 延伸 (用戶端在 TLS 信號交換期間的用戶端 Hello 記錄中提供用戶端 Hello 記錄) 或封 `host` 包中的使用者屬性 `CONNECT` 。
- `Client Id` 為封包中的用戶端識別碼 `CONNECT` 。
- `sas-policy` -如果有的話，會定義用於驗證的 IoT 中樞存取原則。 它會編碼為封包上的使用者屬性 `CONNECT` 。 選擇性：省略它表示將改用裝置登錄中的驗證設定。
- `sas-at` -如果存在，則指定連接的時間-目前時間。 它會編碼為封 `time` 包上類型的使用者屬性 `CONNECT` 。
- `sas-expiry` 定義驗證的到期時間。 它是封 `time` 包上的輸入使用者屬性 `CONNECT` 。 這是必要屬性。

若為選擇性參數，如果省略，則必須改為使用空字串來簽署。

HMAC-SHA256 可用來根據裝置的其中一個對稱金鑰來雜湊字串。 雜湊值接著會設定為屬性的值 `Authentication Data` 。

#### <a name="x509"></a>X509

如果 `Authentication Method` 屬性設定為 `X509` ，則 IoT 中樞會根據所提供的用戶端憑證來驗證連接。

#### <a name="reauthentication"></a>進行

如果使用以 SAS 為基礎的驗證，我們建議使用短期的驗證權杖。 為了持續驗證連線，並防止因為過期而中斷連線，用戶端必須透過傳送封 `AUTH` 包 (重新驗證) 來重新驗證 `Reason Code: 0x19` ：

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

規則：

- `Authentication Method` 必須與用於初始驗證的相同
- 如果最初使用以共用存取原則為基礎的 SAS 來驗證連接，則在重新驗證中使用的簽章必須以相同的原則為基礎。

如果重新驗證成功，IoT 中樞就會傳送 `AUTH` `Reason Code: 0x00` (成功) 的封包。 否則，IoT 中樞 `DISCONNECT` 會將未授權) 的封包傳送給 `Reason Code: 0x87` (，然後關閉連接。

### <a name="disconnection"></a>斷開

由於下列幾個原因，伺服器可以中斷用戶端連線：

- 用戶端的行為異常，無法以負認可的方式回應 (或直接) 的回應
- 伺服器無法維持最新的連接狀態，
- 具有相同身分識別的用戶端已連線。

伺服器可能會與 MQTT 5.0 規格中定義的任何原因程式碼中斷連線。 值得注意的提及：

- `135` 當重新驗證失敗、目前的 SAS 權杖到期或裝置的認證變更時， (未獲授權) 
- `142` 當開啟具有相同用戶端身分識別的新連線時， (會話) 。
- `159` 當 IoT 中樞的連線速率超過時，) 超過 (的連接速率  
- `131` (執行特定的錯誤) 用於此 API 中定義的任何自訂錯誤。 `status` 和 `reason` 屬性會用來傳達有關中斷連接原因的進一步詳細資料 (請參閱) 詳細資料的 [回應](#response) 。

## <a name="operations"></a>Operations

此 API 中的所有功能都會以作業表示。 以下是傳送遙測操作的範例：

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

如需此 API 中作業的完整規格，請參閱 [API 參考](iot-hub-mqtt-5-reference.md)。

> [!NOTE]
> 此規格中的所有範例都是從用戶端的觀點來顯示。 正負號 `->` 表示用戶端傳送封包（ `<-` 接收）。

### <a name="message-topics-and-subscriptions"></a>訊息主題和訂閱

此 API 的作業訊息中使用的主題開頭為 `$iothub/` 。
MQTT broker 語義不適用於這些作業 (如需詳細資料，請參閱「[開頭為 \$ ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)」的主題) 。
`$iothub/`不支援此 API 中未定義的主題開頭：

- 將訊息傳送至未定義的主題會導致 `Not Found` 回應 (如需詳細資料，請參閱下列) 的 [回應](#response) ：
- 訂閱未定義的主題會 `SUBACK` 導致 `Reason Code: 0x8F` (主題篩選不正確) 。

主題名稱和屬性名稱會區分大小寫，且必須完全相符。 例如， `$iothub/telemetry/` 在為時，不支援 `$iothub/telemetry` 。

> [!NOTE]
> 中的訂閱 `$iothub/..` 不支援萬用字元。 亦即，用戶端無法訂閱 `$iothub/+` 或 `$iothub/#` 。 嘗試這麼做 `SUBACK` `Reason Code: 0xA2` 會導致) 不支援 (萬用字元訂閱。 只支援單一區段萬用字元 (`+`) ，而不是針對具有這些參數的作業主題名稱中的路徑參數。

### <a name="interaction-types"></a>互動類型

此 API 中的所有作業都是以兩種互動類型的其中之一為基礎：

- 具有選擇性確認 (MessageAck) 的訊息
- Request-Response (ReqRep) 

作業也會依方向 (由 exchange) 中初始訊息的方向決定：

- 用戶端對伺服器 (c2s) 
- 伺服器對用戶端 (s2c) 

例如，「傳送遙測」是「訊息與通知」類型的用戶端對伺服器作業，而處理直接方法是 Request-Response 類型的伺服器對用戶端操作。

#### <a name="message-acknowledgement-interactions"></a>訊息認可互動

具有選擇性確認 (MessageAck) 互動的訊息，會以 `PUBLISH` MQTT 中的交換和封 `PUBACK` 包表示。 通知是選擇性的，而且寄件者可以選擇不透過傳送封包來要求它 `PUBLISH` `QoS: 0` 。

> [!NOTE]
> 如果封包中的屬性 `PUBACK` 由於用戶端宣告而必須被截斷 `Maximum Packet Size` ，IoT 中樞將會保留最多的使用者屬性，因為它可以容納在指定的限制範圍內。 第一個列出的使用者屬性會比之後所列的傳送更高的機會： `Reason String` 屬性的優先順序最高。

##### <a name="example-of-simple-messageack-interaction"></a>簡單 MessageAck 互動的範例

訊息：

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

確認 (成功) ：

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Request-Response 互動

在 Request-Response (ReqRep) 互動，要求和回應都會轉譯成封 `PUBLISH` 包 `QoS: 0` 。

`Correlation Data` 屬性必須在中設定，而且會用來比對回應封包與要求封包。

此 API 會 `$iothub/responses` 針對所有 ReqRep 作業使用單一回應主題。 不需要針對用戶端對伺服器作業訂閱/取消訂閱此主題-伺服器會假設所有用戶端都已訂閱。

##### <a name="example-of-simple-reqrep-interaction"></a>簡單 ReqRep 互動的範例

要求：

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

回應 (成功) ：

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep 互動不支援 `PUBLISH` 以 `QoS: 1` 要求或回應訊息的封包。 正在傳送要求 `PUBLISH` 結果以 `Bad Request` 回應。

屬性中支援的最大長度 `Correlation Data` 為16個位元組。 如果封 `Correlation Data` 包上的屬性 `PUBLISH` 設定為超過16位元組的值，IoT 中樞就會傳送 `DISCONNECT` `Bad Request` 結果，然後關閉連接。 此行為只適用于在此 API 中交換的封包。

> [!NOTE]
> 相互關聯資料是任意位元組序列，例如不保證為 UTF-8 字串。
>
> ReqRep 使用預先定義的回應主題; `PUBLISH` 如果傳送者) 設定，則會忽略要求封包 (中的回應主題屬性。

IoT 中樞會自動將用戶端訂閱至所有用戶端對伺服器 ReqRep 作業的回應主題。 即使用戶端明確取消訂閱回應主題，IoT 中樞也會自動掉訂用帳戶。 針對伺服器對用戶端 ReqRep 互動，裝置仍需要訂閱。

### <a name="message-properties"></a>郵件內容

作業屬性（系統或使用者定義）會以 MQTT 5 中的封包屬性工作表示。

使用者屬性名稱會區分大小寫，而且必須與定義的拼寫完全相同。 例如， `Trace-ID` 在為時，不支援 `trace-id` 。

如果要求的使用者屬性超出規格，而且沒有前置詞，則會 `@` 導致錯誤。

系統屬性會編碼為第一個類別屬性 (例如 `Content Type`) 或使用者屬性。 規格會提供所支援系統屬性的詳盡清單。
除非規格中明確陳述了所有的第一個類別屬性的支援，否則會予以忽略。

如果允許使用者定義的屬性，其名稱必須遵循此格式 `@{property name}` 。 使用者定義屬性只支援有效的 UTF-8 字串值。 例如， `MyProperty1` 具有值的屬性 `15` 必須編碼為具有名稱 `@MyProperty` 和值的使用者屬性 `15` 。

如果 IoT 中樞無法辨識使用者屬性，則會將其視為錯誤，且 IoT 中樞會 `PUBACK` 以 `Reason Code: 0x83` (執行特定的錯誤) ，並 `status: 0100` (錯誤的要求) 。 如果未要求確認 (QoS： 0) ， `DISCONNECT` 將會傳回具有相同錯誤的封包，並會終止連線。

此 API 除了會定義下列資料類型 `string` ：

- `time`：自之後的毫秒數 `1970-01-01T00:00:00.000Z` 。 例如， `1600987195320` 針對 `2020-09-24T22:39:55.320Z` ，
- `u32`：不帶正負號的32位整數，
- `u64`：不帶正負號的64位整數，
- `i32`：帶正負號的32位整數。

### <a name="response"></a>回應

互動可能會導致不同的結果： `Success` 、 `Bad Request` 、 `Not Found` 和其他。
每個結果都會依 `status` 使用者屬性區分。 `Reason Code` 在 `PUBACK` MessageAck 互動的封包 (中，) 相符的 `status` 意義。

> [!NOTE]
> 如果用戶端指定 `Request Problem Information: 0` 在 CONNECT 封包中，封包上將不會傳送任何使用者屬性， `PUBACK` 以符合 MQTT 5 規格，包括 `status` 屬性。 在此情況下，用戶端仍然可以依賴 `Reason Code` 來判斷確認是正面或負面。 

每個互動都有預設 (或成功) 。 它有 `Reason Code` `0` 和 `status` 屬性為「未設定」。 否則：

- 針對 MessageAck 互動， `PUBACK` `Reason Code` (Success) 的。 `status` 可能會出現屬性以進一步闡明結果。
- 針對 ReqRep 互動，回應會 `PUBLISH` 取得 `status` 屬性集。
- 因為沒有任何方法可以直接回應 MessageAck 互動，所以 `QoS: 0` `DISCONNECT` 會傳送封包，而不是回應資訊，然後再中斷連線。

範例：

錯誤的要求 (MessageAck) ：

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

未授權 (MessageAck) ：

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

未授權 (ReqRep) ：

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

需要時，IoT 中樞會設定下列使用者屬性：

- `status` -IoT 中樞的擴充程式碼，適用于作業的狀態。 這段程式碼可以用來區分結果。
- `trace-id` –作業的追蹤識別碼;IoT 中樞可能會針對可用於內部調查的作業保留額外的診斷。
- `reason` -人們看得懂的訊息，以提供進一步的資訊，說明如何在 [屬性] 指定的狀態中結束操作 `status` 。

> [!NOTE]
> 如果 [將封包中的用戶端設定 `Maximum Packet Size` ] 屬性設定為非常小的值，則並非所有的使用者屬性都可以容納，也不會出現在封包中。
> 
> `reason` 僅適用于人員，不應該用於用戶端邏輯。 此 API 可讓訊息在任何時間點變更，而不會發生任何警告或變更版本。
>
> 如果用戶端 `RequestProblemInformation: 0` 在 CONNECT 封包中傳送，使用者屬性將不會包含在每個 [MQTT 5 規格](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053)的通知中。

#### <a name="status-code"></a>狀態碼

`status` 屬性會攜帶作業的狀態碼。 它已針對機器讀取效率進行優化。
它包含兩個位元組不帶正負號的整數，在字串中編碼為十六進位 `0501` 。
程式碼結構 (位對應) ：

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

第一個位元組用於旗標：

- 位0和1表示結果的類型：
  - `00` -success
  - `01` -用戶端錯誤
  - `10` -伺服器錯誤
- 位2： `1` 表示錯誤可重試
- 位3到7是保留的，且必須設定為 `0`

第二個位元組包含實際相異的回應碼。 具有不同旗標的錯誤碼可以有相同的第二個位元組值。 例如，可能有 `0001` 、、、 `0101` `0201` `0301` 錯誤碼有相異意義。

例如， `Too Many Requests` 是一個用戶端，具有自己的程式碼的可重試錯誤 `1` 。 其值為 `0000 0101 0000 0001` 或 `0x0501` 。

用戶端可以使用類型 bits 來識別作業是否成功結束。 用戶端也可以使用可重試的位來決定是否適合重試操作。

## <a name="recommendations"></a>建議

### <a name="session-management"></a>工作階段管理

`CONNACK` 封包攜帶 `Session Present` 屬性，指出伺服器是否已還原先前建立的會話。 您可以使用此屬性來判斷是否要訂閱主題或略過訂閱，因為先前已完成訂用帳戶。

若要依賴 `Session Present` ，用戶端必須追蹤所做的訂用帳戶 (也就是已傳送的封 `SUBSCRIBE` 包，並在成功的情況下收到程式 `SUBACK` 代碼) ，或務必訂閱單一 exchange 中的所有主題 `SUBSCRIBE` / `SUBACK` 。 否則，如果用戶端傳送兩封封 `SUBSCRIBE` 包，而且伺服器已成功處理其中一個封包，伺服器將會在中進行通訊， `Session Present: 1` `CONNACK` 而只接受一部分用戶端的訂用帳戶。

若要避免舊版用戶端未訂閱所有主題，最好在用戶端行為變更 (例如，作為固件更新) 的一部分時，以無條件的方式進行訂用。 此外，為了確保不會留下過時的訂用帳戶， (從允許的訂用帳戶數目上限) 中，明確取消訂閱不再使用的訂用帳戶。

### <a name="batching"></a>批次處理

傳送訊息批次沒有特殊的格式。 為了減少 TLS 和網路中耗用大量資源之作業的額外負荷，請將封包組合 (、、等， `PUBLISH` `PUBACK` `SUBSCRIBE`) 然後再將它們交給基礎 TLS/TCP 堆疊。 此外，用戶端可以在「批次」內更輕鬆地讓主題別名：

- 在連接的第一個封包中放入完整的主題名稱 `PUBLISH` ，並將主題別名與其相關聯。
- 將下列封包放入與空白主題名稱和主題別名屬性相同的主題。

## <a name="migration"></a>遷移

此區段會列出 API 與 [先前 MQTT api](iot-hub-mqtt-support.md)相較之下的變更。

- 傳輸通訊協定為 MQTT 5。 先前 MQTT 的3.1.1。
- SAS 驗證的內容資訊會直接包含在封包中， `CONNECT` 而不是與簽章一起編碼。
- 驗證方法是用來指出使用的驗證方法。
- 共用存取簽章放在驗證資料屬性中。 使用先前的密碼欄位。
- 作業的主題不同：
  - 遙測： `$iothub/telemetry` 而不是 `devices/{Client Id}/messages/events`
  - 命令： `$iothub/commands` 而不是 `devices/{Client Id}/messages/devicebound`
  - 已報告的修補對應項： `$iothub/twin/patch/reported` 而不是 `$iothub/twin/PATCH/properties/reported`
  - 通知對應項所需的狀態已變更： `$iothub/twin/patch/desired` 而不是 `$iothub/twin/PATCH/properties/desired` 。
- 用戶端-伺服器要求-回應作業的訂用帳戶不需要回應主題。
- 在主題名稱區段中，會使用使用者屬性而非編碼屬性。
- 屬性名稱的拼寫為「虛線-案例」命名慣例，而不是具有特殊前置詞的縮寫。 使用者定義屬性現在只需要前置詞。 例如， `$.mid` 現在是 `message-id` ，而 `myProperty1` 會成為 `@myProperty1` 。
- 相互關聯資料屬性可用來將要求和回應訊息與 `$rid` 在主題中編碼的屬性相互關聯。
- `iothub-connection-auth-method` 屬性不再加上戳記的遙測事件。
- 如果沒有來自裝置的訂用帳戶，則不會清除 C2D 命令。 他們會保持佇列，直到裝置訂閱或到期為止。

## <a name="examples"></a>範例

### <a name="send-telemetry"></a>傳送遙測

訊息：

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

確認：

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

 (節流) 的替代認可：

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>傳送取得對應項的狀態

要求：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

回應 (成功) ：

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

) 不允許回應 (：

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>處理直接方法呼叫

要求：

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

回應 (成功) ：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` 未設定-這是成功的回應。

裝置無法使用的回應：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>使用 QoS 0 時發生錯誤，第一篇

要求：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

回應：

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>使用 QoS 0 時發生錯誤，第2部分

要求：

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

回應：

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>後續步驟

- 若要查看 MQTT 5 preview API 參考，請參閱 [IoT 中樞資料平面 MQTT 5 API 參考](iot-hub-mqtt-5-reference.md)。
- 若要遵循 c # 範例，請參閱 [GitHub 範例存放庫](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp)。