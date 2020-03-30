---
title: 開始在 Azure VM 上使用 SAP |微軟文檔
description: 瞭解在 Microsoft Azure 中的虛擬機器 （VM） 上運行的 SAP 解決方案
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
ms.date: 03/27/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4c708b6e872bfc54371aa38ee1472d27dcb00952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373037"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 託管和運行 SAP 工作負載方案

使用 Microsoft Azure 時，可以在可擴展、合規且經過企業驗證的平臺上可靠地運行任務關鍵型 SAP 工作負載和方案。 您可以獲得 Azure 的可伸縮性、靈活性和成本節約。 通過 Microsoft 和 SAP 之間擴展的夥伴關係，您可以在 Azure 中跨開發、測試和生產方案運行 SAP 應用程式，並得到充分支援。 從 SAP NetWeaver 到 SAP S/4HANA，從 Linux 上的 SAP BI 到 Windows，從 SAP HANA 到 SQL，我們為您提供服務。

除了在 Azure 上託管具有不同 DBMS 的 SAP NetWeaver 方案外，您還可以託管其他 SAP 工作負載方案，如 Azure 上的 SAP BI。 

SAP HANA Azure 的獨特性是使 Azure 與眾不同的一個產品/ 為了啟用託管更多涉及 SAP HANA 的記憶體和 CPU 資源要求苛刻的 SAP 方案，Azure 提供了客戶專用裸機硬體的使用。 使用此解決方案可運行 SAP HANA 部署，這些部署需要高達 24 TB（120 TB 擴展）記憶體才能用於 S/4HANA 或其他 SAP HANA 工作負載。 

在 Azure 中託管 SAP 工作負載方案還可以創建標識集成和單一登入的要求。 當您使用 Azure 活動目錄 （Azure AD） 連接不同的 SAP 元件和 SAP 軟體即服務 （SaaS） 或平臺即服務 （PaaS） 產品時，可能會出現這種情況。 與 Azure AD 和 SAP 實體的此類集成和單一登入方案的清單在"AAD SAP 標識集成和單一登入"一節中描述和記錄。

## <a name="changes-to-the-sap-workload-section"></a>對 SAP 工作負荷部分的更改
本文末尾列出了對 Azure 工作負荷上的 SAP 中文檔的更改。 更改日誌中的條目將保留大約 180 天。

## <a name="you-want-to-know"></a>你想知道
如果您有具體問題，我們將在起始頁的這一部分中指向特定文檔或流。 您想知道：

