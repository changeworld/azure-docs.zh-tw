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
ms.date: 02/25/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c9d287d5bfc091fcbbff46095da5458fa08de73d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624177"
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

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA (大型執行個體)

一系列的檔會引導您完成 Azure （大型實例）或簡短的 HANA 大型實例的 SAP Hana。 如需有關 HANA 大型實例的詳細資訊，請從[Azure （大型實例）上的 SAP Hana 檔總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)開始著手，並流覽 HANA 大型實例一節中的相關檔



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP Hana
文件的本節涵蓋 SAP HANA 的不同層面。 必要條件是，您應該熟悉提供 Azure IaaS 基本服務的 Azure 主要服務。 因此，您需要 Azure 計算、儲存體和網路的知識。 這些主題中有許多都是在 SAP NetWeaver 相關的[Azure 規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中處理。 

如需有關 Azure 上 HANA 的詳細資訊，請參閱下列文章及其 subarticles：

- [快速入門：在 Azure VM 上手動安裝單一執行個體 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [SAP HANA 在 Azure 上的基礎結構組態和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虛擬機器的 SAP Hana 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [單一 Azure 區域中的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [跨 Azure 區域的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 虛擬機器上 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虛擬機器上的 SAP Hana 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [檔案層級的 SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [以儲存體快照集為基礎的 SAP HANA 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>部署在 Azure 虛擬機器上的 SAP NetWeaver
本節列出 Azure 上 SAP NetWeaver 和 Business One 的規劃和部署檔。 本檔著重于在 Azure 上使用非 HANA 資料庫與 SAP 工作負載的基本概念。 適用于高可用性的檔和文章也是 Azure 中 HANA 高可用性的基礎，例如：

- [Azure 虛擬機器上的 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [在 Azure 上部署適用於 SAP ERP 6.0 的 SAP IDES EHP7 SP3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [在 Microsoft Azure SUSE Linux Vm 上執行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [適用於 SAP NetWeaver 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
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
- [您的 S/4HANA 環境：使用 Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/) 的 Fiori 啟動列 SAML 單一登入

如需將 Azure 服務整合到 SAP 元件的相關資訊，請參閱：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 連接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory 提供 SAP HANA 和 Business Warehouse 資料整合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>變更記錄檔
- 02/25/2020：適用于[SAP 的高可用性架構和案例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)變更新增新 HA 文章的連結
- 02/25/2020：在[SUSE Linux Enterprise Server 上使用 Pacemaker 的 Azure vm 上的 IBM DB2 LUW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)變更，以指向說明使用標準 Azure 負載平衡器存取公用端點的檔
- 02/21/2020：[針對 sap 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)文章的完整修訂
- 02/21/2020：[SAP Hana Azure 虛擬機器存放裝置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中的變更，以代表新建議的等量大小以/hana/data 和新增 i/o 排程器的設定
- 02/21/2020：HANA 大型實例檔中的變更，代表 S224 和 S224m 的新認證 Sku
- 02/21/2020：[Azure vm 在 rhel 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和 azure vm 上的 sap NetWeaver 高可用性在 rhel 上的 sap [NetWeaver 使用 azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)來調整佇列伺服器複寫2架構的叢集限制（ENSA2）
- 02/20/2020：[Azure vm 上的 SAP NetWeaver 的高可用性變更 SLES 多 sid 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)，以新增 SUSE 多重 sid 叢集指南的連結
- 02/13/2020：[針對 SAP NetWeaver 的 Azure 虛擬機器規劃和執行的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)變更，以執行新檔的連結
- 02/13/2020：已[在 Azure 虛擬機器支援的案例中新增 SAP 工作負載的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)新檔
- 02/13/2020：已新增新檔[Azure 部署支援的 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020：在[Red Hat Enterprise Linux 伺服器上的 Azure vm 上，將 IBM DB2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)變更為指向說明使用標準 Azure 負載平衡器存取公用端點的檔
- 02/13/2020：將新的 VM 類型新增至[在 Microsoft Azure 上執行的 SAP 認證和](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)設定
- 02/13/2020：在 Azure 上新增 SAP 支援附注[sap 工作負載：規劃和部署檢查清單](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020：在[Azure vm 中變更 Sap NetWeaver 的高可用性（rhel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) ）和[azure VM 高可用性 SAP NetWeaver On Rhel 與 azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) ，以將叢集資源的超時時間對應至 Red Hat 超時建議
- 02/11/2020：[將 Azure 大型實例遷移至 azure 虛擬機器的 SAP Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)版本
- 02/07/2020：在[SAP HA 案例中使用 Azure 標準 ILB 來變更 vm 的公用端點連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)，以更新範例 NSG 螢幕擷取畫面
- 02/03/2020：在 sles [FOR sap 應用程式的 Azure vm 上使用 SAP nw 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變更和 azure vm 上的 Sap nw[高可用性（含及 for sap applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) ），以移除在 sles 上叢集節點的主機名稱中使用破折號的警告
- 01/28/2020：在[RHEL 上的 Azure vm 上 SAP Hana 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)變更，使 SAP Hana 叢集資源的超時時間與 Red Hat 超時建議一致
- 01/17/2020：[針對 SAP 應用程式的最佳網路延遲變更 Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，以變更將現有 vm 移至鄰近位置群組的區段
- 01/17/2020：變更[SAP 工作負載設定與 Azure 可用性區域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)以指向可自動測量可用性區域之間延遲的程式
- 01/16/2020：如何在[Azure 上安裝和設定 SAP Hana （大型實例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)以調整 OS 版本與 HANA IaaS 硬體目錄的變更
- 01/16/2020：[SLES 多重 SID 指南上的 Sap NetWeaver 在 Azure vm 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)變更使用佇列伺服器2架構（ENSA2）新增 sap 系統的指示
- 01/10/2020：SAP Hana 向外延展和 azure Vm 上的[待命節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)中的變更，以及在[azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)上使用 azure netapp files 和 SAP Hana 向外延展中的待命節點，以新增如何進行永久 `nfs4_disable_idmapping` 變更的指示。
- 01/10/2020：在[SLES 上使用適用于 sap 應用程式的 Azure Netapp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)和虛擬機器 azure 中的 sap NetWeaver 的高可用性變更（使用適用于 sap 應用程式的 sap [NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)）在 RHEL 上以 azure netapp files 來新增如何掛接 azure netapp files NFSv4 磁片區的指示。
- 12/23/2019：[SLES 多 SID 指南上的 SAP NetWeaver On Azure vm 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)版本
- 12/18/2019：使用 RHEL 上的[Azure NetApp Files 在 Azure vm 上以待命節點發行 SAP Hana 相應](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)放大
- 11/21/2019：在[SUSE Linux Enterprise Server 上使用 Azure NetApp Files 的 Azure vm 上的待命節點 SAP Hana 相應](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)放大的變更，以簡化 NFS 識別碼對應的設定，並變更建議的主要網路介面以簡化路由。
- 11/15/2019：[SUSE Linux Enterprise Server 上的 Sap NetWeaver 高可用性的](high-availability-guide-suse-netapp-files.md)次要變更，以及適用于 sap 應用程式的 Azure netapp files 和 Red Hat Enterprise Linux 上的 sap NetWeaver 的高可用性，以及適用于 sap[應用程式的 azure netapp files](high-availability-guide-rhel-netapp-files.md) ，以清楚瞭解容量集區大小限制，以及僅支援 NFSv3 版本的 remove 語句。
- 11/12/2019：[使用 Azure NetApp Files （SMB）發行 SAP NetWeaver On Windows 的高可用性](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019：SUSE Linux Enterprise Server 上[的 Azure vm 上的 SAP Hana 的高可用性](sap-hana-high-availability.md)變更、在[azure 虛擬機器（vm）上設定 SAP Hana 系統](sap-hana-high-availability-rhel.md)複寫[、azure 虛擬機器 sap NetWeaver on](high-availability-guide-suse.md)sap NetWeaver 的高可用性、azure SUSE Linux Enterprise Server 的 sap NetWeaver 高可用性虛擬機器上的 azure [NetApp Files](high-availability-guide-suse-netapp-files.md)、azure SUSE Linux Enterprise Server 在虛擬機器[上](high-availability-guide-rhel.md)[的 sap 高可用性Azure 虛擬機器高可用性，適用于 Red Hat Enterprise Linux 上的 SAP NetWeaver，其中包含 Azure NetApp Files](high-availability-guide-rhel-netapp-files.md)、 [azure vm 上的 NFS 高可用性、SUSE Linux Enterprise Server 上的](high-availability-guide-suse-nfs.md) [GLUSTERFS、適用于 sap NetWeaver 的 Red Hat Enterprise Linux](high-availability-guide-rhel-glusterfs.md)上的 Azure vm，以建議 azure 標準負載平衡器  
- 11/08/2019：[SAP 工作負載規劃和部署檢查清單](sap-deployment-checklist.md)中的變更，以闡明加密建議  
- 11/04/2019：在 Azure 中的[SUSE Linux Enterprise Server 上設定 Pacemaker](high-availability-guide-suse-pacemaker.md) ，以直接使用單播設定建立叢集的變更  
- 10/29/2019：[在 SAP 高可用性案例中使用 Azure Standard Load Balancer 虛擬機器的公用端點連線性](high-availability-guide-standard-load-balancer-outbound-connections.md)版本
- 10/25/2019：[SAP Hana azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定的變更，以及[使用 azure 上的待命節點 SAP Hana 向外延展，並在 SUSE Linux Enterprise Server 上使用 azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)來澄清/HANA/SHARED 磁片區的 NFS 通訊協定
- 10/22/2019：[Azure vm 上的 Sap NetWeaver 的高可用性變更在 sap 應用程式的 SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)、azure 上的[sap SUSE Linux Enterprise Server NetWeaver 的高可用性、適用于 sap 應用程式的 azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)、SUSE Linux Enterprise Server 上的 azure vm 上的[高](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)可用性、在 azure 上的 azure vm 上[設定 SUSE Linux Enterprise Server Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)、在具有 Pacemaker 的 SUSE Linux Enterprise Server 上，以高可用性的方式在 azure 上進行[IBM Db2 LUW](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) 適用于 Azure 負載平衡器偵測強化 [SUSE Linux Enterprise Server 上 Azure vm 上的 SAP Hana 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- 變更[SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中的及區段和標頭區段
- 10/21/2019：使用 SLES 上的[Azure NetApp Files 在 Azure vm 上以待命節點發行 SAP Hana 相應](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)放大
- 10/16/2019:修正[備份與還原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)中斷的連結
- 10/16/2019:在[SUSE Linux Enterprise Server 使用 Pacemaker 的 Azure vm 上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)，將 SLES 12 SP3 的最低建議 OS 變更為 SLES 12 SP4 的高可用性
- 10/11/2019：在[SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中，對 Ultra 磁片儲存體設定和引入及的變更
- 10/01/2019：在 Azure 鄰近放置群組的圖形中變更，以取得更清楚的[SAP 應用程式網路延遲](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)
- 10/01/2019：變更[Azure 上 SAP Hana 的基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)，以更正適用于/hana/shared 的高可用性 NFS 共用的語句 
- 09/28/2019：在[Azure 中的 Red Hat Enterprise Linux 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) ，以將 SBD 視為隔離機制，而不支援 RHEL 叢集  
- 09/17/2019：NetWeaver 規劃與部署指南中的變更，以整合適用于 SAP 的 VM 延伸模組術語  
- 08/22/2019：在[Azure 中的 SUSE Linux Enterprise Server 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)以更新自訂角色建立的 url 中的變更  
- 08/16/2019：在[azure Red Hat Enterprise Linux 上設定 Pacemaker 的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)變更，以在更新至新版本的 azure 隔離代理程式時，提醒客戶更新自訂角色中的動作  
- 08/15/2019：[SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定的變更，以反映 ultra （舊稱 ultra SSD）的正式運作
- 08/01/2019：在[Azure 中的 SUSE Linux Enterprise Server 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) ，以整合特別針對 SLES 15 進行的變更 


