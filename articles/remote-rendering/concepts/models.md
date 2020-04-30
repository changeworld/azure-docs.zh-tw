---
title: 模型
description: 說明模型在 Azure 遠端呈現中的內容
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617958"
---
# <a name="models"></a>模型

Azure 遠端呈現中的*模型*是指由[實體](entities.md)和[元件](components.md)組成的完整物件標記法。 模型是將自訂資料取得遠端轉譯服務的主要方式。

## <a name="model-structure"></a>模型結構

模型只有一個[實體](entities.md)做為其根節點。 其底下可能有子實體的任意階層。 載入模型時，會傳回這個根實體的參考。

每個實體都可能附加[元件](components.md)。 在最常見的情況下，實體具有*MeshComponents*，其參考[網格資源](meshes.md)。

## <a name="creating-models"></a>建立模型

建立執行時間模型的方式是從檔案格式（例如 FBX 和 GLTF）[轉換輸入模型](../how-tos/conversion/model-conversion.md)。 轉換程式會將所有資源（例如材質、材質和網格）解壓縮，並將它們轉換成優化的執行時間格式。 它也會將結構化資訊解壓縮，並將其轉換成 ARR 的實體/元件圖形結構。

> [!IMPORTANT]
>
> [模型轉換](../how-tos/conversion/model-conversion.md)是建立[網格](meshes.md)的唯一方法。 雖然網格可以在執行時間于實體之間共用，但是沒有任何其他方法可以將網格加入至執行時間，而不是載入模型。

## <a name="loading-models"></a>載入模型

一旦模型轉換之後，就可以從 Azure blob 儲存體載入至執行時間。

有兩個不同的載入函式，與在 blob 儲存體中定址資產的方式不同：

* 模型可以透過其 SAS URI 來定址。 相關的載入函數`LoadModelFromSASAsync`是使用`LoadModelFromSASParams`參數。 載入[內建模型](../samples/sample-model.md)時，也請使用此變體。
* 如果[blob 儲存體連結至帳戶](../how-tos/create-an-account.md#link-storage-accounts)，blob 儲存體參數可以直接定址模型。 在此情況下，相關的`LoadModelAsync`載入函數`LoadModelParams`是使用參數。

下列程式碼片段示範如何使用其中一個函數來載入模型。 若要使用 SAS URI 載入模型，請使用如下所示的程式碼：

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

如果您想要直接使用其 blob 儲存體參數來載入模型，請使用類似下列程式碼片段的程式碼：

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

之後，您可以流覽實體階層，並修改實體和元件。 多次載入相同的模型會建立多個實例，每個實例都有自己的實體/元件結構複本。 由於網格、材質和材質是[共用資源](../concepts/lifetime.md)，因此不會再次載入其資料。 因此，多次具現化模型只會產生相對較少的記憶體額外負荷。

> [!CAUTION]
> ARR 中的所有*非同步*函數都會傳回非同步作業物件。 您必須先儲存這些物件的參考，直到作業完成為止。 否則，c # 垃圾收集行程可能會提早刪除作業，而且永遠無法完成。 在上述的範例程式碼中，使用*await*可保證本機變數 ' loadOp ' 會保留參考，直到模型載入完成為止。 不過，如果您改為使用*已完成*的事件，您就必須將非同步作業儲存在成員變數中。

## <a name="next-steps"></a>後續步驟

* [實體](entities.md)
* [網狀](meshes.md)
