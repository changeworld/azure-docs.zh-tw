---
title: 使用 Azure Migrate 將機器視為實體伺服器遷移至 Azure。
description: 本文說明如何使用 Azure Migrate 將實體機器遷移至 Azure。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: bd560a6ef4a3b4ab5eb4632e7741c764f6e314e1
ms.sourcegitcommit: c538b6e4cf27b992500c079ad9c914c05d55eb7f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854922"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>將機器視為實體伺服器遷移至 Azure

本文說明如何使用 Azure Migrate：伺服器移轉工具，將機器視為實體伺服器遷移至 Azure。 在許多情況下，將機器視為實體伺服器進行遷移，都有其效用：

- 遷移內部部署實體伺服器。
- 遷移由 Xen、KVM 之類的平台虛擬化的 VM。
- 遷移 Hyper-V 或 VMware VM (如果您因故無法使用標準移轉程序進行 [Hyper-V](tutorial-migrate-hyper-v.md) 或 [VMware](server-migrate-overview.md) 的移轉)。
- 遷移在私人雲端中執行的 VM。
- 遷移在公用雲端中執行的 VM，例如 Amazon Web Services (AWS) 或 Google Cloud Platform (GCP)。


此教學課程是一個系列中的第三篇，會示範如何評定實體伺服器並將其移轉到 Azure。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 準備搭配使用 Azure 與 Azure Migrate：伺服器移轉。
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


## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須：

[檢閱](./agent-based-migration-architecture.md)移轉架構。

## <a name="prepare-azure"></a>準備 Azure

準備 Azure 以使用伺服器移轉進行移轉。

**Task** | **詳細資料**
--- | ---
**建立 Azure Migrate 專案** | 您的 Azure 帳戶需要參與者或擁有者權限，才能[建立新專案](https://docs.microsoft.com/azure/migrate/create-manage-projects)。
**驗證您 Azure 帳戶的權限** | 您的 Azure 帳戶必須有建立 VM 以及寫入至 Azure 受控磁碟的權限。


### <a name="assign-permissions-to-create-project"></a>指派建立專案的權限

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]。
2. 在 [檢查存取權] 中，尋找相關的帳戶，然後按一下以查看權限。
3. 您應該會具有「參與者」或「擁有者」權限。
    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作以指派角色。


### <a name="assign-azure-account-permissions"></a>指派 Azure 帳戶權限

將「虛擬機器參與者」角色指派給 Azure 帳戶。 這會提供權限給：

- 在所選的資源群組中建立 VM。
- 在所選的虛擬網路中建立 VM。
- 寫入至 Azure 受控磁碟。 

### <a name="create-an-azure-network"></a>建立 Azure 網路

