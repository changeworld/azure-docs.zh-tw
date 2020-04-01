---
title: Azure 網站恢復中 VMware/物理災難恢復的支援矩陣
description: 使用 Azure 網站恢復匯總對 VMware VM 和物理伺服器的災難恢復的支援。
ms.topic: conceptual
ms.date: 2/24/2020
ms.openlocfilehash: b4cf19f4f74ba24951efb806a9f2e3d88fcad7bc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478420"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>從 VMware VM 和實體伺服器至 Azure 之災害復原的支援矩陣

本文總結了支援的元件和設置,用於使用[Azure 網站恢復](site-recovery-overview.md)將 VMware VM 和物理伺服器的災難恢復到 Azure。

- [瞭解有關](vmware-azure-architecture.md)VMware VM/物理伺服器災難恢復體系結構的更多資訊。
- 請按照我們的[教程](tutorial-prepare-azure.md)嘗試災難恢復。

## <a name="deployment-scenarios"></a>部署案例

**案例** | **詳細資料**
--- | ---
VMware VM 的災難復原 | 將內部部署 VMware 虛擬機器複寫至 Azure。 可以在 Azure 門戶中或使用[PowerShell](vmware-azure-disaster-recovery-powershell.md)部署此方案。
實體伺服器的災難復原 | 將內部部署 Windows/Linux 實體伺服器複寫至 Azure。 您可以在 Azure 入口網站中部署此案例。

## <a name="on-premises-virtualization-servers"></a>內部部署虛擬化伺服器

**Server** | **需求** | **詳細資料**
--- | --- | ---
vCenter Server | 版本 6.7、6.5、6.0 或 5.5 | 我們建議您在災難恢復部署中使用 vCenter 伺服器。
vSphere 主機 | 版本 6.7、6.5、6.0 或 5.5 | 我們建議 vSphere 主機與 vCenter 伺服器應位於和處理序伺服器相同的網路中。 預設情況下,進程伺服器在配置伺服器上運行。 [深入了解](vmware-physical-azure-config-process-server-overview.md)。


## <a name="site-recovery-configuration-server"></a>Site Recovery 組態伺服器

組態伺服器屬於內部部署機器，可執行 Site Recovery 元件，包括組態伺服器、處理序伺服器和主要目標伺服器。

- 對於 VMware VM,您可以通過下載 OVF 樣本來創建 VMware VM 來設置配置伺服器。
- 對於物理伺服器,您可以手動設置配置伺服器計算機。

