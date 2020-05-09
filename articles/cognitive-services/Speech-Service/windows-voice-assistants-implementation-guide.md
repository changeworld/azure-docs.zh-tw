---
title: Windows 上的語音助理-上述鎖定實施指導方針
titleSuffix: Azure Cognitive Services
description: 執行語音代理程式的語音啟用和鎖定功能的指示。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 7a142060a29561526c378ce04b23aa2b286cd6c1
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997756"
---
# <a name="implementing-voice-assistants-on-windows"></a>在 Windows 上執行語音助理

本指南將逐步解說在 Windows 上建立語音助理的重要執行詳細資料。

## <a name="implementing-voice-activation"></a>執行語音啟用

在[設定您的環境](how-to-windows-voice-assistants-get-started.md)並瞭解[語音啟用的運作方式](windows-voice-assistants-overview.md#how-does-voice-activation-work)之後，您就可以開始為自己的語音助理應用程式執行語音啟用。

### <a name="registration"></a>註冊

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>確定麥克風可供使用且可供存取，然後監視其狀態

MVA 需要有麥克風可供使用，而且能夠偵測語音啟用。 使用[AppCapability](https://docs.microsoft.com/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability?view=winrt-18362)、 [DeviceWatcher](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher?view=winrt-18362)和[MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture?view=winrt-18362)類別，分別檢查麥克風隱私權存取、裝置目前狀態，以及裝置狀態（例如音量和靜音）。

### <a name="register-the-application-with-the-background-service"></a>向背景服務註冊應用程式

為了讓 MVA 在背景啟動應用程式，應用程式必須向背景服務註冊。 如需完整的背景服務註冊指南，請參閱[這裡](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)。

### <a name="unlock-the-limited-access-feature"></a>解除鎖定有限的存取功能

使用您的 Microsoft 提供的有限存取功能金鑰來解除鎖定 [語音助理] 功能。 請使用 Windows SDK 中的[LimitedAccessFeature](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures?view=winrt-18362)類別來執行此動作。

### <a name="register-the-keyword-for-the-application"></a>註冊應用程式的關鍵字

應用程式必須向 Windows 註冊自己、其關鍵字模型和其語言。

從抓取關鍵字偵測器開始。 在此範例程式碼中，我們會抓取第一個偵測器，但您可以從`configurableDetectors`選取特定的偵測器來選取它。

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

取得 ActivationSignalDetector 物件之後，請使用信號`ActivationSignalDetector.CreateConfigurationAsync`識別碼、模型識別碼和顯示名稱呼叫其方法，以註冊您的關鍵字並抓取您的`ActivationSignalDetectionConfiguration`應用程式。 信號和模型識別碼應由開發人員決定 guid，並針對相同的關鍵字保持一致。

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>確認已啟用 [語音啟動] 設定

若要使用語音啟用，使用者必須啟用其系統的語音啟動，並啟用其應用程式的語音啟動。 您可以在 [Windows 設定] 中的 [語音啟用隱私權設定] 下找到此設定。 若要在您的應用程式中檢查語音啟用設定的狀態，請使用的`ActivationSignalDetectionConfiguration`實例來註冊關鍵字。 上的[AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128)欄位`ActivationSignalDetectionConfiguration`包含列舉值，描述語音啟用設定的狀態。

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>取得 ConversationalAgentSession 以向 MVA 系統註冊應用程式

`ConversationalAgentSession`是 Windows SDK 中的類別，可讓您的應用程式以應用程式狀態（閒置、偵測、接聽、工作中、說話）和接收事件（例如，螢幕鎖定之類的啟用偵測和系統狀態變更）來更新 Windows。 抓取 AgentSession 的實例也可向 Windows 註冊應用程式，以透過語音來啟動。 最佳做法是維護的`ConversationalAgentSession`一個參考。 若要取出會話，請使用`ConversationalAgentSession.GetCurrentSessionAsync` API。

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>接聽兩個啟用信號： OnBackgroundActivated 和 OnSignalDetected

當您的應用程式以兩種方式的其中一種偵測到關鍵字時，Windows 就會發出信號。 如果應用程式不在使用中（也就是說，您沒有的非處置實例的`ConversationalAgentSession`參考），則它會啟動您的應用程式，並在應用程式的 App.xaml.cs 檔中呼叫 OnBackgroundActivated 方法。 如果事件引數的`BackgroundActivatedEventArgs.TaskInstance.Task.Name`欄位符合字串 "AgentBackgroundTrigger"，則應用程式啟動是由語音啟用所觸發。 應用程式需要覆寫此方法，並取出 ConversationalAgentSession 的實例，以通知現在作用中的 Windows。 當應用程式處於作用中狀態時，Windows 會使用`ConversationalAgentSession.OnSignalDetected`事件來通知語音啟用的出現次數。 一旦您取得，請將事件處理常式新增至此事件`ConversationalAgentSession`。

## <a name="keyword-verification"></a>關鍵字驗證

一旦語音啟動語音代理程式應用程式，下一步就是驗證關鍵字偵測是否有效。 Windows 不會提供關鍵字驗證的解決方案，但它可讓語音助理從假設啟用存取音訊，並自行完成驗證。

### <a name="retrieve-activation-audio"></a>取出啟用音訊

建立[AudioGraph](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph) ，並將它傳遞至`CreateAudioDeviceInputNodeAsync`的`ConversationalAgentSession`。 這會在偵測*到關鍵字前大約3秒開始*載入圖形的音訊緩衝區。 其中包含這個額外的音訊，以容納各種關鍵字長度和說話者的速度。 然後，處理音訊圖形中的[QuantumStarted](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph.quantumstarted?view=winrt-18362)事件，以取出音訊資料。

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

注意：音訊緩衝區中包含的前置音訊可能會導致關鍵字驗證失敗。 若要修正此問題，請先修剪音訊緩衝區的開頭，再傳送音訊以進行關鍵字驗證。 此初始修剪應針對每個助理量身打造。

### <a name="launch-in-the-foreground"></a>在前景中啟動

當關鍵字驗證成功時，應用程式必須移至前景才會顯示 UI。 呼叫`ConversationalAgentSession.RequestForegroundActivationAsync` API，將您的應用程式移至前景。

### <a name="transition-from-compact-view-to-full-view"></a>從 compact view 轉換成完整視圖

當您的應用程式第一次由語音啟動時，它就會以精簡的方式啟動。 請閱讀[語音啟用預覽的設計指導](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview)方針，以取得有關 Windows 上語音助理的不同視圖和轉換的指引。

若要從 compact view 轉換到完整的應用程式，請使用 ApplicationView API `TryEnterViewModeAsync`：

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>執行上述鎖定啟用

下列步驟涵蓋在 Windows 上啟用語音助理以執行上述鎖定的需求，包括範例程式碼的參考和管理應用程式生命週期的指導方針。 如需有關設計上述鎖定體驗的指引，請造訪[最佳做法指南](windows-voice-assistants-best-practices.md)。

### <a name="detecting-lock-screen-transitions"></a>偵測鎖定畫面轉換

Windows SDK 中的 ConversationalAgent 程式庫會提供 API，讓鎖定畫面狀態和鎖定畫面狀態的變更容易存取。 若要偵測目前的鎖定畫面狀態，請`ConversationalAgentSession.IsUserAuthenticated`檢查欄位。 若要偵測鎖定狀態的變更，請將事件處理常式`ConversationalAgentSession`新增至`SystemStateChanged`物件的事件。 當螢幕從 [未鎖定] 變更為 [已鎖定] 或 [反之亦然] 時，就會引發。 如果事件引數的值為`ConversationalAgentSystemStateChangeType.UserAuthentication`，則鎖定畫面狀態已變更，應用程式應該會關閉。

```csharp
// When the app changes lock state, close the application to prevent duplicates running at once
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        WindowService.CloseWindow();
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>偵測上述鎖定啟用使用者喜好設定

[語音啟用隱私權設定] 頁面中的 [應用程式] 專案具有上述鎖定功能的切換開關。 若要讓您的應用程式能夠啟動上述鎖定，使用者將必須開啟此設定。 上述鎖定許可權的狀態也會儲存在 ActivationSignalDetectionConfiguration 物件中。 AvailabilityInfo. HasLockScreenPermission 狀態會反映使用者是否已指定上述鎖定許可權。 如果停用此設定，語音應用程式會提示使用者流覽至 [ms-設定：隱私權-voiceactivation] 連結上適當的 [設定] 頁面，並提供如何針對應用程式啟用上方鎖定啟用的指示。

## <a name="closing-the-application"></a>關閉應用程式

若要在高於或低於鎖定時，以程式設計方式適當`WindowService.CloseWindow()`地關閉應用程式，請使用 API。 這會觸發所有 UWP 生命週期方法，包括 OnSuspend，讓應用程式在關閉`ConversationalAgentSession`之前處置其實例。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [流覽 UWP 語音助理範例應用程式以取得範例和程式碼解說](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
