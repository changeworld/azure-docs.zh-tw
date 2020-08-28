---
title: 設定 Unity 的遠端轉譯
description: 如何初始化 Unity 專案中的 Azure 遠端轉譯
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a0be44d8709726e159e17e703566c6c576bc18f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018972"
---
# <a name="set-up-remote-rendering-for-unity"></a>設定 Unity 的遠端轉譯

為了在 Unity 中啟用 Azure 遠端轉譯 (ARR) ，我們提供了專門的方法來處理某些 Unity 特定層面。

## <a name="startup-and-shutdown"></a>啟動和關閉

若要初始化遠端呈現，請使用 `RemoteManagerUnity` 。 此類別會呼叫泛型， `RemoteManager` 但已為您執行 Unity 特定的詳細資料。 例如，Unity 會使用特定的座標系統。 呼叫時 `RemoteManagerUnity.Initialize` ，將會設定適當的慣例。 此呼叫也會要求您提供 Unity 攝影機，以用於顯示遠端呈現的內容。

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

若要關閉遠端轉譯，請呼叫 `RemoteManagerStatic.ShutdownRemoteRendering()` 。

在 `AzureSession` 建立並選擇作為主要轉譯會話之後，必須向註冊 `RemoteManagerUnity` ：

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>完整的範例程式碼

下列程式碼示範在 Unity 中初始化 Azure 遠端轉譯所需的所有步驟：

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

## <a name="convenience-functions"></a>便利性函數

### <a name="session-state-events"></a>會話狀態事件

`RemoteManagerUnity.OnSessionUpdate` 當事件的會話狀態變更時，會發出事件。如需詳細資料，請參閱程式碼檔。

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` 是簡化設定和會話管理的選擇性元件。 它包含可在應用程式結束時自動停止其會話的選項，或在編輯器中結束播放模式，以及在需要時自動更新會話租用的選項。 它會快取資料（例如會話屬性） (查看其 `LastProperties` 變數) ，並公開會話狀態變更和會話錯誤的事件。

一次不能有一個以上的實例 `ARRServiceUnity` 。 它的目的是要讓您更快速地開始執行一些常見的功能。 若為較大的應用程式，則最好是自行進行這些操作。

如需如何設定和使用的範例， `ARRServiceUnity` 請參閱 [教學課程：觀看遠端呈現的模型](../../tutorials/unity/view-remote-models/view-remote-models.md)。

## <a name="next-steps"></a>後續步驟

* [安裝適用於 Unity 的遠端轉譯套件](install-remote-rendering-unity-package.md)
* [教學課程：檢視遠端轉譯模型](../../tutorials/unity/view-remote-models/view-remote-models.md)
