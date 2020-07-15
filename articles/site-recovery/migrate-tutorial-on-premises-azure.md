---
title: 透過 Azure Site Recovery 遷移內部部署機器
description: 本文說明如何使用 Azure Site Recovery 將內部部署機器移轉至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ccf83bacedb667e52e9865b6d451641faa0ac414
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131190"
---
# <a name="migrate-on-premises-machines-to-azure"></a>將內部部署機器移轉至 Azure


本文說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 將內部部署機器移轉至 Azure。 

> [!TIP]
> 您現在應該使用 Azure Migrate 將內部部署機器遷移至 Azure，而不是 Azure Site Recovery 服務。 [深入了解](../migrate/migrate-services-overview.md)。


本教學課程說明如何將內部部署 VM 和實體伺服器移轉至 Azure。 您會了解如何：

> [!div class="checklist"]
> * 設定移轉的來源和目標環境
> * 設定複寫原則
> * 啟用複寫
> * 執行測試移轉，確定一切都沒問題
> * 執行一次性容錯移轉至 Azure


> [!TIP]
> 您現在可以使用 Azure Migrate 服務，從內部部署伺服器遷移至 Azure。 [深入了解](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>開始之前

不支援並行虛擬驅動程式所匯出的裝置。


## <a name="prepare-azure-and-on-premises"></a>準備 Azure 與內部部署環境

1. 依照[這篇文章](tutorial-prepare-azure.md)所述準備 Azure。 雖然這篇文章說明災害復原的準備步驟，但這些步驟也適用於移轉。
2. 準備內部部署 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 或 [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) 伺服器。 如果您要遷移實體機器，您不需要做任何準備。 只需確認[支援矩陣](vmware-physical-azure-support-matrix.md)。


## <a name="select-a-protection-goal"></a>選取保護目標

選取您要複寫的項目以及您要複寫到的位置。
1. 按一下 [復原服務保存庫]  > 保存庫。
2. 在 [資源功能表] 中，按一下 [Site Recovery]   > [準備基礎結構]   > [保護目標]  。
3. 在 [保護目標]  中，選取您需要移轉的項目。
    - **VMware**：選取 [至 Azure]   > [是，使用 VMware vSphere Hypervisor]  。
    - **實體機器**：選取 [至 Azure]   > [未虛擬化/其他]  。
    - **Hyper-V**：選取 [至 Azure]   > [是，使用 Hyper-V]  。 如果 Hyper-V VM 是由 VMM 管理，請選取 [是]  。


## <a name="set-up-the-source-environment"></a>設定來源環境

**案例** | **詳細資料**
--- | --- 
VMware | 設定[來源環境](vmware-azure-set-up-source.md)，以及設定[組態伺服器](vmware-azure-deploy-configuration-server.md)。
實體機器 | [設定](physical-azure-set-up-source.md)來源環境與組態伺服器。
Hyper-V | 設定[來源環境](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> 針對使用 System Center VMM 部署的 Hyper-V 設定[來源環境](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment)。

## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。

1. 按一下 [準備基礎結構]   > [目標]  ，然後選取您要使用的 Azure 訂用帳戶。
2. 指定 Resource Manager 部署模型。
3. Site Recovery 會檢查 Azure 資源。
    - 如果您要遷移 VMware VM 或實體伺服器，Site Recovery 會確認您是否有 Azure 網路以作為容錯移轉後所建立 Azure VM 的所在位置。
    - 如果您要遷移 Hyper-V VM，Site Recovery 會確認您是否有相容的 Azure 儲存體帳戶和網路。
4. 如果您要遷移 System Center VMM 所管理的 Hyper-V VM，設定[網路對應](hyper-v-vmm-azure-tutorial.md#configure-network-mapping)。

## <a name="set-up-a-replication-policy"></a>設定複寫原則

**案例** | **詳細資料**
--- | --- 
VMware | 設定 VMware VM 的[複寫原則](vmware-azure-set-up-replication.md)。
實體機器 | 設定實體機器的[複寫原則](physical-azure-disaster-recovery.md#create-a-replication-policy)。
Hyper-V | 設定[複寫原則](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> 針對使用 System Center VMM 部署的 Hyper-V 設定[複寫原則](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy)。

## <a name="enable-replication"></a>啟用複寫

**案例** | **詳細資料**
--- | --- 
VMware | [啟用 VMware VM 的複寫](vmware-azure-enable-replication.md)。
實體機器 | 針對實體機器[啟用複寫功能](physical-azure-disaster-recovery.md#enable-replication)。
Hyper-V | [啟用複寫](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> 針對使用 System Center VMM 部署的 Hyper-V [啟用複寫功能](hyper-v-vmm-azure-tutorial.md#enable-replication)。


## <a name="run-a-test-migration"></a>執行測試移轉

執行[測試容錯移轉](tutorial-dr-drill-azure.md)至 Azure，確定一切都沒問題。


## <a name="migrate-to-azure"></a>移轉至 Azure

針對您要移轉的機器執行容錯移轉。

1. 在 [設定]   > [複寫的項目]  中，按一下機器 > [容錯移轉]  。
2. 在 [容錯移轉]  中，選取容錯移轉的目標**復原點**。 選取最新的復原點。
3. 加密金鑰設定不適用於此案例。
4. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)  。 Site Recovery 在觸發容錯移轉之前，會嘗試將虛擬機器關機。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。
5. 確認 Azure VM 如預期般出現在 Azure 中。
6. 在 [複寫的項目]  中，以滑鼠右鍵按一下 VM > [完成移轉]  。 這會執行以下動作：

   - 完成移轉程序、停止內部部署 VM 的複寫，並停止 VM 的 Site Recovery 計費。
   - 此步驟會清除複寫資料。 但並不會刪除已遷移的 VM。

     ![完成移轉](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **請勿取消正在進行中的容錯移轉**：在容錯移轉開始之前，VM 複寫已停止。 如果您取消正在進行的容錯移轉，容錯移轉會停止，但 VM 不會再次複寫。

在某些情況下，容錯移轉需要額外的處理，這會耗費約 8 到 10 分鐘的時間來完成。 您可能注意到下列項目的測試容錯移轉時間較久：實體伺服器、VMware Linux 機器、未啟用 DHCP 服務的 VMware VM，以及沒有下列開機驅動程式的 VMware VM：storvsc、vmbus、storflt、intelide、atapi。

## <a name="after-migration"></a>移轉之後

將機器移轉至 Azure 之後，有一些您應該完成的步驟。

藉由使用[復原方案](site-recovery-runbook-automation.md)中的內建自動化指令碼功能，可將一些步驟自動化而成為移轉程序的一部分   


### <a name="post-migration-steps-in-azure"></a>Azure 中的移轉後步驟

- 執行任何移轉後應用程式調整，例如更新資料庫連接字串和 Web 伺服器設定。 
- 在現在於 Azure 中執行的已移轉應用程式上，執行最終的應用程式和移轉接受度測試。
- [Azure VM 代理程式](../virtual-machines/extensions/agent-windows.md)可管理 VM 與「Azure 網狀架構控制器」之間的互動。 它對某些 Azure 服務 (例如 Azure 備份、Site Recovery 及 Azure 安全性) 來說是必要的。
    - 如果您要移轉 VMware 機器和實體伺服器，「行動服務」安裝程式就會在 Windows 機器上安裝可用的 Azure VM 代理程式。 在 Linux VM 上，建議您在容錯移轉後安裝代理程式。
    - 如果您要將 Azure VM 移轉至次要區域，就必須在移轉前將 Azure VM 代理程式佈建在 VM 上。
    - 如果您要將 Hyper-V VM 移轉至 Azure，請在移轉後將 Azure VM 代理程式安裝在 Azure VM 上。
- 請從 VM 中手動移除任何 Site Recovery 提供者/代理程式。 如果您遷移 VMware VM 或實體伺服器，請將行動服務從 VM 解除安裝。
- 針對提升復原能力：
    - 使用「Azure 備份」服務來備份 Azure VM 以維護資料安全。 [深入了解](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 將 Azure VM 複寫至次要區域，讓工作負載保持執行且持續可供使用。 [深入了解](azure-to-azure-quickstart.md)。
- 針對提升安全性：
    - 使用「Azure 資訊安全中心」[Just-In-Time 系統管理](../security-center/security-center-just-in-time.md)來鎖定並限制輸入流量存取
    - 使用[網路安全性群組](../virtual-network/security-overview.md)來限制傳送至管理端點的網路流量。
    - 部署 [Azure 磁碟加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)以協助保護磁碟，以及防止資料遭到竊取和受到未經授權的存取。
    - 深入了解如何[保護 IaaS 資源]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ )，並瀏覽 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/ )。
- 針對監視及管理：
    - 可考慮部署 [Azure 成本管理](../cost-management-billing/cloudyn/overview.md)來監視資源使用情況和花費。

### <a name="post-migration-steps-on-premises"></a>內部部署環境中的移轉後步驟

- 將應用程式流量移至在已移轉之 Azure VM 執行個體上執行的應用程式。
- 從您的本機 VM 清查中移除內部部署 VM。
- 從本機備份中移除內部部署 VM。
- 更新任何內部文件，以顯示 Azure VM 的新位置和 IP 位址。




## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將內部部署 VM 移轉至 Azure VM。 Now

> [!div class="nextstepaction"]
> [設定災害復原](azure-to-azure-replicate-after-migration.md)至 Azure VM 的次要 Azure 區域。

  
