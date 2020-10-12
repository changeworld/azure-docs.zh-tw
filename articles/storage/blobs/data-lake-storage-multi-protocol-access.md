---
title: Azure Data Lake Storage 上的多重通訊協定存取 |Microsoft Docs
description: 使用 blob Api 和使用 Blob Api 搭配 Azure Data Lake Storage Gen2 的應用程式。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77914853"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage 上的多重通訊協定存取

Blob Api 現在可搭配具有階層命名空間的帳戶使用。 這可將工具、應用程式和服務的生態系統，以及數個 Blob 儲存體功能，提供給具有階層命名空間的帳戶。

在最近，您可能必須為物件儲存體和分析儲存體維護不同的儲存體解決方案。 這是因為 Azure Data Lake Storage Gen2 的生態系統支援有限。 它也具有 Blob 服務功能的有限存取權，例如診斷記錄。 分散的儲存體解決方案很難維護，因為您必須在帳戶之間移動資料，才能完成各種案例。 您不再需要這麼做。

使用 Data Lake Storage 上的多重通訊協定存取時，您可以使用工具、應用程式和服務的生態系統來處理您的資料。 這也包含協力廠商工具和應用程式。 您可以將它們指向具有階層命名空間的帳戶，而不需要修改它們。 這些應用程式在呼叫 Blob Api *時仍可運作，* 因為 blob api 現在可以在具有階層命名空間的帳戶中運算元據。

Blob 儲存體功能，例如 [診斷記錄](../common/storage-analytics-logging.md)、 [存取層](storage-blob-storage-tiers.md)和 [blob 儲存體生命週期管理原則](storage-lifecycle-management-concepts.md) ，現在可與具有階層命名空間的帳戶搭配使用。 因此，您可以在 blob 儲存體帳戶上啟用階層式命名空間，而不會失去這些重要功能的存取權。 

> [!NOTE]
> Data Lake Storage 上的多重通訊協定存取已正式運作，並可在所有區域使用。 某些由多通訊協定存取啟用的 Azure 服務或 Blob 儲存體功能仍處於預覽狀態。  這些文章摘要說明 Blob 儲存體功能和 Azure 服務整合目前的支援。 
>
> [Azure Data Lake Storage Gen2 中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)
>
>[支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data lake storage 上的多重通訊協定存取如何運作

Blob Api 和 Data Lake Storage Gen2 Api 可以在具有階層命名空間的儲存體帳戶中，操作相同的資料。 Data Lake Storage Gen2 透過階層命名空間路由 Blob Api，讓您可以 (Acl) 取得第一個類別目錄作業和 POSIX 相容存取控制清單的優點。 

![Data Lake Storage 概念上的多重通訊協定存取](./media/data-lake-storage-interop/interop-concept.png) 

使用 Blob API 的現有工具和應用程式會自動獲得這些優勢。 開發人員不需要修改它們。 無論工具和應用程式用來存取資料的通訊協定為何，Data Lake Storage Gen2 一致地套用目錄和檔案層級 Acl。 

## <a name="see-also"></a>請參閱

- [Azure Data Lake Storage Gen2 中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)
- [支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)
- [支援 Azure Data Lake Storage Gen2 的開放原始碼平台](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage Gen2 的已知問題](data-lake-storage-known-issues.md)




