---
title: 操作模型
description: 藉由移動、旋轉調整等方式操作遠端轉譯模型
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 07b5b81dd7e23f25e7bfba90bbab7083090724d4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018853"
---
# <a name="tutorial-manipulating-models"></a>教學課程：操作模型

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 在遠端轉譯模型周圍新增視覺和操作界限
> * 移動、旋轉和調整
> * 具有空間查詢的 Raycast
> * 為遠端轉譯物件新增簡單的動畫

## <a name="prerequisites"></a>必要條件

* 本教學課程的建置基礎為[教學課程：介面和自訂模型](../custom-models/custom-models.md)。

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>查詢遠端物件界限並套用至本機界限

若要與遠端物件互動，我們需要先與本機標記法互動。 [物件界限](../../../concepts/object-bounds.md)有助於快速操作遠端物件。 您可以使用本機實體作為參考，從 ARR 查詢遠端界限。 在模型載入遠端工作階段後，系統會查詢界限。

模型的界限是由包含整個模型的方塊所定義 - 就像 Unity 的 [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html)，其具有為 x、y、Z 軸定義的中心和大小。 事實上，我們將使用 Unity 的 **BoxCollider** 來代表遠端模型的界限。

1. 在與 **RemoteRenderedModel** 相同的目錄中建立新的指令碼，並命名為 **RemoteBounds**。
1. 將指令碼的內容取代為下列程式碼：

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private BoundsQueryAsync remoteBoundsQuery = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create a query using the model entity
        private void QueryBounds()
        {
            //Implement me
        }

        // Check the result and apply it to the local Unity bounding box if it was successful
        private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > 如果您在 Visual Studio 中看到「功能 X 無法在 C#6 中使用。請使用語言版本 7.0 或更高版本，您可以放心地忽略這些錯誤。 這與 Unity 的解決方案和專案產生有關。

    應該將此指令碼新增至與實作 **BaseRemoteRenderedModel** 之指令碼相同的 GameObject。 在本教學課程中，也就是 **RemoteRenderedModel**。 類似於先前的指令碼，此初始程式碼將會處理與遠端界限相關的所有狀態變更、事件和資料。

    有兩種方法可供您實作：**QueryBounds** 和 **ProcessQueryResult**。 **QueryBounds** 會提取界限，而 **ProcessQueryResult** 會取得查詢的結果，並將其套用至本機 **BoxCollider**。

    **QueryBounds** 是很直覺式的方法：將查詢傳送至遠端轉譯工作階段，並接聽 `Completed` 事件。

1. 使用下列已完成的方法來取代 **QueryBounds** 方法：

    ```csharp
    // Create a query using the model entity
    private void QueryBounds()
    {
        remoteBoundsQuery = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        remoteBoundsQuery.Completed += ProcessQueryResult;
    }
    ```

    **ProcessQueryResult** 也很直覺式。 我們會檢查結果，以查看是否成功。 如果是，會以 **BoxCollider** 可接受的格式，轉換並套用傳回的界限。    

