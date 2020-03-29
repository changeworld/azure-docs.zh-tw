---
title: 批量註冊 Azure 中的 SQL 虛擬機器與 SQL VM 資來源提供者 |微軟文檔
description: 批量註冊 SQL Server VM VM VM 提供程式以提高可管理性。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353877"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>批量註冊 Azure 中的 SQL 虛擬機器與 SQL VM 資來源提供者

本文介紹如何使用`Register-SqlVMs`PowerShell Cmdlet 向 SQL VM 資來源提供者批量註冊 Azure 中的 SQL Server 虛擬機器 （VM）。

`Register-SqlVMs` Cmdlet 可用於註冊給定訂閱、資源組或特定虛擬機器清單中的所有虛擬機器。 Cmdlet 將在_羽量級_管理模式下註冊虛擬機器，然後生成[報表和日誌檔](#output-description)。 

註冊過程沒有風險，沒有停機時間，也不會重新開機 SQL Server 或虛擬機器。 

有關資來源提供者的詳細資訊，請參閱 SQL [VM 資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)。 

## <a name="prerequisites"></a>Prerequisites

要向資來源提供者註冊 SQL Server VM，您需要以下操作： 

- 已[註冊到資來源提供者並](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp)包含未註冊的 SQL Server 虛擬機器的[Azure 訂閱](https://azure.microsoft.com/free/)。 
- 用於註冊虛擬機器的用戶端憑據存在於以下任何 RBAC 角色中：**虛擬機器參與者**、**參與者**或**擁有者**。 
- 最新版本的Az [PowerShell](/powershell/azure/new-azureps-module-az). 
- 最新版本的[Az.Sql虛擬電腦](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0)。

## <a name="getting-started"></a>開始使用

在繼續操作之前，必須首先創建腳本的本機複本，將其導入為 PowerShell 模組，然後連接到 Azure。 

### <a name="create-script"></a>創建腳本

要創建腳本，請從本文末尾複製[完整腳本](#full-script)，並將其保存在本地為`RegisterSqlVMs.psm1`。 

### <a name="import-script"></a>導入腳本

創建腳本後，可以在 Powershell 終端中將其導入為模組。 

打開管理 PowerShell 終端並導航到保存`RegisterSqlVMs.psm1`檔的位置。 然後，運行以下 PowerShell Cmdlet 以將腳本導入為模組： 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>連線到 Azure

使用以下 PowerShell Cmdlet 連接到 Azure：

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>訂閱清單中的所有 VM 

使用以下 Cmdlet 在訂閱清單中註冊所有 SQL Server 虛擬機器：

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

範例輸出︰ 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>單個訂閱中的所有 VM

使用以下 Cmdlet 在單個訂閱中註冊所有 SQL Server 虛擬機器： 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

範例輸出︰

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>多個資源組中的所有 VM

使用以下 Cmdlet 在單個訂閱中註冊多個資源組中的所有 SQL Server 虛擬機器：

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

範例輸出︰

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>資源組中的所有 VM

使用以下 Cmdlet 將所有 SQL Server 虛擬機器註冊到單個資源組中： 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

範例輸出︰

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>單個資源組中的特定 VM

使用以下 Cmdlet 在單個資源組中註冊特定的 SQL Server 虛擬機器：

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

範例輸出︰

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>特定 VM

使用以下 Cmdlet 註冊特定的 SQL Server 虛擬機器： 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

範例輸出︰

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>輸出說明

每次使用`Register-SqlVMs`Cmdlet 時都會生成報表和日誌檔。 

### <a name="report"></a>Report

報表生成為名為`.txt`檔`RegisterSqlVMScriptReport<Timestamp>.txt`的檔，其中時間戳記是啟動 Cmdlet 的時間。 報告列出了以下詳細資訊：

| **輸出值** | **描述** |
| :--------------  | :-------------- | 
| 由於您沒有存取權限或憑據不正確，訂閱註冊失敗數 | 這提供了與提供的身份驗證有問題的訂閱的數量和清單。 通過搜索訂閱 ID，可以在日誌中找到詳細的錯誤。 | 
| 由於未註冊到 RP 而無法嘗試的訂閱數 | 本節包含尚未註冊到 SQL VM 資來源提供者的訂閱的計數和清單。 |
| 找到的 VM 總數 | 在傳遞給 Cmdlet 的參數範圍內找到的虛擬機器的計數。 | 
| VM 已註冊 | 已跳過的虛擬機器的計數，因為它們已註冊到資來源提供者。 |
| 成功註冊的 VM 數量 | 運行 Cmdlet 後成功註冊的虛擬機器的計數。 以 格式`SubscriptionID, Resource Group, Virtual Machine`列出已註冊的虛擬機器。 | 
| 由於錯誤而無法註冊的 VM 數量 | 由於某些錯誤而無法註冊的虛擬機器計數。 錯誤的詳細資訊可以在日誌檔中找到。 | 
| 當 VM 或 VM 上的陣風代理未運行時跳過的 VM 數 | 無法註冊為虛擬機器或虛擬機器上的來賓代理的虛擬機器的計數和清單未運行。 啟動虛擬機器或來賓代理後，可以重試這些程式。 詳細資訊可在日誌檔中找到。 |
| 由於未在 Windows 上運行 SQL 伺服器而跳過的 VM 數 | 因未運行 SQL Server 或不是 Windows 虛擬機器而跳過的虛擬機器數。 虛擬機器以格式`SubscriptionID, Resource Group, Virtual Machine`列出。 | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

錯誤記錄在名為"`VMsNotRegisteredDueToError<Timestamp>.log`時間戳記"的日誌檔中，其中時間戳記是腳本啟動的時間。 如果錯誤位於訂閱級別，日誌包含逗號分隔的訂閱 ID 和錯誤訊息。 如果錯誤與虛擬機器註冊，日誌包含訂閱 ID、資源組名稱、虛擬機器名稱、錯誤代碼和按逗號分隔的消息。 

## <a name="remarks"></a>備註

使用提供的腳本向資來源提供者註冊 SQL Server VM 時，請考慮以下事項：

- 與資來源提供者註冊需要在 SQL Server VM 上運行的來賓代理。 Windows Server 2008 映射沒有來賓代理，因此這些虛擬機器將失敗，必須使用[NoAgent 管理模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)手動註冊。
- 內置重試邏輯以克服透明錯誤。 如果虛擬機器已成功註冊，則它是一種快速操作。 但是，如果註冊失敗，則重試每個虛擬機器。  因此，您應該留出大量時間來完成註冊過程，儘管實際時間要求取決於錯誤的類型和數量。 

## <a name="full-script"></a>完整指令碼

有關 GitHub 上的完整腳本，請參閱[批量註冊具有 Az PowerShell 的 SQL VM。](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1) 

複製完整腳本並將其另存為`RegisterSqLVMs.psm1`。

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL 伺服器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 伺服器常見問題解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 伺服器定價指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 伺服器的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
