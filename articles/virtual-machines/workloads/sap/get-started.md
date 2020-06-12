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
ms.date: 05/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b95112146c0003f3fc5ea563e5561dc26ea064e8
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800735"
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

- 有哪些 SAP 軟體版本和作業系統版本支援哪些 Azure VM 和 HANA 大型執行個體單位。 請參閱 [Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) \(部分機器翻譯\) 文件以取得解答及找到相關資訊的程序
- Azure VM 和 HANA 大型執行個體支援哪些 SAP 部署案例。 在下列文件中可以找到支援案例的相關資訊：
    - [Azure 虛擬機器支援案例上的 SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) \(部分機器翻譯\)
    - [HANA 大型執行個體的支援案例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario) \(部分機器翻譯\)
- 若要了解在不同的 Azure 區域中，可以使用哪些 Azure 服務、Azure VM 類型及 Azure 儲存體服務，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)網站 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體)

一系列的文件將引導您逐步完成 Azure 上的 SAP HANA (大型執行個體)，或簡稱 HANA 大型執行個體。 如需 HANA 大型執行個體的相關資訊，請從 [Azure 上的 SAP HANA (大型執行個體) 的概觀與架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) \(部分機器翻譯\) 文件開始，然後逐步完成＜HANA 大型執行個體＞一節中的相關文件



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP HANA
文件的本節涵蓋 SAP HANA 的不同層面。 作為先決條件，您應該先熟悉提供 Azure IaaS 基礎服務的 Azure 主要服務。 因此，您需要 Azure 計算、儲存體和網路的知識。 這些主題中，有許多會在 SAP NetWeaver 相關的 [Azure 規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) \(部分機器翻譯\) 中探討。 

如需 Azure 上的 HANA 的相關資訊，請參閱下列文章及其子文章：

- [SAP HANA 在 Azure 上的基礎結構組態和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虛擬機器的 SAP Hana 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 虛擬機器上 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虛擬機器上的 SAP HANA 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) \(部分機器翻譯\)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>在 Azure 虛擬機器上部署的 SAP NetWeaver
此節會列出 Azure 上的 SAP NetWeaver 和 Business One 的規劃和部署文件。 文件會專注在於 Azure 上搭配 SAP 工作負載之非 HANA 資料庫的基本概念及使用方式。 針對高可用性的文件和文章，也是 Azure 中 HANA 高可用性的基礎，例如：

