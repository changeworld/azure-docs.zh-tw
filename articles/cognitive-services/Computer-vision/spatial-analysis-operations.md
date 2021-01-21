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
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: b530fc320f6c29dd7a86a39c5a7019265bb6b724
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624417"
---
# <a name="spatial-analysis-operations"></a>空間分析作業

空間分析可讓您分析來自相機裝置的即時串流影片。 針對您設定的每個相機裝置，空間分析的作業會產生傳送至 Azure IoT 中樞執行個體的 JSON 訊息輸出資料流。 

空間分析容器會執行下列作業：

| 作業識別碼| 描述|
|---------|---------|
| cognitiveservices，spatialanalysis-personcount | 在相機的視圖欄位中，計算指定區域內的人員。 此區域必須由單一攝影機完全涵蓋，才能讓 PersonCount 記錄精確的總計。 <br> 發出初始 _personCountEvent_ 事件，然後在計數變更時 _personCountEvent_ 事件。  |
| cognitiveservices，spatialanalysis-personcrossingline | 追蹤某位人員如何在相機的視圖欄位中，跨越指定的行。 <br>當人員跨越該行並提供方向資訊時，發出 _personLineEvent_ 事件。 
| cognitiveservices，spatialanalysis-personcrossingpolygon | 當人員進入或離開區域時發出 _personZoneEnterExitEvent_ 事件，並提供已超出區域之編號端的方向資訊。 當人員離開區域時發出 _personZoneDwellTimeEvent_ ，並提供方向資訊，以及使用者在區域內花費的毫秒數。 |
| cognitiveservices，spatialanalysis-persondistance | 追蹤使用者何時違反距離規則。 <br> 使用每個距離違規的位置定期發出 _personDistanceEvent_ 。 |

您也可以在版本中使用上述所有作業 `.debug` ，這可讓您在處理影片框架時將其視覺化。 您必須 `xhost +` 在主機電腦上執行，才能啟用影片框架和事件的視覺效果。

| 作業識別碼| 描述|
|---------|---------|
| cognitiveservices spatialanalysis-personcount. debug | 在相機的視圖欄位中，計算指定區域內的人員。 <br> 發出初始 _personCountEvent_ 事件，然後在計數變更時 _personCountEvent_ 事件。  |
| cognitiveservices spatialanalysis-personcrossingline. debug | 追蹤某位人員如何在相機的視圖欄位中，跨越指定的行。 <br>當人員跨越該行並提供方向資訊時，發出 _personLineEvent_ 事件。 
| cognitiveservices spatialanalysis-personcrossingpolygon. debug | 當人員進入或離開區域時發出 _personZoneEnterExitEvent_ 事件，並提供已超出區域之編號端的方向資訊。 當人員離開區域時發出 _personZoneDwellTimeEvent_ ，並提供方向資訊，以及使用者在區域內花費的毫秒數。 |
| cognitiveservices spatialanalysis-persondistance. debug | 追蹤使用者何時違反距離規則。 <br> 使用每個距離違規的位置定期發出 _personDistanceEvent_ 。 |

您也可以使用 [即時影片分析](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) 作為影片 AI 模組來執行空間分析。 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| 作業識別碼| 描述|
|---------|---------|
| cognitiveservices，spatialanalysis-personcount. livevideoanalytics | 在相機的視圖欄位中，計算指定區域內的人員。 <br> 發出初始 _personCountEvent_ 事件，然後在計數變更時 _personCountEvent_ 事件。  |
| cognitiveservices，spatialanalysis-personcrossingline. livevideoanalytics | 追蹤某位人員如何在相機的視圖欄位中，跨越指定的行。 <br>當人員跨越該行並提供方向資訊時，發出 _personLineEvent_ 事件。 
| cognitiveservices，spatialanalysis-personcrossingpolygon. livevideoanalytics | 當人員進入或離開區域時發出 _personZoneEnterExitEvent_ 事件，並提供已超出區域之編號端的方向資訊。 當人員離開區域時發出 _personZoneDwellTimeEvent_ ，並提供方向資訊，以及使用者在區域內花費的毫秒數。  |
| cognitiveservices，spatialanalysis-persondistance. livevideoanalytics | 追蹤使用者何時違反距離規則。 <br> 使用每個距離違規的位置定期發出 _personDistanceEvent_ 。 |

