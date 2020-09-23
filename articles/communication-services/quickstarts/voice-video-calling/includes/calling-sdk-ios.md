---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: fa7fd73a7d8019919a89dd9e9522b7389dc9c18f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934666"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通訊服務資源。 [建立通訊服務資源](../../create-communication-resource.md)。
- `User Access Token`要啟用呼叫用戶端的。 如需[如何取得的 `User Access Token` ](../../access-tokens.md)詳細資訊
- 選擇性：完成快速入門以 [開始將呼叫新增至您的應用程式](../getting-started-with-calling.md)

## <a name="setting-up"></a>設定

### <a name="creating-the-xcode-project"></a>建立 Xcode 專案

在 Xcode 中，建立新的 iOS 專案，然後選取 [ **單一視圖] 應用程式** 範本。 本快速入門使用 [SwiftUI 架構](https://developer.apple.com/xcode/swiftui/)，因此您應該將 **語言** 設定為 **Swift** ，並將 **消費者介面** 設定為 **SwiftUI**。 在本快速入門中，您不會建立單元測試或 UI 測試。 您可以隨意取消選取 [ **包含單元測試** ]，也可以取消選取 [ **包含 UI 測試**]。

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="顯示 Xcode 中 [建立新專案] 視窗的螢幕擷取畫面。":::

### <a name="install-the-package"></a>安裝套件

在您的專案中，將呼叫用戶端程式庫和其相依性 (AzureCore 和 AzureCommunication) 新增至您的專案。

> [!NOTE]
> 隨著 AzureCommunicationCalling SDK 的發行，您將會發現 bash 腳本 `BuildAzurePackages.sh` 。 執行腳本時， `sh ./BuildAzurePackages.sh` 會提供您在下一個步驟中需要匯入至範例應用程式之所產生架構套件的路徑。 請注意，您必須在執行腳本之前，先設定 Xcode 命令列工具： [開始 Xcode]，然後選取 [喜好設定-> 位置]。 為命令列工具挑選您的 Xcode 版本。

1. 下載適用于 iOS 的 Azure 通訊服務呼叫用戶端程式庫。
2. 在 Xcode 中，按一下您的專案檔，然後選取組建目標以開啟 [專案設定編輯器]。
3. 在 [ **一般** ] 索引標籤的 [架構]、[連結 **庫] 和 [內嵌內容** ] 區段中，按一下 [ **+]** 圖示。
4. 在對話方塊的左下方，選擇 [ **新增**檔案]，流覽至非壓縮的用戶端程式庫套件的 **AzureCommunicationCalling 架構** 目錄。
    1. 重複最後一個步驟，以新增 **AzureCore. framework** 和 **AzureCommunication**。
5. 開啟 [專案設定編輯器] 的 [ **組建設定** ] 索引標籤，並流覽至 [ **搜尋路徑** ] 區段。 針對包含**AzureCommunicationCalling**的目錄，加入新的**架構搜尋路徑**專案。
    1. 新增另一個架構搜尋路徑專案，指向包含相依性的資料夾。

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="螢幕擷取畫面，顯示在 XCode 中更新架構搜尋路徑。":::

### <a name="request-access-to-the-microphone"></a>要求存取麥克風

若要存取裝置的麥克風，您需要使用來更新應用程式的 [資訊] 屬性清單 `NSMicrophoneUsageDescription` 。 您將相關聯的值設定為 `string` ，將會包含在系統用來要求使用者要求存取的對話方塊中。

以滑鼠右鍵按一下 `Info.plist` 專案樹狀結構的專案，然後選取 [**開啟為**  >  **原始程式碼**]。 在最上層區段中新增下列幾行，然後儲存檔案 `<dict>` 。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

開啟專案的 **ContentView** 檔案，然後將宣告新增 `import` 至檔案頂端，以匯入 `AzureCommunicationCalling library` 。 此外，匯入之後 `AVFoundation` ，我們必須在程式碼中要求您輸入音訊許可權。

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>物件模型

下列類別和介面會處理針對 iOS 呼叫用戶端程式庫的 Azure 通訊服務的一些主要功能。


| Name                                  | 描述                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | ACSCallClient 是呼叫用戶端程式庫的主要進入點。|
| ACSCallAgent | ACSCallAgent 可用來啟動及管理呼叫。 |
| CommunicationUserCredential | CommunicationUserCredential 會用來做為權杖認證，以具現化 CallAgent。| 
| CommunicationIndentifier | CommunicationIndentifier 用來代表使用者的身分識別，這可以是下列其中一項： CommunicationUser/PhoneNumber/CallingApplication。 |

> [!NOTE]
> 在執行事件委派時，應用程式必須保存需要事件訂閱之物件的強式參考。 例如，當叫用 `ACSRemoteParticipant` 方法時傳回物件， `call.addParticipant` 而應用程式會設定委派來接聽時 `ACSRemoteParticipantDelegate` ，應用程式必須保存物件的強式參考 `ACSRemoteParticipant` 。 否則，如果收集此物件，當呼叫 SDK 嘗試叫用物件時，委派將會擲回嚴重的例外狀況。

## <a name="initialize-the-acscallagent"></a>將 ACSCallAgent 初始化

若要 `ACSCallAgent` 從建立實例， `ACSCallClient` 您必須使用 `callClient.createCallAgent` 方法，在 `ACSCallAgent` 物件初始化後以非同步方式傳回物件

若要建立呼叫用戶端，您必須傳遞 `CommunicationUserCredential` 物件。

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

將上面建立的 CommunicationUserCredential 物件傳遞至 ACSCallClient

```swift

callClient = ACSCallClient()
callClient?.createCallAgent(userCredential!,
    withCompletionHandler: { (callAgent, error) in
        if error != nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>撥出來電

若要建立並啟動呼叫，您必須在上呼叫其中一個 Api `ACSCallAgent` ，並提供您使用通訊服務管理用戶端程式庫所布建之使用者的通訊服務身分識別。

呼叫建立和啟動是同步的。 您將會收到可讓您訂閱呼叫上所有事件的呼叫實例。

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>使用使用者和 PSTN 來撥打電話給使用者或1： n 呼叫1:1

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.CallingApp.callAgent.call(participants: callees, options: ACSStartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>使用使用者和 PSTN 撥出1： n 呼叫
若要撥打 PSTN 的電話，您必須指定使用通訊服務取得的電話號碼
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.CallingApp.callAgent.call(participants: [pstnCallee, callee], options: ACSStartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>使用影片撥打1:1 電話
若要取得裝置管理員實例，請參閱 [這裡](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(camera)
let videoOptions = ACSVideoOptions(localVideoStream)

let startCallOptions = ACSStartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call([callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>加入群組呼叫
若要加入呼叫，您需要在*CallAgent*上呼叫其中一個 api

```swift

let groupCallContext = ACSGroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: ACSJoinCallOptions())

