---
services: cognitive-services
author: ralphe
manager: cpoulain
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: ralphe
ms.openlocfilehash: 74aa4d62490778ba13a085d62264b7791112504e
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377322"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../overview.md#try-the-speech-service-for-free)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

## <a name="add-sample-code"></a>新增範例程式碼

1. 從 Visual Studio 開啟來源檔案 **helloworld.cpp**。

1. 使用下列程式碼片段取代所有程式碼：

    ```cpp
    #include "pch.h"
    
    #define WIN32_LEAN_AND_MEAN
    #include <Windows.h>
    
    #include <iostream>
    #include <thread>
    #include <future>
    #include <string>
    #include <speechapi_cxx.h>
    
    using namespace std::chrono_literals;
    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Audio;
    using namespace Microsoft::CognitiveServices::Speech::Transcription;
    
    // Create a promise we will set when the user presses Ctrl-C
    std::promise<bool> promise;
    // Create the future we will use to wait for the promise to be fulfilled
    std::future<bool> future = promise.get_future();
    
    void StartNewConversation()
    {
        // Set these
        std::string subscriptionKey("YourSubscriptionKey");

        // Replace below with your own region identifier from here: https://aka.ms/speech/sdkregion
        std::string region("YourServiceRegion");
        
        std::string speechLanguage("en-US");
    
        // Create the conversation object you'll need to manage the conversation
        auto speechConfig = SpeechConfig::FromSubscription(subscriptionKey, region);
        speechConfig->SetSpeechRecognitionLanguage(speechLanguage);
        auto conversation = Conversation::CreateConversationAsync(speechConfig).get();
    
        // Start the conversation so you and others can join
        conversation->StartConversationAsync().get();
    
        // Get the conversation ID. It will be up to your scenario to determine how this is shared with other participants.
        std::cout << "Created conversation: " << conversation->GetConversationId() << std::endl;
    
        // You can now call various commands to manage the room. For example:
        conversation->MuteAllParticipantsAsync().get();
    
        // Create the conversation translator you'll need to send audio, send IMs, and receive conversation events
        auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
        auto conversationTranslator = ConversationTranslator::FromConfig(audioConfig);
    
        // Add any event handlers
        conversationTranslator->SessionStarted += [](const SessionEventArgs& args)
        {
            std::cout << "Session started " << args.SessionId << std::endl;
        };
        conversationTranslator->SessionStopped += [](const SessionEventArgs& args)
        {
            std::cout << "Session stopped " << args.SessionId << std::endl;
        };
        conversationTranslator->Canceled += [](const ConversationTranslationCanceledEventArgs& args)
        {
            switch (args.Reason)
            {
                case CancellationReason::EndOfStream:
                    std::cout << "End of audio reached" << std::endl;
                    break;
    
                case CancellationReason::Error:
                    std::cout << "Canceled due to error. " << (long)args.ErrorCode << ": " << args.ErrorDetails << std::endl;
                    break;
            }
        };
        conversationTranslator->ConversationExpiration += [](const ConversationExpirationEventArgs& args)
        {
            std::cout << "Conversation will expire in " << args.ExpirationTime.count() << " minutes" << std::endl;
        };
        conversationTranslator->ParticipantsChanged += [](const ConversationParticipantsChangedEventArgs& args)
        {
            std::cout << "The following participant(s) have ";
            switch (args.Reason)
            {
                case ParticipantChangedReason::JoinedConversation:
                    std::cout << "joined";
                    break;
    
                case ParticipantChangedReason::LeftConversation:
                    std::cout << "left";
                    break;
    
                case ParticipantChangedReason::Updated:
                    std::cout << "been updated";
                    break;
            }
    
            std::cout << ":" << std::endl;
    
            for(std::shared_ptr<Participant> participant : args.Participants)
            {
                std::cout << "\t" << participant->DisplayName << std::endl;
            }
        };
        conversationTranslator->Transcribing += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a partial transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->Transcribed += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->TextMessageReceived += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received an instant message from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
    
        // Join the conversation so you can start receiving events
        conversationTranslator->JoinConversationAsync(conversation, "Test Host").get();
    
        // Send an instant message
        conversationTranslator->SendTextMessageAsync("This is a short test message").get();
    
        // Start sending audio
        conversationTranslator->StartTranscribingAsync().get();
        std::cout << "Started transcribing. Press Ctrl + C to stop" << std::endl;
        future.get(); // wait for Ctrl - C to be pressed
    
        // Stop audio capture
        conversationTranslator->StopTranscribingAsync().get();
    
        // Leave the conversation. You will stop receiving events
        conversationTranslator->LeaveConversationAsync().get();
    
        // Once you are done, remember to delete the conversation.
        conversation->EndConversationAsync().get(); // You will not be able to rejoin after this
        conversation->DeleteConversationAsync().get(); // All participants still in the room will be ejected
    }
    
    int main()
    {
        // Register a handler for the Ctrl - C callback
        SetConsoleCtrlHandler(
            [](DWORD dwCtrlType) -> BOOL
            {
                if (dwCtrlType == CTRL_C_EVENT)
                {
                    // Signal that the user has pressed ctrl + C
                    promise.set_value(true);
                    return TRUE;
                }
    
                return FALSE;
            },
            TRUE);
    
        StartNewConversation();
    }
    ```

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 將字串 `YourServiceRegion` 取代為與您的訂用帳戶相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)。

