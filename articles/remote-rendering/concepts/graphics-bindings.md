---
title: 圖形繫結
description: 圖形繫結設定和使用案例
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 4854d5ff9d697a2bf082a788c0e761a2152b0294
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758702"
---
# <a name="graphics-binding"></a>圖形繫結

若要能在自訂應用程式中使用 Azure 遠端轉譯，就必須將其整合到應用程式的轉譯管線中。 這項整合作業屬於圖形繫結的範疇。

設定完成後，圖形繫結會將存取權授予會影響轉譯影像的不同函式。 這類函式可以分成兩種類別：隨時可供使用的一般函式，以及只與所選 `Microsoft.Azure.RemoteRendering.GraphicsApiType` 相關的特定函式。

## <a name="graphics-binding-in-unity"></a>Unity 中的圖形繫結

在 Unity 中，整個繫結程序是由傳遞至 `RemoteManagerUnity.InitializeManager` 的 `RemoteUnityClientInit` 結構處理。 若要設定圖形模式，`GraphicsApiType` 欄位必須設定為所選的繫結。 系統會依據 XRDevice 是否存在而自動填入欄位。 您可以使用下列行為來手動覆寫行為：

* **HoloLens 2**：一律使用 [Windows Mixed Reality](#windows-mixed-reality) 圖形繫結。
* **平面 UWP 傳統型應用程式**：一律使用[模擬](#simulation)。 若要使用此模式，請務必遵循[教學課程：從頭開始設定 Unity 專案](../tutorials/unity/project-setup.md)。
* **Unity 編輯器**：除非連接 WMR VR 頭戴式裝置，否則一律使用[模擬](#simulation)。在前述情況下會停用 ARR，以允許對應用程式的非 ARR 相關部分進行偵錯。 並請參閱[全像攝影遠端處理](../how-tos/unity/holographic-remoting.md)。

Unity 另外唯一相關的部分是存取[基本繫結](#access)，至於以下所有其他章節則可略過。

## <a name="graphics-binding-setup-in-custom-applications"></a>自訂應用程式中的圖形繫結設定

若要選取圖形繫結，請進行以下兩個步驟：首先在初始化程式時，必須以靜態方式將圖形繫結初始化：

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

需要上述呼叫，才能將 Azure 遠端轉譯初始化為全像攝影 API。 您必須先呼叫此函式，再呼叫任何全像攝影 API 及存取其他任何遠端轉譯 API。 同樣地，不再呼叫任何全像攝影 API 之後，則應呼叫對應的取消初始化函式 `RemoteManagerStatic.ShutdownRemoteRendering();`。

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">存取圖形繫結

設定好用戶端之後，即可使用 `AzureSession.GraphicsBinding` getter 存取基本圖形繫結。 例如可擷取最後一個畫面的統計資料：

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>圖形 API

目前有兩個圖形 API 可供選取，即 `WmrD3D11` 和 `SimD3D11`。 雖然有第三個 API `Headless`，但用戶端尚未支援。

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` 是要在 HoloLens 2 上執行的預設繫結。 其會建立 `GraphicsBindingWmrD3d11` 繫結。 在此模式中，Azure 遠端轉譯會直接在全像攝影 API 內攔截。

若要存取衍生的圖形繫結，就必須轉換基底 `GraphicsBinding`。
若要使用 WMR 繫結，必須先執行兩件事：

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>將所使用的座標系統告知遠端轉譯

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```


上面的 `ptr` 必須是原生 `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` 物件的指標，以其定義世界空間的座標系統，API 中的座標即是用此系統表示。

#### <a name="render-remote-image"></a>轉譯遠端影像

在每個畫面的開始，需將遠端畫面轉譯成背景緩衝區。 這可藉由呼叫 `BlitRemoteFrame` 來完成，其會在目前繫結的轉譯目標中填入色彩和深度資訊。 因此，將背景緩衝區繫結為轉譯目標後，請務必完成此項作業。

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>模擬

`GraphicsApiType.SimD3D11` 是模擬繫結，如果將之選取，則會建立 `GraphicsBindingSimD3d11` 圖形繫結。 此介面可用來模擬頭部移動，例如在傳統型應用程式中轉譯平面視覺影像的情況。
這種設定方式較為複雜，運作方式如下：

#### <a name="create-proxy-render-target"></a>建立 Proxy 轉譯目標

必須使用 `GraphicsBindingSimD3d11.Update` 函式提供的 Proxy 相機資料，將遠端和本機內容轉譯成螢幕上不可見的色彩/深度轉譯目標，稱為「Proxy」。 Proxy 必須符合背景緩衝區的解析度。 工作階段準備就緒後，需先呼叫 `GraphicsBindingSimD3d11.InitSimulation`，才能與其連線：

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

需要搭配指標將 init 函式提供給原生 D3D 裝置，以及 Proxy 轉譯目標的色彩及深度紋理。 初始化之後可以呼叫多次 `AzureSession.ConnectToRuntime` 和 `DisconnectFromRuntime`，但切換至不同的工作階段時，需先在舊的工作階段上呼叫 `GraphicsBindingSimD3d11.DeinitSimulation`，然後才能在另一個工作階段上呼叫 `GraphicsBindingSimD3d11.InitSimulation`。

#### <a name="render-loop-update"></a>轉譯迴圈更新

轉譯迴圈更新是由多個步驟組成：

1. 在進行任何轉譯之前，每個畫面都會使用目前要傳送至伺服器轉譯的相機轉換來呼叫 `GraphicsBindingSimD3d11.Update`。 同時，應將傳回的 Proxy 轉換套用到 Proxy 相機，以轉譯成 Proxy 轉譯目標。
如果傳回的 proxy 更新 `SimulationUpdate.frameId` 是 Null，代表尚未有遠端資料。 在此情況下，請使用目前的相機資料，直接將任何本機內容轉譯為背景緩衝區，並略過接下來的兩個步驟，而不是轉譯成 Proxy 轉譯目標。
1. 應用程式現在應該已繫結 Proxy 轉譯目標，並呼叫 `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`。 如此會將遠端色彩和深度資訊填入 Proxy 轉譯目標。 現在可以使用 Proxy 相機轉換，將任何本機內容轉譯到 Proxy 上。
1. 接下來，需將背景緩衝區繫結為轉譯目標，並呼叫 `GraphicsBindingSimD3d11.ReprojectProxy`，此時可以呈現背景緩衝區。

```cs
AzureSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>後續步驟

* [教學課程：從頭開始設定 Unity 專案](../tutorials/unity/project-setup.md)
