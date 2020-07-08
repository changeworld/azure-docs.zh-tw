---
title: 內容感知編碼的預設值-Azure 媒體服務
description: 本文討論 Microsoft Azure 媒體服務 v3 中的內容感知編碼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/29/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 57a8d308955719be0d84b87fb3a23c6f510c2836
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84234888"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>使用「內容感知編碼」預設來尋找給定解析的最佳位元速率值

為了準備以彈性[位元速率串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)傳遞內容，必須以多個位元速率（高至低）編碼影片。 這可確保品質的正常降低，因為位元速率會降低，因此是影片的解析度。 這種多位元率編碼會使用所謂的編碼階梯–一份解析度和位元速率表，請參閱媒體服務[內建的編碼](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)預設值。

您應該留意正在處理的內容，並自訂/微調編碼階梯，以獲得個別影片的複雜性。 每次解決時，都有比得品質還差的位元速率，編碼器會以此最佳位元速率值運作。 下一個優化層級是根據內容來選取解析度–例如，PowerPoint 簡報的影片無法從以下的720p 中獲益。 接下來，編碼器可能會負責將影片中每個拍照的設定優化。 

Microsoft 的彈性[串流](autogen-bitrate-ladder.md)預設部分解決來源影片的品質和解析度變化的問題。 我們的客戶有不同的內容混合，有些是1080p，有些則是720p，而有些則是 SD 和較低的解析度。 此外，並非所有的來源內容都是來自電影或電視工作室的高品質 mezzanines。 彈性資料流程預設會藉由確保位元速率階梯永不超過輸入夾層的解析度或平均位元速率，來解決這些問題。 不過，此預設值不會檢查解析和位元速率以外的來源屬性。

## <a name="the-content-aware-encoding"></a>內容感知編碼 

內容感知編碼預設會藉由併入自訂邏輯來延伸「自動調整位元速率串流」機制，其可讓編碼器針對指定的解析度尋找最佳位元速率值，但不需要大量的計算分析。 這個預設值會產生一組對齊 GOP 的 Mp4。 針對任何輸入內容，此服務會執行輸入內容的初始輕量分析，並使用結果來判斷最理想的圖層數目、適當的位元速率和解析度設定，以供彈性串流傳遞。 此預設值特別適用于低和中度複雜度的影片，其中輸出檔案的位元速率會比彈性串流預設值低，但品質仍然會提供良好的檢視器體驗。 輸出會包含具有影片和音訊交錯的有案檔案

下列範例圖表顯示使用品質標準（例如[PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)和[VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)）的比較。 來源是藉由串連電影和電視節目的較短剪輯來建立，其目的是要強調編碼器。 根據定義，此預設會產生不同于內容的結果，這也表示對於某些內容，可能不會大幅降低位元速率或改善品質。

![使用 PSNR 的速率扭曲（RD）曲線](media/content-aware-encoding/msrv1.png)

**圖1：針對高複雜度來源使用 PSNR 度量的速率扭曲（RD）曲線**

![使用 VMAF 的速率扭曲（RD）曲線](media/content-aware-encoding/msrv2.png)

**圖2：對高複雜度來源使用 VMAF 度量的速率扭曲（RD）曲線**

以下是來源內容另一個類別的結果，其中的編碼器能夠判斷輸入的品質不佳（許多壓縮成品，因為低位元速率）。 請注意，使用內容感知預設值時，編碼器決定只產生一個輸出層–以較低的位元速率，讓大部分的用戶端能夠在不停止的情況下播放串流。

![使用 PSNR 的 RD 曲線](media/content-aware-encoding/msrv3.png)

**圖3：使用 PSNR 進行低品質輸入的 RD 曲線（以1080p 為限）**

![使用 VMAF 的 RD 曲線](media/content-aware-encoding/msrv4.png)

**圖4：使用 VMAF 進行低品質輸入的 RD 曲線（以1080p 為限）**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>如何使用內容感知編碼預設值 

您可以建立使用此預設值的轉換，如下所示。 

如需使用轉換輸出的教學課程，請參閱[後續步驟](#next-steps)一節。 輸出資產可以媒體服務從像是 HLS 等通訊協定中的串流端點來傳遞（如教學課程所示）。

> [!NOTE]
> 請務必使用**ContentAwareEncoding**預設值 not ContentAwareEncodingExperimental。

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
> 使用預設值的編碼作業 `ContentAwareEncoding` 會根據輸出分鐘計費。 
  
## <a name="next-steps"></a>後續步驟

* [教學課程：使用媒體服務 v3 上傳、編碼和串流影片](stream-files-tutorial-with-api.md)
* [教學課程：根據 URL 編碼遠端檔案和串流影片-REST](stream-files-tutorial-with-rest.md)
* [教學課程：根據 URL 編碼遠端檔案和串流影片-CLI](stream-files-cli-quickstart.md)
* [教學課程：根據 URL 編碼遠端檔案和串流影片-.NET](stream-files-dotnet-quickstart.md)
* [教學課程：根據 URL 編碼遠端檔案和串流影片-Node.js](stream-files-nodejs-quickstart.md)
