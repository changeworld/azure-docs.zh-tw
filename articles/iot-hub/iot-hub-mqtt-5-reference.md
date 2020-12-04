---
title: 'Azure IoT 中樞 MQTT 5 API 參考 (預覽) '
description: 瞭解 IoT 中樞的 MQTT 5 API 參考
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603208"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>IoT 中樞資料平面 MQTT 5 API 參考

本檔定義 `2020-10-01-preview` IoT 中樞資料平面 api 的版本 2.0 (api 版本：) 中可用的作業。

## <a name="operations"></a>Operations

### <a name="get-twin"></a>取得對應項

取得對應項狀態

#### <a name="request"></a>要求

**主題名稱：**`$iothub/twin/get`

**屬性**：無

承載 **：空白**

#### <a name="success-response"></a>成功回應

**屬性**：無

承載 **：對應** 項

#### <a name="alternative-responses"></a>替代回應

| 狀態 | 名稱 | 描述 |
| :----- | :--- | :---------- |
| 0100 |  不正確的要求 | 作業訊息的格式不正確，因此無法處理。 |
| 0101 |  未授權 | 用戶端沒有執行此作業的授權。 |
| 0102 |  不允許 | 不允許作業。 |
| 0501 |  調整執行速度 | 每個 SKU 的要求率太高 |
| 0502 |  超過配額 | 超過每個目前 SKU 的每日配額 |
| 0601 |  伺服器錯誤 | 內部伺服器錯誤 |
| 0602 |  逾時 | 作業會在完成之前超時 |
| 0603 |  伺服器忙碌 | 伺服器忙碌中 |

#### <a name="pseudo-code-sample"></a>虛擬程式碼範例

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>已報告修補對應項

修補對應項的報告狀態

#### <a name="request"></a>要求

**主題名稱：**`$iothub/twin/patch/reported`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| if-version | u64 | 否 |  |

承載 **： TwinState**

#### <a name="success-response"></a>成功回應

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| version | u64 | 是 | 套用 patch 之後的回報狀態版本 |

承載 **：空白**

#### <a name="alternative-responses"></a>替代回應

| 狀態 | 名稱 | 描述 |
| :----- | :--- | :---------- |
| 0104 |  先決條件失敗 | 未滿足先決條件導致要求取消 |
| 0100 |  不正確的要求 | 作業訊息的格式不正確，因此無法處理。 |
| 0101 |  未授權 | 用戶端沒有執行此作業的授權。 |
| 0102 |  不允許 | 不允許作業。 |
| 0501 |  調整執行速度 | 每個 SKU 的要求率太高 |
| 0502 |  超過配額 | 超過每個目前 SKU 的每日配額 |
| 0601 |  伺服器錯誤 | 內部伺服器錯誤 |
| 0602 |  逾時 | 作業會在完成之前超時 |
| 0603 |  伺服器忙碌 | 伺服器忙碌中 |

#### <a name="pseudo-code-sample"></a>虛擬程式碼範例

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>接收命令

接收和處理命令

#### <a name="message"></a>訊息

**主題名稱：**`$iothub/commands`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| 順序-否 | u64 | 是 | 訊息的序號 |
| 排入佇列的時間 | time | 是 | 訊息進入系統時的時間戳記 |
| delivery-count | u32 | 是 | 嘗試傳遞訊息的次數 |
| creation-time | time | 否 | 傳送者 (提供的訊息建立時間戳記)  |
| message-id | 字串 | 否 | 傳送者提供的訊息身分識別 ()  |
| user-id | 字串 | 否 | 寄件者提供的使用者身分識別 ()  |
| correlation-id | 字串 | 否 | 傳送者提供的相互關聯身分識別 ()  |
| 內容類型 | 字串 | 否 | 判斷承載的內容類型 |
| content-encoding | 字串 | 否 | 決定承載的內容編碼 |

承載 **：任何** 位元組序列

#### <a name="success-acknowledgment"></a>成功通知

指出已接受用戶端處理的命令

**屬性**：無

承載 **：空白**

#### <a name="alternative-acknowledgments"></a>替代通知

| 原因碼 | 狀態 | 名稱 | 描述 |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | 放棄 | 指出此時將不會處理命令，而且應該在未來重新傳遞。 |
| 131 | 0100 | 拒絕 | 表示命令會被用戶端拒絕，不應再試一次。 |

#### <a name="pseudo-code-sample"></a>虛擬程式碼範例

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>接收直接方法

接收和處理直接方法呼叫

#### <a name="request"></a>要求

**主題名稱：**`$iothub/methods/{name}`

**屬性**：無

承載 **：任何** 位元組序列

#### <a name="success-response"></a>成功回應

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| 回應-代碼 | u32 | 是 |  |

承載 **：任何** 位元組序列

#### <a name="alternative-responses"></a>替代回應

| 狀態 | 名稱 | 描述 |
| :----- | :--- | :---------- |
| 06A0 |  [無法使用] | 表示無法透過此連接來連線至用戶端。 |

#### <a name="pseudo-code-sample"></a>虛擬程式碼範例

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>接收對應項所需的狀態變更

接收對應項所需狀態的更新

#### <a name="message"></a>訊息

**主題名稱：**`$iothub/twin/patch/desired`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| version | u64 | 是 | 符合此更新的預期狀態版本 |

承載 **： TwinState**

#### <a name="pseudo-code-sample"></a>虛擬程式碼範例

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>傳送遙測

將訊息張貼到遙測通道-依預設 EventHubs 或透過路由設定的其他端點。

#### <a name="message"></a>訊息

