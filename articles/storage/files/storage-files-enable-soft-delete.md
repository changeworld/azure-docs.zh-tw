---
title: 啟用虛刪除 - Azure 檔案共用
description: 了解如何在 Azure 檔案共用上啟用虛刪除以進行資料復原，以及防止意外刪除。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/01/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 22e4d0998cde14d4461141a53f05cbc19d1ab671
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878964"
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

2.1.3 版和更新版本的 [Azure CLI 模組](/cli/azure/install-azure-cli?view=azure-cli-latest)都有提供虛刪除 Cmdlet。

## <a name="getting-started-with-cli"></a>開始使用 CLI

若要啟用虛刪除，您必須更新檔案用戶端的服務屬性。 下列範例會為儲存體帳戶中的所有檔案共用啟用虛刪除：

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

您可以使用下列命令來確認是否已啟用虛刪除，並檢視其保留原則：

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>必要條件

4.8.0 和新版的 Az. Storage 模組提供虛刪除 Cmdlet。 

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

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="如果 [狀態] 資料行 ([名稱] 資料行旁邊的資料行) 設定為 [已刪除]，則表示您的檔案共用處於虛刪除狀態。並且在您指定的保留期間之後永久刪除。":::

1. 選取 [共用]，然後選取 [取消刪除]，這會還原共用。

    您可以確認共用已還原，因為其狀態會切換為 [作用中]。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="如果 [狀態] 資料行 ([名稱] 資料行旁邊的資料行) 設定為 [作用中]，則表示您的檔案共用已還原。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

2.1.3 版 Azure CLI 中提供虛刪除 Cmdlet。 若要還原已虛刪除的檔案共用，您必須先取得 `--deleted-version` 共用的值。 若要取得該值，請使用下列命令來列出儲存體帳戶的所有已刪除共用：

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

一旦您識別要還原的共用，您可以使用它搭配下列命令來還原它：

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

4.8.0 和新版的 Az. Storage 模組提供虛刪除 Cmdlet。 若要還原已虛刪除的檔案共用，您必須先取得 `-DeletedShareVersion` 共用的值。 若要取得該值，請使用下列命令來列出儲存體帳戶的所有已刪除共用：

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

一旦您識別要還原的共用，您可以使用它搭配下列命令來還原它：

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

2.1.3 版 Azure CLI 中提供虛刪除 Cmdlet。 您可以使用下列命令，在儲存體帳戶上停用虛刪除：

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

4.8.0 和新版的 Az. Storage 模組提供虛刪除 Cmdlet。 您可以使用下列命令，在儲存體帳戶上停用虛刪除：

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>後續步驟

若要瞭解另一種形式的資料保護和復原，請參閱 [Azure 檔案儲存體的共用快照集簡介](storage-snapshots-files.md)。