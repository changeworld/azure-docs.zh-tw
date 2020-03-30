---
title: 使用 Azure 網站恢復直接複製運行存儲空間的 Azure VM
description: 瞭解如何使用 Azure 網站恢復複製運行存儲空間直接的 Azure VM。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 9f394fa8d618c97d74a47ff6e42a002f177cf7d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973656"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>將運行存儲空間的 Azure VM 直接複製到其他區域

本文說明如何對於執行儲存空間直接存取的 Azure VM 啟用災害復原。

>[!NOTE]
>對於儲存空間直接存取叢集僅支援損毀一致復原點。
>

[直接存儲空間 （S2D）](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct)是軟體定義的存儲，它提供了在 Azure 上創建[來賓群集](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure)的方法。  Microsoft Azure 中的來賓群集是由 IaaS VM 組成的容錯移轉叢集。 它允許託管 VM 工作負載跨來賓群集容錯移轉，實現應用程式更高的可用性 SLA，而不是單個 Azure VM 所能提供的。 在 VM 承載關鍵應用程式（如 SQL 或橫向擴展檔案伺服器）的情況下，它非常有用。

## <a name="disaster-recovery-with-storage-spaces-direct"></a>直接存儲空間的災害復原

在典型的案例中，Azure 可能有虛擬機器客體叢集，能夠達到擴充檔案伺服器等等應用程式的較高恢復功能。 雖然這可以提供應用程式的高可用性，不過您會想使用站台復原來保護應用程式在區域層級的失敗。 站台復原會將一個區域的資料複寫到另一個 Azure 區域，並且以容錯移轉在災害復原區域中啟動叢集。

下圖顯示了直接使用存儲空間的雙節點 Azure VM 容錯移轉叢集。

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- 在 Windows 容錯移轉叢集中的兩個 Azure 虛擬機器和每個虛擬機器都有兩個以上的資料磁碟。
- S2D 會同步處理資料磁碟上的資料，並將已同步處理的儲存體當做儲存體集區使用。
- 儲存體集區會向容錯移轉叢集提供叢集共用磁碟區 (CSV)。
- 容錯移轉叢集在資料磁碟機使用 CSV。

**災害復原注意事項**

1. 您設定叢集的[雲端見證](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)時，在災害復原區域中保留見證。
2. 如果您要將虛擬機器容錯移轉到與來源區域不同的 DR 區域上的子網路，則叢集 IP 位址必須在容錯移轉後變更。  要更改群集的 IP，您需要使用網站恢復[恢復計畫腳本。](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
使用自訂指令碼擴充功能在 VM 內執行命令的[範例指令碼](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>啟用 S2D 叢集的站台復原：

1. 在復原服務保存庫中，按一下 [+複寫]
1. 選取叢集中的所有節點，並將這些節點設定為[多 VM 一致性群組](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)的一部分
1. 選取應用程式一致性關閉的複寫原則* (僅提供損毀一致性支援)
1. 啟用複寫

   ![storagespacesdirect 保護](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. 移至複寫的項目，然後您即可看到兩個虛擬機器狀態。
3. 兩個虛擬機器會開始受到保護，而且會顯示為多 VM 一致性群組的一部分。

   ![storagespacesdirect 保護](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>建立復原計劃
復原方案支援在容錯移轉期間對多層式應用程式中的各層進行排序。 排序有助於維持應用程式的一致性。 當您為多層式 Web 應用程式建立復原方案時，請完成[使用 Site Recovery 建立復原方案](site-recovery-create-recovery-plans.md)中所述的步驟。

### <a name="adding-virtual-machines-to-failover-groups"></a>將虛擬機器新增至容錯移轉群組

1.  新增虛擬機器以建立復原方案。
2.  按一下 [自訂] 將 VM 分組。 根據預設，所有 VM 都是「群組 1」的一部分。


### <a name="add-scripts-to-the-recovery-plan"></a>將指令碼新增至復原計畫
為了讓應用程式可正常運作，您可能需要在容錯移轉後或測試容錯移轉期間，於 Azure 虛擬機器上執行某些作業。 您可以自動化某些容錯移轉後的作業。 例如，在這裡，我們附加負載等化器和更改群集 IP。


### <a name="failover-of-the-virtual-machines"></a>虛擬機器的容錯移轉 
使用網站恢復[恢復計畫](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)需要容錯移轉 VM 的兩個節點 

![storagespacesdirect 保護](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>執行測試容錯移轉
1.  在 Azure 入口網站中，選取您的復原服務保存庫。
2.  選取您建立的復原計畫。
3.  選取 [測試容錯移轉]****。
4.  若要啟動測試容錯移轉程序，請選取復原點和 Azure 虛擬網路。
5.  當次要環境啟動時，即可執行您驗證。
6.  完成驗證後，若要清除此容錯移轉環境，請選取 [清除測試容錯移轉]****。

如需詳細資訊，請參閱[在 Site Recovery 中測試容錯移轉至 Azure](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>執行容錯移轉

1.  在 Azure 入口網站中，選取您的復原服務保存庫。
2.  選取為 SAP 應用程式建立的復原計畫。
3.  選取 [容錯移轉]****。
4.  若要啟動容錯移轉程序，請選取復原點。

如需詳細資訊，請參閱[在 Site Recovery 中容錯移轉](site-recovery-failover.md)。
## <a name="next-steps"></a>後續步驟

[深入了解](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)如何執行容錯回復。
