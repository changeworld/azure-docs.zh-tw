---
title: 模型
description: 說明什麼是 Azure 遠端轉譯中的模型
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 7832f999de2f6f16cfe816c061925e371f90662e
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758685"
---
# <a name="models"></a>模型

Azure 遠端轉譯中的「模型」指的是完整的物件標記法，其由 [實體](entities.md)和[元件](components.md)所組成。 模型是讓自訂資料加入遠端轉譯服務的主要方式。

## <a name="model-structure"></a>模型結構

模型只有一個[實體](entities.md)做為根節點。 其底下可能有子實體的任意階層。 載入模型時，系統會傳回此根實體的參考。

每個實體都可能有附加[元件](components.md)。 在最常見的情況下，具有「MeshComponents」的實體會參考[網格資源](meshes.md)。

## <a name="creating-models"></a>建立模型

建立執行階段模型的方式是從檔案格式 (例如 FBX 和 GLTF) [轉換輸入模型](../how-tos/conversion/model-conversion.md)。 轉換程序會將所有資源 (例如紋理、材質和網格) 解壓縮，並將這些資源轉換成最佳化的執行階段格式。 它也會將結構化資訊解壓縮，並將其轉換成 ARR 的實體/元件圖表結構。

> [!IMPORTANT]
>
> [模型轉換](../how-tos/conversion/model-conversion.md)是建立[網格](meshes.md)的唯一方式。 雖然網格可以在執行階段於實體之間共用，但是卻沒有其他方法可以在不載入模型的情況下，將網格加入執行階段。

## <a name="loading-models"></a>載入模型

一旦模型轉換之後，就可以從 Azure blob 儲存體載入至執行階段。

其中有兩個不同的載入函式，其與在 blob 儲存體中定址資產的方式不同：

* 模型可以透過其 SAS URI 定址。 相關的載入函式為 `LoadModelFromSASAsync` 搭配 `LoadModelFromSASParams` 參數。 當在載入[內建模型](../samples/sample-model.md)時，也請使用此變數。
* 此模型可直接由 Blob 儲存體參數定址，以防 [Blob 儲存體連結至帳戶](../how-tos/create-an-account.md#link-storage-accounts)。 在此情況下，相關的載入函式為 `LoadModelAsync` 搭配 `LoadModelParams` 參數。

下列程式碼片段示範如何使用其中一個函式以載入模型。 若要使用 SAS URI 載入模型，請使用如下所示的程式碼：

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

如果您想直接使用它的 blob 儲存體參數以載入模型，則請用類似下列程式碼片段的程式碼：

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

之後，您可以周遊實體階層，並修改該實體和元件。 多次載入相同的模型會建立多個執行個體，而每個執行個體都有自己的實體/元件結構複本。 由於網格、材質和紋理是[共用資源](../concepts/lifetime.md)，因此系統不會再次載入其資料。 因此，多次具現化模型只會產生相對較少的記憶體額外負荷。

> [!CAUTION]
> ARR 中的所有「非同步」函式都會傳回非同步作業物件。 您必須先儲存這些物件的參考，直到作業完成為止。 否則，C# 記憶體回收行程可能會提早刪除作業，而且其永遠無法完成。 在上述範例程式碼中，使用「await」可保證本機變數「loadOp」會保留參考，直到模型載入完成為止。 不過，如果您改為使用「Completed」事件，則必須將非同步作業儲存在成員變數中。

## <a name="next-steps"></a>後續步驟

* [實體](entities.md)
* [網格](meshes.md)
