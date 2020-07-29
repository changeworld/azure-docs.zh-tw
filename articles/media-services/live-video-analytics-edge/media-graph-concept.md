---
title: Media graph 概念-Azure
description: Media graph 可讓您定義應該從何處捕獲媒體、應該如何處理，以及應該在何處傳遞結果。 本文提供 media graph 概念的詳細描述。
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 02e960e917a059afdb0d688c7429d27d8e8a48eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84300802"
---
# <a name="media-graph"></a>媒體圖表

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項

* [IoT Edge 上的 Live Video Analytics 概觀](overview.md)
* [IoT Edge 上的 Live Video Analytics 術語](terminology.md)

## <a name="overview"></a>總覽

Media graph 可讓您定義應該從何處捕獲媒體、應該如何處理，以及應該在何處傳遞結果。 您可以藉由連接元件或節點，以所需的方式來完成這項操作。 下圖提供媒體圖形的圖形表示。  

![Media graph 的圖形表示](./media/media-graph/overview.png)

IoT Edge 上的即時影片分析支援不同類型的來源、處理器和接收器。

* **來源節點**可讓您將媒體捕獲到 media graph。 在概念上，此內容中的媒體可能是音訊串流、影片串流、資料流程，或是在單一資料流程中結合了音訊、影片和/或資料的資料流程。
* **處理器節點**可以在 media graph 中處理媒體。
* **接收節點**可讓您將處理結果傳遞給媒體圖形外的服務和應用程式。

## <a name="media-graph-topologies-and-instances"></a>Media graph 拓撲和實例 

IoT Edge 上的即時影片分析可讓您透過兩個概念（「圖形拓撲」和「圖形實例」）管理媒體圖形。 圖形拓撲可讓您定義圖形的藍圖，並以參數作為值的預留位置。 拓撲會定義要在 media graph 中使用哪些節點，以及在 media graph 中如何連接它們。 例如，如果您想要記錄相機中的摘要，則您需要一個圖形，其中包含接收影片的來源節點，以及一個可寫入影片的 sink 節點。

當您建立參考拓撲的圖形實例時，會指定拓撲中的參數值。 這可讓您建立多個實例，參考相同的拓撲，但針對拓撲中指定的參數使用不同的值。 在上述範例中，您可以使用參數來代表相機的 IP 位址，以及所錄製影片的名稱。 您可以使用該拓撲建立許多圖形實例，也就是建築物中的每個攝影機都有一個實例，每個都有特定的 IP 位址和特定名稱。

## <a name="media-graph-states"></a>Media graph 狀態  

Media graph 可以是下列其中一種狀態：

* 非使用中–代表已設定 media graph 但未啟用的狀態。
* 啟用–要具現化媒體圖形的狀態（也就是在非作用中和作用中的轉換狀態）。
* [作用中] – media graph 作用中的狀態。 

    > [!NOTE]
    >  Media graph 可以在不流經資料的情況下使用（例如，輸入影片來源離線）。
* 停用–這是 media graph 從作用中轉換成非作用中的狀態。

下圖說明 media graph 狀態機器。

