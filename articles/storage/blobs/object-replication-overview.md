---
title: 物件複製總覽
titleSuffix: Azure Storage
description: 物件複寫會以非同步方式複製來源儲存體帳戶和目的地帳戶之間的區塊 blob。 使用物件複寫可將讀取要求的延遲降到最低，以提高計算工作負載的效率、最佳化資料散發，並將成本降至最低。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ff2408e35d76a6ea0d5221e04c7a41ed6cde7ac9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178971"
---
# <a name="object-replication-for-block-blobs"></a>區塊 blob 的物件複寫

物件複寫會以非同步方式複製來源儲存體帳戶和目的地帳戶之間的區塊 blob。 物件複寫支援的部分案例包括：

- **將延遲最小化。** 物件複寫可以讓用戶端從較接近的實際鄰近區域取用資料，藉此減少讀取要求的延遲。
- **提高計算工作負載的效率。** 使用物件複寫，計算工作負載可以處理不同區域中相同的區塊 Blob 集合。
- **最佳化資料散發。** 您可以在單一位置處理或分析資料，然後只將結果複寫到其他區域。
- **最佳化成本。** 複寫您的資料之後，您可以使用生命週期管理原則將其移至封存層，以降低成本。

下圖顯示物件複寫如何將區塊 Blob 從一個區域中的來源儲存體帳戶複寫到兩個不同區域中的目的地帳戶。

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="此圖顯示物件複寫的運作方式":::

若要瞭解如何設定物件複寫，請參閱 [設定物件](object-replication-configure.md)複寫。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>物件複寫的必要條件

物件複寫需要同時啟用下列 Azure 儲存體功能：

