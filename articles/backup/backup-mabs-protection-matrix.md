---
title: MABS (Azure 備份伺服器) V3 UR1 保護對照表
description: 本文提供支援矩陣，其中列出 Azure 備份伺服器保護的所有工作負載、資料類型和安裝。
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: fa8e2a33718c17df7b918b2aaa545ca80c473c29
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190856"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS (Azure 備份伺服器) V3 UR1 保護對照表

本文列出您可以使用 Azure 備份伺服器保護的各種伺服器和工作負載。 下列矩陣列出可使用 Azure 備份伺服器保護的項目。

使用下列適用于 MABS v3 UR1 的矩陣：

* 工作負載 – 技術的工作負載類型。

* 版本–工作負載支援的 MABS 版本。

* MABS 安裝–您想要安裝 MABS 的電腦/位置。

* 保護和還原 – 列出關於工作負載的詳細資訊，例如，支援的儲存體容器或支援的部署

>[!NOTE]
>32位保護代理程式的支援已被 MABS v3 UR1 取代。 請參閱 [32 位保護代理程式](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation)取代。

## <a name="protection-support-matrix"></a>保護支援矩陣

下列各節將詳細說明 MABS 的保護支援矩陣：

* [應用程式備份](#applications-backup)
* [VM 備份](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>應用程式備份

| **[工作負載]**               | **版本**                                                  | **Azure 備份伺服器安裝**                       | **支援的 Azure 備份伺服器** | **保護和復原**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| 用戶端電腦 (64 位)  | Windows 10                                                  | 實體伺服器  <br><br>    Hyper-V 虛擬機器   <br><br>   VMware 虛擬機器 | V3 UR1                            | 磁片區、共用、資料夾、檔案、重復資料刪除磁片區   <br><br>   受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。  <br><br>    磁碟區至少必須是 1 GB。 Azure 備份伺服器使用磁碟區陰影複製服務 (VSS) 來製作資料快照集，而快照集只適用于磁片區至少為 1 GB 的情況。 |
| 伺服器 (64 位)           | Windows Server 2019、2016、2012 R2、2012                    | 當工作負載以 Azure 虛擬機器的形式執行時，azure 虛擬機器 ()   <br><br>    實體伺服器  <br><br>    Hyper-V 虛擬機器 <br><br>     VMware 虛擬機器  <br><br>    Azure Stack | V3 UR1                            | 磁片區、共用、資料夾、檔案 <br><br>    僅 (NTFS 的重復資料刪除磁片區)   <br><br>   當工作負載當做 Azure 虛擬機器執行時，不支援系統狀態和裸機 ()  |
| 伺服器 (64 位)           | Windows Server 2008 R2 SP1、Windows Server 2008 SP2 (您必須安裝 [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855))  | 實體伺服器  <br><br>    Hyper-V 虛擬機器  <br><br>      VMware 虛擬機器  <br><br>   Azure Stack | V3 UR1                            | 磁片區、共用、資料夾、檔案、系統狀態/裸機        |
| SQL Server                | SQL Server 2019、2017、2016和 [支援的 sps](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016)、2014和支援的 [sps](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | 實體伺服器  <br><br>     Hyper-V 虛擬機器   <br><br>     VMware 虛擬機器  <br><br>   Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)  <br><br>     Azure Stack | V3 UR1                            | 所有部署案例：資料庫       <br><br>  MABS v3 UR1 支援透過 ReFS 磁片區備份 SQL 資料庫                  |
| Exchange                   | Exchange 2019、2016                                         | 實體伺服器   <br><br>   Hyper-V 虛擬機器  <br><br>      VMware 虛擬機器  <br><br>   Azure Stack  <br><br>    Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時) | V3 UR1                            | 保護 (所有部署案例) ：獨立 Exchange 伺服器、資料庫可用性群組下的資料庫 (DAG)   <br><br>    復原 (所有部署案例)：信箱、DAG 下的信箱資料庫    <br><br>  MABS v3 UR1 支援透過 ReFS 備份 Exchange |
| SharePoint                 | 具有最新 SPs 的 SharePoint 2019、2016                       | 實體伺服器  <br><br>    Hyper-V 虛擬機器 <br><br>    VMware 虛擬機器  <br><br>   Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)   <br><br>   Azure Stack | V3 UR1                            | 保護 (所有部署案例) ：伺服器陣列、前端網頁伺服器內容  <br><br>    復原 (所有部署案例) ：伺服器陣列、資料庫、web 應用程式、檔案或清單專案、SharePoint 搜尋、前端網頁伺服器  <br><br>    不支援保護針對內容資料庫使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 伺服器陣列。 |

