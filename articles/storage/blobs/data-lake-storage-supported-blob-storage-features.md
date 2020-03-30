---
title: Azure 資料湖存儲第 2 代中提供的 Blob 存儲功能 |微軟文檔
description: 瞭解哪些 Blob 存儲功能可用於 Azure 資料湖存儲第 2 代
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 215244204aa58cc2fdedc639d48e01b514759694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196003"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure 資料湖存儲第 2 代中提供的 Blob 存儲功能

Blob 存儲功能（如[診斷日誌記錄](../common/storage-analytics-logging.md)、[訪問層](storage-blob-storage-tiers.md)和 [Blob 存儲生命週期管理原則](storage-lifecycle-management-concepts.md)）現在使用具有階層命名空間的帳戶。 因此，您可以在 Blob 存儲帳戶上啟用階層命名空間，而不會失去對這些功能的存取權限。

此表列出了可用於 Azure 資料湖存儲 Gen2 的 Blob 存儲功能。 隨著支援的繼續擴展，這些表中顯示的項將隨時間而變化。

## <a name="supported-blob-storage-features"></a>支援的 Blob 存儲功能

> [!NOTE]
> 支援級別僅指資料湖存儲 Gen2 支援該功能的方式。

|Blob 存儲功能 |支援層級 |相關文章 |
|---------------|-------------------|---|
|經常性存取層|正式推出|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|非經常性存取層|正式推出|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|事件|正式推出|[回應 Blob 儲存體事件](storage-blob-event-overview.md)|
|指標（經典）|正式推出|[Azure 存儲分析指標（經典）](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure 監視器中的計量|正式推出|[Azure 監視器中的 Azure 儲存體計量](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 存儲 PowerShell 命令|正式推出|[快速入門：使用 PowerShell 上傳、下載和列出 blob](storage-quickstart-blobs-powershell.md)|
|Blob 存儲 Azure CLI 命令|正式推出|[快速入門：使用 Azure CLI 創建、下載和列出 blob](storage-quickstart-blobs-cli.md)|
|Blob 儲存體 API|正式推出|[快速入門：Azure Blob 存儲用戶端庫 v12 表示 .NET](storage-quickstart-blobs-dotnet.md)<br>[快速入門：使用 JAVA v12 SDK 管理 blob](storage-quickstart-blobs-java.md)<br>[快速入門：使用 Python v12 SDK 管理 Blob](storage-quickstart-blobs-python.md)<br>[快速入門：在 Node.js 中使用 JavaScript v12 SDK 管理 blob](storage-quickstart-blobs-nodejs.md)|
|存檔訪問層|預覽|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|生命週期管理原則|預覽|[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)|
|診斷記錄|預覽|[Azure 儲存體分析記錄](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|變更摘要|尚不支援|[更改 Azure Blob 存儲中的源支援](storage-blob-change-feed.md)|
|帳戶容錯移轉|尚不支援|[災害復原和帳戶容錯移轉](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 容器 ACL|尚不支援|[Set Container ACL (設定容器 ACL)](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|自訂網域|尚不支援|[將自訂域映射到 Azure Blob 存儲終結點](storage-custom-domain-name.md)|
|不可改變的存儲|尚不支援|[使用不可變存儲存儲業務關鍵型 Blob 資料](storage-blob-immutable-storage.md)|
|快照集|尚不支援|[在 .NET 中創建和管理 Blob 快照](storage-blob-snapshots.md)|
|虛刪除|尚不支援|[Azure 儲存體 Blob 的虛刪除](storage-blob-soft-delete.md)|
|靜態網站|尚不支援|[Azure 儲存體中的靜態網站代管](storage-blob-static-website.md)|
|在 Azure 監視器中記錄|尚不支援|尚未提供|
|高級塊 Blob|尚不支援|[創建塊 Blob 存儲帳戶](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage Gen2 的已知問題](data-lake-storage-known-issues.md)
- [支援 Azure 資料存儲第 2 代的 Azure 服務](data-lake-storage-supported-azure-services.md)
- [支援 Azure 資料存儲第 2 代的開源平臺](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 上的多重通訊協定存取](data-lake-storage-multi-protocol-access.md)