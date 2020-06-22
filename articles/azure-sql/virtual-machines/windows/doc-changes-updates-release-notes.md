---
title: Azure 虛擬機器上的 SQL Server 文件變更 | Microsoft Docs
description: 了解 Azure VM 上 SQL Server 的新功能和改善項目
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: f87d72df977e98c60948389d794eb102ac08f8d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032469"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SQL Server 文件變更
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure 可供使用內建的 SQL Server 映像部署虛擬機器 (VM)。 本文會摘要說明 [Azure 虛擬機器上 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 最近版本中與新功能和改善項目建立關聯的文件變更。 


## <a name="january-2020"></a>2020 年 1 月

| 變更 | 詳細資料 |
| --- | --- |
| **Azure Government 支援** | 您現在可針對裝載在 [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) 雲端中的虛擬機器向 SQL VM 資源提供者註冊 SQL Server 虛擬機器。 | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|變更 | 詳細資料 |
 --- | --- |
| **Azure 中的免費 DR 複本** | 若您具備[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，則可為內部部署 SQL Server 執行個體在 Azure 中裝載[免費的被動執行個體](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)以進行災害復原。 | 
| **大量資源提供者註冊** | 您現在可向資源提供者[大量註冊](sql-vm-resource-provider-bulk-register.md) SQL 虛擬機器。 | 
|**效能最佳化儲存體設定** | 您現在可在建立新的 SQL Server VM 時[完全自訂儲存體設定](storage-configuration.md#new-vms)。 |
|**FCI 進階檔案共用** | 您現在可使用[進階檔案共用](failover-cluster-instance-premium-file-share-manually-configure.md)，而非[儲存空間直接存取](failover-cluster-instance-storage-spaces-direct-manually-configure.md)的原始方法來建立容錯移轉叢集執行個體。 
| **Azure 專用主機** | 您可在 [Azure 專用主機](dedicated-host.md)上執行 SQL Server VM。 | 
| **將 SQL VM 移動到不同區域** | 使用 Azure Site Recovery [將 SQL Server VM 從一個區域遷移至另一個區域](move-sql-vm-different-region.md)。 |
|  **新的 SQL IaaS 安裝模式** | 您現在可在[輕量模式](sql-server-iaas-agent-extension-automate-management.md)中安裝 SQL Server IaaS 延伸模組來避免重新啟動 SQL Server 服務。  |
| **SQL Server 版本修改** | 您現在可變更 SQL Server VM 的[版本屬性](change-sql-server-edition.md)。 |
| **變更至 SQL VM 資源提供者** | 您可使用新的 SQL IaaS 模式來[向 SQL VM 資源提供者註冊 SQL Server VM](sql-vm-resource-provider-register.md)。 此功能包括 [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes) 映像。|
| **使用 Azure Hybrid Benefit 的自備授權映像** | 從 Azure Marketplace 部署的自備授權映像現在可[將其授權類型切換至隨用隨付](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)。| 
| **Azure 入口網站中的新 SQL Server VM 管理** | 現在有新的方式可用來在 Azure 入口網站中管理 SQL Server VM。 如需詳細資訊，請參閱[在 Azure 入口網站中管理 SQL Server VM](manage-sql-vm-portal.md)。  | 
| **SQL Server 2008/2008 R2 的延伸支援** | 將 SQL Server 2008 和 SQL Server 2008 R2「依現狀」遷移至 Azure VM 的[延伸支援](sql-server-2008-extend-end-of-support.md)。 | 
| **自訂映像的可支援性** | 您現在可將 [SQL Server IaaS 延伸模組](sql-server-iaas-agent-extension-automate-management.md#installation)安裝到自訂 OS 和 SQL 映像，其提供[彈性授權](licensing-model-azure-hybrid-benefit-ahb-change.md)的有限功能。 當向 SQL 資源提供者註冊自訂映像時，請將授權類型指定為 "AHUB"。 否則註冊將會失敗。 | 
| **具名執行個體的可支援性** | 若預設執行個體已透過適當方式解除安裝，則現在可搭配具名執行個體使用 [SQL Server IaaS 延伸模組](sql-server-iaas-agent-extension-automate-management.md#installation)。 | 
| **入口網站增強** | 部署 SQL Server VM 的 Azure 入口網站體驗現在已經過改造，以改善可用性。 如需詳細資訊，請參閱簡短的部署 SQL Server VM [快速入門](sql-vm-create-portal-quickstart.md)，以及更完整的[操作指南](create-sql-vm-portal.md)。|
| **入口網站改善** | 現在您可使用 [Azure 入口網站](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider)將 SQL Server VM 的授權模型從隨用隨付變更為自備授權。|
| **簡化使用 Azure SQL Server VM CLI 的可用性群組部署** | 現在您可透過比以往更輕鬆的方式，以在 Azure 中將可用性群組部署到 SQL Server VM。 您可使用 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) 來透過命令列建立 Windows 容錯移轉叢集、內部負載平衡器，以及可用性群組接聽程式。 如需詳細資訊，請參閱[使用 Azure SQL Server VM CLI 設定 Azure VM 上 SQL Server 的 Always On 可用性群組](availability-group-az-cli-configure.md)。 | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 變更 | 詳細資料 |
| --- | --- |
|  **SQL Server 叢集的新資源提供者** | 新資源提供者 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) 會定義 Windows 容錯移轉叢集的中繼資料。 將 SQL Server VM 加入*SqlVirtualMachineGroups* 會啟動 Windows Server 容錯移轉叢集 (WSFC) 服務，並將 VM 加入叢集。  |
| **使用 Azure 快速入門範本自動化可用性群組部署的安裝** |現在您可使用兩個 Azure 快速入門範本來建立 Windows 容錯移轉叢集、將 SQL Server VM 加入該叢集、建立接聽程式，以及設定內部負載平衡器。 如需詳細資訊，請參閱[使用 Azure 快速入門範本設定 Azure VM 上 SQL Server 的 Always On 可用性群組](availability-group-quickstart-template-configure.md)。 | 
| **自動化 SQL VM 資源提供者的註冊** | 本月之後部署的 SQL Server VM 會自動向新的 SQL Server 資源提供者註冊。 本月之前部署的 SQL Server VM 仍然需要手動註冊。 如需詳細資訊，請參閱[向 SQL VM 資源提供者註冊 Azure 中的 SQL Server 虛擬機器](sql-vm-resource-provider-register.md)。|
|**新增 SQL VM 資源提供者** |  新資源提供者 (Microsoft.SqlVirtualMachine) 可為 SQL Server VM 提供更佳的管理。 如需註冊 VM 的詳細資訊，請參閱[向 SQL VM 資源提供者註冊 Azure 中的 SQL Server 虛擬機器](sql-vm-resource-provider-register.md)。 |
|**切換授權模型** | 您現在可使用 Azure CLI 或 PowerShell 在依使用量付費與自備授權模型間進行切換。 如需詳細資訊，請參閱[如何變更 Azure 中 SQL Server 虛擬機器的授權模型](licensing-model-azure-hybrid-benefit-ahb-change.md)。 | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>其他資源

**Windows VM**：

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [佈建 SQL Server Windows VM](create-sql-vm-portal.md)
* [將資料庫遷移至 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)
* [Azure 虛擬機器中 SQL Server 的高可用性和災害復原](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 虛擬機器中的 SQL Server 效能最佳做法](performance-guidelines-best-practices.md)
* [Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](application-patterns-development-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [佈建 SQL Server Linux 虛擬機器](../linux/sql-vm-create-portal-quickstart.md)
* [常見問題集 (Linux)](../linux/frequently-asked-questions-faq.md)
* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) \(機器翻譯\)
