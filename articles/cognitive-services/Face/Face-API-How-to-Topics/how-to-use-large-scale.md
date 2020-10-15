---
title: 範例：使用大規模功能 - 臉部
titleSuffix: Azure Cognitive Services
description: 本指南文章說明如何將規模從現有的 PersonGroup 和 FaceList 物件擴大為 LargePersonGroup 和 LargeFaceList 物件。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 5341c2613624c6a52f1649dcd8a64b6746b84f67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332384"
---
# <a name="example-use-the-large-scale-feature"></a>範例：使用大型功能

本指南是一篇進階文章，可說明如何分別將規模從現有的 PersonGroup 和 FaceList 物件擴大為 LargePersonGroup 和 LargeFaceList 物件。 本指南示範移轉流程。 其假設您對於 PersonGroup 和 FaceList 物件、[定型](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) \(英文\) 作業及臉部辨識功能已有基本了解。 若要深入了解這些物件，請參閱[臉部辨識](../concepts/face-recognition.md)概念指南。

LargePersonGroup 和 LargeFaceList 統稱為大規模作業。 LargePersonGroup 最多可包含 100 萬個人，每人最多可有 248 張臉。 LargeFaceList 最多可包含 100 萬張臉。 大規模作業與傳統的 PersonGroup 和 FaceList 類似，但由於採用新架構，因此有一些差異。 

這些範例是使用 Azure 認知服務的臉部用戶端程式庫以 C# 撰寫的。

> [!NOTE]
> 若要大規模針對 Identification 和 FindSimilar 啟用臉部搜尋效能，請導入定型作業來對 LargeFaceList 和 LargePersonGroup 進行前處理。 根據實際容量而定，定型時間從數秒到大約半小時不等。 如果先前已成功進行定型作業，則在定型期間，可能會執行 Identification 和 FindSimilar。 缺點則是在完成新的大規模定型後移轉之前，新增的人員和臉部不會出現在結果中。

## <a name="step-1-initialize-the-client-object"></a>步驟 1:初始化用戶端物件

在使用臉部用戶端程式庫時，會透過 FaceClient 類別的建構函式，傳入訂用帳戶金鑰和訂用帳戶端點。 例如：

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