![Media graph 狀態機器](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>來源、處理器和接收  

IoT Edge 上的即時影片分析支援 media graph 中的下列節點類型：

### <a name="sources"></a>來源 

#### <a name="rtsp-source"></a>RTSP 來源 

RTSP 來源節點可讓您從 [RTSP] （伺服器內嵌媒體 https://tools.ietf.org/html/rfc2326 。 監視和以 IP 為基礎的攝影機會以稱為 RTSP （即時串流通訊協定）的通訊協定來傳輸其資料，這與其他類型的裝置（例如手機和攝影機）不同。 此通訊協定是用來建立和控制伺服器（相機）與用戶端之間的媒體會話。 Media graph 中的 RTSP 來源節點會作為用戶端，並可與 RTSP 伺服器建立會話。 許多裝置（例如大部分的[IP 攝影機](https://en.wikipedia.org/wiki/IP_camera)）都有內建的 RTSP 伺服器。 [ONVIF](https://www.onvif.org/)會在其[設定檔 G、S &](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf)不相容裝置的定義中，要求支援 RTSP。 RTSP 來源節點會要求您指定 RTSP URL，以及用來啟用已驗證連線的認證。

#### <a name="iot-hub-message-source"></a>IoT 中樞訊息來源 

如同其他[IoT Edge 模組](../../iot-edge/iot-edge-glossary.md#iot-edge-module)，IoT Edge 模組上的即時影片分析可以透過[IoT Edge 中樞](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)接收訊息。 這些訊息可以從其他模組、在 Edge 裝置上執行的應用程式，或從雲端傳送。 這類訊息會傳遞（路由）至模組上的[已命名輸入](../../iot-edge/module-composition.md#sink)。 IoT 中樞訊息來源] 節點可讓這類訊息到達 media graph。 這些訊息或信號可以接著在 media graph 內部使用，通常是用來啟動信號閘道（請參閱下方的[信號閘道](#signal-gate-processor)）。 

例如，您可以有一個 IoT Edge 模組，它會在門開啟時產生訊息。 來自該模組的訊息可以路由傳送至 IoT Edge 中樞，然後再將其路由傳送至 media graph 的 IoT 中樞訊息來源。 在 media graph 中，IoT 中樞訊息來源可以將事件傳遞至信號閘道處理器，然後從 RTSP 來源將影片記錄到檔案中。 

### <a name="processors"></a>處理器  

#### <a name="motion-detection-processor"></a>動作偵測處理器 

[動作偵測處理器] 節點可讓您偵測即時影片中的動作。 它會檢查傳入的影片畫面，並判斷影片中是否有移動。 如果偵測到動作，它會將影片畫面格傳遞至下游元件，併發出事件。 當偵測到動作時，可以使用 [動作偵測處理器] 節點（與其他節點搭配）來觸發傳入影片的記錄。

#### <a name="frame-rate-filter-processor"></a>畫面播放速率篩選處理器  

[畫面播放速率篩選器處理器] 節點可讓您以指定的速率從傳入的影片串流取樣畫面格。 這可讓您減少傳送到向下串流元件（例如 HTTP 擴充處理器節點）的框架數目，以進行進一步的處理。

#### <a name="http-extension-processor"></a>HTTP 擴充處理器

[HTTP 擴充處理器] 節點可讓您將自己的 IoT Edge 模組連接到 media graph。 此節點會將已解碼的影片畫面視為輸入，並將這類畫面轉送至您的模組所公開的 HTTP REST 端點。 此節點可以視需要向 REST 端點進行驗證。 此外，節點也具有內建的影像格式器，可在將影片框架轉送至 REST 端點之前進行縮放和編碼。 Scaler 具有可保留、填補或伸展影像外觀比例的選項。 影像編碼器支援 jpeg、png 或 bmp 格式。

#### <a name="signal-gate-processor"></a>信號閘道處理器  

[信號閘道處理器] 節點可讓您有條件地將媒體從某個節點轉送到另一個。 它也可做為緩衝區，以允許同步處理媒體和事件。 典型的使用案例是在 RTSP 來源節點與資產接收節點之間插入信號閘道處理器節點，並使用動作偵測器處理器節點的輸出來觸發閘道。 使用這類 media graph 時，您只會在偵測到動作時錄製影片。

### <a name="sinks"></a>接收器  

#### <a name="asset-sink"></a>資產接收  

[資產接收] 節點可讓您將媒體（影片和/或音訊）資料寫入 Azure 媒體服務資產。 Media graph 中只能有一個 [資產接收] 節點。 如需資產的詳細資訊，以及其在錄製和播放媒體時的角色，請參閱[資產](terminology.md#asset)一節。 如需有關如何使用此節點之屬性的詳細資訊，您也可以參閱[連續影片錄製](continuous-video-recording-concept.md)文章。

#### <a name="file-sink"></a>檔接收  

[檔案接收] 節點可讓您將媒體（影片和/或音訊）資料寫入 IoT Edge 裝置之本機檔案系統上的位置。 Media graph 中只能有一個 [file sink] 節點，而且它必須是 [信號閘道處理器] 節點的下游。 這會將輸出檔案的持續時間限制為 [信號閘道處理器] 節點屬性中指定的值。

#### <a name="iot-hub-message-sink"></a>IoT 中樞訊息接收  

IoT 中樞的 [訊息接收] 節點可讓您將事件發行至 IoT Edge 中樞。 然後，IoT Edge 中樞可以將資料路由至 Edge 裝置上的其他模組或應用程式，或在雲端中 IoT 中樞（針對部署資訊清單中指定的每個路由）。 IoT 中樞的訊息接收節點可以接受來自上游處理器（例如，動作偵測處理器節點）的事件，或透過 HTTP 延伸模組處理器節點從外部推斷服務。

## <a name="rules-on-the-use-of-nodes"></a>使用節點的規則

如需如何在 media graph 中使用不同節點的其他規則，請參閱[配額](quotas-limitations.md#limitations-on-graph-topologies-at-preview)。

## <a name="scenarios"></a>案例

使用上述定義的來源、處理器和接收器組合，您可以針對涉及即時影片分析的各種案例建立媒體圖形。 範例案例包括：

* [錄製連續影片](continuous-video-recording-concept.md)
* [發生事件時錄製影片](event-based-video-recording-concept.md)
* [不錄製影片但進行 Live Video Analytics](analyze-live-video-concept.md)

## <a name="next-steps"></a>後續步驟

若要瞭解如何在即時影片摘要上執行動作偵測，請參閱[快速入門：使用您自己的模型來執行即時影片分析](use-your-model-quickstart.md)。
