---
title: 快速入門：在 Azure 上使用 VMware VM
titleSuffix: Azure VMware Solution by CloudSimple
description: 瞭解如何使用 Azure VMware 解決方案（按雲簡單）配置和使用 Azure 門戶的 VMware VM
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019548"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>快速入門 - 在 Azure 上使用 VMware VM

要在 Azure 門戶中創建虛擬機器，請使用 CloudSimple 管理員為訂閱啟用的虛擬機器範本。 VM 範本位於 VMware 基礎結構中。

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Azure 上的雲簡單 VM 創建需要 VM 範本

使用 vCenter UI 在私有雲上創建虛擬機器。 要創建範本，請按照[vSphere Web 用戶端中"將虛擬機器克隆到範本"中的](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)說明進行操作。 將 VM 範本存儲在私有雲 vCenter 上。

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>在 Azure 入口網站中建立虛擬機器

1. 選擇**所有服務**。

2. 搜尋 **CloudSimple 虛擬機器**。

3. 按一下 **[新增]**。

    ![創建雲簡單虛擬機器](media/create-cloudsimple-virtual-machine.png)

4. 輸入基本資訊，然後按一下 **"下一步：大小**"。

    ![創建雲簡單虛擬機器 - 基礎知識](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 欄位 | 描述 |
    | ------------ | ------------- |
    | 訂用帳戶 | 與私有雲關聯的 Azure 訂閱。  |
    | 資源群組 | 將 VM 分配給的資源組。 您可以選取現有的群組或建立新的群組。 |
    | 名稱 | 名稱以標識 VM。  |
    | Location | 託管此 VM 的 Azure 區域。  |
    | 私人雲端 | 雲簡單私有雲，您要創建虛擬機器。 |
    | 資源集區 | 映射 VM 的資源池。 請從可用的資源集區中選取。 |
    | vSphere 範本 | VM 的 vSphere 範本。  |
    | [使用者名稱] | VM 管理員的使用者名（適用于 Windows 範本）。|
    | 密碼 |  VM 管理員的密碼（適用于 Windows 範本）。 |
    | 確認密碼 | 確認密碼。 |

5. 選擇 VM 的核心和記憶體容量數，然後按一下 **"下一步：配置**"。 如果要向客體作業系統公開完整的 CPU 虛擬化，請選擇該核取方塊。 需要硬體虛擬化的應用程式可以直接在虛擬機器上執行，而不需進行二進位轉譯或半虛擬化。 如需詳細資訊，請參閱 VMware 文章<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">公開 VMware 硬體輔助虛擬化</a>。

    ![創建雲簡單虛擬機器 - 大小](media/create-cloudsimple-virtual-machine-size.png)

6. 按照下表中所述配置網路介面和磁片，然後按一下 **"審閱 + 創建**"。

    ![創建雲簡單虛擬機器 - 配置](media/create-cloudsimple-virtual-machine-configurations.png)

    對於網路介面，請按一下"**添加網路介面**"並配置以下設置。

    | 控制 | 描述 |
    | ------------ | ------------- |
    | 名稱 | 輸入識別介面的名稱。  |
    | 網路 | 從私有雲 vSphere 中配置的分散式埠組清單中選擇。  |
    | 配接器 | 從為 VM 配置的可用類型清單中選擇 vSphere 配接器。 有關詳細資訊，請參閱 VMware 知識庫文章<a href="https://kb.vmware.com/s/article/1001805" target="_blank">為虛擬機器選擇網路介面卡</a>。 |
    | 在開機時開啟電源 | 選擇是否要在 VM 開機時啟用 NIC 硬體。 預設值為 [啟用]****。 |

    對於磁片，請按一下"**添加磁片"** 並配置以下設置。

    | Item | 描述 |
    | ------------ | ------------- |
    | 名稱 | 輸入識別磁碟的名稱。  |
    | 大小 | 選取其中一個可用的大小。  |
    | SCSI 控制器 | 選取磁碟的 SCSI 控制器。  |
    | [模式] | 確定磁片參與快照的方式。 請選擇其中一個選項： <br> - 獨立持久性：寫入磁片的所有資料都是永久寫入的。<br> - 獨立非持久性：當您關閉或重置虛擬機器時，將丟棄寫入磁片的更改。  獨立而非持續的模式可讓您一律以相同的狀態重新啟動 VM。 如需詳細資訊，請參閱 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware 文件</a>。

7. 驗證完成後，查看設置並按一下"**創建**"。 要進行任何更改，請按一下頂部的選項卡或按一下。

    ![創建雲簡單虛擬機器 - 查看](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>後續步驟

* [檢視 CloudSimple 虛擬機器的清單](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [從 Azure 管理雲簡單虛擬機器](azure-manage-vm.md)
