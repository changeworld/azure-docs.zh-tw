---
title: 版本資訊-語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務功能版本、改進、bug 修正和已知問題的執行記錄。
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 13a7250bc89b1c9f81996dfa4e15d7d4469779ab
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607868"
---
# <a name="release-notes"></a>版本資訊
## <a name="speech-sdk-1121-2020-june-release"></a>語音 SDK 1.12.1： 2020-六月版本
**語音 CLI （也稱為 SPX）**
-   已新增 CLI 內的說明搜尋功能：
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   已更新為使用新部署的 v3.0 Batch 和自訂語音 Api：
    -   `spx help batch examples`
    -   `spx help csr examples`

**新功能**
-   **C \# ，c + +**：說話者辨識預覽：這項功能可讓說話者識別（說話者是誰？）和說話者驗證（這是他們宣稱為嗎？）。 從[總覽](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/speaker-recognition-overview)開始，閱讀[說話者辨識基本概念文章](https://docs.microsoft.com/azure/cognitive-services/speech-service/speaker-recognition-basics)，或[API 參考](https://docs.microsoft.com/rest/api/speakerrecognition/)檔。

**Bug 修正**
-   **C \# ，c + +**：固定麥克風錄製在1.12 中無法以說話者辨識的方式運作。
-   **JavaScript**：針對 FireFox 中的文字轉換語音以及 MacOS 和 iOS 上的 Safari 進行修正。
-   修正使用8通道串流時，交談轉譯的 Windows 應用程式驗證器存取違規損毀。
-   修正多重裝置對話轉譯時，Windows 應用程式驗證器存取違規損毀的問題。

**範例**
-   **C #**：說話者辨識的程式[代碼範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition)。
-   **C + +**：說話者辨識的程式[代碼範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition)。
-   **JAVA**：在 Android 上進行意圖辨識的程式[代碼範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition)。 

**COVID-19 簡易測試：** 由於在過去幾周的遠端工作，我們無法像平常一樣執行手動驗證測試。 我們尚未進行任何變更，我們認為可能會中斷任何專案，而且我們的自動化測試全都通過。 萬一我們錯過了什麼事，請在[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)上讓我們知道。<br>
保持良好狀態！


## <a name="speech-sdk-1120-2020-may-release"></a>語音 SDK 1.12.0： 2020-可能發行
**語音 CLI （也稱為 SPX）**
- **SPX**是新的命令列工具，可讓您從命令列執行辨識、合成、翻譯、批次轉譯和自訂語音管理。 使用它來測試語音服務，或編寫您需要執行的語音服務工作的腳本。 下載工具並閱讀[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-overview)的檔。

**新功能**
- **Go**：[語音辨識](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go)和[自訂語音助理](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go)的新 Go 語言支援。 在[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-go)設定您的開發環境。 如需範例程式碼，請參閱下面的範例一節。 
- **JavaScript**：已新增文字轉換語音的瀏覽器支援。 請參閱[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech-audio-file?pivots=programming-language-JavaScript)的檔。
- **C + +、c #、JAVA**： `KeywordRecognizer` Windows、Android、Linux & iOS 平臺上支援的新物件和 api。 請閱讀[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-overview)的檔。 如需範例程式碼，請參閱下面的範例一節。 
- **JAVA**：已新增具有翻譯支援的多重裝置交談。 請參閱[這裡](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.transcription)的參考檔。

