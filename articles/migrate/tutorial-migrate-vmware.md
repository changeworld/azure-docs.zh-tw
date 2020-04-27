---
title: 使用無代理程式 Azure Migrate 伺服器移轉來遷移 VMware VM
description: 了解如何使用 Azure Migrate 執行 VMware VM 的無代理程式移轉。
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 4612c9b0ea2ef8d53b0c04f47628f3789705d833
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535310"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>將 VMware VM 遷移至 Azure (無代理程式)

本文將說明如何搭配 Azure Migrate 伺服器移轉工具使用無代理程式移轉，將內部部署 VMware VM 遷移至 Azure。

[Azure Migrate](migrate-services-overview.md) 提供集中式中樞，可追蹤內部部署應用程式和工作負載及 AWS/GCP VM 執行個體的探索、評估和移轉 (以 Azure 為目標)。 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。

本教學課程是系列中的第三篇，將示範如何使用 Azure Migrate 伺服器評估和移轉來評估 VMware VM 並將其遷移至 Azure。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 準備 VM 以進行移轉。
> * 新增 Azure Migrate 伺服器移轉工具。
> * 探索您想要遷移的 VM。
> * 開始複寫 VM。
> * 執行測試移轉，確定一切都沒問題。
> * 執行完整的 VM 移轉。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="migration-methods"></a>移轉方法

您可以使用 Azure Migrate 伺服器移轉工具將 VMware VM 遷移至 Azure。 此工具為 VMware VM 移轉提供幾個選項：

- 使用無代理程式複寫進行移轉。 無須在 VM 上安裝任何項目即可遷移 VM。
- 使用代理程式複寫來進行移轉。 在 VM 上安裝代理程式以進行複寫。

若要決定是否使用無代理程式或代理程式型移轉，請參閱下列文章：

