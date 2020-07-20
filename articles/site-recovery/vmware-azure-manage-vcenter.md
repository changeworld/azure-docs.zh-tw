---
title: 在 Azure Site Recovery 中管理 VMware vCenter server
description: 本文說明如何使用 Azure Site Recovery，為 VMware Vm 的嚴重損壞修復新增和管理 VMware vCenter。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84692514"
---
# <a name="manage-vmware-vcenter-server"></a>管理 VMware vCenter Server

本文摘要說明[Azure Site Recovery](site-recovery-overview.md)中 VMware vCenter Server 的管理動作。

## <a name="verify-prerequisites-for-vcenter-server"></a>驗證 vCenter Server 的必要條件

在 VMware Vm 至 Azure 的嚴重損壞修復期間，vCenter 伺服器和 Vm 的必要條件會列在[支援矩陣](vmware-physical-azure-support-matrix.md#replicated-machines)中。

## <a name="set-up-an-account-for-automatic-discovery"></a>設定帳戶以進行自動探索

當您設定內部部署 VMware Vm 的嚴重損壞修復時，Site Recovery 需要 vCenter Server/vSphere 主機的存取權。 Site Recovery 進程伺服器接著可以自動探索 Vm，並視需要將它們故障。 根據預設，進程伺服器會在 Site Recovery 設定伺服器上執行。 新增設定伺服器的帳戶，以連線至 vCenter Server/vSphere 主機，如下所示：

1. 登入組態伺服器。
1. 使用桌面快捷方式開啟設定伺服器工具（_cspsconfigtool.exe_）。
1. 在 [**管理帳戶**] 索引標籤上，按一下 [**新增帳戶**]。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 提供帳戶詳細資料，並按一下 [確定]**** 加以新增。 帳戶應該具有帳戶許可權資料表中摘要說明的許可權。

   > [!NOTE]
   > 同步處理帳戶資訊與 Site Recovery 需要大約15分鐘的時間。

### <a name="account-permissions"></a>帳戶權限

|**Task** | **帳戶** | **權限** | **詳細資料**|
|--- | --- | --- | ---|
|**VM 探索/遷移（不含容錯回復）** | 至少是唯讀使用者帳戶。 | 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件]**** 物件的 [沒有存取權]**** 角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。|
|**複寫/容錯移轉** | 至少是唯讀使用者帳戶。 | 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 若要限制存取權，請將具有 [**傳播至子**物件] 許可權的 [**沒有存取權**] 角色指派給子物件（vSphere 主機、資料存放區、虛擬機器和網路）。<br/><br/> 適用於移轉用途，而不是完整複寫、容錯移轉、容錯回復。|
|**複寫/容錯移轉/容錯回復** | 我們建議您建立具有必要許可權的角色（AzureSiteRecoveryRole），然後將角色指派給 VMware 使用者或群組。 | 資料中心物件 –> 傳播至子物件、role=AzureSiteRecoveryRole<br/><br/> 資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案<br/><br/> 網路 -> 網路指派<br/><br/> 資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM<br/><br/> 工作 -> 建立工作、更新工作<br/><br/> 虛擬機器 -> 組態<br/><br/> 虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝<br/><br/> 虛擬機器 -> 清查 -> 建立、註冊、取消註冊<br/><br/> 虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/> 虛擬機器 -> 快照 -> 移除快照 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件]**** 物件的 [沒有存取權]**** 角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。|

## <a name="add-vmware-server-to-the-vault"></a>將 VMware 伺服器新增至保存庫

當您設定內部部署 VMware Vm 的嚴重損壞修復時，會將您在其中探索 Vm 的 vCenter Server/vSphere 主機新增至 Site Recovery 保存庫，如下所示：

1. 在 [保存庫] > **Site Recovery 基礎結構**設定伺服器] 中  >  ** **，開啟 [設定伺服器]。
1. 在 [**詳細資料**] 頁面中，按一下 [ **vCenter**]。
1. 在 [**新增 vCenter**] 中，指定 vSphere 主機或 vCenter 伺服器的易記名稱。
1. 指定伺服器的 IP 位址或 FQDN。
1. 保留連接埠為 443，除非您的 VMware 伺服器設定為在不同連接埠上接聽要求。
1. 選取用來連接到 VMware vCenter 或 vSphere ESXi 伺服器的帳戶。 然後按一下 [確定] 。

## <a name="modify-credentials"></a>修改認證

