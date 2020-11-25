---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 489514068d83f7b2953732415ba066a2d4555df8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015463"
---
在本快速入門中，您將了解使用語音 SDK 進行說話者辨識的基本設計模式，包括：

* 與文字相關，以及與文字無關的驗證
* 說話者辨識，用以識別語音群組之間的語音樣本
* 刪除語音設定檔

如需有關語音辨識概念的高階探討，請參閱 [概觀](../../../speaker-recognition-overview.md) 文章。

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [C++ 快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 說話者辨識目前 *僅* 支援在 `westus` 區域中建立的 Azure 語音資源。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 根據您的平台，使用下列指示：

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>匯入相依性

若要執行本文中的範例，請在 .cpp 檔案的頂端新增下列陳述式。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)。 此類別包含訂用帳戶的相關資訊，例如您的金鑰和關聯的區域、端點、主機或授權權杖。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>文字相依驗證

說話者驗證是確認說話者符合已知或 **已註冊** 語音的動作。 第一個步驟是 **註冊** 一個語音設定檔，讓服務有一些內容可比較未來的語音樣本。 在此範例中，您會使用 **與文字相關的** 策略來註冊設定檔，這需要使用特定的傳遞片語來進行註冊和驗證。 如需支援的傳遞片語清單，請參閱 [參考文件](/rest/api/speakerrecognition/)。

### <a name="textdependentverification-function"></a>TextDependentVerification 函式

首先建立 `TextDependentVerification` 函式。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

此函式會使用 [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) 方法建立 [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) 物件。 請注意，`VoiceProfile` 有三種[類型](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype)：

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

在此情況下，您可將 `VoiceProfileType::TextDependentVerification` 傳遞至 `CreateProfileAsync`。

然後，呼叫您接下來會定義的兩個 Helper 函式：`AddEnrollmentsToTextDependentProfile` 和 `SpeakerVerify`。 最後，呼叫 [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) 以清除設定檔。

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile 函式

定義以下函式來註冊語音設定檔。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

在此函式中，您會在 `while` 迴圈中註冊音訊樣本，以追蹤註冊的剩餘樣本數和必要項目。 在每次反覆運算中，[EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) 會提示您對著麥克風說出傳遞片語，並將樣本新增至語音設定檔。

### <a name="speakerverify-function"></a>SpeakerVerify 函式

如下所示，定義 `SpeakerVerify`。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

在此函式中，您會使用 [SpeakerVerificationModel::FromProfile](/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile) 方法建立 [SpeakerVerificationModel](/cpp/cognitive-services/speech/speakerverificationmodel) 物件，並傳入您稍早建立的 [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) 物件。

接下來，[SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) 會提示您再次說出此傳遞片語，但這次會針對您的語音設定檔進行驗證，並傳回介於 0.0-1.0 的相似性分數。 根據傳遞片語是否相符，[SpeakerRecognitionResult](/cpp/cognitive-services/speech/speakerrecognitionresult) 物件也會傳回 `Accept` 或 `Reject`。

## <a name="text-independent-verification"></a>與文字無關的驗證

相對於 **與文字相關的** 驗證，**與文字無關的** 驗證是：

* 不需要說出特定的傳遞片語，可以說出任何內容
* 不需要三個音訊樣本，但是總計需要 20 秒的音訊

### <a name="textindependentverification-function"></a>TextIndependentVerification 函式

首先建立 `TextIndependentVerification` 函式。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

如同 `TextDependentVerification` 函式，此函式會使用 [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) 方法建立 [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) 物件。

在此情況下，您可將 `VoiceProfileType::TextIndependentVerification` 傳遞至 `CreateProfileAsync`。

接著，呼叫兩個 Helper 函式：您接下來會定義的 `AddEnrollmentsToTextIndependentProfile`，以及您已經定義的 `SpeakerVerify`。 最後，呼叫 [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) 以清除設定檔。

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

定義以下函式來註冊語音設定檔。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

在此函式中，您會在 `while` 迴圈中註冊音訊樣本，以追蹤註冊的剩餘和所需秒數。 在每次反覆運算中，[EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) 會提示您對著麥克風說話，並將樣本新增至語音設定檔。

## <a name="speaker-identification"></a>說話者識別

說話者辨識是用來判斷從指定的已註冊語音群組說話者的 **身份**。 此流程非常類似 **與文字無關的驗證**，主要差異在於能夠一次針對多個語音設定檔進行驗證，而不是針對單一設定檔進行驗證。

### <a name="textindependentidentification-function"></a>TextIndependentIdentification 函式

首先建立 `TextIndependentIdentification` 函式。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

如同 `TextDependentVerification` 和 `TextIndependentVerification` 函式，此函式會使用 [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) 方法建立 [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) 物件。

在此情況下，您可將 `VoiceProfileType::TextIndependentIdentification` 傳遞至 `CreateProfileAsync`。

接著，呼叫兩個 Helper 函式：您已經定義的 `AddEnrollmentsToTextIndependentProfile`，以及您接下來會定義的 `SpeakerIdentify`。 最後，呼叫 [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) 以清除設定檔。

### <a name="speakeridentify-function"></a>SpeakerIdentify 函式

如下所示，定義 `SpeakerIdentify` 函式。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

在此函式中，您會使用 [SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles)方法建立 [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speakeridentificationmodel) 物件。 `SpeakerIdentificationModel::FromProfiles` 會接受 [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) 物件的清單。 在此情況下，您只會傳入您稍早建立的 `VoiceProfile` 物件。 不過，如果您想要，可以傳入多個 `VoiceProfile` 物件，每個物件都是以來自不同語音的音訊樣本註冊。

接下來，[SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) 會提示您再次說話。 這次會比較您的語音與已註冊的語音設定檔，並傳回最相似的語音設定檔。

## <a name="main-function"></a>Main 函式

最後如下所示，定義 `main` 函式。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

此函式只會呼叫您先前定義的函式。 不過，首先會建立 [VoiceProfileClient](/cpp/cognitive-services/speech/voiceprofileclient) 物件和 [SpeakerRecognizer](/cpp/cognitive-services/speech/speakerrecognizer) 物件。

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

`VoiceProfileClient` 用於建立、註冊及刪除語音設定檔。 `SpeakerRecognizer` 用於針對一或多個已註冊的語音設定檔來驗證語音樣本。

## <a name="changing-audio-input-type"></a>變更音訊輸入類型

本文中的範例使用預設裝置麥克風做為音訊樣本的輸入來源。 不過，在您需要使用音訊檔案而非麥克風輸入的情況下，只需將以下這行：

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

變更為：

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

或以 [Audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput)取代任何使用的 `audio_config`。 您也可以採用混合輸入，使用麥克風進行註冊以及使用檔案進行驗證，例如。