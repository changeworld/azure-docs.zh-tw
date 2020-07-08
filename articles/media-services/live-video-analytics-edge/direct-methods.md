---
title: 在 IoT Edge 上使用即時影片分析中的直接方法-Azure
description: IoT Edge 上的即時影片分析會公開數個直接方法。 直接方法是以本主題中所述的慣例為基礎。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: b87452de6b12b0335afca5e28abb3ef6adb29157
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260801"
---
# <a name="direct-methods"></a>直接方法

IoT Edge 上的即時影片分析會公開數個可從 IoT 中樞叫用的直接方法。 直接方法代表與裝置的要求-回覆互動，類似於 HTTP 呼叫，因為會立即成功或失敗 (在使用者指定的逾時之後)。 針對立即動作的進展取決於裝置是否能夠回應的案例，此方法會相當有用。 如需詳細資訊，請參閱[瞭解和叫用來自 IoT 中樞的直接方法](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods)。

本主題描述這些方法和慣例。

## <a name="conventions"></a>慣例

直接方法是根據下列慣例：

1. 直接方法具有在 [主要] 中指定的版本。次要格式（如下列範例所示）：

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. IoT Edge 模組上特定版本的即時影片分析，將會支援最新版本的所有直接方法。 例如，模組版本1.3 將支援版本1.3、1.2、1.1 和1.0 版本的直接方法。
3. 所有直接方法都是同步的。
4. 錯誤結果會遵循 OData 錯誤架構。
5. 名稱應該會觀察下列條件約束：
    
    * 只有英數位元和連字號，但前提是它不是以破折號開頭和結尾
    * 無空格
    * 最多32個字元

### <a name="example"></a>範例

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>最上層錯誤碼     

|狀態 |程式碼   |訊息|
|---|---|---|
|400|   BadRequest| 要求無效|
|400|   InvalidResource|    資源無效|
|400|   InvalidVersion| API 版本無效|
|402|   ConnectivityRequired    |Edge 模組需要雲端連線能力才能正常運作。|
|404|   NotFound    |找不到資源|
|409|   衝突|   操作衝突|
|500|   InternalServerError|    內部伺服器錯誤|
|503|   ServerBusy| 伺服器忙碌中|

### <a name="detailed-error-codes"></a>詳細的錯誤碼

詳細的驗證錯誤（例如圖形模組驗證）會新增為錯誤詳細資料：

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|狀態|    詳細程式碼   |Description|
|---|---|---|
|400|   GraphValidationError|   一般圖形錯誤，例如迴圈或資料分割等等。|
|400|   ModuleValidationError|  模組特定驗證錯誤。|
|409|   GraphTopologyInUse| 一或多個圖形實例仍會參考圖形拓撲。|
|409|   OperationNotAllowedInState| 無法在目前狀態中執行要求的作業。|
|409|   ResourceValidationError|    參考的資源（範例：資產）不是處於有效的狀態。|

## <a name="details"></a>詳細資料  

### <a name="graphtopologyget"></a>GraphTopologyGet

此直接方法會抓取單一圖形拓撲。

#### <a name="request"></a>要求

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>回應

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>狀態碼

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
|找到實體|  200 |N/A
|一般使用者錯誤    |400範圍  ||
|找不到實體   |404        ||
|一般伺服器錯誤| 500範圍       ||

### <a name="graphtopologyset"></a>GraphTopologySet

如果沒有具有指定名稱或更新的現有拓撲，或具有相同名稱的現有，則建立單一拓朴。

重要層面：

* 拓撲可以免費更新，因為沒有圖表參考它。
* 如果所有參考的圖形都已停用，則可以自由更新拓撲，只要：

    * 新加入的參數具有預設值
    * 所有圖形都不會參考已移除的參數
* 如果有作用中的圖形，則不允許拓撲更新

#### <a name="request"></a>要求

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>回應

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>狀態碼

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
已更新現有實體 |200|   N/A|
已建立新實體  |201|   N/A|
一般使用者錯誤 |400範圍  ||
圖形驗證錯誤 |400    |GraphValidationError|
模組驗證錯誤|   400 |ModuleValidationError|
一般伺服器錯誤   |500範圍  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

刪除單一圖形拓撲。

#### <a name="request"></a>要求

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>回應

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>狀態碼

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
|已刪除實體|    200|    N/A|
|找不到實體|  204|    N/A|
|一般使用者錯誤|   400範圍   ||
|一或多個圖形實例正在參考圖形拓撲| 409 |GraphTopologyInUse|
|一般伺服器錯誤| 500範圍   ||

### <a name="graphtopologylist"></a>GraphTopologyList

抓取符合篩選準則的所有圖形拓撲清單。

#### <a name="request"></a>要求

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>回應

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>篩選支援

