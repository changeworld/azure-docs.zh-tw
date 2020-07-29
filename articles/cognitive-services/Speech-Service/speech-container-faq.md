---
title: 語音服務容器的常見問題（FAQ）
titleSuffix: Azure Cognitive Services
description: 安裝並執行語音容器。 語音轉換文字可將會即時串流至文字，讓您的應用程式、工具或裝置可以取用或顯示。 文字轉語音會將輸入文字轉換為仿真人的合成語音。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: aahi
ms.openlocfilehash: e6b90e17c96f7636fa509e31354f9413b312803f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289029"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>語音服務容器的常見問題（FAQ）

搭配容器使用語音服務時，請先依賴這一系列的常見問題，再升級為支援。 這篇文章會從一般到技術的角度，捕捉各種問題。 若要展開答案，請按一下問題。

## <a name="general-questions"></a>一般問題

<details>
<summary>
<b>語音容器如何運作，以及如何設定？</b>
</summary>

**答：** 設定生產叢集時，有幾件事需要考慮。 首先，在同一部電腦上設定單一語言、多個容器，應該不會有太大的問題。 如果您遇到問題，可能是硬體相關問題-因此，我們會先查看資源，也就是：CPU 和記憶體規格。

請考慮一下 `ja-JP` 容器和最新的模型。 聲場模型是 CPU 取向最嚴苛的部分，而語言模型則要求最多的記憶體。 當我們對使用進行基準測試時，會花費大約0.6 的 CPU 核心來處理單一語音轉換文字的要求（當音訊即時流入時）（例如從麥克風）。 如果您是以比即時（如檔案）更快的速度來提供音訊，則該使用方式可能會加倍（1.2 x 核心）。 同時，下面所列的記憶體是解碼語音的操作記憶體。 它不*會*考慮語言模型的實際完整大小，這會位於檔案快取中。 `ja-JP`這是額外的 2 GB; 針對 `en-US` ，可能會有更多（6-7 GB）。

如果您的電腦記憶體不足，而且您嘗試在其上部署多個語言，檔案快取可能已滿，而且會強制 OS 將模型放入和移出。對於執行中的轉譯而言，這可能是災難性的，而且可能會導致速度變慢和其他效能的影響。

