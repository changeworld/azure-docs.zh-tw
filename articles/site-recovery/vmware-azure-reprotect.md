---
title: 使用 Azure 網站恢復將 VMware VM 重新保護到本地網站
description: 瞭解如何使用 Azure 網站恢復在容錯移轉到 Azure 後重新保護 VMware VM。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257169"
---
# <a name="reprotect-from-azure-to-on-premises"></a>從 Azure 移至內部部署來重新保護

將內部部署 VMware VM 或實體伺服器[容錯移轉](site-recovery-failover.md)到 Azure 之後，容錯回復到內部部署網站的第一個步驟，是重新保護在容錯移轉期間建立的 Azure VM。 本文說明如何執行此操作。 

## <a name="before-you-begin"></a>開始之前

1. 按照[本文](vmware-azure-prepare-failback.md)中的步驟準備重新保護和故障倒回，包括在 Azure 中設置進程伺服器和本地主目標伺服器，以及配置網站到網站 VPN 或 ExpressRoute 專用對等互連以進行故障倒回。
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
    - VMware VM 不能故障回超 V。
    - [已遷移](migrate-overview.md#what-do-we-mean-by-migration)的 VM。
    - 已移動到其他資源組的 VM。
    - 已刪除的副本 Azure VM。
    - 不受保護的副本 Azure VM（複製到本地網站）。
10. [查看可以使用的故障恢復類型](concepts-types-of-failback.md)- 原始位置恢復和備用位置恢復。


## <a name="enable-reprotection"></a>啟用重新保護

啟用複寫。 您可以重新保護特定的 VM 或恢復計畫：

- 如果重新保護恢復計畫，則必須為每個受保護的電腦提供值。
- 如果 VM 屬於多個 VM 一致性的複製組，則只能使用恢復計畫重新保護它們。 複製組中的 VM 必須使用相同的主目標伺服器

### <a name="before-you-start"></a>開始之前

- 容錯移轉後，在 Azure 中啟動 VM 後，代理需要一些時間才能註冊回佈建服務器（最多 15 分鐘）。 在這段時間，您將無法執行重新保護，且會有錯誤訊息指出代理程式未安裝。 如果發生這種情況，請等待幾分鐘，然後重新保護。
- 如果要將 Azure VM 故障退回現有本地 VM，請在主目標伺服器的 ESXi 主機上安裝具有讀/寫存取權限的本地 VM 資料存儲。
- 如果要故障回備用位置（例如，如果本地 VM 不存在），請選擇為主目標伺服器配置的保留磁碟機和資料存儲。 當您容錯回復到內部部署網站時，容錯回復保護計畫中的 VMware 虛擬機器會使用與主要目標伺服器相同的資料存放區。 然後在 vCenter 中創建新的 VM。

啟用重新保護，如下所示：

1. 選擇**保存庫** > **複製項**。 以滑鼠右鍵按一下已容錯移轉的虛擬機器，然後選取 [重新保護]****。 或者，從命令按鈕選取機器，然後選取 [重新保護]****。
2. 確認已選取 [Azure 到內部部署]**** 作為保護方向。
3. 在 [主要目標伺服器]**** 和 [處理伺服器]**** 中，選取內部部署主要目標伺服器和處理伺服器。  
4. 對於 [資料存放區]****，選取您想要將內部部署磁碟復原至該位置的資料存放區。 當內部部署虛擬機器已被刪除且您需要建立新的磁碟時，請使用此選項。 如果磁碟已存在，則會忽略此選項。 您仍需要指定一個值。
5. 選取保留磁碟機。
6. 系統會自動選取容錯回復原則。
7. 選取 [確定]**** 以開始重新保護。

    ![重新保護對話方塊](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. 作業開始將 Azure VM 複製到本地網站。 您可以在 [作業] **** 索引標籤上追蹤進度。
    - 重新保護成功後，VM 將進入受保護狀態。
    - 內部部署 VM 會在重新保護期間關閉。 這有助於確保資料在複寫期間的一致性。
    - 重新保護完成後，不要打開本地 VM。
   

## <a name="next-steps"></a>後續步驟

- 如果遇到任何問題，請查看[故障排除文章](vmware-azure-troubleshoot-failback-reprotect.md)。
- Azure VM 受到保護後，可以[運行故障恢復](vmware-azure-failback.md)。 故障回關閉 Azure VM 並引導本地 VM。 預計應用程式會出現一些停機時間，並相應地選擇故障恢復時間。


