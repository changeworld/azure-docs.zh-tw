---
title: 將遠端轉譯整合到 C++/DirectX11 全像攝影應用程式
description: 說明如何在使用 Visual Studio 專案精靈來建立一般 C++/DirectX11 全像攝影應用程式時，將遠端轉譯整合至其中
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 9db32912e86079875ad382fb23e521c720fc7fbd
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83775615"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>教學課程：將遠端轉譯整合到 Hololens 全像攝影應用程式

在本教學課程中，您將了解：

> [!div class="checklist"]
>
> * 使用 Visual Studio 建立可部署至 HoloLens 的全像攝影應用程式
> * 新增必要的程式碼片段和專案設定，以結合本機轉譯及遠端轉譯的內容

本教學課程著重於將必要位元新增至原生 `Holographic App` 範例，以結合本機轉譯與 Azure 遠端轉譯。 此應用程式中唯一的狀態回饋類型是透過 Visual Studio 內的 [偵錯工具輸出] 面板呈現，因此建議您從 Visual Studio 內部啟動範例。 新增適當的應用程式內回饋已超出此範例的範圍，因為從頭開始建立動態文字面板會牽涉到許多程式碼撰寫。 較好的起點是使用 `StatusDisplay` 類別，其屬於 [GitHub 上的遠端播放程式範例專案](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content)。 事實上，本教學課程的預設版本就是使用該類別的本機複本。

> [!TIP]
> [ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)包含本教學課程的結果，是一份可供使用的 Visual Studio 專案。 其中也會透過 UI 類別 `StatusDisplay` 回報適當錯誤和狀態來進行擴充。 在本教學課程中，所有 ARR 專屬的新增項目都在 `#ifdef USE_REMOTE_RENDERING` / `#endif` 的範圍內，因此可以輕鬆地識別新增的遠端轉譯。

## <a name="prerequisites"></a>Prerequisites

在本教學課程中，您需要：

