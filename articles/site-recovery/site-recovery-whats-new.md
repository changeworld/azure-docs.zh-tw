---
title: Azure Site Recovery 的新功能
description: 提供新功能的摘要，以及 Azure Site Recovery 服務中的最新更新。
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 09afd4416efc8e6904b7c487d5ed2f3be22db081
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654815"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 最新功能

[Azure Site Recovery](site-recovery-overview.md) 服務會持續更新並改進。 為了協助您掌握最新訊息，本文提供最新版本、新功能和新內容的相關資訊。 此頁面會定期更新。

您可以遵循並訂閱 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery) 通道中的 Site Recovery 更新通知。

## <a name="supported-updates"></a>支援的更新

針對 Site Recovery 元件，我們支援 N-1 個版本，其中 N 是最新發行的版本。 下表將摘要說明這些報表：

**更新** |  **統一安裝** | **設定伺服器 ova** | **行動服務代理程式** | **站台復原提供者** | **復原服務代理程式**
--- | --- | --- | --- | --- | ---
[匯總套件49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0
[匯總套件48](https://support.microsoft.com/help/4573888/) | 9.35.5659.1 | 5.1.6200.0 | 9.35.5659.1 | 5.1.6200.0 | 2.0.9186.0
[匯總套件47](https://support.microsoft.com/help/4570609/) | 9.34.5634.1 | 5.1.6100.0 | 9.34.5634.1 | 5.1.6100.0 | HyperV 電腦-2.0.9183。0 <br> VMware 機器-2.0.9177。0
[匯總套件46](https://support.microsoft.com/help/4564347/) | 9.33.5598.1 | 5.1.5900.0 | 9.33.5598.1 | 5.1.5900.0 | 2.0.9175.0
[匯總套件45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 版 | 9.32.5487.1 | 5.1.5400.0 版 | 2.0.9165.0

[深入瞭解](service-updates-how-to.md) 更新的安裝與支援。


## <a name="updates-august-2020"></a>更新 (2020 年8月) 

### <a name="update-rollup-49"></a>更新彙總套件49

[更新彙總套件 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) 提供下列更新：

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新，如匯總中所述。
**問題修正/改善** | 匯總中詳述的一些修正和改進。

## <a name="updates-july-2020"></a>2020年7月 (更新) 

### <a name="update-rollup-48"></a>更新彙總套件48

[更新彙總套件 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) 提供下列更新：

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新，如匯總中所述。
**問題修正/改善** | 匯總中詳述的一些修正和改進。

> [!NOTE]
> 更新彙總套件48有已知的問題，可讓使用 ADE 加密的 Linux 機器啟用複寫。 [深入了解](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137)。

### <a name="update-rollup-47"></a>更新彙總套件47

[更新彙總套件 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) 提供下列更新：

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新，如匯總中所述。
**問題修正/改善** | 匯總中詳述的一些修正和改進。

## <a name="updates-june-2020"></a>2020年6月 (更新) 

### <a name="update-rollup-46"></a>更新彙總套件46

[更新彙總套件 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) 提供下列更新：

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新，如匯總中所述。
**問題修正/改善** | 匯總中詳述的一些修正和改進。

## <a name="updates-march-2020"></a>2020年3月 (更新) 

### <a name="update-rollup-45"></a>更新彙總套件45

[更新彙總套件 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) 提供下列更新：

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新，如匯總中所述。
**問題修正/改善** | 匯總中詳述的一些修正和改進。

## <a name="updates-january-2020"></a>更新 (2020 年1月) 

### <a name="update-rollup-44"></a>更新彙總套件44

[更新彙總套件 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 沒有 Site Recovery 提供者和代理程式的更新。
**問題修正/改善** | 匯總中詳述的一些修正和改進。

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware 嚴重損壞修復

Azure 虛擬機器現在支援使用客戶管理的金鑰進行待用加密的 Vm 啟用。 [深入了解](azure-to-azure-how-to-enable-replication-cmk-disks.md)。


### <a name="update-rollup-43"></a>更新彙總套件43

[更新彙總套件 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新 (如匯總中所述) 
**問題修正/改善** | 匯總中詳述的一些修正和改善 () 


## <a name="updates-november-2019"></a>更新 (2019 年11月) 

### <a name="update-rollup-42"></a>更新彙總套件42

[更新彙總套件 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新 (如匯總中所述) 
**問題修正/改善** | 匯總中詳述的一些修正和改善 () 


### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復

資料表中摘要說明 Azure VM 嚴重損壞修復的新功能。

**功能** | **詳細資料**
--- | ---
**UEFI** | Site Recovery 現在支援使用 UEFI 型開機架構的 Azure Vm 進行嚴重損壞修復。
**Linux** | Site Recovery 現在支援 Azure 磁碟加密 (ADE) 執行 Linux 的 Azure Vm。
**第 2 代** | 所有層代 2 Azure Vm 現在都支援進行嚴重損壞修復。
**區域** | 您現在可以在挪威地區針對 Azure Vm 啟用嚴重損壞修復。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 至 Azure 災害復原

表格中摘要說明 VMware 至 Azure 嚴重損壞修復的新功能。

**功能** | **詳細資料**
--- | ---
**UEFI** | Site Recovery 現在支援使用 UEFI 型開機架構的 VMware Vm 進行嚴重損壞修復。<br/><br/> 支援的作業系統包括 Windows Server 2019、Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、SLES 12 SP4、RHEL 8。

## <a name="update-to-servicing-stack-updatesha-2"></a>更新至服務堆疊更新/SHA-2

若要將 Azure Vm 的災難復原到次要區域，或內部部署 VMware Vm 或實體伺服器至 Azure，請注意下列事項：

- 從適用于 Azure) Vm 的行動服務擴充 (版本9.30.5407.1，以及 (VMware/實體機器的行動服務代理程式) ，某些機器作業系統必須執行服務堆疊更新和 SHA-1。 詳細資料如下表所示。
- 根據連結的 KB 安裝 update 和 SHA-1。 2019 年 9 月起不支援 SHA-1，而且如果未啟用 SHA-2 程式碼簽署，代理程式擴充功能將不會如預期般安裝/升級。
- 深入了解 [SHA-2 升級和需求](https://aka.ms/SHA-2KB)。

**作業系統** | **Azure VM** | **VMware VM/實體機器**
--- | --- | ---
**Windows 2008 R2 SP1** | [服務堆疊更新](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [服務堆疊更新](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [服務堆疊更新](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [服務堆疊更新](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [服務堆疊更新](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [服務堆疊更新](https://support.microsoft.com/help/4490628)<br/> [Sha-1](https://support.microsoft.com/help/4474419)。



## <a name="updates-october-2019"></a>2019年10月 (更新) 

### <a name="update-rollup-41"></a>更新彙總套件41

[更新彙總套件 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新 (如匯總中所述) 
**問題修正/改善** | 匯總中詳述的一些修正和改善 () 



### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復

資料表中摘要說明 Azure VM 嚴重損壞修復的新功能。

**功能** | **詳細資料**
--- | ---
**測試容錯移轉設定** | 設定測試容錯移轉時，您現在可以設定測試容錯移轉 VM 和網路的設定，包括 IP 位址、NSG、內部負載平衡，以及每部電腦 NIC 的公用 IP 位址。 這些設定是選擇性的，不會變更目前的行為。 如果您未設定這些設定，您可以在測試容錯移轉時選擇 Azure VNet。 [深入了解](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/)。
**復原計畫** | 復原方案現在僅限 100 Vm，以確保容錯移轉的可靠性。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 至 Azure 災害復原

表格中摘要說明 VMware 至 Azure 嚴重損壞修復的新功能。

**功能** | **詳細資料**
--- | ---
**復原計畫** | 復原方案現在僅限 100 Vm，以確保容錯移轉的可靠性。


## <a name="updates-september-2019"></a>更新 (2019 年9月) 

### <a name="update-rollup-40"></a>更新彙總套件40

[更新彙總套件 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新 (如匯總中所述) 
**問題修正/改善** | 匯總中詳述的一些修正和改善 () 




### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復

資料表中摘要說明 Azure VM 嚴重損壞修復的新功能。

**功能** | **詳細資料**
--- | ---
**容錯回復後的清除** | 在容錯移轉至次要 Azure 之後，然後容錯回復至主要區域之後，Site Recovery 會自動清除次要區域中的機器。 不需要手動刪除 Vm 和 Nic。
**測試容錯移轉會保留 IP 位址** | 您現在可以在災難復原演練期間保留來源 VM 的 IP 位址，並為測試容錯移轉挑選靜態 IP 位址。

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/實體伺服器嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
新增進程伺服器警示 | 我們已加入新的進程伺服器警示。 [深入了解](vmware-physical-azure-monitor-process-server.md)。

### <a name="hyper-v-disaster-recovery"></a>Hyper-v 嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
儲存體帳戶 | Site Recovery 現在支援使用已啟用防火牆的儲存體帳戶來進行 Hyper-v 到 Azure 的嚴重損壞修復。  您可以選取已啟用防火牆的儲存體帳戶做為目標帳戶或快取儲存體。 如果您使用已啟用防火牆的帳戶，請確定您已啟用允許信任 Microsoft 服務的選項。<br/><br/> 具有或不含 System Center VMM 的 Hyper-v Vm 支援此功能。


## <a name="updates-august-2019"></a>更新 (2019 年8月) 

### <a name="update-rollup-39"></a>更新彙總套件39

[更新彙總套件 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新 (如匯總中所述) 
**問題修正/改善** | 匯總中詳述的一些修正和改善 () 


### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復

資料表中摘要說明 Azure VM 嚴重損壞修復的新功能。

**功能** | **詳細資料**
--- | ---
**沒有 Azure AD 的加密** | Azure VM 複寫至執行 Windows 的受控磁片現在支援不含 Azure AD 應用程式的加密。
**用於容錯移轉的網路資源** | 當容錯移轉至另一個區域時，您現在可以將網路資源設定 (Nsg、負載平衡、公用 IP 位址) 連結至 VM。

## <a name="updates-july-2019"></a>2019年7月 (更新) 

### <a name="update-rollup-38"></a>更新彙總套件38

[更新彙總套件 38](https://support.microsoft.com/help/4513507/) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新 (如匯總中所述) 
**問題修正/改善** | 匯總中詳述的一些修正和改善 () 


### <a name="general"></a>一般

Site Recovery 現在支援針對快取儲存體或目標儲存體使用一般用途 v2 儲存體帳戶。 先前只支援 v1。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 至 Azure 災害復原

當您複寫至具有受控磁片的 Azure VM 時，現在可以複寫最多 8 TB 的磁片。


## <a name="updates-june-2019"></a>2019年6月 (更新) 

### <a name="update-rollup-37"></a>更新彙總套件37

[更新彙總套件 37](https://support.microsoft.com/help/4508614/) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新 (如匯總中所述) 
**問題修正/改善** | 匯總中詳述的一些修正和改善 () 


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/實體伺服器嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**GPT 磁碟分割** | 從更新彙總套件37開始 (行動服務版本 9.25.5241.1) ，UEFI 中支援最多五個 GPT 磁碟分割。 在此更新之前，支援四個。



## <a name="updates-may-2019"></a>更新 (5 月 2019) 

### <a name="update-rollup-36"></a>更新彙總套件36

[更新彙總套件 36](https://support.microsoft.com/help/4503156) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新 (如匯總中所述) 
**問題修正/改善** | 匯總中詳述的一些修正和改善 () 

### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**複寫新增的磁片** | 針對已啟用損毀修復的 Azure VM，啟用資料磁片的複寫。 [深入了解](azure-to-azure-enable-replication-added-disk.md)。
**自動更新** | 針對在啟用損毀修復的 Azure Vm 上執行的行動服務擴充功能設定自動更新時，您現在可以選取現有的自動化帳戶來使用，而不是使用 Site Recovery 所建立的預設帳戶。 [深入了解](azure-to-azure-autoupdate.md)。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/實體伺服器嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**進程伺服器監視** | 若為內部部署 VMware Vm 和實體伺服器的嚴重損壞修復，請使用改良的伺服器健康情況報告和警示來監視和疑難排解進程伺服器問題。 [深入了解](vmware-physical-azure-monitor-process-server.md)。





## <a name="updates-march-2019"></a>2019年3月 (更新) 

### <a name="update-rollup-35"></a>更新彙總套件35

[更新彙總套件 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新 (如匯總中所述) 
**問題修正/改善** | 匯總中詳述的一些修正和改善 () 

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/實體伺服器嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**受控磁碟** | 內部部署 VMware Vm 和實體伺服器的複寫現在已直接到 Azure 中的受控磁片。 內部部署資料會傳送至 Azure 中的快取儲存體帳戶，並在目標位置中的受控磁片上建立復原點。 這可確保您不需要管理多個目標儲存體帳戶。
**設定伺服器** | Site Recovery 現在支援具有多個 Nic 的設定伺服器。 在保存庫中註冊設定伺服器之前，請先將其他介面卡新增至設定伺服器 VM。 如果您之後新增，就必須在保存庫中重新註冊伺服器。


## <a name="updates-february-2019"></a>2019年2月 (更新) 

### <a name="update-rollup-34"></a>更新彙總套件34

[更新彙總套件 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新， (匯總) 的詳細資訊。
**問題修正/改善** | 匯總) 中詳述的一些修正和改善 (。


### <a name="update-rollup-33"></a>更新彙總套件33

[更新彙總套件 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新， (匯總) 的詳細資訊。
**問題修正/改善** | 匯總) 中詳述的一些修正和改善 (。


### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**網路對應** | 針對 Azure VM 嚴重損壞修復，您現在可以在啟用複寫時使用任何可用的目標網路。
**標準 SSD** | 您現在可以使用 [標準 SSD 磁片](../virtual-machines/disks-types.md#standard-ssd)來設定 Azure vm 的嚴重損壞修復。
**儲存空間直接存取** | 您可以使用 [儲存空間直接存取](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 高可用性，為在 Azure VM 應用程式上執行的應用程式設定嚴重損壞修復。  使用儲存空間直接存取 (S2D) 搭配 Site Recovery 可提供完整的 Azure VM 工作負載保護。 S2D 可讓您在 Azure 中裝載來賓叢集。 當 VM 裝載重要的應用程式，例如 SAP ASCS 層、SQL Server 或向外延展檔案伺服器時，這特別有用。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/實體伺服器嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**Linux BRTFS 檔案系統** | Site Recovery 現在支援使用 BRTFS 檔案系統複寫 VMware Vm。 如果有下列情況，則不支援複寫：<br/><br/>-在啟用複寫之後，>BTRFS 檔案系統子磁片區會變更。<br/><br/>-檔案系統會散佈在多個磁片上。<br/><br/>->BTRFS 檔案系統支援 RAID。
**Windows Server 2019** | 針對執行 Windows Server 2019 的電腦新增了支援。


## <a name="updates-january-2019"></a>更新 (2019 年1月) 


### <a name="accelerated-networking-azure-vms"></a>加速網路 (Azure Vm) 

加速網路可將單一根目錄 i/o 虛擬化 (SR-IOV) 至 VM，進而改善網路效能。 當您為 Azure VM 啟用複寫功能時，Site Recovery 會偵測是否已啟用加速網路。 如果是，則在容錯移轉之後，Site Recovery 將會自動對 [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 和 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 的目標複本 Azure VM 設定加速網路。

[深入了解](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="update-rollup-32"></a>更新彙總套件32

[更新彙總套件 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新， (匯總) 的詳細資訊。
**問題修正/改善** | 匯總) 中詳述的一些修正和改善 (。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**Linux 支援** | 已新增 RedHat 工作站6/7 的支援，以及適用于 Ubuntu、Debian 和 SUSE 的新核心版本。
**儲存空間直接存取** | Site Recovery 使用儲存空間直接存取 (S2D) 支援 Azure Vm。

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware Vm/實體伺服器嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**Linux 支援** | 針對 Redhat Enterprise Linux 7.6、RedHat 工作站6/7、Oracle Linux 6.10 和 Oracle Linux 7.6，以及 Ubuntu、Debian 和 SUSE 的新核心版本，新增了支援。


### <a name="update-rollup-31"></a>更新彙總套件31

[更新彙總套件 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新， (匯總) 的詳細資訊。
**問題修正/改善** | 匯總) 中詳述的一些修正和改善 (。

### <a name="vmware-vmsphysical-servers-replication"></a>VMware Vm/實體伺服器複寫

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**Linux 支援** |  已針對 Oracle Linux 6.8、Oracle Linux 6.9 和 Oracle Linux 7.0 與 Red Hat 相容核心，以及 Unbreakable Enterprise Kernel (UEK) 第5版的支援。
**LVM** | 針對 LVM 和 LVM2 磁片區加入的支援。<br/><br/> 現在支援磁碟分割上和 LVM 磁片區上的/boot 目錄。
**目錄** | 已針對設定為個別磁碟分割的這些目錄，或不在相同系統磁片上的檔案系統新增了支援：<br/><br/> / (根) 、/boot、/usr、/usr/local、/var、/etc。
**Windows Server 2008** | 新增動態磁碟的支援。
**容錯移轉** | 改進 VMware Vm 的容錯移轉時間，其中 storvsc 和 vsbus 不是開機驅動程式。
**UEFI 支援** | Azure Vm 不支援開機類型 UEFI。 您現在可以使用 Site Recovery 將內部部署實體伺服器與 UEFI 遷移至 Azure。 Site Recovery 在遷移之前將開機類型轉換成 BIOS，以遷移伺服器。 Site Recovery 先前僅支援 Vm 的這種轉換。 支援適用于執行 Windows Server 2012 或更新版本的實體伺服器。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**Linux 支援** | 已針對 Oracle Linux 6.8、Oracle Linux 6.9 和 Oracle Linux 7.0 與 Red Hat 相容核心，以及 Unbreakable Enterprise Kernel (UEK) 第5版的支援。
**Linux BRTFS 檔案系統** | 支援 Azure Vm。
**可用性區域中的 Azure Vm** | 您可以針對部署于可用性區域中的 Azure Vm 啟用複寫至另一個區域。 現在，您可以為 Azure VM 啟用複寫功能，並將容錯移轉的目標設為單一 VM 執行個體、可用性設定組中的 VM，或可用性區域中的 VM。 此設定不會影響複寫。 [閱讀](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。
**啟用防火牆的儲存體 (入口網站/PowerShell) ** | 針對 [已啟用防火牆的儲存體帳戶](../storage/common/storage-network-security.md)新增支援。<br/><br/> 您可以在已啟用防火牆的儲存體帳戶上，將具有非受控磁片的 Azure Vm 複寫至另一個 Azure 區域，以進行嚴重損壞修復。<br/><br/> 您可以使用已啟用防火牆的儲存體帳戶作為非受控磁片的目標儲存體帳戶。<br/><br/> 在入口網站中和使用 PowerShell 支援。

## <a name="updates-december-2018"></a>2018年12月 (更新) 

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>行動服務 (Azure Vm 的自動更新) 

Site Recovery 新增了行動服務擴充功能的自動更新選項。 行動服務擴充功能會安裝在 Site Recovery 所複寫的每個 Azure VM 上。 當您啟用複寫時，您會選取是否允許 Site Recovery 管理此擴充功能的更新。

更新不需要重新啟動 VM，也不會影響複寫。 [深入了解](azure-to-azure-autoupdate.md)。

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM 災害復原的定價計算機

Azure Vm 的嚴重損壞修復會產生 VM 授權成本，以及網路和儲存體成本。 Azure 提供[定價計算機](https://aka.ms/a2a-cost-estimator)來協助您計算這些成本。 Site Recovery 現在提供[範例定價預估](https://aka.ms/a2a-cost-estimator)功能，可根據三層式應用程式計算範例部署的價格；該應用程式使用 6 個 VM，其中包含 12 個標準 HDD 磁碟和六個進階 SSD 磁碟。

- 此範例假設標準有 10 GB 的資料變更，以及 20 GB 的 premium。
- 您可以針對本身的特定部署，變更估計成本的變數。
- 您可以指定 VM 的數目、受控磁碟的數目和類型，以及跨 VM 的預期總資料變更率。
- 此外，您也可以套用壓縮因數來估計頻寬成本。

[閱讀](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)公告。


## <a name="updates-october-2018"></a>2018年10月 (更新) 

### <a name="update-rollup-30"></a>更新彙總套件30

[更新彙總套件 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新， (匯總) 的詳細資訊。
**問題修正/改善** | 匯總) 中詳述的一些修正和改善 (。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復
本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**區域支援** | Site Recovery 新增澳大利亞中部1和澳大利亞中部2的支援。
**支援磁片加密** | 針對使用 Azure 磁碟加密 (ADE) （使用 Azure AD 應用程式加密）的 Azure Vm 進行嚴重損壞修復新增支援。 [深入了解](azure-to-azure-how-to-enable-replication-ade-vms.md)。
**磁片排除** | Azure VM 複寫期間現在會自動排除未初始化的磁片。
**啟用防火牆的儲存體 (PowerShell) ** | 針對 [已啟用防火牆的儲存體帳戶](../storage/common/storage-network-security.md)新增支援。<br/><br/> 您可以在已啟用防火牆的儲存體帳戶上，將具有非受控磁片的 Azure Vm 複寫至另一個 Azure 區域，以進行嚴重損壞修復。<br/><br/> 您可以使用已啟用防火牆的儲存體帳戶作為非受控磁片的目標儲存體帳戶。<br/><br/> 僅支援使用 PowerShell。


### <a name="update-rollup-29"></a>更新彙總套件29

[更新彙總套件 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新， (匯總) 的詳細資訊。
**問題修正/改善** | 匯總) 中詳述的一些修正和改善 (。


## <a name="updates-august-2018"></a>更新 (2018 年8月) 

### <a name="update-rollup-28"></a>更新彙總套件 28

[更新彙總套件 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新， (匯總) 的詳細資訊。
**問題修正/改善** | 匯總) 中詳述的一些修正和改善 (。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復
本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**Linux 支援** | 已新增 RedHat Enterprise Linux 6.10 的支援;CentOS 6.10。<br/><br/>
**雲端支援** | 德國雲端中的 Azure Vm 支援的嚴重損壞修復。
**跨訂用帳戶嚴重損壞修復** | 支援將一個區域中的 Azure Vm 複寫至不同訂用帳戶中的另一個區域（在相同的 Azure Active Directory 租使用者內）。 [深入了解](https://aka.ms/cross-sub-blog)。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/實體伺服器嚴重損壞修復
本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**Linux 支援** | 針對 RedHat Enterprise Linux 6.10、CentOS 6.10 新增了支援。<br/><br/> 現在支援使用 GUID 磁碟分割表格 (GPT) 磁碟分割樣式的 Linux 型 Vm （舊版 BIOS 相容性模式）。 如需詳細資訊，請參閱 [AZURE VM 常見問題](../virtual-machines/linux/faq-for-disks.md) 。
**遷移後 Vm 的嚴重損壞修復** | 支援針對遷移至 Azure 的內部部署 VMware VM 啟用次要區域的嚴重損壞修復，而不需要先卸載 VM 上的行動服務，再啟用複寫。
**Windows Server 2008** | 支援遷移執行 Windows Server 2008 R2/2008 64 位和32位的電腦。<br/><br/> 只遷移 (複寫和容錯移轉) 。 不支援容錯回復。

## <a name="updates-july-2018"></a>2018年7月 (更新) 

### <a name="update-rollup-27-july-2018"></a>更新彙總套件 27 (2018 年7月) 

[更新彙總套件 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) 提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者的更新， (匯總) 的詳細資訊。
**問題修正/改善** | 匯總) 中詳述的一些修正和改善 (。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**Linux 支援** | Red Hat Enterprise Linux 7.5 的新增支援。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/實體伺服器嚴重損壞修復

本月份新增的功能摘要于資料表中。

**功能** | **詳細資料**
--- | ---
**Linux 支援** | Red Hat Enterprise Linux 7.5 SUSE Linux Enterprise Server 12 新增支援。



## <a name="next-steps"></a>後續步驟

透過 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)頁面掌握相關更新的最新訊息。