- 支援哪些 SAP 軟體版本和哪些作業系統版本發佈的 Azure VM 和 HANA 大型實例單元。 閱讀文檔[Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)以查找答案以及查找資訊的過程
- Azure VM 和 HANA 大型實例支援哪些 SAP 部署方案。 有關支援的方案的資訊，請參閱文檔：
    - [Azure 虛擬機器支援的方案上的 SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [HANA 大型實例受支援的方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- 哪些 Azure 服務、Azure VM 類型和 Azure 存儲服務在不同的 Azure 區域中可用，請[按區域](https://azure.microsoft.com/global-infrastructure/services/)檢查網站產品 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體)

一系列文檔引導您通過 Azure（大型實例）上的 SAP HANA，或者對於簡短的 HANA 大型實例。 有關 HANA 大型實例的資訊，從[Azure（大型實例）上的 SAP HANA 文檔概述和體系結構開始，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)然後流覽 HANA 大型實例部分中的相關文檔



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP HANA
文件的本節涵蓋 SAP HANA 的不同層面。 作為先決條件，您應該熟悉 Azure 提供 Azure IaaS 基本服務的主要服務。 因此，您需要瞭解 Azure 計算、存儲和網路。 其中許多主題在 SAP NetWeaver 相關的 Azure[規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中處理。 

有關 Azure 上的 HANA 的資訊，請參閱以下文章及其子文章：

- [SAP HANA 在 Azure 上的基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虛擬機器的 SAP Hana 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 虛擬機器上 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虛擬機器上的 SAP HANA 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>部署在 Azure 虛擬機器上的 SAP NetWeaver
本節列出了 AZURE 上的 SAP NetWeaver 和業務一體的規劃和部署文檔。 本文檔重點介紹 Azure 上具有 SAP 工作負荷的非 HANA 資料庫的基礎知識和使用。 高可用性的文檔和文章也是 Azure 中 HANA 高可用性的基礎，例如：

- [Azure 規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。 
- [Azure 虛擬機器上的 SAP 業務一號](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [使用網站恢復保護多層 SAP NetWeaver 應用程式部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [適用於 Azure 的 SAP LaMa 連接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

有關 Azure 上的 SAP 工作負荷下的非 HANA 資料庫的資訊，請參閱：

- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [適用於 SAP NetWeaver 的 SQL Server Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [在 Azure VM 上部署 SAP MaxDB、即時緩存和內容伺服器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

有關 Azure 上的 SAP HANA 資料庫的資訊，請參閱"Azure 虛擬機器上的 SAP HANA"一節。

有關 Azure 上 SAP 工作負載的高可用性的資訊，請參閱：

- [SAP NetWeaver 的 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

本文檔指向各種其他體系結構和方案文檔。 在以後的方案文檔中，提供了指向詳細技術文檔的連結，這些文檔解釋了不同高可用性方法的部署和配置。 演示如何為 SAP NetWeaver 工作負載建立和配置高可用性的不同文檔涵蓋 Linux 和 Windows 作業系統。


有關 Azure 活動目錄 （Azure AD） 和 SAP 服務和單一登入之間的集成的資訊，請參閱：

- [教學課程：Azure Active Directory 與 SAP Cloud for Customer 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Cloud Platform Identity Authentication 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP 雲端平台整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP NetWeaver 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Business ByDesign 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP HANA 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [您的 S/4HANA 環境：Fiori 啟動板 SAML 單一登入 Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

有關將 Azure 服務集成到 SAP 元件的資訊，請參閱：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 連接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory 提供 SAP HANA 和 Business Warehouse 資料整合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>變更記錄檔
- 2020 年 3 月 27 日：[在 SLES 上的 Azure VM 上 SAP NW 的高可用性變化與 SAP 應用程式進行 ANF，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)以便將檔案系統裝載選項與 NetApp TR-4746 對齊（刪除同步安裝選項）
- 2020/03/26：在[SLES 多 SID 指南上的 Azure VM 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)變化，以添加對 NetApp TR-4746 的引用
- 2020 年 3 月 26 日[：SAP 應用程式在 SLES 上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變化[、SLES 上的 Azure VM 上的 SAP NetWeaver 的高可用性以及用於 SAP 應用程式的 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)、[在 SLES 上的 Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)VM、SLES[多 SID 指南上 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)[、SAP 應用程式 RHEL 上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)以及[使用 Azure 在 RHEL 上的 Azure VM 上的 SAP NetWeaver 的高可用性用於 SAP 應用程式的 NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)，用於更新圖表並闡明 Azure 負載等化器後端池創建的說明
- 03/19/2020：文檔[快速入門的主要修訂：在 Azure 虛擬機器上手動安裝單實例 SAP HANA，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)以[在 Azure 虛擬機器上安裝 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020：在[Azure 中的 SUSE Linux 企業伺服器上設置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)以刪除不再需要的 SBD 配置設置
- 03/16/2020：在[Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)的 SAP HANA IaaS 認證平臺中澄清列認證方案
- 03/11/2020：Azure[虛擬機器支援的方案中 SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)的更改，以澄清每個 DBMS 實例支援的多個資料庫
- 03/11/2020：SAP NetWeaver 解釋第 1 代和第 2 代 VM 的[Azure 虛擬機器規劃和實現](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)的變化
- 03/10/2020：更改[SAP HANA Azure 虛擬機器存儲配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)，以明確 ANF 的實際現有輸送量限制
- 03/09/2020： SAP 應用程式在[SUSE Linux 企業伺服器上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變化， [SUSE Linux 企業伺服器上的 SAP NetWeaver 的高可用性，以及用於 Azure NetApp 檔的 SUSE Linux 企業伺服器上的 SAP NetWeaver SAP 應用程式](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)： [SUSE Linux 企業伺服器上的 Azure VM 上的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)，[在 Azure 中的 SUSE Linux 企業伺服器上設置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)，[在 SUSE Linux 企業伺服器上的 Azure VM 上使用起搏器的高可用性 IBM Db2 LUW，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)[SUSE Linux 企業伺服器上的 Azure VM 上 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)，[以及 SLES 多 SID 指南上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)，以便使用資源代理 azure-lb 更新群集資源 
- 03/05/2020：Azure[虛擬機器規劃和實現的 AZURE NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的 Azure 區域和 Azure 虛擬機器的結構更改和內容更改
- 03/03/2020：SLES[上的 Azure VM 上的 SAP NW 高可用性變化，ANF 使 SAP 應用程式](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)更改為更高效的 ANF 卷佈局
- 03/01/2020：Azure[虛擬機器上的 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)返工備份指南，以包括 Azure 備份服務。 在檔級別減少和壓縮[SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)中的內容，並刪除了處理通過磁片快照進行備份的第三個文檔。 內容在 Azure 虛擬機器上的 SAP HANA 備份指南中處理 
- 2020 年 2 月 27 日[：SAP 應用程式在 SLES 上的 Azure VM 上的 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變化[、SLES 上的 Azure VM 上 SAP NW 的高可用性以及 SAP 應用程式的 ANF](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)和[SLES 多 SID 指南上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)，以調整"故障"群集參數
- 02/26/2020：更改[SAP HANA Azure 虛擬機器存儲配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)，以闡明 Azure 上 HANA 的檔案系統選擇
- 02/26/2020：SAP[的高可用性體系結構和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)的變化，以包括在 RHEL 多 SID 指南上的 Azure VM 上的 SAP NetWeaver 的 HA 連結
- 2020 年 2 月 26 日[：SAP 應用程式在 SLES 上的 Azure VM 上的 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變化[、SLES 上 Azure VM 上的 SAP NW 高可用性以及 SAP 應用程式的 ANF、RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)[上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)以及[RHEL 上 SAP NetWeaver 的 Azure NetWeaver 高可用性以及 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)，以刪除不支援多 SID ASCS/ERS 群集的聲明
- 02/26/2020：在[RHEL 多 SID 指南上的 Azure VM 上發佈 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)版本，以添加指向 SUSE 多 SID 群集指南的連結
- 02/25/2020：[更改高可用性體系結構和方案，以便 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)添加指向較新的 HA 文章的連結
- 02/25/2020：IBM [Db2 LUW 在 SUSE Linux 企業伺服器上的 Azure VM 上的高可用性變化，使用起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)指向描述使用標準 Azure 負載等化器訪問公共終結點的文檔
- 02/21/2020：完成對文章[SAP ASE Azure 虛擬機器 DBMS 部署的 SAP 工作負荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)的完整修訂
- 02/21/2020：更改[SAP HANA Azure 虛擬機器存儲配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)，以表示 /hana/資料條帶大小的新建議，並添加 I/O 計畫程式的設置
- 02/21/2020：HANA 大型實例文檔的更改，以表示新認證的 S224 和 S224m SKU
- 2020 年 2 月 21 日：使用 Azure NetApp 檔[在 RHEL 上更改 SAP NetWeaver 的 AZURE NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和[Azure VM 的高可用性，用於使用 Azure NetApp 檔在 RHEL 上進行 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)，以調整排隊伺服器複製 2 體系結構 （ENSA2） 的群集約束
- 02/20/2020：在[SLES 多 SID 指南上的 Azure VM 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)變化，以添加指向 SUSE 多 SID 群集指南的連結
- 02/13/2020：對[Azure 虛擬機器的更改規劃和實現，以便 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)實施指向新文檔的連結
- 02/13/2020：[在 Azure 虛擬機器支援的方案上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)添加了新的文檔 SAP 工作負載
- 02/13/2020： 添加了新文檔[Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020： [IBM Db2 LUW 在紅帽企業 Linux 伺服器上的 Azure VM 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)變化，以指向描述使用標準 Azure 負載等化器訪問公共終結點的文檔
- 02/13/2020：將新的 VM 類型添加到[在 Microsoft Azure 上運行的 SAP 認證和配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020： 添加新的 SAP 支援說明[Azure 上的 SAP 工作負載：規劃和部署檢查表](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020： Azure VM 中的更改在[RHEL 上對 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)，Azure VM 在[RHEL 上具有 AZURE NetApp 檔，使](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)群集資源超時與紅帽超時建議保持一致
- 02/11/2020：在[Azure 大型實例遷移到 Azure 虛擬機器時發佈 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020：在[SAP HA 方案中使用 Azure 標準 ILB 更改 VM 的公共終結點連接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)，以更新示例 NSG 螢幕截圖
- 02/03/2020：SAP[應用程式在 SLES 上的 Azure VM 上的 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)變化，[在 SLES 上的 Azure VM 上對 SAP NW 的高可用性，用於 SAP 應用程式，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)用於刪除有關在 SLES 上的叢集節點主機名稱中使用破折號的警告
- 01/28/2020：在[RHEL 上的 Azure VM 上更改 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)，以使 SAP HANA 群集資源超時與紅帽超時建議保持一致
- 01/17/2020：使用[SAP 應用程式更改 Azure 接近放置組以獲得最佳網路延遲](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，以更改將現有 VM 移動到鄰近放置組的部分
- 01/17/2020：使用[Azure 可用性區域更改 SAP 工作負載配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)，以指向自動測量可用性區域之間的延遲的過程
- 01/16/2020：更改[如何在 Azure 上安裝和配置 SAP HANA（大型實例），](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)使作業系統版本適應 HANA IaaS 硬體目錄
- 2020 年 1 月 16 日：使用排隊伺服器 2 體系結構 （ENSA2） 在[SLES 多 SID 指南上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)更改，以添加 SAP 系統的說明
- 2020 年 10 月 10 日[：SAP HANA 橫向擴展中，在 Azure VM 上使用具有 Azure NetApp 檔的備用節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)，在[SAP HANA 橫向擴展中，在 Azure VM 上使用具有 AZURE NetApp 檔，在 RHEL 上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)添加有關如何永久進行更改`nfs4_disable_idmapping`的說明。
- 2020 年 10 月 10 日[：SAP NetWeaver 在 SLES 上的 Azure VM 上具有用於 SAP 應用程式的 Azure NetApp 檔，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)在 Azure 虛擬機器中，SAP [NetWeaver 在 RHEL 上具有高可用性，並且使用 AZURE NetApp 檔為 SAP 應用程式](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)添加如何裝載 Azure NetApp 檔 NFSv4 卷的說明。
- 2019/12/23：在[SLES 多 SID 指南上的 Azure VM 上發佈 SAP NetWeaver 的高可用性版本](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 2019/12/18：在[Azure VM 上發佈 SAP HANA 橫向擴展，在 RHEL 上使用 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 2019 年 11 月 21 日[：SAP HANA 擴展中具有 Azure VM 上的備用節點的更改，在 SUSE Linux 企業伺服器上具有 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)，以簡化 NFS ID 映射的配置，並更改建議的主網路介面以簡化路由。
- 2019 年 11 月 15 日[：SUSE Linux 企業伺服器上 SAP NetWeaver 的高可用性略有變化，其中 Azure NetApp 檔適用于 SAP 應用程式](high-availability-guide-suse-netapp-files.md)，而[在紅帽企業 Linux 上，SAP NetWeaver 具有用於 SAP 應用程式的 Azure NetApp 檔](high-availability-guide-rhel-netapp-files.md)，以澄清容量池大小限制並刪除僅支援 NFSv3 版本的聲明。
- 2019/11/12：使用[Azure NetApp 檔 （SMB） 在 Windows 上發佈 SAP NetWeaver 的高可用性版本](high-availability-guide-windows-netapp-files-smb.md)
- 2019/11/08：在[SUSE Linux 企業伺服器上的 Azure VM 上設置 SAP HANA](sap-hana-high-availability.md)系統高可用性的變化，在 Azure 虛擬機器[（VM） 上設置 SAP HANA 系統複製](sap-hana-high-availability-rhel.md)，[在 SUSE Linux 企業伺服器上為 SAP NetWeaver 提供高可用性的 SAP NetWeaver](high-availability-guide-suse.md)應用程式，以及帶有 Azure [NetApp 檔的 SUSE Linux 企業伺服器上的 SAP NetWeaver](high-availability-guide-suse-netapp-files.md)的 Azure 虛擬機器高可用性，以及用於 Web NetApp 檔的 SAP NetWeaver 的[Azure 虛擬機器](high-availability-guide-rhel.md)，[帶有 Azure NetApp 檔的紅帽企業 Linux 上的 SAP NetWeaver 的 Azure 虛擬機器高可用性](high-availability-guide-rhel-netapp-files.md)[，SUSE Linux 企業伺服器上的 Azure VM 上的 NFS 的高可用性](high-availability-guide-suse-nfs.md)，[紅帽企業 Linux 上的 Azure VM 上的 GlusterFS，用於 SAP NetWeaver](high-availability-guide-rhel-glusterfs.md)推薦 Azure 標準負載等化器  
- 2019/11/08：SAP[工作負載規劃和部署清單](sap-deployment-checklist.md)的更改，以澄清加密建議  
- 2019/11/04：在[Azure 中的 SUSE Linux 企業伺服器上設置起搏器](high-availability-guide-suse-pacemaker.md)以直接使用單播配置創建群集的更改  
- 2019 年 10 月 29 日：[在 SAP 高可用性方案中使用 Azure 標準負載等化器釋放虛擬機器的公共終結點連接](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 2019 年 10 月 25 日[：SAP HANA Azure 虛擬機器存儲配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)的更改和[SAP HANA 擴展，在 Azure VM 上使用 Azure NetApp 檔在 SUSE Linux 企業伺服器上使用備用節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)，以闡明 /hana/共用卷的 NFS 協定
- 2019 年 10 月 22 日[：SAP NetWeaver 在 SUSE Linux 企業伺服器上的 Azure VM 上為 SAP 應用程式](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)而高可用性的變化，在[SUSE Linux 企業伺服器上的 Azure VM 上 SAP NetWeaver 的高可用性，以及用於 Azure NetApp 檔的 SAP 應用程式](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)： [SUSE Linux 企業伺服器上的 Azure VM 上的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)，[在 Azure 中的 SUSE Linux 企業伺服器上設置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)，[在 SUSE Linux 企業伺服器上的 Azure VM 上使用起搏器的高可用性 IBM Db2 LUW，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)和[在 SUSE Linux 企業伺服器上的 Azure VM 上高可用性 SAP HANA，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)用於 Azure 負載等化器檢測強化
- 在[SAP HANA Azure 虛擬機器存儲配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中更改 ANF 部分和標頭部分
- 2019/10/21：在[Azure VM 上發佈 SAP HANA 橫向擴展，在 SLES 上具有 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 2019/10/16：修復[備份和恢復](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)中斷開的連結
- 2019/10/16：將推薦的最小作業系統從 SLES 12 SP3 更改為 SLES 12 SP4，在[SUSE Linux 企業伺服器上的 Azure VM 上使用起搏器的 Azure VM 上的 IBM Db2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 2019 年 10 月 11 日：更改超磁片存儲配置，並在[SAP HANA Azure 虛擬機器存儲配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中引入 ANF
- 2019 年 10 月 10 日：使用 SAP 應用程式更改[Azure 接近放置組的圖形，以實現最佳的網路延遲](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，以更清晰
- 2019 年 10 月 10 日：更改[Azure 上的 SAP HANA 基礎結構配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)，以更正有關 /hana/共用的高可用性 NFS 共用的聲明。 
- 09/28/2019：在[Azure 中的紅帽企業 Linux 上設置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)以澄清 SBD，因為 RHEL 群集不支援遮罩機制  
- 09/17/2019：NetWeaver 規劃和部署指南的更改，以統一 SAP 的 VM 擴展術語  
- 08/22/2019：在[Azure 中的 SUSE Linux 企業伺服器上設置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)以更新自訂角色創建的 URL 的更改  
- 08/16/2019：在[Azure 中的紅帽企業 Linux 上設置起搏器的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)更改，以提醒客戶在自訂角色中更新操作（如果更新到新版本的 Azure 圍欄代理）  
- 08/15/2019：SAP [HANA Azure 虛擬機器存儲配置的變化](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)，以反映超磁片（以前超 SSD）的通用可用性
- 08/01/2019：在[Azure 中的 SUSE Linux 企業伺服器上設置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)的更改，以集成專為 SLES 15 所做的更改 


