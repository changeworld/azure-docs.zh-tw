---
title: 在 Azure VM 中執行 SQL Server 的 Azure 備份
description: 在本文中，您將瞭解如何在 Azure 虛擬機器中執行 SQL Server 註冊 Azure 備份。
author: dcurwin
manager: carmonm
ms.service: virtual-machines
ms.subservice: extensions
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 66675f77d480ce8d9f21e5ffb507c475337c9dab
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490725"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>在 Azure VM 中執行 SQL Server 的 Azure 備份

在其他供應專案之間 Azure 備份，可支援備份工作負載，例如在 Azure Vm 中執行的 SQL Server。 由於 SQL 應用程式是在 Azure VM 中執行，因此備份服務需要存取應用程式的許可權，並提取必要的詳細資料。
若要這樣做，Azure 備份在使用者所觸發的註冊程式期間，于執行 SQL Server 的 VM 上安裝 **AzureBackupWindowsWorkload** 擴充功能。

## <a name="prerequisites"></a>必要條件

如需支援的案例清單，請參閱 Azure 備份所支援的支援 [矩陣](../../backup/sql-support-matrix.md#scenario-support) 。

## <a name="network-connectivity"></a>網路連線

Azure 備份支援 NSG 標記、部署 proxy 伺服器或列出的 IP 範圍;如需每個方法的詳細資訊，請參閱這 [篇文章](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity)。

## <a name="extension-schema"></a>擴充功能結構描述

擴充功能架構和屬性值是 (runtime 設定的設定值) 該服務會將該服務傳遞給 CRP API。 這些設定值會在註冊和升級期間使用。 **AzureBackupWindowsWorkload** 延伸模組也會使用此架構。 架構已預先設定;可以在 objectStr 欄位中加入新的參數

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

下列 JSON 顯示 WorkloadBackup 延伸模組的架構。  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>屬性值

名稱 | 值/範例 | 資料類型
 --- | --- | ---
locale | zh-tw  |  字串
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | 字串
objectStr <br/>  (>publicsettings)   | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | 字串
commandStartTimeUTCTicks | "636967192566036845"  | 字串
>-vmtype  | "microsoft. compute/virtualmachines"  | 字串
objectStr <br/>  (protectedSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | 字串
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | 字串
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | 字串

## <a name="template-deployment"></a>範本部署

建議您在虛擬機器上啟用 SQL Server 備份，以將 AzureBackupWindowsWorkload 延伸模組新增至虛擬機器。 這可以透過專為在 SQL Server VM 上自動化備份而設計的 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) 來達成。

## <a name="powershell-deployment"></a>PowerShell 部署

您必須使用復原服務保存庫來「註冊」包含 SQL 應用程式的 Azure VM。 在註冊期間，AzureBackupWindowsWorkload 擴充功能會安裝在 VM 上。 使用 [AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer?view=azps-1.5.0) 指令 Cmdlet 來註冊 VM。

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

此命令會傳回此資源的 **備份容器** ，且狀態將會 **註冊**。

## <a name="next-steps"></a>後續步驟

- [深入瞭解](../../backup/backup-sql-server-azure-troubleshoot.md) AZURE SQL Server VM 備份疑難排解指導方針
- 有關備份在 Azure 虛擬機器上執行的 SQL Server 資料庫 (Vm) 以及使用 Azure 備份服務[的相關常見問題](../../backup/faq-backup-sql-server.md)。
