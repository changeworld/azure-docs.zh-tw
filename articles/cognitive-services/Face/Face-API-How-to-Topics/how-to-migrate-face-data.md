---
title: 跨訂用帳戶遷移臉部資料-臉部
titleSuffix: Azure Cognitive Services
description: 本指南說明如何將已儲存的臉部資料從一個臉部訂用帳戶遷移至另一個。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: nitinme
ms.openlocfilehash: ab03e68acf96928922959ed939c0accc1f8290a5
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84986661"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>將您的臉部資料移轉至其他臉部訂用帳戶

本指南說明如何將臉部資料（例如具有臉部的已儲存 PersonGroup 物件）移至不同的 Azure 認知服務臉部訂用帳戶。 若要移動資料，您可以使用快照集功能。 如此一來，您就不需要在移動或擴充作業時，重複建立和定型 PersonGroup 或 FaceList 物件。 例如，您可能建立了具有免費訂用帳戶的 PersonGroup 物件，而現在想要將它遷移至您的付費訂用帳戶。 或者，您可能需要針對大型企業作業，跨不同區域中的訂用帳戶同步處理臉部資料。

相同的移轉策略也適用於 LargePersonGroup 和 LargeFaceList 物件。 如果您不熟悉本指南中的概念，請參閱[臉部辨識概念](../concepts/face-recognition.md)指南中的定義。 本指南使用具有 c # 的臉部 .NET 用戶端程式庫。

## <a name="prerequisites"></a>先決條件

您需要下列專案：

- 兩個臉部訂用帳戶金鑰，一個包含現有的資料，另一個則用來進行遷移。 若要訂閱臉部服務並取得您的金鑰，請遵循[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示。
- 對應至目標訂用帳戶的臉部訂用帳戶識別碼字串。 若要尋找它，請選取 [Azure 入口網站中的 **[總覽**]。 
- 任何 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 版本。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

本指南使用簡單的主控台應用程式來執行臉部資料移轉。 如需完整的執行方式，請參閱 GitHub 上的[臉部快照集範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)。

1. 在 Visual Studio 中，建立新的主控台應用程式 .NET Framework 專案。 將它命名為**FaceApiSnapshotSample**。
1. 取得必要的 NuGet 套件。 以滑鼠右鍵按一下方案總管中的專案，然後選取 [**管理 NuGet 套件**]。 選取 [**流覽**] 索引標籤，然後選取 [**包含發行**前版本]。 尋找並安裝下列套件：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview) \(英文\)

## <a name="create-face-clients"></a>建立臉部用戶端

在 *Program.cs* 的 **Main** 方法中，為您的來源和目標訂用帳戶建立兩個 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) 執行個體。 這個範例會使用東亞區域中的臉部訂用帳戶做為來源，而「美國西部」訂用帳戶作為目標。 此範例示範如何將資料從一個 Azure 區域遷移至另一個區域。 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

針對您的來源和目標訂用帳戶，填入訂用帳戶金鑰值和端點 Url。


## <a name="prepare-a-persongroup-for-migration"></a>備妥移轉的 PersonGroup

若要將您來源訂用帳戶中的 PersonGroup 移轉到目標訂用帳戶，您會需要它的識別碼。 使用[PersonGroupOperationsExtensions. metrics.listasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)方法來抓取 PersonGroup 物件的清單。 然後取得[PersonGroup. PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)屬性。 此程式會根據您擁有的 PersonGroup 物件而有所不同。 在本指南中，來源 PersonGroup 識別碼會儲存在中 `personGroupId` 。

> [!NOTE]
> [範例程式碼](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)會建立並訓練要遷移的新 PersonGroup。 在大部分情況下，您應該已經有要使用的 PersonGroup。

## <a name="take-a-snapshot-of-a-persongroup"></a>製作 PersonGroup 的快照集

快照集是某些臉部資料類型的暫時性遠端儲存體。 將資料從一個訂用帳戶複製到另一個訂用帳戶時，它的功能是類似剪貼簿。 首先，您會建立來源訂用帳戶中資料的快照集。 然後將它套用到目標訂用帳戶中的新資料物件。

使用來源訂用帳戶的 FaceClient 實例來建立 PersonGroup 的快照集。 使用[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)搭配 PersonGroup 識別碼和目標訂用帳戶的識別碼。 如果您有多個目標訂閱，請在第三個參數中將它們新增為數組專案。

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 採用和套用快照集的程式並不會中斷對來源或目標 Persongroup 或 Facelist 的任何定期呼叫。 請勿進行同時呼叫來變更來源物件，例如[FaceList 管理呼叫](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)或[PersonGroup 訓練](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)呼叫。 快照集作業可能會在這些作業之前或之後執行，或可能會發生錯誤。

## <a name="retrieve-the-snapshot-id"></a>取出快照集識別碼

用來製作快照集的方法是非同步，因此您必須等候其完成。 無法取消快照集作業。 在此程式碼中， `WaitForOperation` 方法會監視非同步呼叫。 它會每隔100毫秒檢查一次狀態。 在作業完成之後，藉由剖析欄位來取出作業識別碼 `OperationLocation` 。 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

一般的 `OperationLocation` 值看起來像這樣：

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

`WaitForOperation` 協助程式方法如下：

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

在作業狀態顯示之後 `Succeeded` ，藉由剖析 `ResourceLocation` 傳回之 OperationStatus 實例的欄位來取得快照集識別碼。

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

一般的 `resourceLocation` 值看起來像這樣：

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>將快照集套用至目標訂用帳戶

接下來，使用隨機產生的識別碼，在目標訂用帳戶中建立新的 PersonGroup。 然後使用目標訂用帳戶的 FaceClient 實例，將快照集套用至此 PersonGroup。 傳入快照集識別碼和新的 PersonGroup 識別碼。

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 快照集物件只適用于48小時。 如果您想要在不久後立即使用快照集來進行資料移轉，請只建立快照集。

快照集套用要求會傳回另一個作業識別碼。 若要取得此識別碼，請剖析 `OperationLocation` 傳回之 applySnapshotResult 實例的欄位。 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

快照集應用程式進程也是非同步，因此，再次使用 `WaitForOperation` 來等待它完成。

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>測試資料移轉

套用快照集之後，目標訂用帳戶中的新 PersonGroup 會填入原始臉部資料。 根據預設，也會複製定型結果。 新的 PersonGroup 已準備好進行臉部辨識呼叫，而不需要重新定型。

若要測試資料移轉，請執行下列作業，並將它們所列印的結果與主控台進行比較：

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

使用下列協助程式方法：

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

現在您可以在目標訂用帳戶中使用新的 PersonGroup。 

若要在未來再次更新目標 PersonGroup，請建立新的 PersonGroup 以接收快照集。 若要這麼做，請依照本指南中的步驟進行。 單一 PersonGroup 物件只能套用一次快照集。

## <a name="clean-up-resources"></a>清除資源

在您完成資料移轉之後，請手動刪除快照集物件。

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>後續步驟

接下來，請參閱相關的 API 參考檔、探索使用快照集功能的範例應用程式，或遵循操作指南開始使用此處所述的其他 API 作業：

- [快照集參考文件 (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [臉部快照集範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [新增人臉](how-to-add-faces.md)
- [偵測影像中的臉部](HowtoDetectFacesinImage.md)
- [識別影像中的臉部](HowtoIdentifyFacesinImage.md)
