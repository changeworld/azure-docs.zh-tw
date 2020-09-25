---
title: 開始使用 Azure 上的 SAP VM | Microsoft Docs
description: 了解在 Microsoft Azure 中的虛擬機器 (VM) 上執行的 SAP 解決方案
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f778e996eeda0f2b850864c9c90cd522c411b032
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328015"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 來裝載及執行 SAP 工作負載案例

當您使用 Microsoft Azure 時，您能夠在可調整、符合規範且經企業證明的平台上，可靠地執行您的任務關鍵性 SAP 工作負載和案例。 您會獲得 Azure 在可擴縮性、彈性及成本節省上的好處。 Microsoft 與 SAP 的擴大合作，讓您可以在 Azure 中的開發、測試及生產案例上執行 SAP 應用程式，並獲得完整的支援。 從 SAP NetWeaver 到 SAP S/4HANA、Linux 上的 SAP BI 到 Windows 上的 SAP BI，乃至於 SAP HANA 到 SQL，全部含括在內。

除了在 Azure 上裝載具有不同 DBMS 的 SAP NetWeaver 案例外，您還可以裝載其他 SAP 工作負載案例，例如 Azure 上的 SAP BI。 

適用於 Azure 的 SAP HANA 是獨一無二的供應項目，可讓 Azure 顯得與眾不同。 為了讓使用者裝載需要更多記憶體和 CPU 資源且涉及 SAP HANA 的 SAP 案例，Azure 提供使用客戶專用的裸機硬體。 使用此解決方案來針對 S/4HANA 或其他 SAP HANA 工作負載執行需要最多 24 TB (向外延展至 120 TB) 之記憶體的 SAP HANA 部署。 

在 Azure 中裝載 SAP 工作負載案例也可能會產生身分識別整合和單一登入的需求。 此情況可能會在使用 Azure Active Directory (Azure AD) 來連線不同的 SAP 元件和 SAP 軟體即服務 (SaaS) 或平台即服務 (PaaS) 供應項目時發生。 這類使用 Azure AD 和 SAP 實體的整合和單一登入案例的清單，已在＜AAD SAP 身分識別整合及單一登入＞一節中說明並加以記載。

## <a name="changes-to-the-sap-workload-section"></a>對＜SAP 工作負載＞一節的變更
對＜Azure 上的 SAP 工作負載＞一節中的文件所做的變更已列於此文章結尾。 變更記錄中的項目會保留約 180 天。

## <a name="you-want-to-know"></a>您想要知道
如果您有特定問題，我們會在起始頁面的這一節中將您指向特定的文件或流程。 您想要知道：

- 有哪些 SAP 軟體版本和作業系統版本支援哪些 Azure VM 和 HANA 大型執行個體單位。 請參閱 [Azure 部署支援哪些 SAP 軟體](./sap-supported-product-on-azure.md) \(部分機器翻譯\) 文件以取得解答及找到相關資訊的程序
- Azure VM 和 HANA 大型執行個體支援哪些 SAP 部署案例。 在下列文件中可以找到支援案例的相關資訊：
    - [Azure 虛擬機器支援案例上的 SAP 工作負載](./sap-planning-supported-configurations.md) \(部分機器翻譯\)
    - [HANA 大型執行個體的支援案例](./hana-supported-scenario.md) \(部分機器翻譯\)
- 若要了解在不同的 Azure 區域中，可以使用哪些 Azure 服務、Azure VM 類型及 Azure 儲存體服務，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)網站 
- 除了支援 Windows 和 Pacemaker 之外，協力廠商 HA 框架是否可運作？ 請參閱[SAP 支援附注](https://launchpad.support.sap.com/#/notes/1928533)的下半部 #1928533
- 哪一個 Azure 儲存體最適合我的案例？ 讀取 [SAP 工作負載的 Azure 儲存體類型](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage)

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體)

