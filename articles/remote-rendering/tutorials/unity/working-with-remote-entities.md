---
title: 在 Unity 中使用遠端實體
description: 說明如何使用 ARR 實體的教學課程。
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 5d995e9a5cdb6fc18532e0c3533959e9feece908
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021240"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>教學課程：在 Unity 中使用遠端實體

[教學課程：從頭開始設定 Unity 專案](project-setup.md)說明了如何設定新的 Unity 專案，以搭配 Azure 遠端轉譯運作。 在本教學課程中，我們將探討每個 ARR 使用者所需的最常見功能。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 使用光線投射來挑選物件。
> * 覆寫物件狀態，例如濃淡色彩、選取狀態和可見性。
> * 刪除遠端實體。
> * 將遠端實體四處移動。
> * 使用切割平面來查看物件內部。

## <a name="prerequisites"></a>必要條件

* 本教學課程的基礎是[教學課程：從頭開始設定 Unity 專案](project-setup.md)。

> [!TIP]
> [ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)包含 *Unity* 資料夾中所有教學課程的預備 Unity 專案，可供您作為參考。

## <a name="pick-objects"></a>挑選物件

我們想要與物件互動，因此我們所需的第一件事就是利用滑鼠游標挑選物件。

建立名為 **RemoteRaycaster** 的[新指令碼](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)，並以下列程式碼取代其整個內容：

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

將此元件新增至場景中的 *RemoteRendering* 物件。

> [!WARNING]
>
> *RemoteRaycaster* 元件需要將 *ARRServiceUnity* 元件附加至相同的物件。 *ARRServiceUnity* 是協助程式類別，可讓您更輕鬆地存取一些 ARR 功能。 不過，場景中只能有此元件的單一執行個體。 因此，請務必將所有需要 *ARRServiceUnity* 的元件新增至相同的 GameObject。
> 如果您想要從多個遊戲物件存取 ARR 功能，請將 *ARRServiceUnity* 元件新增至其中一個物件並在其他指令碼中參考該物件，或直接存取 ARR 功能。

按下播放，連線至工作階段並載入模型。 現在指向場景中的物件並監看主控台輸出。 其應會列出滑鼠暫留的每個組件的物件名稱。

## <a name="highlight-objects"></a>醒目提示物件

在下一個步驟中，我們想要提供視覺化意見反應，這就是使用者所指向的模型組件。 為了達成此目的，我們會將 [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) 附加至我們所挑選的實體。 此元件可用來啟用或停用物件上的各種功能。 我們在此使用該元件來設定濃淡色彩並啟用[外框轉譯](../../overview/features/outlines.md)。

建立另一個名為 **RemoteModelEntity** 的指令檔，並以下列程式碼取代其內容：

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> 請不要將此指令碼指派給任何 Unity 遊戲物件，因為下列程式碼會以程式設計方式指派。

接下來，我們必須擴充 *RemoteRaycaster*，以將 *RemoteModelEntity* 元件新增至我們剛才挑選的物件。

將下列程式碼新增至 **RemoteRaycaster** 實作並移除重複的函式：

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

執行您的專案並指向模型，您應會看到其變成紅色調並出現白色選取外框。

## <a name="isolate-the-selected-object"></a>隔離選取的物件

[HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) 的另一個用途是覆寫可見性的功能。 這可讓您將選取的物件與模型的其餘部分隔離。 開啟 **RemoteModelEntity** 指令碼，新增下列程式碼，並移除重複的函式：

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

此程式碼依賴在階層中最上層物件擁有狀態覆寫元件，使所有物件都隱藏起來。 然後其會在選取的物件上再次覆寫可見性，讓該物件變為可見。 因此，我們需要在根物件建立狀態覆寫元件。

開啟 **RemoteRendering** 指令碼，並將下列程式碼插入於 *LoadModel* 函式的頂端：

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

最後，我們需要切換可見性的方法。 開啟 **RemoteRaycaster** 指令碼並取代 *Update* 函式：

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

執行程式碼並以滑鼠右鍵按一下模型的某個部分。 模型的其餘部分將會消失，且只有醒目提示的部分會保持可見狀態。

## <a name="remove-gameobject-instances-of-remote-entities"></a>移除遠端實體的 GameObject 執行個體

您可能已經注意到，程式碼會繼續建立物件，但絕不會將其清除。 這種情況也可在物件階層面板中看見。 當您在模擬期間展開遠端物件階層時，每次當您將滑鼠暫留在模型的新部分時，您可以看到越來越多的物件出現。

在場景中擁有許多物件會對效能造成負面影響。 您應該一律清除不再需要的物件。

將下列程式碼插入 **RemoteRaycaster** 指令碼中，並移除重複的函式：

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>移動物件

在下一個步驟中，我們想要四處移動選取的物件。 在 **RemoteRaycaster** 指令碼中，插入下列程式碼並移除重複的函式：

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> 如果您執行此程式碼，您會注意到未發生任何事。 這是因為基於效能理由，變更物件的轉換並不會自動將狀態變更同步至伺服器。 相反地，您必須手動將此狀態變更推送至伺服器，或在 *RemoteEntitySyncObject* 元件上啟用 **SyncEveryFrame**。

開啟 **RemoteModelEntity** 指令碼並新增下面這一行：

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

再次執行程式碼，您應該能夠在物件上按一下滑鼠左鍵並將其拖曳。

## <a name="add-a-cut-plane"></a>新增切割平面

我們想要在本教學課程中試用的最後一項功能，就是使用[切割平面](../../overview/features/cut-planes.md)。 切割平面會切開已轉譯物件的組件，讓您得以查看其內部。

在場景 **CutPlane** 中建立新的 GameObject。 建立新的指令碼並將其稱為 **RemoteCutPlane**。 將元件新增至新的 GameObject。

開啟指令檔並以下列程式碼取代其內容：

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

當您立即執行程式碼時，您應會看到平面將模型切開的方式。 您可以選取 *CutPlane* 物件，然後在 [場景] 視窗中進行移動並旋轉。 您可藉由停用切割平面物件來開啟和關閉切割平面。

## <a name="next-steps"></a>後續步驟

您現在知道與遠端物件互動時最重要的功能。 在下一個教學課程中，我們將探討如何自訂場景的外觀。

> [!div class="nextstepaction"]
> [教學課程：變更環境與材質](changing-environment-and-materials.md)
