---
title: 圖形繫結
description: 設定圖形繫結和用例
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681879"
---
# <a name="graphics-binding"></a>圖形繫結

為了能夠在自定義應用程式中使用 Azure 遠端呈現,需要將其整合到應用程式的呈現管道中。 此整合是圖形綁定的責任。

設置后,圖形綁定允許訪問影響渲染圖像的各種功能。 這些函數可以分為兩類:始終可用的常規函數和僅與所選`Microsoft.Azure.RemoteRendering.GraphicsApiType`函數相關的特定函數。

## <a name="graphics-binding-in-unity"></a>整合的圖形

在 Unity 中,整個繫結到`RemoteUnityClientInit`的結構處理`RemoteManagerUnity.InitializeManager`。 要設置圖形模式,必須將`GraphicsApiType`欄位設置為所選綁定。 根據是否存在 XRDevice,將自動填充該欄位。 可以通過以下行為手動重寫該行為:

* **HoloLens 2**: 始終使用[Windows 混合實境](#windows-mixed-reality)圖形綁定。
* **平面 UWP 桌面應用程式**:始終使用[模擬](#simulation)。 要使用此模式,請確保按照教學中的步驟[:從頭開始設定 Unity 專案](../tutorials/unity/project-setup.md)。
* **Unity 編輯器**:除非連接 WMR VR 耳機,否則始終使用[模擬](#simulation),在這種情況下,ARR 將被禁用,以允許調試應用程式的非 ARR 相關部分。 另請參閱[全息遠端處理](../how-tos/unity/holographic-remoting.md)。

Unity 的唯一其他相關部分是訪問[基本綁定](#access),可以跳過下面的所有其他部分。

## <a name="graphics-binding-setup-in-custom-applications"></a>自訂應用程式中的圖像化連結設定

要選擇圖形綁定,請執行以下兩個步驟:首先,在初始化程式時,必須靜態初始化圖形綁定:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

上述調用對於將 Azure 遠端呈現初始化到全息 API 是必需的。 在調用任何全息 API 之前和在訪問任何其他遠端呈現 API 之前,必須調用此功能。 同樣,在不再調用全息 API`RemoteManagerStatic.ShutdownRemoteRendering();`後,應調用相應的去init函數。

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">存取圖形繫結

設置用戶端后,可以使用`AzureSession.GraphicsBinding`getter 訪問基本圖形綁定。 例如,可以像這樣檢索最後一個幀統計資訊:

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

## <a name="graphic-apis"></a>圖形 API

目前可以選擇兩個圖形`WmrD3D11`API,`SimD3D11`與 。 第三個`Headless`存在,但用戶端上尚未支援。

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`是在 HoloLens 2 上運行的默認綁定。 它將創建`GraphicsBindingWmrD3d11`綁定。 在此模式下,Azure 遠端呈現直接掛鉤到全息 API 中。

要訪問派生的圖形綁定,必須強制轉換`GraphicsBinding`基。
使用 WMR 繫結需要執行兩項操作:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>通知已使用座標系的遠端渲染

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

上面`ptr`必須是指向定義世界空間座標系的本`ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem`機 物件的指標,其中表示 API 中的座標。

#### <a name="render-remote-image"></a>影像影像

在每個幀的開頭,需要將遠端幀呈現到後台緩衝區中。 這是通過調用`BlitRemoteFrame`來完成的,這將填充當前綁定渲染目標中的顏色和深度資訊。 因此,在將後台緩衝區綁定為渲染目標後,請務必這樣做。

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>模擬

`GraphicsApiType.SimD3D11`是模擬綁定,如果選中,它將創建`GraphicsBindingSimD3d11`圖形綁定。 此介面用於模擬頭部移動,例如在桌面應用程式中並渲染單鏡圖像。
設置涉及更多,工作如下:

#### <a name="create-proxy-render-target"></a>建立代理呈現目標

使用`GraphicsBindingSimD3d11.Update`函數提供的代理攝像機數據,需要將遠端和本地內容渲染到稱為"代理"的螢幕外顏色/深度渲染目標。 代理必須匹配後台緩衝區的解析度。 工作階段準備就緒後,`GraphicsBindingSimD3d11.InitSimulation`需要在連線到工作階段之前呼叫它:

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

Init 函數需要提供指向本機 d3d 設備的指標以及代理渲染目標的顏色和深度紋理。 初始化後,`AzureSession.ConnectToRuntime``DisconnectFromRuntime`可以多次調用,但在切換到其他會話時`GraphicsBindingSimD3d11.DeinitSimulation`, 需要在舊會話上首先調用,`GraphicsBindingSimD3d11.InitSimulation`然後才能 在另一個會話上調用。

#### <a name="render-loop-update"></a>成成更新

成一個更新的更新由多個步驟組成:

1. 在進行任何渲染之前,`GraphicsBindingSimD3d11.Update`使用發送到要渲染的伺服器的當前攝像機轉換調用每個幀。 同時,返回的代理轉換應用於代理攝像機以呈現為代理渲染目標。
如果返回的代理更新`SimulationUpdate.frameId`為空,則尚未有遠程數據。 在這種情況下,不應渲染到代理渲染目標,任何本地內容都應直接使用當前攝像機數據呈現到後台緩衝區,並跳過接下來的兩個步驟。
1. 應用程式現在應結合代理的呈現目標和呼叫`GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`。 這將將遠端顏色和深度資訊填充到代理渲染目標中。 現在可以使用代理攝像機轉換將任何本地內容呈現到代理上。
1. 接下來,後台緩衝區需要綁定為渲染目標,並`GraphicsBindingSimD3d11.ReprojectProxy`調用可以在此時顯示後台緩衝區。

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

* [教學:從頭開始設定 Unity 專案](../tutorials/unity/project-setup.md)
