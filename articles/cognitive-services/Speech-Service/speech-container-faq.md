---
title: '語音服務容器的常見問題 (常見問題) '
titleSuffix: Azure Cognitive Services
description: 安裝和執行語音容器。 語音轉換文字轉譯即時串流至文字，讓您的應用程式、工具或裝置可以使用或顯示。 文字轉語音會將輸入文字轉換為仿真人的合成語音。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: b13a6944290f58f5ede239dee60610d67fff8b1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88918463"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>語音服務容器的常見問題 (常見問題) 

搭配容器使用語音服務時，請先依賴這組常見問題，再升級為支援。 本文會從一般到技術的不同程度來捕捉問題。 若要展開答案，請按一下問題。

## <a name="general-questions"></a>一般問題

<details>
<summary>
<b>語音容器的運作方式，以及如何設定？</b>
</summary>

**答：** 設定生產叢集時，需要考慮幾件事。 首先，在同一部電腦上設定單一語言、多個容器，不應該是很大的問題。 如果您遇到問題，可能是硬體相關的問題，因此我們會先查看資源，也就是：CPU 和記憶體規格。

請考慮一段時間、 `ja-JP` 容器和最新模型。 聲場模型是最嚴苛的 CPU，而語言模型則要求最多的記憶體。 當我們對使用進行基準測試時，大約要有0.6 的 CPU 核心，才能在音訊即時流入 (（例如麥克風) ）流入時處理單一語音轉文字的要求。 如果您要以較即時的方式（例如從檔案) ）來將音訊饋送 (，該使用量可以 (1.2 x 核心) 。 同時，以下所列的記憶體是用來解碼語音的作業系統。 它不 *會* 考慮語言模型的實際完整大小，這會位於檔案快取中。 這是 `ja-JP` 額外的 2 gb; 針對 `en-US` ，它可能更 (6-7 gb) 。

如果您有記憶體不足的電腦，而且您想要在其上部署多個語言，則可能會有檔案快取已滿，且會強制 OS 將模型移入和移出分頁。針對執行中的轉譯，這可能是災難性的，可能會導致變慢和其他效能的影響。

此外，我們會為具有 [advanced vector extension (AVX2) ](speech-container-howto.md#advanced-vector-extension-support) 指令集的電腦預先封裝可執行檔。 具有 AVX512 指令集的電腦將需要該目標的程式碼產生，而啟動10個語言的10個容器可能會暫時耗盡 CPU。 這類訊息會出現在 docker 記錄檔中：

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

最後，您可以使用變數，在 *單一* 容器內設定您想要的解碼器數目 `DECODER MAX_COUNT` 。 因此，基本上，我們應該從您的 SKU 開始 (的 CPU/記憶體) ，我們可以建議如何充分運用它。 很棒的起點是指建議的主機機器資源規格。

<br>
</details>

<details>
<summary>
<b>您是否可以協助內部部署語音轉換文字容器的容量規劃和成本估計？</b>
</summary>

**答：** 針對批次處理模式中的容器容量，每個解碼器都可以即時處理2到3倍（具有兩個 CPU 核心）進行單一辨識。 我們不建議每個容器實例保留兩個以上的並行表彰，但建議您在負載平衡器後方的可靠性/可用性原因，執行更多的容器實例。

雖然我們可以讓每個容器實例以更多的解碼器來執行。 例如，我們可能可以在八部核心電腦上的每個容器實例上設定7個 (，每個) 超過2倍，以產生15倍的輸送量。 有一個 `DECODER_MAX_COUNT` 要注意的參數。 在極端情況下，會產生可靠性和延遲問題，輸送量也會大幅增加。 若為麥克風，則會有最久的時間。 整體使用方式應該是單一辨識的一個核心。

在批次處理模式中處理 1 K 小時/天的案例中，在極端情況下，3個 Vm 可以在24小時內處理，但不保證。 為了處理尖峰天、容錯移轉、更新，以及提供最小的備份/BCP，我們建議使用4-5 的機器，而不是每個叢集3個，以及2個以上的叢集。

針對硬體，我們使用標準 Azure VM `DS13_v2` 作為參考 (每個核心都必須是 2.6 GHz 或更好的，且已啟用 AVX2 指令集) 。

| 執行個體  | vCPU (s)  | RAM    | 暫存儲存體 | 隨用隨付與 AHB | 1年保留 AHB (% 的節省)  | 使用 AHB 節省3年 (%)  |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | 每小時 $ 0.598            | $ 0.3528/小時 (~ 41% )                  | $ 0.2333/小時 (~ 61% )                   |

根據設計參考 (5 個 Vm 的兩個叢集來處理 1 K 小時/天音訊批次處理) ，1年的硬體成本將會是：

> 2 (叢集) * 5 (每個叢集的 Vm) * $ 0.3528/小時 * 365 (天) * 24 (小時) = $ 31K/年

對應至實體機器時，一般估計為 1 vCPU = 1 個實體 CPU 核心。 實際上，1vCPU 比單一核心更強大。

針對內部內部部署，所有這些額外的因素都能發揮作用：

- 實體 CPU 的類型和其上的核心數目
- 相同 box/電腦上同時執行的 Cpu 數目
- Vm 的設定方式
- 如何使用超執行緒/多執行緒處理
- 如何共用記憶體
- 作業系統等等。

一般來說，它並不會與 Azure 環境調整。 考慮到其他負擔，我會說安全的估計是10個實體 CPU 核心 = 8 個 Azure vCPU。 雖然熱門 Cpu 只有八個核心。 使用內部內部部署部署時，成本將會高於使用 Azure Vm。 此外，請考慮折舊率。

服務成本與線上服務相同：適用于語音轉換文字的每小時 $ 1/小時。 語音服務成本為：

> $1 * 1000 * 365 = $ 365K

支付給 Microsoft 的維護成本取決於服務層級和服務的內容。 這項技術的不同之處是，如果涉及現場服務，基本層級會從 $ $29.99/月到數十萬。 服務/維護的粗略數位是 $ 300/小時。 不包含人員成本。 不包含其他基礎結構成本 (例如儲存體、網路和負載平衡器) 。

<br>
</details>

<details>
<summary>
<b>轉譯程式中遺失標點符號的原因為何？</b>
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
<b>我可以使用自訂聲場模型和語言模型搭配語音容器嗎？</b>
</summary>

我們目前只能傳遞一個模型識別碼，也就是自訂語言模型或自訂聲場模型。

**答：***不*同時支援聲場和語言模型的決策。 這會持續生效，直到建立統一的識別碼以減少 API 中斷為止。 可惜的是，目前不支援此功能。

<br>
</details>

<details>
<summary>
<b>您可以從自訂語音轉換文字容器解釋這些錯誤嗎？</b>
</summary>

**錯誤1：**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**解答1：** 如果您要使用最新的自訂模型進行定型，我們目前不支援。 如果您使用較舊的版本進行定型，則應該可以使用。 我們仍在努力支援最新版本。

基本上，自訂容器不支援 Halide 或以 ONNX 為基礎的聲場模型 (這是自訂定型入口網站) 中的預設值。 這是因為自訂模型未經過加密，而且我們不想要公開 ONNX 模型，語言模型是正常的。 客戶必須明確地選取較舊的非 ONNX 模型以進行自訂定型。 精確度不會受到影響。 模型大小可能較大 (100 MB) 。

> 支援模型 > 20190220 (4.5 版整合) 

**錯誤2：**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**回答2：** 您必須在要求中提供正確的語音名稱，此名稱會區分大小寫。 請參閱完整的服務名稱對應。 您必須使用 `en-US-JessaRUS` ，因為目前 `en-US-JessaNeural` 無法在文字轉換語音的容器版本中使用。

**錯誤3：**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**解答3：** 您用來建立語音資源，而不是認知服務資源的簧片。


<br>
</details>

<details>
<summary>
<b>支援哪些 API 通訊協定、REST 或 WS？</b>
</summary>

**答：** 針對語音轉換文字和自訂語音轉換文字容器，我們目前僅支援 websocket 通訊協定。 SDK 只支援在 WS 中呼叫，但不支援 REST。 目前有一個方案可新增 REST 支援，但目前沒有 ETA。 請一律參考官方檔，請參閱 [查詢預測端點](speech-container-howto.md#query-the-containers-prediction-endpoint)。

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

如需環境設定的詳細資訊，請參閱 [Python 平臺設定](quickstarts/setup-platform.md?pivots=programming-language-python)。 目前，Ubuntu 18.04 是建議的版本。

<br>
</details>

<details>
<summary>
<b>為什麼我在嘗試呼叫 LUIS 預測端點時遇到錯誤？</b>
</summary>

我在 IoT Edge 部署中使用 LUIS 容器，並嘗試從另一個容器呼叫 LUIS 預測端點。 LUIS 容器正在接聽埠5001，而我所使用的 URL 如下：

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

我遇到的錯誤是：

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

我在 LUIS 容器記錄中看到要求，且訊息顯示：

```cmd
The request path /luis//predict" does not match a supported file type.
```

這代表什麼？ 我遺失了什麼？ 我已遵循語音 SDK 的範例，從 [這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk)開始。 案例是，我們會根據我們定型的 LUIS 應用程式，直接從電腦麥克風偵測音訊，並嘗試判斷意圖。 我所連結的範例的確如此。 它可與 LUIS 的雲端式服務搭配運作。 使用語音 SDK 似乎可讓我們不必另行明確呼叫語音轉換文字 API，然後再進行第二次呼叫 LUIS。

因此，我只想從使用雲端中 LUIS 的案例切換到使用 LUIS 容器。 我不可以想像語音 SDK 是否適用于另一種。

**答：** 語音 SDK 不應針對 LUIS 容器使用。 若要使用 LUIS 容器，應使用 LUIS SDK 或 LUIS REST API。 語音 SDK 應針對語音容器使用。

雲端與容器不同。 雲端可以由多個匯總的容器組成 (有時稱為微型服務) 。 因此有一個 LUIS 容器，然後有一個語音容器-兩個不同的容器。 語音容器只會執行語音。 LUIS 容器只會執行 LUIS。 在雲端中，因為這兩個容器都是已知的部署方式，而且如果遠端用戶端移至雲端、執行語音、返回，然後再次移至雲端並執行 LUIS，我們提供了一項功能，可讓用戶端移至語音，然後移至 LUIS，然後再回到用戶端。 因此，即使在此案例中，語音 SDK 會將音訊的語音雲端容器移至語音雲端容器，然後語音雲端容器會使用文字來與 LUIS cloud 容器交談。 LUIS 容器沒有接受音訊的概念 (不適合 LUIS 容器接受串流音訊-LUIS 是以文字為基礎的服務) 。 在內部部署的情況下，我們並不確定我們的客戶已部署兩個容器，但我們不會在客戶的內部部署容器之間進行協調，而如果兩個容器都部署在內部內部部署上，則不會將 SR 優先移回用戶端，並讓客戶取得該文字並移至 LUIS。

<br>
</details>

<details>
<summary>
<b>為什麼我們會收到 macOS、語音容器和 Python SDK 的錯誤？</b>
</summary>

當我們傳送 *.wav* 檔給轉譯時，結果會傳回：

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

**答：** 如果是這種情況，則請參閱 [此 GitHub 問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)。 我們在 [這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)提供了一個解決辦法。

請在1.8 版修正此問題。


<br>
</details>

<details>
<summary>
<b>語音容器端點有何差異？</b>
</summary>

您是否可以協助填滿下列測試計量，包括要測試的函式，以及如何測試 SDK 和 REST Api？ 尤其是「互動式」和「交談」的差異，我在現有的 doc/範例中看不到。

| 端點                                                | 功能測試                                                   | SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | 合成文字 (文字轉換語音)                                   |     | 是      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | 認知服務內部內部部署聽寫 v1 websocket 端點        | 是 | 否       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | 認知服務內部內部部署互動式 v1 websocket 端點  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | 認知服務內部內部部署對話 v1 websocket 端點 |     |          |

**答：** 這是下列各項的融合：
- 嘗試容器的聽寫端點的人， (我不確定其取得 URL 的方式) 
- 1個<sup>st</sup> 合作物件端點是容器中的端點。
- 傳回語音的 1<sup>st</sup> 合作物件端點，而不是 `speech.hypothesis` 3<sup>rd</sup> 元件端點針對聽寫端點傳回的訊息。
- 碳快速入門會使用 `RecognizeOnce` (互動模式) 
- 具有判斷提示的碳對 `speech.fragment` 要求它們的訊息不會在互動模式中傳回。
- 在發行組建中引發 assert 的碳 (終止進程) 。

因應措施是切換至在您的程式碼中使用連續辨識，或 (更快速) 連接到容器中的互動式或連續端點。
針對您的程式碼，將端點設定為 `host:port` /speech/recognition/interactive/cognitiveservices/v1

如需各種模式，請參閱語音模式-請參閱以下內容：

[!INCLUDE [speech-modes](includes/speech-modes.md)]

適當的修正會隨附于 SDK 1.8，其具有內部部署支援 (將挑選正確的端點，因此我們將不會比線上服務) 更糟。 在此同時，還有連續辨識的範例，為什麼我們不會指向它？

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>我應該使用哪種模式的不同音訊檔案？</b>
</summary>

**答：** 以下是 [使用 Python 的快速入門](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python)。 您可以在檔網站上找到其他連結的語言。

只是為了表達互動式、對話和聽寫;這是指定服務將處理語音要求之特定方式的先進方式。 可惜的是，對於內部內部部署的容器，我們必須指定完整的 URI (，因為它包含本機電腦) ，因此此資訊是從抽象的洩漏。 我們正與 SDK 小組合作，讓未來更能使用。

<br>
</details>

<details>
<summary>
<b>我們要如何對交易/秒/核心的粗略量值進行基準測試？</b>
</summary>

**答：** 以下是現有模型所預期的一些粗略數位， (將會在正式運作) 中有更好的變更：

- 針對檔案，節流將會在語音 SDK 中，以2x 為間隔。 音訊的前五秒不會進行節流。 解碼器能夠執行大約3倍的時間。 如此一來，整體 CPU 使用率將會接近2個核心，以進行單一辨識。
- 若為 mic，則會有最久的時間。 單一辨識的整體使用量應大約是1個核心。

這可以透過 docker 記錄進行驗證。 我們實際上是使用 session 和片語/語句統計資料來傾印這一行，其中包含 RTF 數位。


<br>
</details>

<details>
<summary>
<b>將音訊檔案分割成 chucks 以進行語音容器使用是否很常見？</b>
</summary>

我目前的計畫是取得現有的音訊檔案，並將它分割為10秒的區塊，然後透過容器來傳送這些檔案。 這是可接受的案例嗎？  有更好的方法可以處理容器中較大的音訊檔案嗎？

**答：** 您只需使用語音 SDK 並為其提供檔案，就會執行正確的動作。 為什麼您需要區出檔案？


<br>
</details>

<details>
<summary>
<b>如何? 讓多個容器在相同的主機上執行？</b>
</summary>

檔說要公開不同的埠，我這麼做，但 LUIS 容器仍在接聽埠5000？

**答：** 請試試看 `-p <outside_unique_port>:5000` 。 例如： `-p 5001:5000` 。


<br>
</details>

## <a name="technical-questions"></a>技術問題

<details>
<summary>
<b>如何取得非批次 Api 來處理 &lt; 15 秒的音訊？</b>
</summary>

**答：** `RecognizeOnce()` 在互動模式中，最多隻會處理15秒的音訊，因為此模式適用于語音命令，其中語句預期會很短。 如果您使用 `StartContinuousRecognition()` 聽寫或對話，則不會有15秒的限制。


<br>
</details>

<details>
<summary>
<b>建議的資源、CPU 和 RAM 有哪些;針對50的並行要求嗎？</b>
</summary>

有多少並行要求會有4核心、4 GB RAM 處理？ 例如，如果我們必須提供50個並行要求的服務，建議使用多少核心和 RAM？

**答：** 使用我們的最新版本時，即時8， `en-US` 因此建議使用超過6個並行要求的多個 docker 容器。 它會 crazier 超過16個核心，並變成 (NUMA) 節點敏感性的非統一記憶體存取。 下表說明每個語音容器的資源配置下限和建議。

# <a name="speech-to-text"></a>[語音轉換文字](#tab/stt)

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

- 每個核心都必須至少有 2.6 GHz 或更快。
- 針對檔案，節流將會在語音 SDK 中，2 (前5秒的音訊不會受到節流) 。
- 此解碼器能夠執行大約 2-3 倍的時間。 如此一來，整體 CPU 使用率將會接近兩個核心來進行單一辨識。 這就是為什麼我們不建議每個容器實例保留兩個以上的作用中連線。 最極端的是在八部核心電腦（例如）中，將大約10個解碼器的時間放在八個核心電腦上 `DS13_V2` 。 針對容器1.3 版和更新版本，有一個您可以嘗試設定的參數 `DECODER_MAX_COUNT=20` 。
- 若為麥克風，則會有最久的時間。 整體使用方式應該是單一辨識的一個核心。

請考慮您所擁有之音訊的總小時數。 如果數位很大，若要改善可靠性/可用性，建議您在負載平衡器後方的單一或多個方塊上執行多個容器實例。 您可以使用 Kubernetes (K8S) 和 Helm，或使用 Docker 撰寫來完成協調流程。

例如，若要處理1000小時/24 小時，我們已嘗試為每個 VM 設定10個實例/VM 的 3-4 Vm。

<br>
</details>

<details>
<summary>
<b>語音容器是否支援標點符號？</b>
</summary>

**答：** 在內部部署容器中可使用 (ITN) 大小寫。 標點符號與語言相依，而且不支援某些語言，包括中文和日文。

針對現有的容器 *，我們有* 隱含和基本標點符號的支援，但它是 `off` 預設的。 這表示您可以取得 `.` 範例中的字元，而不是 `。` 字元。 為了啟用此隱含邏輯，以下是如何使用語音 SDK 在 Python 中執行此作業的範例 (它會與其他語言) 類似：

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
<b>當我嘗試將資料張貼到語音轉換文字容器時，為什麼會收到404錯誤？</b>
</summary>

以下是 HTTP POST 範例：

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

**答：** 我們不支援語音轉換文字容器中的 REST API，我們僅透過語音 SDK 支援 Websocket。 請一律參考官方檔，請參閱 [查詢預測端點](speech-container-howto.md#query-the-containers-prediction-endpoint)。

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

**答：** 這通常會在您的音訊播放速度比語音辨識容器所能採用的速度還快。 用戶端緩衝區會填滿，並觸發取消。 您需要控制用來傳送音訊的平行存取和 RTF。

<br>
</details>

<details>
<summary>
<b>您可以從 c + + 範例說明這些文字轉換語音的容器錯誤嗎？</b>
</summary>

**答：** 如果容器版本早于1.3，則應該使用此程式碼：

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

較舊的容器沒有適用于使用 API 之碳的必要端點 `FromHost` 。 如果是用於1.3 版的容器，則應該使用此程式碼：

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

 `SetSpeechSynthesisVoiceName`因為具有更新文字轉換語音引擎的容器需要語音名稱，所以會呼叫此函式。

<br>
</details>

<details>
<summary>
<b>如何搭配使用「語音 SDK」和「語音」容器來使用「？1.7」？</b>
</summary>

**答：** 語音容器上有三個端點適用于不同的使用方式，它們是定義為語音模式-請參閱以下內容：

[!INCLUDE [speech-modes](includes/speech-modes.md)]

它們是基於不同的用途，並以不同的方式使用。

Python [範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)：
- 若為單一辨識 (互動模式) 使用自訂端點 (也就是： `SpeechConfig`) 的端點參數，請參閱 `speech_recognize_once_from_file_with_custom_endpoint_parameters()` 。
- 如需持續辨識 (對話模式) ，而且只要修改為使用上述的自訂端點，請參閱 `speech_recognize_continuous_from_file()` 。
- 若要在上述範例中啟用聽寫 (只有在您真正需要的情況下才) ，請在您建立之後 `speech_config` ，加入程式碼 `speech_config.enable_dictation()` 。

在 c # 中，若要啟用聽寫，請叫用 `SpeechConfig.EnableDictation()` 函數。

### <a name="fromendpoint-apis"></a>`FromEndpoint` Api
| Language | API 詳細資料 |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 目前不受支援，也不會規劃。 |

<br>
</details>

<details>
<summary>
<b>如何搭配語音容器使用1.8 版的語音 SDK？</b>
</summary>

**答：** 有新的 `FromHost` API。 這不會取代或修改任何現有的 Api。 它只會新增使用自訂主機建立語音設定的替代方法。

### <a name="fromhost-apis"></a>`FromHost` Api

| Language | API 詳細資料 |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 目前不支援 |

> 參數：主機 (強制) ，訂用帳戶金鑰 (選擇性，如果您可以使用服務，而不) 。

Host 的格式是 `protocol://hostname:port` `:port` 選擇性的 (請參閱下面) ：
- 如果容器在本機執行，主機名稱為 `localhost` 。
- 如果容器正在遠端伺服器上執行，請使用該伺服器的主機名稱或 IPv4 位址。

語音轉換文字的主機參數範例：
- `ws://localhost:5000` -使用埠5000的本機容器的非安全連線
- `ws://some.host.com:5000` -與遠端伺服器上執行的容器之間的非安全連線

上述的 Python 範例，但使用 `host` 參數，而不是 `endpoint` ：

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [認知服務容器](speech-container-howto.md)
