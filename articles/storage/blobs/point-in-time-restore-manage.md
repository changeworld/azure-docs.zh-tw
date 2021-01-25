---
title: 在區塊 blob 資料上執行時間點還原
titleSuffix: Azure Storage
description: 瞭解如何使用時間點還原，在指定的時間點將一組區塊 blob 還原至先前的狀態。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/15/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f550f96a8bd2e402556089061604654b11d47844
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762891"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>在區塊 blob 資料上執行時間點還原

您可以使用時間點還原，將一或多組區塊 blob 還原至先前的狀態。 本文說明如何啟用儲存體帳戶的時間點還原，以及如何執行還原作業。

若要深入瞭解時間點還原，請參閱 [區塊 blob 的時間點還原](point-in-time-restore-overview.md)。

> [!CAUTION]
> 時間點還原只支援在區塊 blob 上還原作業。 無法還原容器上的作業。 如果您藉由呼叫「 [刪除容器](/rest/api/storageservices/delete-container) 」作業來刪除儲存體帳戶中的容器，則無法使用還原作業來還原該容器。 如果您稍後想要還原，請刪除個別的 blob，而不是刪除整個容器。 此外，Microsoft 建議啟用容器和 blob 的虛刪除，以防止意外刪除。 如需詳細資訊，請參閱 [容器的虛刪除 (預覽) ](soft-delete-container-overview.md) 和虛 [刪除 blob](soft-delete-blob-overview.md)。

## <a name="enable-and-configure-point-in-time-restore"></a>啟用和設定時間點還原

在您啟用和設定時間點還原之前，請先啟用其儲存體帳戶的必要條件：虛刪除、變更摘要和 blob 版本設定。 如需啟用上述各項功能的詳細資訊，請參閱下列文章：

