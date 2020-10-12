---
title: 啟用虛刪除 - Azure 檔案共用
description: 瞭解如何在 Azure 檔案共用上啟用「虛刪除」 (預覽版) 以進行資料復原，並防止意外刪除。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 2d2a000879a95f86a6cdda3324add5b692476eee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88590110"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>在 Azure 檔案共用上啟用虛刪除

Azure 儲存體為檔案共用提供 (preview) 的虛刪除，讓您可以在應用程式或其他儲存體帳戶使用者錯誤地刪除資料時，更輕鬆地復原資料。 若要深入了解虛刪除，請參閱[如何防止意外刪除 Azure 檔案共用](storage-files-prevent-file-share-deletion.md)。

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

虛刪除 Cmdlet 目前僅適用于 [2.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) 和 [2.3.1-預覽](https://www.powershellgallery.com/packages/Az.Storage/2.3.1-preview) 版本的 Az. Storage 模組。 

## <a name="getting-started"></a>開始使用

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

您可以在 2.1.1-preview 版本的 Az. Storage 模組中使用虛刪除 Cmdlet。 若要還原虛刪除的檔案共用，請使用下列命令：

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

您可以在 2.1.1-preview 版本的 Az. Storage 模組中使用虛刪除 Cmdlet。 您可以使用下列命令，在儲存體帳戶上停用虛刪除：

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>接下來的步驟

若要瞭解另一種形式的資料保護和復原，請參閱 [Azure 檔案儲存體的共用快照集簡介](storage-snapshots-files.md)。
