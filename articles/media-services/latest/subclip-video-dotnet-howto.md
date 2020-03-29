---
title: 使用 Azure 媒體服務進行編碼時對視頻進行子剪輯
description: 本主題介紹如何使用 .NET SDK 使用 Azure 媒體服務進行編碼時對視頻進行子剪輯
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67304995"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>使用媒體服務進行編碼時，對視頻進行子剪輯 - .NET

您可以使用[作業](https://docs.microsoft.com/rest/api/media/jobs)對其進行編碼時修剪或子剪輯視頻。 此功能適用于使用[內置標準編碼預設預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)或[標準編碼預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)構建的任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)。

以下 C# 示例創建一個作業，該作業在提交編碼作業時修剪資產中的視頻。 

## <a name="prerequisites"></a>Prerequisites

若要完成此主題中所述的步驟，您必須：

- [建立 Azure 媒體服務帳戶](create-account-cli-how-to.md)
- 創建轉換以及輸入和輸出資產。 您可以使用 .NET 教程瞭解如何在[上傳、編碼和資料流視頻](stream-files-tutorial-with-api.md)中創建轉換和輸入和輸出資產。
- 查看[編碼概念](encoding-concept.md)主題。

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
