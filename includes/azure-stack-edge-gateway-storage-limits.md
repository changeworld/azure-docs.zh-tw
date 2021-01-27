---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: d12a5042d0c2d79989d82e86e7265d030912f5ee
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900814"
---
本節說明 Azure 儲存體服務的限制，以及適用于 Azure Stack Edge 服務的 Azure 檔案儲存體、Azure 區塊 blob 和 Azure 分頁 blob 所需的命名慣例。 請仔細檢閱儲存體限制，並遵循所有的建議。

如需與 Azure 儲存體服務限制以及命名共用、容器和檔案的最佳作法有關的最新資訊，請移至：

- [命名和參考容器](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [命名和參考共用](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [區塊 blob 和分頁 blob 慣例](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 如果有任何檔案或目錄超出 Azure 儲存體的服務限制，或不符合 Azure 檔案儲存體/Blob 命名慣例，則不會透過 Azure Stack Edge 服務將這些檔案或目錄內嵌至 Azure 儲存體。