您也可以在 (版中取得即時影片分析作業， `.debug` 例如 cognitiveservices. spatialanalysis-personcount) .，這項功能可將影片框架視覺化以進行處理。 您必須 `xhost +` 在主機電腦上執行，才能啟用影片畫面和事件的視覺效果

> [!IMPORTANT]
> 電腦視覺 AI 模型會使用人為主體周圍的周框方塊，偵測並找出影片素材和輸出中的人為存在。 AI 模型不會嘗試探索個人的身分識別或人口統計。

這些是每個空間分析作業所需的參數。

| 作業參數| 描述|
|---------|---------|
| 作業識別碼 | 上表中的運算識別碼。|
| 已啟用 | 布林值： true 或 false|
| VIDEO_URL| 攝影機裝置的 RTSP url (範例： `rtsp://username:password@url`) 。 空間分析可透過 RTSP、HTTP 或使用方式，支援 h.264 編碼的資料流程。 您可以使用 AES 加密將 Video_URL 提供為模糊的 base64 字串值，如果影片 URL 經過混淆，則 `KEY_ENV` 必須 `IV_ENV` 以環境變數的形式提供。 您可以在 [這裡](https://docs.microsoft.com/dotnet/api/system.security.cryptography.aesmanaged?view=net-5.0&preserve-view=true)找到產生金鑰和加密的範例公用程式。 |
| VIDEO_SOURCE_ID | 攝影機裝置或影片串流的易記名稱。 這會隨事件 JSON 輸出傳回。|
| VIDEO_IS_LIVE| 針對相機裝置則為 True;針對錄製的影片則為 false。|
| VIDEO_DECODE_GPU_INDEX| 要解碼影片框架的 GPU。 預設為0。 應該與 `gpu_index` 其他節點設定中的相同 `VICA_NODE_CONFIG` ，例如， `DETECTOR_NODE_CONFIG` 。|
| INPUT_VIDEO_WIDTH | 輸入影片/資料流程的框架寬度 (例如 1920) 。 它是選擇性欄位，如果提供的框架將會調整為這個維度，但仍會保留長寬比。|
| DETECTOR_NODE_CONFIG | JSON，指出要在哪個 GPU 上執行偵測器節點。 應採用下列格式： `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | 區域和行的 JSON 設定，如下所述。|
| ENABLE_FACE_MASK_CLASSIFIER | `True` 若要啟用偵測影片串流中的臉部遮罩，請 `False` 將其停用。 預設為停用。 臉部遮罩偵測需要輸入影片寬度參數為 1920 `"INPUT_VIDEO_WIDTH": 1920` 。 如果偵測到的人沒有面對相機或太遠，就不會傳回臉部遮罩屬性。 如需詳細資訊，請參閱 [攝影機放置](spatial-analysis-camera-placement.md) 指南 |

這是所有空間分析作業的 DETECTOR_NODE_CONFIG 參數範例。

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sampling_num": 80,
"calibration_quality_check_sampling_times": 5,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000,
"recalibration_score": 75
}
```

| 名稱 | 類型| 描述|
|---------|---------|---------|
| `gpu_index` | 字串| 將執行此作業的 GPU 索引。|
| `do_calibration` | 字串 | 表示已開啟校正。 `do_calibration` cognitiveservices 必須是 true， **spatialanalysis-persondistance** 才能正常運作。 預設會將 do_calibration 設定為 True。 |
| `enable_recalibration` | bool | 指出是否開啟自動 recalibration。 預設為 `true`。|
| `calibration_quality_check_frequency_seconds` | int | 每次品質檢查之間的最小秒數，以判斷是否需要 recalibration。 預設值為 `86400` (24 小時) 。 只有在使用時才會使用 `enable_recalibration=True` 。|
| `calibration_quality_check_sampling_num` | int | 依品質檢查錯誤量值，所要使用的隨機選取預存資料範例數目。 預設為 `80`。 只有在使用時才會使用 `enable_recalibration=True` 。|
| `calibration_quality_check_sampling_times` | int | 每次品質檢查的不同隨機選取資料樣本集合上，會執行錯誤測量的次數。 預設為 `5`。 只有在使用時才會使用 `enable_recalibration=True` 。|
| `calibration_quality_check_sample_collect_frequency_seconds` | int | 收集 recalibration 和品質檢查的新資料樣本之間的最小秒數。 預設值為 `300` (5 分鐘) 。 只有在使用時才會使用 `enable_recalibration=True` 。|
| `calibration_quality_check_one_round_sample_collect_num` | int | 每一回合樣本集合所要收集的新資料樣本數目下限。 預設為 `10`。 只有在使用時才會使用 `enable_recalibration=True` 。|
| `calibration_quality_check_queue_max_size` | int | 校正相機模型時要儲存的最大資料樣本數。 預設為 `1000`。 只有在使用時才會使用 `enable_recalibration=True` 。|
| `recalibration_score` | int | 開始 recalibration 的最高品質閾值。 預設為 `75`。 只有在使用時才會使用 `enable_recalibration=True` 。 校正品質的計算方式是根據與影像目標 reprojection 錯誤的反向關聯性。 如果在2D 影像框架中偵測到目標，則會將目標投射在3D 空間中，並使用現有的相機校正參數重新投射回2D 影像框架。 Reprojection 錯誤是以偵測到的目標和重新投射目標之間的平均距離來測量。|
| `enable_breakpad`| bool | 指出您是否想要啟用 breakpad，以用來產生用來進行 debug 的損毀傾印。 它 `false` 預設為。 如果您將它設定為 `true` ，您也需要加入 `"CapAdd": ["SYS_PTRACE"]` 容器的 `HostConfig` 部分 `createOptions` 。 根據預設，損毀傾印會上傳至 [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter 應用程式，如果您想要將損毀傾印上傳至您自己的 AppCenter 應用程式，您可以 `RTPT_APPCENTER_APP_SECRET` 使用應用程式的應用程式秘密覆寫環境變數。


### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Cognitiveservices 的區域設定-spatialanalysis-personcount

 這是設定區域之 SPACEANALYTICS_CONFIG 參數的 JSON 輸入範例。 您可以為這項操作設定多個區域。

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
    }]
}
```

| 名稱 | 類型| 描述|
|---------|---------|---------|
| `zones` | list| 區域清單。 |
| `name` | 字串| 此區域的易記名稱。|
| `polygon` | list| 每個值組都代表多邊形頂點的 x，y。 多邊形代表人們的追蹤或計算區域，而多邊形點是以正規化座標 (0-1) 為基礎，其中左上角是 (0.0，0.0) ，右下角為 (1.0，1.0) 。   
| `threshold` | FLOAT| 當 AI 模型的信賴度大於或等於此值時，就會輸出事件。 |
| `type` | 字串| 針對 **cognitiveservices spatialanalysis-personcount** 這應該是 `count` 。|
| `trigger` | 字串| 傳送事件的觸發程式類型。 支援的值是在 `event` 計數變更時傳送事件 `interval` ，或是定期傳送事件（不論計數是否已變更）。
| `interval` | 字串| 以秒為單位的時間，此時間會在引發事件之前匯總人員計數。 作業會繼續以固定速率分析場景，並傳回該間隔最常見的計數。 匯總間隔適用于 `event` 和 `interval` 。|
| `focus` | 字串| 用來計算事件的人員周框方塊內的點位置。 焦點的價值可以 `footprint` (人員) 的使用量， (人員的周框方塊) 的正 `bottom_center` 下方， `center` (人員的周框方塊) 的中心。|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>適用于 cognitiveservices 的行配置 spatialanalysis-personcrossingline

這是設定行之 SPACEANALYTICS_CONFIG 參數的 JSON 輸入範例。 您可以為這項作業設定多個跨線。

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| 名稱 | 類型| 描述|
|---------|---------|---------|
| `lines` | list| 行的清單。|
| `name` | 字串| 這一行的易記名稱。|
| `line` | list| 行的定義。 這是一條方向線，可讓您瞭解「輸入」與「結束」。|
| `start` | 值組| 線條起點的 x，y 座標。 Float 值代表頂點相對於左上角的位置。 若要計算絕對 x，y 值，您可以將這些值乘以框架大小。 |
| `end` | 值組| x，y 軸座標，代表線條的結束點。 Float 值代表頂點相對於左上角的位置。 若要計算絕對 x，y 值，您可以將這些值乘以框架大小。 |
| `threshold` | FLOAT| 當 AI 模型的信賴度大於或等於此值時，就會輸出事件。 預設值為 16。 這是達到最大精確度的建議值。 |
| `type` | 字串| 針對 **cognitiveservices spatialanalysis-personcrossingline** 這應該是 `linecrossing` 。|
|`trigger`|字串|傳送事件的觸發程式類型。<br>支援的值：「事件」：當有人跨越該行時引發。|
| `focus` | 字串| 用來計算事件的人員周框方塊內的點位置。 焦點的價值可以 `footprint` (人員) 的使用量， (人員的周框方塊) 的正 `bottom_center` 下方， `center` (人員的周框方塊) 的中心。 預設值為 [使用量]。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Cognitiveservices 的區域設定-spatialanalysis-personcrossingpolygon

這是設定區域之 SPACEANALYTICS_CONFIG 參數的 JSON 輸入範例。 您可以為這項操作設定多個區域。

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| 名稱 | 類型| 描述|
|---------|---------|---------|
| `zones` | list| 區域清單。 |
| `name` | 字串| 此區域的易記名稱。|
| `polygon` | list| 每個值組都代表多邊形頂點的 x，y。 多邊形代表追蹤或計算人員的區域。 Float 值代表頂點相對於左上角的位置。 若要計算絕對 x，y 值，您可以將這些值乘以框架大小。 
| `threshold` | FLOAT| 當 AI 模型的信賴度大於或等於此值時，就會輸出事件。 當類型為 zonecrossing 時，預設值為48，而當 DwellTime 時，預設值為16。 這些是建議的值，可達到最大精確度。  |
| `type` | 字串| 若為 **cognitiveservices，spatialanalysis-personcrossingpolygon** 應該是 `zonecrossing` 或 `zonedwelltime` 。|
| `trigger`|字串|傳送事件的觸發程式類型<br>支援的值：「事件」：當有人進入或離開區域時引發。|
| `focus` | 字串| 用來計算事件的人員周框方塊內的點位置。 焦點的價值可以 `footprint` (人員) 的使用量， (人員的周框方塊) 的正 `bottom_center` 下方， `center` (人員的周框方塊) 的中心。 預設值為 [使用量]。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Cognitiveservices 的區域設定-spatialanalysis-persondistance

這是 SPACEANALYTICS_CONFIG 參數的 JSON 輸入範例，其會設定 cognitiveservices 的區域。 **spatialanalysis-persondistance**。 您可以為這項操作設定多個區域。

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
    "type": "persondistance",
    "config":{
        "trigger": "event",
        "output_frequency":1,
        "minimum_distance_threshold":6.0,
        "maximum_distance_threshold":35.0,
           "threshold": 16.00,
           "focus": "footprint"
            }
    }]
   }]
}
```

