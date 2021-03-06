---
title: 使用 Azure Migrate/Site Recovery 將 Windows Server 2008 伺服器遷移至 Azure
description: 本文說明如何將內部部署 Windows Server 2008 機器遷移至 Azure，並且會建議使用 Azure Migrate。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6cc0855d3a4540de780a566a4613b4dbc647cfc5
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369483"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>將執行 Windows Server 2008 的伺服器移轉到 Azure

此教學課程說明如何使用 Azure Site Recovery，將執行 Windows Server 2008 或 2008 R2 的內部部署伺服器移轉到 Azure。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 準備內部部署環境以便進行移轉。
> * 設定目標環境。
> * 設定複寫原則。
> * 啟用複寫。
> * 執行測試移轉，確定一切都沒問題。
> * 容錯移轉至 Azure 並完成移轉。

## <a name="migrate-with-azure-migrate"></a>使用 Azure Migrate 移轉

建議您使用 [Azure Migrate](../migrate/migrate-services-overview.md) 服務，將機器移轉到 Azure。 

- Azure Migrate 是專為伺服器移轉而建置的。
- Azure Migrate 提供集中式中樞，可探索、評量並將內部部署機器移轉到 Azure。 Azure Site Recovery 只應用於災害復原，不應用於移轉。
- Azure Migrate 支援移轉執行 Windows Server 2008 的伺服器。


## <a name="migrate-with-site-recovery"></a>使用 Site Recovery 移轉

### <a name="supported-operating-systems"></a>支援的作業系統