- [Azure 計劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) \(部分機器翻譯\)。 
- [Azure 虛擬機器上的 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure) \(部分機器翻譯\)
- [使用 Site Recovery 保護多層式 SAP NetWeaver 應用程式部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) \(部分機器翻譯\)
- [適用於 Azure 的 SAP LaMa 連接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

如需 Azure 上 SAP 工作負載底下之非 HANA 資料庫的相關資訊，請參閱：

- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [適用於 SAP NetWeaver 的 SQL Server Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM 上的 SAP MaxDB、Live Cache 和 Content Server 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb) \(部分機器翻譯\)

針對 Azure 上 SAP HANA 資料庫的相關資訊，請參閱＜Azure 虛擬機器上的 SAP HANA＞一節。

如需 Azure 上 SAP 工作負載之高可用性的相關資訊，請參閱：

- [SAP NetWeaver 的 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

此文件會指向各種其他架構和案例文件。 在稍後的案例文件中，會提供詳細技術文件的連結，其中說明如何部署和設定不同的高可用性方法。 說明如何針對 SAP NetWeaver 工作負載建立及設定高可用性的不同文件，會涵蓋 Linux 和 Windows 作業系統。


如需 Azure Active Directory (Azure AD) 和 SAP 服務之間的整合及單一登入的相關資訊，請參閱：

- [教學課程：Azure Active Directory 與 SAP Cloud for Customer 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Cloud Platform Identity Authentication 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP 雲端平台整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP NetWeaver 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Business ByDesign 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP HANA 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [您的 S/4HANA 環境：使用 Azure AD 進行 Fiori Launchpad SAML 單一登入](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/) \(英文\)

如需將 Azure 服務整合到 SAP 元件的相關資訊，請參閱：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 連接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory 提供 SAP HANA 和 Business Warehouse 資料整合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>變更記錄檔

- 2020/05/21：於[在 Azure 中於 SLES 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) \(部分機器翻譯\) 及[在 Azure 中於 RHEL 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) \(部分機器翻譯\) 中進行變更，以新增[在 SAP HA 案例中使用 Azure Standard ILB 取得 VM 的公用端點連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) \(部分機器翻譯\) 的連結  
- 2020/05/19：在 [SAP HANA Azure 虛擬機器儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) \(部分機器翻譯\) 中新增「針對 HANA 相關磁碟區使用 LVM 時不要使用根磁碟區群組」的重要訊息
- 2020/05/19：在[適用於 HANA 大型執行個體的相容作業系統](https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) \(部分機器翻譯\) 中新增針對 HANA 大型執行個體類型 II 所新支援的 OS
- 2020/05/12：在[於 SAP HA 案例中使用 Azure Standard ILB 取得 VM 的公用端點連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) \(部分機器翻譯\) 中進行變更，以更新連結並新增協力廠商防火牆設定的資訊
- 2020/05/11：在 [SLES 上之 Azure VM 上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) \(部分機器翻譯\) 中進行變更，以針對 netcat 資源將資源黏著度設定為 0，因為該設定能使容錯移轉更加順暢 
- 2020/05/05：在 [SAP NetWeaver 的 Azure 虛擬機器規劃和實作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) \(部分機器翻譯\) 中進行變更，以表示 Gen2 部署可供 Mv1 VM 系列使用
- 2020/04/24：在[於 SLES 上使用 ANF 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) \(部分機器翻譯\)、[於 RHEL 上使用 ANF 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) \(部分機器翻譯\)、[於 SLES 上使用 ANF 在 Azure VM 上取得 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) \(部分機器翻譯\)，以及[於 RHEL 上使用 ANF 在 Azure VM 上取得 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) \(部分機器翻譯\) 中進行變更，以釐清系統會自動指派 ANF 磁碟區的 IP 位址
- 2020/04/22：在 [SLES 上之 Azure VM 上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) \(部分機器翻譯\) 中進行變更，以從指示中移除中繼屬性 `is-managed`，因為其會與使叢集進入或結束維護模式產生衝突
- 2020/04/21：在 [Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) \(部分機器翻譯\) 和[於 Microsoft Azure 上執行的 SAP 認證和設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications) \(部分機器翻譯\) 文章中，新增 SQL Azure DB 作為 SAP (Hybris) Commerce Platform 1811 及更新版本所支援的 DBMS
- 2020/04/16：在 [Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) \(部分機器翻譯\) 和[於 Microsoft Azure 上執行的 SAP 認證和設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications) \(部分機器翻譯\) 文章中，新增 SAP HANA 作為 SAP (Hybris) Commerce Platform 所支援的 DBMS
- 2020/04/13：在[適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase) \(部分機器翻譯\) 中修正為確切的 SAP ASE 版本號碼
- 2020/04/07：於[在 Azure 中於 SLES 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) \(部分機器翻譯\) 中進行變更，以釐清 cloud-netconfig-azure 指示
- 2020/04/06：在[於 SLES 上使用 Azure NetApp Files 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) \(部分機器翻譯\) 和[於 RHEL 上使用 Azure NetApp Files 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) \(部分機器翻譯\) 中進行變更，以移除對 NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) \(英文\) 的參考 (以 [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf) \(英文\) 取代)
- 2020/03/31：在 [SLES 上之 Azure VM 上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) \(部分機器翻譯\) 和 [RHEL 上之 Azure VM 上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) \(部分機器翻譯\) 中進行變更，以新增如何在建立等量磁碟區時指定等量大小的指示
- 2020/03/27：在[於 SLES for SAP 應用程式上使用 ANF 以在 Azure VM 上取得 SAP NW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) \(部分機器翻譯\) 中進行變更，以使檔案系統掛接選項符合 NetApp TR-4746 (移除同步掛接選項)
- 2020/03/26：在 [SLES 上之 Azure VM 上的 SAP NetWeaver 高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) \(部分機器翻譯\) 中進行變更，以新增 NetApp TR-4746 的參考
- 2020/03/26：在[於 SLES for SAP Applications 上在 Azure VM 上取得 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) \(部分機器翻譯\)、[於 SLES for SAP Applications 上使用 Azure NetApp Files 以在 Azure VM 上取得 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) \(部分機器翻譯\)、[SLES 上之 Azure VM 上的 NFS 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) \(部分機器翻譯\)、[SLES 上之 Azure VM 上的 SAP NetWeaver 高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) \(部分機器翻譯\)、[於 RHEL for SAP Applications 上在 Azure VM 上取得 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) \(部分機器翻譯\)，以及[於 RHEL for SAP Applications 上使用 Azure NetApp Files 在 Azure VM 上取得 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) \(部分機器翻譯\) 中進行變更，以更新圖表並釐清建立 Azure Load Balancer 後端集區的指示
- 2020/03/19：對[快速入門：在 Azure 虛擬機器上手動安裝單一執行個體 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) \(部分機器翻譯\) 文件進行重大修訂，並修改為[在 Azure 虛擬機器上安裝 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) \(部分機器翻譯\)
- 2020/03/17：於[在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) \(部分機器翻譯\) 中進行變更，以移除不再需要的 SBD 組態設定
- 2020/03/16：在 [Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) \(部分機器翻譯\) 中，釐清 SAP HANA IaaS 已認證平台中的資料行認證案例
- 2020/03/11：在 [Azure 虛擬機器支援案例上的 SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) \(部分機器翻譯\) 中進行變更，以釐清每個 DBMS 執行個體使用多個資料庫的支援
- 2020/03/11：在 [SAP NetWeaver 的 Azure 虛擬機器規劃和實作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) \(部分機器翻譯\) 中進行變更，說明第 1 代和第 2 代 VM
- 2020/03/10：在 [SAP HANA Azure 虛擬機器儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) \(部分機器翻譯\) 中進行變更，以釐清 ANF 實際的現有輸送量限制
- 2020/03/09：在[於 SUSE Linux Enterprise Server for SAP Applications 上在 Azure VM 上取得 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) \(部分機器翻譯\)、[於 SUSE Linux Enterprise Server for SAP Applications 上使用 Azure NetApp Files 以在 Azure VM 上取得 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) \(部分機器翻譯\)、[SUSE Linux Enterprise Server 上之 Azure VM 上的 NFS 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) \(部分機器翻譯\)、[在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) \(部分機器翻譯\)、[於 SUSE Linux Enterprise Server 上使用 Pacemaker 在 Azure VM 上取得 IBM Db2 LUW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) \(部分機器翻譯\)、[SUSE Linux Enterprise Server 上之 Azure VM 上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) \(部分機器翻譯\)，以及[SLES 上之 Azure VM 上的 SAP NetWeaver 高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) \(部分機器翻譯\) 中進行變更，以搭配資源代理程式 azure-lb 更新叢集資源 
- 2020/03/05：在 [SAP NetWeaver 的 Azure 虛擬機器規劃和實作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) \(部分機器翻譯\) 中對 Azure 區域和 Azure 虛擬機器進行結構變更和內容變更
- 2020/03/03：在[於 SLES for SAP 應用程式上使用 ANF 以在 Azure VM 上取得 SAP NW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) \(部分機器翻譯\) 中進行變更，以變更為更加有效的 ANF 磁碟區配置
- 2020/03/01：重新改寫 [Azure 虛擬機器上的 SAP HANA 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) \(部分機器翻譯\) 以包含 Azure 備份服務。 已減少並濃縮[檔案層級的 SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) \(部分機器翻譯\) 中的內容，並刪除關於透過磁碟快照集進行備份的第三份文件。 該內容已於《Azure 虛擬機器上的 SAP HANA 備份指南》中處理 
- 2020/02/27：在[於 SLES for SAP Applications 上在 Azure VM 上取得 SAP NW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) \(部分機器翻譯\)、[於 SLES 上針對 SAP 應用程式使用 ANF 以在 Azure VM 上取得 SAP NW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) \(部分機器翻譯\)，以及 [SLES 上之 Azure VM 上的 SAP NetWeaver 高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) \(部分機器翻譯\) 中進行變更，以調整「失敗時的」叢集參數
- 2020/02/26：在 [SAP HANA Azure 虛擬機器儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) \(部分機器翻譯\) 中進行變更，以釐清 Azure 上適用於 HANA 的檔案系統選擇
- 2020/02/26：在 [SAP 的高可用性架構和案例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) \(部分機器翻譯\) 中進行變更，以包含《於 RHEL 上在 Azure VM 上取得 SAP NetWeaver HA 多 SID 指南》的連結
- 2020/02/26：在[於 SLES for SAP Applications 上在 Azure VM 上取得 SAP NW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) \(部分機器翻譯\)、[於 SLES for SAP 應用程式上使用 ANF 以在 Azure VM 上取得 SAP NW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) \(部分機器翻譯\)、[RHEL 上適用於 SAP NetWeaver 的 Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) \(部分機器翻譯\)，以及[於 RHEL 上使用 Azure NetApp Files 針對 SAP NetWeaver 取得 Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) \(部分機器翻譯\) 中進行變更，以移除不支援多 SID ASCS/ERS 叢集的陳述
- 2020/02/26：發佈[於 RHEL 上在 Azure VM 上取得 SAP NetWeaver 高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) \(部分機器翻譯\) 以新增《SUSE 多 SID 叢集指南》的連結
- 2020/02/25：在 [SAP 的高可用性架構和案例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) \(部分機器翻譯\) 中進行變更，以新增較新 HA 文章的連結
- 2020/02/25：在[於 SUSE Linux Enterprise Server 上使用 Pacemaker 在 Azure VM 上取得 IBM Db2 LUW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) \(部分機器翻譯\) 中進行變更，以指向描述使用標準 Azure Load balancer 存取公用端點的文件
- 2020/02/21：對[適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase) \(部分機器翻譯\) 進行完整修訂
- 2020/02/21：在 [SAP HANA Azure 虛擬機器儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) \(部分機器翻譯\) 中進行變更，以提供對 /hana/data 等量大小的新建議，並新增 I/O 排程器的設定
- 2020/02/21：在 HANA 大型執行個體文件中進行變更，以表示新認證的 S224 和 S224m SKU
- 2020/02/21：在 [RHEL 上適用於 SAP NetWeaver 的 Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) \(部分機器翻譯\) 和[在 RHEL 上使用 Azure NetApp Files 針對 SAP NetWeaver 取得 Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) \(部分機器翻譯\) 中進行變更，以調整加入佇列伺服器複寫 2 架構 (ENSA2) 的叢集條件約束
- 2020/02/20：在 [SLES 上之 Azure VM 上的 SAP NetWeaver 高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) \(部分機器翻譯\) 中進行變更，以新增《SUSE 多 SID 叢集指南》的連結
- 2020/02/13：在 [SAP NetWeaver 的 Azure 虛擬機器規劃和實作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) \(部分機器翻譯\) 中進行變更，以實作新文件的連結
- 2020/02/13：新增新文件 [Azure 虛擬機器支援案例上的 SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) \(部分機器翻譯\)
- 2020/02/13：新增新文件 [Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) \(部分機器翻譯\)
- 2020/02/13：在[於 Red Hat Enterprise Linux Server 上在 Azure VM 上取得 IBM Db2 LUW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) \(部分機器翻譯\) 中進行變更，以指向描述使用標準 Azure Load balancer 存取公用端點的文件
- 2020/02/13：將新的 VM 類型新增至[於 Microsoft Azure 上執行的 SAP 認證和設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications) \(部分機器翻譯\)
- 2020/02/13：將新的 SAP 支援附註新增至 [Azure 上的 SAP 工作負載：規劃和部署檢查清單](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) \(部分機器翻譯\)
- 2020/02/13：在 [RHEL 上適用於 SAP NetWeaver 的 Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) \(部分機器翻譯\) 和[在 RHEL 上使用 Azure NetApp Files 針對 SAP NetWeaver 取得 Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) \(部分機器翻譯\) 中進行變更，以使叢集資源逾時符合 Red Hat 逾時建議
- 2020/02/11：發佈[將 Azure 上的 SAP HANA 大型執行個體移轉到 Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration) \(部分機器翻譯\)
- 2020/02/07：在[於 SAP HA 案例中使用 Azure Standard ILB 取得 VM 的公用端點連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) \(部分機器翻譯\) 中進行變更，以更新範例 NSG 螢幕擷取畫面
- 2020/02/03：在[於 SLES for SAP Applications 上在 Azure VM 上取得 SAP NW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) \(部分機器翻譯\) 和[於 SLES for SAP 應用程式上使用 ANF 以在 Azure VM 上取得 SAP NW 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) \(部分機器翻譯\) 中進行變更，以移除針對在 SLES 上叢集節點的主機名稱中使用破折號的警告
- 2020/01/28：在 [RHEL 上之 Azure VM 上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) \(部分機器翻譯\) 中進行變更，以使 SAP HANA 叢集資源逾時符合 Red Hat 逾時建議
- 2020/01/17：在[適用於搭配 SAP 應用程式取得最佳網路延遲的 Azure 鄰近位置群組](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) \(部分機器翻譯\) 中進行變更，以變更將現有 VM 移至鄰近位置群組的小節
- 2020/01/17：在[使用 Azure 可用性區域進行 SAP 工作負載設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) \(部分機器翻譯\) 中進行變更，以指向能將可用性區域之間延遲的測量自動化的程序
- 2020/01/16：在[如何在 Azure 上安裝並設定 SAP HANA (大型執行個體)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) \(部分機器翻譯\) 中進行變更，以針對 HANA IaaS 硬體目錄調整 OS 版本
- 2020/01/16：在 [SLES 上之 Azure VM 上的 SAP NetWeaver 高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) \(部分機器翻譯\) 中進行變更，以新增適用於 SAP 系統，使用加入佇列伺服器 2 架構 (ENSA2) 的指示
- 2020/01/10：在[於 SLES 上使用 Azure NetApp Files 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) \(部分機器翻譯\) 和[於 RHEL 上使用 Azure NetApp Files 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) \(部分機器翻譯\) 中進行變更，以新增如何對 `nfs4_disable_idmapping` 進行永久變更的指示。
- 2020/01/10：在[於 SLES for SAP Applications 上使用 Azure NetApp Files 以在 Azure VM 上取得 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) \(部分機器翻譯\) 和[於 RHEL for SAP Applications 上使用 Azure NetApp Files 以取得 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) \(部分機器翻譯\) 中進行變更，以新增如何掛接 Azure NetApp Files NFSv4 磁碟區的指示。
- 2019 年 12 月 23 日：發佈[SLES 上之 Azure VM 上的 SAP NetWeaver 高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) \(部分機器翻譯\)
- 2019 年 12 月 18 日：發佈[於 RHEL 上使用 Azure NetApp Files 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) \(部分機器翻譯\)
- 2019 年 11 月 21 日：在[於 SUSE Linux Enterprise Server 上使用 Azure NetApp Files 在 Azure VM 上搭配待命節點進行 SAP HANA 擴增](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) \(部分機器翻譯\) 中進行變更，以簡化 NFS 識別碼對應的設定，以及變更建議的主要網路介面以簡化路由。
- 2019 年 11 月 15 日：在[於 SUSE Linux Enterprise Server for SAP Applications 上使用 Azure NetApp Files 以取得 SAP NetWeaver 高可用性](high-availability-guide-suse-netapp-files.md)和[於 Red Hat Enterprise Linux for SAP Applications 上使用 Azure NetApp Files 以取得 SAP NetWeaver 高可用性](high-availability-guide-rhel-netapp-files.md)中進行次要變更，以釐清容量集區大小限制，並移除僅支援 NFSv3 版本的陳述。
- 2019 年 11 月 12 日：發佈[於 Windows 上使用 Azure NetApp Files (SMB) 取得 SAP NetWeaver 高可用性](high-availability-guide-windows-netapp-files-smb.md)
- 2019 年 11 月 8 日：在 [SUSE Linux Enterprise Server 上之 Azure VM 上的 SAP HANA 高可用性](sap-hana-high-availability.md)、[在 Azure 虛擬機器 (VM) 上設定 SAP HANA 系統複寫](sap-hana-high-availability-rhel.md)、[於 SUSE Linux Enterprise Server for SAP Applications 上針對 SAP NetWeaver 取得 Azure 虛擬機器高可用性](high-availability-guide-suse.md)、[於 SUSE Linux Enterprise Server 上使用 Azure NetApp Files 以針對 SAP NetWeaver 取得 Azure 虛擬機器高可用性](high-availability-guide-suse-netapp-files.md)、[Red Hat Enterprise Linux 上適用於 SAP NetWeaver 的 Azure 虛擬機器高可用性](high-availability-guide-rhel.md)、[於 Red Hat Enterprise Linux 上使用 Azure NetApp Files 針對 SAP NetWeaver 取得 Azure 虛擬機器高可用性](high-availability-guide-rhel-netapp-files.md)、[SUSE Linux Enterprise Server 上之 Azure VM 上的 NFS 高可用性](high-availability-guide-suse-nfs.md)、[Red Hat Enterprise Linux 上之 Azure VM 上適用於 SAP NetWeaver 的 GlusterFS](high-availability-guide-rhel-glusterfs.md) 中進行變更，以建議 Azure 標準負載平衡器  
- 2019 年 11 月 8 日：在 [SAP 工作負載規劃和部署檢查清單](sap-deployment-checklist.md)中進行變更，以釐清加密建議  
- 2019 年 11 月 4 日：於[在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker](high-availability-guide-suse-pacemaker.md) 中進行變更，以直接使用單點傳播設定來建立叢集  


