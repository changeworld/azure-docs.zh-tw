---
title: 在 Azure 中使用 SQL VM 資源提供者大量註冊 SQL 虛擬機器 |Microsoft Docs
description: 使用 SQL VM 資源提供者大量註冊 SQL Server Vm，以改善管理能力。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "75353877"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>在 Azure 中使用 SQL VM 資源提供者大量註冊 SQL 虛擬機器

本文說明如何使用`Register-SqlVMs` PowerShell Cmdlet，在 Azure 中透過 SQL VM 資源提供者大量註冊您的 SQL Server 虛擬機器（VM）。

`Register-SqlVMs` Cmdlet 可以用來在指定的訂用帳戶、資源群組或特定虛擬機器清單中註冊所有虛擬機器。 此 Cmdlet 會在_輕量_管理模式中註冊虛擬機器，然後產生[報表和記錄](#output-description)檔。 

註冊程式不會產生任何風險、沒有停機時間，也不會重新開機 SQL Server 或虛擬機器。 

如需資源提供者的詳細資訊，請參閱[SQL VM 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)。 

## <a name="prerequisites"></a>Prerequisites

若要向資源提供者註冊您的 SQL Server VM，您需要下列各項： 

- 已[向資源提供者註冊](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp)並包含未註冊 SQL Server 虛擬機器的[Azure 訂](https://azure.microsoft.com/free/)用帳戶。 
- 用來註冊虛擬機器的用戶端認證存在於下列任何 RBAC 角色中： [**虛擬機器參與者**]、[**參與者**] 或 [**擁有**者]。 
- 最新版本的[Az PowerShell](/powershell/azure/new-azureps-module-az)。 
- 最新版本的[Az. microsoft.sqlvirtualmachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0)。

## <a name="getting-started"></a>開始使用

在繼續之前，您必須先建立腳本的本機複本、將它匯入為 PowerShell 模組，然後連線至 Azure。 

### <a name="create-script"></a>建立腳本

若要建立腳本，請複製本文結尾的[完整腳本](#full-script)，並將其儲存在本機做`RegisterSqlVMs.psm1`為。 

### <a name="import-script"></a>匯入腳本

建立腳本之後，您可以將它匯入為 Powershell 終端機中的模組。 

開啟系統管理 PowerShell 終端機，並流覽至您儲存`RegisterSqlVMs.psm1`盤案的位置。 然後，執行下列 PowerShell Cmdlet，將腳本匯入為模組： 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>連線到 Azure

使用下列 PowerShell Cmdlet 連接到 Azure：

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>訂用帳戶清單中的所有 Vm 

使用下列 Cmdlet，在訂用帳戶清單中註冊所有 SQL Server 的虛擬機器：

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

## <a name="specific-vms-in-single-resource-group"></a>單一資源群組中的特定 Vm

使用下列 Cmdlet，在單一資源群組中註冊特定 SQL Server 虛擬機器：

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

使用下列 Cmdlet 來註冊特定的 SQL Server 虛擬機器： 

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

每次使用`Register-SqlVMs` Cmdlet 時，都會產生報表和記錄檔。 

### <a name="report"></a>Report

報表會產生`.txt`為名`RegisterSqlVMScriptReport<Timestamp>.txt`為的檔案，其中時間戳記是 Cmdlet 啟動的時間。 此報表會列出下列詳細資料：

| **輸出值** | **說明** |
| :--------------  | :-------------- | 
| 因為您沒有存取權或認證不正確，所以的訂閱註冊失敗次數 | 這會提供具有所提供驗證問題的訂閱數目和清單。 您可以藉由搜尋訂用帳戶識別碼，在記錄檔中找到詳細的錯誤。 | 
| 因為未向 RP 註冊，所以無法嘗試的訂閱數目 | 此區段包含尚未向 SQL VM 資源提供者註冊的訂用帳戶計數和清單。 |
| 找到的 Vm 總數 | 在傳遞至 Cmdlet 的參數範圍中找到的虛擬機器計數。 | 
| Vm 已註冊 | 已向資源提供者註冊時略過的虛擬機器計數。 |
| 已成功註冊的 Vm 數目 | 執行 Cmdlet 之後，已成功註冊的虛擬機器計數。 以格式`SubscriptionID, Resource Group, Virtual Machine`列出已註冊的虛擬機器。 | 
| 因發生錯誤而無法註冊的 Vm 數目 | 因某些錯誤而無法註冊的虛擬機器計數。 錯誤的詳細資料可在記錄檔中找到。 | 
| 因為 vm 或 VM 上的 gust 代理程式未執行而略過的 vm 數目 | 無法在虛擬機器上註冊為虛擬機器或來賓代理程式的虛擬機器計數和清單未執行。 當虛擬機器或來賓代理程式啟動之後，就可以重試這些工作。 詳細資料可以在記錄檔中找到。 |
| 因未在 Windows 上執行 SQL Server 而略過的 Vm 數目 | 因為未執行 SQL Server 或不是 Windows 虛擬機器而略過的虛擬機器計數。 虛擬機器會以格式`SubscriptionID, Resource Group, Virtual Machine`列出。 | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

錯誤會記錄在名為`VMsNotRegisteredDueToError<Timestamp>.log`的記錄檔中，其中 timestamp 是腳本開始的時間。 如果錯誤是在訂用帳戶層級，則記錄檔會包含以逗號分隔的 SubscriptionID 和錯誤訊息。 如果此錯誤與虛擬機器註冊有關，記錄檔會包含訂用帳戶識別碼、資源組名、虛擬機器名稱、錯誤碼和訊息（以逗號分隔）。 

## <a name="remarks"></a>備註

當您使用提供的腳本向資源提供者註冊 SQL Server Vm 時，請考慮下列事項：

- 向資源提供者註冊需要在 SQL Server VM 上執行來賓代理程式。 Windows Server 2008 映射沒有來賓代理程式，因此這些虛擬機器將會失敗，而且必須使用[NoAgent 管理模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)手動註冊。
- 有內建的重試邏輯可克服透明錯誤。 如果虛擬機器已成功註冊，則它是快速的操作。 不過，如果註冊失敗，則會重試每部虛擬機器。  因此，您應該允許很長的時間完成註冊程式，不過實際時間需求取決於錯誤的類型和數目。 

## <a name="full-script"></a>完整指令碼

如需 GitHub 上的完整腳本，請參閱[使用 Az PowerShell 大量註冊 SQL vm](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)。 

複製完整的腳本，並將它`RegisterSqLVMs.psm1`儲存為。

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 總覽](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
