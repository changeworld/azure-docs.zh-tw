---
title: 建立租用戶模型 (預覽) - 語音服務
titleSuffix: Azure Cognitive Services
description: 自動產生安全、合規的租用戶模型 (使用 Microsoft 365 資料的自訂語音)，該模型使用 Microsoft 365 資料針對組織特定詞彙，提供最佳語音辨識。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48cde51ee9941f705aa848d121c419a8f0c9ad1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013709"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>教學課程：建立租用戶模型 (預覽)

租用戶模型 (使用 Microsoft 365 資料的自訂語音) 是 Microsoft 365 企業客戶可選擇加入的服務，它會從您組織的 Microsoft 365 資料自動產生自訂語音辨識模型。 此模型已針對技術詞彙、術語和人員的名字進行最佳化，完全以安全且符合規範的方式進行。

> [!IMPORTANT]
> 如果貴組織使用租用戶模型服務進行註冊，則語音服務可存取貴組織的語言模型。 此模型是從 Microsoft 365 公用群組電子郵件和文件產生而來，貴組織中的任何人都可以看到該模型。 貴組織的 Microsoft 365 系統管理員可以從 Microsoft 365 系統管理入口網站，開啟/關閉全組織語言模型的使用。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 Microsoft 365 系統管理中心在租用戶模型中註冊
> * 取得語音訂用帳戶金鑰
> * 建立租用戶模型
> * 部署租用戶模型
> * 使用租用戶模型搭配語音 SDK

## <a name="enroll-in-the-tenant-model-service"></a>在租用戶模型服務中註冊

您必須先在租用戶模型服務中註冊，才可以部署您的租用戶模型。 註冊是在 Microsoft 365 管理中心完成，而且只能由您的管理員完成。

1. 登入 [Microsoft 365 系統管理中心](https://admin.microsoft.com)。

1. 在左側窗格中選取 [設定]，然後從巢狀功能表中選取 [設定]，最後從主視窗中選取 [Azure 語音服務]。

   ![服務與增益集窗格](media/tenant-language-model/tenant-language-model-enrollment.png)

1. 選取 [允許全組織的語言模型] 核取方塊，然後選取 [儲存變更]。

   ![Azure 語音服務窗格](media/tenant-language-model/tenant-language-model-enrollment-2.png)

若要關閉租用戶模型執行個體：
1. 重複上述步驟 1 和 2。
1. 清除 [允許全組織的語言模型] 核取方塊，然後選取 [儲存變更]。

## <a name="get-a-speech-subscription-key"></a>取得語音訂用帳戶金鑰

若要使用租用戶模型搭配語音 SDK，您會需要語音資源及其相關聯的訂用帳戶金鑰。

1. 登入 [Azure 入口網站](https://aka.ms/azureportal)。
1. 選取 [建立資源]。
1. 在 [搜尋] 方塊中，輸入 **語音**。
1. 在結果清單中，選取 [語音]，然後選取 [建立]。
1. 遵循畫面上的指示來建立資源。 請確認：
   * [位置] 設定為 [eastus] 或 [westus]。
   * [定價層] 設定為 [S0]。
1. 選取 [建立]。

   幾分鐘後，就會建立您的資源。 您可以在資源的 [概觀] 區段中找到訂用帳戶金鑰。

## <a name="create-a-language-model"></a>建立語言模型

當您的管理員已為組織啟用租用戶模型之後，您就可以建立以您的 Microsoft 365 資料為基礎的語言模型。

1. 登入 [Speech Studio](https://speech.microsoft.com/)。
1. 在右上方，選取 [設定] (齒輪圖示)，然後選取 [租用戶模型設定]。

   ![[租用戶模型設定] 連結](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio 會顯示一則訊息，讓您知道您是否有資格建立租用戶模型。

   > [!NOTE]
   > 在北美洲的企業客戶有資格建立租用戶模型 (英文)。 如果您是客戶加密箱、客戶金鑰或 Office 365 政府版客戶，則無法使用此功能。 若要判斷您是「客戶加密箱」或「客戶金鑰」客戶，請參閱：
   > * [客戶加密箱](/microsoft-365/compliance/customer-lockbox-requests) \(部分機器翻譯\)
   > * [客戶金鑰](/microsoft-365/compliance/customer-key-overview) \(部分機器翻譯\)
   > * [Office 365 政府版](https://www.microsoft.com/microsoft-365/government)

1. 選取 [加入]。

   當您的租用戶模型準備就緒時，您會收到確認電子郵件訊息，其中包含進一步的指示。

## <a name="deploy-your-tenant-model"></a>部署租用戶模型

當您的租用戶模型執行個體準備就緒時，請執行下列動作加以部署：

1. 在您的確認電子郵件訊息中，選取 [檢視模型] 按鈕。 或登入 [Speech Studio](https://speech.microsoft.com/)。
1. 在右上方，選取 [設定] (齒輪圖示)，然後選取 [租用戶模型設定]。

   ![[租用戶模型設定] 連結](media/tenant-language-model/tenant-language-settings.png)

1. 選取 [部署]。

   當您的模型已部署時，狀態會變更為 [已部署]。

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>使用租用戶模型搭配語音 SDK

現在您已部署模型，您可以搭配語音 SDK 使用它。 在本節中，您會透過 Azure Active Directory (Azure AD) 驗證，使用範例程式碼來呼叫語音服務。

讓我們看一下您會用 C# 呼叫語音 SDK 的程式碼。 在此範例中，您會使用租用戶模型執行語音辨識。 本指南假設您的平台已設定好。 如果您需要安裝程式說明，請參閱[快速入門：辨識語音，C# (.NET Core)](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)。

請將下列程式碼複製到您的專案中：

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

接下來，您必須從命令列重建並執行專案。 在執行命令之前，請執行下列動作來更新幾個參數：

1. 將 `<Username>` 和 `<Password>` 取代為有效租用戶使用者的值。
1. 將 `<Subscription-Key>` 取代為您語音資源的訂用帳戶金鑰。 您可以在 [Azure 入口網站](https://aka.ms/azureportal)中，於您語音資源的 [概觀] 區段中找到此值。
1. 以下列端點取代 `<Endpoint-Uri>`。 請務必將 `{your region}` 取代為您語音資源所建立在的區域。 支援這些區域：`westus`、`westus2` 和 `eastus`。 您可以在 [Azure 入口網站](https://aka.ms/azureportal)中，於您語音資源的 [概觀] 區段中找到您的區域資訊。
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. 執行以下命令：

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

在本教學課程中，您已了解如何使用 Microsoft 365 資料來建立自訂語音辨識模型、加以部署，並將其搭配語音 SDK 使用。

## <a name="next-steps"></a>後續步驟

* [Speech Studio](https://speech.microsoft.com/) \(英文\)
* [語音 SDK](speech-sdk.md)