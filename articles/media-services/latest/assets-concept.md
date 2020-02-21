---
title: Assets
titleSuffix: Azure Media Services
description: 瞭解什麼是資產，以及它們如何由 Azure 媒體服務使用。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3860823787b860f2504d6fb13b9479d1feec9d28
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505808"
---
# <a name="assets-in-azure-media-services"></a>Azure 媒體服務中的資產

在 Azure 媒體服務中，[資產](https://docs.microsoft.com/rest/api/media/assets)會包含儲存在 Azure 儲存體中之數位檔案（包括影片、音訊、影像、縮圖集合、文字播放軌和隱藏式輔助字幕檔案）的相關資訊。

資產會對應到 [Azure 儲存體帳戶](storage-account-concept.md)中的 Blob 容器，且資產中的檔案會儲存為該容器中的區塊 Blob。 當帳戶使用一般用途 v2 (GPv2) 儲存體時，媒體服務支援 Blob 層。 您可以利用 GPv2，將檔案移至[非經常性存取儲存體或封存儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。 封存儲存體適用于在不再需要時**保存**原始程式檔（例如，在編碼之後）。

**封存**儲存層只建議用於經過編碼，且編碼作業輸出已放在輸出 Blob 容器的極大型來源檔案。 輸出容器中您想要與資產建立關聯，並用於串流或分析內容的 blob，必須**存在於經常性存取或非**經常性**存取儲存層**中。

### <a name="naming"></a>命名 

#### <a name="assets"></a>Assets

資產的名稱必須是唯一的。 媒體服務 v3 資源名稱（例如資產、作業、轉換）會受限於 Azure Resource Manager 命名條件約束。 如需詳細資訊，請參閱[命名慣例](media-services-apis-overview.md#naming-conventions)。

#### <a name="blobs"></a>Blob

資產中的檔案/blob 名稱必須遵循[blob 名稱需求](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)和[NTFS 名稱需求](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)。 這些需求的原因是檔案可以從 blob 儲存體複製到本機 NTFS 磁片進行處理。

## <a name="upload-digital-files-into-assets"></a>將數位檔案上傳到資產

將數位檔案上傳到儲存體並與資產相關聯之後，就可以在媒體服務編碼、串流和分析內容工作流程中使用這些檔案。 其中一個常見的媒體服務工作流程是上傳、編碼和串流檔案。 本節將概述一般步驟。

> [!TIP]
> 在您開始開發之前，請先參閱[使用媒體服務 V3 api 進行開發](media-services-apis-overview.md)（包含存取 api、命名慣例等等的資訊）。

1. 使用媒體服務 v3 API 建立新的「輸入」資產。 這項作業會在與您媒體服務帳戶相關聯的儲存體帳戶中建立容器。 此 API 會傳回容器名稱 (例如 `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`)。

    如果您已經有想要與資產相關聯的 blob 容器，您可以在建立資產時指定容器名稱。 媒體服務目前僅支援根目錄中的 Blob 容器，且檔案名稱中不能有路徑。 如此一來，檔案名稱為 "input.mp4" 的容器可運作。 不過，具有 "影片/輸入/輸入檔案名稱" 檔案名的容器將無法使用。

    您可以使用 Azure CLI，直接上傳至訂用帳戶中任何您有權存取的儲存體帳戶和容器。

    容器名稱必須是唯一的，並且必須遵循儲存體命名指引。 名稱不需要遵循媒體服務資產容器名稱 (資產-GUID) 的格式。

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 以讀寫權限取得 SAS URL，這將用來將數位檔案上傳到資產容器。 您可以使用媒體服務 API 來[列出資產容器 URL](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。
3. 使用 Azure 儲存體 Api 或 Sdk （例如[儲存體 REST API](../../storage/common/storage-rest-api-auth.md)或[.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)）將檔案上傳到資產容器中。
4. 使用媒體服務 v3 API 建立可處理「輸入」資產的轉換和作業。 如需詳細資訊，請參閱[轉換和作業](transform-concept.md)。
5. 串流來自「輸出」資產的內容。

如需示範如何建立資產的完整 .NET 範例，請在儲存體中取得資產容器的可寫入 SAS URL，並使用 SAS URL 將檔案上傳到儲存體中的容器，請參閱[從本機檔案建立作業輸入](job-input-from-local-file-how-to.md)。

### <a name="create-a-new-asset"></a>建立新的資產

> [!NOTE]
> 資產的日期時間類型屬性一律為 UTC 格式。

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

如需 REST 範例，請參閱[使用 REST 建立資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)。

此範例示範如何建立**要求**本文，您可以在其中指定描述、容器名稱、儲存體帳戶和其他有用的資訊。

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

如需完整範例，請參閱[從本機檔案建立作業輸入](job-input-from-local-file-how-to.md)。 在媒體服務 v3 中，您也可以從 HTTPS URL 中建立作業的輸入 (請參閱[從 HTTPS URL 中建立作業輸入](job-input-from-http-how-to.md))。

## <a name="map-v3-asset-properties-to-v2"></a>將 v3 資產屬性對應至 v2

下表顯示[資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)在 v3 中的屬性如何對應至第2版中的資產屬性。

|v3 屬性|v2 屬性|
|---|---|
|`id`-（唯一）完整 Azure Resource Manager 路徑，請參閱[資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate)中的範例||
|`name`-（唯一）請參閱[命名慣例](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`-（唯一）值的開頭為 `nb:cid:UUID:` 前置詞。|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` （建立選項）|
|`type`||

## <a name="storage-side-encryption"></a>儲存端加密

若要保護待用資產，資產應該透過儲存端加密來進行加密。 下表顯示儲存端加密在媒體服務中的運作方式：

|加密選項|描述|媒體服務 v2|媒體服務 v3|
|---|---|---|---|
|媒體服務的儲存體加密|AES-256 加密，媒體服務管理的金鑰。|支援<sup>(1)</sup>|不支援<sup>(2)</sup>|
|[待用資料的儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 儲存體提供的伺服器端加密、由 Azure 或客戶管理的金鑰。|支援|支援|
|[儲存體用戶端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 儲存體所提供的用戶端加密、由客戶在 Key Vault 中管理的金鑰。|不支援|不支援|

<sup>1</sup>雖然媒體服務支援以清除/不使用任何形式的加密來處理內容，但並不建議這麼做。

<sup>2</sup> 在媒體服務 v3 中，如果您的資產是以媒體服務 v2 建立，則儲存體加密 (AES-256 加密) 只對回溯相容性有所支援。 這表示 v3 適用于現有的儲存體加密資產，但不允許建立新的資產。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

* [串流處理檔案](stream-files-dotnet-quickstart.md)
* [使用雲端 DVR](live-event-cloud-dvr.md)
* [媒體服務 v2 及 v3 之間的差異](migrate-from-v2-to-v3.md)
