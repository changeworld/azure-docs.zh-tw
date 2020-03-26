---
title: 使用 Azure Migrate 準備電腦以進行移轉
description: 了解如何使用 Azure Migrate 準備內部部署電腦以進行移轉。
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78927479"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>準備內部部署電腦以移轉至 Azure

本文說明在使用以下工具開始遷移至 Azure 之前，如何準備內部部署機器：[Azure Migrate：伺服器移轉](migrate-services-overview.md#azure-migrate-server-migration-tool) 中)。

在本文章中，您將：
> [!div class="checklist"]
> * 確認移轉限制。
> * 檢查作業系統需求和支援限制。
> * 針對您想要遷移的機器，檢閱 URL 和連接埠存取。
> * 在開始移轉之前，檢閱可能需要進行的變更。
> * 進行設定以便在移轉後保留磁碟機代號。
> * 準備機器，讓您可以在移轉後連線到 Azure VM。

## <a name="verify-migration-limitations"></a>確認移轉限制

- 您可以在單一 Azure Migrate 專案中，使用 Azure Migrate 伺服器移轉，評估最多 35,000 個 VMware VM/Hyper-V VM。 專案可以結合 VMware VM 和 Hyper-V VM，最多可達每個 VM 的限制。
- 您一次最多可以選取 10 個 VM 進行移轉。 如果您需要複寫更多 VM，請以 10 個群組進行複寫。
- 針對 VMware 無代理程式移轉，您最多可以同時執行 100 個複寫。

## <a name="verify-operating-system-requirements"></a>確認作業系統需求

- 確認在 Azure 中支援您的 [Windows 作業系統](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。
- 確認在 Azure 中支援您的 [Linux 發行版本](../virtual-machines/linux/endorsed-distros.md)。

## <a name="see-whats-supported"></a>支援的項目

針對 VMware VM，伺服器移轉支援[無代理程式或代理程式型移轉](server-migrate-overview.md)。

- **VMware VM**：確認 Hyper-V VM 的[移轉需求和支援](migrate-support-matrix-vmware-migration.md)。
- **Hyper-V VM**：確認 Hyper-V VM 的[移轉需求和支援](migrate-support-matrix-hyper-v-migration.md)。
- **實體機器**：確認內部部署實體機器和其他虛擬化伺服器的[移轉需求和支援](migrate-support-matrix-physical-migration.md)。 

## <a name="review-url-and-port-access"></a>檢閱 URL 和連接埠存取

電腦可能需要在移轉期間存取網際網路。

- **Azure Migrate 設備**：檢閱 Azure Migrate 設備在無代理程式移轉期間需要存取的 [URL](migrate-appliance.md#url-access) 和[連接埠](migrate-support-matrix-vmware-migration.md#agentless-ports)。
- **VMware VM 代理程式型移轉**：檢閱複寫設備在進行 VMware VM 代理程式型移轉期間所使用的 [URL](migrate-replication-appliance.md#url-access) 和[連接埠](migrate-replication-appliance.md#port-access)。 
- **主機**：檢閱 Hyper-V 主機在移轉期間需要存取的 [URL 和連接埠](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts)。 
- **實體伺服器**：檢閱複寫設備在實體伺服器移轉期間所使用的 [URL](migrate-replication-appliance.md#url-access) 和[連接埠](migrate-replication-appliance.md#port-access)。

## <a name="verify-required-changes-before-migrating"></a>在遷移前驗證所需的變更

有些 VM 可能需要變更，才能在 Azure 中執行。 Azure Migrate 會針對執行下列作業系統的 VM，自動進行這些變更：

- Red Hat Enterprise Linux 7.0+、6.5+
- CentOS 7.0+、6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS、16.04LTS、14.04LTS
- Debian 8、7

至於其他作業系統，您必須在移轉之前，先手動準備機器。 

### <a name="prepare-windows-machines"></a>準備 Windows 電腦

如果您要遷移 Windows 機器，請在移轉之前進行以下變更。 如果您先移轉 VM 再進行變更，VM 可能無法在 Azure 中啟動。

1. 針對 Azure VM [啟用 Azure 序列主控台](../virtual-machines/troubleshooting/serial-console-windows.md)。 啟用主控台可協助您進行疑難排解。 您不需要重新啟動 VM。 Azure VM 將會使用磁碟映像開機， 這相當於為新的 VM 重新開機。 
2. 如果您要遷移執行 Windows Server 2003 的機器，請在 VM 作業系統上安裝 Hyper-V Guest Integration Services。 [深入了解](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)。

### <a name="prepare-linux-machines"></a>準備 Linux 電腦

1. 安裝 Hyper-V Linux Integration Services。 最新版本的 Linux 發行版本預設包含 Hyper-V Linux Integration Services。
2. 重建 Linux init 映像以包含必要的 Hyper-V 驅動程式。 重建 init 映像可確保 VM 會在 Azure 中開機 (只有某些發行版本才需要這麼做)。
3. [啟用 Azure 序列主控台記錄](../virtual-machines/troubleshooting/serial-console-linux.md)。 啟用主控台記錄可協助您進行疑難排解。 您不需要重新啟動 VM。 Azure VM 將會使用磁碟映像開機， 這相當於為新的 VM 重新開機。
4. 將具有裝置名稱的裝置對應檔案更新為磁碟區關聯，以使用持續性裝置識別碼。
5. 更新 fstab 檔案中的項目以使用持續性磁碟區識別碼。
6. 移除任何會根據 MAC 位址等項目保留介面名稱的 udev 規則。
7. 更新網路介面以接收來自 DHCP 的 IP 位址。

深入了解[在 Azure 上執行 Linux VM 的步驟](../virtual-machines/linux/create-upload-generic.md)，並取得一些常用 Linux 發行版本的指示。

## <a name="preserve-drive-letters-after-migration"></a>移轉後保留磁碟機代號

當您將內部部署電腦移轉到 Microsoft Azure 時，其他資料磁碟的磁碟機代號可能會從原本的值變更。 

根據預設，系統會將磁碟機 D 指派給 Azure VM，作為暫存儲存體。 此磁碟機指派會使其他所有連接的存放磁碟機指派遞增一個字母。 例如，如果您的內部部署安裝使用指派給磁碟機 D 的資料磁片安裝應用程式，則在您將 VM 移轉至 Azure 之後，此磁碟機的指派會遞增為磁碟機 E。 為防止這種自動指派，並確保 Azure 會將下一個可用的磁碟機代號指派給其暫存磁碟區，請將存放區域網路 (SAN) 原則設定為 **OnlineAll**：

1. 在內部部署電腦 (而非主機伺服器) 上，開啟提升權限的命令提示字元。
2. 輸入 **diskpart**。
3. 輸入 **SAN**。 如果未維持客體作業系統的磁碟機代號，則會傳回 [全部離線]  或 [共用離線]  。
4. 在 **DISKPART** 提示字元中，輸入 **SAN Policy=OnlineAll**。 此設定可確保磁碟上線，您也可以讀取和寫入這兩個磁碟。
5. 在測試移轉期間，您可以確認是否保留磁碟機代號。

## <a name="check-azure-vm-requirements"></a>檢查 Azure VM 需求

您複寫到 Azure 的內部部署機器必須符合適用於作業系統的 Azure VM 需求，以及架構、磁碟、網路設定和 VM 命名。 請先確認 [VMware VM 和實體伺服器](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)及 [Hyper-V VM](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) 的需求，再進行遷移。

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

> [!NOTE]
> Azure Bastion 服務會對 Azure VM 提供私密的 RDP 和 SSH 存取權。 [深入了解](../bastion/bastion-overview.md)此服務。

## <a name="next-steps"></a>後續步驟

決定要使用哪個方法[將 VMware VM 遷移](server-migrate-overview.md)到 Azure，或開始遷移 [Hyper-V VM](tutorial-migrate-hyper-v.md) 或[實體伺服器或虛擬化或雲端 VM](tutorial-migrate-physical-virtual-machines.md)。
