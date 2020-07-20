---
title: 介面和自訂模型
description: 新增要由 Azure 遠端轉譯呈現的檢視控制器和內嵌自訂模型
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: b08670c51b56f01ad1193d2729ecc77821242a19
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200740"
---
# <a name="tutorial-interfaces-and-custom-models"></a>教學課程：介面和自訂模型

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 將混合實境工具組新增至專案
> * 管理模型狀態
> * 設定 Azure Blob 儲存體以擷取模型
> * 上傳和處理用於轉譯的模型

## <a name="prerequisites"></a>必要條件

* 本教學課程的建置基礎為[教學課程：檢視遠端轉譯模型](../view-remote-models/view-remote-models.md)。

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>開始使用混合實境工具組 (MRTK)

混合實境工具組 (MRTK) 是一種跨平台的工具組，可用於建置混合實境體驗。 我們會使用 MRTK 2.3 打造互動和視覺效果功能。

若要新增 MRTK，請遵循[開始使用 MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html) 中所列的[必要步驟](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#required)。

這些步驟如下︰
 - [取得最新的 MRTK Unity 套件](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#get-the-latest-mrtk-unity-packages)
     - 雖然系統顯示為「最新」，但這適用於 2.3 版。
     - 我們只會在本教學課程中使用「基本」套件。 不需要用到「擴充功能」、「工具」和「範例」套件。
 - [將 MRTK 套件匯入到您的 Unity 專案](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#import-mrtk-packages-into-your-unity-project)
 - [將您的 Unity 專案切換到目標平台](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#switch-your-unity-project-to-the-target-platform)
     - 您應該已經在第一章完成這個步驟，但現在您也可以再次仔細確認！
 - [將 MRTK 新增至新場景或新專案](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#add-mrtk-to-a-new-scene-or-new-project)
     - 您可以將 MRTK 新增至新場景，並重新新增您的協調器和模型物件/指令碼，也可以使用「混合實境工具組 -> 新增至場景和設定」功能表命令將 MRTK 新增至現有的場景。

## <a name="import-assets-used-by-this-tutorial"></a>匯入本教學課程所使用的資產

從本章開始，我們會針對大部分涵蓋的內容實作簡單的 [model-view-controller pattern](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)。 模式的「模型」是 Azure 遠端轉譯的特定程式碼，以及與 Azure 遠端轉譯相關的狀態管理。 模式的「檢視」和「控制器」會使用 MRTK 資產和一些自訂指令碼來實作。 在此教學課程中，您可以使用「模型」，而不需要使用此處實作的 view-controller。 這種區隔可讓您輕鬆地將本教學課程中找到的程式碼整合到您自己的應用程式中，使其接管設計模式的 view-controller。

隨著 MRTK 的引進，現在您可在專案中新增一些指令碼、Prefabs 和資產，以支援互動和視覺效果意見反應。 這些稱為**教學課程資產**的資產已與 [Unity 資產套件](https://docs.unity3d.com/Manual/AssetPackages.html)配套，並包含在 '\Unity\TutorialAssets\TutorialAssets.unitypackage' 的 [Azure 遠端轉譯 GitHub](https://github.com/Azure/azure-remote-rendering) 中。

1. 如果下載時已將 zip 解壓縮至已知的位置，請將 GIT 存放庫複製或下載到 [Azure 遠端轉譯](https://github.com/Azure/azure-remote-rendering)。
1. 在您的 Unity 專案中，選擇「資產 -> 匯入套件 -> 自訂套件」。
1. 在檔案總管中，瀏覽至您複製或解壓縮 Azure 遠端轉譯存放庫的目錄，然後選取在**Unity -> TutorialAssets-> TutorialAssets.unitypackage** 中找到的 .unitypackage
1. 選取 [匯入] 按鈕，將套件內容匯入到您的專案中。
1. 在 Unity 編輯器中，從頂端功能表列中選取「混合實境 工具組 -> 公用程式 -> MRTK Standard Shader for Lightweight Render Pipeline」，並遵循提示來升級著色器。

一旦 MRTK 和教學課程資產均已納入專案，我們會將 MRTK 設定檔切換到更適合教學課程的設定檔。

1. 選取場景階層中的  **MixedRealityToolkit** GameObject。
1. 在偵測器的 **MixedRealityToolkit** 元件下，將設定設定檔切換為 ARRMixedRealityToolkitConfigurationProfile。
1. 按 Ctrl+S 儲存變更。

此步驟主要會以預設的 HoloLens 2 設定檔來設定 MRTK。 提供的設定檔會以下列方式預先設定：
 - 關閉分析工具 (按 9 加以開啟/關閉，或在裝置上說「顯示/隱藏分析工具」)。
 - 關閉眼睛資料指標。
 - 啟用 Unity 滑鼠點擊功能，讓您可以使用滑鼠 (而不是模擬的手) 來按一下 MRTK UI 元素。

## <a name="add-the-app-menu"></a>新增應用程式功能表

本教學課程中的大部分檢視控制器會針對抽象基底類別運作，而不是針對具體的類別運作。 此模式可提供更大的彈性，並可讓我們在提供檢視控制器的同時，仍可協助您了解 Azure 遠端轉譯程式碼。 為了簡化程序，**RemoteRenderingCoordinator** 類別並未提供抽象類別，而且其檢視控制器會直接針對具體類別作業。

您現在可以將 Prefab **AppMenu** 新增至場景，以取得目前工作階段狀態的視覺化意見反應。 在實作並將更多 ARR 功能整合到場景時，此檢視控制器會將更多的子功能表檢視控制器「解除鎖定」。 目前，**AppMenu** 會有 ARR 狀態的視覺指示，並呈現使用者用來授權應用程式連線到 ARR 的強制回應面板。

1. 找出 Assets/RemoteRenderingTutorial/Prefabs/AppMenu 中的 **AppMenu** Prefab
1. 將 **AppMenu** Prefab 拖曳到場景中。
1. 您可能會看到 **TMP 匯入工具**的對話方塊，因為這是我們第一次在場景中納入 Text Mesh Pro 資產。 依照提示**匯入 TMP Essentials**。 然後關閉匯入工具對話方塊，現在不需要範例和其他項目了。
1. **AppMenu** 設定為自動連結並提供同意連線至工作階段的強制回應，因此我們可以移除稍早所放置的略過。 在 **RemoteRenderingCoordinator** GameObject 中，按下**要求授權事件**的 [-] 按鈕，移除我們先前實作的略過授權。
 ![移除略過](./media/remove-bypass-event.png)。
1. 按下 Unity 編輯器中的 [播放]，測試檢視控制器。
1. 現在已在編輯器中設定 MRTK，您可以使用 WASD 鍵來變更檢視角度，並按住滑鼠右鍵然後移動滑鼠以變更您的檢視方向。 嘗試在場景周圍四處看看，以了解控制項的風格。
1. 在裝置上，您可以抬起手掌以啟動 **AppMenu**；在 Unity 編輯器中則請使用快速鍵 'M'。
1. 如果看不到功能表，請按 'M' 鍵以啟動功能表。 功能表會放在相機附近以方便互動。
1. 授權現在會在 **AppMenu** 右側顯示為要求；現在您可使用此要求來授權應用程式管理遠端轉譯工作階段。
 ![UI 授權](./media/authorize-request-ui.png)\
1. 停止 Unity 以繼續進行本教學課程。

## <a name="manage-model-state"></a>管理模型狀態

現在我們要實作新的指令碼 **RemoteRenderedModel**，此指令碼可以追蹤狀態、回應事件、引發事件和設定。 基本上，**RemoteRenderedModel** 會針對 `modelPath` 中的模型資料儲存遠端路徑。 其會接聽 **RemoteRenderingCoordinator** 中的狀態變更，以查看是否應該自動載入或卸載其定義的模型。 GameObject 已附加 **RemoteRenderedModel**，並且會是遠端內容的本機父系。

請注意，**RemoteRenderedModel** 指令碼會實作**教學課程資產**所包含的 **BaseRemoteRenderedModel**。 這可讓遠端模型檢視控制器與您的指令碼繫結。

1. 在與 **RemoteRenderingCoordinator** 相同的資料夾中，建立名為 **RemoteRenderedModel** 的新指令碼。 以下列程式碼取代整個內容：

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;

        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }
        public override string ModelPath { get => modelPath; set => modelPath = value; }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

最基本的情況下，**RemoteRenderedModel** 會保留載入模型所需的資料 (在本案例中為 SAS 或 builtin:// URI)，並追蹤遠端模型狀態。 載入時，會在 **RemoteRenderingCoordinator** 上呼叫 `LoadModel` 方法，並傳回包含模型的實體以供參考和卸載。

## <a name="load-the-test-model"></a>載入測試模型

讓我們再次載入測試模型來測試新的指令碼。 我們會建立包含指令碼的遊戲物件，並當作測試模型的父系。

1. 在場景中建立新的空白遊戲物件，並命名為 **TestModel**。
1. 將 *RemoteRenderedModel* 指令碼新增至 **TestModel**。
![新增 RemoteRenderedModel 元件](./media/add-remote-rendered-model-script.png)
1. 分別在 `Model Display Name` 和 `Model Path` 中填入 "TestModel" 和 "builtin://Engine"。
![指定模型詳細資料](./media/add-model-script.png)
1. 將 **TestModel** 物件放在相機前方，位置為 **x = 0，y = 0，z = 3**。
![放置物件](./media/test-model-position.png)
1. 確定已開啟 **AutomaticallyLoad**。
1. 按下 Unity 編輯器中的**播放**以測試應用程式。
1. 按一下「連線」按鈕以允許應用程式建立工作階段，其會連線至工作階段並自動載入模型以授與授權。

在應用程式經歷這些狀態時，注意觀看主控台。 請記住，某些狀態可能需要一些時間才能完成，且不會顯示進度。 最後，您會看到模型載入中的記錄，然後測試模型就會呈現在場景中。

請嘗試透過偵測器中的轉換或場景檢視，移動和旋轉 **TestModel** GameObject。 您會看到模型在遊戲檢視中移動並旋轉。

![Unity 記錄](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>在 Azure 中佈建 Blob 儲存體和自訂模型內嵌

現在，我們可以嘗試載入您自己的模型。 若要這麼做，您必須在 Azure 上設定 Blob 儲存體並上傳和轉換模型，然後使用 **RemoteRenderedModel** 指令碼載入模型。 如果您目前不需要載入自己的模型，可以放心地跳過自訂模型載入的步驟。

依照[快速入門：轉換模型以進行轉譯](../../../quickstarts/convert-model.md)中指定的步驟執行。 針對本教學課程的目的，跳過**將新模型插入快速入門範例應用程式**一節。 內嵌了模型的「共用存取簽章 (SAS)」 URI 之後，請繼續進行接下來的下一個步驟。

## <a name="load-and-rendering-a-custom-model"></a>載入和呈現自訂模型

1. 在場景中建立新的空白 GameObject，並以與自訂模型類似的名稱命名。
1. 將 RemoteRenderedModel 指令碼新增至新建立的 GameObject。
 ![新增 RemoteRenderedModel 元件](./media/add-remote-rendered-model-script.png)
1. 以適合模型的名稱填入 `Model Display Name`。
1. 使用您在上述內嵌步驟中建立的模型「共用存取簽章 (SAS)」 URI，填入 `Model Path`。
1. 將 GameObject 放在相機前方，位置為 **x = 0，y = 0，z = 3。**
1. 確定已開啟 **AutomaticallyLoad**。
1. 按下 Unity 編輯器中的**播放**以測試應用程式。

    您會看到主控台開始填入目前的狀態，最後出現模型載入進度訊息。 您的自訂模型接著會載入場景中。

1. 從場景中移除您的自訂模型物件。 使用測試模型能讓您享有本教學課程的最佳體驗。 雖然 ARR 一定支援多個模型，但本教學課程的內容是以一次支援單一遠端模型為目標而編寫而成。

## <a name="next-steps"></a>後續步驟

您現在可將自己的模型載入 Azure 遠端轉譯，並在您的應用程式中檢視！ 接下來，我們將引導您操作模型。

> [!div class="nextstepaction"]
> [下一個步驟：操作模型](../manipulate-models/manipulate-models.md)