一系列的文件將引導您逐步完成 Azure 上的 SAP HANA (大型執行個體)，或簡稱 HANA 大型執行個體。 如需 HANA 大型執行個體的相關資訊，請從 [Azure 上的 SAP HANA (大型執行個體) 的概觀與架構](./hana-overview-architecture.md) \(部分機器翻譯\) 文件開始，然後逐步完成＜HANA 大型執行個體＞一節中的相關文件



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP HANA
文件的本節涵蓋 SAP HANA 的不同層面。 作為先決條件，您應該先熟悉提供 Azure IaaS 基礎服務的 Azure 主要服務。 因此，您需要 Azure 計算、儲存體和網路的知識。 這些主題中，有許多會在 SAP NetWeaver 相關的 [Azure 規劃指南](./planning-guide.md) \(部分機器翻譯\) 中探討。 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>在 Azure 虛擬機器上部署的 SAP NetWeaver
本節列出 Azure 上的 SAP NetWeaver、SAP LaMa 和 Business One 的規劃和部署檔。 文件會專注在於 Azure 上搭配 SAP 工作負載之非 HANA 資料庫的基本概念及使用方式。 高可用性的檔和文章也是在 Azure 中 SAP Hana 高可用性的基礎

## <a name="sap-netweaver-and-s4hana-high-availability"></a>SAP NetWeaver 和 S/4HANA 高可用性
SAP 應用層和 DBMS 的高可用性記載于從[適用于 Sap NetWeaver 的 Azure 虛擬機器高可用性](./sap-high-availability-guide-start.md)檔開始的詳細資料中



## <a name="integrate-azure-ad-with-sap-services"></a>整合 Azure AD 與 SAP 服務
在本節中，您可以找到有關如何使用大部分的 SAP SaaS 和 PaaS 服務（NetWeaver 和 Fiori）設定 SSO 的資訊。 



## <a name="documentation-on-integration-of-azure-services-into-sap-components"></a>將 Azure 服務整合至 SAP 元件的檔

