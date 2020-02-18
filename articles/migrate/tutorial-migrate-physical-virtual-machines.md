---
title: 使用 Azure Migrate 將機器視為實體伺服器遷移至 Azure。
description: 本文說明如何使用 Azure Migrate 將實體機器遷移至 Azure。
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: MVC
ms.openlocfilehash: 908a5915cbb7f5aeb9f641da18024d5dbf497707
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134946"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>將機器視為實體伺服器遷移至 Azure

本文說明如何使用 Azure Migrate：伺服器移轉工具，將機器視為實體伺服器遷移至 Azure。 在許多情況下，將機器視為實體伺服器進行遷移，都有其效用：

- 遷移內部部署實體伺服器。
- 遷移由 Xen、KVM 之類的平台虛擬化的 VM。
- 遷移 Hyper-V 或 VMware VM (如果您因故無法使用標準移轉程序進行 [Hyper-V](tutorial-migrate-hyper-v.md) 或 [VMware](server-migrate-overview.md) 的移轉)。
- 遷移在私人雲端中執行的 VM。
- 遷移在公用雲端中執行的 VM，例如 Amazon Web Services (AWS) 或 Google Cloud Platform (GCP)。


[Azure Migrate](migrate-services-overview.md) 提供集中式中樞，可追蹤內部部署應用程式和工作負載以及雲端 VM 執行個體的探索、評量和移轉 (以 Azure 為目標)。 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。


在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 使用 Azure Migrate 伺服器移轉工具進行 Azure 的移轉準備。
> * 對您要遷移的機器檢查需求，並備妥 Azure Migrate 複寫設備的機器，用以探索機器並將其遷移至 Azure。
> * 在 Azure Migrate 中樞新增 Azure Migrate 伺服器移轉工具。
> * 設定複寫設備。
> * 在您要遷移的機器上安裝行動服務。
> * 啟用複寫。
> * 執行測試移轉，確定一切都沒問題。
> * 執行對 Azure 的完整移轉。

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱 Azure Migrate 的操作說明。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前，您必須：

1. [檢閱](migrate-architecture.md)移轉架構。
2. 確定您的 Azure 帳戶已獲派「虛擬機器參與者」角色，讓您有權執行下列作業：

    - 在所選的資源群組中建立 VM。
    - 在所選的虛擬網路中建立 VM。
    - 寫入至 Azure 受控磁碟。 

3. [設定 Azure 網路](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 當您複寫至 Azure 時會建立 Azure VM，且此 VM 會加入您設定移轉時所指定的 Azure 網路。


## <a name="prepare-azure"></a>準備 Azure

您必須先設定 Azure 權限，才能使用「Azure Migrate 伺服器移轉」來進行遷移。

- **建立專案**：您的 Azure 帳戶必須有建立 Azure Migrate 專案的權限。 

### <a name="assign-permissions-to-create-project"></a>指派建立專案的權限

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]  。
2. 在 [檢查存取權]  中，尋找相關的帳戶，然後按一下以查看權限。
3. 您應該會具有「參與者」  或「擁有者」  權限。
    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作以指派角色。

## <a name="prepare-for-migration"></a>為移轉做準備

### <a name="check-machine-requirements-for-migration"></a>檢查移轉的機器需求

確定機器符合移轉至 Azure 的需求。 

> [!NOTE]
> 使用 Azure Migrate 伺服器移轉來進行的代理程式型移轉，具有與 Azure Site Recovery 服務的代理程式型災害復原功能相同的複寫架構，而所使用的部分元件則共用相同的程式碼基底。 某些需求可能會連結至 Site Recovery 文件。

