---
title: gRPC 擴充通訊協定 - Azure
description: 在本文中，您將了解如何使用 gRPC 擴充通訊協定在即時影片分析模組與 AI 或 CV 自訂擴充之間傳送訊息。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 288dcd1a11c7c42d8796d3b17f2bfd56f562aaf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89448051"
---
# <a name="grpc-extension-protocol"></a>gRPC 擴充通訊協定

在本文中，您將了解如何使用 gRPC 擴充通訊協定在即時影片分析模組與 AI 或 CV 自訂擴充之間傳送訊息。

gRPC 是在任何環境中執行的現代化、開放原始碼、高效能 RPC 架構。 gRPC 傳輸服務會在下列兩者之間使用 HTTP/2 雙向串流：

* gRPC 用戶端 (IoT Edge 模組上的即時影片分析) 和 
* gRPC 伺服器 (您的自訂擴充)。

gRPC 工作階段是透過 TCP/TLS 連接埠從 gRPC 用戶端到 gRPC 伺服器的單一連線。 

在單一工作階段中：用戶端會透過 gRPC 串流工作階段，將媒體串流描述項 (後面接著影片畫面) 傳送到伺服器作為 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 訊息。 伺服器會驗證串流描述項、分析影片畫面，並以 protobuf 訊息的形式傳回推斷結果。

![gRPC 擴充合約](./media/grpc-extension-protocol/grpc.png)

## <a name="implementing-grpc-protocol"></a>實作 gRPC 通訊協定

### <a name="creating-a-grpc-connection"></a>建立 gRPC 連線

自訂擴充必須實作下列 gRPC 服務：

```
service MediaGraphExtension {
  rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
}
```

呼叫時，這會開啟雙向串流，讓訊息在 gRPC 擴充和即時影片分析圖表之間流動。 每一方在此串流中傳送的第一個訊息會包含 MediaStreamDescriptor，以定義將在隨後的 MediaSamples 中傳送的資訊。

例如，圖表擴充可能會傳送訊息 (在此處以 JSON 表示)，以指出其會將內嵌在 gRPC 訊息中的 416x416 rgb24 編碼的畫面傳送至自訂擴充。

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": {
        "graph_identifier": {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": {
            "timescale": 90000,
            "video_frame_sample_format": {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

自訂擴充會在回應中傳送下列訊息，指出其只會傳回推斷。

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": {
        "extension_identifier": "customExtensionName"    }
}
```

既然雙方已交換媒體描述項，即時影片分析就會開始將畫面傳輸至擴充。

### <a name="sequence-numbers"></a>序號

gRPC 擴充節點和自訂擴充都會維護一組個別的序號，並指派給其訊息。 這些序號應從 1 開始單純遞增。 如果未認可任何訊息 (可能會在傳送第一個訊息時發生)，則可以忽略 `ack_sequence_number`。

一旦對應的訊息已完全處理過，就必須認可要求。 在共用記憶體傳輸的情況下，此認可會指出傳送者可能會釋出共用記憶體，而接收者不會再進行存取。 傳送者必須保留共用記憶體，直到其獲得認可為止。

### <a name="reading-embedded-content"></a>讀取內嵌內容

透過 `content_byte` 欄位，可以直接從 `MediaSample` 訊息讀取出內嵌內容。 資料將會根據 `MediaDescriptor` 進行編碼。

### <a name="reading-content-from-shared-memory"></a>從共用記憶體讀取內容

透過共用記憶體傳輸內容時，傳送者會在第一次建立工作階段時，將 `SharedMemoryBufferTransferProperties` 包含在其 `MediaStreamDescriptor` 中。 其樣貌可能如下 (以 JSON 表示)：

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

然後接收者會開啟 `/dev/shm/inference_client_share_memory_2146989006636459346` 檔案。 傳送者會傳送 `MediaSample` 訊息，其中包含參考此檔案中特定位置的 `ContentReference`。

```
{
    "timestamp": 143598615750000,
    "content_reference": {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

然後接收者會從檔案中的這個位置讀取資料。

若要讓即時影片分析容器透過共用記憶體進行通訊，就必須正確設定容器的 IPC 模式。 這可以透過許多方式來完成，但以下是一些建議的設定。

* 與在主機裝置上執行的 gRPC 推斷引擎通訊時，IPC 模式應設定為 [主機]。
* 與在另一個 IoT Edge 模組中執行的 gRPC 伺服器通訊時，應將即時影片分析模組的 IPC 模式設定為可共用，以及將自訂擴充的 IPC 模式設定為 `container:liveVideoAnalytics`，其中 `liveVideoAnalytics` 是即時影片分析模組的名稱。

以下是使用上述第一個選項時，在裝置對應項中的可能樣貌。

```
"liveVideoAnalytics": {
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": {
        "IpcMode": "host"
      }
    }
  }
}
```

如需 IPC 模式的詳細資訊，請參閱 https://docs.docker.com/engine/reference/run/#ipc-settings---ipc 。

## <a name="media-graph-grpc-extension-contract-definitions"></a>媒體圖表 gRPC 擴充合約定義

本節會定義 gRPC 合約以定義資料流程。

### <a name="protocol-messages"></a>通訊協定訊息

![通訊協定訊息](./media/grpc-extension-protocol/grpc2.png)
 
### <a name="client-authentication"></a>用戶端驗證

自訂擴充的實作者可以驗證內送 gRPC 連線的真確性，以確保其來自 gRPC 擴充節點。 此節點會在要求標頭中提供可作為驗證對象的項目。

使用者名稱/密碼認證可用來完成此動作。 建立 gRPC 擴充節點時，會提供如下所示的認證：

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

傳送 gRPC 要求時，下列標頭會包含在要求中繼資料內，以模擬 HTTP 基本驗證。

`x-ms-authentication: Basic (Base64 Encoded username:password)`

## <a name="using-grpc-over-tls"></a>透過 TLS 使用 gRPC

用於推斷的 gRPC 連線可透過 TLS 來加以保護。 當即時影片分析和推斷引擎之間的網路安全性無法獲得保證時，這種方式會很有用。 TLS 會將內嵌到 gRPC 訊息的任何內容加密，而在以較高的速率傳輸畫面時，造成額外的 CPU 負荷。

gRPC 不支援 IgnoreHostname 和 IgnoreSignature 驗證選項，因此推斷引擎所出示的伺服器憑證必須包含與 gRPC 擴充節點端點 URL 中的 IP 位址/主機名稱完全相符的 CN。

## <a name="next-steps"></a>後續步驟

[了解推斷中繼資料結構描述](inference-metadata-schema.md)
