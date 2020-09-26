---
title: 使用 IaaS 代理程式延伸模組自動化管理工作
description: 本文描述如何管理 SQL Server IaaS 代理程式延伸模組，這會自動化特定的 SQL Server 管理工作。 這些工作包括自動備份、自動修補和 Azure Key Vault 整合。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 00dfcad351348ed4ca4f08289e76e85a089c5d86
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272237"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>使用 SQL Server IaaS 代理程式延伸模組自動化 Azure 虛擬機器的管理工作
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 代理程式擴充功能 (SqlIaasExtension) 會在 Azure 虛擬機器上執行，以將管理工作自動化。 本文提供延伸模組支援服務的概觀。 本文也提供安裝、狀態及移除延伸模組的指示。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

如需檢視本文的傳統版本，請參閱 [SQL Server VM 的 SQL Server IaaS 代理程式延伸模組 (傳統)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)。


## <a name="supported-services"></a>支援的服務
SQL Server IaaS 代理程式擴充功能支援下列管理工作︰

| 系統管理功能 | 描述 |
| --- | --- |
| **SQL Server 自動備份** |為預設執行個體或在 VM 上[正確安裝](frequently-asked-questions-faq.md#administration)的 SQL Server 具名執行個體，自動化所有資料庫的備份排程。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動備份 (Resource Manager)](automated-backup-sql-2014.md)。 |
| **SQL Server 自動修補** |設定維護期間 (在此期間會進行 VM 的重要 Windows 更新)，以避免在工作負載尖峰時段進行更新。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動修補 (Resource Manager)](automated-patching.md)。 |
| **Azure Key Vault 整合** |讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。 如需詳細資訊，請參閱[在 Azure 虛擬機器上設定 SQL Server 的 Azure Key Vault 整合 (Resource Manager)](azure-key-vault-integration-configure.md)。 |

安裝並執行 SQL Server IaaS 代理程式延伸模組之後，即可使用系統管理功能：

* 在 Azure 入口網站中虛擬機器的 [SQL Server] 面板上，以及在 Azure Marketplace 上透過 SQL Server 映像的 Azure PowerShell。
* 透過 Azure PowerShell 手動安裝延伸模組。 

## <a name="prerequisites"></a>必要條件
在 VM 上使用 SQL Server IaaS 代理程式延伸模組的需求如下：

**作業系統**：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server 版本**：

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**：

* [下載及設定最新的 Azure PowerShell 命令](/powershell/azure/)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>安裝
當您向 [SQL SERVER vm 資源提供者](sql-vm-resource-provider-register.md)註冊 SQL Server VM 時，會安裝 SQL Server IaaS 擴充功能。 如有需要，您可使用以下 PowerShell 命令來手動安裝 SQL Server IaaS 代理程式： 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> 安裝延伸模組會重新啟動 SQL Server 服務。 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>安裝在只有單一具名 SQL Server 執行個體的 VM 上
如已解除安裝預設執行個體並重新安裝 IaaS 延伸模組，則 SQL Server IaaS 延伸模組會在 SQL Server 上使用具名執行個體。

若要使用 SQL Server 的具名執行個體，請遵循下列步驟：
   1. 從 Azure Marketplace 部署 SQL Server VM。 
   1. 從 [Azure 入口網站](https://portal.azure.com)解除安裝 IaaS 延伸模組。
   1. 完全解除安裝 SQL Server VM 內的 SQL Server。
   1. 使用 SQL Server VM 中的具名執行個體來安裝 SQL Server。 
   1. 從 Azure 入口網站解除安裝 IaaS 延伸模組。  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>取得 SQL Server IaaS 延伸模組的狀態
其中一項驗證已安裝擴充功能的方法，是在 Azure 入口網站中檢視代理程式狀態。 選取虛擬機器視窗中的 [所有設定]，然後選取 [延伸模組]。 您應該會看到列出 **SqlIaasExtension** 擴充功能。

![Azure 入口網站中的 SQL Server IaaS 代理程式延伸模組狀態](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

您也可以使用 **Get-AzVMSqlServerExtension** Azure PowerShell Cmdlet：

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

前一個命令確認已安裝代理程式，並提供一般的狀態資訊。 使用下列命令可取得自動備份和修補的特定狀態資訊：

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>移除
在 Azure 入口網站中，您可選取虛擬機器屬性其 [延伸模組] 視窗中的省略符號，以解除安裝延伸模組。 然後選取 [刪除]。

![解除安裝 Azure 入口網站中的 SQL Server IaaS 代理程式延伸模組](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

您也可以使用 **Remove-AzVMSqlServerExtension** PowerShell Cmdlet：

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>後續步驟
開始使用延伸模組支援的其中一項服務。 如需詳細資訊，請參閱本文＜支援的服務＞[](#supported-services)一節中所參考的文章。

如需在 Azure 虛擬機器上執行 SQL Server 的詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 是什麼](sql-server-on-azure-vm-iaas-what-is-overview.md)。
