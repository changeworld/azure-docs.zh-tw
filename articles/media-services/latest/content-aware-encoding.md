---
title: 內容感知編碼的預設值
description: 本文討論 Microsoft Azure 媒體服務 v3 中的內容感知編碼。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c63ada9621fc1485742a7683fd6742f0e896854
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954424"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>使用內容感知編碼預設值，為指定的解決方式找出最佳位元速率值

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

為了準備以彈性 [位元速率串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)傳遞的內容，必須以多個位元速率編碼的影片 (高至低) 。 這可確保品質的正常降低，因為位元速率會降低，因此是影片的解析度。 這種多重位元速率編碼方式會使用所謂的編碼階梯：解析度和位元速率的表格，請參閱媒體服務 [內建編碼](/rest/api/media/transforms/createorupdate#encodernamedpreset)預設值。

您應該留意您正在處理的內容，並自訂/調整編碼階梯，以因應個別影片的複雜度。 在每個解析度中，都有一個位元速率增加的位元速率，也就是不會感知品質，編碼器會以這個最理想的位元速率值運作。 下一層的優化是根據內容選取解析度，例如，PowerPoint 簡報的影片無法從下720p 開始獲益。 此外，編碼器可能會負責將影片內每個拍照的設定優化。 

Microsoft 的彈性 [串流處理](autogen-bitrate-ladder.md) 預設部分解決來源影片的品質與解析度變化問題。 我們的客戶有不同的內容混合，有些在1080p，有些是720p，還有一些則是 SD 和較低的解析度。 此外，並非所有來源內容都是來自電影或電視工作室的高品質 mezzanines。 彈性資料流程預設會解決這些問題，方法是確保位元速率階梯永遠不超過輸入夾層的解析度或平均位元速率。 不過，此預設值不會檢查解析和位元速率以外的來源屬性。

## <a name="the-content-aware-encoding"></a>內容感知編碼 

內容感知編碼預設值會擴充「彈性位元速率串流」機制，方法是併入自訂邏輯，讓編碼器搜尋指定解析度的最佳位元速率值，但不需要大量計算分析。 此預設會產生一組對齊 GOP 的 Mp4。 針對輸入內容，此服務會執行輸入內容的初始輕量分析，並使用結果來判斷最佳的圖層數目、適當的位元速率和解析度設定，以透過彈性串流傳遞。 此預設值特別適用于低和中度複雜度的影片，其中輸出檔案的位元速率會比調適型串流預設值低，但是品質仍能為檢視器提供良好的體驗。 輸出中會包含具有影片和音訊交錯的檔案

下列範例圖形顯示使用品質計量（例如 [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) 和 [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)）的比較。 來源的建立方式，是從電影和電視節目串連高複雜性的短片，以強調編碼器的壓力。 根據定義，此預設會產生因內容而異的結果，也就是在某些內容中，品質的位元速率或改進可能不會大幅降低。

![使用 PSNR (RD) 曲線的速率扭曲](media/content-aware-encoding/msrv1.png)

**圖1：針對高複雜度來源使用 PSNR 度量的速率扭曲 (RD) 曲線**

![使用 VMAF (RD) 曲線的速率扭曲](media/content-aware-encoding/msrv2.png)

**圖2：針對高複雜度來源使用 VMAF 度量的速率扭曲 (RD) 曲線**

以下是另一個來源內容類別別的結果，其中的編碼器可以判斷輸入品質品質不佳 (許多壓縮成品，因為較低位元速率) 。 請注意，在內容感知的預設情況下，編碼器決定只產生一個輸出層–低位元速率低，因此大部分的用戶端都能在不拖延的情況下播放串流。

![使用 PSNR 的 RD 曲線](media/content-aware-encoding/msrv3.png)

**圖3：使用 PSNR 進行低品質輸入的 RD 曲線 (1080p)**

![使用 VMAF 的 RD 曲線](media/content-aware-encoding/msrv4.png)

**圖4：使用 VMAF 進行低品質輸入的 RD 曲線 (1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>如何使用內容感知編碼預設值 

您可以建立使用此預設值的轉換，如下所示。 

如需使用轉換輸出的教學課程，請參閱 [後續步驟](#next-steps) 一節。 輸出資產可以從媒體服務串流端點（例如 MPEG 和 HLS (）提供，如教學課程) 所示。

> [!NOTE]
> 請務必使用未 ContentAwareEncodingExperimental 的 **ContentAwareEncoding** 預設值。

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> 使用預設值的編碼作業 `ContentAwareEncoding` 會依據輸出分鐘數計費。 
  
## <a name="next-steps"></a>下一步

* [教學課程：使用媒體服務 v3 上傳、編碼和串流影片](stream-files-tutorial-with-api.md)
* [教學課程：編碼以 URL 為基礎的遠端檔案及串流處理影片 - REST](stream-files-tutorial-with-rest.md)
* [教學課程：編碼以 URL 為基礎的遠端檔案及串流影片-CLI](stream-files-cli-quickstart.md)
* [教學課程：編碼以 URL 為基礎的遠端檔案及串流處理影片 - .NET](stream-files-dotnet-quickstart.md)
* [教學課程：編碼以 URL 為基礎的遠端檔案及串流處理影片 - Node.js](stream-files-nodejs-quickstart.md)
