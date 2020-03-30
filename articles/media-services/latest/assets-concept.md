---
title: Assets
titleSuffix: Azure Media Services
description: 瞭解什麼是資源以及如何由 Azure 媒體服務使用。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087907"
---
# <a name="assets-in-azure-media-services-v3"></a>Azure 媒體服務 v3 中的資產

在 Azure 媒體服務中，[資產](https://docs.microsoft.com/rest/api/media/assets)是一個核心概念。 它是您輸入媒體（例如，通過上傳或即時引入）、輸出媒體（來自作業輸出）和發佈媒體（用於資料流）的位置。 

資產映射到[Azure 存儲帳戶](storage-account-concept.md)中的 Blob 容器，資產中的檔存儲為該容器中的塊 Blob。 資產包含有關存儲在 Azure 存儲中的數位檔的資訊（包括視頻、音訊、圖像、縮略圖集合、文本軌道和隱藏字幕檔）。

當帳戶使用一般用途 v2 (GPv2) 儲存體時，媒體服務支援 Blob 層。 您可以利用 GPv2，將檔案移至[非經常性存取儲存體或封存儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。 **存檔**存儲適用于在不再需要時存檔原始檔案（例如，在編碼後）。

**封存**儲存層只建議用於經過編碼，且編碼作業輸出已放在輸出 Blob 容器的極大型來源檔案。 要與資產關聯並用於資料流或分析內容的輸出容器中的 blob 必須存在於**熱**存儲層或 **"酷"** 存儲層中。

## <a name="naming"></a>命名 

### <a name="assets"></a>Assets

資產的名稱必須是唯一的。 媒體服務 v3 資源名稱（例如，資產、作業、轉換）受 Azure 資源管理器命名約束的約束。 有關詳細資訊，請參閱[命名約定](media-services-apis-overview.md#naming-conventions)。

### <a name="blobs"></a>Blob

資產中檔/blob 的名稱必須同時遵循[blob 名稱要求](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)和[NTFS 名稱要求](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)。 這些要求的原因是檔可以從 Blob 存儲複製到本地 NTFS 磁片進行處理。

## <a name="next-steps"></a>後續步驟

[管理媒體服務中的資產](manage-asset-concept.md)

## <a name="see-also"></a>另請參閱

[媒體服務 v2 及 v3 之間的差異](migrate-from-v2-to-v3.md)
