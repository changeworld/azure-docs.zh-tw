---
title: 使用 Azure NetApp Files 的解決方案架構 |Microsoft Docs
description: 提供使用 Azure NetApp Files 之解決方案架構的最佳作法參考。
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
ms.date: 07/30/2020
ms.author: b-juche
ms.openlocfilehash: 6437e25a2d6afc547b03f2b2144df63728116ef4
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460230"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>使用 Azure NetApp Files 的方案架構
本文提供最佳做法的參考，可協助您瞭解使用 Azure NetApp Files 的解決方案架構。  

下圖摘要說明 Azure NetApp Files 所提供的解決方案架構類別：

![解決方案架構類別](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS 應用程式和資料庫解決方案

本節提供適用于 Linux OSS 應用程式和資料庫之解決方案的參考。 

### <a name="oracle"></a>Oracle

* [使用 Azure NetApp Files 的 Oracle on Azure 部署最佳作法指南](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle VM 映射及其在 Microsoft Azure 上的部署：共用存放裝置設定選項](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [使用 Azure NetApp Files 搭配 Oracle Database 的優點](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows 應用程式和 SQL Server 解決方案

本節提供 Windows 應用程式和 SQL Server 解決方案的參考。

### <a name="file-sharing-and-global-file-caching"></a>檔案共用和全域檔案快取

* [Talon/Azure NetApp Files 部署](https://youtu.be/91LKb1qsLIM)
* [建立您自己的 Azure NFS？將 Linux 檔案共用 Wrestling 到雲端](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)

### <a name="sql-server"></a>SQL Server

* [使用 Azure NetApp Files 透過 SMB 部署 SQL Server](https://www.youtube.com/watch?v=x7udfcYbibs)
* [使用 Azure NetApp Files 透過 SMB 部署 SQL Server Always On 容錯移轉叢集](https://www.youtube.com/watch?v=zuNJ5E07e8Q)
* [使用 Azure NetApp Files 部署 Always On 可用性群組](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>Azure 上的 SAP 解決方案

本節提供 Azure 上的 SAP 解決方案的參考。 

### <a name="generic-sap-and-sap-netweaver"></a>一般 SAP 和 SAP Netweaver 

* [使用 Azure NetApp Files 的 Microsoft Azure 上的 SAP 應用程式](https://www.netapp.com/us/media/tr-4746.pdf)
* [針對 SAP 應用程式使用 Azure NetApp Files 在 SUSE Linux Enterprise Server 上的 Azure VM 達到 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Red Hat Enterprise Linux 上的 Azure Vm 上的 SAP NetWeaver 高可用性與適用于 SAP 應用程式的 Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [使用適用于 SAP 應用程式的 Azure NetApp Files （SMB）在 Windows 上的 Azure Vm 上進行 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Azure Vm 上的 SAP NetWeaver 高可用性-適用于 SAP 應用程式的 Red Hat Enterprise Linux 多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA Azure 虛擬機器儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [使用 SUSE Linux Enterprise Server 上的 Azure NetApp Files 在 Azure Vm 上以待命節點 SAP Hana 相應放大](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [使用 Red Hat Enterprise Linux 上的 Azure NetApp Files 在 Azure Vm 上以待命節點 SAP Hana 相應放大](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>SAP tech 社區和 blog 文章 

* [Azure NetApp Files-SAP Hana 備份（秒）](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files-從快照集備份還原 HANA 資料庫](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files-SAP Hana 使用雲端同步處理卸載備份](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [使用 Azure NetApp Files 加速您的 SAP Hana 系統複製](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [雲端磁片區 ONTAP 和 Azure NetApp Files：輕鬆地進行 SAP Hana 系統移轉](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>虛擬桌面基礎結構解決方案

本節提供虛擬桌面基礎結構解決方案的參考。

### <a name="windows-virtual-desktop"></a>Windows 虛擬桌面

* [Windows 虛擬桌面中 FSLogix 設定檔容器的儲存體選項](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [使用 Azure NetApp Files 建立主機集區的 FSLogix 設定檔容器](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)

## <a name="hpc-solutions"></a>HPC 解決方案

本節提供高效能運算（HPC）解決方案的參考。 

### <a name="generic-hpc"></a>一般 HPC

* [Azure NetApp Files：充分運用您的雲端儲存體](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [使用 Azure Batch 和 Azure NetApp Files 執行 MPI 工作負載](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure 週期雲端：在 Azure NetApp Files 上 CycleCloud HPC 環境](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>石油與天然氣

* [高效能運算（HPC）： Azure 中的石油與天然氣](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [在 Azure 上執行容器模擬軟體](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>電子設計自動化（EDA）

* [使用 Azure NetApp Files 進行電子設計自動化的優點](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud： EDA HPC 實驗室與 Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>分析

* [Azure NetApp Files：與 Microsoft Azure 上 SAS 格線搭配使用的新共用檔案系統](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Azure 平臺服務解決方案

本節提供 Azure 平臺服務的解決方案。 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services 和 Kubernetes

* [整合 Azure NetApp Files 與 Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [在 Azure 上使用 Azure NetApp Files 的內部 Kubernetes 效能](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident-適用于容器的儲存體協調器](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [使用 Azure Batch 和 Azure NetApp Files 執行 MPI 工作負載](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 
