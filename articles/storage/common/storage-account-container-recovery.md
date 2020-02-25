---
title: 儲存體帳戶容器復原
description: 儲存體帳戶容器復原
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562270"
---
# <a name="storage-account-container-recovery"></a>儲存體帳戶容器復原

Azure 儲存體可透過自動化複本提供資料復原功能。 不過，這無法避免應用程式程式碼或使用者損毀資料 (無論是意外還是惡意地)。 維護應用程式或使用者錯誤的資料精確度需要更進階的技術，例如複製資料到具有稽核記錄檔的次要儲存體位置。

## <a name="checking-azure-storage-account-type"></a>正在檢查 Azure 儲存體帳戶類型

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com/)。

2. 找出您的儲存體帳戶。

3. 在 [**總覽**] 區段中，**檢查是否有**複寫。

   ![影像](media/storage-account-container-recovery/1.png)

4. 如果複寫類型為**GRS/RA-GRS**，則可以在不保證的情況下進行帳戶容器復原。 針對所有其他複寫類型，則不可行。

5. 收集下列資訊，並使用 Microsoft 支援服務提出支援要求。

   * 儲存體帳戶名稱：
   * 容器名稱：
   * 刪除時間：

   下表提供儲存體帳戶容器復原範圍的總覽，視複寫策略而定。

   |內容類型|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |儲存體容器|否|否|是|是| 

   * 我們可以嘗試還原儲存體帳戶容器，但不會有任何保證。 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>復原成功的事項

* 請不要重新建立具有相同名稱的容器。  
* 如果您已經重新建立容器，您必須先刪除容器，然後再提出支援要求以進行復原。

## <a name="steps-to-prevent-this-in-the-future"></a>在未來避免此情況的步驟

1. 為避免未來發生此情況，最建議使用的功能是虛[刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)。

2. 我們也建議[快照](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)集功能。
 
## <a name="next-steps"></a>後續步驟

以下是此功能的兩個範例代碼：

  * [在 .NET 中建立和管理 blob 快照集](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [搭配 PowerShell 使用 blob 快照集](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

