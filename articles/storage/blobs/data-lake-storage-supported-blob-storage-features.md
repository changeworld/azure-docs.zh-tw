---
title: Azure Data Lake Storage Gen2 中可用的 Blob 儲存體功能 | Microsoft Docs
description: 了解您可以搭配 Azure Data Lake Storage Gen2 使用的 Blob 儲存體功能
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 982f4a9cdf3984bae79cd11dad2bd637a1772f05
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348495"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中可用的 Blob 儲存體功能

Blob 儲存體功能，例如 [診斷記錄](../common/storage-analytics-logging.md)、 [存取層](storage-blob-storage-tiers.md)和 [blob 儲存體生命週期管理原則](storage-lifecycle-management-concepts.md) ，現在可與具有階層命名空間的帳戶搭配使用。 因此，您可以在 Blob 儲存體帳戶上啟用階層式命名空間，而不會失去這些功能的存取權。

下表列出您可以搭配 Azure Data Lake Storage Gen2 使用的 Blob 儲存體功能。 當支援繼續擴充時，出現在這些資料表中的項目會隨著時間而改變。 若要深入了解與功能預覽狀態相關聯的特定問題，請參閱[已知問題](data-lake-storage-known-issues.md)文章。

## <a name="supported-blob-storage-features"></a>支援的 Blob 儲存體功能

下表顯示 Data Lake Storage Gen2 如何支援每個 Blob 儲存體功能。 有一個適用于 standard 和 [premium 效能](premium-tier-for-data-lake-storage.md) 層級的資料行。 

|Blob 儲存體功能 |標準 |Premium |相關文章 |
|---------------|-------------------|---|
|經常性存取層|正式推出|不支援|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|非經常性存取層|正式推出|不支援|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|事件|正式推出|正式推出|[回應 Blob 儲存體事件](storage-blob-event-overview.md)|
|計量 (傳統)|正式推出|正式推出|[Azure 儲存體分析計量 (傳統)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure 監視器中的計量|正式推出|預覽|[Azure 監視器中的 Azure 儲存體計量](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 儲存體 PowerShell 命令|正式推出|正式推出|[快速入門：使用 PowerShell 上傳、下載及列出 Blob](storage-quickstart-blobs-powershell.md)|
|Blob 儲存體 Azure CLI 命令|正式推出|正式推出|[快速入門：使用 Azure CLI 建立、下載及列出 Blob](storage-quickstart-blobs-cli.md)|
|Blob 儲存體 API|正式推出|正式推出|[快速入門：適用於 .NET 的 Azure Blob 儲存體用戶端程式庫 v12](storage-quickstart-blobs-dotnet.md)<br>[快速入門：使用 Java v12 SDK 來管理 Blob](storage-quickstart-blobs-java.md)<br>[快速入門：使用 Python v12 SDK 來管理 Blob](storage-quickstart-blobs-python.md)<br>[快速入門：使用 Node.js 中的 JavaScript v12 SDK 來管理 Blob](storage-quickstart-blobs-nodejs.md)|
|診斷記錄|正式推出|預覽 |[Azure 儲存體分析記錄](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (部分機器翻譯)|
|封存存取層|正式推出|不支援|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|生命週期管理原則 (分層) |正式推出|尚不支援|[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)|
|生命週期管理原則 (刪除 blob) |正式推出|正式推出|[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)|
|登入 Azure 監視器|預覽 |預覽|[監視 Azure 儲存體](./monitor-blob-storage.md)|
|快照集|預覽|預覽|[Blob 快照集](snapshots-overview.md)|
|靜態網站|預覽|預覽|[Azure 儲存體中的靜態網站裝載](storage-blob-static-website.md) \(機器翻譯\)|
|固定儲存體|預覽|預覽|[使用不可變儲存體儲存業務關鍵 Blob 資料](storage-blob-immutable-storage.md)|
|容器虛刪除|預覽|預覽|[適用于容器的虛刪除 (預覽) ](soft-delete-container-overview.md)|
|Azure 儲存體清查|預覽|預覽|[使用 Azure 儲存體清查來管理 blob 資料 (預覽) ](blob-inventory.md)|
|Blob 虛刪除|尚不支援|尚不支援|[Blob 的虛刪除](storage-blob-soft-delete.md)|
|Blob 虛刪除|尚不支援|尚不支援|[Blob 的虛刪除](./soft-delete-blob-overview.md)|
|Blobfuse|正式推出|正式推出|[如何使用 Blobfuse 將 Blob 儲存體掛接為檔案系統](storage-how-to-mount-container-linux.md)|
|客戶管理的帳戶容錯移轉|尚不支援|尚不支援|[災害復原和帳戶容錯移轉](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 容器 ACL|不支援<div role="complementary" aria-labelledby="blob-container-ACL"><sup>1</sup></div>|不支援<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|請參閱下表中的相關附注。|
|客戶提供的金鑰|尚不支援|尚不支援|[針對 Blob 儲存體的要求提供加密金鑰](encryption-customer-provided-keys.md)|
|自訂網域|尚不支援|尚不支援|[將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)|
|加密範圍|尚不支援|尚不支援|[建立及管理加密範圍 (預覽) ](encryption-scope-manage.md)|
|變更摘要|尚不支援|尚不支援|[Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)|
|物件複寫|尚不支援|尚不支援|[設定區塊 blob 的物件複寫](object-replication-configure.md)|
|Blob 版本設定|尚不支援|尚不支援|[啟用和管理 Blob 版本設定](versioning-enable.md)|

<div id="blob-container-ACL"><sup>1</sup> 您可以在容器的根資料夾（而不是容器本身）上設定 acl。</div><br>

<div id="preview-form"><sup>2</sup>若要搭配 Data Lake Storage Gen2 使用快照集、不可變的儲存體或靜態網站，您必須完成此 <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>表單</a>以註冊預覽。  </div>

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage Gen2 的已知問題](data-lake-storage-known-issues.md)
- [支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)
- [支援 Azure Data Lake Storage Gen2 的開放原始碼平台](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 上的多重通訊協定存取](data-lake-storage-multi-protocol-access.md)
