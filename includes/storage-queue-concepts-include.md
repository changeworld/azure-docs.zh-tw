---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80151309"
---
## <a name="what-is-queue-storage"></a>什麼是佇列存儲？

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。 單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。 佇列存儲通常用於創建工作積壓工作以非同步處理。

## <a name="queue-service-concepts"></a>佇列服務概念

Azure 佇列服務包含以下元件：

![Azure 佇列服務元件](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL 格式：** 佇列可以使用以下 URL 格式定址：HTTP://`<storage account>`.queue.core.windows.net/`<queue>`
  
    下列 URL 可定址圖中的佇列：  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **存儲帳戶：** 對 Azure 存儲的所有訪問都通過存儲帳戶完成。 如需儲存體帳戶的詳細資訊，請參閱[儲存體帳戶概觀](../articles/storage/common/storage-account-overview.md)。
* **佇列：** 佇列包含一組訊息。 所有訊息都必須放在佇列中。 請注意，佇列名稱必須是小寫。 如需為佇列命名的詳細資訊，請參閱 [為佇列和中繼資料命名](https://msdn.microsoft.com/library/azure/dd179349.aspx)。
* **訊息：** 大小上限為 64 KB 的訊息 (任何格式)。 訊息可保留在佇列中的時間上限為 7 天。 如需版本 2017-07-29 或更新版本，最大存留時間可以是任何正數，或是表示訊息未過期的 -1。 如果省略此參數，則預設存留時間為 7 天。