1. 使用下列已完成的方法來取代 **ProcessQueryResult** 方法：

    ```csharp
    // Check the result and apply it to the local Unity bounding box if it was successful
    private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
    {
        if (remoteBounds.IsRanToCompletion)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

現在，當 **RemoteBounds** 指令碼新增至與 **RemoteRenderedModel** 相同的遊戲物件時，將會在需要時新增 **BoxCollider**；而當模型達到其 `Loaded` 狀態時，將會自動查詢界限並套用至 **BoxCollider**。

1. 使用先前建立的 **TestModel** GameObject，新增 **RemoteBounds** 元件。
1. 確認已加入指令碼。

     ![新增 RemoteBounds 元件](./media/remote-bounds-script.png)

1. 再次執行應用程式。 在模型載入之後，您很快會看到遠端物件的界限。 您會看到類似下列的值：

     ![界限已更新](./media/updated-bounds.png)

現在，我們已在 Unity 物件上使用正確的界限設定本機 **BoxCollider**。 界限允許視覺效果和互動使用我們在本機轉譯物件中使用的相同策略。 例如，改變轉換、物理等等之後的指令碼。

## <a name="move-rotate-and-scale"></a>移動、旋轉和調整  

移動、旋轉和調整遠端轉譯物件的方式，與任何其他 Unity 物件的運作方式相同。 **RemoteRenderingCoordinator**在其 `LateUpdate` 方法中，會在目前使用中的工作階段呼叫 `Update`。 `Update` 所執行的工作，就是同步處理本機模型實體轉換與其遠端對應專案。 若要移動、旋轉或調整遠端轉譯模型，您只需要移動、旋轉或縮放代表遠端模型之 GameObject 的轉換。 在這裡，我們將修改已附加 **RemoteRenderedModel** 指令碼之父 GameObject 的轉換。

本教學課程使用 MRTK 來進行物件互動。 大部分用於移動、旋轉和調整物件的 MRTK 特定實作，都不在本教學課程的討論範圍內。 在 [模型工具] 功能表的 **AppMenu** 內，會預先設定模型檢視控制器。

1. 確定先前建立的 **TestModel** GameObject 已在場景中。
1. 確定 **AppMenu** Prefab 位於場景中。
1. 按下 Unity 的播放按鈕來播放場景，然後在 **AppMenu** 內開啟 [模型工具] 功能表。
![檢視控制器](./media/model-with-view-controller.png)

**AppMenu** 有一個子功能表**模型工具**，其會實作與模型繫結的檢視控制器。 當 GameObject 包含 **RemoteBounds** 元件時，檢視控制器將會新增 [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html)元件，這是 MRTK 元件，會在含有 **BoxCollider** 的物件周圍呈現周框方塊。 [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/api/Microsoft.MixedReality.Toolkit.Experimental.UI.ObjectManipulator.html?q=ObjectManipulator) 負責與手部互動。 這些指令碼會合併，讓我們能夠移動、旋轉和調整遠端轉譯模型。

1. 將滑鼠移至遊戲面板，並在其中按一下以對焦。
1. 使用 [MRTK 的手部模擬](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation)，按住左側 Shift 鍵。
1. 引導模擬手部，讓手部光線指向測試模型。

    ![手部指向光線](./media/handray-engine.png)

1. 按住滑鼠左鍵並拖曳模型來移動。

您應該會看到遠端轉譯內容與周框方塊一起移動。 您可能會發現在周框方塊和遠端內容之間出現一些延遲情況。 此延遲將取決於您的網際網路延遲和頻寬。

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>遠端模型的光線轉換和空間查詢

模型周圍的方塊碰撞程式適合用來與整個模型互動，但不足以精粗到能與模型的個別元件互動。 為了解決這個問題，我們可以使用[遠端光線轉換](../../../overview/features/spatial-queries.md#ray-casts)。 遠端光線轉換是 Azure 遠端轉譯提供的 API，可將光線轉換成遠端場景，並在本機傳回叫用結果。 這項技術可用來選取大型模型的子實體，或取得叫用結果資訊，例如位置、表面法線和距離。

測試模型有多個子實體可供查詢和選取。 目前，選取項目會將所選實體的名稱輸出至 Unity 主控台。 查看[材質、光源和效果](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining)章節，以了解如何將選取的實體反白顯示。

首先建立遠端光線轉換查詢周圍的靜態包裝函式。 此指令碼會接受 Unity 空間中的位置和方向，將其轉換成遠端光線轉換能接受的資料類型，然後傳回結果。 指令碼會利用 `RayCastQueryAsync` API。

1. 建立名為 **RemoteRaycaster** 的新指令碼，並以下列程式碼取代其內容：

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                return await RemoteRenderingCoordinator.CurrentSession.Actions.RayCastQueryAsync(rayCast).AsTask();
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Unity 具有名為 [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html) 的類別，而 Azure 遠端轉譯具有名為 [**RayCastHit**](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.raycasthit) 的類別。 大寫 **C** 是避免編譯錯誤的重要差異。

    **RemoteRayCaster** 提供一般存取點，可將遠端光線轉換成目前的工作階段。 更明確地說，我們接下來會實作 MRTK 指標控制代碼。 指令碼會實作 `IMixedRealityPointerHandler` 介面，並告訴 MRTK 希望此指令碼接聽[混合實境指標](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html)事件。

1. 建立名為 **RemoteRayCastPointerHandler** 的新指令碼，並以下列程式碼取代該程式碼：

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

**RemoteRayCastPointerHandler** 的 `OnPointerClicked` 方法會在碰撞器上「按一下」指標時呼叫，例如我們的方塊碰撞器。 之後，會呼叫 `PointerDataToRemoteRayCast`，將指標的結果轉換成點和方向。 接著會這個點和方向在遠端工作階段中轉換遠端光線。

![界限已更新](./media/raycast-local-remote.png)

按一下以傳送光線轉換的要求，這是查詢遠端物件的有效原則。 不過，這不是理想的使用者體驗，因為資料指標與方塊碰撞器衝突，而不是模型本身。

您也可以建立新的 MRTK 指標，以便更頻繁地在遠端工作階段中轉換光線。 雖然這個方法更加複雜，但能帶來較佳的使用者體驗。 此策略已超出本教學課程的範圍，但您可以在展示應用程式中看到這種方法的範例，位於 [ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/AzureRemoteRenderingShowcase)中。

當 **RemoteRayCastPointerHandler** 中的光線轉換成功完成時，會從 `OnRemoteEntityClicked` Unity 事件叫用 `Entity`。 為了回應該事件，我們將建立可接受 `Entity` 並對其執行動作的協助程式指令碼。 先從指令碼開始，透過指令碼將 `Entity` 的名稱列印到偵錯工具記錄檔。

1. 建立名為 **RemoteEntityHelper** 的新指令碼，並以下列內容取代其內容：

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. 在先前建立的 **TestModel** GameObject 上，新增 **RemoteRayCastPointerHandler** 元件和 **RemoteEntityHelper** 元件。
1. 將 `EntityToDebugLog` 方法指派給 `OnRemoteEntityClicked` 事件。 當事件的輸出類型和方法的輸入類型相符時，我們可以使用 Unity 的動態事件連結，這會自動將事件值傳遞至方法。
    1. 建立新的回呼欄位 ![新增回呼](./media/add-callback-remote-entity-clicked.png)
    1. 將**遠端實體協助程式**元件拖曳到 [物件] 欄位中，以參考父系 GameObject ![指派物件](./media/assign-object.png)
    1. 將 `EntityToDebugLog` 指派為回呼 ![指派回呼](./media/remote-entity-event.png)
1. 按下 Unity 編輯器中的播放以啟動場景，連線到遠端工作階段並載入測試模型。
1. 使用 MRTK 的手部模擬，按住左側 Shift 鍵。
1. 引導模擬手部，讓手部光線指向測試模型。
1. 長按以模擬空中點選，執行 `OnPointerClicked` 事件。
1. 觀察 Unity 主控台是否有已選取子實體名稱的記錄訊息。 例如：![子實體範例](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>將遠端物件圖形同步處理至 Unity 階層

到目前為止，我們只看到代表整個模型的單一本機 GameObject。 這很適合用於轉譯和操作整個模型。 不過，如果要套用效果或操作特定的子實體，必須建立本機 Gameobject 來代表這些實體。 首先可以在測試模型中手動探索。

1. 啟動場景並載入測試模型。
1. 展開 Unity 階層中 **TestModel** GameObject 的子系，然後選取 **TestModel_Entity** GameObject。
1. 在偵測器中，按一下「顯示子系」按鈕。
![顯示子系](./media/show-remote-children.png)
1. 繼續展開階層中的子系，然後按一下 [顯示子系] 直到顯示較大的子系列表為止。
![所有子系](./media/test-model-children.png)

數十個實體的清單現在會填入階層。 選取其中一個項目，就會在偵測器中顯示 `Transform` 和 `RemoteEntitySyncObject` 元件。 根據預設，每個實體都不會自動同步處理每個畫面，因此 `Transform` 的本機變更不會同步處理至伺服器。 您可以勾選「同步處理每個畫面」，然後在場景檢視中移動、調整或旋轉轉換，您不會在場景檢視中看到轉譯的模型，請觀看遊戲檢視以查看模型的位置和旋轉的視覺效果更新。

您可以用程式設計的方式來執行相同的程序，而且這是修改特定遠端實體的第一個步驟。

1. 修改 **RemoteEntityHelper** 指令碼，以同時包含下列方法：

    ```csharp
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. 將其他回呼新增至 **RemoteRayCastPointerHandler** 事件 `OnRemoteEntityClicked`，將其設定為 `MakeSyncedGameObject`。
![其他回呼](./media/additional-callback.png)
1. 使用 MRTK 的手部模擬，按住左側 Shift 鍵。
1. 引導模擬手部，讓手部光線指向測試模型。
1. 長按以模擬空中點選，執行 `OnPointerClicked` 事件。
1. 勾選並展開階層，以查看代表已點選之實體的新子物件。
![GameObject 表示法](./media/gameobject-representing-entity.png)
1. 測試之後，請移除 `MakeSyncedGameObject` 的回呼，因為我們會在稍後將此部分納入其他效果。

> [!NOTE]
> 只有當您需要同步處理轉換資料時，才需要同步處理每個畫面。 同步轉換會產生一些額外負荷，因此應該謹慎使用。

建立本機執行個體並自動同步處理是操作子實體的第一個步驟。 我們也可以在子實體上使用用來整體操作模型的相同技巧。 例如，建立實體的同步本機執行個體之後，您可以查詢其界限並新增操作控制代碼，以便讓其根據使用者手部的光線四處移動。

## <a name="next-steps"></a>後續步驟

您現在可以操作並與遠端轉譯的模型互動了！ 在下一個教學課程中，我們將討論如何修改材質、改變光線，以及將效果套用至遠端轉譯模型。

> [!div class="nextstepaction"]
> [下一個步驟：調整材質、光線和效果](../materials-lighting-effects/materials-lighting-effects.md)
