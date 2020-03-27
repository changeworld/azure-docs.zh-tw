---
title: 使用 HeadPose 屬性
titleSuffix: Azure Cognitive Services
description: 了解如何使用 HeadPose 屬性自動旋轉臉部矩形或偵測影片摘要中的頭部姿勢。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76169794"
---
# <a name="use-the-headpose-attribute"></a>使用 HeadPose 屬性

在本指南中，您將了解如何使用偵測到的臉部 HeadPose 屬性來啟用一些主要案例。

## <a name="rotate-the-face-rectangle"></a>旋轉臉部矩形

臉部矩形會連同每個偵測到的臉部一起傳回，並標示影像中臉部的位置和大小。 根據預設，矩形一律會與影像對齊 (其側邊會以垂直和水平呈現)；這時，非正臉可能會難以框出。 如果您想要以程式設計方式裁切影像中的臉部，最好能夠旋轉矩形以進行裁剪。

[認知服務臉部 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 範例應用程式會使用 HeadPose 屬性來旋轉其偵測到的臉部矩形。

### <a name="explore-the-sample-code"></a>探討範例程式碼

您可以使用 HeadPose 屬性，以程式設計方式旋轉臉部矩形。 如果您在偵測臉部時指定此屬性 (請參閱[如何偵測臉部](HowtoDetectFacesinImage.md))，您將能夠在稍後進行查詢。 [認知服務臉部 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 應用程式的下列方法會取得 **DetectedFace** 物件的清單，並傳回 **[臉部](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** 物件清單。 此處的**臉部**是一個用來儲存臉部資料的自訂類別，包括更新的矩形座標。 系統會計算**上**、**左**、**寬**和**高**的新值，且新的欄位 **FaceAngle** 會指定旋轉值。

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>顯示更新的矩形

在此，您可以在顯示中使用傳回的**臉部**物件。 [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) 中的以下幾行說明如何從這項資料轉譯新的矩形：

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>偵測頭部姿勢

您可以藉由即時追蹤 HeadPose 的變更來偵測頭部姿勢，例如點頭和搖頭。 您可以使用這項功能作為自訂的活動偵測器。

活動偵測是指判斷某個物體是人體 (而非影像或影片呈現) 的工作。 頭部姿勢偵測器可作為協助驗證活動的一種方式，特別是相對於人體的影像呈現。

> [!CAUTION]
> 若要即時偵測頭部姿勢，您必須以較高的頻率呼叫臉部 API (每秒一次以上)。 如果您使用的是免費層 (f0) 訂用帳戶，則無法進行此工作。 如果您具有付費層訂用帳戶，請確定您已計算對頭部姿勢偵測進行快速 API 呼叫所需的成本。

如需頭部姿勢偵測的實用範例，請參閱 GitHub 上的[臉部 HeadPose 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample)。

## <a name="next-steps"></a>後續步驟

如需旋轉臉部矩形的實用範例，請參閱 GitHub 上的[認知服務使用 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 應用程式。 或者，請參閱[臉部 HeadPose 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples)應用程式，此應用程式會即時追蹤 HeadPose 屬性以偵測頭部移動。