---
title: 在 IoT Edge 的即時影片分析中使用直接方法-Azure
description: IoT Edge 上的即時影片分析會公開數個直接方法。 直接方法是以本主題中所述的慣例為基礎。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8b5c16dc72beed4ec757e48461a2fc194c113f8d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656235"
---
# <a name="direct-methods"></a>直接方法

IoT Edge 上的即時影片分析會公開數個可從 IoT 中樞叫用的直接方法。 直接方法代表與裝置的要求-回覆互動，類似於 HTTP 呼叫，因為會立即成功或失敗 (在使用者指定的逾時之後)。 針對立即動作的進展取決於裝置是否能夠回應的案例，此方法會相當有用。 如需詳細資訊，請參閱 [瞭解並從 IoT 中樞叫用直接方法](../../iot-hub/iot-hub-devguide-direct-methods.md)。

本主題說明這些方法和慣例。

## <a name="conventions"></a>慣例

直接方法是以下列慣例為基礎：

1. 直接方法具有在主要中指定的版本。次要格式 (如下列範例所示) ：

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

2. 特定版本的 IoT Edge 模組上的即時影片分析，將會支援最新版本的所有直接方法。 例如，模組版本1.3 將支援具有1.3、1.2、1.1 和1.0 版的直接方法。
3. 所有直接方法都是同步的。
4. 錯誤結果會遵循 OData 錯誤架構。
5. 名稱應該會觀察下列條件約束：
    
    * 只有英數位元和連字號，只要它不是以虛線開頭和結尾
    * 沒有空格
    * 最大值32個字元

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
|402|   ConnectivityRequired    |Edge 模組需要雲端連線才能正常運作。|
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

|狀態|    詳細的程式碼   |描述|
|---|---|---|
|400|   GraphValidationError|   一般圖形錯誤，例如迴圈或資料分割等等。|
|400|   ModuleValidationError|  模組特定的驗證錯誤。|
|409|   GraphTopologyInUse| 圖形拓撲仍會由一或多個圖形實例參考。|
|409|   OperationNotAllowedInState| 要求的作業無法以目前的狀態執行。|
|409|   ResourceValidationError|    參考的資源 (範例：資產) 不是處於有效的狀態。|

## <a name="details"></a>詳細資料  

### <a name="graphtopologyget"></a>GraphTopologyGet

此直接方法會抓取單一的圖形拓撲。

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

| 條件 | 狀態碼 | 詳細錯誤碼 |
|--|--|--|
| 找到實體 | 200 | N/A |
| 一般使用者錯誤 | 400 範圍 |  |
| 找不到實體 | 404 |  |
| 一般伺服器錯誤 | 500 範圍 |  |

### <a name="graphtopologyset"></a>GraphTopologySet

如果沒有具有指定名稱或更新的現有拓撲，以及具有相同名稱的現有拓撲，則建立單一拓撲。

主要層面：

* 拓撲可以自由更新，沒有圖表參考它。
* 如果所有參考圖形都已停用，則拓撲可以自由更新，只要：

    * 新加入的參數具有預設值
    * 任何圖形都未參考移除的參數
* 如果有使用中的圖形，則不允許拓撲更新

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
一般使用者錯誤 |400 範圍  ||
圖形驗證錯誤 |400    |GraphValidationError|
模組驗證錯誤|   400 |ModuleValidationError|
一般伺服器錯誤   |500 範圍  ||

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

| 條件 | 狀態碼 | 詳細錯誤碼 |
|--|--|--|
| 已刪除實體 | 200 | N/A |
| 找不到實體 | 204 | N/A |
| 一般使用者錯誤 | 400 範圍 |  |
| 一或多個圖形實例正在參考圖形拓撲 | 409 | GraphTopologyInUse |
| 一般伺服器錯誤 | 500 範圍 |  |

### <a name="graphtopologylist"></a>GraphTopologyList

抓取符合篩選準則的所有圖表拓撲清單。

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

|作業 |欄位 (s)     |運算子|
|---|---|---|
|$orderby|NAME  |Asc|


#### <a name="status-codes"></a>狀態碼

| 條件 | 狀態碼 | 詳細錯誤碼 |
|--|--|--|
| Success | 200 | N/A |
| 一般使用者錯誤 | 400 範圍 |  |
| 一般伺服器錯誤 | 500 範圍 |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

