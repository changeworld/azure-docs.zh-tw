---
title: 下載作業的結果-Azure 媒體服務
description: 本文示範如何下載作業的結果。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 0c81523d43fd316c3dc65cc2a5b34557f24014b5
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303404"
---
# <a name="download-the-results-of-a-job"></a>下載作業的結果

在 Azure 媒體服務中，處理您的影片時（例如，編碼或分析），您必須建立輸出[資產](assets-concept.md)來儲存[作業](transforms-jobs-concept.md)的結果。 接著，您可以使用媒體服務和儲存體 Api，將這些結果下載到本機資料夾。 

本文示範如何使用 JAVA 和 .NET Sdk 來下載結果。

## <a name="java"></a>Java

```java
/**
    * Use Media Service and Storage APIs to download the output files to a local folder
    * @param manager               The entry point of Azure Media resource management
    * @param resourceGroup         The name of the resource group within the Azure subscription
    * @param accountName           The Media Services account name
    * @param assetName             The asset name
    * @param outputFolder          The output folder for downloaded files.
    * @throws StorageException
    * @throws URISyntaxException
    * @throws IOException
    */
private static void downloadResults(MediaManager manager, String resourceGroup, String accountName,
        String assetName, File outputFolder) throws StorageException, URISyntaxException, IOException {
    ListContainerSasInput parameters = new ListContainerSasInput()
        .withPermissions(AssetContainerPermission.READ)
        .withExpiryTime(DateTime.now().plusHours(1));
    AssetContainerSas assetContainerSas = manager.assets()
        .listContainerSasAsync(resourceGroup, accountName, assetName, parameters).toBlocking().first();
    
    String strSas = assetContainerSas.assetContainerSasUrls().get(0);
    CloudBlobContainer container = new CloudBlobContainer(new URI(strSas));

    File directory = new File(outputFolder, assetName);
    directory.mkdir();

    ArrayList<ListBlobItem>  blobs = container.listBlobsSegmented(null, true, EnumSet.noneOf(BlobListingDetails.class), 200, null, null, null).getResults();

    for (ListBlobItem blobItem: blobs) {
        if (blobItem instanceof CloudBlockBlob) {
            CloudBlockBlob blob = (CloudBlockBlob)blobItem;
            File downloadTo = new File(directory, blob.getName());

            blob.downloadToFile(downloadTo.getPath());
        }
    }

    System.out.println("Download complete.");
}
```

請參閱完整的程式碼範例： [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/src/main/java/sample/EncodingWithMESPredefinedPreset.java)

## <a name="net"></a>.NET

```csharp
/// <summary>
/// Use Media Service and Storage APIs to download the output files to a local folder
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName">The Media Services account name.</param>
/// <param name="assetName">The asset name.</param>
/// <param name="resultsFolder">The output folder name for downloaded files.</param>
/// <returns>A task.</returns>
private async static Task DownloadResults(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName, string resultsFolder)
{
    AssetContainerSas assetContainerSas = client.Assets.ListContainerSas(
                    resourceGroupName, 
                    accountName, 
                    assetName,
                    permissions: AssetContainerPermission.Read, 
                    expiryTime: DateTime.UtcNow.AddHours(1).ToUniversalTime()
                    );

    Uri containerSasUrl = new Uri(assetContainerSas.AssetContainerSasUrls.FirstOrDefault());
    CloudBlobContainer container = new CloudBlobContainer(containerSasUrl);

    string directory = Path.Combine(resultsFolder, assetName);
    Directory.CreateDirectory(directory);

    Console.WriteLine("Downloading results to {0}.", directory);
    
    var blobs = container.ListBlobsSegmentedAsync(null,true, BlobListingDetails.None,200,null,null,null).Result;
    
    foreach (var blobItem in blobs.Results)
    {
        if (blobItem is CloudBlockBlob)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            string filename = Path.Combine(directory, blob.Name);

            await blob.DownloadToFileAsync(filename, FileMode.Create);
        }
    }

    Console.WriteLine("Download complete.");
}
```

請參閱完整的程式碼範例： [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/Program.cs)

## <a name="next-steps"></a>後續步驟

[從 HTTPS URL 建立工作輸入](job-input-from-http-how-to.md)。
