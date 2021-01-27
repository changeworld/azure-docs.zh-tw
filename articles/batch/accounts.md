---
title: Batch 帳戶和 Azure 儲存體帳戶
description: 從開發觀點了解 Azure Batch 帳戶以及如何使用。
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 83108a265f91c9feef2fab424f1819939c2d58c9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896743"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Batch 帳戶和 Azure 儲存體帳戶

Azure Batch 帳戶是 Batch 服務內唯一識別的實體。 大部分的 Batch 解決方案會使用 [Azure 儲存體](../storage/index.yml)來儲存資源檔和輸出檔案，因此每個 Batch 帳戶通常會與對應的儲存體帳戶相關聯。

## <a name="batch-accounts"></a>Batch 帳戶

所有處理和資源都與 Batch 帳戶相關聯。 當您的應用程式對 Batch 服務提出要求時，可以使用 Azure Batch 帳戶名稱、帳戶的 URL 及存取金鑰或 Azure Active Directory 權杖，來驗證此要求。

您可以在單一 Batch 帳戶中執行多個 Batch 工作負載。 您也可以在 Batch 帳戶之間散佈工作負載，這些帳戶是在相同訂用帳戶中，但是位於不同的 Azure 區域。

您可以使用 [Azure 入口網站](batch-account-create-portal.md)或以程式設計的方式建立 Batch 帳戶，例如使用 [Batch管理 .NET 程式庫](batch-management-dotnet.md)。 建立帳戶時，您可以將 Azure 儲存體帳戶產生關聯，以儲存作業相關的輸入和輸出資料或應用程式。

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]


## <a name="azure-storage-accounts"></a>Azure 儲存體帳戶

大部分 Batch 解決方案都使用 Azure 儲存體來儲存資源檔和輸出檔。 例如，您的 Batch 工作 (包括標準工作、啟動工作、作業準備工作和作業發行工作) 通常會指定位於儲存體帳戶中的資源檔案。 儲存體帳戶也會儲存已處理的資料，以及任何產生的輸出資料。

Batch 支援下列類型的「Azure 儲存體」帳戶：

- 一般用途 v2 (GPv2) 帳戶
- 一般用途 v1 (GPv1) 帳戶
- Blob 儲存體帳戶 (目前支援虛擬機器組態中的集區)

如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../storage/common/storage-account-overview.md)。

您可以在建立 Batch 帳戶時或在稍後，建立儲存體帳戶與 Batch 帳戶的關聯。 在選擇儲存體帳戶時，請考慮您的成本和效能需求。 例如，相較於 GPv1，GPv2 和 Blob 儲存體帳戶選項支援更大的[容量和延展性限制](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。 (請連絡 Azure 支援以要求增加儲存體限制。)對於包含讀取自或寫入至儲存體帳戶之大量平行工作的 Batch 解決方案，這些帳戶選項可以改善其效能。

## <a name="next-steps"></a>後續步驟

- 深入了解[節點和集區](nodes-and-pools.md)。
- 瞭解如何使用 [Azure 入口網站](batch-account-create-portal.md) 或 [batch 管理 .net](batch-management-dotnet.md)來建立和管理 batch 帳戶。
- 瞭解如何使用 [私人端點](private-connectivity.md) 搭配 Azure Batch 帳戶。
