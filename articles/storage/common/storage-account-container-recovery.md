---
title: 存儲帳戶容器恢復
description: 存儲帳戶容器恢復
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562270"
---
# <a name="storage-account-container-recovery"></a>儲存體帳戶容器復原

Azure 儲存體可透過自動化複本提供資料復原功能。 不過，這無法避免應用程式程式碼或使用者損毀資料 (無論是意外還是惡意地)。 維護應用程式或使用者錯誤的資料精確度需要更進階的技術，例如複製資料到具有稽核記錄檔的次要儲存體位置。

## <a name="checking-azure-storage-account-type"></a>檢查 Azure 存儲帳戶類型

1. 導航到[Azure 門戶](https://portal.azure.com/)。

2. 找出您的儲存體帳戶。

3. 在 **"概述"** 部分中，檢查**複製**。

   ![映像](media/storage-account-container-recovery/1.png)

4. 如果複製類型為**GRS/RA-GRS，** 則帳戶容器恢復是無保證的。 對於所有其他複製類型，這是不可能的。

5. 收集以下資訊，並與 Microsoft 支援部門提交支援請求。

   * 存儲帳戶名稱：
   * 容器名稱：
   * 刪除時間：

   下表概述了存儲帳戶容器恢復的範圍，具體取決於複寫原則。

   |內容類型|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |儲存體容器|否|否|是|是| 

   * 我們可以嘗試還原存儲帳戶容器，但沒有任何保證。 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>不為恢復而做的事情是成功的

* 請不要重新創建容器（同名）。  
* 如果已重新創建容器，則需要在提交支援恢復請求之前刪除該容器。

## <a name="steps-to-prevent-this-in-the-future"></a>今後防止這種情況發生的步驟

1. 為了避免將來出現這種情況，建議使用的功能是[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)。

2. 我們還推薦[快照](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)功能。
 
## <a name="next-steps"></a>後續步驟

以下是該功能上的兩個示例代碼：

  * [在 .NET 中創建和管理 Blob 快照](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [將 Blob 快照與 PowerShell 一起使用](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

