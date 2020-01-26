---
title: 內容感知編碼的預設值-Azure 媒體服務 |
description: 本文討論 Microsoft Azure 媒體服務 v3 中的內容感知編碼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 34b4386d536a9845c8a48e7e54612548367b52bc
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759341"
---
# <a name="a-preset-for-content-aware-encoding"></a>內容感知編碼的預設值

為了準備以彈性[位元速率串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)傳遞內容，必須以多個位元速率（高至低）編碼影片。 為了確保品質的效能降低，因為位元速率降低，因此是影片的解析度。 這會導致所謂的編碼階梯–一份解析度和位元速率表;請參閱媒體服務[內建的編碼](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)預設值。

## <a name="overview"></a>概觀

在 Netflix 于2015年12月發佈其[blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2)之後，移至超過一種全功能全影片的方法時感興趣。 從那時起，已在 marketplace 中發行多個內容感知編碼解決方案;如需總覽，請參閱[這篇文章](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx)。 其概念是要留意內容，以自訂或微調編碼階梯，以因應個別影片的複雜度。 每次解決時，都有比得品質還差的位元速率，編碼器會以此最佳位元速率值運作。 下一個優化層級是根據內容來選取解析度–例如，PowerPoint 簡報的影片無法從以下的720p 中獲益。 接下來，編碼器可能會負責將影片中每個拍照的設定優化。 Netflix 在2018中描述[了這種方法](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830)。

在2017年初，Microsoft 發行了彈性[資料流程](autogen-bitrate-ladder.md)預設值，以解決來源影片的品質和解析度變化的問題。 我們的客戶有不同的內容混合，有些是1080p，有些則是720p，而有些則是 SD 和較低的解析度。 此外，並非所有的來源內容都是來自電影或電視工作室的高品質 mezzanines。 彈性資料流程預設會藉由確保位元速率階梯永不超過輸入夾層的解析度或平均位元速率，來解決這些問題。

新的內容感知編碼預設會藉由併入自訂邏輯來擴充該機制，讓編碼器能夠搜尋指定解決方案的最佳位元速率值，但不需要大量的計算分析。 這個預設值會產生一組對齊 GOP 的 Mp4。 針對任何輸入內容，此服務會執行輸入內容的初始輕量分析，並使用結果來判斷最理想的圖層數目、適當的位元速率和解析度設定，以供彈性串流傳遞。 此預設值特別適用于低和中度複雜度的影片，其中輸出檔案的位元速率會比彈性串流預設值低，但品質仍然會提供良好的檢視器體驗。 輸出會包含具有影片和音訊交錯的有案檔案

請參閱下列範例圖形，其中顯示使用品質標準（例如[PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)和[VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)）的比較。 來源是藉由串連電影和電視節目的較短剪輯來建立，其目的是要強調編碼器。 根據定義，此預設會產生不同于內容的結果，這也表示對於某些內容，可能不會大幅降低位元速率或改善品質。

![使用 PSNR 的速率扭曲（RD）曲線](media/cae-experimental/msrv1.png)

**圖1：針對高複雜度來源使用 PSNR 度量的速率扭曲（RD）曲線**

![使用 VMAF 的速率扭曲（RD）曲線](media/cae-experimental/msrv2.png)

**圖2：對高複雜度來源使用 VMAF 度量的速率扭曲（RD）曲線**

以下是來源內容另一個類別的結果，其中的編碼器能夠判斷輸入的品質不佳（許多壓縮成品，因為低位元速率）。 請注意，使用內容感知預設值時，編碼器決定只產生一個輸出層–以較低的位元速率，讓大部分的用戶端能夠在不停止的情況下播放串流。

![使用 PSNR 的 RD 曲線](media/cae-experimental/msrv3.png)

**圖3：使用 PSNR 進行低品質輸入的 RD 曲線（1080p）**

![使用 VMAF 的 RD 曲線](media/cae-experimental/msrv4.png)

**圖4：使用 VMAF 進行低品質輸入的 RD 曲線（1080p）**

## <a name="use-the-experimental-preset"></a>使用實驗性預設值

您可以建立使用此預設值的轉換，如下所示。 如果使用如[這類](stream-files-tutorial-with-api.md)的教學課程，您可以更新程式碼，如下所示：

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
> 基礎演算法會受到進一步的改進。 有可能會隨著時間而改變，以用於產生位元速率 ladders，其目標是提供健全的演算法，並適應各種不同的輸入條件。 使用此預設值的編碼作業仍會根據輸出分鐘數計費，而輸出資產可從我們的串流端點以破折號和 HLS 等通訊協定傳遞。

## <a name="next-steps"></a>後續步驟

現在您已瞭解將影片優化的這個新選項，我們邀請您試試看。您可以使用本文結尾的連結傳送意見反應給我們。
