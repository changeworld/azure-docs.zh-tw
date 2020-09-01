---
title: 從 Azure 媒體服務 v2 遷移至 v3
description: 本文說明 Azure 媒體服務 v3 中所導入的變更，並說明兩個版本之間的差異。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure 媒體服務, 串流, 廣播, 即時, 離線
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: be0c12eacae9bb13a475de4634746e9d38d35e43
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267559"
---
# <a name="media-services-v2-vs-v3"></a>媒體服務 v2 與 v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文說明 Azure 媒體服務 v3 中所導入的變更，並說明兩個版本之間的差異。

## <a name="general-changes-from-v2"></a>V2 的一般變更

* 針對使用 v3 建立的資產，媒體服務僅支援 [Azure 儲存體伺服器端儲存體加密](../../storage/common/storage-service-encryption.md)。
    * 您可以使用 v3 API 搭配以 v2 API 建立的 Asset，後者的[儲存體加密](../previous/media-services-rest-storage-encryption.md) (AES 256) 是由媒體服務所提供。
    * 您無法使用 v3 API 建立具有舊版 AES 256 [儲存體加密](../previous/media-services-rest-storage-encryption.md)的新 Asset。
* V3 中的 [資產](assets-concept.md)屬性與 v2 不同，請參閱 [屬性的對應方式](#map-v3-asset-properties-to-v2)。
* v3 SDK 現在已與儲存體 SDK 分離，其掌控力勝過所使用的儲存體 SDK，並可避免版本控制問題。 
* 在 v3 API 中，所有的編碼位元速率單位都是「位元/秒」。 這不同於 v2 媒體編碼器標準預設。 例如，v2 中的位元速率會指定為 128 (kbps)，但在 v3 中，則會是 128000 (位元/秒)。 
* 實體 AssetFiles、AccessPolicies 和 IngestManifests 不存在於 v3 中。
* IAsset.ParentAssets 屬性不在 v3 中。
* ContentKey 不再是實體，現在是串流定位器的屬性。
* 事件方格支援取代了 NotificationEndpoint。
* 下列實體已重新命名
    * 作業輸出會取代工作，而且現在是作業的一部分。
    * 串流定位器取代了定位器。
    * 即時事件取代了通道。<br/>即時事件計費是以直播頻道計量為基礎。 如需詳細資訊，請參閱[計費](live-event-states-billing.md)和[定價](https://azure.microsoft.com/pricing/details/media-services/)。
    * 即時輸出取代了程式。
* 「實況輸出」會在建立時開始，並在刪除時結束。 程式在 v2 API 中是以不同的方式運作，因此它們必須在建立後啟動。
* 若要取得作業的相關資訊，您必須知道建立作業所使用的轉換名稱。 
* 在 v2 中，XML [輸入](../previous/media-services-input-metadata-schema.md) 和 [輸出](../previous/media-services-output-metadata-schema.md) 中繼資料檔案會產生為編碼工作的結果。 在 v3 中，元資料格式從 XML 變更為 JSON。 
* 在媒體服務 v2 中，可以指定初始化向量 (IV) 。 在媒體服務 v3 中，無法指定 FairPlay IV。 雖然這不會影響使用媒體服務進行封裝和授權傳遞的客戶，但在使用協力廠商 DRM 系統將 FairPlay 授權傳遞 (混合模式) 時可能會有問題。 在這種情況下，請務必瞭解 FairPlay IV 是衍生自 cbcs 金鑰識別碼，並且可以使用以下公式來取出：

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    取代為

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    如需詳細資訊，請參閱適用于 [媒體服務 v3 的 Azure Functions c # 程式碼，適用于即時和 VOD 作業的混合模式](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)。
 
> [!NOTE]
> 檢查適用于 [媒體服務 v3 資源](media-services-apis-overview.md#naming-conventions)的命名慣例。 另請參閱 [命名 blob](assets-concept.md#naming)。

## <a name="feature-gaps-with-respect-to-v2-apis"></a>與 v2 API 相關的功能差距

v3 API 與 v2 API 具有下列功能差距。 縮小差距是刻不容緩的工作。

* [進階編碼器](../previous/media-services-premium-workflow-encoder-formats.md)和舊版[媒體分析處理器](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 Preview、Face Redactor 等) 無法透過 v3 存取。<br/>想要從 Media Indexer 1 或 2 Preview 移轉的客戶可以立即使用 v3 API 中的 AudioAnalyzer 預設值。  相較於舊版的 Media Indexer 1 或 2，這個新的預設值包含更多功能。 
* [V2 api 中媒體編碼器標準的許多先進功能](../previous/media-services-advanced-encoding-with-mes.md)目前無法在 v3 中使用，例如：
  
    * 資產拼接
    * 重疊
    * 裁剪
    * 縮圖原件
    * 在輸入沒有音訊時插入無提示音訊播放軌
    * 在輸入沒有影片時插入影片播放軌
* 具有轉碼的即時事件目前不支援候選影片插入中間串流，以及透過 API 呼叫插入廣告標記。 
* 請參閱 `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git` 範例程式碼，以取得有關使用 V2 REST API 的最佳作法和模式。NETCore SDK。

## <a name="asset-specific-changes"></a>資產特定變更

### <a name="map-v3-asset-properties-to-v2"></a>將 v3 資產屬性對應至 v2

下表顯示 [資產](/rest/api/media/assets/createorupdate#asset)在 v3 中的屬性如何對應至 v2 中的資產屬性。

|v3 屬性|v2 屬性|
|---|---|
|`id`- (唯一) 完整 Azure Resource Manager 路徑，請參閱[資產](/rest/api/media/assets/createorupdate)中的範例||
|`name` - (唯一) 請參閱 [命名慣例](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` - (唯一) 值以前置詞開頭 `nb:cid:UUID:` 。|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (建立選項) |
|`type`||

### <a name="storage-side-encryption"></a>儲存端加密

若要保護待用資產，資產應該透過儲存端加密來進行加密。 下表顯示儲存端加密在媒體服務中的運作方式：

|加密選項|說明|媒體服務 v2|媒體服務 v3|
|---|---|---|---|
|媒體服務的儲存體加密|AES-256 加密、媒體服務所管理的金鑰。|支援<sup>(1)</sup>|不支援<sup>(2)</sup>|
|[待用資料的儲存體服務加密](../../storage/common/storage-service-encryption.md)|由 Azure 或客戶管理的 Azure 儲存體、金鑰所提供的伺服器端加密。|支援|支援|
|[儲存體用戶端加密](../../storage/common/storage-client-side-encryption.md)|Azure 儲存體所提供的用戶端加密，Key Vault 中由客戶管理的金鑰。|不支援|不支援|

<sup>1</sup> 雖然媒體服務支援在純文字/沒有任何形式的加密的情況下處理內容，但不建議這麼做。

<sup>2</sup> 在媒體服務 v3 中，如果您的資產是以媒體服務 v2 建立，則儲存體加密 (AES-256 加密) 只對回溯相容性有所支援。 這表示 v3 適用于現有的儲存體加密資產，但不允許建立新的資產。

## <a name="code-differences"></a>程式碼差異

下表顯示 v2 和 v3 常見案例的程式碼差異。

|案例|V2 API|V3 API|
|---|---|---|
|建立資產並上傳檔案 |[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|提交作業|[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>示範如何先建立 Transform，然後再提交 Job。|
|使用 AES 加密發行資產 |1. 建立 ContentKeyAuthorizationPolicyOption<br/>2. 建立 ContentKeyAuthorizationPolicy<br/>3. 建立 AssetDeliveryPolicy<br/>4. 建立資產並上傳內容，或提交作業並使用輸出資產<br/>5. 將 AssetDeliveryPolicy 與資產產生關聯<br/>6. 建立 ContentKey<br/>7. 將 ContentKey 附加至資產<br/>8. 建立 AccessPolicy<br/>9. 建立定位器<br/><br/>[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. 建立內容金鑰原則<br/>2. 建立資產<br/>3. 上傳內容或使用資產作為 JobOutput<br/>4. 建立串流定位器<br/><br/>[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|取得作業詳細資料及管理工作 |[使用 v2 管理作業](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[使用 v3 管理作業](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> 請將本文加入書籤，並持續檢查更新。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[從媒體服務 v2 移動至 v3 的移轉指導](migrate-from-v2-to-v3.md)
