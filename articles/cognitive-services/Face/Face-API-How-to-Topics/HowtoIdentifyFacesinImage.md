---
title: 範例：識別影像中的臉部 - 臉部
titleSuffix: Azure Cognitive Services
description: 本指南示範如何使用 PersonGroup 物件來識別不明的臉部，這些臉部是事先從已知的人員中建立的。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76169905"
---
# <a name="example-identify-faces-in-images"></a>範例：識別影像中的臉部

本指南示範如何使用 PersonGroup 物件來識別不明的臉部，這些臉部是事先從已知的人員中建立的。 這些範例是使用 Azure 認知服務的臉部用戶端程式庫以 C# 撰寫的。

## <a name="preparation"></a>準備

此範例示範：

- 如何建立 PersonGroup。 此 PersonGroup 包含已知的人員清單。
- 如何將臉部指派給每個人。 這些臉部可用來作為識別人員的基準。 我們建議您使用清晰的臉部正面檢視。 相片識別碼即為一例。 一組不錯的相片包含具有不同姿勢、衣服顏色或髮型之同一個人的臉部。

若要執行此範例的示範，請準備：

- 一些具有人臉的相片。 [下載範例相片](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)，即 Anna、Bill 和 Clare 的相片。
- 一系列測試用的相片。 相片或許會包含 Anna、Bill 或 Clare 的臉部。 它們都可用來測試識別。 此外，也可以從上述連結選取一些範例影像。

## <a name="step-1-authorize-the-api-call"></a>步驟 1:授權 API 呼叫

每次呼叫臉部 API 時，都需要訂用帳戶金鑰。 此金鑰可以透過查詢字串參數來傳遞，或在要求標頭中指定。 若要透過查詢字串來傳遞訂用帳戶金鑰，請參閱[臉部 - 偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) \(英文\) 中可作為範例的要求 URL：
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

或者，在 HTTP 要求標頭中指定訂用帳戶金鑰：**ocp-apim-subscription-key:&lt;訂用帳戶金鑰&gt;** 。
當您使用用戶端程式庫時，即會透過 FaceClient 類別的建構函式傳入訂用帳戶金鑰。 例如：
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
若要取得訂用帳戶金鑰，請從 Azure 入口網站移至 Azure Marketplace。 如需詳細資訊，請參閱[訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)。

## <a name="step-2-create-the-persongroup"></a>步驟 2:建立 PersonGroup

在此步驟中，名為 "MyFriends" 的 PersonGroup 會包含 Anna、Bill 和 Clare。 每個人都會註冊數個臉部。 您必須從影像中偵測臉部。 在所有這些步驟之後，您會有 PersonGroup，如下圖所示：

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>步驟 2.1：針對 PersonGroup 定義人員
人員是基本識別單位。 一個人可以註冊一或多個已知臉部。 PersonGroup 是人員的集合。 每個人均定義於特定的 PersonGroup 內。 識別會針對 PersonGroup 進行。 此工作是建立 PersonGroup，然後在其中建立人員 (例如 Anna、Bill 和 Clare)。

首先，使用 [PersonGroup - 建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) \(英文\) API 來建立新的 PersonGroup。 對應的用戶端程式庫 API 是 FaceClient 類別的 CreatePersonGroupAsync 方法。 指定來建立群組的群組識別碼對每個訂用帳戶而言都是唯一的。 您也可以使用其他 PersonGroup API，來取得、更新或刪除 PersonGroup。 