|作業系統  | 環境  |
|---------|---------|
|Windows Server 2008 SP2 - 32 位元和 64 位元 (IA-32 和 x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     VMware VM、Hyper-V VM 和實體伺服器    |
|Windows Server 2008 R2 SP1 - 64 位元</br>- Standard</br>- Enterprise</br>- Datacenter     |     VMware VM、Hyper-V VM 和實體伺服器|

> [!WARNING]
> - 不支援移轉執行 Server Core 的伺服器。
> - 請先確定您已安裝最新的 Service Pack 和 Windows 更新，再進行遷移。


### <a name="prerequisites"></a>必要條件

開始之前，最好先檢閱 [VMware 和實體伺服器移轉](vmware-azure-architecture.md)或 [Hyper-V 虛擬機器移轉](hyper-v-azure-architecture.md)的 Azure Site Recovery 架構 

若要遷移執行 Windows Server 2008 或 Windows Server 2008 R2 的 Hyper-V 虛擬機器，請遵循[將內部部署機器移轉至 Azure](migrate-tutorial-on-premises-azure.md) 教學課程中的步驟。

本教學課程的其餘部分會說明如何遷移執行 Windows Server 2008 或 2008 R2 的內部部署 VMware 虛擬機器和實體伺服器。
> [!TIP]
> 正在尋找可將 VMware VM 移轉至 Azure 的無代理程式方式嗎？ [按一下這裡](../migrate/tutorial-migrate-vmware.md)


### <a name="limitations-and-known-issues"></a>限制與已知問題

- 用來移轉 Windows Server 2008 SP2 伺服器的組態伺服器、其他處理序伺服器和行動服務，應該要執行 9.19.0.0 版或更新版本的 Azure Site Recovery 軟體。

- 不支援使用應用程式一致復原點和多重 VM 一致性功能，來複寫執行 Windows Server 2008 SP2 的伺服器。 Windows Server 2008 SP2 伺服器應遷移至「當機時保持一致」復原點。 系統預設會每五分鐘產生一次當機時保持一致復原點。 搭配使用複寫原則與已設定的「應用程式一致的快照頻率」，會由於應用程式一致復原點不足，而讓複寫健康情況變得嚴重。 若要避免誤判，請將複寫原則中的「應用程式一致的快照頻率」設定為「關閉」。

- 所要遷移的伺服器應該要有 .NET Framework 3.5 Service Pack 1，行動服務才會運作。

- 如果伺服器有動態磁碟，您可能會注意到在某些設定中，這些磁碟在已容錯移轉的伺服器上會標記為離線或顯示為外部磁碟。 您可能也會注意到，跨動態磁碟鏡像磁碟區的鏡像集狀態會標記為「失敗的備援」。 您可以手動匯入這些磁碟並加以重新啟動，以從 diskmgmt.msc 修正此問題。

- 所要遷移的伺服器應該要有 vmstorfl.sys 驅動程式。 如果所要遷移的伺服器內沒有此驅動程式，容錯移轉可能會失敗。 
  > [!TIP]
  >檢查 "C:\Windows\system32\drivers\vmstorfl.sys" 中是否有驅動程式。 如果找不到驅動程式，您可以在適當位置建立一個虛擬檔案來解決此問題。 
  >
  > 開啟命令提示字元 ([執行] > cmd)，並執行下列命令："copy nul c:\Windows\system32\drivers\vmstorfl.sys"

- 您可能無法在執行 32 位元作業系統的 Windows Server 2008 SP2 伺服器容錯移轉或測試容錯移轉至 Azure 後，立即以 RDP 對其進行連線。 請從 Azure 入口網站重新啟動已容錯移轉的虛擬機器，然後重試連線。 如果仍無法連線，請檢查伺服器是否已設定為允許遠端桌面連線，並確定沒有防火牆規則或網路安全性群組在封鎖連線。 
  > [!TIP]
  > 遷移伺服器之前，強烈建議您先測試容錯移轉。 請確定您已在您要遷移的每一部伺服器上，至少執行過一次成功的測試容錯移轉。 在測試容錯移轉期間，請連線至已測試容錯移轉的機器，並確定一切正常運作。
  >
  >測試容錯移轉作業不會造成系統中斷，可協助您在自選的隔離網路中建立虛擬機器來測試移轉。 不同於容錯移轉作業，在測試容錯移轉作業進行期間，資料複寫會繼續進行。 您可以不限次數地執行測試容錯移轉，直到您準備好遷移。 
  >
  


### <a name="get-started"></a>開始使用

執行下列工作以準備 Azure 訂用帳戶和內部部署 VMware/實體環境：

1. [準備 Azure](tutorial-prepare-azure.md)
2. 準備內部部署 [VMware](vmware-azure-tutorial-prepare-on-premises.md)


### <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

1. 登入 [Azure 入口網站](https://portal.azure.com) > [復原服務]。
2. 按一下 [建立資源] > [管理工具] > [備份和 Site Recovery]。
3. 在 [名稱] 中，指定易記名稱 [W2K8-migration]。 如果您有多個訂用帳戶，請選取適當的一個。
4. 建立資源群組 **w2k8migrate**。
5. 指定 Azure 區域。 若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 若要從儀表板快速存取保存庫，請按一下 [釘選到儀表板]，然後按一下 [建立]。

   ![顯示新保存庫建立選項的螢幕擷取畫面。](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

新的保存庫會新增到主要 [復原服務保存庫] 頁面上 [所有資源] 之下的 [儀表板]。


### <a name="prepare-your-on-premises-environment-for-migration"></a>準備內部部署環境以便進行移轉

- 若要移轉在 VMware 上執行的 Windows Server 2008 虛擬機器，[在 VMware 上安裝內部部署組態伺服器](vmware-azure-tutorial.md#set-up-the-source-environment)。
- 如果組態伺服器無法設定為 VMware 虛擬機器，[在內部部署實體伺服器或虛擬機器上安裝組態伺服器](physical-azure-disaster-recovery.md#set-up-the-source-environment)。

### <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。

1. 按一下 [準備基礎結構]   > [目標]  ，然後選取您要使用的 Azure 訂用帳戶。
2. 指定 Resource Manager 部署模型。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。


### <a name="set-up-a-replication-policy"></a>設定複寫原則

1. 若要建立新的複寫原則，請按一下 [Site Recovery 基礎結構] > [複寫原則] > [+複寫原則]。
2. 在 [建立複寫原則]中，指定原則名稱。
3. 在 [RPO 閾值] 中，指定復原點目標 (RPO) 限制。 如果複寫 RPO 超過此限制，則會產生警示。
4. 在 [復原點保留] 中，指定每個復原點的保留週期長度 (以小時為單位)。 複寫的 VM 可以還原至此時間範圍內的任何時間點。 複寫至進階儲存體的電腦支援最長保留 24 小時，標準儲存體則是 72 小時。
5. 在 [應用程式一致的快照頻率] 中，指定 [關閉]。 按一下 [確定]  以建立原則。

此原則會自動與設定伺服器產生關聯。

> [!WARNING]
> 請務必要在複寫原則的 [應用程式一致的快照頻率] 設定中指定 [關閉]。 在複寫執行 Windows Server 2008 的伺服器時，只支援「當機時保持一致」復原點。 若為 [應用程式一致的快照頻率] 指定任何其他值，將會由於應用程式一致復原點不足，讓伺服器的複寫健康情況變得嚴重，而產生錯誤警示。

   ![顯示複寫原則建立選項的螢幕擷取畫面。](media/migrate-tutorial-windows-server-2008/create-policy.png)

### <a name="enable-replication"></a>啟用複寫

針對要遷移的 Windows Server 2008 SP2/Windows Server 2008 R2 SP1 伺服器[啟用複寫](physical-azure-disaster-recovery.md#enable-replication)。
   
   ![顯示新增實體機器之選項的螢幕擷取畫面。](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![顯示啟用複寫之選項的螢幕擷取畫面。](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

### <a name="run-a-test-migration"></a>執行測試移轉

您可以在初始複寫完成且伺服器的狀態變為**受保護**後，對複寫伺服器執行測試容錯移轉。

執行[測試容錯移轉](tutorial-dr-drill-azure.md)至 Azure，確定一切都沒問題。

   ![顯示測試容錯移轉命令的螢幕擷取畫面。](media/migrate-tutorial-windows-server-2008/testfailover.png)


### <a name="migrate-to-azure"></a>移轉至 Azure

針對您要移轉的機器執行容錯移轉。

1. 在 [設定]   > [複寫的項目]  中，按一下機器 > [容錯移轉]  。
2. 在 [容錯移轉]  中，選取容錯移轉的目標**復原點**。 選取最新的復原點。
3. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)。 Site Recovery 會先嘗試關閉伺服器，再觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。
4. 確認 Azure VM 如預期般出現在 Azure 中。
5. 在 [複寫的項目] 中，以滑鼠右鍵按一下伺服器 > [完成移轉]。 這會執行以下動作：

    - 完成移轉程序、停止伺服器的複寫，並停止伺服器的 Site Recovery 計費。
    - 此步驟會清除複寫資料。 但並不會刪除已遷移的 VM。

   ![顯示完整移轉命令的螢幕擷取畫面。](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **請勿取消正在進行中的容錯移轉**：在容錯移轉開始之前，伺服器複寫已停止。 如果您取消正在進行的容錯移轉，容錯移轉會停止，但伺服器不會繼續複寫。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [檢閱關於 Azure Migrate 的常見問題](../migrate/resources-faq.md)。