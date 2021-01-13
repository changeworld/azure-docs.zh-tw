---
title: 管理 Azure 儲存體帳戶中的 blob 存取層
description: 瞭解如何在 GPv2 或 Blob 儲存體帳戶中變更 blob 的層級
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 183593022c934eaf52ffe18649c23e8deced34d8
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166477"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>管理 Azure 儲存體帳戶中的 blob 存取層

每個 blob 都有一個預設存取層，也就是經常性存取、非經常性存取層或封存。 Blob 會在建立此帳戶的 Azure 儲存體帳戶的預設存取層上取得。 Blob 儲存體和 GPv2 帳戶會在帳戶層級公開 **存取層** 屬性。 這個屬性會指定任何未在物件層級明確設定之 blob 的預設存取層。 對於在物件層級設定階層的物件，帳戶層將不適用。 封存層只能套用於物件層級。 您可以遵循下列步驟，隨時在存取層之間切換。

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>變更 GPv2 或 Blob 儲存體帳戶中的 blob 層級

下列案例使用 Azure 入口網站或 PowerShell：

# <a name="portal"></a>[入口網站](#tab/portal)

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站中，搜尋並選取 [所有資源]。

1. 選取您的儲存體帳戶。

1. 選取容器，然後選取您的 Blob。

1. 在 [Blob 屬性] 中，選取 [變更階層]。

1. 選取經常性 **存取、非****經常性存取層** 或封存存取層。  如果您的 blob 目前處於封存狀態，而且您想要解除凍結到線上層，您也可以選取 [ **標準** ] 或 [ **高**] 的解除凍結優先權。

1. 在底部選取 [儲存]。

![變更 Azure 入口網站中的 blob 層](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

您可以使用下列 PowerShell 腳本來變更 blob 層。 `$rgName` 變數必須以您的資源群組名稱進行初始化。 `$accountName` 變數必須以您的儲存體帳戶名稱進行初始化。 `$containerName` 變數必須以您的容器名稱進行初始化。 `$blobName` 變數必須以您的 Blob 名稱進行初始化。

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>後續步驟

- [如何管理 Azure 儲存體帳戶的預設帳戶存取層](../common/manage-account-default-access-tier.md)
- [瞭解如何從封存層解除凍結 blob 資料](storage-blob-rehydration.md)
- [依區域檢查 Blob 儲存體和 GPv2 帳戶中的經常性存取、非經常性存取和封存價格](https://azure.microsoft.com/pricing/details/storage/)