定義群組之後，您可以使用 [PersonGroup Person - 建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) \(英文\) API，在其內定義人員。 用戶端程式庫方法是 CreatePersonAsync。 您可以在建立人員之後新增每個人的臉部。

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a> 步驟 2.2：偵測臉部並將它們註冊到正確的人員
在 HTTP 要求本文中，使用影像檔將 "POST" Web 要求傳送給[臉部 - 偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API，即可完成偵測。 使用用戶端程式庫時，會透過 FaceClient 類別的其中一個 DetectAsync 方法進行臉部偵測。

針對每張偵測到的臉部，呼叫 [PersonGroup Person - 新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) \(英文\)，以將它新增到正確的人員。

下列程式碼示範如何從影像中偵測到臉部並將它新增給人員的程序：

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
若影像包含多個臉部，則只會新增最大的臉部。 您可以為人員新增其他臉部。 將格式為 "targetFace = left, top, width, height" 的字串傳遞到 [PersonGroup Person - 新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 的 targetFace 查詢參數。 您也可以使用 AddPersonFaceAsync 方法的 targetFace 選擇性參數來新增其他臉部。 每張新增到人員的臉部都會指定唯一存留的臉部識別碼。 您可以在 [PersonGroup Person - 刪除臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e)和[臉部 - 識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)中使用此識別碼。

## <a name="step-3-train-the-persongroup"></a>步驟 3：訓練 PersonGroup

您必須先將 PersonGroup 定型，才能使用它來執行識別。 當您新增或移除任何人員之後，或者，如果您編輯了某位人員已註冊的臉部，就必須將 PersonGroup 重新定型。 訓練是透過 [PersonGroup - 訓練](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API 完成。 使用用戶端程式庫時，它是 TrainPersonGroupAsync 方法的呼叫：
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
定型為非同步的流程。 即使在 TrainPersonGroupAsync 方法傳回之後，它可能也無法完成。 您可能需要查詢定型狀態。 使用 [PersonGroup - 取得定型狀態](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API 或用戶端程式庫的 GetPersonGroupTrainingStatusAsync 方法。 下列程式碼示範等待 PersonGroup 定型完成的簡單邏輯：
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>步驟 4：根據定義的 PersonGroup 來識別臉部

臉部辨識服務執行識別時，會在群組內的所有臉部之間計算測試臉部的相似度。 它會傳回與測試臉部最相似的人員。 此流程會透過[臉部 - 識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API 或用戶端程式庫的 IdentifyAsync 方法來完成。

測試臉部必須使用先前的步驟來偵測。 接著，將臉部識別碼傳遞至識別 API 以作為第二個引數。 一次可識別多個臉部識別碼。 結果會包含所有識別出的結果。 根據預設，識別流程只會傳回一位最符合測試臉部的人員。 如果您想要的話，可以指定選擇性參數 maxNumOfCandidatesReturned，讓識別流程能夠傳回更多候選項目。

下列程式碼示範識別流程：

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

當您完成這些步驟之後，請嘗試識別不同的臉部。 查看是否可以根據上傳進行臉部偵測的影像，正確識別出 Anna、Bill 或 Clare 的臉部。 請參閱下列範例：

![識別不同的臉部](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>步驟 5：大規模的要求

基於先前設計的限制，PersonGroup 最多可保留 10,000 個人。
如需高達百萬規模之情節的詳細資訊，請參閱[如何使用大規模功能](how-to-use-large-scale.md)。

## <a name="summary"></a>摘要

在本指南中，您已了解建立 PersonGroup 及識別人員的流程。 已說明及示範下列功能：

- 使用[臉部 - 偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) \(英文\) API 來偵測臉部。
- 使用 [PersonGroup - 建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) \(英文\) API 來建立 PersonGroup。
- 使用 [PersonGroup - 建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) \(英文\) API 來建立人員。
- 使用 [PersonGroup - 定型](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) \(英文\) API來將 PersonGroup 定型。
- 使用[臉部 - 識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) \(英文\) API，根據 PersonGroup 來識別不明的臉部。

## <a name="related-topics"></a>相關主題

- [臉部辨識概念](../concepts/face-recognition.md)
- [偵測影像中的臉部](HowtoDetectFacesinImage.md)
- [新增臉部](how-to-add-faces.md)
- [使用大規模功能](how-to-use-large-scale.md)
