---
title: 快速入門：在 Azure 虛擬機器上手動安裝單一執行個體 SAP HANA | Microsoft Docs
description: 在 Azure 虛擬機器上手動安裝單一執行個體 SAP HANA 的快速入門指南
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 0090ffe977dee3e493d726c9eb4d151bcbeb503f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617239"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>快速入門：在 Azure 虛擬機器上手動安裝單一實例 SAP Hana
## <a name="introduction"></a>簡介
當您手動安裝 SAP NetWeaver 7.5 和 SAP Hana 1.0 SP12 時，本指南可協助您在 Azure 虛擬機器上設定單一實例 SAP Hana。 本指南的重點在於如何在 Azure 上部署 SAP Hana。 它不會取代 SAP 檔。 

> [!NOTE]
> 本指南說明如何將 SAP HANA 部署到 Azure VM。 如需如何將 SAP Hana 部署到 HANA 大型實例的相關資訊，請參閱[使用 Azure 上的 SAP 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)。
 
## <a name="prerequisites"></a>Prerequisites
本指南假設您已熟悉這類基礎結構即服務（IaaS）基本概念，如下所示：
 * 如何透過 Azure 入口網站或 PowerShell 部署虛擬機器（Vm）或虛擬網路。
 * Azure 跨平臺命令列介面（CLI），包括使用 JavaScript 物件標記法（JSON）範本的選項。

