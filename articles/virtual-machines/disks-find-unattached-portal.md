---
title: 識別未連結的 Azure 磁碟 - Azure 入口網站
description: 如何使用 Azure 入網站尋找未連結的 Azure 受控和非受控 (VHD/分頁 Blob) 磁碟。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/01/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3a5e2c368fd956abb98b2b4c9ccd1060677f3e06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085753"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>尋找及刪除未連結的 Azure 受控和非受控磁碟 - Azure 入口網站

當您在 Azure 中刪除虛擬機器 (VM) 時，不會刪除任何連結至 VM 的磁碟。 這項功能有助於預防因為不小心刪除 VM 所造成的資料遺失。 刪除 VM 之後，您將繼續支付未連結的磁碟。 本文示範如何使用 Azure 入口網站尋找及刪除任何未連結的磁碟，並減少不必要的成本。

## <a name="managed-disks-find-and-delete-unattached-disks"></a>受控磁碟：尋找及刪除未連結的磁碟

如果您有未連結的受控磁碟，而且不再需要在這些磁碟上的資料，下列程式將說明如何從 Azure 入口網站尋找這些磁碟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 搜尋並選取 [磁碟]。

    在 [磁碟] 刀鋒視窗中，您會看到所有磁碟清單。 [擁有者] 資料行中含有「 **-** 」的磁碟都是未連結的磁碟。

    [![](media/disks-find-unattached-portal/managed-disk-unattached-owner.png "Screenshot of the managed disks blade, if a disk has - in the Owner column, it is an unattached disk")](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. 選取要刪除的未連結磁碟，即可開啟磁碟的刀鋒視窗。
1. 在磁碟的刀鋒視窗上，確認磁碟狀態為未連結，然後選取 [刪除]。

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="個別受控磁碟刀鋒視窗的螢幕擷取畫面。如果未連結，此刀鋒視窗會顯示磁碟狀態為未連結。如果不需要再保留磁碟上的資料，可以刪除此磁碟":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>非受控磁碟：尋找及刪除未連結的磁碟

非受控磁碟是 VHD 檔案，會以[分頁 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) 的形式儲存在 [Azure 儲存體帳戶](../storage/common/storage-account-overview.md)中。

如果您有未連結至 VM 的非受控磁碟，且不再需要磁碟上的資料，而想要將磁碟刪除，下列流程說明如何從 Azure 入口網站執行這項操作：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 搜尋並選取 [磁碟 (傳統版)]。

    您會看到所有非受控磁碟的清單。 [連結至] 資料行中任何具有「 **-** 」的磁碟都是未連結的磁碟。

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="非受控磁碟刀鋒視窗的螢幕擷取畫面。此刀鋒視窗中 [已連結至] 資料行的磁碟是未連結的磁碟。":::

1. 選取您想要刪除的未附加磁碟，這會開啟磁碟的分頁。

1. 在磁碟的刀鋒視窗上，您可以確認它並未連結，因為 [已連結至] 仍顯示為 **-** 。

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="個別非受控磁碟刀鋒的螢幕擷取畫面。如果未連結，它會以「-」作為 [已連結至] 的值。如果您不再需要此磁碟資料，可以將它刪除。":::

1. 選取 [刪除] 。

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="個別非受控磁碟刀鋒視窗的螢幕擷取畫面，其中反白顯示「刪除」。":::

## <a name="next-steps"></a>後續步驟

如需自動尋找和刪除未連結的儲存體帳戶，請參閱我們的 [CLI](linux/find-unattached-disks.md) 或 [PowerShell](windows/find-unattached-disks.md) 文章。

如需詳細資訊，請參閱[刪除儲存體帳戶](../storage/common/storage-account-create.md#delete-a-storage-account)和[使用 PowerShell 識別孤立的磁碟](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)
