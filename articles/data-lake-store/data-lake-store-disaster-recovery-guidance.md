---
title: Azure Data Lake Storage Gen1 的災害復原指導方針 | Microsoft Docs
description: 瞭解如何進一步保護您的資料免于整個區域中斷或意外刪除，超出 Azure Data Lake Storage Gen1 的本機多餘儲存體。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b124f828c4a6a019c45243528ed2d957e3f781f3
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191423"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Data Lake Storage Gen1 的高可用性和嚴重損壞修復指引

Data Lake Storage Gen1 提供本機的冗余儲存體 (LRS) 。 因此，您 Data Lake Storage Gen1 帳戶中的資料可透過自動化複本，彈性地處理資料中心內的暫時性硬體故障。 這可確保持久性與高可用性，符合 Data Lake Storage Gen1 的 SLA。 本文提供指引，說明如何進一步保護資料，使其不受罕見的全區停電或意外刪除事件影響。

## <a name="disaster-recovery-guidance"></a>災害復原指引

請務必準備嚴重損壞修復計畫。 請參閱本文中的資訊和這些額外的資源，協助您建立自己的計畫。

* [Azure 應用程式的災害復原和高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Azure 復原技術指導](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practice-recommendations"></a>最佳做法建議

我們建議您以符合災害復原計畫需求的頻率，將重要資料複製到位於其他區域的另一個 Data Lake Storage Gen1 帳戶。 有各種方法可以複製資料，包括 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、 [Azure PowerShell](data-lake-store-get-started-powershell.md)或 [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)。 Azure Data Factory 這項服務很適合用來反覆建立和部署資料移動管線。

發生區域性停電時，您就可以從資料所複製到的區域存取資料。 您可以監視 [Azure 服務健全狀況儀表板](https://azure.microsoft.com/status/)以確定全球各地 Azure 服務的狀態。

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>資料損毀或意外刪除復原指引

雖然 Data Lake Storage Gen1 可透過自動化複本提供資料復原功能，但這無法防止應用程式 (或開發人員/使用者) 的資料遭到損毀或意外刪除。

若要防止意外刪除，建議您先為 Data Lake Storage Gen1 帳戶設定正確的存取原則。 這包括套用 [Azure 資源鎖定](../azure-resource-manager/management/lock-resources.md) 來鎖定重要資源，以及使用可用的 [Data Lake Storage Gen1 安全性功能](data-lake-store-security-overview.md)來套用帳戶和檔案層級的存取控制。 此外，我們也建議您使用 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、[Azure PowerShell](data-lake-store-get-started-powershell.md) 或 [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)，在另一個 Data Lake Storage Gen1 帳戶、資料夾或 Azure 訂用帳戶定期建立重要資料的複本。 這可用來從資料損毀或刪除事件中復原。 Azure Data Factory 這項服務很適合用來反覆建立和部署資料移動管線。

您也可以啟用 Data Lake Storage Gen1 帳戶的 [診斷記錄](data-lake-store-diagnostic-logs.md) ，以收集資料存取 audit 線索。 「審核線索」提供可能已刪除或更新檔案之人員的相關資訊。

您可以嘗試使用適用于 Data Lake Storage Gen 1 的 [DataLakeStore](https://docs.microsoft.com/powershell/module/az.datalakestore/) Azure PowerShell 模組來還原已刪除的專案。 具體而言，請參閱 [Restore-AzDataLakeStoreDeletedItem](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem) 命令。 嘗試使用此命令之前，請務必先查看 [ [描述](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem#description) ] 區段。

## <a name="next-steps"></a>後續步驟

* [開始使用 Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [保護 Data Lake Storage Gen1 中的資料](data-lake-store-secure-data.md)
