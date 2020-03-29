---
title: Azure 資料存儲上的多協定訪問 |微軟文檔
description: 將 Blob API 和應用程式與 Azure 資料存儲第 2 代一起使用 Blob API。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914853"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage 上的多重通訊協定存取

Blob API 現在使用具有階層命名空間的帳戶。 這將解鎖工具、應用程式和服務的生態系統，以及具有階層命名空間的帳戶的多個 Blob 存儲功能。

直到最近，您可能還得維護單獨的物件存儲和分析存儲存儲解決方案。 這是因為 Azure 資料存儲第 2 代具有有限的生態系統支援。 它還對 Blob 服務功能（如診斷日誌記錄）的存取權限有限。 碎片存儲解決方案很難維護，因為您必須在帳戶之間移動資料才能完成各種方案。 你不再需要那樣做。

借助資料湖存儲的多協定訪問，您可以使用工具、應用程式和服務的生態系統處理資料。 這還包括協力廠商工具和應用程式。 您可以將它們指向具有階層命名空間的帳戶，而無需修改它們。 *這些應用程式*即使調用 Blob API 也能正常工作，因為 Blob API 現在可以對具有階層命名空間的帳戶中的資料進行操作。

Blob 存儲功能（如[診斷日誌記錄](../common/storage-analytics-logging.md)、[訪問層](storage-blob-storage-tiers.md)和[Blob 存儲生命週期管理原則](storage-lifecycle-management-concepts.md)）現在使用具有階層命名空間的帳戶。 因此，您可以在 Blob 存儲帳戶上啟用階層命名空間，而不會失去對這些重要功能的存取權限。 

> [!NOTE]
> 資料湖存儲的多協定訪問通常可用，並且在所有區域都可用。 通過多協定訪問啟用的某些 Azure 服務或 Blob 存儲功能將保留在預覽中。  這些文章總結了當前對 Blob 存儲功能和 Azure 服務集成的支援。 
>
> [Azure 資料湖存儲第 2 代中提供的 Blob 存儲功能](data-lake-storage-supported-blob-storage-features.md)
>
>[支援 Azure 資料存儲第 2 代的 Azure 服務](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>資料存儲上的多協定訪問的工作原理

Blob API 和資料存儲 Gen2 API 可以對具有階層命名空間的存儲帳戶中的相同資料進行操作。 資料存儲湖存儲 Gen2 通過階層命名空間路由 Blob API，以便您可以獲得一流目錄操作和符合 POSIX 的存取控制清單 （ACL） 的優勢。 

![資料湖存儲概念上的多協定訪問](./media/data-lake-storage-interop/interop-concept.png) 

使用 Blob API 的現有工具和應用程式會自動獲得這些好處。 開發人員不必修改它們。 Data Lake 存儲 Gen2 始終應用目錄和檔級 ACL，而不管工具和應用程式用於訪問資料的協定如何。 

## <a name="see-also"></a>另請參閱

- [Azure 資料湖存儲第 2 代中提供的 Blob 存儲功能](data-lake-storage-supported-blob-storage-features.md)
- [支援 Azure 資料存儲第 2 代的 Azure 服務](data-lake-storage-supported-azure-services.md)
- [支援 Azure 資料存儲第 2 代的開源平臺](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage Gen2 的已知問題](data-lake-storage-known-issues.md)