**元件** | **需求**
--- |---
CPU 核心 | 8
RAM | 16 GB
磁碟數量 | 3 個磁碟<br/><br/> 磁碟包括作業系統磁碟、處理序伺服器快取磁碟和用於容錯回復的保留磁碟機。
磁碟可用空間 | 進程伺服器緩存的 600 GB 空間。
磁碟可用空間 | 600 GB 的空間用於保留驅動器。
作業系統  | 具有桌面體驗的 Windows 伺服器 2012 R2 或 Windows 伺服器 2016 <br/><br> 如果計劃使用此設備的內建主目標進行故障倒轉,請確保作業系統版本與複製的專案相同或更高。|
作業系統地區設定 | 英文 (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | 配置伺服器版本[9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)或更高版本不需要。
Windows Server 角色 | 不啟用活動目錄域服務;互聯網資訊服務 (IIS) 或超 V。
群組原則| - 防止存取命令提示字元。 <br/> - 防止存取登錄編輯工具。 <br/> - 檔案附件的信任邏輯。 <br/> - 開啟指令碼執行。 <br/> - [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 請確定您已執行下列動作：<br/><br/> - 沒有預先存在的預設網站 <br/> - 啟用[匿名驗證](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br/> - 啟用 [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 設定  <br/> - 沒有預先存在的網站/應用程式接聽連接埠 443<br/>
NIC 類型 | VMXNET3 (部署為 VMware VM 時)
IP 位址類型 | 靜態
連接埠 | 443 用於控制通道編排<br/>9443 用於資料傳輸

## <a name="replicated-machines"></a>複寫的電腦

Site Recovery 支援複寫任何執行於所支援機器上的工作負載。

> [!Note]
> 下表列出了對具有 BIOS 啟動的電腦的支援。 有關基於 UEFI 的電腦的支援,請參閱[存儲](#storage)部分。

**元件** | **詳細資料**
--- | ---
機器設定 | 複寫到 Azure 的電腦必須符合 [Azure 需求](#azure-vm-requirements)。
機器工作負載 | Site Recovery 支援複寫任何執行於所支援機器上的工作負載。 [深入了解](site-recovery-workload.md)。
Windows Server 2019 | 支援[從更新匯總 34](https://support.microsoft.com/help/4490016) (版本 9.22 的行動服務) 開始.
視窗伺服器 2016 64 位元 | 支援伺服器核心,具有桌面體驗的伺服器。
視窗伺服器 2012 R2 / 視窗伺服器 2012 | 支援。
Windows 伺服器 2008 R2 與 SP1 開始。 | 支援。<br/><br/> 從行動服務代理[的 9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)版本開始,您需要在執行 Windows 2008 R2 的帶有 SP1 或更高版本的電腦上安裝[服務堆疊更新 (SSU)](https://support.microsoft.com/help/4490628)和[SHA-2 更新](https://support.microsoft.com/help/4474419)。 從 2019 年 9 月起不支援 SHA-1,如果未啟用 SHA-2 代碼簽名,代理擴展將不會按預期安裝/升級。 瞭解有關[SHA-2 升級和要求](https://aka.ms/SHA-2KB)的更多。
帶 SP2 或更高版本的 Windows 伺服器 2008 (64 位元/32 位元) |  僅支援遷移。 [深入了解](migrate-tutorial-windows-server-2008.md)。<br/><br/> 從行動服務代理[的 9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)版本開始,您需要在 Windows 2008 SP2 電腦上安裝[服務堆疊更新 (SSU)](https://support.microsoft.com/help/4493730)和[SHA-2 更新](https://support.microsoft.com/help/4474419)。 從 2019 年 9 月起不支援 ISHA-1,如果未啟用 SHA-2 代碼簽名,代理擴展將不會按預期安裝/升級。 瞭解有關[SHA-2 升級和要求](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)的更多。
Windows 10、Windows 8.1、Windows 8 | 支援。
Sp1 64 位元的 Windows 7 | 支援[從更新匯總 36](https://support.microsoft.com/help/4503156) (版本 9.22 的行動服務) 開始. </br></br> 從[行動服務代理的 9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)開始,您需要在 Windows 7 SP1 電腦上安裝[服務堆疊更新 (SSU)](https://support.microsoft.com/help/4490628)和[SHA-2 更新](https://support.microsoft.com/help/4474419)。  從 2019 年 9 月起不支援 SHA-1,如果未啟用 SHA-2 代碼簽名,代理擴展將不會按預期安裝/升級。 瞭解有關[SHA-2 升級和要求](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)的更多。
Linux | 僅支援 64 位元系統。 不支援 32 位系統。<br/><br/>每個 Linux 伺服器都應安裝[Linux 整合服務 (LIS) 元件](https://www.microsoft.com/download/details.aspx?id=55106)。 測試故障轉移/故障轉移後,需要在 Azure 中啟動伺服器。 如果缺少 LIS 元件,請確保在啟用複製之前安裝[元件](https://www.microsoft.com/download/details.aspx?id=55106),以便電腦在 Azure 中啟動。 <br/><br/> Site Recovery 會協調容錯移轉以在 Azure 中執行 Linux 伺服器。 不過，Linux 廠商可能會將支援僅限於生命週期尚未結束的發行版本。<br/><br/> 在 Linux 散發套件上，僅支援屬於散發套件次要版本/更新的庫存核心。<br/><br/> 不支援升級各主要 Linux 散發套件版本的受保護機器。 若要升級，請停用複寫、升級作業系統，然後再次啟用複寫。<br/><br/> [詳細瞭解](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)Azure 中對 Linux 和開源技術的支援。
Linux 紅帽企業 | 5.2 到 5.11</b><br/> 6.1 到 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7,](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [8.0,](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)8.1 <br/> 執行紅帽企業 Linux 5.2-5.11 & 6.1-6.10 的伺服器沒有預安裝[Linux 整合服務 (LIS) 元件](https://www.microsoft.com/download/details.aspx?id=55106)。 確保在啟用複製之前安裝[元件](https://www.microsoft.com/download/details.aspx?id=55106),以便電腦在 Azure 中啟動。
Linux：CentOS | 5.2 到 5.11</b><br/> 6.1 到 6.10</b><br/> 7.0 到 7.6<br/> <br/> 8.0 到 8.1<br/><br/> 執行 CentOS 5.2-5.11 & 6.1-6.10 的伺服器沒有預安裝[Linux 整合服務 (LIS) 元件](https://www.microsoft.com/download/details.aspx?id=55106)。 確保在啟用複製之前安裝[元件](https://www.microsoft.com/download/details.aspx?id=55106),以便電腦在 Azure 中啟動。
Ubuntu | Ubuntu 14.04 LTS 伺服器[(查看支援的內核版本)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS 伺服器[(查看支援的內核版本)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS 伺服器[(查看支援的內核版本)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian [8(查看支援的內核版本)](#debian-kernel-versions)
SUSE Linux | SUSE Linux 企業伺服器 12 SP1、SP2、SP3、SP4([查看支援的內核版本)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux 企業伺服器 15, 15 SP1 [(查看支援的核心版本)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3、SUSE Linux Enterprise Server 11 SP4<br/> 不支援將複寫的機器從 SUSE Linux Enterprise Server 11 SP3 升級到 SP4。 要升級,請禁用複製並在升級後重新啟用。
Oracle Linux | 6.4、 6.5、 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> 執行紅帽相容內核或牢不可破的企業內核版本 3、4 & 5 (UEK3、 UEK4、 UEK5)

> [!Note]
> 對於每個 Windows 版本,Azure 網站恢復僅支援[長期服務通道 (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)生成。  [目前不支援半年頻道](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel)版本。

### <a name="ubuntu-kernel-versions"></a>Ubuntu 核心版本

**支援的版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24 泛型到 3.13.0-170 泛型,<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21 泛型到 4.4.0-148 泛型,<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24 泛型到 3.13.0-170 泛型,<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21 泛型到 4.4.0-148 泛型,<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24 泛型到 3.13.0-170 泛型,<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21 泛型到 4.4.0-148 泛型,<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24 泛型到 3.13.0-170 泛型,<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21 泛型到 4.4.0-148 泛型,<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21 泛型到 4.4.0-171 泛型,<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13 泛型到 4.15.0-74 泛型<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21 泛型到 4.4.0-170 泛型,<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13 泛型到 4.15.0-72 泛型<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21 泛型到 4.4.0-166 泛型,<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13 泛型到 4.15.0-66 泛型<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21 泛型到 4.4.0-164 泛型,<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13 泛型到 4.15.0-64 泛型<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20 泛型到 4.15.0-74 泛型 </br> 4.18.0-13 泛型到 4.18.0-25 泛型 </br> 5.0.0-15 泛型到 5.0.0-37 泛型 </br> 5.3.0-19 泛型到 5.3.0-24 泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1028-azure </br> 5.3.0-1007-azure 到 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20 泛型到 4.15.0-72 泛型 </br> 4.18.0-13 泛型到 4.18.0-25 泛型 </br> 5.0.0-15 泛型到 5.0.0-37 泛型 </br> 5.3.0-19 泛型到 5.3.0-24 泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20 泛型到 4.15.0-66 泛型 </br> 4.18.0-13 泛型到 4.18.0-25 泛型 </br> 5.0.0-15 泛型到 5.0.0-32 泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20 泛型到 4.15.0-62 泛型 </br> 4.18.0-13 泛型到 4.18.0-25 泛型 </br> 5.0.0-15 泛型到 5.0.0-27 泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Debian 核心版本


**支援的版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
Debian 7 | [9.29][9.29 UR], [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR]| 3.2.0-4-amd64 至 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR] | 3.16.0-4-amd64 至 3.16.0-10-amd64,4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 至 3.16.0-10-amd64,4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 支援的核心版本

**釋放** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux 企業伺服器 12 (SP1,SP2,SP3,SP4) | [9.32][9.32 UR] | 支援所有[庫存 SUSE 12 SP1、SP2、SP3、SP4 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure,</br>4.12.14-6.3-azure 至 4.12.14-6.34-azure  |
SUSE Linux 企業伺服器 12 (SP1,SP2,SP3,SP4) | [9.31][9.31 UR] | 支援所有[庫存 SUSE 12 SP1、SP2、SP3、SP4 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure,</br>4.12.14-6.3-azure 至 4.12.14-6.29-azure  |
SUSE Linux 企業伺服器 12 (SP1,SP2,SP3,SP4) | [9.30][9.30 UR] | 支援所有[庫存 SUSE 12 SP1、SP2、SP3、SP4 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure,</br>4.12.14-6.3-azure 至 4.12.14-6.26-azure  |
SUSE Linux 企業伺服器 12 (SP1,SP2,SP3,SP4) | [9.29][9.29 UR] | 支援所有[庫存 SUSE 12 SP1、SP2、SP3、SP4 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure,</br>4.12.14-6.3-azure 至 4.12.14-6.23-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux 企業伺服器 15 支援核心版本

**釋放** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux 企業伺服器 15 和 15 SP1 | 9.32 | 支援所有[股票 SUSE 15 與 15 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15)。</br></br> 4.12.14-5.5-azure 至 4.12.14-8.22-azure |

## <a name="linux-file-systemsguest-storage"></a>Linux 檔案系統/客體儲存體

**元件** | **支援**
--- | ---
檔案系統 | 分機3,分機4,XFS,BTRFS(本表適用的條件)
磁碟區管理員 | - 支援 LVM。<br/> - 從[更新匯總 31(](https://support.microsoft.com/help/4478871/)行動服務版本 9.20)開始支援 LVM 上的 /啟動。 在早期的行動服務版本中不支援它。<br/> - 不支援多個作業系統磁碟。
並行虛擬存放裝置 | 不支援並行虛擬驅動程式所匯出的裝置。
多佇列區塊 IO 裝置 | 不支援。
使用 HP CCISS 儲存體控制器的實體伺服器 | 不支援。
裝置/掛接點的命名慣例 | 裝置名稱或掛接點名稱應該是唯一名稱。<br/> 確保沒有兩個設備/安裝點具有區分大小寫的名稱。 例如,不支援將同一 VM 的設備命名為*設備 1* *和設備1。*
目錄 | 如果您執行的行動服務版本早於版本 9.20(在[更新匯總 31](https://support.microsoft.com/help/4478871/)中發表),則這些限制適用:<br/><br/> - 這些目錄(如果設置為單獨的分區/檔案系統)必須位於源伺服器上的同一操作系統磁碟上:/(根)、/引導、/usr、/usr/local、/var、/等。</br> - /boot 目錄應位於磁碟分割區上,而不是 LVM 卷。<br/><br/> 從版本 9.20 開始,這些限制不適用。
開機目錄 | - 引導磁碟不能採用 GPT 分區格式。 這是 Azure 體系結構限制。 GPT 磁碟支援為數據磁碟。<br/><br/> 不支援虛擬機器上的多個開機磁碟<br/><br/> - 不支援跨多個磁碟在 LVM 捲上啟動/ 啟動。<br/> - 無法複製沒有引導磁碟的電腦。
可用空間需求| /root 分割區上為 2 GB <br/><br/> 安裝資料夾上為 250 MB
XFSv5 | 支援 XFS 檔案系統上的 XFSv5 功能,如中繼資料驗證和(行動服務版本 9.10 以後)。<br/> 使用 xfs_info 公用程式來檢查磁碟分割的 XFS 超級區塊。 如果`ftype`設置為 1,則使用 XFSv5 功能。
BTRFS | BTRFS 從[更新匯總 34(](https://support.microsoft.com/help/4490016)行動服務版本 9.22)開始提供支援。 如果:<br/><br/> - 啟用保護後更改 BTRFS 檔案系統子捲。</br> - BTRFS 檔案系統分佈在多個磁碟上。</br> - BTRFS 檔案系統支援 RAID。

## <a name="vmdisk-management"></a>VM/磁碟管理

**動作** | **詳細資料**
--- | ---
在複寫的 VM 上調整磁碟大小 | 故障轉移前在源 VM 上支援,直接在 VM 屬性中支援。 無需禁用/重新啟用複製。<br/><br/> 如果在故障轉移後更改源 VM,則更改不會捕獲。<br/><br/> 如果在故障轉移後更改 Azure VM 上的磁碟大小,則在故障恢復時,網站恢復將創建一個包含更新的新 VM。
在複寫的 VM 上新增磁碟 | 不支援。<br/> 禁用 VM 的複製,添加磁碟,然後重新啟用複製。

## <a name="network"></a>網路

**元件** | **支援**
--- | ---
主機網路 NIC 小組 | 支援 VMware VM。 <br/><br/>不支援實體機器複寫。
主機網路 VLAN | 是。
主機網路 IPv4 | 是。
主機網路 IPv6 | 否。
客體/伺服器網路 NIC 小組 | 否。
客體/伺服器網路 IPv4 | 是。
客體/伺服器網路 IPv6 | 否。
客體/伺服器網路靜態 IP (Windows) | 是。
客體/伺服器網路靜態 IP (Linux) | 是。 <br/><br/>VM 設定為在容錯回復時使用 DHCP。
客體/伺服器網路多重 NIC | 是。


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
動態磁碟 | 操作系統磁碟必須是基本磁碟。 <br/><br/>資料磁碟可以是動態磁碟
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
訪客/伺服器 iSCSI | 對移動移到<br/>對於災難復原 - 否,iSCSI 將作為連接到 VM 的磁碟故障回退
客體/伺服器 SMB 3.0 | 否
客體/伺服器 RDM | 是<br/><br/> 實體伺服器為 N/A
客體/伺服器磁碟 > 1 TB | 是,磁碟必須大於 1024 MB<br/><br/>複製到託管磁碟時高達 8,192 GB(9.26 版本以後)<br></br> 複製到儲存帳戶時高達 4,095 GB
客體/伺服器磁碟使用 4K 邏輯與 4k 實體磁區大小 | 否
具有 4K 邏輯和 512 位元組物理扇區大小的來賓/伺服器磁碟 | 否
客體/伺服器磁碟區使用等量磁碟 > 4 TB <br/><br/>邏輯磁碟區管理 (LVM)| 是
客體/伺服器 - 儲存體空間 | 否
客體/伺服器 熱新增/移除磁碟 | 否
客體/伺服器 - 排除磁碟 | 是
客體/伺服器多重路徑 (MPIO) | 否
來賓/伺服器 GPT 分割區 | [從更新匯總 37(](https://support.microsoft.com/help/4508614/)行動服務版本 9.25)開始,支援五個分區。 以前有四個支援。
ReFS | 移動服務版本 9.23 或更高版本支援彈性檔案系統
來賓/伺服器 EFI/UEFI 啟動 | - 支援 Windows 伺服器 2012 或更高版本, SLES 12 SP4 和 RHEL 8.0 與行動代理版本 9.30 從<br/> - 不支援安全 UEFI 啟動類型。

## <a name="replication-channels"></a>複製通道

|**複製類型**   |**支援**  |
|---------|---------|
|卸載資料傳輸 (ODX)    |       否  |
|離線種子設定        |   否      |
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
靜態加密 (SSE)| 是
靜態加密 (CMK)| 是(透過 Powershell Az 3.3.0 模組開始)
進階儲存體 | 是
匯入/匯出服務 | 否
VNet 的 Azure 儲存防火牆 | 是。<br/> 在目標存儲/快取儲存帳戶上配置(用於儲存複製資料)。
通用 v2 儲存帳戶(熱層和冷層) | 是(與 V1 相比,V2 的交易成本要高得多)

## <a name="azure-compute"></a>Azure 計算

**功能** | **支援**
--- | ---
可用性設定組 | 是
可用性區域 | 否
中樞 | 是
受控磁碟 | 是

## <a name="azure-vm-requirements"></a>Azure VM 需求

複製到 Azure 的本地 VM 必須滿足此表中總結的 Azure VM 要求。 當網站恢復運行複製的先決條件檢查時,如果某些要求未得到滿足,檢查將失敗。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | 驗證複寫的機器[所支援的作業系統](#replicated-machines)。 | 若不支援，則檢查會失敗。
客體作業系統架構 | 64 位元。 | 若不支援，則檢查會失敗。
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則檢查會失敗。
資料磁碟計數 | 64 或以下。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 複製到託管磁碟時高達 8,192 GB(9.26 版本自)<br></br>複製到儲存帳戶時高達 4,095 GB| 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。 |
VM 名稱 | 從 1 到 63 個字元。<br/><br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。

## <a name="resource-group-limits"></a>資源組限制

要瞭解可在單個資源組下保護的虛擬機器數,請參閱有關[訂閱限制和配額](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)的文章。

## <a name="churn-limits"></a>流失限制

下表提供 Azure Site Recovery 限制。
- 這些限制基於我們的測試,但不包括所有可能的應用 I/O 組合。
- 實際的結果會隨著您的應用程式 I/O 混合而有所不同。
- 為獲得最佳結果,我們強烈建議您運行[部署規劃器工具](site-recovery-deployment-planner.md),並使用測試故障轉移執行廣泛的應用程式測試,以獲得應用的真實性能圖景。

**複製目標** | **平均來源磁碟 I/O 大小** |**平均來源磁碟資料改動** | **每天源磁碟資料總改動**
---|---|---|---
標準儲存體 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |    每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |20 MB/s | 每個磁碟 1684 GB


**來源資料變換** | **最大限度**
---|---
VM 上所有磁碟的尖峰資料變換 | 54 MB/秒
處理序伺服器支援的每日資料變換上限 | 2 TB

- 以上是採用 30% I/O 重疊時的平均數字。
- Site Recovery 能夠處理更高的輸送量 (以重疊比為基礎)、較大的寫入大小和實際工作負載 I/O 行為。
- 這些數位假定典型的積壓大約五分鐘。 也就是說，資料上傳之後，便會進行處理並在五分鐘內建立復原點。

## <a name="vault-tasks"></a>保存庫工作

**動作** | **支援**
--- | ---
在資源群組間移動保存庫 | 否
在訂閱內和跨訂閱中移動保管庫 | 否
跨資源群組間移動儲存體、網路、Azure VM | 否
在訂閱內部和跨訂閱中移動存儲、網路、Azure VM。 | 否


## <a name="obtain-latest-components"></a>取得最新元件

**名稱** | **說明** | **詳細資料**
--- | --- | ---
組態伺服器 | 安裝在本地。<br/> 協調本地 VMware 伺服器或物理電腦與 Azure 之間的通訊。 | - [瞭解](vmware-physical-azure-config-process-server-overview.md)配置伺服器。<br/> - [瞭解如何](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)升級到最新版本。<br/> - [瞭解如何](vmware-azure-deploy-configuration-server.md)設置配置伺服器。
處理序伺服器 | 預設會安裝在組態伺服器上。<br/> 接收複製數據,使用緩存、壓縮和加密對其進行優化,並將其發送到 Azure。<br/> 隨著部署的增長,您可以添加其他進程伺服器來處理更大的複製流量。 | - [了解](vmware-physical-azure-config-process-server-overview.md)進程伺服器。<br/> - [瞭解如何](vmware-azure-manage-process-server.md#upgrade-a-process-server)升級到最新版本。<br/> - [瞭解如何](vmware-physical-large-deployment.md#set-up-a-process-server)設置橫向擴展進程伺服器。
行動服務 | 安裝在要複製的 VMware VM 或物理伺服器上。<br/> 協調本地 VMware 伺服器/ 物理伺服器和 Azure 之間的複製。| - [了解](vmware-physical-mobility-service-overview.md)行動服務。<br/> - [瞭解如何](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)升級到最新版本。<br/>



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