[設定](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Azure 虛擬網路 (VNet)。 當您複寫至 Azure 時，系統會建立 Azure VM 並將其加入至您設定移轉時所指定的 Azure VNet。

## <a name="prepare-for-migration"></a>為移轉做準備

若要準備進行實體伺服器移轉，您必須驗證實體伺服器設定，並準備部署複寫設備。

### <a name="check-machine-requirements-for-migration"></a>檢查移轉的機器需求

確定機器符合移轉至 Azure 的需求。 

> [!NOTE]
> 遷移實體機器時，Azure Migrate：伺服器移轉會使用與 Azure Site Recovery 服務中的代理程式型災害復原功能相同的複寫架構，而部分元件會共用相同的程式碼基底。 有些內容可能會連結至 Site Recovery 文件。

1. [確認](migrate-support-matrix-physical-migration.md#physical-server-requirements)實體伺服器需求。
2. 確認您複寫到 Azure 的內部部署機器符合 [Azure VM 需求](migrate-support-matrix-physical-migration.md#azure-vm-requirements)。
3. 在您將 VM 遷移至 Azure 之前，VM 需要進行一些變更。
    - 在某些作業系統上，Azure Migrate 會自動進行這些變更。 
    - 請務必先進行這些變更再開始移轉。 如果您先遷移 VM 再進行變更，VM 可能無法在 Azure 中啟動。 請參閱您所需進行的 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 變更。

### <a name="prepare-a-machine-for-the-replication-appliance"></a>準備複寫設備的機器

「Azure Migrate 伺服器移轉」會使用複寫設備將機器複寫至 Azure。 複寫設備會執行下列元件。

- **設定伺服器**：組態伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- **處理序伺服器**：處理序伺服器可作為複寫閘道。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將其傳送至 Azure 中的快取儲存體帳戶。 

準備設備部署，如下所示：

- 準備裝載複寫設備的機器。 [請檢閱](migrate-replication-appliance.md#appliance-requirements)機器需求。
- 複寫設備會使用 MySQL。 檢閱在設備上安裝 MySQL 的[選項](migrate-replication-appliance.md#mysql-installation)。
- 檢閱複寫設備存取[公用](migrate-replication-appliance.md#url-access)和[政府](migrate-replication-appliance.md#azure-government-url-access)雲端所需的 Azure URL。
- 檢閱複寫設備的[連接埠存取](migrate-replication-appliance.md#port-access)需求。

> [!NOTE]
> 複寫設備不應該安裝在您想要複寫的來源機器上，也不應該安裝在您先前已安裝的 Azure Migrate 探索和評估設備上。

## <a name="set-up-the-replication-appliance"></a>設定複寫設備

移轉的第一個步驟是設定複寫設備。 若要設定設備以進行實體伺服器移轉，請下載設備的安裝程式檔案，然後在[您準備的機器](#prepare-a-machine-for-the-replication-appliance)上執行該檔案。 安裝設備後，您可以將其註冊至「Azure Migrate 伺服器移轉」。


### <a name="download-the-replication-appliance-installer"></a>下載複寫設備安裝程式

1. 在 [Azure Migrate 專案] > [伺服器] 的 **[Azure Migrate：伺服器移轉]** 中，按一下 [探索]。

    ![探索 VM](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. 在 [探索機器] > [機器是否已虛擬化?] 中，按一下 [未虛擬化/其他]。
4. 在 [目標區域] 中，選取您要將機器遷移到的 Azure 區域。
5. 選取 [確認移轉的目標區域為 region-name]。
6. 按一下 [建立資源]。 這會在背景中建立 Azure Site Recovery 保存庫。
    - 如果您已使用「Azure Migrate 伺服器移轉」來設定移轉，則無法設定目標選項，因為先前已設定資源。    
    - 按一下此按鈕後，即無法變更此專案的目標區域。
    - 所有後續的移轉都會以此區域為目標。

7. 在 [是否要安裝新的複寫設備?] 中，選取 [安裝複寫設備]。
9. 在 [下載並安裝複寫設備軟體] 中，下載設備安裝程式和註冊金鑰。 您必須要有金鑰，才能註冊設備。 此金鑰在下載後有五天的有效期。

    ![下載提供者](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. 將設備安裝程式檔案和金鑰檔案複製到您為設備建立的 Windows Server 2016 機器。
11. 安裝完成之後，系統會自動啟動設備設定精靈 (您也可以使用在設備桌面上建立的 cspsconfigtool 捷徑來手動啟動精靈)。 使用精靈的 [管理帳戶] 索引標籤，新增要用於行動服務推送安裝的帳戶詳細資料。 在本教學課程中，我們將在要複寫的來源 VM 上手動安裝行動服務，因此請在此步驟中建立虛擬帳戶並繼續進行。 您可以提供下列詳細資料，以建立虛擬帳戶：「來賓」作為易記名稱、「使用者名稱」作為使用者名稱，以及「密碼」作為帳戶的密碼。 您將會在啟用複寫階段中使用此虛擬帳戶。 

12. 在安裝後重新啟動設備之後，在 [探索機器] 的 [選取組態伺服器] 中選取新設備，然後按一下 [完成註冊]。 完成註冊作業會執行數項最終工作，以備妥複寫設備。

    ![完成註冊](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

完成註冊後可能需要一點時間，所探索到的機器才會出現在「Azure Migrate 伺服器移轉」中。 探索到 VM 時，[探索到的伺服器] 計數即會上升。

![探索到的伺服器](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>安裝行動服務

在要遷移的機器上，您必須安裝行動服務代理程式。 代理程式安裝程式可在複寫設備上取得。 您必須找出正確的安裝程式，並在要遷移的每個機器上安裝代理程式。 以下列方式來執行此動作：

1. 登入複寫設備。
2. 瀏覽至 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**。
3. 尋找機器作業系統和版本適用的安裝程式。 檢閱[支援的作業系統](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 
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
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
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
    sudo ./install -r MS -v VmWare -q
    ```
3. 將代理程式註冊至複寫設備：
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>複寫機器

現在，選取要進行移轉的機器。 

> [!NOTE]
> 您最多可以一起複寫 10 部機器。 如果您需要複寫更多機器，請以 10 個一批的方式同時進行複寫。

1. 在 [Azure Migrate 專案] > [伺服器]、 **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫]。

    ![Azure Migrate - 伺服器畫面的螢幕擷取畫面，其中會顯示已在移轉工具下 [Azure Migrate：伺服器移轉] 中選取的 [複寫] 按鈕。](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. 在 [複寫] > [來源設定][您的電腦虛擬化了嗎] >  中，選取 [未虛擬化/其他]。
3. 在 [內部部署設備] 中，選取您設定的 Azure Migrate 設備的名稱。
4. 在 [處理序伺服器] 中，選取複寫設備的名稱。
6. 在 **來賓認證** 中，請選取先前在 [複寫安裝程式安裝](#download-the-replication-appliance-installer)期間建立的虛擬帳戶以手動安裝行動服務 (不支援推送安裝)。 然後按 [下一步：**虛擬機器]** 。   

    ![[複寫] 畫面中 [來源設定] 索引標籤的螢幕擷取畫面，其中已反白顯示 [來賓認證] 欄位。](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. 在 [虛擬機器] 的 [從評量匯入移轉設定?] 中，保留預設設定 [否，我將手動指定移轉設定]。
8. 檢查您要遷移的每個 VM。 然後按 [下一步：目標設定]。

    ![選取 VM](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. 在 [目標設定] 中，選取訂用帳戶、您的遷移目標區域，並指定 Azure VM 在移轉後所在的資源群組。
10. 在 [虛擬網路] 中，選取 Azure VM 在移轉後所將加入的 Azure VNet/子網路。
11. 在 **可用性選項** 中，選取：
    -  可用性區域，將已遷移的機器釘選到該區域中特定的可用性區域。 使用此選項可將形成多節點應用程式層的伺服器散發到可用性區域。 如果選取此選項，則必須在計算索引標籤中指定要用於每部所選電腦的可用性區域。只有選取要移轉的目的地區域支援可用性區域時，才可以使用此選項
    -  可用性設定組，可將遷移的電腦放在可用性設定組中。 選取的目標資源群組必須有一或多個可用性設定組，才能使用此選項。
    - 如果您不需要為已遷移的電腦提供任何一種可用性設定，則不需要任何基礎結構備援選項。
12. 在 [Azure Hybrid Benefit] 中：

    - 如果您不想套用 Azure Hybrid Benefit，請選取 [否]。 然後按一下 [下一步]。
    - 如果您有 Windows Server 機器涵蓋於有效的軟體保證或 Windows Server 訂用帳戶下，且您想要將權益套用至要移轉的機器，請選取 [是]。 然後按一下 [下一步]。

    ![目標設定](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

13. 請檢閱 **計算** 中的 VM 名稱、大小、OS 磁碟類型和可用性設定 (如果有在上一個步驟中選取)。 VM 必須符合 [Azure 需求](migrate-support-matrix-physical-migration.md#azure-vm-requirements)。

    - **VM 大小**：如果您使用評估建議，[VM 大小] 下拉式清單會顯示建議的大小。 否則，Azure Migrate 會根據 Azure 訂用帳戶中最接近的相符項來選擇大小。 或者，您可以在 [Azure VM 大小] 中手動選擇大小。
    - **OS 磁碟**：指定 VM 的 OS (開機) 磁碟。 OS 磁碟是具有作業系統開機載入器和安裝程式的磁碟。
    - **可用性區域**：指定要使用的可用性區域。
    - **可用性設定組**：指定要使用的可用性設定組。

> [!NOTE]
> 如果要為一組虛擬機器選取不同的可用性選項，請移至步驟1，然後在為一組虛擬機器啟動複寫之後，選取不同的可用性選項並重複這些步驟。

   ![計算設定](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. 在 [磁碟] 中，指定是否應將 VM 磁碟複寫至 Azure，並選取 Azure 中的磁碟類型 (標準 SSD/HDD 或進階受控磁碟)。 然後按一下 [下一步]  。
    - 您可以從複寫排除磁碟。
    - 如果您排除磁碟，則在移轉後磁碟將不會出現在 Azure VM 上。 

    ![磁碟設定](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. 在 [檢閱並啟動複寫] 中檢閱設定，然後按一下 [複寫] 以啟動伺服器的初始複寫。

> [!NOTE]
> 您可以在複寫開始之前隨時更新複寫設定 (經由 [管理] > [複寫機器])。 在複寫啟動後，就無法變更設定。



## <a name="track-and-monitor"></a>追蹤和監視

- 按一下 [複寫] 後，就會開始進行「啟動複寫」作業。 
- 當「啟動複寫」作業順利完成後，機器就會開始進行對 Azure 的初始複寫。
- 初始複寫完成後，就會開始進行差異複寫。 對內部部署磁碟的累加變更會定期複寫至 Azure 中的複本磁碟。


您可以在入口網站通知中追蹤作業狀態。

您可以監視複寫狀態，只要按一下 [複寫伺服器] 即可 (位於 **[Azure Migrate：伺服器移轉]** 中)。
![監視複寫](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>執行測試移轉


在差異複寫開始後，您可以在執行對 Azure 的完整移轉之前，為 VM 執行測試移轉。 強烈建議您對每個機器都至少執行一次此測試，然後再遷移機器。

- 執行測試移轉會檢查移轉是否將如預期運作，且不會影響到內部部署機器；機器仍可運作，並繼續進行複寫。 
- 測試移轉會使用複寫的資料建立 Azure VM，來模擬移轉 (通常會移轉至 Azure 訂用帳戶中的非生產 VNet)。
- 您可以使用複寫的測試 Azure VM 來驗證移轉、執行應用程式測試以及解決任何問題，然後再進行完整移轉。

依照下列方式執行測試移轉：


1. 在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器移轉]** 中，按一下 [測試遷移的伺服器]。

     ![測試遷移的伺服器](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. 以滑鼠右鍵按一下要測試的 VM，然後按一下 [測試遷移]。

    ![測試移轉](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. 在 [測試移轉] 中，選取 Azure VM 在移轉後將位於其中的 Azure VNet。 建議您使用非生產 VNet。
4. **測試移轉** 作業隨即啟動。 請在入口網站通知中監視作業。
5. 移轉完成之後，請在 Azure 入口網站的 [虛擬機器] 中檢視已遷移的 Azure VM。 機器名稱會具有尾碼 **-Test**。
6. 測試完成之後，以滑鼠右鍵按一下 [複寫機器] 中的 Azure VM，然後按一下 [清除測試移轉]。

    ![清除移轉](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>移轉 VM

確認測試移轉如預期運作之後，您就可以遷移內部部署機器。

1. 在 [Azure Migrate 專案] > [伺服器] >  **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫伺服器]。

    ![複寫伺服器](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. 在 [複寫機器] 中，以滑鼠右鍵按一下 VM > [遷移]。
3. 在 [遷移] > [將虛擬機器關機，在沒有資料遺失的情況下執行計劃性移轉] 中，選取 [是] > [確定]。
    - 如果您不想關閉 VM，請選取 [否]
    
    注意:針對實體伺服器移轉，建議您將應用程式降低為移轉視窗的一部份 (不要讓應用程式接受任何連線)，然後起始移轉 (伺服器必須保持執行狀態，讓其餘的變更可以同步處理)，才能完成移轉。

4. VM 會啟動移轉作業。 請在 Azure 通知中追蹤該作業。
5. 作業完成後，您可以從 [虛擬機器] 頁面檢視及管理 VM。

## <a name="complete-the-migration"></a>完成移轉

1. 完成移轉之後，以滑鼠右鍵按一下 VM > [停止移轉]。 這會執行以下動作：
    - 停止內部部署機器的複寫。
    - 從 Azure Migrate 中的 **複寫伺服器** 計數移除機器：伺服器移轉。
    - 清除機器的複寫狀態資訊。
2. 在已遷移的機器上安裝 Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) 或 [Linux](../virtual-machines/extensions/agent-linux.md) 代理程式。
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
    - 使用 [Azure 資訊安全中心 - Just In Time 系統管理](../security-center/security-center-just-in-time.md)來鎖定並限制輸入流量存取。
    - 使用[網路安全性群組](../virtual-network/network-security-groups-overview.md)來限制傳送至管理端點的網路流量。
    - 部署 [Azure 磁碟加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)以協助保護磁碟，以及防止資料遭到竊取和受到未經授權的存取。
    - 深入了解如何[保護 IaaS 資源](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，並瀏覽 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)。
- 針對監視及管理：
    - 可考慮部署 [Azure 成本管理](../cost-management-billing/cloudyn/overview.md)來監視資源使用情況和花費。


## <a name="next-steps"></a>後續步驟

調查 Azure 雲端採用架構中的[雲端移轉旅程](/azure/architecture/cloud-adoption/getting-started/migrate)。
