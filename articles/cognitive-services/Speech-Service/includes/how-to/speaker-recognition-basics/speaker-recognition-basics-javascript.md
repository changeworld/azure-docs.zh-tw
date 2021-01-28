---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 08e4dbb4d16317efe33b308474ce5c32a7c47862
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948414"
---
在本快速入門中，您將了解使用語音 SDK 進行說話者辨識的基本設計模式，包括：

* 與文字相關，以及與文字無關的驗證
* 說話者辨識，用以識別語音群組之間的語音樣本
* 刪除語音設定檔

如需有關語音辨識概念的高階探討，請參閱 [概觀](../../../speaker-recognition-overview.md) 文章。

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [JavaScript 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 說話者辨識目前 *僅* 支援在 `westus` 區域中建立的 Azure 語音資源。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">適用於 JavaScript 的語音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>，才能執行動作。 根據您的平台，使用下列指示：

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">網頁瀏覽器 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

此外，根據目標環境而定，請使用下列其中一項：

# <a name="script"></a>[指令碼](#tab/script)

下載 <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">適用於 JavaScript 的語音 SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* 檔案並將其解壓縮，放在您的 HTML 檔案可存取的資料夾中。

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> 如果您是以網頁瀏覽器為目標，並使用 `<script>` 標籤，則不需要 `sdk` 前置詞。 `sdk` 前置詞是用來命名 `require` 模組的別名。

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

如需 `import` 的詳細資訊，請參閱<a href="https://javascript.info/import-export" target="_blank">匯出和匯入<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

# <a name="require"></a>[必要項目](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

如需 `require` 的相關資訊，請參閱<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">什麼是必要項目？<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

---

## <a name="import-dependencies"></a>匯入相依性

若要執行本文中的範例，請在 .js 檔案的頂端新增下列陳述式。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

這些陳述式會匯入必要的程式庫，並從您的環境變數取得您的語音服務訂用帳戶金鑰和區域。 此外，也會指定您將在下列工作中使用的音訊檔案路徑。

## <a name="create-helper-function"></a>建立 Helper 函式

新增下列 Helper 函式，將音訊檔案讀取至串流中，以供語音服務使用。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

在此函式中，您會使用 [AudioInputStream.createPushStream](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream#createpushstream-audiostreamformat-) 和 [AudioConfig.fromStreamInput](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) 方法來建立 [AudioConfig](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) 物件。 這個 `AudioConfig` 物件代表音訊串流。 在下列工作期間，您將使用其中幾個 `AudioConfig` 物件。

## <a name="text-dependent-verification"></a>文字相依驗證

說話者驗證是確認說話者符合已知或 **已註冊** 語音的動作。 第一個步驟是 **註冊** 一個語音設定檔，讓服務有一些內容可比較未來的語音樣本。 在此範例中，您會使用 **與文字相關的** 策略來註冊設定檔，這需要使用特定的傳遞片語來進行註冊和驗證。 如需支援的傳遞片語清單，請參閱 [參考文件](/rest/api/speakerrecognition/)。

### <a name="textdependentverification-function"></a>TextDependentVerification 函式

首先建立 `TextDependentVerification` 函式。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

此函式會使用 [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) 方法建立 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 物件。 請注意，`VoiceProfile` 有三種[類型](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype)：

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

在此情況下，您可將 `VoiceProfileType.TextDependentVerification` 傳遞至 `VoiceProfileClient.createProfileAsync`。

然後，呼叫您接下來會定義的兩個 Helper 函式：`AddEnrollmentsToTextDependentProfile` 和 `SpeakerVerify`。 最後，呼叫 [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) 以移除設定檔。

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile 函式

定義以下函式來註冊語音設定檔。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

在此函式中，您會呼叫稍早定義的 `GetAudioConfigFromFile` 函式，以從音訊樣本建立 `AudioConfig` 物件。 這些音訊樣本包含複雜密碼，例如 "My voice is my passport, verify me"。 然後，使用 [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) 方法來註冊這些音訊樣本。

### <a name="speakerverify-function"></a>SpeakerVerify 函式

如下所示，定義 `SpeakerVerify`。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

在此函式中，您會使用 [SpeakerVerificationModel.FromProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel#fromprofile-voiceprofile-) 方法建立 [SpeakerVerificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel) 物件，並傳入您稍早建立的 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 物件。

接下來，呼叫 [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) 方法來驗證音訊樣本，其中包含與您先前註冊的音訊樣本相同的複雜密碼。 `SpeechRecognizer.recognizeOnceAsync` 會傳回 [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult) 物件，其 `score` 屬性包含範圍 0.0-1.0 的相似度分數。 `SpeakerRecognitionResult` 物件也包含 [ResultReason](/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason) 類型的 `reason` 屬性。 如果驗證成功，`reason` 屬性應該具有 `RecognizedSpeaker` 值。

## <a name="text-independent-verification"></a>與文字無關的驗證

相對於 **與文字相關的** 驗證，**與文字無關的** 驗證是：

* 不需要說出特定的傳遞片語，可以說出任何內容
* 不需要三個音訊樣本，但是總計需要 20 秒的音訊

### <a name="textindependentverification-function"></a>TextIndependentVerification 函式

首先建立 `TextIndependentVerification` 函式。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

如同 `TextDependentVerification` 函式，此函式會使用 [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) 方法建立 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 物件。

在此情況下，您可將 `VoiceProfileType.TextIndependentVerification` 傳遞至 `createProfileAsync`。

接著，呼叫兩個 Helper 函式：您接下來會定義的 `AddEnrollmentsToTextIndependentProfile`，以及您已經定義的 `SpeakerVerify`。 最後，呼叫 [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) 以移除設定檔。

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

定義以下函式來註冊語音設定檔。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

在此函式中，您會呼叫稍早定義的 `GetAudioConfigFromFile` 函式，以從音訊樣本建立 `AudioConfig` 物件。 然後，使用 [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) 方法來註冊這些音訊樣本。

## <a name="speaker-identification"></a>說話者識別

說話者辨識是用來判斷從指定的已註冊語音群組說話者的 **身份**。 此流程類似於 **與文字無關的驗證**，主要差異在於能夠一次針對多個語音設定檔進行驗證，而不是針對單一設定檔進行驗證。

### <a name="textindependentidentification-function"></a>TextIndependentIdentification 函式

首先建立 `TextIndependentIdentification` 函式。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

如同 `TextDependentVerification` 和 `TextIndependentVerification` 函式，此函式會使用 [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) 方法建立 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 物件。

在此情況下，您可將 `VoiceProfileType.TextIndependentIdentification` 傳遞至 `VoiceProfileClient.createProfileAsync`。

接著，呼叫兩個 Helper 函式：您已經定義的 `AddEnrollmentsToTextIndependentProfile`，以及您接下來會定義的 `SpeakerIdentify`。 最後，呼叫 [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) 以移除設定檔。

### <a name="speakeridentify-function"></a>SpeakerIdentify 函式

如下所示，定義 `SpeakerIdentify` 函式。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

在此函式中，您會使用 [SpeakerIdentificationModel.fromProfiles](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel#fromprofiles-voiceprofile---) 方法建立 [SpeakerIdentificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel) 物件，並傳入您稍早建立的 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 物件。

接下來，呼叫 [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) 方法並傳入音訊樣本。
`SpeechRecognizer.recognizeOnceAsync` 會根據您用來建立 `SpeakerIdentificationModel` 的 `VoiceProfile` 物件，嘗試識別此音訊樣本的語音。 其會傳回 [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult) 物件，其 `profileId` 屬性會識別相符的 `VoiceProfile` (如果有的話)，而 `score` 屬性則包含範圍 0.0-1.0 的相似度分數。

## <a name="main-function"></a>Main 函式

最後如下所示，定義 `main` 函式。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

此函式會建立 [VoiceProfileClient](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient) 物件，該物件用於建立、註冊及刪除語音設定檔。 然後會呼叫您先前定義的函式。