捕獲單一圖形實例：

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

| 條件 | 狀態碼 | 詳細錯誤碼 |
|--|--|--|
| 找到實體 | 200 | N/A |
| 一般使用者錯誤 | 400 範圍 |  |
| 找不到實體 | 404 |  |
| 一般伺服器錯誤 | 500 範圍 |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

如果沒有具有指定名稱或更新的現有圖形實例，以及具有相同名稱的現有實例，則會建立一個。

主要層面：

* 圖形實例可在「已停用」狀態時自由更新。

    * 圖形會在每次更新時重新驗證。
* 當圖形不是處於「非作用中」狀態時，會部分限制圖形實例更新。
* 活動圖形上不允許圖形實例更新。

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

| 條件 | 狀態碼 | 詳細錯誤碼 |
|--|--|--|
| 已更新現有實體 | 200 | N/A |
| 已建立新實體 | 201 | N/A |
| 一般使用者錯誤 | 400 範圍 |  |
| 圖形驗證錯誤 | 400 | GraphValidationError |
| 模組驗證錯誤 | 400 | ModuleValidationError |
| 資源驗證錯誤 | 409 | ResourceValidationError |
| 一般伺服器錯誤 | 500 範圍 |  |  |

### <a name="graphinstancedelete"></a>GraphInstanceDelete

刪除單一圖形實例。

主要層面：

* 您只能刪除已停用的圖形。

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

| 條件 | 狀態碼 | 詳細錯誤碼 |
|--|--|--|
| 已成功刪除圖形 | 200 | N/A |
| 找不到圖形 | 204 | N/A |
| 一般使用者錯誤 | 400 範圍 |  |
| 圖形未處於「已停止」狀態 | 409 | OperationNotAllowedInState |
| 一般伺服器錯誤 | 500 範圍 |  |

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

|作業  |   欄位 (s) |   運算子|
|---|---|---|
|$orderby|  NAME|   Asc|

#### <a name="status-codes"></a>狀態碼

| 條件 | 狀態碼 | 詳細錯誤碼 |
|--|--|--|
| Success | 200 | N/A |
| 一般使用者錯誤 | 400 範圍 |  |
| 一般伺服器錯誤 | 500 範圍 |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

啟用單一圖形實例。 

重要層面

* 只有在啟用圖形時，才會傳回方法 
* 圖形在啟用時，會假設「正在啟動」狀態。

    * 圖形上的 List/Get 作業會傳回適當狀態的圖表。
* 等冪性

    * 在「啟動中」狀態啟動圖形的行為方式，與停用圖形的方式相同 (也就是：呼叫區塊直到圖形啟動為止) 
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

| 條件 | 狀態碼 | 詳細錯誤碼 |
|--|--|--|
| 已成功啟用圖表 | 200 | N/A |
| 已建立新實體 | 201 | N/A |
| 一般使用者錯誤 | 400 範圍 |  |
| 模組驗證錯誤 | 400 | ModuleValidationError |
| 資源驗證錯誤 | 409 | ResourceValidationError |
| 圖形處於停用狀態 | 409 | OperationNotAllowedInState |
| 一般伺服器錯誤 | 500 範圍 |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

停用單一圖形實例。 停用圖形可正常停用媒體處理，並確保儲存在 edge 上的所有事件和媒體暫時都會認可至雲端（如果適用）。

主要層面：

* 只有在停用圖形時，才會傳回方法
* Graph 在停用時，會假設「停用」狀態。

    * 圖形上的 List/Get 作業會傳回適當狀態的圖表。
    * 只有在所有媒體都上傳至雲端時，停止才會完成。
* 等冪性

    * 停用「停用」狀態的圖形的行為方式與停用圖形的方式相同 (也就是：呼叫會封鎖，直到 graph 停用為止) 
    * 停用「非作用中」狀態的圖形時，會立即成功返回。

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

| 條件 | 狀態碼 | 詳細錯誤碼 |
|--|--|--|
| 已成功啟用圖表 | 200 | N/A |
| 已建立新實體 | 201 | N/A |
| 一般使用者錯誤 | 400 範圍 |  |
| 圖形處於啟用狀態 | 409 | OperationNotAllowedInState |
| 一般伺服器錯誤 | 500 範圍 |  |

## <a name="next-steps"></a>後續步驟

[模組對應項組態結構描述](module-twin-configuration-schema.md)
