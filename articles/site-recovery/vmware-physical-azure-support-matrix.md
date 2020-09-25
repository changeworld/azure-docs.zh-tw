---
title: Azure Site Recovery 中的 VMware/實體嚴重損壞修復的支援矩陣
description: 摘要說明使用 Azure Site Recovery 將 VMware Vm 和實體伺服器的災難復原至 Azure 的支援。
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 81cab05b9ad8d6d2bb7f37bc743b5237a4dd6d68
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323612"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>從 VMware VM 和實體伺服器至 Azure 之災害復原的支援矩陣

本文摘要說明使用 [Azure Site Recovery](site-recovery-overview.md)將 VMware vm 和實體伺服器故障復原至 Azure 的支援元件和設定。

- [深入瞭解](vmware-azure-architecture.md) VMware VM/實體伺服器嚴重損壞修復架構。
- 請遵循我們的 [教學](tutorial-prepare-azure.md) 課程來嘗試損毀修復。

> [!NOTE]
> Site Recovery 不會在目的地區域中移動或儲存客戶資料，因為來源機器已設定嚴重損壞修復。 客戶可以選擇不同區域中的復原服務保存庫（如果有的話）。 復原服務保存庫包含中繼資料，但不包含實際的客戶資料。

## <a name="deployment-scenarios"></a>部署案例

**案例** | **詳細資料**
--- | ---
VMware Vm 的嚴重損壞修復 | 將內部部署 VMware 虛擬機器複寫至 Azure。 您可以在 Azure 入口網站中或使用 [PowerShell](vmware-azure-disaster-recovery-powershell.md)來部署此案例。
實體伺服器的嚴重損壞修復 | 將內部部署 Windows/Linux 實體伺服器複寫至 Azure。 您可以在 Azure 入口網站中部署此案例。

## <a name="on-premises-virtualization-servers"></a>內部部署虛擬化伺服器

**Server** | **需求** | **詳細資料**
--- | --- | ---
vCenter Server | 版本 7.0 & 此版本的後續更新、6.7、6.5、6.0 或5。5 | 我們建議您在嚴重損壞修復部署中使用 vCenter 伺服器。
vSphere 主機 | 版本 7.0 & 此版本的後續更新、6.7、6.5、6.0 或5。5 | 我們建議 vSphere 主機與 vCenter 伺服器應位於和處理序伺服器相同的網路中。 根據預設，處理序伺服器會在設定伺服器上執行。 [深入了解](vmware-physical-azure-config-process-server-overview.md)。

## <a name="site-recovery-configuration-server"></a>Site Recovery 組態伺服器

組態伺服器屬於內部部署機器，可執行 Site Recovery 元件，包括組態伺服器、處理序伺服器和主要目標伺服器。

- 針對 VMware Vm，您可以藉由下載 OVF 範本來建立 VMware VM，以設定設定伺服器。
- 對於實體伺服器，您可以手動設定設定伺服器電腦。