**改進 & 優化**
- **JavaScript**：優化的瀏覽器麥克風實現改善語音辨識準確度。
- **JAVA**：使用直接 JNI 執行而不 SWIG 的重構系結。 這可減少適用于 Windows、Android、Linux 和 Mac 之所有 JAVA 套件的系結大小乘以10倍，並更輕鬆地進一步開發語音 SDK JAVA。
- **Linux**：更新支援[檔](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux)，其中包含最新的 RHEL 7 特定注意事項。
- 改善連線邏輯，以便在服務和網路錯誤發生時多次嘗試連接。
- 已更新[Portal.azure.com](https://portal.azure.com)語音快速入門頁面，以協助開發人員在 azure 語音旅程中進行下一個步驟。

**Bug 修正**
- **C #、JAVA**：已修正在 Linux ARM （32和64位）上載入 SDK 程式庫的[問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587)。
- **C #**：已針對 TranslationRecognizer、IntentRecognizer 和 Connection 物件，修正原生控制碼的明確處置。
- **C #**：已修正 ConversationTranscriber 物件的音訊輸入存留期管理。
- 已修正 `IntentRecognizer` 從簡單片語辨識意圖時，未正確設定結果原因的問題。
- `SpeechRecognitionEventArgs`已修正未正確設定結果位移的問題。
- 已修正 SDK 在開啟 websocket 連線之前嘗試傳送網路訊息的競爭條件。 在加入參與者時，是可重現的 `TranslationRecognizer` 。
- 已修正關鍵字辨識器引擎中的記憶體流失。

**範例**
- **Go**：新增[語音辨識](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go)和[自訂語音助理](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go)的快速入門。 [在這裡](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples)尋找範例程式碼。 
- **JavaScript**：新增[文字到語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech?pivots=programming-language-javascript)轉換、[翻譯](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/translate-speech-to-text?pivots=programming-language-javascript)和[意圖辨識](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition?pivots=programming-language-javascript)的快速入門。
- [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)和[JAVA](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)的關鍵字辨識範例（Android）。  

**COVID-19 簡易測試：** 由於在過去幾周的遠端工作，我們無法像平常一樣執行手動驗證測試。 我們尚未進行任何變更，我們認為可能會中斷任何專案，而且我們的自動化測試全都通過。 萬一我們錯過了什麼事，請在[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)上讓我們知道。<br>
保持良好狀態！

## <a name="speech-sdk-1110-2020-march-release"></a>語音 SDK 1.11.0： 2020-3 月發行
**新功能**
- Linux：新增了 Red Hat Enterprise Linux （RHEL）/Centos 作為基礎 7 x64 的支援，並提供如何設定系統 for Speech SDK 的[指示](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7)。
- Linux：已在 Linux ARM32 和 ARM64 上新增對 .NET Core c # 的支援。 您可以在[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux)深入了解. 
- C #，c + +：已 `UtteranceId` 在中新增 `ConversationTranscriptionResult` ，在所有中繼和最終語音辨識結果中都是一致的識別碼。 [C #](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet)、 [c + +](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)的詳細資料。
- Python：已新增的支援 `Language ID` 。 請參閱[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)存放庫中的 speech_sample .py。
- Windows：已針對所有 win32 主控台應用程式，在 Windows 平臺上新增壓縮的音訊輸入格式支援。 詳細資料請參閱[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)。 
- JavaScript：支援 NodeJS 中的語音合成（文字轉換語音）。 請按一下[這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech)進一步了解。 
- JavaScript：加入新的 API，以啟用所有傳送和接收訊息的檢查。 請按一下[這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript)進一步了解。 
        
**Bug 修正**
- C #，c + +：已修正問題，因此 `SendMessageAsync` 現在會將二進位訊息當做二進位類型傳送。 [C #](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_)、 [c + +](https://docs.microsoft.com/cpp/cognitive-services/speech/connection)的詳細資料。
- C #、c + +：修正 `Connection MessageReceived` 如果在 `Recognizer` 物件之前處置，使用事件可能會造成損毀的問題 `Connection` 。 [C #](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet)、 [c + +](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived)的詳細資料。
- Android：麥克風的音訊緩衝區大小從800ms 減少到100毫秒，以改善延遲。
- Android：已修正 Android Studio 中 x86 Android 模擬器的[問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563)。
- JavaScript：已使用 API 在中國新增區域的支援 `fromSubscription` 。 詳細資料請參閱[這裡](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-)。 
- JavaScript：針對來自 NodeJS 的連線失敗，新增更多錯誤資訊。
        
**範例**
- Unity：意圖辨識公用範例是固定的，其中 LUIS json 匯入失敗。 詳細資料請參閱[這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)。
- Python：針對新增的範例 `Language ID` 。 詳細資料請參閱[這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)。
    
**Covid19 簡易測試：** 由於在過去幾周的遠端工作，我們無法像平常一樣執行手動的裝置驗證測試。 其中一個範例是在 Linux、iOS 和 macOS 上測試麥克風輸入和喇叭輸出。 我們尚未進行任何變更，我們認為可能會在這些平臺上中斷任何專案，而且我們的自動化測試全都通過。 萬一我們錯過了什麼事，請在[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)上讓我們知道。<br> 感謝您繼續支援。 如往常，請在[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)或[Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731)上張貼問題或意見反應。<br>
保持良好狀態！

## <a name="speech-sdk-1100-2020-february-release"></a>語音 SDK 1.10.0： 2020-二月發行

**新功能**

 - 已新增 Python 套件以支援 Python 的新3.8 版本。
 - Red Hat Enterprise Linux （RHEL）/Centos 作為基礎 8 x64 支援（c + +、c #、JAVA、Python）。
   > [!NOTE] 
   > 客戶必須根據[這些指示](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)來設定 OpenSSL。
 - 適用于 Debian 和 Ubuntu 的 Linux ARM32 支援。
 - DialogServiceConnector 現在支援在 BotFrameworkConfig 上使用選擇性的「bot 識別碼」參數。 此參數可讓您使用多個 Direct Line 語音 bot 搭配單一 Azure 語音資源。 若未指定參數，則會使用預設的 bot （由 Direct Line 語音訊道設定頁面所決定）。
 - DialogServiceConnector 現在具有 SpeechActivityTemplate 屬性。 Direct Line 語音會使用此 JSON 字串的內容，在所有連線到 Direct Line 語音 bot 的活動中預先填入各種支援的欄位，包括自動產生以回應語音辨識之類事件的活動。
 - TTS 現在會使用訂用帳戶金鑰進行驗證，減少建立合成器後第一個合成結果的第一個位元組延遲。
 - 已更新19個地區設定的語音辨識模型，以減少18.6% （es）的平均文字錯誤率降低 es-MX、fr-CA、fr-fr、it-IT、ja-jp、ko、巴西、zh-CN、zh-HK、nb-NO、fi、ru-RU、pl-PL、CA ES、zh-幼圓、th、pt、tr、。 新的模型會在多個網域中帶來顯著的改良，包括聽寫、話務中心轉譯和影片索引編制案例。

**Bug 修正**

 - 修正了對話 Transcriber 在 JAVA Api 中未正確等待的錯誤（bug） 
 - 適用于 Xamarin GitHub 的 Android x86 模擬器修正程式[問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - 新增遺失的（Get |Set）要 AudioConfig 的屬性方法
 - 修正無法在連接失敗時停止 audioDataStream 的 TTS bug
 - 使用不含區域的端點會導致對話轉譯器的 USP 失敗
 - 通用 Windows 應用程式中的識別碼產生現在會使用適當的唯一 GUID 演算法;它先前和無意中會預設為 stub 的執行，通常會在大型的互動集合上產生衝突。
 
 **範例**
 
 - 使用語音 SDK 搭配[unity 麥克風和推播模式串流](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)的 Unity 範例

**其他變更**

 - [適用于 Linux 的 OpenSSL 設定檔已更新](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>語音 SDK 1.9.0： 2020-1 月版本

**新功能**

- 多裝置交談：將多個裝置連接到相同的語音或文字型交談，並選擇性地轉譯它們之間傳送的訊息。 請在[這篇文章](multi-device-conversation.md)中深入瞭解。 
- 已新增 aar 套件的關鍵字辨識支援，並已新增 x86 和 x64 類別的支援。 
- 目標-C： `SendMessage` 和 `SetMessageProperty` 方法已加入至 `Connection` 物件。 請參閱[這裡](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)的檔。
- TTS c + + api 現在支援 `std::wstring` 合成文字輸入，因此在將 wstring 傳遞給 SDK 之前，不需要將其轉換成字串。 請參閱[這裡](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)的詳細資料。 
- C #：[語言識別項](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp)和[來源語言](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp)設定現在可供使用。
- JavaScript：將功能新增至 `Connection` 物件，以從語音服務以回呼的形式傳遞自訂訊息 `receivedServiceMessage` 。
- JavaScript：已新增的支援， `FromHost API` 以輕鬆搭配內部內部部署的容器和主權雲端使用。 請參閱[這裡](speech-container-howto.md)的檔。
- JavaScript：我們現在接受 `NODE_TLS_REJECT_UNAUTHORIZED` [orgads](https://github.com/orgads)的貢獻。 請參閱[這裡](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)的詳細資料。

**重大變更**

- `OpenSSL`已更新至版本 1.1.1 b，並以靜態方式連結至適用于 Linux 的語音 SDK 核心程式庫。 如果您的收件匣尚未 `OpenSSL` 安裝到系統中的目錄，這可能會造成中斷 `/usr/lib/ssl` 。 如要解決此問題，請參閱語音 SDK 檔底下的[檔](how-to-configure-openssl-linux.md)。
- 我們已將 c # 傳回的資料類型 `WordLevelTimingResult.Offset` 從變更為 `int` `long` ，以便 `WordLevelTimingResults` 在語音資料長度超過2分鐘時，允許存取。
- `PushAudioInputStream``PullAudioInputStream`現在會將 wav 標頭資訊傳送至以為基礎的語音服務，並在 `AudioStreamFormat` 建立時選擇性地指定。 客戶現在必須使用[支援的音訊輸入格式](how-to-use-audio-input-streams.md)。 任何其他格式都會取得次佳的辨識結果，或可能會造成其他問題。 

**Bug 修正**

- 請參閱 `OpenSSL` 上述重大變更底下的更新。 我們已修正 Linux 和 JAVA 中間歇性損毀和效能問題（在高負載下鎖定爭用）。 
- JAVA：改善高並行處理案例中的物件關閉。
- 已重構我們的 NuGet 套件。 我們已移除在 lib 資料夾底下的三個複本 `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` ，讓 NuGet 套件變得更小且更快速下載，而且我們已新增編譯某些 c + + 原生應用程式所需的標頭。
- 已修正[這裡](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)的快速入門範例。 這些會結束，而不會顯示 Linux、macOS、Windows 上的「找不到麥克風」例外狀況。
- 修正了具有長語音辨識的 SDK 損毀，會導致特定程式碼路徑（如[此範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)）。
- 已修正 Azure Web 應用程式環境中的 SDK 部署錯誤，以解決[此客戶問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)。
- 已修正使用多個 `<voice>` 標記或 `<audio>` 標記來解決[此客戶問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)時的 TTS 錯誤。 
- 修正從擱置中復原 SDK 時的 TTS 401 錯誤。
- JavaScript：已修正音訊資料的迴圈匯入，感謝[euirim](https://github.com/euirim)的貢獻。 
- JavaScript：新增了設定服務屬性的支援，如1.7 中所新增。
- JavaScript：已修正連接錯誤可能導致連續、不成功的 websocket 重新連線嘗試的問題。

**範例**

- 已[在此處](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)新增適用于 Android 的關鍵字辨識範例。
- 在[此](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)新增伺服器案例的 TTS 範例。
- [在這裡](quickstarts/multi-device-conversation.md)為 c # 和 c + + 新增多裝置對話快速入門。

**其他變更**

- Android 上的優化 SDK 核心程式庫大小。
- 1.9.0 和更新版本中的 SDK `int` 在 `string` 對話 Transcriber 的 [語音簽名版本] 欄位中支援和類型。

## <a name="speech-sdk-180-2019-november-release"></a>語音 SDK 1.8.0： 2019-11 月版本

**新功能**

- 已新增 `FromHost()` API，以輕鬆搭配內部內部部署的容器和主權雲端使用。
- 已新增語音辨識的自動來源語言偵測（以 JAVA 和 c + +）
- 已新增 `SourceLanguageConfig` 語音辨識的物件，用來指定預期的來來源語言（在 JAVA 和 c + + 中）
- 已 `KeywordRecognizer` 透過 NuGet 和 Unity 套件新增 Windows （UWP）、Android 和 iOS 的支援
- 已新增遠端對話 JAVA API，以在非同步批次中執行對話轉譯。

**重大變更**

- 對話 Transcriber 功能已移至命名空間底下 `Microsoft.CognitiveServices.Speech.Transcription` 。
- 對話 Transcriber 方法的一部分會移至新的 `Conversation` 類別。
- 已捨棄32位（ARMv7 和 x86） iOS 的支援

**Bug 修正**

- 如果 `KeywordRecognizer` 使用本機時沒有有效的語音服務訂用帳戶金鑰，請修正損毀的問題

**範例**

- 適用于的 Xamarin 範例`KeywordRecognizer`
- 的 Unity 範例`KeywordRecognizer`
- 自動來源語言偵測的 c + + 和 JAVA 範例。

## <a name="speech-sdk-170-2019-september-release"></a>語音 SDK 1.7.0： 2019-9 月版

**新功能**

- 已在通用 Windows 平臺（UWP）、Android 和 iOS 上新增 Xamarin 的搶鮮版（Beta）支援
- 已新增 Unity 的 iOS 支援
- 已 `Compressed` 針對 Android、iOS 和 Linux 上的 ALaw、Mulaw、FLAC 新增輸入支援
- 已 `SendMessageAsync` 在 `Connection` 將訊息傳送至服務的類別中新增
- 已 `SetMessageProperty` 在 `Connection` 用於設定訊息屬性的類別中新增
- TTS 已新增 JAVA （Jre 和 Android）、Python、Swift 和目標-C 的系結
- TTS 新增了 macOS、iOS 和 Android 的播放支援。
- 已新增 TTS 的「字邊界」資訊。

**Bug 修正**

- 已修正適用于 Android 的 Unity 2019 的 IL2CPP 組建問題
- 已修正未正確處理 wav 檔案輸入中格式錯誤之標頭的問題
- 已修正在某些連接屬性中，Uuid 不是唯一的問題
- 已修正 Swift 系結中有關 null 屬性規範的幾個警告（可能需要較小的程式碼變更）
- 已修正在網路負載下導致 websocket 連線關閉強制的錯誤
- 已修正 Android 的問題，有時會導致使用重複的印象識別碼`DialogServiceConnector`
- 改善跨多回合互動的連線，以及發生錯誤時的報告（透過 `Canceled` 事件）`DialogServiceConnector`
- `DialogServiceConnector`會話啟動現在會正確地提供事件，包括在 `ListenOnceAsync()` 使用中時呼叫`StartKeywordRecognitionAsync()`
- 解決與所接收之活動相關聯的損毀 `DialogServiceConnector`

**範例**

- Xamarin 的快速入門
- 已更新使用 Linux ARM64 資訊的 CPP 快速入門
- 已更新 Unity 快速入門與 iOS 資訊

## <a name="speech-sdk-160-2019-june-release"></a>語音 SDK 1.6.0： 2019-六月版本

**範例**

- UWP 和 Unity 上的文字轉換語音的快速入門範例
- IOS 上 Swift 的快速入門範例
- 適用于語音 & 意圖辨識和翻譯的 Unity 範例
- 已更新的快速入門範例`DialogServiceConnector`

**/變更**

- 對話方塊命名空間：
  - `SpeechBotConnector` 已重新命名為 `DialogServiceConnector`
  - `BotConfig` 已重新命名為 `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`已重新對應至`DialogServiceConfig::FromBotSecret()`
  - 重新命名之後，仍可繼續支援所有現有的 Direct Line 語音用戶端
- 更新 TTS REST 介面卡以支援 proxy、持續連線
- 當傳遞不正確區域時，改善錯誤訊息
- Swift/目標-C：
  - 改良的錯誤報表：可能導致錯誤的方法現在會出現在兩個版本中：一個會公開 `NSError` 物件以進行錯誤處理，另一個則會引發例外狀況。 前者會公開給 Swift。 這種變更需要採用原音到現有的 Swift 程式碼。
  - 改良的事件處理

**Bug 修正**

- 針對 TTS 的修正： `SpeakTextAsync` 未來傳回而不等待，直到音訊完成呈現為止
- 修正 c # 中的封送處理字串，以啟用完整語言支援
- 修正 .NET core 應用程式問題，以在範例中以 net461 目標架構載入核心程式庫
- 修正在範例中將原生程式庫部署至輸出檔案夾的偶爾問題
- 可靠地關閉 web 通訊端的修正
- 修正在 Linux 上極繁重的負載下開啟連線時可能發生的損毀
- 修正 macOS 架構配套中遺漏的中繼資料
- 修正 `pip install --user` Windows 上的問題

## <a name="speech-sdk-151"></a>語音 SDK 1.5。1

這是 bug 修正版本，只會影響原生/受控 SDK。 它不會影響 JavaScript 版本的 SDK。

**Bug 修正**

- 修正搭配對話轉譯使用時的 FromSubscription。
- 修正語音助理關鍵字找出中的 bug。

## <a name="speech-sdk-150-2019-may-release"></a>語音 SDK 1.5.0： 2019-可能發行

**新功能**

- 關鍵字找出（KWS.TABLE）現在適用于 Windows 和 Linux。 KWS.TABLE 功能可能適用于任何麥克風類型，但官方 KWS.TABLE 支援目前僅限於在 Azure Kinect DK 硬體或語音裝置 SDK 中找到的麥克風陣列。
- 片語提示功能可透過 SDK 取得。 如需詳細資訊，請參閱[這裡](how-to-phrase-lists.md)。
- 對話轉譯功能可透過 SDK 取得。 請參閱[這裡](conversation-transcription-service.md)。
- 使用 Direct Line 語音訊道新增語音助理支援。

**範例**

- 已新增 SDK 支援的新功能或新服務的範例。

**/變更**

- 新增各種辨識器屬性來調整服務行為或服務結果（例如遮罩不雅內容和其他專案）。
- 即使您已建立辨識器，您現在也可以透過標準設定屬性來設定辨識器 `FromEndpoint` 。
- 目標-C： `OutputFormat` 屬性已加入至 `SPXSpeechConfiguration` 。
- SDK 現在支援 Debian 9 作為 Linux 散發套件。

**Bug 修正**

- 已修正說話者資源在文字轉換語音中太早解構的問題。

## <a name="speech-sdk-142"></a>語音 SDK 1.4。2

這是 bug 修正版本，只會影響原生/受控 SDK。 它不會影響 JavaScript 版本的 SDK。

## <a name="speech-sdk-141"></a>語音 SDK 1.4。1

這是僅限 JavaScript 的版本。 未新增任何功能。 已進行下列修正：

- 防止 web 套件載入 HTTPs-proxy 代理程式。

## <a name="speech-sdk-140-2019-april-release"></a>語音 SDK 1.4.0： 2019-4 月版本

**新功能**

- SDK 現在支援文字轉換語音服務作為搶鮮版（Beta）。 它在 Windows 和 Linux Desktop （從 c + + 和 c #）中受到支援。 如需詳細資訊，請參閱[文字轉換語音的總覽](text-to-speech.md#get-started)。
- SDK 現在支援 MP3 和 Opus/OGG 音訊檔案作為串流輸入檔。 這項功能僅適用于 c + + 和 c # 的 Linux，且目前為搶鮮版（如需更多[詳細資料）](how-to-use-codec-compressed-audio-input-streams.md)。
- 適用于 JAVA、.NET core、c + + 和目標的語音 SDK 已取得 macOS 支援。 MacOS 的目標-C 支援目前為搶鮮版（Beta）。
- iOS：適用于 iOS 的語音 SDK （目標-C）現在也會發佈為 CocoaPod。
- JavaScript：支援非預設麥克風作為輸入裝置。
- JavaScript：適用于 node.js 的 Proxy 支援。

**範例**

- 已新增在 macOS 上使用語音 SDK 搭配 c + + 和以目標為 C 的範例。
- 示範如何使用文字轉換語音服務的範例。

**/變更**

- Python：辨識結果的其他屬性現在會透過屬性公開 `properties` 。
- 如需其他開發和偵錯工具支援，您可以將 SDK 記錄和診斷資訊重新導向至記錄檔（[這裡](how-to-use-logging.md)提供更多詳細資料）。
- JavaScript：改善音訊處理效能。

**Bug 修正**

- Mac/iOS：已修正無法建立語音服務連線時，會導致長時間等候的錯誤。
- Python：改善 Python 回呼中引數的錯誤處理。
- JavaScript：已修正 RequestSession 上語音的錯誤狀態報表已結束。

## <a name="speech-sdk-131-2019-february-refresh"></a>語音 SDK 1.3.1： 2019-2 月重新整理

這是 bug 修正版本，只會影響原生/受控 SDK。 它不會影響 JavaScript 版本的 SDK。

**錯誤 (bug) 修正**

- 已修正使用麥克風輸入時的記憶體遺漏。 資料流程型或檔案輸入不受影響。

## <a name="speech-sdk-130-2019-february-release"></a>語音 SDK 1.3.0： 2019-二月發行

**新功能**

- 語音 SDK 支援透過類別來選取輸入麥克風 `AudioConfig` 。 這可讓您從非預設的麥克風將音訊資料串流處理至語音服務。 如需詳細資訊，請參閱說明[音訊輸入裝置選取](how-to-select-audio-input-devices.md)的檔。 JavaScript 尚未提供這項功能。
- 語音 SDK 現在支援搶鮮版 (Beta) Unity。 透過[GitHub 範例存放庫](https://aka.ms/csspeech/samples)中的問題區段來提供意見反應。 此版本可在 Windows x86 和 x64 (桌面或通用 Windows 平台應用程式) 以及 Android (ARM32/64、x86) 上支援 Unity。 詳情請參閱 [Unity 快速入門](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)。
- `Microsoft.CognitiveServices.Speech.csharp.bindings.dll`不再需要檔案（在先前的版本中隨附）。 此功能現在已整合到 core SDK 中。

**範例**

[範例存放庫](https://aka.ms/csspeech/samples)有提供下列新內容：

- 的其他範例 `AudioConfig.FromMicrophoneInput` 。
- 額外的 Python 範例，適用於意圖辨識和翻譯。
- `Connection`在 iOS 中使用物件的其他範例。
- 額外的 Java 範例，適用於以音訊輸出進行翻譯。
- [批次轉譯 REST API](batch-transcription.md) 的新使用範例。

**/變更**

- Python
  - 改善中的參數驗證和錯誤訊息 `SpeechConfig` 。
  - 新增物件的支援 `Connection` 。
  - 支援在 Windows 上使用 32 位元的 Python (x86)。
  - 適用於 Python 的語音 SDK 已不再是搶鮮版 (Beta)。
- iOS
  - SDK 現在是根據 iOS SDK 12.1 版來建置。
  - SDK 現在支援 iOS 9.2 版和更新版本。
  - 改善參考文件，並修正數個屬性名稱。
- JavaScript
  - 新增物件的支援 `Connection` 。
  - 為配套的 JavaScript 新增了類型定義檔
  - 初次支援和實作片語提示。
  - 傳回含有服務 JSON 的屬性集合以供辨識
- Windows DLL 現在會包含版本資源。
- 如果您建立辨識器 `FromEndpoint` ，您可以將參數直接新增至端點 URL。 使用 `FromEndpoint` 時，您無法透過標準設定屬性來設定辨識器。

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

## <a name="speech-sdk-120-2018-december-release"></a>語音 SDK 1.2.0： 2018-十二月版本

**新功能**

- Python
  - 這一版提供 Python 支援的搶鮮版 (Beta) (3.5 和更新版本)。 如需詳細資訊，請參閱這裡] （快速入門-python.md）。
- JavaScript
  - 適用於 JavaScript 的語音 SDK 已開放原始碼。 原始程式碼位於 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)。
  - 我們現在支援 Node.js，您可以在[這裡](quickstart-js-node.md)找到更多資訊。
  - 音訊工作階段的長度限制已移除，重新連線會自動發生。
- `Connection` 物件
  - `Recognizer`您可以從存取 `Connection` 物件。 此物件可讓您明確地起始服務連線，並訂閱連線和中斷連線事件。
    （JavaScript 和 Python 尚未提供這項功能）。
- Ubuntu 18.04 支援。
- Android
  - 已在 APK 產生期間啟用 ProGuard 支援。

**措施**

- 改進內部執行緒使用方式，並減少執行緒、鎖定、Mutex 的數目。
- 已改善錯誤回報 / 資訊。 在幾種情況下，錯誤訊息並未立即傳播出去。
- 已將 JavaScript 中的開發相依性更新為使用最新的模組。

**Bug 修正**

- 已修正由於中的類型不相符而造成的記憶體流失 `RecognizeAsync` 。
- 在某些情況下，例外狀況正在流失。
- 已修正翻譯事件引數的記憶體流失。
- 已修正在長時間執行的工作階段中重新連線的鎖定問題。
- 已修正可能會導致遺漏翻譯失敗之最終結果的問題。
- C #：如果在 `async` 主執行緒中未等候作業，則可能會在非同步工作完成之前處置辨識器。
- JAVA：已修正導致 JAVA VM 損毀的問題。
- 目標-C：固定列舉對應;傳回的是 RecognizedIntent，而不是 `RecognizingIntent` 。
- JavaScript：在中將預設輸出格式設定為「簡單」 `SpeechConfig` 。
- JavaScript：移除 JavaScript 和其他語言中 config 物件的屬性之間不一致的情況。

**範例**

- 已更新並修正數個範例（例如，用於轉譯的輸出語音等等）。
- 已在[範例存放庫](https://aka.ms/csspeech/samples)中新增 Node.js 範例。

## <a name="speech-sdk-110"></a>語音 SDK 1.1.0

**新功能**

- 支援 Android x86/x64。
- Proxy 支援：在 `SpeechConfig` 物件中，您現在可以呼叫函式來設定 proxy 資訊（主機名稱、埠、使用者名稱和密碼）。 此功能尚無法在 iOS 上取得。
- 改善的錯誤碼和訊息。 如果辨識傳回錯誤，系統現在會將 `Reason` (在取消的事件中) 或 `CancellationDetails` (在辨識結果中) 設為 `Error`。 取消的事件現在包含兩個額外的成員，`ErrorCode` 和 `ErrorDetails`。 如果伺服器隨報告的錯誤傳回其他錯誤資訊，現在將可以透過新成員取得這些資訊。

**措施**

- 已在辨識器設定中新增其他驗證，及新增其他錯誤訊息。
- 已改善對音訊檔案中長時間無聲之部分的處理。
- NuGet 套件：針對 .NET Framework 專案，其可防止使用 AnyCPU 設定來建置。

**Bug 修正**

- 已修正在辨識器中找到的幾個例外狀況。 此外，也會攔截例外狀況並轉換成 `Canceled` 事件。
- 修正屬性管理中的記憶體流失。
- 已修正音訊輸入可能會損毀辨識器的錯誤。
- 已修正工作階段將事件停止之後，仍然可收到事件的錯誤。
- 已修正執行緒中的一些競爭條件。
- 已修正可能會導致當機的 iOS 相容性問題。
- 已改善對 Android 麥克風支援的穩定性。
- 已修正 JavaScript 中的辨識器會忽略辨識語言的錯誤。
- 已修正錯誤，導致無法 `EndpointId` 在 JavaScript 中設定（在某些情況下為）。
- 已變更 JavaScript 中 AddIntent 的參數順序，並已新增遺漏的 javascript 簽章 `AddIntent` 。

**範例**

- 已在[範例存放庫](https://aka.ms/csspeech/samples)中新增提取和推入資料流程使用的 c + + 和 c # 範例。

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

- 在此版本中，會引進一些重大變更。
  請查看[此頁面](https://aka.ms/csspeech/breakingchanges_1_0_0)以取得詳細資料。

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

- 辨識事件： `NoMatch` 事件種類已合併至 `Error` 事件。
- C # 中的 SpeechOutputFormat 已重新命名為 `OutputFormat` ，以保持與 c + + 一致。
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
