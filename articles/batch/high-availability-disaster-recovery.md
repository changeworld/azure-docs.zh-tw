---
title: 高可用性和嚴重損壞修復-Azure Batch
description: 瞭解如何設計 Batch 應用程式，以因應區域中斷情形。 工作負載應該容錯移轉至另一個區域，或在兩個或多個區域之間分割。
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: labrenne
ms.openlocfilehash: 84b0cce9557b4ae05586579f175cd0f5db14fdfc
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026076"
---
# <a name="design-your-application-for-high-availability"></a>針對高可用性設計應用程式

Azure Batch 是區域性服務。 Batch 在所有 Azure 區域均提供，但在建立 Batch 帳戶時，必須將其與區域相關聯。 Batch 帳戶的所有作業隨後會套用到該區域。 例如，集區和相關聯的虛擬機器 (VM) 會建立於 Batch 帳戶所在的相同區域中。

設計使用 Batch 的應用程式時，您必須考量 Batch 在某個區域中無法使用的可能性。 您有可能遇到罕見的情況：整個區域都發生問題、區域中的整個 Batch 服務發生問題，或您的特定 Batch 帳戶有問題。

如果使用 Batch 的應用程式或解決方案必須隨時可供使用，則應將其設計為可容錯移轉至另一個區域，或一律將工作負載拆分到兩個或更多區域之間。 這兩種方法都至少需要兩個 Batch 帳戶，且須分別位於不同的區域中。

## <a name="multiple-batch-accounts-in-multiple-regions"></a>多個 Batch 帳戶位於多個區域中

在不同區域中使用多個 Batch 帳戶，可讓您的應用程式在另一個區域中的 Batch 帳戶無法使用時，仍可繼續執行。 如果您的應用程式需要高可用性，則使用多個帳戶尤其重要。

在某些情況下，應用程式可能會設計成一律使用兩個或更多區域。 例如，當您需要相當多的容量時，可能必須使用多個區域才能處理大型應用程式，或因應未來的成長。

## <a name="design-considerations-for-providing-failover"></a>提供容錯移轉的設計考量

提供容錯移轉至替代區域的能力時所需考量的重點在於，解決方案中的所有元件都必須納入考量中；光靠設置第二個 Batch 帳戶是不夠的。 例如，大部分的 Batch 應用程式中都需要 Azure 儲存體帳戶，且儲存體帳戶和 Batch 帳戶必須位於相同的區域中，以達到可接受的效能。

設計可容錯移轉的解決方案時，請考慮下列幾點：

- 在每個區域中預先建立所有必要的帳戶，例如 Batch 帳戶和儲存體帳戶。 建立帳戶通常不會產生任何費用，只有在使用儲存的資料或帳戶時才需付費。
- 確實預先設定帳戶的配額，以便您使用 Batch 帳戶配置所需的核心數目。
- 使用範本和/或指令碼在區域中自動部署應用程式。
- 持續更新所有區域中的應用程式二進位檔和參考資料。 持續更新可確保區域能夠快速上線，而無須等候檔案上傳和部署。 例如，如果使用 Batch 應用程式套件來儲存和參考要在集區節點上安裝的自訂應用程式，則在新版本的應用程式產生後，應將其上傳至每個 Batch 帳戶，並藉由集區組態加以參考 (或使新版本成為預設版本)。
- 在呼叫 Batch、儲存體和任何其他服務的應用程式中，輕鬆地將用戶端或負載切換至不同的區域。
- 要確保容錯移轉能夠成功的最佳做法，是在進行正常作業時經常切換至替代區域。 例如，若有兩個部署位於不同的區域中，則每個月應切換至替代區域一次。

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何使用[Azure 入口網站](batch-account-create-portal.md)、 [Azure CLI](cli-samples.md)、 [PowerShell](batch-powershell-cmdlets-get-started.md)或[batch 管理 API](batch-management-dotnet.md)建立 batch 帳戶。
- 預設配額會與 Batch 帳戶相關聯；[本文](batch-quota-limit.md)提供預設配額值的詳細資訊，並說明如何增加配額。
