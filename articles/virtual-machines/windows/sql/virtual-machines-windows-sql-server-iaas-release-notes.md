---
title: Azure 虛擬機器上的 SQL 伺服器的文檔更改*微軟文檔
description: 瞭解 Azure VM 上 SQL Server 的新功能和改進
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
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201640"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SQL 伺服器的文檔更改

Azure 允許您部署內置 SQL Server 映射的虛擬機器 （VM）。 本文總結了與[Azure 虛擬機器上 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)最近版本中的新功能和改進相關的文檔更改。 


## <a name="january-2020"></a>2020 年 1 月

| 變更 | 詳細資料 |
| --- | --- |
| **Azure 政府支援** | 現在可以將 SQL Server 虛擬機器註冊到[AZURE 政府](https://azure.microsoft.com/global-infrastructure/government/)雲中託管的虛擬機器的 SQL VM 資來源提供者。 | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|變更 | 詳細資料 |
 --- | --- |
| **Azure 中的免費 DR 副本** | 如果具有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，則可以在 Azure 中為本地 SQL Server 實例託管用於災害復原[的免費被動實例](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)。 | 
| **批量資來源提供者註冊** | 現在，您可以[批量](virtual-machines-windows-sql-bulk-register-with-resource-provider.md)向資來源提供者註冊 SQL 虛擬機器。 | 
|**性能優化的存儲配置** | 現在，您可以在創建新 SQL Server VM 時[完全自訂存儲配置](virtual-machines-windows-sql-server-storage-configuration.md#new-vms)。 |
|**FCI 的高級檔共用** | 現在，您可以使用[高級檔共用](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)而不是原始存儲空間[直接](virtual-machines-windows-portal-sql-create-failover-cluster.md)方法創建容錯移轉叢集實例。 
| **Azure 專用主機** | 您可以在[Azure 專用主機](virtual-machines-windows-sql-dedicated-host.md)上運行 SQL Server VM。 | 
| **將 SQL VM 移動到不同的區域** | 使用 Azure 網站恢復[將 SQL Server VM 從一個區域遷移到另一個區域](virtual-machines-windows-sql-move-different-region.md)。 |
|  **新的 SQL IaaS 安裝模式** | 現在可以在[羽量級模式下](virtual-machines-windows-sql-server-agent-extension.md)安裝 SQL Server IaaS 擴展，以避免重新開機 SQL Server 服務。  |
| **SQL 伺服器版本修改** | 您現在可以更改 SQL Server VM[的版本屬性](virtual-machines-windows-sql-change-edition.md)。 |
| **對 SQL VM 資來源提供者的更改** | 您可以使用新的 SQL IaaS 模式[向 SQL VM 資來源提供者註冊 SQL Server VM。](virtual-machines-windows-sql-register-with-resource-provider.md) 此功能包括[Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)映射。|
| **使用 Azure 混合權益自帶許可證映射** | 從 Azure 應用商店部署的自帶許可證映射現在可以將其[許可證類型切換到即用即付](virtual-machines-windows-sql-ahb.md#remarks)。| 
| **Azure 門戶中的新 SQL Server VM 管理** | 現在有一種在 Azure 門戶中管理 SQL Server VM 的方法。 有關詳細資訊，請參閱在[Azure 門戶中管理 SQL Server VM。](virtual-machines-windows-sql-manage-portal.md)  | 
| **對 SQL Server 2008/2008 R2 的擴展支援** | 通過*按 Azure* VM 身份遷移來擴展對 SQL Server 2008 和 SQL Server 2008 R2[的支援](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 | 
| **自訂映射支援性** | 現在，您可以將[SQL Server IaaS 擴展安裝](virtual-machines-windows-sql-server-agent-extension.md#installation)到自訂作業系統和 SQL 映射，這提供了[靈活的許可](virtual-machines-windows-sql-ahb.md)的有限功能。 向 SQL 資來源提供者註冊自訂映射時，請指定許可證類型為"AHUB"。 否則，註冊將失敗。 | 
| **命名實例可支援性** | 如果預設實例已正確卸載，則現在可以將[SQL Server IaaS 擴展](virtual-machines-windows-sql-server-agent-extension.md#installation)與命名實例一起使用。 | 
| **門戶增強** | 已對部署 SQL Server VM 的 Azure 門戶體驗進行了改進，以提高可用性。 有關詳細資訊，請參閱部署 SQL Server VM 的簡短[快速入門](quickstart-sql-vm-create-portal.md)和更全面[的方式指南](virtual-machines-windows-portal-sql-server-provision.md)。|
| **門戶改進** | 現在可以使用[Azure 門戶](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider)將 SQL Server VM 的許可模式從即用即付更改為自帶許可證。|
| **使用 Azure SQL Server VM CLI 簡化可用性組部署** | 現在，將可用性組部署到 Azure 中的 SQL Server VM 比以往任何時候都更容易。 可以使用 Azure [CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid)從命令列創建 Windows 容錯移轉叢集、內部負載平衡器和可用性組攔截器。 有關詳細資訊，請參閱使用[Azure SQL Server VM CLI 為 Azure VM 上的 SQL Server 配置始終打開的可用性組](virtual-machines-windows-sql-availability-group-cli.md)。 | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 變更 | 詳細資料 |
| --- | --- |
|  **SQL Server 群集的新資來源提供者** | 新的資來源提供者（Microsoft.SqlVirtualVm/SqlVirtualMachine 組）定義 Windows 容錯移轉叢集的中繼資料。 將 SQL Server VM 加入*SqlVirtualMachine 組*將引導 Windows 伺服器容錯移轉叢集 （WSFC） 服務並將 VM 加入群集。  |
| **使用 Azure 快速入門範本自動設置可用性組部署** |現在可以創建 Windows 容錯移轉叢集、將 SQL Server VM 加入群集、創建攔截器以及使用兩個 Azure 快速啟動範本配置內部負載等化器。 有關詳細資訊，請參閱使用[Azure 快速入門範本為 Azure VM 上的 SQL Server 配置始終打開的可用性組](virtual-machines-windows-sql-availability-group-quickstart-template.md)。 | 
| **自動註冊到 SQL VM 資來源提供者** | 本月之後部署的 SQL Server VM 會自動向新的 SQL Server 資源提供者註冊。 本月之前部署的 SQL Server VM 仍需要手動註冊。 有關詳細資訊，請參閱在[Azure 中向 SQL VM 資來源提供者註冊 SQL Server 虛擬機器](virtual-machines-windows-sql-register-with-resource-provider.md)。|
|**新增 SQL VM 資源提供者** |  新的資來源提供者 （Microsoft.SqlVirtualMachine） 可以更好地管理 SQL Server VM。 有關註冊 VM 的詳細資訊，請參閱[在 Azure 中向 SQL VM 資來源提供者註冊 SQL Server 虛擬機器](virtual-machines-windows-sql-register-with-resource-provider.md)。 |
|**切換授權模型** | 現在，可以使用 Azure CLI 或 PowerShell 在 SQL Server VM 的按使用量付費和自帶許可證模型之間切換。 有關詳細資訊，請參閱[如何更改 Azure 中的 SQL Server 虛擬機器的許可模型](virtual-machines-windows-sql-ahb.md)。 | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>其他資源

**視窗 VM**：

* [Windows VM 上的 SQL 伺服器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [佈建 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [將資料庫移轉到 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虛擬機器上的 SQL Server 的高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虛擬機器上的 SQL Server 的性能最佳實踐](virtual-machines-windows-sql-performance.md)
* [Azure 虛擬機器上 SQL Server 的應用程式模式和開發策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [預配 SQL Server Linux 虛擬機器](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常見問題集 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux 文檔上的 SQL 伺服器](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
