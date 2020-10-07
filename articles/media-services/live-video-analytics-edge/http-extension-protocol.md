---
title: HTTP 擴充通訊協定 - Azure
description: 在本文中，您將了解如何使用 HTTP 擴充通訊協定在即時影片分析模組與 AI 或 CV 模組之間傳送訊息。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: bfc8672185d284abc8f2985b44cea92000bc73db
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89448045"
---
# <a name="http-extension-protocol"></a>HTTP 擴充通訊協定

在本文中，您將了解如何使用 HTTP 擴充通訊協定在即時影片分析模組與 AI 或 CV 模組之間傳送訊息。

HTTP 合約定義於下列兩個元件之間：

* HTTP 伺服器
* IoT Edge 模組上的即時影片分析會作為 HTTP 用戶端使用

## <a name="request"></a>要求

從即時影片分析模組到 HTTP 伺服器的要求會如下所示：

|Key|值|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|接受|application/json,  */*|
|授權|基本 (Basic)、摘要 (Digest)、持有人 (Bearer) (透過自訂標頭支援)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length 主體長度 (位元組)|
|User-Agent|Azure 媒體服務|
|主體|影像位元組，以其中一種支援內容類型編碼的二進位檔。|

### <a name="example"></a>範例

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>回應

從您的模組到即時影片分析模組的回應應如下所示：

|Key|值|
|---|---|
|狀態碼|200 OK - 找到推斷結果<br/>204 無內容 - AI 找不到任何內容<br/>400 不正確的要求 - 不符合預期<br/>500 內部伺服器錯誤 - 不符合預期<br/>503 伺服器忙碌 - AMS 會根據 "Retry-After" 標頭，或根據預設的時間長度 (如果未預設標頭) 來退出作業。|
|Content-Type|application/json|
|Content-Length|主體長度 (以位元組為單位)|
|主體|具有單一「推斷」屬性的 JSON 物件。|

### <a name="example"></a>範例

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

強烈建議您在下列定義的預建結構描述之後，使用有效的 JSON 文件來傳回回應。 這可讓您更確定新增至即時影片分析模組的其他元件及未來功能具有互通性。

如果您模組傳回內容類型不是 "application/json" 的回應，即時影片分析會將訊息編碼為 base 64 內容，並將其序列化為不透明的 JSON 承載。

如果您的模組傳回內容類型為 "application/json" 的回應，但 JSON 結構描述並未遵循下面所述的推斷中繼資料結構描述，則訊息承載會透過管線轉送，但這會降低互通性。

> [!NOTE]
> 如果您的模組不會產生任何結果，則應該傳回 HTTP 204 狀態碼 (沒有內容) 與空白的回應主體。 即時影片分析將了解這是空白結果，而不會在整個管線中轉送事件。

## <a name="data-contracts---class-hierarchy"></a>資料合約 - 類別階層

![類別階層](./media/http-extension-protocol/class-hierarchy.png)

## <a name="next-steps"></a>後續步驟

[gRPC 資料合約](grpc-data-contract.md)
