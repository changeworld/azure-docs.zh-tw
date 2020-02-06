---
title: 使用 IaaS 代理程式擴充功能將管理工作自動化
description: 本文說明如何管理 SQL Server IaaS 代理程式擴充功能，以將特定的 SQL Server 管理工作自動化。 這些包括自動備份、自動修補和 Azure Key Vault 整合。
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
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030773"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>使用 SQL Server IaaS 代理程式擴充功能，將 Azure 虛擬機器上的管理工作自動化
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [傳統](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 代理程式擴充功能 (SqlIaasExtension) 會在 Azure 虛擬機器上執行，以將管理工作自動化。 本文提供延伸模組支援之服務的總覽。 本文也提供安裝、狀態及移除擴充功能的指示。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

若要查看本文的傳統版本，請參閱[SQL Server vm 的 SQL Server IaaS 代理程式擴充功能（傳統）](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)。


## <a name="supported-services"></a>支援的服務
SQL Server IaaS 代理程式擴充功能支援下列管理工作︰

| 系統管理功能 | 描述 |
| --- | --- |
| **SQL Server 自動備份** |針對預設實例或虛擬機器上[已正確安裝](virtual-machines-windows-sql-server-iaas-faq.md#administration)之 SQL Server 實例的所有資料庫，自動排程備份。 如需詳細資訊，請參閱[Azure 虛擬機器中的 SQL Server 自動備份（Resource Manager）](virtual-machines-windows-sql-automated-backup.md)。 |
| **SQL Server 自動修補** |設定維護期間 (在此期間會進行 VM 的重要 Windows 更新)，以避免在工作負載尖峰時段進行更新。 如需詳細資訊，請參閱[Azure 虛擬機器中的 SQL Server 的自動修補（Resource Manager）](virtual-machines-windows-sql-automated-patching.md)。 |
| **Azure Key Vault 整合** |讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。 如需詳細資訊，請參閱[在 Azure 虛擬機器上設定 SQL Server 的 Azure Key Vault 整合（Resource Manager）](virtual-machines-windows-ps-sql-keyvault.md)。 |

安裝並執行 SQL Server Iaas 代理程式擴充功能之後，它會讓系統管理功能可供使用：

* 在 Azure 入口網站中虛擬機器的 [SQL Server] 面板上，以及 Azure Marketplace 上 SQL Server 影像的 [Azure PowerShell]。
* 透過 Azure PowerShell 來手動安裝延伸模組。 

## <a name="prerequisites"></a>必要條件
以下是在您的 VM 上使用 SQL Server IaaS 代理程式擴充功能的需求：

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

* [下載及設定最新的 Azure PowerShell 命令](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>安裝
當您向[SQL VM 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊您的 SQL Server VM 時，會安裝 SQL Server IaaS 延伸模組。 如有需要，您可以使用下列 PowerShell 命令，手動安裝 SQL Server IaaS 代理程式： 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> 安裝延伸模組會重新開機 SQL Server 服務。 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>在具有單一名為 SQL Server 實例的 VM 上安裝
SQL Server IaaS 擴充功能會在 SQL Server 上使用名為的實例，如果預設實例已卸載，且已重新安裝 IaaS 延伸模組。

若要使用 SQL Server 的已命名實例，請遵循下列步驟：
   1. 從 Azure Marketplace 部署 SQL Server VM。 
   1. 從[Azure 入口網站](https://portal.azure.com)卸載 IaaS 延伸模組。
   1. 完全卸載 SQL Server VM 內的 SQL Server。
   1. 使用 SQL Server VM 中的已命名實例來安裝 SQL Server。 
   1. 從 Azure 入口網站安裝 IaaS 延伸模組。  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>取得 SQL Server IaaS 延伸模組的狀態
其中一項驗證已安裝擴充功能的方法，是在 Azure 入口網站中檢視代理程式狀態。 選取 [虛擬機器] 視窗中的 [**所有設定**]，然後選取 [**擴充**功能]。 您應該會看到列出 **SqlIaasExtension** 擴充功能。

![Azure 入口網站中 SQL Server IaaS 代理程式延伸模組的狀態](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

您也可以使用**set-azvmsqlserverextension** Azure PowerShell Cmdlet：

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

先前的命令會確認代理程式已安裝，並提供一般狀態資訊。 您可以使用下列命令來取得有關自動備份和修補的特定狀態資訊：

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>移除
在 Azure 入口網站中，您可以在虛擬機器內容的 [**擴充**功能] 視窗中選取省略號來卸載擴充功能。 然後選取 [刪除]。

![卸載 Azure 入口網站中的 SQL Server IaaS 代理程式擴充功能](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

您也可以使用**Set-azvmsqlserverextension** PowerShell Cmdlet：

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>後續步驟
開始使用此延伸模組支援的其中一項服務。 如需詳細資訊，請參閱本文的[支援的服務](#supported-services)一節中所參考的文章。

如需在 Azure 虛擬機器上執行 SQL Server 的詳細資訊，請參閱[什麼是 azure 虛擬機器上的 SQL Server？](virtual-machines-windows-sql-server-iaas-overview.md)。
