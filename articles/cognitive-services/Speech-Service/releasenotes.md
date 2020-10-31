---
title: 版本資訊-語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務功能發行、改進、bug 修正和已知問題的執行記錄。
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: eb285f7ef536ac19d0ea7328a4428f33a47cd368
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129621"
---
# <a name="speech-service-release-notes"></a>語音服務版本資訊

## <a name="speech-sdk-1140-2020-october-release"></a>語音 SDK 1.14.0： 2020-10 月版本

**注意** ： Windows 上的語音 SDK 取決於 Visual Studio 2015、2017和2019的共用 Microsoft Visual C++ 可轉散發套件。 在 [這裡](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)下載。

**語音 CLI (也稱為 SPX)**<br>
SPX 是在不需要撰寫程式碼的情況下，使用 Azure 語音服務的命令列介面。 請在 [這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics)下載最新版本。
- `spx csr dataset upload --kind audio|language|acoustic` –從本機資料建立資料集，而不只是從 Url。
- `spx csr evaluation create|status|list|update|delete` –比較新的模型與基準事實/其他模型。
- `spx * list` –支援非分頁體驗 (不需要--top X--skip X) 。
- `spx * --http header A=B` – (針對 Office 自訂驗證) 新增自訂標頭。 
- `spx help` –改良的文字和反向刻度文字色彩 (藍色) 。

**新功能**
- **Linux** ：已新增 Debian 10 和 UBUNTU 20.04 LTS 的支援。
- **Python/目標-C** ：已新增對 API 的支援 `KeywordRecognizer` 。 檔會在 [此](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-basics)。
- **C + +/JAVA/C #** ：已新增透過設定任何索引 `HttpHeader` 鍵/值的支援 `ServicePropertyChannel::HttpHeader` 。
- **JavaScript** ：已新增 API 的支援 `ConversationTranscriber` 。 請參閱 [此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-conversation-transcription?pivots=programming-language-javascript)的檔。 
- **C + +/c #** ：已新增 `AudioDataStream FromFile` 要讀取 (方法。WAV 檔) [這裡 (c + +) ](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audiodatastream) 和 [這裡 (c # ) ](
https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream.fromfile)。
-  **C + +/c #/JAVA/Python/Objective-C/Swift** ：已新增 `stopSpeakingAsync()` 方法來停止文字轉換語音合成。 請參閱此處的參考檔 [ (c + +) ](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace)，此處 [ (c # ) ](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet)，此處 (了 [JAVA) ](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech?view=azure-java-stable)，此處 (了 [Python) ](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech?view=azure-python)， ([目標-C/Swift ](https://docs.microsoft.com/objectivec/cognitive-services/speech/)) 。
- **C #、c + +、JAVA** ：將函式新增 `FromDialogServiceConnector()` 至 `Connection` 類別，可用於監視的連接和中斷連接事件 `DialogServiceConnector` 。 請參閱此處的參考檔 [ (c # ) ](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection?view=azure-dotnet)， [此處 (c + +) ](https://docs.microsoft.com/cpp/cognitive-services/speech/connection)， [此處 (JAVA) ](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.connection?view=azure-java-stable)。
- **C + +/c #/JAVA/Python/Objective-C/Swift** ：新增了發音評量的支援，它會評估語音發音，並為說話者的精確度和流暢度提供說話者的意見反應。 請參閱 [此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-pronunciation-assessment)的檔。

**Bug 修正**
- **全部** ：修正在 `SetServiceProperty` 某些特殊字元中忽略值的固定1.13 回歸。
- **C #** ：已修正 Visual Studio 2019 上的 Windows 主控台範例找不到原生 dll。
- **C #** ：如果將資料流程當成輸入使用，則修正了記憶體管理的損毀 `KeywordRecognizer` 。
- **ObjectiveC/Swift** ：如果使用串流作為辨識器輸入，則修正了記憶體管理的損毀。
- **Windows** ：在 UWP 上修正了 BT HFP/A2DP 的共同存在問題。
- **JavaScript** ：已修正會話識別碼的對應，以改善內部偵錯工具/服務相互關聯的記錄和協助。
- **JavaScript** ：新增 `DialogServiceConnector` `ListenOnce` 了在第一次呼叫之後停用呼叫的修正。
- **JavaScript** ：已修正結果輸出只是「簡單」的問題。
- **JavaScript** ：已修正 MacOS 上 Safari 中的連續辨識問題。
- **JavaScript** ：高要求輸送量案例的 CPU 負載降低。
- **JavaScript** ：允許存取語音設定檔註冊結果的詳細資料。
- **JavaScript** ：已在中新增連續辨識的修正程式 `IntentRecognizer` 。
- **C + +/c #/JAVA/Python/Swift/ObjectiveC** ：已修正中 australiaeast 和 brazilsouth 的不正確 url `IntentRecognizer` 。
- **C + +/c #** ： `VoiceProfileType` 在建立物件時加入做為引數 `VoiceProfile` 。
- **C + +/c #/JAVA/Python/Swift/ObjectiveC** ： `SPX_INVALID_ARG` 嘗試從指定位置讀取時，可能會有潛在的修正 `AudioDataStream` 。
- **IOS** ：透過 Unity 的語音辨識來修正損毀

**範例**
- **ObjectiveC** ：已 [在此](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples)新增關鍵字辨識的範例。
- **C #/JavaScript** ：已 [在這裡新增 (c # )](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) 的對話轉譯快速入門，並 [ (JavaScript)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription)。
- **C + +/c #/JAVA/Python/Swift/ObjectiveC** ：已 [在這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)新增發音評量的範例
- **Xamarin** ： [這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin)已更新最新 Visual Studio 範本的快速入門。

**已知問題**
- HoloLens 2 和 Android 4.4 (KitKat) 中預設不支援 DigiCert 全域根目錄 G2 憑證，必須將其新增至系統，才能讓語音 SDK 正常運作。 憑證會在不久的將來新增至 HoloLens 2 OS 映射。 Android 4.4 客戶必須將更新的憑證新增至系統。

**Covid-19-19 縮減測試：** 由於在過去幾周的遠端工作，我們無法像往常一樣執行手動驗證測試。 我們尚未進行任何變更，而且我們的自動化測試全都通過了。 在不太可能發生的情況下，請在 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)上讓我們知道。<br>
保持狀況良好！


## <a name="text-to-speech-2020-august-release"></a>文字轉換語音 2020-8 月版本

### <a name="new-features"></a>新功能

* 類 **神經 TTS：的 `en-US` 新說話樣式Aria 語音** 。 AriaNeural 在閱讀新聞時可能會像新聞 caster 一樣。 「Newscast 型」樣式聽起來更嚴重，而「newscast」的樣式比較寬鬆且非正式。 瞭解 [如何在 SSML 中使用說話樣式](speech-synthesis-markup.md)。

* **自訂語音：會發行新的功能，以自動檢查定型資料品質** 。 當您上傳資料時，系統會檢查音訊和文字記錄資料的各個層面，並自動修正或篩選問題，以改善語音模型的品質。 這涵蓋音訊的音量、雜訊等級、語音的發音精確度、具有正規化文字的語音對齊、音訊的無回應，以及音訊和腳本的格式。 

