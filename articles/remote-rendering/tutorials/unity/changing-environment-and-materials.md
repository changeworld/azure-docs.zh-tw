---
title: 變更環境與材質
description: 示範如何在 Unity 場景中修改天體圖和物件材質的教學課程。
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678679"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>教學課程：變更環境與材質

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 變更場景的環境地圖。
> * 修改材質參數。
> * 載入自訂紋理。

## <a name="prerequisites"></a>Prerequisites

本教學課程假設您已熟悉[教學課程：在 Unity 中使用遠端實體](working-with-remote-entities.md)。 不過，您只需要一個 Unity 專案，可用來連線至工作階段並載入模型，如以下所示：[教學課程：從頭開始設定 Unity 專案](project-setup.md)。

> [!TIP]
> [ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)包含 *Unity* 資料夾中所有教學課程的預備 Unity 專案，可供您作為參考。

## <a name="change-the-environment-map"></a>變更環境地圖

Azure 遠端轉譯支援使用[天空盒](../../overview/features/sky.md) (有時也稱為「環境地圖」) 來模擬環境光線。 當您的物件使用 *[以實體為基礎的轉譯](../../overview/features/pbr-materials.md)* 時，這會特別有用，如同我們的範例模型一樣。 ARR 也隨附各種內建的天空紋理，我們將在本教學課程中使用。

建立名為 **RemoteSky** 的新指令碼，並將其新增至新的 GameObject。 開啟指令檔並以下列程式碼取代：

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

請注意，上面使用 `LoadTextureFromSASAsync` 變體，因為已載入內建紋理。 如果是從[連結的 Blob 儲存體](../../how-tos/create-an-account.md#link-storage-accounts)載入，請使用 `LoadTextureAsync` 變體。 如需模型載入方式的範例，請參閱[模型載入](../../concepts/models.md#loading-models)一節。

當您執行程式碼並切換到天體圖時，您會注意到模型上的光線截然不同。 不過，背景會保持黑色，而且您看不到實際的天空紋理。 這是刻意設計的，因為轉譯背景會干擾擴增實境裝置。 在適當的應用程式中，您應該使用類似於真實世界周圍的天空紋理，因為這有助於讓物件更真實地出現。

## <a name="modify-materials"></a>修改材質

在上一個教學課程中，我們使用[狀態覆寫元件](../../overview/features/override-hierarchical-state.md)來變更所選物件的濃淡色彩。 我們現在想要達到類似效果，但藉由修改物件的[材質](../../concepts/materials.md)來這麼做。

首先，我們需要指令碼來挑選物件，就像我們在[第二個教學課程](working-with-remote-entities.md)中的做法。 如果您還沒有 **RemoteRaycaster** 指令碼，請立即建立。 以下列程式碼取代其內容：

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
}
```

將元件新增至您的 *RemoteRendering* 遊戲物件。 其負責使用滑鼠挑選物件，並將 *RemoteModelEntity* 元件新增至挑選的物件。 該元件類別就是我們實作實際材質變更功能的地方。

如果您還沒有 **RemoteModelEntity** 指令碼，請加以建立並以下列程式碼取代其內容：

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

當您執行此程式碼時，滑鼠暫留的物件會醒目提示。 效果與我們在教學課程 2 中所做的類似，但其達成方式不同。 我們會在此取得所選物件的材質清單，然後修改第一個材質，使其具有不同的 Albedo 色彩。

> [!IMPORTANT]
> 請注意，此方法是否會醒目提示模型的正確組件，這取決於模型的撰寫方式。 如果每個物件都只使用一個材質，就會完美地運作。 不過，如果模型的組件與材質之間沒有 1:1 關聯性，則上述的簡單程式碼將不會執行正確的動作。

## <a name="use-a-different-texture"></a>使用不同的紋理

[Textures](../../concepts/textures.md) 通常是來源模型的一部分。 在[模型轉換](../../quickstarts/convert-model.md)期間，所有紋理都會轉換成所需的執行階段格式並封裝到最終模型檔案中。 若要在執行階段取代紋理，您需要將其儲存為 [DDS 檔案格式](https://en.wikipedia.org/wiki/DirectDraw_Surface)並將其上傳至 Azure Blob 儲存體。 如需如何建立 Azure Blob 容器的資訊，請參閱[本快速入門指南](../../quickstarts/convert-model.md)。 一旦有了 Blob 容器，您就可以在 Azure 儲存體總管中加以開啟，並透過拖放功能上傳您的檔案。

在執行階段端，您可以使用兩種不同的方式來處理 Blob 儲存體中的文立資產：

* 依其 SAS URI 處理紋理。 為此，請在上傳的檔案上按一下滑鼠右鍵，然後從快顯功能表中選取 [取得共用存取簽章...]  。 使用此 SAS URI 搭配 `LoadTextureFromSASAsync` 函式變體 (請參閱下面的範例程式碼)。
* 直接由 Blob 儲存體參數處理紋理，以防 [Blob 儲存體連結至帳戶](../../how-tos/create-an-account.md#link-storage-accounts)。 在此情況下，相關的載入函式為 `LoadTextureAsync`。

現在開啟 **RemoteModelEntity** 指令碼，新增下列程式碼，並移除重複的函式：

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

執行此程式碼並將滑鼠停留在您的模型上。 如果模型具有適當的 UV 座標，您應會看到您的紋理出現。 否則，您可能只會注意到色彩變更。

## <a name="next-steps"></a>後續步驟

最後是關於如何使用 Azure 遠端轉譯搭配 Unity 的簡介系列。 在下一個步驟中，您應熟悉 ARR 的一些基本概念，例如[工作階段](../../concepts/sessions.md)、[實體](../../concepts/entities.md)和[模型](../../concepts/models.md)，以獲得更深入的了解。 另外還有您應該更詳細探索的各種功能，例如[光線](../../overview/features/lights.md)、[外框轉譯](../../overview/features/outlines.md)、[階層式狀態覆寫](../../overview/features/override-hierarchical-state.md)和[材質](../../concepts/materials.md)。

> [!div class="nextstepaction"]
> [Unity 遊戲物件和元件](../../how-tos/unity/objects-components.md)
