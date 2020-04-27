---
title: 使用 Azure Migrate 伺服器移轉將 Hyper-V VM 遷移至 Azure
description: 了解如何使用 Azure Migrate 伺服器移轉將內部部署 Hyper-V VM 遷移至 Azure
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 6b9732aab9e3fe0d26b4c572efe87c3a9d3e29f6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535344"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>將 Hyper-V VM 遷移至 Azure 

本文將說明如何搭配 Azure Migrate 使用無代理程式移轉，將內部部署 Hyper-V VM 遷移至 Azure：。

[Azure Migrate](migrate-services-overview.md) 提供集中式中樞，可追蹤內部部署應用程式和工作負載及私人/公用雲端 VM 的探索、評量和移轉 (以 Azure 為目標)。 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。

本教學課程是系列中的第三篇，將示範如何使用 Azure Migrate 伺服器評量和伺服器移轉來評估 Hyper-V 並將其遷移至 Azure。 在本教學課程中，您會了解如何：


> [!div class="checklist"]
> * 準備 Azure 和您的內部部署 Hyper-V 環境
> * 設定來源環境。
> * 設定目標環境。
> * 啟用複寫。
> * 執行測試移轉，確定一切都沒問題。
> * 執行對 Azure 的完整移轉。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前，您必須：

