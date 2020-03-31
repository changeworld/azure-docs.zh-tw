---
title: 使用 Azure 媒體服務 v3 的信號描述性音軌 |微軟文檔
description: 按照本教程的步驟上傳檔、對視頻進行編碼、添加描述性音訊軌道以及使用媒體服務 v3 資料流內容。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392185"
---
# <a name="signal-descriptive-audio-tracks"></a>信號描述性音軌

您可以在影片中加入旁白曲目，協助視障用戶藉由聽取旁白來追蹤錄影。 在媒體服務 v3 中，您可以通過在清單檔中對音軌進行說明性音訊軌道添加警告來發出信號。

本文演示如何對視頻進行編碼，將包含描述性音訊的僅音訊 MP4 檔 （AAC 編解碼器） 上載到輸出資產，以及編輯 .ism 檔以包括描述性音訊。

## <a name="prerequisites"></a>Prerequisites

- [創建媒體服務帳戶](create-account-cli-how-to.md)。
- 請依照[使用 Azure CLI 存取 Azure 媒體服務 API](access-api-cli-how-to.md) 中的步驟，並儲存認證。 您必須使用這些認證來存取 API。
- 查看[動態打包](dynamic-packaging-overview.md)。
- 查看[上傳、編碼和資料流視頻](stream-files-tutorial-with-api.md)教程。

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>建立輸入資產並將本機檔案上傳到其中 

