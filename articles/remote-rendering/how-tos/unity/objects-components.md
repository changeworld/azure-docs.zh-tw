---
title: Unity 遊戲物件和元件
description: 描述使用遠端轉譯實體和元件的 Unity 特定方法。
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 662c28196b06f5fbe49f69cb7145fdd33805e000
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019040"
---
# <a name="interact-with-unity-game-objects-and-components"></a>與 Unity 遊戲物件和元件互動

Azure 遠端轉譯 (ARR) 已針對大量物件進行優化 (查看) 的 [限制](../../reference/limits.md) 。 雖然可以在主機上管理大型且複雜的階層，但在低電源裝置上以 Unity 的方式複寫它們都是不可行。

因此，當模型載入至主機時，Azure 遠端轉譯會鏡像用戶端裝置上的模型結構相關資訊 (這會產生) 的網路流量，但不會複寫 Unity 中的物件和元件。 相反地，它會要求您手動要求所需的 Unity 遊戲物件和元件，讓您可以將額外負荷限制在真正需要的部分。 如此一來，您就可以更充分掌控用戶端效能。

因此，Azure 遠端轉譯的 Unity 整合會隨附額外的功能，以視需要複寫遠端轉譯結構。

## <a name="load-a-model-in-unity"></a>在 Unity 中載入模型

當您載入模型時，您會取得已載入模型之根物件的參考。 此參考不是 Unity 遊戲物件，但您可以使用擴充方法將它轉換成一個 `Entity.GetOrCreateGameObject()` 。 該函數需要類型的引數 `UnityCreationMode` 。 如果您通過 `CreateUnityComponents` ，新建立的 Unity 遊戲物件將會另外針對存在於主機上的所有遠端呈現元件，填入 proxy 元件。 不過，建議您最好將 `DoNotCreateUnityComponents` 額外負荷維持在最短。

### <a name="load-model-with-task"></a>具有工作的載入模型

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>使用 Unity 協同程式的負載模型

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>具有 await 模式的載入模型

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

上述程式碼範例透過 SAS 使用模型載入路徑，因為已載入內建模型。 透過 (使用和) 的 blob 容器來定址模型 `LoadModelAsync` ， `LoadModelParams` 可以完全類似。

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

建立 Unity 遊戲物件時，會以隱含方式將 `RemoteEntitySyncObject` 元件新增至遊戲物件。 此元件用來將實體轉換同步處理至伺服器。 依預設， `RemoteEntitySyncObject` 使用者必須明確呼叫 `SyncToRemote()` ，才能將本機 Unity 狀態同步處理到伺服器。 啟用 `SyncEveryFrame` 會自動同步處理物件。

具有的物件 `RemoteEntitySyncObject` 可以透過按鈕來具現化其遠端子系，並顯示在 Unity 編輯器中 **:::no-loc text="Show children":::** 。

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>包裝函式元件

附加至遠端轉譯實體的[元件](../../concepts/components.md)會透過 Proxy 向 Unity 公開 `MonoBehavior` 。 這些 proxy 代表 Unity 中的遠端元件，並將所有的修改轉寄至主機。

若要建立 proxy 遠端呈現元件，請使用擴充方法 `GetOrCreateArrComponent` ：

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>結合存留期

遠端 [實體](../../concepts/entities.md) 和 Unity 遊戲物件的存留期會在透過進行系結時結合 `RemoteEntitySyncObject` 。 如果您 `UnityEngine.Object.Destroy(...)` 使用這類遊戲物件來呼叫，則遠端實體也會一併移除。

若要損毀 Unity 遊戲物件，而不會影響遠端實體，您必須先 `Unbind()` 在上呼叫 `RemoteEntitySyncObject` 。

所有 proxy 元件也是如此。 若只要銷毀用戶端表示，您必須 `Unbind()` 先在 proxy 元件上呼叫：

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>後續步驟

* [設定 Unity 的遠端轉譯](unity-setup.md)
* [教學課程：操作 Unity 中的遠端實體](../../tutorials/unity/manipulate-models/manipulate-models.md)
