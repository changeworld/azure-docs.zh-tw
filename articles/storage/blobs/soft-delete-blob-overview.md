---
title: Blob 的虛刪除
titleSuffix: Azure Storage
description: Blob 的虛刪除可保護您的資料，讓您在應用程式或其他儲存體帳戶使用者錯誤地修改或刪除資料時，可以更輕鬆地復原資料。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a6fc1d6b831ae794907c59ab1af3328902f3a70a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230104"
---
# <a name="soft-delete-for-blobs"></a>Blob 的虛刪除

Blob 的虛刪除可保護您的資料免于不慎或錯誤地修改或刪除。 針對儲存體帳戶啟用 blob 的虛刪除時，該儲存體帳戶中的 blob、blob 版本和快照集，在您指定的保留期限內可能會復原。

如果您的資料有可能意外遭到應用程式或其他儲存體帳戶使用者修改或刪除，Microsoft 建議您開啟虛刪除。 如需啟用虛刪除的詳細資訊，請參閱 [啟用和管理 blob 的虛刪除](soft-delete-enable.md)。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>關於 blob 的虛刪除

在儲存體帳戶上啟用 blob 的虛刪除時，您可以在指定的資料保留期限內，復原已刪除的物件。 這項保護會延伸至任何 blob (區塊 blob、附加 blob 或分頁 blob) ，因為覆寫會將其清除。

如果在啟用 blob 虛刪除時刪除現有 blob 或快照集中的資料，但未啟用 blob 版本設定，則會產生虛刪除的快照集，以儲存覆寫資料的狀態。 指定的保留期限到期後，就會永久刪除物件。

