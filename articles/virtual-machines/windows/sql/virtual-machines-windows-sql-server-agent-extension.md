---
title: 使用 IaaS 代理擴展自動執行管理工作
description: 本文介紹如何管理 SQL Server IaaS 代理擴展，該擴展自動執行特定的 SQL Server 管理工作。 其中包括自動備份、自動修補和 Azure 金鑰保存庫集成。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030773"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>使用 SQL Server IaaS 代理擴展自動執行 Azure 虛擬機器上的管理工作
> [!div class="op_single_selector"]
> * [資源管理器](virtual-machines-windows-sql-server-agent-extension.md)
> * [經典](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 代理程式擴充功能 (SqlIaasExtension) 會在 Azure 虛擬機器上執行，以將管理工作自動化。 本文概述了擴展支援的服務。 本文還提供有關安裝、狀態和刪除擴展的說明。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

要查看本文的經典版本，請參閱[SQL Server 服務器 VM 的 SQL Server IaaS 代理擴展（經典）。](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)


## <a name="supported-services"></a>支援的服務
SQL Server IaaS 代理程式擴充功能支援下列管理工作︰

| 系統管理功能 | 描述 |
| --- | --- |
| **SQL 伺服器自動備份** |自動為預設實例或 VM 上[正確安裝](virtual-machines-windows-sql-server-iaas-faq.md#administration)的 SQL Server 命名實例的所有資料庫的備份計畫。 有關詳細資訊，請參閱[Azure 虛擬機器（資源管理器）中的 SQL Server 的自動備份](virtual-machines-windows-sql-automated-backup.md)。 |
| **SQL 伺服器自動修補** |設定維護期間 (在此期間會進行 VM 的重要 Windows 更新)，以避免在工作負載尖峰時段進行更新。 有關詳細資訊，請參閱[Azure 虛擬機器（資源管理器）中的 SQL Server 的自動修補](virtual-machines-windows-sql-automated-patching.md)。 |
| **Azure 金鑰保存庫整合** |讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。 有關詳細資訊，請參閱為[Azure 虛擬機器（資源管理器）上的 SQL Server 配置 Azure 金鑰保存庫集成](virtual-machines-windows-ps-sql-keyvault.md)。 |

安裝並運行 SQL Server Iaas 代理擴展後，它使管理功能可用：

* 在 Azure 門戶中的虛擬機器的 SQL Server 面板上，並通過 Azure 應用商店上的 SQL Server 映射通過 Azure PowerShell 執行 SQL Server 映射。
* 通過 Azure PowerShell 手動安裝擴展。 

## <a name="prerequisites"></a>Prerequisites
以下是在 VM 上使用 SQL Server IaaS 代理擴展的要求：

**作業系統**：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL 伺服器版本**：

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure 電源外殼**：

* [下載並配置最新的 Azure PowerShell 命令](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>安裝
當您向 SQL [VM 資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊 SQL Server VM 時，將安裝 SQL Server IaaS 擴展。 如有必要，可以使用以下 PowerShell 命令手動安裝 SQL Server IaaS 代理： 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> 安裝擴展將重新開機 SQL Server 服務。 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>使用單個命名 SQL Server 實例在 VM 上安裝
如果卸載了預設實例並重新安裝了 IaaS 擴展，SQL Server IaaS 擴展將使用 SQL Server 上的命名實例。

要使用 SQL Server 的命名實例，請按照以下步驟操作：
   1. 從 Azure 應用商店部署 SQL Server VM。 
   1. 從[Azure 門戶](https://portal.azure.com)卸載 IaaS 擴展。
   1. 完全在 SQL Server VM 中卸載 SQL 伺服器。
   1. 在 SQL Server VM 中安裝具有命名實例的 SQL Server。 
   1. 從 Azure 門戶安裝 IaaS 擴展。  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>獲取 SQL Server IaaS 擴展的狀態
其中一項驗證已安裝擴充功能的方法，是在 Azure 入口網站中檢視代理程式狀態。 選擇虛擬機器視窗中**的所有設置**，然後選擇 **"擴展**"。 您應該會看到列出 **SqlIaasExtension** 擴充功能。

![Azure 門戶中的 SQL Server IaaS 代理擴展的狀態](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

您還可以使用**獲取-AzVMSqlServer 擴展**Azure 電源外殼 Cmdlet：

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

前面的命令確認代理已安裝並提供常規狀態資訊。 您可以使用以下命令獲取有關自動備份和修補的特定狀態資訊：

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>移除
在 Azure 門戶中，可以通過在虛擬機器屬性的**擴展**視窗中選擇省略號來卸載擴展。 然後選取 [刪除]****。

![在 Azure 門戶中卸載 SQL Server IaaS 代理擴展](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

您還可以使用**刪除-AzVMSqlServer 擴展**電源外殼 Cmdlet：

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>後續步驟
開始使用擴展支援的服務之一。 有關詳細資訊，請參閱本文的["支援服務](#supported-services)"部分中引用的文章。

有關在 Azure 虛擬機器上運行 SQL Server 的詳細資訊，請參閱[Azure 虛擬機器上的 SQL Server 是什麼？](virtual-machines-windows-sql-server-iaas-overview.md)
