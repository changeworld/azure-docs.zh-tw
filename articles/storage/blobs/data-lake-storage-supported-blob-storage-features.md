---
title: Azure 資料湖存儲第 2 代中提供的 Blob 存儲功能 |微軟文件
description: 瞭解哪些 Blob 儲存功能可用於 Azure 資料湖儲存第 2 代
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b270ce3cd15dbd1e8dd53bd60376a87d6e08f75c
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637206"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure 資料湖儲存第 2 代中提供的 Blob 儲存功能

Blob 儲存功能(如[診斷紀錄紀錄](../common/storage-analytics-logging.md)、[存取層](storage-blob-storage-tiers.md)和 [Blob 儲存生命週期管理原則](storage-lifecycle-management-concepts.md))現在使用具有分層命名空間的帳戶。 因此,您可以在 Blob 儲存帳戶上啟用分層命名空間,而不會失去對這些功能的訪問許可權。

此表列出了可用於 Azure 資料湖儲存 Gen2 的 Blob 儲存功能。 隨著支援的繼續擴展,這些表中顯示的項將隨時間而變化。

## <a name="supported-blob-storage-features"></a>支援的 Blob 儲存功能

> [!NOTE]
> 支援級別僅指數據湖存儲 Gen2 支援該功能的方式。

|Blob 儲存功能 |支援層級 |相關文章 |
|---------------|-------------------|---|
|經常性存取層|正式推出|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|非經常性存取層|正式推出|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|事件|正式推出|[回應 Blob 儲存體事件](storage-blob-event-overview.md)|
|指標(傳統)|正式推出|[Azure 儲存分析指標 (傳統)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure 監視器中的計量|正式推出|[Azure 監視器中的 Azure 儲存體計量](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 儲存 PowerShell 命令|正式推出|[快速入門:使用 PowerShell 上傳、下載和列出 blob](storage-quickstart-blobs-powershell.md)|
|Blob 儲存 Azure CLI 指令|正式推出|[快速入門:使用 Azure CLI 建立、下載和列出 blob](storage-quickstart-blobs-cli.md)|
|Blob 儲存體 API|正式推出|[快速入門:Azure Blob 儲存用戶端庫 v12 表示 .NET](storage-quickstart-blobs-dotnet.md)<br>[快速入門:使用 Java v12 SDK 管理 blob](storage-quickstart-blobs-java.md)<br>[快速入門:使用 Python v12 SDK 管理 Blob](storage-quickstart-blobs-python.md)<br>[快速入門:在 Node.js 中使用 JavaScript v12 SDK 管理 blob](storage-quickstart-blobs-nodejs.md)|
|存取層|預覽|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|生命週期管理原則|預覽|[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)|
|診斷記錄|正式推出|[Azure 儲存體分析記錄](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|變更摘要|尚不支援|[變更 Azure Blob 儲存中的來源支援](storage-blob-change-feed.md)|
|帳戶容錯移轉|尚不支援|[災難復原和帳戶故障轉移](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 容器 ACL|尚不支援|[Set Container ACL (設定容器 ACL)](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|自訂網域|尚不支援|[將自訂網射到 Azure Blob 儲存終結點](storage-custom-domain-name.md)|
|無法變更的儲存|尚不支援|[使用不可儲存儲存業務關鍵型 Blob 資料](storage-blob-immutable-storage.md)|
|快照集|尚不支援|[在 .NET 中建立及管理 Blob 快照](storage-blob-snapshots.md)|
|虛刪除|尚不支援|[Azure 儲存體 Blob 的虛刪除](storage-blob-soft-delete.md)|
|靜態網站|尚不支援|[Azure 儲存體中的靜態網站代管](storage-blob-static-website.md)|
|在 Azure 監視器中記錄|尚不支援|尚未提供|
|進階區|尚不支援|[建立區塊 Blob 儲存帳戶](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage Gen2 的已知問題](data-lake-storage-known-issues.md)
- [支援 Azure 資料儲存第 2 代的 Azure 服務](data-lake-storage-supported-azure-services.md)
- [支援 Azure 資料儲存第 2 代的開源平臺](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 上的多重通訊協定存取](data-lake-storage-multi-protocol-access.md)