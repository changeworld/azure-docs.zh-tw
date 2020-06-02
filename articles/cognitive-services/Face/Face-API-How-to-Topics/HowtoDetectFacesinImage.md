---
title: 偵測影像中的臉部-臉部
titleSuffix: Azure Cognitive Services
description: 本指南示範如何使用臉部偵測來從指定的影像中，將性別、年齡或姿勢等屬性解壓縮。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: a3fd308ede17f510b3e9188f2bebc93a08a61181
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263171"
---
# <a name="get-face-detection-data"></a>取得臉部偵測資料

本指南示範如何使用臉部偵測來從指定的影像中，將性別、年齡或姿勢等屬性解壓縮。 本指南中的程式碼片段是使用 Azure 認知服務臉部用戶端程式庫以 c # 撰寫。 相同的功能可透過[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)取得。

本指南說明如何：

- 取得影像中臉部的位置和維度。
- 取得影像中各種臉部地標的位置，例如瞳孔、鼻子和嘴。
- 猜測所偵測到臉部的性別、年齡、表情和其他屬性。

## <a name="setup"></a>安裝程式

本指南假設您已[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) `faceClient` 使用臉部訂用帳戶金鑰和端點 URL 來建立名為的 FaceClient 物件。 從這裡開始，您可以藉由呼叫本指南中所使用的[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)或[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)，來使用臉部偵測功能。 如需有關如何設定此功能的指示，請遵循其中一個快速入門。

本指南著重于偵測呼叫的細節，例如您可以傳遞的引數，以及您可以對傳回的資料執行哪些動作。 我們建議您只查詢所需的功能。 每項作業需要額外的時間才能完成。

## <a name="get-basic-face-data"></a>取得基本臉部資料

若要尋找臉部並取得其在影像中的位置，請呼叫方法，並將_returnFaceId_參數設定為**true**。 這項設定是預設值。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

您可以查詢傳回的[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)物件的唯一識別碼，以及提供臉部圖元座標的矩形。

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

如需有關如何剖析臉部位置和維度的詳細資訊，請參閱[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)。 此矩形通常包含眼睛、眉毛、鼻子和嘴。 不一定包含 head、耳和下巴的頂端。 若要使用臉部矩形裁剪完整的標頭，或取得相片識別碼類型影像的中間垂直，您可以在每個方向展開矩形。

## <a name="get-face-landmarks"></a>取得臉部地標

[臉部地標](../concepts/face-detection.md#face-landmarks)是一組表面上容易尋找的點，例如鼻子的瞳孔或刀尖。 若要取得臉部地標資料，請將_returnFaceLandmarks_參數設定為**true**。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

下列程式碼示範如何取得鼻子和瞳孔的位置：

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

您也可以使用臉部地標資料，準確地計算臉部的方向。 例如，您可以將臉部的旋轉定義為從嘴中心到眼睛中心的向量。 下列程式碼會計算此向量：

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

當您知道臉部的方向時，可以旋轉矩形臉部框架，使其更適當地對齊。 若要裁剪影像中的臉部，您可以透過程式設計方式旋轉影像，讓臉部一律呈直立的外觀。

## <a name="get-face-attributes"></a>取得臉部屬性

除了臉部矩形和地標，臉部偵測 API 還可以分析臉部的數個概念屬性。 如需完整清單，請參閱[臉部屬性](../concepts/face-detection.md#attributes)概念一節。

若要分析臉部屬性，請將_returnFaceAttributes_參數設定為[FaceAttributeType 列舉](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)值的清單。

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

然後，取得所傳回資料的參考，並根據您的需求執行更多作業。

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

若要深入瞭解每個屬性，請參閱[臉部偵測和屬性](../concepts/face-detection.md)概念指南。

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何使用臉部偵測的各種功能。 接下來，請遵循深入教學課程，將這些功能整合到您的應用程式中。

- [教學課程：建立 WPF 應用程式來顯示影像中的臉部資料](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>相關主題

- [參考檔（REST）](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [參考檔（.NET SDK）](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)