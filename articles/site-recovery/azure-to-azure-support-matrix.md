---
title: 使用 Azure Site Recovery 進行 Azure VM 災害復原的支援矩陣
description: 摘要說明如何使用 Azure Site Recovery 將 Azure VM 災害復原進行至次要區域的支援。
ms.topic: article
ms.date: 07/14/2020
ms.author: raynew
ms.openlocfilehash: 7687a7a625fb11dcf3438bab9df62c4021d20e81
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331884"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure 區域之間的 Azure VM 災害復原支援矩陣

本文摘要說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 服務，將 Azure VM 災害復原從某個 Azure 進行至另一個區域的支援和必要條件。


## <a name="deployment-method-support"></a>部署方法的支援

**部署** |  **支援**
--- | ---
**Azure 入口網站** | 支援。
**PowerShell** | 支援。 [深入了解](azure-to-azure-powershell.md)
**REST API** | 支援。
**CLI** | 目前不支援


## <a name="resource-support"></a>資源支援

**資源動作** | **詳細資料**
--- | ---
**在資源群組間移動保存庫** | 不支援
**跨資源群組移動計算/儲存體/網路資源** | 不支援。<br/><br/> 如果您在 VM 複寫之後移動 VM 或是相關聯的元件 (例如儲存體/網路)，您必須停用該 VM 的複寫，然後再重新啟用複寫。
**將 Azure VM 從某個訂用帳戶複寫至另一個以進行災害復原** | 在相同的 Azure Active Directory 租用戶中支援。
**在支援的地理叢集 (在訂用帳戶內或跨訂用帳戶) 內移轉區域之間的 VM** | 在相同的 Azure Active Directory 租用戶中支援。
**在相同區域內移轉 VM** | 不支援。

## <a name="region-support"></a>區域支援

您可以複製和復原相同的地理叢集內任何兩個區域之間的 VM。 地理叢集會是以資料延遲及主權範圍定義而成。


**地理叢集** | **Azure 區域**
-- | --
America | 加拿大東部、加拿大中部、美國中南部、美國中西部、美國東部、美國東部 2、美國西部、美國西部 2、美國中部、美國中北部
歐洲 | 英國西部、英國南部、北歐、西歐、南非西部、南非北部、挪威東部、挪威西部、法國中部
Asia | 印度南部、印度中部、印度西部、東南亞、東亞、日本東部、日本西部、南韓中部、南韓南部
澳大利亞    | 澳大利亞東部、澳大利亞東南部、澳大利亞中部、澳大利亞中部 2
Azure Government    | US Gov 維吉尼亞州、US Gov 愛荷華州、US Gov 亞利桑那州、US Gov 德克薩斯州、US DoD 東部、US DoD 中部
德國    | 德國中部、德國東北部
中國 | 中國東部、中國北部、中國北部 2、中國東部 2
保留給國內災害復原的受限制區域 |德國北部保留給德國中西部、瑞士西部保留給瑞士北部、法國南部保留給法國中部、阿拉伯聯合大公國中部限制給阿拉伯聯合大公國北部客戶

>[!NOTE]
>
> - 對於**巴西南部**，您可以複寫並容錯移轉至這些區域：美國中南部、美國中西部、美國東部、美國東部 2、美國西部、美國西部 2 和美國中北部。
> - 巴西南部只能用來做為 VM 可以從中使用 Site Recovery 進行複寫的來源區域。 其無法當作目標區域。 這是由於地理距離所造成的延遲問題。 請注意，如果您將巴西南部當作來源區域容錯移轉至目標，則支援從目標地區域容錯回復至巴西南部。
> - 您可以在具有適當存取權的區域內運作。
> - 如果您要建立保存庫的區域未顯示，請確定您的訂用帳戶具有可在該區域中建立資源的存取權。
> - 當您啟用複寫時，如果您在地理叢集內看不到區域，請確定您的訂用帳戶擁有可在該區域中建立 VM 的權限。



## <a name="cache-storage"></a>快取儲存體

下表摘要說明複寫期間 Site Recovery 使用的快取儲存體帳戶支援。

