---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 51fde73565c9c4cdb21e998b8d43d303ceef062e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82561383"
---
本節說明 Azure 儲存體服務的限制，以及適用于 Azure Stack Edge/Data Box Gateway 服務的 Azure 檔案儲存體、Azure 區塊 blob 和 Azure 分頁 blob 所需的命名慣例。 請仔細檢閱儲存體限制，並遵循所有的建議。

如需與 Azure 儲存體服務限制以及命名共用、容器和檔案的最佳作法有關的最新資訊，請移至：

- [命名和參考容器](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [命名和參考共用](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [區塊 blob 和分頁 blob 慣例](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 如果有任何檔案或目錄超出 Azure 儲存體的服務限制，或不符合 Azure 檔案儲存體/Blob 命名慣例，則不會透過 Azure Stack Edge/Data Box Gateway 服務將這些檔案或目錄內嵌至 Azure 儲存體。