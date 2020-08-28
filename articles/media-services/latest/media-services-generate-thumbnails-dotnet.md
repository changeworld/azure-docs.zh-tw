---
title: 如何搭配 .NET 使用 Azure 媒體服務編碼器標準產生縮圖
description: 本文說明如何使用 .NET 來編碼資產，並同時使用媒體編碼器標準產生縮圖。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/10/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 89b640e1a23123fcac335ee0bf02ef078227e633
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016804"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>如何使用搭配 .NET 的編碼器標準產生縮圖

您可以使用媒體編碼器標準，以 [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 或 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 影像檔案格式，從您的輸入視訊中產生一或多個縮圖。

## <a name="recommended-reading-and-practice"></a>建議閱讀和練習

建議您先閱讀 [如何使用自訂轉換進行編碼-.net](customize-encoder-presets-how-to.md)，以熟悉自訂轉換。

## <a name="transform-code-example"></a>轉換程式碼範例

下列程式碼範例只會建立縮圖。  您應該設定下列參數：

- **開始** -輸入影片中開始產生縮圖的位置。 此值可為 ISO 8601 格式 (例如，PT05S 從5秒開始) 或框架計數 (例如，10開始于第10個框架) ，或的相對值，以串流期間的10% 開始 (。 也支援宏 {最佳}，它會指示編碼器從影片的前幾秒內選取最適合的縮圖，而且只會產生一個縮圖，不論步驟和範圍有哪些其他設定。 預設值為宏 {最佳}。
- **步驟** -產生縮圖的間隔。 此值可為 ISO 8601 格式 (例如，每隔5秒為一個影像 PT05S) ，或一個框架計數 (例如，每30個畫面) 有一個影像的30個，或是一個影像的相對值 (例如，每隔10% 的串流持續時間) 為一個影像10%。 步驟值會影響第一個產生的縮圖，而這可能不是轉換預設開始時間所指定的縮圖。 這是因為編碼器，它會嘗試選取開始時間與開始時間之間的最大縮圖做為第一個輸出。 因為預設值為10%，這表示如果資料流程的持續時間很長，則第一個產生的縮圖可能遠遠離開始時指定的縮圖。 如果預期第一個縮圖接近開始時間，請嘗試為步驟選取合理的值，如果在開始時只需要一個縮圖，請將範圍值設定為1。
- **範圍** -相對於轉換輸入影片中要停止產生縮圖的相對位置。 此值可為 ISO 8601 格式 (例如，PT5M30S 從開始時間的5分鐘和30秒停止) 或框架計數 (例如300在開始時間從畫面格停止300的畫面格。 如果這個值是1，則表示只會在開始時間) 產生一個縮圖，或在資料流程持續期間產生相對值 (例如，50% 從開始時間) 的一半串流期間停止。 預設值為100%，表示要在資料流程的結尾停止。
- **圖層** -編碼器所產生的輸出影像圖層集合。

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>後續步驟
[使用 REST 產生縮圖](media-services-generate-thumbnails-rest.md)