**CreateInputAsset**函數創建新的輸入[資產](https://docs.microsoft.com/rest/api/media/assets)，並將指定的本機視訊檔上載到其中。 此**資產**用作編碼作業的輸入。 在媒體服務 v3 中，**作業**的輸入可以是**資產**，或是您透過 HTTPS URL 讓媒體服務帳戶可存取的內容。 

如果要瞭解如何從 HTTPS URL 進行編碼，請參閱[本文](job-input-from-http-how-to.md)。  

在媒體服務 v3 中，您會使用 Azure 儲存體 API 來上傳檔案。 下列 .NET 程式碼片段將說明執行方式。

下列函式會執行下列動作：

* 創建**資產** 
* 獲取[存儲中資產容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)的可寫[SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* 使用 SAS URL，將檔案上傳至儲存體中的容器

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

如果需要將創建的輸入資產的名稱傳遞給其他方法，請確保使用`Name`從 返回`CreateInputAssetAsync`的資產物件的屬性，例如，inputAsset.Name。 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>創建輸出資產以存儲編碼作業的結果

輸出[資產](https://docs.microsoft.com/rest/api/media/assets)存儲編碼作業的結果。 以下函數演示如何創建輸出資產。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

如果需要將創建的輸出資產的名稱傳遞給其他方法，請確保使用`Name`從 返回`CreateIOutputAssetAsync`的資產物件的屬性，例如，outputAsset.Name。 

在本文中，將`outputAsset.Name`值傳遞給`SubmitJobAsync`和`UploadAudioIntoOutputAsset`函數。

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>創建對上載的檔進行編碼的轉換和作業

在媒體服務中編碼或處理內容時，將編碼設定設為配方 (recipe) 是很常見的模式。 然後您可以透過提交**作業**，將該配方套用到影片。 藉由為每部新影片提交新的作業，您可以將該配方套用到媒體櫃中的所有影片。 配方在媒體服務中稱為「**轉換 (Transform)**」。 有關詳細資訊，請參閱[轉換和作業](transform-concept.md)。 本教學課程中所述的範例會定義編碼影片的配方，以便將影片串流到各種 iOS 和 Android 裝置。 

下面的示例創建一個轉換（如果不存在）。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

以下函數提交作業。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>請等待作業完成

此作業需要一些時間來完成，而您可以選擇在完成時收到通知。 我們建議使用事件網格等待作業完成。

作業通常經歷以下狀態：**計畫**、**排隊**、**處理**、**完成**（最終狀態）。 如果作業發生錯誤，您會收到**錯誤**狀態。 如果正在取消作業，您會收到**正在取消**的狀態，以及完成時的**已取消**狀態。

有關詳細資訊，請參閱[處理事件網格事件](reacting-to-media-services-events.md)。

## <a name="upload-the-audio-only-mp4-file"></a>上傳僅音訊 MP4 檔

將包含描述性音訊的附加僅音訊 MP4 檔 （AAC 編解碼器） 上載到輸出資產中。  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

下面是對`UpoadAudioIntoOutputAsset`函數的調用示例：

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>編輯 .ism 檔

編碼作業完成後，輸出資產將包含編碼作業生成的檔。 

1. 在 Azure 門戶中，導航到與媒體服務帳戶關聯的存儲帳戶。 
1. 查找具有輸出資產名稱的容器。 
1. 在容器中，查找 .ism 檔，然後按一下 **"編輯 blob"（** 在右側視窗中）。 
1. 通過添加有關上載的僅音訊 MP4 檔 （AAC 編解碼器） 的資訊，編輯 .ism 檔，其中包含描述性音訊，並在完成後按 **"保存**"。

    要發出描述性音軌的信號，您需要向 .ism 檔添加"可訪問性"和"角色"參數。 您需負責正確設定這些參數，才能將以音訊描述形式傳送曲目訊號。 例如，添加`<param name="accessibility" value="description" />`和`<param name="role" value="alternate" />`到特定音軌的 .ism 檔，如以下示例所示。
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>獲取流式定位器

編碼完成後，下一個步驟是要讓用戶端可播放輸出資產中的視訊。 您可以透過兩個步驟來執行此動作：第一步，建立[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)，第二步，建置用戶端可以使用的串流 URL。 

創建**流式處理器的過程**稱為發佈。 預設情況下，**流式處理器在**進行 API 呼叫後立即有效，並持續到刪除為止，除非您配置可選的開始和結束時間。 

建立 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) 時，您必須指定需要的 **StreamingPolicyName**。 在此示例中，您將資料流清除內容（或非加密內容），以便使用預定義的清除流策略（**預定義流策略.僅清除流**）。

> [!IMPORTANT]
> 使用自訂的[串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)時，您應該為媒體服務帳戶設計一組受限的這類原則，並且在需要相同的加密選項和通訊協定時，對 StreamingLocators 重新使用這些原則。 媒體服務帳戶有串流原則項目的數量配額。 不建議您對每個串流定位器建立新的串流原則。

下列程式碼假設您要呼叫具有唯一 locatorName 的函式。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

雖然本主題中的範例是在討論串流處理，但您可以使用相同的呼叫來建立串流定位器，以透過漸進式下載來傳遞影片。

### <a name="get-streaming-urls"></a>取得串流 URL

建立了[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)之後，現在您可以取得串流 URL，如 **GetStreamingURLs** 中所示。 若要建置 URL，您需要串連[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)主機名稱和**串流定位器**路徑。 此範例會使用預設的** **串流端點**。 初次建立媒體服務帳戶時，此預設的** **串流端點**會處於停止狀態，因此您需要呼叫 **Start**。

> [!NOTE]
> 在此方法中，您需要 locatorName，也就是為輸出資產建立**串流定位器**時所使用的項目。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>使用 Azure 媒體播放器測試

本文使用 Azure 媒體播放器測試串流。 

> [!NOTE]
> 如果播放程式裝載在 HTTPS 網站上，請務必將 URL 更新為 "https"。

1. 打開 Web 瀏覽器並導航[https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)到 。
2. 在**URL：** 框中，粘貼從應用程式獲得的流 URL 值之一。 
 
     您可以貼上 HLS、Dash 或 Smooth 格式的 URL，Azure 媒體播放器將會切換至適當的串流通訊協定，以便在您的裝置上自動播放。
3. 按一下 [更新播放程式]****。

Azure 媒體播放器可以用於測試，但不應用於生產環境。 

## <a name="next-steps"></a>後續步驟

[分析影片](analyze-videos-tutorial-with-api.md)
