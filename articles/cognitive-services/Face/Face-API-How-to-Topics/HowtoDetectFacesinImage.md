---
title: 偵測影像中的臉部-臉部
titleSuffix: Azure Cognitive Services
description: 本指南示範如何使用臉部偵測來將指定影像中的性別、年齡或姿勢等屬性解壓縮。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: f7a740b1015bda80000f65180eda2c5e618670da
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911234"
---
# <a name="get-face-detection-data"></a>取得臉部偵測資料

本指南示範如何使用臉部偵測來將指定影像中的性別、年齡或姿勢等屬性解壓縮。 本指南中的程式碼片段是使用 Azure 認知服務臉部用戶端程式庫以 c # 撰寫。 您可以透過 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)取得相同的功能。

本指南說明如何：

- 取得影像中臉部的位置和維度。
- 取得影像中各種臉部地標的位置，例如瞳孔、鼻子和嘴。
- 猜測所偵測到臉部的性別、年齡、表情和其他屬性。

## <a name="setup"></a>安裝程式

本指南假設您已[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) `faceClient` 使用臉部訂用帳戶金鑰和端點 URL 來建立名為的 FaceClient 物件。 從這裡開始，您可以藉由呼叫本指南中使用的 [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)或 [>faceclient.face.detectwithstreamasync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)，來使用臉部偵測功能。 如需如何設定這項功能的指示，請遵循其中一個快速入門。

本指南著重于偵測呼叫的詳細資料，例如您可以傳遞的引數，以及您可以如何處理傳回的資料。 建議您只查詢所需的功能。 每項作業需要額外的時間才能完成。

## <a name="get-basic-face-data"></a>取得基本的臉部資料

若要尋找臉部並取得影像中的位置，請呼叫 [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet) 或 [>faceclient.face.detectwithstreamasync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) 方法，並將 _returnFaceId_ 參數設定為 **true** 。 這是預設設定。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

您可以針對其唯一識別碼和提供臉部圖元座標的矩形，查詢傳回的 [>detectedface](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) 物件。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

如需如何剖析臉部位置和維度的詳細資訊，請參閱 [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)。 這個矩形通常會包含眼睛、眉毛、鼻子和嘴。 Head、耳和下巴的頂端不一定會包含在內。 若要使用臉部矩形裁剪整個 head 或取得中間的直向（也許是相片識別碼類型影像），您可以展開每個方向的矩形。

## <a name="get-face-landmarks"></a>取得臉部地標

[臉部地標](../concepts/face-detection.md#face-landmarks) 是臉部的一組容易尋找的點，例如瞳孔或鼻子的秘訣。 若要取得臉部地標資料，請將 _detectionModel_ 參數設定為 **detectionModel. Detection01** ，並將 _returnFaceLandmarks_ 參數設定為 **true** 。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

下列程式碼會示範如何取出鼻子和瞳孔的位置：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

您也可以使用臉部地標資料來精確地計算臉部的方向。 例如，您可以將臉部的旋轉定義為從嘴中央到眼睛中心的向量。 下列程式碼會計算這個向量：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

當您知道臉部的方向時，可以旋轉矩形臉部框架來更正確地對齊。 若要裁剪影像中的臉部，您可以用程式設計方式旋轉影像，讓臉部一律以直立的方式出現。

## <a name="get-face-attributes"></a>取得臉部屬性

除了臉部矩形和地標，臉部偵測 API 還可以分析臉部的數個概念屬性。 如需完整清單，請參閱 [臉部屬性](../concepts/face-detection.md#attributes) 概念一節。

若要分析臉部屬性，請將 _detectionModel_ 參數設定為 **detectionModel Detection01** ，並將 _ReturnFaceAttributes_ 參數設定為 [FaceAttributeType 列舉](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) 值的清單。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

然後，取得所傳回資料的參考，並根據您的需求進行更多作業。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

若要深入瞭解每個屬性，請參閱 [臉部偵測和屬性](../concepts/face-detection.md) 概念指南。

## <a name="next-steps"></a>下一步

在本指南中，您已瞭解如何使用臉部偵測的各種功能。 接下來，請遵循深入的教學課程，將這些功能整合到您的應用程式中。

- [教學課程：建立 WPF 應用程式來顯示影像中的臉部資料](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>相關主題

- [ (REST) 的參考檔 ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [ ( .NET SDK) 的參考檔 ](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)