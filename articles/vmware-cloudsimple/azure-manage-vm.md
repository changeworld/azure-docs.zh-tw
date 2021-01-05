---
title: Azure VMware Solution by CloudSimple-管理 Azure 中的私人雲端 Vm
description: 說明如何管理 Azure 入口網站中的 CloudSimple 私人雲端 Vm，包括新增磁片、變更 VM 容量，以及新增網路介面
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 090b7711ab061b989eae13113fe7048e8dd875ee
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895184"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>在 Azure 中管理您的 CloudSimple 私用雲端虛擬機器

若要管理您 [為 CloudSimple 私人雲端建立](azure-create-vm.md)的虛擬機器，請登入 [Azure 入口網站](https://portal.azure.com)。 在側邊功能表上，搜尋並選取 [ **所有服務** 或 **虛擬機器** ] 底下的 [虛擬 (搜尋]) 。

## <a name="control-virtual-machine-operation"></a>控制虛擬機器操作

您可以從所選虛擬機器的 [ **總覽** ] 頁面使用下列控制項。

| 控制 | 描述 |
| ------------ | ------------- |
| 連線 | 連接到指定的 VM。  |
| 開始 | 啟動指定的 VM。  |
| 重新啟動 | 關閉並開啟指定的 VM。  |
| Stop | 關閉特定的 VM。  |
| 擷取 | 捕獲指定 VM 的映射，讓它可以作為映射來建立其他 Vm。 請參閱 [在 Azure 中建立一般化 VM 的受控映射](../virtual-machines/windows/capture-image-resource.md)。   |
| 移動 | 移至指定的 VM。  |
| 刪除 | 移除指定的 VM。  |
| 重新整理 | 重新整理顯示中的資料。  |

### <a name="view-performance-information"></a>檢視效能資訊

[ **總覽** ] 頁面下方區域中的圖表會顯示所選間隔 (過去一小時到過去30天的效能資料;預設值為 [前1小時]) 。 在每個圖表中，您可以在圖表中來回移動游標，以顯示間隔內任何時間的數位值。

下列圖表隨即顯示。

| Item | 描述 |
| ------------ | ------------- |
| CPU (平均)  | 所選間隔的平均 CPU 使用率（以百分比表示）。   |
| 網路 | 在選取的間隔內，網路中進出網路的流量 (MB) 。  |
| 磁片位元組 | 在選取的間隔內，從磁片讀取並寫入磁片 (MB) 的資料總計。  |
| 磁碟作業 | 所選間隔內 (作業/秒) 的平均磁片作業速率。 |

## <a name="manage-vm-disks"></a>管理虛擬機器磁碟

若要新增 VM 磁片，請開啟所選 VM 的 [ **磁片** ] 頁面。 若要新增磁片，請按一下 [ **新增磁片**]。 輸入或選取內嵌選項，以設定下列每一項設定。 按一下 [儲存]。

   | Item | 描述 |
   | ------------ | ------------- |
   | 名稱 | 輸入識別磁碟的名稱。  |
   | 大小 | 選取其中一個可用的大小。  |
   | SCSI 控制器 | 選取 SCSI 控制器。 可用的控制器會因不同的支援作業系統而有所不同。  |
   | [模式] | 決定磁片參與快照集的方式。 請選擇其中一個選項： <br> -獨立持續性：寫入磁片的所有資料都會永久寫入。<br> 獨立的非持續性：當您關閉或重設虛擬機器時，寫入磁片的變更會被捨棄。  此模式可讓您一律以相同的狀態重新開機 VM。 如需詳細資訊，請參閱 [VMware 文件](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)。 |

若要刪除磁片，請選取它，然後按一下 [ **刪除**]。

## <a name="change-the-capacity-of-the-vm"></a>變更 VM 的容量

若要變更 VM 的容量，請開啟所選取 VM 的 [ **大小** ] 頁面。 指定下列其中一項，然後按一下 [ **儲存**]。

| Item | 描述 |
| ------------ | ------------- |
| 核心數目 | 指派給 VM 的核心數目。  |
| 硬體虛擬化 | 選取核取方塊，將硬體虛擬化公開給虛擬作業系統。 請參閱 VMware 文章，以 [公開 Vmware 硬體輔助虛擬化](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)。 |
| 記憶體大小 | 選取要配置給 VM 的記憶體數量。  

## <a name="manage-network-interfaces"></a>管理網路介面

若要新增介面，請按一下 [ **新增網路介面**]。 輸入或選取內嵌選項，以設定下列每一項設定。 按一下 [儲存]。

   | 控制 | 描述 |
   | ------------ | ------------- |
   | 名稱 | 輸入識別介面的名稱。  |
   | 網路 | 從私用雲端 vSphere 中已設定的網路清單中選取。  |
   | 配接器 | 從為 VM 設定的可用類型清單中，選取 vSphere 的介面卡。 如需詳細資訊，請參閱 VMware 知識庫文章 [選擇虛擬機器的網路介面卡](https://kb.vmware.com/s/article/1001805)。 |
   | 在開機時開啟電源 | 選擇是否要在 VM 開機時啟用 NIC 硬體。 預設值為 [啟用]。 |

若要刪除網路介面，請選取它，然後按一下 [ **刪除**]。
