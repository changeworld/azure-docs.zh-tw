---
title: 當您在 Azure 中建立虛擬機器時，不支援 VHD |Microsoft Docs
description: 本文可協助您在 Microsoft Azure 中執行虛擬機器時更正 VHD 錯誤。
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: ff4822b513ed2aea6a18ba45bffc1d060ee2410e
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937502"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>當您在 Azure 中建立虛擬機器時，不支援 VHD

本文可協助修正在 Windows 或 Linux 中執行虛擬機器時的 VHD 錯誤。

## <a name="symptoms"></a>徵狀

當您使用上傳的 VHD 在 Microsoft Azure 中建立虛擬機器時，部署將會失敗並傳回下列錯誤訊息： 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>原因

這個問題發生的原因如下：

- VHD 不符合 1 MB 對齊 (位移)。 支援的磁碟大小應為 1 MB * N。例如，磁碟應為 102,401 MB。
- VHD 已損毀或不受支援。 

## <a name="resolution"></a>解決方案

> [!NOTE]
> 若要執行下列修正，客戶必須先執行這些步驟，再將 VHD 上傳至 Azure。

若要解決此問題，請調整磁片大小以符合 1 MB 的對齊方式：

- 若要解決 Windows 中的問題，請使用重[設大小-VHD PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/hyper-v/resize-vhd)。 請注意，重**設大小-VHD**不是 Azure PowerShell Cmdlet。

  1. [在 Windows Server 上安裝 Hyper-v 角色](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [將虛擬磁片轉換成固定大小的 VHD](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#convert-the-virtual-disk-to-a-fixed-size-vhd)

- 若要解決 Linux 中的問題，請使用[qemu-img convert-img 命令](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)。

如需有關如何建立和上傳 VHD 以建立 Azure VM 的詳細資訊，請參閱下列文章：

- [使用 Azure CLI 1.0 從自訂磁碟映像上傳並建立 Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)
- [建立並上傳 Windows Server VHD 到 Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

繼續問題可能表示 VHD 損毀。 在此情況下，建議您從頭重建 VHD。

如需詳細資訊，請參閱下列文章：

- [關於 Windows VHD](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#about-vhds)
- [關於 Linux VHD](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds#about-vhds)