---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 6922ab2aac8529da8ba55a98f465e3c0e3123b53
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934671"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通訊服務資源。 [建立通訊服務資源](../../create-communication-resource.md)。
- `User Access Token`要啟用呼叫用戶端的。 如需[如何取得的 `User Access Token` ](../../access-tokens.md)詳細資訊
- 選擇性：完成快速入門以 [開始將呼叫新增至您的應用程式](../getting-started-with-calling.md)

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>安裝用戶端程式庫

使用 `npm install` 命令來安裝適用于 JavaScript 的 Azure 通訊服務呼叫和一般用戶端程式庫。

```console
npm install @azure/communication-common --save

npm install @azure/communication-calling --save

```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務呼叫用戶端程式庫的一些主要功能：

| Name                             | 描述                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient 是呼叫用戶端程式庫的主要進入點。                                                                       |
| CallAgent                        | CallAgent 可用來啟動及管理呼叫。                                                                                            |
| AzureCommunicationUserCredential | AzureCommunicationUserCredential 類別會實 CommunicationUserCredential 介面，用來將 CallAgent 具現化。 |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>將 CallClient、create CallAgent 和 access DeviceManager 初始化

具現化新的 `CallClient` 實例。 您可以使用記錄器實例之類的自訂選項來設定它。
一旦具 `CallClient` 現化之後，您就可以 `CallAgent` `createCallAgent` 在實例上呼叫方法來建立實例 `CallClient` 。 這會以非同步方式傳回 `CallAgent` 實例物件。
`createCallAgent`方法會採用 `CommunicationUserCredential` 做為引數，以接受[使用者存取權杖](../../access-tokens.md)。
若要存取 `DeviceManager` callAgent 實例，必須先建立。 然後，您可以 `getDeviceManager` 在實例上使用方法 `CallClient` 來取得 DeviceManager。

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationUserCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential);
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>撥出來電

若要建立並開始通話，您需要在 CallAgent 上使用其中一個 Api，並提供您透過通訊服務管理用戶端程式庫建立的使用者。

呼叫建立和啟動是同步的。 呼叫實例可讓您訂閱呼叫事件。

## <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>使用使用者和 PSTN 來撥打電話給使用者或1： n 呼叫1:1

若要呼叫另一個通訊服務使用者，請 `call` 在上叫用方法， `callAgent` 並傳遞您 [使用通訊服務管理程式庫所建立](../../access-tokens.md)的 CommunicationUser。

```js
const oneToOneCall = callAgent.call([CommunicationUser]);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>使用使用者和 PSTN 撥出1： n 呼叫
> [!WARNING]
> PSTN 呼叫目前處於私人預覽狀態。 若要存取，請 [申請早期採用者計畫](https://aka.ms/ACS-EarlyAdopter)。
若要對使用者和 PSTN 號碼進行1： n 呼叫，您必須指定 CommunicationUser 和兩個被呼叫端的電話號碼。
您的通訊服務資源必須設定為允許 PSTN 呼叫。
```js

const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const groupCall = callClient.call([userCallee, pstnCallee], placeCallOptions);

```

### <a name="place-a-11-call-with-with-video-camera"></a>使用攝影機撥打1:1 電話
> [!WARNING]
> 目前不能有一個以上的傳出本機影片串流。
若要撥打影片，您必須使用 deviceManager API 來列舉本機相機 `getCameraList` 。
選取所需的相機之後，請使用它來建立 `LocalVideoStream` 實例，並將它以 `videoOptions` 陣列中的專案形式傳遞 `localVideoStream` 給 `call` 方法。
一旦您的電話連線，它會自動開始將影片串流從選取的相機傳送到其他參與者 (s) 
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callClient.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>加入群組呼叫
若要啟動新的群組呼叫或加入進行中的群組呼叫，請使用 ' join ' 方法並傳遞具有屬性的物件 `groupId` 。 值必須是 GUID。
```js

const context = { groupId: <GUID>}
const call = callClient.join(context);

```

## <a name="call-management"></a>通話管理

您可以存取呼叫屬性，並在呼叫以管理與影片和音訊相關的設定時執行各種作業。

### <a name="call-properties"></a>呼叫屬性
* 取得此呼叫的唯一識別碼。
```js

const callId: string = call.id;

```

* 若要瞭解呼叫中的其他參與者，請檢查 `remoteParticipant` 實例上的集合 `call` 。
```js
const remoteParticipants: RemoteParticipants = call.remoteParticipants;
```

* 呼叫的傳入時，呼叫端的身分識別。
```js

