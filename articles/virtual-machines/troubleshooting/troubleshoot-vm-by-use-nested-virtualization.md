---
title: 使用 Azure 中的嵌套虛擬化對故障 Azure VM 進行故障排除 |微軟文檔
description: 如何在 Azure 中使用巢狀虛擬化來疑難排解問題 Azure VM
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: e1acfc3216ccfaeac035f1ff31e82c7b67c17daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119613"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>使用 Azure 中的嵌套虛擬化對故障 Azure VM 進行故障排除

本文演示如何在 Microsoft Azure 中創建嵌套虛擬化環境，以便可以將故障 VM 的磁片裝載到 Hyper-V 主機（救援 VM）上以進行故障排除。

## <a name="prerequisites"></a>Prerequisites

要裝載故障 VM，救援 VM 必須使用與故障 VM 相同的存儲帳戶類型（標準帳戶或高級帳戶）。

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>步驟 1：建立救援 VM 並安裝 Hyper-V 角色

1.  建立新的救援 VM：

    -  作業系統：Windows Server 2016 資料中心

    -  大小：支援巢狀虛擬化的任何 V3 系列 (至少具有兩個核心)。 如需詳細資訊，請參閱[新 Dv3 和 Ev3 VM 大小簡介](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)。

    -  與故障 VM 相同的位置、存儲帳戶和資源組。

    -  選擇與故障 VM（標準 VM 或高級 VM）相同的存儲類型。

2.  建立「救援 VM」之後，建立「救援 VM」的遠端桌面。

3.  在伺服器管理員中，選擇 **"管理** > **添加角色和功能**"。

4.  在 [安裝類型]**** 區段中，選取 [角色型或功能型安裝]****。

5.  在 [選取目的地伺服器]**** 區段中，確定已選取「救援 VM」。

6.  選擇**Hyper-V 角色** > **添加功能**。

7.  在 [功能]**** 區段上選取 [下一步]****。

8.  如果提供虛擬交換器，請選取它。 否則，請選取 [下一步]****。

9.  在 [移轉]**** 區段中，選取 [下一步]****

10. 在 [預設存放區]**** 區段中，選取 [下一步]****。

11. 視需要勾選方塊，以自動重新啟動伺服器。

12. 選取 [安裝]****。

13. 允許伺服器安裝 Hyper-V 角色。 這需要幾分鐘，而且伺服器會自動重新開機。

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>第 2 步：在救援 VM 的 Hyper-V 伺服器上創建故障 VM

1.  為有問題的 VM 的 OS 磁片[創建快照磁片](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk)，然後將快照磁片附加到重新使用 VM。

2.  遠端桌面到救援 VM。

3.  開啟「磁碟管理」(diskmgmt.msc)。 確保故障 VM 的磁片設置為**離線**。

4.  開啟 [Hyper-V 管理員]：在 [伺服器管理員]**** 中，選取 [Hyper-V 角色]****。 以滑鼠右鍵按一下伺服器，然後選取 [Hyper-V 管理員]****。

5.  在 Hyper-V 管理器中，按右鍵"救援 VM"，然後選擇 **"新建** > **虛擬機器** > **下一個**"。

6.  輸入 VM 的名稱，然後選取 [下一步]****。

7.  選取 [第 1 代]****。

8.  將啟動記憶體設定為 1024 MB 以上。

9. 如果適用，請選取已建立的 Hyper-V 網路交換器。 否則，請移至下一頁。

10. 選取 [稍後連接虛擬硬碟]****。

    ![「稍後連接虛擬硬碟」選項的圖片](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. 建立 VM 後，選取 [完成]****。

12. 以滑鼠右鍵按一下您建立的 VM，然後選取 [設定]****。

13. 選取 [IDE 控制器 0]****，選取 [硬碟]****，然後按一下 [新增]****。

    ![新增硬碟的圖片](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. 在**物理硬碟**中，選擇附加到 Azure VM 的故障 VM 的磁片。 如果看不到任何列出的磁碟，請使用磁碟管理檢查磁碟是否設定為離線。

    ![掛接磁碟的圖片](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. 選取 [套用]****，然後選取 [確定]****。

16. 按兩下 VM，並加以啟動。

17. 現在，您可以將 VM 作為內部部署 VM 進行運作。 可以按照任何您所需要的疑難排解步驟進行。

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>步驟 3：更換故障 VM 使用的作業系統磁片

1.  讓 VM 恢復上線之後，在 Hyper-V 管理員中關閉 VM。

2.  [卸載並分離已修復的 OS 磁片](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
)。
3.  [將 VM 使用的 OS 磁片替換為已修復的 OS 磁片](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
)。

## <a name="next-steps"></a>後續步驟

如果連接至 VM 時發生問題，請參閱[針對 Azure VM 的 RDP 連接進行疑難排解](troubleshoot-rdp-connection.md)。 如果存取 VM 上執行的應用程式時發生問題，請參閱[針對 Windows VM 上的應用程式連線問題進行疑難排解](troubleshoot-app-connection.md)。
