---
title: 使用 Azure 儲存體分析收集記錄和計量資料 | Microsoft Docs
description: 儲存體分析可讓您追蹤所有儲存體服務的度量資料，以及收集 Blob、佇列和資料表儲存體的記錄。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 4ad9f13bcdf36b67400adb62d58ee260ff256bb3
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637153"
---
# <a name="storage-analytics"></a>儲存體分析

Azure 儲存體分析會執行記錄，並提供儲存體帳戶的度量資料。 您可以使用此資料來追蹤要求、分析使用量趨勢，以及診斷儲存體帳戶的問題。

若要使用儲存體分析，您必須針對想要監視的每個服務個別啟用它。 可以從[Azure 門戶](https://portal.azure.com)啟用它。 有關詳細資訊,請參閱[在 Azure 門戶中監視儲存帳戶](storage-monitor-storage-account.md)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用[設定 Blob 服務屬性](/rest/api/storageservices/set-blob-service-properties)、[設定佇列服務屬性](/rest/api/storageservices/set-queue-service-properties)、[設定表服務屬性](/rest/api/storageservices/set-table-service-properties)和[設定檔服務屬性](/rest/api/storageservices/Get-File-Service-Properties)操作來啟用每個服務的儲存分析。

彙總的資料會儲存於已知的 Blob (用於記錄) 和已知的資料表 (用於度量) 中，您可以使用 Blob 服務和資料表服務 API 來存取。

儲存體分析在儲存的資料量上有 20 TB 的限制，但此限制與儲存體帳戶的總限制無關。 有關儲存帳戶限制的詳細資訊,請參閱[標準儲存帳戶的可伸縮性和效能目標](scalability-targets-standard-account.md)。

如需使用儲存體分析和其他工具來識別、診斷及疑難排解 Azure 儲存體相關問題的深入指南，請參閱 [監視、診斷及疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)。

## <a name="billing-for-storage-analytics"></a>儲存體分析計費

所有的度量資料都是由儲存體帳戶的服務所寫入。 因此，儲存體分析所執行的每個寫入作業都會列入計費。 此外，度量資料所使用的儲存體數量也會列入計費。

儲存體分析所執行的下列動作會列入計費：

* 建立 Blob 以用於記錄的要求。
* 針對度量建立資料表實體的要求。

如果您已設定資料保留原則，就不需在儲存體分析刪除舊記錄和度量資料時支付刪除交易的費用。 不過，從用戶端刪除交易會列入計費。 如需保留原則的詳細資訊，請參閱 [設定儲存體分析資料保留原則](https://msdn.microsoft.com/library/azure/hh343263.aspx)。

### <a name="understanding-billable-requests"></a>了解計費要求

對帳戶的儲存體服務所提出的每個要求，都會被歸類為計費或不計費。 儲存體分析會記錄對服務所做的每個個別要求，包括表示如何處理要求的狀態訊息。 同樣地，儲存體分析會儲存服務及該服務之 API 作業的度量，包括特定狀態訊息的百分比和計數。 這些功能可共同協助您分析計費要求、改善您的應用程式，以及診斷服務要求的問題。 如需計費的詳細資訊，請參閱 [了解 Azure 儲存體計費 - 頻寬、交易和容量](https://docs.microsoft.com/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity)。

檢視儲存體分析資料時，您可以使用 [儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 主題中的資料表，來判斷哪些要求需要計費。 然後，您可以將記錄和度量資料與狀態訊息進行比較，以查看您是否需為特定要求支付費用。 您也可以使用上述主題中的資料表，來調查儲存體服務或個別 API 作業的可用性。

## <a name="next-steps"></a>後續步驟
* [在 Azure 入口網站中監視儲存體帳戶](storage-monitor-storage-account.md)
* [儲存體分析度量](storage-analytics-metrics.md)
* [儲存體分析記錄](storage-analytics-logging.md)
