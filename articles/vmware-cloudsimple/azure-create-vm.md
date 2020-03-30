---
title: Azure VMware 解決方案（按雲簡單 ） - 使用 VM 範本在 Azure 中創建虛擬機器
description: 介紹如何使用雲簡單私有雲的 VMware 基礎結構上的 VM 範本在 Azure 中創建虛擬機器
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244689"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>使用 VMware 基礎結構上的 VM 範本在 Azure 中創建虛擬機器

您可以使用 CloudSimple 管理員為訂閱啟用的 VMware 基礎結構上的 VM 範本在 Azure 門戶中創建虛擬機器。

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 Azure[門戶](https://portal.azure.com)。

## <a name="create-cloudsimple-virtual-machine"></a>創建雲簡單虛擬機器

1. 選擇**所有服務**。

2. 搜尋 **CloudSimple 虛擬機器**。

3. 按一下 **[新增]**。

    ![創建雲簡單虛擬機器](media/create-cloudsimple-virtual-machine.png)

4. 輸入基本資訊，按一下 **"下一步：大小**"。

    > [!NOTE]
    > 在 Azure 上創建雲簡單虛擬機器需要 VM 範本。  此 VM 範本應存在於私有雲 vCenter 上。  使用 vCenter UI 在私有雲上創建虛擬機器，並具有所需的作業系統和配置。  使用[將虛擬機器克隆到 vSphere Web 用戶端中的範本中的](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)說明，創建一個範本。

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
    | [使用者名稱] | VM 管理員的使用者名（適用于 Windows 範本）|
    | 密碼 <br>確認密碼 | VM 管理員的密碼（適用于 Windows 範本）。  |

5. 選擇 VM 的核心和記憶體容量數，然後按一下 **"下一步：配置**"。 如果要向客體作業系統公開完全 CPU 虛擬化，以便需要硬體虛擬化的應用程式可以在虛擬機器上運行，而無需二進位轉換或半虛擬化，請選擇該核取方塊。 如需詳細資訊，請參閱 VMware 文章[公開 VMware 硬體輔助虛擬化](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)。

    ![創建雲簡單虛擬機器 - 大小](media/create-cloudsimple-virtual-machine-size.png)

6. 按照下表中所述配置網路介面和磁片，然後按一下 **"審閱 + 創建**"。

    ![創建雲簡單虛擬機器 - 配置](media/create-cloudsimple-virtual-machine-configurations.png)

    對於網路介面，請按一下"**添加網路介面**"並配置以下設置。

    | 控制 | 描述 |
    | ------------ | ------------- |
    | 名稱 | 輸入識別介面的名稱。  |
    | 網路 | 從私有雲 vSphere 中配置的分散式埠組清單中選擇。  |
    | 配接器 | 從為 VM 配置的可用類型清單中選擇 vSphere 配接器。 有關詳細資訊，請參閱 VMware 知識庫文章[為虛擬機器選擇網路介面卡](https://kb.vmware.com/s/article/1001805)。 |
    | 在開機時開啟電源 | 選擇是否要在 VM 開機時啟用 NIC 硬體。 預設值為 [啟用]****。 |

    對於磁片，請按一下"**添加磁片"** 並配置以下設置。

    | Item | 描述 |
    | ------------ | ------------- |
    | 名稱 | 輸入識別磁碟的名稱。  |
    | 大小 | 選取其中一個可用的大小。  |
    | SCSI 控制器 | 選取磁碟的 SCSI 控制器。  |
    | [模式] | 確定磁片參與快照的方式。 請選擇其中一個選項： <br> - 獨立持久性：寫入磁片的所有資料都是永久寫入的。<br> - 獨立非持久性：當您關閉或重置虛擬機器時，將丟棄寫入磁片的更改。  獨立而非持續的模式可讓您一律以相同的狀態重新啟動 VM。 如需詳細資訊，請參閱 [VMware 文件](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)。

7. 驗證完成後，查看設置並按一下"**創建**"。 要進行任何更改，請按一下頂部的選項卡或按一下。

    ![創建雲簡單虛擬機器 - 查看](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>檢視 CloudSimple 虛擬機器的清單

1. 選擇**所有服務**。

2. 搜尋 **CloudSimple 虛擬機器**。

3. 選擇創建私有雲的方面。

    ![雲簡單虛擬機器清單](media/list-cloudsimple-virtual-machines.png)

雲簡單虛擬機器清單包括從 Azure 門戶創建的虛擬機器。  在映射的 vCenter 資源池中的私有雲 vCenter 上創建的虛擬機器將顯示在清單中。  