```

## <a name="push-notification"></a>推播通知

行動推播通知是您在行動裝置中取得的快顯通知。 針對呼叫，我們將著重于 VoIP (語音 over 網際網路通訊協定) 推播通知。 我們將提供您註冊推播通知、處理推播通知，以及取消註冊推播通知的功能。

### <a name="prerequisite"></a>必要條件

- 步驟1： Xcode-> 簽署 & 功能-> 新增功能-> 「推播通知」
- 步驟2： Xcode > 簽署 & 功能-> 新增功能-> 「背景模式」
- 步驟3：「背景模式」-> 選取「語音 over IP」和「遠端通知」

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="顯示如何在 Xcode 中新增功能的螢幕擷取畫面。" lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>註冊推播通知

為了註冊推播通知，請在具有裝置註冊權杖的 *CallAgent* 實例上呼叫 registerPushNotification ( # A1。

必須在成功初始化之後呼叫推播通知註冊。 當物件終結時 `callAgent` ， `logout` 會呼叫，以自動取消註冊推播通知。


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken,
                withCompletionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>推播通知處理
若要接收傳入的來電推播通知，請在具有字典承載的*CallAgent*實例上呼叫*HandlePushNotification ( # B1* 。

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(dictionaryPayload, withCompletionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>取消註冊推播通知

應用程式可以隨時取消註冊推播通知。 只要 `unRegisterPushNotification` 在 *CallAgent*上呼叫方法即可。
> [!NOTE]
> 登出時，不會自動從推播通知取消註冊應用程式。

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>中間通話作業

您可以在呼叫中執行各種作業，以管理與影片和音訊相關的設定。

### <a name="mute-and-unmute"></a>靜音和取消靜音

若要將本機端點靜音或取消靜音，您可以使用 `mute` 和 `unmute` 非同步 api：

```swift
call.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

