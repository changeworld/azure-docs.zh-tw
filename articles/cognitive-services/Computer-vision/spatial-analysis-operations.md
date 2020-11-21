---
title: 空間分析作業
titleSuffix: Azure Cognitive Services
description: 空間分析作業。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 9d1b6e20bf2decfe051e79e073736f71181260fa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014538"
---
# <a name="spatial-analysis-operations"></a>空間分析作業

空間分析可讓您分析來自相機裝置的即時串流影片。 針對您設定的每個相機裝置，空間分析的作業會產生傳送至 Azure IoT 中樞執行個體的 JSON 訊息輸出資料流。 

空間分析容器會執行下列作業：

| 作業識別碼| Description|
|---------|---------|
| cognitiveservices，spatialanalysis-personcount | 在相機的視圖欄位中，計算指定區域內的人員。 <br> 發出初始 _personCountEvent_ 事件，然後在計數變更時 _personCountEvent_ 事件。  |
| cognitiveservices，spatialanalysis-personcrossingline | 追蹤某位人員如何在相機的視圖欄位中，跨越指定的行。 <br>當人員跨越該行並提供方向資訊時，發出 _personLineEvent_ 事件。 
| cognitiveservices，spatialanalysis-personcrossingpolygon | 追蹤某位人員如何在相機的視圖欄位中，跨越指定的行。 <br> 當人員跨越區域時發出 _personLineEvent_ 事件，並提供方向性資訊。 |
| cognitiveservices，spatialanalysis-persondistance | 追蹤使用者何時違反距離規則。 <br> 使用每個距離違規的位置定期發出 _personDistanceEvent_ 。 |

您也可以在版本中使用上述所有作業 `.debug` ，這可讓您在處理影片框架時將其視覺化。 您必須 `xhost +` 在主機電腦上執行，才能啟用影片框架和事件的視覺效果。

| 作業識別碼| Description|
|---------|---------|
| cognitiveservices spatialanalysis-personcount. debug | 在相機的視圖欄位中，計算指定區域內的人員。 <br> 發出初始 _personCountEvent_ 事件，然後在計數變更時 _personCountEvent_ 事件。  |
| cognitiveservices spatialanalysis-personcrossingline. debug | 追蹤某位人員如何在相機的視圖欄位中，跨越指定的行。 <br>當人員跨越該行並提供方向資訊時，發出 _personLineEvent_ 事件。 
| cognitiveservices spatialanalysis-personcrossingpolygon. debug | 追蹤某位人員如何在相機的視圖欄位中，跨越指定的行。 <br> 當人員跨越區域時發出 _personLineEvent_ 事件，並提供方向性資訊。 |
| cognitiveservices spatialanalysis-persondistance. debug | 追蹤使用者何時違反距離規則。 <br> 使用每個距離違規的位置定期發出 _personDistanceEvent_ 。 |

您也可以使用 [即時影片分析](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) 作為影片 AI 模組來執行空間分析。 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| 作業識別碼| Description|
|---------|---------|
| cognitiveservices，spatialanalysis-personcount. livevideoanalytics | 在相機的視圖欄位中，計算指定區域內的人員。 <br> 發出初始 _personCountEvent_ 事件，然後在計數變更時 _personCountEvent_ 事件。  |
| cognitiveservices，spatialanalysis-personcrossingline. livevideoanalytics | 追蹤某位人員如何在相機的視圖欄位中，跨越指定的行。 <br>當人員跨越該行並提供方向資訊時，發出 _personLineEvent_ 事件。 
| cognitiveservices，spatialanalysis-personcrossingpolygon. livevideoanalytics | 追蹤某位人員如何在相機的視圖欄位中，跨越指定的行。 <br> 當人員跨越區域時發出 _personLineEvent_ 事件，並提供方向性資訊。 |
| cognitiveservices，spatialanalysis-persondistance. livevideoanalytics | 追蹤使用者何時違反距離規則。 <br> 使用每個距離違規的位置定期發出 _personDistanceEvent_ 。 |

您也可以在 (版中取得即時影片分析作業， `.debug` 例如 cognitiveservices. spatialanalysis-personcount) .，這項功能可將影片框架視覺化以進行處理。 您必須 `xhost +` 在主機電腦上執行，才能啟用影片畫面和事件的視覺效果

