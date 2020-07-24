---
title: Azure VMware Solution by CloudSimple-在 Azure 中管理私人雲端 Vm
description: 說明如何管理 Azure 入口網站中的 CloudSimple 私人雲端 Vm，包括新增磁片、變更 VM 容量，以及新增網路介面
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 82aa1ba6ec20e8242568a2725899ddc515a4dc0f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082064"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>在 Azure 中管理您的 CloudSimple 私用雲端虛擬機器

若要管理您[為 CloudSimple 私人雲端建立](azure-create-vm.md)的虛擬機器，請登入[Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [虛擬] （在 [**所有服務**] 或側邊功能表上的 [**虛擬機器**] 底下搜尋）。

## <a name="control-virtual-machine-operation"></a>控制虛擬機器操作

您可以從所選虛擬機器的 [**總覽**] 頁面取得下列控制項。

| 控制 | 描述 |
| ------------ | ------------- |
| 連線 | 連接到指定的 VM。  |
| 開始 | 啟動指定的 VM。  |
| 重新啟動 | 關閉並開啟指定的 VM。  |
| Stop | 關閉特定的 VM。  |
| 擷取 | 捕獲指定 VM 的映射，讓它可以用來做為建立其他 Vm 的映射。 請參閱[在 Azure 中建立一般化 VM 的受控映射](../virtual-machines/windows/capture-image-resource.md)。   |
| 移動 | 移至指定的 VM。  |
| 刪除 | 移除指定的 VM。  |
| 重新整理 | 重新整理顯示中的資料。  |

### <a name="view-performance-information"></a>檢視效能資訊

[**總覽**] 頁面下方的圖表會顯示所選間隔的效能資料（過去一小時到過去30天; 預設值為 [過去1小時]）。 在每個圖表中，您可以在間隔內的任何時間顯示數值，方法是在圖表上來回移動資料指標。

系統會顯示下列圖表。

| 項目 | 說明 |
| ------------ | ------------- |
| CPU （平均） | 所選間隔的平均 CPU 使用率（以百分比表示）。   |
| 網路 | 在所選間隔內進出網路的流量（MB）。  |
| 磁片位元組 | 在選取的間隔內，從磁片讀取並寫入磁片（MB）的資料總數。  |
| 磁碟作業 | 所選間隔的磁片作業平均速率（作業數/秒）。 |

## <a name="manage-vm-disks"></a>管理虛擬機器磁碟

若要新增 VM 磁片，請開啟所選取 VM 的 [**磁片**] 頁面。 若要新增磁片，請按一下 [**新增磁片**]。 輸入或選取內嵌選項，以設定下列每個設定。 按一下 [檔案] 。

   | 項目 | 說明 |
   | ------------ | ------------- |
   | 名稱 | 輸入識別磁碟的名稱。  |
   | Size | 選取其中一個可用的大小。  |
   | SCSI 控制器 | 選取 SCSI 控制器。 可用的控制器會因不同的支援作業系統而有所不同。  |
   | [模式] | 決定磁片如何參與快照集。 請選擇其中一個選項： <br> 獨立的持續性：寫入磁片的所有資料都會永久寫入。<br> 獨立的非持續性：當您關閉或重設虛擬機器時，會捨棄寫入磁片的變更。  此模式可讓您一律以相同的狀態重新開機 VM。 如需詳細資訊，請參閱 [VMware 文件](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)。 |

若要刪除磁片，請選取它，然後按一下 [**刪除**]。

## <a name="change-the-capacity-of-the-vm"></a>變更 VM 的容量

若要變更 VM 的容量，請開啟所選取 VM 的 [**大小**] 頁面。 指定下列任何一項，然後按一下 [**儲存**]。

| 項目 | 說明 |
| ------------ | ------------- |
| 核心數目 | 指派給 VM 的核心數目。  |
| 硬體虛擬化 | 選取核取方塊，將硬體虛擬化公開給客體作業系統。 請參閱 VMware 一文：[公開 Vmware 硬體輔助虛擬化](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)。 |
| 記憶體大小 | 選取要配置給 VM 的記憶體數量。  

## <a name="manage-network-interfaces"></a>管理網路介面

若要新增介面，請按一下 [**新增網路介面**]。 輸入或選取內嵌選項，以設定下列每個設定。 按一下 [檔案] 。

   | 控制 | 描述 |
   | ------------ | ------------- |
   | 名稱 | 輸入識別介面的名稱。  |
   | 網路 | 從私人雲端 vSphere 中已設定的網路清單中選取。  |
   | 配接器 | 從為 VM 設定的可用類型清單中，選取 vSphere 介面卡。 如需詳細資訊，請參閱 VMware 知識庫文章[選擇虛擬機器的網路介面卡](https://kb.vmware.com/s/article/1001805)。 |
   | 在開機時開啟電源 | 選擇是否要在 VM 開機時啟用 NIC 硬體。 預設值為 [啟用]****。 |

若要刪除網路介面，請選取它，然後按一下 [**刪除**]。
