---
title: 圖形系結
description: 圖形系結和使用案例的設定
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681879"
---
# <a name="graphics-binding"></a>圖形系結

若要能夠在自訂應用程式中使用 Azure 遠端轉譯，它必須整合到應用程式的轉譯管線中。 這項整合是圖形系結的責任。

設定好之後，圖形系結可存取影響轉譯影像的各種功能。 這些函式可以分成兩個類別：一律可用的一般函式，以及僅與所選`Microsoft.Azure.RemoteRendering.GraphicsApiType`的相關的特定功能。

## <a name="graphics-binding-in-unity"></a>Unity 中的圖形系結

在 Unity 中，整個系結是由傳入`RemoteUnityClientInit`的結構所`RemoteManagerUnity.InitializeManager`處理。 若要設定圖形模式，必須`GraphicsApiType`將欄位設定為所選的系結。 系統會根據 XRDevice 是否存在，自動填入欄位。 您可以使用下列行為來手動覆寫行為：

* **HoloLens 2**：一律會使用[Windows Mixed Reality](#windows-mixed-reality)圖形系結。
* **平面 UWP 桌面應用程式**：一律會使用[模擬](#simulation)。 若要使用此模式，請務必依照[教學課程：從頭開始設定 Unity 專案](../tutorials/unity/project-setup.md)中的步驟進行。
* **Unity 編輯器**：一律會使用[模擬](#simulation)，除非已連接 WMR VR 頭戴式裝置，在此情況下會停用 ARR，讓能夠對應用程式的非 ARR 相關部分進行偵測。 另請參閱全像攝影[遠端](../how-tos/unity/holographic-remoting.md)。

Unity 唯一的另一個相關部分是存取[基本](#access)系結，而且可以略過下列所有其他區段。

## <a name="graphics-binding-setup-in-custom-applications"></a>自訂應用程式中的圖形系結設定

若要選取圖形系結，請執行下列兩個步驟：第一，在初始化程式時，必須以靜態方式初始化圖形系結：

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

需要上述呼叫，才能將 Azure 遠端呈現初始化為全像攝影 Api。 您必須先呼叫此函式，才能呼叫任何全像攝影 API，並在存取其他任何遠端轉譯 Api 之前。 同樣地，在不再呼叫任何全息`RemoteManagerStatic.ShutdownRemoteRendering();`型 api 之後，應該呼叫對應的取消初始化函式。

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">存取圖形系結

設定好用戶端之後，就可以使用`AzureSession.GraphicsBinding` getter 來存取基本的圖形系結。 例如，您可以如下所示抓取最後一個畫面格統計資料：

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>圖形 Api

目前有兩個可選取的圖形 Api： `WmrD3D11`和。 `SimD3D11` 第三個`Headless`存在，但用戶端尚未支援。

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`是要在 HoloLens 2 上執行的預設系結。 它會建立`GraphicsBindingWmrD3d11`系結。 在此模式中，Azure 遠端轉譯會直接攔截到全像攝影 Api。

若要存取衍生的圖形系結， `GraphicsBinding`必須轉換基底。
若要使用 WMR 系結，必須執行兩件事：

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>通知遠端呈現已使用的座標系統

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

其中，上述`ptr`必須是原生`ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem`物件的指標，其中定義了在中用來表示 API 座標的世界空間座標系統。

#### <a name="render-remote-image"></a>呈現遠端影像

在每個框架的開頭，必須將遠端框架轉譯成背景的緩衝區。 這是藉由呼叫`BlitRemoteFrame`來完成，這會在目前系結的呈現目標中填滿色彩和深度資訊。 因此，在將後置緩衝區系結為轉譯目標之後，請務必這麼做。

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>模擬

`GraphicsApiType.SimD3D11`是模擬系結，如果已選取，則`GraphicsBindingSimD3d11`會建立圖形系結。 此介面可用來模擬前端移動，例如在桌面應用程式中，並轉譯 monoscopic 的影像。
安裝程式比較複雜，且運作方式如下：

#### <a name="create-proxy-render-target"></a>建立 proxy 呈現目標

您必須使用函式所提供`GraphicsBindingSimD3d11.Update`的 proxy 攝影機資料，將遠端和本機內容轉譯成稱為「proxy」的螢幕幕後色彩/深度轉譯目標。 Proxy 必須符合後端緩衝區的解析。 會話準備就緒後，必須`GraphicsBindingSimD3d11.InitSimulation`先呼叫，才能連接到它：

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Init 函式必須提供原生 d3d 裝置的指標，以及 proxy 呈現目標的色彩和深度材質。 一旦初始化之後`AzureSession.ConnectToRuntime` ， `DisconnectFromRuntime`您可以呼叫多次，但切換至不同的會話時， `GraphicsBindingSimD3d11.DeinitSimulation`必須先在舊的會話`GraphicsBindingSimD3d11.InitSimulation`上呼叫，然後才能在另一個會話上呼叫。

#### <a name="render-loop-update"></a>轉譯迴圈更新

「轉譯迴圈更新」是由多個步驟所組成：

1. 在進行任何轉譯之前，每個框架`GraphicsBindingSimD3d11.Update`都會使用目前傳送至要呈現之伺服器的相機轉換來呼叫。 同時，傳回的 proxy 轉換應該套用至 proxy 攝影機，以轉譯成 proxy 呈現目標。
如果傳回的 proxy 更新`SimulationUpdate.frameId`是 null，則尚未有遠端資料。 在此情況下，您應該使用目前的相機資料直接將任何本機內容轉譯為後緩衝區，並略過接下來的兩個步驟，而不是轉譯成 proxy 呈現目標。
1. 應用程式現在應該系結 proxy 轉譯目標並呼叫`GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`。 這會將遠端色彩和深度資訊填入 proxy 呈現目標。 現在可以使用 proxy 相機轉換，將任何本機內容轉譯到 proxy 上。
1. 接下來，後端緩衝區必須系結為轉譯目標，並`GraphicsBindingSimD3d11.ReprojectProxy`呼叫該位置，此時可以呈現後端緩衝區。

``` cs
AzureSession currentSesson = ...;
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

## <a name="next-steps"></a>後續步驟

* [教學課程：從頭開始設定 Unity 專案](../tutorials/unity/project-setup.md)
