---
title: 使用 Azure 媒體服務編碼時子剪輯影片
description: 本主題說明如何使用 .NET SDK 以 Azure 媒體服務編碼時子剪輯影片
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 1725fab30ddb4d40e0bf5345683588c878658052
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296561"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>使用媒體服務編碼時子剪輯影片-.NET

使用 [作業](/rest/api/media/jobs)進行編碼時，您可以修剪或子剪輯影片。 這項功能適用於使用 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 預設或 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 預設所建立的任何[轉換](/rest/api/media/transforms)。

下列 c # 範例會建立一項作業，在資產提交編碼作業時修剪其影片。 

## <a name="prerequisites"></a>必要條件

若要完成此主題中所述的步驟，您必須：

- [建立 Azure 媒體服務帳戶](./create-account-howto.md)
- 建立轉換和輸入與輸出資產。 您可以瞭解如何 [使用 .net 教學課程，在上傳、編碼和串流](stream-files-tutorial-with-api.md) 影片中建立轉換和輸入與輸出資產。
- 請參閱 [編碼概念](encoding-concept.md) 主題。

## <a name="example"></a>範例

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>後續步驟

[如何使用自訂轉換進行編碼](customize-encoder-presets-how-to.md) 
