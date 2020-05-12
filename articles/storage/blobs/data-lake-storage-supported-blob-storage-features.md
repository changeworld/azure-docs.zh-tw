---
title: Azure Data Lake Storage Gen2 中可用的 Blob 儲存體功能 |Microsoft Docs
description: 瞭解您可以搭配 Azure Data Lake Storage Gen2 使用的 Blob 儲存體功能
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: ab704f53e608e7021f53a5d4b0a3e06c2ab807a1
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007456"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中可用的 Blob 儲存體功能

Blob 儲存體功能（例如[診斷記錄](../common/storage-analytics-logging.md)、[存取層](storage-blob-storage-tiers.md)和 [Blob 儲存體生命週期管理原則](storage-lifecycle-management-concepts.md)）現在會使用具有階層式命名空間的帳戶。 因此，您可以在 Blob 儲存體帳戶上啟用階層式命名空間，而不會失去這些功能的存取權。

下表列出您可以搭配 Azure Data Lake Storage Gen2 使用的 Blob 儲存體功能。 當支援繼續擴充時，出現在這些資料表中的專案會隨著時間而改變。 若要深入瞭解與功能的預覽狀態相關聯的特定問題，請參閱[已知問題](data-lake-storage-known-issues.md)一文。

## <a name="supported-blob-storage-features"></a>支援的 Blob 儲存體功能

> [!NOTE]
> 支援層級僅指 Data Lake Storage Gen2 支援功能的方式。 
>
> Premium-適用于 Data Lake Storage Gen2 的[效能區塊 blob 儲存體帳戶](storage-blob-create-account-block-blob.md)目前處於公開預覽狀態。 這些帳戶類型的支援層級會顯示在 [ **Premium 區塊 blob 儲存體帳戶] 資料**行中。

|Blob 儲存體功能 |一般用途 V2 儲存體帳戶|進階區塊 Blob 儲存體帳戶 |相關文章 |
|---------------|-------------------|---|
|經常性存取層|正式推出|不支援|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|非經常性存取層|正式推出|不支援|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|事件|正式推出|預覽|[回應 Blob 儲存體事件](storage-blob-event-overview.md)|
|計量（傳統）|正式推出|不支援|[Azure 儲存體分析計量（傳統）](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure 監視器中的計量|正式推出|預覽|[Azure 監視器中的 Azure 儲存體計量](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 儲存體 PowerShell 命令|正式推出|預覽|[快速入門：使用 PowerShell 上傳、下載及列出 blob](storage-quickstart-blobs-powershell.md)|
|Blob 儲存體 Azure CLI 命令|正式推出|預覽|[快速入門：使用 Azure CLI 建立、下載及列出 blob](storage-quickstart-blobs-cli.md)|
|Blob 儲存體 API|正式推出|預覽|[快速入門：適用于 .NET 的 Azure Blob 儲存體用戶端程式庫 v12](storage-quickstart-blobs-dotnet.md)<br>[快速入門：使用 JAVA v12 SDK 管理 blob](storage-quickstart-blobs-java.md)<br>[快速入門：使用 Python v12 SDK 管理 blob](storage-quickstart-blobs-python.md)<br>[快速入門：使用 node.js 中的 JavaScript v12 SDK 來管理 blob](storage-quickstart-blobs-nodejs.md)|
|診斷記錄|正式推出|預覽 <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Azure 儲存體分析記錄](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (部分機器翻譯)|
|封存存取層|預覽|不支援|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|生命週期管理原則|預覽|尚不支援|[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)|
|變更摘要|尚不支援|尚不支援|[Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)|
|帳戶容錯移轉|尚不支援|尚不支援|[嚴重損壞修復和帳戶容錯移轉](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 容器 ACL|尚不支援|尚不支援|[Set Container ACL (設定容器 ACL)](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|自訂網域|尚不支援|尚不支援|[將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)|
|不可變的儲存體|尚不支援|尚不支援|[使用不可變的儲存體儲存業務關鍵的 blob 資料](storage-blob-immutable-storage.md)|
|快照集|尚不支援|尚不支援|[在 .NET 中建立和管理 blob 快照集](storage-blob-snapshots.md)|
|虛刪除|尚不支援|尚不支援|[Azure 儲存體 Blob 的虛刪除](storage-blob-soft-delete.md)|
|靜態網站|尚不支援|尚不支援|[Azure 儲存體中的靜態網站代管](storage-blob-static-website.md)|
|登入 Azure 監視器|尚不支援|尚不支援|尚未提供|

<div id="diagnostic-logging"><sup>1</sup>若為 premium 區塊 blob 儲存體帳戶，則無法使用 Azure 入口網站來啟用診斷記錄（傳統）。 使用 PowerShell 加以啟用。</div>

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage Gen2 的已知問題](data-lake-storage-known-issues.md)
- [支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)
- [支援 Azure Data Lake Storage Gen2 的開放原始碼平臺](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 上的多重通訊協定存取](data-lake-storage-multi-protocol-access.md)