匿名本機取消靜音

```swift
call.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>啟動和停止傳送本機影片

若要開始將本機影片傳送給通話中的其他參與者，請使用 `startVideo` API 並傳遞 `localVideoStream``camera`

```swift

let firstCamera: ACSVideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(firstCamera)

call.startVideo(localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

當您開始傳送影片之後， `ACSLocalVideoStream` 實例就會新增至 `localVideoStreams` 呼叫實例上的集合：

```swift

call.localVideoStreams[0]

```

匿名若要停止本機影片，請傳遞 `localVideoStream` 從的調用傳回的 `call.startVideo` ：

```swift

call.stopVideo(localVideoStream,{ (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    }
    else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>遠端參與者管理

所有遠端參與者都以類型表示 `ACSRemoteParticipant` ，並可透過 `remoteParticipants` 呼叫實例上的集合取得：

### <a name="list-participants-in-a-call"></a>列出通話中的參與者

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>遠端參與者屬性

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>將參與者新增至來電

若要將參與者加入至呼叫 (使用者或電話號碼) 您可以叫用 `addParticipant` 。 這會以同步方式傳回遠端參與者實例。

```swift

let remoteParticipantAdded: ACSRemoteParticipant = call.addParticipant(CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>從來電中移除參與者
若要從呼叫中移除參與者 (使用者或電話號碼) 您可以叫用  `removeParticipant` API。 這將會以非同步方式解決。

```swift

call!.remove(remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>呈現遠端參與者影片串流

遠端參與者可能會在通話期間起始影片或螢幕共用。

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>處理遠端參與者的影片/螢幕共用串流

若要列出遠端參與者的資料流程，請檢查 `videoStreams` 集合：

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>遠端影片資料流程屬性

```swift

var type: ACSMediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>呈現遠端參與者串流

若要開始呈現遠端參與者資料流程：

```swift

let renderer: ACSRenderer? = ACSRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: ACSRendererView? = renderer?.createView(ACSRenderingOptions(ACSScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(ACSScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>遠端影片轉譯器方法和屬性

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
```

## <a name="device-management"></a>裝置管理

`DeviceManager` 可讓您列舉可在呼叫中用來傳輸音訊/影片串流的本機裝置。 它也可讓您向使用者要求存取麥克風/攝影機的許可權。 您可以 `deviceManager` 在物件上存取 `callClient` ：

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>列舉本機裝置

若要存取本機裝置，您可以在裝置管理員上使用列舉方法。 列舉是同步動作。

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>設定預設麥克風/喇叭

裝置管理員可讓您設定開始通話時將使用的預設裝置。 如果未設定堆疊預設值，通訊服務會切換回作業系統預設值。

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(ACSAudioDeviceInfo())
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeakers(ACSAudioDeviceInfo())
```

### <a name="local-camera-preview"></a>本機相機預覽

您可以使用 `ACSRenderer` 來開始從您的本機相機轉譯資料流程。 此資料流程不會傳送給其他參與者;它是本機預覽摘要。 這是非同步動作。

```swift

let camera: ACSVideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: ACSLocalVideoStream = ACSLocalVideoStream(camera)
let renderer: ACSRenderer = ACSRenderer(localVideoStream: localVideoStream)
self.view = renderer!.createView(ACSRenderingOptions())

```

### <a name="local-camera-preview-properties"></a>本機相機預覽屬性

轉譯器有一組屬性和方法，可讓您控制呈現：

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = ACSRenderer(localVideoStream:localVideoStream)
let remoteRenderer = ACSRenderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view with rendering options
localRenderer.createView(options:ACSRenderingOptions())
// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>事件模型

您可以訂閱大部分的屬性和集合，以便在值變更時收到通知。

### <a name="properties"></a>屬性
若要訂閱 `property changed` 事件：

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: ACSCall!,
                               _ args: ACSPropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>集合
若要訂閱 `collection updated` 事件：

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: ACSCall!,
                                       _ args: ACSLocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
