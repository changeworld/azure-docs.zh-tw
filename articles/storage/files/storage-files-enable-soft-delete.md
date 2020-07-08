---
title: 啟用虛刪除 - Azure 檔案共用
description: 瞭解如何在 Azure 檔案共用上啟用虛刪除（預覽）以進行資料復原，以及防止意外刪除。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 84da8993bd1b0a61ef885d03f7c2bc80b92d47cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510820"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>在 Azure 檔案共用上啟用虛刪除

Azure 儲存體提供檔案共用的虛刪除（預覽），因此當應用程式或其他儲存體帳戶使用者錯誤地刪除資料時，您可以更輕鬆地復原資料。 若要深入了解虛刪除，請參閱[如何防止意外刪除 Azure 檔案共用](storage-files-prevent-file-share-deletion.md)。

下列各節說明如何在現有的儲存體帳戶上，啟用和使用 Azure 檔案共用的虛刪除：

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 瀏覽至您的儲存體帳戶，然後選取 [檔案服務] 底下的 [虛刪除]。
1. 針對 [檔案共用虛刪除]，選取 [啟用]。
1. 選取 [檔案共用保留期間 (天)]，然後輸入您所選擇的數字。
1. 選取 [儲存] 以確認您的資料保留設定。

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="[儲存體帳戶虛刪除設定] 窗格的螢幕擷取畫面。反白顯示 [檔案共用] 區段，啟用切換、設定保留期間及儲存。這會對您儲存體帳戶中的所有檔案共用啟用虛刪除。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="如果 [狀態] 資料行 ([名稱] 資料行旁邊的資料行) 設定為 [已刪除]，則表示您的檔案共用處於虛刪除狀態。並且在您指定的保留期間之後永久刪除。":::

1. 選取 [共用]，然後選取 [取消刪除]，這會還原共用。

    您可以確認共用已還原，因為其狀態會切換為 [作用中]。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="如果 [狀態] 資料行 ([名稱] 資料行旁邊的資料行) 設定為 [作用中]，則表示您的檔案共用已還原。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要還原虛刪除的檔案共用，請使用下列命令：

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

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="停用虛刪除可讓您立即並永久刪除儲存體帳戶中的所有檔案共用。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

您可以使用下列命令，在儲存體帳戶上停用虛刪除：

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>後續步驟

若要瞭解另一種資料保護和復原形式，請參閱[Azure 檔案儲存體的共用快照](storage-snapshots-files.md)集的總覽。
