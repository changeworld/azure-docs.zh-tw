---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: c015561e66d77e6df352e601bf1a67da5996d4d5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915197"
---
## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署通訊服務資源。 [建立通訊服務資源](../../create-communication-resource.md)。
- 用來啟用通話用戶端的 `User Access Token`。 如需[如何取得 `User Access Token`](../../access-tokens.md) 的詳細資訊
- 選擇性：完成快速入門以 [開始將呼叫新增至您的應用程式](../getting-started-with-calling.md)

## <a name="setting-up"></a>設定

### <a name="install-the-package"></a>安裝套件

<!-- TODO: update with instructions on how to download, install and add package to project -->
找出您的專案層級 build.gradle，並務必將 `mavenCentral()` 新增至 `buildscript` 和 `allprojects` 下的存放庫清單
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
然後，在您的模組層級組建中，gradle 將下列幾行新增至 [相依性] 區段

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.2'
    ...
}

```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務通話用戶端程式庫的一些主要功能：

| Name                                  | 描述                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient 是通話用戶端程式庫的主要進入點。|
| CallAgent | CallAgent 是用來啟動和管理通話。 |
| CommunicationUserCredential | CommunicationUserCredential 會當做權杖認證使用，以具現化 CallAgent。|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>初始化 CallClient、建立 CallAgent，並存取 DeviceManager

若要建立 `CallAgent` 實例，您必須 `createCallAgent` 在實例上呼叫方法 `CallClient` 。 這會以非同步方式傳回 `CallAgent` 實例物件。
`createCallAgent`方法會使用 `CommunicationUserCredential` 做為引數，它會封裝[存取權杖](../../access-tokens.md)。
若要存取 `DeviceManager` ，必須先建立 callAgent 實例，然後您可以使用 `CallClient.getDeviceManager` 方法來取得 DeviceManager。

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationUserCredential tokenCredential = new CommunicationUserCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = await callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = await callClient.getDeviceManager().get();
```

## <a name="place-an-outgoing-call-and-join-a-group-call"></a>撥出來電並加入群組呼叫

若要建立並啟動呼叫，您必須呼叫 `CallAgent.call()` 方法，並將 `Identifier` 被呼叫端的 (s) 。
若要加入群組呼叫，您需要呼叫 `CallAgent.join()` 方法並提供 groupId。 群組識別碼必須是 GUID 或 UUID 格式。

呼叫建立和啟動是同步的。 呼叫實例可讓您訂閱呼叫上的所有事件。

