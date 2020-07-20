---
title: 使用Azure 媒體索引器 2 Preview 編製媒體檔案索引 | Microsoft Docs
description: Azure Media Indexer 讓您能將媒體檔案的內容變成可搜尋，並產生隱藏式字幕和關鍵字的全文檢索記錄。 本主題說明如何使用 Media Indexer 2 Preview。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: c24218dc116803ca0e0a1f166b7b54b24fc4d5ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78163789"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>使用 Azure Media Indexer 2 Preview 編製媒體檔案索引

> [!NOTE]
> **Azure 媒體索引子 2**媒體處理器將被淘汰。 如需淘汰日期，請參閱此[舊版元件](legacy-components.md)主題。 [Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此舊版媒體處理器。 如需詳細資訊，請參閱[從 Azure 媒體索引器和 Azure 媒體索引器 2 遷移到 Azure 媒體服務影片索引器](migrate-indexer-v1-v2.md)。

**Azure Media Indexer 2 Preview** 媒體處理器 (MP) 可讓您將媒體檔案和內容設為可供搜尋，並產生隱藏式輔助字幕追蹤。 相較於舊版的 [Azure Media Indexer](media-services-index-content.md)， **Azure Media Indexer 2 Preview** 編製索引速度更快，並提供更廣泛的語言支援。 支援的語言包括英文、西班牙文、法文、德文、義大利文、中文 (國語、簡體)、葡萄牙文、阿拉伯文、俄文和日文。

**Azure Media Indexer 2 Preview** MP 目前為預覽功能。

本文章說明如何使用 **Azure Media Indexer 2 Preview** 建立索引工作。

## <a name="considerations"></a>考量

您必須考量下列事項：
 
* Azure 中國的世紀和 Azure Government 不支援索引子2。
* 在編製內容索引時，請務必使用語音非常清楚的媒體檔案 (不含背景音樂、噪音、效果或麥克風雜音)。 適當內容的一些範例有：錄製的會議、演講或簡報。 下列內容可能不適合用來編製索引：電影、電視節目、任何具有混合音訊與音效的內容、錄製效果不良有背景噪音 (雜音) 的內容。
 
## <a name="input-and-output-files"></a>輸入和輸出檔案
### <a name="input-files"></a>輸入檔案
音訊或視訊檔案

### <a name="output-files"></a>輸出檔案
索引工作可以產生下列格式的隱藏式輔助字幕檔案：  

* **TTML**
* **WebVTT**

這些格式的隱藏式輔助字幕 (CC) 檔案可以用來讓具有聽力障礙的人存取音訊和視訊檔案。

## <a name="task-configuration-preset"></a>工作組態 (預設)
以 **Azure 媒體索引器 2 預覽**建立索引工作時，您必須指定設定預設值。

下列 JSON 會設定可用的參數。

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>支援的語言
Azure Media Indexer 2 Preview 支援下列語言的語音轉文字 (在工作設定中指定語言名稱時，請使用以括號括住的 4 字元程式碼，如下所示)：

* 英文 [EnUs]
* 西班牙文 [EsEs]
* 中文 (國語、簡體) [ZhCn]
* 法文 [FrFr]
* 德文 [DeDe]
* 義大利文 [ItIt]
* 葡萄牙文 [PtBr]
* 阿拉伯文 (埃及) [ArEg]
* 日文 [JaJp]
* 俄文 [RuRu]
* 英式英文 [EnGb]
* 西班牙文（墨西哥） [EsMx] 

## <a name="supported-file-types"></a>支援的檔案類型

如需支援的檔案類型資訊，請參閱[支援的轉碼器/格式](media-services-media-encoder-standard-formats.md#input-containerfile-formats)一節。

## <a name="net-sample-code"></a>.NET 範例程式碼

下列程式將示範如何：

1. 建立資產並將媒體檔案上傳到資產。
2. 根據包含下列 JSON 預設值的設定檔，建立具有索引編製工作的作業：

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. 下載輸出檔案。 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>建立和設定 Visual Studio 專案

設定您的開發環境，並在 app.config 檔案中填入連接資訊，如[使用 .net 進行媒體服務開發](media-services-dotnet-how-to-use.md)中所述。 

#### <a name="example"></a>範例

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace IndexContent
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>相關連結
[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

