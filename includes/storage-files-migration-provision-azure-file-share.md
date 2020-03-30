---
title: 預配 Azure 檔共用的注意事項。
description: 預配 Azure 檔共用，以便與 Azure 檔同步一起使用。在遷移文檔之間共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209424"
---
Azure 檔共用存儲在 Azure 存儲帳戶中的雲中。
這裡還有另一個級別的性能注意事項。

如果您有高度活躍的共用 - 許多使用者和/或應用程式使用的共用，則兩個 Azure 檔共用可能達到存儲帳戶的性能限制。

最佳做法是部署存儲帳戶，每個帳戶都有一個檔共用。
您可以將多個 Azure 檔共用池到同一存儲帳戶中，以防共用具有存檔共用，或者預期它們中的日常活動較低。

與應用於 Azure 檔同步相比，這些注意事項更多地應用於直接雲訪問（通過 Azure VM）。如果計畫在這些共用上僅使用 Azure 檔同步，則將多個帳戶分組到單個 Azure 存儲帳戶中可以。

如果您已列出共用，則應將每個共用映射到它們將駐留在的存儲帳戶。

在上一階段，您已經確定了適當的股票數量。 在此步驟中，您創建了存儲帳戶到檔共用的映射。 部署現在適當數量的 Azure 存儲帳戶，其中具有適當數量的 Azure 檔共用。

確保每個存儲帳戶的區域相同，並匹配已部署的存儲同步服務資源的區域。

> [!CAUTION]
> 如果創建 100 TiB 限制 Azure 檔共用，則該共用只能使用本地冗余存儲或區域冗余存儲冗余選項。 在使用 100 個 TiB 檔共用之前，請考慮存儲冗余需求。

預設情況下，Azure 檔共用仍以 5 TiB 限制創建。 由於您要創建新的存儲帳戶，請確保按照[該指南創建允許 Azure 檔共用的存儲帳戶，限制為 100 TiB。](../articles/storage/files/storage-files-how-to-create-large-file-share.md)

部署存儲帳戶時的另一個考慮因素是 Azure 存儲的冗余。 請參閱[：Azure 存儲冗余選項](../articles/storage/common/storage-redundancy.md)。

資源的名稱也很重要。 例如，如果將 HR 部門的多個共用分組到 Azure 存儲帳戶中，則應相應地命名存儲帳戶。 同樣，在命名 Azure 檔共用時，應使用與本地對應共用類似的名稱。