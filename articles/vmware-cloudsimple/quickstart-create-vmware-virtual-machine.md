---
title: 快速入門：使用 Azure 上的 VMware Vm
titleSuffix: Azure VMware Solution by CloudSimple
description: 瞭解如何使用 CloudSimple 的 Azure VMware 解決方案，從 Azure 入口網站設定和取用 VMware Vm
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77019548"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>快速入門-使用 Azure 上的 VMware Vm

若要在 Azure 入口網站中建立虛擬機器，請使用 CloudSimple 系統管理員已為您的訂用帳戶啟用的虛擬機器範本。 您可以在 VMware 基礎結構中找到 VM 範本。

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>在 Azure 上建立 CloudSimple VM 需要 VM 範本

從 vCenter UI 在私人雲端上建立虛擬機器。 若要建立範本，請依照將[虛擬機器複製到 VSphere Web 用戶端](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)中的範本中的指示進行。 將 VM 範本儲存在您的私用雲端 vCenter 上。

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>在 Azure 入口網站中建立虛擬機器

1. 選取 [所有服務]  。

2. 搜尋 **CloudSimple 虛擬機器**。

3. 按一下 [加入]  。

    ![建立 CloudSimple 虛擬機器](media/create-cloudsimple-virtual-machine.png)

4. 輸入基本資訊，然後按 **[下一步：大小]**。

    ![建立 CloudSimple 虛擬機器-基本概念](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 欄位 | 描述 |
    | ------------ | ------------- |
    | 訂用帳戶 | 與私人雲端相關聯的 Azure 訂用帳戶。  |
    | 資源群組 | 將指派 VM 的資源群組。 您可以選取現有的群組或建立新的群組。 |
    | Name | 用以識別 VM 的名稱。  |
    | 位置 | 託管此 VM 的 Azure 區域。  |
    | 私人雲端 | CloudSimple 您想要在其中建立虛擬機器的私人雲端。 |
    | 資源集區 | VM 的對應資源集區。 請從可用的資源集區中選取。 |
    | vSphere 範本 | VM 的 vSphere 範本。  |
    | 使用者名稱 | VM 系統管理員的使用者名稱（適用于 Windows 範本）。|
    | 密碼 |  VM 系統管理員的密碼（適用于 Windows 範本）。 |
    | 確認密碼 | 確認密碼。 |

5. 選取 VM 的核心和記憶體容量數目，然後按 **[下一步：設定]**。 如果您想要將完整的 CPU 虛擬化公開給客體作業系統，請選取核取方塊。 需要硬體虛擬化的應用程式可以直接在虛擬機器上執行，而不需進行二進位轉譯或半虛擬化。 如需詳細資訊，請參閱 VMware 文章<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">公開 VMware 硬體輔助虛擬化</a>。

    ![建立 CloudSimple 虛擬機器-大小](media/create-cloudsimple-virtual-machine-size.png)

6. 依照下表所述設定網路介面和磁片，然後按一下 [**審查 + 建立**]。

    ![建立 CloudSimple 虛擬機器-設定](media/create-cloudsimple-virtual-machine-configurations.png)

    針對 [網路介面]，按一下 [**新增網路介面**] 並設定下列設定。

    | 控制 | 描述 |
    | ------------ | ------------- |
    | 名稱 | 輸入識別介面的名稱。  |
    | 網路 | 從私人雲端 vSphere 中已設定的分散式埠群組清單中選取。  |
    | 配接器 | 從為 VM 設定的可用類型清單中，選取 vSphere 介面卡。 如需詳細資訊，請參閱 VMware 知識庫文章<a href="https://kb.vmware.com/s/article/1001805" target="_blank">選擇虛擬機器的網路介面卡</a>。 |
    | 在開機時開啟電源 | 選擇是否要在 VM 開機時啟用 NIC 硬體。 預設值為 [啟用]****。 |

    針對 [磁片]，按一下 [**新增磁片**] 並設定下列設定。

    | 項目 | 描述 |
    | ------------ | ------------- |
    | 名稱 | 輸入識別磁碟的名稱。  |
    | Size | 選取其中一個可用的大小。  |
    | SCSI 控制器 | 選取磁碟的 SCSI 控制器。  |
    | [模式] | 決定磁片如何參與快照集。 請選擇其中一個選項： <br> 獨立的持續性：寫入磁片的所有資料都會永久寫入。<br> 獨立的非持續性：當您關閉或重設虛擬機器時，會捨棄寫入磁片的變更。  獨立而非持續的模式可讓您一律以相同的狀態重新啟動 VM。 如需詳細資訊，請參閱 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware 文件</a>。

7. 驗證完成時，請檢查設定，然後按一下 [**建立**]。 若要進行任何變更，請按一下頂端的索引標籤，或按一下 []。

    ![建立 CloudSimple 虛擬機器-審查](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>後續步驟

* [檢視 CloudSimple 虛擬機器的清單](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [從 Azure 管理 CloudSimple 虛擬機器](azure-manage-vm.md)
