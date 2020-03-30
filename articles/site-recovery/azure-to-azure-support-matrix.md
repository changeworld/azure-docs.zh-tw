---
title: 支援使用 Azure 網站恢復恢復 Azure VM 災害復原
description: 將 Azure VM 災害復原的支援匯總到具有 Azure 網站恢復的次要區域。
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: 0b4a654093e0842e66e1f8b0924edfa6c9c215e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276643"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure VM 災害復原在 Azure 區域之間的支援矩陣

本文總結了使用[Azure 網站恢復](site-recovery-overview.md)服務將 Azure VM 從一個 Azure 區域恢復到另一個 Azure 區域的支援和先決條件。


## <a name="deployment-method-support"></a>部署方法的支援

**部署** |  **支援**
--- | ---
**Azure 門戶** | 支援。
**電源外殼** | 支援。 [深入了解](azure-to-azure-powershell.md)
**REST API** | 支援。
**Cli** | 目前不支援


## <a name="resource-support"></a>資源支援

**資源動作** | **詳細資料**
--- | ---
**跨資源組移動保存庫** | 不支援
**跨資源群組移動計算/儲存體/網路資源** | 不支援。<br/><br/> 如果您在 VM 複寫之後移動 VM 或是相關聯的元件 (例如儲存體/網路)，您必須停用該 VM 的複寫，然後再重新啟用複寫。
**將 Azure VM 從某個訂用帳戶複寫至另一個以進行災害復原** | 在相同的 Azure Active Directory 租用戶中支援。
**在支援的地理叢集 (在訂用帳戶內或跨訂用帳戶) 內移轉區域之間的 VM** | 在相同的 Azure Active Directory 租用戶中支援。
**在相同區域內移轉 VM** | 不支援。

## <a name="region-support"></a>區域支援

您可以複製和復原相同的地理叢集內任何兩個區域之間的 VM。 地理叢集會是以資料延遲及主權範圍定義而成。


**地理叢集** | **Azure 區域**
-- | --
America | 加拿大東部、加拿大中部、美國中南部、美國中西部、美國東部、美國東部 2、美國西部、美國西部 2、美國中部、美國中北部
歐洲 | 英國西部、英國南部、北歐、西歐、法國中部、法國南部、南非西部、南非北部、挪威東部、挪威西部
Asia | 印度南部、印度中部、西印度、東南亞、東亞、日本東部、日本西部、韓國中部、韓國南部、阿聯酋中部、阿聯酋北部
澳大利亞    | 澳大利亞東部、澳大利亞東南部、澳大利亞中部、澳大利亞中部 2
Azure Government    | US Gov 維吉尼亞州、US Gov 愛荷華州、US Gov 亞利桑那州、US Gov 德克薩斯州、US DoD 東部、US DoD 中部
德國    | 德國中部、德國東北部
中國 | 中國東部、中國北部、中國北部 2、中國東部 2
為國內災害復原保留的受限區域 |德國北保留為德國西中，瑞士西保留瑞士北部，法國南部保留為法國中央客戶

>[!NOTE]
>
> - 對於**巴西南部**，您可以複製和容錯移轉到這些地區：美國中南部、美國中西部、美國東部、美國東部 2、美國西部、美國西部 2 和美國中北部。
> - 巴西南部只能用作源區域，VM 可以從中複製使用網站恢復。 它不能作為目的地區域。 這是因為由於地理距離而導致的延遲問題。 請注意，如果從巴西南部故障作為源區域容錯移轉到目標，則支援從目的地區域故障回巴西南部。
> - 您可以在具有適當存取權限的區域內工作。
> - 如果要在其中創建保存庫的區域未顯示，請確保訂閱有權訪問在該區域創建資源。
> - 如果在啟用複製時看不到地理群集中的區域，請確保訂閱具有在該區域中創建 VM 的許可權。



## <a name="cache-storage"></a>快取儲存體

下表摘要說明複寫期間 Site Recovery 使用的快取儲存體帳戶支援。