## <a name="vm-backup"></a>VM 備份

| **[工作負載]**                                                 | **版本**                                             | **Azure 備份伺服器安裝**                      | **支援的 Azure 備份伺服器** | **保護與修復**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-v 主機伺服器、叢集或 VM 上的 hyper-v 主機-MABS 保護代理程式 | Windows Server 2019、2016、2012 R2、2012               | 實體伺服器  <br><br>    Hyper-V 虛擬機器 <br><br>    VMware 虛擬機器 | V3 UR1                             | 保護： Hyper-v 電腦、叢集共用磁片區 (Csv)   <br><br>    復原：虛擬機器、檔案和資料夾的專案層級復原僅適用于 Windows、磁片區、虛擬硬碟 |
| VMware Vm                                                  | VMware 伺服器5.5、6.0 或6.5、6.7 (授權版本)  | Hyper-v 虛擬機器  <br><br>   VMware 虛擬機器         | V3 UR1                             | 保護：叢集共用磁片區上的 VMware Vm (Csv) 、NFS 及 SAN 儲存體   <br><br>     復原：虛擬機器、檔案和資料夾的專案層級復原僅適用于 Windows、磁片區、虛擬硬碟 <br><br>    不支援 VMware vApps。 |

## <a name="linux"></a>Linux

| **[工作負載]** | **版本**                               | **Azure 備份伺服器安裝**                      | **支援的 Azure 備份伺服器** | **保護與修復**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | 以 Hyper-v 或 VMware 來賓身分執行的 Linux | 實體伺服器、內部部署 Hyper-v VM、VMWare 中的 Windows VM | V3 UR1                             | Hyper-v 必須在 Windows Server 2012 R2、Windows Server 2016 或 Windows Server 2019 上執行。 保護：整部虛擬機器   <br><br>   復原：整部虛擬機器   <br><br>    僅支援檔案一致的快照集。    <br><br>   如需支援的 Linux 散發套件和版本的完整清單，請參閱 [Azure 背書的 linux 發行](../virtual-machines/linux/endorsed-distros.md)版一文。 |

## <a name="azure-expressroute-support"></a>Azure ExpressRoute 支援

您可以使用公用對等互連 (適用于舊線路) 和 Microsoft 對等互連的 Azure ExpressRoute 來備份您的資料。 不支援透過私用對等互連進行備份。

使用公用對等互連：請確認存取下列網域/位址：

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

使用 Microsoft 對等互連，選取下列服務/區域和相關的 [社區] 值：

* Azure Active Directory (12076:5060)
* 根據復原服務保存庫的位置 (Microsoft Azure 區域) 
* 根據復原服務保存庫的位置 Azure 儲存體 () 

如需詳細資訊，請參閱 [ExpressRoute 路由需求](../expressroute/expressroute-routing.md)。

>[!NOTE]
>新線路的公用對等互連已被取代。

## <a name="cluster-support"></a>叢集支援

Azure 備份伺服器可以保護下列叢集應用程式中的資料：

* 檔案伺服器

* SQL Server

* Hyper-v-如果您使用向外延展的 MABS 保護代理程式來保護 Hyper-v 叢集，就無法為受保護的 Hyper-v 工作負載新增次要保護。

* Exchange Server - Azure 備份伺服器可以保護支援之 Exchange Server 版本的非共用磁碟叢集 (叢集連續複寫)，也可以保護設定用於本機連續複寫的 Exchange Server。

* SQL Server - Azure 備份伺服器不支援備份裝載於叢集共用磁碟區 (CSV) 上的 SQL Server 資料庫。

Azure 備份伺服器可以保護位於與 MABS 伺服器相同的網域和子域或受信任網域中的叢集工作負載。 如果您想要保護未信任網域或工作群組中的資料來源，請針對單一伺服器使用 NTLM 或憑證驗證，或是針對叢集只使用憑證驗證。
