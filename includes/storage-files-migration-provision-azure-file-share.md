---
title: 布建 Azure 檔案共用的考慮
description: 布建 Azure 檔案共用以搭配 Azure 檔案同步使用。共通的文字區塊，會跨遷移檔共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d48baba5ee60a2bf5a4cb5e4d1ce840fce8eec43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143598"
---
Azure 檔案共用會儲存在雲端的 Azure 儲存體帳戶中。
這裡有另一層的效能考慮。

如果您有許多使用者及/或應用) 程式所使用的高度主動式共用 (共用，則兩個 Azure 檔案共用可能會達到儲存體帳戶的效能限制。

最佳做法是部署儲存體帳戶，每個都有一個檔案共用。
您可以將多個 Azure 檔案共用集區放在相同的儲存體帳戶中，以防您有封存共用，或您預期在這些共用中有較低的日常活動。

這些考慮適用于透過 Azure VM) 的直接雲端存取 (，而不是 Azure 檔案同步。如果您打算只在這些共用上使用 Azure 檔案同步，請將多個群組分組成單一 Azure 儲存體帳戶。

如果您已建立共用清單，您應該將每個共用對應至其所在的儲存體帳戶。

在上一個階段中，您已決定適當的共用數目。 在此步驟中，您已建立儲存體帳戶與檔案共用的對應。 現在部署適當數量的 Azure 儲存體帳戶，其中包含適當的 Azure 檔案共用數目。

請確定每個儲存體帳戶的區域都相同，並符合您已部署之儲存體同步服務資源的區域。

> [!CAUTION]
> 如果您建立的 Azure 檔案共用有 100 TiB 的限制，該共用只能使用本機多餘的儲存體或區域冗余的儲存體冗余選項。 使用 100 TiB 檔案共用之前，請先考慮您的儲存體冗余需求。

預設仍會使用5個 TiB 的限制建立 Azure 檔案共用。 因為您正在建立新的儲存體帳戶，請務必遵循 [指導方針來建立儲存體帳戶，以允許具有 100-TiB 限制的 Azure 檔案共用](../articles/storage/files/storage-files-how-to-create-large-file-share.md)。

當您部署儲存體帳戶時，另一個考慮是 Azure 儲存體的冗余。 請參閱 [Azure 儲存體冗余選項](../articles/storage/common/storage-redundancy.md)。

您的資源名稱也很重要。 例如，如果您將 HR 部門的多個共用組成 Azure 儲存體帳戶，您應該適當地命名儲存體帳戶。 同樣地，當命名 Azure 檔案共用時，您應該使用與用於其內部部署對應專案的名稱類似的名稱。