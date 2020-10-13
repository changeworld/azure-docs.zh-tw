---
title: 檢視遠端轉譯模型
description: Azure 遠端轉譯的「Hello World」，教學課程會示範如何檢視 Azure 遠端轉譯的模型
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: bcee951dc85d9c317bad481ebdb91ff6c761371c
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653667"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>教學課程：檢視遠端轉譯模型

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 佈建 Azure 遠端轉譯 (ARR) 執行個體
> * 建立及停止轉譯工作階段
> * 重複使用現有的轉譯工作階段
> * 連線並中斷與工作階段的連線
> * 將模型載入轉譯工作階段中

## <a name="prerequisites"></a>必要條件

在本教學課程中，您需要：

* 有效的隨用隨付 Azure 訂用帳戶[建立帳戶](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(下載)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* 最新版的 Visual Studio 2019 [(下載)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(下載)](https://git-scm.com/downloads)
* Unity 為最新版本的 2019.3，建議您對此使用 Unity Hub [(下載)](https://unity3d.com/get-unity/download)
  * 在 Unity 中安裝下列模組：
    * **UWP** - 通用 Windows 平台建置支援
    * **IL2CPP** - Windows 建置支援 (IL2CPP)
* Unity 和 C# 語言的中繼知識 (例如：建立指令碼和物件、使用預製項目、設定 Unity 事件等)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>佈建 Azure 遠端轉譯 (ARR) 執行個體

若要取得 Azure 遠端轉譯服務的存取權，您必須先[建立帳戶](../../../how-tos/create-an-account.md#create-an-account)。

## <a name="create-a-new-unity-project"></a>建立新的 Unity 專案

> [!TIP]
> [ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)包含一個已完成所有教學課程的專案，可以用來做為參考。 在 Unity\Tutorial-Complete 中尋找完整的 Unity 專案。

從 Unity Hub 建立新的專案。
在此範例中，我們假設專案正在名為 **RemoteRendering** 的資料夾中建立。

:::image type="content" source="./media/unity-new-project.PNG" alt-text="新增 Unity 專案":::

## <a name="include-the-azure-remote-rendering-package"></a>包含 Azure 遠端轉譯套件

您需要修改位於 Unity 專案資料夾中的 `Packages/manifest.json` 檔案。 在文字編輯器中開啟檔案，並將以下幾行新增至您的資訊清單頂端：

```json
{
    "scopedRegistries": [
    {
        "name": "Azure Mixed Reality Services",
        "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
        "scopes": ["com.microsoft.azure"]
    }
    ],
    "dependencies": {
        "com.unity.render-pipelines.universal": "7.3.1",
        "com.microsoft.azure.remote-rendering": "0.1.31",
        ...existing dependencies...
    }
}
```

在您修改並儲存資訊清單之後，Unity 會自動重新整理。 確認已在 [專案] 視窗中載入此套件：

:::image type="content" source="./media/confirm-packages.png" alt-text="新增 Unity 專案":::

如果您的套件未載入，請檢查您的 Unity 主控台是否有錯誤。 如果您沒有任何錯誤，但仍未在 [套件] 資料夾底下看到任何套件，請核取 [套件可見度] 切換按鈕。\
![具有箭頭指向套件可見度切換按鈕的螢幕擷取畫面。](./media/unity-package-visibility.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>確定您有最新版的套件。

下列步驟可確保您的專案使用最新版的遠端轉譯套件。

1. 在 Unity 編輯器的頂端功能表中，開啟 [視窗] > [套件管理員]。
1. 選取 **Microsoft Azure 遠端轉譯**套件。
1. 在 **Microsoft Azure 遠端轉譯**套件的套件管理員頁面中，查看 [更新] 按鈕是否可用。 如果可用，按一下該按鈕會將套件更新為最新可用的版本：\
![套件管理員中的 ARR 套件](./media/package-manager.png)
1. 更新套件有時可能會導致主控台錯誤。 如果發生這種情況，請嘗試關閉並重新開啟專案。
1. 當套件為最新時，套件管理員會顯示 [最新]，而不是 [更新] 按鈕。\
![最新套件](./media/package-up-to-date.png)
## <a name="configure-the-camera"></a>設定相機

1. 選取 [主要相機] 節點。

1. 以滑鼠右鍵按一下 [轉換] 元件，然後選取 [重設] 選項，以開啟操作功能表：

    ![重設相機轉換](./media/camera-reset-transform.png)

1. 將 [清除旗標] 設定為 [單色]

1. 將**背景**設定為「黑色」(#000000)，且 alpha (A) 為完全透明 (0)

    ![色彩轉輪](./media/color-wheel-black.png)

1. 將 [裁剪平面] 設定為 [近 = 0.3] 和 [遠 = 20]。 這表示轉譯會裁剪距離小於 30 公分或大於 20 公尺的幾何。

    ![Unity 相機屬性](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>調整專案設定

1. 開啟 [編輯] > [專案設定...]
1. 從左側清單功能表中選取 [品質]
1. 將所有平台的 [預設品質等級] 變更為 [低]。 這項設定可讓您更有效率地轉譯本機內容，而不會影響遠端轉譯內容的品質。

    ![變更專案品質設定](./media/settings-quality.png)

1. 從左側清單功能表中選取 [圖形]
1. 將 [可編寫指令碼的轉譯管線] 設定變更為 [HybridRenderingPipeline]。\
    ![指出您在其中將可編寫指令碼的轉譯管線設定變更為 HybridRenderingPipeline 所在位置的螢幕擷取畫面。](./media/settings-graphics-render-pipeline.png)\
    有時候 UI 並不會從套件填入可用管線類型的清單。 如果發生這種情況，就必須手動將 HybridRenderingPipeline 資產拖曳到欄位：\
    ![變更專案圖形設定](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > 如果您無法將 HybridRenderingPipeline 資產拖放到 [轉譯管線資產] 欄位 (可能是因為欄位不存在！)，請確定您的套件設定包含 `com.unity.render-pipelines.universal` 套件。

1. 從左側清單功能表中選取 [播放器]
1. 選取 [通用 Windows 平台設定] 索引標籤 (以 Windows 圖示表示)。
1. 變更 [XR 設定] 以支援 Windows Mixed Reality，如下所示：
    1. 啟用 [虛擬實境支援]
    1. 按 [+] 按鈕並新增 **Windows Mixed Reality**
    1. 將 [深度格式] 設定為 [16 位元深度]
    1. 確定已啟用 [深度緩衝區共用]
    1. 將 [立體聲轉譯模式] 設定為 [單程執行個體]

    ![播放器設定](./media/xr-player-settings.png)

1. 在相同視窗的 [XR 設定] 之上，展開 [發佈設定]
1. 向下捲動至 [功能] 並選取：
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (選用)。 如果您想要將 Unity 遠端偵錯工具連線到您的裝置，請選取此選項。

1. 在 [支援的裝置系列] 底下，啟用 [全像攝影] 和 [桌面]
1. 關閉或停駐 [專案設定] 面板
1. 開啟 [檔案] > [組建設定]
1. 選取 [通用 Windows 平台]
1. 設定您的設定，使其符合下列所找到的設定
1. 按下 [切換平台] 按鈕。\
![組建設定](./media/build-settings.png)
1. 在 Unity 變更平台之後，請關閉 [組建] 面板。

## <a name="validate-project-setup"></a>驗證專案設定

請執行下列步驟來驗證專案設定是否正確。

1. 從 Unity 編輯器工具列中的 **RemoteRendering** 功能表選擇 **ValidateProject** 項目。
1. 檢閱 **ValidateProject** 視窗中的錯誤，並在必要時修正專案設定。

    ![Unity 編輯器專案驗證](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>建立指令碼以協調 Azure 遠端轉譯連線和狀態

有四個基本階段可以顯示遠端轉譯的模型，如以下流程圖中所概述。 每個階段都必須依序執行。 下一個步驟是建立一個指令碼，其會管理應用程式狀態，並繼續進行每個必要階段。

![ARR 堆疊 0](./media/remote-render-stack-0.png)

1. 在 [專案] 窗格的 [資產] 底下，建立名為 RemoteRenderingCore 的新資料夾。 然後在 RemoteRenderingCore 內，建立另一個名為「指令碼」的資料夾。

1. 建立名為 **RemoteRenderingCoordinator** 的[新 C# 指令碼](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)。
您的專案看起來應該像這樣：

    ![專案階層](./media/project-structure.png)

    此協調器指令碼會追蹤並管理遠端轉譯狀態。 請注意，此程式碼中的部分是用來維護狀態、將功能公開給其他元件、觸發事件，以及儲存非「直接」與 Azure 遠端轉譯相關的應用程式特定資料。 使用下列程式碼做為起點，我們將在稍後的教學課程中解決並實作特定的 Azure 遠端轉譯程式碼。

1. 在程式碼編輯器中開啟 **RemoteRenderingCoordinator**，並將其整個內容取代為下列程式碼：

```csharp
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // For most people '<region>' is either 'westus2' or 'westeurope'
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<AzureFrontendAccountInfo> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static AzureSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<AzureFrontendAccountInfo> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        var allSessions = await ARRSessionService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();
        return allSessions.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Actions.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Actions?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        var properties = await session.GetPropertiesAsync().AsTask();

        switch (properties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>建立 Azure 遠端轉譯 GameObject

遠端轉譯協調器及其必要的指令碼 (ARRServiceUnity) 都是 MonoBehaviours，必須附加到場景中的 GameObject。 ARR 會提供 ARRServiceUnity 指令碼，以公開用來連線及管理遠端工作階段大部分的 ARR 功能。

1. 在場景中建立新的 GameObject (Ctrl+Shift+N 或 GameObject -> 建立空的)，並將其命名為 **RemoteRenderingCoordinator**。
1. 將 RemoteRenderingCoordinator 指令碼新增至 **RemoteRenderingCoordinator** GameObject。\
![新增 RemoteRenderingCoordinator 元件](./media/add-coordinator-script.png)
1. 確認 ARRServiceUnity 指令碼 (在偵測器中顯示為「服務」) 會自動新增至 GameObject。 如果您想知道，這是 `[RequireComponent(typeof(ARRServiceUnity))]` 在 **RemoteRenderingCoordinator** 指令碼頂端的結果。
1. 將您的 Azure 遠端轉譯認證和帳戶網域新增至協調器指令碼：\
![新增您的認證](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>初始化 Azure 遠端轉譯

既然我們已經有協調器的架構，我們將會從**初始化遠端轉譯**開始，實作四個階段。

![ARR 堆疊 1](./media/remote-render-stack-1.png)

**Initialize** 會告訴 Azure 遠端轉譯要使用哪一個相機物件來轉譯，並將狀態機器進行到 **NotAuthorized**。 這表示其已初始化，但尚未授權連線到工作階段。 由於啟動 ARR 工作階段會產生成本，因此我們必須確認使用者想要繼續進行。

當輸入 **NotAuthorized** 狀態時，會呼叫 **CheckAuthorization**，其會叫用 **RequestingAuthorization** 事件，並決定要使用哪些帳號憑證 (**AccountInfo** 會在類別頂端附近定義，並使用您在上述步驟中透過 Unity Inspector 定義的認證)。

   > [!NOTE]
   > ARR 不支援執行階段重新編譯。 當播放模式為作用中時，修改指令碼並加以儲存可能會導致 Unity 凍結，且必須透過工作管理員將其強制關閉。 在編輯指令碼之前，請務必先確定您已停止播放模式。

1. 使用下列完整的程式碼來取代 **InitializeARR** 和 **InitializeSessionService** 的內容：

 ```csharp
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

為了從 **NotAuthorized** 進行到 **NoSession**，我們通常會向使用者呈現強制回應對話方塊，讓他們可以選擇 (我們會在另一章節中這麼做)。 現在，我們將會在觸發 **RequestingAuthorization** 事件時，立即呼叫 **ByPassAuthentication**，以自動略過授權檢查。

1. 選取 **RemoteRenderingCoordinator** GameObject，然後尋找在 **RemoteRenderingCoordinator** 元件的偵測器中公開的 **OnRequestingAuthorization** Unity 事件。

1. 按下右下方的 '+' 以新增新的事件。
1. 將元件拖曳至其本身的事件，以參考其本身。\
![略過驗證](./media/bypass-authorization-add-event.png)\
1. 在下拉式清單中選取 **RemoteRenderingCoordinator -> BypassAuthorization**。\
![顯示已選取 RemoteRenderingCoordinator.BypassAuthorization 選項的螢幕擷取畫面。](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>建立或加入遠端工作階段

第二個階段是 [建立] 或 [加入] 遠端轉譯工作階段 (如需詳細資訊，請參閱[遠端轉譯工作階段](../../../concepts/sessions.md))。

![ARR 堆疊 2](./media/remote-render-stack-2.png)

遠端工作階段是將轉譯模型的位置。 **JoinRemoteSession( )** 方法會嘗試加入現有的工作階段，並使用 **LastUsedSessionID** 屬性進行追蹤，或 **SessionIDOverride** 上是否已指派作用中的工作階段識別碼。 **SessionIDOverride** 僅適用於您的偵錯工具，只有在您知道工作階段存在且想要明確地連線時，才應該使用此方法。

如果沒有可用的工作階段，則會建立新的工作階段。 不過，建立新的工作階段是耗時的作業。 因此，您應該只在必要時才嘗試建立工作階段，並盡可能加以重複使用 (請參閱[商業就緒：工作階段集區、排程和最佳做法](../commercial-ready/commercial-ready.md#fast-startup-time-strategies)，以取得管理工作階段的詳細資訊)。

> [!TIP]
> **StopRemoteSession()** 將結束使用中的工作階段。 若要避免不必要的費用，您應該一律停止不再需要的工作階段。

狀態機器現在會進行到 **ConnectingToNewRemoteSession** 或 **ConnectingToExistingRemoteSession**，視可用的工作階段而定。 開啟現有的工作階段或建立新的工作階段都會觸發 **ARRSessionService.OnSessionStatusChanged** 事件，並執行我們的 **OnRemoteSessionStatusChanged** 方法。 在理想的情況下，這會導致狀態機器前進到 **RemoteSessionReady**。

1. 若要加入新的工作階段，請修改程式碼，以使用下列完整範例來取代 **JoinRemoteSession( )** 和 **StopRemoteSession( )** 方法：

```csharp
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationParams(renderingSessionVmSize, maxLeaseHours, maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

如果您想要重複使用工作階段來節省時間，請務必停用 *ARRServiceUnity* 元件中的 [自動停止工作階段] 選項。 請記住，這會讓工作階段保持執行狀態，即使沒有任何人連線也一樣。 您的工作階段在由伺服器關閉之前，可能會隨您的 MaxLeaseTime 持續執行，(MaxLeaseTime 的值可以在遠端轉譯協調器的 [新工作階段預設值] 底下進行修改)。 另一方面，如果您在中斷連線時自動關閉每個工作階段，則每次都必須等候新的工作階段啟動，這可能是有點冗長的程序。

> [!NOTE]
> 停止工作階段將會立即生效且無法復原。 一旦停止，您就必須建立新的工作階段，其具有相同的啟動額外負荷。

## <a name="connect-the-local-runtime-to-the-remote-session"></a>將本機執行階段連線到遠端工作階段

接下來，應用程式需要將其本機執行階段連線到遠端工作階段。

![ARR 堆疊 3](./media/remote-render-stack-3.png)

應用程式也需要接聽執行階段與目前工作階段之間連線的相關事件；這些狀態變更會在 **OnLocalRuntimeStatusChanged** 中進行處理。 這段程式碼會將我們的狀態推進到 **ConnectingToRuntime**。 一旦在 **OnLocalRuntimeStatusChanged** 中連線之後，狀態將會推進到 **RuntimeConnected**。 連線到執行階段是協調器自我考量的最後一個 狀態，這表示應用程式會使用所有一般設定來完成，並準備好開始載入和轉譯模型的工作階段特定工作。

 1. 使用下列已完成的版本取代 **ConnectRuntimeToRemoteSession( )** 和 **DisconnectRuntimeFromRemoteSession( )** 方法。
 1. 請務必記下 Unity 方法 **LateUpdate**，以及更新目前使用中的工作階段。 這可讓目前的工作階段傳送/接收訊息，並以從遠端工作階段接收到的框架來更新框架緩衝區。 ARR 正常運作很重要。

```csharp
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectToRuntime(new ConnectToRuntimeParams());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.DisconnectFromRuntime();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Actions.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Actions?.Update();
}
```

> [!NOTE]
> 將本機執行階段連線到遠端工作階段，需視目前使用中工作階段上呼叫的 [更新] 而定。 如果您發現您的應用程式從未進行超過 **ConnectingToRuntime** 狀態，請確定您在使用中的工作階段上定期呼叫 [更新]。

## <a name="load-a-model"></a>載入模型

有了所需的基礎之後，您就可以開始將模型載入遠端工作階段，並開始接收框架。

![此圖顯示準備載入和檢視模型的程序流程。](./media/remote-render-stack-4.png)

**LoadModel** 方法是設計用來接受模型路徑、進度處理常式和父系轉換。 這些引數會用來將模型載入遠端工作階段、更新載入進度上的使用者，以及根據父系轉換來調整遠端轉譯模型的方向。

1. 使用下列程式碼完全取代 **LoadModel** 方法：

    ```csharp
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        if(progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

上述程式碼會執行下列步驟：

1. 建立[遠端實體](../../../concepts/entities.md)。
1. 建立代表遠端實體的本機 GameObject。
1. 將本機 GameObject 設定為每個框架將其狀態同步 (即轉換) 至遠端實體。
1. 設定名稱並新增 [**WorldAnchor**](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) 以協助穩定。
1. 將模型資料從 Blob 儲存體載入遠端實體中。
1. 傳回父系實體，以供稍後參考。

## <a name="view-the-test-model"></a>檢視測試模型

現在，我們已擁有檢視遠端轉譯模型所需的所有程式碼，遠端轉譯的全部四個必要階段皆已完成。 現在，我們需要新增一些程式碼來啟動模型載入程序。

![ARR 堆疊 4](./media/remote-render-stack-5.png)

1. 將下列程式碼新增至 **RemoteRenderingCoordinator** 類別，其正下方的 **LoadModel** 方法是正常的：

    ```csharp
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    此程式碼會建立 GameObject，做為測試模型的父系。 然後其會呼叫 **LoadModel** 方法來載入「builtin://Engine」模型，這會內建於 Azure 遠端轉譯的資產，用於測試轉譯。

1. 儲存您的程式碼。
1. 按 Unity 編輯器中的 [播放] 按鈕，開始連線至 Azure 遠端轉譯並建立新工作階段的程序。
1. 您不會在 [遊戲] 視圖中看到太多，不過，[主控台] 會顯示應用程變更的式狀態。 這可能會進行到 `ConnectingToNewRemoteSession` 並且停留，最多可能需要五分鐘的時間。
1. 選取 **RemoteRenderingCoordinator** GameObject，以在偵測器中查看其附加的指令碼。 請在 [服務] 元件初始化和連線步驟進行時，監看其更新。
1. 監視 [主控台] 輸出 - 等候狀態變更為 **RuntimeConnected**。
1. 連線執行階段之後，以滑鼠右鍵按一下偵測器中的 [RemoteRenderingCoordinator]，以公開操作功能表。 然後，在操作功能表中，按一下由上述程式碼 `[ContextMenu("Load Test Model")]` 部分所新增的 [負載測試模型] 選項。

    ![從操作功能表載入](./media/load-test-model.png)

1. 監看主控台，找出我們傳遞至 **LoadModel** 方法的 **ProgressHandler** 輸出。
1. 請參閱遠端轉譯的模型！

> [!NOTE]
> 遠端模型永遠不會顯示在 [場景] 視圖中，只會出現在 [遊戲] 視圖中。 這是因為 ARR 會特別針對 [遊戲] 視圖相機的角度，從遠端轉譯框架，並不知道 [編輯器] 相機 (用來轉譯 [場景] 視圖)。

## <a name="next-steps"></a>後續步驟

![已載入模型](./media/test-model-rendered.png)

恭喜！ 您已建立基本應用程式，能夠使用 Azure 遠端轉譯來檢視遠端轉譯的模型。 在下一個教學課程中，我們將整合 MRTK 並匯入我們自己的模型。

> [!div class="nextstepaction"]
> [下一個步驟：介面和自訂模型](../custom-models/custom-models.md)
