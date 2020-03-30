---
title: 內容感知編碼的預設 - Azure 媒體服務
description: 本文討論 Microsoft Azure 媒體服務 v3 中的內容感知編碼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772134"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>使用內容感知編碼預設查找給定解析度的最佳位元速率值

為了準備通過[自我調整位元速率流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)交付的內容，視頻需要以多個位元速率（高到低）進行編碼。 這可確保品質正常下降，因為位元速率降低，視頻的解析度也降低了。 這種多位元率編碼使用所謂的編碼階梯 -解析度和位元速率表，請參閱媒體服務[內置編碼預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)。

您應該瞭解正在處理的內容，並自訂/調整編碼梯以適應各個視頻的複雜性。 在每個解析度下，都有一個位元速率，超過此點率，品質的任何提高都是不感知的 - 編碼器以這個最佳位元速率值運行。 下一個優化級別是根據內容選擇解析度，例如，PowerPoint 演示文稿的視頻不會從低於 720p 中受益。 更進一步，編碼器可以負責優化視頻中每個鏡頭的設置。 

Microsoft 的[自我調整流式處理](autogen-bitrate-ladder.md)預設部分解決了源視頻的品質和解析度的可變性問題。 我們的客戶有不同的內容組合，有些在1080p，其他在720p，一些在SD和低解析度。 此外，並非所有源內容是來自電影或電視製片廠的高品質夾層。 自我調整流式處理預設通過確保位元速率梯級數永遠不會超過輸入夾層的解析度或平均位元速率來解決這些問題。 但是，此預設不會檢查解析度和位元速率以外的源屬性。

## <a name="the-content-aware-encoding"></a>內容感知編碼 

內容感知編碼預設擴展了"自我調整位元速率流"機制，通過合併自訂邏輯，使編碼器能夠為給定解析度尋求最佳位元速率值，但無需進行廣泛的計算分析。 此預設生成一組與 GOP 對齊的 MP4。 給定任何輸入內容，該服務對輸入內容執行初始羽量級分析，並使用結果確定最佳層數、適當的位元速率和解析度設置，以便通過自我調整流式處理進行傳遞。 此預設對於中低複雜視頻特別有效，其中輸出檔案的位元速率將低於自我調整流式處理預設，但品質仍可為觀看者提供良好的體驗。 輸出將包含帶有視頻和音訊交錯的 MP4 檔

以下示例圖顯示了使用諸如[PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)和[VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)等品質指標的比較。 源是通過串聯電影和電視節目中高複雜鏡頭的短剪輯創建的，旨在強調編碼器。 根據定義，此預設會產生因內容而異的結果 - 這也意味著，對於某些內容，位元速率或品質可能不會顯著降低。

![使用 PSNR 的速率失真 （RD） 曲線](media/content-aware-encoding/msrv1.png)

**圖 1：使用 PSNR 指標實現高複雜性源的速率失真 （RD） 曲線**

![使用 VMAF 的速率失真 （RD） 曲線](media/content-aware-encoding/msrv2.png)

**圖 2：使用 VMAF 指標實現高複雜性源的速率失真 （RD） 曲線**

下面是另一類源內容的結果，其中編碼器能夠確定輸入品質較差（許多壓縮偽影由於位元速率低）。 請注意，使用內容感知預設時，編碼器決定只生成一個輸出層 - 位元速率足夠低，以便大多數用戶端能夠在不停滯的情況下播放流。

![使用 PSNR 的 RD 曲線](media/content-aware-encoding/msrv3.png)

**圖 3：使用 PSNR 進行低品質輸入的 RD 曲線（在 1080p 時）**

![使用 VMAF 的 RD 曲線](media/content-aware-encoding/msrv4.png)

**圖 4：使用 VMAF 進行低品質輸入的 RD 曲線（在 1080p 時）**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>如何使用內容感知編碼預設 

您可以創建使用此預設的轉換，如下所示。 

> [!TIP]
> 有關使用表單輸出的教程，請參閱[後續步驟](#next-steps)部分。 輸出資產可以從媒體服務流端點（如 MPEG-DASH 和 HLS）（如教程中所示）提供。


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
> 使用預設對`ContentAwareEncoding`作業進行編碼，並根據輸出分鐘數計費。 

## <a name="next-steps"></a>後續步驟

* [教程：使用媒體服務 v3 上傳、編碼和資料流視頻](stream-files-tutorial-with-api.md)
* [教程：基於 URL 對遠端檔進行編碼並資料流視頻 - REST](stream-files-tutorial-with-rest.md)
* [教程：基於 URL 對遠端檔進行編碼並資料流視頻 - CLI](stream-files-cli-quickstart.md)
* [教程：基於 URL 對遠端檔進行編碼並資料流視頻 - .NET](stream-files-dotnet-quickstart.md)
* [教程：基於 URL 對遠端檔進行編碼並資料流視頻 - Node.js](stream-files-nodejs-quickstart.md)
