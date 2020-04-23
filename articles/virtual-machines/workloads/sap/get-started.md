---
title: 開始使用 Azure 上的 SAP Vm |Microsoft Docs
description: 瞭解在 Microsoft Azure 的虛擬機器（Vm）上執行的 SAP 解決方案
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
ms.date: 04/22/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fe7b375ebafb1e8194b29061430d956e48f2c441
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100749"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 來裝載和執行 SAP 工作負載案例

當您使用 Microsoft Azure 時，您可以在可調整、符合規範且經企業證明的平臺上，可靠地執行您的任務關鍵性 SAP 工作負載和案例。 您可以獲得 Azure 的擴充性、彈性和成本節約。 透過 Microsoft 與 SAP 的擴充合作關係，您可以在 Azure 中的開發和測試和生產案例中執行 SAP 應用程式，並受到完整支援。 從 SAP NetWeaver 到 SAP S/4HANA、從 Linux 到 Windows 的 SAP BI，以及 SAP Hana 到 SQL，我們都已涵蓋在內。

除了在 Azure 上裝載具有不同 DBMS 的 SAP NetWeaver 案例，您可以裝載其他 SAP 工作負載案例，例如 Azure 上的 SAP BI。 

適用于 SAP Hana 的 Azure 唯一性是設定 Azure 的供應專案。 為了能夠裝載包含 SAP Hana 的記憶體和 CPU 資源需求較高的 SAP 案例，Azure 提供了客戶專用的裸機硬體使用。 使用此解決方案可針對 S/4HANA 或其他 SAP Hana 的工作負載，執行需要最多 24 TB （120 TB 向外延展）記憶體的 SAP Hana 部署。 

在 Azure 中裝載 SAP 工作負載案例也可以建立身分識別整合和單一登入的需求。 當您使用 Azure Active Directory （Azure AD）連接不同的 SAP 元件和 SAP 軟體即服務（SaaS）或平臺即服務（PaaS）供應專案時，就會發生這種情況。 「AAD SAP 身分識別整合和單一登入」一節會描述並記載一份這類整合和單一登入案例的清單，其中包含 Azure AD 和 SAP 實體。

## <a name="changes-to-the-sap-workload-section"></a>SAP 工作負載區段的變更
Azure 上的 SAP 工作負載區段中的檔變更會列在本文結尾。 變更記錄檔中的專案會保留大約180天。

## <a name="you-want-to-know"></a>您想要知道
如果您有特定問題，我們會將您指向 [起始頁] 區段中的特定檔或流程。 您想要知道：

