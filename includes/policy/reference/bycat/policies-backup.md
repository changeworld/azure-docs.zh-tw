---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9e6d9cee5cd9e302f30681772bf1e06243efd72a
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047134"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |藉由啟用 Azure 備份，確保您的 Azure 虛擬機器受到保護。 Azure 備份是適用於 Azure 安全且符合成本效益的資料保護解決方案。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[使用預設原則，將具有指定標籤的 VM 上的備份設定為新的復原服務保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83644c87-93dd-49fe-bf9f-6aff8fd0834e) |在與虛擬機器相同的位置和資源群組中部署復原服務保存庫，以強制執行所有虛擬機器的備份。 當組織中的不同應用程式小組配置了不同的資源群組，且需要管理其本身的備份和還原時，這樣做會很有用。 您可以選擇性地納入包含指定標籤的虛擬機器，以控制指派的範圍。 請參閱 [https://aka.ms/AzureVMAppCentricBackupIncludeTag](https://aka.ms/AzureVMAppCentricBackupIncludeTag) |deployIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineApplicationCentricBackup_Backup_Deploy_WithTag.json) |
|[將具有指定標籤的 VM 上的備份設定為相同位置中的現有復原服務保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F345fa903-145c-4fe1-8bcd-93ec2adccde8) |將虛擬機器備份至與虛擬機器相同的位置和訂用帳戶中的現有中央復原服務保存庫，以強制執行所有虛擬機器的備份。 當您的組織以中央小組管理訂用帳戶中所有資源的備份時，這樣做會很有用。 您可以選擇性地納入包含指定標籤的虛擬機器，以控制指派的範圍。 請參閱 [https://aka.ms/AzureVMCentralBackupIncludeTag](https://aka.ms/AzureVMCentralBackupIncludeTag) |deployIfNotExists、auditIfNotExists、disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineWithTag_Backup_Deploy.json) |
|[使用預設原則，將不具指定標籤的 VM 上的備份設定為新的復原服務保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98d0b9f8-fd90-49c9-88e2-d3baf3b0dd86) |在與虛擬機器相同的位置和資源群組中部署復原服務保存庫，以強制執行所有虛擬機器的備份。 當組織中的不同應用程式小組配置了不同的資源群組，且需要管理其本身的備份和還原時，這樣做會很有用。 您可以選擇性地排除包含指定標籤的虛擬機器，以控制指派的範圍。 請參閱 [https://aka.ms/AzureVMAppCentricBackupExcludeTag](https://aka.ms/AzureVMAppCentricBackupExcludeTag) |deployIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineApplicationCentricBackup_Backup_Deploy_WithOutTag.json) |
|[將不具指定標籤的 VM 上的備份設定為相同位置中的現有復原服務保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |將虛擬機器備份至與虛擬機器相同的位置和訂用帳戶中的現有中央復原服務保存庫，以強制執行所有虛擬機器的備份。 當您的組織以中央小組管理訂用帳戶中所有資源的備份時，這樣做會很有用。 您可以選擇性地排除包含指定標籤的虛擬機器，以控制指派的範圍。 請參閱 [https://aka.ms/AzureVMCentralBackupExcludeTag](https://aka.ms/AzureVMCentralBackupExcludeTag) |deployIfNotExists、auditIfNotExists、disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[為復原服務保存庫將診斷設定部署到 Log Analytics 工作區，以納入資源專屬類別。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |為復原服務保存庫將診斷設定部署到 Log Analytics 工作區，以串流至資源專屬類別。 如果沒有啟用任何資源專屬類別，則會建立新的診斷設定。 |deployIfNotExists |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
