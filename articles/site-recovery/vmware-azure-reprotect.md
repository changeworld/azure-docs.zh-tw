---
title: 使用 Azure Site Recovery 將 VMware Vm 重新保護至內部部署網站
description: 瞭解如何在容錯移轉至 Azure 之後，使用 Azure Site Recovery 來重新保護 VMware Vm。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257169"
---
# <a name="reprotect-from-azure-to-on-premises"></a>從 Azure 移至內部部署來重新保護

將內部部署 VMware VM 或實體伺服器[容錯移轉](site-recovery-failover.md)到 Azure 之後，容錯回復到內部部署網站的第一個步驟，是重新保護在容錯移轉期間建立的 Azure VM。 本文說明如何執行此操作。 

## <a name="before-you-begin"></a>開始之前

1. 依照[這篇文章](vmware-azure-prepare-failback.md)中的步驟來準備重新保護和容錯回復，包括在 Azure 中設定進程伺服器、內部部署主要目標伺服器，以及設定站對站 VPN 或 ExpressRoute 私人對等互連，以進行容錯回復。
2. 請確定內部部署設定伺服器正在執行並已連線至 Azure。 在容錯移轉至 Azure 期間，內部部署網站可能無法存取，而且設定伺服器可能無法使用或關機。 在容錯回復期間，VM 必須存在於設定伺服器資料庫中。 否則，將無法成功容錯回復。
3. 刪除內部部署主要目標伺服器上的任何快照集。 如果有快照集，重新保護將無法作用。  VM 上的快照集會在重新保護作業期間自動合併。
4. 如果您要重新保護收集到複寫群組以進行多部 VM 一致性的 Vm，請確定它們都具有相同的作業系統（Windows 或 Linux），並確定您部署的主要目標伺服器具有相同類型的作業系統。 複寫群組中的所有 Vm 都必須使用相同的主要目標伺服器。
5. 開啟容錯回復[所需的埠](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback)。
6. 確定 vCenter Server 在容錯回復之前已連線。 否則，將磁碟中斷連線並將它們重新連結至虛擬機器的作業會失敗。
7. 如果 vCenter 伺服器管理您要容錯回復的 Vm，請確定您擁有必要的許可權。 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，保護會成功且容錯回復可作用。 不過，在重新保護期間，容錯移轉會失敗，因為無法探索資料存放區，而且不會在重新保護期間列出。 若要解決這個問題，您可以使用[適當的帳戶/許可權](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)更新 vCenter 認證，然後重試此作業。 
8. 如果您使用範本來建立虛擬機器，請確定每個 VM 都有它自己的 UUID 作為磁片。 如果內部部署 VM UUID 與主要目標伺服器的 UUID 衝突，因為兩者都是從相同的範本建立的，則重新保護會失敗。 從不同的範本進行部署。
9. 如果您要容錯回復到替代的 vCenter Server，請確定已探索新的 vCenter Server 和主要目標伺服器。 通常，如果它們不是無法**存取的資料存放區**，或在重新保護時看不到。
10. 確認您無法容錯回復的下列案例：
    - 如果您使用的是 ESXi 5.5 免費版或 vSphere 6 程式管理的免費版本。 升級至不同的版本。
    - 如果您有 Windows Server 2008 R2 SP1 實體伺服器，則為。
    - VMware Vm 無法容錯回復至 Hyper-v。
    - 已[遷移](migrate-overview.md#what-do-we-mean-by-migration)的 vm。
    - 已移至另一個資源群組的 VM。
    - 已刪除的複本 Azure VM。
    - 未受保護的複本 Azure VM （複寫至內部部署網站）。
10. 請參閱您可以使用的容錯[回復類型](concepts-types-of-failback.md)-原始位置復原和替代位置復原。


## <a name="enable-reprotection"></a>啟用重新保護

啟用複寫。 您可以重新保護特定 Vm 或復原方案：

- 如果您重新保護復原方案，您必須為每部受保護的機器提供值。
- 如果 Vm 屬於多部 VM 一致性的複寫群組，則只能使用復原方案來重新保護它們。 複寫群組中的 Vm 必須使用相同的主要目標伺服器

### <a name="before-you-start"></a>開始之前

- 在容錯移轉之後，在 Azure 中啟動 VM 之後，代理程式需要一些時間才會註冊回設定伺服器（最多15分鐘）。 在這段時間，您將無法執行重新保護，且會有錯誤訊息指出代理程式未安裝。 如果發生這種情況，請等候幾分鐘的時間，然後再重新保護。
- 如果您想要將 Azure VM 容錯回復到現有的內部部署 VM，請在主要目標伺服器的 ESXi 主機上掛接具有讀取/寫入存取權的內部部署 VM 資料存放區。
- 如果您想要容錯回復至替代位置（例如，如果內部部署 VM 不存在），請選取為主要目標伺服器所設定的保留磁片磁碟機和資料存放區。 當您容錯回復到內部部署網站時，容錯回復保護計畫中的 VMware 虛擬機器會使用與主要目標伺服器相同的資料存放區。 接著會在 vCenter 中建立新的 VM。

啟用重新保護，如下所示：

1. 選取 [保存庫] > [複寫的項目]。 以滑鼠右鍵按一下已容錯移轉的虛擬機器，然後選取 [重新保護]。 或者，從命令按鈕選取機器，然後選取 [重新保護]。
2. 確認已選取 [Azure 到內部部署] 作為保護方向。
3. 在 [主要目標伺服器] 和 [處理伺服器] 中，選取內部部署主要目標伺服器和處理伺服器。  
4. 對於 [資料存放區]，選取您想要將內部部署磁碟復原至該位置的資料存放區。 當內部部署虛擬機器已被刪除且您需要建立新的磁碟時，請使用此選項。 如果磁碟已存在，則會忽略此選項。 您仍需要指定一個值。
5. 選取保留磁碟機。
6. 系統會自動選取容錯回復原則。
7. 選取 [確定] 以開始重新保護。

    ![重新保護對話方塊](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. 作業會開始將 Azure VM 複寫至內部部署網站。 您可以在 [作業] 索引標籤上追蹤進度。
    - 當重新保護成功時，VM 會進入受保護的狀態。
    - 內部部署 VM 會在重新保護期間關閉。 這有助於確保資料在複寫期間的一致性。
    - 重新保護完成後，請勿開啟內部部署 VM。
   

## <a name="next-steps"></a>後續步驟

- 如果您遇到任何問題，請參閱[疑難排解文章](vmware-azure-troubleshoot-failback-reprotect.md)。
- 在 Azure Vm 受到保護之後，您就可以[執行容錯回復](vmware-azure-failback.md)。 容錯回復會關閉 Azure VM，並啟動內部部署 VM。 應用程式預期會停機一段時間，並據以選擇容錯回復時間。