如有必要，您可以修改用來連接到 vCenter Server/vSphere 主機的認證，如下所示：

1. 登入組態伺服器。
1. 使用桌面快捷方式開啟設定伺服器工具（_cspsconfigtool.exe_）。
1. 按一下 [管理帳戶]**** 索引標籤上的 [新增帳戶]****。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 提供新的帳戶詳細資料，然後按一下 **[確定]**。 帳戶需要 [[帳戶許可權](#account-permissions)] 資料表中所列的許可權。
1. 在保存庫 > **Site Recovery 基礎結構**設定伺服器] 中  >  ** **，開啟設定伺服器。
1. 在 [**詳細資料**] 中，按一下 [重新整理**伺服器**]。
1. 在 [重新整理伺服器] 作業完成後，選取 [vCenter Server]。
1. 在 [**摘要**] 中，選取 [ **VCenter server/vSphere 主機帳戶**] 中新增的帳戶，然後按一下 [**儲存**]。

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>刪除 vCenter Server

1. 在保存庫 > **Site Recovery 基礎結構**設定伺服器] 中  >  ** **，開啟設定伺服器。
1. 在 [詳細資料]**** 頁面中，選取 vCenter Server。
1. 按一下 [**刪除**] 按鈕。

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>修改 IP 位址和埠

您可以修改 vCenter Server 的 IP 位址，或是伺服器與 Site Recovery 之間通訊所使用的埠。 根據預設，Site Recovery 會透過埠443存取 vCenter Server/vSphere 主機資訊。

1. 在 [保存庫 > **Site Recovery 基礎結構**設定  >  **伺服器**] 中，按一下要新增 vCenter Server 的 [設定] 伺服器。
1. 在 [ **vCenter server**] 中，按一下您想要修改的 vCenter Server。
1. 在 [**摘要**] 中，更新 IP 位址和埠，並儲存變更。

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. 若要讓變更生效，請等待15分鐘，或重新整理設定[伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。

## <a name="migrate-all-vms-to-a-new-server"></a>將所有 Vm 遷移至新的伺服器

如果您想要將所有 Vm 遷移為使用新的 vCenter Server，您只需要更新指派給 vCenter Server 的 IP 位址。 請勿新增另一個 VMware 帳戶，因為這可能會導致重複的專案。 更新位址，如下所示：

1. 在 [保存庫 > **Site Recovery 基礎結構**設定  >  **伺服器**] 中，按一下要新增 vCenter Server 的 [設定] 伺服器。
1. 在 [ **vCenter server** ] 區段中，按一下您想要從中遷移的 vCenter Server。
1. 在 [**摘要**] 中，更新新 VCENTER SERVER 的 IP 位址，然後儲存變更。
1. 一旦更新 IP 位址，Site Recovery 就會開始從新的 vCenter Server 接收 VM 探索資訊。 這不會影響進行中的複寫活動。

## <a name="migrate-a-few-vms-to-a-new-server"></a>將幾個 Vm 遷移至新的伺服器

如果您只想要將幾個複寫 Vm 遷移到新的 vCenter Server，請執行下列動作：

1. [將](#add-vmware-server-to-the-vault)新的 vCenter Server 新增至設定伺服器。
1. 針對將移至新伺服器的 Vm[停](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)用複寫。
1. 在 VMware 中，將 Vm 遷移至新的 vCenter Server。
1. 再次[啟用](vmware-azure-tutorial.md#enable-replication)已遷移 vm 的複寫，然後選取新的 vCenter Server。

## <a name="migrate-most-vms-to-a-new-server"></a>將大部分的 Vm 遷移至新的伺服器

如果您想要遷移至新 vCenter Server 的 Vm 數目高於將保留在原始 vCenter Server 上的 Vm 數目，請執行下列動作：

1. 將 [設定伺服器設定] 中指派給 vCenter Server 的[IP 位址更新](#modify-the-ip-address-and-port)為新 vCenter Server 的位址。
1. 針對保留在舊伺服器上的幾部 Vm[停](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)用複寫。
1. [將舊的 vCenter Server](#add-vmware-server-to-the-vault)及其 IP 位址新增到設定伺服器。
1. 針對保留在舊伺服器上的 Vm[重新啟用複寫](vmware-azure-tutorial.md#enable-replication)。

## <a name="next-steps"></a>後續步驟

如果您有任何問題，請參閱針對[vCenter Server 探索失敗進行疑難排解](vmware-azure-troubleshoot-vcenter-discovery-failures.md)。
