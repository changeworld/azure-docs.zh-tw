---
title: 在區塊 blob 資料上執行時間點還原
titleSuffix: Azure Storage
description: 瞭解如何使用時間點還原，在指定的時間點將一組區塊 blob 還原至先前的狀態。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 828b5c34aaccf2a53aa197f921a8ef02d46821ae
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280465"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>在區塊 blob 資料上執行時間點還原

您可以使用時間點還原，將一或多組區塊 blob 還原至先前的狀態。 本文說明如何啟用儲存體帳戶的時間點還原，以及如何執行還原作業。

若要深入瞭解時間點還原，請參閱 [區塊 blob 的時間點還原](point-in-time-restore-overview.md)。

> [!CAUTION]
> 時間點還原只支援在區塊 blob 上還原作業。 無法還原容器上的作業。 如果您藉由呼叫「 [刪除容器](/rest/api/storageservices/delete-container) 」作業來刪除儲存體帳戶中的容器，則無法使用還原作業來還原該容器。 如果您想要還原，請刪除個別的 blob，而不是刪除容器。

## <a name="enable-and-configure-point-in-time-restore"></a>啟用和設定時間點還原

在您啟用和設定時間點還原之前，請先啟用其儲存體帳戶的必要條件：虛刪除、變更摘要和 blob 版本設定。 如需啟用上述各項功能的詳細資訊，請參閱下列文章：

- [啟用 Blob 的虛刪除](soft-delete-enable.md)
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
1. 儲存變更。

下圖顯示針對時間點還原設定的儲存體帳戶（還原點為七天前），以及 blob 虛刪除14天的保留期限。

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="顯示如何在 Azure 入口網站中設定時間點還原的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 設定時間點還原，請先安裝 [Az. Storage](https://www.powershellgallery.com/packages/Az.Storage) module version 2.6.0 或更新版本。 然後呼叫 AzStorageBlobRestorePolicy 命令，以啟用儲存體帳戶的時間點還原。

下列範例會啟用虛刪除，並設定虛刪除保留期間、啟用變更摘要和版本控制，然後啟用時間點還原。    執行此範例時，請記得以您自己的值取代角括弧中的值：

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
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

---

## <a name="perform-a-restore-operation"></a>執行還原作業

當您執行還原作業時，您必須指定還原點做為 UTC **日期時間** 值。 容器和 blob 將會還原至該日期和時間的狀態。 還原作業可能需要幾分鐘的時間才能完成。

您可以還原儲存體帳戶中的所有容器，也可以在一或多個容器中還原 blob 的範圍。 Blob 的範圍定義為詞典編纂，也就是以字典順序表示。 每個還原作業最多可支援十個字典的範圍。 範圍的開頭是內含的，而範圍的結尾是獨佔的。

為開始範圍和結束範圍指定的容器模式必須包含至少三個字元。 用來分隔容器名稱與 blob 名稱的正斜線 (/) ，不會計入此最小值。

字典範圍中不支援萬用字元。 任何萬用字元都會視為標準字元。

您可以藉 `$root` `$web` 由在傳遞至還原作業的範圍中明確指定 blob，來還原和容器中的 blob。 `$root`只有在 `$web` 明確指定和容器時，才會還原。 無法還原其他系統容器。

只會還原區塊 blob。 分頁 blob 和附加 blob 不包含在還原作業中。 如需附加 blob 相關限制的詳細資訊，請參閱 [區塊 blob 的時間點還原](point-in-time-restore-overview.md)。

> [!IMPORTANT]
> 當您執行還原作業時，Azure 儲存體會封鎖作業期間正在還原之範圍中 blob 的資料作業。 主要位置會封鎖讀取、寫入和刪除作業。 基於這個理由，在進行還原作業時，在 Azure 入口網站中列出容器的作業可能不會如預期般執行。
>
> 如果儲存體帳戶是異地複寫的，則在還原作業期間，次要位置的讀取作業可能會繼續進行。

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

若要使用 PowerShell 來還原儲存體帳戶中的所有容器和 blob，請呼叫 **restore-AzStorageBlobRange** 命令。 根據預設， **AzStorageBlobRange** 命令會以非同步方式執行，並傳回 **PSBlobRestoreStatus** 類型的物件，您可以使用此物件來檢查還原作業的狀態。

下列範例會以非同步方式將儲存體帳戶中的容器還原至目前為止的12小時狀態，並檢查還原作業的一些屬性：

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)

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

- 還原 *container1*的完整內容。
- 還原字典範圍中的 blob *>original-blob1*至*container2*中的*blob5* 。 此範圍會還原具有名稱的 blob，例如 *>original-blob1*、 *blob11*、 *blob100*、 *blob2*等等。 因為範圍的結尾是專屬的，所以它會還原名稱開頭為 *blob4*的 blob，但不會還原名稱開頭為 *blob5*的 blob。
- 還原 *container3* 和 *container4*中的所有 blob。 由於範圍結尾是獨佔的，此範圍不會還原 *container5*。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要還原單一 blob 範圍，請呼叫 **restore-AzStorageBlobRange** 命令，並為參數指定容器和 blob 名稱的字典範圍 `-BlobRestoreRange` 。 例如，若要在名為 *container1*的單一容器中還原 blob，您可以指定以 *container1* 開頭且結尾為 *container2*的範圍。 開始和結束範圍中指定的容器不需要存在。 由於範圍結尾是專屬的，即使儲存體帳戶包含名為 *container2*的容器，也只會還原名為 *container1* 的容器：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

若要指定要還原之容器中的 blob 子集，請使用正斜線 (/) 來分隔容器名稱與 blob 首碼模式。 例如，下列範圍會選取單一容器中的 blob，其名稱是以字母 *d* 到 *f*開頭：

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

---

## <a name="next-steps"></a>後續步驟

- [區塊 blob 的時間點還原](point-in-time-restore-overview.md)
- [虛刪除](soft-delete-overview.md)
- [變更摘要](storage-blob-change-feed.md)
- [Blob 版本設定](versioning-overview.md)