此外，我們還針對具有[advanced vector extension （AVX2）](speech-container-howto.md#advanced-vector-extension-support)指令集的機器，預先封裝可執行檔。 具有 AVX512 指令集的電腦將需要該目標的程式碼產生，而10個語言的10個容器可能會暫時耗盡 CPU。 這類訊息會出現在 docker 記錄檔中：

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

最後，您可以使用變數，在*單一*容器內設定您想要的解碼器數目 `DECODER MAX_COUNT` 。 因此，基本上，我們應該從您的 SKU （CPU/記憶體）開始，我們可以建議如何充分發揮其效能。 很棒的起點是指建議的主機機器資源規格。

<br>
</details>

<details>
<summary>
<b>您是否可以協助內部部署的語音轉換文字容器進行容量規劃和成本估計？</b>
</summary>

**答：** 針對批次處理模式中的容器容量，每個解碼器都可以使用兩個 CPU 核心來處理單一辨識的 2-3 倍。 我們不建議每個容器實例保留兩個以上的並行辨識，但建議您在負載平衡器後方，針對可靠性/可用性原因，執行更多的容器實例。

雖然我們可以讓每個容器實例以更多的解碼器來執行。 例如，我們可以在八個核心機器上設定每個容器實例7個的解碼器（每個最多2倍），以產生15倍的輸送量。 有一個 `DECODER_MAX_COUNT` 要注意的參數。 在極端情況下，會發生可靠性和延遲問題，輸送量也大幅增加。 若為麥克風，則會即時使用1x。 整體使用方式應該是單一辨識的大約一個核心。

針對在批次處理模式中處理 1 K 小時/天的情況，在極端的情況下，3個 Vm 可以在24小時內處理它，但無法保證。 若要處理尖峰天數、容錯移轉、更新，以及提供最低的備份/BCP，建議使用4-5 部機器，而不是每個叢集有3個，並搭配2個以上的叢集。

針對硬體，我們使用標準 Azure VM `DS13_v2` 做為參考（每個核心必須是 2.6 GHz 或更佳，並已啟用 AVX2 指令集）。

| 執行個體  | vCPU （秒） | RAM    | 暫存儲存體 | 隨用隨付與 AHB | 含 AHB 的1年保留期（省下%） | 3年保留 AHB （節省%） |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $ 0.598/小時            | $ 0.3528/小時（~ 41%）                 | $ 0.2333/小時（~ 61%）                  |

根據設計參考（5個 Vm 的兩個叢集來處理 1 K 小時/天的音訊批次處理），1年的硬體成本會是：

> 2（個叢集） * 5 （每個叢集的 Vm） * $ 0.3528/小時 * 365 （天） * 24 （小時） = $ 31K/年

對應至實體機器時，一般估計是 1 vCPU = 1 個實體 CPU 核心。 實際上，1vCPU 比單一核心更強大。

針對內部內部部署，所有這些額外的因素都能發揮作用：

- 實體 CPU 的類型及其核心數目
- 在同一個 box/電腦上同時執行的 Cpu 數目
- 如何設定 Vm
- 如何使用超執行緒/多執行緒
- 如何共用記憶體
- OS 等等。

一般來說，它並不是像 Azure 環境一樣調整。 考慮到其他額外負荷，我會假設安全估計是10個實體 CPU 核心 = 8 個 Azure vCPU。 雖然熱門的 Cpu 只有八個核心。 使用內部內部部署部署時，成本會高於使用 Azure Vm。 此外，請考慮折舊率。

服務成本與線上服務相同： $ 1/小時適用于語音轉換文字。 語音服務成本為：

> $1 * 1000 * 365 = $ 365K

向 Microsoft 付費的維護成本取決於服務層級和服務的內容。 這種方式可從每月的 $ $29.99/月份，到包含的現場服務，基本層級為數十萬。 「服務/維護」的粗略數位是 $ 300/小時。 不包含人員成本。 不包含其他基礎結構成本（例如儲存體、網路和負載平衡器）。

<br>
</details>

<details>
<summary>
<b>為什麼轉譯中遺漏了標點符號？</b>
</summary>

**答：**`speech_recognition_language=<YOUR_LANGUAGE>`如果是使用碳 client，則應該在要求中明確設定。

例如：

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
輸出如下：

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>我可以搭配使用自訂聲場模型和語言模型與語音容器嗎？</b>
</summary>

我們目前只能夠傳遞一個模型識別碼，也就是自訂語言模型或自訂聲場模型。

**答：***不*同時支援聲場和語言模型的決策。 在建立整合識別碼以減少 API 中斷之前，這會持續有效。 可惜的是，目前並不支援這種情況。

<br>
</details>

<details>
<summary>
<b>您是否可以從自訂語音轉換文字容器中說明這些錯誤？</b>
</summary>

**錯誤1：**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**解答1：** 如果您是使用最新的自訂模型進行定型，我們目前不支援。 如果您使用較舊的版本進行定型，應該就可以使用。 我們仍在努力支援最新版本。

基本上，自訂容器不支援 Halide 或以 ONNX 為基礎的聲場模型（自訂訓練入口網站中的預設值）。 這是因為自訂模型未加密，而且我們不想要公開 ONNX 模型，但語言模型沒什麼問題。 客戶必須明確地選取較舊的非 ONNX 模型以進行自訂定型。 精確度不會受到影響。 模型大小可能較大（100 MB）。

> 支援模型 > 20190220 （4.5 版整合）

**錯誤2：**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**回答2：** 您必須在要求中提供正確的語音名稱，這會區分大小寫。 請參閱完整的服務名稱對應。 您必須使用 `en-US-JessaRUS` ，因為 `en-US-JessaNeural` 目前無法在文字轉換語音的容器版本中提供。

**錯誤3：**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**解答3：** 您可以使用簧片來建立語音資源，而不是認知服務資源。


<br>
</details>

<details>
<summary>
<b>支援哪些 API 通訊協定、REST 或 WS？</b>
</summary>

**答：** 針對語音轉換文字和自訂的語音轉換文字容器，我們目前僅支援以 websocket 為基礎的通訊協定。 SDK 僅支援在 WS （而非 REST）中呼叫。 目前有一種計畫可以加入 REST 支援，而不是使用 ETA。 請一律參閱官方檔，請參閱[查詢預測端點](speech-container-howto.md#query-the-containers-prediction-endpoint)。

<br>
</details>

<details>
<summary>
<b>語音容器是否支援 CentOS？</b>
</summary>

**答：** Python SDK 尚不支援 CentOS 7，也不支援 Ubuntu 19.04。

Python 語音 SDK 套件適用於下列作業系統：
- **Windows** -x64 和 x86
- **Mac** -macOS X 10.12 版或更新版本
- **Linux** -ubuntu 16.04、ubuntu 18.04、Debian 9 （x64）

如需環境設定的詳細資訊，請參閱[Python 平臺設定](quickstarts/setup-platform.md?pivots=programming-language-python)。 目前，Ubuntu 18.04 是建議的版本。

<br>
</details>

<details>
<summary>
<b>為什麼我在嘗試呼叫 LUIS 預測端點時收到錯誤？</b>
</summary>

我使用 IoT Edge 部署中的 LUIS 容器，並嘗試從另一個容器呼叫 LUIS 預測端點。 LUIS 容器正在接聽埠5001，而我使用的 URL 是：

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

我收到的錯誤是：

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

我在 LUIS 容器記錄檔中看到要求，而訊息指出：

```cmd
The request path /luis//predict" does not match a supported file type.
```

這代表什麼？ 我錯過什麼？ 我遵循語音 SDK 的範例，從[這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk)開始。 案例是我們正從電腦麥克風直接偵測音訊，並根據我們訓練的 LUIS 應用程式嘗試判斷意圖。 我所連結的範例就是完全如此。 而且也適用于 LUIS 雲端式服務。 您似乎會使用語音 SDK，讓我們不需要對語音轉換文字 API 進行個別的明確呼叫，然後再對 LUIS 進行第二次呼叫。

因此，我嘗試執行的動作就是從使用雲端中的 LUIS，到使用 LUIS 容器的案例中進行切換。 我無法想像一下語音 SDK 是否適用于另一個。

**答：** 語音 SDK 不應用於 LUIS 容器。 若要使用 LUIS 容器，應使用 LUIS SDK 或 LUIS REST API。 語音 SDK 應用於語音容器。

雲端與容器不同。 雲端可以由多個匯總的容器（有時稱為微服務）組成。 因此有一個 LUIS 容器，接著有一個語音容器-兩個不同的容器。 語音容器只會執行語音。 LUIS 容器只會執行 LUIS。 在雲端中，因為這兩個容器都已被部署，而且遠端用戶端移至雲端、執行語音、返回、再次前往雲端並執行 LUIS 的效能不佳，所以我們提供了一項功能，可讓用戶端移至語音，並移至 LUIS，然後回到用戶端。 因此，即使在此案例中，語音 SDK 也會移至具有音訊的語音雲端容器，然後語音雲端容器會與文字 LUIS 雲端容器交談。 LUIS 容器沒有接受音訊的概念（LUIS 容器接受串流音訊並不合理-LUIS 是以文字為基礎的服務）。 透過內部部署，我們不確定我們的客戶已部署這兩個容器，我們不打算在客戶內部部署的容器之間進行協調，而且如果兩個容器都部署在-內部部署上，則在用戶端有更多的本機時，不會有負擔優先返回用戶端，並讓客戶取得該文字並移至 LUIS。

<br>
</details>

<details>
<summary>
<b>為什麼我們會收到 macOS、語音容器和 Python SDK 的錯誤？</b>
</summary>

當我們傳送要轉譯的 *.wav*檔案時，結果會傳回：

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

我們知道 websocket 已正確設定。

**答：** 如果是這種情況，請參閱[此 GitHub 問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)。 我們在[此](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)提供了一種因應的解決方法。

碳固定在版本1.8。


<br>
</details>

<details>
<summary>
<b>語音容器端點有何差異？</b>
</summary>

您是否可以協助填滿下列測試計量，包括要測試哪些函式，以及如何測試 SDK 和 REST Api？ 尤其是「互動」和「對話」的差異，我從現有的 doc/sample 看不到。

| 端點                                                | 功能測試                                                   | SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | 合成文字（文字轉換語音）                                  |     | 是      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | 認知服務內部內部部署聽寫 v1 websocket 端點        | 是 | 否       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | 認知服務的內部部署互動式 v1 websocket 端點  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | 認知服務內部內部部署交談 v1 websocket 端點 |     |          |

**答：** 這是的融合：
- 嘗試進行容器聽寫端點的人員（我不確定他們取得該 URL 的方式）
- 1<sup>st</sup>合作物件端點是容器中的一個。
- 1<sup>st</sup>合作物件端點會傳回 speech 訊息，而不是 `speech.hypothesis` 針對聽寫端點傳回的3個<sup>rd</sup>元件端點訊息。
- 碳快速入門全部使用 `RecognizeOnce` （互動模式）
- 對於 `speech.fragment` 要求不會在互動模式中傳回的訊息，其具有判斷提示。
- 在發行組建中引發判斷提示的碳（終止進程）。

因應措施是切換至在您的程式碼中使用連續辨識，或（更快）連接到容器中的互動式或連續端點。
針對您的程式碼，將端點設定為 <主機：埠>/speech/recognition/interactive/cognitiveservices/v1

如需各種模式，請參閱語音模式-請參閱下列內容：

[!INCLUDE [speech-modes](includes/speech-modes.md)]

適當的修正會隨附于 SDK 1.8，其具有內部部署支援（會挑選正確的端點，因此我們將不會比線上服務更糟）。 同時，還有連續辨識的範例，為什麼不會指向它呢？

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>我應該使用哪種模式來進行各種音訊檔案？</b>
</summary>

**答：** 以下是[使用 Python 的快速入門](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python)。 您可以在檔網站上找到其他已連結的語言。

只是為了清楚說明互動式、對話和聽寫;這是指定服務將處理語音要求之特定方式的先進方法。 可惜的是，對於內部部署中的容器，我們必須指定完整的 URI （因為它包含本機電腦），因此這類資訊會從抽象概念中洩漏。 我們正與 SDK 小組合作，使其更能在未來使用。

<br>
</details>

<details>
<summary>
<b>如何將交易/秒/核心的粗略量值進行基準測試？</b>
</summary>

**答：** 以下是現有模型中預期會有的一些粗略數位（在我們將在 GA 中提供的資訊會變得更好）：

- 針對檔案，節流會在語音 SDK 中，速度為2倍。 前5秒的音訊不會進行節流。 解碼器能夠進行大約3倍的即時時間。 為此，單一辨識的整體 CPU 使用量會接近2個核心。
- 若為 mic，則會即時為1x。 單一辨識的整體使用量應該大約為1個核心。

這一切都可透過 docker 記錄進行驗證。 我們實際上會傾印具有 session 和片語/語句統計資料的程式程式碼，其中包含 RTF 數位。


<br>
</details>

<details>
<summary>
<b>將音訊檔案分割成適用于語音容器用途的 chucks 是很常見的嗎？</b>
</summary>

我目前的計畫是採用現有的音訊檔案，並將它分割成10秒的區塊，然後透過容器傳送這些檔案。 這是可接受的案例嗎？  是否有更好的方法可以使用容器來處理較大的音訊檔案？

**答：** 只要使用語音 SDK 並為其提供檔案，它就會執行正確的動作。 為什麼需要將檔案進行區塊處理？


<br>
</details>

<details>
<summary>
<b>如何? 讓多個容器在相同的主機上執行嗎？</b>
</summary>

檔指出要公開不同的埠，我這麼做，但 LUIS 容器仍在接聽埠5000？

**答：** 試試看 `-p <outside_unique_port>:5000` 。 例如： `-p 5001:5000` 。


<br>
</details>

## <a name="technical-questions"></a>技術問題

<details>
<summary>
<b>如何取得非批次 Api 以處理音訊 &lt; 15 秒的時間？</b>
</summary>

**答：** `RecognizeOnce()`在互動模式中，只會處理最多15秒的音訊，因為模式適用于語音命令，其中語句應該是 short。 如果您使用 `StartContinuousRecognition()` 進行聽寫或交談，則不會有15秒的限制。


<br>
</details>

<details>
<summary>
<b>建議的資源、CPU 和 RAM;針對50並行要求？</b>
</summary>

有多少個並行要求會有4個核心，4 GB 的 RAM 處理？ 例如，如果我們必須提供50個並行要求，建議使用多少核心和 RAM？

**答：** 使用最新的8個即時， `en-US` 因此建議使用超過6個並行要求的 docker 容器。 它會 crazier 超過16個核心，而且它會變成非統一記憶體存取（NUMA）節點的敏感性。 下表說明每個語音容器的資源的最低和建議配置。

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

| 容器      | 最小值             | 建議         |
|----------------|---------------------|---------------------|
| 語音轉文字 | 2核心，2 GB 記憶體 | 4核心，4 GB 記憶體 |

# <a name="custom-speech-to-text"></a>[自訂語音轉換文字](#tab/cstt)

| 容器             | 最小值             | 建議         |
|-----------------------|---------------------|---------------------|
| 自訂語音轉換文字 | 2核心，2 GB 記憶體 | 4核心，4 GB 記憶體 |

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

| 容器      | 最小值             | 建議         |
|----------------|---------------------|---------------------|
| 文字轉換語音 | 1核心，2 GB 記憶體 | 2核心，3 GB 記憶體 |

# <a name="custom-text-to-speech"></a>[自訂文字轉換語音](#tab/ctts)

| 容器             | 最小值             | 建議         |
|-----------------------|---------------------|---------------------|
| 自訂文字轉換語音 | 1核心，2 GB 記憶體 | 2核心，3 GB 記憶體 |

***

- 每個核心必須至少有 2.6 GHz 或更快的速度。
- 針對檔案，節流將會在語音 SDK 中，在2分鐘（前5秒的音訊未進行節流）。
- 此解碼器能夠進行大約 2-3 倍的即時作業。 為此，單一辨識的整體 CPU 使用量會接近兩個核心。 這就是為什麼我們不建議在每個容器實例中保留兩個以上的使用中連接。 最極端的是在八個核心機器（例如）中，將大約10個解碼器放在2倍 `DS13_V2` 。 針對容器版本1.3 和更新版本，有一個您可以嘗試設定的參數 `DECODER_MAX_COUNT=20` 。
- 若為麥克風，則會即時為1x。 整體使用方式應該是單一辨識的大約一個核心。

請考慮您擁有的音訊時數總計。 如果數位很大，若要改善可靠性/可用性，建議您在負載平衡器後方的單一或多個方塊上執行更多的容器實例。 您可以使用 Kubernetes （K8S）和 Helm，或透過 Docker 撰寫來完成協調流程。

例如，若要處理1000小時/24 小時，我們嘗試設定 3-4 Vm，每個 VM 10 個實例/個解碼器。

<br>
</details>

<details>
<summary>
<b>語音容器支援標點符號嗎？</b>
</summary>

**答：** 在內部部署容器中，我們有大小寫（ITN）可供使用。 標點符號與語言無關，而某些語言（包括中文和日文）則不支援。

針對現有的容器 *，我們有*隱含和基本的標點符號支援，但它 `off` 預設為。 這表示您可以取得 `.` 範例中的字元，而不是 `。` 字元。 若要啟用此隱含邏輯，以下是如何使用我們的語音 SDK 在 Python 中執行此動作的範例（類似于其他語言）：

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>為什麼我在嘗試將資料張貼至語音轉換文字容器時收到404錯誤？</b>
</summary>

以下是範例 HTTP POST：

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**答：** 我們不支援語音轉換文字容器中的 REST API，我們只透過語音 SDK 支援 Websocket。 請一律參閱官方檔，請參閱[查詢預測端點](speech-container-howto.md#query-the-containers-prediction-endpoint)。

<br>
</details>

<details>
<summary>
<b>使用語音轉換文字服務時，為什麼會收到此錯誤？</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**答：** 這通常會在您以音訊的速度比語音辨識容器所能採用的時間更快時發生。 用戶端緩衝區會填滿，並觸發取消。 您必須控制用來傳送音訊的平行存取和 RTF。

<br>
</details>

<details>
<summary>
<b>您可以從 c + + 範例說明這些文字轉換語音容器的錯誤嗎？</b>
</summary>

**答：** 如果容器版本早于1.3，則應該使用此程式碼：

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

較舊的容器沒有適用于此 API 之碳的必要端點 `FromHost` 。 如果用於1.3 版的容器，則應該使用此程式碼：

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

以下是使用 API 的範例 `FromEndpoint` ：

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 `SetSpeechSynthesisVoiceName`系統會呼叫函式，因為具有更新文字轉換語音引擎的容器需要語音名稱。

<br>
</details>

<details>
<summary>
<b>如何搭配使用「語音 SDK」的？1.7 和「語音」容器？</b>
</summary>

**答：** 語音容器上有三個端點適用于不同的使用方式，它們定義為語音模式-請參閱下列內容：

[!INCLUDE [speech-modes](includes/speech-modes.md)]

它們適用于不同的用途，並以不同的方式使用。

Python[範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)：
- 如需使用自訂端點（也就是使用端點參數）的單一辨識（互動模式） `SpeechConfig` ，請參閱 `speech_recognize_once_from_file_with_custom_endpoint_parameters()` 。
- 如需連續辨識（對話模式），並只修改以使用上述的自訂端點，請參閱 `speech_recognize_continuous_from_file()` 。
- 若要啟用上述範例中的聽寫（僅在您真正需要的情況下），請在建立之後 `speech_config` ，立即加入程式碼 `speech_config.enable_dictation()` 。

在 c # 中啟用聽寫功能，叫用函式 `SpeechConfig.EnableDictation()` 。

### <a name="fromendpoint-apis"></a>`FromEndpoint`Api
| Language | API 詳細資料 |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 目前不支援，也不會進行規劃。 |

<br>
</details>

<details>
<summary>
<b>我要如何搭配語音容器來使用「1.8 版」的語音 SDK？</b>
</summary>

**答：** 有一個新的 `FromHost` API。 這不會取代或修改任何現有的 Api。 它只是新增了使用自訂主機來建立語音設定的替代方式。

### <a name="fromhost-apis"></a>`FromHost`Api

| Language | API 詳細資料 |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 目前不支援 |

> 參數：主機（必要）、訂用帳戶金鑰（選擇性，如果您可以使用服務，則不需要它）。

Host 的格式是 `protocol://hostname:port` `:port` 選擇性的（如下所示）：
- 如果容器是在本機執行，則主機名稱為 `localhost` 。
- 如果容器是在遠端伺服器上執行，請使用該伺服器的主機名稱或 IPv4 位址。

語音轉換文字的主機參數範例：
- `ws://localhost:5000`-使用埠5000對本機容器進行不安全的連接
- `ws://some.host.com:5000`-與遠端伺服器上執行之容器的非安全連線

上述的 Python 範例，但使用 `host` 參數，而不是 `endpoint` ：

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [認知服務容器](speech-container-howto.md)
