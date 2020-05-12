---
title: 啟用和管理區塊 blob 的時間點還原（預覽）
titleSuffix: Azure Storage
description: 瞭解如何使用時間點還原（預覽）將區塊 blob 還原至較早時間點的狀態。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118204"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>啟用和管理區塊 blob 的時間點還原（預覽）

您可以使用時間點還原（預覽），將區塊 blob 還原至較早時間點的狀態。 本文說明如何使用 PowerShell 啟用儲存體帳戶的還原時間點。 它也會說明如何使用 PowerShell 執行還原作業。

如需詳細資訊及瞭解如何註冊預覽版，請參閱[區塊 blob 的還原時間點（預覽）](point-in-time-restore-overview.md)。

> [!CAUTION]
> 還原時間點僅支援在區塊 blob 上還原作業。 無法還原容器上的作業。 如果您在還原時間點期間呼叫「[刪除容器](/rest/api/storageservices/delete-container)」作業來刪除儲存體帳戶中的容器，就無法使用還原作業來還原該容器。 在預覽期間，如果您想要還原，請刪除個別的 blob，而不是刪除容器。

> [!IMPORTANT]
> 時間點還原預覽僅適用于非生產環境的使用。 生產環境的服務等級協定 (SLA) 目前無法使用。

## <a name="install-the-preview-module"></a>安裝預覽模組

若要使用 PowerShell 設定 Azure 時間點還原，請先安裝[1.14.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview)的 Az. Storage PowerShell 模組。 請遵循下列步驟來安裝預覽模組：

1. 使用 [**設定**] 底下的 [**應用程式 & 功能**] 設定，從 Windows 卸載任何先前安裝的 Azure PowerShell。

1. 確定您已安裝最新版的 PowerShellGet。 開啟 Windows PowerShell 視窗，然後執行下列命令來安裝最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. 在安裝完 PowerShellGet 之後，關閉並重新開啟 PowerShell 視窗。

1. 安裝最新版的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 安裝 Az Storage preview 模組：

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

如需有關安裝 Azure PowerShell 的詳細資訊，請參閱[使用 PowerShellGet 安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="enable-and-configure-point-in-time-restore"></a>啟用和設定時間點還原

啟用和設定時間點還原之前，請先啟用其必要條件：虛刪除、變更摘要和 blob 版本設定。 如需啟用這些功能的詳細資訊，請參閱下列文章：

- [啟用 blob 的虛刪除](soft-delete-enable.md)
- [啟用和停用變更摘要](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [啟用和管理 blob 版本設定](versioning-enable.md)

若要使用 PowerShell 設定 Azure 時間點還原，請呼叫 AzStorageBlobRestorePolicy 命令。 下列範例會啟用虛刪除，並設定虛刪除保留期限、啟用變更摘要，然後啟用時間點還原。 執行此範例之前，請使用 Azure 入口網站或 Azure Resource Manager 範本，同時啟用 blob 版本設定。

執行此範例時，請記得以您自己的值取代角括弧中的值：

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>執行還原作業

若要起始還原作業，請呼叫 Restore-AzStorageBlobRange 命令，並將還原點指定為 UTC**日期時間**值。 您可以指定一或多個字典的 blob 範圍進行還原，或省略一個範圍來還原儲存體帳戶中所有容器的所有 blob。 還原作業可能需要幾分鐘的時間才能完成。

指定要還原的 blob 範圍時，請記住下列規則：

- 為開始範圍和結束範圍指定的容器模式必須包含至少三個字元。 用來分隔容器名稱與 blob 名稱的正斜線（/）不會計入此最小值。
- 每個還原作業只能指定一個範圍。
- 不支援萬用字元。 系統會將其視為標準字元。
- 您可以藉 `$root` `$web` 由在傳遞至還原作業的範圍中明確指定 blob，來還原和容器中的 blob。 `$root`只有在 `$web` 明確指定和容器時，才會還原。 無法還原其他系統容器。

### <a name="restore-all-containers-in-the-account"></a>還原帳戶中的所有容器

若要還原儲存體帳戶中的所有容器和 blob，請呼叫 Restore-AzStorageBlobRange 命令，並省略 `-BlobRestoreRange` 參數。 下列範例會將儲存體帳戶中的容器還原到目前為止的12小時狀態：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>還原單一區塊 blob 範圍

若要還原某個範圍的 blob，請呼叫 AzStorageBlobRange 命令，並為參數指定字典的別名範圍和 blob 名稱 `-BlobRestoreRange` 。 範圍的開頭為 [內含]，而範圍的結尾為 [獨佔]。

例如，若要還原名為*範例-容器*的單一容器中的 blob，您可以指定以*範例-容器*開頭並以*container1*結尾的範圍。 開始和結束範圍中名為的容器不需要存在。 因為範圍的結尾是獨佔的，即使儲存體帳戶包含名為*sample-container1*的容器，只會還原名為*sample-container*的容器：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

若要在容器中指定要還原的 blob 子集，請使用正斜線（/）來分隔容器名稱與 blob 模式。 例如，下列範圍會在名稱開頭為字母*d*到*f*的單一容器中選取 blob：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

接下來，提供 AzStorageBlobRange 命令的範圍。 提供參數的 UTC**日期時間**值，以指定還原點 `-TimeToRestore` 。 下列範例會將指定範圍中的 blob 還原到目前為止的3天狀態：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>還原多個區塊 blob 範圍

若要還原多個區塊 blob 範圍，請為參數指定範圍陣列 `-BlobRestoreRange` 。 下列範例會還原*container1*和*container4*的完整內容：

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>後續步驟

- [區塊 blob 的還原時間點（預覽）](point-in-time-restore-overview.md)
- [虛刪除](soft-delete-overview.md)
- [變更摘要（預覽）](storage-blob-change-feed.md)
- [Blob 版本設定（預覽）](versioning-overview.md)
