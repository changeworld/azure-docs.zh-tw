---
title: 高可用性和災害復原
description: 了解如何設計 Batch 應用程式以因應區域性中斷。
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831001"
---
# <a name="design-your-batch-application-for-high-availability"></a>設計 Batch 應用程式以獲得高可用性

Azure Batch 可在所有 Azure 區域中使用，但在建立 Batch 帳戶時，必須與一個特定區域相關聯。 Batch 帳戶的所有作業隨後會套用到該區域。 例如，集區和相關聯的虛擬機器 (VM) 會建立於 Batch 帳戶所在的相同區域中。

設計使用 Batch 的應用程式時，您必須考量 Batch 在某個區域中無法使用的可能性。 當整個區域、區域中的整個 Batch 服務或您的特定 Batch 帳戶發生問題時，可能會遇到罕見的情況。

如果使用 Batch 的應用程式或解決方案必須隨時可供使用，則應將其設計為可容錯移轉至另一個區域，或一律將工作負載拆分到兩個或更多區域之間。 這兩種方法都至少需要兩個 Batch 帳戶，且須分別位於不同的區域中。

## <a name="multiple-batch-accounts-in-multiple-regions"></a>多個 Batch 帳戶位於多個區域中

在不同區域中使用多個 Batch 帳戶，可讓您的應用程式在某個區域中的 Batch 帳戶變成無法使用時繼續執行。 如果您的應用程式需要高可用性，則有多個帳戶特別重要。

在某些情況下，應用程式可能會設計成刻意使用兩個或更多區域。 例如，當您需要相當多的容量時，可能必須使用多個區域才能處理大型應用程式，或因應未來的成長。 這些應用程式也會需要多個 Batch 帳戶 () 所使用的每個區域一個。

## <a name="design-considerations-for-providing-failover"></a>提供容錯移轉的設計考量

當提供容錯移轉至替代區域的能力時，解決方案中的所有元件都必須考慮;只要有第二個 Batch 帳戶就夠了。 例如，大部分的 Batch 應用程式中都需要 Azure 儲存體帳戶，且儲存體帳戶和 Batch 帳戶必須位於相同的區域中，以達到可接受的效能。

設計可容錯移轉的解決方案時，請考慮下列幾點：

- 在每個區域中預先建立所有必要的帳戶，例如 Batch 帳戶和儲存體帳戶。 建立帳戶通常不會產生任何費用，而且只有在使用帳戶或儲存資料時，才會產生費用。
- 請先確定已在所有帳戶上設定適當的 [配額](batch-quota-limit.md) ，以便您可以使用 Batch 帳戶配置所需的核心數目。
- 使用範本和/或指令碼在區域中自動部署應用程式。
- 持續更新所有區域中的應用程式二進位檔和參考資料。 持續更新可確保區域能夠快速上線，而無須等候檔案上傳和部署。 例如，如果使用 Batch 應用程式套件來儲存和參考要在集區節點上安裝的自訂應用程式，則在新版本的應用程式產生後，應將其上傳至每個 Batch 帳戶，並藉由集區組態加以參考 (或使新版本成為預設版本)。
- 在呼叫批次、儲存體和任何其他服務的應用程式中，可讓您輕鬆地將用戶端切換到不同的區域，或將負載切換到不同的區域。
- 請考慮經常切換到替代區域，作為正常操作的一部分。 例如，在不同區域中的兩個部署中，每個月切換至替代區域。

## <a name="next-steps"></a>後續步驟

- 了解如何使用 [Azure 入口網站](batch-account-create-portal.md)、[Azure CLI](./scripts/batch-cli-sample-create-account.md)、[PowerShell](batch-powershell-cmdlets-get-started.md) 或 [Batch Management API](batch-management-dotnet.md) 建立 Batch 帳戶。
- 瞭解 [與 Batch 帳戶相關聯的預設配額](batch-quota-limit.md) ，以及如何增加配額。