1. 從功能表列中，選擇 [檔案] > [全部儲存]。

## <a name="build-and-run-the-application-to-create-a-new-conversation"></a>建置並執行應用程式以建立新的交談

1. 從功能表列中，選取 [建置] > [建置方案] 來建置應用程式。 程式碼現在應該可以編譯，而且不會出現任何錯誤。

1. 選擇 [偵錯] > [開始偵錯] (或按 <kbd>F5</kbd>) 以啟動 **helloworld** 應用程式。

1. 一旦看到 `Started transcribing` 訊息出現，您就可以開始說話。 當您說話時，您會看到文字記錄出現
    - 如果您與其他人共用交談程式碼，而且這些人加入交談，您也會看到他們的文字記錄。

1. 說完之後，請按下鍵盤上的 <kbd>Ctrl+C</kbd> 來停止音訊擷取。

    > [!NOTE]
    > 您可能會看到來自 Visual Studio 關於例外狀況的訊息，如下所示：`Exception thrown at 0x76EB90BF (KernelBase.dll) in helloworld.exe: 0x40010005: Control-C.` 您可以安全地忽略這些訊息。
    > <br/> <br/>
    > 按 <kbd>F5</kbd> 繼續。

## <a name="build-and-run-the-application-to-join-an-existing-conversation"></a>建置並執行應用程式以加入現有交談

1. 複製下列函式並貼到 **helloworld.cpp** 中的 `int main()` 函式正前方：

    ```cpp
    void JoinExistingConversation(const std::string& conversationId)
    {
        std::string speechLanguage("en-US");
    
        // You'll now need to create a ConversationTranslator to send audio, send IMs, and receive conversation events
        auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
        auto conversationTranslator = ConversationTranslator::FromConfig(audioConfig);
    
        // Attach event handlers here. For example:
        conversationTranslator->Transcribing += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a partial transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->Transcribed += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->TextMessageReceived += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received an instant message from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
    
        // Join the conversation
        conversationTranslator->JoinConversationAsync(conversationId, "participant", speechLanguage).get();
    
        // Start sending audio
        conversationTranslator->StartTranscribingAsync().get();
        std::cout << "Started transcribing. Press Ctrl + C to stop" << std::endl;
        future.get(); // wait for Ctrl - C to be pressed
    
        // Stop audio capture
        conversationTranslator->StopTranscribingAsync().get();
    
        // Once you are done, leave the conversation
        conversationTranslator->LeaveConversationAsync().get();
    }
    ```

2. 使用下列內容取代 `int main()` 函式中的 `StartNewConversation();`：

    ```cpp
    // Set this to the conversation you want to join
    JoinExistingConversation("YourConversationId");
    ```

[!INCLUDE [create-from-web](../create-from-web.md)]

4. 返回 Visual Studio，並將 `int main()` 函式中的 `YourConversationId` 取代為上一個步驟中的交談識別碼。

5. 從功能表列中，選取 [建置]   > [建置方案]  來建置應用程式。 這應該會編譯程式碼，而且不會出現任何錯誤。

6. 選擇 [偵錯]   > [開始偵錯]  (或按 <kbd>F5</kbd>) 以啟動 **helloworld** 應用程式。

7. 一旦看到 `Started transcribing` 訊息出現，您就可以開始說話。 當您說話時，您會看到文字記錄出現。
    - 如果您返回瀏覽器，則應該也會在說話時看到文字記錄出現在該處。

8.  說完之後，請按 <kbd>Ctrl+C</kbd> 來停止音訊擷取，並結束交談。

    > [!NOTE]
    > 您可能會看到來自 Visual Studio 關於例外狀況的訊息，如下所示：`Exception thrown at 0x76EB90BF (KernelBase.dll) in helloworld.exe: 0x40010005: Control-C.` 您可以安全地忽略這些訊息。
    > <br/> <br/>
    > 按 <kbd>F5</kbd> 繼續。

9. 返回瀏覽器，並使用右上角的 [結束] 按鈕來結束交談。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
