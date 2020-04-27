---
title: 使用 Azure 媒體服務進行編碼時子剪輯影片
description: 本主題說明如何使用 .NET SDK 以 Azure 媒體服務編碼時子剪輯影片
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "67304995"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>使用媒體服務進行編碼時子剪輯影片-.NET

使用[作業](https://docs.microsoft.com/rest/api/media/jobs)進行編碼時，您可以修剪或子剪輯影片。 這項功能適用于使用 [ [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ] 預設值或 [ [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) ] 預設建立的任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)。

下列 c # 範例會建立一個作業，在資產提交編碼工作時修剪該影片。 

## <a name="prerequisites"></a>先決條件

若要完成此主題中所述的步驟，您必須：

- [建立 Azure 媒體服務帳戶](create-account-cli-how-to.md)
- 建立轉換和輸入和輸出資產。 您可以在[使用 .net 的上傳、編碼和串流](stream-files-tutorial-with-api.md)影片教學課程中，瞭解如何建立轉換和輸入和輸出資產。
- 請參閱[編碼概念](encoding-concept.md)主題。

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
