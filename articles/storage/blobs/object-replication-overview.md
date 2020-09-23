---
title: 物件複製總覽
titleSuffix: Azure Storage
description: 物件複寫會以非同步方式複製來源儲存體帳戶和目的地帳戶之間的區塊 blob。 使用物件複寫可將讀取要求的延遲降到最低，以提高計算工作負載的效率、最佳化資料散發，並將成本降至最低。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ee76c1692049d5b25e85b6780fbcf78f7ebfdd2f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987052"
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

## <a name="object-replication-policies-and-rules"></a>物件複寫原則和規則

當您設定物件複寫時，您會建立複寫原則，指定來源儲存體帳戶和目的地帳戶。 複寫原則包含一或多個規則，可指定來源容器和目的地容器，並指出將複寫來源容器中的哪些區塊 Blob。

在您設定物件複寫之後，Azure 儲存體會定期檢查來源帳戶的變更摘要，並以非同步方式將任何寫入或刪除作業複寫至目的地帳戶。 複寫延遲取決於所複寫區塊 Blob 的大小。

> [!IMPORTANT]
> 因為區塊 Blob 資料會以非同步方式複寫，所以來源帳戶和目的地帳戶不會立即同步。目前不提供將資料複寫至目的地帳戶所需時間的 SLA。

### <a name="replication-policies"></a>複寫原則

當您設定物件複寫時，會透過 Azure 儲存體資源提供者，在來源帳戶和目的地帳戶上建立複寫原則。 複寫原則是以原則識別碼進行識別。 來源和目的地帳戶上的原則必須擁有相同的原則識別碼，才能進行複寫。

儲存體帳戶最多可作為兩個目的地帳戶的來源帳戶。 來源和目的地帳戶可能位於相同區域或不同區域。 它們也可以位於不同的訂用帳戶中，以及不同的 Azure Active Directory (Azure AD) 租使用者中。 每個來源帳戶/目的地帳戶組只能建立一個複寫原則。

### <a name="replication-rules"></a>複寫規則

複寫規則會指定 Azure 儲存體如何將 Blob 從來源容器複製到目的地容器。 您最多能為每個複寫原則指定 10 個複寫規則。 每個複寫規則都會定義單一來源和目的地容器，而每個來源和目的地容器只能在一個規則中使用。

當您建立複寫規則時，根據預設，只會複製後續新增至來源容器的新區塊 Blob。 您可以指定要複製新的和現有的區塊 blob，也可以定義自訂複製範圍，以複製從指定的時間開始建立的區塊 blob。

您也可以指定一或多個篩選作為複製規則的一部分，以依前置詞篩選區塊 Blob。 當您指定前置詞時，只有符合來源容器中該前置詞的 Blob 才會複製到目的地容器。

來源和目的地容器必須同時存在，您才能在規則中加以指定。 建立複寫原則之後，目的地容器會變成唯讀。 任何寫入目的地容器的嘗試都會失敗，錯誤碼為 409 (衝突)。 不過，您可以在目的地容器中的 blob 上呼叫「 [設定 Blob 層](/rest/api/storageservices/set-blob-tier) 」作業，以將它移至封存層。 如需封存層的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取層和封存存取層](storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="billing"></a>計費

物件複寫會針對來源和目的地帳戶的讀取和寫入交易產生額外成本，以及將資料從來源帳戶複製到目的地帳戶的輸出費用，以及讀取處理變更摘要的費用。

## <a name="next-steps"></a>下一步

- [設定物件複寫](object-replication-configure.md)
- [Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)
- [啟用和管理 Blob 版本設定](versioning-enable.md)
