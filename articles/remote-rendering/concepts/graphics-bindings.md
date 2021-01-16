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
ms.custom: devx-track-csharp
ms.openlocfilehash: cefd00609062c30b036f87a0a01a75dc2afb868b
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246140"
---
# <a name="graphics-binding"></a>圖形繫結

若要能在自訂應用程式中使用 Azure 遠端轉譯，就必須將其整合到應用程式的轉譯管線中。 這項整合作業屬於圖形繫結的範疇。

設定完成後，圖形繫結會將存取權授予會影響轉譯影像的不同函式。 這類函式可以分成兩種類別：隨時可供使用的一般函式，以及只與所選 `Microsoft.Azure.RemoteRendering.GraphicsApiType` 相關的特定函式。

## <a name="graphics-binding-in-unity"></a>Unity 中的圖形繫結

在 Unity 中，整個繫結程序是由傳遞至 `RemoteManagerUnity.InitializeManager` 的 `RemoteUnityClientInit` 結構處理。 若要設定圖形模式，`GraphicsApiType` 欄位必須設定為所選的繫結。 系統會依據 XRDevice 是否存在而自動填入欄位。 您可以使用下列行為來手動覆寫行為：

* **HoloLens 2**：一律使用 [Windows Mixed Reality](#windows-mixed-reality) 圖形繫結。
* **平面 UWP 傳統型應用程式**：一律使用 [模擬](#simulation)。
* **Unity 編輯器**：除非連接 WMR VR 頭戴式裝置，否則一律使用 [模擬](#simulation)。在前述情況下會停用 ARR，以允許對應用程式的非 ARR 相關部分進行偵錯。 並請參閱[全像攝影遠端處理](../how-tos/unity/holographic-remoting.md)。

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

在每個畫面格的開頭，必須將遠端框架轉譯為背景緩衝區。 這是藉由呼叫來完成 `BlitRemoteFrame` ，這會在目前系結的轉譯目標中填滿兩個眼睛的色彩和深度資訊。 因此，在將完整背景緩衝區系結為轉譯目標之後，請務必這麼做。

> [!WARNING]
> 將遠端影像 array.blit 至背景緩衝區之後，應使用單一傳遞的身歷聲轉譯技術（例如使用 **SV_RenderTargetArrayIndex**）來轉譯本機內容。 使用其他身歷聲轉譯技術（例如在個別行程中轉譯每個眼睛），可能會導致重大的效能降低或圖形構件，因此應予以避免。

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

若要執行模擬系結，請務必瞭解本機相機和遠端框架之間的差異，如 [相機](../overview/features/camera.md) 頁面所述。

需要兩個相機：

* **本機相機**：此攝影機代表由應用程式邏輯驅動的目前相機位置。
* **Proxy 相機**：此攝影機符合伺服器傳送的目前 *遠端框架* 。 由於用戶端要求框架與其抵達之間有時間延遲，因此 *遠端框架* 一律是在本機相機的移動背後。

這裡的基本方法是，使用 proxy 攝影機將遠端影像和本機內容轉譯成非畫面的目標。 然後 proxy 映射會 reprojected 到本機相機空間中，並在 [reprojection 的晚期](../overview/features/late-stage-reprojection.md)解說中進一步說明。

`GraphicsApiType.SimD3D11` 也支援 stereoscopic 轉譯，在 `InitSimulation` 下面的安裝程式呼叫期間必須加以啟用。 這種設定方式較為複雜，運作方式如下：

#### <a name="create-proxy-render-target"></a>建立 Proxy 轉譯目標

必須使用 `GraphicsBindingSimD3d11.Update` 函式提供的 Proxy 相機資料，將遠端和本機內容轉譯成螢幕上不可見的色彩/深度轉譯目標，稱為「Proxy」。

Proxy 必須符合後端緩衝區的解析，而且必須是 *DXGI_FORMAT_R8G8B8A8_UNORM* 或 *DXGI_FORMAT_B8G8R8A8_UNORM* 格式的 int。 在 stereoscopic 轉譯的案例中，色彩 proxy 材質和（如果使用 depth），深度 proxy 材質必須有兩個數組圖層，而不是一個。 工作階段準備就緒後，需先呼叫 `GraphicsBindingSimD3d11.InitSimulation`，才能與其連線：

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
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
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.frameId != 0)
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

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.frameId != 0)
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

#### <a name="simulation-update-structures"></a>模擬更新結構

每個框架（從上一節的轉譯 **迴圈更新** ）都會要求您輸入對應至本機相機的一系列相機參數，並傳回一組對應至下一個可用框架相機的相機參數。 這兩個集合會分別在 `SimulationUpdateParameters` 和結構中捕獲 `SimulationUpdateResult` ：

```cs
public struct SimulationUpdateParameters
{
    public UInt32 frameId;
    public StereoMatrix4x4 viewTransform;
    public StereoCameraFOV fieldOfView;
};

public struct SimulationUpdateResult
{
    public UInt32 frameId;
    public float nearPlaneDistance;
    public float farPlaneDistance;
    public StereoMatrix4x4 viewTransform;
    public StereoCameraFOV fieldOfView;
};
```

結構成員具有下列意義：

| member | 描述 |
|--------|-------------|
| frameId | 連續框架識別碼。 SimulationUpdateParameters 輸入的必要項，而且必須針對每個新的框架持續遞增。 如果沒有可用的框架資料，SimulationUpdateResult 中的會是0。 |
| viewTransform | 框架的相機視圖轉換矩陣的左右-身歷聲配對。 針對 monoscopic 轉譯，只有 `left` 成員有效。 |
| fieldOfView | 在 [視圖慣例](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles)的 [OpenXR] 欄位中，以左至左的框架相機配對。 針對 monoscopic 轉譯，只有 `left` 成員有效。 |
| System.windows.media.media3d.projectioncamera.nearplanedistance | 用於目前遠端框架投射矩陣的近平面距離。 |
| System.windows.media.media3d.projectioncamera.farplanedistance | 目前遠端框架的投射矩陣所用的平面距離。 |

如果啟用了 stereoscopic 轉譯，則身歷聲配對 `viewTransform` 和 `fieldOfView` 允許設定這兩種攝影機值。 否則， `right` 將會忽略成員。 如您所見，在沒有指定投射矩陣的情況下，只有相機的轉換會以純4x4 轉換矩陣的形式傳遞。 實際的矩陣是在內部使用指定的 CameraSettings，以及在 [API](../overview/features/camera.md)上設定的目前最接近平面和最上層平面 Azure 遠端轉譯來計算。

由於您可以視需要在執行時間變更 [CameraSettings](../overview/features/camera.md) 的近乎平面和最接近的平面，而服務會以非同步方式套用這些設定，因此每個 SimulationUpdateResult 也會在轉譯對應的框架期間，攜帶特定的近平面和最遠的平面。 您可以使用這些平面值來調整用來轉譯本機物件的投射矩陣，以符合遠端畫面格呈現。

最後，雖然 **模擬更新** 呼叫需要 OpenXR 慣例中的欄位，但基於標準化和演算法的安全考慮，您可以利用下列結構擴展範例中所示的轉換函式：

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(UInt32 frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.frameId = frameId;
    parameters.viewTransform.left = viewTransform;
    if(parameters.fieldOfView.left.fromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        return null;
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out UInt32 frameId)
{
    if(result.frameId == 0)
    {
        // Invalid frame data
        return;
    }
    
    // Use the screenspace depth convention you expect for your projection matrix locally
    if(result.fov.left.toProjectionMatrix(result.nearPlaneDistance, result.farPlaneDistance, DepthConvention.ZeroToOne, projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.viewTransform.left;
    frameId = result.frameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.frameId = frameId;
    parameters.viewTransform.left = viewTransform;
    if(FovFromProjectionMatrix(projectionMatrix, parameters.fieldOfView.left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if(result.frameId == 0)
    {
        // Invalid frame data
        return;
    }
    
    // Use the screenspace depth convention you expect for your projection matrix locally
    if(FovToProjectionMatrix(result.fieldOfView.left, result.nearPlaneDistance, result.farPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.viewTransform.left;
    frameId = result.frameId;
}
```

這些轉換函式可讓您快速切換現場規格和一般4x4 透視圖投影矩陣，視您對本機轉譯的需求而定。 這些轉換函式包含驗證邏輯，而且會傳回錯誤，而不會設定有效的結果，以防輸入投射矩陣或輸入欄位無效。

## <a name="api-documentation"></a>API 文件

* [C # RemoteManagerStatic. StartupRemoteRendering ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [C # GraphicsBinding 類別](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C # GraphicsBindingWmrD3d11 類別](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C # GraphicsBindingSimD3d11 類別](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [C + + RemoteRenderingInitialization 結構](/cpp/api/remote-rendering/remoterenderinginitialization)
* [C + + GraphicsBinding 類別](/cpp/api/remote-rendering/graphicsbinding)
* [C + + GraphicsBindingWmrD3d11 類別](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [C + + GraphicsBindingSimD3d11 類別](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>後續步驟

* [相機](../overview/features/camera.md)
* [延遲階段重新投影](../overview/features/late-stage-reprojection.md)
* [教學課程：檢視遠端轉譯模型](../tutorials/unity/view-remote-models/view-remote-models.md)