- SAP 軟體版本和哪些作業系統版本支援哪些 Azure Vm 和 HANA 大型實例單位。 閱讀檔[Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)來取得解答和尋找資訊的流程
- Azure Vm 和 HANA 大型實例支援哪些 SAP 部署案例。 您可以在檔中找到支援案例的相關資訊：
    - [Azure 虛擬機器支援案例上的 SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [HANA 大型實例的支援案例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- 不同 Azure 區域中有哪些 Azure 服務、Azure VM 類型和 Azure 儲存體服務可供使用，請查看[依區域提供](https://azure.microsoft.com/global-infrastructure/services/)的網站產品 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體)

一系列的檔會引導您完成 Azure （大型實例）或簡短的 HANA 大型實例的 SAP Hana。 如需有關 HANA 大型實例的詳細資訊，請從[Azure （大型實例）上的 SAP Hana 檔總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)開始著手，並流覽 HANA 大型實例一節中的相關檔



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP Hana
文件的本節涵蓋 SAP HANA 的不同層面。 必要條件是，您應該熟悉提供 Azure IaaS 基本服務的 Azure 主要服務。 因此，您需要 Azure 計算、儲存體和網路的知識。 這些主題中有許多都是在 SAP NetWeaver 相關的[Azure 規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中處理。 

如需有關 Azure 上 HANA 的詳細資訊，請參閱下列文章及其 subarticles：

- [SAP HANA 在 Azure 上的基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虛擬機器的 SAP Hana 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 虛擬機器上 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虛擬機器上的 SAP Hana 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>部署在 Azure 虛擬機器上的 SAP NetWeaver
本節列出 Azure 上 SAP NetWeaver 和 Business One 的規劃和部署檔。 本檔著重于在 Azure 上使用非 HANA 資料庫與 SAP 工作負載的基本概念。 適用于高可用性的檔和文章也是 Azure 中 HANA 高可用性的基礎，例如：

- [Azure 規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。 
- [Azure 虛擬機器上的 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [使用 Site Recovery 保護多層式 SAP NetWeaver 應用程式部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [適用於 Azure 的 SAP LaMa 連接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

如需 Azure 上 SAP 工作負載下非 HANA 資料庫的詳細資訊，請參閱：

- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [適用於 SAP NetWeaver 的 SQL Server Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure Vm 上的 SAP MaxDB、Live Cache 和 Content Server 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

如需有關 Azure 上 SAP Hana 資料庫的詳細資訊，請參閱 Azure 虛擬機器上的「SAP Hana」一節。

如需有關 Azure 上 SAP 工作負載的高可用性資訊，請參閱：

- [SAP NetWeaver 的 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

本檔指向各種不同的架構和案例檔。 在稍後的案例檔中，會提供詳細技術檔的連結，說明如何部署和設定不同的高可用性方法。 說明如何為 SAP NetWeaver 工作負載建立和設定高可用性的不同檔涵蓋 Linux 和 Windows 作業系統。


如需 Azure Active Directory （Azure AD）與 SAP 服務與單一登入之間整合的相關資訊，請參閱：

- [教學課程：Azure Active Directory 與 SAP Cloud for Customer 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Cloud Platform Identity Authentication 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP 雲端平台整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP NetWeaver 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Business ByDesign 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP HANA 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [您的 S/4HANA 環境：使用 Azure AD 的 Fiori 啟動列 SAML 單一登入](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

如需將 Azure 服務整合到 SAP 元件的相關資訊，請參閱：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 連接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory 提供 SAP HANA 和 Business Warehouse 資料整合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>變更記錄

- 04/22/2020：在[SLES 上的 Azure vm 上 SAP Hana 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)變更，以從`is-managed`指示中移除中繼屬性，因為它與將叢集置於或離開維護模式相衝突
- 04/21/2020：已將 SQL Azure DB 新增為 SAP （Hybris） Commerce Platform 1811 的支援 DBMS，並在文章中加入[了哪些 sap 軟體支援用於 Azure 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)，以及在[Microsoft Azure 上執行的 sap 認證和](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)設定
- 04/16/2020：已將 SAP Hana 新增為 SAP （Hybris）商務平臺的支援 DBMS，文章中的[Azure 部署支援的 sap 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)，以及[Microsoft Azure 上執行的 sap 認證和](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)設定
- 04/13/2020：更正 sap ASE 中針對 sap 工作負載的確切 SAP ASE 版本號碼[Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 04/07/2020：在 Azure 中的[SLES 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)的變更，以澄清雲端 netconfig-Azure 指示
- 04/06/2020： SAP Hana 向外延展的變更，其中包含 SLES 上的[Azure Netapp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)和在 azure vm 上具有 Azure netapp [files 的 SAP Hana 向外](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)延展中的待命節點，以移除對 NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf)的參考（由[TR-4746](https://www.netapp.com/us/media/tr-4746.pdf)取代）
- 03/31/2020：在 RHEL 上以 azure vm 上的 SLES 和[高可用性 SAP Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)在 azure vm 上[SAP Hana 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)變更，以新增在建立等量磁片區時指定等量大小的指示
- 03/27/2020：[使用及 FOR sap applications 在 SLES 上的 Azure vm 上變更 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)，以將檔案系統掛接選項對應至 NetApp TR-4746 （移除同步掛接選項）
- 03/26/2020： [SLES 多重 SID 指南上的 SAP NetWeaver 在 Azure vm 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)變更，以新增對 NetApp TR-4746 的參考
- 03/26/2020：在 sles [FOR sap 應用程式的 Azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)上，sap NetWeaver 的高可用性變更，在[sles 上使用適用于 sap 應用程式的 azure NetApp Files 的 azure VM 上的 sap NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)Sles[上的 azure vm 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)、azure vm 上的[sap NetWeaver 高可用性-](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)適用于 sap 的 azure vm 上的 sap [NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)在 rhel 上的 azure 虛擬機器上[使用 sap 應用程式](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)來更新圖表和明確說明 Azure Load Balancer 後端集區建立
- 03/19/2020：檔的主要修訂版[快速入門：在 azure 上手動安裝單一實例 SAP Hana 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)在[azure 上安裝 SAP Hana 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020：在[Azure 中的 SUSE Linux Enterprise Server 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)的變更，以移除不再需要的 SBD configuration 設定
- 03/16/2020：在[適用于 Azure 部署的 SAP 軟體支援](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)的 SAP Hana IaaS 認證平臺中，對資料行認證案例的澄清
- 03/11/2020：在[Azure 虛擬機器支援的案例中變更 SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)，以明確說明每個 DBMS 實例支援的多個資料庫
- 03/11/2020： [Azure 虛擬機器規劃和實作為 SAP NetWeaver 的變更，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)說明第1代和第2代 vm
- 03/10/2020： [SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中的變更，以澄清及的真正現有輸送量限制
- 03/09/2020：在適用于 sap 應用程式的[SUSE Linux Enterprise Server 上的 Azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)上，sap NetWeaver 的高可用性變更、azure vm 上的 sap [NetWeaver 高可用性（含適用于 sap 應用程式的 azure NetApp Files） SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)SUSE Linux Enterprise Server 上[的 Azure vm 上的 NFS 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)、在 azure 的[SUSE Linux Enterprise Server 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)、SUSE LINUX ENTERPRISE SERVER 上的 azure vm 上的 SAP Db2 [LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)，以及 azure vm 上的 Pacemaker、高可用性、在 azure vm 上 SUSE Linux Enterprise Server[和](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)高[可用性，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)以及使用資源代理程式 azure-lb 更新叢集資源 SAP Hana 
- 03/05/2020： Azure 中適用于 SAP NetWeaver 的 azure 區域和 Azure 虛擬機器的結構變更和內容變更[虛擬機器規劃和執行](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020：在[SLES 上使用及 FOR sap applications 變更 SAP NW 在 Azure vm 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)，以進行更有效率的及磁片區版面配置
- 03/01/2020：已改編[Azure 虛擬機器上 SAP Hana 的備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)，以包含 Azure 備份服務。 減少和壓縮檔案[層級 SAP Hana Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)中的內容，並刪除第三份檔，處理透過磁片快照集的備份。 Azure 虛擬機器上 SAP Hana 的備份指南中會處理內容 
- 02/27/2020： sles [FOR sap 應用程式的 Azure vm 上的 SAP nw 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變更，azure vm 上的 sap [nw 高可用性（含及 for sap Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) ）和[Azure Vm 上的 sap NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 02/26/2020： [SAP Hana azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中的變更，以澄清 AZURE 上 HANA 的檔案系統選擇
- 02/26/2020：適用于[sap 的高可用性架構和案例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)變更在 RHEL 多 SID 指南的 Azure vm 上包含 HA For sap NetWeaver 的連結
- 02/26/2020： [sles FOR sap 應用程式的 Azure 虛擬機器上的 SAP nw 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變更、在[SLES 上使用及 for sap applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)的 azure VM 上的 sap nw 高可用性、 [azure VM 高可用性適用于 rhel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)上的 sap NetWeaver，以及 Azure Vm 高可用性（[使用 azure NetApp FILES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) ）來移除不支援多重 SID ASCS/ERS 叢集的語句
- 02/26/2020：[在 RHEL 多 sid 指南上，于 Azure vm 上使用 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)版本，以新增 SUSE 多重 sid 叢集指南的連結
- 02/25/2020：[高可用性架構和 SAP 案例的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)變更，可將連結新增至較新的 HA 文章
- 02/25/2020： [SUSE Linux Enterprise Server 上的 Azure vm 上的 IBM DB2 LUW 與 Pacemaker 之間的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)變更，以指向說明使用標準 Azure 負載平衡器存取公用端點的檔
- 02/21/2020：[適用于 sap 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)文章的完整修訂
- 02/21/2020： [SAP Hana Azure 虛擬機器存放裝置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中的變更，以代表新建議的等量大小以/hana/data 和新增 i/o 排程器的設定
- 02/21/2020： HANA 大型實例檔中的變更，代表 S224 和 S224m 的新認證 Sku
- 02/21/2020：在 RHEL 和 Azure Vm[上的 Sap NetWeaver 高](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)可用性變更 azure vm 高可用性適用于[RHEL 上的 Sap NetWeaver 與 azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) ，以調整佇列伺服器複寫2架構（ENSA2）的叢集條件約束
- 02/20/2020： [SLES 多 sid 指南上的 SAP NetWeaver 在 Azure vm 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)變更新增了 SUSE 多重 sid 叢集指南的連結
- 02/13/2020： [Azure 虛擬機器規劃和實作為 SAP NetWeaver 的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)變更，以執行新檔的連結
- 02/13/2020：已[在 Azure 虛擬機器支援的案例中新增 SAP 工作負載的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)新檔
- 02/13/2020：已新增新檔[Azure 部署支援的 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020： [Red Hat Enterprise Linux 伺服器上的 Azure vm 上的 IBM DB2 LUW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)變更為指向說明使用標準 Azure 負載平衡器存取公用端點的檔
- 02/13/2020：將新的 VM 類型新增至[在 Microsoft Azure 上執行的 SAP 認證和](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)設定
- 02/13/2020：將新的 SAP 支援附注新增至[Azure 上的 sap 工作負載：規劃和部署檢查清單](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020： [Azure vm 中的 Sap NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)變更在 Rhel 和[azure Vm 上使用 azure NETAPP Files 的 sap NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)，使叢集資源的超時時間與 Red Hat 的超時建議相符
- 02/11/2020：從[Azure 大型實例遷移至 azure 虛擬機器的 SAP Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)版本
- 02/07/2020：[在 SAP HA 案例中使用 Azure 標準 ILB 來變更 vm 的公用端點連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)，以更新範例 NSG 螢幕擷取畫面
- 02/03/2020：在 sles [FOR sap 應用程式的 Azure vm 上，SAP nw 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變更和 azure vm 上的 sap nw 高[可用性（含及 for sap applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) ）可移除在 sles 上叢集節點的主機名稱中使用破折號的警告
- 01/28/2020：在[RHEL 上的 Azure vm 上 SAP Hana 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)變更，使 SAP Hana 叢集資源的超時時間與 Red Hat 的建議保持一致
- 01/17/2020：在[Azure 鄰近放置群組中變更 SAP 應用程式的最佳網路延遲](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，以變更將現有 vm 移至鄰近位置群組的區段
- 01/17/2020： Azure 可用性區域的[SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)設定變更為指向可自動測量可用性區域之間延遲的程式
- 01/16/2020：變更[如何在 Azure 上安裝和設定 SAP Hana （大型實例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)以調整 OS 版本到 HANA IaaS 硬體目錄
- 01/16/2020： [SLES 多重 SID 指南上的 Sap NetWeaver 在 Azure vm 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)變更使用排入佇列伺服器2架構（ENSA2）新增 sap 系統的指示
- 01/10/2020： SAP Hana 向外延展的變更，其中包含 SLES 上的[Azure Netapp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)和在 azure vm 上具有 Azure [netapp files 的 SAP Hana 向外](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)延展中的待命節點，以新增有關如何進行`nfs4_disable_idmapping`永久變更的指示。
- 01/10/2020：在[SLES 上使用適用于 sap 應用程式的 Azure Netapp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)和 azure 中的 sap NetWeaver 的高可用性變更（使用適用于 sap 應用程式的 azure netapp files）在 RHEL 上的 sap [NetWeaver 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)，以新增如何掛接 azure netapp files NFSv4 磁片區的指示。
- 12/23/2019： [SLES 多 SID 指南的 Azure vm 上的 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)版本
- 12/18/2019：使用 RHEL 上的[Azure NetApp Files 在 Azure vm 上以待命節點發行 SAP Hana 相應](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)放大
- 11/21/2019：在[SUSE Linux Enterprise Server 上使用 Azure NetApp Files 的 Azure vm 上的待命節點 SAP Hana 相應](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)放大的變更，以簡化 NFS 識別碼對應的設定，並變更建議的主要網路介面以簡化路由。
- 11/15/2019： [SUSE Linux Enterprise Server 上的 Sap NetWeaver 高可用性](high-availability-guide-suse-netapp-files.md)的次要變更，其中包含適用于 sap 應用程式的 Azure netapp files 和[RED HAT ENTERPRISE LINUX 上的 Sap NetWeaver 的高可用性，以及適用于 sap 應用程式的 azure netapp files](high-availability-guide-rhel-netapp-files.md) ，以清楚瞭解容量集區大小限制，以及僅支援 NFSv3 版本的 remove 語句。
- 11/12/2019：[在 Windows 上使用 Azure NetApp Files （SMB）發行 SAP NetWeaver 的高可用性](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019： [SUSE Linux Enterprise Server 上的 Azure vm 上 SAP Hana 的高可用性](sap-hana-high-availability.md)變更，[在 azure 虛擬機器（vm）上設定 SAP Hana 系統](sap-hana-high-availability-rhel.md)複寫[azure 虛擬機器在 sap 應用程式 SUSE Linux Enterprise Server 上的 sap NetWeaver 高可用性](high-availability-guide-suse.md)、azure[虛擬機器 Azure netapp files SUSE Linux Enterprise Server 上的 sap NetWeaver 高](high-availability-guide-suse-netapp-files.md)可用性、azure[虛擬機器](high-availability-guide-rhel.md)在 Red Hat Enterprise Linux 上的 sap NetWeaver 高可用性、azure 虛擬機器高可用性 Red Hat Enterprise Linux 上的 sap [NetWeaver](high-availability-guide-rhel-netapp-files.md)、適用于 Sap GlusterFS 的 azure vm[上](high-availability-guide-rhel-glusterfs.md)的[高可用性，](high-availability-guide-suse-nfs.md)以建議 azure 標準負載平衡器  
- 11/08/2019： [SAP 工作負載規劃和部署檢查清單](sap-deployment-checklist.md)中的變更，以闡明加密建議  
- 11/04/2019：在[Azure 中的 SUSE Linux Enterprise Server 上設定 Pacemaker](high-availability-guide-suse-pacemaker.md) ，以直接使用單播設定建立叢集的變更  
- 10/29/2019：[在 SAP 高可用性案例中使用 Azure Standard Load Balancer 虛擬機器的公用端點連線能力](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019： [SAP Hana azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定和 SAP Hana 使用 azure vm 上的待命節點向外延展的變更[SUSE Linux Enterprise Server 上的 azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) ，以澄清/HANA/SHARED 磁片區的 NFS 通訊協定
- 10/22/2019： NetWeaver [sap 應用程式的 SUSE Linux Enterprise Server 上的 Azure vm 上的 Sap 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變更 SUSE Linux Enterprise Server 上的[azure VM 上的 sap NetWeaver 的高可用性，以及適用于 sap 應用程式的 azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)、 [SUSE Linux Enterprise Server 上的 azure vm 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)、在 azure 上設定 Pacemaker、在 azure 的[SUSE Linux Enterprise Server 上設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)、azure 上[的 IBM Db2 LUW 的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)高可用性、SAP Hana 上的 Azure Vm 上的 SUSE Linux Enterprise Server，以及適用于 azure 負載 SUSE Linux Enterprise Server 平衡器偵測強化的[高](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)可用性
- 變更[SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中的及區段和標頭區段
- 10/21/2019：使用 SLES 上的[Azure NetApp Files 在 Azure vm 上以待命節點發行 SAP Hana 相應](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)放大
- 10/16/2019：修正[備份和還原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)中中斷的連結
- 10/16/2019：在 SUSE Linux Enterprise Server 上使用 Pacemaker，將 SLES 12 SP3 的最低建議 OS 變更為[Azure vm 上的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)SLES 12 SP4
- 10/11/2019： [SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中，對 Ultra 磁片儲存體設定和引入及的變更
- 10/01/2019：變更[Azure 鄰近放置群組的圖形以取得最佳的網路延遲，讓 SAP 應用程式](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)更清楚
- 10/01/2019：變更[Azure 上 SAP Hana 的基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)，以更正適用于/hana/shared 的高可用性 NFS 共用的語句 



