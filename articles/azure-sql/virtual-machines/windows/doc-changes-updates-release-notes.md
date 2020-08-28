---
title: Azure 虛擬機器上的 SQL Server 文件變更 | Microsoft Docs
description: 瞭解 Azure 虛擬機器上不同版本 SQL Server 的新功能和增強功能。
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.openlocfilehash: 5f0ba27a0964f08b32e1af19d6f578b3f41ee300
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006409"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SQL Server 文件變更
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure 可供使用內建的 SQL Server 映像部署虛擬機器 (VM)。 本文會摘要說明 [Azure 虛擬機器上 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 最近版本中與新功能和改善項目建立關聯的文件變更。 

## <a name="august-2020"></a>2020 年 8 月

| 變更 | 詳細資料 |
| --- | --- |
| **在入口網站中設定 ag** | 您現在可以透過 [Azure 入口網站設定您的可用性群組](availability-group-azure-portal-configure.md)。 這項功能目前處於預覽狀態，正在部署中，因此如果您想要的區域無法使用，請稍後再回來查看。 | 


## <a name="july-2020"></a>2020 年 7 月


| 變更 | 詳細資料 |
| --- | --- |
| **將記錄遷移至 ultra 磁片** | 瞭解如何將 [記錄檔遷移至 ultra 磁片](storage-migrate-to-ultradisk.md) ，以利用高效能和低延遲。 | 
| **使用 PowerShell 建立 AG** | 您現在可以使用 [PowerShell](availability-group-az-commandline-configure.md) 和 Azure CLI 來簡化可用性群組的建立。 | 


## <a name="june-2020"></a>2020 年 6 月

| 變更 | 詳細資料 |
| --- | --- |
| **分散式網路名稱 (DNN) ** | Windows Server 2016 + 上的 SQL Server 2019 現在支援使用 [分散式網路名稱](hadr-distributed-network-name-dnn-configure.md) （而不是使用 Azure Load Balancer）將流量路由傳送至容錯移轉叢集實例 (FCI) 。 這項支援可簡化和簡化 Azure 中的高可用性 (HA) 解決方案的連接。 | 
| **使用 Azure 共用磁片的 FCI** | 您現在可以使用[Azure 共用磁片](failover-cluster-instance-azure-shared-disks-manually-configure.md)，將[容錯移轉叢集實例部署 (FCI) ](failover-cluster-instance-overview.md) 。 |
| **重新組織 FCI 檔** | 在 [Azure vm 上使用 SQL Server 的容錯移轉叢集實例](failover-cluster-instance-overview.md) 相關檔已重新撰寫並重新組織，以清楚瞭解。 我們已分隔某些設定內容，例如叢集設定的 [最佳作法](hadr-cluster-best-practices.md)、如何準備 [SQL Server FCI 的虛擬機器](failover-cluster-instance-prepare-vm.md)，以及如何設定 [Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md)。 | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>2020 年 5 月 

| 變更 | 詳細資料 |
| --- | --- |
| **Azure SQL 系列** | Azure 虛擬機器上的 SQL Server 現在是 [AZURE SQL 系列產品](../../azure-sql-iaas-vs-paas-what-is-overview.md)的一部分。 查看我們的 [新外觀](../index.yml)！ 產品中沒有任何變更，但檔的目標是讓 Azure SQL 產品的決策更容易。 | 


## <a name="january-2020"></a>2020 年 1 月

| 變更 | 詳細資料 |
| --- | --- |
| **Azure Government 支援** | 您現在可以向 [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) 雲端中裝載的虛擬機器，使用 SQL VM 資源提供者註冊 SQL Server 的虛擬機器。 | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|變更 | 詳細資料 |
 --- | --- |
| **Azure 中的免費 DR 複本** | 若您具備[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，則可為內部部署 SQL Server 執行個體在 Azure 中裝載[免費的被動執行個體](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)以進行災害復原。 | 
| **大量資源提供者註冊** | 您現在可以使用資源提供者來 [大量註冊](sql-vm-resource-provider-bulk-register.md) SQL Server 的虛擬機器。 | 
|**效能優化儲存體設定** | 您現在可在建立新的 SQL Server VM 時[完全自訂儲存體設定](storage-configuration.md#new-vms)。 |
|**FCI 進階檔案共用** | 您現在可以使用 [Premium 檔案共用](failover-cluster-instance-premium-file-share-manually-configure.md) ，而不是原始的 [儲存空間直接存取](failover-cluster-instance-storage-spaces-direct-manually-configure.md)方法來建立容錯移轉叢集實例。 
| **Azure 專用主機** | 您可以在 [Azure 專用主機](dedicated-host.md)上執行 SQL Server VM。 | 
| **將 VM 遷移至不同區域 SQL Server** | 使用 Azure Site Recovery [將 SQL Server VM 從一個區域遷移至另一個區域](move-sql-vm-different-region.md)。 |
|  **新的 SQL IaaS 安裝模式** | 您現在可在[輕量模式](sql-server-iaas-agent-extension-automate-management.md)中安裝 SQL Server IaaS 延伸模組來避免重新啟動 SQL Server 服務。  |
| **SQL Server 版本修改** | 您現在可變更 SQL Server VM 的[版本屬性](change-sql-server-edition.md)。 |
| **SQL VM 資源提供者的變更** | 您可使用新的 SQL IaaS 模式來[向 SQL VM 資源提供者註冊 SQL Server VM](sql-vm-resource-provider-register.md)。 此功能包括 [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes) 映像。|
| **使用 Azure Hybrid Benefit 的自備授權映像** | 從 Azure Marketplace 部署的自備授權映像現在可[將其授權類型切換至隨用隨付](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)。| 
| **Azure 入口網站中的新 SQL Server VM 管理** | 現在有新的方式可用來在 Azure 入口網站中管理 SQL Server VM。 如需詳細資訊，請參閱[在 Azure 入口網站中管理 SQL Server VM](manage-sql-vm-portal.md)。  | 
| **SQL Server 2008 和 2008 R2 的延伸支援** | 將 SQL Server 2008 和 SQL Server 2008 R2「依現狀」遷移至 Azure VM 的[延伸支援](sql-server-2008-extend-end-of-support.md)。 | 
| **自訂映像的可支援性** | 您現在可以將 [SQL Server IaaS 延伸](sql-server-iaas-agent-extension-automate-management.md#installation) 模組安裝到自訂 OS 和 SQL Server 映射，以提供 [彈性授權](licensing-model-azure-hybrid-benefit-ahb-change.md)的有限功能。 當您要向 SQL VM 資源提供者註冊自訂映射時，請將授權類型指定為 "AHUB"。 否則註冊將會失敗。 | 
| **具名執行個體的可支援性** | 若預設執行個體已透過適當方式解除安裝，則現在可搭配具名執行個體使用 [SQL Server IaaS 延伸模組](sql-server-iaas-agent-extension-automate-management.md#installation)。 | 
| **入口網站增強** | 部署 SQL Server VM 的 Azure 入口網站體驗現在已經過改造，以改善可用性。 如需詳細資訊，請參閱簡短的部署 SQL Server VM [快速入門](sql-vm-create-portal-quickstart.md)，以及更完整的[操作指南](create-sql-vm-portal.md)。|
| **入口網站改善** | 現在您可使用 [Azure 入口網站](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider)將 SQL Server VM 的授權模型從隨用隨付變更為自備授權。|
| **透過 Azure CLI 簡化可用性群組至 SQL Server VM 的部署** | 現在您可透過比以往更輕鬆的方式，以在 Azure 中將可用性群組部署到 SQL Server VM。 您可以使用 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) 從命令列建立 Windows 容錯移轉叢集、內部負載平衡器和可用性群組接聽程式。 如需詳細資訊，請參閱 [使用 Azure CLI 為 AZURE VM 上的 SQL Server 設定 Always On 可用性群組](availability-group-az-cli-configure.md)。 | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 變更 | 詳細資料 |
| --- | --- |
|  **SQL Server 叢集的新資源提供者** | 新資源提供者 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) 會定義 Windows 容錯移轉叢集的中繼資料。 將 SQL Server VM 加入*SqlVirtualMachineGroups* 會啟動 Windows Server 容錯移轉叢集 (WSFC) 服務，並將 VM 加入叢集。  |
| **使用 Azure 快速入門範本自動化可用性群組部署的安裝** |您現在可以使用兩個 Azure 快速入門範本，建立 Windows 容錯移轉叢集、加入 SQL Server Vm、建立接聽程式，並設定內部負載平衡器。 如需詳細資訊，請參閱[使用 Azure 快速入門範本設定 Azure VM 上 SQL Server 的 Always On 可用性群組](availability-group-quickstart-template-configure.md)。 | 
| **自動化 SQL VM 資源提供者的註冊** | 在本月之後部署的 SQL Server Vm 會自動向新的 SQL VM 資源提供者註冊。 本月之前部署的 SQL Server VM 仍然需要手動註冊。 如需詳細資訊，請參閱[向 SQL VM 資源提供者註冊 Azure 中的 SQL Server 虛擬機器](sql-vm-resource-provider-register.md)。|
|**新增 SQL VM 資源提供者** |  新資源提供者 (Microsoft.SqlVirtualMachine) 可為 SQL Server VM 提供更佳的管理。 如需註冊 VM 的詳細資訊，請參閱[向 SQL VM 資源提供者註冊 Azure 中的 SQL Server 虛擬機器](sql-vm-resource-provider-register.md)。 |
|**切換授權模型** | 您現在可使用 Azure CLI 或 PowerShell 在依使用量付費與自備授權模型間進行切換。 如需詳細資訊，請參閱[如何變更 Azure 中 SQL Server 虛擬機器的授權模型](licensing-model-azure-hybrid-benefit-ahb-change.md)。 | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>其他資源

**Windows VM**：

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [在 Windows VM 上布建 SQL Server](create-sql-vm-portal.md)
* [將資料庫移轉至 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)
* [Azure 虛擬機器中 SQL Server 的高可用性和災害復原](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 虛擬機器中的 SQL Server 效能最佳做法](performance-guidelines-best-practices.md)
* [Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](application-patterns-development-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [在 Linux 虛擬機器上布建 SQL Server](../linux/sql-vm-create-portal-quickstart.md)
* [常見問題集 (Linux)](../linux/frequently-asked-questions-faq.md)
* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) \(機器翻譯\)
