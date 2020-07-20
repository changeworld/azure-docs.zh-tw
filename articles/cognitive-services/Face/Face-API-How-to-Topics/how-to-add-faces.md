---
title: 範例：將臉部新增至 PersonGroup - 臉部
titleSuffix: Azure Cognitive Services
description: 本指南示範如何使用 Azure 認知服務的臉部辨識服務，將大量的人員和臉部新增至 PersonGroup 物件。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 240905d538afc5c0f4b7f0e0bf400fac23c3183f
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76169835"
---
# <a name="add-faces-to-a-persongroup"></a>將臉部新增至 PersonGroup

本指南示範如何將大量的人員和臉部新增至 PersonGroup 物件。 相同的策略也適用於 LargePersonGroup、FaceList 及 LargeFaceList 物件。 此範例是使用 Azure 認知服務的臉部 .NET 用戶端程式庫以 C# 撰寫的。

## <a name="step-1-initialization"></a>步驟 1:初始化

下列程式碼會宣告數個變數，並實作協助程式函式來排程臉部新增要求：

- `PersonCount` 是人員的總數。
- `CallLimitPerSecond` 是根據訂用帳戶層而定的每秒呼叫數上限。
- `_timeStampQueue` 是要記錄要求時間戳記的佇列。
- `await WaitCallLimitPerSecondAsync()` 會等候，直到能夠傳送下一個要求為止。

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>步驟 2:授權 API 呼叫

當您使用用戶端程式庫時，必須將訂用帳戶金鑰傳至 **FaceClient** 類別的建構函式。 例如：

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

若要取得訂用帳戶金鑰，請從 Azure 入口網站移至 Azure Marketplace。 如需詳細資訊，請參閱[訂用帳戶](https://www.microsoft.com/cognitive-services/sign-up)。

## <a name="step-3-create-the-persongroup"></a>步驟 3：建立 PersonGroup

名為 "MyPersonGroup" 的 PersonGroup 用來儲存人員。
要求時間已排入 `_timeStampQueue` 佇列中，以確保整體驗證。

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>步驟 4：針對 PersonGroup 建立人員

同時建立人員，而且也會套用 `await WaitCallLimitPerSecondAsync()` 以避免超出呼叫限制。

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>步驟 5：將人臉新增至人員

同時處理已新增至不同人的臉部。 針對某位特定人員新增的臉部均會進行循序處理。
同樣地，會叫用 `await WaitCallLimitPerSecondAsync()` 以確保要求頻率在限制的範圍內。

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>摘要

在本指南中，您已了解使用大量人員和臉部建立 PersonGroup 的流程。 幾項提醒：

- 此策略也適用於 FaceList 和 LargePersonGroup。
- 可同時處理對 LargePersonGroup 中的不同 FaceList 或人員新增或刪除臉部。
- 可循序處理對 LargePersonGroup 中的某個特定 FaceList 或人員新增或刪除臉部。
- 為了簡單起見，本指南省略了處理潛在例外狀況的方式。 如果您想要變得更強固，請套用適當的重試原則。

已說明及示範下列功能：

- 使用 [PersonGroup - 建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) \(英文\) API 來建立 PersonGroup。
- 使用 [PersonGroup - 建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) \(英文\) API 來建立人員。
- 使用 [PersonGroup Person - 新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) \(英文\) API 來將臉部新增至人員。

## <a name="related-topics"></a>相關主題

- [識別影像中的臉部](HowtoIdentifyFacesinImage.md)
- [偵測影像中的臉部](HowtoDetectFacesinImage.md)
- [使用大規模功能](how-to-use-large-scale.md)
