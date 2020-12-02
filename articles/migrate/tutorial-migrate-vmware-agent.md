---
title: 使用代理程式型 Azure Migrate 伺服器移轉來遷移 VMware VM
description: 了解如何使用 Azure Migrate 執行 VMware VM 的代理程式型移轉。
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 46f23953e6572b752f8773bc9db86be946ccf212
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492992"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>將 VMware VM 遷移至 Azure (代理程式型)

本文將說明如何使用 [Azure Migrate 伺服器移轉](migrate-services-overview.md#azure-migrate-server-migration-tool)工具搭配代理程式型移轉，將內部部署 VMware VM 遷移至 Azure。  您也可使用代理程式型移轉來遷移 VMware VM。 [比較](server-migrate-overview.md#compare-migration-methods)方法。


 在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 準備 Azure 以使用 Azure Migrate。
> * 準備代理程式型移轉。 設定 VMware 帳戶，讓 Azure Migrate 可以探索可供移轉的機器。 設定帳戶，讓行動服務代理程式可安裝在您想要遷移的機器上，並準備機器作為複寫設備。
> * 新增 Azure Migrate：伺服器移轉工具
> * 設定複寫設備。
> * 複寫 VM。
> * 執行測試移轉，確定一切都沒問題。
> * 執行對 Azure 的完整移轉。

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前，請 [檢閱](./agent-based-migration-architecture.md) VMware 代理程式型移轉架構。

## <a name="prepare-azure"></a>準備 Azure

完成資料表中的工作，以準備 Azure 進行代理程式型移轉。

**Task** | **詳細資料**
--- | ---
**建立 Azure Migrate 專案** | 您的 Azure 帳戶需要參與者或擁有者權限，才能建立專案。
**確認 Azure 帳戶權限** | 您的 Azure 帳戶必須有建立 VM 以及寫入至 Azure 受控磁碟的權限。
**設定 Azure 網路** | 設定 Azure VM 將在移轉後加入的網路。

### <a name="assign-permissions-to-create-project"></a>指派建立專案的權限
如果您沒有 Azure Migrate 專案，請確認建立專案的權限。


1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]。
2. 在 [檢查存取權] 中，尋找相關的帳戶，然後按一下以查看權限。
3. 確認您具有「參與者」或「擁有者」權限。

    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作以指派角色。
    
### <a name="assign-azure-account-permissions"></a>指派 Azure 帳戶權限

將虛擬機器參與者角色指派給帳戶，讓您有權執行下列作業：

- 在所選的資源群組中建立 VM。
- 在所選的虛擬網路中建立 VM。
- 寫入至 Azure 受控磁碟。 


### <a name="set-up-an-azure-network"></a>設定 Azure 網路

[設定 Azure 網路](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 內部部署機器會複寫至 Azure 受控磁碟。 當您容錯移轉至 Azure 以進行移轉時，Azure VM 會從這些受控磁碟中建立，並加入您設定的 Azure 網路。

## <a name="prepare-for-migration"></a>為移轉做準備

確認支援需求和權限，並準備部署複寫設備。 

### <a name="prepare-an-account-to-discover-vms"></a>準備帳戶以探索 VM

Azure Migrate 伺服器移轉需要 VMware 伺服器的存取權，才能探索您要遷移的 VM。 遵循下列方式建立此帳戶：

1. 若要使用專用帳戶，請在 vCenter 層級建立一個角色。 指定角色的名稱，例如 **Azure_Migrate**。
2. 將下表摘要說明的權限指派給角色。
3. 在 vCenter 伺服器或 vSphere 主機上建立使用者。 將角色指派給使用者。

#### <a name="vmware-account-permissions"></a>VMware 帳戶權限

**Task** | **角色/權限** | **詳細資料**
--- | --- | ---
**VM 探索** | 至少是唯讀使用者<br/><br/> 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 與網路)。
**複寫** |  建立具有必要權限的角色 (Azure_Site_Recovery)，然後將角色指派給 VMware 使用者或群組<br/><br/> 資料中心物件 –> 傳播至子物件、role=Azure_Site_Recovery<br/><br/> 資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案<br/><br/> 網路 -> 網路指派<br/><br/> 資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM<br/><br/> 工作 -> 建立工作、更新工作<br/><br/> 虛擬機器 -> 組態<br/><br/> 虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝<br/><br/> 虛擬機器 -> 清查 -> 建立、註冊、取消註冊<br/><br/> 虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/> 虛擬機器 -> 快照 -> 移除快照 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 與網路)。