若要取得訂用帳戶金鑰及其對應端點，請從 Azure 入口網站移至 Azure Marketplace。
如需詳細資訊，請參閱[訂用帳戶](https://azure.microsoft.com/services/cognitive-services/directory/vision/)。

## <a name="step-2-code-migration"></a>步驟 2:程式碼移轉

本節著重於如何將 PersonGroup 或 FaceList 實作移轉至 LargePersonGroup 或 LargeFaceList。 雖然 LargePersonGroup 或 LargeFaceList 與 PersonGroup 或 FaceList 在設計及內部實作上不同，但 API 介面類似，均支援回溯相容性。

不支援資料移轉。 您可以改為重新建立 LargePersonGroup 或 LargeFaceList。

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>將 PersonGroup 移轉至 LargePersonGroup

從 PersonGroup 移轉至 LargePersonGroup 很簡單。 它們會共用完全相同的群組層級作業。

針對 PersonGroup 或人員相關的實作，只需將 API 路徑或 SDK 類別/模組變更為 LargePersonGroup 和 LargePersonGroup Person 即可。

將 PersonGroup 中的所有臉部和人員新增至新的 LargePersonGroup。 如需詳細資訊，請參閱[新增臉部](how-to-add-faces.md)。

### <a name="migrate-a-facelist-to-a-largefacelist"></a>將 FaceList 移轉至 LargeFaceList

| FaceList API | LargeFaceList API |
|:---:|:---:|
| 建立 | 建立 |
| 刪除 | 刪除 |
| Get | Get |
| 清單 | 清單 |
| 更新 | 更新 |
| - | 定型 |
| - | 取得定型訓練 |

上表是 FaceList 與 LargeFaceList 之間的清單層級作業比較。 如所示，與 FaceList 相比時，LargeFaceList 附帶「定型」和「取得定型狀態」這兩項新作業。 將 LargeFaceList 定型是 [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) \(英文\) 作業的前置條件。 FaceList 不需要定型。 以下程式碼片段是一個等候 LargeFaceList 定型的協助程式函式：

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

以前，含已新增臉部的 FaceList 與 FindSimilar 的一般用法如下所示：

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

將其移轉至 LargeFaceList 時，即會變成下列內容：

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

如前所示，資料管理和 FindSimilar 部分幾乎相同。 唯一例外是必須先在 LargeFaceList 中完成全新的前處理定型作業，FindSimilar 才能運作。

## <a name="step-3-train-suggestions"></a>步驟 3：定型建議

雖然定型作業可加快 [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) \(英文\) 和 [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 的速度，但定型時間會受到影響，尤其是在大規模進行時。 下表列出針對各種不同規模預估的定型時間。

| 臉部或人員的規模 | 預估的定型時間 |
|:---:|:---:|
| 1,000 | 1-2 秒 |
| 10,000 | 5-10 秒 |
| 100,000 | 1-2 分 |
| 1,000,000 | 10-30 分 |

若要充分利用大規模功能，我們建議使用下列策略。

### <a name="step-31-customize-time-interval"></a>步驟 3.1：自訂時間間隔

如 `TrainLargeFaceList()` 中所示，有一個會延遲無限定型狀態檢查程序的時間間隔 (以毫秒為單位)。 針對臉部數量較多的 LargeFaceList，使用較長的間隔將可降低呼叫計數和成本。 根據預期的 LargeFaceList 容量來自訂時間間隔。

相同的策略也適用於 LargePersonGroup。 例如，當您將含 100 萬人的 LargePersonGroup 定型時，`timeIntervalInMilliseconds` 可能是 60,000 (此為 1 分鐘的間隔)。

### <a name="step-32-small-scale-buffer"></a>步驟 3.2：小規模緩衝區

LargePersonGroup 或 LargeFaceList 中的人員或臉部只有在定型後才可供搜尋。 在動態案例中，新的人員或臉部會持續新增且必須立即可供搜尋，而定型所花費的時間可能超出預期。 

為了緩解此問題，僅針對剛新增的項目使用額外的小規模 LargePersonGroup 或 LargeFaceList 作為緩衝區。 由於大小較小，因此，此緩衝區會採用較短的時間來定型。 此暫存緩衝區上的即時搜尋功能應會運作。 請以更疏鬆的間隔來執行主要定型，藉以將此緩衝區與主要 LargePersonGroup 或 LargeFaceList 上的定型一起使用。 範例包括午夜和每日。

範例工作流程：

1. 建立主要 LargePersonGroup 或 LargeFaceList，此為主要集合。 建立緩衝區 LargePersonGroup 或 LargeFaceList，此為緩衝區集合。 緩衝區集合僅適用於新增加的人員或臉部。
1. 將新的人員或臉部同時新增至主要集合和緩衝區集合。
1. 只以短時間間隔來將緩衝區集合定型，以確保新增加的項目會生效。
1. 同時針對主要集合和緩衝區集合呼叫 Identification 或 FindSimilar。 合併結果。
1. 在緩衝區集合大小增加達到閾值，或處於系統閒置時間時，建立新的緩衝區集合。 觸發主要集合上的定型作業。
1. 當主要集合上的定型完成之後，刪除舊的緩衝區集合。

### <a name="step-33-standalone-training"></a>步驟 3.3：獨立定型

如果可以接受相對較長的延遲，就不需要在新增資料後立即觸發定型作業。 可以改為將「定型」作業從主要邏輯中分割出來，而以定期方式觸發。 此策略適用於可接受延遲的動態案例。 它可以套用至靜態案例，以進一步降低定型頻率。

假設有一個類似於 `TrainLargeFaceList` 的 `TrainLargePersonGroup` 函式。 透過叫用 `System.Timers` 中的 [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) \(部分機器翻譯\) 類別，以在 LargePersonGroup 上進行獨立定型的一般實作為：

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

如需資料管理和識別相關實作的詳細資訊，請參閱[新增臉部](how-to-add-faces.md)。

## <a name="summary"></a>摘要

在本指南中，您已了解如何將現有的 PersonGroup 或 FaceList 程式碼 (非資料) 移轉至 LargePersonGroup 或 LargeFaceList：

- LargePersonGroup 和 LargeFaceList 的運作方式與 PersonGroup 或 FaceList 類似，差別在於 LargeFaceList 需要定型作業。
- 採用適當的定型策略，以針對大規模資料集進行動態資料更新。

## <a name="next-steps"></a>後續步驟

依照操作指南了解如何將臉部新增至 PersonGroup，或撰寫指令碼以在 PersonGroup 上執行識別作業。

- [新增臉部](how-to-add-faces.md)
- [臉部用戶端程式庫快速入門](../Quickstarts/client-libraries.md)