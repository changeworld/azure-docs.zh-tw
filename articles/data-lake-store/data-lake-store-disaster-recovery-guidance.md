---
title: Azure Data Lake Storage Gen1 的災害復原指導方針 | Microsoft Docs
description: 瞭解如何進一步保護您的資料免于整個區域中斷或超過本地 Azure Data Lake Storage Gen1 儲存體的意外刪除。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: fe5940083d04c1b5cc60b21834adf609e523e059
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666240"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Data Lake Storage Gen1 的高可用性和嚴重損壞修復指導方針

Data Lake Storage Gen1 提供本機冗余的儲存體 (LRS) 。 因此，您 Data Lake Storage Gen1 帳戶中的資料可透過自動化複本，復原資料中心內的暫時性硬體故障。 這可確保持久性與高可用性，符合 Data Lake Storage Gen1 的 SLA。 本文提供指引，說明如何進一步保護資料，使其不受罕見的全區停電或意外刪除事件影響。

## <a name="disaster-recovery-guidance"></a>災害復原指引

您必須準備嚴重損壞修復計畫。 請參閱本文中的資訊和這些額外的資源，以協助您建立自己的計畫。

* [Azure 應用程式的災害復原和高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Azure 復原技術指導](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practice-recommendations"></a>最佳做法建議

我們建議您以符合災害復原計畫需求的頻率，將重要資料複製到位於其他區域的另一個 Data Lake Storage Gen1 帳戶。 複製資料的方法有很多種，包括 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、 [Azure PowerShell](data-lake-store-get-started-powershell.md)或 [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)。 Azure Data Factory 這項服務很適合用來反覆建立和部署資料移動管線。

發生區域性停電時，您就可以從資料所複製到的區域存取資料。 您可以監視 [Azure 服務健全狀況儀表板](https://azure.microsoft.com/status/)以確定全球各地 Azure 服務的狀態。

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>資料損毀或意外刪除復原指引

雖然 Data Lake Storage Gen1 可透過自動化複本提供資料復原功能，但這無法防止應用程式 (或開發人員/使用者) 的資料遭到損毀或意外刪除。

若要防止意外刪除，建議您先為 Data Lake Storage Gen1 帳戶設定正確的存取原則。 這包括套用 [Azure 資源鎖定](../azure-resource-manager/management/lock-resources.md) ，以使用可用的 [Data Lake Storage Gen1 安全性功能](data-lake-store-security-overview.md)來鎖定重要資源，以及套用帳戶和檔案層級存取控制。 此外，我們也建議您使用 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、[Azure PowerShell](data-lake-store-get-started-powershell.md) 或 [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)，在另一個 Data Lake Storage Gen1 帳戶、資料夾或 Azure 訂用帳戶定期建立重要資料的複本。 這可用來從資料損毀或刪除事件中復原。 Azure Data Factory 這項服務很適合用來反覆建立和部署資料移動管線。

您也可以啟用 Data Lake Storage Gen1 帳戶的 [診斷記錄](data-lake-store-diagnostic-logs.md) ，以收集資料存取審核線索。 「審核線索」會提供可能已刪除或更新檔案的相關資訊。

## <a name="next-steps"></a>後續步驟

* [開始使用 Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [保護 Data Lake Storage Gen1 中的資料](data-lake-store-secure-data.md)
