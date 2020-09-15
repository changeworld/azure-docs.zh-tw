---
title: '啟用和管理區塊 blob 的時間點還原 (預覽) '
titleSuffix: Azure Storage
description: 瞭解如何使用時間點還原 (預覽版) 將一組區塊 blob 還原至先前的狀態。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 140e1203a29dcebec9d6483e73e906591b2213fb
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068472"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>啟用和管理區塊 blob 的時間點還原 (預覽) 

您可以使用時間點還原 (預覽) 將一組區塊 blob 還原至先前的狀態。 本文說明如何使用 PowerShell 啟用儲存體帳戶的時間點還原。 它也會說明如何使用 PowerShell 執行還原作業。

如需詳細資訊，並瞭解如何註冊預覽版，請參閱 [區塊 blob 的時間點還原 (預覽) ](point-in-time-restore-overview.md)。

> [!CAUTION]
> 時間點還原只支援在區塊 blob 上還原作業。 無法還原容器上的作業。 如果您在時間點還原預覽版期間呼叫「 [刪除容器](/rest/api/storageservices/delete-container) 」作業來刪除儲存體帳戶中的容器，則無法使用還原作業來還原該容器。 在預覽期間，如果您想要還原，請刪除個別的 blob，而不是刪除容器。

> [!IMPORTANT]
> 時間點還原預覽僅供非生產環境使用。

## <a name="enable-and-configure-point-in-time-restore"></a>啟用和設定時間點還原

在您啟用和設定時間點還原之前，請先啟用其儲存體帳戶的必要條件：虛刪除、變更摘要和 blob 版本設定。 如需啟用上述各項功能的詳細資訊，請參閱下列文章：

- [啟用 Blob 的虛刪除](soft-delete-enable.md)
- [啟用和停用變更摘要](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [啟用和管理 Blob 版本設定](versioning-enable.md)

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

若要使用 PowerShell 來設定時間點還原，請先安裝1.14.1 預覽版或更新版本的預覽模組。 移除任何其他版本的 Az. Storage 模組。

確認您已安裝2.2.4.1 或更新版本的 PowerShellGet。 若要判斷您目前已安裝的版本，請執行下列命令：

```powershell
Get-InstalledModule PowerShellGet
```

接下來，安裝 Az. Storage preview 模組。 下列命令會安裝 [2.5.2 預覽](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) 版本的 Az. Storage 模組：

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
```

如需安裝 Azure PowerShell 的詳細資訊，請參閱 [安裝 powershellget](/powershell/scripting/gallery/installing-psget) 並 [使用 PowerShellGet 安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

若要使用 PowerShell 來設定 Azure 時間點還原，請呼叫 AzStorageBlobRestorePolicy 命令。 下列範例會啟用虛刪除，並設定虛刪除保留期間、啟用變更摘要，然後啟用時間點還原。 執行範例之前，請使用 Azure 入口網站或 Azure Resource Manager 範本，也可以啟用 blob 版本設定。

執行此範例時，請記得以您自己的值取代角括弧中的值：

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

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

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

只會還原區塊 blob。 分頁 blob 和附加 blob 不包含在還原作業中。 如需附加 blob 相關限制的詳細資訊，請參閱 [已知問題](#known-issues)。

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

若要使用 PowerShell 來還原儲存體帳戶中的所有容器和 blob，請呼叫 **restore-AzStorageBlobRange** 命令，並省略 `-BlobRestoreRange` 參數。 下列範例會將儲存體帳戶中的容器還原至目前為止的12小時之前的狀態：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
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

若要還原單一 blob 範圍，請呼叫 **restore-AzStorageBlobRange** 命令，並為參數指定容器和 blob 名稱的字典範圍 `-BlobRestoreRange` 。 例如，若要在名為 *範例容器*的單一容器中還原 blob，您可以指定以 *範例容器* 開頭的範圍，並以 *範例-container1*結尾。 開始和結束範圍中指定的容器不需要存在。 因為範圍的結尾是專屬的，即使儲存體帳戶包含名為 *sample container1*的容器，也只會還原名為 *sample* 容器的容器：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container `
    -EndRange sample-container1
```

若要指定要還原之容器中的 blob 子集，請使用正斜線 (/) 來分隔容器名稱與 blob 首碼模式。 例如，下列範圍會選取單一容器中的 blob，其名稱是以字母 *d* 到 *f*開頭：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d `
    -EndRange sample-container/g
```

接下來，提供 **AzStorageBlobRange** 命令的範圍。 藉由提供參數的 UTC **日期時間** 值來指定還原點 `-TimeToRestore` 。 下列範例會將指定範圍內的 blob 還原至目前時間的3天之前的狀態：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

若要還原多個區塊 blob 範圍，請為參數指定範圍陣列 `-BlobRestoreRange` 。 下列範例會指定兩個範圍來還原 *container1* 和 *container4*的完整內容：

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

---

### <a name="restore-block-blobs-asynchronously-with-powershell"></a>使用 PowerShell 以非同步方式還原區塊 blob

若要以非同步方式執行還原作業，請將 `-AsJob` 參數新增至 **AzStorageBlobRange** 的呼叫，並將呼叫的結果儲存在變數中。 **AzStorageBlobRange**命令會傳回**AzureLongRunningJob**類型的物件。 您可以檢查這個物件的 **State** 屬性，判斷還原作業是否已完成。 **State**屬性的值可能**正在**執行或**已完成**。

下列範例顯示如何以非同步方式呼叫還原作業：

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

若要在執行還原作業之後等候還原作業完成，請呼叫 [Wait 工作](/powershell/module/microsoft.powershell.core/wait-job) 命令，如下列範例所示：

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>已知問題

若為存在附加 blob 的還原作業子集，則還原作業會失敗。 如果帳戶中有附加 blob，Microsoft 建議您不要在預覽期間執行時間點還原。

## <a name="next-steps"></a>後續步驟

- [區塊 blob 的時間點還原 (預覽) ](point-in-time-restore-overview.md)
- [虛刪除](soft-delete-overview.md)
- [變更摘要 (預覽) ](storage-blob-change-feed.md)
- [Blob 版本設定](versioning-overview.md)
