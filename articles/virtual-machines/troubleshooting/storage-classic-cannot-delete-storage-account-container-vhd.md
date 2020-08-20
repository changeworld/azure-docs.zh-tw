---
title: 針對刪除 Azure 傳統儲存體帳戶、容器或 VHD 時所發生的錯誤進行疑難排解 | Microsoft Docs
description: 如何在刪除包含連結 VHD 的儲存體資源時，針對所發生的問題進行疑難排解。
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 3e7469f0d53a154f605480b811d36937e3d4ad6c
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649834"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>針對傳統儲存體資源刪除錯誤進行疑難排解
本文章提供疑難排解指引，協助您排解在嘗試刪除 Azure 傳統儲存體帳戶、容器或 *.vhd 分頁 Blob 檔案時發生的以下任一錯誤。 


本文僅涵蓋傳統儲存體資源的問題。 如果使用者使用 Azure 入口網站、PowerShell 或 CLI 刪除傳統虛擬機器，則磁碟不會自動刪除。 使用者可以選取刪除「磁碟」資源。 如果未選取該選項，「磁碟」資源會防止存儲體帳戶、容器和實際 *.vhd 分頁 Blob 檔案遭到刪除。

如需 Azure 磁碟的詳細資訊，[請參閱](../../virtual-machines/managed-disks-overview.md)。 Azure 能預防刪除已連接 VM 的磁碟，以避免損毀。 它也能預防刪除分頁 Blob 已連接 VM 的容器和儲存體帳戶。 

## <a name="what-is-a-disk"></a>什麼是「磁碟」？
「磁碟」資源是用來將 *.vhd 分頁 Blob 檔案裝載到虛擬機器，作為作業系統磁碟或資料磁碟。 在刪除之前，作業系統磁碟或資料磁碟資源將繼續租用 *.vhd 檔案。 如果「磁碟」資源指向下圖所示路徑中的任何儲存體資源，則該儲存體資源無法刪除。

![開啟磁碟 (傳統)「屬性」窗格的入口網站螢幕擷取畫面](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>刪除傳統虛擬機器的步驟 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. 刪除傳統虛擬機器。
2. 如果已選取 [磁碟] 核取方塊，則與分頁 Blob *.vhd 相關聯的**磁碟租用** (如上圖所示) 已中斷。 實際分頁 Blob *.vhd 檔案仍會存在於儲存體帳戶中。
![開啟虛擬機器 (傳統)「刪除」錯誤窗格的入口網站螢幕擷取畫面](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. 磁碟租用中斷之後，就可以刪除分頁 Blobb 本身。 在刪除儲存體帳戶或容器上的所有「磁碟」資源之後，即可刪除儲存體帳戶或容器。

>[!NOTE] 
>如果使用者刪除 VM 但不刪除 VHD，則會在分頁 Blob *.vhd 檔案上繼續累積費用。 費用會與儲存體帳戶類型一致，如需詳細資訊，請查看[定價頁面](https://azure.microsoft.com/pricing/details/storage/)。 如果使用者不再打算使用 VHD，請將其刪除以避免產生未來的費用。 

## <a name="unable-to-delete-storage-account"></a>無法刪除儲存體帳戶 

當使用者嘗試刪除不再需要的傳統儲存體帳戶時，使用者可能會看到下列行為。

#### <a name="azure-portal"></a>Azure 入口網站 
使用者導覽至 [Azure 入口網站](https://portal.azure.com)上的傳統儲存體帳戶，然後按一下 [刪除]****，使用者會看到下列訊息： 

使用「附加」到虛擬機器的磁碟

![開啟虛擬機器 (傳統)「刪除」錯誤窗格的入口網站螢幕擷取畫面](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


使用「未附加」到虛擬機器的磁碟

![虛擬機器 (傳統)「刪除」非錯誤窗格的入口網站螢幕擷取畫面](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
使用者嘗試使用傳統 PowerShell Cmdlet 刪除不再使用的儲存體帳戶。 使用者會看到下列訊息：

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">New-azurestorageaccount： BadRequest：儲存體帳戶 myclassicaccount 有一些作用中的映射 (s) 和/或磁片 (s) ，例如  
> myclassicaccount. 確認這些映像及/或磁碟已移除之後再刪除此儲存體帳戶。</span>

## <a name="unable-to-delete-storage-container"></a>無法刪除儲存體容器

當使用者嘗試刪除不再需要的傳統儲存體 Blob 容器時，使用者可能會看到下列行為。

#### <a name="azure-portal"></a>Azure 入口網站 
如果存在的「磁碟」租用指向容器中的 *.vhd 分頁 Blob 檔案，則 Azure 入口網站不允許使用者刪除容器。 它的設計是用來防止意外刪除具有磁碟租用的 vhd 檔案。 

![開啟儲存體容器「清單」窗格的入口網站螢幕擷取畫面](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
如果使用者選擇使用 PowerShell 進行刪除，則會導致下列錯誤。 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">>new-azurestoragecontainer：遠端伺服器傳回錯誤： (412) 容器上目前有租用，且要求中沒有指定任何租用識別碼。HTTP 狀態碼： 412-HTTP 錯誤訊息：目前容器上有租用，且要求中沒有指定任何租用識別碼。</span>

## <a name="unable-to-delete-a-vhd"></a>無法刪除 vhd 

刪除 Azure 虛擬機器之後，使用者嘗試刪除 vhd 檔案 (分頁 Blob)，並收到下列訊息：

#### <a name="azure-portal"></a>Azure 入口網站 
在入口網站上，根據選擇要刪除的 Blob 清單，可能會有兩種體驗。

1. 如果只選取 [已租用] Blob，則不會顯示 [刪除] 按鈕。
![開啟容器 Blob「清單」窗格的入口網站螢幕擷取畫面](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. 如果混合選取了 [已租用] 和 [可用] Blob，則會顯示 [刪除] 按鈕。 但是 [刪除] 作業會留下其上具有磁碟租用的分頁 Blob。 
![開啟容器 Blob「清單」窗格的入口網站螢幕擷取畫面](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![開啟選擇的 Blob「刪除」窗格的入口網站螢幕擷取畫面](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
如果使用者選擇使用 PowerShell 進行刪除，則會導致下列錯誤。 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">>get-azurestorageblob：遠端伺服器傳回錯誤： (412) blob 上目前有租用，且要求中沒有指定任何租用識別碼。HTTP 狀態碼： 412-HTTP 錯誤訊息： blob 上目前有租用，且要求中沒有指定任何租用識別碼。</span>


## <a name="resolution-steps"></a>解決步驟

### <a name="to-remove-classic-disks"></a>若要移除傳統磁碟
在 Azure 入口網站中遵循下列步驟：
1.  瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2.  瀏覽至磁碟 (傳統)。 
3.  按一下 [磁片] 索引標籤。 ![入口網站的螢幕擷取畫面，其中已開啟容器 blob 「清單」窗格](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  選取資料磁碟，然後按一下 [刪除磁碟]。
 ![開啟容器 Blob「清單」窗格的入口網站螢幕擷取畫面](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  重試先前失敗的刪除作業。
6.  只要儲存體帳戶或容器具有單一磁碟，就無法加以刪除。

### <a name="to-remove-classic-images"></a>若要移除傳統磁碟   
在 Azure 入口網站中遵循下列步驟：
1.  瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2.  瀏覽至作業系統映像 (傳統)。
3.  刪除映像。
4.  重試先前失敗的刪除作業。
5.  只要儲存體帳戶或容器具有單一映像，就無法加以刪除。
