---
title: 從 Azure 媒體服務 v2 遷移到 v3
description: 本文說明 Azure 媒體服務 v3 中所導入的變更，並說明兩個版本之間的差異。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure 媒體服務, 串流, 廣播, 即時, 離線
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087820"
---
# <a name="media-services-v2-vs-v3"></a>媒體服務 v2 vs. v3

本文說明 Azure 媒體服務 v3 中所導入的變更，並說明兩個版本之間的差異。

## <a name="general-changes-from-v2"></a>v2 的一般更改

* 對於使用 v3 創建的資產，媒體服務僅支援[Azure 存儲伺服器端存儲加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。
    * 您可以使用 v3 API 搭配以 v2 API 建立的 Asset，後者的[儲存體加密](../previous/media-services-rest-storage-encryption.md) (AES 256) 是由媒體服務所提供。
    * 您無法使用 v3 API 建立具有舊版 AES 256 [儲存體加密](../previous/media-services-rest-storage-encryption.md)的新 Asset。
* 在 v3 中[，資產](assets-concept.md)的屬性與 v2 不同，請參閱[屬性如何映射](#map-v3-asset-properties-to-v2)。
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
* 要獲取有關作業的資訊，您需要知道創建作業的 Transform 名稱。 
* 在 v2 中，XML[輸入](../previous/media-services-input-metadata-schema.md)和[輸出](../previous/media-services-output-metadata-schema.md)元資料檔案將生成為編碼作業的結果。 在 v3 中，元資料格式從 XML 更改為 JSON。 
* 在媒體服務 v2 中，可以指定初始化向量 （IV）。 在媒體服務 v3 中，無法指定公平播放 IV。 雖然它不會影響使用媒體服務進行打包和許可證交付的客戶，但當使用協力廠商 DRM 系統交付 FairPlay 許可證（混合模式）時，可能會出現問題。 在這種情況下，請務必知道 FairPlay IV 派生自 cbcs 金鑰識別碼，並可使用此公式檢索：

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

    有關詳細資訊，請參閱[即時和 VOD 操作混合模式下的媒體服務 v3](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)的 Azure 函數 C# 代碼。
 
> [!NOTE]
> 查看應用於[媒體服務 v3 資源的](media-services-apis-overview.md#naming-conventions)命名約定。 還要查看[命名 blob](assets-concept.md#naming)。

## <a name="feature-gaps-with-respect-to-v2-apis"></a>與 v2 API 相關的功能差距

v3 API 與 v2 API 具有下列功能差距。 縮小差距是刻不容緩的工作。

* [進階編碼器](../previous/media-services-premium-workflow-encoder-formats.md)和舊版[媒體分析處理器](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 Preview、Face Redactor 等) 無法透過 v3 存取。<br/>想要從 Media Indexer 1 或 2 Preview 移轉的客戶可以立即使用 v3 API 中的 AudioAnalyzer 預設值。  相較於舊版的 Media Indexer 1 或 2，這個新的預設值包含更多功能。 
* v2 API[中的媒體編碼器標準的許多高級功能](../previous/media-services-advanced-encoding-with-mes.md)目前不在 v3 中，例如：
  
    * 資產拼接
    * 重疊
    * 裁剪
    * 縮圖原件
    * 當輸入沒有音訊時插入靜音音軌
    * 當輸入沒有視頻時插入視頻軌道
* 具有轉碼的即時事件目前不支援候選影片插入中間串流，以及透過 API 呼叫插入廣告標記。 
 
## <a name="asset-specific-changes"></a>特定于資產的更改

### <a name="map-v3-asset-properties-to-v2"></a>將 v3 資產屬性對應到 v2

下表顯示了[v3](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)中的資產屬性如何映射到 v2 中的資產屬性。

|v3 屬性|v2 屬性|
|---|---|
|`id`- （唯一）完整的 Azure 資源管理器路徑，請參閱[資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate)中的示例||
|`name`- （唯一）請參閱[命名約定](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- （唯一的）值`nb:cid:UUID:`以首碼開頭。|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`（創建選項）|
|`type`||

### <a name="storage-side-encryption"></a>儲存端加密

若要保護待用資產，資產應該透過儲存端加密來進行加密。 下表顯示儲存端加密在媒體服務中的運作方式：

|加密選項|描述|媒體服務 v2|媒體服務 v3|
|---|---|---|---|
|媒體服務的儲存體加密|AES-256 加密，由媒體服務管理金鑰。|支援<sup>(1)</sup>|不支援<sup>(2)</sup>|
|[待用資料的儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 存儲提供的伺服器端加密，由 Azure 或客戶管理金鑰。|支援|支援|
|[儲存體用戶端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 存儲提供的用戶端加密，金鑰由金鑰保存庫中的客戶管理。|不支援|不支援|

<sup>1</sup>雖然媒體服務確實支援以清除/無任何形式的加密方式處理內容，但不建議這樣做。

<sup>2</sup> 在媒體服務 v3 中，如果您的資產是以媒體服務 v2 建立，則儲存體加密 (AES-256 加密) 只對回溯相容性有所支援。 意味著 v3 適用于現有存儲加密資產，但不允許創建新的存儲加密資產。

## <a name="code-differences"></a>程式碼差異

下表顯示 v2 和 v3 常見案例的程式碼差異。

|狀況|V2 API|V3 API|
|---|---|---|
|建立資產並上傳檔案 |[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|提交作業|[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>示範如何先建立 Transform，然後再提交 Job。|
|使用 AES 加密發行資產 |1. 創建內容金鑰授權策略選項<br/>2. 創建內容金鑰授權策略<br/>3. 創建資產交付政策<br/>4. 創建資產並上傳內容或提交作業並使用輸出資產<br/>5. 將資產交付政策與資產關聯<br/>6. 創建內容鍵<br/>7. 將內容金鑰附加到資產<br/>8. 創建訪問策略<br/>9. 創建定位器<br/><br/>[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. 創建內容金鑰策略<br/>2. 創建資產<br/>3. 上傳內容或使用資產作為作業輸出<br/>4. 創建流式處理器<br/><br/>[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|獲取工作詳細資訊並管理作業 |[使用 v2 管理作業](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[使用 v3 管理作業](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> 請將本文加入書籤，並持續檢查更新。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[從媒體服務 v2 移動至 v3 的移轉指導](migrate-from-v2-to-v3.md)
