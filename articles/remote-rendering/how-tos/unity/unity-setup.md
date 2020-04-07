---
title: 為統一設定遠端的成像
description: 如何在 Unity 專案中初始化 Azure 遠端呈現
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681138"
---
# <a name="set-up-remote-rendering-for-unity"></a>為統一設定遠端的成像

為了在 Unity 中啟用 Azure 遠端呈現 (ARR),我們提供專門的方法,用於處理一些特定於 Unity 的方面。

## <a name="startup-and-shutdown"></a>啟動與關閉

要初始化遠端的成`RemoteManagerUnity`像, 請使用 。 此類調用泛型`RemoteManager`,但已為您實現特定於 Unity 的詳細資訊。 例如,Unity 使用特定的座標系。 調用`RemoteManagerUnity.Initialize`時,將設置適當的約定。 該呼叫還要求您提供 Unity 攝像機,該攝像機應用於顯示遠端呈現的內容。

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

對遠端的成像,請呼`RemoteManagerStatic.ShutdownRemoteRendering()`叫 。

建立`AzureSession`並選擇 做為主呈現工作階段後,必須`RemoteManagerUnity`將其註冊到 :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>完整範例碼

以下代碼展示了在 Unity 中初始化 Azure 遠端呈現所需的所有步驟:

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

## <a name="convenience-functions"></a>便利功能

### <a name="session-state-events"></a>工作階段狀態事件

`RemoteManagerUnity.OnSessionUpdate`發出會話狀態更改時的事件,有關詳細資訊,請參閱代碼文檔。

### <a name="arrserviceunity"></a>ARR 服務團結

`ARRServiceUnity`是簡化設置和會話管理的可選元件。 它包含在編輯器中退出應用程式或播放模式時自動停止其工作階段的選項,以及在需要時自動續訂會話租約。 它緩存工作階段屬性等資料(請參閱其`LastProperties`變數),並公開會話狀態更改和會話錯誤的事件。

一次不能有多個實體`ARRServiceUnity`。 它旨在通過實現一些常見功能來加快您入門速度。 然而,對於一個更大的應用程式,最好自己做這些事情。

有關如何設定和使用`ARRServiceUnity`的範例,請參閱[教學:從頭開始設定 Unity 專案](../../tutorials/unity/project-setup.md)。

## <a name="next-steps"></a>後續步驟

* [安裝遠端呈現套件以進行統一](install-remote-rendering-unity-package.md)
* [教學:從頭開始設定 Unity 專案](../../tutorials/unity/project-setup.md)
