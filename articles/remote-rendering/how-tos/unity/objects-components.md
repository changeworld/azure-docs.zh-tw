---
title: Unity 遊戲物件和元件
description: 說明用來處理遠端呈現實體和元件的 Unity 特定方法。
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81409863"
---
# <a name="interact-with-unity-game-objects-and-components"></a>與 Unity 遊戲物件和元件互動

Azure 遠端呈現（ARR）已針對大量物件進行優化（請參閱[限制](../../reference/limits.md)）。 雖然您可以在主機上管理大型且複雜的階層，但在低電源裝置上的 Unity 中全部複寫都是不可行。

因此，在主機上載入模型時，Azure 遠端轉譯會反映用戶端裝置上有關模型結構的資訊（這會產生網路流量），但不會複寫 Unity 中的物件和元件。 相反地，它會要求您手動要求所需的 Unity 遊戲物件和元件，讓您可以限制實際所需的額外負荷。 如此一來，您就能更充分掌控用戶端的效能。

因此，Azure 遠端轉譯的 Unity 整合隨附了額外的功能，可在需要時複寫遠端轉譯結構。

## <a name="load-a-model-in-unity"></a>在 Unity 中載入模型

當您載入模型時，會取得已載入模型之根物件的參考。 此參考不是 Unity 遊戲物件，但您可以使用擴充方法`Entity.GetOrCreateGameObject()`將它轉換成一個。 該函數需要類型`UnityCreationMode`的引數。 如果您通過`CreateUnityComponents`，新建立的 Unity 遊戲物件也會針對存在於主機上的所有遠端呈現元件，另外填入 proxy 元件。 不過`DoNotCreateUnityComponents`，建議您最好將額外負荷降到最低。

### <a name="load-model-with-task"></a>使用工作載入模型

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

### <a name="load-model-with-unity-coroutines"></a>使用 Unity 協同程式載入模型

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

### <a name="load-model-with-await-pattern"></a>使用 await 模式載入模型

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

上述程式碼範例使用透過 SAS 的模型載入路徑，因為已載入內建模型。 透過 blob 容器（使用和`LoadModelAsync` `LoadModelParams`）來定址模型，可以完整類似。

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

建立 Unity 遊戲物件會以隱含方式`RemoteEntitySyncObject`將元件新增至遊戲物件。 這個元件是用來將實體轉換同步處理到伺服器。 根據預設`RemoteEntitySyncObject` ，使用者必須明確地呼叫`SyncToRemote()` ，以同步處理本機 Unity 狀態與伺服器。 啟用`SyncEveryFrame`會自動同步處理物件。

具有的`RemoteEntitySyncObject`物件可以透過 [**顯示子**系] 按鈕，具現化並顯示在 Unity 編輯器中的遠端子系。

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>包裝函式元件

附加至遠端轉譯實體的[元件](../../concepts/components.md)會透過 Proxy `MonoBehavior`向 Unity 公開。 這些 proxy 代表 Unity 中的遠端元件，並將所有修改轉送到主機。

若要建立 proxy 遠端轉譯元件，請使用擴充`GetOrCreateArrComponent`方法：

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>結合存留期

當遠端[實體](../../concepts/entities.md)和 Unity 遊戲物件透過系結時，其存留期會結合在一起`RemoteEntitySyncObject`。 如果您使用`UnityEngine.Object.Destroy(...)`這類遊戲物件呼叫，遠端實體也會一併移除。

若要摧毀 Unity 遊戲物件，而不影響遠端實體，您必須先在上`Unbind()`呼叫`RemoteEntitySyncObject`。

所有 proxy 元件的情況也是如此。 若只要終結用戶端表示，您必須先在 proxy 元件`Unbind()`上呼叫：

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
* [教學課程：在 Unity 中使用遠端實體](../../tutorials/unity/working-with-remote-entities.md)