| 名稱 | 類型| 描述|
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
| `focus` | 字串| 用來計算事件的人員周框方塊內的點位置。 焦點的價值可以 `footprint` (人員) 的使用量， (人員的周框方塊) 的正 `bottom_center` 下方， `center` (人員的周框方塊) 的中心。|

請參閱 [攝影機放置](spatial-analysis-camera-placement.md) 指導方針，以瞭解區域和線路設定。

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
            "status": "Calibrated",
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
            "metadata": {
            "attributes": {
                "face_Mask": 0.99
            }
        }
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
            "metadata":{
            "attributes": {
                "face_noMask": 0.99
            }
            }
    }
    ],
    "schemaVersion": "1.0"
}
```

| 事件功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 事件識別碼|
| `type` | 字串| 事件類型|
| `detectionsId` | array| 觸發這個事件之人員偵測唯一識別碼的大小1陣列|
| `properties` | collection| 值的集合|
| `trackinId` | 字串| 偵測到的人員唯一識別碼|
| `zone` | 字串 | 多邊形的「名稱」欄位，代表超過的區域|
| `trigger` | 字串| 觸發程式類型為「事件」或「間隔」，視 SPACEANALYTICS_CONFIG 中的值而定。 `trigger`|

| 偵測功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 偵測識別碼|
| `type` | 字串| 偵測類型|
| `region` | collection| 值的集合|
| `type` | 字串| 區欄位型別|
| `points` | collection| 當區欄位型別是矩形時，左上角和右下角的點 |
| `confidence` | FLOAT| 演算法信賴度|
| `face_Mask` | FLOAT | 範圍 (0-1 的屬性信賴值) 指出偵測到的人員正在佩戴臉部遮罩 |
| `face_noMask` | FLOAT | 範圍 (0-1 的屬性信賴值) 指出偵測到的人員 **未** 戴上臉部遮罩 |

| SourceInfo 功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 相機識別碼|
| `timestamp` | date| 發出 JSON 承載時的 UTC 日期|
| `width` | int | 影片框架寬度|
| `height` | int | 影片框架高度|
| `frameId` | int | 畫面格識別碼|
| `cameraCallibrationInfo` | collection | 值的集合|
| `status` | 字串 | 校正的狀態，格式為 `state[;progress description]` 。 如果啟用 recalibration，則狀態可以是 `Calibrating` `Recalibrating` () 或 `Calibrated` 。 只有當 [進度描述] 部分處於 `Calibrating` and 狀態時，才會 `Recalibrating` 使用它來顯示目前校正處理常式的進度。|
| `cameraHeight` | FLOAT | 攝影機的高度，以英尺為基礎。 這是從自動校正推斷而來。 |
| `focalLength` | FLOAT | 攝影機的焦距長度（以圖元為單位）。 這是從自動校正推斷而來。 |
| `tiltUpAngle` | FLOAT | 攝影機從垂直傾斜角度。 這是從自動校正推斷而來。|

| SourceInfo 功能變數名稱 | 類型| 描述|
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
            "metadata": {
            "attributes": {
                "face_Mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| 事件功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 事件識別碼|
| `type` | 字串| 事件類型|
| `detectionsId` | array| 觸發這個事件之人員偵測唯一識別碼的大小1陣列|
| `properties` | collection| 值的集合|
| `trackinId` | 字串| 偵測到的人員唯一識別碼|
| `status` | 字串| 直線交點的方向，也就是 ' CrossLeft ' 或 ' CrossRight '|
| `zone` | 字串 | 超過的行的 [名稱] 欄位|

| 偵測功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 偵測識別碼|
| `type` | 字串| 偵測類型|
| `region` | collection| 值的集合|
| `type` | 字串| 區欄位型別|
| `points` | collection| 當區欄位型別是矩形時，左上角和右下角的點 |
| `confidence` | FLOAT| 演算法信賴度|
| `face_Mask` | FLOAT | 範圍 (0-1 的屬性信賴值) 指出偵測到的人員正在佩戴臉部遮罩 |
| `face_noMask` | FLOAT | 範圍 (0-1 的屬性信賴值) 指出偵測到的人員 **未** 戴上臉部遮罩 |

| SourceInfo 功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 相機識別碼|
| `timestamp` | date| 發出 JSON 承載時的 UTC 日期|
| `width` | int | 影片框架寬度|
| `height` | int | 影片框架高度|
| `frameId` | int | 畫面格識別碼|


> [!IMPORTANT]
> AI 模型會偵測某位人員，不論該人員是否朝向相機。 AI 模型不會執行臉部辨識，也不會發出任何生物特徵辨識資訊。 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>適用于 cognitiveservices 的 JSON 格式-spatialanalysis-personcrossingpolygon AI 見解

這項作業使用類型 SPACEANALYTICS_CONFIG 進行偵測輸出的 JSON 範例 `zonecrossing` 。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
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
        "metadata": {
        "attributes": {
        "face_Mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

這項作業使用類型 SPACEANALYTICS_CONFIG 進行偵測輸出的 JSON 範例 `zonedwelltime` 。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
        "durationMs": 7132.0
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

| 事件功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 事件識別碼|
| `type` | 字串| 事件類型。 值可以是 _personZoneDwellTimeEvent_ 或 _personZoneEnterExitEvent_|
| `detectionsId` | array| 觸發這個事件之人員偵測唯一識別碼的大小1陣列|
| `properties` | collection| 值的集合|
| `trackinId` | 字串| 偵測到的人員唯一識別碼|
| `status` | 字串| 多邊形交叉的方向，也就是「Enter」或「Exit」|
| `side` | int| 該人員所交叉多邊形的邊數。 每一端都是多邊形的兩個頂點之間的編號邊緣，代表您的區域。 多邊形前兩個頂點之間的邊緣代表第一端|
| `durationMs` | FLOAT | 代表人員在區域中所花費時間的毫秒數。 當事件種類為 _personZoneDwellTimeEvent_ 時，就會提供此欄位。|
| `zone` | 字串 | 多邊形的「名稱」欄位，代表超過的區域|

| 偵測功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 偵測識別碼|
| `type` | 字串| 偵測類型|
| `region` | collection| 值的集合|
| `type` | 字串| 區欄位型別|
| `points` | collection| 當區欄位型別是矩形時，左上角和右下角的點 |
| `confidence` | FLOAT| 演算法信賴度|
| `face_Mask` | FLOAT | 範圍 (0-1 的屬性信賴值) 指出偵測到的人員正在佩戴臉部遮罩 |
| `face_noMask` | FLOAT | 範圍 (0-1 的屬性信賴值) 指出偵測到的人員 **未** 戴上臉部遮罩 |

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
            "status": "Calibrated",
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

| 事件功能變數名稱 | 類型| 描述|
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

| 偵測功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 偵測識別碼|
| `type` | 字串| 偵測類型|
| `region` | collection| 值的集合|
| `type` | 字串| 區欄位型別|
| `points` | collection| 當區欄位型別是矩形時，左上角和右下角的點 |
| `confidence` | FLOAT| 演算法信賴度|
| `centerGroundPoint` | 2個 float 值| `x`， `y` 具有人員在地面的推斷位置座標（以英尺為單位）的值。 `x` 和 `y` 是 floor 平面上的座標（假設樓層是層級）。 相機的位置是原點。 |

在計算時 `centerGroundPoint` ， `x` 是與相機之間的距離，沿著與相機影像平面垂直的線。 `y` 這是與相機之間的距離，以及與相機影像平面平行的直線。 

![範例中心基礎點](./media/spatial-analysis/x-y-chart.png) 

在此範例中，`centerGroundPoint` 是 `{x: 4, y: 5}`。 這表示有一個人從相機移到4英尺，右邊有5英尺，查看空間由上而下。


| SourceInfo 功能變數名稱 | 類型| 描述|
|---------|---------|---------|
| `id` | 字串| 相機識別碼|
| `timestamp` | date| 發出 JSON 承載時的 UTC 日期|
| `width` | int | 影片框架寬度|
| `height` | int | 影片框架高度|
| `frameId` | int | 畫面格識別碼|
| `cameraCallibrationInfo` | collection | 值的集合|
| `status` | 字串 | 校正的狀態，格式為 `state[;progress description]` 。 如果啟用 recalibration，則狀態可以是 `Calibrating` `Recalibrating` () 或 `Calibrated` 。 只有當 [進度描述] 部分處於 `Calibrating` and 狀態時，才會 `Recalibrating` 使用它來顯示目前校正處理常式的進度。|
| `cameraHeight` | FLOAT | 攝影機的高度，以英尺為基礎。 這是從自動校正推斷而來。 |
| `focalLength` | FLOAT | 攝影機的焦距長度（以圖元為單位）。 這是從自動校正推斷而來。 |
| `tiltUpAngle` | FLOAT | 攝影機從垂直傾斜角度。 這是從自動校正推斷而來。|


## <a name="use-the-output-generated-by-the-container"></a>使用容器所產生的輸出

您可能會想要將空間分析偵測或事件整合到您的應用程式中。 以下是一些要考慮的方法： 

* 針對您所選擇的程式設計語言使用 Azure 事件中樞 SDK，以連接到 Azure IoT 中樞端點並接收事件。 如需詳細資訊，請參閱 [從內建端點讀取裝置到雲端訊息](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) 。 
* 在您的 Azure IoT 中樞上設定 **訊息路由** ，以將事件傳送至其他端點，或將事件儲存到您的資料儲存體。 如需詳細資訊，請參閱 [IoT 中樞訊息路由](../../iot-hub/iot-hub-devguide-messages-d2c.md) 。 
* 設定 Azure 串流分析作業，以在事件抵達時即時處理這些事件，並建立視覺效果。 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>大規模部署空間分析作業 (多個相機) 

為了取得 Gpu 的最佳效能和使用方式，您可以使用圖形實例，在多個相機上部署任何空間分析作業。 以下是 `cognitiveservices.vision.spatialanalysis-personcrossingline` 在十五張攝影機上執行作業的範例。

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
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
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| 名稱 | 類型| 描述|
|---------|---------|---------|
| `batch_size` | int | 指出將在作業中使用的相機數目。 |

## <a name="next-steps"></a>後續步驟

* [部署人員計數 web 應用程式](spatial-analysis-web-app.md)
* [記錄和疑難排解](spatial-analysis-logging.md)
* [攝影機放置指南](spatial-analysis-camera-placement.md)
* [區域和行放置指南](spatial-analysis-zone-line-placement.md)
