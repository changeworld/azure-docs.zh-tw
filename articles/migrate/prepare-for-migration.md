---
title: 使用 Azure Migrate 準備電腦以進行移轉
description: 了解如何使用 Azure Migrate 準備內部部署電腦以進行移轉。
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: MVC
ms.openlocfilehash: d5ac4ded59a69e57de02779b0ba8ade9d7b48b26
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85106364"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>準備內部部署電腦以移轉至 Azure

本文說明在您使用 [Azure Migrate 伺服器移轉](migrate-services-overview.md#azure-migrate-server-migration-tool)工具將內部部署機器移轉至 Azure 之前，如何進行這些機器的準備工作。

在本文章中，您將：
> [!div class="checklist"]
> * 檢閱移轉限制。
> * 選取遷移 VMware VM 的方法
> * 針對您要遷移的機器，檢查其 Hypervisor 和作業系統需求。
> * 針對您想要遷移的機器，檢閱 URL 和連接埠存取。
> * 在開始移轉之前，檢閱可能需要進行的變更。
> * 針對已移轉的機器檢查其 Azure VM 需求
> * 準備機器，讓您可以在移轉後連線到 Azure VM。



## <a name="verify-migration-limitations"></a>確認移轉限制

下表摘要說明 Azure Migrate 的探索、評估和移轉限制。 建議您先評估機器再進行移轉，但這並非必要動作。

**案例** | **專案** | **探索/評估** | **移轉**
--- | --- | --- | ---
**VMware VM** | 在單一 Azure Migrate 專案中探索及評估多達 35,000 個 VM。 | 在 VMware 的單一 [Azure Migrate 設備](common-questions-appliance.md)上，最多可探索 10,000 個 VMware VM。 | **無代理程式移轉**：您可以同時複寫最多 300 個 VM。 為了達到最佳效能，建議您在數量超過 50 個時建立多個批次的 VM。<br/><br/> **以代理程式為基礎的移轉**：您可以[擴增](./agent-based-migration-architecture.md#performance-and-scaling)[複寫設備](migrate-replication-appliance.md)，以複寫大量的 VM。<br/><br/> 在入口網站中，您最多可以選取 10 個機器進行複寫。 若要複寫更多機器，請分批新增 (一批 10 個)。
**Hyper-V VM** | 在單一 Azure Migrate 專案中探索及評估多達 35,000 個 VM。 | 在單一 Azure Migrate 設備上探索多達 5,000 個 Hyper-V VM | 設備不會用於 Hyper-V 移轉。 Hyper-V 複寫提供者會在每個 Hyper-V 主機上執行。<br/><br/> 複寫容量會受到效能因素的影響，例如 VM 變換量，以及複寫資料的上傳頻寬。<br/><br/> 在入口網站中，您最多可以選取 10 個機器進行複寫。 若要複寫更多機器，請分批新增 (一批 10 個)。
**實體機器** | 在單一 Azure Migrate 專案中探索及評估多達 35,000 個機器。 | 在實體伺服器的單一 Azure Migrate 設備上，探索多達 250 個實體伺服器。 | 您可以[擴增](/agent-based-migration-architecture.md#performance-and-scaling)[複寫設備](migrate-replication-appliance.md)，以複寫大量的伺服器。<br/><br/> 在入口網站中，您最多可以選取 10 個機器進行複寫。 若要複寫更多機器，請分批新增 (一批 10 個)。

## <a name="select-a-vmware-migration-method"></a>選取 VMware 移轉方法

如果您要將 VMware VM 遷移至 Azure，請[比較](server-migrate-overview.md#compare-migration-methods)無代理程式和以代理程式為基礎的移轉方法，以決定何者較適用。

## <a name="verify-hypervisor-requirements"></a>確認 Hypervisor 需求

- 確認 [VMware 無代理程式](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)或[以 VMware 代理程式為基礎](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based)的需求。
- 確認 [Hyper-V 主機](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements)需求。


## <a name="verify-operating-system-requirements"></a>確認作業系統需求

確認進行移轉的作業系統：

- 如果您要遷移 VMware VM 或 Hyper-V VM，請確認[無代理程式](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)和[以代理程式為基礎](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based)移轉的 VMware VM 需求，以及 [Hyper-V VM](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) 的需求。
- 確認 Azure 支援 [Windows 作業系統](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。
- 確認 Azure 支援 [Linux 發行版本](../virtual-machines/linux/endorsed-distros.md)。

## <a name="review-url-and-port-access"></a>檢閱 URL 和連接埠存取

檢閱在移轉期間存取的 URL 和連接埠。

**案例** | **詳細資料** |  **URL** | **連接埠**
--- | --- | --- | ---
**VMware 無代理程式移轉** | 使用 [Azure Migrate 設備](migrate-appliance-architecture.md)進行移轉。 VMware VM 不會進行任何安裝。 | 檢閱使用設備進行探索、評估和移轉時所需的公用雲端和政府 [URL](migrate-appliance.md#url-access)。 | [檢閱](migrate-support-matrix-vmware-migration.md#port-requirements-agentless)無代理程式移轉的連接埠需求。
**VMware 以代理程式為基礎的移轉** | 使用[複寫設備](migrate-replication-appliance.md)進行移轉。 VM 上會安裝行動服務代理程式。 | 檢閱複寫設備需要存取的[公用雲端](migrate-replication-appliance.md#url-access) 和 [Azure Government URL](migrate-replication-appliance.md#azure-government-url-access)。 | [檢閱](migrate-replication-appliance.md#port-access)以代理程式為基礎的移轉期間所使用的連接埠。
**Hyper-V 移轉** | 使用安裝在 Hyper-V 主機上的提供者進行移轉。 Hyper-V VM 不會進行任何安裝。 | 檢閱在主機上執行的複寫提供者所需存取的[公用雲端](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud)和 [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) URL。 | Hyper-V 主機上的複寫提供者會經由 HTTPS 連接埠 443 使用輸出連線來傳送 VM 複寫資料。
**實體機器** | 使用[複寫設備](migrate-replication-appliance.md)進行移轉。 實體機器上會安裝行動服務代理程式。 | 檢閱複寫設備需要存取的[公用雲端](migrate-replication-appliance.md#url-access) 和 [Azure Government URL](migrate-replication-appliance.md#azure-government-url-access)。 | [檢閱](migrate-replication-appliance.md#port-access)實體移轉期間所使用的連接埠。

## <a name="verify-required-changes-before-migrating"></a>在遷移前驗證所需的變更

在您將 VM 遷移至 Azure 之前，VM 需要進行一些變更。

- 在某些作業系統上，Azure Migrate 在複寫/移轉的過程中會自動進行變更。
- 在其他作業系統上，您必須手動進行設定。
- 開始進行移轉之前，請務必手動進行設定。 如果您先遷移 VM 再進行變更，VM 可能無法在 Azure 中啟動。

檢閱相關表格以識別您需要進行的變更。

### <a name="windows-machines"></a>Windows 機器

下表摘要說明必要的變更。

**動作** | **VMware (無代理程式移轉)** | **VMware (以代理程式為基礎)/實體機器** | **Hyper-V 上的 Windows** 
--- | --- | --- | ---
**將 SAN 原則設定為 [全都線上]**<br/><br/> 這樣可以確保 Azure VM 中的 Windows 磁碟區使用與內部部署 VM 相同的磁碟機代號指派。 | 對於執行 Windows Server 2008 R2 或更新版本的機器，會自動設定。<br/><br/> 在舊版作業系統上必須手動設定。 | 多數情況下會自動設定。 | 手動設定。
**安裝 Hyper-V 客體整合** | 在執行 Windows Server 2003 的機器上[手動安裝](prepare-windows-server-2003-migration.md#install-on-vmware-vms)。 | 在執行 Windows Server 2003 的機器上[手動安裝](prepare-windows-server-2003-migration.md#install-on-vmware-vms)。 | 在執行 Windows Server 2003 的機器上[手動安裝](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms)。
**啟用 Azure 序列主控台**。<br/><br/>在 Azure VM 上[啟用主控台](../virtual-machines/troubleshooting/serial-console-windows.md)，以利進行疑難排解。 您不需要重新啟動 VM。 Azure VM 將會使用磁碟映像開機， 這相當於為新的 VM 重新開機。 | 手動啟用 | 手動啟用 | 手動啟用
**在移轉後連線**<br/><br/> 若要在移轉後進行連線，您必須先執行幾個步驟，再進行遷移。 | 手動[設定](#prepare-to-connect-to-azure-windows-vms)。 | 手動[設定](#prepare-to-connect-to-azure-windows-vms)。 | 手動[設定](#prepare-to-connect-to-azure-windows-vms)。


#### <a name="configure-san-policy"></a>設定 SAN 原則

根據預設，系統會將磁碟機 D 指派給 Azure VM，作為暫存儲存體。

- 此磁碟機指派會使其他所有連接的存放磁碟機指派遞增一個字母。
- 例如，如果您的內部部署安裝使用指派給磁碟機 D 的資料磁片安裝應用程式，則在您將 VM 移轉至 Azure 之後，此磁碟機的指派會遞增為磁碟機 E。 
- 為防止這種自動指派，並確保 Azure 會將下一個可用的磁碟機代號指派給其暫存磁碟區，請將存放區域網路 (SAN) 原則設定為 **OnlineAll：

手動進行此設定，如下所示：

1. 在內部部署電腦 (而非主機伺服器) 上，開啟提升權限的命令提示字元。
2. 輸入 **diskpart**。
3. 輸入 **SAN**。 如果未維持客體作業系統的磁碟機代號，則會傳回 [全部離線] 或 [共用離線]。
4. 在 **DISKPART** 提示字元中，輸入 **SAN Policy=OnlineAll**。 此設定可確保磁碟上線，您也可以讀取和寫入這兩個磁碟。
5. 在測試移轉期間，您可以確認是否保留磁碟機代號。


### <a name="linux-machines"></a>Linux 機器

針對下列版本，Azure Migrate 會自動完成這些動作

- Red Hat Enterprise Linux 7.0+、6.5+
- CentOS 7.0+、6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS、16.04LTS、14.04LTS
- Debian 8、7

若為其他版本，請將機器備妥，如下表的摘要所示。  


**動作** | **詳細資料** | **Linux 版本**
--- | --- | ---
**安裝 Hyper-V Linux Integration Services** | 重建 Linux init 映像以包含必要的 Hyper-V 驅動程式。 重建 init 映像可確保 VM 會在 Azure 中開機。 | 最新版的 Linux 散發套件預設會包含此元件。<br/><br/> 若未包含在內，請對所有版本 (上述版本除外) 進行手動安裝。
**啟用 Azure 序列主控台記錄** | 啟用主控台記錄可協助您進行疑難排解。 您不需要重新啟動 VM。 Azure VM 將會使用磁碟映像開機， 這相當於為新的 VM 重新開機。<br/><br/> 請遵循[這些指示](../virtual-machines/troubleshooting/serial-console-linux.md)來啟用。
**更新裝置對應檔案** | 將具有裝置名稱的裝置對應檔案更新為磁碟區關聯，以使用持續性裝置識別碼。 | 對所有版本 (上述版本除外) 進行手動安裝。
**更新 fstab 項目** |  更新項目以使用持續性磁碟區識別碼。    | 對所有版本 (上述版本除外) 進行手動更新。
**移除 udev 規則** | 移除任何會根據 mac 位址等項目來保留介面名稱的 udev 規則。 | 對所有版本 (上述版本除外) 進行手動移除。
**更新網路介面** | 更新網路介面以根據 DHCP.nst 來接收 IP 位址 | 對所有版本 (上述版本除外) 進行手動更新。
**啟用 ssh** | 確定已啟用 ssh，而且 sshd 服務已設定為在重新開機時自動啟動。<br/><br/> 確定傳入的 ssh 連線要求未遭到 OS 防火牆或可編寫指令碼的規則封鎖。| 對所有版本 (上述版本除外) 進行手動啟用。

深入了解[在 Azure 上執行 Linux VM](../virtual-machines/linux/create-upload-generic.md) 的步驟，並取得一些常用 Linux 發行版本的指示。


## <a name="check-azure-vm-requirements"></a>檢查 Azure VM 需求

您複寫到 Azure 的內部部署機器必須符合適用於作業系統的 Azure VM 需求，以及架構、磁碟、網路設定和 VM 命名。

在遷移之前，請參閱 [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)、[Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) 和[實體伺服器](migrate-support-matrix-physical-migration.md#azure-vm-requirements)移轉的 Azure VM 需求。



## <a name="prepare-to-connect-after-migration"></a>準備在移轉後連線

Azure VM 會在移轉至 Azure 期間建立。 移轉之後，您必須能夠連線到新的 Azure VM。 需要多個步驟才能成功連線。

### <a name="prepare-to-connect-to-azure-windows-vms"></a>準備連線至 Azure Windows VM

在內部部署 Windows 機器上：

1. 設定 Windows 設定。 設定包括移除任何靜態持續性路由或 WinHTTP Proxy。
2. 確定[必要服務](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)都在執行中。
3. 啟用遠端桌面 (RDP) 以允許從遠端連線至內部部署電腦。 了解如何[使用 PowerShell 啟用 RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)。
4. 若要在移轉後透過網際網路存取 Azure VM，請在內部部署電腦的 Windows 防火牆中，允許公用設定檔中的 TCP 和 UDP，並將 RDP 設定為所有設定檔允許的應用程式。
5. 如果您想要在移轉後透過站對站 VPN 存取 Azure VM，請在內部部署電腦的 Windows 防火牆中，針對網域和私人設定檔允許 RDP。 了解如何[允許 RDP 流量](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)。 
6. 移轉時，請確定內部部署 VM 上沒有任何 Windows 更新擱置中。 如果有，更新可能會在移轉後開始安裝在 Azure VM 上，而且您將無法登入 VM，直到更新完成為止。


### <a name="prepare-to-connect-with-linux-azure-vms"></a>準備與 Linux Azure VM 連線

在內部部署 Linux 電腦上：

1. 檢查安全殼層服務已設定為在系統開機時自動啟動。
2. 確認防火牆規則允許 SSH 連線。

### <a name="configure-azure-vms-after-migration"></a>移轉後設定 Azure VM

移轉之後，請在所建立的 Azure VM 上完成這些步驟：

1. 若要透過網際網路連線至 VM，請將公用 IP 位址指派給 VM。 您必須針對用於內部部署電腦的 Azure VM 使用不同的公用 IP 位址。 [深入了解](../virtual-network/virtual-network-public-ip-address.md)。
2. 檢查 VM 上的網路安全性群組 (NSG) 規則是否允許連至 RDP 或 SSH 連接埠的連入連線。
3. 檢查 [開機診斷](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) 以檢視 VM。


## <a name="next-steps"></a>後續步驟

決定要使用哪個方法[將 VMware VM 遷移](server-migrate-overview.md)到 Azure，或開始遷移 [Hyper-V VM](tutorial-migrate-hyper-v.md) 或[實體伺服器或虛擬化或雲端 VM](tutorial-migrate-physical-virtual-machines.md)。

## <a name="see-whats-supported"></a>支援的項目

針對 VMware VM，伺服器移轉支援[無代理程式或代理程式型移轉](server-migrate-overview.md)。

- **VMware VM**：確認 Hyper-V VM 的[移轉需求和支援](migrate-support-matrix-vmware-migration.md)。
- **Hyper-V VM**：確認 Hyper-V VM 的[移轉需求和支援](migrate-support-matrix-hyper-v-migration.md)。
- **實體機器**：確認內部部署實體機器和其他虛擬化伺服器的[移轉需求和支援](migrate-support-matrix-physical-migration.md)。 
