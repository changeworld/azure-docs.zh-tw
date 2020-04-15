---
title: 語音服務容器常見問題 (FAQ)
titleSuffix: Azure Cognitive Services
description: 安裝並運行語音容器。 語音到文字將音訊串流即時轉錄到文本,應用程式、工具或設備可以使用或顯示。 文字轉語音會將輸入文字轉換為仿真人的合成語音。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: aahi
ms.openlocfilehash: 17582244aef173da6ac700c980f7bd7fb0fec307
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383089"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>語音服務容器常見問題 (FAQ)

將語音服務與容器一起使用時,請依賴此常見問題集合,然後再升級以支援。 本文從一般到技術,都不同程度地捕獲了問題。 要展開答案,請單擊該問題。

## <a name="general-questions"></a>一般問題

<details>
<summary>
<b>語音容器如何工作,如何設置它們?</b>
</summary>

**答案:** 設置生產群集時,需要考慮以下幾點。 首先,在同一台計算機上設置單一語言、多個容器不應是大問題。 如果您遇到問題,這可能是一個與硬體相關的問題 - 因此,我們將首先查看資源,即;CPU 和記憶體規格。

考慮一下,`ja-JP`容器和最新的模型。 聲學模型是 CPU 要求最高的部分,而語言模型需要最多的記憶體。 當我們對使用進行基準測試時,當音訊即時流入時(如從麥克風流出),大約需要 0.6 個 CPU 內核來處理單個語音到文本請求。 如果要以比即時(如從檔中)更快地饋送音訊,則該使用量可以加倍(1.2 倍內核)。 同時,下面列出的記憶體是用於解碼語音的操作記憶體。 它不考慮*語言*模型的實際全大小,該大小將駐留在檔緩存中。 對於`ja-JP`這是額外的2 GB;對於`en-US`,它可能更多 (6-7 GB)。

如果電腦記憶體不足,並且嘗試在電腦上部署多種語言,則檔緩存可能已滿,並且操作系統被迫將模型頁到和外頁。對於正在運行的轉錄,這可能是災難性的,並可能導致減速和其他性能影響。