### <a name="prepare-an-account-for-mobility-service-installation"></a>準備一個用來安裝行動服務的帳戶

行動服務必須安裝在您要複寫的電腦上。

- Azure Migrate 複寫設備可以在您為電腦啟用複寫時，進行此服務的推送安裝，您也可以手動進行安裝，或使用安裝工具。
- 在本教學課程中，我們即將使用推送安裝來安裝行動服務。
- 針對推送安裝，您必須準備一個可讓 Azure Migrate 伺服器移轉用來存取 VM 的帳戶。 如果您未手動安裝行動服務，則此帳戶只能用於推入安裝。

遵循下列方式準備此帳戶：

1. 準備有權限可以在 VM 上安裝的網域或本機帳戶。
2. 若是 Windows VM，如果您不使用網域帳戶，請藉由在 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 底下新增 DWORD 項目 **LocalAccountTokenFilterPolicy**，並在登錄中設定一個值，來停用本機電腦上的遠端使用者存取控制
3. 針對 Linux VM，請在來源 Linux 伺服器上準備根帳戶。


### <a name="prepare-a-machine-for-the-replication-appliance"></a>準備複寫設備的機器

此設備用於將機器複寫至 Azure。 此設備是高可用性的單一內部部署 VMware VM，並且裝載下列元件：

- **設定伺服器**：組態伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- **處理序伺服器**：處理序伺服器可作為複寫閘道。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將其傳送至 Azure 中的快取儲存體帳戶。 處理序伺服器也會在您要複寫的 VM 上安裝行動服務代理程式，並且在內部部署 VMware VM 上執行自動探索。

準備設備，如下所示：

