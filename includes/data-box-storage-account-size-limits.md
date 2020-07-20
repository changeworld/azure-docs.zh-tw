---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85378503"
---
以下是複製到儲存體帳戶中的資料在大小方面的限制。 請確定您上傳的資料符合這些限制。 如需有關這些限制的最新資訊，請參閱[Blob 儲存體的擴充性和效能目標](../articles/storage/blobs/scalability-targets.md)和 Azure 檔案儲存體的擴充[性和效能目標](../articles/storage/files/storage-files-scale-targets.md)。

| 複製到 Azure 儲存體帳戶中的資料大小                      | 預設限制          |
|---------------------------------------------------------------------|------------------------|
| 區塊 Blob 和分頁 Blob                                            | 最大限制與[針對 Azure 訂用帳戶定義的儲存體限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)相同，而且包含來自所有來源的資料，包括資料箱。|
| Azure 檔案                                                          | 標準檔案共用的大小上限為 5 TB <br> Premium 檔案共用的大小上限是每個共用100TiB。<br> StorageAccount_AzureFiles** 底下的所有資料夾必須遵循此限制。       |
