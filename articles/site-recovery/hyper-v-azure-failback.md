---
title: 使用 Azure Site Recovery 從 Azure 容錯回復 Hyper-v Vm
description: 如何使用 Azure Site Recovery 將 Hyper-v Vm 從 Azure 容錯回復至內部部署網站。
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281778"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>執行 Hyper-V VM 的容錯回復

本文說明如何將 Hyper-v Vm 從內部部署網站容錯移轉至 Azure 之後所建立的 Azure Vm 容錯回復，並[Azure Site Recovery](site-recovery-overview.md)。

- 從 azure 執行規劃的容錯移轉至內部部署網站，將 Hyper-v Vm 從 Azure 容錯回復。 如果容錯移轉方向是從 Azure 到內部部署，則會將其視為容錯回復。
- 因為 Azure 是高可用性環境，而且 Vm 一律可供使用，所以從 Azure 容錯回復是規劃的活動。 您可以規劃短暫的停機時間，讓工作負載可以再次開始在內部部署中執行。 
- 規劃的容錯回復會關閉 Azure 中的 Vm，並下載最新的變更。 不需要遺失資料。

## <a name="before-you-start"></a>開始之前

1. 請參閱您可以使用的容錯[回復類型](failover-failback-overview.md#hyper-v-reprotectionfailback)-原始位置復原和替代位置復原。
2. 請確定 Azure Vm 使用的是儲存體帳戶，而非受控磁片。 不支援使用受控磁片複寫的 Hyper-v Vm 容錯回復。
3. 檢查內部部署 Hyper-v 主機（或如果您使用搭配 Site Recovery 的 System Center VMM 伺服器）是否正在執行並已連線至 Azure。 
4. 請確定 Vm 的容錯移轉和認可已完成。 您不需要設定任何特定的 Site Recovery 元件，即可從 Azure 容錯回復 Hyper-v Vm。
5. 完成資料同步處理和啟動內部部署 VM 所需的時間將取決於許多因素。 若要加速資料下載，您可以設定 Microsoft 復原服務代理程式使用更多執行緒來平行處理下載。 [詳細資訊](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage)。


## <a name="fail-back-to-the-original-location"></a>容錯回復至原始位置

若要將 Azure 中的 Hyper-v Vm 容錯回復至原始內部部署 VM，請從 Azure 執行規劃的容錯移轉至內部部署網站，如下所示：

1. 在 [保存庫] > [複寫的**專案**] 中，選取 VM。 以滑鼠右鍵按一下 VM >**規劃的容錯移轉**。 如果您要容錯回復復原方案，請選取方案名稱，然後按一下 [**容錯移轉** > **規劃的容錯移轉**]。
2. 在 [**確認計畫的容錯移轉**] 中，選擇來源和目標位置。 記下容錯移轉方向。 如果從主要容錯移轉的運作正常，且所有虛擬機器都位於次要位置，則只會提供資訊。
3. 在 **[資料同步**處理] 中，選取一個選項：
    - **在容錯移轉前同步處理資料（僅同步處理差異變更）** -這個選項會在不關閉 vm 的情況下進行同步處理時，儘量縮短停機時間。
        - **第1階段**：取得 Azure VM 的快照集，並將其複製到內部部署 hyper-v 主機。 機器會繼續在 Azure 中執行。
        - **第2階段**：關閉 Azure VM，如此一來，就不會發生任何新的變更。 最後一組差異變更會傳送到內部部署伺服器，並啟動內部部署 VM。
    - **僅在容錯移轉期間同步處理資料（完整下載）** -這個選項的速度較快，因為我們假設大部分的磁片都已變更，而不想花時間計算總和檢查碼。 這個選項不會執行任何總和檢查碼計算。
        - 它會下載磁碟。 
        - 如果您已執行 Azure 一段時間（一個月或更多），或如果已刪除內部部署 VM，建議您使用此選項。

4. 僅針對 VMM，如果已針對雲端啟用資料加密，請在 [**加密金鑰**] 中，選取當您在 VMM 伺服器上的提供者安裝期間啟用資料加密時所發出的憑證。
5. 起始容錯移轉。 您可以在 [工作] 索引標籤上追蹤容錯移轉進度。
6. 如果您選取了在容錯移轉前同步處理資料的選項，則在完成初始資料同步處理且您已經準備好關閉 Azure 中的虛擬機器之後，請按一下 **作業** > 作業名稱 >**完成容錯移轉**。 這會執行以下動作：
    - 關閉 Azure 機器。
    - 將最新的變更傳輸至內部部署 VM。
    - 啟動內部部署 VM。
7. 您現在可以登入內部部署 VM 電腦，以檢查它是否如預期般可用。
8. 虛擬機器目前處於認可擱置中的狀態。 按一下 [認可] 以認可容錯移轉。
9. 若要完成容錯回復，請按一下 [**反向**複寫]，再次開始將內部部署 VM 複寫至 Azure。



## <a name="fail-back-to-an-alternate-location"></a>容錯回復至其他位置 

容錯回復至替代位置，如下所示：

1. 如果您要設定新的硬體，請在電腦上安裝[支援的 Windows 版本](hyper-v-azure-support-matrix.md#replicated-vms)和 hyper-v 角色。
2. 使用您在原始伺服器上所擁有的相同名稱來建立虛擬網路交換器。
3. 在 [**受保護的專案** > **保護群組** > \<ProtectionGroupName >-> \<VirtualMachineName >] 中，選取您要容錯回復的 VM，然後選取 [**規劃的容錯移轉**]。
4. 在 [**確認計畫的容錯移轉**] 中，選擇 [**建立內部部署虛擬機器] （如果不存在**）。
5. 在 [**主機名稱**] 中，選取您要放置 VM 的新 hyper-v 主機伺服器。
6. 在 [**資料同步**處理] 中，建議您選取在容錯移轉前同步處理資料的選項。 這可將 Vm 的停機時間降到最低，而不會將它們關閉。 它會執行下列工作：
    - **第1階段**：取得 Azure VM 的快照集，並將其複製到內部部署 hyper-v 主機。 機器會繼續在 Azure 中執行。
    - **第2階段**：關閉 Azure VM，如此一來，就不會發生任何新的變更。 最後一組變更會傳送到內部部署伺服器，並啟動內部部署虛擬機器。
    
7. 按一下勾號以開始容錯移轉 (容錯回復)。
8. 完成初始同步處理且您已經準備好關閉 Azure VM 之後，請按一下 [**作業**] > \<規劃的容錯移轉作業 > >**完成容錯移轉**。 這會將 Azure 機器關機、將最新變更傳送到內部部署 VM，然後啟動它。
9. 您可以登入內部部署 VM，以確認所有專案都如預期般運作。
10. 按一下 [**認可**] 以完成容錯移轉。 Commit 會刪除 Azure VM 和其磁片，並準備要再次保護的內部部署 VM。
10. 按一下 [**反向**複寫]，開始將內部部署 VM 複寫至 Azure。 只有在 Azure 中關閉 VM 之後的差異變更才會進行複寫。

    > [!NOTE]
    > 如果您在資料同步處理期間取消容錯回復作業，內部部署 VM 將會處於損毀狀態。 這是因為資料同步處理會將最新的資料從 Azure VM 磁碟複製到內部部署資料磁片，而在同步處理完成之前，磁片資料可能不會處於一致的狀態。 如果內部部署 VM 在資料同步處理取消後啟動，則可能無法開機。 在此情況下，請重新執行容錯移轉以完成資料同步處理。


## <a name="next-steps"></a>後續步驟
將內部部署 VM 複寫至 Azure 之後，您可以視需要[執行另一個容錯移轉](site-recovery-failover.md)至 azure。