|操作      |欄位   |操作員|
|---|---|---|
|$orderby|NAME  |asc|


#### <a name="status-codes"></a>狀態碼

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
|成功|   200 |N/A|
|一般使用者錯誤|   400範圍   ||
|一般伺服器錯誤| 500範圍   ||

### <a name="graphinstanceget"></a>GraphInstanceGet

抓取單一圖形實例：

#### <a name="request"></a>要求

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>回應

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>狀態碼

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
|找到實體   |200|   N/A|
|一般使用者錯誤|   400範圍   ||
|找不到實體|  404 ||
|一般伺服器錯誤| 500範圍   ||

### <a name="graphinstanceset"></a>GraphInstanceSet

如果沒有具有指定名稱或更新的現有一個圖形實例，而且現有的實例具有相同的名稱，則會建立一個。

重要層面：

* 在「停用」狀態時，可以免費更新圖形實例。

    * 每次更新時，會重新驗證圖表。
* 當圖形不是處於「非作用中」狀態時，圖表實例更新會部分受到限制。
* 活動圖表上不允許有圖表實例更新。

#### <a name="request"></a>要求

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>回應

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>狀態碼

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
|已更新現有實體    |200    |N/A|
|已建立新實體|    201 |N/A|
|一般使用者錯誤|   400範圍   ||
|圖形驗證錯誤    |400|   GraphValidationError|
|模組驗證錯誤|  400 |ModuleValidationError|
|資源驗證錯誤 |409    |ResourceValidationError|
|一般伺服器錯誤  |500範圍||    

### <a name="graphinstancedelete"></a>GraphInstanceDelete

刪除單一圖形實例。

重要層面：

* 只能刪除已停用的圖形。

#### <a name="request"></a>要求

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>回應

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>狀態碼

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
|已成功刪除圖形|    200|    N/A|
|找不到圖形|   204|    N/A|
|一般使用者錯誤    |400範圍  ||
|圖形不是處於「已停止」狀態    |409    |OperationNotAllowedInState|
|一般伺服器錯誤| 500範圍   ||

### <a name="graphinstancelist"></a>GraphInstanceList

這類似于 GraphTopologyList。 它可讓您用來列舉圖形實例。
抓取符合篩選準則的所有圖形實例清單。

#### <a name="request"></a>要求

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>回應

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>篩選支援

|操作  |   欄位|   操作員|
|---|---|---|
|$orderby|  NAME|   asc|

#### <a name="status-codes"></a>狀態碼

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
|成功    |200    |N/A|
|一般使用者錯誤|   400範圍   ||
|一般伺服器錯誤| 500範圍   ||

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

啟用單一圖形實例。 

重要層面

* 只有在啟用 graph 時，方法才會傳回 
* Graph 會在啟動時假設「啟用」狀態。

    * 圖表上的清單/取得作業會以適當的狀態傳回圖形。
* 等冪性

    * 以「啟用」狀態啟動圖表的行為與停用圖形的方式相同（也就是：呼叫區塊直到啟動圖表為止）
    * 啟用「作用中」狀態的圖形時，會立即成功傳回。

#### <a name="request"></a>要求

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>回應

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>狀態碼

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
|已成功啟用圖形   |200    |N/A|
|已建立新實體 |201|   N/A|
|一般使用者錯誤    |400範圍  ||
|模組驗證錯誤   |400|   ModuleValidationError|
|資源驗證錯誤|    409|    ResourceValidationError|
|圖形處於停用狀態 |409    |OperationNotAllowedInState|
|一般伺服器錯誤| 500範圍   ||

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

停用單一圖形實例。 停用圖形會正常停用媒體處理，並確保所有儲存在 edge 上的事件和媒體暫時都會認可到雲端（如果適用的話）。

重要層面：

* 只有在停用 graph 時，方法才會傳回
* Graph 在停用時，會假設「停用」狀態。

    * 圖表上的清單/取得作業會以適當的狀態傳回圖形。
    * 只有當所有媒體都已上傳至雲端時，[停止] 才會完成。
* 等冪性

    * 停用「停用」狀態的圖表，其行為方式與停用圖形相同（也就是在停用 graph 之前呼叫區塊）。
    * 停用「非作用中」狀態的圖表會立即成功傳回。

#### <a name="request"></a>要求

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>回應

```
{
    "status": 200,
    "payload": null
}
```

|條件  |狀態碼    |詳細錯誤碼|
|---|---|---|
|已成功啟用圖形   |200|   N/A|
|已建立新實體 |201|   N/A|
|一般使用者錯誤    |400範圍  ||
|圖形處於啟用狀態   |409|   OperationNotAllowedInState|
|一般伺服器錯誤  |500範圍  ||

## <a name="next-steps"></a>後續步驟

[模組對應項組態結構描述](module-twin-configuration-schema.md)