* 您的帳戶資訊 (帳戶識別碼、帳戶金鑰、訂用帳戶識別碼)。 如果您沒有帳戶，請[建立帳戶](../../../how-tos/create-an-account.md)。
* Windows SDK 10.0.18362.0 [(下載)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)。
* 最新版的 Visual Studio 2019 [(下載)](https://visualstudio.microsoft.com/vs/older-downloads/)。
* 適用於 Visual Studio 的 Windows Mixed Reality 應用程式範本 [(下載)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX)。

## <a name="create-a-new-holographic-app-sample"></a>建立新的全像攝影應用程式範例

在第一個步驟中，我們會建立一個庫存樣本，以作為遠端轉譯整合的基礎。 開啟 Visual Studio 並選取 [建立新專案]，然後搜尋「全像 DirectX 11 應用程式 (通用 Windows)(C++/WinRT)」

![建立新專案](media/new-project-wizard.png)

輸入您選擇的專案名稱、選擇路徑，然後選取 [建立] 按鈕。
在新專案中，將設定切換為 [偵錯/ARM64]。 您現在應該能夠編譯專案並將其部署到連線的 HoloLens 2 裝置。 如果您在 HoloLens 上執行，您應該會在前方看到旋轉的立方體。

## <a name="add-remote-rendering-dependencies-through-nuget"></a>透過 NuGet 新增遠端轉譯相依性

新增遠端轉譯功能的第一個步驟是加入用戶端相依性。 相關相依性會以 NuGet 套件的形式提供。
在 [方案總管] 中，以滑鼠右鍵按一下專案，然後從捷徑功能表中選取 [管理 NuGet 套件...]。

在提示對話方塊中，瀏覽至名為 **"Microsoft.Azure.RemoteRendering.Cpp"** 的 NuGet 套件：

![瀏覽 NuGet 套件](media/add-nuget.png)

然後選取套件並按下 [安裝] 按鈕，將其新增至專案。

NuGet 套件會將遠端轉譯相依性新增至專案。 具體來說：
* 連結用戶端程式庫 (RemoteRenderingClient.lib)。
* 設定 .dll 相依性。
* 設定 include 目錄的正確路徑。

## <a name="project-preparation"></a>專案準備

我們需要對現有的專案進行一些小變更。 這些變更很小，但沒有這些變更的話，遠端轉譯無法正常運作。

### <a name="enable-multithread-protection-on-directx-device"></a>在 DirectX 裝置上啟用多執行緒保護
`DirectX11` 裝置必須啟用多執行緒保護。 若要變更此設定，請在 [Common] 資料夾中開啟 DeviceResources.cpp 檔案，然後在 `DeviceResources::CreateDeviceResources()` 函式的結尾處插入下列程式碼：

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>在應用程式資訊清單中啟用網路功能
已部署的應用程式必須明確啟用網路功能。 如果沒有設定此功能，連線查詢最後會超時。 若要啟用，請在方案瀏覽器中的 `package.appxmanifest` 項目上按兩下。 在下一個 UI 中，移至 [功能] 索引標籤，然後選取：
* 網際網路 (用戶端與伺服器)
* 網際網路 (用戶端)

![網路功能](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>整合遠端轉譯

專案準備就緒後，我們就可以開始進入程式碼的部分。 較好的應用程式進入點是使用 `HolographicAppMain` 類別 (HolographicAppMain.h/cpp 檔案)，因為其具有初始化、解除初始化和轉譯的所有必要連結。

### <a name="includes"></a>Includes

我們會先從加入必要的 include 項目開始。 將下列 include 新增至 HolographicAppMain.h 檔案：

```cpp
#include <AzureRemoteRendering.h>
```

...以及將此額外的 `include` 指示詞新增至 HolographicAppMain.cpp：

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
```

為了簡化程式碼，我們會在HolographicAppMain.h 檔案的頂端定義下列命名空間 (在 `include` 指示詞之後)：

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

此捷徑很實用，因此我們不需要在每一處寫出完整的命名空間，但仍然可以辨識 ARR 特有的資料結構。 當然，我們也可以使用 `using namespace...` 指示詞。

### <a name="remote-rendering-initialization"></a>遠端轉譯初始化
 
在應用程式的存留期內，我們需要保留幾個物件來用於工作階段等等。 存留期間與應用程式的 `HolographicAppMain` 物件存留期一致，因此我們將物件當做成員來新增至 `HolographicAppMain` 類別。 下一個步驟是在 HolographicAppMain.h 檔案中新增下列類別成員：

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

實際執行的最佳位置是 `HolographicAppMain` 類別的建構函式。 我們必須在其中執行三種初始化類型：
1. 一次性的遠端轉譯系統初始化
1. 前端建立
1. 工作階段建立

我們會在建構函式中依序執行所有動作。 不過，在實際使用案例中，建議您分別執行這些步驟。

在 HolographicAppMain.cpp 檔案中，將下列程式碼新增至建構函式主體的開頭：

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        memset(&clientInit, 0, sizeof(RR::RemoteRenderingInitialization));
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        RR::StartupRemoteRendering(clientInit);
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        memset(&init, 0, sizeof(RR::AzureFrontendAccountInfo));
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // if there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        auto SetNewSession = [this](RR::ApiHandle<RR::AzureSession> newSession)
        {
            SetNewState(AppConnectionStatus::Connecting, RR::Result::Success);
            m_session = newSession;
            m_api = m_session->Actions();
            m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
            m_session->ConnectionStatusChanged([this](auto status, auto error)
                {
                    OnConnectionStatusChanged(status, error);
                });

            // The following is async, but we'll get notifications via OnConnectionStatusChanged
            RR::ConnectToRuntimeParams init;
            init.mode = RR::ServiceRenderMode::Default;
            m_session->ConnectToRuntime(init);
        };

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // Create a new session
            RR::RenderingSessionCreationParams init;
            memset(&init, 0, sizeof(RR::RenderingSessionCreationParams));
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->Result())
                    {
                        SetNewSession(*handler->Result());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, RR::Result::Fail);
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, RR::Result::Success);
        }
    }

    // Rest of constructor code:
    ...
}
```
在 `SetNewSession` 區域函式中，我們會註冊回呼，每當指定工作階段的連線狀態變更時，就會觸發此回呼。 我們會將該呼叫重新導向至我們自己的 `OnConnectionStatusChanged` 函式。 我們將在下一段中宣告並執行此函式 (以及狀態機器程式碼的其餘部分)。 另請注意，範例中的認證是硬式編碼，而且必須確實填寫 ([帳戶識別碼、帳戶金鑰](../../../how-tos/create-an-account.md#retrieve-the-account-information)和[網域](../../../reference/regions.md))。

我們會在解構函式主體的結尾，以對稱方式及反過來的順序進行解除初始化：

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>狀態機器

在遠端轉譯中，用於建立工作階段和載入模型的主要函式是非同步函式。 為此，我們需要一個簡單的狀態機器，其基本上會自動透過下列狀態轉換：

*初始化-> 工作階段建立-> 模型載入 (含進度)*

因此，在下一個步驟中，我們會將一些狀態機器處理新增至類別。 我們會針對應用程式可具有的各種不同狀態，宣告自己的 `AppConnectionStatus` 列舉。 這類似於 `RR::ConnectionStatus`，但具有其他的連線失敗狀態。

將下列成員和函式新增至類別宣告：

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, RR::Result error);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;

    }
```

在 .cpp 檔案的實作端，新增下列函式主體：

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = *async->Status();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, RR::Result error)
{
    m_currentStatus = state;
    m_connectionResult = error;

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, RR::ResultToString(error));
    OutputDebugStringA(buffer);
}


void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, error);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;

        // start model loading as soon as we have a valid connection
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
}
```

### <a name="per-frame-update"></a>個別畫面格更新

我們必須針對每個模擬計時 (tick)，對用戶端計時一次。 `HolographicApp1Main` 類別會針對個別畫面格更新提供良好的連結，因此，請將下列程式碼新增至函式 `HolographicApp1Main::Update` 的主體：

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    // Tick Remote rendering:
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();
    }

    // Rest of the body:
    ...
}
```

### <a name="rendering"></a>轉譯

最後一件事就是叫用遠端內容的轉譯。 在轉譯目標清楚之後，我們必須在轉譯管線中的正確位置執行此呼叫。 將下列程式碼片段插入 `HolographicAppMain::Render` 函式內部的 `UseHolographicCameraResources` 鎖定中：

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);

        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>執行範例

此範例現在應處於進行編譯和執行的狀態。

當範例正確執行時，您的前方就顯示旋轉的立方體，而在某些工作階段建立和模型載入之後，其會轉譯位於目前標頭位置的引擎模型。 工作階段建立和模型載入可能需要幾分鐘的時間。 目前的狀態只會寫入 Visual Studio 的輸出面板。 因此，建議您從 Visual Studio 內啟動範例。

> [!CAUTION]
> 當計時 (tick) 函式未進行呼叫達數秒時，用戶端會中斷與伺服器的連線。 因此，觸發中斷點很容易就會導致應用程式中斷連線。

如需使用文字面板顯示適當的狀態，請參閱本教學課程在 GitHub 上的預設版本。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解將遠端轉譯新增至庫存**全像攝影應用程式** C++/DirectX11 範例的所有必要步驟。
若要轉換您自己的模型，請參閱下列快速入門：

> [!div class="nextstepaction"]
> [快速入門：轉換模型以進行轉譯](../../../quickstarts/convert-model.md)
