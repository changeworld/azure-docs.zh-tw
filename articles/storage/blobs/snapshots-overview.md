---
title: Blob 快照集
titleSuffix: Azure Storage
description: 瞭解 blob 快照集的運作方式，以及它們的計費方式。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ab4c152f30ab96fe5e221a605a2339c773e32547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295390"
---
# <a name="blob-snapshots"></a>Blob 快照集

快照集是在某個點時間取得的唯讀 Blob 版本。

> [!NOTE]
> Blob 版本設定提供更佳的方式來維護舊版的 blob。 如需詳細資訊，請參閱 [Blob 版本](versioning-overview.md)設定。

## <a name="about-blob-snapshots"></a>關於 blob 快照集

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Blob 的快照集與其基底 Blob 相同，除了 Blob URI 附加了 [日期時間] **** 值以表示擷取快照當時的時間。 例如，如果分頁 Blob URI 為 `http://storagesample.core.blob.windows.net/mydrives/myvhd`，則快照集 URI 類似於 `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`。

> [!NOTE]
> 所有快照集會共用基底 blob 的 URI。 基底 blob 與快照集之間的唯一差別在於附加的 **DateTime** 值。

Blob 可包含任意數目的快照集。 快照集會保存到明確地刪除為止，因為它們會獨立或作為基底 blob 之 [刪除 Blob](/rest/api/storageservices/delete-blob) 作業的一部分。 您可以列舉與基底 Blob 相關聯的快照集，以追蹤目前的快照集。

當您建立 Blob 的快照集時，Blob 的系統屬性都會使用相同值複製到快照集中。 基底 blob 的中繼資料也會複製到快照集，除非您在建立快照集時為其指定個別的中繼資料。 建立快照集後，您便可加以讀取、複製或刪除，但無法加以修改。

任何與基底 Blob 相關聯的租用不會影響快照集。 您無法取得快照集上的租用。

VHD 檔案是用來儲存 VM 磁碟目前的資訊和狀態。 您可以從 VM 內卸離磁碟或關閉 VM，然後製作其 VHD 檔案的快照集。 稍後您可以使用快照集檔案來擷取該時間點的 VHD 檔案，並重新建立 VM。

## <a name="understand-how-snapshots-accrue-charges"></a>了解快照集產生費用的方式

### <a name="important-billing-considerations"></a>重要的計費考量

下列清單包含建立快照集時要考量的重點。

- 無論唯一的區塊或分頁是在 Blob 或快照集中，您的儲存體帳戶都會產生費用。 在您更新快照集所依據的 Blob 之前，您的帳戶不會針對與該 Blob 相關聯的快照集產生額外費用。 在您更新基底 blob 之後，它會與其快照集分離。 發生這種情況時，您需支付每個 blob 或快照集中唯一的區塊或頁面。
- 當您取代區塊 Blob 內的某個區塊時，後續即會將該區塊視為唯一區塊進行收費。 即使該區塊的區塊識別碼和資料與其在快照集中擁有的相同，也是如此。 再次認可該區塊之後，它就會與其在任何快照集中的對應項目分離，而您將需支付其資料的費用。 這同樣適用分頁 Blob 中以相同資料更新的頁面。
- 藉由呼叫覆寫 blob 的整個內容的方法來更新區塊 blob，將會取代 blob 中的所有區塊。 如果您的快照集與該 Blob 相關聯，基底 Blob 和快照集中的所有區塊現在都會分離出來，而您將針對這兩個 Blob 的所有區塊支付費用。 即使基底 Blob 和快照集中的資料保持一致，也是如此。
- Azure Blob 服務未提供方法來判斷兩個區塊是否包含相同資料。 已上傳且認可的每個區塊都會被視為唯一，即使它具有相同資料和相同的區塊識別碼也一樣。 由於費用是針對唯一區塊而產生，因此請務必注意，更新含有快照集的 Blob 會產生額外的唯一區塊及額外費用。

### <a name="minimize-costs-with-snapshot-management"></a>使用快照集管理將成本降至最低

我們建議您謹慎管理快照集，以避免產生額外費用。 您可以遵循以下最佳做法，協助您將快照集儲存體產生的成本降到最低︰

