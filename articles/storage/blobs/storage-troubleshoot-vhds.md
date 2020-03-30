---
title: 針對連結至 Azure VM 的磁碟進行疑難排解 | Microsoft Docs
description: 提供針對 Azure 虛擬機器虛擬硬碟 (VHD) 的資源進行疑難排解的連結。
services: storage
author: roygara
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: b81c0947327dc8e84c5d3fbbecde3aa31313cc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061191"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>針對連結至 Azure VM 的磁碟進行疑難排解 

對於作業系統磁碟和任何連結的資料磁碟，Azure 虛擬機器 (VM) 需仰賴虛擬硬碟 (VHD)。 VHD 會以分頁 Blob 的形式儲存在一或多個 Azure 儲存體帳戶中。 本文指向 VHD 可能發生之常見問題的疑難排解內容。 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>針對 VM 的儲存體刪除錯誤進行疑難排解

在某些情況下，如果資源管理員部署中的 VM 包含連結的 VHD，則刪除儲存體資源時可能會發生錯誤。 如需解決此問題的說明，請參閱下列其中一篇文章： 

  * Linux VM：[資源管理員部署中的儲存體刪除錯誤](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Windows VM：[資源管理員部署中的儲存體刪除錯誤](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>針對包含連結 VHD 的 VM 非預期重新開機進行疑難排解

如果您遇到包含大量連結 VHD 的 VM 非預期重新開機，請參閱下列其中一篇文章：

  * Linux VM：[包含連結 VHD 的 VM 非預期重新開機](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Windows VM：[包含連結 VHD 的 VM 非預期重新開機](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