- [變更](storage-blob-change-feed.md)摘要：必須在來源帳戶上啟用。 若要瞭解如何啟用變更摘要，請參閱 [啟用和停用變更](storage-blob-change-feed.md#enable-and-disable-the-change-feed)摘要。
- [Blob 版本](versioning-overview.md)設定：必須同時在來源和目的地帳戶上啟用。 若要瞭解如何啟用版本設定，請參閱 [啟用和管理 blob 版本](versioning-enable.md)設定。

啟用變更摘要和 Blob 版本設定可能會產生額外的成本。 如需詳細資訊，請參閱 [Azure 儲存體價格頁面](https://azure.microsoft.com/pricing/details/storage/)。

## <a name="how-object-replication-works"></a>物件複寫的運作方式

物件複寫會根據您設定的規則，以非同步方式複製容器中的區塊 blob。 Blob 的內容、任何與 blob 相關聯的版本，以及 blob 的中繼資料和屬性，全都從源容器複製到目的地容器。

> [!IMPORTANT]
> 因為區塊 Blob 資料會以非同步方式複寫，所以來源帳戶和目的地帳戶不會立即同步。目前不提供將資料複寫至目的地帳戶所需時間的 SLA。 您可以檢查來源 blob 上的複寫狀態，以判斷複寫是否已完成。 如需詳細資訊，請參閱 [檢查 blob 的複寫狀態](object-replication-configure.md#check-the-replication-status-of-a-blob)。

### <a name="blob-versioning"></a>Blob 版本設定

物件複寫需要在來源和目的地帳戶上啟用 blob 版本設定。 修改來源帳戶中的複寫 blob 時，會在來源帳戶中建立新版本的 blob，以反映 blob 先前的狀態，然後再進行修改。 來源帳戶中 (或基底 blob) 的目前版本會反映最新的更新。 更新的目前版本和新的舊版本都會複寫到目的地帳戶。 如需寫入作業如何影響 blob 版本的詳細資訊，請參閱 [寫入作業的版本控制](versioning-overview.md#versioning-on-write-operations)。

刪除來源帳戶中的 blob 時，會在先前的版本中捕捉 blob 的目前版本，然後刪除。 即使在刪除目前的版本之後，仍會保留所有舊版的 blob。 此狀態會複寫至目的地帳戶。 如需刪除作業如何影響 blob 版本的詳細資訊，請參閱 [刪除作業的版本控制](versioning-overview.md#versioning-on-delete-operations)。

### <a name="snapshots"></a>快照集

物件複寫不支援 blob 快照集。 來源帳戶中 blob 的任何快照集都不會複寫到目的地帳戶。

### <a name="blob-tiering"></a>Blob 階層處理

當來源和目的地帳戶在經常性存取層或非經常性存取層時，就會支持對象複寫。 來源和目的地帳戶可能位於不同的層級。 但是，如果來源或目的地帳戶中的 blob 已移至封存層，則物件複寫將會失敗。 如需有關 blob 層的詳細資訊，請參閱 [Azure Blob 儲存體經常性存取、非經常性存取層和封存的存取層](storage-blob-storage-tiers.md)。

### <a name="immutable-blobs"></a>固定 Blob

物件複寫不支援不可變的 blob。 如果來源或目的地容器具有以時間為基礎的保留原則或合法保存，則物件複寫會失敗。 如需不可變 blob 的詳細資訊，請參閱 [使用不可變的儲存體儲存商務關鍵 blob 資料](storage-blob-immutable-storage.md)。

## <a name="object-replication-policies-and-rules"></a>物件複寫原則和規則

當您設定物件複寫時，您會建立複寫原則，指定來源儲存體帳戶和目的地帳戶。 複寫原則包含一或多個規則，可指定來源容器和目的地容器，並指出將複寫來源容器中的哪些區塊 Blob。

在您設定物件複寫之後，Azure 儲存體會定期檢查來源帳戶的變更摘要，並以非同步方式將任何寫入或刪除作業複寫至目的地帳戶。 複寫延遲取決於所複寫區塊 Blob 的大小。

### <a name="replication-policies"></a>複寫原則

當您設定物件複寫時，會透過 Azure 儲存體資源提供者，在來源帳戶和目的地帳戶上建立複寫原則。 複寫原則是以原則識別碼進行識別。 來源和目的地帳戶上的原則必須擁有相同的原則識別碼，才能進行複寫。

儲存體帳戶最多可作為兩個目的地帳戶的來源帳戶。 來源和目的地帳戶可能位於相同區域或不同區域。 它們也可以位於不同的訂用帳戶中，以及不同的 Azure Active Directory (Azure AD) 租使用者中。 每個來源帳戶/目的地帳戶組只能建立一個複寫原則。

### <a name="replication-rules"></a>複寫規則

複寫規則會指定 Azure 儲存體如何將 Blob 從來源容器複製到目的地容器。 您最多能為每個複寫原則指定 10 個複寫規則。 每個複寫規則都會定義單一來源和目的地容器，而每個來源和目的地容器只能在一個規則中使用。

當您建立複寫規則時，根據預設，只會複製後續新增至來源容器的新區塊 Blob。 您可以指定要複製新的和現有的區塊 blob，也可以定義自訂複製範圍，以複製從指定的時間開始建立的區塊 blob。

您也可以指定一或多個篩選作為複製規則的一部分，以依前置詞篩選區塊 Blob。 當您指定前置詞時，只有符合來源容器中該前置詞的 Blob 才會複製到目的地容器。

來源和目的地容器必須同時存在，您才能在規則中加以指定。 建立複寫原則之後，目的地容器會變成唯讀。 任何寫入目的地容器的嘗試都會失敗，錯誤碼為 409 (衝突)。 不過，您可以在目的地容器中的 blob 上呼叫「 [設定 Blob 層](/rest/api/storageservices/set-blob-tier) 」作業，以將它移至封存層。 如需封存層的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取層和封存存取層](storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="replication-status"></a>複寫狀態

您可以在來源帳戶中檢查 blob 的複寫狀態。 如需詳細資訊，請參閱 [檢查 blob 的複寫狀態](object-replication-configure.md#check-the-replication-status-of-a-blob)。

如果來源帳戶中 blob 的複寫狀態指出失敗，則請調查下列可能的原因：

- 請確定已在目的地帳戶上設定物件複寫原則。
- 確認目的地容器仍然存在。
- 如果已使用客戶提供的金鑰來加密來源 blob 做為寫入作業的一部分，則物件複寫將會失敗。 如需客戶提供之金鑰的詳細資訊，請參閱針對 [Blob 儲存體的要求提供加密金鑰](encryption-customer-provided-keys.md)。

## <a name="billing"></a>計費

物件複寫會針對來源和目的地帳戶的讀取和寫入交易產生額外成本，以及將資料從來源帳戶複製到目的地帳戶的輸出費用，以及讀取處理變更摘要的費用。

## <a name="next-steps"></a>下一步

- [設定物件複寫](object-replication-configure.md)
- [Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)
- [啟用和管理 Blob 版本設定](versioning-enable.md)
