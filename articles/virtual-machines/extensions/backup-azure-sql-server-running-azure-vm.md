---
title: Azure 備份，用於在 Azure VM 中運行的 SQL 伺服器
description: 在本文中，瞭解如何在 Azure 虛擬機器中運行的 SQL Server 中註冊 Azure 備份。
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247379"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure 備份，用於在 Azure VM 中運行的 SQL 伺服器

Azure 備份和其他產品都支援備份工作負荷，如在 Azure VM 中運行的 SQL Server。 由於 SQL 應用程式在 Azure VM 中運行，因此備份服務需要許可權才能訪問該應用程式並獲取必要的詳細資訊。
為此，Azure 備份在使用者觸發的註冊過程中在運行 SQL Server 的 VM 上安裝**Azure BackupWindows 工作負荷**擴展。

## <a name="prerequisites"></a>Prerequisites

有關支援的方案清單，請參閱 Azure 備份支援[的支援性矩陣](../../backup/sql-support-matrix.md#scenario-support)。

## <a name="network-connectivity"></a>網路連線

Azure 備份支援 NSG 標記、部署代理伺服器或列出的 IP 範圍;有關每種方法的詳細資訊，請參閱[本文](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)。

## <a name="extension-schema"></a>擴充功能結構描述

擴展架構和屬性值是服務傳遞給 CRP API 的配置值（運行時設置）。 這些配置值在註冊和升級期間使用。 **Azure 備份視窗工作負載**擴展也使用此架構。 架構已預設;因此，該架構已預設為可以在物件Str欄位中添加新參數

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

以下 JSON 顯示了工作負載備份擴展的架構。  

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

名稱 | 值/示例 | 資料類型
 --- | --- | ---
地區設定 | zh-tw  |  字串
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | 字串
objectStr <br/> （公共設置）  | "eyJjb250YLuZXJQcm9wZXJ0aWVzijp7IkNvnnNnHaW5lcklEIiMzVjQyTitogRjNy00ZGE5LWI4NTMtmjjjjdzlM2ZkwiSWRNZ210Q 29udGFpbmVySWQiOjM0NTY3ODg5LCJSZSZXJjZUlkiMDU5NWIwogEtYzI4zi00ZmFlLWE5ODTokwowMYMGJNjvhwiU3Vic2nyaXB0aw9uswQioijk NGezOTliny1iyjyaltQ2MWMtODdyS1jNTM5ODI3ZtzNTQiLCjvbmlxdVDb250YWluZXJOYW1lIjoiODM4MDZjODNTNTTq4OS0nmNhLWEyZtctNWMnznznhyj g3OTcyIn0sinNYW1wTGlzdCI6W3su2VydmljZU5hbWUiOjUsilnlnlzpy2VTdGFtcffbCI6Imh0dHA6XC9cL015V0xGYWTdmMuY29tin1dfq==" | 字串
commandStartTimeUTCTicks | "636967192566036845"  | 字串
vmType  | "微軟.計算/虛擬機器"  | 字串
objectStr <br/> （受保護設置） | "eyJjb250YLuZXJQcm9wZXJ0aWVzijp7IkNvnnNnHaW5lcklEIiMzVjQyTitogRjNy00ZGE5LWI4NTMtmjjjjdzlM2ZkwiSWRNZ210Q 29udGFpbmVySWQiOjM0NTY3ODg5LCJSZSZXJjZUlkiMDU5NWIwogEtYzI4zi00ZmFlLWE5ODTokwowMYMGJNjvhwiU3Vic2nyaXB0aw9uswQioijk NGezOTliny1iyjyaltQ2MWMtODdyS1jNTM5ODI3ZtzNTQiLCjvbmlxdVDb250YWluZXJOYW1lIjoiODM4MDZjODNTNTTq4OS0nmNhLWEyZtctNWMnznznhyj g3OTcyIn0sinNYW1wTGlzdCI6W3su2VydmljZU5hbWUiOjUsilnlnlzpy2VTdGFtcffbCI6Imh0dHA6XC9cL015V0xGYWTdmMuY29tin1dfq==" | 字串
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | 字串
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | 字串

## <a name="template-deployment"></a>範本部署

我們建議將 AzureBackupWindows 工作負荷擴展添加到虛擬機器，是在虛擬機器上啟用 SQL Server 備份。 這可以通過專為在 SQL Server VM 上自動備份而設計的[資源管理器範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup)來實現。

## <a name="powershell-deployment"></a>PowerShell 部署

您需要使用恢復服務保存庫"註冊"包含 SQL 應用程式的 Azure VM。 在註冊期間，Azure 備份Windows工作負載擴展將安裝在 VM 上。 使用 [註冊-Az 恢復服務備份容器PS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) Cmdlet 註冊 VM。

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

該命令將返回此資源的**備份容器**，並將**註冊**狀態。

## <a name="next-steps"></a>後續步驟

- [瞭解有關](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot)Azure SQL Server VM 備份故障排除指南的資訊
- 有關備份在 Azure 虛擬機器 （VM） 上運行並使用 Azure 備份服務運行的 SQL Server 資料庫的[常見問題](https://docs.microsoft.com/azure/backup/faq-backup-sql-server)。
