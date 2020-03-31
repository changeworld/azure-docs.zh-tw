---
title: Azure VMware 解決方案（按雲簡單 ） - 在 Azure 中管理私有雲 VM
description: 介紹如何在 Azure 門戶中管理雲簡單私有雲 VM，包括添加磁片、更改 VM 容量和添加網路介面
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014992"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>在 Azure 中管理雲簡單私有雲虛擬機器

要管理[為雲簡單私有雲創建的虛擬機器](azure-create-vm.md)，請登錄到[Azure 門戶](https://portal.azure.com)。 搜索並選擇虛擬（在側功能表上**的所有服務**或**虛擬機器**下搜索）。

## <a name="control-virtual-machine-operation"></a>控制虛擬機器操作

以下控制項可從所選虛擬機器的 **"概述"** 頁獲得。

| 控制 | 描述 |
| ------------ | ------------- |
| 連線 | 連接到指定的 VM。  |
| Start | 啟動指定的 VM。  |
| 重新啟動 | 關閉，然後打開指定的 VM 電源。  |
| Stop | 關閉特定 VM。  |
| 擷取 | 捕獲指定 VM 的映射，以便將其用作創建其他 VM 的圖像。 請參閱[在 Azure 中創建通用 VM 的託管映射](../virtual-machines/windows/classic/capture-image.md)。   |
| 移動 | 移動到指定的 VM。  |
| 刪除 | 刪除指定的 VM。  |
| Refresh | 刷新顯示幕中的資料。  |

### <a name="view-performance-information"></a>檢視效能資訊

**"概述"** 頁下部的圖表顯示所選間隔的效能資料（最後一小時到持續 30 天;預設值為最後一小時）。 在每個圖表中，您可以通過在圖表上來回移動游標來顯示間隔內任意時間的數值。

將顯示以下圖表。

| Item | 描述 |
| ------------ | ------------- |
| CPU（平均） | 所選間隔內的平均 CPU 利用率（百分比）。   |
| 網路 | 在所選間隔內進出網路 （MB） 的流量。  |
| 磁片位元組 | 在所選間隔內從磁片讀取並寫入磁片 （MB） 的總數據。  |
| 磁碟作業 | 所選間隔內磁片操作的平均速率（操作/秒）。 |

## <a name="manage-vm-disks"></a>管理虛擬機器磁碟

要添加 VM 磁片，打開所選 VM 的**磁片**頁面。 要添加磁片，請按一下"**添加磁片**"。 通過輸入或選擇內聯選項配置以下每個設置。 按一下 [儲存]****。

   | Item | 描述 |
   | ------------ | ------------- |
   | 名稱 | 輸入識別磁碟的名稱。  |
   | 大小 | 選取其中一個可用的大小。  |
   | SCSI 控制器 | 選擇 SCSI 控制器。 可用的控制器因受支援的不同作業系統而異。  |
   | [模式] | 確定磁片參與快照的方式。 請選擇其中一個選項： <br> - 獨立持久性：寫入磁片的所有資料都是永久寫入的。<br> - 獨立、非持久性：當您關閉或重置虛擬機器時，將丟棄寫入磁片的更改。  此模式允許您始終以相同的狀態重新開機 VM。 如需詳細資訊，請參閱 [VMware 文件](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)。 |

要刪除磁片，請選擇它，然後按一下"**刪除**"。

## <a name="change-the-capacity-of-the-vm"></a>更改 VM 的容量

要更改 VM 的容量，打開所選 VM**的大小頁。** 指定以下任一項，然後按一下"**保存**"。

| Item | 描述 |
| ------------ | ------------- |
| 核心數目 | 分配給 VM 的內核數。  |
| 硬體虛擬化 | 選擇該核取方塊以向客體作業系統公開硬體虛擬化。 請參閱 VMware 文章["公開 VMware 硬體輔助虛擬化](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)"。 |
| 記憶體大小 | 選擇要分配給 VM 的記憶體量。  

## <a name="manage-network-interfaces"></a>管理網路介面

要添加介面，請按一下"**添加網路介面**"。 通過輸入或選擇內聯選項配置以下每個設置。 按一下 [儲存]****。

   | 控制 | 描述 |
   | ------------ | ------------- |
   | 名稱 | 輸入識別介面的名稱。  |
   | 網路 | 從私有雲 vSphere 中配置的網路清單中選擇。  |
   | 配接器 | 從為 VM 配置的可用類型清單中選擇 vSphere 配接器。 有關詳細資訊，請參閱 VMware 知識庫文章[為虛擬機器選擇網路介面卡](https://kb.vmware.com/s/article/1001805)。 |
   | 在開機時開啟電源 | 選擇是否要在 VM 開機時啟用 NIC 硬體。 預設值為 [啟用]****。 |

要刪除網路介面，請選擇它並按一下"**刪除**"。
