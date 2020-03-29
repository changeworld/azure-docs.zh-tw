---
title: 為具有網站恢復的物理伺服器設置容錯移轉和容錯移轉
description: 了解如何將實體伺服器容錯移轉到 Azure，然後容錯回復到內部部署網站，以使用 Azure Site Recovery 進行災害復原
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497867"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>將複寫的實體伺服器容錯移轉及容錯回復至 Azure

本教程介紹如何通過[Azure 網站恢復](site-recovery-overview.md)將複製到 Azure 的本地物理伺服器進行容錯移轉。 容錯移轉後，在可用時從 Azure 故障回本地網站。

## <a name="before-you-start"></a>開始之前

- [瞭解](failover-failback-overview.md)災害復原中的容錯移轉過程。
- 如果要容錯移轉多台電腦，[瞭解如何](recovery-plan-overview.md)在恢復計畫中將電腦集中在一起。
- 在進行完全容錯移轉之前，運行[災害復原演練](site-recovery-test-failover-to-azure.md)以確保一切按預期工作。
- 按照[這些說明](site-recovery-failover.md#prepare-to-connect-after-failover)準備在容錯移轉後連接到 Azure VM。



## <a name="run-a-failover"></a>執行容錯移轉

### <a name="verify-server-properties"></a>驗證伺服器屬性

驗證伺服器屬性，並確定伺服器符合 Azure VM 的 [Azure 需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 在 [受保護的項目]**** 中，按一下 [複寫的項目]****，然後選取機器。
2. 在 [複寫的項目]**** 窗格中，將會呈現機器資訊、健康情況狀態，以及最新可用復原點的摘要。 如需檢視詳細資訊，請按一下 [屬性]****。
3. 在**計算和網路**中，可以修改 Azure 名稱、資源組、目標大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和託管磁片設置
4. 您可以檢視及修改網路設定，包括在容錯移轉後 Azure VM 所在的網路/子網路，以及要指派給它的 IP 位址。
5. 在 [磁碟]**** 中，您可以看見機器作業系統和資料磁碟的相關資訊。

### <a name="fail-over-to-azure"></a>容錯移轉至 Azure

1. 在 **"設置** > **複製專案"** 中，按一下電腦>**容錯移轉**。
2. 在 [容錯移轉]**** 中，選取容錯移轉的目標**復原點**。 您可以使用下列其中一個選項：
   - **最新**：此選項會先處理所有傳送至 Site Recovery 的資料。 它會提供最低的 RPO (復原點目標)，因為在容錯移轉後建立的 Azure VM 具有在觸發容錯移轉時複寫到 Site Recovery 的所有資料。
   - **最近處理**：此選項會將機器容錯移轉到 Site Recovery 所處理的最新復原點。 此選項提供低 RTO (復原時間目標)，因為無須花費時間處理未處理的資料。
   - **最新應用程式一致**：此選項會將機器容錯移轉到 Site Recovery 所處理的最近應用程式一致復原點。
   - **自訂**：指定任何復原點。

3. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源 VM 關機，請選取 [先將機器關機再開始容錯移轉]****。 即使關機失敗，仍會繼續容錯移轉。 您可以按照 **"作業**"頁上的容錯移轉進度進行操作。
4. 如果您已準備好連線到 Azure VM，請進行連線以在容錯移轉之後加以驗證。
5. 驗證之後，請 [認可]**** 容錯移轉。 這會刪除所有可用的復原點。

> [!WARNING]
> 請勿取消正在進行中的容錯移轉。 容錯移轉開始之前，就會停止機器複寫。 如果您取消容錯移轉，容錯移轉會隨即停止，但機器不會重新複寫一次。
> 若是實體伺服器，其他的容錯移轉可能需要處理約 8 到 10 分鐘才能完成。

## <a name="automate-actions-during-failover"></a>容錯移轉期間自動執行操作

您可能希望在容錯移轉期間自動執行操作。 為此，可以在恢復計畫中使用腳本或 Azure 自動化 Runbook。

- [瞭解如何](site-recovery-create-recovery-plans.md)創建和自訂恢復計畫，包括添加腳本。
- [瞭解](site-recovery-runbook-automation.md)將 Azure 自動化運行簿添加到恢復計畫。

## <a name="configure-settings-after-failover"></a>容錯移轉後配置設置

容錯移轉後，需要[配置 Azure 設置](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover)以連接到複製的 Azure VM。 此外，設置[內部和公共](site-recovery-failover.md#set-up-ip-addressing)IP 定址。

## <a name="prepare-for-reprotection-and-failback"></a>準備重新保護和故障恢復

容錯移轉到 Azure 後，通過將 Azure VM 複製到本地網站來重新保護 Azure VM。 然後，在複製它們之後，可以通過運行從 Azure 到本地網站的容錯移轉，將它們容錯移轉回本地網站。

1. 使用站台復原複寫至 Azure 的實體伺服器，僅可以容錯回復為 VMware VM。 您需要 VMware VM 基礎結構，才能容錯回復。 按照[本文](vmware-azure-prepare-failback.md)中的步驟準備重新保護和故障倒回，包括在 Azure 中設置進程伺服器和本地主目標伺服器，以及配置網站到網站 VPN 或 ExpressRoute 專用對等互連以進行故障倒回。
2. 確保本地佈建服務器正在運行並連接到 Azure。 在容錯移轉到 Azure 期間，本地網站可能無法訪問，並且佈建服務器可能不可用或關閉。 在故障償還期間，VM 必須存在於佈建服務器資料庫中。 否則，將無法成功容錯回復。
3. 刪除本地主目標伺服器上的任何快照。 如果有快照，重新保護不起作用。  在重新保護作業期間，VM 上的快照將自動合併。
4. 如果要重新保護收集到複製組中的 VM 以實現多 VM 一致性，請確保它們都具有相同的作業系統（Windows 或 Linux），並確保部署的主目標伺服器具有相同的作業系統類型。 複製組中的所有 VM 都必須使用相同的主目標伺服器。
5. 打開故障恢復[所需的埠](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback)。
6. 確保 vCenter 伺服器在故障倒回之前已連接。 否則，將磁碟中斷連線並將它們重新連結至虛擬機器的作業會失敗。
7. 如果 vCenter 伺服器管理要故障返回的 VM，請確保您具有所需的許可權。 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，保護會成功且容錯回復可作用。 但是，在重新保護期間，容錯移轉失敗，因為無法發現資料存儲，並且在重新保護期間未列出。 要解決此問題，可以使用[適當的帳戶/許可權](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)更新 vCenter 憑據，然後重試作業。 
8. 如果使用範本創建虛擬機器，請確保每個 VM 都有其磁片的 UUID。 如果本地 VM UUID 與主目標伺服器的 UUID 衝突，因為兩者都是從同一範本創建的，則重新保護將失敗。 從其他範本進行部署。
9. 如果故障返回備用 vCenter 伺服器，請確保發現新的 vCenter 伺服器和主目標伺服器。 通常，如果資料存儲不是無法訪問的，或者在**Reprotect**中不可見。
10. 驗證以下無法故障返回的方案：
    - 如果您使用的是 ESXi 5.5 免費版或 vSphere 6 虛擬機器管理程式免費版。 升級到其他版本。
    - 如果您有 Windows 伺服器 2008 R2 SP1 物理伺服器。
    - [已遷移](migrate-overview.md#what-do-we-mean-by-migration)的 VM。
    - 已移動到其他資源組的 VM。
    - 已刪除的副本 Azure VM。
    - 不受保護的副本 Azure VM（複製到本地網站）。
10. [查看可以使用的故障恢復類型](concepts-types-of-failback.md)- 原始位置恢復和備用位置恢復。


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>將 Azure VM 重新保護到備用位置

此過程假定本地 VM 不可用。

1. 在"**設置** > **複製項**>保存庫中，按右鍵在>**重新保護**上出現故障的電腦。
2. 在 [重新保護]**** 中，確認已選取 [Azure 到內部部署]****。
3. 指定內部部署主要目標伺服器，以及處理序伺服器。
4. 在 [資料存放區]**** 中，選取您要將內部部署磁碟復原至的主要目標資料存放區。
       - 如果本地 VM 已刪除或不存在，並且需要創建新磁片，請使用此選項。
       - 如果磁片已存在，則忽略此設置，但確實需要指定值。
5. 選取主要目標保留磁碟機。 系統會自動選取容錯回復原則。
6. 按一下 [確定]**** 以開始重新保護。 作業開始將 Azure VM 複製到本地網站。 您可以在 [作業] **** 索引標籤上追蹤進度。

> [!NOTE]
> 若要將 Azure VM 復原到現有的內部部署 VM，請在主要目標伺服器的 ESXi 主機上掛接內部部署虛擬機器的資料存放區 (具有讀寫權限)。


## <a name="fail-back-from-azure"></a>從 Azure 容錯移轉

執行容錯移轉，如下所示：

1. 在 [已複寫的項目]**** 頁面上，以滑鼠右鍵按一下機器 > [非計劃性容錯移轉]****。
2. 在 [確認容錯移轉]**** 中，確認容錯移轉方向是從 Azure。
3.選擇要用於容錯移轉的復原點。
    - 我們建議您使用**最新的**復原點。 應用一致性點落後于最新時間點，並導致一些資料丟失。
    - **最新**是一個崩潰一致的復原點。
    - 執行容錯移轉時，Site Recovery 會關閉 Azure VM，並啟動內部部署 VM。 機器將會停機一段時間，所以請選擇適當的時間。
4. 在機器上按一下滑鼠右鍵，然後按一下 [認可]****。 這會觸發可移除 Azure VM 的作業。
5. 確認 Azure VM 已如預期般關閉。


## <a name="reprotect-on-premises-machines-to-azure"></a>將內部部署機器放到 Azure 重新保護

資料現在應該回到內部部署網站上，但不會複寫至 Azure。 您可以再次開始複寫至 Azure，如下所示：

1. 在保存庫 > [設定]**** > > [複寫項目]**** 中，選取已容錯回復的 VM，然後按一下 [重新保護]****。
2. 選取用於將複寫的資料傳送至 Azure 的處理序伺服器，然後按一下 [確定]****。


## <a name="next-steps"></a>後續步驟

重新保護作業完成後，本地 VM 將複製到 Azure。 根據需要，可以[運行另一個容錯移轉](site-recovery-failover.md)到 Azure。
