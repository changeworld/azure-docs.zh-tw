---
title: Azure 媒體服務事件的 Azure 事件格線結構描述
description: 描述利用 Azure 事件格線提供給媒體服務事件的屬性
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: d4a206bbddedfe9f23a943df27c6ac4b5fe17e8a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665744"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure 媒體服務事件的 Azure 事件格線結構描述

本文提供媒體服務事件的結構描述和屬性。

如需範例指令碼和教學課程的清單，請參閱[媒體服務事件來源](../../event-grid/event-sources.md#azure-subscriptions)。

## <a name="job-related-event-types"></a>作業相關的事件類型

媒體服務會發出如下所述的 [作業] 相關事件類型。 [作業] 相關事件有兩種類別：「監視工作狀態變更」和「監視作業輸出的狀態變更」。 

您可以訂閱 JobStateChange 事件來註冊所有事件。 或者，您可以只訂閱特定事件 (例如，JobErrored、JobFinished 和 JobCanceled 等最終狀態)。   

### <a name="monitoring-job-state-changes"></a>監視作業狀態變更

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| 取得所有作業狀態變更的事件。 |
| Microsoft.Media.JobScheduled| 取得當作業轉換成已排程狀態時的事件。 |
| Microsoft.Media.JobProcessing| 取得當作業轉換成處理狀態時的事件。 |
| Microsoft.Media.JobCanceling| 取得當作業轉換成取消狀態時的事件。 |
| Microsoft.Media.JobFinished| 取得當作業轉換成完成狀態時的事件。 這是包含作業輸出的最終狀態。|
| Microsoft.Media.JobCanceled| 取得當作業轉換成已取消狀態時的事件。 這是包含作業輸出的最終狀態。|
| Microsoft.Media.JobErrored| 取得當作業轉換成錯誤狀態時的事件。 這是包含作業輸出的最終狀態。|

請參閱接下來將說明的[結構描述範例](#event-schema-examples)。

### <a name="monitoring-job-output-state-changes"></a>監視作業輸出狀態變更

作業可以包含多個作業輸出（如果您將轉換設定為具有多個作業輸出）。如果您想要追蹤個別作業輸出的詳細資料，請接聽作業輸出變更事件。

每項**作業**的層級會高於**JobOutput**，因此作業輸出事件會在對應的作業內引發。 

`JobFinished`、`JobCanceled``JobError` 中的錯誤訊息會輸出每個作業輸出的匯總結果–全部都完成。 不過，作業輸出事件會在每個工作完成時引發。 例如，如果您有編碼輸出，接著是影片分析輸出，您會在最後一個 JobFinished 事件引發後，以匯總資料的形式引發兩個事件，做為作業輸出事件。

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| 取得所有作業輸出狀態變更的事件。 |
| Microsoft.Media.JobOutputScheduled| 取得當作業輸出轉換成已排程狀態時的事件。 |
| Microsoft.Media.JobOutputProcessing| 取得當作業輸出轉換成處理狀態時的事件。 |
| Microsoft.Media.JobOutputCanceling| 取得當作業輸出轉換成取消狀態時的事件。|
| Microsoft.Media.JobOutputFinished| 取得當作業輸出轉換成完成狀態時的事件。|
| Microsoft.Media.JobOutputCanceled| 取得當作業輸出轉換成已取消態時的事件。|
| Microsoft.Media.JobOutputErrored| 取得當作業輸出轉換成錯誤狀態時的事件。|

請參閱接下來將說明的[結構描述範例](#event-schema-examples)。

### <a name="monitoring-job-output-progress"></a>監視作業輸出進度

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| 此事件反映作業處理進度 (從 0% 到 100%)。 若進度值增加超過 5%，或自上一個事件起已超過 30 秒 (活動訊號)，則服務會嘗試傳送事件。 進度值不一定會從 0% 開始、不一定會達到 100%，也不會隨著時間的經過而以固定速率增加。 您不應該使用此事件來判斷處理是否已完成，您應該改為使用狀態變更事件。|

請參閱接下來將說明的[結構描述範例](#event-schema-examples)。

## <a name="live-event-types"></a>即時事件類型

媒體服務也會發出如下所述的 [即時] 事件類型。 **即時**事件有兩種類別：資料流層級事件和資料軌層級事件。 

### <a name="stream-level-events"></a>串流層級事件

資料流層級事件會以每個資料流或連線為基礎來引發。 每個事件都有 `StreamId` 參數可識別連線或資料流。 每個資料流或連線都有一或多個不同類型的資料軌。 例如，來自編碼器的一個連線可能會有一個音訊資料軌和四個視訊資料軌。 資料流事件類型有：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | 編碼器的連線嘗試遭到拒絕。 |
| Microsoft.Media.LiveEventEncoderConnected | 編碼器對即時事件建立連線。 |
| Microsoft.Media.LiveEventEncoderDisconnected | 編碼器中斷連線。 |

請參閱接下來將說明的[結構描述範例](#event-schema-examples)。

### <a name="track-level-events"></a>追蹤層級事件

資料軌層級事件會以每個資料軌為基礎來引發。 

> [!NOTE]
> 在即時編碼器連線之後，會引發所有追蹤層級事件。

追蹤層級的事件種類如下：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | 媒體伺服器卸除資料區塊，原因是其來得太晚或有重疊的時間戳記 (新資料區塊的時間戳記小於先前資料區塊的結束時間)。 |
| Microsoft.Media.LiveEventIncomingStreamReceived | 媒體伺服器收到資料流或連線中每個資料軌的第一個資料區塊。 |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | 媒體伺服器偵測到音訊和影片串流不同步。請使用做為警告，因為使用者體驗可能不會受到影響。 |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | 媒體伺服器偵測到來自外部編碼器的兩個影片資料流程中，有任何一個都不同步。請使用做為警告，因為使用者體驗可能不會受到影響。 |
| Microsoft.Media.LiveEventIngestHeartbeat | 當即時事件執行時，會針對每個資料軌每 20 秒發佈一次。 提供內嵌健康情況摘要。<br/><br/>第一次連線編碼器之後，無論編碼器是否仍在連線中，每隔20秒就會繼續發出訊號事件。 |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | 媒體伺服器偵測到內送的資料軌發生中斷。 |

請參閱接下來將說明的[結構描述範例](#event-schema-examples)。

## <a name="event-schema-examples"></a>事件結構描述範例

### <a name="jobstatechange"></a>JobStateChange

下列範例顯示 **JobStateChange** 事件的結構描述： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| previousState | string | 工作在該事件之前的狀態。 |
| state | string | 在此事件中，被通知的工作新狀態。 例如，「已排程：作業已準備好開始」或「已完成：作業已完成」。|

工作狀態可以是以下其中一個值：*已排入佇列*、*已排程*、*處理中*、*已完成*、*錯誤*、*已取消*、*取消中*

> [!NOTE]
> 「已排入佇列」 只會存在於 **previousState** 屬性中，但是不會在 **state** 屬性中。

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled、JobProcessing、JobCanceling

對於每個非最終作業狀態的變更 (例如 JobScheduled、JobProcessing、JobCanceling)，範例結構描述看起來如下所示：

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished、JobCanceled、JobErrored

對於每個最終作業狀態的變更 (例如 JobFinished、JobCanceled、JobErrored)，範例結構描述看起來如下所示：

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 輸出 | Array | 取得作業輸出。|

### <a name="joboutputstatechange"></a>JobOutputStateChange

下列範例顯示 **JobOutputStateChange** 事件的結構描述：

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled、JobOutputProcessing、JobOutputFinished、JobOutputCanceling、JobOutputCanceled、JobOutputErrored

對於每個 JobOutput 狀態變更，範例結構描述看起來如下所示：

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

範例結構描述看起來像下面這樣：

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

下列範例顯示 **LiveEventConnectionRejected** 事件的結構描述： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| streamId | string | 資料流或連線的識別碼。 編碼器或客戶要負責在內嵌 URL 中新增此識別碼。 |  
| ingestUrl | string | 即時事件所提供的內嵌 URL。 |  
| encoderIp | string | 編碼器的 IP。 |
| encoderPort | string | 此資料流來源編碼器的連接埠。 |
| ResultCode | string | 連線遭到拒絕的原因。 結果碼列於下表。 |

您可以在[即時事件錯誤碼](live-event-error-codes.md)中找到錯誤的結果碼。

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

下列範例顯示 **LiveEventEncoderConnected** 事件的結構描述： 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| streamId | string | 資料流或連線的識別碼。 編碼器或客戶要負責在內嵌 URL 中提供此識別碼。 |
| ingestUrl | string | 即時事件所提供的內嵌 URL。 |
| encoderIp | string | 編碼器的 IP。 |
| encoderPort | string | 此資料流來源編碼器的連接埠。 |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

下列範例顯示 **LiveEventEncoderDisconnected** 事件的結構描述： 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| streamId | string | 資料流或連線的識別碼。 編碼器或客戶要負責在內嵌 URL 中新增此識別碼。 |  
| ingestUrl | string | 即時事件所提供的內嵌 URL。 |  
| encoderIp | string | 編碼器的 IP。 |
| encoderPort | string | 此資料流來源編碼器的連接埠。 |
| ResultCode | string | 編碼器中斷連線的原因。 可能是正常中斷連線或錯誤所致。 結果碼列於下表。 |

您可以在[即時事件錯誤碼](live-event-error-codes.md)中找到錯誤的結果碼。

正常中斷連線的結果碼如下：

| 結果碼 | 描述 |
| ----------- | ----------- |
| S_OK | 編碼器已成功中斷連線。 |
| MPE_CLIENT_TERMINATED_SESSION | 編碼器已中斷連線 (RTMP)。 |
| MPE_CLIENT_DISCONNECTED | 編碼器已中斷連線 (FMP4)。 |
| MPI_REST_API_CHANNEL_RESET | 收到通道重設命令。 |
| MPI_REST_API_CHANNEL_STOP | 收到通道停止命令。 |
| MPI_REST_API_CHANNEL_STOP | 通道正在進行維護。 |
| MPI_STREAM_HIT_EOF | 編碼器傳送了 EOF 資料流。 |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

下列範例顯示 **LiveEventIncomingDataChunkDropped** 事件的結構描述： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| trackType | string | 資料軌的類型 (音訊/視訊)。 |
| trackName | string | 資料軌的名稱。 |
| bitrate | integer | 資料軌的位元速率。 |
| timestamp | string | 資料區塊的卸除時間戳記。 |
| timescale | string | 時間戳記的時幅。 |
| ResultCode | string | 資料區塊卸除的原因。 **FragmentDrop_OverlapTimestamp** 或 **FragmentDrop_NonIncreasingTimestamp**。 |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

下列範例顯示 **LiveEventIncomingStreamReceived** 事件的結構描述： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| trackType | string | 資料軌的類型 (音訊/視訊)。 |
| trackName | string | 資料軌的名稱 (由編碼器提供，在 RTMP 的案例中，伺服器會以 TrackType_Bitrate 格式產生)。 |
| bitrate | integer | 資料軌的位元速率。 |
| ingestUrl | string | 即時事件所提供的內嵌 URL。 |
| encoderIp | string  | 編碼器的 IP。 |
| encoderPort | string | 此資料流來源編碼器的連接埠。 |
| timestamp | string | 所收到資料區塊的第一個時間戳記。 |
| timescale | string | 用來表示時間戳記的時幅。 |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

下列範例顯示 **LiveEventIncomingStreamsOutOfSync** 事件的結構描述： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| minLastTimestamp | string | 所有資料軌 (音訊或視訊) 最後一個時間戳記之間的最小值。 |
| typeOfTrackWithMinLastTimestamp | string | 最後一個時間戳記為最小值的資料軌 (音訊或視訊) 類型。 |
| maxLastTimestamp | string | 所有資料軌 (音訊或視訊) 的所有時間戳記之間的最大值。 |
| typeOfTrackWithMaxLastTimestamp | string | 最後一個時間戳記為最大值的資料軌 (音訊或視訊) 類型。 |
| timescaleOfMinLastTimestamp| string | 取得用來表示 "MinLastTimestamp" 的時幅。|
| timescaleOfMaxLastTimestamp| string | 取得用來表示 "MaxLastTimestamp" 的時幅。|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

下列範例顯示 **LiveEventIncomingVideoStreamsOutOfSync** 事件的結構描述： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| firstTimestamp | string | 其中一個視訊類型資料軌/品質層級所收到的時間戳記。 |
| firstDuration | string | 具有第一個時間戳記的資料區塊持續時間。 |
| secondTimestamp | string  | 其他某些視訊類型資料軌/品質層級所收到的時間戳記。 |
| secondDuration | string | 具有第二個時間戳記的資料區塊持續時間。 |
| timescale | string | 時間戳記和持續時間的時幅。|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

下列範例顯示 **LiveEventIngestHeartbeat** 事件的結構描述： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| trackType | string | 資料軌的類型 (音訊/視訊)。 |
| trackName | string | 資料軌的名稱 (由編碼器提供，在 RTMP 的案例中，伺服器會以 TrackType_Bitrate 格式產生)。 |
| bitrate | integer | 資料軌的位元速率。 |
| incomingBitrate | integer | 根據來自編碼器的資料區塊所計算出的位元速率。 |
| lastTimestamp | string | 資料軌在過去 20 秒所收到的最新時間戳記。 |
| timescale | string | 用來表示時間戳記的時幅。 |
| overlapCount | integer | 在過去 20 秒有重疊時間戳記的資料區塊數目。 |
| discontinuityCount | integer | 在過去 20 秒所觀察到的中斷次數。 |
| nonIncreasingCount | integer | 在過去 20 秒所收到有過去時間戳記的資料區塊數目。 |
| unexpectedBitrate | bool | 在過去 20 秒內，預期和實際的位元速率差異是否超過允許的限制。 只有在「incomingBitrate >= 2* 位元速率」或「incomingBitrate <= 位元速率/2」或「IncomingBitrate = 0」時，才會是 true。 |
| state | string | 即時事件的狀態。 |
| healthy | bool | 根據計數和旗標來指出內嵌是否狀況良好。 如果 overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false，則健康情況為 true。 |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

下列範例顯示 **LiveEventTrackDiscontinuityDetected** 事件的結構描述： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| trackType | string | 資料軌的類型 (音訊/視訊)。 |
| trackName | string | 資料軌的名稱 (由編碼器提供，在 RTMP 的案例中，伺服器會以 TrackType_Bitrate 格式產生)。 |
| bitrate | integer | 資料軌的位元速率。 |
| previousTimestamp | string | 上一個片段的時間戳記。 |
| newTimestamp | string | 當前片段的時間戳記。 |
| discontinuityGap | string | 上述兩個時間戳記之間的間距。 |
| timescale | string | 用來表示時間戳記和中斷間距的時幅。 |

### <a name="common-event-properties"></a>常見的事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 主題 | string | EventGrid 主題。 此屬性有媒體服務帳戶的資源識別碼。 |
| subject | string | 媒體服務帳戶底下媒體服務通道的資源路徑。 串連主題和主旨即可獲得作業的資源識別碼。 |
| eventType | string | 此事件來源已註冊的事件類型之一。 例如，"Microsoft.Media.JobStateChange"。 |
| eventTime | string | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | string | 事件的唯一識別碼。 |
| data | object | 媒體服務事件資料。 |
| dataVersion | string | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | string | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

## <a name="next-steps"></a>後續步驟

[登記工作狀態變更事件](job-state-events-cli-how-to.md)

## <a name="see-also"></a>另請參閱

- [包含媒體服務事件的 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [媒體服務事件的定義](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [實況活動錯誤碼](live-event-error-codes.md)