**設定** | **支援** | **詳細資料**
--- | --- | ---
一般用途 V2 儲存體帳戶 (經常性存取層和非經常性存取層) | 支援 | 不建議使用 GPv2，因為 V2 的交易成本遠遠高於 V1 存儲帳戶。
進階儲存體 | 不支援 | 標準存儲帳戶用於緩存存儲，以説明優化成本。
適用於虛擬網路的 Azure 儲存體防火牆  | 支援 | 如果您使用啟用防火牆的快取儲存體帳戶或目標儲存體帳戶，請確定您[「允許信任的 Microsoft 服務」](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)。<br></br>此外，請確保您允許存取來源 Vnet 的至少一個子網路。


## <a name="replicated-machine-operating-systems"></a>複寫的機器作業系統

Site Recovery 可對執行本節所列作業系統的 Azure VM 進行複寫。 請注意，如果已複製的電腦隨後升級到（或降級）到其他主內核，則需要在升級後禁用複製並重新啟用複製。

### <a name="windows"></a>Windows


**作業系統** | **詳細資料**
--- | ---
Windows Server 2019 | 支援伺服器核心，具有桌面體驗的伺服器。
Windows Server 2016  | 支援的伺服器核心，具有桌面體驗的伺服器。
Windows Server 2012 R2 | 支援。
Windows Server 2012 | 支援。
Windows 伺服器 2008 R2 與 SP1/SP2 | 支援。<br/><br/> 從 Azure VM 的移動服務擴展版[9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)開始，您需要在運行 Windows Server 2008 R2 SP1/SP2 的電腦上安裝 Windows[服務堆疊更新 （SSU）](https://support.microsoft.com/help/4490628)和[SHA-2 更新](https://support.microsoft.com/help/4474419)。  從 2019 年 9 月起不支援 SHA-1，如果未啟用 SHA-2 代碼簽名，代理擴展將不會按預期安裝/升級。 瞭解有關[SHA-2 升級和要求](https://aka.ms/SHA-2KB)的更多。
Windows 10 (x64) | 支援。
視窗 8.1 （x64） | 支援。
視窗 8 （x64） | 支援。
Windows 7 （x64）， SP1 從後 | 從 Azure VM 的移動服務擴展版[9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)中，需要在使用 SP1 運行 Windows 7 的電腦上安裝 Windows[服務堆疊更新 （SSU）](https://support.microsoft.com/help/4490628)和[SHA-2 更新](https://support.microsoft.com/help/4474419)。  從 2019 年 9 月起不支援 SHA-1，如果未啟用 SHA-2 代碼簽名，代理擴展將無法按預期安裝/升級。 瞭解有關[SHA-2 升級和要求](https://aka.ms/SHA-2KB)的更多。



#### <a name="linux"></a>Linux

**作業系統** | **詳細資料**
--- | ---
Red Hat Enterprise Linux | 6.7， 6.8， 6.9， 6.10， 7.0， 7.1， 7.2， 7.3， 7.4， 7.5， 7.6，[7.7，](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [8.0，](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS 伺服器 | [受支援的內核版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS 伺服器 | [支援的核心版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 使用基於密碼的身份驗證和登錄以及配置雲 VM 的雲 init 包的 Ubuntu 伺服器在容錯移轉時可能會禁用基於密碼的登錄（具體取決於 cloudinit 配置）。 通過在 Azure 門戶中"通過 VM 故障">設置功能表（通過 VM 故障）重置密碼>可重新啟用基於密碼的登錄。
Ubuntu 18.04 LTS 伺服器 | [支援的核心版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [受支援的內核版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [受支援的內核版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4。 [（支援的內核版本）](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 和 15 SP1。 [（支援的內核版本）](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> 不支援將複寫機器從 SP3 升級至 SP4。 如果已升級複寫的機器，您需要在升級後停用複寫，然後再重新啟用複寫。
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、 6.5、 6.6， 6.7， 6.8， 6.9， 6.10， 7.0， 7.1， 7.2， 7.3， 7.4， 7.5， 7.6， [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> 運行紅帽相容內核或牢不可破的企業內核版本 3、4 & 5 （UEK3、 UEK4、 UEK5）


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 Ubuntu 核心版本

**釋放** | **行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24 泛型到 3.13.0-170 泛型，<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21 泛型到 4.4.0-148 泛型，<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24 泛型到 3.13.0-170 泛型，<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21 泛型到 4.4.0-148 泛型，<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24 泛型到 3.13.0-170 泛型，<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21 泛型到 4.4.0-148 泛型，<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24 泛型到 3.13.0-170 泛型，<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21 泛型到 4.4.0-148 泛型，<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
|||
16.04 LTS | 9.32 | 4.4.0-21 泛型到 4.4.0-171 泛型，<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13 泛型到 4.15.0-74 泛型<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1066-azure|
16.04 LTS | 9.31 | 4.4.0-21 泛型到 4.4.0-170 泛型，<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13 泛型到 4.15.0-72 泛型<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1063-azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21 泛型到 4.4.0-166 泛型，<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13 泛型到 4.15.0-66 泛型<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1061-azure|
16.04 LTS | 9.29 | 4.4.0-21 泛型到 4.4.0-164 泛型，<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13 泛型到 4.15.0-64 泛型<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20 泛型到 4.15.0-74 泛型 </br> 4.18.0-13 泛型到 4.18.0-25 泛型 </br> 5.0.0-15 泛型到 5.0.0-37 泛型 </br> 5.3.0-19 泛型到 5.3.0-24 泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1028-azure </br> 5.3.0-1007-azure 到 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20 泛型到 4.15.0-72 泛型 </br> 4.18.0-13 泛型到 4.18.0-25 泛型 </br> 5.0.0-15 泛型到 5.0.0-37 泛型 </br> 5.3.0-19 泛型到 5.3.0-24 泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20 泛型到 4.15.0-66 泛型 </br> 4.18.0-13 泛型到 4.18.0-25 泛型 </br> 5.0.0-15 泛型到 5.0.0-32 泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20 泛型到 4.15.0-64 泛型 </br> 4.18.0-13 泛型到 4.18.0-25 泛型 </br> 5.0.0-15 泛型到 5.0.0-29 泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 Debian 核心版本

**釋放** | **行動服務版本** | **核心版本** |
--- | --- | --- |
Debian 7 | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64 至 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | 3.16.0-4-amd64 至 3.16.0-10-amd64，4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.28 | 3.16.0-4-amd64 至 3.16.0-10-amd64，4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 SUSE Linux Enterprise Server 12 核心版本

**釋放** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux 企業伺服器 12 （SP1，SP2，SP3，SP4） | 9.32 | 支援所有[庫存 SUSE 12 SP1、SP2、SP3、SP4 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 至 4.12.14-6.34-azure  |
SUSE Linux 企業伺服器 12 （SP1，SP2，SP3，SP4） | 9.31 | 支援所有[庫存 SUSE 12 SP1、SP2、SP3、SP4 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 至 4.12.14-6.29-azure  |
SUSE Linux 企業伺服器 12 （SP1，SP2，SP3，SP4） | 9.30 | 支援所有[庫存 SUSE 12 SP1、SP2、SP3、SP4 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 至 4.12.14-6.29-azure  |
SUSE Linux 企業伺服器 12 （SP1，SP2，SP3，SP4） | 9.29 | 支援所有[庫存 SUSE 12 SP1、SP2、SP3、SP4 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 至 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>支援的 SUSE Linux 企業伺服器 15 個適用于 Azure 虛擬機器的內核版本

**釋放** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux 企業伺服器 15 和 15 SP1 | 9.32 | 支援所有[股票 SUSE 15 和 15 內核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15)。</br></br> 4.12.14-5.5-azure 至 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>複寫機器 - Linux 檔案系統/客體儲存體

* 檔案系統：分機3，分機4，XFS，BTRFS
* 磁碟區管理員：LVM2
* 多重路徑軟體：裝置對應程式


## <a name="replicated-machines---compute-settings"></a>複寫的機器 - 計算設定

**設定** | **支援** | **詳細資料**
--- | --- | ---
大小 | 至少 2 顆 CPU 核心和 1 GB RAM 的任何 Azure VM 大小 | 確認 [Azure 虛擬機器大小](../virtual-machines/windows/sizes.md)。
可用性設定組 | 支援 | 如果使用預設選項為 Azure VM 啟用複製，則根據源地區設定自動創建可用性集。 您可以修改這些設定。
可用性區域 | 支援 |
Hybrid Use Benefit (HUB) | 支援 | 如果來源 VM 已啟用 HUB 授權，測試容錯移轉或容錯移轉 VM 也會使用 HUB 授權。
虛擬機器擴展集 | 不支援 |
Azure 資源庫映像 - Microsoft 發行 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
Azure 資源庫映像 - 第三方發行 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
自訂映像 - 第三方發行 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
使用 Site Recovery 移轉 VM | 支援 | 如果使用 Site Recovery 將 VMware VM 或實體機器遷移到 Azure，您需要將機器上執行的舊版行動服務解除安裝，然後重新啟動機器，再複寫到另一個 Azure 區域。
RBAC 政策 | 不支援 | VM 上基於角色的存取控制 （RBAC） 策略不會複製到目的地區域中的容錯移轉 VM。
延伸模組 | 不支援 | 擴展不會複製到目的地區域中的容錯移轉 VM。 容錯移轉後需要手動安裝它。

## <a name="replicated-machines---disk-actions"></a>複寫的機器 - 磁碟動作

**動作** | **詳細資料**
-- | ---
在複寫的 VM 上調整磁碟大小 | 容錯移轉前在源 VM 上受支援。 無需禁用/重新啟用複製。<br/><br/> 如果在容錯移轉後更改源 VM，則不會捕獲更改。<br/><br/> 如果在容錯移轉後更改 Azure VM 上的磁片大小，網站恢復不會捕獲更改，並且故障回退將更改為原始 VM 大小。
在複寫的 VM 上新增磁碟 | 支援

## <a name="replicated-machines---storage"></a>複寫的機器 - 儲存體

下表摘要說明 Azure VM OS 磁碟、資料磁碟和暫存磁碟的支援。

- 請務必注意 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 和 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM 的 VM 磁碟限制和目標，以避免發生任何效能問題。
- 如果您使用預設設定來部署，Site Recovery 會以來源設定作為基礎，自動建立磁碟與儲存體帳戶。
- 如果您使用自訂的設定，請務必遵循指導方針。

**元件** | **支援** | **詳細資料**
--- | --- | ---
OS 磁碟的大小上限 | 2048 GB | [深入了解](../virtual-machines/windows/managed-disks-overview.md) VM 磁碟。
暫存磁碟 | 不支援 | 暫存磁碟一律排除在複寫之外。<br/><br/> 請不要將任何永續性資料儲存於暫存磁碟上。 [深入了解](../virtual-machines/windows/managed-disks-overview.md)。
資料磁碟的大小上限 | 8192 GB 的託管磁片<br></br>4095 GB，用於非託管磁片|
資料磁片最小大小 | 對非託管磁片沒有限制。 2 GB 的託管磁片 |
資料磁碟的數目上限 | 最多 64 個 (根據特定的 Azure VM 大小支援) | [深入了解](../virtual-machines/windows/sizes.md) VM 大小。
資料磁碟的變更率 | 進階儲存體的每個磁碟最多 10 MBps。 標準儲存體的每個磁碟最多 2 MBps。 | 如果磁碟的平均資料變更率持續高於最大值，複寫將趕不上進度。<br/><br/>  不過，如果是偶而超過最大值，則複寫可以趕上進度，但您可能會看到稍有延遲的復原點。
資料磁碟 - 標準儲存體帳戶 | 支援 |
資料磁碟 - 進階儲存體帳戶 | 支援 | 如果 VM 的磁碟分散於進階和標準儲存體帳戶，您可以對於各個磁碟選取不同的目標儲存體帳戶，以確保目標區域有相同的儲存體設定。
受控磁碟 - 標準 | 在支援 Azure Site Recovery 的 Azure 區域中會支援。 |
受控磁碟 - 進階 | 在支援 Azure Site Recovery 的 Azure 區域中會支援。 |
標準 SSD | 支援 |
備援性 | 支援 LRS 和 GRS。<br/><br/> 不支援 ZRS。
非經常性和經常性儲存體 | 不支援 | 非經常性和經常性儲存體不支援 VM 磁碟
儲存空間 | 支援 |
待用加密 (SSE) | 支援 | SSE 是儲存體帳戶上的預設設定。
靜態加密 （CMK） | 支援 | 託管磁片都支援軟體和 HSM 金鑰
適用於 Windows OS 的 Azure 磁碟加密 (ADE) | 支援具有託管磁片的 VM。 | 不支援使用非託管磁片的 VM。 <br/><br/> 不支援受 HSM 保護的金鑰。 |
適用於 Linux OS 的 Azure 磁碟加密 (ADE) | 支援具有託管磁片的 VM。 | 不支援使用非託管磁片的 VM。 <br/><br/> 不支援受 HSM 保護的金鑰。 |
熱添加    | 支援 | 對於使用託管磁片的 VM，支援對添加到複製的 Azure VM 的資料磁片啟用複製。
熱刪除磁片    | 不支援 | 如果刪除 VM 上的資料磁片，則需要禁用複製並再次為 VM 啟用複製。
排除磁碟 | 支援。 您必須使用[PowerShell](azure-to-azure-exclude-disks.md)進行配置。 |    預設情況下，臨時磁片被排除。
儲存空間直接存取  | 支援損毀一致復原點。 不支援應用程式一致復原點。 |
向外延展檔案伺服器  | 支援損毀一致復原點。 不支援應用程式一致復原點。 |
DRBD | 不支援作為 DRBD 設置一部分的磁片。 |
LRS | 支援 |
GRS | 支援 |
RA-GRS | 支援 |
ZRS | 不支援 |
非經常性和經常性儲存體 | 不支援 | 非經常性和經常性儲存體不支援虛擬機器磁碟
適用於虛擬網路的 Azure 儲存體防火牆  | 支援 | 如果限制對存儲帳戶的虛擬網路訪問，請啟用[允許受信任的 Microsoft 服務](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)。
一般用途 V2 儲存體帳戶 (經常性存取層和非經常性存取層) | 支援 | 與一般用途 V1 儲存體帳戶相比，交易成本大幅增加
第 2 代（UEFI 啟動） | 支援

>[!IMPORTANT]
> 為了避免性能問題，請確保遵循[Linux](../virtual-machines/linux/disk-scalability-targets.md)或[Windows](../virtual-machines/windows/disk-scalability-targets.md) VM 的 VM 磁片可伸縮性和性能目標。 如果使用預設設置，網站恢復會根據源配置創建所需的磁片和存儲帳戶。 如果自訂並選擇自己的設置，請按照源 VM 的磁片可伸縮性和性能目標進行操作。

## <a name="limits-and-data-change-rates"></a>限制和資料更改率

下表總結了網站恢復限制。

- 這些限制基於我們的測試，但顯然不包括所有可能的應用程式 I/O 組合。
- 實際結果可能因應用 I/O 組合而異。
- 每個磁片資料改動和每個虛擬機器資料改動都有兩個限制需要考慮。

**存儲目標** | **平均源磁片 I/O** |**平均源磁片資料改動** | **每天源磁片資料總改動**
---|---|---|---
標準儲存體 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |    每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |20 MB/s | 每個磁片 1684 GB

## <a name="replicated-machines---networking"></a>複寫的機器 - 網路
**設定** | **支援** | **詳細資料**
--- | --- | ---
NIC | 針對特定 Azure VM 大小支援的數目上限 | 在容錯移轉期間建立 VM 時，系統會建立 NIC。<br/><br/> 容錯移轉 VM 的 NIC 數目取決於啟用複寫時來源 VM 具有的 NIC 數量。 如果您在啟用複寫後新增或移除 NIC，不會影響容錯移轉後複寫 VM 上的 NIC 數目。 另請注意，容錯移轉後 NIC 的順序不保證與原始順序相同。
網際網路負載平衡器 | 支援 | 使用復原方案中的 Azure 自動化指令碼，使預先設定的負載平衡器產生關聯。
內部負載平衡器 | 支援 | 使用復原方案中的 Azure 自動化指令碼，使預先設定的負載平衡器產生關聯。
公用 IP 位址 | 支援 | 將現有公用 IP 位址與 NIC 產生關聯。 或者，建立公用 IP 位址，然後使用復原方案中的 Azure 自動化指令碼讓它與 NIC 產生關聯。
NIC 上的 NSG | 支援 | 使用復原方案中的 Azure 自動化指令碼，使 NSG 與 NIC 產生關聯。
子網路上的 NSG | 支援 | 使用復原方案中的 Azure 自動化指令碼，使 NSG 與子網路產生關聯。
保留 (靜態) IP 位址 | 支援 | 如果來源 VM 上的 NIC 有靜態 IP 位址，而目標子網路有相同的 IP 位址可用，則會將它指派給容錯移轉 VM。<br/><br/> 如果目標子網路沒有相同的 IP 位址，子網路中一個可用的 IP 位址會被保留供 VM 使用。<br/><br/> 您還可以在**複製專案** > **"計算** > **和網路** > **網路"介面**中指定固定 IP 位址和子網。
動態 IP 位址 | 支援 | 如果來源上的 NIC 有動態 IP 位址，則容錯移轉 VM 上的 NIC 預設也是動態。<br/><br/> 如有需要，您可以將此修改為固定的 IP 位址。
多個 IP 位址 | 不支援 | 如果對具有多個 IP 位址的 NIC 的 VM 進行故障處理，則僅保留源區域中 NIC 的主 IP 位址。 要分配多個 IP 位址，可以將 VM 添加到[恢復計畫](recovery-plan-overview.md)並附加腳本，以便向計畫分配其他 IP 位址，也可以在容錯移轉後手動或使用腳本進行更改。
流量管理員     | 支援 | 您可以預先設定流量管理員，定期將流量傳輸到來源區域中的端點，如果發生容錯移轉，則傳輸到目標區域中的端點。
Azure DNS | 支援 |
自訂 DNS    | 支援 |
未經驗證的 Proxy | 支援 | [深入了解](site-recovery-azure-to-azure-networking-guidance.md)
經驗證的 Proxy | 不支援 | 如果 VM 對於輸出連線能力使用經驗證的 Proxy，則無法使用 Azure Site Recovery 加以複寫。
VPN 網站到本地的連接<br/><br/>（帶或不帶快速路由）| 支援 | 確保 UDR 和 NSG 的配置方式不會將網站恢復流量路由到本地。 [深入了解](site-recovery-azure-to-azure-networking-guidance.md)
VNET 對 VNET 連線    | 支援 | [深入了解](site-recovery-azure-to-azure-networking-guidance.md)
虛擬網路服務端點 | 支援 | 如果您要限制只有儲存體帳戶可以存取虛擬網路，請確定受信任的 Microsoft 服務可以存取儲存體帳戶。
加速網路 | 支援 | 必須在來源 VM 上啟用加速網路。 [深入了解](azure-vm-disaster-recovery-with-accelerated-networking.md)。



## <a name="next-steps"></a>後續步驟
- 閱讀複製 Azure VM[的網路指南](site-recovery-azure-to-azure-networking-guidance.md)。
- 透過[複寫 Azure VM](site-recovery-azure-to-azure.md) 來部署災害復原。
