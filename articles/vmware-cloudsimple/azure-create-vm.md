---
title: Azure VMware Solution by CloudSimple-使用 VM 範本在 Azure 中建立虛擬機器
description: 說明如何使用 CloudSimple 私用雲端的 VMware 基礎結構上的 VM 範本，在 Azure 中建立虛擬機器
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b01afe60a78a746eb0dc5f03cc7b45989f8cf81e
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898754"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>使用 VMware 基礎結構上的 VM 範本，在 Azure 中建立虛擬機器

您可以使用 CloudSimple 系統管理員已為您的訂用帳戶啟用的 VMware 基礎結構上的 VM 範本，在 Azure 入口網站中建立虛擬機器。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-cloudsimple-virtual-machine"></a>建立 CloudSimple 虛擬機器

1. 選取 [所有服務]  。

2. 搜尋 **CloudSimple 虛擬機器**。

3. 按一下 [新增] 。

    ![建立 CloudSimple 虛擬機器](media/create-cloudsimple-virtual-machine.png)

4. 輸入基本資訊，然後按 **[下一步：大小]**。

    > [!NOTE]
    > 在 Azure 上建立 CloudSimple 虛擬機器需要 VM 範本。  此 VM 範本應該存在於您的私人雲端 vCenter 上。  從具有所需作業系統和設定的 vCenter UI，在您的私人雲端上建立虛擬機器。  使用在 [VSphere Web 用戶端中將虛擬機器複製到範本](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)的指示，建立範本。

    ![建立 CloudSimple 虛擬機器-基本](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 欄位 | 描述 |
    | ------------ | ------------- |
    | 訂用帳戶 | 與您私人雲端相關聯的 Azure 訂用帳戶。  |
    | 資源群組 | 將指派 VM 的資源群組。 您可以選取現有的群組或建立新的群組。 |
    | 名稱 | 用來識別 VM 的名稱。  |
    | 位置 | 託管此 VM 的 Azure 區域。  |
    | 私人雲端 | 您要在其中建立虛擬機器的 CloudSimple 私人雲端。 |
    | 資源集區 | VM 的對應資源集區。 請從可用的資源集區中選取。 |
    | vSphere 範本 | VM 的 vSphere 範本。  |
    | 使用者名稱 | Windows 範本的 VM 系統管理員 (的使用者名稱) |
    | 密碼 <br>確認密碼 | 適用于 Windows 範本的 VM 系統管理員 (的密碼) 。  |

5. 選取 VM 的核心數目和記憶體容量，然後按 **[下一步：設定]**。 如果您想要將完整的 CPU 虛擬化公開給客體作業系統，讓需要硬體虛擬化的應用程式可以在沒有二進位轉譯或 paravirtualization 的虛擬機器上執行，請選取此核取方塊。 如需詳細資訊，請參閱 VMware 文章[公開 VMware 硬體輔助虛擬化](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)。

    ![建立 CloudSimple 虛擬機器-大小](media/create-cloudsimple-virtual-machine-size.png)

6. 設定網路介面和磁片（如下表所述），然後按一下 [ **審核 + 建立**]。

    ![建立 CloudSimple 虛擬機器-設定](media/create-cloudsimple-virtual-machine-configurations.png)

    若為網路介面，請按一下 [ **新增網路介面** ] 並設定下列設定。

    | 控制 | 描述 |
    | ------------ | ------------- |
    | 名稱 | 輸入識別介面的名稱。  |
    | 網路 | 從私用雲端 vSphere 中已設定的分散式埠群組清單中選取。  |
    | 配接器 | 從為 VM 設定的可用類型清單中，選取 vSphere 的介面卡。 如需詳細資訊，請參閱 VMware 知識庫文章 [選擇虛擬機器的網路介面卡](https://kb.vmware.com/s/article/1001805)。 |
    | 在開機時開啟電源 | 選擇是否要在 VM 開機時啟用 NIC 硬體。 預設值為 [啟用]。 |

    若為磁片，請按一下 [ **新增磁片** ] 並設定下列設定。

    | Item | 描述 |
    | ------------ | ------------- |
    | 名稱 | 輸入識別磁碟的名稱。  |
    | 大小 | 選取其中一個可用的大小。  |
    | SCSI 控制器 | 選取磁碟的 SCSI 控制器。  |
    | [模式] | 決定磁片參與快照集的方式。 請選擇其中一個選項： <br> -獨立持續性：寫入磁片的所有資料都會永久寫入。<br> -獨立的非持續性：當您關閉或重設虛擬機器時，寫入磁片的變更會被捨棄。  獨立而非持續的模式可讓您一律以相同的狀態重新啟動 VM。 如需詳細資訊，請參閱 [VMware 文件](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)。

7. 驗證完成後，請檢查設定，然後按一下 [ **建立**]。 若要進行任何變更，請按一下頂端的索引標籤，或按一下 []。

    ![建立 CloudSimple 虛擬機器-評論](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>檢視 CloudSimple 虛擬機器的清單

1. 選取 [所有服務]  。

2. 搜尋 **CloudSimple 虛擬機器**。

3. 選取您私人雲端建立所在的。

    ![CloudSimple 虛擬機器的清單](media/list-cloudsimple-virtual-machines.png)

CloudSimple 虛擬機器的清單包含從 Azure 入口網站建立的虛擬機器。  在對應的 vCenter 資源集區中的私人雲端 vCenter 上建立的虛擬機器，將會顯示在清單中。  