如果儲存體帳戶上已啟用 blob 版本設定和 blob 虛刪除，則刪除 blob 會建立新的版本，而不是虛刪除的快照集。 當虛刪除保留期限到期時，新版本不會被虛刪除，且不會移除。 您可以藉由呼叫取消 [刪除 blob](/rest/api/storageservices/undelete-blob) 作業，在保留期限內還原 blob 的虛刪除版本。 接著，您可以藉由呼叫 [複製 blob](/rest/api/storageservices/copy-blob) 作業，從它的其中一個版本還原 blob。 如需使用 blob 版本設定和虛刪除的詳細資訊，請參閱 [blob 版本設定和虛刪除](versioning-overview.md#blob-versioning-and-soft-delete)。

虛刪除的物件是不可見的，除非明確列出。

Blob 虛刪除具有回溯相容性，因此您不需要對應用程式進行任何變更，就能利用這項功能提供的保護。 不過，[資料復原](#recovery)導入了新的**取消刪除 Blob** API。

Blob 虛刪除適用于新的和現有的一般用途 v2、一般用途 v1 和 Blob 儲存體帳戶。 支援標準和 premium 帳戶類型。 Blob 虛刪除適用于所有儲存層，包括經常性存取、非經常性存取和封存。 虛刪除適用于非受控磁片，也就是分頁 blob，但不適用於受控磁片。

### <a name="configuration-settings"></a>組態設定

當您建立新帳戶時，預設會停用虛刪除。 針對現有的儲存體帳戶，虛刪除也預設為停用。 您可以隨時啟用或停用儲存體帳戶的虛刪除。

當您啟用虛刪除時，您必須設定保留期限。 保留期限能指出系統儲存虛刪除資料而可供您復原的時間長度。 若為明確刪除的物件，則會在刪除資料時啟動保留週期時鐘。 若為虛刪除功能在覆寫資料時所產生的虛刪除版本或快照集，則會在產生版本或快照集時開始計時。 保留期限可以介於1到365天之間。

您可以隨時變更虛刪除保留期限。 更新的保留期限僅適用于新刪除的資料。 先前刪除的資料會根據資料刪除時所設定的保留期限到期。 嘗試刪除虛刪除的物件並不會影響其到期時間。

如果您停用虛刪除，您可以在啟用此功能時，繼續存取及復原已儲存之儲存體帳戶中已虛刪除的資料。

### <a name="saving-deleted-data"></a>儲存已刪除的資料

虛刪除會在刪除或覆寫物件的許多情況下保存您的資料。

使用 **Put blob**、 **put Block List**或 **Copy blob**覆寫 blob 時，系統會自動產生寫入作業之前的 blob 狀態版本或快照集。 除非明確列出虛刪除的物件，否則不會顯示此物件。 請參閱[復原](#recovery)一節，以了解如何列出虛刪除的物件。

![此圖顯示 blob 快照集在使用 Put Blob、Put 區塊清單或複製 Blob 覆寫時如何儲存。](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

*虛刪除的資料是灰色的，而作用中的資料則是藍色。最近寫入的資料會出現在較舊資料的下方。當 B0 以 B1 覆寫時，會產生 B0 的虛刪除快照集。當 B1 以 B2 覆寫時，會產生 B1 的虛刪除快照集。*

> [!NOTE]  
> 只有為目的地 Blob 的帳戶開啟虛刪除時，虛刪除才會提供複製作業的覆寫保護。

> [!NOTE]  
> 虛刪除不會為封存層中的 Blob 提供覆寫保護。 如果以任何層中的新 Blob 覆寫已封存的 Blob，被覆寫的 Blob 將會永久過期。

對快照集呼叫**刪除 Blob** 時，該快照集將會標示為已虛刪除。 此時不會產生新的快照集。

![此圖顯示使用刪除 Blob 時如何虛刪除 blob 快照集。](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*虛刪除的資料是灰色的，而作用中的資料則是藍色。最近寫入的資料會出現在較舊資料的下方。呼叫 **快照集 Blob** 時，B0 會變成快照集，而 B1 是 Blob 的作用中狀態。當刪除 B0 快照集時，它會標示為已虛刪除。*

對基底 Blob (本身不是快照集的任何 Blob) 呼叫**刪除 Blob** 時，該 Blob 將會標示為已虛刪除。 與先前的行為一致，對具有作用中快照集的 Blob 呼叫**刪除 Blob**，將會傳回錯誤。 對具有虛刪除快照集的 Blob 呼叫**刪除 Blob**，則不會傳回錯誤。 在虛刪除開啟時，您仍可在單一作業中刪除 Blob 及其所有的快照集。 執行此動作會將基底 Blob 和快照集標示為已虛刪除。

![此圖顯示在基底 blob 上呼叫刪除 Blog 時所發生的情況。](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

*虛刪除的資料是灰色的，而作用中的資料則是藍色。最近寫入的資料會出現在較舊資料的下方。在這裡，會進行 **刪除的 Blob** 呼叫，以刪除 B2 和所有相關聯的快照集。作用中的 blob、B2 和所有相關聯的快照集會標示為已虛刪除。*

> [!NOTE]  
> 在覆寫已虛刪除的 Blob 時，將會自動產生該 Blob 在寫入作業之前所處狀態的虛刪除快照集。 覆寫的 Blob 層會由新的 Blob 繼承。

如果是刪除容器或帳戶，或是覆寫了 Blob 中繼資料和 Blob 屬性，虛刪除將不會儲存您的資料。 若要防止儲存體帳戶誤遭刪除，您可以使用 Azure Resource Manager 設定鎖定。 如需詳細資訊，請參閱 Azure Resource Manager 文章 [鎖定資源以防止非預期的變更](../../azure-resource-manager/management/lock-resources.md)。

下表將詳細說明虛刪除開啟時的預期行為：

| REST API 作業 | 資源類型 | 描述 | 行為變更 |
|--------------------|---------------|-------------|--------------------|
| [刪除](/rest/api/storagerp/StorageAccounts/Delete) | 帳戶 | 刪除儲存體帳戶，包括其中包含的所有容器和 Blob。                           | 沒有變更。 已刪除之帳戶中的容器和 Blob 無法復原。 |
| [刪除容器](/rest/api/storageservices/delete-container) | 容器 | 刪除容器，包括其中包含的所有 Blob。 | 沒有變更。 已刪除之容器中的 Blob 無法復原。 |
| [放置 Blob](/rest/api/storageservices/put-blob) | 區塊、附加和分頁 blob | 建立新的 Blob，或取代容器內現有的 Blob | 如果用來取代現有的 Blob，則會自動產生該 Blob 在呼叫之前所處狀態的快照集。 這也適用于先前已虛刪除的 blob，但僅適用于使用相同類型的 blob （ (區塊、附加或頁面) 所取代）。 如果取代為不同類型的 Blob，則所有已虛刪除的現有資料將會永久過期。 |
| [刪除 Blob](/rest/api/storageservices/delete-blob) | 區塊、附加和分頁 blob | 將 Blob 或 Blob 快照集標示為要刪除。 Blob 或快照集會在後續的記憶體回收期間刪除 | 如果用來刪除 Blob 快照集，該快照集將會標示為已虛刪除。 如果用來刪除 Blob，該 Blob 將會標示為已虛刪除。 |
| [複製 Blob](/rest/api/storageservices/copy-blob) | 區塊、附加和分頁 blob | 將來源 Blob 複製到相同儲存體帳戶或其他儲存體帳戶中的目的地 Blob。 | 如果用來取代現有的 Blob，則會自動產生該 Blob 在呼叫之前所處狀態的快照集。 這也適用于先前已虛刪除的 blob，但僅適用于使用相同類型的 blob （ (區塊、附加或頁面) 所取代）。 如果取代為不同類型的 Blob，則所有已虛刪除的現有資料將會永久過期。 |
| [放置區塊](/rest/api/storageservices/put-block) | 區塊 Blob | 建立要認可作為區塊 Blob 一部分的新區塊。 | 如果用來將區塊認可至使用中的 blob，則不會有任何變更。 如果用來認可區塊，且其目標 Blob 已虛刪除，則會建立新的 Blob，並自動產生快照集，以擷取已虛刪除之 Blob 的狀態。 |
| [放置區塊清單](/rest/api/storageservices/put-block-list) | 區塊 Blob | 藉由指定包含區塊 Blob 的區塊集識別碼來認可 Blob。 | 如果用來取代現有的 Blob，則會自動產生該 Blob 在呼叫之前所處狀態的快照集。 這也適用于先前已虛刪除的 blob （如果它是區塊 blob 的話）。 如果取代為不同類型的 Blob，則所有已虛刪除的現有資料將會永久過期。 |
| [放置頁面](/rest/api/storageservices/put-page) | 分頁 Blob | 將一系列頁面寫入分頁 blob。 | 沒有變更。 使用此作業覆寫或清除的分頁 blob 資料不會儲存，且無法復原。 |
| [附加區塊](/rest/api/storageservices/append-block) | 附加 Blob | 將資料區塊寫入至附加 blob 的結尾 | 沒有變更。 |
| [設定 Blob 屬性](/rest/api/storageservices/set-blob-properties) | 區塊、附加和分頁 blob | 設定為 Blob 定義之系統屬性的值。 | 沒有變更。 覆寫的 Blob 屬性無法復原。 |
| [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata) | 區塊、附加和分頁 blob | 將指定 Blob 的使用者定義中繼資料設為一或多個名稱/值配對。 | 沒有變更。 覆寫的 Blob 中繼資料無法復原。 |

請務必注意，呼叫 **Put 頁面** 以覆寫或清除分頁 blob 的範圍將不會自動產生快照集。 虛擬機器磁片是由分頁 blob 所支援，並使用 **Put 頁面** 來寫入資料。

### <a name="recovery"></a>復原

針對虛刪除的基底 blob 呼叫「取消 [刪除 Blob](/rest/api/storageservices/undelete-blob) 」作業，會將它和所有相關聯的虛刪除快照集還原為作用中。 針對作用中的基底 blob 呼叫取消 **刪除 Blob** 作業，會將所有相關聯的虛刪除快照集還原為作用中。 快照集還原為作用中後，看起來會像是使用者產生的快照集；它們並不會覆寫基底 Blob。

若要將 blob 還原至特定的虛刪除快照集，您可以在基底 blob 上呼叫「取消 **刪除」 blob** 。 然後，您可以將快照集複製到目前作用中的 Blob。 您也可以將快照集複製到新的 Blob。

![此圖顯示使用取消刪除 blob 時所發生的情況。](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

*虛刪除的資料是灰色的，而作用中的資料則是藍色。最近寫入的資料會出現在較舊資料的下方。在這裡，會在 blob B 上呼叫取消 **刪除 blob** ，藉此將基底 Blob、B1 和所有相關聯的快照集（在此只是 B0）還原為使用中。在第二個步驟中，會將 B0 複製到基底 blob 上。這項複製作業會產生 B1 的虛刪除快照集。*

若要檢視已虛刪除的 Blob 和 Blob 快照集，您可以選擇在**清單 Blob** 中包含已刪除的資料。 您可以選擇僅檢視已虛刪除的基底 Blob，或是也包含已虛刪除的 Blob 快照集。 對於所有已虛刪除的資料，您可以檢視資料的刪除時間，以及資料將永久失效之前的天數。

### <a name="example"></a>範例

以下是在開啟虛刪除時，上傳、覆寫、快照、刪除和還原名為 *HelloWorld* 之 blob 的 .net 腳本的主控台輸出：

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

請參閱[後續步驟](#next-steps)一節，以取得產生此輸出之應用程式的指標。

## <a name="pricing-and-billing"></a>價格和計費

所有虛刪除的資料都會比照作用中資料的相同費率計費。 對於在設定的保留期限之後永久刪除的資料，您將不需要付費。 若要深入瞭解快照集，以及它們如何產生費用，請參閱 [瞭解快照集如何產生費用](storage-blob-snapshots.md)。

與自動產生快照集有關的交易不會計費。 您將會以寫入作業的速率來收取 **刪除 Blob** 交易的費用。

如需 Azure Blob 儲存體常規價格方面的詳細資訊，請參閱 [Azure Blob 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

當您一開始開啟虛刪除時，Microsoft 建議使用簡短的保留期限，以進一步瞭解此功能將對您的帳單有何影響。

針對經常覆寫的資料啟用虛刪除可能會導致儲存體容量費用增加，並在列出 blob 時增加延遲。 您可以藉由將經常覆寫的資料儲存在停用虛刪除的個別儲存體帳戶，來減輕這項額外的成本和延遲。

## <a name="faq"></a>常見問題集

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>是否可以使用「設定 Blob 層 API」將具有虛刪除快照集的 Blob 分層？

是。 已虛刪除的快照集會留在原始階層，但基底 Blob 會移到新階層。

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>進階儲存體帳戶有每個 Blob 100 個快照集的限制。 虛刪除快照集是否會計入這項限制中？

不會，虛刪除快照集不會計入這項限制中。

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>如果我在開啟虛刪除的情況下刪除整個帳戶或容器，是否會儲存所有相關聯的 Blob？

不會，如果您刪除整個帳戶或容器，將會永久刪除所有相關聯的 Blob。 如需保護儲存體帳戶免于意外刪除的詳細資訊，請參閱 [鎖定資源以防止非預期的變更](../../azure-resource-manager/management/lock-resources.md)。

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>我可以檢視已刪除資料的容量計量嗎？

虛刪除的資料會計入為儲存體帳戶容量總計的一部分。 如需追蹤和監視儲存體容量的詳細資訊，請參閱 [儲存體分析](../common/storage-analytics.md)。

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>我是否可從 Blob 讀取和複製已虛刪除的快照集？  

是，但您必須先對 Blob 呼叫「取消刪除」。

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>虛刪除是否適用於虛擬機器磁碟？  

虛刪除適用于 premium 和 standard 非受控磁片，也就是分頁 blob。 虛刪除只會協助您復原 **刪除 blob**、 **放置 Blob**、 **放置區塊清單**和 **複製 blob** 作業所刪除的資料。 藉由呼叫**放置頁面**而覆寫的資料無法復原。

Azure 虛擬機器使用 **Put 頁面**的呼叫寫入非受控磁片，因此不支援使用虛刪除來復原從 Azure VM 到非受控磁片的寫入。

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>是否需要變更現有的應用程式才能使用虛刪除？

無論您使用何種 API 版本，都可以使用虛刪除。 不過，若要列出及復原已虛刪除的 blob 和 blob 快照集，您必須使用2017-07-29 版或更高版本的 [REST API Azure 儲存體](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 。 Microsoft 建議一律使用最新版的 Azure 儲存體 API。

## <a name="next-steps"></a>後續步驟

- [啟用 Blob 的虛刪除](soft-delete-enable.md)
- [Blob 版本設定](versioning-overview.md)