**元件** | **需求**
--- |---
CPU 核心 | 8
RAM | 16 GB
磁碟數量 | 3 個磁碟<br/><br/> 磁碟包括作業系統磁碟、處理序伺服器快取磁碟和用於容錯回復的保留磁碟機。
磁碟可用空間 | 600 GB 的進程伺服器快取空間。
磁碟可用空間 | 保留磁片磁碟機有 600 GB 的空間。
作業系統  | 具有桌面體驗的 windows Server 2012 R2 或 Windows Server 2016 <br/><br> 如果您打算使用此設備的內建主要目標進行容錯回復，請確定作業系統版本與複寫的專案相同或更高。|
作業系統地區設定 | 英文 (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Configuration server [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 版或更新版本不需要此設定。
Windows Server 角色 | 請勿啟用 Active Directory Domain Services;Internet Information Services (IIS) 或 Hyper-v。
群組原則| - 防止存取命令提示字元。 <br/> - 防止存取登錄編輯工具。 <br/> - 檔案附件的信任邏輯。 <br/> - 開啟指令碼執行。 <br/> - [深入了解](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
IIS | 請確定您已執行下列動作：<br/><br/> -沒有預先存在的預設網站 <br/> - 啟用[匿名驗證](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br/> - 啟用 [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 設定  <br/> - 沒有預先存在的網站/應用程式接聽連接埠 443<br/>
NIC 類型 | VMXNET3 (部署為 VMware VM 時)
IP 位址類型 | Static
連接埠 | 443用於控制通道協調流程<br/>9443用於資料傳輸

## <a name="replicated-machines"></a>複寫的電腦

Site Recovery 支援複寫任何執行於所支援機器上的工作負載。

**元件** | **詳細資料**
--- | ---
機器設定 | 複寫到 Azure 的電腦必須符合 [Azure 需求](#azure-vm-requirements)。
機器工作負載 | Site Recovery 支援複寫任何執行於所支援機器上的工作負載。 [深入了解](https://aka.ms/asr_workload)。
電腦名稱 | 確定電腦的顯示名稱不屬於 [Azure 保留的資源名稱](../azure-resource-manager/templates/error-reserved-resource-name.md)<br/><br/> 邏輯磁片區名稱不區分大小寫。 請確定裝置上的兩個磁片區都沒有相同的名稱。 例如：名稱為 "voLUME1"、"voLUME1" 的磁片區無法透過 Azure Site Recovery 保護。

### <a name="for-windows"></a>若為 Windows

**作業系統** | **詳細資料**
--- | ---
Windows Server 2019 | 從 [更新彙總套件 34](https://support.microsoft.com/help/4490016) (行動服務) 的9.22 版開始支援。
Windows Server 2016 64 位 | 支援伺服器核心、含有桌面體驗的伺服器。
Windows Server 2012 R2/Windows Server 2012 | 支援。
Windows Server 2008 R2 SP1 和更新版本。 | 支援。<br/><br/> 從行動服務代理程式的 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 版，您必須在執行 Windows 2008 R2 SP1 或更新版本的電腦上安裝 [服務堆疊更新 (SSU) ](https://support.microsoft.com/help/4490628) 和 [sha-1 更新](https://support.microsoft.com/help/4474419) 。 2019 年 9 月起不支援 SHA-1，而且如果未啟用 SHA-2 程式碼簽署，代理程式擴充功能將不會如預期般安裝/升級。 深入了解 [SHA-2 升級和需求](https://aka.ms/SHA-2KB)。
Windows Server 2008 SP2 或更新版本 (64 位/32 位)  |  僅支援遷移。 [深入了解](migrate-tutorial-windows-server-2008.md)。<br/><br/> 從行動服務代理程式的 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 版，您需要在 WINDOWS 2008 SP2 機器上安裝 [服務堆疊更新 (SSU) ](https://support.microsoft.com/help/4493730) 和 [sha-1 更新](https://support.microsoft.com/help/4474419) 。 2019年9月不支援 ISHA-1，如果未啟用 SHA-1 程式碼簽署，則代理程式延伸模組將不會如預期般安裝/升級。 深入了解 [SHA-2 升級和需求](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)。
Windows 10、Windows 8.1、Windows 8 | 僅支援64位系統。 不支援32位系統。
Windows 7 SP1 （含 SP1）64位 | 從 [更新彙總套件 36](https://support.microsoft.com/help/4503156) (行動服務) 的9.22 版開始支援。 </br></br> 從行動服務代理程式的 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 中，您需要在 WINDOWS 7 SP1 電腦上安裝 [服務堆疊更新 (SSU) ](https://support.microsoft.com/help/4490628) 和 [sha-1 更新](https://support.microsoft.com/help/4474419) 。  2019 年 9 月起不支援 SHA-1，而且如果未啟用 SHA-2 程式碼簽署，代理程式擴充功能將不會如預期般安裝/升級。 深入了解 [SHA-2 升級和需求](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)。

### <a name="for-linux"></a>若為 Linux

**作業系統** | **詳細資料**
--- | ---
Linux | 僅支援64位系統。 不支援32位系統。<br/><br/>每部 Linux 伺服器都應該已安裝 [linux Integration Services (.lis) 元件](https://www.microsoft.com/download/details.aspx?id=55106) 。 在測試容錯移轉/容錯移轉之後，必須在 Azure 中啟動伺服器。 如果缺少內建的 IIS 元件，請務必先安裝 [元件](https://www.microsoft.com/download/details.aspx?id=55106) ，再啟用複寫，讓機器在 Azure 中開機。 <br/><br/> Site Recovery 會協調容錯移轉以在 Azure 中執行 Linux 伺服器。 不過，Linux 廠商可能會將支援僅限於生命週期尚未結束的發行版本。<br/><br/> 在 Linux 散發套件上，僅支援屬於散發套件次要版本/更新的庫存核心。<br/><br/> 不支援升級各主要 Linux 散發套件版本的受保護機器。 若要升級，請停用複寫、升級作業系統，然後再次啟用複寫。<br/><br/> [深入瞭解](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) Azure 中的 Linux 和開放原始碼技術支援。
Linux Red Hat Enterprise | 5.2 至5.11</b><br/> 6.1 至6.10</b> </br> 7.0、7.1、7.2、7.3、7.4、7.5、7.6、 [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、 [7.8](https://support.microsoft.com/help/4564347/)、 [7.9 Beta 版](https://support.microsoft.com/help/4578241/) </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1、 [8.2](https://support.microsoft.com/help/4570609) <br/> 在執行 Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 的伺服器上，有幾個較舊的核心沒有預先安裝的 [Linux Integration Services (.lis) 元件](https://www.microsoft.com/download/details.aspx?id=55106) 。 如果缺少內建的 IIS 元件，請務必先安裝 [元件](https://www.microsoft.com/download/details.aspx?id=55106) ，再啟用複寫，讓機器在 Azure 中開機。
Linux：CentOS | 5.2 至5.11</b><br/> 6.1 至6.10</b><br/> </br> 7.0、7.1、7.2、7.3、7.4、7.5、7.6、 [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、 [7.8](https://support.microsoft.com/help/4564347/)、 [7.9](https://support.microsoft.com/help/4578241/) </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1、 [8.2](https://support.microsoft.com/help/4570609) <br/><br/> 執行 CentOS 5.2-5.11 & 6.1-6.10 的伺服器上有幾個較舊的核心沒有預先安裝的  [Linux Integration Services (的 .lis) 元件](https://www.microsoft.com/download/details.aspx?id=55106) 。 如果缺少內建的 IIS 元件，請務必先安裝 [元件](https://www.microsoft.com/download/details.aspx?id=55106) ，再啟用複寫，讓機器在 Azure 中開機。
Ubuntu | Ubuntu 14.04 * LTS server [ (檢查支援的核心版本) ](#ubuntu-kernel-versions)<br/>Ubuntu 16.04 * LTS server [ (檢查支援的核心版本) ](#ubuntu-kernel-versions) </br> Ubuntu 18.04 * LTS server [ (檢查支援的核心版本) ](#ubuntu-kernel-versions) </br> Ubuntu 20.04 * LTS server [ (檢查支援的核心版本) ](#ubuntu-kernel-versions) </br>  (*包含所有14.04 的支援。* x *、16.04。* x *、18.04。* x *、20.04。* x * 版本) 
Debian | Debian 7/Debian 8 (包含所有7的支援。 *x*、8。 *x* 版本) ;Debian 9 (包含9.1 到9.13 的支援。 不支援 Debian 9.0。 ) [ (檢查支援的核心版本) ](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1、SP2、SP3、SP4、 [SP5](https://support.microsoft.com/help/4570609) [ (檢查支援的核心版本) ](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15，15 SP1 [ (檢查支援的核心版本) ](#suse-linux-enterprise-server-15-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 11 SP3。 [請務必下載設定伺服器上的最新行動代理程式安裝程式](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)。 </br> SUSE Linux Enterprise Server 11 SP4 </br> **注意**：不支援將複寫的機器從 SUSE LINUX ENTERPRISE SERVER 11 SP3 升級至 SP4。 若要升級，請停用複寫，然後在升級之後重新啟用。 <br/>|
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、 [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)、 [7.8](https://support.microsoft.com/help/4573888/)、 [8.0](https://support.microsoft.com/help/4573888/)、 [8.2](https://support.microsoft.com/help/4573888/)  <br/> 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3、4 及 5 版 (UEK3、UEK4、UEK5)<br/><br/>8.1<br/>在所有 UEK 核心和 RedHat 核心上執行 <= 3.10.0-1062. * [9.35](https://support.microsoft.com/help/4573888/)支援可支援 RedHat 核心的其餘部分，可在[9.36](https://support.microsoft.com/help/4578241/)中取得

> [!Note]
> 針對每個 Windows 版本，Azure Site Recovery 僅支援 [長期維護通道 (LTSC) ](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 組建。  目前不支援[半年通道](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel)版本。

### <a name="ubuntu-kernel-versions"></a>Ubuntu 核心版本

**支援的版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | [9.33](https://support.microsoft.com/help/4564347/)、 [9.34](https://support.microsoft.com/help/4570609)、 [9.35](https://support.microsoft.com/help/4573888/)、 [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/) | 3.13.0-24-generic 至 3.13.0-170-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-148-generic、<br/>4.15.0-1023-azure 至 4.15.0-1045-azure |
|||
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic 至 4.4.0-189-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-115-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1093-azure |
16.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-generic 至 4.4.0-186-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-112-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1092-azure |
16.04 LTS | [9.35](https://support.microsoft.com/help/4573888/) | 4.4.0-21-generic 至 4.4.0-184-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-106-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1089-azure |
16.04 LTS | [9.34](https://support.microsoft.com/help/4570609) | 4.4.0-21-generic 至 4.4.0-178-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-101-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1083-azure |
16.04 LTS | [9.33](https://support.microsoft.com/help/4564347/) | 4.4.0-21-generic 至 4.4.0-178-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-99-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1082-azure|
|||
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic 至 4.15.0-115-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-泛型至 5.0.0-60-泛型 </br> 5.3.0-19-generic 至 5.3.0-66-generic </br> 5.4.0-37-generic 至 5.4.0-45-generic</br> 4.15.0-1009-azure 到 4.15.0-1093-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1035-azure </br> 5.4.0-1020-azure 到 5.4.0-1023-azure|
18.04 LTS | [9.36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-generic 至 4.15.0-112-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-泛型至 5.0.0-58-generic </br> 5.3.0-19-generic 至 5.3.0-64-generic </br> 5.4.0-37-generic 至 5.4.0-42-generic</br> 4.15.0-1009-azure 到 4.15.0-1092-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1032-azure </br> 5.4.0-1020-azure 到 5.4.0-1022-azure|
18.04 LTS | [9.35](https://support.microsoft.com/help/4573888/) | 4.15.0-20-generic 至 4.15.0-108-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-泛型至 5.0.0-52-泛型 </br> 5.3.0-19-generic 至 5.3.0-61-generic </br> 4.15.0-1009-azure 到 4.15.0-1089-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1031-azure|
18.04 LTS | [9.34](https://support.microsoft.com/help/4570609) | 4.15.0-20-generic 至 4.15.0-101-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-泛型至 5.0.0-48-泛型 </br> 5.3.0-19-generic 至 5.3.0-53-generic </br> 4.15.0-1009-azure 到 4.15.0-1083-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1022-azure|
18.04 LTS | [9.33](https://support.microsoft.com/help/4564347/) | 4.15.0-20-generic 至 4.15.0-99-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-泛型至 5.0.0-47-generic </br> 5.3.0-19-generic 至 5.3.0-51-generic </br> 4.15.0-1009-azure 到 4.15.0-1082-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1020-azure|
|||
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic 至 5.4.0-45 </br> -一般 5.4.0-1010-azure 到 5.4.0-1023-azure
20.04 LTS |[9.36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-generic 至 5.4.0-42 </br> -一般 5.4.0-1010-azure 到 5.4.0-1022-azure

### <a name="debian-kernel-versions"></a>Debian 核心版本


**支援的版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
Debian 7 | [9.33](https://support.microsoft.com/help/4564347/)、 [9.34](https://support.microsoft.com/help/4570609)、 [9.35](https://support.microsoft.com/help/4573888/)、 [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/)| 3.2.0-4-amd64 至 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.35](https://support.microsoft.com/help/4573888/)、 [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/) | 3.16.0-4-amd64 至 3.16.0-11-amd64、4.9.0 4.9.0-. bpo. 4-amd64 至 4.9.0 4.9.0-. bpo. 11-amd64 |
Debian 8 |  [9.33](https://support.microsoft.com/help/4564347/)、 [9.34](https://support.microsoft.com/help/4570609) | 3.16.0-4-amd64 至 3.16.0-10-amd64、4.9.0-0.bpo.4-amd64 至 4.9.0-0.bpo.11-amd64 |
|||
Debian 9。1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 至 4.9.0-13-amd64、4.19.0 4.9.0-. bpo. 6-amd64 至 4.19.0 4.9.0-. bpo. 10-amd64、4.19.0 4.9.0-. bpo. 6-a-a-i 至 4.19.0 4.9.0-. bpo. 10-雲端

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 支援的核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5)  | [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/) | 支援所有 [股票 SUSE 12 SP1、SP2、SP3、SP4](https://www.suse.com/support/kb/doc/?id=000019587) 核心。</br></br> 4.4.138-4.7-azure 至 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.22-azure  |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5)  | [9.35](https://support.microsoft.com/help/4573888/) | 支援所有 [股票 SUSE 12 SP1、SP2、SP3、SP4](https://www.suse.com/support/kb/doc/?id=000019587) 核心。</br></br> 4.4.138-4.7-azure 至 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.19-azure  |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5)  | [9.34](https://support.microsoft.com/help/4570609) | 支援所有 [股票 SUSE 12 SP1、SP2、SP3、SP4](https://www.suse.com/support/kb/doc/?id=000019587) 核心。</br></br> 4.4.138-4.7-azure 至 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.13-azure  |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4) | [9.33](https://support.microsoft.com/help/4564347/) | 支援所有 [股票 SUSE 12 SP1、SP2、SP3、SP4](https://www.suse.com/support/kb/doc/?id=000019587) 核心。</br></br> 4.4.138-4.7-azure 至 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 至 4.12.14-6.34-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 個支援的核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 和 15 SP1 | [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/)  | 根據預設，支援所有 [stock SUSE 15 和 15 核心](https://www.suse.com/support/kb/doc/?id=000019587)。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.38-azure
SUSE Linux Enterprise Server 15 和 15 SP1 | [9.35](https://support.microsoft.com/help/4573888/)  | 根據預設，支援所有 [stock SUSE 15 和 15 核心](https://www.suse.com/support/kb/doc/?id=000019587)。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.33-azure 
SUSE Linux Enterprise Server 15 和 15 SP1 | [9.34](https://support.microsoft.com/help/4570609)  | 根據預設，支援所有 [stock SUSE 15 和 15 核心](https://www.suse.com/support/kb/doc/?id=000019587)。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.30-azure 
SUSE Linux Enterprise Server 15 和 15 SP1 | [9.33](https://support.microsoft.com/help/4564347/) | 根據預設，支援所有 [stock SUSE 15 和 15 核心](https://www.suse.com/support/kb/doc/?id=000019587)。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.30-azure |

## <a name="linux-file-systemsguest-storage"></a>Linux 檔案系統/客體儲存體

**元件** | **支援**
--- | ---
檔案系統 | 根據此資料表的 ext3、ext4、XFS、>BTRFS (條件) 
邏輯磁片區管理 (LVM) 布建| 厚布建-是 <br></br> 精簡布建-否
磁碟區管理員 | -支援 LVM。<br/> - (行動服務) 更新版本9.20 的 [更新彙總套件 31](https://support.microsoft.com/help/4478871/) 支援 LVM 上的/boot。 先前的行動服務版本中不支援此功能。<br/> -不支援多個 OS 磁片。
並行虛擬存放裝置 | 不支援並行虛擬驅動程式所匯出的裝置。
多佇列區塊 IO 裝置 | 不支援。
使用 HP CCISS 儲存體控制器的實體伺服器 | 不支援。
裝置/掛接點的命名慣例 | 裝置名稱或掛接點名稱應該是唯一名稱。<br/> 確定兩個裝置/掛接點都沒有區分大小寫的名稱。 例如，不支援將相同 VM 的裝置命名為 *device1* 和 *device1* 。
目錄 | 如果您執行的版本是早于9.20 版的行動服務版本， (在 [更新彙總套件 31](https://support.microsoft.com/help/4478871/)) 中發行，則適用下列限制：<br/><br/> -這些目錄 (如果設定為個別的磁碟分割/檔案系統) 必須在來源伺服器的相同 OS 磁片上：/ (根目錄) 、/boot、/usr、/usr/local、/var、/etc。</br> -/Boot 目錄應該位於磁碟分割，而不是 LVM 磁片區。<br/><br/> 從9.20 版開始，這些限制並不適用。 
開機目錄 | -開機磁片不得為 GPT 磁碟分割格式。 這是 Azure 架構的限制。 GPT 磁片支援作為資料磁片。<br/><br/> 不支援 VM 上有多個開機磁片<br/><br/> -不支援在多個磁片上的 LVM 磁片區上/boot。<br/> -無法複寫沒有開機磁片的電腦。
可用空間需求| /root 分割區上為 2 GB <br/><br/> 安裝資料夾上為 250 MB
XFSv5 | XFS 檔案系統上的 XFSv5 功能（例如中繼資料總和檢查碼）支援 (行動服務9.10 版開始) 。<br/> 使用 xfs_info 公用程式來檢查磁碟分割的 XFS 超級區塊。 如果 `ftype` 設定為1，則會使用 XFSv5 功能。
>BTRFS |  (行動服務) 的 [更新彙總套件 34](https://support.microsoft.com/help/4490016) 9.22 版）支援 >btrfs。 如果有下列情況，則不支援 >BTRFS：<br/><br/> ->BTRFS 檔案系統 subvolume 會在啟用保護後變更。</br> ->BTRFS 檔案系統會散佈在多個磁片上。</br> ->BTRFS 檔案系統支援 RAID。

## <a name="vmdisk-management"></a>VM/磁碟管理

**動作** | **詳細資料**
--- | ---
在複寫的 VM 上調整磁碟大小 | 在容錯移轉之前于來源 VM 上支援，直接在 VM 屬性中。 不需要停用/重新啟用複寫。<br/><br/> 如果您在容錯移轉後變更來源 VM，則不會捕獲變更。<br/><br/> 如果您在容錯移轉後變更 Azure VM 上的磁片大小，當您容錯回復時，Site Recovery 會使用更新來建立新的 VM。
在複寫的 VM 上新增磁碟 | 不支援。<br/> 停用 VM 的複寫、新增磁片，然後重新啟用複寫。

> [!NOTE]
> 不支援對磁片身分識別進行任何變更。 例如，如果磁碟分割已從 GPT 變更為 MBR 或反之亦然，則這會變更磁片身分識別。 在這種情況下，複寫將會中斷，而且需要全新的設定。 

## <a name="network"></a>網路

**元件** | **支援**
--- | ---
主機網路 NIC 小組 | 支援 VMware VM。 <br/><br/>不支援實體機器複寫。
主機網路 VLAN | 可以。
主機網路 IPv4 | 可以。
主機網路 IPv6 | 否。
客體/伺服器網路 NIC 小組 | 否。
客體/伺服器網路 IPv4 | 可以。
客體/伺服器網路 IPv6 | 否。
客體/伺服器網路靜態 IP (Windows) | 可以。
客體/伺服器網路靜態 IP (Linux) | 可以。 <br/><br/>VM 設定為在容錯回復時使用 DHCP。
客體/伺服器網路多重 NIC | 可以。
Site Recovery 服務的 Private link 存取權 | 可以。 [深入了解](hybrid-how-to-enable-replication-private-endpoints.md)。


## <a name="azure-vm-network-after-failover"></a>Azure VM 網路 (容錯移轉後)

**元件** | **支援**
--- | ---
Azure ExpressRoute | 是
ILB | 是
ELB | 是
Azure 流量管理員 | 是
多個 NIC | 是
保留的 IP 位址 | 是
IPv4 | 是
保留來源 IP 位址 | 是
Azure 虛擬網路服務端點<br/> | 是
加速網路 | 否

## <a name="storage"></a>儲存體
**元件** | **支援**
--- | ---
動態磁碟 | 作業系統磁片必須是基本磁碟。 <br/><br/>資料磁碟可以是動態磁碟
Docker 磁碟設定 | 否
主機 NFS | VMware 為是<br/><br/> 實體伺服器為否
主機 SAN (iSCSI/FC) | 是
主機 vSAN | VMware 為是<br/><br/> 實體伺服器為 N/A
主機多重路徑 (MPIO) | 是，通過 Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON 測試
主機虛擬磁碟區 (VVol) | VMware 為是<br/><br/> 實體伺服器為 N/A
客體/伺服器 VMDK | 是
客體/伺服器共用叢集磁碟 | 否
客體/伺服器加密磁碟 | 否
客體/伺服器 NFS | 否
來賓/伺服器 iSCSI | 適用于遷移-是<br/>若為嚴重損壞修復-否，iSCSI 會以連結的磁片容錯回復至 VM
客體/伺服器 SMB 3.0 | 否
客體/伺服器 RDM | 是<br/><br/> 實體伺服器為 N/A
客體/伺服器磁碟 > 1 TB | 是，磁片必須大於 1024 MB<br/><br/>複寫至受控磁片時，最高可達 8192 GB (9.26 版) <br></br> 複寫至儲存體帳戶時最高達 4095 GB
客體/伺服器磁碟使用 4K 邏輯與 4k 實體磁區大小 | 否
具有4K 邏輯和 512-位元組實體磁區大小的來賓/伺服器磁片 | 否
客體/伺服器磁碟區使用等量磁碟 > 4 TB | 是
邏輯磁碟區管理 (LVM)| 厚布建-是 <br></br> 精簡布建-否
客體/伺服器 - 儲存體空間 | 否
客體/伺服器 熱新增/移除磁碟 | 否
客體/伺服器 - 排除磁碟 | 是
客體/伺服器多重路徑 (MPIO) | 否
來賓/伺服器 GPT 磁碟分割 | [更新彙總套件 37](https://support.microsoft.com/help/4508614/)支援五個磁碟分割 (行動服務) 的版本9.25。 先前支援四個。
ReFS | 行動服務9.23 版或更高版本支援復原檔案系統
來賓/伺服器 EFI/UEFI 開機 | -支援所有具有 Site Recovery 行動代理程式9.30 版的 [Azure MARKETPLACE UEFI 作業系統](../virtual-machines/windows/generation-2.md#generation-2-vm-images-in-azure-marketplace) 。 <br/> -不支援安全 UEFI 開機類型。 [深入了解。](../virtual-machines/windows/generation-2.md#on-premises-vs-azure-generation-2-vms)

## <a name="replication-channels"></a>複寫通道

|**複寫的類型**   |**支援**  |
|---------|---------|
|卸載資料傳輸 (ODX)     |       否  |
|離線植入        |   否      |
| Azure 資料箱 | 否

## <a name="azure-storage"></a>Azure 儲存體

**元件** | **支援**
--- | ---
本地備援儲存體 | 是
異地備援儲存體 | 是
讀取權限異地備援儲存體 | 是
非經常性儲存體 | 否
經常性存取儲存體| 否
區塊 Blob | 否
靜態加密 (SSE) | 是
靜態加密 (CMK) | 是 (via PowerShell Az 3.3.0 module) 
靜態加密 | 是 (via) 的 PowerShell Az 3.3.0 模組。 深入瞭解適用于 [Windows](../virtual-machines/windows/disk-encryption.md) 和 [Linux](../virtual-machines/linux/disk-encryption.md)的支援區域。
進階儲存體 | 是
安全傳輸選項 | 是
匯入/匯出服務 | 否
適用于 Vnet 的 Azure 儲存體防火牆 | 可以。<br/> 設定在目標儲存體/快取儲存體帳戶上 (用來儲存複寫資料) 。
一般用途 v2 儲存體帳戶 (經常性存取層和非經常性存取層)  | 是，V2 相較于 V1) ， (的交易成本明顯較高

## <a name="azure-compute"></a>Azure 計算

**功能** | **支援**
--- | ---
可用性設定組 | 是
可用性區域 | 否
中樞 | 是
受控磁碟 | 是

## <a name="azure-vm-requirements"></a>Azure VM 需求

複寫到 Azure 的內部部署 Vm 必須符合下表中摘要說明的 Azure VM 需求。 當 Site Recovery 執行複寫的必要條件檢查時，如果不符合某些需求，檢查將會失敗。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | 驗證複寫的機器[所支援的作業系統](#replicated-machines)。 | 若不支援，則檢查會失敗。
客體作業系統架構 | 64 位元。 | 若不支援，則檢查會失敗。
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 </br> 不支援在不同磁片上的開機和系統磁碟分割 | 若不支援，則檢查會失敗。
資料磁碟計數 | 64 或以下。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 複寫至受控磁片時，最高可達 8192 GB)  (9.26 版<br></br>複寫至儲存體帳戶時最高達 4095 GB| 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。 |
VM 名稱 | 從 1 到 63 個字元。<br/><br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。

## <a name="resource-group-limits"></a>資源群組限制

若要瞭解可在單一資源群組下受到保護的虛擬機器數目，請參閱訂用帳戶 [限制和配額](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)的文章。

## <a name="churn-limits"></a>流失限制

下表提供 Azure Site Recovery 限制。
- 這些限制是以我們的測試為基礎，但不涵蓋所有可能的應用程式 i/o 組合。
- 實際的結果會隨著您的應用程式 I/O 混合而有所不同。
- 為了獲得最佳結果，強烈建議您執行 [部署規劃工具工具](site-recovery-deployment-planner.md)，並使用測試容錯移轉執行大量的應用程式測試，為您的應用程式取得真正的效能圖片。

**複寫目標** | **平均來源磁片 i/o 大小** |**平均來源磁碟資料變換** | **每日的來源磁碟資料變換總計**
---|---|---|---
標準儲存體 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |    每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |20 MB/秒 | 每個磁碟 1684 GB


**來源資料變換** | **上限**
---|---
VM 上所有磁碟的尖峰資料變換 | 54 MB/秒
處理序伺服器支援的每日資料變換上限 | 2 TB

- 以上是採用 30% I/O 重疊時的平均數字。
- Site Recovery 能夠處理更高的輸送量 (以重疊比為基礎)、較大的寫入大小和實際工作負載 I/O 行為。
- 這些數位假設有大約五分鐘的一般待處理專案。 也就是說，資料上傳之後，便會進行處理並在五分鐘內建立復原點。

## <a name="vault-tasks"></a>保存庫工作

**動作** | **支援**
--- | ---
在資源群組間移動保存庫 | 否
移動訂用帳戶內和跨訂用帳戶 | 否
跨資源群組間移動儲存體、網路、Azure VM | 否
在訂用帳戶內和跨訂用帳戶移動儲存體、網路、Azure Vm。 | 否


## <a name="obtain-latest-components"></a>取得最新的元件

**名稱** | **說明** | **詳細資料**
--- | --- | ---
組態伺服器 | 已安裝在內部部署環境。<br/> 協調內部部署 VMware 伺服器或實體機器與 Azure 之間的通訊。 | - [瞭解](vmware-physical-azure-config-process-server-overview.md) 設定伺服器。<br/> - [瞭解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) 如何升級至最新版本。<br/> - [瞭解](vmware-azure-deploy-configuration-server.md) 設定伺服器的設定。
處理序伺服器 | 預設會安裝在組態伺服器上。<br/> 接收復寫資料、以快取、壓縮和加密進行優化，然後將其傳送至 Azure。<br/> 當您的部署成長時，您可以新增額外的進程伺服器來處理更大量的複寫流量。 | - [瞭解](vmware-physical-azure-config-process-server-overview.md) 進程伺服器。<br/> - [瞭解](vmware-azure-manage-process-server.md#upgrade-a-process-server) 如何升級至最新版本。<br/> - [瞭解如何](vmware-physical-large-deployment.md#set-up-a-process-server) 設定相應放大進程伺服器。
行動服務 | 安裝在您想要複寫的 VMware VM 或實體伺服器上。<br/> 協調內部部署 VMware 伺服器/實體伺服器與 Azure 之間的複寫。| - [瞭解](vmware-physical-mobility-service-overview.md) 行動服務。<br/> - [瞭解](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) 如何升級至最新版本。<br/>



## <a name="next-steps"></a>後續步驟
[了解如何](tutorial-prepare-azure.md)準備 Azure 的 VMware VM 災害復原。

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery