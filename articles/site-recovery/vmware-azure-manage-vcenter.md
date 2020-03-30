---
title: 在 Azure 網站恢復中管理 VMware vCenter 伺服器
description: 本文介紹如何通過 Azure 網站恢復添加和管理 VMware vCenter，以便 VMware VM 的災害復原到 Azure。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257260"
---
# <a name="manage-vmware-vcenter-server"></a>管理 VMware vCenter 伺服器

本文總結了[Azure 網站恢復](site-recovery-overview.md)中 VMware vCenter 伺服器的管理操作。

## <a name="verify-prerequisites-for-vcenter-server"></a>驗證 vCenter 伺服器的先決條件

VMware VM 到 Azure 的災害復原期間 vCenter 伺服器和 VM 的先決條件列在[支援矩陣](vmware-physical-azure-support-matrix.md#replicated-machines)中。

## <a name="set-up-an-account-for-automatic-discovery"></a>設定帳戶以進行自動探索

為本地 VMware VM 設置災害復原時，網站恢復需要訪問 vCenter 伺服器/vSphere 主機。 然後，網站恢復過程伺服器可以自動探索 VM，並根據需要將其容錯移轉。 預設情況下，進程伺服器在網站恢復佈建服務器上運行。 為佈建服務器添加帳戶以連接到 vCenter 伺服器/vSphere 主機，如下所示：

1. 登入組態伺服器。
1. 使用桌面快捷方式打開佈建服務器工具 （_cspsconfigtool.exe）。_
1. 在 **"管理帳戶**"選項卡上，按一下"**添加帳戶**"。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 提供帳戶詳細資料，並按一下 [確定]**** 加以新增。 帳戶應具有帳戶許可權表中匯總的許可權。

   > [!NOTE]
   > 將帳戶資訊與網站恢復同步大約需要 15 分鐘。

### <a name="account-permissions"></a>帳戶權限

|**任務** | **帳戶** | **許可權** | **詳細資料**|
|--- | --- | --- | ---|
|**VM 發現/遷移（無故障恢復）** | 至少一個唯讀使用者帳戶。 | 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件]**** 物件的 [沒有存取權]**** 角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。|
|**複製/容錯移轉** | 至少一個唯讀使用者帳戶。 | 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 要限制訪問，請將 **"傳播"到子**物件（vSphere 主機、資料存儲、虛擬機器和網路）的 **"無訪問"** 角色指派給子物件。<br/><br/> 適用於移轉用途，而不是完整複寫、容錯移轉、容錯回復。|
|**複寫/容錯移轉/容錯回復** | 我們建議您創建具有所需許可權的角色 （AzureSiteRecoveryRole），然後將該角色指派給 VMware 使用者或組。 | 資料中心物件 –> 傳播至子物件、role=AzureSiteRecoveryRole<br/><br/> 資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案<br/><br/> 網路 -> 網路指派<br/><br/> 資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM<br/><br/> 工作 -> 建立工作、更新工作<br/><br/> 虛擬機器 -> 組態<br/><br/> 虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝<br/><br/> 虛擬機器 -> 清查 -> 建立、註冊、取消註冊<br/><br/> 虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/> 虛擬機器 -> 快照 -> 移除快照 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件]**** 物件的 [沒有存取權]**** 角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。|

## <a name="add-vmware-server-to-the-vault"></a>將 VMware 伺服器新增至保存庫

為本地 VMware VM 設置災害復原時，會將發現 VM 的 vCenter Server/vSphere 主機添加到網站恢復保存庫中，如下所示：

1. 在保存庫>**網站恢復基礎結構** > **佈建服務器**中，打開佈建服務器。
1. 在 **"詳細資訊**"頁中，按一下**vCenter**。
1. 在**Add vCenter**中 ，為 vSphere 主機或 vCenter 伺服器指定易記名稱。
1. 指定伺服器的 IP 位址或 FQDN。
1. 保留連接埠為 443，除非您的 VMware 伺服器設定為在不同連接埠上接聽要求。
1. 選擇用於連接到 VMware vCenter 或 vSphere ESXi 伺服器的帳戶。 然後按一下 **[確定]**。

## <a name="modify-credentials"></a>修改認證

如有必要，您可以修改用於連接到 vCenter 伺服器/vSphere 主機的憑據，如下所示：

1. 登入組態伺服器。
1. 使用桌面快捷方式打開佈建服務器工具 （_cspsconfigtool.exe）。_
1. 按一下 [管理帳戶]**** 索引標籤上的 [新增帳戶]****。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 提供新帳戶詳細資訊，然後按一下 **"確定**"。 帳戶需要[帳戶許可權](#account-permissions)表中列出的許可權。
1. 在保存庫>**網站恢復基礎結構** > **佈建服務器**中，打開佈建服務器。
1. 在 **"詳細資訊**"中，按一下 **"刷新伺服器**"。
1. 刷新伺服器作業完成後，選擇 vCenter 伺服器。
1. 在**摘要**中，在**vCenter 伺服器/vSphere 主機帳戶**中選擇新添加的帳戶，然後按一下"**保存**"。

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>刪除 vCenter 伺服器

1. 在保存庫>**網站恢復基礎結構** > **佈建服務器**中，打開佈建服務器。
1. 在 [詳細資料]**** 頁面中，選取 vCenter Server。
1. 按一下 [刪除]**** 按鈕。

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>修改 IP 位址和埠

您可以修改 vCenter 伺服器的 IP 位址，或者用於伺服器和網站恢復之間通信的埠。 預設情況下，網站恢復通過埠 443 訪問 vCenter 伺服器/vSphere 主機資訊。

1. 在**網站恢復基礎結構** > **佈建服務器**>保存庫中，按一下 vCenter 伺服器添加到的佈建服務器。
1. 在**vCenter 伺服器**中，按一下要修改的 vCenter 伺服器。
1. 在**摘要**中，更新 IP 位址和埠，並保存更改。

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. 要使更改生效，請等待 15 分鐘或[刷新佈建服務器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。

## <a name="migrate-all-vms-to-a-new-server"></a>將所有 VM 遷移到新伺服器

如果要遷移所有 VM 以使用新的 vCenter 伺服器，只需更新分配給 vCenter 伺服器的 IP 位址。 不要添加其他 VMware 帳戶，因為這可能導致重複的條目。 更新位址如下：

1. 在**網站恢復基礎結構** > **佈建服務器**>保存庫中，按一下 vCenter 伺服器添加到的佈建服務器。
1. 在**vCenter 伺服器**部分中，按一下要從其中遷移的 vCenter 伺服器。
1. 在**摘要**中，將 IP 位址更新到新 vCenter 伺服器的位址，並保存更改。
1. 更新 IP 位址後，網站恢復將開始從新的 vCenter 伺服器接收 VM 發現資訊。 這不會影響正在進行的複製活動。

## <a name="migrate-a-few-vms-to-a-new-server"></a>將一些 VM 遷移到新伺服器

如果只想將一些複製 VM 遷移到新的 vCenter 伺服器，請執行以下操作：

1. 將新的 vCenter 伺服器[添加到](#add-vmware-server-to-the-vault)佈建服務器。
1. [禁用](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)將移動到新伺服器的 VM 的複製。
1. 在 VMware 中，將 VM 遷移到新的 vCenter 伺服器。
1. 再次為遷移的 VM[啟用複製](vmware-azure-tutorial.md#enable-replication)，選擇新的 vCenter 伺服器。

## <a name="migrate-most-vms-to-a-new-server"></a>將大多數 VM 遷移到新伺服器

如果要遷移到新 vCenter 伺服器的 VM 數高於將保留在原始 vCenter 伺服器上的 VM 數，則執行以下操作：

1. 將佈建服務器設置中分配給 vCenter 伺服器的[IP 位址更新](#modify-the-ip-address-and-port)到新 vCenter 伺服器的位址。
1. [禁用](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)保留在舊伺服器上的少數 VM 的複製。
1. [將舊的 vCenter 伺服器](#add-vmware-server-to-the-vault)及其 IP 位址添加到佈建服務器。
1. [重新啟用](vmware-azure-tutorial.md#enable-replication)保留在舊伺服器上的 VM 的複製。

## <a name="next-steps"></a>後續步驟

如果有任何問題，請參閱[疑難排解 vCenter 伺服器發現失敗](vmware-azure-troubleshoot-vcenter-discovery-failures.md)。