此外,我們為具有[先進向量擴展 (AVX2)](speech-container-howto.md#advanced-vector-extension-support)指令集的電腦預包裝可執行檔。 具有 AVX512 指令集的電腦將需要為該目標生成代碼,並且為 10 種語言啟動 10 個容器可能會暫時耗盡 CPU。 像這樣的訊息會顯示在 docker 紀錄中:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

最後,您可以使用`DECODER MAX_COUNT`變數設置*單個容器內*所需的解碼器數量。 因此,基本上,我們應該從您的 SKU(CPU/記憶體)開始,我們可以建議如何充分利用它。 一個很好的起點是參考推薦的主機資源規格。

<br>
</details>

<details>
<summary>
<b>您能否幫助預置語音容器的容量規劃和成本估算?</b>
</summary>

**答案:** 對於批處理模式下的容器容量,每個解碼器可以即時處理 2-3 倍,並帶有兩個 CPU 內核,實現單個識別。 我們不建議每個容器實例保留兩個以上併發識別,但出於可靠性/可用性原因,建議在負載均衡器後面運行更多的容器實例。

儘管我們可以讓每個容器實例使用更多的解碼器運行。 例如,我們可以在 8 台核心電腦上為每個容器實例設置 7 個解碼器(每台代碼數超過 2 倍),從而產生 15 倍的輸送量。 需要注意一個參數`DECODER_MAX_COUNT`。 對於極端情況,會出現可靠性和延遲問題,輸送量顯著增加。 對於麥克風,它將是 1 倍的即時。 對於單個識別,總體用法應約為一個核心。

對於批處理模式下每天處理 1 K 小時的情況,在極端情況下,3 個 VM 可以在 24 小時內處理,但不能保證。 要處理高峰日、故障轉移、更新和提供最少的備份/BCP,我們建議使用 4-5 台電腦,而不是每個群集的 3 台計算機,以及 2+ 群集。

對於硬體,我們使用標準 Azure `DS13_v2` VM 作為參考(每個內核必須為 2.6 GHz 或更高,啟用 AVX2 指令集)。

| 執行個體  | vCPU | RAM    | 暫存記憶體 | 隨付隨付 | 帶 AHB 的 1 年期準備金(百分比儲蓄) | 3年預留與AHB(百分比節省) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 吉布 | 112 GiB      | $0.598/小時            | $0.3528/小時 (+41%)                 | $0.2333/小時 (+61%)                  |

基於設計參考(兩個 5 個 VM 群集可處理 1 K 小時/ 天音訊批次處理),1 年硬體成本為:

> 2(群集) = 5 (每個群集的 VM) = 0.3528 美元/小時 = 365 美元(天) = 24 (小時) = 31K / 年

映射到物理計算機時,一般估計為 1 vCPU = 1 物理 CPU 核心。 實際上,1vCPU 比單個內核更強大。

對於預賽,所有這些附加因素都起作用了:

- 實體 CPU 的類型及其上有多少個核心
- 在同一盒/機上一起執行的 CPU 數量
- 如何設定 VM
- 如何使用超線程/多線程
- 如何共用記憶體
- 作業系統等

通常,它不像 Azure 環境那樣調整良好。 考慮到其他開銷,我認為安全估計是 10 個物理 CPU 內核 = 8 個 Azure vCPU。 雖然流行的 CPU 只有八個內核。 使用預先部署時,成本將高於使用 Azure VM。 此外,請考慮折舊率。

服務成本與在線服務相同:語音到文本每小時 1 美元。 語音服務成本為:

> $1 = 1000 × 365 = $365K

支付給 Microsoft 的維護成本取決於服務的服務級別和服務內容。 從29.99美元/月的基本水準到幾十萬,如果現場服務涉及。 粗略的數位是300美元/小時的服務/維護。 不包括人員成本。 不包括其他基礎結構成本(如存儲、網路和負載均衡器)。

<br>
</details>

<details>
<summary>
<b>為什麼轉錄中缺少標點符號?</b>
</summary>

**答案:** 請`speech_recognition_language=<YOUR_LANGUAGE>`要求使用碳用戶端,則應在請求中顯示式設定 。

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
<b>是否可以將自定義聲學模型和語言模型與語音容器一起使用?</b>
</summary>

我們目前只能傳遞一個模型 ID,無論是自定義語言模型還是自定義聲學模型。

**答案:** 決定*同時不支援聲學*和語言模型。 這將保持有效,直到創建統一標識符以減少 API 中斷。 因此,不幸的是,現在不支援這一點。

<br>
</details>

<details>
<summary>
<b>您能否從自訂語音到文字容器解釋這些錯誤?</b>
</summary>

**錯誤 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**答案 1:** 如果您正在培訓最新的自定義模型,我們目前不支援。 如果使用舊版本進行訓練,則應可以使用。 我們仍在努力支援最新版本。

從本質上講,自定義容器不支援基於 Halide 或 ONNX 的聲學模型(這是自定義培訓門戶中的預設值)。 這是由於自定義模型未加密,我們不想公開 ONNX 模型;但是,我們不希望公開 ONNX 模型。語言模型很好。 客戶需要顯式選擇較舊的非 ONNX 模型進行自定義培訓。 準確性不會受到影響。 型號大小可能更大(100 MB)。

> 支援型號 > 20190220 (v4.5 統一)

**錯誤 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**答案 2:** 您需要在請求中提供正確的語音名稱,該名稱區分大小寫。 請參閱完整的服務名稱映射。 您必須使用`en-US-JessaRUS`,`en-US-JessaNeural`因為 現在在文字到語音轉換的容器版本中不可用。

**錯誤 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**答案 3:** 您可以重新建立語音資源,而不是認知服務資源。


<br>
</details>

<details>
<summary>
<b>支援哪些 API 協定、REST 或 WS?</b>
</summary>

**答案:** 對於語音到文字和自訂語音到文本容器,我們目前僅支援基於 Websocket 的協定。 SDK 僅支援在 WS 中呼叫,而不支援 REST。 有一個計劃添加 REST 支援,但暫時沒有 ETA。 始終參考官方文件,請參閱[查詢預測終結點](speech-container-howto.md#query-the-containers-prediction-endpoint)。

<br>
</details>

<details>
<summary>
<b>語音容器是否支援 CentOS?</b>
</summary>

**答案:** CentOS 7 尚不受 Python SDK 支援,也不支援 Ubuntu 19.04。

Python 語音 SDK 套件適用於下列作業系統：
- **視窗**- x64 與 x86
- **Mac** - macOS X 版本 10.12 或更高版本
- **Linux** - Ubuntu 16.04, 烏本圖 18.04, Debian 9 在 x64

關於環境設定的詳細資訊,請參考[Python 平台設定](quickstarts/setup-platform.md?pivots=programming-language-python)。 目前,Ubuntu 18.04 是推薦的版本。

<br>
</details>

<details>
<summary>
<b>為什麼在嘗試調用 LUIS 預測終結點時會收到錯誤?</b>
</summary>

我在 IoT Edge 部署中使用 LUIS 容器,並嘗試從其他容器調用 LUIS 預測終結點。 LUIS 容器正在偵聽埠 5001,我使用的 URL 是:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

我得到的錯誤是:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

我在 LUIS 容器紀錄中看到請求,消息顯示:

```cmd
The request path /luis//predict" does not match a supported file type.
```

這代表什麼？ 我錯過了什麼? 我在此關注語音 SDK[的範例。](https://github.com/Azure-Samples/cognitive-services-speech-sdk) 場景是,我們直接從 PC 麥克風檢測音訊,並嘗試根據我們培訓的 LUIS 應用程式確定意圖。 我連結到的示例正是這樣做的。 它與 LUIS 基於雲的服務配合良好。 使用語音 SDK 似乎使我們不必對語音到文本 API 進行單獨的顯式調用,然後對 LUIS 進行第二次調用。

因此,我嘗試所做的只是從在雲中使用 LUIS 的方案切換到使用 LUIS 容器。 我無法想像,如果語音 SDK 適用於其中一個,它將不會適用於另一個。

**答案:** 不應對 LUIS 容器使用語音 SDK。 對於使用 LUIS 容器,應使用 LUIS SDK 或 LUIS REST API。 語音 SDK 應針對語音容器使用。

雲與容器不同。 雲可以由多個聚合容器(有時稱為微服務)組成。 因此,有一個LUIS容器,然後有一個語音容器 - 兩個單獨的容器。 "語音"容器僅執行語音。 LUIS 容器僅執行 LUIS。 在雲中,由於已知兩個容器都已部署,並且遠端用戶端進入雲、做語音、返回,然後再次訪問雲並做 LUIS,因此我們提供了一個功能,允許用戶端轉到語音、留在雲中、轉到 LUIS 然後返回用戶端。 因此,即使在這種情況下,語音 SDK 也會使用音訊轉到語音雲容器,然後語音雲容器與 LUIS 雲容器進行文本對話。 LUIS 容器沒有接受音訊的概念(LUIS 容器接受流式音訊沒有意義 - LUIS 是基於文本的服務)。 對於 Prem,我們無法確定我們的客戶已經部署了兩個容器,我們假定在客戶處所的容器之間進行協調,如果兩個容器都部署在 prem 上,因為它們對於客戶來說更加本地,那麼先去 SR、返回用戶端、讓客戶獲取該文本並轉到 LUIS 並不是一種負擔。

<br>
</details>

<details>
<summary>
<b>為什麼我們收到 macOS、語音容器和 Python SDK 的錯誤?</b>
</summary>

當我們傳送要轉錄的 *.wav*檔時,結果會傳回:

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

我們知道網路套接字設置正確。

**答案:** 如果是這種情況,請參閱[此 GitHub 問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)。 我們有一個解決方法,[建議在這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)。

碳在版本 1.8 上修復了此點。


<br>
</details>

<details>
<summary>
<b>語音容器終結點中有哪些差異?</b>
</summary>

您能否幫助填寫以下測試指標,包括要測試哪些功能以及如何測試 SDK 和 REST API? 特別是"交互"和"對話"的差異,我沒有看到從現有的文檔/示例。

| 端點                                                | 功能測試                                                   | SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | 合成文字(文字到語音)                                  |     | 是      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | 認知服務預聽寫 v1 Websocket 終結點        | 是 | 否       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | 認知服務上預交互式 v1 Websocket 終結點  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | 預處理工作階段 v1 Websocket 終結點上的認知服務 |     |          |

**答案:** 這是融合:
- 試著容器的聽寫終結點的人(我不確定他們是如何獲得該 URL 的)
- <sup>第</sup>1 個端終結點是容器中的終結點。
- 第<sup>1</sup>方終結點返回語音.片段消息`speech.hypothesis`,而不是<sup>第</sup>3 部分終結點返回的消息,用於聽寫終結點。
- 碳快速啟動所有使用`RecognizeOnce`(互動模式)
- 碳斷言,對於`speech.fragment`需要它們不以交互模式返回的消息。
- 在釋放中斷言火的碳(扼殺過程)。

解決方法是切換到在代碼中使用連續識別,或者(更快地)連接到容器中的互動式或連續終結點。
對於代碼,將終結點設置為<主機:埠>/語音/識別/互動式/認知服務/v1

有關各種模式,請參閱語音模式 - 請參閱以下內容:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

正確的修復程式是帶有 SDK 1.8,它有預支援(將選擇正確的終結點,所以我們不會比在線服務差)。 同時,還有一個連續識別的樣本,我們為什麼不指出呢?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>我應該為各種音訊檔使用哪種模式?</b>
</summary>

**答案:** 下面是[使用 Python 的快速入門](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python)。 您可以在文件網站上找到連結的其他語言。

只是為了澄清互動、對話和聽寫;這是指定我們的服務處理語音請求的特定方式的一種高級方法。 遺憾的是,對於 prem 容器,我們必須指定完整的 URI(因為它包括本地電腦),因此此資訊從抽象中洩漏。 我們正在與 SDK 團隊合作,使將來更加可用。

<br>
</details>

<details>
<summary>
<b>我們如何對交易/秒/核的粗略衡量基準?</b>
</summary>

**答案:** 以下是一些對現有模型的預期粗略數位(將在 GA 中發貨的模型中為更好的數位而改變):

- 對於檔,限制將在語音 SDK 中,為 2 倍。 前五秒的音訊不會受到限制。 解碼器能夠即時執行大約 3 倍的時間。 為此,單個識別的總體 CPU 使用率將接近 2 個內核。
- 對於麥克風,它將在 1 倍的即時。 單個識別的總體用法應約為 1 核。

這一切都可以從 Docker 日誌中驗證。 實際上,我們使用會話和短語/陳述統計資訊轉儲行,其中包括 RTF 編號。


<br>
</details>

<details>
<summary>
<b>將音訊檔拆分為夾頭以用於語音容器使用是否常見?</b>
</summary>

我目前的計劃是獲取一個現有的音訊檔,並將其拆分為 10 秒的塊,並通過容器發送這些塊。 這是可接受的方案嗎?  是否有更好的方法來處理更大的音訊檔與容器?

**答案:** 只需使用語音 SDK 並給它檔,它將做正確的事情。 為什麼需要分塊檔?


<br>
</details>

<details>
<summary>
<b>如何使多個容器在同一主機上運行?</b>
</summary>

文檔說要公開一個不同的埠,我這樣做,但 LUIS 容器仍在偵聽埠 5000?

**答案:** 試`-p <outside_unique_port>:5000`著 。 例如： `-p 5001:5000` 。


<br>
</details>

## <a name="technical-questions"></a>技術問題

<details>
<summary>
<b>如何獲取非批處理 API 來處理&lt;15 秒長的音訊?</b>
</summary>

**答:**`RecognizeOnce()`在互動式模式下,只能處理長達 15 秒的音訊,因為該模式適用於語音命令,其中語音預期較短。 如果使用`StartContinuousRecognition()`聽寫或對話,則沒有 15 秒的限制。


<br>
</details>

<details>
<summary>
<b>推薦的資源、CPU 和 RAM 是什麼;對於 50 個併發請求?</b>
</summary>

4 核 4 GB RAM 句柄有多少個併發請求? 例如,如果我們必須提供 50 個併發請求,建議多少個核心請求和 RAM?

**答案:** 即時,8與我們的最新`en-US`,所以我們建議使用更多的 docker 容器超過 6 個併發請求。 它變得瘋狂超過16個內核,並成為非統一的記憶體訪問(NUMA)節點敏感。 下表描述了每個語音容器的最小和建議的資源分配。

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

| 容器      | 最小值             | 建議         |
|----------------|---------------------|---------------------|
| 語音轉文字 | 2 核,2 GB 記憶體 | 4 核,4 GB 記憶體 |

# <a name="custom-speech-to-text"></a>[自訂語音到文字](#tab/cstt)

| 容器             | 最小值             | 建議         |
|-----------------------|---------------------|---------------------|
| 自訂語音到文字 | 2 核,2 GB 記憶體 | 4 核,4 GB 記憶體 |

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

| 容器      | 最小值             | 建議         |
|----------------|---------------------|---------------------|
| 文字轉換語音 | 1 個內核,2 GB 記憶體 | 2 核,3 GB 記憶體 |

# <a name="custom-text-to-speech"></a>[自訂文字到語音](#tab/ctts)

| 容器             | 最小值             | 建議         |
|-----------------------|---------------------|---------------------|
| 自訂文字到語音 | 1 個內核,2 GB 記憶體 | 2 核,3 GB 記憶體 |

***

- 每個內核必須至少為 2.6 GHz 或更快。
- 對於檔,限制將在語音 SDK 中,為 2x(前 5 秒音訊不受限制)。
- 解碼器能夠即時執行大約 2-3 倍。 為此,對於單個識別,總 CPU 使用率將接近兩個內核。 這就是為什麼我們不建議每個容器實例保留兩個以上活動連接。 極端的一面是把大約10解碼器在2倍的即時在8核機器像`DS13_V2`。 對於容器版本 1.3 和更高版本,您可以`DECODER_MAX_COUNT=20`嘗試設置 參數。
- 對於麥克風,它將是 1 倍的即時。 對於單個識別,總體用法應約為一個核心。

考慮您擁有的總音頻小時數。 如果數量很大,為了提高可靠性/可用性,我們建議在負載平衡器後面運行更多容器實例,無論是在單個框中還是在多個框中。 業務流程可以使用 Kubernetes (K8S) 和 Helm 完成,也可以使用 Docker 撰寫。

例如,為了處理 1000 小時/24 小時,我們嘗試設置 3-4 個 VM,每個 VM 包含 10 個實例/解碼器。

<br>
</details>

<details>
<summary>
<b>語音容器是否支援標點符號?</b>
</summary>

**答案:** 我們在前置容器中提供了大寫 (ITN)。 標點符號依賴於語言,某些語言(包括中文和日語)不支援。

對於現有容器,*我們確實有*隱式和基本標點符號支援,但默認情況下`off`是 預設的。 這意味著你可以得到你的例子中的`.`字元,但不能`。`得到 字元。 為了啟用此隱式邏輯,下面是如何使用我們的語音 SDK 在 Python 中執行此操作的範例(在其他語言中,它類似):

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
<b>為什麼在嘗試將 POST 資料到語音到文字容器時出現 404 個錯誤?</b>
</summary>

下面是一個範例 HTTP POST:

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

**答案:** 我們不支援在語音到文字容器中的 REST API,我們僅透過語音 SDK 支援 WebSocket。 始終參考官方文件,請參閱[查詢預測終結點](speech-container-howto.md#query-the-containers-prediction-endpoint)。

<br>
</details>

<details>
<summary>
<b>使用語音到文本服務時,為什麼會收到此錯誤?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**答案:** 當您以比「語音辨識」容器所獲取的更快速度饋送音訊時,通常會發生這種情況。 用戶端緩衝區已滿,並觸發取消。 您需要控制傳送音訊的併發和 RTF。

<br>
</details>

<details>
<summary>
<b>您能否從C++示例中解釋這些文本到語音容器錯誤?</b>
</summary>

**答案:** 如果容器版本早於 1.3,則應使用此代碼:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

較舊的容器沒有碳處理`FromHost`API 所需的終結點。 如果用於版本 1.3 的容器,則應使用此代碼:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

下面是使用`FromEndpoint`API 的範例:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 呼叫`SetSpeechSynthesisVoiceName`此函數是因為具有更新的文本到語音轉換引擎的容器需要語音名稱。

<br>
</details>

<details>
<summary>
<b>如何使用帶有語音容器的語音 SDK v1.7?</b>
</summary>

**答案:** 對於不同的用法,語音容器上有三個終結點,它們被定義為語音模式 - 如下所示:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

它們用於不同的目的,並且使用方式不同。

Python[範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- 對於具有自定義終結點的單識別(互動式模式)(即;`SpeechConfig`使用終結點參數),請參閱`speech_recognize_once_from_file_with_custom_endpoint_parameters()`。
- 對於連續識別(對話模式),只需修改以使用上述自定義終結點,請參閱`speech_recognize_continuous_from_file()`。
- 在此範例開啟聽寫(只在您真正需要時),在建立`speech_config`後,請新增代碼`speech_config.enable_dictation()`。

在 C# 中啟用聽寫`SpeechConfig.EnableDictation()`,請呼叫函數。

### <a name="fromendpoint-apis"></a>`FromEndpoint`Api
| Language | API 詳細資料 |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 當前不支援,也不計劃。 |

<br>
</details>

<details>
<summary>
<b>如何使用帶有語音容器的語音 SDK v1.8?</b>
</summary>

**答案:** 有一個新的`FromHost`API。 這不會替換或修改任何現有的 API。 它只是添加了使用自定義主機創建語音配置的替代方法。

### <a name="fromhost-apis"></a>`FromHost`Api

| Language | API 詳細資料 |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 目前不支援 |

> 參數:主機(必需)、訂閱密鑰(可選,如果無需使用服務即可)。

主機格式是`protocol://hostname:port`選擇性的`:port`(見下文):
- 如果容器在本地端執行,則主機`localhost`名為 。
- 如果容器在遠端伺服器上運行,請使用該伺服器的主機名或 IPv4 位址。

語音到文字的主機參數範例:
- `ws://localhost:5000`- 使用連接埠 5000 與本地的非安全連線
- `ws://some.host.com:5000`- 與遠端伺服器上執行的容器的非安全連線

Python 範例來自上面,`host`但使用參數`endpoint`而不是 :

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [認知服務容器](speech-container-howto.md)
