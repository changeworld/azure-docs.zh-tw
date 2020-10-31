---
title: 啟用虛刪除 - Azure 檔案共用
description: 了解如何在 Azure 檔案共用上啟用虛刪除以進行資料復原，以及防止意外刪除。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 7defa8611080027a67a0d1db1daa4c4a9d44edfe
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126136"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>在 Azure 檔案共用上啟用虛刪除

Azure 儲存體提供檔案共用的虛刪除功能，因此，當應用程式或其他儲存體帳戶使用者錯誤地刪除您的資料時，您將可更輕鬆地復原資料。 若要深入了解虛刪除，請參閱[如何防止意外刪除 Azure 檔案共用](storage-files-prevent-file-share-deletion.md)。

下列各節說明如何在現有的儲存體帳戶上，啟用和使用 Azure 檔案共用的虛刪除：

# <a name="portal"></a>[入口網站](#tab/azure-portal)

## <a name="getting-started"></a>開始使用

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 瀏覽至您的儲存體帳戶，然後選取 [檔案服務] 底下的 [虛刪除]。
1. 針對 [檔案共用虛刪除]，選取 [啟用]。
1. 選取 [檔案共用保留期間 (天)]，然後輸入您所選擇的數字。
1. 選取 [儲存] 以確認您的資料保留設定。

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="[儲存體帳戶虛刪除設定] 窗格的螢幕擷取畫面。反白顯示 [檔案共用] 區段，啟用切換、設定保留期間及儲存。這會對您儲存體帳戶中的所有檔案共用啟用虛刪除。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>必要條件

[3.0.0](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)版的 Az. Storage 模組提供虛刪除 Cmdlet。 

## <a name="getting-started-with-powershell"></a>開始使用 PowerShell

若要啟用虛刪除，您必須更新檔案用戶端的服務屬性。 下列範例會為儲存體帳戶中的所有檔案共用啟用虛刪除：

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

您可以使用下列命令來確認是否已啟用虛刪除，並檢視其保留原則：

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>還原虛刪除的檔案共用

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要還原虛刪除的檔案共用：

1. 瀏覽至您的儲存體帳戶，並選取 [檔案共用]。
1. 在 [檔案共用] 刀鋒視窗上，啟用 [顯示已刪除的共用]，以顯示已虛刪除的任何共用。

    這會顯示目前處於 [已刪除] 狀態的任何共用。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="[儲存體帳戶虛刪除設定] 窗格的螢幕擷取畫面。反白顯示 [檔案共用] 區段，啟用切換、設定保留期間及儲存。這會對您儲存體帳戶中的所有檔案共用啟用虛刪除。":::

1. 選取 [共用]，然後選取 [取消刪除]，這會還原共用。

    您可以確認共用已還原，因為其狀態會切換為 [作用中]。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="[儲存體帳戶虛刪除設定] 窗格的螢幕擷取畫面。反白顯示 [檔案共用] 區段，啟用切換、設定保留期間及儲存。這會對您儲存體帳戶中的所有檔案共用啟用虛刪除。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

3.0.0 版的 Az. Storage 模組提供虛刪除 Cmdlet。 若要還原虛刪除的檔案共用，請使用下列命令：

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>停用虛刪除

如果您想要停止使用虛刪除，或永久刪除檔案共用，請遵循下列指示：

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 瀏覽至您的儲存體帳戶，並選取 [設定] 下的 [虛刪除]。
1. 在 [檔案共用] 下，針對 [檔案共用的虛刪除] 選取 [停用]。
1. 選取 [儲存] 以確認您的資料保留設定。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="[儲存體帳戶虛刪除設定] 窗格的螢幕擷取畫面。反白顯示 [檔案共用] 區段，啟用切換、設定保留期間及儲存。這會對您儲存體帳戶中的所有檔案共用啟用虛刪除。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

3.0.0 版的 Az. Storage 模組提供虛刪除 Cmdlet。 您可以使用下列命令，在儲存體帳戶上停用虛刪除：

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>後續步驟

若要瞭解另一種形式的資料保護和復原，請參閱 [Azure 檔案儲存體的共用快照集簡介](storage-snapshots-files.md)。