- [檢閱設備需求](migrate-replication-appliance.md#appliance-requirements)。 一般來說，您會使用已下載的 OVA 檔案將複寫設備設定為 VMware VM。 此範本會建立符合所有需求的設備。
- MySQL 必須安裝在此設備上。 [檢閱](migrate-replication-appliance.md#mysql-installation)安裝方法。
- 檢閱設備機器需要存取的[公用雲端 URL](migrate-replication-appliance.md#url-access) 和 [Azure Government URL](migrate-replication-appliance.md#azure-government-url-access)。
- 檢閱複寫設備機器需要存取的[連接埠](migrate-replication-appliance.md#port-access)。



### <a name="check-vmware-requirements"></a>檢查 VMware 需求

確定 VMware 伺服器和 VM 符合移轉至 Azure 的需求。 

1. [檢查](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) VMware 伺服器需求。
2. [確認](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based)移轉的 VM 需求。
3. 確認 Azure 設定。 您複寫到 Azure 的內部部署 VM 必須符合 [Azure VM 需求](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)。
4. 在您將 VM 遷移至 Azure 之前，VM 需要進行一些變更。
    - 請務必先進行這些變更再開始移轉。 如果您先遷移 VM 再進行變更，VM 可能無法在 Azure 中啟動。
    - 請參閱您所需進行的 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 變更。

> [!NOTE]
> 使用 Azure Migrate 伺服器移轉的代理程式型移轉，會以 Azure Site Recovery 服務的功能作為基礎。 某些需求可能會連結至 Site Recovery 文件。

## <a name="set-up-the-replication-appliance"></a>設定複寫設備

此程序說明如何使用已下載的開放式虛擬化應用程式 (OVA) 範本來設定設備。 如果您無法使用此方法，則可[使用指令碼](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)來設定設備。 

### <a name="download-the-replication-appliance-template"></a>下載複寫設備範本

以下列方式下載範本：

1. 在 Azure Migrate 專案中，按一下 [移轉目標] 底下的 [伺服器]。
2. 在 Azure Migrate - 伺服器 >  **Azure Migrate：伺服器移轉** 中，按一下 探索。

    ![探索 VM](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. 在 [探索機器] > [機器是否已虛擬化?] 中，按一下 [是，使用 VMware vSphere Hypervisor]。
4. 在 [您要如何遷移？] 中，選取 [使用代理程式型複寫]。
5. 在 [目標區域] 中，選取您要將機器遷移到的 Azure 區域。
6. 選取 [確認移轉的目標區域為 region-name]。
7. 按一下 [建立資源]。 這會在背景中建立 Azure Site Recovery 保存庫。 按一下此按鈕後，即無法變更此專案的目標區域，而所有後續移轉都會送至此區域。

    ![建立復原服務保存庫](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. 在 [是否要安裝新的複寫設備?] 中，選取 [安裝複寫設備]。
9. 按一下 [下載] 。 這會下載 OVF 範本。
    ![下載 OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. 記下資源群組的名稱和復原服務保存庫。 您在部署設備時需要用到這些。


### <a name="import-the-template-in-vmware"></a>在 VMware 中匯入範本

下載 OVF 範本之後，您可以將其匯入 VMware，以便在執行 Windows Server 2016 的 VMware VM 上建立複寫應用程式。

1. 透過 VMware vSphere 用戶端，登入 VMware vCenter 伺服器或 vSphere ESXi 主機。
2. 在 [檔案]  功能表上，選取 [部署 OVF 範本]  以啟動 [部署 OVF 範本精靈]  。 
3. 在 [選取來源] 中，輸入所下載 OVF 的位置。
4. 在 [檢閱詳細資料] 中，選取 [下一步]。
5. 在 [選取名稱和資料夾] 和 [選取設定] 中，接受預設設定。
6. 在 [選取儲存體] > [選取虛擬磁碟格式] 中，選取 [Thick Provision Eager Zeroed]，以達到最佳效能。
7. 在精靈的其餘頁面上，接受所有的預設設定。
8. 在 [準備完成] 上，若要使用預設設定來設定 VM，請選取 [在部署後開啟電源] > [完成]。

   > [!TIP]
   > 如果您想要新增額外的 NIC，請清除 [在部署後開啟電源]   > [完成]  。 根據預設，此範本包含單一 NIC。 您可以在部署後新增其他 NIC。

### <a name="start-appliance-setup"></a>開始設備設定

1. 在 VMware vSphere 用戶端主控台中開啟 VM。 VM 會開機進入 Windows Server 2016 安裝體驗。
2. 接受授權合約，並輸入系統管理員密碼。
3. 在安裝完成之後，以系統管理員身分使用系統管理員密碼登入 VM。 您第一次登入時，複寫設備設定工具 (Azure Site Recovery 設定工具) 會在數秒內啟動。
5. 輸入要用來向伺服器移轉註冊設備的名稱。 然後按一下 [下一步]。
6. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，選取 [登入]  以登入您的 Azure 訂用帳戶。
7. 等候工具完成註冊 Azure AD 應用程式，以識別設備。 設備會重新開機。
1. 再次登入機器。 組態伺服器管理精靈會在數秒內自動啟動。

### <a name="register-the-replication-appliance"></a>註冊複寫設備

完成複寫設備的設定和註冊。

1. 在 [設備設定] 中，選取 [設定連線]。
2. 選取複寫設備用來進行 VM 探索的 NIC (預設只有一個 NIC)，並在來源機器上執行行動服務的推入安裝。
3. 選取複寫設備用來與 Azure 連線的 NIC。 然後選取 [儲存]。 一旦進行此設定之後便無法變更。
4. 如果設備位於 Proxy 伺服器後方，您必須指定 Proxy 設定。
    - 將 Proxy 名稱指定為 **http://ip-address** 或 **http://FQDN** 。 不支援 HTTPS Proxy 伺服器。
5. 當系統提示您輸入訂用帳戶、資源群組和保存庫詳細資料時，請新增您下載設備範本時所記下的詳細資料。
6. 在 [安裝第三方軟體]  中，接受授權合約。 選取 [下載並安裝]  以安裝 MySQL Server。
7. 選取 [安裝 VMware PowerCLI]。 在您執行這項作業之前，確定所有瀏覽器視窗都已關閉。 然後選取 [繼續]。
8. 在 [驗證設備設定]  中，必要條件會在您繼續之前進行驗證。
9. 在 [設定 vCenter 伺服器/vSphere ESXi 伺服器] 中，輸入 vCenter 伺服器或 vSphere 主機 (您要複寫的 VM 位於其上) 的 FQDN 或 IP 位址。 輸入伺服器所接聽的連接埠。 輸入要用於保存庫中 VMware 伺服器的易記名稱。
10. 輸入您為 VMware 探索[建立](#prepare-an-account-to-discover-vms)的帳戶認證。 選取 [新增] > [繼續]。
11. 在[設定虛擬機器認證] 中，輸入您啟用 VM 的複寫時，為行動服務推入安裝[建立](#prepare-an-account-for-mobility-service-installation)的認證。  
    - 若為 Windows 電腦，此帳戶需具備您要複寫之機器的本機系統管理員權限。
    - 若為 Linux，請提供根帳戶的詳細資料。
12. 選取 [完成設定]  以完成註冊。


註冊複寫設備之後，Azure Migrate 伺服器評量會使用指定的設定連線到 VMware 伺服器，並探索 VM。 您可以在 [其他] 索引標籤的 [管理] > [探索到的項目] 中，檢視探索到的項目。



## <a name="replicate-vms"></a>複寫 VM

選取要進行移轉的 VM。

> [!NOTE]
> 在入口網站中，您最多可以選取 10 部機器進行複寫。 如果您需要複寫更多機器，請將其整批 (一批 10 個) 分組。

1. 在 [Azure Migrate 專案] > [伺服器]、 **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫]。

    ![Azure Migrate 中 [伺服器] 畫面的螢幕擷取畫面。 [複寫] 按鈕會在移轉工具下的 [Azure Migrate：伺服器移轉] 中加以選取。](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. 在 [複寫] > [來源設定] > [您的電腦虛擬化了嗎] 中，選取 [是，使用 VMware vSphere]。
3. 在 [內部部署設備] 中，選取您設定的 Azure Migrate 設備的名稱。
4. 在 **vCenter Server** 中指定管理 VM 的 vCenter Server 名稱，或裝載 VM 的 vSphere Server 名稱。
5. 在 [處理序伺服器] 中，選取複寫設備的名稱。
6. 在 [來賓認證] 中，指定行動服務推送安裝將使用的 VM 管理員帳戶。 然後按 [下一步：**虛擬機器]** 。

    ![[複寫] 畫面中 [來源設定] 索引標籤的螢幕擷取畫面。 [來賓認證] 欄位會反白顯示，且值會設定為 VM-admin-account。](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. 在 [虛擬機器] 中，選取您要複寫的機器。

    - 如果您已執行 VM 的評估，您可以套用評估結果中的 VM 大小調整和磁碟類型 (進階/標準) 建議。 若要這麼做，請在 [從 Azure Migrate 評估匯入移轉設定?] 中，選取 [是] 選項。
    - 如果您未執行評估，或不想使用評估設定，請選取 [否] 選項。
    - 如果您選擇使用評估，請選取 VM 群組和評估名稱。
8. 在 **可用性選項** 中，選取：
    -  可用性區域，將已遷移的機器釘選到該區域中特定的可用性區域。 使用此選項可將形成多節點應用程式層的伺服器散發到可用性區域。 如果選取此選項，則必須在計算索引標籤中指定要用於每部所選電腦的可用性區域。只有選取要移轉的目的地區域支援可用性區域時，才可以使用此選項
    -  可用性設定組，可將遷移的電腦放在可用性設定組中。 選取的目標資源群組必須有一或多個可用性設定組，才能使用此選項。
    - 如果您不需要為已遷移的電腦提供任何一種可用性設定，則不需要任何基礎結構備援選項。
9. 檢查您要遷移的每個 VM。 然後按 [下一步：目標設定]。
10. 在 [目標設定] 中，選取訂用帳戶、您的遷移目標區域，並指定 Azure VM 在移轉後所在的資源群組。
11. 在 [虛擬網路] 中，選取 Azure VM 在移轉後所將加入的 Azure VNet/子網路。
12. 在 **可用性選項** 中，選取：
    -  可用性區域，將已遷移的機器釘選到該區域中特定的可用性區域。 使用此選項可將形成多節點應用程式層的伺服器散發到可用性區域。 如果選取此選項，則必須在計算索引標籤中指定要用於每部所選電腦的可用性區域。只有選取要移轉的目的地區域支援可用性區域時，才可以使用此選項
    -  可用性設定組，可將遷移的電腦放在可用性設定組中。 選取的目標資源群組必須有一或多個可用性設定組，才能使用此選項。
    - 如果您不需要為已遷移的電腦提供任何一種可用性設定，則不需要任何基礎結構備援選項。
    
13. 在 [Azure Hybrid Benefit] 中：

    - 如果您不想套用 Azure Hybrid Benefit，請選取 [否]。 然後按一下 [下一步]  。
    - 如果您有 Windows Server 機器涵蓋於有效的軟體保證或 Windows Server 訂用帳戶下，且您想要將權益套用至要移轉的機器，請選取 [是]。 然後按一下 [下一步]  。

14. 請檢閱 **計算** 中的 VM 名稱、大小、OS 磁碟類型和可用性設定 (如果有在上一個步驟中選取)。 VM 必須符合 [Azure 需求](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)。

   - **VM 大小**：如果您使用評估建議，[VM 大小] 下拉式清單會顯示建議的大小。 否則，Azure Migrate 會根據 Azure 訂用帳戶中最接近的相符項來選擇大小。 或者，您可以在 [Azure VM 大小] 中手動選擇大小。 
    - **OS 磁碟**：指定 VM 的 OS (開機) 磁碟。 OS 磁碟是具有作業系統開機載入器和安裝程式的磁碟。 
    - **可用性區域**：指定要使用的可用性區域。
    - **可用性設定組**：指定要使用的可用性設定組。

15. 在 [磁碟] 中，指定是否應將 VM 磁碟複寫至 Azure，並選取 Azure 中的磁碟類型 (標準 SSD/HDD 或進階受控磁碟)。 然後按一下 [下一步]  。
    - 您可以從複寫排除磁碟。
    - 如果您排除磁碟，則在移轉後磁碟將不會出現在 Azure VM 上。 

16. 在 [檢閱並啟動複寫] 中檢閱設定，然後按一下 [複寫] 以啟動伺服器的初始複寫。

> [!NOTE]
> 您可以在複寫開始之前隨時更新複寫設定 (經由 [管理] > [複寫機器])。 在複寫啟動後，就無法變更設定。


## <a name="track-and-monitor"></a>追蹤和監視

1. 在入口網站通知中追蹤作業狀態。 

    ![追蹤作業](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. 若要監視複寫狀態，請按一下 [複寫伺服器] (位於 [Azure Migrate：伺服器移轉] 中)。

    ![監視複寫](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

複寫會如下所示進行︰
- 當「啟動複寫」作業順利完成後，機器就會開始進行對 Azure 的初始複寫。
- 初始複寫完成後，就會開始進行差異複寫。 對內部部署磁碟的累加變更會定期複寫至 Azure 中的複本磁碟。


## <a name="run-a-test-migration"></a>執行測試移轉


在差異複寫開始後，您可以在執行對 Azure 的完整移轉之前，為 VM 執行測試移轉。 強烈建議您對每個機器都至少執行一次此測試，然後再遷移機器。

- 執行測試移轉會檢查移轉是否將如預期運作，且不會影響到內部部署機器；機器仍可運作，並繼續進行複寫。 
- 測試移轉會使用複寫的資料建立 Azure VM，來模擬移轉 (通常會移轉至 Azure 訂用帳戶中的非生產 VNet)。
- 您可以使用複寫的測試 Azure VM 來驗證移轉、執行應用程式測試以及解決任何問題，然後再進行完整移轉。

依照下列方式執行測試移轉：


1. 在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器移轉]** 中，按一下 [測試遷移的伺服器]。

     ![測試遷移的伺服器](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. 以滑鼠右鍵按一下要測試的 VM，然後按一下 [測試遷移]。

    ![測試移轉](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. 在 [測試移轉] 中，選取 Azure VM 在移轉後將位於其中的 Azure VNet。 建議您使用非生產 VNet。
4. **測試移轉** 作業隨即啟動。 請在入口網站通知中監視作業。
5. 移轉完成之後，請在 Azure 入口網站的 [虛擬機器] 中檢視已遷移的 Azure VM。 機器名稱會具有尾碼 **-Test**。
6. 測試完成之後，以滑鼠右鍵按一下 [複寫機器] 中的 Azure VM，然後按一下 [清除測試移轉]。

    ![清除移轉](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>移轉 VM

確認測試移轉如預期運作之後，您就可以遷移內部部署機器。

1. 在 [Azure Migrate 專案] > [伺服器] >  **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫伺服器]。

    ![複寫伺服器](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. 在 [複寫機器] 中，以滑鼠右鍵按一下 VM > [遷移]。
3. 在 [遷移] > [將虛擬機器關機，在沒有資料遺失的情況下執行計劃性移轉] 中，選取 [是] > [確定]。
    - 根據預設，Azure Migrate 會關閉內部部署 VM，以確保遺失最少資料。 
    - 如果您不想關閉 VM，請選取 [否]
4. VM 會啟動移轉作業。 請在 Azure 通知中追蹤該作業。
5. 作業完成後，您可以從 [虛擬機器] 頁面檢視及管理 VM。

## <a name="complete-the-migration"></a>完成移轉

1. 完成移轉之後，以滑鼠右鍵按一下 VM > [停止移轉]。 這會執行以下動作：
    - 停止內部部署機器的複寫。
    - 從 Azure Migrate 中的 **複寫伺服器** 計數移除機器：伺服器移轉。
    - 清除 VM 的複寫狀態資訊。
2. 在已遷移的機器上安裝 Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) 或 [Linux](../virtual-machines/extensions/agent-linux.md) 代理程式。
3. 執行任何移轉後應用程式調整，例如更新資料庫連接字串和 Web 伺服器設定。
4. 在現在於 Azure 中執行的已移轉應用程式上，執行最終的應用程式和移轉接受度測試。
5. 將流量完全移轉至已遷移的 Azure VM 執行個體。
6. 從您的本機 VM 清查中移除內部部署 VM。
7. 從本機備份中移除內部部署 VM。
8. 更新任何內部文件，以顯示 Azure VM 的新位置和 IP 位址。 

## <a name="post-migration-best-practices"></a>移轉後的最佳做法

- 內部部署
    - 將應用程式流量移至在已移轉之 Azure VM 執行個體上執行的應用程式。
    - 從您的本機 VM 清查中移除內部部署 VM。
    - 從本機備份中移除內部部署 VM。
    - 更新任何內部文件，以顯示 Azure VM 的新位置和 IP 位址。
- 在移轉之後調整 Azure VM 設定：
    - [Azure VM 代理程式](../virtual-machines/extensions/agent-windows.md)可管理 VM 與「Azure 網狀架構控制器」之間的互動。 它對某些 Azure 服務 (例如 Azure 備份、Site Recovery 及 Azure 安全性) 來說是必要的。 當您使用代理程式型移轉來遷移 VMare VM 時，行動服務安裝程式會在 Windows 電腦上安裝 Azure VM 代理程式。 在 Linux VM 上，建議您在移轉後安裝代理程式。
    - 在移轉之後，手動將行動服務從 Azure VM 解除安裝。
    - 在移轉之後手動將 VMware 工具解除安裝。
- 在 Azure 中：
    - 執行任何移轉後應用程式調整，例如更新資料庫連接字串和 Web 伺服器設定。
    - 在現在於 Azure 中執行的已移轉應用程式上，執行最終的應用程式和移轉接受度測試。
- 商務持續性/災害復原
    - 使用「Azure 備份」服務來備份 Azure VM 以維護資料安全。 [深入了解](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 將 Azure VM 複寫至次要區域，讓工作負載保持執行且持續可供使用。 [深入了解](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 針對提升安全性：
    - 使用 [Azure 資訊安全中心 - Just In Time 系統管理](../security-center/security-center-just-in-time.md)來鎖定並限制輸入流量存取。
    - 使用[網路安全性群組](../virtual-network/network-security-groups-overview.md)來限制傳送至管理端點的網路流量。
    - 部署 [Azure 磁碟加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)以協助保護磁碟，以及防止資料遭到竊取和受到未經授權的存取。
    - 深入了解如何[保護 IaaS 資源](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，並瀏覽 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)。
- 針對監視及管理：
    - 可考慮部署 [Azure 成本管理](../cost-management-billing/cloudyn/overview.md)來監視資源使用情況和花費。




 ## <a name="next-steps"></a>後續步驟

調查 Azure 雲端採用架構中的[雲端移轉旅程](/azure/architecture/cloud-adoption/getting-started/migrate)。