---
title: 建立具有多個轉換輸出的 Azure 媒體服務作業
description: 本主題示範如何建立具有多個轉換輸出的 Azure 媒體服務作業。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2020
ms.author: juliako
ms.openlocfilehash: dbbeeb33ee46b37ec920fe598483c332d3439689
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563137"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>建立具有多個轉換輸出的作業

本主題說明如何建立具有兩個轉換輸出的轉換。 第一個呼叫會使用內建的[AdaptiveStreaming](encoding-concept.md#builtinstandardencoderpreset)預設值，針對自動調整位元速率串流的輸入進行編碼。 第二個會呼叫輸入影片中的音訊信號，以使用[AudioAnalyzerPreset](analyzing-video-audio-files-concept.md#built-in-presets)進行處理。 建立轉換之後，您可以提交將會據以處理影片的作業。 因為在此範例中，我們會指定兩個轉換輸出，所以我們必須指定兩個作業輸出。 您可以選擇將這兩個作業輸出導向相同的資產（如下所示），也可以將結果寫入不同的資產。
 

> [!TIP]
> 在您開始開發之前，請先參閱[使用媒體服務 V3 api 進行開發](media-services-apis-overview.md)（包含存取 api、命名慣例等的資訊）。

## <a name="create-a-transform"></a>建立轉換

下列程式碼顯示如何建立會產生兩個輸出的轉換。

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```
## <a name="submit-a-job"></a>提交作業

建立具有 HTTPS URL 輸入和兩個作業輸出的作業。

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>作業錯誤碼

請參閱[錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode) \(英文\)。

## <a name="next-steps"></a>後續步驟

[使用 .NET Azure 媒體服務 v3 範例](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