1. [確認](migrate-support-matrix-physical-migration.md#physical-server-requirements)實體伺服器需求。
2. 確認 VM 設定。 您複寫到 Azure 的內部部署機器必須符合 [Azure VM 需求](migrate-support-matrix-physical-migration.md#azure-vm-requirements)。


### <a name="prepare-a-machine-for-the-replication-appliance"></a>準備複寫設備的機器

「Azure Migrate 伺服器移轉」會使用複寫設備將機器複寫至 Azure。 複寫設備會執行下列元件。

- **設定伺服器**：組態伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- **處理序伺服器**：處理序伺服器可作為複寫閘道。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將其傳送至 Azure 中的快取儲存體帳戶。 

開始之前，您必須準備用來裝載複寫設備的 Windows Server 2016 機器。 此機器應符合[這些需求](migrate-replication-appliance.md)。 設備不應該安裝在您想要保護的來源機器上。


## <a name="add-the-azure-migrate-server-migration-tool"></a>新增 Azure Migrate 伺服器移轉工具

設定 Azure Migrate 專案，然後新增 Azure Migrate 伺服器移轉工具。

1. 在 Azure 入口網站 > [所有服務]  中，搜尋 **Azure Migrate**。
2. 在 [服務]  下，選取 [Azure Migrate]  。
3. 在 [概觀]  中，按一下 [評估和遷移伺服器]  。
4. 在 [探索、評估和遷移伺服器]  下方，按一下 [評估和遷移伺服器]  。

    ![探索和評估伺服器](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. 在 [探索、評估和遷移伺服器]  中，按一下 [新增工具]  。
6. 在 [Migrate 專案]  中選取您的 Azure 訂用帳戶，並建立資源群組 (如果您還沒有的話)。
7. 在 [專案詳細資料]  中指定專案名稱，以及您要在其中建立專案的地理位置，然後按 [下一步] 

    ![建立 Azure Migrate 專案](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    您可以在下列任何地理位置建立 Azure Migrate 專案。

    **地理位置** | **區域**
    --- | ---
    Asia | 東南亞
    歐洲 | 北歐或西歐
    美國 | 美國東部或美國中西部

    針對專案所指定的地理位置，僅會用於儲存從內部部署虛擬機器收集的中繼資料。 您可以選取任何目標區域以進行實際移轉。
8. 在 [選取評量工具]  中，選取 [暫時跳過新增評量工具]   > [下一步]  。
9. 在 [選取移轉工具]  中，選取 **[Azure Migrate：伺服器移轉]**  > [下一步]  。
10. 在 [檢閱 + 新增工具]  中檢閱設定，然後按一下 [新增工具] 
11. 新增工具之後，工具會出現在 Azure Migrate 專案 > [伺服器]   > [移轉工具]  中。

## <a name="set-up-the-replication-appliance"></a>設定複寫設備

移轉的第一個步驟是設定複寫設備。 您可以下載設備的安裝程式檔案，並在[您備妥的機器](#prepare-a-machine-for-the-replication-appliance)上加以執行。 安裝設備後，您可以將其註冊至「Azure Migrate 伺服器移轉」。


### <a name="download-the-replication-appliance-installer"></a>下載複寫設備安裝程式

1. 在 [Azure Migrate 專案] > [伺服器]  的 **[Azure Migrate：伺服器移轉]** 中，按一下 [探索]  。

    ![探索 VM](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. 在 [探索機器]   > [機器是否已虛擬化?]  中，按一下 [未虛擬化/其他]  。
4. 在 [目標區域]  中，選取您要將機器遷移到的 Azure 區域。
5. 選取 [確認移轉的目標區域為 region-name]  。
6. 按一下 [建立資源]  。 這會在背景中建立 Azure Site Recovery 保存庫。
    - 如果您已使用「Azure Migrate 伺服器移轉」來設定移轉，則無法設定目標選項，因為先前已設定資源。
    - 按一下此按鈕後，即無法變更此專案的目標區域。
    - 所有後續的移轉都會以此區域為目標。

7. 在 [是否要安裝新的複寫設備?]  中，選取 [安裝複寫設備]  。
9. 在 [下載並安裝複寫設備軟體]  中，下載設備安裝程式和註冊金鑰。 您必須要有金鑰，才能註冊設備。 此金鑰在下載後有五天的有效期。

    ![下載提供者](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. 將設備安裝程式檔案和金鑰檔案複製到您為設備建立的 Windows Server 2016 機器。
11. 依照下一個程序中的說明，執行複寫設備安裝檔案。
12. 在安裝後重新啟動設備之後，在 [探索機器]  的 [選取組態伺服器]  中選取新設備，然後按一下 [完成註冊]  。 完成註冊作業會執行數項最終工作，以備妥複寫設備。

    ![完成註冊](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

完成註冊後可能需要一點時間，所探索到的機器才會出現在「Azure Migrate 伺服器移轉」中。 探索到 VM 時，[探索到的伺服器]  計數即會上升。

![探索到的伺服器](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>安裝行動服務

在要遷移的機器上，您必須安裝行動服務代理程式。 代理程式安裝程式可在複寫設備上取得。 您必須找出正確的安裝程式，並在要遷移的每個機器上安裝代理程式。 以下列方式來執行此動作：

1. 登入複寫設備。
2. 瀏覽至 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**。
3. 尋找機器作業系統和版本適用的安裝程式。 檢閱[支援的作業系統](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)。 
4. 將安裝程式檔案複製到要遷移的機器。
5. 確定您具有先前部署設備時所產生的複雜密碼。
    - 將檔案儲存在機器上的暫存文字檔中。
    - 您可以在複寫設備上取得此複雜密碼。 從命令列執行 **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v**，可檢視目前的複雜密碼。
    - 請勿重新產生複雜密碼。 這會使連線中斷，而您將必須重新註冊複寫設備。


### <a name="install-on-windows"></a>在 Windows 上安裝

1. 將安裝程式檔案的內容解壓縮到機器上的本機資料夾 (例如 C:\Temp)，如下所示：

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. 執行行動服務安裝程式：
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. 將代理程式註冊至複寫設備：
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>在 Linux 上安裝

1. 將安裝程式 tarball 的內容解壓縮到機器上的本機資料夾 (例如 /tmp/MobSvcInstaller)，如下所示：
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. 執行安裝程式指令碼：
    ```
    sudo ./install -r MS -q
    ```
3. 將代理程式註冊至複寫設備：
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>複寫機器

現在，選取要進行移轉的機器。 

> [!NOTE]
> 您最多可以一起複寫 10 部機器。 如果您需要複寫更多機器，請以 10 個一批的方式同時進行複寫。

1. 在 [Azure Migrate 專案] > [伺服器]  、 **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫]  。

    ![複寫 VM](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. 在 [複寫]  > [來源設定]  [您的電腦虛擬化了嗎] >   中，選取 [未虛擬化/其他]  。
3. 在 [內部部署設備]  中，選取您設定的 Azure Migrate 設備的名稱。
4. 在 [處理序伺服器]  中，選取複寫設備的名稱。
6. 在 [來賓認證]  中，您可以指定行動服務的推送安裝所將使用的 VM 管理員帳戶。 在本教學課程中，我們將手動安裝行動服務，因此您可以新增任何虛擬帳戶。 然後按 [下一步：**虛擬機器]** 。

    ![複寫 VM](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. 在 [虛擬機器]  的 [從評量匯入移轉設定?]  中，保留預設設定 [否，我將手動指定移轉設定]  。
8. 檢查您要遷移的每個 VM。 然後按 [下一步：  目標設定]。

    ![選取 VM](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. 在 [目標設定]  中，選取訂用帳戶、您的遷移目標區域，並指定 Azure VM 在移轉後所在的資源群組。
10. 在 [虛擬網路]  中，選取 Azure VM 在移轉後所將加入的 Azure VNet/子網路。
11. 在 [Azure Hybrid Benefit]  中：

    - 如果您不想套用 Azure Hybrid Benefit，請選取 [否]  。 然後按一下 [下一步]  。
    - 如果您有 Windows Server 機器涵蓋於有效的軟體保證或 Windows Server 訂用帳戶下，且您想要將權益套用至要移轉的機器，請選取 [是]  。 然後按一下 [下一步]  。

    ![目標設定](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. 在 [計算]  中，檢閱 VM 名稱、大小、OS 磁碟類型和可用性設定組。 VM 必須符合 [Azure 需求](migrate-support-matrix-physical-migration.md#azure-vm-requirements)。

    - **VM 大小**：根據預設，「Azure Migrate 伺服器移轉」會根據 Azure 訂用帳戶中最接近的相符項來選擇大小。 或者，您可以在 [Azure VM 大小]  中手動選擇大小。 
    - **OS 磁碟**：指定 VM 的 OS (開機) 磁碟。 OS 磁碟是具有作業系統開機載入器和安裝程式的磁碟。 
    - **可用性設定組**：如果 VM 在移轉後應位於 Azure 可用性設定組中，請指定設定組。 此設定組必須位於您為移轉指定的目標資源群組中。

    ![計算設定](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. 在 [磁碟]  中，指定是否應將 VM 磁碟複寫至 Azure，並選取 Azure 中的磁碟類型 (標準 SSD/HDD 或進階受控磁碟)。 然後按一下 [下一步]  。
    - 您可以從複寫排除磁碟。
    - 如果您排除磁碟，則在移轉後磁碟將不會出現在 Azure VM 上。 

    ![磁碟設定](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. 在 [檢閱並啟動複寫]  中檢閱設定，然後按一下 [複寫]  以啟動伺服器的初始複寫。

> [!NOTE]
> 您可以在複寫開始之前隨時更新複寫設定 (經由 [管理]   > [複寫機器]  )。 在複寫啟動後，就無法變更設定。



## <a name="track-and-monitor"></a>追蹤和監視

- 按一下 [複寫]  後，就會開始進行「啟動複寫」作業。 
- 當「啟動複寫」作業順利完成後，機器就會開始進行對 Azure 的初始複寫。
- 初始複寫完成後，就會開始進行差異複寫。 對內部部署磁碟的累加變更會定期複寫至 Azure 中的複本磁碟。


您可以在入口網站通知中追蹤作業狀態。

您可以監視複寫狀態，只要按一下 [複寫伺服器]  即可 (位於 **[Azure Migrate：伺服器移轉]** 中)。
![監視複寫](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>執行測試移轉


在差異複寫開始後，您可以在執行對 Azure 的完整移轉之前，為 VM 執行測試移轉。 強烈建議您對每個機器都至少執行一次此測試，然後再遷移機器。

- 執行測試移轉會檢查移轉是否將如預期運作，且不會影響到內部部署機器；機器仍可運作，並繼續進行複寫。 
- 測試移轉會使用複寫的資料建立 Azure VM，來模擬移轉 (通常會移轉至 Azure 訂用帳戶中的非生產 VNet)。
- 您可以使用複寫的測試 Azure VM 來驗證移轉、執行應用程式測試以及解決任何問題，然後再進行完整移轉。

依照下列方式執行測試移轉：


1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器移轉]** 中，按一下 [測試遷移的伺服器]  。

     ![測試遷移的伺服器](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. 以滑鼠右鍵按一下要測試的 VM，然後按一下 [測試遷移]  。

    ![測試移轉](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. 在 [測試移轉]  中，選取 Azure VM 在移轉後將位於其中的 Azure VNet。 建議您使用非生產 VNet。
4. **測試移轉**作業隨即啟動。 請在入口網站通知中監視作業。
5. 移轉完成之後，請在 Azure 入口網站的 [虛擬機器]  中檢視已遷移的 Azure VM。 機器名稱會具有尾碼 **-Test**。
6. 測試完成之後，以滑鼠右鍵按一下 [複寫機器]  中的 Azure VM，然後按一下 [清除測試移轉]  。

    ![清除移轉](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>移轉 VM

確認測試移轉如預期運作之後，您就可以遷移內部部署機器。

1. 在 [Azure Migrate 專案] > [伺服器]   >  **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫伺服器]  。

    ![複寫伺服器](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. 在 [複寫機器]  中，以滑鼠右鍵按一下 VM > [遷移]  。
3. 在 [遷移]   > [將虛擬機器關機，在沒有資料遺失的情況下執行計劃性移轉]  中，選取 [是]   > [確定]  。
    - 根據預設，Azure Migrate 會關閉內部部署 VM，並執行隨選複寫，以同步處理上次複寫執行後發生的任何 VM 變更。 這樣可確保不會遺失任何資料。
    - 如果您不想關閉 VM，請選取 [否] 
4. VM 會啟動移轉作業。 請在 Azure 通知中追蹤該作業。
5. 作業完成後，您可以從 [虛擬機器]  頁面檢視及管理 VM。

## <a name="complete-the-migration"></a>完成移轉

1. 完成移轉之後，以滑鼠右鍵按一下 VM > [停止移轉]  。 這會停止內部部署機器的複寫，並清除 VM 的複寫狀態資訊。
2. 在已遷移的機器上安裝 Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) 或 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 代理程式。
3. 執行任何移轉後應用程式調整，例如更新資料庫連接字串和 Web 伺服器設定。
4. 在現在於 Azure 中執行的已移轉應用程式上，執行最終的應用程式和移轉接受度測試。
5. 將流量完全移轉至已遷移的 Azure VM 執行個體。
6. 從您的本機 VM 清查中移除內部部署 VM。
7. 從本機備份中移除內部部署 VM。
8. 更新任何內部文件，以顯示 Azure VM 的新位置和 IP 位址。 

## <a name="post-migration-best-practices"></a>移轉後的最佳做法

- 針對提升復原能力：
    - 使用「Azure 備份」服務來備份 Azure VM 以維護資料安全。 [深入了解](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 將 Azure VM 複寫至次要區域，讓工作負載保持執行且持續可供使用。 [深入了解](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 針對提升安全性：
    - 使用 [Azure 資訊安全中心 - Just In Time 系統管理](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)來鎖定並限制輸入流量存取。
    - 使用[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)來限制傳送至管理端點的網路流量。
    - 部署 [Azure 磁碟加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)以協助保護磁碟，以及防止資料遭到竊取和受到未經授權的存取。
    - 深入了解如何[保護 IaaS 資源](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，並瀏覽 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)。
- 針對監視及管理：
    - 可考慮部署 [Azure 成本管理](https://docs.microsoft.com/azure/cost-management/overview)來監視資源使用情況和花費。


## <a name="next-steps"></a>後續步驟

調查 Azure 雲端採用架構中的[雲端移轉旅程](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)。