- 每當您更新 Blob 時，刪除並重新建立與該 Blob 相關聯的快照集，除非您的應用程式設計為需要維護快照集，否則即使您正以相同資料進行更新也一樣。 藉由刪除並重新建立 Blob 的快照集，讓您可以確保該 Blob 和快照集不會分離開來。
- 如果您要維護 blob 的快照集，請避免在更新 blob 時呼叫覆寫整個 blob 的方法。 相反地，請更新最少的區塊數目，以維持低成本。

### <a name="snapshot-billing-scenarios"></a>快照集計費案例

下列案例示範如何針對區塊 Blob 及其快照集產生費用。

## <a name="pricing-and-billing"></a>價格和計費

建立快照集 (即 Blob 的唯讀複本) 可能會為您的帳戶產生額外的資料儲存體費用。 設計您的應用程式時，請務必留意產生這些費用的可能方式，以便將成本降至最低。

Blob 快照集（例如 blob 版本）的計費方式與使用中資料的費率相同。 快照集的計費方式取決於您是否已明確設定基底 blob 的層級，或其任何快照集 (或) 的版本。 如需 blob 層的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取層、非經常性存取層，以及封存存取層](storage-blob-storage-tiers.md)。

如果您未變更 blob 或快照集層，則會向您收取跨該 blob 的唯一資料區塊、其快照集，以及任何可能擁有的版本。 如需詳細資訊，請參閱 [未明確設定 blob 層時的計費](#billing-when-the-blob-tier-has-not-been-explicitly-set)。

如果您已變更 blob 或快照集的層級，則會向您收取整個物件的費用，而不論 blob 和快照集最終是否會再次在相同的階層中。 如需詳細資訊，請參閱在 [明確設定 blob 層時的計費](#billing-when-the-blob-tier-has-been-explicitly-set)。

如需 blob 版本計費詳細資料的詳細資訊，請參閱 [blob 版本](versioning-overview.md)設定。

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>未明確設定 blob 層時的帳單

如果您未明確設定基底 blob 的 blob 層或其任何快照集，則會向您收取唯一的區塊或頁面的快照集、其快照集，以及任何可能擁有的版本。 跨 blob 和其快照集共用的資料只需支付一次。 更新 blob 時，基底 blob 中的資料會從其快照中儲存的資料分歧，而唯一的資料會依每個區塊或頁面收費。

當您取代區塊 Blob 內的某個區塊時，後續即會將該區塊視為唯一區塊進行收費。 即使該區塊的區塊識別碼和資料與其在快照集中擁有的相同，也是如此。 當封鎖再次認可之後，它會從快照中的對應項分歧，並向您收取其資料的費用。 這同樣適用分頁 Blob 中以相同資料更新的頁面。

Blob 儲存體沒有方法可判斷兩個區塊是否包含相同的資料。 已上傳且認可的每個區塊都會被視為唯一，即使它具有相同資料和相同的區塊識別碼也一樣。 因為唯一的區塊會產生費用，請務必記住，當 blob 有快照集或版本時，更新 blob 將會導致額外的唯一區塊及額外費用。

當 blob 有快照集時，請呼叫區塊 blob 上的更新作業，讓它們更新可能的區塊數目上限。 允許對區塊進行細微控制的寫入作業是 [放置區塊](/rest/api/storageservices/put-block) 和 [放置區塊清單](/rest/api/storageservices/put-block-list)。 另一方面， [Put blob](/rest/api/storageservices/put-blob) 作業會取代整個 blob 的內容，因此可能會導致額外的費用。

下列案例示範在未明確設定 blob 層時，如何針對區塊 blob 和其快照集產生費用。

#### <a name="scenario-1"></a>實例 1

在案例 1 中，基底 Blob 在擷取快照之後尚未更新，因此只會針對唯一區塊 1、2 和 3 產生費用。

![圖1顯示基底 blob 和快照中唯一區塊的計費。](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>案例 2

在案例 2 中，基底 Blob 已更新，但快照集並未更新。 區塊 3 已更新，而且即使它包含相同資料及相同的識別碼，它還是與快照集中的區塊 3 不一樣。 因此，此帳戶必須支付四個區塊的費用。

![圖2顯示基底 blob 和快照中唯一區塊的計費。](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>案例 3

在案例 3 中，基底 Blob 已更新，但快照集並未更新。 區塊 3 已使用基底 Blob 中的區塊 4 來取代，但快照集仍然反映區塊 3。 因此，此帳戶必須支付四個區塊的費用。

![圖3顯示基底 blob 和快照中唯一區塊的計費。](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>案例 4

在案例 4 中，基底 Blob 已完全更新，且未包含它的任何原始區塊。 因此，此帳戶必須支付所有八個唯一區塊的費用。

![圖4顯示基底 blob 和快照中唯一區塊的計費。](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> 避免呼叫覆寫整個 blob 的方法，並改為更新個別的區塊，以降低成本。

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>在明確設定 blob 層時進行計費

如果您已明確設定 blob 或快照集 (或版本) 的 blob 層，則會向您收取新層中物件的完整內容長度的費用，不論它是否與原始層中的物件共用區塊。 您也必須支付原始層中最舊版本的完整內容長度。 保留在原始階層中的任何版本或快照集會針對它們可共用的唯一區塊收費，如 [未明確設定 blob 層時的計費](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

#### <a name="moving-a-blob-to-a-new-tier"></a>將 blob 移至新的層

下表說明將 blob 或快照移至新階層時的計費行為。

| 當 blob 層明確設定時 .。。 | 然後您需要支付 .。。 |
|-|-|
| 具有快照集的基底 blob | 新層中的基底 blob 和原始層中最舊的快照集，再加上其他快照集中的任何唯一區塊。<sup>1</sup> |
| 具有舊版和快照集的基底 blob | 新階層中的基底 blob、原始階層中最舊的版本，以及原始層中最舊的快照集，再加上其他版本或快照<sup>1</sup>中的任何唯一區塊。 |
| 快照集 | 新階層中的快照集和原始層次中的基底 blob，以及其他快照集中的任何唯一區塊。<sup>1</sup> |

<sup>1</sup>如果有其他先前的版本或尚未從其原始階層移動的快照集，則會根據所包含的唯一區塊數目來收費這些版本或快照集，如 [未明確設定 blob 層的計費](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

下圖說明當具有快照集的 blob 移至不同的層級時，物件的計費方式。

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="此圖顯示當具有快照集的 blob 明確分層時，物件如何計費。":::

明確設定 blob、版本或快照集的層級無法復原。 如果您將 blob 移至新的階層，然後再將它移回其原始階層，則會向您收取物件的完整內容長度，即使它與原始層中的其他物件共用區塊也一樣。

明確設定 blob、版本或快照集層的作業包括：

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- 使用指定的階層[放置 Blob](/rest/api/storageservices/put-blob)
- 使用指定的層級[放置區塊清單](/rest/api/storageservices/put-block-list)
- [複製](/rest/api/storageservices/copy-blob) 具有指定層的 Blob

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>啟用虛刪除時刪除 blob

啟用 blob 虛刪除時，如果您刪除或覆寫已明確設定其層級的基底 blob，則任何先前的虛刪除 blob 版本或快照集會以完整內容長度計費。 如需 blob 版本控制和虛刪除如何一起運作的詳細資訊，請參閱 [blob 版本設定和虛刪除](versioning-overview.md#blob-versioning-and-soft-delete)。

下表描述已虛刪除之 blob 的計費行為，取決於是否已啟用或停用版本控制。 啟用版本控制時，會在將 blob 虛刪除時建立新的版本。 停用版本控制時，虛刪除 blob 會建立虛刪除快照集。

| 當您以明確設定的層級覆寫基底 blob 時 .。。 | 然後您需要支付 .。。 |
|-|-|
| 如果同時啟用 blob 虛刪除和版本控制 | 所有具有完整內容長度的現有版本（不論層級為何）。 |
| 如果已啟用 blob 虛刪除，但停用版本設定 | 所有現有的虛刪除快照集，不論層級為何，都有完整的內容長度。 |

## <a name="next-steps"></a>後續步驟

- [Blob 版本設定](versioning-overview.md)
- [在 .NET 中建立和管理 Blob 快照集](snapshots-manage-dotnet.md)
- [以遞增快照集備份 Azure 非受控 VM 磁碟](../../virtual-machines/windows/incremental-snapshots.md)
