---
title: 使用 Site Recovery 設定實體伺服器的容錯移轉和容錯回復
description: 了解如何將實體伺服器容錯移轉到 Azure，然後容錯回復到內部部署網站，以使用 Azure Site Recovery 進行災害復原
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: 2994f68e4159c7c4aa7d82bef7a5891deb5055a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292812"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>將複寫的實體伺服器容錯移轉及容錯回復至 Azure

本教學課程說明如何容錯移轉使用 [Azure Site Recovery](site-recovery-overview.md)複寫到 Azure 的內部部署實體伺服器。 在容錯移轉之後，您可以從 Azure 容錯回復至內部部署網站（如果有的話）。

## <a name="before-you-start"></a>開始之前

- [瞭解](failover-failback-overview.md) 嚴重損壞修復中的容錯移轉程式。
- 如果您想要容錯移轉多部機器，請 [瞭解](recovery-plan-overview.md) 如何在復原方案中一起收集機器。
- 執行完整容錯移轉之前，請執行嚴重損壞 [修復演練](site-recovery-test-failover-to-azure.md) ，以確定一切都如預期般運作。
- 請遵循 [下列指示](site-recovery-failover.md#prepare-to-connect-after-failover) ，以準備在容錯移轉後連線至 Azure vm。



## <a name="run-a-failover"></a>執行容錯移轉

### <a name="verify-server-properties"></a>驗證伺服器屬性

驗證伺服器屬性，並確定伺服器符合 Azure VM 的 [Azure 需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 在 [受保護的項目]**** 中，按一下 [複寫的項目]****，然後選取機器。
2. 在 [複寫的項目]**** 窗格中，將會呈現機器資訊、健康情況狀態，以及最新可用復原點的摘要。 如需檢視詳細資訊，請按一下 [屬性]****。
3. 在 [ **計算和網路**] 中，您可以修改 Azure 名稱、資源群組、目標大小、 [可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)和受控磁片設定
4. 您可以檢視及修改網路設定，包括在容錯移轉後 Azure VM 所在的網路/子網路，以及要指派給它的 IP 位址。
5. 在 [磁碟]**** 中，您可以看見機器作業系統和資料磁碟的相關資訊。

### <a name="fail-over-to-azure"></a>容錯移轉至 Azure

1. 在 [設定]   > [複寫的項目]  中，按一下機器 > [容錯移轉]  。
2. 在 [容錯移轉]  中，選取容錯移轉的目標**復原點**。 您可以使用下列其中一個選項：
   - **最新**：此選項會先處理所有傳送至 Site Recovery 的資料。 它會提供最低的 RPO (復原點目標)，因為在容錯移轉後建立的 Azure VM 具有在觸發容錯移轉時複寫到 Site Recovery 的所有資料。
   - **最近處理**：此選項會將機器容錯移轉到 Site Recovery 所處理的最新復原點。 此選項提供低 RTO (復原時間目標)，因為無須花費時間處理未處理的資料。
   - **最新應用程式一致**：此選項會將機器容錯移轉到 Site Recovery 所處理的最近應用程式一致復原點。
   - **自訂**：指定任何復原點。

3. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源 VM 關機，請選取 [先將機器關機再開始容錯移轉]****。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。
4. 如果您已準備好連線到 Azure VM，請進行連線以在容錯移轉之後加以驗證。
5. 驗證之後，請 [認可]**** 容錯移轉。 這會刪除所有可用的復原點。

> [!WARNING]
> 請勿取消正在進行中的容錯移轉。 容錯移轉開始之前，就會停止機器複寫。 如果您取消容錯移轉，容錯移轉會隨即停止，但機器不會重新複寫一次。
> 若是實體伺服器，其他的容錯移轉可能需要處理約 8 到 10 分鐘才能完成。

## <a name="automate-actions-during-failover"></a>在容錯移轉期間自動執行動作

您可能會想要在容錯移轉期間自動執行動作。 若要這樣做，您可以使用腳本或復原方案中的 Azure 自動化 runbook。

- [瞭解](site-recovery-create-recovery-plans.md) 如何建立和自訂復原方案，包括新增腳本。
- [瞭解](site-recovery-runbook-automation.md) 將 Azure 自動化 runbook 新增至復原方案的相鄰。

## <a name="configure-settings-after-failover"></a>在容錯移轉之後設定設定

容錯移轉之後，您必須 [設定 azure 設定](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) 以連線到複寫的 azure vm。 此外，請設定 [內部和公用](site-recovery-failover.md#set-up-ip-addressing) IP 位址。

## <a name="prepare-for-reprotection-and-failback"></a>準備重新保護和容錯回復

在容錯移轉至 Azure 之後，您可以將 Azure Vm 複寫到內部部署網站，以重新保護這些 Vm。 然後，在複寫之後，您可以執行從 Azure 到內部部署網站的容錯移轉，將它們容錯回復至內部部署。

1. 使用站台復原複寫至 Azure 的實體伺服器，僅可以容錯回復為 VMware VM。 您需要 VMware VM 基礎結構，才能容錯回復。 遵循本文中的 [步驟來準備](vmware-azure-prepare-failback.md) 重新保護和容錯回復，包括在 Azure 中設定進程伺服器，以及內部部署主要目標伺服器，以及設定站對站 VPN 或 ExpressRoute 私人對等互連，以進行容錯回復。
2. 確定內部部署設定伺服器正在執行且已連線到 Azure。 在容錯移轉至 Azure 期間，內部部署網站可能無法存取，而且設定伺服器可能無法使用或已關閉。 在容錯回復期間，VM 必須存在於設定伺服器資料庫中。 否則，將無法成功容錯回復。
3. 刪除內部部署主要目標伺服器上的任何快照集。 如果有快照集，重新保護將無法運作。  VM 上的快照集會在重新保護作業期間自動合併。
4. 如果您要重新保護收集到複寫群組以進行多重 VM 一致性的 Vm，請確定它們都具有相同的作業系統 (Windows 或 Linux) ，並確定您部署的主要目標伺服器具有相同類型的作業系統。 複寫群組中的所有 Vm 都必須使用相同的主要目標伺服器。
5. 開啟容錯回復 [所需的埠](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) 。
6. 確定 vCenter Server 在容錯回復前已連線。 否則，將磁碟中斷連線並將它們重新連結至虛擬機器的作業會失敗。
7. 如果 vCenter 伺服器管理您要容錯回復的 Vm，請確定您擁有必要的許可權。 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，保護會成功且容錯回復可作用。 不過，在重新保護期間，容錯移轉會失敗，因為無法探索資料存放區，也不會在重新保護期間列出。 若要解決此問題，您可以使用 [適當的帳戶/許可權](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)來更新 vCenter 認證，然後重試此作業。 
8. 如果您使用範本來建立虛擬機器，請確定每個 VM 都有自己的磁片 UUID。 如果內部部署 VM 與主要目標伺服器的 UUID 衝突（因為兩者都是從相同的範本建立），則重新保護會失敗。 從不同的範本部署。
9. 如果您要容錯回復至替代的 vCenter Server，請確定已探索新的 vCenter Server 和主要目標伺服器。 一般來說，如果它們不是無法存取的資料存放區，或在重新保護時看不**到。**
10. 確認您無法容錯回復的下列案例：
    - 如果您使用的是 ESXi 5.5 免費版或 vSphere 6 管理程式免費版。 升級至不同的版本。
    - 如果您有 Windows Server 2008 R2 SP1 實體伺服器。
    - 已遷移的 Vm。
    - 已移至另一個資源群組的 VM。
    - 已刪除的複本 Azure VM。
    - 不受保護 (複寫到內部部署網站) 的複本 Azure VM。
10. 請參閱您可以使用的容錯[回復類型](concepts-types-of-failback.md)-原始位置復原和替代位置復原。


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>將 Azure Vm 重新保護到替代位置

此程式假設無法使用內部部署 VM。

1. 在保存庫 >**設定**  >  **已**複寫的專案] 中，以滑鼠右鍵按一下已容錯移轉 >**重新保護**的電腦。
2. 在 [重新保護]**** 中，確認已選取 [Azure 到內部部署]****。
3. 指定內部部署主要目標伺服器，以及處理序伺服器。
4. 在 [資料存放區]**** 中，選取您要將內部部署磁碟復原至的主要目標資料存放區。
       - 如果內部部署 VM 已刪除或不存在，且您需要建立新的磁片，請使用此選項。
       - 如果磁片已存在，則會忽略此設定，但您必須指定一個值。
5. 選取主要目標保留磁碟機。 系統會自動選取容錯回復原則。
6. 按一下 [確定]**** 以開始重新保護。 作業會開始將 Azure VM 複寫到內部部署網站。 您可以在 [作業] **** 索引標籤上追蹤進度。

> [!NOTE]
> 若要將 Azure VM 復原到現有的內部部署 VM，請在主要目標伺服器的 ESXi 主機上掛接內部部署虛擬機器的資料存放區 (具有讀寫權限)。


## <a name="fail-back-from-azure"></a>從 Azure 容錯移轉

執行容錯移轉，如下所示：

1. 在 [已複寫的項目]**** 頁面上，以滑鼠右鍵按一下機器 > [非計劃性容錯移轉]****。
2. 在 [確認容錯移轉]**** 中，確認容錯移轉方向是從 Azure。
3. 選取您要用於容錯移轉的復原點。
    - 建議您使用 **最新** 的復原點。 應用程式一致的點位於最近的時間點後方，而且會導致資料遺失。
    - **最新** 的是損毀一致復原點。
    - 執行容錯移轉時，Site Recovery 會關閉 Azure VM，並啟動內部部署 VM。 機器將會停機一段時間，所以請選擇適當的時間。
4. 在機器上按一下滑鼠右鍵，然後按一下 [認可]****。 這會觸發可移除 Azure VM 的作業。
5. 確認 Azure VM 已如預期般關閉。


## <a name="reprotect-on-premises-machines-to-azure"></a>將內部部署機器放到 Azure 重新保護

資料現在應該回到內部部署網站上，但不會複寫至 Azure。 您可以再次開始複寫至 Azure，如下所示：

1. 在保存庫 > [設定]**** > > [複寫項目]**** 中，選取已容錯回復的 VM，然後按一下 [重新保護]****。
2. 選取用於將複寫的資料傳送至 Azure 的處理序伺服器，然後按一下 [確定]****。


## <a name="next-steps"></a>接下來的步驟

重新保護作業完成後，內部部署 VM 會複寫至 Azure。 您可以視需要 [執行另一個容錯移轉](site-recovery-failover.md) 至 Azure。
