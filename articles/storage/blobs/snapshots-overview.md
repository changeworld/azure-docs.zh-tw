---
title: Blob 快照集
titleSuffix: Azure Storage
description: 瞭解如何建立 blob 的唯讀快照集，以便在指定的時間點備份 blob 資料。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cd8771afdc9c4462faaff758ffdd073382f69f53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550984"
---
# <a name="blob-snapshots"></a>Blob 快照集

快照集是在某個點時間取得的唯讀 Blob 版本。

> [!NOTE]
> Blob 版本設定（預覽）提供另一種方式來維護 blob 的歷程記錄複本。 如需詳細資訊，請參閱[Blob 版本設定（預覽）](versioning-overview.md)。

## <a name="about-blob-snapshots"></a>關於 blob 快照集

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Blob 的快照集與其基底 Blob 相同，除了 Blob URI 附加了 [日期時間] **** 值以表示擷取快照當時的時間。 例如，如果分頁 Blob URI 為 `http://storagesample.core.blob.windows.net/mydrives/myvhd`，則快照集 URI 類似於 `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`。

> [!NOTE]
> 所有快照集會共用基底 blob 的 URI。 基底 blob 與快照集之間的唯一差別在於附加的 **DateTime** 值。
>

Blob 可包含任意數目的快照集。 快照集會一直保存到明確刪除為止，這表示快照集無法 outlive 其基底 blob。 您可以列舉與基底 Blob 相關聯的快照集，以追蹤目前的快照集。

當您建立 Blob 的快照集時，Blob 的系統屬性都會使用相同值複製到快照集中。 基底 blob 的中繼資料也會複製到快照集，除非您在建立快照集時為其指定個別的中繼資料。 建立快照集後，您便可加以讀取、複製或刪除，但無法加以修改。

任何與基底 Blob 相關聯的租用不會影響快照集。 您無法取得快照集上的租用。

VHD 檔案是用來儲存 VM 磁碟目前的資訊和狀態。 您可以從 VM 內卸離磁碟或關閉 VM，然後製作其 VHD 檔案的快照集。 稍後您可以使用快照集檔案來擷取該時間點的 VHD 檔案，並重新建立 VM。

## <a name="understand-how-snapshots-accrue-charges"></a>了解快照集產生費用的方式

建立快照集 (即 Blob 的唯讀複本) 可能會為您的帳戶產生額外的資料儲存體費用。 設計您的應用程式時，請務必留意產生這些費用的可能方式，以便將成本降至最低。

### <a name="important-billing-considerations"></a>重要的計費考量

下列清單包含建立快照集時要考量的重點。

- 無論唯一的區塊或分頁是在 Blob 或快照集中，您的儲存體帳戶都會產生費用。 在您更新快照集所依據的 Blob 之前，您的帳戶不會針對與該 Blob 相關聯的快照集產生額外費用。 在您更新基底 blob 之後，它會與其快照集分離。 發生這種情況時，您需支付每個 blob 或快照集中唯一的區塊或頁面。
- 當您取代區塊 Blob 內的某個區塊時，後續即會將該區塊視為唯一區塊進行收費。 即使該區塊的區塊識別碼和資料與其在快照集中擁有的相同，也是如此。 再次認可該區塊之後，它就會與其在任何快照集中的對應項目分離，而您將需支付其資料的費用。 這同樣適用分頁 Blob 中以相同資料更新的頁面。
- 藉由呼叫 [UploadFromFile] [dotnet_UploadFromFile]、[UploadText] [dotnet_UploadText]、[UploadFromStream] [dotnet_UploadFromStream] 或 [UploadFromByteArray] [dotnet_UploadFromByteArray] 方法來取代 blob 中的所有區塊，以取代區塊 blob。 如果您的快照集與該 Blob 相關聯，基底 Blob 和快照集中的所有區塊現在都會分離出來，而您將針對這兩個 Blob 的所有區塊支付費用。 即使基底 Blob 和快照集中的資料保持一致，也是如此。
- Azure Blob 服務未提供方法來判斷兩個區塊是否包含相同資料。 已上傳且認可的每個區塊都會被視為唯一，即使它具有相同資料和相同的區塊識別碼也一樣。 由於費用是針對唯一區塊而產生，因此請務必注意，更新含有快照集的 Blob 會產生額外的唯一區塊及額外費用。

### <a name="minimize-cost-with-snapshot-management"></a>透過管理快照集將成本降到最低

我們建議您謹慎管理快照集，以避免產生額外費用。 您可以遵循以下最佳做法，協助您將快照集儲存體產生的成本降到最低︰

- 每當您更新 Blob 時，刪除並重新建立與該 Blob 相關聯的快照集，除非您的應用程式設計為需要維護快照集，否則即使您正以相同資料進行更新也一樣。 藉由刪除並重新建立 Blob 的快照集，讓您可以確保該 Blob 和快照集不會分離開來。
- 如果您要維護 blob 的快照集，請避免呼叫 [UploadFromFile] [dotnet_UploadFromFile]、[UploadText] [dotnet_UploadText]、[UploadFromStream] [dotnet_UploadFromStream] 或 [UploadFromByteArray] [dotnet_UploadFromByteArray] 來更新 blob。 這些方法會取代 Blob 中的所有區塊，造成您的基底 Blob 和其快照集明顯分離。 相反地，您可以使用 [PutBlock] [dotnet_PutBlock] 和 [PutBlockList] [dotnet_PutBlockList] 方法來更新最少的區塊數目。

### <a name="snapshot-billing-scenarios"></a>快照集計費案例

下列案例示範如何針對區塊 Blob 及其快照集產生費用。

#### <a name="scenario-1"></a>實例 1

在案例 1 中，基底 Blob 在擷取快照之後尚未更新，因此只會針對唯一區塊 1、2 和 3 產生費用。

![Azure 儲存體資源](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>案例 2

在案例 2 中，基底 Blob 已更新，但快照集並未更新。 區塊 3 已更新，而且即使它包含相同資料及相同的識別碼，它還是與快照集中的區塊 3 不一樣。 因此，此帳戶必須支付四個區塊的費用。

![Azure 儲存體資源](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>案例 3

在案例 3 中，基底 Blob 已更新，但快照集並未更新。 區塊 3 已使用基底 Blob 中的區塊 4 來取代，但快照集仍然反映區塊 3。 因此，此帳戶必須支付四個區塊的費用。

![Azure 儲存體資源](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>案例 4

在案例 4 中，基底 Blob 已完全更新，且未包含它的任何原始區塊。 因此，此帳戶必須支付所有八個唯一區塊的費用。 如果您使用如 [UploadFromFile] [dotnet_UploadFromFile]、[UploadText] [dotnet_UploadText]、[UploadFromStream] [dotnet_UploadFromStream] 或 [UploadFromByteArray] [dotnet_UploadFromByteArray] 的 update 方法，這種情況就會發生，因為這些方法會取代 blob 的所有內容。

![Azure 儲存體資源](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>後續步驟

- [在 .NET 中建立和管理 Blob 快照集](snapshots-manage-dotnet.md)
- [以遞增快照集備份 Azure 非受控 VM 磁碟](../../virtual-machines/windows/incremental-snapshots.md)
