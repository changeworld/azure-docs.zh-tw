---
title: 使用 Azure NetApp 檔的解決方案體系結構 |微軟文檔
description: 提供使用 Azure NetApp 檔的解決方案體系結構的最佳做法的引用。
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
ms.date: 03/27/2020
ms.author: b-juche
ms.openlocfilehash: 8eae528c965e599e7adfb546a09b0d5879e7c54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369533"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>使用 Azure NetApp Files 的方案架構
本文提供了最佳做法的參考，這些最佳實踐可以説明您瞭解使用 Azure NetApp 檔的解決方案體系結構。  

## <a name="azure-high-performance-computing-hpc-solutions"></a>Azure 高性能計算 （HPC） 解決方案

* [在 Azure 上運行儲層模擬軟體](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)
* [使用 Azure 批次處理和 Azure NetApp 檔運行 MPI 工作負荷](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)

## <a name="azure-kubernetes-service-aks-solutions"></a>Azure 庫伯奈斯服務 （AKS） 解決方案

* [Azure NetApp 檔與 AKS 集成](https://github.com/andyzhangx/demo/tree/master/linux/nfs)
* [將 Azure NetApp 檔與 Azure 庫伯奈斯服務集成](https://docs.microsoft.com/azure/aks/azure-netapp-files)

## <a name="oracle-database-solutions"></a>Oracle 資料庫解決方案

* [使用 Azure NetApp 檔在 Azure 部署上的 Oracle 最佳實踐指南](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle VM 映射及其在 Microsoft Azure 上的部署：共用存儲配置選項](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)

## <a name="sap-application-solutions"></a>SAP 應用解決方案 

* [使用 Azure NetApp 檔在 Microsoft Azure 上的 SAP 應用程式](https://www.netapp.com/us/media/tr-4746.pdf)
* [SAP HANA Azure 虛擬機器儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SUSE Linux 企業伺服器上的 Azure VM 上的 SAP NetWeaver 的高可用性，具有用於 SAP 應用程式的 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [用於 SAP 應用程式的 Azure NetApp 檔在紅帽企業 Linux 上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [用於 SAP 應用程式多 SID 指南的紅帽企業 Linux 上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [適用于 SAP 應用程式的 Windows 上 Azure VM 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [SAP HANA 擴展，在 Azure VM 上使用備用節點，在 SUSE Linux 企業伺服器上使用 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [SAP HANA 擴展，在 Azure VM 上使用備用節點，在 RedHat 企業 Linux 上使用 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
* [Azure NetApp 檔 – SAP HANA 備份，在幾秒鐘內](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp 檔 – 從快照備份還原 HANA 資料庫](https://blog.netapp.com/azure-netapp-files-backup-sap-hana/)
* [Azure NetApp 檔 – SAP HANA 卸載具有雲同步的備份](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [使用 Azure NetApp 檔加快 SAP HANA 系統副本](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [雲卷 ONTAP 和 Azure NetApp 檔：SAP HANA 系統移轉變得簡單](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/) 

## <a name="global-file-caching-solutions"></a>通用檔案緩存解決方案

* [與 Azure NetApp 檔和 Talon FAST 全域分發的企業檔共用™](https://www.talonstorage.com/products/azure-netapp-files)

## <a name="windows-virtual-desktopvdi-solutions"></a>Windows 虛擬桌面/VDI 解決方案

* [使用 Azure NetApp 檔為主機池創建 FSLogix 設定檔容器](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)