* 建立 **音訊內容：一組新功能，可讓您更強大的語音微調和音訊管理功能** 。

    * 發音：發音微調功能已更新為最新的音素集。 您可以從程式庫挑選正確的音素元素，並精簡您所選取文字的發音。 

    * 下載：音訊「下載」/「匯出」功能已增強，可支援依段落產生音訊。 您可以在相同的檔案/SSML 中編輯內容，同時產生多個音訊輸出。 [下載] 的檔案結構也會進行調整。 現在，您可以輕鬆地取得一個資料夾中的所有音訊檔案。 

    * 工作狀態：已改善多檔案匯出體驗。 當您在過去匯出多個檔案時，如果其中一個檔案失敗，則整個工作將會失敗。 但現在，所有其他檔案都將成功匯出。 工作報表會以更詳細的結構化資訊來擴充。 您可以立即使用報表檢查所有失敗檔案和句子的記錄。 

    * SSML 檔：連結到 SSML 檔，可協助您檢查如何使用所有微調功能的規則。

* **語音清單 API 已更新為包含方便使用的顯示名稱和類神經語音支援的語音樣式** 。

### <a name="general-tts-voice-quality-improvements"></a>一般 TTS 語音品質改進

* 減少 56% ) 減少的 (錯誤的文字層級發音錯誤% `ru-RU` ，並 `sv-SE` 減少49% 的 (錯誤 ) 

* 改進了 `en-US` 40% 的神經語音 polyphony 字詞閱讀。 Polyphony 單字的範例包括「讀取」、「即時」、「內容」、「記錄」、「物件」等等。 

* 改進中的問題 naturalness `fr-FR` 。 MOS (Mean 意見分數) 增益： + 0.28

* 已更新下列語音的 vocoders，並提供精確度改善和整體效能加速，40%。

    | 地區設定 | 語音 |
    |---|---|    
    | `en-GB` | 米婭 |
    | `es-MX` | Dalia |
    | `fr-CA` | 西爾維 |
    | `fr-FR` | 鄧尼斯 |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>錯誤修正

* 使用音訊內容建立工具修正了許多 bug 
    * 已修正自動重新整理的問題。 
    * 修正了 zh 中的語音樣式在南東亞地區的-CN 問題。
    * 修正穩定性問題，包括具有「中斷」標記的匯出錯誤，以及標點符號中的錯誤。

## <a name="new-speech-to-text-locales-2020-august-release"></a>新的語音轉換文字地區設定： 2020-8 月版本
語音轉換文字已于8月發行26個新的地區設定：2個歐洲語言， `cs-CZ` 以及 `hu-HU` 5 個英文地區設定和19個西班牙文的地區設定，涵蓋大部分的南美洲國家/地區。 以下是新的地區設定清單。 請參閱 [這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support)的完整語言清單。

| Locale  | Language                          |
|---------|-----------------------------------|
| `cs-CZ` | 捷克文 (捷克共和國)            | 
| `en-HK` | 英文 (香港特別行政區)                | 
| `en-IE` | 英文 (愛爾蘭)                 | 
| `en-PH` | 英文 (菲律賓)             | 
| `en-SG` | 英文 (新加坡)               | 
| `en-ZA` | 英文 (南非)            | 
| `es-AR` | 西班牙文 (阿根廷)               | 
| `es-BO` | 西班牙文 (玻利維亞)                 | 
| `es-CL` | 西班牙文 (智利)                   | 
| `es-CO` | 西班牙文 (哥倫比亞)                | 
| `es-CR` | 西班牙文 (哥斯大黎加)              | 
| `es-CU` | 西班牙文 (古巴)                     | 
| `es-DO` | 西班牙文 (多明尼加)      | 
| `es-EC` | 西班牙文 (厄瓜多)                 | 
| `es-GT` | 西班牙文 (瓜地馬拉)               | 
| `es-HN` | 西班牙文 (宏都拉斯)                | 
| `es-NI` | 西班牙文 (尼加拉瓜)               | 
| `es-PA` | 西班牙文 (巴拿馬)                  | 
| `es-PE` | 西班牙文 (秘魯)                    | 
| `es-PR` | 西班牙文 (波多黎各)             | 
| `es-PY` | 西班牙文 (巴拉圭)                | 
| `es-SV` | 西班牙文 (薩爾瓦多)             | 
| `es-US` | 美國西班牙文 (USA)                      | 
| `es-UY` | 西班牙文 (烏拉圭)                 | 
| `es-VE` | 西班牙文 (委內瑞拉)               | 
| `hu-HU` | 匈牙利文 (匈牙利)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>語音 SDK 1.13.0： 2020-七月版本

**注意** ： Windows 上的語音 SDK 取決於 Visual Studio 2015、2017和2019的共用 Microsoft Visual C++ 可轉散發套件。 請從[這裡](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)下載並安裝。

**新功能**
- **C #** ：已新增非同步對話轉譯的支援。 請參閱 [此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-async-conversation-transcription)的檔。  
- **JavaScript** ：已新增 [瀏覽器](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) 和 [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition)的說話者辨識支援。
- **JavaScript** ：已新增自動語言偵測/語言識別項的支援。 請參閱 [此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-javascript)的檔。
- **目標-C** ：新增對 [多裝置對話](https://docs.microsoft.com/azure/cognitive-services/speech-service/multi-device-conversation) 和 [對話](https://docs.microsoft.com/azure/cognitive-services/speech-service/conversation-transcription)轉譯的支援。 
- **Python** ：為 Windows 和 Linux 上的 python 新增了壓縮的音訊支援。 請參閱 [此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)的檔。 

**Bug 修正**
- **全部** ：已修正導致 KeywordRecognizer 在辨識之後不會向前移動資料流程的問題。
- **全部** ：已修正導致從 KeywordRecognitionResult 取得的資料流程不包含關鍵字的問題。
- **全部** ：已修正 >sendmessageasync 在使用者完成等候時，不會在網路上真正傳送訊息的問題。
- **全部** ：已修正當使用者呼叫 VoiceProfileClient：： SpeakerRecEnrollProfileAsync 方法多次，且未等候呼叫完成時，說話者辨識 api 中的當機。
- **全部** ：已修正在 VoiceProfileClient 和 SpeakerRecognizer 類別中啟用檔案記錄。
- **JavaScript** ：修正了最小化瀏覽器時的節流 [問題](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) 。
- **JavaScript** ：已修正資料流程上記憶體流失的 [問題](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) 。
- **JavaScript** ：已針對來自 NODEJS 的 OCSP 回應新增快取。
- **JAVA** ：已修正導致 BigInteger 欄位永遠傳回0的問題。
- **ios** ：已修正在 iOS App Store 中發佈以語音 SDK 為基礎之應用程式的 [問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) 。

**範例**
- **C + +** ： [在此](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)新增說話者辨識的範例程式碼。

**Covid-19-19 縮減測試：** 由於在過去幾周的遠端工作，我們無法像往常一樣執行手動驗證測試。 我們尚未進行任何變更，而且我們的自動化測試全都通過了。 在不太可能發生的情況下，請在 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)上讓我們知道。<br>
保持狀況良好！

## <a name="text-to-speech-2020-july-release"></a>文字轉換語音 2020-7 月版本

### <a name="new-features"></a>新功能

* **神經 tts、15個新的神經語音** ：新增至類神經 tts 組合的新語音會以 `ar-EG` 阿拉伯文 (埃及) 、 `ar-SA` 阿拉伯文 (沙烏地阿拉伯) 、Alba 于加泰羅尼亞文 (西班牙) 、丹麥文中的 Christel (丹麥) 、Neerja （英文） (`ca-ES` `da-DK` `es-IN` 印度) 、芬蘭文中的 Noora `fi-FI` (芬蘭) 、 `hi-IN` 印度印度 (印度) 、荷蘭文 (荷蘭 Colette、Zofia in 波蘭文) 波蘭 (、Fernanda （葡萄牙文）) 葡萄牙 (、Dariya （俄文）) 葡萄牙 (、Hillevi in 俄文) 俄文 (、Achara In 俄文) 俄文 (、繁體中文 `nl-NL` `pl-PL` `pt-PT` `ru-RU` `sv-SE` `th-TH` `zh-HK`) 和 HiuGaai （ `zh-TW` 中文 (繁體中文廣東話) 。 檢查所有 [支援的語言](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)。  

* **自訂語音，利用訓練流程簡化語音測試以簡化使用者體驗** ：利用新的測試功能，每個聲音都會自動以針對每個語言優化的預先定義測試集進行測試，以涵蓋一般和語音助理案例。 這些測試集會謹慎選取並經過測試，以包含語言中的一般使用案例和音素。 除此之外，使用者仍可選擇在訓練模型時上傳自己的測試腳本。

* **建立音訊內容：一組新功能，可讓您更強大的語音微調和音訊管理功能**

    * `Pitch`、 `rate` 和 `volume` 已增強，可支援以預先定義的值（例如慢、中和快速）進行微調。 現在，使用者可以直接挑選「常數」值進行音訊編輯。

    ![音訊微調](media/release-notes/audio-tuning.png)

    * 使用者現在可以檢查 `Audio history` 其工作檔案的。 有了這項功能，使用者就可以輕鬆地追蹤與工作檔案相關的所有產生的音訊。 他們可以檢查歷程記錄版本，並在同時調整時比較品質。 

    ![音訊歷程記錄](media/release-notes/audio-history.png)

    * `Clear`這項功能現在更有彈性。 使用者可以清除特定的調整參數，同時保留所選內容可用的其他參數。  

    * [登陸頁面](https://speech.microsoft.com/audiocontentcreation)上已新增教學課程影片，可協助使用者快速開始使用 TTS 語音微調和音訊管理。 

### <a name="general-tts-voice-quality-improvements"></a>一般 TTS 語音品質改進

* 改善的 TTS vocoder，以提供更高的精確度和較低的延遲。

    * 已將 Elsa 更新 `it-IT` 為新的 vocoder，以達成 + 0.464 CMOS (比較平均的意見分數) 獲得語音品質、40% 合成速度更快，且第一次位元組延遲減少30%。 
    * `zh-CN`使用適用于一般網域的 + 0148 CMOS 增益，將 Xiaoxiao 更新為新的 vocoder，適用于 newscast 樣式的 + 0.348 和 lyrical 樣式的 + 0.195。 

* 更新 `de-DE` 和 `ja-JP` 語音模型，使 TTS 輸出更自然。
    
    * 以 `de-DE` 最新的韻律模型方法更新了 Katja，MOS (Mean 意見分數) 增益是 + 0.13。 
    * 以 `ja-JP` 新的音調輔韻律模型更新了 Nanami，MOS (Mean 意見分數) 增益是 + 0.19;  

* 改進了五種語言的單字層級發音精確度。

    | 語言 | 發音錯誤減少 |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>錯誤修正

* 貨幣讀取
    * 修正了和的貨幣讀取問題 `es-ES``es-MX`
     
    | 語言 | 輸入 | 改進後讀出 |
    |---|---|---|
    | `es-MX` | $1.58 | un 比索 cincuenta y ocho centavos |
    | `es-ES` | $1.58 | un dólar cincuenta y ocho centavos |

    * 支援下列地區設定中的負數貨幣 (例如 "-€325" ) ： `en-US` 、 `en-GB` 、 `fr-FR` 、 `it-IT` 、 `en-AU` 、 `en-CA` 。

* 已改善在中讀取的位址 `pt-PT` 。
* 已修正 Natasha (`en-AU`) 和 Libby (在 " `en-UK` for" 和 "四" 這個字上) 發音問題。  
* 已修正音訊內容建立工具的錯誤
    * 第二個段落之後的額外和非預期的暫停都是固定的。  
    * 「不中斷」功能會從回歸 bug 中新增回來。 
    * 語音 Studio 的隨機重新整理問題已修正。  

### <a name="samplessdk"></a>範例/SDK

* JavaScript：修正 Firefox 中的播放問題，以及 macOS 和 iOS 上的 Safari。 

## <a name="speech-sdk-1121-2020-june-release"></a>語音 SDK 1.12.1： 2020-六月版本
**語音 CLI (也稱為 SPX)**
-   新增 CLI 內的說明搜尋功能：
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   已更新為使用新部署的 v3.0 批次和自訂語音 Api：
    -   `spx help batch examples`
    -   `spx help csr examples`

**新功能**
-   **C \# ，c + +** ：說話者辨識預覽：這項功能可讓說話者識別 (說話？ ) 和說話者驗證 (是他們宣稱的說話者？ ) 。 首先 [，請閱讀](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/speaker-recognition-overview)「 [說話者辨識基本」文章](https://docs.microsoft.com/azure/cognitive-services/speech-service/speaker-recognition-basics)或 [API 參考](https://docs.microsoft.com/rest/api/speakerrecognition/)檔。

**Bug 修正**
-   **C \# ，c + +** ：固定麥克風錄製在1.12 的說話者辨識中無法運作。
-   **JavaScript** ：針對 Firefox 中的文字轉換語音，以及在 MacOS 和 iOS 上的 Safari 進行修正。
-   修正在使用八個通道的資料流程時，對交談轉譯的 Windows application verifier 存取違規損毀。
-   修正多重裝置交談轉譯上的 Windows application verifier 存取違規損毀。

**範例**
-   **C #** ：說話者辨識的程式 [代碼範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) 。
-   **C + +** ：說話者辨識的程式 [代碼範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) 。
-   **JAVA** ： Android 上意圖辨識的程式 [代碼範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) 。 

**Covid-19-19 縮減測試：** 由於在過去幾周的遠端工作，我們無法像往常一樣執行手動驗證測試。 我們尚未進行任何變更，而且我們的自動化測試全都通過了。 在不太可能發生的情況下，請在 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)上讓我們知道。<br>
保持狀況良好！


## <a name="speech-sdk-1120-2020-may-release"></a>語音 SDK 1.12.0： 2020-可能發行
**語音 CLI (也會以 SPX 的形式來得知)**
- **SPX** 是一種新的命令列工具，可讓您從命令列執行辨識、合成、轉譯、批次轉譯和自訂語音管理。 您可以使用它來測試語音服務，或編寫您需要執行之語音服務工作的腳本。 下載工具並閱讀 [此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-overview)的檔。

**新功能**
- **Go** ：適用于 [語音辨識](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) 的新 go 語言支援和 [自訂語音助理](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go)。 在 [這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-go)設定您的開發環境。 如需範例程式碼，請參閱下面的範例一節。 
- **JavaScript** ：已新增文字轉換語音的瀏覽器支援。 請參閱 [此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech-audio-file?pivots=programming-language-JavaScript)的檔。
- **C + +、c #、JAVA** ： `KeywordRecognizer` Windows、Android、Linux & iOS 平臺上支援的新物件和 api。 請參閱 [此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-overview)的檔。 如需範例程式碼，請參閱下面的範例一節。 
- **JAVA** ：已新增多裝置與翻譯支援的交談。 請參閱 [此處](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.transcription)的參考檔。

**改善 & 優化**
- **JavaScript** ：優化的瀏覽器麥克風實行改善語音辨識精確度。
- **JAVA** ：使用直接 JNI 執行而不 SWIG 重構的系結。 針對 Windows、Android、Linux 和 Mac 使用的所有 JAVA 封裝，這項變更會減少10倍的系結大小，並簡化語音 SDK JAVA 執行的進一步開發。
- **Linux** ：已更新包含最新 RHEL 7 特定附注的支援 [檔](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux) 。
- 改善連接邏輯，以在發生服務和網路錯誤時嘗試連接多次。
- 已更新 [Portal.azure.com](https://portal.azure.com) 語音快速入門頁面，協助開發人員在 azure 語音旅程圖中採取下一個步驟。

**Bug 修正**
- **C #、JAVA** ：已修正在 Linux ARM 上載入 SDK 程式庫 (32 位和64位) 的 [問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) 。
- **C #** ：已修正 TranslationRecognizer、IntentRecognizer 和 Connection 物件原生控制碼的明確處置。
- **C #** ：固定 ConversationTranscriber 物件的音訊輸入存留期管理。
- 修正了 `IntentRecognizer` 從簡單片語辨識意圖時，未正確設定結果原因的問題。
- 修正 `SpeechRecognitionEventArgs` 未正確設定結果位移的問題。
- 修正了 SDK 在開啟 websocket 連線之前嘗試傳送網路訊息的競爭情形。 在加入參與者時，是可重現的 `TranslationRecognizer` 。
- 修正了關鍵字辨識器引擎中的記憶體流失。

**範例**
- **Go** ：已新增 [語音辨識](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) 和 [自訂語音助理](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go)的快速入門。 [在這裡](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples)尋找範例程式碼。 
- **JavaScript** ：新增 [文字轉換語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech?pivots=programming-language-javascript)、 [轉譯](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-speech-translation?tabs=script&pivots=programming-language-csharp)和 [意圖辨識](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition?pivots=programming-language-javascript)的快速入門。
- [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)和[JAVA](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android) 的關鍵字辨識範例。  

**Covid-19-19 縮減測試：** 由於在過去幾周的遠端工作，我們無法像往常一樣執行手動驗證測試。 我們尚未進行任何變更，而且我們的自動化測試全都通過了。 如果我們錯過了某個內容，請在 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)上讓我們知道。<br>
保持狀況良好！

## <a name="speech-sdk-1110-2020-march-release"></a>語音 SDK 1.11.0： 2020-三月版
**新功能**
- Linux：已新增 Red Hat Enterprise Linux (RHEL) /Centos 作為基礎 7 x64 的支援，以及如何設定 system for Speech SDK 的 [指示](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) 。
- Linux：已新增 Linux ARM32 和 ARM64 上的 .NET Core c # 支援。 您可以在[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux)深入了解. 
- C #、c + +： `UtteranceId` 在中加入 `ConversationTranscriptionResult` ，在所有中繼和最終語音辨識結果中都是一致的識別碼。 [C #](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet&preserve-view=true)、 [c + +](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)的詳細資料。
- Python：已新增的支援 `Language ID` 。 請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)存放庫中的 speech_sample .py。
- Windows：已針對所有 win32 主控台應用程式，在 Windows 平臺上新增壓縮的音訊輸入格式支援。 詳細 [資訊](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)請參閱。 
- JavaScript：支援 NodeJS 中的語音合成 (文字轉換語音) 。 [在此](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech)深入了解。 
- JavaScript：加入新的 API，以啟用所有傳送和接收訊息的檢查。 [在此](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript)深入了解。 
        