本指南也假設您已經熟悉：
* SAP HANA 與 SAP NetWeaver，以及如何加以內部部署安裝。
* 如何在 Azure 上安裝和操作 SAP Hana 和 SAP 應用程式實例。
* 下列概念和程序：
   * 在 Azure 上規劃 SAP 部署，其中包括 Azure 虛擬網路規劃和 Azure 儲存體使用量。 請參閱[Azure 上的 SAP NetWeaver 虛擬機器-規劃與實施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。
   * 部署原則及在 Azure 中部署 VM 的方式。 請參閱[適用於 SAP 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)。
   * SAP NetWeaver ABAP SAP Central Services （ASCS）、SAP Central Services （SCS）和佇列複寫伺服器（ERS）在 Azure 上的高可用性。 請參閱 [Azure VM 上的 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide)。
   * 有關如何在 Azure 上 ASCS/SCS 的多重 SID 安裝中改善效率的詳細資料。 請參閱[建立 SAP NetWeaver 多 SID 組態](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid)。 
   * Azure 中以 Linux 驅動的 VM 作為基礎執行 SAP NetWeaver 的準則。 請參閱[在 MICROSOFT AZURE SUSE Linux vm 上執行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)。 本指南提供 Azure Vm 中 Linux 的特定設定。 它也會提供如何正確地將 Azure 儲存體磁片連結至 Linux Vm 的資訊。

可以用於生產案例的 Azure VM 類型會列在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 針對非生產案例，有更多的原生 Azure VM 類型可供使用。
如需 VM 設定和作業的詳細資訊，請參閱[Azure 上的 SAP Hana 基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
如需 SAP Hana 高可用性，請參閱[Azure 虛擬機器的 SAP Hana 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。

如果您想要快速部署 SAP Hana 實例或 S/4HANA 或 BW/4HANA 系統，請考慮使用[SAP 雲端應用裝置程式庫](https://cal.sap.com)。 如需瞭解如何透過 Azure 上的 SAP 雲端應用裝置程式庫部署 S/4HANA 系統，例如[本指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)中的檔。 您只需要 Azure 訂用帳戶，以及可以向 SAP 雲端應用裝置程式庫註冊的 SAP 使用者。

## <a name="additional-resources"></a>其他資源
### <a name="sap-hana-backup"></a>SAP HANA 備份
如需如何備份 Azure Vm 上 SAP Hana 資料庫的相關資訊，請參閱：
* [Azure 虛擬機器上 SAP Hana 的備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)。
* 檔案[層級上的 SAP Hana Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)。
* [SAP Hana 以儲存體快照集為基礎的備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)。

### <a name="sap-cloud-appliance-library"></a>SAP 雲端應用裝置程式庫
如需如何使用 SAP 雲端應用裝置程式庫來部署 S/4HANA 或 BW/4HANA 的相關資訊，請參閱[在 Microsoft Azure 上部署 Sap S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)。

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA 支援的作業系統
如需 SAP Hana 支援作業系統的相關資訊，請參閱[SAP 附注 2235581-SAP Hana：支援的作業系統](https://launchpad.support.sap.com/#/notes/2235581/E)。 Azure VM 僅支援這些作業系統的其中一部份。 支援下列作業系統在 Azure 上部署 SAP HANA︰ 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

如需關於 SAP HANA 和不同 Linux 作業系統的其他 SAP 文件，請參閱：

* [Sap 附注171356： Linux 上的 Sap 軟體：一般資訊](https://launchpad.support.sap.com/#/notes/1984787)。
* [SAP 附注1944799： SLES 作業系統安裝的 SAP Hana 指導方針](http://service.sap.com/sap/support/notes/1944799)。
* [Sap 附注2205917： SAP HANA DB 針對 SLES 12 FOR SAP 應用程式所建議的 OS 設定](https://launchpad.support.sap.com/#/notes/2205917/E)。
* [SAP 附注1391070： LINUX UUID 解決方案](https://launchpad.support.sap.com/#/notes/1391070)。
* [SAP 附注2009879：適用于 Red Hat Enterprise Linux （RHEL）作業系統的 SAP Hana 指導方針](https://launchpad.support.sap.com/#/notes/2009879)。
* [SAP 附注2292690： SAP HANA DB： RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2292690/E)。

### <a name="sap-monitoring-in-azure"></a>Azure 中的 SAP 監視
如需 Azure 中 SAP 監視的相關資訊：

* [Sap Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)討論在 Azure 上使用 Linux VM 進行 SAP 增強監視。 
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)討論 Linux 上的 SAPOSCOL 相關資訊。 
* [Sap Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)討論 sap on Microsoft Azure 的重要監視計量。

### <a name="azure-vm-types"></a>Azure VM 類型
與 SAP Hana 搭配使用的 Azure VM 類型與 SAP 支援的工作負載案例，記載于[SAP 認證的 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 

由 SAP for SAP NetWeaver 或 S/4HANA 應用層認證的 Azure VM 類型，記載于[Sap 附注1928533： azure 上的 sap 應用程式：支援的產品和 AZURE VM 類型](https://launchpad.support.sap.com/#/notes/1928533/E)。

> [!NOTE]
> 只有 Azure Resource Manager 支援 SAP-Linux-Azure 整合，傳統部署模型並不支援。 

## <a name="manual-installation-of-sap-hana"></a>手動安裝 SAP HANA

> [!IMPORTANT]
> 請確定您選取的作業系統已通過 SAP 認證，可 SAP Hana 您所使用的特定 VM 類型。 您可以在[SAP Hana 認證的 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中查閱這些 vm 類型 SAP Hana 認證的 vm 類型和 OS 版本清單。 請務必按一下所列 VM 類型的詳細資料，以取得特定 VM 類型的 SAP Hana 支援作業系統版本的完整清單。 針對本檔中的範例，我們使用了 SAP 在 M 系列 Vm 上 SAP Hana 不支援的 SUSE Linux Enterprise Server （SLES）作業系統版本。
>

本指南說明如何在 Azure VM 上，以下列兩種不同方式手動安裝 SAP HANA：

* 在「安裝資料庫實例」步驟的分散式 NetWeaver 安裝過程中使用 SAP 軟體布建管理員（SWPM）。
* 使用 SAP Hana 資料庫生命週期管理員（HDBLCM）工具，然後安裝 NetWeaver。

您也可以使用 SWPM，在單一 VM 中安裝所有元件，例如 SAP Hana、SAP 應用程式伺服器和 ASCS 實例。 此[SAP Hana 的 blog 公告](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)中會說明這些步驟。 此選項不會在本快速入門手冊中說明，但您必須考慮的問題是相同的。

在您開始安裝之前，建議您先閱讀本指南稍後的「準備 Azure Vm 以手動安裝 SAP Hana」一節。 當您只使用預設的 Azure VM 設定時，這樣做有助於避免數個可能發生的基本錯誤。

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>當您使用 SAP SWPM 時，SAP HANA 安裝的主要步驟
本節會列出當您使用 SAP SWPM 執行分散式 SAP NetWeaver 7.5 安裝時，適用於手動、單一執行個體 SAP HANA 安裝的主要步驟。 本指南稍後會以螢幕擷取畫面形式更詳細說明個別步驟。

1. 建立包含這兩個測試 VM 的 Azure 虛擬網路。
2. 根據 Azure Resource Manager 模型，部署兩個具有作業系統的 Azure Vm。 這個範例會使用 SUSE Linux Enterprise Server 和 SLES for SAP Applications 12 SP1。 
3. 將兩個 Azure 標準或高階儲存體磁片（例如，75-GB 或 500 GB 磁片）連結至應用程式伺服器 VM。
4. 將進階儲存體連結至 HANA DB 伺服器 VM。 如需詳細資訊，請參閱本指南稍後的「磁片設定」一節。
5. 視大小或輸送量需求而定，連接多個磁片。 然後建立等量磁片區。 在 VM 內的 OS 層級，使用邏輯磁片區管理（LVM）或多裝置系統管理（mdadm）工具。
6. 在連接的磁碟或邏輯磁碟區上建立 XFS 檔案系統。
7. 在 OS 層級上掛接新的 XFS 檔案系統。 將一個檔案系統用於所有 SAP 軟體。 例如，將其他檔案系統用於 /sapmnt 目錄和備份。 在 SAP HANA DB 伺服器的進階儲存體磁碟上，將 XFS 檔案系統掛接為 /hana 和 /usr/sap。 這是防止根檔案系統填滿的必要程式。 根檔案系統在 Linux Azure Vm 上不大。 
8. 在 /etc/hosts 檔案中輸入測試 VM 的本機 IP 位址。
9. 在 /etc/fstab 檔案中輸入 **nofail** 參數。
10. 根據您所使用的 Linux 作業系統版本，設定 Linux 核心參數。 如需詳細資訊，請參閱本指南中討論 HANA 和「核心參數」一節的 SAP 附注。
11. 新增交換空間。
12. (選擇性) 在測試 VM 上安裝圖形化桌面。 否則，請使用遠端 SAPinst 安裝。
13. 從 SAP Service Marketplace 下載 SAP 軟體。
14. 在應用程式伺服器 VM 上安裝 SAP ASCS 執行個體。
15. 使用 NFS，在測試 VM 之間共用 /sapmnt 目錄。 應用程式伺服器 VM 為 NFS 伺服器。
16. 在 DB 伺服器 VM 上使用 SWPM，安裝包含 HANA 的資料庫實例。
17. 在應用程式伺服器 VM 上，安裝主要應用程式伺服器 (PAS)。
18. 啟動 SAP 管理主控台 (SAP MC)。 例如，連線到 SAP GUI 或 HANA Studio。

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>當您使用 HDBLCM 時，SAP HANA 安裝的主要步驟
本節會列出當您使用 SAP HDBLCM 執行分散式 SAP NetWeaver 7.5 安裝時，適用於手動、單一執行個體 SAP HANA 安裝的主要步驟。 整份指南會以螢幕擷取畫面形式更詳細說明個別步驟。

1. 建立包含這兩個測試 VM 的 Azure 虛擬網路。
2. 根據 Azure Resource Manager 模型，部署兩個具有作業系統的 Azure Vm。 此範例使用 SLES 和 SLES for SAP Applications 12 SP1。
3. 將兩個 Azure 標準或高階儲存體磁片（例如，75-GB 或 500 GB 磁片）連結至應用程式伺服器 VM。
4. 將進階儲存體連結至 HANA DB 伺服器 VM。 如需詳細資訊，請參閱本指南稍後的「磁片設定」一節。
5. 視大小或輸送量需求而定，連接多個磁片。 在 VM 內的 OS 層級，使用邏輯磁片區管理或 mdadm 工具來建立等量磁片區。
6. 在連接的磁碟或邏輯磁碟區上建立 XFS 檔案系統。
7. 在 OS 層級上掛接新的 XFS 檔案系統。 將一個檔案系統用於所有 SAP 軟體。 例如，將其他檔案系統用於 /sapmnt 目錄和備份。 在 SAP HANA DB 伺服器的進階儲存體磁碟上，將 XFS 檔案系統掛接為 /hana 和 /usr/sap。 此程式是協助防止根檔案系統填滿的必要步驟。 根檔案系統在 Linux Azure Vm 上不大。
8. 在 /etc/hosts 檔案中輸入測試 VM 的本機 IP 位址。
9. 在 /etc/fstab 檔案中輸入 **nofail** 參數。
10. 根據您所使用的 Linux 作業系統版本，設定核心參數。 如需詳細資訊，請參閱本指南中討論 HANA 和「核心參數」一節的 SAP 附注。
11. 新增交換空間。
12. (選擇性) 在測試 VM 上安裝圖形化桌面。 否則，請使用遠端 SAPinst 安裝。
13. 從 SAP Service Marketplace 下載 SAP 軟體。
14. 在 HANA DB 伺服器 VM 上，建立群組識別碼為 1001 的群組 "sapsys"。
15. 使用 HANA 資料庫生命週期管理員，在 DB 伺服器 VM 上安裝 SAP Hana。
16. 在應用程式伺服器 VM 上安裝 SAP ASCS 執行個體。
17. 使用 NFS，在測試 VM 之間共用 /sapmnt 目錄。 應用程式伺服器 VM 為 NFS 伺服器。
18. 在 HANA DB 伺服器 VM 上使用 SWPM，安裝包含 HANA 的資料庫實例。
19. 在應用程式伺服器 VM 上安裝主要應用程式伺服器。
20. 啟動 SAP MC。 透過 SAP GUI 或 HANA Studio 連線。

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>準備 Azure VM 以便手動安裝 SAP HANA
本節包含下列主題：

* OS 更新
* 磁碟設定
* 核心參數
* 檔案系統
* /etc/hosts 檔案
* /etc/fstab 檔案

### <a name="os-updates"></a>OS 更新
安裝其他軟體之前，請先檢查 Linux OS 更新和修正程式。 藉由安裝修補程式，您可以避免打電話給支援人員。

請確定您使用：
* 適用於 SAP 應用程式的 SUSE Linux Enterprise Server。
* 適用於 SAP 應用程式的 Red Hat Enterprise Linux 或適用於 SAP HANA 的 Red Hat Enterprise Linux。 

如果您尚未這麼做，請向 Linux 廠商的 Linux 訂用帳戶註冊 OS 部署。 SUSE 有適用于 SAP 應用程式的 OS 映射，其中已包含服務，且會自動註冊。

以下範例說明如何使用**zypper**命令來檢查 SUSE Linux 的可用修補程式：

 `sudo zypper list-patches`

視問題的種類而定，修補程式會依分類和嚴重性歸類。 類別目錄的常用值如下： 
- 安全性
- 建議
- 選用
- 功能
- Document
- Yast

嚴重性常用的值如下：

- 重大
- 重要事項
- 中度
- 低
- [未指定]

**Zypper** 命令只會尋找已安裝套件所需要的更新。 例如，您可以使用此命令：

`sudo zypper patch  --category=security,recommended --severity=critical,important`

您可以新增 `--dry-run` 參數來測試更新，而不需實際更新系統。


### <a name="disk-setup"></a>磁碟設定
Azure 上 Linux VM 中的根目錄檔案系統有大小限制。 因此，您必須將額外的磁碟空間附加至 Azure VM，才能執行 SAP。 針對 SAP 應用程式伺服器 Azure Vm，使用 Azure 標準儲存體磁片可能就已足夠。 針對 SAP Hana DBMS Azure Vm，您必須使用 Azure premium 儲存體磁片來進行生產和非生產的實施。

根據[SAP HANA TDI 儲存體需求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，建議您採用下列 Azure premium 儲存體設定： 

| VM SKU | RAM |  /hana/data 和 /hana/log <br /> 與 LVM 或 mdadm 等量 | HANA/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

在建議的磁片設定中，HANA 資料磁片區和記錄磁片區會放在與 LVM 或 mdadm 等量的同一組 Azure premium 儲存體磁片上。 不需要定義任何 RAID 冗余層級，因為 Azure premium 儲存體會保留三個磁片映射以供複製。 

若要確定您已設定足夠的儲存空間，請參閱[SAP HANA TDI 儲存需求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)和[SAP Hana 伺服器安裝與更新指南](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。 也請考慮不同 Azure premium 儲存體磁片的不同虛擬硬碟（VHD）輸送量，如[適用于 vm 的高效能 premium 儲存體和受控磁片](../../windows/disks-types.md)中所述。 

您可以將更多的 premium 儲存體磁片新增至 HANA DBMS Vm，以儲存資料庫或交易記錄備份。

如需用來設定等量配置的兩個主要工具的詳細資訊，請參閱：

* [在 Linux 上設定軟體 RAID](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* [在 Azure 中的 Linux VM 上設定 LVM](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如需如何將磁片連接至以虛擬作業系統身分執行 Linux 之 Azure Vm 的詳細資訊，請參閱[將磁片新增至 LINUX VM](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

使用 Azure premium Ssd 時，您可以定義磁碟快取模式。 若為保存/hana/data 和/hana/log 的等量集合，請停用磁碟快取。 若是其他磁片區，也就是磁片，將快取模式設定為**ReadOnly**。

若要尋找用來建立 Vm 的範例 JSON 範本，請參閱[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)。
vm-simple-sles 範本是基本的範本。 它包含儲存體區段與其他 100 GB 的資料磁碟。 使用此範本作為基底。 您可以針對特定的組態採用範本。

> [!NOTE]
> 請務必使用 UUID 來連結 Azure 儲存體磁片，如在[MICROSOFT AZURE SUSE Linux vm 上執行 SAP NetWeaver](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中所述。

在測試環境中，會將兩個 Azure 標準儲存體磁片連接至 SAP 應用程式伺服器 VM，如下列螢幕擷取畫面所示。 一個磁片會儲存所有 SAP 軟體，例如 NetWeaver 7.5、SAP GUI 和 SAP Hana，以供安裝。 第二個磁片可確保有足夠的可用空間可用於其他需求。 例如，備份和測試資料和/sapmnt 目錄（也就是 SAP 設定檔）必須在屬於相同 SAP 環境的所有 Vm 之間共用。

![SAP HANA 應用程式伺服器的 [磁碟] 視窗，其中顯示兩個資料磁碟及其大小](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>核心參數
SAP Hana 需要特定的 Linux 核心設定。 這些核心設定不是標準 Azure 資源庫映射的一部分，必須手動設定。 依據您是使用 SUSE 還是 Red Hat，參數可能不一樣。 先前所列的 SAP 附注會提供這些參數的相關資訊。 在顯示的螢幕擷取畫面中，使用 SUSE Linux 12 SP1。 

SLES for SAP Applications 12 正式運作和 SLES for SAP Applications 12 SP1 具有新的工具（**微調 adm**），會取代舊的**sapconf**工具。 **tuned-adm** 有特別的 SAP HANA 設定檔可供其使用。 若要調整系統的 SAP Hana，請以根使用者身分輸入下列設定檔：

   `tuned-adm profile sap-hana`

如需有關 **tuned-adm** 的詳細資訊，請參閱[關於 tuned-adm 的 SUSE 文件](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)。

在下列螢幕擷取畫面中，您可以根據所需的 SAP Hana 設定，查看**調整 adm**如何變更 `transparent_hugepage` 和 `numa_balancing` 值：

![tuned-adm 工具會根據必要的 SAP HANA 設定來變更值](./media/hana-get-started/image005.jpg)

若要永久保留 SAP HANA 核心設定，請在 SLES 12 上使用 **grub2**。 如需**grub2**的詳細資訊，請參閱 SUSE[檔的設定檔結構](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)一節。

下列螢幕擷取畫面顯示如何在組態檔中變更核心設定，然後使用 **grub2-mkconfig** 進行編譯：

![在組態檔中變更核心設定，然後使用 grub2-mkconfig 進行編譯](./media/hana-get-started/image006.jpg)

另一個選項是使用 YaST 和 [開機載入器] > [核心參數] 設定來變更這些設定：

![YaST 開機載入器中的 [核心參數] 設定索引標籤](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>檔案系統
下列螢幕擷取畫面顯示兩個檔案系統，這兩者均建立於這兩個連接之 Azure 標準儲存體磁碟上的 SAP 應用程式伺服器 VM 上。 這兩個檔案系統的類型都是 XFS，而且會掛接至/sapdata 和/sapsoftware。

以這種方式來結構您的檔案系統並不是必要的。 您有其他選項可讓您瞭解如何結構磁碟空間。 最重要的考量是防止根目錄檔案系統的可用空間用盡。

![在 SAP 應用程式伺服器 VM 上建立兩個檔案系統](./media/hana-get-started/image008.jpg)

針對 SAP Hana DB VM，在資料庫安裝期間，當您使用 SAPinst 搭配 SWPM 和**一般**安裝選項時，所有專案都會安裝在/hana 和/usr/sap 之下底下 SAP HANA 記錄備份的預設位置是在 /usr/sap 之下。 同樣地，請務必防止根檔案系統用盡儲存空間。 在使用 SWPM 安裝 SAP Hana 之前，請確定/hana 和/usr/sap 下有足夠的可用空間。

如需 SAP Hana 的標準檔案系統配置的說明，請參閱[SAP Hana 伺服器安裝與更新指南](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。

![在 SAP 應用程式伺服器 VM 上建立的其他檔案系統](./media/hana-get-started/image009.jpg)

當您在標準 SLES/SLES for SAP Applications 12 Azure 資源庫映射上安裝 SAP NetWeaver 時，會顯示一則訊息，指出沒有交換空間，如下列螢幕擷取畫面所示。 若要關閉此訊息，您可以使用 **dd**、**mkswap** 和 **swapon** 手動新增分頁檔。 若要深入瞭解，請在 SUSE 檔的[使用 YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)分割區一節中，搜尋「手動新增交換檔」。

另一個選項是使用 Linux VM 代理程式設定交換空間。 如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南](../../extensions/agent-linux.md)。

![快顯訊息，建議的交換空間不足](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/etc/hosts 檔案
開始安裝 SAP 前，請確定在 /etc/hosts 檔案中包含 SAP VM 的主機名稱和 IP 位址。 在一個 Azure 虛擬網路內部署所有 SAP Vm。 然後使用內部 IP 位址，如下所示：

![SAP VM 的主機名稱和 IP 位址會列於 /etc/hosts 檔案中](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/etc/fstab 檔案

將**nofail**參數新增至 fstab 檔案會很有説明。 如此一來，如果磁片發生問題，VM 就不會在開機過程中停止回應。 但請記得，可能無法取得額外的磁碟空間，而且程序可能會填滿根目錄檔案系統。 如果遺失 /hana，SAP HANA 將無法啟動。

![將 nofail 參數新增至 fstab 檔案](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES for SAP Applications 12 上的圖形化 GNOME 桌面
本節說明如何：

* 在 SLES 12/SLES for SAP Applications 12 上安裝 GNOME 桌面和 xrdp。
* 在 SLES 12/SLES for SAP Applications 12 上使用 Firefox，以執行以 JAVA 為基礎的 SAP MC。

您也可以使用本指南中未說明的替代方案，例如 Xterminal 或 VNC。

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>在 SLES 12/SLES for SAP Applications 12 上安裝 GNOME 桌面和 xrdp
如果您有 Windows 背景，就可以輕鬆地直接在 SAP Linux Vm 內使用圖形化桌面來執行 Firefox、SAPinst、SAP GUI、SAP MC 或 HANA Studio。 接著，您可以從 Windows 電腦透過遠端桌面通訊協定（RDP）連線到 VM。 根據您的公司原則，將 Gui 新增至生產環境和非生產 Linux 系統，您可能會想要在伺服器上安裝 GNOME。 請遵循下列步驟，在 Azure SLES 12/SLES for SAP Applications 12 VM 上安裝 GNOME 桌面。

1. 藉由輸入下列命令（例如在 PuTTY 視窗中）來安裝 GNOME 桌面：

   `zypper in -t pattern gnome-basic`

2. 安裝 xrdp 以允許透過 RDP 連接到 VM：

   `zypper in xrdp`

3. 編輯 /etc/sysconfig/windowmanager，並將預設的視窗管理員設定為 GNOME：

   `DEFAULT_WM="gnome"`

4. 執行 **chkconfig** 以確保 xrdp 會在重新開機後自動啟動：

   `chkconfig -level 3 xrdp on`

5. 如果您有 RDP 連線的問題，請嘗試重新開機，例如，從 PuTTY 視窗：

   `/etc/xrdp/xrdp.sh restart`

6. 如果上一個步驟中所述的 xrdp 重新啟動無法運作，請檢查 .pid 檔案：

   `check /var/run` 

   尋找 `xrdp.pid`。 如果找到的話，請將它移除，並嘗試重新啟動。

### <a name="start-sap-mc"></a>啟動 SAP MC
在您安裝 GNOME 桌面之後，請從 Firefox 啟動圖形化 JAVA 型 SAP MC。 如果它在 Azure SLES 12/SLES for SAP Applications 12 VM 中執行，它可能會顯示錯誤。 錯誤發生的原因是缺少 JAVA 瀏覽器外掛程式。

啟動 SAP MC 的 URL 為 `<server>:5<instance_number>13`。

如需詳細資訊，請參閱[啟動以 web 為基礎的 SAP 管理主控台](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)。

下列螢幕擷取畫面顯示遺失 JAVA 瀏覽器外掛程式時所顯示的錯誤訊息：

![指出遺失 Java 瀏覽器外掛程式的錯誤訊息](./media/hana-get-started/image013.jpg)

解決這個問題的其中一種方式，是使用 YaST 安裝遺失的外掛程式，如下列螢幕擷取畫面所示：

![使用 YaST 安裝遺失的外掛程式](./media/hana-get-started/image014.jpg)

當您重新輸入 SAP 管理主控台 URL 時，系統會要求您啟用外掛程式：

![要求啟用外掛程式的對話方塊](./media/hana-get-started/image015.jpg)

您可能也會收到關於遺失檔案 (javafx.properties) 的錯誤訊息。 這與適用于 SAP GUI 7.4 的 Oracle JAVA 1.8 需求有關。 如需詳細資訊，請參閱[SAP 附注 2059429](https://launchpad.support.sap.com/#/notes/2059424)。
使用 SLES/SLES for SAP Applications 12 所提供的 IBM JAVA 版本和 openjdk 套件，不會包含所需的 javafx 屬性檔案。 解決方案是從 Oracle 下載 Java SE 8。

如需與 OpenSUSE 上 openjdk 類似問題的相關資訊，請參閱討論區對話 [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)。

## <a name="manual-installation-of-sap-hana-swpm"></a>手動安裝 SAP HANA：SWPM
本節的一系列螢幕擷取畫面顯示當您使用 SWPM 搭配 SAPinst 時，如何安裝 SAP NetWeaver 7.5 和 SAP Hana SP12 的主要步驟。 在 NetWeaver 7.5 安裝過程中，SWPM 也可以將 HANA 資料庫安裝為單一實例。

在範例測試環境中，我們已安裝一個 Advanced Business Application 程式設計（ABAP）應用程式伺服器。 如下列螢幕擷取畫面所示，我們使用 [**分散式系統**] 選項，在一個 Azure VM 中安裝 ASCS 和主要應用程式伺服器實例。 我們使用 SAP Hana 做為另一個 Azure VM 中的資料庫系統。

![使用 [分散式系統] 選項安裝 ASCS 和主要應用程式伺服器執行個體](./media/hana-get-started/image012.jpg)

在應用程式伺服器 VM 上安裝 ASCS 實例之後，SAP 管理主控台中會以綠色圖示來識別它。 包含 SAP 設定檔目錄的/sapmnt 目錄必須與 SAP Hana DB 伺服器 VM 共用。 DB 安裝步驟需要存取此資訊。 提供存取的最佳方式是使用可利用 YaST 設定的 NFS。

![使用綠色圖示來顯示安裝在應用程式伺服器 VM 上的 ASCS 實例的 SAP 管理主控台](./media/hana-get-started/image016.jpg)

在應用程式伺服器 VM 上，會使用**rw**和**no_root_squash**選項，透過 NFS 共用/sapmnt 目錄。 預設值為 **ro** 和 **root_squash**，這可能導致安裝資料庫執行個體時發生問題。

![透過 NFS 使用 rw 和 no_root_squash 選項共用 /sapmnt 目錄](./media/hana-get-started/image017b.jpg)

如下列螢幕擷取畫面所示，您必須在 SAP Hana DB 伺服器 VM 上，使用**NFS 用戶端**和 YaST 來設定應用程式伺服器 vm 中的/sapmnt 共用：

![使用 NFS 用戶端設定的/sapmnt 共用](./media/hana-get-started/image018b.jpg)

若要執行分散式 NetWeaver 7.5 安裝（也就是**資料庫實例**），請登入 SAP Hana DB 伺服器 VM 並開始 SWPM：

![登入 SAP HANA DB 伺服器 VM，然後啟動 SWPM，藉以安裝資料庫執行個體](./media/hana-get-started/image019.jpg)

在您選取 [**一般**安裝] 和安裝媒體的路徑之後，請輸入 db SID、主機名稱、實例號碼及 db 系統管理員密碼：

![SAP HANA 資料庫系統管理員登入頁面](./media/hana-get-started/image035b.jpg)

輸入 DBACOCKPIT 結構描述的密碼：

![DBACOCKPIT 結構描述的密碼輸入方塊](./media/hana-get-started/image036b.jpg)

輸入 SAPABAP1 結構描述密碼的問題：

![輸入 SAPABAP1 結構描述密碼的問題](./media/hana-get-started/image037b.jpg)

完成每個工作之後，就會在 DB 安裝程序的每個階段旁邊顯示綠色核取記號。 訊息「執行 .。。資料庫實例已完成」。

![含有確認訊息的完成工作視窗](./media/hana-get-started/image023.jpg)

成功安裝之後，SAP 管理主控台也會顯示含有綠色圖示的資料庫實例。 它會顯示 SAP Hana 進程的完整清單，例如 hdbindexserver 和 hdbcompileserver。

![含有 SAP HANA 程序清單的 SAP 管理主控台視窗](./media/hana-get-started/image024.jpg)

下列螢幕擷取畫面會顯示 SWPM 在 HANA 安裝期間於 /hana/shared 目錄下所建立的部分檔案結構。 因為沒有指定不同路徑的選項，所以在使用 SWPM SAP Hana 安裝之前，請務必先在/hana 目錄下掛接額外的磁碟空間。 此步驟可防止根檔案系統用盡可用空間。

![在 HANA 安裝期間建立 /hana/shared 目錄檔案結構](./media/hana-get-started/image025.jpg)

這個螢幕擷取畫面會顯示 /usr/sap 目錄的檔案結構：

![/usr/sap 目錄檔案結構](./media/hana-get-started/image026.jpg)

分散式 ABAP 安裝的最後一個步驟，是安裝主要應用程式伺服器執行個體：

![將主要應用程式伺服器執行個體顯示為最後一個步驟的 ABAP 安裝](./media/hana-get-started/image027b.jpg)

安裝主要應用程式伺服器實例和 SAP GUI 之後，請使用**DBA 環境**交易來確認 SAP Hana 安裝已正確完成：

![確認安裝成功的 DBA Cockpit 視窗](./media/hana-get-started/image028b.jpg)

在最後一個步驟中，您可能會想要先在 SAP 應用程式伺服器 VM 中安裝 HANA Studio。 然後連接到在 DB 伺服器 VM 上執行的 SAP Hana 實例。

![在 SAP 應用程式伺服器 VM 中安裝 SAP HANA Studio](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>手動安裝 SAP HANA：HDBLCM
除了使用 SWPM 在分散式安裝過程中安裝 SAP HANA 之外，您還能使用 HDBLCM，先獨立安裝 HANA。 例如，您接著可以安裝 SAP NetWeaver 7.5。 本節中的螢幕擷取畫面會顯示此程序的運作方式。

如需有關 HANA HDBLCM 工具的詳細資訊，請參閱：

* [為您的工作選擇正確的 SAP HANA HDBLCM](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)。
* [SAP Hana 生命週期管理工具](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)。
* [SAP Hana 伺服器安裝與更新指南](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)。

您想要避免 `\<HANA SID\>adm user`的預設群組識別碼設定發生問題，這是由 HDBLCM 工具所建立。 透過 HDBLCM 安裝 SAP Hana 之前，請使用群組識別碼 `1001`來定義名為 `sapsys` 的新群組：

![使用群組識別碼 1001 定義的新群組 "sapsys"](./media/hana-get-started/image030.jpg)

當您第一次啟動 HDBLCM 時，會顯示簡單的 [開始] 功能表。 選取 [專案 1]、[**安裝新系統**]：

![HDBLCM 開始視窗中的 [安裝新的系統] 選項](./media/hana-get-started/image031.jpg)

下列螢幕擷取畫面顯示您先前選取的所有重要選項。

> [!IMPORTANT]
> 針對 HANA 記錄檔和資料磁片區命名的目錄，以及在此範例中/hana/shared 的安裝路徑和/usr/sap，都不能是根檔案系統的一部分。 這些目錄屬於連接至 VM 的 Azure 資料磁片。 如需詳細資訊，請參閱磁片設定一節。 

這個方法有助於避免根檔案系統的空間不足。 請注意，HANA 系統管理員具有 [使用者識別碼] `1005`，而且是在安裝之前定義之 `sapsys` 群組（識別碼為 `1001`）的一部分。

![先前選取的所有重要 SAP HANA 元件清單](./media/hana-get-started/image032.jpg)

檢查/etc/passwd 目錄中的 `\<HANA SID\>adm user` 詳細資料。 尋找 `azdadm`，如下列螢幕擷取畫面所示：

![HANA \<HANA SID\>adm 使用者詳細資料會列於 /etc/passwd 目錄中](./media/hana-get-started/image033.jpg)

使用 HDBLCM 安裝 SAP HANA 之後，您就能在 SAP HANA Studio 中看見檔案結構，如下列螢幕擷取畫面所示。 尚未提供 SAPABAP1 結構描述 (其中包括所有的 SAP NetWeaver 資料表)。

![SAP HANA Studio 中的 SAP HANA 檔案結構](./media/hana-get-started/image034.jpg)

安裝 SAP HANA 之後，就能在其上安裝 SAP NetWeaver。 如下列螢幕擷取畫面所示，使用 SWPM 以分散式安裝的方式執行安裝。 上一節將說明此程式。 當您使用 SWPM 安裝資料庫實例時，您可以使用 HDBLCM 來輸入相同的資料。 例如，您可以輸入主機名稱、HANA SID 和實例號碼。 SWPM 接著會使用現有的 HANA 安裝，並加入多個結構描述。

![使用 SWPM 執行分散式安裝](./media/hana-get-started/image035b.jpg)

下列螢幕擷取畫面會顯示 SWPM 安裝步驟，您可以在其中輸入 DBACOCKPIT 結構描述的相關資料：

![輸入 DBACOCKPIT 結構描述資料的 SWPM 安裝步驟](./media/hana-get-started/image036b.jpg)

輸入 SAPABAP1 結構描述的相關資料：

![輸入 SAPABAP1 結構描述的相關資料](./media/hana-get-started/image037b.jpg)

SWPM 資料庫實例安裝完成之後，您可以在 SAP Hana Studio 中看到 SAPABAP1 架構：

![SAP HANA Studio 中的 SAPABAP1 結構描述](./media/hana-get-started/image038b.jpg)

最後，在完成 SAP 應用程式伺服器和 SAP GUI 安裝之後，請使用**DBA 環境**交易來驗證 HANA DB 實例：

![使用 DBA Cockpit 來交易驗證 HANA DB 執行個體](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP 軟體下載
您可以從 SAP Service Marketplace 下載軟體，如下列螢幕擷取畫面所示。

下載適用於 Linux/HANA 的 NetWeaver 7.5：

 ![用於下載 NetWeaver 7.5 的 SAP 服務安裝和升級視窗](./media/hana-get-started/image001.jpg)

下載 HANA SP12 平台版本：

 ![用於下載 HANA SP12 Platform Edition 的 SAP 服務安裝和升級視窗](./media/hana-get-started/image002.jpg)

