---
title: Azure 媒體服務中的資產
description: 瞭解什麼是 Azure 媒體服務的資產，以及它們的使用方式。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5159432107e60f6c21bcf70e0bbc9a9e2123a728
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897691"
---
# <a name="assets-in-azure-media-services-v3"></a>Azure 媒體服務 v3 中的資產

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒體服務中， [資產](/rest/api/media/assets) 是核心概念。 您可以在這裡輸入媒體 (例如，透過上傳或即時內嵌) 、輸出媒體 (從作業輸出) ，以及從 (發佈媒體以進行串流) 。 

資產會對應到 [Azure 儲存體帳戶](storage-account-concept.md) 中的 blob 容器，而資產中的檔案會儲存為該容器中的區塊 blob。 資產包含儲存在 Azure 儲存體 (中的數位檔案的相關資訊，包括影片、音訊、影像、縮圖集合、文字播放軌和隱藏式輔助字幕檔案) 。

當帳戶使用一般用途 v2 (GPv2) 儲存體時，媒體服務支援 Blob 層。 您可以利用 GPv2，將檔案移至[非經常性存取儲存體或封存儲存體](../../storage/blobs/storage-blob-storage-tiers.md)。 封存 **儲存體適合** 用來封存原始程式檔（如果不再需要的話） (例如，在) 編碼之後。

**封存** 儲存層只建議用於經過編碼，且編碼作業輸出已放在輸出 Blob 容器的極大型來源檔案。 您要與資產產生關聯的輸出容器中的 blob，以及用來串流或分析內容的 blob，都必須存在 **于經常性存取或非** 經常性 **存取儲存層** 中。

## <a name="naming"></a>命名 

### <a name="assets"></a>Assets

資產的名稱必須是唯一的。 媒體服務 v3 資源名稱 (例如，資產、作業、轉換) 會受到 Azure Resource Manager 命名條件約束的約束。 如需詳細資訊，請參閱 [命名慣例](media-services-apis-overview.md#naming-conventions)。

### <a name="blobs"></a>Blob

資產中的檔案/Blob 名稱必須遵循 [Blob 名稱需求](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)與 [NTFS 名稱需求](/windows/win32/fileio/naming-a-file)。 之所以會有這些需求，是因為檔案會從 Blob 儲存體複製到本機 NTFS 磁碟加以處理。

## <a name="next-steps"></a>後續步驟

[媒體服務總覽](media-services-overview.md)

## <a name="see-also"></a>另請參閱

[媒體服務 v2 及 v3 之間的差異](migrate-v-2-v-3-migration-introduction.md)
