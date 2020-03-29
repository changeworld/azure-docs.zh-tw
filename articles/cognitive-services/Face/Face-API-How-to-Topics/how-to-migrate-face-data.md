---
title: 跨訂閱遷移面部資料 - 人臉
titleSuffix: Azure Cognitive Services
description: 本指南演示如何將存儲的面資料從一個面容訂閱遷移到另一個面容訂閱。
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169819"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>將您的臉部資料移轉至其他臉部訂用帳戶

本指南演示如何將面資料（如已保存的具有面的 PersonGroup 物件）移動到其他 Azure 認知服務面訂閱。 要移動資料，請使用快照功能。 這樣，在移動或展開操作時，您就不必重複構建和訓練 PersonGroup 或 FaceList 物件。 例如，您可能使用免費試用訂閱創建了 PersonGroup 物件，現在想要將其遷移到付費訂閱。 或者，對於大型企業操作，您可能需要跨不同區域的訂閱同步人臉資料。

相同的移轉策略也適用於 LargePersonGroup 和 LargeFaceList 物件。 如果您不熟悉本指南中的概念，請參閱[面識別概念](../concepts/face-recognition.md)指南中的定義。 本指南使用帶有 C# 的面 .NET 用戶端庫。

## <a name="prerequisites"></a>Prerequisites

您需要以下專案：

- 兩個面容訂閱金鑰，一個帶有現有資料，一個要遷移到。 要訂閱 Face 服務並獲得金鑰，請按照[創建認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的說明操作。
- 與目標訂閱對應的面容訂閱 ID 字串。 要查找它，請在 Azure 門戶中選擇 **"概述**"。 
- 任何版本的[視覺工作室2015或2017](https://www.visualstudio.com/downloads/)年。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

本指南使用簡單的主控台應用來運行人臉資料移轉。 有關完整實現，請參閱 GitHub 上的["人臉"快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)。

1. 在視覺化工作室中，創建新的主控台應用 .NET 框架專案。 將其命名為**FaceApi 快照示例**。
1. 取得必要的 NuGet 套件。 在解決方案資源管理器中按右鍵專案，然後選擇 **"管理 NuGet 包**"。 選擇"**流覽**"選項卡，然後選擇 **"包括預發行**"。 查找並安裝以下套裝軟體：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview) \(英文\)

## <a name="create-face-clients"></a>建立臉部用戶端

在 *Program.cs* 的 **Main** 方法中，為您的來源和目標訂用帳戶建立兩個 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) 執行個體。 本示例使用東亞區域的 Face 訂閱作為源，將西美國訂閱作為目標。 此示例演示如何將資料從一個 Azure 區域遷移到另一個 Azure 區域。 

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

填寫源和目標訂閱的訂閱鍵值和終結點 URL。


## <a name="prepare-a-persongroup-for-migration"></a>備妥移轉的 PersonGroup

若要將您來源訂用帳戶中的 PersonGroup 移轉到目標訂用帳戶，您會需要它的識別碼。 使用[PersonGroup 操作擴展.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)方法檢索人員組物件的清單。 然後獲取[PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)屬性。 此過程根據您擁有的人員組物件看起來不同。 在本指南中，源人員組 ID 存儲在`personGroupId`中。

> [!NOTE]
> [示例代碼](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)創建並訓練要遷移的新人員組。 在大多數情況下，您應該已經使用人員組。

## <a name="take-a-snapshot-of-a-persongroup"></a>拍攝人員組的快照

快照是某些 Face 資料類型的臨時遠端存放。 將資料從一個訂用帳戶複製到另一個訂用帳戶時，它的功能是類似剪貼簿。 首先，在源訂閱中拍攝資料的快照。 然後，將其應用於目標訂閱中的新資料物件。

使用源訂閱的 FaceClient 實例拍攝 PersonGroup 的快照。 將["帶Async"](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)與人員組 ID 和目標訂閱的 ID 一起使用。 如果您有多個目標訂閱，則將它們添加為第三個參數中的陣列條目。

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 拍攝和應用快照的過程不會中斷對源或目標人員組或 Facelists 的任何常規調用。 不要同時進行更改源物件的調用，例如[FaceList 管理調用](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)或[PersonGroup 培訓](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)呼叫。 快照操作可能在這些操作之前或之後運行，或者可能會遇到錯誤。

## <a name="retrieve-the-snapshot-id"></a>檢索快照 ID

用於拍攝快照的方法是非同步，因此必須等待其完成。 無法取消快照操作。 在此代碼中，`WaitForOperation`該方法監視非同步調用。 它每 100 毫秒檢查一次狀態。 操作完成後，通過分析`OperationLocation`欄位檢索操作 ID。 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

典型`OperationLocation`值如下所示：

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

顯示操作狀態後`Succeeded`，通過分析返回的操作狀態實例的`ResourceLocation`欄位獲取快照 ID。

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

典型`resourceLocation`值如下所示：

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>將快照應用於目標訂閱

接下來，使用隨機生成的 ID 在目標訂閱中創建新的 PersonGroup。 然後，使用目標訂閱的 FaceClient 實例將快照應用於此 PersonGroup。 傳遞快照 ID 和新的人員組 ID。

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 快照物件僅有效 48 小時。 僅當打算在不久後將其用於資料移轉時，才拍攝快照。

快照應用請求返回另一個操作 ID。 要獲取此 ID，請分析`OperationLocation`返回的 applySnapshotResult 實例的欄位。 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

快照應用程式過程也是非同步，因此再次使用它`WaitForOperation`等待它完成。

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>測試資料移轉

應用快照後，目標訂閱中的新 PersonGroup 將填充原始面資料。 預設情況下，也會複製訓練結果。 新的 PersonGroup 已準備好進行面部識別呼叫，無需再培訓。

要測試資料移轉，請運行以下操作並將它們列印的結果與主控台進行比較：

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

現在，您可以在目標訂閱中使用新的 PersonGroup。 

要將來再次更新目標人員組，請創建新的人員組以接收快照。 為此，請按照本指南中的步驟操作。 單個 PersonGroup 物件只能對它應用一次快照。

## <a name="clean-up-resources"></a>清除資源

完成遷移人臉資料後，手動刪除快照物件。

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>後續步驟

接下來，請參閱相關的 API 參考文檔，流覽使用快照功能的示例應用，或按照操作指南開始使用此處提到的其他 API 操作：

- [快照集參考文件 (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [人臉快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [新增臉部](how-to-add-faces.md)
- [檢測圖像中的人臉](HowtoDetectFacesinImage.md)
- [識別影像中的臉部](HowtoIdentifyFacesinImage.md)
