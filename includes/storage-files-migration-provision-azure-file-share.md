---
title: 布建 Azure 檔案共用的考慮
description: 布建 Azure 檔案共用以與 Azure 檔案同步搭配使用。跨遷移檔共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d48baba5ee60a2bf5a4cb5e4d1ce840fce8eec43
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143598"
---
Azure 檔案共用會儲存在雲端的 Azure 儲存體帳戶中。
這裡有另一層的效能考慮。

如果您有高度作用中的共用（許多使用者和/或應用程式所使用的共用），則兩個 Azure 檔案共用可能會達到儲存體帳戶的效能限制。

最佳做法是部署具有一個檔案共用的儲存體帳戶。
您可以將多個 Azure 檔案共用集區在同一個儲存體帳戶中，以防您擁有封存共用，或您預期在其中有較低的每日活動。

這些考慮適用于直接雲端存取（透過 Azure VM），而不是 Azure 檔案同步。如果您打算只在這些共用上使用 Azure 檔案同步，那麼將數個群組組成單一 Azure 儲存體帳戶就沒問題。

如果您已建立共用清單，您應該將每個共用對應至其所在的儲存體帳戶。

在上一個階段中，您已決定適當的共用數目。 在此步驟中，您已建立儲存體帳戶與檔案共用的對應。 現在部署適當數目的 Azure 儲存體帳戶，其中包含適當數目的 Azure 檔案共用。

請確定每個儲存體帳戶的區域都相同，而且符合您已部署的儲存體同步服務資源的區域。

> [!CAUTION]
> 如果您建立的 Azure 檔案共用具有 100 TiB 的限制，該共用只能使用本機的多餘儲存體或區域冗余儲存體的重複選項。 使用 100-TiB 檔案共用之前，請考慮您的儲存體多餘需求。

根據預設，Azure 檔案共用仍會以5個 TiB 的限制建立。 因為您正在建立新的儲存體帳戶，請務必遵循[指導方針來建立儲存體帳戶，以允許 Azure 檔案共用使用 100-TiB 限制](../articles/storage/files/storage-files-how-to-create-large-file-share.md)。

當您部署儲存體帳戶時，另一個考慮是 Azure 儲存體的冗余。 請參閱[Azure 儲存體的重複選項](../articles/storage/common/storage-redundancy.md)。

您的資源名稱也很重要。 例如，如果您將 HR 部門的多個共用群組到 Azure 儲存體帳戶中，您應該適當地命名儲存體帳戶。 同樣地，在命名 Azure 檔案共用時，您應該使用與其內部部署對應專案相似的名稱。