- [在 Power BI Desktop 中使用 SAP HANA](/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 連接器](/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory 提供 SAP HANA 和 Business Warehouse 資料整合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>變更記錄檔

- 09/02/2020：在您[的可用 sku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)中新增適用于紐約的新認證 sku 
- 09/08/2020：在 [SLES 上變更 Azure vm 上 SAP Hana 的高可用性](./sap-hana-high-availability.md) ，以闡明 stonith 定義
- 09/03/2020： [SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 設定中的變更，可使用 Ultra 磁片以每 1 GB 容量調整為基本 2 IOPS
- 09/02/2020：在 [可用的 sku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus) 中變更，以在哪些 sku 已通過 HANA 認證時變得更透明
- 08/28/2020：在 [SLES WITH ANF 上的 Azure vm 上變更 SAP NW 的 HA](./high-availability-guide-suse-netapp-files.md) ，以修正打字問題
- 08/25/2020： [SAP ASCS/scs 與 wsfc 和共用磁片的 HA 指南](./sap-high-availability-guide-wsfc-shared-disk.md)變更，使用 wsfc 和共用磁片 [準備適用于 SAP ASCS/scs 的 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-shared-disk.md) ，並 [安裝具有 WSFC 和共用磁片的 sap NW HA](./sap-high-availability-guide-wsfc-shared-disk.md) ，以引入使用 AZURE 共用磁片和檔 sap ERS2 架構的選項
- 08/25/2020：[使用 WSFC 和 Azure 共用磁片的 SAP ASCS/SCS 的多重 SID HA 指南](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)版本
- 08/25/2020： [SAP ASCS/SCS 的 HA 指南變更了 WSFC 和 Azure NetApp Files (SMB) ](./high-availability-guide-windows-netapp-files-smb.md)，使用 wsfc 和檔案 [共用準備適用于 SAP ASCS/scs 的 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-file-share.md)、搭配 wsfc 和 [共用磁片的 sap ASCS/scs 的多重 sid HA 指南](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) ，以及搭配 wsfc 和 SOFS 檔案共用的 sap [ASCS/scs 的多重 SID ha 指南](./sap-ascs-ha-multi-sid-wsfc-file-share.md) ，以搭配使用 WFC 和共用磁片的 sap ASCS/scs ha 指南中的內容更新和重建 
- 08/21/2020：將新的 OS 版本新增至適用于 [HANA 大型實例的相容作業系統](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) ，作為 I 和 II 類型的中的
- 08/18/2020：[在 RHEL 上使用 ANF 擴大 SAP Hana 的 HA](./sap-hana-high-availability-netapp-files-red-hat.md)版本
- 08/17/2020：新增有關使用 Azure Site Recovery 的資訊，以將 SAP NetWeaver 系統從內部部署移至 Azure 的 [Azure 虛擬機器規劃和實行 Sap NetWeaver](./planning-guide.md)
- 08/14/2020：為[SAP 工作負載的 IBM Db2 Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)文章中的 db2 新增磁片設定建議
- 08/11/2020：將 RHEL 7.6 新增至適用于 [HANA 大型實例的作業系統](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) ，作為 I 類型的
- 08/10/2020：在 azure[虛擬機器儲存體設定中 SAP Hana azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中 SAP Hana 儲存體設定，並對[Azure 上的 SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)進行一些更新，以提升成本：規劃和部署檢查清單
- 08/04/2020：在 Azure 中的 [SLES 上設定 Pacemaker](./high-availability-guide-suse-pacemaker.md) 的變更，並在 [AZURE 中設定 RHEL](./high-availability-guide-rhel-pacemaker.md) 上的 Pacemaker，以強調 Pacemaker 叢集的可靠名稱解析重要性
- 08/04/2020：在 WFCS 上使用檔案共用的 sap [NW ha](./sap-high-availability-installation-wsfc-file-share.md)變更、在 WFCS 上搭配[共用磁片的 sap Nw Ha](./sap-high-availability-installation-wsfc-shared-disk.md)、 [azure vm](./high-availability-guide.md)上的 Ha for sap nw、 [AZURE vm 上的 ha for sap Nw](./high-availability-guide-suse.md)[適用于 azure vm 上的 azure vm 上的 ha （具有 ANF](./high-availability-guide-suse-netapp-files.md)的 ha）、適用于 azure vm 上的 azure 虛擬機器的[ha](./high-availability-guide-suse-multi-sid.md)、適用于 rhel 的 azure vm 上的 sap nw、適用于 rhel 上的 azure vm 上的 sap nw 的[高可用性](./high-availability-guide-rhel.md)，以及在 rhel[上的 azure](./high-availability-guide-rhel-multi-sid.md) vm[上使用 sap](./high-availability-guide-rhel-netapp-files.md)`enque/encni/set_so_keepalive`
- 07/23/2020：已將 [儲存在 SAP Hana 大型實例，其中包含 Azure 保留](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) 專案，說明購買 SAP Hana 大型實例保留專案之前需要知道的事項，以及如何進行購買
- 07/16/2020：描述如何使用 Azure PowerShell 在[部署指南](deployment-guide.md)中安裝適用于 SAP 的新 VM 擴充功能
- 7/04/2020：推出  [適用于 SAP 解決方案的 Azure 監視器 (預覽版) ](./azure-monitor-overview.md)
- 07/01/2020：根據檔中的 Azure premium 儲存體高載功能，建議較便宜的儲存體設定[SAP Hana Azure 虛擬機器儲存體](./hana-vm-operations-storage.md)設定 
- 06/24/2020：在 Azure 中的 [SLES 上設定 Pacemaker](./high-availability-guide-suse-pacemaker.md) ，以針對以 Azure 隔離代理程式為基礎的裝置發行新改良的 Azure 隔離代理程式和更具復原能力的 STONITH 設定 
- 06/24/2020：在 [Azure 中設定 RHEL 的 Pacemaker](./high-availability-guide-rhel-pacemaker.md) ，以發行更具復原能力的 STONITH 設定
- 06/23/2020： [Sap NetWeaver 的 Azure 虛擬機器規劃和實行](./planning-guide.md) 變更指南，以及 [sap 工作負載的 Azure 儲存體類型](./planning-guide-storage.md) 簡介指南
- 06/22/2020：將適用于 SAP 的新 VM 擴充功能的安裝步驟新增至 [部署指南](deployment-guide.md)
- 06/16/2020： [在 SAP HA 案例中使用 Azure 標準 ILB 來變更 vm 的公用端點連線能力](./high-availability-guide-standard-load-balancer-outbound-connections.md) ，以新增 SUSE 公用雲端基礎結構101檔的連結 
- 06/10/2020：將新的！ Sku 新增至 [適用于紐約的可用 sku](./hana-available-skus.md) 和 [SAP Hana (大型實例) 儲存體架構](./hana-storage-architecture.md)
- 5月 21 2020：在[azure 中的 SLES 上設定 Pacemaker](./high-availability-guide-suse-pacemaker.md)的變更，以及在[AZURE 中設定 RHEL](./high-availability-guide-rhel-pacemaker.md)上的 PACEMAKER，以[在 SAP HA 案例中使用 Azure 標準 ILB 新增 vm 的公用端點](./high-availability-guide-standard-load-balancer-outbound-connections.md)連線連結  
- 5月 19 2020：在[SAP Hana Azure 虛擬機器儲存體](./hana-vm-operations-storage.md)設定中使用 HANA 相關磁片區的 LVM 時，請新增重要訊息，不要使用根磁片區群組
- 5月 19 2020：在[適用于 Hana 大型實例的相容作業系統](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)中，為 Hana 大型實例類型 II 新增支援的 OS
- 5月 12 2020： [使用 SAP HA 案例中的 Azure 標準 ILB 來變更 vm 的公用端點連線能力](./high-availability-guide-standard-load-balancer-outbound-connections.md) ，以更新連結並新增協力廠商防火牆設定的資訊
- 5月 11 2020：在 [SLES 上的 Azure vm 上 SAP Hana 的高可用性](./sap-hana-high-availability.md) 變更，以將 netcat 資源的資源內容設定為0，這會導致更簡化的容錯移轉 
- 5月 05 2020： [Azure 虛擬機器規劃和執行的變更，適用于 SAP NetWeaver](./planning-guide.md) 以表示 Gen2 部署適用于 Mv1 VM 系列
- 24 2020 年4月：在具有 [ANF ON sles 的 Azure vm 上使用待命節點進行 SAP Hana 相應](./sap-hana-scale-out-standby-netapp-files-suse.md)放大的變更，在 [SAP Hana 向外擴充 azure vm 上的待命節點](./sap-hana-scale-out-standby-netapp-files-rhel.md)（使用 azure vm 上的 ANF）、azure vm 上的 azure vm 上的 azure vm 上的 Azure vm 上的 azure vm [高可用性](./high-availability-guide-suse-netapp-files.md) ，以及磁片區的 azure vm 上的 [sap](./high-availability-guide-rhel-netapp-files.md) NetWeaver
- 22 2020 年4月：變更 [SLES 上的 Azure vm 上 SAP Hana 的高可用性](./sap-hana-high-availability.md) ，以移除指示中的中繼屬性 `is-managed` ，因為它與將叢集放入或移出維護模式發生衝突
- 21 2020 年4月：已將 SQL Azure DB 新增為 SAP (Hybris) Commerce Platform 1811 和更新版本中的支援的 DBMS，以[瞭解 Azure 部署支援的 sap 軟體](./sap-supported-product-on-azure.md)，以及[在 Microsoft Azure 上執行的 sap 認證和](./sap-certifications.md)設定
- 16 2020 年4月：已將 SAP Hana 新增為 SAP (Hybris) Commerce 平臺的支援 DBMS 文章中的[Azure 部署支援的 sap 軟體](./sap-supported-product-on-azure.md)，以及[在 Microsoft Azure 上執行的 sap 認證和](./sap-certifications.md)設定。
- 13 2020 年4月：適用于 sap [Ase Azure 虛擬機器 DBMS 部署中 sap 工作負載的](./dbms_guide_sapase.md)精確 sap ase 版本號碼
- 07 2020 年4月：在 [azure 中的 SLES 上設定 Pacemaker](./high-availability-guide-suse-pacemaker.md) 的變更，以闡明雲端 netconfig-Azure 指示
- 06 2020 年4月：在 azure Vm 上使用 azure NetApp Files 的 azure Vm 上的 [待命節點進行 SAP Hana 相應](./sap-hana-scale-out-standby-netapp-files-suse.md) 放大的變更，並 [SAP Hana 在 azure vm](./sap-hana-scale-out-standby-netapp-files-rhel.md) 上使用 azure Netapp files 的 azure vm 上的待命節點來移除對 NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) 的參考，並以 [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf)) 取代 (
- 2020年3月31日：在 Azure vm 上的 SLES 和[高 SAP Hana 可用性](./sap-hana-high-availability-rhel.md)上變更[azure vm 上的 SAP Hana 高可用性](./sap-hana-high-availability.md)，以新增在建立等量磁片區時如何指定等量大小的指示
- 2020年3月27日：在 SLES 上的 [Azure vm 上，使用 ANF FOR sap 應用程式變更 SAP NW 的高可用性](./high-availability-guide-suse-netapp-files.md) ，以將檔案系統掛接選項對應至 NetApp TR-4746 (移除同步裝載選項) 
- 2020年3月26日：在 [Azure vm 上的 SAP NetWeaver 變更高可用性（SLES 多重 SID 指南](./high-availability-guide-suse-multi-sid.md) ），以新增對 NetApp TR-4746 的參考
- 2020年3月26日： [sles FOR sap 應用程式上的 Sap NetWeaver 在 Azure vm 上的高可用性](./high-availability-guide-suse.md)變更、在 sles 上的 azure vm 上的 [sap NetWeaver 的高可用性，以及適用于 sap 應用程式的 azure NetApp Files](./high-availability-guide-suse-netapp-files.md)適用于 Azure Vm 上的 [azure vm 上的 azure](./high-availability-guide-suse-nfs.md)vm 高可用性（適用于 rhel [多重 SID 指南的](./high-availability-guide-suse-multi-sid.md)azure vm 上的 sap NETWEAVER）、適用于 Rhel [for sap 應用程式](./high-availability-guide-rhel.md) 之 SAP NetWeaver 的高可用性，以及適用于 sap [應用程式之 azure NetApp Files 的](./high-availability-guide-rhel-netapp-files.md) azure vm 上的 sap NetWeaver 的高可用性 Azure Load Balancer
- 2020年3月19日：檔快速入門的主要修訂 [：在 Azure 虛擬機器上手動安裝單一實例 SAP Hana](./hana-get-started.md) ，以 [在 azure 虛擬機器上安裝 SAP Hana](./hana-get-started.md)
- 2020年3月17日：在 [Azure SUSE Linux Enterprise Server 上設定 Pacemaker](./high-availability-guide-suse-pacemaker.md) 以移除不再需要的 SBD 設定設定
- 16 2020 年3月： [Azure 部署支援哪些 SAP 軟體](./sap-supported-product-on-azure.md)的 SAP Hana IaaS 認證平臺中的資料行憑證案例澄清
- 2020/03/11：在 [Azure 虛擬機器支援案例上的 SAP 工作負載](./sap-planning-supported-configurations.md) \(部分機器翻譯\) 中進行變更，以釐清每個 DBMS 執行個體使用多個資料庫的支援
- 2020年3月11日： [適用于 SAP NetWeaver 的 Azure 虛擬機器規劃和執行](./planning-guide.md) 變更，說明第1代和第2代 vm
- 2020年3月10日： [SAP Hana Azure 虛擬機器儲存體](./hana-vm-operations-storage.md) 設定中的變更，以闡明 ANF 的實際現有輸送量限制
- 2020年3月09日： azure [vm 上的 Sap NetWeaver 的高可用性變更在 sap 應用程式的 SUSE Linux Enterprise Server](./high-availability-guide-suse.md)上，azure [VM 上的 sap NetWeaver 在 azure vm SUSE Linux Enterprise Server 上的高可用性，適用于 sap 應用程式的 Azure NetApp Files](./high-availability-guide-suse-netapp-files.md) [azure vm 上的 NFS 高可用性 SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)、在[azure SUSE Linux Enterprise Server 上設定 Pacemaker](./high-availability-guide-suse-pacemaker.md)、在 azure vm 上的 azure [vm 上](./dbms-guide-ha-ibm.md)[設定高](./sap-hana-high-availability.md)可用性、azure vm 上的 Pacemaker、高可用性、azure vm 上的、高可用性、azure vm 上的 SAP NetWeaver、 [azure vm](./high-availability-guide-suse-multi-sid.md)上的、高可用性、使用資源代理程式來更新叢集資源-SUSE Linux Enterprise Server lb SUSE Linux Enterprise Server SAP Hana 
- 2020年3月5日： azure 虛擬機器中的 Azure 區域和 Azure 虛擬機器的結構變更和內容變更， [適用于 SAP NetWeaver 的 Azure 虛擬機器規劃和實行](./planning-guide.md)
- 2020/03/03：在[於 SLES for SAP 應用程式上使用 ANF 以在 Azure VM 上取得 SAP NW 高可用性](./high-availability-guide-suse-netapp-files.md) \(部分機器翻譯\) 中進行變更，以變更為更加有效的 ANF 磁碟區配置
- 2020年3月01日：修改 [Azure 虛擬機器上 SAP Hana 的備份指南](./sap-hana-backup-guide.md) ，以包含 Azure 備份服務。 已減少並濃縮[檔案層級的 SAP HANA Azure 備份](./sap-hana-backup-file-level.md) \(部分機器翻譯\) 中的內容，並刪除關於透過磁碟快照集進行備份的第三份文件。 該內容已於《Azure 虛擬機器上的 SAP HANA 備份指南》中處理 
- 2020年2月27日：在[sles FOR sap 應用程式上，Azure vm 上的 SAP nw](./high-availability-guide-suse.md)有高可用性，在 sles 上的 azure vm 上[使用 ANF for sap 應用程式](./high-availability-guide-suse-netapp-files.md)的高可用性和[Azure vm 上的 sap NetWeaver 高可用性](./high-availability-guide-suse-multi-sid.md)
- 2020年2月26日：變更 [SAP Hana azure 虛擬機器儲存體](./hana-vm-operations-storage.md) 設定，以說明 AZURE 上 HANA 的檔案系統選擇
- 2020年2月26日： [sap 的高可用性架構和案例](./sap-high-availability-architecture-scenarios.md) 中的變更，以包含 RHEL 多重 SID 上 Azure vm 上的 HA For sap NetWeaver 連結指南
- 2020年2月26日：在 [sles FOR sap 應用程式上，Azure vm 上的 SAP nw](./high-availability-guide-suse.md)有高可用性，在 [具有 ANF for sap 應用程式的 sles](./high-availability-guide-suse-netapp-files.md)上的 azure VM 上使用 sap nw 的高可用性，在 [rhel 和](./high-availability-guide-rhel.md) azure vm 上的 sap NetWeaver 的高可用性，可 [使用 AZURE NETAPP Files](./high-availability-guide-rhel-netapp-files.md) 移除不支援多重 SID ASCS/ERS 叢集的語句
- 2020年2月26日：  [在 RHEL 多重 sid 指南的 Azure vm 上的 SAP NetWeaver 推出高可用性](./high-availability-guide-rhel-multi-sid.md) ，以新增 SUSE 多重 sid 叢集指南的連結
- 2020/02/25：在 [SAP 的高可用性架構和案例](./sap-high-availability-architecture-scenarios.md) \(部分機器翻譯\) 中進行變更，以新增較新 HA 文章的連結
- 2020年2月25日： [SUSE Linux Enterprise Server 上的 Azure vm 上的 IBM DB2 LUW 的高可用性](./dbms-guide-ha-ibm.md) 變更為 Pacemaker，指向說明使用標準 Azure 負載平衡器存取公用端點的檔
- 2020年2月21日：[適用于 sap 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](./dbms_guide_sapase.md)文章的完整修訂
- 2020年2月21日： [SAP Hana Azure 虛擬機器儲存體](./hana-vm-operations-storage.md) 設定中的變更，以代表/hana/data 和新增 i/o 排程器設定的新建議
- 2020年2月21日： HANA 大型實例檔中的變更，以代表 S224 和 S224m 的新認證 Sku
- 2020年2月21日：在 rhel 上的 SAP NetWeaver 和[Azure vm 高](./high-availability-guide-rhel-netapp-files.md)可用性的 azure [Vm 高可用性](./high-availability-guide-rhel.md)變更 azure NETAPP Files 上的 sap NetWeaver，以調整排入佇列伺服器複寫2架構的叢集限制 (ENSA2) 
- 2020年2月20日：在 [Azure vm 上的 SAP NetWeaver 變更高可用性（SLES 多重 sid 指南](./high-availability-guide-suse-multi-sid.md) ），以新增 SUSE 多重 sid 叢集指南的連結
- 2020年2月13日： [適用于 SAP NetWeaver 的 Azure 虛擬機器規劃和執行](./planning-guide.md) 變更，以執行新檔的連結
- 2020年2月13日：[在 Azure 虛擬機器支援的案例中新增了 SAP 工作負載的](./sap-planning-supported-configurations.md)新檔
- 2020年2月13日：新增[了 Azure 部署支援哪些 SAP 軟體的](./sap-supported-product-on-azure.md)新檔
- 2020年2月13日：在 [Red Hat Enterprise Linux Server 上的 Azure vm 上，將 IBM DB2 LUW 的高可用性](./high-availability-guide-rhel-ibm-db2-luw.md) 變更為指向檔，以使用標準的 Azure 負載平衡器來說明公用端點的存取權
- 2020年2月13日：將新的 VM 類型新增至[在 Microsoft Azure 上執行的 SAP 認證和](./sap-certifications.md)設定
- 2020年2月13日：在 Azure 上新增 sap 支援附注 [sap 工作負載：規劃和部署檢查清單](./sap-deployment-checklist.md)
- 2020年2月13日：在 rhel 和 Azure Vm [上的 Sap NetWeaver 高](./high-availability-guide-rhel.md) 可用性變更 azure vm 高可用性，以搭配 [azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) 將叢集資源的超時時間與 Red Hat timeout 建議保持一致
- 2020年2月11日： [Azure 大型實例遷移至 Azure 虛擬機器上的 SAP Hana](./hana-large-instance-virtual-machine-migration.md)版本
- 2020年2月07日： [在 SAP HA 案例中使用 Azure 標準 ILB 來變更 vm 的公用端點連線能力](./high-availability-guide-standard-load-balancer-outbound-connections.md) ，以更新範例 NSG 螢幕擷取畫面
- 2020年2月3日：在 sles [FOR sap 應用程式上的 Azure vm 上的 SAP Nw 變更高可用性](./high-availability-guide-suse.md) ，以及在 sles 上的 [azure VM 上使用 ANF for sap 應用程式的高可用性](./high-availability-guide-suse-netapp-files.md) ，以移除在 sles 上叢集節點的主機名稱中使用破折號的警告
- 2020年1月28日：在 [RHEL 上變更 Azure vm 上 SAP Hana 的高可用性](./sap-hana-high-availability-rhel.md) ，以將 SAP Hana 叢集資源超時與 Red Hat timeout 建議保持一致
- 2020年1月17日： [Azure 鄰近放置群組中的變更，可讓 SAP 應用程式的最佳網路延遲](./sap-proximity-placement-scenarios.md) 變更將現有 vm 移至鄰近放置群組的區段
- 2020年1月17日：變更 [SAP 工作負載](./sap-ha-availability-zones.md) 設定中的 Azure 可用性區域，以指向將可用性區域之間的延遲測量自動化的程式
- 2020年1月16日： [如何在 Azure 上安裝和設定 SAP Hana (大型) 實例](./hana-installation.md) 的變更，以將作業系統版本調整為 HANA IaaS 硬體目錄
- 2020年1月16日： [Azure vm 上的 Sap NetWeaver 在 SLES 多重 SID 指南上的高可用性](./high-availability-guide-suse-multi-sid.md) 變更，以使用佇列伺服器2架構來新增適用于 sap 系統的指示， (ENSA2) 
- 2020年1月10日：在 Azure Vm 上使用 azure NetApp Files 的 azure Vm 上的 [待命節點進行 SAP Hana 相應](./sap-hana-scale-out-standby-netapp-files-suse.md) 放大的變更，並 SAP Hana 在 azure vm 上使用 Azure [netapp Files 的 azure vm 上的待命節點](./sap-hana-scale-out-standby-netapp-files-rhel.md) ，以新增如何進行 `nfs4_disable_idmapping` 永久變更的指示。
- 2020年1月10日： [SLES 上的 Azure vm 上的 Sap NetWeaver 有高可用性的](./high-availability-guide-suse-netapp-files.md) 變更，以及適用于 sap 應用程式之 azure netapp files 的 Azure [虛擬機器中 sap NetWeaver 的高可用性](./high-availability-guide-rhel-netapp-files.md) ，以新增如何掛接 azure netapp files NFSv4 磁片區的指示。
- 2019 年 12 月 23 日：發佈[SLES 上之 Azure VM 上的 SAP NetWeaver 高可用性多 SID 指南](./high-availability-guide-suse-multi-sid.md) \(部分機器翻譯\)
- 2019 年 12 月 18 日：發佈[於 RHEL 上使用 Azure NetApp Files 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](./sap-hana-scale-out-standby-netapp-files-rhel.md) \(部分機器翻譯\)
- 2019 年 11 月 21 日：在[於 SUSE Linux Enterprise Server 上使用 Azure NetApp Files 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](./sap-hana-scale-out-standby-netapp-files-suse.md) \(部分機器翻譯\) 中進行變更，以簡化 NFS 識別碼對應的設定，以及變更建議的主要網路介面以簡化路由。
- 2019 年 11 月 15 日：在[於 SUSE Linux Enterprise Server for SAP Applications 上使用 Azure NetApp Files 以取得 SAP NetWeaver 高可用性](high-availability-guide-suse-netapp-files.md)和[於 Red Hat Enterprise Linux for SAP Applications 上使用 Azure NetApp Files 以取得 SAP NetWeaver 高可用性](high-availability-guide-rhel-netapp-files.md)中進行次要變更，以釐清容量集區大小限制，並移除僅支援 NFSv3 版本的陳述。
- 2019 年 11 月 12 日：發佈[於 Windows 上使用 Azure NetApp Files (SMB) 取得 SAP NetWeaver 高可用性](high-availability-guide-windows-netapp-files-smb.md)
- 2019 年 11 月 8 日：在 [SUSE Linux Enterprise Server 上之 Azure VM 上的 SAP HANA 高可用性](sap-hana-high-availability.md)、[在 Azure 虛擬機器 (VM) 上設定 SAP HANA 系統複寫](sap-hana-high-availability-rhel.md)、[於 SUSE Linux Enterprise Server for SAP Applications 上針對 SAP NetWeaver 取得 Azure 虛擬機器高可用性](high-availability-guide-suse.md)、[於 SUSE Linux Enterprise Server 上使用 Azure NetApp Files 以針對 SAP NetWeaver 取得 Azure 虛擬機器高可用性](high-availability-guide-suse-netapp-files.md)、[Red Hat Enterprise Linux 上適用於 SAP NetWeaver 的 Azure 虛擬機器高可用性](high-availability-guide-rhel.md)、[於 Red Hat Enterprise Linux 上使用 Azure NetApp Files 針對 SAP NetWeaver 取得 Azure 虛擬機器高可用性](high-availability-guide-rhel-netapp-files.md)、[SUSE Linux Enterprise Server 上之 Azure VM 上的 NFS 高可用性](high-availability-guide-suse-nfs.md)、[Red Hat Enterprise Linux 上之 Azure VM 上適用於 SAP NetWeaver 的 GlusterFS](high-availability-guide-rhel-glusterfs.md) 中進行變更，以建議 Azure 標準負載平衡器  
- 2019 年 11 月 8 日：在 [SAP 工作負載規劃和部署檢查清單](sap-deployment-checklist.md)中進行變更，以釐清加密建議  
- 2019 年 11 月 4 日：於[在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker](high-availability-guide-suse-pacemaker.md) 中進行變更，以直接使用單點傳播設定來建立叢集  