const callerIdentity = call.callerIdentity;

```

* 取得呼叫的狀態。
```js

const callState: CallState = call.state;

```
這會傳回代表呼叫之目前狀態的字串：
* ' None '-初始撥號狀態
* 「傳入」-表示來電為傳入，必須接受或拒絕
* 「連線」-進入或接受呼叫後的初始轉換狀態
* 「響鈴」-若為外寄呼叫-表示電話正在撥打遠端參與者，其端為「傳入」
* ' EarlyMedia '-表示在連接電話之前播放公告的狀態
* 「已連線」-呼叫已連線
* 「保留」-通話處於保留狀態，本機端點與遠端參與者之間沒有媒體流動 () 
* 「中斷連接」-在呼叫進入「中斷連線」狀態之前的轉換狀態
* 「已中斷連線」-最終撥號狀態


* 若要查看給定通話結束的原因，請檢查 `callEndReason` 屬性。
```js

const callEndReason: CallEndReason = call.callEndReason;

```

* 若要瞭解目前的呼叫是否為傳入的呼叫，請檢查 `isIncoming` 屬性
```js

const isIncoming: boolean = call.isIncoming;

```

*  若要檢查目前的麥克風是否已靜音，請檢查 `muted` 屬性：
```js

const muted: boolean = call.isMicrophoneMuted;

```

* 若要查看是否從指定的端點傳送螢幕共用串流，請檢查 `isScreenSharingOn` 屬性：
```js

const isScreenSharingOn: boolean = call.isScreenSharingOn;

```

* 若要檢查主動式影片串流，請檢查 `localVideoStreams` 集合：
```js

const localVideoStreams: LocalVideoStream[] = call.localVideoStreams;

```

### <a name="mute-and-unmute"></a>靜音和取消靜音

若要將本機端點靜音或取消靜音，您可以使用 `mute` 和 `unmute` 非同步 api：

```js

//mute local device 
await call.mute();

//unmute device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>啟動和停止傳送本機影片


若要開始影片，您必須 `getCameraList` 在物件上使用方法來列舉相機 `deviceManager` 。 然後，建立新的實例，將 `LocalVideoStream` 所需的相機 `startVideo` 以引數的形式傳遞至方法：


```js
const localVideoStream = new SDK.LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

當您成功開始傳送影片之後， `LocalVideoStream` 實例就會新增至 `localVideoStreams` 呼叫實例上的集合。

```js

call.localVideoStreams[0] === localVideoStream;

```

若要停止本機影片，請傳遞 `localVideoStream` 集合中可用的實例 `localVideoStreams` ：

```js

await call.stopVideo(localVideoStream);

```

在實例上叫用時，您可以切換到不同的相機裝置 `switchSource` `localVideoStream` ：

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```

## <a name="remote-participants-management"></a>遠端參與者管理

所有遠端參與者都以 `RemoteParticipant` 類型表示，並可透過 `remoteParticipants` 集合在呼叫實例上使用。

### <a name="list-participants-in-a-call"></a>列出通話中的參與者
`remoteParticipants`集合會傳回指定呼叫中的遠端參與者清單：

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>遠端參與者屬性
遠端參與者有一組與其相關聯的屬性和集合

* 取得此遠端參與者的識別碼。
身分識別是 ' Identifier ' 類型的其中一個：
```js

const identity: CommunicationUser | PhoneNumber | CallingApplication | UnknownIdentifier;

```

* 取得此遠端參與者的狀態。
```js

const state: RemoteParticipantState = remoteParticipant.state;
```
狀態可以是下列其中一項
* 「閒置」-初始狀態
* 當參與者連接到呼叫時，「正在連接」-轉換狀態
* 「已連線」-參與者已連線到通話
* 「保留」-參與者正在保存
* ' EarlyMedia '-參與者連接到通話之前會播放公告
* 「已中斷連線」-最終狀態-參與者已與呼叫中斷連線

若要瞭解參與者離開通話的原因，請檢查 `callEndReason` 屬性：
```js

const callEndReason: CallEndReason = remoteParticipant.callEndReason;

```

* 若要檢查此遠端參與者是否為靜音，請檢查 `isMuted` 屬性：
```js

const isMuted: boolean = remoteParticipant.isMuted;

```

* 若要檢查此遠端參與者是否正在說話，請檢查 `isSpeaking` 屬性：
```js

const isSpeaking: boolean = remoteParticipant.isSpeaking;

```