**主題名稱：**`$iothub/telemetry`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| 內容類型 | 字串 | 否 | `content-type`在張貼的訊息上轉譯為系統屬性 |
| content-encoding | 字串 | 否 | `content-encoding`在張貼的訊息上轉譯為系統屬性 |
| message-id | 字串 | 否 | `message-id`在張貼的訊息上轉譯為系統屬性 |
| user-id | 字串 | 否 | `user-id`在張貼的訊息上轉譯為系統屬性 |
| correlation-id | 字串 | 否 | `correlation-id`在張貼的訊息上轉譯為系統屬性 |
| creation-time | time | 否 | `iothub-creation-time-utc`在張貼的訊息上轉譯為屬性 |

承載 **：任何** 位元組序列

#### <a name="success-acknowledgment"></a>成功通知

訊息已成功發佈至遙測通道

**屬性**：無

承載 **：空白**

#### <a name="alternative-acknowledgments"></a>替代通知

| 原因碼 | 狀態 | 名稱 | 描述 |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | 不正確的要求 | 作業訊息的格式不正確，因此無法處理。 |
| 135 | 0101 | 未授權 | 用戶端沒有執行此作業的授權。 |
| 131 | 0102 | 不允許 | 不允許作業。 |
| 131 | 0601 | 伺服器錯誤 | 內部伺服器錯誤 |
| 151 | 0501 | 調整執行速度 | 每個 SKU 的要求率太高 |
| 151 | 0502 | 超過配額 | 超過每個目前 SKU 的每日配額 |
| 131 | 0602 | 逾時 | 作業會在完成之前超時 |
| 131 | 0603 | 伺服器忙碌 | 伺服器忙碌中 |

#### <a name="pseudo-code-sample"></a>虛擬程式碼範例

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>回應

### <a name="bad-request"></a>不正確的要求

作業訊息的格式不正確，因此無法處理。

**原因代碼：**`131`

**狀態：**`0100`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| reason | 字串 | 否 | 包含特別對訊息不正確資訊 |

承載 **：空白**

### <a name="conflict"></a>衝突

作業與另一個進行中的作業發生衝突。

**原因代碼：**`131`

**狀態：**`0103`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| 追蹤識別碼 | 字串 | 否 | 與錯誤的其他診斷相關之相互關聯的追蹤識別碼 |
| reason | 字串 | 否 | 包含特別對訊息不正確資訊 |

承載 **：空白**

### <a name="not-allowed"></a>不允許

不允許作業。

**原因代碼：**`131`

**狀態：**`0102`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| reason | 字串 | 否 | 包含特別對訊息不正確資訊 |

承載 **：空白**

### <a name="not-authorized"></a>未授權

用戶端沒有執行此作業的授權。

**原因代碼：**`135`

**狀態：**`0101`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| 追蹤識別碼 | 字串 | 否 | 與錯誤的其他診斷相關之相互關聯的追蹤識別碼 |

承載 **：空白**

### <a name="not-found"></a>找不到

要求的資源不存在

**原因代碼：**`131`

**狀態：**`0504`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| reason | 字串 | 否 | 包含特別對訊息不正確資訊 |

承載 **：空白**

### <a name="not-modified"></a>未修改

未根據提供的先決條件修改資源。

**原因代碼：**`0`

**狀態：**`0001`

**屬性**：無

承載 **：空白**

### <a name="precondition-failed"></a>先決條件失敗

未滿足先決條件導致要求取消

**原因代碼：**`131`

**狀態：**`0104`

**屬性**：無

承載 **：空白**

### <a name="quota-exceeded"></a>超過配額

超過每個目前 SKU 的每日配額

**原因代碼：**`151`

**狀態：**`0502`

**屬性**：無

承載 **：空白**

### <a name="resource-exhausted"></a>資源已用盡

資源沒有可完成作業的容量

**原因代碼：**`131`

**狀態：**`0503`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| reason | 字串 | 否 | 包含特別對訊息不正確資訊 |

承載 **：空白**

### <a name="server-busy"></a>伺服器忙碌

伺服器忙碌中

**原因代碼：**`131`

**狀態：**`0603`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| 追蹤識別碼 | 字串 | 否 | 與錯誤的其他診斷相關之相互關聯的追蹤識別碼 |

承載 **：空白**

### <a name="server-error"></a>伺服器錯誤

內部伺服器錯誤

**原因代碼：**`131`

**狀態：**`0601`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| 追蹤識別碼 | 字串 | 否 | 與錯誤的其他診斷相關之相互關聯的追蹤識別碼 |

承載 **：空白**

### <a name="target-failed"></a>目標失敗

目標已回應，但回應無效或格式不正確

**原因代碼：**`131`

**狀態：**`06A2`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| reason | 字串 | 否 | 包含特別對訊息不正確資訊 |

承載 **：空白**

### <a name="target-timeout"></a>目標 Timeout

等候目標完成要求時發生超時

**原因代碼：**`131`

**狀態：**`06A1`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| 追蹤識別碼 | 字串 | 否 | 與錯誤的其他診斷相關之相互關聯的追蹤識別碼 |
| reason | 字串 | 否 | 包含特別對訊息不正確資訊 |

承載 **：空白**

### <a name="target-unavailable"></a>目標無法使用

無法連線到目標以完成要求

**原因代碼：**`131`

**狀態：**`06A0`

**屬性**：無

承載 **：空白**

### <a name="throttled"></a>調整執行速度

每個 SKU 的要求率太高

**原因代碼：**`151`

**狀態：**`0501`

**屬性**：無

承載 **：空白**

### <a name="timeout"></a>逾時

作業會在完成之前超時

**原因代碼：**`131`

**狀態：**`0602`

**屬性**：

| 名稱 | 類型 | 必要 | 描述 |
| :--- | :--- | :------- | :---------- |
| 追蹤識別碼 | 字串 | 否 | 與錯誤的其他診斷相關之相互關聯的追蹤識別碼 |

承載 **：空白**

