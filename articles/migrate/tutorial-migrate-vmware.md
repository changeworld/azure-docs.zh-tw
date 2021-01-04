---
title: 使用無代理程式 Azure Migrate 伺服器移轉來遷移 VMware VM
description: 了解如何使用 Azure Migrate 執行 VMware VM 的無代理程式移轉。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: f9e7547b8b9a8728c2c1ce7d1d01d06ad1d55dc1
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387236"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>將 VMware VM 遷移至 Azure (無代理程式)

本文說明如何使用 [Azure Migrate：伺服器移轉](migrate-services-overview.md#azure-migrate-server-migration-tool)工具搭配無代理程式移轉，將內部部署 VMware VM 遷移至 Azure。 您也可使用代理程式型移轉來遷移 VMware VM。 [比較](server-migrate-overview.md#compare-migration-methods)方法。

本教學課程是一個系列中的第三篇，會示範如何評估 VMware VM 並將其遷移至 Azure。 

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增 Azure Migrate：伺服器移轉工具。
> * 探索您想要遷移的 VM。
> * 開始複寫 VM。
> * 執行測試移轉，確定一切都沒問題。
> * 執行完整的 VM 移轉。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須：

1. [完成第一個教學課程](./tutorial-discover-vmware.md)，準備 Azure 和 VMware 進行移轉。
2. 我們建議您先[評估 Mware VM](./tutorial-assess-vmware-azure-vm.md)，再將其遷移至 Azure，但您不必這麼做。 
3. 移至已建立的專案，或 [建立新的專案](./create-manage-projects.md)
4. 驗證 Azure 帳戶的權限 - 您的 Azure 帳戶必須有建立 VM 以及寫入至 Azure 受控磁碟的權限。

## <a name="set-up-the-azure-migrate-appliance"></a>設定 Azure Migrate 設備

Azure Migrate 伺服器移轉會執行輕量型 VMware VM 設備，該設備用於 VMware VM 的探索、評量和無代理程式移轉。 如果您遵循[評估教學課程](./tutorial-assess-vmware-azure-vm.md)，便已經設定好設備。 如果您還未設定，請使用下列其中一種方法立即設定：

- **OVA 範本**：使用下載的 OVA 範本，在 VMware VM 上進行 [設定](how-to-set-up-appliance-vmware.md)。
- **Script**：使用 PowerShell 安裝程式指令碼，在 VMware VM 或實體機器上進行 [設定](deploy-appliance-script.md)。 如果您無法使用 OVA 範本設定 VM，或如果您是在 Azure Government 中，則應該使用此方法。

建立設備之後，您會檢查其是否可以連線到 Azure Migrate：伺服器評量、進行第一次設，以及向 Azure Migrate 專案註冊設備。

## <a name="replicate-vms"></a>複寫 VM

設定好設備並完成探索之後，您就可以開始將 VMware VM 複寫到 Azure。 

- 您可以同時執行多達 300 個複寫作業。
- 在入口網站中，您一次最多可以選取 10 個 VM 進行移轉。 若要遷移更多機器，請將其分批 (一批 10 個) 新增至群組。

啟用複寫，如下所示︰

1. 在 [Azure Migrate 專案] > [伺服器]、 **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫]。

    ![複寫 VM](./media/tutorial-migrate-vmware/select-replicate.png)

2. 在 [複寫] > [來源設定] > [您的電腦虛擬化了嗎] 中，選取 [是，使用 VMware vSphere]。
3. 在 [內部部署設備] 中，選取您設定的 Azure Migrate 設備名稱 > [確定]。 

    ![來源設定](./media/tutorial-migrate-vmware/source-settings.png)

4. 在 [虛擬機器] 中，選取您要複寫的機器。 若要套用評估中的 VM 大小和磁碟類型 (如果您已執行評估)，請在 [從 Azure Migrate 評估匯入移轉設定?] 中，選取 [是]，然後選取 VM 群組和評估名稱。 如果您未使用評估設定，請選取 [否]。
   
    ![選取評估](./media/tutorial-migrate-vmware/select-assessment.png)

5. 在 [虛擬機器] 中，選取您想要遷移的 VM。 然後按 [下一步：目標設定]。

    ![選取 VM](./media/tutorial-migrate-vmware/select-vms.png)

