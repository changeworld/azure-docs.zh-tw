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
ms.openlocfilehash: ce32343faefbcf2484ec0b1b39f752654a2d8514
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303608"
---
# <a name="assets-in-azure-media-services"></a>Azure 媒體服務中的資產

在 Azure 媒體服務中，[資產](https://docs.microsoft.com/rest/api/media/assets)是核心概念。 這是您輸入媒體的位置（例如，透過上傳或即時內嵌）、輸出媒體（來自作業輸出），以及從發佈媒體（適用于串流處理）。 

資產會對應到 [Azure 儲存體帳戶](storage-account-concept.md)中的 Blob 容器，且資產中的檔案會儲存為該容器中的區塊 Blob。 資產包含儲存在 Azure 儲存體中的數位檔案相關資訊（包括影片、音訊、影像、縮圖集合、文字播放軌和隱藏式輔助字幕檔案）。

當帳戶使用一般用途 v2 (GPv2) 儲存體時，媒體服務支援 Blob 層。 您可以利用 GPv2，將檔案移至[非經常性存取儲存體或封存儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。 封存儲存體適用于在不再需要時**保存**原始程式檔（例如，在編碼之後）。

**封存**儲存層只建議用於經過編碼，且編碼作業輸出已放在輸出 Blob 容器的極大型來源檔案。 輸出容器中您想要與資產建立關聯，並用於串流或分析內容的 blob，必須**存在於經常性存取或非**經常性**存取儲存層**中。

## <a name="naming"></a>命名 

### <a name="assets"></a>Assets

資產的名稱必須是唯一的。 媒體服務 v3 資源名稱（例如資產、作業、轉換）會受限於 Azure Resource Manager 命名條件約束。 如需詳細資訊，請參閱[命名慣例](media-services-apis-overview.md#naming-conventions)。

### <a name="blobs"></a>Blob

資產中的檔案/blob 名稱必須遵循[blob 名稱需求](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)和[NTFS 名稱需求](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)。 這些需求的原因是檔案可以從 blob 儲存體複製到本機 NTFS 磁片進行處理。

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

## <a name="next-steps"></a>後續步驟

[管理媒體服務中的資產](manage-asset-concept.md)

## <a name="see-also"></a>另請參閱

[媒體服務 v2 及 v3 之間的差異](migrate-from-v2-to-v3.md)
