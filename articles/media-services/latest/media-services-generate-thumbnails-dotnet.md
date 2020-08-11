---
title: 如何使用 Azure 媒體服務編碼器標準搭配 .NET 產生縮圖
description: 本文說明如何使用 .NET 來編碼資產，並使用媒體編碼器標準同時產生縮圖。
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
ms.openlocfilehash: f6a432ad30fa3ce24693a00478f2194caaa11ff9
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068097"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>如何使用編碼器標準搭配 .NET 產生縮圖

您可以使用媒體編碼器標準，以 [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 或 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 影像檔案格式，從您的輸入視訊中產生一或多個縮圖。

## <a name="recommended-reading-and-practice"></a>建議的閱讀與實務

建議您透過閱讀[如何使用自訂轉換進行編碼，以](customize-encoder-presets-how-to.md)熟悉自訂轉換-.net。

## <a name="transform-code-example"></a>轉換程式碼範例

下列程式碼範例只會建立縮圖。  您應該設定下列參數：

- **開始**-輸入影片中開始產生縮圖的位置。 此值可以是 ISO 8601 格式 (例如，從5秒開始的 PT05S) 或框架計數 (例如，10從10個畫面格開始) ，或使用相對值來串流持續時間 (例如，10% 從串流持續時間的10% 開始) 。 也支援宏 {最佳}，這會指示編碼器從影片的前幾秒內選取最佳縮圖，而且只會產生一個縮圖，無論步驟和範圍的其他設定為何。 預設值為宏 {最佳}。
- **step** -產生縮圖的間隔。 此值可以是 ISO 8601 格式 (例如，每5秒) 一個影像的 PT05S，或一個框架計數 (例如，每隔30個畫面) 一個影像30，或一個相對值來串流持續時間 (例如，每隔10% 的串流持續時間) 一個影像10%。 步驟值會影響第一個產生的縮圖，而這可能不是轉換預設開始時間所指定。 這是因為編碼器所造成，它會嘗試選取開始時間和步驟位置之間的最佳縮圖做為第一個輸出。 由於預設值是10%，因此，如果資料流程的持續時間很長，第一個產生的縮圖可能會遠離在開始時間指定的縮圖。 如果第一個縮圖應接近開始時間，請嘗試為 [步驟] 選取合理的值，或在開始時間只需要一個縮圖時，將 [範圍] 值設定為 [1]。
- **範圍**-在輸入影片中相對於轉換預設開始時間的位置，以停止產生縮圖。 此值可以是 ISO 8601 格式 (例如，PT5M30S 會從開始時間) 的5分鐘和30秒停止，或框架計數 (例如，300在開始時間從框架的300框架停止。 如果這個值是1，則表示只會在開始時產生一個縮圖) ，或是在資料流程持續時間的相對值 (例如，50% 在開始時間) 的一半資料流程期間停止。 預設值為100%，表示在資料流程結尾處停止。
- **圖層**-由編碼器產生的輸出影像層集合。

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