- [了解](server-migrate-overview.md)無代理程式移轉的運作方式，並[比較移轉方法](server-migrate-overview.md#compare-migration-methods)。
- 如果您想要使用代理程式型方法，請[閱讀這篇文章](tutorial-migrate-vmware-agent.md)。

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前，您必須：

1. [完成本系列的第一個教學課程](tutorial-prepare-vmware.md)，設定 Azure 和 VMware 以進行移轉。 具體而言，在本教學課程中，您需要：
    - [準備 Azure](tutorial-prepare-vmware.md#prepare-azure) 以進行移轉。
    - [準備內部部署環境](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration)以進行移轉。
    
2. 建議您先嘗試使用 Azure Migrate 伺服器評估來評估 VMware VM，再將其遷移至 Azure。 若要設定評估，請[完成本系列的第二個教學課程](tutorial-assess-vmware.md)。 如果您不想要評估 VM，則可以略過此教學課程。 雖然我們建議您試用評估，但並非一定要先執行評估才能嘗試移轉。



## <a name="add-the-azure-migrate-server-migration-tool"></a>新增 Azure Migrate 伺服器移轉工具

新增 Azure Migrate：伺服器移轉工具。

- 如果您遵循第二個教學課程來[評估 VMware VM](/tutorial-assess-vmware.md)，您可以繼續並新增工具。
- 如果您未遵循第二個教學課程，[請遵循這些指示](how-to-add-tool-first-time.md)來設定 Azure Migrate 專案。  您會在建立專案時新增 Azure Migrate：伺服器移轉工具。

如果您已設定專案，請新增工具，如下所示：

1. 在 Azure Migrate 專案中，按一下 [概觀]  。 
2. 在 [探索、評估和遷移伺服器]  底下，按一下 [評估和遷移伺服器]  。

     ![評估和遷移伺服器](./media/tutorial-migrate-vmware/assess-migrate.png)

3. 在 [移轉工具]  中，選取 [當您準備好遷移時按一下此處來新增移轉工具]  。

    ![選取工具](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. 在工具清單中選取 **[Azure Migrate：伺服器移轉]**  > [新增工具] 

    ![伺服器移轉工具](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>設定 Azure Migrate 設備

Azure Migrate 伺服器移轉會執行輕量型 VMware VM 設備。 此設備會執行 VM 探索，並將 VM 的中繼資料和效能資料傳送至「Azure Migrate 伺服器移轉」。 Azure Migrate：伺服器評估工具也會使用相同的設備來執行 VMware VM 的無代理程式移轉。

- 如果您已遵循[教學課程來評估 VMware VM](tutorial-assess-vmware.md)，您便已在該教學課程進行期間設定好設備。
- 如果您未遵循該教學課程，您可以使用下列其中一種方法來立即設定設備：
    - 使用下載的 OVA 範本，在 VMware VM 上進行[設定](how-to-set-up-appliance-vmware.md)。
    - 使用 PowerShell 安裝程式指令碼在 VMware VM 或實體機器上進行設定。 如果您無法使用 OVA 範本設定 VM，或如果您是在 Azure Government 中，則應該使用[此方法](deploy-appliance-script.md)。

建立設備之後，您會檢查其是否可以連線到 Azure Migrate：伺服器評量、進行第一次設，以及向 Azure Migrate 專案註冊設備。


## <a name="prepare-vms-for-migration"></a>準備 VM 以進行移轉

Azure Migrate 需要稍微變更 VM，以確保 VM 可以遷移至 Azure。

- 在某些作業系統上，Azure Migrate 會自動進行這些變更。 [深入了解](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- 如果您要遷移的 VM 未執行上述其中一個作業系統，則請遵循指示來準備 VM。
- 請務必先進行這些變更再開始移轉。 如果您先遷移 VM 再進行變更，VM 可能無法在 Azure 中啟動。
- 在啟用 VM 的複寫之後，您對內部部署 VM 所做的設定變更就會複寫到 Azure。 為確保系統會複寫變更，請確定作為遷移目的地的復原點，其建立時間比內部部署環境的設定變更時間還晚。


### <a name="prepare-windows-server-vms"></a>準備 Windows Server VM

**動作** | **詳細資料** | **指示**
--- | --- | ---
確定 Azure VM 中的 Windows 磁碟區使用與內部部署 VM 相同的磁碟機代號指派。 | 將 [SAN 原則] 設定為 [全都線上]。 | 1.使用系統管理員帳戶登入 VM，然後開啟命令視窗。<br/> 2.輸入 **diskpart** 以執行 Diskpart 公用程式。<br/> 3.輸入 **SAN POLICY=OnlineAll**<br/> 4.輸入 Exit 離開 Diskpart，然後關閉命令提示字元。
啟用 Azure VM 的 Azure 序列存取主控台 | 這有助於進行疑難排解。 您不需要重新啟動 VM。 Azure VM 會使用磁碟映像來開機，這相當於新 VM 的重新開機。 | 請遵循[這些指示](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console)來啟用。
安裝 Hyper-V 來賓整合 | 如果您要遷移執行 Windows Server 2003 的機器，請在 VM 作業系統上安裝 Hyper-V 來賓 Integration Services。 | [深入了解](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)。
遠端桌面 | 在 VM 上啟用遠端桌面，然後確認 Windows 防火牆不會封鎖任何網路設定檔上的遠端桌面存取。 | [深入了解](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)。

### <a name="prepare-linux-vms"></a>準備 Linux VM

**動作** | **詳細資料** 
--- | --- | ---
安裝 Hyper-V Linux Integration Services | 最新版的 Linux 散發套件預設會包含此元件。
重建 Linux init 映像以包含必要的 Hyper-V 驅動程式 | 這可確保 VM 會在 Azure 中開機，只有某些散發套件才需要這麼做。
啟用 Azure 序列主控台記錄 | 這有助於進行疑難排解。 您不需要重新啟動 VM。 Azure VM 會使用磁碟映像來開機，這相當於新 VM 的重新開機。<br/> 請遵循[這些指示](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)來啟用。
更新裝置對應檔案 | 將具有裝置名稱的裝置對應檔案更新為磁碟區關聯，以使用持續性裝置識別碼
更新 fstab 項目 | 更新項目以使用持續性磁碟區識別碼。
移除 udev 規則 | 移除任何會根據 mac 位址等項目來保留介面名稱的 udev 規則。
更新網路介面 | 更新網路介面以根據 DHCP 來接收 IP 位址。
啟用 ssh | 確定已啟用 ssh，而且 sshd 服務已設定為在重新開機時自動啟動。<br/> 確定傳入的 ssh 連線要求未遭到 OS 防火牆或可編寫指令碼的規則封鎖。

請[遵循這篇文章](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)，其內容會討論在 Azure 上執行 Linux VM 的這些步驟，並包含一些熱門 Linux 散發套件的指示。  


## <a name="replicate-vms"></a>複寫 VM

完成探索之後，您就可以開始將 VMware VM 複寫至 Azure。 

> [!NOTE]
> 您最多可以一起複寫 10 部機器。 如果您需要複寫更多機器，請以 10 個一批的方式同時進行複寫。 針對無代理程式移轉，您最多可以同時執行 100 個複寫。

1. 在 [Azure Migrate 專案] > [伺服器]  、 **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫]  。

    ![複寫 VM](./media/tutorial-migrate-vmware/select-replicate.png)

2. 在 [複寫]  > [來源設定]   > [您的電腦虛擬化了嗎]  中，選取 [是，使用 VMware vSphere]  。
3. 在 [內部部署設備]  中，選取您設定的 Azure Migrate 設備名稱 > [確定]  。 

    ![來源設定](./media/tutorial-migrate-vmware/source-settings.png)

    - 此步驟假設您已在完成教學課程時設定好設備。
    - 如果您尚未設定好設備，請遵循[這篇文章](how-to-set-up-appliance-vmware.md)中的指示。

4. 在 [虛擬機器]  中，選取您要複寫的機器。
    - 如果您已執行 VM 的評估，您可以套用評估結果中的 VM 大小調整和磁碟類型 (進階/標準) 建議。 若要這麼做，請在 [從 Azure Migrate 評估匯入移轉設定?]  中，選取 [是]  選項。
    - 如果您未執行評估，或不想使用評估設定，請選取 [否]  選項。
    - 如果您選擇使用評估，請選取 VM 群組和評估名稱。

    ![選取評估](./media/tutorial-migrate-vmware/select-assessment.png)

5. 在 [虛擬機器]  中，視需要搜尋 VM，並檢查您要遷移的每個 VM。 然後按 [下一步：  目標設定]。

    ![選取 VM](./media/tutorial-migrate-vmware/select-vms.png)

6. 在 [目標設定]  中，選取訂用帳戶、您的遷移目標區域，並指定 Azure VM 在移轉後所在的資源群組。 在 [虛擬網路]  中，選取 Azure VM 在移轉後所將加入的 Azure VNet/子網路。
7. 在 [Azure Hybrid Benefit]  中：

    - 如果您不想套用 Azure Hybrid Benefit，請選取 [否]  。 然後按一下 [下一步]  。
    - 如果您有 Windows Server 機器涵蓋於有效的軟體保證或 Windows Server 訂用帳戶下，且您想要將權益套用至要移轉的機器，請選取 [是]  。 然後按一下 [下一步]  。

    ![目標設定](./media/tutorial-migrate-vmware/target-settings.png)

8. 在 [計算]  中，檢閱 VM 名稱、大小、OS 磁碟類型和可用性設定組。 VM 必須符合 [Azure 需求](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)。

    - **VM 大小**：如果您使用評估建議，[VM 大小] 下拉式清單會包含建議的大小。 否則，Azure Migrate 會根據 Azure 訂用帳戶中最接近的相符項來選擇大小。 或者，您可以在 [Azure VM 大小]  中手動選擇大小。 
    - **OS 磁碟**：指定 VM 的 OS (開機) 磁碟。 OS 磁碟是具有作業系統開機載入器和安裝程式的磁碟。 
    - **可用性設定組**：如果 VM 在移轉後應位於 Azure 可用性設定組中，請指定設定組。 此設定組必須位於您為移轉指定的目標資源群組中。

    ![VM 計算設定](./media/tutorial-migrate-vmware/compute-settings.png)

9. 在 [磁碟]  中，指定是否應將 VM 磁碟複寫至 Azure，並選取 Azure 中的磁碟類型 (標準 SSD/HDD 或進階受控磁碟)。 然後按一下 [下一步]  。
    - 您可以從複寫排除磁碟。
    - 如果您排除磁碟，則在移轉後磁碟將不會出現在 Azure VM 上。 

    ![磁碟](./media/tutorial-migrate-vmware/disks.png)

10. 在 [檢閱並啟動複寫]  中檢閱設定，然後按一下 [複寫]  以啟動伺服器的初始複寫。

> [!NOTE]
> 您可以在複寫開始之前隨時更新複寫設定 (經由 [管理]   > [複寫機器]  )。 在複寫啟動後，就無法變更設定。

### <a name="provisioning-for-the-first-time"></a>第一次佈建

如果這是您在 Azure Migrate 專案中複寫的第一個 VM，Azure Migrate 伺服器移轉會自動在與專案相同的資源群組中佈建這些資源。

- **服務匯流排**：Azure Migrate 伺服器移轉會使用服務匯流排將複寫協調訊息傳送至設備。
- **閘道儲存體帳戶**：伺服器移轉會使用閘道儲存體帳戶來儲存所複寫 VM 的相關狀態資訊。
- **記錄儲存體帳戶**︰Azure Migrate 設備會將 VM 的複寫記錄上傳至記錄儲存體帳戶。 Azure Migrate 會將複寫資訊套用到複本受控磁碟。
- **金鑰保存庫**：Azure Migrate 設備會使用金鑰保存庫來管理服務匯流排的連接字串，以及複寫中所用儲存體帳戶的存取金鑰。 當您準備好時，您應該設定金鑰保存庫存取儲存體帳戶所需的權限。 [檢閱這些權限](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)。   


## <a name="track-and-monitor"></a>追蹤和監視


- 按一下 [複寫]  後，就會開始進行「啟動複寫」作業。 
- 當「啟動複寫」作業順利完成後，機器就會開始進行對 Azure 的初始複寫。
- 在初始複寫期間，系統會建立 VM 快照集。 快照集內的磁碟資料會複寫至 Azure 中的複本受控磁碟。
- 初始複寫完成後，就會開始進行差異複寫。 對內部部署磁碟的累加變更會定期複寫至 Azure 中的複本磁碟。

您可以在入口網站通知中追蹤作業狀態。

您可以監視複寫狀態，只要按一下 [複寫伺服器]  即可 (位於 **[Azure Migrate：伺服器移轉]** 中)。
![監視複寫](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>執行測試移轉


在差異複寫開始後，您可以在執行對 Azure 的完整移轉之前，為 VM 執行測試移轉。 強烈建議您對每個機器都至少執行一次此測試，然後再遷移機器。

- 執行測試移轉會檢查移轉是否將如預期運作，且不會影響到內部部署機器；機器仍可運作，並繼續進行複寫。 
- 測試移轉會使用複寫的資料建立 Azure VM，來模擬移轉 (通常會移轉至 Azure 訂用帳戶中的非生產 VNet)。
- 您可以使用複寫的測試 Azure VM 來驗證移轉、執行應用程式測試以及解決任何問題，然後再進行完整移轉。

依照下列方式執行測試移轉：


1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器移轉]** 中，按一下 [測試遷移的伺服器]  。

     ![測試遷移的伺服器](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. 以滑鼠右鍵按一下要測試的 VM，然後按一下 [測試遷移]  。

    ![測試移轉](./media/tutorial-migrate-vmware/test-migrate.png)

3. 在 [測試移轉]  中，選取 Azure VM 在移轉後將位於其中的 Azure VNet。 建議您使用非生產 VNet。
4. **測試移轉**作業隨即啟動。 請在入口網站通知中監視作業。
5. 移轉完成之後，請在 Azure 入口網站的 [虛擬機器]  中檢視已遷移的 Azure VM。 機器名稱會具有尾碼 **-Test**。
6. 測試完成之後，以滑鼠右鍵按一下 [複寫機器]  中的 Azure VM，然後按一下 [清除測試移轉]  。

    ![清除移轉](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>移轉 VM

確認測試移轉如預期運作之後，您就可以遷移內部部署機器。

1. 在 [Azure Migrate 專案] > [伺服器]   >  **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫伺服器]  。

    ![複寫伺服器](./media/tutorial-migrate-vmware/replicate-servers.png)

2. 在 [複寫機器]  中，以滑鼠右鍵按一下 VM > [遷移]  。
3. 在 [遷移]   > [將虛擬機器關機，在沒有資料遺失的情況下執行計劃性移轉]  中，選取 [是]   > [確定]  。
    - 根據預設，Azure Migrate 會關閉內部部署 VM，並執行隨選複寫，以同步處理上次複寫執行後發生的任何 VM 變更。 這樣可確保不會遺失任何資料。
    - 如果您不想關閉 VM，請選取 [否] 
4. VM 會啟動移轉作業。 請在 Azure 通知中追蹤該作業。
5. 作業完成後，您可以從 [虛擬機器]  頁面檢視及管理 VM。

## <a name="complete-the-migration"></a>完成移轉

1. 在移轉完成後，以滑鼠右鍵按一下 VM > [停止複寫]  。 這會停止內部部署機器的複寫，並清除 VM 的複寫狀態資訊。
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
-  可考慮部署 [Azure 成本管理](https://docs.microsoft.com/azure/cost-management/overview)來監視資源使用情況和花費。


## <a name="next-steps"></a>後續步驟

調查 Azure 雲端採用架構中的[雲端移轉旅程](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)。