**Bug 修正**
- C #、c + +：已修正問題，因此 `SendMessageAsync` 現在會將二進位訊息傳送為二進位類型。 [C #](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_)、 [c + +](https://docs.microsoft.com/cpp/cognitive-services/speech/connection)的詳細資料。
- C #、c + +：已修正使用 `Connection MessageReceived` 事件在 `Recognizer` 物件之前處置時可能造成損毀的問題 `Connection` 。 [C #](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet&preserve-view=true)、 [c + +](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived)的詳細資料。
- Android：來自麥克風的音訊緩衝區大小從800ms 減少為100毫秒，以改善延遲。
- Android：已修正 Android Studio 中 x86 Android 模擬器的 [問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) 。
- JavaScript：已新增使用 API 的中國區域支援 `fromSubscription` 。 詳細 [資訊](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#fromsubscription-string--string-)請參閱。 
- JavaScript：針對 NodeJS 的連接失敗，新增更多錯誤資訊。
        
**範例**
- Unity：意圖辨識公用範例是固定的，其中 LUIS json 匯入失敗。 詳細 [資訊](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)請參閱。
- Python：為新增的範例 `Language ID` 。 詳細 [資訊](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)請參閱。
    
**Covid19 縮減測試：** 由於在過去幾周的遠端工作，我們無法像往常一樣執行手動裝置驗證測試。 例如，我們無法測試 Linux、iOS 和 macOS 上的麥克風輸入和說話者輸出。 我們尚未進行任何變更，因此我們認為這些平臺上的所有內容都不會有任何改變，而且所有的自動化測試都能通過。 在不太可能發生的情況下，請在 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)上讓我們知道。<br> 感謝您的持續支援。 如往常一樣，請在 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) 或 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731)張貼問題或意見反應。<br>
保持狀況良好！

