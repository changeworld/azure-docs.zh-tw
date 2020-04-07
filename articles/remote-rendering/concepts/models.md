---
title: 模型
description: 描述模型在 Azure 遠端呈現中的內容
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 770c88ddfb44004b76633cbeb726d28e1626a72c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681723"
---
# <a name="models"></a>模型

Azure 遠端呈現中的*模型*是指由[實體](entities.md)和[元件](components.md)組成的完整物件表示形式。 模型是將自定義數據引入遠程呈現服務的主要方式。

## <a name="model-structure"></a>模型結構

模型有一個[實體](entities.md)作為其根節點。 下面說,它可能具有子實體的任意層次結構。 載入模型時,將返回對此根實體的引用。

每個實體可能附加[元件](components.md)。 在最常見的情況下,實體具有*格格元件*,它引用[網格資源](meshes.md)。

## <a name="creating-models"></a>建立模型

通過轉換來自 FBX 和 GLTF 等檔案格式的[輸入模型](../how-tos/conversion/model-conversion.md),可以建立執行時模型。 轉換過程提取所有資源,如紋理、材質和等點,並將其轉換為優化的運行時格式。 它還將提取結構資訊並將其轉換為 ARR 的實體/元件圖形結構。

> [!IMPORTANT]
>
> [模型轉換](../how-tos/conversion/model-conversion.md)是創建[模樣](meshes.md)的唯一方法。 儘管在運行時可以在實體之間共用網格,但除了載入模型之外,沒有其他方法可以將網格引入運行時。

## <a name="loading-models"></a>載入模型

轉換模型後,可以從 Azure Blob 儲存載入至執行時。

有兩個不同的載入函數因 Blob 儲存中資產的定址方式而異:

* 模型可以通過其 SAS URI 進行定址。 相關的載入函數是`LoadModelFromSASAsync`帶參數`LoadModelFromSASParams`的。 載入[內建模型](../samples/sample-model.md)時,也可以使用此變體。
* 如果[blob 儲存連結到帳戶](../how-tos/create-an-account.md#link-storage-accounts),則可以通過 blob 儲存參數直接定址模型。 在這種情況下,相關的載入功能是`LoadModelAsync`參數`LoadModelParams`。

以下代碼段演示如何使用任一函數載入模型。 要使用 SAS URI 載入模型,請使用以下的代碼:

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

如果要直接使用其 blob 儲存參數載入模型,請使用類似於以下代碼段的代碼:

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

    // ... (identical to the SAS URI snippet above)
}
```

之後,您可以遍歷實體層次結構並修改實體和元件。 多次載入同一模型可創建多個實例,每個實例都有自己的實體/元件結構副本。 但是,由於網、材質和紋理是[共用資源](../concepts/lifetime.md),因此它們的數據將不再載入。 因此,多次實例化模型會產生相對較少的記憶體開銷。

> [!CAUTION]
> ARR 中的所有*非同步*函數傳回非同步操作物件。 您必須存儲對這些物件的引用,直到操作完成。 否則,C# 垃圾回收器可能會提前刪除該操作,並且它永遠無法完成。 在上面的示例代碼中,使用*await*可確保本地變數"loadOp"在模型載入完成之前保留引用。 但是,如果要改用 *"已完成"* 事件,則需要將非同步操作儲存在成員變數中。

## <a name="next-steps"></a>後續步驟

* [實體](entities.md)
* [格](meshes.md)
