---
ms.openlocfilehash: eff73888a449de20b2b460d519b36c0f03c4ea77
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88690927"
---
當您執行媒體圖表時，來自動作偵測器處理器節點的結果會透過 IoT 中樞接收節點傳遞至 IoT 中樞。 您在 Visual Studio Code 的 [輸出] 視窗中看到的訊息包含 `body` 區段和 `applicationProperties` 區段。 如需詳細資訊，請參閱[建立及讀取 IoT 中樞訊息](../../../../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下列訊息中，Live Video Analytics 模組會定義應用程式屬性和主體內容。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

具現化媒體圖表時，RTSP 來源節點會嘗試連線到在 rtspsim-live555 容器中執行的 RTSP 伺服器。 如果連線成功，則會列印下列事件。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-04-09T16:42:18.1280000Z"  
    }  
}
```

在上述輸出中： 

* 此訊息為診斷事件 `MediaSessionEstablished`。 這表示 RTSP 來源節點 (主體) 已與 RTSP 模擬器連線，並開始接收 (模擬的) 即時摘要。
* 在 `applicationProperties` 中，`subject` 會參考訊息產生來源圖表拓撲中的節點。 在此案例中，訊息來自 RTSP 來源節點。
* 在 `applicationProperties` 中，`eventType` 表示此事件是診斷事件。
* `eventTime` 值表示事件的發生時間。
* `body` 區段包含診斷事件的相關資料。 在此情況下，資料包含[工作階段描述通訊協定 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 詳細資料。

### <a name="motiondetection-event"></a>MotionDetection 事件

當偵測到動作時，IoT Edge 模組上的 Live Video Analytics 會傳送推斷事件。 `type` 會設定為 `motion` 以表示這是來自動作偵測處理器的結果。 `eventTime` 值會告訴您動作的發生時間 (UTC)。 

這個訊息的範例如下：

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

在此範例中： 

* 在 `applicationProperties` 中，`subject` 會參考訊息產生來源媒體圖表中的節點。 在此情況下，訊息是來自動作偵測處理器節點。
* 在 `applicationProperties` 中，`eventType` 表示此事件是分析事件。
* `eventTime` 值是事件的發生時間。
* `body` 值包含分析事件的相關資料。 在此情況下，事件是推斷事件，因此主體包含 `timestamp` 和 `inferences` 資料。
* `inferences` 資料指出 `type` 是 `motion`。 其具有關於該 `motion` 事件的其他資料。
* `box` 區段包含移動物件外圍週框方塊的座標。 這些值會依影片的寬度和高度 (以像素為單位) 進行一般化。 例如，寬度為 1920，而高度為 1080。

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