6. 在 [目標設定] 中，選取訂用帳戶和目標區域。 指定 Azure VM 在移轉後所在的資源群組。
7. 在 [虛擬網路] 中，選取 Azure VM 在移轉後所加入的 Azure VNet/子網路。
8. 在 **可用性選項** 中，選取：
    -  可用性區域，將已遷移的機器釘選到該區域中特定的可用性區域。 使用此選項可將形成多節點應用程式層的伺服器散發到可用性區域。 如果選取此選項，則必須在計算索引標籤中指定要用於每部所選電腦的可用性區域。只有選取要移轉的目的地區域支援可用性區域時，才可以使用此選項
    -  可用性設定組，可將遷移的電腦放在可用性設定組中。 選取的目標資源群組必須有一或多個可用性設定組，才能使用此選項。
    - 如果您不需要為已遷移的電腦提供任何一種可用性設定，則不需要任何基礎結構備援選項。
9. 在 **磁碟加密類型** 中，選取：
    - 使用平台代控金鑰加密待用資料
    - 使用客戶自控金鑰加密待用資料

   > [!NOTE]
   > 若要使用 CMK 複寫 VM，您必須在目標資源群組下[建立磁碟加密集](https://go.microsoft.com/fwlink/?linkid=2151800)。 磁碟加密設定物件會將受控磁碟對應至包含要用於 SSE 之 CMK 的 Key Vault。
  
10. 在 [Azure Hybrid Benefit] 中：

    - 如果您不想套用 Azure Hybrid Benefit，請選取 [否]。 然後按一下 [下一步]  。
    - 如果您有 Windows Server 機器涵蓋於有效的軟體保證或 Windows Server 訂用帳戶下，且您想要將權益套用至要移轉的機器，請選取 [是]。 然後按一下 [下一步]  。

    ![目標設定](./media/tutorial-migrate-vmware/target-settings.png)

11. 請檢閱 **計算** 中的 VM 名稱、大小、OS 磁碟類型和可用性設定 (如果有在上一個步驟中選取)。 VM 必須符合 [Azure 需求](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)。

    - **VM 大小**：如果您使用評估建議，[VM 大小] 下拉式清單會顯示建議的大小。 否則，Azure Migrate 會根據 Azure 訂用帳戶中最接近的相符項來選擇大小。 或者，您可以在 [Azure VM 大小] 中手動選擇大小。 
    - **OS 磁碟**：指定 VM 的 OS (開機) 磁碟。 OS 磁碟是具有作業系統開機載入器和安裝程式的磁碟。 
    - **可用性區域**：指定要使用的可用性區域。
    - **可用性設定組**：指定要使用的可用性設定組。

    > [!NOTE]
    > 如果要為一組虛擬機器選取不同的可用性選項，請移至步驟1，然後在為一組虛擬機器啟動複寫之後，選取不同的可用性選項並重複這些步驟。


 ![VM 計算設定](./media/tutorial-migrate-vmware/compute-settings.png)

12. 在 [磁碟] 中，指定是否應將 VM 磁碟複寫至 Azure，並選取 Azure 中的磁碟類型 (標準 SSD/HDD 或進階受控磁碟)。 然後按一下 [下一步]。
   
    ![顯示 [複製] 對話方塊 [磁碟] 索引標籤的螢幕擷取畫面。](./media/tutorial-migrate-vmware/disks.png)

13. 在 [檢閱並啟動複寫] 中檢閱設定，然後按一下 [複寫] 以啟動伺服器的初始複寫。

> [!NOTE]
> 您可以在複寫開始之前隨時更新複寫設定 ([管理] > [複寫機器])。 在複寫開始後，就無法變更設定。

### <a name="provisioning-for-the-first-time"></a>第一次佈建

如果這是您在專案中複寫的第一個 VM，伺服器移轉會自動在與專案相同的資源群組中佈建這些資源。

- **服務匯流排**：伺服器移轉會使用服務匯流排將複寫協調訊息傳送至設備。
- **閘道儲存體帳戶**：伺服器移轉會使用閘道儲存體帳戶來儲存所複寫 VM 的相關狀態資訊。
- **記錄儲存體帳戶**︰Azure Migrate 設備會將 VM 的複寫記錄上傳至記錄儲存體帳戶。 Azure Migrate 會將複寫資訊套用到複本受控磁碟。
- **金鑰保存庫**：Azure Migrate 設備會使用金鑰保存庫來管理服務匯流排的連接字串，以及複寫中所用儲存體帳戶的存取金鑰。

## <a name="track-and-monitor"></a>追蹤和監視

1. 在入口網站通知中追蹤作業狀態。
2. 監視複寫狀態，只要按一下 [複寫伺服器] 即可 (位於 [Azure Migrate：**伺服器移轉]** 中)。

     ![監視複寫](./media/tutorial-migrate-vmware/replicating-servers.png)

複寫會如下所示進行︰
- 當「啟動複寫」作業順利完成後，機器就會開始進行對 Azure 的初始複寫。
- 在初始複寫期間，系統會建立 VM 快照集。 快照集內的磁碟資料會複寫至 Azure 中的複本受控磁碟。
- 初始複寫完成後，就會開始進行差異複寫。 對內部部署磁碟的累加變更會定期複寫至 Azure 中的複本磁碟。

## <a name="run-a-test-migration"></a>執行測試移轉


在差異複寫開始後，您可以在執行對 Azure 的完整移轉之前，為 VM 執行測試移轉。 強烈建議您對每個機器都至少執行一次此測試，然後再遷移機器。

- 執行測試移轉會檢查移轉是否將如預期運作，且不會影響到內部部署機器；機器仍可運作，並繼續進行複寫。 
- 測試移轉會使用複寫的資料建立 Azure VM，來模擬移轉 (通常會移轉至 Azure 訂用帳戶中的非生產 VNet)。
- 您可以使用複寫的測試 Azure VM 來驗證移轉、執行應用程式測試以及解決任何問題，然後再進行完整移轉。

依照下列方式執行測試移轉：


1. 在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器移轉]** 中，按一下 [測試遷移的伺服器]。

     ![測試遷移的伺服器](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. 以滑鼠右鍵按一下要測試的 VM，然後按一下 [測試遷移]。

    ![測試移轉](./media/tutorial-migrate-vmware/test-migrate.png)

3. 在 [測試移轉] 中，選取 Azure VM 在移轉後將位於其中的 Azure VNet。 建議您使用非生產 VNet。
4. **測試移轉** 作業隨即啟動。 請在入口網站通知中監視作業。
5. 移轉完成之後，請在 Azure 入口網站的 [虛擬機器] 中檢視已遷移的 Azure VM。 機器名稱會具有尾碼 **-Test**。
6. 測試完成之後，以滑鼠右鍵按一下 [複寫機器] 中的 Azure VM，然後按一下 [清除測試移轉]。

    ![清除移轉](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>移轉 VM

確認測試移轉如預期運作之後，您就可以遷移內部部署機器。

1. 在 [Azure Migrate 專案] > [伺服器] >  **[Azure Migrate：伺服器移轉]** 中，按一下 [複寫伺服器]。

    ![複寫伺服器](./media/tutorial-migrate-vmware/replicate-servers.png)

2. 在 [複寫機器] 中，以滑鼠右鍵按一下 VM > [遷移]。
3. 在 [遷移] > [將虛擬機器關機，在沒有資料遺失的情況下執行計劃性移轉] 中，選取 [是] > [確定]。
    - 根據預設，Azure Migrate 會關閉內部部署 VM，並執行隨選複寫，以同步處理上次複寫執行後發生的任何 VM 變更。 這樣可確保不會遺失任何資料。
    - 如果您不想關閉 VM，請選取 [否]
4. VM 會啟動移轉作業。 請在 Azure 通知中追蹤該作業。
5. 作業完成後，您可以從 [虛擬機器] 頁面檢視及管理 VM。

## <a name="complete-the-migration"></a>完成移轉

1. 在移轉完成後，以滑鼠右鍵按一下 VM > [停止複寫]。 這會停止內部部署機器的複寫，並清除 VM 的複寫狀態資訊。
2. 如果電腦採用 Linux OS，請在移轉的電腦上安裝 Azure VM [Linux](../virtual-machines/extensions/agent-linux.md) 代理程式。 我們會在遷移期間自動安裝適用於 Windows VM 的 VM 代理程式。
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
-  可考慮部署 [Azure 成本管理](../cost-management-billing/cloudyn/overview.md)來監視資源使用情況和花費。


## <a name="next-steps"></a>後續步驟

調查 Azure 雲端採用架構中的[雲端移轉旅程](/azure/architecture/cloud-adoption/getting-started/migrate)。