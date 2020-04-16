---
title: Unity 遊戲物件和元件
description: 描述用於遠端渲染實體和元件的 Unity 特定方法。
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409863"
---
# <a name="interact-with-unity-game-objects-and-components"></a>與 Unity 遊戲物件和元件互動

Azure 遠端呈現 (ARR) 針對大量物件進行了優化(請參閱[限制](../../reference/limits.md))。 雖然可以在主機上管理大型和複雜的層次結構,但在低功率設備上在 Unity 中複製它們是不可行的。

因此,在主機上載入模型時,Azure 遠端呈現會鏡像有關用戶端設備上模型結構的資訊(這將產生網路流量),但不會複製 Unity 中的物件和元件。 相反,它期望您手動請求所需的 Unity 遊戲物件和元件,以便可以將開銷限制為實際需要的內容。 這樣,您可以對用戶端性能進行更多控制。

因此,Azure 遠程呈現的統一整合的附帶了可按需複製遠端呈現結構的其他功能。

## <a name="load-a-model-in-unity"></a>在 Unity 中載入模型

載入模型時,您將獲得對載入模型的根物件的引用。 此引用不是 Unity 遊戲物件,但您可以使用`Entity.GetOrCreateGameObject()`擴充方法將其轉換為一個。 該函數需要類型`UnityCreationMode`參數。 如果通過`CreateUnityComponents`,則新創建的 Unity 遊戲物件將另外使用主機上存在的所有遠端呈現元件的代理元件填充。 不過,建議您選擇`DoNotCreateUnityComponents`,以盡量少用開銷。

### <a name="load-model-with-task"></a>帶任務的載入模型

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

### <a name="load-model-with-unity-coroutines"></a>具有 Unity 協同例程式的載入模型

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

### <a name="load-model-with-await-pattern"></a>具有等待模式的載入模型

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

上述代碼示例通過 SAS 使用模型載入路徑,因為已載入內建模型。 通過 blob 容器`LoadModelAsync`(`LoadModelParams`使用和 ) 定址模型的工作方式完全類似。

## <a name="remoteentitysyncobject"></a>遠端實體同步物件

建立 Unity 遊戲物件`RemoteEntitySyncObject`隱式 地向遊戲物件添加元件。 此元件用於將實體轉換同步到伺服器。 預設情況下`RemoteEntitySyncObject`,要求使用者顯式調用`SyncToRemote()`以將本地 Unity 狀態同步到伺服器。 啟用`SyncEveryFrame`將自動同步物件。

具有的`RemoteEntitySyncObject`物件的遠端子級可以實例化,並通過 **「顯示子級**」按鈕在 Unity 編輯器中顯示。

![遠端實體同步物件](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>包裝元件

附加到遠端渲染實體的[元件](../../concepts/components.md)通過`MonoBehavior`代理 s 向 Unity 公開。 這些代理表示 Unity 中的遠端元件,並將所有修改轉發到主機。

要建立代理遠端呈現元件,請使用擴充方法`GetOrCreateArrComponent`:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>耦合壽命

遠端[實體](../../concepts/entities.md)與 Unity 遊戲物件的存留期在透過連結`RemoteEntitySyncObject`時耦合 。 如果使用此類遊戲`UnityEngine.Object.Destroy(...)`物件進行調用,則遠端實體也將被刪除。

要銷毀 Unity 遊戲物件,在不影響遠端實體的情況下,首先需要`Unbind()`呼`RemoteEntitySyncObject`叫 。

所有代理元件也是如此。 要僅銷毀用戶端表示形式,需要首先調用`Unbind()`代理元件:

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
