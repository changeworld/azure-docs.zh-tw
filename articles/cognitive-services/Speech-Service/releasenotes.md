---
title: 發行標題 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務功能發佈的運行日誌、改進、錯誤修復和已知問題。
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 7bab0a28ba2b75903b6bdf4708e6aa0a98bdc9e5
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607411"
---
# <a name="release-notes"></a>版本資訊
## <a name="speech-sdk-1110-2020-march-release"></a>語音 SDK 1.11.0:2020-3 月版本

**新功能**

- Linux:添加了對紅帽企業 Linux (RHEL)/CentOS 7 x64 的支援,並提供了有關如何為語音 SDK 設定系統[的說明](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7)。
- Linux:在Linux ARM32和ARM64上增加了對.NET核心C++的支援。 您可以在[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux)進一步了解。 
- C#,C++:在`UtteranceId``ConversationTranscriptionResult`中添加,在所有中間部分和最終語音辨識結果中添加一致的 ID。 [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet)的詳細資訊[,C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)。
- Python: 添加`Language ID`了 對的支援。 請參閱[gitHub 儲存庫](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)中的 speech_sample.py。
- Windows:為所有 win32 控制台應用程式在 Windows 平臺上添加了壓縮音訊輸入格式支援。 詳情[請見此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)。 
- JavaScript:在 NodeJS 中支持語音合成(文本到語音)。 [在此處](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech)瞭解更多資訊。 
- JavaScript:添加新 API,以便檢查所有發送和接收的消息。 [在此處](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript)瞭解更多資訊。 
        
**錯誤修復**

- C#,C++:修復了問題,所以`SendMessageAsync`現在以二進位類型發送二進制消息。 [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_)的詳細資訊[,C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection)。
- C#,C++:修復了在物件之前`Connection MessageReceived``Recognizer``Connection`釋放使用事件可能會導致崩潰的問題。 [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet)的詳細資訊[,C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived)。
- Android:麥克風的音頻緩衝大小從 800ms 減少到 100ms,以提高延遲。
- 安卓:修復了Android工作室中x86 Android模擬器[的問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563)。
- JavaScript:使用`fromSubscription`API 為中國區域添加了支援。 詳情[請見此處](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-)。 
- JavaScript:為 NodeJS 的連接失敗添加更多錯誤資訊。
        
**範例**

- 統一:意圖識別公共示例是固定的,LUIS json 導入失敗。 詳情[請見此處](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)。
- Python:為`Language ID`添加的範例。 詳情[請見此處](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)。
    
**Covid19 分橋測試**

