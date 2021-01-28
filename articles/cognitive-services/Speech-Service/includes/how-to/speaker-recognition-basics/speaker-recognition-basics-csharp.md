---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: f29fd56bb5e9f32b37f6dcf1f67edd325b58a07d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948415"
---
在本快速入門中，您將了解使用語音 SDK 進行說話者辨識的基本設計模式，包括：

* 與文字相關，以及與文字無關的驗證
* 說話者辨識，用以識別語音群組之間的語音樣本
* 刪除語音設定檔

如需有關語音辨識概念的高階探討，請參閱 [概觀](../../../speaker-recognition-overview.md) 文章。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 說話者辨識目前 *僅* 支援在 `westus` 區域中建立的 Azure 語音資源。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝語音 SDK，才能執行動作。 根據您的平台，使用下列指示：

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>匯入相依性

若要執行本文中的範例，請在指令碼的頂端包含下列 `using` 陳述式。

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>建立語音設定

若要使用語音 SDK 來呼叫語音服務，您必須建立 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)。 在此範例中，您會使用訂用帳戶金鑰和區域來建立 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)。 您也會建立一些基本的重複使用程式碼，用於本文的其餘部分，而您可針對不同的自訂項目進行修改。

請注意，區域會設定為 `westus`，因為這是唯一支援該服務的區域。

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>文字相依驗證

說話者驗證是確認說話者符合已知或 **已註冊** 語音的動作。 第一個步驟是 **註冊** 一個語音設定檔，讓服務有一些內容可比較未來的語音樣本。 在此範例中，您會使用 **與文字相關的** 策略來註冊設定檔，這需要使用特定的傳遞片語來進行註冊和驗證。 如需支援的傳遞片語清單，請參閱 [參考文件](/rest/api/speakerrecognition/)。

一開始，請先在您的 `Program` 類別中建立下列函式，以註冊語音設定檔。

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

在此函式中，`await client.CreateProfileAsync()` 實際上會建立新的語音設定檔。 建立之後，您可以使用此範例中的 `AudioConfig.FromDefaultMicrophoneInput()`，透過您的預設輸入裝置擷取音訊，藉此指定輸入音訊樣本的方式。 接下來，您會在 `while` 迴圈中註冊音訊樣本，以追蹤註冊的剩餘樣本數和必要項目。 在每個反復項目中，`client.EnrollProfileAsync(profile, audioInput)` 會提示您對著麥克風說出傳遞片語，並將樣本新增至語音設定檔。

註冊完成之後，您會呼叫 `await SpeakerVerify(config, profile, profileMapping)` 以根據您剛才建立的設定檔進行驗證。 新增另一個函式來定義 `SpeakerVerify`。

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

在此函式中，您會傳遞剛建立的 `VoiceProfile` 物件以初始化模型，藉此進行驗證。 接下來，`await speakerRecognizer.RecognizeOnceAsync(model)` 會提示您再次說出此傳遞片語，但這次它會根據您的語音設定檔進行驗證，並傳回介於 0.0-1.0 的相似性分數。 根據傳遞片語是否相符，`result` 物件也會傳回 `Accept` 或 `Reject`。

接下來，修改您的 `Main()` 函式，以呼叫您所建立的新函式。 此外，請注意，您可以透過函式呼叫，建立由參考傳遞的 `Dictionary<string, string>`。 這是因為服務不允許以建立的 `VoiceProfile` 儲存人可閱讀的名稱，而且只會儲存用於隱私權的識別碼號碼。 在 `VerificationEnroll` 函式中，您會將含有新建立之識別碼的項目加上文字名稱，加入此字典中。 在需要顯示人類可閱讀名稱的應用程式開發案例中，**您必須將此對應儲存在某處，因為服務無法將其儲存**。

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

執行指令碼，系統會提示您說出語句 *我的語音就是我的通行證，請驗證我* 三次以進行註冊，另外還有一次以進行驗證。 傳回的結果是相似性分數，您可以用它來建立自己的自訂閾值以進行驗證。

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>與文字無關的驗證

相對於 **與文字相關的** 驗證，**與文字無關的** 驗證是：

* 不需要說出特定的傳遞片語，可以說出任何內容
* 不需要三個音訊樣本，但是總計 *需要* 20 秒的音訊

對 `VerificationEnroll` 函式進行幾項簡單的變更，即可切換至 **與文字無關的** 驗證。 首先，將驗證類型變更為 `VoiceProfileType.TextIndependentVerification`。 接下來，將 `while` 迴圈變更為追蹤 `result.RemainingEnrollmentsSpeechLength`，這會繼續提示您說話，直到擷取達 20 秒的音訊為止。

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

再次執行程式，並在驗證階段期間說出任何內容，因為不需要傳遞片語。 同樣地，會傳回相似性分數。

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>說話者識別

說話者辨識是用來判斷從指定的已註冊語音群組說話者的 **身份**。 此流程非常類似 **與文字無關的驗證**，主要差異在於能夠一次針對多個語音設定檔進行驗證，而不是針對單一設定檔進行驗證。

建立函式 `IdentificationEnroll` 以註冊多個語音設定檔。 每個設定檔的註冊流程皆和 **與文字無關的驗證** 註冊流程相同，而且每個設定檔都需要 20 秒的音訊。 此函式會接受 `profileNames`的字串清單，並會為清單中的每個名稱建立新的語音設定檔。 函式會傳回 `VoiceProfile` 物件的清單，您可以在下一個函式中用來識別說話者。

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

建立下列函式 `SpeakerIdentification` 以提交識別要求。 相較於 **說話者** 驗證，此函式的主要差異在於使用 `SpeakerIdentificationModel.FromProfiles()`，這會接受 `VoiceProfile` 物件的清單。 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

將您的 `Main()` 函式變更為下列內容。 您會建立 `profileNames` 的字串清單，並將其傳遞給您的 `IdentificationEnroll()` 函式。 這會提示您為此清單中的每個名稱建立新的語音設定檔，因此您可以新增更多名稱，以便為朋友或同事建立額外的設定檔。

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

執行指令碼，系統會提示您向第一個設定檔註冊語音樣本。 註冊完成後，系統會提示您針對清單中的每個名稱重複此流程 `profileNames`。 完成每個註冊之後，系統會提示您讓 **任何人** 說話，而服務會嘗試從您已註冊的語音設定檔中識別此人員。

這個範例只會傳回最接近的相符項目及其相似性分數，但您可以藉由將 `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` 新增至 `SpeakerIdentification` 函式，取得包含前五個相似性分數的完整回應。

## <a name="changing-audio-input-type"></a>變更音訊輸入類型

本文中的範例使用預設裝置麥克風做為音訊樣本的輸入來源。 不過，在您需要使用音訊檔案而非麥克風輸入的情況下，只需將 `AudioConfig.FromDefaultMicrophoneInput()` 的任何執行個體變更為 `AudioConfig.FromWavFileInput(path/to/your/file.wav)`，即可切換為檔案輸入。 您也可以採用混合輸入，使用麥克風進行註冊以及使用檔案進行驗證，例如。

## <a name="deleting-voice-profile-enrollments"></a>刪除語音設定檔註冊

若要刪除已註冊的設定檔，請在 `VoiceProfileClient` 物件上使用 `DeleteProfileAsync()` 函式。 下列範例函式顯示，如何從已知的語音設定檔識別碼中刪除語音設定檔。

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```