**設定** | **支援** | **詳細資料**
--- | --- | ---
一般用途 V2 儲存體帳戶 (經常性存取層和非經常性存取層) | 支援 | 不建議使用 GPv2，因為 V2 的交易成本明顯高於 V1 儲存體帳戶。
進階儲存體 | 不支援 | 標準儲存體帳戶會用於快取儲存體，以協助最佳化成本。
適用於虛擬網路的 Azure 儲存體防火牆  | 支援 | 如果您使用啟用防火牆的快取儲存體帳戶或目標儲存體帳戶，請確定您[「允許信任的 Microsoft 服務」](../storage/common/storage-network-security.md#exceptions)。<br></br>此外，請確保您允許存取來源 Vnet 的至少一個子網路。


## <a name="replicated-machine-operating-systems"></a>複寫的機器作業系統

Site Recovery 可對執行本節所列作業系統的 Azure VM 進行複寫。 請注意，如果已複寫的機器後續升級 (或降級) 至不同的主要核心，您必須停用複寫，並在升級之後重新啟用複寫。

### <a name="windows"></a>Windows


**作業系統** | **詳細資料**
--- | ---
Windows Server 2019 | 支援伺服器核心、含有桌面體驗的伺服器。
Windows Server 2016  | 支援的伺服器核心、含有桌面體驗的伺服器。
Windows Server 2012 R2 | 支援。
Windows Server 2012 | 支援。
Windows Server 2008 R2 (含 SP1/SP2) | 支援。<br/><br/> 從適用於 Azure Vm 的行動服務擴充功能版本 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 中，您必須在執行 Windows Server 2008 R2 SP1/SP2 的電腦上安裝 Windows [服務堆疊更新 (SSU)](https://support.microsoft.com/help/4490628) 和 [SHA-2 更新](https://support.microsoft.com/help/4474419)。  2019 年 9 月起不支援 SHA-1，而且如果未啟用 SHA-2 程式碼簽署，代理程式擴充功能將不會如預期般安裝/升級。 深入了解 [SHA-2 升級和需求](https://aka.ms/SHA-2KB)。
Windows 10 (x64) | 支援。
Windows 8.1 (x64) | 支援。
Windows 8 (x64) | 支援。
Windows 7 (x64)，含 SP1 和更新版本 | 從適用於 Azure Vm 的行動服務擴充功能版本 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 中，您必須在執行 Windows 7 (含 SP1) 的電腦上安裝 Windows [服務堆疊更新 (SSU)](https://support.microsoft.com/help/4490628) 和 [SHA-2 更新](https://support.microsoft.com/help/4474419)。  2019 年 9 月起不支援 SHA-1，而且如果未啟用 SHA-2 程式碼簽署，代理程式擴充功能將不會如預期般安裝/升級。 深入了解 [SHA-2 升級和需求](https://aka.ms/SHA-2KB)。



#### <a name="linux"></a>Linux

**作業系統** | **詳細資料**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、 [7.8](https://support.microsoft.com/help/4564347/)、 [7.9](https://support.microsoft.com/help/4578241/)、 [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1、 [8.2](https://support.microsoft.com/help/4570609/)
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10 </br> 7.0、7.1、7.2、7.3、7.4、7.5、7.6、7.7、 [7.8](https://support.microsoft.com/help/4564347/)、 [7.9 預先 GA 版本](https://support.microsoft.com/help/4578241/)、7.9 熱修正程式支援 9.37 ga 版本 * * </br> 8.0、8.1、 [8.2](https://support.microsoft.com/en-us/help/4570609)
Ubuntu 14.04 LTS 伺服器 | 包含所有14.04 的支援。*x* 版本; [支援的核心版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines); 
Ubuntu 16.04 LTS 伺服器 | 包含所有16.04 的支援。*x* 版本; [支援的核心版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 使用密碼型驗證和登入並使用雲端 init 封裝來設定雲端 VM 的 Ubuntu 伺服器，可能對容錯移轉停用密碼型登入 (取決於 cloudinit 組態)。 針對已容錯移轉的 VM，從 Azure 入口網站中的 [支援] > [疑難排解] > [設定功能表] 中重設密碼，即可在虛擬機器上重新啟用密碼型登入。
Ubuntu 18.04 LTS 伺服器 | 包含所有18.04 的支援。*x* 版本; [支援的核心版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines) |
Ubuntu 20.04 LTS 伺服器 | 包含所有20.04 的支援。*x* 版本; [支援的核心版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | 包含所有7的支援。 *x* 版本 [支援的核心版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | 包含所有8的支援。 *x* 版本 [支援的核心版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | 包含9.1 到9.13 的支援。 不支援 Debian 9.0。 [支援的核心版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4、SP5  [ (支援的核心版本) ](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15、SP1、SP2[ (支援的核心版本) ](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> 不支援將複寫機器從 SP3 升級至 SP4。 如果已升級複寫的機器，您需要在升級後停用複寫，然後再重新啟用複寫。
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、 [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)、 [7.8](https://support.microsoft.com/help/4573888/)、 [8.0](https://support.microsoft.com/help/4573888/)、 [8.1](https://support.microsoft.com/help/4573888/)  <br/> 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3、4 及 5 版 (UEK3、UEK4、UEK5)<br/><br/>8.1<br/>在所有 UEK 核心和 RedHat 核心上執行 <= 3.10.0-1062. * [9.35](https://support.microsoft.com/help/4573888/)支援可支援 RedHat 核心的其餘部分，可在[9.36](https://support.microsoft.com/help/4578241/)中取得

* * 注意：若要在發行後的15天內支援最新的 Linux 核心，Azure Site Recovery 在最新的行動代理程式版本之上推出熱修正修補程式。 這項修正會在兩個主要版本版本之間推出。 若要更新為最新版本的行動代理程式 (包括熱修正修補程式) 請遵循 [本文中所述的步驟](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)。 此修補程式目前已針對 Azure 中使用的行動代理程式推出，適用于 azure DR 案例。

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 Ubuntu 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | [9.34](https://support.microsoft.com/help/4570609)、 [9.35](https://support.microsoft.com/help/4573888/)、 [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/)、 [9.38](https://support.microsoft.com/help/4590304/)| 3.13.0-24-generic 至 3.13.0-170-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-148-generic、<br/>4.15.0-1023-azure 至 4.15.0-1045-azure |
|||
16.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generic 至 4.4.0-190-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-118-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1096-azure|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic 至 4.4.0-189-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-115-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1093-azure </br> 4.4.0-190-generic、4.15.0-117-generic、4.15.0-118-generic、4.15.0-1095-azure、4.15.0-1096-azure 到9.37 熱修正修補程式 * *|
16.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-generic 至 4.4.0-187-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-112-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1092-azure |
16.04 LTS | [9.34](https://support.microsoft.com/help/4570609)、 [9.35](https://support.microsoft.com/help/4573888/) | 4.4.0-21-generic 至 4.4.0-184-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-106-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1089-azure</br> 4.15.0-107-generic、4.4.0-185-generic & 4.15.0-1091-azure 至9.35 熱修正修補程式 * * |
|||
18.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generic 至 4.15.0-118-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-泛型至 5.0.0-61-generic </br> 5.3.0-19-generic 至 5.3.0-67-generic </br> 5.4.0-37-generic 至 5.4.0-48-generic</br> 4.15.0-1009-azure 到 4.15.0-1096-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1035-azure </br> 5.4.0-1020-azure 到 5.4.0-1026-azure|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic 至 4.15.0-115-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-泛型至 5.0.0-60-泛型 </br> 5.3.0-19-generic 至 5.3.0-66-generic </br> 5.4.0-37-generic 至 5.4.0-45-generic</br> 4.15.0-1009-azure 到 4.15.0-1093-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1035-azure </br> 5.4.0-1020-azure 到 5.4.0-1023-azure</br> 4.15.0-117-generic、4.15.0-118-generic、5.0.0-61-generic、5.3.0-67-generic、5.4.0-47-generic、5.4.0-48-generic、4.15.0-1095-azure、4.15.0-1096-azure、5.4.0-1025-azure、5.4.0-1026-azure 到9.37 熱修正修補程式 * *|
18.04 LTS | [9.36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-generic 至 4.15.0-112-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-泛型至 5.0.0-58-generic </br> 5.3.0-19-generic 至 5.3.0-65-generic </br> 5.4.0-37-generic 至 5.4.0-42-generic</br> 4.15.0-1009-azure 到 4.15.0-1092-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1032-azure </br> 5.4.0-1020-azure 到 5.4.0-1022-azure </br> 5.0.0-60-generic & 5.3.0-1035-azure 到9.36 熱修正修補程式 * *|
18.04 LTS | [9.34](https://support.microsoft.com/help/4570609)、 [9.35](https://support.microsoft.com/help/4573888/) | 4.15.0-20-generic 至 4.15.0-108-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-泛型至 5.0.0-52-泛型 </br> 5.3.0-19-generic 至 5.3.0-61-generic </br> 4.15.0-1009-azure 到 4.15.0-1089-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1031-azure </br> 4.15.0-109-generic、5.0.0-53-generic、5.3.0-62-generic、4.15.0-1091-azure & 5.3.0-1032-azure 到9.35 熱修正修補程式 * *|
|||
20.04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic 至 5.4.0-48 </br> -一般 5.4.0-1010-azure 到 5.4.0-1026-azure
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic 至 5.4.0-45 </br> -一般 5.4.0-1010-azure 到 5.4.0-1023-azure </br> 5.4.0-47-generic、5.4.0-48-generic、5.4.0-1025-azure、5.4.0-1026-azure 到9.37 熱修正修補程式 * *
20.04 LTS |[9.36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-generic 至 5.4.0-42 </br> -一般 5.4.0-1010-azure 到 5.4.0-1022-azure

* * 注意：若要在發行後的15天內支援最新的 Linux 核心，Azure Site Recovery 在最新的行動代理程式版本之上推出熱修正修補程式。 這項修正會在兩個主要版本版本之間推出。 若要更新為最新版本的行動代理程式 (包括熱修正修補程式) 請遵循 [本文中所述的步驟](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)。 此修補程式目前已針對 Azure 中使用的行動代理程式推出，適用于 azure DR 案例。

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 Debian 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
Debian 7 |  [9.34](https://support.microsoft.com/help/4570609)、 [9.35](https://support.microsoft.com/help/4573888/)、 [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/)、 [9.38](https://support.microsoft.com/help/4590304/) | 3.2.0-4-amd64 至 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.35](https://support.microsoft.com/help/4573888/, )、 [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/)、 [9.38](https://support.microsoft.com/help/4590304/) | 3.16.0-4-amd64 至 3.16.0-11-amd64、4.9.0 4.9.0-. bpo. 4-amd64 至 4.9.0 4.9.0-. bpo. 11-amd64 |
Debian 8 | [9.34](https://support.microsoft.com/help/4570609) | 3.16.0-4-amd64 至 3.16.0-10-amd64、4.9.0-0.bpo.4-amd64 至 4.9.0-0.bpo.11-amd64 |
|||
Debian 9。1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 至 4.9.0-13-amd64 </br> 4.19.0 4.9.0-. bpo. 1-amd64 to 4.19.0 4.9.0-. bpo. 11-amd64 </br> 4.19.0 4.9.0-. bpo. 1-4.19.0 4.9.0-. bpo. 11-a </br> 
Debian 9。1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 至 4.9.0-13-amd64、4.19.0 4.9.0-. bpo. 6-amd64 至 4.19.0 4.9.0-. bpo. 10-amd64、4.19.0 4.9.0-. bpo. 6-a-a-i 至 4.19.0 4.9.0-. bpo. 10-雲端

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 SUSE Linux Enterprise Server 12 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5)  | [9.38](https://support.microsoft.com/help/4590304/) | 支援所有 [股票 SUSE 12 SP1、SP2、SP3、SP4](https://www.suse.com/support/kb/doc/?id=000019587) 核心。</br></br> 4.4.138-4.7-azure 至 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5)  | [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/) | 支援所有 [股票 SUSE 12 SP1、SP2、SP3、SP4](https://www.suse.com/support/kb/doc/?id=000019587) 核心。</br></br> 4.4.138-4.7-azure 至 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.22-azure </br> 4.12.14-16.25-azure、4.12.14-16.28-azure 至9.37 熱修正修補程式 * *|
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5)  | [9.34](https://support.microsoft.com/help/4570609)、 [9.35](https://support.microsoft.com/help/4573888/) | 支援所有 [股票 SUSE 12 SP1、SP2、SP3、SP4](https://www.suse.com/support/kb/doc/?id=000019587) 核心。</br></br> 4.4.138-4.7-azure 至 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.19-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 SUSE Linux Enterprise Server 15 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | 根據預設，支援所有 [stock SUSE 15 和 15 核心](https://www.suse.com/support/kb/doc/?id=000019587)。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure 到 5.3.18-18.18-azure
SUSE Linux Enterprise Server 15 和 15 SP1 | [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/)  | 根據預設，支援所有 [stock SUSE 15 和 15 核心](https://www.suse.com/support/kb/doc/?id=000019587)。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.38-azure </br> 4.12.14-8.41-azure、4.12.14-8.44-azure 至9.37 熱修正修補程式 * *
SUSE Linux Enterprise Server 15 和 15 SP1 | [9.34](https://support.microsoft.com/help/4570609)、 [9.35](https://support.microsoft.com/help/4573888/)  | 根據預設，支援所有 [stock SUSE 15 和 15 核心](https://www.suse.com/support/kb/doc/?id=000019587)。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.33-azure 


## <a name="replicated-machines---linux-file-systemguest-storage"></a>複寫機器 - Linux 檔案系統/客體儲存體

* 檔案系統：ext3、ext4、XFS、BTRFS
* 磁碟區管理員：LVM2

> [!NOTE]
> 不支援多重路徑軟體。 


## <a name="replicated-machines---compute-settings"></a>複寫的機器 - 計算設定

**設定** | **支援** | **詳細資料**
--- | --- | ---
大小 | 至少 2 顆 CPU 核心和 1 GB RAM 的任何 Azure VM 大小 | 確認 [Azure 虛擬機器大小](../virtual-machines/sizes.md)。
可用性設定組 | 支援 | 如果您以預設選項啟用 Azure VM 的複寫，系統會根據來源區域設定自動建立可用性設定組。 您可以修改這些設定。
可用性區域 | 支援 |
Hybrid Use Benefit (HUB) | 支援 | 如果來源 VM 已啟用 HUB 授權，測試容錯移轉或容錯移轉 VM 也會使用 HUB 授權。
虛擬機器擴展集 | 不支援 |
Azure 資源庫映像 - Microsoft 發行 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
Azure 資源庫映像 - 第三方發行 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
自訂映像 - 第三方發行 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
使用 Site Recovery 移轉 VM | 支援 | 如果使用 Site Recovery 將 VMware VM 或實體機器遷移到 Azure，您需要將機器上執行的舊版行動服務解除安裝，然後重新啟動機器，再複寫到另一個 Azure 區域。
RBAC 原則 | 不支援 | 以角色為基礎的存取控制 (Vm 上的 RBAC) 原則，不會複寫到目的地區域中的容錯移轉 VM。
延伸模組 | 不支援 | 延伸模組不會複寫至目標區域中的容錯移轉 VM。 在容錯移轉之後必須手動安裝延伸模組。
鄰近放置群組 | 支援 | 位於鄰近位置群組內的虛擬機器可以使用 Site Recovery 來保護。


## <a name="replicated-machines---disk-actions"></a>複寫的機器 - 磁碟動作

**動作** | **詳細資料**
-- | ---
在複寫的 VM 上調整磁碟大小 | 在容錯移轉之前，於來源 VM 上支援。 不需要停用/重新啟用複寫。<br/><br/> 如果您在容錯移轉之後變更來源 VM，則不會擷取變更。<br/><br/> 如果您在容錯移轉之後變更 Azure VM 上的磁碟大小，Site Recovery 不會擷取變更，而容錯回復將會是原始 VM 大小。
在複寫的 VM 上新增磁碟 | 支援
對受保護磁片的離線變更 | 將磁片中斷連線並對它們進行離線修改需要觸發完整的重新同步處理。

## <a name="replicated-machines---storage"></a>複寫的機器 - 儲存體

下表摘要說明 Azure VM OS 磁碟、資料磁碟和暫存磁碟的支援。

- 請務必注意 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 和 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM 的 VM 磁碟限制和目標，以避免發生任何效能問題。
- 如果您使用預設設定來部署，Site Recovery 會以來源設定作為基礎，自動建立磁碟與儲存體帳戶。
- 如果您使用自訂的設定，請務必遵循指導方針。

**元件** | **支援** | **詳細資料**
--- | --- | ---
OS 磁碟的大小上限 | 2048 GB | [深入了解](../virtual-machines/managed-disks-overview.md) VM 磁碟。
暫存磁碟 | 不支援 | 暫存磁碟一律排除在複寫之外。<br/><br/> 請不要將任何永續性資料儲存於暫存磁碟上。 [深入了解](../virtual-machines/managed-disks-overview.md)。
資料磁碟的大小上限 | 8192 GB (若為受控磁碟)<br></br>4095 GB (若為非受控磁碟)|
資料磁碟的大小下限 | 非受控磁碟沒有限制。 2 GB (若為受控磁碟) |
資料磁碟的數目上限 | 最多 64 個 (根據特定的 Azure VM 大小支援) | [深入了解](../virtual-machines/sizes.md) VM 大小。
資料磁碟的變更率 | Premium 儲存體的每個磁片最多 20 MBps。 標準儲存體的每個磁碟最多 2 MBps。 | 如果磁碟的平均資料變更率持續高於最大值，複寫將趕不上進度。<br/><br/>  不過，如果是偶而超過最大值，則複寫可以趕上進度，但您可能會看到稍有延遲的復原點。
資料磁碟 - 標準儲存體帳戶 | 支援 |
資料磁碟 - 進階儲存體帳戶 | 支援 | 如果 VM 的磁碟分散於進階和標準儲存體帳戶，您可以對於各個磁碟選取不同的目標儲存體帳戶，以確保目標區域有相同的儲存體設定。
受控磁碟 - 標準 | 在支援 Azure Site Recovery 的 Azure 區域中會支援。 |
受控磁碟 - 進階 | 在支援 Azure Site Recovery 的 Azure 區域中會支援。 |
標準 SSD | 支援 |
備援性 | 支援 LRS 和 GRS。<br/><br/> 不支援 ZRS。
非經常性和經常性儲存體 | 不支援 | 非經常性和經常性儲存體不支援 VM 磁碟
儲存空間 | 支援 |
待用加密 (SSE) | 支援 | SSE 是儲存體帳戶上的預設設定。
待用加密 (CMK) | 支援 | 受控磁碟同時支援軟體和 HSM 金鑰
靜態加密 | 支援 | 深入瞭解適用于[Windows](../virtual-machines/windows/disk-encryption.md)和[Linux](../virtual-machines/linux/disk-encryption.md)的支援區域
適用於 Windows OS 的 Azure 磁碟加密 (ADE) | 僅具有受控磁碟的 VM 提供支援。 | 不支援使用非受控磁碟的 VM。 <br/><br/> 不支援 HSM 保護的金鑰。 <br/><br/> 不支援在單一磁片上加密個別卷。 |
適用於 Linux OS 的 Azure 磁碟加密 (ADE) | 僅具有受控磁碟的 VM 提供支援。 | 不支援使用非受控磁碟的 VM。 <br/><br/> 不支援 HSM 保護的金鑰。 <br/><br/> 不支援在單一磁碟上加密個別磁碟區。 <br><br> 啟用複寫的已知問題。 [深入了解。](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS 金鑰輪替 | 不支援 | 如果儲存體帳戶的 SAS 金鑰已輪替，客戶必須停用再重新啟用複寫。 |
熱新增    | 支援 | 使用受控磁碟的 VM 支援為您新增至所複寫 Azure VM 的資料磁碟啟用複寫。 <br/><br/> 一次只有一個磁碟可以熱新增至 Azure VM。 不支援平行新增多個磁碟。 |
熱移除磁碟    | 不支援 | 如果您在 VM 上移除資料磁碟，需要停用複寫，然後再次為 VM 啟用複寫。
排除磁碟 | 支援。 您必須使用 [PowerShell](azure-to-azure-exclude-disks.md) 才能設定。 |    預設排除暫存磁碟。
儲存空間直接存取  | 支援損毀一致復原點。 不支援應用程式一致復原點。 |
向外延展檔案伺服器  | 支援損毀一致復原點。 不支援應用程式一致復原點。 |
DRBD | 不支援屬於 DRBD 設定的磁碟。 |
LRS | 支援 |
GRS | 支援 |
RA-GRS | 支援 |
ZRS | 不支援 |
非經常性和經常性儲存體 | 不支援 | 非經常性和經常性儲存體不支援虛擬機器磁碟
適用於虛擬網路的 Azure 儲存體防火牆  | 支援 | 如果限制只有儲存體帳戶可以存取虛擬網路，請啟用[允許受信任的 Microsoft 服務](../storage/common/storage-network-security.md#exceptions)。
一般用途 V2 儲存體帳戶 (經常性存取層和非經常性存取層) | 支援 | 與一般用途 V1 儲存體帳戶相比，交易成本大幅增加
第2代 (UEFI 開機) | 支援
NVMe 磁碟 | 不支援
Azure 共用磁碟 | 不支援
安全傳輸選項 | 支援
已啟用寫入加速器的磁片 | 不支援

>[!IMPORTANT]
> 若要避免發生效能問題，請務必遵循 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 或 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM 的 VM 磁碟可擴縮性和效能目標。 如果您使用預設設定，Site Recovery 會根據來源組態建立必要的磁碟和儲存體帳戶。 如果您自訂並選取您自己的設定，請遵循來源 VM 的磁碟可擴縮性和效能目標。

## <a name="limits-and-data-change-rates"></a>限制與資料變更率

下表摘要說明 Site Recovery 限制。

- 上述限制是以我們的測試為基礎，但很明顯並未涵蓋所有可能的應用程式 I/O 組合。
- 實際的結果會隨著應用程式 I/O 混合而有所不同。
- 有兩個需要考量的限制：每個磁碟資料變換率和每個虛擬機器資料變換率。
- 每個虛擬機器資料變換目前的限制為 54 MB/秒，而不論大小為何。

**儲存目標** | **平均來源磁碟 I/O** |**平均來源磁碟資料變換** | **每日的來源磁碟資料變換總計**
---|---|---|---
標準儲存體 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |    每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |20 MB/秒 | 每個磁碟 1684 GB

## <a name="replicated-machines---networking"></a>複寫的機器 - 網路
**設定** | **支援** | **詳細資料**
--- | --- | ---
NIC | 針對特定 Azure VM 大小支援的數目上限 | 在容錯移轉期間建立 VM 時，系統會建立 NIC。<br/><br/> 容錯移轉 VM 的 NIC 數目取決於啟用複寫時來源 VM 具有的 NIC 數量。 如果您在啟用複寫後新增或移除 NIC，不會影響容錯移轉後複寫 VM 上的 NIC 數目。 <br/><br/> 在容錯移轉之後，Nic 的順序不保證會與原始順序相同。 <br/><br/> 您可以根據組織的命名慣例，重新命名目的地區域中的 Nic。 使用 PowerShell 可支援重新命名 NIC。
網際網路負載平衡器 | 不支援 | 您可以在主要區域中設定公用/網際網路負載平衡器。 不過，DR 區域中的 Azure Site Recovery 不支援公用/網際網路負載平衡器。
內部負載平衡器 | 支援 | 使用復原方案中的 Azure 自動化指令碼，使預先設定的負載平衡器產生關聯。
公用 IP 位址 | 支援 | 將現有公用 IP 位址與 NIC 產生關聯。 或者，建立公用 IP 位址，然後使用復原方案中的 Azure 自動化指令碼讓它與 NIC 產生關聯。
NIC 上的 NSG | 支援 | 使用復原方案中的 Azure 自動化指令碼，使 NSG 與 NIC 產生關聯。
子網路上的 NSG | 支援 | 使用復原方案中的 Azure 自動化指令碼，使 NSG 與子網路產生關聯。
保留 (靜態) IP 位址 | 支援 | 如果來源 VM 上的 NIC 有靜態 IP 位址，而目標子網路有相同的 IP 位址可用，則會將它指派給容錯移轉 VM。<br/><br/> 如果目標子網路沒有相同的 IP 位址，子網路中一個可用的 IP 位址會被保留供 VM 使用。<br/><br/> 您也可以在 [複寫的項目] > [設定] > [計算與網路] > [網路介面] 中指定固定的 IP 位址和子網路。
動態 IP 位址 | 支援 | 如果來源上的 NIC 有動態 IP 位址，則容錯移轉 VM 上的 NIC 預設也是動態。<br/><br/> 如有需要，您可以將此修改為固定的 IP 位址。
多個 IP 位址 | 不支援 | 當您容錯移轉的 VM 具有搭配多個 IP 位址的 NIC 時，只會保留來源區域中 NIC 的主要 IP 位址。 若要指派多個 IP 位址，您可以將 VM 新增至[復原方案](recovery-plan-overview.md)，並附加指令碼將額外的 IP 位址指派給方案，或者您可以在容錯移轉之後手動進行變更，或使用指令碼進行變更。
流量管理員     | 支援 | 您可以預先設定流量管理員，定期將流量傳輸到來源區域中的端點，如果發生容錯移轉，則傳輸到目標區域中的端點。
Azure DNS | 支援 |
自訂 DNS    | 支援 |
未經驗證的 Proxy | 支援 | [深入了解](./azure-to-azure-about-networking.md)
經驗證的 Proxy | 不支援 | 如果 VM 對於輸出連線能力使用經驗證的 Proxy，則無法使用 Azure Site Recovery 加以複寫。
內部部署的 VPN 站台對站台連線<br/><br/>(含或不含 ExpressRoute)| 支援 | 請確定設定 UDR 和 NSG 時，站台復原流量不是傳送到內部部署的裝置。 [深入了解](./azure-to-azure-about-networking.md)
VNET 對 VNET 連線    | 支援 | [深入了解](./azure-to-azure-about-networking.md)
虛擬網路服務端點 | 支援 | 如果您要限制只有儲存體帳戶可以存取虛擬網路，請確定受信任的 Microsoft 服務可以存取儲存體帳戶。
加速網路 | 支援 | 必須在來源 VM 上啟用加速網路。 [深入了解](azure-vm-disaster-recovery-with-accelerated-networking.md)。
Palo Alto 網路設備 | 不支援 | 使用協力廠商應用設備時，虛擬機器內的提供者通常會強加一些限制。 Azure Site Recovery 需要代理程式、擴充功能和輸出連線能力可供使用。 但設備不會讓任何輸出活動在虛擬機器內進行設定。
IPv6  | 不支援 | 亦不支援同時包含 IPv4 和 IPv6 的混合組態。 請釋放 IPv6 範圍的子網路，再進行任何 Site Recovery 作業。
Site Recovery 服務的 Private link 存取權 | 支援 | [深入了解](azure-to-azure-how-to-enable-replication-private-endpoints.md)



## <a name="next-steps"></a>後續步驟

- 閱讀複寫 Azure VM 的[網路指引](./azure-to-azure-about-networking.md)。
- 透過[複寫 Azure VM](./azure-to-azure-quickstart.md) 來部署災害復原。