由於過去幾周遠端工作,我們無法執行通常情況下的手動設備驗證測試。 例如,在 Linux、iOS 和 macOS 上測試麥克風輸入和揚聲器輸出。 我們尚未進行任何我們認為可能破壞這些平臺上的任何內容的更改,我們的自動測試都通過了。 萬一我們錯過了什麼,請讓我們知道[GitHub。](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen)<br> 感謝您的繼續支援。 與往常一樣,請在[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen)或[堆疊溢出](https://stackoverflow.microsoft.com/questions/tagged/731)上發佈問題或反饋。<br>
保持健康!

## <a name="speech-sdk-1100-2020-february-release"></a>語音 SDK 1.10.0:2020-2 月版本

**新功能**

 - 新增了 Python 套件以支援新的 3.8 版 Python。
 - 紅帽企業 Linux (RHEL)/CentOS 8 x64 支援(C++、C#、JAVA、Python)。
   > [!NOTE] 
   > 客戶必須根據[這些說明](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)配置OpenSSL。
 - Linux ARM32 支援 Debian 和 Ubuntu。
 - 對話服務連接器現在支援 BotFrameworkConfig 上的可選「機器人 ID」參數。 此參數允許將多個直接線路語音自動程式與單個 Azure 語音資源一起使用。 如果未指定參數,將使用預設自動程式(由"直線語音通道配置頁"確定)。
 - 對話框服務連接器現在具有語音活動範本屬性。 此 JSON 字串的內容將由 Direct Line 語音用於在到達直線語音機器人的所有活動中預填充各種受支援的欄位,包括自動生成的活動以回應語音辨識等事件。
 - TTS 現在使用訂閱金鑰進行身份驗證,減少了創建合成器后第一個合成結果的第一個字節延遲。
 - 更新的 19 區域設置的語音辨識模型,平均字錯誤率降低 18.6%(es-ES、es-MX、fr-CA、fr-FR、IT、ja-JP、ko-KR、pt-BR、zh-CN、zh-HK、nb-NO、fi-FL、ru-RU、pl-PL、ca-ES、zh-TW、th-TH、pt-PT、tr-TR)。 新模型在多個域(包括聽寫、話務中心轉錄和視頻索引方案)中帶來了顯著改進。

**錯誤修復**

 - 修復了對話轉錄器未在 JAVA API 中正確等待的修復錯誤 
 - Android x86 模擬器修復 Xamarin [GitHub 問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - 新增遺失(抓取*設定)屬性方法到音訊設定
 - 修復連線時無法停止音效資料串流的 TTS 錯誤
 - 使用沒有區域的終結點會導致對話翻譯的 USP 失敗
 - 通用 Windows 應用程式中的 ID 生成現在使用適當唯一的 GUID 演演演算法;它以前無意中預設為存根實現,該實現經常在大型交互集上產生衝突。
 
 **範例**
 
 - 將語音 SDK 與[Unity 麥克風和推送模式串](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)流 一起使用的 Unity 範例

**其他變更**

 - [為 Linux 更新的 OpenSSL 設定文件](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>語音 SDK 1.9.0:2020-1 月版本

**新功能**

- 多設備對話:將多個設備連接到同一語音或基於文本的對話,並可以選擇翻譯它們之間發送的消息。 在本文中[瞭解更多資訊。](multi-device-conversation.md) 
- 為 Android .aar 包添加了關鍵字識別支援,並增加了對 x86 和 x64 口味的支援。 
- 目標`SendMessage`C:`SetMessageProperty`和`Connection`新增到 物件的方法。 [在此處](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)查看文檔。
- TTS C++`std::wstring`api 現在支援作為合成文本輸入,無需將 wstring 轉換為字串,然後再將其傳遞到 SDK。 請參閱[這裡](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)的詳細資料。 
- C#:[語言 ID](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp)和[源語言配置](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp)現已可用。
- JavaScript:添加了一個`Connection`用於反對的功能,以將來自語音服務的自定義消息作為`receivedServiceMessage`回調 傳遞。
- JavaScript:添加了支援,`FromHost API`以方便使用上置容器和主權雲。 [在此處](speech-container-howto.md)查看文檔。
- JavaScript:我們現在很榮幸`NODE_TLS_REJECT_UNAUTHORIZED`,感謝[來自組織](https://github.com/orgads)的貢獻。 請參閱[這裡](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)的詳細資料。

**重大變更**

- `OpenSSL`已更新到版本 1.1.1b,並靜態連結到 Linux 的語音 SDK 核心庫。 如果收件匣`OpenSSL`尚未安裝到系統中的目錄`/usr/lib/ssl`, 則可能導致中斷。 請查看我們在語音 SDK 文件下[的文檔](how-to-configure-openssl-linux.md)以解決此問題。
- 我們更改了`WordLevelTimingResult.Offset`C# 傳`int`回`long`的資料類型`WordLevelTimingResults`, 以允許存取 語音資料超過 2 分鐘時。
- `PushAudioInputStream`現在`PullAudioInputStream`,`AudioStreamFormat`根據 () 將 wav 標頭資訊發送到語音服務,這些資訊在創建時可選擇指定。 客戶現在必須使用[支援的音訊輸入格式](how-to-use-audio-input-streams.md)。 任何其他格式都將獲得次優的識別結果,或可能導致其他問題。 

**錯誤修復**

- 請參閱上面`OpenSSL`的「中斷更改」下的更新。 我們在 Linux 和 Java 中修復了間歇性崩潰和性能問題(在高負載下鎖定爭用)。 
- Java:在高併發方案中對物件閉包進行了改進。
- 重組我們的 NuGet 套件。 我們刪除了 lib`Microsoft.CognitiveServices.Speech.core.dll``Microsoft.CognitiveServices.Speech.extension.kws.dll`資料夾的 三個副本和 lib 資料夾,使 NuGet 包更小、更快地下載,並且添加了編譯某些C++本機應用所需的標頭。
- [修復了此處](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)的快速入門示例。 這些是在Linux、MacOS、Windows上未顯示"找不到麥克風"的異常。
- 修復了 SDK 崩潰,在某些代碼路徑(如[此示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp))上會導致長語音辨識結果。
- 修復 Azure Web 應用程式環境中的 SDK 部署錯誤,以解決[此客戶問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)。
- 修復了使用多`<voice>`標記`<audio>`或 標記來解決[此客戶問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)時的 TTS 錯誤。 
- 修復了從掛起的 SDK 恢復時 TTS 401 錯誤。
- JavaScript:修復了音訊數據的循環導入,這要歸功於[來自 euirim](https://github.com/euirim)的貢獻。 
- JavaScript:添加了對設置服務屬性的支援,如 1.7 中添加的。
- JavaScript:修復了連接錯誤可能導致連續、不成功的 Web 套接字重新連接嘗試的問題。

**範例**

- [在此處](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)為 Android 添加了關鍵字識別範例。
- [在此處](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)為伺服器方案添加了 TTS 示例。
- [在此處](quickstarts/multi-device-conversation.md)添加了 C# 和 C++的多設備對話快速啟動。

**其他變更**

- 在 Android 上優化了 SDK 核心庫大小。
- 1.9.0 及以後的 SDK`int`支援`string`對話轉錄器的語音簽名版本欄位中的兩種和類型。

## <a name="speech-sdk-180-2019-november-release"></a>語音 SDK 1.8.0:2019-11 月版本

**新功能**

- 添加了`FromHost()`API,以方便使用預處理容器和主權雲。
- 新增語音辨識的自動源語言偵測(在 JAVA 和 C++中)
- 為`SourceLanguageConfig`語音辨識添加物件,用於指定預期的源語言(在 JAVA 和 C++)
- 使用`KeywordRecognizer`NuGet 與 Unity 套件在 Windows (UWP)、Android 和 iOS 上新增支援
- 添加了遠端對話 JAVA API 以非同步批次處理執行會話轉錄。

**重大變更**

- 對話轉錄器功能在命名空間`Microsoft.CognitiveServices.Speech.Transcription`下移動。
- 部分對話轉錄器方法將移動到新`Conversation`類。
- 放棄對 32 位元 (ARMv7 和 x86) iOS 的支援

**錯誤修復**

- 如果使用本地軟體`KeywordRecognizer`時沒有有效的語音服務訂閱金鑰,則修復崩潰

**範例**

- Xamarin 樣品`KeywordRecognizer`
- 整合範例`KeywordRecognizer`
- 用於自動源語言檢測的C++和 Java 示例。

## <a name="speech-sdk-170-2019-september-release"></a>語音 SDK 1.7.0:2019-9 月版本

**新功能**

- 在通用 Windows 平臺 (UWP)、Android 和 iOS 上新增了對 Xamarin 的測試版支援
- 新增了對統一的 iOS 支援
- 增加了`Compressed`對ALaw、Mulaw、在Android、iOS和Linux上的FLAC的輸入支援
- 新增`SendMessageAsync`在`Connection`類別的服務傳送訊息
- 新增`SetMessageProperty`在`Connection`類別中加入設定訊息屬性
- TTS 為 Java(Jre 和 Android)、Python、Swift 和目標 C 添加了綁定
- TTS 增加了對 macOS、iOS 和 Android 的播放支援。
- 為 TTS 添加了「字邊界」資訊。

**錯誤修復**

- 修復了 Android Unity 2019 上的 IL2CPP 構建問題
- 修復 wav 檔輸入格式錯誤的標頭處理不正確的問題
- 修復了 UUD 在某些連接屬性中不唯一的問題
- 修復了有關 Swift 綁定中無效指定器的一些警告(可能需要少量代碼更改)
- 修復了導致網路套接字連線在網路負載下不正常關閉的 Bug
- 修復了 Android 上的問題,該問題有時會導致使用重複的展示 ID`DialogServiceConnector`
- 改進了跨多圈交互的連接穩定性,並在發生故障時(通過`Canceled`事件)報告故障`DialogServiceConnector`
- `DialogServiceConnector`工作階段啟動現在將正確提供事件,包括在活動期間`ListenOnceAsync()`呼叫時`StartKeywordRecognitionAsync()`
- 解決了與`DialogServiceConnector`正在接收的活動相關的崩潰

**範例**

- 夏馬林的快速入門
- 更新的 CPP 快速入門與 Linux ARM64 資訊
- 使用 iOS 資訊更新 Unity 快速入門

## <a name="speech-sdk-160-2019-june-release"></a>語音 SDK 1.6.0:2019-6 月版本

**範例**

- UWP 與 Unity 上文字到語音的快速入門示例
- 在 iOS 快速啟動快速快速取樣
- 語音&意向識別和翻譯的統一示例
- 更新快速入門示例`DialogServiceConnector`

**/變更**

- 對話框命名空間:
  - `SpeechBotConnector` 已重新命名為 `DialogServiceConnector`
  - `BotConfig` 已重新命名為 `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`已重新映射到`DialogServiceConfig::FromBotSecret()`
  - 重新命名後所有現有的直線語音客戶端將繼續受支援
- 更新 TTS REST 配接器以支援代理、持久連線
- 傳遞不合法區域時改進錯誤訊息
- Swift/目標C:
  - 改進的錯誤報告:可能導致錯誤的方法現在存在於兩個版本中:一個公開物件以處理錯誤,另一`NSError`個版本引發異常。 前者暴露於斯威夫特。 此更改需要對現有 Swift 代碼進行調整。
  - 改進的事件處理

**錯誤修復**

- TTS 的修復`SpeakTextAsync`: 在未等待音訊完成成成一個的位置
- 修復了 C# 中封送字串,以啟用完整的語言支援
- 修復了 .NET 核心應用問題,以在範例中使用 net461 目標框架載入核心庫
- 修復將本機庫部署到範例中的輸出資料夾的偶發問題
- 修復 Web 通訊接字的限制
- 修復在 Linux 上負載非常重的情況下開啟連接時可能出現的崩潰
- 修復了 macOS 框架捆綁套件中缺少的元數據
- 修復 Windows`pip install --user`上的問題

## <a name="speech-sdk-151"></a>語音 SDK 1.5.1

這是一個錯誤修復版本,僅影響本機/託管 SDK。 它不會影響 SDK 的 JAVAScript 版本。

**錯誤修復**

- 與對話轉錄一起使用時修復"從訂閱」。
- 修復了語音助理的關鍵字發現中的 Bug。

## <a name="speech-sdk-150-2019-may-release"></a>語音 SDK 1.5.0: 2019-5 月版本

**新功能**

- 關鍵字發現 (KWS) 現在可用於 Windows 和 Linux。 KWS 功能可能適用於任何麥克風類型,但官方 KWS 支援目前僅限於 Azure Kinect DK 硬體或語音裝置 SDK 中的麥克風陣列。
- 短語提示功能可通過 SDK 獲得。 如需詳細資訊，請參閱[這裡](how-to-phrase-lists.md)。
- 對話轉錄功能可通過 SDK 獲得。 [見此處](conversation-transcription-service.md)。
- 使用"直撥電話語音"通道添加對語音助理的支援。

**範例**

- 添加了 SDK 支援的新功能或新功能的範例。

**/變更**

- 添加了各種識別器屬性來調整服務行為或服務結果(如掩蓋褻瀆和其他)。
- 現在,您可以通過標準設定屬性設定辨識器,即使您建立了識別`FromEndpoint`器 。
- 目標 C:`OutputFormat`屬性已`SPXSpeechConfiguration`新增到 。
- SDK 現在支援 Debian 9 作為 Linux 發行版。

**錯誤修復**

- 修復了揚聲器資源在文本到語音轉換中過早析的問題。

## <a name="speech-sdk-142"></a>語音 SDK 1.4.2

這是一個錯誤修復版本,僅影響本機/託管 SDK。 它不會影響 SDK 的 JAVAScript 版本。

## <a name="speech-sdk-141"></a>語音 SDK 1.4.1

這是僅限 JavaScript 的版本。 未新增任何功能。 已進行下列修正：

- 防止 Web 包載入 Hth 代理代理。

## <a name="speech-sdk-140-2019-april-release"></a>語音 SDK 1.4.0:2019-4 月版本

**新功能**

- SDK 現在支援文字到語音轉換服務作為測試版。 它受windows和Linux桌面C++和C#的支援。 關於詳細資訊,請檢視[文字到語音轉換概述](text-to-speech.md#get-started)。
- SDK 現在支援 MP3 和 Opus/OGG 音訊檔作為串流輸入檔。 此功能僅在 linux 上從 C++ 和 C# 上可用,並且當前處於測試版中([此處](how-to-use-codec-compressed-audio-input-streams.md)的更多詳細資訊)。
- Java、.NET 內核、C++和 Objective-C 的語音 SDK 獲得了 macOS 支援。 對 macOS 的目標 C 支援目前處於測試階段。
- iOS: iOS 的語音 SDK (目標 C) 現在也作為 CocoaPod 發佈。
- JavaScript:支援非預設麥克風作為輸入設備。
- JavaScript:對 Node.js 的代理支援。

**範例**

- 添加了用於在 macOS 上使用語音 SDK C++和目標 C 的範例。
- 添加了演示文本到語音轉換服務使用方式的範例。

**/變更**

- Python:現在通過`properties`屬性公開了識別結果的其他屬性。
- 對於其他開發和除錯支援,您可以將 SDK 紀錄記錄和診斷資訊重定向到紀錄檔中([此處](how-to-use-logging.md)的更多詳細資訊)。
- JavaScript:提高音訊處理性能。

**錯誤修復**

- Mac/iOS:修復了無法建立與語音服務的連接導致長時間等待的錯誤。
- Python:改進 Python 回檔中參數的錯誤處理。
- JavaScript:修復了在請求會話時結束的語音錯誤狀態報告。

## <a name="speech-sdk-131-2019-february-refresh"></a>語音 SDK 1.3.1:2019 年 2 月刷新

這是一個錯誤修復版本,僅影響本機/託管 SDK。 它不會影響 SDK 的 JAVAScript 版本。

**錯誤修復**

- 使用麥克風輸入時修復了記憶體洩漏。 基於流的或檔輸入不受影響。

## <a name="speech-sdk-130-2019-february-release"></a>語音 SDK 1.3.0:2019 年 2 月版本

**新功能**

- 語音 SDK`AudioConfig`支援透過 類選擇輸入麥克風。 這允許您從非預設麥克風將音訊數據流式傳輸到語音服務。 有關詳細資訊,請參閱描述[音訊輸入設備選擇](how-to-select-audio-input-devices.md)的文檔。 此功能尚不在 JAVAScript 中提供。
- 語音 SDK 現在支援搶鮮版 (Beta) Unity。 通過[GitHub 範例儲存庫](https://aka.ms/csspeech/samples)中的問題部分提供回饋。 此版本可在 Windows x86 和 x64 (桌面或通用 Windows 平台應用程式) 以及 Android (ARM32/64、x86) 上支援 Unity。 詳情請參閱 [Unity 快速入門](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)。
- 不再需要該檔`Microsoft.CognitiveServices.Speech.csharp.bindings.dll`(在以前的版本中提供)。 該功能現已集成到核心 SDK 中。

**範例**

[範例存放庫](https://aka.ms/csspeech/samples)有提供下列新內容：

- 的其他範例`AudioConfig.FromMicrophoneInput`。
- 額外的 Python 範例，適用於意圖辨識和翻譯。
- 用於在 iOS`Connection`中使用物件的其他範例。
- 額外的 Java 範例，適用於以音訊輸出進行翻譯。
- [批次轉譯 REST API](batch-transcription.md) 的新使用範例。

**/變更**

- Python
  - 改進了 參數驗證`SpeechConfig`和中的錯誤消息。
  - 添加對`Connection`對對象的支援。
  - 支援在 Windows 上使用 32 位元的 Python (x86)。
  - 適用於 Python 的語音 SDK 已不再是搶鮮版 (Beta)。
- iOS
  - SDK 現在是根據 iOS SDK 12.1 版來建置。
  - SDK 現在支援 iOS 9.2 版和更新版本。
  - 改善參考文件，並修正數個屬性名稱。
- JavaScript
  - 添加對`Connection`對對象的支援。
  - 為配套的 JavaScript 新增了類型定義檔
  - 初次支援和實作片語提示。
  - 傳回含有服務 JSON 的屬性集合以供辨識
- Windows DLL 現在會包含版本資源。
- 如果創建識別器`FromEndpoint`,則可以將參數直接添加到終結點 URL。 使用`FromEndpoint`無法通過標準配置屬性配置識別器。

**錯誤修復**

- 空的 Proxy 使用者名稱和 Proxy 密碼之前不會正確處理。 在此版本中，如果您將 Proxy 使用者名稱和 Proxy 密碼設定為空字串，系統將不會在連線到 Proxy 時提交出去。
- 在某些語言&nbsp;/環境中，SDK 所建立的工作階段識別碼並不一定會真正隨機。 添加了隨機生成器初始化以解決此問題。
- 改善授權權杖的處理方式。 如果要使用授權權杖,請在中指定`SpeechConfig`,並將訂閱密鑰留空。 然後如往常般建立辨識器。
- 在某些情況下,`Connection`物件未正確釋放。 已修正此問題。
- JavaScript 範例已修正，現在也能夠在 Safari 上支援以音訊輸出合成翻譯。

## <a name="speech-sdk-121"></a>語音 SDK 1.2.1

這是僅限 JavaScript 的版本。 未新增任何功能。 已進行下列修正：

- 會在 turn.end 而非 speech.end 引發資料流的結束。
- 修正音訊幫浦中的 BUG，此 BUG 會導致系統未在目前的傳送失敗時排程下一次傳送。
- 修正使用驗證權杖來進行的連續辨識。
- 修正不同辨識器/端點的 BUG。
- 文件改進。

## <a name="speech-sdk-120-2018-december-release"></a>語音 SDK 1.2.0:2018-12 月版本

**新功能**

- Python
  - 這一版提供 Python 支援的搶鮮版 (Beta) (3.5 和更新版本)。 有關詳細資訊,請參閱此處*(快速入門-python.md)。
- JavaScript
  - 適用於 JavaScript 的語音 SDK 已開放原始碼。 原始程式碼位於 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)。
  - 我們現在支援 Node.js，您可以在[這裡](quickstart-js-node.md)找到更多資訊。
  - 音訊工作階段的長度限制已移除，重新連線會自動發生。
- `Connection` 物件
  - 從`Recognizer`中`Connection`可以訪問 物件。 此物件可讓您明確地起始服務連線，並訂閱連線和中斷連線事件。
    (此功能尚不在 JAVAScript 和 Python 中。
- Ubuntu 18.04 支援。
- Android
  - 已在 APK 產生期間啟用 ProGuard 支援。

**改進**

- 改進內部執行緒使用方式，並減少執行緒、鎖定、Mutex 的數目。
- 已改善錯誤回報 / 資訊。 在若干情況下,錯誤消息並未一直傳播出來。
- 已將 JavaScript 中的開發相依性更新為使用最新的模組。

**錯誤修復**

- 修復了由於`RecognizeAsync`類型 不匹配而導致的記憶體洩漏。
- 在某些情況下，例外狀況正在流失。
- 已修正翻譯事件引數的記憶體流失。
- 已修正在長時間執行的工作階段中重新連線的鎖定問題。
- 修復了可能導致翻譯失敗導致缺少最終結果的問題。
- C#: 如果`async`在 主線程中未等待操作,則有可能在非同步任務完成之前釋放辨識器。
- Java:修復了導致 JAVA VM 崩潰的問題。
- 目標C:固定枚舉映射;已傳回已辨識的`RecognizingIntent`意向 ,而不是 。
- JavaScript:將預設輸出格式設置為中的`SpeechConfig`「簡單」。
- JavaScript:刪除 JavaScript 和其他語言配置物件上的屬性之間的不一致。

**範例**

- 更新並修復了多個範例(例如輸出語音進行翻譯等)。
- 已在[範例存放庫](https://aka.ms/csspeech/samples)中新增 Node.js 範例。

## <a name="speech-sdk-110"></a>語音 SDK 1.1.0

**新功能**

- 支援 Android x86/x64。
- 代理支援:在物件`SpeechConfig`中,現在可以調用函數來設置代理資訊(主機名、埠、使用者名和密碼)。 此功能尚無法在 iOS 上取得。
- 改善的錯誤碼和訊息。 如果辨識傳回錯誤，系統現在會將 `Reason` (在取消的事件中) 或 `CancellationDetails` (在辨識結果中) 設為 `Error`。 取消的事件現在包含兩個額外的成員，`ErrorCode` 和 `ErrorDetails`。 如果伺服器隨報告的錯誤傳回其他錯誤資訊，現在將可以透過新成員取得這些資訊。

**改進**

- 已在辨識器設定中新增其他驗證，及新增其他錯誤訊息。
- 已改善對音訊檔案中長時間無聲之部分的處理。
- NuGet 套件：針對 .NET Framework 專案，其可防止使用 AnyCPU 設定來建置。

**錯誤修復**

- 已修正在辨識器中找到的幾個例外狀況。 此外,異常將被捕獲並轉換為`Canceled`事件。
- 修正屬性管理中的記憶體流失。
- 已修正音訊輸入可能會損毀辨識器的錯誤。
- 已修正工作階段將事件停止之後，仍然可收到事件的錯誤。
- 已修正執行緒中的一些競爭條件。
- 已修正可能會導致當機的 iOS 相容性問題。
- 已改善對 Android 麥克風支援的穩定性。
- 已修正 JavaScript 中的辨識器會忽略辨識語言的錯誤。
- 修復了阻止在 JavaScript`EndpointId`中設置 (在某些情況下)的錯誤。
- 在 JavaScript 中更改了 AddIntent 中的`AddIntent`參數順序,並添加了缺少的 JavaScript 簽名。

**範例**

- 添加了C++和C# 示例,用於[在示例存儲庫](https://aka.ms/csspeech/samples)中拉取和推送流使用方式。

## <a name="speech-sdk-101"></a>語音 SDK 1.0.1

穩定性改進及 Bug 修正：

- 已修正處置辨識器時因競爭條件而可能導致的嚴重錯誤
- 已修正未設定屬性時可能導致的嚴重錯誤。
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

- 使用此版本,將引入一些重大更改。
  有關詳細資訊,請檢視[此頁面](https://aka.ms/csspeech/breakingchanges_1_0_0)。

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

**錯誤修復**

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

- 識別事件:`NoMatch`事件型態已合併到`Error`事件中 。
- C# 中的語音輸出格式已重新命名`OutputFormat`, 以便與C++保持一致。
- `AudioInputStream` 介面部分方法的傳回型別已稍微變更：
  - 在 Java 中，`read` 方法現在會傳回 `long` 而不是 `int`。
  - 在 C# 中，`Read` 方法現在會傳回 `uint` 而不是 `int`。
  - 在 C++ 中，`Read` 和 `GetFormat` 方法現在會傳回 `size_t` 而不是 `int`。
- C++：音訊輸入串流的執行個體現在只能當作 `shared_ptr` 傳遞。

**錯誤修復**

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

**打破變革**

- 在 C# 中已從 `SpeechRecognitionResult.RecognizedText` 變更為 `SpeechRecognitionResult.Text`。

**錯誤修復**

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
