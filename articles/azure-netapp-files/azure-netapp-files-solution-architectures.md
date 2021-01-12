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
ms.date: 01/11/2021
ms.author: b-juche
ms.openlocfilehash: a66b5d59415aad7abf3e8dc95c7498b5d774d640
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071267"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>使用 Azure NetApp Files 的方案架構
本文提供最佳作法的參考，可協助您瞭解使用 Azure NetApp Files 的解決方案架構。  

下圖摘要說明 Azure NetApp Files 提供的解決方案架構類別：

![解決方案架構類別](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS 應用程式和資料庫解決方案

本節提供 Linux OSS 應用程式和資料庫的解決方案參考。 

### <a name="oracle"></a>Oracle

* [Azure NetApp Files 單一磁碟區上的 Oracle 資料庫效能](performance-oracle-single-volumes.md)
* [使用 Azure NetApp Files 的 Azure 上的 Oracle 部署最佳做法指南](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle VM 映射及其在 Microsoft Azure 上的部署：共用存放裝置設定選項](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [使用 Azure NetApp Files 搭配 Oracle Database 的優點](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>機器學習
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows 應用程式和 SQL Server 解決方案

本節提供 Windows 應用程式和 SQL Server 解決方案的參考。

### <a name="file-sharing-and-global-file-caching"></a>檔案共用和全域檔案快取

* [建立您自己的 Azure NFS？將 Linux 檔案共用 Wrestling 到雲端](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [全域檔案快取/Azure NetApp Files 部署](https://youtu.be/91LKb1qsLIM)
* [Azure NetApp Files 的雲端合規性](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [透過 Azure NetApp Files 部署透過 SMB 的 SQL Server](https://www.youtube.com/watch?v=x7udfcYbibs)
<!-- * [Deploy SQL Server Always-On Failover Cluster over SMB with Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q) --> 
<!-- * [Deploy Always-On Availability Groups with Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc) --> 

## <a name="sap-on-azure-solutions"></a>Azure 上的 SAP 解決方案

本節提供 Azure 上的 SAP 解決方案的參考。 

### <a name="generic-sap-and-sap-netweaver"></a>一般 SAP 和 SAP Netweaver 

* [使用 Azure NetApp Files Microsoft Azure 上的 SAP 應用程式](https://www.netapp.com/us/media/tr-4746.pdf)
* [針對 SAP 應用程式使用 Azure NetApp Files 在 SUSE Linux Enterprise Server 上的 Azure VM 達到 SAP NetWeaver 高可用性](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Azure Vm 上的 SAP NetWeaver 的高可用性，適用于 SAP 應用程式的 Azure NetApp Files Red Hat Enterprise Linux](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [Sap NetWeaver 在 Windows 上的 Azure Vm 上的高可用性，適用于 SAP 應用程式的 Azure NetApp Files (SMB) ](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Azure Vm 上的 SAP NetWeaver 的高可用性，適用于 SAP 應用程式的多 SID 指南 Red Hat Enterprise Linux](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA Azure 虛擬機器儲存體設定](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [SAP Hana Red Hat Enterprise Linux 上的 Azure NetApp Files 向上擴充的高可用性](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [在 SUSE Linux Enterprise Server 上搭配 Azure NetApp Files 使用 Azure Vm 上的待命節點進行相應放大 SAP Hana](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [在 Red Hat Enterprise Linux 上搭配 Azure NetApp Files 使用 Azure Vm 上的待命節點進行相應放大 SAP Hana](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)

### <a name="sap-iq-nls"></a>SAP IQ-NLS
*   [在 SUSE Linux Enterprise Server 上使用 Azure NetApp Files 部署 SAP IQ-NLS HA 解決方案](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)

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
* [在 Windows 虛擬桌面中 FSLogix 設定檔容器的儲存體選項](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [使用 Azure NetApp Files 建立主機集區的 FSLogix 設定檔容器](../virtual-desktop/create-fslogix-profile-container.md)
* [企業規模的 Windows 虛擬桌面](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [適用于企業的 Microsoft FSLogix-Azure NetApp Files 最佳作法](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [設定 MSIX 應用程式附加的 Azure NetApp Files](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

## <a name="hpc-solutions"></a>HPC 解決方案

本節提供高效能運算 (HPC) 解決方案的參考。 

### <a name="generic-hpc"></a>一般 HPC

* [Azure NetApp Files：充分利用您的雲端儲存體](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [使用 Azure Batch 和 Azure NetApp Files 執行 MPI 工作負載](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure 週期雲端： Azure NetApp Files 上的 CycleCloud HPC 環境](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>石油與天然氣

* [高效能計算 (HPC) ： Azure 中的石油和天然氣](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [在 Azure 上執行容器模擬軟體](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>電子設計自動化 (EDA) 

* [使用 Azure NetApp Files 進行電子設計自動化的優點](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud：使用 Azure NetApp Files 的 EDA HPC Lab](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [適用于半導體產業的 Azure](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>分析

* [Azure NetApp Files：用於 Microsoft Azure 上 SAS 方格的共用檔案系統](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [搭配使用 Microsoft Azure 與 SAS®的最佳作法](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

## <a name="azure-platform-services-solutions"></a>Azure 平臺服務解決方案

本節提供 Azure 平臺服務的解決方案。 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services 和 Kubernetes

* [整合 Azure NetApp Files 與 Azure Kubernetes Service](../aks/azure-netapp-files.md)
* [在 Azure 上使用 Azure NetApp Files 的全球 Kubernetes 效能](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident-適用于容器的儲存體協調器](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Azure Kubernetes Service (AKS) 的 Magento 電子商務平臺 ](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-batch"></a>Azure Batch

* [使用 Azure Batch 和 Azure NetApp Files 執行 MPI 工作負載](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
