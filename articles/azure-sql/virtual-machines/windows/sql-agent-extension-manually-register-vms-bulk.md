---
title: 使用 SQL IaaS 代理程式擴充功能在 Azure 中註冊多個 SQL Vm
description: 使用 SQL IaaS 代理程式擴充功能大量註冊 SQL Server Vm，以改善管理能力。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 208df7ad53049598255ce358f2db128ba84fea9a
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557552"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>使用 SQL IaaS 代理程式擴充功能在 Azure 中註冊多個 SQL Vm
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何使用 Azure PowerShell Cmdlet，透過 [SQL IaaS 代理程式擴充](sql-server-iaas-agent-extension-automate-management.md) 功能，在 Azure 中 () vm 大量註冊 SQL Server 虛擬機器 `Register-SqlVMs` 。 


本文將指導您以手動方式大量註冊 SQL Server Vm。 或者，您也可以手動註冊 [所有 SQL Server vm](sql-agent-extension-automatic-registration-all-vms.md) 或 [個別 SQL Server vm](sql-agent-extension-manually-register-single-vm.md)。 

## <a name="overview"></a>概觀

`Register-SqlVMs` Cmdlet 可用在指定的訂用帳戶、資源群組或特定虛擬機器清單中註冊所有虛擬機器。 此 Cmdlet 會在 [lightweight_ 管理模式下](sql-server-iaas-agent-extension-automate-management.md#management-modes)註冊虛擬機器，然後產生 [報告和記錄](#output-description)檔。 

註冊程式不會有任何風險，也不會有停機時間，也不會重新開機 SQL Server 服務或虛擬機器。 

## <a name="prerequisites"></a>先決條件

若要向擴充功能註冊 SQL Server VM，您需要下列各項： 

- 已 [向 **microsoft.sqlvirtualmachine** 提供者註冊](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-rp)並包含未註冊 SQL Server 虛擬機器的 [Azure 訂](https://azure.microsoft.com/free/)用帳戶。 
- 用來註冊虛擬機器的用戶端認證存在於下列任何 Azure 角色中： **虛擬機器參與者** 、 **參與者** 或 **擁有** 者。 
- Az PowerShell 的最新版本 [ (5.0 最小) ](/powershell/azure/new-azureps-module-az)。 


## <a name="get-started"></a>開始使用

在繼續操作前，您必須先建立指令碼的本機複本、將其匯入為 PowerShell 模組，然後連線至 Azure。 

### <a name="create-the-script"></a>建立指令碼

若要建立指令碼，請複製本文結尾的[完整指令碼](#full-script)，並在本機將它儲存為 `RegisterSqlVMs.psm1`。 

### <a name="import-the-script"></a>匯入指令碼

建立指令碼之後，您可以將其匯入為 PowerShell 終端機中的模組。 

開啟系統管理 PowerShell 終端機，並瀏覽至您儲存 `RegisterSqlVMs.psm1` 檔案的位置。 然後，執行下列 PowerShell Cmdlet，將指令碼匯入為模組： 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>連線到 Azure

使用以下 PowerShell Cmdlet 來連線至 Azure：

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-a-list-of-subscriptions"></a>訂用帳戶清單中的所有 Vm 

使用下列 Cmdlet，在訂用帳戶清單中註冊所有 SQL Server 的虛擬機器：

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

範例輸出︰ 

```
Number of subscriptions registration failed for 
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

## <a name="all-vms-in-a-single-subscription"></a>單一訂用帳戶中的所有 Vm

使用下列 Cmdlet，在單一訂用帳戶中註冊所有 SQL Server 的虛擬機器： 

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

## <a name="all-vms-in-multiple-resource-groups"></a>多個資源群組中的所有 Vm

使用下列 Cmdlet，在單一訂用帳戶內的多個資源群組中註冊所有 SQL Server 的虛擬機器：

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

## <a name="all-vms-in-a-resource-group"></a>資源群組中的所有 Vm

使用下列 Cmdlet，在單一資源群組中註冊所有 SQL Server 的虛擬機器： 

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

## <a name="specific-vms-in-a-single-resource-group"></a>單一資源群組中的特定 Vm

使用下列 Cmdlet，在單一資源群組中註冊特定 SQL Server 的虛擬機器：

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

## <a name="a-specific-vm"></a>特定 VM

使用下列 Cmdlet 註冊特定 SQL Server 的虛擬機器： 

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


## <a name="output-description"></a>輸出描述

每次使用 `Register-SqlVMs` Cmdlet 時，都會產生報表和記錄檔。 

### <a name="report"></a>Report

報表會產生名為 `RegisterSqlVMScriptReport<Timestamp>.txt` 的 `.txt` 檔案，其中時間戳記是 Cmdlet 啟動的時間。 此報告列出下列詳細資料：

| **輸出值** | **說明** |
| :--------------  | :-------------- | 
| 因為您沒有存取權限或認證不正確的訂用帳戶註冊失敗數 | 這會針對所提供驗證發生問題的訂用帳戶提供訂用帳戶數目和清單。 您可以藉由搜尋訂用帳戶識別碼，在記錄檔中找到詳細的錯誤。 | 
| 因為未向資源提供者註冊，所以無法嘗試的訂用帳戶數目 | 此區段包含尚未註冊到 SQL IaaS 代理程式擴充功能的訂用帳戶計數和清單。 |
| 已找到 VM 總數 | 在傳遞至 Cmdlet 的參數範圍內找到的虛擬機器計數。 | 
| 已註冊 VM 數 | 因為已向擴充功能註冊的虛擬機器計數，所以已略過這些虛擬機器。 |
| 成功註冊的 VM 數 | 在執行 Cmdlet 後已成功註冊的虛擬機器計數。 以 `SubscriptionID, Resource Group, Virtual Machine` 格式列出已註冊的虛擬機器。 | 
| 因發生錯誤而無法註冊的 VM 數 | 因某個錯誤而無法註冊的虛擬機器計數。 錯誤的詳細資料可在記錄檔中找到。 | 
| 因為 VM 或 VM 上的 gust 代理程式未執行而略過的 VM 數 | 無法在虛擬機器上註冊為虛擬機器或客體代理程式的虛擬機器計數和清單未執行。 當虛擬機器或客體代理程式啟動之後，就可以重試這些操作。 在記錄檔中可找到詳細資料。 |
| 因未在 Windows 上執行 SQL Server 而略過的 VM 數 | 因為未執行 SQL Server 或不是 Windows 虛擬機器而略過的虛擬機器計數。 虛擬機器會以 `SubscriptionID, Resource Group, Virtual Machine` 格式列出。 | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

錯誤會記錄在名為 `VMsNotRegisteredDueToError<Timestamp>.log` 的記錄檔中，其中時間戳記是指令碼啟動的時間。 如果錯誤位於訂用帳戶層級，則記錄檔會包含以逗號分隔的訂用帳戶識別碼和錯誤訊息。 如果此錯誤與虛擬機器註冊有關，記錄檔會包含訂用帳戶識別碼、資源群組名稱、虛擬機器名稱、錯誤碼和訊息 (以逗號分隔)。 

## <a name="remarks"></a>備註

當您使用提供的腳本向擴充功能註冊 SQL Server Vm 時，請考慮下列事項：

- 使用延伸模組註冊需要在 SQL Server VM 上執行的來賓代理程式。 Windows Server 2008 映像沒有客體代理程式，因此這些虛擬機器將會失敗，而必須使用 [NoAgent 管理模式](sql-server-iaas-agent-extension-automate-management.md#management-modes)手動註冊。
- 有內建的重試邏輯可克服透明錯誤。 如果虛擬機器已成功註冊，則是快速操作。 不過，如果註冊失敗，則會重試每部虛擬機器。  因此，您應該允許大量時間來完成註冊流程，不過實際時間需求取決於錯誤的類型和數量。 

## <a name="full-script"></a>完整指令碼

如需 GitHub 上的完整腳本，請參閱 [使用 Az PowerShell 大量註冊 SQL Server vm](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)。 

複製完整指令碼，並將其儲存為 `RegisterSqLVMs.psm1`。

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定價指引](pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](../../database/doc-changes-updates-release-notes.md)
