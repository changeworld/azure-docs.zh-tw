---
title: 設定 Unity 的遠端轉譯
description: 如何在 Unity 專案中初始化 Azure 遠端呈現
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: f3400d82a6aa184daabfa2ebbe6b775b8e4c1562
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565453"
---
# <a name="set-up-remote-rendering-for-unity"></a>設定 Unity 的遠端轉譯

為了在 Unity 中啟用 Azure 遠端轉譯（ARR），我們提供了一些專門的方法來處理一些 Unity 特有的層面。

## <a name="startup-and-shutdown"></a>啟動和關閉

若要初始化遠端呈現，請使用 `RemoteManagerUnity` 。 這個類別會呼叫泛型， `RemoteManager` 但已為您執行 Unity 特定的詳細資料。 例如，Unity 使用特定的座標系統。 呼叫時 `RemoteManagerUnity.Initialize` ，將會設定適當的慣例。 呼叫也會要求您提供應該用來顯示遠端呈現內容的 Unity 攝影機。

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

若要關閉遠端轉譯，請呼叫 `RemoteManagerStatic.ShutdownRemoteRendering()` 。

`AzureSession`建立並選擇做為主要呈現會話之後，必須向註冊 `RemoteManagerUnity` ：

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>完整範例程式碼

下列程式碼示範在 Unity 中初始化 Azure 遠端呈現所需的所有步驟：

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>便利性函式

### <a name="session-state-events"></a>會話狀態事件

`RemoteManagerUnity.OnSessionUpdate`當其會話狀態變更時，會發出的事件，如需詳細資訊，請參閱程式碼檔。

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`是簡化設定和會話管理的選用元件。 其中包含的選項可讓您在應用程式結束時自動停止其會話，或在編輯器中離開播放模式，以及在需要時自動更新會話租用。 它會快取諸如會話屬性（請參閱其 `LastProperties` 變數）之類的資料，並公開會話狀態變更和會話錯誤的事件。

一次不能有一個以上的實例 `ARRServiceUnity` 。 其目的是要讓您藉由執行一些常見的功能來加快入門速度。 不過，如果是較大的應用程式，最好是自行執行這些動作。

如需如何設定和使用的範例， `ARRServiceUnity` 請參閱[教學課程：觀看遠端呈現的模型](../../tutorials/unity/view-remote-models/view-remote-models.md)。

## <a name="next-steps"></a>後續步驟

* [安裝適用於 Unity 的遠端轉譯套件](install-remote-rendering-unity-package.md)
* [教學課程：觀看遠端呈現的模型](../../tutorials/unity/view-remote-models/view-remote-models.md)
