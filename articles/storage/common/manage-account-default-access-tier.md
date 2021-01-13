---
title: 管理 Azure 儲存體帳戶的預設存取層
description: 瞭解如何變更 GPv2 或 Blob 儲存體帳戶的預設存取層
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 10d4e99d7bbebb6bc7d7def308e233507ed99ce9
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166472"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>管理 Azure 儲存體帳戶的預設存取層

每個 Azure 儲存體帳戶都有一個預設存取層，也就是經常性存取、非經常性存取層或封存。 當您建立儲存體帳戶時，您會指派存取層。 預設存取層為熱。

您可以藉由在儲存體帳戶上設定 **存取層** 屬性來變更預設帳戶層級。 變更帳戶層級會套用至帳戶中儲存的所有物件，而該帳戶並未設定明確的層級。 將帳戶層從經常性存取切換至非經常性存取時，會產生所有 blob 的寫入作業 (每 10000) ，而不需要 GPv2 帳戶中的設定層，且從非經常性存取會產生每個 10000) 的讀取 (作業，以及對 Blob 儲存體和 GPv2 帳戶中所有 blob 的資料抓取 (每 GB) 費用。

如果是在物件層級設定階層的 blob，則不適用帳戶層。 封存層只能套用在物件層級。 您可以隨時在存取層之間切換。

您可以遵循下列步驟，在建立儲存體帳戶之後變更預設存取層。

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>變更 GPv2 或 Blob 儲存體帳戶的預設帳戶存取層

下列案例使用 Azure 入口網站或 PowerShell：

# <a name="portal"></a>[入口網站](#tab/portal)

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站中，搜尋並選取 [所有資源]。

1. 選取您的儲存體帳戶。

1. 在 [設定] 中 **，選取 [** 設定] 以查看及變更帳戶 **設定**。

1. 針對您的需求選取正確的存取層：將 **存取層** 設定為 [非經常性存取 **] 或 [** 經常性存取 **]。**

1. 按一下頂端的 [ **儲存** ]。

![變更 Azure 入口網站中的預設帳戶層](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

您可以使用下列 PowerShell 腳本來變更帳戶層。 `$rgName` 變數必須以您的資源群組名稱進行初始化。 `$accountName` 變數必須以您的儲存體帳戶名稱進行初始化。

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>後續步驟

- [如何管理 Azure 儲存體帳戶中的 blob 層](../blobs/manage-access-tier.md)
- [判斷 premium 效能是否能讓您的應用程式受益](../blobs/storage-blob-performance-tiers.md)
- [啟用 Azure 儲存體計量以評估您目前的儲存體帳戶使用量](../blobs/monitor-blob-storage.md)
