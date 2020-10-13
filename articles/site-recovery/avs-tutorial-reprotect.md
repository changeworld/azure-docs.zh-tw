---
title: 使用 Azure Site Recovery 將 Azure VM 移至 Azure VMware 解決方案私人雲端來重新保護
description: 了解如何在容錯移轉至 Azure 之後，使用 Azure Site Recovery 來重新保護 Azure VMware 解決方案 VM。
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 80ff2f3f3d5fdcf61770889dcdaaf075941b90ff
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814201"
---
# <a name="reprotect-from-azure-to-azure-vmware-solution-private-cloud"></a>從 Azure 移至 Azure VMware 解決方案私人雲端來重新保護

將 Azure VMware 解決方案 VM [容錯移轉](avs-tutorial-failover.md)到 Azure 之後，若要容錯回復至 Azure VMware 解決方案私人雲端，則第一個步驟是重新保護在容錯移轉期間建立的 Azure VM。 本文說明如何執行此操作。 

## <a name="before-you-begin"></a>開始之前

1. 遵循[本文](vmware-azure-prepare-failback.md)中的步驟進行以準備重新保護和容錯回復，包括在 Azure 中設定處理序伺服器以及 Azure VMware 解決方案私人雲端的主要目標伺服器，以及設定站對站 VPN 或 ExpressRoute 私人對等互連以進行容錯回復。
2. 確定 Azure VMware 解決方案私人雲端設定伺服器正在執行並已連線到 Azure。 在容錯回復期間，VM 必須存在於設定伺服器資料庫中。 否則，將無法成功容錯回復。
3. 刪除 Azure VMware 解決方案私人雲端主要目標伺服器上的任何快照集。 如果有快照集，重新保護就無法運作。  VM 上的快照集會在重新保護作業期間自動合併。
4. 如果您要重新保護多個收集到複寫群組內以確保多 VM 一致性的 VM，請確定其全部具有相同的作業系統 (Windows 或 Linux)，並確定您部署的主要目標伺服器具有相同類型的作業系統。 複寫群組中的所有 VM 都必須使用相同的主要目標伺服器。
5. 開啟容錯回復[所需的連接埠](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback)。
6. 確定 vCenter Server 已連線後再進行容錯回復。 否則，將磁碟中斷連線並將它們重新連結至虛擬機器的作業會失敗。
7. 如果 vCenter 伺服器負責管理您要作為容錯回復目的地的 VM，請確定您擁有所需的權限。 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，保護會成功且容錯回復可作用。 不過，在重新保護期間，容錯移轉將會失敗，因為資料存放區既無法供您探索，也不會在重新保護期間列出。 若要解決此問題，您可以使用[適當的帳戶/權限](avs-tutorial-prepare-avs.md#prepare-an-account-for-automatic-discovery)更新 vCenter 認證，然後重試作業。 
8. 如果您使用範本來建立虛擬機器，請確定每部虛擬機器有磁碟本身的 UUID。 如果 Azure VMware 解決方案 VM UUID 和主要目標伺服器的 UUID 衝突 (因為兩者都是從相同的範本建立的)，則重新保護會失敗。 從不同範本進行部署。
9. 如果您要容錯回復至替代的 vCenter Server，請確定您已探索到新的 vCenter Server 和主要目標伺服器。 一般來說，如果未探索到，您就無法存取資料存放區，或無法在 [重新保護] 中看到。
10. 確認您無法進行容錯回復的下列案例：
    - 如果您使用的是 ESXi 5.5 免費版或 vSphere 6 Hypervisor 免費版。 請升級至不同版本。
    - 如果您有 Windows Server 2008 R2 SP1 實體伺服器。
    - VMware VM 無法容錯回復至 Hyper-V。
    - 已遷移的 VM。
    - 已移至另一個資源群組的 VM。
    - 已刪除的複本 Azure VM。
    - 未受保護的複本 Azure VM。
10. [檢閱您可以使用的容錯回復類型](concepts-types-of-failback.md) - 原始位置復原和替代位置復原。


## <a name="enable-reprotection"></a>啟用重新保護

啟用複寫。 您可以重新保護特定 VM 或復原方案：

- 如果您重新保護復原方案，則必須為每個受保護的機器提供值。
- 如果 VM 為了確保多 VM 一致性而屬於複寫群組，則只能使用復原方案來加以重新保護。 複寫群組中的 VM 都必須使用相同的主要目標伺服器

>[!NOTE]
>在重新保護期間從 Azure 傳送到先前來源的資料量，可以介於 0 個位元組到所有受保護機器的磁碟大小總和之間，而且無法進行計算。

### <a name="before-you-start"></a>在您開始使用 Intune 之前

- 在容錯移轉並於 Azure 中啟動 VM 之後，需要經過一些時間，代理程式才會註冊回到設定伺服器中 (最多 15 分鐘)。 在這段時間，您將無法執行重新保護，且會有錯誤訊息指出代理程式未安裝。 如果發生這種情況，請等候幾分鐘，然後再重新保護。
- 如果您想要將 Azure VM 容錯回復至現有的 Azure VMware 解決方案 VM，請在主要目標伺服器的 ESXi 主機上掛接具有讀取/寫入存取權的 VM 資料存放區。
- 如果您想要容錯回復至替代位置 (例如，如果 Azure VMware 解決方案 VM 不存在)，請選取針對主要目標伺服器所設定的保留磁碟機和資料存放區。 當您容錯回復至 Azure VMware 解決方案私人雲端時，容錯回復保護計劃中的虛擬機器會使用與主要目標伺服器相同的資料存放區。 接著會在 vCenter 中建立新的 VM。

啟用重新保護，如下所示：

1. 選取 [保存庫] > [複寫的項目]。 以滑鼠右鍵按一下已容錯移轉的虛擬機器，然後選取 [重新保護]****。 或者，從命令按鈕選取機器，然後選取 [重新保護]****。
2. 確認已選取 [Azure 到內部部署]**** 作為保護方向。
3. 在 [主要目標伺服器]**** 和 [處理伺服器]**** 中，選取內部部署主要目標伺服器和處理伺服器。  
4. 對於 [資料存放區]，選取您想要作為 Azure VMware 解決方案中磁碟復原目的地的資料存放區。 當您刪除 Azure VMware 解決方案 VM 時，會使用此選項，而且您需要建立新的磁碟。 如果磁碟已存在，則會忽略此選項。 您仍需要指定一個值。
5. 選取保留磁碟機。
6. 系統會自動選取容錯回復原則。
7. 選取 [確定]**** 以開始重新保護。

    ![重新保護對話方塊](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. 作業會開始將 Azure VM 複寫至 Azure VMware 解決方案私人雲端。 您可以在 [作業] **** 索引標籤上追蹤進度。
    - 重新保護成功時，VM 就會進入受保護狀態。
    - Azure VMware 解決方案 VM 會在重新保護期間關閉。 這有助於確保資料在複寫期間的一致性。
    - 重新保護完成後，請勿開啟 Azure VMware 解決方案 VM。
   

## <a name="next-steps"></a>下一步

- 如果您遇到任何問題，請檢閱[疑難排解文章](vmware-azure-troubleshoot-failback-reprotect.md)。
- Azure VM 受到保護之後，您就可以[執行容錯回復](avs-tutorial-failback.md)。 容錯回復會關閉 Azure VM，並啟動 Azure VMware 解決方案 VM。 應用程式應該會停機一段時間，請據以選擇容錯回復時間。


