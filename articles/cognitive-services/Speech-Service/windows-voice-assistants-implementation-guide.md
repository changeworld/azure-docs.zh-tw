---
title: Windows 上的語音助理-上方鎖定實施指導方針
titleSuffix: Azure Cognitive Services
description: 適用于語音代理程式應用程式的語音啟用和鎖定功能的指示。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: a529875536c2feafe05695e5d20daed0873a95e6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934441"
---
# <a name="implementing-voice-assistants-on-windows"></a>在 Windows 上執行語音助理

本指南將逐步解說在 Windows 上建立語音助理的重要實行詳細資料。

## <a name="implementing-voice-activation"></a>執行語音啟用

[設定您的環境](how-to-windows-voice-assistants-get-started.md)並瞭解[語音啟用的運作方式](windows-voice-assistants-overview.md#how-does-voice-activation-work)之後，您就可以開始為您自己的語音助理應用程式執行語音啟用。

### <a name="registration"></a>註冊

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>確定麥克風可用且可供存取，然後監視其狀態

MVA 必須要有麥克風，才能偵測語音啟用。 使用 [AppCapability](https://docs.microsoft.com/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability?view=winrt-18362)、 [DeviceWatcher](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher?view=winrt-18362)和 [MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture?view=winrt-18362) 類別，分別檢查麥克風的隱私權存取、裝置狀態和裝置狀態 (例如音量和靜音) 。

### <a name="register-the-application-with-the-background-service"></a>使用背景服務註冊應用程式

為了讓 MVA 在背景中啟動應用程式，應用程式必須向背景服務註冊。 請參閱 [這裡](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)的背景服務註冊完整指南。

### <a name="unlock-the-limited-access-feature"></a>將受限存取功能解除鎖定

使用 Microsoft 提供的有限存取功能金鑰，將語音助理功能解除鎖定。 使用 Windows SDK 中的 [LimitedAccessFeature](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures?view=winrt-18362) 類別來執行此動作。

### <a name="register-the-keyword-for-the-application"></a>註冊應用程式的關鍵字

應用程式必須註冊本身、其關鍵字模型，以及其在 Windows 中的語言。

首先，請先取出關鍵字偵測器。 在此範例程式碼中，我們會抓取第一個偵測器，但您可以選取特定的偵測器 `configurableDetectors` 。

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

在抓取 ActivationSignalDetector 物件之後，請 `ActivationSignalDetector.CreateConfigurationAsync` 使用信號識別碼、模型識別碼和顯示名稱呼叫其方法，以註冊您的關鍵字並取出您的應用程式 `ActivationSignalDetectionConfiguration` 。 信號和模型識別碼應為開發人員決定的 guid，並且針對相同的關鍵字保持一致。

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>確認已啟用語音啟用設定

若要使用語音啟用，使用者必須啟用其系統的語音啟用，並啟用其應用程式的語音啟用。 您可以在 [Windows 設定] 中的 [語音啟用隱私權設定] 下找到此設定。 若要在您的應用程式中檢查語音啟用設定的狀態，請使用的實例 `ActivationSignalDetectionConfiguration` 註冊關鍵字。 上的 [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) 欄位 `ActivationSignalDetectionConfiguration` 包含列舉值，這個值描述語音啟用設定的狀態。

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>取出 ConversationalAgentSession 以向 MVA 系統註冊應用程式

`ConversationalAgentSession`是 Windows SDK 中的類別，可讓您的應用程式更新具有應用程式狀態的 Windows (閒置、偵測、接聽、工作、說話) 以及接收事件，例如啟用偵測和系統狀態變更，例如螢幕鎖定。 AgentSession 的實例也可以用來向 Windows 註冊應用程式，以透過語音來啟動。 最佳做法是維護一個對的參考 `ConversationalAgentSession` 。 若要取出會話，請使用 `ConversationalAgentSession.GetCurrentSessionAsync` API。

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>接聽這兩個啟用信號： OnBackgroundActivated 和 OnSignalDetected

當您的應用程式偵測到關鍵字時，Windows 會以兩種方式的其中一種來發出信號。 如果應用程式不在使用中 (也就是說，您沒有) 的未處置實例的參考 `ConversationalAgentSession` ，則會啟動您的應用程式，並在應用程式的 App.xaml.cs 檔中呼叫 OnBackgroundActivated 方法。 如果事件引數的 `BackgroundActivatedEventArgs.TaskInstance.Task.Name` 欄位符合字串 "AgentBackgroundTrigger"，則應用程式啟動是由語音啟用所觸發。 應用程式必須覆寫這個方法，並取出 ConversationalAgentSession 的實例，以對現在作用中的 Windows 發出信號。 當應用程式在作用中時，Windows 會使用事件來通知出現語音啟用 `ConversationalAgentSession.OnSignalDetected` 。 在您抓取之後，將事件處理常式加入至此事件 `ConversationalAgentSession` 。

## <a name="keyword-verification"></a>關鍵字驗證

語音代理程式應用程式由語音啟動後，下一個步驟就是驗證關鍵字偵測是否有效。 Windows 不會提供關鍵字驗證的解決方案，但它可讓語音助理從假設啟用存取音訊，並自行完成驗證。

### <a name="retrieve-activation-audio"></a>取出啟用音訊

建立 [AudioGraph](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph) ，並將它傳遞至的 `CreateAudioDeviceInputNodeAsync` `ConversationalAgentSession` 。 這會載入圖形的音訊緩衝區，其中包含在偵測 *到關鍵字之前大約3秒*的音訊。 包含此額外的前置音訊，以容納各式各樣的關鍵字長度和說話者速度。 然後，處理來自音訊圖表的 [QuantumStarted](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph.quantumstarted?view=winrt-18362) 事件，以取出音訊資料。

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

注意：音訊緩衝區中包含的前置音訊可能會導致關鍵字驗證失敗。 若要修正此問題，請先修剪音訊緩衝區的開頭，再傳送音訊進行關鍵字驗證。 此初始修剪應針對每個助理量身打造。

### <a name="launch-in-the-foreground"></a>在前景中啟動

當關鍵字驗證成功時，應用程式必須移至前景才能顯示 UI。 呼叫 `ConversationalAgentSession.RequestForegroundActivationAsync` API，將您的應用程式移至前景。

### <a name="transition-from-compact-view-to-full-view"></a>從 compact view 轉換成完整視圖

當您的應用程式第一次由語音啟動時，就會以精簡視圖的方式啟動。 如需有關不同視圖的指引，以及在 Windows 上的語音助理之間的轉換，請閱讀 [語音啟用預覽的設計指引](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) 。

若要將 compact view 轉換成完整的應用程式視圖，請使用 ApplicationView API `TryEnterViewModeAsync` ：

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>執行上述鎖定啟用

下列步驟涵蓋在 Windows 上啟用語音助理以執行上述鎖定的需求，包括對範例程式碼的參考，以及管理應用程式生命週期的指導方針。

如需有關設計上述鎖定體驗的指引，請造訪 [最佳作法指南](windows-voice-assistants-best-practices.md)。

當應用程式顯示鎖定上方的視圖時，會將其視為「Kiosk 模式」。 如需有關如何執行使用 Kiosk 模式的應用程式的詳細資訊，請參閱 [Kiosk 模式檔](https://docs.microsoft.com/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)。

### <a name="transitioning-above-lock"></a>轉換上方鎖定

上述鎖定的啟用類似于以下的啟用鎖定。 如果沒有作用中的應用程式實例，則會在背景中啟動新的實例，並 `OnBackgroundActivated` 在 App.xaml.cs 中呼叫。 如果有應用程式的實例，該實例會透過事件取得通知 `ConversationalAgentSession.SignalDetected` 。

如果應用程式尚未顯示以上鎖定，則必須呼叫 `ConversationalAgentSession.RequestForegroundActivationAsync` 。 這會觸發 `OnLaunched` App.xaml.cs 中的方法，此方法應該會流覽至將顯示在鎖定上方的視圖。

### <a name="detecting-lock-screen-transitions"></a>偵測鎖定畫面轉換

Windows SDK 中的 ConversationalAgent 程式庫提供 API，可讓您輕鬆存取鎖定畫面狀態和鎖定畫面狀態的變更。 若要偵測目前的鎖定畫面狀態，請檢查 `ConversationalAgentSession.IsUserAuthenticated` 欄位。 若要偵測鎖定狀態的變更，請將事件處理常式加入至 `ConversationalAgentSession` 物件的 `SystemStateChanged` 事件。 只要螢幕從解除鎖定變成鎖定，就會引發它，反之亦然。 如果事件引數的值為 `ConversationalAgentSystemStateChangeType.UserAuthentication` ，則鎖定畫面狀態已變更。

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>偵測上述鎖定啟用使用者喜好設定

[語音啟動隱私權設定] 頁面中的應用程式專案具有上述鎖定功能的切換。 若要讓您的應用程式能夠啟動上述鎖定，使用者將需要開啟此設定。 以上鎖定許可權的狀態也會儲存在 ActivationSignalDetectionConfiguration 物件中。 AvailabilityInfo. HasLockScreenPermission 狀態會反映使用者是否已提供上述鎖定許可權。 如果停用此設定，語音應用程式可能會提示使用者流覽至適當的設定頁面（位於連結 "ms-settings：隱私權-voiceactivation"），並提供如何針對應用程式啟用上述鎖定啟用的指示。

## <a name="closing-the-application"></a>關閉應用程式

若要在鎖定的上方或下方以程式設計方式適當地關閉應用程式，請使用 `WindowService.CloseWindow()` API。 這會觸發所有 UWP 生命週期方法（包括 OnSuspend），讓應用程式在 `ConversationalAgentSession` 關閉之前處置其實例。

> [!NOTE]
> 應用程式可以在不關閉 [下列鎖定實例](https://docs.microsoft.com/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-)的情況下關閉。 在此情況下，上述鎖定視圖需要「清除」，以確保一旦畫面解除鎖定，就不會有任何事件處理常式或工作嘗試操作上述鎖定視圖。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [造訪 UWP 語音助理範例應用程式，以取得範例和程式碼的逐步解說-解說](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
