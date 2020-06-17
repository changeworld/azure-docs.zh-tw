---
title: 從頭開始設定 Unity 專案
description: 說明如何設定空白的 Unity 專案，以便搭配 Azure 遠端轉譯使用。
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 7901f12763cb97fa76c0908e76755247ae934a20
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300584"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>教學課程：從頭開始設定 Unity 專案

在本教學課程中，您將了解：

> [!div class="checklist"]
>
> * 設定 ARR 的臨時 Unity 專案。
> * 建立和停止轉譯工作階段。
> * 重複使用現有的工作階段。
> * 與工作階段連線和中斷連線。
> * 將模型載入轉譯工作階段中。
> * 顯示連線統計資料。

## <a name="prerequisites"></a>必要條件

在本教學課程中，您需要：

* 您的帳戶資訊 (帳戶識別碼、帳戶金鑰、訂用帳戶識別碼)。 如果您沒有帳戶，請[建立帳戶](../../how-tos/create-an-account.md)。
* Windows SDK 10.0.18362.0 [(下載)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* 最新版的 Visual Studio 2019 [(下載)](https://visualstudio.microsoft.com/vs/older-downloads/)。 
* [適用於混合實境的 Visual Studio 工具](https://docs.microsoft.com/windows/mixed-reality/install-the-tools)。 具體而言，必須要安裝下列*工作負載*：
  * **具備 C++ 的桌面開發**
  * **通用 Windows 平台 (UWP) 開發**
* GIT [(下載)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(下載)](https://unity3d.com/get-unity/download)
  * 在 Unity 中安裝下列模組：
    * **UWP** - 通用 Windows 平台建置支援
    * **IL2CPP** - Windows 建置支援 (IL2CPP)

> [!TIP]
> [ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)包含適用於所有教學課程的預備 Unity 專案。 您可使用這些專案作為參考。

## <a name="create-a-new-unity-project"></a>建立新的 Unity 專案

從 Unity Hub 建立新的專案。
在此範例中，我們假設專案正在名為 `RemoteRendering` 的資料夾中建立。

![[新增專案] 視窗](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>設定專案的資訊清單

您需要修改位於 Unity 專案資料夾中的 `Packages/manifest.json` 檔案。 在文字編輯器中開啟該檔案並附加下列幾行：

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
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

通用轉譯管線套件是選用項目，但基於效能理由，建議使用。
在您修改並儲存資訊清單之後，Unity 會自動重新整理。 確認已在 [專案] 視窗中載入此套件：

![確認套件匯入](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>確定您有最新版的套件。

下列步驟可確保您的專案使用最新版的遠端轉譯套件。
1. 在 [專案] 視窗中選取套件，然後按一下 :::no-loc text="package"::: 圖示：![選取套件圖示](media/package-icons.png)
1. 在偵測器中，按一下 [在套件管理員中檢視]：![套件偵測器](media/package-properties.png)
1. 在遠端轉譯套件的套件管理員頁面中，查看更新按鈕是否可用。 如果可用，按一下該按鈕會將套件更新為最新可用的版本：![套件管理員中的 ARR 套件](media/package-manager.png)
1. 有時候更新套件可能會導致主控台發生錯誤。 如果發生這種情況，請嘗試關閉並重新開啟專案。

## <a name="configure-the-camera"></a>設定相機

選取 [主要相機] 節點。

1. 重設其 [轉換]：

    ![重設相機轉換](media/camera-reset-transform.png)

1. 將 **:::no-loc text="Clear flags":::** 設定為 *:::no-loc text="Solid Color":::*

1. 將 **:::no-loc text="Background":::** 設定為 *:::no-loc text="Black":::*

1. 將 **:::no-loc text="Clipping Planes":::** 設定為 *Near = 0.3* 和 *Far = 20*。 這表示轉譯會裁剪距離小於 30 公分或大於 20 公尺的幾何。

    ![Unity 相機屬性](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>調整專案設定

1. 開啟 [編輯] > [專案設定...]
1. 在左側的清單中選取 [品質]。
1. 將 [預設品質等級] 變更為 [低]

    ![變更專案品質設定](media/settings-quality.png)

1. 選取左側的 [圖形]。
1. 將 [可編寫指令碼的轉譯管線] 設定變更為 [HybridRenderingPipeline]。 如果未使用通用轉譯管線，請略過此步驟。

    ![變更專案圖形設定](media/settings-graphics-lwrp.png) 有時候 UI 並不會從套件填入可用的管線類型清單，在此情況下，必須手動將 *HybridRenderingPipeline* 資產拖曳到欄位上：![變更專案圖形設定](media/hybrid-rendering-pipeline.png)
1. 選取左側的 [播放器]。
1. 選取 [通用 Windows 平台設定] 索引標籤
1. 變更 [XR 設定] 以支援 Windows Mixed Reality：![播放器設定](media/xr-player-settings.png)
1. 選取上述螢幕擷取畫面中的設定：
    1. 啟用 [虛擬實境支援]
    1. 將 [深度格式] 設定為 [16 位元深度]
    1. 啟用 [深度緩衝區共用]
    1. 將 [立體聲轉譯模式] 設定為 [單程執行個體]

1. 在相同視窗的 [XR 設定] 之上，展開 [發佈設定]
1. 向下捲動至 [功能] 並選取：
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * 開發選用項目：**PrivateNetworkClientServer**

      如果您想要將 Unity 遠端偵錯工具連線到您的裝置，則需要此選項。

1. 在 [支援的裝置系列] 中，啟用 [全像攝影] 和 [桌面]

1. 如果您想要使用混合實境工具組，請參閱 [MRTK 文件](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)，以取得建議設定和功能的詳細資訊。

## <a name="validate-project-setup"></a>驗證專案設定

請執行下列步驟來驗證專案設定是否正確。

1. 從 Unity 編輯器工具列中的 RemoteRendering 功能表選擇 ValidateProject 專案。
1. 使用 ValidateProject 視窗，在必要時檢查並修正專案設定。

    ![Unity 編輯器專案驗證](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>建立指令碼以初始化 Azure 遠端轉譯

建立[新指令碼](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)並將其命名為 **RemoteRendering**。 開啟指令檔並以下列程式碼取代其整個內容：

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

此指令碼會初始化 Azure 遠端轉譯，告知哪個相機物件要用於轉譯，然後在啟動 [播放模式] 時，將 [建立工作階段] 按鈕放入檢視區。

> [!CAUTION]
> 當播放模式在 Unity 中為作用中時，修改指令碼並加以儲存可能會導致 Unity 凍結，而且您必須透過工作管理員將其關閉。 因此，在編輯 *RemoteRendering* 指令碼之前，一律停止播放模式。

## <a name="test-azure-remote-rendering-session-creation"></a>測試 Azure 遠端轉譯工作階段建立

在場景中建立新的 GameObject，並在其中新增 *RemoteRendering* 元件。 針對您的遠端轉譯帳戶，填入適當的 [帳戶網域]、[帳戶識別碼] 和 [帳戶金鑰]：

![遠端轉譯元件屬性](media/remote-rendering-component.png)

在編輯器中啟動應用程式 (**按下 [播放]** 或 CTRL+P)。 您應會看到 [建立工作階段] 按鈕出現在檢視區中。 按一下該按鈕以啟動第一個 ARR 工作階段：

![建立第一個工作階段](media/test-create.png)

如果失敗，請確定您已將您的帳戶詳細資料正確輸入到 RemoteRendering 元件屬性中。 否則，主控台視窗中會出現一則訊息，其中顯示指派給您的工作階段識別碼，並指出工作階段目前處於 [啟動中] 狀態：

![工作階段開始輸出](media/create-session-output.png)

此時，Azure 會為您佈建伺服器並啟動遠端轉譯虛擬機器。 這通常**需要 3 到 5 分鐘的時間**。 當 VM 準備就緒時，就會執行 Unity 指令碼的 `OnSessionStatusChanged` 回呼並將列出新的工作階段狀態：

![工作階段準備輸出](media/create-session-output-2.png)

就是這樣！ 目前不會發生任何事。 若要避免產生費用，您應該一律停止不再需要的工作階段。 在此範例中，您可按一下 [停止工作階段] 按鈕或停止 Unity 模擬來這麼做。 由於 *ARRServiceUnity* 元件的 [自動停止工作階段] 屬性 (預設為開啟)，系統將會為您自動停止工作階段。 如果由於當機或連線問題以致一切失敗，您的工作階段在由伺服器關閉以前，其執行時間長度可能為 *MaxLeaseTime*。

> [!NOTE]
> 停止工作階段將會立即生效且無法復原。 一旦停止，您就必須建立新的工作階段，其具有相同的啟動額外負荷。

## <a name="reusing-sessions"></a>重複使用工作階段

不幸的是，建立新的工作階段是耗時的作業。 因此，使用者應該很少嘗試建立工作階段，且盡可能加以重複使用。

將下列程式碼插入 *RemoteRendering* 指令碼中，並移除舊版的重複函式：

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> 執行此程式碼之前，請務必停用 RemoteRendering 元件中的 [自動停止工作階段] 選項。 否則，您建立的每個工作階段都會在您停止模擬時自動停止，而嘗試重複使用工作階段將會失敗。

當您按下 [播放] 時，您現在會在檢視區中取得三個按鈕：[建立工作階段]、[查詢作用中工作階段]，以及 [使用現有的工作階段]。 第一個按鈕一律會建立新的工作階段。 第二個按鈕會查詢有哪些「作用中」工作階段存在。 如果您未手動指定要嘗試使用的工作階段識別碼，此動作將會自動選取該工作階段識別碼，以供日後使用。 第三個按鈕會嘗試連線到現有的工作階段。 不是您透過 [工作階段識別碼] 元件屬性手動指定的工作階段，就是 [查詢作用中工作階段] 所找到的工作階段。

**AutoStartSessionAsync** 函式可用來模擬在編輯器之外按下的按鈕。

> [!TIP]
> 您可以開啟已停止、已過期或處於錯誤狀態的工作階段。 雖然其無法再用於轉譯，但您可在開啟非作用中工作階段之後，查詢其詳細資料。 上述程式碼會在 `ARRService_OnSessionStarted` 中檢查工作階段的狀態，以便在工作階段變成無法使用時自動停止。

透過此功能，您現在可建立並重複使用工作階段，這應該會大幅改善您的開發工作流程。

一般而言，建立工作階段會在用戶端應用程式外部觸發，因為啟動伺服器需要時間。

## <a name="connect-to-an-active-session"></a>連接到作用中工作階段

到目前為止，我們已建立或開啟數個工作階段。 下一步是「連線」到工作階段。 連線之後，轉譯伺服器將會產生影像並將影片串流傳送至我們的應用程式。

將下列程式碼插入 *RemoteRendering* 指令碼中，並移除舊版的重複函式：

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

若要測試這項功能：

1. 在 Unity 中按下 [播放]。
1. 開啟工作階段：
    1. 如果您已經有工作階段，請按 [查詢作用中工作階段]，然後按 [使用現有的工作階段]。
    1. 否則，請按 [建立工作階段]。
1. 按 [連線]。
1. 幾秒後，主控台輸出應該會列出您所連線的項目。
1. 目前不應發生任何其他情況。
1. 按 [中斷連線] 或停止 Unity 的播放模式。

>[!NOTE]
> 多位使用者可以「開啟」一個工作階段來查詢其資訊，但一次只能有一位使用者「連線」到一個工作階段。 如果另一位使用者已經連線，則連線會失敗並發生**交握錯誤**。

## <a name="load-a-model"></a>載入模型

將下列程式碼插入 *RemoteRendering* 指令碼中，並移除舊版的重複函式：

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

當您現在按下播放、開啟工作階段並加以連線時，[載入模型] 按鈕隨即出現。 按一下之後，主控台輸出將會顯示載入進度，而達到 100% 時，您應會看到引擎的模型出現：

![在編輯器中載入的模型](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) 是用於 [全像投影穩定性](https://docs.microsoft.com/windows/mixed-reality/hologram-stability)的重要元件。 不過，其只在部署於混合實境裝置時有效。

> [!TIP]
> 如果您遵循了[快速入門：轉換模型以進行轉譯](../../quickstarts/convert-model.md)，您就已經知道如何轉換自己的模型。 若要進行轉譯，您現在只需要將已轉換模型的 URI 放入 [模型名稱] 屬性中。

## <a name="display-frame-statistics"></a>顯示畫面統計資料

Azure 遠端轉譯會追蹤有關連線品質的各種資訊。 若要快速顯示這項資訊，請執行下列動作：

建立[新指令碼](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)並將其命名為 **RemoteFrameStats**。 開啟指令檔並以下列程式碼取代其整個內容：

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

建立 GameObject 並將其命名為 *FrameStats*。 將其當作子節點附加至 [主要相機] 物件，並將其位置設定為 **x = 0、y = 0、z = 0.325**。 將 **RemoteFrameStats** 元件新增至物件。

將 [UI] > [畫布] 子物件新增至 *FrameStats* 物件並設定其屬性，如下所示：

![畫布屬性](media/framestats-canvas.png)

將 [UI] > [文字] 物件新增為畫布的子系並設定其屬性，如下所示：

![文字屬性](media/framestats-text.png)

選取 [FrameStats] 物件，然後在 **FrameStats 欄位**中填入資料，做法是按一下圓形圖示，然後選取 [Text] 物件：

![設定文字屬性](media/framestats-set-text.png)

現已連線到遠端工作階段，文字應該會顯示串流統計資料：

![畫面統計資料輸出](media/framestats-output.png)

程式碼會停用編輯器外部的統計資料更新，因為標頭鎖定的文字方塊會造成干擾。 在[快速入門](../../quickstarts/render-model.md)專案中可找到更詳盡的實作方式。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解取得空白 Unity 專案所需的所有步驟，並且讓該專案能搭配 Azure 遠端轉譯運作。 在下一個教學課程中，我們將進一步探討如何使用遠端實體。

> [!div class="nextstepaction"]
> [教學課程：在 Unity 中使用遠端實體](working-with-remote-entities.md)
