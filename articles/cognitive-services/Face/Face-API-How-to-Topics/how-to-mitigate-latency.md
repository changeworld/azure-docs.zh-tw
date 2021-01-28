---
title: 如何在使用臉部辨識服務時降低延遲
titleSuffix: Azure Cognitive Services
description: 瞭解如何在使用臉部辨識服務時降低延遲。
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: e7f4293955772697ddeea5fce9daac4b04755274
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937281"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>如何：在使用臉部辨識服務時降低延遲

使用臉部辨識服務時，您可能會遇到延遲。 延遲指的是在網路上通訊時所發生的任何一種延遲。 一般來說，延遲的可能原因包括：
- 每個封包的實體距離必須從來源傳送到目的地。
- 傳輸媒體的問題。
- 路由器或交換器中的錯誤（沿著傳輸路徑）。
- 防毒程式、防火牆和其他安全性機制所需的時間，以檢查封包。
- 用戶端或伺服器應用程式發生故障。

本主題討論使用 Azure 認知服務的特定延遲可能原因，以及如何緩和這些原因。

> [!NOTE]
> Azure 認知服務不會提供任何有關延遲的服務等級協定 (SLA) 。

## <a name="possible-causes-of-latency"></a>延遲的可能原因

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>認知服務與遠端 URL 之間的連接緩慢

某些 Azure 認知服務會提供方法，以從您提供的遠端 URL 取得資料。 例如，當您呼叫臉部服務的 [DetectWithUrlAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) 時，您可以指定服務嘗試偵測臉部的影像 URL。

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

然後，臉部辨識服務必須從遠端伺服器下載映射。 如果從臉部服務到遠端伺服器的連線速度很慢，則會影響偵測方法的回應時間。

若要減輕此問題，請考慮 [將映射儲存在 Azure Premium Blob 儲存體中](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet)。 例如：

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>大上傳大小

某些 Azure 認知服務會提供方法，以從您上傳的檔案取得資料。 例如，當您呼叫臉部服務的 [>faceclient.face.detectwithstreamasync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) 時，您可以上傳服務嘗試用來偵測臉部的影像。

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

如果要上傳的檔案很大，這會影響方法的回應時間 `DetectWithStreamAsync` ，原因如下：
- 上傳檔案需要較長的時間。
- 服務會花更長的時間來處理檔案，與檔案大小成正比。

緩和措施：
- 考慮 [將映射儲存在 Azure Premium Blob 儲存體中](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet)。 例如：
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- 請考慮上傳較小的檔案。
    - 請參閱關於臉部偵測和輸入資料的 [輸入資料](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-detection#input-data) 的指導方針， [以進行臉部](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-recognition#input-data)辨識。
    - 針對臉部偵測，使用偵測模型時 `DetectionModel.Detection01` ，減少影像檔案大小會增加處理速度。 使用偵測模型時 `DetectionModel.Detection02` ，如果影像檔案小於1920x1080，減少影像檔案大小只會增加處理速度。
    - 針對臉部辨識，將臉部大小減少為200x200 圖元不會影響辨識模型的精確度。
    - 和方法的效能 `DetectWithUrlAsync` `DetectWithStreamAsync` 也取決於影像中有多少臉部。 臉部辨識服務最多可傳回影像的100臉部。 臉部會依臉部矩形大小從大到小進行排名。
    - 如果您需要呼叫多個服務方法，請考慮在您的應用程式設計允許時，以平行方式呼叫它們。 例如，如果您需要偵測兩個影像中的臉部以執行臉部比較：
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>計算資源與臉部服務之間的連接緩慢

如果您的電腦連線到臉部辨識服務的速度很慢，則會影響服務方法的回應時間。

緩和措施：
- 當您建立臉部訂用帳戶時，請務必選擇最接近應用程式託管位置的區域。
- 如果您需要呼叫多個服務方法，請考慮在您的應用程式設計允許時，以平行方式呼叫它們。 如需範例，請參閱上一節。

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何在使用臉部辨識服務時降低延遲。 接下來，瞭解如何將現有的 PersonGroup 和 FaceList 物件擴大至 LargePersonGroup 和 LargeFaceList 物件。

> [!div class="nextstepaction"]
> [範例：使用大型功能](how-to-use-large-scale.md)

## <a name="related-topics"></a>相關主題

- [ (REST) 的參考檔 ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [ ( .NET SDK) 的參考檔 ](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
