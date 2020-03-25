---
title: 在 Azure Site Recovery 中設定 Hyper-V VM 到 Azure 的容錯移轉
description: 了解如何使用 Azure Site Recovery 將 Hyper-V VM 容錯移轉至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 03826abf6da94859c510f4c127dfce035aa79370
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498168"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>將 Hyper-V VM 容錯移轉至 Azure

本文將說明了解如何使用 [Azure Site Recovery ](site-recovery-overview.md)將 Hyper-V VM 容錯移轉至 Azure。 在容錯移轉之後，您可以容錯回復至可用的內部部署網站。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 確認要檢查的 Hyper-V VM 屬性符合 Azure 需求。
> * 將特定 VM 容錯移轉至 Azure。


此教學課程是本系列的第五個教學課程。 其內容假設您已經完成了先前教學課程中的工作。    

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. 設定適用於 [Hyper-V VM](tutorial-hyper-v-to-azure.md)，或適用於 [System Center VMM 雲端中所管理的 Hyper-V VM](tutorial-hyper-v-vmm-to-azure.md) 的災害復原
4. [執行災害復原演練](tutorial-dr-drill-azure.md)

[了解](failover-failback-overview.md#types-of-failover)不同類型的容錯移轉。 如果您想要容錯移轉復原方案中的多個 VM，請參閱[本文](site-recovery-failover.md)。

## <a name="prepare-for-failover"></a>準備容錯移轉 
請確認 VM 上有沒有快照集，且在容錯回復期間已將內部部署 VM 關閉。 這有助於確保資料在複寫期間的一致性。 請勿在容錯回復期間開啟內部部署 VM。 

容錯移轉和容錯回復有三個階段：

1. **容錯移轉至 Azure**：從內部部署網站將 Hyper-V VM 容錯移轉至 Azure。
2. **容錯回復至內部部署**：在內部部署網站可供使用時，將 Azure VM 容錯回復至內部部署網站。 它會開始將資料從 Azure 同步處理至內部部署，並在完成時，於內部部署上啟動 VM。  
3. **反向複寫內部部署 VM**：容錯回復至內部部署之後，請反向複寫內部部署 VM，以開始將這些 VM 複寫至 Azure。

## <a name="verify-vm-properties"></a>驗證 VM 屬性

在容錯移轉之前，請驗證 VM 屬性，並確定 VM 符合 [Azure 需求](hyper-v-azure-support-matrix.md#replicated-vms)。

在 [受保護的項目]  中，按一下 [複寫的項目]  > VM。

1. 在 [複寫的項目]  窗格中，將會呈現 VM 資訊、健康情況狀態，以及最新可用復原點的摘要。 如需檢視詳細資訊，請按一下 [屬性]  。

1. 在 [計算與網路]  中，您可以修改 Azure 的名稱、資源群組、目標大小、[可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)，及管理磁碟設定。

1. 您可以檢視及修改網路設定，包括在容錯移轉後 Azure VM 所在的網路/子網路，以及要指派給它的 IP 位址。

1. 在 [磁碟]  中，您可以看見 VM 上作業系統和資料磁碟的相關資訊。

## <a name="fail-over-to-azure"></a>容錯移轉至 Azure

1. 在 [設定]   > [複寫的項目]  中，按一下 [VM] > [容錯移轉]  。
2. 在 [容錯移轉]  中，選取 [最新的]  復原點。 
3. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)  。 Site Recovery 會嘗試先關閉來源 VM 後，再觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。
4. 驗證容錯移轉之後，請按一下 [認可]  。 這會刪除所有可用的復原點。

> [!WARNING]
> **請勿取消進行中的容錯移轉**：如果您在進行時取消，容錯移轉會停止，但 VM 不會再次複寫。

## <a name="connect-to-failed-over-vm"></a>連線至容錯移轉的 VM

1. 如果您在容錯移轉後想要使用遠端桌面通訊協定 (RDP) 和安全殼層 (SSH) 連線至 Azure VM，請[確認已符合需求](failover-failback-overview.md#connect-to-azure-after-failover)。
2. 容錯移轉之後，請移至 VM 並[連線](../virtual-machines/windows/connect-logon.md)至該 VM 進行驗證。
3. 如果您想要在容錯移轉後使用不同的復原點，請使用 [變更復原點]  。 在下一個步驟中認可容錯移轉之後，將無法再使用此選項。
4. 在驗證之後，選取 [認可]  以完成容錯移轉後的 VM 復原點。
5. 在認可之後，系統會刪除所有其他可用的復原點。 此步驟會完成容錯移轉作業。

>[!TIP]
> 如果您在容錯移轉後遇到任何連線問題，請遵循[疑難排解指南](site-recovery-failover-to-azure-troubleshoot.md)。


## <a name="next-steps"></a>後續步驟

容錯移轉後，請重新保護 Azure VM，使其能從 Azure 複寫到內部部署環境。 然後，在 VM 受到重新保護並複寫到內部部署網站後，就會在您準備就緒時從 Azure 容錯回復。