* 若要檢查給定參與者在此呼叫中傳送的所有影片資料流程，請檢查 `videoStreams` 集合：
```js

const videoStreams: RemoteVideoStream[] = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>將參與者新增至來電

若要將參與者加入至呼叫 (使用者或電話號碼) 您可以叫用 `addParticipant` 。
提供其中一個「識別碼」類型。
這會同步傳回遠端參與者實例。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier);

```

### <a name="remove-participant-from-a-call"></a>從呼叫中移除參與者

若要從呼叫中移除參與者 (可以叫用使用者或電話號碼) `removeParticipant` 。
當參與者從呼叫中移除之後，您必須傳遞其中一個 ' identifier ' 類型。
參與者也會從集合中移除 `remoteParticipants` 。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>呈現遠端參與者影片串流

若要列出遠端參與者的影片串流和螢幕共用串流，請檢查 `videoStreams` 集合：

```js

const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
若要呈現 `RemoteVideoStream` ，您必須訂閱 `isAvailableChanged` 事件。
如果 `isAvailable` 屬性變更為 `true` ，遠端參與者就會傳送資料流程。
一旦發生這種情況，請建立的新實例 `Renderer` ，然後 `RendererView` 使用非同步方法建立新的實例 `createView` 。  然後您可以附加 `view.target` 至任何 UI 元素。
當遠端資料流的可用性有所變更時，您可以選擇終結整個轉譯器、特定 `RendererView` 或保留，但這會導致顯示空白的影片畫面。

```js
let renderer: Renderer;
const displayVideo = () => {
    renderer = new Renderer(remoteParticipantStream);
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>遠端影片資料流程屬性
遠端視訊串流具有下列屬性：

* `Id` -遠端影片資料流程的識別碼
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -調整遠端影片串流 ( 寬度/高度 ) 
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -可以是「影片」或「ScreenSharing」
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` -指出遠端參與者端點是否正在主動傳送資料流程
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>轉譯器方法和屬性

* 建立 `RendererView` 可稍後在應用程式 UI 中附加以轉譯遠端視訊資料流程的實例。
```js
renderer.createView()
```

* 處置轉譯器和所有相關聯的 `RendererView` 實例。
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererView 方法和屬性
建立時 `RendererView` ，您可以指定 `scalingMode` 和 `mirrored` 屬性。
調整模式可以是「延展」、「裁剪」或「調整」（如果 `Mirrored` 指定的話），轉譯的資料流程將會垂直翻轉。

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
任何指定的 `RendererView` 實例都有 `target` 代表呈現介面的屬性。 這必須在應用程式 UI 中附加：
```js
document.body.appendChild(rendererView.target);
```

您稍後可以藉由叫用方法來更新調整模式 `updateScalingMode` 。
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>裝置管理

`DeviceManager` 可讓您列舉可在呼叫中用來傳輸音訊/影片串流的本機裝置。 它也可讓您向使用者要求許可權，以使用原生瀏覽器 API 來存取其麥克風和相機。

您可以透過 `deviceManager` 呼叫方法來存取 `callClient.getDeviceManager()` 。
> [!WARNING]
> 目前 `callAgent` 必須先將物件具現化，才能取得 DeviceManager 的存取權。

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>列舉本機裝置

若要存取本機裝置，您可以在裝置管理員上使用列舉方法。 列舉是同步動作。

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>設定預設麥克風/喇叭

裝置管理員可讓您設定開始通話時將使用的預設裝置。
如果未設定用戶端預設值，通訊服務會切換回作業系統預設值。

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceMicrophone.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>本機相機預覽

您可以使用 `DeviceManager` 和 `Renderer` 來開始從您的本機相機呈現資料流程。 此資料流程不會傳送給其他參與者;它是本機預覽摘要。 這是非同步動作。

```js
const localVideoDevice = deviceManager().getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>要求相機/麥克風的許可權

提示使用者授與相機/麥克風許可權，如下所示：

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
這會使用指出是否 `audio` 已授與許可權的物件來非同步解析 `video` ：
```js
console.log(result.audio);
console.log(result.video);
```

您可以藉由呼叫下列方法，檢查指定類型目前的許可權狀態 `getPermissionState` ：

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="eventing-model"></a>事件模型

您可以訂閱大部分的屬性和集合，以便在值變更時收到通知。

### <a name="properties"></a>屬性
若要訂閱 `property changed` 事件：

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>集合
若要訂閱 `collection updated` 事件：

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
