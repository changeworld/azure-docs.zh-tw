---
title: 物件複寫概觀 (預覽)
titleSuffix: Azure Storage
description: 物件複寫 (預覽) 會以非同步方式複製來源儲存體帳戶與目的地帳戶之間的區塊 Blob。 使用物件複寫可將讀取要求的延遲降到最低，以提高計算工作負載的效率、最佳化資料散發，並將成本降至最低。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: abec9811cd407c1fab91cfb60412aabdd969690d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036976"
---
# <a name="object-replication-for-block-blobs-preview"></a>區塊 Blob 的物件複寫 (預覽)

物件複寫 (預覽) 會以非同步方式複製來源儲存體帳戶與目的地帳戶之間的區塊 Blob。 物件複寫支援的部分案例包括：

- **將延遲最小化。** 物件複寫可以讓用戶端從較接近的實際鄰近區域取用資料，藉此減少讀取要求的延遲。
- **提高計算工作負載的效率。** 使用物件複寫，計算工作負載可以處理不同區域中相同的區塊 Blob 集合。
- **最佳化資料散發。** 您可以在單一位置處理或分析資料，然後只將結果複寫到其他區域。
- **最佳化成本。** 複寫您的資料之後，您可以使用生命週期管理原則將其移至封存層，以降低成本。

下圖顯示物件複寫如何將區塊 Blob 從一個區域中的來源儲存體帳戶複寫到兩個不同區域中的目的地帳戶。

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="此圖顯示物件複寫的運作方式":::

若要了解如何設定物件複寫，請參閱[設定物件複寫 (預覽)](object-replication-configure.md)。

## <a name="object-replication-policies-and-rules"></a>物件複寫原則和規則

當您設定物件複寫時，您會建立複寫原則，指定來源儲存體帳戶和目的地帳戶。 複寫原則包含一或多個規則，可指定來源容器和目的地容器，並指出將複寫來源容器中的哪些區塊 Blob。

在您設定物件複寫之後，Azure 儲存體會定期檢查來源帳戶的變更摘要，並以非同步方式將任何寫入或刪除作業複寫至目的地帳戶。 複寫延遲取決於所複寫區塊 Blob 的大小。

> [!IMPORTANT]
> 因為區塊 Blob 資料會以非同步方式複寫，所以來源帳戶和目的地帳戶不會立即同步。目前不提供將資料複寫至目的地帳戶所需時間的 SLA。

### <a name="replications-policies"></a>複寫原則

當您設定物件複寫時，會透過 Azure 儲存體資源提供者，在來源帳戶和目的地帳戶上建立複寫原則。 複寫原則是以原則識別碼進行識別。 來源和目的地帳戶上的原則必須擁有相同的原則識別碼，才能進行複寫。

儲存體帳戶最多可作為兩個目的地帳戶的來源帳戶。 而目的地帳戶可能不會有兩個以上的來源帳戶。 來源和目的地帳戶可能都在不同的區域中。 您可以設定個別的複寫原則，將資料複寫到每個目的地帳戶。

### <a name="replication-rules"></a>複寫規則

複寫規則會指定 Azure 儲存體如何將 Blob 從來源容器複製到目的地容器。 您最多能為每個複寫原則指定 10 個複寫規則。 每個規則都會定義單一來源和目的地容器，而且每個來源和目的地容器只能用在一個規則中。

當您建立複寫規則時，根據預設，只會複製後續新增至來源容器的新區塊 Blob。 您也可以指定同時複製新的和現有的區塊 Blob，或定義自訂複製範圍，從指定的時間開始複製建立的區塊 Blob。

您也可以指定一或多個篩選作為複製規則的一部分，以依前置詞篩選區塊 Blob。 當您指定前置詞時，只有符合來源容器中該前置詞的 Blob 才會複製到目的地容器。

來源和目的地容器必須同時存在，您才能在規則中加以指定。 建立複寫原則之後，目的地容器會變成唯讀。 任何寫入目的地容器的嘗試都會失敗，錯誤碼為 409 (衝突)。 不過，您可以在目的地容器中的 blob 上呼叫「[設定 Blob 層](/rest/api/storageservices/set-blob-tier)」作業，以將其移至封存層。 如需封存層的詳細資訊，請參閱[Azure Blob 儲存體：經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="about-the-preview"></a>關於預覽

僅針對一般用途 v2 儲存體帳戶支援物件複寫。 物件複寫適用於下列區域 (預覽)：

- 法國中部
- 加拿大東部
- 加拿大中部
- 美國東部 2
- 美國中部

來源和目的地帳戶都必須位於其中一個區域，才能使用物件複寫。 這些帳戶可以在兩個不同的區域中。

預覽期間，在儲存體帳戶之間複寫資料並不會產生任何額外的成本。

> [!IMPORTANT]
> 物件複寫預覽僅適用於非生產環境。 生產環境的服務等級協定 (SLA) 目前無法使用。

### <a name="prerequisites-for-object-replication"></a>物件複寫的必要條件

物件複寫需要啟用下列 Azure 儲存體功能： 
- [變更摘要](storage-blob-change-feed.md)
- [版本控制](versioning-overview.md)

在您設定物件複寫之前，請先啟用其必要條件。 必須在來源帳戶上啟用變更摘要，且必須在來源和目的地帳戶上皆啟用 Blob 版本設定。 如需啟用這些功能的詳細資訊，請參閱下列文章：

- [啟用和停用變更摘要](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [啟用和管理 Blob 版本設定](versioning-enable.md)

請務必先註冊變更摘要和 Blob 版本設定預覽，然後才能加以啟用。

啟用變更摘要和 Blob 版本設定可能會產生額外的成本。 如需詳細資訊，請參閱 [Azure 儲存體價格頁面](https://azure.microsoft.com/pricing/details/storage/)。

### <a name="register-for-the-preview"></a>註冊預覽

您可以使用 PowerShell 或 Azure CLI 註冊物件複寫預覽。 如果您還沒有註冊變更摘要和 Blob 版本設定預覽，請確定您也進行註冊。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 註冊預覽，請執行下列命令：

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 註冊預覽，請執行下列命令：

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>檢查註冊狀態

您可以使用 PowerShell 或 Azure CLI 來檢查註冊要求的狀態。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 檢查註冊要求的狀態，請執行下列命令：

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 檢查註冊要求的狀態，請執行下列命令：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>提出問題或提供意見反應

若要提出有關物件複寫預覽的問題，或是要提供意見反應，請在 AzureStorageFeedback@microsoft.com 聯繫 Microsoft。 永遠歡迎在 [Azure 儲存體意見反應論壇](https://feedback.azure.com/forums/217298-storage)發表有關 Azure 儲存體的想法和建議。

## <a name="next-steps"></a>後續步驟

- [設定物件複寫 (預覽)](object-replication-configure.md)
- [Azure Blob 儲存體中的變更摘要支援 (預覽)](storage-blob-change-feed.md)
- [啟用和管理 Blob 版本設定](versioning-enable.md)
