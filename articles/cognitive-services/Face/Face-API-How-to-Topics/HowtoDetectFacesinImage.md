---
title: 檢測圖像中的人臉 - 人臉
titleSuffix: Azure Cognitive Services
description: 本指南演示如何使用人臉檢測從給定圖像中提取性別、年齡或姿勢等屬性。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169883"
---
# <a name="get-face-detection-data"></a>獲取人臉檢測資料

本指南演示如何使用人臉檢測從給定圖像中提取性別、年齡或姿勢等屬性。 本指南中的程式碼片段通過使用 Azure 認知服務面用戶端庫以 C# 形式編寫。 相同的功能可以通過[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)獲得。

本指南介紹如何：

- 獲取圖像中人臉的位置和尺寸。
- 在圖像中獲取各種面部地標的位置，如學生、鼻子和嘴巴。
- 猜猜被檢測到的面孔的性別、年齡、情感和其他屬性。

## <a name="setup"></a>安裝程式

本指南假定您已經構建了一個[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)物件，`faceClient`名為 ，具有面容訂閱金鑰和終結點 URL。 在此處，您可以通過調用本指南中使用的[DetectWithUrlsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)或[DetectWithasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)來使用人臉檢測功能。 有關如何設置此功能的說明，請按照其中一個快速啟動。

本指南重點介紹檢測調用的細節，例如可以傳遞哪些參數以及可以對返回的資料執行哪些操作。 我們建議您僅查詢所需的功能。 每個操作都需要額外的時間才能完成。

## <a name="get-basic-face-data"></a>獲取基本人臉資料

要查找面並在圖像中獲取其位置，請調用使用_returnFaceId_參數設置為**true**的方法。 這項設定是預設值。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

您可以查詢返回的["已檢測面"](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)物件及其唯一 ID 和提供人臉圖元座標的矩形。

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

有關如何分析人臉的位置和尺寸的資訊，請參閱[Face 矩形](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)。 通常，這個矩形包含眼睛、眉毛、鼻子和嘴巴。 頭部、耳朵和下巴的頂部不一定包括在內。 要使用面矩形裁剪完整的頭部或獲取中拍肖像，也許對於照片 ID 類型圖像，可以在每個方向展開矩形。

## <a name="get-face-landmarks"></a>獲取面部地標

[面部地標](../concepts/face-detection.md#face-landmarks)是一組容易找到的面部點，如學生或鼻尖。 要獲取人臉地標資料，將_返回FaceLandmarks_參數設置為**true**。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

以下代碼演示如何檢索鼻子和學生的位置：

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

您還可以使用面地標資料來精確計算人臉的方向。 例如，您可以將面的旋轉定義為從嘴的中心到眼睛中心的向量。 以下代碼計算此向量：

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

當您知道面的方向時，可以旋轉矩形面框以更正確地對齊它。 要裁剪圖像中的面，可以以程式設計方式旋轉圖像，以便人臉始終直立顯示。

## <a name="get-face-attributes"></a>獲取人臉屬性

除了面矩形和地標之外，人臉檢測 API 還可以分析人臉的多個概念屬性。 有關完整清單，請參閱["面"屬性](../concepts/face-detection.md#attributes)概念部分。

要分析面屬性，請將_returnFaceAttributes_參數設置為[FaceAttributes 類型枚舉](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)值清單。

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

然後，獲取對返回資料的引用，並根據您的需要執行更多操作。

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

要瞭解有關每個屬性的更多內容，請參閱[面檢測和屬性](../concepts/face-detection.md)概念指南。

## <a name="next-steps"></a>後續步驟

在本指南中，您學習了如何使用人臉檢測的各種功能。 接下來，通過遵循深入教程將這些功能集成到應用中。

- [教學課程：建立 WPF 應用程式來顯示影像中的臉部資料](../Tutorials/FaceAPIinCSharpTutorial.md)
- [教學課程：建立 Android 應用程式來偵測並框出影像中的臉部](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>相關主題

- [參考文檔](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [參考文檔 （.NET SDK）](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)