> [!IMPORTANT]
> 電腦視覺 AI 模型會使用人為主體周圍的周框方塊，偵測並找出影片素材和輸出中的人為存在。 AI 模型不會嘗試偵測臉部，或找出個人的身分識別或人口統計。

這些是每個空間分析作業所需的參數。

| 作業參數| Description|
|---------|---------|
| 作業識別碼 | 上表中的運算識別碼。|
| 已啟用 | 布林值： true 或 false|
| VIDEO_URL| 攝影機裝置的 RTSP url (範例： `rtsp://username:password@url`) 。 空間分析可透過 RTSP、HTTP 或使用方式，支援 h.264 編碼的資料流程 |
| VIDEO_SOURCE_ID | 攝影機裝置或影片串流的易記名稱。 這會隨事件 JSON 輸出傳回。|
| VIDEO_IS_LIVE| 針對相機裝置則為 True;針對錄製的影片則為 false。|
| VIDEO_DECODE_GPU_INDEX| 要解碼影片框架的 GPU。 預設為0。 應該與 `gpu_index` 其他節點設定中的相同 `VICA_NODE_CONFIG` ，例如， `DETECTOR_NODE_CONFIG` 。|
| DETECTOR_NODE_CONFIG | JSON，指出要在哪個 GPU 上執行偵測器節點。 應採用下列格式： `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | 區域和行的 JSON 設定，如下所述。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Cognitiveservices 的區域設定-spatialanalysis-personcount

 這是設定區域之 SPACEANALYTICS_CONFIG 參數的 JSON 輸入範例。 您可以為這項操作設定多個區域。

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| 名稱 | 類型| Description|
|---------|---------|---------|
| `zones` | list| 區域清單。 |
| `name` | 字串| 此區域的易記名稱。|
| `polygon` | list| 每個值組都代表多邊形頂點的 x，y。 多邊形代表人們的追蹤或計算區域，而多邊形點是以正規化座標 (0-1) 為基礎，其中左上角是 (0.0，0.0) ，右下角為 (1.0，1.0) 。   
| `threshold` | FLOAT| 當 AI 模型的信賴度大於或等於此值時，就會輸出事件。 |
| `type` | 字串| 針對 **cognitiveservices spatialanalysis-personcount** 這應該是 `count` 。|
| `trigger` | 字串| 傳送事件的觸發程式類型。 支援的值是在 `event` 計數變更時傳送事件 `interval` ，或是定期傳送事件（不論計數是否已變更）。
| `interval` | 字串| 以秒為單位的時間，此時間會在引發事件之前匯總人員計數。 作業會繼續以固定速率分析場景，並傳回該間隔最常見的計數。 匯總間隔適用于 `event` 和 `interval` 。|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>適用于 cognitiveservices 的行配置 spatialanalysis-personcrossingline

這是設定行之 SPACEANALYTICS_CONFIG 參數的 JSON 輸入範例。 您可以為這項作業設定多個跨線。

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| 名稱 | 類型| Description|
|---------|---------|---------|
| `lines` | list| 行的清單。|
| `name` | 字串| 這一行的易記名稱。|
| `line` | list| 行的定義。 這是一條方向線，可讓您瞭解「輸入」與「結束」。|
| `start` | 值組| 線條起點的 x，y 座標。 Float 值代表頂點相對於左上角的位置。 若要計算絕對 x，y 值，您可以將這些值乘以框架大小。 |
| `end` | 值組| x，y 軸座標，代表線條的結束點。 Float 值代表頂點相對於左上角的位置。 若要計算絕對 x，y 值，您可以將這些值乘以框架大小。 |
| `threshold` | FLOAT| 當 AI 模型的信賴度大於或等於此值時，就會輸出事件。 |
| `type` | 字串| 針對 **cognitiveservices spatialanalysis-personcrossingline** 這應該是 `linecrossing` 。|
|`trigger`|字串|傳送事件的觸發程式類型。<br>支援的值：「事件」：當有人跨越該行時引發。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Cognitiveservices 的區域設定-spatialanalysis-personcrossingpolygon

這是設定區域之 SPACEANALYTICS_CONFIG 參數的 JSON 輸入範例。 您可以為這項操作設定多個區域。

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| 名稱 | 類型| Description|
|---------|---------|---------|
| `zones` | list| 區域清單。 |
| `name` | 字串| 此區域的易記名稱。|
| `polygon` | list| 每個值組都代表多邊形頂點的 x，y。 多邊形代表追蹤或計算人員的區域。 Float 值代表頂點相對於左上角的位置。 若要計算絕對 x，y 值，您可以將這些值乘以框架大小。 
| `threshold` | FLOAT| 當 AI 模型的信賴度大於或等於此值時，就會輸出事件。 |
| `type` | 字串| 若為 **cognitiveservices，spatialanalysis-personcrossingpolygon** 應該是 `enter` 或 `exit` 。|
| `trigger`|字串|傳送事件的觸發程式類型<br>支援的值：「事件」：當有人進入或離開區域時引發。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Cognitiveservices 的區域設定-spatialanalysis-persondistance

這是 SPACEANALYTICS_CONFIG 參數的 JSON 輸入範例，其會設定 cognitiveservices 的區域。 **spatialanalysis-persondistance**。 您可以為這項操作設定多個區域。

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| 名稱 | 類型| Description|
|---------|---------|---------|
| `zones` | list| 區域清單。 |
| `name` | 字串| 此區域的易記名稱。|
| `polygon` | list| 每個值組都代表多邊形頂點的 x，y。 多邊形代表計算人員的區域，以及測量人員之間的距離。 Float 值代表頂點相對於左上角的位置。 若要計算絕對 x，y 值，您可以將這些值乘以框架大小。 
| `threshold` | FLOAT| 當 AI 模型的信賴度大於或等於此值時，就會輸出事件。 |
| `type` | 字串| 針對 **cognitiveservices spatialanalysis-persondistance** 這應該是 `people_distance` 。|
| `trigger` | 字串| 傳送事件的觸發程式類型。 支援的值是在 `event` 計數變更時傳送事件 `interval` ，或是定期傳送事件（不論計數是否已變更）。
| `interval` | 字串 | 在引發事件之前，將會匯總違規的時間（以秒為單位）。 匯總間隔適用于 `event` 和 `interval` 。|
| `output_frequency` | int | 輸出事件的速率。 當 `output_frequency` = X 時，每個 X 事件都是輸出，例如 `output_frequency` = 2 表示其他每個事件都是輸出。 Output_frequency 適用于 `event` 和 `interval` 。|
| `minimum_distance_threshold` | FLOAT| 當使用者小於該距離時，將會觸發「TooClose」事件的距離（以英尺為間隔）。|
| `maximum_distance_threshold` | FLOAT| 當使用者大於該距離時，將會觸發「TooFar」事件的距離（以英尺為間隔）。|

這是 DETECTOR_NODE_CONFIG 參數的 JSON 輸入範例，可設定 **cognitiveservices spatialanalysis-persondistance** 區域。

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| 名稱 | 類型| Description|
|---------|---------|---------|
| `gpu_index` | 字串| 將執行此作業的 GPU 索引。|
| `do_calibration` | 字串 | 表示已開啟校正。 `do_calibration` cognitiveservices 必須是 true， **spatialanalysis-persondistance** 才能正常運作。|

請參閱 [攝影機放置](spatial-analysis-camera-placement.md)  指導方針，以瞭解區域和線路設定。

## <a name="spatial-analysis-operation-output"></a>空間分析作業輸出

每項作業的事件都會輸出為 JSON 格式的 Azure IoT 中樞。

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>適用于 cognitiveservices 的 JSON 格式-spatialanalysis-personcount AI 見解

這項作業之事件輸出的範例 JSON。

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 事件功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 事件識別碼|
| `type` | 字串| 事件類型|
| `detectionsId` | array| 觸發這個事件之人員偵測唯一識別碼的大小1陣列|
| `properties` | collection| 值的集合|
| `trackinId` | 字串| 偵測到的人員唯一識別碼|
| `status` | 字串| ' Enter ' 或 ' Exit '|
| `side` | int| 該人員所交叉多邊形的邊數|
| `zone` | 字串 | 多邊形的「名稱」欄位，代表超過的區域|
| `trigger` | 字串| 觸發程式類型為「事件」或「間隔」，視 SPACEANALYTICS_CONFIG 中的值而定。 `trigger`|

| 偵測功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 偵測識別碼|
| `type` | 字串| 偵測類型|
| `region` | collection| 值的集合|
| `type` | 字串| 區欄位型別|
| `points` | collection| 當區欄位型別是矩形時，左上角和右下角的點 |
| `confidence` | FLOAT| 演算法信賴度|

| SourceInfo 功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 相機識別碼|
| `timestamp` | date| 發出 JSON 承載時的 UTC 日期|
| `width` | int | 影片框架寬度|
| `height` | int | 影片框架高度|
| `frameId` | int | 畫面格識別碼|
| `cameraCallibrationInfo` | collection | 值的集合|
| `status` | 字串 | 指出是否將攝影機校正至地面平面為「完成」|
| `cameraHeight` | FLOAT | 攝影機的高度，以英尺為基礎。 這是從自動校正推斷而來。 |
| `focalLength` | FLOAT | 攝影機的焦距長度（以圖元為單位）。 這是從自動校正推斷而來。 |
| `tiltUpAngle` | FLOAT | 攝影機從垂直傾斜角度。 這是從自動校正推斷而來。|

| SourceInfo 功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 相機識別碼|
| `timestamp` | date| 發出 JSON 承載時的 UTC 日期|
| `width` | int | 影片框架寬度|
| `height` | int | 影片框架高度|
| `frameId` | int | 畫面格識別碼|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>適用于 cognitiveservices 的 JSON 格式-spatialanalysis-personcrossingline AI 見解

這項作業的偵測輸出範例 JSON。

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| 事件功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 事件識別碼|
| `type` | 字串| 事件類型|
| `detectionsId` | array| 觸發這個事件之人員偵測唯一識別碼的大小1陣列|
| `properties` | collection| 值的集合|
| `trackinId` | 字串| 偵測到的人員唯一識別碼|
| `status` | 字串| 直線交點的方向，也就是 ' CrossLeft ' 或 ' CrossRight '|
| `zone` | 字串 | 超過的行的 [名稱] 欄位|

| 偵測功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 偵測識別碼|
| `type` | 字串| 偵測類型|
| `region` | collection| 值的集合|
| `type` | 字串| 區欄位型別|
| `points` | collection| 當區欄位型別是矩形時，左上角和右下角的點 |
| `confidence` | FLOAT| 演算法信賴度|

| SourceInfo 功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 相機識別碼|
| `timestamp` | date| 發出 JSON 承載時的 UTC 日期|
| `width` | int | 影片框架寬度|
| `height` | int | 影片框架高度|
| `frameId` | int | 畫面格識別碼|


> [!IMPORTANT]
> AI 模型會偵測某位人員，不論該人員是否朝向相機。 AI 模型不會執行臉部偵測或辨識，也不會發出任何生物特徵辨識資訊。 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>適用于 cognitiveservices 的 JSON 格式-spatialanalysis-personcrossingpolygon AI 見解

這項作業的偵測輸出範例 JSON。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 事件功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 事件識別碼|
| `type` | 字串| 事件類型|
| `detectionsId` | array| 觸發這個事件之人員偵測唯一識別碼的大小1陣列|
| `properties` | collection| 值的集合|
| `trackinId` | 字串| 偵測到的人員唯一識別碼|
| `status` | 字串| 多邊形交叉的方向，也就是「Enter」或「Exit」|
| `zone` | 字串 | 多邊形的「名稱」欄位，代表超過的區域|

| 偵測功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 偵測識別碼|
| `type` | 字串| 偵測類型|
| `region` | collection| 值的集合|
| `type` | 字串| 區欄位型別|
| `points` | collection| 當區欄位型別是矩形時，左上角和右下角的點 |
| `confidence` | FLOAT| 演算法信賴度|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>適用于 cognitiveservices 的 JSON 格式-spatialanalysis-persondistance AI 見解

這項作業的偵測輸出範例 JSON。

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 事件功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 事件識別碼|
| `type` | 字串| 事件類型|
| `detectionsId` | array| 觸發這個事件之人員偵測唯一識別碼的大小1陣列|
| `properties` | collection| 值的集合|
| `personCount` | int| 發出事件時偵測到的人員數目|
| `averageDistance` | FLOAT| 所有偵測到的人員在墊腳之間的平均距離|
| `minimumDistanceThreshold` | FLOAT| 當使用者小於該距離時，將會觸發「TooClose」事件的距離（以英尺為間隔）。|
| `maximumDistanceThreshold` | FLOAT| 當使用者大於距離時，將會觸發「TooFar」事件的距離（以英尺為間隔）。|
| `eventName` | 字串| 已違反的事件名稱是 `TooClose` `minimumDistanceThreshold` ， `TooFar` 違反時 `maximumDistanceThreshold` 或 `unknown` 自動校正未完成時|
| `distanceViolationPersonCount` | int| 偵測到或發生違規的人數 `minimumDistanceThreshold``maximumDistanceThreshold`|
| `zone` | 字串 | 多邊形的「名稱」欄位，代表在人員之間 distancing 監視的區域|
| `trigger` | 字串| 觸發程式類型為「事件」或「間隔」，視 SPACEANALYTICS_CONFIG 中的值而定。 `trigger`|

| 偵測功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 偵測識別碼|
| `type` | 字串| 偵測類型|
| `region` | collection| 值的集合|
| `type` | 字串| 區欄位型別|
| `points` | collection| 當區欄位型別是矩形時，左上角和右下角的點 |
| `confidence` | FLOAT| 演算法信賴度|
| `centerGroundPoint` | 2個 float 值| `x`， `y` 具有人員在地面的推斷位置座標（以英尺為單位）的值。 `x` 與相機之間的距離，與投影在地面的鏡頭影像平面垂直距離。 `y` 與相機之間的距離，與以英尺為地面投影的影像平面平行。|

| SourceInfo 功能變數名稱 | 類型| Description|
|---------|---------|---------|
| `id` | 字串| 相機識別碼|
| `timestamp` | date| 發出 JSON 承載時的 UTC 日期|
| `width` | int | 影片框架寬度|
| `height` | int | 影片框架高度|
| `frameId` | int | 畫面格識別碼|
| `cameraCallibrationInfo` | collection | 值的集合|
| `status` | 字串 | 指出是否將攝影機校正至地面平面為「完成」|
| `cameraHeight` | FLOAT | 攝影機的高度，以英尺為基礎。 這是從自動校正推斷而來。 |
| `focalLength` | FLOAT | 攝影機的焦距長度（以圖元為單位）。 這是從自動校正推斷而來。 |
| `tiltUpAngle` | FLOAT | 攝影機從垂直傾斜角度。 這是從自動校正推斷而來。|


## <a name="use-the-output-generated-by-the-container"></a>使用容器所產生的輸出

您可能會想要將空間分析偵測或事件整合到您的應用程式中。 以下是一些要考慮的方法： 

* 針對您所選擇的程式設計語言使用 Azure 事件中樞 SDK，以連接到 Azure IoT 中樞端點並接收事件。 如需詳細資訊，請參閱 [從內建端點讀取裝置到雲端訊息](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) 。 
* 在您的 Azure IoT 中樞上設定 **訊息路由** ，以將事件傳送至其他端點，或將事件儲存到您的資料儲存體。 如需詳細資訊，請參閱 [IoT 中樞訊息路由](../../iot-hub/iot-hub-devguide-messages-d2c.md) 。 
* 設定 Azure 串流分析作業，以在事件抵達時即時處理這些事件，並建立視覺效果。 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>大規模部署空間分析作業 (多個相機) 

為了取得 Gpu 的最佳效能和使用方式，您可以使用圖形實例，在多個相機上部署任何空間分析作業。 以下是 `cognitiveservices.vision.spatialanalysis-personcount` 在五個攝影機上執行操作的範例。

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| 名稱 | 類型| Description|
|---------|---------|---------|
| `batch_size` | int | 指出將在作業中使用的相機數目。 |

## <a name="next-steps"></a>下一步

* [部署人員計數 web 應用程式](spatial-analysis-web-app.md)
* [記錄和疑難排解](spatial-analysis-logging.md)
* [攝影機放置指南](spatial-analysis-camera-placement.md)
* [區域和行放置指南](spatial-analysis-zone-line-placement.md)