- [啟用 Blob 的虛刪除](./soft-delete-blob-enable.md)
- [啟用和停用變更摘要](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [啟用和管理 Blob 版本設定](versioning-enable.md)

> [!IMPORTANT]
> 啟用虛刪除、變更摘要和 blob 版本設定可能會導致額外的費用。 如需詳細資訊，請參閱 [blob](soft-delete-blob-overview.md)的虛刪除、 [Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)，以及 [blob 版本](versioning-overview.md)設定。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站設定時間點還原，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在 [ **設定**] 底下，選擇 [ **資料保護**]。
1. 選取 [ **開啟時間點** 還原]。 當您選取此選項時，也會啟用 blob、版本控制和變更摘要的虛刪除。
1. 設定時間點還原的最大還原點（以天為單位）。 此數位必須至少為一天，小於針對「blob 虛刪除」指定的保留週期。
1. 儲存您的變更。

下圖顯示針對時間點還原設定的儲存體帳戶（還原點為七天前），以及 blob 虛刪除14天的保留期限。

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="顯示如何在 Azure 入口網站中設定時間點還原的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 設定時間點還原，請先安裝 [Az. Storage](https://www.powershellgallery.com/packages/Az.Storage) module version 2.6.0 或更新版本。 然後呼叫 [AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) 命令，以啟用儲存體帳戶的時間點還原。

下列範例會啟用虛刪除，並設定虛刪除保留期間、啟用變更摘要和版本控制，然後啟用時間點還原。 執行此範例時，請記得以您自己的值取代角括弧中的值：

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 設定時間點還原，請先安裝 Azure CLI 版本2.2.0 或更新版本。 然後呼叫 [az storage account blob-service-properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) 命令，以啟用時間點還原，以及儲存體帳戶所需的其他資料保護設定。

下列範例會啟用虛刪除，並將虛刪除保留期限設定為14天、啟用變更摘要和版本控制，並啟用還原期間為7天的時間點還原。 執行此範例時，請記得以您自己的值取代角括弧中的值：

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>選擇還原點

還原點是還原資料的日期和時間。 Azure 儲存體一律使用 UTC 日期/時間值做為還原點。 不過，Azure 入口網站可讓您以本地時間指定還原點，然後將該日期/時間值轉換為 UTC 日期/時間值，以執行還原作業。

當您使用 PowerShell 或 Azure CLI 執行還原作業時，您應該指定還原點做為 UTC 日期/時間值。 如果還原點是以本機時間值而非 UTC 時間值指定，則在某些情況下，還原作業可能仍會如預期般運作。 例如，如果您的當地時間是 UTC 減去五個小時，則指定本機時間值會導致還原點，此還原點是您所提供的值之前五個小時。 如果未對要在五小時期間內還原的範圍中的資料進行任何變更，則不論提供的時間值為何，還原作業都會產生相同的結果。 建議您指定還原點的 UTC 時間，以避免非預期的結果。

## <a name="perform-a-restore-operation"></a>執行還原作業

您可以還原儲存體帳戶中的所有容器，也可以在一或多個容器中還原 blob 的範圍。 Blob 的範圍定義為詞典編纂，也就是以字典順序表示。 每個還原作業最多可支援十個字典的範圍。 範圍的開頭是內含的，而範圍的結尾是獨佔的。

為開始範圍和結束範圍指定的容器模式必須包含至少三個字元。 用來分隔容器名稱與 blob 名稱的正斜線 (/) ，不會計入此最小值。

字典範圍中不支援萬用字元。 任何萬用字元都會視為標準字元。

您可以藉 `$root` `$web` 由在傳遞至還原作業的範圍中明確指定 blob，來還原和容器中的 blob。 `$root`只有在 `$web` 明確指定和容器時，才會還原。 無法還原其他系統容器。

只會還原區塊 blob。 分頁 blob 和附加 blob 不包含在還原作業中。 如需附加 blob 相關限制的詳細資訊，請參閱 [區塊 blob 的時間點還原](point-in-time-restore-overview.md)。

> [!IMPORTANT]
> 當您執行還原作業時，Azure 儲存體會封鎖作業期間正在還原之範圍中 blob 的資料作業。 主要位置會封鎖讀取、寫入和刪除作業。 基於這個理由，在進行還原作業時，在 Azure 入口網站中列出容器的作業可能不會如預期般執行。
>
> 如果儲存體帳戶是異地複寫的，則在還原作業期間，次要位置的讀取作業可能會繼續進行。
>
> 還原一組資料所需的時間，取決於還原期間所做的寫入和刪除作業數目。 例如，每日新增3000個物件且每天刪除1000個物件的1000000帳戶，大約需要兩個小時才能還原至過去30天的時間點。 具有此變更率的帳戶不建議在過去的保留期間和還原超過90天。

### <a name="restore-all-containers-in-the-account"></a>還原帳戶中的所有容器

您可以還原儲存體帳戶中的所有容器，使其在指定的時間點回到先前的狀態。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站還原儲存體帳戶中的所有容器和 blob，請遵循下列步驟：

1. 流覽至儲存體帳戶的容器清單。
1. 在工具列上，選擇 [ **還原容器**]，然後 **全部還原**。
1. 在 [ **還原所有容器** ] 窗格中，藉由提供日期和時間來指定還原點。
1. 勾選方塊，確認您想要繼續進行。
1. 選取 [ **還原** ] 開始還原作業。

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="顯示如何將所有容器還原至指定還原點的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 還原儲存體帳戶中的所有容器和 blob，請呼叫 **restore-AzStorageBlobRange** 命令，並將還原點提供為 UTC 日期/時間值。 根據預設， **AzStorageBlobRange** 命令會以非同步方式執行，並傳回 **PSBlobRestoreStatus** 類型的物件，您可以使用此物件來檢查還原作業的狀態。

下列範例會以非同步方式將儲存體帳戶中的容器還原至目前為止的12小時狀態，並檢查還原作業的一些屬性：

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

若要同步執行還原作業，請在命令上包含 **-WaitForComplete** 參數。 當 **-WaitForComplete** 參數存在時，PowerShell 會顯示一則訊息，其中包含作業的還原識別碼，然後在還原作業完成之前封鎖執行。 請記住，還原作業所需的時間長度取決於要還原的資料量，而大型還原作業最多可能需要一小時的時間才能完成。

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 還原儲存體帳戶中的所有容器和 blob，請呼叫 [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) 命令，並提供 UTC 日期/時間值的還原點。

下列範例會以非同步方式將儲存體帳戶中的所有容器還原為其在指定日期和時間之前12小時的狀態。 若要檢查還原作業的狀態，請呼叫 [az storage account show](/cli/azure/storage/account#az_storage_account_show)：

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

若要同步執行 **az storage blob restore** 命令並封鎖執行直到還原作業完成，請省略 `--no-wait` 參數。

---

### <a name="restore-ranges-of-block-blobs"></a>還原區塊 blob 的範圍

您可以在單一容器內或跨多個容器還原一或多個字典的 blob 範圍，以在指定的時間點將這些 blob 傳回至先前的狀態。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站還原一或多個容器中的 blob 範圍，請遵循下列步驟：

1. 流覽至儲存體帳戶的容器清單。
1. 選取要還原的容器。
1. 在工具列上，選擇 [ **還原容器**]，然後 **選取 [還原**]。
1. 在 [ **還原選取的容器** ] 窗格中，藉由提供日期和時間來指定還原點。
1. 指定要還原的範圍。 使用正斜線 (/) 來描繪 blob 首碼中的容器名稱。
1. [ **還原選取的容器** ] 窗格預設會指定包含容器中所有 blob 的範圍。 如果您不想要還原整個容器，請刪除此範圍。 預設範圍如下圖所示。

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="顯示在指定自訂範圍之前，要刪除之預設 blob 範圍的螢幕擷取畫面":::

1. 勾選方塊，確認您想要繼續進行。
1. 選取 [ **還原** ] 開始還原作業。

下圖顯示一組範圍的還原作業。

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="顯示如何在一或多個容器中還原 blob 範圍的螢幕擷取畫面":::

映射中顯示的還原作業會執行下列動作：

- 還原 *container1* 的完整內容。
- 還原字典範圍中的 blob *>original-blob1* 至 *container2* 中的 *blob5* 。 此範圍會還原具有名稱的 blob，例如 *>original-blob1*、 *blob11*、 *blob100*、 *blob2* 等等。 因為範圍的結尾是專屬的，所以它會還原名稱開頭為 *blob4* 的 blob，但不會還原名稱開頭為 *blob5* 的 blob。
- 還原 *container3* 和 *container4* 中的所有 blob。 由於範圍結尾是獨佔的，此範圍不會還原 *container5*。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要還原單一 blob 範圍，請呼叫 **restore-AzStorageBlobRange** 命令，並為參數指定容器和 blob 名稱的字典範圍 `-BlobRestoreRange` 。 例如，若要在名為 *container1* 的單一容器中還原 blob，您可以指定以 *container1* 開頭且結尾為 *container2* 的範圍。 開始和結束範圍中指定的容器不需要存在。 由於範圍結尾是專屬的，即使儲存體帳戶包含名為 *container2* 的容器，也只會還原名為 *container1* 的容器：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

若要指定要還原之容器中的 blob 子集，請使用正斜線 (/) 來分隔容器名稱與 blob 首碼模式。 例如，下列範圍會選取單一容器中的 blob，其名稱是以字母 *d* 到 *f* 開頭：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

接下來，提供 **AzStorageBlobRange** 命令的範圍。 藉由提供參數的 UTC **日期時間** 值來指定還原點 `-TimeToRestore` 。 下列範例會將指定範圍內的 blob 還原至目前時間的3天之前的狀態：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

依預設， **Restore-AzStorageBlobRange** 命令會以非同步方式執行。 當您以非同步方式起始還原作業時，PowerShell 會立即顯示作業的屬性資料表：  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

若要還原多個區塊 blob 範圍，請為參數指定範圍陣列 `-BlobRestoreRange` 。 下列範例會指定兩個範圍，以將 *container1* 和 *container4* 的完整內容還原至24小時前的狀態，並將結果儲存至變數：

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

若要同步執行還原作業並封鎖執行直到完成，請在命令上包含 **-WaitForComplete** 參數。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要還原某個範圍的 blob，請呼叫 [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) 命令，並為參數指定容器和 blob 名稱的字典範圍 `--blob-range` 。 若要指定多個範圍，請提供 `--blob-range` 每個不同範圍的參數。

例如，若要在名為 *container1* 的單一容器中還原 blob，您可以指定以 *container1* 開頭且結尾為 *container2* 的範圍。 開始和結束範圍中指定的容器不需要存在。 由於範圍結尾是專屬的，即使儲存體帳戶包含名為 *container2* 的容器，也只會還原名為 *container1* 的容器。

若要指定要還原之容器中的 blob 子集，請使用正斜線 (/) 來分隔容器名稱與 blob 首碼模式。 下面顯示的範例會以非同步方式還原容器中名稱以字母為開頭的 blob 範圍 `d` `f` 。

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

若要同步執行 **az storage blob restore** 命令並封鎖執行直到還原作業完成，請省略 `--no-wait` 參數。

---

## <a name="next-steps"></a>後續步驟

- [區塊 blob 的時間點還原](point-in-time-restore-overview.md)
- [虛刪除](./soft-delete-blob-overview.md)
- [變更摘要](storage-blob-change-feed.md)
- [Blob 版本設定](versioning-overview.md)