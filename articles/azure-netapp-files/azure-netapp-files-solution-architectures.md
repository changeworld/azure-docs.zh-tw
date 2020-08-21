---
title: 使用 Azure NetApp Files 的解決方案架構 |Microsoft Docs
description: 針對使用 Azure NetApp Files 的解決方案架構，提供最佳作法的參考。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: b-juche
ms.openlocfilehash: e7bdf6ce35e2649a068de234b4b52e13efe72c44
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690539"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>使用 Azure NetApp Files 的方案架構
本文提供最佳作法的參考，可協助您瞭解使用 Azure NetApp Files 的解決方案架構。  

下圖摘要說明 Azure NetApp Files 提供的解決方案架構類別：

![解決方案架構類別](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS 應用程式和資料庫解決方案

本節提供 Linux OSS 應用程式和資料庫的解決方案參考。 

### <a name="oracle"></a>Oracle

* [使用 Azure NetApp Files 的 Azure 上的 Oracle 部署最佳做法指南](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle VM 映射及其在 Microsoft Azure 上的部署：共用存放裝置設定選項](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [使用 Azure NetApp Files 搭配 Oracle Database 的優點](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows 應用程式和 SQL Server 解決方案

本節提供 Windows 應用程式和 SQL Server 解決方案的參考。

### <a name="file-sharing-and-global-file-caching"></a>檔案共用和全域檔案快取

* [建立您自己的 Azure NFS？將 Linux 檔案共用 Wrestling 到雲端](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [全域檔案快取/Azure NetApp Files 部署](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [透過 Azure NetApp Files 部署透過 SMB 的 SQL Server](https://www.youtube.com/watch?v=x7udfcYbibs)
* [使用 Azure NetApp Files 透過 SMB 部署 SQL Server Always On 容錯移轉叢集](https://www.youtube.com/watch?v=zuNJ5E07e8Q)
* [使用 Azure NetApp Files 部署 Always On 可用性群組](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>Azure 上的 SAP 解決方案

本節提供 Azure 上的 SAP 解決方案的參考。 

### <a name="generic-sap-and-sap-netweaver"></a>一般 SAP 和 SAP Netweaver 

* [使用 Azure NetApp Files Microsoft Azure 上的 SAP 應用程式](https://www.netapp.com/us/media/tr-4746.pdf)
* [針對 SAP 應用程式使用 Azure NetApp Files 在 SUSE Linux Enterprise Server 上的 Azure VM 達到 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Azure Vm 上的 SAP NetWeaver 的高可用性，適用于 SAP 應用程式的 Azure NetApp Files Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Sap NetWeaver 在 Windows 上的 Azure Vm 上的高可用性，適用于 SAP 應用程式的 Azure NetApp Files (SMB) ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Azure Vm 上的 SAP NetWeaver 的高可用性，適用于 SAP 應用程式的多 SID 指南 Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA Azure 虛擬機器儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SAP Hana Red Hat Enterprise Linux 上的 Azure NetApp Files 向上擴充的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat)
* [在 SUSE Linux Enterprise Server 上搭配 Azure NetApp Files 使用 Azure Vm 上的待命節點進行相應放大 SAP Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [在 Red Hat Enterprise Linux 上搭配 Azure NetApp Files 使用 Azure Vm 上的待命節點進行相應放大 SAP Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>SAP tech 社區和 blog 文章 

* [Azure NetApp Files-SAP Hana 備份（以秒為單位）](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files-從快照集備份還原您的 HANA 資料庫](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files-SAP Hana 卸載雲端同步的備份](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [使用 Azure NetApp Files 加速 SAP Hana 系統複製](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [雲端磁片區 ONTAP 和 Azure NetApp Files：簡化了 SAP Hana 系統移轉](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>虛擬桌面基礎結構解決方案

本節提供虛擬桌面基礎結構解決方案的參考。

### <a name="windows-virtual-desktop"></a>Windows 虛擬桌面

* [搭配使用 Azure NetApp Files 和 Windows 虛擬桌面的優點](solutions-windows-virtual-desktop.md)
* [在 Windows 虛擬桌面中 FSLogix 設定檔容器的儲存體選項](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [使用 Azure NetApp Files 建立主機集區的 FSLogix 設定檔容器](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)
* [企業規模的 Windows 虛擬桌面](https://docs.microsoft.com/azure/architecture/example-scenario/wvd/windows-virtual-desktop)

## <a name="hpc-solutions"></a>HPC 解決方案

本節提供高效能運算 (HPC) 解決方案的參考。 

### <a name="generic-hpc"></a>一般 HPC

* [Azure NetApp Files：充分利用您的雲端儲存體](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [使用 Azure Batch 和 Azure NetApp Files 執行 MPI 工作負載](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure 週期雲端： Azure NetApp Files 上的 CycleCloud HPC 環境](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>石油與天然氣

* [高效能計算 (HPC) ： Azure 中的石油和天然氣](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [在 Azure 上執行容器模擬軟體](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>電子設計自動化 (EDA) 

* [使用 Azure NetApp Files 進行電子設計自動化的優點](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud：使用 Azure NetApp Files 的 EDA HPC Lab](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>分析

* [Azure NetApp Files：用於 Microsoft Azure 上 SAS 方格的新共用檔案系統](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Azure 平臺服務解決方案

本節提供 Azure 平臺服務的解決方案。 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services 和 Kubernetes

* [整合 Azure NetApp Files 與 Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [在 Azure 上使用 Azure NetApp Files 的全球 Kubernetes 效能](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident-適用于容器的儲存體協調器](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [使用 Azure Batch 和 Azure NetApp Files 執行 MPI 工作負載](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 