1. [檢閱](hyper-v-migration-architecture.md) Hyper-V 移轉架構。
2. [請參閱](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Hyper-V 主機需求，以及 Hyper-V 主機需要存取的 Azure URL。
3. [檢閱](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)您要遷移的 Hyper-V VM 有何需求。 Hyper-V VM 必須符合 [Azure VM 需求](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)。
2. 我們建議您完成本系列中的前幾個教學課程。 [第一個教學課程](tutorial-prepare-hyper-v.md)會示範如何設定 Azure 和 Hyper-V 以進行遷移。 第二個教學課程會示範如何使用「Azure Migrate：伺服器評量」，在移轉之前[評估 Hyper-V VM](tutorial-assess-hyper-v.md)。 
    > [!NOTE]
    > 雖然我們建議您嘗試使用評量，但並非一定要執行評量，才能遷移 VM。
    > 若要遷移 Hyper-V Vm，Azure Migrate：伺服器移轉會在 Hyper-V 主機或叢集節點上執行軟體代理程式 (Microsoft Azure Site Recovery 提供者和 Microsoft Azure 復原服務代理程式)，以協調資料並將資料複寫至 Azure Migrate。 [Azure Migrate 設備](migrate-appliance.md)不會用於 Hyper-V 移轉。

3. 確定您的 Azure 帳戶已獲派「虛擬機器參與者」角色，讓您有權執行下列作業：

    - 在所選的資源群組中建立 VM。
    - 在所選的虛擬網路中建立 VM。
    - 寫入至 Azure 受控磁碟。
4. [設定 Azure 網路](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 當您遷移至 Azure 時，所建立的 Azure VM 會加入您設定移轉時指定的 Azure 網路。

## <a name="add-the-azure-migrateserver-migration-tool"></a>新增 Azure Migrate：伺服器移轉工具

新增 Azure Migrate：伺服器移轉工具。

- 如果您遵循第二個教學課程來[評估 VMware VM](/tutorial-assess-hyper-v.md)，則您已經設定了 Azure Migrate 專案，而且可以立即加入工具。
- 如果您未遵循第二個教學課程，[請遵循這些指示](how-to-add-tool-first-time.md)來設定 Azure Migrate 專案。 您會在建立專案時新增 Azure Migrate：伺服器移轉工具。

如果您已設定專案，請新增工具，如下所示：

1. 在 Azure Migrate 專案中，按一下 [概觀]  。 
2. 在 [探索、評估和遷移伺服器]  底下，按一下 [評估和遷移伺服器]  。
3. 在 [移轉工具]  中，選取 [當您準備好遷移時按一下此處來新增移轉工具]  。

    ![選取工具](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. 在工具清單中選取 **[Azure Migrate：伺服器移轉]**  > [新增工具] 

    ![伺服器移轉工具](./media/tutorial-migrate-hyper-v/server-migration-tool.png)

## <a name="prepare-hyper-v-hosts"></a>準備 Hyper-V 主機


1. 在 [Azure Migrate 專案] > [伺服器]  的 **[Azure Migrate：伺服器移轉]** 中，按一下 [探索]  。
2. 在 [探索機器]   > [機器是否已虛擬化?]  中，選取 [是，使用 Hyper-V]  。
3. 在 [目標區域]  中，選取您要將機器遷移到的 Azure 區域。
6. 選取 [確認移轉的目標區域為 region-name]  。
7. 按一下 [建立資源]  。 這會在背景中建立 Azure Site Recovery 保存庫。
    - 如果您已使用「Azure Migrate 伺服器移轉」來設定移轉，則無法顯示此選項，因為先前已設定資源。
    - 按一下此按鈕後，即無法變更此專案的目標區域。
    - 所有後續的移轉都會以此區域為目標。
    
8. 在 [準備 Hyper-V 主機伺服器]  中，下載 Hyper-V 複寫提供者和註冊金鑰檔案。
    - 需要註冊金鑰，才能向 Azure Migrate 伺服器移轉註冊 Hyper-V 主機。
    - 該金鑰在產生後會維持 5 天有效。

    ![下載提供者和金鑰](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. 將提供者安裝檔案和註冊金鑰檔案複製到每部執行所要複寫 VM 的 Hyper-V 主機 (或叢集節點)。
5. 在每部主機上執行提供者安裝檔案，如下一個程序所述。
6. 在主機上安裝提供者之後，在 [探索機器]  中，按一下 [完成註冊]  。

    ![完成註冊](./media/tutorial-migrate-hyper-v/finalize-registration.png)

完成登錄後最多可能需要 15 分鐘的時間，探索到的 VM 才會出現在「Azure Migrate 伺服器移轉」中。 探索到 VM 時，[探索到的伺服器]  計數即會上升。

![探索到的伺服器](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>複寫 Hyper-V VM

完成探索之後，您就可以開始將 Hyper-V VM 複寫至 Azure。

> [!NOTE]
> 您最多可以一起複寫 10 部機器。 如果您需要複寫更多機器，請以 10 個一批的方式同時進行複寫。

1. 在 [Azure Migrate 專案] > [伺服器]  、 **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫]  。
2. 在 [複寫]  > [來源設定]  [您的電腦虛擬化了嗎] >   中，選取 [是，使用 Hyper-V]  。 然後按 [下一步：**虛擬機器]** 。
3. 在 [虛擬機器]  中，選取您要複寫的機器。
    - 如果您已執行 VM 的評估，您可以套用評估結果中的 VM 大小調整和磁碟類型 (進階/標準) 建議。 若要這麼做，請在 [從 Azure Migrate 評估匯入移轉設定?]  中，選取 [是]  選項。
    - 如果您未執行評估，或不想使用評估設定，請選取 [否]  選項。
    - 如果您選擇使用評估，請選取 VM 群組和評估名稱。

        ![選取評估](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. 在 [虛擬機器]  中，視需要搜尋 VM，並檢查您要遷移的每個 VM。 然後按 **[下一步：** 目標設定]。

    ![選取 VM](./media/tutorial-migrate-hyper-v/select-vms.png)

5. 在 [目標設定]  中，選取您遷移訂用帳戶的目標區域，以及 Azure VM 在移轉後所在的資源群組。
7. 在 [複寫儲存體帳戶]  中，選取 Azure 儲存體帳戶，其中複寫的資料會儲存在 Azure 中。
8. 在 [虛擬網路]  中，選取 Azure VM 在移轉後所將加入的 Azure VNet/子網路。
9. 在 [Azure Hybrid Benefit]  中：

    - 如果您不想套用 Azure Hybrid Benefit，請選取 [否]  。 然後按 [下一步]  。
    - 如果您有 Windows Server 機器涵蓋於有效的軟體保證或 Windows Server 訂用帳戶下，且您想要將權益套用至要移轉的機器，請選取 [是]  。 然後按一下 [下一步]  。

    ![目標設定](./media/tutorial-migrate-hyper-v/target-settings.png)

10. 在 [計算]  中，檢閱 VM 名稱、大小、OS 磁碟類型和可用性設定組。 VM 必須符合 [Azure 需求](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)。

    - **VM 大小**：如果您使用評估建議，[VM 大小] 下拉式清單會包含建議的大小。 否則，Azure Migrate 會根據 Azure 訂用帳戶中最接近的相符項來選擇大小。 或者，您可以在 [Azure VM 大小]  中手動選擇大小。 
    - **OS 磁碟**：指定 VM 的 OS (開機) 磁碟。 OS 磁碟是具有作業系統開機載入器和安裝程式的磁碟。 
    - **可用性設定組**：如果 VM 在移轉後應位於 Azure 可用性設定組中，請指定設定組。 此設定組必須位於您為移轉指定的目標資源群組中。

    ![VM 計算設定](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. 在 [磁碟]  中，指定是否應將 VM 磁碟複寫至 Azure，並選取 Azure 中的磁碟類型 (標準 SSD/HDD 或進階受控磁碟)。 然後按一下 [下一步]  。
    - 您可以從複寫排除磁碟。
    - 如果您排除磁碟，則在移轉後磁碟將不會出現在 Azure VM 上。 

    ![磁碟](./media/tutorial-migrate-hyper-v/disks.png)

10. 在 [檢閱並啟動複寫]  中檢閱設定，然後按一下 [複寫]  以啟動伺服器的初始複寫。

> [!NOTE]
> 您可以在複寫開始之前隨時更新複寫設定 (經由 [管理]   > [複寫機器]  )。 在複寫啟動後，就無法變更設定。

## <a name="provisioning-for-the-first-time"></a>第一次佈建

如果這是您要在 Azure Migrate 專案中複寫的第一個 VM，Azure Migrate：伺服器移轉會自動在與專案相同的資源群組中佈建這些資源。

- **服務匯流排**：Azure Migrate：伺服器移轉會使用服務匯流排將複寫協調訊息傳送至設備。
- **閘道儲存體帳戶**：Azure Migrate：伺服器移轉會使用閘道儲存體帳戶來儲存所複寫 VM 的相關狀態資訊。
- **記錄儲存體帳戶**︰Azure Migrate 設備會將 VM 的複寫記錄上傳至記錄儲存體帳戶。 Azure Migrate 會將複寫資訊套用到複本受控磁碟。
- **金鑰保存庫**：Azure Migrate 設備會使用金鑰保存庫來管理服務匯流排的連接字串，以及複寫中所用儲存體帳戶的存取金鑰。 當您針對 Hyper-V VM 評量和移轉[備妥 Azure](tutorial-prepare-hyper-v.md#prepare-azure) 時，您應該設定金鑰保存庫存取儲存體帳戶所需的權限。 


## <a name="track-and-monitor"></a>追蹤和監視


- 按一下 [複寫]  後，就會開始進行「啟動複寫」作業。 
- 當「啟動複寫」作業順利完成後，機器就會開始進行對 Azure 的初始複寫。
- 初始複寫完成後，就會開始進行差異複寫。 對內部部署磁碟的累加變更會定期複寫至 Azure。

您可以在入口網站通知中追蹤作業狀態。

您可以監視複寫狀態，只要按一下 [複寫伺服器]  即可 (位於 **[Azure Migrate：伺服器移轉]** 中)。
![監視複寫](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>執行測試移轉


在差異複寫開始後，您可以在執行對 Azure 的完整移轉之前，為 VM 執行測試移轉。 強烈建議您對每個機器都至少執行一次此測試，然後再遷移機器。

- 執行測試移轉會檢查移轉是否將如預期運作，且不會影響到內部部署機器；機器仍可運作，並繼續進行複寫。 
- 測試移轉會使用複寫的資料建立 Azure VM，來模擬移轉 (通常會移轉至 Azure 訂用帳戶中的非生產 Azure VNet)。
- 您可以使用複寫的測試 Azure VM 來驗證移轉、執行應用程式測試以及解決任何問題，然後再進行完整移轉。

依照下列方式執行測試移轉：


1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器移轉]** 中，按一下 [測試遷移的伺服器]  。

     ![測試遷移的伺服器](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. 以滑鼠右鍵按一下要測試的 VM，然後按一下 [測試遷移]  。

    ![測試移轉](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. 在 [測試移轉]  中，選取 Azure VM 在移轉後將位於其中的 Azure 虛擬網路。 建議您使用非生產虛擬網路。
4. **測試移轉**作業隨即啟動。 請在入口網站通知中監視作業。
5. 移轉完成之後，請在 Azure 入口網站的 [虛擬機器]  中檢視已遷移的 Azure VM。 機器名稱會具有尾碼 **-Test**。
6. 測試完成之後，以滑鼠右鍵按一下 [複寫機器]  中的 Azure VM，然後按一下 [清除測試移轉]  。

    ![清除移轉](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>移轉 VM

確認測試移轉如預期運作之後，您就可以遷移內部部署機器。

1. 在 [Azure Migrate 專案] > [伺服器]   >  **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫伺服器]  。

    ![複寫伺服器](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. 在 [複寫機器]  中，以滑鼠右鍵按一下 VM > [遷移]  。
3. 在 [遷移]   > [將虛擬機器關機，在沒有資料遺失的情況下執行計劃性移轉]  中，選取 [是]   > [確定]  。
    - 根據預設，Azure Migrate 會關閉內部部署 VM，並執行隨選複寫，以同步處理上次複寫執行後發生的任何 VM 變更。 這樣可確保不會遺失任何資料。
    - 如果您不想關閉 VM，請選取 [否] 
4. VM 會啟動移轉作業。 請在 Azure 通知中追蹤該作業。
5. 作業完成後，您可以從 [虛擬機器]  頁面檢視及管理 VM。

## <a name="complete-the-migration"></a>完成移轉

1. 完成移轉之後，以滑鼠右鍵按一下 VM > [停止移轉]  。 這會執行以下動作：
    - 停止內部部署機器的複寫。
    - 從 Azure Migrate 中的**複寫伺服器**計數移除機器：伺服器移轉。
    - 清除 VM 的複寫狀態資訊。
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
