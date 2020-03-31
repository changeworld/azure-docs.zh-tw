---
title: 決定何時使用 Azure Blob、Azure 檔案服務或 Azure 磁碟
description: 深入了解在 Azure 中儲存和存取資料的不同方法，協助您決定使用哪種技術。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671038"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>決定何時使用 Azure Blob、Azure 檔案服務或 Azure 磁碟

Microsoft Azure 在 Azure 儲存體中提供數種功能，用以在雲端儲存和存取資料。 本文涵蓋 Azure 檔案服務、Azure Blob 和 Azure 磁碟，並設計來協助您從這些功能中選擇想要的功能。

## <a name="scenarios"></a>案例

下表會對 Azure 檔案服務、Azure Blob 和 Azure 磁碟進行比較，並顯示適用於各項的範例案例。

| 功能 | 描述 | 使用時機 |
|--------------|-------------|-------------|
| **Azure 檔案** | 提供 SMB 介面、用戶端程式庫和 [REST 介面](/rest/api/storageservices/file-service-rest-api)，允許從任何位置存取儲存的檔案。 | 您想要將應用程式「隨即轉移」到雲端，該應用程式已使用原生檔案系統 API 來在它與在 Azure 中執行的其他應用程式之間共用資料。<br/><br/>您想要儲存需要從許多虛擬機器存取的開發和偵錯工具。 |
| **Azure Blob** | 提供用戶端程式庫和 [REST 介面](/rest/api/storageservices/blob-service-rest-api)，允許在區塊 Blob 中大規模地儲存及存取非結構化資料。<br/><br/>也支援將 [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) 用於企業巨量資料分析解決方案。 | 您想要應用程式支援串流及隨機存取案例。<br/><br/>您想要能夠從任何位置存取應用程式資料。<br/><br/>您想要在 Azure 上建置企業 Data Lake，並執行巨量資料分析。 |
| **Azure 磁碟** | 提供用戶端程式庫和 [REST 介面](/rest/api/compute/manageddisks/disks/disks-rest-api)，允許持續針對連結的虛擬硬碟進行資料的儲存和存取。 | 您想要隨即轉移使用原生檔案系統 API 讀取及寫入資料至永續性磁碟的應用程式。<br/><br/>您想要儲存不需要從連結磁碟的虛擬機器之外存取的資料。 |


## <a name="next-steps"></a>後續步驟

決定如何儲存及存取資料時，您也應考慮牽涉的成本。 如需詳細資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。
  
某些 SMB 功能並不適用於雲端。 如需詳細資訊，請參閱 [Azure 檔案服務不支援的功能](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)。
 
有關 Azure Blob 的詳細資訊，請參閱我們的文章"[什麼是 Azure Blob 存儲？"。](../blobs/storage-blobs-overview.md)

有關磁片存儲的詳細資訊，請參閱我們的[託管磁片簡介](../../virtual-machines/windows/managed-disks-overview.md)。

有關 Azure 檔的詳細資訊，請參閱我們的文章"[規劃 Azure 檔部署](../files/storage-files-planning.md)"。