## <a name="speech-sdk-1100-2020-february-release"></a>語音 SDK 1.10.0： 2020-二月版

**新功能**

 - 已新增 Python 套件以支援新3.8 版的 Python。
 - Red Hat Enterprise Linux (RHEL) /Centos 作為基礎 8 x64 支援 (c + +、c #、JAVA、Python) 。
   > [!NOTE] 
   > 客戶必須根據 [這些指示](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)來設定 OpenSSL。
 - 適用于 Debian 和 Ubuntu 的 Linux ARM32 支援。
 - DialogServiceConnector 現在支援 BotFrameworkConfig 上的選擇性 "bot ID" 參數。 此參數可讓您使用多個 Direct Line 語音 bot 搭配單一 Azure 語音資源。 如果未指定參數，則會使用 Direct Line 語音通道設定頁面所決定的預設 bot () 。
 - DialogServiceConnector 現在有 SpeechActivityTemplate 屬性。 Direct Line 的語音會使用此 JSON 字串的內容，在所有到達 Direct Line 語音 bot 的活動中預先填入各種支援的欄位，包括自動產生的活動，以回應像是語音辨識的事件。
 - TTS 現在會使用訂用帳戶金鑰進行驗證，在建立合成器之後，減少第一個合成結果的第一個位元組延遲。
 - 更新了19個地區設定的語音辨識模型，可減少 18.6% (es 的平均字組誤差率 es-MX、fr-CA、fr-fr、it-IT、ja-jp、ko-KR、pt-BR、zh-CN、zh-HK、nb-NO、fi、ru-RU、pl-PL、、zh、、th、pt、tr、tr) 。 新模型會在多個網域中帶來顯著的改進，包括聽寫、Call-Center 轉譯和影片索引編制案例。

**Bug 修正**

 - 修正對話 Transcriber 在 JAVA Api 中未適當等候的 bug 
 - 適用于 Xamarin GitHub 的 Android x86 模擬器修正 [問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - 新增遺漏的 (Get |將) 屬性方法設定為 AudioConfig
 - 修正連接失敗時無法停止 audioDataStream 的 TTS bug
 - 使用不含區域的端點會造成對話翻譯工具的 USP 失敗
 - 在通用 Windows 應用程式中產生識別碼現在會使用適當的唯一 GUID 演算法;它先前和不慎預設為 stub 的執行，通常會產生與大型互動集的衝突。
 
 **範例**
 
 - 使用 Speech SDK 搭配[unity 麥克風和推送模式串流](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)的 Unity 範例

**其他變更**

 - [適用于 Linux 的 OpenSSL 設定檔已更新](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>語音 SDK 1.9.0： 2020-一月發行

**新功能**

- 多裝置對話：將多個裝置連接到相同的語音或文字型交談，並選擇性地轉譯它們之間傳送的訊息。 若要深入[瞭解，請參閱本文。](multi-device-conversation.md) 
- 已新增 aar 套件的關鍵字辨識支援，並新增了 x86 和 x64 類別的支援。 
- 目標-C： `SendMessage` 和 `SetMessageProperty` 方法新增至 `Connection` 物件。 請參閱 [此處](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)的檔。
- TTS c + + api 現在支援 `std::wstring` 合成文字輸入，在將 wstring 傳遞給 SDK 之前，不需要先將它轉換成字串。 請參閱[這裡](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)的詳細資料。 
- C #： [語言識別項](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) 和 [來源語言](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) 設定現已可供使用。
- JavaScript：已將功能新增至 `Connection` 物件，以傳遞來自語音服務的自訂訊息做為回呼 `receivedServiceMessage` 。
- JavaScript：已新增支援， `FromHost API` 以簡化與內部內部部署容器和主權雲端的搭配使用。 請參閱 [此處](speech-container-howto.md)的檔。
- JavaScript：我們現在接受 `NODE_TLS_REJECT_UNAUTHORIZED` [orgads](https://github.com/orgads)的貢獻。 請參閱[這裡](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)的詳細資料。

**重大變更**

- `OpenSSL` 已更新為1.1.1 版，並以靜態方式連結至適用于 Linux 的語音 SDK 核心程式庫。 如果您的收件匣尚未 `OpenSSL` 安裝至系統中的目錄，這可能會導致中斷 `/usr/lib/ssl` 。 請參閱語音 SDK 檔下的 [檔](how-to-configure-openssl-linux.md) ，以解決此問題。
- 我們已將 c # 傳回的資料類型從變更為，以便 `WordLevelTimingResult.Offset` `int` `long` `WordLevelTimingResults` 在語音資料超過2分鐘時存取。
- `PushAudioInputStream` 現在，您 `PullAudioInputStream` 可以視需要將 wav 標頭資訊傳送到語音服務，並在 `AudioStreamFormat` 建立時選擇性地指定。 客戶現在必須使用 [支援的音訊輸入格式](how-to-use-audio-input-streams.md)。 任何其他格式都會取得次佳的辨識結果，或可能造成其他問題。 

**Bug 修正**

- 請參閱 `OpenSSL` 上方重大變更下的更新。 我們已修正在 Linux 和 JAVA 的高負載) 下， (鎖定爭用的間歇性損毀和效能問題。 
- JAVA：對高平行存取案例中的物件關閉進行改善。
- 已重建 NuGet 套件。 我們移除了 lib 資料夾的三個複本 `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` ，讓 NuGet 套件更小且更快速地下載，而且我們新增了編譯一些 c + + 原生應用程式所需的標頭。
- 修正 [這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)的快速入門範例。 在 Linux、macOS、Windows 上，這些都不會顯示「找不到麥克風」例外狀況。
- 修正了在特定程式碼路徑（例如 [此範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)）上，具有長語音辨識結果的 SDK 損毀結果。
- 修正了 Azure Web 應用程式環境中的 SDK 部署錯誤，以解決 [此客戶的問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)。
- 修正使用多重 `<voice>` 標記或 `<audio>` 標記解決 [此客戶問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)時的 TTS 錯誤。 
- 修正 SDK 從暫停復原時的 TTS 401 錯誤。
- JavaScript：由於 [euirim](https://github.com/euirim)的貢獻，已修正音訊資料的迴圈匯入。 
- JavaScript：新增了設定服務屬性的支援，如1.7 中所新增。
- JavaScript：修正連接錯誤可能導致連續失敗的 websocket 重新連線嘗試的問題。

**範例**

- [在這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)新增了適用于 Android 的關鍵字辨識範例。
- 在 [這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)新增了伺服器案例的 TTS 範例。
- [在這裡](quickstarts/multi-device-conversation.md)新增了適用于 c # 和 c + + 的多裝置對話快速入門。

**其他變更**

- Android 上的優化 SDK 核心程式庫大小。
- 1.9.0 和更新版本中的 SDK `int` 在 `string` 對話 Transcriber 的 [語音簽名版本] 欄位中支援和類型。

## <a name="speech-sdk-180-2019-november-release"></a>語音 SDK 1.8.0： 2019-11 月版

**新功能**

- 新增 `FromHost()` API，以簡化與內部內部部署容器和主權雲端的搭配使用。
- 在 JAVA 和 c + + 中新增語音辨識 (的自動來源語言偵測) 
- 已新增 `SourceLanguageConfig` 語音辨識的物件，用來指定 JAVA 和 c + + (的預期來來源語言) 
- `KeywordRecognizer`透過 NuGet 和 Unity 套件在 Windows (UWP) 、Android 和 iOS 上新增支援
- 新增遠端對話 JAVA API，以非同步批次進行對話轉譯。

**重大變更**

- 對話 Transcriber 功能已移至命名空間下 `Microsoft.CognitiveServices.Speech.Transcription` 。
- 對話 Transcriber 方法的元件會移至新的 `Conversation` 類別。
- 已中斷32位 (ARMv7 和 x86) iOS 的支援

**Bug 修正**

- 如果未 `KeywordRecognizer` 使用有效的語音服務訂用帳戶金鑰，請修正損毀的問題

**範例**

- 的 Xamarin 範例 `KeywordRecognizer`
- 的 Unity 範例 `KeywordRecognizer`
- 自動來源語言偵測的 c + + 和 JAVA 範例。

## <a name="speech-sdk-170-2019-september-release"></a>語音 SDK 1.7.0： 2019-九月版本

**新功能**

- 針對通用 Windows 平臺 (UWP) 、Android 和 iOS 上的 Xamarin 新增 Beta 支援
- 已新增適用于 Unity 的 iOS 支援
- `Compressed`針對 Android、iOS 和 Linux 上的 ALaw、Mulaw、FLAC 新增了輸入支援
- 已 `SendMessageAsync` 在類別中新增傳送 `Connection` 訊息至服務
- `SetMessageProperty`在 `Connection` 設定訊息屬性的類別中加入
- TTS 已新增 JAVA (JRE 和 Android) 、Python、Swift 和目標-C 的系結
- TTS 已新增 macOS、iOS 和 Android 的播放支援。
- 已新增 TTS 的「字邊界」資訊。

**Bug 修正**

- 已修正 IL2CPP 適用于 Android 的 Unity 2019 組建問題
- 修正 wav 檔案輸入中格式錯誤的標頭未正確處理的問題
- 修正了某些連接屬性中的 Uuid 不是唯一的問題
- 修正了 Swift 系結中的可 null 性規範的一些警告 (可能需要變更少量的程式碼) 
- 修正在網路負載下導致 websocket 連線關閉強制的錯誤（bug）
- 修正了 Android 上的問題，有時會導致使用重複的印象識別碼 `DialogServiceConnector`
- 改進跨多重回合互動的連線穩定性，以及發生失敗時，透過 `Canceled` 事件)  (報告 `DialogServiceConnector`
- `DialogServiceConnector` 會話啟動現在會正確提供事件，包括在 `ListenOnceAsync()` 使用中的期間呼叫 `StartKeywordRecognitionAsync()`
- 解決與收到的活動相關聯的損毀 `DialogServiceConnector`

**範例**

- Xamarin 的快速入門
- 以 Linux ARM64 資訊更新的 CPP 快速入門
- 使用 iOS 資訊更新 Unity 快速入門

## <a name="speech-sdk-160-2019-june-release"></a>語音 SDK 1.6.0： 2019-六月版本

**範例**

- UWP 和 Unity 上文字轉換語音的快速入門範例
- IOS 上 Swift 的快速入門範例
- 適用于語音 & 意圖辨識和轉譯的 Unity 範例
- 的更新快速入門範例 `DialogServiceConnector`

**/變更**

- 對話方塊命名空間：
  - `SpeechBotConnector` 已重新命名為 `DialogServiceConnector`
  - `BotConfig` 已重新命名為 `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` 已重新對應至 `DialogServiceConfig::FromBotSecret()`
  - 重新命名之後，仍會繼續支援所有現有的 Direct Line 語音用戶端
- 更新 TTS REST 介面卡以支援 proxy、持續連線
- 改進傳遞無效區域時的錯誤訊息
- Swift/目標-C：
  - 改良的錯誤報表：可能導致錯誤的方法現在會出現在兩個版本中：一個會公開 `NSError` 處理錯誤的物件，另一個則會引發例外狀況。 前者會公開至 Swift。 這種變更需要採用原音至現有的 Swift 程式碼。
  - 改進的事件處理

**Bug 修正**

- 修正 TTS： `SpeakTextAsync` 未來傳回但未等候音訊已完成轉譯的情況
- 針對 c # 中的封送處理字串進行修正，以啟用完整的語言支援
- 修正 .NET core 應用程式問題，以使用 net461 目標架構在範例中載入核心程式庫
- 修正將原生程式庫部署至範例中輸出檔案夾的偶爾問題
- 可靠地關閉 web 通訊端的修正
- 修正在 Linux 上的負載過重時開啟連線時可能損毀的問題
- 修正 macOS 架構套件組合中遺失的中繼資料
- 修正 `pip install --user` Windows 上的問題

## <a name="speech-sdk-151"></a>語音 SDK 1.5。1

這是 bug 修正版本，只會影響原生/受管理的 SDK。 它不會影響 JavaScript 版本的 SDK。

**Bug 修正**

- 修正搭配對話轉譯時使用的 FromSubscription。
- 修正語音助理關鍵字找出中的 bug。

## <a name="speech-sdk-150-2019-may-release"></a>語音 SDK 1.5.0： 2019-可能版本

**新功能**

- 關鍵字找出 (KWS) 現在適用于 Windows 和 Linux。 KWS 功能可能適用于任何麥克風類型、官方 KWS 支援，但目前僅限於 Azure Kinect DK 硬體或語音裝置 SDK 中的麥克風陣列。
- 片語提示功能可透過 SDK 取得。 如需詳細資訊，請參閱[這裡](how-to-phrase-lists.md)。
- 您可以透過 SDK 取得對話轉譯功能。 請參閱 [這裡](conversation-transcription-service.md)。
- 使用 Direct Line 語音通道來新增語音助理支援。

**範例**

- 已新增 SDK 所支援的新功能或新服務的範例。

**/變更**

- 新增各種辨識器屬性來調整服務行為或服務結果 (例如遮罩不雅內容和其他) 。
- 您現在可以透過標準設定屬性來設定辨識器，即使您已建立辨識器 `FromEndpoint` 。
- 目標-C： `OutputFormat` 屬性已新增至 `SPXSpeechConfiguration` 。
- SDK 現在支援 Debian 9 作為 Linux 散發套件。

**Bug 修正**

- 修正瞭解構說話者資源過早在文字轉換語音中的問題。

## <a name="speech-sdk-142"></a>語音 SDK 1.4。2

這是 bug 修正版本，只會影響原生/受管理的 SDK。 它不會影響 JavaScript 版本的 SDK。

## <a name="speech-sdk-141"></a>語音 SDK 1.4。1

這是僅限 JavaScript 的版本。 未新增任何功能。 已進行下列修正：

- 防止 web 套件載入 HTTPs-proxy 代理程式。

## <a name="speech-sdk-140-2019-april-release"></a>語音 SDK 1.4.0： 2019-四月版本

**新功能**

- SDK 現在支援以文字轉換語音服務作為 Beta 版。 從 c + + 和 c #，Windows 和 Linux Desktop 都支援此功能。 如需詳細資訊，請參閱 [文字轉換語音總覽](text-to-speech.md#get-started)。
- SDK 現在支援 MP3 和 Opus/OGG 音訊檔案作為串流輸入檔。 這項功能僅適用于 c + + 和 c # 的 Linux，且目前為 Beta 版 ([詳細資料) ](how-to-use-codec-compressed-audio-input-streams.md) 。
- 適用于 JAVA、.NET core、c + + 和目標 C 的語音 SDK 已獲得 macOS 支援。 MacOS 的目標 C 支援目前為搶鮮版（Beta）。
- iOS：適用于 iOS 的語音 SDK (目標-C) 現在也會發佈為 CocoaPod。
- JavaScript：支援非預設的麥克風作為輸入裝置。
- JavaScript： Node.js 的 Proxy 支援。

**範例**

- 已新增在 macOS 上使用語音 SDK 搭配 c + + 和目標 C 的範例。
- 已新增示範如何使用文字轉換語音服務的範例。

**/變更**

- Python：辨識結果的其他屬性現在會透過屬性公開 `properties` 。
- 如需其他開發和偵錯工具支援，您可以將 SDK 記錄和診斷資訊重新導向至記錄檔， ([詳細資料) ](how-to-use-logging.md) 。
- JavaScript：改善音訊處理效能。

**Bug 修正**

- Mac/iOS：已修正無法建立語音服務連線時，導致等候時間過長的 bug。
- Python：改善 Python 回呼中引數的錯誤處理。
- JavaScript：已修正在 RequestSession 上結束語音的錯誤狀態報表。

## <a name="speech-sdk-131-2019-february-refresh"></a>語音 SDK 1.3.1： 2019-二月更新

這是 bug 修正版本，只會影響原生/受管理的 SDK。 它不會影響 JavaScript 版本的 SDK。

**錯誤 (bug) 修正**

- 修正使用麥克風輸入時的記憶體遺漏。 以資料流程為基礎或檔案輸入不受影響。

## <a name="speech-sdk-130-2019-february-release"></a>語音 SDK 1.3.0： 2019-二月版

**新功能**

- 語音 SDK 支援透過類別選取輸入麥克風 `AudioConfig` 。 這可讓您將音訊資料從非預設的麥克風串流至語音服務。 如需詳細資訊，請參閱說明 [音訊輸入裝置選擇](how-to-select-audio-input-devices.md)的檔。 JavaScript 尚無法使用這項功能。
- 語音 SDK 現在支援搶鮮版 (Beta) Unity。 透過 [GitHub 範例存放庫](https://aka.ms/csspeech/samples)中的「問題」一節提供意見反應。 此版本可在 Windows x86 和 x64 (桌面或通用 Windows 平台應用程式) 以及 Android (ARM32/64、x86) 上支援 Unity。 詳情請參閱 [Unity 快速入門](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)。
- `Microsoft.CognitiveServices.Speech.csharp.bindings.dll`先前版本中隨附的檔案 (不再需要) 。 這項功能現已整合到核心 SDK 中。

**範例**

[範例存放庫](https://aka.ms/csspeech/samples)有提供下列新內容：

- 的其他範例 `AudioConfig.FromMicrophoneInput` 。
- 額外的 Python 範例，適用於意圖辨識和翻譯。
- `Connection`在 iOS 中使用物件的其他範例。
- 額外的 Java 範例，適用於以音訊輸出進行翻譯。
- [批次轉譯 REST API](batch-transcription.md) 的新使用範例。

**/變更**

- Python
  - 中改善的參數驗證和錯誤訊息 `SpeechConfig` 。
  - 加入物件的支援 `Connection` 。
  - 支援在 Windows 上使用 32 位元的 Python (x86)。
  - 適用於 Python 的語音 SDK 已不再是搶鮮版 (Beta)。
- iOS
  - SDK 現在是根據 iOS SDK 12.1 版來建置。
  - SDK 現在支援 iOS 9.2 版和更新版本。
  - 改善參考文件，並修正數個屬性名稱。
- JavaScript
  - 加入物件的支援 `Connection` 。
  - 為配套的 JavaScript 新增了類型定義檔
  - 初次支援和實作片語提示。
  - 傳回含有服務 JSON 的屬性集合以供辨識
- Windows DLL 現在會包含版本資源。
- 如果您建立辨識器 `FromEndpoint` ，您可以直接將參數加入至端點 URL。 使用 `FromEndpoint` 時，您無法透過標準設定屬性來設定辨識器。

**Bug 修正**

- 空的 Proxy 使用者名稱和 Proxy 密碼之前不會正確處理。 在此版本中，如果您將 Proxy 使用者名稱和 Proxy 密碼設定為空字串，系統將不會在連線到 Proxy 時提交出去。
- 在某些語言&nbsp;/環境中，SDK 所建立的工作階段識別碼並不一定會真正隨機。 已新增隨機產生器初始化以修正此問題。
- 改善授權權杖的處理方式。 如果您想要使用授權權杖，請在中指定， `SpeechConfig` 並將訂用帳戶金鑰保留空白。 然後如往常般建立辨識器。
- 在某些情況下， `Connection` 物件未正確釋放。 已修正此問題。
- JavaScript 範例已修正，現在也能夠在 Safari 上支援以音訊輸出合成翻譯。

## <a name="speech-sdk-121"></a>語音 SDK 1.2.1

這是僅限 JavaScript 的版本。 未新增任何功能。 已進行下列修正：

- 會在 turn.end 而非 speech.end 引發資料流的結束。
- 修正音訊幫浦中的 BUG，此 BUG 會導致系統未在目前的傳送失敗時排程下一次傳送。
- 修正使用驗證權杖來進行的連續辨識。
- 修正不同辨識器/端點的 BUG。
- 文件改進。

## <a name="speech-sdk-120-2018-december-release"></a>語音 SDK 1.2.0： 2018-十二月發行

**新功能**

- Python
  - 這一版提供 Python 支援的搶鮮版 (Beta) (3.5 和更新版本)。 如需詳細資訊，請參閱這裡] (quickstart-python.md) 。
- JavaScript
  - 適用於 JavaScript 的語音 SDK 已開放原始碼。 原始程式碼位於 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)。
  - 我們現在支援 Node.js，您可以在[這裡](quickstart-js-node.md)找到更多資訊。
  - 音訊工作階段的長度限制已移除，重新連線會自動發生。
- `Connection` 物件
  - `Recognizer`您可以從存取 `Connection` 物件。 此物件可讓您明確地起始服務連線，並訂閱連線和中斷連線事件。
     (這項功能尚無法從 JavaScript 和 Python 使用。 ) 
- Ubuntu 18.04 支援。
- Android
  - 已在 APK 產生期間啟用 ProGuard 支援。

**改善項目**

- 改進內部執行緒使用方式，並減少執行緒、鎖定、Mutex 的數目。
- 已改善錯誤回報 / 資訊。 在幾個案例中，錯誤訊息尚未傳播出去。
- 已將 JavaScript 中的開發相依性更新為使用最新的模組。

**Bug 修正**

- 修正記憶體流失，因為中的類型不相符 `RecognizeAsync` 。
- 在某些情況下，例外狀況正在流失。
- 已修正翻譯事件引數的記憶體流失。
- 已修正在長時間執行的工作階段中重新連線的鎖定問題。
- 修正了可能導致失敗的翻譯遺失最終結果的問題。
- C #：如果在 `async` 主執行緒中未等候某項作業，則可能會在非同步工作完成之前處置辨識器。
- JAVA：已修正導致 JAVA VM 損毀的問題。
- 目標-C：固定的列舉對應;傳回 RecognizedIntent，而不是 `RecognizingIntent` 。
- JavaScript：在中將預設輸出格式設定為 [簡單] `SpeechConfig` 。
- JavaScript：在 JavaScript 和其他語言中的設定物件上的屬性之間移除不一致的情況。

**範例**

- 已更新並修正數個範例 (例如，用於轉譯的輸出語音等 ) 。
- 已在[範例存放庫](https://aka.ms/csspeech/samples)中新增 Node.js 範例。

## <a name="speech-sdk-110"></a>語音 SDK 1.1.0

**新功能**

- 支援 Android x86/x64。
- Proxy 支援：在 `SpeechConfig` 物件中，您現在可以呼叫函式來設定 proxy 資訊 (主機名稱、埠、使用者名稱和密碼) 。 此功能尚無法在 iOS 上取得。
- 改善的錯誤碼和訊息。 如果辨識傳回錯誤，系統現在會將 `Reason` (在取消的事件中) 或 `CancellationDetails` (在辨識結果中) 設為 `Error`。 取消的事件現在包含兩個額外的成員，`ErrorCode` 和 `ErrorDetails`。 如果伺服器隨報告的錯誤傳回其他錯誤資訊，現在將可以透過新成員取得這些資訊。

**改善項目**

- 已在辨識器設定中新增其他驗證，及新增其他錯誤訊息。
- 已改善對音訊檔案中長時間無聲之部分的處理。
- NuGet 套件：針對 .NET Framework 專案，其可防止使用 AnyCPU 設定來建置。

**Bug 修正**

- 已修正在辨識器中找到的幾個例外狀況。 此外，也會攔截例外狀況，並將其轉換成 `Canceled` 事件。
- 修正屬性管理中的記憶體流失。
- 已修正音訊輸入可能會損毀辨識器的錯誤。
- 已修正工作階段將事件停止之後，仍然可收到事件的錯誤。
- 已修正執行緒中的一些競爭條件。
- 已修正可能會導致當機的 iOS 相容性問題。
- 已改善對 Android 麥克風支援的穩定性。
- 已修正 JavaScript 中的辨識器會忽略辨識語言的錯誤。
- 修正在某些情況下，在 JavaScript 中) 無法設定 (的錯誤 `EndpointId` 。
- 已在 JavaScript 中變更 AddIntent 中的參數順序，並新增了缺少的 JavaScript 簽章 `AddIntent` 。

**範例**

- 已針對 [範例存放庫](https://aka.ms/csspeech/samples)中的提取和推送資料流程使用，新增 c + + 和 c # 範例。

## <a name="speech-sdk-101"></a>語音 SDK 1.0.1

穩定性改進及 Bug 修正：

- 已修正處置辨識器時因競爭條件而可能導致的嚴重錯誤
- 已修正取消設定屬性時可能發生的嚴重錯誤。
- 已新增額外的錯誤和參數檢查。
- Objective-C：已修正因 NSString 中的名稱覆寫而可能導致的嚴重錯誤。
- Objective-C：已調整 API 的可見性
- JavaScript：已修正有關事件及其承載的問題。
- 文件改進。

在我們的[範例存放庫](https://aka.ms/csspeech/samples)中，已新增新的 JavaScript 範例。

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>認知服務語音 SDK 1.0.0：2018 年 9 月版

**新功能**

- iOS 上對 Objective-C 的支援。 請參閱我們的[適用於 iOS 的 Objective-C 快速入門](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)。
- 瀏覽器中對 JavaScript 的支援。 請參閱我們的 [JavaScript 快速入門](quickstart-js-browser.md)。

**重大變更**

- 在此版本中，引進了一些重大變更。
  請查看 [此頁面](https://aka.ms/csspeech/breakingchanges_1_0_0) 以取得詳細資料。

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>認知服務語音 SDK 0.6.0：2018 年 8 月版

**新功能**

- 使用語音 SDK 所建置的 UWP 應用程式現在可以通過 Windows 應用程式認證套件 (WACK) 的認證。
  請參閱 [UWP 快速入門](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)。
- 在 Linux (Ubuntu 16.04 x64) 上支援 .NET Standard 2.0。
- 實驗性：在 Windows (64 位元) 和 Linux (Ubuntu 16.04 x64) 上支援 Java 8。
  請參閱 [Java 執行階段環境快速入門](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)。

**功能性變更**

- 公開關於連線錯誤的其他錯誤詳細資訊。

**重大變更**

- 在 Java (Android) 上，`SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 函式不再需要路徑參數。 系統現在會自動在所有支援平台上偵測路徑。
- Java 和 C# 中 `EndpointUrl` 屬性的 get-accessor 已移除。

**Bug 修正**

- 在 Java 中，現已實作翻譯辨識器上的音訊合成結果。
- 已修正錯誤，此錯誤可能導致非作用中的執行緒，並增加已開啟且未使用的通訊端數目。
- 已修正問題，長時間執行的辨識可能會在傳輸途中終止。
- 已修正辨識器關機中的競爭條件。

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>認知服務語音 SDK 0.5.0：2018 年 7 月版

**新功能**

- 支援 Android 平台 (API 23: Android 6.0 Marshmallow 或更新版本)。 請參閱 [Android 快速入門](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)。
- 在 Windows 上支援 .NET Standard 2.0。 請參閱 [.NET Core 快速入門](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)。
- 實驗性：在 Windows 上支援 UWP (1709 版或更新版本)。
  - 請參閱 [UWP 快速入門](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)。
  - 注意：使用語音 SDK 建置的 UWP 應用程式尚未通過 Windows 應用程式認證套件 (WACK) 的認證。
- 使用自動重新連線支援長時間執行辨識。

**功能性變更**

- `StartContinuousRecognitionAsync()` 支援長時間執行辨識。
- 辨識結果包含更多欄位。 它們從已辨識文字的音訊開頭和持續時間 (兩者都以刻度為單位)，以及表示辨識狀態的其他值位移，例如 `InitialSilenceTimeout` 和 `InitialBabbleTimeout`。
- 支援 AuthorizationToken 以建立處理站執行個體。

**重大變更**

- 辨識事件： `NoMatch` 事件種類已合併到 `Error` 事件中。
- C # 中的 SpeechOutputFormat 已重新命名為， `OutputFormat` 以便與 c + + 保持一致。
- `AudioInputStream` 介面部分方法的傳回型別已稍微變更：
  - 在 Java 中，`read` 方法現在會傳回 `long` 而不是 `int`。
  - 在 C# 中，`Read` 方法現在會傳回 `uint` 而不是 `int`。
  - 在 C++ 中，`Read` 和 `GetFormat` 方法現在會傳回 `size_t` 而不是 `int`。
- C++：音訊輸入串流的執行個體現在只能當作 `shared_ptr` 傳遞。

**Bug 修正**

- 修正 `RecognizeAsync()` 逾時的時候，結果中不正確的傳回值。
- 已移除 Windows 上媒體基礎程式庫的相依性。 SDK 現在使用核心音訊 API。
- 文件修正：已新增[區域](regions.md)頁面來描述支援的區域。

**已知問題**

- 適用於 Android 的語音 SDK 不會回報用於翻譯的語音合成結果。 此問題將在下一版中修正。

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>認知服務語音 SDK 0.4.0：2018 年 6 月版本

**功能性變更**

- AudioInputStream

  辨識器現在可以取用資料流作為音訊來源。 如需詳細資訊，請參閱相關的[操作說明指南](how-to-use-audio-input-streams.md)。

- 詳細的輸出格式

  建立 `SpeechRecognizer` 時，您可以要求 `Detailed` 或 `Simple` 輸出格式。 `DetailedSpeechRecognitionResult` 包含信賴分數、已辨識的文字、未經處理的語彙形式，以及標準化形式和加上遮罩的粗話。

**重大變更**

- 在 C# 中已從 `SpeechRecognitionResult.RecognizedText` 變更為 `SpeechRecognitionResult.Text`。

**Bug 修正**

- 已修正在關閉期間 USP 層中可能發生的回呼問題。
- 如果辨識器取用音訊輸入檔，其保留檔案控制代碼超過所需的時間。
- 已移除訊息幫浦與辨識器之間的數個死結。
- 如果來自服務的回應逾時，則引發 `NoMatch`。
- Windows 上的媒體基礎程式庫延遲載入。 只有麥克風輸入需要此程式庫。
- 音訊資料的上傳速度受限於大約兩倍的原始音訊速度。
- 在 Windows 上，C# .NET 組件現在使用強式名稱。
- 文件修正：`Region` 是建立辨識器時所需的資訊。

已新增更多範例，且持續更新。 如需最新的範例集，請參閱[語音 SDK 範例 GitHub 存放庫](https://aka.ms/csspeech/samples) \(英文\)。

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>認知服務語音 0.2.12733：2018 年 5 月版本

此版本為認知服務語音 SDK 的第一個公開預覽版本。