### <a name="place-a-11-call-to-a-user"></a>將1:1 呼叫放到使用者
若要呼叫另一個通訊服務使用者，請 `call` 在上叫用方法， `callAgent` 並傳遞具有索引鍵的物件 `communicationUserId` 。
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUser acsUserId = new CommunicationUser(<USER_ID>);
CommunicationUser participants[] = new CommunicationUser[]{ acsUserId };
call oneToOneCall = callAgent.call(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>使用使用者和 PSTN 撥出1： n 呼叫
> [!WARNING]
> 目前無法使用 PSTN 呼叫來放置1： n 呼叫使用者和 PSTN 號碼，您必須指定被呼叫者的電話號碼。
您的通訊服務資源必須設定為允許 PSTN 呼叫：
```java
CommunicationUser acsUser1 = new CommunicationUser(<USER_ID>);
PhoneNumber acsUser2 = new PhoneNumber("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.call(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>使用攝影機撥打1:1 電話
> [!WARNING]
> 目前只支援一個連出的本機影片串流來與影片進行呼叫，您必須使用 API 來列舉本機相機 `deviceManager` `getCameraList` 。
選取所需的相機之後，請使用它來建立 `LocalVideoStream` 實例，並將它以 `videoOptions` 陣列中的專案形式傳遞 `localVideoStream` 給 `call` 方法。
一旦呼叫連接後，它會自動開始將影片串流從選取的相機傳送到 (s) 的其他參與者。

> [!NOTE]
> 基於隱私權考慮，如果未在本機預覽影片，則不會將其分享給通話。
如需詳細資訊，請參閱 [本機相機預覽](#local-camera-preview) 。
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUser[] participants = new CommunicationUser[]{ new CommunicationUser("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.call(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>加入群組通話
若要啟動新的群組呼叫或加入進行中的群組呼叫，您必須呼叫 ' join ' 方法，並傳遞具有屬性的物件 `groupId` 。 值必須是 GUID。
```java
Context appContext = this.getApplicationContext();
GroupCallContext groupCallContext = new groupCallContext("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallContext, joinCallOptions);
```

## <a name="push-notifications"></a>推送通知

### <a name="overview"></a>概觀
行動推播通知是您在行動裝置上看到的快顯通知。 為了進行呼叫，我們將著重于 VoIP (語音 over 網際網路通訊協定) 推播通知。 我們會註冊推播通知、處理推播通知，然後取消註冊推播通知。

### <a name="prerequisites"></a>先決條件

使用雲端通訊 (FCM 設定的 Firebase 帳戶，會在已連線到 Azure 通知中樞實例的 Firebase 雲端通訊服務) 啟用。 如需詳細資訊，請參閱 [通訊服務通知](../../../concepts/notifications.md) 。
此外，本教學課程假設您使用 Android Studio 3.6 版或更高版本來建立您的應用程式。

Android 應用程式需要一組許可權，才能接收來自 Firebase 雲端通訊的通知訊息。 在您的檔案中 `AndroidManifest.xml` ，將下列許可權集加入 *<資訊清單 ... >* 或在 *</application>* 標記下方

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>註冊推播通知

若要註冊推播通知，應用程式必須 `registerPushNotification()` 在具有裝置註冊權杖的 *CallAgent* 實例上呼叫。

若要取得裝置註冊權杖，請將 Firebase 用戶端程式庫新增至您應用程式模組的 *gradle* 檔案，方法是在區段中新增下列幾行 `dependencies` （如果它還不存在）：

```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

在專案層級的 *gradle* 檔案中，于區段中新增下列專案（ `dependencies` 如果尚未存在）：

```
    classpath 'com.google.gms:google-services:4.3.3'
```

將下列外掛程式新增至檔案的開頭（如果尚未存在）：

```
apply plugin: 'com.google.gms.google-services'
```

選取工具列中的 [ *立即同步* ]。 新增下列程式碼片段，以取得用戶端應用程式實例之 Firebase 雲端通訊用戶端程式庫所產生的裝置註冊權杖，請務必將下列匯入新增至實例的主要活動標頭。 程式碼片段需要這些參數才能取得權杖：

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

新增此程式碼片段以取得權杖：

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
向呼叫服務用戶端程式庫註冊註冊推播通知的裝置註冊權杖：

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>推播通知處理

若要接收傳入的來電推播通知，請在具有承載的 *CallAgent* 實例上呼叫 *HandlePushNotification ( # B1* 。

若要從 Firebase 雲端通訊取得承載，請從建立新的服務 (檔開始，> 新的 > Service > 服務) ，擴充 *FirebaseMessagingService* Firebase 用戶端程式庫類別並覆寫 `onMessageReceived` 方法。 當 Firebase 雲端通訊將推播通知傳遞給應用程式時，這個方法就是呼叫的事件處理常式。

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
將下列服務定義新增至檔案的 `AndroidManifest.xml` <application> 標記內：

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- 一旦抓取承載之後，您就可以在 *CallAgent* 實例上呼叫 *handlePushNotification* 方法，將它傳遞至 *通訊服務* 用戶端程式庫以進行處理。 藉 `CallAgent` 由 `createCallAgent(...)` 在類別上呼叫方法來建立實例 `CallClient` 。

```java
try {
    callAgent.handlePushNotification(pushNotificationMessageDataFromFCM).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

當處理推播通知訊息成功，而且已正確註冊所有事件處理常式時，應用程式會響鈴。

### <a name="unregister-push-notifications"></a>取消註冊推播通知

應用程式可以隨時取消註冊推播通知。 `unregisterPushNotification()`在 callAgent 上呼叫方法來取消註冊。

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>通話管理
您可以存取呼叫屬性，並在呼叫以管理與影片和音訊相關的設定時執行各種作業。

### <a name="call-properties"></a>呼叫屬性

取得此呼叫的唯一識別碼：

```java
String callId = call.getCallId();
```

若要在實例上瞭解呼叫檢查集合中的其他參與者 `remoteParticipant` `call` ：

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

呼叫的傳入時，呼叫端的身分識別：

```java
CommunicationIdentifier callerId = call.getCallerId();
```

取得通話的狀態： 

```java
CallState callState = call.getState();
```

它會傳回代表呼叫之目前狀態的字串：
* ' None '-初始撥號狀態
* 「連入」-指出來電為傳入，必須接受或拒絕
* 「連線」-進入或接受呼叫後的初始轉換狀態
* 「響鈴」-若為外寄呼叫-表示電話正在撥打遠端參與者，它是「連入」的那一端
* ' EarlyMedia '-表示在連接電話之前播放公告的狀態
* 「已連線」-呼叫已連線
* 「保留」-通話處於保留狀態，本機端點與遠端參與者之間沒有媒體流動 () 
* 「中斷連接」-呼叫之前的轉換狀態會進入「已中斷連線」狀態
* 「已中斷連線」-最終撥號狀態


若要瞭解通話結束的原因，請檢查 `callEndReason` 屬性。 它包含程式碼/子代碼： 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

若要查看目前的呼叫是否為傳入的呼叫，請檢查 `isIncoming` 屬性：

```java
boolean isIncoming = call.getIsIncoming();
```

若要查看目前的麥克風是否已靜音，請檢查 `muted` 屬性：

```java
boolean muted = call.getIsMicrophoneMuted();
```

若要檢查主動式影片串流，請檢查 `localVideoStreams` 集合：

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>靜音和取消靜音

若要將本機端點靜音或取消靜音，您可以使用 `mute` 和 `unmute` 非同步 api：

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>啟動和停止傳送本機影片

若要開始影片，您必須使用物件上的 API 來列舉相機 `getCameraList` `deviceManager` 。 然後，建立新的實例以 `LocalVideoStream` 傳遞所需的相機，並在 API 中將它 `startVideo` 當作引數傳遞：

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
videoOptions = new VideoOptions(currentVideoStream);
Future startVideoFuture = call.startVideo(currentVideoStream);
startVideoFuture.get();
```

當您成功開始傳送影片之後， `LocalVideoStream` 實例就會新增至 `localVideoStreams` 呼叫實例上的集合。

```java
currentVideoStream == call.getLocalVideoStreams().get(0);
```

若要停止本機影片，請傳遞 `localVideoStream` 集合中可用的實例 `localVideoStreams` ：

```java
call.stopVideo(localVideoStream).get();
```

在實例上叫用時，您可以切換到不同的相機裝置 `switchSource` `localVideoStream` ：
```java
localVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>遠端參與者管理

所有遠端參與者都以 `RemoteParticipant` 類型表示，並可透過 `remoteParticipants` 呼叫實例上的集合來使用。

### <a name="list-participants-in-a-call"></a>列出通話中的參與者
`remoteParticipants`集合會傳回指定呼叫中的遠端參與者清單：
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>遠端參與者屬性
任何指定的遠端參與者都有一組與其相關聯的屬性和集合：

* 取得此遠端參與者的識別碼。
身分識別是 ' Identifier ' 類型的其中之一
```java
CommunicationIdentifier participantIdentity = remoteParticipant.getIdentifier();
```

* 取得此遠端參與者的狀態。
```java
ParticipantState state = remoteParticipant.getState();
```
狀態可以是下列其中一項
* 「閒置」-初始狀態
* 當參與者連接到呼叫時，「正在連接」-轉換狀態
* 「已連線」-參與者已連線到通話
* 「保留」-參與者正在保存
* ' EarlyMedia '-參與者連接到通話之前會播放公告
* 「已中斷連線」-最終狀態-參與者已與呼叫中斷連線


* 若要瞭解參與者離開通話的原因，請檢查 `callEndReason` 屬性：
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* 若要檢查此遠端參與者是否為靜音，請檢查 `isMuted` 屬性：
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* 若要檢查此遠端參與者是否正在說話，請檢查 `isSpeaking` 屬性：
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* 若要檢查指定的參與者在此呼叫中傳送的所有影片資料流程，請檢查 `videoStreams` 集合：
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>將參與者新增至來電

若要將參與者加入至呼叫 (使用者或電話號碼) 您可以叫用 `addParticipant` 。 這會同步傳回遠端參與者實例。

```java
const acsUser = new CommunicationUser("<acs user id>");
const acsPhone = new PhoneNumber("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone);
```

### <a name="remove-participant-from-a-call"></a>從呼叫中移除參與者
若要從呼叫中移除參與者 (可以叫用使用者或電話號碼) `removeParticipant` 。
當參與者從呼叫中移除之後，就會以非同步方式解決此問題。
參與者也會從集合中移除 `remoteParticipants` 。
```java
RemoteParticipant remoteParticipant = call.getParticipants().get(0);
call.removeParticipant(acsUser).get();
call.removeParticipant(acsPhone).get();
```

## <a name="render-remote-participant-video-streams"></a>呈現遠端參與者影片串流
若要列出遠端參與者的影片串流和螢幕共用串流，請檢查 `videoStreams` 集合：
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
若要 `RemoteVideoStream` 從遠端參與者呈現，您必須訂閱 `OnVideoStreamsUpdated` 事件。

在事件中，將屬性變更 `isAvailable` 為 true 表示遠端參與者目前正在傳送資料流程。 一旦發生這種情況，請建立新的實例 `Renderer` ，然後使用非同步 API 建立新的， `RendererView` `createView` 並 `view.target` 在應用程式 UI 中的任何位置附加。

當遠端資料流的可用性有所變更時，您可以選擇終結整個轉譯器、特定 `RendererView` 或保留，但這會導致顯示空白的影片畫面。

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>遠端影片資料流程屬性
遠端視訊串流有幾個屬性

* `Id` -遠端影片資料流程的識別碼
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -可以是「影片」或「ScreenSharing」
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` -指出遠端參與者端點是否正在主動傳送資料流程
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>轉譯器方法和屬性
下列 Api 的轉譯器物件

* 建立 `RendererView` 可稍後在應用程式 UI 中附加以轉譯遠端影片串流的實例。
```java
// Create a view for a video stream
renderer.createView()
```
* 處置轉譯器和所有 `RendererView` 與此轉譯器相關聯的。 當您從 UI 移除所有相關聯的視圖時要呼叫的。
```java
renderer.dispose()
```

* `StreamSize` -調整遠端影片串流 (寬度/高度) 
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>RendererView 方法和屬性
建立時， `RendererView` 您可以指定 `scalingMode` 將套用 `mirrored` 至此視圖的和屬性：調整模式可以是 [延展] |「裁剪」 |' Fit ' 如果 `mirrored` 設定為 `true` ，則轉譯的資料流程會垂直翻轉。

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

然後，您可以使用下列程式碼片段，將建立的 RendererView 附加至應用程式 UI：
```java
layout.addView(rendererView);
```

您稍後可以在 `updateScalingMode` RendererView 物件上叫用具有 ScalingMode 的其中一個的 API 來更新調整模式 |ScalingMode。ScalingMode：符合引數。
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>裝置管理

`DeviceManager` 可讓您列舉可在呼叫中用來傳輸音訊/影片串流的本機裝置。 它也可讓您向使用者要求許可權，以使用原生瀏覽器 API 來存取其麥克風和相機。

您可以藉 `deviceManager` 由呼叫 `callClient.getDeviceManager()` 方法來存取。
> [!WARNING]
> 目前 `callAgent` 必須先將物件具現化，才能取得 DeviceManager 的存取權。

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>列舉本機裝置

若要存取本機裝置，您可以在裝置管理員上使用列舉方法。 列舉是同步動作。

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>設定預設麥克風/喇叭

裝置管理員可讓您設定開始通話時將使用的預設裝置。
如果未設定用戶端預設值，通訊服務會切換回作業系統預設值。

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophoneList().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakerList().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>本機相機預覽

您可以使用 `DeviceManager` 和 `Renderer` 來開始從您的本機相機呈現資料流程。 此資料流程不會傳送給其他參與者;它是本機預覽摘要。 這是非同步動作。

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>事件模型
您可以訂閱大部分的屬性和集合，以便在值變更時收到通知。

### <a name="properties"></a>屬性
若要訂閱 `property changed` 事件：

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnCallStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnCallStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>集合
若要訂閱 `collection updated` 事件：

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```